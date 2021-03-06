---
title: DFS---POJ2488 A Knignt's Journey
key: 20160919
tags: 算法
---

[poj骑士问题](http://poj.org/problem?id=2488)

![棋盘](http://poj.org/images/2488_1.jpg)


<!--more-->


在p*q个单元大小的棋盘上，骑士从(0,0)出发，一次移动分为先向一个方向移动两步，再向另一个方向移动一步，就像是棋盘上马的 "日" 字型走向。
判断骑士能否在给定棋盘上不重复地访问所有单元格。

这就是个简单的**深搜**问题，移动选择一共有8个方向，需要注意的地方是题中**lexicographically**这个词，是字典序的意思，注意了这个地方，才能AC了这道题。

>**字典序**：是一种对于随机变量形成序列的排序方法。其方法是，按照字母顺序，或者数字小大顺序，由小到大的形成序列。
比如说有一个随机变量X包含{1 2 3}三个数值。
其字典排序就是{} {1} {1 2} {1 2 3} {2} {2 3} {3}

所以题中的八个方向可以表示为

{-2，-1}, {-2,+1}, {-1,-2}, {-1,+2}, {+1,-2}, {+1,+2}, {+2,-1}, {+2,+1}，

这样第一次遍历得到的路径就是按照字典序排列的了。

代码如下：

    #include<iostream>
    #include<cstring>
    using namespace std;
    #define m 30
    
    int p, q, sum = 0, flag = 0;
    int visited[m][m];
    int x[m], y[m];
    void dfs(int i, int j);
    
    int judge(int i, int j)
    {
        //注意这个地方的限制条件，尤其是最后的flag != 1
    	if(i >= 0 && i < q && j >= 0 && j < p && !visited[i][j] && !flag)
    	    return true;
    	return false;
    }
    void loop(int i, int j)
    {
    	visited[i][j] = 1;
    	sum++;
    	dfs(i, j);
    	sum--;
    	visited[i][j] = 0;
    }
    void dfs(int i, int j)
    {
    	x[sum] = i;
    	y[sum] = j;
    
    	if(sum + 1 == p*q){
    	    flag = 1;
            return;
    	}
    	// 8 directions
    	if(judge(i - 2, j - 1))	loop(i - 2, j - 1);
    	if(judge(i - 2, j + 1))	loop(i - 2, j + 1);
    	if(judge(i - 1, j - 2))	loop(i - 1, j - 2);
    	if(judge(i - 1, j + 2))	loop(i - 1, j + 2);
    	if(judge(i + 1, j - 2))	loop(i + 1, j - 2);
    	if(judge(i + 1, j + 2))	loop(i + 1, j + 2);
    	if(judge(i + 2, j - 1))	loop(i + 2, j - 1);
    	if(judge(i + 2, j + 1))	loop(i + 2, j + 1);
    }
    
    int main()
    {
    	int n;
    	cin &gt;&gt; n;
    	int t = 0;
    	while(n--){
    		cin >> n;
    		memset(visited,0,sizeof(visited)); 
    		visited[0][0] = 1;
    		flag = 0;
    		sum = 0;
    		dfs(0, 0);
    		cout << "Scenario #" << ++t << ":" << endl; 
    		if(!flag)
    			cout << "impossible";
    		else{
    		    for(int i = 0; i < p*q; i++) 
    			 	cout << char(x[i] + 'A') << y[i] + 1; 
    		}
    		cout << endl << endl;
    	}
    	return 0;
    }

最后需要注意的是poj提交的严格性，定义为int等类型的函数一定要有返回值，否则会发生compile error。
