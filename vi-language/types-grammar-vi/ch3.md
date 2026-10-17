# You Don't Know JS Yet: Types & Grammar - 2nd Edition
# Chương 3: Giá trị Đối tượng (Object Values)

| GHI CHÚ: |
| :--- |
| Đang trong quá trình hoàn thiện (Work in progress) |

Giờ đây khi chúng ta đã nắm vững các kiểu nguyên thủy có sẵn (built-in primitive types), chúng ta sẽ chuyển hướng chú ý sang các kiểu `object` (đối tượng) trong JS.

Tôi có thể viết cả một cuốn sách để bàn luận chuyên sâu về đối tượng; thực tế là, tôi đã làm điều đó rồi! Tập sách "Objects & Classes" trong bộ sách này đã đề cập rất sâu về đối tượng, vì vậy hãy đảm bảo bạn đã đọc tập đó trước khi tiếp tục với chương này.

Thay vì lặp lại nội dung của cuốn sách đó, ở đây chúng ta sẽ tập trung sự chú ý vào cách mà kiểu giá trị `object` hoạt động và tương tác với các giá trị khác trong JS.

## Các loại Đối tượng (Types of Objects)

Kiểu giá trị `object` bao gồm một số kiểu con (sub-types), mỗi kiểu đều có các hành vi chuyên biệt, bao gồm:

* Plain objects (đối tượng thuần túy)
* Fundamental objects (đối tượng nền tảng / boxed primitives - các giá trị nguyên thủy được bao gói)
* Built-in objects (đối tượng tích hợp sẵn)
* Mảng (arrays)
* Biểu thức chính quy (regular expressions)
* Hàm (functions - hay còn gọi là "callable objects" / đối tượng có thể gọi được)

Ngoài các hành vi chuyên biệt, một đặc điểm chung là tất cả các đối tượng đều có thể đóng vai trò như các tập hợp (gồm các thuộc tính - properties) nắm giữ các giá trị (bao gồm cả các hàm/phương thức).

## Đối tượng Thuần túy (Plain Objects)

Kiểu giá trị đối tượng nói chung đôi khi được gọi là *plain ol' javascript objects* (POJO).

Các plain object có cú pháp khai báo dạng literal (khởi tạo trực tiếp):

```js
address = {
    street: "12345 Market St",
    city: "San Francisco",
    state: "CA",
    zip: "94114"
};
```

Đối tượng thuần túy này (POJO), được định nghĩa bằng các dấu ngoặc nhọn `{ .. }`, là một tập hợp các thuộc tính có tên (`street`, `city`, `state`, và `zip`). Các thuộc tính có thể chứa bất kỳ giá trị nào, các kiểu nguyên thủy hoặc các đối tượng khác (bao gồm mảng, hàm, v.v.).

Cùng một đối tượng đó cũng có thể được định nghĩa theo phong cách mệnh lệnh (imperatively) bằng cách sử dụng hàm khởi tạo `new Object()`:

```js
address = new Object();
address.street = "12345 Market St";
address.city = "San Francisco";
address.state = "CA";
address.zip = "94114";
```

Mặc định, các plain object được liên kết `[[Prototype]]` với `Object.prototype`, mang lại cho chúng quyền truy cập được ủy quyền (delegated access) tới một số phương thức đối tượng chung, chẳng hạn như:

* `toString()` / `toLocaleString()`
* `valueOf()`
* `isPrototypeOf(..)`
* `hasOwnProperty(..)` (gần đây đã bị coi là không dùng nữa -- giải pháp thay thế: tiện ích tĩnh `Object.hasOwn(..)`)
* `propertyIsEnumerable(..)`
* `__proto__` (hàm getter)

```js
address.isPrototypeOf(Object.prototype);    // true
address.isPrototypeOf({});                  // false
```

## Đối tượng Nền tảng (Fundamental Objects)

JS định nghĩa một vài kiểu đối tượng *nền tảng* (fundamental object types), vốn là các thể hiện (instances) của các hàm khởi tạo có sẵn khác nhau, bao gồm:

* `new String()`
* `new Number()`
* `new Boolean()`

Lưu ý rằng những hàm khởi tạo này phải được sử dụng với từ khóa `new` để tạo ra các thể hiện của các fundamental object. Nếu không, các hàm này thực chất sẽ thực hiện việc ép kiểu (coercion) (xem Chương 4).

Những hàm khởi tạo đối tượng nền tảng này tạo ra các kiểu giá trị đối tượng thay vì các kiểu nguyên thủy:

```js
myName = "Kyle";
typeof myName;                      // "string"

myNickname = new String("getify");
typeof myNickname;                  // "object"
```

Nói cách khác, một thể hiện của một hàm khởi tạo đối tượng nền tảng thực sự có thể được xem như một lớp bọc (wrapper) bao quanh giá trị nguyên thủy cơ bản tương ứng.

| CẢNH BÁO: |
| :--- |
| Việc trực tiếp khởi tạo các đối tượng nền tảng này gần như luôn được coi là *thực hành không tốt* (bad practice). Các phiên bản nguyên thủy tương ứng thường dễ dự đoán hơn, hiệu năng tốt hơn, và cung cấp khả năng *tự động bao gói* (*auto-boxing* - xem phần "Đối tượng Tự động" bên dưới) bất cứ khi nào dạng đối tượng bọc cơ bản là cần thiết để truy cập thuộc tính/phương thức. |

Các hàm `Symbol(..)` và `BigInt(..)` được gọi trong đặc tả kỹ thuật là "constructors", mặc dù chúng không được sử dụng cùng từ khóa `new`, và các giá trị mà chúng tạo ra trong một chương trình JS quả thực là các kiểu nguyên thủy.

Tuy nhiên, có các *fundamental objects* nội bộ dành cho hai kiểu này, được sử dụng cho ủy quyền prototype (prototype delegation) và *auto-boxing*.

Ngược lại, đối với các giá trị nguyên thủy `null` và `undefined`, không có các "constructor" `Null()` hoặc `Undefined()`, cũng không có các fundamental object hay prototype tương ứng.

### Prototype

Các thể hiện của hàm khởi tạo đối tượng nền tảng được liên kết `[[Prototype]]` với các đối tượng `prototype` của hàm khởi tạo của chúng:

* `String.prototype`: định nghĩa thuộc tính `length`, cũng như các phương thức chuyên biệt cho chuỗi, như `toUpperCase()`, v.v.

* `Number.prototype`: định nghĩa các phương thức chuyên biệt cho số, như `toPrecision(..)`, `toFixed(..)`, v.v.

* `Boolean.prototype`: định nghĩa các phương thức `toString()` và `valueOf()` mặc định.

* `Symbol.prototype`: định nghĩa `description` (getter), cũng như các phương thức `toString()` và `valueOf()` mặc định.

* `BigInt.prototype`: định nghĩa các phương thức `toString()`, `toLocaleString()`, và `valueOf()` mặc định.

Bất kỳ thể hiện trực tiếp nào của các hàm khởi tạo tích hợp sẵn đều có quyền truy cập được ủy quyền `[[Prototype]]` tới các thuộc tính/phương thức trong `prototype` tương ứng của nó. Hơn nữa, các giá trị nguyên thủy tương ứng cũng có quyền truy cập được ủy quyền như vậy, thông qua cơ chế *auto-boxing*.

### Đối tượng Tự động (Automatic Objects)

Tôi đã đề cập đến *auto-boxing* nhiều lần (bao gồm Chương 1 và 2, và một vài lần trong chương này từ đầu đến giờ). Cuối cùng cũng đã đến lúc chúng ta giải thích khái niệm đó.

Việc truy cập một thuộc tính hoặc phương thức trên một giá trị đòi hỏi giá trị đó phải là một đối tượng. Như chúng ta đã thấy trong Chương 1, các giá trị nguyên thủy *không phải* là đối tượng, vì vậy JS sau đó cần tạm thời chuyển đổi/bọc giá trị nguyên thủy đó thành đối tượng nền tảng tương ứng[^AutoBoxing] để thực hiện việc truy cập đó.

Ví dụ:

```js
myName = "Kyle";

myName.length;              // 4

myName.toUpperCase();       // "KYLE"
```

Việc truy cập thuộc tính `length` hoặc phương thức `toUpperCase()` chỉ được cho phép trên một giá trị chuỗi nguyên thủy bởi vì JS *tự động bao gói* (*auto-box*) kiểu `string` nguyên thủy thành một đối tượng nền tảng bao bọc, là một thể hiện của `new String(..)`. Nếu không, tất cả các truy cập như vậy sẽ đều thất bại, vì các giá trị nguyên thủy không có bất kỳ thuộc tính nào.

Quan trọng hơn, khi giá trị nguyên thủy được *auto-boxed* thành đối tượng nền tảng tương ứng, các đối tượng được tạo nội bộ đó có quyền truy cập vào các thuộc tính/phương thức được định nghĩa trước (như `length` và `toUpperCase()`) thông qua liên kết `[[Prototype]]` với prototype của đối tượng nền tảng tương ứng của chúng.

Vì vậy, một `string` được *auto-boxed* là một thể hiện của `new String()`, và do đó được liên kết với `String.prototype`. Tương tự, điều này cũng đúng với `number` (được bọc thành một thể hiện của `new Number()`) và `boolean` (được bọc thành một thể hiện của `new Boolean()`).

Mặc dù các "constructor" `Symbol(..)` và `BigInt(..)` (được sử dụng không có `new`) tạo ra các giá trị nguyên thủy, những giá trị nguyên thủy này cũng có thể được *auto-boxed* thành các dạng bọc đối tượng nền tảng nội bộ của chúng, nhằm phục vụ cho mục đích ủy quyền truy cập vào các thuộc tính/phương thức.

| GHI CHÚ: |
| :--- |
| Xem cuốn sách "Objects & Classes" trong bộ sách này để biết thêm về các liên kết `[[Prototype]]` và quyền truy cập ủy quyền/kế thừa tới các đối tượng prototype của các hàm khởi tạo đối tượng nền tảng. |

Vì `null` và `undefined` không có các đối tượng nền tảng tương ứng, nên không có hiện tượng *auto-boxing* đối với các giá trị này.

Một câu hỏi mang tính chủ quan cần xem xét: liệu *auto-boxing* có phải là một hình thức ép kiểu (coercion) không? Theo tôi thì có, mặc dù một số người không đồng tình. Về mặt nội bộ, một giá trị nguyên thủy được chuyển đổi thành một đối tượng, nghĩa là đã xảy ra sự thay đổi về kiểu giá trị. Đúng vậy, nó chỉ là tạm thời, nhưng rất nhiều phép ép kiểu cũng chỉ là tạm thời. Hơn nữa, việc chuyển đổi này khá là *ngầm định* (*implicit*) (được ngụ ý bởi việc truy cập thuộc tính/phương thức, nhưng chỉ diễn ra ở bên trong nội bộ). Chúng ta sẽ xem xét lại bản chất của coercion trong Chương 4.

## Các Đối tượng Tích hợp Sẵn khác (Other Built-in Objects)

Ngoài các hàm khởi tạo đối tượng nền tảng, JS còn định nghĩa một số hàm khởi tạo có sẵn khác nhằm tạo ra các kiểu con đối tượng chuyên biệt hơn:

* `new Date(..)`
* `new Error(..)`
* `new Map(..)`, `new Set(..)`, `new WeakMap(..)`, `new WeakSet(..)` -- các tập hợp theo khóa (keyed collections)
* `new Int8Array(..)`, `new Uint32Array(..)`, v.v. -- các tập hợp mảng định kiểu theo chỉ số (indexed, typed-array collections)
* `new ArrayBuffer(..)`, `new SharedArrayBuffer(..)`, v.v. -- các tập hợp dữ liệu có cấu trúc (structured data collections)

## Mảng (Arrays)

Mảng là các đối tượng được chuyên biệt hóa để hoạt động như các tập hợp giá trị được đánh chỉ mục theo số (numerically indexed), trái ngược với việc lưu giữ các giá trị tại các thuộc tính có tên như các plain object.

Mảng có cú pháp khai báo dạng literal:

```js
favoriteNumbers = [ 3, 12, 42 ];

favoriteNumbers[2];                 // 42
```

Cùng mảng đó cũng có thể được định nghĩa theo phong cách mệnh lệnh bằng cách sử dụng hàm khởi tạo `new Array()`:

```js
favoriteNumbers = new Array();
favoriteNumbers[0] = 3;
favoriteNumbers[1] = 12;
favoriteNumbers[2] = 42;
```

Mảng được liên kết `[[Prototype]]` với `Array.prototype`, mang lại cho chúng quyền truy cập được ủy quyền tới một loạt các phương thức hướng mảng, chẳng hạn như `map(..)`, `includes(..)`, v.v.:

```js
favoriteNumbers.map(v => v * 2);
// [ 6, 24, 84 ]

favoriteNumbers.includes(42);       // true
```

Một số phương thức được định nghĩa trên `Array.prototype` -- ví dụ: `push(..)`, `pop(..)`, `sort(..)`, v.v. -- hoạt động bằng cách biến đổi trực tiếp giá trị mảng tại chỗ (modify in place). Các phương thức khác -- ví dụ: `concat(..)`, `map(..)`, `slice(..)` -- hoạt động bằng cách tạo ra một mảng mới để trả về, giữ nguyên mảng ban đầu. Nhóm hàm mảng thứ ba -- ví dụ: `indexOf(..)`, `includes(..)`, v.v. -- chỉ đơn thuần tính toán và trả về một kết quả (không phải là mảng).

## Biểu thức Chính quy (Regular Expressions)

// TODO

## Hàm (Functions)

// TODO

## Được Đề xuất: Records/Tuples (Proposed: Records/Tuples)

Tại thời điểm viết cuốn sách này, có một đề xuất (stage-2)[^RecordsTuplesProposal] nhằm thêm một bộ tính năng mới vào JS, tương ứng chặt chẽ với các plain object và mảng, nhưng có một số khác biệt đáng chú ý.

Record tương tự như plain object, nhưng có tính bất biến (immutable - bị đóng kín, chỉ đọc), và (không giống như đối tượng) được đối xử như các giá trị nguyên thủy, cho các mục đích gán giá trị và so sánh bằng. Điểm khác biệt về cú pháp là ký tự `#` trước dấu phân cách `{ }`. Record chỉ có thể chứa các giá trị nguyên thủy (bao gồm cả record và tuple).

Tuple có mối quan hệ hoàn toàn tương tự, nhưng đối với mảng, bao gồm ký tự `#` trước các dấu phân cách `[ ]`.

Điều quan trọng cần lưu ý là mặc dù chúng trông và có vẻ giống như các đối tượng/mảng, chúng thực sự là các giá trị nguyên thủy (không phải đối tượng).

[^FundamentalObjects]: "20 Fundamental Objects", EcamScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-fundamental-objects ; Truy cập tháng 8 năm 2022

[^AutoBoxing]: "6.2.4.6 PutValue(V,W)", Bước 5.a, ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-putvalue ; Truy cập tháng 8 năm 2022

[^RecordsTuplesProposal]: "JavaScript Records & Tuples Proposal"; Robin Ricard, Rick Button, Nicolò Ribaudo;
https://github.com/tc39/proposal-record-tuple ; Truy cập tháng 8 năm 2022
