!<img width="2940" height="1912" alt="image" src="https://github.com/user-attachments/assets/57698bc8-08e2-41b2-8bb6-8fead7485b43" />


I tested row-major and column-major traversal using four different matrix sizes. Both functions produced the same sum, which confirms that they performed the same computation. However, the execution times were different.

Matrix Size

Row-Major (ms)

Column-Major (ms)

Ratio (Col/Row)

1000 x 1000

3.28

6.17

1.88x

2000 x 2000

5.69

22.03

3.87x

4000 x 4000

14.01

99.47

7.10x

8000 x 8000

57.00

370.36

6.50x

The results show that row-major traversal was faster for every matrix size. The performance difference became more noticeable as the matrix sizes increased. At 4000 x 4000, column-major traversal took about seven times longer than row-major traversal.



I tested the program using three compiler optimization levels: O0, O2, and O3.

All execution times are in milliseconds.

Matrix Size

O0 Row

O0 Column

O2 Row

O2 Column

O3 Row

O3 Column

1000 x 1000

0.83

1.29

0.06

0.87

0.06

1.04

2000 x 2000

3.33

21.92

0.24

6.26

0.30

7.52

4000 x 4000

12.31

94.50

2.04

50.03

2.15

51.44

8000 x 8000

58.38

383.20

15.50

184.56

7.51

186.84

The O2 and O3 optimization levels improved the performance of row-major traversal considerably. However, they did not eliminate the performance gap between the two traversal methods. Even with O3, column-major traversal was much slower because the memory access pattern still causes more cache misses. Compiler optimization can improve the instructions being executed, but it cannot completely eliminate the problems caused by accessing memory in an inefficient order.

Section 3: Assembly Analysis

I used Compiler Explorer with x86-64 GCC to examine both traversal functions.

!<img width="2940" height="1912" alt="image" src="https://github.com/user-attachments/assets/57698bc8-08e2-41b2-8bb6-8fead7485b43" />




<img width="1470" height="956" alt="Screenshot 2026-11-03 at 1 36 43 PM" src="https://github.com/user-attachments/assets/ec1708c7-be30-4e68-82e6-be786b31bce8" />
The two functions generate similar types of assembly instructions because they perform the same basic operations. Both load values from memory, add them to a running total, and use loops to visit the matrix elements. However, the order of their memory accesses is different. The row-major function accesses consecutive elements within each row, while the column-major function moves between different rows for each element. This shows that the performance difference comes mainly from memory access patterns and cache behavior rather than the amount of computation.

Section 4: Cache Behavior Explanation

Row-major traversal is faster than column-major traversal because of how computers organize and access memory. In C, a traditional two-dimensional array is stored in row-major order. This means that the elements of the first row are stored next to each other, followed by the elements of the second row, and so on. In my program, each row is allocated separately, but the elements within each row are still stored next to each other in memory.

When the CPU accesses memory, it does not usually retrieve just one individual value. Instead, it fetches a block of nearby memory called a cache line. During row-major traversal, the program accesses elements next to each other in the same row. When the CPU loads one element, the surrounding elements are likely brought into the cache at the same time. This makes it faster to access the next elements because they may already be available in the cache. This is an example of spatial locality, which means accessing memory locations that are close together.

Column-major traversal works differently. Instead of finishing one row before moving to the next, it accesses one element from each row before moving to the next column. This causes the program to jump between different memory locations. Because the elements being accessed are farther apart, the CPU is less likely to find the next requested value in its cache. This leads to more cache misses and forces the CPU to retrieve additional data from main memory. As the matrix gets larger, these inefficient memory accesses become more noticeable and increase execution time.

The compiler cannot completely fix this problem through optimization because the two functions intentionally use different memory access patterns. Compiler optimizations can improve instructions, reduce unnecessary operations, and make loops more efficient. However, they cannot automatically guarantee that the column-major traversal will have the same cache behavior as row-major traversal. My results showed that even with O3 optimization, column-major traversal remained significantly slower. This demonstrates why writing code with efficient memory access patterns is important, not just relying on compiler optimization.

Connection to Final Project

Understanding cache behavior will help me make better decisions when building my personal project tracker. I plan to use data structures such as arrays or dynamic arrays to organize project information and structures to keep related project details together. I will try to access project data in a consistent order instead of repeatedly jumping between unrelated memory locations. Using cache-friendly data structures and access patterns should help my application run more efficiently as the number of projects increases.
