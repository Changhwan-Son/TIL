# 알고리즘 - 최소 신장 트리

# 최소 신장 트리 (Minimum Spanning Tree)

- Spanning Tree : 모든 꼭짓점(노드)을 포함하는 부분 그래프
- Minimum Spanning Tree : Spanning Tree 중 가중치 합이 최소인 트리

## 크루스칼 알고리즘 (Kruskal Algorithm)

- 최소 신장 트리를 찾는 알고리즘
- 가장 적은 비용으로 모든 노드를 연결하기 위해 사용하는 알고리즘

### 동작 과정

1. 모든 간선들을 가중치 기준으로 오름차순으로 정렬
2. 가중치가 가장 작은 간선 선택
3. 선택한 간선의 두 노드들이 서로 연결되지 않은 상태면 서로 연결
4. 이 과정을 반복

### 코드

```
import java.io.*;
import java.util.*;

public Class Kruskal {
    // 간선 클래스
    static class Edge implements Comparable<Edge> {
        int from, to, weight;   // 시작점, 끝점, 가중치

        public Edge(int from, int to, int weight) {
            this.from = from;
            this.to = to;
            this.weight = weight;
        }

        @Override
        public int compareTo(Edge o) {
            return Integer.compare(this.weight, o.weight);
        }
    }

    // 정점(노드) 개수 V, 간선 개수 E
    static int V, E;
    // 각 노드가 연결된 노드들 중 대표
    static int[] parents;
    // 간선 배열
    static Edge[] edgeList;

    // 각 꼭짓점(노드)의 연결된 노드들 중 대표를 자신으로 만든다.
    static void make() {
        for(int i = 0 ; i < V ; i++)
            parents[i] = i;
    }

    // 연결된 노드 중 대표 찾기
    static int findSet(int a) {
        if(parents[a] == a)
            return a;
        return parents[a] = findSet(parents[a]);    // 대표 찾으면서 대표 등록 한번에
    }

    // a 노드와 b 노드 연결 (a의 대표를 b의 대표로 등록하는 것과 같음 )
    // 이미 연결되어 있으면 false 리턴
    static boolean union(int a, int b) {
        int aRoot = findSet(a); // a 대표
        int bRoot = findSet(b); // b 대표
        if(aRoot == bRoot)      // 대표가 같으면 이미 연결되어 있는 거임
            return false;

        parents[bRoot] = aRoot; // b의 대표로 a의 대표를 등록
        return true;
    }

    public static void main(String[] args) thorws IOException {
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(in.readLine());

		V = Integer.parseInt(st.nextToken());
		E = Integer.parseInt(st.nextToken());

		parents = new int[V];
		edgeList = new Edge[E];

		for (int i = 0; i < E; i++) {
			st = new StringTokenizer(in.readLine());
			edgeList[i] = new Edge(Integer.parseInt(st.nextToken())-1, Integer.parseInt(st.nextToken())-1, Integer.parseInt(st.nextToken()));
		}

        // 1. 모든 간선들을 가중치 기준 오름차순으로 정렬
        Arrays.sort(edgeList);

        make();
        int result = 0;     // 가중치 합
        int count = 0;      // 선택한 간선 수

        for(Edge edge: edgeList){
            if(union(edge.from, edge.to)) { // 서로 연결되어있지 않다면 연결, 간선 등록
                result += edge.weight;
                if(++count == V - 1)        // 간선을 정점 -1 개 만큼 등록하면 끝임
                    break;
            }
        }

        System.out.println(result);
    }
}


```

## 프림 알고리즘 (Prim's Algorithm)

- 시작 정점(선택된 정점들)을 기준으로 가중치가 가장 작은 간선과 연결된 정점을 선택하며 신장 트리를 확장시켜나가는 알고리즘
- 정점(노드) 선택을 기반으로 하는 알고리즘

### 동작 과정

- 시작 정점 하나 선택 (신장 트리의 시작)
- 신장 트리에 연결되지 않은 정점들의 신장 트리와의 최소 가중치 계산
- 그 중 가장 작은 값을 가진 정점 선택해서 연결
- 모든 정점 선택할 때까지 과정 반복

```
import java.io.*;
import java.util.*;

public class Prim {

    public static void main(String[] args) NumberFormatException, IOException {
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));

        // 정점 개수 N
		int N = Integer.parseInt(in.readLine());
        // 간선 가중치 행렬
		int[][] adjMatrix = new int[N][N];
        // 신장 트리에 포함되었는지 저장 배열
		boolean[] visited = new boolean[N];
        // 각 정점들의 신장 트리와의 최소 가중치 배열
		int[] minEdge = new int[N];

		StringTokenizer st = null;
		for(int i = 0; i < N ; i++) {
			st =  new StringTokenizer(in.readLine());
			for(int j = 0 ; j < N ; j++) {
				adjMatrix[i][j] = Integer.parseInt(st.nextToken());
			}
			minEdge[i] = Integer.MAX_VALUE;     // 각 정점들의 최소 가중치
		}

        int result = 0;     // 가중치의 합
        minEdge[0] = 0;     // 시작점 0으로 잡고 시작

        for(int c = 0 ; c < N ; c++) {

            int min = Integer.MAX_VALUE;        // 최소 가중치
            int minVertex = 0;                  // 정점 번호
            // 신장 트리에 연결되지 않은 정점들 중 최소 가중치가 최소인 정점 선택
            for(int i = 0 ; i < N ; i++) {
                if(!visited[i] && min > minEdge[i]) {
                    min = minEdge[i];
                    minVertex = i;
                }
            }

            // 선택된 정점의 최소 가중치 더해주기
            result += min;
            // 신장 트리에 포함됨 체크
            visited[minVertex] = true;

            // 신장 트리와의 최소 가중치 다시 계산
            for(int i = 0 ; i < N ; i++) {
                if(!visited[i] && adjMatrix[minVertex][i] != 0 && minEdge[i] > adjMatrix[minVertex][i]) {
                    minEdge[i] = adjMatrix[minVertex][i];
                }
            }
        }
        System.out.println(result);
    }
}

```
