## 基础算法

### 1. 用栈实现 进制转换算法

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
//----------------------------------------
#include<iostream>
#include<math.h>
#include<string>
#include<stack>
using namespace std;
int funcNto10(string &str,int base){
    int size = str.size();
    int sum = 0;
    for(int i = 0;i < size;++i){
        if(str[i] >= '0' && str[i] <= '9'){
            int temp = str[i]-'0';
            sum += temp*pow(base,size-i-1);
        }
        else if(str[i] >= 'A' && str[i] <= 'Z'){
            int temp = str[i]-'A'+10;
            sum += temp*pow(base,size-i-1);
        }
    }
    return sum;
}
string func10toN(int num,int base){
    string str;
    stack<char> stack;
    char ch;
    while(num != 0){
        int temp = num % base;
        if(temp > 9)
            ch = 'A'+temp-10;
        else
            ch = '0'+temp;
        stack.push(ch);
        num /= base;
    }
    while(!stack.empty()){
        str += stack.top();
        stack.pop();
    }
    return str;
}
int main(){
    string str = "2B";
    int res = funcNto10(str,16);
    cout<<res<<endl;
    int num = 43;
    string rest= func10toN(num,8);
    cout<<rest<<endl;
    return 0;
}
```

### 2. 用栈实现 括号匹配算法

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

### 3. 中缀表达式求值

```c++
/*
栈的延迟缓冲，预读足够长之后，方能确定可处理的前缀；
借助栈结构，将所有扫描的部分保存为一个栈，在所有已经扫描过的部分中，有一些是已经经过判断以后，可以直接处理，但还有一部分是还不足以能够判断计算的部分，将通过栈来缓存；逐步将尚未扫描的部分扫描，并且处理掉；


*/
```

### 4. 二叉树的先序遍历

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
struct TreeNode{
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x):val(x),left(NULL),right(NULL) {}
};
void travPre(TreeNode *root){
    stack<TreeNode*> stack;
    if(root) stack.push(root);
    while(!stack.empty()){
        root = stack.top();
        stack.pop();
        visit(root->data);
        if(root->right) stack.push(root->right);
        if(root->left) stack.push(root->left);
    }
}
//迭代版本2
struct TreeNode{
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x):val(x),left(NULL),right(NULL) {}
};
vector<int> travPre2(TreeNode *root){
    vector<int> res;
    stack<TreeNode*> stack;
    while(true){
        while(root){
            res.push_back(root->val);
            stack.push(root->right);
            root = root->left;
        }
        if(stack.empty()) break;
        root = stack.top();
        stack.pop();
    }
    return res;
}
```

### 5. 二叉树中序遍历

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
struct TreeNode{
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x),left(NULL),right(NULL) {}
};
vector<int> travIn(TreeNode *root){
    vector<int> res;
    stack<TreeNode*> stack;
    while(root || !stack.empty()){
        while(root){
            stack.push(root);
            root = root->left;
        }
        root = stack.top();
        stack.pop();
        res.push_back(root->val);
        root = root->right;
    }
    return res; 
}
```

### 6. 二叉树后序遍历

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
//后序遍历 迭代版（leetcode）
 struct TreeNode {
     int val;
     TreeNode *left;
     TreeNode *right;
     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 };
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> ret;
        stack<TreeNode*> s;
        set<TreeNode*> myset;
        if(root) s.push(root);
        while(!s.empty()){
            root = s.top();
            bool leftVisited = true, rightVisited = true;
            if(root->right && myset.find(root->right) == myset.end()){
                s.push(root->right);
                rightVisited = false;
            }
            if(root->left && myset.find(root->left) == myset.end()){
                s.push(root->left);
                leftVisited = false;
            }
            if(leftVisited && rightVisited){
                ret.push_back(root->val);
                myset.insert(root);
                s.pop();
            }
        }
        return ret;        
    }
};
```

### 7. 广度优先遍历

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

### 8. 深度优先搜索DFS

```c++
/*
访问顶点s，若s尚有未被访问的邻居，任取其一u，递归执行DFS(u)。直到没有子节点为止，就返回上一级节点，查看是否有其他未访问节点，如果没有就继续返回更上一级，如果有就执行DFS算法；

//leetcode刷题写法

```

### 9. 层序遍历

```c++
//层序遍历

```

### 10. 排序算法

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
//归并排序
    void mergeSort(vector<int>& nums, int lo, int hi){
        if(hi-lo < 2) return ;
        int mid = lo + (hi-lo)/2;
        mergeSort(nums,lo,mid);
        mergeSort(nums,mid,hi);
        merge(nums,lo,mid,hi);
    }
    void merge(vector<int>& nums,int lo,int mid,int hi){
        int size = mid - lo;
        vector<int> temp_nums;
        for(int i = lo;i < mid;i++)
            temp_nums.push_back(nums[i]);
        for(int i = lo,j = 0,k = mid;(j < size) || (k < hi);){
            if((j < size) && (k >= hi || (temp_nums[j] <= nums[k])))
                nums[i++] = temp_nums[j++];
            if((k < hi) && (j >= size || (nums[k] < temp_nums[j])))
                nums[i++] = nums[k++];
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

### 11. 串匹配算法

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



## 手撕题目

### 链表

#### 建立一个双向链表；

#### 设计一个数据结构 list:  rpush rpop lpush lpop index 五种方法的时间复杂度均为 O(1)



#### 反转链表

```c++
//递归法
struct ListNode{
    int val;
    ListNode *next;
    ListNode(int x) : val(x),next(NULL) {}
};
class solution{
public:
    ListNode * reverseList(ListNode *head){
        if(head == NULL || head->next == NULL) return head;
        ListNode * last = reverseList(head->next);
        head->next->next = head;
        head->next = NULL;
        return last;        
    }
};
//迭代法
class solution{
public:
    ListNode * reverseList(ListNode *head){
        if(head == NULL) return NULL;
        ListNode * pre = NULL;
        ListNode * cur = head;
        while(cur != NULL){
            ListNode * temp = cur->next;
            cur->next = pre;
            pre = cur;
            cur = temp;
        }
        return pre;
    }
}
```

#### leetcode 92 反转链表从m到n

```c++
//迭代法
struct ListNode{
    int val;
    ListNode * next;
    ListNode(int x) : val(x),next(NULL) {}
}
class solution{
public:
    ListNode * reverse(ListNode *head, int m, int n){
        if(m == 1){
            ListNode *ptr = reverseN(head,n);
            return ptr;
        }
        ListNode *last = reverse(head->next,m-1,n-1);
        head->next = last;
        return head;
    }
    ListNode * reverseN(ListNode *head, int n){
        if(n == 1){
            successed = head->next;
            return head;
        }
        ListNode *last = reverseN(head->next,n-1);
        head->next->next = head;
        head->next = successed;
        return last;
    }
    ListNode *successed;
};
```

#### leetcode 25 K个一组反转链表；

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        if(head == NULL) return head;
        int count = k;
        ListNode* ptr = head;
        ListNode* cur = head;
        ListNode* pre = head;
        bool first = true;
        while(ptr != NULL){
            while(count != 1){
                ptr = ptr->next;
                --count;
                if(ptr == NULL)
                    return head;
            }
            ptr = cur;
            if(first == true){
                cur = reverseN(cur,k);
                head = cur;
                first = false;
            }
            else{
                cur = reverseN(cur,k);
                pre->next = cur;
            }
            cur = succ;
            pre = ptr;
            ptr = succ;
            count = k;
        }
        return head;
    }
    ListNode* reverseN(ListNode* head,int n){
        if(n == 1){
            succ = head->next;
            return head;
        }
        ListNode* last = reverseN(head->next,n-1);
        head->next->next = head;
        head->next = succ;       
        return last;
    }
    ListNode* succ;
};
```

#### 判断链表是否有环,并返回入环节点

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if(head == NULL) return head;
        ListNode* slow = head;
        ListNode* fast = head;
        while(fast != NULL && fast->next != NULL){
            slow = slow->next;
            fast = fast->next->next;
            if(slow == fast){
                slow = head;
                while(slow != fast){
                    slow = slow->next;
                    fast = fast->next;
                }
                return slow;
            }
        }
        return NULL;
    }
};
```

#### 单链表只遍历一次，要找到链表的中间位置要怎么做；

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* middleNode(ListNode* head) {
        if(head == NULL) return NULL;
        ListNode* slow = head;
        ListNode* fast = head;
        while(fast != NULL && fast->next != NULL){
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }
};
```

#### 找到两个链表的首个公共节点；

```c++
//ok
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* first = headA;
        ListNode* second = headB;
        while(first != second){
            first = first == NULL ? headB : first->next;
            second = second == NULL ? headA : second->next;
        }
        return first;
    }
};
```

#### 二叉树的Z型遍历

```c++
//Z型遍历
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {//BFS+deque  法二：层序遍历，奇数reverse偶数不反转；
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        if(root == NULL) return {};
        vector<vector<int>> res;
        deque<TreeNode*> deque;
        deque.push_back(root);
        bool sw = true;
        while(!deque.empty()){
            int size = deque.size();
            vector<int> level;
            while(size--)
                if(sw == true){
                    root = deque.front();
                    deque.pop_front();
                    level.push_back(root->val);
                    if(root->left != NULL) deque.push_back(root->left);
                    if(root->right != NULL) deque.push_back(root->right);
                } 
                else{
                    root = deque.back();
                    deque.pop_back();
                    level.push_back(root->val);
                    if(root->right != NULL) deque.push_front(root->right);
                    if(root->left != NULL) deque.push_front(root->left);
                }
            res.push_back(level);
            sw = !sw;
        }
        return res;        
    }
};
```

#### 合并两个有序链表

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode dummyHead(0);
        ListNode* ptr = &dummyHead;
        while(l1 && l2){
            if(l1->val < l2->val){
                ptr->next = l1;
                ptr = l1;
                l1 = l1->next;
            }
            else{
                ptr->next = l2;
                ptr = l2;
                l2 = l2->next;
            }
        }
        ptr->next = l1 ? l1 : l2;
        return dummyHead.next;
    }
};
```

#### 在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序。

```c++
//法1：借助vector进行排序，然后重新装填到链表中，时间O（2n+nlogn),空间O(n);
//法2：利用归并排序的递归法，时间O(nlogn),空间O(n+logn);
//法3：利用归并排序的button to up方法，时间O(nlogn)，空间O(1);
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        ListNode dummyNode(0);
        dummyNode.next = head;
        int length = 0;
        ListNode* p = head;
        while(p){
            p = p->next;
            ++length;
        }
        for(int size = 1;size < length;size <<= 1){
            ListNode* cur = dummyNode.next;
            ListNode* tail = &dummyNode;
            
            while(cur){
                auto left = cur;
                auto right = cut(left,size);
                cur = cut(right,size);

                tail->next = merge(left,right);
                while(tail->next) tail = tail->next;
            }
        }
        return dummyNode.next;
    }
    ListNode* cut(ListNode* head,int size){
        auto ptr = head;
        while(--size && ptr){
            ptr = ptr->next;
        }
        if(ptr == NULL)
            return NULL;
        ListNode* next = ptr->next;
        ptr->next = NULL;
        return next;
    }
    ListNode* merge(ListNode* left,ListNode* right){
        ListNode dummyNode(0);
        ListNode* ptr = &dummyNode;
        while(left && right){
            if(left->val < right->val){
                ptr->next = left;
                ptr = ptr->next;
                left = left->next;
            }
            else{
                ptr->next = right;
                ptr = ptr->next;
                right = right->next;
            }
        }
        ptr->next = left ? left : right;
        return dummyNode.next;
    }
};
```

#### 给定一个字符串 s，将 s 分割成一些子串，使每个子串都是回文串。返回 s 所有可能的分割方案。  



#### 计算数组的小和



#### 合并两个数组并排序



#### 二叉树的层序遍历

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {//BFS
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        if(root == NULL) return {};
        queue<TreeNode*> deq;
        deq.push(root);
        vector<vector<int>> res;
        while(!deq.empty()){
            int size = deq.size();
            vector<int> vec;
            while(size--){
                root = deq.front();
                deq.pop();
                vec.push_back(root->val);
                if(root->left != NULL) deq.push(root->left);
                if(root->right != NULL) deq.push(root->right);
            }
            res.push_back(vec);
        }    
        return res;            
    }
};
//自底向上的层序遍历
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        if(root == NULL) return {};
        int n = getDepth(root);
        vector<vector<int>> res(n,vector<int>());
        queue<TreeNode*> queue;
        queue.push(root);
        while(!queue.empty()){
            int size = queue.size();
            vector<int> level;
            while(size--){
                root = queue.front();
                queue.pop();
                level.push_back(root->val);
                if(root->left != NULL) queue.push(root->left);
                if(root->right != NULL) queue.push(root->right);
            }
            res[--n] = level;
        }
        return res;
    }
    int getDepth(TreeNode * root){
        if(root == NULL) return 0;
        return max(getDepth(root->left),getDepth(root->right))+1;
    }
};


```



LFU leetcode 460



二分法求浮点数平方根，不得递归，精度要求0.001 



#### 多线程打印ABCD

```c++

```

#### 实现两个线程交替打印AB

```c++
#include<iostream>
#include<windows.h>
#include<string>

using namespace std;

class THREAD_DATA{
public:
    int maxnum;
    string data;
    THREAD_DATA() : maxnum(0) ,data("") {}
    THREAD_DATA(int num,string str) : maxnum(num),data(str) {}
};

HANDLE cout_Mutex;
HANDLE hEvent;

DWORD WINAPI MyThread1(LPVOID lpParamter){

    THREAD_DATA *data = (THREAD_DATA *)lpParamter;
    for(int i = 0;i < data->maxnum;++i){
        WaitForSingleObject(hEvent,INFINITE);
        cout<<data->data<<"A"<<endl;
        //ReleaseMutex(cout_Mutex);
        SetEvent(hEvent);
    }
    return 0L;
};

DWORD WINAPI MyThread2(LPVOID lpParamter){

    THREAD_DATA *data = (THREAD_DATA *)lpParamter;
    for(int i = 0;i < data->maxnum;++i){
        WaitForSingleObject(hEvent,INFINITE);
        cout<<data->data<<"B"<<endl;
        //ReleaseMutex(cout_Mutex);
        SetEvent(hEvent);
    }
    return 0L;
}

int main(){

    THREAD_DATA thread_data1,thread_data2;
    thread_data1.maxnum = 5;
    thread_data1.data = "线程1----";
    thread_data2.maxnum = 5;
    thread_data2.data = "线程2----";

    cout_Mutex = CreateMutex(NULL,FALSE,NULL);
    hEvent = CreateEvent(NULL, FALSE, TRUE, NULL);

    HANDLE hThread1 = CreateThread(NULL,0,MyThread1,&thread_data1,0,NULL);
    HANDLE hThread2 = CreateThread(NULL,0,MyThread2,&thread_data2,0,NULL);
    CloseHandle(hThread1);
    CloseHandle(hThread2);
    system("pause");   
    return 0;
}
```

手写大小端转换函数 



手写socket断点续传文件

手撕智能指针

斐波那契数列 

二叉树的最大路径和、二叉树最大和的路径；

DFS、BFS

整数转化成字符串；

迭代二叉树的深度；

0-n-1中缺失的数字，两种方法；

二叉搜索树后序遍历；

IP地址字符串转换为32位整数；

两个有序数组，其中一个有足够空位，不使用额外空间排序到含空位数组中；

求二叉树两个节点的最小距离；

对大规模数据进行去重；

寻找无序整数数组中第一个缺失的正数；

对给出一个数组中的每个元素求因数个数；

二维数组回旋打印；