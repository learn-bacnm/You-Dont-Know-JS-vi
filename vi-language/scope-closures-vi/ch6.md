# You Don't Know JS Yet: Scope & Closures - Tái bản lần 2
# Chương 6: Hạn chế phơi bày phạm vi (Limiting Scope Exposure)

Cho đến nay, trọng tâm của chúng ta là giải thích cơ chế hoạt động của scope và các biến. Với nền tảng vững chắc đó, sự chú ý của chúng ta giờ đây hướng tới một cấp độ tư duy cao hơn: các quyết định và khuôn mẫu (pattern) mà chúng ta áp dụng trên toàn bộ chương trình.

Để bắt đầu, chúng ta sẽ xem xét cách thức và lý do tại sao chúng ta nên sử dụng các cấp độ scope khác nhau (các hàm và các khối lệnh) để tổ chức các biến của chương trình, cụ thể là nhằm giảm thiểu sự phơi bày phạm vi quá mức (scope over-exposure).

## Phơi bày tối thiểu (Least Exposure)

Việc các hàm tự định nghĩa scope riêng của chúng là điều hoàn toàn hợp lý. Nhưng tại sao chúng ta cũng cần các khối lệnh (blocks) để tạo ra scope?

Ngành kỹ thuật phần mềm có một nguyên tắc nền tảng, thường được áp dụng trong bảo mật phần mềm, gọi là "Nguyên tắc đặc quyền tối thiểu" (The Principle of Least Privilege - POLP). [^POLP] Và một biến thể của nguyên tắc này áp dụng cho cuộc thảo luận hiện tại của chúng ta thường được gọi là "Phơi bày tối thiểu" (Least Exposure - POLE).

POLP thể hiện một lập trường phòng thủ trong kiến trúc phần mềm: các thành phần của hệ thống nên được thiết kế để hoạt động với đặc quyền tối thiểu, quyền truy cập tối thiểu, và sự phơi bày tối thiểu. Nếu mỗi phần được kết nối với các năng lực cần thiết tối thiểu, toàn bộ hệ thống sẽ mạnh mẽ hơn từ góc độ bảo mật, bởi vì việc một phần bị xâm phạm hoặc gặp sự cố sẽ giảm thiểu tối đa tác động lên phần còn lại của hệ thống.

Nếu POLP tập trung vào thiết kế thành phần ở cấp độ hệ thống, thì biến thể *Phơi bày* (Exposure) POLE lại tập trung ở một cấp độ thấp hơn; chúng ta sẽ áp dụng nó vào cách các scope tương tác với nhau.

Khi tuân theo POLE, chúng ta muốn giảm thiểu sự phơi bày của cái gì? Đơn giản thôi: các biến được đăng ký trong mỗi scope.

Hãy nghĩ theo cách này: tại sao bạn không nên đặt tất cả các biến của chương trình ra ngoài scope toàn cục (global scope)? Điều đó nghe qua có lẽ ngay lập tức tạo cảm giác là một ý tưởng tồi, nhưng cũng rất đáng để xem xét lý do tại sao lại như vậy. Khi các biến được một phần của chương trình sử dụng lại bị phơi bày cho một phần khác của chương trình, thông qua scope, thường sẽ phát sinh ba mối nguy hiểm chính:

* **Xung đột tên gọi (Naming Collisions)**: nếu bạn sử dụng một tên biến/hàm phổ biến và hữu ích ở hai phần khác nhau của chương trình, nhưng định danh đó lại đến từ một scope dùng chung (chẳng hạn như global scope), thì sự xung đột tên sẽ xảy ra, và rất có thể lỗi (bug) sẽ phát sinh khi một phần sử dụng biến/hàm theo cách mà phần kia không lường trước được.

    Ví dụ: hãy tưởng tượng nếu tất cả các vòng lặp của bạn đều dùng chung một biến chỉ số `i` toàn cục, và sau đó tình cờ một vòng lặp trong một hàm đang chạy trong khi một vòng lặp từ hàm khác đang lặp, và giờ đây biến `i` dùng chung nhận một giá trị không mong muốn.

* **Hành vi ngoài dự kiến (Unexpected Behavior)**: nếu bạn phơi bày các biến/hàm mà đáng lẽ việc sử dụng chúng phải là *riêng tư* (private) đối với một phần của chương trình, điều đó cho phép các lập trình viên khác sử dụng chúng theo những cách bạn không hề dự tính, có thể phá vỡ hành vi kỳ vọng và gây ra lỗi.

    Ví dụ: nếu phần chương trình của bạn giả định rằng một mảng chỉ chứa toàn số, nhưng mã lệnh của ai đó lại truy cập và sửa đổi mảng đó để chứa thêm các giá trị boolean và chuỗi, mã lệnh của bạn sau đó có thể hoạt động sai theo những cách không lường trước.

    Tệ hơn nữa, việc để lộ các chi tiết *riêng tư* sẽ mời gọi những kẻ có ý đồ xấu cố gắng lách qua các giới hạn mà bạn đặt ra, để làm những việc với phần phần mềm của bạn mà lẽ ra không được phép.

* **Sự phụ thuộc ngoài ý muốn (Unintended Dependency)**: nếu bạn phơi bày các biến/hàm một cách không cần thiết, nó sẽ mời gọi các lập trình viên khác sử dụng và phụ thuộc vào những phần đáng lẽ là *riêng tư* đó. Mặc dù điều đó không làm hỏng chương trình của bạn ngay hôm nay, nhưng nó tạo ra mối nguy hại cho việc tái cấu trúc (refactoring) trong tương lai, bởi vì giờ đây bạn không thể dễ dàng tái cấu trúc biến hoặc hàm đó nữa mà không có nguy cơ làm hỏng các phần khác của phần mềm mà bạn không kiểm soát.

    Ví dụ: nếu mã lệnh của bạn dựa vào một mảng chứa các con số, và sau đó bạn quyết định rằng tốt hơn nên sử dụng một cấu trúc dữ liệu khác thay vì mảng, lúc này bạn phải gánh chịu trách nhiệm điều chỉnh các phần bị ảnh hưởng khác của phần mềm.

POLE, khi áp dụng vào việc thiết lập scope cho biến/hàm, về cơ bản nói rằng: theo mặc định hãy chỉ phơi bày ở mức tối thiểu cần thiết, giữ mọi thứ khác ở mức riêng tư nhất có thể. Khai báo các biến trong các scope càng nhỏ và lồng nhau càng sâu càng tốt, thay vì đặt mọi thứ vào scope toàn cục (hoặc thậm chí là scope hàm bên ngoài).

Nếu bạn thiết kế phần mềm của mình theo hướng đó, bạn sẽ có cơ hội lớn hơn rất nhiều để tránh (hoặc ít nhất là giảm thiểu) ba mối nguy hại này.

Hãy xem xét:

```js
function diff(x,y) {
    if (x > y) {
        let tmp = x;
        x = y;
        y = tmp;
    }

    return y - x;
}

diff(3,7);      // 4
diff(7,5);      // 2
```

Trong hàm `diff(..)` này, chúng ta muốn đảm bảo rằng `y` lớn hơn hoặc bằng `x`, để khi chúng ta trừ (`y - x`), kết quả sẽ là `0` hoặc lớn hơn. Nếu ban đầu `x` lớn hơn (kết quả sẽ là số âm!), chúng ta hoán đổi `x` và `y` bằng cách sử dụng một biến `tmp`, nhằm giữ cho kết quả luôn dương.

Trong ví dụ đơn giản này, việc `tmp` nằm bên trong khối lệnh `if` hay thuộc về cấp độ hàm dường như không mấy quan trọng—chắc chắn nó không nên là một biến toàn cục! Tuy nhiên, tuân theo nguyên tắc POLE, `tmp` nên được ẩn giấu trong scope càng kín đáo càng tốt. Vì vậy, chúng ta thiết lập phạm vi khối (block scope) cho `tmp` (sử dụng `let`) bên trong khối lệnh `if`.

## Ẩn giấu bên trong phạm vi (hàm) thông thường (Hiding in Plain (Function) Scope)

Đến đây hẳn đã rõ tại sao việc ẩn giấu các khai báo biến và hàm của chúng ta trong các scope thấp nhất (lồng sâu nhất) có thể lại quan trọng. Nhưng làm thế nào để chúng ta thực hiện điều đó?

Chúng ta đã thấy các từ khóa `let` và `const`, vốn là các từ khóa khai báo phạm vi khối (block-scoped declarators); chúng ta sẽ quay lại với chúng chi tiết hơn trong chốc lát. Nhưng trước tiên, việc ẩn giấu các khai báo `var` hoặc `function` trong các scope thì sao? Điều đó có thể dễ dàng thực hiện bằng cách bọc một scope `function` xung quanh một khai báo.

Hãy cùng xem xét một ví dụ mà ở đó scope `function` có thể hữu ích.

Phép toán "giai thừa" (factorial - ký hiệu là "6!") là phép nhân một số nguyên đã cho với tất cả các số nguyên liên tiếp nhỏ hơn nó cho đến `1`—thực tế, bạn có thể dừng lại ở `2` vì nhân với `1` không làm thay đổi giá trị. Nói cách khác, "6!" cũng giống như "6 * 5!", và "6 * 5!" lại giống như "6 * 5 * 4!", cứ tiếp tục như vậy. Do bản chất của phép toán liên quan, một khi giai thừa của bất kỳ số nguyên nào (chẳng hạn như "4!") đã được tính toán, chúng ta không cần phải thực hiện lại công việc đó nữa, vì kết quả sẽ luôn giống nhau.

Vì vậy, nếu bạn tính giai thừa cho `6` một cách ngây thơ, rồi sau đó lại muốn tính giai thừa cho `7`, bạn có thể phải tính toán lại một cách không cần thiết giai thừa của tất cả các số nguyên từ 2 cho đến 6. Nếu bạn sẵn sàng đánh đổi bộ nhớ để lấy tốc độ, bạn có thể giải quyết sự lãng phí tính toán đó bằng cách lưu vào bộ nhớ đệm (cache) giai thừa của từng số nguyên ngay khi nó được tính toán:

```js
var cache = {};

function factorial(x) {
    if (x < 2) return 1;
    if (!(x in cache)) {
        cache[x] = x * factorial(x - 1);
    }
    return cache[x];
}

factorial(6);
// 720

cache;
// {
//     "2": 2,
//     "3": 6,
//     "4": 24,
//     "5": 120,
//     "6": 720
// }

factorial(7);
// 5040
```

Chúng ta đang lưu trữ tất cả các giai thừa đã tính vào `cache` để qua nhiều lần gọi `factorial(..)`, các kết quả tính toán trước đó vẫn còn nguyên vẹn. Nhưng biến `cache` rõ ràng là một chi tiết *riêng tư* trong cách thức hoạt động của `factorial(..)`, không phải là thứ nên bị phơi bày ra scope bên ngoài—đặc biệt là không phải scope toàn cục.

| LƯU Ý: |
| :--- |
| `factorial(..)` ở đây mang tính đệ quy—một lời gọi tới chính nó được thực hiện từ bên trong—nhưng điều đó chỉ nhằm mục đích giữ cho mã lệnh ngắn gọn; một cách triển khai không đệ quy cũng sẽ mang lại kết quả phân tích scope tương tự đối với `cache`. |

Tuy nhiên, việc khắc phục vấn đề phơi bày quá mức này không đơn giản chỉ là giấu biến `cache` vào bên trong `factorial(..)`, như ta tưởng tượng. Vì chúng ta cần `cache` tồn tại qua nhiều lần gọi hàm, nó phải được đặt ở một scope nằm ngoài hàm đó. Vậy chúng ta có thể làm gì?

Hãy định nghĩa một scope trung gian khác (nằm giữa scope bên ngoài/toàn cục và bên trong của `factorial(..)`) để đặt `cache`:

```js
// outer/global scope

function hideTheCache() {
    // "middle scope", nơi chúng ta giấu `cache`
    var cache = {};

    return factorial;

    // **********************

    function factorial(x) {
        // inner scope
        if (x < 2) return 1;
        if (!(x in cache)) {
            cache[x] = x * factorial(x - 1);
        }
        return cache[x];
    }
}

var factorial = hideTheCache();

factorial(6);
// 720

factorial(7);
// 5040
```

Hàm `hideTheCache()` không phục vụ mục đích nào khác ngoài việc tạo ra một scope để `cache` tồn tại xuyên suốt nhiều lần gọi `factorial(..)`. Nhưng để `factorial(..)` có quyền truy cập vào `cache`, chúng ta phải định nghĩa `factorial(..)` bên trong chính scope đó. Sau đó, chúng ta trả về tham chiếu hàm, như một giá trị từ `hideTheCache()`, và lưu nó vào một biến ở scope bên ngoài, cũng được đặt tên là `factorial`. Giờ đây, khi chúng ta gọi `factorial(..)` (nhiều lần!), `cache` tồn tại bền bỉ của nó vẫn được ẩn giấu nhưng chỉ có `factorial(..)` mới truy cập được!

Được rồi, nhưng... sẽ thật tẻ nhạt khi phải định nghĩa (và đặt tên!) một scope hàm `hideTheCache(..)` mỗi khi phát sinh nhu cầu ẩn giấu biến/hàm như vậy, đặc biệt là khi chúng ta có thể muốn tránh xung đột tên với hàm này bằng cách đặt cho mỗi trường hợp một cái tên duy nhất. Thật phiền phức.

| LƯU Ý: |
| :--- |
| Kỹ thuật được minh họa—lưu trữ đầu ra được tính toán của một hàm vào bộ nhớ đệm để tối ưu hóa hiệu năng khi dự kiến có các lần gọi lặp lại với cùng đầu vào—rất phổ biến trong thế giới Lập trình Hàm (Functional Programming - FP), thường được gọi là "memoization"; việc lưu cache này dựa vào closure (xem Chương 7). Ngoài ra, còn có những lo ngại về mức độ sử dụng bộ nhớ (được đề cập trong "Đôi lời về bộ nhớ" ở Phụ lục B). Các thư viện FP thường cung cấp một tiện ích memoization tối ưu và đã được kiểm chứng cho các hàm, điều này sẽ thay thế cho `hideTheCache(..)` ở đây. Memoization vượt quá phạm vi (*scope* - một cách chơi chữ!) của cuộc thảo luận của chúng ta, nhưng hãy xem cuốn sách *Functional-Light JavaScript* của tôi để biết thêm thông tin. |

Thay vì định nghĩa một hàm mới với tên duy nhất mỗi khi xảy ra tình huống cần-scope-chỉ-để-giấu-biến, một giải pháp có lẽ tốt hơn là sử dụng một biểu thức hàm (function expression):

```js
var factorial = (function hideTheCache() {
    var cache = {};

    function factorial(x) {
        if (x < 2) return 1;
        if (!(x in cache)) {
            cache[x] = x * factorial(x - 1);
        }
        return cache[x];
    }

    return factorial;
})();

factorial(6);
// 720

factorial(7);
// 5040
```

Khoan đã! Cách này vẫn đang sử dụng một hàm để tạo scope nhằm ẩn giấu `cache`, và trong trường hợp này, hàm vẫn được đặt tên là `hideTheCache`, vậy thì nó giải quyết được vấn đề gì chứ?

Hãy nhớ lại từ "Phạm vi tên hàm" (Function Name Scope trong Chương 3), điều gì xảy ra với định danh tên của một biểu thức `function`. Vì `hideTheCache(..)` được định nghĩa dưới dạng một biểu thức `function` thay vì một khai báo `function` (function declaration), tên của nó nằm trong scope của chính nó—về cơ bản cùng scope với `cache`—thay vì nằm trong scope bên ngoài/toàn cục.

Điều đó có nghĩa là chúng ta có thể đặt cùng một cái tên chính xác cho mọi trường hợp của biểu thức hàm như vậy, mà không bao giờ gặp phải bất kỳ xung đột nào. Phù hợp hơn, chúng ta có thể đặt tên cho mỗi trường hợp theo ngữ nghĩa dựa trên bất kỳ thứ gì chúng ta đang cố gắng ẩn giấu, và không cần lo lắng rằng bất kể cái tên nào chúng ta chọn sẽ xung đột với bất kỳ scope biểu thức `function` nào khác trong chương trình.

Trên thực tế, chúng ta *có thể* chỉ cần lược bỏ hoàn toàn tên gọi—do đó định nghĩa một "biểu thức `function` ẩn danh" (anonymous function expression) thay thế. Nhưng Phụ lục A sẽ thảo luận về tầm quan trọng của tên gọi ngay cả đối với các hàm chỉ dùng để tạo scope như vậy.

### Thực thi ngay lập tức biểu thức hàm (Invoking Function Expressions Immediately)

Có một chi tiết quan trọng khác trong chương trình đệ quy tính giai thừa trước đó rất dễ bị bỏ qua: dòng ở cuối biểu thức `function` chứa `})();`.

Hãy chú ý rằng chúng ta đã bao bọc toàn bộ biểu thức `function` trong một cặp dấu ngoặc đơn `( .. )`, và sau đó ở phần cuối, chúng ta thêm cặp dấu ngoặc đơn thứ hai `()`; điều đó thực chất đang gọi biểu thức `function` mà chúng ta vừa định nghĩa. Hơn nữa, trong trường hợp này, cặp dấu ngoặc `( .. )` bao quanh đầu tiên ở bên ngoài biểu thức hàm không thực sự bắt buộc về mặt cú pháp (chúng ta sẽ nói thêm về điều này trong chốc lát), nhưng chúng tôi vẫn sử dụng chúng để dễ đọc hơn.

Vì vậy, nói cách khác, chúng ta đang định nghĩa một biểu thức `function` mà sau đó được thực thi ngay lập tức. Khuôn mẫu phổ biến này có một cái tên (rất sáng tạo!): Biểu thức hàm được gọi ngay lập tức (Immediately Invoked Function Expression - IIFE).

Một IIFE rất hữu ích khi chúng ta muốn tạo một scope để ẩn giấu các biến/hàm. Vì nó là một biểu thức, nó có thể được sử dụng ở **bất kỳ** vị trí nào trong một chương trình JS mà một biểu thức được cho phép. Một IIFE có thể được đặt tên, như với `hideTheCache()`, hoặc (phổ biến hơn nhiều!) không có tên/ẩn danh. Và nó có thể đứng độc lập hoặc, như trước đó, là một phần của câu lệnh khác—`hideTheCache()` trả về tham chiếu hàm `factorial()` mà sau đó được gán `=` cho biến `factorial`.

Để so sánh, đây là một ví dụ về một IIFE độc lập:

```js
// outer scope

(function(){
    // inner hidden scope
})();

// more outer scope
```

Không giống như trước đó với `hideTheCache()`, nơi mà dấu `(..)` bao quanh bên ngoài được lưu ý là một lựa chọn phong cách tùy chọn, đối với một IIFE độc lập, chúng là **bắt buộc**; chúng phân biệt `function` đó là một biểu thức chứ không phải một câu lệnh. Tuy nhiên, để nhất quán, hãy luôn bao quanh một `function` IIFE bằng `( .. )`.

| LƯU Ý: |
| :--- |
| Về mặt kỹ thuật, dấu `( .. )` bao quanh không phải là cách cú pháp duy nhất để đảm bảo `function` trong một IIFE được trình phân tích cú pháp (parser) JS coi là một biểu thức hàm. Chúng ta sẽ xem xét một số tùy chọn khác trong Phụ lục A. |

#### Ranh giới hàm (Function Boundaries)

Hãy cẩn thận rằng việc sử dụng một IIFE để định nghĩa một scope có thể dẫn đến một số hệ quả không mong muốn, tùy thuộc vào mã lệnh xung quanh nó. Bởi vì một IIFE là một hàm đầy đủ, ranh giới hàm làm thay đổi hành vi của một số câu lệnh/cấu trúc nhất định.

Ví dụ: câu lệnh `return` trong một đoạn mã nào đó sẽ thay đổi ý nghĩa của nó nếu một IIFE được bọc quanh nó, bởi vì lúc này `return` sẽ tham chiếu đến hàm của IIFE. Các IIFE hàm thông thường (không phải arrow function) cũng làm thay đổi ràng buộc của từ khóa `this`—sẽ nói thêm về điều đó trong cuốn *Objects & Classes*. Và các câu lệnh như `break` và `continue` sẽ không hoạt động xuyên qua ranh giới hàm của IIFE để điều khiển một vòng lặp hoặc khối lệnh bên ngoài.

Vì vậy, nếu đoạn mã bạn cần bọc scope xung quanh có chứa `return`, `this`, `break`, hoặc `continue`, thì một IIFE có lẽ không phải là cách tiếp cận tốt nhất. Trong trường hợp đó, bạn có thể tìm cách tạo scope bằng một khối lệnh (block) thay vì một hàm.

## Giới hạn phạm vi bằng các khối lệnh (Scoping with Blocks)

Đến thời điểm này, bạn hẳn đã cảm thấy khá thoải mái với những lợi ích của việc tạo ra các scope nhằm hạn chế sự phơi bày của định danh.

Cho đến nay, chúng ta đã xem xét việc thực hiện điều này thông qua scope của `function` (tức là IIFE). Nhưng bây giờ chúng ta hãy xem xét việc sử dụng các khai báo `let` với các khối lệnh lồng nhau. Nhìn chung, bất kỳ cặp ngoặc nhọn `{ .. }` nào đóng vai trò là một câu lệnh sẽ hoạt động như một khối lệnh (block), nhưng **không nhất thiết** là một scope.

Một khối lệnh chỉ trở thành một scope khi cần thiết, để chứa các khai báo có phạm vi khối bên trong nó (tức là `let` hoặc `const`). Hãy xem xét:

```js
{
    // chưa nhất thiết là một scope
    
    // ..

    // bây giờ chúng ta biết khối lệnh này cần phải là một scope
    let thisIsNowAScope = true;

    for (let i = 0; i < 5; i++) {
        // đây cũng là một scope, được kích hoạt ở mỗi
        // lần lặp
        if (i % 2 == 0) {
            // đây chỉ là một khối lệnh, không phải là một scope
            console.log(i);
        }
    }
}
// 0 2 4
```

Không phải mọi cặp ngoặc nhọn `{ .. }` đều tạo ra các khối lệnh (và do đó đủ điều kiện để trở thành scope):

* Các đối tượng nguyên bản (object literals) sử dụng các cặp ngoặc nhọn `{ .. }` để phân tách danh sách khóa-giá trị của chúng, nhưng các giá trị đối tượng như vậy **không phải** là scope.

* `class` sử dụng dấu ngoặc nhọn `{ .. }` xung quanh phần định nghĩa thân của nó, nhưng đây không phải là một khối lệnh hay một scope.

* Một `function` sử dụng `{ .. }` xung quanh phần thân của nó, nhưng về mặt kỹ thuật đây không phải là một khối lệnh—nó là một câu lệnh duy nhất cho thân hàm. Tuy nhiên, nó *chính là* một scope (hàm).

* Cặp ngoặc nhọn `{ .. }` trên câu lệnh `switch` (xung quanh tập hợp các mệnh đề `case`) không định nghĩa một khối lệnh/scope.

Ngoại trừ những ví dụ không phải khối lệnh như vậy, một cặp ngoặc nhọn `{ .. }` có thể định nghĩa một khối lệnh gắn liền với một câu lệnh (như `if` hoặc `for`), hoặc đứng độc lập một mình—hãy xem cặp ngoặc nhọn `{ .. }` ngoài cùng nhất trong đoạn mã trước. Một khối lệnh tường minh (explicit block) loại này—nếu nó không có khai báo nào, nó thực sự không phải là một scope—không phục vụ mục đích vận hành nào, mặc dù nó vẫn có thể hữu ích như một tín hiệu ngữ nghĩa.

Các khối lệnh `{ .. }` độc lập tường minh luôn là cú pháp JS hợp lệ, nhưng vì chúng không thể là một scope trước khi có `let`/`const` trong ES6, nên chúng khá hiếm gặp. Tuy nhiên, sau ES6, chúng bắt đầu được đón nhận dần dần.

Trong hầu hết các ngôn ngữ hỗ trợ block scoping, một scope khối lệnh tường minh là một mẫu cực kỳ phổ biến để tạo ra một lát cắt phạm vi hẹp cho một hoặc một vài biến. Vì vậy, tuân theo nguyên tắc POLE, chúng ta cũng nên áp dụng mẫu này rộng rãi hơn trong JS; sử dụng block scoping (tường minh) để thu hẹp sự phơi bày của các định danh xuống mức tối thiểu thực tế.

Một scope khối lệnh tường minh có thể hữu ích ngay cả bên trong một khối lệnh khác (cho dù khối bên ngoài có phải là một scope hay không).

Ví dụ:

```js
if (somethingHappened) {
    // đây là một khối lệnh, nhưng không phải là một scope

    {
        // đây vừa là một khối lệnh vừa là một
        // scope tường minh
        let msg = somethingHappened.message();
        notifyOthers(msg);
    }

    // ..

    recoverFromSomething();
}
```

Ở đây, cặp ngoặc nhọn `{ .. }` **bên trong** câu lệnh `if` là một scope khối lệnh tường minh bên trong thậm chí còn nhỏ hơn dành cho `msg`, vì biến đó không cần thiết cho toàn bộ khối `if`. Hầu hết các lập trình viên sẽ chỉ đặt phạm vi khối cho `msg` vào khối `if` rồi tiếp tục công việc. Và công bằng mà nói, khi chỉ có vài dòng mã để xem xét, đó là một phán đoán mang tính tương đối. Nhưng khi mã lệnh ngày một lớn lên, những vấn đề phơi bày quá mức này sẽ trở nên rõ rệt hơn.

Vậy việc thêm cặp `{ .. }` phụ và mức thụt lề có đủ quan trọng không? Tôi nghĩ bạn nên tuân theo POLE và luôn luôn (trong giới hạn hợp lý!) định nghĩa khối lệnh nhỏ nhất cho mỗi biến. Do đó tôi khuyên bạn nên sử dụng scope khối lệnh tường minh bổ sung như minh họa.

Hãy nhớ lại cuộc thảo luận về các lỗi TDZ từ phần "Các biến chưa được khởi tạo (TDZ)" (Chương 5). Gợi ý của tôi ở đó là: để giảm thiểu rủi ro xảy ra lỗi TDZ với các khai báo `let`/`const`, hãy luôn đặt các khai báo đó ở đầu scope của chúng.

Nếu bạn thấy mình đang đặt một khai báo `let` ở giữa một scope, trước tiên hãy nghĩ: "Ồ không! Cảnh báo TDZ!" Nếu khai báo `let` này không cần thiết trong nửa đầu của khối lệnh đó, bạn nên sử dụng một scope khối lệnh tường minh bên trong để thu hẹp hơn nữa phạm vi phơi bày của nó!

Một ví dụ khác với scope khối lệnh tường minh:

```js
function getNextMonthStart(dateStr) {
    var nextMonth, year;

    {
        let curMonth;
        [ , year, curMonth ] = dateStr.match(
                /(\d{4})-(\d{2})-\d{2}/
            ) || [];
        nextMonth = (Number(curMonth) % 12) + 1;
    }

    if (nextMonth == 1) {
        year++;
    }

    return `${ year }-${
            String(nextMonth).padStart(2,"0")
        }-01`;
}
getNextMonthStart("2019-12-25");   // 2020-01-01
```

Trước tiên, hãy xác định các scope và các định danh của chúng:

1. Scope bên ngoài/toàn cục có một định danh, đó là hàm `getNextMonthStart(..)`.

2. Scope hàm của `getNextMonthStart(..)` có ba định danh: `dateStr` (tham số), `nextMonth`, và `year`.

3. Cặp ngoặc nhọn `{ .. }` định nghĩa một scope khối lệnh bên trong bao gồm một biến: `curMonth`.

Vậy tại sao lại đặt `curMonth` vào một scope khối lệnh tường minh thay vì chỉ đặt cạnh `nextMonth` và `year` trong scope hàm cấp cao nhất? Bởi vì `curMonth` chỉ cần thiết cho hai câu lệnh đầu tiên đó; ở cấp độ scope hàm, nó bị phơi bày quá mức.

Ví dụ này có quy mô nhỏ, vì vậy các nguy cơ của việc phơi bày quá mức `curMonth` là khá hạn chế. Nhưng những lợi ích của nguyên tắc POLE đạt được tốt nhất khi bạn rèn luyện tư duy giảm thiểu sự phơi bày phạm vi theo mặc định, như một thói quen. Nếu bạn tuân thủ nguyên tắc một cách nhất quán ngay cả trong những trường hợp nhỏ, nó sẽ phục vụ bạn nhiều hơn khi các chương trình của bạn phát triển lớn mạnh.

Bây giờ chúng ta hãy xem xét một ví dụ thực chất hơn:

```js
function sortNamesByLength(names) {
    var buckets = [];

    for (let firstName of names) {
        if (buckets[firstName.length] == null) {
            buckets[firstName.length] = [];
        }
        buckets[firstName.length].push(firstName);
    }

    // một khối lệnh để thu hẹp phạm vi
    {
        let sortedNames = [];

        for (let bucket of buckets) {
            if (bucket) {
                // sắp xếp từng bucket theo thứ tự bảng chữ cái/chữ số
                bucket.sort();

                // thêm các tên đã sắp xếp vào danh sách
                // đang xử lý của chúng ta
                sortedNames = [
                    ...sortedNames,
                    ...bucket
                ];
            }
        }

        return sortedNames;
    }
}

sortNamesByLength([
    "Sally",
    "Suzy",
    "Frank",
    "John",
    "Jennifer",
    "Scott"
]);
// [ "John", "Suzy", "Frank", "Sally",
//   "Scott", "Jennifer" ]
```

Có sáu định danh được khai báo qua năm scope khác nhau. Liệu tất cả các biến này có thể tồn tại trong cùng một scope bên ngoài/toàn cục duy nhất hay không? Về mặt kỹ thuật là có, vì tất cả chúng đều có tên duy nhất và do đó không xảy ra xung đột tên. Nhưng đây sẽ là cách tổ chức mã lệnh thực sự tồi tệ, và có khả năng dẫn đến cả sự nhầm lẫn lẫn các lỗi trong tương lai.

Chúng ta tách chúng ra thành từng scope lồng nhau bên trong khi thích hợp. Mỗi biến được định nghĩa ở scope trong cùng nhất có thể để chương trình hoạt động như mong muốn.

`sortedNames` có thể đã được định nghĩa trong scope hàm cấp cao nhất, nhưng nó chỉ cần thiết cho nửa sau của hàm này. Để tránh làm phơi bày biến đó quá mức ở một scope cấp cao hơn, chúng ta lại tiếp tục tuân theo POLE và đặt phạm vi khối cho nó trong scope khối lệnh tường minh bên trong.

### `var` *và* `let`

Tiếp theo, hãy nói về khai báo `var buckets`. Biến đó được sử dụng trên toàn bộ hàm (ngoại trừ câu lệnh `return` cuối cùng). Bất kỳ biến nào cần thiết trên toàn bộ (hoặc thậm chí hầu hết) một hàm đều nên được khai báo sao cho việc sử dụng như vậy là hiển nhiên.

| LƯU Ý: |
| :--- |
| Tham số `names` không được sử dụng trên toàn bộ hàm, nhưng không có cách nào để giới hạn scope của một tham số, vì vậy nó vẫn hoạt động như một khai báo có phạm vi toàn hàm. |

Vậy tại sao chúng ta lại sử dụng `var` thay vì `let` để khai báo biến `buckets`? Có cả lý do ngữ nghĩa lẫn kỹ thuật để chọn `var` ở đây.

Về mặt phong cách, `var` từ những ngày đầu của JS luôn báo hiệu "biến thuộc về toàn bộ một hàm." Như chúng ta đã khẳng định trong "Lexical Scope" (Chương 1), `var` gắn vào scope hàm bao quanh gần nhất, bất kể nó xuất hiện ở đâu. Điều đó vẫn đúng ngay cả khi `var` xuất hiện bên trong một khối lệnh:

```js
function diff(x,y) {
    if (x > y) {
        var tmp = x;    // `tmp` có phạm vi hàm
        x = y;
        y = tmp;
    }

    return y - x;
}
```

Mặc dù `var` nằm bên trong một khối lệnh, khai báo của nó có phạm vi hàm (đối với `diff(..)`), chứ không phải phạm vi khối.

Mặc dù bạn có thể khai báo `var` bên trong một khối lệnh (và nó vẫn có phạm vi hàm), tôi khuyên bạn nên tránh cách tiếp cận này ngoại trừ một vài trường hợp cụ thể (được thảo luận trong Phụ lục A). Nếu không, `var` nên được dành riêng để sử dụng trong scope cấp cao nhất của một hàm.

Tại sao không chỉ sử dụng `let` ở cùng vị trí đó? Bởi vì `var` khác biệt về mặt trực quan so với `let` và do đó báo hiệu rõ ràng rằng: "biến này có phạm vi hàm." Sử dụng `let` ở scope cấp cao nhất, đặc biệt nếu không nằm trong vài dòng đầu tiên của một hàm, và khi tất cả các khai báo khác trong các khối lệnh đều sử dụng `let`, sẽ không thu hút sự chú ý trực quan đến sự khác biệt với khai báo có phạm vi hàm.

Nói cách khác, tôi cảm thấy `var` truyền đạt ý nghĩa phạm vi hàm tốt hơn so với `let`, và `let` vừa truyền đạt (vừa đạt được!) phạm vi khối nơi mà `var` không đáp ứng được. Miễn là các chương trình của bạn sẽ cần cả các biến có phạm vi hàm lẫn phạm vi khối, thì cách tiếp cận hợp lý và dễ đọc nhất là sử dụng cả `var` *và* `let` cùng nhau, mỗi loại cho mục đích tốt nhất của riêng nó.

Còn có những lý do ngữ nghĩa và vận hành khác để chọn `var` hoặc `let` trong các tình huống khác nhau. Chúng ta sẽ khám phá lý lẽ bảo vệ cho `var` *và* `let` chi tiết hơn trong Phụ lục A.

| CẢNH BÁO: |
| :--- |
| Lời khuyên của tôi về việc sử dụng cả `var` *và* `let` rõ ràng gây tranh cãi và đi ngược lại số đông. Việc nghe thấy những khẳng định như "var bị hỏng, let sửa chữa nó" và "không bao giờ sử dụng var, let là sự thay thế" phổ biến hơn nhiều. Những ý kiến đó là hợp lệ, nhưng chúng chỉ đơn thuần là ý kiến, giống như của tôi vậy. `var` không hề bị hỏng hay bị phản đối (deprecated) trên thực tế; nó đã hoạt động từ thời kỳ đầu của JS và nó sẽ tiếp tục hoạt động chừng nào JS còn tồn tại. |

### Đặt `let` ở đâu? (Where To `let`?)

Lời khuyên của tôi về việc (hầu như) chỉ dành riêng `var` cho scope hàm cấp cao nhất đồng nghĩa với việc hầu hết các khai báo khác nên sử dụng `let`. Nhưng bạn vẫn có thể băn khoăn làm thế nào để quyết định nơi mỗi khai báo trong chương trình của bạn thuộc về?

POLE đã hướng dẫn bạn về những quyết định đó, nhưng chúng ta hãy đảm bảo nêu rõ điều đó ra. Cách để quyết định không dựa trên việc bạn muốn sử dụng từ khóa nào. Cách để quyết định là tự hỏi: "Mức độ phơi bày phạm vi tối thiểu nào là đủ cho biến này?"

Một khi câu hỏi đó được trả lời, bạn sẽ biết liệu một biến thuộc về scope khối lệnh hay scope hàm. Nếu ban đầu bạn quyết định rằng một biến nên có phạm vi khối, và sau đó nhận ra rằng nó cần được nâng lên để có phạm vi hàm, thì điều đó quyết định sự thay đổi không chỉ ở vị trí khai báo của biến đó, mà còn ở từ khóa khai báo được sử dụng. Quy trình ra quyết định thực sự nên diễn ra như vậy.

Nếu một khai báo thuộc về một scope khối lệnh, hãy sử dụng `let`. Nếu nó thuộc về scope hàm, hãy sử dụng `var` (nhắc lại, đây chỉ là quan điểm của tôi).

Nhưng một cách khác để hình dung quá trình ra quyết định này là xem xét phiên bản tiền-ES6 của một chương trình. Ví dụ, hãy nhớ lại `diff(..)` từ phần trước:

```js
function diff(x,y) {
    var tmp;

    if (x > y) {
        tmp = x;
        x = y;
        y = tmp;
    }

    return y - x;
}
```

Trong phiên bản này của `diff(..)`, `tmp` rõ ràng được khai báo trong scope hàm. Điều đó có phù hợp với `tmp` không? Tôi cho rằng là không. `tmp` chỉ cần thiết cho vài câu lệnh đó. Nó không cần thiết cho câu lệnh `return`. Do đó, nó nên có phạm vi khối.

Trước ES6, chúng ta không có `let` nên chúng ta không thể *thực sự* đưa nó vào phạm vi khối. Nhưng chúng ta có thể làm điều tốt thứ hai để phát tín hiệu về ý định của mình:

```js
function diff(x,y) {
    if (x > y) {
        // `tmp` vẫn có phạm vi hàm, nhưng
        // vị trí đặt ở đây phát tín hiệu ngữ nghĩa
        // về phạm vi khối
        var tmp = x;
        x = y;
        y = tmp;
    }

    return y - x;
}
```

Việc đặt khai báo `var` cho `tmp` bên trong câu lệnh `if` phát tín hiệu cho người đọc mã lệnh rằng `tmp` thuộc về khối lệnh đó. Mặc dù JS không thực thi việc giới hạn phạm vi đó, nhưng tín hiệu ngữ nghĩa vẫn mang lại lợi ích cho người đọc mã của bạn.

Theo góc nhìn này, bạn có thể tìm thấy bất kỳ `var` nào nằm bên trong một khối lệnh dạng này và chuyển nó sang `let` để củng cố tín hiệu ngữ nghĩa vốn đã được gửi đi. Đó là cách sử dụng hợp lý của `let` theo quan điểm của tôi.

Một ví dụ khác trong lịch sử từng dựa vào `var` nhưng giờ đây hầu như luôn nên sử dụng `let` là vòng lặp `for`:

```js
for (var i = 0; i < 5; i++) {
    // làm gì đó
}
```

Bất kể vòng lặp như vậy được định nghĩa ở đâu, `i` về cơ bản hầu như luôn chỉ nên được sử dụng bên trong vòng lặp, trong trường hợp này POLE chỉ ra rằng nó nên được khai báo bằng `let` thay vì `var`:

```js
for (let i = 0; i < 5; i++) {
    // làm gì đó
}
```

Hầu như trường hợp duy nhất mà việc chuyển `var` sang `let` theo cách này sẽ "làm hỏng" mã của bạn là nếu bạn dựa vào việc truy cập biến lặp của vòng lặp (`i`) ở bên ngoài/sau vòng lặp, chẳng hạn như:

```js
for (var i = 0; i < 5; i++) {
    if (checkValue(i)) {
        break;
    }
}

if (i < 5) {
    console.log("The loop stopped early!");
}
```

Khuôn mẫu sử dụng này không phải là quá hiếm gặp, nhưng hầu hết mọi người đều cảm thấy nó có mùi cấu trúc mã kém chất lượng (poor code structure). Một cách tiếp cận thích hợp hơn là sử dụng một biến khác có scope bên ngoài cho mục đích đó:

```js
var lastI;

for (let i = 0; i < 5; i++) {
    lastI = i;
    if (checkValue(i)) {
        break;
    }
}

if (lastI < 5) {
    console.log("The loop stopped early!");
}
```

`lastI` cần thiết trên toàn bộ scope này, vì vậy nó được khai báo bằng `var`. `i` chỉ cần thiết trong (mỗi) lần lặp của vòng lặp, vì vậy nó được khai báo bằng `let`.

### Điểm gài bẫy là gì? (What's the Catch?)

Cho đến nay, chúng ta đã khẳng định rằng `var` và các tham số có phạm vi hàm, còn `let`/`const` báo hiệu các khai báo có phạm vi khối. Có một ngoại lệ nhỏ cần chỉ ra: mệnh đề `catch`.

Kể từ khi `try..catch` được giới thiệu từ thời ES3 (vào năm 1999), mệnh đề `catch` đã sử dụng một khả năng khai báo phạm vi khối bổ sung (ít được biết đến):

```js
try {
    doesntExist();
}
catch (err) {
    console.log(err);
    // ReferenceError: 'doesntExist' is not defined
    // ^^^^ thông điệp được in ra từ ngoại lệ bị bắt
    
    let onlyHere = true;
    var outerVariable = true;
}

console.log(outerVariable);     // true

console.log(err);
// ReferenceError: 'err' is not defined
// ^^^^ đây là một ngoại lệ khác được ném ra (không được bắt)
```

Biến `err` được khai báo bởi mệnh đề `catch` có phạm vi khối đối với khối lệnh đó. Khối mệnh đề `catch` này có thể chứa các khai báo phạm vi khối khác thông qua `let`. Nhưng một khai báo `var` bên trong khối này vẫn gắn vào scope hàm/toàn cục bên ngoài.

ES2019 (gần đây, tại thời điểm viết sách) đã thay đổi các mệnh đề `catch` để việc khai báo của chúng là tùy chọn; nếu việc khai báo bị bỏ qua, khối `catch` không còn (theo mặc định) là một scope nữa; tuy nhiên, nó vẫn là một khối lệnh!

Vì vậy, nếu bạn cần phản ứng với điều kiện *rằng một ngoại lệ đã xảy ra* (để bạn có thể phục hồi một cách an toàn), nhưng bạn không quan tâm đến bản thân giá trị lỗi, bạn có thể bỏ qua khai báo `catch`:

```js
try {
    doOptionOne();
}
catch {   // bỏ qua khai báo catch
    doOptionTwoInstead();
}
```

Đây là một sự đơn giản hóa cú pháp nhỏ nhưng thú vị cho một trường hợp sử dụng khá phổ biến, và cũng có thể có hiệu năng tốt hơn một chút khi loại bỏ một scope không cần thiết!

## Khai báo hàm trong khối lệnh (Function Declarations in Blocks - FiB)

Hiện tại chúng ta đã thấy rằng các khai báo sử dụng `let` hoặc `const` có phạm vi khối, và các khai báo `var` có phạm vi hàm. Vậy còn các khai báo `function` xuất hiện trực tiếp bên trong các khối lệnh thì sao? Dưới dạng một tính năng, điều này được gọi là "FiB" (Function Declarations in Blocks).

Chúng ta thường nghĩ về các khai báo `function` giống như chúng tương đương với một khai báo `var`. Vậy chúng có phạm vi hàm giống như `var` không?

Không và có. Tôi biết... điều đó thật khó hiểu. Hãy cùng đi sâu vào:

```js
if (false) {
    function ask() {
        console.log("Does this run?");
    }
}
ask();
```

Bạn mong đợi chương trình này sẽ làm gì? Ba kết quả hợp lý có thể xảy ra:

1. Lời gọi `ask()` có thể thất bại với một ngoại lệ `ReferenceError`, bởi vì định danh `ask` có phạm vi khối đối với scope khối lệnh `if` và do đó không khả dụng trong scope bên ngoài/toàn cục.

2. Lời gọi `ask()` có thể thất bại với một ngoại lệ `TypeError`, bởi vì định danh `ask` có tồn tại, nhưng nó mang giá trị `undefined` (vì câu lệnh `if` không chạy) và do đó không phải là một hàm có thể gọi được.

3. Lời gọi `ask()` có thể chạy bình thường, in ra thông điệp "Does this run?".

Đây là phần gây bối rối: tùy thuộc vào môi trường JS nào bạn thử chạy đoạn mã đó, bạn có thể nhận được các kết quả khác nhau! Đây là một trong số ít những vùng điên rồ mà ở đó hành vi kế thừa cũ phản bội lại một kết quả có thể dự đoán được.

Đặc tả JS (JS specification) nói rằng các khai báo `function` bên trong các khối lệnh có phạm vi khối, do đó câu trả lời nên là (1). Tuy nhiên, hầu hết các JS engine trên trình duyệt (bao gồm v8, đến từ Chrome nhưng cũng được sử dụng trong Node) sẽ hoạt động theo trường hợp (2), nghĩa là định danh có phạm vi bên ngoài khối `if` nhưng giá trị hàm không được tự động khởi tạo, vì vậy nó vẫn là `undefined`.

Tại sao các JS engine của trình duyệt lại được phép hoạt động trái ngược với đặc tả? Bởi vì các engine này vốn dĩ đã có các hành vi nhất định xung quanh FiB trước khi ES6 giới thiệu phạm vi khối, và người ta lo ngại rằng việc thay đổi để tuân thủ đặc tả có thể làm hỏng một số mã JS của các trang web hiện có. Do đó, một ngoại lệ đã được đưa ra trong Phụ lục B của đặc tả JS, cho phép một số sai lệch nhất định đối với các JS engine của trình duyệt (chỉ riêng chúng!).

| LƯU Ý: |
| :--- |
| Bạn thường không phân loại Node là một môi trường JS trình duyệt, vì nó thường chạy trên máy chủ. Nhưng engine v8 của Node được chia sẻ với các trình duyệt Chrome (và Edge). Vì v8 trước hết là một JS engine của trình duyệt, nó áp dụng ngoại lệ Phụ lục B này, điều đó đồng nghĩa với việc các ngoại lệ của trình duyệt cũng được mở rộng sang Node. |

Một trong những trường hợp sử dụng phổ biến nhất cho việc đặt một khai báo `function` trong một khối lệnh là để định nghĩa hàm theo điều kiện theo cách này hay cách khác (chẳng hạn như với câu lệnh `if..else`) tùy thuộc vào trạng thái môi trường nào đó. Ví dụ:

```js
if (typeof Array.isArray != "undefined") {
    function isArray(a) {
        return Array.isArray(a);
    }
}
else {
    function isArray(a) {
        return Object.prototype.toString.call(a)
            == "[object Array]";
    }
}
```

Rất cám dỗ khi cấu trúc mã theo cách này vì lý do hiệu năng, vì việc kiểm tra `typeof Array.isArray` chỉ được thực hiện một lần, trái ngược với việc chỉ định nghĩa một hàm `isArray(..)` và đặt câu lệnh `if` bên trong nó—việc kiểm tra khi đó sẽ chạy không cần thiết trong mỗi lần gọi.

| CẢNH BÁO: |
| :--- |
| Ngoài những rủi ro về sự sai lệch của FiB, một vấn đề khác với việc định nghĩa hàm theo điều kiện là việc gỡ lỗi (debug) một chương trình như vậy sẽ khó khăn hơn. Nếu bạn gặp lỗi trong hàm `isArray(..)`, trước tiên bạn phải tìm ra cách triển khai `isArray(..)` *nào* thực sự đang chạy! Đôi khi, lỗi xảy ra do phiên bản sai đã được áp dụng vì kiểm tra điều kiện không chính xác! Nếu bạn định nghĩa nhiều phiên bản của một hàm, chương trình đó luôn khó suy luận và bảo trì hơn. |

Ngoài các đoạn mã trước, một số trường hợp góc (corner cases) khác của FiB vẫn đang rình rập; những hành vi như vậy trong các trình duyệt khác nhau và các môi trường JS không phải trình duyệt (các JS engine không dựa trên trình duyệt) có thể sẽ khác nhau. Ví dụ:

```js
if (true) {
    function ask() {
        console.log("Am I called?");
    }
}

if (true) {
    function ask() {
        console.log("Or what about me?");
    }
}

for (let i = 0; i < 5; i++) {
    function ask() {
        console.log("Or is it one of these?");
    }
}

ask();

function ask() {
    console.log("Wait, maybe, it's this one?");
}
```

Hãy nhớ lại rằng hoisting hàm như được mô tả trong "Khi nào tôi có thể sử dụng một biến?" (trong Chương 5) có thể gợi ý rằng hàm `ask()` cuối cùng trong đoạn mã này, với thông điệp "Wait, maybe...", sẽ hoist lên trên lời gọi `ask()`. Vì đây là khai báo hàm cuối cùng có tên đó, nó nên "chiến thắng", đúng không? Đáng tiếc là không.

Tôi không có ý định ghi lại tất cả các trường hợp góc kỳ lạ này, cũng như không cố gắng giải thích tại sao mỗi trường hợp lại hoạt động theo một cách nhất định. Thông tin đó, theo quan điểm của tôi, là những thông tin vụn vặt kế thừa bí truyền không đáng bận tâm.

Mối bận tâm thực sự của tôi với FiB là: tôi có thể đưa ra lời khuyên nào để đảm bảo mã lệnh của bạn hoạt động có thể dự đoán được trong mọi hoàn cảnh?

Theo tôi thấy, câu trả lời thực tế duy nhất để tránh sự thất thường của FiB là chỉ cần tránh FiB hoàn toàn. Nói cách khác, đừng bao giờ đặt một khai báo `function` trực tiếp bên trong bất kỳ khối lệnh nào. Luôn đặt các khai báo `function` ở bất kỳ đâu trong scope cấp cao nhất của một hàm (hoặc trong scope toàn cục).

Vì vậy, đối với ví dụ `if..else` trước đó, đề xuất của tôi là tránh định nghĩa hàm theo điều kiện nếu có thể. Vâng, nó có thể kém hiệu năng hơn một chút, nhưng đây là cách tiếp cận tổng thể tốt hơn:

```js
function isArray(a) {
    if (typeof Array.isArray != "undefined") {
        return Array.isArray(a);
    }
    else {
        return Object.prototype.toString.call(a)
            == "[object Array]";
    }
}
```

Nếu sự sụt giảm hiệu năng đó trở thành một vấn đề then chốt (critical path) đối với ứng dụng của bạn, tôi khuyên bạn nên xem xét cách tiếp cận này:

```js
var isArray = function isArray(a) {
    return Array.isArray(a);
};

// ghi đè định nghĩa, nếu bạn bắt buộc phải làm vậy
if (typeof Array.isArray == "undefined") {
    isArray = function isArray(a) {
        return Object.prototype.toString.call(a)
            == "[object Array]";
    };
}
```

Điều quan trọng cần nhận thấy là ở đây tôi đang đặt một **biểu thức** `function`, chứ không phải một khai báo, bên trong câu lệnh `if`. Điều đó hoàn toàn ổn và hợp lệ, khi các biểu thức `function` xuất hiện bên trong các khối lệnh. Cuộc thảo luận của chúng ta về FiB là nhằm tránh các **khai báo** `function` trong các khối lệnh.

Ngay cả khi bạn kiểm tra chương trình của mình và nó hoạt động chính xác, lợi ích nhỏ bạn có thể nhận được từ việc sử dụng phong cách FiB trong mã của mình hoàn toàn bị lu mờ bởi những rủi ro tiềm ẩn trong tương lai gây bối rối cho các lập trình viên khác, hoặc sự khác biệt trong cách mã của bạn chạy trong các môi trường JS khác nhau.

FiB không đáng để đánh đổi, và nên tránh hoàn toàn.

## Vượt qua các rào cản khối lệnh (Blocked Over)

Mục đích của các quy tắc lexical scoping trong một ngôn ngữ lập trình là để chúng ta có thể tổ chức các biến của chương trình một cách thích hợp, cho cả mục đích vận hành cũng như truyền đạt ngữ nghĩa của mã lệnh.

Và một trong những kỹ thuật tổ chức quan trọng nhất là đảm bảo rằng không có biến nào bị phơi bày quá mức đối với các scope không cần thiết (POLE). Hy vọng giờ đây bạn đã đánh giá cao phạm vi khối (block scoping) sâu sắc hơn nhiều so với trước đây.

Hy vọng đến thời điểm này, bạn cảm thấy mình đang đứng trên mảnh đất vững chắc hơn rất nhiều với sự thấu hiểu về lexical scope. Từ nền tảng đó, chương tiếp theo sẽ nhảy vào chủ đề nặng ký về closure.

[^POLP]: *Principle of Least Privilege*, https://en.wikipedia.org/wiki/Principle_of_least_privilege, 3 March 2020.
