# leetCode

# 209 长度最小的子数组

给定一个含有 **n** 个正整数的数组和一个正整数 **s ，**找出该数组中满足其和 **≥ s** 的长度最小的子数组，并返回其长度**。**如果不存在符合条件的子数组，返回 0。

```
输入：s = 7, nums = [2,3,1,2,4,3]
输出：2
解释：子数组 [4,3] 是该条件下的长度最小的子数组。
```



滑动窗口

```javascript
var minSubArrayLen = function(s, nums) {
    let arr = []
    let min = nums.length
    let add = 0
    let bool = false
    for(let i = 0; i < nums.length ; i++ ) {
        arr.push(nums[i])
        add = add + nums[i]
        while(add >= s) {
            bool = true
            min = Math.min(min, arr.length)
            add = add - arr[0]
            arr.shift()
        }
    }
    if(bool) return min
    return 0
};
```





# 94 二叉树中序遍历

中序

```javascript
var inorderTraversal = function(root) {
let arr = []
let func = (root) => {
    if(root) {
        func(root.left)
        arr.push(root.val)
        func(root.right)
    }
}
func(root)
return arr
};
```





# 226 翻转二叉树

```javascript
var invertTree = function(root) {
    if(!root) return null
    if(root) {
        let temp = root.left
        root.left = root.right
        root.right = temp
    }
    invertTree(root.left)
    invertTree(root.right)
    return root
};
```





# 206 翻转链表

```javascript
var reverseList = function(head) {
    if(!head) return null
    let pred
    let next
    while(head) {
        next = head.next
        head.next = pred
        pred = head
        head = next
    }
    return pred
};
```





# 83 删除有序链表中的重复元素

给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。重点是有序。

```javascript
var deleteDuplicates = function(head) {
    if(!head) return null
    let first = head
    let pred, next 
    while(head) {
        if(pred && (pred.val === head.val)) {
            pred.next = head.next
        } else {
            pred = head
        }
        head = head.next
    }
    return first
};
```





# 3 无重复字符的最长子串

两个指针创造滑动窗口

```javascript
var lengthOfLongestSubstring = function(s) {
  if(s.length === 0) return 0
  if(s.length === 1) return 1
  let p = 0
  let str = s[0]
  let res = 1 // 目前最长子字符串长度
  for(let i = 1 ; i < s.length ; i++){
    if(str.indexOf(s[i]) > -1) {
      p = str.indexOf(s[i])+1
    }
    str = str + s[i]
    str = str.slice(p, i+1)
    res = Math.max(res, str.length)
    p = 0 // 重置p
  }
  return res
};
```

