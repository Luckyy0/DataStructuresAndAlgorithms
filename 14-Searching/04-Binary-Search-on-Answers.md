# 04. Binary Search on Answers (Min-Max / Max-Min)

## 1. Khái niệm và Mục đích (Concept & Purpose)
**Binary Search on Answers** (Tìm kiếm nhị phân trên kết quả) là một biến thể nâng cao của thuật toán tìm kiếm nhị phân. Thay vì tìm kiếm một phần tử cụ thể trong một mảng đã sắp xếp, kỹ thuật này tìm kiếm **đáp án tối ưu** trong một **không gian kết quả** (search space) liên tục hoặc rời rạc thỏa mãn một điều kiện cho trước.
Mục đích chính: Giải quyết các bài toán tối ưu hóa (Optimization Problems) dạng "Minimize the Maximum" (tìm giá trị nhỏ nhất của các mức lớn nhất) hoặc "Maximize the Minimum", bằng cách chuyển chúng thành bài toán quyết định (Decision Problems).

## 2. Cấu trúc cấu thành (Components & Architecture)
1. **Search Space (`low`, `high`)**: Giới hạn dưới và giới hạn trên của kết quả có thể xảy ra.
2. **Hàm quyết định `isValid(mid)` (Predicate Function)**: Một hàm trả về kiểu `boolean` (True/False) kiểm tra xem giá trị `mid` có phải là một đáp án hợp lệ hay không. Hàm này thường sử dụng thuật toán Tham lam (Greedy) hoặc Mô phỏng (Simulation).
3. **Monotonicity (Tính đơn điệu)**: Không gian tìm kiếm phải có tính chất phân đôi rõ ràng đối với hàm `isValid`. Ví dụ:
   - Dạng 1: `F F F F T T T T` (Cần tìm giá trị `T` đầu tiên - Minimize).
   - Dạng 2: `T T T T F F F F` (Cần tìm giá trị `T` cuối cùng - Maximize).

## 3. Hoạt động bên trong (Under the Hood)
- **Bước 1**: Xác định chính xác khoảng `[low, high]`. `low` là giá trị nhỏ nhất có thể, `high` là giá trị lớn nhất có thể.
- **Bước 2**: Lặp trong khi `low <= high`. Tính `mid = low + (high - low) / 2`.
- **Bước 3**: Chạy `isValid(mid)` để kiểm tra.
  - Nếu `isValid(mid)` trả về `True` và bài toán yêu cầu tìm Min: Lưu lại `mid` (hoặc `high = mid - 1` để tìm giá trị nhỏ hơn).
  - Nếu `isValid(mid)` trả về `True` và bài toán yêu cầu tìm Max: Lưu lại `mid` (hoặc `low = mid + 1` để tìm giá trị lớn hơn).
  - Ngược lại, điều chỉnh `low` hoặc `high` tương ứng.

## 4. Phân loại (Variations/Types)
- **Min-Max Problems**: Ví dụ chia mảng thành $K$ phần sao cho tổng lớn nhất của một phần là nhỏ nhất (Split Array Largest Sum). Cấu trúc `isValid` là `F F F T T T`.
- **Max-Min Problems**: Ví dụ đặt vị trí $K$ con bò sao cho khoảng cách nhỏ nhất giữa 2 con bò là lớn nhất (Aggressive Cows). Cấu trúc `isValid` là `T T T F F F`.
- **Floating-point Binary Search**: Tìm kiếm trên tập số thực thay vì số nguyên (Cần sai số epsilon `while (high - low > 1e-6)`).
- **K-th Element in Matrix/Two Sorted Arrays**: Sử dụng Binary Search để đoán giá trị phần tử thứ K (Median of Two Sorted Arrays).

## 5. Hướng dẫn triển khai (Implementation Guide)
Mẫu code tổng quát cho bài toán **Min-Max** (Tìm giá trị nhỏ nhất thỏa mãn điều kiện - dạng `F F F T T T`):
```java
public int binarySearchOnAnswer(int[] arr, int k) {
    int low = getMinPossibleAnswer(arr);
    int high = getMaxPossibleAnswer(arr);
    int ans = -1;
    
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (isValid(arr, k, mid)) {
            ans = mid; // Có thể là đáp án, thử tìm cái nhỏ hơn
            high = mid - 1;
        } else {
            low = mid + 1; // Không thỏa mãn, cần tăng giá trị lên
        }
    }
    return ans; // Có thể trả về low tùy cấu trúc lặp
}

private boolean isValid(int[] arr, int k, int mid) {
    // Triển khai logic Tham lam / Mô phỏng ở đây
    return true; 
}
```

## 6. Định nghĩa / Terminology
- **Search Space**: Tập hợp tất cả các giá trị có thể là kết quả của bài toán.
- **Predicate**: Hàm kiểm tra tính hợp lệ của một ứng viên kết quả.
- **Monotonic Sequence**: Dãy có tính chất luôn tăng, luôn giảm, hoặc là dãy boolean chỉ đổi trạng thái đúng 1 lần (`TTT...FFF`).

## 7. Trực quan hóa (Visualization)
Với không gian kết quả và hàm `isValid`:
```text
Giá trị mid:    10   20   30   40   50   60   70   80
isValid(mid):   F    F    F    T    T    T    T    T
                               ^
                       Đáp án tối ưu (Min-Max)
```
Nếu ta thử `mid = 40`, `isValid(40) = True`. Ta thử tiếp `high = 39` để tìm `mid` nhỏ hơn. Nếu thử `mid = 20`, `isValid(20) = False`, ta thử `low = 21` để tăng `mid`.

## 8. Ưu điểm và Nhược điểm (Pros & Cons)
**Ưu điểm**:
- Giảm thiểu độ phức tạp thời gian cực kỳ mạnh mẽ. Chuyển từ duyệt tuyến tính $O(M)$ (M là kích thước search space) sang $O(\log M)$.
- Rất dễ viết nếu đã xác định được hàm `isValid()`.

**Nhược điểm**:
- Khó nhận dạng bài toán: Không phải lúc nào cũng rõ ràng để nhận ra bài toán có thể giải bằng cách này (đặc biệt là nhận ra tính đơn điệu).
- Dễ sai số ở các bài toán Floating-point (số thực).

## 9. So sánh (Comparison)
| Tiêu chí | Linear Search on Answer | Binary Search on Answer | Dynamic Programming |
|----------|-------------------------|-------------------------|---------------------|
| Độ phức tạp | $O(M \times N)$ | $O(\log M \times N)$ | $O(N^2)$ hoặc hơn |
| Tính đơn điệu | Không cần thiết | Bắt buộc phải có | Không cần |
| Ứng dụng | Không gian kết quả nhỏ | Không gian kết quả lớn | Tối ưu với bài có cấu trúc lặp con |

## 10. Độ phức tạp (Complexity Analysis)
- **Time Complexity**: $O(N \log M)$, trong đó $M$ là kích thước của không gian kết quả (`high - low`), và $O(N)$ là thời gian để chạy hàm `isValid`.
- **Space Complexity**: $O(1)$ (nếu `isValid` chỉ duyệt mảng) hoặc $O(N)$ tùy thuộc vào logic của `isValid`.

## 11. 20 câu hỏi phỏng vấn (20 Interview Questions)
1. Kỹ thuật Binary Search on Answers là gì?
2. Khi nào nên áp dụng Binary Search on Answers?
3. Làm sao để xác định bounds `low` và `high` hợp lý?
4. Điều kiện tiên quyết của hàm `isValid` là gì? (Tính đơn điệu)
5. Hãy phân tích bài toán Koko Eating Bananas. Hàm `isValid` và search space ở đây là gì?
6. Làm sao để xử lý overflow khi tính tổng hoặc khi tính `mid` trong bài toán Allocate Books?
7. Sự khác biệt giữa bài toán Min-Max và Max-Min khi xác định điểm cập nhật `low` và `high`?
8. Tại sao trong tìm Max-Min, khi `isValid` trả về True ta lại làm `low = mid + 1`?
9. Nếu không gian tìm kiếm là số thực thì điều kiện dừng của while loop là gì?
10. Tại sao `low = max(arr)` và `high = sum(arr)` thường là boundaries cho bài toán chia mảng liên tiếp?
11. Đánh giá độ phức tạp của bài Split Array Largest Sum giải bằng DP so với BS on Answers.
12. Có thể sử dụng Binary Search on Answers cho mảng chưa sắp xếp không? (Có, vì ta BS trên "Answers" chứ không phải trên mảng dữ liệu đầu vào).
13. Làm sao để đếm số phần tử nhỏ hơn hoặc bằng một giá trị trong một ma trận đã sắp xếp dòng và cột?
14. Trong bài Median of Two Sorted Arrays, tại sao lại dùng Binary Search on array's cut thay vì answer space?
15. Khi `isValid` tốn $O(N \log N)$ thời gian thì tổng thời gian là bao nhiêu?
16. Có thể BS on Answers cho các bài toán đồ thị như tìm đường đi có cạnh lớn nhất là nhỏ nhất không? (Có, kết hợp với BFS/DFS trong `isValid`).
17. Greedy strategy trong hàm `isValid` của bài Minimum Number of Days to Make m Bouquets hoạt động ra sao?
18. Trình bày bài Aggressive Cows.
19. Nếu hàm `isValid` không có tính đơn điệu thì sao?
20. Nếu có nhiều đáp án thỏa mãn thì BS on Answers sẽ trả về đáp án nào?

## 12. Best Practices
- **Xác định hàm `isValid` trước**: Đừng vội viết Binary Search. Hãy nghĩ xem "Nếu tôi đưa cho bạn đáp án là X, bạn có thể kiểm tra xem X có hợp lệ không với độ phức tạp $O(N)$?".
- **Luôn kiểm tra tính đơn điệu**: Hãy thử nghiệm bằng tay xem hàm `isValid` có trả về dạng `F F F T T T` hoặc `T T T F F F` hay không.
- **Tính toán cẩn thận `low` và `high`**: `low` quá thấp hoặc `high` quá cao có thể dẫn đến tràn số (Overflow) khi tính `mid` hoặc dư thừa số vòng lặp không cần thiết.

## 13. Các lỗi thường gặp (20 Common Bugs)
1. `high` không đủ lớn để bao phủ tất cả đáp án.
2. `low` bắt đầu từ 0 thay vì giá trị khả dĩ nhỏ nhất (ví dụ mảng có phần tử âm, hoặc phải lớn hơn max(arr)).
3. Tràn số nguyên `Integer Overflow` khi tính tổng các phần tử để gán cho `high`.
4. Tràn số khi tính `mid = (low + high) / 2`. Cần dùng `low + (high - low) / 2`.
5. Infinite loop do cập nhật `low = mid` thay vì `low = mid + 1` (khi tìm Max-Min, cần đổi cách tính `mid = low + (high - low + 1) / 2`).
6. Hàm `isValid` có lỗi logic, dẫn đến kết quả không đơn điệu.
7. Sai điều kiện `low < high` thay vì `low <= high` và không ghi lại kết quả `ans`.
8. Trả về `low` thay vì trả về giá trị biến `ans` đã lưu trữ (rủi ro nếu cấu trúc lặp không chính xác).
9. Trong `isValid`, tính toán biến tích luỹ bị tràn số (dùng `long` thay vì `int`).
10. Bài toán số thực, điều kiện while là `low <= high` thay vì `high - low > 1e-7`.
11. Bỏ qua các ràng buộc bài toán dẫn đến không thể có đáp án (ví dụ số phần cần chia nhiều hơn số phần tử mảng).
12. Đếm sai số cụm (clusters/groups) trong hàm `isValid`.
13. Nhầm lẫn giữa các cấu trúc `T T T F F F` với `F F F T T T`.
14. Không xử lý mảng có giá trị âm.
15. Tính `mid` là số nguyên trong khi đáp án yêu cầu số thực.
16. Nhầm lẫn giữa `Math.ceil` và phép chia nguyên thông thường.
17. Viết `isValid` với thời gian thực thi quá chậm.
18. Không return ngay khi biến điều kiện trong `isValid` bị vi phạm để tối ưu thời gian.
19. Gán sai giá trị mặc định cho `ans` (không thuộc search space).
20. Trả về đáp án sai khi mảng đầu vào rỗng hoặc kích thước không hợp lệ.

## 14. Edge Cases (30 Edge Cases)
1. Mảng có 1 phần tử.
2. Mảng đã được sắp xếp tăng dần.
3. Mảng được sắp xếp giảm dần.
4. Mảng toàn các phần tử bằng nhau.
5. `k = 1` (số nhóm/số ngày bằng 1).
6. `k = n` (số nhóm bằng số phần tử).
7. `k > n` (yêu cầu vô lý, cần return -1 ngay từ đầu).
8. Tổng của mảng rất lớn (Overflow `int`, cần `long`).
9. Các phần tử mảng có giá trị rất lớn.
10. Đáp án nằm ở biên `low`.
11. Đáp án nằm ở biên `high`.
12. Hàm `isValid` trả về `True` cho mọi giá trị.
13. Hàm `isValid` trả về `False` cho mọi giá trị (không có đáp án).
14. Các phần tử mảng có giá trị là 0.
15. Mảng có cả giá trị âm và dương (nếu bài toán cho phép).
16. Tìm kiếm trên khoảng cách, khoảng cách nhỏ nhất = 0.
17. Tốc độ tìm kiếm bằng 0 (lỗi chia cho 0).
18. Phân số trong quá trình chia ra kết quả làm tròn sai.
19. Bài toán yêu cầu sai số `10^-5` nhưng dùng số nguyên.
20. `low` và `high` âm.
21. Cập nhật `high = mid` thay vì `mid - 1` dẫn đến infinite loop ở khoảng cách 1.
22. Bài toán không đảm bảo tính liên tục của dữ liệu.
23. `isValid` yêu cầu duyệt nhiều lần trên mảng.
24. Sử dụng mảng 2D cho Binary search.
25. Mảng đầu vào có các giá trị lớn bằng `Integer.MAX_VALUE`.
26. Mảng rất lớn `N = 10^5`.
27. Đếm số cụm trong khi các phần tử đứng rời rạc.
28. Thời gian chênh lệch khi dùng mảng so với ArrayList trong Java.
29. Cần tối ưu bằng Sliding Window thay vì Binary Search On Answer (chỉ dùng nếu monotonic).
30. Dùng `double` thay vì `int` cho search space, số lần lặp thường cố định (VD: 100 lần lặp).

## 15. Tính ứng dụng (Real-world Applications)
- **Tối ưu hóa tài nguyên**: Lập lịch thực thi với chi phí nhỏ nhất (Load Balancing, Task Scheduling).
- **Thiết kế mạng**: Tìm đường truyền mạng có băng thông cổ chai lớn nhất.
- **Logistics & Vận tải**: Tìm dung tích tàu tối thiểu để chuyển hết hàng hóa trong thời gian quy định.
- **Quản lý sản xuất**: Phân bổ nhân công để hoàn thành quy trình sản xuất nhanh nhất.

## 16. Code Mẫu (Sample Code)
Giải quyết bài "Koko Eating Bananas" (Tìm Min-Max): Koko thích ăn chuối, mỗi giờ ăn được $K$ quả. Tìm $K$ nhỏ nhất để Koko ăn hết các nải chuối trong $H$ giờ.
```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int low = 1;
        int high = Integer.MIN_VALUE;
        for (int pile : piles) {
            high = Math.max(high, pile);
        }
        
        int ans = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canEatAll(piles, mid, h)) {
                ans = mid; // Có thể ăn hết, thử tìm tốc độ chậm hơn (nhỏ hơn)
                high = mid - 1;
            } else {
                low = mid + 1; // Không kịp, phải tăng tốc độ
            }
        }
        return ans;
    }
    
    private boolean canEatAll(int[] piles, int speed, int h) {
        long hoursNeeded = 0;
        for (int pile : piles) {
            // Tính số giờ = Math.ceil(pile / speed)
            hoursNeeded += pile / speed;
            if (pile % speed != 0) {
                hoursNeeded++;
            }
        }
        return hoursNeeded <= h;
    }
}
```

## 17. Tối ưu hóa (Optimization Tips)
- Giới hạn không gian tìm kiếm `[low, high]` càng hẹp càng tốt.
- Trong hàm `isValid`, nếu điều kiện đã bị phá vỡ (ví dụ tính tổng thời gian vừa vượt quá `H`), hãy **return false ngay lập tức** thay vì duyệt hết mảng.
- Tối ưu các phép toán bên trong `isValid` (sử dụng phép chia nguyên, cộng dồn thay vì gọi các hàm thư viện phức tạp như `Math.ceil`).

## 18. Công cụ & Thư viện (Tools & Libraries)
- Ngôn ngữ Java không có thư viện cụ thể trực tiếp hỗ trợ Binary Search on Answers, cần phải tự viết tay theo khuôn mẫu (Template) trên.
- Sử dụng `Long.MAX_VALUE` để tránh tràn số cho `high` khi không chắc chắn.

## 19. Tương lai (Future Trends)
Các bài toán phỏng vấn ngày càng phức tạp hóa phần logic của hàm `isValid`, kết hợp cùng BFS/DFS, Two Pointers, hoặc Dynamic Programming để kiểm tra kết quả `mid`.

## 20. Câu chuyện thực tế (Case Studies)
- Giao hàng bằng xe tải: Amazon tính toán số chuyến xe và dung tải từng xe để đảm bảo hàng nghìn gói hàng đến kho trong $D$ ngày. Search space là dung tích xe, predicate là mô phỏng quá trình chất hàng hóa vào xe. Thuật toán này chạy trong micro giây thay vì phải rà soát mọi dung tích xe bằng hệ thống AI.

## 21. Bài tập thực hành (Practice Exercises)
1. LeetCode 875: Koko Eating Bananas
2. LeetCode 1011: Capacity To Ship Packages Within D Days
3. LeetCode 410: Split Array Largest Sum
4. LeetCode 1482: Minimum Number of Days to Make m Bouquets
5. Spoj: AGGRCOW - Aggressive cows
6. LeetCode 774: Minimize Max Distance to Gas Station (Số thực)

## 22. Phụ lục (Appendix/Cheatsheet)
- **Template Min-Max (Tìm giá trị nhỏ nhất làm cho `isValid` == true):** `F F F T T T` -> Nếu `isValid(mid)`, lưu `ans = mid`, `high = mid - 1`. Ngược lại `low = mid + 1`.
- **Template Max-Min (Tìm giá trị lớn nhất làm cho `isValid` == true):** `T T T F F F` -> Nếu `isValid(mid)`, lưu `ans = mid`, `low = mid + 1`. Ngược lại `high = mid - 1`.

## 23. Tài liệu tham khảo (References)
- CP-Algorithms: Binary Search
- LeetCode Discuss: "Binary Search 101"

## 24. Checklist kiểm tra (Quality Checklist)
- [x] Đã xác định rõ yêu cầu hàm `isValid`.
- [x] Không gian tìm kiếm có tính đơn điệu không?
- [x] Đã xử lý Integer Overflow ở `mid` và trong `isValid`?
- [x] Hàm `isValid` có chạy trong $O(N)$ hoặc nhanh hơn không?
- [x] Đã test với mảng kích thước 1 và dữ liệu lớn nhất chưa?
