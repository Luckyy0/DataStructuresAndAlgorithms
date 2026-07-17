# Metadata
- **Document ID**: DSA-04-05
- **Version**: 1.0
- **Prerequisites**: DSA-04-04 (Prefix Sum Pattern)
- **Learning Objectives**: Nắm vững kỹ thuật Difference Array để tối ưu hóa thao tác Cập nhật theo khoảng (Range Update) từ $\mathcal{O}(N)$ xuống $\mathcal{O}(1)$. Hiểu cách kết hợp Mảng Chênh Lệch và Mảng Cộng Dồn để khôi phục mảng ban đầu.
- **Estimated Reading Time**: 45 phút
- **Difficulty**: Intermediate
- **Keywords**: Difference Array, Range Update, Range Addition, Sweep Line

---

# 1 Purpose
Nếu **Prefix Sum** là kỹ thuật sinh ra để tối ưu Truy vấn đọc theo khoảng (Range Query Sum), thì **Difference Array (Mảng chênh lệch)** sinh ra để tối ưu Cập nhật cộng/trừ theo khoảng (Range Update). Nó cho phép bạn tăng hoặc giảm tất cả các phần tử từ chỉ số $L$ đến $R$ lên một giá trị $V$ chỉ với $\mathcal{O}(1)$ thời gian, bất kể khoảng $L \dots R$ dài bao nhiêu.

---

# 2 Motivation
**Bài toán Thực tế:** Bạn vận hành một hệ thống đặt vé máy bay nội địa. Một chuyến bay có 100 ghế và dừng qua 10,000 trạm.
Có 5,000 yêu cầu đặt vé dạng: "Tôi muốn đặt 3 ghế từ trạm L đến trạm R".
- **Cách naïve**: Mỗi booking, lặp `for i = L to R` và trừ đi 3 ghế trống. Mất $\mathcal{O}(N)$ thời gian cho mỗi booking. Tổng 5,000 bookings mất $\mathcal{O}(Q \times N)$ phép tính $\rightarrow$ Server quá tải.
- **Dùng Difference Array**: Bạn chỉ ghi chú tại trạm L: "Bắt đầu lấy 3 ghế". Tại trạm R+1: "Trả lại 3 ghế". 1 booking tốn 2 phép toán $\mathcal{O}(1)$. Xử lý 5,000 bookings mất $\mathcal{O}(Q)$ thời gian. Sau cùng, quét lại 1 lần để biết chính xác số ghế trống ở từng trạm. Cực kỳ tốc độ!

---

# 3 Mathematical Foundation
Cho mảng ban đầu $A = [a_0, a_1, \dots, a_{n-1}]$.
Định nghĩa mảng chênh lệch $D$ cùng kích thước, trong đó:
$$D[0] = a_0$$
$$D[i] = a_i - a_{i-1} \quad \text{cho } 1 \le i \le n-1$$

**Tính chất Khôi phục (Reconstruction):**
Nếu ta tính Prefix Sum của mảng $D$, ta sẽ thu lại được mảng $A$:
$$\text{PrefixSum}(D, i) = D[0] + D[1] + \dots + D[i]$$
$$= a_0 + (a_1 - a_0) + (a_2 - a_1) + \dots + (a_i - a_{i-1}) = a_i$$

**Thao tác Range Update:**
Để cộng thêm giá trị $V$ vào mảng $A$ từ chỉ số $L$ đến $R$:
- Ta chỉ cộng $V$ vào $D[L]$: `D[L] += V` (Nghĩa là từ $L$ trở đi, mọi phần tử sau khi khôi phục sẽ mang thêm $+V$).
- Ta trừ $V$ tại $D[R+1]$: `D[R+1] -= V` (Nghĩa là triệt tiêu hiệu ứng $+V$ từ chỉ số $R+1$ trở đi).
Các vị trí khác trong mảng $D$ giữ nguyên!

---

# 4 Core Theory
## 4.1 Trade-off giữa Truy vấn và Cập nhật
| Kỹ thuật | Range Query (Sum) | Range Update | Ghi chú |
|---|---|---|---|
| Mảng thường | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ | Dễ cài đặt nhất |
| Prefix Sum | $\mathcal{O}(1)$ | $\mathcal{O}(N)$ | Phải build lại toàn bộ mảng Prefix sau khi Update |
| Difference Array| $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | Phải khôi phục bằng Prefix Sum trước khi Query |
| Segment/Fenwick| $\mathcal{O}(\log N)$| $\mathcal{O}(\log N)$| Phức tạp, hỗ trợ cả Query và Update đan xen |

Quy tắc chọn:
- Nếu chỉ có Update liên tục, sau đó Query MỘT LẦN ở cuối $\rightarrow$ **Difference Array**.
- Nếu Query và Update đan xen nhau (Update, Query, Update, Query) $\rightarrow$ **Segment Tree / Fenwick Tree**.

## 4.2 Sweep Line Algorithm (Biến thể)
Bài toán Đặt phòng, Meeting Rooms, Lịch trình:
Thay vì mảng cố định, ta có các Events (Bắt đầu lúc S, Kết thúc lúc E).
Cập nhật: Tại thời điểm S: `timeline[S] += 1`. Tại E: `timeline[E] -= 1`.
Sau đó quét tuyến tính dọc theo timeline (Sweep Line) tính Running Sum. Chỗ nào Running Sum lớn nhất chính là thời điểm đắt hàng / bận rộn nhất.

---

# 5 Visual Explanation
Ví dụ: $A = [0, 0, 0, 0, 0]$, Update: Cộng 3 từ index 1 đến 3.

```mermaid
graph TD
    subgraph Bước 1: Khởi tạo mảng D
        D0[0] --- D1[0] --- D2[0] --- D3[0] --- D4[0] --- D5[0]
    end
    
    subgraph Bước 2: Range Update L=1, R=3, V=3
        U1["D[1] += 3"] --> D1_new[3]
        U2["D[4] -= 3"] --> D4_new[-3]
        D_Mid["D = [0, 3, 0, 0, -3, 0]"]
    end
    
    subgraph Bước 3: Khôi phục mảng A (Prefix Sum của D)
        A0["A[0] = 0"]
        A1["A[1] = 0 + 3 = 3"]
        A2["A[2] = 3 + 0 = 3"]
        A3["A[3] = 3 + 0 = 3"]
        A4["A[4] = 3 + (-3) = 0"]
    end
    
    D_Mid --> A0
```

---

# 6 Java Implementation
Tạo class đa dụng cho Difference Array:

```java
public class DifferenceArray {
    private int[] diff;

    // Khởi tạo từ mảng ban đầu
    public DifferenceArray(int[] nums) {
        // Cấp phát dư 1 phần tử (N+1) để D[R+1] không bị OutOfBounds nếu R = N-1
        diff = new int[nums.length + 1];
        diff[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            diff[i] = nums[i] - nums[i - 1];
        }
    }
    
    // Nếu khởi tạo từ mảng toàn 0
    public DifferenceArray(int size) {
        diff = new int[size + 1];
    }

    // Tăng giá trị V cho khoảng [L, R]
    // Time: O(1)
    public void update(int left, int right, int val) {
        diff[left] += val;
        diff[right + 1] -= val;
    }

    // Khôi phục mảng gốc
    // Time: O(N)
    public int[] result() {
        int n = diff.length - 1;
        int[] res = new int[n];
        res[0] = diff[0];
        for (int i = 1; i < n; i++) {
            res[i] = res[i - 1] + diff[i];
        }
        return res;
    }
}
```

---

# 8 Complexity Analysis
| Thao tác | Time | Space | Note |
|---|---|---|---|
| Khởi tạo | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ | Cần mảng độ dài $N+1$ |
| `update(L, R, V)` | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | 2 phép cộng trừ |
| `result()` khôi phục | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ | Prefix Sum của mảng diff |

Tổng quát nếu có $Q$ truy vấn cập nhật và xuất kết quả cuối cùng: Thời gian là $\mathcal{O}(N + Q)$. (Nhanh hơn rất nhiều so với $\mathcal{O}(N \times Q)$).

---

# 9 JVM Analysis
Tương tự như Prefix Sum, mảng Difference Array tiềm ẩn rủi ro **Integer Overflow**.
Nếu Update `diff[left] += val` diễn ra nhiều lần với `val` lớn, phần tử `diff[left]` hoặc mảng kết quả `res[i]` có thể vượt mốc `2,147,483,647` của kiểu `int`.
Luôn cân nhắc sử dụng mảng `long[] diff` nếu tổng của các Updates có rủi ro tràn số nguyên.

---

# 11 Production Usage
**Ứng dụng vào Booking / Thống kê đồng thời:**
- **Lập lịch chuyến bay**: Có 10,000 chuyến bay khởi hành và hạ cánh tại các giờ khác nhau. Giám đốc muốn biết: "Tại thời điểm nào trên không trung có NHIỀU chuyến bay nhất?".
- **Giải quyết**: Tạo mảng `diff` (với chỉ số mảng là thời gian tính bằng Phút từ 0 đến $24 \times 60$). Khởi hành lúc $S$, hạ cánh lúc $E \rightarrow$ `diff[S]++`, `diff[E]--`. Khôi phục mảng `res` (chính là số máy bay đang bay tại mỗi phút). Duyệt `res` tìm Max.

---

# 13 Common Bugs
20 lỗi thường gặp:
1. `ArrayIndexOutOfBoundsException`: Quên tăng kích thước mảng `diff` lên $N+1$. Khi $R = N-1$, `diff[R+1]` sẽ truy cập `diff[N]` gây lỗi.
2. Trừ/Cộng ngược chiều: `diff[L] -= V` thay vì `+= V`.
3. Nhầm chỉ số biên: Update từ L đến R, nhưng viết nhầm `diff[R] -= V` (Làm mất tác dụng của số R, đúng ra phải là `R+1`).
4. Khôi phục sai: Viết nhầm `res[i] = res[i-1] + diff[i-1]`. (Đúng phải là `+ diff[i]`).
5. Nếu tạo Difference array từ một mảng `A` không phải toàn số 0, quên khởi tạo `diff[0] = A[0]`.
6. Dùng Difference Array nhưng bài toán lại có yêu cầu Query đan xen. (Mảng Diff không trả lời Query nhanh được).
7. Gặp bài toán 2D Difference Array nhưng trừ thiếu ô góc Dưới-Phải.
8. Sweep Line với Tọa độ cực lớn (VD: $10^9$). Nếu dùng mảng sẽ bị OOM. (Nên đổi sang dùng `TreeMap`).
9. Nhầm bài toán Prefix Sum thành Difference Array hoặc ngược lại.
10. `R` nhỏ hơn `L` do input sai nhưng không có kiểm tra lệ.
(Còn tiếp ở phần Problem Files...)

---

# 14 Edge Cases
- $L = 0$: Update từ đầu mảng.
- $R = N-1$: Update đến phần tử cuối cùng (Lúc này `R+1 = N`, do đó mảng cần kích thước $N+1$).
- $L = R$: Chỉ cập nhật đúng 1 phần tử.

---

# 15 Optimization Techniques
- **Khử mảng Khôi phục**: Khi khôi phục để tìm Max hoặc xử lý logic, có thể tính `currentSum += diff[i]` mà không cần tạo mảng `res[]` mới. Tránh tốn RAM.
- **Sparse Difference Array (Với Tọa độ lớn)**: Khi khoảng Range cập nhật là $[1, 10^9]$ nhưng chỉ có $N = 1000$ cập nhật. Ta không tạo mảng 1 tỷ phần tử! Dùng `TreeMap<Integer, Integer> map`.
  - `map.put(L, map.getOrDefault(L, 0) + V)`
  - `map.put(R + 1, map.getOrDefault(R + 1, 0) - V)`
  - Sau đó duyệt qua `map.values()` để tính Prefix Sum.

---

# 19 Interview Questions
20 câu hỏi:
**Easy**
1. Difference Array là gì?
2. So sánh Prefix Sum và Difference Array.
3. Cách cập nhật mảng Diff để cộng $V$ vào đoạn $L \dots R$?
4. Làm sao khôi phục mảng ban đầu?
5. Tại sao cần tạo mảng kích thước $N+1$?

**Medium**
6. Nếu bạn phải Update, rồi Query, rồi Update, rồi Query. Có nên dùng mảng chênh lệch không?
7. Giải thích thuật toán Sweep Line dựa trên nguyên lý mảng chênh lệch.
8. Làm sao áp dụng Difference array nếu chỉ số bắt đầu từ $1$ tới $1,000,000,000$? (Sparse array bằng TreeMap).
9. Giải quyết bài toán "Car Pooling" (Ghép xe) sử dụng Difference array.

**Hard & Senior**
10. 2D Difference Array (Mảng chênh lệch 2 chiều). Để cộng V vào vùng $r1, c1$ đến $r2, c2$, 4 thao tác cập nhật là gì?
11. Chứng minh công thức 2D Difference Array bằng nguyên lý bao hàm loại trừ.

---

# 20 Practice Problems Link
Xem toàn bộ 30 bài toán tại: [05-Difference-Array-Pattern-Problems.md](05-Difference-Array-Pattern-Problems.md).

---

# 23 Summary
Difference Array (Mảng chênh lệch) là người anh em song sinh của Prefix Sum. Nếu Prefix Sum giúp lấy thông tin $\mathcal{O}(1)$, thì Mảng chênh lệch giúp sửa thông tin $\mathcal{O}(1)$. Chúng bù trừ cho nhau và thường xuyên được sử dụng liên hoàn (Cập nhật liên tục bằng Diff $\rightarrow$ Biến Diff thành Prefix Sum $\rightarrow$ Truy vấn liên tục). Nhớ mấu chốt: Cắm mốc bắt đầu `+V`, cắm mốc kết thúc `-V`.

---

# 24 Checklist
- [ ] Hiểu công thức `diff[L] += V` và `diff[R+1] -= V`.
- [ ] Luôn cấp phát mảng Diff có size $N+1$.
- [ ] Biết cách chuyển đổi qua lại với Prefix Sum.
- [ ] Biết dùng TreeMap (Sweep Line) cho bài toán giới hạn lớn.
