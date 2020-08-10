---
title : "[백준] 1922 네트워크 연결"
toc : true
toc_sticky: true
categories :
    - 알고리즘
tag :
    - 백준
    - 알고리즘
    - union-find
    - kruskal
---

### union-find 정리
- 그래프 알고리즘이다.
- `union` : 집합을 합친다. 
- `find` : 어떤 집합에 포함되어있는지 찾는다.

연결 관계를 나타내는 배열을 초기화한다.
```java
int[] parent = new int[10+1];

for (int i=1; i<=10; i++){
    parent[i] = i;
}
```

연결 관계의 부모를 찾는다. `find`
```java
public int find(int x){
    if (x == parent[x])
        return x;
    else
        return parent[x] = find(parent[x]);
}
```

두 집합을 합친다. `union`
```java
public void union(int x, int y){
    x = find(x);
    y = find(y);
    
    if (x != y){
        if (x < y) parent[y] = x;
        else parent[x] = y;
    }
}
```

### kruskal 알고리즘 정리
- 가장 적은 비용으로 모든 노드를 연결하기 위한 알고리즘이다.
- union-find 알고리즘이 사용된다.

연결 관계, 비용을 멤버 변수로갖는 객체를 만들고 비용을 기준으로 오름차순 정렬한다.
적은 비용순서로 싸이클이 발생하지 않게 연결하여 최소 비용을 구한다.

```java
public class Node implements Comparable<Node>{
    int v1;
    int v2;
    int cost;
    
    public Node(int v1, int v2, int cost){
        this.v1 = v1;
        this.v2 = v2;
        this.cost = cost;
    }
    
    public int compareTo(Node n){
        return Integer.compare(this.cost, n.cost);
    }
}

public int find(int x){
    if (parent[x] == x)
        return x;
    else
        return parent[x] = find(parent[x]);
}

public void union(int x, int y){
    x = find(x);
    y = find(y);
    
    if (x != y){
        if (x < y) parent[y] = x;
        else parent[x] = y;
    }
}


public static void main(String[] args){
    List<Node> list = new ArrayList<>();
    int[] parent = new int[10+1];
    for (int i=1; i<=10; i++)
        parent[i] = i;
    list.add(1,2, 0);
    list.add(2,3, 1);
    list.add(3,1, 2);
    Collections.sort(list);
    
    int cost = 0;
    for (Node n : list){
        cost += union(n.v1, n.v2);
    }
}
```

### 풀이

```java
import java.util.*;

public class Main{

public static class Node implements Comparable<Node>{
    int v1;
    int v2;
    int cost;
    
    public Node(int v1, int v2, int cost){
        this.v1 = v1;
        this.v2 = v2;
        this.cost = cost;
    }
    
    public int compareTo(Node n){
        return Integer.compare(this.cost, n.cost);
    }
}

public static int find(int x, int[] parent){
    if (parent[x] == x)
        return x;
    else
        return parent[x] = find(parent[x], parent);
}

public static boolean union(int x, int y, int[] parent){
    x = find(x, parent);
    y = find(y, parent);
    
    if (x != y){
        parent[y] = x;
        return true;
    }
    return false;
}


public static void main(String[] args){
    Scanner sc = new Scanner(System.in);
    int v = Integer.parseInt(sc.nextLine());
    int e = Integer.parseInt(sc.nextLine());
    List<Node> list = new ArrayList<>();
    int[] parent = new int[v+1];
    for (int i=1; i<=v; i++)
        parent[i] = i;
    for (int i=0; i<e; i++){
        String input = sc.nextLine();
        int v1 = Integer.parseInt(input.split(" ")[0]);
        int v2 = Integer.parseInt(input.split(" ")[1]);
        int cost = Integer.parseInt(input.split(" ")[2]);
        list.add(new Node(v1, v2, cost));
    }
    Collections.sort(list);
    
    int cost = 0;
    for (Node n : list){
        if (union(n.v1, n.v2, parent))
            cost += n.cost;
    }
    System.out.println(cost);
}
}
```





