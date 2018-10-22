---
title: "다익스트라 알고리즘"
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

 

### 음의 가중치가 있으면 성립하지 않는 이유

이전 노드까지 계산해둔 최소거리 값이 최소라고 보장할 수 없기때문이다. 다익스트라는 정점을 지날수록 가중치가 증가한다 라는 사실을 전제하고 쓰여진 알고리즘이다. 하지만 음의 가중치가 있다면 이미 최단거리라고 확정지은 정점이 최소가 아니게 될 수도 있다. 해결 방법으로는 음의 가중치중 가장 작은 값을 각각의 간선에 더해주고 다익스트라 알고리즘을 적용시키는 방법이 있는데  노드마다 음의 가중치에 영향을 받는것이 다르기 때문에 100% 제대로 동작을 보장할 수 없다. 음의 가중치가 있다면 벨만포드 알고리즘을 쓰자!