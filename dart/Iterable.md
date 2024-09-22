## 1. firstWhere

查找第一个满足条件的元素。

找不到则返回 StateError

## 2. singleWhere

查找集合中唯一一个满足条件的元素

`该方法会一直迭代到最后一个元素，因此如果 Iterable 存在很多的元素，或者长度无上限，可能会产生位置问题`

找不到则返回 StateError

```dart
bool predicate(String item){
	return item.length>5;
}

void main{
	const items = ['Salad','Popcorn','Toast','Lasagne']
	var foundItem1 = items.firstWhere((item)=>item.length>5);
	print(foundItem1); // Popcorn

	var foundItem2 = items.firstWhere((item){
		return item.length>5;
	});
	print(foundItem2);//Popcorn

	var foundItem3 = items.firstWhere(predicate);
	print(foundItem3);//Popcorn

	var foundItem4 = items.firstWhere((item)=>item.length>10,orElse:()=>'No
	ne!',);
	print(foundItem4);
}

```

## 3. any()、every()