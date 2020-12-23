石子游戏 VII
石子游戏中，爱丽丝和鲍勃轮流进行自己的回合，爱丽丝先开始 。

有 n 块石子排成一排。每个玩家的回合中，可以从行中 移除 最左边的石头或最右边的石头，并获得与该行中剩余石头值之 和 相等的得分。当没有石头可移除时，得分较高者获胜。

鲍勃发现他总是输掉游戏（可怜的鲍勃，他总是输），所以他决定尽力 减小得分的差值 。爱丽丝的目标是最大限度地 扩大得分的差值 。

给你一个整数数组 stones ，其中 stones[i] 表示 从左边开始 的第 i 个石头的值，如果爱丽丝和鲍勃都 发挥出最佳水平 ，请返回他们 得分的差值 。


示例 1：

输入：stones = [5,3,1,4,2]
输出：6
解释：
- 爱丽丝移除 2 ，得分 5 + 3 + 1 + 4 = 13 。游戏情况：爱丽丝 = 13 ，鲍勃 = 0 ，石子 = [5,3,1,4] 。
- 鲍勃移除 5 ，得分 3 + 1 + 4 = 8 。游戏情况：爱丽丝 = 13 ，鲍勃 = 8 ，石子 = [3,1,4] 。
- 爱丽丝移除 3 ，得分 1 + 4 = 5 。游戏情况：爱丽丝 = 18 ，鲍勃 = 8 ，石子 = [1,4] 。
- 鲍勃移除 1 ，得分 4 。游戏情况：爱丽丝 = 18 ，鲍勃 = 12 ，石子 = [4] 。
- 爱丽丝移除 4 ，得分 0 。游戏情况：爱丽丝 = 18 ，鲍勃 = 12 ，石子 = [] 。
得分的差值 18 - 12 = 6 。
示例 2：

输入：stones = [7,90,5,1,100,10,10,2]
输出：122
 

提示：

n == stones.length
2 <= n <= 1000
1 <= stones[i] <= 1000


设dp[i][j]表示从第i个石头到第j个石头中Alice，Bob所能获取的最大分差
则dp[1][n] 即为我们最后的答案

自己看到的最好的题解：

解题思路：
首先明确——谁是先手谁的得分就最大.
- 对于 dp[i][j] 定义为区间 [i,j] 我们要的结果，在区间 [i, j]，dp[i][j] = 先手的总分 - 后手的总分。

- 如果 dp[i][j]这个区间当前是鲍勃操作，那么鲍勃的得分一定最大。
选择去掉 stones[i] 后当前的分数为 sum(stones[i + 1], stones[j]).
那么区间 [i + 1, j]鲍勃的得分是多少呢？不用管它，dp[i + 1][j] 一定为对手爱丽丝作为先手得到的结果，因为谁先手谁的得分最大，则 dp[i + 1][j] = 爱丽丝得分 - 鲍勃的得分。


- sum(stones[i + 1], stones[j]) - dp[i + 1][j]
= 鲍勃当前操作得分 - （爱丽丝的总分 - 鲍勃的总分）
= 鲍勃当前操作得分 + 鲍勃的总分 - 爱丽丝的总分
= 鲍勃新的总分 - 爱丽丝的总分 > 0（谁先手谁最大）。
如果去掉 stones[j] 则原理同上.

- 如果当前 dp[i][j] 是爱丽丝，则将上面的叙述中爱丽丝和鲍勃名字互换。
对于爱丽丝我们很好理解为什么要最大化
dp[i][j] = max(sum(stones[i + 1], stones[j]) - dp[i + 1][j], sum(stones[i], stones[j - 1]) - dp[i][j - 1]);

- 那么鲍勃为什么也要最大化 dp[i][j] 呢，因为爱丽丝先手，鲍勃必输，题目给出了。所以只有当鲍勃操作时 dp[i][j] 最大，才能让爱丽丝操作时得到的结果最小,满足鲍勃的野心
爱丽丝当前操作得分 - (鲍勃的总分 - 爱丽丝的总分)(鲍勃操作时的最大化差值)
- 基础情况为只有 2 堆石子，值最大的那堆为答案，所以从只有2堆石子开始往上进行状态转移。


```java
//out of time
class Solution {
    public int stoneGameVII(int[] stones) {
        int sum = 0;
        for (int st:stones) sum+=st;
        return diff(stones,0,stones.length-1,sum);

    }
    private int diff(int[] stones,int left,int right,int sum){
        if (left == right){
            return 0;
        }
        int gainLeft = sum-stones[left];
        int DiffRemoveLeft = gainLeft-diff(stones,left+1,right,gainLeft);
        int gainRight = sum - stones[right];
        int DiffRemoveRight = gainRight-diff(stones,left,right-1,gainRight);
        return Math.max(DiffRemoveLeft,DiffRemoveRight);
    }
}

//解法2
class Solution {
    public int stoneGameVII(int[] stones) {
         int n = stones.length ;

        int[] sum = new int[n + 1];

        for (int i = 0; i < n; i++)
            sum[i + 1] = sum[i] + stones[i];

        int[][] dp = new int[n + 1][n + 1];
        for (int len = 2; len <= n; ++len) {
            for (int i = 1, j = i + len - 1; j <= n; ++i, ++j) {
                dp[i][j] = Math.max(sum[j] - sum[i] - dp[i + 1][j], sum[j - 1] - sum[i - 1] - dp[i][j - 1]);
            }
        }
        return dp[1][n];

    }
}

```
作者：jiang-xian-sen-3
链接：https://leetcode-cn.com/problems/stone-game-vii/solution/cbo-yi-si-lu-xiang-jie-by-jiang-xian-sen-j2tj/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。




来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/stone-game-vii
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
