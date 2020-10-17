# 题解
## 方法一：
对于二叉搜索树，如果进行中序遍历，那么得到的序列是有序的，如果有两个节点交换了，那么就是无序的，我们只需要找出这两个节点所在的位置然后交换值就好了。

先来看看交换两个值会发生什么。对于递增序列a=[1,2,3,4,5]
- 如果交换**不相邻**的数字，例如swap(a[1],a[3]),那么a=[1,4,3,2,5],此时a[1]>a[2]&&a[2]>a[3],存在两个非递增序列。
- 如果交换**相邻**的数字，例如swap(a[1],a[2]),那么a=[1,3,2,4,5],此时a[1]>a[2],只存在一个非递增序列。

### 代码
#### 首先讲一下pair的用法
```
pair<T,U> p1;
p1.first=value1;
p1.second=value2;

pair<T,U> p2(value1,value2);

pair<int,int> p3=make_pair(value1,value2);

//pair作为函数返回值
pair<int,int> function(int x,int y){
	//注意是大括号
	return {x,y};
}

```
#### 代码
```
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

	//中序遍历，把中序遍历的序列存入nums数组中
    void inorder(TreeNode* root,vector<int>& nums){
        if(root==nullptr)
            return;
        inorder(root->left,nums);
        nums.push_back(root->val);
        inorder(root->right,nums);
    }
    
	//返回中序遍历数组中应该交换的两个值(x,y)
    pair<int,int>findTwoSwapped(vector<int>& nums){
        int n=nums.size();
        int x=-1,y=-1;
        for(int i=0;i<n-1;++i){
        	//如果存在逆序对
            if(nums[i]>nums[i+1]){
                y=nums[i+1];
                //如果x==-1，那么x还没有找到，否则若x!=-1,说明已经找到正确的x，此时(x,y)已经找到，退出就好
                if(x==-1)
                    x=nums[i];
                else
                    break;
            }
        }
        return {x,y};
        
    }

	//在树中swap(x,y)，其中count用来记录修改的次数，应该修改两次，即count=2
    void recover(TreeNode* root,int count,int x,int y){
        if(root){
            if(root->val==x||root->val==y){
            	//如果当前节点的值为x，那么另它为y，否则若当前节点的值为y，那么另它为x
                root->val=root->val==x?y:x;
                //如果count==0说明x和y已经修改完毕(修改了两次)
                if(--count==0)
                    return;
            }
            recover(root->left,count,x,y);
            recover(root->right,count,x,y);
        }
    }

    void recoverTree(TreeNode* root) {
        vector<int> nums;
        inorder(root,nums);
        pair<int,int> swapped=findTwoSwapped(nums);
        recover(root,2,swapped.first,swapped.second);
    }
};
```
## 方法二：
通过非递归的中序遍历找出(x,y),设置一个指针pred指向当前节点的root的前驱，如果pred->val>root->val，说明x=pred，x此时就找到了，继续按相同的方法找y即可。

最后swap(x->val,y->val)
### 首先回顾一下中序遍历的两种非递归写法：
#### 第一种
```
void InOrder(TreeNode *root){
	//空树
    if(!root)
    	return;
    TreeNode* p=root;
    stack<TreeNode> s;
    //当p为root时，s为空
    while(!s.empty()||p){
    	//一直遍历到最左边的节点
    	while(p){
        	s.push(p);
            p=p->left;
        }
        //此时p==nullptr，可以访问p的父节点以及右孩子了
        if(!s.empty()){
        	p=s.top();
            s.pop();
            visit(p);
            p=p->right;
       	}
    }
}
```
#### 第二种
```

void InOrder2(TreeNode* root){
	//空树
	if (!root)
		return;
	//树非空
	TreeNode* p = root;
	stack<TreeNode*> s;
	while (!s.empty() || p){
		if (p){
			s.push(p);
			p = p->left;
		}
		else{
			p = s.top();
			s.pop();
			visit(p);
			p = p->right;
		}
	}
```
### 代码
#### 利用第一种中序遍历非递归写法
```
class Solution {
public:
    void recoverTree(TreeNode* root){
        stack<TreeNode*> s;
        TreeNode* x=nullptr;
        TreeNode* y=nullptr;
        TreeNode* pred=nullptr;
        TreeNode* p=root;
        while(!s.empty()||p){
            while(p){
                s.push(p);
                p=p->left;
            }
            if(!s.empty()){
                p=s.top();
                s.pop();
                if(pred&&p->val<pred->val){
                    y=p;
                    if(x==nullptr)
                        x=pred;
                    else break;
                }
                pred=p;
                p=p->right;
            }
        }
        swap(x->val,y->val);
    }
};
```
#### 利用第二种非递归中序遍历写法
```
class Solution {
public:
    void recoverTree(TreeNode* root){
        stack<TreeNode*> s;
        TreeNode* x=nullptr;
        TreeNode* y=nullptr;
        TreeNode* pred=nullptr;
        TreeNode* p=root;
        while(!s.empty()||p){
            if(p){
                s.push(p);
                p=p->left;
            }
            else{
                p=s.top();
                s.pop();
                if(pred&&p->val<pred->val){
                    y=p;
                    if(x==nullptr)
                        x=pred;
                    else break;
                }
                pred=p;
                p=p->right;
            }
        }
        swap(x->val,y->val);
    }
};
```
题目链接(https://leetcode-cn.com/problems/recover-binary-search-tree/)
