# 30 Bài tập Sắp xếp không so sánh (Non-Comparison Sorting Problems)

## 1. Sort an Array (Sắp xếp mảng - Bằng Counting Sort)
**Đề bài chi tiết:** Cho một mảng các số nguyên `nums`, hãy sắp xếp mảng theo thứ tự tăng dần và trả về. Cố gắng sử dụng giải pháp có thời gian $O(N)$.
**Phân tích thuật toán:** Bài toán này có thể giải bằng nhiều cách, nhưng nếu biết range của phần tử nhỏ (ví dụ $-50000$ đến $50000$), ta có thể áp dụng Counting Sort. Cần tịnh tiến các số âm lên số không âm bằng một offset.
**Độ phức tạp:** Thời gian: $O(N + K)$, Không gian: $O(N + K)$ với $K$ là chênh lệch max - min.
**Mã nguồn Java:**
```java
class Solution {
    public int[] sortArray(int[] nums) {
        if (nums == null || nums.length == 0) return nums;
        int min = nums[0];
        int max = nums[0];
        for (int num : nums) {
            if (num < min) min = num;
            if (num > max) max = num;
        }
        
        int range = max - min + 1;
        int[] count = new int[range];
        for (int num : nums) {
            count[num - min]++;
        }
        
        int index = 0;
        for (int i = 0; i < range; i++) {
            while (count[i] > 0) {
                nums[index++] = i + min;
                count[i]--;
            }
        }
        return nums;
    }
}
```

## 2. Maximum Gap (Khoảng cách lớn nhất)
**Đề bài chi tiết:** Cho một mảng chưa sắp xếp, tìm khoảng cách lớn nhất giữa hai phần tử liên tiếp ở dạng đã sắp xếp của nó. Trả về 0 nếu mảng chứa ít hơn 2 phần tử. Yêu cầu chạy trong $O(N)$ thời gian và không gian.
**Phân tích thuật toán:** Dùng Bucket Sort. Dải giá trị max - min chia thành $N-1$ xô. Khoảng cách giữa các xô là `Math.ceil((max - min) / (N - 1))`. Các phần tử nằm trong cùng xô sẽ có khoảng cách nhỏ hơn khoảng cách các xô. Ta chỉ cần tìm max của xô này so với min của xô tiếp theo.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$.
**Mã nguồn Java:**
```java
class Solution {
    public int maximumGap(int[] nums) {
        if (nums == null || nums.length < 2) return 0;
        int min = nums[0], max = nums[0];
        for (int num : nums) {
            min = Math.min(min, num);
            max = Math.max(max, num);
        }
        if (min == max) return 0;
        
        int n = nums.length;
        int gap = (int) Math.ceil((double)(max - min) / (n - 1));
        int[] bucketMin = new int[n];
        int[] bucketMax = new int[n];
        Arrays.fill(bucketMin, Integer.MAX_VALUE);
        Arrays.fill(bucketMax, Integer.MIN_VALUE);
        
        for (int num : nums) {
            int idx = (num - min) / gap;
            bucketMin[idx] = Math.min(bucketMin[idx], num);
            bucketMax[idx] = Math.max(bucketMax[idx], num);
        }
        
        int maxGap = Integer.MIN_VALUE;
        int prev = min;
        for (int i = 0; i < n; i++) {
            if (bucketMin[i] == Integer.MAX_VALUE) continue; // Empty bucket
            maxGap = Math.max(maxGap, bucketMin[i] - prev);
            prev = bucketMax[i];
        }
        return maxGap;
    }
}
```

## 3. Sort Characters By Frequency (Sắp xếp kí tự theo tần suất)
**Đề bài chi tiết:** Cho chuỗi `s`, sắp xếp nó theo thứ tự giảm dần dựa trên tần suất xuất hiện của các kí tự. Tần suất của kí tự là số lần nó xuất hiện trong chuỗi.
**Phân tích thuật toán:** Đếm tần suất mỗi kí tự bằng mảng/Map. Sau đó dùng Bucket Sort, trong đó chỉ số xô đại diện cho tần suất (từ 1 đến $N$). Duyệt các xô từ lớn đến nhỏ để ghép lại chuỗi.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$.
**Mã nguồn Java:**
```java
class Solution {
    public String frequencySort(String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (char c : s.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        
        List<Character>[] bucket = new List[s.length() + 1];
        for (char key : map.keySet()) {
            int freq = map.get(key);
            if (bucket[freq] == null) {
                bucket[freq] = new ArrayList<>();
            }
            bucket[freq].add(key);
        }
        
        StringBuilder sb = new StringBuilder();
        for (int i = bucket.length - 1; i >= 0; i--) {
            if (bucket[i] != null) {
                for (char c : bucket[i]) {
                    for (int j = 0; j < i; j++) {
                        sb.append(c);
                    }
                }
            }
        }
        return sb.toString();
    }
}
```

## 4. H-Index
**Đề bài chi tiết:** Cho mảng số nguyên `citations`, trong đó `citations[i]` là số trích dẫn bài báo thứ $i$ của một nhà khoa học, tính chỉ số H-Index. Nhà khoa học có chỉ số $h$ nếu ít nhất $h$ bài báo của họ có ít nhất $h$ trích dẫn.
**Phân tích thuật toán:** Sử dụng Counting Sort (hoặc dạng Bucket). Cắt các trích dẫn vượt quá $N$ thành $N$ vì H-index không thể lớn hơn số bài báo. Tính prefix sum ngược để tìm điểm cắt H.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$.
**Mã nguồn Java:**
```java
class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int[] count = new int[n + 1];
        for (int c : citations) {
            if (c >= n) count[n]++;
            else count[c]++;
        }
        
        int total = 0;
        for (int i = n; i >= 0; i--) {
            total += count[i];
            if (total >= i) return i;
        }
        return 0;
    }
}
```

## 5. Contains Duplicate III
**Đề bài chi tiết:** Cho mảng `nums`, tìm 2 chỉ số khác biệt $i$ và $j$ sao cho `abs(nums[i] - nums[j]) <= valueDiff` và `abs(i - j) <= indexDiff`.
**Phân tích thuật toán:** Sử dụng Bucket (Xô) đóng vai trò như một cửa sổ trượt (Sliding Window). Mỗi xô có kích thước $valueDiff + 1$. Nếu hai phần tử vào cùng xô, chắc chắn thỏa mãn. Nếu chúng ở xô kề nhau, kiểm tra thủ công. Xóa phần tử cũ khi cửa sổ trượt vượt quá $indexDiff$.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(min(N, indexDiff))$.
**Mã nguồn Java:**
```java
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int indexDiff, int valueDiff) {
        if (indexDiff <= 0 || valueDiff < 0) return false;
        Map<Long, Long> buckets = new HashMap<>();
        long bucketSize = (long) valueDiff + 1;
        
        for (int i = 0; i < nums.length; i++) {
            long num = (long) nums[i];
            long bucketId = getBucketId(num, bucketSize);
            
            if (buckets.containsKey(bucketId)) return true;
            if (buckets.containsKey(bucketId - 1) && Math.abs(num - buckets.get(bucketId - 1)) < bucketSize) return true;
            if (buckets.containsKey(bucketId + 1) && Math.abs(num - buckets.get(bucketId + 1)) < bucketSize) return true;
            
            buckets.put(bucketId, num);
            if (i >= indexDiff) {
                buckets.remove(getBucketId((long) nums[i - indexDiff], bucketSize));
            }
        }
        return false;
    }
    
    private long getBucketId(long num, long size) {
        return num < 0 ? (num + 1) / size - 1 : num / size;
    }
}
```

## 6. Top K Frequent Words (Các từ xuất hiện nhiều nhất K lần)
**Đề bài chi tiết:** Cho một mảng các chuỗi `words` và số nguyên `k`, trả về k từ có tần suất xuất hiện cao nhất. Phải sắp xếp các từ cùng tần suất theo thứ tự từ điển (Lexicographical order).
**Phân tích thuật toán:** Có thể dùng Bucket Sort trong đó các bucket là list các từ. Tại mỗi xô, dùng Collections.sort hoặc PriorityQueue để sắp xếp từ điển. Cách này tốt khi phân bố tần suất có độ lệch cao (Tần suất tối đa là $N$).
**Độ phức tạp:** Thời gian $O(N + K \log K)$, Không gian $O(N)$.
**Mã nguồn Java:**
```java
class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> map = new HashMap<>();
        for (String w : words) map.put(w, map.getOrDefault(w, 0) + 1);
        
        List<String>[] bucket = new List[words.length + 1];
        for (String w : map.keySet()) {
            int freq = map.get(w);
            if (bucket[freq] == null) bucket[freq] = new ArrayList<>();
            bucket[freq].add(w);
        }
        
        List<String> res = new ArrayList<>();
        for (int i = bucket.length - 1; i >= 0 && res.size() < k; i--) {
            if (bucket[i] != null) {
                Collections.sort(bucket[i]); // Lexicographical sort
                for (String w : bucket[i]) {
                    res.add(w);
                    if (res.size() == k) return res;
                }
            }
        }
        return res;
    }
}
```

## 7. Minimum Time Difference (Độ lệch thời gian nhỏ nhất)
**Đề bài chi tiết:** Cho một danh sách các thời điểm dạng "HH:MM", trả về khoảng cách số phút nhỏ nhất giữa hai thời điểm bất kì trong danh sách.
**Phân tích thuật toán:** Chuyển thời gian thành phút (từ 0 đến 1439). Sử dụng mảng Boolean kích thước 1440 như Bucket. Nếu một bucket xuất hiện >1 lần, chênh lệch bằng 0. Quét mảng Boolean để tìm chênh lệch tối thiểu của 2 giờ gần kề và chênh lệch xoay vòng (23:59 qua 00:00).
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$ do bucket max là 1440.
**Mã nguồn Java:**
```java
class Solution {
    public int findMinDifference(List<String> timePoints) {
        boolean[] seen = new boolean[1440];
        for (String t : timePoints) {
            int h = Integer.parseInt(t.substring(0, 2));
            int m = Integer.parseInt(t.substring(3, 5));
            int mins = h * 60 + m;
            if (seen[mins]) return 0;
            seen[mins] = true;
        }
        
        int first = Integer.MAX_VALUE, last = Integer.MIN_VALUE, prev = -1;
        int minDiff = Integer.MAX_VALUE;
        
        for (int i = 0; i < 1440; i++) {
            if (seen[i]) {
                if (first == Integer.MAX_VALUE) first = i;
                last = Math.max(last, i);
                if (prev != -1) {
                    minDiff = Math.min(minDiff, i - prev);
                }
                prev = i;
            }
        }
        
        return Math.min(minDiff, 1440 - last + first);
    }
}
```

## 8. Vowel Spellchecker (Trình kiểm tra chính tả nguyên âm)
**Đề bài chi tiết:** Cho danh sách từ đúng `wordlist` và danh sách truy vấn `queries`. Áp dụng các quy tắc kiểm tra (in hoa/thường, nguyên âm thay thế) để trả về từ đúng tương ứng.
**Phân tích thuật toán:** Gom nhóm (bucketing) các từ về các định dạng chuẩn hóa bằng Hash Map. Map thứ nhất cho kiểm tra khớp 100%, Map 2 chuẩn hóa chuỗi về lowercase, Map 3 chuẩn hóa chuỗi lowercase thành mặt nạ nguyên âm (ví dụ: thay nguyên âm bằng dấu `*`).
**Độ phức tạp:** Thời gian $O(N \times L)$, Không gian $O(N \times L)$ với $L$ độ dài từ.
**Mã nguồn Java:**
```java
class Solution {
    public String[] spellchecker(String[] wordlist, String[] queries) {
        Set<String> words = new HashSet<>();
        Map<String, String> cap = new HashMap<>();
        Map<String, String> vowel = new HashMap<>();
        
        for (String w : wordlist) {
            words.add(w);
            String lower = w.toLowerCase();
            cap.putIfAbsent(lower, w);
            String devowel = lower.replaceAll("[aeiou]", "*");
            vowel.putIfAbsent(devowel, w);
        }
        
        String[] res = new String[queries.length];
        for (int i = 0; i < queries.length; i++) {
            String q = queries[i];
            if (words.contains(q)) res[i] = q;
            else if (cap.containsKey(q.toLowerCase())) res[i] = cap.get(q.toLowerCase());
            else if (vowel.containsKey(q.toLowerCase().replaceAll("[aeiou]", "*"))) res[i] = vowel.get(q.toLowerCase().replaceAll("[aeiou]", "*"));
            else res[i] = "";
        }
        return res;
    }
}
```

## 9. Car Fleet (Hạm đội xe)
**Đề bài chi tiết:** Cho các xe với vị trí ban đầu `position` và tốc độ `speed` hướng tới đích `target`. Xe chạy nhanh hơn không được vượt mà sẽ tạo hạm đội với xe chạy chậm phía trước. Tìm số lượng hạm đội xe về đích.
**Phân tích thuật toán:** Một dạng cấu trúc nhóm mảng dựa theo giá trị. Chúng ta có thể coi array/bucket dựa trên `position` (vì position là duy nhất). Tính thời gian về đích, duyệt từ gần đích về xa, nếu xe xa tốn nhiều thời gian hơn xe gần đích, nó tạo hạm đội mới.
**Độ phức tạp:** Thời gian $O(N \log N)$ (nếu array) hoặc có thể tối ưu không gian bằng Bucket với time complexity tuyến tính khi dải position không quá lớn.
**Mã nguồn Java:**
```java
class Solution {
    public int carFleet(int target, int[] position, int[] speed) {
        int n = position.length;
        double[][] cars = new double[n][2];
        for (int i = 0; i < n; i++) {
            cars[i][0] = position[i];
            cars[i][1] = (double)(target - position[i]) / speed[i];
        }
        Arrays.sort(cars, (a, b) -> Double.compare(b[0], a[0])); // Xa nhat den gan nhat
        
        int res = 0;
        double maxTime = 0;
        for (int i = 0; i < n; i++) {
            if (cars[i][1] > maxTime) {
                maxTime = cars[i][1];
                res++;
            }
        }
        return res;
    }
}
```

## 10. Max Chunks To Make Sorted (Số lượng đoạn nhiều nhất để sắp xếp)
**Đề bài chi tiết:** Cho mảng `arr` chứa hoán vị của `[0, ..., n-1]`. Chia mảng thành nhiều "khối" (chunks), sau đó sắp xếp từng khối. Lắp ghép các khối để được mảng sắp xếp. Trả về số khối tối đa có thể chia.
**Phân tích thuật toán:** Không cần so sánh mảng hay hoán vị thực sự, mà ta lợi dụng tính chất nếu giá trị lớn nhất từ `[0...i]` bằng đúng `i`, thì ta có thể cắt 1 chunk tại `i`. Tương đồng với việc theo dõi biên của bucket trong Counting logic.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public int maxChunksToSorted(int[] arr) {
        int max = 0, chunks = 0;
        for (int i = 0; i < arr.length; i++) {
            max = Math.max(max, arr[i]);
            if (max == i) {
                chunks++;
            }
        }
        return chunks;
    }
}
```

## 11. Top K Frequent Elements (K phần tử xuất hiện nhiều nhất)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và số nguyên `k`, trả về `k` phần tử xuất hiện nhiều nhất. Bạn có thể trả về câu trả lời theo bất kỳ thứ tự nào. Cố gắng đạt được độ phức tạp thời gian tốt hơn $O(N \log N)$.
**Phân tích thuật toán:** Sử dụng Bucket Sort. Đếm tần suất xuất hiện của mỗi phần tử bằng HashMap. Tạo mảng các danh sách (buckets) trong đó chỉ số mảng đại diện cho tần suất. Tần suất tối đa không vượt quá $N$. Duyệt mảng buckets từ cuối về đầu để lấy ra đúng $k$ phần tử.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$.
**Mã nguồn Java:**
```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> count = new HashMap<>();
        for (int num : nums) {
            count.put(num, count.getOrDefault(num, 0) + 1);
        }
        
        List<Integer>[] bucket = new List[nums.length + 1];
        for (int key : count.keySet()) {
            int freq = count.get(key);
            if (bucket[freq] == null) {
                bucket[freq] = new ArrayList<>();
            }
            bucket[freq].add(key);
        }
        
        int[] res = new int[k];
        int index = 0;
        for (int i = bucket.length - 1; i >= 0 && index < k; i--) {
            if (bucket[i] != null) {
                for (int num : bucket[i]) {
                    res[index++] = num;
                    if (index == k) return res;
                }
            }
        }
        return res;
    }
}
```

## 12. Wiggle Sort II (Sắp xếp lượn sóng II)
**Đề bài chi tiết:** Cho mảng số nguyên `nums`, hãy sắp xếp lại nó theo thứ tự lượn sóng, tức là `nums[0] < nums[1] > nums[2] < nums[3]...`.
**Phân tích thuật toán:** Nếu khoảng giá trị của mảng nhỏ (ví dụ tối đa 5000 như trong nhiều test cases thực tế), chúng ta có thể sử dụng Counting Sort (mảng đếm). Sau khi đếm tần suất, điền các giá trị từ lớn đến nhỏ vào các vị trí lẻ trước (vì chúng cần lớn hơn), sau đó điền phần còn lại vào các vị trí chẵn.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(K)$ với $K$ là dải giá trị.
**Mã nguồn Java:**
```java
class Solution {
    public void wiggleSort(int[] nums) {
        int[] count = new int[5001];
        for (int num : nums) {
            count[num]++;
        }
        
        int maxVal = 5000;
        // Điền các vị trí lẻ với các số lớn
        for (int i = 1; i < nums.length; i += 2) {
            while (count[maxVal] == 0) maxVal--;
            nums[i] = maxVal;
            count[maxVal]--;
        }
        
        // Điền các vị trí chẵn với các số nhỏ hơn
        for (int i = 0; i < nums.length; i += 2) {
            while (count[maxVal] == 0) maxVal--;
            nums[i] = maxVal;
            count[maxVal]--;
        }
    }
}
```

## 13. Maximum Frequency Stack (Ngăn xếp tần suất tối đa)
**Đề bài chi tiết:** Thiết kế một ngăn xếp (`FreqStack`) hỗ trợ thao tác đẩy (push) và rút (pop) phần tử có tần suất xuất hiện nhiều nhất. Nếu có nhiều phần tử cùng tần suất tối đa, phần tử gần nhất với đỉnh ngăn xếp sẽ được rút ra trước.
**Phân tích thuật toán:** Sử dụng Map lưu tần suất của các phần tử và một Map of Stacks (đóng vai trò như các Buckets) trong đó khóa là tần suất, giá trị là một stack chứa các phần tử có cùng tần suất đó. Duy trì biến `maxFreq` lưu tần suất cao nhất hiện tại để đảm bảo rút $O(1)$.
**Độ phức tạp:** Thời gian $O(1)$ cho mỗi thao tác, Không gian $O(N)$.
**Mã nguồn Java:**
```java
class FreqStack {
    Map<Integer, Integer> freq;
    Map<Integer, Stack<Integer>> group;
    int maxFreq;

    public FreqStack() {
        freq = new HashMap<>();
        group = new HashMap<>();
        maxFreq = 0;
    }
    
    public void push(int val) {
        int f = freq.getOrDefault(val, 0) + 1;
        freq.put(val, f);
        if (f > maxFreq) {
            maxFreq = f;
        }
        group.computeIfAbsent(f, z -> new Stack<>()).push(val);
    }
    
    public int pop() {
        int x = group.get(maxFreq).pop();
        freq.put(x, freq.get(x) - 1);
        if (group.get(maxFreq).size() == 0) {
            maxFreq--;
        }
        return x;
    }
}
```

## 14. Make Two Arrays Equal by Reversing Sub-arrays (Làm cho hai mảng bằng nhau)
**Đề bài chi tiết:** Cho hai mảng số nguyên `target` và `arr` có cùng độ dài. Bạn có thể chọn bất kỳ mảng con nào của `arr` và đảo ngược nó với số lần tùy ý. Trả về `true` nếu bạn có thể biến `arr` thành `target`, ngược lại `false`.
**Phân tích thuật toán:** Việc đảo ngược mảng con nhiều lần tương đương với việc hoán vị `arr` thành bất kì thứ tự nào. Do đó, bài toán chỉ là kiểm tra xem hai mảng có chứa các phần tử giống nhau với cùng tần suất hay không. Có thể dùng mảng đếm (Counting) do giá trị tối đa là 1000.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$ (dải phần tử nhỏ cố định $1000$).
**Mã nguồn Java:**
```java
class Solution {
    public boolean canBeEqual(int[] target, int[] arr) {
        int[] count = new int[1001];
        for (int i = 0; i < target.length; i++) {
            count[target[i]]++;
            count[arr[i]]--;
        }
        for (int c : count) {
            if (c != 0) return false;
        }
        return true;
    }
}
```

## 15. Sort Array By Parity (Sắp xếp mảng theo tính chẵn lẻ)
**Đề bài chi tiết:** Cho mảng số nguyên `nums`, hãy sắp xếp lại các phần tử sao cho tất cả các phần tử chẵn nằm phía trước tất cả các phần tử lẻ.
**Phân tích thuật toán:** Đây là phiên bản rút gọn của bài toán phân lô (Bucketing/Partitioning). Dùng kỹ thuật Hai con trỏ (Two Pointers) từ hai đầu. Nếu gặp lẻ ở bên trái và chẵn ở bên phải thì hoán đổi chúng.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public int[] sortArrayByParity(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            if (nums[left] % 2 > nums[right] % 2) {
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
            }
            if (nums[left] % 2 == 0) left++;
            if (nums[right] % 2 == 1) right--;
        }
        return nums;
    }
}
```

## 16. Sort Array By Parity II (Sắp xếp mảng theo tính chẵn lẻ II)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` có nửa chẵn, nửa lẻ. Sắp xếp lại sao cho phần tử ở chỉ số `i` là chẵn nếu `i` chẵn, và là lẻ nếu `i` lẻ.
**Phân tích thuật toán:** Tiếp tục ý tưởng in-place Partitioning/Bucketing. Dùng hai con trỏ, con trỏ `i` duyệt các vị trí chẵn, con trỏ `j` duyệt các vị trí lẻ. Tìm các phần tử đặt sai vị trí ở cả hai con trỏ và hoán đổi chúng cho nhau.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public int[] sortArrayByParityII(int[] nums) {
        int i = 0, j = 1;
        int n = nums.length;
        while (i < n && j < n) {
            while (i < n && nums[i] % 2 == 0) {
                i += 2;
            }
            while (j < n && nums[j] % 2 == 1) {
                j += 2;
            }
            if (i < n && j < n) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
            }
        }
        return nums;
    }
}
```

## 17. K-th Smallest Prime Fraction (Phân số nguyên tố nhỏ thứ K)
**Đề bài chi tiết:** Cho mảng đã sắp xếp `arr` chứa giá trị $1$ và các số nguyên tố, cùng với số nguyên `k`. Xét tất cả các phân số tạo bởi $arr[i] / arr[j]$ với $i < j$. Trả về mảng 2 phần tử chứa tử số và mẫu số của phân số nhỏ thứ `k`.
**Phân tích thuật toán:** Tuy không sử dụng Counting Sort/Bucket, chúng ta dùng Binary Search trên miền giá trị $[0, 1]$ của phân số để "đếm" (counting) số lượng các phân số nhỏ hơn một giá trị `mid`. Nếu số lượng bằng `k` thì ta đã tìm ra câu trả lời.
**Độ phức tạp:** Thời gian $O(N \log(1/eps))$, Không gian $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        double left = 0, right = 1.0;
        int n = arr.length;
        while (left < right) {
            double mid = (left + right) / 2;
            double maxFrac = 0.0;
            int total = 0, p = 0, q = 0;
            int j = 1;
            
            for (int i = 0; i < n - 1; i++) {
                while (j < n && arr[i] > mid * arr[j]) {
                    j++;
                }
                total += (n - j);
                if (j < n && maxFrac < (double)arr[i] / arr[j]) {
                    maxFrac = (double)arr[i] / arr[j];
                    p = i;
                    q = j;
                }
            }
            
            if (total == k) {
                return new int[]{arr[p], arr[q]};
            } else if (total > k) {
                right = mid;
            } else {
                left = mid;
            }
        }
        return new int[]{};
    }
}
```

## 18. Custom Sort String (Sắp xếp chuỗi tùy chỉnh)
**Đề bài chi tiết:** Cho chuỗi `order` chứa các kí tự duy nhất được sắp xếp thứ tự tùy chỉnh, và chuỗi `s`. Hãy hoán vị `s` sao cho thứ tự các kí tự tuân theo chuỗi `order`. Các kí tự không có trong `order` có thể đặt ở bất kì vị trí nào.
**Phân tích thuật toán:** Đây là một dạng Counting Sort. Đếm tần suất tất cả các kí tự trong `s`. Sau đó duyệt lần lượt từng kí tự trong `order`, thêm vào kết quả ứng với tần suất đếm được và trừ đi. Cuối cùng nối thêm các kí tự còn thừa vào đuôi.
**Độ phức tạp:** Thời gian $O(N + M)$ với $N = |s|, M = |order|$, Không gian $O(1)$ (mảng 26 kí tự).
**Mã nguồn Java:**
```java
class Solution {
    public String customSortString(String order, String s) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) {
            count[c - 'a']++;
        }
        
        StringBuilder sb = new StringBuilder();
        for (char c : order.toCharArray()) {
            while (count[c - 'a'] > 0) {
                sb.append(c);
                count[c - 'a']--;
            }
        }
        
        // Thêm các kí tự không có trong order
        for (int i = 0; i < 26; i++) {
            while (count[i] > 0) {
                sb.append((char)('a' + i));
                count[i]--;
            }
        }
        
        return sb.toString();
    }
}
```

## 19. Array Partition I (Phân chia mảng I)
**Đề bài chi tiết:** Cho mảng `nums` gồm $2n$ số nguyên, hãy chia nó thành $n$ cặp, sao cho tổng các $min(a_i, b_i)$ của tất cả các cặp là lớn nhất.
**Phân tích thuật toán:** Bài toán yêu cầu sắp xếp mảng tăng dần rồi lấy tổng các phần tử ở vị trí chẵn. Vì dải giá trị là $[-10^4, 10^4]$, ta dùng mảng đếm (Counting Sort) kích thước 20001 để đạt thời gian $O(N)$ thay vì $O(N \log N)$ so sánh.
**Độ phức tạp:** Thời gian $O(N + K)$ với $K = 20000$, Không gian $O(K)$.
**Mã nguồn Java:**
```java
class Solution {
    public int arrayPairSum(int[] nums) {
        int[] count = new int[20001];
        for (int num : nums) {
            count[num + 10000]++;
        }
        
        int sum = 0;
        boolean take = true; // Biến cờ để chỉ lấy phần tử min của cặp
        for (int i = 0; i < count.length; i++) {
            while (count[i] > 0) {
                if (take) {
                    sum += (i - 10000);
                }
                take = !take;
                count[i]--;
            }
        }
        return sum;
    }
}
```

## 20. Largest Number (Số lớn nhất)
**Đề bài chi tiết:** Cho mảng các số nguyên không âm `nums`, hãy sắp xếp chúng sao cho khi nối lại với nhau chúng tạo thành số lớn nhất.
**Phân tích thuật toán:** Thông thường bài toán sắp xếp chuỗi nối này được giải bằng cách cung cấp một Custom Comparator để so sánh hai chuỗi $a+b$ và $b+a$. Tuy nhiên ta cũng có thể coi đây là kĩ thuật sắp xếp nhóm theo luồng kí tự. Ở đây trình bày thuật toán chuẩn với `Comparator` để dễ dàng implement trong Java với quy tắc so sánh tự tạo.
**Độ phức tạp:** Thời gian $O(N \log N \cdot L)$, Không gian $O(N \cdot L)$ với $L$ là độ dài trung bình chuỗi.
**Mã nguồn Java:**
```java
class Solution {
    public String largestNumber(int[] nums) {
        String[] strs = new String[nums.length];
        for (int i = 0; i < nums.length; i++) {
            strs[i] = String.valueOf(nums[i]);
        }
        
        Arrays.sort(strs, (a, b) -> (b + a).compareTo(a + b));
        
        // Xử lý trường hợp mảng toàn số 0
        if (strs[0].equals("0")) {
            return "0";
        }
        
        StringBuilder sb = new StringBuilder();
        for (String s : strs) {
            sb.append(s);
        }
        return sb.toString();
    }
}
```

## 21. Reorganize String (Tổ chức lại chuỗi)
**Đề bài chi tiết:** Cho chuỗi `s`, sắp xếp lại các kí tự của chuỗi sao cho không có hai kí tự liền kề nào giống nhau. Nếu không thể làm được, trả về chuỗi rỗng.
**Phân tích thuật toán:** Đếm tần suất các kí tự. Dùng một mảng để tìm kí tự xuất hiện nhiều nhất. Điền kí tự này cách quãng (vị trí 0, 2, 4...) trước vào mảng kết quả. Sau đó, điền các kí tự còn lại vào các vị trí chẵn còn trống trước, rồi mới đến các vị trí lẻ. Đây là sự kết hợp của đếm tần suất (Counting Sort) và đặt xen kẽ vào các vị trí.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$ để lưu chuỗi kết quả.
**Mã nguồn Java:**
```java
class Solution {
    public String reorganizeString(String s) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) {
            count[c - 'a']++;
        }
        
        int maxFreq = 0, letter = 0;
        for (int i = 0; i < 26; i++) {
            if (count[i] > maxFreq) {
                maxFreq = count[i];
                letter = i;
            }
        }
        
        if (maxFreq > (s.length() + 1) / 2) return "";
        
        char[] res = new char[s.length()];
        int idx = 0;
        while (count[letter] > 0) {
            res[idx] = (char) (letter + 'a');
            idx += 2;
            count[letter]--;
        }
        
        for (int i = 0; i < 26; i++) {
            while (count[i] > 0) {
                if (idx >= res.length) {
                    idx = 1; // Hết chỉ số chẵn, nhảy sang lẻ
                }
                res[idx] = (char) (i + 'a');
                idx += 2;
                count[i]--;
            }
        }
        
        return new String(res);
    }
}
```

## 22. First Missing Positive (Số dương thiếu đầu tiên)
**Đề bài chi tiết:** Cho mảng số nguyên chưa sắp xếp `nums`, tìm số nguyên dương nhỏ nhất bị thiếu. Giải thuật phải chạy trong thời gian $O(n)$ và sử dụng không gian bộ nhớ phụ $O(1)$.
**Phân tích thuật toán:** Thuật toán Cyclic Sort in-place (sắp xếp vòng). Coi chính mảng như một dãy các bucket. Mỗi số $x$ trong khoảng $[1, N]$ sẽ được đặt vào vị trí (bucket) $x-1$. Sau khi đổi chỗ (swap) xong, ta chỉ việc duyệt lại từ đầu, chỉ số đầu tiên $i$ mà `nums[i] != i + 1` chính là số bị thiếu.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            while (nums[i] > 0 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                int temp = nums[nums[i] - 1];
                nums[nums[i] - 1] = nums[i];
                nums[i] = temp;
            }
        }
        
        for (int i = 0; i < n; i++) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        return n + 1;
    }
}
```

## 23. Find All Duplicates in an Array (Tìm tất cả các bản sao trong mảng)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` có độ dài $n$, tất cả các phần tử nằm trong khoảng $[1, n]$. Mỗi số nguyên xuất hiện một hoặc hai lần. Trả về mảng chứa tất cả các phần tử xuất hiện hai lần. Yêu cầu $O(n)$ thời gian và $O(1)$ không gian phụ.
**Phân tích thuật toán:** Tận dụng chính mảng đầu vào làm bảng tần suất bằng cách lưu dấu. Khi duyệt đến một số $x$, ta lấy giá trị tuyệt đối $|x|$, tìm đến bucket ứng với chỉ số $|x| - 1$ và đổi giá trị tại đó thành số âm. Nếu tại vị trí đó đã âm sẵn, tức là ta đã từng thăm nó, suy ra số $|x|$ bị lặp.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$ (bỏ qua mảng kết quả).
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            int index = Math.abs(nums[i]) - 1;
            if (nums[index] < 0) {
                res.add(index + 1);
            } else {
                nums[index] = -nums[index];
            }
        }
        return res;
    }
}
```

## 24. Find the Duplicate Number (Tìm số trùng lặp)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` chứa $n+1$ số nguyên trong đó mỗi phần tử thuộc khoảng $[1, n]$. Có duy nhất một phần tử bị lặp lại, hãy tìm số đó. Yêu cầu không được sửa đổi mảng và phải dùng $O(1)$ bộ nhớ phụ.
**Phân tích thuật toán:** Do cấm sửa đổi mảng, ta không thể dùng kĩ thuật đánh dấu âm. Dùng con trỏ rùa và thỏ (Floyd's Cycle Detection). Coi mảng như một danh sách liên kết nơi `nums[i]` trỏ đến `nums[nums[i]]`. Điểm giao nhau của chu trình chính là số bị lặp.
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[0];
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (slow != fast);
        
        slow = nums[0]; // Bắt đầu lại một con trỏ từ đầu để tìm điểm vào chu trình
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        return slow;
    }
}
```

## 25. Sort Items by Groups Respecting Dependencies (Sắp xếp theo nhóm tôn trọng quan hệ phụ thuộc)
**Đề bài chi tiết:** Có $n$ phần tử, một số thuộc các nhóm, số khác không thuộc nhóm. Bạn được cấp mảng sự phụ thuộc `beforeItems` để quy định thứ tự trước/sau. Hãy sắp xếp sao cho các phần tử cùng nhóm đứng cạnh nhau và tất cả mọi phụ thuộc đều được thỏa mãn.
**Phân tích thuật toán:** Áp dụng Topological Sort (sắp xếp tô-pô). Bước mấu chốt là tạo đồ thị và theo dõi độ bán bậc vào (indegree) ở cả hai cấp độ: cấp Nhóm (Group) và cấp Phần tử (Item). Nhóm các phần tử không có nhóm vào các nhóm ảo. Xếp hạng Topo cho nhóm, xếp hạng Topo cho phần tử. Ráp các phần tử vào thứ tự của nhóm.
**Độ phức tạp:** Thời gian $O(N + M + E)$, Không gian $O(N + M + E)$ với $E$ là số cạnh phụ thuộc.
**Mã nguồn Java:**
```java
class Solution {
    public int[] sortItems(int n, int m, int[] group, List<List<Integer>> beforeItems) {
        for (int i = 0; i < n; i++) {
            if (group[i] == -1) group[i] = m++;
        }
        
        List<Integer>[] groupGraph = new ArrayList[m];
        List<Integer>[] itemGraph = new ArrayList[n];
        int[] groupIndegree = new int[m];
        int[] itemIndegree = new int[n];
        
        for (int i = 0; i < m; i++) groupGraph[i] = new ArrayList<>();
        for (int i = 0; i < n; i++) itemGraph[i] = new ArrayList<>();
        
        for (int i = 0; i < n; i++) {
            for (int prev : beforeItems.get(i)) {
                itemGraph[prev].add(i);
                itemIndegree[i]++;
                if (group[i] != group[prev]) {
                    groupGraph[group[prev]].add(group[i]);
                    groupIndegree[group[i]]++;
                }
            }
        }
        
        List<Integer> groupOrder = topoSort(groupGraph, groupIndegree, m);
        List<Integer> itemOrder = topoSort(itemGraph, itemIndegree, n);
        
        if (groupOrder.isEmpty() || itemOrder.isEmpty()) return new int[0];
        
        List<Integer>[] orderedGroups = new ArrayList[m];
        for (int i = 0; i < m; i++) orderedGroups[i] = new ArrayList<>();
        for (int item : itemOrder) {
            orderedGroups[group[item]].add(item);
        }
        
        int[] res = new int[n];
        int index = 0;
        for (int g : groupOrder) {
            for (int item : orderedGroups[g]) {
                res[index++] = item;
            }
        }
        return res;
    }
    
    private List<Integer> topoSort(List<Integer>[] graph, int[] indegree, int n) {
        List<Integer> res = new ArrayList<>();
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            if (indegree[i] == 0) q.offer(i);
        }
        
        while (!q.isEmpty()) {
            int u = q.poll();
            res.add(u);
            for (int v : graph[u]) {
                if (--indegree[v] == 0) q.offer(v);
            }
        }
        return res.size() == n ? res : new ArrayList<>();
    }
}
```

## 26. K Closest Points to Origin (K điểm gần gốc tọa độ nhất)
**Đề bài chi tiết:** Cho mảng `points` trong đó `points[i] = [x, y]` đại diện cho một điểm trên mặt phẳng 2D, và một số nguyên `k`. Trả về `k` điểm gần gốc tọa độ (0,0) nhất. Khoảng cách được tính bằng khoảng cách Euclid.
**Phân tích thuật toán:** Một dạng bài phân vùng (partition) khoảng cách. Giải pháp QuickSelect trung bình đạt $O(N)$ bằng cách chia để trị lấy phân vùng trái thay vì sắp xếp toàn bộ.
**Độ phức tạp:** Thời gian trung bình $O(N)$, xấu nhất $O(N^2)$, Không gian $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public int[][] kClosest(int[][] points, int k) {
        quickSelect(points, 0, points.length - 1, k);
        return Arrays.copyOfRange(points, 0, k);
    }
    
    private void quickSelect(int[][] points, int left, int right, int k) {
        if (left >= right) return;
        int pivotIndex = partition(points, left, right);
        if (pivotIndex == k) {
            return;
        } else if (pivotIndex < k) {
            quickSelect(points, pivotIndex + 1, right, k);
        } else {
            quickSelect(points, left, pivotIndex - 1, k);
        }
    }
    
    private int partition(int[][] points, int left, int right) {
        int[] pivot = points[right];
        int pivotDist = dist(pivot);
        int i = left;
        for (int j = left; j < right; j++) {
            if (dist(points[j]) <= pivotDist) {
                swap(points, i, j);
                i++;
            }
        }
        swap(points, i, right);
        return i;
    }
    
    private int dist(int[] point) {
        return point[0] * point[0] + point[1] * point[1];
    }
    
    private void swap(int[][] points, int i, int j) {
        int[] temp = points[i];
        points[i] = points[j];
        points[j] = temp;
    }
}
```

## 27. Number of Matching Subsequences (Số lượng chuỗi con khớp)
**Đề bài chi tiết:** Cho chuỗi `s` và mảng chuỗi `words`, trả về số lượng từ `words[i]` là chuỗi con (subsequence) của `s`.
**Phân tích thuật toán:** Ứng dụng Bucketing. Ta tạo 26 buckets, mỗi bucket đại diện cho một chữ cái. Xếp các từ vào bucket dựa trên chữ cái đầu tiên của nó. Duyệt qua từng kí tự của `s`, ta "mở khóa" bucket tương ứng, tiến mỗi từ trong đó lên 1 kí tự, rồi xếp lại vào bucket dựa trên kí tự kế tiếp. Kĩ thuật này gọi là Next Letter Pointers.
**Độ phức tạp:** Thời gian $O(|S| + \sum |words[i]|)$, Không gian $O(\text{số lượng từ})$.
**Mã nguồn Java:**
```java
class Solution {
    public int numMatchingSubseq(String s, String[] words) {
        List<String>[] waiting = new List[26];
        for (int i = 0; i < 26; i++) {
            waiting[i] = new ArrayList<>();
        }
        
        for (String w : words) {
            waiting[w.charAt(0) - 'a'].add(w);
        }
        
        int count = 0;
        for (char c : s.toCharArray()) {
            List<String> advance = waiting[c - 'a'];
            waiting[c - 'a'] = new ArrayList<>(); // Làm rỗng bucket hiện tại
            for (String word : advance) {
                if (word.length() == 1) {
                    count++; // Đã khớp hết
                } else {
                    String nextStr = word.substring(1);
                    waiting[nextStr.charAt(0) - 'a'].add(nextStr);
                }
            }
        }
        return count;
    }
}
```

## 28. Group Anagrams (Nhóm các chuỗi đảo chữ)
**Đề bài chi tiết:** Cho mảng chuỗi `strs`, gom nhóm các anagram (chuỗi đảo chữ) lại với nhau thành các danh sách. Có thể trả về kết quả theo thứ tự bất kì.
**Phân tích thuật toán:** Một dạng nhóm tần suất đếm. Đếm số lượng kí tự của mỗi chuỗi (giống Counting Sort cho bảng chữ cái). Chuyển cấu hình tần suất đếm thành một chuỗi đặc trưng (hash string key) rồi đưa vào Hash Map (như các bucket).
**Độ phức tạp:** Thời gian $O(N \cdot K)$ với $N$ là số lượng chuỗi, $K$ là độ dài cực đại của mỗi chuỗi; Không gian $O(N \cdot K)$.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for (String s : strs) {
            int[] count = new int[26];
            for (char c : s.toCharArray()) {
                count[c - 'a']++;
            }
            
            // Xây dựng khóa đại diện
            StringBuilder keyBuilder = new StringBuilder();
            for (int i = 0; i < 26; i++) {
                keyBuilder.append('#');
                keyBuilder.append(count[i]);
            }
            String key = keyBuilder.toString();
            
            if (!map.containsKey(key)) {
                map.put(key, new ArrayList<>());
            }
            map.get(key).add(s);
        }
        
        return new ArrayList<>(map.values());
    }
}
```

## 29. Minimum Number of Arrows to Burst Balloons (Số mũi tên tối thiểu để bắn nổ bóng)
**Đề bài chi tiết:** Một mảng `points` đại diện cho tọa độ trục hoành của các quả bóng bay. Một mũi tên bắn theo phương thẳng đứng tại $x$ sẽ làm nổ quả bóng $i$ nếu $points[i][0] \le x \le points[i][1]$. Tìm số mũi tên tối thiểu.
**Phân tích thuật toán:** Đây là bài toán đoạn chéo nhau (Overlapping Intervals). Ta sắp xếp theo điểm kết thúc sớm của bóng bay. Kế tiếp, duyệt với tham lam (Greedy) để bắn mũi tên vào ngay cuối của bóng bay hiện tại nhằm tối đa hóa khả năng bắn vỡ bóng kế tiếp.
**Độ phức tạp:** Thời gian $O(N \log N)$ (chủ yếu cho sắp xếp), Không gian $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public int findMinArrowShots(int[][] points) {
        if (points == null || points.length == 0) return 0;
        
        Arrays.sort(points, (a, b) -> Integer.compare(a[1], b[1]));
        
        int arrows = 1;
        int end = points[0][1];
        
        for (int i = 1; i < points.length; i++) {
            if (points[i][0] > end) { // Bóng bay này vượt khỏi tầm mũi tên
                arrows++;
                end = points[i][1];
            }
        }
        return arrows;
    }
}
```

## 30. Divide Array in Sets of K Consecutive Numbers (Chia mảng thành các tập k số liên tiếp)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và số nguyên dương `k`. Kiểm tra xem mảng có thể chia thành một hoặc nhiều tập con, mỗi tập gồm `k` số liên tiếp hay không.
**Phân tích thuật toán:** Sử dụng TreeMap để đếm tần suất các số trong mảng, tạo cấu trúc khóa đã sắp xếp. Lần lượt lấy các khóa nhỏ nhất có tần suất dương, trừ đi các giá trị cho $k$ khóa liên tiếp. Nếu một mắt xích thiếu tần suất, trả về false.
**Độ phức tạp:** Thời gian $O(N \log M)$ với $M$ là số lượng khóa phân biệt, Không gian $O(M)$.
**Mã nguồn Java:**
```java
class Solution {
    public boolean isPossibleDivide(int[] nums, int k) {
        if (nums.length % k != 0) return false;
        
        TreeMap<Integer, Integer> count = new TreeMap<>();
        for (int num : nums) {
            count.put(num, count.getOrDefault(num, 0) + 1);
        }
        
        for (int key : count.keySet()) {
            int freq = count.get(key);
            if (freq > 0) {
                for (int i = 0; i < k; i++) {
                    int next = key + i;
                    if (count.getOrDefault(next, 0) < freq) {
                        return false;
                    }
                    count.put(next, count.get(next) - freq);
                }
            }
        }
        return true;
    }
}
```
