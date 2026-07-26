# Bài tập: Design Hash Data Structures

## 1. Design HashSet (LeetCode 705)
**Đề bài chi tiết:**
Thiết kế một cấu trúc dữ liệu HashSet không sử dụng bất kỳ thư viện hash table có sẵn nào.
Bao gồm các hàm:
- `void add(key)`: Thêm giá trị key vào set.
- `boolean contains(key)`: Trả về true nếu key có trong set, ngược lại trả về false.
- `void remove(key)`: Xóa key khỏi set nếu nó tồn tại.

**Phân tích thuật toán:**
Để thiết kế HashSet, ta sử dụng một mảng có kích thước cố định (ví dụ 10^4). Mỗi bucket của mảng sẽ chứa một danh sách liên kết hoặc một cấu trúc dữ liệu phụ (ở đây ta dùng LinkedList) để giải quyết xung đột (Separate Chaining). Hàm băm sẽ là `key % 10000`.

**Mã nguồn Java:**
```java
class MyHashSet {
    private int buckets = 10000;
    private List<Integer>[] table;

    public MyHashSet() {
        table = new LinkedList[buckets];
    }
    
    private int hash(int key) {
        return key % buckets;
    }
    
    public void add(int key) {
        int i = hash(key);
        if (table[i] == null) {
            table[i] = new LinkedList<>();
        }
        if (table[i].indexOf(key) == -1) {
            table[i].add(key);
        }
    }
    
    public void remove(int key) {
        int i = hash(key);
        if (table[i] != null) {
            int pos = table[i].indexOf(key);
            if (pos != -1) {
                table[i].remove(pos);
            }
        }
    }
    
    public boolean contains(int key) {
        int i = hash(key);
        if (table[i] == null) return false;
        return table[i].indexOf(key) != -1;
    }
}
```

**Độ phức tạp:**
- Thời gian: O(1) trung bình cho add, remove, contains. O(N) trường hợp xấu nhất.
- Không gian: O(K + M) với K là số lượng buckets, M là số lượng unique keys được add.

---

## 2. Design HashMap (LeetCode 706)
**Đề bài chi tiết:**
Thiết kế một cấu trúc dữ liệu HashMap không sử dụng thư viện có sẵn.
Bao gồm các hàm:
- `put(key, value)`: Cập nhật giá trị nếu key tồn tại, thêm mới nếu chưa tồn tại.
- `get(key)`: Lấy giá trị theo key, trả về -1 nếu không tồn tại.
- `remove(key)`: Xóa cặp key-value khỏi map.

**Phân tích thuật toán:**
Tương tự HashSet, nhưng thay vì chỉ lưu key, ta lưu trữ một đối tượng Node chứa cả key và value. Sử dụng Separate Chaining với mảng các LinkedList.

**Mã nguồn Java:**
```java
class MyHashMap {
    class Node {
        int key, val;
        Node(int k, int v) { key = k; val = v; }
    }
    
    private int buckets = 10000;
    private List<Node>[] table;

    public MyHashMap() {
        table = new LinkedList[buckets];
    }
    
    private int hash(int key) {
        return key % buckets;
    }
    
    public void put(int key, int value) {
        int i = hash(key);
        if (table[i] == null) {
            table[i] = new LinkedList<>();
        }
        for (Node node : table[i]) {
            if (node.key == key) {
                node.val = value;
                return;
            }
        }
        table[i].add(new Node(key, value));
    }
    
    public int get(int key) {
        int i = hash(key);
        if (table[i] == null) return -1;
        for (Node node : table[i]) {
            if (node.key == key) return node.val;
        }
        return -1;
    }
    
    public void remove(int key) {
        int i = hash(key);
        if (table[i] == null) return;
        for (Node node : table[i]) {
            if (node.key == key) {
                table[i].remove(node);
                return;
            }
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: O(1) trung bình cho put, get, remove. O(N/K) với N phần tử và K buckets.
- Không gian: O(K + N) với K buckets và N phần tử được lưu trữ.

---

## 3. LRU Cache (LeetCode 146)
**Đề bài chi tiết:**
Thiết kế một Least Recently Used (LRU) Cache.
- `LRUCache(int capacity)`: Khởi tạo cache với capacity cho trước.
- `int get(int key)`: Trả về value của key, ngược lại -1.
- `void put(int key, int value)`: Cập nhật hoặc chèn (key, value). Nếu vượt capacity, xóa phần tử ít được sử dụng nhất gần đây. O(1) time complexity.

**Phân tích thuật toán:**
Để đạt O(1), ta sử dụng `HashMap` (lưu trữ con trỏ tới Node) và một `Doubly Linked List` (lưu thứ tự truy cập). Head của List là Node mới dùng gần nhất (MRU), Tail là Node ít dùng nhất (LRU).

**Mã nguồn Java:**
```java
class LRUCache {
    class Node {
        int key, value;
        Node prev, next;
        Node(int k, int v) { key = k; value = v; }
    }
    
    private int capacity;
    private Map<Integer, Node> map;
    private Node head, tail;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        map = new HashMap<>();
        head = new Node(-1, -1);
        tail = new Node(-1, -1);
        head.next = tail;
        tail.prev = head;
    }
    
    private void addNode(Node node) {
        Node next = head.next;
        head.next = node;
        node.prev = head;
        node.next = next;
        next.prev = node;
    }
    
    private void removeNode(Node node) {
        Node prev = node.prev;
        Node next = node.next;
        prev.next = next;
        next.prev = prev;
    }
    
    private void moveToHead(Node node) {
        removeNode(node);
        addNode(node);
    }
    
    public int get(int key) {
        if (!map.containsKey(key)) return -1;
        Node node = map.get(key);
        moveToHead(node);
        return node.value;
    }
    
    public void put(int key, int value) {
        if (map.containsKey(key)) {
            Node node = map.get(key);
            node.value = value;
            moveToHead(node);
        } else {
            Node newNode = new Node(key, value);
            map.put(key, newNode);
            addNode(newNode);
            if (map.size() > capacity) {
                Node tailPrev = tail.prev;
                removeNode(tailPrev);
                map.remove(tailPrev.key);
            }
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: O(1) cho cả get và put.
- Không gian: O(capacity).

---

## 4. LFU Cache (LeetCode 460)
**Đề bài chi tiết:**
Thiết kế Least Frequently Used (LFU) Cache.
- `LFUCache(int capacity)`: Khởi tạo.
- `int get(int key)`: Trả về value, tăng tần suất.
- `void put(int key, int value)`: Chèn hoặc cập nhật, tăng tần suất. Nếu vượt quá capacity, loại bỏ phần tử có tần suất ít nhất (nếu hòa thì loại theo LRU). O(1) time complexity.

**Phân tích thuật toán:**
Dùng 2 HashMaps: `keyToNode` map key tới Node, `freqToDLL` map tần suất tới một Doubly Linked List chứa các node có cùng freq. Cần duy trì biến `minFreq`.

**Mã nguồn Java:**
```java
class LFUCache {
    class Node {
        int key, val, freq;
        Node prev, next;
        Node(int k, int v) { key = k; val = v; freq = 1; }
    }
    
    class DLL {
        Node head, tail;
        int size;
        DLL() {
            head = new Node(-1, -1);
            tail = new Node(-1, -1);
            head.next = tail; tail.prev = head;
        }
        void addNode(Node node) {
            Node next = head.next;
            head.next = node; node.prev = head;
            node.next = next; next.prev = node;
            size++;
        }
        void removeNode(Node node) {
            Node prev = node.prev; Node next = node.next;
            prev.next = next; next.prev = prev;
            size--;
        }
        Node removeTail() {
            if (size > 0) {
                Node tailNode = tail.prev;
                removeNode(tailNode);
                return tailNode;
            }
            return null;
        }
    }

    private int capacity, minFreq;
    private Map<Integer, Node> keyNodeMap;
    private Map<Integer, DLL> freqDLLMap;

    public LFUCache(int capacity) {
        this.capacity = capacity;
        keyNodeMap = new HashMap<>();
        freqDLLMap = new HashMap<>();
    }
    
    private void update(Node node) {
        int freq = node.freq;
        DLL dll = freqDLLMap.get(freq);
        dll.removeNode(node);
        if (freq == minFreq && dll.size == 0) minFreq++;
        
        node.freq++;
        freqDLLMap.computeIfAbsent(node.freq, k -> new DLL()).addNode(node);
    }

    public int get(int key) {
        if (capacity == 0 || !keyNodeMap.containsKey(key)) return -1;
        Node node = keyNodeMap.get(key);
        update(node);
        return node.val;
    }
    
    public void put(int key, int value) {
        if (capacity == 0) return;
        if (keyNodeMap.containsKey(key)) {
            Node node = keyNodeMap.get(key);
            node.val = value;
            update(node);
        } else {
            if (keyNodeMap.size() == capacity) {
                DLL minDLL = freqDLLMap.get(minFreq);
                Node removed = minDLL.removeTail();
                keyNodeMap.remove(removed.key);
            }
            Node newNode = new Node(key, value);
            keyNodeMap.put(key, newNode);
            minFreq = 1;
            freqDLLMap.computeIfAbsent(1, k -> new DLL()).addNode(newNode);
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: O(1) cho get và put.
- Không gian: O(capacity).

---

## 5. Insert Delete GetRandom O(1) (LeetCode 380)
**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu `RandomizedSet`.
- `insert(val)`: Chèn một giá trị vào set nếu chưa tồn tại.
- `remove(val)`: Xóa một giá trị nếu nó tồn tại.
- `getRandom()`: Trả về một giá trị ngẫu nhiên trong tập, các phần tử có xác suất ngang nhau. O(1) time complexity.

**Phân tích thuật toán:**
Dùng `ArrayList` để lưu các số để `getRandom` nhanh. Dùng `HashMap` lưu mapping từ giá trị sang index trong danh sách. Để xóa O(1), ta swap phần tử cần xóa với phần tử cuối mảng, sau đó xóa phần tử cuối mảng và cập nhật lại index trong map.

**Mã nguồn Java:**
```java
class RandomizedSet {
    private Map<Integer, Integer> map;
    private List<Integer> list;
    private Random rand;

    public RandomizedSet() {
        map = new HashMap<>();
        list = new ArrayList<>();
        rand = new Random();
    }
    
    public boolean insert(int val) {
        if (map.containsKey(val)) return false;
        map.put(val, list.size());
        list.add(val);
        return true;
    }
    
    public boolean remove(int val) {
        if (!map.containsKey(val)) return false;
        int idx = map.get(val);
        int lastVal = list.get(list.size() - 1);
        
        // Swap last element to the deleted position
        list.set(idx, lastVal);
        map.put(lastVal, idx);
        
        list.remove(list.size() - 1);
        map.remove(val);
        return true;
    }
    
    public int getRandom() {
        return list.get(rand.nextInt(list.size()));
    }
}
```

**Độ phức tạp:**
- Thời gian: O(1) trung bình cho mọi thao tác.
- Không gian: O(N) với N là số lượng phần tử.

---

## 6. Insert Delete GetRandom O(1) - Duplicates allowed (LeetCode 381)
**Đề bài chi tiết:**
Giống hệt bài 5, nhưng lần này cho phép các phần tử trùng lặp (duplicates). Hàm `getRandom` trả về với xác suất tuyến tính theo số lượng lặp của phần tử.

**Phân tích thuật toán:**
Vẫn dùng List và HashMap. Lần này HashMap lưu trữ mapping từ một giá trị (val) sang một tập hợp (`Set`) các chỉ số (indices) của phần tử đó trong List. Khi xóa, ta lấy một index bất kỳ từ Set, sau đó swap với phần tử cuối cùng trong List như bài trước.

**Mã nguồn Java:**
```java
class RandomizedCollection {
    Map<Integer, Set<Integer>> map;
    List<Integer> list;
    Random rand;

    public RandomizedCollection() {
        map = new HashMap<>();
        list = new ArrayList<>();
        rand = new Random();
    }
    
    public boolean insert(int val) {
        boolean isNew = !map.containsKey(val);
        map.putIfAbsent(val, new LinkedHashSet<>());
        map.get(val).add(list.size());
        list.add(val);
        return isNew;
    }
    
    public boolean remove(int val) {
        if (!map.containsKey(val) || map.get(val).isEmpty()) return false;
        
        int removeIdx = map.get(val).iterator().next();
        map.get(val).remove(removeIdx);
        
        int lastIdx = list.size() - 1;
        int lastVal = list.get(lastIdx);
        
        list.set(removeIdx, lastVal);
        map.get(lastVal).add(removeIdx);
        map.get(lastVal).remove(lastIdx);
        
        list.remove(lastIdx);
        if (map.get(val).isEmpty()) map.remove(val);
        return true;
    }
    
    public int getRandom() {
        return list.get(rand.nextInt(list.size()));
    }
}
```

**Độ phức tạp:**
- Thời gian: O(1) trung bình cho mọi thao tác.
- Không gian: O(N).

---

## 7. Design a File System (LeetCode 1166)
**Đề bài chi tiết:**
Thiết kế một File System để tạo mới các đường dẫn và gán giá trị cho chúng.
- `createPath(path, value)`: Trả về true nếu tạo path thành công. Path tạo được nếu cha của nó đã tồn tại và nó chưa tồn tại.
- `get(path)`: Trả về value gắn với path, nếu không tồn tại trả về -1.

**Phân tích thuật toán:**
Do yêu cầu đơn giản, ta có thể lưu cấu trúc cây (Trie) hoặc sử dụng trực tiếp một `HashMap` với key là String path và value là int. Khi tạo path `/a/b`, ta check xem path `/a` có tồn tại trong map không.

**Mã nguồn Java:**
```java
class FileSystem {
    Map<String, Integer> map;

    public FileSystem() {
        map = new HashMap<>();
        map.put("", -1); // Root
    }
    
    public boolean createPath(String path, int value) {
        if (path.isEmpty() || path.equals("/") || map.containsKey(path)) return false;
        
        int lastSlash = path.lastIndexOf("/");
        String parent = path.substring(0, lastSlash);
        
        if (!map.containsKey(parent)) return false;
        
        map.put(path, value);
        return true;
    }
    
    public int get(String path) {
        return map.getOrDefault(path, -1);
    }
}
```

**Độ phức tạp:**
- Thời gian: O(L) cho chuỗi có độ dài L (do tính toán hash và thao tác chuỗi).
- Không gian: O(N * L) với N là số lượng paths, L là độ dài trung bình.

---

## 8. Design In-Memory File System (LeetCode 588)
**Đề bài chi tiết:**
Thiết kế hệ thống tập tin trong bộ nhớ (In-Memory File System) với các hàm:
- `ls(path)`: Trả về danh sách file/thư mục.
- `mkdir(path)`: Tạo thư mục (và cha của nó nếu chưa có).
- `addContentToFile(filePath, content)`: Tạo file hoặc append nội dung.
- `readContentFromFile(filePath)`: Đọc nội dung file.

**Phân tích thuật toán:**
Dùng kiến trúc Trie. Mỗi Node đại diện cho một thư mục hoặc file. Nó chứa một `TreeMap` (để tự động sắp xếp tên file/thư mục khi ls) ánh xạ từ tên (String) sang Node con (File/Dir).

**Mã nguồn Java:**
```java
class FileSystem {
    class FileNode {
        boolean isFile;
        StringBuilder content = new StringBuilder();
        TreeMap<String, FileNode> children = new TreeMap<>();
    }
    
    FileNode root;

    public FileSystem() {
        root = new FileNode();
    }
    
    private FileNode traverse(String path) {
        FileNode cur = root;
        if (!path.equals("/")) {
            String[] dirs = path.split("/");
            for (int i = 1; i < dirs.length; i++) {
                cur.children.putIfAbsent(dirs[i], new FileNode());
                cur = cur.children.get(dirs[i]);
            }
        }
        return cur;
    }
    
    public List<String> ls(String path) {
        FileNode cur = traverse(path);
        List<String> res = new ArrayList<>();
        if (cur.isFile) {
            String[] dirs = path.split("/");
            res.add(dirs[dirs.length - 1]);
        } else {
            res.addAll(cur.children.keySet());
        }
        return res;
    }
    
    public void mkdir(String path) {
        traverse(path);
    }
    
    public void addContentToFile(String filePath, String content) {
        FileNode cur = traverse(filePath);
        cur.isFile = true;
        cur.content.append(content);
    }
    
    public String readContentFromFile(String filePath) {
        FileNode cur = traverse(filePath);
        return cur.content.toString();
    }
}
```

**Độ phức tạp:**
- Thời gian: `ls`: O(L + k log k) với L độ dài đường dẫn, k số con. `mkdir`, `add`, `read`: O(L).
- Không gian: O(N * L) để lưu nội dung và trie.

---

## 9. All O`one Data Structure (LeetCode 432)
**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu lưu chuỗi với các thao tác `inc(key)`, `dec(key)`, `getMaxKey()`, `getMinKey()` đều trong O(1).

**Phân tích thuật toán:**
Sử dụng một Hash Map kết hợp với một Doubly Linked List của các Buckets (tương tự như LFU Cache). Map trỏ `key` tới một `Bucket`. `Bucket` chứa một tập (Set) các keys có cùng giá trị count (tần suất). Danh sách các buckets luôn được sắp xếp theo count.

**Mã nguồn Java:**
```java
class AllOne {
    class Bucket {
        int count;
        Set<String> keys;
        Bucket prev, next;
        Bucket(int c) { count = c; keys = new HashSet<>(); }
    }
    
    Bucket head, tail;
    Map<String, Bucket> map;

    public AllOne() {
        head = new Bucket(0);
        tail = new Bucket(0);
        head.next = tail;
        tail.prev = head;
        map = new HashMap<>();
    }
    
    private void addBucketAfter(Bucket newBucket, Bucket prevBucket) {
        newBucket.prev = prevBucket;
        newBucket.next = prevBucket.next;
        prevBucket.next.prev = newBucket;
        prevBucket.next = newBucket;
    }
    
    private void removeBucket(Bucket bucket) {
        bucket.prev.next = bucket.next;
        bucket.next.prev = bucket.prev;
    }
    
    public void inc(String key) {
        if (!map.containsKey(key)) {
            if (head.next.count != 1) {
                addBucketAfter(new Bucket(1), head);
            }
            head.next.keys.add(key);
            map.put(key, head.next);
        } else {
            Bucket curr = map.get(key);
            if (curr.next.count != curr.count + 1) {
                addBucketAfter(new Bucket(curr.count + 1), curr);
            }
            curr.next.keys.add(key);
            map.put(key, curr.next);
            curr.keys.remove(key);
            if (curr.keys.isEmpty()) removeBucket(curr);
        }
    }
    
    public void dec(String key) {
        if (!map.containsKey(key)) return;
        Bucket curr = map.get(key);
        if (curr.count == 1) {
            map.remove(key);
        } else {
            if (curr.prev.count != curr.count - 1) {
                addBucketAfter(new Bucket(curr.count - 1), curr.prev);
            }
            curr.prev.keys.add(key);
            map.put(key, curr.prev);
        }
        curr.keys.remove(key);
        if (curr.keys.isEmpty()) removeBucket(curr);
    }
    
    public String getMaxKey() {
        return tail.prev == head ? "" : tail.prev.keys.iterator().next();
    }
    
    public String getMinKey() {
        return head.next == tail ? "" : head.next.keys.iterator().next();
    }
}
```

**Độ phức tạp:**
- Thời gian: O(1) cho mọi thao tác.
- Không gian: O(N) với N số lượng keys.

---

## 10. Time Based Key-Value Store (LeetCode 981)
**Đề bài chi tiết:**
Tạo một bộ lưu trữ Key-Value Time-based.
- `set(key, value, timestamp)`: Lưu key và value cùng với timestamp.
- `get(key, timestamp)`: Trả về value được lưu ở timestamp lớn nhất (nhưng nhỏ hơn hoặc bằng tham số timestamp).

**Phân tích thuật toán:**
Dùng `HashMap` map key tới một `ArrayList` chứa các cặp `(timestamp, value)`. Bởi vì timestamp trong các lời gọi `set()` luôn được cấp phát tăng dần tuần tự (đề bài đảm bảo điều này), array sẽ tự động được sorted theo timestamp. Khi `get()`, ta dùng Binary Search trên array.

**Mã nguồn Java:**
```java
class TimeMap {
    class Node {
        int time;
        String val;
        Node(int t, String v) { time = t; val = v; }
    }
    
    Map<String, List<Node>> map;

    public TimeMap() {
        map = new HashMap<>();
    }
    
    public void set(String key, String value, int timestamp) {
        map.computeIfAbsent(key, k -> new ArrayList<>()).add(new Node(timestamp, value));
    }
    
    public String get(String key, int timestamp) {
        if (!map.containsKey(key)) return "";
        List<Node> list = map.get(key);
        
        int l = 0, r = list.size() - 1;
        String res = "";
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (list.get(mid).time <= timestamp) {
                res = list.get(mid).val;
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return res;
    }
}
```

**Độ phức tạp:**
- Thời gian: `set` O(1), `get` O(log K) với K là số lượng timestamp ứng với 1 key.
- Không gian: O(N) tổng số cặp (key, value, timestamp).

---

## 11. Design a Leaderboard (1244)
**Đề bài chi tiết**
Thiết kế một hệ thống bảng xếp hạng (Leaderboard) bao gồm các hàm sau:
- `addScore(playerId, score)`: Cập nhật thêm điểm cho `playerId`. Nếu người chơi chưa có trên bảng xếp hạng, thêm họ vào bảng với điểm số là `score`. Nếu đã có, cộng thêm `score` vào điểm số hiện tại của họ.
- `top(K)`: Trả về tổng điểm của `K` người chơi có điểm số cao nhất.
- `reset(playerId)`: Đặt lại điểm của `playerId` về 0 (đảm bảo `playerId` luôn có trước khi bị reset).

**Phân tích thuật toán**
Sử dụng một `HashMap<Integer, Integer>` (lưu `playerId -> score`) để có thể tra cứu và cập nhật điểm số của người chơi trong thời gian $O(1)$.
Tuy nhiên, để có thể lấy được tổng điểm của K người chơi cao nhất, chúng ta có thể sử dụng `TreeMap<Integer, Integer>` (lưu `score -> count`) để giữ danh sách các điểm số luôn được sắp xếp. 
- Khi cập nhật điểm, chúng ta xóa điểm cũ khỏi TreeMap (nếu đếm về 0 thì xóa key), sau đó thêm điểm mới.
- Khi lấy `top(K)`, duyệt TreeMap từ lớn đến bé và lấy K người chơi.

**Mã nguồn Java**
```java
import java.util.*;

class Leaderboard {
    private Map<Integer, Integer> playerScores;
    private TreeMap<Integer, Integer> scoreCounts;

    public Leaderboard() {
        playerScores = new HashMap<>();
        scoreCounts = new TreeMap<>(Collections.reverseOrder());
    }
    
    public void addScore(int playerId, int score) {
        if (playerScores.containsKey(playerId)) {
            int oldScore = playerScores.get(playerId);
            int count = scoreCounts.get(oldScore);
            if (count == 1) {
                scoreCounts.remove(oldScore);
            } else {
                scoreCounts.put(oldScore, count - 1);
            }
            int newScore = oldScore + score;
            playerScores.put(playerId, newScore);
            scoreCounts.put(newScore, scoreCounts.getOrDefault(newScore, 0) + 1);
        } else {
            playerScores.put(playerId, score);
            scoreCounts.put(score, scoreCounts.getOrDefault(score, 0) + 1);
        }
    }
    
    public int top(int K) {
        int sum = 0;
        for (Map.Entry<Integer, Integer> entry : scoreCounts.entrySet()) {
            int score = entry.getKey();
            int count = entry.getValue();
            if (K >= count) {
                sum += score * count;
                K -= count;
            } else {
                sum += score * K;
                break;
            }
        }
        return sum;
    }
    
    public void reset(int playerId) {
        int oldScore = playerScores.get(playerId);
        int count = scoreCounts.get(oldScore);
        if (count == 1) {
            scoreCounts.remove(oldScore);
        } else {
            scoreCounts.put(oldScore, count - 1);
        }
        playerScores.remove(playerId);
    }
}
```
**Độ phức tạp**
- Thời gian: `addScore` $O(\log N)$ (do sử dụng TreeMap), `top(K)` $O(K)$, `reset` $O(\log N)$.
- Không gian: $O(N)$ lưu trữ thông tin của $N$ người chơi.

---

## 12. Logger Rate Limiter (359)
**Đề bài chi tiết**
Thiết kế một bộ giới hạn tốc độ lưu log. Cho một chuỗi tin nhắn và nhãn thời gian của chúng (theo giây), trả về `true` nếu tin nhắn được phép in ra. Điều kiện in ra là tin nhắn đó phải cách ít nhất 10 giây so với lần in cuối cùng của chính nó.
Hàm `shouldPrintMessage(timestamp, message)` trả về `true` hoặc `false`.

**Phân tích thuật toán**
Dùng một `HashMap<String, Integer>` để lưu ánh xạ từ chuỗi `message` đến `timestamp` tiếp theo mà nó có thể được in ra.
Khi có yêu cầu in tin nhắn ở thời điểm `t`:
- Nếu tin nhắn không tồn tại trong map hoặc `t >= map.get(message)`, ta cho phép in, đồng thời cập nhật `map.put(message, t + 10)`.
- Nếu `t < map.get(message)`, từ chối in và trả về `false`.

**Mã nguồn Java**
```java
import java.util.*;

class Logger {
    private Map<String, Integer> nextAllowedTime;

    public Logger() {
        nextAllowedTime = new HashMap<>();
    }
    
    public boolean shouldPrintMessage(int timestamp, String message) {
        if (!nextAllowedTime.containsKey(message) || timestamp >= nextAllowedTime.get(message)) {
            nextAllowedTime.put(message, timestamp + 10);
            return true;
        }
        return false;
    }
}
```
**Độ phức tạp**
- Thời gian: $O(1)$ cho mỗi thao tác gọi `shouldPrintMessage`.
- Không gian: $O(N)$ với $N$ là số lượng tin nhắn duy nhất đến được log.

---

## 13. First Unique Character in a String (387)
**Đề bài chi tiết**
Cho một chuỗi `s`, tìm ký tự không bị lặp lại đầu tiên trong chuỗi và trả về chỉ số của nó. Nếu không tồn tại, trả về `-1`.

**Phân tích thuật toán**
Có thể giải bằng Hash Map đếm tần suất xuất hiện.
Do chuỗi chỉ chứa chữ cái tiếng Anh in thường, ta có thể tối ưu bằng cách dùng mảng số nguyên kích thước 26 như một dạng Hash Table trực tiếp để tiết kiệm chi phí tạo đối tượng và tăng tốc độ.
Thuật toán có 2 bước:
1. Duyệt chuỗi và đếm số lần xuất hiện của từng ký tự.
2. Duyệt chuỗi lần 2, nếu tần suất của ký tự bằng 1, trả về chỉ số đó.

**Mã nguồn Java**
```java
class Solution {
    public int firstUniqChar(String s) {
        int[] freq = new int[26];
        int n = s.length();
        
        // Bước 1: Đếm tần suất
        for (int i = 0; i < n; i++) {
            freq[s.charAt(i) - 'a']++;
        }
        
        // Bước 2: Tìm ký tự đầu tiên có tần suất 1
        for (int i = 0; i < n; i++) {
            if (freq[s.charAt(i) - 'a'] == 1) {
                return i;
            }
        }
        
        return -1;
    }
}
```
**Độ phức tạp**
- Thời gian: $O(N)$ do duyệt chuỗi 2 lần.
- Không gian: $O(1)$ vì mảng chỉ có kích thước cố định là 26.

---

## 14. Group Anagrams (49)
**Đề bài chi tiết**
Cho một mảng các chuỗi `strs`, hãy nhóm các chuỗi là Anagram (đảo ngữ) của nhau lại thành từng danh sách.
Anagram là một từ hoặc cụm từ được tạo thành bằng cách sắp xếp lại các chữ cái của một từ hoặc cụm từ khác, điển hình là dùng tất cả các chữ cái gốc chính xác một lần.

**Phân tích thuật toán**
Hai chuỗi là anagram của nhau thì khi đếm tần suất các ký tự, chúng sẽ có kết quả giống hệt nhau, hoặc khi sắp xếp các ký tự theo thứ tự từ điển, chúng sẽ tạo thành một chuỗi giống hệt nhau.
Ta có thể dùng Hash Map, với key là chuỗi đại diện (chuỗi đã được sắp xếp hoặc chuỗi biểu diễn đếm tần suất) và value là danh sách các từ có cùng key đó.
Sử dụng mảng đếm (count array) kết hợp làm chuỗi (ví dụ `"1#0#2#..."`) sẽ tối ưu hơn sắp xếp nếu độ dài chuỗi dài. Tuy nhiên, cách dùng mảng kí tự tự cấu trúc thành khóa cũng rất thông dụng.

**Mã nguồn Java**
```java
import java.util.*;

class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        
        for (String s : strs) {
            char[] count = new char[26];
            for (char c : s.toCharArray()) {
                count[c - 'a']++;
            }
            String key = new String(count); // Dùng mảng char làm key
            
            map.putIfAbsent(key, new ArrayList<>());
            map.get(key).add(s);
        }
        
        return new ArrayList<>(map.values());
    }
}
```
**Độ phức tạp**
- Thời gian: $O(N \times K)$ với $N$ là số từ, $K$ là độ dài tối đa của từ.
- Không gian: $O(N \times K)$ để lưu trữ dữ liệu Hash Map.

---

## 15. Design Twitter (355)
**Đề bài chi tiết**
Thiết kế một phiên bản Twitter đơn giản để người dùng có thể đăng bài (tweet), theo dõi (follow) / hủy theo dõi (unfollow) người khác và xem bảng tin (news feed) gồm top 10 bài viết mới nhất từ những người dùng mà họ theo dõi và cả bản thân họ.
Các API:
- `postTweet(userId, tweetId)`: Người dùng có ID `userId` đăng bài `tweetId`.
- `getNewsFeed(userId)`: Lấy 10 bài viết mới nhất từ những người mà `userId` follow (bao gồm cả chính mình).
- `follow(followerId, followeeId)`: `followerId` theo dõi `followeeId`.
- `unfollow(followerId, followeeId)`: Hủy theo dõi.

**Phân tích thuật toán**
Cần có hai Hash Map:
- `Map<Integer, Set<Integer>> followees`: Lưu danh sách người mà mỗi user đang follow.
- `Map<Integer, List<Tweet>> tweets`: Lưu danh sách bài đăng của từng user.

Để có thể sắp xếp 10 tweets mới nhất, ta cần sử dụng một biến đếm thời gian (timestamp) toàn cục. Mỗi tweet sẽ gắn liền với một timestamp. Khi lấy bảng tin, ta dùng Max-Heap (PriorityQueue) để gộp các list bài đăng của tất cả các user đang follow.

**Mã nguồn Java**
```java
import java.util.*;

class Twitter {
    private static int timestamp = 0;
    
    private static class Tweet {
        int id;
        int time;
        Tweet next;
        
        public Tweet(int id) {
            this.id = id;
            this.time = timestamp++;
            this.next = null;
        }
    }
    
    private Map<Integer, Set<Integer>> followees;
    private Map<Integer, Tweet> userTweets;

    public Twitter() {
        followees = new HashMap<>();
        userTweets = new HashMap<>();
    }
    
    public void postTweet(int userId, int tweetId) {
        Tweet t = new Tweet(tweetId);
        t.next = userTweets.get(userId);
        userTweets.put(userId, t); // Cập nhật tweet mới ở đầu
    }
    
    public List<Integer> getNewsFeed(int userId) {
        PriorityQueue<Tweet> pq = new PriorityQueue<>((a, b) -> b.time - a.time);
        
        Set<Integer> follows = followees.getOrDefault(userId, new HashSet<>());
        follows.add(userId); // Tự follow bản thân để lấy bài của mình
        
        for (int followeeId : follows) {
            Tweet t = userTweets.get(followeeId);
            if (t != null) {
                pq.offer(t);
            }
        }
        
        List<Integer> res = new ArrayList<>();
        int count = 0;
        while (!pq.isEmpty() && count < 10) {
            Tweet t = pq.poll();
            res.add(t.id);
            count++;
            if (t.next != null) {
                pq.offer(t.next);
            }
        }
        
        // Remove bản thân sau khi truy vấn để không phá vỡ logic
        follows.remove(userId);
        
        return res;
    }
    
    public void follow(int followerId, int followeeId) {
        followees.putIfAbsent(followerId, new HashSet<>());
        followees.get(followerId).add(followeeId);
    }
    
    public void unfollow(int followerId, int followeeId) {
        if (followees.containsKey(followerId)) {
            followees.get(followerId).remove(followeeId);
        }
    }
}
```
**Độ phức tạp**
- Thời gian: `postTweet`, `follow`, `unfollow` là $O(1)$. `getNewsFeed` $O(U + 10 \log U)$ với $U$ là số lượng người đang theo dõi.
- Không gian: $O(T + U \times F)$ lưu $T$ tweet và quan hệ follower/followee.

---

## 16. Snapshot Array (1146)
**Đề bài chi tiết**
Thực hiện cấu trúc dữ liệu `SnapshotArray`:
- `SnapshotArray(length)`: Khởi tạo mảng số nguyên có độ dài `length`, ban đầu mọi giá trị là 0.
- `set(index, val)`: Gán giá trị tại `index` bằng `val`.
- `snap()`: Chụp một bức ảnh (snapshot) của mảng hiện tại. Trả về `snap_id`, là tổng số lần gọi `snap()` trừ đi 1.
- `get(index, snap_id)`: Lấy giá trị tại `index` ứng với mảng lúc gọi `snap()` ở `snap_id`.

**Phân tích thuật toán**
Sử dụng mảng các `TreeMap<Integer, Integer>` (lưu `snap_id -> value`). 
- Khi `set(index, val)`, ta lưu `val` vào `TreeMap` tại `index` với khóa là `snap_id` hiện tại.
- Khi gọi `get(index, snap_id)`, ta dùng hàm `floorEntry(snap_id)` trong TreeMap để lấy giá trị có hiệu lực nhất ngay trước (hoặc chính xác bằng) `snap_id` đó. Việc này cho phép lưu trữ dưới dạng Delta, tránh phải lưu nguyên cả mảng mỗi lần snap, tiết kiệm được lượng lớn bộ nhớ.

**Mã nguồn Java**
```java
import java.util.*;

class SnapshotArray {
    private int snapId;
    private TreeMap<Integer, Integer>[] arr;

    public SnapshotArray(int length) {
        snapId = 0;
        arr = new TreeMap[length];
        for (int i = 0; i < length; i++) {
            arr[i] = new TreeMap<>();
            arr[i].put(0, 0); // Giá trị mặc định
        }
    }
    
    public void set(int index, int val) {
        arr[index].put(snapId, val);
    }
    
    public int snap() {
        return snapId++;
    }
    
    public int get(int index, int snap_id) {
        return arr[index].floorEntry(snap_id).getValue();
    }
}
```
**Độ phức tạp**
- Thời gian: Khởi tạo $O(N)$, `set` và `get` là $O(\log S)$ với $S$ là số lượng lần `set` cho 1 index cụ thể, `snap` là $O(1)$.
- Không gian: $O(S)$ với $S$ là tổng số lần cập nhật phần tử, vì ta chỉ lưu điểm thay đổi.

---

## 17. Maximum Frequency Stack (895)
**Đề bài chi tiết**
Thiết kế cấu trúc ngăn xếp `FreqStack` trả về phần tử có tần suất xuất hiện lớn nhất:
- `push(val)`: Đẩy `val` vào đỉnh.
- `pop()`: Xóa và trả về phần tử xuất hiện nhiều nhất trong ngăn xếp. Nếu có nhiều phần tử như vậy, hãy trả về phần tử nằm gần đỉnh nhất.

**Phân tích thuật toán**
Bài toán yêu cầu lấy phần tử có tần suất cao nhất, nếu hòa thì lấy phần tử được push vào gần đây nhất (giống Stack). Ta có thể dùng hai Hash Maps:
1. `freqMap`: Lưu tần suất của từng giá trị (`val -> count`).
2. `groupMap`: Lưu một Stack các giá trị ứng với mỗi tần suất (`count -> Stack<Integer>`).
Đồng thời có một biến `maxFreq` để lưu lại tần suất lớn nhất hiện tại.

Khi push: Tăng count, đẩy giá trị vào Stack ứng với count đó trong `groupMap`. Cập nhật `maxFreq`.
Khi pop: Lấy phần tử từ Stack ứng với `maxFreq` trong `groupMap`. Giảm count của phần tử đó trong `freqMap`. Nếu Stack của `maxFreq` rỗng, giảm `maxFreq` đi 1.

**Mã nguồn Java**
```java
import java.util.*;

class FreqStack {
    private Map<Integer, Integer> freqMap;
    private Map<Integer, Stack<Integer>> groupMap;
    private int maxFreq;

    public FreqStack() {
        freqMap = new HashMap<>();
        groupMap = new HashMap<>();
        maxFreq = 0;
    }
    
    public void push(int val) {
        int count = freqMap.getOrDefault(val, 0) + 1;
        freqMap.put(val, count);
        
        maxFreq = Math.max(maxFreq, count);
        
        groupMap.putIfAbsent(count, new Stack<>());
        groupMap.get(count).push(val);
    }
    
    public int pop() {
        Stack<Integer> maxFreqStack = groupMap.get(maxFreq);
        int val = maxFreqStack.pop();
        
        freqMap.put(val, freqMap.get(val) - 1);
        if (maxFreqStack.isEmpty()) {
            maxFreq--;
        }
        
        return val;
    }
}
```
**Độ phức tạp**
- Thời gian: `push` và `pop` đều hoạt động ở $O(1)$.
- Không gian: $O(N)$ lưu dữ liệu cho tần suất và các Stacks.

---

## 18. Design Tic-Tac-Toe (348)
**Đề bài chi tiết**
Thiết kế trò chơi Tic-Tac-Toe trên bảng $n \times n$ cho hai người chơi. Hàm `move(row, col, player)` đại diện cho một nước đi của `player` (1 hoặc 2) tại vị trí `(row, col)`. Trả về `0` nếu không ai thắng, trả về `player` nếu người đó thắng bằng nước đi này.
Luật thắng là có $n$ ký hiệu của mình liên tiếp trên một hàng, một cột, hoặc một trong hai đường chéo.

**Phân tích thuật toán**
Cách ngây thơ là sau mỗi nước đi lại đi kiểm tra toàn bộ bảng $O(N)$.
Tuy nhiên, ta chỉ cần đếm xem với mỗi hàng, mỗi cột và 2 đường chéo, điểm số là bao nhiêu.
Nếu người chơi 1 đi, ta cộng thêm 1. Nếu người chơi 2 đi, ta trừ đi 1.
Nếu một hàng (hoặc cột, đường chéo) có trị tuyệt đối là $n$, người vừa đánh nước đó thắng.

Cấu trúc lưu trữ:
- Mảng `rows[n]`, `cols[n]` lưu tổng của các hàng, các cột.
- Biến `diagonal` lưu tổng đường chéo chính, `antiDiagonal` lưu tổng đường chéo phụ.

**Mã nguồn Java**
```java
class TicTacToe {
    private int[] rows;
    private int[] cols;
    private int diagonal;
    private int antiDiagonal;
    private int n;

    public TicTacToe(int n) {
        this.rows = new int[n];
        this.cols = new int[n];
        this.diagonal = 0;
        this.antiDiagonal = 0;
        this.n = n;
    }
    
    public int move(int row, int col, int player) {
        int value = (player == 1) ? 1 : -1;
        
        rows[row] += value;
        cols[col] += value;
        
        if (row == col) {
            diagonal += value;
        }
        if (col == (n - row - 1)) {
            antiDiagonal += value;
        }
        
        if (Math.abs(rows[row]) == n || Math.abs(cols[col]) == n || 
            Math.abs(diagonal) == n || Math.abs(antiDiagonal) == n) {
            return player;
        }
        
        return 0;
    }
}
```
**Độ phức tạp**
- Thời gian: $O(1)$ cho mỗi lần gọi `move`.
- Không gian: $O(N)$ với mảng lưu số đếm (với $N$ là số hàng/cột của bảng).

---

## 19. Minimum Area Rectangle (939)
**Đề bài chi tiết**
Cho một tập hợp các điểm trên mặt phẳng 2D `points`, tìm diện tích của hình chữ nhật nhỏ nhất có cạnh song song với các trục tọa độ (x và y) được tạo bởi các điểm này. Nếu không tìm được hình chữ nhật nào, trả về `0`.

**Phân tích thuật toán**
Ta có thể duyệt qua tất cả các cặp hai điểm bất kì trong không gian tọa độ. Coi hai điểm đó là đường chéo của hình chữ nhật (vì cạnh song song với các trục).
Nếu hai điểm là $(x1, y1)$ và $(x2, y2)$, điều kiện tạo thành hình chữ nhật là $x1 \neq x2$ và $y1 \neq y2$.
Khi chọn được hai điểm này, 2 điểm còn lại buộc phải là $(x1, y2)$ và $(x2, y1)$.
Ta dùng `HashSet` để lưu chuỗi encode `x + "," + y` (hoặc encode nguyên thành `x * 40001 + y`) nhằm có thể check sự tồn tại của 2 điểm trên trong $O(1)$.
Trong quá trình duyệt, duy trì biến tính diện tích nhỏ nhất có thể tìm được.

**Mã nguồn Java**
```java
import java.util.*;

class Solution {
    public int minAreaRect(int[][] points) {
        Set<Integer> pointSet = new HashSet<>();
        // Max value of x and y is 40000, we can encode the point to single int
        for (int[] p : points) {
            pointSet.add(p[0] * 40001 + p[1]);
        }
        
        int minArea = Integer.MAX_VALUE;
        int n = points.length;
        
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                int x1 = points[i][0], y1 = points[i][1];
                int x2 = points[j][0], y2 = points[j][1];
                
                // Phải tạo đường chéo (không thẳng hàng)
                if (x1 != x2 && y1 != y2) {
                    if (pointSet.contains(x1 * 40001 + y2) && pointSet.contains(x2 * 40001 + y1)) {
                        int area = Math.abs(x1 - x2) * Math.abs(y1 - y2);
                        minArea = Math.min(minArea, area);
                    }
                }
            }
        }
        
        return minArea == Integer.MAX_VALUE ? 0 : minArea;
    }
}
```
**Độ phức tạp**
- Thời gian: $O(N^2)$ với $N$ là số lượng điểm.
- Không gian: $O(N)$ lưu `HashSet`.

---

## 20. Subarray Sum Equals K (560)
**Đề bài chi tiết**
Cho một mảng các số nguyên `nums` và số nguyên `k`. Hãy trả về số lượng các mảng con liên tiếp có tổng bằng `k`.

**Phân tích thuật toán**
Cách tiếp cận ngây thơ là duyệt 2 vòng lặp, tốn $O(N^2)$.
Ta có thể tối ưu bằng Prefix Sum + HashMap.
Ý tưởng: Dùng một biến `sum` để tính prefix sum (tổng từ 0 tới chỉ số i hiện tại).
Nếu tại vị trí `i`, tổng là `sum`, để tồn tại một đoạn có tổng bằng `k` kết thúc tại `i`, thì phải có một vị trí trước đó có prefix sum là `sum - k`.
Dùng một `HashMap` lưu `prefixSum -> count` để đếm số lượng prefix sum đã xuất hiện.
Lưu ý khởi tạo map với phần tử `(0, 1)` để bao gồm trường hợp chính `sum` tại vị trí hiện hành bằng `k` (mảng con bắt đầu từ vị trí 0).

**Mã nguồn Java**
```java
import java.util.*;

class Solution {
    public int subarraySum(int[] nums, int k) {
        int count = 0;
        int currentSum = 0;
        Map<Integer, Integer> prefixSumCount = new HashMap<>();
        
        // Cần khởi tạo 0 để đếm trường hợp currentSum - k == 0 (tức currentSum == k)
        prefixSumCount.put(0, 1);
        
        for (int num : nums) {
            currentSum += num;
            
            if (prefixSumCount.containsKey(currentSum - k)) {
                count += prefixSumCount.get(currentSum - k);
            }
            
            prefixSumCount.put(currentSum, prefixSumCount.getOrDefault(currentSum, 0) + 1);
        }
        
        return count;
    }
}
```
**Độ phức tạp**
- Thời gian: $O(N)$ do chỉ duyệt mảng một lần, mỗi phần tử tính toán với HashMap mất $O(1)$.
- Không gian: $O(N)$ trong trường hợp xấu nhất lưu tới $N$ prefix sum vào HashMap.

---

## 21. Contains Duplicate II (219)
**Đề bài chi tiết**
Cho một mảng các số nguyên `nums` và một số nguyên `k`, tìm xem liệu có tồn tại hai chỉ số khác nhau `i` và `j` trong mảng sao cho `nums[i] == nums[j]` và trị tuyệt đối của `i - j` nhỏ hơn hoặc bằng `k` (`abs(i - j) <= k`). Trả về `true` nếu tồn tại, ngược lại trả về `false`.

**Phân tích thuật toán**
Sử dụng một `HashMap<Integer, Integer>` để lưu ánh xạ từ giá trị của phần tử trong mảng đến vị trí (index) gần nhất mà nó xuất hiện.
Khi duyệt mảng:
- Kiểm tra xem `nums[i]` đã có trong HashMap chưa.
- Nếu có, gọi chỉ số cũ là `j`. Kiểm tra `i - j <= k`. Nếu đúng, trả về `true`.
- Cập nhật (hoặc thêm mới) `nums[i]` với chỉ số `i` vào HashMap để các phần tử phía sau so sánh với khoảng cách ngắn nhất.

**Mã nguồn Java**
```java
import java.util.*;

class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(nums[i])) {
                if (i - map.get(nums[i]) <= k) {
                    return true;
                }
            }
            map.put(nums[i], i);
        }
        
        return false;
    }
}
```
**Độ phức tạp**
- Thời gian: $O(N)$, chỉ duyệt qua mảng một lần.
- Không gian: $O(\min(N, k))$ (nếu dùng sliding window với HashSet, còn với HashMap thì tối đa $O(N)$).

---

## 22. Valid Sudoku (36)
**Đề bài chi tiết**
Xác định xem bảng Sudoku $9 \times 9$ có hợp lệ không. Chỉ cần kiểm tra các ô đã điền.
Quy tắc:
1. Mỗi hàng phải chứa các chữ số `1-9` không bị lặp.
2. Mỗi cột phải chứa các chữ số `1-9` không bị lặp.
3. Mỗi trong 9 ô vuông nhỏ $3 \times 3$ phải chứa các chữ số `1-9` không bị lặp.

**Phân tích thuật toán**
Ta có thể dùng `HashSet` để lưu chuỗi định dạng nhận dạng duy nhất vị trí của số.
Ví dụ nếu số `5` nằm ở hàng `i`, cột `j`, ta thêm vào HashSet 3 chuỗi:
- `"5 in row i"`
- `"5 in col j"`
- `"5 in block i/3-j/3"`
Nếu hàm `add()` của HashSet trả về `false`, nghĩa là giá trị đó đã bị trùng ở hàng, cột, hoặc block đó $\rightarrow$ Sudoku không hợp lệ.

**Mã nguồn Java**
```java
import java.util.*;

class Solution {
    public boolean isValidSudoku(char[][] board) {
        Set<String> seen = new HashSet<>();
        
        for (int i = 0; i < 9; ++i) {
            for (int j = 0; j < 9; ++j) {
                char number = board[i][j];
                if (number != '.') {
                    if (!seen.add(number + " in row " + i) ||
                        !seen.add(number + " in col " + j) ||
                        !seen.add(number + " in block " + i / 3 + "-" + j / 3)) {
                        return false;
                    }
                }
            }
        }
        return true;
    }
}
```
**Độ phức tạp**
- Thời gian: $O(1)$ vì bảng luôn là $9 \times 9$.
- Không gian: $O(1)$ (tối đa 243 chuỗi trong Set).

---

## 23. Word Pattern (290)
**Đề bài chi tiết**
Cho một `pattern` và một chuỗi `s`, xác định xem `s` có tuân theo đúng mẫu `pattern` đó không. 
Ở đây, việc tuân theo có nghĩa là một song ánh đầy đủ (bijection) giữa một chữ cái trong `pattern` và một từ không rỗng trong `s`.

**Phân tích thuật toán**
Tách chuỗi `s` thành các từ bằng khoảng trắng. Nếu số lượng từ không bằng chiều dài chuỗi `pattern`, trả về `false`.
Sử dụng một (hoặc hai) `HashMap` để kiểm tra ánh xạ 1-1. 
Ở Java, ta có thể dùng một `HashMap` chung và dựa vào tính chất hàm `put()` (trả về giá trị cũ) để kiểm tra. Vì các khóa là `Character` và `String` nên để chắc chắn, ta có thể so sánh trực tiếp hoặc dùng 2 bản đồ độc lập (1 cho `char -> string`, 1 cho `string -> char`) hoặc 1 bản đồ cho index xuất hiện. 

**Mã nguồn Java**
```java
import java.util.*;

class Solution {
    public boolean wordPattern(String pattern, String s) {
        String[] words = s.split(" ");
        if (words.length != pattern.length()) {
            return false;
        }
        
        Map<Character, String> charToWord = new HashMap<>();
        Map<String, Character> wordToChar = new HashMap<>();
        
        for (int i = 0; i < pattern.length(); i++) {
            char c = pattern.charAt(i);
            String word = words[i];
            
            if (charToWord.containsKey(c) && !charToWord.get(c).equals(word)) {
                return false;
            }
            if (wordToChar.containsKey(word) && wordToChar.get(word) != c) {
                return false;
            }
            
            charToWord.put(c, word);
            wordToChar.put(word, c);
        }
        
        return true;
    }
}
```
**Độ phức tạp**
- Thời gian: $O(N)$ trong đó $N$ là độ dài của `s`.
- Không gian: $O(M)$ với $M$ là số lượng từ duy nhất.

---

## 24. Isomorphic Strings (205)
**Đề bài chi tiết**
Hai chuỗi `s` và `t` được gọi là đẳng cấu (isomorphic) nếu các ký tự trong `s` có thể được thay thế để tạo thành `t`.
Không có hai ký tự nào có thể ánh xạ đến cùng một ký tự, nhưng một ký tự có thể ánh xạ đến chính nó.

**Phân tích thuật toán**
Rất giống với **Word Pattern**, ta cần ánh xạ 1-1 giữa các ký tự của `s` và `t`. 
Vì bảng mã ASCII chỉ có 256 ký tự, ta có thể dùng hai mảng int thay cho HashMap để tiết kiệm không gian và thời gian. Mảng lưu lại vị trí xuất hiện gần nhất của ký tự đó. Nếu tại bất kỳ vị trí `i` nào, hai mảng cho kết quả vị trí lưu trữ khác nhau, hai chuỗi không đẳng cấu.

**Mã nguồn Java**
```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        if (s.length() != t.length()) return false;
        
        int[] map1 = new int[256];
        int[] map2 = new int[256];
        
        for (int i = 0; i < s.length(); i++) {
            char c1 = s.charAt(i);
            char c2 = t.charAt(i);
            
            // So sánh giá trị index được đánh dấu (chú ý dùng i + 1 để tránh lỗi giá trị 0 mặc định)
            if (map1[c1] != map2[c2]) {
                return false;
            }
            
            map1[c1] = i + 1;
            map2[c2] = i + 1;
        }
        
        return true;
    }
}
```
**Độ phức tạp**
- Thời gian: $O(N)$.
- Không gian: $O(1)$ do kích thước mảng tĩnh là 256.

---

## 25. LFU Cache (460)
**Đề bài chi tiết**
Thiết kế và triển khai một LFU (Least Frequently Used) Cache. Lớp `LFUCache` hỗ trợ:
- `LFUCache(capacity)`: Khởi tạo với sức chứa `capacity`.
- `get(key)`: Lấy giá trị của `key` nếu tồn tại, ngược lại trả về `-1`.
- `put(key, value)`: Cập nhật hoặc thêm giá trị. Khi vượt quá sức chứa, loại bỏ phần tử ít được sử dụng nhất (LFU). Nếu có nhiều phần tử cùng số lần sử dụng tối thiểu, loại bỏ phần tử cũ nhất theo thời gian sử dụng (LRU).

**Phân tích thuật toán**
Để các thao tác `get` và `put` đạt $O(1)$, ta sử dụng hai bản đồ (Maps):
1. `Map<Integer, Node> cache`: ánh xạ khóa (key) tới nút chứa giá trị và tần suất (freq).
2. `Map<Integer, LinkedHashSet<Integer>> freqMap`: ánh xạ tần suất tới một tập hợp các khóa được sắp xếp theo thời gian truy cập (sử dụng LinkedHashSet để vừa xóa $O(1)$ vừa duy trì tính chất LRU).
Kết hợp một biến `minFreq` để theo dõi tần suất nhỏ nhất hiện tại.

**Mã nguồn Java**
```java
import java.util.*;

class LFUCache {
    private int capacity;
    private int minFreq;
    private Map<Integer, Integer> keyToVal;
    private Map<Integer, Integer> keyToFreq;
    private Map<Integer, LinkedHashSet<Integer>> freqToKeys;

    public LFUCache(int capacity) {
        this.capacity = capacity;
        this.minFreq = 0;
        this.keyToVal = new HashMap<>();
        this.keyToFreq = new HashMap<>();
        this.freqToKeys = new HashMap<>();
    }
    
    public int get(int key) {
        if (!keyToVal.containsKey(key)) {
            return -1;
        }
        int freq = keyToFreq.get(key);
        keyToFreq.put(key, freq + 1);
        freqToKeys.get(freq).remove(key);
        
        if (freq == minFreq && freqToKeys.get(freq).isEmpty()) {
            minFreq++;
        }
        
        freqToKeys.putIfAbsent(freq + 1, new LinkedHashSet<>());
        freqToKeys.get(freq + 1).add(key);
        return keyToVal.get(key);
    }
    
    public void put(int key, int value) {
        if (capacity <= 0) return;
        
        if (keyToVal.containsKey(key)) {
            keyToVal.put(key, value);
            get(key); // cập nhật tần suất
            return;
        }
        
        if (keyToVal.size() >= capacity) {
            int evict = freqToKeys.get(minFreq).iterator().next();
            freqToKeys.get(minFreq).remove(evict);
            keyToVal.remove(evict);
            keyToFreq.remove(evict);
        }
        
        keyToVal.put(key, value);
        keyToFreq.put(key, 1);
        minFreq = 1;
        freqToKeys.putIfAbsent(1, new LinkedHashSet<>());
        freqToKeys.get(1).add(key);
    }
}
```
**Độ phức tạp**
- Thời gian: $O(1)$ cho cả `get` và `put`.
- Không gian: $O(N)$ nơi $N$ là capacity.

---

## 26. Range Module (715)
**Đề bài chi tiết**
Thiết kế dữ liệu `RangeModule` để theo dõi các nửa khoảng `[left, right)`:
- `addRange(left, right)`: Thêm khoảng. Nếu trùng lặp một phần, kết hợp lại.
- `queryRange(left, right)`: Trả về `true` nếu mọi số thực trong khoảng này đều được theo dõi.
- `removeRange(left, right)`: Ngừng theo dõi mọi số thực trong khoảng này.

**Phân tích thuật toán**
Sử dụng `TreeMap<Integer, Integer>` với key là điểm `left` và value là `right`.
Khi thêm một khoảng, ta tìm các khoảng giao nhau bằng hàm `floorKey` và các khoảng kề sau đó, gộp tất cả và chèn một khoảng duy nhất.
Khi kiểm tra, dùng `floorKey(left)` để xem khoảng lớn nhất bắt đầu trước `left` có bao trọn tới `right` không.
Khi xóa khoảng, ta có thể cần tách các khoảng đang tồn tại thành 2 nửa nếu chúng chứa khoảng cần xóa.

**Mã nguồn Java**
```java
import java.util.*;

class RangeModule {
    private TreeMap<Integer, Integer> intervals;

    public RangeModule() {
        intervals = new TreeMap<>();
    }
    
    public void addRange(int left, int right) {
        Integer start = intervals.floorKey(left);
        if (start != null && intervals.get(start) >= left) {
            left = start;
            right = Math.max(right, intervals.get(start));
        }
        Integer end = intervals.floorKey(right);
        if (end != null && intervals.get(end) > right) {
            right = intervals.get(end);
        }
        intervals.subMap(left, true, right, true).clear();
        intervals.put(left, right);
    }
    
    public boolean queryRange(int left, int right) {
        Integer start = intervals.floorKey(left);
        return start != null && intervals.get(start) >= right;
    }
    
    public void removeRange(int left, int right) {
        Integer start = intervals.floorKey(left);
        if (start != null && intervals.get(start) > left) {
            int oldRight = intervals.get(start);
            intervals.put(start, left);
            if (oldRight > right) {
                intervals.put(right, oldRight);
            }
        }
        Integer end = intervals.floorKey(right);
        if (end != null && intervals.get(end) > right) {
            intervals.put(right, intervals.get(end));
        }
        intervals.subMap(left, true, right, false).clear();
    }
}
```
**Độ phức tạp**
- Thời gian: `add` và `remove` tốn $O(K + \log N)$ (chỉnh sửa K đoạn), `query` tốn $O(\log N)$. (Do `subMap.clear()` xóa tuần tự). Trung bình hiệu suất rất tốt do số lượng đoạn (intervals) thu hẹp.
- Không gian: $O(N)$ lưu số lượng điểm mút.

---

## 27. Design Hit Counter (362)
**Đề bài chi tiết**
Thiết kế một bộ đếm số lượt nhấn (hit counter) thống kê số lượt hit trong vòng 5 phút (300 giây) qua.
- `hit(timestamp)`: Ghi nhận một hit ở `timestamp` (theo giây).
- `getHits(timestamp)`: Lấy số lượng hit trong vòng 300 giây qua (từ `timestamp - 300 + 1` đến `timestamp`).
Lưu ý: hàm được gọi với `timestamp` tăng dần.

**Phân tích thuật toán**
Sử dụng mảng vòng (circular array) kích thước 300 để đếm số hits vì giới hạn cửa sổ chỉ là 300 giây.
Dùng một mảng `times[300]` lưu `timestamp` mới nhất map tới slot tương ứng và `hits[300]` lưu số lượng nhấn ở thời điểm đó.
Khi gọi `hit(t)`, index là `t % 300`. Nếu `times[index] != t`, reset `hits[index] = 1` và cập nhật `times[index] = t`. Ngược lại tăng `hits[index]`.
Khi gọi `getHits(t)`, chỉ cộng gộp các giá trị `hits[i]` mà `t - times[i] < 300`.

**Mã nguồn Java**
```java
class HitCounter {
    private int[] times;
    private int[] hits;

    public HitCounter() {
        times = new int[300];
        hits = new int[300];
    }
    
    public void hit(int timestamp) {
        int idx = timestamp % 300;
        if (times[idx] != timestamp) {
            times[idx] = timestamp;
            hits[idx] = 1;
        } else {
            hits[idx]++;
        }
    }
    
    public int getHits(int timestamp) {
        int totalHits = 0;
        for (int i = 0; i < 300; i++) {
            if (timestamp - times[i] < 300) {
                totalHits += hits[i];
            }
        }
        return totalHits;
    }
}
```
**Độ phức tạp**
- Thời gian: $O(1)$ cho mỗi thao tác (chỉ lặp 300 lần không phụ thuộc $N$).
- Không gian: $O(1)$ (vì kích thước mảng hằng số 300).

---

## 28. Two Sum Data Structure (170)
**Đề bài chi tiết**
Thiết kế một cấu trúc dữ liệu cho phép:
- `add(number)`: thêm một số vào trong cấu trúc.
- `find(value)`: trả về `true` nếu tồn tại bất kỳ cặp số nào (có thể trùng giá trị nhưng phải khác vị trí) cộng lại bằng `value`.

**Phân tích thuật toán**
Sử dụng một `HashMap<Integer, Integer>` (lưu `number -> count`).
Khi `add(number)`, tăng biến đếm của số này lên 1.
Khi `find(value)`, duyệt qua các key trong map. Giả sử key đang xét là `num1`, tính `num2 = value - num1`.
Nếu `num1 != num2`, chỉ cần `map.containsKey(num2)`.
Nếu `num1 == num2`, cần đảm bảo `map.get(num1) > 1`.

**Mã nguồn Java**
```java
import java.util.*;

class TwoSum {
    private Map<Integer, Integer> numCounts;

    public TwoSum() {
        numCounts = new HashMap<>();
    }
    
    public void add(int number) {
        numCounts.put(number, numCounts.getOrDefault(number, 0) + 1);
    }
    
    public boolean find(int value) {
        for (Map.Entry<Integer, Integer> entry : numCounts.entrySet()) {
            int num1 = entry.getKey();
            int count1 = entry.getValue();
            int num2 = value - num1;
            
            if (num1 == num2) {
                if (count1 > 1) return true;
            } else {
                if (numCounts.containsKey(num2)) return true;
            }
        }
        return false;
    }
}
```
**Độ phức tạp**
- Thời gian: `add` là $O(1)$, `find` là $O(N)$ do phải duyệt các key duy nhất.
- Không gian: $O(N)$ lưu dữ liệu trong hash map.

---

## 29. Find Duplicate Subtrees (652)
**Đề bài chi tiết**
Cho gốc của một cây nhị phân, trả về danh sách gồm một nút đại diện cho mỗi cây con bị trùng lặp. Hai cây trùng lặp nếu chúng có cùng cấu trúc và giá trị tại các nút tương ứng.

**Phân tích thuật toán**
Dùng Hashing và Serialization.
Thực hiện duyệt hậu tố (Post-order Traversal) để lấy chuỗi serialization của từng cây con. Chuỗi này miêu tả cấu trúc độc nhất của cây. (VD: `left,right,root`).
Lưu chuỗi này vào một `HashMap<String, Integer>` (lưu `serialization -> count`).
Nếu gặp một cây có serialization xuất hiện đúng 2 lần (count == 1 khi chuẩn bị put tiếp), ta thêm nút gốc của nó vào mảng kết quả.
Để tránh lỗi về định dạng, có thể dùng chuỗi dạng `"val,left,right"`.

**Mã nguồn Java**
```java
import java.util.*;

class Solution {
    private Map<String, Integer> countMap;
    private List<TreeNode> result;

    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        countMap = new HashMap<>();
        result = new ArrayList<>();
        serialize(root);
        return result;
    }
    
    private String serialize(TreeNode node) {
        if (node == null) return "#";
        
        String serial = node.val + "," + serialize(node.left) + "," + serialize(node.right);
        
        countMap.put(serial, countMap.getOrDefault(serial, 0) + 1);
        
        if (countMap.get(serial) == 2) {
            result.add(node);
        }
        
        return serial;
    }
}
```
**Độ phức tạp**
- Thời gian: $O(N^2)$ bởi phép nối chuỗi. (Có thể tối ưu thành $O(N)$ bằng cách hash chuỗi trực tiếp hoặc gán id cho mỗi chuỗi mới).
- Không gian: $O(N^2)$ lưu nội dung các serialization lớn.

---

## 30. Serialize and Deserialize Binary Tree (297)
**Đề bài chi tiết**
Thiết kế một thuật toán để tuần tự hóa (serialize) một cây nhị phân thành một chuỗi, và giải tuần tự hóa (deserialize) chuỗi đó ngược lại thành một cây nhị phân.

**Phân tích thuật toán**
- **Serialize**: Sử dụng DFS (Pre-order traversal) hoặc BFS. Pre-order đơn giản hơn: ghi đè giá trị nút, rồi gọi đệ quy sang trái, sang phải. Nút null được đại diện bằng chuỗi như `"X"`. Phân cách bằng dấu phẩy `","`.
- **Deserialize**: Tách chuỗi theo dấu `","` để tạo một Queue (hoặc List). Xây dựng lại cây đệ quy theo cùng logic Pre-order: Lấy phần tử đầu trong queue làm nút. Nếu là `"X"`, trả về `null`. Nếu không, tạo `TreeNode`, sau đó đệ quy gán cho nhánh trái và nhánh phải.

**Mã nguồn Java**
```java
import java.util.*;

class Codec {
    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        buildString(root, sb);
        return sb.toString();
    }
    
    private void buildString(TreeNode node, StringBuilder sb) {
        if (node == null) {
            sb.append("X").append(",");
        } else {
            sb.append(node.val).append(",");
            buildString(node.left, sb);
            buildString(node.right, sb);
        }
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        Queue<String> nodes = new LinkedList<>();
        nodes.addAll(Arrays.asList(data.split(",")));
        return buildTree(nodes);
    }
    
    private TreeNode buildTree(Queue<String> nodes) {
        String val = nodes.poll();
        if (val.equals("X")) {
            return null;
        } else {
            TreeNode node = new TreeNode(Integer.parseInt(val));
            node.left = buildTree(nodes);
            node.right = buildTree(nodes);
            return node;
        }
    }
}
```
**Độ phức tạp**
- Thời gian: $O(N)$ vì chúng ta thăm mỗi nút một lần trong cả 2 quá trình.
- Không gian: $O(N)$ dùng StringBuilder, Queue, và không gian call stack.
