# 使用js做LeetCode

## 概述

无意中得知了LeetCode这个刷题网站, 深得我意. 其实作为一个程序员, 我是很看重**写基础代码**的, 因为这个写熟了, 以后学各种语言就不会太困难. 所以我觉得有必要把这件事记下来, 供以后开发累了刷几道题, 相信对其他人也有用.

LeetCode有英文官网和中文官网, 由于我现在打算从题库开始刷, 而中文官网的题库是和英文官网的题库一样的, 所以我打算在**中文官网**刷了.

这是[我的中文LeetCode官网账号主页](https://leetcode-cn.com/sishenhei7/).

### 必要性

刷LeetCode的*必要性*:
- 看书看累了？刷几题吧～
- 心慌气短压力大？刷几题吧～
- 不知道要做啥？还是刷几题吧～
- 居家旅行，缓解压力，清空罪槽必备良药～

刷LeetCode的*好处*:
1. 熟悉原生js的基础方法.
2. 熟悉各种数据结构, 算法等, 为以后学习其它语言做准备.

### 示例

下面是我用两种方法实现第一题:[两数之和](https://leetcode-cn.com/problems/two-sum/description/). 感觉**收获蛮大**的, 熟悉了Array的各种方法.

题目:

```
给定一个整数数列，找出其中和为特定值的那两个数。

你可以假设每个输入都只会有一种答案，同样的元素不能被重用。

示例:

给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

我的解答:

```
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
//第一种方法
var twoSum1 = function(nums, target) {
    for(let i = 0, j = nums.length; i <= j; i++ ) {       
        for(let k = i + 1; k <= j; k++) {
            if(nums[i] + nums[k] === target) {
                return [i, k];
            }
        }
    }
};

//第二种方法
var twoSum2 = function(nums, target) {
    let x, y, z = nums.length;
    nums.some((item1, index1) => {
        return nums.slice(index1 + 1, z).some((item2,index2) => {
                if(item1 + item2 === target) {
                    x = index1;
                    y = index1 + index2 + 1;
                    return true;
                }
        })
    });
    if(typeof(x) === 'undefined') {
        return '没找到!';
    } else {
        return [x, y];
    }
};

//计算用时
var getTime = function(func, nums, target) {
    let start = new Date();
    console.log(func(nums, target));
    let end = new Date();
    console.log('运行时间: ' + (end - start));
}

//test
let nums = [2, 3, 5, 6, 4, 9, 2, 7, 3, 8],
    target = 17;
getTime(twoSum1, nums, target);
getTime(twoSum2, nums, target);
```


































