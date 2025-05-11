---
comments: true
title:  "Data Structure (1) - BST (Binary Search Tree) 이론, 구현"
excerpt: "자료구조 (1), 이진 탐색 트리"

categories:
  - Data Structure
tags:
  - code
  - Data Structure
  - Priority Queue
  - Heap
last_modified_at: 2021-06-18T012:06:00-05:00
---

### ✅ 개요

 이진 탐색 트리(Binary Search Tree, BST)의 이론과 구현을 정리합니다. Priority Queue, Heap을 공부하다가 정리했습니다.

[https://algorithm90.tistory.com/27?category=826977](https://algorithm90.tistory.com/27?category=826977) 

[https://ratsgo.github.io/data%20structure&algorithm/2017/10/22/bst/](https://ratsgo.github.io/data%20structure&algorithm/2017/10/22/bst/)

를 보고 공부했습니다.

---

### ❓ 이진 탐색 트리(Binary Search Tree, BST)란?

 이진 탐색 트리(Binary Search Tree)는 이진 탐색(Binary search)과 연결리스트(linked list)를 결합한 자료구조의 일종입니다.

**cf) 이진 탐색(Binary search)** : 정렬된 데이터에서 특정한 값을 찾아내는 알고리즘. 중간 임의의 값을 선택해 비교해가며 타깃이 작으면 더 작은 쪽으로, 크면 더 큰 쪽으로 이동하는 과정을 타깃을 찾을 때까지 반복한다.

**cf) 연결 리스트(linked list)** : 각 노드가 데이터와 포인터를 가지고 연쇄적으로 연결되어 있는 자료구조이다.

 효율적인 탐색 능력을 유지하면서 자료 입력/삭제를 가능하게 합니다.

 이진탐색의 시간 복잡도는 탐색의 경우 O(log n) 이고 자료 입/출력은 불가능합니다. 연결 리스트의 시간 복잡도는 자료 입/출력은 O(1)이지만 자료 탐색은 O(n)으로 이진 탐색 트리는 각각의 장점을 취하는 데에 목적이 있습니다.

 ![img](https://i.imgur.com/nCYjtI7.png)

![img](https://i.imgur.com/SSusVoP.png)

이진 탐색 트리는...

- 각 노드의 왼쪽 서브트리에는 해당 노드 값보다 작은 값을 지닌 노드들로, 오른쪽 서브트리에는 해당 노드 값보다 큰 값을 지닌 노드들로 이루어져 있습니다.
- 중복된 노드는 없습니다.
- 서브트리 또한 이진 탐색 트리입니다. 

이진탐색트리는 중위순회 (inorder)를 사용하여 순회합니다. (왼쪽 서브트리, 노드, 오른쪽 서브트리 순서) 중위순회를 통해 이진탐색트리를 정렬된 순서로 접근할 수 있습니다.

---

### 🚩구현 - 기본 노드, 트리

```python
class Node:
    def __init__(self, val):
        self.val = val
        self.leftChild = None
        self.rightChild = None
class BST:
    def __init__(self):
        self.root = None

    def setRoot(self, val):
        self.root = Node(val)
```

Node 클래스와 BST 클래스를 만들었습니다.

---

### 🚩구현 - Find

```python
    def find(self, val):
        if (self.findNode(self.root, val) is False):
            return False
        else:
            return True

    def findNode(self, currentNode : Node, val):
        # 노드 데이터 없음
        if (currentNode is None):
            return False
        # 타겟 값과 같은 val 가진 노드 발견
        elif (val == currentNode.val):
            return currentNode
        # 타겟보다 큰 노드 발견, 왼쪽 child Node로 재귀
        elif (val < currentNode.val):
            return self.findNode(currentNode.leftChild, val)
        # 타겟보다 작은 노드 발견, 오른쪽 child Node로 재귀
        else:
            return self.findNode(currentNode.rightChild, val)
```

fineNode에서는 재귀적으로 값을 찾아 노드를 return합니다. find는 return할 node가 존재하면 True, 존재하지 않으면 False를 반환합니다.

시간 복잡도는 트리의 높이에 해당하는 O(h)입니다. 완전 이진 트리에서는 높이에 따라 노드가 지수적으로 늘어나니 높이가 log값이 되어 O(logN) 이 되겠죠.

---

### 🚩구현 - insert

삽입의 경우 가장 높이가 큰 노드 (잎새노드) 에서부터 시작합니다. 이 이유는 서브트리 속성이 깨질 수 있어서라고 하네요. 

```python
    def insert(self, val):
        if (self.root is None):
            self.setRoot(val)
        else:
            self.insertNode(self.root, val)

    def insertNode(self, currentNode, val):
        if (val <= currentNode.val):
            if (currentNode.leftChild):
                self.insertNode(currentNode.leftChild, val)
            else:
                currentNode.leftChild = Node(val)
        elif (val > currentNode.val):
            if (currentNode.rightChild):
                self.insertNode(currentNode.rightChild, val)
            else:
                currentNode.rightChild = Node(val)
```

insert 함수는 이전 노드정보가 없다면 setRoot를 통해 첫 노드를 만들고 존재한다면 insertNode함수를 실행시킵니다.

insertNode는 현재 노드와 value를 인자로 받습니다. 현재 노드보다 val가 작다면 왼쪽 자식으로, 크다면 오른쪽 자식으로 보냅니다. 보내려는데 값이 없는, 그러니까 끝단까지 간다면 Node로 그 val를 저장해 기존 트리의 규칙을 유지합니다.

---

### 🚩구현 - delete

 delete의 경우 3가지 경우의 수가 있다.

1. 자식 노드가 없는 경우

   그냥 없애면 된다.

2. 하나의 자식 노드가 있는 경우

   없애고 자식 노드를 해당 노드 위치로 슥 옮긴다.

3. 자식 노드가 꽉 찬(두 개) 경우

   