---
title: Hibernate EntityManager
date: 2019-05-31 10:08:43
tags:
---
# 1. Hibernate Maven Dependencies
```xml
<hibernate.version>5.3.1.Final</hibernate.version>

<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>${hibernate.version}</version>
</dependency>
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-java8</artifactId>
    <version>${hibernate.version}</version>
</dependency>
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-entitymanager</artifactId>
    <version>${hibernate.version}</version>
    <exclusions>
        <exclusion>
            <groupId>cglib</groupId>
            <artifactId>cglib</artifactId>
        </exclusion>
        <exclusion>
            <groupId>dom4j</groupId>
            <artifactId>dom4j</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-spatial</artifactId>
    <version>${hibernate.version}</version>
</dependency>
<dependency>
    <groupId>org.hibernate.javax.persistence</groupId>
    <artifactId>hibernate-jpa-2.1-api</artifactId>
    <version>1.0.0.Final</version>
</dependency>
```
# 2. Database
I am using below query to create a test table in Postgresql database.
```sql
CREATE SEQUENCE users_id_seq;
create table users (
    Id bigint not null DEFAULT NEXTVAL('users_id_seq'),
    email varchar(255) not NULL,
    last_name varchar(255) not NULL,
    first_name varchar(255) not NULL,
    primary key (Id)
);Entity
ALTER  SEQUENCE users_id_seq OWNED BY users.id;
```
# 3. Configuration
## 3.1 Defining the Entity
I will create a User class and define it as entity using @Entity annotation.
```java
package me.syus.myproject.domain;

import javax.persistence.*;
import static javax.persistence.GenerationType.SEQUENCE;
import javax.persistence.Entity;
import javax.persistence.Table;

@Entity
@Table(name = "users")

public class User {
    @Id
    @GeneratedValue(strategy = SEQUENCE, generator = "users_id_seq1")
    @SequenceGenerator(name = "users_id_seq1", sequenceName = "users_id_seq", allocationSize = 1)
    private Long Id;
    @Column
    private String email;
    @Column(name = "last_name")
    private String lastName;
    @Column(name = "first_name")
    private String firstName;
}
```
## 3.2 Using LocalContainerEntityManagerFactoryBean for setting up JPA EntityManagerFactory
```java
@Bean(name = "entityManagerFactory")
public LocalContainerEntityManagerFactoryBean entityManagerFactoryBean() {
    LocalContainerEntityManagerFactoryBean factoryBean = new LocalContainerEntityManagerFactoryBean();
    factoryBean.setDataSource(getDataSource());
    factoryBean.setPackagesToScan(new String[]{"me.syus.myproject.domain"});
    factoryBean.setPersistenceProviderClass(HibernatePersistenceProvider.class);
    Properties props = new Properties();
    props.put("hibernate.dialect", "org.hibernate.spatial.dialect.postgis.PostgisDialect");
    props.put("hibernate.hbm2ddl.auto", "validate");
    props.put("hibernate.connection.charSet", "UTF-8");
    props.put("hibernate.show_sql", "true");
    factoryBean.setJpaProperties(props);
    return factoryBean;
}
3.3 The DataSourceInitializer.java file
package me.syus.myproject.config;

import org.apache.commons.dbcp.BasicDataSource;
import org.hibernate.jpa.HibernatePersistenceProvider;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean;

import javax.sql.DataSource;
import java.util.Properties;

@Configuration

public class DataSourceInitializer {
    protected String databaseUrl = "jdbc:postgresql://localhost:5432/myproject";
    protected String databaseUserName = "admin";
    protected String databasePassword = "password";
    protected String driverClassName = "org.postgresql.ds.PGSimpleDataSource";


    @Bean(name = "dataSource")
    public DataSource getDataSource() {
        DataSource dataSource = createDataSource();
        return dataSource;
    }

    private BasicDataSource createDataSource() {
        BasicDataSource dataSource = new BasicDataSource();
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUrl(databaseUrl);
        dataSource.setUsername(databaseUserName);
        dataSource.setPassword(databasePassword);
        dataSource.setTestOnBorrow(true);
        dataSource.setTestOnReturn(true);
        dataSource.setTestWhileIdle(true);
        dataSource.setTimeBetweenEvictionRunsMillis(1800000);
        dataSource.setNumTestsPerEvictionRun(3);
        dataSource.setMinEvictableIdleTimeMillis(1800000);
        return dataSource;
    }

    @Bean(name = "entityManagerFactory")
    public LocalContainerEntityManagerFactoryBean entityManagerFactoryBean() {
        LocalContainerEntityManagerFactoryBean factoryBean = new LocalContainerEntityManagerFactoryBean();
        factoryBean.setDataSource(getDataSource());
        factoryBean.setPackagesToScan(new String[]{"me.syus.myproject.domain"});
        factoryBean.setPersistenceProviderClass(HibernatePersistenceProvider.class);
        Properties props = new Properties();
        props.put("hibernate.dialect", "org.hibernate.spatial.dialect.postgis.PostgisDialect");
        props.put("hibernate.hbm2ddl.auto", "validate");
        props.put("hibernate.connection.charSet", "UTF-8");
        props.put("hibernate.show_sql", "true");
        factoryBean.setJpaProperties(props);
        return factoryBean;
    }
}
```
