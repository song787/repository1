### JZ65-矩阵中的路径-较难

```c++
//3ms	384K
class Solution {
public:
    bool hasPath(char* matrix, int rows, int cols, char* str)
    {
        vector<int> dir = {1,-1,cols,-cols};
        for(int i = 0;i < rows;i++)
            for(int j = 0;j < cols;j++){
                bool flag = dfs(matrix+i*cols+j,str,dir);
                if(flag == true)
                    return true;
            }
        return false;              
    }
    
    bool dfs(char* matrix, char* str, vector<int>& dir){
        if(*matrix != *str)
            return false;
        if(*(str+1) == '\0')
            return true;        
        char temp = *matrix;
        *matrix = '0';
        for(auto ele:dir){
            char* next = matrix+ele;
            if(next == nullptr)
                continue;
            if(dfs(next,str+1,dir))
                return true;            
        }
        *matrix = temp;
        return false;
    }
};
/*dfs + 回溯
- dfs是一种写法，dfs+回溯是另一种写法；
- 注意记录已经遍历过的节点，方法主要有（利用一个数组记录已经遍历过的节点的坐标，在回溯的时候注意坐标也要更新，另一种就是修改已经遍历过的元素的值，改为一个特殊值，在后序遍历中如果遇到就跳过，同样的在回溯的时候要将原值重新赋回去；）
*/
```





### JZ31-整数中1出现的次数（从1到n整数中1出现的次数）

```c++
//3ms 488k
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        if(n < 1) return 0;
        int count = 0;
        for(int i = 0;i <= n;i++){
            int temp = i;
            while(temp){
                if(temp % 10 == 1)
                    count ++;
                temp /= 10;
            }        
        }
        return count;
    }
};
/*
计算整数中1出现的次数，121 这种整数算作是1出现了两次；
主要就是通过取余数来计算，通过不断的模10，得到余数，判断是否是1，是1就++；然后再通过除法来把最低位剪枝；
*/

//4ms 356k
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        int count = 0;
        for(int i = 1;i <= n;i *= 10){
            int temp = 0;
            
            int k = n % (i*10);
            temp = n / (i*10) * i;
            
            if(k > i*2-1)
                temp += i;
            else if(k < i)
                temp += 0;
            else
                temp += k-i+1;         
            count += temp;
        }
        return count;
    }
};
/*
我们知道在个位数上，1会每隔10出现一次，例如1、11、21等等，我们发现以10为一个阶梯的话，每一个完整的阶梯里面都有一个1，例如数字22，按照10为间隔来分三个阶梯，在完整阶梯0-9，10-19之中都有一个1，但是19之后有一个不完整的阶梯，我们需要去判断这个阶梯中会不会出现1，易推断知，如果最后这个露出来的部分小于1，则不可能出现1（这个归纳换做其它数字也成立）。

我们可以归纳个位上1出现的个数为：

n/10 * 1+(n%10!=0 ? 1 : 0)

十位
现在说十位数，十位数上出现1的情况应该是10-19，依然沿用分析个位数时候的阶梯理论，我们知道10-19这组数，每隔100出现一次，这次我们的阶梯是100，例如数字317，分析有阶梯0-99，100-199，200-299三段完整阶梯，每一段阶梯里面都会出现10次1（从10-19），最后分析露出来的那段不完整的阶梯。我们考虑如果露出来的数大于19，那么直接算10个1就行了，因为10-19肯定会出现；如果小于10，那么肯定不会出现十位数的1；如果在10-19之间的，我们计算结果应该是k - 10 + 1。例如我们分析300-317，17个数字，1出现的个数应该是17-10+1=8个。

那么现在可以归纳：十位上1出现的个数为：

设k = n % 100，即为不完整阶梯段的数字
归纳式为：(n / 100) * 10 + (if(k > 19) 10 else if(k < 10) 0 else k - 10 + 1)
百位
现在说百位1，我们知道在百位，100-199都会出现百位1，一共出现100次，阶梯间隔为1000，100-199这组数，每隔1000就会出现一次。这次假设我们的数为2139。跟上述思想一致，先算阶梯数 * 完整阶梯中1在百位出现的个数，即n/1000 * 100得到前两个阶梯中1的个数，那么再算漏出来的部分139，沿用上述思想，不完整阶梯数k199，得到100个百位1，100<=k<=199则得到k - 100 + 1个百位1。

那么继续归纳百位上出现1的个数：

设k = n % 1000
归纳式为：(n / 1000) * 100 + (if(k >199) 100 else if(k < 100) 0 else k - 100 + 1)
后面的依次类推....

再次回顾个位
我们把个位数上算1的个数的式子也纳入归纳式中

k = n % 10
个位数上1的个数为：n / 10 * 1 + (if(k > 1) 1 else if(k < 1) 0 else k - 1 + 1)
完美！归纳式看起来已经很规整了。 来一个更抽象的归纳，设i为计算1所在的位数，i=1表示计算个位数的1的个数，10表示计算十位数的1的个数等等。

k = n % (i * 10)
count(i) = (n / (i * 10)) * i + (if(k > i * 2 - 1) i else if(k < i) 0 else k - i + 1)
好了，这样从10到10的n次方的归纳就完成了。

sum1 = sum(count(i))，i = Math.pow(10, j), 0<=j<=log10(n)
但是有一个地方值得我们注意的，就是代码的简洁性来看，有多个ifelse不太好，能不能进一步简化呢？ 我们可以把后半段简化成这样，我们不去计算i * 2 - 1了，我们只需保证k - i + 1在[0, i]区间内就行了，最后后半段可以写成这样

min(max((n mod (i*10))−i+1,0),i)
*/
```



### JZ63 数据流中的中位数

```c++
//运行时间：3ms   占用内存：504k

class Solution {
public:
    void Insert(int num)
    {
        nums.push_back(num);
    }
    double GetMedian()
    { 
        int size = nums.size();
        int i = 0,j = 0;
        bubbleSort(nums);
        while(j != size-1 && j != size){
            i++;
            j+=2;
        }
        if(size % 2 == 0)
            return (double)(nums[i-1]+nums[i])/2;
        else
            return nums[i];        
    }
    void bubbleSort(vector<int>& nums){
        int lo = 0;
        int hi = nums.size();
        while(lo < hi){
            int last = lo;
            int move = lo;
            while(++move < hi)
                if(nums[move-1] > nums[move]){
                    last = move;
                    swap(nums[move-1],nums[move]);
                }
            hi = last;
        }
    }  
private:
    vector<int> nums;
};
//容器采用 vector ，排序算法用的是bubblesort；查找中位数用的是快慢指针；

//运行时间：3ms 占用内存：504k
class Solution {
public:
    void Insert(int num)
    {
        nums.push_back(num);
    }
    double GetMedian()
    { 
        int size = nums.size();
        int i = 0，j = 0;
        mergeSort(nums,0,size);
        while(j != size-1 && j != size){
            i++;
            j+=2;
        }
        if(size % 2 == 0)
            return (double)(nums[i-1]+nums[i])/2;
        else
            return nums[i];        
    }
    void mergeSort(vector<int>& nums,int lo,int hi){
        if(hi - lo < 2) return ;
        int mid = lo + (hi - lo) / 2;
        mergeSort(nums,lo,mid);
        mergeSort(nums,mid,hi);
        merge1(nums,lo,mid,hi);        
    }
    void merge1(vector<int>& nums,int lo,int mid,int hi){
        int hulf_size = mid - lo;
        vector<int> temp_vec;
        for(int i = lo;i < mid;i++)
            temp_vec.push_back(nums[i]);
        for(int i = lo,j = 0,k = mid;(j < hulf_size || k < hi);){
            if((j < hulf_size) && (k >= hi || temp_vec[j] <= nums[k]))
                nums[i++] = temp_vec[j++];
            if((k < hi) && (j >= hulf_size || temp_vec[j] > nums[k]))
                nums[i++] = nums[k++];
        }
    }
private:
    vector<int> nums;
};
//容器采用 vector ， 排序算法为归并排序 ， 中位数用的快慢指针；
```

### JZ42 和为S的两个数字

```c++
//运行时间：3ms  占用内存：376k

class Solution {
public:
    vector<int> FindNumbersWithSum(vector<int> array,int sum) {
        if(array.empty()) return {};
        vector<int> res;
        int multi = INT_MAX;
        int i = 0;
        int j = array.size()-1;
        while(i < j){
            if(array[i] + array[j] == sum && array[i] * array[j] < multi){
                multi = array[i] * array[j];
                res.resize(0);
                res.push_back(array[i++]);
                res.push_back(array[j--]);
            }
            else if(array[i] + array[j] < sum)
                ++i;
            else 
                --j;
        }
        return res;
    }
};

//双指针，从两端往中间逼近
```

### JZ47 求1+2+3...+n

```C++
//运行时间：2ms 占用内存：492k

class Solution {
public:
    int Sum_Solution(int n) {      
        int ans = n;
        ans && (ans += Sum_Solution(n-1));  //逻辑与的 短路 特性；
        return ans;
    }
};

```

### JZ12 数值的整数次方

```c++
//运行时间：3ms  占用内存：376k

class Solution {
public:
    double Power(double base, int exponent) {
        if(exponent == 0) return 1;
        double res = base;
        for(int i = abs(exponent)-1;i > 0;i--){
            res *= base;
        }
        if(exponent > 0)
            return res;
        else
            return 1/res;
    }
};

//首先判断指数是否为0，如果为0 直接返回1；然后取指数的绝对值，循环求得乘方结果，如果指数是大于零的，直接返回结果，如果指数是小于零的，返回结果分之一；
```

### JZ11 二进制中1的个数（*）

```c++
//运行时间：3ms  占用内存：376k
class Solution {
public:
     int  NumberOf1(int n) {
         int count = 0;
         int flag = 1;
         while(flag != 0){
             if((flag & n) != 0)
                 ++count;
             flag = flag << 1;
         }
         return count;
     }
};

//运行时间：2ms  占用内存：376k
class Solution {
public:
     int  NumberOf1(int n) {
         int count = 0;
         while(n != 0){
             ++count;
             n = n & (n-1);
         }
         return count;
     }
};

//可能陷入死循环的方法是因为>>右移是带符号位右移的导致，右移空出来的位子都是1所以进入死循环。如果把n=n>>1改成n=n>>>1就不会进入死循环了。
```

### JZ49 把字符串转换成整数（不是中缀表达式问题） 

```C++
//运行时间：4ms  占用内存：488k

class Solution {
public:
    int StrToInt(string str) {
        if(str.empty()) return 0;
        vector<int> temp;
        bool flag = true;
        for(int i = 0;i < str.size();++i){
            if(str[i] >= 'a' && str[i] <= 'z')
                return 0;
            if(str[i] == '-')
                flag = false;
            if(str[i] >= '0' && str[i] <= '9'){
                char ch = str[i] - '0';
                temp.push_back((int)ch);
            }
        }
        int res = 0;
        int size = temp.size()-1;
        for(int i = 0;i < temp.size();++i,--size){
            res += temp[i] * pow(10,size);
        }
        if(!flag)
            return -res;
        else
            return res;        
    }
};

//就是多了一些非法字符 以及 在开始给了正负号的 一个字符串转换为int型的问题，有非法字符就返回0；

//优化：
//运行时间：3ms  占用内存：380k
//把符号的判断移除循环，因为本题中的符号只会出现在开头位置；
```

### JZ50 数组中重复的数字

```c++
//运行时间：2ms  占用内存：604k
class Solution {
public:
    // Parameters:
    //        numbers:     an array of integers
    //        length:      the length of array numbers
    //        duplication: (Output) the duplicated number in the array number
    // Return value:       true if the input is valid, and there are some duplications in the array number
    //                     otherwise false
    bool duplicate(int numbers[], int length, int* duplication) {
        
        map<int,int> mymap;
        for(int i = 0;i < length;++i){
            mymap[numbers[i]]++;
            if(mymap[numbers[i]] > 1){
                *duplication = numbers[i];
                return true;
            }
        }
        return false;       
    }
};
```

### JZ40 数组中只出现一次的数字

```c++
//运行时间：2ms  占用内存：584k

class Solution {
public:
    void FindNumsAppearOnce(vector<int> data,int* num1,int *num2) {
        if(data.empty()) return ;
        quickSort(data,0,data.size());
        cout<<data[0]<<" "<<data[1]<<" "<<data[2]<<" "<<data[3]<<endl;
        vector<int> res;
        for(int i = 0,j = 1;i < data.size() && j <= data.size();++i,++j){
            if(j == data.size())
                res.push_back(data[i]);
            else{
                if(data[i] != data[j])
                    res.push_back(data[i]);
                else{
                    ++i;
                    ++j;
                }
            }
        }
        *num1 = res[0];
        *num2 = res[1];    
    }
private:
    void quickSort(vector<int>& nums,int lo,int hi){
        if(hi - lo < 2) return ;
        int pivot = pratition(nums,lo,hi);
        quickSort(nums,lo,pivot);
        quickSort(nums,pivot+1,hi);
    }
    
    int pratition(vector<int>& nums,int lo,int hi){
        swap(nums[lo],nums[lo+rand()%(hi-lo)]);
        int mid = lo;
        int pivot = nums[lo];
        for(int k = lo+1;k < hi;k++){
            if(nums[k] < pivot)
                swap(nums[++mid],nums[k]);
        }
        swap(nums[mid],nums[lo]);
        return mid;
    }
};
//先利用快排对数组进行一次排序，然后用双指针，紧挨着一前一后，如果两个元素相同则都移动两步，如果两个元素不同，则都移动一步；
```

### JZ37 数字在排序数组中出现的次数

```c++
//运行时间：3ms  占用内存：376k
class Solution {
public:
    int GetNumberOfK(vector<int> data ,int k) {
        if(data.empty()) return 0;
        int count = 0;
        for(int i = 0;i < data.size();++i){
            if(data[i] == k)
                ++count;
        }
        return count;
    }
};

//运行时间：2ms  占用内存：476k
//利用二分查找分别找k值的最左边位置和最后边位置，然后相减得到数量；
class Solution {
public:
    int GetNumberOfK(vector<int> data ,int k) {
        if(data.empty()) return 0;
        int left = binsearch_left(data,k);
        int right = binsearch_right(data,k);
        return right - left + 1;
    }
    int binsearch_left(vector<int>& data,int k){
        int lo = 0;
        int hi = data.size();
        while(lo < hi){
            int mid = lo + (hi -lo) / 2;
            if(data[mid] < k)
                lo = mid + 1;
            else
                hi = mid;             
        }
        return lo;
    }
    int binsearch_right(vector<int>& data,int k){
        int lo = 0;
        int hi = data.size();
        while(lo < hi){
            int mid = lo + (hi - lo) / 2;
            if(data[mid] <= k)
                lo = mid + 1;
            else
                hi = mid;
        }
        return lo - 1;
    }
};
```

### JZ30 连续子数组的最大和

```c++
//运行时间：2ms  占用内存：488k

class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> array) {
        if(array.size() == 1) return array[0];
        int size = array.size();
        int max_value = INT_MIN;
        int sum = 0;
        for(int i = 0;i < size;++i){
            sum += array[i];
            max_value = max(max_value,sum);
            if(sum < 0)
                sum = 0;
        }
        return max_value;
    }
};


```





```c++
#include<iostream>
#inculde<vector>

using namespace std;

int main() {
    
    
    
    return 0;
}

class Aclass{
public:
    
private:
   
}




```

