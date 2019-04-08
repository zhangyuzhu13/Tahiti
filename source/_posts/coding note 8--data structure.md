title: coding note 8 Data Structure
date: 2018-05-15 14:16:00
categories: Algorithm

---



## hash
###Collision:

* open hash(LinkedList)
* closed hash(Array)(no delete)

### LRU(Least Rencently Used) Cache

* LRU
* LinkedList(Double Linked Node)
* HashSet

reasons for using these method and data structure:
if you want a O(1)get, hash table is definitely the first choice to use. Considering the situation when the node you want to pick is in the set, you may just want to let this node to become the top node. What's more, when the set is full, you need to add a new node and delete the last node. For these cases, the LinkedList structure is more suitable for us to use. Double linked list should be better because we need to add and delete in the middle of the list and double linked list is easy to deal with these operation.

### PriorityQueue for Top-K
PriorityQueue is not the same with normal queue which is first-in-first-out. It always let the node which is of highest level priority come out first. We can define the judgement of priority by overwrite the Comparator() interface.
For example:

```
PriorityQueue<E> this.queue = new PriorityQueue(maxSize, new Comparator<E>() {
			public int compare(E o1, E o2) {
				// 生成最大堆使用o2-o1,生成最小堆使用o1-o2, 并修改 e.compareTo(peek) 比较规则
				return (o2.compareTo(o1));
			}
		});
```

### Heap
there are several rules for a heap to follow:

1. father node is always bigger or smaller than chailren nodes.
2. heap is always a compelete binary tree  

heap operators:

* add O(logN)   operator:sift-up
* delete O(logN)   operator:sift-down
* min/max O(1)



