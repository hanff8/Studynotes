## 1. **String ...** 与 **String[]** 的区别
String ... 是Java5 开始对方法参数的一种新写法，叫可变长度参数。
假设现在有两个方法分别是
	test()与test(String...)
```java
public class Test003 {  

    private Test003(){  
        test();  
        test("a","b");
        test(new String[]{"aaa","bbb"});
        test("ccc");  
    }  

    private void test(){  
        System.out.println("test");   
    }  

    private void test(String...strings){  
        for(String str:strings){  
            System.out.print(str + ", ");  
        }  
        System.out.println();  
    }  
    public static void main(String[] args) {  
        new Test003();  
    }  

}  
```
假设我们没有参数去调用test(String...)，即test()，
我们在调用test()的时候会优先调用test()方法。只有当没有test()函数的时候，我们调，程序才会走test(String ...)
