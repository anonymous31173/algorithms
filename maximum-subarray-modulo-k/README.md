# Problem

Given an array of non-negative integers and a positive integer `k`,
find the maximum amongst the sum of each subarray modulo `k`.
When no maximum exists, return -1.

**Example.**
Let `k` be 7 and the array the following.

```
3 3 9 9 5
```

The subarrays and their sums modulo 7 are the following.

```
3 3 9 9 5
| | | | |
V V V V V
3 3 2 2 5

3 3 9 9 5
---
 6 <------ maximum
  ---
   5
    ---
     4
      ---
       0

3 3 9 9 5
-----
  1
  -----
    0
    -----
      2

3 3 9 9 5
-------
   3
  -------
     5

3 3 9 9 5
---------
    1
```

The maximum is 6.

# Solution

The proposed solution is `O(n log n)`.

- Search for maximum.
- Maximum is maximum amongst end poss.
- Search for maximum for each end pos amongst values for pos.
- Don't compute values for pos and then search for maximum.
- Compute values for pos on demand when you search for maximum.
  - Start with value last segment of subarray.
  - Look for biggest value that does not overflow value of last segment of subarray.
  - If none, take last segment of subarray.
```
   A: 3 5 2 2 2
 pos: 0 1 2 3 4

      3 5 3 5 6  <- max for end pos

      3 1 3 5 0  <- start pos 0
        5 0 2 4  <- start pos 1
          2 4 6  <- start pos 2
            2 4  <- start pos 3
              2  <- start pos 4
     
      ^ ^ ^ ^ ^
      | | | | |
      | | | | end pos 4
      | | | end pos 3
      | | end pos 2
      | end pos 1
      end pos 0
```

```
------------------
-------
       |----------|

       
   A: 3 5 2 2 2
 pos: 0 1 2 3 4

      3 1 3 5 0  <- value of longest array
      3 8 0 2 4

   0: 0         -> 0
   1:     2     -> 6
   2:         
   3:   1   3   -> 4
   4: 
   5:         4 -> 2
   6:         
   ^  ^ ^ ^ ^ ^
   |  | | | | |
   |  | | | | end pos 4
   |  | | | end pos 3
   |  | | end pos 2
   |  | end pos 1
   |  end pos 0
   offset from left


```
   A: 3 5 2 2 2
 pos: 0 1 2 3 4

   0:     1   0
   1:   0      
   2:     2 3 4
   3: 0   0    
   4:       2 3
   5:   1   0  
   6:         2
   ^  ^ ^ ^ ^ ^
   |  | | | | |
   |  | | | | end pos 4
   |  | | | end pos 3
   |  | | end pos 2
   |  | end pos 1
   |  end pos 0
   values
```

```
   A: 3 5 2 2 2
 pos: 0 1 2 3 4

   0: 3 1 3 5 0
    : 4 2 4 6 1
   4: 5 3 5 0 2
    : 6 4 6 1 3
 1,3: 0 5 0 2 4
    : 1 6 1 3 5
   2: 2 0 2 4 6
   ^  ^ ^ ^ ^ ^
   |  | | | | |
   |  | | | | end pos 4
   |  | | | end pos 3
   |  | | end pos 2
   |  | end pos 1
   |  end pos 0
   start positions
```

```
   A: 3 5 2 2 2
 pos: 0 1 2 3 4

      3 1 3 5 0  <- offset for end pos

   0: 0       *
   1:       *
   2:         4
   3:   
   4: * 1 * 3  
   5:   
   6:   * 2    
   ^  ^ ^ ^ ^ ^
   |  | | | | |
   |  | | | | end pos 4
   |  | | | end pos 3
   |  | | end pos 2
   |  | end pos 1
   |  end pos 0
   relative value
```


```
   A: 3 5 2 2 2
 pos: 0 1 2 3 4

   0: 3 1 3 5 *
   1:   
   2:         2
   3:   
   4: * 5 * 2 4
   5:   
   6:   * 2 4 6
   ^  ^ ^ ^ ^ ^
   |  | | | | |
   |  | | | | end pos 4
   |  | | | end pos 3
   |  | | end pos 2
   |  | end pos 1
   |  end pos 0
   relative value
```

```
   A: 3 5 2 2 2
 pos: 0 1 2 3 4

 |
 |
 v

 3 0: 0
 4 1:    
 5 2:    
 6 3:  
 0 4:  
 1 5:  
 2 6:  

 1 0: 0
 2 1:    
 3 2:  
 4 3:  
 5 4: 1 
 6 5:  
 0 6:  

 3 0: 0
 4 1:    
 5 2:  
 6 3:  
 0 4: 1 
 1 5:  
 2 6: 2

 5 0: 0
 6 1:    
 0 2:  
 1 3:  
 2 4: 3 
 3 5:  
 4 6: 2

 0 0: 0
 1 1:    
 2 2: 4
 3 3:  
 4 4: 3 
 5 5:  
 6 6: 2
```

```
          A: 3 5 2 2 2
        pos: 0 1 2 3 4

        3 0: 0

        1 0: 0
        5 4: 1 

        3 0: 0
        0 4: 1 
        2 6: 2

        5 0: 0
        2 4: 3 
        4 6: 2

        0 0: 0
        2 2: 4
        4 4: 3 
        6 6: 2
```


The solution value is the maximum amongst the maximum arrays that end
in each position.
To illustrate how to compute the maximum value for a given position,
consider the following example.

**Example.**
Given array `A = [3 2 11]` and `k = 7`, compute the maximum array that
ends in the last position in the following way.

```
         . <- 16 % 7 = 2
         .
14       .
         * <- 13 % 7 = 6
         *
11       # <- 11 % 7 = 4
         #
         #
         #
7        #
         #
       . #
       . #
     . . #
     . * #
     . * #
     
  A: 3 2 11
pos: 0 1 2
     
     ---
      5
       -
       2
     ------
       2
       ----
         6
         __
         4

0 -> 6
1 -> 5
2 -> 4
3 -> 3
4 -> 2
5 -> 1
6 -> 0


```

- The maximum subarray that ends in position 2 has value 6, the
  maximum value any subarray can have given `k = 7`.
- 11 is part of the maximum subarray that ends in position 2.
- The maximum subarray that ends in position 2 consists of 11 and
  the subarray that ends in position 1 with value 2.
- For each position i, there are at most 7 different values (0 to 6)
  for arrays ending in position i.
  - For position 0, values of arrays ending there are 3.
  - For position 1, values of arrays ending there are the following.
    - 5 = 3 + 2
    - 2
  - For position 2, values of arrays ending there are the following.
    - 2 = 16 % 7 = (5 + 11) % 7 = (5 + 4) % 7 = 9 % 7
    - 6 = 13 % 7 = (2 + 11) % 7 = (2 + 4) % 7 = 6 % 7
    - 4 = 11 % 7
- For position 2, the maximum array that ends there is the maximum of
  the values of arrays ending there.


```
  A: 3 2 11
pos: 0 1 2
  0:
  1:
  2:   1 0
  3: 0   
  4:     2
  5:   0 
  6:     1

  0:  
  1:  
  2:  
  3: 0
  4:  
  5:  
  6:  

  0:  
  1:  
  2: 1
  3: 
  4:  
  5: 0 
  6:  

  0:  
  1:  
  2: 0
  3: 
  4: 2 
  5: 
  6: 1

3 0: 0
4 1:    
5 2:    
6 3:  
0 4:  
1 5:  
2 6:  

5 0: 0
6 1:    
0 2:    
1 3:  
2 4: 1
3 5:  
4 6:  

2 0: 0
3 1:    
4 2: 2   
5 3:  
6 4: 1
0 5: 
1 6:  



  A: 3 2 11
pos: 0 1 2

     3 5 2
       2 6
         4

  A: 3 5 5
pos: 0 1 2

     3 1 6
       5 3
         5

  A: 3 5 2 2 2
pos: 0 1 2 3 4

     3 1 3 5 0
       5 0 
         2 4 6
           2 4
             2




-

       .
       .
     . .
     . . . . .
     . . . . .
  A: 3 5 2 2 2
pos: 0 1 2 3 4

   : 3 1 2 2
   :   5 3 4
           5    

  A: 1 1 1 1 1 1
pos: 1 2 0 
       1 2 0 
         1 2 0 
           1 2 0
             1 2
               1
               
0 1 2 3 4 5 6 7 8 9 10 11 12
0 1 2 0 1 2 0 1 2 0 1  2  0
```

**Other examples**

```
1 8 2
```


```
5 6
-
5
---
 4
  -
  6

6 % 7 = 6
9 % 7 = 2
     

7    
     #
     #
     #
   . #
   * #
 . * #
 1 2 6
 -----
   2
   ---
    1
     -
     6


 .    
 * * 
7* * 
 # # #
 # # #
 # # #
 # # #
 # # #
 # # #
 1 2 6
 -----
   2
   ---
    1
     -
     6


 .
 # #
7# #  
 # # 
 # # 
 # # 
 # # 
 * * *
 * * *
 1 6 2
 ---
  0
   -
   6
 -----
   2
   ---
    1
     -
     2

  
7    
   # 
   # 
   # 
   # 
   # *
 . # *
 1 6 2
 -----
   2
   ---
    1
     -
     2
```