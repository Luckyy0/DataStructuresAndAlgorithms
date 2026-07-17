# Metadata
- **Document ID**: DSA-04-04
- **Version**: 1.0
- **Prerequisites**: DSA-04-01 (Array Basics), DSA-02-02 (Time Complexity)
- **Learning Objectives**: Hiểu và cài đặt Prefix Sum (Mảng cộng dồn) 1D và 2D. Ứng dụng kỹ thuật này để chuyển bài toán truy vấn tổng đoạn (Range Query) từ $\mathcal{O}(N)$ xuống $\mathcal{O}(1)$. Kết hợp với HashMap và Modular Arithmetic để giải quyết các bài toán Subarray phức tạp.
- **Estimated Reading Time**: 55 phút
- **Difficulty**: Intermediate
- **Keywords**: Prefix Sum, Range Query, Subarray, HashMap, Modular Arithmetic, 2D Prefix Sum

---

# 1 Purpose
Prefix Sum (Mảng cộng dồn) là một trong những Pattern quan trọng nhất khi làm việc với Array. Mục đích của nó là **tiền xử lý (Pre-processing)** một mảng ban đầu thành mảng cộng dồn, sao cho mọi truy vấn tính tổng của một đoạn con (Subarray) diễn ra trong thời gian hằng số $\mathcal{O}(1)$ thay vì $\mathcal{O}(N)$. Đây là chìa khóa để tối ưu thuật toán từ $\mathcal{O}(N^2)$ xuống $\mathcal{O}(N)$.

---

# 2 Motivation
Giả sử bạn có dữ liệu doanh thu của một cửa hàng trong 365 ngày. Giám đốc yêu cầu bạn trả lời 10,000 truy vấn: "Tổng doanh thu từ ngày L đến ngày R là bao nhiêu?".
- **Cách naïve**: Mỗi truy vấn dùng vòng lặp `for` từ L đến R để cộng dồn. Tốn $\mathcal{O}(N)$ mỗi truy vấn. 10,000 truy vấn tốn $\mathcal{O}(Q \times N) \approx 3.6 \times 10^6$ phép toán.
- **Prefix Sum**: Tính toán trước tổng từ ngày 1 đến mỗi ngày. Trả lời mỗi truy vấn chỉ bằng 1 phép TRỪ trong $\mathcal{O}(1)$. Tổng thời gian giảm xuống còn $\mathcal{O}(N + Q) \approx 10,000$ phép toán. Nhanh gấp hàng trăm lần!

---

# 3 Mathematical Foundation
## 3.1 1D Prefix Sum
Cho mảng $A = [a_0, a_1, \dots, a_{n-1}]$.
Định nghĩa mảng Prefix Sum $P$ kích thước $n+1$, với $P[0] = 0$ và:
$$P[i] = P[i-1] + a_{i-1} = \sum_{k=0}^{i-1} a_k \quad \text{cho } 1 \le i \le n$$

Khi đó, tổng của đoạn từ chỉ số $L$ đến $R$ (inclusive) là:
$$\text{Sum}(L, R) = \sum_{k=L}^{R} a_k = P[R+1] - P[L]$$

## 3.2 2D Prefix Sum
Cho ma trận $A$ kích thước $M \times N$.
Mảng Prefix Sum 2D $P$ kích thước $(M+1) \times (N+1)$ được định nghĩa là tổng các phần tử trong hình chữ nhật từ $(0,0)$ đến $(i-1, j-1)$:
$$P[i][j] = A[i-1][j-1] + P[i-1][j] + P[i][j-1] - P[i-1][j-1]$$

Tổng của hình chữ nhật có đỉnh trên-trái $(r_1, c_1)$ và dưới-phải $(r_2, c_2)$ là:
$$\text{Sum}(r_1, c_1, r_2, c_2) = P[r_2+1][c_2+1] - P[r_1][c_2+1] - P[r_2+1][c_1] + P[r_1][c_1]$$
(Đây chính là nguyên lý **Inclusion-Exclusion Principle** - Nguyên lý Bao hàm Loại trừ).

---

# 4 Core Theory
## 4.1 Trade-off (Đánh đổi)
Prefix Sum là bài toán kinh điển của việc đánh đổi **Space** (Không gian bộ nhớ) để lấy **Time** (Tốc độ thời gian).
- Tốn thêm $\mathcal{O}(N)$ Space để lưu mảng $P$.
- Tốn $\mathcal{O}(N)$ Time để tiền xử lý.
- Đổi lại: Truy vấn $\mathcal{O}(1)$. Cực kỳ hiệu quả nếu số lượng truy vấn $Q$ lớn.

## 4.2 Các biến thể của Prefix
- **Prefix Product (Tích)**: Tính tích liên tiếp (Cẩn thận số 0).
- **Prefix XOR**: $P[i] = P[i-1] \oplus a_{i-1}$. Query: $XOR(L, R) = P[R+1] \oplus P[L]$.
- **Prefix GCD/LCM**: Query cần dùng Sparse Table, không thể dùng phép trừ đơn thuần.

## 4.3 Kết hợp HashMap
Bài toán: "Đếm số mảng con có tổng bằng $K$".
Ta có: $\text{Sum}(i, j) = P[j] - P[i-1] = K$.
$\Rightarrow P[i-1] = P[j] - K$.
Trong khi duyệt mảng, ta tính $P[j]$. Dùng HashMap để lưu tần suất xuất hiện của các giá trị Prefix Sum trước đó. Tại mỗi bước $j$, ta chỉ cần kiểm tra xem $P[j] - K$ đã xuất hiện bao nhiêu lần trong HashMap!

---

# 5 Visual Explanation

```mermaid
graph TD
    subgraph Original Array A
        A0[3] --> A1[1] --> A2[4] --> A3[2] --> A4[5]
    end
    
    subgraph Prefix Sum Array P
        P0[0] -.-> |+ 3| P1[3]
        P1 -.-> |+ 1| P2[4]
        P2 -.-> |+ 4| P3[8]
        P3 -.-> |+ 2| P4[10]
        P4 -.-> |+ 5| P5[15]
    end
    
    subgraph Query Sum L=1, R=3
        Q1["Sum(1, 3) = A[1]+A[2]+A[3] = 1+4+2 = 7"]
        Q2["Sum(1, 3) = P[4] - P[1] = 10 - 3 = 7"]
        Q1 <--> Q2
    end
```

---

# 6 Java Implementation
Cài đặt 1D và 2D Prefix Sum căn bản:

```java
public class PrefixSumDemo {

    // ===== 1D Prefix Sum =====
    static class NumArray {
        private int[] prefix;

        public NumArray(int[] nums) {
            // Khởi tạo mảng n+1 để dễ xử lý L=0
            prefix = new int[nums.length + 1];
            for (int i = 0; i < nums.length; i++) {
                prefix[i + 1] = prefix[i] + nums[i];
            }
        }

        public int sumRange(int left, int right) {
            return prefix[right + 1] - prefix[left];
        }
    }

    // ===== 2D Prefix Sum =====
    static class NumMatrix {
        private int[][] prefix;

        public NumMatrix(int[][] matrix) {
            if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return;
            int m = matrix.length;
            int n = matrix[0].length;
            prefix = new int[m + 1][n + 1];

            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    prefix[i + 1][j + 1] = matrix[i][j] 
                                         + prefix[i][j + 1] 
                                         + prefix[i + 1][j] 
                                         - prefix[i][j];
                }
            }
        }

        public int sumRegion(int row1, int col1, int row2, int col2) {
            return prefix[row2 + 1][col2 + 1] 
                 - prefix[row1][col2 + 1] 
                 - prefix[row2 + 1][col1] 
                 + prefix[row1][col1];
        }
    }

    public static void main(String[] args) {
        int[] arr = {3, 1, 4, 2, 5};
        NumArray na = new NumArray(arr);
        System.out.println("Sum(1,3): " + na.sumRange(1, 3)); // 7
    }
}
```

---

# 7 Step-by-Step Execution
**Bài toán: Số lượng mảng con có tổng bằng K (Subarray Sum Equals K)**
Input: `nums = [1, -1, 1, 1, 1, 1]`, `K = 3`

1. Khởi tạo `HashMap(PrefixSum -> Count)`. Đưa vào `(0, 1)` (Base case: tổng = 0 xuất hiện 1 lần trước khi duyệt).
2. `i = 0, num = 1`: `sum = 1`. `sum - K = -2` (Không có trong map). Đưa `(1, 1)` vào map.
3. `i = 1, num = -1`: `sum = 0`. `sum - K = -3` (Không có). Tăng đếm của `0`: `(0, 2)`.
4. `i = 2, num = 1`: `sum = 1`. `sum - K = -2` (Không có). Tăng đếm của `1`: `(1, 2)`.
5. `i = 3, num = 1`: `sum = 2`. `sum - K = -1` (Không có). Đưa `(2, 1)`.
6. `i = 4, num = 1`: `sum = 3`. `sum - K = 0`. Có trong map! `Count(0) = 2` $\rightarrow$ Tăng kết quả thêm 2 (Có 2 mảng con tổng 3 kết thúc tại đây). Đưa `(3, 1)`.
7. `i = 5, num = 1`: `sum = 4`. `sum - K = 1`. Có trong map! `Count(1) = 2` $\rightarrow$ Tăng kết quả thêm 2. Đưa `(4, 1)`.
8. Kết quả cuối cùng = 4.

---

# 8 Complexity Analysis
| Thao tác | Time | Space | Note |
|---|---|---|---|
| Build 1D Prefix | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ | Mảng phụ $N+1$ |
| Query 1D | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | Phép trừ đơn giản |
| Build 2D Prefix | $\mathcal{O}(MN)$ | $\mathcal{O}(MN)$ | Mảng phụ $(M+1) \times (N+1)$ |
| Query 2D | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | 3 phép cộng/trừ |
| HashMap + Prefix | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ | Tối ưu $\mathcal{O}(N^2)$ xuống $\mathcal{O}(N)$ |

---

# 9 JVM Analysis
## Integer Overflow Risk
Prefix Sum cộng dồn liên tục. Nếu mảng có $10^5$ phần tử, mỗi phần tử tối đa $10^5$, tổng sẽ là $10^{10}$. Kiểu `int` trong Java chỉ chứa được tối đa $\approx 2 \times 10^9$ (`Integer.MAX_VALUE`).
$\rightarrow$ Lỗi Overflow xảy ra ngầm (silent overflow) biến số dương thành âm.
**Giải pháp**: Luôn dùng `long[]` cho mảng Prefix Sum nếu có nguy cơ vượt qua $2 \times 10^9$.

## HashMap Boxing Overhead
Khi kết hợp Prefix Sum với HashMap: `HashMap<Long, Integer> map = new HashMap<>()`.
Tại mỗi bước vòng lặp, ta gọi `map.put()` và `map.getOrDefault()`.
Auto-boxing chuyển primitive `long` thành `Long` object trên Heap, tạo ra **Memory Overhead và GC Pressure** khổng lồ (Xem lại DSA-03-02, DSA-03-03).
Nếu dữ liệu cực lớn, ta nên cân nhắc mảng thuần túy nếu phạm vi giá trị hẹp, hoặc Primitive HashMap library (như Eclipse Collections `LongIntHashMap`).

---

# 10 OpenJDK Analysis
## Hashing primitives in OpenJDK
Khi bạn dùng `Long.hashCode()`, thuật toán nội bộ cực kỳ đơn giản:
```java
public static int hashCode(long value) {
    return (int)(value ^ (value >>> 32));
}
```
Tuy nhiên, cấu trúc dữ liệu HashMap của Java có cơ chế `hash(Object key)` để trộn (mix) hash code thêm 1 bước nữa, giúp giảm bucket collisions. Nhưng khi sử dụng `HashMap<Long, Integer>`, chi phí chủ yếu không nằm ở hash mà nằm ở việc Memory Allocation (cấp phát Heap).

---

# 11 Production Usage
**Ứng dụng của Prefix Sum:**
1. **Phân tích Log/Traffic**: "Tổng số request trả về 500 error từ 08:00 đến 09:30?". Chia thời gian thành bucket (giây/phút), build prefix sum.
2. **Computer Vision / Xử lý ảnh (Image Processing)**: Integral Image. Đây chính là 2D Prefix Sum. Nó được dùng trong thuật toán Viola-Jones để nhận diện khuôn mặt cực nhanh (tính tổng pixels trong vùng chữ nhật O(1)).
3. **Heatmap & Analytics**: Grid base analytics (e.g., Tính tổng dân số trong một vùng tọa độ vuông góc).

---

# 12 Design Decisions
**Nên dùng mảng kích thước $N$ hay $N+1$?**
- Mảng cỡ $N$: `prefix[i] = prefix[i-1] + arr[i]`. Truy vấn `L` đến `R` là `prefix[R] - prefix[L-1]`.
  $\rightarrow$ Nếu $L=0$, ta phải `if (L == 0)` để tránh `ArrayIndexOutOfBoundsException`.
- Mảng cỡ $N+1$: `prefix[i] = prefix[i-1] + arr[i-1]`. Truy vấn `L` đến `R` là `prefix[R+1] - prefix[L]`.
  $\rightarrow$ **Quyết định**: LUÔN DÙNG $N+1$. Đổi 4 bytes bộ nhớ lấy sự đơn giản của Code, tránh if/else lặp đi lặp lại.

---

# 13 Common Bugs
20 lỗi thường gặp:
1. Integer Overflow: Không dùng `long` khi cộng dồn mảng lớn.
2. `ArrayIndexOutOfBoundsException`: Quên xử lý trường hợp truy vấn từ Index $L=0$. (Nên dùng mảng N+1).
3. 2D Prefix Sum nhầm công thức: Quên $+ P[i-1][j-1]$ ở cuối.
4. Trừ nhầm biên (Off-by-one): Truy vấn $L$ đến $R$, viết nhầm `prefix[R] - prefix[L]`. (Phải là `R+1`).
5. Trong Subarray Sum = K, quên add `(0, 1)` vào HashMap lúc ban đầu.
6. Subarray Divisible by K: Số dư bị âm. Trong Java, `-5 % 3 = -2`. Phải xử lý: `(sum % K + K) % K`.
7. Subarray Sum với số lượng dương/âm: Dùng Two Pointers/Sliding Window là SAI (Sliding Window chỉ đúng nếu tất cả dương). BẮT BUỘC dùng HashMap + Prefix Sum.
8. Khởi tạo mảng 2D kích thước $M \times N$ thay vì $(M+1) \times (N+1)$.
9. Không kiểm tra trường hợp mảng `arr` rỗng `arr.length == 0` trước khi truy cập.
10. Sửa đổi (Update) mảng ban đầu sau khi đã Build Prefix Sum: Prefix Sum không hỗ trợ Update in $\mathcal{O}(1)$. (Phải dùng Fenwick Tree / Segment Tree).
11. Tính Prefix XOR quên tính chất $X \oplus X = 0$.
12. Auto-boxing overhead khiến thuật toán Timeout (TLE) trên LeetCode/HackerRank (Cần tối ưu sang primitive array nếu được).
13. HashMap collision do hash phân tán kém, làm giảm performance từ $\mathcal{O}(N)$ xuống $\mathcal{O}(N^2)$.
14. Nhầm lẫn giữa biến vòng lặp `i, j` với tọa độ `r, c` trong 2D Matrix.
15. Quên cộng dồn (prefix) trên chính hàng hoặc cột đầu tiên (Nếu dùng mảng N).
16. Dùng Subarray nhưng đọc nhầm đề thành Subsequence (Prefix Sum không áp dụng cho Subsequence không liên tiếp).
17. Khi dùng mảng N+1, Index của mảng gốc bị lệch 1 so với Prefix array, dễ gây nhầm lẫn truy xuất.
18. Không reset giá trị HashMap/Prefix mảng nếu chạy nhiều Testcases.
19. Mảng Prefix Product bị 0 chia 0 (Zero division): Cần xử lý riêng vị trí số 0 (Thường là đếm số lượng số 0).
20. Xài Prefix Sum cho Range Update (Sai mục đích, phải xài Difference Array).

---

# 14 Edge Cases
- $L = R$: Truy vấn chỉ 1 phần tử.
- $L = 0$: Truy vấn từ đầu mảng.
- Mảng có chứa toàn số 0.
- Mảng chứa số âm.
- K = 0 (Trong bài đếm số lượng mảng con tổng K).

---

# 15 Optimization Techniques
- **In-place Prefix Sum**: Nếu mảng ban đầu không cần giữ lại, tính thẳng `arr[i] += arr[i-1]`. Tiết kiệm $\mathcal{O}(N)$ Space.
- **Biến cộng dồn (Running Sum)**: Nếu chỉ duyệt 1 pass từ trái qua phải kết hợp HashMap, KHÔNG cần lưu mảng Prefix Sum. Chỉ cần biến `currentSum`. Tiết kiệm $\mathcal{O}(N)$ mảng.
- **Prefix Sum cho boolean/binary**: Bài toán "Số lượng 0 bằng số lượng 1", chuyển 0 thành -1, chuyển bài về "Tìm mảng con có tổng = 0".

---

# 16 Best Practices
- Ghi nhớ kĩ mô típ: **"Subarray condition" $\rightarrow$ Nghĩ tới Prefix Sum + HashMap đầu tiên.**
- Luôn khởi tạo `map.put(0L, 1)` khi giải bài Subarray Sum bằng HashMap để cover trường hợp mảng con bắt đầu từ index 0.
- Handle Modulo khéo léo với Java: `int mod = (sum % K + K) % K;`.

---

# 19 Interview Questions
20 câu hỏi:
**Easy**
1. Định nghĩa mảng Prefix Sum.
2. Công thức tính tổng từ L đến R bằng Prefix Sum.
3. Độ phức tạp thời gian/không gian của Prefix Sum?
4. Ưu/nhược điểm so với tính tổng bằng vòng lặp?
5. Array size nên là N hay N+1?

**Medium**
6. Giải bài "Subarray Sum Equals K" tối ưu $\mathcal{O}(N)$.
7. Tại sao lại dùng HashMap trong bài trên?
8. Tại sao phải put (0, 1) vào map lúc bắt đầu?
9. Công thức 2D Prefix Sum là gì? (Chứng minh).
10. Tại sao phải xử lý phép chia lấy dư âm trong Java? Code như thế nào?
11. Sliding Window có giải được "Subarray Sum = K" nếu mảng có số âm không? Tại sao?
12. Prefix XOR hoạt động ra sao? Tính chất toán học?

**Hard & Senior**
13. So sánh Prefix Sum vs Segment Tree / Fenwick Tree. Khi nào chọn cái nào?
14. Thiết kế hệ thống Integral Image cho Computer Vision.
15. 3D Prefix Sum công thức như thế nào?
16. Nếu mảng bị Update liên tục thì xử lý thế nào?
17. Tối ưu Boxing/Unboxing cho HashMap của Java trong môi trường Low-Latency?

---

# 20 Practice Problems Link
Xem toàn bộ 30 bài toán tại: [04-Prefix-Sum-Pattern-Problems.md](04-Prefix-Sum-Pattern-Problems.md).

---

# 23 Summary
Prefix Sum là vũ khí tối thượng cho mọi bài toán dạng Range Query (Chỉ đọc) trên mảng. Bằng cách hi sinh $\mathcal{O}(N)$ không gian và thời gian tiền xử lý, ta giảm độ phức tạp truy vấn về $\mathcal{O}(1)$. Khi kết hợp thêm HashMap, nó trở thành công cụ $\mathcal{O}(N)$ để đếm và tìm mảng con có tổng đặc biệt, thay thế hoàn toàn $\mathcal{O}(N^2)$ của thuật toán vét cạn.

---

# 24 Checklist
- [ ] Implement thuần thục mảng Prefix N+1.
- [ ] Xây dựng và truy vấn 2D Prefix Sum.
- [ ] Nhớ mô típ HashMap + Prefix Sum (`currentSum - K`).
- [ ] Tránh bẫy Integer Overflow bằng `long`.
- [ ] Xử lý đúng toán tử `%` với số âm trong Java.
