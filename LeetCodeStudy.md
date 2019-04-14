# two sum
该题是给定一个数组，和一个目标数，要求返回数组中相加等于目标数的元素的下标。

示例：
nums=[2,7,11,15],target=9
因为nums[0]+nums[1] = 2+7 = 9
所以应该返回[0,1]

看到该题的第一反应是使用嵌套循环，对数组中的每一个元素，都循环遍历其他元素，并相加和目标数做比较。这样的做法时间复杂度为O(n^2)。

leetcode上的第二个解答是采用哈希表的方式，将nums中的元素储存在一个hashMap中，元素的下标为map的value，元素的值为map的key。代码如下：
``` C++
vector<int> Solution::twoSum(vector<int>& nums, int target) {
	vector<int> result;
	map<int,int> tmpMap;

    int index = 0;
	for (auto i : nums) {
		tmpMap[index] = i;
		index++;
	}

	for (auto i : tmpMap) {
		int tmpResult = target - i.first;

		if ((tmpMap.find(tmpResult) != tmpMap.end())&& (*(tmpMap.find(tmpResult)) != i) && (find(result.begin(),result.end(),(*tmpMap.find(tmpResult)).second))==result.end()) {
			result.push_back(i.second);
			result.push_back((*tmpMap.find(tmpResult)).second);			
		}
	}

	return result;
}
```

该方法中，循环里的if判断的最后一个条件find(result.begin(),result.end(),(*tmpMap.find(tmpResult)).second))==result.end()是由自己添加的。如果没有这一个判断条件，则在最后的结果中会出现重复结果。如示例中的情况，会返回[0,1,1,0].

该方法还有一个缺点，即不支持nums中存在重复的数字。因为采用nums中的元素值作为下标，所以如果nums中有重复的数字，则后一个元素会覆盖掉前一个相同的元素。

如nums=[3,3],target=6，则该方法的输出为[].

第三个改进的方法代码如下：
```C++
vector<int> Solution::twoSum(vector<int>& nums, int target) {
	vector<int> result;
	map<int,int> tmpMap;
	for (int i = 0; i < nums.size(); i++) {
		int tmpResult = target - nums[i];
		map<int, int>::iterator findIt = tmpMap.find(tmpResult);
		if (findIt == tmpMap.end()) {
			tmpMap[nums[i]] = i;
		}
		else {
			result.push_back(i);
			result.push_back((*findIt).second);
		}
	}
    	return result;
}
```

该方法在遍历nums中的元素，向map中插入的时候，会进行判断。如果该元素无法和已经在map中的元素匹配成功，则将该元素插入，否则的话直接作为结果输出，不再插入map中。这样就可以避免重复元素覆盖的问题。

但同样，该方法的问题在于如果nums=[3,3,3]，target=6，则输出结果为[0,1,0,2]，即第一个元素会出现两次。因此可以改进，当在map中的元素匹配成功时，则将该元素从map中删除。

#add two numbers
给定两个用链表代表的整数，其中每个节点包含一个数字。数字存储按照在原来整数中相反的顺序，使得第一个数字位于链表的开头。写出一个函数将两个整数相加，用链表的形式返回和。、

示例：
(2->4->3)+(5->6->4)
输出7->0->8
即342+465 = 807

该题是从前到后模拟加法的过程。注意单独记录每次相加的进位。每次加法使用对应节点上的数字加上进位，对10取模得到该位置的值，除以10得到的商即为新的进位。

另外，在两个链表都遍历结束之后，注意判断是否还有进位，如果有，则需要新建一个节点存储该进位。

代码如下：
```C++
class ListNode {
public:
	int val;
	ListNode *next;
	ListNode(int x) : val(x),next(NULL) {}
};


ListNode *AddTwoNums::addTwoNums(ListNode *l1, ListNode *l2) {
	ListNode *dummy = new ListNode(0);
	ListNode *tail = dummy;

	int carry = 0;
	for (ListNode *i = l1, *j = l2; i != NULL || j != NULL || carry != 0;) {
		int sum  = carry;
		sum += (i == NULL) ?  0 : i->val;
		sum += (j == NULL) ? 0 : j->val;
		tail->next = new ListNode(sum % 10);
		tail = tail->next;
		carry = sum / 10;

		i = (i == NULL) ? i : i->next;
		j = (j == NULL) ? j : j->next;
	}

	if (carry != 0) {
		tail->next = new ListNode(carry);
	}

	return dummy->next;
}
```