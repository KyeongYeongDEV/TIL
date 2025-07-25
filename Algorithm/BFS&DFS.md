# BFS와 DFS

| 항목 | BFS (Breadth-First Search) | DFS (Depth-First Search) |
| --- | --- | --- |
| 탐색 방향 | **가까운 노드부터** 탐색 | **깊은 노드부터** 탐색 |
| 자료구조 | **Queue** (FIFO) 사용 | **Stack** (재귀 or 명시적 Stack) 사용 |
| 경로 찾기 | 최단 경로 보장 (가중치 없을 때) | 최단 경로 보장 X |
| 재귀 사용 | X | 재귀 또는 명시적 스택 |
| 메모리 사용 | 보통 더 많음 (넓게 퍼짐) | 보통 더 적음 (깊게 탐색) |
| 사용 예시 | 최단 거리, 레벨 탐색 | 미로 탐색, 백트래킹 |

![image.png](./assets/bfs:dfs.png)

# 구현

## C++

```cpp
#include <iostream>
#include <queue>
#include <stack>
#include <unordered_map>
#include <vector>
#include <set>

using namespace std;

// 그래프: 문자 노드 연결
unordered_map<char, vector<char>> graph = {
    {'A', {'B', 'C'}},
    {'B', {'D', 'E'}},
    {'C', {'F'}},
    {'D', {}},
    {'E', {}},
    {'F', {}}
};

void bfs(char start) {
    queue<char> q;
    set<char> visited;

    q.push(start);

    while (!q.empty()) {
        char node = q.front(); q.pop();

        if (visited.count(node)) continue;

        cout << node << ' ';
        visited.insert(node);

        for (char neighbor : graph[node]) {
            q.push(neighbor);
        }
    }
}

void dfs(char start) {
    stack<char> s;
    set<char> visited;

    s.push(start);

    while (!s.empty()) {
        char node = s.top(); s.pop();

        if (visited.count(node)) continue;

        cout << node << ' ';
        visited.insert(node);

        // 역순으로 push해야 DFS 순서 유지됨
        for (auto it = graph[node].rbegin(); it != graph[node].rend(); ++it) {
            s.push(*it);
        }
    }
}

int main() {
    cout << "[BFS] ";
    bfs('A'); // A B C D E F
    cout << endl;

    cout << "[DFS] ";
    dfs('A'); // A B D E C F
    cout << endl;

    return 0;
}

```

## JAVA

```java
import java.util.*;

public class GraphSearch {
    static Map<Character, List<Character>> graph = new HashMap<>();

    public static void main(String[] args) {
        graph.put('A', Arrays.asList('B', 'C'));
        graph.put('B', Arrays.asList('D', 'E'));
        graph.put('C', Arrays.asList('F'));
        graph.put('D', new ArrayList<>());
        graph.put('E', new ArrayList<>());
        graph.put('F', new ArrayList<>());

        System.out.print("[BFS] ");
        bfs('A'); // A B C D E F
        System.out.println();

        System.out.print("[DFS] ");
        dfs('A'); // A B D E C F
        System.out.println();
    }

    public static void bfs(char start) {
        Queue<Character> queue = new LinkedList<>();
        Set<Character> visited = new HashSet<>();

        queue.add(start);

        while (!queue.isEmpty()) {
            char node = queue.poll();

            if (visited.contains(node)) continue;

            System.out.print(node + " ");
            visited.add(node);

            for (char neighbor : graph.get(node)) {
                queue.add(neighbor);
            }
        }
    }

    public static void dfs(char start) {
        Stack<Character> stack = new Stack<>();
        Set<Character> visited = new HashSet<>();

        stack.push(start);

        while (!stack.isEmpty()) {
            char node = stack.pop();

            if (visited.contains(node)) continue;

            System.out.print(node + " ");
            visited.add(node);

            List<Character> neighbors = graph.get(node);
            // 역순 push: DFS 순서 유지
            for (int i = neighbors.size() - 1; i >= 0; i--) {
                stack.push(neighbors.get(i));
            }
        }
    }
}

```
