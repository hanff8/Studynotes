## 一般写法
```java
String regex = "xxx";
String target = "yyy";
Pattern pattern = Pattern.compile();
Matcher matcher = pattern.matcher(targer);
```

## 全匹配与子串匹配

	maches() 
全字符串匹配，也就是说，即使正则匹配到了target字符串的某一段字符，也会返回false

	find()
支持子串匹配，即会将匹配到的字符输出出来，存进group数组中
```java
int i=0;
while(matcher.find()){
	输出匹配到的内容
	Systerm.out.println(matcher.group(i++));
	// 匹配到了即输出整个串
	Systerm.out.println(src);
}
```