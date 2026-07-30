# 30 Bài tập Backtracking trên Grids (Problems)

## 1. Word Search
**Đề bài chi tiết:** Cho một ma trận các ký tự `m x n` và một chuỗi `word`. Trả về `true` nếu `word` tồn tại trong ma trận. Chuỗi phải được tạo từ các ký tự kề nhau theo hướng ngang hoặc dọc. Không được sử dụng cùng một ô chữ cái hai lần.
**Phân tích thuật toán:** Duyệt qua mọi ô. Từ mỗi ô, thực hiện DFS Backtracking để match từng ký tự của `word`. Dùng biến in-place thay đổi ô thành `#` để đánh dấu đã thăm.
**Độ phức tạp:** Time: $O(M \times N \times 3^L)$ với $L$ là độ dài từ. Space: $O(L)$ cho call stack.

**Mã nguồn Java:**
```java
class Solution {
    public boolean exist(char[][] board, String word) {
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (dfs(board, i, j, word, 0)) return true;
            }
        }
        return false;
    }
    
    private boolean dfs(char[][] board, int i, int j, String word, int index) {
        if (index == word.length()) return true;
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] != word.charAt(index)) return false;
        
        char temp = board[i][j];
        board[i][j] = '#'; // Mark
        
        boolean found = dfs(board, i+1, j, word, index+1) || 
                        dfs(board, i-1, j, word, index+1) || 
                        dfs(board, i, j+1, word, index+1) || 
                        dfs(board, i, j-1, word, index+1);
                        
        board[i][j] = temp; // Unmark
        return found;
    }
}
```

## 2. Word Search II
**Đề bài chi tiết:** Cho một ma trận ký tự `board` và danh sách các từ `words`, tìm tất cả các từ có trong ma trận.
**Phân tích thuật toán:** Tương tự Word Search nhưng thay vì gọi DFS cho từng từ, ta xây dựng một Trie từ danh sách `words`. Đi DFS trên grid và duyệt qua Trie đồng thời. Nếu gặp node trong Trie đánh dấu kết thúc từ, thêm vào kết quả.
**Độ phức tạp:** Time: $O(M \times N \times 3^{L_{max}})$, Space: $O(K)$ cho kích thước Trie (K là tổng số ký tự).

**Mã nguồn Java:**
```java
class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word;
    }
    
    public List<String> findWords(char[][] board, String[] words) {
        List<String> res = new ArrayList<>();
        TrieNode root = buildTrie(words);
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                dfs (board, i, j, root, res);
            }
        }
        return res;
    }
    
    public void dfs(char[][] board, int i, int j, TrieNode p, List<String> res) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length) return;
        char c = board[i][j];
        if (c == '#' || p.children[c - 'a'] == null) return;
        
        p = p.children[c - 'a'];
        if (p.word != null) {
            res.add(p.word);
            p.word = null; // Tránh duplicate
        }
        
        board[i][j] = '#';
        dfs(board, i-1, j, p, res);
        dfs(board, i+1, j, p, res);
        dfs(board, i, j-1, p, res);
        dfs(board, i, j+1, p, res);
        board[i][j] = c;
    }
    
    public TrieNode buildTrie(String[] words) {
        TrieNode root = new TrieNode();
        for (String w : words) {
            TrieNode p = root;
            for (char c : w.toCharArray()) {
                int i = c - 'a';
                if (p.children[i] == null) p.children[i] = new TrieNode();
                p = p.children[i];
            }
            p.word = w;
        }
        return root;
    }
}
```

## 3. N-Queens
**Đề bài chi tiết:** Đặt `n` quân hậu trên bàn cờ `n x n` sao cho không quân nào tấn công quân nào. Trả về tất cả các cách sắp xếp có thể.
**Phân tích thuật toán:** Backtracking theo từng hàng (row). Ở mỗi hàng, thử đặt Hậu ở các cột. Dùng các vòng lặp hoặc mảng phụ trợ để kiểm tra điều kiện validation an toàn của hàng dọc, đường chéo $O(1)$ hay $O(N)$.
**Độ phức tạp:** Time: $O(N!)$, Space: $O(N^2)$ lưu kết quả.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> res = new ArrayList<>();
        char[][] board = new char[n][n];
        for(int i = 0; i < n; i++) Arrays.fill(board[i], '.');
        backtrack(res, board, 0, n);
        return res;
    }
    
    private void backtrack(List<List<String>> res, char[][] board, int row, int n) {
        if (row == n) {
            res.add(construct(board));
            return;
        }
        for (int col = 0; col < n; col++) {
            if (isValid(board, row, col, n)) {
                board[row][col] = 'Q';
                backtrack(res, board, row + 1, n);
                board[row][col] = '.';
            }
        }
    }
    
    private boolean isValid(char[][] board, int row, int col, int n) {
        for(int i = 0; i < row; i++) if (board[i][col] == 'Q') return false;
        for(int i=row-1, j=col-1; i>=0 && j>=0; i--, j--) if (board[i][j] == 'Q') return false;
        for(int i=row-1, j=col+1; i>=0 && j<n; i--, j++) if (board[i][j] == 'Q') return false;
        return true;
    }
    
    private List<String> construct(char[][] board) {
        List<String> res = new ArrayList<>();
        for (char[] row : board) res.add(new String(row));
        return res;
    }
}
```

## 4. N-Queens II
**Đề bài chi tiết:** Tương tự N-Queens, nhưng chỉ cần trả về **số lượng** các cách đặt thoả mãn.
**Phân tích thuật toán:** Dùng Backtracking tối ưu với 3 mảng boolean (hoặc Bitmask) để theo dõi các cột, đường chéo chính (id = row - col), đường chéo phụ (id = row + col).
**Độ phức tạp:** Time: $O(N!)$, Space: $O(N)$ cho call stack và bộ nhớ theo dõi.

**Mã nguồn Java:**
```java
class Solution {
    int count = 0;
    public int totalNQueens(int n) {
        boolean[] cols = new boolean[n];
        boolean[] d1 = new boolean[2 * n];
        boolean[] d2 = new boolean[2 * n];
        backtrack(0, n, cols, d1, d2);
        return count;
    }
    
    private void backtrack(int row, int n, boolean[] cols, boolean[] d1, boolean[] d2) {
        if (row == n) {
            count++;
            return;
        }
        for (int col = 0; col < n; col++) {
            int id1 = col - row + n;
            int id2 = col + row;
            if (cols[col] || d1[id1] || d2[id2]) continue;
            
            cols[col] = true; d1[id1] = true; d2[id2] = true;
            backtrack(row + 1, n, cols, d1, d2);
            cols[col] = false; d1[id1] = false; d2[id2] = false;
        }
    }
}
```

## 5. Sudoku Solver
**Đề bài chi tiết:** Giải bài toán điền Sudoku trên lưới 9x9.
**Phân tích thuật toán:** Duyệt qua các ô trống (được đánh dấu là '.'). Tại mỗi ô, thử các giá trị từ '1' đến '9'. Kiểm tra tính hợp lệ ở hàng, cột và sub-box 3x3. Nếu hợp lệ, đi tiếp đệ quy. Nếu sai, backtrack trả về '.'.
**Độ phức tạp:** Time: $O(9^{81})$ cực đại, nhưng được cắt nhánh lớn. Space: $O(81) \approx O(1)$ stack.

**Mã nguồn Java:**
```java
class Solution {
    public void solveSudoku(char[][] board) {
        solve(board);
    }
    
    private boolean solve(char[][] board) {
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (board[i][j] == '.') {
                    for (char c = '1'; c <= '9'; c++) {
                        if (isValid(board, i, j, c)) {
                            board[i][j] = c;
                            if (solve(board)) return true;
                            board[i][j] = '.';
                        }
                    }
                    return false;
                }
            }
        }
        return true;
    }
    
    private boolean isValid(char[][] board, int row, int col, char c) {
        for (int i = 0; i < 9; i++) {
            if (board[row][i] == c) return false;
            if (board[i][col] == c) return false;
            if (board[3 * (row / 3) + i / 3][3 * (col / 3) + i % 3] == c) return false;
        }
        return true;
    }
}
```

## 6. Valid Sudoku
**Đề bài chi tiết:** Kiểm tra xem bảng Sudoku hiện tại có hợp lệ không (không xét đến có giải được hay không).
**Phân tích thuật toán:** Sử dụng HashSet hoặc mảng để lưu lại sự xuất hiện của các chữ số trên mỗi hàng, mỗi cột và mỗi sub-box. Đây là bước kiểm tra Validation cho Sudoku.
**Độ phức tạp:** Time: $O(1)$ vì bảng luôn 9x9. Space: $O(1)$.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        HashSet<String> seen = new HashSet<>();
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                char number = board[i][j];
                if (number != '.') {
                    if (!seen.add(number + " in row " + i) ||
                        !seen.add(number + " in column " + j) ||
                        !seen.add(number + " in block " + i/3 + "-" + j/3))
                        return false;
                }
            }
        }
        return true;
    }
}
```

## 7. Unique Paths III
**Đề bài chi tiết:** Cho một ma trận, '1' (Bắt đầu), '2' (Kết thúc), '0' (Ô trống), '-1' (Vật cản). Tìm số lượng đường đi qua **tất cả** ô trống chính xác 1 lần.
**Phân tích thuật toán:** Tìm vị trí bắt đầu và đếm số lượng ô trống. DFS qua 4 hướng. Khi đến đích, check xem có thăm đủ mọi ô trống không.
**Độ phức tạp:** Time: $O(3^{N})$, $N$ là số ô trống. Space: $O(N)$ call stack.

**Mã nguồn Java:**
```java
class Solution {
    int res = 0, empty = 1, sx, sy;
    public int uniquePathsIII(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 0) empty++;
                else if (grid[i][j] == 1) { sx = i; sy = j; }
            }
        }
        dfs(grid, sx, sy, 0);
        return res;
    }
    
    private void dfs(int[][] grid, int r, int c, int count) {
        if (r < 0 || r >= grid.length || c < 0 || c >= grid[0].length || grid[r][c] < 0) return;
        if (grid[r][c] == 2) {
            if (count == empty) res++;
            return;
        }
        grid[r][c] = -1; // Mark visited
        dfs(grid, r+1, c, count+1);
        dfs(grid, r-1, c, count+1);
        dfs(grid, r, c+1, count+1);
        dfs(grid, r, c-1, count+1);
        grid[r][c] = 0; // Backtrack
    }
}
```

## 8. Path with Maximum Gold
**Đề bài chi tiết:** Trong mỏ vàng, tìm đường đi thu được nhiều vàng nhất. Không được dẫm ô 0 vàng và đi lùi ô cũ.
**Phân tích thuật toán:** Thử xuất phát DFS từ tất cả các ô có vàng. Tính lượng vàng cộng dồn lớn nhất ở mọi nhánh dfs.
**Độ phức tạp:** Time: $O(K \times 3^K)$, với $K$ là số ô có vàng. Space: $O(K)$.

**Mã nguồn Java:**
```java
class Solution {
    public int getMaximumGold(int[][] grid) {
        int maxGold = 0;
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] > 0) {
                    maxGold = Math.max(maxGold, dfs(grid, i, j));
                }
            }
        }
        return maxGold;
    }
    
    private int dfs(int[][] grid, int i, int j) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == 0) return 0;
        int gold = grid[i][j];
        grid[i][j] = 0; // Mark visited
        
        int max = 0;
        max = Math.max(max, dfs(grid, i+1, j));
        max = Math.max(max, dfs(grid, i-1, j));
        max = Math.max(max, dfs(grid, i, j+1));
        max = Math.max(max, dfs(grid, i, j-1));
        
        grid[i][j] = gold; // Backtrack
        return max + gold;
    }
}
```

## 9. Rat in a Maze
**Đề bài chi tiết:** Chuột tìm đường trong mê cung $N \times N$. Tìm tất cả các đường theo thứ tự từ điển (D, L, R, U).
**Phân tích thuật toán:** Khởi chạy DFS từ (0,0). Đánh dấu 0 để đánh dấu thăm, sau đó backtrack trả lại 1. Gọi theo thứ tự D-L-R-U.
**Độ phức tạp:** Time: $O(3^{N^2})$. Space: $O(N^2)$ stack và strings.

**Mã nguồn Java:**
```java
class Solution {
    public static ArrayList<String> findPath(int[][] m, int n) {
        ArrayList<String> res = new ArrayList<>();
        if (m[0][0] == 0 || m[n-1][n-1] == 0) return res;
        dfs(m, n, 0, 0, "", res);
        return res;
    }
    
    private static void dfs(int[][] m, int n, int r, int c, String path, ArrayList<String> res) {
        if (r < 0 || r >= n || c < 0 || c >= n || m[r][c] == 0) return;
        if (r == n - 1 && c == n - 1) {
            res.add(path);
            return;
        }
        m[r][c] = 0; // Mark
        dfs(m, n, r+1, c, path + "D", res); // Down
        dfs(m, n, r, c-1, path + "L", res); // Left
        dfs(m, n, r, c+1, path + "R", res); // Right
        dfs(m, n, r-1, c, path + "U", res); // Up
        m[r][c] = 1; // Unmark
    }
}
```

## 10. Number of Islands
**Đề bài chi tiết:** Đếm số hòn đảo (kết nối 4 hướng). '1' là đất, '0' là nước.
**Phân tích thuật toán:** Một biến thể DFS Grid. Mọi ô '1' sẽ lan toả và đổi thành '0'. **Không cần state restoration** (Backtracking) vì mục tiêu là gom nhóm, không phải tìm đường riêng rẽ.
**Độ phức tạp:** Time: $O(M \times N)$. Space: $O(M \times N)$.

**Mã nguồn Java:**
```java
class Solution {
    public int numIslands(char[][] grid) {
        int count = 0;
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == '1') {
                    dfs(grid, i, j);
                    count++;
                }
            }
        }
        return count;
    }
    
    private void dfs(char[][] grid, int i, int j) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == '0') return;
        grid[i][j] = '0'; // Sink the island, no backtracking needed
        dfs(grid, i+1, j);
        dfs(grid, i-1, j);
        dfs(grid, i, j+1);
        dfs(grid, i, j-1);
    }
}
```

## 11. Max Area of Island
**Đề bài chi tiết:** Cho một ma trận nhị phân, `1` đại diện cho đất và `0` đại diện cho nước. Một hòn đảo là một nhóm các số `1` kết nối với nhau theo 4 hướng (lên, xuống, trái, phải). Tìm diện tích lớn nhất của một hòn đảo trong ma trận. Nếu không có đảo nào, trả về 0.
**Phân tích thuật toán:** Duyệt qua từng ô của ma trận. Nếu ô hiện tại là `1`, ta thực hiện DFS hoặc BFS để khám phá toàn bộ hòn đảo, đồng thời đếm số lượng ô `1`. Để tránh đếm trùng, ta đánh dấu các ô đã thăm bằng cách đổi chúng thành `0` (hoặc giá trị khác). Cập nhật diện tích lớn nhất sau mỗi lần khám phá một hòn đảo mới.
**Độ phức tạp:** Time: $O(M \times N)$, Space: $O(M \times N)$ cho call stack trong trường hợp xấu nhất (toàn bộ ma trận là một hòn đảo).

**Mã nguồn Java:**
```java
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        int maxArea = 0;
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == 1) {
                    maxArea = Math.max(maxArea, dfs(grid, i, j));
                }
            }
        }
        return maxArea;
    }
    
    private int dfs(int[][] grid, int i, int j) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == 0) {
            return 0;
        }
        grid[i][j] = 0; // Đánh dấu đã thăm
        int area = 1;
        area += dfs(grid, i + 1, j);
        area += dfs(grid, i - 1, j);
        area += dfs(grid, i, j + 1);
        area += dfs(grid, i, j - 1);
        return area;
    }
}
```

## 12. Surrounded Regions
**Đề bài chi tiết:** Cho một ma trận kích thước `m x n` chứa các ký tự `X` và `O`. Bắt giữ (capture) tất cả các vùng `O` bị bao quanh hoàn toàn bởi `X`. Bắt giữ bằng cách chuyển toàn bộ `O` thành `X` trong vùng bị bao quanh đó.
**Phân tích thuật toán:** Một vùng `O` KHÔNG bị bao quanh nếu nó nối với một `O` ở rìa của ma trận. Thuật toán: Đầu tiên, chạy DFS/BFS từ tất cả các `O` nằm ở các biên (rìa) và đánh dấu chúng bằng một ký tự tạm thời (ví dụ `T`). Sau đó, duyệt lại toàn bộ lưới: chuyển tất cả các `O` còn lại (bị bao quanh) thành `X`, và phục hồi các `T` (không bị bao quanh) về `O`.
**Độ phức tạp:** Time: $O(M \times N)$, Space: $O(M \times N)$ cho call stack.

**Mã nguồn Java:**
```java
class Solution {
    public void solve(char[][] board) {
        if (board == null || board.length == 0) return;
        int m = board.length, n = board[0].length;
        
        // Đánh dấu các chữ 'O' kết nối với rìa
        for (int i = 0; i < m; i++) {
            if (board[i][0] == 'O') dfs(board, i, 0);
            if (board[i][n - 1] == 'O') dfs(board, i, n - 1);
        }
        for (int j = 0; j < n; j++) {
            if (board[0][j] == 'O') dfs(board, 0, j);
            if (board[m - 1][j] == 'O') dfs(board, m - 1, j);
        }
        
        // Cập nhật ma trận
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'O') board[i][j] = 'X';
                else if (board[i][j] == 'T') board[i][j] = 'O';
            }
        }
    }
    
    private void dfs(char[][] board, int i, int j) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] != 'O') return;
        board[i][j] = 'T'; // Đánh dấu tạm thời
        dfs(board, i + 1, j);
        dfs(board, i - 1, j);
        dfs(board, i, j + 1);
        dfs(board, i, j - 1);
    }
}
```

## 13. Pacific Atlantic Water Flow
**Đề bài chi tiết:** Cho ma trận `m x n` biểu diễn độ cao địa hình. Nước mưa chỉ có thể chảy từ ô cao hơn hoặc bằng xuống ô thấp hơn. Tìm tất cả các tọa độ ô mà nước mưa có thể chảy ra cả Thái Bình Dương (rìa trên, trái) và Đại Tây Dương (rìa dưới, phải).
**Phân tích thuật toán:** Chạy ngược lại từ 2 đại dương. Thực hiện DFS từ biên Thái Bình Dương và đánh dấu các ô mà nước có thể chảy ngược lên (độ cao bằng hoặc cao hơn ô hiện tại). Tương tự cho biên Đại Tây Dương. Lấy giao của 2 tập hợp ô có thể đến được từ 2 đại dương, đó chính là kết quả cần tìm.
**Độ phức tạp:** Time: $O(M \times N)$, Space: $O(M \times N)$.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> pacificAtlantic(int[][] heights) {
        List<List<Integer>> res = new ArrayList<>();
        if (heights == null || heights.length == 0 || heights[0].length == 0) return res;
        
        int m = heights.length, n = heights[0].length;
        boolean[][] pacific = new boolean[m][n];
        boolean[][] atlantic = new boolean[m][n];
        
        for (int i = 0; i < m; i++) {
            dfs(heights, pacific, Integer.MIN_VALUE, i, 0);
            dfs(heights, atlantic, Integer.MIN_VALUE, i, n - 1);
        }
        for (int j = 0; j < n; j++) {
            dfs(heights, pacific, Integer.MIN_VALUE, 0, j);
            dfs(heights, atlantic, Integer.MIN_VALUE, m - 1, j);
        }
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (pacific[i][j] && atlantic[i][j]) {
                    res.add(Arrays.asList(i, j));
                }
            }
        }
        return res;
    }
    
    private void dfs(int[][] heights, boolean[][] visited, int prevHeight, int i, int j) {
        if (i < 0 || i >= heights.length || j < 0 || j >= heights[0].length || visited[i][j] || heights[i][j] < prevHeight) {
            return;
        }
        visited[i][j] = true;
        dfs(heights, visited, heights[i][j], i + 1, j);
        dfs(heights, visited, heights[i][j], i - 1, j);
        dfs(heights, visited, heights[i][j], i, j + 1);
        dfs(heights, visited, heights[i][j], i, j - 1);
    }
}
```

## 14. Minesweeper
**Đề bài chi tiết:** Cập nhật bảng trò chơi dò mìn. Cho một ma trận `m x n` biểu diễn bảng (`M` là mìn chưa lật, `E` là ô trống chưa lật, `B` là ô trống đã lật, chữ số là số mìn xung quanh, `X` là mìn đã nổ) và toạ độ một click. Trả về bảng sau khi click.
**Phân tích thuật toán:**
- Nếu click vào mìn `M`, cập nhật thành `X` và game over.
- Nếu click vào ô trống `E`:
  - Tính số mìn xung quanh (8 hướng).
  - Nếu có mìn xung quanh, điền số lượng mìn và dừng lại.
  - Nếu không có mìn xung quanh (điền `B`), gọi đệ quy DFS mở rộng ra 8 hướng lân cận để tiếp tục lật.
**Độ phức tạp:** Time: $O(M \times N)$ trong trường hợp lật toàn bộ bảng, Space: $O(M \times N)$ cho đệ quy DFS.

**Mã nguồn Java:**
```java
class Solution {
    int[] dx = {-1, -1, -1, 0, 0, 1, 1, 1};
    int[] dy = {-1, 0, 1, -1, 1, -1, 0, 1};
    
    public char[][] updateBoard(char[][] board, int[] click) {
        int r = click[0], c = click[1];
        if (board[r][c] == 'M') {
            board[r][c] = 'X';
            return board;
        }
        dfs(board, r, c);
        return board;
    }
    
    private void dfs(char[][] board, int r, int c) {
        if (r < 0 || r >= board.length || c < 0 || c >= board[0].length || board[r][c] != 'E') return;
        
        int mines = getMines(board, r, c);
        if (mines == 0) {
            board[r][c] = 'B';
            for (int i = 0; i < 8; i++) {
                dfs(board, r + dx[i], c + dy[i]);
            }
        } else {
            board[r][c] = (char)('0' + mines);
        }
    }
    
    private int getMines(char[][] board, int r, int c) {
        int count = 0;
        for (int i = 0; i < 8; i++) {
            int nr = r + dx[i];
            int nc = c + dy[i];
            if (nr >= 0 && nr < board.length && nc >= 0 && nc < board[0].length) {
                if (board[nr][nc] == 'M' || board[nr][nc] == 'X') count++;
            }
        }
        return count;
    }
}
```

## 15. Shortest Bridge
**Đề bài chi tiết:** Cho ma trận nhị phân có chính xác hai hòn đảo. Tìm số ô `0` ít nhất cần lật thành `1` để nối hai hòn đảo thành một.
**Phân tích thuật toán:**
1. Dùng DFS để tìm và đánh dấu toàn bộ các ô của hòn đảo đầu tiên (chuyển các ô `1` thành `2`), đồng thời đẩy tất cả các toạ độ của đảo 1 vào một hàng đợi (Queue).
2. Dùng BFS bắt đầu từ các điểm trong hàng đợi, mở rộng từng bước qua các ô `0` (nước) để tìm đường đi ngắn nhất đến hòn đảo thứ hai (các ô `1`).
**Độ phức tạp:** Time: $O(M \times N)$, Space: $O(M \times N)$.

**Mã nguồn Java:**
```java
class Solution {
    int[] dx = {1, -1, 0, 0};
    int[] dy = {0, 0, 1, -1};
    
    public int shortestBridge(int[][] grid) {
        int n = grid.length;
        Queue<int[]> queue = new LinkedList<>();
        boolean found = false;
        
        // Bước 1: DFS đánh dấu đảo đầu tiên bằng 2 và thêm vào queue
        for (int i = 0; i < n && !found; i++) {
            for (int j = 0; j < n && !found; j++) {
                if (grid[i][j] == 1) {
                    dfs(grid, i, j, queue);
                    found = true;
                }
            }
        }
        
        // Bước 2: BFS để tìm đường ngắn nhất tới đảo kia
        int step = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            while (size-- > 0) {
                int[] curr = queue.poll();
                for (int i = 0; i < 4; i++) {
                    int nx = curr[0] + dx[i];
                    int ny = curr[1] + dy[i];
                    
                    if (nx >= 0 && nx < n && ny >= 0 && ny < n) {
                        if (grid[nx][ny] == 1) return step;
                        if (grid[nx][ny] == 0) {
                            grid[nx][ny] = 2;
                            queue.offer(new int[]{nx, ny});
                        }
                    }
                }
            }
            step++;
        }
        return -1;
    }
    
    private void dfs(int[][] grid, int i, int j, Queue<int[]> queue) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid.length || grid[i][j] != 1) return;
        grid[i][j] = 2;
        queue.offer(new int[]{i, j});
        for (int k = 0; k < 4; k++) {
            dfs(grid, i + dx[k], j + dy[k], queue);
        }
    }
}
```

## 16. Making A Large Island
**Đề bài chi tiết:** Cho một ma trận nhị phân. Bạn có quyền thay đổi TỐI ĐA một ô `0` thành `1`. Trả về diện tích của hòn đảo lớn nhất có thể được tạo ra.
**Phân tích thuật toán:**
1. Quét toàn bộ lưới. Dùng DFS đánh chỉ số ID duy nhất (ví dụ bắt đầu từ 2) cho mỗi hòn đảo và lưu diện tích tương ứng của ID đó vào một HashMap.
2. Tìm hòn đảo lớn nhất ban đầu (trong trường hợp toàn `1` không có `0`).
3. Duyệt lưới lần nữa, tại mỗi ô `0`, tính tổng diện tích của các đảo kề cạnh nó (sử dụng ID trong HashMap, dùng HashSet để tránh đếm trùng ID). Cập nhật kết quả cực đại có thể tạo ra.
**Độ phức tạp:** Time: $O(N^2)$, Space: $O(N^2)$.

**Mã nguồn Java:**
```java
class Solution {
    int[] dr = {-1, 1, 0, 0};
    int[] dc = {0, 0, -1, 1};
    
    public int largestIsland(int[][] grid) {
        int n = grid.length;
        int islandId = 2;
        Map<Integer, Integer> areaMap = new HashMap<>();
        int maxArea = 0;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    int area = dfs(grid, i, j, islandId);
                    areaMap.put(islandId, area);
                    maxArea = Math.max(maxArea, area);
                    islandId++;
                }
            }
        }
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 0) {
                    Set<Integer> seen = new HashSet<>();
                    int currentMax = 1;
                    for (int k = 0; k < 4; k++) {
                        int r = i + dr[k], c = j + dc[k];
                        if (r >= 0 && r < n && c >= 0 && c < n && grid[r][c] > 1) {
                            int id = grid[r][c];
                            if (!seen.contains(id)) {
                                currentMax += areaMap.get(id);
                                seen.add(id);
                            }
                        }
                    }
                    maxArea = Math.max(maxArea, currentMax);
                }
            }
        }
        return maxArea == 0 ? 1 : maxArea; // Trường hợp lưới toàn 0 ban đầu
    }
    
    private int dfs(int[][] grid, int i, int j, int id) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid.length || grid[i][j] != 1) return 0;
        grid[i][j] = id;
        int area = 1;
        for (int k = 0; k < 4; k++) {
            area += dfs(grid, i + dr[k], j + dc[k], id);
        }
        return area;
    }
}
```

## 17. Count Sub Islands
**Đề bài chi tiết:** Cho hai ma trận nhị phân `grid1` và `grid2` cùng kích thước. Một đảo trong `grid2` được gọi là đảo con (sub island) nếu tất cả các ô của nó đều trùng với các ô có giá trị `1` tương ứng trong `grid1`. Đếm số đảo con.
**Phân tích thuật toán:** Duyệt qua tất cả các đảo trong `grid2` bằng DFS. Trong quá trình DFS, kiểm tra xem tại toạ độ đang thăm, ma trận `grid1` có bằng `1` không. Nếu có bất kỳ ô nào thuộc đảo của `grid2` mà `grid1` tại đó bằng `0`, thì đảo hiện tại không phải là đảo con. Chú ý: Vẫn phải duyệt hết toàn bộ đảo của `grid2` bằng DFS kể cả khi phát hiện vi phạm, để đánh dấu đã thăm toàn bộ đảo đó.
**Độ phức tạp:** Time: $O(M \times N)$, Space: $O(M \times N)$.

**Mã nguồn Java:**
```java
class Solution {
    public int countSubIslands(int[][] grid1, int[][] grid2) {
        int m = grid2.length, n = grid2[0].length;
        int count = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid2[i][j] == 1) {
                    if (dfs(grid1, grid2, i, j)) {
                        count++;
                    }
                }
            }
        }
        return count;
    }
    
    private boolean dfs(int[][] grid1, int[][] grid2, int i, int j) {
        if (i < 0 || i >= grid2.length || j < 0 || j >= grid2[0].length || grid2[i][j] == 0) return true;
        
        grid2[i][j] = 0; // Đánh dấu đã thăm
        boolean isSub = true;
        if (grid1[i][j] == 0) isSub = false; // Đảo này không nằm trọn trong grid1
        
        // Cần gọi hết DFS để xoá toàn bộ đảo khỏi grid2
        boolean up = dfs(grid1, grid2, i - 1, j);
        boolean down = dfs(grid1, grid2, i + 1, j);
        boolean left = dfs(grid1, grid2, i, j - 1);
        boolean right = dfs(grid1, grid2, i, j + 1);
        
        return isSub && up && down && left && right;
    }
}
```

## 18. Number of Closed Islands
**Đề bài chi tiết:** Cho ma trận nhị phân `0` là đất và `1` là nước. Đảo đóng (closed island) là hòn đảo hoàn toàn được bao quanh bởi `1`, tức là không có bất kỳ ô `0` nào chạm vào biên của ma trận. Đếm số đảo đóng.
**Phân tích thuật toán:**
Bước 1: Chạy DFS dọc theo chu vi (biên) của ma trận. Nếu gặp `0` (đất), ta đổi thành `1` (nước) hoặc đánh dấu để loại bỏ toàn bộ phần đảo đó, vì chúng không thể là closed island.
Bước 2: Quét lại toàn bộ phần lõi bên trong ma trận, dùng DFS để đếm số lượng hòn đảo `0` còn lại, đây chính là các đảo đóng vì chúng không chạm biên.
**Độ phức tạp:** Time: $O(M \times N)$, Space: $O(M \times N)$.

**Mã nguồn Java:**
```java
class Solution {
    public int closedIsland(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        
        // Bước 1: DFS ở 4 biên
        for (int i = 0; i < m; i++) {
            if (grid[i][0] == 0) dfs(grid, i, 0);
            if (grid[i][n-1] == 0) dfs(grid, i, n-1);
        }
        for (int j = 0; j < n; j++) {
            if (grid[0][j] == 0) dfs(grid, 0, j);
            if (grid[m-1][j] == 0) dfs(grid, m-1, j);
        }
        
        // Bước 2: Đếm số đảo còn lại
        int count = 0;
        for (int i = 1; i < m - 1; i++) {
            for (int j = 1; j < n - 1; j++) {
                if (grid[i][j] == 0) {
                    dfs(grid, i, j);
                    count++;
                }
            }
        }
        return count;
    }
    
    private void dfs(int[][] grid, int i, int j) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] != 0) {
            return;
        }
        grid[i][j] = 1; // Mark as visited (thành nước)
        dfs(grid, i + 1, j);
        dfs(grid, i - 1, j);
        dfs(grid, i, j + 1);
        dfs(grid, i, j - 1);
    }
}
```

## 19. Robot Room Cleaner
**Đề bài chi tiết:** Cho một robot lau dọn tự động nằm trong một phòng. Có vật cản. Robot chỉ có các hàm interface như: `move()` đi tới 1 bước (trả về false nếu vướng vật cản), `turnLeft()`, `turnRight()`, `clean()`. Thiết kế thuật toán để lau sạch toàn bộ phòng.
**Phân tích thuật toán:** Bài toán không cho Grid, ta phải tự xây dựng toạ độ bằng DFS với HashSet lưu lại toạ độ `(r, c)` đã thăm tương đối so với gốc ban đầu `(0, 0)`. Hướng ban đầu là hướng trên (0). Duyệt 4 hướng bằng cách rẽ phải liên tục. Đi sâu và sau đó cần quay trở lại trạng thái cũ (Backtracking) bằng cách xoay 180 độ, bước một bước và xoay lại 180 độ.
**Độ phức tạp:** Time: $O(V)$, Space: $O(V)$, với $V$ là số lượng ô không có vật cản.

**Mã nguồn Java:**
```java
/**
 * // This is the robot's control interface.
 * // You should not implement it, or speculate about its implementation
 * interface Robot {
 *     public boolean move();
 *     public void turnLeft();
 *     public void turnRight();
 *     public void clean();
 * }
 */
class Solution {
    int[] dr = {-1, 0, 1, 0}; // Up, Right, Down, Left
    int[] dc = {0, 1, 0, -1};
    
    public void cleanRoom(Robot robot) {
        Set<String> visited = new HashSet<>();
        dfs(robot, 0, 0, 0, visited);
    }
    
    private void dfs(Robot robot, int r, int c, int dir, Set<String> visited) {
        String pos = r + "," + c;
        visited.add(pos);
        robot.clean();
        
        for (int i = 0; i < 4; i++) {
            int newDir = (dir + i) % 4;
            int newR = r + dr[newDir];
            int newC = c + dc[newDir];
            
            if (!visited.contains(newR + "," + newC) && robot.move()) {
                dfs(robot, newR, newC, newDir, visited);
                goBack(robot); // Backtrack
            }
            robot.turnRight(); // Xoay robot hướng tới ngã rẽ tiếp theo
        }
    }
    
    private void goBack(Robot robot) {
        robot.turnRight();
        robot.turnRight();
        robot.move();
        robot.turnRight();
        robot.turnRight();
    }
}
```

## 20. Sliding Puzzle
**Đề bài chi tiết:** Trên một bảng `2 x 3` có 5 khối đánh số từ 1 đến 5 và một ô trống đánh số 0. Một lần di chuyển hợp lệ là đổi chỗ số 0 với một số liền kề. Cho trạng thái ban đầu của bảng, tìm số lượt di chuyển ít nhất để đạt được trạng thái mục tiêu `[[1,2,3],[4,5,0]]`.
**Phân tích thuật toán:** Mặc dù lưới nhỏ, bài toán yêu cầu tìm số bước tối thiểu nên ta dùng BFS trên các trạng thái (State). Biểu diễn lưới thành một chuỗi (String) 6 ký tự để dễ hash. Các chỉ số kề của chỉ số `i` trong chuỗi 1D tương ứng với bảng 2x3 đã biết trước, ví dụ: 0 -> {1, 3}, 1 -> {0, 2, 4}, ... Dùng HashSet để tránh lặp lại các trạng thái.
**Độ phức tạp:** Time: $O(V + E) \approx O(6!)$ số lượng hoán vị, Space: $O(6!)$.

**Mã nguồn Java:**
```java
class Solution {
    public int slidingPuzzle(int[][] board) {
        String target = "123450";
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 3; j++) {
                sb.append(board[i][j]);
            }
        }
        String start = sb.toString();
        
        // Mảng quy đổi toạ độ kề 1D cho bảng 2x3
        int[][] dirs = {{1, 3}, {0, 2, 4}, {1, 5}, {0, 4}, {1, 3, 5}, {2, 4}};
        
        Queue<String> queue = new LinkedList<>();
        Set<String> visited = new HashSet<>();
        queue.offer(start);
        visited.add(start);
        
        int steps = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            while (size-- > 0) {
                String curr = queue.poll();
                if (curr.equals(target)) return steps;
                
                int zeroIndex = curr.indexOf('0');
                for (int nextIndex : dirs[zeroIndex]) {
                    String next = swap(curr, zeroIndex, nextIndex);
                    if (!visited.contains(next)) {
                        visited.add(next);
                        queue.offer(next);
                    }
                }
            }
            steps++;
        }
        return -1;
    }
    
    private String swap(String s, int i, int j) {
        char[] chars = s.toCharArray();
        char temp = chars[i];
        chars[i] = chars[j];
        chars[j] = temp;
        return new String(chars);
    }
}
```

## 21. Flood Fill
**Đề bài chi tiết:** Cho một ma trận `image` biểu diễn các pixel của một bức ảnh, kèm theo toạ độ bắt đầu `(sr, sc)` và một màu `color` mới. Thực hiện tô màu loang (flood fill) bằng cách thay đổi màu sắc của pixel xuất phát và toàn bộ các pixel kề cạnh (4 hướng) có cùng màu ban đầu với nó, và tiếp tục lan ra tương tự.
**Phân tích thuật toán:** Lưu lại màu ban đầu của điểm `(sr, sc)`. Sử dụng DFS. Tại mỗi ô, nếu nằm trong ma trận và có màu bằng với màu ban đầu, ta đổi màu nó thành `color` mới, sau đó gọi đệ quy ra 4 hướng. Lưu ý nếu màu ban đầu đã giống màu mới thì phải dừng ngay để tránh lặp vô hạn.
**Độ phức tạp:** Time: $O(M \times N)$ duyệt tất cả các pixel nếu cùng một màu, Space: $O(M \times N)$ cho stack.

**Mã nguồn Java:**
```java
class Solution {
    public int[][] floodFill(int[][] image, int sr, int sc, int color) {
        int startColor = image[sr][sc];
        if (startColor != color) {
            dfs(image, sr, sc, startColor, color);
        }
        return image;
    }
    
    private void dfs(int[][] image, int r, int c, int startColor, int newColor) {
        if (r < 0 || r >= image.length || c < 0 || c >= image[0].length || image[r][c] != startColor) {
            return;
        }
        image[r][c] = newColor;
        dfs(image, r + 1, c, startColor, newColor);
        dfs(image, r - 1, c, startColor, newColor);
        dfs(image, r, c + 1, startColor, newColor);
        dfs(image, r, c - 1, startColor, newColor);
    }
}
```

## 22. Out of Boundary Paths
**Đề bài chi tiết:** Cho ma trận kích thước `m x n`. Bạn có một quả bóng ban đầu nằm ở ô `(startRow, startColumn)`. Bạn được phép di chuyển quả bóng sang ô kề cạnh hoặc đá ra ngoài lưới (tối đa `maxMove` lần). Tìm số lượng đường đi có thể đưa bóng ra ngoài biên của lưới. Trả về kết quả modulo $10^9 + 7$.
**Phân tích thuật toán:** Một bài toán Backtracking Grid kết hợp với Quy hoạch động (Memoization). Từ một ô, thử đi cả 4 hướng với `moves - 1`. Trạng thái cần ghi nhớ là `(r, c, moves)`. Nếu đi ra ngoài lưới (`r < 0 || r >= m || c < 0 || c >= n`) thì ta có 1 đường đi hợp lệ.
**Độ phức tạp:** Time: $O(M \times N \times \text{maxMove})$, Space: $O(M \times N \times \text{maxMove})$ để lưu mảng `memo`.

**Mã nguồn Java:**
```java
class Solution {
    int MOD = 1000000007;
    Integer[][][] memo;
    
    public int findPaths(int m, int n, int maxMove, int startRow, int startColumn) {
        memo = new Integer[m][n][maxMove + 1];
        return dfs(m, n, maxMove, startRow, startColumn);
    }
    
    private int dfs(int m, int n, int move, int r, int c) {
        if (r < 0 || r >= m || c < 0 || c >= n) return 1;
        if (move == 0) return 0;
        if (memo[r][c][move] != null) return memo[r][c][move];
        
        int count = 0;
        count = (count + dfs(m, n, move - 1, r + 1, c)) % MOD;
        count = (count + dfs(m, n, move - 1, r - 1, c)) % MOD;
        count = (count + dfs(m, n, move - 1, r, c + 1)) % MOD;
        count = (count + dfs(m, n, move - 1, r, c - 1)) % MOD;
        
        memo[r][c][move] = count;
        return count;
    }
}
```

## 23. Snakes and Ladders
**Đề bài chi tiết:** Cho một bảng Rắn và Thang kích thước `n x n`, các ô được đánh số từ `1` đến `n^2` theo thứ tự zic-zắc bắt đầu từ góc dưới cùng bên trái. Mỗi ô có thể có giá trị `-1` (bình thường) hoặc một chỉ số trỏ tới ô khác (rắn hoặc thang). Tìm số lần đổ xúc xắc (1-6) ít nhất để đi từ ô số 1 đến ô số `n^2`.
**Phân tích thuật toán:** Bài toán tìm đường ngắn nhất trên đồ thị không trọng số, nên dùng BFS là tối ưu nhất. Có thể quy về Grid Backtracking nếu duyệt. Hàm ánh xạ `getCoordinate(square)` dùng để chuyển đổi từ số hiệu ô về toạ độ `(r, c)` trong ma trận zic-zắc. Tránh thăm lặp lại bằng mảng boolean `visited`.
**Độ phức tạp:** Time: $O(N^2)$, Space: $O(N^2)$.

**Mã nguồn Java:**
```java
class Solution {
    public int snakesAndLadders(int[][] board) {
        int n = board.length;
        Queue<Integer> q = new LinkedList<>();
        boolean[] visited = new boolean[n * n + 1];
        
        q.offer(1);
        visited[1] = true;
        
        int steps = 0;
        while (!q.isEmpty()) {
            int size = q.size();
            while (size-- > 0) {
                int curr = q.poll();
                if (curr == n * n) return steps;
                
                for (int i = 1; i <= 6; i++) {
                    int next = curr + i;
                    if (next > n * n) break;
                    
                    int[] pos = getCoordinate(next, n);
                    int r = pos[0], c = pos[1];
                    if (board[r][c] != -1) {
                        next = board[r][c]; // Trượt theo rắn hoặc leo thang
                    }
                    
                    if (!visited[next]) {
                        visited[next] = true;
                        q.offer(next);
                    }
                }
            }
            steps++;
        }
        return -1;
    }
    
    private int[] getCoordinate(int s, int n) {
        int r = n - 1 - (s - 1) / n;
        int c = (s - 1) % n;
        if (((n - 1 - r) % 2) != 0) {
            c = n - 1 - c; // Hàng chẵn tính từ dưới thì thứ tự ngược lại (zic-zac)
        }
        return new int[]{r, c};
    }
}
```

## 24. Longest Increasing Path in a Matrix
**Đề bài chi tiết:** Cho một ma trận các số nguyên, tìm độ dài của đường đi tăng dần liên tục dài nhất. Bạn có thể di chuyển ra 4 hướng. Không được di chuyển theo đường chéo hoặc ra ngoài biên.
**Phân tích thuật toán:** DFS với Memoization. Tại mỗi ô, ta chạy DFS tìm đường đi tăng dần. Nếu chưa đi qua, duyệt 4 hướng kề, thoả mãn lớn hơn số hiện tại thì đệ quy. Lưu lại độ dài lớn nhất tính từ ô đó vào ma trận `memo`.
**Độ phức tạp:** Time: $O(M \times N)$ do mỗi ô được tính đúng 1 lần, Space: $O(M \times N)$.

**Mã nguồn Java:**
```java
class Solution {
    int[][] dirs = {{1,0}, {-1,0}, {0,1}, {0,-1}};
    
    public int longestIncreasingPath(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return 0;
        int m = matrix.length, n = matrix[0].length;
        int[][] memo = new int[m][n];
        int maxLen = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                maxLen = Math.max(maxLen, dfs(matrix, i, j, memo));
            }
        }
        return maxLen;
    }
    
    private int dfs(int[][] matrix, int r, int c, int[][] memo) {
        if (memo[r][c] != 0) return memo[r][c];
        
        int max = 1;
        for (int[] d : dirs) {
            int nr = r + d[0];
            int nc = c + d[1];
            if (nr >= 0 && nr < matrix.length && nc >= 0 && nc < matrix[0].length && matrix[nr][nc] > matrix[r][c]) {
                max = Math.max(max, 1 + dfs(matrix, nr, nc, memo));
            }
        }
        memo[r][c] = max;
        return max;
    }
}
```

## 25. Cherry Pickup
**Đề bài chi tiết:** Cho một ma trận $N \times N$ với giá trị là số cherry trên ô. `-1` là vật cản, `0` là ô trống, `1` là có 1 cherry. Bạn đi từ góc `(0,0)` tới góc `(N-1, N-1)` và hái cherry (chỉ được đi xuống hoặc phải). Sau đó bạn đi ngược từ `(N-1,N-1)` về `(0,0)` (chỉ đi lên hoặc trái) và gom tiếp cherry. Mỗi lần đi qua 1 ô thì mất cherry trên ô đó. Tìm lượng cherry lớn nhất.
**Phân tích thuật toán:** Quy về hai người cùng đi từ `(0,0)` đến `(N-1, N-1)` một lúc thay vì một người đi đi về về. Toạ độ: `r1 + c1 = r2 + c2 = bước chạy (step)`. Dùng DFS kết hợp DP 3 chiều (hoặc Memoization 3 chiều) theo `(r1, c1, r2)` vì `c2` suy ra được. Nếu trùng ô, ta chỉ tính 1 cherry.
**Độ phức tạp:** Time: $O(N^3)$, Space: $O(N^3)$.

**Mã nguồn Java:**
```java
class Solution {
    public int cherryPickup(int[][] grid) {
        int n = grid.length;
        Integer[][][] memo = new Integer[n][n][n];
        return Math.max(0, dfs(grid, n, 0, 0, 0, memo));
    }
    
    private int dfs(int[][] grid, int n, int r1, int c1, int r2, Integer[][][] memo) {
        int c2 = r1 + c1 - r2;
        if (r1 >= n || c1 >= n || r2 >= n || c2 >= n || grid[r1][c1] == -1 || grid[r2][c2] == -1) {
            return Integer.MIN_VALUE;
        }
        
        if (r1 == n - 1 && c1 == n - 1) return grid[r1][c1];
        if (memo[r1][c1][r2] != null) return memo[r1][c1][r2];
        
        int cherries = grid[r1][c1];
        if (r1 != r2) cherries += grid[r2][c2]; // Hai người không dẫm chân lên nhau
        
        int max = Math.max(dfs(grid, n, r1 + 1, c1, r2 + 1, memo), dfs(grid, n, r1, c1 + 1, r2, memo)); // Xuống, Xuống hoặc Phải, Phải
        max = Math.max(max, dfs(grid, n, r1 + 1, c1, r2, memo)); // Xuống, Phải
        max = Math.max(max, dfs(grid, n, r1, c1 + 1, r2 + 1, memo)); // Phải, Xuống
        
        int res = (max == Integer.MIN_VALUE) ? Integer.MIN_VALUE : max + cherries;
        memo[r1][c1][r2] = res;
        return res;
    }
}
```

## 26. Dungeon Game
**Đề bài chi tiết:** Một hiệp sĩ cần giải cứu công chúa bị giam ở ngục `(M-1, N-1)` từ toạ độ `(0, 0)`. Các ô mang giá trị âm (quái thú làm mất máu) và giá trị dương (hồi máu). Máu ban đầu của hiệp sĩ là một số nguyên dương, nếu máu xuống $\le 0$ thì hiệp sĩ chết. Tìm lượng máu ban đầu tối thiểu để cứu công chúa (chỉ được đi sang phải, xuống dưới).
**Phân tích thuật toán:** Một bài DFS/DP dạng đi lùi (Bottom-Up). Để hiệp sĩ tồn tại tại ô `(i,j)`, số máu tối thiểu anh ta cần có trước khi vào ô đó phải bù đắp được thiệt hại ở ô `(i,j)` và lượng máu cần để sống sót để đi tiếp ngã rẽ phía sau. Công thức: `minHealth = Math.max(1, Math.min(đi xuống, đi phải) - dungeon[i][j])`.
**Độ phức tạp:** Time: $O(M \times N)$, Space: $O(M \times N)$ cho memoization.

**Mã nguồn Java:**
```java
class Solution {
    public int calculateMinimumHP(int[][] dungeon) {
        int m = dungeon.length, n = dungeon[0].length;
        Integer[][] memo = new Integer[m][n];
        return dfs(dungeon, 0, 0, m, n, memo);
    }
    
    private int dfs(int[][] dungeon, int i, int j, int m, int n, Integer[][] memo) {
        if (i == m - 1 && j == n - 1) {
            return Math.max(1, 1 - dungeon[i][j]);
        }
        if (i >= m || j >= n) return Integer.MAX_VALUE;
        if (memo[i][j] != null) return memo[i][j];
        
        int down = dfs(dungeon, i + 1, j, m, n, memo);
        int right = dfs(dungeon, i, j + 1, m, n, memo);
        
        int minHealthRequired = Math.min(down, right) - dungeon[i][j];
        memo[i][j] = Math.max(1, minHealthRequired);
        return memo[i][j];
    }
}
```

## 27. Knight Probability in Chessboard
**Đề bài chi tiết:** Cho bàn cờ `n x n`, quân mã ban đầu nằm ở ô `(row, column)`. Nó thực hiện đúng `k` bước nhảy ngẫu nhiên. Tại mỗi bước nó sẽ nhảy 1 trong 8 hướng di chuyển chuẩn của mã. Tìm xác suất để sau `k` bước, quân mã vẫn nằm trên bàn cờ.
**Phân tích thuật toán:** Tại một ô, mã có xác suất $1/8$ nhảy sang hướng kề. Trạng thái của bài toán gồm vị trí mã `(r, c)` và số lần đi tiếp còn lại `k`. Dùng DFS có kết hợp mảng Memo `memo[r][c][k]` dạng double để lưu kết quả trung gian.
**Độ phức tạp:** Time: $O(N^2 \times K)$, Space: $O(N^2 \times K)$.

**Mã nguồn Java:**
```java
class Solution {
    int[] dr = {-2, -2, -1, -1, 1, 1, 2, 2};
    int[] dc = {-1, 1, -2, 2, -2, 2, -1, 1};
    
    public double knightProbability(int n, int k, int row, int column) {
        Double[][][] memo = new Double[n][n][k + 1];
        return dfs(n, k, row, column, memo);
    }
    
    private double dfs(int n, int k, int r, int c, Double[][][] memo) {
        if (r < 0 || r >= n || c < 0 || c >= n) return 0.0;
        if (k == 0) return 1.0;
        if (memo[r][c][k] != null) return memo[r][c][k];
        
        double prob = 0;
        for (int i = 0; i < 8; i++) {
            prob += dfs(n, k - 1, r + dr[i], c + dc[i], memo) / 8.0;
        }
        
        memo[r][c][k] = prob;
        return prob;
    }
}
```

## 28. Minimum Path Sum
**Đề bài chi tiết:** Cho lưới $m \times n$ chứa số không âm. Tìm một đường đi từ góc trên cùng bên trái đến dưới cùng bên phải có tổng các số trên đường đi là nhỏ nhất. Chỉ được đi sang phải hoặc xuống dưới.
**Phân tích thuật toán:** Một bài duyệt Grid điển hình nhưng tốt nhất nên dùng DFS + Memoization (Top-down DP) để không bị TLE. Số điền tại mỗi ô bằng giá trị ô đó cộng cho lượng nhỏ nhất của nhánh phải hoặc nhánh xuống (chọn nhánh tối ưu hơn).
**Độ phức tạp:** Time: $O(M \times N)$, Space: $O(M \times N)$.

**Mã nguồn Java:**
```java
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        Integer[][] memo = new Integer[m][n];
        return dfs(grid, 0, 0, memo);
    }
    
    private int dfs(int[][] grid, int i, int j, Integer[][] memo) {
        if (i == grid.length - 1 && j == grid[0].length - 1) return grid[i][j];
        if (i >= grid.length || j >= grid[0].length) return Integer.MAX_VALUE;
        if (memo[i][j] != null) return memo[i][j];
        
        int right = dfs(grid, i, j + 1, memo);
        int down = dfs(grid, i + 1, j, memo);
        
        memo[i][j] = grid[i][j] + Math.min(right, down);
        return memo[i][j];
    }
}
```

## 29. Maze II
**Đề bài chi tiết:** Một mê cung với không gian trống `0` và tường `1`. Một quả bóng ở ô xuất phát có thể lăn thẳng theo 4 hướng nhưng nó **sẽ không bao giờ dừng lại** cho đến khi đập vào một bức tường. Tìm khoảng cách ngắn nhất để bóng đến được đích. Trả về -1 nếu không thể.
**Phân tích thuật toán:** Dùng BFS hoặc DFS Backtracking nhưng thay vì tính 1 bước, mỗi lần đệ quy ta phải đẩy một vòng `while` tiến liên tục cho đến khi chạm tường. Ta cũng phải dùng mảng `distance` 2D để memoize lại độ dài tính từ start ngắn nhất đến mọi ô (giống thuật toán Dijkstra hoặc BFS có trọng số).
**Độ phức tạp:** Time: $O(M \times N \times \max(M, N))$, Space: $O(M \times N)$.

**Mã nguồn Java:**
```java
class Solution {
    public int shortestDistance(int[][] maze, int[] start, int[] destination) {
        int m = maze.length, n = maze[0].length;
        int[][] distance = new int[m][n];
        for (int[] row : distance) Arrays.fill(row, Integer.MAX_VALUE);
        
        distance[start[0]][start[1]] = 0;
        dfs(maze, start, distance);
        
        return distance[destination[0]][destination[1]] == Integer.MAX_VALUE ? -1 : distance[destination[0]][destination[1]];
    }
    
    private void dfs(int[][] maze, int[] start, int[][] distance) {
        int[][] dirs = {{0,1}, {0,-1}, {1,0}, {-1,0}};
        
        for (int[] dir : dirs) {
            int r = start[0], c = start[1];
            int count = 0;
            // Lăn quả bóng cho tới khi đập tường
            while (r + dir[0] >= 0 && r + dir[0] < maze.length && c + dir[1] >= 0 && c + dir[1] < maze[0].length && maze[r + dir[0]][c + dir[1]] == 0) {
                r += dir[0];
                c += dir[1];
                count++;
            }
            if (distance[start[0]][start[1]] + count < distance[r][c]) {
                distance[r][c] = distance[start[0]][start[1]] + count;
                dfs(maze, new int[]{r, c}, distance);
            }
        }
    }
}
```

## 30. Matchsticks to Square
**Đề bài chi tiết:** Cho một mảng số nguyên lưu trữ độ dài của các que diêm. Bạn cần lấy TẤT CẢ các que diêm, không được bẻ que, để ghép thành một hình vuông. Trả về `true` nếu có thể ghép được và `false` nếu không.
**Phân tích thuật toán:** Tuy không phải là lưới ma trận truyền thống, bài toán này áp dụng Backtracking nhóm/lưới logic chia mảng. Tính chu vi `Sum` của tất cả các que. Nếu `Sum` không chia hết cho 4, trả về `false`. Độ dài cạnh hình vuông phải là `side = Sum / 4`. Khởi tạo mảng `sides` với 4 giá trị `0`. Sắp xếp các que diêm giảm dần, sau đó đệ quy DFS thử xếp dần que diêm lớn nhất vào 4 cạnh. Rút que ra (backtrack) nếu nó không phù hợp.
**Độ phức tạp:** Time: $O(4^N)$ ở trường hợp tồi tệ nhất, nhưng sắp xếp giảm dần làm thời gian tiệm cận tối ưu hơn rất nhiều, Space: $O(N)$ depth.

**Mã nguồn Java:**
```java
class Solution {
    public boolean makesquare(int[] matchsticks) {
        if (matchsticks == null || matchsticks.length < 4) return false;
        long sum = 0;
        for (int match : matchsticks) sum += match;
        if (sum % 4 != 0) return false;
        
        long side = sum / 4;
        Arrays.sort(matchsticks);
        // Đảo ngược mảng để tối ưu DFS
        for (int i = 0; i < matchsticks.length / 2; i++) {
            int temp = matchsticks[i];
            matchsticks[i] = matchsticks[matchsticks.length - 1 - i];
            matchsticks[matchsticks.length - 1 - i] = temp;
        }
        
        return dfs(matchsticks, new long[4], 0, side);
    }
    
    private boolean dfs(int[] matchsticks, long[] sides, int index, long target) {
        if (index == matchsticks.length) {
            return sides[0] == target && sides[1] == target && sides[2] == target && sides[3] == target;
        }
        
        for (int i = 0; i < 4; i++) {
            if (sides[i] + matchsticks[index] <= target) {
                sides[i] += matchsticks[index];
                if (dfs(matchsticks, sides, index + 1, target)) return true;
                sides[i] -= matchsticks[index]; // Backtrack
            }
            if (sides[i] == 0) break; // Tối ưu hoá nhánh vô ích
        }
        return false;
    }
}
```
