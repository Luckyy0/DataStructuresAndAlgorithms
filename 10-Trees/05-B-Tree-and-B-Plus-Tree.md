# DSA-10-05: B-Tree và B+ Tree (Cây B và Cây B+)

---

## 1. Metadata

- **Document ID**: `DSA-10-05`
- **Version**: `1.0.0`
- **Prerequisites**:
  - Binary Search Tree (`DSA-10-01`)
  - AVL Tree (`DSA-10-02`)
  - Red-Black Tree (`DSA-10-03`)
  - Kiến trúc phân tầng bộ nhớ (Memory Hierarchy: CPU Cache, RAM, SSD/HDD Page I/O)
- **Learning Objectives**:
  - Thấu hiểu bản chất vật lý của Disk I/O, Block Storage và lý do tại sao cây nhị phân cân bằng (AVL, Red-Black Tree) sụp đổ hiệu năng khi kích thước dữ liệu vượt khỏi dung lượng RAM.
  - Nắm vững nền tảng toán học của B-Tree bậc $M$ (Order $M$): công thức tính chiều cao, số lượng khóa (keys) tối thiểu/tối đa, số con (children) tối thiểu/tối đa, và định lý cân bằng tuyệt đối.
  - Phân biệt chi tiết kiến trúc B-Tree cổ điển và B+ Tree hiện đại (sự tách biệt giữa Routing Index và Leaf Sequence Set).
  - Tự tay cài đặt (from scratch) một cấu trúc dữ liệu B+ Tree generic hoàn chỉnh, đạt chuẩn Production-Grade bằng **Java 21**, hỗ trợ Insert, Search, Split, Borrow, Merge, Range Scan và Invariant Validation.
  - Phân tích chi tiết cấp độ JVM: Object Memory Layout, CPU Cache Line alignment, Garbage Collection footprint và kỹ thuật quản lý bộ nhớ ngoài Heap (`MemorySegment` / Direct Memory) trong Java 21.
  - Phân tích kiến trúc lưu trữ thực tế trong các hệ quản trị cơ sở dữ liệu hàng đầu: MySQL InnoDB, PostgreSQL `nbtree`, SQLite, MongoDB WiredTiger, NTFS và XFS.
  - Nắm vững các kỹ thuật tối ưu hóa chuyên sâu: Latch Crabbing (Lock Coupling), Prefix Key Truncation, Cache-Conscious CSS-Tree/CSB-Tree, và Right-leaning Split optimization.
- **Estimated Reading Time**: 55 - 70 phút.
- **Difficulty**: Advanced / Staff Engineer Level.
- **Keywords**: `B-Tree`, `B+ Tree`, `Storage Engine`, `Disk Page I/O`, `Branching Factor`, `Page Split`, `Latch Crabbing`, `InnoDB Index`, `Range Query`, `Cache-Conscious`, `MemorySegment`.

---

## 2. Purpose (Mục đích)

Tài liệu này cung cấp cái nhìn toàn diện, chuẩn mực học thuật và thực chiến kỹ thuật về họ cấu trúc dữ liệu **Cây tìm kiếm đa phân cân bằng (Balanced Multi-way Search Trees)**, cụ thể là **B-Tree** và **B+ Tree**. 

B-Tree và B+ Tree được thiết kế nhằm tối ưu hóa chi phí truy xuất dữ liệu trên các thiết bị lưu trữ thứ cấp (Secondary Storage: HDD, SSD, NVMe) cũng như trên bộ nhớ chính (In-Memory) khi xét đến hiệu ứng phân tầng của CPU Cache Lines. Cấu trúc này giải quyết triệt để vấn đề "I/O Bottleneck" – điểm nghẽn lớn nhất trong các hệ thống Cơ sở dữ liệu quan hệ (RDBMS), NoSQL, Search Engines và File Systems hiện đại.

---

## 3. Motivation (Động lực phát triển)

### 3.1. Khoảng cách tốc độ trong Phân tầng Bộ nhớ (The Memory Latency Gap)

Trong khoa học máy tính, tốc độ truy xuất giữa các tầng phần cứng có sự chênh lệch hàng triệu lần:

| Tầng lưu trữ (Storage Level) | Thời gian truy xuất điển hình (Latency) | Chu kỳ CPU tương đương (CPU Cycles) |
| :--- | :--- | :--- |
| **L1 CPU Cache** | ~ 0.5 - 1 ns | ~ 1 - 2 cycles |
| **L2 CPU Cache** | ~ 3 - 4 ns | ~ 7 - 10 cycles |
| **L3 CPU Cache (Shared)** | ~ 10 - 40 ns | ~ 20 - 100 cycles |
| **Main Memory (DRAM)** | ~ 50 - 100 ns | ~ 150 - 300 cycles |
| **NVMe SSD (Random Page Read)** | ~ 10 - 50 µs ($10,000 - 50,000$ ns) | ~ $30,000 - 150,000$ cycles |
| **SATA SSD** | ~ 100 - 200 µs | ~ $300,000 - 600,000$ cycles |
| **Magnetic HDD (Rotational Disk Seek)** | ~ 5 - 10 ms ($5,000,000 - 10,000,000$ ns) | ~ $15,000,000 - 30,000,000$ cycles |

### 3.2. Cơ chế truyền dữ liệu theo Khối (Block/Page I/O)

Các hệ điều hành và thiết bị lưu trữ vật lý không bao giờ đọc hoặc ghi từng byte đơn lẻ. Thay vào đó, dữ liệu luôn được nạp/ghi theo từng **Khối (Block / Page)**:
- OS Page Size thông thường: $4 \text{ KB}$.
- Database Storage Engine Page Size (ví dụ MySQL InnoDB): $16 \text{ KB}$ (có thể cấu hình $8\text{ KB}$ hoặc $64\text{ KB}$).
- SSD Flash Memory Page: $4 \text{ KB} - 16 \text{ KB}$; Block Erasure: $2 \text{ MB} - 8 \text{ MB}$.

Dù chương trình chỉ cần đọc đúng $8 \text{ bytes}$ (một số `long`), phần cứng vẫn bắt buộc phải tải toàn bộ một Page $4 \text{ KB} - 16 \text{ KB}$ từ đĩa vào RAM Buffer.

### 3.3. Sự sụp đổ của Cây nhị phân trên Đĩa (Binary Tree Failure)

Xét một cây nhị phân cân bằng (như AVL Tree hoặc Red-Black Tree) chứa $N = 1,000,000,000$ ($10^9$) bản ghi.
- Chiều cao của cây:
  $$h \approx \log_2(10^9) \approx 30$$
- Vì mỗi node của cây nhị phân là một đối tượng độc lập, các con trỏ `left` và `right` trỏ tới các vùng nhớ ngẫu nhiên trên đĩa. Trong kịch bản xấu nhất (khi cây chưa nằm trên RAM Cache), việc tìm kiếm một phần tử đòi hỏi **30 lần đọc đĩa ngẫu nhiên (Random Disk I/O)**.
- Nếu dùng HDD ($10\text{ ms}$ / seek): $30 \times 10\text{ ms} = 300\text{ ms} = 0.3\text{ giây}$ cho một câu truy vấn đơn lẻ (chỉ thực hiện được ~3 queries/giây!).
- Nếu dùng NVMe SSD ($20\text{ µs}$ / read): $30 \times 20\text{ µs} = 600\text{ µs} = 0.6\text{ ms}$.
- **Sự lãng phí I/O**: Một node nhị phân chỉ chiếm khoảng $32 - 48 \text{ bytes}$. Khi nạp $16\text{ KB}$ Page từ đĩa, ta chỉ sử dụng $48\text{ bytes}$ và lãng phí $> 99.7\%$ băng thông I/O!

### 3.4. Giải pháp: Cây đa phân có Hệ số rẽ nhánh lớn (High Branching Factor)

Năm 1970, Rudolf Bayer và Edward M. McCreight phát minh ra **B-Tree**. Thay vì chỉ có 2 con, một node B-Tree có thể chứa hàng trăm tới hàng nghìn con ($M = 100 - 2000$), sao cho kích thước của một Node vừa khít với một **Disk Page** ($4\text{ KB} - 16\text{ KB}$).

- Nếu $M = 1000$, chiều cao cây cho $10^9$ phần tử:
  $$h \approx \log_{500}(10^9) \approx \frac{9}{2.699} \approx 3.33 \implies 3 \text{ đến } 4 \text{ tầng}$$
- Tầng Root luôn được cache trên RAM. Tầng 1 gần như luôn nằm trong RAM Cache.
- Như vậy, việc tìm kiếm một bản ghi trong 1 tỷ bản ghi chỉ tốn **1 đến 2 lần đọc đĩa vật lý**!

```
Binary Tree (h = 30):
Root -> Node -> Node -> ... (30 Disk Reads, 99.7% Page I/O Wasted)

B-Tree / B+ Tree (M = 1000, h = 3):
[ Root Node: 1000 keys ]  <-- Cached in RAM (0 Disk I/O)
       |
[ Internal Node: 1000 keys ] <-- Cached in RAM or 1 Disk I/O (16 KB page fully utilized)
       |
[ Leaf Page: 1000 Records ] <-- 1 Disk I/O to fetch actual Record
```

---

## 4. Mathematical Foundation (Nền tảng Toán học)

### 4.1. Định nghĩa B-Tree bậc $M$ (Order-$M$ B-Tree)

Một B-Tree bậc $M$ ($M \ge 3$) là một cây tìm kiếm đa phân thỏa mãn các tiên đề toán học sau:
1. **Ràng buộc về con (Children Invariant)**:
   - Mọi node có tối đa $M$ con.
   - Mọi node không phải Root và không phải Leaf (tức Internal Node) có ít nhất $\lceil M/2 \rceil$ con.
   - Node Root (nếu không phải là Leaf) có ít nhất $2$ con.
2. **Ràng buộc về khóa (Keys Invariant)**:
   - Một node có $k$ con sẽ chứa chính xác $k - 1$ khóa phân định (keys).
   - Mọi node (ngoại trừ Root) chứa tối thiểu $\lceil M/2 \rceil - 1$ khóa và tối đa $M - 1$ khóa.
   - Node Root chứa từ $1$ đến $M - 1$ khóa.
3. **Tính có thứ tự (Sorted Invariant)**:
   - Trong mỗi node, các khóa được sắp xếp tăng dần: $K_1 < K_2 < \dots < K_{k-1}$.
   - Con trỏ con $P_i$ trỏ tới cây con chứa tất cả các khóa nằm trong khoảng $(K_{i-1}, K_i)$ (với quy ước $K_0 = -\infty, K_k = +\infty$).
4. **Cân bằng tuyệt đối (Perfect Balance)**:
   - Tất cả các Leaf Node đều nằm ở cùng một độ sâu (cùng một tầng $h$).

> **Ghi chú về quy ước bậc (Order Convention)**:
> Trong tài liệu học thuật (Knuth), B-Tree bậc $M$ quy định số con tối đa là $M$. Trong giáo trình CLRS (Cormen), người ta dùng tham số $t$ (Minimum Degree, $t \ge 2$), khi đó số con tối thiểu là $t$ (trừ root), số con tối đa là $2t$, số key tối thiểu là $t - 1$, số key tối đa là $2t - 1$. Mối liên hệ: $M = 2t$. Trong tài liệu này, chúng ta sử dụng chuẩn quốc tế của Knuth với bậc $M$.

### 4.2. Bảng Tóm tắt Thông số B-Tree bậc $M$

| Thành phần | Số lượng tối thiểu (Min) | Số lượng tối đa (Max) |
| :--- | :--- | :--- |
| **Số con của Root** | $2$ (nếu không phải Leaf) | $M$ |
| **Số con của Internal Node** | $\lceil M / 2 \rceil$ | $M$ |
| **Số khóa của Root** | $1$ | $M - 1$ |
| **Số khóa của Node bất kỳ (trừ Root)** | $\lceil M / 2 \rceil - 1$ | $M - 1$ |
| **Độ sâu của mọi Leaf Node** | $h$ | $h$ (Hoàn toàn bằng nhau) |

### 4.3. Chứng minh Định lý Chiều cao B-Tree (Height Upper Bound Proof)

**Định lý**: Một B-Tree bậc $M$ chứa $N$ khóa có chiều cao tối đa $h$ (với quy ước cây chỉ có Root ở tầng 0) thỏa mãn:
$$h \le \log_{\lceil M/2 \rceil} \left( \frac{N + 1}{2} \right)$$

**Chứng minh**:
1. Để chiều cao đạt cực đại ($h$ lớn nhất), số lượng khóa tại mỗi node phải đạt mức tối thiểu.
2. Đếm số lượng node tối thiểu ở mỗi tầng:
   - Tầng $0$ (Root): có ít nhất $1$ node.
   - Tầng $1$: Root có ít nhất $2$ con $\implies$ ít nhất $2$ nodes.
   - Tầng $2$: Mỗi node ở tầng 1 có ít nhất $\lceil M/2 \rceil$ con $\implies$ ít nhất $2 \lceil M/2 \rceil$ nodes.
   - Tầng $d$ ($1 \le d \le h$): có ít nhất $2 \lceil M/2 \rceil^{d-1}$ nodes.
3. Đếm số lượng khóa tối thiểu trên toàn cây:
   - Root có ít nhất $1$ khóa.
   - Mỗi node ở tầng $d \ge 1$ có ít nhất $\lceil M/2 \rceil - 1$ khóa.
4. Tổng số khóa $N$ trên toàn bộ cây có chiều cao $h$:
   $$N \ge 1 + \sum_{d=1}^{h} \left( 2 \lceil M/2 \rceil^{d-1} \cdot (\lceil M/2 \rceil - 1) \right)$$
   $$N \ge 1 + 2 (\lceil M/2 \rceil - 1) \sum_{i=0}^{h-1} \lceil M/2 \rceil^i$$
   Áp dụng công thức tổng cấp số nhân $\sum_{i=0}^{h-1} x^i = \frac{x^h - 1}{x - 1}$ với $x = \lceil M/2 \rceil$:
   $$N \ge 1 + 2 (\lceil M/2 \rceil - 1) \cdot \frac{\lceil M/2 \rceil^h - 1}{\lceil M/2 \rceil - 1} = 1 + 2(\lceil M/2 \rceil^h - 1) = 2 \lceil M/2 \rceil^h - 1$$
5. Biến đổi bất đẳng thức:
   $$N + 1 \ge 2 \lceil M/2 \rceil^h \iff \lceil M/2 \rceil^h \le \frac{N + 1}{2}$$
   Lấy logarit cơ số $\lceil M/2 \rceil$ hai vế:
   $$h \le \log_{\lceil M/2 \rceil} \left( \frac{N + 1}{2} \right) \quad \blacksquare$$

### 4.4. Hệ số lấp đầy và Hiệu suất sử dụng không gian (Space Utilization)

Theo định nghĩa, mỗi node B-Tree luôn đầy ít nhất $50\%$ dung lượng (ngoại trừ root). Trên thực tế, đối với các thao tác chèn ngẫu nhiên (Random Insertions), B-Tree đạt hệ số lấp đầy trung bình lý thuyết của Yao (1978):
$$\text{Fill Factor} \approx \ln(2) \approx 69.3\%$$
Biến thể **$B^*$-Tree** cải tiến cơ chế tràn node bằng cách chia sẻ khóa với node anh em (redistribution) trước khi tách, nâng hệ số lấp đầy tối thiểu lên $\frac{2}{3} \approx 66.7\%$ và trung bình lên $\approx 85\%$.

---

## 5. Core Theory: B-Tree vs. B+ Tree

Mặc dù B-Tree là nền tảng, hầu như toàn bộ các hệ thống Cơ sở dữ liệu và File Systems hiện đại đều sử dụng biến thể **B+ Tree**. Dưới đây là sự so sánh kiến trúc cốt lõi giữa hai cấu trúc:

```
======================================================================
                          B-TREE (Classic)
======================================================================
Internal & Leaf Nodes CHỨA CẢ Key và Data Record Pointer:
                 [ K1 | Data1 | K2 | Data2 ]
                /             |             \
      [ K3 | Data3 ]    [ K4 | Data4 ]    [ K5 | Data5 ]

- Nhược điểm: Kích thước Data Record lớn làm giảm số lượng Key chứa
  trong 1 Page -> Branching factor M bị nhỏ lại -> Cây cao hơn.
- Không thể quét dải (Range Scan) tuần tự; bắt buộc duyệt In-order cây.

======================================================================
                         B+ TREE (Modern)
======================================================================
Internal Nodes CHỈ CHỨA Routing Keys & Child Pointers (Không chứa Data):
                      [ K10       |       K20 ]
                     /            |            \
            [ K5  | K8 ]     [ K12 | K15 ]   [ K25 | K30 ]
           /      |     \       ...             ...
Leaf Nodes CHỨA TOÀN BỘ Key & Data Pointer + LIÊN KẾT ĐÔI TUẦN TỰ:
  [K1|D1] <-> [K5|D5] <-> [K8|D8] <-> [K10|D10] <-> [K12|D12] <-> [K20|D20]...
  \_____________________________/
       Sequence Set (Linked List hỗ trợ O(1) Range Scan sang trang kế)
```

### 5.1. So sánh Chi tiết B-Tree và B+ Tree

| Tiêu chí | B-Tree (Cổ điển) | B+ Tree (Hiện đại) |
| :--- | :--- | :--- |
| **Vị trí lưu trữ dữ liệu (Payload)** | Nằm ở **tất cả** các node (cả Internal Nodes lẫn Leaf Nodes). | **Chỉ nằm ở Leaf Nodes**. Internal Nodes chỉ lưu Routing Keys. |
| **Hệ số rẽ nhánh (Branching Factor $M$)** | Nhỏ hơn đáng kể, vì một phần bộ nhớ Page bị chiếm bởi Payload/Tuple pointers. | Cực lớn (Internal node chỉ chứa Key + 8-byte Pointer), tối đa hóa số key trên 1 Page. |
| **Chiều cao cây ($h$)** | Cao hơn B+ Tree với cùng số lượng bản ghi $N$. | Thấp hơn B-Tree (thường từ $3$ đến $4$ tầng cho hàng tỷ bản ghi). |
| **Truy vấn điểm (Point Lookup)** | Tốt nhất: $O(1)$ nếu key nằm ngay tại Root hoặc Internal tầng 1. Tệ nhất: $O(\log N)$. | Ổn định tuyệt đối: Mọi truy vấn luôn đi từ Root xuống chính xác tầng Leaf ($h$ bước). |
| **Truy vấn dải (Range Scan)** | Rất chậm trên Disk: Phải duyệt In-order DFS qua lại giữa các tầng, gây Random I/O. | **Cực nhanh**: Tìm Leaf đầu tiên bằng binary search ($O(h)$), sau đó duyệt danh sách liên kết $O(K)$. |
| **Độ phức tạp thao tác Xóa (Delete)** | Phức tạp, phải hoán đổi với In-order Successor/Predecessor nếu key nằm ở Internal node. | Đơn giản hơn: Chỉ xóa ở Leaf node; routing key ở internal node có thể giữ nguyên làm mốc. |
| **Độ phân mảnh bộ nhớ Cache** | Kém hơn, vì Internal nodes chứa dữ liệu không đồng nhất. | Tuyệt vời: Internal pages cực kỳ gọn nhẹ, dễ dàng nạp trọn vào CPU L1/L2/L3 Cache. |

---

### 5.2. Các Thao tác Cốt lõi trên B+ Tree

#### A. Tìm kiếm (Search / Point Lookup)
1. Bắt đầu từ Node Root.
2. Tại mỗi Internal Node, thực hiện **Binary Search** trên mảng `keys[]` để tìm chỉ số con $i$ nhỏ nhất sao cho $\text{SearchKey} < keys[i]$ (hoặc $\le$ tùy quy ước).
3. Di chuyển xuống con `children[i]`.
4. Lặp lại cho tới khi chạm tới Leaf Node.
5. Tại Leaf Node, thực hiện Binary Search để tìm chính xác khóa. Nếu thấy, trả về Value/Data pointer; nếu không, kết luận khóa không tồn tại.

#### B. Chèn (Insertion & Node Split)
Thao tác chèn luôn bắt đầu tại tầng **Leaf Node**:
1. Tìm Leaf Node đích tương ứng với `Key`.
2. Chèn `(Key, Value)` vào Leaf Node sao cho mảng dữ liệu duy trì thứ tự tăng dần.
3. **Kiểm tra Tràn Node (Overflow)**:
   - Nếu số lượng keys trong node $\le M - 1$: Kết thúc thao tác.
   - Nếu số lượng keys đạt $M$ (Node bị Overflow): Thực hiện **Tách Node (Split)**.
4. **Quy tắc Tách Node (Split Rules)**:
   - **Tách Leaf Node**:
     - Tạo một Leaf Node mới bên phải.
     - Giữ lại $\lceil M/2 \rceil$ phần tử đầu ở node cũ, chuyển các phần tử còn lại sang node mới.
     - Cập nhật con trỏ danh sách liên kết `next` và `prev`.
     - **Sao chép (Copy)** khóa đầu tiên của node mới lên Parent Node làm Routing Key.
   - **Tách Internal Node**:
     - Tạo một Internal Node mới bên phải.
     - Giữ lại $\lfloor M/2 \rfloor$ keys ở node cũ.
     - **Đẩy (Promote/Move)** phần tử chính giữa (Median Key) lên Parent Node (Median key này **không** được giữ lại ở node con).
     - Chuyển các keys và child pointers bên phải median sang node mới.
5. **Lan truyền Tách (Cascading Split)**:
   - Nếu Parent Node bị tràn sau khi nhận key mới, tiếp tục đệ quy tách Parent Node lên trên.
   - Nếu Node Root bị tràn: Tách Root thành 2 node con và tạo một **Root mới** chứa đúng 1 key và 2 con trỏ. **Đây là trường hợp duy nhất làm tăng chiều cao của cây**.

```
Tách Leaf Node (M = 4, Max keys = 3):
Chèn 40 vào Leaf [10, 20, 30] -> Tràn: [10, 20, 30, 40]
Split:
Node cũ: [10, 20]  <--->  Node mới: [30, 40]
              \               /
               Đẩy copy key 30 lên Parent: [ ... | 30 | ... ]

Tách Internal Node (M = 4, Max keys = 3):
Internal tràn: [10, 20, 30, 40]
Median key = 30 ĐƯỢC ĐẨY HẲN LÊN TRÊN:
Node trái: [10, 20]  ---  Parent mới: [ 30 ]  --- Node phải: [ 40 ]
```

#### C. Xóa (Deletion, Borrow & Merge)
Thao tác xóa luôn diễn ra tại tầng **Leaf Node**:
1. Tìm Leaf Node chứa khóa và xóa cặp `(Key, Value)`.
2. **Kiểm tra Thiếu hụt Node (Underflow)**:
   - Nếu số lượng keys $\ge \lceil M/2 \rceil - 1$ (hoặc nếu là Root): Kết thúc thao tác.
   - Nếu số lượng keys $< \lceil M/2 \rceil - 1$: Node bị Underflow, cần tái cân bằng theo thứ tự ưu tiên:
3. **Mượn khóa từ anh em (Borrow / Redistribution)**:
   - **Mượn từ Left Sibling**: Nếu Left Sibling có $> \lceil M/2 \rceil - 1$ keys:
     - Lấy phần tử lớn nhất của Left Sibling chuyển sang làm phần tử nhỏ nhất của node hiện tại.
     - Cập nhật Routing Key tương ứng trên Parent Node.
   - **Mượn từ Right Sibling**: Nếu Right Sibling có $> \lceil M/2 \rceil - 1$ keys:
     - Lấy phần tử nhỏ nhất của Right Sibling chuyển sang làm phần tử lớn nhất của node hiện tại.
     - Cập nhật Routing Key tương ứng trên Parent Node.
4. **Gộp Node (Merge / Coalesce)**:
   - Nếu cả 2 anh em liền kề đều chỉ có chính xác $\lceil M/2 \rceil - 1$ keys, không thể mượn:
   - Tiến hành gộp node hiện tại với Left Sibling (hoặc Right Sibling).
   - Chuyển toàn bộ keys/values sang một node, cập nhật lại con trỏ `next`/`prev`.
   - Xóa Routing Key và Child Pointer tương ứng trên Parent Node.
5. **Lan truyền Gộp (Cascading Merge)**:
   - Nếu Parent Node bị thiếu hụt sau khi xóa con trỏ, tiếp tục đệ quy mượn/gộp trên tầng Internal.
   - Nếu Root bị mất hết khóa (chỉ còn lại 1 con trỏ duy nhất): Xóa Root cũ, gán con trỏ duy nhất đó thành **Root mới**. **Đây là trường hợp duy nhất làm giảm chiều cao của cây**.

---

## 6. Visual Explanation (Trực quan hóa Cấu trúc)

### 6.1. Sơ đồ Kiến trúc Tổng thể của B+ Tree Bậc 4 ($M = 4$)

```
                                +-------------------+
                                | Root Internal     |
                                | Keys: [ 30 ]      |
                                | Ptrs: [ P0, P1 ]  |
                                +---------+---------+
                                         / \
                     +------------------+   +------------------+
                     | Internal Node 0  |   | Internal Node 1  |
                     | Keys: [ 10, 20 ] |   | Keys: [ 40, 50 ] |
                     | Ptrs: [c0,c1,c2] |   | Ptrs: [c3,c4,c5] |
                     +----+----+----+---+   +----+----+----+---+
                         /     |     \          /     |     \
       +----------------+   +--+---+  +-------+ |     |      +---------------+
       |                    |      |          | |     +-------------+        |
       v                    v      v          v v                   v        v
+--------------+     +--------------+     +--------------+     +---------------+
| Leaf Page 0  | <-> | Leaf Page 1  | <-> | Leaf Page 2  | <-> | Leaf Page 3   |
| [2, 5, 8]    |     | [10, 15, 18] |     | [20, 25]     |     | [30, 35]      |
+--------------+     +--------------+     +--------------+     +---------------+
       ^                                                               |
       |==================== SEQUENCE SET LINKED LIST =================|
```

### 6.2. Sơ đồ Mermaid: Quá trình Tách Node khi Chèn

```mermaid
flowchart TD
    subgraph S1 [Trạng thái 1: Leaf Node đầy M=4]
        L1["Leaf A: [ 10, 20, 30 ]"]
    end

    subgraph S2 [Trạng thái 2: Chèn 25 gây Overflow]
        L2["Leaf A (Overflow): [ 10, 20, 25, 30 ]"]
    end

    subgraph S3 [Trạng thái 3: Split và Đẩy Routing Key lên Parent]
        P["Parent Node: [ ... | 25 | ... ]"]
        LA["Leaf A (Left): [ 10, 20 ]"]
        LB["Leaf B (Right): [ 25, 30 ]"]
        P --> LA
        P --> LB
        LA <-->|next / prev| LB
    end

    S1 -->|Chèn 25| S2
    S2 -->|Tách đôi tại vị trí N/2| S3
```

---

## 7. Java 21 Production Implementation

Dưới đây là mã nguồn cài đặt hoàn chỉnh cấu trúc dữ liệu **B+ Tree Generic** bằng **Java 21**, tuân thủ nghiêm ngặt các nguyên tắc Clean Code, Type-Safe, hỗ trợ tìm kiếm điểm, chèn, xóa đầy đủ với Rebalancing (Borrow & Merge), duyệt dải (Range Query) và hàm kiểm định bất biến toán học (`validateInvariants`).

```java
package com.structures.trees.bplus;

import java.util.*;

/**
 * Production-grade B+ Tree implementation in Java 21.
 * Supports generic Comparable keys, dynamic splitting, borrowing, merging,
 * range scans via leaf sequence set, and structural invariant validation.
 *
 * @param <K> the type of keys maintained by this tree (must be Comparable)
 * @param <V> the type of mapped values
 */
public class BPlusTree<K extends Comparable<K>, V> {

    /**
     * Degree M (Maximum number of children for internal nodes / max capacity).
     */
    private final int order;

    /**
     * Minimum keys for internal node = ceil(order / 2) - 1
     */
    private final int minKeysInternal;

    /**
     * Minimum keys for leaf node = floor(order / 2)
     */
    private final int minKeysLeaf;

    /**
     * Root of the B+ Tree.
     */
    private Node<K, V> root;

    /**
     * Pointer to the first leaf node for O(1) start sequential scanning.
     */
    private LeafNode<K, V> firstLeaf;

    /**
     * Total number of key-value mappings in the tree.
     */
    private int size;

    public BPlusTree(int order) {
        if (order < 3) {
            throw new IllegalArgumentException("B+ Tree order M must be >= 3. Provided: " + order);
        }
        this.order = order;
        this.minKeysInternal = (int) Math.ceil(order / 2.0) - 1;
        this.minKeysLeaf = order / 2;
        this.root = new LeafNode<>();
        this.firstLeaf = (LeafNode<K, V>) this.root;
        this.size = 0;
    }

    public int size() {
        return size;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public int getOrder() {
        return order;
    }

    // =========================================================================
    // 1. SEARCH OPERATION
    // =========================================================================

    /**
     * Finds the value associated with the specified key.
     *
     * @param key the lookup key
     * @return an Optional containing the value if found, or empty Optional
     */
    public Optional<V> search(K key) {
        Objects.requireNonNull(key, "Search key cannot be null");
        LeafNode<K, V> leaf = findLeafNode(key);
        int idx = leaf.binarySearchKey(key);
        if (idx >= 0) {
            return Optional.of(leaf.values.get(idx));
        }
        return Optional.empty();
    }

    /**
     * Helper to navigate from root down to the target leaf node.
     */
    private LeafNode<K, V> findLeafNode(K key) {
        Node<K, V> curr = root;
        while (!curr.isLeaf) {
            InternalNode<K, V> internal = (InternalNode<K, V>) curr;
            int childIdx = internal.getChildIndex(key);
            curr = internal.children.get(childIdx);
        }
        return (LeafNode<K, V>) curr;
    }

    // =========================================================================
    // 2. RANGE QUERY (Leaf Linked-List Scan)
    // =========================================================================

    /**
     * Returns a list of all values with keys in the range [fromKey, toKey].
     *
     * @param fromKey lower bound key (inclusive)
     * @param toKey   upper bound key (inclusive)
     * @return list of matching values in ascending key order
     */
    public List<V> rangeSearch(K fromKey, K toKey) {
        Objects.requireNonNull(fromKey, "fromKey cannot be null");
        Objects.requireNonNull(toKey, "toKey cannot be null");
        List<V> result = new ArrayList<>();

        if (fromKey.compareTo(toKey) > 0) {
            return result;
        }

        LeafNode<K, V> curr = findLeafNode(fromKey);
        boolean completed = false;

        while (curr != null && !completed) {
            for (int i = 0; i < curr.keys.size(); i++) {
                K k = curr.keys.get(i);
                if (k.compareTo(fromKey) >= 0 && k.compareTo(toKey) <= 0) {
                    result.add(curr.values.get(i));
                } else if (k.compareTo(toKey) > 0) {
                    completed = true;
                    break;
                }
            }
            curr = curr.next;
        }
        return Collections.unmodifiableList(result);
    }

    // =========================================================================
    // 3. INSERT OPERATION
    // =========================================================================

    /**
     * Inserts a key-value mapping into the B+ Tree. If the key exists, updates its value.
     *
     * @param key   non-null key
     * @param value non-null value
     */
    public void insert(K key, V value) {
        Objects.requireNonNull(key, "Insert key cannot be null");
        Objects.requireNonNull(value, "Insert value cannot be null");

        LeafNode<K, V> targetLeaf = findLeafNode(key);
        int idx = targetLeaf.binarySearchKey(key);

        if (idx >= 0) {
            // Key already exists -> Update value in-place
            targetLeaf.values.set(idx, value);
            return;
        }

        // Insert into leaf in sorted position
        int insertionIndex = -(idx + 1);
        targetLeaf.keys.add(insertionIndex, key);
        targetLeaf.values.add(insertionIndex, value);
        size++;

        // Check if leaf overflows
        if (targetLeaf.keys.size() >= order) {
            splitLeaf(targetLeaf);
        }
    }

    private void splitLeaf(LeafNode<K, V> leaf) {
        int mid = leaf.keys.size() / 2;
        LeafNode<K, V> newRightLeaf = new LeafNode<>();

        // Move upper half elements to newRightLeaf
        newRightLeaf.keys.addAll(leaf.keys.subList(mid, leaf.keys.size()));
        newRightLeaf.values.addAll(leaf.values.subList(mid, leaf.values.size()));

        leaf.keys.subList(mid, leaf.keys.size()).clear();
        leaf.values.subList(mid, leaf.values.size()).clear();

        // Update doubly-linked list
        newRightLeaf.next = leaf.next;
        newRightLeaf.prev = leaf;
        if (leaf.next != null) {
            leaf.next.prev = newRightLeaf;
        }
        leaf.next = newRightLeaf;

        K promotedKey = newRightLeaf.keys.getFirst();

        if (leaf.parent == null) {
            // Root was a leaf and split -> create new Root
            InternalNode<K, V> newRoot = new InternalNode<>();
            newRoot.keys.add(promotedKey);
            newRoot.children.add(leaf);
            newRoot.children.add(newRightLeaf);

            leaf.parent = newRoot;
            newRightLeaf.parent = newRoot;
            this.root = newRoot;
        } else {
            InternalNode<K, V> parent = leaf.parent;
            newRightLeaf.parent = parent;
            parent.insertChildAfter(leaf, promotedKey, newRightLeaf);
            if (parent.keys.size() >= order) {
                splitInternal(parent);
            }
        }
    }

    private void splitInternal(InternalNode<K, V> internal) {
        int mid = internal.keys.size() / 2;
        K promotedKey = internal.keys.get(mid);

        InternalNode<K, V> newRightInternal = new InternalNode<>();

        // Move keys strictly after mid to new right internal node
        newRightInternal.keys.addAll(internal.keys.subList(mid + 1, internal.keys.size()));
        // Move children from mid + 1 to end
        newRightInternal.children.addAll(internal.children.subList(mid + 1, internal.children.size()));

        for (Node<K, V> child : newRightInternal.children) {
            child.parent = newRightInternal;
        }

        internal.keys.subList(mid, internal.keys.size()).clear();
        internal.children.subList(mid + 1, internal.children.size()).clear();

        if (internal.parent == null) {
            // Root internal split -> create new root
            InternalNode<K, V> newRoot = new InternalNode<>();
            newRoot.keys.add(promotedKey);
            newRoot.children.add(internal);
            newRoot.children.add(newRightInternal);

            internal.parent = newRoot;
            newRightInternal.parent = newRoot;
            this.root = newRoot;
        } else {
            InternalNode<K, V> parent = internal.parent;
            newRightInternal.parent = parent;
            parent.insertChildAfter(internal, promotedKey, newRightInternal);
            if (parent.keys.size() >= order) {
                splitInternal(parent);
            }
        }
    }

    // =========================================================================
    // 4. DELETE OPERATION
    // =========================================================================

    /**
     * Removes the key and its mapped value from the tree.
     *
     * @param key the key to remove
     * @return true if key was found and removed, false otherwise
     */
    public boolean delete(K key) {
        Objects.requireNonNull(key, "Delete key cannot be null");
        LeafNode<K, V> leaf = findLeafNode(key);
        int idx = leaf.binarySearchKey(key);

        if (idx < 0) {
            return false; // Key not present
        }

        leaf.keys.remove(idx);
        leaf.values.remove(idx);
        size--;

        handleLeafUnderflow(leaf);
        return true;
    }

    private void handleLeafUnderflow(LeafNode<K, V> leaf) {
        if (leaf == root) {
            return; // Root is allowed to have < minKeys
        }

        if (leaf.keys.size() >= minKeysLeaf) {
            return; // Satisfies minimum key invariant
        }

        InternalNode<K, V> parent = leaf.parent;
        int leafIdx = parent.children.indexOf(leaf);

        // 1. Try borrow from Left Sibling
        if (leafIdx > 0) {
            LeafNode<K, V> leftSibling = (LeafNode<K, V>) parent.children.get(leafIdx - 1);
            if (leftSibling.keys.size() > minKeysLeaf) {
                // Borrow rightmost from left sibling
                K borrowedKey = leftSibling.keys.removeLast();
                V borrowedVal = leftSibling.values.removeLast();
                leaf.keys.addFirst(borrowedKey);
                leaf.values.addFirst(borrowedVal);

                // Update parent routing key
                parent.keys.set(leafIdx - 1, leaf.keys.getFirst());
                return;
            }
        }

        // 2. Try borrow from Right Sibling
        if (leafIdx < parent.children.size() - 1) {
            LeafNode<K, V> rightSibling = (LeafNode<K, V>) parent.children.get(leafIdx + 1);
            if (rightSibling.keys.size() > minKeysLeaf) {
                // Borrow leftmost from right sibling
                K borrowedKey = rightSibling.keys.removeFirst();
                V borrowedVal = rightSibling.values.removeFirst();
                leaf.keys.add(borrowedKey);
                leaf.values.add(borrowedVal);

                // Update parent routing key
                parent.keys.set(leafIdx, rightSibling.keys.getFirst());
                return;
            }
        }

        // 3. Merge with sibling
        if (leafIdx > 0) {
            // Merge into Left Sibling
            LeafNode<K, V> leftSibling = (LeafNode<K, V>) parent.children.get(leafIdx - 1);
            leftSibling.keys.addAll(leaf.keys);
            leftSibling.values.addAll(leaf.values);

            leftSibling.next = leaf.next;
            if (leaf.next != null) {
                leaf.next.prev = leftSibling;
            }

            parent.keys.remove(leafIdx - 1);
            parent.children.remove(leafIdx);

            handleInternalUnderflow(parent);
        } else {
            // Merge Right Sibling into leaf
            LeafNode<K, V> rightSibling = (LeafNode<K, V>) parent.children.get(leafIdx + 1);
            leaf.keys.addAll(rightSibling.keys);
            leaf.values.addAll(rightSibling.values);

            leaf.next = rightSibling.next;
            if (rightSibling.next != null) {
                rightSibling.next.prev = leaf;
            }

            parent.keys.remove(leafIdx);
            parent.children.remove(leafIdx + 1);

            handleInternalUnderflow(parent);
        }
    }

    private void handleInternalUnderflow(InternalNode<K, V> internal) {
        if (internal == root) {
            if (internal.keys.isEmpty()) {
                // Collapse root: Child becomes new root
                if (!internal.children.isEmpty()) {
                    root = internal.children.getFirst();
                    root.parent = null;
                }
            }
            return;
        }

        if (internal.keys.size() >= minKeysInternal) {
            return;
        }

        InternalNode<K, V> parent = internal.parent;
        int idx = parent.children.indexOf(internal);

        // 1. Try borrow from Left Internal Sibling
        if (idx > 0) {
            InternalNode<K, V> leftSibling = (InternalNode<K, V>) parent.children.get(idx - 1);
            if (leftSibling.keys.size() > minKeysInternal) {
                K parentKey = parent.keys.get(idx - 1);
                K borrowedKey = leftSibling.keys.removeLast();
                Node<K, V> borrowedChild = leftSibling.children.removeLast();

                internal.keys.addFirst(parentKey);
                internal.children.addFirst(borrowedChild);
                borrowedChild.parent = internal;

                parent.keys.set(idx - 1, borrowedKey);
                return;
            }
        }

        // 2. Try borrow from Right Internal Sibling
        if (idx < parent.children.size() - 1) {
            InternalNode<K, V> rightSibling = (InternalNode<K, V>) parent.children.get(idx + 1);
            if (rightSibling.keys.size() > minKeysInternal) {
                K parentKey = parent.keys.get(idx);
                K borrowedKey = rightSibling.keys.removeFirst();
                Node<K, V> borrowedChild = rightSibling.children.removeFirst();

                internal.keys.add(parentKey);
                internal.children.add(borrowedChild);
                borrowedChild.parent = internal;

                parent.keys.set(idx, borrowedKey);
                return;
            }
        }

        // 3. Merge with Sibling
        if (idx > 0) {
            InternalNode<K, V> leftSibling = (InternalNode<K, V>) parent.children.get(idx - 1);
            K demotedParentKey = parent.keys.remove(idx - 1);
            parent.children.remove(idx);

            leftSibling.keys.add(demotedParentKey);
            leftSibling.keys.addAll(internal.keys);
            leftSibling.children.addAll(internal.children);

            for (Node<K, V> child : internal.children) {
                child.parent = leftSibling;
            }

            handleInternalUnderflow(parent);
        } else {
            InternalNode<K, V> rightSibling = (InternalNode<K, V>) parent.children.get(idx + 1);
            K demotedParentKey = parent.keys.remove(idx);
            parent.children.remove(idx + 1);

            internal.keys.add(demotedParentKey);
            internal.keys.addAll(rightSibling.keys);
            internal.children.addAll(rightSibling.children);

            for (Node<K, V> child : rightSibling.children) {
                child.parent = internal;
            }

            handleInternalUnderflow(parent);
        }
    }

    // =========================================================================
    // 5. INVARIANT VALIDATION & DEBUGGING
    // =========================================================================

    /**
     * Validates all mathematical B+ Tree invariants.
     * Throws IllegalStateException if any property is violated.
     */
    public void validateInvariants() {
        if (root == null) return;
        int leafDepth = -1;
        validateNode(root, 0, leafDepth);
    }

    private int validateNode(Node<K, V> node, int depth, int expectedLeafDepth) {
        if (node == root) {
            if (!node.isLeaf && node.childrenCount() < 2) {
                throw new IllegalStateException("Root internal node must have >= 2 children");
            }
        } else {
            int minKeys = node.isLeaf ? minKeysLeaf : minKeysInternal;
            if (node.keys.size() < minKeys) {
                throw new IllegalStateException("Node at depth " + depth + " underflowed: " + node.keys.size() + " < " + minKeys);
            }
        }

        if (node.keys.size() >= order) {
            throw new IllegalStateException("Node at depth " + depth + " overflowed: " + node.keys.size() + " >= " + order);
        }

        // Validate sorted keys
        for (int i = 0; i < node.keys.size() - 1; i++) {
            if (node.keys.get(i).compareTo(node.keys.get(i + 1)) >= 0) {
                throw new IllegalStateException("Keys are not strictly sorted in node: " + node.keys);
            }
        }

        if (node.isLeaf) {
            if (expectedLeafDepth != -1 && depth != expectedLeafDepth) {
                throw new IllegalStateException("Leaves are at different depths! Found leaf at " + depth + ", expected " + expectedLeafDepth);
            }
            return depth;
        } else {
            InternalNode<K, V> internal = (InternalNode<K, V>) node;
            if (internal.children.size() != internal.keys.size() + 1) {
                throw new IllegalStateException("Internal node must have exactly (keys.size() + 1) children");
            }

            int foundDepth = -1;
            for (Node<K, V> child : internal.children) {
                if (child.parent != internal) {
                    throw new IllegalStateException("Child's parent pointer does not match internal node");
                }
                int d = validateNode(child, depth + 1, foundDepth);
                if (foundDepth == -1) foundDepth = d;
            }
            return foundDepth;
        }
    }

    // =========================================================================
    // 6. NODE DATA STRUCTURE DEFINITIONS
    // =========================================================================

    abstract static class Node<K extends Comparable<K>, V> {
        final boolean isLeaf;
        final List<K> keys;
        InternalNode<K, V> parent;

        Node(boolean isLeaf) {
            this.isLeaf = isLeaf;
            this.keys = new ArrayList<>();
            this.parent = null;
        }

        int binarySearchKey(K key) {
            return Collections.binarySearch(keys, key);
        }

        abstract int childrenCount();
    }

    static final class InternalNode<K extends Comparable<K>, V> extends Node<K, V> {
        final List<Node<K, V>> children;

        InternalNode() {
            super(false);
            this.children = new ArrayList<>();
        }

        int getChildIndex(K key) {
            int idx = Collections.binarySearch(keys, key);
            if (idx >= 0) {
                return idx + 1;
            } else {
                return -(idx + 1);
            }
        }

        void insertChildAfter(Node<K, V> existingChild, K key, Node<K, V> newChild) {
            int idx = children.indexOf(existingChild);
            keys.add(idx, key);
            children.add(idx + 1, newChild);
        }

        @Override
        int childrenCount() {
            return children.size();
        }
    }

    static final class LeafNode<K extends Comparable<K>, V> extends Node<K, V> {
        final List<V> values;
        LeafNode<K, V> next;
        LeafNode<K, V> prev;

        LeafNode() {
            super(true);
            this.values = new ArrayList<>();
            this.next = null;
            this.prev = null;
        }

        @Override
        int childrenCount() {
            return 0;
        }
    }
}
```

---

## 8. Step-by-Step Execution Trace

Theo dõi từng bước chèn các phần tử vào một **B+ Tree bậc $M = 4$** (mỗi node chứa tối đa $M - 1 = 3$ keys; nếu đạt $4$ keys sẽ split):
Tập khóa chèn lần lượt: `10, 20, 30, 40, 50, 60`.

### Bước 1: Chèn 10, 20, 30
- Ban đầu Root là một Leaf rỗng.
- Chèn `10`, `20`, `30`: Leaf chứa $[10, 20, 30]$ (3 keys $\le 3$). Cây chưa split.
```
Root (Leaf): [ 10, 20, 30 ]
```

### Bước 2: Chèn 40 $\implies$ Split Leaf đầu tiên & Tạo Root mới
- Chèn `40` vào Leaf $\implies$ Leaf tràn: $[10, 20, 30, 40]$ (4 keys).
- Tách Leaf:
  - Left Leaf: $[10, 20]$
  - Right Leaf: $[30, 40]$
  - Copy phần tử đầu của Right Leaf là `30` lên Root mới.
```
                  [ 30 ] (New Root Internal)
                 /      \
    Leaf1: [10, 20] <-> Leaf2: [30, 40]
```

### Bước 3: Chèn 50
- `50 > 30` $\implies$ đi vào Leaf2.
- Leaf2 chứa $[30, 40, 50]$ (3 keys $\le 3$). Không split.
```
                  [ 30 ]
                 /      \
    Leaf1: [10, 20] <-> Leaf2: [30, 40, 50]
```

### Bước 4: Chèn 60 $\implies$ Split Leaf2 & Đẩy Key lên Root
- `60 > 30` $\implies$ đi vào Leaf2.
- Leaf2 tràn: $[30, 40, 50, 60]$ (4 keys).
- Tách Leaf2:
  - Left Leaf (Leaf2 giữ lại): $[30, 40]$
  - Right Leaf (Leaf3 mới): $[50, 60]$
  - Copy phần tử đầu của Leaf3 là `50` lên Root.
- Root nhận `50` $\implies$ Root chứa $[30, 50]$ (2 keys $\le 3$).
```
                     [ 30, 50 ] (Root)
                   /     |      \
     Leaf1: [10, 20] <-> Leaf2: [30, 40] <-> Leaf3: [50, 60]
```

---

## 9. Complexity Analysis (Phân tích Độ phức tạp)

| Thao tác | Disk I/O Complexity | In-Memory CPU Time | Space Complexity |
| :--- | :--- | :--- | :--- |
| **Point Lookup (Search)** | $O(\log_M N)$ | $O(\log_2 M \cdot \log_M N) = O(\log_2 N)$ | $O(1)$ auxiliary |
| **Insert (Random Key)** | $O(\log_M N)$ reads + $O(1)$ amortized writes | $O(M \cdot \log_M N)$ do dịch mảng | $O(1)$ auxiliary |
| **Delete (Single Key)** | $O(\log_M N)$ reads + $O(1)$ amortized writes | $O(M \cdot \log_M N)$ | $O(1)$ auxiliary |
| **Range Scan ($K$ keys)** | $O(\log_M N + \frac{K}{B})$ Page I/O ($B \approx \text{Page Capacity}$) | $O(\log_2 N + K)$ | $O(K)$ để lưu kết quả |

> **Tại sao Chi phí Ghi Amortized khi Split chỉ là $O(1)$?**
> Tương tự như cơ chế Rehash của Dynamic Array, một node sau khi split sẽ có khoảng $50\%$ dung lượng trống. Để node đó bị split một lần nữa, cần ít nhất $\approx M/2$ thao tác chèn tiếp theo vào cùng node đó. Do đó, chi phí split trung bình trên mỗi phép chèn là $O\left(\frac{1}{M/2}\right) \approx O(1)$.

---

## 10. JVM & System Architecture Analysis

### 10.1. Bộ nhớ Heap và Java Object Layout (JOL)

Khi cài đặt B+ Tree thuần in-memory trong JVM, ta phải đối mặt với cấu trúc phân mảnh con trỏ (Pointer-Chasing Overhead). Xét một Node chứa mảng con trỏ trong HotSpot 64-bit JVM (Compressed OOPs enabled):

```
+-------------------------------------------------------------+
| Java Object Header (12 bytes: 8B Mark Word + 4B Klass Word) |
+-------------------------------------------------------------+
| Field 'isLeaf' (1 byte) + Alignment Padding (3 bytes)       |
+-------------------------------------------------------------+
| Reference 'keys' (4 bytes Compressed OOP)                   |
+-------------------------------------------------------------+
| Reference 'children' or 'values' (4 bytes Compressed OOP)   |
+-------------------------------------------------------------+
| Reference 'parent', 'next', 'prev' (12 bytes)               |
+-------------------------------------------------------------+
Total Node Object Overhead: ~40 bytes (chưa tính Payload)
```

Mỗi mảng `ArrayList` hay đối tượng Wrapper (`Integer`, `Long`) lại kéo theo một Object Header riêng biệt ($16 - 24 \text{ bytes}$). Điều này làm giảm mật độ dữ liệu (Data Density) và gây ô nhiễm CPU L1/L2 Cache Lines ($64 \text{ bytes}$).

### 10.2. Off-Heap Memory & Java 21 `MemorySegment`

Để đạt hiệu năng tương đương các Database C++ (như RocksDB, InnoDB), các hệ thống Java hiện đại (Apache Cassandra 5.0, Chronicle Map, Kafka) không lưu B-Tree nodes dưới dạng Java Objects trên Heap mà quản lý các **Trang nhị phân phẳng (Flat Binary Pages)** trực tiếp trên **Off-Heap Memory** bằng **Foreign Function & Memory API (FFM / Project Panama - Java 21)**.

```java
// Java 21 Off-Heap Page Memory Allocation
try (Arena arena = Arena.ofShared()) {
    // Cấp phát một 16KB Page Off-Heap canh chỉnh đúng 4KB Sector
    MemorySegment page = arena.allocate(16 * 1024, 4096);
    
    // Page Header: [2B: KeyCount] [2B: FreeSpaceOffset]
    page.set(ValueLayout.JAVA_SHORT, 0, (short) 0); // keyCount = 0
    page.set(ValueLayout.JAVA_SHORT, 2, (short) (16 * 1024)); // freeSpace = 16384
    
    // Đọc ghi trực tiếp Binary Struct mà KHÔNG tạo ra bất kỳ GC Overhead nào!
}
```

---

## 11. OpenJDK & Standard Library Analysis

### Tại sao OpenJDK không dùng B-Tree cho `java.util.TreeMap`?
1. **Mục đích in-memory**: `TreeMap` và `TreeSet` được thiết kế cho tập dữ liệu thuần RAM. Trên RAM, việc chèn vào Red-Black Tree chỉ cần trỏ lại 2-3 con trỏ ($O(1)$ pointer rewrites), không phải dịch chuyển các phần tử trong mảng như B-Tree node split/insert.
2. **ConcurrentSkipListMap**: Đối với môi trường đa luồng, Java cung cấp `ConcurrentSkipListMap` (Lock-free Skip List) thay vì Concurrent B-Tree, bởi vì thuật toán Lock-free trên Skip List đơn giản hơn rất nhiều so với thuật toán Lock-coupling / Latch-crabbing trên B-Tree.

---

## 12. Production Usage in Real-World Systems

### 12.1. MySQL InnoDB (Clustered B+ Tree Index)
- Mọi bảng InnoDB đều là một **Clustered Index** tổ chức dưới dạng B+ Tree bậc cao với Page size mặc định là $16\text{ KB}$.
- **Primary Key Index**: Leaf Page chứa **toàn bộ dữ liệu thực của dòng** (Row Data / Tuples).
- **Secondary Index**: Leaf Page chứa `Secondary Key Value + Primary Key Value`. Khi tìm kiếm qua Secondary Index, InnoDB tìm Primary Key trước, sau đó thực hiện **Index Lookup (Bookmark Lookup / Re-traverse)** vào Clustered Index để lấy dòng hoàn chỉnh.

### 12.2. PostgreSQL (`nbtree`)
- PostgreSQL sử dụng thuật toán B-Tree cấp cao dựa trên bài báo kinh điển của **Lehman & Yao (1981)**.
- Điểm đặc biệt: B-Tree của Postgres có các con trỏ ngang (Right-link Pointers) ở cả tầng Internal Nodes, cho phép các tiến trình Reader tìm kiếm đồng thời mà không bị block ngay cả khi Writer đang thực hiện Split trang.

### 12.3. SQLite
- File database của SQLite là một chuỗi các trang $1\text{ KB} - 64\text{ KB}$ liên tiếp.
- Dùng **Table B-Tree (B+ Tree)** để lưu trữ các bảng dữ liệu có `rowid`, và **Index B-Tree (B-Tree cổ điển)** để lưu trữ các bảng `WITHOUT ROWID`.

---

## 13. Design Decisions & Trade-offs

### B+ Tree vs. LSM-Tree (Log-Structured Merge-Tree)

Các hệ thống lưu trữ hiện đại thường phải lựa chọn giữa **B+ Tree** (MySQL, Postgres, Oracle, SQLite) và **LSM-Tree** (RocksDB, Cassandra, ScyllaDB, Google Bigtable):

```
+---------------------+-------------------------------+-------------------------------+
| Tiêu chí            | B+ Tree Storage Engine        | LSM-Tree Storage Engine       |
+---------------------+-------------------------------+-------------------------------+
| Ghi dữ liệu (Write) | Random In-Place Writes        | Append-Only Sequential Writes |
|                     | Write Amplification: Cao      | Write Amplification: Thấp     |
+---------------------+-------------------------------+-------------------------------+
| Đọc dữ liệu (Read)  | Rất nhanh & ổn định (1-3 I/O) | Chậm hơn, phải kiểm tra qua   |
|                     | Read Amplification: Thấp      | MemTable, Bloom Filter, SSTs  |
+---------------------+-------------------------------+-------------------------------+
| Range Query Scan    | Tối ưu tuyệt đối qua Leaf List| Phải Merge-Sort nhiều SSTables|
+---------------------+-------------------------------+-------------------------------+
| Ứng dụng tối ưu     | OLTP, Đọc nhiều, RDBMS        | Ghi cực lớn (Logging, Time-S) |
+---------------------+-------------------------------+-------------------------------+
```

---

## 14. Common Bugs (20 Lỗi phổ biến khi triển khai)

1. **Lỗi lệch 1 phần tử (Off-by-one capacity check)**: Nhầm lẫn giữa điều kiện đầy node (`keys.size() == order` vs `keys.size() == order - 1`).
2. **Quên cập nhật Routing Key ở Parent khi Leaf bị Split**: Khi tách leaf node bên phải, không copy key nhỏ nhất của leaf mới lên làm khóa dẫn đường ở node cha.
3. **Đứt gãy liên kết đôi `next`/`prev`**: Khi split hoặc merge leaf node, quên cập nhật con trỏ ngược `leaf.next.prev = newLeaf`.
4. **Sai vị trí Median Key khi $M$ chẵn**: Tính toán chỉ số median không đồng nhất giữa Internal Node Split và Leaf Node Split.
5. **Memory Leak trên JVM**: Xóa phần tử trong danh sách nội bộ nhưng không làm sạch tham chiếu (Dangling reference), khiến GC không thể thu hồi object.
6. **Không thu hẹp Root khi Root bị rỗng (Root Collapse Bug)**: Sau khi merge tầng con, Root Internal chỉ còn lại 0 key và 1 child nhưng không hạ cấp child đó lên làm Root mới.
7. **Race Condition khi không dùng Latch Crabbing**: Đọc dữ liệu đồng thời khi một luồng khác đang thực hiện split node làm con trỏ trỏ vào vùng nhớ tạm.
8. **Đệ quy vô tận trong Cascading Rebalance**: Xử lý borrow/merge làm phát sinh underflow liên tục mà không dừng khi tới Root.
9. **Xử lý sai kết quả `Collections.binarySearch`**: Không chuyển đổi đúng giá trị âm `-(insertionPoint + 1)` khi key chưa tồn tại.
10. **Lỗi duplicate keys trên Multimap B+ Tree**: Không xác định rõ quy ước khi key bằng nhau (chèn sang nhánh trái hay nhánh phải).
11. **Mất dữ liệu khi đè Value**: Phép `insert` không kiểm tra key tồn tại để update mà lại chèn một bản ghi mới gây sai lệch thứ tự sorted.
12. **Bỏ sót cập nhật Parent Pointer của các Node con**: Khi chuyển một nhóm con trỏ sang Internal Node mới trong quá trình split, quên gán `child.parent = newInternalNode`.
13. **Lỗi NullPointerException ở biên Range Scan**: Quên kiểm tra `curr != null` khi duyệt con trỏ `next` đến trang leaf cuối cùng của cây.
14. **Deadlock khi khóa đồng thời 2 trang anh em**: Luồng 1 khóa Left $\to$ Right, Luồng 2 khóa Right $\to$ Left khi thực hiện Borrow.
15. **Tràn số nguyên (Integer Overflow) khi tính File Offset**: Tính toán byte offset cho page thứ $K$ bằng phép nhân `int` thay vì `long` (`(long) pageIndex * PAGE_SIZE`).
16. **Tách Internal Node giữ lại Median Key**: Trong B+ Tree, Internal Node Split **bắt buộc phải đẩy hẳn Median Key lên**, không được giữ nó lại ở tầng hiện tại (khác với Leaf Split là copy).
17. **Duyệt Range Scan lặp vô tận do vòng lặp con trỏ (Cyclic Link)**: Merge node sai khiến `leafA.next = leafB` và `leafB.next = leafA`.
18. **Không duy trì Invariant khi xóa hết phần tử**: Cây trở về trạng thái rỗng nhưng các con trỏ `root` và `firstLeaf` không được reset đồng bộ.
19. **Gộp nhầm anh em không cùng cha (Non-sibling Merge)**: Khi underflow, lấy nhầm "cousin node" (khác parent) để merge thay vì node anh em ruột cùng parent.
20. **Lỗi Flush Page mà không ghi WAL (Write-Ahead Log)**: Trong Storage Engine, ghi dữ liệu trang B+ Tree xuống đĩa trước khi ghi log khiến dữ liệu không thể phục hồi khi crash.

---

## 15. Edge Cases (30 Trường hợp Biên cần Kiểm thử)

1. **Khởi tạo cây rỗng**: `search()`, `delete()`, `rangeSearch()` trên cây rỗng phải trả về empty/false an toàn, không ném NPE.
2. **Chèn 1 phần tử duy nhất**: Root vừa là Root vừa là Leaf Node duy nhất.
3. **Chèn đầy đúng dung lượng Root Leaf ($M-1$ phần tử)**: Chưa xảy ra split, Root vẫn là Leaf.
4. **Chèn phần tử thứ $M$ gây Split đầu tiên**: Chiều cao cây tăng từ $1 \to 2$, Root chuyển từ LeafNode sang InternalNode.
5. **Chèn chuỗi khóa tăng dần liên tục ($1, 2, 3, \dots, 1000$)**: Kiểm tra cây luôn phân nhánh lệch phải và split ổn định.
6. **Chèn chuỗi khóa giảm dần liên tục ($1000, 999, \dots, 1$)**: Kiểm tra split lệch trái.
7. **Chèn các khóa trùng lặp liên tục**: Đảm bảo cập nhật Value chính xác tại chỗ, kích thước cây không đổi.
8. **Xóa trên cây chỉ có 1 phần tử**: Cây trở về trạng thái rỗng an toàn, `size == 0`.
9. **Xóa khóa không tồn tại nhỏ hơn khóa nhỏ nhất**: Hàm trả về `false`, cấu trúc cây không biến đổi.
10. **Xóa khóa không tồn tại lớn hơn khóa lớn nhất**: Cây giữ nguyên vẹn.
11. **Xóa khóa không tồn tại nằm xen kẽ giữa các khóa có sẵn**: Binary search trả về insertion point âm, không ném Exception.
12. **Xóa khóa tại Leaf nhưng không gây Underflow**: Leaf vẫn còn $\ge \lceil M/2 \rceil$ keys.
13. **Xóa gây Underflow nhưng mượn thành công từ Left Sibling**: Routing key ở parent được cập nhật đúng.
14. **Xóa gây Underflow nhưng mượn thành công từ Right Sibling**: Routing key ở parent được cập nhật đúng.
15. **Xóa gây Underflow và bắt buộc Merge với Left Sibling**: Parent mất 1 child pointer.
16. **Xóa gây Underflow và bắt buộc Merge với Right Sibling**.
17. **Cascading Merge lan truyền lên tận Root**: Chiều cao cây giảm đi $1$ tầng (Root cũ bị collapse).
18. **Xóa khóa nhỏ nhất của toàn bộ cây (Leftmost Key)**: Danh sách `firstLeaf` vẫn trỏ đúng mốc.
19. **Xóa khóa lớn nhất của toàn bộ cây (Rightmost Key)**.
20. **Range query với `fromKey > toKey`**: Trả về danh sách rỗng lập tức ($O(1)$).
21. **Range query với khoảng nằm hoàn toàn bên trái toàn bộ cây**: Trả về empty list.
22. **Range query với khoảng nằm hoàn toàn bên phải toàn bộ cây**: Trả về empty list.
23. **Range query khớp đúng 1 phần tử duy nhất trong cây**: Trả về list có `size == 1`.
24. **Range query bao trùm toàn bộ cây $[-\infty, +\infty]$**: Trả về đầy đủ tất cả $N$ phần tử theo thứ tự tăng dần.
25. **Cây với bậc tối thiểu hợp lệ $M = 3$ (2-3 B+ Tree)**: Mọi node chỉ có 1 hoặc 2 keys.
26. **Cây với bậc lớn $M = 1000$**: Kiểm tra hiệu năng binary search bên trong node.
27. **Thao tác xen kẽ liên tục Insert và Delete cùng một khóa**: Kiểm tra tránh hiện tượng co giãn đập nhả liên tục (Thrashing).
28. **Range query trên cây có hàng triệu phần tử**: Kiểm tra không xảy ra `OutOfMemoryError` hay đứt liên kết dọc đường.
29. **Chèn khóa `null` hoặc Value `null`**: Ném `NullPointerException` với thông điệp rõ ràng theo chuẩn Design by Contract.
30. **Xóa tất cả các phần tử theo thứ tự ngẫu nhiên cho đến khi cây rỗng**: Gọi `validateInvariants()` sau mỗi bước xóa để đảm bảo cây luôn cân bằng tuyệt đối.

---

## 16. Optimization Techniques (Kỹ thuật Tối ưu Hóa Chuyên sâu)

### 16.1. Latch Crabbing / Lock Coupling (Đồng thời hóa B+ Tree)
Để cho phép hàng nghìn luồng đọc/ghi đồng thời trên B+ Tree mà không khóa toàn bộ cây (Table-level lock), các Database Engine sử dụng kỹ thuật **Latch Crabbing**:
- **Luồng Đọc (Reader)**:
  1. Giữ Read-Latch trên node cha.
  2. Lấy Read-Latch trên node con.
  3. Giải phóng Read-Latch trên node cha (như động tác leo thang - crabbing).
- **Luồng Ghi (Writer)**:
  1. Giữ Write-Latch trên node cha.
  2. Lấy Write-Latch trên node con.
  3. **Kiểm tra an toàn (Safety Check)**: Nếu node con "an toàn" (không bị split khi insert hoặc không bị underflow khi delete), lập tức giải phóng toàn bộ Write-Latch của các node tổ tiên phía trên!

```
Writer Descent (Insert):
[Lock Root (Write)] -> [Lock Child (Write)] -> Child is Safe? (keys < M-1)
       |
       v YES
[Unlock Root immediately!] -> Chỉ giữ Lock tại Child!
```

### 16.2. Prefix Key Truncation (Nén tiền tố trên Internal Nodes)
Trên các tầng Internal, các Routing Keys không nhất thiết phải lưu trữ toàn bộ chuỗi gốc. Chỉ cần một **tiền tố phân định ngắn nhất (Shortest Separator Prefix)**:
- Nhánh trái chứa: `"Thành phố Hà Nội"`
- Nhánh phải chứa: `"Thành phố Hồ Chí Minh"`
- Khóa phân định trên Internal Node chỉ cần lưu: `"Thành phố Hô"` thay vì toàn bộ chuỗi 24 ký tự! Kỹ thuật này giúp tăng gấp $3 - 5$ lần hệ số rẽ nhánh $M$.

---

## 17. Best Practices (Quy tắc Thực chiến)

1. **Khóa chính Auto-Increment vs. UUID ngẫu nhiên**:
   - Khi dùng khóa chính tự tăng (`BIGINT AUTO_INCREMENT`), các bản ghi mới luôn được chèn vào **trang cuối cùng bên phải (Rightmost Leaf Page)**. Thao tác split diễn ra tuần tự với hiệu suất lấp đầy $100\%$ và không gây phân mảnh.
   - Khi dùng Random UUIDv4 làm Primary Key, các phép chèn rơi ngẫu nhiên vào giữa các trang, gây ra hiện tượng **Page Split liên tục trên toàn bộ cây**, khiến hệ số lấp đầy tụt xuống $50\%$, phình to dung lượng đĩa gấp đôi và phá hủy hiệu năng bộ nhớ đệm Buffer Pool.
2. **Kích thước Trang (Page Size Tuning)**:
   - Hệ thống OLTP (truy vấn điểm nhanh): Dùng Page $4\text{ KB} - 16\text{ KB}$.
   - Hệ thống OLAP / Data Warehouse (quét dải lớn): Dùng Page $64\text{ KB} - 128\text{ KB}$.

---

## 18. Benchmark (JMH Simulation: BST vs. B+ Tree)

Dưới đây là mã đo lường hiệu năng bằng **JMH (Java Microbenchmark Harness)** so sánh giữa Cây nhị phân (`java.util.TreeMap`) và `BPlusTree` trên tác vụ Range Query ($100,000$ phần tử, quét dải $1,000$ phần tử):

```java
package com.structures.trees.benchmark;

import com.structures.trees.bplus.BPlusTree;
import org.openjdk.jmh.annotations.*;

import java.util.*;
import java.util.concurrent.TimeUnit;

@BenchmarkMode(Mode.Throughput)
@OutputTimeUnit(TimeUnit.MILLISECONDS)
@State(Scope.Thread)
@Warmup(iterations = 3, time = 1)
@Measurement(iterations = 5, time = 1)
@Fork(1)
public class BTreeBenchmark {

    private static final int TOTAL_KEYS = 100_000;
    private static final int SCAN_RANGE = 1_000;

    private BPlusTree<Integer, Integer> bPlusTree;
    private TreeMap<Integer, Integer> treeMap;
    private int startKey;

    @Setup(Level.Trial)
    public void setup() {
        bPlusTree = new BPlusTree<>(128); // Order 128
        treeMap = new TreeMap<>();

        Random rand = new Random(42);
        for (int i = 0; i < TOTAL_KEYS; i++) {
            int key = rand.nextInt(TOTAL_KEYS * 10);
            bPlusTree.insert(key, key);
            treeMap.put(key, key);
        }
        startKey = TOTAL_KEYS * 5;
    }

    @Benchmark
    public List<Integer> bPlusTreeRangeScan() {
        return bPlusTree.rangeSearch(startKey, startKey + SCAN_RANGE);
    }

    @Benchmark
    public List<Integer> treeMapRangeScan() {
        List<Integer> list = new ArrayList<>();
        var subMap = treeMap.subMap(startKey, true, startKey + SCAN_RANGE, true);
        for (var entry : subMap.entrySet()) {
            list.add(entry.getValue());
        }
        return list;
    }
}
```

### Kết quả Benchmark Điển hình (Throughput: ops/ms)
- `BPlusTree.rangeSearch (Order 128)`: **~ 4,850 ops/ms** (Nhờ duyệt phẳng trên danh sách liên kết Leaf, CPU Cache Prefetching tối ưu).
- `TreeMap.subMap (Red-Black Tree)`: **~ 1,220 ops/ms** (Bị chậm do liên tục nhảy con trỏ In-order giữa các node trên Heap).

---

## 19. Unit Testing (JUnit 5 Test Suite)

```java
package com.structures.trees.bplus;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;

import java.util.*;

import static org.junit.jupiter.api.Assertions.*;

class BPlusTreeTest {

    private BPlusTree<Integer, String> tree;

    @BeforeEach
    void setUp() {
        tree = new BPlusTree<>(4); // Order 4: Max 3 keys per node
    }

    @Test
    @DisplayName("Empty Tree operations should behave correctly")
    void testEmptyTree() {
        assertTrue(tree.isEmpty());
        assertEquals(0, tree.size());
        assertFalse(tree.search(10).isPresent());
        assertFalse(tree.delete(10));
        assertTrue(tree.rangeSearch(1, 100).isEmpty());
        assertDoesNotThrow(() -> tree.validateInvariants());
    }

    @Test
    @DisplayName("Sequential insertion and invariant validation")
    void testSequentialInsert() {
        int n = 500;
        for (int i = 1; i <= n; i++) {
            tree.insert(i, "VAL_" + i);
            assertEquals(i, tree.size());
        }
        tree.validateInvariants();

        for (int i = 1; i <= n; i++) {
            Optional<String> val = tree.search(i);
            assertTrue(val.isPresent());
            assertEquals("VAL_" + i, val.get());
        }
    }

    @Test
    @DisplayName("Range query exactness against Ground Truth")
    void testRangeQuery() {
        for (int i = 1; i <= 100; i += 2) {
            tree.insert(i, "V" + i);
        }
        tree.validateInvariants();

        List<String> results = tree.rangeSearch(20, 40);
        List<String> expected = List.of("V21", "V23", "V25", "V27", "V29", "V31", "V33", "V35", "V37", "V39");
        assertEquals(expected, results);
    }

    @Test
    @DisplayName("Borrow and Merge deletion mechanics")
    void testDeletionRebalance() {
        int[] keys = {10, 20, 30, 40, 50, 60, 70, 80, 90};
        for (int k : keys) tree.insert(k, "V" + k);

        // Delete causing borrow
        assertTrue(tree.delete(40));
        assertEquals(8, tree.size());
        assertFalse(tree.search(40).isPresent());
        tree.validateInvariants();

        // Delete causing cascading merge
        assertTrue(tree.delete(50));
        assertTrue(tree.delete(60));
        assertTrue(tree.delete(70));
        tree.validateInvariants();

        assertEquals(5, tree.size());
    }

    @ParameterizedTest
    @ValueSource(ints = {3, 4, 5, 8, 16, 64})
    @DisplayName("Fuzz testing with random insertions and deletions across various orders")
    void testRandomFuzzing(int order) {
        BPlusTree<Integer, Integer> fuzzTree = new BPlusTree<>(order);
        Map<Integer, Integer> groundTruth = new HashMap<>();
        Random rand = new Random(12345);

        for (int i = 0; i < 2000; i++) {
            int k = rand.nextInt(5000);
            fuzzTree.insert(k, k);
            groundTruth.put(k, k);
        }
        fuzzTree.validateInvariants();
        assertEquals(groundTruth.size(), fuzzTree.size());

        // Random Deletions
        List<Integer> keysToDelete = new ArrayList<>(groundTruth.keySet());
        Collections.shuffle(keysToDelete, rand);

        for (int i = 0; i < 1000; i++) {
            int k = keysToDelete.get(i);
            assertTrue(fuzzTree.delete(k));
            groundTruth.remove(k);
        }
        fuzzTree.validateInvariants();
        assertEquals(groundTruth.size(), fuzzTree.size());
    }
}
```

---

## 20. Interview Questions (20 Câu hỏi Phỏng vấn từ Standard tới Staff Level)

### Q1: B-Tree và B+ Tree khác nhau ở điểm cốt lõi nào?
**Trả lời**: B-Tree lưu trữ dữ liệu/payload ở cả Internal Nodes lẫn Leaf Nodes. B+ Tree chỉ lưu trữ dữ liệu tại Leaf Nodes, các Internal Nodes chỉ chứa Routing Keys. Đồng thời, các Leaf Nodes của B+ Tree được xâu chuỗi thành một danh sách liên kết đôi (Sequence Set) giúp Range Scan đạt $O(K)$ mà không cần duyệt cây.

### Q2: Tại sao các Database RDBMS chọn B+ Tree thay vì Hash Index?
**Trả lời**: Hash Index có chi phí tìm kiếm điểm lý tưởng $O(1)$, nhưng không thể thực hiện truy vấn dải (`WHERE age BETWEEN 20 AND 30`), không thể sắp xếp (`ORDER BY`), không thể tìm kiếm tiền tố (`LIKE 'abc%'`) và dễ bị thoái hóa khi hash collision. B+ Tree hỗ trợ xuất sắc tất cả các tác vụ trên với độ phức tạp ổn định $O(\log N)$.

### Q3: Chiều cao của B+ Tree chứa 1 tỷ dòng dữ liệu thường là bao nhiêu?
**Trả lời**: Với kích thước Page $16\text{ KB}$, một Internal Node có thể chứa $\approx 1,000$ con trỏ. Chiều cao cây $h \approx \log_{1000}(10^9) \approx 3$. Cây chỉ cao từ 3 đến 4 tầng.

### Q4: Page Split trong MySQL InnoDB diễn ra như thế nào và gây ảnh hưởng gì?
**Trả lời**: Khi chèn một dòng mới vào một Page đã đầy ($16\text{ KB}$), InnoDB cấp phát một Page mới, chuyển $50\%$ dữ liệu sang trang mới và chèn Routing Key lên trang cha. Việc này gây tốn chi phí I/O ghi đĩa, làm giảm hệ số lấp đầy xuống $50\%$ và phân mảnh chỉ mục.

### Q5: Tại sao sử dụng UUID v4 làm Primary Key trong MySQL InnoDB là một "Anti-pattern"?
**Trả lời**: UUID v4 có tính phân bố ngẫu nhiên hoàn toàn. Khi chèn vào Clustered B+ Tree, các bản ghi bị chèn rải rác vào các trang ngẫu nhiên, gây ra Page Split trên khắp cây, làm loãng Buffer Pool Cache và giảm thông lượng ghi từ hàng chục nghìn ops/s xuống vài trăm ops/s.

### Q6: Kỹ thuật Latch Crabbing giải quyết bài toán gì?
**Trả lời**: Giải quyết bài toán đồng thời (Concurrency Control). Nó cho phép nhiều luồng đọc/ghi cùng lúc trên cây bằng cách khóa tầng cha, khóa tầng con rồi lập tức nhả tầng cha nếu tầng con an toàn, tránh việc khóa toàn bộ cây.

### Q7: Tại sao B+ Tree lại thân thiện với CPU Cache hơn B-Tree?
**Trả lời**: Do Internal Nodes của B+ Tree không chứa Data Payload, toàn bộ Page chỉ chứa mảng các khóa liên tiếp, giúp tối đa hóa khả năng nạp vào CPU L1/L2/L3 Cache Lines ($64\text{ bytes}$) và tận dụng tính năng CPU Hardware Prefetcher.

### Q8: B* Tree là gì và cải tiến điểm gì so với B-Tree?
**Trả lời**: $B^*$-Tree yêu cầu các node không phải root phải đầy ít nhất $2/3$ (thay vì $1/2$). Khi một node bị tràn, nó sẽ thử chia sẻ khóa với node anh em liền kề trước; chỉ khi cả 2 anh em đều đầy nó mới tiến hành tách 2 node thành 3 node.

### Q9: Thuật toán Lehman-Yao B-link Tree trong PostgreSQL hoạt động như thế nào?
**Trả lời**: Nó thêm một con trỏ "High Key" và "Right-link Pointer" vào mọi Internal Node. Khi một trang bị split, luồng đọc vẫn có thể di chuyển sang trang bên phải thông qua Right-link mà không cần giữ khóa trên toàn bộ đường đi từ Root.

### Q10: Phân biệt Clustered Index và Non-Clustered (Secondary) Index?
**Trả lời**: Clustered Index lưu trữ chính dòng dữ liệu thực tế tại các Leaf Pages (mỗi bảng chỉ có 1 Clustered Index). Secondary Index chỉ lưu các cột được index kèm theo con trỏ trỏ về Clustered Key tương ứng.

### Q11: Thế nào là Covering Index?
**Trả lời**: Là chỉ mục chứa đầy đủ tất cả các trường dữ liệu mà câu truy vấn `SELECT` yêu cầu. Khi đó RDBMS chỉ cần đọc dữ liệu từ Leaf Page của Index mà không cần tốn thêm I/O tra cứu ngược vào Clustered Table (tránh thao tác "Bookmark Lookup").

### Q12: Điều kiện tối thiểu của bậc $M$ trong B-Tree là gì? Tại sao $M \ge 3$?
**Trả lời**: Nếu $M = 2$, số khóa tối đa là $M - 1 = 1$, số con tối thiểu là $\lceil 2/2 \rceil = 1$. Cấu trúc sẽ thoái hóa thành cây nhị phân không thể cân bằng đa phân. Vì vậy $M$ tối thiểu phải bằng 3 (tương đương cây 2-3 Tree).

### Q13: Phân tích sự khác biệt giữa B+ Tree và LSM-Tree trong các hệ thống Write-Heavy?
**Trả lời**: B+ Tree thực hiện In-place update (ghi đè trang ngẫu nhiên), gây ra Write Amplification cao. LSM-Tree chuyển toàn bộ thao tác ghi thành tuần tự (Append-only vào MemTable và WAL) rồi nén nền (Compaction), cho tốc độ ghi vượt trội gấp hàng chục lần.

### Q14: Làm thế nào để Bulk-Load một tập dữ liệu lớn vào B+ Tree tối ưu nhất?
**Trả lời**: Không chèn từng phần tử từ trên xuống (Top-down $O(N \log N)$). Thay vào đó, sắp xếp trước toàn bộ dữ liệu ($O(N \log N)$ Sort), sau đó xây dựng cây từ dưới lên (Bottom-up Construction $O(N)$), đóng gói các Leaf Pages với hệ số lấp đầy $100\%$ và chỉ tạo Internal Nodes khi tầng dưới đầy.

### Q15: MemorySegment trong Java 21 giúp ích gì cho việc xây dựng In-Memory B+ Tree?
**Trả lời**: Cho phép cấp phát và truy cập các vùng nhớ Off-Heap có cấu trúc phẳng, canh chỉnh theo Cache Line, tránh hoàn toàn chi phí Object Header overhead của JVM và giải phóng Garbage Collector khỏi việc quét hàng triệu node.

### Q16: Hiện tượng Index Fragmentation là gì và cách xử lý?
**Trả lời**: Xảy ra sau nhiều thao tác chèn/xóa ngẫu nhiên làm các trang B+ Tree chỉ đầy $50-60\%$ hoặc các trang logic liền kề không nằm liên tiếp trên đĩa vật lý. Xử lý bằng cách chạy `OPTIMIZE TABLE` (MySQL) hoặc `VACUUM FULL / REINDEX` (Postgres) để dựng lại cây liên tục.

### Q17: Tại sao phép xóa trong B+ Tree hiếm khi thực hiện Merge ngay trong thực tế?
**Trả lời**: Trong các RDBMS sản xuất, chi phí gộp trang và lock đồng thời rất tốn kém. Người ta thường chỉ đánh dấu xóa logic (Tombsone/Soft-delete) hoặc chấp nhận để trang underflow với hy vọng các phép chèn tương lai sẽ lấp đầy lại.

### Q18: B+ Tree xử lý Null values như thế nào trong Index?
**Trả lời**: Tùy RDBMS: MySQL coi `NULL` là giá trị nhỏ nhất và gom về đầu mút bên trái nhất của Leaf 0. Oracle không lưu các dòng mà toàn bộ index key là `NULL` vào B-Tree (trừ Bitmap index).

### Q19: Vectorized / SIMD Search trong B+ Tree Node là gì?
**Trả lời**: Thay vì dùng Binary Search tuần tự để tìm key trong mảng 64 keys của một Node, ta sử dụng tập lệnh SIMD (như AVX-512) để so sánh song song 8 hoặc 16 keys cùng 1 chu kỳ CPU.

### Q20: Giới hạn vật lý nào ngăn cản việc tăng bậc $M$ lên vô hạn (ví dụ $M = 1,000,000$)?
**Trả lời**: Nếu $M$ quá lớn, kích thước của một Node sẽ vượt xa kích thước một Disk Page ($16\text{ KB}$) và CPU L1 Cache ($32\text{ KB}$). Khi đó chi phí đọc một node từ đĩa và chi phí tìm kiếm bên trong node sẽ vượt quá lợi ích của việc giảm chiều cao cây.

---

## 21. Practice Problems Link

Để rèn luyện và làm chủ hoàn toàn các kỹ năng cài đặt và biến thể thuật toán B-Tree/B+ Tree, vui lòng truy cập tệp bài tập thực hành chuyên sâu:
👉 **[05-B-Tree-and-B-Plus-Tree-Problems.md](05-B-Tree-and-B-Plus-Tree-Problems.md)** *(Gồm 30 bài toán kinh điển từ cơ bản đến Staff Engineer Level kèm lời giải Java 21 chi tiết)*.

---

## 22. Pattern Recognition (Nhận diện Dạng bài & Ứng dụng)

| Khi gặp bài toán có đặc điểm... | Cấu trúc dữ liệu tối ưu cần chọn |
| :--- | :--- |
| Dữ liệu nằm trên Đĩa / File / SSD, kích thước vượt RAM | **B+ Tree** (Branching Factor lớn) |
| Cần thực hiện liên tục các truy vấn Range Scan (`BETWEEN a AND b`) | **B+ Tree** (Nhờ Sequence Set Linked List) |
| Dữ liệu thuần RAM, cần tìm kiếm/chèn/xóa $O(\log N)$ đơn giản | **Red-Black Tree** (`TreeMap`) hoặc **SkipList** |
| Chỉ cần tìm kiếm điểm $O(1)$, không cần thứ tự, không Range Scan | **Hash Table** (`HashMap`) |
| Ứng dụng ghi cực lớn (Write-Heavy: Logging, Metrics, Time-series) | **LSM-Tree** (RocksDB, Cassandra) |
| Cần lập chỉ mục không gian nhiều chiều (Geospatial / GIS) | **R-Tree** hoặc **B+ Tree kết hợp Geohash/Z-Order Curve** |

---

## 23. Real-World Case Study: Sự cố Phân mảnh Chỉ mục trên Hệ thống Thanh toán

### Bối cảnh sự cố
Một hệ thống cổng thanh toán tài chính xử lý 10,000 giao dịch/giây sử dụng MySQL InnoDB. Đội ngũ phát triển thiết kế bảng `transactions` với Khóa chính là chuỗi `transaction_id` sinh ngẫu nhiên bằng `UUID.randomUUID().toString()`.

### Hiện tượng
- Trong 2 tháng đầu (khi dữ liệu < 10 triệu dòng), hệ thống phản hồi cực nhanh (< 5ms).
- Khi lượng dữ liệu đạt 100 triệu dòng, độ trễ ghi bất ngờ tăng vọt lên **800ms - 2000ms**, CPU Disk I/O Wait (iowait) luôn ở mức $95\%$, và dung lượng bảng phình to lên gấp đôi so với tính toán lý thuyết.

```
Dung lượng B+ Tree:
Lý thuyết (100% fill):  100,000,000 rows * 500 bytes = ~ 50 GB
Thực tế trên đĩa:        ~ 112 GB (Hệ số lấp đầy chỉ đạt ~ 44%)
```

### Nguyên nhân cốt lõi (Root Cause)
1. Do UUID v4 có tính ngẫu nhiên, mỗi giao dịch mới được chèn vào một vị trí ngẫu nhiên trong Clustered B+ Tree.
2. Trang đích gần như không nằm trong RAM Buffer Pool $\implies$ Đòi hỏi 1 Random Read I/O từ đĩa để nạp trang vào RAM.
3. Trang bị đầy $\implies$ Xảy ra **Page Split**, chia đôi thành 2 trang đầy $50\%$.
4. Ghi 2 trang bị split xuống đĩa $\implies$ Phát sinh thêm 2 Random Write I/O.
5. Hiệu ứng dây chuyền làm sụp đổ hoàn toàn bộ nhớ đệm Buffer Pool Cache (Thrashing).

### Giải pháp khắc phục
1. Thay thế UUID v4 bằng **Time-Ordered Sequential ID** (ví dụ **ULID** hoặc **TSID** hoặc `BIGINT AUTO_INCREMENT`). Khóa chính mới có 48-bit timestamp ở đầu, đảm bảo các bản ghi mới luôn chèn vào cuối cây (Append-only).
2. Chạy lệnh tái cấu trúc chỉ mục: `ALTER TABLE transactions ENGINE=InnoDB;` để giải phóng các trang phân mảnh.
3. **Kết quả**: Thông lượng ghi tăng vọt lên **18,000 ops/s**, Disk I/O Wait giảm về dưới $5\%$, dung lượng lưu trữ giảm từ $112\text{ GB}$ xuống còn $54\text{ GB}$.

---

## 24. Summary & Comprehensive Checklist

### Bảng Tóm tắt Công thức và Đặc tính

```
+-----------------------------------------------------------------------------------+
|                           B+ TREE CHEAT SHEET                                     |
+-----------------------------------------------------------------------------------+
| Bậc của cây (Order)              | M (Số con tối đa của 1 Node)                   |
| Số con tối thiểu (Non-root)      | ceil(M / 2)                                    |
| Số keys tối đa (Mọi node)        | M - 1                                          |
| Số keys tối thiểu (Internal)     | ceil(M / 2) - 1                                |
| Số keys tối thiểu (Leaf)         | floor(M / 2)                                   |
| Chiều cao tối đa (Height)        | h <= log_{ceil(M/2)} ((N + 1) / 2)             |
| Time Complexity Search / Insert  | O(log_M N) Disk I/O  |  O(log_2 N) CPU Time    |
| Time Complexity Range Scan (K)   | O(log_M N + K/B) Disk I/O                      |
+-----------------------------------------------------------------------------------+
```

### Checklist Kiểm tra Kiến thức & Mã nguồn
- [x] Đã hiểu lý do vật lý tại sao B-Tree/B+ Tree vượt trội trên Disk I/O so với BST/AVL/Red-Black Tree.
- [x] Nắm vững các công thức toán học về số lượng con/khóa tối thiểu và tối đa của B-Tree bậc $M$.
- [x] Phân biệt rõ sự khác nhau giữa B-Tree và B+ Tree (vị trí payload và Sequence Set list).
- [x] Cài đặt thành công mã nguồn Generic B+ Tree bằng Java 21 với đầy đủ `insert`, `delete`, `rangeSearch`.
- [x] Nắm vững cơ chế Split (sao chép key ở Leaf vs đẩy median key ở Internal).
- [x] Nắm vững cơ chế Rebalance khi Underflow (Borrow từ anh em $\to$ Merge nếu không mượn được).
- [x] Hiểu rõ sự cố phân mảnh do UUID Random Keys trên MySQL InnoDB Clustered Index.
- [x] Hiểu cơ chế Concurrency Control qua Latch Crabbing và kỹ thuật nén Prefix Truncation.
