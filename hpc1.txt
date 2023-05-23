#include <iostream>
#include <vector>
#include <queue>
#include <stack>
#include <omp.h>

using namespace std;

// Graph class representing an undirected graph using adjacency list representation
class Graph {
private:
    int numVertices;          // Number of vertices
    vector<vector<int>> adj;  // Adjacency list

public:
    Graph(int vertices) : numVertices(vertices), adj(vertices) {}

    // Add an edge between two vertices
    void addEdge(int src, int dest) {
        adj[src].push_back(dest);
        adj[dest].push_back(src);
    }

    // Perform Breadth First Search (BFS) in parallel
    void bfs(int startVertex) {
        vector<bool> visited(numVertices, false);
        queue<int> q;

        // Mark the start vertex as visited and enqueue it
        visited[startVertex] = true;
        q.push(startVertex);

        while (!q.empty()) {
            int currentVertex = q.front();
            q.pop();
            cout << currentVertex << " ";

            // Enqueue all adjacent unvisited vertices
            #pragma omp parallel for
            for (int neighbor : adj[currentVertex]) {
                if (!visited[neighbor]) {
                    visited[neighbor] = true;
                    q.push(neighbor);
                }
            }
        }
    }

    // Perform Depth First Search (DFS) in parallel
    void dfs(int startVertex) {
        vector<bool> visited(numVertices, false);
        stack<int> s;

        // Mark the start vertex as visited and push it onto the stack
        visited[startVertex] = true;
        s.push(startVertex);

        while (!s.empty()) {
            int currentVertex = s.top();
            s.pop();
            cout << currentVertex << " ";

            // Push all adjacent unvisited vertices onto the stack
            #pragma omp parallel for
            for (int neighbor : adj[currentVertex]) {
                if (!visited[neighbor]) {
                    visited[neighbor] = true;
                    s.push(neighbor);
                }
            }
        }
    }
};

int main() {
    // Create a graph with 7 vertices
    Graph graph(7);

    // Add edges between vertices
    graph.addEdge(0, 1);
    graph.addEdge(0, 2);
    graph.addEdge(1, 3);
    graph.addEdge(1, 4);
    graph.addEdge(2, 5);
    graph.addEdge(2, 6);

    cout << "Breadth First Search (BFS): ";
    graph.bfs(0);
    cout << endl;

    cout << "Depth First Search (DFS): ";
    graph.dfs(0);
    cout << endl;

    return 0;
}
