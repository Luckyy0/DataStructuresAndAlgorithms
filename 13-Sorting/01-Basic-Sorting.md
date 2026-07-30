# 01. Basic Sorting (Các thuật toán sắp xếp cơ bản)

## 1. Tổng quan về Basic Sorting
Basic Sorting bao gồm các thuật toán cơ bản, đơn giản nhất để sắp xếp một danh sách các phần tử theo thứ tự tăng dần hoặc giảm dần. Mặc dù không tối ưu cho dữ liệu lớn, chúng rất quan trọng trong việc xây dựng tư duy thuật toán và thường được sử dụng làm nền tảng cho nhiều chủ đề phức tạp khác.

## 2. Bubble Sort (Sắp xếp nổi bọt)
Bubble Sort hoạt động bằng cách lặp qua mảng nhiều lần, so sánh các cặp phần tử kề nhau và hoán đổi chúng nếu chúng sai thứ tự. Quá trình này tiếp tục cho đến khi không cần hoán đổi nữa.

## 3. Selection Sort (Sắp xếp chọn)
Selection Sort chia mảng thành hai phần: đã sắp xếp và chưa sắp xếp. Nó liên tục tìm phần tử nhỏ nhất (hoặc lớn nhất) từ phần chưa sắp xếp và đưa nó vào cuối phần đã sắp xếp.

## 4. Insertion Sort (Sắp xếp chèn)
Insertion Sort xây dựng mảng kết quả từng phần tử một bằng cách chèn phần tử hiện tại vào vị trí thích hợp trong phần mảng đã được sắp xếp trước đó. Giống như cách bạn sắp xếp các lá bài trên tay.

## 5. Stable vs Unstable Sorting
- **Stable Sort (Sắp xếp ổn định):** Các phần tử có giá trị bằng nhau sẽ giữ nguyên thứ tự tương đối của chúng trước và sau khi sắp xếp.
  - Bubble Sort và Insertion Sort là thuật toán ổn định.
- **Unstable Sort (Sắp xếp không ổn định):** Các phần tử có giá trị bằng nhau có thể bị thay đổi thứ tự tương đối.
  - Selection Sort là thuật toán không ổn định.

## 6. Khái niệm In-place (Sắp xếp tại chỗ)
Thuật toán In-place là thuật toán biến đổi cấu trúc dữ liệu bằng một lượng bộ nhớ bổ sung rất nhỏ, thường là O(1) space complexity. Cả 3 thuật toán Bubble, Selection và Insertion Sort đều là thuật toán In-place.

## 7. Cài đặt Bubble Sort (Java)
```java
public void bubbleSort(int[] arr) {
    int n = arr.length;
    boolean swapped;
    for (int i = 0; i < n - 1; i++) {
        swapped = false;
        for (int j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
                swapped = true;
            }
        }
        if (!swapped) break;
    }
}
```

## 8. Cài đặt Selection Sort (Java)
```java
public void selectionSort(int[] arr) {
    int n = arr.length;
    for (int i = 0; i < n - 1; i++) {
        int min_idx = i;
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[min_idx]) {
                min_idx = j;
            }
        }
        int temp = arr[min_idx];
        arr[min_idx] = arr[i];
        arr[i] = temp;
    }
}
```

## 9. Cài đặt Insertion Sort (Java)
```java
public void insertionSort(int[] arr) {
    int n = arr.length;
    for (int i = 1; i < n; ++i) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j = j - 1;
        }
        arr[j + 1] = key;
    }
}
```

## 10. Đánh giá độ phức tạp (Time & Space Complexity)
- **Time Complexity:** O(N^2) cho cả 3 thuật toán trong trường hợp xấu nhất (Worst Case) và trung bình (Average Case). Trong Best Case (Mảng đã sắp xếp), Bubble Sort tối ưu và Insertion Sort đạt O(N).
- **Space Complexity:** O(1) do chỉ cần vài biến lưu trữ tạm thời (In-place).

## 11. Ưu điểm và Nhược điểm
- **Ưu điểm:** Cài đặt rất đơn giản, không tốn thêm bộ nhớ (In-place).
- **Nhược điểm:** Hiệu năng quá chậm trên các bộ dữ liệu lớn do độ phức tạp thời gian bậc 2 (Quadratic time).

## 12. Ứng dụng thực tế
Dùng cho tập dữ liệu rất nhỏ, hoặc mảng gần như đã được sắp xếp sẵn (đối với Insertion Sort). Thường được tích hợp như thuật toán fallback khi dữ liệu bị giảm kích thước trong Timsort hoặc IntroSort.

## 13. Các biến thể
- Biến thể của Bubble: Cocktail Shaker Sort.
- Biến thể của Insertion: Shell Sort.
- Biến thể của Selection: Heap Sort (sử dụng cấu trúc dữ liệu Heap để tối ưu hóa việc lấy Min/Max).

## 14. So sánh chi tiết
Insertion Sort thường nhanh hơn Bubble Sort và Selection Sort trong thực tế với các tập dữ liệu nhỏ nhờ việc giảm thiểu số phép gán so với thao tác hoán vị và có thể dừng sớm (break) ngay khi tìm được vị trí phù hợp.

## 15. Thiết kế và Pattern phân tích
Pattern nhận dạng Basic Sorting là duyệt mảng với hai vòng lặp lồng nhau (Two Nested Loops). Nếu thấy yêu cầu không sử dụng bộ nhớ phụ trợ trên mảng nhỏ, cân nhắc cách hoạt động của Basic Sort.

## 16. Phân tích không gian bộ nhớ
Vì bộ nhớ phân bổ là O(1) Allocation, Basic Sorting rất thân thiện với cache (Cache-friendly), hạn chế Page Fault trên các hệ điều hành hiện đại.

## 17. Gỡ lỗi (Debugging Guide)
Thường xảy ra lỗi Index Out of Bounds khi truy cập vòng lặp cuối cùng. Hãy sử dụng Logging bên trong vòng lặp lồng nhau để theo dõi sự di chuyển của phần tử đang được đánh giá.

## 18. 30 Edge Cases thường gặp
1. Mảng rỗng (Empty array).
2. Mảng chỉ có 1 phần tử.
3. Mảng đã được sắp xếp hoàn toàn (Sorted array).
4. Mảng sắp xếp ngược chiều (Reverse sorted array).
5. Mảng có tất cả các phần tử giống hệt nhau.
6. Mảng có nhiều phần tử trùng lặp.
7. Mảng chứa số âm.
8. Mảng chứa toàn số âm.
9. Mảng có giá trị hỗn hợp âm, dương, số 0.
10. Số lượng phần tử cực lớn.
11. Mảng chỉ gồm các số rất lớn (Integer.MAX_VALUE).
12. Mảng chứa giá trị min của số nguyên (Integer.MIN_VALUE).
13. Mảng gồm các số luân phiên lớn nhỏ (Wiggle).
14. Mảng gần như đã sắp xếp (chỉ vài phần tử sai vị trí).
15. Số lớn nhất nằm ở đầu mảng.
16. Số nhỏ nhất nằm ở cuối mảng.
17. Kích thước bộ dữ liệu vượt ngưỡng cache dòng (Cache Line).
18. Các mảng song song cần sắp xếp dựa trên một khóa duy nhất.
19. Mảng đối tượng chứa giá trị Null.
20. Mảng chứa đối tượng không cài đặt interface Comparable.
21. Phần tử cực trị gây tràn số nguyên (Integer Overflow) khi sử dụng phép trừ để so sánh.
22. Độ dài mảng vượt ngưỡng cho phép của mảng chuẩn trong ngôn ngữ.
23. Sắp xếp các chuỗi ký tự rất dài bằng Basic Sort.
24. Mảng chứa số thực Double.NaN hoặc Positive/Negative Infinity.
25. Mảng có tính đối xứng đảo ngược (Palindromic unsorted).
26. Sắp xếp trên danh sách liên kết thay vì mảng cấp phát liên tục.
27. Sắp xếp mảng Object phức hợp dựa vào một thuộc tính Id.
28. Chỉ thực hiện sắp xếp một phần (Sub-array) của mảng.
29. Cập nhật In-place trên mảng đang bị đa luồng (Thread concurrency) tác động.
30. Lỗi Buffer Overflow do các ngôn ngữ không có bộ kiểm tra biên (C/C++).

## 19. 20 Common Bugs phổ biến
1. `ArrayIndexOutOfBoundsException` trong vòng lặp trong cùng.
2. Sai điều kiện lặp (Ví dụ: `< n` thay vì `< n-1` ở vòng lặp ngoài).
3. Sử dụng sai chỉ số hoán vị trong Selection Sort.
4. Quên reset cờ `swapped = false` bên trong vòng lặp của Bubble Sort.
5. Cập nhật `arr[j+1]` bị ghi đè dữ liệu lỗi trong Insertion Sort.
6. Hoán vị bị sai logic khi biến `temp` được gán không đúng thứ tự.
7. Điều kiện so sánh sai toán tử (`<` thay vì `>`) gây kết quả sắp xếp bị ngược.
8. So sánh trực tiếp chuỗi hoặc Object bằng toán tử `==` thay vì phương thức `compareTo()`.
9. Khuyết điều kiện `j >= 0` trong vòng lặp của Insertion Sort dẫn đến truy cập Index âm.
10. Quá trình chọn `min_idx` bị lưu giá trị cũ của vòng lặp trước do quên gán lại bằng `i`.
11. Vòng lặp của Bubble Sort duyệt đến `n-1` thay vì tối ưu duyệt tới `n-i-1`.
12. Vòng lặp `while` trở thành vô tận nếu điều kiện thoát không được thay đổi.
13. Gửi tham số bằng Pass-by-value (ở một số ngôn ngữ) khiến mảng gốc không hề thay đổi.
14. Sử dụng đệ quy cho Basic Sort với dữ liệu lớn gây tràn Stack (`StackOverflowError`).
15. Trong Insertion Sort, sai lệnh khi dùng lệnh swap thay vì dời vị trí phần tử.
16. Không cài đặt lệnh `break` để thoát sớm (Early Exit) trong Bubble Sort.
17. Dùng phép XOR hoán vị cho cùng một index `arr[i]` và `arr[j]` khi `i=j` khiến giá trị biến thành 0.
18. Selection Sort hoán vị ngay lập tức khi tìm được giá trị nhỏ hơn thay vì đợi quét hết phần chưa sắp xếp.
19. Lỗi tương thích kiểu biến (Type mismatch hoặc Unchecked Cast) với Generics.
20. Tràn số nguyên (Integer Overflow) khi lập trình tự định nghĩa hàm so sánh.

## 20. 20 Interview Questions
1. Phân tích sự khác biệt cơ bản giữa Bubble Sort, Selection Sort và Insertion Sort.
2. Khi nào nên dùng Insertion Sort thay vì Quick Sort?
3. Tính ổn định (Stability) là gì? Tại sao Selection Sort lại Unstable?
4. Khái niệm In-place sorting mang lại lợi ích gì trong kỹ thuật phần mềm?
5. Làm cách nào để tối ưu thuật toán Bubble Sort?
6. Việc sử dụng Insertion Sort trên cấu trúc Linked List có khả thi không? Đánh giá hiệu suất.
7. Giải thích sự khác biệt giữa Best Case và Worst Case của Insertion Sort.
8. Tại sao Insertion Sort lại cho hiệu suất rất tốt đối với các mảng gần như đã sắp xếp?
9. Tại sao Selection Sort có tổng số lượng lần hoán đổi (Swaps) ít hơn Bubble Sort?
10. Bạn có thể triển khai Bubble Sort bằng phương pháp đệ quy được không?
11. Có thủ thuật nào để làm cho Selection Sort trở thành thuật toán Stable không?
12. Trong 3 thuật toán Basic, thuật toán nào thực hiện ít thao tác ghi bộ nhớ (Memory Writes) nhất?
13. Thuật toán nào nên dùng khi chi phí so sánh (Comparison) rẻ nhưng chi phí hoán đổi (Swap) rất tốn kém?
14. Basic Sorting có thể song song hóa (Parallelizable) không? Trình bày qua Odd-Even Sort.
15. Shell Sort dựa trên cải tiến từ thuật toán Basic Sort nào?
16. Nếu mảng đầu vào đã được xếp ngược, Basic Sort nào sẽ chạy lâu nhất? Tại sao?
17. Bạn có thể xây dựng một thuật toán In-place với Time Complexity O(N) và Space Complexity O(1) không (cần thỏa mãn điều kiện đặc biệt gì)?
18. Sự khác biệt giữa "Comparison-based Sort" (như Basic Sort) và thuật toán Sorting phân phối (như Counting Sort) là gì?
19. Giải thích ảnh hưởng của Branch Prediction trên CPU đối với thời gian chạy thực tế của các Basic Sorting.
20. Trình bày cơ chế mà Timsort (sử dụng trong Python, Java) gọi đến Insertion Sort trong quy trình làm việc của nó.

## 21. Thực hành
Hãy áp dụng mã nguồn trên cho các bài toán phân loại theo nhãn "Sort" với mức Easy trên các hệ thống LeetCode, HackerRank.

## 22. Mẹo và Kinh nghiệm
Hãy luôn tránh sử dụng Basic Sort trong môi trường mã nguồn Production. Chúng chỉ mang tính chất giáo dục hoặc sử dụng khi mảng có số lượng phần tử cực kỳ nhỏ (ví dụ, dưới 10 - 16 phần tử).

## 23. Mẫu Design Template
Khi thiết kế giải thuật sắp xếp, việc tách thao tác hoán đổi thành một hàm phụ trợ dạng `swap(arr, i, j)` sẽ làm cho cấu trúc mã nguồn sáng sủa và dễ bảo trì hơn rất nhiều.

## 24. Tổng kết
Basic Sort tuy không có tính ứng dụng cao đối với bộ dữ liệu lớn thực tế, nhưng nó là cánh cửa tuyệt vời để làm quen với Control Flow, Time/Space Complexity, Stability, và các tư duy về Memory Layout trong Khoa học máy tính.
