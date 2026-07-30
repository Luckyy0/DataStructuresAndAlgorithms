# 01 - Backtracking: Combinations and Permutations (Tổ hợp và Hoán vị)

## 1. Giới thiệu (Introduction)
Backtracking (Quay lui) là một kỹ thuật thuật toán dựa trên Đệ quy (Recursion) để tìm tất cả (hoặc một số) giải pháp cho một bài toán tính toán, đặc biệt là các bài toán thỏa mãn ràng buộc (Constraint Satisfaction Problems). Bài học này tập trung vào các mẫu cơ bản nhất của Backtracking: Subsets (Tập con), Combinations (Tổ hợp) và Permutations (Hoán vị).

## 2. Mục đích (Purpose)
Hiểu và nắm vững Backtracking template chung, cách phân biệt và áp dụng Subsets, Combinations, và Permutations vào các bài toán thực tế. Hiểu cách xử lý các trường hợp có phần tử trùng lặp (duplicates).

## 3. Khái niệm cơ bản (Basic Concepts)
- **Backtracking**: Thử xây dựng một giải pháp từng bước một, loại bỏ (prune) những con đường không thể dẫn đến giải pháp hợp lệ.
- **State space tree (Cây không gian trạng thái)**: Biểu diễn tất cả các trạng thái có thể có của thuật toán. Backtracking duyệt qua cây này thường theo chiến lược DFS (Depth-First Search).
- **Choose, Explore, Un-choose**: 3 bước cơ bản tại mỗi node trong quá trình đệ quy.

## 4. Đặc điểm và Thuộc tính (Characteristics and Properties)
- Là cách duyệt vét cạn (Exhaustive Search) nhưng có tối ưu bằng cách cắt tỉa (Pruning).
- Độ phức tạp thời gian thường rất lớn: $O(2^N)$ cho Subsets, $O(N!)$ cho Permutations.
- Thường đi kèm với việc dùng một cấu trúc dữ liệu mảng hoặc danh sách để lưu đường đi (Path).

## 5. Phân biệt Subsets vs Combinations vs Permutations
- **Subsets (Tập con)**: Lấy một số phần tử bất kỳ từ tập hợp ban đầu. Thứ tự không quan trọng. Tập hợp con có thể rỗng hoặc chứa toàn bộ phần tử.
- **Combinations (Tổ hợp)**: Chọn $K$ phần tử từ $N$ phần tử. Thứ tự không quan trọng (ví dụ: `[1, 2]` giống `[2, 1]`). Subsets thực chất là tập hợp của tất cả các Combinations với $K$ từ 0 đến $N$.
- **Permutations (Hoán vị)**: Sắp xếp các phần tử của một tập hợp. Thứ tự là quan trọng (ví dụ: `[1, 2]` khác `[2, 1]`). Thường có độ dài bằng với mảng ban đầu (hoặc bằng $K$).

## 6. Template Backtracking Chung (General Template)
```java
private void backtrack(List<List<Integer>> result, List<Integer> tempList, int[] nums, int start) {
    if (/* điều kiện thỏa mãn bài toán */) {
        result.add(new ArrayList<>(tempList));
        return; // đôi khi không cần return nếu cần duyệt tiếp (vd: Subsets)
    }
    
    for (int i = start; i < nums.length; i++) {
        // Có thể thêm bước kiểm tra điều kiện / Pruning tại đây
        
        tempList.add(nums[i]);            // Choose (Chọn)
        backtrack(result, tempList, nums, i + 1); // Explore (Khám phá)
        tempList.remove(tempList.size() - 1);     // Un-choose (Hủy chọn)
    }
}
```

## 7. Xử lý phần tử trùng lặp (Handling Duplicates)
Khi mảng đầu vào có chứa các phần tử trùng lặp, ta cần tránh tạo ra các Subsets/Combinations/Permutations trùng lặp.
Nguyên tắc:
1. **Sort** mảng đầu vào ngay từ đầu (`Arrays.sort(nums)`).
2. **Skip** các phần tử trùng lặp trong vòng lặp `for`: `if (i > start && nums[i] == nums[i-1]) continue;`

## 8. Biểu diễn (Representation)
- Đầu vào: Mảng `nums` (có thể chứa số nguyên, ký tự, string).
- Đầu ra: Danh sách các danh sách (List of lists) chứa các phần tử thỏa mãn.

## 9. Cấu trúc dữ liệu liên quan (Related Data Structures)
- **Array / ArrayList**: Lưu trữ dữ liệu đầu vào và kết quả.
- **LinkedList / Stack**: Đóng vai trò làm `tempList` (đường đi hiện tại), nhờ thao tác thêm/xóa phần tử cuối ở $O(1)$.
- **HashSet / boolean[]**: Dùng trong Permutations để đánh dấu phần tử đã được sử dụng (visited).

## 10. Độ phức tạp (Complexity)
- **Subsets**: Thời gian $O(N \cdot 2^N)$, Không gian $O(N)$.
- **Combinations**: Thời gian $O(K \cdot C(N, K))$, Không gian $O(K)$.
- **Permutations**: Thời gian $O(N \cdot N!)$, Không gian $O(N)$.

## 11. Ứng dụng thực tế (Real-world Applications)
- Lên lịch trình, sắp xếp công việc.
- Bài toán xếp Hậu (N-Queens), Sudoku Solver.
- Sinh mật khẩu, brute-force tấn công mã hóa mật khẩu.
- Các bài toán quy hoạch tối ưu cần duyệt toàn bộ.

## 12. Ứng dụng trong hệ thống (System Applications)
- Trình biên dịch (Compilers) xử lý cú pháp.
- Tìm kiếm đường đi mạng lưới, phân tích cấu hình hệ thống (Configuration generation).
- Regular Expression Matching.

## 13. Ưu điểm (Advantages)
- Dễ hiểu và cài đặt.
- Có khả năng duyệt toàn bộ State Space, luôn tìm thấy kết quả nếu tồn tại.
- Tiết kiệm bộ nhớ (Space Complexity thấp) so với BFS.

## 14. Nhược điểm (Disadvantages)
- Chậm. Độ phức tạp theo hàm số mũ (Exponential Time).
- Rất dễ bị Time Limit Exceeded (TLE) nếu không có chiến lược Pruning hiệu quả.
- Đệ quy sâu gây tràn bộ nhớ Stack (StackOverflowError).

## 15. So sánh với các thuật toán khác (Comparison)
- **Backtracking vs DFS**: Backtracking là một dạng của DFS, tập trung vào việc lưu lại và phục hồi state (Choose -> Unchoose).
- **Backtracking vs DP (Dynamic Programming)**: DP lưu lại (memoize) kết quả của sub-problems để tránh tính toán lặp lại. Backtracking duyệt lại mọi nhánh, không lưu trữ kết quả để tái sử dụng.
- **Backtracking vs BFS**: BFS duyệt theo chiều rộng, tốn nhiều bộ nhớ để lưu Queue. Backtracking dùng Stack của hệ thống (Call Stack) nên ít tốn bộ nhớ hơn.

## 16. 20 câu hỏi phỏng vấn thực tế (20 Interview Questions)
1. Hãy viết thuật toán in ra tất cả các tập con của `[1, 2, 3]`.
2. Phân biệt Subsets và Permutations? Tại sao Permutations không dùng tham số `start` trong vòng lặp?
3. Cách tốt nhất để xử lý mảng đầu vào có chứa phần tử trùng lặp trong bài toán Combinations?
4. Viết hàm sinh tất cả hoán vị của một chuỗi ký tự.
5. Giải thích tại sao phải dùng `new ArrayList<>(tempList)` khi add vào `result`?
6. Độ phức tạp thời gian và không gian của thuật toán sinh Subsets là bao nhiêu?
7. Làm thế nào để giải bài toán Subsets bằng Bit Manipulation? So sánh với Backtracking.
8. Áp dụng Backtracking để tìm các tập con có tổng bằng $K$ (Combination Sum).
9. Nếu số lượng phần tử là lớn, làm sao để tránh TLE trong Backtracking? (Gợi ý: Pruning).
10. Làm sao sinh ra Permutation thứ $K$ mà không cần sinh hết toàn bộ?
11. Letter Combinations of a Phone Number có phải bài toán Combination hay Permutation? Tại sao?
12. Generate Parentheses sử dụng Backtracking như thế nào? Điều kiện dừng là gì?
13. Giải thuật in tất cả Palindrome Partitioning của một chuỗi.
14. Phân tích bài toán Word Search sử dụng Backtracking trên ma trận 2D.
15. Hướng giải bài toán "Beautiful Arrangement" bằng Backtracking.
16. "N-Queens" là bài toán Backtracking kinh điển. Bạn sẽ sử dụng cấu trúc dữ liệu nào để kiểm tra cột và đường chéo?
17. Backtracking có thể giải Sudoku như thế nào?
18. Nếu thay thế DFS đệ quy bằng Iterative (dùng Stack), code của bạn sẽ thay đổi như thế nào?
19. Giải thích kỹ thuật Branch and Bound, so sánh với Backtracking.
20. Bài toán Subset Sum Problem khác gì với 0/1 Knapsack Problem?

## 17. Các mẫu câu hỏi (Question Patterns)
1. **Tập con (Subsets)**: `nums` không trùng, `nums` có trùng. Lấy tất cả mọi độ dài.
2. **Tổ hợp (Combinations)**: Lấy $K$ phần tử. Hoặc Combination Sum (cho phép dùng lặp lại, hoặc mỗi phần tử 1 lần).
3. **Hoán vị (Permutations)**: Có/không có phần tử trùng. Mọi thứ tự đều được tính.
4. **Phân hoạch (Partitioning)**: Palindrome Partitioning, IP Address generation.
5. **Duyệt ma trận (Matrix/Grid Exploration)**: Word Search, Number of Islands (có backtracking nếu cần phục hồi state).

## 18. Mẫu code cơ bản (Basic Code Snippets / Templates)

### 18.1. Subsets (Không trùng)
```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    backtrack(result, new ArrayList<>(), nums, 0);
    return result;
}
private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] nums, int start) {
    result.add(new ArrayList<>(temp)); // Mọi trạng thái đều là kết quả
    for (int i = start; i < nums.length; i++) {
        temp.add(nums[i]);
        backtrack(result, temp, nums, i + 1);
        temp.remove(temp.size() - 1);
    }
}
```

### 18.2. Subsets (Có trùng)
```java
public List<List<Integer>> subsetsWithDup(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    Arrays.sort(nums); // Bắt buộc
    backtrack(result, new ArrayList<>(), nums, 0);
    return result;
}
private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] nums, int start) {
    result.add(new ArrayList<>(temp));
    for (int i = start; i < nums.length; i++) {
        if (i > start && nums[i] == nums[i-1]) continue; // Skip duplicates
        temp.add(nums[i]);
        backtrack(result, temp, nums, i + 1);
        temp.remove(temp.size() - 1);
    }
}
```

### 18.3. Permutations (Không trùng)
```java
public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    backtrack(result, new ArrayList<>(), nums, new boolean[nums.length]);
    return result;
}
private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] nums, boolean[] used) {
    if (temp.size() == nums.length) {
        result.add(new ArrayList<>(temp));
        return;
    }
    for (int i = 0; i < nums.length; i++) {
        if (used[i]) continue; // Bỏ qua phần tử đã duyệt
        used[i] = true;
        temp.add(nums[i]);
        backtrack(result, temp, nums, used);
        temp.remove(temp.size() - 1);
        used[i] = false; // Phục hồi state
    }
}
```

## 19. 20 Lỗi thường gặp (20 Common Bugs/Mistakes)
1. Thêm `tempList` tham chiếu trực tiếp vào `result` thay vì `new ArrayList<>(tempList)`.
2. Quên thao tác Un-choose (`remove` phần tử ở cuối).
3. Sử dụng `i` thay vì `i + 1` khi đệ quy cho `backtrack` trong bài toán Subsets (dẫn đến lặp vô hạn hoặc Combination Sum thay vì Subsets).
4. Sử dụng `start` thay vì `0` trong vòng lặp của Permutations.
5. Quên `Arrays.sort(nums)` khi bài toán yêu cầu xử lý duplicate.
6. Viết sai điều kiện skip duplicate: `if (nums[i] == nums[i-1])` thay vì `if (i > start && nums[i] == nums[i-1])`.
7. Dùng `remove(Object)` thay vì `remove(int index)` trong ArrayList với Integer (gây nhầm lẫn giá trị và vị trí).
8. Không có điều kiện base case hoặc điều kiện dừng bị sai, gây StackOverflow.
9. Trong Permutations với Duplicates, điều kiện skip sai: `if(used[i] || (i > 0 && nums[i] == nums[i-1] && !used[i-1])) continue;` dễ bị gõ nhầm.
10. Biến đổi biến toàn cục không đúng cách, ảnh hưởng đến các Test Cases tiếp theo.
11. Trong mảng 2D Backtracking, quên khôi phục ký tự / giá trị cũ trong ma trận.
12. Đệ quy với chuỗi String và sử dụng `+` (String concatenation) thay vì StringBuilder (Gây tốn kém bộ nhớ O(N^2)).
13. Khởi tạo mảng boolean `used` sai kích thước.
14. Pruning trễ (Cắt tỉa muộn) làm cho thuật toán bị TLE.
15. Không chú ý đến trường hợp mảng đầu vào rỗng (Empty array).
16. Nhầm lẫn giữa việc tìm "chỉnh hợp" và "tổ hợp".
17. Dùng HashSet thay cho ArrayList nhưng không hỗ trợ kiểm tra thứ tự, dẫn tới lỗi kết quả sinh ra.
18. Không return sau khi add vào kết quả trong hàm đệ quy (Nếu cần kết thúc nhánh đó).
19. Gắn giá trị hardcode cho độ dài kết quả thay vì dùng `nums.length`.
20. Dùng `List.remove(0)` làm Un-choose thay vì `List.remove(List.size() - 1)` gây độ phức tạp O(N) cho thao tác xóa.

## 20. 30 Corner Cases / Edge Cases
1. Mảng đầu vào rỗng `[]`.
2. Mảng chỉ có 1 phần tử `[1]`.
3. Mảng có tất cả các phần tử giống nhau `[2, 2, 2]`.
4. Mảng đã được sắp xếp sẵn.
5. Mảng sắp xếp ngược.
6. Bài toán Combination sum có phần tử `0` (gây đệ quy vô hạn nếu được sử dụng lại phần tử).
7. Số lượng phần tử trùng lặp rất lớn.
8. Độ dài chuỗi/mảng lớn, gây TLE.
9. Mảng có chứa số âm (ảnh hưởng việc tính Sum pruning).
10. Tham số K lớn hơn N trong Combinations.
11. Tham số K = 0.
12. Mảng chứa giá trị Integer.MAX_VALUE.
13. Yêu cầu sinh kết quả theo thứ tự từ điển (Lexicographical order).
14. Các phần tử là chuỗi dài.
15. Khi `start` bằng `nums.length` (ngoài biên).
16. Khi có nhiều nhánh đệ quy trả về các tập hợp rỗng lồng nhau.
17. Có yêu cầu kết quả không được rỗng.
18. Khi mảng 2D bị null hoặc chứa mảng con rỗng.
19. Ký tự cần backtrack chứa các loại khoảng trắng hoặc ký tự đặc biệt.
20. Bài có duplicate nhưng muốn trả về cả ID của phần tử, không chỉ giá trị.
21. Yêu cầu số phần tử chẵn lẻ trong tổ hợp.
22. Permutations với mảng có độ dài > 10 (chú ý tới N!).
23. Tìm Subsets với giới hạn tổng bộ nhớ của JVM.
24. Backtracking trong đồ thị có chu trình (Cần HashSet / visited).
25. Thuật toán có sử dụng bitmask thay mảng visited cho tiết kiệm bộ nhớ (N < 32).
26. Kết quả có số lượng List trả về cực lớn, vượt qua giới hạn `Integer.MAX_VALUE`.
27. Đệ quy không bao giờ dừng nếu điều kiện dừng phụ thuộc biến float/double (lỗi so sánh).
28. Backtrack nhiều tập hợp với tham số nhiều chiều (List của List làm param).
29. Gọi hàm đệ quy với thao tác `tempList.subList(0, ...)`.
30. Stack của máy ảo thiết lập quá thấp, gặp mảng 1000 phần tử.

## 21. Cách Debug và Troubleshoot (Debugging and Troubleshooting)
- Sử dụng thao tác in ra Console hoặc Breakpoint trước và sau các lệnh Choose và Unchoose:
  ```java
  System.out.println("Before: " + tempList);
  tempList.add(nums[i]);
  // call backtrack
  tempList.remove(tempList.size() - 1);
  System.out.println("After: " + tempList);
  ```
- Vẽ cây không gian trạng thái (State Space Tree) ra giấy cho mảng ví dụ nhỏ `[1, 2]`.
- Giám sát độ sâu Call Stack bằng một tham số `depth` truyền vào đệ quy nếu bị StackOverflowError.

## 22. Các framework / thư viện sử dụng (Frameworks/Libraries)
- Java `java.util.List`, `java.util.ArrayList`, `java.util.Arrays`.
- Một số bài toán cần `java.util.HashSet` hoặc thư viện `Guava` (Sets.combinations, Sets.permutations) nếu không bị yêu cầu code chay.

## 23. Tài liệu tham khảo (References)
- LeetCode Problems: 78 (Subsets), 90 (Subsets II), 77 (Combinations), 46 (Permutations), 47 (Permutations II).
- Sách: "Cracking the Coding Interview" - Chapter 8 (Recursion and Dynamic Programming).
- "Introduction to Algorithms" (CLRS).

## 24. Kết luận (Conclusion)
Backtracking (Tổ hợp, Tập con, Hoán vị) là nền tảng cốt lõi trong thuật toán. Việc nắm vững Template "Choose - Explore - Unchoose" sẽ giúp bạn dễ dàng mở rộng để giải quyết các bài toán duyệt cấu trúc dữ liệu phức tạp hơn như Đồ thị (Graphs), Lưới (Grid) hoặc Cây (Trees). Điểm quan trọng nhất cần nhớ là "Cắt tỉa" (Pruning) và "Quản lý state" chính xác.
