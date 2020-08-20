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

