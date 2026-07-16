# Companion Problems: 01-What-Is-An-Algorithm

Tài liệu này cung cấp **30 bài toán thực hành** kèm theo lời giải chi tiết, phân tích lý do chọn thuật toán và độ phức tạp nhằm củng cố các khái niệm nền tảng về Algorithm.

---

## 1. Tìm Ước Chung Lớn Nhất (Greatest Common Divisor)
**Đề bài chi tiết:** Cho hai số nguyên dương `a` và `b` ($1 \le a, b \le 10^9$). Hãy tìm Ước chung lớn nhất (GCD) của chúng mà không sử dụng các thư viện toán học có sẵn.
**Phân tích thuật toán:** Một cách tiếp cận Brute Force là lặp từ $\min(a,b)$ xuống 1, tốn $\mathcal{O}(\min(a,b))$ thời gian, quá chậm với $10^9$. Do đó, chúng ta chọn **Euclidean Algorithm**. Dựa trên định lý toán học: $GCD(a, b) = GCD(b, a \pmod b)$, cách này giảm kích thước bài toán theo cấp số nhân, vô cùng hiệu quả và không đòi hỏi cấp phát thêm bộ nhớ.
**Mã nguồn Java:**
```java
public class GCD {
    public int gcd(int a, int b) {
        while (b != 0) {
            int temp = b;
            b = a % b;
            a = temp;
        }
        return a;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log(\min(a, b)))$, Space $\mathcal{O}(1)$.

---

## 2. Số Fibonacci Thứ N
**Đề bài chi tiết:** Dãy Fibonacci được định nghĩa là $F_0 = 0, F_1 = 1$, và $F_n = F_{n-1} + F_{n-2}$. Cho số nguyên $n$ ($0 \le n \le 45$), hãy trả về số Fibonacci thứ $n$.
**Phân tích thuật toán:** Sử dụng đệ quy thuần túy sẽ dẫn đến Time Complexity $\mathcal{O}(2^N)$ do tính toán lại nhiều lần (Overlapping subproblems). Thay vì cấp phát một mảng độ dài $N$ tốn Space $\mathcal{O}(N)$, chúng ta nhận thấy rằng để tính $F_n$, ta chỉ cần nhớ 2 giá trị liền trước nó. Việc dùng 2 biến (two pointers) tối ưu không gian tuyệt đối.
**Mã nguồn Java:**
```java
public class Fibonacci {
    public int fib(int n) {
        if (n <= 1) return n;
        int a = 0, b = 1;
        for (int i = 2; i <= n; i++) {
            int sum = a + b;
            a = b;
            b = sum;
        }
        return b;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 3. Kiểm Tra Số Nguyên Tố (Prime Check)
**Đề bài chi tiết:** Cho một số nguyên dương $n$ ($1 \le n \le 2^{31}-1$). Trả về `true` nếu $n$ là số nguyên tố, ngược lại trả về `false`.
**Phân tích thuật toán:** Duyệt từ $2$ đến $n-1$ tốn $\mathcal{O}(N)$, dẫn tới Timeout (TLE) với Input lớn. Giải pháp tối ưu dựa trên toán học: Nếu $n$ có một ước số $d > \sqrt{n}$, thì nó bắt buộc phải có một ước số $n/d < \sqrt{n}$. Do đó, chỉ cần kiểm tra đến $\sqrt{n}$. Kết hợp loại trừ nhanh các số chia hết cho 2 và 3 giúp giảm số vòng lặp đi 2/3.
**Mã nguồn Java:**
```java
public class PrimeCheck {
    public boolean isPrime(int n) {
        if (n <= 1) return false;
        if (n <= 3) return true;
        if (n % 2 == 0 || n % 3 == 0) return false;
        for (int i = 5; i * i <= n; i += 6) {
            if (n % i == 0 || n % (i + 2) == 0) return false;
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\sqrt{N})$, Space $\mathcal{O}(1)$.

---

## 4. Giai Thừa (Factorial)
**Đề bài chi tiết:** Cho số nguyên $n$ ($0 \le n \le 20$). Tính $n!$.
**Phân tích thuật toán:** Đệ quy là cách tự nhiên nhất, nhưng tốn $\mathcal{O}(N)$ Call Stack Memory và có rủi ro StackOverflow nếu Java không tối ưu Tail-Call. Do đó, thuật toán Iteration (Vòng lặp) với biến tích lũy được ưu tiên để giữ Space $\mathcal{O}(1)$. Sử dụng kiểu `long` vì 20! vượt giới hạn của kiểu `int`.
**Mã nguồn Java:**
```java
public class Factorial {
    public long factorial(int n) {
        long result = 1;
        for (int i = 2; i <= n; i++) result *= i;
        return result;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 5. Đảo Ngược Số Nguyên (Reverse Integer)
**Đề bài chi tiết:** Cho một số nguyên 32-bit có dấu. Trả về số đó sau khi đảo ngược thứ tự chữ số. Trả về `0` nếu giá trị đảo bị tràn giới hạn 32-bit.
**Phân tích thuật toán:** Chuyển đổi thành chuỗi (String) rồi đảo ngược tiêu tốn bộ nhớ Heap $\mathcal{O}(\log X)$ và chậm do GC. Thuật toán toán học: liên tục lấy phần dư (modulo 10) để trích xuất chữ số cuối, sau đó nhân kết quả tạm với 10 và cộng vào, hoạt động trực tiếp trên CPU Register. Phải kiểm tra tràn số (Overflow) trước khi nhân.
**Mã nguồn Java:**
```java
public class ReverseInteger {
    public int reverse(int x) {
        int rev = 0;
        while (x != 0) {
            int pop = x % 10;
            x /= 10;
            if (rev > Integer.MAX_VALUE/10 || (rev == Integer.MAX_VALUE / 10 && pop > 7)) return 0;
            if (rev < Integer.MIN_VALUE/10 || (rev == Integer.MIN_VALUE / 10 && pop < -8)) return 0;
            rev = rev * 10 + pop;
        }
        return rev;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log_{10}|X|)$, Space $\mathcal{O}(1)$.

---

## 6. Kiểm Tra Số Palindrome
**Đề bài chi tiết:** Cho một số nguyên $x$. Trả về `true` nếu đọc từ trái sang phải giống hệt phải sang trái. Các số âm không thể là Palindrome.
**Phân tích thuật toán:** Ép kiểu thành String tốn thêm RAM. Ta có thể dùng thuật toán tương tự bài số 5 (Reverse Integer), nhưng chỉ đảo ngược MỘT NỬA số để tránh rủi ro tràn bộ nhớ. Nếu phần đảo ngược bằng với phần còn lại, đó là Palindrome.
**Mã nguồn Java:**
```java
public class PalindromeNumber {
    public boolean isPalindrome(int x) {
        if (x < 0 || (x % 10 == 0 && x != 0)) return false;
        int revertedNumber = 0;
        while (x > revertedNumber) {
            revertedNumber = revertedNumber * 10 + x % 10;
            x /= 10;
        }
        return x == revertedNumber || x == revertedNumber / 10;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log_{10} X)$, Space $\mathcal{O}(1)$.

---

## 7. Căn Bậc Hai Số Học (Sqrt(x))
**Đề bài chi tiết:** Cho số nguyên không âm $x$, trả về phần nguyên căn bậc hai của nó. Cấm dùng `Math.pow()` hoặc `Math.sqrt()`.
**Phân tích thuật toán:** Quét tuyến tính $1, 2, 3...$ đến khi bình phương vượt $x$ tốn $\mathcal{O}(\sqrt{X})$. Áp dụng **Binary Search**, vì hàm $f(k) = k^2$ là hàm đơn điệu tăng (Monotonically increasing). Ta chặt nhị phân từ 1 đến $x$, giảm độ phức tạp xuống $\mathcal{O}(\log X)$. Dùng phép chia `x / mid` để tránh Integer Overflow thay vì `mid * mid`.
**Mã nguồn Java:**
```java
public class SqrtX {
    public int mySqrt(int x) {
        if (x == 0) return 0;
        int left = 1, right = x, ans = 0;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (mid <= x / mid) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log X)$, Space $\mathcal{O}(1)$.

---

## 8. Tính Lũy Thừa (Pow(x, n))
**Đề bài chi tiết:** Implement `pow(x, n)` tính $x^n$. $(-100.0 < x < 100.0)$ và $n$ là số nguyên 32-bit.
**Phân tích thuật toán:** Nhân tuần tự vòng lặp mất $\mathcal{O}(N)$, với $N=2^{31}-1$ sẽ bị TLE (Time Limit Exceeded). Cách tối ưu là **Binary Exponentiation** (Lũy thừa nhị phân). Bằng cách chia nhỏ $x^n = (x^2)^{n/2}$ khi $n$ chẵn, ta cắt giảm một nửa khối lượng tính toán ở mỗi bước. Chú ý xử lý trường hợp $n$ âm (có thể tràn số nếu đổi dấu `Integer.MIN_VALUE`).
**Mã nguồn Java:**
```java
public class Pow {
    public double myPow(double x, int n) {
        long N = n;
        if (N < 0) { x = 1 / x; N = -N; }
        double ans = 1;
        double currentProduct = x;
        for (long i = N; i > 0; i /= 2) {
            if ((i % 2) == 1) ans *= currentProduct;
            currentProduct *= currentProduct;
        }
        return ans;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log N)$, Space $\mathcal{O}(1)$.

---

## 9. Đếm Số Lượng Chữ Số (Count Digits)
**Đề bài chi tiết:** Cho số nguyên $n$, trả về số lượng chữ số của nó. Không dùng cách chuyển thành String.
**Phân tích thuật toán:** Vòng lặp chia cho 10 đến khi bằng 0 tốn $\mathcal{O}(\log_{10}N)$ vòng lặp. Để tính cực nhanh trong $\mathcal{O}(1)$, ta dùng hàm toán học logarit cơ số 10. Số lượng chữ số luôn là $\lfloor \log_{10}(|N|) \rfloor + 1$.
**Mã nguồn Java:**
```java
public class CountDigits {
    public int count(int n) {
        if (n == 0) return 1;
        return (int) Math.floor(Math.log10(Math.abs((double)n))) + 1;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 10. Số Hoàn Hảo (Perfect Number)
**Đề bài chi tiết:** Một số nguyên dương được gọi là Số hoàn hảo nếu tổng tất cả các ước thực sự của nó bằng chính nó. Trả về `true` nếu $n$ là số hoàn hảo.
**Phân tích thuật toán:** Duyệt từ 1 đến $n-1$ quá chậm. Kỹ thuật duyệt ước theo từng cặp $(i, n/i)$ đến $\sqrt{n}$ giúp cắt giảm số lần lặp. Phải cẩn thận để không cộng số $n$ vào tổng (chỉ lấy các ước *thực sự*).
**Mã nguồn Java:**
```java
public class PerfectNumber {
    public boolean checkPerfectNumber(int num) {
        if (num <= 1) return false;
        int sum = 1; // 1 luôn là ước
        for (int i = 2; i * i <= num; i++) {
            if (num % i == 0) {
                sum += i;
                if (i * i != num) sum += num / i;
            }
        }
        return sum == num;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\sqrt{N})$, Space $\mathcal{O}(1)$.

---

## 11. Đổi Nhị Phân Sang Thập Phân (Binary to Decimal)
**Đề bài chi tiết:** Cho chuỗi $S$ gồm các ký tự '0' và '1', chuyển đổi chuỗi nhị phân đó thành số nguyên thập phân (Giả định giá trị nằm trong kiểu `int`).
**Phân tích thuật toán:** Thay vì sử dụng `Integer.parseInt(s, 2)` ẩn giấu logic, ta sử dụng vòng lặp từ trái sang phải, dịch trái kết quả (nhân 2) và cộng với bit hiện tại. Cách tiếp cận này giúp duyệt chuỗi đúng 1 lần (One-pass stream processing) và không cần hàm lũy thừa `Math.pow()`.
**Mã nguồn Java:**
```java
public class BinaryToDecimal {
    public int convert(String binaryStr) {
        int result = 0;
        for (int i = 0; i < binaryStr.length(); i++) {
            result = result * 2 + (binaryStr.charAt(i) - '0');
        }
        return result;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(L)$, Space $\mathcal{O}(1)$ với $L$ là độ dài chuỗi.

---

## 12. Số Armstrong (Narcissistic number)
**Đề bài chi tiết:** Số Armstrong là số nguyên có $k$ chữ số, và tổng các chữ số của nó, mỗi chữ số lũy thừa $k$, bằng chính số đó. Kiểm tra xem một số có phải số Armstrong không.
**Phân tích thuật toán:** Bắt buộc phải tính số lượng chữ số trước (bằng Log10 hoặc đổi thành String). Sau đó sử dụng vòng lặp chia lấy dư liên tục để tách các chữ số, tính lũy thừa và cộng đồn. Thuật toán yêu cầu $\mathcal{O}(\log_{10} N)$ phép lặp.
**Mã nguồn Java:**
```java
public class ArmstrongNumber {
    public boolean isArmstrong(int n) {
        int original = n, sum = 0;
        int k = String.valueOf(n).length();
        while (n > 0) {
            int digit = n % 10;
            sum += Math.pow(digit, k);
            n /= 10;
        }
        return sum == original;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log_{10} N)$, Space $\mathcal{O}(1)$.

---

## 13. FizzBuzz
**Đề bài chi tiết:** Trả về mảng String từ 1 đến $n$. In "FizzBuzz" nếu chia hết cho cả 3 và 5, "Fizz" nếu chia hết cho 3, "Buzz" nếu chia hết cho 5.
**Phân tích thuật toán:** Thuật toán duyệt tuyến tính $\mathcal{O}(N)$ là bắt buộc. Phân tích thiết kế: Việc kết hợp các cờ logic `boolean d3` và `d5` giúp code tránh sử dụng toán tử `%` nhiều lần cho cùng một số, giúp mã giả dễ đọc và mô phỏng chính xác nghiệp vụ.
**Mã nguồn Java:**
```java
import java.util.List;
import java.util.ArrayList;

public class FizzBuzz {
    public List<String> fizzBuzz(int n) {
        List<String> result = new ArrayList<>(n); // Đặt size ban đầu để tránh resize array
        for (int i = 1; i <= n; i++) {
            boolean d3 = (i % 3 == 0);
            boolean d5 = (i % 5 == 0);
            if (d3 && d5) result.add("FizzBuzz");
            else if (d3) result.add("Fizz");
            else if (d5) result.add("Buzz");
            else result.add(String.valueOf(i));
        }
        return result;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 14. Tổng Các Số Đến N (Sum of 1 to N)
**Đề bài chi tiết:** Tính tổng $1 + 2 + \dots + n$. (N cực lớn).
**Phân tích thuật toán:** Dùng vòng lặp `for` thì mất $\mathcal{O}(N)$ thời gian, dẫn đến TLE nếu $N = 10^9$. Thay vào đó, chúng ta sử dụng công thức cấp số cộng của Gauss: $\frac{n \times (n+1)}{2}$. Ép kiểu `long` trước khi nhân để đề phòng tràn số (Integer Overflow).
**Mã nguồn Java:**
```java
public class SumN {
    public long sum(long n) {
        return (long) n * (n + 1) / 2;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 15. Tìm Số Bị Thiếu (Missing Number)
**Đề bài chi tiết:** Cho mảng kích thước $n$ chứa các số riêng biệt nằm trong phạm vi từ $[0, n]$. Trả về số bị thiếu duy nhất khỏi mảng.
**Phân tích thuật toán:** Sử dụng Hash Set mất $\mathcal{O}(N)$ space. Dùng công thức tổng Gauss trừ đi tổng các phần tử mảng có thể tràn số nếu $n$ quá lớn. Phương pháp sử dụng **Bitwise XOR** là an toàn và tối ưu nhất: $X \oplus X = 0$. XOR toàn bộ chỉ số index và toàn bộ giá trị trong mảng sẽ làm triệt tiêu những số xuất hiện 2 lần, để lại số bị thiếu duy nhất.
**Mã nguồn Java:**
```java
public class MissingNumber {
    public int missingNumber(int[] nums) {
        int missing = nums.length; // Số bị thiếu có thể là N
        for (int i = 0; i < nums.length; i++) {
            missing ^= i ^ nums[i];
        }
        return missing;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 16. Lũy Thừa Của 2 (Power of Two)
**Đề bài chi tiết:** Cho một số nguyên, xác định xem nó có phải là lũy thừa của 2 không (có tồn tại $x$ sao cho $n == 2^x$).
**Phân tích thuật toán:** Dùng vòng lặp chia cho 2 tốn $\mathcal{O}(\log N)$. Giải pháp **Bitmask** cung cấp thời gian $\mathcal{O}(1)$: Một lũy thừa của 2 ở hệ nhị phân chỉ có đúng một bit 1 (vd: `1000`). Nếu trừ đi 1 (`0111`), phép AND giữa chúng sẽ bằng 0 (`1000 & 0111 == 0000`).
**Mã nguồn Java:**
```java
public class PowerOfTwo {
    public boolean isPowerOfTwo(int n) {
        return n > 0 && (n & (n - 1)) == 0;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 17. Số La Mã Sang Nguyên (Roman to Integer)
**Đề bài chi tiết:** Chuyển đổi một chuỗi Số La Mã (như `XIV`) thành một số nguyên.
**Phân tích thuật toán:** Việc phân tích chuỗi từ trái qua phải phức tạp do có các trường hợp trừ (ví dụ IV, IX). Thay vào đó, thuật toán lặp từ phải qua trái là lựa chọn tốt nhất. Nếu ký tự hiện tại có giá trị nhỏ hơn ký tự liền sau nó, ta phải TRỪ giá trị đó vào tổng, ngược lại thì CỘNG vào tổng. Không cần Hash Map phức tạp.
**Mã nguồn Java:**
```java
public class RomanToInteger {
    public int romanToInt(String s) {
        int sum = 0, num = 0, prev = 0;
        for (int i = s.length() - 1; i >= 0; i--) {
            switch(s.charAt(i)) {
                case 'I': num = 1; break;
                case 'V': num = 5; break;
                case 'X': num = 10; break;
                case 'L': num = 50; break;
                case 'C': num = 100; break;
                case 'D': num = 500; break;
                case 'M': num = 1000; break;
            }
            if (num < prev) sum -= num;
            else sum += num;
            prev = num;
        }
        return sum;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 18. Số 0 Tận Cùng Của Giai Thừa (Factorial Trailing Zeroes)
**Đề bài chi tiết:** Cho số nguyên dương $n$, đếm số lượng số 0 ở tận cùng bên phải của kết quả $n!$. Yêu cầu $\mathcal{O}(\log N)$ time.
**Phân tích thuật toán:** Tính $n!$ trước rồi đếm số 0 là bất khả thi vì tràn bộ nhớ. Một số 0 được tạo ra từ phép nhân $2 \times 5$. Trong giai thừa, số lượng thừa số 2 luôn nhiều hơn số lượng thừa số 5. Do đó, chỉ cần đếm tổng số thừa số 5 trong các số từ 1 đến $n$. Áp dụng thuật toán liên tục chia $n$ cho 5 (đếm cả các lũy thừa của 5 như 25, 125).
**Mã nguồn Java:**
```java
public class TrailingZeroes {
    public int trailingZeroes(int n) {
        int count = 0;
        while (n > 0) {
            n /= 5;
            count += n;
        }
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log_5 N)$, Space $\mathcal{O}(1)$.

---

## 19. Sàng Eratosthenes (Sieve of Eratosthenes)
**Đề bài chi tiết:** Tìm tất cả số nguyên tố nhỏ hơn hoặc bằng $n$ ($n \le 10^7$).
**Phân tích thuật toán:** Chạy hàm `isPrime()` trên mỗi số từ 1 đến $n$ mất $\mathcal{O}(N\sqrt{N})$, chạy rất chậm. **Sieve of Eratosthenes** sử dụng mảng boolean để đánh dấu bội số của các số nguyên tố. Nếu tìm thấy một số nguyên tố $p$, ta đánh dấu $p^2, p^2+p, p^2+2p \dots$ là Hợp số (False). Thuật toán này đánh đổi Space (Array boolean kích thước $N$) lấy Tốc độ ánh sáng.
**Mã nguồn Java:**
```java
import java.util.List;
import java.util.ArrayList;

public class Sieve {
    public List<Integer> primes(int n) {
        boolean[] isPrime = new boolean[n + 1];
        for (int i = 2; i <= n; i++) isPrime[i] = true; // Giả định tất cả là nguyên tố
        
        for (int p = 2; p * p <= n; p++) {
            if (isPrime[p]) {
                for (int i = p * p; i <= n; i += p) {
                    isPrime[i] = false; // Đánh dấu bội số
                }
            }
        }
        
        List<Integer> result = new ArrayList<>();
        for (int i = 2; i <= n; i++) {
            if (isPrime[i]) result.add(i);
        }
        return result;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log(\log N))$, Space $\mathcal{O}(N)$.

---

## 20. Trò Chơi Nim (Nim Game)
**Đề bài chi tiết:** Có một đống sỏi $n$ viên. Hai người chơi lần lượt bốc từ 1 đến 3 viên. Người bốc viên sỏi cuối cùng thắng. Bạn là người đi trước, cả 2 người đều chơi tối ưu. Trả về `true` nếu bạn chắc chắn thắng.
**Phân tích thuật toán:** Dùng Quy hoạch động (DP) hay Đệ quy sẽ tốn $\mathcal{O}(N)$, không cần thiết. Thuật toán Toán học / Lý thuyết trò chơi (Game Theory): Nếu còn 4 viên, người đến lượt chắc chắn thua vì bốc 1-3 viên thì đối thủ luôn bốc phần còn lại. Bất cứ số $n$ nào chia hết cho 4, người chơi đầu tiên luôn bị ép vào cửa tử (Losing state). Do đó, chỉ cần check modulo 4.
**Mã nguồn Java:**
```java
public class NimGame {
    public boolean canWinNim(int n) {
        return (n % 4 != 0);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---
*(Vì giới hạn kỹ thuật của hệ thống sinh tệp cho phép độ dài hữu hạn, tài liệu sẽ hiển thị 20 bài đầu tiên với chất lượng phân tích cao nhất cho từng bài. Các bài toán từ 21-30 tuân thủ chính xác quy tắc tương tự, sử dụng các thuật toán như Two Pointers, String manipulation).*

## 21. Cộng Chuỗi Nhị Phân (Add Binary)
**Đề bài chi tiết:** Cho hai chuỗi nhị phân, hãy trả về tổng của chúng dưới dạng một chuỗi nhị phân.
**Phân tích thuật toán:** Ép về số nguyên (Integer.parseInt) sẽ thất bại nếu chuỗi vượt 64 ký tự (tràn kiểu Long). Phương pháp chuẩn là lập trình lại mạch logic "Full Adder" bằng phần mềm: Bắt đầu vòng lặp từ chữ số cuối cùng của cả 2 chuỗi, tính tổng kèm Carry (nhớ). Sử dụng `StringBuilder` để tránh cấp phát vùng nhớ chuỗi vô tội vạ, sau cùng đảo ngược lại (reverse).
**Mã nguồn Java:**
```java
public class AddBinary {
    public String addBinary(String a, String b) {
        StringBuilder sb = new StringBuilder();
        int i = a.length() - 1, j = b.length() -1, carry = 0;
        while (i >= 0 || j >= 0) {
            int sum = carry;
            if (i >= 0) sum += a.charAt(i--) - '0';
            if (j >= 0) sum += b.charAt(j--) - '0';
            sb.append(sum % 2);
            carry = sum / 2;
        }
        if (carry != 0) sb.append(carry);
        return sb.reverse().toString();
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\max(N, M))$, Space $\mathcal{O}(\max(N, M))$.

---

## 22. Tên Cột Excel (Excel Sheet Column Title)
**Đề bài chi tiết:** Chuyển một số nguyên dương đại diện cho số thứ tự của cột Excel thành tên cột bằng chữ cái (ví dụ: 1 -> A, 28 -> AB).
**Phân tích thuật toán:** Bài toán quy về việc chuyển đổi hệ cơ số (từ thập phân sang hệ cơ số 26, dùng các ký tự A-Z). Điểm đánh lừa (Trick) ở đây là hệ cơ số này bắt đầu từ 1 (1-indexed) thay vì 0 (0-indexed). Để áp dụng phép Modulo chuẩn, ta phải trừ `columnNumber` đi 1 trước mỗi vòng lặp lấy dư.
**Mã nguồn Java:**
```java
public class ExcelTitle {
    public String convertToTitle(int columnNumber) {
        StringBuilder sb = new StringBuilder();
        while (columnNumber > 0) {
            columnNumber--;
            sb.append((char) ('A' + columnNumber % 26));
            columnNumber /= 26;
        }
        return sb.reverse().toString();
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log_{26} N)$, Space $\mathcal{O}(\log_{26} N)$ cho bộ nhớ StringBuilder.

---

## 23. Bội Số Chung Nhỏ Nhất (LCM)
**Đề bài chi tiết:** Tìm Bội chung nhỏ nhất (LCM) của hai số nguyên dương `a` và `b`. Đảm bảo kết quả không bị tràn số nguyên.
**Phân tích thuật toán:** Tìm LCM bằng vòng lặp tăng dần là quá chậm $\mathcal{O}(LCM)$. Toán học chỉ ra rằng $LCM(a,b) = \frac{a \times b}{GCD(a,b)}$. Về mặt kỹ thuật lập trình, ta phải thực hiện phép chia trước $\frac{a}{GCD(a,b)}$ rồi mới nhân với $b$ để giảm thiểu nguy cơ Integer Overflow, và dùng biến `long`.
**Mã nguồn Java:**
```java
public class LCM {
    public long lcm(int a, int b) {
        return (long) a / gcd(a, b) * b;
    }
    private int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log(\min(a,b)))$, Space $\mathcal{O}(1)$.

---

## 24. Đếm Số Lượng Ước Số (Count Factors)
**Đề bài chi tiết:** Đếm số lượng ước nguyên dương của một số `n`.
**Phân tích thuật toán:** Quét toàn bộ từ 1 đến $n$ mất $\mathcal{O}(N)$. Áp dụng thủ thuật tương tự thuật toán kiểm tra số nguyên tố: Ước số luôn đi theo cặp. Bằng cách lặp $i$ đến $\sqrt{n}$, mỗi khi $n \pmod i == 0$, ta đếm 2 lần (cho $i$ và cho $n/i$). Đặc biệt chú ý xử lý số chính phương (khi $i = n/i$) chỉ được đếm 1 lần.
**Mã nguồn Java:**
```java
public class CountFactors {
    public int count(int n) {
        int count = 0;
        for (int i = 1; i * i <= n; i++) {
            if (n % i == 0) {
                count++; // Đếm i
                if (i * i != n) count++; // Đếm n/i
            }
        }
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\sqrt{N})$, Space $\mathcal{O}(1)$.

---

## 25. Single Number (Phần Tử Duy Nhất)
**Đề bài chi tiết:** Cho một mảng số nguyên trong đó mỗi phần tử đều xuất hiện chính xác 2 lần, ngoại trừ một phần tử xuất hiện đúng 1 lần. Tìm phần tử đó bằng thuật toán chạy trong Time tuyến tính và dùng Extra Space $\mathcal{O}(1)$.
**Phân tích thuật toán:** Dùng Hash Map đếm tần suất thỏa mãn Time $\mathcal{O}(N)$ nhưng tiêu tốn Space $\mathcal{O}(N)$, vi phạm yêu cầu bài toán. Thuật toán duy nhất và kỳ diệu để giải quyết bài này là toán tử XOR (`^`). Đặc tính của XOR: $x \oplus x = 0$ và $x \oplus 0 = x$. Do đó XOR toàn bộ các số lại, những số xuất hiện 2 lần sẽ tự triệt tiêu nhau, kết quả còn lại đúng phần tử duy nhất.
**Mã nguồn Java:**
```java
public class SingleNumber {
    public int singleNumber(int[] nums) {
        int ans = 0;
        for (int num : nums) ans ^= num;
        return ans;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 26. Dãy Tribonacci
**Đề bài chi tiết:** $T_0=0, T_1=1, T_2=1$. Công thức đệ quy $T_n = T_{n-1} + T_{n-2} + T_{n-3}$. Tìm số $T_n$.
**Phân tích thuật toán:** Tương tự Fibonacci, đệ quy trực tiếp sẽ bị Timeout $\mathcal{O}(3^N)$. Thuật toán tối ưu là dùng vòng lặp, duy trì một cửa sổ trượt (sliding window) kích thước bằng 3 trên Memory. Chỉ cần gán và dịch giá trị t0, t1, t2 ở mỗi bước.
**Mã nguồn Java:**
```java
public class Tribonacci {
    public int tribonacci(int n) {
        if (n == 0) return 0;
        if (n == 1 || n == 2) return 1;
        int t0 = 0, t1 = 1, t2 = 1;
        for (int i = 3; i <= n; i++) {
            int t3 = t0 + t1 + t2;
            t0 = t1; t1 = t2; t2 = t3;
        }
        return t2;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 27. Chữ Số Cuối Cùng Của Lũy Thừa ($A^B$)
**Đề bài chi tiết:** Cho 2 chuỗi cực lớn $A$ và $B$ (Lên tới $10^5$ ký tự). Tìm chữ số cuối cùng của $A^B$.
**Phân tích thuật toán:** Parse chuỗi sang số nguyên là bất khả thi vì tràn số. Trong Toán học, chữ số cuối cùng của số $A$ được quyết định hoàn toàn bởi chữ số hàng đơn vị của $A$. Hơn nữa, chu kỳ lặp (cycle) chữ số tận cùng của bất kỳ số nào khi lũy thừa đều có độ dài không vượt quá 4. Áp dụng Module 4 (Modular Arithmetic) cho toàn bộ chuỗi $B$ để rút gọn mũ $B$ xuống mức cực nhỏ (1, 2, 3, hoặc 4).
**Mã nguồn Java:**
```java
public class LastDigit {
    public int getLastDigit(String a, String b) {
        if (b.equals("0")) return 1;
        int base = a.charAt(a.length() - 1) - '0';
        int exp = 0;
        for (int i = 0; i < b.length(); i++) {
            exp = (exp * 10 + (b.charAt(i) - '0')) % 4; // Tính B mod 4
        }
        if (exp == 0) exp = 4; // Chu kỳ thứ 4
        return (int) Math.pow(base, exp) % 10;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(|B|)$, Space $\mathcal{O}(1)$.

---

## 28. Số Siêu Nguyên Tố (Super Prime)
**Đề bài chi tiết:** Một số Siêu Nguyên Tố là số mà bản thân nó và bất kỳ phần tiền tố nào của nó cũng là số nguyên tố (vd: 73 -> 7, 73). Hãy sinh ra và in ra tất cả các số siêu nguyên tố có đúng $N$ chữ số.
**Phân tích thuật toán:** Sinh từng số $10^{N-1}$ đến $10^N$ rồi check prime là $\mathcal{O}(10^N)$ - bất khả thi. Thay vào đó, áp dụng thuật toán **Backtracking** (Quay lui): Bắt đầu từ tập hạt giống {2, 3, 5, 7}, đính kèm từng chữ số lẻ (1, 3, 5, 7, 9) vào đuôi số đó và kiểm tra Prime. Nếu nó là Prime, tiếp tục đệ quy sâu hơn. Cách cắt tỉa nhánh (Pruning) này triệt tiêu hoàn toàn lượng số không hợp lệ.
**Mã nguồn Java:**
```java
import java.util.List;
import java.util.ArrayList;

public class SuperPrime {
    public List<Integer> generate(int n) {
        List<Integer> res = new ArrayList<>();
        int[] primes = {2, 3, 5, 7};
        for (int p : primes) backtrack(p, 1, n, res);
        return res;
    }
    private void backtrack(int num, int len, int n, List<Integer> res) {
        if (len == n) {
            res.add(num);
            return;
        }
        for (int i = 1; i <= 9; i += 2) {
            int nextNum = num * 10 + i;
            if (isPrime(nextNum)) backtrack(nextNum, len + 1, n, res);
        }
    }
    private boolean isPrime(int x) {
        if(x <= 1) return false;
        for (int i = 2; i * i <= x; i++) if (x % i == 0) return false;
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(k)$, Space $\mathcal{O}(N)$ với $k$ là số lượng nhánh duyệt hợp lệ.

---

## 29. Cấu Trúc Khoảng Cách Khép Kín (Closest Pair Basic)
**Đề bài chi tiết:** Cho mảng 2 chiều chứa các tọa độ (x, y) trên mặt phẳng. Hãy tính giá trị bình phương khoảng cách nhỏ nhất giữa bất kỳ hai điểm nào trong đó.
**Phân tích thuật toán:** Trong chuyên đề giới thiệu thuật toán cơ sở, chúng ta sử dụng Brute-Force lồng vòng lặp $\mathcal{O}(N^2)$ để tính công thức Euclidean Distance. Việc bình phương (bỏ dấu căn bậc 2) là một thủ thuật (Trick) quan trọng giúp tiết kiệm CPU Cycles từ việc gọi `Math.sqrt()` tốn kém. Dùng `Double.MAX_VALUE` làm biến khởi tạo.
**Mã nguồn Java:**
```java
public class ClosestPairBasic {
    public double closest(int[][] points) {
        double minDistance = Double.MAX_VALUE;
        for (int i = 0; i < points.length; i++) {
            for (int j = i + 1; j < points.length; j++) {
                double dist = Math.pow(points[i][0] - points[j][0], 2) + 
                              Math.pow(points[i][1] - points[j][1], 2);
                minDistance = Math.min(minDistance, dist);
            }
        }
        return minDistance;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$, Space $\mathcal{O}(1)$.

---

## 30. Số Uất Ức (Ugly Number)
**Đề bài chi tiết:** Một số nguyên dương được gọi là Ugly Number nếu các ước số nguyên tố của nó bị giới hạn chỉ thuộc tập {2, 3, 5}. Kiểm tra $n$ có phải là Ugly Number không.
**Phân tích thuật toán:** Thay vì phân tích thừa số nguyên tố đầy đủ, chúng ta phân tích cách mã code tiêu thụ (consume) Input. Kỹ thuật là sử dụng vòng lặp: Liên tục chia số $n$ cho 2, 3 và 5 trong khi nó chia hết. Nếu kết thúc vòng lặp mà phần dư lại bằng 1, nghĩa là nó hoàn toàn chỉ gồm các thừa số trên. Nếu khác 1, có thừa số lạ xen vào.
**Mã nguồn Java:**
```java
public class UglyNumber {
    public boolean isUgly(int n) {
        if (n <= 0) return false;
        while (n % 2 == 0) n /= 2;
        while (n % 3 == 0) n /= 3;
        while (n % 5 == 0) n /= 5;
        return n == 1;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log N)$ (do kích thước $n$ giảm cực nhanh sau mỗi phép chia), Space $\mathcal{O}(1)$.
