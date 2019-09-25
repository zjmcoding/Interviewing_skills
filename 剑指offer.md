---
t itle: 剑指offer
date: 2019-09-5 20:30:07
tags: coding
---

##### 面试题6：重建二叉树
<table><tr><td bgcolor=#D3D3D3>
	题目：输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。
</td></tr></table>

``` C++
TreeNode* reConstructBinaryTree(vector<int> pre, vector<int> vin){
	int inlen = vin.size();
	int prelen = pre.size();
	if(inlen == 0 || prelen == 0){
		return NULL;
	}
	//前序遍历的第一个值是根结点的值
	TreeNode *root = new TreeNode(pre[0]);

	// 在中序遍历中找到根结点的值
	int gen = 0;
	for(int i = 0; i < inlen; i++){
		if(vin[i] == pre[0]){
			gen = i;
			break;
		}
	}

	vector<int> pre_left, pre_right, vin_left, vin_right;
	//根据中序遍历的特点，得到根结点的左右子树对应的前序与后续遍历结果
	for(int i = 0; i < gen; i++){
		pre_left.push_back(pre[i+1]);
		vin_left.push_back(vin[i]);
	}

	for(int j = gen + 1; j < inlen; j++){
		pre_right.push_back(pre[j]);
		vin_right.push_back(vin[j]);
	}
	//重建左子树
	root->left = reConstructBinaryTree(pre_left, vin_left);
	//重建右子树
	root->right = reConstructBinaryTree(pre_right, vin_right);

	return root;
}
```
<!-- more -->

##### 面试题8：旋转数组的最小数字
<table><tr><td bgcolor=#D3D3D3>
	题目：把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。
NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。
</td></tr></table>
                               
``` C++
int minInorder(vector<int> rotateArray, int i, int j){
	int result = rotateArray[i];
	for(int x = i+1; x <= j; x++){
		if(rotateArray[x] < result){
			result = x;
		}
	}
	return result;
}

int minNumberInRotateArray(vector<int> rotateArray){
	if(rotateArray.size() == 0){
		return 0;
	}

	int i = 0; 
	int j = rotateArray.size()-1;
	int mid = i;
	while(rotateArray[i] >= rotateArray[j]){
		if(j - i == 1){
			mid = j;
			break;
		}

		mid = (i + j) / 2;
		if(rotateArray[i] == rotateArray[j] &&
			rotateArray[i] == rotateArray[mid]){
			return minInorder(rotateArray, i, j);
		}
		if(rotateArray[mid] >= rotateArray[i]){
			i = mid;
		}
		if(rotateArray[mid] <= rotateArray[j]){
			j = mid;
		}
	}

	return rotateArray[mid];
}
```                               
##### 面试题10：二进制中1的个数
<table><tr><td bgcolor=#D3D3D3>
	题目：输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。
</td></tr></table>

``` C++
//解法一：将1每次左移一位判断整数对应的位置上是否为1
//1100 & 0001 = 0000
//1100 & 0010 = 0000
//1100 & 0100 = 0100
//1100 & 1000 = 1000
//32位整数需要虚幻32次
int numberOf1(int n){
	int count = 0;
	int flag = 1;
	while(flag){
		if(n & flag)
			count++;
		flag = flag << 1;
	}
	return count;
}
//解法二：只需要循环一的个数次
//1100减去1 = 1011
//1100 & 1011 == 1000
//1000 - 1 = 0111
//0111 & 1000 = 0000
int numberOf1(int n){
	int count = 0;
	while(n){
		count++;
		n = n & (n-1);
	}
	return count;
}
```

##### 面试题11：数值的整数次方
``` C++
/*
 1.判断底数是否为0
 2.用位运算替代乘除法及求余运算 
 	x >> 1 相当于 x / 2
 	x << 1 相当于 x * 2
 	num & 1 == 1 判断一个数是奇数还是偶数。
 3.利用递推公式计算乘方更快，比如num的32次直接相乘需要31次，
 但是先求平方，再在平方的基础上求4次方，在4次方的基础上求8次，
 以此计算，只需要5次乘法。
*/
bool g_InvalidInput = false; //g_表示全局变量，该全局变量用来标示输入是否出错了

double Power(double base, int exponent){
	g_InvalidInput = false;

	if(equal(base, 0.0) && exponent < 0){
		//base=0 且 exponent < 0, 则无效输入值为true
		g_invalidInput = true;
		return 0.0;
	}

	//考虑指数为正负数的情况，并且将int转化成unsigned int
	unsigned int absExponent = (unsigned int)(exponent);

	if(exponent < 0)
		absExponent = (unsigned int)(-exponent);

	double result = PowerWithUnsignedExponent(base, absExponent);
	if(exponent < 0)
		result = 1.0 / result;

	return result;
} 

double PowerWithUnsignedExponent(double base, unsigned int exponent){
	//递归出口
	if(exponent == 0)
		return 1;
	if(exponent == 1)
		return base;

	double result = PowerWithUnsignedExponent(base, exponent >> 1);
	result *= result;

	if(exponent & 0x1 == 1)
		result *= base;

	return result;
}

//判断两个小数相等，只能判断他们之差的绝对值是不是在一个很小的范围内
bool equal(double num1, double num2){
	if((num1 - num2 > -0.0000001 ) && 
		(num1 - num2 < 0.0000001)){
		return true;
	}
	return false;
}
```

##### 面试题12：打印1到最大的n位数
<table><tr><td bgcolor=#D3D3D3>
	题目：输入数字n,按顺序打印从1到最大的n位十进制，比如输入2，打印1，2，3一直到最大的3位数999
</td></tr></table>

``` C++
/* 
1. 考虑大数问题，因为有可能n太大，用int 或者 long long 会溢出
2. 考虑加法的运算法则，包括进位，以及判断最大数是否溢出
3. 打印输出是防止00000x的情况发生，也就是输出忽略若干个开头的零
*/

void Print1ToMaxOfNDigits(int n){
	//一定学会考虑程序的边界及输入错误的情况
	if(n <= 0)
		return;

	//定义一个长度为n+1字符串，包含一个结束符'\0'
	char *number = new char[n + 1]; //在堆上分配一块内存区域
	memset(number, '0', n); //字符数组的初始化
	number[n] = '\0';

	while(!Increment(number)){
		PrintNumber(number);
	}

	delete []number; //释放申请的内存空间
}

//在表示数字的字符串上加1 加法的底层实现
bool Increment(char* number){
	bool isOverflow = false; //标识是否溢出，即是否到达了最大的n位数

	int nTakeOver = 0; //用于表示进位
	int nLength = strlen(number);//返回字符数组的长度

	for(int i = nLength - 1; i >= 0; i--){
		int nSum = number[i] - '0' + nTakeOver;//将一个字符转化为数字

		if(i == nLength - 1)
			nSum++;
		//nSum >= 10表示产生进位，12
		if(nSum >= 10){
			if(i == 0)
				isOverflow = true;
			else{
				nSum -= 10;
				nTakeOver = 1;
				number[i] = '0' + nSum;
			}
		}
		else{
			number[i] = '0' + nSum;
			break;
		}
	}

	return isOverflow;
}

//打印字符数组中的值
//字符串number表示一个数字，数字有若干个0开头，忽略这些0输出。
void PrintNumber(char* number){

	bool isBeginning0 = true;//用于标识当前是否是以0开头
	int nLength = strlen(number);

	for(int i = 0; i < nLength; i++){
		if(isBeginning0 && number[i] != '0')
			isBeginning0 = false;
		if(!isBeginning0){
			printf("%c", number[i]);
		}
	}
	pirntf("\t");
}
```
##### 面试题13：在O(1)的时间里删除链表节点
<table><tr><td bgcolor=#D3D3D3>
	题目：给定单向链表的头指针和一个结点指针，定义一个函数在O(1)的时间里删除该结点。
</td></tr></table>

``` C++
struct ListNode{
	int val;
	ListNode *next;
};

//顺序查找到i的前一个结点，并删除结点i的时间复杂度为O(n),不满足要求
//方法二：1.要删除节点i， 先把i的下一个节点j复制到i
//		 2. 把i的指针指向节点j的下一个结点
//		 3. 删除结点j, 其效果刚好把结点i给删除了
//平均时间复杂度为 [(n-1) * O(1) + O(n)] / n ~ O(1)
void DeleteNode(ListNode *pListHead, ListNode *pToBeDeleted){
	if(!pListHead || !pToBeDeleted)
		return;

	//要删除的结点不是尾结点
	if(pToBeDeleted->next != NULL){
		ListNode *pNext = pToBeDeleted->next;
		pToBeDeleted->val = pNext->val;
		pToBeDeleted->next = pNext->next;

		delete pNext;
	}
	//链表只有一个结点，删除头节点（也是尾节点）
	else if(pListHead == pToBeDeleted){
		delete pToBeDeleted;
		pToBeDeleted = NULL;
		pListHead = NULL;
	}
	//链表中有多个结点，删除尾节点
	else{
		ListNode *pNode = pListHead;
		while(pNode->next != pToBeDeleted){
			pNode = pNode->next;
		}

		pNode->next = NULL;
		delete pToBeDeleted;
		pToBeDeleted = NULL;
	}
}
```

##### 面试题14：调整数组顺序使奇数位于偶数前面
<table><tr><td bgcolor=#D3D3D3>
	题目：输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分
</td></tr></table>

``` C++
//这道题目我在面试汇量科技的时候遇见过，只可惜自己当时还没有认真看剑指offer
//面试官提示我用一次快排就可以解决，现在想想确实如此，这道题目就是一道快速排序的变形
void reOrderArray(vector<int> &array) {
	//对于测试用类=[1,2,3,4,5,6,7]
	//输出[1,7,3,5,4,6,2]
	if(array.size() == 0){
		return;
	}

	int i = 0;	//头结点哨兵i
	int j = array.size() - 1;  //尾节点哨兵j

	while(i < j){
		while(i < j && (array[i] & 1) != 0)
			i++;
		while(i < j && (array[i] & 1) == 0)
			j--;

		if(i < j){
			swap(array[i++], array[j--]);
		}
	}

}

//下面是关于nowcoder而言的
//要求输入有序[1,2,3,4,5,6,7]
//输出也有序[1,3,5,7,2,4,6]
//但是这样的要求也意味着时间复杂度的上升
void reOderArray_nowcoder(vector<int> &nums){
	if(nums.size() == 0)
		return ;

	int i = 0;
	int j = array.size();
	int index = -1; //用于标记当前遇到的第一个偶数位置

	while(i < j){
		if((nums[i] & 1) == 1){
			if(index >= 0){
				int temp = nums[i];
				for(int x = i; x >= index; x--){
					nums[x] = nums[x-1];
				}
				nums[index] = temp;
				index++;
			}
		}
		else{
			if(i == 0)
				index = 0;
			if(index < 0)
				index = i;
			i++;
			continue;
		}
	}
}

//考虑可扩展性的解法
void Reorder(vector<int> &nums, bool (*func)(int)){
	if(nums.size() == 0)
		return;

	int i = 0;
	int j = nums.size() - 1;

	while(i < j){
		while(i < j && !func(nums[i]))
			i++;
		while(i < j && func(nums[j]))
			j--;
		if(i < j)
			swap(nums[i++], nums[j--]);
	}
}

bool isEven(int n){
	return (n & 1) == 0;
}

void reOrderArray(vector<int> &nums){
	Reorder(nums, isEven);
}

//把数组中的负数移到非负数前面
//把能被3整除的数移到不能被3整除的数前面等等
//都可以考虑用快速排序解决
```


##### 面试题15： 链表中倒数第k个结点
<table><tr><td bgcolor=#D3D3D3>
	题目：输入一个链表， 输出该链表中倒数第k个结点。为符合大多数人的习惯，本题从1开始计数，即链表的尾结点是倒数第1个结点。例如一个链表有6个结点， 从头结点开始他们的值依次是 1、2、3、4、5、6. 这个链表的倒数第3个结点是值为4的结点。
</td></tr></table>

``` C++
struct ListNode
{
	int val;
	ListNode *next;
};

//不明智的解法：1.遍历链表一次，获得链表的长度n
//			2.再次遍历链表，直到n-k+1即为倒数第k个结点

/* 
 * 为了实现遍历链表一次就能找到倒数第k个结点
 * 第一个指针从链表的头指针开始遍历向前走k-1
 * 第二个指针不动；
 * 第k步开始，两个指针同时向后开始遍历
 * 由于两个指针的距离保持k - 1,当第一个指针到达链表的尾结点
 * 第二个指针正好是倒数第k个结点
*/
//鲁棒性
ListNode* FindKthToTail(ListNode *pHead, unsigned int k){
	//判断pHead = NULL 防止代码试图访问空指针指向的内存造成的程序崩溃
	//判断k = 0 由于k是一个无符号整数， 那么在for循环中k-1得到的将不是-1，而是0xFFFFFFFF
	if(pHead == NULL || k == 0)
		return NULL;

	ListNode *pAhead = pHead;
	ListNode *pBehind = NULL;

	for(unsigned int i = 0; i < k - 1; i++){
		//防止k大于结点总数时，pAhead访问空指针造成程序崩溃
		if(pAhead->next != NULL){
			pAhead = pAhead->next;
		}
		else{
			return NULL;
		}
	}

	pBehind = pHead;
	while(pAhead->next != NULL){
		pAhead = pAhead->next;
		pBehind = pBehind->next;
	}

	return pBehind;
}

//求链表的中间节点
ListNode* FindMidInList(ListNode *pHead){
	if (pHead == NULL)
		return NULL;

	ListNode *fast = pHead;
	ListNode *slow = pHead;

	if (fast->next != NULL)
		fast = fast->next;

	while (fast->next != NULL){
		fast = fast->next;
		slow = slow->next;
		if (fast->next != NULL)
			fast = fast->next;
	}

	return slow;
}
```
当我们用一个指针遍历链表不能解决问题的时候，可以尝试两个指针来遍历链表。可以让一个指针快一些（快慢指针），或者先让她在链表上走若干步。


##### 面试题16：反转链表
<table><tr><td bgcolor=#D3D3D3>
	题目：输入一个链表的头结点，反转该链表并输出反转后链表的头结点。
</td></tr></table>

``` C++
ListNode* reverseList(ListNode *head){
	if(head == NULL)
		return head;

	ListNode *new_head = NULL;
	while(head){
		ListNode *next = head->next;
		head->next = new_head;
		new_head = head;
		head = next;
	}
	return new_head;
}


```

##### 面试题17：合并两个排序的单链表
<table><tr><td bgcolor=#D3D3D3>
	题目：已知两个已排序的链表头结点指针l1与l2，将这两个链表合并，合并后认为有序，返回合并后的头结点。LeetCode 21
</td></tr></table>

``` C++
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2){
	ListNode temp_head(0);	//设置临时的头结点
	ListNode *pre = &temp_head;	//使用pre指针指向temp_head;
	//l1与l2同时不为空时，对他们进行比较
	while(l1 && l2){
		if(l1->val < l2->val){
			pre->next = l1;
			l1 = l1->next;
		}
		else{
			pre->next = l2;
			l2 = l2->next;
		}
		pre = pre->next;
	}
	if(l1){
		pre->next = l1;
	}
	if(l2){
		pre->next = l2;
	}

	return temp_head.next;
}
```

扩展题目：多个排序链表的合并

<table><tr><td bgcolor=#D3D3D3>
	题目：已知k个已排序链表头结点的指针，将这k个链表合并，合并后任然有序，返回合并后的头结点。 Leetcode 23.
</td></tr></table>

```C++
/*方案一：最普通的方法，k个链表按顺序合并k-1次。
  设有k个链表，平均每个链表有n个结点，
  时间复杂度为：O(k^2 * n)
*/

/* 方案二：将k*n个节点放到vector中，再将vector排序，
		  再将节点顺序相连。
		  O(kNlogkN)
*/

/*方案三：对k个链表进行分治， 两两进行合并。
设有k个链表， 平均每个链表有n个节点，时间复杂度：
第一轮，进行k/2 次， 每次处理2n个节点；
第二轮，进行k/4 次，每次处理4n个数字；
最后一次，进行k/(2^logk)次，每次处理2^logk*N个值。
O(kNlogk)
*/
#include <vector>
#include <algorithm>
using namespace std;

//排序法：
bool (const ListNode *a, const ListNode *b){
	return a->val < b->val;
}

ListNode *mergeKLists(vector<ListNode*> &lists){
	vector<ListNode *> node_vec;
	for(int i = 0; i < lists.size(); i++){
		ListNode *head = lists[i];
		while(head){
			node_vec.push_back(head);
			head = head->next;
		}
	}
	if(node_vec.size() == 0){
		return NULL;
	}
	sort(node_vec.begin(), node_vec.end(), cmp);
	for(int i = 1; i < node_vec.size(); i++){
		node_vec[i-1]->next = node_vec[i];
	}
	node_vec[node_vec.size() - 1]->next = NULL;
	return node_vec[0];
}

//分治法
ListNode* mergeKLists(vector<ListNode*> &lists){
	if(lists.size() == 0){
		return NULL;
	}

	if(lists.size() == 1){
		return lists[0];
	}

	if(lists.size() == 2){
		return mergeTwoLists(lists[0], lists[1]);
	}

	int mid = lists.size() / 2;
	vector<ListNode*> sub1_lists;
	vector<ListNode*> sub2_lists;
	for(int i = 0; i < mid; i++){
		sub1_lists.push_back(lists[i]);
	}
	for(int i = mid+1; i < lists.size(); i++){
		sub2_lists.push_back(lists[i]);
	}
	ListNode *l1 = mergeKLists(sub1_lists);
	ListNode *l2 = mergeKLists(sub2_lists);
	return mergeTwoLists(l1,l2); //分治处理
}
```

##### 面试题18：树的子结构
<table><tr><td bgcolor=#D3D3D3>
	题目：输入两颗二叉树A和B, 判断B是不是A的子结构。
</td></tr></table>

``` C++
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};
bool DoseTree1HasTree2(TreeNode *pRoot1, TreeNode *pRoot2){
	//注意判断是否到达叶节点的顺序，应该先判断树B,然后判断树A
	//到达树B的叶子节点表示到达树B的叶子结点都匹配
	if(pRoot2 == NULL)
		return true;

	if(pRoot1 == NULL)
		return false;

	if(pRoot1->val != pRoot2->val)
		return false;

	return DoseTree1HasTree2(pRoot1->left, pRoot2->left) &&
		DoseTree1HasTree2(pRoot1->right, pRoot2->right);
}
bool hasSubtree(TreeNode *pRoot1, TreeNode *pRoot2){
	bool result = false;

	if(pRoot1 != NULL && pRoot2 != NULL){
		//在树A中找到与树B的根结点的值一样的结点R
		//判断树A中以R为根结点的子树是不是包含和树B一样的结构
		if(pRoot1->val == pRoot2->val){
			result = DoseTree1HasTree2(pRoot1, pRoot2);
		}
		//如果树1中以R为根结点的子树不包含树B,则递归的判断
		//以R的左右孩子为根结点的子树是否包含树B.
		if(!result){
			result = hasSubtree(pRoot1->left, pRoot2);
		}
		if(!result){
			result = hasSubtree(pRoot1->right, pRoot2);
		}
	}
	return result;
}
```
##### 面试题19：二叉树的镜像

``` C++
void Mirror(TreeNode *pRoot){
	if(pRoot == NULL)
		return;

	//左右子树均为空，只有一个根结点，一个根结点的镜像是其本身。
	if(pRoot->left == NULL && pRoot->right == NULL)
		return;
	//若左右子节点有一个不为空，则交换左右子节点
	TreeNode *temp = pRoot->left;
	pRoot->left = pRoot->right;
	pRoot->right = temp;

	//左子树不为空，则递归的处理左子树
	if(pRoot->left)
		Mirror(pRoot->left);

	if(pRoot->right)
		Mirror(pRoot->right);
}
```
##### 面试题20：顺时针打印矩阵
<table><tr><td bgcolor=#D3D3D3>
输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 
1   2  3  4 
5   6  7  8 
9  10 11 12
13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.
</td></tr></table>

``` C++
vector<int> printMatrix(vector<vector<int> > matrix) {
    vector<int> res;
    res.clear();
    int row = matrix.size();//行数
    int column = matrix[0].size();//列数
    if (row == 0 || column == 0)  return res;
    //计算打印的圈数
    int circle=((row < column ? row:column)- 1) / 2 + 1;//圈数
    for(int i = 0;i < circle; i++){
        //从左向右打印
        for(int j = i; j < column - i; j++)
            res.push_back(matrix[i][j]);         
        //从上往下的每一列数据
        for(int k = i+1;k < row - i; k++)
            res.push_back(matrix[k][column-1-i]);
        //判断是否会重复打印(从右向左的每行数据)
        //endX > start && endY > start
        for(int m = column-i-2;(m >= i)&&(row-i-1!= i);m--)
            res.push_back(matrix[row-i-1][m]);
        //判断是否会重复打印(从下往上的每一列数据)
        for(int n = row-i-2;( n > i)&&(column - i - 1 != i);n--)
            res.push_back(matrix[n][i]);
    }
    return res;
}
```

##### 面试题23：从上往下打印二叉树
<table><tr><td bgcolor=#D3D3D3>
	题目：从上往下打印出二叉树的每个节点，同层节点按照从左往右的顺序打印。
</td></tr></table>

``` C++
//每次打印一个结点的时候，如果该结点有子节点，则该结点的子节点放到
//一个队列的尾部
//接下来来到对头取出最早进入队列的结点，重复前面的打印操作
//直至队列中的所有元素都被打印出来
vector<int> PrintFromTopToBottom(TreeNode* root){
	vector<int> result;
	if(root == NULL)
		return result;
	deque<TreeNode*> deque_treeNode;
	deque_treeNode.push_back(root);
	while(deque_treeNode.size()){
		TreeNode *temp = deque_treeNode.front();
		deque_treeNode.pop_front();
		result.push_back(temp->val);
		if(temp->left)
			deque_treeNode.push_back(temp->left);
		if(temp->right)
			deque_treeNode.push_back(temp->right);
	}
	return result;
}
```

##### 面试题24：二叉搜索树的后序遍历序列
<table><tr><td bgcolor=#D3D3D3>
	题目：输入一个整数数组，判断该整数数组是不是某二叉树搜索树的后序遍历的结果。如果是返回true,否则返回false
</td></tr></table>

``` C++
//后序遍历的最后一个值为根结点
//二叉搜索树的左子树的值都比根结点的值小
//右子树结点的值都比根结点的值大
bool VerifySequenceOfBST(vector<int> sequence){
	if(sequence.empty())
		return false;
	int root = sequence[sequence.size() - 1];

	int i = 0; 
	vector<int> leftSequence;
	for(; i < sequence.size() - 1; i++){
		if(sequence[i] < root){
			leftSequence.push_back(sequence[i]);
		}
		else{
			break;
		}
	}
	vector<int> rightSequence;
	for(int j = i; j < sequence.size() - 1; j++){
		if(sequence[j] > root){
			rightSequence.push_back(sequence[j]);
		}
		else{
			return false;
		}
	}

	bool left = true;
	if(!leftSequence.empty())
		left = VerifySequenceOfBST(leftSequence);
	bool right = true;
	if(!rightSequence.empty())
		right = VerifySequenceOfBST(rightSequence);

	return (left && right);
}
```
##### 面试题25：二叉树中和为某一值的路径
<table><tr><td bgcolor=#D3D3D3>
	题目：输入一颗二叉树的根节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)
</td></tr></table>

``` C++
vector<vector<int> > FindPath(TreeNode* root, int expectNumber){
	vector<vector<int> > result;
	vector<int> path; //用于模拟一个栈
	preorder(root, path_value, expectNumber,path, result);
	return result;
}

void preorder(TreeNode *node, int &path_value, int 
	expectNumber, vector<int> &path,
	vector<vector<int> > &result){
	if(!node)
		return;
	path_value += node->val;
	path.push_back(node->val);
	if(!node->left && !node->right && path_value == expectNumber){
		result.push_back(path);
	}
	preorder(root->left, path_value, expectNumber,path, result);
	preorder(root->right, path_value, expectNumber,path, result);
	path_value = node->val;
	path.pop_back();
}
```


##### 面试题26：复杂链表的复制
<table><tr><td bgcolor=#D3D3D3>
	题目：前)输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）
</td></tr></table>

``` C++
struct RandomListNode
{
	int label;
	RandomListNode *next, *random;
	RandomListNode(int x):label(x), next(NULL), random(NULL){}
};
//使用一个大小为n的哈希表，以空间换时间，复杂度为O(n)
RandomListNode *copyRandomList(RandomListNode *head){
	map<RandomListNode*, int> node_map;	//地址到结点位置的map;
	vector<RandomListNode *> node_vec;
	RandomListNode *ptr = head; //使用vector根据存储结点位置访问地址
	int i = 0;
	while(ptr){
		//将新链表结点push入node_vec,生成新链表位置到地址的map
		node_vec.push_back(new RandomListNode(ptr->val));
		node_map(node_vec[i], i);
		ptr = ptr->next;	//遍历原始链表
		i++;	//i记录结点位置
	}
	node_vec.push_back(0);
	ptr = head;
	i = 0;
	while(ptr){
		node_vec[i]->next = node_vec[i+1]; //连接新链表的next指针
		if(ptr->random){
			int id = node_map[ptr->random];
			node_vec[i]->random = node_vec[id]; 
		}
		ptr = ptr->next;
		i++;
	}
	return node_vec[0];
}
```
使用复制链表的方法解决

``` C++
class Solution
{
public:
	RandomListNode* Clone(RandomListNode* pHead){
		CloneNodes(pHead);
		ConnectRandomNodes(pHead);
		return ReconnectNodes(pHead);
	}

	//第一步：根据原始链表的每个结点 N 创建对应的 N'
	//把 N' 连接到 N 的后面
	void CloneNodes(RandomListNode* pHead){
		RandomListNode* pNode = pHead;
		while(pNode){
			RandomListNode* pCloned = new RandomListNode(pNode->val);
			pCloned->next = pNode->next;
			pCloned->random = NULL;
			pNode->next = pCloned;
			pNode = pCloned->next;
		}
	}

	//第二步：设置复制出来的结点的random指针
	//假设原始链表上的N的random指针指向S
	//那么对应复制出来的N'是N的next指向的结点
	//同样S'是S的next指向的结点
	//即 N' 的random 指针指向S的下一个结点S'

	void ConnectRandomNodes(RandomListNode* pHead){
		RandomListNode* pNode = pHead;
		while(pNode){
			RandomListNode* pCloned = pNode->next;
			if(pNode->random){
				pCloned->random = pNode->random->next;
			}
			pNode = pCloned->next;
		}
	}

	//把这个长链表拆分成两个链表：
	//把奇数位置的结点用next链接起来就是原始链表
	//把偶数位置的链表用next链接起来就是复制出来的链表

	RandomListNode* ReconnectNodes(RandomListNode* pHead){
		RandomListNode* pNode = pHead;
		RandomListNode* pClonedNode = NULL;
		RandomListNode* pClonedHead = NULL;
		if(pNode){
			pClonedHead = pClonedNode = pNode->next;
			pNode->next = pClonedNode->next;
			pNode = pNode->next;
		}
		while(pNode){
			pClonedNode->next = pNode->next;
			pClonedNode = pClonedNode->next;
			pNode->next = pClonedNode->next;
			pNode = pNode->next;
		}
		return pClonedHead;
	}
};
```

##### 面试题27：二叉搜索树与双向链表
<table><tr><td bgcolor=#D3D3D3>
输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。
    10 
  6 	14 
4  8  12  16
</td></tr></table>


``` C++
TreeNode* Convert(TreeNode* pRootOfTree){
	TreeNode *pLastNodeInList = NULL;
	ConvertNode(pRootOfTree, &pLastNodeInList);

	TreeNode *pHeadOfList = pLastNodeInList；
	while(pHeadOfList != NULL && pHeadOfList->left != NULL)
		pHeadOfList = pLastNodeInList->left;

	return pHeadOfList;
}

void ConvertNode(TreeNode* pNode, TreeNode** pLastNodeInList){
	if(pNode == NULL)
		return;

	TreeNode *pCurrent = pNode;
	if(pCurrent->left != NULL)
		ConvertNode(pCurrent->left, pLastNodeInList);
	pCurrent->left = *pLastNodeInList;
	if(*pLastNodeInList != NULL)
		(*pLastNodeInList)->right = pCurrent;
	*pLastNodeInList = pCurrent;

	if(pCurrent->right != NULL)
		ConvertNode(pCurrent->right, pLastNodeInList);
}
```
##### 面试题28：字符串的排列
<table><tr><td bgcolor=#D3D3D3>
	题目：输入一个字符串打印出该字符串中字符的所有排列。输入abc,输出abc、acb、bac、bca、cab、cba
</td></tr></table>

``` C++
class Solution {
public:
    vector<string> Permutation(string str) {
        vector<string> result;
        if(str.empty()){
            return result;
        }
        //第一部分为它的第一个字符，第二部分是后面的所有字符
        //str整个字符串的第一个字符
        Permutation(str, result, 0);
        
        sort(result.begin(), result.end());
        
        return result;
        
    }
    //
    void Permutation(string str, vector<string> &result, int begin){
    	//begin指向当前我们做排列操作的字符串的第一个字符
        if(begin == str.size() - 1){
        	//防止当前找到的排列已经在result结果集当中
            if(find(result.begin(), result.end(), str) == result.end()){
                result.push_back(str);
            }
        }
        else{
            for(int i = begin; i <str.size(); i++){
                swap(str[i], str[begin]);//交换begin和i指向的字符
                Permutation(str,result,begin+1);//递归地在对字符串begin后面的字符做排列
                swap(str[i], str[begin]);
            }
        }
    }
    
    void swap(char &fir,char &sec){
        char temp = fir;
        fir = sec;
        sec = temp;
    }
};
```
##### 面试题29：数组中出现次数超过一半的数字
<table><tr><td bgcolor=#D3D3D3>
	题目：数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。
</td></tr></table>

``` C++
bool Input_invalid = false;
bool check_invalid_array(vector<int> numbers){
	bool Input_invalid = false;
	if(numbers.size() == 0)
		Input_invalid = true;
	return Input_invalid;
}

bool checkMoreThanHalf(vector<int> numbers, int number){
	int times = 0;
	for(int i = 0; i < numbers.size(); i++){
		if(numbers[i] == number){
			times++;
		}
	}

	bool isMoreThanHalf = true;
	if(times * 2 <= numbers.size()){
		Input_invalid = true;
		isMoreThanHalf = false;
	}

	return isMoreThanHalf;
}

int MoreThanHalfNum_Solution(vector<int> numbers){
	if(check_invalid_array(numbers)){
		return 0;
	}
	int result = numbers[0];
	int times = 1;
	for(int i = 1; i < numbers.size(); i++){
		if(times == 0){
			result = numbers[i];
			times = 1;
		}
		else if(numbers[i] == result)
			times++;
		else
			times--;
	}

	if(!chekMoreThanHalf(numbers, result)){
		return 0;
	}

	return result;
}
```
##### 面试题30：最小的k个数
<table><tr><td bgcolor=#D3D3D3>
	题目：输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4。
</td></tr></table>

``` C++
//最大堆的方法实现，特别适合于处理海量数据集
vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {

    int len = input.size();
    if(len < k) return vector<int>();
    
    multiset<int, greater<int> > LeastNums; //最大堆
    vector<int>::iterator iter = input.begin();
    for(; iter != input.end(); ++iter){
        if(LeastNums.size() < k){
            LeastNums.insert(*iter);
        }
        else{
        	//取出堆顶元素
            multiset<int, greater<int> >::iterator iterGreatest = LeastNums.begin();
            //当堆顶元素大于当前访问的元素
            //则删除对顶元素，并把当前元素插入堆中
            if(*iter < *(LeastNums.begin())){
                LeastNums.erase(iterGreatest);
                LeastNums.insert(*iter);
            }
        }
    }
    
    return vector<int>(LeastNums.begin(), LeastNums.end());
}

//快速排序的变形
int Partition(vector<int> arr, int left, int right){
    int i = left + 1;
    int j = right;
    int pivot = arr[left]; //基准
    while(i < j){
        while(arr[j] >= pivot && i < j)
            --j;
        while(arr[i] <= pivot && i < j)
            ++i;
        if(i < j)
            swap(arr[i++],arr[j--]);
    }
    swap(arr[j],arr[left]);
    return j;
}

vector<int> GetLeastNumbers(vector<int> nums, int k){
	vector<int> result;
	if(nums.size() == 0)
		return result;
	int start = 0;
	int end = nums.size() - 1;
	int index = Partition(nums, start, end);
	while(index != k-1){
		if(index > k-1){
			end = index - 1;
			index = Partition(nums, start, end);
		}
		else
		{
			start = index + 1;
			index = Partition(nums, start, end);
		}
	}

	for(int i = 0; i < k; i++){
		result.push_back(nums[i]);
	}
	sort(result.begin(), result.end());
	return result;
}
```

##### 面试题31：连续子数组的最大和
<table><tr><td bgcolor=#D3D3D3>
	题目：输入一个整型数组，数组里有正数也有负数，数组中一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。要求时间复杂度为O(n).
</td></tr></table>

``` C++
//[1,-2,3,10,-4,7,2,-5]
int FindGreatestSumOfSubArray(vector<int> array) {
    if(array.size() <= 0) return -1;
    //最大值
    int max_sum = 0x80000000;
    int cur_sum = 0; //当前的最大值
    for(int i = 0; i < array.size(); i++){
        if(cur_sum <= 0){
            cur_sum = array[i]; //1+(-2) = -1 cur_sum = 3
        }
        else{
            cur_sum += array[i];
        }
        
        if(cur_sum > max_sum)
            max_sum = cur_sum;
    }
    
    return max_sum;
}
```

##### 面试题32：从1到n整数中1出现的次数leetcode233
<table><tr><td bgcolor=#D3D3D3>
	题目：输入一个整数n，求从1到n这个n个整数的十进制表示中1出现的次数。
</td></tr></table>

``` C++
//数学法：
//个位： 1 n / 10 + (n % 10 != 0)
//10,20,30...,160,161,16X
//十位： 10 (n / 100) * 10 + min(max(n % 100 - 10 + 1, 0), 10)
//100,200,1600,1610,161X(X>0),1650
//百位： 100 (n / 1000) * 100 + min(max(n % 1000 - 100 + 1, 0), 100)
//1000,2000,...16000,161xy,16500
int countDigitOne(int n)
{
    int count = 0;
    for(long long i = 1; i <= n; i *= 10){
        long long diviser = i * 10;
        count += (n / diviser) * i + min(max((n % diviser) - i + 1, 0LL), i);
    }
    return count;
}
```

##### 面试题33：把数组排成最小的数
<table><tr><td bgcolor=#D3D3D3>
	题目：输入一个正整数数组，把数组中的所有数排成一个数，打印能拼接出的所有数字中最小的一个。
</td></tr></table>

两个数字m和n能拼接成数字mn和nm，如果mn < nm，那么我们打印出mn，也就是说m应该排在n的前面，我们定义此时m < n；反之，如果nm < mn，我们定义n < m; 如果mn = nm，定义m = n.

``` C++
static bool cmp(int x, int y){
	return to_string(x) + to_string(y) < to_string(y) + to_string(x);
}

string PrintMinNumber(vector<int> nums){
	string result = "";
	if(nums.size() <= 0)
		return result;
	sort(nums.begin(), nums.end(), cmp);
	for(int i = 0; i < nums.size(); i++){
		result += to_string(nums[i]);
	}

	return result;
}
```
##### 面试题34：丑数
<table><tr><td bgcolor=#D3D3D3>
	题目：我们把只包含因子2、3、5的数称为丑数，求按从小到大顺序第1500个丑数。
</td></tr></table>


``` C++
int GetUglyNumber_Solution(int index) {
    int *ugly = new int[index];
    ugly[0] = 1;
    int num_2 = 0;
    int num_3 = 0;
    int num_5 = 0;
    for(int i = 1; i < index; i++){
        ugly[i] = min(min(ugly[num_2]*2, ugly[num_3]*3), ugly[num_5]*5);
        if(ugly[i] / ugly[num_2] == 2)
            num_2++;
        if(ugly[i] / ugly[num_3] == 3)
            num_3++;
        if(ugly[i] / ugly[num_5] == 5)
            num_5++;
    }
    return ugly[index-1];
}
```

##### 面试题35：第一个只出现一次的字符
<table><tr><td bgcolor=#D3D3D3>
	题目：在字符串中找出第一个只出现一次的字符。如输入"abaccdeff"，则输出"b"
</td></tr></table>

``` C++
int FirstNotRepeatingChar(string str) {
    if ( str.length() == 0)
        return -1;
     
    unsigned int hashTime[256] = {0};//哈希表
    //统计每一个字符出现的次数
    for(int i = 0;i < str.length();++i)
        hashTime[str[i]]++;
    //查找第一个只出现一次的字符
    for(int i = 0;i < str.length();++i)
    {
        if(hashTime[str[i]] == 1)
            return i;
    }
    return -1;
}
```
##### 面试题36：数组中的逆序对

``` C++
class Solution
{
private:
    long result; //结果变量
public:
    int InversePairs(vector<int> data)
    {
        result = 0; //初始化
        if(data.empty()) return 0;
        mergeSort(data, 0, data.size()-1); //按从大到小归并排序，并统计逆序对个数
        return result % 1000000007; //取模输出，防止输出太大
    }
   
private:
    void mergeSort(vector<int> &a, int begin, int end){
        if(begin >= end) return; //递归出口
        int mid = (begin + end) / 2;
        mergeSort(a, begin, mid);    //左子数组排序
        mergeSort(a, mid+1, end);    // 右子数组排序
        merge(a, begin, mid, end); //归并有序数组
    }
    //
    void merge(vector<int> &a, int begin, int mid, int end){
        vector<int> temp(end - begin + 1);
        //i指向第一个子数组的第一个字符
        //j指向第二个子数组的第一个字符
        int i = begin, j = mid + 1, k = 0;

        while(i <= mid && j <= end){
        	//当a[i] > a[j] 说明存在逆序对
        	//将a[i]存入temp[k],并分别向后移动i,k指针
        	//逆序对的个数加 end - j + 1;
            if(a[i] > a[j]){
                temp[k++] = a[i++];
                result += end - j + 1;
            }
            else{
            	//当a[i] < a[j] 说明不存在逆序对
            	//将a[j]存入temp[k],并分别向后移动j,k指针
                temp[k++] = a[j++];
            }
        }
        // 如果 i<= mid, 说明 
        while(i <= mid) temp[k++] = a[i++];
        while(j <= end) temp[k++] = a[j++];
        
        for(int i = begin, k = 0; i <= end && k < temp.size(); i++,k++){
            a[i] = temp[k];
        }
    }
};
```

##### 面试题37：两个链表的第一个公共结点
有两种方式：
一种是使用set求交集的方式，遍历链表A，将A中的节点对应的指针插入set; 遍历链表B中对应的指针,在set中查找，发现set中的第一个节点地址，即两个链表的交点。空间复杂度O(n).
``` C++
ListNode *getInterectionNode(ListNode *headA, ListNode *headB){
    set<ListNode*> node_set;
    while(headA){
        node_set.insert(headA);
        headA = headA->next;
    }
    while(headB){
        if(node_set.find(headB) != node_set.end()){
            return headB;
        }
        headB = headB->next;
    }
    return NULL;
}
```
第二种方法：

步骤一：计算headA链表长度，计算headB链表长度，较长的链表多出的长度。

步骤二：将较长的链表的指针移动到和较短链表指针对齐的位置

步骤三：headA与headB同时移动时，当两个指针指向同一个节点，即找到了交叉链表的交点。

``` C++
int get_list_length(ListNode *head){
    int len = 0;
    while(head){
        ++len;
        head = head->next;
    }
    return len;
}
ListNode *forword_long_list(int long_len, int short_len, ListNode *head){
    int delta = long_len - short_len;
    while(head && delta){
        head = head->next;
        --delta;
    }
    return head;
}
ListNode *getInterectionNode(ListNode *headA, ListNode *headB){
    int list_A_len = get_list_length(headA);
    int list_B_len = get_list_length(headB);
    if(list_A_len > list_B_len){
        headA = forword_long_list(list_A_len,list_B_len,headA);
    }
    else{
        headB = forword_long_list(list_B_len,list_A_len,headB);
    }
    while(headA && headB){
        if(headA == headB){
            return headA;
        }
        headA = headA->next;
        headB = headB->next;
    }
    return NULL;
}
```
##### 面试题38：数字在排序数组中出现的次数

``` C++
//二分查找在数组中查找一个合乎要求的数字，他们的时间复杂度都是O(logn)

int GetFirstK(const int* data, int length, int k, int start, int end);
int GetLastK(const int* data, int length, int k, int start, int end);

int GetNumberOfK(const int* data, int length, int k)
{
    int number = 0;

    if(data != nullptr && length > 0)
    {
        int first = GetFirstK(data, length, k, 0, length - 1);
        int last = GetLastK(data, length, k, 0, length - 1);
        
        if(first > -1 && last > -1)
            number = last - first + 1;
    }

    return number;
}

// 找到数组中第一个k的下标。如果数组中不存在k，返回-1
int GetFirstK(const int* data, int length, int k, int start, int end)
{
    if(start > end)
        return -1;

    int middleIndex = (start + end) / 2;
    int middleData = data[middleIndex];

    if(middleData == k)
    {
        if((middleIndex > 0 && data[middleIndex - 1] != k) 
            || middleIndex == 0)
            return middleIndex;
        else
            end  = middleIndex - 1;
    }
    else if(middleData > k)
        end = middleIndex - 1;
    else
        start = middleIndex + 1;

    return GetFirstK(data, length, k, start, end);
}

// 找到数组中最后一个k的下标。如果数组中不存在k，返回-1
int GetLastK(const int* data, int length, int k, int start, int end)
{
    if(start > end)
        return -1;

    int middleIndex = (start + end) / 2;
    int middleData = data[middleIndex];

    if(middleData == k)
    {
        if((middleIndex < length - 1 && data[middleIndex + 1] != k) 
            || middleIndex == length - 1)
            return middleIndex;
        else
            start  = middleIndex + 1;
    }
    else if(middleData < k)
        start = middleIndex + 1;
    else
        end = middleIndex - 1;

    return GetLastK(data, length, k, start, end);
}
```

##### 面试题39：二叉树的深度
``` C++
int treeDepth(TreeNode *pRoot){
	if(pRoot == NULL)
		return 0;

	return max(treeDepth(pRoot->left), treeDepth(pRoot->right)) + 1;
}
```

##### 面试题40：数组中只出现一次的数字
<table><tr><td bgcolor=#D3D3D3>
	题目：一个整数数组里除了两个数字之外，其他的数字都出现两次，找出这两个只出现一次的数字。
</td></tr></table>

从头到尾依次异或数组中的每一个数字，那么最终得到的结果就是两个只出现一次的数字的异或结果
我们找到异或结果第一个为1的位置，记为n，然后根据第n位是不是1把数组分成两个子数组。

``` C++
void FindNumsAppearOnce(vector<int> data,int* num1,int *num2) {
    int length = data.size();
    if(length < 2){
        return;
    }

    int resultExclusiveOR = 0;
    for(int i = 0; i < length; i++){
        resultExclusiveOR ^= data[i];
    }

    if(resultExclusiveOR == 0)
        return;
    unsigned int indexOf1 = FindFirstBitIs1(resultExclusiveOR);

    *num1 = *num2 = 0;
    for(int j = 0; j < length; j++){
        if(IsBit1(data[j], indexOf1)){
            *num1 ^= data[j];
        }
        else{
            *num2 ^= data[j];
        }
    }
}

unsigned int FindFirstBitIs1(int num){
    int indexBit = 0;
    while((num & 1) == 0 && (indexBit < 8 * sizeof(int))){
        num = num >> 1;
        indexBit++;
    }
    return indexBit;
}

bool IsBit1(int num, unsigned int indexBit){
    num = num >> indexBit;
    return (num & 1);
}
```

##### 面试题41：和为S的连续正整数序列

<table><tr><td bgcolor=#D3D3D3>
	题目：输入一个正数s,打印所有和为s的连续正数序列（至少包含两个数）注意此题目不包含正数本身
</td></tr></table>

``` C++
vector<vector<int> > FindContinuousSequence(int sum) {
    vector<vector<int> > result;
    if(sum < 3)
        return result;
    int small = 1;
    int big = 2;
    int middle = (1 + sum) >> 1;
    int curSum = small + big;

    while(small < middle){
        
        if(curSum == sum){
            vector<int> temp;
            for(int i = small; i <= big; i++){
                temp.push_back(i);
            }
            result.push_back(temp);
        }
        while(curSum > sum && small < middle){
            curSum -= small;
            small++;
            if(curSum == sum){
                vector<int> temp;
                for(int i = small; i <= big; i++){
                    temp.push_back(i);
                }
                result.push_back(temp);
            }
        }
        big++;
        curSum += big;
    }
    
    return result;
}
```

##### 面试题42：反转单词顺序 VS 左旋转字符串
``` C++
string ReverseSentence(string str) {
	//反转句子中所有的字符
    reverse(str.begin(), str.end());
    //定义两个指针，均指向字符串的第一个字符
    string::size_type left = 0, right = 0;
    while((right = str.find(' ', left)) != str.npos){
        // reverse函数用于反转在[first,last)范围内的顺序
        reverse(str.begin() + left, str.begin() + right);
        left = right + 1;
    }
    reverse(str.begin()+left, str.end());
    return str;
}
```
<table><tr><td bgcolor=#D3D3D3>
	题目：字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。比如输入字符串"abcdefg"和2，输出"cdefgab";
</td></tr></table>

``` C++
string LeftRotateString(string str, int n){
	int len = str.size();
	//if判断防止输入的字符串为空
	if(!str.empty()){
		//防止内存访问越界的问题
		if(n > 0 && n < len){
			reverse(str.begin()+0, str.begin()+n);
			reverse(str.begin()+n, str.begin()+len);
			reverse(str.begin()+0, str.begin()+len);
		}
	}
	return str;
}
```

##### 面试题44：扑克牌的顺子
<table><tr><td bgcolor=#D3D3D3>
	题目：从扑克牌中随机抽5张牌，判断是不是顺子，即这五张牌是不是连续的。大小王是0
</td></tr></table>

``` C++
bool IsContinuous(vector<int> nums){
	if(nums.empty()) return false;

	sort(nums.begin(), nums.end());
	int numberOfZero = 0;
	int numberOfGap = 0;
	for(int i = 0; i < nums.size(); ++i){
		if(nums[i] == 0)
			++numberOfZero;
	}

	int small = numberOfZero;
	int big = small + 1;

	while(big < nums.size()){
		if(nums[small] == nums[big])
			return false;

		numberOfGap += numbers[big] - numbers[small] - 1;
		small = big;
		++big;
	}
	return (numberOfZero < numberOfGap) ? false : true;
}
bool IsContinuous( vector<int> numbers ) {
	//判断输入是否合理
    if(numbers.empty()) return false;
    int count[14] = {0};
    int len = numbers.size();
    int max = -1;
    int min = 14;
    for(int i = 0; i < len; i++){
        count[numbers[i]]++;

        if(numbers[i] == 0) continue;//0可以代表任意数字，故不用

        if(count[numbers[i]] > 1) return false;//存在对子，则不可能是顺子
        
        if(numbers[i] > max) max = numbers[i];

        if(numbers[i] < min) min = numbers[i];
    }
    if(max - min < 5) return true;
    
    return false;
}
```

##### 面试题45：圆圈中最后剩下的数字
<table><tr><td bgcolor=#D3D3D3>
	题目：0，1，...，n-1这n个数字排成一个圆圈，从数字0开始每次从这个圆圈里删除m个数字。求出圆圈里剩下的最后一个数字。
</td></tr></table>

方法一：使用环形链表模拟圆圈的经典解法
``` C++
//分析每次被删除的数字的规律并直接计算出圆圈中最后剩下的数字。
int LastRemaining(unsigned int n, unsigned int m){
	if(n < 1 || m < 1){
		return -1;
	}

	int last = 0;
	for(int i = 2; i <= n; i++){
		last = (last + m) % i;
	}

	return last;
}
```

##### 面试题47：不用加减乘除做加法
<table><tr><td bgcolor=#D3D3D3>
	题目：写一个函数，求两个整数之和，要求函数体内不得使用+、-、*、/四则运算符号。
</td></tr></table>

``` C++
int Add(int num1, int num2){
	int sum,carry;
	do{
		sum = num1 ^ num2;
		carry = (num1 & num2) << 1;

		num1 = sum;
		num2 = carry;
	}while(carry != 0);

	return num1;
}
```

##### 面试题47：不能被继承的类

``` C++
template <typename T> calss MakeSealed
{
	friend T;
private:
	MakeSealed(){}
	~MakeSealed(){}
};

class SealedClass2 : virtual public MakeSealed<SealedClass2>
{
public:
	SealedClass2(){}
	~SealedClass2(){}
};
```
##### 面试题49：atoi的实现（将字符串转化为整形）。
``` C++
enum Status {kValid = 0, kInvalid};
int g_nStatus = kValid;


long long StrToIntCore(const char *digit, bool minus){
	long long num = 0;
	while(*digit != '\0'){
		if(*digit >= '0' && *digit <= '9'){
			int flag = minus ? -1 : 1;
			num = num * 10 + flag * (*digit - '0');

			//判断整数是否发生了上溢出或者下溢出。
			//0x7FFF FFFF表示最大的正整数
			//0x80000000 表示最小的负整数
			if((!minus && num > 0x7FFFFFFF) ||
				(minus && num < (signed int)0x80000000)){
				num = 0;
				break;
			}

			digit++;
		}
		else{
			num = 0;
			break;
		}
	}

	if(*digit == '\0'){
		g_nStatus = kValid;
	}

	return num;
}

int StrToInt(const char *str){
	g_nStatus = kInvalid;
	long long num = 0;

	if(str != NULL && *str != '\0'){
		bool minus = false;
		if(*str == '+')
			str++;
		else if(*str == '-'){
			str++;
			minus = true;
		}

		if(*str != '\0'){
			num = StrToIntCore(str, minus);
		}
	}

	return (int)num;
}
```

##### 面试题50：树中两个结点的最低公共祖先问题

情况一：建设该树是一颗二叉树，且是二叉搜索树

二叉搜索树是排过顺序的，位于左子树中的结点都比父节点小，而位于右子树的结点都比父节点大，我们只需要从树的根结点和两个输入的结点进行比较。如果当前结点的值比两个结点的值都大，那么最低的公共父节点一定是在当前结点的左子树中，则遍历当前节点的左子节点；反之，若当前节点的值比两个节点的值都小，那么最低的祖先节点一定在当前节点的右子树中，则遍历当前节点的右子节点；这样，直到找到一个节点，位于两个节点值的中间，则找到了最低的公共祖先节点。

``` C++
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};

TreeNode* lowestCommonAncestor(TreeNode *pRoot, TreeNode *pNode1, TreeNode *pNode2){
	if(pRoot == NULL || pNode1 == NULL || pNode2 == NULL)
		return NULL;

	if(pRoot->val > pNode1->val && pRoot->val > pNode2->val){
		return lowestCommonAncestor(pRoot->left, pNode1, pNode2);
	}
	else if(pRoot->val < pNode1->val && pRoot->val < pNode2->val){
		return lowestCommonAncestor(pRoot->right, pNode1, pNode2);
	}
	else{
		return pRoot;
	}
}
```

情况二： 题目中的树不是二叉搜索树，也不是二叉树，只是普通的树，但是树中的结点有指向父节点的指针。

为什么需要指向父节点的指针？

答：如果存在parent指针，则分别从输入的p节点和q节点指向root根节点，其实这就是两个单链表。问题转化为求两个单链表相交的第一个公共节点

``` C++
TreeNode* lowestCommonAncestor(TreeNode *pRoot, TreeNode *pNode1, TreeNode *pNode2){
	if(pRoot == NULL || pNode1 == NULL || pNode2 == NULL)
		return NULL;

	set<TreeNode*> node_set;

	while(pNode1 != pRoot){
		node_set.insert(pNode1);
		pNode1 = pNode1->parent;
	}
	path_node1.push_back(pRoot);

	while(pNode2 != pRoot){
		if(node_set.find(pNode2) != node_set.end()){
			return pNode2;
		}
		pNode2 = pNode2->parent;
	}
	return NULL;
}
```

情况三：树是一颗普通树，而且树中的结点没有指向父节点的指针。

1. 两个结点的公共祖先一定在从根结点，至这两个结点的路径上

2. 由于求公共祖先中的最近公共祖先，那么即同时出现在这两条路径上的离根结点最远的结点

3. 最终算法即：求p结点的路径，求q结点的路径，两路径上最后一个相同的结点。

``` C++
void preorder(TreeNode* node, TreeNode* search,
	vector<TreeNode*> &path,//遍历时的结点路径结果
	vector<TreeNode*> &result,//最终搜索到结点search的路径结果
	int &finish){//记录是否找到了结点search的变量，未找到时为0，找到为1
	if(!node || finish){
		return;
	}
	path.push_back(node);//先序遍历时，将结点压入path栈
	if(node == search){
		finish = 1;		//当找到search结点后，标记finish变量
		result = path;//当前的path存储到result中
	}
	preorder(node->left, search, path, result, finish);
	preorder(node->right, search, path, result, finish);
	path.pop_back();//结束遍历node时，将node结点弹出path栈
}

TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode *q){
	vector<TreeNode*> path;
	vector<TreeNode*> node_p_path;
	vector<TreeNode*> node_q_path;
	int finish = 0;
	preorder(root, p, path, node_p_path, finish);
	path.clear();
	finish = 0;
	preorder(root, q, path, node_q_path, finish);
	int path_len = 0;
	if(node_p_path.size() < node_q_path.size()){
		path_len = node_p_path.size();
	}
	else{
		path_len = node_q_path.size();
	}

	TreeNode *result = 0;
	for(int i = 0; i < path_len; i++){
		if(node_q_path[i] == node_p_path[i]){
			result = node_p_path[i];
		}
	}

	return result;
}
```


