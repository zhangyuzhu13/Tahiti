title: HashMap Source Code Review(Java 8)
date: 2018-04-20 14:16:00
categories: Algorithm
mathjax: true

---



## TreeNode & Node
HashMap in java 8 is different from the older version on the Entry. In java 8 version, when there are many nodes in one hash unit(default is more than 8), the Entry is convert to TreeNode type, and then use tree to add, delete and find. When the nodes become less(default is 6), the entry will then convert from TreeNode to normal Entry. 

So there are two kind of Node: Node implement from Map.Entry and TreeNode implement from LinkedHashMap.Entry. The TreeNode is implemented by red-black tree.

## putVal
What the put value do is to put a node into map. There are some cases being considered:

* The hash value's index place in the map is not being used, so just insert the new node.
* The key of new node exist in the map, so update the value
* If nodes are saved in TreeNode type, use putTreeVal() method.
* for loop until the end and find if the key exist. If not, insert into the end of ths linkedlist.
* Finally, keep the size below the threshold. If not, call resize() to adjust the size of the map.

## removeVal
This is much similar with putVal(). When the first node need to be removed, just remove it. Or if the nodes are in TreeNode type, call getTreeNode and removeTreeNode(). Or if the node need to be removed is in the middle of the list, keep track of the node's parent and remove it. What special is remove will not cause the size of map change in HashMap.

## resize
This function is used to adjust the map size twice as old size if it could. There is a law that: when the size is twice as old size, the old nodes in the same unit will only show in the same unit or in the unit which is in the distance of the old size. So we need to keep two list to put old nodes into new map. 
