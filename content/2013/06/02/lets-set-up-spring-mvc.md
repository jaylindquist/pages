+++
menu = "main"
date = "Mon, 03 Jun 2013 03:15:24 +0000"
title = "Lets set up Spring MVC"
draft = false
+++

Welcome to the [Modern Web](http://www.springsource.org/features/modern-web), here's your library. Spring MVC comes standard with the monolith that is the Spring Framework. It certainly makes creating a web app easy... once it's set up. Getting started, though, is the difficult part.

This isn't meant to be a very educational article, only an instructive article. If you are unfamiliar with the concepts listed, google is your friend. There are a lot of moving pieces to this, so lets dive in.

## Requirements
What do we want to accomplish? We want to set up Spring MVC to handle all *.html files, not interact with resources such as javascript and CSS files, forward rendering to JSP files under WEB-INF/jsp and use Logback for logging.

## Dependencies
There's one downside for Spring, a lot of jars are involved. To accomplish the above we need the following jars:

```
aopalliance-1.0.jar
commons-logging-1.1.3.jar
groovy-all-2.1.1.jar
jcl-over-slf4j-1.7.5.jar
jstl-1.2.jar
logback-classic-1.0.13.jar
logback-core-1.0.13.jar
slf4j-api-1.7.5.jar
spring-aop-3.2.3.RELEASE.jar
spring-beans-3.2.3.RELEASE.jar
spring-context-3.2.3.RELEASE.jar
spring-core-3.2.3.RELEASE.jar
spring-expression-3.2.3.RELEASE.jar
spring-web-3.2.3.RELEASE.jar
spring-webmvc-3.2.3.RELEASE.jar
```
Your versions may vary

## File Structure
We will order our files in the following directory structure:

```
\---src
    +---main
    |   +---groovy
    |   |   \---com
    |   |       \---mycom
    |   |           \---<controller>.groovy
    |   \---resources
    |       \---logback.xml
    \---webapp
        +---css
        +---images
        +---js
        \---WEB-INF
            +---web.xml
            +---spring-servlet.xml
            \---jsp
                \---<view>.jsp
```

`<controller>.groovy` will hold the controller code that will load the model and forward to the `<view>.jsp` under `WEB-INF/jsp`. `logback.xml` will configure our logging to print out all the debug statements to make sure the app is working correctly. `css`, `images` and `js` hold the appropriate resource files. `spring-servlet.xml` configures Spring MVC to find our controllers and views. And `web.xml` is the standard deployment descriptor.

## web.xml

The deployment descriptor sets up 4 items: the welcome files list, set to index.html, <a title="defaultHtmlEscape" href="http://static.springsource.org/spring/docs/3.2.3.RELEASE/spring-framework-reference/htmlsingle/#spring.tld.htmlEscape">default HTML escaping</a> to prevent XSS attacks, the <a title="Spring MVC DispatcherServlet" href="http://static.springsource.org/spring/docs/3.2.3.RELEASE/spring-framework-reference/htmlsingle/#mvc-servlet">DispatcherServlet</a>, and which files Spring MVC will handle.

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee" version="2.5">
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
  </welcome-file-list>

  <!-- Escape HTML output by default -->
  <context-param>
    <param-name>defaultHtmlEscape</param-name>
    <param-value>true</param-value>
  </context-param>

  <!-- This servlet named "spring" will make Spring MVC look for
       spring-servlet.xml under WEB-INF -->
  <servlet>
    <servlet-name>spring</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
  </servlet>

  <!-- Map Spring MVC to all .html files -->
  <servlet-mapping>
    <servlet-name>spring</servlet-name>
    <url-pattern>*.html</url-pattern>
  </servlet-mapping>
</web-app>
```

## spring-servlet.xml

The *-servlet.xml file is automatically loaded by the DispatcherServlet based on the servlet name. In this case, Spring will look for spring-servlet.xml. We need to configure this file to do the following: scan our source code for controllers, note that Spring MVC will be driven by annotations (not necessarily needed, but will prevent issues in the future) and tell Spring MVC to resolves views in our WEB-INF/jsp folder.

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:p="http://www.springframework.org/schema/p"
  xmlns:context="http://www.springframework.org/schema/context"
  xmlns:mvc="http://www.springframework.org/schema/mvc"
  xsi:schemaLocation="
    http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
    http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

  <!-- All annotated Spring classes under this package will be picked up -->
  <context:component-scan base-package="com.mycom"/>
  <!-- Drive Spring MVC with annotations -->
  <mvc:annotation-driven />

  <!-- Any view returned by the app will be resolved to WEB-INF/lib/[view].jsp -->
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <property name="suffix" value=".jsp"/>
  </bean>
</beans>
```

## Resources

Since Spring MVC is mapped to *.html, only html files will be picked up. So any images, CSS and javascript can be placed in their respective folders and be picked up with out being processed.

## Controllers

Any groovy files with the @Controller annotation will be picked up Spring. The controller needs to hold methods that map to a specific URL (an html file, in this case). It will then need return a Model and or View. The string representing the view will be mapped to the corresponding JSP file under WEB-INF/jsp.

```
package com.mycom.controller

import org.springframework.stereotype.Controller
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.servlet.ModelAndView

// Ensure Spring picks this class up as a controller
@Controller
class DefaultController {
  // map index.html to WEB-INF/jsp/index.jsp
  @RequestMapping('/index.html')
  def index() {
    return 'index'
  }

  // map search.html to WEB-INF/jsp/search.jsp with a SearchResults as the model
  @RequestMapping('/search.html')
  def search() {
    return new ModelAndView('search', 'results', new SearchResults(...))
  }
}
```

## Views

Our views a simple JSP pages that get resolved to WEB-INF/jsp/<view-name>.jsp. Since this is just an article about setup, you will need to see the <a title="Spring MVC views" href="http://static.springsource.org/spring/docs/3.2.3.RELEASE/spring-framework-reference/htmlsingle/#mvc-viewresolver">documentation </a>for more information.

## logback.xml

Lastly we configure our logging to print out all our debug statements, letting us know if everything is working correctly.

```
<?xml version="1.0" encoding="UTF-8" ?>
<configuration debug="true">
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>
        %d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n
      </pattern>
    </encoder>
  </appender>

  <logger name="org.springframework" level="DEBUG"/>
  <root level="DEBUG">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```
