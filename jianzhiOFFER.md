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

### JZ29 最小的K个数

```c++
//运行时间：3ms  占用内存：504k

class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        if(input.empty() || k > input.size()) return {};
        quickSort(input,0,input.size());
        vector<int> res;
        for(int i = 0;i < k;++i)
            res.push_back(input[i]);
        return res;
    }
    void quickSort(vector<int>& nums,int lo,int hi){
        if(hi - lo < 2) return ;
        int mid = partition(nums,lo,hi);
        quickSort(nums,lo,mid);
        quickSort(nums,mid+1,hi);        
    }
    int partition(vector<int>& nums,int lo,int hi){
        swap(nums[lo],nums[lo+rand()%(hi - lo)]);
        int mid = lo;
        int pivot = nums[lo];
        for(int k = lo + 1;k < hi;++k){
            if(nums[k] < pivot)
                swap(nums[++mid],nums[k]);
        }
        swap(nums[mid],nums[lo]);
        return mid;
    }
};
//利用快排对数组进行排序，然后按照顺序输出K个最小的数字即可；

```

### JZ数组中出现次数超过一般的数字

```c++
//运行时间：2ms  占用内存：476k

class Solution {
public:
    int MoreThanHalfNum_Solution(vector<int> numbers) {
        if(numbers.empty()) return 0;
        int count = 0;
        int target;
        for(int k = 0;k < numbers.size();++k){
            if(count == 0){
                target = numbers[k];
                count = 1;
            }
            else
                target == numbers[k] ? ++count : --count;
        }
        int num = 0;
        for(int k = 0;k < numbers.size();++k)
            if(numbers[k] == target)
                ++num;
        if(num > numbers.size()/2)
            return target;
        else
            return 0;
    }
};
//求众数；剪枝思想；

```

### JZ1 二维数组中的查找

```c++
//8ms	1444KB

class Solution {
public:
    bool Find(int target, vector<vector<int> > array) {
        int m = array.size();
        int n = array[0].size();
        int res = -1;
        for(int i = 0;i < m && res == -1;i ++){
            if(array[i][0] <= target){
                int res = binsearch(array[i],target,n);
                if(res !=  -1)
                    return true;
            }
        }
        return false;
    }
    int binsearch(vector<int>& nums,int target,int n){
        int lo = 0;
        int hi = n;
        while(lo < hi){
            int mid = lo + (hi - lo) / 2;
            if(nums[mid] == target)
                return mid;
            else if(nums[mid] > target)
                hi = mid;
            else
                lo = mid + 1;
        }
        return -1;
    }
};
```

### JZ13 调整数组顺序使奇数位于偶数前面

```c++
//运行时间：2ms 占用内存：376k

class Solution {
public:
    void reOrderArray(vector<int> &array) {
        if(array.empty()) return ;
        int last = array.size()-1;
        for(int i = 0;i < last;++i)
            for(int j = 0;j < last;++j)
                if(iseven(array[j]) && !iseven(array[j+1]))
                   swap(array[j],array[j+1]);
    }
    bool iseven(int num){
        if(num % 2 == 0)
            return true;
        else
            return false;
    }
};
//借用冒泡排序的思想，遇到偶数在前奇数在后的数对就要swap
```

### JZ32 把数组排成最小的数

```c++
//运行时间：3ms  占用内存：496k

class Solution {
public:
    string PrintMinNumber(vector<int> numbers) {
        if(numbers.empty()) return "";
        sort(numbers.begin(),numbers.end(),mycmp());
        string res;
        
        for(int i = 0;i < numbers.size();++i){
            res += to_string(numbers[i]);
        }
        return res;
    }
    class mycmp{
        public:
        bool operator()(int num1,int num2){
            string temp1 = to_string(num1) + to_string(num2);
            string temp2 = to_string(num2) + to_string(num1);
            if(temp1 < temp2)
                return true;
            else
                return false;
        }
    };
};

//利用函数 to_string(); 
//std::to_string是C++标准(2011年)的最新版本中引入的功能。将数值转化为字符串。返回对应的字符串。
```

### JZ66 机器人的运动范围

```c++
//运行时间：2ms  占用内存：504k

class Solution {
private:
    vector<pair<int,int>> dir = {{0,1},{0,-1},{1,0},{-1,0}};
    int maxnum = 0;
public:
    int movingCount(int threshold, int rows, int cols)
    {
        vector<vector<int>> flag(rows+1,vector<int>(cols+1,0));
        dfs(0,0,threshold,rows,cols,flag);
        return maxnum;
    }
    void dfs(int cur_rows,int cur_cols,int& threshold,int& rows,int& cols,vector<vector<int>>& flag){
        int temp_rows = cur_rows;
        int temp_cols = cur_cols;
        int sum = 0;
        while(temp_rows || temp_cols){
            sum += (temp_rows%10 + temp_cols%10);
            temp_rows /= 10;
            temp_cols /= 10;
        }
        if(sum > threshold)
            return ;
        maxnum++;
        flag[cur_rows][cur_cols] = 1;
        for(auto ele:dir){
            int next_i = cur_rows + ele.first;
            int next_j = cur_cols + ele.second;
            if(next_i >= rows || next_i < 0 || next_j >= cols || next_j < 0 || flag[next_i][next_j])
                continue;
            dfs(next_i,next_j,threshold,rows,cols,flag);
        }
    }
};

//DFS



```

### JZ64 滑动窗口的最大值

```c++
//运行时间：2ms  占用内存：376k

class Solution {
public:
    vector<int> maxInWindows(const vector<int>& num, unsigned int size)
    {
        if(size > num.size() || size == 0) return {};
        vector<int> res;
        for(int i = size;i <= num.size();++i){
            res.push_back(max_num(num,i-size,i));
        }
        return res;
    }
    int max_num(vector<int> num , int lo, int hi ){
        int maxvalue = 0;
        for(int i = lo;i < hi;++i)
            maxvalue = max(maxvalue,num[i]);
        return maxvalue;
    }
};

```

### JZ19 顺时针打印矩阵

```c++
//3ms	396KB

class Solution {
public:
    vector<int> printMatrix(vector<vector<int> > matrix) {
        int left_i = 0;
        int left_j = 0;
        int right_i = matrix.size() - 1;
        int right_j = matrix[0].size() - 1;
        vector<int> res;      
        while(left_i <= right_i && left_j <= right_j){
            for(int i = left_j;i <= right_j;++i)
                res.push_back(matrix[left_i][i]);
            for(int i = left_i + 1;i < right_i;++i)
                res.push_back(matrix[i][right_j]);
            for(int i = right_j;i >= left_j && (left_i != right_i);--i)
                res.push_back(matrix[right_i][i]);
            for(int i = right_i - 1;i > left_i && (left_j != right_j);--i)
                res.push_back(matrix[i][left_j]);
            ++left_i;++left_j;
            --right_i;--right_j;
        }
        return res;
    }
};

//利用左上角顶点与右下角顶点实现定位，围绕着这两个点构成的矩阵来遍历；
```

### JZ 数组中的逆序对

```c++
//运行时间：108ms   占用内存：4268k

class Solution {
public:
    int InversePairs(vector<int> data) {
        int res = mergeSort(data,0,data.size());
        return res;
    }
    int mergeSort(vector<int>& nums, int lo, int hi){
        if(hi-lo < 2) return 0;
        int mid = lo + (hi-lo)/2;
        int left = mergeSort(nums,lo,mid);
        int right = mergeSort(nums,mid,hi);
        int num = merge(nums,lo,mid,hi);
        return (left+right+num)%1000000007;
    }
    int merge(vector<int>& nums,int lo,int mid,int hi){
        int count = 0;
        int size = mid - lo;
        vector<int> temp_nums;
        for(int i = lo;i < mid;i++)
            temp_nums.push_back(nums[i]);
        for(int i = lo,j = 0,k = mid;(j < size) || (k < hi);){
            if((j < size) && (k >= hi || (temp_nums[j] <= nums[k])))
                nums[i++] = temp_nums[j++];
            if((k < hi) && (j >= size || (nums[k] < temp_nums[j]))){
                nums[i++] = nums[k++];
                count += size - j;
                if(count >= 1000000007)
                    count %= 1000000007;
            }  
        }
        return count;
    }
};
```

### JZ2 替换空格

```c++
//运行时间：4ms  占用内存：504k

class Solution {
public:
	void replaceSpace(char *str,int length) {
        char * ptr = str;
        for(int i = 0;i < length;++i)
            if(ptr[i] == ' '){
                for(int j = length-i-1;j > 0;--j)
                    ptr[i+j+2] = ptr[i+j];
                ptr[i] = '%';
                ptr[i+1] = '2';
                ptr[i+2] = '0';                
            }
    }
};

//遇到空格就把空格后面的字符串整体后移两位，然后将空格处修改为%20；
```

### JZ43 左旋转字符串

```c++
//运行时间：4ms  占用内存：376k

class Solution {
public:
    string LeftRotateString(string str, int n) {
        
        if(str.empty()) return "";
        reverse(str.begin(),str.begin()+n);
        reverse(str.begin()+n,str.end());
        reverse(str.begin(),str.end());
		return str;
    }
};



```

### JZ54 字符流中第一个不重复的字符

```c++
//运行时间：3ms  占用内存：584k

class Solution
{
public:
  //Insert one char from stringstream
    void Insert(char ch)
    {
         str += ch;
    }
  //return the first appearence once char in current stringstream
    char FirstAppearingOnce()
    {
        map<char,int> mymap;
        for(auto ele:str)
            mymap[ele]++;
        vector<char> vec;
        for(auto ele:mymap)
            if(ele.second == 1)
                vec.push_back(ele.first);
        if(vec.empty()) return '#';
        int min_index = INT_MAX;
        char res;
        for(auto ele:vec){
            int index = str.find(ele);
            if(index < min_index){
                min_index = index;
                res = ele;
            }                
        }
        return res;
    }
    
private:
    string str;
};

```

### JZ34 第一个只出现一次的字符

```c++
//运行时间：4ms  占用内存：376k

class Solution {
public:
    int FirstNotRepeatingChar(string str) {
        if(str.empty()) return -1;
        
        map<char,int> mymap;
        for(auto ele:str)
            mymap[ele]++;
        vector<char> vec;
        for(auto ele:mymap){
            if(ele.second == 1)
                vec.push_back(ele.first);
        }
        if(vec.empty()) return -1;
        int min_index = INT_MAX;
        for(auto ele:vec){
            int index = str.find(ele);
            min_index = min(index,min_index);
        }
        return min_index;
    }
};

//利用map实现str元素出现次数的计数，把所有出现次数为1的char放入vector中，然后依次str.find(ele)，记录最小的下标；
```

### JZ53 表示数值的字符串*

```c++
//运行时间：3ms  占用内存：388k

class Solution {
public:
    bool isNumeric(char* string)
    {
        bool hasE = false, sign = false, decimal = false;
        for(int i = 0;i < strlen(string);++i){
            if(string[i] == 'e' || string[i] == 'E'){
                if(i == strlen(string)-1) return false;
                if(hasE) return false;
                hasE = true;
            }
            else if(string[i] == '-' || string[i] == '+'){
                if(sign && string[i-1] != 'e' && string[i-1] != 'E') return false;
                if(!sign && i != 0 && string[i-1] != 'e' && string[i-1] != 'E') return false;
                sign = true;
            }
            else if(string[i] == '.'){
                if(decimal || hasE) return false;
                decimal = true;
            }
            else if(string[i] < '0' || string[i] > '9')
                return false;
        }
        return true;
    }
};
```

### JZ45 扑克牌顺子

```c++
//运行时间：3ms  占用内存：376k

class Solution {
public:
    bool IsContinuous( vector<int> numbers ) {
        if(numbers.empty()) return false;
        sort(numbers.begin(),numbers.end());
        int count_0 = 0;
        int value_not0 = 0;
        int last_not0 = 0;
        int first_not0 = INT_MAX;
        for(auto ele:numbers)
            if(ele == 0)
                ++count_0;
            else{
                if(ele == last_not0)
                    return false;
                else{
                    first_not0 = min(first_not0,ele);
                    value_not0 += (ele - last_not0 - 1);
                    last_not0 = ele;
                }
            }
        value_not0 -= (first_not0 - 1);
        if(count_0 >= value_not0) return true;
        else return false;
    }
};

```

### JZ44 反转单词顺序列

```c++
//运行时间：4ms  占用内存：488k

class Solution {
public:
    string ReverseSentence(string str) {
        if(str.empty()) return "";
        vector<int> index;
        index.push_back(-1);
        int size = str.size();
        for(int i = 0;i < size;++i)
            if(str[i] == ' ')
                index.push_back(i);
        index.push_back(size);
        for(int i = 0;i < index.size()-1;++i)
            reverse(str.begin()+index[i]+1,str.begin()+index[i+1]);
        reverse(str.begin(),str.end());
        return str;
    }
};

//类似于JZ43左旋转字符串，对字符串的子串做反转，然后对整个字符串做反转；
```

### JZ27 字符串的排列

```c++
//运行时间：5ms  占用内存：488k

class Solution {
private:
    vector<string> res;  
public:
    vector<string> Permutation(string str) {
        
        if(str.empty()) return {};
        vector<int> trace(str.size(),0);        
        string str_cur;
        backtrace(str,trace,res,str_cur);
        sort(res.begin(),res.end());
        return res;
    }
    void backtrace(string str,vector<int>& trace,vector<string>& res, string str_cur){
        if(str_cur.size() > str.size())
            return ;
        else if(str.size() == str_cur.size()){
            if(find(res.begin(),res.end(),str_cur) == res.end())
                res.push_back(str_cur);
            return ;
        }
        for(int i = 0;i < str.size();++i){
            if(trace[i] == 1) continue;
            trace[i] = 1;
            str_cur += str[i];
            backtrace(str,trace,res,str_cur);
            str_cur.pop_back();
            trace[i] = 0;            
        }
    }
};

//利用回溯的方式完成str中元素的排列组合，push到vector容器中，然后sort字典排序；
```

### JZ52 正则表达式匹配

```c++
//运行时间：3ms  占用内存：376k

class Solution {
public:
    bool match(char* str, char* pattern)
    {
        int i = 0,j = 0;
        int size_str = strlen(str),size_pattern = strlen(pattern);
        if(size_str == 0 && size_pattern == 0) return true;
        while(j < size_pattern){
            if(i == size_str)
                if(j+1 == size_pattern-1 && pattern[j+1] == '*')
                    return true;
                else
                    return false;
            if(j != size_pattern - 1 && pattern[j+1] == '*')
                if(str[i] != pattern[j] && pattern[j] != '.')//*前一位不匹配且不等于'.'
                    j += 2;
                else if(pattern[j] == '.'){//*前一位不匹配但是等于'.'
                    j += 2;
                    while(i != size_str && str[++i] != pattern[j]);
                }
                else{//*前一位是与str[i]想匹配的
                    return match(str+i,pattern+j+2) || match(str+i+1,pattern) || match(str+i+1,pattern+j+2);
                }
            else if(pattern[j] == '.' || str[i] == pattern[j]){
                ++i;
                ++j;
            }
            else
                return false;
        }
        if(i == size_str) return true;
        else return false;
    }
};
```

### JZ15 反转链表

```c++
//运行时间：3ms  占用内存：472k

/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {//利用stack作为辅助容器
public:
    ListNode* ReverseList(ListNode* pHead) {
        stack<int> vec;
        ListNode* ptr = pHead;
        while(pHead != nullptr){
            vec.push(pHead->val);
            pHead = pHead->next;            
        }            
        pHead = ptr;
        while(!vec.empty()){
            pHead->val = vec.top();
            vec.pop();
            pHead = pHead->next;
        }
        return ptr;
    }
};

//运行时间：3ms  占用内存：488k

/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {//手动构造当前节点的pre节点与next节点；
public:
    ListNode* ReverseList(ListNode* pHead) {
        if(pHead == nullptr) return nullptr;
        ListNode* pre = nullptr;
        ListNode* next = nullptr;
        while(pHead != nullptr){
            next = pHead->next;
            pHead->next = pre;
            pre = pHead;
            pHead = next;            
        }
        return pre;
    }
};
```

### JZ14 链表中倒数第K个节点

```c++
//运行时间：3ms  占用内存：504k

/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
        if(pListHead == nullptr) return nullptr;
        ListNode* first = pListHead;
        ListNode* second = pListHead;
        for(int i = 0;i < k;++i){
            if(second == nullptr)
                return nullptr;
            second = second->next;
        }
        while(second != nullptr){
            second = second->next;
            first = first->next;
        }
        return first;            
    }
};

//双指针完成，second指针先移动k位，然后双指针同步后移，当second移动到nullptr的时候，first指针所在位置就是倒数第K个节点；
```

### JZ16 合并两个排序的链表

```c++
//运行时间：3ms 占用内存：376k

/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {//非递归解法
public:
    ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
    {
        if(pHead1 == nullptr || pHead2 == nullptr) 
            return pHead1 == nullptr ? pHead2 : pHead1;
        ListNode* resHead = new ListNode(0);
        ListNode* ptr = resHead;
        while(pHead1 != nullptr && pHead2 != nullptr){
            if(pHead1->val <= pHead2->val){
                ptr->next = new ListNode(pHead1->val);
                pHead1 = pHead1->next;
            }
            else{
                ptr->next = new ListNode(pHead2->val);
                pHead2 = pHead2->next;
            }
            ptr = ptr->next;
        }
        ptr->next = pHead1 == nullptr ? pHead2 : pHead1;
        return resHead->next;
    }
};
//指针一定要初始化，否则总是报段错误；


//运行时间：3ms  占用内存：456k

/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {//递归解法
public:
    ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
    {
        if(pHead1 == nullptr || pHead2 == nullptr) 
            return pHead1 == nullptr ? pHead2 : pHead1;
        if(pHead1->val <= pHead2->val){
            pHead1->next = Merge(pHead1->next,pHead2);
            return pHead1;
        }
        else{
            pHead2->next = Merge(pHead1,pHead2->next);
            return pHead2;
        }
    }
};
```

### JZ36 两个链表的第一个公共节点*

```c++
//解法一：不记录长度
//运行时间：3ms  占用内存：504k
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        
        ListNode* first = pHead1;
        ListNode* second = pHead2;
        while(first != second){
            first = first == nullptr ? pHead2 : first->next;
            second = second == nullptr ? pHead1 : second->next;
        }
        return first;
    }
};
//长度相同有公共结点，第一次就遍历到；没有公共结点，走到尾部NULL相遇，返回NULL
//长度不同有公共结点，第一遍差值就出来了，第二遍一起到公共结点；没有公共，一起到结尾NULL。

//解法二：记录长度

```

### JZ55 链表中环的入口节点

```c++
//运行时间：2ms  占用内存：492k

/*
struct ListNode {
    int val;
    struct ListNode *next;
    ListNode(int x) :
        val(x), next(NULL) {
    }
};
*/
class Solution {
public:
    ListNode* EntryNodeOfLoop(ListNode* pHead)
    {
        if(pHead->next == pHead) return pHead;
        ListNode* first = pHead->next;
        if(first == nullptr)
            return nullptr;
        ListNode* second = pHead->next->next;
        while(first != second){
            first = first->next;
            if(second == nullptr || second->next == nullptr)
                return nullptr;
            second = second->next->next;
        }
        second = pHead;
        while(second != first){
            first = first->next;
            second = second->next;
        }
        return first;
    }
};
```

### JZ46 圆圈中最后剩下的数

```c++
//运行时间：17ms  占用内存：504k

class Solution {
public:
    int LastRemaining_Solution(int n, int m)
    {
        if(m == 0 || n == 0)return -1;
        list<int> child;
        for(int i = 0;i < n;++i){
            child.push_back(i);    
        }
        auto it = child.begin();
        while(child.size() > 1){
            int count = 0;
            while(count < m-1){
                ++count;
                ++it;
                if(it == child.end())
                    it = child.begin();
            }
            auto temp_it = ++it;
            if(temp_it == child.end())
                temp_it = child.begin();
            child.erase(--it);
            it = temp_it;
        }
        return child.back();
    }
};
```

### JZ56 删除链表中重复的节点； 

```c++
//运行时间：2ms  占用内存：492k

/*
struct ListNode {
    int val;
    struct ListNode *next;
    ListNode(int x) :
        val(x), next(NULL) {
    }
};
*/
class Solution {
public:
    ListNode* deleteDuplication(ListNode* pHead)
    {
        if(pHead == nullptr) return nullptr;
        ListNode* pre = nullptr;
        ListNode* cur = pHead;
        ListNode* next = pHead->next;
        
        while(next != nullptr){
            if(cur->val == next->val){
                while(next != nullptr && next->val == cur->val)
                    next = next->next;
                if(cur == pHead)
                    pHead = next;
                else
                    pre->next = next;
                cur = next;
                if(next != nullptr)
                    next = next->next;
            }
            else{
                pre = cur;
                cur = next;
                next = next->next;
            }
        }
        return pHead;
    }
};
```

### JZ25 复杂链表的复制**

```c++
//运行时间：3ms  占用内存：488k

/*
struct RandomListNode {
    int label;
    struct RandomListNode *next, *random;
    RandomListNode(int x) :
            label(x), next(NULL), random(NULL) {
    }
};
*/
class Solution {
public:
    RandomListNode* Clone(RandomListNode* pHead)
    {
        if(pHead == nullptr) return nullptr;
        RandomListNode* cur = nullptr;
        RandomListNode* pre = pHead;
        while(pre != nullptr){
            cur = new RandomListNode(pre->label);
            cur->next = pre->next;
            pre->next = cur;
            pre = cur->next;
        }
        
        pre = pHead;
        while(pre != nullptr){
            cur = pre->next;
            cur->random = pre->random == nullptr ? nullptr : pre->random->next;
            pre = cur->next;
        }
        
        cur = pHead;
        RandomListNode* next = nullptr;
        RandomListNode* head = cur->next; 
        while(cur != nullptr){
            next = cur->next;
            cur->next = next->next;
            cur = next->next;
            next->next = cur == nullptr ? nullptr : cur->next;
        }
        return head;        
    }
};
```

### JZ26 二叉搜索树与双向链表**

```c++
//运行时间：2ms  占用内存：380k

/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    TreeNode* Convert(TreeNode* pRootOfTree)
    {
        TreeNode* cur = pRootOfTree;
        TreeNode* pre = nullptr;
        stack<TreeNode*> s;
        bool isFirst = true;
        TreeNode* head = nullptr;
        while(cur != nullptr || !s.empty()){
            while(cur != nullptr){
                s.push(cur);
                cur = cur->left;
            }
            cur = s.top();
            s.pop();
            if(isFirst){
                head = cur;
                pre = cur;
                isFirst = false;
            }
            else{
                pre->right = cur;
                cur->left = pre;
                pre = cur;
            }
            cur = cur->right;
        }
        return head;        
    }
};
```

### JZ4 重建二叉树**

```c++
//运行时间：5ms占用内存：608k  

/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
        TreeNode* root = reConstructBinaryTree(pre,0,pre.size()-1,vin,0,vin.size()-1);
        return root;
    }
private:
    TreeNode* reConstructBinaryTree(vector<int> pre,int startPre,int endPre,vector<int> vin,int startIn,int endIn){
        if(startPre > endPre || startIn > endIn)
            return nullptr;
        TreeNode* root = new TreeNode(pre[startPre]);
        for(int i = startIn;i <= endIn;++i)
            if(vin[i] == pre[startPre]){
                root->left = reConstructBinaryTree(pre,startPre+1,startPre+i-startIn,vin,startIn,i-1);
                root->right = reConstructBinaryTree(pre,startPre+i-startIn+1,endPre,vin,i+1,endIn);
                break;
            }
        return root;
    }
};
```

### JZ17 树的子结构

```c++
//运行时间：2ms  占用内存：480k

/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    bool HasSubtree(TreeNode* pRoot1, TreeNode* pRoot2)
    {
        if(pRoot1 == nullptr || pRoot2 == nullptr)
            return false;
        bool flag = false;
        if(pRoot1->val == pRoot2->val)
            flag = subtree(pRoot1,pRoot2);
        if(!flag)
            flag = HasSubtree(pRoot1->left,pRoot2);
        if(!flag)
            flag = HasSubtree(pRoot1->right,pRoot2);
        return flag;
    }
    bool subtree(TreeNode* root1,TreeNode* root2){
        if(root2 == nullptr)
            return true;
        else if(root1 == nullptr)
            return false;
        if(root1->val != root2->val)
            return false;
        return subtree(root1->left,root2->left) && subtree(root1->right,root2->right);
    }
};
//判断当前节点，如果相等，则判断结构，如果不等则递归自己的左子树与右子树；
```

### JZ22 从上往下打印二叉树

```c++
//运行时间：2ms  占用内存：504k

/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    vector<int> PrintFromTopToBottom(TreeNode* root) {
        if(root == nullptr) return {};
        queue<TreeNode*> que;
        que.push(root);
        vector<int> res;
        while(!que.empty()){
            TreeNode* root = que.front();
            que.pop();
            res.push_back(root->val);
            if(root->left != nullptr)
                que.push(root->left);
            if(root->right != nullptr)
                que.push(root->right);      
        }
        return res;
    }
};
//借助队列，分别访问当前节点，并且把当前节点的左子节点与右子节点入队列，直到队列为空的时候就停止；
```

### JZ23 二叉搜索树的后序遍历序列

```c++
//运行时间：2ms  占用内存：504k

class Solution {
public:
    bool VerifySquenceOfBST(vector<int> sequence) {
        if(sequence.empty())  return false;
        return judge(sequence,0,sequence.size()-1);
    }
    bool judge(vector<int> sequence,int lo,int hi){
        if(lo >= hi) return true;
        int root = sequence[hi];
        int count = lo;
        bool flag = false;
        for(int i = lo;i < hi;++i){
            if(flag == false && sequence[i] < root)
                ++count;
            else{
                flag = true;
                if(sequence[i] < root)
                     return false;       
            }
        }
        return judge(sequence,lo,count-1) && judge(sequence,count,hi-1);       
    }
};
//BST的后序序列的合法序列是，对于一个序列S，最后一个元素是x （也就是根），如果去掉最后一个元素的序列为T
//那么T满足：T可以分成两段，前一段（左子树）小于x，后一段（右子树）大于x，且这两段（子树）都是合法的后序序列。完美的递归定义 : ) 。
```

### JZ24  二叉树中和为某一值的路径

```c++
//运行时间：2ms  占用内存：376k 

/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
private:
    vector<vector<int>> res;
    int sum = 0;
public:
    vector<vector<int> > FindPath(TreeNode* root,int expectNumber) {
        
        if(root == nullptr) return {};
        vector<int> vec;
        backtrace(root,expectNumber,vec);
        return res;
    }
private:
    void  backtrace(TreeNode* curptr, int expectNumber, vector<int> vec){
        vec.push_back(curptr->val);
        sum += curptr->val;
        if(!curptr->left && !curptr->right)
            if(expectNumber == sum)
                res.push_back(vec);
            else{
                sum -= curptr->val;
                vec.pop_back();
                return ;
            }
        if(curptr->left != nullptr)
            backtrace(curptr->left,expectNumber,vec);
        if(curptr->right != nullptr)
            backtrace(curptr->right,expectNumber,vec);
        vec.pop_back();        
        sum -= curptr->val;
    }
};
//思路：DFS+回溯的方式

```

### JZ39  平衡二叉树

```c++
//运行时间：2ms  占用内存：480k

class Solution {
public:
    bool IsBalanced_Solution(TreeNode* pRoot) {
        return tree_deep(pRoot) != -1;
    }
    int tree_deep(TreeNode* root){
        if(root == nullptr)
            return 0;
        int left = tree_deep(root->left);
        if(left == -1) return -1;
        int right = tree_deep(root->right);
        if(right == -1) return -1;
        return abs(right-left) > 1 ? -1 : (max(right,left)+1);
    }
};
//自下往上的遍历；
```

### JZ7 斐波那契数列

```c++
//运行时间：2ms  占用内存：476k

class Solution {
public:
    int Fibonacci(int n) {
        if(n == 0) return 0;
        int pre = 0;
        int cur = 1;
        for(int i = 2;i <= n;++i){
            int temp = cur;
            cur = pre + temp;
            pre = temp;
        }
        return cur;
    }
};
```

### JZ6 旋转数组的最小数字

```c++
class Solution {
public:
    int minNumberInRotateArray(vector<int> rotateArray) {
        if(rotateArray.empty()) return 0;
        
        int lo = 0;
        int hi = rotateArray.size()-1;
        while(lo < hi){
            int mid = lo + (hi - lo) / 2;
            if(rotateArray[mid] > rotateArray[hi])//目前看只能与有边界比
                lo = mid + 1;
            else if(rotateArray[mid] < rotateArray[hi])
                hi = mid;
            else
                --hi;
        }
        return rotateArray[lo];
    }
};
```

### JZ8 跳台阶

```c++
class Solution {
public:
    int jumpFloor(int number) {
        if(number == 0) return 0;
        int pre = 1;
        int cur = 1;
        for(int i = 2;i <= number;++i){
            int temp = cur;
            cur = temp + pre;
            pre = temp;
        }
        return cur;
    }
};
```

### JZ20 包含mini函数的栈

```c++
class Solution {
private:
    stack<int> stack,stack_min;
public:
    void push(int value) {
        if(stack.empty()){
            stack.push(value);
            stack_min.push(value);
        }
        else{
            if(value <= stack.top())
                stack_min.push(value);
            stack.push(value);
        }
    }
    void pop() {
        if(stack.top() == stack_min.top())
            stack_min.pop();
        stack.pop();            
    }
    int top() {
        return stack.top();
    }
    int min() {
        return stack_min.top();
    }
};
```

### JZ21 栈的压入弹出序列

```c++
class Solution {//借助辅助栈
public:
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        if(pushV.empty() && popV.empty()) return true;
        if(pushV.size() != popV.size()) return false;
        stack<int> stack;
        for(int i = 0,j = 0;i < pushV.size();++i){
            stack.push(pushV[i]);
            while(j < popV.size() && stack.top() == popV[j]){
                stack.pop();
                ++j;
            }
        }
        if(stack.empty())
            return true;
        else
            return false;
        
    }
};
```

### JZ 丑数**

```c++
class Solution {
public:
    int GetUglyNumber_Solution(int index) {
        if(index < 7) return index;
        int p2 = 0,p3 = 0,p5 = 0,num = 1;
        vector<int> vec;
        vec.push_back(num);
        while(vec.size() < index){
            num = min(vec[p2] * 2 , min(vec[p3] * 3 , vec[p5] * 5));
            vec.push_back(num);
            if(num == vec[p2]*2) ++p2;
            if(num == vec[p3]*3) ++p3;
            if(num == vec[p5]*5) ++p5;
        }
        return vec.back();
    }
};
/*通俗易懂的解释：
首先从丑数的定义我们知道，一个丑数的因子只有2,3,5，那么丑数p = 2 ^ x * 3 ^ y * 5 ^ z，换句话说一个丑数一定由另一个丑数乘以2或者乘以3或者乘以5得到，那么我们从1开始乘以2,3,5，就得到2,3,5三个丑数，在从这三个丑数出发乘以2,3,5就得到4，6,10,6，9,15,10,15,25九个丑数，我们发现这种方***得到重复的丑数，而且我们题目要求第N个丑数，这样的方法得到的丑数也是无序的。那么我们可以维护三个队列：
（1）丑数数组： 1
乘以2的队列：2
乘以3的队列：3
乘以5的队列：5
选择三个队列头最小的数2加入丑数数组，同时将该最小的数乘以2,3,5放入三个队列；
（2）丑数数组：1,2
乘以2的队列：4
乘以3的队列：3，6
乘以5的队列：5，10
选择三个队列头最小的数3加入丑数数组，同时将该最小的数乘以2,3,5放入三个队列；
（3）丑数数组：1,2,3
乘以2的队列：4,6
乘以3的队列：6,9
乘以5的队列：5,10,15
选择三个队列头里最小的数4加入丑数数组，同时将该最小的数乘以2,3,5放入三个队列；
（4）丑数数组：1,2,3,4
乘以2的队列：6，8
乘以3的队列：6,9,12
乘以5的队列：5,10,15,20
选择三个队列头里最小的数5加入丑数数组，同时将该最小的数乘以2,3,5放入三个队列；
（5）丑数数组：1,2,3,4,5
乘以2的队列：6,8,10，
乘以3的队列：6,9,12,15
乘以5的队列：10,15,20,25
选择三个队列头里最小的数6加入丑数数组，但我们发现，有两个队列头都为6，所以我们弹出两个队列头，同时将12,18,30放入三个队列；
……………………
疑问：
1.为什么分三个队列？
丑数数组里的数一定是有序的，因为我们是从丑数数组里的数乘以2,3,5选出的最小数，一定比以前未乘以2,3,5大，同时对于三个队列内部，按先后顺序乘以2,3,5分别放入，所以同一个队列内部也是有序的；
2.为什么比较三个队列头部最小的数放入丑数数组？
因为三个队列是有序的，所以取出三个头中最小的，等同于找到了三个队列所有数中最小的。
实现思路：
我们没有必要维护三个队列，只需要记录三个指针显示到达哪一步；“|”表示指针,arr表示丑数数组；
（1）1
|2
|3
|5
目前指针指向0,0,0，队列头arr[0] * 2 = 2,  arr[0] * 3 = 3,  arr[0] * 5 = 5
（2）1 2
2 |4
|3 6
|5 10
目前指针指向1,0,0，队列头arr[1] * 2 = 4,  arr[0] * 3 = 3, arr[0] * 5 = 5
（3）1 2 3
2| 4 6
3 |6 9
|5 10 15
目前指针指向1,1,0，队列头arr[1] * 2 = 4,  arr[1] * 3 = 6, arr[0] * 5 = 5
………………
```

### JZ 和为S的连续正数序列

```c++
class Solution {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        if(sum < 3) return {};
        int left = 1,right = 2;
        int size = sum/2 + 1;
        vector<vector<int>> res;
        while(left < right && right <= size){
            if(leftToright(left,right) == sum){
                //vector->vector<vector>>
                vector<int> level;
                int temp = left;
                while(temp <= right)
                    level.push_back(temp++);
                res.push_back(level);
                ++left;
                ++right;
            }
            else if(leftToright(left,right) > sum)
                ++left;
            else if(leftToright(left,right) < sum)
                ++right;
        }
        return res;
    }
    int leftToright(int left,int right){
        int sum = 0;
        for(int i = left;i <= right;++i)
            sum += i;
        return sum;
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

###