## 1. 显式 Intent

```java
Intent intent = new Intent(MainActivity.this,navigateToActivity.class);

startActivity(intent);
```
## 2. 隐式 Intent

通过内置的category，或者约定的 category 来调用外部应用能力