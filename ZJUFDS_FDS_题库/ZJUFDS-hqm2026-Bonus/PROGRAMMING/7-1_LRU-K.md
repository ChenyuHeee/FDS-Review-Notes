## 7-1 LRU-K

- **类型**: PROGRAMMING
- **分值**: 20 分
- **作者**: 陈越

---

**Least Recently Used (LRU)** cache scheme is to remove the least recently used frame (the one hasn't been used for the longest amount of time) when the cache is full and a new page is referenced which is not there in cache.

LRU-K is a variant of the LRU algorithm, where K represents the number of recent uses.  LRU can be considered as LRU-1.  Unlike LRU, the LRU-K requires the maintenance of two different queues (for historical access and cache). The data in the historical access queue is not moved to the cache queue until the data is hit K times. 

For example, assuming that the length of both queues is 5, and the memory is initialized to be empty.  LRU-2 is used to process the data sequence in order: 9，5，6，7，8，3，8，9，5，9，8，3，4，7，5，6.  The changes of the historical access queue and the cache queue are shown in the following table: 

|Data Access|Historical Aaccess Queue|Cache Queue|
|- |- |- |
|9,5,6,7,8| 9,5,6,7,8| Empty |
|3| 5,6,7,8,3 | Empty |
|8| 5,6,7,3 | 8|
|9| 5,6,7,3,9| 8|
|5| 6,7,3,9| 8,5|
|9| 6,7,3|8,5,9|
|8| 6,7,3|5,9,8|
|3| 6,7| 5,9,8,3|
|4| 6,7,4|5,9,8,3|
|7| 6,4|5,9,8,3,7|
|5| 6,4|9,8,3,7,5|
|6| 4| 8,3,7,5,6|

Your job is to implement this LRU-K cache scheme.

### Input Specification:

Each input file contains one test case. For each case, the first line gives 3 positive integers: $$K$$ ($$\le 5$$), $$N$$ ($$\le 10^4$$) and $$M$$ ($$\le 10^5$$) which are the number of hits for cache, the size of the queues (assuming both queues have the same size) and the number of referenced page ID's. Then $$M$$ referenced page ID's are given in the next line.  A page ID is a number in the range $$[1, 2\times 10^4]$$. All the numbers in a line are separated by a space.

### Output Specification:

For each test case, output in the first line the page ID's in the historical access queue, and in the second line, those in the cache queue.  The ID's are ordered from front to rear of each queue.  All the numbers in a line must be separated by 1 space, and there must be no extra space at the beginning or the end of the line.  In case that a queue is empty, output `-` in a line instead.

### Sample Input 1:
```in
2 5 17
9 5 6 7 8 3 8 9 5 9 8 3 4 7 5 6 9
```

### Sample Output 1:
```out
4 9
8 3 7 5 6
```

### Sample Input 2:
```in
3 5 10
9 5 6 7 8 3 8 9 5 9
```

### Sample Output 2:
```out
7 3 8 5 9
-
```

- **时间限制**: 200 ms
- **内存限制**: 65536 KB
