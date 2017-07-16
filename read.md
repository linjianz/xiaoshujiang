[toc]

---
# 随便吧
## 2. Add Two Numbers
```
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
        ListNode preHead(0), *p = &preHead;
        int flag = 0;
        while (l1 || l2 || flag) {
            int sum = (l1 ? l1 -> val : 0) + (l2 ? l2 -> val : 0) + flag;
            flag = sum / 10;  // 更新进位
            p -> next = new ListNode(sum % 10);  // 更新加和
            p = p -> next;
            l1 = l1 ? l1 -> next : l1;  // 处理边界
            l2 = l2 ? l2 -> next : l2;
        }
        return preHead.next;
    }
};
```
## 3. Longest Substring Without Repeating Characters
问题：求最长不重复字串的长度  
思路
- 两个“指针”分别指向起点和终点
- 终点指针遍历的同时，从起点开始遍历至终点，无相同元素，则更新maxLen
```
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        if (s.empty()) return 0;
        int p1 = 0, p2 = 1;  // p1为起点下标，p2为终点下标
        int maxLen = p2 - p1;
        for (; p2 < s.size(); p2++) {  // p2遍历后面的元素
            int i = p1;
            for (; i < p2; i++) {  // 遍历当前字串，与终点对比，可以保证所有元素都不重复
                if (s[i] == s[p2]) {
                    p1 = i + 1;
                    break;
                }
            }
            maxLen = max(maxLen, p2 - p1 + 1);  // 更新最大长度
        }
        return maxLen;
    }
};
```
## 5. Longest Palindromic Substring
问题： 求最长回文字串
思路：
- 寻找重复元素，然后从两头进行扩展
- 记录长度以及起始位置，下一次循环时从重复元素末端开始
```
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.size() < 2) return s;
        int i = 0;  // 指示中心位置
        int start, maxLen = 0;
        for (; i < s.size();) {
            if (s.size() - i <= maxLen / 2) break;
            int j = i, k = i;  // 从i开始搜索，找出重复序列[j,k]
            while (k < s.size() - 1 && s[j] == s[k + 1]) k++;
            i = k + 1;  // 更新i，如果j==k，则意味着i++；如果j<k，则意味着i跳过了重复序列
            while (j > 0 && k < s.size() - 1 && s[j - 1] == s[k + 1]) {j--; k++;}  // 扩展: j,k分别指示起点和终点
            int newLen = k - j + 1;
            if (newLen > maxLen) {start = j; maxLen = newLen;}
        }
        return s.substr(start, maxLen);
    }
};
```
## 11. Container With Most Water
问题：找两条竖线使得蓄水面积最大  
思路：用两个指针分别指示两条竖线
```
class Solution {
public:
    int maxArea(vector<int>& height) {
        int max_area = 0, l = 0, r = height.size() - 1;
        while (l < r) {
            max_area = max(max_area, min(height[l], height[r]) * (r - l));
            if (height[l] < height[r]) l++;
            else r--;
        }
        return max_area;
    }
};
```
## 15. 3Sum
利用2sum的思路，两次循环，找出所有符合条件的组合，但是可能存在重复元素，所以要去重  
==当size<=3时，不知道为什么不是输出[]==
```
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());  // 先排序，保证后面的元素内三个数按大小排序
        vector<vector<int>> ret;
        for (int i = 0; i < nums.size() - 2; i++) {
            map<int, int> m;
            for (int j = i + 1; j < nums.size(); j++) {
                if (m.find(-nums[i] - nums[j]) != m.end())
                    // 对应的三个数分别为{nums[i], -nums[i] - nums[j], nums[j]}，按大小顺序
                    ret.push_back({nums[i], -nums[i] - nums[j], nums[j]});
                else
                    m[nums[j]] = i;
            }
        }
        sort(ret.begin(), ret.end());
        ret.erase(unique(ret.begin(), ret.end()), ret.end());
        return ret;
    }
};
```
## 17. Letter Combinations of a Phone Number
### 方法1
转化为4进制，但是存在多余的组合，故最后需要加限制条件
```
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        vector<string> ret;
        if (digits.empty()) return vector<string>();
        string letter[10][4] = 
                 {{"","","",""},
                  {"","","",""},
                  {"a","b","c",""},
                  {"d","e","f",""},
                  {"g","h","i",""},
                  {"j","k","l",""},
                  {"m","n","o",""},
                  {"p","q","r","s"},
                  {"t","u","v",""},
                  {"w","x","y","z"}};
        for (int i = 0; i < pow(4, digits.size()); i++) {
            int index, tmp = i;
            string st;
            for (int j = 0; j < digits.size(); j++) {
                index = tmp % 4; tmp /= 4;  // 转化为4进制
                st += letter[digits[j] - '0'][index];
            }
            if (!st.empty() && st.size() == digits.size()) ret.push_back(st);
        }
        return ret;
    }
};
```
### 方法2
类似与引入队列的思路，每次将数字对应的字母入队，下一个数字对应的字母分别与队列中每个字母进行组合。  
注意：初始时添加空字符，保证添加第一个字母时不报错
```
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        string letters[8] = {"abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
        vector<string> ret;
        if (digits.empty()) return ret;
        ret.push_back("");  // 这一步很重要，保证ret里初始有一个空元素，避免后面添加第一个元素时报错
        for (int i = 0; i < digits.size(); i++) {  // 遍历digits
            vector<string> tmp;  // 暂时存放添加一组字母后ret的结果
            string chars = letters[digits[i] - '0' - 2];
            for (int j = 0; j < chars.size(); j++)  // 遍历digits的每个数字对应的字母
                for (int k = 0; k < ret.size(); k++)
                    tmp.push_back(ret[k] + chars[j]);
            ret = tmp;
        }
        return ret;
    }
};
```
## 21. Merge Two Sorted Lists
类似于==2==，新建一个头结点，并初始化为0，然后利用new来更新值并指向下一个结点。最后一步要注意使用`preHead.next()`来返回头结点。
```
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
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode preHead(0), *p = &preHead;
        while (l1 && l2) {
            if (l1 -> val < l2 -> val) {
                p -> next = new ListNode(l1 -> val);
                p = p -> next;
                l1 = l1 -> next;
            }
            else {
                p -> next = new ListNode(l2 -> val);
                p = p -> next;
                l2 = l2 -> next;
            }
            
        }
        while (l1) {
            p -> next = new ListNode(l1 -> val);
            p = p -> next;
            l1 = l1 -> next;
        }
        while (l2) {
            p -> next = new ListNode(l2 -> val);
            p = p -> next;
            l2 = l2 -> next;
        }
        return preHead.next;
    }
    
    // 简洁版
    ListNode* mergeTwoLists_2(ListNode* l1, ListNode* l2) {
        ListNode preHead(0), *p = &preHead;
        while (l1 && l2) {
            if (l1 -> val < l2 -> val) {
                p -> next = l1;
                l1 = l1 -> next;
            }
            else {
                p -> next = l2;
                l2 = l2 -> next;
            }
            p = p -> next;            
        }
        p -> next = l1 ? l1 : l2;
        return preHead.next;
    }
};
```

# Array
## 4. Median of Two Sorted Arrays
问题：求两个有序数组的中位数  
### 方法1
时间复杂度为O(log(min(m, n)))  
思路
- 把数组1（长度为m）分割为两部分，其中左半部分含有元素个数为i
- 把数组2（长度为n）分割为两部分，其中左半部分含有元素个数为j=((m+n+1)/2-i)
- 如果max(left_part)  <= min(right_part)，那么i和j分割位置正确，此时分情况讨论
    - 如果m+n为偶数，均值即为中位数
    - 如果m+n为奇数，max(left_part)即为中位数
```
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int m = nums1.size(), n = nums2.size();
        if (m > n) return findMedianSortedArrays(nums2, nums1);  // 保证m<=n，防止j为负数
        int i, j, imin = 0, imax = m, half = (m + n + 1) / 2;
        while (imin <= imax) {  // 采用二分法进行搜索
            i = (imin & imax) + ((imin ^ imax) >> 1);  // 求imin和imax的均值，若和为奇数，则只保留整数部分
            j = half - i;
            if (i > 0 && nums1[i - 1] > nums2[j]) imax = i - 1;  // i太大，故向左搜索
            else if (i < m && nums2[j - 1] > nums1[i]) imin = i + 1;  // i太小，故向右搜索
            else break;
        }
        // 返回的i和j为最佳分割位置，此时获得左半支最大数和右半支最小数即可
        int num1;
        if (!i) num1 = nums2[j - 1];  // 如果i为0，则为2的左半支的最大值
        else if (!j) num1 = nums1[i - 1];  // 如果j为0，则为1的左半支的最大值
        else num1 = max(nums1[i - 1], nums2[j - 1]);    // 否则，则为较大者
        if ((m + n) & 1) return num1;  // 如果元素总数为奇数，那直接返回
        
        int num2;
        if (i == m) num2 = nums2[j];
        else if (j == n) num2 = nums1[i];
        else num2 = min(nums1[i], nums2[j]);
        return (num1 + num2) / 2.0;  // 千万注意，可能为小数！
    }
};
```
### 方法2
思路：首先实现求第k小的数的函数，那么中值很容易求得  
求第k小的数的思路：  
- 首先保证数组1更长
- 每次将k的值减半：对比数组1左侧第k/2个元素和数组2左侧第k/2个元素的大小，如果1大则移除2的前k/2个元素；否则移除1的前k/2个元素  

以下代码有点小问题
```
class Solution {
public:
    double kth(vector<int>& nums1, int m, vector<int>& nums2, int n, int k) {
        if (m < n) return kth(nums2, n, nums1, m, k);  // 保证第一个数组长度更长
        if (n == 0) return nums1[k - 1];  // 第二个数组长度为零时，直接返回第一个数组对应元素
        if (k == 1) return min(nums1[0], nums2[0]);  // 二分法终止条件判断
        
        int j = min(n, k / 2);  // 数组2可以忽略的元素个数
        int i = k - j;  // 数组1可以忽略的元素个数
        if (nums1[i - 1] < nums2[j - 1]) return kth(nums1[i:], m - i, nums2, n, k - i)
        return kth(nums1, m, nums2[j:], n - j, k - j);
    }
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int m = nums1.size(), n = nums2.size();
        int k = (m + n) / 2;
        if ((m + n) % 2 == 0) {  // 如果为偶数，则需要求第k个和第k+1个的值
            return (kth(nums1, m, nums2, n, k) + kth(nums1, m, nums2, n, k + 1)) / 2.0;
        }
        else return kth(nums1, m, nums2, n, k + 1);
    }
};
```
## 	First Missing Positive
问题：找出第一个遗漏的正数  
思路
- 第一次循环，将所有正的元素放在正确位置（eg：将2第二个位置），直到当前位置元素为负数或者超出了1~n的范围
- 第二次循环，找出与位置index不等的元素即可；若没有，则返回n+1
```
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();
        for (int i = 0; i < n; i++) {
            while (nums[i] > 0 && nums[i] <= n && nums[i] != nums[nums[i] - 1])
                swap(nums[i], nums[nums[i] - 1]);
        }
        for (int i = 0; i < n; i++) {
            if(nums[i] != i + 1)
                return (i + 1);
        }
        return (n + 1);
    }
};
```
## Trapping Rain Water
问题：求出最大的蓄水量  
思路
- 左右两边同时搜索，分别记录两侧的最大值；两个指针也分别指向当前需要操作的元素
- 从最大值较小的一边开始操作，与最大值的差值即为当前位置的可蓄水量，累加即可
- 直到左右指针相遇
```
class Solution {
public:
    int trap(vector<int>& height) {
        int i = 0, j = height.size() - 1;  // 类似指针，指向当前位置
        int maxleft = 0, maxright = 0;  // 分别记录左右最大的高度
        int result = 0;
        while (i <= j) {
            maxleft = max(height[i], maxleft);
            maxright = max(height[j], maxright);
            if (maxleft < maxright) {  // 哪边最大的高度更低，就从哪边开始操作
                result += (maxleft - height[i]);
                i++;
            }
            else {
                result += (maxright - height[j]);
                j--;
            }
        }
        return result;
    }
};
```
# string
## 273 Integer to English Words
问题：把正整数转化为英文单词  
思路
- 拆分为0, (1~19), (20,30,...,90), 100, 1000, 1000000, ...
- 分情况讨论，递归求解
- 注意：空格分隔

```
class Solution {
public:
    vector<string> digits{"One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine", "Ten",
                             "Eleven", "Twelve", "Thirteen", "Fourteen", "Fifteen", "Sixteen", "Seventeen", "Eighteen", "Nineteen"};  // 1~19
    vector<string> tens{"Twenty", "Thirty", "Forty", "Fifty", "Sixty", "Seventy", "Eighty", "Ninety"};  // 20,...,90 
    
    string int2string(int n) {
        if (n < 1) return "";  // 如果取余后为0，注意返回空字符串
        else if (n < 20) return " " + digits[n - 1];
        else if (n < 100) return " " + tens[n / 10 - 2] + int2string(n % 10);
        else if (n < 1000) return int2string(n / 100) + " Hundred" + int2string(n % 100);
        else if (n < 1000000) return int2string(n / 1000) + " Thousand" + int2string(n % 1000);
        else if (n < 1000000000) return int2string(n / 1000000) + " Million" + int2string(n % 1000000);
        else return int2string(n / 1000000000) + " Billion" + int2string(n % 1000000000);
    }
    
    string numberToWords(int num) {
        if (num == 0) return "Zero";
        else {
            string ret = int2string(num);
            return ret.substr(1);
        }
    }
};
```
## 72 Edit Distance
问题： 求两个字符串的编辑距离  
思路
- 动态规划求解，设长度为i的字符串1和长度为j的字符串2的编辑距离为edit[i][j]，则存在三种情况
    - edit[i][j] = edit[i - 1][j] + 1
    - edit[i][j] = edit[i][j - 1] + 1
    - edit[i][j] = edit[i - 1][j - 1] + f(i, j)  (如果字符串1的第i个字符和字符串的第2个字符相同，则f(i,j)为0，否则为1)
- edit为上述三种情况下的最小值
- 注意考虑边界情况edit[i][0] = i, edit[0][j] = j
```
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size(), n = word2.size();
        vector<vector<int> > edit(m + 1, vector<int> (n + 1, 0));  // 初始化包含了字符串为空的情况
        int flag;
        // 初始化边界值
        for (int i = 1; i < m + 1; i++) edit[i][0] = i;
        for (int j = 1; j < n + 1; j++) edit[0][j] = j;
        // 动态求解
        for (int k = 1; k < m + 1; k++) {
            for (int l = 1; l < n + 1; l++) {
                if (word1[k - 1] != word2[l-1]) flag = 1;
                else flag = 0;
                edit[k][l] = min(edit[k - 1][l] + 1, min(edit[k][l - 1] + 1, edit[k - 1][l - 1] + flag));
            }
        }
        return edit[m][n];
    }
};
```