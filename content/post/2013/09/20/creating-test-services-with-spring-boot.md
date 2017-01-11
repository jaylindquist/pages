+++
 
date = "Sat, 21 Sep 2013 03:39:35 +0000"
title = "Creating Test Services with Spring Boot"
draft = false
 
+++

One of the coolest new technologies from the Spring Foundation has to be the new <a href="http://spring.io/blog/2013/08/06/spring-boot-simplifying-spring-for-everyone" title="Spring Boot   Simplifying Spring for Everyone">Spring Boot</a> project. Spring Boot takes all the common template / bootstrap / setup code that everyone is doing already and just does it for you. 

It also covers all the main Spring technologies: Spring MVC, Spring Batch, Spring Security, etc. Need a database? Just add the schema. Need a service? Just add a request mapping. Spring Boot will interpret your setup and start a server if needed.

<a href="https://github.com/spring-projects/spring-boot" title="Spring Boot on Github">GitHub</a> already has the basics for creating simple examples. For example, from the Spring Blog (once you have the Spring Boot command line installed) the following groovy code will run a web site:

```java
@Controller
class ThisWillActuallyRun {
    @RequestMapping("/")
    @ResponseBody
    String home() {
        return "Hello World!"
    }
}
```

Amazingly simple to use.

It gets a little more complicated when you need an advanced site. Specifically, what do we need in order to create a database driven REST data service? All of the code for this example is in <a href="https://github.com/jaylindquist/spring-boot-service-template" title="Spring Boot Service Template" target="_blank">my GitHhub repo</a>.

## Build file

Something that I always appreciate, is a gradle plugin, and Spring Boot comes through. The following build.gradle loads in Spring Boot, a few more dependencies to support Groovy, an in memory database and JSON parsing for the service, then adds a `run` command to make life easier.

```groovy
buildscript {
    repositories {
        mavenCentral()
        maven {
            url "http://repo.springsource.org/milestone"
        }
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:0.5.0.M3")
    }
}

repositories {
    mavenCentral()
    maven {
        url "http://repo.springsource.org/milestone"
    }
}

apply plugin: 'groovy'
apply plugin: 'eclipse'
apply plugin: 'spring-boot'

sourceCompatibility = 1.6
version = '1.0'

dependencies {
    compile 'org.codehaus.groovy:groovy-all:2.1.7'
    compile 'org.springframework.boot:spring-boot-starter-web:0.5.0.M3'
    compile 'org.springframework:spring-jdbc:4.0.0.M3'
    runtime 'org.codehaus.jackson:jackson-mapper-asl:1.9.12'
    runtime 'org.hsqldb:hsqldb:latest.release'
}

task run(type: JavaExec) {
    classpath configurations.runtime, sourceSets.main.output
    main = 'com.mycom.App'
}

task wrapper(type: Wrapper) {
    gradleVersion = '1.7'
}
```

## Project Structure

You have two options to run Spring Boot projects. The first is simply use the Spring Boot command line against a class you want to execute, as with the example from the Spring blog. The second options is for more complex projects that are set up to use an IDE and custom project structure. We'll use the latter for this example.

I've used the standard Groovy Jar project structure for Gradle, but any structure will do. Note that, even though we are building a web app, the project structure is for a Jar. We have no need to create the web.xml.

## Main

From the build file above, the run task simply executes the main class `com.mycom.App`. This class will tell Spring Boot how to run our app and set up all our autowiring needs.

```java
package com.mycom 

import org.springframework.boot.*
import org.springframework.boot.autoconfigure.*
import org.springframework.context.annotation.ComponentScan
import org.springframework.context.annotation.Configuration
import org.springframework.stereotype.*
import org.springframework.web.bind.annotation.*

@Configuration
@ComponentScan(basePackages = "com.mycom" )
@EnableAutoConfiguration
class App {
    public static void main(String[] args) throws Exception {
        def app = new SpringApplication(App.class)
        app.showBanner = false
        app.run(args)
    }
}
```

I've shown that <a href="https://github.com/spring-projects/spring-boot/blob/master/spring-boot/src/main/java/org/springframework/boot/SpringApplication.java" title="Spring Application Source" target="_blank">`SpringApplication`</a> actually includes a few custom properties you may be interested in. However, in most cases, the static `run(Object, args)` should be enough. Otherwise, note that this class configures the rest of the classes in `com.mycom` for Spring wiring.

## App Settings

Let's take a detour into the resources directory. Spring Boot has additional configuration steps in the `application.properties` file. Simply create this file, put it on your classpath, and fill in what is needed.

```
spring.application.name=spring-boot-service-template
server.port:9080
logging.file:logs/system.log

spring.database.schema=classpath*:schema-hsqldb.sql
```

Application name, server port and logging file are all completely optional and only serve as examples. The only important information in this file is the database schema. which is also in the resources directory. Spring Boot will load the database file and create the `DataSource` for us. 

## Schema

Here's our actual data. We'll follow the model from the <a href="http://bitwiseor.com/2013/08/03/lets-set-up-ember-js/" title="Lets set up Ember.js" target="_blank">Ember example</a>: 

* We have Franchises which represent a game franchise, like Legend of Zelda
* Within each franchise, we have a Game
* Each game has a release date

Our schema creates the tables and sample data in one file:

```sql
CREATE TABLE franchises (
    id BIGINT NOT NULL PRIMARY KEY,
    title VARCHAR(255)
);

CREATE TABLE games (
    id BIGINT NOT NULL,
    franchise_id BIGINT NOT NULL, 
    name VARCHAR(255),
    release DATE,
    FOREIGN KEY (franchise_id) REFERENCES franchises(id)
);

INSERT INTO franchises 
    (id, title) VALUES
    (0, 'Final Fantasy'),
    (1, 'Fire Emblem'),
    (2, 'The Legend of Zelda');

INSERT INTO games
    (franchise_id, id, name, release) VALUES
    (0, 0, 'Final Fantasy Type 0', '2011-10-27'),
    (0, 1, 'Final Fantasy IV: The After Years', '2008-02-18'),
    (0, 2, 'Final Fantasy VII', '1997-09-07'),
    (1, 0, 'Fire Emblem Awakening', '2013-02-04'),
    (1, 1, 'Fire Emblem Shadow Dragon', '2009-02-16'),
    (2, 0, 'The Legend of Zelda: Skyward Sword', '2011-11-20'),
    (2, 1, 'The Legend of Zelda: Ocarina of Time', '2007-02-26'),
    (2, 2, 'The Legend of Zelda: A Link to the Past', '1991-11-21');
```

## Logging

Just to note that logging is already configured for Logback and SLF4J. A logback.xml on the classpath will configure logging as you would expect.

## Service Code

Now to the actual service code that all the above supports. Spring Boot does all the work on configuring Spring MVC for us, so all that is left for us to do is the actual business logic for the REST services.

The services themselves will simply return the data from the database based on the resource requested. So, `/franchise/1` will return Fire Emblem franchise while `/franchise/1/game/0` will return Fire Emblem Awakening with its release date.

For Frachises:

```java
package com.mycom

import javax.sql.DataSource

import org.springframework.beans.factory.annotation.Autowired
import org.springframework.dao.EmptyResultDataAccessException
import org.springframework.jdbc.core.JdbcTemplate
import org.springframework.stereotype.Controller
import org.springframework.web.bind.annotation.PathVariable
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.ResponseBody

@Controller
class Franchise {
    private JdbcTemplate jdbcTemplate

    @Autowired
    public Franchise(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource)
    }
    
    @ResponseBody
    @RequestMapping('/franchise/{id}')
    String franchiseId(@PathVariable Long id) {
        try {
            return jdbcTemplate.queryForMap('SELECT id, title FROM franchises WHERE id=?', id)
        } catch(EmptyResultDataAccessException ex) {
            return Collections.EMPTY_MAP
        }
    }
    
    @ResponseBody
    @RequestMapping('/franchise/')
    String franchises() {
        try {
            return jdbcTemplate.queryForList('SELECT id, title FROM franchises')
        } catch(EmptyResultDataAccessException ex) {
            return Collections.EMPTY_MAP
        }
    }
}
```

And similarly for Games:

```java
package com.mycom

import javax.sql.DataSource

import org.springframework.beans.factory.annotation.Autowired
import org.springframework.dao.EmptyResultDataAccessException
import org.springframework.jdbc.core.JdbcTemplate
import org.springframework.stereotype.Controller
import org.springframework.web.bind.annotation.PathVariable
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.ResponseBody

import com.fasterxml.jackson.databind.node.NodeCursor.Object;

@Controller
class Game {
    private JdbcTemplate jdbcTemplate

    @Autowired
    public Game(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource)
    }
    
    @ResponseBody
    @RequestMapping('/franchise/{franchiseId}/game/{id}')
    String gameId(@PathVariable Long franchiseId, @PathVariable Long id) {
        try {
            return jdbcTemplate.queryForMap('SELECT id, name, release FROM games WHERE franchise_id=? AND id=?', franchiseId, id)
        } catch(EmptyResultDataAccessException ex) {
            return Collections.EMPTY_MAP
        }
    }
    
    @ResponseBody
    @RequestMapping('/franchise/{franchiseId}/game')
    String games(@PathVariable Long franchiseId) {
        try {
            return jdbcTemplate.queryForList('SELECT id, name, release FROM games WHERE franchise_id=?', franchiseId)
        } catch(EmptyResultDataAccessException ex) {
            return Collections.EMPTY_MAP
        }
    }
}
```

## Running

And that's it. Six code files and a build file gives us a full fledged REST service running locally, ready for an app to start consuming it.

To run the sample, open a command prompt and execute `gradlew run`. This will download dependencies, compile the code, package it into a jar and execute the main class.

Visit `http://localhost:9080/franchise/1` and other variants to see the JSON responses.

## Wrap up

Not only was the above easy to learn, but it opens up a lot of possibilities for local test services. Spring Boot can also easily be deployed to <a href="http://www.cloudfoundry.com/" target="_blank">Cloud Foundry</a>, Spring's favored cloud provider. 

In the end, how is Spring Boot anything other than magic? We currently have enough trouble accepting Groovy in an enterprise environment, and Groovy is just making Java easier to work with. Spring Boot makes Spring apps easier, so it will face the same hurdles in large organizations that Groovy faces. And, just like Groovy, the best way to introduce Spring Boot may be through testing. Because, "Who cares if it's only used for tests". Then, once we have it in all our tests, it will naturally begin to grow until we have production Spring Boot apps.

Hopefully, by the time this project reaches 1.0, we'll see a lot of clever new features to make our Spring developer's lives easier.
