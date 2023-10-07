

## 1. AOP
- Advice：
	- @Before
	- @After
	- @AfterReturning
	- @AfterThrowing
	- @Around
- JoinPoint
- Pointcut
- Advisor
- Aspect
- AOP Proxy
## 2. 注解参数


[@Pointcut()的execution、@annotation等参数说明_pointcut execution_Normal Developer的博客-CSDN博客](https://blog.csdn.net/java_green_hand0909/article/details/90238242)
- execution：用于匹配方法执行的连接点；
- within：用于匹配指定类型内的方法执行；
- this：用于匹配当前AOP代理对象类型的执行方法；注意是AOP代理对象的类型匹配，这样就可能包括引入接口也类型匹配；        
- target：用于匹配当前目标对象类型的执行方法；注意是目标对象的类型匹配，这样就不包括引入接口也类型匹配；
- args：用于匹配当前执行的方法传入的参数为指定类型的执行方法；
- @within：用于匹配所以持有指定注解类型内的方法；
- @target：用于匹配当前目标对象类型的执行方法，其中目标对象持有指定的注解；
- @args：用于匹配当前执行的方法传入的参数持有指定注解的执行；
- @annotation：用于匹配当前执行方法持有指定注解的方法；
- 任意公共方法的执行：execution(public * *(..))
- 任何一个以“set”开始的方法的执行：execution(* set*(..))
- AccountService 接口的任意方法的执行：execution(* com.xyz.service.AccountService.*(..))
- 定义在service包里的任意方法的执行： execution(* com.xyz.service.*.*(..))
- 定义在service包和所有子包里的任意类的任意方法的执行：execution(* com.xyz.service..*.*(..)) 


```java
execution(modifiers-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern)throws-pattern?) 
```

其中后面跟着“?”的是可选项

括号中各个pattern分别表示：

- 修饰符匹配（modifier-pattern?）
- 返回值匹配（ret-type-pattern）：   可以为*表示任何返回值, 全路径的类名等
- 类路径匹配（declaring-type-pattern?）
- 方法名匹配（name-pattern）：可以指定方法名 或者 *代表所有, set* 代表以set开头的所有方法
- 参数匹配（(param-pattern)）：可以指定具体的参数类型，**多个参数间用“,”隔开，各个参数也可以用"*" 来表示匹配任意类型的参数，".."表示零个或多个任意参数。**  
    如(String)表示匹配一个String参数的方法；(*,String) 表示匹配有两个参数的方法，第一个参数可以是任意类型，而第二个参数是String类型。
- 异常类型匹配（throws-pattern?）
    第一个*表示匹配任意的方法返回值， ..(两个点)表示零个或多个，第一个..表示service包及其子包,第二个*表示所有类, 第三个*表示所有方法，第二个..表示方法的任意参数个数
- 定义在pointcutexp包和所有子包里的JoinPointObjP2类的任意方法的执行：execution(* com.test.spring.aop.pointcutexp..JoinPointObjP2.*(..))")
- pointcutexp包里的任意类： within(com.test.spring.aop.pointcutexp.*)
- pointcutexp包和所有子包里的任意类：within(com.test.spring.aop.pointcutexp..*)
- 实现了Intf接口的所有类,如果Intf不是接口,限定Intf单个类：this(com.test.spring.aop.pointcutexp.Intf)  
    当一个实现了接口的类被AOP的时候,用getBean方法必须cast为接口类型,不能为该类的类型
- 带有@Transactional标注的所有类的任意方法： 
    - @within(org.springframework.transaction.annotation.Transactional)
    - @target(org.springframework.transaction.annotation.Transactional)
- 带有@Transactional标注的任意方法：@annotation(org.springframework.transaction.annotation.Transactional)  
    @within和@target针对类的注解,@annotation是针对方法的注解
- 参数带有@Transactional标注的方法：@args(org.springframework.transaction.annotation.Transactional)
- 参数为String类型(运行是决定)的方法： args(String)

### 2.1. @Around注解

环绕执行，在嗲用目标方法之前和之后都会执行一定的逻辑