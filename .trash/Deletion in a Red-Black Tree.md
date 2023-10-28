---
url: https://www.programiz.com/dsa/deletion-from-a-red-black-tree
title: Deletion in a Red-Black Tree
date: 2023-10-25 15:57:10
time: 1698220630735
tags:
---
### insert
Red-Black tree is a self-balancing binary search tree in which each node contains an extra bit for denoting the color of the node, either red or black.

Before reading this article, please refer to the article on [red-black tree](https://www.programiz.com/dsa/red-black-tree).

Deleting a node may or may not disrupt the red-black properties of a red-black tree. If this action violates the red-black properties, then a fixing algorithm is used to regain the red-black properties.

#### Deleting an element from a Red-Black Tree

This operation removes a node from the tree. After deleting a node, the red-black property is maintained again.

1.  Let the `nodeToBeDeleted` be:
    
    ![](<assets/1698220630768.png>)
    
    Node to be deleted
    
2.  Save the color of nodeToBeDeleted in origrinalColor.
    
    ![](<assets/1698220631338.png>)
    
    Saving original color
    
3.  If the left child of nodeToBeDeleted is `NULL`
    1.  Assign the right child of nodeToBeDeleted to x.
        
        ![](<assets/1698220631952.png>)
        
        Assign x to the rightChild
        
    2.  Transplant nodeToBeDeleted with x.
        
        ![](<assets/1698220632470.png>)
        
        Transplant nodeToBeDeleted with x
        
4.  Else if the right child of nodeToBeDeleted is `NULL`
    1.  Assign the left child of nodeToBeDeleted into x.
    2.  Transplant nodeToBeDeleted with x.
5.  Else
    1.  Assign the minimum of right subtree of noteToBeDeleted into y.
    2.  Save the color of y in originalColor.
    3.  Assign the rightChild of y into x.
    4.  If y is a child of nodeToBeDeleted, then set the parent of x as y.
    5.  Else, transplant y with rightChild of y.
    6.  Transplant nodeToBeDeleted with y.
    7.  Set the color of y with originalColor.
6.  If the originalColor is BLACK, call DeleteFix(x).

#### Algorithm to maintain Red-Black property after deletion

This algorithm is implemented when a black node is deleted because it violates the black depth property of the red-black tree.

This violation is corrected by assuming that node x (which is occupying y's original position) has an extra black. This makes node x neither red nor black. It is either doubly black or black-and-red. This violates the red-black properties.

However, the color attribute of x is not changed rather the extra black is represented in x's pointing to the node.

The extra black can be removed if

1.  It reaches the root node.
2.  If x points to a red-black node. In this case, x is colored black.
3.  Suitable rotations and recolorings are performed.

Following algorithm retains the properties of a red-black tree.

1.  Do the following until the x is not the root of the tree and the color of x is BLACK
2.  If x is the left child of its parent then,
    1.  Assign `w` to the sibling of `x`.
        
        ![](<assets/1698220632973.png>)
        
        Assigning w
        
    2.  If the sibling of x is RED,  
        **Case-I:**
        1.  Set the color of the right child of the parent of x as BLACK.
        2.  Set the color of the parent of x as RED.
            
            ![](<assets/1698220633487.png>)
            
            Color change
            
        3.  Left-Rotate the parent of x.
            
            ![](<assets/1698220633999.png>)
            
            Left-rotate
            
        4.  Assign the rightChild of the parent of x to w.
            
            ![](<assets/1698220634514.png>)
            
            Reassign w
            
    3.  If the color of both the right and the leftChild of w is BLACK,  
        **Case-II:**
        1.  Set the color of w as RED
        2.  Assign the parent of x to x.
    4.  Else if the color of the rightChild of w is BLACK  
        **Case-III:**
        1.  Set the color of the leftChild of w as BLACK
        2.  Set the color of w as RED
            
            ![](<assets/1698220635027.png>)
            
            Color change
            
        3.  Right-Rotate w.
            
            ![](<assets/1698220635541.png>)
            
            Right rotate
            
        4.  Assign the rightChild of the parent of x to w.
            
            ![](<assets/1698220636157.png>)
            
            Reassign w
            
    5.  If any of the above cases do not occur, then do the following.  
        **Case-IV:**
        1.  Set the color of w as the color of the parent of x.
        2.  Set the color of the parent of parent of x as BLACK.
        3.  Set the color of the right child of w as BLACK.
            
            ![](<assets/1698220636668.png>)
            
            Color change
            
        4.  Left-Rotate the parent of x.
            
            ![](<assets/1698220637077.png>)
            
            Left-rotate
            
        5.  Set x as the root of the tree.
            
            ![](<assets/1698220637595.png>)
            
            Set x as root
            
3.  Else same as above with right changed to left and vice versa.
4.  Set the color of x as BLACK.

The workflow of the above cases can be understood with the help of the flowchart below.

![](<assets/1698220638011.png>)

Flowchart for deletion operation
