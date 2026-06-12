## 2-4 Suppose that all the integer operands are stored in the stack $$

- **类型**: MULTIPLE_CHOICE
- **分值**: 2 分
- **作者**: 考研真题

---

Suppose that all the integer operands are stored in the stack $$S_1$$, and all the operators in the other stack $$S_2$$.  The function `F()` does the following operations sequentially:

- (1) Pop two operands `a` and `b` from $$S_1$$;
- (2) Pop one operator `op` from $$S_2$$;
- (3) Calculate `b op a`; and
- (4) Push the result back to $$S_1$$.

Now given { 5, 8, 3, 2 } in $$S_1$$ (where 2 is at the top), and { `*`, `-`, `+` } in $$S_2$$ (where `+` is at the top).  What is remained at the top of $$S_1$$ after `F()` is executed 3 times? `[    ]`

A. -15
B. 15
C. -20
D. 20
