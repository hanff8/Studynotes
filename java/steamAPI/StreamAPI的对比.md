
## 1. mapToInt 与 map
通过两个api的参数可以直观看出区别

	IntStream mapToInt(ToIntFunction<? super T> mapper);

	Stream map(<R> Stream<R> map(Function<? super T, ? extends R> mapper);)

mapToInt返回的是一个IntStream，通过toArray()可以直接转换成int[]数组

	int[] arr = target.stream().mapToInt(Integer::intValue).toArray();

map的返回流则需要自己定制，同样通过转换成**int[]** 数组的例子来说明，使用map的需要一步多余的拆箱操作

	int[] arr = target.stream().map(Integer::intValue).mapToInt(Integer::intValue).toArray();