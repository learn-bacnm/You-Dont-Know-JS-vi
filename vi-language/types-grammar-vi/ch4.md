# You Don't Know JS Yet: Types & Grammar - 2nd Edition
# Chương 4: Ép kiểu Giá trị (Coercing Values)

| GHI CHÚ: |
| :--- |
| Đang trong quá trình hoàn thiện (Work in progress) |

Chúng ta đã xem xét kỹ lưỡng tất cả các *kiểu* giá trị khác nhau trong JS. Và trong suốt hành trình đó, đã hơn vài lần chúng ta đề cập đến khái niệm chuyển đổi -- thực chất là ép kiểu (coercing) -- từ một kiểu giá trị này sang một kiểu giá trị khác.

Trong chương này, chúng ta sẽ đào sâu vào việc ép kiểu (coercion) và khám phá toàn bộ những điều bí ẩn của nó.

## Ép kiểu: Tường minh vs Ngầm định (Coercion: Explicit vs Implicit)

Một số nhà phát triển khẳng định rằng khi bạn chỉ định rõ ràng một sự thay đổi kiểu trong một phép toán, điều này không được coi là một sự *ép kiểu* (coercion) mà chỉ là một cú chuyển kiểu (type-cast) hoặc chuyển đổi kiểu (type-conversion). Nói cách khác, quan điểm của họ là ép kiểu chỉ xảy ra ngầm định (implicit).

Tôi không đồng ý với cách mô tả này. Tôi sử dụng thuật ngữ *coercion* (ép kiểu) để gán cho bất kỳ sự chuyển đổi kiểu nào trong một ngôn ngữ định kiểu động (dynamically-typed language), bất kể nó hiển hiện rõ ràng trong mã nguồn hay không. Đây là lý do: ranh giới giữa *tường minh* (explicit) và *ngầm định* (implicit) không hề rõ ràng và khách quan, mà nó mang tính chủ quan khá cao. Nếu bạn nghĩ rằng một phép chuyển đổi kiểu là ngầm định (và do đó là *coercion*), nhưng tôi lại nghĩ nó là tường minh (và do đó không phải là *coercion*), thì sự phân biệt này trở nên vô nghĩa.

Hãy ghi nhớ tính chủ quan đó khi chúng ta khám phá các hình thức ép kiểu *tường minh* và *ngầm định* khác nhau. Thực tế, tôi xin "spoil" trước một điều: hầu hết các phép ép kiểu đều có thể được tranh luận theo cả hai hướng, vì vậy chúng ta sẽ xem xét chúng với một góc nhìn cân bằng như vậy.

### Ngầm định: Xấu hay là ...? (Implicit: Bad or ...?)

Một quan điểm cực kỳ phổ biến trong cộng đồng các nhà phát triển JS là *ép kiểu là xấu*, cụ thể hơn, *ép kiểu ngầm định là xấu*; sự gia tăng mức độ phổ biến của các công cụ nhận biết kiểu như TypeScript đã nói lên rất rõ tâm lý này.

Nhưng cảm giác đó không hề mới mẻ. Hơn 14 năm trước, cuốn sách "The Good Parts" của Douglas Crockford cũng đã phê phán nổi tiếng rằng *ép kiểu ngầm định* là một trong những *phần tồi tệ* (bad parts). Thậm chí Brendan Eich, người tạo ra JS, cũng thường xuyên tuyên bố rằng *ép kiểu ngầm định* là một sai lầm[^EichCoercion] trong thiết kế ban đầu của ngôn ngữ mà giờ đây ông cảm thấy hối tiếc.

Nếu bạn tiếp xúc với JS nhiều hơn vài tháng, gần như chắc chắn bạn đã nghe thấy những quan điểm này được lên tiếng mạnh mẽ và chiếm ưu thế áp đảo. Và nếu bạn đã gắn bó với JS nhiều năm trở lên, có lẽ tâm trí bạn đã mặc định chốt xong định kiến này rồi.

Thực tế, tôi nghĩ bạn sẽ rất khó để kể tên được bất kỳ nguồn tài liệu giảng dạy JS nổi tiếng nào khác lại ủng hộ mạnh mẽ việc ép kiểu (dưới hầu như mọi hình thức của nó); tôi thì có -- và cuốn sách này chắc chắn làm điều đó! -- nhưng tôi cảm thấy mình hầu như giống như một tiếng kêu cô độc, vô vọng giữa chốn hoang vu.

Tuy nhiên, đây là một quan sát mà tôi đã nhận thấy qua nhiều năm: hầu hết những người công khai lên án *ép kiểu ngầm định*, trên thực tế lại sử dụng *ép kiểu ngầm định* trong chính mã nguồn của họ. Hmmmm...

Douglas Crockford bảo hãy tránh sai lầm của việc *ép kiểu ngầm định*[^CrockfordCoercion], nhưng mã nguồn của ông ấy lại sử dụng các câu lệnh `if (..)` với các giá trị không phải boolean được đánh giá.[^CrockfordIfs] Nhiều người đã gạt phắt đi sự chỉ ra đó của tôi trong quá khứ, với lập luận rằng chuyển đổi sang boolean không *thực sự* là ép kiểu. Ummm... vậy cơ à?

Brendan Eich nói ông ấy hối tiếc về *ép kiểu ngầm định*, nhưng dù vậy ông ấy lại công khai tán thành[^BrendanToString] các thành ngữ (idioms) như `x + ""` (và những cách khác!) để ép giá trị trong `x` sang một chuỗi (chúng ta sẽ đề cập đến điều này sau); và đó chắc chắn nhất là một phép *ép kiểu ngầm định*.

Vậy chúng ta rút ra được điều gì từ sự mâu thuẫn này? Liệu đó chỉ là một sự tự mâu thuẫn nhỏ kiểu "hãy làm theo những gì tôi nói, đừng làm theo những gì tôi làm"? Hay còn có điều gì uẩn khúc hơn thế nữa?

Tôi sẽ chưa đưa ra phán quyết cuối cùng ở đây vội, nhưng tôi muốn bạn, người đọc, hãy suy ngẫm sâu sắc về câu hỏi đó, khi bạn tiếp tục đi qua chương này và toàn bộ cuốn sách.

## Các Thao tác Trừu tượng (Abstracts)

Bây giờ khi tôi đã thách thức bạn xem xét việc ép kiểu ở mức độ sâu hơn những gì bạn có thể từng thỏa hiệp trước đây, trước tiên chúng ta hãy nhìn vào nền tảng của cách mà việc ép kiểu diễn ra, theo đặc tả kỹ thuật của JS (JS specification).

Đặc tả nêu chi tiết một số *thao tác trừu tượng* (abstract operations)[^AbstractOperations] quy định việc chuyển đổi nội bộ từ một kiểu giá trị này sang một kiểu giá trị khác. Việc nhận biết các thao tác này là rất quan trọng, vì các cơ chế ép kiểu trong ngôn ngữ sẽ kết hợp và ghép nối chúng theo nhiều cách khác nhau.

Các thao tác này *trông như thể* chúng là các hàm thực sự có thể gọi được, chẳng hạn như `ToString(..)` hay `ToNumber(..)`. Nhưng khi nói *trừu tượng* (abstract), ý chúng tôi là chúng chỉ tồn tại về mặt khái niệm dưới những cái tên này; chúng không phải là các hàm mà chúng ta có thể gọi *trực tiếp* trong chương trình của mình. Thay vào đó, chúng ta kích hoạt chúng một cách ngầm định/gián tiếp tùy thuộc vào các câu lệnh/biểu thức trong chương trình.

### ToBoolean

Việc đưa ra quyết định (rẽ nhánh điều kiện) luôn đòi hỏi một giá trị boolean `true` hoặc `false`. Nhưng việc muốn đưa ra những quyết định này dựa trên các điều kiện giá trị không phải boolean là cực kỳ phổ biến, chẳng hạn như kiểm tra xem một chuỗi có rỗng hay có chứa nội dung gì trong đó hay không.

Khi các giá trị không phải boolean xuất hiện trong một ngữ cảnh đòi hỏi kiểu boolean -- chẳng hạn như mệnh đề điều kiện của một câu lệnh `if` hoặc vòng lặp `for` -- thao tác trừu tượng `ToBoolean(..)`[^ToBoolean] sẽ được kích hoạt để tạo điều kiện thuận lợi cho việc ép kiểu.

Tất cả các giá trị trong JS đều rơi vào một trong hai nhóm: *truthy* hoặc *falsy*. Các giá trị truthy sẽ được ép kiểu thông qua thao tác `ToBoolean()` thành `true`, trong khi các giá trị falsy sẽ được ép kiểu thành `false`:

```
// ToBoolean() là thao tác trừu tượng

ToBoolean(undefined);               // false
ToBoolean(null);                    // false
ToBoolean("");                      // false
ToBoolean(0);                       // false
ToBoolean(-0);                      // false
ToBoolean(0n);                      // false
ToBoolean(NaN);                     // false
```

Quy tắc đơn giản: *bất kỳ giá trị nào khác* không nằm trong danh sách trên đều là truthy và được ép kiểu thông qua `ToBoolean()` thành `true`:

```
ToBoolean("hello");                 // true
ToBoolean(42);                      // true
ToBoolean([ 1, 2, 3 ]);             // true
ToBoolean({ a: 1 });                // true
```

Thậm chí các giá trị như `"   "` (chuỗi chỉ chứa khoảng trắng), `[]` (mảng rỗng), và `{}` (đối tượng rỗng), những thứ theo trực giác có vẻ mang tính "false" nhiều hơn "true", dù vậy vẫn được ép kiểu thành `true`.

| CẢNH BÁO: |
| :--- |
| *Có* những ngoại lệ hẹp, phức tạp đối với quy tắc truthy này. Ví dụ, nền tảng web đã khai tử tính năng tập hợp/mảng lâu đời `document.all`, mặc dù nó không thể bị loại bỏ hoàn toàn -- điều đó sẽ làm hỏng quá nhiều trang web. Ngay cả ở nơi mà `document.all` vẫn được định nghĩa, nó hoạt động như một "đối tượng mang tính falsy" (falsy object)[^ExoticFalsyObjects] -- trả về `undefined` mà sau đó sẽ được ép kiểu thành `false`; điều này có nghĩa là các phép kiểm tra điều kiện kế thừa cũ như `if (document.all) { .. }` sẽ không còn vượt qua được nữa. |

Thao tác ép kiểu `ToBoolean()` về cơ bản là một bảng tra cứu (lookup table) thay vì là một thuật toán gồm nhiều bước để sử dụng trong việc ép một giá trị không phải boolean thành boolean. Do đó, một số nhà phát triển khẳng định rằng điều này không *thực sự* là ép kiểu theo cách mà các thao tác ép kiểu trừu tượng khác vận hành. Tôi nghĩ điều đó thật nhảm nhí. `ToBoolean()` chuyển đổi từ các kiểu giá trị không phải boolean sang kiểu boolean, và đó rõ ràng là ép kiểu (ngay cả khi nó chỉ là một bảng tra cứu rất đơn giản thay vì một thuật toán).

Hãy nhớ rằng: những quy tắc ép kiểu boolean này chỉ áp dụng khi `ToBoolean()` thực sự được kích hoạt. Có những cấu trúc/thành ngữ trong ngôn ngữ JS thoạt nhìn có vẻ liên quan đến ép kiểu boolean nhưng thực tế lại không làm như vậy. Chúng ta sẽ nói thêm về những điều này sau.

### ToPrimitive

Bất kỳ giá trị nào không phải là giá trị nguyên thủy (primitive) đều có thể được giản lược về dạng nguyên thủy bằng cách sử dụng thao tác trừu tượng `ToPrimitive()` (cụ thể là `OrdinaryToPrimitive()`[^OrdinaryToPrimitive]). Nhìn chung, `ToPrimitive()` được cung cấp một *gợi ý* (hint) để cho nó biết liệu kiểu `number` hay `string` được ưu tiên hơn.

```
// ToPrimitive() là thao tác trừu tượng

ToPrimitive({ a: 1 },"string");          // "[object Object]"

ToPrimitive({ a: 1 },"number");          // NaN
```

Thao tác `ToPrimitive()` sẽ tìm kiếm trên đối tượng được cung cấp một phương thức `toString()` hoặc một phương thức `valueOf()`; thứ tự mà nó tìm kiếm chúng được kiểm soát bởi *hint*. `"string"` có nghĩa là kiểm tra theo thứ tự `toString()` / `valueOf()`, trong khi `"number"` (hoặc không có *hint*) có nghĩa là kiểm tra theo thứ tự `valueOf()` / `toString()`.

Nếu phương thức trả về một giá trị khớp với kiểu được *gợi ý* (hinted type), thao tác sẽ kết thúc. Nhưng nếu phương thức không trả về một giá trị thuộc kiểu được *gợi ý*, `ToPrimitive()` sau đó sẽ tìm kiếm và gọi phương thức còn lại (nếu tìm thấy).

Nếu các lần thử gọi phương thức đều không tạo ra được giá trị thuộc kiểu được *gợi ý*, giá trị trả về cuối cùng sẽ bị ép kiểu cưỡng bức thông qua thao tác trừu tượng tương ứng: `ToString()` hoặc `ToNumber()`.

### ToString

Hầu như bất kỳ giá trị nào chưa phải là chuỗi đều có thể được ép kiểu thành một biểu diễn chuỗi, thông qua `ToString()`. [^ToString] Điều này thường khá trực quan, đặc biệt là với các giá trị nguyên thủy:

```
// ToString() là thao tác trừu tượng

ToString(42.0);                 // "42"
ToString(-3);                   // "-3"
ToString(Infinity);             // "Infinity"
ToString(NaN);                  // "NaN"
ToString(42n);                  // "42"

ToString(true);                 // "true"
ToString(false);                // "false"

ToString(null);                 // "null"
ToString(undefined);            // "undefined"
```

Có *một số* kết quả có thể khác với trực giác thông thường. Như đã đề cập trong Chương 2, các số rất lớn hoặc rất nhỏ sẽ được biểu diễn bằng ký hiệu khoa học (scientific notation):

```
ToString(Number.MAX_VALUE);     // "1.7976931348623157e+308"
ToString(Math.EPSILON);         // "2.220446049250313e-16"
```

Một kết quả phản trực giác khác đến từ `-0`:

```
ToString(-0);                   // "0" -- wtf?
```

Đây không phải là một lỗi, nó chỉ là một hành vi có chủ ý từ những ngày đầu tiên của JS, dựa trên giả định rằng các nhà phát triển nhìn chung sẽ không bao giờ muốn nhìn thấy đầu ra số không âm (negative-zero).

Một kiểu giá trị nguyên thủy *không được phép* ép kiểu (ít nhất là ngầm định) sang chuỗi là `symbol`:

```
ToString(Symbol("ok"));         // Ném ra ngoại lệ TypeError
```

| CẢNH BÁO: |
| :--- |
| Việc gọi hàm cụ thể `String()`[^StringFunction] (không có toán tử `new`) thường được coi là *chỉ đơn thuần* kích hoạt thao tác trừu tượng `ToString()`. Mặc dù điều đó hầu như đúng, nhưng nó không hoàn toàn đúng tuyệt đối. `String(Symbol("ok"))` hoạt động được, trong khi bản thân thao tác trừu tượng `ToString(Symbol(..))` lại ném ra ngoại lệ. Chi tiết hơn về `String(..)` sẽ có ở phần sau trong chương này. |

#### `toString()` Mặc định (Default `toString()`)

Khi `ToString()` được kích hoạt với một kiểu giá trị đối tượng, nó ủy quyền cho thao tác `ToPrimitive()` (như đã giải thích ở trước), với `"string"` là kiểu được *gợi ý* (hinted type):

```
ToString(new String("abc"));        // "abc"
ToString(new Number(42));           // "42"

ToString({ a: 1 });                 // "[object Object]"
ToString([ 1, 2, 3 ]);              // "1,2,3"
```

Nhờ việc ủy quyền `ToPrimitive(..,"string")`, tất cả các đối tượng này đều được kích hoạt phương thức `toString()` mặc định của chúng (được kế thừa thông qua `[[Prototype]]`).

### ToNumber

Các giá trị không phải số *nhìn giống như* số, chẳng hạn như các chuỗi số (numeric strings), nhìn chung có thể được ép kiểu thành một biểu diễn số, bằng cách sử dụng `ToNumber()`: [^ToNumber]

```
// ToNumber() là thao tác trừu tượng

ToNumber("42");                     // 42
ToNumber("-3");                     // -3
ToNumber("1.2300");                 // 1.23
ToNumber("   8.0    ");             // 8
```

Nếu toàn bộ giá trị không giống *hoàn toàn* (ngoại trừ khoảng trắng) một số hợp lệ, kết quả sẽ là `NaN`:

```
ToNumber("123px");                  // NaN
ToNumber("hello");                  // NaN
```

Các giá trị nguyên thủy khác có các giá trị số tương đương được chỉ định nhất định:

```
ToNumber(true);                     // 1
ToNumber(false);                    // 0

ToNumber(null);                     // 0
ToNumber(undefined);                // NaN
```

Có một số giá trị tương đương khá bất ngờ đối với `ToNumber()`:

```
ToNumber("");                       // 0
ToNumber("       ");                // 0
```

| GHI CHÚ: |
| :--- |
| Tôi gọi những trường hợp này là "bất ngờ" vì tôi nghĩ việc chúng được ép kiểu thành `NaN` sẽ hợp lý hơn nhiều, giống như cách `undefined` được xử lý. |

Một số giá trị nguyên thủy *không được phép* ép kiểu sang số, và dẫn đến ngoại lệ thay vì `NaN`:

```
ToNumber(42n);                      // Ném ra ngoại lệ TypeError
ToNumber(Symbol("42"));             // Ném ra ngoại lệ TypeError
```

| CẢNH BÁO: |
| :--- |
| Việc gọi hàm cụ thể `Number()`[^NumberFunction] (không có toán tử `new`) thường được coi là *chỉ đơn thuần* kích hoạt thao tác trừu tượng `ToNumber()` để ép một giá trị sang số. Mặc dù điều đó hầu như đúng, nhưng nó không hoàn toàn đúng tuyệt đối. `Number(42n)` hoạt động được, trong khi bản thân thao tác trừu tượng `ToNumber(42n)` lại ném ra ngoại lệ. |

#### Các Chuyển đổi Số Trừu tượng Khác (Other Abstract Numeric Conversions)

Ngoài `ToNumber()`, đặc tả còn định nghĩa `ToNumeric()`, thao tác này kích hoạt `ToPrimitive()` trên một giá trị, sau đó ủy quyền có điều kiện cho `ToNumber()` nếu giá trị đó *không* phải là kiểu giá trị `bigint`.

Ngoài ra còn có rất nhiều thao tác trừu tượng liên quan đến việc chuyển đổi các giá trị sang các tập hợp con rất cụ thể của kiểu `number` nói chung:

* `ToIntegerOrInfinity()`
* `ToInt32()`
* `ToUint32()`
* `ToInt16()`
* `ToUint16()`
* `ToInt8()`
* `ToUint8()`
* `ToUint8Clamp()`

Các thao tác khác liên quan đến `bigint`:

* `ToBigInt()`
* `StringToBigInt()`
* `ToBigInt64()`
* `ToBigUint64()`

Có thể bạn sẽ suy đoán được mục đích của các thao tác này từ tên gọi của chúng, và/hoặc từ việc tham khảo các thuật toán của chúng trong đặc tả. Đối với hầu hết các hoạt động của JS, nhiều khả năng một thao tác cấp cao hơn như `ToNumber()` sẽ được kích hoạt, thay vì các thao tác cụ thể này.

#### `valueOf()` Mặc định (Default `valueOf()`)

Khi `ToNumber()` được kích hoạt trên một kiểu giá trị đối tượng, thay vào đó nó sẽ ủy quyền cho thao tác `ToPrimitive()` (như đã giải thích ở trước), với `"number"` là kiểu được *gợi ý*:

```
ToNumber(new String("abc"));        // NaN
ToNumber(new Number(42));           // 42

ToNumber({ a: 1 });                 // NaN
ToNumber([ 1, 2, 3 ]);              // NaN
ToNumber([]);                       // 0
```

Nhờ việc ủy quyền `ToPrimitive(..,"number")`, tất cả các đối tượng này đều được kích hoạt phương thức `valueOf()` mặc định của chúng (được kế thừa thông qua `[[Prototype]]`).

### So sánh Bằng (Equality Comparison)

Khi JS cần xác định xem hai giá trị có phải là *cùng một giá trị* hay không, nó sẽ kích hoạt thao tác `SameValue()`[^SameValue], thao tác này ủy quyền cho nhiều thao tác con liên quan khác.

Thao tác này rất hẹp và nghiêm ngặt, đồng thời không thực hiện ép kiểu hay bất kỳ trường hợp ngoại lệ đặc biệt nào. Nếu hai giá trị *chính xác* là như nhau, kết quả là `true`, ngược lại là `false`:

```
// SameValue() là thao tác trừu tượng

SameValue("hello","\x68ello");          // true
SameValue("\u{1F4F1}","\uD83D\uDCF1");  // true
SameValue(42,42);                       // true
SameValue(NaN,NaN);                     // true

SameValue("\u00e9","\u0065\u0301");     // false
SameValue(0,-0);                        // false
SameValue([1,2,3],[1,2,3]);             // false
```

Một biến thể của các thao tác này là `SameValueZero()` và các thao tác con liên quan của nó. Điểm khác biệt chính là các thao tác này coi `0` và `-0` là không thể phân biệt được.

```
// SameValueZero() là thao tác trừu tượng

SameValueZero(0,-0);                    // true
```

Nếu các giá trị là dạng số (`number` hoặc `bigint`), cả `SameValue()` và `SameValueZero()` đều ủy quyền cho các thao tác con cùng tên, lần lượt được chuyên biệt hóa cho từng kiểu `number` và `bigint`.

Nếu không, `SameValueNonNumeric()` là thao tác con được ủy quyền nếu các giá trị được so sánh đều không phải dạng số:

```
// SameValueNonNumeric() là thao tác trừu tượng

SameValueNonNumeric("hello","hello");   // true

SameValueNonNumeric([1,2,3],[1,2,3]);   // false
```

#### Phép So sánh Bằng Trừu tượng Hóa Cấp Cao Hơn (Higher-Abstracted Equality)

Khác với `SameValue()` và các biến thể của nó, đặc tả còn định nghĩa hai thao tác so sánh bằng trừu tượng cấp cao quan trọng:

* `IsStrictlyEqual()`[^StrictEquality]
* `IsLooselyEqual()`[^LooseEquality]

Thao tác `IsStrictlyEqual()` ngay lập tức trả về `false` nếu các kiểu giá trị được so sánh là khác nhau.

Nếu các kiểu giá trị giống nhau, `IsStrictlyEqual()` ủy quyền cho các thao tác con để so sánh các giá trị `number` hoặc `bigint`. [^NumericAbstractOps] Theo lẽ thông thường, bạn có thể kỳ vọng các thao tác con được ủy quyền này sẽ là các thao tác `SameValue()` / `SameValueZero()` chuyên biệt cho dạng số đã đề cập trước đó. Tuy nhiên, thay vào đó `IsStrictlyEqual()` lại ủy quyền cho `Number:equal()`[^NumberEqual] hoặc `BigInt:equal()`[^BigIntEqual].

Sự khác biệt giữa `Number:SameValue()` và `Number:equal()` là hàm sau định nghĩa các trường hợp đặc biệt cho phép so sánh `0` vs `-0`:

```
// tất cả các thao tác này đều là thao tác trừu tượng

Number:SameValue(0,-0);             // false
Number:SameValueZero(0,-0);         // true
Number:equal(0,-0);                 // true
```

Các thao tác này cũng khác nhau trong phép so sánh `NaN` vs `NaN`:

```
Number:SameValue(NaN,NaN);          // true
Number:equal(NaN,NaN);              // false
```

| CẢNH BÁO: |
| :--- |
| Nói cách khác, bất chấp tên gọi của nó, `IsStrictlyEqual()` không hoàn toàn "nghiêm ngặt" (strict) như `SameValue()`, ở chỗ nó *nói dối* khi có sự tham gia của các phép so sánh liên quan đến `-0` hoặc `NaN`. |

Thao tác `IsLooselyEqual()` cũng kiểm tra các kiểu giá trị được so sánh; nếu chúng giống nhau, nó ngay lập tức ủy quyền cho `IsStrictlyEqual()`.

Nhưng nếu các kiểu giá trị được so sánh khác nhau, `IsLooselyEqual()` sẽ thực hiện một loạt các bước *so sánh bằng có ép kiểu* (coercive equality). Điều quan trọng cần lưu ý là thuật toán này luôn cố gắng giản lược phép so sánh về trạng thái mà cả hai kiểu giá trị đều giống nhau (và nó có xu hướng ưu tiên `number` / `bigint`).

Các bước của phần *so sánh bằng có ép kiểu* trong thuật toán có thể được tóm tắt sơ bộ như sau:

1. Nếu một trong hai giá trị là `null` và giá trị kia là `undefined`, `IsLooselyEqual()` trả về `true`. Nói cách khác, thuật toán này áp dụng phép bằng mang tính *nullish*, trong đó `null` và `undefined` bằng nhau theo kiểu ép buộc (và không bằng bất kỳ giá trị nào khác).

2. Nếu một trong hai giá trị là `number` và giá trị kia là `string`, giá trị `string` sẽ được ép kiểu thành `number` thông qua `ToNumber()`.

3. Nếu một trong hai giá trị là `bigint` và giá trị kia là `string`, giá trị `string` sẽ được ép kiểu thành `bigint` thông qua `StringToBigInt()`.

4. Nếu một trong hai giá trị là `boolean`, nó sẽ được ép kiểu thành `number`.

5. Nếu một trong hai giá trị không phải là kiểu nguyên thủy (object, v.v.), nó sẽ được ép kiểu thành giá trị nguyên thủy bằng `ToPrimitive()`; mặc dù một *hint* không được cung cấp rõ ràng, hành vi mặc định sẽ hoạt động như thể `"number"` là gợi ý.

Mỗi lần một phép ép kiểu được thực hiện trong các bước trên, thuật toán sẽ được tái kích hoạt một cách *đệ quy* với (các) giá trị mới. Quá trình đó tiếp tục cho đến khi các kiểu trở nên giống nhau, và sau đó phép so sánh được ủy quyền cho thao tác `IsStrictlyEqual()`.

Chúng ta có thể rút ra điều gì từ thuật toán này? Đầu tiên, chúng ta thấy có một sự thiên vị đối với phép so sánh `number` (hoặc `bigint`); nó không bao giờ ép các giá trị sang kiểu giá trị `string` hoặc `boolean`.

Quan trọng là, chúng ta thấy rằng cả `IsLooselyEqual()` và `IsStrictlyEqual()` đều nhạy cảm với kiểu (type-sensitive). `IsStrictlyEqual()` lập tức từ bỏ (trả về false) nếu các kiểu không khớp nhau, trong khi `IsLooselyEqual()` thực hiện thêm công việc để ép các kiểu giá trị không khớp nhau trở thành cùng một kiểu giá trị (một lần nữa, lý tưởng nhất là `number` hoặc `bigint`).

Hơn nữa, nếu/khi các kiểu giống nhau, cả hai thao tác là giống hệt nhau -- `IsLooselyEqual()` ủy quyền cho `IsStrictlyEqual()`.

### So sánh Quan hệ (Relational Comparison)

Khi các giá trị được so sánh theo quan hệ thứ tự -- nghĩa là, một giá trị có "nhỏ hơn" một giá trị khác hay không? -- có một thao tác trừu tượng cụ thể được kích hoạt: `IsLessThan()`. [^LessThan]

```
// IsLessThan() là thao tác trừu tượng

IsLessThan(1,2, /*LeftFirst=*/ true );            // true
```

Không có thao tác `IsGreaterThan()` nào cả; thay vào đó, hai đối số đầu tiên của `IsLessThan()` có thể được đảo ngược để thực hiện phép so sánh "lớn hơn". Để duy trì ngữ nghĩa đánh giá từ trái sang phải (trong trường hợp có các tác dụng phụ tinh vi), `IsLessThan()` cũng nhận đối số thứ ba (`LeftFirst`); nếu là `false`, điều này cho biết phép so sánh đã bị đảo ngược và tham số thứ hai cần được đánh giá trước tham số thứ nhất.

```
IsLessThan(1,2, /*LeftFirst=*/ true );            // true

// tương đương với một "IsGreaterThan()" giả định
IsLessThan(2,1, /*LeftFirst=*/ false );          // false
```

Tương tự như `IsLooselyEqual()`, thao tác `IsLessThan()` mang tính *ép kiểu* (coercive), nghĩa là trước tiên nó đảm bảo rằng các kiểu giá trị của hai giá trị khớp nhau, và ưu tiên các phép so sánh dạng số. Không có thao tác `IsStrictLessThan()` nào dành cho phép so sánh quan hệ không ép kiểu cả.

Ví dụ về phép so sánh quan hệ có ép kiểu: nếu kiểu của một giá trị là `string` và kiểu của giá trị kia là `bigint`, chuỗi `string` sẽ được ép kiểu thành `bigint` bằng thao tác `StringToBigInt()` đã đề cập trước đó. Khi các kiểu đã giống nhau, `IsLessThan()` tiến hành như được mô tả trong các phần sau.

#### So sánh Chuỗi (String Comparison)

Khi cả hai giá trị đều thuộc kiểu `string`, `IsLessThan()` kiểm tra xem giá trị bên tay trái có phải là tiền tố (prefix - gồm *n* ký tự đầu tiên[^StringPrefix]) của giá trị bên tay phải hay không; nếu đúng, `true` sẽ được trả về.

Nếu không có chuỗi nào là tiền tố của chuỗi kia, vị trí ký tự đầu tiên (theo hướng từ đầu đến cuối, không phải từ trái sang phải) khác biệt giữa hai chuỗi sẽ được so sánh theo giá trị đơn vị mã (code-unit - dạng số) tương ứng của chúng; kết quả sau đó được trả về.

Nhìn chung, các đơn vị mã tuân theo thứ tự từ điển (lexicographic / dictionary order) một cách trực quan:

```
IsLessThan("a","b", /*LeftFirst=*/ true );        // true
```

Ngay cả các chữ số cũng được coi là ký tự (không phải số):

```
IsLessThan("101","12", /*LeftFirst=*/ true );     // true
```

Thậm chí còn có một chút *hài hước* được cài cắm trong thứ tự đơn vị mã unicode:

```
IsLessThan("🐔","🥚", /*LeftFirst=*/ true );      // true
```

Ít nhất thì bây giờ chúng ta đã trả lời được câu hỏi muôn thuở là *cái nào có trước*?!

#### So sánh Số (Numeric Comparison)

Đối với các phép so sánh số, `IsLessThan()` lần lượt tuân theo thao tác `Number:lessThan()` hoặc `BigInt:lessThan()`[^NumericAbstractOps]:

```
IsLessThan(41,42, /*LeftFirst=*/ true );         // true

IsLessThan(-0,0, /*LeftFirst=*/ true );          // false

IsLessThan(NaN,1 /*LeftFirst=*/ true );          // false

IsLessThan(41n,42n, /*LeftFirst=*/ true );       // true
```

## Các Phép Ép kiểu Cụ thể (Concrete Coercions)

Bây giờ khi chúng ta đã nắm được tất cả các thao tác trừu tượng mà JS định nghĩa để xử lý các loại ép kiểu khác nhau, đã đến lúc chuyển sự chú ý sang các câu lệnh/biểu thức cụ thể mà chúng ta có thể sử dụng trong chương trình để kích hoạt các thao tác này.

### Ép sang Boolean (To Boolean)

Để ép một giá trị không thuộc kiểu `boolean` về kiểu đó, chúng ta cần thao tác trừu tượng `ToBoolean()`, như đã mô tả ở phần trước trong chương này.

Trước khi khám phá *cách* kích hoạt nó, hãy cùng thảo luận xem *tại sao* bạn lại muốn ép buộc một sự ép kiểu `ToBoolean()`.

Từ góc độ khả năng đọc hiểu mã nguồn, việc *tường minh* về các phép ép kiểu có thể được ưu tiên hơn (mặc dù không phải luôn luôn như vậy). Nhưng về mặt chức năng, lý do phổ biến nhất để ép buộc về kiểu `boolean` là khi bạn chuyển dữ liệu sang một nguồn bên ngoài -- ví dụ: gửi dữ liệu dưới dạng JSON đến một API endpoint -- và nơi đó mong đợi `true` / `false` mà không cần phải tự thực hiện ép kiểu.

Có một số cách để kích hoạt `ToBoolean()`. Có lẽ cách *tường minh* nhất (rõ ràng nhất) là hàm `Boolean(..)`:

```js
Boolean("hello");               // true
Boolean(42);                    // true

Boolean("");                    // false
Boolean(0);                     // false
```

Như đã đề cập trong Chương 3, hãy nhớ rằng `Boolean(..)` đang được gọi mà không có từ khóa `new`, để kích hoạt thao tác trừu tượng `ToBoolean()`.

Việc nhìn thấy các nhà phát triển JS sử dụng hàm `Boolean(..)` cho những phép ép kiểu tường minh như vậy không phải là quá phổ biến. Thường xuyên hơn, các nhà phát triển sẽ sử dụng thành ngữ hai dấu chấm than `!!`:

```js
!!"hello";                      // true
!!42;                           // true

!!"";                           // false
!!0;                            // false
```

`!!` không phải là một toán tử độc lập của riêng nó, mặc dù nhìn trông có vẻ như vậy. Nó thực chất là hai lần sử dụng toán tử một ngôi `!`. Toán tử này đầu tiên sẽ ép bất kỳ giá trị nào không phải `boolean`, sau đó phủ định nó. Để hoàn tác sự phủ định, dấu `!` thứ hai sẽ đảo ngược nó trở lại.

Vậy thì... bạn coi cách nào trong hai cách, `Boolean(..)` hay `!!`, mang tính ép kiểu tường minh hơn?

Xét về việc đảo ngược mà `!` thực hiện, rồi sau đó phải được hoàn tác bằng một dấu `!` khác, tôi sẽ nói `Boolean(..)` *tường minh hơn* -- trong nhiệm vụ ép một giá trị không phải `boolean` thành `boolean` -- so với `!!`. Nhưng khi khảo sát mã nguồn mở JS, `!!` được sử dụng thường xuyên hơn rất nhiều.

Nếu chúng ta định nghĩa *tường minh* là "thực hiện một hành động một cách trực tiếp và hiển nhiên nhất", `Boolean(..)` vượt trội hơn `!!`. Nhưng nếu chúng ta định nghĩa *tường minh* là "thực hiện một hành động một cách dễ nhận diện nhất", `!!` có thể chiếm ưu thế. Liệu có một câu trả lời dứt khoát ở đây không?

Trong khi bạn đang suy ngẫm về câu hỏi đó, hãy cùng xem xét một cơ chế JS khác kích hoạt `ToBoolean()` ở bên dưới:

```js
specialNumber = 42;

if (specialNumber) {
    // ..
}
```

Câu lệnh `if` đòi hỏi một giá trị `boolean` cho phần điều kiện để đưa ra quyết định điều khiển luồng (control-flow). Nếu bạn truyền cho nó một giá trị không phải `boolean`, một phép *ép kiểu* `ToBoolean()` sẽ được thực hiện.

Không giống như các biểu thức ép kiểu `ToBoolean()` trước đó, như `Boolean(..)` hoặc `!!`, phép ép kiểu của `if` này mang tính phù du (ephemeral), ở chỗ chương trình JS của chúng ta không bao giờ nhìn thấy kết quả của phép ép kiểu; nó chỉ được sử dụng nội bộ bởi `if`. Một số người có thể cảm thấy nó không *thực sự* là ép kiểu nếu chương trình không lưu giữ/sử dụng giá trị đó. Nhưng tôi kịch liệt không đồng ý, bởi vì phép ép kiểu này chắc chắn nhất là ảnh hưởng đến hành vi của chương trình.

Nhiều loại câu lệnh khác cũng kích hoạt phép ép kiểu `ToBoolean()`, bao gồm toán tử ba ngôi điều kiện `? :`, và các vòng lặp `for` / `while`. Chúng ta cũng có các toán tử `&&` (AND logic) và `||` (OR logic). Ví dụ:

```js
isLoggedIn = user.sessionID || req.cookie["Session-ID"];

isAdmin = isLoggedIn && ("admin" in user.permissions);
```

Đối với cả hai toán tử, biểu thức bên tay trái được đánh giá trước; nếu nó chưa phải là một `boolean`, một phép ép kiểu `ToBoolean()` sẽ được kích hoạt để tạo ra một giá trị cho quyết định điều kiện.

| GHI CHÚ: |
| :--- |
| Để giải thích ngắn gọn về các toán tử này: đối với `||`, nếu giá trị của biểu thức bên tay trái (sau khi ép kiểu, nếu cần) là `true`, giá trị trước khi ép kiểu sẽ được trả về; ngược lại, biểu thức bên tay phải được đánh giá và trả về (không ép kiểu). Đối với `&&`, nếu giá trị của biểu thức bên tay trái (sau khi ép kiểu, nếu cần) là `false`, giá trị trước khi ép kiểu sẽ được trả về; ngược lại, biểu thức bên tay phải được đánh giá và trả về (không ép kiểu). Nói cách khác, cả `&&` và `||` đều ép buộc một phép ép kiểu `ToBoolean()` trên toán hạng bên tay trái để đưa ra quyết định, nhưng kết quả cuối cùng của cả hai toán tử đều không thực sự bị ép sang kiểu `boolean`. |

Trong đoạn mã trước, bất chấp hàm ý từ cách đặt tên, việc `isLoggedIn` thực sự là một `boolean` là điều khó xảy ra; và nếu nó là truthy, `isAdmin` cũng sẽ không phải là một `boolean`. Kiểu mã đó khá phổ biến, nhưng chắc chắn là nguy hiểm khi các kiểu `boolean` kết quả được giả định lại không thực sự tồn tại ở đó. Chúng ta sẽ xem lại ví dụ này, và các toán tử này, trong chương tiếp theo.

Liệu những loại câu lệnh/biểu thức này (ví dụ: `if (..)`, `||`, `&&`, v.v.) đang minh họa việc ép kiểu *tường minh* hay ép kiểu *ngầm định* trong việc đưa ra quyết định điều kiện của chúng?

Một lần nữa, tôi nghĩ điều đó phụ thuộc vào góc nhìn của bạn. Đặc tả quy định khá rõ ràng rằng chúng chỉ đưa ra quyết định với các giá trị điều kiện `boolean`, yêu cầu ép kiểu nếu nhận được một giá trị không phải `boolean`. Mặt khác, một lập luận mạnh mẽ cũng có thể được đưa ra rằng bất kỳ phép ép kiểu nội bộ nào cũng là một hiệu ứng thứ cấp (ngầm định) đối với công việc chính của `if` / `&&` / v.v.

Hơn nữa, như đã đề cập trước đó trong phần thảo luận về `ToBoolean()`, một số người không coi *bất kỳ* sự kích hoạt nào của `ToBoolean()` là một phép ép kiểu.

Tuy nhiên, tôi nghĩ điều đó là khiên cưỡng quá mức. Quan điểm của tôi: `Boolean(..)` là hình thức ép kiểu *tường minh* thích hợp nhất. Tôi nghĩ `!!`, `if`, `for`, `while`, `&&`, và `||` đều đang ép kiểu *ngầm định* các giá trị không phải `boolean`, nhưng tôi hoàn toàn đồng tình với điều đó.

Vì hầu hết các nhà phát triển, bao gồm cả những tên tuổi nổi tiếng như Doug Crockford, trong thực tế cũng sử dụng các phép ép kiểu (`boolean`) ngầm định trong mã nguồn của họ[^CrockfordIfs], tôi nghĩ chúng ta có thể nói rằng ít nhất *một số hình thức* ép kiểu *ngầm định* là được chấp nhận rộng rãi, bất chấp những lời lẽ hùng hồn phổ biến nói ngược lại.

### Ép sang Chuỗi (To String)

Giống như với `ToBoolean()`, có một số cách để kích hoạt phép ép kiểu `ToString()` (như đã thảo luận ở phần trước của chương). Quyết định chọn cách tiếp cận nào cũng mang tính chủ quan tương tự.

Giống như hàm `Boolean(..)`, hàm `String(..)` (không có từ khóa `new`) là một cách chủ đạo để kích hoạt phép ép kiểu `ToString()` một cách *tường minh*:

```js
String(true);                   // "true"
String(42);                     // "42"
String(-0);                     // "0"
String(Infinity);               // "Infinity"

String(null);                   // "null"
String(undefined);              // "undefined"
```

Tuy nhiên, `String(..)` không *chỉ đơn thuần* là một sự kích hoạt của `ToString()`. Ví dụ:

```js
String(Symbol("ok"));           // "Symbol(ok)"
```

Cách này hoạt động được, bởi vì việc ép kiểu *tường minh* các giá trị `symbol` được cho phép. Nhưng trong các trường hợp một symbol bị ép kiểu *ngầm định* sang chuỗi (ví dụ: `Symbol("ok") + ""`), thao tác `ToString()` bên dưới sẽ ném ra ngoại lệ. Điều đó chứng minh rằng `String(..)` không chỉ đơn giản là kích hoạt `ToString()`. Sẽ có thêm thông tin về việc ép kiểu chuỗi *ngầm định* của symbol sau một chút nữa.

Nếu bạn gọi `String(..)` với một giá trị đối tượng (ví dụ: mảng, v.v.), nó sẽ kích hoạt thao tác `ToPrimitive()` (thông qua thao tác `ToString()`), sau đó thao tác này sẽ tìm kiếm và gọi phương thức `toString()` của giá trị đó:

```js
String([1,2,3]);                // "1,2,3"

String(x => x + 1);             // "x => x + 1"
```

Bên cạnh `String(..)`, bất kỳ giá trị nguyên thủy nào không thuộc dạng nullish (không phải `null` cũng không phải `undefined`) đều có thể được tự động bao gói (auto-boxed - xem Chương 3) trong wrapper object tương ứng của nó, cung cấp một phương thức `toString()` có thể gọi được.

```js
true.toString();                // "true"
42..toString();                 // "42"
-0..toString();                 // "0"
Infinity.toString();            // "Infinity"
Symbol("ok").toString();        // "Symbol(ok)"
```

| GHI CHÚ: |
| :--- |
| Hãy nhớ rằng, các phương thức `toString()` này *không* nhất thiết phải kích hoạt thao tác `ToString()`, chúng chỉ tự định nghĩa các quy tắc của riêng mình về cách biểu diễn giá trị dưới dạng một chuỗi. |

Như đã thấy với `String(..)` cách đây ít phút, các kiểu con đối tượng khác nhau -- chẳng hạn như mảng, hàm, biểu thức chính quy, các instance của `Date` và `Error`, v.v. -- đều định nghĩa các phương thức `toString()` cụ thể của riêng chúng, có thể được gọi trực tiếp:

```js
[1,2,3].toString();             // "1,2,3"

(x => x + 1).toString();        // "x => x + 1"
```

Hơn nữa, bất kỳ đối tượng thuần túy nào được liên kết `[[Prototype]]` (theo mặc định) tới `Object.prototype` đều có sẵn phương thức `toString()` mặc định:

```js
({ a : 1 }).toString();         // "[object Object]"
```

Cách tiếp cận ép kiểu bằng `toString()` là *tường minh* hay *ngầm định*? Một lần nữa, điều đó còn tùy. Nó chắc chắn là một cơ chế tự mô tả, nghiêng về phía *tường minh*. Nhưng nó thường dựa vào tính năng tự động bao gói (auto-boxing), bản thân việc này lại là một sự ép kiểu khá *ngầm định*.

Hãy cùng xem xét một thành ngữ phổ biến khác -- và được tán thành nổi tiếng! -- để ép một giá trị thành một chuỗi. Nhớ lại phần "Nối Chuỗi" (String Concatenation) trong Chương 2, toán tử `+` được nạp chồng (overloaded) để ưu tiên nối chuỗi nếu một trong hai toán hạng đã là một chuỗi, và do đó sẽ ép toán hạng không phải chuỗi thành chuỗi nếu cần thiết.

Hãy xem xét:

```js
true + "";                      // "true"
42 + "";                        // "42"
null + "";                      // "null"
undefined + "";                 // "undefined"
```

Thành ngữ `+ ""` để ép kiểu chuỗi tận dụng tính năng nạp chồng của `+`, mà không làm thay đổi giá trị chuỗi được ép kiểu cuối cùng. Nhân tiện, tất cả những biểu thức này đều hoạt động giống nhau khi các toán hạng bị đảo ngược (nghĩa là `"" + ..`).

| CẢNH BÁO: |
| :--- |
| Một quan niệm sai lầm cực kỳ phổ biến là `String(x)` và `x + ""` về cơ bản là các phép ép kiểu tương đương, lần lượt chỉ khác nhau ở dạng thức *tường minh* vs *ngầm định*. Nhưng điều đó không hoàn toàn đúng! Chúng ta sẽ xem lại điều này trong phần "Ép sang Giá trị Nguyên thủy" (To Primitive) ở phần sau của chương này. |

Một số người cảm thấy đây là một phép ép kiểu *tường minh*, nhưng tôi nghĩ nó rõ ràng mang tính *ngầm định* hơn, ở chỗ nó đang tận dụng sự nạp chồng của `+`; hơn nữa, `""` được sử dụng một cách gián tiếp để kích hoạt việc ép kiểu mà không làm thay đổi nội dung. Hơn thế nữa, hãy xem xét điều gì xảy ra khi thành ngữ này được áp dụng với một giá trị symbol:

```js
Symbol("ok") + "";              // Ném ra ngoại lệ TypeError
```

| CẢNH BÁO: |
| :--- |
| Cho phép ép kiểu *tường minh* các symbol (`String(Symbol("ok"))`), nhưng không cho phép ép kiểu *ngầm định* (`Symbol("ok") + ""`), là một quyết định hoàn toàn có chủ ý của TC39. [^SymbolString] Người ta cảm thấy rằng các symbol, với tư cách là các giá trị nguyên thủy thường được sử dụng ở những nơi mà chuỗi hay được dùng, có thể quá dễ bị nhầm lẫn với chuỗi. Do đó, họ muốn đảm bảo rằng các nhà phát triển phải thể hiện rõ ý định ép kiểu symbol thành chuỗi, hy vọng tránh được nhiều sự nhầm lẫn đã được dự đoán trước đó. Đây là một trong những trường hợp *cực kỳ hiếm hoi* mà thiết kế ngôn ngữ thể hiện một quan điểm, và thực sự phân biệt đối xử giữa ép kiểu *tường minh* vs *ngầm định*. |

Tại sao lại có ngoại lệ? JS coi `+ ""` là một phép ép kiểu *ngầm định*, đó là lý do tại sao khi được kích hoạt với một symbol, một ngoại lệ sẽ được ném ra. Tôi nghĩ đó là một bằng chứng đanh thép không thể chối cãi.

Dù vậy, như tôi đã đề cập ở đầu chương này, Brendan Eich tán thành `+ ""`[^BrendanToString] là cách *tốt nhất* để ép các giá trị thành chuỗi. Tôi nghĩ điều đó mang rất nhiều sức nặng, xét trên phương diện ông ấy ủng hộ ít nhất một tập hợp con của các thực hành ép kiểu *ngầm định*. Quan điểm của ông về ép kiểu *ngầm định* hẳn phải có nhiều sắc thái tinh tế hơn là chỉ đơn thuần "tất cả đều tồi tệ".

### Ép sang Số (To Number)

Các phép ép kiểu số phức tạp hơn một chút so với ép kiểu chuỗi, vì chúng ta có thể đang nói về kiểu đích là `number` hoặc `bigint`. Ngoài ra còn có một tập hợp giá trị nhỏ hơn nhiều có thể được biểu diễn hợp lệ dưới dạng số (mọi thứ khác đều biến thành `NaN`).

Hãy bắt đầu với các hàm `Number(..)` và `BigInt(..)` (không có từ khóa `new`):

```js
Number("42");                   // 42
Number("-3.141596");            // -3.141596
Number("-0");                   // -0

BigInt("42");                   // 42n
BigInt("-0");                   // 0n
```

Phép ép kiểu `Number` bị thất bại (không được nhận dạng) sẽ dẫn đến `NaN` (xem "Số Không hợp lệ" trong Chương 1), trong khi `BigInt` ném ra ngoại lệ:

```js
Number("123px");                // NaN

BigInt("123px");
// SyntaxError: Cannot convert 123px to a BigInt
```

Hơn nữa, mặc dù `42n` là cú pháp hợp lệ cho một literal `bigint`, chuỗi `"42n"` không bao giờ là một biểu diễn chuỗi được nhận dạng của một `bigint`, bởi bất kỳ hình thức hàm ép kiểu nào:

```js
Number("42n");                  // NaN

BigInt("42n");
// SyntaxError: Cannot convert 42n to a BigInt
```

Tuy nhiên, chúng ta *có thể* ép kiểu các chuỗi số với các biểu diễn số khác ngoài các số thập phân hệ cơ số 10 thông thường (xem Chương 1 để biết thêm thông tin):

```js
Number("0b101010");             // 42

BigInt("0b101010");             // 42n
```

Thông thường, `Number(..)` và `BigInt(..)` nhận các giá trị chuỗi, nhưng điều đó thực sự không bắt buộc. Ví dụ, `true` và `false` ép kiểu thành các giá trị số tương đương điển hình của chúng:

```js
Number(true);                   // 1
Number(false);                  // 0

BigInt(true);                   // 1n
BigInt(false);                  // 0n
```

Bạn cũng có thể ép kiểu tổng quát giữa các kiểu `number` và `bigint`:

```js
Number(42n);                    // 42
Number(42n ** 1000n);           // Infinity

BigInt(42);                     // 42n
```

Chúng ta cũng có thể sử dụng toán tử một ngôi `+`, toán tử thường được cho là thực hiện việc ép kiểu tương tự như hàm `Number(..)`:

```js
+"42";                          // 42
+"0b101010";                    // 42
```

Tuy nhiên hãy cẩn thận. Nếu các phép ép kiểu không an toàn/không hợp lệ theo những cách nhất định, ngoại lệ sẽ được ném ra:

```js
BigInt(3.141596);
// RangeError: The number 3.141596 cannot be converted to a BigInt

+42n;
// TypeError: Cannot convert a BigInt value to a number
```

Rõ ràng, `3.141596` không thể ép kiểu an toàn thành một số nguyên, chưa nói đến một `bigint`.

Nhưng việc `+42n` ném ra ngoại lệ là một trường hợp thú vị. Ngược lại, `Number(42n)` hoạt động bình thường, vì vậy hơi bất ngờ khi `+42n` lại thất bại.

| CẢNH BÁO: |
| :--- |
| Sự ngạc nhiên đó đặc biệt rõ rệt vì việc đặt dấu `+` đằng trước một số thường được cho là chỉ biểu thị một "số dương", giống như cách đặt dấu `-` đằng trước một số được cho là biểu thị một "số âm". Tuy nhiên, như đã giải thích trong Chương 1, cú pháp số của JS (`number` và `bigint`) không nhận biết cú pháp nào cho "giá trị âm". Tất cả các numeric literal đều được phân tích cú pháp là "dương" theo mặc định. Nếu một dấu `+` hoặc `-` được đặt phía trước, chúng được coi là các toán tử một ngôi được áp dụng cho số (dương) đã được phân tích cú pháp. |

Được rồi, vậy `+42n` được phân tích cú pháp thành `+(42n)`. Nhưng vẫn... tại sao `+` lại ném ra một ngoại lệ ở đây?

Bạn có thể nhớ lại trước đó khi chúng tôi chỉ ra rằng JS cho phép ép kiểu chuỗi *tường minh* đối với các giá trị symbol, nhưng không cho phép ép kiểu chuỗi *ngầm định*? Điều tương tự cũng đang diễn ra ở đây. Thiết kế ngôn ngữ JS diễn giải toán tử một ngôi `+` đứng trước một giá trị `bigint` như một phép ép kiểu `ToNumber()` *ngầm định* (do đó bị cấm!), nhưng `Number(..)` được diễn giải như một phép ép kiểu `ToNumber()` *tường minh* (do đó được phép!).

Nói cách khác, trái ngược với giả định/khẳng định phổ biến, `Number(..)` và `+` không thể hoán đổi cho nhau. Tôi nghĩ `Number(..)` là dạng an toàn hơn/đáng tin cậy hơn.

#### Các Phép toán Học (Mathematical Operations)

Các toán tử toán học (ví dụ: `+`, `-`, `*`, `/`, `%`, và `**`) yêu cầu các toán hạng của chúng phải là dạng số. Nếu bạn sử dụng một giá trị không phải `number` với chúng, giá trị đó sẽ được ép kiểu thành `number` phục vụ cho mục đích tính toán số học.

Tương tự như việc `x + ""` là một thành ngữ để ép `x` thành một chuỗi, một biểu thức như `x - 0` sẽ ép `x` thành một số một cách an toàn.

| CẢNH BÁO: |
| :--- |
| `x + 0` không an toàn bằng, vì toán tử `+` bị nạp chồng để thực hiện nối chuỗi nếu một trong hai toán hạng đã là một chuỗi. Toán tử trừ `-` không bị nạp chồng như vậy, do đó phép ép kiểu duy nhất sẽ là sang `number`. Dĩ nhiên, `x * 1`, `x / 1`, và thậm chí `x ** 1` về mặt toán học nhìn chung cũng sẽ tương đương, nhưng những cách đó ít phổ biến hơn nhiều, và có lẽ nên tránh vì có thể gây nhầm lẫn cho người đọc mã nguồn của bạn. Thậm chí `x % 1` có vẻ như sẽ an toàn, nhưng nó có thể tạo ra độ lệch dấu phẩy động (xem "Sự Không chính xác của Số Dấu phẩy Động" trong Chương 2). |

Bất kể toán tử toán học nào được sử dụng, nếu phép ép kiểu thất bại, kết quả sẽ là một giá trị `NaN`, và tất cả các toán tử này sẽ lan truyền `NaN` ra ngoài dưới dạng kết quả của chúng.

#### Các Phép toán Thao tác Bit (Bitwise Operations)

Các toán tử thao tác bit (ví dụ: `|`, `&`, `^`, `>>`, `<<`, và `>>>`) đều yêu cầu các toán hạng là số, nhưng cụ thể là chúng kẹp (clamp) các giá trị này vào phạm vi số nguyên 32-bit.

Nếu bạn chắc chắn các số mình đang xử lý nằm an toàn trong phạm vi số nguyên 32-bit, `x | 0` là một thành ngữ biểu thức phổ biến khác có tác dụng ép `x` thành một `number` nếu cần thiết.

Hơn nữa, vì các engine JS biết các giá trị này sẽ là số nguyên, nên có tiềm năng để chúng tối ưu hóa cho các phép toán chỉ dành riêng cho số nguyên nếu chúng nhìn thấy `x | 0`. Đây là một trong số các "chú thích kiểu" (type annotations) được đề xuất từ những nỗ lực của ASM.js[^ASMjs] từ nhiều năm trước.

#### Truy cập Thuộc tính (Property Access)

Truy cập thuộc tính của các đối tượng (và truy cập chỉ mục của mảng) là một nơi khác mà việc ép kiểu ngầm định có thể xảy ra.

Hãy xem xét:

```js
myObj = {};

myObj[3] = "hello";
myObj["3"] = "world";

console.log( myObj );
```

Bạn mong đợi điều gì từ nội dung của đối tượng này? Bạn có mong đợi hai thuộc tính khác nhau, số `3` (chứa `"hello"`) và chuỗi `"3"` (chứa `"world"`)? Hay bạn nghĩ cả hai thuộc tính đều nằm trong cùng một vị trí?

Nếu bạn thử đoạn mã đó, bạn sẽ thấy rằng thực sự chúng ta nhận được một đối tượng chỉ có một thuộc tính duy nhất, và nó chứa giá trị `"world"`. Điều đó có nghĩa là JS đang ép kiểu nội bộ hoặc `3` thành `"3"`, hoặc ngược lại, khi các truy cập thuộc tính đó được thực hiện.

Điều thú vị là, bảng điều khiển dành cho nhà phát triển (developer console) rất có thể biểu diễn đối tượng theo kiểu như thế này:

```js
console.log( myObj );
// {3: 'world'}
```

Số `3` ở đó có chỉ ra rằng thuộc tính là một số `3` không? Không hẳn vậy. Hãy thử thêm một thuộc tính khác vào `myObj`:

```js
myObj.something = 42;

console.log( myObj )
// {3: 'world', something: 42}
```

Chúng ta có thể thấy rằng developer console này không đặt dấu ngoặc kép cho các khóa thuộc tính dạng chuỗi, vì vậy chúng ta không thể suy luận được điều gì từ số `3` so với việc nếu console đã sử dụng `"3"` cho tên khóa.

Thay vào đó, hãy thử tham khảo đặc tả cho giá trị đối tượng[^ObjectValue], nơi chúng ta tìm thấy:

> Một giá trị khóa thuộc tính (property key value) hoặc là một giá trị String của ECMAScript hoặc là một giá trị Symbol. Tất cả các giá trị String và Symbol, bao gồm cả String rỗng, đều hợp lệ dưới dạng khóa thuộc tính. Một tên thuộc tính (property name) là một khóa thuộc tính có giá trị là một String.

OK! Vì vậy, trong JS, các đối tượng chỉ chứa các thuộc tính dạng chuỗi (hoặc symbol). Điều đó có nghĩa là số `3` được ép kiểu thành một chuỗi `"3"`, đúng không?

Trong cùng phần đó của đặc tả, chúng ta đọc thêm:

> Một chỉ mục số nguyên (integer index) là một khóa thuộc tính có giá trị String vốn là một String số chuẩn tắc (canonical numeric String - xem 7.1.21) và có giá trị số hoặc là +0𝔽 hoặc là một Số nguyên dương ≤ 𝔽(253 - 1). Một chỉ mục mảng (array index) là một chỉ mục số nguyên có giá trị số i nằm trong phạm vi +0𝔽 ≤ i < 𝔽(232 - 1).

Nếu một khóa thuộc tính (như `"3"`) *trông giống* một số, nó được coi là một chỉ mục số nguyên. Hmmm... điều đó gần như gợi ý điều ngược lại với những gì chúng ta vừa giả định, đúng không?

Tuy nhiên, chúng ta biết từ trích dẫn trước rằng các khóa thuộc tính *chỉ* là các chuỗi (hoặc symbol). Vì vậy chắc chắn rằng "chỉ mục số nguyên" ở đây không mô tả vị trí thực tế, mà đúng hơn là cách sử dụng có chủ đích của số `3` trong mã JS, như một "chỉ mục số nguyên" do nhà phát triển diễn đạt; JS sau đó vẫn thực sự lưu trữ nó tại vị trí của "String số chuẩn tắc".

Hãy xem xét các nỗ lực sử dụng các kiểu giá trị khác, chẳng hạn như `true`, `null`, `undefined`, hoặc thậm chí các giá trị không phải nguyên thủy (các đối tượng khác):

```js
myObj[true] = 100;
myObj[null] = 200;
myObj[undefined] = 300;
myObj[ {a:1} ] = 400;

console.log(myObj);
// {3: 'world', something: 42, true: 100, null: 200,
// undefined: 300, [object Object]: 400}
```

Như bạn có thể thấy, tất cả các kiểu giá trị khác đó đều được ép kiểu thành chuỗi cho mục đích làm tên thuộc tính đối tượng.

Nhưng trước khi chúng ta tự thuyết phục mình về cách diễn giải này rằng mọi thứ (ngay cả số) đều được ép kiểu thành chuỗi, hãy cùng xem một ví dụ về mảng:

```js
myArr = [];

myArr[3] = "hello";
myArr["3"] = "world";

console.log( myArr );
// [empty × 3, 'world']
```

Developer console có thể sẽ biểu diễn một mảng hơi khác một chút so với một đối tượng thuần túy. Dù vậy, chúng ta vẫn thấy rằng mảng này chỉ có giá trị `"world"` duy nhất trong đó, tại vị trí chỉ mục số tương ứng với `3`.

Kiểu đầu ra đó ngụ ý điều ngược lại với cách giải thích trước đây của chúng ta: các giá trị của một mảng chỉ được lưu trữ tại các vị trí dạng số. Nếu chúng ta thêm một tên thuộc tính chuỗi khác vào `myArr`:

```js
myArr.something = 42;
console.log( myArr );
// [empty × 3, 'world', something: 42]
```

Bây giờ chúng ta thấy rằng developer console này biểu diễn các vị trí được lập chỉ mục bằng số trong mảng *mà không có* tên thuộc tính (vị trí), nhưng thuộc tính `something` lại được đặt tên trong đầu ra.

Cũng đúng là các engine JS như v8 có xu hướng, vì lý do tối ưu hóa hiệu năng, xử lý đặc biệt các thuộc tính đối tượng là các chuỗi trông giống số dưới dạng thực sự được lưu trữ ở các vị trí số như thể chúng là các mảng. Vì vậy, ngay cả khi chương trình JS hoạt động như thể tên thuộc tính là `"3"`, thực tế ở bên dưới, v8 có thể đang coi nó như thể đã được ép kiểu thành `3`!

Chúng ta có thể rút ra điều gì từ tất cả những điều này?

Đặc tả nói rõ cho chúng ta biết rằng hành vi của các thuộc tính đối tượng là chúng được đối xử giống như các chuỗi (hoặc symbol). Điều đó có nghĩa là chúng ta có thể giả định rằng việc sử dụng `3` để truy cập một vị trí trên một đối tượng sẽ có tác dụng nội bộ là ép tên thuộc tính đó thành `"3"`.

Nhưng với mảng, chúng ta quan sát thấy một ngữ nghĩa gần như ngược lại: sử dụng `"3"` làm tên thuộc tính có tác dụng truy cập vào vị trí `3` được lập chỉ mục bằng số, như thể chuỗi đã được ép kiểu thành số. Nhưng điều đó phần lớn chỉ là một hệ quả từ thực tế là các mảng luôn có xu hướng hoạt động dưới dạng được lập chỉ mục bằng số, và/hoặc có lẽ là sự phản ánh các chi tiết triển khai/tối ưu hóa bên dưới trong engine JS.

Phần quan trọng là, chúng ta cần nhận ra rằng các đối tượng không thể chỉ đơn giản sử dụng bất kỳ giá trị nào làm tên thuộc tính. Nếu nó là bất kỳ thứ gì khác ngoài chuỗi hoặc số, chúng ta có thể dự đoán rằng *sẽ có* một phép ép kiểu cho giá trị đó.

Chúng ta cần dự liệu và lên kế hoạch cho điều đó thay vì để nó làm chúng ta bất ngờ với các lỗi phát sinh về sau!

### Ép sang Giá trị Nguyên thủy (To Primitive)

Hầu hết các toán tử trong JS, bao gồm cả những toán tử chúng ta đã thấy với các phép ép kiểu sang `string` và `number`, được thiết kế để chạy trên các giá trị nguyên thủy. Khi bất kỳ toán tử nào trong số này được sử dụng trên một giá trị đối tượng, thuật toán trừu tượng `ToPrimitive` (như đã mô tả ở trước) sẽ được kích hoạt để ép đối tượng đó thành một giá trị nguyên thủy.

Hãy thiết lập một đối tượng mà chúng ta có thể sử dụng để kiểm tra cách các thao tác khác nhau hoạt động:

```js
spyObject = {
    toString() {
        console.log("toString() invoked!");
        return "10";
    },
    valueOf() {
        console.log("valueOf() invoked!");
        return 42;
    },
};
```

Đối tượng này định nghĩa cả hai phương thức `toString()` và `valueOf()`, và mỗi phương thức trả về một kiểu giá trị khác nhau (`string` vs `number`).

Hãy thử một số thao tác ép kiểu mà chúng ta đã thấy:

```js
String(spyObject);
// toString() invoked!
// "10"

spyObject + "";
// valueOf() invoked!
// "42"
```

Chà! Tôi cá là điều đó đã làm một số độc giả ngạc nhiên; bản thân tôi chắc chắn cũng từng như vậy. Mọi người khẳng định rất phổ biến rằng `String(..)` và `+ ""` là các dạng tương đương nhau để kích hoạt thao tác `ToString()`. Nhưng rõ ràng là chúng không hề như vậy!

Sự khác biệt bắt nguồn từ *hint* (gợi ý) mà mỗi thao tác cung cấp cho `ToPrimitive()`. `String(..)` rõ ràng cung cấp `"string"` làm *hint*, trong khi thành ngữ `+ ""` không cung cấp *hint* nào (tương tự như việc *gợi ý* `"number"`). Nhưng đừng bỏ lỡ chi tiết này: mặc dù `+ ""` gọi `valueOf()`, khi phương thức đó trả về một giá trị nguyên thủy `number` là `42`, giá trị đó sau đó được ép kiểu thành một chuỗi (thông qua `ToString()`), vì vậy chúng ta nhận được `"42"` thay vì `42`.

Hãy tiếp tục:

```js
Number(spyObject);
// valueOf() invoked!
// 42

+spyObject;
// valueOf() invoked!
// 42
```

Ví dụ này ngụ ý rằng `Number(..)` và toán tử một ngôi `+` đều thực hiện cùng một phép ép kiểu `ToPrimitive()` (với *hint* là `"number"`), trong trường hợp của chúng ta trả về `42`. Vì đó đã là một `number` như được yêu cầu, giá trị sẽ được xuất ra mà không cần làm gì thêm.

Nhưng điều gì sẽ xảy ra nếu một `valueOf()` trả về một `bigint`?

```js
spyObject2 = {
    valueOf() {
        console.log("valueOf() invoked!");
        return 42n;  // bigint!
    }
};

Number(spyObject2);
// valueOf() invoked!
// 42     <--- nhìn này, không phải là bigint!

+spyObject2;
// valueOf() invoked!
// TypeError: Cannot convert a BigInt value to a number
```

Chúng ta đã thấy sự khác biệt này trước đó trong phần "Ép sang Số". JS cho phép ép kiểu *tường minh* giá trị bigint `42n` thành giá trị number `42`, nhưng nó không cho phép những gì nó coi là một hình thức ép kiểu *ngầm định*.

Còn hàm ép kiểu `BigInt(..)` (không có từ khóa `new`) thì sao?

```js
BigInt(spyObject);
// valueOf() invoked!
// 42n    <--- nhìn này, là một bigint!

BigInt(spyObject2);
// valueOf() invoked!
// 42n

// *******************************

spyObject3 = {
    valueOf() {
        console.log("valueOf() invoked!");
        return 42.3;
    }
};

BigInt(spyObject3);
// valueOf() invoked!
// RangeError: The number 42.3 cannot be converted to a BigInt
```

Một lần nữa, như chúng ta đã thấy trong phần "Ép sang Số", `42` có thể được ép kiểu an toàn thành `42n`. Mặt khác, `42.3` không thể được ép kiểu an toàn thành một `bigint`.

Chúng ta đã thấy rằng `toString()` và `valueOf()` được gọi một cách đa dạng khi các phép ép kiểu `string` và `number` / `bigint` nhất định được thực hiện.

#### Không Tìm thấy Giá trị Nguyên thủy? (No Primitive Found?)

Nếu `ToPrimitive()` không tạo ra được một giá trị nguyên thủy, một ngoại lệ sẽ được ném ra:

```js
spyObject4 = {
    toString() {
        console.log("toString() invoked!");
        return [];
    },
    valueOf() {
        console.log("valueOf() invoked!");
        return {};
    }
};

String(spyObject4);
// toString() invoked!
// valueOf() invoked!
// TypeError: Cannot convert object to primitive value

Number(spyObject4);
// valueOf() invoked!
// toString() invoked!
// TypeError: Cannot convert object to primitive value
```

Nếu bạn định định nghĩa các phép ép kiểu sang nguyên thủy tùy chỉnh thông qua `toString()` / `valueOf()`, hãy đảm bảo trả về một giá trị nguyên thủy từ ít nhất một trong hai phương thức đó!

#### Ép Đối tượng sang Boolean (Object To Boolean)

Thế còn các phép ép kiểu `boolean` của đối tượng thì sao?

```js
Boolean(spyObject);
// true

!spyObject;
// false

if (spyObject) {
    console.log("if!");
}
// if!

result = spyObject ? "ternary!" : "nope";
// "ternary!"

while (spyObject) {
    console.log("while!");
    break;
}
// while!
```

Mỗi câu lệnh này đều đang kích hoạt `ToBoolean()`. Nhưng nếu bạn nhớ lại từ trước, thuật toán *đó* không bao giờ ủy quyền cho `ToPrimitive()`; do đó, chúng ta không thấy "valueOf() invoked!" được in ra console.

#### Giải gói: Từ Đối tượng Bao gói sang Nguyên thủy (Unboxing: Wrapper To Primitive)

Một dạng đối tượng đặc biệt thường xuyên được ép kiểu `ToPrimitive()`: các primitive được bao bọc/bao gói (boxed/wrapped primitives - như đã thấy trong Chương 3). Phép ép kiểu cụ thể từ đối tượng sang nguyên thủy này thường được gọi là *giải gói* (unboxing).

Hãy xem xét:

```js
hello = new String("hello");
String(hello);                  // "hello"
hello + "";                     // "hello"

fortyOne = new Number(41);
Number(fortyOne);               // 41
fortyOne + 1;                   // 42
```

Các object wrapper `hello` và `fortyOne` ở trên có các phương thức `toString()` và `valueOf()` được cấu hình trên chúng, để hoạt động tương tự như các đối tượng `spyObject` / v.v. từ các ví dụ trước của chúng ta.

Một trường hợp đặc biệt cần cẩn thận với các đối tượng bao gói primitive là với `Boolean()`:

```js
nope = new Boolean(false);
Boolean(nope);                  // true   <--- oops!
!!nope;                         // true   <--- oops!
```

Hãy nhớ rằng, điều này là do `ToBoolean()` *không* giản lược một đối tượng về dạng nguyên thủy của nó bằng `ToPrimitive`; nó chỉ đơn thuần tra cứu giá trị trong bảng nội bộ của nó, và vì các đối tượng bình thường (không dị thường[^ExoticFalsyObjects]) luôn là truthy, nên kết quả `true` được trả về.

| GHI CHÚ: |
| :--- |
| Đây là một cái bẫy nhỏ khó chịu. Chắc chắn có thể lập luận rằng `new Boolean(false)` nên tự cấu hình nội bộ như một "đối tượng mang tính falsy" dị thường (exotic falsy object).[^ExoticFalsyObjects] Thật không may, sự thay đổi đó vào thời điểm này, sau 25 năm lịch sử của JS, có thể dễ dàng gây đổ vỡ trong các chương trình. Do đó, JS đã để nguyên cạm bẫy này mà không động tới. |

#### Ghi đè `toString()` Mặc định (Overriding Default `toString()`)

Như chúng ta đã thấy, bạn luôn có thể định nghĩa một hàm `toString()` trên một đối tượng để *nó* được gọi bởi phép ép kiểu `ToPrimitive()` thích hợp. Nhưng một tùy chọn khác là ghi đè thuộc tính `Symbol.toStringTag`:

```js
spyObject5a = {};
String(spyObject5a);
// "[object Object]"
spyObject5a.toString();
// "[object Object]"

spyObject5b = {
    [Symbol.toStringTag]: "my-spy-object"
};
String(spyObject5b);
// "[object my-spy-object]"
spyObject5b.toString();
// "[object my-spy-object]"

spyObject5c = {
    get [Symbol.toStringTag]() {
        return `myValue:${this.myValue}`;
    },
    myValue: 42
};
String(spyObject5c);
// "[object myValue:42]"
spyObject5c.toString();
// "[object myValue:42]"
```

`Symbol.toStringTag` nhằm mục đích định nghĩa một giá trị chuỗi tùy chỉnh để mô tả đối tượng bất cứ khi nào thao tác `toString()` mặc định của nó được gọi trực tiếp, hoặc gián tiếp thông qua việc ép kiểu; nếu thiếu nó, giá trị được sử dụng là `"Object"` trong đầu ra phổ biến `"[object Object]"`.

Cú pháp `get ..` trong `spyObject5c` là định nghĩa một *getter*. Điều đó có nghĩa là khi JS cố gắng truy cập `Symbol.toStringTag` này như một thuộc tính (như bình thường), mã getter này sẽ làm cho hàm mà chúng ta chỉ định được gọi để tính toán kết quả. Chúng ta có thể chạy bất kỳ logic tùy ý nào bên trong getter này để xác định động một *tag* chuỗi phục vụ cho phương thức `toString()` mặc định sử dụng.

#### Ghi đè `ToPrimitive` (Overriding `ToPrimitive`)

Thay vào đó, bạn có thể ghi đè toàn bộ thao tác `ToPrimitive()` mặc định cho bất kỳ đối tượng nào, bằng cách đặt thuộc tính symbol đặc biệt `Symbol.toPrimitive` giữ một hàm:

```js
spyObject6 = {
    [Symbol.toPrimitive](hint) {
        console.log(`toPrimitive(${hint}) invoked!`);
        return 25;
    },
    toString() {
        console.log("toString() invoked!");
        return "10";
    },
    valueOf() {
        console.log("valueOf() invoked!");
        return 42;
    },
};

String(spyObject6);
// toPrimitive(string) invoked!
// "25"   <--- không phải "10"

spyObject6 + "";
// toPrimitive(default) invoked!
// "25"   <--- không phải "42"

Number(spyObject6);
// toPrimitive(number) invoked!
// 25     <--- không phải 42 hay "25"

+spyObject6;
// toPrimitive(number) invoked!
// 25
```

Như bạn có thể thấy, nếu bạn định nghĩa hàm này trên một đối tượng, nó sẽ được sử dụng hoàn toàn để thay thế cho thao tác trừu tượng `ToPrimitive()` mặc định. Vì `hint` vẫn được cung cấp cho hàm được gọi này (`[Symbol.toPrimitive](..)`), về mặt lý thuyết bạn có thể triển khai phiên bản thuật toán của riêng mình, gọi một phương thức `toString()`, `valueOf()`, hoặc bất kỳ phương thức nào khác trên đối tượng (tham chiếu ngữ cảnh `this`).

Hoặc bạn có thể chỉ cần tự định nghĩa thủ công một giá trị trả về như minh họa ở trên. Bất kể thế nào, JS sẽ *không* tự động gọi các phương thức `toString()` hay `valueOf()` nữa.

| CẢNH BÁO: |
| :--- |
| Như đã thảo luận trước đây trong mục "Không Tìm thấy Giá trị Nguyên thủy?", nếu hàm `Symbol.toPrimitive` được định nghĩa không thực sự trả về một giá trị nguyên thủy, một ngoại lệ sẽ được ném ra thông báo không thể "...convert object to primitive value". Hãy đảm bảo luôn luôn trả về một giá trị nguyên thủy thực sự từ một hàm như vậy! |

### So sánh Bằng (Equality)

Cho đến nay, các phép ép kiểu mà chúng ta đã thấy đều tập trung vào các giá trị đơn lẻ. Giờ đây chúng ta chuyển sự chú ý sang các phép so sánh bằng, vốn vốn dĩ liên quan đến hai giá trị, một trong hai hoặc cả hai đều có thể phải chịu sự ép kiểu.

Ở phần trước trong chương này, chúng ta đã nói về một số thao tác trừu tượng cho việc so sánh bằng giá trị.

Ví dụ, thao tác `SameValue()`[^SameValue] là phép so sánh bằng nghiêm ngặt nhất, hoàn toàn không có bất kỳ sự ép kiểu nào. Thao tác JS rõ ràng nhất dựa trên `SameValue()` là:

```js
Object.is(42,42);                   // true
Object.is(-0,-0);                   // true
Object.is(NaN,NaN);                 // true

Object.is(0,-0);                    // false
```

Thao tác `SameValueZero()` -- nhớ lại, nó chỉ khác với `SameValue()` ở chỗ coi `-0` và `0` là không thể phân biệt được -- được sử dụng ở khá nhiều nơi khác, bao gồm:

```js
[ 1, 2, NaN ].includes(NaN);        // true
```

Chúng ta có thể thấy sự đánh lừa giữa `0` / `-0` của `SameValueZero()` ở đây:

```js
[ 1, 2, -0 ].includes(0);           // true  <--- oops!

(new Set([ 1, 2, 0 ])).has(-0);     // true  <--- ugh

(new Map([[ 0, "ok" ]])).has(-0);   // true  <--- :(
```

Trong những trường hợp này, có một sự *ép kiểu* (theo một cách nào đó!) coi `-0` và `0` là không thể phân biệt được. Không, về mặt kỹ thuật đó không phải là một phép "ép kiểu" (coercion) vì kiểu không bị thay đổi, nhưng tôi đang tạm nới rộng định nghĩa một chút để *đưa* trường hợp này vào cuộc thảo luận rộng hơn của chúng ta về sự ép kiểu ở đây.

Hãy đối chiếu các phương thức `includes()` / `has()` ở đây (vốn kích hoạt `SameValueZero()`), với tiện ích mảng quen thuộc xưa nay `indexOf(..)`, vốn thay vào đó kích hoạt `IsStrictlyEqual()`. Thuật toán này có tính "ép kiểu" hơn một chút so với `SameValueZero()`, ở chỗ nó ngăn các giá trị `NaN` bao giờ được coi là bằng nhau:

```js
[ 1, 2, NaN ].indexOf(NaN);         // -1  <--- không tìm thấy
```

Nếu những đặc điểm kỳ quặc tinh tế này của `includes(..)` và `indexOf(..)` làm bạn bận tâm, khi tìm kiếm -- tìm kiếm sự trùng khớp bằng nhau bên trong -- một giá trị trong một mảng, bạn có thể tránh bất kỳ sự kỳ quặc mang tính "ép kiểu" nào và *bắt buộc* so khớp bằng `SameValue()` nghiêm ngặt nhất, thông qua `Object.is(..)`:

```js
vals = [ 0, 1, 2, -0, NaN ];

vals.find(v => Object.is(v,-0));            // -0
vals.find(v => Object.is(v,NaN));           // NaN

vals.findIndex(v => Object.is(v,-0));       // 3
vals.findIndex(v => Object.is(v,NaN));      // 4
```

#### Các Toán tử So sánh Bằng: `==` vs `===` (Equality Operators: `==` vs `===`)

Nơi rõ ràng nhất mà *ép kiểu* tham gia vào các phép kiểm tra bằng nhau là với toán tử `==`. Bất chấp mọi định kiến bạn có thể có về `==`, nó hoạt động cực kỳ có thể dự đoán được, đảm bảo rằng cả hai toán hạng đều khớp kiểu trước khi thực hiện kiểm tra bằng nhau.

Để khẳng định một điều có thể là siêu hiển nhiên hoặc không: các toán tử `==` (và `===`) luôn trả về một giá trị `boolean` (`true` hoặc `false`), biểu thị kết quả của việc kiểm tra bằng; chúng không bao giờ trả về bất kỳ thứ gì khác, bất kể việc ép kiểu gì có thể xảy ra.

Bây giờ, hãy nhớ lại và xem lại các bước đã thảo luận ở phần trước của chương đối với thao tác `IsLooselyEqual()`. [^LooseEquality] Hành vi của nó, và do đó là cách `==` hành xử, có thể được cảm nhận theo hướng thực dụng chỉ với hai sự thật này trong tâm trí:

1. Nếu kiểu của cả hai toán hạng là giống nhau, `==` có hành vi hoàn toàn giống hệt như `===` -- `IsLooselyEqual()` ngay lập tức ủy quyền cho `IsStrictlyEqual()`. [^StrictEquality]

    Ví dụ: khi cả hai toán hạng đều là các tham chiếu đối tượng:

    ```js
    myObj = { a: 1 };
    anotherObj = myObj;

    myObj == anotherObj;                // true
    myObj === anotherObj;               // true
    ```

    Ở đây, `==` và `===` xác định rằng cả hai toán hạng tương ứng của chúng đều thuộc kiểu tham chiếu `object`, vì vậy cả hai phép kiểm tra bằng đều hành xử giống hệt nhau; chúng so sánh các tham chiếu đối tượng xem có bằng nhau hay không.

2. Nhưng nếu các kiểu toán hạng khác nhau, `==` cho phép ép kiểu cho đến khi chúng khớp nhau, và ưu tiên so sánh số; nó cố gắng ép cả hai toán hạng thành số, nếu có thể:

    ```js
    42 == "42";                         // true
    ```

    Ở đây, chuỗi `"42"` được ép kiểu thành số `42` (chứ không phải ngược lại), và do đó phép so sánh sau đó là `42 == 42`, và rõ ràng phải trả về `true`.

Được trang bị kiến thức này, bây giờ chúng ta sẽ xua tan huyền thoại phổ biến cho rằng chỉ có `===` mới kiểm tra kiểu và giá trị, trong khi `==` chỉ kiểm tra giá trị. Không đúng chút nào!

Thực tế, `==` và `===` đều nhạy cảm với kiểu, mỗi toán tử đều kiểm tra kiểu của các toán hạng của chúng. Toán tử `==` cho phép ép kiểu các kiểu không khớp nhau, trong khi `===` không cho phép bất kỳ sự ép kiểu nào.

Có một quan điểm gần như phổ quát là nên tránh `==` để ưu tiên `===`. Tôi có thể là một trong số ít những nhà phát triển công khai ủng hộ một lập luận rõ ràng và nghiêm túc cho điều ngược lại. Tôi nghĩ lý do chính khiến mọi người thích `===` hơn, ngoài việc chỉ đơn giản là tuân theo hiện trạng (status quo), là do họ thiếu việc dành thời gian để thực sự hiểu `==`.

Tôi sẽ quay lại chủ đề này để đưa ra lý lẽ cho việc ưu tiên `==` hơn `===`, ở phần sau trong chương này, tại mục "So sánh Bằng Nhận biết Kiểu" (Type Aware Equality). Tất cả những gì tôi yêu cầu là, cho dù hiện tại bạn có kịch liệt không đồng ý với tôi đến mức nào, hãy cố gắng giữ một tâm thế cởi mở.

#### Ép kiểu Dạng Nullish (Nullish Coercion)

Chúng ta đã thấy một số thao tác JS mang tính nullish -- coi `null` và `undefined` là bằng nhau theo cách ép kiểu, bao gồm toán tử optional-chaining `?.` và toán tử nullish-coalescing `??` (xem "Null'ish" trong Chương 1).

Nhưng `==` là nơi rõ ràng nhất mà JS phơi bày phép so sánh bằng có ép kiểu dạng nullish:

```js
null == undefined;              // true
```

Cả `null` và `undefined` sẽ không bao giờ bằng nhau theo kiểu ép buộc với bất kỳ giá trị nào khác trong ngôn ngữ, ngoài việc bằng với chính đối phương. Điều đó có nghĩa là `==` làm cho việc coi hai giá trị này là không thể phân biệt trở nên rất tiện lợi (ergonomic).

Bạn có thể tận dụng khả năng này như sau:

```js
if (someData == null) {
    // `someData` là "chưa được thiết lập" (hoặc null hoặc undefined),
    // vì vậy hãy đặt nó thành một giá trị mặc định nào đó
}

// HOẶC:

if (someData != null) {
    // `someData` đã được thiết lập (không phải null cũng không phải undefined),
    // vì vậy hãy sử dụng nó theo cách nào đó
}
```

Hãy nhớ rằng `!=` là phủ định của `==`, trong khi `!==` là phủ định của `===`. Đừng đếm số lượng dấu `=` để làm khớp nhau trừ khi bạn muốn tự làm rối trí mình!

Hãy so sánh hai cách tiếp cận này:

```js
if (someData == null) {
    // ..
}

// so với:

if (someData === null || someData === undefined) {
    // ..
}
```

Cả hai câu lệnh `if` sẽ hành xử hoàn toàn giống hệt nhau. Bạn thích viết câu lệnh nào hơn, và bạn muốn đọc câu lệnh nào hơn sau này?

Công bằng mà nói, một số bạn thích phiên bản tương đương `===` dài dòng hơn. Và điều đó không sao cả. Tôi không đồng ý, tôi nghĩ phiên bản `==` của phép kiểm tra này *tốt hơn nhiều*. Và tôi cũng cho rằng phiên bản `==` nhất quán hơn về mặt tinh thần phong cách với cách các toán tử nullish khác như `?.` và `??` hành xử.

Nhưng một sự thật nhỏ khác bạn có thể cân nhắc: trong các benchmark hiệu năng mà tôi đã chạy nhiều lần, các engine JS có thể thực hiện kiểm tra đơn lẻ `== null` như đã thấy *nhanh hơn một chút* so với sự kết hợp của hai lần kiểm tra `===`. Nói cách khác, có một lợi ích nhỏ nhưng có thể đo lường được khi để cho `==` của JS thực hiện phép ép kiểu nullish *ngầm định* so với việc cố gắng tự mình liệt kê *tường minh* cả hai lần kiểm tra.

Tôi nhận thấy rằng ngay cả nhiều người hâm mộ cuồng nhiệt `===` cũng có xu hướng thừa nhận rằng `== null` ít nhất là một trường hợp như vậy mà ở đó `==` là thích hợp hơn.

#### Cạm bẫy Boolean của `==` (`==` Boolean Gotcha)

Bên cạnh một số trường hợp góc (corner cases) ép kiểu mà chúng ta sẽ giải quyết trong phần tiếp theo, có lẽ cạm bẫy lớn nhất cần lưu ý với `==` liên quan đến kiểu boolean.

Hãy chú ý rất kỹ ở đây, vì đây là một trong những lý do lớn nhất khiến mọi người bị "cắn", và sau đó trở nên căm ghét `==`. Nếu bạn làm theo lời khuyên đơn giản của tôi (ở cuối phần này), bạn sẽ không bao giờ trở thành nạn nhân!

Hãy xem xét đoạn mã sau, và hãy giả sử trong một phút rằng `isLoggedIn` *không* chứa một giá trị `boolean` (`true` hoặc `false`):

```js
if (isLoggedIn) {
    // ..
}

// so với:

if (isLoggedIn == true) {
    // ..
}
```

Chúng ta đã đề cập đến dạng câu lệnh `if` đầu tiên. Chúng ta biết `if` mong đợi một `boolean`, vì vậy trong trường hợp này `isLoggedIn` sẽ được ép kiểu thành một `boolean` bằng cách sử dụng bảng tra cứu trong thao tác trừu tượng `ToBoolean()`. Khá đơn giản để dự đoán, phải không?

Nhưng hãy nhìn vào biểu thức `isLoggedIn == true`. Bạn có nghĩ nó sẽ hành xử theo cách tương tự không?

Nếu bản năng của bạn là *có*, bạn vừa rơi vào một cái bẫy nhỏ đầy xảo quyệt. Hãy nhớ lại phần đầu chương này khi tôi cảnh báo rằng các quy tắc ép kiểu của `ToBoolean()` chỉ áp dụng nếu thao tác JS thực sự đang kích hoạt thuật toán đó. Ở đây, có vẻ như JS chắc chắn phải làm như vậy, bởi vì `== true` dường như rất rõ ràng là một kiểu so sánh "liên quan đến boolean".

Nhưng không đâu. Hãy đọc lại thuật toán `IsLooselyEqual()` (cho `==`) ở phần trước của chương. Cứ đọc đi, tôi sẽ đợi. Nếu bạn không thích bản tóm tắt của tôi, hãy đi đọc chính thuật toán trong đặc tả[^LooseEquality].

Được rồi, bạn có thấy điều gì trong đó đề cập đến việc gọi `ToBoolean()` trong bất kỳ trường hợp nào không?

Không hề!

Hãy nhớ rằng: khi kiểu của hai toán hạng `==` không giống nhau, nó thích ép cả hai thành số hơn.

Điều gì có thể nằm trong `isLoggedIn`, nếu nó không phải là một `boolean`? Chà, nó có thể là một giá trị chuỗi như `"yes"`, chẳng hạn. Ở dạng đó, `if ("yes") { .. }` rõ ràng sẽ vượt qua kiểm tra điều kiện và thực thi khối lệnh.

Nhưng điều gì sẽ xảy ra với dạng `==` của điều kiện `if`? Nó sẽ hoạt động như thế này:

```js
// (1)
"yes" == true

// (2)
"yes" == 1

// (3)
NaN == 1

// (4)
NaN === 1           // false
```

Nói cách khác, nếu `isLoggedIn` chứa một giá trị như `"yes"`, khối `if (isLoggedIn) { .. }` sẽ vượt qua kiểm tra điều kiện, nhưng kiểm tra `if (isLoggedIn == true)` thì không. Ugh!

Nếu `isLoggedIn` chứa chuỗi `"true"` thì sao?

```js
// (1)
"true" == true

// (2)
"true" == 1

// (3)
NaN == 1

// (4)
NaN === 1           // false
```

Thật muốn đập đầu vào tường (Facepalm).

Đây là một câu đố vui: `isLoggedIn` cần giữ giá trị nào để cả hai dạng điều kiện câu lệnh `if` đều vượt qua?

...

...

...

...

Điều gì sẽ xảy ra nếu `isLoggedIn` đang giữ số `1`? `1` là truthy, vì vậy dạng `if (isLoggedIn)` sẽ vượt qua. Và dạng `==` khác có liên quan đến việc ép kiểu:

```js
// (1)
1 == true

// (2)
1 == 1

// (3)
1 === 1             // true
```

Nhưng nếu thay vào đó `isLoggedIn` lại giữ chuỗi `"1"`? Một lần nữa, `"1"` là truthy, nhưng còn việc ép kiểu của `==` thì sao?

```js
// (1)
"1" == true

// (2)
"1" == 1

// (3)
1 == 1

// (4)
1 === 1             // true
```

Được rồi, vậy `1` và `"1"` là hai giá trị mà `isLoggedIn` có thể chứa để an toàn khi ép kiểu cùng với `true` trong phép kiểm tra bằng nhau `==`. Nhưng về cơ bản hầu như không có giá trị nào khác an toàn để `isLoggedIn` nắm giữ cả.

Chúng ta gặp phải một cạm bẫy tương tự nếu phép kiểm tra là `== false`. Những giá trị nào an toàn trong một phép so sánh như vậy? `""` và `0` hoạt động được. Nhưng:

```js
if ([] == false) {
    // cái này sẽ chạy!
}
```

`[]` là một giá trị truthy, nhưng nó cũng bằng `false` theo kiểu ép buộc?! Đau đớn thật.

Chúng ta rút ra được điều gì từ những cạm bẫy này với các phép kiểm tra `== true` và `== false`? Tôi có một câu trả lời đơn giản và thẳng thắn.

Không bao giờ, không bao giờ, trong bất kỳ trường hợp nào, thực hiện kiểm tra `==` nếu một trong hai vế của phép so sánh là giá trị `true` hoặc `false`. Nhìn bề ngoài có vẻ như nó sẽ hoạt động như một phép ép kiểu `ToBoolean()` êm đẹp, nhưng một cách lén lút nó sẽ không làm vậy, và thay vào đó sẽ bị mắc kẹt vào một loạt các trường hợp góc của ép kiểu (sẽ được đề cập trong phần tiếp theo). Và cũng hãy tránh cả các dạng `===` nữa.

Khi bạn đang xử lý các giá trị boolean, hãy gắn bó với các dạng ép kiểu ngầm định thực sự kích hoạt `ToBoolean()`, chẳng hạn như `if (isLoggedIn)`, và tránh xa các dạng `==` / `===`.

## Các Trường hợp Góc của Ép kiểu (Coercion Corner Cases)

Tôi đã nói rất rõ ràng trong việc bày tỏ quan điểm ủng hộ ép kiểu của mình từ đầu đến giờ. Và đó *đích thực* chỉ là một quan điểm, mặc dù nó dựa trên việc diễn giải các sự thật thu thập được từ việc nghiên cứu đặc tả ngôn ngữ và các hành vi có thể quan sát được của JS.

Điều đó không có nghĩa là việc ép kiểu là hoàn hảo. Có một số trường hợp góc gây khó chịu mà chúng ta cần nhận thức được, để tránh vấp phải những ổ gà đó. Trong trường hợp chưa rõ ràng, những mô tả sau đây của tôi về các trường hợp góc này chỉ là những ý kiến cá nhân khác của tôi. Trải nghiệm thực tế của bạn có thể khác biệt.

### Chuỗi (Strings)

Chúng ta đã thấy rằng việc ép kiểu chuỗi của một mảng trông như thế này:

```js
String([ 1, 2, 3 ]);                // "1,2,3"
```

Cá nhân tôi thấy điều đó siêu khó chịu, vì nó không bao gồm các dấu `[ ]` bao quanh. Cụ thể, điều đó dẫn đến sự phi lý này:

```js
String([]);                         // ""
```

Vậy là chúng ta thậm chí không thể biết nó có phải là một mảng hay không, bởi vì tất cả những gì chúng ta nhận được chỉ là một chuỗi rỗng? Tuyệt vời thật đấy, JS. Điều đó thật ngớ ngẩn. Xin lỗi, nhưng đúng là như vậy. Và nó còn tệ hơn:

```js
String([ null, undefined ]);        // ","
```

CÁI GÌ CƠ!? (WAT!?) Chúng ta biết rằng `null` ép kiểu thành chuỗi `"null"`, và `undefined` ép kiểu thành chuỗi `"undefined"`. Nhưng nếu những giá trị đó nằm trong một mảng, chúng chỉ biến mất một cách kỳ diệu thành các chuỗi rỗng trong quá trình ép kiểu từ mảng sang chuỗi. Chỉ có dấu `","` còn sót lại để thậm chí gợi ý cho chúng ta rằng đã có thứ gì đó trong mảng! Đúng là chuyện nực cười, ngay tại đây.

Thế còn đối tượng thì sao? Gần như gây bực bội không kém, mặc dù theo hướng ngược lại:

```js
String({});                         // "[object Object]"

String({ a: 1 });                   // "[object Object]"
```

Umm... Được rồi. Chắc chắn rồi, cảm ơn JS vì đã hoàn toàn không giúp ích được chút nào trong việc hiểu giá trị đối tượng là gì.

### Số (Numbers)

Tôi chuẩn bị tiết lộ điều mà tôi nghĩ là cội nguồn tồi tệ *nhất* của mọi tội lỗi về các trường hợp góc của ép kiểu. Bạn đã sẵn sàng cho nó chưa?!?

```js
Number("");                         // 0
Number("       ");                  // 0
```

Tôi vẫn đang lắc đầu ngao ngán trước điều này, dù tôi đã biết về nó gần 20 năm rồi. Tôi vẫn không hiểu Brendan đã nghĩ gì với trường hợp này.

Chuỗi rỗng hoàn toàn không có bất kỳ nội dung nào; nó không có gì trong đó để xác định một biểu diễn số. `0` tuyệt đối ***KHÔNG PHẢI*** là giá trị số tương đương của một giá trị số bị thiếu/không hợp lệ. Bạn biết chúng ta có giá trị số nào rất phù hợp để truyền đạt điều đó không? `NaN`. Đừng để tôi bắt đầu nói về việc khoảng trắng bị tước bỏ khỏi các chuỗi khi ép kiểu sang số như thế nào, để rồi chuỗi rất-không-hề-rỗng `"       "` vẫn được xử lý giống như `""` cho các mục đích ép kiểu số.

Tệ hơn nữa, hãy nhớ lại cách `[]` ép kiểu thành chuỗi `""` chứ? Hệ quả kéo theo:

```js
Number([]);                         // 0
```

Trời đất ơi (Doh)! Nếu `""` không ép kiểu thành `0` -- hãy nhớ, đây là cội nguồn của mọi tội lỗi ép kiểu! -- thì `[]` cũng sẽ không ép kiểu thành `0`.

Đây thực sự là vùng đất của sự ngớ ngẩn, của một vũ trụ đảo lộn.

Trường hợp sau thuần hóa hơn nhiều, nhưng vẫn hơi khó chịu:

```js
Number("NaN");                      // NaN  <--- ngẫu nhiên/vô tình!

Number("Infinity");                 // Infinity
Number("infinity");                 // NaN  <--- oops, chú ý chữ hoa/thường!
```

Chuỗi `"NaN"` không được phân tích cú pháp như một giá trị số có thể nhận dạng, vì vậy phép ép kiểu thất bại, tạo ra (một cách vô tình!) giá trị `NaN`. `"Infinity"` có thể phân tích cú pháp rõ ràng cho việc ép kiểu, nhưng bất kỳ cách viết hoa thường nào khác, bao gồm cả `"infinity"`, sẽ thất bại, một lần nữa tạo ra `NaN`.

Ví dụ tiếp theo này, bạn có thể không nghĩ nó là một trường hợp góc chút nào:

```js
Number(false);                      // 0
Number(true);                       // 1
```

Đó chỉ đơn thuần là quy ước của lập trình viên, di sản từ các ngôn ngữ vốn ban đầu không có các giá trị boolean `true` và `false`, mà chúng ta coi `0` là `false`, và `1` là `true`. Nhưng liệu việc đi theo hướng ngược lại có *thực sự* hợp lý không?

Hãy nghĩ về nó theo cách này:

```js
false + true + false + false + true;        // 2
```

Thật sự đấy à? Tôi không nghĩ có bất kỳ trường hợp nào mà việc coi một `boolean` tương đương với giá trị `number` của nó lại mang lại bất kỳ ý nghĩa hợp lý nào trong một chương trình. Tôi có thể hiểu chiều ngược lại, vì lý do lịch sử: `Boolean(0)` và `Boolean(1)`.

Nhưng tôi thực sự cảm thấy rằng `Number(false)` và `Number(true)` (cũng như bất kỳ hình thức ép kiểu ngầm định nào) nên tạo ra `NaN`, chứ không phải `0` / `1`.

### Sự Phi lý của Ép kiểu (Coercion Absurdity)

Để chứng minh quan điểm của mình, hãy đẩy sự phi lý lên cấp độ 11:

```js
[] == ![];                          // true
```

Làm thế nào được!? Điều đó dường như vượt quá sức tin tưởng rằng một giá trị có thể bằng chính sự phủ định của nó theo kiểu ép buộc, đúng không!?

Nhưng hãy lần theo "hang thỏ" của các bước ép kiểu:

1. `[] == ![]`
2. `[] == false`
3. `"" == false`
4. `0 == false`
5. `0 == 0`
6. `0 === 0`  ->  `true`

Chúng ta có ba điều phi lý khác nhau cùng "cấu kết" chống lại chúng ta: `String([])`, `Number("")`, và `Number(false)`; nếu bất kỳ điều nào trong số này không phải là sự thật, kết quả trường hợp góc vô nghĩa này sẽ không xảy ra.

Tuy nhiên, hãy để tôi làm rõ một điều tuyệt đối: không có điều nào trong số này là lỗi của `==`. Tất nhiên nó bị đổ lỗi ở đây. Nhưng thủ phạm thực sự chính là các trường hợp góc của `string` và `number` ở bên dưới.

## Nhận thức về Kiểu (Type Awareness)

Bây giờ chúng ta đã mổ xẻ, phân tích và xem xét việc ép kiểu từ mọi góc độ có thể tưởng tượng được, bắt đầu từ các cơ chế nội bộ trừu tượng của đặc tả, sau đó chuyển sang các biểu thức và câu lệnh cụ thể thực sự kích hoạt các phép ép kiểu.

Nhưng mục đích của tất cả những điều này là gì? Liệu chi tiết trong chương này, và quả thực là toàn bộ cuốn sách này cho đến thời điểm hiện tại, phần lớn chỉ là những kiến thức vụn vặt (trivia)? Ồ, tôi không nghĩ vậy.

Hãy quay trở lại các quan sát/câu hỏi mà tôi đã đặt ra ở phần đầu của chương dài này.

Không thiếu các quan điểm (đặc biệt là tiêu cực) về việc ép kiểu. Quan điểm gần như phổ quát là ép kiểu phần lớn/hoàn toàn là một *phần tồi tệ* trong thiết kế ngôn ngữ của JS. Nhưng bất chấp thực tế đó, hầu như mọi nhà phát triển, trong hầu hết mọi chương trình JS từng được viết ra, đều phải đối mặt với thực tế là không thể tránh khỏi việc ép kiểu.

Nói cách khác, dù bạn làm gì đi nữa, bạn cũng sẽ không thể thoát khỏi sự cần thiết phải nhận thức được, thấu hiểu và quản lý các kiểu giá trị của JS cũng như sự chuyển đổi giữa chúng. Trái ngược với những giả định phổ biến, việc đón nhận một ngôn ngữ định kiểu động (hoặc thậm chí định kiểu yếu) *không* đồng nghĩa với việc cẩu thả hoặc không để tâm đến các kiểu dữ liệu.

Lập trình nhận thức về kiểu (Type-aware programming) luôn luôn, luôn luôn tốt hơn lập trình phớt lờ/không quan tâm đến kiểu (type ignorant/agnostic programming).

### Uhh... Thế còn TypeScript? (Uhh... TypeScript?)

Chắc chắn ngay lúc này bạn đang nghĩ: "Tại sao tôi không thể chỉ dùng TypeScript và khai báo tất cả các kiểu của mình một cách tĩnh, tránh mọi sự nhầm lẫn của việc định kiểu động và ép kiểu?"

| GHI CHÚ: |
| :--- |
| Tôi có nhiều suy nghĩ chi tiết hơn về TypeScript và vai trò lớn hơn mà nó đóng góp trong hệ sinh thái của chúng ta; tôi sẽ dành những quan điểm đó cho phần phụ lục ("Suy nghĩ về TypeScript"). |

Hãy bắt đầu bằng cách giải quyết trực diện những cách mà TypeScript hỗ trợ, và không hỗ trợ, trong lập trình nhận thức về kiểu, như tôi đang chủ trương.

TypeScript vừa **được định kiểu tĩnh** (statically-typed - nghĩa là các kiểu được khai báo tại thời điểm viết mã và được kiểm tra tại thời điểm biên dịch) vừa **được định kiểu mạnh** (strongly-typed - nghĩa là các biến/vùng chứa được gắn kiểu, và các mối liên kết này được thực thi nghiêm ngặt; các hệ thống định kiểu mạnh cũng không cho phép ép kiểu *ngầm định*). Điểm mạnh lớn nhất của TypeScript là nó thường buộc cả người viết mã lẫn người đọc mã phải đối mặt với các kiểu cấu thành nên hầu hết (lý tưởng nhất là tất cả!) của một chương trình. Đó chắc chắn là một điều tốt.

Ngược lại, JS **được định kiểu động** (dynamically-typed - nghĩa là các kiểu được phát hiện và quản lý hoàn toàn tại thời điểm chạy) và **được định kiểu yếu** (weakly-typed - nghĩa là các biến/vùng chứa không được gắn kiểu, do đó không có mối liên kết nào để thực thi, và các biến do đó có thể chứa bất kỳ kiểu giá trị nào; các hệ thống định kiểu yếu cho phép bất kỳ hình thức ép kiểu nào).

| GHI CHÚ: |
| :--- |
| Tôi đang khái quát hóa ở mức độ khá cao ở đây, và cố ý không đi sâu vào nhiều sắc thái tinh tế trên các dải quang phổ định kiểu tĩnh/động và mạnh/yếu. Nếu bạn đang cảm thấy muốn nói "À thực ra thì..." (Well, actually...) với tôi lúc này, xin vui lòng kiềm lại một chút và để tôi trình bày các luận điểm của mình. |

### Nhận thức về Kiểu *Không cần* TypeScript (Type-Awareness *Without* TypeScript)

Liệu một hệ thống định kiểu động có tự động đồng nghĩa với việc bạn đang lập trình với ít sự nhận thức về kiểu hơn không? Nhiều người sẽ lập luận như vậy, nhưng tôi không đồng ý.

Tôi hoàn toàn không nghĩ rằng việc khai báo các kiểu tĩnh (các chú thích - annotations, như trong TypeScript) là cách duy nhất để đạt được nhận thức về kiểu hiệu quả. Tuy nhiên, rõ ràng là những người ủng hộ việc định kiểu tĩnh tin rằng đó là cách *tốt nhất*.

Hãy để tôi minh họa nhận thức về kiểu mà không cần kiểu tĩnh của TypeScript. Hãy xem xét khai báo biến này:

```js
let API_BASE_URL = "https://some.tld/api/2";
```

Câu lệnh đó có *nhận thức về kiểu* theo bất kỳ cách nào không? Chắc chắn rồi, không có chú thích `: string` nào đằng sau `API_BASE_URL`. Nhưng tôi chắc chắn nghĩ rằng nó *vẫn* nhận thức về kiểu! Chúng ta thấy rõ kiểu giá trị (`string`) của giá trị đang được gán cho `API_BASE_URL`.

| CẢNH BÁO: |
| :--- |
| Đừng bị phân tâm bởi việc khai báo `let` có thể gán lại được (trái ngược với `const`). `const` của JS *không phải* là một tính năng hạng nhất của hệ thống kiểu của nó. Chúng ta không thực sự có thêm nhận thức về kiểu đơn giản chỉ vì chúng ta biết rằng việc gán lại một biến `const` bị engine JS cấm. Nếu mã được cấu trúc tốt -- e hèm, được cấu trúc với sự nhận thức về kiểu như một ưu tiên hàng đầu -- chúng ta chỉ cần đọc mã và thấy rõ rằng `API_BASE_URL` *không* bị gán lại và do đó vẫn là kiểu giá trị mà nó đã được gán trước đó. Từ góc độ nhận thức về kiểu, điều đó về mặt thực tế hoàn toàn tương đương với việc nó *không thể* bị gán lại. |

Nếu sau này tôi muốn làm điều gì đó như:

```js
// chúng ta có đang sử dụng URL API bảo mật không?
isSecureAPI = /^https/.test(API_BASE_URL);
```

Tôi biết phương thức biểu thức chính quy `test(..)` mong đợi một chuỗi, và vì tôi biết `API_BASE_URL` đang chứa một chuỗi, nên tôi biết thao tác đó là an toàn về kiểu (type-safe).

Tương tự, vì tôi biết các quy tắc đơn giản của phép ép kiểu `ToBoolean()` khi nó liên quan đến các giá trị chuỗi, tôi biết loại câu lệnh này cũng an toàn về kiểu:

```js
// chúng ta đã xác định được API URL chưa?
if (API_BASE_URL) {
    // ..
}
```

Nhưng nếu sau đó, tôi bắt đầu gõ một cái gì đó như thế này:

```js
APIVersion = Number(API_BASE_URL);
```

Một tiếng còi cảnh báo sẽ reo lên trong đầu tôi. Vì tôi biết có một số quy tắc rất cụ thể về cách các giá trị chuỗi ép kiểu thành số, tôi nhận ra rằng thao tác này **không** an toàn về kiểu. Vì vậy thay vào đó tôi tiếp cận nó theo cách khác:

```js
// lấy ra số phiên bản từ API URL
versionDigit = API_BASE_URL.match(/\/api\/(\d+)$/)[1];

// đảm bảo phiên bản thực sự là một số
APIVersion = Number(versionDigit);
```

Tôi biết rằng `API_BASE_URL` là một chuỗi, và tôi biết thêm định dạng nội dung của nó bao gồm `".../api/{digits}"` ở cuối. Điều đó cho tôi biết rằng phép so khớp biểu thức chính quy sẽ thành công, vì vậy việc truy cập mảng `[1]` là an toàn về kiểu.

Tôi cũng biết rằng `versionDigit` sẽ chứa một chuỗi, bởi vì đó là những gì các phép khớp biểu thức chính quy trả về. Bây giờ, tôi biết việc ép chuỗi ký tự số đó thành một số bằng `Number(..)` là an toàn.

Theo định nghĩa của tôi, kiểu tư duy đó, và phong cách viết mã đó, chính là nhận thức về kiểu. Nhận thức về kiểu trong lập trình có nghĩa là suy nghĩ cẩn thận về việc liệu những điều như vậy có *rõ ràng* và *hiển nhiên* đối với người đọc mã hay không.

### Nhận thức về Kiểu *Cùng với* TypeScript (Type-Awareness *With* TypeScript)

Những người hâm mộ TypeScript sẽ chỉ ra rằng TypeScript có thể, thông qua suy luận kiểu (type inference), thực hiện định kiểu tĩnh (thực thi) mà không cần phải có một chú thích kiểu nào trong chương trình. Vì vậy tất cả các ví dụ mã tôi đã chia sẻ trong phần trước, TypeScript cũng có thể xử lý và cung cấp phong cách thực thi kiểu tĩnh tại thời điểm biên dịch của nó.

Nói cách khác, TypeScript sẽ mang lại cho chúng ta cùng một loại lợi ích trong việc kiểm tra kiểu, bất kể chúng ta viết cách nào trong hai cách này:

```ts
let API_BASE_URL: string = "https://some.tld/api/2";

// so với:

let API_BASE_URL = "https://some.tld/api/2";
```

Nhưng không có bữa trưa nào là miễn phí cả. Chúng ta có một số vấn đề cần phải đối mặt. Trước hết, TypeScript *không* kích hoạt lỗi ở đây:

```js
API_BASE_URL = "https://some.tld/api/2";

APIVersion = Number(API_BASE_URL);
// NaN
```

Theo trực giác, *tôi* muốn một hệ thống nhận thức về kiểu hiểu được lý do tại sao điều đó là không an toàn. Nhưng có lẽ điều đó đòi hỏi quá nhiều chăng. Hoặc có lẽ nếu chúng ta thực sự định nghĩa một kiểu hẹp hơn/cụ thể hơn cho biến `API_BASE_URL` đó, thay vì chỉ đơn giản là `string`, nó có thể giúp ích? Chúng ta có thể sử dụng một mẹo của TypeScript gọi là "Template Literal Types": [^TSLiteralTypes]

```ts
type VersionedURL = `https://some.tld/api/${number}`;

API_BASE_URL: VersionedURL = "https://some.tld/api/2";

APIVersion = Number(API_BASE_URL);
// NaN
```

Không, TypeScript vẫn không thấy có vấn đề gì với điều đó. Vâng, tôi biết có một lời giải thích cho lý do tại sao (cách mà bản thân `Number(..)` được gắn kiểu).

| GHI CHÚ: |
| :--- |
| Tôi tưởng tượng những người thực sự thông minh, những người *thấu hiểu* tường tận TypeScript có những ý tưởng sáng tạo về cách chúng ta có thể uốn mình để đưa ra cảnh báo lỗi ở đó. Có thể thậm chí có hàng tá cách khác nhau để buộc TypeScript kích hoạt lỗi trên mã đó. Nhưng đó không thực sự là vấn đề mấu chốt. |

Ý tôi là, chúng ta không thể hoàn toàn dựa dẫm vào các kiểu của TypeScript để giải quyết mọi vấn đề của mình, cho phép chúng ta buông xuôi và hoàn toàn không để tâm đến những sắc thái tinh tế của các kiểu và, trong trường hợp này, các hành vi ép kiểu.

Nhưng! Chắc chắn bạn đang phản đối dòng lập luận này, tha thiết khẳng định rằng ngay cả khi TypeScript không thể hiểu một tình huống cụ thể nào đó, thì chắc chắn việc sử dụng TypeScript cũng đâu làm cho nó *tồi tệ hơn*! Đúng không!?

Hãy xem TypeScript nói gì[^TSExample1] về dòng này:

```ts
type VersionedURL = `https://some.tld/api/${number}`;

let API_BASE_URL: VersionedURL = "https://some.tld/api/2";

let versionDigit = API_BASE_URL.match(/\/api\/(\d+)$/)[1];
// Object is possibly 'null'.
```

Lỗi chỉ ra rằng truy cập `[1]` không an toàn về kiểu, bởi vì nếu biểu thức chính quy không tìm thấy bất kỳ kết quả khớp nào trên chuỗi, `match(..)` sẽ trả về `null`.

Bạn thấy đấy, mặc dù *tôi* có thể lập luận về nội dung của chuỗi so với cách biểu thức chính quy được viết, và ngay cả khi *tôi* đã cất công làm cho TypeScript thấy siêu rõ ràng chính xác nội dung chuỗi cụ thể đó là gì, nó vẫn chưa đủ thông minh để kết nối hai điều đó lại với nhau nhằm thấy rằng việc giả định kết quả khớp xảy ra thực sự là hoàn toàn an toàn về kiểu.

| MẸO: |
| :--- |
| Liệu việc bị uốn éo để thể hiện mọi sắc thái nhỏ nhất có thể có của sự an toàn kiểu có thực sự là nhiệm vụ, và cách sử dụng tốt nhất của một công cụ nhận thức về kiểu không? Chúng ta không cần những công cụ hoàn hảo và phổ quát để thu được lượng lợi ích to lớn từ những thứ mà chúng *có thể* làm. |

Hơn nữa, khi so sánh phong cách mã trong phần trước với mã trong phần này (có hoặc không có chú thích), liệu TypeScript có thực sự làm cho việc viết mã của chúng ta có nhận thức về kiểu hơn không?

Chẳng hạn như, liệu những thứ như `type VersionedURL = ..` và `API_BASE_URL: VersionedURL` đó có *thực sự* làm cho mã của chúng ta nhận thức về kiểu rõ ràng hơn không? Tôi không nhất thiết nghĩ như vậy.

### Trí tuệ của TypeScript (TypeScript Intelligence)

Vâng, tôi nghe thấy bạn đang hét vào mặt tôi qua màn hình máy tính. Vâng, tôi biết rằng TypeScript cung cấp những thông tin kiểu mà nó phát hiện (hoặc suy luận) cho trình soạn thảo mã của bạn, những thứ xuất hiện dưới dạng tự động hoàn thành thông minh (autocomplete), các điểm đánh dấu cảnh báo nội dòng hữu ích, v.v.

Nhưng tôi đang lập luận rằng ngay cả *những thứ đó*, tự bản thân chúng, cũng không làm cho bạn trở nên có nhận thức về kiểu hơn với tư cách là một nhà phát triển.

Tại sao? Bởi vì nhận thức về kiểu *không* chỉ là về trải nghiệm viết mã (authoring experience). Nó còn là về trải nghiệm đọc mã (reading experience), thậm chí có thể còn nhiều hơn thế. Và không phải tất cả các nơi/cơ chế mà mã được đọc đều có quyền truy cập để hưởng lợi từ tất cả trí thông minh bổ sung đó.

Nghe này, sự kỳ diệu của một language-server bơm trí thông minh vào trình soạn thảo mã của bạn chắc chắn là điều đáng kinh ngạc. Nó tuyệt vời và siêu hữu ích.

Và tôi không hề bài xích TypeScript như một công cụ suy luận những điều về **mã JS** của tôi và đưa ra cho tôi những gợi ý và đề xuất thông qua các tích hợp trình soạn thảo mã thú vị. Tôi chỉ không nhất thiết muốn *phải* chú thích thông tin kiểu theo một cách cực kỳ cụ thể nào đó chỉ để dập tắt những lời phàn nàn của công cụ.

### Tiêu chuẩn Nằm trên TypeScript (The Bar Above TypeScript)

Nhưng ngay cả khi tôi đã làm/có tất cả những thứ đó, nó vẫn chưa ***đủ*** để tôi hoàn toàn có nhận thức về kiểu, cả với tư cách là một người viết mã lẫn một người đọc mã.

Những công cụ này không bắt được mọi lỗi kiểu có thể xảy ra, bất kể chúng ta muốn tự nhủ rằng chúng có thể làm được điều đó đến mức nào, và bất kể chúng ta phải chịu đựng bao nhiêu vòng lặp và sự uốn éo để mong muốn điều đó trở thành hiện thực. Tất cả những nỗ lực để dỗ dành và *ép* một công cụ bắt được những lỗi tinh vi đó, thông qua sự phức tạp ngày càng tăng vô tận của các mẹo cú pháp kiểu, nhiều nhất cũng chỉ là... nỗ lực đặt nhầm chỗ.

Hơn nữa, không có công cụ nào như vậy miễn nhiễm với các trường hợp dương tính giả (false positives), phàn nàn về những thứ thực sự không phải là lỗi; những công cụ này sẽ không bao giờ thông minh như con người chúng ta. Bạn đang thực sự lãng phí thời gian của mình trong việc đuổi theo một số mẹo cú pháp kỳ quặc chỉ để làm dịu đi những lời phàn nàn của công cụ.

Đơn giản là không có gì thay thế được, nếu bạn muốn thực sự là một người viết mã và người đọc mã có nhận thức về kiểu, ngoài việc học cách hoạt động của các hệ thống kiểu tích hợp sẵn của ngôn ngữ. Và vâng, điều đó có nghĩa là từng nhà phát triển trong nhóm của bạn cần phải bỏ công sức ra để học nó. Bạn không thể giảm nhẹ những thứ này chỉ để làm cho nó dễ tiếp cận hơn đối với những nhà phát triển ít kinh nghiệm hơn trong dự án/nhóm.

Ngay cả khi chúng tôi chấp nhận rằng bạn có thể tránh được 100% tất cả các phép ép kiểu *ngầm định* -- bạn không thể đâu -- bạn chắc chắn sẽ phải đối mặt với sự cần thiết của các phép ép kiểu *tường minh* -- mọi chương trình đều như vậy!

Và nếu phản ứng của bạn đối với thực tế đó là đề xuất rằng bạn sẽ chỉ cần trút bỏ gánh nặng tinh thần của việc thấu hiểu chúng cho một công cụ như TypeScript... thì tôi rất tiếc phải nói với bạn, nhưng bạn đang rõ ràng và đau đớn tụt lại phía dưới tiêu chuẩn *nhận thức về kiểu* mà tôi đang thách thức tất cả các nhà phát triển nỗ lực hướng tới.

Ở đây, tôi không hề cổ súy việc bạn từ bỏ TypeScript. Nếu bạn thích nó, tốt thôi. Nhưng tôi đang thách thức bạn một cách rất rõ ràng và đầy nhiệt huyết: hãy ngừng sử dụng TypeScript như một chiếc nạng chống. Hãy ngừng quỳ gối để xoa dịu những kẻ thống trị là TypeScript engine. Hãy ngừng việc dại dột đuổi theo mọi con thỏ kiểu dữ liệu xuống từng cái hang cú pháp.

Theo quan sát của tôi, có một mối quan hệ nghịch đảo đầy bi kịch giữa việc sử dụng các công cụ nhận biết kiểu (như TypeScript) và mong muốn/nỗ lực theo đuổi nhận thức về kiểu thực sự với tư cách là một người viết mã và người đọc mã. Bạn càng dựa dẫm vào TypeScript, dường như bạn càng bị cám dỗ và khuyến khích chuyển sự chú ý của mình ra khỏi hệ thống kiểu của JS (và đặc biệt là khỏi việc ép kiểu) sang hệ thống kiểu thay thế của TypeScript.

Thật không may, TypeScript không bao giờ có thể thoát ly hoàn toàn khỏi hệ thống kiểu của JS, bởi vì các kiểu của TypeScript bị trình biên dịch *xóa bỏ* (erased), và những gì còn lại chỉ là JS thuần túy mà JS engine phải đối phó.

| MẸO: |
| :--- |
| Hãy tưởng tượng nếu ai đó đưa cho bạn một cốc nước đã lọc để uống. Và ngay trước khi bạn nhấp một ngụm, họ nói: "Chúng tôi đã lấy nguồn nước đó từ lòng đất gần một bãi rác thải. Nhưng đừng lo, chúng tôi đã sử dụng một bộ lọc cực kỳ tuyệt vời, và nước đó hoàn toàn an toàn!" Bạn tin tưởng bộ lọc đó đến mức nào? Nói đúng hơn vào quan điểm bao quát của tôi, chẳng phải bạn sẽ cảm thấy thoải mái hơn khi uống cốc nước đó nếu bạn hiểu rõ mọi thứ về nguồn nước, tất cả các quy trình lọc, và tất cả những gì *có trong* nước của chiếc cốc trên tay bạn hay sao!? Hay chỉ việc tin tưởng vào bộ lọc đó là đã đủ tốt rồi? |

### So sánh Bằng Nhận biết Kiểu (Type Aware Equality)

Tôi sẽ khép lại chương dài và quanh co này bằng một minh họa cuối cùng, mô hình hóa cách mà tôi nghĩ các nhà phát triển nên -- được trang bị tư duy phản biện nhiều hơn là sự a dua theo trào lưu -- tiếp cận việc viết mã nhận thức về kiểu, cho dù bạn có sử dụng một công cụ như TypeScript hay không.

Chúng ta sẽ một lần nữa xem xét lại các phép so sánh bằng (`==` vs `===`), từ góc độ nhận thức về kiểu. Trước đó trong chương này, tôi đã hứa rằng tôi sẽ đưa ra lập luận ủng hộ `==` hơn `===`, vì vậy đây chính là lúc thực hiện điều đó.

Hãy cùng nhắc lại/tóm tắt những gì chúng ta đã biết về `==` và `===` cho đến nay:

1. Nếu kiểu của các toán hạng đối với `==` khớp nhau, nó hành xử *hoàn toàn giống hệt* như `===`.

2. Nếu kiểu của các toán hạng đối với `===` không khớp nhau, nó sẽ luôn luôn trả về `false`.

3. Nếu kiểu của các toán hạng đối với `==` không khớp nhau, nó sẽ cho phép ép kiểu một trong hai toán hạng (nhìn chung ưu tiên các giá trị kiểu số), cho đến khi các kiểu cuối cùng khớp nhau; một khi chúng khớp nhau, hãy xem (1).

Được rồi, bây giờ hãy lấy những sự thật đó và phân tích xem chúng có thể tương tác như thế nào trong chương trình của chúng ta.

Nếu bạn đang thực hiện một phép so sánh bằng giữa `x` và `y` như thế này:

```js
if ( /* x và y có bằng nhau không */ ) {
    // ..
}
```

Những điều kiện có thể xảy ra mà chúng ta có thể gặp phải, xét về kiểu của `x` và `y`, là gì?

1. Chúng ta có thể biết chính xác kiểu (các kiểu) mà `x` và `y` có thể là, bởi vì chúng ta biết những biến đó đang được gán giá trị như thế nào.

2. Hoặc chúng ta có thể không thể biết những kiểu đó có thể là gì. Có thể là `x` hoặc `y` có thể là bất kỳ kiểu nào, hoặc ít nhất là bất kỳ kiểu nào trong số nhiều kiểu khác nhau, đến mức các sự kết hợp kiểu có thể có trong phép so sánh quá phức tạp để hiểu/dự đoán.

Chúng ta có đồng ý rằng (1) là thích hợp hơn nhiều so với (2) không? Chúng ta có đồng ý thêm rằng (1) đại diện cho việc chúng ta đã viết mã theo phong cách nhận thức về kiểu, trong khi (2) đại diện cho mã dứt khoát là *không nhận thức* về kiểu không?

Nếu bạn đang sử dụng TypeScript, bạn rất có thể nhận thức được kiểu của `x` và `y`, đúng không? Ngay cả khi bạn không sử dụng TypeScript, chúng tôi đã chỉ ra rằng bạn có thể thực hiện các bước có chủ ý để viết mã của mình theo cách mà kiểu của `x` và `y` được nhận biết và trở nên hiển nhiên.

#### (2) Các Kiểu Không Xác định (Unknown Types)

Nếu bạn đang ở trong kịch bản (2), tôi xin khẳng định rằng mã của bạn đang ở trong trạng thái có vấn đề. Mã của bạn chưa đạt mức lý tưởng. Mã của bạn cần được tái cấu trúc (refactored). Điều tốt nhất nên làm, nếu bạn tìm thấy mã ở trạng thái này, là... sửa nó!

Hãy thay đổi mã để nó có nhận thức về kiểu. Nếu điều đó có nghĩa là sử dụng TypeScript, và thậm chí chèn thêm một số chú thích kiểu, hãy làm như vậy. Hoặc nếu bạn cảm thấy mình có thể đạt được trạng thái nhận thức về kiểu với *chỉ JS thuần*, hãy làm điều đó. Dù bằng cách nào, hãy làm bất cứ điều gì bạn có thể để chuyển sang kịch bản (1).

Nếu bạn không thể đảm bảo mã thực hiện phép so sánh bằng này giữa `x` và `y` có nhận thức về kiểu, và bạn không còn lựa chọn nào khác, thì bạn tuyệt đối *phải* sử dụng toán tử bằng nghiêm ngặt `===`. Không làm như vậy sẽ là cực kỳ vô trách nhiệm.

```js
if (x === y) {
    // ..
}
```

Nếu bạn không biết gì về các kiểu, làm sao bạn (hoặc bất kỳ người đọc mã nào khác trong tương lai) có thể có bất kỳ khái niệm nào về việc các bước ép kiểu trong `==` sẽ hành xử ra sao!? Bạn không thể biết được.

Điều có trách nhiệm duy nhất cần làm là, tránh việc ép kiểu và sử dụng `===`.

Nhưng đừng đánh mất sự thật này: bạn chỉ chọn `===` như một giải pháp cuối cùng, khi mã của bạn quá thiếu nhận thức về kiểu -- e hèm, bị hỏng về kiểu! -- đến mức không còn lựa chọn nào khác.

#### (1) Các Kiểu Đã Biết (Known Types)

Được rồi, thay vào đó hãy giả sử bạn đang ở trong kịch bản (1). Bạn biết các kiểu của `x` và `y`. Rất rõ ràng trong mã rằng tập hợp các kiểu hạn hẹp này tham gia vào việc kiểm tra bằng nhau có thể là gì.

Tuyệt vời!

Nhưng vẫn có hai điều kiện con có thể xảy ra mà bạn có thể gặp phải:

* (1a): `x` và `y` có thể đã thuộc cùng một kiểu, cho dù cả hai đều là `string`, `number`, v.v.

* (1b): `x` và `y` có thể thuộc các kiểu khác nhau.

Chúng ta hãy xem xét từng trường hợp này một cách riêng biệt.

##### (1a) Các Kiểu Đã Biết Trùng Khớp Nhau (Known Matching Types)

Nếu các kiểu trong phép so sánh bằng trùng khớp nhau (bất kể chúng là gì), chúng ta đã biết chắc chắn rằng `==` và `===` làm chính xác cùng một điều. Hoàn toàn không có sự khác biệt nào.

Ngoại trừ việc, `==` *ngắn hơn* một ký tự. Hầu hết các nhà phát triển đều cảm thấy theo bản năng rằng phiên bản ngắn gọn nhất nhưng tương đương của một thứ gì đó thường được ưa chuộng nhất. Điều đó dĩ nhiên không phải là phổ quát, nhưng ít nhất nó là một sự ưu tiên chung.

```js
// cách này là tốt nhất
if (x == y) {
    // ..
}
```

Trong trường hợp cụ thể này, thêm một dấu `=` sẽ không giúp ích gì cho chúng ta trong việc làm cho mã rõ ràng hơn. Thực tế, nó thực sự sẽ làm cho phép so sánh trở nên tồi tệ hơn!

```js
// cách này ở đây hoàn toàn tệ hơn!
if (x === y) {
    // ..
}
```

Tại sao nó lại tệ hơn?

Bởi vì trong kịch bản (2), chúng ta đã xác định rằng `===` được sử dụng làm giải pháp cuối cùng khi chúng ta không biết đủ/biết gì về các kiểu để có thể dự đoán kết quả. Chúng ta sử dụng `===` khi chúng ta muốn đảm bảo rằng mình đang tránh việc ép kiểu khi chúng ta biết việc ép kiểu có thể xảy ra.

Nhưng điều đó không áp dụng ở đây! Chúng ta đã biết rằng không có sự ép kiểu nào xảy ra cả. Không có lý do gì để làm người đọc bối rối với một dấu `===` ở đây. Nếu bạn sử dụng `===` ở một nơi mà bạn đã *biết* các kiểu -- và hơn nữa, chúng đã trùng khớp! -- điều đó thực sự có thể gửi một tín hiệu mâu thuẫn lẫn lộn đến người đọc. Họ có thể đã cho rằng họ biết điều gì sẽ xảy ra trong phép kiểm tra bằng, nhưng sau đó họ nhìn thấy `===` và họ tự nghi ngờ phán đoán của chính mình!

Một lần nữa, để nói một cách thẳng thắn, nếu bạn biết các kiểu của một phép so sánh bằng, và bạn biết chúng trùng khớp, chỉ có một lựa chọn đúng duy nhất: `==`.

```js
// hãy kiên định với lựa chọn này
if (x == y) {
    // ..
}
```

##### (1b) Các Kiểu Đã Biết Không Khớp Nhau (Known Mismatched Types)

Được rồi, chúng ta đang ở kịch bản cuối cùng. Chúng ta cần so sánh `x` và `y`, và chúng ta biết kiểu của chúng, nhưng chúng ta cũng biết kiểu của chúng **KHÔNG** giống nhau.

Chúng ta nên sử dụng toán tử nào ở đây?

Nếu bạn chọn `===`, bạn đã phạm phải một sai lầm to lớn. Tại sao!? Bởi vì `===` được sử dụng với các kiểu đã biết là không khớp sẽ không bao giờ, không bao giờ, không bao giờ trả về `true`. Nó sẽ luôn luôn thất bại.

```js
// `x` và `y` có kiểu khác nhau?
if (x === y) {
    // xin chúc mừng, mã bên trong này sẽ KHÔNG BAO GIỜ chạy
}
```

Được rồi. Vì vậy, `===` bị loại bỏ khi các kiểu đã biết là không khớp nhau. Lựa chọn duy nhất khác của chúng ta là gì?

Thực ra, chúng ta lại có hai lựa chọn. Chúng ta *có thể* quyết định:

* (1b-1): Hãy thay đổi mã để chúng ta không cố thực hiện kiểm tra bằng nhau với các kiểu đã biết là không khớp; điều đó có thể liên quan đến việc ép kiểu tường minh một hoặc cả hai giá trị để kiểu của chúng bây giờ trùng khớp nhau, trong trường hợp đó hãy quay trở lại kịch bản (1a).

* (1b-2): Nếu chúng ta định so sánh các kiểu đã biết là không khớp để kiểm tra sự bằng nhau, và chúng ta muốn có bất kỳ hy vọng nào để phép kiểm tra đó có thể vượt qua, chúng ta *phải* sử dụng `==`, bởi vì nó là toán tử duy nhất trong số các toán tử so sánh bằng có thể ép kiểu một hoặc cả hai toán hạng cho đến khi các kiểu trùng khớp nhau.

```js
// `x` và `y` có kiểu khác nhau,
// vì vậy hãy để JS ép kiểu chúng
// cho phép so sánh bằng
if (x == y) {
    // .. (vậy là, bạn đang nói vẫn có cơ hội ư?)
}
```

Thế là xong. Chúng ta đã hoàn thành. Chúng ta đã xem xét mọi điều kiện so sánh bằng nhạy cảm với kiểu có thể xảy ra (giữa `x` và `y`).

#### Tóm tắt Phép So sánh Bằng Nhạy cảm với Kiểu (Summarizing Type-Sensitive Equality Comparison)

Lập luận cho việc luôn ưu tiên `==` hơn `===` như sau:

1. Cho dù bạn có sử dụng TypeScript hay không -- nhưng đặc biệt là nếu bạn *có* sử dụng TypeScript -- mục tiêu nên là làm cho mọi phần đơn lẻ của mã, bao gồm tất cả các phép so sánh bằng, đều *nhận thức về kiểu*.

2. Nếu bạn biết các kiểu, bạn nên luôn luôn ưu tiên `==`.

    - Trong trường hợp các kiểu trùng khớp, `==` vừa ngắn hơn vừa đúng đắn hơn cho phép kiểm tra.

    - Trong trường hợp các kiểu không khớp, `==` là toán tử duy nhất có thể ép kiểu (các) toán hạng cho đến khi các kiểu khớp nhau, vì vậy đó là cách duy nhất mà một phép kiểm tra như vậy có thể có hy vọng vượt qua được.

3. Cuối cùng, chỉ khi bạn *không thể* biết/dự đoán các kiểu, vì một lý do gây ức chế nào đó, và bạn không còn lựa chọn nào khác, hãy lùi lại sử dụng `===` như một giải pháp cuối cùng. Và có lẽ nên thêm một ghi chú mã (comment) ở đó thừa nhận lý do tại sao `===` được sử dụng, và có thể nhắc nhở một số nhà phát triển tương lai sau này thay đổi mã để sửa chữa sự thiếu sót đó và loại bỏ chiếc nạng chống `===`.

#### Vấn đề Thiếu Nhất quán của TypeScript (TypeScript's Inconsistency Problem)

Hãy để tôi nói siêu rõ ràng: nếu bạn đang sử dụng TypeScript đúng cách, và bạn biết các kiểu của một phép so sánh bằng, việc sử dụng `===` cho phép so sánh đó đơn giản chỉ là *sai lầm*! Chấm hết.

Vấn đề là, TypeScript một cách kỳ lạ và gây bực bội vẫn yêu cầu bạn phải sử dụng `===`, trừ khi nó đã biết rằng các kiểu đã trùng khớp.

Đó là bởi vì TypeScript hoặc không hiểu đầy đủ về nhận thức kiểu và sự ép kiểu, hoặc -- và điều này thậm chí còn gây phẫn nộ hơn! -- nó hiểu đầy đủ nhưng nó vẫn căm ghét hệ thống kiểu của JS đến mức xa lánh ngay cả những lập luận nhận thức kiểu cơ bản nhất.

Không tin tôi sao? Bạn nghĩ tôi đang quá gay gắt ư? Hãy thử điều này trong TypeScript: [^TSExample2]

```js
let result = (42 == "42");
// This condition will always return 'false' since
// the types 'number' and 'string' have no overlap.
```

Tôi không còn từ ngữ nào để diễn tả điều đó làm tôi bực bội đến mức nào. Nếu bạn đã chú ý đến chương dài và nặng nề này, bạn biết rằng TypeScript về cơ bản đang nói dối ở đây. Dĩ nhiên `42 == "42"` sẽ tạo ra `true` trong JS.

Chà, nó không phải là một lời nói dối, nhưng nó đang phơi bày một sự thật cơ bản mà rất nhiều người vẫn chưa đánh giá hết: TypeScript hoàn toàn vứt bỏ các quy tắc thông thường của hệ thống kiểu của JS, bởi vì lập trường của TypeScript là hệ thống kiểu của JS -- và đặc biệt là ép kiểu ngầm định -- là tồi tệ, và cần phải được thay thế.

Trong thế giới của TypeScript, `42` và `"42"` không bao giờ có thể bằng nhau. Do đó mới có thông báo lỗi. Nhưng trong thế giới JS, `42` và `"42"` tuyệt đối bằng nhau theo kiểu ép buộc. Và tôi tin rằng tôi đã đưa ra một lập luận mạnh mẽ ở đây rằng chúng *nên được* giả định là tương đương nhau một cách an toàn theo kiểu ép buộc.

Điều làm tôi bận tâm hơn nữa là, TypeScript có một loạt các điểm mâu thuẫn không nhất quán về mặt này. TypeScript hoàn toàn ổn với việc ép kiểu *ngầm định* trong mã này:

```js
irony = `The value '42' and ${42} are coercively equal.`;
```

Số `42` bị ép kiểu ngầm định thành một chuỗi khi được nội suy vào câu. Tại sao TypeScript lại đồng ý với phép ép kiểu ngầm định này, nhưng lại không đồng ý với phép ép kiểu ngầm định `42 == "42"`?

TypeScript cũng không có lời phàn nàn nào về mã này:

```js
API_BASE_URL = "https://some.tld/api/2";
if (API_BASE_URL) {
    // ..
}
```

Tại sao `ToBoolean()` lại là một phép ép kiểu ngầm định ĐƯỢC CHẤP NHẬN, nhưng `ToNumber()` trong thuật toán `==` lại không?

Tôi sẽ để lại cho bạn suy ngẫm điều này: bạn có thực sự nghĩ rằng việc viết mã mà cuối cùng sẽ chạy trong một JS engine, nhưng lại sử dụng một công cụ và phong cách mã đã cố tình đẩy bỏ hầu hết toàn bộ một trụ cột của ngôn ngữ JS là một ý tưởng hay ho không? Hơn nữa, liệu có ổn không khi nó cũng xoay như chong chóng với nhiều ngoại lệ không nhất quán, chỉ đơn giản là để phục vụ cho các thói quen cũ của các nhà phát triển JS?

## Còn Lại Những Gì? (What's Left?)

Tôi hy vọng đến bây giờ bạn cảm thấy có nhiều thông tin hơn về cách hoạt động của hệ thống kiểu của JS, từ các kiểu giá trị nguyên thủy đến các kiểu đối tượng, cho đến cách mà việc ép kiểu được engine thực hiện.

Quan trọng hơn, giờ đây bạn cũng có một bức tranh hoàn chỉnh hơn nhiều về ưu/nhược điểm của những lựa chọn mà chúng ta đưa ra khi sử dụng hệ thống kiểu của JS, chẳng hạn như việc lựa chọn các phép ép kiểu *ngầm định* hay *tường minh* ở các thời điểm khác nhau.

Nhưng chúng ta vẫn chưa bao quát đầy đủ ngữ cảnh mà hệ thống kiểu hoạt động. Đối với phần còn lại của cuốn sách này, chúng ta sẽ chuyển sự chú ý sang các quy tắc cú pháp/ngữ pháp (syntax/grammar) của JS nhằm chi phối cách các toán tử và câu lệnh hành xử.

[^EichCoercion]: "The State of JavaScript - Brendan Eich", luồng bình luận, Hacker News; 9 tháng 10 năm 2012; https://news.ycombinator.com/item?id=4632704 ; Truy cập tháng 8 năm 2022

[^CrockfordCoercion]: "JavaScript: The World's Most Misunderstood Programming Language"; 2001; https://www.crockford.com/javascript/javascript.html ; Truy cập tháng 8 năm 2022

[^CrockfordIfs]: "json2.js", Github; 21 tháng 4 năm 2018; https://github.com/douglascrockford/JSON-js/blob/8e8b0407e475e35942f7e9461dab81929fcc7321/json2.js#L336 ; Truy cập tháng 8 năm 2022

[^BrendanToString]: Danh sách gửi thư ESDiscuss; 26 tháng 8 năm 2014; https://esdiscuss.org/topic/string-symbol#content-15 ; Truy cập tháng 8 năm 2022

[^AbstractOperations]: "7.1 Type Conversion", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-type-conversion ; Truy cập tháng 8 năm 2022

[^ToBoolean]: "7.1.2 ToBoolean(argument)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-toboolean ; Truy cập tháng 8 năm 2022

[^ExoticFalsyObjects]: "B.3.6 The [[IsHTMLDDA]] Internal Slot", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-IsHTMLDDA-internal-slot ; Truy cập tháng 8 năm 2022

[^OrdinaryToPrimitive]: "7.1.1.1 OrdinaryToPrimitive(O,hint)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-ordinarytoprimitive ; Truy cập tháng 8 năm 2022

[^ToString]: "7.1.17 ToString(argument)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-tostring ; Truy cập tháng 8 năm 2022

[^StringConstructor]: "22.1.1 The String Constructor", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-string-constructor ; Truy cập tháng 8 năm 2022

[^StringFunction]: "22.1.1.1 String(value)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-string-constructor-string-value ; Truy cập tháng 8 năm 2022

[^ToNumber]: "7.1.4 ToNumber(argument)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-tonumber ; Truy cập tháng 8 năm 2022

[^ToNumeric]: "7.1.3 ToNumeric(argument)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-tonumeric ; Truy cập tháng 8 năm 2022

[^NumberConstructor]: "21.1.1 The Number Constructor", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-number-constructor ; Truy cập tháng 8 năm 2022

[^NumberFunction]: "21.1.1.1 Number(value)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-number-constructor-number-value ; Truy cập tháng 8 năm 2022

[^SameValue]: "7.2.11 SameValue(x,y)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-samevalue ; Truy cập tháng 8 năm 2022

[^StrictEquality]: "7.2.16 IsStrictlyEqual(x,y)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-isstrictlyequal ; Truy cập tháng 8 năm 2022

[^LooseEquality]: "7.2.15 IsLooselyEqual(x,y)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-islooselyequal ; Truy cập tháng 8 năm 2022

[^NumericAbstractOps]: "6.1.6 Numeric Types", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-numeric-types ; Truy cập tháng 8 năm 2022

[^NumberEqual]: "6.1.6.1.13 Number:equal(x,y)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-numeric-types-number-equal ; Truy cập tháng 8 năm 2022

[^BigIntEqual]: "6.1.6.2.13 BigInt:equal(x,y)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-numeric-types-bigint-equal ; Truy cập tháng 8 năm 2022

[^LessThan]: "7.2.14 IsLessThan(x,y,LeftFirst)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-islessthan ; Truy cập tháng 8 năm 2022

[^StringPrefix]: "7.2.9 IsStringPrefix(p,q)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-isstringprefix ; Truy cập tháng 8 năm 2022

[^SymbolString]: "String(symbol)", Danh sách gửi thư ESDiscuss; 12 tháng 8 năm 2014; https://esdiscuss.org/topic/string-symbol ; Truy cập tháng 8 năm 2022

[^ASMjs]: "ASM.js - Working Draft"; 18 tháng 8 năm 2014; http://asmjs.org/spec/latest/ ; Truy cập tháng 8 năm 2022

[^TSExample1]: "TypeScript Playground"; https://tinyurl.com/ydkjs-ts-example-1 ; Truy cập tháng 8 năm 2022

[^TSExample2]: "TypeScript Playground"; https://tinyurl.com/ydkjs-ts-example-2 ; Truy cập tháng 8 năm 2022

[^TSLiteralTypes]: "TypeScript 4.1, Template Literal Types"; https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-1.html#template-literal-types ; Truy cập tháng 8 năm 2022
