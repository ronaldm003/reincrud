# Download #

ReinCRUD is available from two repositories. You can add one of them to your pom.xml and then add the following dependency:

```
<dependency>
  <groupId>org.acoveo</groupId>
  <artifactId>reincrud</artifactId>
  <version>1.1</version>
</dependency>
```

From the [vaadin](http://www.vaadin.com) [addon site](http://vaadin.com/directory#addon/reincrud) repository:

```
<repository>
   <id>vaadin-addons</id>
   <url>http://maven.vaadin.com/vaadin-addons</url>
</repository>
```

Or our corporate maven repository:

```
<repository>
   <id>acoveo</id>
   <url>http://www.acoveo.com/maven2/repo</url>
</repository>
```


# New project #
If you would like to start a new project based on reinCRUD, you can use our maven archetype to create a new project named _reincrudproject_ in your current directory:

```
mvn archetype:generate -DarchetypeCatalog=http://acoveo.com/maven2/repo/archetype-catalog.xml -DgroupId=org.acoveo -DartifactId=reincrudproject -Dversion=1.0-SNAPSHOT
```

The example project you have just created assumes that there is a mysql database named _vaadintest_ on your localhost. You have to create the DB before running the project (change the mysql user (root) according to your environment and/or remove the _-p_ option if you do not have a password set):

```
sudo mysqladmin create vaadintest
echo "grant all privileges on vaadintest.* to 'vaadintest'@'localhost' Identified by 'vaadintest';" | mysql -u root -p
```

Now your project is set-up and you can compile and run a Tomcat instance with your new project using maven:

```
cd reincrudproject
mvn install cargo:start
```

If Tomcat came up fine, you can now visit http://localhost:8080/reincrudproject/vaadintest and should see your reinCRUD application.

# Eclipse #
If you set-up your project using our archetype, you can easily generate an eclipse project with;
```
mvn eclipse:eclipse
```
and import it using _Existing projects into workspace_.

Have a look at the [Compiling](Compiling.md) page if you would like to use a Tomcat instance managed by eclipse.

# Custom widgets #
In order to use the pre-compiled widgetset in reincrud (with custom widgets) you have to extract the _VAADIN_ directory from the jar to _src/main/webapp/_. On Linux just run the following command from within your project directory:
```
unzip .m2/repository/org/acoveo/reincrud/1.1/reincrud-1.1.jar VAADIN* -d src/main/webapp/
```

In order to use the widgetset, you have to modify your src/main/webapp/WEB-INF/web.xml and set _org.acoveo.reincrud.gwt.widgetset.ReinCrudWidgetset_ as the widgetset param-value of the vaadintest servlet. Then set _org.acoveo.reincrud.customGwtWidgetsEnabled=true_ in src/main/resources/config.properties and re-deploy to your Tomcat.

You can also use your own widgetset and inherit from _org.acoveo.reincrud.gwt.widgetset.ReinCrudWidgetset_. In that case, you need to use the GWT compiler, though. If you created your project using our archetype, there is an example _VaadintestAppWidgetSet.gwt.xml_ widgetset in your project and you can enable the GWT compiler, by setting _activeByDefault_ to true within the _compile-widgetset_ profile of the pom.xml.