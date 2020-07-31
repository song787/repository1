## 基础算法

### 用栈实现 进制转换算法

```c++
//进制转换器 n-待转换数字，base-待转换进制
void convert(_int64 n, int base){
	stack<char> _stack;
	static char digit[] = {'1','2','3','4','5','6','7','8','9','10','A','B','C','D','E','F'};
    
    while(n > 0){
        _stack.push(digit[n % base]);
        n /= base;
    }
    while(!_stack.empty())
        printf("%c",_stack.pop());
}
```

### 用栈实现 括号匹配算法

```c++
/*
思路：消去一对紧邻的左右括号，不会影响全局的匹配判断；
用栈来记录已经扫描的部分，遇到'（'就入栈，遇到'）'就出栈;
这是通用算法，不仅用于小括号，可以扩展到各种括号，甚至约定好的成对的格式；
*/
bool paren(const char exp[], int lo, int hi){
    stack<char> _stack;
    for(int i = lo;i <= hi;i++)
        if(exp[i] == '(')  
            _stack.push(exp[i]);//左括号 入栈
    	else if(!_stack.empty() && exp[i] == ')')
            _stack.pop();//右括号 出栈
    	else
            return false; //如果中途栈空了，说明右括号多了，返回false
    return _stack.empty();
}
```

### 中缀表达式求值

```c++
/*
栈的延迟缓冲，预读足够长之后，方能确定可处理的前缀；
借助栈结构，将所有扫描的部分保存为一个栈，在所有已经扫描过的部分中，有一些是已经经过判断以后，可以直接处理，但还有一部分是还不足以能够判断计算的部分，将通过栈来缓存；逐步将尚未扫描的部分扫描，并且处理掉；


*/
```



### 二叉树的先序遍历

```c++
/*
template<typename T>
struct Binnode{
	T data;
	Binnode<T>* parent,left,right;
	
	Binnode() : parent(NULL),left(NULL),right(NULL) {}
	Binnode(T e,Binnode<T>* p = NULL,Binnode<T>* lc = NULL,Binnode<T>* rc = NULL)
		: data(e),parent(p),left(lc),right(rc) {}
}
*/
//递归版本
template <typename T, typename VST>
void travPre_R (Binnode<T>* t, VST& visit){
    if(!t) return;
    visit(t -> data);
    travPre_R(t -> left, visit);
    travPre_R(t -> right, visit);
}
//迭代版本
template<typename T, typename VST>
void travPre_I1 (Binnode<T>* t, VST& visit){
    stack<Binnode<T>*> s;
    if(t) s.push(s);
    while(!s.empty()){
        Binnode<T>* temp = s.pop();
        if(temp->right != NULL)
            s.push(temp->right);
        if(temp->left != NULL)
            s.push(temp->left);
    }
}
//迭代版本2
template<typename T, typename VST>
void travPre_I2(Binnode<T>* t, VST& visit){
    stack<Binnode<T>*> s;
    while(true){
        visit(t->data);
        s.push(t->right);
        t = t->left;
    }
    if(s.empty()) break;
    t = s.pop();
}
```

### 二叉树中序遍历

```c++
/*
template<typename T>
struct Binnode{
	T data;
	Binnode<T>* parent,left,right;
	
	Binnode() : parent(NULL),left(NULL),right(NULL) {}
	Binnode(T e,Binnode<T>* p = NULL,Binnode<T>* lc = NULL,Binnode<T>* rc = NULL)
		: data(e),parent(p),left(lc),right(rc) {}
}
*/
//中序遍历 递归版
template< typename T, typename VST>
void travIn_R(Binnode<T>* t, VST& visit){
    if(!t) return;
    travIn_R(t->left,visit);
    visit(t->data);
    travIN_R(t->right,visit);
}
//中序遍历 迭代版
template<typename T,typename VST>
void travIn_I1(Binnode<T>* t,VST& visit){
    stack<Binnode<T>*> s;
    if(!t) return;
    while(true){
        while(t){
            s.push(t->left);
            t = t->left;            
        }
        if(s.empty()) break;
        Binnode<T>* t = s.pop();
        visit(t->data);
        t = t->right;
    }
}
//中序遍历 迭代版2-无需辅助栈
template<typename T,typename VST>
void travIn_I1(Binnode<T>* t,VST& visit){   
    
}       
    
```

### 二叉树后序遍历

```c++
/*
template<typename T>
struct Binnode{
	T data;
	Binnode<T>* parent,left,right;
	
	Binnode() : parent(NULL),left(NULL),right(NULL) {}
	Binnode(T e,Binnode<T>* p = NULL,Binnode<T>* lc = NULL,Binnode<T>* rc = NULL)
		: data(e),parent(p),left(lc),right(rc) {}
}
*/
//后序遍历 递归版
template<typename T,typename VST>
void travPos_R(Binnode<T>* t,VST& visit){
    if(!t) return ;
    travPos_R(t->left,visit);
    travPos_R(t->right,visit);
    visit(t->data);
}
//后序遍历 迭代版
template<typename T,typename VST>
void travPos_R(Binnode<T>* t,VST& visit){
    stack<Binnode<T>*> s;   //辅助栈
    if(t) s,push(t);	    //根节点入栈
    while(!s.empty()){			
        if(s.pop() != t->parent){ //若栈顶非当前节点之父（则必为其右兄），此时需在以其右兄为根之子树中，找到HLVFL（相当于递归深入其中）
            while(Binnode<T>* x = s.top()){ //在以S栈顶节点为根的子树中，找到最高左侧可见叶节点，沿途所遇节点依次入栈，自顶而下，反复检查当前节点
                if(x->left != NULL){//尽可能向左
                    if(x->right != NULL)//若有右孩子，优先入栈
                        s.push(x->right);
                    s.push(x->left)//然后才转至左孩子
                }
                else{//实不得已才向右
                    s.push(x->right);
                }
            }
            s.pop();//返回之前，弹出栈顶的空节点
        }
        t = s.pop();//弹出栈顶（即前一节点之后继），并访问之
        visit(t->data);            
    }
```

### 广度优先遍历

```c++
//数据结构课程中的算法
template<typename Tv,typename Te>
void bfs(int s){
    reset();  ////初始化
    int clock = 0;
    int v = s;
    do//逐一检查所有顶点
        if(undiscovered == status(v))//一旦遇到尚未发现的顶点
            BFS(v,clock);//即从该顶点出发启动一次BFS
    while(s != (v = (++v % n)));//按序号检查，故不漏不重
}
template<typename Tv,typename Te>
void BFS(int v, int& clock){
    Queue<int> Q;//初始化
    status(v) = discovered;
    Q.enqueue(v);
    while(!Q.empty()){
        int v = Q.dequeue();
        dTime(v) = ++clock;//取出队首顶点v
        for(int u = firstNbr(v);-1 < u;u = nextNbr(v,u))//考察v的每一邻居u，根据u的状态分别处理
            if(status(u) == undiscovered){//若u尚未被发现，则发现该顶点
                status(u) = discovered;
                Q.enqueue(u);
                status(v,u) = tree;//引入树边拓展支撑树
                parent(u) = v;
            }        
        	else//若u已被发现，或者甚至已访问完毕，则
                status(v,u) = cross;//将(v, u)归类于跨边
        status(v) = visited;//当前顶点访问完毕
    }
}

//leetcode刷题中的写法

```

### 深度优先搜索DFS

```c++
/*
访问顶点s，若s尚有未被访问的邻居，任取其一u，递归执行DFS(u)。直到没有子节点为止，就返回上一级节点，查看是否有其他未访问节点，如果没有就继续返回更上一级，如果有就执行DFS算法；
*/
0009 template <typename Tv, typename Te> //深度优先搜索DFS算法（全图）
0010 void Graph<Tv, Te>::dfs ( int s ) { //assert: 0 <= s < n
0011    reset(); int clock = 0; int v = s; //初始化
0012    do //逐一检查所有顶点
0013       if ( UNDISCOVERED == status ( v ) ) //一旦遇到尚未发现的顶点
0014          DFS ( v, clock ); //即从该顶点出发启动一次DFS
0015    while ( s != ( v = ( ++v % n ) ) ); //按序号检查，故不漏不重
0016 }
0017 
0018 template <typename Tv, typename Te> //深度优先搜索DFS算法（单个连通域）
0019 void Graph<Tv, Te>::DFS ( int v, int& clock ) { //assert: 0 <= v < n
0020    dTime ( v ) = ++clock; status ( v ) = DISCOVERED; //发现当前顶点v
0021    for ( int u = firstNbr ( v ); -1 < u; u = nextNbr ( v, u ) ) //枚举v的所有邻居u
0022       switch ( status ( u ) ) { //并视其状态分别处理
0023          case UNDISCOVERED: //u尚未发现，意味着支撑树可在此拓展
0024             type ( v, u ) = TREE; parent ( u ) = v; DFS ( u, clock ); break;
0025          case DISCOVERED: //u已被发现但尚未访问完毕，应属被后代指向的祖先
0026             type ( v, u ) = BACKWARD; break;
0027          default: //u已访问完毕（VISITED，有向图），则视承袭关系分为前向边或跨边
0028             type ( v, u ) = ( dTime ( v ) < dTime ( u ) ) ? FORWARD : CROSS; break;
0029       }
0030    status ( v ) = VISITED; fTime ( v ) = ++clock; //至此，当前顶点v方告访问完毕
0031 }

//leetcode刷题写法

```

### 排序算法

```c++
#include<iostream>
#include<vector>
#include<queue>

using namespace std;

int main(){
    
    return 0;
}

class solution{
public:
    
//冒泡排序
    //-版本1
    void bubbleSort(vector<int>& nums){
        int len = nums.size();
        for(int i = 0;i < len;++i)
            for(int j = 0;j < len-i-1;++j)
        		if(nums[j] > nums[j+1])
                    swap(nums[j],nums[j+1]);
    }
    //-版本2
    void bubbleSort(vector<int>& nums){
        int lo = 0;
        int hi = nums.size();
        while(lo < hi){
            int last = lo;
            int lo_ptr = lo;
            while(++lo_ptr < hi)
                if(nums[lo_ptr-1] > nums[lo_ptr]){
                    last = lo_ptr;//记录最右侧逆序对的位置，并最终更新到hi中；
                    swap(nums[lo_ptr-1],nums[lo_ptr]);
                }
            hi = last;
        }
    } 
    
//归并排序
    void mergeSort(vector<int>& nums, int lo, int hi){
        if(hi-lo < 2) return ;
        int mid = lo + (hi-lo)/2;
        mergeSort(nums,lo,mid);
        mergeSort(nums,mid,hi);
        merge(nums,lo,mid,hi);
    }
    void merge(vector<int>& nums,int lo,int mid,int hi){
        vector<int> temp_nums;
        for(int i = lo;i < mid;i++)
            temp_nums1.push_back(nums[i]);
        for(int i = lo,j = 0,k = mid;(j < mid) || (k < hi);){
            if((j < mid) && (k >= hi || (temp_nums[j] <= nums[k])))
                nums[i++] = temp_nums[j++];
            if((k < hi) && (j >= mid || (nums[k] < temp_nums[j])))\
                nums[i++] = nums[k++];
        }        
    }
    
    //选择排序
    void selectSort(vector<int>& nums){
        int size = nums.size();
        for(int i = 0;i < size;++i){
            int index_max = 0;
            for(int j = 1;j < size - i;++j)
                if(nums[j] > nums[index_max])
                    index_max = j;   
            if(index_max != size-i-1)
                swap(nums[index_max],nums[size-i-1]);
        }
    }
    
    //插入排序
    void insertSort(vector<int>& nums){
        int size = nums.size();
        for(int i = 1;i < size;++i){
            int key = nums[i];
            int j = i-1;
            while(j >= 0 && nums[j] > key){
                nums[j+1] = nums[j];
                --j;
            }
            nums[j+1] = key;
        }
    }
    
    //堆排序
    void heapSort(vector<int>& nums){
		priority_queue<int,vector<int>,cmp> pq;
        int size = nums.size();
    	for(int i = 0;i < size;++i){
            pq.push(nums[i]);
        }
        for(int k = 0;k < size;++k){
            cout<<pq.top()<<" ";
            pq.pop();
        }
    }
    class cmp{
    public:
        bool operator()(int p1,int p2){
            //return p1 < p2;//大顶堆
            return p1 > p2;//小顶堆
        }
    };
    
    //快速排序
    void quickSort(vector<int>& nums, int lo, int hi){
        if(hi - lo < 2) return ;
        int mid = partition(nums,lo,hi);
        quickSort(nums,lo,mid);
        quickSort(nums,mid+1,hi);
    }
    int partition(vector<int>& nums,int lo,int hi){
        swap(nums[lo],nums[lo + rand()%(hi - lo)]);
        int mid = lo;
        int pivot = nums[lo];
        for(int k = lo + 1;k < hi;++k){
            if(nums[k] < pivot)
                swap(nums[++mid],nums[k]);
        }
        swap(nums[mid],nums[lo]);
        return mid;
    }
    
    //希尔排序
    
};
```

### 串匹配算法

```c++
#include<iostream>

using namespace std;

int main(){
    
    return 0;
}

class solution{
	
    //BF算法
    
    
    //KMP算法
    
    
    //BM_BC算法
    
    
    //BM_GS算法
    
    
    //Rabin-karp算法
    
};
```



## 算法思想

### 1、分治

```c++
//分治一般都是包含着递归，不断的进行分块分块，直到分到最小，然后一层一层往上返回各自的结果；
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<TreeNode*> generateTrees(int n) {
        if(n == 0)  return { };
        return subtree(1,n);
    }
    vector<TreeNode*> subtree(int left,int right){
        vector<TreeNode*> leftTree;
        vector<TreeNode*> rightTree;
        vector<TreeNode*> res;
        if(left > right){
            res.push_back(nullptr);
            return res;
        }
        for(int i = left;i <= right;i++){
            leftTree = subtree(left,i-1);
            rightTree = subtree(i+1,right);
        
            for(auto left_i:leftTree)
                for(auto right_i:rightTree){
                    TreeNode* t = new TreeNode(i);
                    t->left = left_i;
                    t->right = right_i;
                    res.push_back(t);
                }                
        }
        return res;
    }
};
```

### 2、二分查找思想[未整理]

```c++
//采用最极端的方式去确定lo与hi的变化值，也就是最边界的情况；




```

### 3、贪心算法[未整理]

```c++
//






//leetcode.53 最大子序和
/*
给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
*/
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        /*
        //动态规划-常规解法
        if(nums.empty()) return 0;
        int size = nums.size();
        vector<int> dp(size);
        int res = nums[0];
        dp[0] = nums[0];
        for(int i = 1;i < size;i++){
            dp[i] = max(dp[i-1] + nums[i],nums[i]);
            res = max(res,dp[i]);
        }
        return res;
        */
        /*
        //动态规划-空间压缩解法
        if(nums.empty()) return 0;
        int size = nums.size();
        int dp = nums[0];
        int res = dp;
        for(int i = 1;i < size;i++){
            dp = max(dp + nums[i],nums[i]);
            res = max(dp,res);
        }
        return res;
        */
        
        //贪心解法
        if(nums.empty()) return 0;
        int size = nums.size();
        int sum = 0;
        int res = nums[0];
        for(int i = 0;i < size;i++){
            sum += nums[i];
            res = max(res,sum);
            if(sum < 0)
                sum = 0;
        }
        return res;
    }
};

```

### 4、排序问题

- 快排思想

> 很多时候用的不是快速排序的算法，而是用快速排序中partition分割的思想；

```c++
//快速排序基本算法（迭代版）-由小到大排序

class solution{
public:
	void quickSort(vector<int>& nums,int lo, int hi){//[lo,hi)
    	if(nums.empty()) return ;
        if(hi-lo < 2) return ;
        int mid = partition(nums,lo,hi);
        quickSort(nums,lo,mid);
        quickSort(nums,mid+1,hi);
	}
  	int partition(vector<int>& nums, int lo, int hi){
        swap(nums[lo],nums[lo+rand()%(hi-lo)]);
        int mid = lo;
        int pivot = nums[lo];
        for(int k = lo+1;k < hi;k++){
            if(nums[k] < pivot){
                swap(nums[++mid],nums[k]);
            }
        }
        swap(nums[mid],nums[lo]);
        return mid;        
    }      
};
```

- 堆排思想

> 利用堆的有序性来实现排序，堆在逻辑上是一棵完全二叉树，而底层的实现一般是利用向量来实现的；在代码中通常使用优先级队列来表示一个堆；

```c++
#include<queue>

priority_queue<Type, Container, Functional>;
/*
Type 是数据类型;
Container 是容器类型(Container必须是用数组实现的容器，比如vector,deque等等，但不能用 list。STL里面默认用的是vector);
Functional 就是比较的方式,默认是大顶堆。
*/
/*
top 访问队头元素
empty 队列是否为空
size 返回队列内元素个数
push 插入元素到队尾 (并排序)
emplace 原地构造一个元素并插入队列
pop 弹出队头元素
swap 交换内容
*/
```

通常情况下求前`k大`用小根堆，求前`k小`用大根堆。如果非要用大根堆求前`k大`，那么就要转换判断堆大小的条件，转为判断`size == n - k`

```c++
//堆排序的基本实现 -小顶堆

#include<iostream>
#include<queue>
using namespace std;

class solution{//取第k大的元素
public:
    int solution_a(vector<int>& nums, int k){
        
        priority_queue<int, vector<int>, cmp> pq;
        for(int ele:nums){
            pq.push(ele);
            if(pq.size() > k)
                pq.pop();
        }
        return pq.top();
    }
    class cmp{
    public:
        bool operator()(int p1,int p2){
            return p1 > p2;
        }
    };
};
```

- 桶排思想

> 桶排序：设置很多的桶，每个桶设置一个标签，然后将相同标签的元素丢到同一个桶中，然后将桶按照桶的标签来排序；桶一般借助map或者unordered_map来实现；



**Top K 问题**

>  1、不涉及频率问题： 可以用快速排序、堆排序实现；
>
>  2、涉及频率问题： 一般是先利用map容器计算出需要的频率值 ( 比如：元素出现的次数 ) ；然后再排序；
>

**荷兰国旗问题**

> 一般采用三指针，左右指针分别指向左边区域边界、右边区域边界，中间指针用于遍历；

### 5、动态规划[未整理]



### 6、搜索算法[未整理]



### 7、双指针思想

双指针主要用于遍历线性列表类容器，通过两个指针的初始位置，还可以再细分为两类：一类是两指针同侧初始化，一类是两指针异侧初始化。

- **两指针同侧初始化**

这一类指针通常出现在链表容器中，并且两个指针的步长是不相同的，表现出一个指针移动慢，称`慢指针`，一个指针移动快，称`快指针`，通常初始化在容器的头节点处。典型问题：

一、判断链表是否存在环，存在就返回环的长度和环开始的节点位置；

> 判断是否存在环：利用快慢指针实现，在链表的头部初始化两个指针，慢指针指向`头部`，快指针指向`头部`，先移动快慢指针，然后通过快指针的位置来判断进程，一般是通过快指针不为`NULL`为条件来循环（注意在fast->next->next之前要判断fast->next是否为空），如果退出循环说明不存在环，如果快慢指针相等了，说明找到了环；

> 返回环的长度：快慢指针第一次相遇的地方，就是快指针比慢指针足足多跑了一个环的距离，所以假定慢指针移动距离为`m`，快指针移动距离为`2m`，那么环的长度就是`m`；
>

> 返回环开始的节点位置：快慢指针第一次相遇的地方，假设慢指针移动距离为`m`，快指针移动距离为`2m`，假设相遇点距环起点的距离为`k`，那么非环部分的长度就是`m-k`，以及相遇点到环起点的剩余部分的距离也是`m-k`，这时只需要将某一个指针初始化为`head`节点，然后两个指针同步移动，且步长均为`1`，那么下一次相遇的节点就是环开始的节点；
>

二、返回链表的`Kth element`；

> 正数第K个元素：此处不需要用双指针，用单指针+计数变量就可以实现；

> 倒数第k个元素：利用双指针，先将一个指针移动`k`步，然后两个指针同步移动，步长均为`1`，当快指针移动到`null`处，慢指针所在位置就是倒数第`k`个元素的位置；

> 中间点元素：利用双指针，一快一慢，如果元素的个数为奇数个，那么当快指针到达最后一个元素的时候，慢指针所在的位置就是中间点，如果元素的个数为偶数个，那么当快指针到达null处时，慢指针所在位置就是中间点靠右边的位置；

- **两指针异侧初始化**

这一类指针通常出现在数组、字符串等容器中，一个指针初始化在容器的开端int ptr1 = 0，一个指针初始化在容器的末端int ptr2 = nums.size() - 1，根据条件同步或者异步的向容器中间移动。

一、二分查找

> 一个头指针，一个尾指针

二、两数和=target问题（前提是有序）

> 在有序的前提下，一个头指针，一个尾指针，通过与目标值的比较来调整头尾指针，如果小于目标值，头指针前移，如果大于目标值，尾指针后移；

三、反转数组

> swap（元素[头指针++]，元素[尾指针--]）；

四、滑动窗口





## 重做leetcode题目

```c++
/*重做题目*/

//547 朋友圈
//241 为运算表达式设计优先级
//95  不用的二叉搜索树
//51  N皇后
//34  在排序数组中查找元素的第一个和最后一个位置
```

