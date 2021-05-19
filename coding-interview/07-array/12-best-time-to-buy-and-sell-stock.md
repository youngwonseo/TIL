# 주식을 사고팔기 가장 좋은 시점

LeetCode 121. Best Time to Buy and Sell Stock

## 브루트 포스

```java
class Solution {
    public int maxProfit(int[] prices) {
        int max = 0;
        for(int i=0; i < prices.length; i++) {
            for(int j=i+1; j < prices.length; j++) {
                int curr = prices[j] - prices[i];
                if (max < curr){
                    max = curr;
                }
            }   
        }
        return max;
    }
}
```

* leetcode에서 위 코드는 time limit exceeded가 발생

## 저점과 현재 값과의 차이 계산

```java
class Solution {
    public int maxProfit(int[] prices) {
        int profit = 0;
        int minPrice = Integer.MAX_VALUE;
        
        for(int i=0; i < prices.length; i++) {
            if(minPrice > prices[i]) {
                minPrice = prices[i];
            }
            
            if(prices[i] - minPrice > profit) {
                profit = prices[i] - minPrice;
            }
        }
        
        return profit;
    }
}
```
