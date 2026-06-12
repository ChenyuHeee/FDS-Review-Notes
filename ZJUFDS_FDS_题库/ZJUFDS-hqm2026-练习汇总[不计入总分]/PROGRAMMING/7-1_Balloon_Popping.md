## 7-1 Balloon Popping

- **类型**: PROGRAMMING
- **分值**: 10 分
- **作者**: 陈越

---

![pqq.jpg](~/6b53b1a2-7325-4de6-8dd5-bc403552b751.jpg)

Balloon popping is a fun game for kids.  Now $$n$$ balloons are positioned in a line. The goal of the game is very simple: to pop as many balloons as possible.  Here we add a special rule to this game -- that is, you can only make ONE jump.  Assume that a smart baby covers his/her body by thorns（刺）, jumps to some position and lies down (as shown by the figures below), so that the balloons will be popped as soon as they are touched by any part of the baby's body.  Now it is your job to tell the baby at which position he/she must jump to pop the most number of balloons.

![f1.jpg](~/8ac137cc-1253-42d6-ba2f-3da93df331bc.jpg)

![f2.jpg](~/7a0b9adf-ee5a-45ed-81e3-4ad970784ff2.jpg)

### Input Specification:

Each input file contains one test case. For each case, two positive integers are given in the first line: $$n$$ ($$\le 10^5$$), the number of balloons in a line, and $$h$$ ($$\le 10^3$$), the height of the baby with his/her arms stretched up.  Then $$n$$ integers are given in the next line, each corresponds to the coordinate of a balloon on the axis of the line.  It is guaranteed that the coordinates are given in ascending order, and are all in the range $$[-10^6, 10^6]$$.

### Output Specification:

Output in a line the coordinate at which the baby shall jump, so that if the baby jumps at this position and then lie down, the maximum number of the balloons can be popped beneath his/her body.  Then also print the maximum number of balloons that can be popped.  If the coordinate is not unique, output the smallest one.

The numbers must be separated by 1 space, and there must be no extra space at the beginning or the end of the line.

### Sample Input:
```in
11 120
-120 -40 0 80 122 140 160 220 240 260 300
```

### Sample Output:
```out
120 5
```

**Note:** jumping at any position from 120 to 140, or from 240 to 260 can pop 5 balloons, hence 120 is printed as the smallest one.

- **时间限制**: 200 ms
- **内存限制**: 65536 KB
