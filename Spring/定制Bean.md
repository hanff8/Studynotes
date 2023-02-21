1. 有些时候，我们会有一系列接口相同，不同实现类的Bean，这时候我们可以分别对各个implements 该接口的类添加**@Component** 注解，然后再使用一个入口（比如说一个List来存储这些类，因为implements的是同一个接口，所以会被Spring自动注入List中）
2. 接上. 如果对注入的Bean的顺序有要求，可以使用Order注解