+++
 
date = "Tue, 29 May 2012 02:48:49 +0000"
title = "Setting Up Spring Batch"
draft = false
 
+++
Lets do the setup work that we'll rely on for the rest of the project. We want to configure Spring Batch's database, configure logging and create a simple reader and writer. All code available on the Google Code project page for <a href="http://code.google.com/p/glextensions/" title="GLExtensions on Google Code">GLExtensions</a>.

## Spring Batch Database

The database used by Spring Batch is very handy. It allows for restarting jobs after failure, logging of job progress and real time statistics via the Spring Batch Admin Console. There is also the option to use a Map based repository instead which allows for in-memory job runs, but lacks the advantages of the database repository. A third option is to use an in-memory database setup, no advantages from an actual database setup, but it will allow us to switch to a real database easier in the future. This is the setup that Spring Tool Suite gives us when selecting a Spring Batch project template.

First, the repository setup that will configure HQLDB as the Spring Batch repo. The following properties file will configure the database with the Spring Batch schema:

> batch.properties

```
batch.jdbc.driver=org.hsqldb.jdbcDriver
batch.jdbc.url=jdbc:hsqldb:mem:testdb;sql.enforce_strict_size=true
batch.jdbc.user=sa
batch.jdbc.password=
batch.schema=
batch.schema.script=classpath:/org/springframework/batch/core/schema-hsqldb.sql
```

The following beans file will set up the job repository, configure component scan and import the jobs configuration which will be discussed later:

> beans.xml

```xml
 <?xml version="1.0" encoding="UTF-8"?>
 <beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:jdbc="http://www.springframework.org/schema/jdbc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd
		http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-3.0.xsd">

	 <context:property-placeholder location="classpath:batch.properties" />
	 <context:component-scan base-package="com.bitwiseor" />

	 <import resource="classpath:jobs.xml"/>
	
	 <jdbc:initialize-database>
		 <jdbc:script location="${batch.schema.script}" />
	 </jdbc:initialize-database>
 </beans>
```

The rest of the database setup happens in code:

> BatchConfig.groovy

```java
package com.bitwiseor.extensions.config

import javax.sql.DataSource;

import org.apache.commons.dbcp.BasicDataSource;
import org.springframework.batch.core.launch.support.SimpleJobLauncher;
import org.springframework.batch.core.repository.JobRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.PlatformTransactionManager;

@Configuration
class BatchConfig {
	@Value('${batch.jdbc.driver}')
	private def driverClassName

	@Value('${batch.jdbc.url}')
	private def driverUrl

	@Value('${batch.jdbc.user}')
	private def driverUsername

	@Value('${batch.jdbc.password}')
	private def driverPassword

	@Autowired
	@Qualifier("jobRepository")
	def jobRepository

	@Bean
	def dataSource() {
		BasicDataSource dataSource = new BasicDataSource()
		dataSource.driverClassName = driverClassName
		dataSource.url = driverUrl
		dataSource.username = driverUsername
		dataSource.password = driverPassword
		return dataSource
	}

	@Bean
	def jobLauncher() {
		SimpleJobLauncher jobLauncher = new SimpleJobLauncher()
		jobLauncher.jobRepository = jobRepository
		return jobLauncher
	}

	@Bean
	def transactionManager() {
		return new DataSourceTransactionManager(dataSource())
	}
}
```

## Logging Config

We also want logging to keep track of the work we've done. Using SLF4J and Logback is easy enough. All we need is the Logback configuration:

> logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
 <configuration>
     <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
         <encoder>
             <pattern>%5p %-25logger{25} %m %n </pattern>
         </encoder>
     </appender>

     <logger name="com.bitwiseor">
         <level value="INFO" />
     </logger>

     <logger name="org.springframework">
         <level value="ERROR" />
     </logger>

     <root>
         <level value="INFO" />
         <appender-ref ref="CONSOLE" />
     </root>
 </configuration>
```

## Job Configuration

Now for the interesting piece: job configuration. Spring Batch has an easy pattern that can be followed by most batch applications: read, process, write. The ItemReader interface has a single method to read an item, the ItemProcessor interface converts the read item into a writable item, and the ItemWriter interface simply writes a batch of items out to wherever it needs to. For the sample job to make sure everything is running correctly, we'll write an ItemReader to read files from a directory, and an ItemWriter that simply writes the item to the info log.

First, the ItemReader. The interface has a single method call that must return an object. Once there are no more objects to read, it returns null. DirectoryItemReader will get an injected directory, then create an iterator once the bean is instantiated. The simple read call will return a file in the directory until there are no files using the iterator. Easy enough.

> DirectoryItemReader.groovy

```java
package com.bitwiseor.extensions.batch

import org.springframework.batch.item.ItemReader
import org.springframework.beans.factory.InitializingBean
import org.springframework.beans.factory.annotation.Value
import org.springframework.stereotype.Component

@Component
class DirectoryItemReader implements ItemReader <File>, InitializingBean {
	@Value('D:/code/glextensions') // hard coded for simplicity
	File directory
	private Iterator <File> iterator;
	
	@Override
	public void afterPropertiesSet() throws Exception {
		assert directory.isDirectory()
		this.iterator = Arrays.asList(directory.listFiles()).iterator()
	}
	
	@Override
	File read() {
		return iterator.hasNext()? iterator.next() : null
	}
}
```

And then the ItemWriter. The LoggingItemWriter simply logs each item out as an info level log using SLF4J. Groovy makes this stupidly simple.

> LoggingItemWriter.groovy

```java
package com.bitwiseor.extensions.batch

import groovy.util.logging.Slf4j

import java.util.List;

import org.springframework.batch.item.ItemWriter
import org.springframework.stereotype.Component;

@Slf4j
@Component
public class LoggingItemWriter implements ItemWriter {
	@Override
	void write(List items) {
		items.each{ log.info it.toString() }
	}
}
```

Lastly, we need our job XML configuration. The job simply defines the reader and writer and executes in batches of 10.

> jobs.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
 <beans:beans xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://www.springframework.org/schema/batch"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
		http://www.springframework.org/schema/batch http://www.springframework.org/schema/batch/spring-batch-2.1.xsd">

	 <job-repository id="jobRepository" />
	
	 <job id="sampleJob">
		 <step id="readDir">
			 <tasklet>
				 <chunk reader="directoryItemReader" writer="loggingItemWriter" commit-interval="10"/>
			 </tasklet>
		 </step>
	 </job>
 </beans:beans>
```

## Build File

In order to build and run everything, we'll use gradle, a Groovy based build tool. The following build file will collect all our dependencies and setup a test script to run the sample job.

> build.gradle

```java
apply plugin:'groovy'
apply plugin:'eclipse'
apply plugin:'application'

repositories {
	mavenCentral()
}

def springFrameworkVersion = '3.0.6.RELEASE'
def springBatchVersion = '2.1.7.RELEASE'
def slf4jVersion = '1.6.4'
def logbackVersion = '1.0.3'

mainClassName = 'org.springframework.batch.core.launch.support.CommandLineJobRunner'
run {
    args = ['classpath:/beans.xml', 'sampleJob']
}

dependencies {
	compile gradleApi()
	groovy localGroovy()
	
	compile "org.springframework:spring-jdbc:${springFrameworkVersion}"
	compile "org.springframework:spring-context:${springFrameworkVersion}"
	compile "cglib:cglib-nodep:2.2"
	compile "org.springframework.batch:spring-batch-core:${springBatchVersion}"
	compile "commons-dbcp:commons-dbcp:1.2.2"
	compile "hsqldb:hsqldb:1.8.0.7"	
}

task wrapper(type: Wrapper) {
	gradleVersion = '1.0-rc-3'
}
```

Running this with `gradlew install run` will print out the files in our directory as INFO messages as expected. Now we can get to work creating the actual job that will convert extension specifications into wiki documents.
