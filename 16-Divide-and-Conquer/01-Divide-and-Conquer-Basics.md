# Chia để trị (Divide and Conquer) - Cơ bản

## 1. Khái niệm cơ bản (Basic Concept)
Chia để trị (Divide and Conquer) là một kỹ thuật thiết kế thuật toán (algorithm design paradigm) hoạt động dựa trên việc chia nhỏ một bài toán phức tạp thành các bài toán con (subproblems) nhỏ hơn nhưng có cùng bản chất. Sau đó, giải quyết độc lập từng bài toán con này và kết hợp các kết quả lại để tạo thành lời giải cho bài toán ban đầu.

## 2. Mô hình Chia để trị (General Paradigm)
Mô hình Chia để trị chuẩn bao gồm 3 bước chính:
1. **Divide (Chia)**: Chia bài toán ban đầu thành các phần nhỏ hơn (subproblems). Các phần này phải đủ nhỏ để có thể dễ dàng giải quyết hoặc tiếp tục chia nhỏ.
2. **Conquer (Trị)**: Giải quyết các bài toán con một cách đệ quy (recursively). Khi kích thước bài toán con đủ nhỏ, ta đạt đến trường hợp cơ sở (base case) và có thể giải trực tiếp mà không cần đệ quy.
3. **Combine (Kết hợp)**: Gộp các lời giải của các bài toán con lại với nhau để tạo thành lời giải cuối cùng cho bài toán lớn ban đầu.

## 3. Định lý thợ chính (Master Theorem)
Định lý thợ chính cung cấp một phương pháp trực tiếp để đánh giá độ phức tạp thời gian (time complexity) cho các phương trình truy hồi (recurrence relations) phổ biến trong Divide and Conquer.

Phương trình tổng quát có dạng: `T(n) = a * T(n/b) + f(n)`
Trong đó:
- `n` là kích thước bài toán.
- `a >= 1`: Số lượng bài toán con.
- `b > 1`: Hệ số chia nhỏ kích thước (ví dụ `b=2` là chia đôi).
- `f(n)`: Chi phí cho việc phân chia (Divide) và kết hợp (Combine).

**3 trường hợp của Master Theorem:**
1. **Trường hợp 1**: Nếu $f(n) = O(n^{\log_b a - \epsilon})$ với $\epsilon > 0$, thì $T(n) = \Theta(n^{\log_b a})$. (Chi phí các node lá chiếm ưu thế).
2. **Trường hợp 2**: Nếu $f(n) = \Theta(n^{\log_b a})$, thì $T(n) = \Theta(n^{\log_b a} \log n)$. (Chi phí phân bố đều trên tất cả các mức của cây).
3. **Trường hợp 3**: Nếu $f(n) = \Omega(n^{\log_b a + \epsilon})$ với $\epsilon > 0$, và nếu $a \cdot f(n/b) \le c \cdot f(n)$ với $c < 1$ (điều kiện Regularity), thì $T(n) = \Theta(f(n))$. (Chi phí ở node gốc chiếm ưu thế).

## 4. Phương pháp cây đệ quy (Recursion Tree Method)
Phương pháp cây đệ quy là công cụ trực quan để phân tích các phương trình truy hồi, đặc biệt khi Master Theorem không thể áp dụng.
- **Bước 1**: Vẽ một cây đệ quy. Node gốc biểu diễn chi phí của toàn bộ bài toán, các nhánh biểu diễn chi phí của từng bài toán con.
- **Bước 2**: Xác định chi phí tại mỗi tầng (level) của cây.
- **Bước 3**: Xác định độ sâu (depth) của cây.
- **Bước 4**: Tính tổng chi phí của tất cả các tầng để lấy độ phức tạp thời gian tiệm cận (asymptotic time complexity).

## 5. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity (Thời gian)**: Phụ thuộc vào quá trình phân chia và kết hợp, thường được tính bằng đệ quy hoặc Master Theorem, phổ biến là $O(n \log n)$ (như Merge Sort) hoặc $O(\log n)$ (như Binary Search).
- **Space Complexity (Không gian bộ nhớ)**: Chủ yếu bị ảnh hưởng bởi Call Stack (ngăn xếp gọi hàm đệ quy). Không gian tối thiểu thường là $O(\log n)$ tương ứng với độ sâu đệ quy.

## 6. Các cấu trúc dữ liệu liên quan (Related Data Structures)
- **Arrays & Strings**: Thường dùng để chia nửa (mid) và xử lý mảng con.
- **Trees (Đặc biệt là Binary Trees)**: Bản chất của cây rất phù hợp với Divide and Conquer (duyệt cây đệ quy).
- **Matrices**: Chia ma trận thành các khối nhỏ (block matrices) như trong thuật toán Strassen.

## 7. Các kỹ thuật tối ưu hóa (Optimization Techniques)
- **Memoization (Lưu trữ kết quả)**: Nếu các bài toán con trùng lặp (overlapping subproblems), việc lưu trữ kết quả sẽ chuyển hướng bài toán sang Quy hoạch động (Dynamic Programming) để giảm thời gian từ Exponential xuống Polynomial.
- **Tail Recursion Optimization**: Thiết kế đệ quy đuôi (tail recursion) để tránh tràn bộ nhớ stack (stack overflow) trong các ngôn ngữ có hỗ trợ.
- **Cắt tỉa (Pruning)**: Dừng việc đệ quy vào các nhánh chắc chắn không chứa kết quả.
- **Chuyển sang Iterative (Vòng lặp)**: Một số thuật toán D&C có thể cài đặt bằng stack tường minh để tối ưu không gian bộ nhớ thực tế.

## 8. Ứng dụng thực tế (Real-world Applications)
- **Thuật toán sắp xếp**: Merge Sort, Quick Sort.
- **Thuật toán tìm kiếm**: Binary Search.
- **Nhân số lớn (Large Integer Multiplication)**: Thuật toán Karatsuba.
- **Nhân ma trận**: Thuật toán Strassen.
- **Tính toán hình học (Computational Geometry)**: Cặp điểm gần nhất (Closest Pair of Points), Bao lồi (Convex Hull).
- **Xử lý tín hiệu**: Biến đổi Fourier nhanh (Fast Fourier Transform - FFT).

## 9. Tại sao lại sử dụng cấu trúc này? (Why use this?)
- Khả năng xử lý các bài toán lớn bằng cách mô phỏng tự nhiên tư duy chia nhỏ vấn đề.
- **Tính toán song song (Parallelism)**: Các bài toán con hoàn toàn độc lập, rất lý tưởng để chạy song song trên multi-core processors.
- Hiệu suất vượt trội (O(N log N) so với O(N^2)) trong nhiều bài toán tìm kiếm và sắp xếp.

## 10. So sánh với Quy hoạch động và Tham lam (Comparison)
- **Chia để trị (Divide and Conquer)**: Phân tách bài toán thành các sub-problems **độc lập** (không trùng lặp). Kết hợp lại để có đáp án.
- **Quy hoạch động (Dynamic Programming)**: Áp dụng khi các sub-problems **có sự trùng lặp** (overlapping subproblems) và có tính chất cấu trúc tối ưu (optimal substructure).
- **Tham lam (Greedy)**: Lựa chọn tối ưu cục bộ ở mỗi bước, không cần giải lại toàn bộ các sub-problems. Thường nhanh hơn nhưng không phải lúc nào cũng tìm được cấu hình tối ưu toàn cục.

## 11. Các thuật toán cơ bản (Basic Algorithms)
- **Merge Sort**: Chia mảng, đệ quy sắp xếp 2 nửa, gộp lại.
- **Quick Sort**: Chọn pivot, chia thành phần lớn hơn và nhỏ hơn pivot, đệ quy sắp xếp.
- **Binary Search**: Chia đôi không gian tìm kiếm.
- **Tìm max/min**: Dùng D&C để tìm với số phép so sánh ít hơn cách lặp thông thường.

## 12. Ý nghĩa thực tiễn (Practical Significance)
- Giúp giảm độ phức tạp bài toán từ đa thức bậc cao xuống các bậc thấp hơn kết hợp hàm logarit.
- Thiết kế nền tảng cho nhiều API chuẩn trong các thư viện (như `Arrays.sort()` trong Java sử dụng TimSort - kết hợp MergeSort và InsertionSort).

## 13. Các mẫu code (Code Templates)
```java
// Mẫu chung cho Divide and Conquer
public ResultType divideAndConquer(ProblemType problem) {
    // 1. Base case
    if (problem.isSmallEnough()) {
        return solveBaseCase(problem);
    }
    
    // 2. Divide
    ProblemType[] subproblems = problem.split();
    
    // 3. Conquer
    ResultType res1 = divideAndConquer(subproblems[0]);
    ResultType res2 = divideAndConquer(subproblems[1]);
    
    // 4. Combine
    return combine(res1, res2);
}
```

## 14. Kỹ thuật Debug (Debugging Techniques)
- **In ra trạng thái đệ quy (Print trace)**: Thêm các câu lệnh print tại phần đầu của hàm đệ quy để in ra tham số hiện tại (ví dụ: `low`, `high`, mức độ đệ quy bằng indentation).
- **Kiểm tra Base Case**: Đặt breakpoint tại base case để đảm bảo nó được gọi và trả về giá trị hợp lệ.
- **Vẽ cây đệ quy thủ công**: Với các case nhỏ (N=3, 4), dùng giấy nháp vẽ lại quá trình thực thi để so sánh với kết quả code.

## 15. 20 Câu hỏi phỏng vấn phổ biến
1. Define the Divide and Conquer strategy. (Định nghĩa chiến lược Chia để trị)
2. What are the three steps in the Divide and Conquer algorithm? (Ba bước của D&C là gì?)
3. How is Divide and Conquer different from Dynamic Programming? (D&C khác DP như thế nào?)
4. Explain the Master Theorem. (Giải thích Định lý thợ chính)
5. Can Binary Search be classified as Divide and Conquer? Why? (Tìm kiếm nhị phân có phải là D&C không? Tại sao?)
6. What is the time complexity of Merge Sort? Show how to derive it. (Độ phức tạp của Merge Sort là bao nhiêu và cách chứng minh?)
7. What are the advantages of using Divide and Conquer? (Lợi ích của D&C là gì?)
8. Can we solve Fibonacci using Divide and Conquer? What is the downside? (Có thể giải Fibonacci bằng D&C không? Nhược điểm là gì?)
9. Explain Karatsuba’s multiplication algorithm. (Giải thích thuật toán nhân Karatsuba)
10. How does Quick Sort use the Divide and Conquer paradigm? (Quick Sort sử dụng D&C như thế nào?)
11. Is Divide and Conquer suitable for parallel processing? Why? (D&C có phù hợp xử lý song song không? Tại sao?)
12. How do you find the maximum and minimum of an array using Divide and Conquer in minimal comparisons? (Cách tìm Min/Max bằng D&C với số phép so sánh tối thiểu?)
13. Explain Strassen's Matrix Multiplication. (Thuật toán nhân ma trận Strassen)
14. What is the space complexity of a typical Divide and Conquer algorithm? (Độ phức tạp không gian của D&C?)
15. How do you find the median of two sorted arrays using Divide and Conquer? (Cách tìm trung vị 2 mảng đã sắp xếp)
16. How does the Fast Fourier Transform (FFT) use Divide and Conquer? (FFT dùng D&C như thế nào?)
17. What is a recursion tree and how is it used to solve recurrences? (Cây đệ quy là gì?)
18. What happens if the base case is omitted in a D&C algorithm? (Chuyện gì xảy ra nếu quên base case?)
19. How to find the closest pair of points in a plane using D&C? (Bài toán cặp điểm gần nhất)
20. Explain how to count inversions in an array using D&C. (Đếm số nghịch thế bằng D&C)

## 16. 20 Lỗi phổ biến (20 Common Bugs)
1. **Missing Base Case**: Quên xử lý điều kiện dừng, dẫn đến đệ quy vô hạn (StackOverflowError).
2. **Incorrect Base Case Logic**: Xử lý sai điều kiện dừng (VD: nhầm lẫn giữa mảng 0 phần tử và 1 phần tử).
3. **Improper Divide Logic**: Chia bài toán con sai cách (VD: `mid = (low + high) / 2` gây lặp vô hạn nếu không cẩn thận với cận).
4. **Integer Overflow in Midpoint Calculation**: Sử dụng `mid = (low + high) / 2` thay vì `mid = low + (high - low) / 2`.
5. **Overlapping Boundary Ranges**: Gọi đệ quy `f(low, mid)` và `f(mid, high)` thay vì `f(low, mid)` và `f(mid + 1, high)`, gây lặp vô hạn.
6. **Passing Wrong Parameters**: Truyền nhầm tham số (truyền `low` thay vì `mid + 1`).
7. **Modifying Shared State**: Thay đổi biến toàn cục (global variables) trong các nhánh đệ quy độc lập mà không reset.
8. **Incomplete Combination**: Quên không gộp tất cả các thành phần trong bước Combine (ví dụ trong Merge Sort quên gộp mảng dư).
9. **Returning Incorrect Results from Subproblems**: Trả về tham chiếu đối tượng (object reference) khiến các thay đổi sau đó ảnh hưởng đến kết quả trước.
10. **Memory Leak from Array Creation**: Tạo quá nhiều mảng con (Sub-arrays) mới ở mỗi bước thay vì truyền tham chiếu (pointers/indices), gây hao bộ nhớ.
11. **Off-by-one Errors**: Lỗi lệch một đơn vị ở các chỉ số biên (`high` hay `high - 1`).
12. **Assuming Subproblems are Independent when they are not**: Dùng D&C khi bài toán có tính chất trùng lặp (lẽ ra phải dùng DP).
13. **Ignoring Edge Cases (like single element array)**: Không xử lý mảng có 1 phần tử hoặc rỗng trước khi gọi đệ quy.
14. **Unoptimized Base Case Threshold**: Không dùng thuật toán đơn giản (như Insertion Sort) khi kích thước bài toán con đã quá nhỏ (dẫn đến chi phí overhead đệ quy cao).
15. **Incorrect Complexity Assumptions**: Nhầm lẫn độ phức tạp do tính sai phương trình Master Theorem.
16. **Stack Overflow for Large Inputs**: Không dự phòng trường hợp input quá lớn gây tràn call stack, ngay cả khi code đúng logic.
17. **Using Recursion for Tail-Recursive Iterative Solutions**: Không tối ưu thành vòng lặp (iterative) khi đệ quy là đệ quy đuôi (tail recursion).
18. **Not Handling Null Inputs**: Quên kiểm tra `null` ở hàm bọc (wrapper function) bên ngoài.
19. **Incorrect Data Types for Combinations**: Dùng `int` thay vì `long` khi cộng các giá trị kết quả trả về từ nhánh đệ quy (gây tràn số).
20. **Infinite Recursion on Duplicate Elements**: Logic phân chia bị kẹt khi mọi phần tử đều bằng nhau.

## 17. 30 Trường hợp biên (30 Edge Cases)
1. Mảng/Input rỗng (Empty input).
2. Input chỉ có 1 phần tử.
3. Input có 2 phần tử.
4. Mảng đã được sắp xếp tăng dần.
5. Mảng đã được sắp xếp giảm dần.
6. Mảng chứa tất cả các phần tử giống hệt nhau.
7. Mảng chứa số âm và số dương đan xen.
8. Mảng chỉ toàn số âm.
9. Mảng chỉ toàn số 0.
10. Tham số đầu vào là null.
11. Kích thước mảng lẻ (Odd length).
12. Kích thước mảng chẵn (Even length).
13. Kích thước mảng là số nguyên tố (Prime length).
14. Kích thước mảng là luỹ thừa của 2 (Power of 2).
15. Kích thước bài toán cực lớn ($10^6$ trở lên) gây quá tải Stack.
16. Tìm kiếm trong mảng có chứa các giá trị MAX_INT và MIN_INT.
17. Kết quả bước Combine vượt giới hạn số nguyên (Integer overflow).
18. Hai phần tử ở biên trái và phải (Boundaries test).
19. Thuật toán trên chuỗi ký tự dài chứa khoảng trắng.
20. Ma trận 1x1, 1xN, hoặc Nx1 trong các bài toán 2D.
21. Cấu trúc cây suy biến thành danh sách liên kết (Skewed tree).
22. Đồ thị không liên thông trong xử lý D&C đồ thị.
23. Đồ thị có chứa chu trình (Cycles) khi chia để trị (cần cẩn thận).
24. Cặp điểm trùng nhau trong bài toán Closest Pair.
25. Sự kiện thời gian trùng nhau trong các bài toán Intervals.
26. Mảng chứa các giá trị null xen kẽ.
27. Đệ quy quá sâu trên hệ thống có cấu hình bộ nhớ cấp phát thấp (Low stack memory).
28. Bài toán yêu cầu return index khi mảng chứa các giá trị bị lặp.
29. D&C trên cấu trúc dữ liệu không hỗ trợ truy cập ngẫu nhiên (O(1) random access như LinkedList).
30. D&C trên ma trận không vuông (Non-square matrix).

## 18. Tối ưu hóa bộ nhớ (Memory Optimization)
- Thay vì cấp phát mới (Allocate new arrays/objects) trong mỗi bước phân chia, hãy cố gắng truyền chỉ số (indices) đại diện cho vùng đang xử lý (in-place processing).
- Ví dụ, khi dùng Merge Sort, thay vì tạo mảng tạm nhiều lần, có thể tạo 1 mảng tạm (auxiliary array) duy nhất ở hàm ngoài cùng và truyền tham chiếu vào trong.

## 19. Các thư viện hỗ trợ (Library Support)
- Ngôn ngữ Java: Framework `ForkJoinPool` trong `java.util.concurrent` được thiết kế tối ưu cho xử lý song song các bài toán Divide and Conquer (VD: `ParallelStream`, `Arrays.parallelSort()`).
- Python: Thư viện `multiprocessing` để phân tán xử lý các sub-tasks.

## 20. Mẹo khi đi phỏng vấn (Interview Tips)
- **Nhận diện (Identify)**: Nếu bài toán có thể giải quyết bằng cách chia đôi (hoặc chia n) và độc lập giải quyết từng nửa, nghĩ ngay đến D&C.
- **Phân biệt với DP**: Nếu bạn chia bài toán ra và thấy có nhiều tính toán bị lặp lại, hãy nói với Interviewer: "Bài này có overlapping subproblems, tôi sẽ dùng DP để tối ưu".
- **Tree approach**: Nhiều bài toán về Binary Tree (như tính độ sâu, LCA) bản chất là D&C. Hãy nêu rõ đây là mô hình D&C.
- **Trình bày Time Complexity**: Luôn luôn tự tin viết ra phương trình Master Theorem hoặc giải thích bằng cây đệ quy khi trình bày độ phức tạp.

## 21. Bài tập thực hành (Practice Exercises)
(Xem chi tiết trong file bài tập)
- Maximum Subarray
- Pow(x, n)
- Majority Element
- Longest Common Prefix
- Search a 2D Matrix II

## 22. Bài toán mở rộng (Advanced Problems)
- K-th Largest Element in an Array (Quick Select).
- Count of Smaller Numbers After Self.
- Skyline Problem.
- Closest Pair of Points.

## 23. Tổng kết (Conclusion)
Chia để trị là một kỹ thuật tư duy cốt lõi trong Khoa học máy tính. Mặc dù đôi khi cách cài đặt đệ quy có thể tốn bộ nhớ stack, nhưng những thuật toán phát sinh từ mô hình này (Merge Sort, Quick Sort, Binary Search) là xương sống của mọi thư viện chuẩn. Việc nắm chắc Master Theorem giúp bạn tự tin trong việc đánh giá và thiết kế thuật toán tối ưu.

## 24. Checklist ôn tập (Review Checklist)
- [ ] Hiểu rõ 3 bước: Divide, Conquer, Combine.
- [ ] Nắm được 3 trường hợp của Master Theorem.
- [ ] Phân biệt được D&C và Dynamic Programming.
- [ ] Có thể cài đặt thành thạo đệ quy với base case đúng.
- [ ] Hiểu cách tối ưu hoá không gian bộ nhớ (In-place operations).
- [ ] Nhận biết được các bài toán dạng Tree recursion.
