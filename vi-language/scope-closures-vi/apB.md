# You Don't Know JS Yet: Scope & Closures - Tái bản lần 2
# Phụ lục B: Thực hành

Phụ lục này nhằm mang đến cho bạn một số bài tập thử thách và thú vị để kiểm tra và củng cố mức độ hiểu biết của bạn về các chủ đề chính trong cuốn sách này. Lời khuyên là bạn nên tự mình thử làm các bài tập—trên một trình soạn thảo mã nguồn thực tế!—thay vì nhảy thẳng ngay tới phần lời giải ở cuối. Không chơi gian lận nhé!

Những bài tập này không có một đáp án chuẩn xác duy nhất mà bạn bắt buộc phải làm theo y hệt. Cách tiếp cận của bạn có thể khác đôi chút (hoặc rất nhiều!) so với các giải pháp được trình bày ở đây, và điều đó hoàn toàn ổn.

Không ai phán xét cách bạn viết mã cả. Mong muốn của tôi là sau khi đọc xong cuốn sách này, bạn sẽ cảm thấy tự tin rằng mình có thể giải quyết những dạng bài tập lập trình này dựa trên một nền tảng kiến thức vững chắc. Đó là mục tiêu duy nhất ở đây. Nếu bạn hài lòng với code của mình, tôi cũng thấy vui rồi!

## Các giỏ bi (Buckets of Marbles)

Bạn còn nhớ Hình 2 từ Chương 2 không?

<figure>
    <img src="images/fig2.png" width="300" alt="Colored Scope Bubbles" align="center">
    <figcaption><em>Hình 2 (Ch. 2): Các bong bóng phạm vi (scope) được tô màu</em></figcaption>
    <br><br>
</figure>

Bài tập này yêu cầu bạn viết một chương trình—bất kỳ chương trình nào!—chứa các hàm lồng nhau và các phạm vi khối (block scope), thỏa mãn các ràng buộc sau:

* Nếu bạn tô màu tất cả các phạm vi (bao gồm cả phạm vi toàn cục - global scope!) bằng các màu khác nhau, bạn cần ít nhất sáu màu. Hãy chắc chắn thêm chú thích (comment) trong code để gắn nhãn từng phạm vi với màu sắc tương ứng của nó.

    ĐIỂM THƯỞNG: xác định bất kỳ phạm vi ngầm ẩn (implied scope) nào mà code của bạn có thể có.

* Mỗi phạm vi phải có ít nhất một định danh (identifier).

* Chứa ít nhất hai phạm vi hàm (function scope) và ít nhất hai phạm vi khối (block scope).

* Ít nhất một biến từ phạm vi bên ngoài phải bị che khuất (shadowed) bởi một biến của phạm vi lồng bên trong (xem Chương 3).

* Ít nhất một tham chiếu biến phải phân giải (resolve) tới một khai báo biến ở cấp cao hơn ít nhất hai bậc trong chuỗi phạm vi (scope chain).

| MẸO: |
| :--- |
| Bạn *có thể* chỉ cần viết mấy đoạn code linh tinh dạng foo/bar/baz cho bài tập này, nhưng tôi khuyên bạn nên thử nghĩ ra một dạng code thực tế, không tầm thường và ít nhất là làm được một điều gì đó hợp lý. |

Hãy tự mình làm thử bài tập, sau đó xem giải pháp gợi ý ở cuối phụ lục này.

## Closure (PHẦN 1)

Trước tiên, hãy thực hành closure với một số thao tác toán học máy tính phổ biến: xác định xem một giá trị có phải là số nguyên tố hay không (không có ước số nào khác ngoài 1 và chính nó), và tạo danh sách các thừa số nguyên tố (prime factors - các ước số nguyên tố) cho một số đã cho.

Ví dụ:

```js
isPrime(11);        // true
isPrime(12);        // false

factorize(11);      // [ 11 ]
factorize(12);      // [ 3, 2, 2 ] --> 3*2*2=12
```

Dưới đây là một cách triển khai `isPrime(..)`, được điều chỉnh từ thư viện Math.js: [^MathJSisPrime]

```js
function isPrime(v) {
    if (v <= 3) {
        return v > 1;
    }
    if (v % 2 == 0 || v % 3 == 0) {
        return false;
    }
    var vSqrt = Math.sqrt(v);
    for (let i = 5; i <= vSqrt; i += 6) {
        if (v % i == 0 || v % (i + 2) == 0) {
            return false;
        }
    }
    return true;
}
```

Và đây là cách triển khai cơ bản của `factorize(..)` (đừng nhầm lẫn với `factorial(..)` từ Chương 6):

```js
function factorize(v) {
    if (!isPrime(v)) {
        let i = Math.floor(Math.sqrt(v));
        while (v % i != 0) {
            i--;
        }
        return [
            ...factorize(i),
            ...factorize(v / i)
        ];
    }
    return [v];
}
```

| LƯU Ý: |
| :--- |
| Tôi gọi đây là cơ bản vì nó chưa được tối ưu hóa về mặt hiệu năng. Nó mang tính đệ quy nhị phân (binary-recursive, vốn không thể tối ưu hóa lời gọi đuôi - tail-call optimizable), và nó tạo ra rất nhiều bản sao mảng trung gian. Nó cũng không sắp xếp các thừa số tìm được theo bất kỳ thứ tự nào. Có rất nhiều thuật toán khác cho tác vụ này, nhưng tôi muốn dùng thứ gì đó ngắn gọn và tương đối dễ hiểu cho bài tập của chúng ta. |

Nếu bạn gọi `isPrime(4327)` nhiều lần trong một chương trình, bạn có thể thấy rằng nó sẽ phải thực hiện lại hàng chục bước so sánh/tính toán mỗi lần được gọi. Nếu bạn xem xét hàm `factorize(..)`, nó gọi `isPrime(..)` nhiều lần trong quá trình tính toán danh sách thừa số. Và khả năng cao là phần lớn các lệnh gọi đó bị lặp lại. Điều đó thật lãng phí tài nguyên!

Phần đầu tiên của bài tập này là sử dụng closure để triển khai một bộ nhớ đệm (cache) ghi nhớ kết quả của `isPrime(..)`, để tính nguyên tố (`true` hoặc `false`) của một số nhất định chỉ cần tính toán một lần duy nhất. Gợi ý: chúng ta đã minh họa kiểu caching này trong Chương 6 với `factorial(..)`.

Nếu bạn nhìn vào hàm `factorize(..)`, nó được triển khai bằng đệ quy, nghĩa là nó tự gọi lại chính nó liên tục. Điều đó lại có nghĩa là chúng ta có khả năng thấy nhiều lệnh gọi lãng phí để tính toán các thừa số nguyên tố cho cùng một số. Vì vậy phần thứ hai của bài tập là sử dụng cùng kỹ thuật closure cache đó cho `factorize(..)`.

Hãy dùng các closure riêng biệt để lưu cache cho `isPrime(..)` và `factorize(..)`, thay vì gộp chung chúng vào trong một phạm vi đơn lẻ.

Hãy tự mình làm thử bài tập, sau đó xem giải pháp gợi ý ở cuối phụ lục này.

### Đôi lời về bộ nhớ

Tôi muốn chia sẻ một lưu ý ngắn gọn về kỹ thuật closure cache này và những tác động của nó tới hiệu năng ứng dụng của bạn.

Chúng ta có thể thấy rằng việc hạn chế các lệnh gọi lặp lại giúp cải thiện tốc độ tính toán (trong một số trường hợp, mức cải thiện là vô cùng đáng kể). Nhưng việc áp dụng closure theo cách này tạo ra một sự đánh đổi rõ ràng mà bạn cần phải hết sức lưu ý.

Sự đánh đổi đó là bộ nhớ. Về cơ bản, chúng ta đang làm cho cache của mình (trong bộ nhớ) phình to không giới hạn. Nếu các hàm liên quan được gọi hàng triệu lần với hầu hết là các giá trị đầu vào duy nhất (không trùng lặp), chúng ta sẽ ngốn một lượng bộ nhớ rất lớn. Chi phí này hoàn toàn xứng đáng để đánh đổi, nhưng chỉ khi chúng ta thấy khả năng cao sẽ có sự lặp lại của các giá trị đầu vào thông dụng để có thể tận dụng lợi thế của bộ nhớ đệm.

Nếu hầu như mỗi lệnh gọi đều mang một giá trị đầu vào riêng biệt không lặp lại, và bộ nhớ đệm gần như không bao giờ được *tận dụng* mang lại lợi ích gì, thì đây là kỹ thuật không phù hợp để áp dụng.

Có thể bạn cũng nên cân nhắc một phương pháp caching phức tạp hơn, chẳng hạn như bộ nhớ đệm LRU (least recently used - ít được sử dụng gần đây nhất), giúp giới hạn kích thước của nó; khi chạm tới giới hạn, bộ nhớ đệm LRU sẽ loại bỏ các giá trị... đúng vậy, ít được dùng gần đây nhất!

Mặt trái ở đây là bản thân LRU lại khá phức tạp. Bạn sẽ muốn sử dụng một phiên bản triển khai LRU được tối ưu hóa cao, và cần nhận thức sâu sắc về mọi sự đánh đổi liên quan.

## Closure (PHẦN 2)

Trong bài tập này, chúng ta sẽ tiếp tục thực hành closure bằng cách định nghĩa một tiện ích `toggle(..)` cung cấp cho chúng ta một bộ chuyển đổi giá trị luân phiên (toggler).

Bạn sẽ truyền một hoặc nhiều giá trị (dưới dạng các đối số) vào `toggle(..)`, và nhận lại một hàm. Hàm được trả về đó sẽ luân phiên/xoay vòng lần lượt giữa tất cả các giá trị được truyền vào theo thứ tự, từng giá trị một, mỗi khi nó được gọi liên tục.

```js
function toggle(/* .. */) {
    // ..
}

var hello = toggle("hello");
var onOff = toggle("on","off");
var speed = toggle("slow","medium","fast");

hello();      // "hello"
hello();      // "hello"

onOff();      // "on"
onOff();      // "off"
onOff();      // "on"

speed();      // "slow"
speed();      // "medium"
speed();      // "fast"
speed();      // "slow"
```

Trường hợp biên khi không truyền giá trị nào vào `toggle(..)` không quá quan trọng; một instance toggler như vậy có thể chỉ cần luôn trả về `undefined`.

Hãy tự mình làm thử bài tập, sau đó xem giải pháp gợi ý ở cuối phụ lục này.

## Closure (PHẦN 3)

Trong bài tập thứ ba và cũng là bài tập cuối cùng về closure, chúng ta sẽ triển khai một chiếc máy tính bỏ túi cơ bản. Hàm `calculator()` sẽ tạo ra một thực thể (instance) máy tính duy trì trạng thái của riêng nó, dưới dạng một hàm (`calc(..)`, bên dưới):

```js
function calculator() {
    // ..
}

var calc = calculator();
```

Mỗi lần `calc(..)` được gọi, bạn sẽ truyền vào một ký tự đơn đại diện cho một lần nhấn phím trên máy tính. Để giữ cho mọi thứ đơn giản hơn, chúng ta sẽ giới hạn máy tính chỉ hỗ trợ nhập các chữ số (0-9), các phép tính số học (+, -, \*, /), và phím "=" để tính toán kết quả phép tính. Các phép tính được xử lý nghiêm ngặt theo thứ tự nhập vào; không có phép nhóm ngoặc "( )" hay độ ưu tiên toán tử (operator precedence).

Chúng ta không hỗ trợ nhập số thập phân, nhưng phép chia có thể tạo ra số thập phân. Chúng ta không hỗ trợ nhập số âm, nhưng phép toán "-" có thể tạo ra số âm. Do đó, bạn sẽ có thể tạo ra bất kỳ số âm hoặc số thập phân nào bằng cách trước tiên nhập vào một phép tính để tính ra nó. Sau đó bạn có thể tiếp tục tính toán với giá trị đó.

Giá trị trả về của các lệnh gọi `calc(..)` sẽ mô phỏng những gì được hiển thị trên một chiếc máy tính thật, như phản ánh ký tự vừa được nhấn, hoặc tính tổng kết quả khi nhấn "=".

Ví dụ:

```js
calc("4");     // 4
calc("+");     // +
calc("7");     // 7
calc("3");     // 3
calc("-");     // -
calc("2");     // 2
calc("=");     // 75
calc("*");     // *
calc("4");     // 4
calc("=");     // 300
calc("5");     // 5
calc("-");     // -
calc("5");     // 5
calc("=");     // 0
```

Vì cách sử dụng từng lần gọi như vậy hơi rườm rà, đây là hàm trợ giúp `useCalc(..)`, chạy máy tính với các ký tự lần lượt từ một chuỗi, và tính toán màn hình hiển thị mỗi lần:

```js
function useCalc(calc,keys) {
    return [...keys].reduce(
        function showDisplay(display,key){
            var ret = String( calc(key) );
            return (
                display +
                (
                  (ret != "" && key == "=") ?
                      "=" :
                      ""
                ) +
                ret
            );
        },
        ""
    );
}

useCalc(calc,"4+3=");           // 4+3=7
useCalc(calc,"+9=");            // +9=16
useCalc(calc,"*8=");            // *5=128
useCalc(calc,"7*2*3=");         // 7*2*3=42
useCalc(calc,"1/0=");           // 1/0=ERR
useCalc(calc,"+3=");            // +3=ERR
useCalc(calc,"51=");            // 51
```

Cách sử dụng hợp lý nhất của hàm trợ giúp `useCalc(..)` này là luôn để "=" là ký tự cuối cùng được nhập vào.

Một số định dạng của tổng hiển thị trên máy tính yêu cầu xử lý đặc biệt. Tôi cung cấp hàm `formatTotal(..)` này, mà máy tính của bạn nên sử dụng bất cứ khi nào nó chuẩn bị trả về tổng vừa được tính toán (sau khi nhấn `"="`):

```js
function formatTotal(display) {
    if (Number.isFinite(display)) {
        // giới hạn màn hình hiển thị tối đa 11 ký tự
        let maxDigits = 11;
        // chừa khoảng trống cho ký hiệu "e+"?
        if (Math.abs(display) > 99999999999) {
            maxDigits -= 6;
        }
        // chừa khoảng trống cho dấu "-"?
        if (display < 0) {
            maxDigits--;
        }

        // số nguyên?
        if (Number.isInteger(display)) {
            display = display
                .toPrecision(maxDigits)
                .replace(/\.0+$/,"");
        }
        // số thập phân
        else {
            // chừa khoảng trống cho dấu "."
            maxDigits--;
            // chừa khoảng trống cho số "0" đứng đầu?
            if (
                Math.abs(display) >= 0 &&
                Math.abs(display) < 1
            ) {
                maxDigits--;
            }
            display = display
                .toPrecision(maxDigits)
                .replace(/0+$/,"");
        }
    }
    else {
        display = "ERR";
    }
    return display;
}
```

Đừng quá bận tâm về cách `formatTotal(..)` hoạt động. Hầu hết logic của nó là một loạt các xử lý để giới hạn màn hình hiển thị của máy tính ở mức tối đa 11 ký tự, ngay cả khi cần hiển thị số âm, số thập phân vô hạn tuần hoàn, hay thậm chí ký hiệu số mũ "e+".

Một lần nữa, đừng quá sa đà vào các hành vi cụ thể của chiếc máy tính. Hãy tập trung vào *khả năng ghi nhớ (memory)* của closure.

Hãy tự mình làm thử bài tập, sau đó xem giải pháp gợi ý ở cuối phụ lục này.

## Module

Bài tập này là chuyển đổi chiếc máy tính từ Closure (PHẦN 3) thành một module.

Chúng ta sẽ không thêm bất kỳ chức năng bổ sung nào cho máy tính, mà chỉ thay đổi giao diện (interface) của nó. Thay vì gọi một hàm đơn lẻ `calc(..)`, chúng ta sẽ gọi các phương thức cụ thể trên API công khai (public API) cho mỗi lần "nhấn phím" của máy tính. Kết quả đầu ra vẫn giữ nguyên như cũ.

Module này nên được biểu diễn dưới dạng một hàm xuất xưởng module kinh điển (classic module factory function) có tên là `calculator()`, thay vì một IIFE singleton, để có thể tạo nhiều máy tính nếu muốn.

Public API nên bao gồm các phương thức sau:

* `number(..)` (đầu vào: ký tự/con số được "nhấn")
* `plus()`
* `minus()`
* `mult()`
* `div()`
* `eq()`

Cách dùng sẽ trông như sau:

```js
var calc = calculator();

calc.number("4");     // 4
calc.plus();          // +
calc.number("7");     // 7
calc.number("3");     // 3
calc.minus();         // -
calc.number("2");     // 2
calc.eq();            // 75
```

Hàm `formatTotal(..)` vẫn giữ nguyên từ bài tập trước. Nhưng helper `useCalc(..)` cần được điều chỉnh để hoạt động với module API:

```js
function useCalc(calc,keys) {
    var keyMappings = {
        "+": "plus",
        "-": "minus",
        "*": "mult",
        "/": "div",
        "=": "eq"
    };

    return [...keys].reduce(
        function showDisplay(display,key){
            var fn = keyMappings[key] || "number";
            var ret = String( calc[fn](key) );
            return (
                display +
                (
                  (ret != "" && key == "=") ?
                      "=" :
                      ""
                ) +
                ret
            );
        },
        ""
    );
}

useCalc(calc,"4+3=");           // 4+3=7
useCalc(calc,"+9=");            // +9=16
useCalc(calc,"*8=");            // *5=128
useCalc(calc,"7*2*3=");         // 7*2*3=42
useCalc(calc,"1/0=");           // 1/0=ERR
useCalc(calc,"+3=");            // +3=ERR
useCalc(calc,"51=");            // 51
```

Hãy tự mình làm thử bài tập, sau đó xem giải pháp gợi ý ở cuối phụ lục này.

Khi thực hiện bài tập này, bạn cũng hãy dành chút thời gian suy nghĩ về ưu/nhược điểm của việc biểu diễn máy tính dưới dạng một module so với cách tiếp cận hàm closure từ bài tập trước.

ĐIỂM THƯỞNG: viết ra một vài câu giải thích suy nghĩ của bạn.

ĐIỂM THƯỞNG #2: hãy thử chuyển đổi module của bạn sang các định dạng module khác, bao gồm: UMD, CommonJS, và ESM (ES Modules).

## Giải pháp gợi ý

Hy vọng rằng bạn đã tự mình thử làm các bài tập trước khi đọc tới phần này. Không chơi gian lận nhé!

Hãy nhớ rằng, mỗi giải pháp gợi ý chỉ là một trong số rất nhiều cách tiếp cận vấn đề khác nhau. Chúng không phải là "đáp án duy nhất đúng", nhưng chúng minh họa một cách hợp lý để tiếp cận từng bài tập.

Lợi ích quan trọng nhất mà bạn có thể nhận được từ việc đọc các giải pháp gợi ý này là so sánh chúng với mã nguồn của chính bạn và phân tích xem tại sao mỗi người chúng ta lại đưa ra những lựa chọn giống nhau hoặc khác nhau. Đừng sa đà quá mức vào việc tranh cãi những tiểu tiết vụn vặt (bikeshedding); hãy cố gắng tập trung vào chủ đề chính thay vì các chi tiết nhỏ.

### Gợi ý: Các giỏ bi (Buckets of Marbles)

Bài tập *Buckets of Marbles* có thể được giải quyết như sau:

```js
// RED(1)
const howMany = 100;

// Sàng Eratosthenes
function findPrimes(howMany) {
    // BLUE(2)
    var sieve = Array(howMany).fill(true);
    var max = Math.sqrt(howMany);

    for (let i = 2; i < max; i++) {
        // GREEN(3)
        if (sieve[i]) {
            // ORANGE(4)
            let j = Math.pow(i,2);
            for (let k = j; k < howMany; k += i) {
                // PURPLE(5)
                sieve[k] = false;
            }
        }
    }

    return sieve
        .map(function getPrime(flag,prime){
            // PINK(6)
            if (flag) return prime;
            return flag;
        })
        .filter(function onlyPrimes(v){
            // YELLOW(7)
            return !!v;
        })
        .slice(1);
}

findPrimes(howMany);
// [
//    2, 3, 5, 7, 11, 13, 17,
//    19, 23, 29, 31, 37, 41,
//    43, 47, 53, 59, 61, 67,
//    71, 73, 79, 83, 89, 97
// ]
```

### Gợi ý: Closure (PHẦN 1)

*Bài tập Closure (PHẦN 1)* cho `isPrime(..)` và `factorize(..)` có thể được giải quyết như sau:

```js
var isPrime = (function isPrime(v){
    var primes = {};

    return function isPrime(v) {
        if (v in primes) {
            return primes[v];
        }
        if (v <= 3) {
            return (primes[v] = v > 1);
        }
        if (v % 2 == 0 || v % 3 == 0) {
            return (primes[v] = false);
        }
        let vSqrt = Math.sqrt(v);
        for (let i = 5; i <= vSqrt; i += 6) {
            if (v % i == 0 || v % (i + 2) == 0) {
                return (primes[v] = false);
            }
        }
        return (primes[v] = true);
    };
})();

var factorize = (function factorize(v){
    var factors = {};

    return function findFactors(v) {
        if (v in factors) {
            return factors[v];
        }
        if (!isPrime(v)) {
            let i = Math.floor(Math.sqrt(v));
            while (v % i != 0) {
                i--;
            }
            return (factors[v] = [
                ...findFactors(i),
                ...findFactors(v / i)
            ]);
        }
        return (factors[v] = [v]);
    };
})();
```

Các bước chung tôi đã sử dụng cho từng tiện ích:

1. Bọc một IIFE để xác định phạm vi chứa biến cache.

2. Trong lời gọi hàm bên dưới, trước tiên hãy kiểm tra cache, và nếu kết quả đã biết trước đó, hãy trả về kết quả đó.

3. Tại mỗi vị trí ban đầu có lệnh `return`, hãy gán vào cache và chỉ cần trả về kết quả của phép gán đó—đây là một mẹo tiết kiệm không gian chủ yếu nhằm giúp cho nội dung trong cuốn sách được ngắn gọn.

Tôi cũng đã đổi tên hàm bên trong từ `factorize(..)` thành `findFactors(..)`. Điều này về mặt kỹ thuật là không bắt buộc, nhưng nó giúp làm rõ hơn hàm nào đang được gọi đệ quy.

### Gợi ý: Closure (PHẦN 2)

*Bài tập Closure (PHẦN 2)* `toggle(..)` có thể được giải quyết như sau:

```js
function toggle(...vals) {
    var unset = {};
    var cur = unset;

    return function next(){
        // lưu lại giá trị trước đó vào
        // cuối danh sách
        if (cur != unset) {
            vals.push(cur);
        }
        cur = vals.shift();
        return cur;
    };
}

var hello = toggle("hello");
var onOff = toggle("on","off");
var speed = toggle("slow","medium","fast");

hello();      // "hello"
hello();      // "hello"

onOff();      // "on"
onOff();      // "off"
onOff();      // "on"

speed();      // "slow"
speed();      // "medium"
speed();      // "fast"
speed();      // "slow"
```

### Gợi ý: Closure (PHẦN 3)

*Bài tập Closure (PHẦN 3)* `calculator()` có thể được giải quyết như sau:

```js
// từ trước:
//
// function useCalc(..) { .. }
// function formatTotal(..) { .. }

function calculator() {
    var currentTotal = 0;
    var currentVal = "";
    var currentOper = "=";

    return pressKey;

    // ********************

    function pressKey(key){
        // phím số?
        if (/\d/.test(key)) {
            currentVal += key;
            return key;
        }
        // phím toán tử?
        else if (/[+*/-]/.test(key)) {
            // nhiều phép tính liên tiếp?
            if (
                currentOper != "=" &&
                currentVal != ""
            ) {
                // nhấn phím '=' ngầm định
                pressKey("=");
            }
            else if (currentVal != "") {
                currentTotal = Number(currentVal);
            }
            currentOper = key;
            currentVal = "";
            return key;
        }
        // phím =?
        else if (
            key == "=" &&
            currentOper != "="
        ) {
            currentTotal = op(
                currentTotal,
                currentOper,
                Number(currentVal)
            );
            currentOper = "=";
            currentVal = "";
            return formatTotal(currentTotal);
        }
        return "";
    };

    function op(val1,oper,val2) {
        var ops = {
            // LƯU Ý: dùng arrow functions
            // chỉ để ngắn gọn trong sách
            "+": (v1,v2) => v1 + v2,
            "-": (v1,v2) => v1 - v2,
            "*": (v1,v2) => v1 * v2,
            "/": (v1,v2) => v1 / v2
        };
        return ops[oper](val1,val2);
    }
}

var calc = calculator();

useCalc(calc,"4+3=");           // 4+3=7
useCalc(calc,"+9=");            // +9=16
useCalc(calc,"*8=");            // *5=128
useCalc(calc,"7*2*3=");         // 7*2*3=42
useCalc(calc,"1/0=");           // 1/0=ERR
useCalc(calc,"+3=");            // +3=ERR
useCalc(calc,"51=");            // 51
```

| LƯU Ý: |
| :--- |
| Hãy nhớ rằng: bài tập này nói về closure. Đừng quá chú trọng vào cơ chế thực tế của một chiếc máy tính, mà hãy tập trung vào việc liệu bạn có đang *ghi nhớ (remembering)* trạng thái máy tính qua các lệnh gọi hàm một cách hợp lý hay không. |

### Gợi ý: Module

*Bài tập Module* `calculator()` có thể được giải quyết như sau:

```js
// từ trước:
//
// function useCalc(..) { .. }
// function formatTotal(..) { .. }

function calculator() {
    var currentTotal = 0;
    var currentVal = "";
    var currentOper = "=";

    var publicAPI = {
        number,
        eq,
        plus() { return operator("+"); },
        minus() { return operator("-"); },
        mult() { return operator("*"); },
        div() { return operator("/"); }
    };

    return publicAPI;

    // ********************

    function number(key) {
        // phím số?
        if (/\d/.test(key)) {
            currentVal += key;
            return key;
        }
    }

    function eq() {
        // phím =?
        if (currentOper != "=") {
            currentTotal = op(
                currentTotal,
                currentOper,
                Number(currentVal)
            );
            currentOper = "=";
            currentVal = "";
            return formatTotal(currentTotal);
        }
        return "";
    }

    function operator(key) {
        // nhiều phép tính liên tiếp?
        if (
            currentOper != "=" &&
            currentVal != ""
        ) {
            // nhấn phím '=' ngầm định
            eq();
        }
        else if (currentVal != "") {
            currentTotal = Number(currentVal);
        }
        currentOper = key;
        currentVal = "";
        return key;
    }

    function op(val1,oper,val2) {
        var ops = {
            // LƯU Ý: dùng arrow functions
            // chỉ để ngắn gọn trong sách
            "+": (v1,v2) => v1 + v2,
            "-": (v1,v2) => v1 - v2,
            "*": (v1,v2) => v1 * v2,
            "/": (v1,v2) => v1 / v2
        };
        return ops[oper](val1,val2);
    }
}

var calc = calculator();

useCalc(calc,"4+3=");           // 4+3=7
useCalc(calc,"+9=");            // +9=16
useCalc(calc,"*8=");            // *5=128
useCalc(calc,"7*2*3=");         // 7*2*3=42
useCalc(calc,"1/0=");           // 1/0=ERR
useCalc(calc,"+3=");            // +3=ERR
useCalc(calc,"51=");            // 51
```

Đó là toàn bộ nội dung của cuốn sách này, xin chúc mừng thành quả của bạn! Khi bạn đã sẵn sàng, hãy tiếp tục với Cuốn 3, *Đối tượng & Lớp (Objects & Classes)*.

[^MathJSisPrime]: *Math.js: isPrime(..)*, https://github.com/josdejong/mathjs/blob/develop/src/function/utils/isPrime.js, 3 March 2020.
