# You Don't Know JS Yet: Types & Grammar - Tái bản lần 2
# Chương 1: Các giá trị nguyên thủy (Primitive Values)

| GHI CHÚ: |
| :--- |
| Đang trong quá trình hoàn thiện |

Trong Chương 1 của cuốn "Objects & Classes" thuộc bộ sách này, chúng ta đã đối mặt với quan niệm sai lầm phổ biến rằng "mọi thứ trong JS đều là object". Giờ đây, chúng ta quay trở lại chủ đề đó và một lần nữa bác bỏ lầm tưởng này.

Tại đây, chúng ta sẽ xem xét các kiểu giá trị cốt lõi của JS, cụ thể là các kiểu không phải object được gọi là *nguyên thủy* (*primitives*).

## Kiểu giá trị (Value Types)

JS không áp dụng kiểu cho các biến hay thuộc tính -- điều mà tôi gọi là "kiểu đồ chứa" ("container types") -- mà thay vào đó, chính bản thân các giá trị mới có kiểu -- điều mà tôi gọi là "kiểu giá trị" ("value types").

Ngôn ngữ cung cấp bảy kiểu giá trị nguyên thủy (không phải object) được tích hợp sẵn: [^PrimitiveValues]

* `undefined`
* `null`
* `boolean`
* `number`
* `bigint`
* `symbol`
* `string`

Các kiểu giá trị này định nghĩa tập hợp gồm một hoặc nhiều giá trị cụ thể, mỗi kiểu đi kèm một tập các hành vi được chia sẻ chung cho tất cả các giá trị thuộc kiểu đó.

### Type-Of

Kiểu giá trị của bất kỳ giá trị nào đều có thể được kiểm tra thông qua toán tử `typeof`, toán tử này luôn trả về một giá trị `string` đại diện cho kiểu giá trị JS bên dưới:

```js
typeof true;            // "boolean"

typeof 42;              // "number"

typeof 42n;             // "bigint"

typeof Symbol("42");    // "symbol"
```

Khi được sử dụng với một biến thay vì một giá trị trực tiếp, toán tử `typeof` sẽ báo cáo kiểu giá trị của *giá trị nằm trong biến đó*:

```js
greeting = "Hello";
typeof greeting;        // "string"
```

Bản thân các biến trong JS không có kiểu. Chúng chứa bất kỳ giá trị tùy ý nào, và chính giá trị đó mới mang một kiểu giá trị.

### Không phải object? (Non-objects?)

Cụ thể điều gì làm cho 7 kiểu giá trị nguyên thủy này khác biệt so với các kiểu (và kiểu con) giá trị object? Tại sao chúng ta không coi tất cả chúng về bản chất đều là *object* ở bên dưới?

Hãy xem xét:

```js
myName = "Kyle";

myName.nickname = "getify";

console.log(myName.nickname);           // undefined
```

Đoạn mã này dường như thất bại trong im lặng khi cố gắng thêm một thuộc tính `nickname` vào một chuỗi nguyên thủy. Thoạt nhìn bề ngoài, điều đó có thể ngụ ý rằng các giá trị nguyên thủy thực sự chỉ là các object ở tầng dưới, như nhiều người đã (nhầm lẫn) khẳng định suốt nhiều năm qua.

| CẢNH BÁO: |
| :--- |
| Người ta có thể giải thích sự thất bại trong im lặng đó như một ví dụ về *auto-boxing* (đóng hộp tự động - xem phần "Automatic Objects" ở Chương 3), nơi giá trị nguyên thủy được ngầm chuyển đổi thành một object bọc (wrapper object) thể hiện của `String` trong khi cố gắng gán thuộc tính, và sau đó object nội bộ này bị vứt bỏ sau khi câu lệnh hoàn tất. Trên thực tế, tôi đã nói chính xác điều đó trong ấn bản đầu tiên của cuốn sách này. Nhưng tôi đã sai; ôi thôi! |

Có điều gì đó sâu xa hơn đang diễn ra, như chúng ta thấy trong phiên bản này của đoạn mã trước:

```js
"use strict";

myName = "Kyle";

myName.nickname = "getify";
// TypeError: Cannot create property 'nickname'
// on string 'Kyle'
```

Thật thú vị! Trong chế độ nghiêm ngặt (strict mode), JS thực thi một hạn chế cấm thiết lập một thuộc tính mới trên một giá trị nguyên thủy, giống như việc ngầm nâng cấp nó thành một object mới.

Ngược lại, ở chế độ không nghiêm ngặt (non-strict mode), JS cho phép hành vi vi phạm này trôi qua mà không báo lỗi gì. Vậy tại sao? Bởi vì strict mode được thêm vào ngôn ngữ trong ES5.1 (2011), sau hơn 15 năm tồn tại, và một sự thay đổi như vậy sẽ làm hỏng các chương trình hiện có nếu nó không được xác định là chỉ có hiệu lực khi có khai báo strict mode mới.

Vậy chúng ta có thể kết luận gì về sự khác biệt giữa các giá trị nguyên thủy và object? Các giá trị nguyên thủy là những giá trị *không được phép có thuộc tính*; chỉ có object mới được phép làm như vậy.

| MẸO: |
| :--- |
| Sự phân biệt cụ thể này dường như bị mâu thuẫn bởi các biểu thức như `"hello".length`; ngay cả trong strict mode, nó vẫn trả về giá trị mong đợi là `5`. Vì vậy, chắc chắn *có vẻ* như chuỗi có một thuộc tính `length`! Nhưng, như vừa đề cập ở trên, lời giải thích chính xác là *auto-boxing*; chúng ta sẽ đề cập đến chủ đề này trong phần "Automatic Objects" ở Chương 3. |

## Các giá trị rỗng (Empty Values)

Cả hai kiểu `null` và `undefined` thông thường đều đại diện cho sự trống rỗng hoặc vắng mặt của giá trị.

Thật không may, kiểu giá trị `null` lại cho ra kết quả `typeof` không như mong đợi. Thay vì `"null"`, chúng ta thấy:

```js
typeof null;            // "object"
```

Không, điều đó không có nghĩa là `null` bằng cách nào đó là một loại object đặc biệt. Đó chỉ là một tàn dư lịch sử từ những ngày đầu của JS, điều không thể thay đổi vì nó sẽ phá vỡ rất nhiều mã nguồn đang chạy trong thực tế.

Kiểu `undefined` được báo cáo cho cả các giá trị `undefined` rõ ràng và bất kỳ nơi nào bắt gặp một giá trị dường như bị thiếu:

```js
typeof undefined;               // "undefined"

var whatever;

typeof whatever;                // "undefined"
typeof nonExistent;             // "undefined"

whatever = {};
typeof whatever.missingProp;    // "undefined"

whatever = [];
typeof whatever[10];            // "undefined"
```

| GHI CHÚ: |
| :--- |
| Biểu thức `typeof nonExistent` đang tham chiếu đến một biến chưa được khai báo `nonExistent`. Thông thường, việc truy cập vào một tham chiếu biến chưa khai báo sẽ gây ra ngoại lệ, nhưng toán tử `typeof` được trao khả năng đặc biệt để truy cập an toàn ngay cả các định danh không tồn tại và bình thản trả về `"undefined"` thay vì ném ra ngoại lệ. |

Tuy nhiên, mỗi kiểu "rỗng" tương ứng đều có chính xác một giá trị mang cùng tên. Vì vậy, `null` là giá trị duy nhất trong kiểu giá trị `null`, và `undefined` là giá trị duy nhất trong kiểu giá trị `undefined`.

### Dạng Null (Null'ish)

Về mặt ngữ nghĩa, cả hai kiểu `null` và `undefined` đều đại diện cho sự trống rỗng nói chung, hoặc sự vắng mặt của một giá trị mang ý nghĩa khẳng định khác.

| GHI CHÚ: |
| :--- |
| Các thao tác trong JS có hành vi tương tự nhau dù gặp phải `null` hay `undefined` được gọi là "null'ish" (hoặc "nullish"). Tôi đoán từ "undefined'ish" nghe/nhìn có vẻ quá kỳ quặc! |

Đối với phần lớn mã JS, đặc biệt là mã do lập trình viên viết, hai giá trị *nullish* này có thể hoán đổi cho nhau; quyết định chủ ý sử dụng/gán `null` hay `undefined` trong bất kỳ tình huống cụ thể nào phụ thuộc vào ngữ cảnh và tùy thuộc vào lập trình viên.

JS cung cấp một số khả năng giúp xử lý hai giá trị nullish này như không thể phân biệt.

Ví dụ, toán tử `==` (so sánh bằng có ép kiểu - coercive-equality comparison) đối xử đặc biệt với `null` và `undefined` là bằng nhau qua ép kiểu với nhau, nhưng không bằng bất kỳ giá trị nào khác trong ngôn ngữ. Do đó, việc kiểm tra `.. == null` là an toàn để thực hiện nếu bạn muốn kiểm tra xem một giá trị cụ thể là `null` hay `undefined`:

```js
if (greeting == null) {
    // greeting là nullish/rỗng
}
```

Một bổ sung (gần đây) khác cho JS là toán tử `??` (nullish-coalescing):

```js
who = myName ?? "User";

// tương đương với:
who = (myName != null) ? myName : "User";
```

Như biểu thức ba ngôi tương đương minh họa, `??` kiểm tra xem `myName` có phải là non-nullish (không nullish) hay không, và nếu có, trả về giá trị của nó. Nếu không, nó trả về toán hạng còn lại (ở đây là `"User"`).

Cùng với `??`, JS cũng đã thêm toán tử `?.` (chuỗi điều kiện nullish - nullish conditional-chaining hay optional chaining):

```js
record = {
    shippingAddress: {
        street: "123 JS Lane",
        city: "Browserville",
        state: "XY"
    }
};

console.log( record?.shippingAddress?.street );
// 123 JS Lane

console.log( record?.billingAddress?.street );
// undefined
```

Toán tử `?.` kiểm tra giá trị ngay phía trước (bên trái), và nếu nó là nullish, toán tử sẽ dừng lại và trả về giá trị `undefined`. Nếu không, nó thực hiện truy cập thuộc tính `.` đối với giá trị đó và tiếp tục biểu thức.

Để làm rõ: `record?.` có nghĩa là, "kiểm tra `record` xem có phải nullish không trước khi truy cập thuộc tính `.`". Ngoài ra, `billingAddress?.` có nghĩa là, "kiểm tra `billingAddress` xem có phải nullish không trước khi truy cập thuộc tính `.`".

| CẢNH BÁO: |
| :--- |
| Một số nhà phát triển JS tin rằng toán tử `?.` mới hơn vượt trội hơn `.`, và do đó hầu như luôn nên được sử dụng thay cho `.`. Tôi tin rằng đó là một góc nhìn thiếu khôn ngoan. Trước hết, nó làm tăng thêm sự lộn xộn về mặt thị giác, điều chỉ nên làm nếu bạn nhận được lợi ích từ nó. Thứ hai, bạn nên nhận thức và lường trước tính rỗng của một giá trị nào đó để biện minh cho việc sử dụng `?.`. Nếu bạn luôn mong đợi một giá trị non-nullish hiện diện trong một biểu thức nào đó, việc sử dụng `?.` để truy cập thuộc tính trên đó không chỉ không cần thiết/lãng phí, mà còn có khả năng che giấu các lỗi trong tương lai khi giả định về sự hiện diện của giá trị của bạn bị sai nhưng `?.` lại bưng bít nó. Giống như hầu hết các tính năng trong JS, hãy sử dụng `.` ở nơi thích hợp nhất, và sử dụng `?.` ở nơi thích hợp nhất. Đừng bao giờ thay thế cái này khi cái kia phù hợp hơn. |

Ngoài ra còn có dạng toán tử `?.[` hơi kỳ lạ, chứ không phải `?[`, dành cho khi bạn cần sử dụng cách truy cập kiểu `[ .. ]` thay vì truy cập bằng `.`:

```js
record?.["shipping" + "Address"]?.state;    // XY
```

Một biến thể khác, được gọi là "gọi tùy chọn" ("optional-call"), là `?.(`, và được sử dụng khi gọi một hàm có điều kiện nếu giá trị đó là non-nullish:

```js
// thay vì:
//   if (someFunc) someFunc(42);
//
// hoặc:
//   someFunc && someFunc(42);

someFunc?.(42);
```

Toán tử `?.(` có vẻ như đang kiểm tra xem liệu `someFunc(..)` có phải là một hàm hợp lệ có thể gọi được hay không. Nhưng không phải vậy! Nó chỉ kiểm tra để đảm bảo giá trị đó là non-nullish trước khi cố gắng gọi nó. Nếu đó là một kiểu giá trị khác không phải nullish nhưng cũng không phải là hàm, nỗ lực thực thi vẫn sẽ thất bại với ngoại lệ `TypeError`.

| CẢNH BÁO: |
| :--- |
| Vì cạm bẫy đó, tôi *cực kỳ ghét* dạng toán tử này và cảnh báo mọi người không bao giờ nên sử dụng nó. Tôi nghĩ đó là một tính năng được quan niệm nghèo nàn, gây hại nhiều hơn lợi (cho chính JS và cho các chương trình). Có rất ít tính năng JS mà tôi đi xa đến mức nói rằng "đừng bao giờ sử dụng nó." Nhưng theo ý kiến của tôi, đây là một trong những *phần tồi tệ* (bad parts) thực sự của ngôn ngữ. |

### Rõ ràng là phân biệt (Distinct'ish)

Điều quan trọng cần ghi nhớ là `null` và `undefined` thực sự *là* các kiểu riêng biệt, và do đó `null` có thể khác biệt rõ rệt so với `undefined`. Bạn có thể xây dựng các chương trình một cách cẩn thận để hầu như coi chúng là không thể phân biệt. Nhưng điều đó đòi hỏi sự cẩn trọng và kỷ luật của lập trình viên. Từ góc độ của JS, chúng thường phân biệt nhiều hơn.

Có những trường hợp `null` và `undefined` sẽ kích hoạt hành vi khác nhau từ ngôn ngữ, điều quan trọng cần ghi nhớ. Chúng ta sẽ không đề cập thấu đáo tất cả các trường hợp ở đây, nhưng đây là một ví dụ:

```js
function greet(msg = "Hello") {
    console.log(msg);
}

greet();            // Hello
greet(undefined);   // Hello
greet("Hi");        // Hi

greet(null);        // null
```

Mệnh đề `= ..` trên một tham số được gọi là "giá trị mặc định của tham số" ("parameter default"). Nó chỉ kích hoạt và gán giá trị mặc định cho tham số nếu đối số ở vị trí đó bị thiếu, hoặc chính xác là giá trị `undefined`. Nếu bạn truyền `null`, mệnh đề đó không kích hoạt, và do đó `null` được gán cho tham số.

Không có cách nào là *đúng* hay *sai* khi sử dụng `null` hay `undefined` trong một chương trình. Vì vậy, bài học rút ra là: hãy cẩn thận khi chọn giá trị này hay giá trị kia. Và nếu bạn đang sử dụng chúng thay thế cho nhau, hãy cẩn thận hơn nữa.

## Các giá trị Boolean (Boolean Values)

Kiểu `boolean` chứa hai giá trị: `false` và `true`.

Trong "thời xưa", các ngôn ngữ lập trình theo quy ước sẽ sử dụng `0` để biểu thị `false` và `1` để biểu thị `true`. Vì vậy, bạn có thể coi kiểu `boolean`, cùng với các từ khóa `false` và `true`, như một cú pháp tiện lợi (semantic convenience sugar) phủ lên trên các giá trị `0` và `1`:

```js
// isLoggedIn = 1;
isLoggedIn = true;

isComplete = 0;
// isComplete = false;
```

Các giá trị Boolean là cách mọi việc đưa ra quyết định diễn ra trong một chương trình JS:

```js
if (isLoggedIn) {
    // làm gì đó
}

while (!isComplete) {
    // tiếp tục chạy
}
```

Toán tử `!` phủ định/đảo ngược một giá trị boolean sang giá trị kia: `false` trở thành `true`, và `true` trở thành `false`.

## Các giá trị Chuỗi (String Values)

Kiểu `string` chứa bất kỳ giá trị nào là một tập hợp gồm một hoặc nhiều ký tự, được phân tách (bao quanh hai bên) bởi các ký tự dấu ngoặc kép hoặc ngoặc đơn:

```js
myName = "Kyle";
```

JS không phân biệt một ký tự đơn thành một kiểu khác biệt như một số ngôn ngữ làm; `"a"` là một chuỗi giống hệt như `"abc"`.

Các chuỗi có thể được phân tách bằng dấu ngoặc kép (`"`), ngoặc đơn (`'`), hoặc dấu backtick (`` ` ``). Dấu phân tách kết thúc phải luôn khớp với dấu phân tách bắt đầu.

Chuỗi có một độ dài nội tại tương ứng với số lượng code-point -- thực ra là code-unit, sẽ nói thêm về điều này ngay sau đây -- mà chúng chứa.

```js
myName = "Kyle";

myName.length;      // 4
```

Điều này không nhất thiết tương ứng với số lượng ký tự nhìn thấy được hiện diện giữa dấu phân tách bắt đầu và kết thúc (hay còn gọi là string literal - chuỗi ký tự nguyên văn). Đôi khi có thể hơi bối rối để phân biệt rạch ròi giữa một string literal và giá trị chuỗi bên dưới, vì vậy hãy chú ý kỹ.

| GHI CHÚ: |
| :--- |
| Chúng ta sẽ đề cập chi tiết đến việc tính toán độ dài chuỗi trong Chương 2. |

### Mã hóa ký tự trong JS (JS Character Encodings)

JS sử dụng kiểu mã hóa ký tự nào cho các ký tự trong chuỗi?

Có lẽ bạn đã từng nghe nói về "Unicode" và thậm chí có thể là "UTF-8" (8-bit) hoặc "UTF-16" (16-bit). Nếu bạn giống tôi (trước khi thực hiện nghiên cứu để viết cuốn sách này), bạn có thể chỉ xua tay và quyết định rằng đó là tất cả những gì bạn cần biết về mã hóa ký tự trong chuỗi JS.

Nhưng... không phải vậy. Thậm chí còn cách xa lắm.

Hóa ra, bạn cần hiểu nhiều khía cạnh khác nhau của cách thức Unicode hoạt động, và thậm chí phải xem xét các khái niệm từ UCS-2 (Tập ký tự phổ quát 2 byte - 2-byte Universal Character Set), vốn tương tự như UTF-16, nhưng không hoàn toàn giống nhau. [^UTFUCS]

Unicode định nghĩa tất cả các "ký tự" mà chúng ta có thể biểu diễn phổ quát trong các chương trình máy tính, bằng cách gán một con số cụ thể cho mỗi ký tự, được gọi là các code-point (điểm mã). Các con số này dao động từ `0` cho đến tối đa là `1114111` (`10FFFF` trong hệ thập lục phân).

Ký hiệu tiêu chuẩn cho các ký tự Unicode là `U+` theo sau bởi 4-6 ký tự thập lục phân. Ví dụ, `❤` (biểu tượng trái tim) có code-point là `10084` (`2764` trong hệ thập lục phân), và do đó được ký hiệu là `U+2764`.

Nhóm 65.535 code point đầu tiên trong Unicode được gọi là BMP (Basic Multilingual Plane - Mặt phẳng đa ngôn ngữ cơ bản). Tất cả các ký tự này đều có thể được biểu diễn bằng 16 bit (2 byte). Khi biểu diễn các ký tự Unicode từ BMP, điều đó khá đơn giản, vì chúng có thể *vừa vặn* gọn gàng vào các ký tự UTF-16 đơn lẻ trong JS.

Tất cả các code point còn lại được nhóm thành 16 mặt phẳng được gọi là "mặt phẳng bổ sung" ("supplemental planes") hoặc "mặt phẳng thiên thể" ("astral planes"). Các code-point này yêu cầu nhiều hơn 16 bit để biểu diễn -- chính xác là 21 bit -- vì vậy khi biểu diễn các ký tự mở rộng/bổ sung phía trên BMP, JS thực sự lưu trữ các code-point này dưới dạng một cặp gồm hai code unit 16-bit liền kề nhau, được gọi là *các nửa đại diện* (*surrogate halves*, hoặc *cặp đại diện* - *surrogate pairs*).

Ví dụ, code point Unicode `127878` (thập lục phân `1F386`) là `🎆` (biểu tượng pháo hoa). JS lưu trữ ký tự này trong một giá trị chuỗi dưới dạng hai code unit nửa đại diện: `U+D83C` và `U+DF86`. Hãy nhớ rằng hai phần này của toàn bộ ký tự *không* đứng độc lập; chúng chỉ hợp lệ/có ý nghĩa khi được ghép đôi ngay cạnh nhau.

Điều này có ảnh hưởng đến độ dài của chuỗi, bởi vì một ký tự hiển thị đơn lẻ như biểu tượng pháo hoa `🎆`, khi ở trong một chuỗi JS, sẽ được tính là 2 ký tự cho mục đích độ dài chuỗi!

Chúng ta sẽ xem lại các ký tự Unicode sau một lát nữa, và sau đó tìm hiểu những thách thức trong việc tính toán độ dài chuỗi trong Chương 2.

### Các chuỗi thoát (Escape Sequences)

Nếu `"` hoặc `'` được sử dụng để phân tách một string literal, nội dung sẽ chỉ được phân tích cú pháp cho *các chuỗi thoát ký tự* (*character-escape sequences*): `\` theo sau bởi một hoặc nhiều ký tự mà JS nhận diện và phân tích cú pháp với ý nghĩa đặc biệt. Bất kỳ ký tự nào khác trong chuỗi không được phân tích cú pháp thành chuỗi thoát (đơn ký tự hoặc đa ký tự), đều được chèn nguyên trạng vào giá trị chuỗi.

Đối với các chuỗi thoát đơn ký tự, các ký tự sau được nhận diện sau một dấu `\`: `b`, `f`, `n`, `r`, `t`, `v`, `0`, `'`, `"`, và `\`. Ví dụ, `\n` có nghĩa là dòng mới (new-line), `\t` có nghĩa là tab, v.v.

Nếu một dấu `\` được theo sau bởi bất kỳ ký tự nào khác (ngoại trừ `x` và `u` -- được giải thích bên dưới), ví dụ như `\k`, chuỗi đó được hiểu là dấu `\` là một ký tự thoát không cần thiết, do đó nó bị loại bỏ, chỉ để lại chính ký tự nguyên văn đó (`k`).

Để đưa một dấu `"` vào giữa một string literal được phân tách bằng `"`, hãy sử dụng chuỗi thoát `\"`. Tương tự, nếu bạn đưa một ký tự `'` vào giữa một string literal được phân tách bằng `'`, hãy sử dụng chuỗi thoát `\'`. Ngược lại, một dấu `'` *không* cần phải thoát bên trong một chuỗi được phân tách bằng `"`, và ngược lại.

```js
myTitle = "Kyle Simpson (aka, \"getify\"), former O'Reilly author";

console.log(myTitle);
// Kyle Simpson (aka, "getify"), former O'Reilly author
```

Trong văn bản, dấu gạch chéo xuôi `/` là phổ biến nhất. Nhưng đôi khi, bạn cần một dấu gạch chéo ngược `\`. Để đưa vào một ký tự gạch chéo ngược `\` nguyên văn mà không để nó hoạt động như điểm bắt đầu của một chuỗi thoát ký tự, hãy sử dụng `\\` (hai dấu gạch chéo ngược).

Vậy thì... `\\\` (ba dấu gạch chéo ngược) trong một chuỗi sẽ được phân tích cú pháp thành gì? Hai dấu `\` đầu tiên sẽ là một chuỗi thoát `\\`, qua đó chèn một ký tự `\` đơn lẻ vào giá trị chuỗi, và dấu `\` còn lại sẽ chỉ đóng vai trò thoát cho bất kỳ ký tự nào đứng ngay sau nó.

Một nơi mà dấu gạch chéo ngược thường xuất hiện là trong đường dẫn tệp của Windows, vốn sử dụng dấu phân cách `\` thay vì dấu phân cách `/` được sử dụng trong các đường dẫn kiểu linux/unix:

```js
windowsFontsPath =
    "C:\\Windows\\Fonts\\";

console.log(windowsFontsPath);
// C:\Windows\Fonts\"
```

| MẸO: |
| :--- |
| Còn bốn dấu gạch chéo ngược `\\\\` trong một string literal thì sao? À, đó chỉ là hai chuỗi thoát `\\` đứng cạnh nhau, do đó nó tạo ra hai dấu gạch chéo ngược liền kề (`\\`) trong giá trị chuỗi bên dưới. Bạn có thể nhận ra có một quy luật chẵn/lẻ đang diễn ra. Do đó, bạn sẽ có thể giải mã bất kỳ số lượng dấu gạch chéo ngược lẻ (`\\\\\`, `\\\\\\\\\`, v.v.) hoặc chẵn (`\\\\\\`, `\\\\\\\\\\`, v.v.) nào trong một string literal. |

#### Tiếp tục dòng (Line Continuation)

Ký tự `\` được theo sau bởi một ký tự dòng mới thực tế (không phải chỉ là ký tự nguyên văn `n`) là một trường hợp đặc biệt, và nó tạo ra cái được gọi là tiếp tục dòng (line-continuation):

```js
greeting = "Hello \
Friends!";

console.log(greeting);
// Hello Friends!
```

Như bạn có thể thấy, dòng mới ở cuối dòng `greeting = ` ngay trước đó có một dấu `\`, cho phép string literal này tiếp tục sang dòng tiếp theo. Nếu không có dấu thoát `\` trước nó, một dòng mới -- dòng mới thực tế, chứ không phải chuỗi thoát ký tự `\n` -- xuất hiện trong một string literal được phân tách bằng `"` hoặc `'` thực sự sẽ tạo ra lỗi cú pháp phân tích của JS.

Bởi vì dấu `\` ở cuối dòng biến ký tự dòng mới thành một sự tiếp tục dòng, ký tự dòng mới bị bỏ qua khỏi chuỗi, như được hiển thị bởi đầu ra `console.log(..)`.

| GHI CHÚ: |
| :--- |
| Tính năng tiếp tục dòng này thường được gọi là "chuỗi nhiều dòng" ("multi-line strings"), nhưng tôi nghĩ đó là một nhãn gây hiểu nhầm. Như bạn có thể thấy, bản thân giá trị chuỗi không có nhiều dòng, nó chỉ được định nghĩa trên nhiều dòng thông qua các phép nối dòng. Một chuỗi nhiều dòng thực sự sẽ có nhiều dòng trong giá trị bên dưới. Chúng ta sẽ xem lại chủ đề này sau trong chương này khi tìm hiểu về Template Literals. |

### Các chuỗi thoát đa ký tự (Multi-Character Escapes)

Các chuỗi thoát đa ký tự có thể là các chuỗi thập lục phân hoặc chuỗi Unicode.

Các chuỗi thoát thập lục phân (hexadecimal escape sequences) được sử dụng để mã hóa bất kỳ ký tự ASCII cơ bản nào (mã 0-255), và có dạng `\x` theo sau bởi chính xác hai ký tự thập lục phân (`0-9` và `a-f` / `A-F` -- không phân biệt chữ hoa chữ thường). Ví dụ, `A9` hoặc `a9` là giá trị thập phân `169`, tương ứng với:

```js
copyright = "\xA9";  // hoặc "\xa9"

console.log(copyright);     // ©
```

Đối với bất kỳ ký tự bình thường nào có thể gõ trên bàn phím, chẳng hạn như `"a"`, việc chỉ định ký tự nguyên văn thường dễ đọc nhất, trái ngược với cách biểu diễn thập lục phân khó hiểu hơn:

```js
"a" === "\x61";             // true
```

#### Unicode trong chuỗi

Chỉ riêng các chuỗi thoát Unicode đã có thể mã hóa bất kỳ ký tự nào từ BMP của Unicode. Chúng có dạng `\u` theo sau bởi chính xác bốn ký tự thập lục phân.

Ví dụ, chuỗi thoát `\u00A9` (hoặc `\u00a9`) tương ứng với cùng ký hiệu `©` đó, trong khi `\u263A` (hoặc `\u263a`) tương ứng với ký tự Unicode có code-point `9786`: `☺` (biểu tượng mặt cười).

Khi bất kỳ chuỗi thoát ký tự nào (bất kể độ dài) được nhận diện, ký tự đơn lẻ mà nó đại diện sẽ được chèn vào chuỗi, thay vì các ký tự riêng biệt ban đầu. Vì vậy, trong chuỗi `"\u263A"`, chỉ có một ký tự (mặt cười), không phải sáu ký tự riêng lẻ.

Nhưng như đã giải thích trước đó, nhiều code-point Unicode cao hơn nhiều so với `65535`. Ví dụ, `1F4A9` (hoặc `1f4a9`) là code-point thập phân `128169`, tương ứng với biểu tượng hài hước `💩` (đống phân).

Nhưng `\u1F4A9` sẽ không hoạt động để đưa ký tự này vào một chuỗi, vì nó sẽ được phân tích cú pháp thành chuỗi thoát Unicode `\u1F4A`, theo sau là một ký tự nguyên văn `9`. Để giải quyết hạn chế này, một biến thể của chuỗi thoát Unicode đã được giới thiệu cho phép một số lượng ký tự thập lục phân tùy ý sau `\u`, bằng cách bao quanh chúng bằng các dấu ngoặc nhọn `{ .. }`:

```js
myReaction = "\u{1F4A9}";

console.log(myReaction);
// 💩
```

Bạn còn nhớ cuộc thảo luận trước đó về các ký tự Unicode mở rộng (ngoài BMP) và *các nửa đại diện* (*surrogate halves*) không? Cùng một ký tự `💩` này cũng có thể được định nghĩa bằng hai code unit rõ ràng tạo thành một cặp đại diện:

```js
myReaction = "\uD83D\uDCA9";

console.log(myReaction);
// 💩
```

Cả ba cách biểu diễn của cùng một ký tự này đều được JS lưu trữ nội bộ giống hệt nhau và không thể phân biệt:

```js
"💩" === "\u{1F4A9}";                // true
"\u{1F4A9}" === "\uD83D\uDCA9";     // true
```

Mặc dù JS không quan tâm ký tự đó được biểu diễn theo cách nào trong chương trình của bạn, hãy cân nhắc cẩn thận sự khác biệt về khả năng đọc khi viết mã của bạn.

| GHI CHÚ: |
| :--- |
| Mặc dù `💩` trông giống như một ký tự đơn lẻ, cách biểu diễn nội bộ của nó ảnh hưởng đến những thứ như việc tính toán độ dài của một chuỗi có chứa ký tự đó. Chúng ta sẽ đề cập đến việc tính toán độ dài chuỗi trong Chương 2. |

##### Chuẩn hóa Unicode (Unicode Normalization)

Một khúc mắc khác trong việc xử lý chuỗi Unicode là ngay cả một số ký tự BMP đơn lẻ nhất định cũng có thể được biểu diễn theo những cách khác nhau.

Ví dụ, ký tự `"é"` có thể được biểu diễn dưới dạng chính nó (code-point `233`, tức `\xe9` hoặc `\u00e9` hoặc `\u{e9}`), hoặc dưới dạng kết hợp của hai code-point: ký tự `"e"` (code-point `101`, tức `\x65`, `\u0065`, `\u{65}`) và *dấu ngã/sắc kết hợp* (*combining tilde/accent* - code-point `769`, tức `\u0301`, `\u{301}`).

Xem xét:

```js
eTilde1 = "é";
eTilde2 = "\u00e9";
eTilde3 = "\u0065\u0301";

console.log(eTilde1);       // é
console.log(eTilde2);       // é
console.log(eTilde3);       // é
```

String literal được gán cho `eTilde3` trong đoạn mã này lưu trữ dấu thanh dưới dạng một ký hiệu *dấu kết hợp* (*combining mark*) riêng biệt. Giống như các cặp đại diện, một dấu kết hợp chỉ có ý nghĩa khi kết nối với biểu tượng mà nó đứng cạnh (thường là đứng sau).

Việc hiển thị biểu tượng Unicode sẽ giống nhau bất kể thế nào, nhưng cách ký tự `"é"` được lưu trữ nội bộ sẽ ảnh hưởng đến những thứ như tính toán `length` của chuỗi chứa nó, cũng như phép so sánh bằng và so sánh quan hệ (sẽ nói thêm về những điều này trong Chương 2):

```js
eTilde1.length;             // 2
eTilde2.length;             // 1
eTilde3.length;             // 2

eTilde1 === eTilde2;        // false
eTilde1 === eTilde3;        // true
```

Một thách thức cụ thể là bạn có thể sao chép-dán một chuỗi có ký tự `"é"` nhìn thấy trong đó, và ký tự bạn đã sao chép có thể ở dạng *kết hợp sẵn* (*composed*) hoặc dạng *tách rời* (*decomposed*). Nhưng không có cách nào trực quan để nhận biết, tuy nhiên giá trị chuỗi bên dưới trong literal sẽ khác nhau:

```js
"é" === "é";           // false!!
```

Sự khác biệt về biểu diễn nội bộ này có thể khá phức tạp nếu không được lên kế hoạch cẩn thận. May mắn thay, JS cung cấp phương thức tiện ích `normalize(..)` trên các chuỗi để hỗ trợ:

```js
eTilde1 = "é";
eTilde2 = "\u{e9}";
eTilde3 = "\u{65}\u{301}";

eTilde1.normalize("NFC") === eTilde2;
eTilde2.normalize("NFD") === eTilde3;
```

Chế độ chuẩn hóa `"NFC"` kết hợp các code-point liền kề thành code-point *kết hợp sẵn* (*composed*) (nếu có thể), trong khi chế độ chuẩn hóa `"NFD"` tách một code-point đơn lẻ thành các code-point *tách rời* (*decomposed*) của nó (nếu có thể).

Và thực sự có thể có nhiều hơn hai code-point *tách rời* riêng lẻ tạo nên một code-point *kết hợp sẵn* duy nhất -- ví dụ, một ký tự đơn lẻ có thể có nhiều dấu phụ được áp dụng cho nó.

Khi xử lý các chuỗi Unicode sẽ được so sánh, sắp xếp hoặc phân tích độ dài, điều rất quan trọng là phải ghi nhớ sự chuẩn hóa Unicode và sử dụng nó khi cần thiết.

##### Cụm Grapheme trong Unicode (Unicode Grapheme Clusters)

Một sự phức tạp cuối cùng của việc xử lý chuỗi Unicode là sự hỗ trợ cho việc gom cụm nhiều code-point liền kề thành một biểu tượng trực quan riêng biệt, được gọi là một *grapheme* (hoặc một *cụm grapheme* - *grapheme cluster*).

Một ví dụ là một emoji gia đình chẳng hạn như `"👩‍👩‍👦‍👦"`, thực chất được tạo thành từ 7 code-point gom cụm/nhóm lại với nhau thành một biểu tượng trực quan duy nhất.

Hãy xem xét:

```js
familyEmoji = "\u{1f469}\u{200d}\u{1f469}\u{200d}\u{1f466}\u{200d}\u{1f466}";

familyEmoji;            // 👩‍👩‍👦‍👦
```

Emoji này *không* phải là một code-point Unicode đơn lẻ đã đăng ký, và do đó, không có phép *chuẩn hóa* nào có thể được thực hiện để kết hợp 7 code-point riêng biệt này thành một thực thể duy nhất. Logic dựng hình ảnh cho các biểu tượng tổng hợp như vậy khá phức tạp, vượt xa những gì hầu hết các lập trình viên JS muốn nhúng vào chương trình của mình. Các thư viện có tồn tại để xử lý một số logic này, nhưng chúng thường lớn và vẫn không nhất thiết bao quát hết tất cả các sắc thái/biến thể.

Không giống như các cặp đại diện và dấu kết hợp, các biểu tượng trong các cụm grapheme trên thực tế có thể đóng vai trò như các ký tự độc lập, nhưng có hành vi kết hợp đặc biệt khi được đặt cạnh nhau.

Loại phức tạp này ảnh hưởng đáng kể đến việc tính toán độ dài, so sánh, sắp xếp và nhiều thao tác theo định hướng chuỗi thông thường khác.

### Template Literals

Trước đó tôi đã đề cập rằng các chuỗi có thể được phân tách luân phiên bằng dấu backtick `` `..` ``:

```js
myName = `Kyle`;
```

Tất cả các quy tắc tương tự về mã hóa ký tự, chuỗi thoát ký tự và độ dài đều áp dụng cho các loại chuỗi này.

Tuy nhiên, nội dung của các template (string) literal này còn được phân tích cú pháp bổ sung cho một chuỗi phân tách đặc biệt `${ .. }`, đánh dấu một biểu thức cần được đánh giá và nội suy (interpolate) vào giá trị chuỗi tại vị trí đó:

```js
myName = `Kyle`;

greeting = `Hello, ${myName}!`;

console.log(greeting);      // Hello, Kyle!
```

Mọi thứ nằm giữa `{ .. }` trong một template literal như vậy đều là một biểu thức JS tùy ý. Nó có thể là các biến đơn giản như `myName`, hoặc các chương trình JS phức tạp, hoặc bất cứ thứ gì ở giữa (thậm chí là một biểu thức template literal khác!).

| MẸO: |
| :--- |
| Tính năng này thường được gọi là "template literals" hoặc "template strings", nhưng tôi nghĩ điều đó gây nhầm lẫn. "Template" (khuôn mẫu) thường có nghĩa, trong các ngữ cảnh lập trình, là một tập hợp văn bản có thể tái sử dụng và có thể được đánh giá lại với dữ liệu khác nhau. Ví dụ: *template engines* cho các trang web, mẫu email cho các chiến dịch bản tin, v.v. Tính năng JS này không thể tái sử dụng. Nó là một literal, và nó tạo ra một giá trị duy nhất, tức thì (thường là một chuỗi). Bạn có thể đặt một giá trị như vậy trong một hàm và gọi hàm đó nhiều lần. Nhưng khi đó hàm đang đóng vai trò là khuôn mẫu, chứ không phải bản thân literal. Thay vào đó, tôi thích gọi tính năng này là *interpolated literals* (các literal được nội suy), hoặc từ viết tắt hài hước: *interpoliterals*. Tôi chỉ nghĩ rằng cái tên đó mô tả chính xác hơn. |

Template literals cũng có một hành vi khác thú vị liên quan đến các dòng mới, so với các chuỗi được phân tách bằng `"` hoặc `'` cổ điển. Hãy nhớ rằng đối với những chuỗi đó, một sự tiếp tục dòng yêu cầu một dấu `\` ở cuối mỗi dòng, ngay trước một dòng mới. Không phải vậy với template literals!

```js
myPoem = `
Roses are red
Violets are blue
C3PO's a funny robot
and so R2.`;

console.log(myPoem);
//
// Roses are red
// Violets are blue
// C3PO's a funny robot
// and so R2.
```

Việc tiếp tục dòng với template literals *không yêu cầu* ký tự thoát. Tuy nhiên, điều đó có nghĩa là dòng mới là một phần của chuỗi, ngay cả dòng mới đầu tiên ở trên. Nói cách khác, `myPoem` ở trên chứa một *chuỗi nhiều dòng* thực sự, như đã hiển thị. Tuy nhiên, nếu bạn dùng dấu thoát `\` ở cuối bất kỳ dòng nào trong một template literal, dòng mới đó sẽ bị bỏ qua, giống hệt như với các chuỗi non-template literal.

Template literals thường dẫn đến một giá trị chuỗi, nhưng không phải luôn luôn. Một dạng template literal có vẻ hơi kỳ lạ được gọi là *tagged template literal*:

```js
price = formatCurrency`The cost is: ${totalCost}`;
```

Ở đây, `formatCurrency` là một thẻ (tag) được áp dụng cho giá trị template literal, thực chất là gọi `formatCurrency(..)` như một hàm, truyền cho nó các chuỗi literal và các biểu thức được nội suy được phân tích từ giá trị đó. Sau đó, hàm này có thể lắp ráp chúng theo bất kỳ cách nào mà nó thấy phù hợp -- chẳng hạn như định dạng một giá trị `number` thành tiền tệ theo bản địa (locale) hiện tại -- và trả về bất kỳ giá trị nào, chuỗi hoặc giá trị khác, mà nó muốn.

Vì vậy, các tagged template literal không phải lúc nào cũng là chuỗi; chúng có thể là bất kỳ giá trị nào. Nhưng các untagged template literal (không có thẻ) *sẽ luôn là* chuỗi.

Một số lập trình viên JS tin rằng các chuỗi untagged template literal là tốt nhất để sử dụng cho *tất cả* các chuỗi, ngay cả khi không sử dụng bất kỳ phép nội suy biểu thức hay nhiều dòng nào. Tôi không đồng ý. Tôi nghĩ chúng chỉ nên được sử dụng khi có nội suy (hoặc có nhiều dòng).

| MẸO: |
| :--- |
| Nguyên tắc tôi luôn áp dụng khi đưa ra những quyết định như vậy: hãy sử dụng tính năng/công cụ phù hợp gần nhất và ít khả năng nhất cho bất kỳ tác vụ nào. |

Hơn nữa, có một vài nơi mà các chuỗi kiểu `` `..` `` không được phép. Ví dụ, pragma `"use strict"` không thể sử dụng backtick, nếu không pragma sẽ bị bỏ qua trong im lặng (và do đó chương trình vô tình chạy ở chế độ non-strict). Ngoài ra, kiểu chuỗi này không thể được sử dụng trong tên thuộc tính đặt trong dấu ngoặc kép của object literal, các mẫu destructuring, hoặc trong mệnh đề xác định module `import .. from ..` của ES Module.

Ý kiến của tôi: hãy sử dụng chuỗi phân tách bằng `` `..` `` ở nơi được phép, nhưng chỉ khi cần nội suy/nhiều dòng; và tiếp tục sử dụng chuỗi phân tách bằng `".."` hoặc `'..'` cho mọi trường hợp khác.

## Các giá trị Số (Number Values)

Kiểu `number` chứa bất kỳ giá trị số nào (số nguyên hoặc số thập phân), chẳng hạn như `-42` hoặc `3.1415926`. Các giá trị này được công cụ JS biểu diễn dưới dạng các giá trị số thực dấu phẩy động nhị phân độ chính xác kép 64-bit IEEE-754. [^IEEE754]

Các `number` trong JS luôn là số thập phân; các số nguyên (còn gọi là "integers") không được lưu trữ theo cách khác biệt/đặc biệt. Một "số nguyên" được lưu trữ dưới dạng một giá trị `number` chỉ đơn thuần là không có phần khác 0 ở phần phân số; do đó `42` trong JS không thể phân biệt được với `42.0` và `42.000000`.

Chúng ta có thể sử dụng `Number.isInteger(..)` để xác định xem một giá trị `number` có phần phân số khác không hay không:

```js
Number.isInteger(42);           // true
Number.isInteger(42.0);         // true
Number.isInteger(42.000000);    // true

Number.isInteger(42.0000001);   // false
```

### Phân tích cú pháp so với Ép kiểu (Parsing vs Coercion)

Nếu một giá trị chuỗi chứa nội dung trông giống số, bạn có thể cần chuyển đổi từ giá trị chuỗi đó sang một `number`, cho các mục đích thao tác toán học.

Tuy nhiên, điều rất quan trọng là phải phân biệt giữa chuyển đổi dạng phân tích cú pháp (parsing-conversion) và chuyển đổi dạng ép kiểu (coercive-conversion).

Chúng ta có thể chuyển đổi bằng phân tích cú pháp với các tiện ích tích hợp sẵn của JS là `parseInt(..)` hoặc `parseFloat(..)`:

```js
someNumericText = "123.456";

parseInt(someNumericText,10);               // 123
parseFloat(someNumericText);                // 123.456

parseInt("42",10) === parseFloat("42");     // true

parseInt("512px");                          // 512
```

| GHI CHÚ: |
| :--- |
| Phân tích cú pháp chỉ có ý nghĩa đối với các giá trị chuỗi, vì nó là một thao tác từng ký tự một (từ trái qua phải). Không có ý nghĩa gì khi phân tích nội dung của một `boolean`, cũng như không có ý nghĩa gì khi phân tích nội dung của một `number` hoặc một `null`; chẳng có gì để phân tích cả. Nếu bạn truyền bất cứ thứ gì khác ngoài giá trị chuỗi vào `parseInt(..)` / `parseFloat(..)`, các tiện ích đó trước tiên sẽ chuyển đổi giá trị đó thành một chuỗi rồi mới cố gắng phân tích nó. Điều đó hầu như chắc chắn có vấn đề (dẫn đến lỗi) hoặc lãng phí -- `parseInt(42)` là ngớ ngẩn, và `parseInt(42.3)` là một sự lạm dụng `parseInt(..)` để làm công việc của `Math.floor(..)`. |

Phân tích cú pháp sẽ trích xuất các ký tự trông giống số từ giá trị chuỗi và đưa chúng vào một giá trị `number`, dừng lại khi gặp phải một ký tự không phải số (ví dụ: không phải `-`, `.` hoặc `0`-`9`). Nếu phân tích cú pháp thất bại ngay từ ký tự đầu tiên, cả hai tiện ích đều trả về giá trị đặc biệt `NaN` (xem "Invalid Number" bên dưới), cho biết thao tác không hợp lệ và đã thất bại.

Khi `parseInt(..)` gặp dấu `.` trong `"123.456"`, nó dừng lại, chỉ sử dụng `123` trong giá trị `number` kết quả. Ngược lại, `parseFloat(..)` chấp nhận ký tự `.` này và tiếp tục phân tích cú pháp số thực với bất kỳ chữ số thập phân nào sau dấu `.`.

Tiện ích `parseInt(..)` nói riêng nhận một đối số thứ hai tùy chọn -- nhưng *trên thực tế* lại khá cần thiết -- là `radix`: cơ số để giả định cho việc diễn giải các ký tự chuỗi thành `number` (phạm vi từ `2` - `36`). `10` dành cho các số hệ cơ số 10 tiêu chuẩn, `2` dành cho hệ nhị phân, `8` dành cho hệ bát phân và `16` dành cho hệ thập lục phân. Bất kỳ `radix` bất thường nào khác, như `23`, giả định các chữ số theo thứ tự, `0` - `9` theo sau bởi thứ tự các ký tự `a` - `z` (không phân biệt chữ hoa chữ thường). Nếu radix được chỉ định nằm ngoài phạm vi `2` - `36`, `parseInt(..)` sẽ thất bại vì không hợp lệ và trả về giá trị `NaN`.

Nếu `radix` bị bỏ qua, hành vi của `parseInt(..)` khá tinh vi và khó hiểu, ở chỗ nó cố gắng đoán cơ số dựa trên những gì nó thấy ở ký tự đầu tiên. Về mặt lịch sử, điều này đã dẫn đến rất nhiều lỗi tiềm ẩn, vì vậy đừng bao giờ dựa vào việc tự động đoán mặc định; luôn chỉ định một radix rõ ràng (như `10` trong các lệnh gọi ở trên).

`parseFloat(..)` luôn phân tích cú pháp với cơ số `10`, vì vậy không chấp nhận đối số thứ hai.

| CẢNH BÁO: |
| :--- |
| Một điểm khác biệt đáng ngạc nhiên giữa `parseInt(..)` và `parseFloat(..)` là `parseInt(..)` sẽ không phân tích cú pháp đầy đủ ký hiệu khoa học (ví dụ: `"1.23e+5"`), thay vào đó dừng lại ở dấu `.` vì nó không hợp lệ đối với số nguyên; trên thực tế, ngay cả `"1e+5"` cũng dừng lại ở chữ `"e"`. Mặt khác, `parseFloat(..)` phân tích cú pháp đầy đủ ký hiệu khoa học như mong đợi. |

Trái ngược với chuyển đổi bằng phân tích cú pháp, chuyển đổi bằng ép kiểu là một loại thao tác được-ăn-cả-ngã-về-không. Toàn bộ nội dung của chuỗi hoặc là được nhận diện là số (số nguyên hoặc số thực dấu phẩy động), hoặc là toàn bộ quá trình chuyển đổi thất bại (dẫn đến `NaN` -- một lần nữa, xem "Invalid Number" ở phần sau trong chương này).

Chuyển đổi bằng ép kiểu có thể được thực hiện rõ ràng bằng hàm `Number(..)` (không có từ khóa `new`) hoặc với toán tử một ngôi `+` ở phía trước giá trị:

```js
someNumericText = "123.456";

Number(someNumericText);        // 123.456
+someNumericText;               // 123.456

Number("512px");                // NaN
+"512px";                       // NaN
```

### Các cách biểu diễn số khác (Other Numeric Representations)

Ngoài việc định nghĩa các số bằng chữ số hệ thập phân truyền thống (`0`-`9`), JS hỗ trợ định nghĩa các số nguyên thuần túy trong ba hệ cơ số khác: nhị phân (cơ số 2), bát phân (cơ số 8) và thập lục phân (cơ số 16).

```js
// binary
myAge = 0b101010;
myAge;              // 42

// octal
myAge = 0o52;
myAge;              // 42

// hexadecimal
myAge = 0x2a;
myAge;              // 42
```

Như bạn có thể thấy, các tiền tố `0b` (nhị phân), `0o` (bát phân) và `0x` (thập lục phân) báo hiệu việc định nghĩa các số trong các hệ cơ số khác nhau, nhưng số thập phân không được phép trên các numeric literal này.

| GHI CHÚ: |
| :--- |
| Cú pháp JS cũng cho phép các tiền tố `0B`, `0O`, và `0X`. Tuy nhiên, xin đừng bao giờ sử dụng các dạng tiền tố viết hoa đó. Tôi nghĩ bất kỳ người có nhận thức hợp lý nào cũng sẽ đồng ý: `0O` dễ bị nhầm lẫn khi nhìn lướt qua hơn nhiều so với `0o` (bản thân nó cũng đã hơi mơ hồ về mặt thị giác khi nhìn lướt qua). Hãy luôn trung thành với các dạng tiền tố chữ thường! |

Điều quan trọng cần nhận ra là bạn không định nghĩa một *con số khác*, mà chỉ đang sử dụng một hình thức khác để tạo ra cùng một giá trị số bên dưới.

Theo mặc định, JS biểu diễn giá trị số bên dưới theo kiểu đầu ra/chuỗi ở dạng hệ cơ số 10 tiêu chuẩn. Tuy nhiên, các giá trị `number` có phương thức tích hợp sẵn `toString(..)` để tạo ra biểu diễn dạng chuỗi theo bất kỳ cơ số/radix nào được chỉ định (như với `parseInt(..)`, trong phạm vi `2` - `36`):

```js
myAge = 42;

myAge.toString(2);          // "101010"
myAge.toString(8);          // "52"
myAge.toString(16);         // "2a"
myAge.toString(23);         // "1j"
myAge.toString(36);         // "16"
```

Bạn có thể chuyển đổi qua lại bất kỳ chuỗi biểu diễn cơ số tùy ý nào trở lại thành một `number` bằng cách sử dụng `parseInt(..)`, với radix thích hợp:

```js
myAge = 42;

parseInt(myAge.toString("23"),23);      // 42
```

Một dạng khác được cho phép để chỉ định các numeric literal là sử dụng ký hiệu khoa học (scientific notation):

```js
myAge = 4.2E1;      // hoặc 4.2e1 hoặc 4.2e+1

myAge;              // 42
```

`4.2E1` (hoặc `4.2e1`) có nghĩa là, `4.2 * (10 ** 1)` (`10` lũy thừa `1`). Phần số mũ có thể tùy chọn có dấu `+` hoặc `-`. Nếu dấu bị bỏ qua, nó được coi là `+`. Số mũ âm làm cho số nhỏ hơn (di chuyển dấu thập phân sang trái) thay vì lớn hơn (di chuyển dấu thập phân sang phải):

```js
4.2E-3;             // 0.0042
```

Dạng ký hiệu khoa học này đặc biệt hữu ích cho khả năng đọc khi chỉ định các lũy thừa lớn hơn của `10`:

```js
someBigPowerOf10 = 1000000000;

// so với:

someBigPowerOf10 = 1e9;
```

Theo mặc định, JS sẽ biểu diễn (ví dụ: dưới dạng các giá trị chuỗi, v.v.) các số rất lớn hoặc rất nhỏ -- cụ thể nếu các giá trị yêu cầu độ chính xác hơn 21 chữ số -- bằng cách sử dụng chính ký hiệu khoa học này:

```js
ratherBigNumber = 123 ** 11;
ratherBigNumber.toString();     // "9.748913698143826e+22"

prettySmallNumber = 123 ** -11;
prettySmallNumber.toString();   // "1.0257553107587752e-23"
```

Các số có giá trị tuyệt đối nhỏ hơn (gần `0` hơn) so với các ngưỡng này vẫn có thể được buộc vào dạng ký hiệu khoa học (dưới dạng chuỗi):

```js
plainBoringNumber = 42;

plainBoringNumber.toExponential();      // "4.2e+1"
plainBoringNumber.toExponential(0);     // "4e+1"
plainBoringNumber.toExponential(4);     // "4.2000e+1"
```

Đối số tùy chọn cho `toExponential(..)` chỉ định số chữ số thập phân cần đưa vào biểu diễn chuỗi.

Một tiện ích về khả năng đọc khác để chỉ định các numeric literal trong mã nguồn là khả năng chèn `_` làm dấu phân cách chữ số ở bất kỳ nơi nào thuận tiện/có ý nghĩa. Ví dụ:

```js
someBigPowerOf10 = 1_000_000_000;

totalCostInPennies = 123_45;  // so với 12_345
```

Quyết định sử dụng `12345` (không có dấu phân cách), `12_345` (giống như "12,345"), hoặc `123_45` (giống như "123.45") hoàn toàn tùy thuộc vào tác giả của đoạn mã; JS bỏ qua các dấu phân cách này. Nhưng tùy thuộc vào ngữ cảnh, `123_45` có thể mang ý nghĩa ngữ nghĩa hơn (về mặt khả năng đọc) so với kiểu nhóm-ba-chữ-số-từ-bên-phải-ngăn-cách-bằng-dấu-phẩy truyền thống được bắt chước với `12_345`.

### Biểu diễn nhị phân Bitwise IEEE-754 (IEEE-754 Bitwise Binary Representations)

IEEE-754[^IEEE754] là một tiêu chuẩn kỹ thuật cho việc biểu diễn nhị phân của các số thập phân. Nó được sử dụng rộng rãi bởi hầu hết các ngôn ngữ lập trình máy tính, bao gồm JS, Python, Ruby, v.v.

Tôi sẽ không đi sâu vào chi tiết thấu đáo về nó, nhưng tôi nghĩ một hướng dẫn sơ lược về cách các con số hoạt động trong các ngôn ngữ như JS là hoàn toàn chính đáng, xét đến việc có rất ít lập trình viên có *bất kỳ* sự quen thuộc nào với nó.

Trong chuẩn IEEE-754 64-bit -- được gọi là "độ chính xác kép" ("double-precision"), bởi vì ban đầu IEEE-754 từng là 32-bit, và bây giờ nó đã gấp đôi con số đó! -- 64 bit được chia thành ba phần: 52 bit cho giá trị cơ sở của số (còn gọi là "phân số", "phần định trị" - "mantissa", hoặc "significand"), 11 bit cho số mũ để nâng `2` lên trước khi nhân, và 1 bit cho dấu của giá trị cuối cùng.

| GHI CHÚ: |
| :--- |
| Vì chỉ có 52 trong số 64 bit thực sự được sử dụng để biểu diễn giá trị cơ sở, kiểu `number` không thực sự có `2^64` giá trị trong đó. Theo đặc tả kỹ thuật cho kiểu `number`[^NumberType], số lượng giá trị chính xác là `2^64 - 2^53 + 3`, hay khoảng 18 nghìn tỷ tỷ (18 quintillion), được chia khá đồng đều giữa các số dương và số âm. |

Các bit này được sắp xếp từ trái sang phải như sau (S = Bit dấu, E = Bit số mũ, M = Bit phần định trị):

```js
SEEEEEEEEEEEMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
```

Vì vậy, số `42` (hoặc `42.000000`) sẽ được biểu diễn bằng các bit sau:

```
// 42:
01000000010001010000000000000000
00000000000000000000000000000000
```

Bit dấu là `0`, có nghĩa là số dương (`1` có nghĩa là số âm).

Số mũ 11-bit là nhị phân `10000000100`, trong hệ cơ số 10 là `1028`. Nhưng trong IEEE-754, giá trị này được diễn giải là được lưu trữ không dấu với "độ lệch số mũ" ("exponent bias") là `1023`, có nghĩa là chúng ta đang dịch chuyển dải số mũ từ `-1022:1023` thành `1:2046` (trong đó `0` và `2047` được dành riêng cho các biểu diễn đặc biệt). Vì vậy, lấy `1028` trừ đi độ lệch `1023`, cho ta số mũ thực tế là `5`. Ta nâng `2` lên giá trị đó (`2^5`), cho ra `32`.

| GHI CHÚ: |
| :--- |
| Nếu việc trừ `1023` khỏi giá trị số mũ cho ra một số âm (ví dụ: `-3`), thì giá trị đó vẫn được diễn giải là số mũ của `2`; việc nâng `2` lên các số âm chỉ tạo ra các giá trị ngày càng nhỏ hơn. |

52 bit còn lại cho chúng ta giá trị cơ sở `01010000...`, được hiểu là số thập phân nhị phân `1.0101000...` (với tất cả các số 0 ở cuối). Chuyển đổi *giá trị đó* sang hệ thập phân, chúng ta được `1.3125000...`. Cuối cùng, nhân giá trị đó với `32` đã được tính từ số mũ. Kết quả: `42`.

Như bạn có thể nhận ra bây giờ, tiêu chuẩn biểu diễn số IEEE-754 này được gọi là "dấu phẩy động" ("floating point") vì dấu thập phân "trôi nổi" qua lại dọc theo các bit, tùy thuộc vào giá trị số mũ được chỉ định.

Số `42.0000001`, vốn chỉ khác với `42.000000` đúng `0.0000001`, sẽ được biểu diễn bằng các bit sau:

```
// 42.0000001:
01000000010001010000000000000000
00000000110101101011111110010101
```

Hãy chú ý xem mẫu bit trước đó và mẫu bit này khác nhau khá nhiều bit ở các vị trí cuối cùng như thế nào! Phần phân số thập phân nhị phân chứa tất cả các bit `1` bổ sung đó (`1.010100000000...01011111110010101`) chuyển đổi sang hệ thập phân thành `1.31250000312500003652`, nhân với `32` cho chúng ta chính xác `42.0000001`.

Chúng ta sẽ xem lại chi tiết hơn về sự (không) chính xác của số dấu phẩy động trong Chương 2. Nhưng bây giờ bạn đã hiểu *thêm một chút* (a *bit* more) về cách IEEE-754 hoạt động!

### Các giới hạn của Số (Number Limits)

Như có thể thấy rõ sau khi bạn đã hiểu cách thức IEEE-754 hoạt động, 52 bit của phần cơ sở số phải được chia sẻ, biểu diễn cả phần số nguyên (nếu có) cũng như phần thập phân (nếu có) của giá trị `number` dự kiến. Về cơ bản, phần số nguyên cần biểu diễn càng lớn thì càng có ít bit khả dụng cho phần thập phân, và ngược lại.

Giá trị lớn nhất có thể được lưu trữ chính xác trong kiểu `number` được phơi bày dưới dạng `Number.MAX_VALUE`:

```js
Number.MAX_VALUE;           // 1.7976931348623157e+308
```

Bạn có thể mong đợi giá trị đó là một giá trị thập phân, dựa trên cách biểu diễn. Nhưng khi kiểm tra kỹ hơn, `1.79E308` (xấp xỉ) là `2^1024 - 1`. Điều đó có vẻ giống một số nguyên hơn nhiều, đúng không? Chúng ta có thể xác minh:

```js
Number.isInteger(Number.MAX_VALUE);         // true
```

Nhưng điều gì sẽ xảy ra nếu bạn vượt quá giá trị tối đa?

```js
Number.MAX_VALUE === (Number.MAX_VALUE + 1);
// true -- ôi thôi!

Number.MAX_VALUE === (Number.MAX_VALUE + 10000000);
// true
```

Vậy thì, `Number.MAX_VALUE` có thực sự là giá trị lớn nhất có thể biểu diễn trong JS không? Nó chắc chắn là giá trị `number` *hữu hạn* (finite) lớn nhất.

IEEE-754 định nghĩa một giá trị vô hạn đặc biệt, mà JS phơi bày dưới dạng `Infinity`; ngoài ra còn có `-Infinity` ở đầu kia của trục số. Các giá trị có thể được kiểm tra xem chúng là hữu hạn hay vô hạn:

```js
Number.isFinite(Number.MAX_VALUE);  // true

Number.isFinite(Infinity);          // false
Number.isFinite(-Infinity);         // false
```

Bạn không bao giờ có thể đếm tăng dần (với `+ 1`) từ `Number.MAX_VALUE` đến `Infinity`, bất kể bạn để chương trình chạy bao lâu, bởi vì phép toán `+ 1` không thực sự tăng tiến vượt ra ngoài giá trị trần `Number.MAX_VALUE`.

Tuy nhiên, các phép toán số học JS (`+`, `*`, và thậm chí `/`) chắc chắn có thể tràn kiểu `number` ở đầu trên, trong trường hợp đó `Infinity` sẽ là kết quả:

```js
Number.MAX_VALUE + 1E291;           // 1.7976931348623157e+308
Number.MAX_VALUE + 1E292;           // Infinity

Number.MAX_VALUE * 1.0000000001;    // Infinity

1 / 1E-308;                         // 1e+308
1 / 1E-309;                         // Infinity
```

| MẸO: |
| :--- |
| Điều ngược lại là không đúng: một phép toán số học trên một giá trị vô hạn *sẽ không bao giờ* tạo ra một giá trị hữu hạn. |

Đi từ cực kỳ lớn đến cực kỳ nhỏ -- thực ra là gần bằng 0 nhất, điều này không giống như việc đi về phía cực kỳ âm! -- giá trị thập phân tuyệt đối nhỏ nhất bạn có thể lưu trữ trên lý thuyết trong kiểu `number` sẽ là `2^-1022` (nhớ phạm vi số mũ của IEEE-754 chứ?), hoặc khoảng `2E-308`. Tuy nhiên, các công cụ JS được đặc tả kỹ thuật cho phép thay đổi trong các biểu diễn nội bộ của chúng đối với giới hạn dưới này. Bất kể giới hạn dưới thực tế của công cụ là bao nhiêu, nó sẽ được hiển thị dưới dạng `Number.MIN_VALUE`:

```js
Number.MIN_VALUE;               // 5e-324 <-- thường là vậy!
```

Hầu hết các công cụ JS dường như có giá trị nhỏ nhất có thể biểu diễn vào khoảng `5E-324` (khoảng `2^-1074`). Tùy thuộc vào công cụ và/hoặc nền tảng, một giá trị khác có thể xuất hiện. Hãy cẩn thận với bất kỳ logic chương trình nào dựa trên các giá trị phụ thuộc vào việc hiện thực hóa như vậy.

### Giới hạn số nguyên an toàn (Safe Integer Limits)

Vì `Number.MAX_VALUE` là một số nguyên, bạn có thể cho rằng nó là số nguyên lớn nhất trong ngôn ngữ. Nhưng điều đó không thực sự chính xác.

Số nguyên lớn nhất bạn có thể lưu trữ chính xác trong kiểu `number` là `2^53 - 1`, hay `9007199254740991`, vốn *nhỏ hơn rất nhiều* so với `Number.MAX_VALUE` (khoảng `2^1024 - 1`). Giá trị an toàn hơn đặc biệt này được phơi bày dưới dạng `Number.MAX_SAFE_INTEGER`:

```js
maxInt = Number.MAX_SAFE_INTEGER;

maxInt;             // 9007199254740991

maxInt + 1;         // 9007199254740992

maxInt + 2;         // 9007199254740992
```

Chúng ta đã thấy rằng các số nguyên lớn hơn `9007199254740991` có thể xuất hiện. Tuy nhiên, những số nguyên lớn hơn đó không "an toàn", ở chỗ độ chính xác/tính chuẩn xác bắt đầu bị phá vỡ khi bạn thực hiện các phép toán với chúng. Như được hiển thị ở trên, cả hai biểu thức `maxInt + 1` và `maxInt + 2` đều cho kết quả giống nhau một cách sai lầm, minh họa cho mối nguy hiểm khi vượt quá giới hạn `Number.MAX_SAFE_INTEGER`.

Nhưng số nguyên an toàn nhỏ nhất là gì?

Tùy thuộc vào cách bạn hiểu từ "nhỏ nhất", bạn có thể trả lời `0` hoặc... `Number.MIN_SAFE_INTEGER`:

```js
Number.MIN_SAFE_INTEGER;    // -9007199254740991
```

Và JS cung cấp một tiện ích để xác định xem một giá trị có phải là một số nguyên trong phạm vi an toàn này (`-2^53 + 1` đến `2^53 - 1`) hay không:

```js
Number.isSafeInteger(2 ** 53);      // false
Number.isSafeInteger(2 ** 53 - 1);  // true
```

### Hai số không (Double Zeros)

Có thể bạn sẽ ngạc nhiên khi biết rằng JS có hai số không: `0`, và `-0` (số không âm). Nhưng trên đời này "số không âm" là cái gì cơ chứ? [^SignedZero] Một nhà toán học chắc chắn sẽ chùn bước trước khái niệm như vậy.

Đây không chỉ là một nét kỳ quặc hài hước của JS; nó được quy định bởi đặc tả kỹ thuật IEEE-754[^IEEE754]. Tất cả các số dấu phẩy động đều có dấu, bao gồm cả số không. Và mặc dù JS có phần che giấu sự tồn tại của `-0`, nhưng hoàn toàn có thể tạo ra nó và phát hiện ra nó:

```js
function isNegZero(v) {
    return v == 0 && (1 / v) == -Infinity;
}

regZero = 0 / 1;
negZero = 0 / -1;

regZero === negZero;        // true -- ôi thôi!
Object.is(-0,regZero);      // false -- phù!
Object.is(-0,negZero);      // true

isNegZero(regZero);         // false
isNegZero(negZero);         // true
```

Bạn có thể tự hỏi tại sao chúng ta lại cần một thứ như `-0`. Nó có thể hữu ích khi sử dụng các con số để biểu thị cả độ lớn của chuyển động (tốc độ) của một đối tượng nào đó (chẳng hạn như một nhân vật trong trò chơi hoặc một hình ảnh động) và cả hướng di chuyển của nó (ví dụ: âm = trái, dương = phải).

Nếu không có giá trị số không có dấu, bạn không thể biết một đối tượng như vậy đang hướng về phía nào tại thời điểm nó dừng lại.

| GHI CHÚ: |
| :--- |
| Mặc dù JS định nghĩa số không có dấu trong kiểu `number`, không có số không có dấu tương ứng trong kiểu số `bigint`. Do đó, `-0n` chỉ được diễn giải là `0n`, và hai giá trị này không thể phân biệt được. |

### Số không hợp lệ (Invalid Number)

Các phép toán đôi khi có thể tạo ra một kết quả không hợp lệ. Ví dụ:

```js
42 / "Kyle";            // NaN
```

Điều này có lẽ là hiển nhiên, nhưng nếu bạn cố gắng chia một số cho một chuỗi, đó là một phép toán không hợp lệ.

Một dạng khác của thao tác số không hợp lệ là cố gắng ép kiểu chuyển đổi một giá trị không có dạng giống số thành một `number`. Như đã thảo luận trước đó, chúng ta có thể làm như vậy bằng hàm `Number(..)` hoặc toán tử một ngôi `+`:

```js
myAge = Number("just a number");

myAge;                  // NaN

+undefined;             // NaN
```

Tất cả các thao tác không hợp lệ như vậy (toán học hoặc ép kiểu/số) đều tạo ra giá trị `number` đặc biệt gọi là `NaN`.

Nguồn gốc lịch sử của "NaN" (từ đặc tả kỹ thuật IEEE-754[^IEEE754]) là từ viết tắt của "Not a Number" (Không phải là một số). Về mặt kỹ thuật, có khoảng 9 triệu tỷ giá trị trong không gian số IEEE-754 64-bit được chỉ định là "NaN", nhưng JS coi tất cả chúng như một giá trị `NaN` duy nhất không thể phân biệt.

Thật không may, ý nghĩa *không phải là một số* đó gây ra sự nhầm lẫn, vì `NaN` *hoàn toàn* là một `number`.

| MẸO: |
| :--- |
| Tại sao `NaN` lại là một `number`?!? Hãy nghĩ về điều ngược lại: điều gì sẽ xảy ra nếu một phép toán số học/toán học, chẳng hạn như `+` hoặc `/`, lại tạo ra một giá trị không phải `number` (như `null`, `undefined`, v.v.)? Điều đó chẳng phải sẽ thực sự kỳ lạ và không mong muốn sao? Sẽ ra sao nếu chúng ném ra ngoại lệ, để rồi bạn phải `try..catch` tất cả các phép tính toán của mình? Hành vi hợp lý duy nhất là, các phép toán số học/toán học phải *luôn luôn* tạo ra một `number`, ngay cả khi giá trị đó không hợp lệ vì nó xuất phát từ một thao tác không hợp lệ. |

Để tránh sự nhầm lẫn như vậy, tôi đặc biệt thích định nghĩa "NaN" theo bất kỳ cách nào sau đây:

* "iNvalid Number" (Số không hợp lệ)
* "Not actual Number" (Không phải số thực sự)
* "Not available Number" (Số không khả dụng)
* "Not applicable Number" (Số không áp dụng được)

`NaN` là một giá trị đặc biệt trong JS, ở chỗ nó là giá trị duy nhất trong ngôn ngữ thiếu *tính chất đồng nhất* (*identity property*) -- nó không bao giờ bằng chính nó.

```js
NaN === NaN;            // false
```

Vì vậy, thật không may, toán tử `===` không thể kiểm tra một giá trị để xem nó có phải là `NaN` hay không. Nhưng có một số cách để làm như vậy:

```js
politicianIQ = "nothing" / Infinity;

Number.isNaN(politicianIQ);         // true

Object.is(NaN,politicianIQ);        // true
[ NaN ].includes(politicianIQ);     // true
```

Đây là một thực tế của hầu như tất cả các chương trình JS, cho dù bạn có nhận ra hay không: `NaN` luôn xảy ra. Nghiêm túc mà nói, hầu như tất cả các chương trình thực hiện bất kỳ phép toán hoặc chuyển đổi số nào đều có nguy cơ gặp phải sự xuất hiện của `NaN`.

Nếu bạn không kiểm tra `NaN` đúng cách trong các chương trình của mình ở nơi thực hiện các phép toán hoặc chuyển đổi số, tôi có thể khẳng định với mức độ chắc chắn nhất định: bạn có thể đang có một lỗi về số ở đâu đó trong chương trình của mình, và nó chỉ chưa làm phiền bạn mà thôi (theo như những gì bạn biết!).

| CẢNH BÁO: |
| :--- |
| JS ban đầu cung cấp một hàm toàn cục có tên `isNaN(..)` để kiểm tra `NaN`, nhưng thật không may, nó lại có một lỗi ép kiểu tồn tại từ lâu. `isNaN("Kyle")` trả về `true`, mặc dù giá trị chuỗi `"Kyle"` chắc chắn *không phải* là giá trị `NaN`. Điều này là do hàm toàn cục `isNaN(..)` ép buộc bất kỳ đối số nào không phải `number` trước tiên phải ép kiểu sang `number`, trước khi kiểm tra `NaN`. Việc ép kiểu `"Kyle"` sang một `number` tạo ra `NaN`, vì vậy bây giờ hàm nhìn thấy một `NaN` và trả về `true`! Hàm toàn cục `isNaN(..)` có lỗi này vẫn tồn tại trong JS, nhưng không bao giờ nên được sử dụng. Khi kiểm tra `NaN`, hãy luôn sử dụng `Number.isNaN(..)`, `Object.is(..)`, v.v. |

## Các giá trị Số nguyên lớn (BigInteger Values)

Vì số nguyên an toàn tối đa trong các `number` của JS là `9007199254740991` (xem ở trên), một giới hạn tương đối thấp như vậy có thể gây ra vấn đề nếu một chương trình JS cần thực hiện các phép toán với số nguyên lớn hơn, hoặc thậm chí chỉ lưu trữ các giá trị như ID số nguyên 64-bit (ví dụ: Tweet ID của Twitter).

Vì lý do đó, JS cung cấp kiểu thay thế `bigint` (BigInteger), có thể lưu trữ các số nguyên lớn tùy ý (về mặt lý thuyết không bị giới hạn, ngoại trừ bộ nhớ hữu hạn của máy tính và/hoặc cách hiện thực hóa của JS).

Để phân biệt một `bigint` với một giá trị `number` nguyên (integer), vốn trông sẽ giống nhau (`42`), JS yêu cầu một hậu tố `n` trên các giá trị `bigint`:

```js
myAge = 42n;        // đây là một bigint, không phải một number

myKidsAge = 11;     // đây là một number, không phải một bigint
```

Hãy cùng minh họa tính chất không giới hạn bên trên của `bigint`:

```js
Number.MAX_SAFE_INTEGER;        // 9007199254740991

Number.MAX_SAFE_INTEGER + 2;    // 9007199254740992 -- ôi thôi!

myBigInt = 9007199254740991n;

myBigInt + 2n;                  // 9007199254740993n -- phù!

myBigInt ** 2n;                 // 81129638414606663681390495662081n
```

Như bạn có thể thấy, kiểu giá trị `bigint` có khả năng thực hiện các phép tính số học chính xác vượt lên trên giới hạn số nguyên của kiểu giá trị `number`.

| CẢNH BÁO: |
| :--- |
| Bạn có nhận thấy rằng toán tử `+` yêu cầu `.. + 2n` thay vì chỉ `.. + 2` không? Bạn không thể trộn lẫn các kiểu giá trị `number` và `bigint` trong cùng một biểu thức. Hạn chế này gây khó chịu, nhưng nó bảo vệ chương trình của bạn khỏi các thao tác toán học không hợp lệ có thể đem lại các kết quả không mong muốn không rõ ràng. |

Một giá trị `bigint` cũng có thể được tạo ra bằng hàm `BigInt(..)`; ví dụ: để chuyển đổi một giá trị `number` nguyên (integer) thành một `bigint`:

```js
myAge = 42n;

inc = 1;

myAge += BigInt(inc);

myAge;              // 43n
```

| CẢNH BÁO: |
| :--- |
| Mặc dù có vẻ phản trực giác đối với một số độc giả, `BigInt(..)` *luôn luôn* được gọi mà không có từ khóa `new`. Nếu sử dụng `new`, một ngoại lệ sẽ được ném ra. |

Đó chắc chắn là một trong những cách sử dụng phổ biến nhất của hàm `BigInt(..)`: để chuyển đổi các `number` thành `bigint`, phục vụ mục đích thao tác toán học.

Nhưng việc biểu diễn các giá trị số nguyên lớn dưới dạng chuỗi không phải là hiếm, đặc biệt nếu những giá trị đó đến với môi trường JS từ các môi trường ngôn ngữ khác, hoặc thông qua một số định dạng trao đổi dữ liệu vốn không tự hỗ trợ các giá trị kiểu `bigint`.

Do đó, `BigInt(..)` rất hữu ích để ép kiểu các giá trị chuỗi đó thành `bigint`:

```js
myBigInt = BigInt("12345678901234567890");

myBigInt;                       // 12345678901234567890n
```

Không giống như `parseInt(..)`, nếu bất kỳ ký tự nào trong chuỗi không phải là số (các chữ số `0-9` hoặc `-`), bao gồm cả `.` hoặc thậm chí là ký tự hậu tố `n` ở cuối, một ngoại lệ sẽ được ném ra. Nói cách khác, `BigInt(..)` là một phép chuyển đổi bằng ép kiểu được-ăn-cả-ngã-về-không, chứ không phải một phép chuyển đổi bằng phân tích cú pháp.

| GHI CHÚ: |
| :--- |
| Tôi thấy thật vô lý khi `BigInt(..)` không chấp nhận ký tự `n` ở cuối khi ép kiểu chuỗi (và do đó bỏ qua nó một cách hiệu quả). Tôi đã vận động quyết liệt cho hành vi đó trong quy trình TC39, nhưng cuối cùng đã bị từ chối. Theo tôi, giờ đây nó là một hạt sạn nhỏ trong JS, nhưng dẫu sao thì nó vẫn là một hạt sạn. |

## Các giá trị Symbol (Symbol Values)

Kiểu `symbol` chứa các giá trị mờ đục (opaque) đặc biệt được gọi là "symbol". Các giá trị này chỉ có thể được tạo ra bởi hàm `Symbol(..)`:

```js
secret = Symbol("my secret");
```

| CẢNH BÁO: |
| :--- |
| Cũng giống như với `BigInt(..)`, hàm `Symbol(..)` phải được gọi mà không có từ khóa `new`. |

Chuỗi `"my secret"` được truyền vào lệnh gọi hàm `Symbol(..)` *không phải* là bản thân giá trị symbol, mặc dù có vẻ như vậy. Nó chỉ đơn thuần là một nhãn mô tả tùy chọn, chỉ được sử dụng cho mục đích gỡ lỗi (debugging) vì lợi ích của nhà phát triển.

Giá trị bên dưới được trả về từ `Symbol(..)` là một loại giá trị đặc biệt chống lại việc chương trình/nhà phát triển kiểm tra bất cứ điều gì về cách biểu diễn bên dưới của nó. Đó là những gì tôi muốn nói qua từ "mờ đục" ("opaque").

| GHI CHÚ: |
| :--- |
| Bạn có thể nghĩ về các symbol như thể chúng là các số nguyên tăng đơn điệu -- thực tế, đó là cách tương tự mà ít nhất một số công cụ JS hiện thực hóa chúng. Nhưng công cụ JS sẽ không bao giờ để lộ bất kỳ biểu diễn nào về giá trị bên dưới của một symbol theo bất kỳ cách nào mà bạn hoặc chương trình có thể nhìn thấy. |

Các symbol được công cụ JS đảm bảo là duy nhất (chỉ trong phạm vi chính chương trình đó) và không thể đoán trước được. Nói cách khác, một giá trị symbol trùng lặp không bao giờ có thể được tạo ra trong một chương trình.

Đến đây bạn có thể tự hỏi symbol được sử dụng để làm gì?

Một cách sử dụng điển hình là làm các giá trị "đặc biệt" mà nhà phát triển phân biệt với bất kỳ giá trị nào khác có thể vô tình trùng lặp. Ví dụ:

```js
EMPTY = Symbol("not set yet");
myNickname = EMPTY;

// sau đó:

if (myNickname == EMPTY) {
    // ..
}
```

Ở đây, tôi đã định nghĩa một giá trị `EMPTY` đặc biệt và khởi tạo `myNickname` bằng nó. Sau đó, tôi kiểm tra xem nó có còn là giá trị đặc biệt đó hay không, và sau đó thực hiện một số hành động nếu đúng như vậy. Tôi có thể không muốn sử dụng `null` hoặc `undefined` cho các mục đích như vậy, vì một nhà phát triển khác có thể truyền vào một trong những giá trị tích hợp phổ biến đó. Ngược lại, `EMPTY` ở đây là một giá trị duy nhất, không thể đoán trước mà chỉ tôi mới định nghĩa, có quyền kiểm soát và truy cập vào.

Có lẽ thậm chí còn phổ biến hơn, các symbol thường được sử dụng làm các thuộc tính đặc biệt (meta-properties) trên các object:

```js
myInfo = {
    name: "Kyle Simpson",
    nickname: "getify",
    age: 42
};

// sau đó:
PRIVATE_ID = Symbol("private unique ID, don't touch!");

myInfo[PRIVATE_ID] = generateID();
```

Điều quan trọng cần lưu ý là các thuộc tính symbol vẫn hiển thị công khai trên bất kỳ object nào; chúng không *thực sự* ở chế độ riêng tư (private). Nhưng chúng được đối xử như đặc biệt và tách biệt khỏi bộ sưu tập bình thường của các thuộc tính object. Điều này tương tự như nếu thay vào đó tôi đã làm:

```js
Object.defineProperty(myInfo,"__private_id_dont_touch",{
    value: generateID(),
    enumerable: false,
    // (thuộc tính không thể liệt kê)
});
```

Chỉ theo quy ước, hầu hết các nhà phát triển đều biết rằng nếu một tên thuộc tính có tiền tố là `_` (hoặc thậm chí hơn thế nữa, `__`!), điều đó có nghĩa là nó là "giả riêng tư" ("pseudo-private") và hãy để yên trừ khi họ thực sự được phép truy cập vào nó.

Các symbol về cơ bản phục vụ cùng một trường hợp sử dụng, nhưng tiện dụng và tinh tế hơn một chút so với cách tiếp cận đặt tiền tố.

### Các Well-Known Symbol (WKS)

JS định nghĩa trước một tập hợp các symbol, được gọi là *well-known symbols* (WKS), đại diện cho một số điểm nối siêu lập trình (metaprogramming hooks) đặc biệt trên các object. Các symbol này được lưu trữ dưới dạng các thuộc tính tĩnh trên hàm đối tượng `Symbol`. Ví dụ:

```js
myInfo = {
    // ..
};

String(myInfo);         // [object Object]

myInfo[Symbol.toStringTag] = "my-info";
String(myInfo);         // [object my-info]
```

`Symbol.toStringTag` là một well-known symbol để truy cập và ghi đè biểu diễn chuỗi mặc định của một plain object (`"[object Object]"`), thay thế phần `"Object"` bằng một giá trị khác (ví dụ: `"my-info"`).

Xem cuốn sách "Objects & Classes" của bộ sách này để biết thêm thông tin về Well-Known Symbols và siêu lập trình.

### Sổ đăng ký Symbol Toàn cục (Global Symbol Registry)

Thông thường, bạn muốn giữ các giá trị symbol ở chế độ riêng tư, chẳng hạn như bên trong một scope của module. Nhưng đôi khi, bạn muốn hiển thị chúng để chúng có thể truy cập được trên phạm vi toàn cầu trong tất cả các tệp của một chương trình JS.

Thay vì chỉ gắn chúng dưới dạng các biến toàn cục (nghĩa là các thuộc tính trên đối tượng `globalThis`), JS cung cấp một *không gian tên toàn cục* (*global namespace*) thay thế để đăng ký các symbol:

```js
// lấy ra nếu đã đăng ký,
// nếu không thì đăng ký
PRIVATE_ID = Symbol.for("private-id");

// ở nơi khác:

privateIDKey = Symbol.keyFor(PRIVATE_ID);
privateIDKey;           // "private-id"

// ở nơi khác:

// lấy symbol từ sổ đăng ký theo
// khóa đã chỉ định
privateIDSymbol = Symbol.for(privateIDKey);
```

Giá trị được truyền vào `Symbol.for(..)` *không* giống như giá trị được truyền vào `Symbol(..)`. `Symbol.for(..)` mong đợi một *khóa* (*key*) duy nhất để symbol được đăng ký trong sổ đăng ký toàn cục, trong khi `Symbol(..)` tùy chọn chấp nhận một nhãn mô tả (không nhất thiết phải là duy nhất).

Nếu sổ đăng ký chưa có symbol nào dưới *khóa* đã chỉ định đó, một symbol mới (không có nhãn mô tả) sẽ được tạo và tự động đăng ký ở đó. Nếu không, `Symbol.for(..)` trả về bất kỳ symbol nào đã được đăng ký trước đó dưới *khóa* đó.

Đi theo hướng ngược lại, nếu bạn có chính giá trị symbol đó và muốn truy xuất *khóa* mà nó đã đăng ký, `Symbol.keyFor(..)` nhận chính symbol đó làm đầu vào và trả về *khóa* (nếu có). Điều đó hữu ích trong trường hợp việc truyền giá trị chuỗi *khóa* đi loanh quanh thuận tiện hơn bản thân symbol.

### Object hay Nguyên thủy? (Object or Primitive?)

Không giống như các giá trị nguyên thủy khác như `42`, nơi bạn có thể tạo nhiều bản sao của cùng một giá trị, các symbol *thực sự* hoạt động giống các tham chiếu object cụ thể hơn ở chỗ chúng luôn hoàn toàn duy nhất (cho mục đích gán giá trị và so sánh bằng). Đặc tả kỹ thuật cũng phân loại hàm `Symbol()` trong phần "Fundamental Objects", gọi hàm này là một "hàm khởi tạo" ("constructor"), và thậm chí định nghĩa thuộc tính `prototype` của nó.

Tuy nhiên, như đã đề cập trước đó, `new` không thể được sử dụng với `Symbol(..)`; điều này tương tự như "hàm khởi tạo" `BigInt()`. Chúng ta biết rõ rằng các giá trị `bigint` là nguyên thủy, vì vậy các giá trị `symbol` dường như cũng thuộc cùng một *chủng loại* đó.

Và trong phần "Terms and Definitions" của đặc tả kỹ thuật, nó liệt kê symbol là một giá trị nguyên thủy. [^PrimitiveValues] Hơn nữa, bản thân các giá trị này được sử dụng trong các chương trình JS như các giá trị nguyên thủy chứ không phải object. Ví dụ, các symbol chủ yếu được sử dụng làm các key trong các object -- chúng ta biết các object không thể sử dụng các giá trị object khác làm key! -- cùng với các chuỗi, vốn cũng là các giá trị nguyên thủy.

Như đã đề cập trước đó, một số công cụ JS thậm chí còn hiện thực hóa nội bộ các symbol dưới dạng các số nguyên tăng đơn điệu duy nhất (các giá trị nguyên thủy!).

Cuối cùng, như đã giải thích ở phần đầu của chương này, chúng ta biết các giá trị nguyên thủy *không được phép* thiết lập các thuộc tính trên chúng, mà được *auto-box* (xem "Automatic Objects" trong Chương 3) ở tầng nội bộ thành kiểu object bọc tương ứng để tạo điều kiện thuận lợi cho việc truy cập thuộc tính/phương thức. Các symbol tuân theo tất cả các hành vi chính xác này, giống như tất cả các giá trị nguyên thủy khác.

Cân nhắc tất cả những điều này, tôi nghĩ các symbol *giống các giá trị nguyên thủy hơn nhiều* so với object, vì vậy đó là cách tôi trình bày chúng trong cuốn sách này.

## Nguyên thủy là các Kiểu Tích hợp sẵn (Primitives Are Built-In Types)

Bây giờ chúng ta đã đào sâu vào bảy kiểu giá trị nguyên thủy (không phải object) mà JS cung cấp tự động được tích hợp sẵn.

Trước khi chuyển sang thảo luận về kiểu giá trị object tích hợp sẵn của JS, chúng ta muốn xem xét kỹ hơn các loại hành vi mà chúng ta có thể mong đợi từ các giá trị JS. Chúng ta sẽ làm như vậy một cách sâu sắc trong chương tiếp theo.

[^PrimitiveValues]: "4.4.5 primitive value", ECMAScript 2022 Language Specification; https://tc39.es/ecma262/#sec-primitive-value ; Truy cập tháng 8 năm 2022

[^UTFUCS]: "JavaScript’s internal character encoding: UCS-2 or UTF-16?"; Mathias Bynens; Ngày 20 tháng 1 năm 2012; https://mathiasbynens.be/notes/javascript-encoding ; Truy cập tháng 7 năm 2022

[^IEEE754]: "IEEE-754"; https://en.wikipedia.org/wiki/IEEE_754 ; Truy cập tháng 7 năm 2022

[^NumberType]: "6.1.6.1 The Number Type", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-ecmascript-language-types-number-type ; Truy cập tháng 8 năm 2022

[^SignedZero]: "Signed Zero", Wikipedia; https://en.wikipedia.org/wiki/Signed_zero ; Truy cập tháng 8 năm 2022
