学习笔记

Spring boot 默认扫描问题

最近想把对安全的了解写成一个对单独的spring 项目，提供spring cloud 项目方便引入一下就可以使用
但是在写到spring security browser的测试时无法引用到此包中的代码
研究了几天找了几种方法多不实用

这实际是springboot的默认扫描问题
package com.b2b2c.developer_center;
注意到没这里是com.b2b2c.developer_center
springboot是默认扫描子包目录下的class
我的包又是
com.zwl.security.securitybrowser
所有在载不了

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;

import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration;


import java.util.Properties;

@SpringBootApplication(scanBasePackages={
        "com.zwl.security.securitybrowser",
        "com.b2b2c.developer_center",
        "com.zwl.security.securitycore"
})
@MapperScan("com.b2b2c.developer_center.mapper")
public class DeveloperCenterApplication {
    public static void main(String[] args) {

        SpringApplication.run(DeveloperCenterApplication.class, args);
    }

}

