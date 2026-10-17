# Bạn Chưa Biết JS Đâu: Đối Tượng & Lớp - Tái bản lần 2
# Chương 2: Cách đối tượng hoạt động

| GHI CHÚ: |
| :--- |
| Đang trong quá trình hoàn thiện (Work in progress) |

Đối tượng không chỉ đơn thuần là những thùng chứa nhiều giá trị, mặc dù rõ ràng đó là ngữ cảnh cho hầu hết các tương tác với đối tượng.

Để hiểu thấu đáo cơ chế đối tượng trong JS, và tận dụng tối đa việc sử dụng đối tượng trong các chương trình của mình, chúng ta cần xem xét kỹ lưỡng hơn một số đặc tính của đối tượng (và các thuộc tính của chúng) vốn có thể ảnh hưởng đến hành vi của chúng khi chúng ta tương tác với chúng.

Những đặc tính xác định hành vi nền tảng của đối tượng này được gọi chung theo thuật ngữ hình thức là "giao thức siêu đối tượng" (metaobject protocol - MOP)[^mop]. MOP hữu ích không chỉ cho việc tìm hiểu cách các đối tượng sẽ hành xử, mà còn để ghi đè các hành vi mặc định của đối tượng nhằm uốn nắn ngôn ngữ phục vụ trọn vẹn hơn nhu cầu chương trình của chúng ta.

## Bộ mô tả thuộc tính (Property Descriptors)

Mỗi thuộc tính trên một đối tượng được mô tả nội bộ bởi thứ được gọi là "bộ mô tả thuộc tính" (property descriptor). Bản thân nó chính là một đối tượng (hay còn gọi là "siêu đối tượng" - metaobject) chứa một số thuộc tính (hay còn gọi là các "đặc tính" - attributes), chi phối cách thức hoạt động của thuộc tính mục tiêu.

Chúng ta có thể truy xuất một bộ mô tả thuộc tính cho bất kỳ thuộc tính hiện có nào bằng cách sử dụng `Object.getOwnPropertyDescriptor(..)` (ES5):

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle"
};

Object.getOwnPropertyDescriptor(myObj,"favoriteNumber");
// {
//     value: 42,
//     enumerable: true,
//     writable: true,
//     configurable: true
// }
```

Chúng ta thậm chí có thể dùng một bộ mô tả như vậy để định nghĩa một thuộc tính mới trên một đối tượng, bằng cách sử dụng `Object.defineProperty(..)` (ES5):

```js
anotherObj = {};

Object.defineProperty(anotherObj,"fave",{
    value: 42,
    enumerable: true,     // mặc định nếu bị bỏ qua
    writable: true,       // mặc định nếu bị bỏ qua
    configurable: true    // mặc định nếu bị bỏ qua
});

anotherObj.fave;          // 42
```

Nếu một thuộc tính hiện có chưa bị đánh dấu là không thể cấu hình (với `configurable: false` trong bộ mô tả của nó), nó luôn có thể được định nghĩa lại/ghi đè bằng cách sử dụng `Object.defineProperty(..)`.

| CẢNH BÁO: |
| :--- |
| Một số phần trước trong chương này có nhắc đến việc "sao chép" (copying) hoặc "nhân bản" (duplicating) các thuộc tính. Người ta có thể giả định rằng việc sao chép/nhân bản đó sẽ diễn ra ở cấp độ bộ mô tả thuộc tính. Tuy nhiên, không có thao tác nào trong số đó thực sự hoạt động theo cách như vậy; tất cả chúng đều thực hiện truy cập và gán kiểu `=` đơn giản, điều này có tác dụng bỏ qua mọi sắc thái tinh tế trong cách thức định nghĩa bộ mô tả bên dưới của một thuộc tính. |

Mặc dù có vẻ ít phổ biến hơn nhiều trong thực tế, chúng ta thậm chí có thể định nghĩa nhiều thuộc tính cùng một lúc, mỗi thuộc tính có một bộ mô tả riêng:

```js
anotherObj = {};

Object.defineProperties(anotherObj,{
    "fave": {
        // một bộ mô tả thuộc tính
    },
    "superFave": {
        // một bộ mô tả thuộc tính khác
    }
});
```

Không phổ biến lắm khi bắt gặp cách dùng này, bởi vì hiếm khi bạn cần kiểm soát cụ thể định nghĩa của nhiều thuộc tính cùng lúc. Nhưng nó có thể hữu ích trong một số trường hợp.

### Thuộc tính truy cập (Accessor Properties)

Một bộ mô tả thuộc tính thường định nghĩa một thuộc tính `value`, như được hiển thị ở trên. Tuy nhiên, một loại thuộc tính đặc biệt, được gọi là "thuộc tính truy cập" (accessor property - hay còn gọi là getter/setter), có thể được định nghĩa. Đối với một thuộc tính như thế này, bộ mô tả của nó không định nghĩa một thuộc tính `value` cố định, mà thay vào đó sẽ trông giống như sau:

```js
{
    get() { .. },    // hàm được gọi khi truy xuất giá trị
    set(v) { .. },   // hàm được gọi khi gán giá trị
    // .. enumerable, v.v.
}
```

Một getter trông giống như một truy cập thuộc tính (`obj.prop`), nhưng ở hậu trường nó kích hoạt phương thức `get()` đã được định nghĩa; nó tương tự như thể bạn đã gọi `obj.prop()`. Một setter trông giống như một phép gán thuộc tính (`obj.prop = value`), nhưng nó kích hoạt phương thức `set(..)` đã được định nghĩa; nó tương tự như thể bạn đã gọi `obj.prop(value)`.

Hãy cùng minh họa một thuộc tính truy cập getter/setter:

```js
anotherObj = {};

Object.defineProperty(anotherObj,"fave",{
    get() { console.log("Getting 'fave' value!"); return 123; },
    set(v) { console.log(`Ignoring ${v} assignment.`); }
});

anotherObj.fave;
// Getting 'fave' value!
// 123

anotherObj.fave = 42;
// Ignoring 42 assignment.

anotherObj.fave;
// Getting 'fave' value!
// 123
```

### Enumerable, Writable, Configurable

Bên cạnh `value` hoặc `get()` / `set(..)`, 3 đặc tính khác của một bộ mô tả thuộc tính là (như đã hiển thị ở trên):

* `enumerable`
* `writable`
* `configurable`

Đặc tính `enumerable` kiểm soát việc thuộc tính có xuất hiện trong các phép liệt kê khác nhau của thuộc tính đối tượng hay không, chẳng hạn như `Object.keys(..)`, `Object.entries(..)`, các vòng lặp `for..in`, và việc sao chép diễn ra với cú pháp trải đối tượng `...` cùng `Object.assign(..)`. Hầu hết các thuộc tính nên được để ở trạng thái có thể liệt kê (enumerable), nhưng bạn có thể đánh dấu một số thuộc tính đặc biệt trên đối tượng là không thể liệt kê (non-enumerable) nếu chúng không nên được lặp qua hoặc sao chép.

Đặc tính `writable` kiểm soát việc phép gán `value` (thông qua `=`) có được cho phép hay không. Để biến một thuộc tính thành "chỉ đọc" (read only), hãy định nghĩa nó với `writable: false`. Tuy nhiên, miễn là thuộc tính đó vẫn có thể cấu hình (configurable), `Object.defineProperty(..)` vẫn có thể thay đổi giá trị bằng cách thiết lập `value` khác đi.

Đặc tính `configurable` kiểm soát việc **bộ mô tả** của thuộc tính có thể được định nghĩa lại/ghi đè hay không. Một thuộc tính có `configurable: false` sẽ bị khóa với định nghĩa hiện tại của nó, và mọi nỗ lực tiếp theo nhằm thay đổi nó bằng `Object.defineProperty(..)` sẽ thất bại. Một thuộc tính không thể cấu hình (non-configurable) vẫn có thể được gán các giá trị mới (thông qua `=`), miễn là `writable: true` vẫn được thiết lập trên bộ mô tả của thuộc tính.

## Kiểu phụ của đối tượng (Object Sub-Types)

Có rất nhiều kiểu phụ chuyên biệt của đối tượng trong JS. Nhưng cho đến nay, hai kiểu phổ biến nhất mà bạn sẽ tương tác là mảng (arrays) và hàm (`function`s).

| GHI CHÚ: |
| :--- |
| Bằng thuật ngữ "kiểu phụ" (sub-type), chúng tôi muốn nói đến khái niệm về một kiểu phái sinh đã kế thừa các hành vi từ một kiểu cha nhưng sau đó chuyên biệt hóa hoặc mở rộng các hành vi đó. Nói cách khác, các giá trị thuộc các kiểu phụ này hoàn toàn là các đối tượng, nhưng cũng *nhiều hơn chỉ là* đối tượng thuần túy. |

### Mảng (Arrays)

Mảng là các đối tượng được dành riêng cho việc **đánh chỉ mục bằng số** (numerically indexed), thay vì sử dụng các vị trí thuộc tính có tên bằng chuỗi. Chúng vẫn là các đối tượng, vì vậy một thuộc tính có tên như `favoriteNumber` là hoàn toàn hợp lệ. Nhưng việc trộn lẫn các thuộc tính có tên vào các mảng được đánh chỉ mục bằng số là điều rất không nên làm.

Mảng thường được ưu tiên định nghĩa bằng cú pháp literal (tương tự như đối tượng), nhưng với dấu ngoặc vuông `[ .. ]` thay vì dấu ngoặc nhọn `{ .. }`:

```js
myList = [ 23, 42, 109 ];
```

JS cho phép pha trộn bất kỳ kiểu giá trị nào trong mảng, bao gồm các đối tượng, các mảng khác, các hàm, v.v. Như bạn có thể đã biết, mảng là kiểu "chỉ mục bắt đầu từ 0" (zero-indexed), nghĩa là phần tử đầu tiên trong mảng nằm ở chỉ số `0`, chứ không phải `1`:

```js
myList = [ 23, 42, 109 ];

myList[0];      // 23
myList[1];      // 42
```

Hãy nhớ lại rằng bất kỳ tên thuộc tính dạng chuỗi nào trên một đối tượng mà "trông giống như" một số nguyên -- có thể được ép kiểu một cách hợp lệ thành một số nguyên dạng số -- sẽ thực sự được xử lý như một thuộc tính số nguyên (tức là chỉ mục số nguyên). Điều tương tự cũng áp dụng cho mảng. Bạn nên luôn luôn sử dụng `42` làm chỉ mục số nguyên (tức là tên thuộc tính), nhưng nếu bạn sử dụng chuỗi `"42"`, JS sẽ ngầm hiểu bạn muốn nó là một số nguyên và thực hiện việc đó thay cho bạn.

```js
// "2" hoạt động như một chỉ mục số nguyên ở đây, nhưng điều này không được khuyến khích
myList["2"];    // 109
```

Một ngoại lệ đối với *quy tắc* "không có thuộc tính được đặt tên trên mảng" là tất cả các mảng đều tự động cung cấp một thuộc tính `length`, thuộc tính này luôn được tự động cập nhật theo "độ dài" của mảng.

```js
myList = [ 23, 42, 109 ];

myList.length;   // 3

// "push" một giá trị khác vào cuối danh sách
myList.push("Hello");

myList.length;   // 4
```

| CẢNH BÁO: |
| :--- |
| Nhiều nhà phát triển JS tin một cách sai lầm rằng `length` của mảng về cơ bản là một *getter* (xem phần "Thuộc tính truy cập" ở phần trước của chương này), nhưng thực tế không phải vậy. Hậu quả là những nhà phát triển này cảm thấy việc truy cập thuộc tính này là "tốn kém" -- như thể JS phải tính toán lại độ dài ngay trong lúc chạy -- và do đó sẽ làm những việc như lưu lại/ghi nhớ độ dài của một mảng trước khi thực hiện một vòng lặp không làm biến đổi mảng qua nó. Điều này từng được coi là "thực hành tốt nhất" (best practice) từ góc độ hiệu năng. Nhưng trong ít nhất 10 năm trở lại đây, điều đó thực ra lại là một mẫu hình phản diện (anti-pattern), bởi vì engine JS quản lý thuộc tính `length` hiệu quả hơn nhiều so với việc mã JS của chúng ta cố gắng "tỏ ra thông minh hơn" engine để tránh việc kích hoạt thứ mà chúng ta tưởng là một *getter*. Sẽ hiệu quả hơn nhiều nếu để engine JS làm công việc của nó, và chỉ cần truy cập thuộc tính bất cứ khi nào và bất kể tần suất bao nhiêu lần nếu cần. |

#### Các vị trí rỗng (Empty Slots)

Mảng trong JS cũng có một "khiếm khuyết" thiết kế thực sự đáng tiếc, được gọi là "các vị trí rỗng" (empty slots). Nếu bạn gán một chỉ mục của mảng vượt quá phần đuôi hiện tại của mảng nhiều hơn một vị trí, JS sẽ để các vị trí ở giữa "rỗng" thay vì tự động gán chúng bằng `undefined` như bạn có thể mong đợi:

```js
myList = [ 23, 42, 109 ];
myList.length;              // 3

myList[14] = "Hello";
myList.length;              // 15

myList;                     // [ 23, 42, 109, empty x 11, "Hello" ]

// trông giống như một vị trí thực sự với
// một giá trị `undefined` thực sự bên trong nó,
// nhưng hãy cẩn thận, đó là một cái bẫy!
myList[9];                  // undefined
```

Bạn có thể tự hỏi tại sao các vị trí rỗng lại tồi tệ đến vậy? Một lý do: có những API trong JS, chẳng hạn như phương thức `map(..)` của mảng, nơi mà các vị trí rỗng bị bỏ qua một cách đầy bất ngờ! Đừng bao giờ, không bao giờ cố ý tạo các vị trí rỗng trong mảng của bạn. Đây không thể bàn cãi là một trong những "phần tồi tệ" (bad parts) của JS.

### Hàm (Functions)

Tôi không có quá nhiều điều cụ thể để nói về hàm ở đây, ngoại trừ việc chỉ ra rằng chúng cũng là các kiểu phụ của đối tượng. Điều này có nghĩa là bên cạnh khả năng thực thi, chúng cũng có thể có các thuộc tính có tên được thêm vào hoặc truy cập từ chúng.

Các hàm có hai thuộc tính được xác định trước mà bạn có thể thấy mình tương tác, cụ thể là cho các mục đích siêu lập trình (meta-programming):

```js
function help(opt1,opt2,...remainingOpts) {
    // ..
}

help.name;          // "help"
help.length;        // 2
```

Thuộc tính `length` của một hàm là số lượng các tham số được định nghĩa tường minh của nó, tính đến nhưng không bao gồm tham số có giá trị mặc định được định nghĩa (ví dụ: `param = 42`) hoặc một "tham số còn lại" (rest parameter - ví dụ: `...remainingOpts`).

#### Tránh gán thuộc tính cho đối tượng hàm (Avoid Setting Function-Object Properties)

Bạn nên tránh gán các thuộc tính trên các đối tượng hàm. Nếu bạn muốn lưu trữ thông tin bổ sung liên kết với một hàm, hãy sử dụng một `Map(..)` (hoặc `WeakMap(..)`) riêng biệt với đối tượng hàm làm khóa (key), và thông tin bổ sung làm giá trị (value).

```js
extraInfo = new Map();

extraInfo.set(help,"this is some important information");

// sau đó:
extraInfo.get(help);   // "this is some important information"
```

## Đặc tính đối tượng (Object Characteristics)

Bên cạnh việc định nghĩa các hành vi cho các thuộc tính cụ thể, một số hành vi nhất định có thể định cấu hình trên toàn bộ đối tượng:

* extensible (khả năng mở rộng)
* sealed (niêm phong)
* frozen (đóng băng)

### Khả năng mở rộng (Extensible)

Khả năng mở rộng (extensibility) đề cập đến việc một đối tượng có thể có các thuộc tính mới được định nghĩa/thêm vào nó hay không. Theo mặc định, tất cả các đối tượng đều có thể mở rộng, nhưng bạn có thể tắt khả năng mở rộng của một đối tượng:

```js
myObj = {
    favoriteNumber: 42
};

myObj.firstName = "Kyle";                  // hoạt động bình thường

Object.preventExtensions(myObj);

myObj.nicknames = [ "getify", "ydkjs" ];   // thất bại
myObj.favoriteNumber = 123;                // hoạt động bình thường
```

Ở chế độ không nghiêm ngặt (non-strict-mode), một phép gán tạo thuộc tính mới sẽ âm thầm thất bại, trong khi ở chế độ nghiêm ngặt (strict mode), một ngoại lệ (exception) sẽ được ném ra.

### Niêm phong (Sealed)

// CẦN LÀM (TODO)

### Đóng băng (Frozen)

// CẦN LÀM (TODO)

## Mở rộng MOP (Extending The MOP)

Như đã đề cập ở đầu chương này, các đối tượng trong JS hành xử theo một bộ quy tắc được gọi là Giao thức Siêu đối tượng (Metaobject Protocol - MOP)[^mop]. Giờ đây khi chúng ta đã hiểu đầy đủ hơn về cách thức hoạt động mặc định của đối tượng, chúng ta muốn chuyển sự chú ý sang cách chúng ta có thể can thiệp vào một số hành vi mặc định này và ghi đè/tùy biến chúng.

// CẦN LÀM (TODO)

## Chuỗi `[[Prototype]]` (`[[Prototype]]` Chain)

Một trong những đặc tính quan trọng nhất, nhưng lại ít hiển hiện rõ ràng nhất, của một đối tượng (một phần của MOP) được gọi là "chuỗi nguyên mẫu" (prototype chain); ký hiệu quy chuẩn trong đặc tả JS chính thức là `[[Prototype]]`. Hãy chắc chắn rằng bạn không nhầm lẫn `[[Prototype]]` này với một thuộc tính công khai có tên là `prototype`. Bất chấp sự tương đồng về tên gọi, đây là những khái niệm hoàn toàn khác biệt.

`[[Prototype]]` là một liên kết nội bộ mà một đối tượng nhận được theo mặc định khi nó được tạo ra, trỏ tới một đối tượng khác. Liên kết này là một đặc tính ẩn, thường rất tinh vi của một đối tượng, nhưng nó có những tác động sâu sắc đến cách các tương tác với đối tượng sẽ diễn ra. Nó được gọi là một "chuỗi" (chain) vì một đối tượng liên kết đến một đối tượng khác, đối tượng đó lại liên kết đến một đối tượng khác nữa, ... và cứ tiếp tục như vậy. Có một *điểm kết thúc* hoặc *đỉnh* của chuỗi này, nơi liên kết dừng lại và không còn nơi nào xa hơn để đi tiếp. Chúng ta sẽ nói thêm về điều đó ngay sau đây.

Chúng ta đã thấy một số hệ quả của liên kết `[[Prototype]]` trong Chương 1. Chẳng hạn, theo mặc định, tất cả các đối tượng đều được liên kết `[[Prototype]]` tới đối tượng tích hợp sẵn có tên là `Object.prototype`.

| CẢNH BÁO: |
| :--- |
| Bản thân cái tên `Object.prototype` đó có thể gây nhầm lẫn, vì nó sử dụng một thuộc tính có tên là `prototype`. `[[Prototype]]` và `prototype` có mối quan hệ như thế nào!? Hãy tạm gác lại những câu hỏi/sự bối rối đó một chút, vì chúng ta sẽ quay trở lại và giải thích sự khác biệt giữa `[[Prototype]]` và `prototype` ở phần sau của chương này. Tại thời điểm hiện tại, bạn chỉ cần chấp nhận sự hiện diện của đối tượng tích hợp sẵn quan trọng nhưng có cái tên kỳ lạ này: `Object.prototype`. |

Hãy cùng xem xét đoạn mã sau:

```js
myObj = {
    favoriteNumber: 42
};
```

Điều đó trông có vẻ quen thuộc từ Chương 1. Nhưng những gì bạn *không nhìn thấy* trong đoạn mã này là đối tượng ở đó đã được tự động liên kết (thông qua `[[Prototype]]` nội bộ của nó) tới đối tượng `Object.prototype` tích hợp sẵn nhưng có tên gọi kỳ lạ đó.

Khi chúng ta làm những việc như:

```js
myObj.toString();                             // "[object Object]"

myObj.hasOwnProperty("favoriteNumber");   // true
```

Chúng ta đang tận dụng liên kết `[[Prototype]]` nội bộ này mà không thực sự nhận ra điều đó. Vì `myObj` không có các thuộc tính `toString` hoặc `hasOwnProperty` được định nghĩa trên nó, các truy cập thuộc tính đó thực sự kết thúc bằng việc **ỦY QUYỀN** (DELEGATING) lượt truy cập để tiếp tục quá trình tra cứu dọc theo chuỗi `[[Prototype]]`.

Vì `myObj` được liên kết `[[Prototype]]` tới đối tượng có tên là `Object.prototype`, việc tra cứu các thuộc tính `toString` và `hasOwnProperty` sẽ tiếp tục trên đối tượng đó; và quả thực, những phương thức này được tìm thấy ở đó!

Khả năng để `myObj.toString` truy cập thuộc tính `toString` mặc dù bản thân nó không thực sự sở hữu thuộc tính đó, thường được gọi là "kế thừa" (inheritance), hay cụ thể hơn là "kế thừa theo nguyên mẫu" (prototypal inheritance). Các thuộc tính `toString` và `hasOwnProperty`, cùng với nhiều thuộc tính khác, được cho là các "thuộc tính được kế thừa" (inherited properties) trên `myObj`.

| GHI CHÚ: |
| :--- |
| Tôi cảm thấy vô cùng ức chế với việc sử dụng từ "kế thừa" (inheritance) ở đây -- nó đáng lẽ phải được gọi là "ủy quyền" (delegation)! -- nhưng đó là cách hầu hết mọi người gọi nó, vì vậy chúng ta sẽ miễn cưỡng tuân theo và sử dụng cùng một thuật ngữ đó vào lúc này (dù trong sự phản đối, được đặt trong dấu ngoặc kép ""). Tôi sẽ dành những phản biện của mình cho một phụ lục của cuốn sách này. |

`Object.prototype` có một số thuộc tính và phương thức tích hợp sẵn, tất cả đều được "kế thừa" bởi bất kỳ đối tượng nào được liên kết `[[Prototype]]`, trực tiếp hoặc gián tiếp thông qua liên kết của một đối tượng khác, tới `Object.prototype`.

Một số thuộc tính "được kế thừa" phổ biến từ `Object.prototype` bao gồm:

* `constructor`
* `__proto__`
* `toString()`
* `valueOf()`
* `hasOwnProperty(..)`
* `isPrototypeOf(..)`

Hãy nhớ lại `hasOwnProperty(..)`, phương thức mà chúng ta đã thấy trước đó cung cấp cho chúng ta một kiểm tra dạng boolean xem liệu một thuộc tính nhất định (theo tên chuỗi) có thuộc sở hữu của một đối tượng hay không:

```js
myObj = {
    favoriteNumber: 42
};

myObj.hasOwnProperty("favoriteNumber");   // true
```

Việc một tiện ích quan trọng như `hasOwnProperty(..)` được đưa vào chuỗi `[[Prototype]]` của Object dưới dạng một phương thức thể hiện (instance method), thay vì được định nghĩa như một tiện ích tĩnh (static utility), luôn được coi là có phần đáng tiếc (về tổ chức ngữ nghĩa, xung đột tên gọi, v.v.).

Kể từ ES2022, JS cuối cùng đã bổ sung phiên bản tĩnh của tiện ích này: `Object.hasOwn(..)`.

```js
myObj = {
    favoriteNumber: 42
};

Object.hasOwn(myObj,"favoriteNumber");   // true
```

Dạng này hiện được coi là lựa chọn ưu tiên hơn và mạnh mẽ hơn, và dạng phương thức thể hiện (`hasOwnProperty(..)`) nhìn chung bây giờ nên tránh sử dụng.

Có phần đáng tiếc và thiếu nhất quán là, (cho đến nay, tại thời điểm viết sách) vẫn chưa có các tiện ích tĩnh tương ứng, chẳng hạn như `Object.isPrototype(..)` (thay cho phương thức thể hiện `isPrototypeOf(..)`). Nhưng ít nhất `Object.hasOwn(..)` đã tồn tại, và đó là một sự tiến bộ.

### Tạo một đối tượng với một `[[Prototype]]` khác (Creating An Object With A Different `[[Prototype]]`)

Theo mặc định, bất kỳ đối tượng nào bạn tạo ra trong chương trình của mình sẽ được liên kết `[[Prototype]]` tới đối tượng `Object.prototype` đó. Tuy nhiên, bạn có thể tạo một đối tượng với một liên kết khác như thế này:

```js
myObj = Object.create(differentObj);
```

Phương thức `Object.create(..)` nhận đối số đầu tiên làm giá trị để thiết lập cho `[[Prototype]]` của đối tượng mới được tạo.

Một nhược điểm của cách tiếp cận này là bạn không sử dụng cú pháp literal `{ .. }`, do đó ban đầu bạn không định nghĩa bất kỳ nội dung nào cho `myObj`. Sau đó bạn thường phải định nghĩa từng thuộc tính một, sử dụng `=`.

| GHI CHÚ: |
| :--- |
| Đối số thứ hai, tùy chọn của `Object.create(..)` -- giống như đối số thứ hai của `Object.defineProperties(..)` như đã thảo luận ở phần trước -- là một đối tượng chứa các thuộc tính nắm giữ các bộ mô tả để khởi tạo định nghĩa ban đầu cho đối tượng mới. Trong thực tế, dạng này hiếm khi được sử dụng, có lẽ vì việc chỉ định đầy đủ các bộ mô tả thay vì chỉ các cặp tên/giá trị sẽ rườm rà hơn. Nhưng nó có thể hữu ích trong một số trường hợp hạn chế. |

Ngoài ra, nhưng ít được khuyến khích hơn, bạn có thể sử dụng cú pháp literal `{ .. }` cùng với một thuộc tính đặc biệt (và trông khá kỳ lạ!):

```js
myObj = {
    __proto__: differentObj,

    // .. phần còn lại của định nghĩa đối tượng
};
```

| CẢNH BÁO: |
| :--- |
| Thuộc tính trông kỳ lạ `__proto__` đã xuất hiện trong một số engine JS hơn 20 năm qua, nhưng chỉ mới được chuẩn hóa trong JS kể từ ES6 (vào năm 2015). Ngay cả như vậy, nó cũng chỉ được thêm vào Phụ lục B của tài liệu đặc tả[^specApB], nơi liệt kê các tính năng mà TC39 miễn cưỡng đưa vào vì chúng đã tồn tại phổ biến trong nhiều engine JS dựa trên trình duyệt và do đó là một thực tế hiển nhiên (de-facto) ngay cả khi chúng không bắt nguồn từ TC39. Do đó, tính năng này được đặc tả "đảm bảo" tồn tại trong tất cả các engine JS tuân thủ chuẩn trên trình duyệt, nhưng không nhất thiết được đảm bảo hoạt động trong các engine JS độc lập khác. Node.js sử dụng engine JS (v8) từ trình duyệt Chrome, vì vậy Node.js có được `__proto__` theo mặc định/ngẫu nhiên. Hãy cẩn thận khi sử dụng `__proto__` và luôn ý thức về tất cả các môi trường engine JS mà mã của bạn sẽ chạy trên đó. |

Cho dù bạn sử dụng `Object.create(..)` hay `__proto__`, đối tượng được tạo ra đang đề cập thường sẽ được liên kết `[[Prototype]]` tới một đối tượng khác biệt so với `Object.prototype` mặc định.

#### Liên kết `[[Prototype]]` rỗng (Empty `[[Prototype]]` Linkage)

Chúng ta đã đề cập ở trên rằng chuỗi `[[Prototype]]` phải dừng lại ở một nơi nào đó, để các quá trình tra cứu không tiếp diễn mãi mãi. `Object.prototype` thường là đỉnh/điểm kết thúc của mọi chuỗi `[[Prototype]]`, vì `[[Prototype]]` của chính nó là `null`, và do đó không còn nơi nào khác để tiếp tục tìm kiếm.

Tuy nhiên, bạn cũng có thể định nghĩa các đối tượng với giá trị `null` cho chính `[[Prototype]]` của chúng, chẳng hạn như:

```js
emptyObj = Object.create(null);
// hoặc: emptyObj = { __proto__: null }

emptyObj.toString;   // undefined
```

Việc tạo một đối tượng không có liên kết `[[Prototype]]` tới `Object.prototype` có thể khá hữu ích. Ví dụ, như đã đề cập trong Chương 1, các cấu trúc `in` và `for..in` sẽ tham khảo chuỗi `[[Prototype]]` để tìm các thuộc tính được kế thừa. Nhưng điều này có thể không mong muốn, vì bạn có thể không muốn điều gì đó như `"toString" in myObj` trả về kết quả thành công.

Hơn nữa, một đối tượng có `[[Prototype]]` rỗng sẽ an toàn trước mọi sự va chạm "kế thừa" ngẫu nhiên giữa tên thuộc tính của chính nó và những thuộc tính mà nó "kế thừa" từ nơi khác. Những kiểu đối tượng (rất hữu ích!) này đôi khi được gọi theo cách thông dụng là "các đối tượng từ điển" (dictionary objects).

### `[[Prototype]]` và `prototype` (`[[Prototype]]` vs `prototype`)

Bạn có nhận thấy tên thuộc tính công khai `prototype` trong tên gọi/vị trí của đối tượng đặc biệt này, `Object.prototype` không? Điều đó thực chất là gì?

`Object` chính là hàm `Object(..)`; theo mặc định, tất cả các hàm (bản thân chúng cũng là các đối tượng!) đều có một thuộc tính `prototype` như vậy trên chúng, trỏ tới một đối tượng.

Và đây chính là nơi mà sự xung đột tên gọi giữa `[[Prototype]]` và `prototype` thực sự gây rắc rối cho chúng ta. Thuộc tính `prototype` trên một hàm không hề định nghĩa bất kỳ liên kết nào mà chính hàm đó trải nghiệm. Thật vậy, các hàm (với tư cách là đối tượng) có liên kết `[[Prototype]]` nội bộ riêng của chúng ở một nơi khác -- chúng ta sẽ nói thêm về điều này sau một giây nữa.

Đúng hơn, thuộc tính `prototype` trên một hàm đề cập đến một đối tượng mà bất kỳ đối tượng nào khác được tạo ra khi gọi hàm đó với từ khóa `new` sẽ *liên kết TỚI*:

```js
myObj = {};

// về cơ bản giống như:
myObj = new Object();
```

Bởi vì cú pháp literal của đối tượng `{ .. }` về bản chất giống như lời gọi `new Object()`, đối tượng tích hợp sẵn được đặt tên/định vị tại `Object.prototype` được sử dụng làm giá trị `[[Prototype]]` nội bộ cho đối tượng mới mà chúng ta tạo và đặt tên là `myObj`.

Phù! Đúng là một chủ đề trở nên khó hiểu hơn đáng kể chỉ vì sự trùng lặp tên gọi giữa `[[Prototype]]` và `prototype`!

----

Nhưng bản thân các hàm (với tư cách là các đối tượng!) liên kết đến đâu, xét về phương diện `[[Prototype]]`? Chúng liên kết tới `Function.prototype`, lại là một đối tượng tích hợp sẵn khác, nằm tại thuộc tính `prototype` trên hàm `Function(..)`.

Nói cách khác, bạn có thể nghĩ rằng bản thân các hàm đã được "tạo ra" bởi một lời gọi `new Function(..)`, và sau đó được liên kết `[[Prototype]]` tới đối tượng `Function.prototype`. Đối tượng này chứa các thuộc tính/phương thức mà tất cả các hàm đều "kế thừa" theo mặc định, chẳng hạn như `toString()` (để tuần tự hóa mã nguồn của một hàm thành chuỗi) và `call(..)` / `apply(..)` / `bind(..)` (chúng ta sẽ giải thích những phương thức này ở phần sau của cuốn sách).

## Hành vi của đối tượng (Objects Behavior)

Các thuộc tính trên đối tượng được định nghĩa và kiểm soát nội bộ bởi một siêu đối tượng "bộ mô tả" (descriptor), bao gồm các đặc tính như `value` (giá trị hiện tại của thuộc tính) và `enumerable` (một giá trị boolean kiểm soát việc thuộc tính có được đưa vào danh sách chỉ dành cho các thuộc tính/tên thuộc tính có thể liệt kê hay không).

Cách thức đối tượng và các thuộc tính của chúng hoạt động trong JS được gọi là "giao thức siêu đối tượng" (metaobject protocol - MOP)[^mop]. Chúng ta có thể kiểm soát hành vi chính xác của các thuộc tính thông qua `Object.defineProperty(..)`, cũng như các hành vi trên toàn bộ đối tượng với `Object.freeze(..)`. Nhưng mạnh mẽ hơn nữa, chúng ta có thể can thiệp và ghi đè một số hành vi mặc định trên đối tượng bằng cách sử dụng các Symbol được định nghĩa trước đặc biệt.

Nguyên mẫu (Prototypes) là các liên kết nội bộ giữa các đối tượng cho phép việc truy cập thuộc tính hoặc phương thức trên một đối tượng -- nếu thuộc tính/phương thức được yêu cầu vắng mặt -- được xử lý bằng cách "ủy quyền" (delegating) việc tra cứu truy cập đó cho một đối tượng khác. Khi sự ủy quyền liên quan đến một phương thức, ngữ cảnh để phương thức chạy bên trong được chia sẻ từ đối tượng ban đầu tới đối tượng mục tiêu thông qua từ khóa `this` .

[^mop]: "Metaobject", Wikipedia; https://en.wikipedia.org/wiki/Metaobject ; Truy cập tháng 7 năm 2022.

[^specApB]: "Phụ lục B: Các tính năng ECMAScript bổ sung cho trình duyệt web", Đặc tả ngôn ngữ ECMAScript 2022; https://262.ecma-international.org/13.0/#sec-additional-ecmascript-features-for-web-browsers ; Truy cập tháng 7 năm 2022.
