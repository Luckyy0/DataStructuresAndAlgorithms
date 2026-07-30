# 02. Merge and Inversions (Trộn và Đếm nghịch thế)

## 1. Khái niệm cơ bản (Introduction)
Kỹ thuật **Merge and Inversions** (Trộn và Đếm nghịch thế) là một ứng dụng mở rộng mạnh mẽ của thuật toán **Merge Sort** (Sắp xếp trộn). Bằng cách tận dụng quá trình kết hợp (merge) hai mảng con đã sắp xếp, chúng ta không chỉ sắp xếp mảng mà còn có thể đếm số lượng các phần tử thỏa mãn một điều kiện nhất định giữa nửa trái (left half) và nửa phải (right half). Một ví dụ kinh điển là đếm số lượng nghịch thế (Inversions), tức là các cặp `(i, j)` sao cho `i < j` và `A[i] > A[j]`.

## 2. Cách hoạt động (How it works)
Thuật toán tuân theo mô hình **Divide and Conquer** (Chia để trị):
1. **Divide**: Chia mảng thành 2 nửa (left và right).
2. **Conquer**: Đệ quy giải bài toán trên từng nửa, trả về mảng đã sắp xếp và kết quả đếm (count) nội bộ của từng nửa.
3. **Combine**: Trong quá trình merge hai nửa trái và phải đã được sắp xếp, ta duyệt qua các phần tử. Do nửa trái và nửa phải đều đã có thứ tự, ta có thể dễ dàng đếm số cặp thỏa mãn điều kiện (ví dụ `A[i] > A[j]`) một cách hàng loạt (batch counting) thay vì đếm từng cặp, từ đó giảm độ phức tạp từ $O(N^2)$ xuống $O(N \log N)$.

## 3. Ứng dụng thực tế (Real-world applications)
- **Recommendation Systems**: Đo lường sự khác biệt về sở thích giữa hai người dùng (thông qua Kendall tau distance).
- **Bioinformatics**: So sánh gene hoặc chuỗi protein.
- **Data Analytics**: Tính toán số lượng các giao dịch bất thường hoặc các sự kiện vượt ngưỡng trong chuỗi thời gian (Range sum counting).
- **Ranking Algorithms**: Đánh giá mức độ đồng thuận giữa các kết quả tìm kiếm của nhiều search engine.

## 4. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity**: $O(N \log N)$ cho cả Best, Average, và Worst case, tương đương với thuật toán Merge Sort.
- **Space Complexity**: $O(N)$ do cần mảng phụ (auxiliary array) để lưu trữ các phần tử trong quá trình trộn.
- **Tính ổn định (Stability)**: Thuật toán có thể được thiết kế để giữ tính ổn định (Stable) bằng cách ưu tiên lấy phần tử bên trái khi các giá trị bằng nhau.

## 5. Implementations (Mã nguồn)
```java
public class InversionCounter {
    public long countInversions(int[] arr) {
        if (arr == null || arr.length < 2) return 0;
        int[] temp = new int[arr.length];
        return mergeSortAndCount(arr, temp, 0, arr.length - 1);
    }

    private long mergeSortAndCount(int[] arr, int[] temp, int left, int right) {
        long count = 0;
        if (left < right) {
            int mid = left + (right - left) / 2;
            count += mergeSortAndCount(arr, temp, left, mid);
            count += mergeSortAndCount(arr, temp, mid + 1, right);
            count += mergeAndCount(arr, temp, left, mid, right);
        }
        return count;
    }

    private long mergeAndCount(int[] arr, int[] temp, int left, int mid, int right) {
        for (int i = left; i <= right; i++) {
            temp[i] = arr[i];
        }
        int i = left, j = mid + 1, k = left;
        long swaps = 0;
        while (i <= mid && j <= right) {
            if (temp[i] <= temp[j]) {
                arr[k++] = temp[i++];
            } else {
                arr[k++] = temp[j++];
                swaps += (mid + 1 - i); // Batch counting
            }
        }
        while (i <= mid) arr[k++] = temp[i++];
        return swaps;
    }
}
```

## 6. Các phép toán cơ bản (Basic operations)
- **Chia mảng (Divide)**: Xác định `mid = left + (right - left) / 2`.
- **Đếm nội bộ (Internal Count)**: Đệ quy tính số cặp thỏa mãn trong nửa trái và nửa phải.
- **Đếm chéo (Cross-boundary Count)**: Đếm số cặp `(i, j)` với `i` ở nửa trái và `j` ở nửa phải, thực hiện trong lúc merge.

## 7. So sánh với các cấu trúc dữ liệu/thuật toán khác (Comparison)
- **Vs Brute Force ($O(N^2)$)**: Merge Sort nhanh hơn nhiều với dữ liệu lớn, nhưng tốn $O(N)$ không gian.
- **Vs Fenwick Tree / Segment Tree**: Fenwick Tree có thể đếm Inversions trong $O(N \log M)$ (với $M$ là giá trị lớn nhất) hoặc $O(N \log N)$ sau khi Coordinate Compression. Merge Sort không yêu cầu compression và không phụ thuộc vào độ lớn của giá trị.
- **Vs Balanced BST**: Dùng AVL hoặc Red-Black tree sửa đổi có thể đạt $O(N \log N)$ nhưng thao tác implement phức tạp và tốn nhiều bộ nhớ hằng số (constant overhead) hơn Merge Sort.

## 8. Các biến thể (Variations)
- **Reverse Pairs**: Đếm `i < j` sao cho `A[i] > 2 * A[j]`. Yêu cầu một vòng lặp đếm riêng biệt trước khi gộp (merge) vì điều kiện này không bảo toàn thứ tự trực tiếp như Inversion.
- **Count of Smaller Numbers After Self**: Trả về một mảng trong đó `res[i]` là số phần tử bên phải nhỏ hơn `A[i]`. Cần lưu giữ thêm chỉ số gốc (original indices) của phần tử.
- **Count of Range Sum**: Đếm số mảng con có tổng nằm trong `[lower, upper]`. Đếm chéo trên mảng prefix sum (Prefix Sum Array).

## 9. Khi nào nên sử dụng (When to use)
- Cần đếm hoặc tìm các cặp có quan hệ qua lại giữa bên trái và bên phải trong một danh sách tuyến tính.
- Dữ liệu lớn và giá trị của các phần tử phân tán rộng (thay vì tập trung trong một khoảng nhỏ).
- Bài toán yêu cầu sắp xếp dữ liệu đồng thời với việc tính toán một thuộc tính nào đó.

## 10. Khi nào không nên sử dụng (When not to use)
- Khi không gian bộ nhớ (Space Complexity) bị giới hạn khắt khe (không cho phép $O(N)$ phụ trợ).
- Dữ liệu gần như đã được sắp xếp và giá trị nhỏ (có thể dùng Insertion Sort + Fenwick Tree).
- Yêu cầu cập nhật trực tuyến (Online queries) liên tục (Segment Tree/Fenwick Tree phù hợp hơn).

## 11. 20 Lỗi thường gặp (Common Bugs)
1. **Tính toán `mid` sai**: Dùng `(left + right) / 2` gây tràn số (integer overflow) thay vì `left + (right - left) / 2`.
2. **Sai lệch chỉ số mảng phụ**: Khởi tạo hoặc sao chép vào `temp` không khớp với dải `[left, right]`.
3. **Tràn số với `count`**: Dùng `int` cho biến đếm thay vì `long`, gây tràn số (Inversions có thể lên tới $N(N-1)/2$).
4. **Viết nhầm điều kiện `>=` thành `>`**: Làm mất tính ổn định (Stability) của thuật toán sắp xếp.
5. **Thiếu lệnh chép phần còn lại của mảng con trái**: Quên vòng lặp `while (i <= mid) arr[k++] = temp[i++];`.
6. **Sao chép phần tử mảng con phải (thừa)**: Quên rằng nửa phải đã nằm sẵn tại đúng vị trí trong `arr` nếu nửa trái đã cạn.
7. **Xử lý sai với Range Sum**: Không tính chỉ số cẩn thận khi sử dụng prefix sum (ví dụ quên padding 0 ở đầu).
8. **Đếm Reverse Pairs trong lúc gộp**: Tính gộp bước đếm `A[i] > 2 * A[j]` vào vòng lặp merge gây sai lệch vì điều kiện trộn và điều kiện đếm khác nhau.
9. **Mảng chứa phần tử âm**: Viết code giả định mọi phần tử là dương, dẫn đến lỗi khi chia hoặc so sánh trong các biến thể.
10. **Lỗi con trỏ (NullPointerException)**: Không kiểm tra `arr == null` hoặc `arr.length < 2` ngay từ đầu.
11. **Gán nhầm `temp[i]`**: Trong quá trình merge, sử dụng trực tiếp `arr` dẫn đến ghi đè mất dữ liệu (data overwrite).
12. **Tính số Inversions sai công thức**: Thay vì `mid - i + 1`, lại viết `mid - i` hoặc công thức khác.
13. **Khởi tạo biến đếm đệ quy sai**: Không cộng dồn `count += mergeSortAndCount()` mà chỉ lấy giá trị cuối.
14. **Giới hạn đệ quy sai (Base case)**: Thiếu điều kiện `if (left >= right) return 0;` dẫn đến Stack Overflow.
15. **Sai lầm trong Coordinate Compression**: (Khi kết hợp BST/Fenwick) nén mảng sai lệch thứ tự.
16. **Lỗi logic mảng chỉ số (Indices array)**: Ở bài `Count of Smaller Numbers`, quên swap các index tương ứng cùng với mảng chính.
17. **Cập nhật mảng tĩnh sai cách**: Sử dụng một mảng `temp` global nhưng không dọn sạch hoặc gán nhầm vùng nhớ.
18. **Không bao hàm các phần tử trùng lặp (Duplicates)**: Điều kiện đếm bỏ sót hoặc đếm dư các phần tử có giá trị bằng nhau.
19. **Bỏ quên phần tử tự ghép đôi**: Đếm chính nó với nó `(i = j)` nếu điều kiện vòng lặp viết sai `i <= j`.
20. **Lỗi tối ưu cấp phát**: Cấp phát mảng `temp` mới ở mỗi bước đệ quy thay vì tái sử dụng mảng tĩnh ban đầu, gây tốn Memory và thời gian Garbage Collection.

## 12. 30 Trường hợp biên (Edge Cases)
1. Mảng rỗng (`length == 0`).
2. Mảng có 1 phần tử (`length == 1`).
3. Mảng đã được sắp xếp tăng dần hoàn toàn (0 Inversions).
4. Mảng được sắp xếp giảm dần hoàn toàn (Max Inversions).
5. Mảng có tất cả các phần tử giống hệt nhau (0 Inversions).
6. Mảng có kích thước rất lớn (Ví dụ $10^5$ hoặc $10^6$ phần tử).
7. Mảng chỉ gồm 2 phần tử nghịch thế `[2, 1]`.
8. Mảng chứa giá trị Integer.MAX_VALUE.
9. Mảng chứa giá trị Integer.MIN_VALUE.
10. Tổng Range Sum có thể vượt quá giới hạn Long (cần kiểm tra BigInteger hoặc cẩn thận).
11. Số lượng Inversion vượt quá `Integer.MAX_VALUE` (kiểm thử kiểu `long`).
12. Mảng có kích thước chẵn.
13. Mảng có kích thước lẻ.
14. Mảng luân phiên lớn nhỏ (VD: `[5, 1, 5, 1, 5, 1]`).
15. Mảng có cấu trúc nửa đầu lớn hơn hẳn nửa sau (VD: `[10, 11, 12, 1, 2, 3]`).
16. Mảng có cấu trúc nửa sau lớn hơn hẳn nửa đầu (VD: `[1, 2, 3, 10, 11, 12]`).
17. Reverse Pairs có giá trị nhân 2 gây tràn số (VD: `A[j] * 2 > Integer.MAX_VALUE`).
18. Range Sum với `lower` = `upper`.
19. Range Sum với khoảng cách rất lớn giữa `lower` và `upper`.
20. Các phần tử xen kẽ âm dương (VD: `[-2, 5, -1, 4]`).
21. Cập nhật Inversion với số 0 (`A[i] = 0`).
22. Bài toán có mảng chứa một dải giá trị rất hẹp nhưng mảng rất dài (nhiều bản sao).
23. Bài toán yêu cầu Local Inversion bằng Global Inversion (VD: `A[i] = i + 1` trừ một vài cặp liền kề).
24. Phần tử đầu và cuối khác biệt lớn với phần còn lại của mảng.
25. Thuật toán chạy trên môi trường bộ nhớ siêu giới hạn (cần kiểm tra in-place merge, dù khó).
26. Mảng đầu vào có chứa phần tử `NaN` (nếu dùng `double`).
27. Đếm Range Sum với Prefix sum ban đầu là mảng gồm toàn các số 0.
28. Range Sum với giới hạn âm (`lower`, `upper` đều âm).
29. Trộn k mảng đã sắp xếp với kích thước mỗi mảng không đều nhau.
30. Count Smaller After Self với mảng liên tục giảm dần (kết quả mảng là `[N-1, N-2, ..., 0]`).

## 13. Các kỹ thuật tối ưu (Optimization techniques)
- **Tái sử dụng mảng phụ (Reuse auxiliary array)**: Thay vì cấp phát mảng `temp` liên tục trong mỗi hàm đệ quy, hãy cấp phát 1 mảng duy nhất từ ban đầu và truyền tham chiếu vào các hàm đệ quy.
- **Chuyển sang Insertion Sort ở mảng nhỏ**: Với mảng có kích thước dưới 15-32 phần tử, có thể dùng Insertion Sort (kết hợp đếm nghịch thế trực tiếp) để tăng tốc độ do loại bỏ hằng số gọi đệ quy.
- **Kiểm tra sơ bộ**: Nếu `arr[mid] <= arr[mid+1]`, tức là 2 mảng con đã hòa hợp, ta không cần Merge và số Inversions chéo bằng 0.

## 14. Kinh nghiệm thực tế (Practical experience)
- Luôn cẩn thận với **Integer Overflow** trong bài đếm số lượng. Mặc định dùng biến `long count` thay vì `int`.
- Khó khăn lớn nhất ở các biến thể phức tạp (như Reverse Pairs) là vòng lặp đếm phải tách khỏi vòng lặp Merge. Nếu bạn cố gắng làm đồng thời trong 1 vòng `while`, code sẽ rất rắc rối và dễ sinh bug. Hãy viết 2 vòng: 1 vòng đếm (O(N)), 1 vòng trộn (O(N)).
- Trong bài `Count of Smaller Numbers After Self`, việc theo dõi chỉ số gốc đòi hỏi một mảng các đối tượng (như `Pair<Value, OriginalIndex>`) hoặc hai mảng song song. Cẩn trọng khi swap.

## 15. Các bài toán kinh điển (Classic problems)
- Đếm số nghịch thế trong mảng (Inversion Counting).
- Trả về danh sách số lượng phần tử nhỏ hơn nằm bên phải (Count Smaller After Self).
- Lặp nghịch đảo có trọng số (Reverse Pairs).
- Đếm số chuỗi con có tổng nằm trong mảng giá trị cho trước (Count of Range Sum).
- So sánh Global Inversions và Local Inversions.

## 16. 20 Câu hỏi phỏng vấn (Interview Questions)
1. Inversion là gì? Có thể tính tổng Inversion bằng cách nào?
2. Tại sao Merge Sort thích hợp nhất để đếm Inversions so với Quick Sort?
3. Viết thuật toán O(N log N) đếm nghịch thế.
4. Điều chỉnh hàm đếm Inversions để nó vẫn giữ được tính ổn định cho Sort?
5. Sự khác biệt giữa đếm Inversion và Reverse Pairs (`A[i] > 2 * A[j]`) là gì?
6. Làm thế nào để giải `Count of Smaller Numbers After Self` bằng Divide and Conquer?
7. Giải thích cách Prefix Sum giúp giải `Count of Range Sum` kết hợp với Merge Sort.
8. Có thể giải bài đếm Inversion mà không dùng đệ quy không? (Sử dụng Bottom-Up Merge Sort).
9. Thời gian tối thiểu để đếm nghịch thế trong worst case có thể giảm dưới O(N log N) không? (Dựa trên decision tree, là Không).
10. So sánh cách đếm Inversions bằng Fenwick Tree và Merge Sort. Khi nào chọn cái nào?
11. Local Inversion là gì? Mối liên hệ giữa Local và Global Inversion?
12. Làm thế nào để biết một mảng có Global Inversion == Local Inversion chỉ trong O(N)?
13. Giải thuật Merge có đếm sót trường hợp phần tử trùng nhau không?
14. Nếu tôi chỉ có O(1) bộ nhớ phụ, tôi có thể đếm nghịch thế trong O(N log N) được không?
15. Có thể áp dụng Divide and Conquer để đếm số cặp `(i, j)` mà `i < j` và `A[i] + A[j] == K` không? (Sử dụng Two Pointers hiệu quả hơn).
16. Nếu cần đếm số nghịch thế trên danh sách liên kết đơn (Linked List), thuật toán Merge Sort có tối ưu không?
17. Cách giải bài toán `Create Sorted Array through Instructions` trên Leetcode liên quan gì đến kỹ thuật đếm này?
18. Thuật toán có áp dụng tốt trên môi trường Multithreading không? (Rất tốt do tính độc lập của Left và Right).
19. Giải thích kỹ thuật Batch Counting trong quá trình Merge.
20. Sẽ ra sao nếu biến `mid` được chọn không chia đôi mảng đều nhau (VD 1/3 và 2/3)?

## 17. Design Patterns / System Design ứng dụng
- **MapReduce**: Kỹ thuật Divide and Conquer trên mảng chia sẻ nhiều nguyên tắc với mô hình phân tán MapReduce (Map = đếm nội bộ các chunk phân tán, Reduce = đếm chéo khi gộp kết quả).
- **Concurrency / Fork-Join Framework**: Trong Java, đếm Inversions với mảng siêu lớn được hiện thực hóa qua `ForkJoinPool` để chia công việc trên nhiều luồng.

## 18. Tài liệu tham khảo (References)
- *Introduction to Algorithms (CLRS)* - Chương 2: Merge Sort và bài tập đếm Inversions.
- *Algorithm Design (Jon Kleinberg, Éva Tardos)* - Chương 5: Divide and Conquer.
- Leetcode Problems Discuss sections for Divide and Conquer approaches.

## 19. Bài tập thực hành (Practice Exercises)
- Implement lại đếm Inversion không nhìn code mẫu.
- Tự viết hàm giải Reverse Pairs và so sánh tốc độ với Binary Indexed Tree (BIT).
- Viết hàm `Count Range Sum` có in ra các đoạn con thỏa mãn để kiểm tra tính đúng đắn.

## 20. Hướng dẫn debug (Debugging guide)
- Hãy in ra mảng (hoặc mảng chỉ số) sau mỗi lần đệ quy trả về để đảm bảo nửa trái và nửa phải thực sự đã được sắp xếp.
- Kiểm tra tính đúng đắn của số `count` được cộng dồn tại từng bước Merge. Dùng một mảng nhỏ cỡ 4 phần tử tính tay và so với logs.

## 21. Lịch sử và nguồn gốc (History and origin)
John von Neumann lần đầu tiên phát minh ra thuật toán Merge Sort vào năm 1945. Việc đếm nghịch thế nổi lên như một bài toán tự nhiên trong lý thuyết sắp xếp và trong các phép thử thống kê không tham số (như thống kê Kendall tau).

## 22. Các tính chất toán học/Logic (Mathematical properties)
- Số lượng Inversions tối đa của mảng N phần tử là $\frac{N(N-1)}{2}$.
- Mỗi thao tác hoán đổi vị trí của 2 phần tử liền kề (Adjacent swap) sẽ thay đổi số Inversions của mảng đi chính xác 1 đơn vị.

## 23. Tích hợp trong các Framework/Thư viện (Framework integration)
Các hàm `.sort()` chuẩn trong Java (Timsort / MergeSort biến thể) có thể được sửa đổi để theo dõi số lượng swap/inversion cho các mục đích phân tích học máy cụ thể, mặc dù thường không được expose dưới dạng Public API.

## 24. Tổng kết (Summary)
**Merge and Inversions** cung cấp một lăng kính tuyệt vời để thấy tính đa dụng của **Divide and Conquer**. Không chỉ giới hạn ở việc thay đổi thứ tự phần tử, quá trình cấu trúc lại dữ liệu cho phép ta trích xuất những thông tin phân tích vô giá (như đếm số lượng, kiểm tra giới hạn) với chi phí thời gian rẻ hơn rất nhiều so với Brute Force. Làm chủ kỹ thuật này là chìa khóa để giải quyết một loạt các bài toán Hard trên Leetcode và trong thực tế.
