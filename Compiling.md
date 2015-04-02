# Introduction #

reinCRUD is built using maven2, so all you need is a [maven2](http://maven.apache.org/) installation.


# Compiling #

There are currently two projects:
  * reincrud
  * reincrudexample

If you would like to hack on reinCRUD, you should build both projects. Do the following:

```
mkdir workspace
cd workspace
svn co https://acoveo.com/svnroot/business/public/trunk/reincrud
svn co https://acoveo.com/svnroot/business/public/trunk/reincrudexample
cd reincrud
mvn eclipse:eclipse install
cd ../reincrudexample
mvn eclipse:eclipse install
```

This will take a while, as it runs the GWT compiler for each project.

# Database #
You have to create a matching mysql database before you deploy the application on a Tomcat server:

You can setup a matching mysql database as follows:
```
sudo mysqladmin create vaadintest
sudo mysql
> grant all privileges on vaadintest.* to 'vaadintest'@'localhost' Identified by 'vaadintest';
```

# Tomcat #
The reincrudexample project can be immediately deployed on a standalone Tomcat using the [cargo](http://cargo.codehaus.org) maven plugin. Just unzip a fresh Tomcat 6.0.29 installation to:
_/tmp/apache-tomcat-6.0.29_

and run:
```
cd reincrudexample
mvn cargo:start
```

Cargo will create a fresh tomcat HOME directory in _target/tomcat6_, so _/tmp/apache-tomcat-6.0.29_ will not be modified.

# Eclipse setup #

Now fire-up an eclipse installation with the [WTP plugin](http://www.eclipse.org/webtools/) installed, specify the workspace directory you have just created as the workspace and import the two projects (choose _Existing projects into workspace_).

In order to be able to run/debug the web application, define a new tomcat 6 instance, copy the [mysql-connector-java-5.1.13-bin.jar](http://mirrors.ibiblio.org/pub/mirrors/maven2/mysql/mysql-connector-java/5.1.13/mysql-connector-java-5.1.13.jar) into the _lib_ directory of your tomcat installation and have a look at _reincrudexample/src/main/webapp/META-INF/context.xml_ which explains how to setup your JNDI datasource. Basically you have to add:

```
<Resource auth="Container" driverClassName="com.mysql.jdbc.Driver" logAbandoned="true" 
			maxActive="100" maxIdle="30" maxWait="3000" name="jdbc/servlettestDB" 
			password="SECRETPASSWORD" removeAbandoned="true" testOnBorrow="true" 
			type="javax.sql.DataSource" url="jdbc:mysql://localhost/vaadintest?enableQueryTimeouts=false" 
			username="vaadintest" validationQuery="SELECT 1"/>
```

within the _GlobalNamingResources_ tag of server.xml.

Remember that the server.xml is in the _Servers_ project within the eclipse workspace!