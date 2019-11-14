## mybatis(只有注解)

### 1 基本curd

#### @Select

```java
@Select("select * from user")
List<User> findAll();


@Select("select * from user  where id=#{id} ")
User findById(Integer userId);

@Select("select count(*) from user ")
int findTotalUser();
```

#### 模糊查询

```java
//@Select("select * from user where username like #{username} ")
@Select("select * from user where username like '%${value}%' ")
List<User> findUserByName(String username);

@Test
public  void testFindByName(){
    //List<User> users = userDao.findUserByName("%mybatis%");
    List<User> users = userDao.findUserByName("mybatis");
    for(User user : users){
        System.out.println(user);
    }
}
```



#### @Insert

```java
@Insert("insert into user(username,address,sex,birthday)values(#{username},#{address},#{sex},#{birthday})")
void saveUser(User user);
```

#### @Update

```java
@Update("update user set username=#{username},sex=#{sex},birthday=#{birthday},address=#{address} where id=#{id}")
void updateUser(User user);
```

#### @Delete

```java
@Delete("delete from user where id=#{id} ")
void deleteUser(Integer userId);
```



### 2 复杂关系映射

####  @Results

> **代替的是标签<resultMap>**
>
> 该注解中可以使用单个@Result 注解，也可以使用@Result 集合
> @Results（{@Result（），@Result（）}）或@Results（@Result（））

```java
@Select("select * from user")
@Results(id="userMap",value={
    @Result(id=true,column = "id",property = "userId"),
    @Result(column = "username",property = "userName"),
    @Result(column = "address",property = "userAddress"),
    @Result(column = "sex",property = "userSex"),
    @Result(column = "birthday",property = "userBirthday"),
    @Result(property = "accounts",column = "id",
            many = @Many(select = "com.itheima.dao.IAccountDao.findAccountByUid",
                         fetchType = FetchType.LAZY))
})
List<User> findAll();

// id 定义一个Results,便于以后用
// 可以用
@ResultMap("userMap")
```

#### @Result

```java
id 是否是主键字段
column 数据库的列名
property 需要装配的属性名
one 需要使用的@One 注解（@Result（one=@One）（）））
many 需要使用的@Many 注解（@Result（many=@many）（）））
```

#### @One

```java
@Result(property = "accounts",column = "id",
        one = @One(select = "com.itheima.dao.IAccountDao.findAccountByUid",
                         fetchType = FetchType.LAZY))
```

#### @Many

```java
 @Result(property = "accounts",column = "id",
            many = @Many(select ="com.itheima.dao.IAccountDao.findAccountByUid",
                         fetchType = FetchType.LAZY))
```



### 3 @SelectKey

> 新增后获取主键id

```java
@Insert("insert into user(username, address, sex, birthday) values(#{username},
        #{address},#{sex},#{birthday})")
@SelectKey(statement = "select last_insert_id()",keyProperty = "id",keyColumn = 			  "id",resultType = Integer.class,before = false)
public Integer insertOne(User user);

// 返回id会保存在 user中(不是返回值 Integer)
```

### 4 pojo 查询对象

> 封装xxVo 做查询对象

```java
@Select("select * from user where username like #{user.username} ")
//    @Select("select * from user where username like '%${value}%' ")
List<User> findUserByNameVo(UserVo userVo);

@Data
public class UserVo {
    private User user;
}

UserVo userVo = new UserVo();
User user1 = new User();
user1.setUsername("%l%");
userVo.setUser(user1);

List<User> users = sqlSession.getMapper(IUserMapper.class).findUserByNameVo(userVo);
//        List<User> users = sqlSession.getMapper(IUserMapper.class).findUserByName("mybatis");
for(User user : users){
    System.out.println(user);
}

// 注意: 在select中 #{user.username}
```

### 5 SqlMapConfig.xml配置

#### 配置中的内容和顺序

```java
-properties （属性）
	--property
	
-settings（全局配置参数）
	--setting
	
-typeAliases （类型别名）
	--typeAliase
	--package
    
-typeHandlers（类型处理器）

-objectFactory（对象工厂）

-plugins（插件）

-environments（环境集合属性对象）
    --environment（环境子属性对象）
        ---transactionManager（事务管理）
        ---dataSource（数据源）
        
-mappers （映射器）
    --mapper
    --package
```

#### properties



```xml
// 在内部写死
<properties>
    <property name="jdbc.driver" value="com.mysql.jdbc.Driver"/>
    <property name="jdbc.url" value="jdbc:mysql://localhost:3306/eesy"/>
    <property name="jdbc.username" value="root"/>
	<property name="jdbc.password" value="1234"/>
</properties>
   
// 在classpath下定义 db.properties 文件
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/eesy
jdbc.username=root
jdbc.password=1234

// 使用
<properties resource="jdbcConfig.properties"/>
<environments default="mysql">
    <environment id="mysql">
        <transactionManager type="JDBC"/>
        <!--配置连接池-->
        <dataSource type="POOLED">
            <property name="driver" value="${jdbc.driver}"/>
            <property name="url" value="${jdbc.url}"/>
            <property name="username" value="${jdbc.username}"/>
            <property name="password" value="${jdbc.password}"/>
        </dataSource>
    </environment>
</environments>

```

#### typeAliases 类型别名

```xml
<typeAliases>
    <!-- 单个别名定义 -->
    <typeAlias alias="user" type="com.itheima.domain.User"/>
    <!-- 批量别名定义，扫描整个包下的类，别名为类名（首字母大写或小写都可以） -->
    <package name="com.itheima.domain"/>
    <package name=" 其它包 "/>
</typeAliases>
```

#### mappers

```xml
// 使用相对于类路径的资源
<mapper resource="com/itheima/dao/IUserDao.xml" />

// 使用 mapper 接口类路径
<mapper class="com.itheima.dao.UserDao"/>
注意：此种方法要求 mapper 接口名称和 mapper 映射文件名称相同,且放在同一个目录中

// 注册指定包下的所有 mapper 接口
<package name="cn.itcast.mybatis.mapper"/>
注意：此种方法要求  mapper  接口名称和 mapper  映射文件名称相同，且放在同一个目录中。
```

### 6  连接池

> 3中方式 

```xml
<environment id="mysql">
    <dataSource type="POOLED">
        <property name="driver" value="${jdbc.driver}"></property>
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </dataSource>
</environment>
```

#### POOLED

> 采用传统的javax.sql.DataSource规范中的连接池，mybatis中有针对规范的实现

#### UNPOOLED

> 采用传统的获取连接的方式，虽然也实现Javax.sql.DataSource接口，但是并没有使用池的思想。

#### JNDI

> 采用服务器提供的JNDI技术实现，来获取DataSource对象，不同的服务器所能拿到DataSource是不一样。
>
> 注意：如果不是web或者maven的war工程，是不能使用的。
> 我们课程中使用的是tomcat服务器，采用连接池就是dbcp连接池。

### 7 事务

```java
// 默认的session 是不自动提交的
sqlSession = new SqlSessionFactoryBuilder().build(is).openSession();
// 需要手动提交
sqlSession.commit();
// 回滚
sqlSession.rollback();

// 可以设置为自动提交
sqlSession = new SqlSessionFactoryBuilder().build(is).openSession(true);
```

### 8 动态sql

#### 动态查询@SelectProvider

```java
public interface EmployeeMapper {
   
    //动态查询  type:指定一个类    method:使用这个类中的selectWhitParamSql方法返回的sql字符串  作为查询的语句
    		@SelectProvider(type=Intefaceproxy.Dyno.EmployeeDynaSqlProvider.class,method="sel	ectWhitParamSql")
List<Employee> selectWithParam(Map<String,Object> param);

}

public class EmployeeDynaSqlProvider {
    //方法中的关键字是区分大小写的  SQL SELECT WHERE
    //该方法会根据传递过来的map中的参数内容  动态构建sql语句
    public String selectWhitParamSql(Map<String, Object> param) {
        return new SQL() {
            {
                SELECT("*");
                FROM("tb_employee");
                if (param.get("id")!=null) {
                    WHERE("id=#{id}");
                }
                if(param.get("loginname")!=null) {
                    WHERE("loginname=#{loginname}");
                }
                if(param.get("password")!=null) {
                    WHERE("password=#{password}");
                }
                if(param.get("name")!=null) {
                    WHERE("name=#{name}");
                }
                if(param.get("sex")!=null) {
                    WHERE("sex=#{sex}");
                }
                if(param.get("age")!=null) {
                    WHERE("age=#{age}");
                }
                if(param.get("phone")!=null) {
                    WHERE("phone=#{phone}");
                }
                if(param.get("sal")!=null) {
                    WHERE("sal=#{sal}");
                }
                if(param.get("state")!=null) {
                    WHERE("state=#{state}");
                }
            }
            
        }.toString();
    }
}
```

#### 动态插入@InsertProvider

```java
//动态插入
@InsertProvider(type=Intefaceproxy.Dyno.EmployeeDynaSqlProvider.class,method="insertEmployeeSql")
@Options(useGeneratedKeys=true,keyProperty="id")
int insertEmployee(Employee employee);

 public String insertEmployeeSql(Employee employee) {
        return new SQL() {
            {
                INSERT_INTO("tb_employee");
                if(employee.getLoginname()!=null) {
                    VALUES("loginname","#{loginname}");
                }
                if(employee.getPassword()!=null) {
                    VALUES("password", "#{password}");
                }
                if(employee.getName()!=null) {
                    VALUES("name", "#{name}");
                }
                if(employee.getSex()!=null) {
                    VALUES("sex", "#{sex}");
                }
                if(employee.getAge()!=null) {
                    VALUES("age", "#{age}");
                }
                if(employee.getPhone()!=null) {
                    VALUES("phone", "#{phone}");
                }
                if(employee.getSal()!=null) {
                    VALUES("sal", "#{sal}");
                }
                if(employee.getState()!=null) {
                    VALUES("state", "#{state}");
                }
            }
        }.toString();
    }
```

#### 动态更新@UpdateProvider

```java
//动态更新
@UpdateProvider(type=Intefaceproxy.Dyno.EmployeeDynaSqlProvider.class,method="updateEmployeeSql")
void updateEmployee(Employee employee);

public String updateEmployeeSql(Employee employee) {
        return new SQL() {
            {
                UPDATE("tb_employee");
                if(employee.getLoginname()!=null) {
                    SET("loginname=#{loginname}");
                }
                if(employee.getPassword()!=null) {
                    SET("password=#{password}");
                }
                if(employee.getName()!=null) {
                    SET("name=#{name}");
                }
                if(employee.getSex()!=null) {
                    SET("sex=#{sex}");
                }
                if(employee.getAge()!=null) {
                    SET("age=#{age}");
                }
                if(employee.getPhone()!=null) {
                    SET("phone=#{phone}");
                }
                if(employee.getSal()!=null) {
                    SET("sal=#{sal}");
                }
                if(employee.getState()!=null) {
                    SET("state=#{state}");
                }
                WHERE("id=#{id}");
            }
        }.toString();
    }
```

#### 动态删除@DeleteProvider

```java
//动态删除
@DeleteProvider(type=Intefaceproxy.Dyno.EmployeeDynaSqlProvider.class,method="deleteEmployeeSql")
void deleteEmployee(Employee employee);

public String deleteEmployeeSql(Employee employee) {
    return new SQL() {
        {
            DELETE_FROM("tb_employee");
            if(employee.getLoginname()!=null) {
                WHERE("loginname=#{loginname}");
            }
            if(employee.getPassword()!=null) {
                WHERE("password=#{password}");
            }
            if(employee.getName()!=null) {
                WHERE("name=#{name}");
            }
        }
    }.toString();
}
```

#### 另一种方式写sql

```java
public class EmployeeSQLProvider {
    //拿到查询总行数的SQL语句
    public String getCountSql(EmployeeQueryObject qo) {
        //创建SQL对象并设置select语句要查询的列
        SQL sql = new SQL().SELECT("count(0)"); 
        addFrom(sql); //添加from语句
        addWhere(sql, qo); //添加where语句
        return sql.toString();
    }

    //拿到查询集合对象的SQL语句
    public String getLimitSql(EmployeeQueryObject qo) {
        SQL sql = new SQL().SELECT("id, name, age");
        addFrom(sql);
        addWhere(sql, qo);
        return sql.toString();
    }

    //抽取拼接的from语句的方法
    private void addFrom(SQL sql) {
        sql.FROM("employee"); //往SQL对象中添加from语句
    }

    //抽取拼接的where语句的方法
    private void addWhere(SQL sql, EmployeeQueryObject qo) {
        //往SQL对象中动态的添加添加
        if (StringUtils.hasLength(qo.getName())) {
            sql.WHERE("e.name like #{name}");
        }
        if (qo.getMinAge() != null) {
            sql.WHERE("e.age >= #{minAge}");
        }
        if (qo.getMaxAge() != null) {
            sql.WHERE("e.age <= #{maxAge}");
        }
    }
}
```

