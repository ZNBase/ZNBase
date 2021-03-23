# 权限管理
## 权限管理

​		在实际的项目中，我们建议用户规划好不同数据库用户的职责，并给它赋予相应的操作权限，以保证数据库的安全。在InCloud ZNBASE中，可以使用GRANT和REVOKE命令进行权限的授予和撤销且可通过查询语句查询权限状态。为了使用的灵活性，在用户拥有权限的同时，还可以赋予用户赋权他人的能力。

### 权限的授予

​		权限的授予，用于授予用户操作数据库对象或表数据的权限。用户必须拥有目标数据库、模式、表或列的欲授予权限，且拥有为他人授权（GRANT OPTION）的属性，才能为其他用户或角色进行赋权。为用户授予权限的语法是GRANT语法。下面进行详细介绍。

##### 语法格式

GRANT语法格式如下：

![](media/924f4a6176a60e88b4ef24cef97ac543.png)

#### 参数说明

- column_name

  列名，是以逗号分隔的列名列表。在列上授予的权限，会影响用户对列的操作。拥有表上的权限，会拥有该表所有列的权限。

- table_pattern

  table的名称模式，是以逗号分隔的表名列表。可以是最简单的表名称；也可以是database_name.table_name模式。要授予所有表权限，要使用\*：ON TABLE *授予数据库中所有现有表权限，但不会影响之后创建的表；要授予某个具体数据库的所有表权限，要使用database_name.\*：ON [TABLE ] database_name.\*授予指定数据库database_name中所有现有表的权限，但是不会影响之后创建的表。

- database_name

  数据库名称，是以逗号分隔的数据库名称列表。在数据库上授予的权限，会被创建在该数据库的所有新表继承，但不会影响数据库中的现有表。

- schema_name

  模式名称，是以逗号分隔的数据库模式名称列表，在模式上授予的权限，会影响用户模式本身的操作或模式下对象的操作。

- proc_name

  存储过程名称。

- func_args_with_defaults

  存储过程的参数，存储过程的名称与参数共同构成了赋权时存储过程对象，如果参数为空，则以proc_name
  +“（）”的形式，如果不为空，则以proc_name+“（type1,type2,…）”的形式，其中type为参数的类型。

- user_name

  用户或角色名称，是以逗号分隔的用户/角色列表。

对用户的授权，可以对权限类型和权限适用对象进行控制。

#### **权限类型**

一次可以给用户授予多个权限，多个权限之间使用逗号“,”分隔。下面对不同的权限进行介绍。

| 权限       | 适用级别                                    | 描述                   |
|------------|---------------------------------------------|------------------------|
| ALL        | DATABASE，TABLE， SCHEMA，COLUMN，PROCEDURE | 该对象可获得的所有权限 |
| CREATE     | DATABASE，TABLE， SCHEMA                    | 创建权限               |
| DROP       | DATABASE，TABLE， SCHEMA，PROCEDURE         | 删除对象权限           |
| USAGE      | DATABASE，SCHEMA                            | 使用权限               |
| SELECT     | TABLE，COLUMN                               | 查询数据权限           |
| INSERT     | TABLE，COLUMN                               | 新增数据权限           |
| DELETE     | TABLE                                       | 删除数据权限           |
| UPDATE     | TABLE，COLUMN                               | 更新数据权限           |
| TRIGGER    | TABLE                                       | 触发器权限             |
| REFERENCES | TABLE                                       | 更改关系属性权限       |
| EXECUTE    | PROCEDURE                                   | 执行权限               |

#### **授权适用对象**

为用户授权的对象可以是数据库，也可以是表。可以为用户授予多个对象的多个权限，多个对象之间使用逗号“,”分隔。

##### 示例

示例1：授予用户user1在数据库db上创建模式的权限，并且此用户可以将此权限继续授予给其他用户。

> GRANT CREATE ON DATABASE db1 TO user1 WITH GRANT OPTION;


查看授权情况：

> SHOW GRANTS ON DATABASE db1 FOR user1;
>
> database_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+---------+---------+----------------+-----------+ 
>
> db1 \| root \| public \| USAGE \| NO db1 \| root \| user1 \| CREATE \| YES (2 rows) |
> 


用户user1已经拥有了数据库db1的CREATE权限。

示例2：授权用户user1删除模式sc的权限。

> GRANT DROP ON SCHEMA db1.sc TO user1; 


查看授权情况：

> SHOW GRANTS ON SCHEMA db1.sc FOR user1; 
>
> database_name \| schema_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+-------------+---------+---------+----------------+-----------+ 
>
> db1 \| sc \| root \| user1 \| DROP \| NO 
>
> 用户user1已经拥有了数据库db1模式sc的DROP权限。

示例3：为用户useer1授权删除表的权限。

> GRANT DELETE ON TABLE db1.sc.t1, db1.sc.t2 TO user1; 


查看授权情况：

> SHOW GRANTS ON TABLE db1.sc.t1, db1.sc.t2 FOR user1; 
>
> database_name \| schema_name \| table_name \| column_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+-------------+------------+-------------+---------+---------+----------------+-----------+ 
>
> db1 \| sc \| t1 \| \| root \| user1 \| DELETE \| NO db1 \| sc \| t2 \| \| root \| user1 \| DELETE \| NO 


用户user1已经拥有了db1.sc.t1和db1.sc.t2的DELETE权限。

示例4：授予用户表t1列上的权限。

> GRANT SELECT (id, name) ON TABLE db1.sc.t1 TO user1; 


查看权限情况：

> SHOW GRANTS ON TABLE db1.sc.t1 FOR user1; 
>
> database_name \| schema_name \| table_name \| column_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+-------------+------------+-------------+---------+---------+----------------+-----------+ 
>
> db1 \| sc \| t1 \| \| root \| user1 \| DELETE \| NO 
>
> db1 \| sc \| t1 \| id \| root \| user1 \| SELECT \| NO 
>
> db1 \| sc \| t1 \| name \| root \| user1 \| SELECT \| 
>
> 用户user1拥有了db1.sc.t1上id，name列的SELECT权限。

### 权限的撤销

​		权限的撤销，用于撤销用户或角色操作数据库对象或表数据的权限。用户必须拥有目标数据库、模式、表或列的欲撤回权限，且拥有为他人撤权（GRANT OPTION）的属性，才能为其他用户或角色进行撤权。撤销用户权限的语法是REVOKE语法。下面进行详细介绍。

##### 语法格式

REVOKE语法格式如下：

![](media/3291d37d74975a0af32a1deb7d9fe0d6.png)

#### 参数说明

- column_name

  列名，是以逗号分隔的列名列表。在列上授予的权限，会影响用户对列的操作。撤销表上的权限，会撤销该表所有列的权限。撤销列的权限，不会对该表的权限产生影响。

- table_pattern

  table的名称模式，是以逗号分隔的表名列表。可以是最简单的表名称；也可以是database_name.table_name模式。要撤销所有表权限，要使用\*：ON TABLE  *撤销数据库中所有现有表的权限，但不会影响之后创建的表；要插销某个具体数据库的所有表权限，要使用database_name.\*：ON [TABLE ] database_name.\*撤销指定数据库database_name中所有现有表的权限，但是不会影响之后创建的表。

- database_name

  数据库名称，是以逗号分隔的数据库名称列表。

- schema_name

  模式名称，是以逗号分隔的数据库模式名称列表，在模式上授予的权限，会影响用户模式本身的操作或模式下对象的操作。

- proc_name

  存储过程名称。

- func_args_with_defaults

  存储过程的参数，存储过程的名称与参数共同构成了赋权时存储过程对象，如果参数为空，则以proc_name +“（）”的形式，如果不为空，则以proc_name+“（type1,type2,…）”的形式，其中type为参数的类型。

- user_name

  用户或角色名称，是以逗号分隔的用户/角色列表。



撤销用户权限，可以对权限类型和权限适用对象进行控制。

#### 权限类型

一次可以撤销用户多个权限，多个权限之间使用逗号“,”分隔。下面对不同的权限进行介绍。

| 权限       | 适用级别                                    | 描述                   |
|------------|---------------------------------------------|------------------------|
| ALL        | DATABASE，TABLE， SCHEMA，COLUMN，PROCEDURE | 该对象可获得的所有权限 |
| CREATE     | DATABASE，TABLE， SCHEMA                    | 创建权限               |
| DROP       | DATABASE，TABLE， SCHEMA，PROCEDURE         | 删除对象权限           |
| USAGE      | DATABASE，SCHEMA                            | 使用权限               |
| SELECT     | TABLE，COLUMN                               | 查询数据权限           |
| INSERT     | TABLE，COLUMN                               | 新增数据权限           |
| DELETE     | TABLE                                       | 删除数据权限           |
| UPDATE     | TABLE，COLUMN                               | 更新数据权限           |
| TRIGGER    | TABLE                                       | 触发器权限             |
| REFERENCES | TABLE                                       | 更改关系属性权限       |
| EXECUTE    | PROCEDURE                                   | 执行权限               |

#### 撤销权限适用对象

撤销用户权限的对象可以是数据库，也可以是表。可以撤销用户多个对象的多个权限，多个对象之间使用逗号“,”分隔。

##### 示例

示例1：撤销数据库权限

> SHOW GRANTS ON DATABASE db1 FOR user1; 
>
> database_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+---------+---------+----------------+-----------+ 
>
> db1 \| root \| public \| USAGE \| NO db1 \| root \| user1 \| CREATE \| YES 
>
> REVOKE CREATE ON DATABASE db1 FROM user1; 
>
> REVOKE 
>
> SHOW GRANTS ON DATABASE db1 FOR user1; database_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+---------+---------+----------------+-----------+ 
>
> db1 \| root \| public \| USAGE \| NO (1 row) |


撤销用户user1对数据库db1的CREATE权限。

示例2：撤销数据库中模式的权限。

> SHOW GRANTS ON SCHEMA db1.sc FOR user1; 
>
> database_name \| schema_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+-------------+---------+---------+----------------+-----------+ 
>
> db1 \| sc \| root \| user1 \| DROP \| NO 
>
> REVOKE DROP ON SCHEMA db1.sc FROM user1; 
>
> REVOKE
>
> SHOW GRANTS ON SCHEMA db1.sc FOR user1; 
>
> database_name \| schema_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+-------------+---------+---------+----------------+-----------+ 
>
> (0 rows)
> 


撤销了用户user1对数据库db1模式sc的DROP权限。

示例3：撤销用户对指定数据库中表的权限。

> SHOW GRANTS ON TABLE db1.sc.t1 FOR user1; 
>
> database_name \| schema_name \| table_name \| column_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+-------------+------------+-------------+---------+---------+----------------+-----------+ 
>
> db1 \| sc \| t1 \| \| root \| user1 \| DELETE \| NO 
>
> db1 \| sc \| t1 \| id \| root \| user1 \| SELECT \| NO 
>
> db1 \| sc \| t1 \| name \| root \| user1 \| SELECT \| NO 
>
> REVOKE DELETE ON TABLE db1.sc.t1 FROM user1; 
>
> REVOKE 
>
> SHOW GRANTS ON TABLE db2.t1; 
>
> database_name \| schema_name \| table_name \| column_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+-------------+------------+-------------+---------+---------+----------------+-----------+ 
>
> db1 \| sc \| t1 \| id \| root \| user1 \| SELECT \| NO db1 \| sc \| t1 \| name \| root \| user1 \| SELECT \| NO 


收回了用户user1对db1.sc.t1的DELETE权限。

示例4：撤销用户在列上的权限。

> SHOW GRANTS ON TABLE db1.sc.t1 FOR user1; 
>
> database_name \| schema_name \| table_name \| column_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+-------------+------------+-------------+---------+---------+----------------+-----------+ 
>
> db1 \| sc \| t1 \| id \| root \| user1 \| SELECT \| NO 
>
> db1 \| sc \| t1 \| name \| root \| user1 \| SELECT \| NO 
>
> REVOKE SELECT (id, name) ON TABLE db1.sc.t1 FROM user1; 
>
> REVOKRE 
>
> SHOW GRANTS ON TABLE db1.t1 FOR user1; 
>
> database_name \| schema_name \| table_name \| column_name \| grantor \| grantee \| privilege_type \| grantable 
>
> +---------------+-------------+------------+-------------+---------+---------+----------------+-----------+ 
>
> (0 rows) |


撤销了用户user1对db1.sc.t1上id，name列的SELECT权限。

> ROLE IF EXISTS role2;
>
