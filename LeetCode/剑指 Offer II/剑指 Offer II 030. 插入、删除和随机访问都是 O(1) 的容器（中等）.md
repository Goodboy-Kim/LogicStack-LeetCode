### 题目描述

这是 LeetCode 上的 **[剑指 Offer II 030. 插入、删除和随机访问都是 O(1) 的容器](https://leetcode-cn.com/problems/FortPu/solution/by-ac_oier-rls4/)** ，难度为 **中等**。

Tag : 「数据结构」、「哈希表」



实现 `RandomizedSet` 类：

* `RandomizedSet()` 初始化 `RandomizedSet` 对象
* `bool insert(int val)` 当元素 `val` 不存在时，向集合中插入该项，并返回 `true`；否则，返回 `false`。
* `bool remove(int val)` 当元素 `val` 存在时，从集合中移除该项，并返回 `true`；否则，返回 `false`。
* `int getRandom()` 随机返回现有集合中的一项（测试用例保证调用此方法时集合中至少存在一个元素）。每个元素应该有 相同的概率 被返回。

你必须实现类的所有函数，并满足每个函数的 平均 时间复杂度为 $O(1)$ 。

示例：
```
输入
["RandomizedSet", "insert", "remove", "insert", "getRandom", "remove", "insert", "getRandom"]
[[], [1], [2], [2], [], [1], [2], []]

输出
[null, true, false, true, 2, true, false, 2]

解释
RandomizedSet randomizedSet = new RandomizedSet();
randomizedSet.insert(1); // 向集合中插入 1 。返回 true 表示 1 被成功地插入。
randomizedSet.remove(2); // 返回 false ，表示集合中不存在 2 。
randomizedSet.insert(2); // 向集合中插入 2 。返回 true 。集合现在包含 [1,2] 。
randomizedSet.getRandom(); // getRandom 应随机返回 1 或 2 。
randomizedSet.remove(1); // 从集合中移除 1 ，返回 true 。集合现在包含 [2] 。
randomizedSet.insert(2); // 2 已在集合中，所以返回 false 。
randomizedSet.getRandom(); // 由于 2 是集合中唯一的数字，getRandom 总是返回 2 。
```

提示：
* $-2^{31} <= val <= 2^{31} - 1$
* 最多调用 `insert`、`remove` 和 `getRandom` 函数 $2 * 10^5$ 次
* 在调用 `getRandom` 方法时，数据结构中 至少存在一个 元素。

---

### 哈希表 + 删除交换

对于 `insert` 和 `remove` 操作容易想到使用「哈希表」来实现 $O(1)$ 复杂度，但对于 `getRandom` 操作，比较理想的情况是能够在一个数组内随机下标进行返回。

将两者结合，我们可以将哈希表设计为：以入参 `val` 为键，数组下标 `loc` 为值。

**为了确保严格 $O(1)$，我们不能「使用拒绝采样」和「在数组非结尾位置添加/删除元素」。**

因此我们需要申请一个足够大的数组 `nums`（利用数据范围为 $2* 10^5$），并使用变量 `idx` 记录当前使用到哪一位（即下标在 $[0, idx]$ 范围内均是存活值）。

对于几类操作逻辑：

* `insert` 操作：使用哈希表判断 `val` 是否存在，存在的话返回 `fasle`，否则将其添加到 `nums`，更新 `idx`，同时更新哈希表；
* `remove` 操作：使用哈希表判断 `val` 是否存在，不存在的话返回 `false`，否则从哈希表中将 `val` 删除，同时取出其所在 `nums` 的下标 `loc`，然后将 `nums[idx]` 赋值到 `loc` 位置，并更新 `idx`（含义为将原本处于 `loc` 位置的元素删除），同时更新原本位于 `idx` 位置的数在哈希表中的值为 `loc`（若 `loc` 与 `idx` 相等，说明删除的是最后一个元素，这一步可跳过）；
* `getRandom` 操作：由于我们人为确保了 $[0, idx]$ 均为存活值，因此直接在 $[0, idx + 1)$ 范围内进行随机即可。

代码：
```Java
class RandomizedSet {
    static int[] nums = new int[200010];
    Random random = new Random();
    Map<Integer, Integer> map = new HashMap<>();
    int idx = -1;
    public boolean insert(int val) {
        if (map.containsKey(val)) return false;
        nums[++idx] = val;
        map.put(val, idx);
        return true;
    }
    public boolean remove(int val) {
        if (!map.containsKey(val)) return false;
        int loc = map.remove(val);
        if (loc != idx) map.put(nums[idx], loc);
        nums[loc] = nums[idx--];
        return true;
    }
    public int getRandom() {
        return nums[random.nextInt(idx + 1)];
    }
}
```
* 时间复杂度：所有操作均为 $O(1)$
* 空间复杂度：$O(n)$

---

### 最后

这是我们「刷穿 LeetCode」系列文章的第 `剑指 Offer II 030` 篇，系列开始于 2021/01/01，截止于起始日 LeetCode 上共有 1916 道题目，部分是有锁题，我们将先把所有不带锁的题目刷完。

在这个系列文章里面，除了讲解解题思路以外，还会尽可能给出最为简洁的代码。如果涉及通解还会相应的代码模板。

为了方便各位同学能够电脑上进行调试和提交代码，我建立了相关的仓库：https://github.com/SharingSource/LogicStack-LeetCode 。

在仓库地址里，你可以看到系列文章的题解链接、系列文章的相应代码、LeetCode 原题链接和其他优选题解。

