```c++
//KMP
int findByKMP(vector<int>& s,int k,vector<int>& p) {
	int m=p.size(),n=s.size();
	if(k+p.size()>s.size()){
		return -1;
	}
	vector<int> next(m);
	//构建next数组
	for (int i=1,j=0; i<m; i++) {
		while (j>0&&p[i]!=p[j]) {
			j=next[j-1];
		}
		if (p[i]==p[j]) {
			j++;
		}
		next[i] = j;
	}
	//利用数组开始匹配
	for (int i=k,j=0; i<n; i++) {
		while(j>0&&s[i]!=p[j]){
			j = next[j-1];
		}
		if(s[i]==p[j]){
			j++;
		}
		if(j==m){
			return i-m+1;
		}
	}
	return -1;
}
/*
 *来自leetcode：https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/solution/shi-xian-strstr-by-leetcode-solution-ds6y/
 *
 */
```