# Configurar projeto JSF + PrimeFaces + JUnit + Hibernate (baseado em Maven)

## Pastas necessárias
* src/main/java
* src/main/webapp
    * resources
    * WEB-INF
        * templates
* src/test/java
* conf

## Arquivos necessários
* /pom.xml
* /src/main/webapp/WEB-INF/web.xml
* /src/main/webapp/WEB-INF/beans.xml
* /conf/hibernate.cfg.xml

### pom.xml
Principais dependências:
* `<scope>test</scope>`
    * junit - junit
    * org.mockito - mockito-all
* `<scope>provided</scope>`
    * javax - javaee-api
* Escopo padrão 
    * org.primefaces - primefaces 
    * com.sun.faces - jsf-api
    * org.hibernate - hibernate-core
    * org.hibernate - hibernate-entitymanager

Atenção também para as seguintes configurações:
```xml
[...]
<packaging>war</packaging>
[...]
<build>
    <finalName>${project.artifactId}</finalName>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
[...]
```

### web.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="3.1" 
		xmlns="http://xmlns.jcp.org/xml/ns/javaee" 
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
		xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd">
	<context-param>
        <param-name>javax.faces.PROJECT_STAGE</param-name>
        <param-value>Development</param-value>
    </context-param>
    <context-param>
        <param-name>javax.faces.ENABLE_CDI_RESOLVER_CHAIN</param-name>
        <param-value>true</param-value>
    </context-param>
    <context-param>
        <param-name>javax.faces.INTERPRET_EMPTY_STRING_SUBMITTED_VALUES_AS_NULL</param-name>
        <param-value>true</param-value>
    </context-param>
    <servlet>
        <servlet-name>Faces Servlet</servlet-name>
        <servlet-class>javax.faces.webapp.FacesServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>Faces Servlet</servlet-name>
        <url-pattern>*.xhtml</url-pattern>
    </servlet-mapping>
    <session-config>
        <session-timeout>
            30
        </session-timeout>
    </session-config>
    <welcome-file-list>
        <welcome-file>index.xhtml</welcome-file>
    </welcome-file-list>
</web-app>
```

### beans.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<faces-config version="2.1" xmlns="http://java.sun.com/xml/ns/javaee"
       xmlns:xi="http://www.w3.org/2001/XInclude" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-facesconfig_2_2.xsd">
</faces-config>
```

### configurate.cfg.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC "-//Hibernate/Hibernate Configuration DTD 3.0//EN" "http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
    	<property name="connection.url">jdbc:postgresql://localhost/myDBName</property> <!-- BD Name -->
   		<property name="connection.driver_class">org.postgresql.Driver</property> <!-- DB Driver -->
   		<property name="connection.username">postgres</property> <!-- DB User -->
   		<property name="connection.password">postgres</property> <!-- DB Password -->
    	
    	<property name="dialect">org.hibernate.dialect.PostgreSQLDialect</property>
    	<property name="hbm2ddl.auto">update</property>
    	
    	<property name="show_sql">true</property>
    	<property name="format_sql">false</property>
    	
    	<mapping class="com.jonnrauber.shoppinglist.model.ListaDeCompras" />
    	<mapping class="com.jonnrauber.shoppinglist.model.Produto" />
    	<mapping class="com.jonnrauber.shoppinglist.model.ProdutoLista" />
    </session-factory>
</hibernate-configuration>
```