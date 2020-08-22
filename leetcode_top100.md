### leetcode 2 两数相加

```c++
/*
执行用时：40 ms, 在所有 C++ 提交中击败了29.82%的用户
内存消耗：9.3 MB, 在所有 C++ 提交中击败了79.32%的用户
*/
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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
       if(l1 == NULL || l2 == NULL) return NULL;
       ListNode* pre = new ListNode(0);
       ListNode* ptr = pre;
       bool flag = false;
       int sum = 0;
       while(l1 || l2){
           if(l1 == NULL){
               sum = l2->val + flag;
               flag = false;
               l2 = l2->next;
           }
           else if(l2 == NULL){
               sum = l1->val + flag;
               flag = false;
               l1 = l1->next;
           }
           else{
               sum = l1->val + l2->val + flag;
               flag = false;
               l1 = l1->next;
               l2 = l2->next;
           }
           if(sum > 9) {
                flag = true;
                sum -= 10;
            }
            pre->next = new ListNode(sum);
            pre = pre->next;          
       }
       if(flag){
           pre->next = new ListNode(1);
       }
       return ptr->next;
    }
};
```

### leetcode 3 无重复字符的最长子串

```c++
//超时
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        if(s.empty()) return 0;
        set<char> myset;
        int size = s.size();
        int count = 1;
        int max_count = 1;
        for(int j = 0;j < size;++j){
            count = 1;
            myset.clear();
            myset.insert(s[j]);
            for(int i = j+1;i < size;++i){
                if(myset.find(s[i]) == myset.end()){
                    ++count;
                    max_count = max(max_count,count);
                    myset.insert(s[i]);
                }
                else
                    break;
            }
        }
        return max_count;
    }
};

/*
执行用时：48 ms, 在所有 C++ 提交中击败了47.70%的用户
内存消耗：8.8 MB, 在所有 C++ 提交中击败了40.35%的用户
*/
class Solution {//双指针滑动窗口
public:
    int lengthOfLongestSubstring(string s) {
        if(s.empty()) return 0;
        int max_count = 0;
        unordered_map<char,int> mymap;

        for(int i = 0,j = 0;j < s.size();++j){
            mymap[s[j]]++;
            while(mymap[s[j]] > 1)
                mymap[s[i++]]--;          
            max_count = max(max_count,j-i+1);
        }
        return max_count;
    }
};
```

### leetcode 4 寻找两个正序数组的中位数

```c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {

        int m = nums1.size();
        int n = nums2.size();
        int mid1 = (m+n+1)/2;
        int mid2 = (m+n+2)/2;
        return (getKth(nums1,0,m-1,nums2,0,n-1,mid1) + getKth(nums1,0,m-1,nums2,0,n-1,mid2)) * 0.5;
    }
    int getKth(vector<int> nums1,int lo1,int hi1,vector<int> nums2,int lo2,int hi2,int k){
        int lenth1 = hi1 - lo1 + 1;
        int lenth2 = hi2 - lo2 + 1;
        if(lenth1 > lenth2) return getKth(nums2,lo2,hi2,nums1,lo1,hi1,k);
        if(lenth1 == 0) return nums2[lo2 + k - 1];
        if(k == 1) return min(nums1[lo1],nums2[lo2]);
        int i = lo1 + min(lenth1,k/2)-1;
        int j = lo2 + min(lenth2,k/2)-1;
        
        if(nums1[i] > nums2[j]){
            return getKth(nums1,lo1,hi1,nums2,j+1,hi2,k-(j-lo2+1));
        }
        else{
            return getKth(nums1,i+1,hi1,nums2,lo2,hi2,k-(i-lo1+1));
        }
    }
};
// O(log(m+n));
```

### leetcode 234 回文链表

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
    bool isPalindrome(ListNode* head) {
        if(head == NULL || head->next == NULL)
            return true;
        ListNode* slow = head;
        ListNode* fast = head;
        ListNode* p = NULL;
        ListNode* pre = NULL;
        while(fast != NULL && fast->next != NULL){
            p = slow;
            slow = slow->next;
            fast = fast->next->next;
            p->next = pre;
            pre = p;
        }
        if(fast)
            slow = slow->next;       
        while(p != NULL){
            if(p->val != slow->val)
                return false;
            p = p->next;
            slow = slow->next;
        }
        return true;
    }
};
```

### leetcode 448 找到所有数组中消失的数字

```c++
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        for(int i = 0;i < nums.size();++i){
            nums[abs(nums[i])-1] = -abs(nums[abs(nums[i])-1]);
        }
        vector<int> res;
        for(int i = 0;i < nums.size();++i){
            if(nums[i] > 0)
                res.push_back(i+1);
        }
        return res;
    }
};
```

### leetcode 461 汉明距离

```c++
class Solution {
public:
    int hammingDistance(int x, int y) {

        int distance = 0;
        while(x || y){
            int x_r = x % 2;
            int y_r = y % 2;
            if(x_r != y_r)
                ++distance;
            x = x >> 1;
            y = y >> 1;
        }
        return distance;
    }
};
```

### leetcode 538 把二叉搜索树转换为累加树

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
class Solution {
public:
    TreeNode* convertBST(TreeNode* root) {
        if(root == NULL) return NULL;
        stack<TreeNode*> stack;
        TreeNode* ptr = root;
        int sum = 0;
        while(true){
            while(ptr){
                stack.push(ptr);
                ptr = ptr->right;
            }
            if(stack.empty()) break;
            ptr = stack.top();
            stack.pop();
            ptr->val = ptr->val + sum;
            sum = ptr->val;
            ptr = ptr->left;
        }
        return root;
    }
};
```

### leetcode 543 二叉树的直径

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
class Solution {
public:
    int max_sum = 0;
    int diameterOfBinaryTree(TreeNode* root) {
        if(root == NULL) return 0;
        int dia = deep(root);
        return max_sum;
    }
    int deep(TreeNode* root){
        int deep_left = 0;
        int deep_right = 0;

        deep_left = root->left == NULL ? 0 : deep(root->left) + 1;
        deep_right = root->right == NULL ? 0 : deep(root->right) + 1;
        max_sum = max(max_sum,deep_left+deep_right);
        return max(deep_left,deep_right);        
    }
};
```

### leetcode 581 最短连续无序子数组*

```c++
class Solution {
public:
    int findUnsortedSubarray(vector<int>& nums) {

        int size = nums.size();
        if(size < 1) return 0;
        int high = 0,low = size-1;
        int maxval = nums[high],minval = nums[low];

        for(int i = 1;i < size;++i){
            maxval = max(maxval,nums[i]);
            minval = min(minval,nums[size-i-1]);
            if(nums[i] < maxval) high = i;
            if(nums[size-i-1] > minval) low = size-i-1;
        }
        return high > low ? high-low+1 : 0;
    }
};
```

### leetcode 617 合并二叉树

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
class Solution {//递归
public:
    TreeNode* mergeTrees(TreeNode* t1, TreeNode* t2) {
        if(t1 == NULL) return t2;
        if(t2 == NULL) return t1;
        t1->val += t2->val;
        t1->left = mergeTrees(t1->left,t2->left);
        t1->right = mergeTrees(t1->right,t2->right);
        return t1;
    }
};

class Solution {//迭代
public:
    TreeNode* mergeTrees(TreeNode* t1, TreeNode* t2) {
        if(t1 == NULL) return t2;
        if(t2 == NULL) return t1;
        queue<TreeNode*> que;
        que.push(t1);
        que.push(t2);

        while(!que.empty()){
            TreeNode* node1 = que.front();que.pop();
            TreeNode* node2 = que.front();que.pop();
            node1->val += node2->val;
            if(node1->left != NULL && node2->left != NULL){
                que.push(node1->left);
                que.push(node2->left);
            }
            if(node1->right != NULL && node2->right != NULL){
                que.push(node1->right);
                que.push(node2->right);
            }
            if(node1->left == NULL && node2->left != NULL){
                node1->left = node2->left;
            }
            if(node1->right == NULL && node2->right != NULL){
                node1->right = node2->right;
            }
        }
        return t1;
    }
};
```

### leetcode 5 最长回文子串

```c++
class Solution {//动态规划法
public:
    string longestPalindrome(string s) {
        int size = s.size();
        if(size < 2) return s;

        vector<vector<bool>> DP(size+1,vector<bool>(size+1,false));
        int maxlen = 1;
        int begin = 0;
        for(int i = 0;i < size;++i){
            DP[i][i] = true;
        }

        for(int j = 1;j < size;++j){
            for(int i = 0;i < j;++i){
                if(s[i] != s[j])
                    DP[i][j] = false;
                else{
                    if(j - i < 3)
                        DP[i][j] = true;
                    else
                        DP[i][j] = DP[i+1][j-1];
                }

                if(DP[i][j] == true && j-i+1 > maxlen){
                    maxlen = j-i+1;
                    begin = i;
                }
            }
        }
        return s.substr(begin,maxlen);
    }
};

class Solution {//中心扩散法
public:
    string longestPalindrome(string s) {
        int size = s.size();
        if(size < 2) return s;

        int maxlen = 1;
        string res = s.substr(0,1);
        for(int i = 0;i < size-1;++i){

            string str1 = centertoedge(s,i,i);
            string str2 = centertoedge(s,i,i+1);
            string maxsubstr = str1.size() > str2.size() ? str1 : str2;
            if(maxsubstr.size() > maxlen){
                res = maxsubstr;
                maxlen = maxsubstr.size();
            }
        }
        return res;
    }
    string centertoedge(string s,int left,int right){
        int i = left;
        int j = right;
        while(i >= 0 && j < s.size()){
            if(s[i] == s[j]){
                --i;
                ++j;
            }
            else{
                break;
            }
        }
        return s.substr(i+1,j-i-1);
    }
};
```

