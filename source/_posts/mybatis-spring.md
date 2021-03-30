---
title: MyBatis+Spring整合
date: 2021-03-29 10:00:00
tags:
 - MyBatis
 - Spring
 - Java
---



#### 一.配置引入

1. 添加以下依赖到pom.xml 

   ```xml
		<!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.1</version>
        </dependency>
        <!--mybatis spring整合jar包-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.1</version>
        </dependency>
        <!--jdbc-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
        </dependency>
        <!--单元测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
   ```

2. 在`resources`目录下薪增`mybatis-config.xml`文件,内容如下:

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <environments default="development">
            <environment id="development">
                <transactionManager type="JDBC"/>
                <dataSource type="POOLED">
                    <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql://10.21.100.77:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                    <property name="username" value="root"/>
                    <property name="password" value="${password}"/>
                </dataSource>
            </environment>
        </environments>
        <mappers>
            <mapper resource="com/kaychen/springboot01/dao/UserMapper.xml"/>
        </mappers>
    </configuration>
    ```

3. 为了能正常引入mappers文件, 需要在`pom.xml`文件的`<build>`模块中增加

   ```xml
   		<resources>
               <resource>
                   <directory>src/main/java</directory>
                   <includes>
                       <include>**/*.properties</include>
                       <include>**/*.xml</include>
                   </includes>
                   <filtering>false</filtering>
               </resource>
           </resources>
   ```

4. 在`com/kaychen/springboot01/`目录下创建`dao`, `pojo`, `controller`,  `service`

   ![image-20210329161054355](C:\Users\CainChan\AppData\Roaming\Typora\typora-user-images\image-20210329161054355.png)

5. 在`utils`工具类包中创建`MybatisUtils.java`工具类文件

    ```java
    package com.kaychen.springboot01.utils;

    import org.apache.ibatis.io.Resources;
    import org.apache.ibatis.session.SqlSession;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.apache.ibatis.session.SqlSessionFactoryBuilder;

    import java.io.IOException;
    import java.io.InputStream;

    public class MybatisUtils {
        private  static SqlSessionFactory sqlSessionFactory;
        static {
            try {
                String resource = "mybatis-config.xml";
                InputStream inputStream = Resources.getResourceAsStream(resource);
                sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        public static SqlSession getSqlSession(){
            return sqlSessionFactory.openSession();
        }
    }

    ```

6. 在`pojo`目录中新增`User.java`数据库实体类文件

   ```java
   package com.kaychen.springboot01.pojo;
   
   public class User {
       private int id;
       private String username;
       private String password;
   
       public User(){
       }
       public User(int id,String username, String password){
           this.id = id;
           this.username = username;
           this.password = password;
       }
   
       public int getId(){
           return id;
       }
   
       public String getUsername() {
           return username;
       }
   
       public String getPassword() {
           return password;
       }
   
   }
   ```

   

6. 在`dao`目录中新增`userDao.java`接口类文件

   ```java
   package com.kaychen.springboot01.dao;
   
   import com.kaychen.springboot01.pojo.User;
   
   import java.util.List;
   
   public interface UserDao {
       List<User> listUser();
   }
   
   ```

7. 在`dao`目录中新增`UserMapper.xml`文件

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   
   <mapper namespace="com.kaychen.springboot01.dao.UserDao">
       <select id="listUser" resultType="com.kaychen.springboot01.pojo.User">
           select * from mybatis.user
       </select>
   </mapper>
   ```



#### 二.junit测试

1.在`com/kaychen/springboot01`目录下创建`dao`目录以及`UserDaoTest.java`文件

   ```java
package com.kaychen.springboot01.dao;

import com.kaychen.springboot01.pojo.User;
import com.kaychen.springboot01.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;


public class UserDaoTest {
    @Test
    public void test(){
        SqlSession sqlSession =  MybatisUtils.getSqlSession();
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        List<User> listUser = userDao.listUser();
        for (User user : listUser) {
            System.out.println(user);
        }
        sqlSession.close();
    }
}

   ```



#### 三. 整合springboot

1. 在`service`中新增`UserService.java`类文件

   ```java
   package com.kaychen.springboot01.service;
   
   import com.kaychen.springboot01.dao.UserDao;
   import com.kaychen.springboot01.pojo.User;
   import com.kaychen.springboot01.utils.MybatisUtils;
   import org.apache.ibatis.session.SqlSession;
   
   import java.util.List;
   
   public class UserService {
       public List<User> listUser(){
           SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserDao userdao = sqlSession.getMapper(UserDao.class);
           List<User> data = userdao.listUser();
        return data;
       }
   }
   ```

2. 在`controller`目录中新增`UserController.java`类文件

   ```java
   package com.kaychen.springboot01.controller;
   
   import com.kaychen.springboot01.service.UserService;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.ResponseBody;
   import org.springframework.web.bind.annotation.RestController;
   
   
   @RestController
   @RequestMapping("/api")
   public class UserController {
       @RequestMapping("/hello")
       @ResponseBody
    public Object listUser(){
           UserService userService = new UserService();
        return userService.listUser();
       }
   }
   ```

3. 请求`localhost/api/hello`得到返回数据

   ```json
   [
       {
           "id": 1,
           "username": "king",
           "password": "Zhengkai2021"
       }
   ]
   ```



#### 参考文档：

1. [MyBatis官方文档](https://mybatis.org/mybatis-3/zh/getting-started.html)

