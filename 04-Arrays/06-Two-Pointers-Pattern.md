# Metadata
- **Document ID**: DSA-04-06
- **Version**: 1.0
- **Prerequisites**: DSA-04-01 (Array Basics), DSA-02-02 (Time Complexity)
- **Learning Objectives**: Hiểu và phân loại 3 dạng chính của Two Pointers (Opposite Direction, Same Direction, Fast & Slow Pointers). Biết cách áp dụng Two Pointers để tối ưu hóa thuật toán từ $\mathcal{O}(N^2)$ xuống $\mathcal{O}(N)$, tiết kiệm bộ nhớ, và tránh tạo mảng phụ.
- **Estimated Reading Time**: 60 phút
- **Difficulty**: Intermediate
- **Keywords**: Two Pointers, Fast and Slow, Opposite Direction, Monotonic, In-place

---

# 1 Purpose
**Two Pointers (Hai con trỏ)** không phải là một thuật toán, mà là một **Mẫu tư duy (Pattern)** dùng để duyệt mảng, chuỗi hoặc danh sách liên kết. Thay vì dùng 2 vòng lặp lồng nhau duyệt mảng tốn $\mathcal{O}(N^2)$ thời gian, ta sử dụng hai biến chỉ số (pointers) chạy đồng thời để duyệt cấu trúc dữ liệu trong 1 lần duy nhất, giảm thời gian xuống $\mathcal{O}(N)$ và chỉ tốn $\mathcal{O}(1)$ bộ nhớ phụ.

---

# 2 Motivation
**Bài toán kinh điển**: Cho một mảng **đã sắp xếp** (ví dụ: `[-3, -1, 0, 2, 5, 8]`), tìm 2 số có tổng bằng một mục tiêu `Target = 7`.
- **Cách Brute Force (Vét cạn)**: Dùng 2 vòng lặp lồng nhau, thử mọi cặp $(i, j)$. Nếu mảng có $10^5$ phần tử, ta cần khoảng $10^{10} / 2$ phép tính $\rightarrow$ Chậm (Time Limit Exceeded).
- **Dùng HashSet**: Lưu các phần tử vào Set, với mỗi $x$, kiểm tra xem $Target - x$ có trong Set không. Thời gian $\mathcal{O}(N)$, nhưng tốn thêm $\mathcal{O}(N)$ bộ nhớ cho HashSet.
- **Dùng Two Pointers**: Đặt con trỏ Trái ở đầu, Phải ở cuối.
  - Tổng hiện tại $S = arr[L] + arr[R]$.
  - Nếu $S == Target \rightarrow$ Tìm thấy!
  - Nếu $S < Target \rightarrow$ Cần tăng tổng lên. Tăng $L$ (`L++`). (Vì mảng đã sắp xếp, tiến L sang phải sẽ được số lớn hơn).
  - Nếu $S > Target \rightarrow$ Cần giảm tổng xuống. Giảm $R$ (`R--`).
$\rightarrow$ Vừa đạt $\mathcal{O}(N)$ thời gian, vừa đạt $\mathcal{O}(1)$ không gian. Đó là sức mạnh của Two Pointers.

---

# 3 Mathematical Foundation
## Tính đúng đắn của Two Pointers (Tại sao nó hoạt động?)
Nhiều người nghĩ Two Pointers chỉ là một trò lừa mẹo (heuristic) và có thể bỏ sót kết quả. Toán học chứng minh điều ngược lại trong bài toán Two Sum (Mảng sắp xếp tăng dần):
Giả sử cặp kết quả đúng là ở chỉ số $(l^*, r^*)$.
- Ban đầu $L = 0, R = N-1$.
- Trong quá trình chạy, nếu $L$ chạm tới $l^*$ trước (tức là $L = l^*, R > r^*$), tổng hiện tại $S = arr[l^*] + arr[R]$.
- Vì mảng tăng dần và $R > r^*$, ta có $arr[R] > arr[r^*]$. Do đó $S = arr[l^*] + arr[R] > arr[l^*] + arr[r^*] = Target$.
- Thuật toán sẽ nhận thấy $S > Target$ và chỉ đạo `R--`. Nó sẽ tiếp tục giảm $R$ liên tục cho đến khi $R$ bằng đúng $r^*$.
- Do đó, thuật toán **KHÔNG THỂ** bỏ lỡ cặp kết quả $(l^*, r^*)$. Số lần lặp tối đa là $N$, nên chi phí là $\mathcal{O}(N)$.

**Điều kiện kiên quyết (Prerequisite):** Không gian tìm kiếm phải có tính chất **Đơn điệu (Monotonicity)** (Ví dụ: Mảng đã sắp xếp tăng dần).

---

# 4 Core Theory
Có 3 dạng (Archetypes) chính của Pattern này:

## 4.1 Opposite Direction (Hai con trỏ ngược chiều)
- **Cơ chế**: Một con trỏ bắt đầu từ `0` (Left), một con trỏ từ `N-1` (Right). Tiến dần về nhau cho đến khi gặp nhau (`Left >= Right`).
- **Ứng dụng**:
  - Tìm cặp số (Two Sum II, 3Sum, 4Sum).
  - Đảo ngược mảng (Reverse Array/String).
  - Kiểm tra chuỗi đối xứng (Palindrome).
  - Bài toán diện tích chứa nước lớn nhất (Container with Most Water).

## 4.2 Same Direction (Hai con trỏ cùng chiều)
- **Cơ chế**: Cả 2 con trỏ bắt đầu từ `0`. Một con trỏ `reader` đọc dữ liệu, một con trỏ `writer` ghi dữ liệu đè lên chính mảng đó.
- **Ứng dụng**:
  - Xóa phần tử In-place (Remove Duplicates, Remove Element).
  - Dời số 0 về cuối (Move Zeros).
  - Sliding Window (Là một biến thể nâng cao của Same Direction, sẽ có bài học riêng).

## 4.3 Fast & Slow Pointers (Con trỏ Nhanh và Chậm)
- **Cơ chế**: Hai con trỏ bắt đầu cùng vị trí. `Slow` bước 1 bước, `Fast` bước 2 bước.
- **Ứng dụng**:
  - Tìm chu trình trong Linked List (Floyd's Cycle-Finding).
  - Tìm điểm chính giữa của Linked List.
  - Số hạnh phúc (Happy Number).

---

# 5 Visual Explanation

```mermaid
graph LR
    subgraph Opposite Direction (Two Sum)
        O0[-3]:::left --- O1[-1] --- O2[0] --- O3[2] --- O4[5] --- O5[8]:::right
        classDef left fill:#3498db,color:#fff;
        classDef right fill:#e74c3c,color:#fff;
    end
    
    subgraph Same Direction (Remove Duplicates)
        S0[1]:::writer --- S1[1] --- S2[2]:::reader --- S3[2] --- S4[3]
        classDef writer fill:#2ecc71,color:#fff;
        classDef reader fill:#9b59b6,color:#fff;
    end
    
    subgraph Fast & Slow (Middle of List)
        F0((1)) --- F1((2)):::slow --- F2((3)) --- F3((4)):::fast --- F4((5))
        classDef slow fill:#f1c40f;
        classDef fast fill:#e67e22,color:#fff;
    end
```

---

# 6 Java Implementation
## Dạng 1: Opposite Direction (Two Sum Sorted)
```java
public class TwoSum {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
        
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) {
                return new int[]{left + 1, right + 1}; // 1-indexed (tuỳ đề bài)
            } else if (sum < target) {
                left++; // Tổng nhỏ, tăng số nhỏ
            } else {
                right--; // Tổng lớn, giảm số lớn
            }
        }
        return new int[]{-1, -1};
    }
}
```

## Dạng 2: Same Direction (Move Zeros)
```java
public class MoveZeros {
    public void moveZeroes(int[] nums) {
        int writer = 0; // Đánh dấu nơi ghi số khác 0 tiếp theo
        
        // Reader duyệt qua toàn bộ mảng
        for (int reader = 0; reader < nums.length; reader++) {
            if (nums[reader] != 0) {
                // Đổi chỗ để số khác 0 nhảy lên trên
                int temp = nums[writer];
                nums[writer] = nums[reader];
                nums[reader] = temp;
                writer++;
            }
        }
    }
}
```

## Dạng 3: Fast & Slow (Find duplicate number in Array - Coi mảng như Linked List)
```java
public class FindDuplicate {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[nums[0]];
        
        // Giai đoạn 1: Tìm điểm giao nhau (Intersection)
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[nums[fast]];
        }
        
        // Giai đoạn 2: Tìm cổng của chu trình (Cycle entrance)
        slow = 0;
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        
        return slow; // Đây chính là số bị lặp
    }
}
```

---

# 8 Complexity Analysis
| Dạng bài | Time | Space | Ghi chú |
|---|---|---|---|
| Mảng có sắp xếp sẵn | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | Tối ưu tuyệt đối. |
| Mảng CHƯA sắp xếp | $\mathcal{O}(N \log N)$ | $\mathcal{O}(1)$ hoặc $\mathcal{O}(N)$ | Mất $\mathcal{O}(N \log N)$ cho thao tác `Arrays.sort()` |
| Same Direction (In-place) | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | Không dùng thêm cấu trúc phụ. |

---

# 9 JVM Analysis
## CPU Branch Prediction
Đoạn code trong Two Pointers thường chứa các lệnh điều kiện (if/else).
```java
if (sum == target) return;
else if (sum < target) left++;
else right--;
```
CPU sử dụng **Branch Predictor** để đoán xem nhánh nào sẽ chạy. Đối với Two Pointers, việc tiến Left hay lùi Right có tính chu kỳ hoặc cụm (Clusters), CPU có thể đoán đúng khá nhiều, giúp giảm thiểu Branch Misprediction Penalty (Chi phí dự đoán sai nhánh), dẫn đến thuật toán cực kì nhanh trên máy tính hiện đại so với các bước nhảy ngẫu nhiên.

---

# 11 Production Usage
Trong hệ thống thực tế:
- **Git Merge / Diff**: Thuật toán so sánh phiên bản văn bản (Myer's Diff Algorithm) sử dụng khái niệm tương tự Two Pointers để tìm độ dài chuỗi chung dài nhất (LCS).
- **Memory Compaction (GC)**: Khi JVM Garbage Collector dọn dẹp Heap, nó sử dụng thuật toán Lisp2 (hoặc tương tự) có dùng "Same Direction Pointers": Một con trỏ đọc object còn sống, con trỏ kia ghi object đó vào vùng bộ nhớ liên tiếp mới để dồn rác (Compact).

---

# 12 Design Decisions
**Tại sao phải dùng "Writer" và "Reader" trong bài toán In-place thay vì hàm `remove()`?**
Nếu bạn dùng `ArrayList.remove(index)`, mỗi lần xóa tốn $\mathcal{O}(N)$ thời gian do mảng phải tự dời các phần tử. Nếu xóa $K$ phần tử, thời gian là $\mathcal{O}(K \times N)$.
Sử dụng Reader & Writer Pointers (Two Pointers), ta chỉ "Bỏ qua" phần tử rác và chỉ copy các phần tử cần thiết đè lên trước. Duyệt đúng 1 lần, tổng thời gian $\mathcal{O}(N)$.

---

# 13 Common Bugs
20 lỗi thường gặp:
1. **Lặp vô hạn (Infinite Loop)**: Quên tăng `left` hoặc giảm `right` bên trong vòng `while`.
2. **Index Out Of Bounds**: Vượt mảng do dùng `<` thay vì `<=` (hoặc ngược lại) trong vòng lặp.
3. Không xử lý mảng trùng lặp (Duplicates) trong bài 3Sum: Quên lệnh `while (left < right && nums[left] == nums[left+1]) left++;`.
4. Viết `while (left <= right)` thay vì `while (left < right)` trong các bài toán yêu cầu 2 con trỏ KHÁC NHAU.
5. Mảng chưa được sắp xếp mà cứ dùng Opposite Direction Pointers (Kết quả sai hoàn toàn).
6. Integer Overflow: Khi tính tổng `arr[left] + arr[right]`, tổng vượt quá `int`. Cần ép kiểu `(long)arr[left] + arr[right]`.
7. Tính Mid nhầm: `mid = (left + right) / 2` gây tràn số nếu `left+right > MAX_INT`. Chuẩn: `mid = left + (right - left) / 2`.
8. 3Sum: Không sort mảng trước khi dùng 2 pointers.
9. Fast & Slow pointers: `fast.next.next` có thể gây NPE (NullPointerException) nếu không kiểm tra `fast` và `fast.next` khác null.
10. Remove Element: Hàm trả về độ dài mảng mới, nhưng code lại trả về mảng gốc có các rác ở cuối.
11. Sort Colors (Dutch Flag - 3 pointers): Khi `nums[i] == 2`, đổi chỗ với `high`, nhưng quên KHÔNG tăng `i` vì phần tử từ `high` đổi về chưa được kiểm tra!
12. Gán nhầm `writer = reader` mà không có điều kiện `if`.
13. Tính diện tích lớn nhất (Trapping Rain Water): Nhầm thuật toán của Cột với Diện tích Container.
14. Move Zeros: Lệnh swap tự đổi chỗ với chính nó khi mảng toàn số khác 0 (Gây tốn CPU, nhưng không sai logic. Có thể thêm `if (writer != reader)` để tối ưu).
15. Binary Search vs Two Pointers: Cố xài Two Pointers $\mathcal{O}(N)$ cho bài toán có thể giải $\mathcal{O}(\log N)$ bằng Binary Search.
16. Valid Palindrome: Không dùng `Character.isLetterOrDigit` dẫn đến so sánh luôn cả dấu phẩy, khoảng trắng.
17. Valid Palindrome: Lỗi viết hoa/thường (`A` != `a`). Cần `Character.toLowerCase()`.
18. String trong Java là Immutable. Dùng 2 pointers để swap kí tự phải chuyển String sang `char[]` trước.
19. Mảng toàn số âm: Container with Most Water không hoạt động với độ cao âm.
20. Trả về Index 0-based hay 1-based: Nhầm lẫn giữa LeetCode và HackerRank.

---

# 14 Edge Cases
- Mảng Rỗng hoặc chỉ có 1 phần tử.
- Mảng có toàn số giống hệt nhau `[2, 2, 2, 2]`. (Dễ bị lặp vô hạn nếu không skip duplicate cẩn thận).
- Mảng đã sắp xếp ngược (Descending) (Cần nghịch đảo logic của Two Pointers).

---

# 15 Optimization Techniques
- **Skip Duplicates**: Trong bài toán đếm/tìm cặp, nếu $A[i] == A[i-1]$, ta dùng lệnh `continue` (Skip qua) để không lặp lại tính toán thừa thãi.
- **Early Exit**: Nếu mục tiêu là tìm mảng con có tổng = K, và đang duyệt mảng chỉ toàn số dương, nếu tổng $> K$, ta có thể thoát sớm (Break) thay vì duyệt hết mảng.

---

# 16 Best Practices
- LUÔN LUÔN vẽ cấu trúc mảng và đặt ngón tay cái & trỏ mô phỏng Left và Right trước khi code. Sự di chuyển của ngón tay chính là logic của thuật toán.
- Tránh dùng phép trừ `target - nums[left]` so sánh với `nums[right]` vì nguy cơ Integer Underflow (Trừ số âm lớn thành số dương tràn). Dùng phép cộng và ép kiểu an toàn.

---

# 19 Interview Questions
20 câu hỏi:
**Easy**
1. Two Pointers là gì? Nó giúp cải thiện Time Complexity như thế nào?
2. Có mấy loại Two Pointers thường gặp?
3. Tại sao Two Pointers yêu cầu mảng phải Sắp xếp (trong dạng tìm tổng)?
4. Làm sao dùng 2 con trỏ lật ngược mảng?
5. Same Direction Pointers được ứng dụng trong trường hợp nào?

**Medium**
6. Thuật toán Fast & Slow Pointers phát hiện chu trình (Floyd's algorithm) hoạt động toán học thế nào?
7. Giải thích bài toán 3Sum. Tại sao nó có độ phức tạp $\mathcal{O}(N^2)$?
8. Tại sao trong 3Sum, ta phải sort mảng?
9. Thuật toán Dutch National Flag (Sắp xếp 0, 1, 2) dùng bao nhiêu con trỏ?
10. Tại sao Two Pointers lại tối ưu được bài Container With Most Water? Chứng minh không bị sót trường hợp Max.

**Hard & Senior**
11. Trình bày bài Trapping Rain Water bằng Two Pointers (Không dùng mảng phụ).
12. Có thể áp dụng Two Pointers cho Cây Nhị Phân Tìm Kiếm (BST) không? (Dùng Iterator tiến và lùi).
13. Khác biệt giữa Sliding Window và Same Direction Two Pointers là gì?

---

# 20 Practice Problems Link
Xem toàn bộ 30 bài toán tại: [06-Two-Pointers-Pattern-Problems.md](06-Two-Pointers-Pattern-Problems.md).

---

# 23 Summary
Two Pointers là Pattern nền tảng và dễ hiểu nhất để tối ưu từ $\mathcal{O}(N^2)$ xuống $\mathcal{O}(N)$. Sức mạnh của nó nằm ở việc loại bỏ sự dư thừa: Nhờ biết mảng đã sắp xếp (Đơn điệu), khi một con trỏ tiến lên hoặc lùi xuống, ta có thể "loại bỏ một cách an toàn" toàn bộ không gian tìm kiếm còn lại mà không cần phải dùng vòng lặp vét cạn.

---

# 24 Checklist
- [ ] Ghi nhớ Template vòng `while (left < right)`.
- [ ] Xử lý đúng IndexOutBounds.
- [ ] Biết cách Skip Duplicates trong bài 3Sum.
- [ ] Sử dụng thuần thục Reader/Writer để xóa In-place.
- [ ] Biết dùng Fast/Slow cho Linked List/Chu trình.
