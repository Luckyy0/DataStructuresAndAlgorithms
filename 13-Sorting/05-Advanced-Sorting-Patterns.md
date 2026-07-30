# 05 - Các mẫu Sắp xếp Nâng cao (Advanced Sorting Patterns)

## 1. Tiêu đề
Các mẫu sắp xếp nâng cao: Cyclic Sort (Sắp xếp theo chu trình), Custom Sorting với Comparators (Sắp xếp tùy chỉnh), và Thuật toán Minimum Swaps to Sort (Số lần hoán đổi tối thiểu để sắp xếp).

## 2. Giới thiệu (Introduction)
Các thuật toán sắp xếp thông thường (Quick Sort, Merge Sort) giải quyết các bài toán sắp xếp tổng quát với độ phức tạp $O(N \log N)$. Tuy nhiên, trong phỏng vấn thuật toán, thường có các bài toán yêu cầu tận dụng tính chất đặc biệt của tập dữ liệu hoặc tiêu chí sắp xếp hoàn toàn mới. Nắm vững **Cyclic Sort**, **Custom Comparators**, và **Minimum Swaps** giúp tối ưu hóa thời gian chạy từ $O(N \log N)$ xuống $O(N)$ trong một số trường hợp hoặc xử lý được những cấu trúc dữ liệu phức tạp linh hoạt hơn.

## 3. Khái niệm cốt lõi (Core Concepts)
- **Cyclic Sort**: Khi một mảng có kích thước $N$ chứa các số liên tiếp từ $1$ đến $N$ (hoặc $0$ đến $N$), ta có thể tận dụng mảng như một Hash Map in-place. Vị trí đúng của số $k$ chính là $index = k - 1$.
- **Custom Comparator**: Khi tiêu chí lớn/nhỏ không phụ thuộc vào giá trị tự nhiên (ví dụ: ghép hai số thành chuỗi lớn nhất `a+b` vs `b+a`), ta cần định nghĩa lại hàm `compare(a, b)`.
- **Minimum Swaps to Sort**: Tính số lượng lần đổi chỗ tối thiểu để một mảng trở về dạng tăng dần hoặc giảm dần bằng cách xem mảng như một đồ thị các chu trình (cycles graph).

## 4. Cách thức hoạt động (How it works)
- **Cyclic Sort**: Duyệt qua mảng `nums`. Nếu `nums[i]` không nằm đúng vị trí của nó (tức là `nums[i] != nums[nums[i] - 1]`), ta hoán đổi `nums[i]` với phần tử tại vị trí đúng của nó. Nếu đúng rồi, ta xét tiếp phần tử kế tiếp (`i++`).
- **Custom Comparator**: Triển khai `java.util.Comparator` và viết logic vào hàm `compare(o1, o2)`. Trả về số âm nếu `o1` đứng trước `o2`, số 0 nếu bằng nhau, và số dương nếu `o1` đứng sau `o2`.
- **Minimum Swaps**: Sắp xếp một mảng bản sao để biết vị trí đích. Khởi tạo mảng `visited[]`. Tại mỗi đỉnh chưa thăm, theo dấu chu trình theo vị trí thực và vị trí đích. Một chu trình độ dài $L$ sẽ cần $L - 1$ lần hoán đổi.

## 5. Đặc điểm và Thuộc tính (Characteristics and Properties)
- **Thời gian (Cyclic Sort)**: $O(N)$ do mỗi phần tử tối đa bị hoán đổi một lần về đúng chỗ.
- **Không gian (Cyclic Sort)**: $O(1)$, hoàn toàn In-place.
- **Thời gian (Custom Sort)**: Thường phụ thuộc vào thuật toán sắp xếp dưới nền của ngôn ngữ, ví dụ Timsort (trong Java) chạy trong $O(N \log N)$.
- **Minimum Swaps**: Thời gian $O(N \log N)$ để tìm vị trí đích hoặc $O(N)$ nếu tận dụng được giá trị (vd $1..N$). Không gian phụ $O(N)$ để theo dõi visited hoặc Hash Map.

## 6. Phân loại
- *Dựa trên tính chất chỉ số (Index-based)*: Cyclic Sort.
- *Dựa trên tiêu chí so sánh (Comparison-based)*: Custom Object Sorting, Multiple Field Sorting.
- *Dựa trên đồ thị (Graph-based)*: Minimum Swaps to Sort.

## 7. Ứng dụng thực tế (Real-world Applications)
- **Hệ điều hành**: Tìm PID thấp nhất còn trống (First Missing Positive).
- **Frontend / UI**: Sắp xếp danh sách hiển thị với nhiều tiêu chí (ví dụ: Tên sản phẩm, sau đó là Giá, sau đó là Rating).
- **Hệ thống phân tán**: Lập lịch, log analysis, thứ tự ưu tiên phức hợp cho các tiến trình (Reorder logs in files).
- **Nén/Phân tích dữ liệu**: Tính toán độ tương đồng giữa hai tập dữ liệu thông qua Minimum Swaps.

## 8. Mã nguồn minh họa (Code Implementation - Java)

**Cyclic Sort Skeleton:**
```java
public void cyclicSort(int[] nums) {
    int i = 0;
    while (i < nums.length) {
        int correctIdx = nums[i] - 1; // Giả sử mảng chứa 1..N
        if (nums[i] > 0 && nums[i] <= nums.length && nums[i] != nums[correctIdx]) {
            // Swap nums[i] và nums[correctIdx]
            int temp = nums[i];
            nums[i] = nums[correctIdx];
            nums[correctIdx] = temp;
        } else {
            i++;
        }
    }
}
```

**Custom Comparator Skeleton:**
```java
Arrays.sort(arr, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        String order1 = a + b;
        String order2 = b + a;
        return order2.compareTo(order1); // Sắp xếp giảm dần để tạo Largest Number
    }
});
// Hoặc bằng Lambda:
// Arrays.sort(arr, (a, b) -> (b + a).compareTo(a + b));
```

## 9. Phân tích độ phức tạp (Complexity Analysis)
- **Cyclic Sort**: Mặc dù có hai vòng lặp lồng nhau (while bên ngoài, nhưng việc tăng `i` có thể dừng để swap liên tục), số lần swap tối đa trên toàn mảng không bao giờ vượt quá $N$. Do đó tổng số phép tính bị giới hạn ở $O(N)$. Không gian $O(1)$.
- **Custom Sort**: So sánh $2$ đối tượng tốn $O(K)$ (với $K$ là kích thước đối tượng, vd độ dài chuỗi). Thuật toán sort chạy $O(N \log N)$. Suy ra thời gian có thể là $O(K \cdot N \log N)$.
- **Minimum Swaps**: Thuật toán sắp xếp bản sao tốn $O(N \log N)$. Đếm chu trình tốn $O(N)$. Tổng cộng thời gian $O(N \log N)$ và không gian $O(N)$.

## 10. So sánh với các thuật toán khác (Comparison)
- **Cyclic Sort vs Hashing**: Cyclic Sort không tốn kém không gian bộ nhớ (In-place) trong khi Hash Map tốn $O(N)$ space.
- **Custom Sort vs Comparable**: `Comparable` gắn chết (hardcode) vào class đối tượng (natural ordering), trong khi `Comparator` cho phép định nghĩa các tiêu chí so sánh (custom ordering) riêng lẻ linh hoạt từ bên ngoài.

## 11. Nhận diện Pattern (Algorithmic Patterns)
- **Dấu hiệu Cyclic Sort**: Đề bài cho mảng chứa số nguyên từ $1$ đến $N$ hoặc $0$ đến $N$. Cần tìm các số bị mất, số lặp, hoặc số dương nhỏ nhất thiếu sót trong mảng. Yêu cầu làm in-place với thời gian $O(N)$.
- **Dấu hiệu Custom Comparator**: Cần kết hợp hai trường/biến số trở lên để xếp hạng, hoặc có logic ghép chuỗi, logic toán học tuỳ chỉnh.
- **Dấu hiệu Minimum Swaps**: Hỏi cụ thể về "số lần hoán đổi ít nhất" hoặc bài toán cho phép hoán đổi vị trí tùy ý.

## 12. 20 Câu hỏi phỏng vấn phổ biến (Interview Questions)
1. Cyclic Sort xử lý các phần tử ngoài khoảng $1..N$ như thế nào?
2. Có thể sử dụng Cyclic Sort nếu có phần tử lặp lại không? Nó tránh infinite loop ra sao?
3. Khi nào nên dùng Lambda so với Anonymous Class khi viết Comparator?
4. Định nghĩa `Stable Sort` là gì? Hàm `Arrays.sort()` của Java có stable không?
5. Gặp lỗi "Comparison method violates its general contract" trong Java là do đâu?
6. Làm thế nào để giải bài `First Missing Positive` bằng Cyclic Sort?
7. Minimum Swaps để phân loại mảng nhị phân có khác biệt gì với Minimum Swaps mảng số nguyên thường?
8. Kỹ thuật đếm "Cycles" có thể áp dụng cho việc tìm permutation nhỏ nhất không?
9. Cyclic Sort cho mảng từ $0$ đến $N$ khác biệt thế nào với mảng từ $1$ đến $N$?
10. Sắp xếp mảng log `Reorder Data in Log Files` tốn bao nhiêu thời gian và không gian?
11. Giải thích chi tiết điều kiện kiểm tra hoán đổi `nums[i] != nums[nums[i]-1]`.
12. Có thể tối ưu Minimum Swaps xuống $O(N)$ nếu chúng ta đã biết mảng gốc là tập hoán vị $1..N$ không?
13. Chaining comparators sử dụng `Comparator.comparing().thenComparing()` trong Java 8 hoạt động ra sao?
14. Kỹ thuật đánh dấu (Negation marking) có thể thay thế Cyclic Sort ở những bài toán nào?
15. Tại sao `Largest Number` không dùng được toán tử sắp xếp mặc định của chuỗi?
16. Số lần Swap tối đa cho mảng $N$ phần tử bằng Cyclic Sort là bao nhiêu?
17. Sự nguy hiểm của việc trừ thẳng số nguyên `a - b` trong custom comparator.
18. Đảo ngược chuỗi (Reverse order) Comparator trong Java như thế nào một cách an toàn?
19. First Missing Positive có chạy được nếu mảng có cả số âm và số $0$ không?
20. Bài toán tính Minimum swaps để đưa mảng về mảng khác thay vì mảng sắp xếp.

## 13. 20 Lỗi thường gặp (Common Bugs)
1. Quên tăng `i++` khi điều kiện không thoả trong Cyclic Sort, gây infinite loop (Vòng lặp vô hạn).
2. Swap sai giá trị (Ví dụ: hoán đổi `i` và `nums[i]` thay vì `nums[i]` và `nums[nums[i]-1]`).
3. Ngoại lệ OutOfBounds khi phần tử `nums[i]` lớn hơn $N$ hoặc là số âm nhưng không có điều kiện `nums[i] <= N && nums[i] > 0`.
4. Viết `a - b` trong `Comparator.compare` gây lỗi tràn số (Integer Overflow).
5. Không kiểm tra phần tử trùng lặp trước khi swap (`nums[i] == nums[correctIdx]`) làm vòng lặp chết.
6. Dùng `==` thay vì `.equals()` để so sánh đối tượng String trong custom Comparator.
7. Vi phạm tính chất bắt cầu (Transitivity) khi định nghĩa Custom Comparator làm văng `IllegalArgumentException`.
8. Comparator không bao giờ trả về $0$ kể cả khi hai object bằng nhau, phá vỡ hợp đồng của Comparator.
9. NullPointerException khi gọi phương thức của object trong Comparator khi field bằng null.
10. Quên xử lý trường hợp Leading Zeros (ví dụ `[0, 0]`) trong bài Largest Number sinh ra chuỗi `"00"` thay vì `"0"`.
11. Đếm chu trình (cycles) trong Min Swaps bị sai khi đánh dấu visited sai vị trí.
12. Quên không bảo toàn (duplicate) mảng ban đầu nếu đề bài cấm sửa mảng gốc trong tính Minimum swaps.
13. Nhầm lẫn index 1-based và 0-based khi tính toán correctIdx trong Cyclic Sort.
14. Custom Comparator sắp xếp nhưng không trả về đúng $-1$ hoặc $1$ đối với trường hợp so sánh nghịch chiều.
15. Reorder Log Files phân rã chuỗi bằng khoảng trắng `.split(" ", 2)` nhưng giới hạn mảng trả về sai, làm thiếu vế sau.
16. Tính sai vị trí mảng `0` trong mảng chứa số từ $0 \dots N$ (VD bài Missing Number).
17. Dùng Primitive Types (`int`, `double`) nhưng gọi custom comparator (Chỉ hỗ trợ Reference Types như `Integer`, `Double`).
18. Không quan tâm Stability khi mảng bị sort qua nhiều Comparator độc lập mà không dùng `thenComparing`.
19. Nhầm lẫn giữa thuật toán hoán đổi (Swapping algorithm) đồ thị vô hướng và đồ thị có hướng (Cycle decomposition graph là có hướng/song ánh).
20. Cyclic sort sai vị trí cập nhật index của hoán vị dẫn đến mất (override) dữ liệu phần tử đang xét.

## 14. 30 Trường hợp kiểm thử (Edge Cases)
1. Mảng rỗng (`[]`).
2. Mảng có $1$ phần tử.
3. Mảng đã được sắp xếp tăng dần hoàn hảo.
4. Mảng đã sắp xếp giảm dần hoàn hảo.
5. Mảng có các phần tử trùng lặp liên tiếp nhau.
6. Mảng có các phần tử trùng lặp xen kẽ nhau.
7. Mảng chứa số cực lớn `Integer.MAX_VALUE`.
8. Mảng chứa số âm nhỏ nhất `Integer.MIN_VALUE`.
9. First missing positive toàn số nhỏ hơn $0$.
10. First missing positive chỉ toàn số lớn hơn $N$.
11. Cyclic Sort có phần tử thiếu là phần tử đầu tiên ($1$).
12. Cyclic Sort có phần tử thiếu là phần tử cuối cùng ($N$).
13. Largest number với mảng chỉ có toàn $0$ (cần trả về `"0"`).
14. Largest number với mảng chứa các số giống phần đầu (ví dụ: `3`, `30`, `34`).
15. Reorder Data in Log files với toàn bộ là letter logs.
16. Log files chỉ toàn là digit logs (cần giữ nguyên thứ tự gốc).
17. Hai letter logs có nội dung y hệt nhau, phải so sánh bằng identifier.
18. Hai letter logs có nội dung y hệt nhau, identifier cũng bằng nhau (ít khi xảy ra nhưng cần robust).
19. Minimum Swaps với mảng cần đúng 1 swap cho một chu trình cực dài (Kích thước N).
20. Minimum Swaps với mảng có nhiều chu trình rời rạc có kích thước 2.
21. Custom Sort String khi Pattern chứa ký tự không có trong chuỗi nguồn.
22. Custom Sort String khi chuỗi nguồn có nhiều ký tự không nằm trong Pattern (cần nối vào cuối).
23. Sort object theo 3 criteria (Ví dụ: Score giảm dần, Tên tăng dần, ID tăng dần).
24. Mảng cực dài nhưng dải giá trị rất nhỏ (Performance test).
25. Mảng có Null object xen kẽ (cần Null-safe comparator).
26. Mảng các chuỗi cần sắp xếp độ dài dài hơn bộ nhớ RAM cho phép (External Custom Sort).
27. Cyclic Sort trong mảng 0-indexed chứa phần tử đúng bằng độ dài mảng $N$.
28. Tính chu trình swap nhưng đầu vào đã bị sai lệch thành mảng không phải là Permutation (chứa duplicate).
29. Trị số $0$ đứng đầu trong log identifiers.
30. Dùng Comparator cho `Double.NaN` hoặc `Double.POSITIVE_INFINITY`.

## 15. Các biến thể (Variations)
- **Multiple Missing Numbers**: Tìm $K$ số bị thiếu trong mảng thay vì 1.
- **Find all Duplicates**: Mảng $1..N$ nhưng có các số lặp $2$ lần, tìm tất cả số lặp (In-place).
- **Sort Array By Parity / Custom Grouping**: Dồn các số chẵn sang trái, số lẻ sang phải, áp dụng logic Custom thay vì Comparable.

## 16. Best Practices
- Sử dụng `Integer.compare(a, b)` thay vì `a - b` để ngăn tràn số nguyên.
- Kết hợp `Comparator.comparing()` và `thenComparing()` trong Java >= 8 để xây dựng logic rõ ràng và dễ đọc.
- Luôn kiểm tra điều kiện `nums[i] > 0 && nums[i] <= nums.length` trong Cyclic Sort trước khi gọi mảng `nums[nums[i]-1]`.
- Dùng logic `continue;` hoặc `i++` rõ ràng trong vòng lặp while của Cyclic Sort để tránh loop vô tận.
- Viết Comparator cẩn thận với Null values, có thể dùng `Comparator.nullsFirst()` hoặc `Comparator.nullsLast()`.

## 17. Mẹo và Thủ thuật (Tips & Tricks)
- Trong bài `Largest Number`, khi so sánh kiểu chuỗi, hãy nhớ `String.compareTo()` mặc định là sắp xếp từ điển (Lexicographical). Chèn logic `(b+a).compareTo(a+b)` là thủ thuật vàng.
- Để làm các bài Cyclic Sort in-place mà vẫn nhớ dữ liệu, có kỹ thuật **Mark by Negation** (đánh dấu bằng số âm) kết hợp. Ví dụ đi qua `v = Math.abs(nums[i])`, ta sửa `nums[v-1] *= -1`. Những index có giá trị dương khi duyệt lại là những index bị thiếu.
- Đối với Minimum swaps, sử dụng $O(N)$ Space HashMap `value -> correct_index` để theo dõi các đỉnh chu trình mà không cần mảng thứ hai (tuy nhiên dùng array sẽ nhanh hơn).

## 18. Trải nghiệm thực tế (Developer Experience)
Thuật toán Cyclic Sort ban đầu trông giống như một dạng mảng băm khó hiểu, nhưng một khi Developer "nhìn thấy" pattern $1..N$, nó mang lại sự thú vị lớn trong lúc giải quyết giới hạn không gian $O(1)$. Custom Comparators lại là kỹ năng cực kỳ thiết yếu hàng ngày (trong Backend Data Processing) khi thao tác với Collections.

## 19. Mối quan hệ với các chủ đề khác
- **Hash Table / Array Marking**: Cùng chia sẻ kỹ năng sử dụng chỉ số mảng làm khóa ẩn (Implicit Keys).
- **Graph / DFS**: Minimum Swaps là ứng dụng trực tiếp của việc tìm liên thông / chu trình (Connected Components / Cycles) trong đồ thị có hướng.
- **Greedy**: Khi swap in-place để đưa phần tử về đúng chỗ cũng chính là một nước đi tham lam cục bộ.

## 20. Tài nguyên học thêm (Further Reading)
- [Java Comparator Interface documentation](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html)
- Các bài giảng về Permutation Cycles trên hệ thống thi đấu thuật toán (Codeforces, CP-Algorithms).

## 21. Bài tập thực hành (Practice Exercises)
Vui lòng tham khảo File: `05-Advanced-Sorting-Patterns-Problems.md` để xem danh sách $30$ bài tập chọn lọc kèm mã nguồn Java cho Top $10$ bài.

## 22. Câu hỏi trắc nghiệm (Quiz/MCQs)
1. Độ phức tạp không gian (Space Complexity) của thuật toán Cyclic Sort chuẩn là:
    A. $O(N)$
    B. $O(\log N)$
    C. $O(1)$
    D. $O(N^2)$
   *(Đáp án đúng: C)*
2. Biểu thức đúng nhất khi viết Comparator ghép 2 chuỗi để tạo số lớn nhất giảm dần:
    A. `(a + b).compareTo(b + a)`
    B. `(b + a).compareTo(a + b)`
    C. `a.compareTo(b)`
    D. Cần tự viết vòng lặp `for`.
   *(Đáp án đúng: B)*

## 23. Tóm tắt (Summary)
- **Cyclic Sort**: Công cụ "vàng" cho mảng số từ $1$ đến $N$ muốn tìm missing, duplicate với $O(N)$ Time & $O(1)$ Space.
- **Custom Sort**: "Vũ khí" thao tác đối tượng, đa field, logic kết hợp độc đáo. Luôn cẩn trọng với Interger Overflow và Stability.
- **Minimum Swaps**: Phân tích dữ liệu như một đồ thị chu trình để đếm số thao tác đổi chỗ ít nhất. $Swaps = N - Cycles$.

## 24. Phụ lục (Appendix)
**Ghi nhớ hợp đồng của Custom Comparator:**
- Tính đối xứng (Symmetry): `sgn(compare(x, y)) == -sgn(compare(y, x))`.
- Tính bắc cầu (Transitivity): Nếu `compare(x, y) > 0` và `compare(y, z) > 0`, thì `compare(x, z) > 0`.
- Tính nhất quán với Equality: `compare(x, y) == 0` ngụ ý rằng `sgn(compare(x, z)) == sgn(compare(y, z))` đối với mọi `z`.
Lỗi vi phạm hợp đồng này thường gây lỗi Runtime ngoại lệ thảm khốc trong môi trường Production.
