---
title: "그래프 알고리즘 정리"
category: algorithm
---



# Dijkstra Algoritm

다익스트라 알고리즘은 간선에 가중치가 있는 그래프에서 1:N 최단거리를 구하는 알고리즘이다.



다익스트라 알고리즘의 아이디어는 **최단거리는** **최단거리로** **이루어져** **있다**는 그리디(Greedy)한 생각에서부터 출발한다.

다익스트라 알고리즘을 구현하는 방법은 **1)** **배열을 사용하여 구현하는 방법**과 **2)** **Min Heap(Priority Queue)를 사용하는 방법**이 있다.

배열을 사용할 경우 최단 거리를 뽑아내기 위해 선형 탐색을 해야하기 때문에 총 시간이 O(N2)이 나오기 때문에 비효율적이다.

반면 우선순위 큐를 사용하면 O(logN)으로 최단거리를 뽑아내어 총 O(NlogN)의 시간만에 알고리즘을 완료할 수 있다.



## 구현방법

**\# 필요한 자료구조**

```java
int[][] graph; // 그래프
int[] d; // 출발 위치부터 각각의 노드까지의 최단거리를 저장할 배열
PriorityQueue<Node> pq; // 현재 노드에 인접한 노드를 최단거리순으로 저장하는 큐 (minHeap)
```



**\# 알고리즘**

```java
import java.util.PriorityQueue;

/**
 * Created by jingyu on 2018. 10. 22..
 */
public class DijkstraTest {
    public static int INF = 10000000;

    public static void main(String[] args) {
        Graph g = new Graph(8);
        g.input(1, 2, 3);
        g.input(1, 5, 4);
        g.input(1, 4, 4);
        g.input(2, 3, 2);
        g.input(3, 4, 1);
        g.input(4, 5, 2);
        g.input(5, 6, 4);
        g.input(4, 7, 6);
        g.input(7, 6, 3);
        g.input(3, 8, 3);
        g.input(6, 8, 2);
        g.dijkstra(1);
    }

    private static class Graph {
        int size;
        int[][] graph;
        int[] d;
        PriorityQueue<Node> pq;

        public Graph(int n) {
            size = n;
            graph = new int[n + 1][n + 1];
            d = new int[n + 1];
            pq = new PriorityQueue<>();

            for (int i = 1; i <= n; i++) {
                for (int j = 1; j <= n; j++) {
                    if (i == j)
                        graph[i][j] = 0;
                    else
                        graph[i][j] = INF;
                }
            }
        }

        public void input(int n1, int n2, int w) {
            graph[n1][n2] = w;
            graph[n2][n1] = w;
        }

        public void dijkstra(int s) {
            //1
            for (int i = 1; i <= size; i++) {
                d[i] = graph[s][i];
            }

            //2
            for (int i = 1; i <= size; i++) {
                if (d[i] != INF) {
                    pq.offer(new Node(i, d[i]));
                }
            }

            //3
            while (!pq.isEmpty()) {
                Node curr = pq.poll();
                for (int i = 1; i <= size; i++) {
                    // 기존에 알고있던 길이보다 더 짧은 길이가 있으면 갱신
                    if (d[i] > d[curr.v] + graph[curr.v][i]) {
                        d[i] = d[curr.v] + graph[curr.v][i];
                        pq.offer(new Node(i, d[i]));
                    }
                }
            }

            for (int i = 1; i <= size; i++) {
                System.out.println(d[i]);
            }
        }

        class Node implements Comparable<Node> {
            int v, e;

            public Node(int v, int e) {
                this.v = v;
                this.e = e;
            }

            @Override
            public int compareTo(Node o) {
                return this.e - o.e;
            }
        }
    }
}
```



**dijkstra(int s) 메서드 설명**

1. 시작노드를 기준으로 최단거리 배열을 초기화한다.
2. 시작노드의 인접 노드를 우선순위 큐에 넣는다
3. 가중치가 가장 낮은 노드를 꺼내서 꺼낸 노드와 인접한 노드(새로운 노드)를 조사한다. 시작노드부터 꺼낸 노드까지의 최단 거리 + 꺼낸 노드부터 인접한 노드(새로운 노드)의 거리가 시작노드부터 새로운 노드까지의 거리보다 더 짧다면 그 거리를 갱신해주고 새로운 노드를 우선순위 큐에 넣어준다.
4. 3번을 반복한다.

 

## 음의 가중치가 있으면 성립하지 않는 이유

이전 노드까지 계산해둔 최소거리 값이 최소라고 보장할 수 없기때문이다. 다익스트라는 정점을 지날수록 가중치가 증가한다 라는 사실을 전제하고 쓰여진 알고리즘이다. 하지만 음의 가중치가 있다면 이미 최단거리라고 확정지은 정점이 최소가 아니게 될 수도 있다. 해결 방법으로는 음의 가중치중 가장 작은 값을 각각의 간선에 더해주고 다익스트라 알고리즘을 적용시키는 방법이 있는데  노드마다 음의 가중치에 영향을 받는것이 다르기 때문에 100% 제대로 동작을 보장할 수 없다. 음의 가중치가 있다면 벨만포드 알고리즘을 쓰자!



# Bellman-Ford Algoritm

One source to all destinations에 대한 shortest path 문제이다. Dijkstra와 다른 점은 여기서는 음수 edge가 존재할 수 있다는 것이다. 물론 음수 cycle은 아니다. (만약 negative cycle이 있을 경우 Bellman-Ford는 그 negative cycle의 weight를 반환한다)

또한 Dijkstra 보다 간단하며 분산 시스템에서 동작하기에 더 적합하다. 그리고 time complexity가 O(VE)인데 엄밀히 말하자면 Dijkstra 보다 좋다. 

방법론적으로는 Floyd-Warshall처럼 DP다. 즉, source와 하나의 destination을 잇는 최소 비용 경로는 경유지를 거치거나 거치지 않는 경우 중 최소값을 취하면 된다. 



두 번째 과정을 보자. 최초에는 하나의 edge를 사용하는 최소 거리를 구하고 그 다음은 최대 2개의 edge를 사용하는 최소 거리를 구한다. 이런식으로 최대 vertex개수 - 1만큼의 edge에 대해서 연산을 한다. (왜냐하면 모든 노드까지의 경로에서 최대 edge는 vertex 개수보다 하나 작기 때문)

세 번째 과정은 사실 부가적인 과정이다. 만일 문제에서 negative cycle이 없다는 것이 보장된다면 굳이 할 필요없다. 이 과정이 성립하는 이유는 두 번째 과정에서 이미 최소 거리를 구했다는 가정이 있기 때문이다. 따라서 모든 edge를 한 번 이상 순회하였을 때 어떤 vertex에 대한 path가 짧아진다면 negative cycle이 존재하는 것이다.

출처: http://greatzzo.tistory.com/50 [What a Great World!!]



## 구현방법

**수도코드**

```java
bellmanFord(src) {
    int[] dist = new int[V];
    //1
    foreach d in dist
        d = INF;
    dist[src] = 0;
    
    //2
    loof(V-1) {
        //3
        foreach e in G.edge {
            if(dist[e.src] != INF && dist[e.src] + e.weight < dist[e.dist]) {
                dist[e.dist] = dist[e.src] + e.weight                
            }
        }
    }
    
    //4
    foreach e in G.edge {
        if(dist[e.src] != INF && dist[e.src] + e.weight < dist[e.dist]) {
            // Graph contains nagative weight cycle!
        }
    }
        
}
```

1. 출발지는 0, 나머지는 무한으로 초기화해준다.
2. 간선의 갯수(V-1)만큼 루프를 돌면서
3. 모든 edge들에 대해 relax를 해준다.
4. 음의 cycled이 있는지 검사한다.



### 시간 복잡도 

 O(VE)





# Kruskal Algoritm

최소비용 신장트리 (MST)를 찾는 그리디한 알고리즘이다. 

크루스칼 알고리즘은 모든 간선들의 가중치만을 보아 작은 가중치를 골라 산발적으로 만들어진 트리의 조각들을 합쳐 스패닝 트리를 만든다.



## 구현방법

**수도코드**

```c
kruskal(G) {
	PriorityQueue pq;
	foreach e in G.E 
		pq.add(e)

	while(!pq.isEmpty) {
		e = pq.poll
        if (find(e.x) != find(e.y)) {
			union(e.x, e.y)
			weight += e.w
        }
	}
}
```

1. 그래프에 대한 모든 Edge를 우선순위 큐에 넣어서 weight의 오름차순이 되게 한다.

2. 우선순위 큐에서 Edge를 뽑으면서 Cycle이 생기지 않는 경우를 검사한다.
3. Cycle이 없으면 해당 Vertex를 union시켜주고 가중치합을 계산한다.



   

### 시간 복잡도 

edge의 sort 시간이 pq를 사용한 O(ElogE)이다.
edge는 최대가 V^2이므로 logE = 2logV 이다. 즉 O(ElogeE) = O(VlogV)

</br>

   



# Prim Algoritm

프림 알고리즘에서는 하나의 시작점으로 구성된 트리에 인접한 정점으로의 간선을 하나씩 추가하며 스패닝 트리가 될 때까지 키워 갑니다. 프림 알고리즘에서 간선을 추가하는 과정은 하나의 정점에 인접한 정점들을 조사하고 그 정점들까지의 간선을 모두 후보 간선으로 지정해놓은 후 다음 루프문에서 후보들 중 가장 작은 가중치의 간선을 선택하게 됩니다.

**[출처]** [Prim 프림 알고리즘](https://blog.naver.com/cdndgml/221298861374)|**작성자** [찌오](https://blog.naver.com/cdndgml)



## 구현방법

**수도코드**

```java
prim(G) {
    boolean visit[G.size];
    PriorityQueue pq;
    visit[startVertex] = true;
    
    // 시작점에 연결된 주변 노드들을 큐에 넣음
    foreach v,e in naivorhood of startVertex {
        pq.add(v,e);
    }
    
    while(!pq.isEmpty) {
        vertex, edge = pq.poll(); // 가장 짧은 거리순으로 뽑음
        if(visit[vertex]) continue; // 방문한 노드는 건너뜀 (greedy)
        visit[vertex] = true; // 방문기록
        foreach v,e in naivorhood of vertex { // 싸이클이 생기지 않는 주변 노드를 큐에 넣음
            if(!visit[v]) {
                pq.add(v,e)
            }
        }
    }
    
    // 정점이 모두 연결된 그래프일 경우 생략가능
    foreach v in visit {
        if (!v) return -1; // 방문 못한 노드가 있음
    }
}
```

시간 복잡도 : heap을 쓰면 O(ElogV), 아니면 O(V^2)



# Kruskal과 Prim 비교

프림과 크루스칼 알고리즘은 최소 스패닝 트리를 구하는 방법 중 하나이다.

\- 둘 다 Greedy Method을 기초로 하고 있다. (당장 눈앞의 최소 비용의 선택을 해서 결과적으로 최적의 Solution을 찾는다.)



\- 프림은 정점 위주의 알고리즘, 크루스칼은 간선 위주의 알고리즘이다.

\- 프림은 시작점을 정하고, 시작점에서 가까운 정점을 선택하면서 MST를 구성하므로 그 과정에서 사이클을 이루지 않지만

\- 크루스칼은 시작점을 정하지 않고, 최소 비용의 간선을 차례로 대입하면서 MST를 구성하므로, 그 과정에서 사이클을 이루는지 항상 확인해야 한다. 사이클을 확인하는 방법으로는 Union-Find(Disjoint-Set) 방법이 있다.



\- 시간 복잡도는 비슷하지만, 일반적으로 Dense한 그래프의 경우 프림이, 그렇지 않은 경우에는 크루스칼이 유리하다.

\- 프림의 경우 최소 거리의 정점을 찾는 부분에서 자료구조의 성능에 영향을 받는다.

\- 크루스칼은 간선을 weight 기준으로 정렬하는 과정이 오래 걸린다.

\- 프림 : O(V^2+E) → O(E logV)

\- 크루스칼 : O(E logE)

출처: 

http://stack07142.tistory.com/54

   



# Floyd Warshall Algoritm





# 참고

출처: <http://greatzzo.tistory.com/54>[What a Great World!!]