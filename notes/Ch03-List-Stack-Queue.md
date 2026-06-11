# Chapter 3: Lists, Stacks, and Queues

## §1 Abstract Data Type (ADT)

**Definition:** Data Type = { Objects } $\cup$ { Operations }

**Example:** `int = { 0, ±1, ±2, ..., INT_MAX, INT_MIN }` $\cup$ `{ +, -, ×, ÷, %, ... }`

**Definition:** An **Abstract Data Type (ADT)** is a data type that is organized in such a way that:
- The **specification on the objects** and **specification of the operations** on the objects are **separated** from
- The **representation of the objects** and the **implementation of the operations**.

This separation is the key idea of abstraction — users interact with the ADT through its interface without needing to know how it is implemented internally.

---

## §2 The List ADT

### ADT Specification

**Objects:** $( \text{item}_0, \text{item}_1, \ldots, \text{item}_{N-1} )$

**Operations:**
- Finding the length, $N$, of a list.
- Printing all the items in a list.
- Making an empty list.
- Finding the $k$-th item from a list, $0 \le k < N$.
- Inserting a new item after the $k$-th item of a list, $0 \le k < N$ (note: "why after?" — this is a design choice).
- Deleting an item from a list.
- Finding next of the current item from a list.
- Finding previous of the current item from a list.

---

### 1. Simple Array Implementation of Lists

$$
\text{array}[i] = \text{item}_i
$$

**Diagram description:** Sequential mapping in memory — elements are stored contiguously:
- Address `array + i` contains `item_i`
- Address `array + i + 1` contains `item_{i+1}`
- ...

**Properties:**
- $\triangle$ **MaxSize has to be estimated** in advance (if the list grows beyond the array size, resizing is needed).
- $\checkmark$ **Find_Kth takes $O(1)$ time** (direct index access).
- $\triangle$ **Insertion and Deletion** not only take $O(N)$ time, but also involve a lot of **data movements** which takes time (shifting elements).

---

### 2. Linked Lists

**Diagram description:** Each node contains:
- **Data** field
- **Pointer** (`next`) field pointing to the next node

Example structure with nodes containing names: `ZHAO -> QIAN -> SUN -> LI -> NULL`

Memory addresses illustration (locations may change on different runs):
```
Address  Data   Pointer
0010     SUN    1011
0011     QIAN   0010
0110     ZHAO   0011
1011     LI     NULL
```
Head pointer `ptr = 0110` (points to ZHAO).

**Initialization in C:**
```c
typedef struct list_node *list_ptr;
typedef struct list_node {
    char     data[4];
    list_ptr next;
};
list_ptr ptr;
```

**Linking nodes:**
```c
list_ptr N1, N2;
N1 = (list_ptr)malloc(sizeof(struct list_node));
N2 = (list_ptr)malloc(sizeof(struct list_node));
N1->data = 'ZHAO';
N2->data = 'QIAN';
N1->next = N2;
N2->next = NULL;
ptr = N1;
```

**Important:** Locations of the nodes may change on different runs (dynamic allocation).

#### Insertion — $O(1)$ time

**Diagram description:** Inserting a new node `temp` containing element `b` after node `node` (which points to `a_i`).

**Steps:**
```c
temp->next = node->next;  // Step 1: point temp to a_{i+1}
node->next = temp;        // Step 2: point node to temp
```

**Questions:**
- What will happen if the order of the two steps is reversed? (Answer: the link to the rest of the list would be lost — `node->next` would be overwritten before `temp->next` is set, breaking the chain.)
- How can we insert a new first item? (Answer: update the head pointer `ptr` to point to the new node.)

#### Deletion — $O(1)$ time

**Diagram description:** Deleting node `node` (which contains `b`). We need `pre` which points to `a_i` (the node before `node`).

**Steps:**
```c
pre->next = node->next;  // Step 1: bypass the node
free(node);              // Step 2: free the memory
```

**Questions:**
- How can we delete the first node from a list? (Answer: We can add a **dummy head node** to a list, so that the first real node is not pointed to by the head pointer directly.)

**Reference:** Read programs in Figures 3.6-3.15 for detailed implementations of operations.

---

### Doubly Linked Circular Lists

**Why do we need doubly linked lists?**
- In a singly linked list, finding the previous node from a given node requires traversing from the head again — $O(N)$.
- If you want to delete the $m$-th node, you need its predecessor. With a singly linked list, you'd have to go from the 1st node again.
- With a doubly linked list, you can go backwards.

**Node structure:**
```c
typedef struct node *node_ptr;
typedef struct node {
    node_ptr llink;    // link to previous node
    element   item;
    node_ptr rlink;    // link to next node
};
```

**Property:**
$$
\text{ptr} = \text{ptr}\to\text{llink}\to\text{rlink} = \text{ptr}\to\text{rlink}\to\text{llink}
$$
(This is the invariant of a properly linked doubly linked list.)

**Diagram description — Doubly linked circular list with head node:**
```
H <-> item1 <-> item2 <-> item3 <-> ... (circular, back to H)
```
- `H` is the dummy head node.
- Each node has `llink` (pointing left) and `rlink` (pointing right).
- The head node's `rlink` points to `item1` and `llink` points to the last node.
- The last node's `rlink` points back to `H`, and `H`'s `llink` points to the last node.

**Empty list:**
```
H <-> H  (both llink and rlink point to itself)
```

---

### Two Applications of Lists

#### (a) The Polynomial ADT

**Objects:**
$$
P(x) = a_1 x^{e_1} + \cdots + a_n x^{e_n}
$$
A set of ordered pairs $\langle e_i, a_i \rangle$ where $a_i$ is the coefficient and $e_i$ is the exponent. $e_i$ are **nonnegative integers**.

**Operations:**
- Finding degree, $\max\{e_i\}$, of a polynomial.
- Addition of two polynomials.
- Subtraction between two polynomials.
- Multiplication of two polynomials.
- Differentiation of a polynomial.

---

##### Representation 1: Array of Coefficients

```c
typedef struct {
    int CoeffArray[MaxDegree + 1];
    int HighPower;
} *Polynomial;
```

- Each exponent is used as an index into the array.
- **Advantage:** Easy to implement most operations, such as Add and Multiplication.
- **Disadvantage:** Wastes space for sparse polynomials.

**Example to illustrate the problem:**
- $P_1(x) = 10x^{1000} + 5x^{14} + 1$
- $P_2(x) = 3x^{1990} - 2x^{1492} + 11x + 5$

The array would need size at least 1991, but most entries are zero. This is very wasteful.

**Time complexity for multiplication of two polynomials of degree $N_1$ and $N_2$:**
$$
O(N_1 \times N_2)
$$

---

##### Representation 2: Linked List

**Diagram description:** Each term is represented as a node containing:
```
[Coefficient | Exponent | Next ->]
```

The list is sorted by exponent in descending order.

**Declaration:**
```c
typedef struct poly_node *poly_ptr;
struct poly_node {
    int      Coefficient;  /* assume coefficients are integers */
    int      Exponent;
    poly_ptr Next;
};
typedef poly_ptr a;  /* nodes sorted by exponent */
```

**Diagram description:**
```
a -> [a_0 | e_0 | ->] [a_1 | e_1 | ->] ... [a_{m-1} | e_{m-1} | NULL]
```

---

#### (b) Multilists

**Example:** Suppose that we have 40,000 students and 2,500 courses. Print the students' name list for each course, and print the registered classes list for each student.

##### Representation 1: 2D Array
```c
int Array[40000][2500];
```
- This is **enormous** (40,000 $\times$ 2,500 = 100 million entries).
- Most entries would be empty (each student takes only a few courses).

##### Representation 2: Multilist (cross-linked list)

**Diagram description:** A list structure with both vertical and horizontal links:
- Student nodes (S1, S2, S3, S4, S5) are connected horizontally.
- Course nodes (C1, C2, C3, C4) are connected vertically.
- Enrollment records are cross-linked in both directions.
- This allows efficient traversal: for a given student, follow the student's links to see all courses; for a given course, follow the course's links to see all students.

**Homework:** Self-study the sparse matrix representation.

---

### 3. Cursor Implementation of Linked Lists (no pointer)

For languages that do not support pointers (e.g., older Pascal, BASIC), a cursor-based implementation can simulate linked lists using arrays.

**Features that a linked list must have:**
- The data are stored in a collection of structures.
- Each structure contains data and a pointer to the next structure.
- A new structure can be obtained from the system's global memory by a call to `malloc` and released by a call to `free`.

**Cursor space (global array of structures):**
```
Global cursor array:
Index   Element   Next
0                  (header/freelist head)
1        ...       ...
2        ...       ...
...      ...       ...
S-1      ...       ...
```

**Key idea:**
- Use an array `CursorSpace[]` of structures, where `CursorSpace[i].Next` serves as the "pointer" (cursor).
- Index `0` is the header of the **freelist** (the list of available nodes).
- `malloc` equivalent: take a node from the freelist.
- `free` equivalent: return a node to the freelist.

**malloc (equivalent):**
```c
p = CursorSpace[0].Next;
CursorSpace[0].Next = CursorSpace[p].Next;
```

**free (equivalent):**
```c
CursorSpace[p].Next = CursorSpace[0].Next;
CursorSpace[0].Next = p;
```

**Diagram description:**
- Before `malloc`: index 0 has Next pointing to the first free node (e.g., 2). After `malloc`: `p` gets the value 2, and `CursorSpace[0].Next` is updated to `CursorSpace[2].Next` (e.g., 5).
- Before `free(p)`: the node at index `p` is returned to the freelist by making its Next point to whatever `CursorSpace[0].Next` points to, then updating `CursorSpace[0].Next` to `p`.

**Note:** The interface for the cursor implementation (given in Figure 3.27 on p. 52) is **identical** to the pointer implementation (given in Figure 3.6 on p. 40).

**Note:** The cursor implementation is usually **significantly faster** because of the lack of memory management routines (no system calls to `malloc`/`free`).

Read operation implementations given in Figures 3.31-3.35.

---

## §3 The Stack ADT

### 1. ADT Specification

**Definition:** A **stack** is a **Last-In-First-Out (LIFO)** list — an ordered list in which insertions and deletions are made **at the top only**.

**Objects:** A finite ordered list with zero or more elements.

**Operations:**
```c
int IsEmpty(Stack S);
Stack CreateStack();
void DisposeStack(Stack S);
void MakeEmpty(Stack S);
void Push(ElementType X, Stack S);
ElementType Top(Stack S);
void Pop(Stack S);
```

**Diagram description of stack operations:**
- Elements are pushed onto the top (e.g., push 1, 2, 3, 4, 5, 6 in order).
- When popping, the most recently pushed element is removed first (LIFO).

**Notes:**
- A `Pop` (or `Top`) on an **empty stack** is an **error** in the stack ADT.
- `Push` on a **full stack** is an **implementation error** but **not an ADT error** (the ADT does not define capacity; that's an implementation detail).

---

### 2. Implementations

#### (a) Linked List Implementation (with a header node)

**Push:**
```c
// Step 1: point new node's next to current first cell
TmpCell->Next = S->Next;
// Step 2: point header to new node
S->Next = TmpCell;
```

**Diagram description:**
- `S` is the header node (dummy head).
- `S->Next` points to the first real element.
- New node `TmpCell` gets inserted right after `S`.

**Top:**
```c
return S->Next->Element;
```

**Pop:**
```c
FirstCell = S->Next;          // Step 1: remember the first cell
S->Next = S->Next->Next;      // Step 2: bypass it
free(FirstCell);              // Step 3: free memory
```

**Diagram description:**
- The first real element (pointed to by `S->Next`) is removed.
- `S->Next` is updated to point to the next element.

**Concern:** The calls to `malloc` and `free` are expensive.
- **Solution:** Simply keep another stack as a **recycle bin** (a pool of pre-allocated nodes).

---

#### (b) Array Implementation

```c
struct StackRecord {
    int Capacity;        /* size of stack */
    int TopOfStack;      /* the top pointer */
                         /* ++ for push, -- for pop, -1 for empty stack */
    ElementType *Array;  /* array for stack elements */
};
```

**Notes:**
1. The stack model must be **well encapsulated**. No part of your code, except for the stack routines, can attempt to access the `Array` or `TopOfStack` variable directly.
2. **Error check** must be done before `Push` or `Pop`/`Top`.

**Mechanism:**
- `TopOfStack` starts at `-1` (empty stack).
- `Push(X, S)`: increment `TopOfStack`, then `Array[TopOfStack] = X`.
- `Pop(S)`: decrement `TopOfStack`.
- `Top(S)`: return `Array[TopOfStack]`.

**Reference:** Read Figures 3.38-3.52 for detailed implementations of stack operations.

---

### 3. Applications

#### (a) Balancing Symbols

Check if parentheses `()`, brackets `[]`, and braces `{}` are balanced.

**Algorithm:**
```
Make an empty stack S;
while (read in a character c) {
    if (c is an opening symbol)
        Push(c, S);
    else if (c is a closing symbol) {
        if (S is empty) { ERROR; exit; }
        else {  /* stack is okay */
            if (Top(S) doesn't match c) { ERROR; exit; }
            else Pop(S);
        }
    }
}
if (S is not empty) ERROR;
```

**Complexity:** $T(N) = O(N)$ where $N$ is the length of the expression.

**This is an on-line algorithm** — it processes the input as it is read, without needing to look ahead.

---

#### (b) Postfix Evaluation (Reverse Polish Notation)

**Example — Infix expression:**
$$
a + b \times c - d / e
$$

**Prefix expression:**
$$
- + a \times b c / d e
$$

**Postfix expression:**
$$
a \; b \; c \times + \; d \; e / -
$$

- Operator with the **highest precedence** is evaluated first in postfix.
- **Reverse Polish notation:** operator follows its operands.

**Example — Evaluation of postfix expression:** $6 \; 2 / \; 3 - \; 4 \; 2 \times + = ?$

**Step-by-step evaluation:**

| Step | Token | Stack (top to right) | Action |
|------|-------|---------------------|--------|
| 1 | 6 | 6 | Push operand |
| 2 | 2 | 6, 2 | Push operand |
| 3 | / | 3 | Pop 2, Pop 6, compute $6/2=3$, push result |
| 4 | 3 | 3, 3 | Push operand |
| 5 | - | 0 | Pop 3, Pop 3, compute $3-3=0$, push result |
| 6 | 4 | 0, 4 | Push operand |
| 7 | 2 | 0, 4, 2 | Push operand |
| 8 | * | 0, 8 | Pop 2, Pop 4, compute $4 \times 2 = 8$, push result |
| 9 | + | 8 | Pop 8, Pop 0, compute $0+8=8$, push result |

**Result:** $8$

**Algorithm for Postfix Evaluation:**
```
Stack s;
while (read in a token t) {
    if (t is an operand)
        Push(t, s);
    else {  /* t is an operator */
        op2 = Pop(s);
        op1 = Pop(s);
        result = op1 t op2;  // e.g., op1 + op2, op1 * op2, etc.
        Push(result, s);
    }
}
FinalResult = Pop(s);
```

**Complexity:** $T(N) = O(N)$. **No need to know precedence rules** (they are already encoded in the postfix order).

---

#### (c) Infix to Postfix Conversion

**Example 1:** $a + b \times c - d = \; ?$

**Result:** $a \; b \; c \times + \; d -$

**Algorithm illustration step-by-step:**

| Token | Output | Stack (top to right) | Notes |
|-------|--------|---------------------|-------|
| a | a | | Operand: output directly |
| + | a | + | Operator: push onto stack |
| b | a b | + | Operand: output directly |
| * | a b | +, * | Operator: * has higher precedence than +, push |
| c | a b c | +, * | Operand: output directly |
| - | a b c * + | - | Operator: * >= -? pop *; + >= -? pop +; then push - |
| d | a b c * + d | - | Operand: output directly |
| end | a b c * + d - | | Pop remaining operators |

**Key observations:**
- The **order of operands** is the same in infix and postfix.
- **Operators with higher precedence appear before those with lower precedence** in postfix.

**Example 2:** $a \times (b + c) / d = \; ?$

**Result:** $a \; b \; c + \times \; d /$

**Algorithm illustration step-by-step:**

| Token | Output | Stack (top to right) | Notes |
|-------|--------|---------------------|-------|
| a | a | | Operand: output |
| * | a | * | Operator: push |
| ( | a | *, ( | Left paren: push (highest incoming precedence) |
| b | a b | *, ( | Operand: output |
| + | a b | *, (, + | Operator: compare with (, ( has lower in-stack precedence, so push + |
| c | a b c | *, (, + | Operand: output |
| ) | a b c + | * | Right paren: pop and output until matching ( is found; pop +, then pop ( |
| / | a b c + * | / | Operator: * >= /? yes, pop *; then push / |
| d | a b c + * d | / | Operand: output |
| end | a b c + * d / | | Pop remaining operators |

**Complexity:** $T(N) = O(N)$

**Important rules for handling parentheses:**
1. **Never pop a `(` from the stack except when processing a `)`.**
2. When `(` is **not** in the stack, its precedence is the **highest**. But when it **is** in the stack, its precedence is the **lowest**.
   - Define **in-stack precedence** and **incoming precedence** for symbols.
   - Each time, use the corresponding precedence for comparison.

**Precedence table (typical):**

| Symbol | Incoming Precedence (ICP) | In-Stack Precedence (ISP) |
|--------|--------------------------|--------------------------|
| `(` | highest | lowest |
| `+`, `-` | 1 | 1 |
| `*`, `/` | 2 | 2 |
| `^` (exponentiation) | 3(right-assoc) | 3 |

**Special case — Exponentiation:**
- $a - b - c$ will be converted to $a \; b - c -$ (left-associative).
- However, $2^{2^3}$ must be converted to $2 \; 2 \; 3 \; \hat{} \; \hat{}$, not $2 \; 2 \; \hat{} \; 3 \; \hat{}$, since exponentiation associates **right to left**.

---

#### (d) Function Calls (System Stack / Runtime Stack)

**Concepts:**
- When a function is called, a **stack frame** (activation record) is pushed onto the system stack.
- The stack frame contains:
  - **Old Frame Pointer** (previous base pointer)
  - **Return Address** (where to continue after the function returns)
  - **Local Variables** (space for the function's local data)
- When the function returns, the frame is popped, and control returns to the saved return address.

**Recursion:**
- Recursion can **always** be completely removed (converted to iteration).
- **Non-recursive programs are generally faster** than equivalent recursive programs.
- **However, recursive programs are in general much simpler and easier to understand.**

**Tail Recursion:**
- A special form of recursion where the recursive call is the **last operation** in the function.
- Tail recursion can be easily removed by the compiler.

**Example — Bad use of recursion (tail recursion):**
```c
void PrintList(List L)
{
    if (L != NULL) {
        PrintElement(L->Element);
        PrintList(L->next);
    }
}  /* a bad use of recursion */
```
This is a tail recursion. For a list with 1 million elements, this would cause a **stack overflow** because each call consumes a stack frame.

**Compiler-removed version (iterative):**
```c
void PrintList(List L)
{
top:
    if (L != NULL) {
        PrintElement(L->Element);
        L = L->next;
        goto top;  /* do NOT do this in real code — use a loop */
    }
}  /* compiler removes recursion */
```

**Important:** "If 1 million elements are not enough to crash your program, try a larger one." — Be careful with deep recursion; the stack has limited space.

---

## §4 The Queue ADT

### 1. ADT Specification

**Definition:** A **queue** is a **First-In-First-Out (FIFO)** list — an ordered list in which **insertions take place at one end (Rear)** and **deletions take place at the opposite end (Front)**.

**Objects:** A finite ordered list with zero or more elements.

**Operations:**
```c
int IsEmpty(Queue Q);
Queue CreateQueue();
void DisposeQueue(Queue Q);
void MakeEmpty(Queue Q);
void Enqueue(ElementType X, Queue Q);  // insert at Rear
ElementType Front(Queue Q);            // examine Front element
void Dequeue(Queue Q);                 // remove Front element
```

**Diagram description:**
- Elements enter from the Rear side and exit from the Front side.
- Like a real-life queue (line of people): first person in line is first to be served.

---

### 2. Array Implementation of Queues

> **Note:** Linked list implementation is trivial (similar to linked list stack but with both head and tail pointers).

**QueueRecord structure:**
```c
struct QueueRecord {
    int Capacity;     /* max size of queue */
    int Front;        /* the front pointer */
    int Rear;         /* the rear pointer */
    int Size;         /* Optional - the current size of queue */
    ElementType *Array;  /* array for queue elements */
};
```

**Example — Job Scheduling in an Operating System:**

**Diagram description of queue operations step-by-step:**
1. **Enqueue Job 1:** Front=0, Rear=0, `Array[0] = Job1`
2. **Enqueue Job 2:** Front=0, Rear=1, `Array[1] = Job2`
3. **Enqueue Job 3:** Front=0, Rear=2, `Array[2] = Job3`
4. **Dequeue Job 1:** Front=1 (moves forward), Rear=2
5. **Enqueue Job 4:** Front=1, Rear=3, `Array[3] = Job4`
6. **Enqueue Job 5:** Front=1, Rear=4, `Array[4] = Job5`
7. **Enqueue Job 6:** Front=1, Rear=5, `Array[5] = Job6`
8. **Dequeue Job 2:** Front=2, Rear=5
9. **Enqueue Job 7:** Front=2, Rear=6, `Array[6] = Job7`
10. **Enqueue Job 8:** Front=2, Rear=7 — but if Capacity=8, Rear now equals Capacity, need to wrap around!

---

### Circular Queue

To avoid wasting space when Front moves forward and the space before Front becomes free, the queue is implemented as a **circular array**.

**Diagram description:**
```
Circular array with indices [0], [1], [2], [3], [4], [5]
- Initially: Rear and Front both at [0] (empty)
- After Enqueue Job 1: Rear moves to [1], Front at [0]
- After Enqueue Job 2, Job 3: Rear moves to [2], then [3]
- After Dequeue Job 1: Front moves to [1]
- After Enqueue Job 4, Job 5: Rear wraps around if needed
- When Rear catches up to Front (from behind), the queue is full
```

**Key operations in circular queue:**
- `Enqueue`: `Rear = (Rear + 1) % Capacity; Array[Rear] = X;`
- `Dequeue`: `Front = (Front + 1) % Capacity;`
- Empty condition: `Front == Rear`
- Full condition: `(Rear + 1) % Capacity == Front` (one slot is left empty to distinguish full from empty)

**Question:** Why is the queue announced full while there is still a free space left?
- **Answer:** To distinguish "full" from "empty". In a circular queue, `Front == Rear` means empty. If we filled all slots, then `Front == Rear` could also mean full — ambiguous. So we sacrifice one slot to distinguish the two states.

**How to avoid wasting that one space:**
- **Add a `Size` field** to the structure to track the current number of elements. With `Size`, `Front == Rear` with `Size == 0` means empty, and `Front == Rear` with `Size == Capacity` means full — no wasted slot.
- **Other ideas:** Use a flag bit to indicate whether the last operation was an enqueue or dequeue.

---

### Bonus Problem (from the slides)

**LRU-K** (2 points)
- Due: Tuesday, June 16th, 2026 at 10:00pm
- The problem can be found and submitted at: https://pintia.cn/

---

## Summary of Time Complexities

| Operation | Array List | Linked List | Array Stack | Linked Stack | Array Queue (Circular) | Linked Queue |
|-----------|-----------|-------------|-------------|--------------|----------------------|--------------|
| Find_Kth  | $O(1)$ | $O(N)$ | N/A | N/A | N/A | N/A |
| Insertion | $O(N)$ | $O(1)$ (given position) | $O(1)$ (Push) | $O(1)$ (Push) | $O(1)$ (Enqueue) | $O(1)$ (Enqueue) |
| Deletion  | $O(N)$ | $O(1)$ (given position) | $O(1)$ (Pop) | $O(1)$ (Pop) | $O(1)$ (Dequeue) | $O(1)$ (Dequeue) |

## Key Terminology

| Term | Definition |
|------|-----------|
| **ADT** | Abstract Data Type — separates specification from implementation |
| **List** | Ordered collection of items |
| **Sequential mapping** | Array-based storage where element $i$ is at position `base + i` |
| **Linked List** | Nodes connected by pointers, dynamic size |
| **Doubly Linked Circular List** | Each node has prev/next pointers; last points back to head |
| **Cursor Implementation** | Array-based linked list simulation using indices instead of pointers |
| **Stack** | LIFO structure — insert/delete at top only |
| **Push** | Insert onto stack |
| **Pop** | Remove from stack |
| **Top** | Examine top element |
| **Queue** | FIFO structure — insert at Rear, delete from Front |
| **Enqueue** | Insert into queue |
| **Dequeue** | Remove from queue |
| **Front** | Examine front element |
| **Circular Queue** | Array-based queue that wraps around to reuse space |
| **Tail Recursion** | Recursion where the recursive call is the last operation |
| **Postfix/Reverse Polish** | Operator follows operands — no precedence rules needed |
| **Prefix** | Operator precedes operands |
| **Infix** | Operator between operands (normal notation) |
