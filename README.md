# Asf Base POM

## Rationale

A base pom codifies policy for projects. It allows a new Maven project
to quickly get off the ground and focus on the project itself and not 
how to build it. The Asf Base POM focus on building libraries 
and components that are distributed as jar files through the Maven 
central repository.

It is possible to use the base pom in a new project without any additional changes 
and modifications.

## Preliminaries

The Asf Base POM enforces Maven 3.0.4 or later. The reason for 
this is http://jira.codehaus.org/browse/MNG-5121 which has only been resolved in version 3.0.4.

By default, the Asf Base POM enforces JDK 1.6. To use another version, add
```xml
<properties>
    <project.build.targetJdk>1.7</project.build.targetJdk>
    ...
</properties>
```

### Local setup required!


To fully leverage the deployment options from the Asf OSS POM, a number of servers 
need to be configured in the local `~/.m2/settings.xml` file. If these servers are missing, 
either artifact or site deployment will fail.

As described on https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide, 
the `sonatype-nexus-staging` and `sonatype-nexus-snapshots` repositories should be configured:

```xml
<servers>
    ...
    <server>
        <id>sonatype-nexus-snapshots</id>
        <username>user</username>
        <password>password</password>
    </server>
    <server>
        <id>sonatype-nexus-staging</id>
        <username>user</username>
        <password>password</password>
    </server>
    ...
</servers>
```

To support releasing and tagging repositories that are hosted on Github and to allow site 
deployment to Github, entries for Github and the project site must exist:

```xml
<servers>
    ...
    <server>
        <id>github</id>
        <username>github-id</username>
        <password>github-password</password>
    </server>
    <server>
        <id>github-project-site</id>
        <username>git</username>
    </server>
    ...
</servers>
```

The hard-coded username `git` and no password for the github-project-site are a limitation of 
the deployment tool used for the github site. They must exist in the local settings file.

```xml
<parent>
    <groupId>net.my.asf</groupId>
    <artifactId>asf-base-pom</artifactId>
    <version> ... current pom release version ...</version>
</parent>
```

## Project pom structure

The following elements should be present in a pom using the Asf Base POM as parent:

* `groupId`, `artifactId`, `version`, `packaging`, `name`, `description` and `inceptionYear`

  Define the new project. These elements should always be present. If any of those fields are missing from the project,
the values from the Asf Base POM are picked up instead.

* `scm`

  Defines the SCM location and URL for the project. This is required to use the `release:prepare` and `release:perform` targets
  to deploy artifacts to Maven Central.

* `organization`, `developers`, `distributionManagement`

  Empty elements override the values inherited from the Asf Base. 

This is a sample skeleton pom using the Asf Base POM:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>net.my.asf</groupId>
        <artifactId>asf-base-pom</artifactId>
        <version> ... current version ...</version>
    </parent>

    <groupId> ... group id of the new project ... </groupId>
    <artifactId> ... artifact id of the new project ... </artifactId>
    <version> ... version of the new project ... </version>
    <packaging> ... jar|pom ... </packaging>
    <description> ... description of the new project ...  </description>
    <name>${project.artifactId}</name>
    <inceptionYear>2013</inceptionYear>
    
    <scm>
        <connection> ... scm read only connection ... </connection>
        <developerConnection>... scm read write connection ... </developerConnection>
        <url> ... project url ... </url>
    </scm>

    <distributionManagement/>
    <developers/>
    <organization/>
    ...
</project>
```

## Project POM conventions

## Deploy to Maven Central

## Project Build and Checkers

The Asf Base POM hooks various checkers into the build lifecycle and executes them on each build. 

Generally speaking, running a set of checks at each build is a good way to catch problems early 
and any problem reported by a checker should be treated as something that needs to be fixed 
before releasing.

Checkers are organized in two groups, basic and extended.


### Basic checkers
* Maven Enforcer                  (http://maven.apache.org/enforcer/maven-enforcer-plugin/)
* Maven Dependencies              (http://maven.apache.org/plugins/maven-dependency-plugin/)
* Maven Dependency version check  (https://github.com/ning/maven-dependency-versions-check-plugin)
* Maven Duplicate finder          (https://github.com/ning/maven-duplicate-finder-plugin)

### Extended checkers
* Findbugs                        (http://mojo.codehaus.org/findbugs-maven-plugin/)
* PMD                             (http://maven.apache.org/plugins/maven-pmd-plugin/)
* License check                   (http://code.google.com/p/maven-license-plugin/)
* Code coverage                   (http://www.eclemma.org/jacoco/trunk/doc/maven.html)


All checkers are enabled by default and all checkers *will fail the build* if a problem is encountered. 

## Well known dependencies

The Asf Base POM provides a number of dependencies to projects. These dependencies are 
considered "well known and stable". When a project wants to use any of these dependencies, it 
can declare them in the project `<dependencies>` section without a version and automatically 
pick up a version from the Asf Base POM.

The following dependencies are defined:

<table>
    <tr><th>Dependency name</th><th>Group/Artifact Ids</th><th>property</th></tr>
    <tr>
       <td>Google Guice</td>
       <td><tt>com.google.inject:guice</tt><p/><tt>com.google.inject.extensions:guice-servlet</tt><p/><tt>com.google.inject.extensions:guice-assistedinject</tt><p/><tt>com.google.inject.extensions:guice-multibindings</tt><p/><tt>com.google.inject.extensions:guice-throwingproviders</tt></td>
       <td><tt>dep.guice.version</tt></td>
    </tr>
    <tr>
       <td>Google Guava</td>
       <td><tt>com.google.guava:guava</tt></td>
       <td><tt>dep.guava.version</tt></td>
    </tr>
    <tr>
        <td>Joda Time</td>
        <td><tt>joda-time:joda-time</tt></td>
        <td><tt>dep.joda.version</tt></td>
    </tr>
    <tr>
        <td>Apache Commons Lang 3</td>
        <td><tt>org.apache.commons:commons-lang3</tt></td>
        <td><tt>dep.commons-lang3.version</tt></td>
    </tr>
    <tr>
        <td>Apache Commons Lang</td>
        <td><tt>commons-lang:commons-lang</tt></td>
        <td><tt>dep.commons-lang.version</tt></td>
    </tr>
    <tr>
        <td>Apache Commons Configuratio</td>
        <td><tt>commons-configuration:commons-configuration</tt></td>
        <td><tt>dep.commons-configuration.version</tt></td>
    </tr>
    <tr>
        <td>Apache Commons Codec</td>
        <td><tt>commons-codec:commons-codec</tt></td>
        <td><tt>dep.commons-codec.version</tt></td>
    </tr>
    <tr>
        <td>Apache Commons Collections</td>
        <td><tt>commons-collections:commons-collections</tt></td>
        <td><tt>dep.commons-collections.version</tt></td>
    </tr>
    <tr>
        <td>Apache Commons IO</td>
        <td><tt>commons-io:commons-io</tt></td>
        <td><tt>dep.commons-io.version</tt></td>
    </tr>
    <tr>
        <td>Apache Commons Beanutils</td>
        <td><tt>commons-beanutils:commons-beanutils</tt></td>
        <td><tt>dep.commons-beanutils.version</tt></td>
    </tr>
    <tr>
        <td>Java Inject API</td>
        <td><tt>javax.inject:javax.inject</tt></td>
        <td><tt>dep.javax-inject.version</tt></td>
    </tr>
    <tr>
        <td>Java Servlet API</td>
        <td><tt>javax.servlet:javax.servlet-api</tt></td>
        <td><tt>dep.javax-servlet.version</tt></td>
    </tr>
    <tr>
        <td>slf4j (Simple Logging Facade for Java)</td>
        <td><tt>org.slf4j:slf4j-api</tt><p/><tt>org.slf4j:slf4j-jcl</tt><p/><tt>org.slf4j:slf4j-jdk14</tt><p/><tt>org.slf4j:slf4j-log4j12</tt><p/><tt>org.slf4j:slf4j-nop</tt><p/><tt>org.slf4j:slf4j-simple</tt><p/><tt>org.slf4j:slf4j-ext</tt><p/><tt>org.slf4j:jcl-over-slf4j</tt><p/><tt>org.slf4j:jul-to-slf4j</tt><p/><tt>org.slf4j:log4j-over-slf4j</tt></td>
        <td><tt>dep.slf4j.version</tt></td>
    </tr>
    <tr>
        <td>Logback</td>
        <td><tt>ch.qos.logback:logback-core</tt><p/><tt>ch.qos.logback:logback-classic</tt></td>
        <td><tt>dep.logback.version</tt></td>
    </tr>
    <tr>
        <td>log4j</td>
        <td><tt>log4j:log4j</tt></td>
        <td><tt>dep.log4j.version</tt></td>
    </tr>
    <tr>
        <td>Findbugs Annotations</td>
        <td><tt>com.google.code.findbugs:annotations</tt></td>
        <td><tt>dep.findbugs-annotations.version</tt></td>
    </tr>
    <tr>
        <td>JUnit testing</td>
        <td><tt>junit:junit-dep</tt></td>
        <td><tt>dep.junit.version</tt></td>
    </tr>
    <tr>
        <td>TestNG testing</td>
        <td><tt>org.testng:testng</tt></td>
        <td><tt>dep.testng.version</tt></td>
    </tr>
    <tr>
        <td>Easymock Mocking framework</td>
        <td><tt>org.easymock:easymock</tt></td>
        <td><tt>dep.easymock.version</tt></td>
    </tr>
    <tr>
        <td>Hamcrest matchers</td>
        <td><tt>org.hamcrest:hamcrest-core</tt><p/><tt>org.hamcrest:hamcrest-library</tt></td>
        <td><tt>dep.hamcrest.version</tt></td>
    </tr>
    <tr>
        <td>Objenesis</td>
        <td><tt>org.objenesis:objenesis</tt></td>
        <td><tt>dep.objenesis.version</tt></td>
    </tr>
    <tr>
        <td>Unitils</td>
        <td><tt>org.unitils:unitils</tt></td>
        <td><tt>dep.unitils.version</tt></td>
    </tr>
    <tr>
        <td>Spring</td>
        <td><tt>org.springframework:spring-core</tt><p/><tt>org.springframework:spring-expression</tt><p/><tt>org.springframework:spring-aop</tt><p/><tt>org.springframework:spring-context</tt><p/><tt>org.springframework:spring-context-support</tt><p/><tt>org.springframework:spring-tx</tt><p/><tt>org.springframework:spring-jdbc</tt><p/><tt>org.springframework:spring-orm</tt><p/><tt>org.springframework:spring-oxm</tt><p/><tt>org.springframework:spring-web</tt><p/><tt>org.springframework:spring-webmvc</tt></td>
        <td><tt>dep.spring.version</tt></td>
    </tr>
    <tr>
        <td>Dubbo</td>
        <td><tt>com.alibaba:dubbo</tt></td>
        <td><tt>dep.dubbo.version</tt></td>
    </tr>
    <tr>
        <td>Thrift</td>
        <td><tt>org.apache.thrift:libthrift</tt></td>
        <td><tt>dep.thrift.version</tt></td>
    </tr>
    <tr>
        <td>Nifty</td>
        <td><tt>com.facebook.nifty:nifty-core</tt><p/><tt>com.facebook.nifty:nifty-client</tt></td>
        <td><tt>dep.nifty.version</tt></td>
    </tr>
    <tr>
        <td>Zookeeper</td>
        <td><tt>org.apache.zookeeper:zookeeper</tt></td>
        <td><tt>dep.zookeeper.version</tt></td>
    </tr>
    <tr>
        <td>Zkclient</td>
        <td><tt>com.github.sgroschupf:zkclient</tt></td>
        <td><tt>dep.zkclient.version</tt></td>
    </tr>
    <tr>
        <td>Fastjson</td>
        <td><tt>com.alibaba:fastjson</tt></td>
        <td><tt>dep.fastjson.version</tt></td>
    </tr>
    <tr>
        <td>Jodd</td>
        <td><tt>org.jodd:jodd-core</tt><p/><tt>org.jodd:jodd-mail</tt></td>
        <td><tt>dep.jodd.version</tt></td>
    </tr>
    <tr>
        <td>Velocity</td>
        <td><tt>org.apache.velocity:velocity</tt></td>
        <td><tt>dep.velocity.version</tt></td>
    </tr>
    <tr>
        <td>Bouncycastle</td>
        <td><tt>bouncycastle:bcprov-jdk15</tt><p/><tt>bouncycastle:bcpg-jdk15</tt><p/><tt>bouncycastle:bcmail-jdk15</tt></td>
        <td><tt>dep.bouncycastle.version</tt></td>
    </tr>
    <tr>
        <td>Zip4j</td>
        <td><tt>net.lingala.zip4j:zip4j</tt></td>
        <td><tt>dep.zip4j.version</tt></td>
    </tr>
    <tr>
        <td>Store4j</td>
        <td><tt>com.taobao.store4j:store4j</tt></td>
        <td><tt>dep.store4j.version</tt></td>
    </tr>
</table>
