# Advanced Searching (Tìm kiếm Nâng cao)

## 1. Giới thiệu (Introduction)
Trong khoa học máy tính, **Advanced Searching** (Tìm kiếm Nâng cao) bao gồm các kỹ thuật và biến thể phức tạp hơn của Binary Search. Những kỹ thuật này không chỉ áp dụng trên các mảng đã được sắp xếp tăng dần hoặc giảm dần, mà còn mở rộng ra các dạng hàm số hoặc cấu trúc đặc biệt như **Mountain Array** (mảng dạng núi - tăng rồi giảm), **Unimodal Functions** (hàm đơn cực - hàm chỉ có một cực đại hoặc cực tiểu), hoặc việc tìm kiếm trên hai mảng đã sắp xếp (ví dụ: tìm trung vị).

Các kỹ thuật nổi bật trong chủ đề này bao gồm: tìm đỉnh (Finding peaks), **Ternary Search** (Tìm kiếm tam phân), và **Partitioning** (Phân hoạch) trên nhiều mảng độc lập.

## 2. Khái niệm cơ bản (Basic Concepts)
- **Mountain Array**: Một mảng giá trị tăng dần đến một phần tử cực đại (đỉnh - peak) rồi giảm dần. 
- **Unimodal Function**: Một hàm số có đồ thị đi lên tới một đỉnh rồi đi xuống (đơn cực đại), hoặc đi xuống tới một đáy rồi đi lên (đơn cực tiểu).
- **Peak Element**: Một phần tử lớn hơn (hoặc lớn hơn hoặc bằng) các phần tử kề cạnh nó.
- **Partitioning**: Chia một hoặc nhiều tập hợp dữ liệu thành các phần sao cho các phần tử ở nửa này luôn nhỏ hơn hoặc bằng các phần tử ở nửa kia, thường được dùng trong bài toán tìm Median of Two Sorted Arrays.

## 3. Cấu trúc dữ liệu (Data Structures)
- **Array** (Mảng): Cấu trúc dữ liệu phổ biến nhất để thực hiện tìm kiếm.
- **Function/API**: Trong một số bài toán, dữ liệu không được cho dưới dạng mảng rõ ràng mà ẩn qua một API (hàm) như trong bài toán `Find in Mountain Array` hoặc Ternary Search trên không gian số thực.

## 4. Thuật toán cốt lõi (Core Algorithms)
1. **Finding peaks in mountain arrays**: Sử dụng Binary Search. Nếu phần tử hiện tại nhỏ hơn phần tử bên phải, ta đang ở sườn dốc lên (đỉnh nằm bên phải). Ngược lại, ta đang ở sườn dốc xuống (đỉnh nằm bên trái hoặc chính là phần tử hiện tại).
2. **Ternary Search**: Thuật toán chia không gian tìm kiếm thành 3 phần bằng 2 điểm `m1` và `m2`. Bằng cách so sánh giá trị tại `m1` và `m2`, ta có thể loại bỏ 1/3 không gian tìm kiếm mỗi bước để tìm cực trị của hàm Unimodal.
3. **Partitioning two sorted arrays**: Để tìm phần tử thứ $k$ hoặc trung vị (Median) của hai mảng đã sắp xếp `A` và `B`, ta thực hiện Binary Search trên mảng nhỏ hơn để tìm một vách ngăn (partition). Nếu vách ngăn chia hai mảng sao cho phần tử lớn nhất bên trái nhỏ hơn hoặc bằng phần tử nhỏ nhất bên phải, ta đã tìm được lời giải.

## 5. Cách hoạt động (How It Works)
- **Tìm đỉnh (Peak Finding)**: 
  - `mid = left + (right - left) / 2`
  - Nếu `A[mid] < A[mid + 1]`, đỉnh nằm ở nửa phải: `left = mid + 1`.
  - Ngược lại, đỉnh nằm ở nửa trái (có thể là `mid`): `right = mid`.
- **Ternary Search**:
  - `m1 = left + (right - left) / 3`
  - `m2 = right - (right - left) / 3`
  - So sánh `f(m1)` và `f(m2)` để thu hẹp khoảng.
- **Phân hoạch (Partitioning)**:
  - Chọn `partitionX` trong mảng `X` (mảng ngắn hơn).
  - Từ `partitionX`, tính được `partitionY` cho mảng `Y`.
  - So sánh `maxLeftX` với `minRightY` và `maxLeftY` với `minRightX` để điều chỉnh Binary Search.

## 6. Độ phức tạp (Complexity Analysis)
- **Finding peaks**: 
  - Time Complexity: $O(\log n)$
  - Space Complexity: $O(1)$
- **Ternary Search**: 
  - Time Complexity: $O(\log_3 n)$
  - Space Complexity: $O(1)$
- **Median of Two Sorted Arrays**: 
  - Time Complexity: $O(\log(\min(m, n)))$
  - Space Complexity: $O(1)$

## 7. Ưu điểm và Nhược điểm (Pros & Cons)
**Ưu điểm:**
- Tối ưu hóa cực lớn so với thuật toán $O(n)$, đặc biệt hữu ích khi $n$ lớn (vd: $10^9$).
- Ternary Search có thể giải quyết các bài toán tối ưu hóa liên tục mà không cần đạo hàm.

**Nhược điểm:**
- Khó cài đặt chính xác do các trường hợp biên (edge cases).
- Yêu cầu điều kiện chặt chẽ về dữ liệu (mảng phải là Mountain, hàm phải là Unimodal, hoặc mảng phải sorted).

## 8. Các biến thể (Variations)
- **Finding peak in 2D grid**: Tìm đỉnh trong ma trận 2D với độ phức tạp $O(n \log m)$.
- **Binary Search on Answer**: Tìm kiếm nhị phân trên không gian kết quả (không gian thực hoặc nguyên).
- **Ternary Search trên tập rời rạc (Discrete)** và liên tục (Continuous).

## 9. Ứng dụng thực tế (Real-world Applications)
- Đồ họa máy tính: Tìm điểm cao nhất của một địa hình (Mountain array).
- Machine Learning: Tối ưu hóa hyperparameter của các hàm lồi/lõm (convex/concave) bằng Ternary Search.
- Hệ thống cơ sở dữ liệu: Trộn (merge) thống kê phân vị (percentile) từ nhiều shard dữ liệu đã sắp xếp (tương tự Median of Two Sorted Arrays).

## 10. So sánh (Comparisons)
- **Binary Search vs Ternary Search**: Binary Search loại bỏ $1/2$ không gian tìm kiếm, Ternary Search loại bỏ $1/3$. Mặc dù Ternary Search giảm không gian tìm kiếm nhanh hơn về số lượng bước, nhưng mỗi bước lại yêu cầu tính toán 2 điểm thay vì 1 điểm. Thông thường Binary Search tối ưu hơn nếu ta có thể tính đạo hàm rời rạc. Ternary Search dùng khi không thể xác định hướng đi chỉ bằng 1 điểm mid.
- **Partitioning vs Merge (Two Pointers)**: Partitioning dùng Binary Search để đạt $O(\log(\min(m,n)))$, trong khi dùng Two Pointers để merge hai mảng đạt $O(m+n)$.

## 11. Ngôn ngữ lập trình (Programming Languages Implementation - Java)
Ternary Search tìm cực đại của hàm `f(x)` trên không gian thực:
```java
public double ternarySearch(double left, double right) {
    double epsilon = 1e-9;
    while (right - left > epsilon) {
        double m1 = left + (right - left) / 3.0;
        double m2 = right - (right - left) / 3.0;
        if (f(m1) < f(m2)) {
            left = m1;
        } else {
            right = m2;
        }
    }
    return f(left);
}

private double f(double x) {
    // Hàm mục tiêu đơn cực
    return -x * x + 4 * x; 
}
```

## 12. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Thế nào là một phần tử đỉnh (Peak Element)? Làm sao để tìm nó trong mảng không sắp xếp?
2. Sự khác biệt giữa Binary Search và Ternary Search?
3. Tại sao Ternary Search không hoạt động trên mảng có nhiều đỉnh cực trị?
4. Trình bày ý tưởng giải bài toán Median of Two Sorted Arrays trong $O(\log(\min(m,n)))$.
5. Làm thế nào để tìm kiếm một phần tử trong một Mountain Array?
6. Ternary Search có thể sử dụng mảng tĩnh thay vì không gian liên tục không?
7. Trong bài Peak Element, nếu mảng chứa nhiều đỉnh, thuật toán có đảm bảo tìm ra ít nhất một đỉnh không?
8. Tại sao phải chọn chia mảng ngắn hơn khi giải bài Median of Two Sorted Arrays?
9. Nếu $m$ và $n$ là độ dài của hai mảng, bài toán K-th element of two sorted arrays có thể giải trong $O(\log k)$ không?
10. Làm thế nào để xử lý các phần tử trùng lặp khi tìm Peak Element?
11. Đánh giá độ phức tạp của việc tìm đỉnh trong Grid 2D bằng thuật toán chia để trị.
12. Có thể dùng Binary Search thay thế Ternary Search trên mảng nguyên không? Làm thế nào?
13. Tại sao trong Binary Search tìm đỉnh ta so sánh `mid` với `mid + 1`?
14. Tìm phần tử cực tiểu (Local Minimum) trong một mảng khác gì với cực đại?
15. Binary Search on Answer là gì? Hãy đưa ra một ví dụ áp dụng.
16. Trong bài toán phân hoạch 2 mảng, điều kiện dừng (termination condition) là gì?
17. Nếu mảng `X` có số phần tử bằng 0, thuật toán Partitioning xử lý như thế nào?
18. Độ lớn của `epsilon` trong Ternary Search liên tục ảnh hưởng thế nào đến kết quả?
19. Có thể mở rộng Median of K sorted arrays được không? Độ phức tạp là bao nhiêu?
20. Tại sao Ternary Search yêu cầu hàm phải là Unimodal Strictly (đơn cực ngặt)?

## 13. 20 Lỗi phổ biến (20 Common Bugs)
1. **Lỗi chia cho mảng rỗng** trong Partitioning hai mảng.
2. **Index out of bounds** khi tính `mid + 1` nếu `mid` nằm ở cuối mảng.
3. Nhầm lẫn giữa cực đại (peak) và giá trị lớn nhất (maximum).
4. Sai công thức tính chỉ số đối xứng (`m2 = right - (right - left)/3`).
5. Vòng lặp vô hạn do điều kiện `epsilon` của Ternary Search không đủ chuẩn hoặc sai số số thực.
6. Sử dụng Ternary Search trên hàm không có tính Unimodal (ví dụ đồ thị hình sin).
7. Gắn cứng (Hardcoding) vô cực (`Integer.MAX_VALUE`) mà không quan tâm khả năng tràn số trong bài Median.
8. So sánh sai trong Binary search của mảng Mountain khi có các phần tử bằng nhau liền kề.
9. Lỗi cộng tổng mảng vượt quá kích thước Integer trong thuật toán Binary Search on Answer.
10. Chọn mảng dài hơn để Binary search trong bài Median làm tăng thời gian thực thi lên $O(\log(\max(m,n)))$ và gây lỗi chỉ số.
11. Bỏ qua trường hợp mảng chỉ có 1 hoặc 2 phần tử khi tìm Peak.
12. Không điều khiển tốt hai sườn (lên, xuống) khi tìm kiếm trong Mountain Array.
13. Không phân tách logic thành 3 phần rõ ràng trong Ternary Search rời rạc.
14. `partitionY` bị âm do độ dài phân hoạch không khớp.
15. Không làm tròn phép chia số thực làm Ternary search trên số nguyên bị kẹt (infinite loop).
16. Điều kiện dừng của vòng lặp `while (left < right)` nhưng lại trả về sai giá trị cuối cùng.
17. Dùng `left + right` gây tràn bộ nhớ nguyên (Integer Overflow).
18. Không tính đến trường hợp 2 nửa ghép lại có tổng số phần tử chẵn hay lẻ trong bài toán Median.
19. Gắn biến `ans` sớm trước khi thực sự kiểm tra tính hợp lệ của phân hoạch.
20. Trả về index thay vì value hoặc ngược lại.

## 14. 30 Trường hợp biên (30 Edge Cases)
1. Tìm đỉnh trong mảng chỉ có 1 phần tử.
2. Tìm đỉnh trong mảng đã được sắp xếp tăng dần hoàn toàn (đỉnh ở phần tử cuối cùng).
3. Mảng sắp xếp giảm dần hoàn toàn (đỉnh ở phần tử đầu tiên).
4. Hai mảng có kích thước cực kỳ chênh lệch (VD: $1$ và $10^6$).
5. Giá trị Median nằm ở 2 phần tử của cùng 1 mảng.
6. Một mảng chứa toàn bộ các giá trị âm, mảng kia toàn giá trị dương.
7. Đỉnh nằm ở sát biên của hàm số trong không gian Ternary Search.
8. Mảng Mountain có đỉnh lệch hẳn về đầu mảng (VD: `[10, 9, 8, 7, 6]`).
9. Mảng Mountain có đỉnh lệch hẳn về cuối mảng (VD: `[1, 2, 3, 4, 3]`).
10. Ternary search trên hàm có một đoạn phẳng nằm ngang (không strictly unimodal).
11. Hàm mục tiêu luôn bằng hằng số.
12. Median của 2 mảng khi cả hai mảng đều rỗng (Lỗi đầu vào).
13. Trong bài K-th element, $k = 1$ (tương đương tìm giá trị nhỏ nhất).
14. K-th element khi $k = m + n$ (tìm giá trị lớn nhất).
15. Các phần tử trong mảng trùng lặp nhiều.
16. Tất cả các giá trị của mảng bằng nhau.
17. Phân hoạch (Partitioning) cắt ngay tại đầu mảng `X`.
18. Phân hoạch cắt ngay tại cuối mảng `X`.
19. Phân hoạch cắt ngay tại đầu mảng `Y`.
20. Phân hoạch cắt ngay tại cuối mảng `Y`.
21. Khi cả hai mảng có chung các giá trị tạo ra nhiều cặp Median khả thi.
22. Hàm cần tìm Ternary Search có các dao động nhiễu siêu nhỏ.
23. Phạm vi tìm kiếm chứa số thực âm, số $0$, số thực dương lẫn lộn.
24. Trong thuật toán tìm trên ma trận 2D, các cột biên có giá trị âm vô cực.
25. Thuật toán `Find in Mountain Array` với target không tồn tại.
26. Target nằm ở sườn tăng nhưng lớn hơn đỉnh.
27. Target nằm ở sườn giảm nhưng nhỏ hơn cực tiểu.
28. Target nằm ở cả sườn tăng và sườn giảm.
29. Tràn số khi $1e-9$ quá nhỏ so với khả năng biểu diễn của double.
30. Cập nhật `left = m1` thay vì `left = m1 + epsilon` gây Infinite loop.

## 15. Tối ưu hóa (Optimizations)
- **Ternary Search to Golden Section Search**: Tỉ lệ chia thay vì 1/3 - 2/3 có thể dùng Tỷ lệ vàng (Golden Ratio $\approx 0.618$) để tận dụng lại điểm đã tính trước đó, giảm số lần gọi hàm `f(x)` mỗi bước lặp từ 2 xuống 1.
- **Dùng Derivative (Đạo hàm) nếu có thể**: Nếu hàm liên tục và có thể tính đạo hàm, dùng Binary Search trên đạo hàm $f'(x) = 0$ sẽ cho hiệu suất $O(\log n)$ tốt hơn Ternary Search với hằng số nhỏ hơn.
- **Gộp Binary Search**: Trong các dạng bài Mountain Array, thay vì gọi 2 lần Binary Search riêng lẻ sau khi tìm đỉnh, có thể gộp các luồng xử lý hoặc trả về ngay khi tìm thấy.

## 16. Mẫu thiết kế (Design Patterns)
- **Strategy Pattern**: Dùng để đóng gói các loại chiến lược tìm kiếm nhị phân hoặc tam phân, cho phép thay đổi tuỳ thuộc vào dữ liệu đầu vào.
- **Template Method**: Định nghĩa bộ khung thuật toán Binary/Ternary search, để các lớp con tự triển khai phương thức `check(mid)` hoặc `evaluate(x)`.

## 17. Khung tư duy (Mental Models)
- **Tìm đỉnh (Peak)**: "Đi về phía sườn dốc cao hơn". Bất kỳ lúc nào bạn cũng đi lên, và vì mảng là hữu hạn, chắc chắn bạn sẽ chạm đỉnh.
- **Ternary Search**: Thay vì chặt làm đôi (Binary), ta chặt làm 3. So sánh 2 nhát cắt, phần nào cho kết quả tồi hơn (xa đỉnh hơn) thì ta vứt bỏ phần đó (1/3 đoạn).
- **Partitioning (Median)**: Đặt hai mảng trên cùng một trục. Việc của bạn là kẻ một đường thẳng duy nhất đi qua cả hai mảng sao cho số phần tử bên trái đường thẳng bằng tổng số phần tử bên phải.

## 18. Bài tập thực hành (Practice Problems)
- LeetCode 852: Peak Index in a Mountain Array
- LeetCode 162: Find Peak Element
- LeetCode 1095: Find in Mountain Array
- LeetCode 4: Median of Two Sorted Arrays
- GeeksforGeeks: K-th Element of Two Sorted Arrays
- Ternary Search application in Codeforces.

## 19. Mẹo và Thủ thuật (Tips & Tricks)
- Trong bài Median of 2 Sorted Arrays, luôn đảm bảo mảng đầu tiên (X) là mảng ngắn hơn. Điều này giới hạn phạm vi của Binary Search là độ dài mảng X, đảm bảo `partitionY` không bao giờ bị âm.
- Để xử lý an toàn các điểm tại biên (khi `partition == 0` hoặc `partition == length`), luôn dùng biến giả (dummy variable) lưu trữ `-Infinity` hoặc `+Infinity`.
- Khi viết Binary Search trên Mountain Array, điều kiện `A[mid] < A[mid+1]` cực kỳ hữu dụng vì nó cho biết bạn đang ở bên trái (sườn lên) của đỉnh.

## 20. Tài nguyên học tập (Learning Resources)
- [CP-Algorithms: Ternary Search](https://cp-algorithms.com/num_methods/ternary_search.html)
- [LeetCode Discuss: Binary Search 101](https://leetcode.com/discuss/general-discussion/786126/python-powerful-ultimate-binary-search-template-solved-many-problems)
- Các bài giảng MIT 6.006 về Peak Finding (Bài giảng đầu tiên của khóa học).

## 21. Lịch sử và Nguồn gốc (History & Origins)
- Thuật toán Tìm Đỉnh 1D và 2D là bài toán kinh điển thường được dạy trong các khóa Algorithm tại MIT (6.006) để làm nổi bật kỹ thuật Chia để trị (Divide and Conquer).
- Ternary Search ra đời như một biến thể rời rạc hóa của các phương pháp tìm kiếm cực tiểu (như Fibonacci Search và Golden Section Search) được áp dụng trong toán học tối ưu trước khi máy tính xuất hiện.

## 22. Xu hướng tương lai (Future Trends)
- Tự động tối ưu hóa hyperparameter trong AI bằng việc kết hợp các thuật toán Gradient-free như Ternary Search hoặc Golden Section Search trên phần cứng phân tán.
- Các bài toán dữ liệu lớn áp dụng Partitioning algorithm để query song song trung vị hoặc phân vị.

## 23. Tiêu chuẩn ngành (Industry Standards)
- Các thư viện chuẩn ít khi cung cấp trực tiếp Ternary Search hay Median of Two Arrays (ngoại trừ một số module tính toán thống kê). Developer phải tự triển khai chúng dựa trên yêu cầu đặc thù của hệ thống.
- Cần có Unit Test bao phủ tốt mọi Edge cases (nhất là trường hợp mảng rỗng, giá trị âm và trùng lặp).

## 24. Kết luận (Conclusion)
**Advanced Searching** mở rộng chân trời của việc chia nhỏ không gian tìm kiếm. Bằng cách hiểu sâu và thực hành các kỹ thuật như tìm đỉnh trong Mountain Array, Ternary Search hay phân hoạch hai mảng đã sắp xếp, bạn sẽ trang bị cho mình tư duy logic cực kỳ sắc bén, dễ dàng vượt qua các bài toán Hard trong các vòng phỏng vấn kỹ thuật của Big Tech.
