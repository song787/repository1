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

