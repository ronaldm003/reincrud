# Introduction #

This page explains how the [example application](http://www.acoveo.com/reincrudexample/vaadintest) based on ReinCRUD works.


# Details #
The ReinCRUD example does not contain a single line of business logic. The only code required are the definitions of the entity classes. The rest is just a bit of Spring configuration (in our case using Spring 3.0 [Java Config](http://static.springsource.org/spring/docs/3.0.x/reference/beans.html#beans-java)).

## The Person entity ##
ReinCRUD uses convention over configuration and chooses sane defaults for your entities. Thus the most simple definition does not require any ReinCRUD specific annotations at all. As that would be a bit boring, the reincrudexample app contains a real-life example.

Here is the Person class:
```
@Entity
@UiName(name="Person.uiName")
@UiForm(fieldModifier=PersonFieldModifier.class)
public class Person {
	@Id @GeneratedValue(strategy = GenerationType.IDENTITY)
	@UiProperty(globallyHidden=true)
	protected long id;
	
	@Enumerated(EnumType.STRING)
	@UiProperty(triggersFieldModifier=true)
	protected PersonType type = PersonType.PHYSICAL;
	
	protected String reference;
	
	@Enumerated(EnumType.STRING)
	@Column(nullable=false)
	protected PersonState state = PersonState.DRAFT;

	@UiProperty(inList=true, order=100, uiToString = {
		@UiToString(order = 100)
	})
	@Size(max=15)
	protected String title;

	@Size(max=15)
	protected String honTitle;
	
	@Size(max=15)
	protected String adminTitle;
	
	@UiProperty(inList=true, order=80, uiToString = {
		@UiToString(order = 80, before = " ")
	})
	protected String surname;

	@UiProperty(inList = true, order = 90, uiToString = {
		@UiToString(order = 90, before = " ")
	})
	@UiAutocomplete(entity = Forenames.class, sourceAttribute = Forenames.P_NAME, 
		copyAttributes = { 
			@Copy(sourceAttribute = Forenames.P_SEX, targetAttribute = Person.P_SEX) 
		}
	)
	protected String name;
	
	@UiProperty(inList=true)
	@Enumerated(EnumType.STRING)
	protected Sex sex;
	
	protected String companyName1;
	
	protected String companyName2;
	
	protected Date birthDate;
	
	@UiProperty(inList=true)
	protected String socialSecNumber;
	
	protected String foreignSocialSecNumber;
	
	protected String letterOpening;
	
	protected String letterOpening2;

	@OneToMany(mappedBy="owner")
	protected Set<Job> job;

	... accessor methods ...
}
```

Let's go from top to bottom. The first annotation you see is **@UiName(name="Person.uiName")**. The UiName annotation is used to define the string which is used in the user interface to name you entity or property. It can be used with internationalisation (I18N lookup) or without. If I18N is enabled the _name_ and _description_ will be interpreted as keys for a message lookup using a **MessageSource**. Using I18N is the default and you can set _noi18n=false_ to disable it. If I18N lookup is disable the strings you set are directly used as captions and descriptions in the user interface.

The **@UiForm** annotation is used to customise the behaviour of the ReinCRUD Form component for this entity. In this case we use it to assign an _IFieldModifier_ implementation by setting _fieldModifier=PersonFieldModifier.class_. An _IFieldModifier_ is triggered whenever the property data source of a Form, or the value of an attribute marked with **@UiProperty(triggersFieldModifier=true)** changes. Field modifiers are useful to show/hide parts of a Form depending on the value of specific properties. In the example it is used to hide attributes depending on the type of the person entity (physical or legal).

The next annotation is **@UiProperty(globallyHidden=true)** and it is placed on the **@Id** annotated attribute. The UiProperty annotation is used to customise the behaviour of UI components which are used to edit the respective attribute. In this case we set _globallyHidden=true_ which causes the attribute to be ignored in all ReinCRUD components. Moving to the next annotation, you can see that we set _triggersFieldModifier=true_. As a result, the _IFieldModifier_ which is attached to the Person class using the **@UiForm** annotation is triggered whenever the value of the _type_ attribute changes.

The title attribute is annotated with **@UiProperty(inList=true, order=100, uiToString = {@UiToString(order = 100)})**. This showcases a few more important attributes of the _UiProperty_ annotation. The _inList_ attribute defines whether the annotated attribute is displayed in a column in the ReinCRUD Form component. It is set to _false_ by default, as you we assumed that you would like to show only a very few column for each entity. The reason for that is that you should create database **table indices** for those columns, as the List component allows the user to sort by the displayed columns. The _order_ attribute defines the ordering of the attribute in the List and Form components. The higher this number, the further up/left the attribute is shown. The _uiToString_ attribute is discussed below.

The **@UiToString(order = 80, before = " ")** which is placed within the _UiProperty.uiToString_ array (in this case on the _name_ attribute) can be used to define the way this entity is displayed when converted to a string. We do not replace the toString() method of your entity, but rather use a helper class. The annotation can be used to specify the _order_ of the attributes used to define the string representation and you can define a String to be placed _before_ and _after_ the attribute. The _before_ and _after_ strings are not used if the attribute is null.

The second annotation used on the _name_ attribute is
```
@UiAutocomplete(entity = Forenames.class, sourceAttribute = Forenames.P_NAME, 
		copyAttributes = { 
			@Copy(sourceAttribute = Forenames.P_SEX, targetAttribute = Person.P_SEX) 
		}
	)
```
The _UiAutocomplete_ annotation is used to define that the user should be presented with suggested values for this attribute. In order to use this feature, you have to enable the use of the custom ReinCRUD client side GWT widgets, by setting **org.acoveo.reincrud.customGwtWidgetsEnabled=true** in a _config.properties_ file on your classpath and you have to use a custom widgetset, which inherits from **org.acoveo.reincrud.gwt.widgetset.ReinCrudWidgetset**. The _UiAutocomplete_ annotation defines which _entity_ class is used to query the database for suggested values and the _sourceAttribute_ from the _entity_ class which is used to set the value of the annotated attribute. In addition to that the _copyAttributes_ array can be used to specify an unlimited number of additional attributes to copy from the _entity_ class.

The rest of the annotations should now all be familiar if you read the paragraphs above.

Unfortunately ReinCRUD currently does not support @OneToMany, @ManyToMany and @ManyToAny associations. It does however, support @ManyToOne associations, so you can choose the associated entity on the other side of a @OneToMany relationship. In our case this means that you can edit the Job entity and choose the associated Person there.

## Other entities ##
The other entity classes Job and Forenames do not bring anything new to the table.

## Spring configuration ##
ReinCRUD needs just a bit of Spring configuration to work. In our example this boils down to:
```
@Configuration
@Import(value= {
		org.acoveo.reincrud.SpringConfiguration.class,
		SpringConfigurationEnv.class
})
public class SpringConfiguration {
	@Autowired ApplicationContext appContext;
	
	@Bean
	@Scope(value = "request")
	public IEntityService service() {
		return new ServiceImpl();
	}
	
	@Bean
	public MessageSource messageSource() {
		ResourceBundleMessageSource msgSource = new ResourceBundleMessageSource();
		msgSource.setBasename("org.acoveo.dao.messages");
		return msgSource;
	}
	
	@Bean
	@ReinCrudFactoryQualifier
	public IReinCrudFactory<Person> reinCrudFactoryPerson() {
		return new ReinCrudFactory<Person>(Person.class, null);
	}
	
	@Bean
	@ReinCrudFactoryQualifier
	public IReinCrudFactory<Forenames> reinCrudFactoryForenames() {
		return new ReinCrudFactory<Forenames>(Forenames.class, null);
	}
	
	@Bean
	@ReinCrudFactoryQualifier
	public IReinCrudFactory<Job> reinCrudFactoryJob() {
		return new ReinCrudFactory<Job>(Job.class, null);
	}
	
	@Bean
	@Scope(value = "prototype")
	public PersonFieldModifier personFieldModifier(Class<?> entityType) {
		return new PersonFieldModifier();
	}
}

@Configuration
public class SpringConfigurationEnv {
	@Bean
	@Scope(value="request")
	public HibernateTransactionManager transactionManager() {
		HibernateTransactionManager tManager = new HibernateTransactionManager();
		tManager.setSessionFactory(sessionFactory().getObject());
		return tManager;
	}
	
	@Bean
	public AnnotationSessionFactoryBean sessionFactory() {
		AnnotationSessionFactoryBean sf = new AnnotationSessionFactoryBean();
		sf.setDataSource(myJndiDataSource());
		Class<?>[] classes = {
			Person.class,
			Tag.class,
			Job.class,
			Forenames.class,
		};
		sf.setAnnotatedClasses(classes);
		Properties props = new Properties();
		props.setProperty("hibernate.dialect", "org.hibernate.dialect.MySQLDialect");
		props.setProperty("hibernate.show_sql", "true");
		// Enable to upgrade schema
		props.setProperty("hibernate.hbm2ddl.auto", "update");
		sf.setHibernateProperties(props);
		return sf;
	}
	
	@Bean
	public DataSource myJndiDataSource() {
		try {
			InitialContext ic = new InitialContext();
			return (DataSource)ic.lookup("java:comp/env/jdbc/servlettestDB");
		}catch (NamingException e) {
			e.printStackTrace();
		}
		return null;
	}
	

}
```

Most importantly ReinCRUD needs one configured bean per entity class. This bean has to qualified with the **@ReinCrudFactoryQualifier**. In addition to that, a bean implementing **IEntityService** (a default implementation is provided), a **MessageSource** bean for I18N and the standard Spring hibernate setup, consisting of a **SessionFactory** and a **TransactionManager** is all that is required.