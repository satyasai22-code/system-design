# Database Indexing

- Data is logically stored in tables, but not physically
- Physically it is stored in `Data Pages`
- Typically Data Page is 8KB in size(Might differ DB to DB).
- Data Page contains Header(96bytes), Data Records(8060 bytes), Offset(36 bytes).
- Header: Page No, Free Space available, checksum etc..
- Data Records: Actual data is stored here
- Offset: contains an Array, each index of an array holds a pointer to corresponding data in Data Records.
- DBMS creates and manage data pages. It can create many data pages for storing 1 table data.
- These data pages ultimately gets stored in Data Block in physical memory like disk.
- Data Block:
    - Min amount of data which can be read/write by an I/O operation.
    - It is not managed by DBMS, but by disk
    - Size: 4 kb to 32kb(common is 8kb).
    - So based on data block size, it can hold 1 or many data page
    - Can be scattered over the disk
- DBMS maintains the mapping of Datapage and Data block.

What type of indexing present in RDBMS?
- Clustered Index, Non-Clustered indexing

What is Indexing?
- Used to increase performance of DB query.
- Without it, DBMS has to iterate each and every table tow to find requested data. Coz it doesnot know which row goes to which data page. T(n): O(n)
- B+ Tree provides O(log N) for insertion, searching and deletion

How B(Balanced) Tree works? (B, B+ tree are almost same)
- It maintains sorted data.
- All leaves are at the same level
- M order tree means, each node can have at most M children
-And M-1 keys per node.

3 order(2 keys) B tree: 

3-order means 3 pointer, 2 keys

| p1 | k1 | p2 | k2 | p3 |
   \        \          \
    c1       c2         c3

pi: ith pointer

ki: ith key

ci: ith child

p1 points to child node that is < k1
p2 points to child node that is >= k1 but < k2
p3 points to child node that is > k2

Let stores below data in 3-Order B tree:
9, 33, 75, 41, 98, 214, 126, 55, 72


(insert 9): | 9 |  |  
(insert 33): | 9 | 33 |
(insert 75): 2 Keys are filled already. Rebalance
            :   | 33 |  |
                 /  \
            | 9 | |   | 75 |  |
(insert 41): 41 greater than 33 so goes to right side
            :   | 33 |  |
                 /  \
            | 9 | |   | 41 | 75 |  (We had space in right key and it should be sorted, so moved 75 to right and inserted 41 at left).
(insert 98): Goes to right child of 31. But wait, keys are filled. So check if parent of 75 has space. Yes 33 has space.
            :   | 33         |   75 |
                 /  \             \
            | 9 |  | |41| |   | 98 |  |
- Just watch some video of B tree. Its mind blowing already.
- B+ Tree has connection b/w child nodes.


- DBMS uses B+ Trees to manage its Data Pages and Rows within the pages.
- How?
   - Leaf node actually holds the indexed column value(lets say ID).
   - Root node or Intermediary node hold value which is used for faster searching the data. Possible that value might have deleted from DB, but its can be used for sorting the tree.