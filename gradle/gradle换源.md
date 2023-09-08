```groovy
// 阿里云
buildscript{
	repository{
		maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/' } 
		maven{ url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'}  
		maven{ url 'https://maven.aliyun.com/repository/gradle-plugin'}  
		maven{ url 'https://maven.aliyun.com/repository/spring-plugin'}  
		maven{ url 'https://maven.aliyun.com/repository/central'}  
		maven{ url 'https://maven.aliyun.com/repository/spring'}  
		maven{ url 'https://maven.aliyun.com/repository/google'}
	}
}

```