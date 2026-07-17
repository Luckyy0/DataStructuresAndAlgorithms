# Metadata
- **Document ID**: DSA-04-07
- **Version**: 1.0
- **Prerequisites**: DSA-04-01 (Array Basics), DSA-04-06 (Two Pointers Pattern)
- **Learning Objectives**: Thấu hiểu kỹ thuật Sliding Window (Cửa sổ trượt). Phân biệt được Window Kích thước cố định (Fixed-size) và Window Kích thước thay đổi (Variable-size). Nắm được Template chuẩn cho Sliding Window để tránh các lỗi logic viền hẹp (Off-by-one).
- **Estimated Reading Time**: 60 phút
- **Difficulty**: Intermediate / Advanced
- **Keywords**: Sliding Window, Subarray, Substring, Hash Map State, Variable Size Window

---

# 1 Purpose
Sliding Window (Cửa sổ trượt) là một bước tiến hóa của thuật toán Two Pointers (Dạng cùng chiều - Same Direction). Nó được sinh ra để giải quyết đặc thù các bài toán liên quan đến **Mảng con liên tiếp (Contiguous Subarray)** hoặc **Chuỗi con liên tiếp (Substring)**. Thay vì tính toán lại từ đầu cho mỗi mảng con, ta tạo ra một "khung cửa sổ" trượt qua mảng, tại mỗi bước chỉ cần **Cộng phần tử mới vào viền phải** và **Trừ phần tử cũ bị rớt ra khỏi viền trái**.

---

# 2 Motivation
**Bài toán**: Cho mảng $N = 10^5$, tìm tổng lớn nhất của một mảng con liên tiếp có độ dài đúng $K = 1000$.
- **Cách Brute Force**: Duyệt điểm đầu $i$ từ $0$ đến $N-K$. Tại mỗi điểm đầu, tạo vòng lặp $1000$ bước tính tổng. Tổng thời gian $\approx N \times K = 10^8$ phép tính. Rất chậm!
- **Sử dụng Prefix Sum**: Dùng công thức $P[i+K] - P[i]$. Tốn $\mathcal{O}(N)$ thời gian, nhưng phải cấp phát thêm mảng $\mathcal{O}(N)$ bộ nhớ.
- **Dùng Sliding Window**: Tính tổng $K$ phần tử đầu tiên làm Window gốc. Sau đó dịch Window sang phải từng bước một. `newSum = oldSum + nums[right] - nums[left]`. Tốn $\mathcal{O}(N)$ thời gian, KHÔNG tốn thêm bộ nhớ $\mathcal{O}(1)$.

---

# 3 Mathematical Foundation
## Chứng minh độ phức tạp $\mathcal{O}(N)$ của Variable-size Window
Trong dạng cửa sổ có kích thước thay đổi (Variable-size), vòng lặp `while` co cửa sổ (Shrink) nằm BÊN TRONG vòng lặp `for` mở rộng cửa sổ (Expand). Thoạt nhìn có vẻ là $\mathcal{O}(N^2)$.
Tuy nhiên, hãy phân tích bằng Amortized Analysis:
- Con trỏ `right` (Expand) bắt đầu từ 0 và luôn tăng lên, tối đa chạm mốc $N$. (Thực hiện $N$ lần dịch phải).
- Con trỏ `left` (Shrink) bắt đầu từ 0 và CŨNG luôn tăng lên, tối đa chạm mốc $N$. (Thực hiện tối đa $N$ lần dịch trái).
- Một phần tử bất kỳ vào Window đúng 1 lần (qua `right`), ra khỏi Window đúng 1 lần (qua `left`).
- Tổng cộng cả 2 con trỏ chỉ di chuyển tối đa $2N$ bước.
$\rightarrow$ Vòng lặp bên trong dù có, tổng số lần chạy trên toàn chương trình bị chặn trên bởi $N$. Suy ra thời gian là Hằng số tỷ lệ thuận với $N \rightarrow \mathcal{O}(N)$.

---

# 4 Core Theory
Có 2 biến thể Sliding Window.

## 4.1 Fixed-size Window (Kích thước Cố định)
- Bài toán cung cấp một hằng số $K$ (Kích thước mảng con).
- Yêu cầu tìm Tối đa (Max), Tối thiểu (Min) hoặc Trung bình (Average) của một đại lượng nào đó trong cửa sổ $K$.
- **Đặc trưng**: Window mở rộng cho đến khi chiều dài đạt $K$. Kể từ đó, mỗi khi `right` tiến 1 bước thì `left` BẮT BUỘC tiến 1 bước để giữ nguyên độ dài.

## 4.2 Variable-size Window (Kích thước Thay đổi)
- Bài toán cung cấp một **Điều kiện (Condition/Target)**. (Ví dụ: Tổng mảng con $\ge S$, Không có ký tự trùng lặp, Có tối đa $K$ số 0).
- Yêu cầu tìm **Độ dài lớn nhất (Max Length)** hoặc **Độ dài nhỏ nhất (Min Length)** của cửa sổ thỏa mãn điều kiện đó.
- **Đặc trưng**:
  - `right` luôn tiến để mở rộng Window nhằm thêm phần tử.
  - Sau mỗi lần `right` tiến, ta kiểm tra xem Window còn HỢP LỆ (Valid) không.
  - Nếu KHÔNG hợp lệ (Ví dụ: Tổng vượt quá S, ký tự bị trùng), ta bắt đầu tiến `left` thu hẹp cửa sổ cho đến khi nó HỢP LỆ trở lại.

---

# 5 Visual Explanation

**Ví dụ: Variable-size Window. Tìm Subarray dài nhất có tổng $\le 7$. Mảng: [3, 1, 2, 5, 2]**

```mermaid
graph TD
    subgraph Bước 1: [3], Sum = 3 (Hợp lệ)
        W1["[3], 1, 2, 5, 2"] --> Max1["Max = 1"]
    end
    
    subgraph Bước 2: [3, 1], Sum = 4 (Hợp lệ)
        W2["[3, 1], 2, 5, 2"] --> Max2["Max = 2"]
    end
    
    subgraph Bước 3: [3, 1, 2], Sum = 6 (Hợp lệ)
        W3["[3, 1, 2], 5, 2"] --> Max3["Max = 3"]
    end
    
    subgraph Bước 4: [3, 1, 2, 5], Sum = 11 (VƯỢT QUÁ 7 -> Thu hẹp)
        W4["3, [1, 2, 5], 2"] --> Sum8["Sum = 8 (Vẫn vượt, tiếp tục thu)"]
        Sum8 --> W4_2["3, 1, [2, 5], 2"]
        W4_2 --> Sum7["Sum = 7 (Hợp lệ, Max = 3)"]
    end
    
    W1 --> W2 --> W3 --> W4
```

---

# 6 Java Implementation
## Template: Fixed-size Window
```java
public class FixedWindow {
    public double findMaxAverage(int[] nums, int k) {
        int windowSum = 0;
        int maxSum = Integer.MIN_VALUE;
        
        // Bước 1: Xây dựng cửa sổ gốc (K phần tử đầu tiên)
        for (int i = 0; i < k; i++) {
            windowSum += nums[i];
        }
        maxSum = windowSum;
        
        // Bước 2: Trượt cửa sổ về phía bên phải
        for (int right = k; right < nums.length; right++) {
            // Thêm phần tử mới ở right, Bỏ phần tử cũ ở left (right - k)
            windowSum = windowSum + nums[right] - nums[right - k];
            maxSum = Math.max(maxSum, windowSum);
        }
        
        return (double) maxSum / k;
    }
}
```

## Template: Variable-size Window (Tìm Mảng DÀI NHẤT)
*Ví dụ: Dài nhất không quá Target.*
```java
public class VariableWindowLongest {
    public int longestSubarray(int[] nums, int k) {
        int left = 0, maxLength = 0;
        int windowSum = 0; // Trạng thái của window
        
        for (int right = 0; right < nums.length; right++) {
            windowSum += nums[right]; // Thêm dữ liệu vào Window
            
            // Xử lý VI PHẠM (Phải là vòng lặp while)
            while (windowSum > k) { 
                windowSum -= nums[left]; // Loại bỏ từ bên trái
                left++; // Thu hẹp Window
            }
            
            // Tại đây Window ĐÃ HỢP LỆ. Cập nhật kết quả Max
            maxLength = Math.max(maxLength, right - left + 1);
        }
        return maxLength;
    }
}
```

## Template: Variable-size Window (Tìm Mảng NGẮN NHẤT)
*Ví dụ: Ngắn nhất lớn hơn hoặc bằng Target.*
```java
public class VariableWindowShortest {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0, minLength = Integer.MAX_VALUE;
        int windowSum = 0;
        
        for (int right = 0; right < nums.length; right++) {
            windowSum += nums[right];
            
            // Xử lý ĐẠT ĐIỀU KIỆN (Thỏa mãn rồi nên cố gắng thu hẹp cho ngắn hơn)
            while (windowSum >= target) {
                // Tại đây Window ĐẠT, cập nhật minLength TRONG vòng lặp
                minLength = Math.min(minLength, right - left + 1);
                
                windowSum -= nums[left]; // Thử vứt bớt đồ bên trái xem còn đạt không
                left++;
            }
        }
        
        return minLength == Integer.MAX_VALUE ? 0 : minLength;
    }
}
```

---

# 8 Complexity Analysis
| Phương pháp | Time | Space | Ghi chú |
|---|---|---|---|
| Sliding Window Thuần (Array/Sum) | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | |
| String Window + Mảng đếm Ký tự (256) | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | Tốn 256 bytes cho bộ đếm |
| String Window + HashMap Đếm Từ khóa | $\mathcal{O}(N)$ | $\mathcal{O}(K)$ | K là số lượng từ khóa khác nhau |

---

# 9 JVM Analysis
## Sự nguy hiểm của Auto-Boxing trong Window State
Khi giải toán trên Chuỗi, người lập trình có xu hướng dùng `Map<Character, Integer> window = new HashMap<>()` để đếm số lần xuất hiện của kí tự.
Do quá trình trượt diễn ra $\mathcal{O}(N)$ lần, thao tác `map.put(c, map.getOrDefault(c, 0) + 1)` sẽ liên tục tạo ra hàng vạn Object `Integer` trên Heap do Auto-Boxing, gây áp lực cực lớn lên Garbage Collector (Minor GC).
**Giải pháp trong JVM:** Nếu biết chắc Chuỗi chỉ gồm kí tự ASCII (Tiếng Anh, số), LUÔN dùng mảng nguyên thủy `int[] charCount = new int[256]` (hoặc 128) để làm bộ đếm (State tracker). Nó nằm trọn trên Stack hoặc Heap liên tiếp, cực nhanh và không sinh Object rác.

---

# 11 Production Usage
**Network Rate Limiting (Giới hạn truy cập)**
Thuật toán **Sliding Window Log** hoặc **Sliding Window Counter** được Redis và Nginx sử dụng để chống DDOS / Rate Limit.
Ví dụ: "Mỗi IP chỉ được request 100 lần trong 60 giây qua". Cửa sổ trượt của chúng ta là $T$ đến $T - 60s$. Khi thời gian $T$ trôi đi, cửa sổ dịch sang phải, các request cũ hơn $60s$ bị `left` quét qua và loại bỏ khỏi bộ nhớ.

---

# 12 Design Decisions
**Tại sao trong Template Dài nhất, ta cập nhật `Max` BÊN NGOÀI vòng `while`, còn trong Template Ngắn nhất ta cập nhật `Min` BÊN TRONG vòng `while`?**
- Tìm **Mảng Dài nhất** với đk (ví dụ $Sum \le K$): Vòng `while` mục đích là để TRỪ ĐI sự vi phạm. Nghĩa là lúc chui ra khỏi `while`, cửa sổ mới trở về trạng thái HỢP LỆ. Ta chỉ được phép lấy kỷ lục khi nó hợp lệ $\rightarrow$ Phải cập nhật `Max` ở ngoài.
- Tìm **Mảng Ngắn nhất** với đk (ví dụ $Sum \ge K$): Vòng `while` chỉ được kích hoạt khi ĐÃ ĐẠT/HỢP LỆ. Vì nó đã đạt, ta có quyền ghi nhận kỷ lục `Min`. Sau đó ta thu hẹp nó xem có kỷ lục nào nhỏ hơn không $\rightarrow$ Phải cập nhật `Min` ở ngay bên trong trước khi thu hẹp.

---

# 13 Common Bugs
20 lỗi thường gặp:
1. Nhầm Template: Tính Max mà lại cập nhật bên trong `while`.
2. Vòng `while` thu hẹp không có điều kiện bảo vệ `left <= right`, dẫn tới `left` chạy vượt `right` khi mảng có số âm.
3. Không xử lý kết quả trống: Trả về `Integer.MAX_VALUE` thay vì `0` nếu không tìm thấy chuỗi nào.
4. Lỗi Off-by-one khi tính độ dài: Viết `right - left` thay vì `right - left + 1`.
5. Trong Sliding Window Ký tự, mảng đếm tần suất bị tràn vì dùng ASCII Code nhưng không trừ đi gốc `'a'`.
6. String Window: Thu hẹp `left` nhưng quên xóa/giảm số đếm kí tự tương ứng với `s.charAt(left)` trong HashMap.
7. Áp dụng Sliding Window Variable Size cho Mảng có Số Âm. (LỖI NGHIÊM TRỌNG! Nếu mảng có số âm, việc thêm 1 số vào không đảm bảo Tổng sẽ TĂNG, thu hẹp không đảm bảo Tổng sẽ GIẢM. Window bị gãy tính Monotonicity. Khi có số âm $\rightarrow$ Phải dùng Prefix Sum + HashMap).
8. Dùng Fixed-size window nhưng lại viết vòng lặp kiểm tra `while (right - left + 1 > K)` ở mỗi bước (Không sai nhưng thừa thãi so với việc dùng `for` ban đầu).
9. Trong Sliding Window Maximum (Dùng Deque), quên loại bỏ chỉ số cũ (out of window) ở đầu Deque.
10. Quên tăng `left++` sau khi thu hẹp cửa sổ, sinh lặp vô hạn.
(Còn tiếp ở Problem Files...)

---

# 14 Edge Cases
- Kích thước cửa sổ $K$ lớn hơn cả chiều dài mảng (Tùy bài sẽ return mảng đó, hoặc ném lỗi).
- Cửa sổ không tìm thấy đáp án hợp lệ.
- Phần tử trong mảng gây tràn Integer (Overflow) khi tính `windowSum`.

---

# 15 Optimization Techniques
- **Non-shrinkable Window (Cửa sổ không thu hẹp)**: Một trick đỉnh cao khi tìm Max Length. Nếu ta chỉ quan tâm kỷ lục lớn nhất, thay vì dùng `while` thu hẹp `left`, ta chỉ dùng `if (invalid)` để dịch `left` một bước. Kích thước cửa sổ **không bao giờ giảm**, nó chỉ trượt đi hoặc phình to. Ở cuối vòng lặp, độ dài cửa sổ CHÍNH LÀ đáp án. Kỹ thuật này giúp tiết kiệm lệnh gán biến `maxLen`.

---

# 19 Interview Questions
20 câu hỏi:
**Easy**
1. Giải thích khái niệm Cửa sổ trượt (Sliding Window).
2. Sự khác biệt giữa Fixed-size và Variable-size Window là gì?
3. Tính độ dài cửa sổ bằng công thức gì dựa trên 2 chỉ số left và right?
4. Sliding Window khắc phục nhược điểm gì của thuật toán Vét cạn (Brute force) 2 vòng lặp lồng nhau?

**Medium**
5. Tại sao Sliding window Variable-size lại có Time Complexity là $\mathcal{O}(N)$ dù có 2 vòng lặp (For lồng While)?
6. Tôi muốn tìm mảng con có tổng $\ge K$, tại sao tôi có thể dùng Variable window?
7. Tôi muốn tìm mảng con có tổng BẰNG K. Nếu mảng chứa số âm, tôi có thể dùng Sliding window không? Tại sao? (Không, vì mất tính đơn điệu. Tổng không còn tăng/giảm tuyến tính theo con trỏ).
8. Hãy giải thích tại sao trong template Shortest (Ngắn nhất), ta update min bên trong vòng While.
9. Có thể áp dụng Sliding Window để giải quyết bài toán chuỗi đối xứng không? (Không, vì tính đối xứng bị phá vỡ hoàn toàn khi dịch một kí tự, không có tính chất bảo toàn viền (edge property)).

**Hard & Senior**
10. Trình bày chi tiết thuật toán Sliding Window Maximum (Mảng các max cục bộ của các cửa sổ cố định K) sử dụng Cấu trúc Monotonic Deque. Phân tích tại sao nó đạt $\mathcal{O}(N)$.
11. Trong hệ thống Rate Limiter phân tán, Sliding Window Log tốn bao nhiêu RAM nếu một user request 1 triệu lần / phút? Bạn tối ưu cấu trúc dữ liệu thế nào? (Thay log bằng cấu trúc Sliding Window Counter với các bucket nhỏ).

---

# 20 Practice Problems Link
Xem toàn bộ 30 bài toán tại: [07-Sliding-Window-Pattern-Problems.md](07-Sliding-Window-Pattern-Problems.md).

---

# 23 Summary
Sliding Window biến các bài toán Mảng con / Chuỗi con $\mathcal{O}(N^2)$ trở thành những cuộc dạo chơi $\mathcal{O}(N)$. Việc nắm chắc 2 Template (Longest - Cập nhật ngoài While, Shortest - Cập nhật trong While) và nhận diện được Cạm bẫy Số Âm (Negative numbers breaker) sẽ giúp bạn vượt qua 90% bài toán Cửa sổ trượt trong mọi buổi phỏng vấn.

---

# 24 Checklist
- [ ] Phân biệt được khi nào update Max/Min ở trong hay ngoài vòng While.
- [ ] Luôn cảnh giác kiểm tra xem Mảng có Số Âm không trước khi quyết định dùng Sliding window để tính tổng.
- [ ] Tự viết được thuật toán đếm kí tự `int[] map = new int[256]` để tối ưu hóa thay thế cho HashMap trong Sliding Window String.
