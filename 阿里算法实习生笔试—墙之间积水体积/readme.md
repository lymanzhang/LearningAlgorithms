## 阿里算法实习生笔试——墙之间积水体积[链接](http://www.jianshu.com/p/b554ed980b19)
![墙之间积水体积](https://github.com/lymanzhang/LearningAlgorithms/blob/master/%E9%98%BF%E9%87%8C%E7%AE%97%E6%B3%95%E5%AE%9E%E4%B9%A0%E7%94%9F%E7%AC%94%E8%AF%95%E2%80%94%E5%A2%99%E4%B9%8B%E9%97%B4%E7%A7%AF%E6%B0%B4%E4%BD%93%E7%A7%AF/volume.jpg)

### 初步分析

- 情况1：未遇到全局最大值之前
该情况只需要保存现在的截止目前的临时最大值tempMax，然后积水体积是tempMax-height[i]。   

![情况1](https://github.com/lymanzhang/LearningAlgorithms/blob/master/%E9%98%BF%E9%87%8C%E7%AE%97%E6%B3%95%E5%AE%9E%E4%B9%A0%E7%94%9F%E7%AC%94%E8%AF%95%E2%80%94%E5%A2%99%E4%B9%8B%E9%97%B4%E7%A7%AF%E6%B0%B4%E4%BD%93%E7%A7%AF/situation2.jpg)

- 情况2：已经遍历过了全局最大值，后面的数值虽然有局部极值，但没有再出现全局最大值   

![情况2](https://github.com/lymanzhang/LearningAlgorithms/blob/master/%E9%98%BF%E9%87%8C%E7%AE%97%E6%B3%95%E5%AE%9E%E4%B9%A0%E7%94%9F%E7%AC%94%E8%AF%95%E2%80%94%E5%A2%99%E4%B9%8B%E9%97%B4%E7%A7%AF%E6%B0%B4%E4%BD%93%E7%A7%AF/situation3.jpg)

- 情况3：已经遇到全局最大值，但是遇到了第二个极值比之前遇到的第一个极值要大，那么积水的体积就是全局最大值和第二次的遇到的极值（即次大值）之间的容积了
总结情况2和情况3，在第三次遇到的极值要和次大值比较。   

![情况3](https://github.com/lymanzhang/LearningAlgorithms/blob/master/%E9%98%BF%E9%87%8C%E7%AE%97%E6%B3%95%E5%AE%9E%E4%B9%A0%E7%94%9F%E7%AC%94%E8%AF%95%E2%80%94%E5%A2%99%E4%B9%8B%E9%97%B4%E7%A7%AF%E6%B0%B4%E4%BD%93%E7%A7%AF/situation4.jpg)

- 情况4：其实这里和情况1是一样的   

为什么这里会列出来这个呢？   

因为题目要求只能遍历一次数组，我们不确定在一次遍历的过程中是不是还能碰到全局最大值，所以，这里考虑局部极值和全局最大值的关系就显得尤为重要。   

相比情况2和情况3，在第三次遇到的极值要和最大值比较。


我们要在一次遍历的过程中判断局部极值点，然后比较局部极值点和暂时的最大值和次大值的大小关系，根据不同的情况来做出相应的动作。

### 反其道而行之

上面的分析看似把情况都考虑到了，但这使得问题扩散开来。尤其是当阶段性的极值不是最大值也不是次大值的情况就无法解决。所以，我们应该反其道而行之，换个角度来简化问题。   

我们一直在考虑当遇到最大值之后，如何进行比较来计算后续的蓄水容积的情况，但这样不如从最后设定一个下标来反向计算。于是这就形成了解决这个问题的核心思路：分别从头尾设置两个下标i，j，首先比较这两个下标对应的值的大小，由对应值小的那个下标先移动，并记录该下标的当前的临时最大值（比如i先移动，记录i_max），当遇到一个新的最大值的时候，不但要对i进行局部变量的清零，还要比较该最大值与j所对应值的大小关系，然后较小值对应的下标继续移动，直到两个下标相遇，遍历了一遍数组为止。

### 程序代码
```
int Volume(int* height, int n)
{
    int i = 0;
    int j = n-1;
    int i_max = height[i];
    int j_max = height[j];
    int volume = 0;
    const int i_walk = 1;
    const int j_walk = 2;
    //i_max <= j_max , i先走
    int who_walk = i_max <= j_max ? i_walk : j_walk;

    while(i != j)
    {
        if(who_walk == i_walk)
        {
            if(i_max > height[i])
            {
                volume += i_max-height[i];
            }
            else
            {
                i_max = height[i];
                if (i_max > j_max)
                {
                    who_walk = j_walk;
                    continue;
                }
            }
            ++i;
        }
        else if(who_walk == j_walk)
        {
            if(j_max > height[j])
            {
                volume += j_max-height[j];
            }
            else
            {
                j_max = height[j];
                if (j_max >= i_max)
                {
                    who_walk = i_walk;
                    continue;
                }
            }
            --j;
        }
    }
    return volume;
}
```
