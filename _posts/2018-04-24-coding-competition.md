---
layout: single
title: 코딩대회용 코드 조각 모음
tags: [c, 프로그래밍, 코딩]
author_profile: false
toc: true
toc_label: 목차
toc_icon: "cog"
classes: wide
permalink: /coding_test/
sidebar:
    nav: "coding_test" 
---
c11 코드 조각 모음

### --- 입출력 관련 --- 

<p id="io"></p>

### iostream
```c++
#include<iostream>
```
#### 한 줄 입력받기
```c++
using namespace std;
// int 입력받기
vector<int> readInt(int n){
    int tmp=0;
    vector<int> v(n);
    for (int i=0; i<n; ++i){
        cin >> tmp;
        v.emplace_back(tmp);
    }
    return v;
}
// string 
vector<string> readStr(int n){
    string tmp=0;
    vector<string> v(n);
    for (int i=0; i<n; ++i){
        cin >> tmp;
        v.emplace_back(tmp);
    }
    return v;
}
```
### cstdio
```c++
#include<cstdio>
```

<p id="io"></p>

### --- 자료구조 관련 ---
### vector

<p id="vector"></p>

```c++
#include<vector>
```
#### Iterator
* begin() - cbegein rbegin crbegin
* end() - cend rend crend

#### Size
* size()
* reserve()

#### Access Data
* []
* at - 예외 던짐
* front()
* end()

#### 삽입 삭제
* push_back()
* pop_back()

### array
```c++
#include<array>
```

### list

<p id="list"></p>


```c++
#include<list>
```
#### Iterator
* begin() - cbegein rbegin crbegin
* end() - cend rend crend

#### Size
* size()

#### Access Data
* front()
* end()

#### 삽입 삭제
* emplace_front()
* emplace_back()
* push_front()
* push_back()
* pop_front()
* pop_back()
* insert(iterator, 값) : [detail](http://www.cplusplus.com/reference/list/list/insert/)
* erase(iterator) : [detail](http://www.cplusplus.com/reference/list/list/erase/)

#### 다른 연산
* sort(comparater)
* merge()
* reverse()



### map/ hashmap (ordered)

<p id="map"></p>


```c++
#include<map>
```
#### Iterator
* begin() - cbegein rbegin crbegin
* end() - cend rend crend
#### access
* []
* at
#### 삽입 삭제
* insert(pair)/ insert(pair)
```c++
map<char,int> m;
m['a'] = 1; 
```
* erase(key)

### set (ordered)
```c++
#include<set>
```
#### Iterator
* begin() - cbegein rbegin crbegin
* end() - cend rend crend
#### 삽입 삭제
* insert(key)
* erase(key)
### queue (FIFO)

<p id="queue"></p>

```c++
#include<queue>
```

#### Access Data
* front()
* back()
#### 삽입 삭제
* push()
* pop()
#### Priority Queue

<p id="prioq"></p>

```c++
#include <queue> 
int main(){
    std::priority_queue<int> mypq;
}
```
Queue와 같은 연산 지원


### deque (양방향)

<p id="deque"></p>

```c++
#include<deque>
```
#### Iterator
* begin() - cbegein rbegin crbegin
* end() - cend rend crend

#### Size
* size()

#### Access Data
* []
* at - 예외 던짐
* front()
* end()

#### 삽입 삭제
* push_front()
* push_back()
* pop_front()
* pop_back()

### stack (LIFO)

<p id="stack"></p>


```c++
#include<stack>
```
#### Access Data
* top()
#### 삽입 삭제
* push()
* pop()

## --- 그래프 ---

<p id="graph"></p>

### 수행시간
<p id="time"></p>

| Operation | Graph | Digraph|CompleteGraph|GridGraph|
|:------|------:|------:|------:|------:|
| n = numberOfVertices()|O(1)|O(1)|O(1)|O(1)|
| n = numberOfEdges()|O(1)|O(1)|O(1)|O(1)|
| n = numberOfEdgesFromVertex(v)|O(1)|O(1)|O(1)|O(d)|
| n = numberOfEdgesToVertex(v)|O(1)|O(1)|O(1)|O(d)|
| v = vertexOfEdge(e, j)|O(1)|O(1)|O(1)|O(d)|
| e = edgeFromVertex(v, j)|O(1)|O(1)|O(1)|O(d)|
| e = edgeToVertex(v, j)|O(1)|O(1)|O(1)|O(d)|
| v = vertexFromVertex(v, j)|O(1)|O(1)|O(1)|O(d)|
| v = vertexToVertex(v, j)|O(1)|O(1)|O(1)|O(d)|
| r = findEdge(v0, v1)|O(log deg G)|O(log deg G)|O(1)|O(d2)|
| v = insertVertex()|O(1)|O(1)|n/a|n/a|
| v = insertVertices(n)|O(1)|O(1)|n/a|n/a|
| e = insertEdge(v0, v1)|O(deg G)|O(deg G)|n/a|n/a|
| eraseEdge(e)|O(deg G)|O(deg G)|n/a|n/a|
| eraseVertex(v)|O((deg G)2)|O((deg G)2)|n/a|n/a|

### 알고리즘

<p id="graph_algo"></p>

* Search and traversal, implemented generically, with callbacks
  * depth first search (DFS)
  * breadth first search (BFS)
* 1-connected components
  * by breadth-first search
  * by disjoint sets
* 2-connected components
  * cut-vertices/articulation vertices (Hopcroft-Tarjan Algorithm [6])
  * cut-edges/bridges (Tarjan's algorithm [10])
* Shortest paths in weighted and unweighted graphs, as sequences of edges or * vertices
  * Single source shortest path (SSSP)
  * Single pair shortest path (SPSP)
* Minimum spanning trees and minimum spanning forests
  * Prim's algorithm [9]
  * Dynamic Programming
* Maximum st-flow
  * Goldberg-Tarjan Algorithm/Push-Relabel Algorithm [5] with FIFO vertex selection   * rule
  * Edmonds-Karp Algorithm [4]
* Bipartite Matching
  * Extension of the Munkres algorithm by Bourgeois and Lasalle [11]
* Minimum Cost (Lifted) Multicut [2,3]
  * A Branch-and-Cut Algorithm based on [2] and Gurobi
  * A generalization [1] of the Kernighan-Lin Algorithm [7]
  * Greedy additive edge contraction [8]
* Set Partition [3]
  * by specialization of minimum cost multicut algorithms for complete graphs

### Class

<p id="class"></p>

[자세한 코드 깃허브](https://github.com/raunaak/Generic-Graph)

```c++
#include <iostream>
#include <vector>
#include <algorithm>

template<class T>
class AdjacentNode;
template<class T>
class GraphNode{
    public:
    T data;
    AdjacentNode<T>* next;
    GraphNode(const T& d, AdjacentNode<T>* n);
};

template<class T>
GraphNode<T>::GraphNode(const T& d, AdjacentNode<T>* n){
    data = d; next = n;
}

template<class T>
class AdjacentNode{
    public:
    GraphNode<T>* node;
    AdjacentNode<T>* next;
    int weight;
    AdjacentNode(GraphNode<T>* g, AdjacentNode<T>* n);
    AdjacentNode(GraphNode<T>* g, AdjacentNode<T>* n, int weight);
};

template<class T>
AdjacentNode<T>::AdjacentNode(GraphNode<T>* g, AdjacentNode<T>* n){
    node = g; next = n; 
    weight = 0;
}

template<class T>
AdjacentNode<T>::AdjacentNode(GraphNode<T>* g, AdjacentNode<T>* n, int weight){
    node = g; next = n;
    this->weight = weight;
}
template<class T>
class Edge{
    public:
    GraphNode<T>* node1;
    GraphNode<T>* node2;
    int weight;
    Edge(GraphNode<T>* node1, GraphNode<T>* node2, int weight);
    bool operator<(const Edge<T>& e); 
};

template<class T>
bool Edge<T>::operator <(const Edge<T>& e){
    return this->weight < e.weight;
}

template<class T>
Edge<T>::Edge(GraphNode<T>* node1, GraphNode<T>* node2, int weight){
    this->node1 = node1;
    this->node2 = node2;
    this->weight = weight;
}
template<class T>
class Graph{
    public:
    vector<GraphNode<T>*> nodeList;
    vector<Edge<T>*> edgeList;
    void addEdge(const T& data1, const T& data2);
    void addEdge(const T& data1, const T& data2, int w);
    void addVertex(const T& d);
    void print();
    GraphNode<T>* findVertex(const T& d);
    vector<GraphNode<T>*>& getList();
    vector<Edge<T>*>& getEdge();
    void sortEdgesByWeight();
};

template<class T>
GraphNode<T>* Graph<T>::findVertex(const T& d){
    /*Find the vertex in vector with given data if found or returns NULL*/
    for(int i=0; i<nodeList.size(); i++){
        if(nodeList[i]->data==d)return nodeList[i];
    }
    return NULL;
}

template<class T>
void Graph<T>::addVertex(const T& d){
    /* Add graphnode at the end of vector */
    nodeList.push_back(new GraphNode<T>(d, NULL));    
}

template<class T>
void Graph<T>::print(){
    /*Print line by line all the vertices in vector along with all the neighbours */
    for(int i=0; i<nodeList.size();i++){
        cout<<nodeList[i]->data<<" ";
        AdjacentNode<T>* n = nodeList[i]->next;
        while(n){cout<<n->node->data<<" "; n = n->next;}
        cout<<endl;        
    }
}

template<class T>
void Graph<T>::addEdge(const T& data1, const T& data2, int w){
    /* Add graphnode to vector if there is no element corresponding to data1 and data2
       Find graphnodes in vector for elements corresponding to data1 and data2
       Add adjacentnode to both of the elements of vector
    */
    if(!findVertex(data1))nodeList.push_back(new GraphNode<T>(data1,NULL));
    if(!findVertex(data2))nodeList.push_back(new GraphNode<T>(data2,NULL));
    GraphNode<T>* n1 = findVertex(data1);
    GraphNode<T>* n2 = findVertex(data2);
    n1->next = new AdjacentNode<T>(n2,n1->next,w);
    n2->next = new AdjacentNode<T>(n1, n2->next,w);
    this->edgeList.push_back(new Edge<T>(n1, n2, w));    
}

template<class T>
void Graph<T>::addEdge(const T& data1, const T& data2){
    this->addEdge(data1, data2, 0);
}


template<class T>
vector<GraphNode<T>*>& Graph<T>::getList(){
    return this->nodeList;
}

template<class T>
vector<Edge<T>*>& Graph<T>::getEdge(){
    return this->edgeList;
}

template<class T>
void Graph<T>::sortEdgesByWeight(){
    sort(edgeList.begin(), edgeList.end());
}
```

### 메소드 정의

<p id="method"></p>

DFS
```c++
#include <set>
using namespace std;

template<typename T>
void depthFirstSearch(Graph<T>* g, GraphNode<T>* node, set<GraphNode<T>*>* gset){
    if(node){
        if(gset->find(node)==gset->end()){
            cout<<node->data<<" ";
            gset->insert(node);
            AdjacentNode<T>* adjNode = node->next;
            while(adjNode){depthFirstSearch(g, adjNode->node, gset); adjNode = adjNode->next;}
        }
    }
}

template<typename T>
void depthFirstSearch(Graph<T>* g){
    vector<GraphNode<T>*> list = g->getList();
    set<GraphNode<T>*> gset;
    for(int i=0; i<list.size(); i++)
    depthFirstSearch(g, list[i], &gset);
}

template<typename T>
void depthFirstSearch(Graph<T>* g, GraphNode<T>* node){
    set<GraphNode<T>*> gset;
    depthFirstSearch(g, node, &gset);
}
```

BFS
```c
#include <queue>
#include <set>
using namespace std;

template<typename T>
void breadthFirstSearch(Graph<T>* g, queue<GraphNode<T>*>* q, set<GraphNode<T>*>* gset){
    while(q->size()>0){
        GraphNode<T>* node = q->front(); q->pop(); cout<<node->data<<" ";
        AdjacentNode<T>* adjNode = node->next;
        while(adjNode){
            if(gset->find(adjNode->node)==gset->end()){
                q->push(adjNode->node); 
                gset->insert(adjNode->node);
            }
            adjNode = adjNode->next;       
        }        
    }
}

template<typename T>
void breadthFirstSearch(Graph<T>* g){
    /* Algorithm
     * Add first graph node to queue, add this node to set to mark that we have visited the node
     * while queue is not empty
     *     pop from queue and print it
     *     Go to all the neighbours of this popped node
     *     for every neighbour, 
     *         if the neighbour is not present in set, add it to queue and set
     *         else continue;
     * 
     * The graph might be disconnected, so go to every node
     *     Check if it is present in set
     *         if yes, continue
     *         if no, run breadth first search on this part
     */
    vector<GraphNode<T>*> list = g->getList();
    queue<GraphNode<T>*> q;  
    set<GraphNode<T>*> gset; 
    for(int i=0; i<list.size(); i++){
        if(gset.find(list[i])==gset.end()){
            q.push(list[i]);
            gset.insert(list[i]);
            breadthFirstSearch(g, &q, &gset);
        }
    }    
}

template<typename T>
void breadthFirstSearch(Graph<T>* g, GraphNode<T>* node){
    queue<GraphNode<T>*> q;  
    set<GraphNode<T>*> gset; 
    q.push(node);
    gset.insert(node);
    breadthFirstSearch(g, &q, &gset);        
}
```

MST 찾기 
```c++
#include <map>
#include <queue>

template<typename T>
void minimumSpanningTree(Graph<T>* g){
    /* Algorithm
     * Use bfs but add an additional parent map which has children as key and parents as values
     */
    vector<GraphNode<T>*> list = g->getList();
    queue<GraphNode<T>*> q;
    map<GraphNode<T>*, GraphNode<T>*> gmap;
    for(int i=0; i<list.size(); i++){
    if(gmap.find(list[i])==gmap.end()){
    q.push(list[i]);
    gmap[list[i]]=NULL;
    while(q.empty()==false){
        GraphNode<T>* node = q.front(); q.pop();
        AdjacentNode<T>* adjNode = node->next;
        while(adjNode){
            if(gmap.find(adjNode->node)==gmap.end()){
                q.push(adjNode->node);
                gmap[adjNode->node] = node;
            }
            adjNode = adjNode->next;
        }
    }
    }
    }
    
    typename map<GraphNode<T>*, GraphNode<T>*>::iterator it = gmap.begin();
    for( ; it!=gmap.end(); it++){
        cout<<it->first->data;
        if(it->second)cout<<"-->"<<it->second->data;
        cout<<endl;
    }
}
```

최단경로
```c++
template<typename T>
void shortestPath(Graph<T>* g, const T& data1, const T& data2){
    /* Algorithm
     * Use bfs but add an additional parent map which has children as key and parents as values
     */
    GraphNode<T>* n1 = g->findVertex(data1);
    GraphNode<T>* n2 = g->findVertex(data2);
    if(!n1||!n2)return;
    if(!existPath(g, n1, n2)){
        cout << "Not exist" << endl;
        return;
    }
    vector<GraphNode<T>*> list = g->getList();
    queue<GraphNode<T>*> q;
    map<GraphNode<T>*, GraphNode<T>*> gmap;
    for(int i=0; i<list.size(); i++){
    if(gmap.find(list[i])==gmap.end()){
    q.push(list[i]);
    gmap[list[i]]=NULL;
    while(q.empty()==false){
        GraphNode<T>* node = q.front(); q.pop();
        AdjacentNode<T>* adjNode = node->next;
        while(adjNode){
            if(gmap.find(adjNode->node)==gmap.end()){
                q.push(adjNode->node);
                gmap[adjNode->node] = node;
                if(gmap.find(n1)!=gmap.end()&&gmap.find(n2)!=gmap.end())goto stop;
            }
            adjNode = adjNode->next;
        }
    }
    }
    }
    stop:
    GraphNode<T>* node = n2;
    while(n2&&n2!=n1){
        cout<<n2->data<<"-->"<<gmap[n2]->data<<endl;
        n2 = gmap[n2];
    }
}
```

경로 찾기
```c++
// 경로가 존재하는 지 확인 
template<typename T>
bool existPath(Graph<T>* g, GraphNode<T>* u, GraphNode<T>* v){
    if(!u||!v)
        return false;        
    set<GraphNode<T>*> gset;
    depthFirstSearch(g,u,&gset);
    if (gset.find(v) == gset.end())
        return false;
    else
        return true;
}

template<typename T>
bool findPathDFS(Graph<T>* g, GraphNode<T>* u, GraphNode<T>* v, set<GraphNode<T>*>* gset){
    if(u==v) 
        return true;
    AdjacentNode<T>* adjNode = u->next;
    while(adjNode){
        if(gset->find(adjNode->node)==gset->end()){
            gset->insert(adjNode->node);
            if(findPathDFS(g, adjNode->node, v, gset)){
                cout<<" "<<adjNode->node->data; 
                return true;
            }
        }
    }
    return false;
}

template<typename T>
void findPathDFS(Graph<T>* g, const T& data1, const T& data2){
    GraphNode<T>* u = g->findVertex(data1);
    GraphNode<T>* v = g->findVertex(data2);
    if(!u||!v)
        return;        
    set<GraphNode<T>*> gset;
    if(!existPath(g, u,v)){
        cout << 0 << endl;
        return;
    }
    gset.insert(u);
    if(findPathDFS(g, u, v, &gset))
        cout<<" "<<u->data<<endl;

}
```

## STL

<p id="stl"></p>

### STL로 벡터 정렬하기 
```c++
#include<string>
#include<algorithm>
class School{
public:
    School(string name_, uint64_t soju_): name(name_), soju(soju_){}
    string name;
    uint64_t soju;
};

bool operator<(const School& lhs, const School& rhs){
    return lhs.soju<rhs.soju;
}

int main(int argc, char **argv)
{
    vector<School> v;
    v.emplace_back("Yonsei",15);
    v.emplace_back("Korea",11);
    v.emplace_back("Ehwa",17);
    v.emplace_back("Sogang",2);
    sort(v.rbegin(),v.rend());
    for(auto& s : v)
        cout << s.name << " " << s.soju << endl;

    return 0;
}

```