---
url: https://www.programiz.com/dsa/insertion-in-a-red-black-tree
title: Insertion in a Red-Black Tree
date: 2023-10-25 15:57:50
time: 1698220670413
tag: 
summary: The new node is always inserted as a RED node. If it is violating the red-black properties, fix up al......
---
### Insertion
Red-Black tree is a self-balancing binary search tree in which each node contains an extra bit for denoting the color of the node, either red or black.

Before reading this article, please refer to the article on [red-black tree](https://www.programiz.com/dsa/red-black-tree).

While inserting a new node, the new node is always inserted as a RED node. After insertion of a new node, if the tree is violating the properties of the red-black tree then, we do the following operations.

1.  Recolor
2.  Rotation

* * *

#### Algorithm to Insert a New Node

Following steps are followed for inserting a new element into a red-black tree:

1.  The `newNode` be:
    
    ![](<assets/1698220670428.png>)
    
    New node
    
2.  Let y be the leaf (ie. `NIL`) and `x` be the root of the tree. The new node is inserted in the following tree.
    
    ![](<assets/1698220670842.png>)
    
    Initial tree
    
3.  Check if the tree is empty (ie. whether `x` is `NIL`). If yes, insert `newNode` as a root node and color it black.
4.  Else, repeat steps following steps until leaf (`NIL`) is reached.
    1.  Compare `newKey` with `rootKey`.
    2.  If `newKey` is greater than `rootKey`, traverse through the right subtree.
    3.  Else traverse through the left subtree.
        
        ![](<assets/1698220671377.png>)
        
        Path leading to the node where newNode is to be inserted
        
5.  Assign the parent of the leaf as parent of `newNode`.
6.  If `leafKey` is greater than `newKey`, make `newNode` as `rightChild`.
7.  Else, make `newNode` as `leftChild`.
    
    ![](<assets/1698220671897.png>)
    
    New node inserted
    
8.  Assign `NULL` to the left and `rightChild` of `newNode`.
9.  Assign RED color to `newNode`.
    
    ![](<assets/1698220672507.png>)
    
    Set the color of the newNode red and assign null to the children
    
10.  Call InsertFix-algorithm to maintain the property of red-black tree if violated.

* * *

**Why newly inserted nodes are always red in a red-black tree?**

This is because inserting a red node does not violate the depth property of a red-black tree.

If you attach a red node to a red node, then the rule is violated but it is easier to fix this problem than the problem introduced by violating the depth property.

* * *

#### Algorithm to Maintain Red-Black Property After Insertion

This algorithm is used for maintaining the property of a red-black tree if insertion of a newNode violates this property.

1.  Do the following until the parent of `newNode` `p` is RED.
2.  If `p` is the left child of `grandParent` `gP` of `newNode`, do the following.  
    **Case-I:**
    1.  If the color of the right child of `gP` of `newNode` is RED, set the color of both the children of `gP` as BLACK and the color of `gP` as RED.
        
        ![](<assets/1698220673117.png>)
        
        Color change
        
    2.  Assign `gP` to `newNode`.
        
        ![](<assets/1698220673531.png>)
        
        Reassigning newNode
        
          
        **Case-II:**
    3.  (Before moving on to this step, while loop is checked. If conditions are not satisfied, it the loop is broken.)  
        Else if `newNode` is the right child of `p` then, assign `p` to `newNode`.
        
        ![](<assets/1698220673937.png>)
        
        Assigning parent of newNode as newNode
        
    4.  Left-Rotate `newNode`.
        
        ![](<assets/1698220674450.png>)
        
        Left Rotate
        
          
        **Case-III:**
    5.  (Before moving on to this step, while loop is checked. If conditions are not satisfied, it the loop is broken.)  
        Set color of `p` as BLACK and color of `gP` as RED.
        
        ![](<assets/1698220674962.png>)
        
        Color change
        
    6.  Right-Rotate `gP`.
        
        ![](<assets/1698220675475.png>)
        
        Right Rotate
        
3.  Else, do the following.
    1.  If the color of the left child of `gP` of `z` is RED, set the color of both the children of `gP` as BLACK and the color of `gP` as RED.
    2.  Assign `gP` to `newNode`.
    3.  Else if `newNode` is the left child of `p` then, assign `p` to `newNode` and Right-Rotate `newNode`.
    4.  Set color of `p` as BLACK and color of `gP` as RED.
    5.  Left-Rotate `gP`.
4.  (This step is performed after coming out of the while loop.)  
    Set the root of the tree as BLACK.
    
    ![](<assets/1698220675893.png>)
    
    Set root's color black
    

The final tree look like this:

![](<assets/1698220676418.png>)

Final tree
