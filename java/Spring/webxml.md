如果没有特殊指定的话，默认的`config_location`在`XmlWebApplicationContext`中指定的是`DEFAULT_CONFIG_LOCATION`:`/WEB-INF/applicationContext.xml`
如果指定的话则通过拼接来获得
```java
@Override  
protected String[] getDefaultConfigLocations() {  
    if (getNamespace() != null) {  
       return new String[] {DEFAULT_CONFIG_LOCATION_PREFIX + getNamespace() + DEFAULT_CONFIG_LOCATION_SUFFIX};  
    }  
    else {  
       return new String[] {DEFAULT_CONFIG_LOCATION};  
    }  
}
```
