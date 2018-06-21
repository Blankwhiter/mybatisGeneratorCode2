# mybatisGeneratorCode2

### 代码工程结构图：
![](https://i.imgur.com/KHxMVyq.png)

#### 第一步：引入所需要的jar包以及maven插件
```xml
	  <plugin>
                <!--Mybatis-generator插件,用于自动生成Mapper和POJO-->
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.5</version>
                <configuration>
                    <!--配置文件的位置-->
                    <configurationFile>src/main/resources/generator/mybatis_generator_config.xml
                    </configurationFile>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
                <executions>
                    <execution>
                        <id>Generate MyBatis Artifacts</id>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                    </execution>
                </executions>

                <dependencies>
                    <!--防止找不到驱动 报出如下异常：Exception getting JDBC Driver: com.mysql.jdbc.Driver-->
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>5.1.43</version>
                    </dependency>

                    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
                    <dependency>
                        <groupId>org.mybatis</groupId>
                        <artifactId>mybatis</artifactId>
                        <version>3.4.4</version>
                    </dependency>
                    <!-- mybatis-generator-core 反向生成java代码-->
                    <dependency>
                        <groupId>org.mybatis.generator</groupId>
                        <artifactId>mybatis-generator-core</artifactId>
                        <version>1.3.5</version>
                    </dependency>

                </dependencies>

            </plugin>
```
#### 第二步：编写所需的配置文件：generator.properties、mybatis_generator_config.xml
说明：这里面需要读者改写五个自己的配置--> ***jdbc.connectionURL***（数据库连接）***jdbc.userId***（用户名）***jdbc.password***（密码）***table.name***（需要生成实体类的表名）***table.domainObjectName***（对应想生成实体类的类名）

#### generator.properties 配置如下：
```xml
jdbc.driverLocation=D:\\mavenLocal\\mysql\\mysql-connector-java\\5.1.43\\mysql-connector-java-5.1.43.jar
# 该驱动会导致 XXByPrimaryKey 生成失败
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.connectionURL=jdbc:mysql://localhost:3306/world?serverTimezone=UTC&useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=false&tinyInt1isBit=false
jdbc.userId=root
jdbc.password=111111

# defaultModelType
# 1，conditional：类似hierarchical；
# 2，flat：所有内容（主键，blob）等全部生成在一个对象中； default
# 3，hierarchical：主键生成一个XXKey对象(key class)，Blob等单独生成一个对象，其他简单属性在一个对象中(record class)
context.defaultModelType=flat
# targetRuntime:
# 1，MyBatis3：默认的值，生成基于MyBatis3.x以上版本的内容，包括XXXBySample；
# 2，MyBatis3Simple：类似MyBatis3，只是不生成XXXBySample；
context.targetRuntime=MyBatis3

# 相关表的配置
table.name=t_sys_dept
table.domainObjectName=SysDept

java.model.generator.targetPackage=com.example.demo.po
java.client.generator.targetPackage=com.example.demo.dao
sql.map.generator.targetPackage=mappers

table.enableInsert=true
table.enableDeleteByPrimaryKey=true
table.enableUpdateByPrimaryKey=true
table.enableSelectByPrimaryKey=true

table.enableDeleteByExample=true
table.enableUpdateByExample=true
table.enableSelectByExample=true
table.enableCountByExample=true
# column:主键的列名
table.generatedKey.column=id
# sqlStatement：要生成的selectKey语句，有以下可选项：
# Cloudscape:相当于selectKey的SQL为： VALUES IDENTITY_VAL_LOCAL()
# DB2       :相当于selectKey的SQL为： VALUES IDENTITY_VAL_LOCAL()
# DB2_MF    :相当于selectKey的SQL为：SELECT IDENTITY_VAL_LOCAL() FROM SYSIBM.SYSDUMMY1
# Derby      :相当于selectKey的SQL为：VALUES IDENTITY_VAL_LOCAL()
# HSQLDB      :相当于selectKey的SQL为：CALL IDENTITY()
# Informix  :相当于selectKey的SQL为：select dbinfo('sqlca.sqlerrd1') from systables where tabid=1
# MySql      :相当于selectKey的SQL为：SELECT LAST_INSERT_ID()
# SqlServer :相当于selectKey的SQL为：SELECT SCOPE_IDENTITY()
# SYBASE      :相当于selectKey的SQL为：SELECT @@IDENTITY
# JDBC      :相当于在生成的insert元素上添加useGeneratedKeys="true"和keyProperty属性
table.generatedKey.sqlStatement=JDBC


# 实体类生成的位置
# Model模型生成器,用来生成含有主键key的类，记录类 以及查询Example类
# targetPackage     指定生成的model生成所在的包名
# targetProject     指定在该项目下所在的路径
java.model.generator.targetProject=src/main/java

# *Mapper.xml 文件的位置
# Mapper映射文件生成所在的目录 为每一个数据库的表生成对应的SqlMap文件
sql.map.generator.targetProject=src/main/resources

# Mapper 接口文件的位置
# 客户端代码，生成易于使用的针对Model对象和XML配置文件 的代码
# type="ANNOTATEDMAPPER",生成Java Model 和基于注解的Mapper对象
# type="MIXEDMAPPER",生成基于注解的Java Model 和相应的Mapper对象
# type="XMLMAPPER",生成SQLMap XML文件和独立的Mapper接口
java.client.generator.targetProject=src/main/java
java.client.generator.type=XMLMAPPER

```
#### mybatis_generator_config.xml配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<!-- 配置生成器 -->
<generatorConfiguration>
    <!-- 可以用于加载配置项或者配置文件，在整个配置文件中就可以使用${propertyKey}的方式来引用配置项
    resource：配置资源加载地址，使用resource，MBG从classpath开始找，比如com/myproject/generatorConfig.properties
    url：配置资源加载地质，使用URL的方式，比如file:///C:/myfolder/generatorConfig.properties.
    注意，两个属性只能选址一个;
    另外，如果使用了mybatis-generator-maven-plugin，那么在pom.xml中定义的properties都可以直接在generatorConfig.xml中使用
    <properties resource="" url="" />
    -->
    <!-- 在MBG工作的时候，需要额外加载的依赖包
     location属性指明加载 jar/zip包的全路径-->
    <properties resource="generator/generator.properties"></properties>
    <!-- 本地数据库驱动程序jar包的全路径 -->
    <!--<classPathEntry location="${jdbc.driverLocation}"/>-->
    <!--
    context:生成一组对象的环境
    id:必选，上下文id，用于在生成错误时提示
    defaultModelType:指定生成对象的样式
        1，conditional：类似hierarchical；
        2，flat：所有内容（主键，blob）等全部生成在一个对象中；
        3，hierarchical：主键生成一个XXKey对象(key class)，Blob等单独生成一个对象，其他简单属性在一个对象中(record class)
    targetRuntime:
        1，MyBatis3：默认的值，生成基于MyBatis3.x以上版本的内容，包括XXXBySample；
        2，MyBatis3Simple：类似MyBatis3，只是不生成XXXBySample；
    introspectedColumnImpl：类全限定名，用于扩展MBG
    -->
    <context id="context" defaultModelType="${context.defaultModelType}" targetRuntime="${context.targetRuntime}" >
        <!-- 自动识别数据库关键字，默认false，如果设置为true，根据SqlReservedWords中定义的关键字列表；
        一般保留默认值，遇到数据库关键字（Java关键字），使用columnOverride覆盖
        -->
        <property name="autoDelimitKeywords" value="false" />
        <!-- 生成的Java文件的编码 -->
        <property name="javaFileEncoding" value="UTF-8" />
        <!-- 格式化java代码 -->
        <property name="javaFormatter" value="org.mybatis.generator.api.dom.DefaultJavaFormatter" />
        <!-- 格式化XML代码 -->
        <property name="xmlFormatter" value="org.mybatis.generator.api.dom.DefaultXmlFormatter" />

        <!-- beginningDelimiter和endingDelimiter：指明数据库的用于标记数据库对象名的符号，比如ORACLE就是双引号，MYSQL默认是`反引号； -->
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>


        <!--实体类增加序列化-->
        <plugin type="org.mybatis.generator.plugins.SerializablePlugin" ></plugin>

        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="false"/>
            <!-- 这个元素用来去除指定生成的注释中是否包含生成的日期 false:表示保护 -->
            <!-- 如果生成日期，会造成即使修改一个字段，整个实体类所有属性都会发生变化，不利于版本控制，所以设置为true -->
            <property name="suppressDate" value="true" />
        </commentGenerator>
        <!-- 数据库的相关配置 -->
        <jdbcConnection driverClass="${jdbc.driverClass}" connectionURL="${jdbc.connectionURL}" userId="${jdbc.userId}"
                        password="${jdbc.password}" />
        <!-- 非必需，类型处理器，在数据库类型和java类型之间的转换控制-->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>

        <!-- 实体类生成的位置 -->
        <!-- Model模型生成器,用来生成含有主键key的类，记录类 以及查询Example类
          targetPackage     指定生成的model生成所在的包名
          targetProject     指定在该项目下所在的路径
      -->
        <javaModelGenerator targetPackage="${java.model.generator.targetPackage}"
                            targetProject="${java.model.generator.targetProject}">
            <!--  for MyBatis3/MyBatis3Simple
              自动为每一个生成的类创建一个构造方法，构造方法包含了所有的field；而不是使用setter；
            -->
            <property name="constructorBased" value="false"/>
            <!-- 是否允许子包，即targetPackage.schemaName.tableName -->
            <property name="enableSubPackages" value="false" />
            <!-- 建立的Model对象是否 不可改变  即生成的Model对象不会有 setter方法，只有构造方法 -->
            <property name="immutable" value="false"/>
            <!-- 给Model添加一个父类 -->
            <!--<property name="rootClass" value="com.foo.louis.Hello"/>-->
            <!-- 是否对类CHAR类型的列的数据进行trim操作 -->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>

        <!-- *Mapper.xml 文件的位置 -->
        <!--Mapper映射文件生成所在的目录 为每一个数据库的表生成对应的SqlMap文件 -->
        <sqlMapGenerator targetPackage="${sql.map.generator.targetPackage}"
                         targetProject="${sql.map.generator.targetProject}">
            <property name="enableSubPackages" value="false" />
        </sqlMapGenerator>

        <!-- Mapper 接口文件的位置 -->
        <!-- 客户端代码，生成易于使用的针对Model对象和XML配置文件 的代码
               type="ANNOTATEDMAPPER",生成Java Model 和基于注解的Mapper对象
               type="MIXEDMAPPER",生成基于注解的Java Model 和相应的Mapper对象
               type="XMLMAPPER",生成SQLMap XML文件和独立的Mapper接口
       -->
        <javaClientGenerator targetPackage="${java.client.generator.targetPackage}"
                             targetProject="${java.client.generator.targetProject}"
                             type="${java.client.generator.type}">
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>

        <!-- 相关表的配置 -->
        <table
                tableName="${table.name}"
                domainObjectName="${table.domainObjectName}"
                enableInsert="${table.enableInsert}"
                enableDeleteByPrimaryKey="${table.enableDeleteByPrimaryKey}"
                enableUpdateByPrimaryKey="${table.enableUpdateByPrimaryKey}"
                enableSelectByPrimaryKey="${table.enableSelectByPrimaryKey}"

                enableDeleteByExample="${table.enableDeleteByExample}"
                enableUpdateByExample="${table.enableUpdateByExample}"
                enableSelectByExample="${table.enableSelectByExample}"
                enableCountByExample="${table.enableCountByExample}"
        >

        <!-- generatedKey用于生成生成主键的方法，
          如果设置了该元素，MBG会在生成的<insert>元素中生成一条正确的<selectKey>元素，该元素可选
          column:主键的列名；
          sqlStatement：要生成的selectKey语句，有以下可选项：
              Cloudscape:相当于selectKey的SQL为： VALUES IDENTITY_VAL_LOCAL()
              DB2       :相当于selectKey的SQL为： VALUES IDENTITY_VAL_LOCAL()
              DB2_MF    :相当于selectKey的SQL为：SELECT IDENTITY_VAL_LOCAL() FROM SYSIBM.SYSDUMMY1
              Derby      :相当于selectKey的SQL为：VALUES IDENTITY_VAL_LOCAL()
              HSQLDB      :相当于selectKey的SQL为：CALL IDENTITY()
              Informix  :相当于selectKey的SQL为：select dbinfo('sqlca.sqlerrd1') from systables where tabid=1
              MySql      :相当于selectKey的SQL为：SELECT LAST_INSERT_ID()
              SqlServer :相当于selectKey的SQL为：SELECT SCOPE_IDENTITY()
              SYBASE      :相当于selectKey的SQL为：SELECT @@IDENTITY
              JDBC      :相当于在生成的insert元素上添加useGeneratedKeys="true"和keyProperty属性
      <generatedKey column="" sqlStatement=""/>
       -->
            <generatedKey column="${table.generatedKey.column}" sqlStatement="${table.generatedKey.sqlStatement}" identity="true" />
        </table>
    </context>
</generatorConfiguration>
```


#### 第三步：通过idea开发工具右侧栏Maven projects中找到Plugins，在Plugins中找到generate，双击运行。如图所示：
![maven projects](https://i.imgur.com/l63hwRQ.png)

下图是运行后，目录结构变化。

 ![](https://i.imgur.com/NDMKJtV.png)
