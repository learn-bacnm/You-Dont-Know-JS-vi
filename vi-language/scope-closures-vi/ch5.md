# Bạn Chưa Biết JS: Scope & Closures - Tái bản lần 2
# Chương 5: Vòng Đời (Không Hề) Bí Mật Của Biến

Đến thời điểm này, bạn hẳn đã nắm tương đối vững về sự lồng nhau của các scope, từ global scope đi xuống—được gọi là chuỗi scope (scope chain) của chương trình.

Thế nhưng, việc chỉ biết biến đến từ scope nào mới chỉ là một phần của câu chuyện. Nếu một khai báo biến xuất hiện sau câu lệnh đầu tiên của một scope, thì bất kỳ tham chiếu nào tới định danh đó *trước* khi khai báo sẽ hoạt động ra sao? Chuyện gì xảy ra nếu bạn cố khai báo cùng một biến hai lần trong một scope?

Sắc thái riêng biệt của lexical scope trong JS ẩn chứa rất nhiều chi tiết tinh tế về cách thức và thời điểm các biến ra đời cũng như sẵn sàng để chương trình sử dụng.

## Khi Nào Tôi Có Thể Sử Dụng Một Biến?

Tại thời điểm nào một biến sẽ sẵn sàng để được sử dụng bên trong scope của nó? Dường như có một câu trả lời hiển nhiên: *sau khi* biến đã được khai báo/tạo ra. Phải vậy không? Không hẳn.

Hãy xem xét:

```js
greeting();
// Hello!

function greeting() {
    console.log("Hello!");
}
```

Đoạn mã này hoạt động bình thường. Có thể bạn đã từng thấy hoặc thậm chí từng viết những đoạn mã tương tự trước đây. Nhưng bạn đã bao giờ tự hỏi nó hoạt động như thế nào hoặc tại sao lại hoạt động được chưa? Cụ thể hơn, tại sao bạn có thể truy cập định danh `greeting` từ dòng 1 (để lấy và thực thi một tham chiếu hàm), dù cho khai báo hàm `greeting()` mãi tận dòng 4 mới xuất hiện?

Nhớ lại Chương 1 đã chỉ ra rằng tất cả các định danh đều được đăng ký vào scope tương ứng của chúng trong thời gian biên dịch (compile time). Hơn thế nữa, mọi định danh đều được *tạo ra* ở ngay đầu scope mà nó trực thuộc, **mỗi lần scope đó được bước vào**.

Thuật ngữ phổ biến nhất dùng để chỉ việc một biến có thể nhìn thấy được từ ngay đầu scope bao bọc nó, mặc dù phần khai báo của nó có thể nằm sâu hơn ở phía dưới trong scope, được gọi là **hoisting**.

Thế nhưng, chỉ riêng hoisting thì chưa giải thích trọn vẹn câu hỏi. Chúng ta có thể thấy một định danh tên là `greeting` từ đầu scope, nhưng tại sao chúng ta có thể **gọi** hàm `greeting()` trước khi nó được khai báo?

Nói cách khác, làm thế nào mà biến `greeting` lại có sẵn bất kỳ giá trị nào (tham chiếu hàm) được gán cho nó, ngay từ thời điểm scope bắt đầu chạy? Câu trả lời nằm ở một đặc tính đặc biệt của các khai báo `function` chính quy, gọi là *function hoisting* (hoisting hàm). Khi định danh tên của một khai báo `function` được đăng ký ở đầu scope của nó, nó còn được tự động khởi tạo luôn với tham chiếu của hàm đó. Đó là lý do tại sao hàm có thể được gọi xuyên suốt toàn bộ scope!

Một chi tiết mấu chốt là cả *function hoisting* lẫn *variable hoisting* kiểu `var` đều gắn định danh tên của chúng vào **function scope** (scope hàm) gần nhất bao bọc nó (hoặc, nếu không có, là global scope), chứ không phải là một block scope (scope khối).

| LƯU Ý: |
| :--- |
| Các khai báo với `let` và `const` vẫn được hoist (xem phần thảo luận về TDZ ở phần sau của chương này). Nhưng hai hình thức khai báo này gắn vào block bao bọc chúng thay vì chỉ gắn vào function bao bọc như với các khai báo `var` và `function`. Xem "Scoping with Blocks" ở Chương 6 để biết thêm thông tin. |

### Hoisting: Khai Báo so với Biểu Thức

*Function hoisting* chỉ áp dụng cho các khai báo `function` chính quy (cụ thể là những khai báo xuất hiện bên ngoài các block—xem "FiB" ở Chương 6), chứ không áp dụng cho các phép gán biểu thức `function` (`function` expression). Hãy xem xét:

```js
greeting();
// TypeError

var greeting = function greeting() {
    console.log("Hello!");
};
```

Dòng 1 (`greeting();`) ném ra lỗi. Nhưng *loại* lỗi được ném ra là điều rất quan trọng cần lưu ý. Một `TypeError` có nghĩa là chúng ta đang cố gắng làm một điều gì đó với một giá trị không được phép. Tùy thuộc vào môi trường JS của bạn, thông báo lỗi có thể nói đại loại như: "'undefined' is not a function," hoặc hữu ích hơn là "'greeting' is not a function."

Hãy để ý rằng lỗi này **không phải** là `ReferenceError`. JS không hề bảo rằng nó không tìm thấy `greeting` như một định danh trong scope. Nó đang báo cho chúng ta biết rằng `greeting` đã được tìm thấy nhưng vào thời điểm đó nó không giữ một tham chiếu hàm. Chỉ có hàm mới có thể được gọi thực thi (invoke), vì vậy việc cố gắng gọi thực thi một giá trị không phải là hàm sẽ dẫn đến lỗi.

Nhưng `greeting` nắm giữ cái gì, nếu không phải là tham chiếu hàm?

Ngoài việc được hoisted, các biến được khai báo bằng `var` còn được tự động khởi tạo thành giá trị `undefined` ở ngay đầu scope của chúng—xin nhắc lại, là function gần nhất bao bọc nó, hoặc global scope. Một khi đã được khởi tạo, chúng sẵn sàng để được sử dụng (được gán giá trị, truy xuất giá trị, v.v.) xuyên suốt toàn bộ scope.

Vì vậy ở dòng đầu tiên đó, `greeting` có tồn tại, nhưng nó chỉ giữ giá trị mặc định là `undefined`. Mãi cho đến dòng 4 thì `greeting` mới được gán tham chiếu hàm.

Hãy chú ý kỹ sự phân biệt ở đây. Một khai báo `function` được hoist **và được khởi tạo với giá trị hàm của nó** (xin nhắc lại, gọi là *function hoisting*). Một biến `var` cũng được hoist, rồi được tự động khởi tạo thành `undefined`. Bất kỳ phép gán biểu thức `function` nào tiếp theo cho biến đó đều không diễn ra cho đến khi phép gán đó được xử lý trong quá trình thực thi lúc runtime.

Trong cả hai trường hợp, tên của định danh đều được hoist. Nhưng mối liên kết với tham chiếu hàm không được xử lý tại thời điểm khởi tạo (đầu scope) trừ phi định danh đó được tạo ra trong một khai báo `function` chính quy.

### Variable Hoisting

Hãy cùng xem một ví dụ khác về *variable hoisting* (hoisting biến):

```js
greeting = "Hello!";
console.log(greeting);
// Hello!

var greeting = "Howdy!";
```

Mặc dù `greeting` mãi đến dòng 5 mới được khai báo, nó đã có thể được gán giá trị từ tận dòng 1. Tại sao?

Có hai phần tất yếu để giải thích:

* định danh được hoist,
* **và** nó được tự động khởi tạo với giá trị `undefined` từ đầu scope.

| LƯU Ý: |
| :--- |
| Sử dụng *variable hoisting* kiểu này có lẽ đem lại cảm giác không tự nhiên, và nhiều độc giả có thể hoàn toàn muốn tránh dựa dẫm vào nó trong chương trình của họ. Nhưng liệu có nên tránh mọi loại hoisting (bao gồm cả *function hoisting*) hay không? Chúng ta sẽ tìm hiểu chi tiết hơn những góc nhìn khác nhau về hoisting trong Phụ lục A. |

## Hoisting: Lại Một Phép Ẩn Dụ Khác

Chương 2 ngập tràn các phép ẩn dụ (để minh họa cho scope), nhưng ở đây chúng ta lại phải đối mặt với một ẩn dụ khác: chính bản thân hoisting. Thay vì coi hoisting là một bước thực thi cụ thể mà engine JS thực hiện, sẽ hữu ích hơn nếu coi hoisting như một sự hình tượng hóa các hành động khác nhau mà JS thực hiện để thiết lập chương trình **trước khi thực thi**.

Khẳng định điển hình về ý nghĩa của hoisting là: *kéo lên* (lifting)—giống như nâng một quả tạ nặng lên trên cao—bất kỳ định danh nào lên tận cùng trên cùng của scope. Lời giải thích thường được quả quyết là engine JS sẽ thực sự *viết lại* (rewrite) chương trình đó trước khi thực thi, sao cho nó trông giống như thế này hơn:

```js
var greeting;           // khai báo được hoist
greeting = "Hello!";    // dòng 1 ban đầu
console.log(greeting);  // Hello!
greeting = "Howdy!";    // `var` đã biến mất!
```

Phép ẩn dụ hoisting cho rằng JS tiền xử lý chương trình ban đầu và sắp xếp lại nó một chút, sao cho tất cả các khai báo đã được chuyển lên đầu scope tương ứng của chúng, trước khi thực thi. Hơn nữa, phép ẩn dụ hoisting còn khẳng định rằng các khai báo `function`, trong toàn bộ thể thức của chúng, đều được hoist lên đầu mỗi scope. Hãy xem xét:

```js
studentName = "Suzy";
greeting();
// Hello Suzy!

function greeting() {
    console.log(`Hello ${ studentName }!`);
}
var studentName;
```

"Quy tắc" của phép ẩn dụ hoisting là các khai báo hàm được hoist trước, sau đó các biến được hoist ngay sau tất cả các hàm. Do đó, câu chuyện hoisting gợi ý rằng chương trình được engine JS *sắp xếp lại* trông giống như sau:

```js
function greeting() {
    console.log(`Hello ${ studentName }!`);
}
var studentName;

studentName = "Suzy";
greeting();
// Hello Suzy!
```

Phép ẩn dụ hoisting này rất tiện lợi. Lợi ích của nó là cho phép chúng ta đơn giản hóa (cho qua) khâu tiền xử lý nhìn trước đầy kỳ diệu cần thiết để tìm thấy tất cả những khai báo bị chôn sâu trong các scope và bằng cách nào đó di chuyển (hoist) chúng lên đầu; chúng ta có thể chỉ cần nghĩ về chương trình như thể nó được engine JS thực thi qua **một lượt duy nhất** (single pass), từ trên xuống dưới.

Một lượt duy nhất rõ ràng có vẻ dễ hiểu hơn khẳng định về quá trình xử lý hai giai đoạn (two-phase processing) của Chương 1.

Hoisting với tư cách là cơ chế sắp xếp lại code có thể là một sự đơn giản hóa hấp dẫn, nhưng nó không chính xác. Engine JS không thực sự sắp xếp lại mã lệnh. Nó không thể nhìn trước một cách thần kỳ và tìm ra các khai báo; cách duy nhất để tìm chính xác chúng, cũng như tất cả các ranh giới scope trong chương trình, là phân tích cú pháp (parse) toàn bộ mã nguồn.

Đoán xem phân tích cú pháp là gì nào? Chính là giai đoạn đầu tiên của quá trình xử lý hai giai đoạn! Không có trò nhào lộn tư duy kỳ diệu nào có thể lách qua được sự thật đó.

Vì vậy, nếu phép ẩn dụ hoisting (trong trường hợp tốt nhất) là không chính xác, chúng ta nên làm gì với thuật ngữ này? Tôi nghĩ nó vẫn hữu ích—thực tế là ngay cả các thành viên của TC39 cũng thường xuyên sử dụng nó!—nhưng tôi không nghĩ chúng ta nên tuyên bố rằng nó là sự sắp xếp lại mã nguồn trên thực tế.

| CẢNH BÁO: |
| :--- |
| Các mô hình tư duy sai lệch hoặc chưa hoàn thiện đôi khi vẫn có vẻ như đủ dùng vì thỉnh thoảng chúng có thể vô tình dẫn tới câu trả lời đúng. Nhưng về lâu dài, sẽ khó phân tích và dự đoán kết quả một cách chính xác nếu suy nghĩ của bạn không thực sự ăn khớp với cách thức engine JS hoạt động. |

Tôi khẳng định rằng hoisting *nên* được dùng để chỉ **thao tác trong thời gian biên dịch** (compile-time operation) nhằm tạo ra các chỉ thị runtime phục vụ cho việc tự động đăng ký một biến ở ngay đầu scope của nó, mỗi khi scope đó được bước vào.

Đó là một sự thay đổi tinh tế nhưng quan trọng, từ hoisting như một hành vi runtime chuyển về đúng vị trí của nó trong số các nhiệm vụ ở thời gian biên dịch.

## Khai Báo Lại (Re-declaration)?

Bạn nghĩ điều gì sẽ xảy ra khi một biến được khai báo nhiều hơn một lần trong cùng một scope? Hãy xem xét:

```js
var studentName = "Frank";
console.log(studentName);
// Frank

var studentName;
console.log(studentName);   // ???
```

Bạn mong đợi thông báo thứ hai sẽ in ra cái gì? Nhiều người tin rằng `var studentName` thứ hai đã khai báo lại biến (và do đó "reset" nó), nên họ mong đợi `undefined` sẽ được in ra.

Nhưng liệu có tồn tại thứ gọi là một biến được "khai báo lại" (re-declared) trong cùng một scope không? Không.

Nếu bạn xem xét chương trình này dưới góc nhìn của phép ẩn dụ hoisting, mã lệnh sẽ được sắp xếp lại như thế này cho mục đích thực thi:

```js
var studentName;
var studentName;    // rõ ràng là một no-op vô nghĩa!

studentName = "Frank";
console.log(studentName);
// Frank

console.log(studentName);
// Frank
```

Vì hoisting thực chất là việc đăng ký một biến ở đầu scope, nên không có việc gì cần làm ở giữa scope nơi mà chương trình gốc thực sự có câu lệnh `var studentName` thứ hai. Nó chỉ là một thao tác rỗng hay no-op (no-operation), một câu lệnh vô nghĩa.

| MẸO: |
| :--- |
| Theo phong cách mẩu đối thoại từ Chương 2, *Compiler* sẽ tìm thấy câu lệnh khai báo `var` thứ hai và hỏi *Scope Manager* xem nó đã từng thấy định danh `studentName` chưa; vì nó đã thấy rồi, nên sẽ chẳng còn việc gì khác phải làm cả. |

Cũng quan trọng không kém khi chỉ ra rằng `var studentName;` không có nghĩa là `var studentName = undefined;`, như đa số mọi người vẫn lầm tưởng. Hãy chứng minh chúng khác nhau bằng cách xem xét biến thể này của chương trình:

```js
var studentName = "Frank";
console.log(studentName);   // Frank

var studentName;
console.log(studentName);   // Frank <--- vẫn vậy!

// hãy thêm phép khởi tạo một cách rõ ràng
var studentName = undefined;
console.log(studentName);   // undefined <--- thấy chưa!?
```

Bạn có thấy việc khởi tạo tường minh `= undefined` tạo ra một kết quả khác so với việc giả định nó diễn ra ngầm định khi bị bỏ qua không? Trong phần tiếp theo, chúng ta sẽ quay trở lại chủ đề khởi tạo biến từ các khai báo của chúng.

Một khai báo `var` lặp lại cho cùng một tên định danh trong một scope thực chất là một thao tác không-làm-gì-cả. Dưới đây là một minh họa khác, lần này qua lại với một hàm có cùng tên:

```js
var greeting;

function greeting() {
    console.log("Hello!");
}

// về cơ bản, là một no-op
var greeting;

typeof greeting;        // "function"

var greeting = "Hello!";

typeof greeting;        // "string"
```

Khai báo `greeting` đầu tiên đăng ký định danh vào scope, và vì nó là `var` nên giá trị tự động khởi tạo sẽ là `undefined`. Khai báo `function` không cần phải đăng ký lại định danh, nhưng nhờ có *function hoisting*, nó ghi đè việc tự động khởi tạo để sử dụng tham chiếu hàm. Bản thân dòng `var greeting` thứ hai không làm bất cứ điều gì vì `greeting` đã là một định danh và *function hoisting* đã được ưu tiên cho việc tự động khởi tạo.

Việc thực sự gán `"Hello!"` cho `greeting` làm thay đổi giá trị của nó từ hàm ban đầu `greeting()` thành chuỗi; bản thân từ khóa `var` không gây ra hiệu ứng nào.

Thế còn việc lặp lại một khai báo bên trong một scope bằng cách sử dụng `let` hoặc `const` thì sao?

```js
let studentName = "Frank";

console.log(studentName);

let studentName = "Suzy";
```

Chương trình này sẽ không thực thi, mà thay vào đó ngay lập tức ném ra lỗi `SyntaxError`. Tùy thuộc vào môi trường JS của bạn, thông báo lỗi sẽ chỉ ra đại loại như: "studentName has already been declared." Nói cách khác, đây là trường hợp mà hành vi cố tình "khai báo lại" bị cấm một cách tường minh!

Không chỉ hai khai báo cùng liên quan đến `let` mới ném ra lỗi này. Nếu một trong hai khai báo sử dụng `let`, khai báo còn lại dù là `let` hay `var` thì lỗi vẫn sẽ xảy ra, như được minh họa qua hai biến thể sau:

```js
var studentName = "Frank";

let studentName = "Suzy";
```

và:

```js
let studentName = "Frank";

var studentName = "Suzy";
```

Trong cả hai trường hợp, `SyntaxError` đều bị ném ra ở khai báo *thứ hai*. Nói cách khác, cách duy nhất để "khai báo lại" một biến là sử dụng `var` cho tất cả (từ hai khai báo trở lên) của nó.

Nhưng tại sao lại cấm nó? Lý do cho lỗi này tự thân nó không phải vì kỹ thuật, bởi vì việc "khai báo lại" với `var` xưa nay luôn được cho phép; rõ ràng, sự cho phép tương tự hoàn toàn có thể được áp dụng cho `let`.

Nó thực sự giống một vấn đề "tâm lý hành vi / định hướng kỹ thuật" (social engineering) hơn. Việc "khai báo lại" các biến bị một số người, bao gồm nhiều người trong ủy ban TC39, coi là một thói quen xấu có thể dẫn tới lỗi trong chương trình. Vì vậy khi ES6 giới thiệu `let`, họ quyết định ngăn chặn việc "khai báo lại" bằng một lỗi.

| LƯU Ý: |
| :--- |
| Đây dĩ nhiên là một quan điểm về phong cách viết code, không hẳn là một lập luận kỹ thuật thuần túy. Nhiều nhà phát triển đồng tình với lập trường này, và đó có lẽ là một phần lý do tại sao TC39 đưa lỗi này vào (cũng như để `let` tương thích với `const`). Nhưng một lập luận hợp lý có thể được đưa ra rằng việc giữ tính nhất quán với tiền lệ của `var` sẽ thận trọng hơn, và việc bắt buộc tuân theo quan điểm phong cách như vậy tốt nhất nên để cho các công cụ tự chọn như linter xử lý. Trong Phụ lục A, chúng ta sẽ tìm hiểu xem liệu `var` (và hành vi liên quan của nó, như "khai báo lại") có còn hữu ích trong JS hiện đại hay không. |

Khi *Compiler* hỏi *Scope Manager* về một khai báo, nếu định danh đó đã được khai báo trước đó, và nếu một trong hai hoặc cả hai khai báo được thực hiện bằng `let`, thì một lỗi sẽ được ném ra. Tín hiệu dự kiến gửi tới nhà phát triển là: "Hãy ngừng dựa dẫm vào việc khai báo lại cẩu thả!"

### Hằng Số (Constants)?

Từ khóa `const` còn bị ràng buộc chặt chẽ hơn `let`. Giống như `let`, `const` không thể lặp lại với cùng một định danh trong cùng một scope. Nhưng thực sự có một lý do kỹ thuật mang tính quyết định giải thích tại sao dạng "khai báo lại" đó bị cấm, không giống như `let` cấm "khai báo lại" chủ yếu vì lý do phong cách.

Từ khóa `const` yêu cầu một biến phải được khởi tạo, vì vậy việc bỏ sót phép gán trong khai báo sẽ dẫn đến lỗi `SyntaxError`:

```js
const empty;   // SyntaxError
```

Các khai báo `const` tạo ra các biến không thể bị gán lại (re-assigned):

```js
const studentName = "Frank";
console.log(studentName);
// Frank

studentName = "Suzy";   // TypeError
```

Biến `studentName` không thể bị gán lại vì nó được khai báo bằng `const`.

| CẢNH BÁO: |
| :--- |
| Lỗi bị ném ra khi gán lại `studentName` là `TypeError`, không phải `SyntaxError`. Sự phân biệt tinh tế ở đây thực sự rất quan trọng, nhưng đáng tiếc lại quá dễ bị bỏ qua. Syntax error (lỗi cú pháp) đại diện cho các lỗi trong chương trình khiến nó thậm chí không thể bắt đầu thực thi. Type error (lỗi kiểu) đại diện cho các lỗi phát sinh trong quá trình thực thi chương trình. Trong đoạn mã trên, `"Frank"` đã được in ra trước khi chúng ta xử lý phép gán lại của `studentName`, nơi sau đó mới ném ra lỗi. |

Vì vậy nếu các khai báo `const` không thể bị gán lại, và các khai báo `const` luôn yêu cầu phép gán, thì chúng ta có một lý do kỹ thuật rõ ràng tại sao `const` bắt buộc phải cấm mọi hành vi "khai báo lại": bất kỳ hành vi "khai báo lại" bằng `const` nào cũng tất yếu là một phép gán lại `const`, điều không thể được cho phép!

```js
const studentName = "Frank";

// hiển nhiên điều này phải là một lỗi
const studentName = "Suzy";
```

Vì việc "khai báo lại" với `const` phải bị cấm (dựa trên các cơ sở kỹ thuật đó), TC39 về cơ bản cảm thấy rằng việc "khai báo lại" với `let` cũng nên bị cấm theo, để đảm bảo tính nhất quán. Liệu đây có phải là lựa chọn tốt nhất hay không thì vẫn còn có thể tranh luận, nhưng ít nhất chúng ta đã hiểu được lý do đằng sau quyết định này.

### Các Vòng Lặp (Loops)

Như vậy, qua cuộc thảo luận trước đó, rõ ràng là JS không thực sự muốn chúng ta "khai báo lại" các biến bên trong cùng một scope. Điều đó có vẻ như một lời khuyến cáo rành mạch, cho đến khi bạn cân nhắc xem điều đó có ý nghĩa gì đối với việc thực thi lặp đi lặp lại các câu lệnh khai báo trong các vòng lặp. Hãy xem xét:

```js
var keepGoing = true;
while (keepGoing) {
    let value = Math.random();
    if (value > 0.5) {
        keepGoing = false;
    }
}
```

Liệu `value` có đang bị "khai báo lại" lặp đi lặp lại trong chương trình này không? Chúng ta có gặp phải lỗi bị ném ra không? Không.

Tất cả các quy tắc của scope (bao gồm cả việc "khai báo lại" các biến được tạo bởi `let`) đều được áp dụng *trên mỗi thực thể scope* (per scope instance). Nói cách khác, mỗi lần một scope được bước vào trong quá trình thực thi, mọi thứ đều được thiết lập lại từ đầu.

Mỗi lần lặp của vòng lặp là một thực thể scope mới của riêng nó, và bên trong mỗi thực thể scope, `value` chỉ được khai báo một lần duy nhất. Vì vậy không hề có hành vi "khai báo lại" nào, và do đó không có lỗi. Trước khi chúng ta xem xét các dạng vòng lặp khác, điều gì sẽ xảy ra nếu khai báo `value` trong đoạn mã trước được đổi thành `var`?

```js
var keepGoing = true;
while (keepGoing) {
    var value = Math.random();
    if (value > 0.5) {
        keepGoing = false;
    }
}
```

Liệu `value` có đang bị "khai báo lại" ở đây không, đặc biệt là khi chúng ta biết `var` cho phép điều đó? Không. Bởi vì `var` không được coi là một khai báo block-scoping (xem Chương 6), nó tự gắn mình vào global scope. Vì vậy chỉ có một biến `value` duy nhất, nằm trong cùng scope với `keepGoing` (trong trường hợp này là global scope). Cũng không có việc "khai báo lại" ở đây luôn!

Một cách để hiểu thông suốt tất cả những điều này là nhớ rằng các từ khóa `var`, `let`, và `const` trên thực tế đã bị *loại bỏ* khỏi mã lệnh vào thời điểm nó bắt đầu thực thi. Chúng được xử lý hoàn toàn bởi compiler.

Nếu bạn thử xóa bỏ trong tâm trí các từ khóa khai báo này rồi cố gắng xử lý mã lệnh, điều đó sẽ giúp bạn quyết định xem liệu việc (khai báo) lại có thể xảy ra hay không và khi nào.

Thế còn việc "khai báo lại" với các dạng vòng lặp khác, chẳng hạn như vòng lặp `for` thì sao?

```js
for (let i = 0; i < 3; i++) {
    let value = i * 10;
    console.log(`${ i }: ${ value }`);
}
// 0: 0
// 1: 10
// 2: 20
```

Rõ ràng là chỉ có một `value` được khai báo trên mỗi thực thể scope. Nhưng còn `i` thì sao? Nó có đang bị "khai báo lại" không?

Để trả lời điều đó, hãy xem xét xem `i` nằm trong scope nào. Có vẻ như nó sẽ nằm ở scope bên ngoài (trong trường hợp này là global), nhưng thực tế không phải vậy. Nó nằm trong scope của phần thân vòng lặp `for`, giống hệt như `value`. Trên thực tế, bạn có thể hình dung vòng lặp đó dưới dạng tương đương dài dòng hơn như sau:

```js
{
    // một biến giả định để minh họa
    let $$i = 0;

    for ( /* nothing */; $$i < 3; $$i++) {
        // đây là biến `i` thực sự của vòng lặp!
        let i = $$i;

        let value = i * 10;
        console.log(`${ i }: ${ value }`);
    }
    // 0: 0
    // 1: 10
    // 2: 20
}
```

Bây giờ thì mọi thứ đã sáng tỏ: cả hai biến `i` và `value` đều được khai báo chính xác một lần **trên mỗi thực thể scope**. Không có chuyện "khai báo lại" ở đây.

Thế còn các dạng vòng lặp `for` khác thì sao?

```js
for (let index in students) {
    // cái này hoàn toàn ổn
}

for (let student of students) {
    // cái này cũng vậy
}
```

Điều tương tự cũng áp dụng cho các vòng lặp `for..in` và `for..of`: biến được khai báo được coi là nằm *bên trong* thân vòng lặp, và do đó được xử lý trên mỗi lần lặp (tức là trên mỗi thực thể scope). Không có việc "khai báo lại".

Được rồi, tôi biết bạn đang nghĩ rằng nãy giờ tôi nói nghe như một cái máy hát bị xước đĩa. Nhưng hãy cùng khám phá xem `const` tác động như thế nào đến các cấu trúc lặp này. Hãy xem xét:

```js
var keepGoing = true;
while (keepGoing) {
    // ồ, một hằng số sáng bóng!
    const value = Math.random();
    if (value > 0.5) {
        keepGoing = false;
    }
}
```

Giống hệt như biến thể `let` của chương trình này mà chúng ta đã thấy trước đó, `const` được chạy chính xác một lần bên trong mỗi lần lặp của vòng lặp, do đó nó an toàn trước các rắc rối về "khai báo lại". Nhưng mọi thứ trở nên phức tạp hơn khi chúng ta nói về vòng lặp `for`.

`for..in` và `for..of` hoàn toàn ổn khi sử dụng với `const`:

```js
for (const index in students) {
    // cái này ổn
}

for (const student of students) {
    // cái này cũng ổn
}
```

Nhưng vòng lặp `for` thông thường thì không:

```js
for (const i = 0; i < 3; i++) {
    // ối, cái này sẽ thất bại với
    // một Type Error sau lần lặp đầu tiên
}
```

Có điều gì sai ở đây? Chúng ta có thể dùng `let` ngon lành trong cấu trúc này, và chúng ta đã khẳng định rằng nó tạo ra một `i` mới cho mỗi scope lần lặp, nên nó thậm chí có vẻ chẳng phải là "khai báo lại".

Hãy "mở rộng" trong tâm trí vòng lặp đó giống như chúng ta đã làm lúc nãy:

```js
{
    // một biến giả định để minh họa
    const $$i = 0;

    for ( ; $$i < 3; $$i++) {
        // đây là biến `i` thực sự của vòng lặp!
        const i = $$i;
        // ..
    }
}
```

Bạn có phát hiện ra vấn đề không? Biến `i` của chúng ta quả thực chỉ được tạo ra một lần bên trong vòng lặp. Đó không phải là vấn đề. Vấn đề nằm ở biến mang tính khái niệm `$$i` vốn phải được tăng lên sau mỗi lần lặp bằng biểu thức `$$i++`. Đó là **phép gán lại** (re-assignment, chứ không phải "khai báo lại"), điều không được phép đối với các hằng số.

Hãy nhớ rằng, dạng "mở rộng" này chỉ là một mô hình khái niệm giúp bạn trực giác hóa nguồn gốc của vấn đề. Bạn có thể tự hỏi liệu JS có thể biến `const $$i = 0` thành `let $$i = 0` một cách hiệu quả, từ đó cho phép `const` hoạt động với vòng lặp `for` kinh điển của chúng ta không? Điều đó hoàn toàn khả thi, nhưng khi ấy nó có thể tạo ra những ngoại lệ tiềm ẩn gây bất ngờ đối với ngữ nghĩa của vòng lặp `for`.

Ví dụ, nó sẽ là một ngoại lệ khá tùy tiện (và có khả năng gây bối rối) khi cho phép `i++` ở phần tiêu đề vòng lặp `for` lách qua sự nghiêm ngặt của phép gán `const`, nhưng lại không cho phép các phép gán lại khác đối với `i` bên trong lần lặp của vòng lặp, điều đôi khi rất hữu ích.

Câu trả lời dứt khoát là: `const` không thể được sử dụng với dạng vòng lặp `for` kinh điển vì phép gán lại bắt buộc phải có.

Điều thú vị là, nếu bạn không thực hiện việc gán lại, thì nó lại hợp lệ:

```js
var keepGoing = true;

for (const i = 0; keepGoing; /* không có gì ở đây */ ) {
    keepGoing = (Math.random() > 0.5);
    // ..
}
```

Đoạn mã đó hoạt động được, nhưng nó vô nghĩa. Chẳng có lý do gì để khai báo `i` ở vị trí đó bằng `const`, vì toàn bộ mục đích của một biến ở vị trí đó là **được dùng để đếm số lần lặp**. Hãy dùng một dạng vòng lặp khác, chẳng hạn như vòng lặp `while`, hoặc dùng `let`!

## Biến Chưa Khởi Tạo (hay còn gọi là TDZ)

Với các khai báo `var`, biến được "hoist" lên đầu scope của nó. Nhưng nó cũng được tự động khởi tạo với giá trị `undefined`, sao cho biến có thể được sử dụng xuyên suốt toàn bộ scope.

Tuy nhiên, các khai báo `let` và `const` lại không hoàn toàn giống như vậy ở khía cạnh này.

Hãy xem xét:

```js
console.log(studentName);
// ReferenceError

let studentName = "Suzy";
```

Kết quả của chương trình này là một `ReferenceError` bị ném ra ở dòng đầu tiên. Tùy thuộc vào môi trường JS của bạn, thông báo lỗi có thể nói đại loại như: "Cannot access studentName before initialization."

| LƯU Ý: |
| :--- |
| Thông báo lỗi như thấy ở đây trước kia từng mơ hồ hoặc dễ gây hiểu lầm hơn nhiều. Rất may, một số người trong chúng tôi thuộc cộng đồng đã vận động hành lang thành công để các engine JS cải thiện thông báo lỗi này nhằm báo cho bạn biết chính xác hơn điều gì đang bị sai! |

Thông báo lỗi đó nói lên khá rõ vấn đề: `studentName` có tồn tại ở dòng 1, nhưng nó chưa được khởi tạo, do đó nó chưa thể được sử dụng. Hãy thử cách này:

```js
studentName = "Suzy";   // hãy thử khởi tạo nó xem!
// ReferenceError

console.log(studentName);

let studentName;
```

Ối. Chúng ta vẫn gặp lỗi `ReferenceError`, nhưng giờ là ở dòng đầu tiên nơi chúng ta đang cố gắng gán giá trị cho (hay nói cách khác, khởi tạo!) biến được gọi là "chưa khởi tạo" `studentName` này. Rốt cuộc chuyện là thế nào!?

Câu hỏi thực sự là, làm thế nào để chúng ta khởi tạo một biến chưa khởi tạo? Đối với `let`/`const`, **cách duy nhất** để làm điều đó là thông qua một phép gán đi kèm với một câu lệnh khai báo. Một phép gán đứng riêng lẻ là không đủ! Hãy xem xét:

```js
let studentName = "Suzy";
console.log(studentName);   // Suzy
```

Ở đây, chúng ta đang khởi tạo `studentName` (trong trường hợp này là `"Suzy"` thay vì `undefined`) thông qua dạng câu lệnh khai báo `let` được kết hợp với một phép gán.

Hoặc cách khác:

```js
// ..

let studentName;
// hoặc:
// let studentName = undefined;

// ..

studentName = "Suzy";

console.log(studentName);
// Suzy
```

| LƯU Ý: |
| :--- |
| Thật thú vị! Nhớ lại phần trước, chúng ta đã nói rằng `var studentName;` *không* giống với `var studentName = undefined;`, nhưng ở đây với `let`, chúng lại hoạt động giống hệt nhau. Sự khác biệt quy về thực tế là `var studentName` tự động khởi tạo ở đầu scope, trong khi `let studentName` thì không. |

Hãy nhớ rằng chúng ta đã khẳng định một vài lần rằng *Compiler* rốt cuộc sẽ loại bỏ bất kỳ từ khóa khai báo `var`/`let`/`const` nào, thay thế chúng bằng các chỉ thị ở đầu mỗi scope để đăng ký các định danh tương ứng.

Vì vậy nếu phân tích những gì đang diễn ra ở đây, chúng ta thấy một nét tinh tế bổ sung là *Compiler* cũng đang thêm một chỉ thị vào giữa chương trình, tại vị trí mà biến `studentName` được khai báo, để xử lý việc tự động khởi tạo của khai báo đó. Chúng ta không thể sử dụng biến ở bất kỳ thời điểm nào trước khi quá trình khởi tạo đó diễn ra. Điều này áp dụng cho `const` tương tự như đối với `let`.

Thuật ngữ do TC39 đặt ra để chỉ *khoảng thời gian* từ khi bước vào một scope cho đến khi việc tự động khởi tạo biến diễn ra là: Vùng Chết Tạm Thời hay Temporal Dead Zone (TDZ).

TDZ là khoảng thời gian (time window) mà một biến có tồn tại nhưng vẫn chưa được khởi tạo, và do đó không thể được truy cập theo bất kỳ cách nào. Chỉ có việc thực thi các chỉ thị được *Compiler* để lại tại vị trí khai báo ban đầu mới có thể thực hiện việc khởi tạo đó. Sau thời điểm đó, TDZ kết thúc, và biến được tự do sử dụng cho phần còn lại của scope.

Một biến `var` về mặt kỹ thuật cũng có một TDZ, nhưng nó có độ dài bằng không và do đó không thể quan sát được đối với các chương trình của chúng ta! Chỉ có `let` và `const` mới có một TDZ có thể quan sát được.

Nhân tiện, chữ "temporal" (thuộc về thời gian) trong TDZ quả thực chỉ *thời gian* chứ không phải *vị trí trong mã lệnh*. Hãy xem xét:

```js
askQuestion();
// ReferenceError

let studentName = "Suzy";

function askQuestion() {
    console.log(`${ studentName }, do you know?`);
}
```

Mặc dù xét về mặt vị trí thì `console.log(..)` tham chiếu đến `studentName` đứng *sau* khai báo `let studentName`, nhưng xét về mặt thời gian thì hàm `askQuestion()` lại được gọi thực thi *trước khi* câu lệnh `let` được chạm tới, trong khi `studentName` vẫn còn đang nằm trong TDZ của nó! Do đó lỗi xuất hiện.

Có một quan niệm sai lầm phổ biến cho rằng TDZ có nghĩa là `let` và `const` không được hoist. Đây là một khẳng định không chính xác, hoặc ít nhất là gây hiểu lầm một chút. Chúng chắc chắn có được hoist.

Sự khác biệt thực sự là các khai báo `let`/`const` không tự động khởi tạo ở đầu scope như cách `var` thực hiện. Vậy thì *tranh luận* đặt ra là liệu việc tự động khởi tạo có phải là *một phần của* hoisting hay không? Tôi cho rằng việc tự động đăng ký một biến ở đầu scope (tức là điều mà tôi gọi là "hoisting") và việc tự động khởi tạo ở đầu scope (thành `undefined`) là các thao tác tách biệt và không nên bị gộp chung dưới một thuật ngữ duy nhất là "hoisting."

Chúng ta đã thấy rằng `let` và `const` không tự động khởi tạo ở đầu scope. Nhưng hãy chứng minh rằng `let` và `const` *thực sự có* hoist (tự động đăng ký ở đầu scope), nhờ sự hỗ trợ từ người bạn che khuất biến hay shadowing (xem "Shadowing" ở Chương 3):

```js
var studentName = "Kyle";

{
    console.log(studentName);
    // ???

    // ..

    let studentName = "Suzy";

    console.log(studentName);
    // Suzy
}
```

Điều gì sẽ xảy ra với câu lệnh `console.log(..)` đầu tiên? Nếu `let studentName` không được hoist lên đầu scope, thì câu lệnh `console.log(..)` đầu tiên *lẽ ra phải* in ra `"Kyle"`, đúng không? Vào thời điểm đó, có vẻ như chỉ có `studentName` bên ngoài tồn tại, vì vậy đó là biến mà `console.log(..)` sẽ truy cập và in ra.

Nhưng thay vào đó, câu lệnh `console.log(..)` đầu tiên lại ném ra lỗi TDZ, bởi vì trên thực tế, `studentName` của scope bên trong **đã được** hoist (tự động đăng ký ở đầu scope). Điều **chưa** xảy ra (vào lúc đó!) là việc tự động khởi tạo cho `studentName` bên trong đó; nó vẫn chưa được khởi tạo tại thời điểm đó, do đó phạm phải quy tắc TDZ!

Vì vậy tóm lại, các lỗi TDZ xảy ra vì các khai báo `let`/`const` *thực sự có* hoist các khai báo của chúng lên đầu scope, nhưng không giống như `var`, chúng hoãn việc tự động khởi tạo các biến của chúng cho đến thời điểm trong trình tự mã lệnh nơi mà khai báo ban đầu xuất hiện. Khoảng thời gian này (gợi ý: temporal - thời gian), bất kể độ dài của nó là bao lâu, chính là TDZ.

Làm thế nào bạn có thể tránh các lỗi TDZ?

Lời khuyên của tôi: luôn đặt các khai báo `let` và `const` của bạn ở ngay đầu của bất kỳ scope nào. Thu nhỏ cửa sổ thời gian TDZ về độ dài bằng không (hoặc gần bằng không), và khi đó vấn đề sẽ không còn đáng lo nữa.

Nhưng tại sao TDZ lại tồn tại? Tại sao TC39 không quy định rằng `let`/`const` tự động khởi tạo như cách `var` làm? Hãy kiên nhẫn một chút, chúng ta sẽ quay trở lại để khám phá lý do *tại sao* có TDZ trong Phụ lục A.

## Cuối Cùng Cũng Được Khởi Tạo (Finally Initialized)

Làm việc với các biến có nhiều sắc thái tinh tế hơn nhiều so với những gì chúng ta thấy thoạt đầu. *Hoisting*, *khai báo (lại)*, và *TDZ* là những nguồn gốc gây bối rối phổ biến cho các nhà phát triển, đặc biệt là những người đã từng làm việc với các ngôn ngữ khác trước khi đến với JS. Trước khi tiếp tục, hãy đảm bảo mô hình tư duy của bạn đã hoàn toàn vững vàng về các khía cạnh này của biến và scope trong JS.

Hoisting thường được trích dẫn như một cơ chế tường minh của engine JS, nhưng nó thực sự giống một phép ẩn dụ hơn để mô tả những cách khác nhau mà JS xử lý các khai báo biến trong quá trình biên dịch. Nhưng ngay cả khi là một phép ẩn dụ, hoisting vẫn cung cấp cấu trúc hữu ích để tư duy về vòng đời của một biến—khi nào nó được tạo ra, khi nào nó sẵn sàng để sử dụng, khi nào nó biến mất.

Khai báo và khai báo lại các biến thường gây nhầm lẫn khi được coi là các thao tác lúc runtime. Nhưng nếu bạn chuyển hướng sang tư duy thời gian biên dịch (compile-time thinking) cho các thao tác này, những nét kỳ quặc và hiện tượng *che khuất* (shadows) sẽ giảm bớt.

Lỗi TDZ (vùng chết tạm thời - temporal dead zone) thật kỳ lạ và gây bực bội khi bắt gặp. May mắn thay, TDZ tương đối dễ tránh nếu bạn luôn cẩn thận đặt các khai báo `let`/`const` ở đầu bất kỳ scope nào.

Khi bạn đã điều hướng thành công qua những ngã rẽ quanh co này của biến và scope, chương tiếp theo sẽ chỉ ra các yếu tố định hướng cho những quyết định đặt khai báo của chúng ta vào các scope khác nhau, đặc biệt là các block lồng nhau.
