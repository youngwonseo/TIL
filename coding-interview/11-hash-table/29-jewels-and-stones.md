# 보석과 돌

```java
class Solution {
    public int numJewelsInStones(String jewels, String stones) {
        Map<Character, Integer> map = new HashMap<>();
        char[] chs = stones.toCharArray();
        
        for(int i=0;i<chs.length;i++) {
            if (map.get(chs[i]) == null){
                map.put(chs[i], 1);
            }else{
                map.put(chs[i], map.get(chs[i]) + 1);
            }
        }
        
        int result = 0;
        chs = jewels.toCharArray();
        for(int i=0;i<chs.length;i++) {
            if (map.get(chs[i]) != null) {
                result += map.get(chs[i]);
            }
        }
        
        return result;
    }
}
```
