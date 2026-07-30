# 02. Disjoint Set Union (DSU) / Union-Find

## 1. Giới thiệu (Introduction)
Disjoint Set Union (DSU), hay còn gọi là Union-Find, là một cấu trúc dữ liệu dùng để quản lý một tập hợp các phần tử được chia thành các tập con không giao nhau (disjoint sets). Nó đặc biệt hiệu quả trong việc giải quyết các bài toán liên quan đến tính liên thông trong đồ thị, gộp các thành phần liên thông, và tìm chu trình. DSU trả lời rất nhanh hai câu hỏi: "Hai phần tử này có thuộc cùng một tập hợp không?" và "Gộp hai tập hợp lại với nhau".

## 2. Cấu trúc dữ liệu (Data Structure)
DSU được cài đặt phổ biến bằng mảng 1 chiều (hoặc Hash Map nếu các phần tử không phải là số nguyên liên tiếp).
- `parent[]`: Mảng lưu trữ nút cha của mỗi phần tử. Nếu `parent[i] == i`, thì `i` là nút đại diện (root/leader) của tập hợp chứa nó.
- `rank[]` hoặc `size[]`: Mảng lưu trữ độ sâu (rank) hoặc kích thước (size) của cây tương ứng để tối ưu hóa quá trình gộp (Union by Rank/Size).
- `count`: Biến lưu số lượng thành phần liên thông (Number of Connected Components) hiện có.

## 3. Các phép toán cơ bản (Basic Operations)
1. **MakeSet(x)**: Khởi tạo tập hợp mới chứa phần tử `x`. Ban đầu, mỗi phần tử là một tập hợp riêng biệt, `parent[x] = x`.
2. **Find(x)**: Tìm phần tử đại diện của tập hợp chứa phần tử `x`. Bằng cách duyệt theo mảng `parent` cho đến khi gặp `parent[root] == root`.
3. **Union(x, y)**: Gộp hai tập hợp chứa phần tử `x` và `y` thành một. Điều này được thực hiện bằng cách tìm đại diện của `x` và `y`, sau đó cho một đại diện làm con của đại diện kia.

## 4. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity**: 
  - Find, Union: $O(\alpha(n))$ (với $\alpha$ là hàm Inverse Ackermann), gần như là hằng số $O(1)$ trong thực tế vì $\alpha(n) \le 4$ với mọi giá trị $n$ có thể lưu trong máy tính.
- **Space Complexity**: $O(N)$ để lưu trữ các mảng `parent` và `rank`/`size`.

## 5. Cách cài đặt (Implementation)
Có hai kỹ thuật quan trọng để đạt được độ phức tạp $O(\alpha(n))$:
- **Path Compression (Nén đường)**: Thực hiện trong hàm Find. Mọi nút trên đường đi từ $x$ đến root sẽ được nối trực tiếp vào root, làm bẹp cây.
- **Union by Rank/Size (Gộp theo hạng/kích thước)**: Thực hiện trong hàm Union. Cây nhỏ hơn (hoặc nông hơn) sẽ được gắn vào gốc của cây lớn hơn (hoặc sâu hơn) để đảm bảo chiều cao của cây không tăng quá nhanh.

## 6. Các biến thể (Variations)
1. **DSU with Bipartite Graph**: DSU mở rộng để kiểm tra đồ thị hai phía.
2. **Weighted DSU / DSU with Distances**: Duy trì khoảng cách từ mỗi nút đến gốc của nó.
3. **Persistent DSU**: DSU có thể khôi phục lại các trạng thái trước đó (Rollback DSU) - thường dùng mảng size thay vì path compression để có thể rollback $O(\log n)$.
4. **Dynamic Connectivity**: Xử lý đồ thị thêm và xóa cạnh offline.

## 7. Ưu điểm và nhược điểm (Pros & Cons)
- **Ưu điểm**:
  - Cực kỳ nhanh, hiệu quả.
  - Mã nguồn ngắn gọn, dễ triển khai.
  - Phù hợp với luồng dữ liệu liên tục (thêm cạnh động).
- **Nhược điểm**:
  - Khó khăn trong việc xóa một phần tử hoặc xóa một cạnh (cần Persistent DSU hoặc kỹ thuật offline).
  - Không cung cấp thông tin đường đi giữa hai đỉnh (chỉ biết có nối hay không).

## 8. Các trường hợp sử dụng (Use Cases)
- Tìm các thành phần liên thông trong đồ thị vô hướng.
- Phát hiện chu trình (Cycle Detection) trong đồ thị vô hướng.
- Thuật toán Kruskal để tìm Cây khung nhỏ nhất (MST).
- Mạng xã hội (Friend circles).
- Trò chơi (đánh giá trạng thái cờ vây/Go).

## 9. So sánh với các cấu trúc dữ liệu khác (Comparison)
- **DSU vs DFS/BFS**: DFS/BFS có thể tìm thành phần liên thông và chu trình với đồ thị tĩnh $O(V+E)$. DSU hiệu quả hơn nhiều khi đồ thị thay đổi liên tục (thêm cạnh mới - Dynamic Connectivity).
- **DSU vs Shortest Path**: DSU không giải được bài toán tìm đường đi ngắn nhất.

## 10. Mẫu nhận diện (Recognition Patterns)
Dấu hiệu nhận biết bạn cần DSU:
- Bài toán yêu cầu nhóm các đối tượng có quan hệ "tương đương" hoặc "kết nối".
- Từ khóa: "connected components", "union", "merge", "dynamic connectivity", "is connected to".
- Xây dựng mạng lưới từng bước một và cần kiểm tra tính liên thông ở các bước.

## 11. 20 câu hỏi phỏng vấn thường gặp (20 Common Interview Questions)
1. DSU là gì? Giải thích hai hàm Find và Union.
2. Path Compression làm gì? Tại sao nó hiệu quả?
3. Sự khác biệt giữa Union by Rank và Union by Size là gì?
4. Hàm Inverse Ackermann $\alpha(n)$ là gì và ý nghĩa của nó?
5. Làm thế nào để dùng DSU đếm số lượng thành phần liên thông?
6. Làm thế nào để phát hiện chu trình bằng DSU?
7. Thuật toán Kruskal sử dụng DSU như thế nào?
8. Tại sao DSU ít dùng cho đồ thị có hướng?
9. Có thể xóa một cạnh khỏi DSU thông thường không? Tại sao?
10. Làm sao để giải bài toán DSU offline (truy vấn xóa cạnh)?
11. Phân tích độ phức tạp thời gian khi không dùng Path Compression? $O(\log n)$ hoặc $O(n)$
12. Có bắt buộc dùng cả Path Compression và Union by Rank không? (Thực tế chỉ cần Path Compression là đủ cho hầu hết bài).
13. Giải thích Weighted DSU và một bài toán áp dụng.
14. Làm thế nào để tìm kích thước của một thành phần liên thông bằng DSU?
15. DSU có dùng bộ nhớ phụ không? (Không, chỉ mảng parent và size).
16. Code Rollback DSU khác DSU thường ở điểm nào?
17. DSU với cấu trúc Bipartite (ví dụ bài Satisfiability of Equality Equations).
18. So sánh DSU và DFS khi tìm Connected Components trên lưới 2D.
19. Giải thích ứng dụng của DSU trong bài toán "Lowest Common Ancestor" (Thuật toán Tarjan DSU).
20. Nếu có tập hợp các chuỗi thay vì số nguyên, bạn implement DSU thế nào? (Dùng HashMap/Map).

## 12. 20 lỗi thường gặp (20 Common Bugs)
1. Quên khởi tạo mảng `parent` (`parent[i] = i`).
2. Khởi tạo mảng `parent` bị thiếu phần tử (Index out of bounds).
3. Trong hàm `Find`, gán `parent[x] = Find(parent[x])` nhưng quên `return`.
4. Viết `Find(x)` mà không có Path Compression (chạy chậm thành $O(N)$).
5. Trong hàm `Union(x, y)`, gán `parent[x] = y` thay vì gán gốc của x cho gốc của y.
6. Quên kiểm tra `rootX == rootY` trước khi Union (dẫn đến logic sai khi đếm số thành phần hoặc tính size).
7. Gộp sai thứ tự trong Union by Rank (gắn cây to vào cây nhỏ).
8. Quên cập nhật tổng số lượng thành phần liên thông `count` sau mỗi phép gộp thành công.
9. Quên cập nhật mảng `size` khi Union.
10. Lẫn lộn giữa rank và size (rank là chiều cao, size là số nút).
11. Dùng DSU cho đồ thị có hướng để phát hiện chu trình (không chính xác).
12. Vượt quá kích thước mảng nếu các nút có nhãn từ 1 đến N (sử dụng `parent[N+1]`).
13. Hàm Find gọi đệ quy quá sâu gây Stack Overflow (hiếm gặp nếu có path compression và depth thấp, nhưng xảy ra ở cây thẳng dài nếu chưa nén).
14. Rollback DSU có dùng Path Compression (Rollback DSU không được dùng path compression, làm mất trạng thái cây).
15. Khởi tạo `size` là 0 thay vì 1.
16. So sánh `x == y` thay vì `Find(x) == Find(y)` để kiểm tra liên thông.
17. Dùng HashMap trong Java cho DSU nhưng quên implement hashCode/equals hoặc object references bị lỗi.
18. Khởi tạo DSU quá lớn gây Memory Limit Exceeded.
19. Cập nhật `count` giảm đi kể cả khi `rootX == rootY`.
20. Logic lặp lại trong Find khi dùng vòng lặp thay vì đệ quy.

## 13. 30 trường hợp góc (30 Edge Cases)
1. Đồ thị có 0 đỉnh.
2. Đồ thị có 1 đỉnh.
3. Đồ thị không có cạnh nào (số components bằng số đỉnh).
4. Đồ thị đầy đủ (đã liên thông hoàn toàn).
5. Nhiều cạnh song song (Parallel edges).
6. Khuyên (Self-loops).
7. Chỉ liên tục gọi Find mà không Union.
8. Liên tục gộp cùng một cặp đỉnh (Redundant unions).
9. Đồ thị dạng chuỗi thẳng dài (Line graph).
10. Đồ thị dạng hình sao (Star graph).
11. `N` rất lớn $\sim 10^5, 10^6$.
12. Mã định danh các nút không liên tiếp (Sparse IDs) - Yêu cầu dùng HashMap.
13. Mã định danh các nút là số âm hoặc rất lớn (sử dụng tọa độ 2D ánh xạ 1D).
14. Các ID nút bắt đầu từ 0.
15. Các ID nút bắt đầu từ 1.
16. Union by Size khi hai cây có cùng size.
17. Union by Rank khi hai cây có cùng rank.
18. Kiểm tra Find trên một nút chưa từng tồn tại (với HashMap DSU).
19. Gộp 2 đỉnh trên 2 nửa riêng biệt của đồ thị rời rạc.
20. Tính số components khi có các đỉnh cô lập.
21. Đồ thị dạng lưới 2D nơi đỉnh được quy đổi thành số $r * C + c$.
22. Lưới 2D không hợp lệ hoặc các cạnh cắt nhau (ví dụ: bài Regions Cut by Slashes).
23. Path compression chạy qua các thành phần được gộp ngược từ trước.
24. Cạnh được thêm vào sau đó cần xác định thứ tự cạnh dư thừa (Redundant connection).
25. Mạng đồ thị thay đổi cả trọng số và tính liên kết.
26. Số lượng truy vấn $Q$ lớn hơn nhiều so với $N$.
27. Đồ thị ngắt kết nối (Disconnected graphs).
28. Hủy thao tác (Undo Union) trong Persistent DSU với $k$ bước.
29. Weighted DSU với khoảng cách bằng âm.
30. DSU chứa nhiều dạng đối tượng khác nhau trộn lẫn.

## 14. Mẫu mã nguồn (Code Templates)
```java
class DSU {
    private int[] parent;
    private int[] size;
    private int count; // Số thành phần liên thông

    public DSU(int n) {
        parent = new int[n];
        size = new int[n];
        count = n;
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    // Path Compression
    public int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]);
        }
        return parent[x];
    }

    // Union by Size
    public boolean union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);

        if (rootX == rootY) {
            return false; // Đã chung tập hợp
        }

        // Đảm bảo gốc X có size lớn hơn
        if (size[rootX] < size[rootY]) {
            int temp = rootX;
            rootX = rootY;
            rootY = temp;
        }

        parent[rootY] = rootX;
        size[rootX] += size[rootY];
        count--;
        return true;
    }

    public int getCount() {
        return count;
    }
    
    public int getSize(int x) {
        return size[find(x)];
    }
}
```

## 15. Các lỗi phổ biến trong lập trình thi đấu (Common Competitive Programming Mistakes)
- Dùng đồ thị 1-indexed nhưng mảng `parent` chỉ cấp phát cỡ `N`, dẫn đến `IndexOutOfBounds`. (Cách khắc phục: `new int[N + 1]`).
- Bỏ qua hàm `count` bên trong DSU và thay vào đó đếm số `parent[i] == i` thủ công ở cuối chương trình - không sai nhưng chậm hơn.
- Không tối ưu hóa Path Compression, code đệ quy gọi quá nhiều vòng làm quá thời gian trên cây suy biến $O(N^2)$.
- Khi lưu trạng thái trước đó để làm bài có truy vấn quay lui, cố ý dùng path compression phá vỡ cấu trúc cây.
- Lẫn lộn mảng đỉnh và mảng giá trị (trong các bài DSU trên chuỗi hay mảng, nơi đỉnh thực sự là index còn giá trị thì khác).

## 16. Mẹo gỡ lỗi (Debugging Tips)
- In mảng `parent` để xem trạng thái liên thông. Tuy nhiên nhớ rằng sau hàm `union` không gọi hàm `find` cho mọi đỉnh, một số nút con ở dưới chưa được cập nhật trực tiếp `parent` đến gốc mà vẫn trỏ về ông cha trung gian.
- Nếu muốn xem root chính xác, phải dùng vòng lặp in `Find(i)`.
- Chú ý số lượng thành phần liên thông: kiểm tra biến `count` xem có đúng với kỳ vọng không sau khi gộp xong mọi thứ.

## 17. Tối ưu hóa (Optimization)
- **Cấu trúc phẳng (Flat Array)**: Thay vì dùng mảng cấp phát trên Heap, trong C++/Java có thể dùng mảng tĩnh toàn cục nếu kích thước dữ liệu cố định, chạy nhanh hơn.
- Bỏ qua mảng `size` (chỉ dùng Path Compression). Trong thực tế, chỉ Path Compression thôi đã đủ cho độ phức tạp xấp xỉ hằng số, bỏ `size` giúp tiết kiệm mem.

## 18. Các bài toán thực tế (Real-world Applications)
- Khai phá mạng lưới mạng (Network Connectivity).
- Quản lý bộ nhớ (Garbage collection).
- Hợp nhất vùng ảnh trong Xử lý ảnh (Image Segmentation).
- Mạng truyền thông để tìm tuyến nhánh kết nối hệ thống.
- Cân bằng tài khoản kế toán trong ngân hàng khi người dùng có nhiều thẻ ngân hàng khác nhau (merge accounts).

## 19. Câu hỏi hệ thống (System Design Questions)
- Làm thế nào thiết kế hệ thống gợi ý bạn bè (Friend Suggestion) trên một mạng xã hội tỷ người dùng?
  - DSU trên bộ nhớ không khả thi. Cần chia cắt theo Graph Partitioning và sử dụng Distributed Graph Processing.
- Trong Distributed DSU, làm sao giải quyết tranh chấp (conflict) khi hai máy chủ cùng gộp một tập hợp?
  - Sử dụng Lock, hoặc mô hình Eventual Consistency truyền message gộp cây.

## 20. Câu hỏi trắc nghiệm (Multiple Choice Questions)
**Câu hỏi 1**: Độ phức tạp tối đa của DSU với cả Path Compression và Union by Size là gì?
A. O(1)
B. O(log N)
C. O(α(N))
D. O(N)
*Đáp án: C*

**Câu hỏi 2**: Thuật toán nào sau đây cần dùng DSU?
A. Thuật toán Dijkstra
B. Thuật toán Kruskal
C. Thuật toán Prim
D. Thuật toán Bellman-Ford
*Đáp án: B*

## 21. Thử thách nâng cao (Advanced Challenges)
- Tự cài đặt Bipartite DSU: Kiểm tra đồ thị 2 phía khi các cạnh được cho lần lượt.
- Persistent DSU: Cài đặt DSU hỗ trợ thao tác `undo()` để xóa cạnh gần nhất.
- Dynamic Connectivity Offline: Trả lời các truy vấn kết nối, thêm, xóa cạnh trong thời gian $O(Q \log Q \log N)$ sử dụng Segment Tree kết hợp DSU Rollback.

## 22. Khuyến nghị thực hành (Practice Recommendations)
- Bắt đầu với các bài kiểm tra kết nối đơn giản: *Number of Provinces*.
- Chuyển sang các bài kết hợp Logic + DSU: *Redundant Connection*, *Accounts Merge*.
- Nâng cao với việc ánh xạ 2D -> 1D và DSU trên Grid: *Regions Cut By Slashes*, *Islands*.
- Thử nghiệm các bài tập cần giữ size/trọng số: *Number of Operations to Make Network Connected*, *Satisfiability of Equality Equations*.

## 23. Tài liệu tham khảo (References)
- *Introduction to Algorithms (CLRS)* - Chương về Data Structures for Disjoint Sets.
- Bài viết trên CP-Algorithms về Disjoint Set Union.

## 24. Tóm tắt (Summary)
Disjoint Set Union (DSU) là cấu trúc siêu cấp gọn nhẹ chuyên xử lý các mối quan hệ tương đương. Hai thao tác Find và Union với sự hỗ trợ của Path Compression và Union by Rank cho tốc độ tiệm cận $O(1)$. Bất cứ khi nào bạn thấy bài toán liên quan đến việc "gộp các nhóm lại với nhau" hoặc "kiểm tra hai phần tử có kết nối không" trên môi trường dữ liệu được cấp liên tục, hãy nghĩ ngay đến DSU.
