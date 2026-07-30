# Cấu trúc dữ liệu nâng cao: Advanced Trees (Sparse Table, LCA, Binary Lifting)

## 1. Khái niệm cơ bản
**Sparse Table** (Bảng thưa) là một cấu trúc dữ liệu cho phép trả lời các truy vấn Range Minimum Query (RMQ) - tìm giá trị nhỏ nhất trong một khoảng - trên một mảng tĩnh (không cập nhật dữ liệu) trong thời gian $O(1)$ sau khi tiền xử lý mất $O(N \log N)$.
**Lowest Common Ancestor (LCA)** là tổ tiên chung gần nhất của hai node trong một cây có gốc. Node tổ tiên chung này nằm sâu nhất có thể (xa gốc nhất).
**Binary Lifting** (Nhảy nhị phân) là một kỹ thuật dùng để tối ưu hóa quá trình di chuyển trên cây hoặc đồ thị bằng cách lưu các bước nhảy có độ dài là lũy thừa của 2 (1, 2, 4, 8...). Kỹ thuật này giúp giải quyết bài toán LCA và Kth Ancestor trong thời gian $O(\log N)$ sau khi tiền xử lý $O(N \log N)$.

## 2. Mục đích
- **Hiệu năng cao cho dữ liệu tĩnh**: Khi dữ liệu không thay đổi, Sparse Table vượt trội hơn Segment Tree cho bài toán RMQ do truy vấn chỉ mất $O(1)$.
- **Truy vấn trên cây**: Binary Lifting giúp truy vấn LCA và Kth Ancestor nhanh chóng, rất hữu ích cho các bài toán tính khoảng cách giữa hai node, truy vấn trọng số lớn nhất/nhỏ nhất trên đường đi trong cây.

## 3. Đặc điểm và tính chất
- **Không hỗ trợ cập nhật động**: Sparse Table thông thường không hỗ trợ update (nếu cần update, phải dùng Segment Tree hoặc Fenwick Tree).
- **Tính giao hoán (Idempotence)**: Sparse Table hoạt động tốt nhất trong $O(1)$ cho các phép toán có tính idempotent (như `min`, `max`, `gcd`, `bitwise AND/OR`) vì việc các khoảng giao nhau không làm thay đổi kết quả ($x \oplus x = x$).
- **Không gian lưu trữ**: Bảng Sparse Table và mảng cho Binary Lifting đều yêu cầu $O(N \log N)$ bộ nhớ, có thể không khả thi nếu $N$ quá lớn.

## 4. Các loại Advanced Trees & Concepts
- **Sparse Table cho RMQ/MaxQ/GCD**: Bảng 2D lưu giá trị hàm trên đoạn độ dài $2^j$.
- **LCA bằng Binary Lifting**: Lưu ma trận tổ tiên thứ $2^j$ cho mỗi node.
- **LCA qua RMQ (Euler Tour)**: Trải phẳng cây bằng Euler Tour để biến bài toán LCA thành bài toán RMQ, sau đó giải bằng Sparse Table hoặc Segment Tree.

## 5. Cấu trúc dữ liệu
- Sparse Table cần một mảng 2 chiều `st[N][LOG]`, trong đó `st[i][j]` lưu kết quả cho đoạn từ $i$ đến $i + 2^j - 1$.
- Binary Lifting cũng cần một mảng 2 chiều `up[N][LOG]`, trong đó `up[i][j]` lưu node tổ tiên thứ $2^j$ của node $i$.

## 6. Phân tích độ phức tạp (thời gian, không gian)
**Sparse Table (RMQ):**
- Tiền xử lý: Thời gian $O(N \log N)$, Không gian $O(N \log N)$.
- Truy vấn: Thời gian $O(1)$ (cho phép toán idempotent) hoặc $O(\log N)$ (cho phép cộng/nhân).

**LCA bằng Binary Lifting:**
- Tiền xử lý: Thời gian $O(N \log N)$, Không gian $O(N \log N)$.
- Truy vấn LCA: Thời gian $O(\log N)$.

## 7. Các thao tác cơ bản
- **Build Sparse Table**: $st[i][j] = \min(st[i][j-1], st[i + 2^{j-1}][j-1])$.
- **Query Sparse Table (RMQ)**: Tính $k = \log_2(R - L + 1)$, kết quả là $\min(st[L][k], st[R - 2^k + 1][k])$.
- **Build Binary Lifting**: $up[u][i] = up[ up[u][i-1] ][i-1]$.
- **Find LCA**: Đưa 2 node về cùng độ sâu (depth), sau đó nhảy cùng lúc lên trên nhờ mảng `up` cho đến khi tìm được tổ tiên.

## 8. Ứng dụng thực tế
- Sinh học máy tính (so sánh DNA).
- Trò chơi điện tử: Tìm đường đi ngắn nhất giữa hai object trên bản đồ phân cấp.
- Mạng máy tính: Tính toán đường đi tối ưu thông qua các router (Tree topology).
- Phân tích chuỗi và thuật toán chuỗi (Suffix Array kết hợp với LCP Array qua Sparse Table).

## 9. So sánh với các cấu trúc dữ liệu khác
- **Sparse Table vs Segment Tree**: Sparse Table nhanh hơn trong truy vấn ($O(1)$ so với $O(\log N)$) nhưng không hỗ trợ cập nhật tĩnh (Segment Tree cập nhật mất $O(\log N)$).
- **Binary Lifting vs Heavy-Light Decomposition (HLD)**: HLD tốn nhiều code hơn nhưng mạnh mẽ hơn (hỗ trợ update trên đường đi), trong khi Binary Lifting dễ cài đặt hơn cho các bài toán tĩnh.

## 10. Mã nguồn minh họa (Java)

**Sparse Table cho Range Minimum Query:**
```java
public class SparseTable {
    private int[][] st;
    private int[] log;

    public SparseTable(int[] arr) {
        int n = arr.length;
        int maxLog = (int) (Math.log(n) / Math.log(2)) + 1;
        st = new int[n][maxLog];
        log = new int[n + 1];

        // Tiền tính toán mảng log
        log[1] = 0;
        for (int i = 2; i <= n; i++) {
            log[i] = log[i / 2] + 1;
        }

        // Khởi tạo base case
        for (int i = 0; i < n; i++) {
            st[i][0] = arr[i];
        }

        // Xây dựng Sparse Table
        for (int j = 1; j < maxLog; j++) {
            for (int i = 0; i + (1 << j) <= n; i++) {
                st[i][j] = Math.min(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
            }
        }
    }

    public int query(int L, int R) {
        int j = log[R - L + 1];
        return Math.min(st[L][j], st[R - (1 << j) + 1][j]);
    }
}
```

## 11. 20 Câu hỏi phỏng vấn thường gặp
1. Sparse Table là gì và ưu điểm của nó so với Segment Tree?
2. Tại sao Sparse Table có thể trả lời truy vấn min/max trong $O(1)$?
3. Tính chất "idempotent" (lũy đẳng) là gì và ảnh hưởng của nó thế nào đến Sparse Table?
4. Sparse Table có thể dùng để tính tổng một mảng tĩnh không? Độ phức tạp truy vấn khi đó là bao nhiêu?
5. Trình bày thuật toán Binary Lifting.
6. Làm sao để tìm LCA bằng Binary Lifting?
7. Sự khác biệt giữa Binary Lifting và kỹ thuật 2 con trỏ?
8. Tại sao độ sâu (depth) lại quan trọng trong việc tìm LCA?
9. Euler Tour của một cây là gì?
10. Làm sao chuyển bài toán LCA thành bài toán RMQ bằng Euler Tour?
11. Bạn sẽ xử lý RMQ trên dữ liệu động như thế nào?
12. Có thể sử dụng Sparse Table cho bài toán GCD (Ước chung lớn nhất) không? Độ phức tạp ra sao?
13. Bạn xử lý các truy vấn Kth Ancestor như thế nào?
14. Nếu cấu trúc cây thay đổi (thêm node/cạnh mới), Binary Lifting còn dùng được không?
15. Khoảng cách giữa 2 node trên cây biểu diễn bằng LCA như thế nào?
16. Bạn có thể sử dụng Binary Lifting trên một đồ thị có hướng không chu trình (DAG) không?
17. Không gian bộ nhớ của Binary Lifting là bao nhiêu, làm sao để tối ưu?
18. So sánh LCA giải bằng Tarjan's Offline với LCA Binary Lifting.
19. Giải thích thuật toán Farach-Colton và Bender cho LCA/RMQ.
20. Nếu mỗi cạnh trong cây có trọng số, làm sao tìm khoảng cách nhỏ nhất trên đường đi giữa 2 node trong cây?

## 12. 20 Lỗi thường gặp (Bugs)
1. **Lỗi Off-by-one:** Đặt sai giới hạn cho mảng độ dài $2^j$.
2. **Quên tính log base 2 cho độ dài:** Dùng phép chia đơn thuần thay vì tìm lũy thừa của 2 lớn nhất nhỏ hơn đoạn truy vấn.
3. **Mảng chứa không đủ kích thước cho cột LOG:** Tính nhầm giới hạn `maxLog` dẫn tới ArrayIndexOutOfBounds.
4. **Không khởi tạo Base Case cho DFS/Binary Lifting:** Gốc không có độ sâu 0 hoặc tổ tiên gốc bị trỏ sai.
5. **Vòng lặp j xây dựng Sparse Table đặt sai thứ tự:** Cần lặp `j` (lũy thừa) ở ngoài, `i` (vị trí) ở trong. Nếu đảo ngược sẽ dẫn đến dữ liệu không khớp.
6. **Lỗi tính toán giới hạn của i trong Sparse Table:** Điều kiện `i + (1 << j) <= n` thường bị nhầm thành `< n`.
7. **Lỗi toán tử Bitwise:** Dùng sai thứ tự ưu tiên của toán tử, ví dụ `1 << j - 1` thay vì `1 << (j - 1)`.
8. **Nhảy nhị phân (Binary Lifting) không nhảy từ lũy thừa lớn xuống lũy thừa nhỏ:** Nếu duyệt từ nhỏ lên lớn, việc cộng lũy thừa không còn biểu diễn dưới hệ nhị phân chính xác.
9. **Lỗi truy vấn RMQ khi dùng hàm không idempotent (ví dụ tổng sum):** Truy vấn bị sai do tính chồng chéo (overlapping) của 2 nửa đoạn.
10. **Lỗi tính depth:** Trong LCA, quên cộng 1 cho độ sâu của node con khi DFS.
11. **Gán sai cha mẹ của root:** Trong mảng `up`, gán cha của gốc bằng -1 và không xử lý kỹ base case dẫn đến lỗi tràn mảng hoặc loop vô hạn.
12. **DFS tràn Stack:** DFS trên cây bị suy biến thành đường thẳng có thể gây StackOverflow; đôi khi phải tăng size của stack hoặc dùng BFS thay thế.
13. **Không xử lý việc hai node bằng nhau:** Nếu hai node giống nhau nhưng vẫn tiếp tục đưa lên cao, LCA sẽ bị sai.
14. **Quên nâng các node về cùng độ sâu trước:** Trong bước đầu tiên của thuật toán LCA Binary Lifting.
15. **Kết luận sai sau khi 2 node cùng độ sâu chưa bằng nhau:** Bước cuối cùng của Binary Lifting cho LCA kết thúc khi node cha là LCA, chứ không phải node hiện tại là LCA. Do đó, kết quả là `up[u][0]`.
16. **Nhầm lẫn các phép toán GCD/LCM khi dùng Sparse Table.**
17. **Cấp phát mảng lớn quá giới hạn Heap Memory:** Với $N = 10^6$ thì mảng 2 chiều tốn quá nhiều RAM.
18. **Chưa xử lý đồ thị có chu trình:** Cố gắng áp dụng Binary Lifting vào đồ thị vòng.
19. **Lỗi khởi tạo mảng log trước khi dùng (mảng log[] không đủ size).**
20. **Lưu sai thời gian vào/ra (in/out time) trong bài toán Euler Tour.**

## 13. 30 Trường hợp đặc biệt (Edge cases)
1. Cây chỉ có 1 node (Gốc).
2. Cây suy biến thành một danh sách liên kết thẳng (Linear List).
3. Mảng đầu vào chỉ có 1 phần tử (RMQ trên $N=1$).
4. Mảng chứa tất cả các phần tử giống nhau.
5. Truy vấn RMQ với khoảng cách lớn bằng toàn bộ mảng.
6. Truy vấn $L = R$ (một phần tử duy nhất).
7. Gốc (Root) được truy vấn LCA với chính nó.
8. Hai node trong truy vấn LCA có quan hệ cha-con trực tiếp.
9. Giá trị trong mảng rất lớn (Integer.MAX_VALUE) ảnh hưởng tới hàm sum hoặc max.
10. LCA của 2 node nằm ở hai nhánh con xa nhất của gốc.
11. Giá trị độ sâu của tree vượt quá giới hạn LOG dự đoán (ví dụ $N > 10^5$ nhưng LOG chỉ là 15).
12. Các giá trị số nguyên âm trong mảng (Sparse table tìm min vẫn chạy tốt).
13. Node Kth Ancestor yêu cầu K lớn hơn độ sâu của node đó (cần trả về null hoặc -1).
14. Kth Ancestor với $K = 0$ (trả về chính node đó).
15. Cây hình sao (Star graph) với một root và nhiều node lá.
16. Truy vấn 2 node giống nhau, LCA phải là chính nó.
17. Sparse Table với các phần tử max value có thể overflow nếu có sum query (dù sum không quản lý bởi Sparse table $O(1)$).
18. Euler tour tốn không gian $2N - 1$, dễ nhầm giới hạn mảng $N$.
19. Sparse Table truy vấn nghịch đảo ($L > R$).
20. Cây là một đồ thị nhị phân hoàn chỉnh.
21. Truy vấn liên tiếp trên các đoạn con nằm sâu và gần bằng nhau.
22. Bài toán Sparse Table yêu cầu tính bitwise XOR (không thể làm trong $O(1)$ do tính không giao hoán, phải làm trong $O(\log N)$).
23. Cây được cho mà không xác định gốc, và bạn cần chọn ngẫu nhiên một node.
24. Mảng có kích thước chính xác là lũy thừa của 2.
25. Chỉ số đỉnh trong cây không bắt đầu từ 0 (1-based index).
26. Mảng hoặc đồ thị chứa trọng số cạnh âm (LCA tìm min edge/max edge still work).
27. Bộ nhớ bị hạn chế gắt gao (memory tight constraint).
28. Quá trình tính Logarithm dùng hàm `Math.log()` gây tốn thời gian, cần tiền tính một mảng tĩnh trước.
29. Cấu trúc cây lớn làm Stack Overflow Error với DFS, phải chuyển sang code BFS.
30. Tìm đường đi kết hợp (Path query) có số cạnh chẵn hoặc lẻ làm ảnh hưởng giá trị trả về.

## 14. Kỹ năng gỡ lỗi (Debugging)
- **In ra mảng `st[][]`**: In ra nội dung của Sparse Table để xem giá trị min của từng đoạn `2^j` có chính xác hay không.
- **Biểu diễn ma trận cha `up[][]`**: In ra bảng Binary Lifting (node `i`, nhảy độ dài `2^j`), đây là bảng mấu chốt dễ hình dung tổ tiên.
- **Kiểm tra đồ thị bằng Graphviz**: Nếu dữ liệu cây bị vòng lặp, dùng dot/graphviz để xem có phải cây hợp lệ hay không.
- **Trace bằng test đơn giản (N = 4,5)**: Mô phỏng nhảy bước Binary Lifting bằng tay với độ dài $1, 2, 4$ để đối chiếu với output của thuật toán.

## 15. Các biến thể
- **2D Sparse Table**: Sử dụng Bảng thưa hai chiều để xử lý các ma trận lưới tĩnh.
- **Disjoint Sparse Table**: Một biến thể phức tạp hơn giúp xử lý các phép toán không có tính "lũy đẳng" (như phép cộng, phép nhân) bằng cách chia chẵn thành các khối.
- **LCA bằng RMQ**: Chuyển cây sang mảng tuyến tính bằng Euler Tour (mỗi khi thăm node, ghi vào mảng độ sâu của nó). Lấy RMQ độ sâu nhỏ nhất đoạn $[L, R]$ tương ứng chính là LCA. Kỹ thuật này đạt thời gian truy vấn $O(1)$ thay vì $O(\log N)$.

## 16. Mẹo và thủ thuật
- Luôn luôn tính trước một mảng tĩnh lưu giá trị log cơ số 2 của tất cả các số từ 1 đến $N$ để tăng tốc độ thay vì dùng `Math.log()` trong mỗi lượt gọi truy vấn.
- Trong thuật toán duyệt để tạo Binary Lifting, nếu node $u$ nhảy quá xa và vượt qua gốc (ví dụ node cha = -1 hoặc 0), hãy coi node tổ tiên vượt quá đó chính là node gốc (hoặc node ngoài luồng, tùy xử lý) để vòng lặp đơn giản.
- Chú ý thứ tự vòng lặp ở Sparse Table: `j` lặp trước rồi tới `i`.

## 17. Nền tảng toán học
- **Phép toán lũy đẳng (Idempotence)**: Hàm $f(a, b)$ gọi là lũy đẳng nếu $f(a, a) = a$ (ví dụ: max, min, AND, OR). Sự giao nhau của hai khoảng sẽ không làm sai lệch kết quả, ví dụ $\min(\min(A, B), \min(B, C)) = \min(A, B, C)$. Điều này giúp giải quyết truy vấn bất kỳ đoạn có độ dài $X$ bằng cách ghép hai nửa giao nhau độ dài $2^K$.
- **Hệ cơ số 2**: Mọi số nguyên đều có thể được phân tích thành tổng các lũy thừa phân biệt của 2. Binary Lifting dựa trên thực tế đó để "nhảy" theo các bit 1 trong biểu diễn nhị phân của khoảng cách cần nhảy.

## 18. Các bài toán kinh điển
- Range Minimum Query (RMQ) tĩnh.
- Kth Ancestor of a Tree Node.
- Lowest Common Ancestor của 2 node trong Binary Tree.
- Tìm khoảng cách đường đi ngắn nhất giữa hai node bằng công thức: $Dist(u, v) = Depth[u] + Depth[v] - 2 \cdot Depth[LCA(u, v)]$.
- Tính Max / Min / GCD trên đoạn đường đi từ u tới v.

## 19. Tài liệu tham khảo
- CLRS Algorithms (Ch. 21).
- Competitive Programmer's Handbook (Bảng thưa và Binary Lifting).
- CP-Algorithms (cp-algorithms.com/data_structures/sparse-table.html, cp-algorithms.com/graph/lca.html).
- GeeksforGeeks, LeetCode Discuss.

## 20. Câu hỏi trắc nghiệm
1. Độ phức tạp thời gian khi khởi tạo Sparse Table kích thước N?
   - A. $O(N)$
   - B. $O(N \log N)$ (Đúng)
   - C. $O(\log N)$
   - D. $O(1)$
2. Kỹ thuật Binary Lifting dựa trên nền tảng gì?
   - A. Số Fibonacci
   - B. Biểu diễn số nguyên dưới hệ thập phân
   - C. Biểu diễn số nguyên bằng các lũy thừa của 2 (Đúng)
   - D. Chia để trị theo 3 phần (Ternary Search)
3. Tìm phần tử nhỏ nhất bằng Sparse Table yêu cầu phép kết hợp gì của 2 nửa không?
   - A. Tính tổng
   - B. Trừ đi đoạn giao nhau
   - C. Tính min của 2 nửa, không quan tâm phần bị chồng chéo (Đúng)
   - D. Nhảy ngẫu nhiên

## 21. Bài tập thực hành
- Thiết kế một Class `SparseTable` hỗ trợ truy vấn Min, Max và GCD cùng lúc.
- Viết chương trình xây dựng cây từ mảng cạnh, tính bảng `up[][]` và viết hàm trả về `LCA(u, v)`.
- Cài đặt cấu trúc Euler Tour và trả về LCA qua Segment Tree hoặc Sparse Table.

## 22. Liên kết đến phần sau
- [Chương 20.6 - Suffix Tree and Suffix Automaton](./06-Suffix-Tree-Automaton.md): Áp dụng Range Minimum Query vào việc tính Longest Common Prefix (LCP) trên mảng hậu tố.

## 23. Ghi chú cá nhân
- (Dành cho học sinh ghi chép, chú thích các đoạn bug tự gặp phải trong quá trình học và sửa lỗi).

## 24. Tổng kết
Sparse Table và Binary Lifting tuy là những cấu trúc dữ liệu / thuật toán khá đặc thù và thiên về cạnh tranh (Competitive Programming) nhưng chúng mang lại hiệu năng cao trong môi trường truy vấn tĩnh. Nắm vững kỹ thuật Binary Lifting sẽ giúp học sinh rất nhiều trong các bài toán về cấu trúc đồ thị lớn, truy vấn đường đi dài, góp phần vào tư duy xử lý luồng dữ liệu logarit.
