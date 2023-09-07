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

