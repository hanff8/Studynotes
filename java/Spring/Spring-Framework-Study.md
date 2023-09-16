---
link: https://www.notion.so/Spring-Framework-Study-5533f0d516e24be781d11883fd78efc8
notionID: 5533f0d5-16e2-4be7-81d1-1883fd78efc8
---
## 1. SetConfigLocations

- 创建环境对象ConfiguableEnvironment 
- 处理ClassPathXmlApplicationContext传入的字符串中的占位符
```java
public void setConfigLocations(@Nullable String... locations) {  
    if (locations != null) {  
       Assert.noNullElements(locations, "Config locations must not be null");  
       this.configLocations = new String[locations.length];  
       for (int i = 0; i < locations.length; i++) {  
       // trim 处理空格
          this.configLocations[i] = resolvePath(locations[i]).trim();  
       }  
    }  
    else {  
       this.configLocations = null;  
    }  
}
```

## 2. resolvepath

```java
protected String resolvePath(String path) {  
    return getEnvironment().resolveRequiredPlaceholders(path);  
}
```
### 2.1. getEnvironment()
返回一个StandardEnvironment(),
这个StandardEnvironment处理Java进程中系统的环境变量和系统的properties资源文件
```java
public class StandardEnvironment extends AbstractEnvironment {  
    public static final String SYSTEM_ENVIRONMENT_PROPERTY_SOURCE_NAME = "systemEnvironment";  
    public static final String SYSTEM_PROPERTIES_PROPERTY_SOURCE_NAME = "systemProperties";  
  
    public StandardEnvironment() {  
    }  
  
    protected void customizePropertySources(MutablePropertySources propertySources) {  
        propertySources.addLast(new MapPropertySource("systemProperties", this.getSystemProperties()));  
        propertySources.addLast(new SystemEnvironmentPropertySource("systemEnvironment", this.getSystemEnvironment()));  
    }  
}
```

- PropertyPlaceHolderHelper.parseStringValue() 处理 `${}`占位符
## 3. refresh()
```java
public void refresh() throws BeansException, IllegalStateException {  
    synchronized (this.startupShutdownMonitor) {  
       // Prepare this context for refreshing.  
       prepareRefresh();  
  
       // Tell the subclass to refresh the internal bean factory.  
       ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();  
  
       // Prepare the bean factory for use in this context.  
       prepareBeanFactory(beanFactory);  
  
       try {  
          // Allows post-processing of the bean factory in context subclasses.  
          postProcessBeanFactory(beanFactory);  
  
          // Invoke factory processors registered as beans in the context.  
          invokeBeanFactoryPostProcessors(beanFactory);  
  
          // Register bean processors that intercept bean creation.  
          registerBeanPostProcessors(beanFactory);  
  
          // Initialize message source for this context.  
          initMessageSource();  
  
          // Initialize event multicaster for this context.  
          initApplicationEventMulticaster();  
  
          // Initialize other special beans in specific context subclasses.  
          onRefresh();  
  
          // Check for listener beans and register them.  
          registerListeners();  
  
          // Instantiate all remaining (non-lazy-init) singletons.  
          finishBeanFactoryInitialization(beanFactory);  
  
          // Last step: publish corresponding event.  
          finishRefresh();  
       }  
  
       catch (BeansException ex) {  
          if (logger.isWarnEnabled()) {  
             logger.warn("Exception encountered during context initialization - " +  
                   "cancelling refresh attempt: " + ex);  
          }  
  
          // Destroy already created singletons to avoid dangling resources.  
          destroyBeans();  
  
          // Reset 'active' flag.  
          cancelRefresh(ex);  
  
          // Propagate exception to caller.  
          throw ex;  
       }  
  
       finally {  
          // Reset common introspection caches in Spring's core, since we  
          // might not ever need metadata for singleton beans anymore...          resetCommonCaches();  
       }  
    }  
}
```

### 3.1. `Synchronized()`
加锁，为了避免 `refresh()`还没结束，防止再次发起启动或者销毁容器从而引起冲突
### 3.2. `prepareRefresh()`
做一些准备工作，记录容器的启动时间、标记"已启动"状态、检查环境变量等
```java
protected void prepareRefresh() {  
    this.startupDate = System.currentTimeMillis();  
    this.closed.set(false);  
    this.active.set(true);  
  
    if (logger.isInfoEnabled()) {  
       logger.info("Refreshing " + this);  
    }  
  
    // Initialize any placeholder property sources in the context environment  
    // 初始化加载配置文件方法，并没有具体实现，一个留给用户的扩展点
    initPropertySources();  
  
    // Validate that all properties marked as required are resolvable  
    // see ConfigurablePropertyResolver#setRequiredProperties
    // 检查环境变量
    getEnvironment().validateRequiredProperties();  
  
    // Allow for the collection of early ApplicationEvents,  
    // to be published once the multicaster is available...        
    this.earlyApplicationEvents = new LinkedHashSet<>();  
}
```

检查变量的核心方法，为null则抛出异常，异常针对每个变量进行抛出
```java
public void validateRequiredProperties() {  
    MissingRequiredPropertiesException ex = new MissingRequiredPropertiesException();  
    for (String key : this.requiredProperties) {  
       if (this.getProperty(key) == null) {  
          ex.addMissingRequiredProperty(key);  
       }  
    }  
    if (!ex.getMissingRequiredProperties().isEmpty()) {  
       throw ex;  
    }  
}
```
### 3.3. `obtainFreshBeanFactory`
负责了BeanFactory的初始化、Bean的加载和注册
```java
	protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {  
	//核心
    refreshBeanFactory();

	//返回刚刚创建的 BeanFactory
    ConfigurableListableBeanFactory beanFactory = getBeanFactory();  
    if (logger.isDebugEnabled()) {  
       logger.debug("Bean factory for " + getDisplayName() + ": " + beanFactory);  
    }  
    return beanFactory;  
}
```
#### 3.3.1. `BeanFactory`
##### 3.3.1.1. `refreshBeanFactory`
```java
protected final void refreshBeanFactory() throws BeansException {  
    // 判断当前ApplicationContext是否存在BeanFactory,存在则销毁所有Bean,并且销毁BeanFactory
    // 一个应用可以存在多个BeanFactory,这里判断的是当前的ApplicationContext中是否存在BeanFactory
    if (hasBeanFactory()) {  
       destroyBeans();  
       closeBeanFactory();  
    }  
    try {  
    // 初始化DeafaultListableBeanFactory
       DefaultListableBeanFactory beanFactory = createBeanFactory();  
       beanFactory.setSerializationId(getId());

	// 设置BeanFactory的两个配置属性：是否允许Bean覆盖、是否允许循环引用
       customizeBeanFactory(beanFactory);
         
   // 加载Bean岛BeanFactory中
       loadBeanDefinitions(beanFactory);  
       synchronized (this.beanFactoryMonitor) {  
          this.beanFactory = beanFactory;  
       }  
    }  
    catch (IOException ex) {  
       throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);  
    }  
}
```


##### 3.3.1.2. `DefaultListableBeanFactory`
继承关系：
![image.png|500](https://s2.loli.net/2023/09/07/j7xeUrGqSXBCPtF.png)



##### 3.3.1.3. `BeanDefinition` 
是 `bean`的一种形式（里面包含了Bean指向的类，是否单例、是否懒加载、Bean的依赖关系等）。BeanFactory中就是保存的BeanDefinition。

```java
public interface BeanDefinition extends AttributeAccessor, BeanMetadataElement {

	// Bean的生命周期，默认sington和prototype两种，在webApplicationConext中在WebApplicationContext中还会有request, session, globalSession, application, websocket 等
	String SCOPE_SINGLETON = ConfigurableBeanFactory.SCOPE_SINGLETON;
	String SCOPE_PROTOTYPE = ConfigurableBeanFactory.SCOPE_PROTOTYPE;


	
	int ROLE_APPLICATION = 0;
	int ROLE_SUPPORT = 1;
	int ROLE_INFRASTRUCTURE = 2;


	// 父bean
	void setParentName(@Nullable String parentName);
	@Nullable
	String getParentName();


	// Bean的类名称
	void setBeanClassName(@Nullable String beanClassName);
	@Nullable
	String getBeanClassName();

	//Bean的Scope
	void setScope(@Nullable String scope);
	@Nullable
	String getScope();

	//懒加载
	void setLazyInit(boolean lazyInit);
	boolean isLazyInit();


	// 设置当前Bean依赖的2的所有Bean
	void setDependsOn(@Nullable String... dependsOn);
	@Nullable
	// 返回Bean的所有依赖
	String[] getDependsOn();

	// 设置该Bean是否可以注入到其他Bean中
	void setAutowireCandidate(boolean autowireCandidate);
	boolean isAutowireCandidate();


	void setPrimary(boolean primary);
	boolean isPrimary();


	void setFactoryBeanName(@Nullable String factoryBeanName);
	@Nullable
	String getFactoryBeanName();


	void setFactoryMethodName(@Nullable String factoryMethodName);


	@Nullable
	String getFactoryMethodName();

	// 构造器参数
	ConstructorArgumentValues getConstructorArgumentValues();


	default boolean hasConstructorArgumentValues() {
		return !getConstructorArgumentValues().isEmpty();
	}


	// Bean中的属性值
	MutablePropertyValues getPropertyValues();


	default boolean hasPropertyValues() {
		return !getPropertyValues().isEmpty();
	}



	boolean isSingleton();

	boolean isPrototype();


	// 如果这个 Bean 是被设置为 abstract，那么不能实例化，常用于作为 父bean 用于继承
	boolean isAbstract();

	int getRole();


	@Nullable
	String getDescription();


	@Nullable
	String getResourceDescription();

	@Nullable
	BeanDefinition getOriginatingBeanDefinition();

}

```


##### 3.3.1.4. `loadBeanDefinitions`
```java
protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {  
    // Create a new XmlBeanDefinitionReader for the given BeanFactory.  
    XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);  
  
    // Configure the bean definition reader with this context's  
    // resource loading environment.    beanDefinitionReader.setEnvironment(this.getEnvironment());  
    beanDefinitionReader.setResourceLoader(this);  
    beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));  
  
    // Allow a subclass to provide custom initialization of the reader,  
    // then proceed with actually loading the bean definitions.    initBeanDefinitionReader(beanDefinitionReader);  
    loadBeanDefinitions(beanDefinitionReader);  
}

protected void loadBeanDefinitions(XmlBeanDefinitionReader reader) throws BeansException, IOException {  
    Resource[] configResources = getConfigResources();  
    if (configResources != null) {  
       reader.loadBeanDefinitions(configResources);  
    }  
    String[] configLocations = getConfigLocations();  
    if (configLocations != null) {  
       reader.loadBeanDefinitions(configLocations);  
    }  
}
```


## 4. systemproperties 与 systemEnvironment有什么区别

`systemProperties` 和 `systemEnvironment` 是两个不同的概念，它们在计算机编程和操作系统中有不同的用途和含义。

1. **systemProperties**（系统属性）：
   - `systemProperties` 通常指的是程序运行时可以访问的系统级别的属性或配置信息。这些属性通常存储在系统属性表中，可以通过编程语言或框架的API来获取。
   - 这些属性包括诸如操作系统的版本、Java虚拟机的配置、程序的启动参数等信息。在Java中，你可以使用`System.getProperty()`方法来访问这些属性。
   - 例如，在Java中，你可以使用 `System.getProperty("os.name")` 来获取操作系统的名称。

2. **systemEnvironment**（系统环境）：
   - `systemEnvironment` 通常指的是操作系统级别的环境变量。这些变量包含了有关操作系统和其它系统级别信息的配置。
   - 环境变量通常用于存储一些全局性的配置，它们可以在不同的程序之间共享。在许多操作系统中，环境变量的值可以在命令行或脚本中设置和检索。
   - 例如，在Unix/Linux系统中，你可以使用`echo $PATH`来查看`PATH`环境变量的值，该变量定义了系统在哪些目录中查找可执行文件。

总结：`systemProperties` 主要是针对程序运行时的配置和属性，而 `systemEnvironment` 则是关于操作系统环境的全局配置。它们在不同的上下文中使用，但都是管理和控制计算机系统的重要工具。


## 5. 依赖注入

Bean创建的生命周期
	UserService.class-->无参的构造方法--->对象--->依赖注入--->初始化前--->初始化--->初始化后--->放入单例池--->Bean对象


推断构造方法
先 ByType 再ByName

Spring中并没有对代理对象进行`依赖注入`

CGLIB 基于父子类实现的

UserServiceProxy对象--->UserService代理对象

UserServiceProxy.test();

```java
class UserServiceProxy extends UserService{
	UserSevice target;
	
	public void test(){
		//切面逻辑@Before
		target.test
	}
}

// 事务管理也是通过aop实现的
class UserServiceProxy extends UserService{
	UserSevice target;
	
	public void test(){
		// Spring 事务切面逻辑
		// @Transactional
		// 开启事务
		// 1.用事务管理器建一个数据库连接Conn
		// 2.conn.autoCommit=false
		target.test(); //普通对象.test() jdbcTemplate sql1 sql2
		// conn.commit() conn.rollback()
	}
}

// Configuration
class UserServiceProxy extends UserService{
	UserSevice target;
	
	public void test(){
		// Spring 事务切面逻辑
		// @Transactional
		// 开启事务
		// 1.用事务管理器建一个数据库连接Conn ThreadLocal<Map<DataSource,Conn>>
		// 2.conn.autoCommit=false
		target.test(); //普通对象.test() jdbcTemplate sql1 sql2
		// conn.commit() conn.rollback()
	}
}
```

@Configurationn AOP @Lazy
均基于动态代理


```java

AppConfig代理对象.jdbcTemplate()
class AppConfigProxy extends Appconfig{
	UserSevice target;
	
	public void jdbcTemplate(){
		//代理对象
		super.jdbcTemplate()
	}
	public void dataSource(){
		// 代理对象
		super.jdbcTemplate()
	}
}
```

解决循环依赖

三级缓存

一级缓存：singletonObjects
二级缓存：earlySingletonObjects
三级缓存：singletonFactories


```java
1. createSet<'Aservice'>
2. 实例化-->Aservice普通对象-->singletonnFactories.put('Aservice',()->getEarlyBeanReference(beanName,mbd,Aservice普通对象))
3. 填充BService-->单例池Map-->创建BService
	1. BService的Bean生命周期
	2. 实例化-->普通对象
	3. 填充AService-->单例池Map-->createingSet-->Aservice出现了循环依赖-->earlySingletonObjects-->singletobFactories-->lambda-->执行lambda-->aop-->AService代理对象-->earLySingletonObjects
	4. 填充其他属性
	5. 做一些其他的事儿
4. 填充其他属性
5. earlySingletonObjects.get('AService')
6. 做一些其他的事儿
7. 添加到单例池
8. creatingSet.remove<'AService'>
```


@excludeFilter 排除ComponentScan指定包名下的某个包/某一类包，根据FilterType决定