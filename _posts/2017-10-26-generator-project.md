---
layout:     post
title:      mybatis逆向工程
subtitle:   快速生成mybatis工程文件
date:       2017-10-26
author:     lc
header-img: img/post-bg-universe.jpg
catalog:    true
tags:
    - mybatis
---

## 所需文件 ##
* generator.java 生成程序
* generatorConfig.xml 配置文件
* mybatis-generator-core 引入包

### 1. generator.java ###
```
public class Generator {
    public void generator() throws Exception{
        List<String> warnings = new ArrayList<String>();
        boolean overwrite = true;
        File configFile = new File("配置文件绝对路径");
        ConfigurationParser cp =new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(
                config,callback,warnings);
        myBatisGenerator.generate(null);

    }
    //执行程序，自动生成文件
    public static void main(String[] args){
        try{
            Generator generator = new Generator();
            generator.generator();
        }catch(Exception e){
            e.printStackTrace();
        }
    }
}
```
### 2. generatorConfig.xml ###
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <context id="default" targetRuntime="MyBatis3Simple">
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="数据库连接地址"
                        userId="用户名"
                        password="密码"/>
        <javaTypeResolver>
        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和 NUMERIC 类型解析为java.math.BigDecimal -->
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <javaModelGenerator targetPackage="包名"
            targetProject="po类存放路径">
            <property name="constructorBased" value="false"/>
            <property name="enableSubPackages" value="false"/>
            <property name="immutable" value="false"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <sqlMapGenerator targetPackage="/"
            targetProject="mapper.xml文件存放路径">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
        <javaClientGenerator targetPackage="mapper接口包名" 
            targetProject="mapper接口存放路径" type="XMLMAPPER">
            <property name="enableSubPackages" value=""/>
            <property name="exampleMethodVisibility" value=""/>
            <property name="methodNameCalculator" value=""/>
            <property name="rootInterface" value=""/>
        </javaClientGenerator>
        <table tableName="要关联的表名" schema=""/>        
    </context>
</generatorConfiguration>
```
### 3. 引入mybatis-generator-core ###
```
<dependency>
	<groupId>org.mybatis.generator</groupId>
	<artifactId>mybatis-generator-core</artifactId>
	<version>1.3.5</version>
</dependency>
```
