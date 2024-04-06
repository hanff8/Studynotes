## 1. 获取数组中最大值最小值的下标
```java
int[] arr = new int[]{1,2,3,45,5,7};  
// 最大值下标
int maxIndex = IntStream.range(0, arr.length).reduce((i, j) -> arr[i] > arr[j] ? i : j).getAsInt();

//最小值下标
int minIndex = IntStream.range(0, arr.length).reduce((i, j) -> arr[i] < arr[j] ? i : j).getAsInt();
```