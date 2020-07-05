---
title : "[leetcode] Shortest Palindrome"
toc : true
toc_sticky: true
categories :
    - 알고리즘
tag :
    - leetcode
    - 알고리즘
---
### 잘못된 접근 방식

문자열의 중간에 포인터를 두고 양쪽으로 넓혀가면서 palindrome을 찾으려고 했다.

```java
Input: "aacecaaa"
Input: "aabbc"
Input: "accccad"
```

### 해결 방식

1.  문자열의 시작부터 끝까지 탐색하며 부분 문자를 생성한다.
2.  부분 문자열이 palindrome인지 확인한다.
3.  palindrome이 맞다면 해당 index를 변수에 저장한다.
4.  반복문을 빠져나왔을때 index+1 ~ 문자열끝을 뒤집어 앞에 붙인 후 반환한다.

### 최종

```java
    public String shortestPalindrome(String s) {
        int length = s.length();
        if (length == 1)
            return s;
        if (s.equals(""))
            return "";
        int mid = s.length()/2;
        int end = s.length()/2;
        int start = end-1;
        int check = 0;
        for (int i=2; i<=length; i++){
            String substring = s.substring(0,i);
            String reverse =
                new StringBuffer(substring.substring(0,i/2)).reverse().toString();
            if (i % 2 == 0){
                if (s.startsWith(reverse, i/2))
                    check = i;
            }else {
                if (s.startsWith(reverse, (i/2)+1))
                    check = i;
            }
        }
        String answer = "";
        if (check == 0){
            answer = 
                new StringBuffer(s.substring(1,s.length())).reverse().toString() + s;
        }else{
            System.out.println(check);
            String add = "";
            for (int i=check; i<length; i++){
                add = s.charAt(i) + add;
            }
            answer = add + s;
        }
        return answer;
    }
```