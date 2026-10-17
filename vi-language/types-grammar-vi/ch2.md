# You Don't Know JS Yet: Types & Grammar - Tái bản lần 2
# Chương 2: Các hành vi của giá trị nguyên thủy (Primitive Behaviors)

| GHI CHÚ: |
| :--- |
| Đang trong quá trình hoàn thiện |

Cho đến thời điểm này, chúng ta đã tìm hiểu bảy kiểu giá trị nguyên thủy tích hợp sẵn trong JS: `null`, `undefined`, `boolean`, `string`, `number`, `bigint`, và `symbol`.

Chương 1 chứa lượng kiến thức khá đồ sộ cần tiếp thu, sâu sắc và phức tạp hơn rất nhiều so với những gì tôi tin phần lớn độc giả từng mong đợi. Nếu bạn vẫn đang phải nghỉ lấy hơi sau khi đọc hết những điều đó, đừng ngại ngần tạm dừng một chút trước khi tiếp tục tại đây!

Một khi đầu óc bạn đã thông thoáng và sẵn sàng bước tiếp, chúng ta hãy cùng đào sâu vào những hành vi nhất định được quy định bởi các kiểu giá trị cho toàn bộ các giá trị tương ứng của chúng. Chúng ta sẽ xem xét cẩn thận và kỹ lưỡng hơn về tất cả các hành vi đa dạng này.

## Tính bất biến của kiểu nguyên thủy (Primitive Immutability)

Tất cả các giá trị nguyên thủy đều là bất biến (*immutable*), có nghĩa là không có bất cứ thứ gì trong một chương trình JS có thể can thiệp vào nội dung bên trong của giá trị đó và sửa đổi nó theo bất kỳ cách nào.

```js
myAge = 42;

// sau đó:

myAge = 43;
```

Câu lệnh `myAge = 43` không làm thay đổi giá trị. Nó gán lại một giá trị khác `43` cho biến `myAge`, thay thế hoàn toàn giá trị `42` trước đó.

Các giá trị mới cũng được tạo ra thông qua nhiều phép toán khác nhau, nhưng một lần nữa, chúng không hề sửa đổi giá trị ban đầu:

```js
42 + 1;             // 43

"Hello" + "!";      // "Hello!"
```

Các giá trị `43` và `"Hello!"` là những giá trị mới, hoàn toàn tách biệt tương ứng với các giá trị `42` và `"Hello"` ban đầu.

Ngay cả một giá trị chuỗi, trông có vẻ chỉ như một mảng gồm các ký tự -- mà nội dung của mảng thì thường có thể thay đổi được (*mutable*) -- cũng là bất biến:

```js
greeting = "Hello.";

greeting[5] = "!";

console.log(greeting);      // Hello.
```

| CẢNH BÁO: |
| :--- |
| Trong non-strict mode, việc gán cho một thuộc tính chỉ đọc (như `greeting[5] = ..`) sẽ âm thầm thất bại. Trong strict-mode, phép gán không được phép này sẽ ném ra ngoại lệ. |

Bản chất bất biến của các giá trị nguyên thủy *hoàn toàn không bị ảnh hưởng* bởi cách khai báo biến hoặc thuộc tính đối tượng đang chứa giá trị đó. Chẳng hạn, dù `const`, `let`, hay `var` được sử dụng để khai báo biến `greeting` ở trên, thì giá trị chuỗi mà nó chứa vẫn luôn là bất biến.

`const` không tạo ra các giá trị bất biến, nó khai báo các biến không thể bị gán lại (nói cách khác là các phép gán bất biến - *immutable assignments*) -- hãy tham khảo cuốn "Scope & Closures" trong bộ sách này để biết thêm chi tiết.

Một thuộc tính trên một đối tượng có thể được đánh dấu là chỉ đọc -- với thuộc tính bộ mô tả `writable: false`, như đã thảo luận trong cuốn "Objects & Classes" của bộ sách này. Nhưng điều đó vẫn không có tác động gì đến bản chất của giá trị, mà chỉ nhằm ngăn chặn việc gán lại thuộc tính đó mà thôi.

### Giá trị nguyên thủy có thuộc tính? (Primitives With Properties?)

Ngoài ra, các thuộc tính *không thể* được thêm vào bất kỳ giá trị nguyên thủy nào:

```js
greeting = "Hello.";

greeting.isRendered = true;

greeting.isRendered;        // undefined
```

Đoạn mã này trông như thể đang thêm thuộc tính `isRendered` vào giá trị trong `greeting`, nhưng phép gán này âm thầm thất bại (ngay cả trong strict-mode).

Việc truy cập thuộc tính hoàn toàn không được phép dưới bất kỳ hình thức nào trên các giá trị nguyên thủy nullish là `null` và `undefined`. Nhưng các thuộc tính *lại có thể* được truy cập trên tất cả các giá trị nguyên thủy khác -- vâng, nghe có vẻ phản trực giác.

Ví dụ, tất cả các giá trị chuỗi đều có một thuộc tính chỉ đọc là `length`:

```js
greeting = "Hello.";

greeting.length;            // 6
```

`length` không thể thiết lập, nhưng nó có thể được truy cập, và nó phơi bày số lượng code-unit được lưu trữ trong giá trị đó (xem mục "Mã hóa ký tự trong JS" ở Chương 1), điều này thường tương ứng với số lượng ký tự trong chuỗi.

| GHI CHÚ: |
| :--- |
| Đại loại là như vậy. Đối với hầu hết các ký tự tiêu chuẩn, điều đó đúng; một ký tự là một code-point, và là một code-unit. Tuy nhiên, như đã giải thích trong Chương 1, các ký tự Unicode mở rộng có code-point trên `65535` sẽ được lưu trữ dưới dạng hai code-unit (các nửa đại diện surrogate). Do đó, đối với mỗi ký tự như vậy, `length` sẽ tính `2` vào số đếm của nó, mặc dù về mặt thị giác ký tự đó chỉ hiển thị như một biểu tượng đơn lẻ. |

Các giá trị nguyên thủy non-nullish cũng có một vài phương thức tích hợp sẵn tiêu chuẩn có thể truy cập được:

```js
greeting = "Hello.";

greeting.toString();    // "Hello." <-- dư thừa
greeting.valueOf();     // "Hello."
```

Ngoài ra, hầu hết các kiểu giá trị nguyên thủy đều định nghĩa các phương thức riêng của chúng với những hành vi đặc thù vốn có của kiểu đó. Chúng ta sẽ tìm hiểu những điều này ở phần sau của chương.

| GHI CHÚ: |
| :--- |
| Như đã đề cập ngắn gọn trong Chương 1, về mặt kỹ thuật, những kiểu truy cập thuộc tính/phương thức này trên các giá trị nguyên thủy được hỗ trợ bởi một hành vi ép kiểu ngầm định gọi là *auto-boxing* (tự động đóng hộp). Chúng ta sẽ tìm hiểu chi tiết về điều này trong mục "Đối tượng tự động" ở Chương 3. |

## Phép gán giá trị nguyên thủy (Primitive Assignments)

Bất kỳ phép gán một giá trị nguyên thủy nào từ một biến/đồ chứa này sang một biến/đồ chứa khác đều là phép *sao chép giá trị* (*value-copy*):

```js
myAge = 42;

yourAge = myAge;        // gán theo cơ chế sao chép giá trị (value-copy)

myAge;                  // 42
yourAge;                // 42
```

Ở đây, hai biến `myAge` và `yourAge` mỗi biến đều có một bản sao riêng của giá trị số `42`.

| GHI CHÚ: |
| :--- |
| Bên trong JS engine, *có thể* xảy ra trường hợp chỉ có một giá trị `42` duy nhất tồn tại trong bộ nhớ, và engine sẽ trỏ cả hai biến `myAge` và `yourAge` vào giá trị dùng chung đó. Vì các giá trị nguyên thủy là bất biến, nên engine JS làm như vậy cũng hoàn toàn an toàn, không có bất kỳ rủi ro nào. Nhưng điều quan trọng đối với chúng ta với tư cách là các nhà phát triển JS là: trong chương trình của mình, `myAge` và `yourAge` hoạt động như thể chúng nắm giữ bản sao giá trị riêng biệt của chính chúng, chứ không phải dùng chung. |

Nếu sau đó chúng ta gán lại `myAge` thành `43` (vào dịp sinh nhật của tôi chẳng hạn), điều đó không hề ảnh hưởng đến giá trị `42` vẫn đang được gán cho `yourAge`:

```js
myAge++;            // đại loại giống như: myAge = myAge + 1

myAge;              // 43
yourAge;            // 42 <-- không đổi
```

## Các hành vi của chuỗi (String Behaviors)

Các giá trị chuỗi sở hữu một số hành vi cụ thể mà mọi lập trình viên JS đều cần phải thấu hiểu.

### Truy cập ký tự trong chuỗi (String Character Access)

Mặc dù chuỗi không thực sự là mảng, nhưng JS cho phép truy cập theo phong cách mảng dạng `[ .. ]` tới một ký tự tại chỉ số dạng số (được đánh chỉ số bắt đầu từ `0`):

```js
greeting = "Hello!";

greeting[4];            // "o"
```

Nếu giá trị/biểu thức nằm giữa dấu ngoặc vuông `[ .. ]` không trả về một số, giá trị đó sẽ được ép kiểu ngầm định sang dạng biểu diễn số nguyên/toàn phần của nó (nếu có thể).

```js
greeting["4"];          // "o"
```

Nếu giá trị/biểu thức trả về một số nằm ngoài phạm vi số nguyên từ `0` đến `length - 1` (hoặc `NaN`), hoặc nếu nó không phải là kiểu giá trị `number`, việc truy cập này sẽ được xử lý như một phép truy cập thuộc tính với tên thuộc tính là chuỗi tương đương. Nếu phép truy cập thuộc tính thất bại, kết quả sẽ là `undefined`.

| GHI CHÚ: |
| :--- |
| Chúng ta sẽ tìm hiểu sâu về ép kiểu (coercion) ở phần sau của cuốn sách. |

### Lặp qua các ký tự (Character Iteration)

Chuỗi không phải là mảng, nhưng chúng chắc chắn mô phỏng mảng rất sát theo nhiều cách. Một trong những hành vi như vậy là: tương tự mảng, chuỗi là các iterable (có thể lặp). Điều này có nghĩa là các ký tự (code-unit) của một chuỗi có thể được lặp qua từng ký tự một:

```js
myName = "Kyle";

for (let char of myName) {
    console.log(char);
}
// K
// y
// l
// e

chars = [ ...myName ];
chars;
// [ "K", "y", "l", "e" ]
```

Các giá trị như chuỗi và mảng là các iterable (thông qua toán tử `...`, vòng lặp `for..of`, và `Array.from(..)`), nếu chúng phơi bày một phương thức tạo iterator tại vị trí thuộc tính symbol đặc biệt `Symbol.iterator` (xem mục "Các Symbol nổi tiếng" ở Chương 1):

```js
myName = "Kyle";
it = myName[Symbol.iterator]();

it.next();      // { value: "K", done: false }
it.next();      // { value: "y", done: false }
it.next();      // { value: "l", done: false }
it.next();      // { value: "e", done: false }
it.next();      // { value: undefined, done: true }
```

| GHI CHÚ: |
| :--- |
| Chi tiết cụ thể của giao thức iterator, bao gồm cả việc kết quả `{ value: "e" .. }` vẫn trả về `done: false`, được trình bày chi tiết trong cuốn "Sync & Async" của bộ sách này. |

### Tính toán độ dài (Length Computation)

Như đã đề cập trong Chương 1, các giá trị chuỗi có thuộc tính `length` tự động phơi bày độ dài của chuỗi; thuộc tính này chỉ có thể đọc; mọi nỗ lực gán giá trị cho nó đều bị âm thầm bỏ qua.

Giá trị `length` được báo cáo phần nào tương ứng với số lượng ký tự trong chuỗi (thực chất là các code-unit), nhưng như chúng ta đã thấy trong Chương 1, vấn đề trở nên phức tạp hơn nhiều khi có sự xuất hiện của các ký tự Unicode.

Hầu hết mọi người nhận diện các biểu tượng một cách trực quan như các ký tự riêng biệt; khái niệm về một biểu tượng thị giác độc lập này được gọi là một *grapheme*, hoặc một *grapheme cluster* (cụm grapheme). Do đó, khi đếm "độ dài" của một chuỗi, chúng ta thường ngầm hiểu là đang đếm số lượng grapheme.

Nhưng máy tính lại không xử lý các ký tự theo cách đó.

Trong JS, mỗi *ký tự* là một code-unit (16 bit), với giá trị code-point bằng hoặc nhỏ hơn `65535`. Thuộc tính `length` của một chuỗi luôn đếm số lượng code-unit trong giá trị chuỗi, chứ không phải code-point. Một code-unit có thể tự thân đại diện cho một ký tự đơn lẻ, hoặc nó có thể là một phần của một cặp surrogate, hoặc có thể được kết hợp với một ký tự *combining* (ký tự kết hợp) liền kề, hoặc là một phần của một grapheme cluster. Do vậy, `length` không khớp với khái niệm thông thường về việc đếm các ký tự/grapheme trực quan.

Để tiến gần hơn đến một *độ dài grapheme* đúng như kỳ vọng/trực giác cho một chuỗi, giá trị chuỗi trước tiên cần được chuẩn hóa bằng `normalize("NFC")` (xem "Chuẩn hóa Unicode" ở Chương 1) nhằm tạo ra các code-unit *hợp thành* (*composed*) (nếu có thể), phòng trường hợp các ký tự ban đầu vốn được lưu trữ ở dạng *phân tách* (*decomposed*) thành các code-unit rời rạc.

Ví dụ:

```js
favoriteItem = "teléfono";
favoriteItem.length;            // 9 -- ôi không!

favoriteItem = favoriteItem.normalize("NFC");
favoriteItem.length;            // 8 -- phù, may quá!
```

Thật không may, như đã thấy trong Chương 1, chúng ta vẫn sẽ đối mặt với khả năng các ký tự có code-point lớn hơn `65535`, và do đó cần một cặp surrogate để biểu diễn. Những ký tự như vậy sẽ bị tính gấp đôi trong `length`:

```js
// "☎" === "\u260E"
oldTelephone = "☎";
oldTelephone.length;            // 1

// "📱" === "\u{1F4F1}" === "\uD83D\uDCF1"
cellphone = "📱";
cellphone.length;               // 2 -- ối chà!
```

Vậy chúng ta phải làm gì?

Một giải pháp là sử dụng cơ chế lặp ký tự (thông qua toán tử `...`) như chúng ta đã thấy trong phần trước, vì nó tự động gom các cặp surrogate để trả về từng ký tự hoàn chỉnh:

```js
cellphone = "📱";
cellphone.length;               // 2 -- ối chà!
[ ...cellphone ].length;        // 1 -- nhẹ cả người!
```

Thế nhưng, đáng tiếc thay, các grapheme cluster (như đã giải thích trong Chương 1) lại tiếp tục "ngáng đường" việc tính toán độ dài chuỗi. Ví dụ, nếu chúng ta lấy biểu tượng emoji ngón tay cái chúc xuống (`"\u{1F44E}"`) và thêm vào đó ký tự điều chỉnh tông màu da ngăm đen trung bình (`"\u{1F3FE}"`), chúng ta thu được:

```js
// "👎🏾" = "\u{1F44E}\u{1F3FE}"
thumbsDown = "👎🏾";

thumbsDown.length;              // 4 -- ôi không!
[ ...thumbsDown ].length;       // 2 -- vẫn sai!
```

Như bạn có thể thấy, đây là hai code-point riêng biệt (không phải cặp surrogate) mà nhờ vào thứ tự và vị trí liền kề của chúng, bộ render Unicode của máy tính sẽ vẽ biểu tượng ngón tay cái chúc xuống nhưng với màu da tối hơn màu mặc định. Do đó, độ dài chuỗi tính toán được bằng `2`.

Để nhận diện các cụm code-point như vậy thành một "ký tự" duy nhất nhằm phục vụ mục đích đếm độ dài, bạn sẽ phải tái hiện lại phần lớn logic hiển thị Unicode phức tạp của cả nền tảng hệ điều hành. Có những thư viện tuyên bố làm được điều đó, nhưng chúng không hẳn là hoàn hảo, và chúng phải trả giá đắt bằng một lượng mã nguồn bổ sung cồng kềnh.

| GHI CHÚ: |
| :--- |
| Là một người dùng Twitter, bạn có thể kỳ vọng mình gõ được 280 biểu tượng emoji ngón tay cái chúc xuống trong một tweet đơn lẻ, vì trông nó chỉ như một ký tự duy nhất. Nhưng Twitter đếm `"👎"` (ngón cái mặc định), `"👎🏾"` (ngón cái da ngăm đen), và thậm chí cả cụm grapheme cluster biểu tượng gia đình `"👩‍👩‍👦‍👦"` đều là 2 ký tự mỗi biểu tượng, mặc dù độ dài chuỗi tương ứng của chúng (dưới góc nhìn của JS) lần lượt là `2`, `4`, và `7`; do đó, bạn chỉ có thể đưa vào một nửa số lượng emoji (140 thay vì 280) trong một tweet. Thực tế, Twitter đã áp dụng thay đổi này vào năm 2018 để bình đẳng hóa việc tính toán cho tất cả các ký tự Unicode, quy định ở mức 2 ký tự cho mỗi biểu tượng. [^TwitterUnicode] Đó là một sự thay đổi đáng hoan nghênh cho người dùng Twitter, đặc biệt là những người muốn dùng các ký tự emoji thể hiện chính xác nhất giới tính, tông màu da mong muốn, v.v. Dẫu vậy, việc Twitter chọn đếm tất cả các biểu tượng Unicode/emoji là 2 ký tự thay vì 1 ký tự (grapheme) trực quan hơn quả thực vẫn *khá* kỳ lạ. |

Việc đếm *độ dài* của một chuỗi sao cho khớp với trực giác của con người là một nhiệm vụ thử thách đáng kinh ngạc, có lẽ nó giống một môn nghệ thuật hơn là khoa học. Chúng ta có thể đạt được các giá trị xấp xỉ chấp nhận được trong nhiều trường hợp, nhưng vẫn có vô vàn trường hợp khác khiến chương trình của chúng ta bối rối.

### Quốc tế hóa (i18n) và Bản địa hóa (l10n)

Để đáp ứng nhu cầu ngày càng tăng về việc các chương trình JS phải vận hành đúng như kỳ vọng trong mọi bối cảnh ngôn ngữ/văn hóa quốc tế, ủy ban ECMAScript cũng phát hành Quy chuẩn API Quốc tế hóa ECMAScript (ECMAScript Internationalization API). [^INTLAPI]

Một chương trình JS mặc định áp dụng locale/ngôn ngữ tùy theo môi trường đang chạy chương trình đó (trang trình duyệt web, một tiến trình Node, v.v.). Locale đang có hiệu lực sẽ ảnh hưởng đến việc sắp xếp (và so sánh giá trị), định dạng, cùng một số hành vi mặc định khác. Những hành vi bị biến đổi này có lẽ rõ ràng hơn một chút đối với chuỗi, nhưng chúng cũng có thể xuất hiện với các con số (và ngày tháng!).

Tuy nhiên, các ký tự trong chuỗi cũng có thể mang thông tin ngôn ngữ/locale được nhúng sẵn bên trong chúng, và thông tin này sẽ được ưu tiên hơn so với mặc định của môi trường. Nếu một ký tự chuỗi có tính chất mơ hồ/dùng chung giữa các ngôn ngữ/locale (chẳng hạn như chữ `"a"`), thiết lập môi trường mặc định sẽ được sử dụng.

Tùy thuộc vào nội dung của chuỗi, nó có thể được diễn giải theo thứ tự từ trái sang phải (LTR - Left-To-Right) hoặc từ phải sang trái (RTL - Right-To-Left). Do vậy, nhiều phương thức chuỗi mà chúng ta sẽ tìm hiểu sau đây sử dụng các từ mô tả mang tính logic trong tên gọi của chúng, chẳng hạn như "start", "end", "begin", "end", và "last", thay vì các thuật ngữ chỉ hướng như "left" và "right".

Ví dụ, tiếng Do Thái (Hebrew) và tiếng Ả Rập (Arabic) đều là các ngôn ngữ viết từ phải sang trái (RTL) phổ biến:

```js
hebrewHello = "\u{5e9}\u{5dc}\u{5d5}\u{5dd}";

console.log(hebrewHello);                       // שלום
```

Bạn có nhận thấy rằng ký tự được liệt kê đầu tiên trong chuỗi ký tự (`"\u{5e9}"`) thực tế lại là ký tự nằm ngoài cùng bên phải khi chuỗi được hiển thị không?

Mặc dù tiếng Hebrew là một ngôn ngữ RTL, bạn không hề gõ các ký tự trong chuỗi ký tự theo thứ tự đảo ngược (RTL) như cách chúng hiển thị. Bạn nhập các ký tự theo thứ tự logic, trong đó vị trí `0` là ký tự đầu tiên, vị trí `1` là ký tự thứ hai, v.v. Tầng hiển thị (rendering layer) mới là nơi các ký tự RTL được đảo ngược để xuất hiện theo đúng thứ tự thẩm mỹ.

Điều đó cũng có nghĩa là nếu bạn truy cập `hebrewHello[0]` (hoặc `hebrewHello.charAt(0)`) -- để lấy ký tự tại vị trí `0` -- bạn sẽ nhận được chữ `"ש"` vì đó là ký tự đầu tiên về mặt logic của chuỗi, chứ không phải `"ם"` (về mặt logic là ký tự cuối cùng của chuỗi). Phép truy cập vị trí chỉ số tuân theo vị trí logic, không phải vị trí hiển thị.

Dưới đây là một ví dụ tương tự trong một ngôn ngữ RTL khác là tiếng Ả Rập:

```js
arabicHello = "\u{631}\u{62d}\u{628}\u{627}";

console.log(arabicHello);                       // رحبا

console.log(arabicHello[0]);                    // ر
```

Các chương trình JS có thể ép buộc sử dụng một ngôn ngữ/locale cụ thể bằng cách dùng các API khác nhau trong `Intl`, chẳng hạn như `Intl.Collator`: [^INTLCollator]

```js
germanStringSorter = new Intl.Collator("de");

listOfGermanWords = [ /* .. */ ];

germanStringSorter.compare("Hallo","Welt");
// -1 (hoặc số âm)

// ví dụ phỏng theo MDN:
//
germanStringSorter.compare("Z","z");
// 1 (hoặc số dương)

caseFirstSorter = new Intl.Collator("de",{ caseFirst: "upper", });
caseFirstSorter.compare("Z","z");
// -1 (hoặc số âm)
```

Các chuỗi gồm nhiều từ có thể được phân đoạn bằng `Intl.Segmenter`: [^INTLSegmenter]

```js
arabicHelloWorld = "\u{645}\u{631}\u{62d}\u{628}\u{627} \
\u{628}\u{627}\u{644}\u{639}\u{627}\u{644}\u{645}";

console.log(arabicHelloWorld);      // مرحبا بالعالم

arabicSegmenter = new Intl.Segmenter("ar",{ granularity: "word" });

for (
    let { segment: word, isWordLike } of
    arabicSegmenter.segment(arabicHelloWorld)
) {
    if (isWordLike) {
        console.log(word);
    }
}
// مرحبا
//لعالم
```

| GHI CHÚ: |
| :--- |
| Phương thức `segment(..)` (từ các thể hiện của `Intl.Segmenter`) trả về một JS iterator tiêu chuẩn, và vòng lặp `for..of` ở đây sẽ tiêu thụ iterator đó. Bạn có thể tìm hiểu thêm về các giao thức lặp trong cuốn "Sync & Async" của bộ sách này. |

### So sánh chuỗi (String Comparison)

Các giá trị chuỗi có thể được so sánh (cho cả so sánh bằng và so sánh thứ tự quan hệ) với các giá trị chuỗi khác, bằng cách sử dụng nhiều toán tử tích hợp sẵn khác nhau. Cần luôn ghi nhớ rằng các phép so sánh như vậy phụ thuộc rất lớn vào nội dung thực tế của chuỗi, đặc biệt là các code-point bên dưới bắt nguồn từ các ký tự Unicode nằm ngoài BMP.

Cả so sánh bằng và so sánh quan hệ đều phân biệt chữ hoa chữ thường (case-sensitive), đối với bất kỳ ký tự nào mà chữ hoa và chữ thường được định nghĩa rõ ràng. Để thực hiện so sánh không phân biệt hoa thường, trước tiên hãy chuẩn hóa cách viết hoa/thường của cả hai giá trị (bằng `toUpperCase()` hoặc `toLowerCase()`).

#### So sánh bằng trên chuỗi (String Equality)

Các toán tử `===` và `==` (cùng với các dạng phủ định tương ứng của chúng là `!==` và `!=`) là cách phổ biến nhất để thực hiện so sánh bằng cho các giá trị nguyên thủy, bao gồm cả các giá trị chuỗi:

```js
"my name" === "my n\x61me";               // true

"my name" !== String.raw`my n\x61me`;     // true
```

Toán tử `===`[^StrictEquality] -- thường được gọi là "so sánh bằng nghiêm ngặt" ("strict equality") -- trước tiên sẽ kiểm tra xem kiểu dữ liệu có khớp nhau hay không, và nếu không khớp, nó trả về `false` ngay lập tức. Nếu các kiểu khớp nhau, sau đó nó sẽ kiểm tra xem các giá trị có giống hệt nhau không; đối với chuỗi, đây là một phép so sánh từng code-unit, từ đầu đến cuối.

Bất chấp cái tên "nghiêm ngặt" (strict), vẫn có những sắc thái đặc biệt đối với `===` (chẳng hạn như cách xử lý `-0` và `NaN`), nhưng chúng ta sẽ tìm hiểu những điều đó sau.

##### So sánh bằng có ép kiểu (Coercive Equality)

Ngược lại, toán tử `==`[^LooseEquality] -- thường được gọi là "so sánh bằng lỏng lẻo" ("loose equality") -- thực hiện *so sánh bằng có ép kiểu* (*coercive equality*): nếu kiểu giá trị của hai toán hạng không khớp nhau, `==` trước tiên sẽ ép kiểu một hoặc cả hai toán hạng cho đến khi các kiểu giá trị *thực sự* khớp nhau, và sau đó nó sẽ bàn giao phép so sánh đó sang cho `===` xử lý nội bộ.

Ép kiểu (Coercion) là một chủ đề cực kỳ quan trọng -- nó là một phần cố hữu trong hệ thống kiểu của JS, một trong ba trụ cột của ngôn ngữ này -- nhưng chúng ta sẽ chỉ giới thiệu ngắn gọn về nó ở đây trong chương này, và sẽ quay lại bàn luận chi tiết sau.

| GHI CHÚ: |
| :--- |
| Bạn có thể đã từng nghe một lời giải thích được trích dẫn rất thường xuyên, nhưng lại hoàn toàn thiếu chính xác: rằng sự khác biệt giữa `==` và `===` là `==` so sánh giá trị trong khi `===` so sánh cả giá trị lẫn kiểu dữ liệu. Điều đó không đúng, và bạn có thể tự mình đọc tài liệu đặc tả để kiểm chứng -- cả hai thuật toán đặc tả `isStrictlyEqual(..)` và `isLooselyEqual(..)` đều được liên kết dưới dạng chú thích ở các đoạn văn trước. Tóm lại: cả `==` và `===` đều nhận biết và nhạy cảm với kiểu của các toán hạng. Nếu kiểu của các toán hạng giống nhau, cả hai toán tử làm việc hoàn toàn y hệt nhau; nếu kiểu khác nhau, `==` ép buộc thực hiện ép kiểu cho đến khi các kiểu khớp nhau, trong khi `===` trả về `false` ngay lập tức. |

Việc các nhà phát triển quả quyết rằng toán tử `==` gây bối rối và quá khó dùng mà không gặp phải bất ngờ là điều vô cùng phổ biến (chính vì thế mới có sự ưu ái gần như tuyệt đối dành cho `===`). Tôi cho rằng quan điểm đó hoàn toàn sai lầm, và trên thực tế, các lập trình viên JS nên mặc định dùng `==` (và tránh dùng `===` nếu có thể). Nhưng chúng ta cần thêm nhiều phân tích để bảo vệ cho một phát biểu gây tranh cãi như vậy; hãy giữ lại những phản biện của bạn cho đến khi chúng ta thảo luận lại về nó sau nhé.

Hiện tại, để có được chút trực giác về bản chất ép kiểu của `==`, nhận định sáng tỏ nhất là: nếu các kiểu không khớp nhau, `==` *ưu tiên* so sánh dạng số. Điều đó có nghĩa là nó sẽ cố gắng chuyển đổi cả hai toán hạng thành số, rồi mới thực hiện kiểm tra bằng (tương tự như `===`).

Vì vậy, liên quan đến cuộc thảo luận hiện tại của chúng ta, phép so sánh bằng chuỗi thực sự *chỉ có thể* được kiểm tra nếu cả hai toán hạng vốn đã là chuỗi:

```js
// phép kiểm tra bằng chuỗi thực sự (thông qua === ở nội bộ):
"42" == "42";           // true
```

Bản thân `==` không thực sự tự mình thực hiện phép kiểm tra bằng chuỗi. Nếu kiểu giá trị của cả hai toán hạng đều là chuỗi, `==` chỉ việc bàn giao phép so sánh đó cho `===`. Nếu chúng không cùng là chuỗi, các bước ép kiểu trong `==` sẽ quy phép so sánh về dạng số thay vì dạng chuỗi:

```js
// so sánh bằng dạng số (không phải chuỗi!):
42 == "42";             // true
```

Chúng ta sẽ tìm hiểu về phép so sánh bằng dạng số ở phần sau của chương này.

##### So sánh bằng *thực sự* nghiêm ngặt (*Really* Strict Equality)

Bên cạnh `==` và `===`, JS cung cấp tiện ích `Object.is(..)`, tiện ích này trả về `true` nếu cả hai đối số *hoàn toàn đồng nhất*, và `false` trong trường hợp ngược lại (không có ngoại lệ hay sắc thái khác biệt nào):

```js
Object.is("42",42);             // false

Object.is("42","\x34\x32");     // true
```

Vì `===` bổ sung thêm một dấu `=` vào đuôi của `==` để làm cho hành vi của nó nghiêm ngặt hơn, tôi thường nói đùa nửa thật nửa đùa rằng tiện ích `Object.is(..)` giống như một toán tử `====` (thêm dấu `=` thứ tư), dành cho kiểu kiểm tra bằng thực-sự-nghiêm-ngặt-không-ngoại-lệ!

Dẫu vậy, `===` (và `==` nhờ vào việc ủy thác nội bộ cho `===`) là *cực kỳ dễ dự đoán*, không có bất kỳ ngoại lệ kỳ quặc nào khi nói đến việc so sánh hai giá trị mà bản thân chúng vốn đã thực sự là chuỗi. Tôi thực sự khuyến nghị nên sử dụng `==` cho các phép kiểm tra như vậy (hoặc `===`), và dành riêng `Object.is(..)` cho các trường hợp góc cạnh (vốn thường là dạng số).

#### So sánh quan hệ trên chuỗi (String Relational Comparisons)

Bên cạnh các phép kiểm tra bằng giữa các chuỗi, JS hỗ trợ các phép so sánh quan hệ giữa các giá trị nguyên thủy, chẳng hạn như chuỗi: `<`, `<=`, `>`, và `>=`.

Các phép toán `<` (nhỏ hơn) và `>` (lớn hơn) so sánh hai giá trị chuỗi theo thứ tự từ điển (lexicographically) -- giống như cách bạn sắp xếp các từ trong từ điển -- và do đó sẽ khá dễ hiểu:

```js
"hello" < "world";          // true
```

| GHI CHÚ: |
| :--- |
| Như đã đề cập trước đó, chương trình JS đang chạy có một locale mặc định, và các toán tử này sẽ so sánh dựa trên locale đó. |

Tương tự `==`, các toán tử `<` và `>` có tính chất ép kiểu về số. Bất kỳ giá trị nào không phải là số đều được ép kiểu thành số. Do đó, cách duy nhất để thực hiện so sánh quan hệ theo chuỗi là đảm bảo cả hai toán hạng vốn đã là các giá trị chuỗi.

Có thể hơi bất ngờ, nhưng `<` và `>` không có dạng tương đương so sánh nghiêm ngặt (strict-comparison), giống như cách `===` tránh việc ép kiểu của `==`. Các toán tử này luôn luôn ép kiểu (khi các kiểu không khớp nhau), và không có cách nào trong JS để né tránh điều đó.

Vậy điều gì sẽ xảy ra khi cả hai giá trị đều là các chuỗi *trông giống như số*?

```js
"100" < "11";               // true
```

Về mặt giá trị số, dĩ nhiên `100` *không thể* nhỏ hơn `11`.

Thế nhưng phép so sánh quan hệ giữa hai chuỗi lại áp dụng thứ tự từ điển. Do đó, ký tự `"0"` thứ hai (trong `"100"`) nhỏ hơn ký tự `"1"` thứ hai (trong `"11"`), và vì vậy `"100"` sẽ được xếp trước `"11"` trong một cuốn *từ điển*. Các toán tử quan hệ chỉ ép kiểu sang số nếu các kiểu của toán hạng chưa phải là chuỗi.

Các toán tử `<=` (nhỏ hơn hoặc bằng) và `>=` (lớn hơn hoặc bằng) thực chất là dạng viết tắt cho một phép kiểm tra phức hợp.

```js
"hello" <= "hello";                             // true
("hello" < "hello") || ("hello" == "hello");    // true

"hello" >= "hello";                             // true
("hello" > "hello") || ("hello" == "hello");    // true
```

| GHI CHÚ: |
| :--- |
| Đây là một nét tinh tế thú vị trong tài liệu đặc tả: JS thực chất không hề định nghĩa riêng các phép toán lớn hơn (dành cho `>`) hay lớn hơn hoặc bằng (dành cho `>=`). Thay vào đó, nó định nghĩa chúng bằng cách đảo ngược các đối số đối với các phép toán bù *nhỏ hơn* tương ứng của chúng. Do đó, biểu thức `x > y` về bản chất được JS xử lý như `y <= x`, và `x >= y` về bản chất được JS xử lý như `y < x`. Nhờ vậy JS chỉ cần đặc tả cách thức hoạt động của `<` và `==`, và nghiễm nhiên có được `>` cùng `>=` mà không tốn công định nghĩa thêm! |

##### So sánh quan hệ có nhận biết Locale (Locale-Aware Relational Comparisons)

Như tôi vừa đề cập cách đây ít phút, các toán tử quan hệ giả định và sử dụng locale hiện đang có hiệu lực. Tuy nhiên, đôi khi việc ép buộc áp dụng một locale cụ thể cho các phép so sánh lại rất hữu ích (chẳng hạn như khi sắp xếp một danh sách các chuỗi).

JS cung cấp phương thức `localeCompare(..)` trên các chuỗi JS cho mục đích này:

```js
"hello".localeCompare("world");
// -1 (hoặc số âm)

"world".localeCompare("hello","en");
// 1 (hoặc số dương)

"hello".localeCompare("hello","en",{ ignorePunctuation: true });
// 0

// các ví dụ từ MDN:
//
// trong tiếng Đức, ä đứng trước z
"ä".localeCompare("z","de");
// -1 (hoặc số âm) // một giá trị âm

// trong tiếng Thụy Điển, ä đứng sau z
"ä".localeCompare("z","sv");
// 1 (hoặc số dương)
```

Các đối số thứ hai và thứ ba tùy chọn truyền vào `localeCompare(..)` sẽ điều khiển locale nào được áp dụng, thông qua API `Intl.Collator`[^INTLCollatorApi] như đã trình bày trước đó.

Bạn có thể sử dụng `localeCompare(..)` khi sắp xếp một mảng các chuỗi:

```js
studentNames = [
    "Lisa",
    "Kyle",
    "Jason"
];

// Array::sort() biến đổi trực tiếp trên mảng (in place)
studentNames.sort(function alphabetizeNames(name1,name2){
    return name1.localeCompare(name2);
});

studentNames;
// [ "Jason", "Kyle", "Lisa" ]
```

Nhưng như đã thảo luận trước đó, một cách trực tiếp hơn (và hiệu năng nhỉnh hơn đôi chút khi sắp xếp số lượng lớn chuỗi) là sử dụng trực tiếp `Intl.Collator`:

```js
studentNames = [
    "Lisa",
    "Kyle",
    "Jason"
];

nameSorter = new Intl.Collator("en");

// Array::sort() biến đổi trực tiếp trên mảng (in place)
studentNames.sort(nameSorter.compare);

studentNames;
// [ "Jason", "Kyle", "Lisa" ]
```

### Nối chuỗi (String Concatenation)

Hai hoặc nhiều giá trị chuỗi có thể được nối (kết hợp) lại với nhau thành một giá trị chuỗi mới, bằng cách sử dụng toán tử `+`:

```js
greeting = "Hello, " + "Kyle!";

greeting;               // Hello, Kyle!
```

Toán tử `+` sẽ đóng vai trò là phép nối chuỗi nếu một trong hai toán hạng (các giá trị ở vế trái hoặc vế phải của toán tử) vốn đã là chuỗi (kể cả một chuỗi rỗng `""`).

Nếu một toán hạng là chuỗi và toán hạng kia thì không, toán hạng không phải chuỗi sẽ được ép kiểu sang dạng biểu diễn chuỗi của nó phục vụ cho mục đích nối chuỗi:

```js
userCount = 7;

status = "There are " + userCount + " users online";

status;         // There are 7 users online
```

Việc nối chuỗi theo kiểu này về bản chất là phép nội suy (interpolation) dữ liệu vào trong chuỗi, vốn là mục đích chính của template literal (xem Chương 1). Do đó đoạn mã sau đây sẽ mang lại kết quả tương tự nhưng thường được coi là phương pháp được ưu tiên hơn:

```js
userCount = 7;

status = `There are ${userCount} users online`;

status;         // There are 7 users online
```

Các lựa chọn khác cho việc nối chuỗi bao gồm `"one".concat("two","three")` và `[ "one", "two", "three" ].join("")`, nhưng những cách tiếp cận này chỉ thích hợp hơn khi số lượng chuỗi cần nối phụ thuộc vào các điều kiện/phép tính tại thời điểm chạy (runtime). Nếu chuỗi có một tập hợp nội dung cố định/đã biết trước như ở trên, template literal là lựa chọn tốt hơn.

### Các phương thức của giá trị chuỗi (String Value Methods)

Các giá trị chuỗi cung cấp hàng loạt các phương thức đặc thù riêng cho chuỗi (dưới dạng các thuộc tính):

* `charAt(..)`: tạo ra một giá trị chuỗi mới tại chỉ số dạng số, tương tự như `[ .. ]`; khác với `[ .. ]`, kết quả luôn luôn là một chuỗi, hoặc là ký tự tại vị trí `0` (nếu chỉ số hợp lệ nhưng nằm ngoài phạm vi), hoặc là chuỗi rỗng `""` (nếu thiếu chỉ số hoặc chỉ số không hợp lệ)

* `at(..)` tương tự như `charAt(..)`, nhưng chỉ số âm sẽ đếm ngược từ cuối chuỗi trở về trước

* `charCodeAt(..)`: trả về code-unit dạng số (xem mục "Mã hóa ký tự trong JS" ở Chương 1) tại chỉ số được chỉ định

* `codePointAt(..)`: trả về toàn bộ code-point bắt đầu tại chỉ số được chỉ định; nếu tìm thấy một cặp surrogate tại đó, toàn bộ ký tự (code-point) sẽ được trả về

* `substr(..)` / `substring(..)` / `slice(..)`: tạo ra một giá trị chuỗi mới đại diện cho một khoảng ký tự từ chuỗi ban đầu; các phương thức này khác nhau ở cách thức các chỉ số bắt đầu/kết thúc của khoảng được xác định hoặc quy định

* `toUpperCase()`: tạo ra một giá trị chuỗi mới trong đó toàn bộ các ký tự được chuyển thành chữ hoa

* `toLowerCase()`: tạo ra một giá trị chuỗi mới trong đó toàn bộ các ký tự được chuyển thành chữ thường

* `toLocaleUpperCase()` / `toLocaleLowerCase()`: áp dụng các ánh xạ theo locale cho các thao tác chuyển đổi chữ hoa hoặc chữ thường

* `concat(..)`: tạo ra một giá trị chuỗi mới là kết quả nối giữa chuỗi ban đầu và tất cả các đối số giá trị chuỗi được truyền vào

* `indexOf(..)`: tìm kiếm một đối số giá trị chuỗi trong chuỗi ban đầu, có thể tùy chọn bắt đầu từ vị trí được chỉ định trong đối số thứ hai; trả về vị trí chỉ số (bắt đầu từ `0`) nếu tìm thấy, hoặc `-1` nếu không tìm thấy

* `lastIndexOf(..)`: tương tự như `indexOf(..)` nhưng tìm kiếm từ cuối chuỗi (bên phải trong các locale LTR, bên trái trong các locale RTL)

* `includes(..)`: tương tự như `indexOf(..)` nhưng trả về kết quả dạng boolean

* `search(..)`: tương tự như `indexOf(..)` nhưng có cơ chế khớp biểu thức chính quy (regular expression) như được chỉ định

* `trimStart()` / `trimEnd()` / `trim()`: tạo ra một giá trị chuỗi mới với các khoảng trắng được cắt tỉa ở đầu chuỗi (bên trái trong locale LTR, bên phải trong locale RTL), hoặc ở cuối chuỗi (bên phải trong locale LTR, bên trái trong locale RTL), hoặc cả hai đầu

* `repeat(..)`: tạo ra một chuỗi mới với giá trị chuỗi ban đầu được lặp lại theo số lần được chỉ định

* `split(..)`: tạo ra một mảng các giá trị chuỗi được tách ra tại các ranh giới chuỗi hoặc biểu thức chính quy được chỉ định

* `padStart(..)` / `padEnd(..)`: tạo ra một giá trị chuỗi mới có phần đệm (mặc định là khoảng trắng `" "`, nhưng có thể ghi đè) được áp dụng vào đầu chuỗi (bên trái trong locale LTR, bên phải trong locale RTL) hoặc cuối chuỗi (bên phải trong locale LTR, bên trái trong locale RTL), sao cho kết quả chuỗi cuối cùng đạt độ dài tối thiểu được chỉ định

* `startsWith(..)` / `endsWith(..)`: kiểm tra xem đầu chuỗi (bên trái trong locale LTR, bên phải trong locale RTL) hoặc cuối chuỗi (bên phải trong locale LTR) của chuỗi ban đầu có khớp với đối số giá trị chuỗi hay không; trả về một kết quả dạng boolean

* `match(..)` / `matchAll(..)`: trả về kết quả khớp biểu thức chính quy dạng giống mảng (array-like) đối với chuỗi ban đầu

* `replace(..)`: trả về một chuỗi mới với sự thay thế từ chuỗi ban đầu, áp dụng cho một hoặc nhiều lần xuất hiện khớp với biểu thức chính quy được chỉ định

* `normalize(..)`: tạo ra một chuỗi mới sau khi đã thực hiện chuẩn hóa Unicode (xem mục "Chuẩn hóa Unicode" ở Chương 1) trên nội dung của nó

* `localeCompare(..)`: hàm so sánh hai chuỗi dựa trên locale hiện tại (hữu ích cho việc sắp xếp); trả về một số âm (thường là `-1` nhưng không đảm bảo tuyệt đối) nếu giá trị chuỗi ban đầu đứng trước giá trị chuỗi đối số theo thứ tự từ điển, một số dương (thường là `1` nhưng không đảm bảo tuyệt đối) nếu giá trị chuỗi ban đầu đứng sau giá trị chuỗi đối số theo thứ tự từ điển, và `0` nếu hai chuỗi giống hệt nhau

* `anchor()`, `big()`, `blink()`, `bold()`, `fixed()`, `fontcolor()`, `fontsize()`, `italics()`, `link()`, `small()`, `strike()`, `sub()`, và `sup()`: trong lịch sử, những phương thức này từng rất hữu dụng để tạo ra các đoạn chuỗi HTML; hiện tại chúng đã bị coi là lỗi thời (deprecated) và nên tránh sử dụng

| CẢNH BÁO: |
| :--- |
| Rất nhiều phương thức được mô tả ở trên phụ thuộc vào các chỉ số vị trí. Như đã đề cập trước đó trong phần "Tính toán độ dài", các vị trí này phụ thuộc vào nội dung nội bộ của giá trị chuỗi, điều đó có nghĩa là nếu một ký tự Unicode mở rộng xuất hiện và chiếm hai ô code-unit, nó sẽ được tính là hai vị trí chỉ số thay vì một. Việc không tính đến các code-unit *phân tách* (decomposed), các cặp surrogate, và các grapheme cluster là một nguồn gốc phổ biến gây ra lỗi trong việc xử lý chuỗi của JS. |

Tất cả các phương thức chuỗi này đều có thể được gọi trực tiếp trên một giá trị literal, hoặc trên một biến/thuộc tính đang chứa một giá trị chuỗi. Khi được áp dụng, chúng tạo ra một giá trị chuỗi mới thay vì sửa đổi giá trị chuỗi hiện có (vì chuỗi là bất biến):

```js
"all these letters".toUpperCase();      // ALL THESE LETTERS

greeting = "Hello!";
greeting.repeat(2);                     // Hello!Hello!
greeting;                               // Hello!
```

### Các hàm trợ giúp tĩnh của `String` (Static `String` Helpers)

Các hàm tiện ích chuỗi sau đây được cung cấp trực tiếp trên đối tượng `String`, thay vì là các phương thức trên từng giá trị chuỗi riêng lẻ:

* `String.fromCharCode(..)` / `String.fromCodePoint(..)`: tạo ra một chuỗi từ một hoặc nhiều đối số đại diện cho các code-unit (`fromCharCode(..)`) hoặc toàn bộ các code-point (`fromCodePoint(..)`)

* `String.raw(..)`: một hàm template-tag mặc định cho phép nội suy trên một template literal nhưng ngăn chặn các chuỗi thoát ký tự (escape sequences) bị phân tích cú pháp, nhờ đó chúng giữ nguyên các ký tự đầu vào *thô* (*raw*) từ literal

Hơn nữa, hầu hết các giá trị (đặc biệt là các giá trị nguyên thủy) đều có thể được ép kiểu tường minh sang chuỗi tương đương bằng cách truyền chúng vào hàm `String(..)` (không dùng từ khóa `new`). Ví dụ:

```js
String(true);           // "true"
String(42);             // "42"
String(Infinity);       // "Infinity"
String(undefined);      // "undefined"
```

Chúng ta sẽ tìm hiểu chi tiết hơn rất nhiều về những phép ép kiểu như vậy trong một chương sau.

## Các hành vi của số (Number Behaviors)

Các con số được sử dụng cho nhiều tác vụ khác nhau trong chương trình của chúng ta, nhưng chủ yếu là phục vụ cho các phép tính toán học. Hãy đặc biệt chú ý đến cách các con số trong JS hành xử, nhằm đảm bảo kết quả thu được đúng như kỳ vọng.

### Sự thiếu chính xác của số dấu phẩy động (Floating Point Imprecision)

Chúng ta cần quay trở lại cuộc thảo luận về chuẩn IEEE-754 từ Chương 1.

Một trong những cạm bẫy kinh điển của bất kỳ hệ thống số chuẩn IEEE-754 nào trong mọi ngôn ngữ lập trình -- KHÔNG PHẢI CHỈ RIÊNG JS! -- là không phải mọi phép toán và giá trị đều có thể đặt vừa vặn vào các dạng biểu diễn của IEEE-754.

Minh họa phổ biến nhất là:

```js
point3a = 0.1 + 0.2;
point3b = 0.3;

point3a;                        // 0.30000000000000004
point3b;                        // 0.3

point3a === point3b;            // false <-- ôi không!
```

Phép toán `0.1 + 0.2` rốt cuộc lại tạo ra lỗi dấu phẩy động (độ trôi số học - drift), trong đó giá trị thực tế được lưu trữ là `0.30000000000000004`.

Các dạng biểu diễn bit tương ứng của chúng là:

```
// 0.30000000000000004
00111111110100110011001100110011
00110011001100110011001100110100

// 0.3
00111111110100110011001100110011
00110011001100110011001100110011
```

Nếu quan sát kỹ các mẫu bit đó, bạn sẽ thấy chỉ có 2 bit cuối cùng là khác nhau, từ `00` thành `11`. Nhưng điều đó là đủ để hai con số này không bằng nhau!

Một lần nữa, để nhấn mạnh thêm: hành vi này **HOÀN TOÀN KHÔNG PHẢI** là nét đặc thù chỉ có ở JS. Đây chính xác là cách mà bất kỳ ngôn ngữ lập trình nào tuân theo chuẩn IEEE-754 đều sẽ hoạt động trong cùng một kịch bản. Như tôi đã khẳng định ở trên, phần lớn các ngôn ngữ lập trình đều sử dụng IEEE-754, và do đó tất cả chúng đều phải chịu chung số phận này.

Tôi biết sự cám dỗ muốn đem JS ra chế giễu vì `0.1 + 0.2 !== 0.3` là rất lớn. Nhưng ở đây, điều đó hoàn toàn vô căn cứ.

| GHI CHÚ: |
| :--- |
| Hầu như tất cả các lập trình viên đều cần phải nhận thức được về chuẩn IEEE-754 và đảm bảo rằng họ phải cẩn trọng trước những cạm bẫy kiểu này. Thật đáng kinh ngạc, theo một cách đáng thất vọng, khi có quá ít người trong số họ hiểu được cách IEEE-754 vận hành. Nếu bạn đã dành thời gian đọc và hiểu những khái niệm này cho đến lúc này, bạn hiện đã nằm trong số phần trăm rất nhỏ những người thực sự nỗ lực để hiểu rõ các con số trong chương trình của mình! |

#### Ngưỡng Epsilon (Epsilon Threshold)

Một lời khuyên phổ biến để khắc phục sự thiếu chính xác của số dấu phẩy động như vậy là sử dụng giá trị `number` *cực kỳ nhỏ* này do JS định nghĩa:

```js
Number.EPSILON;                 // 2.220446049250313e-16
```

*Epsilon* là khoảng chênh lệch nhỏ nhất mà JS có thể biểu diễn giữa `1` và giá trị tiếp theo lớn hơn `1`. Mặc dù giá trị này về mặt kỹ thuật phụ thuộc vào triển khai/nền tảng, nhưng thông thường nó xấp xỉ khoảng `2.2E-16`, hay `2^-52`.

Đối với những ai không chú ý đủ kỹ vào các chi tiết ở đây -- bao gồm cả bản thân tôi trong quá khứ! -- người ta thường mặc định cho rằng bất kỳ độ lệch nào về độ chính xác của dấu phẩy động từ một phép toán đơn lẻ sẽ không bao giờ lớn hơn `Number.EPSILON`. Vì vậy, về mặt lý thuyết, chúng ta có thể sử dụng `Number.EPSILON` làm một giá trị dung sai (tolerance) *rất nhỏ* để đảm bảo các phép so sánh bằng giữa các con số diễn ra *an toàn*:

```js
function safeNumberEquals(a,b) {
    return Math.abs(a - b) < Number.EPSILON;
}

point3a = 0.1 + 0.2;
point3b = 0.3;

// liệu chúng có "bằng nhau" một cách an toàn?
safeNumberEquals(point3a,point3b);      // true
```

| CẢNH BÁO: |
| :--- |
| Trong ấn bản đầu tiên của cuốn "Types & Grammar", tôi thực sự đã khuyến nghị chính xác cách tiếp cận này. Tôi đã sai. Đáng lẽ tôi phải nghiên cứu chủ đề này kỹ lưỡng hơn. |

Thế nhưng, hóa ra cách tiếp cận này lại chẳng an toàn chút nào:

```js
point3a = 10.1 + 0.2;
point3b = 10.3;

safeNumberEquals(point3a,point3b);      // false :(
```

Chà... thật đáng thất vọng!

Không may thay, `Number.EPSILON` chỉ hoạt động như một ngưỡng sai số "bằng nhau an toàn" cho một số con số/phép toán nhỏ nhất định, còn trong các trường hợp khác, nó lại quá nhỏ, và dẫn đến các kết quả âm tính giả (false negatives).

Bạn có thể nhân tỉ lệ `Number.EPSILON` với một hệ số nào đó để tạo ra một ngưỡng lớn hơn nhằm tránh các trường hợp âm tính giả nhưng vẫn lọc được toàn bộ độ lệch dấu phẩy động trong chương trình của mình. Tuy nhiên, việc sử dụng hệ số nào hoàn toàn là quyết định mang tính cảm quan thủ công dựa trên độ lớn của các giá trị và các phép toán trên chúng mà chương trình của bạn sẽ thực hiện. Không có cách nào tự động để tính toán một ngưỡng phổ quát và đáng tin cậy.

Trừ khi bạn thực sự biết rõ mình đang làm gì, tốt nhất bạn *không nên* sử dụng cách tiếp cận dựa trên ngưỡng `Number.EPSILON` này.

| MẸO: |
| :--- |
| Nếu bạn muốn đọc thêm chi tiết và những lời khuyên xác đáng về chủ đề này, tôi thực sự khuyến khích bạn đọc bài viết này. [^EpsilonBad] Nhưng nếu chúng ta không thể dùng `Number.EPSILON` để né tránh những hiểm họa từ độ lệch dấu phẩy động, thì chúng ta phải làm gì? Nếu bạn có thể tránh hoàn toàn dấu phẩy động bằng cách nhân tỉ lệ tất cả các con số lên để chúng đều là các số nguyên toàn phần (hoặc bigint) trong khi thực hiện các phép tính toán học, hãy làm như vậy. Chỉ xử lý các giá trị thập phân khi bạn bắt buộc phải xuất/biểu diễn một giá trị cuối cùng sau khi mọi phép toán đã hoàn tất. Nếu điều đó là bất khả thi/không thực tế, hãy sử dụng một thư viện mô phỏng số thập phân có độ chính xác tùy ý (arbitrary precision) và tránh dùng các giá trị `number` hoàn toàn. Hoặc hãy thực hiện các phép toán của bạn trong một môi trường lập trình bên ngoài khác không dựa trên IEEE-754. |

### So sánh dạng số (Numeric Comparison)

Tương tự chuỗi, các giá trị số có thể được so sánh (cho cả so sánh bằng và so sánh quan hệ thứ tự) bằng cách sử dụng cùng các toán tử đó.

Hãy nhớ rằng bất kể giá trị số có hình thức biểu diễn thế nào khi được khai báo dưới dạng literal (hệ cơ số 10, bát phân, thập lục phân, số mũ, v.v.), thì giá trị thực sự được lưu trữ bên dưới mới chính là thứ được đem ra so sánh. Đồng thời hãy luôn ghi nhớ các vấn đề thiếu chính xác của số dấu phẩy động đã được thảo luận ở phần trước, vì các phép so sánh sẽ rất nhạy cảm với nội dung nhị phân chính xác.

#### So sánh bằng dạng số (Numeric Equality)

Cũng giống như chuỗi, các phép so sánh bằng cho các con số sử dụng các toán tử `==` / `===` hoặc `Object.is(..)`. Cũng hãy nhớ lại rằng nếu kiểu của cả hai toán hạng là giống nhau, `==` hoạt động hoàn toàn đồng nhất với `===`.

```js
42 == 42;                   // true
42 === 42;                  // true

42 == 43;                   // false
42 === 43;                  // false

Object.is(42,42);           // true
Object.is(42,43);           // false
```

Đối với so sánh bằng có ép kiểu `==` (khi kiểu của các toán hạng không khớp nhau), nếu một trong hai toán hạng không phải là một giá trị chuỗi, `==` sẽ ưu tiên kiểm tra bằng dạng số (có nghĩa là cả hai toán hạng đều được ép kiểu sang số).

```js
// so sánh dạng số (không phải chuỗi!)
42 == "42";                 // true
```

Trong đoạn mã này, phép so sánh bằng có ép kiểu sẽ ép kiểu `"42"` thành `42`, chứ không phải ngược lại (`42` thành `"42"`). Một khi cả hai kiểu đều là `number`, khi đó các giá trị của chúng sẽ được so sánh bằng chính xác, giống hệt như những gì `===` sẽ làm.

Hãy nhớ lại rằng JS không phân biệt giữa các giá trị như `42`, `42.0`, và `42.000000`; bên dưới bề mặt, tất cả chúng đều là một. Không có gì đáng ngạc nhiên khi các phép kiểm tra bằng `==` và `===` đều xác thực điều đó:

```js
42 == 42.0;                 // true
42.0 == 42.00000;           // true
42.00 === 42.000;           // true
```

Trực giác bạn có được có lẽ là: nếu hai số thực sự giống hệt nhau, chúng sẽ bằng nhau. Và đó là cách JS diễn giải nó. Nhưng `0.3` không thực sự giống hệt kết quả của `0.1 + 0.2`, bởi vì (như chúng ta đã thấy trước đó), phép tính sau tạo ra một giá trị bên dưới *rất gần* với `0.3`, nhưng không hoàn toàn đồng nhất.

Điều thú vị là, hai giá trị này *quá gần nhau* đến mức độ chênh lệch giữa chúng nhỏ hơn cả ngưỡng `Number.EPSILON`, do đó JS thực tế không thể biểu diễn sự khác biệt đó một cách *chính xác*.

Khi đó, bạn có thể nghĩ một cách không chính thức rằng những con số JS như vậy phải "bằng nhau", vì sự khác biệt giữa chúng quá nhỏ để có thể biểu diễn. Nhưng hãy chú ý: JS *có thể* biểu thị rằng *thực sự có* một sự khác biệt, đó là lý do tại sao bạn nhìn thấy số `4` ở tận cùng của phần thập phân khi JS tính toán `0.1 + 0.2`. Và bạn *có thể* gõ ra giá trị số literal `0.00000000000000004` (tức `4e-17`), chính là độ chênh lệch giữa `0.3` và `0.1 + 0.2`.

Những gì JS không thể làm được với các số dấu phẩy động IEEE-754 của nó, là biểu diễn một con số nhỏ như vậy theo một cách đủ *chính xác* để các phép toán trên nó tạo ra kết quả như mong đợi. Nó quá nhỏ để có thể được biểu diễn đầy đủ và đúng đắn trong kiểu `number` mà JS cung cấp.

Do đó `0.1 + 0.2 == 0.3` trả về kết quả `false`, bởi vì có sự khác biệt giữa hai giá trị, mặc dù JS không thể biểu diễn hay thao tác chính xác với một giá trị nhỏ như mức chênh lệch đó.

Tương tự như những gì chúng ta đã thấy với chuỗi, các toán tử `!=` (không bằng có ép kiểu) và `!==` (nghiêm ngặt không bằng) cũng hoạt động với các con số. `x != y` về cơ bản là `!(x == y)`, và `x !== y` về cơ bản là `!(x === y)`.

Có hai ngoại lệ gây bực bội trong phép so sánh bằng dạng số (dù bạn dùng `==` hay `===`):

```js
NaN === NaN;                // false -- bực thật!
-0 === 0;                   // true -- chán thật!
```

`NaN` không bao giờ bằng chính nó (kể cả khi dùng `===`), và `-0` luôn luôn bằng `0` (kể cả khi dùng `===`). Đôi khi mọi người cảm thấy bất ngờ vì ngay cả `===` cũng chứa hai ngoại lệ này bên trong nó.

Tuy nhiên, phép kiểm tra bằng `Object.is(..)` lại không hề có bất kỳ ngoại lệ nào trong số này, vì vậy đối với các phép so sánh bằng với `NaN` và `-0`, hãy tránh các toán tử `==` / `===` và sử dụng `Object.is(..)` -- hoặc đối với riêng `NaN`, hãy dùng `Number.isNaN(..)`.

#### So sánh quan hệ dạng số (Numeric Relational Comparisons)

Cũng giống như với các giá trị chuỗi, các toán tử quan hệ trong JS (`<`, `<=`, `>`, và `>=`) hoạt động tốt với các con số. Các phép toán `<` (nhỏ hơn) và `>` (lớn hơn) tương đối dễ hiểu:

```js
41 < 42;                    // true

0.1 + 0.2 > 0.3;            // true (chán thật, IEEE-754)
```

Hãy nhớ rằng: tương tự như `==`, các toán tử `<` và `>` cũng có tính chất ép kiểu, nghĩa là bất kỳ giá trị nào không phải là số đều sẽ được ép kiểu thành số -- trừ khi cả hai toán hạng vốn đã là chuỗi, như chúng ta đã thấy trước đó. Hoàn toàn không có toán tử so sánh quan hệ nghiêm ngặt nào tồn tại cả.

Nếu bạn đang thực hiện các phép so sánh quan hệ giữa các con số, cách duy nhất để tránh việc ép kiểu là đảm bảo rằng các phép so sánh luôn luôn có hai toán hạng là số. Nếu không, các toán tử này sẽ thực hiện các phép so sánh *quan hệ có ép kiểu* tương tự như cách `==` thực hiện các phép so sánh *bằng có ép kiểu*.

### Các toán tử toán học (Mathematical Operators)

Như tôi đã khẳng định trước đó, lý do chính để các con số tồn tại trong một ngôn ngữ lập trình là để thực hiện các phép toán học với chúng. Vì vậy, chúng ta hãy cùng nói về cách chúng ta làm điều đó.

Các toán tử số học cơ bản là `+` (cộng), `-` (trừ), `*` (nhân), và `/` (chia). Ngoài ra còn có các toán tử `**` (lũy thừa) và `%` (modulo, tức *phần dư của phép chia*). Cũng có các dạng toán tử kết hợp `+=`, `-=`, `*=`, `/=`, `**=`, và `%=`, ngoài việc tính toán chúng còn gán ngược kết quả trở lại cho toán hạng bên trái -- vốn phải là một mục tiêu gán hợp lệ như biến hoặc thuộc tính.

| GHI CHÚ: |
| :--- |
| Như chúng ta đã thấy, toán tử `+` bị nạp chồng (overloaded) để hoạt động với cả số và chuỗi. Khi một hoặc cả hai toán hạng là chuỗi, kết quả sẽ là phép nối chuỗi (bao gồm cả việc ép kiểu toán hạng còn lại thành chuỗi nếu cần thiết). Nhưng nếu không có toán hạng nào là chuỗi, kết quả sẽ là một phép cộng dạng số, đúng như mong đợi. |

Tất cả các toán tử toán học này đều là dạng *nhị phân* (*binary*), nghĩa là chúng yêu cầu hai toán hạng giá trị, mỗi bên toán tử một giá trị; tất cả chúng đều mong muốn các toán hạng là các giá trị số. Nếu một trong hai hoặc cả hai toán hạng không phải là số, (các) toán hạng không phải số đó sẽ được ép kiểu thành số để thực hiện phép tính. Chúng ta sẽ tìm hiểu chi tiết về ép kiểu trong một chương sau.

Hãy xem xét:

```js
40 + 2;                 // 42
44 - 2;                 // 42
21 * 2;                 // 42
84 / 2;                 // 42
7 ** 2;                 // 49
49 % 2;                 // 1

40 + "2";               // "402" (nối chuỗi)
44 - "2";               // 42 (bởi vì "2" bị ép kiểu thành 2)
21 * "2";               // 42 (..tương tự..)
84 / "2";               // 42 (..tương tự..)
"7" ** "2";             // 49 (cả hai toán hạng đều bị ép kiểu thành số)
"49" % "2";             // 1 (..tương tự..)
```

Các toán tử `+` và `-` cũng có dạng *đơn phân* (*unary*), nghĩa là chúng chỉ có một toán hạng duy nhất; một lần nữa, toán hạng này được kỳ vọng là một số, và sẽ bị ép kiểu thành số nếu không phải:

```js
+42;                    // 42
-42;                    // -42

+"42";                  // 42
-"42";                  // -42
```

Có thể bạn đã nhận thấy rằng `-42` trông như thể nó chỉ là một số âm literal "âm bốn mươi hai". Điều đó không hoàn toàn đúng. Một nét tinh tế trong cú pháp của JS là nó không công nhận các literal số âm. Thay vào đó, JS coi đây là một literal số dương `42` được đặt trước và bị đảo dấu bởi toán tử đơn phân `-` nằm ở đằng trước nó.

Có phần bất ngờ là:

```js
-42;                    // -42
- 42;                   // -42
-
    42;                 // -42
```

Như bạn có thể thấy, các khoảng trắng (và thậm chí là các dòng mới) hoàn toàn được cho phép nằm giữa toán tử đơn phân `-` và toán hạng của nó; thực tế, điều này đúng với tất cả các toán tử và toán hạng.

#### Tăng và Giảm (Increment and Decrement)

Có hai toán tử số học đơn phân khác: `++` (tăng) và `--` (giảm). Cả hai đều thực hiện thao tác tương ứng của chúng rồi sau đó gán lại kết quả cho toán hạng -- vốn phải là một mục tiêu gán hợp lệ như một biến hoặc thuộc tính.

Bạn có thể ngầm hiểu `++` tương đương với `+= 1`, và `--` tương đương với `-= 1`:

```js
myAge = 42;

myAge++;
myAge;                  // 43

numberOfHeadHairs--;
```

Tuy nhiên, đây là những toán tử đặc biệt ở chỗ chúng có thể xuất hiện ở vị trí hậu tố (postfix - đứng sau toán hạng) như ở trên, hoặc ở vị trí tiền tố (prefix - đứng trước toán hạng):

```js
myAge = 42;

++myAge;
myAge;                  // 43

--numberOfHeadHairs;
```

Có vẻ kỳ lạ khi cả vị trí tiền tố và hậu tố dường như đều cho ra cùng một kết quả (tăng hoặc giảm) trong các ví dụ như vậy. Sự khác biệt là rất tinh tế, và nó không liên quan đến kết quả được gán lại cuối cùng. Chúng ta sẽ quay lại tìm hiểu những toán tử đặc biệt này trong một chương sau để đào sâu vào sự khác biệt theo vị trí.

### Các toán tử thao tác bit (Bitwise Operators)

JS cung cấp một số toán tử thao tác bit để thực hiện các phép toán ở cấp độ bit trên các giá trị số.

Tuy nhiên, các phép toán bit này không được thực hiện trực tiếp trên mẫu bit đóng gói của số chuẩn IEEE-754 (xem Chương 1). Thay vào đó, số toán hạng trước tiên được chuyển đổi thành một *số nguyên* có dấu 32 bit, phép toán bit được thực hiện, và sau đó kết quả được chuyển đổi ngược lại thành một số chuẩn IEEE-754.

Hãy nhớ rằng, giống như bất kỳ toán tử nguyên thủy nào khác, các toán tử này chỉ tính toán ra các giá trị mới, chứ không thực sự sửa đổi một giá trị trực tiếp tại chỗ.

* `&` (AND thao tác bit): Thực hiện phép toán AND với từng cặp bit tương ứng từ hai toán hạng; `42 & 36 === 32` (tức `0b00...101010 & 0b00...100100 === 0b00..100000`)

* `|` (OR thao tác bit): Thực hiện phép toán OR với từng cặp bit tương ứng từ hai toán hạng; `42 | 36 === 46` (tức `0b00...101010 | 0b00...100100 === 0b00...101110`)

* `^` (XOR thao tác bit): Thực hiện phép toán XOR (eXclusive-OR - tuyển loại trừ) với từng cặp bit tương ứng từ hai toán hạng; `42 ^ 36 === 14` (tức `0b00...101010 ^ 0b00...100100 === 0b00...001110`)

* `~` (NOT thao tác bit): Thực hiện phép toán NOT đảo ngược các bit của một toán hạng đơn lẻ; `~42 === -43` (tức `~0b00...101010 === 0b11...010101`); sử dụng biểu diễn bù 2 (2's complement), số nguyên có dấu có bit đầu tiên được đặt thành `1` nghĩa là số âm, và các bit còn lại (khi được lật ngược lại theo quy tắc bù 2, tức là đảo bit bù 1 rồi cộng thêm `1`) sẽ là `43` (`0b10...101011`); dạng tương đương của `~` trong số học thập phân là `~x === -(x + 1)`, do đó `~42 === -43`

* `<<` (dịch trái - left shift): Thực hiện dịch trái các bit của toán hạng bên trái theo số lượng bit được chỉ định bởi toán hạng bên phải; `42 << 3 === 336` (tức `0b00...101010 << 3 === 0b00...101010000`)

* `>>` (dịch phải lan truyền dấu - right shift): Thực hiện dịch phải có bảo toàn dấu các bit của toán hạng bên trái theo số lượng bit được chỉ định bởi toán hạng bên phải, loại bỏ các bit bị tràn ra ngoài ở phía bên phải; bất kể bit ngoài cùng bên trái ban đầu là gì (`0`, hoặc `1` nếu là số âm) thì nó sẽ được sao chép điền vào các bit mới ở bên trái (nhờ đó giữ nguyên dấu của giá trị ban đầu trong kết quả); `42 >> 3 === 5` (tức `0b00..101010 >> 3 === 0b00...000101`)

* `>>>` (dịch phải điền số 0, hay dịch phải không dấu - zero-fill right shift): Thực hiện phép dịch phải tương tự như `>>`, nhưng luôn điền số `0` vào các bit được dịch vào từ phía bên trái thay vì sao chép bit dấu ngoài cùng bên trái (do đó bỏ qua dấu của giá trị ban đầu trong kết quả); `42 >>> 3 === 5` nhưng `-43 >>> 3 === 536870906` (tức `0b11...010101 >>> 3 === 0b0001...111010`)

* `&=`, `|=`, `<<=`, `>>=`, và `>>>=` (các toán tử thao tác bit kết hợp phép gán): Thực hiện phép toán bit tương ứng, rồi sau đó gán kết quả cho toán hạng bên trái (vốn phải là một mục tiêu gán hợp lệ như biến hoặc thuộc tính, chứ không thể chỉ là một giá trị literal); lưu ý rằng `~=` không có mặt trong danh sách này, vì không tồn tại toán tử "phủ định nhị phân kèm phép gán" như vậy

Thành thực mà nói, các phép toán thao tác bit không quá phổ biến trong JS. Nhưng đôi khi bạn có thể bắt gặp một câu lệnh kiểu như:

```js
myGPA = 3.54;

myGPA | 0;              // 3
```

Vì các toán tử thao tác bit chỉ hoạt động trên các số nguyên 32 bit, nên phép toán `| 0` sẽ cắt bỏ (tức `Math.trunc(..)`) bất kỳ phần thập phân nào, chỉ để lại phần số nguyên.

| CẢNH BÁO: |
| :--- |
| Một quan niệm sai lầm phổ biến là `| 0` giống như phép lấy sàn *floor* (tức `Math.floor(..)`). Kết quả của `| 0` trùng khớp với `Math.floor(..)` trên các số dương, nhưng lại khác biệt trên các số âm, vì theo định nghĩa chuẩn, *floor* là một phép toán làm tròn xuống về phía `-Infinity`. `| 0` chỉ đơn thuần vứt bỏ các bit thập phân, mà đó thực chất chính là hành vi cắt cụt (truncation). |

### Các phương thức của giá trị số (Number Value Methods)

Các giá trị số cung cấp các phương thức sau (dưới dạng thuộc tính) cho các thao tác đặc thù với số:

* `toExponential(..)`: tạo ra một chuỗi biểu diễn con số bằng ký hiệu khoa học (ví dụ: `"4.2e+1"`)

* `toFixed(..)`: tạo ra một chuỗi biểu diễn con số không dùng ký hiệu khoa học với số lượng chữ số thập phân được chỉ định (làm tròn hoặc đệm thêm các số 0 nếu cần)

* `toPrecision(..)`: tương tự như `toFixed(..)`, ngoại trừ việc nó áp dụng đối số số học làm số lượng chữ số có nghĩa (tức là độ chính xác - precision) bao gồm cả toàn bộ phần nguyên và các chữ số thập phân nếu có

* `toLocaleString(..)`: tạo ra một chuỗi biểu diễn con số dựa theo locale hiện tại

```js
myAge = 42;

myAge.toExponential(3);         // "4.200e+1"
```

Một nét tinh tế đặc biệt trong cú pháp của JS là dấu `.` có thể gây mơ hồ khi kết hợp giữa các literal số và việc truy cập thuộc tính/phương thức.

Nếu một dấu `.` xuất hiện ngay lập tức (không có khoảng trắng) sau một chữ số của literal số, và bản thân giá trị số đó chưa có dấu thập phân `.`, thì dấu `.` đó được coi là dấu bắt đầu phần thập phân của con số. Nhưng nếu vị trí của dấu `.` rõ ràng *không phải* là một phần của literal số, thì nó luôn luôn được xử lý như một phép truy cập thuộc tính.

```js
42 .toExponential(3);           // "4.200e+1"
```

Ở đây, khoảng trắng giúp phân định rõ ràng dấu `.`, chỉ định nó là một phép truy cập thuộc tính/phương thức. Có lẽ cách phổ biến/được ưa chuộng hơn là sử dụng cặp ngoặc `(..)` thay vì khoảng trắng để giải trừ sự mơ hồ đó:

```js
(42).toExponential(3);          // "4.200e+1"
```

Một hệ quả trông khá bất thường của quy tắc ngữ pháp phân tích cú pháp JS này:

```js
42..toExponential(3);           // "4.200e+1"
```

Thường được gọi là thành ngữ "hai dấu chấm" ("double-dot"), dấu `.` thứ nhất trong biểu thức này là dấu thập phân, và do đó dấu `.` thứ hai rõ ràng *không phải* là dấu thập phân, mà là một phép truy cập thuộc tính/phương thức.

Ngoài ra, hãy chú ý rằng không có chữ số nào đứng sau dấu `.` đầu tiên; việc để lại một dấu `.` ở đuôi của một literal số là cú pháp hoàn toàn hợp lệ:

```js
myAge = 41. + 1.;

myAge;                          // 42
```

Các giá trị thuộc kiểu `bigint` không thể có phần thập phân, do đó cú pháp phân tích hoàn toàn rõ ràng rằng một dấu `.` đứng sau một literal (với ký tự `n` ở cuối) luôn luôn là một phép truy cập thuộc tính:

```js
42n.toString();                 // 42
```

### Các thuộc tính tĩnh của `Number` (Static `Number` Properties)

* `Number.EPSILON`: Giá trị nhỏ nhất có thể có giữa `1` và con số lớn hơn tiếp theo

* `Number.NaN`: Hoàn toàn giống với biểu tượng `NaN` toàn cục, giá trị số không hợp lệ đặc biệt

* `Number.MIN_SAFE_INTEGER` / `Number.MAX_SAFE_INTEGER`: Các số nguyên dương và âm có giá trị tuyệt đối lớn nhất (xa số `0` nhất) mà an toàn

* `Number.MIN_VALUE` / `Number.MAX_VALUE`: Giá trị nhỏ nhất (giá trị dương gần `0` nhất) và giá trị lớn nhất (giá trị dương xa `0` nhất) có thể biểu diễn được bằng kiểu `number`

* `Number.NEGATIVE_INFINITY` / `Number.POSITIVE_INFINITY`: Hoàn toàn giống với `-Infinity` và `Infinity` toàn cục, các giá trị đại diện cho các giá trị lớn nhất (không hữu hạn) xa số `0` nhất

### Các hàm trợ giúp tĩnh của `Number` (Static `Number` Helpers)

* `Number.isFinite(..)`: trả về một giá trị boolean cho biết giá trị đó có phải là hữu hạn hay không -- một `number` không phải là `NaN`, cũng không phải là một trong hai giá trị vô cực

* `Number.isInteger(..)` / `Number.isSafeInteger(..)`: cả hai đều trả về giá trị boolean cho biết liệu giá trị đó có phải là một `number` nguyên vẹn không có phần thập phân hay không, và liệu nó có nằm trong phạm vi *an toàn* cho số nguyên hay không (`-2^53 + 1` đến `2^53 - 1`)

* `Number.isNaN(..)`: Phiên bản đã được sửa lỗi của tiện ích toàn cục `isNaN(..)`, có nhiệm vụ xác định xem đối số được cung cấp có phải là giá trị `NaN` đặc biệt hay không

* `Number.parseFloat(..)` / `Number.parseInt(..)`: các tiện ích phân tích cú pháp giá trị chuỗi để tìm các chữ số, từ trái sang phải, cho đến cuối chuỗi hoặc cho đến khi gặp ký tự đầu tiên không phải số thực (hoặc không phải số nguyên)

### Không gian tên `Math` tĩnh (Static `Math` Namespace)

Vì công dụng chính của các giá trị `number` là để thực hiện các phép tính toán học, nên JS bao gồm rất nhiều hằng số toán học tiêu chuẩn và các tiện ích phép toán trên không gian tên `Math`.

Có rất nhiều mục như vậy, vì vậy tôi sẽ không liệt kê chi tiết từng mục một. Nhưng dưới đây là một vài ví dụ minh họa:

```js
Math.PI;                        // 3.141592653589793

// giá trị tuyệt đối
Math.abs(-32.6);                // 32.6

// làm tròn
Math.round(-32.6);              // -33

// chọn giá trị nhỏ nhất/lớn nhất
Math.min(100,Math.max(0,42));   // 42
```

Không giống như `Number`, vốn đồng thời là hàm `Number(..)` (dùng cho việc ép kiểu số), `Math` chỉ đơn thuần là một đối tượng chứa các thuộc tính này và các hàm tiện ích tĩnh; nó không thể được gọi như một hàm thông thường.

| CẢNH BÁO: |
| :--- |
| Một thành viên kỳ lạ trong không gian tên `Math` là `Math.random()`, dùng để tạo ra một giá trị dấu phẩy động ngẫu nhiên trong khoảng từ `0` đến `1.0`. Việc coi việc sinh số ngẫu nhiên -- một tác vụ vốn có tính lưu trạng thái/tạo hiệu ứng phụ (stateful/side-effecting) -- như một phép toán học quả thực rất khác thường. Nó cũng từ lâu đã là một hiểm họa bảo mật tiềm tàng ("footgun"), bởi vì bộ sinh số giả ngẫu nhiên (PRNG) mà JS sử dụng *không hề* an toàn (có thể dự đoán được) dưới góc độ mật mã học. Nền tảng web cách đây nhiều năm đã bổ sung API an toàn hơn là `crypto.getRandomValues(..)` (dựa trên một PRNG tốt hơn), giúp lấp đầy một mảng định kiểu (typed-array) bằng các bit ngẫu nhiên có thể được diễn giải thành một hoặc nhiều số nguyên (với độ lớn tối đa được xác định theo kiểu dữ liệu). Việc sử dụng `Math.random()` hiện nay được khuyến cáo rộng rãi là nên tránh. |

### BigInt và Number không thể trộn lẫn (BigInts and Numbers Don't Mix)

Như chúng ta đã tìm hiểu trong Chương 1, các giá trị thuộc kiểu `number` và kiểu `bigint` không thể kết hợp cùng nhau trong cùng một phép toán. Điều đó có thể khiến bạn vấp ngã ngay cả khi bạn chỉ đang thực hiện một phép tăng giá trị đơn giản (chẳng hạn như trong một vòng lặp):

```js
myAge = 42n;

myAge + 1;                  // Ném ra TypeError!
myAge += 1;                 // Ném ra TypeError!

myAge + 1n;                 // 43n
myAge += 1n;                // 43n

myAge++;
myAge;                      // 44n
```

Vì lý do đó, nếu bạn đang sử dụng cả hai giá trị `number` và `bigint` trong chương trình của mình, bạn sẽ cần phải ép kiểu thủ công từ kiểu giá trị này sang kiểu kia một cách tương đối thường xuyên. Hàm `BigInt(..)` (không dùng từ khóa `new`) có thể ép một giá trị `number` sang `bigint`. Ngược lại, để chuyển theo chiều ngược lại từ `bigint` sang `number`, hãy sử dụng hàm `Number(..)` (một lần nữa, không dùng từ khóa `new`):

```js
BigInt(42);                 // 42n

Number(42n);                // 42
```

Dẫu vậy hãy luôn ghi nhớ: việc ép kiểu qua lại giữa các kiểu này tiềm ẩn một số rủi ro:

```js
BigInt(4.2);                // Ném ra RangeError!
BigInt(NaN);                // Ném ra RangeError!
BigInt(Infinity);           // Ném ra RangeError!

Number(2n ** 1024n);        // Infinity
```

## Các kiểu nguyên thủy là nền tảng (Primitives Are Foundational)

Xuyên suốt hai chương vừa qua, chúng ta đã đào sâu vào cách các giá trị nguyên thủy vận hành trong JS. Tôi cá rằng không ít độc giả, cũng giống như tôi trước đây, đã từng muốn bỏ qua những chủ đề này. Nhưng giờ đây, hy vọng rằng bạn đã nhận ra tầm quan trọng của việc thấu hiểu những khái niệm này.

Tuy nhiên, câu chuyện không dừng lại ở đây. Còn xa mới kết thúc! Trong chương tiếp theo, chúng ta sẽ hướng sự chú ý của mình vào việc tìm hiểu các kiểu đối tượng của JS (object, mảng, v.v.).

[^TwitterUnicode]: "New update to the Twitter-Text library: Emoji character count"; Andy Piper; Tháng 10 năm 2018; https://twittercommunity.com/t/new-update-to-the-twitter-text-library-emoji-character-count/114607 ; Truy cập tháng 7 năm 2022

[^INTLAPI]: ECMAScript 2022 Internationalization API Specification; https://402.ecma-international.org/9.0/ ; Truy cập tháng 8 năm 2022

[^INTLCollator]: "Intl.Collator", MDN; https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Collator ; Truy cập tháng 8 năm 2022

[^INTLSegmenter]: "Intl.Segmenter", MDN; https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter ; Truy cập tháng 8 năm 2022

[^StrictEquality]: "7.2.16 IsStrictlyEqual(x,y)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-isstrictlyequal ; Truy cập tháng 8 năm 2022

[^LooseEquality]: "7.2.15 IsLooselyEqual(x,y)", ECMAScript 2022 Language Specification; https://262.ecma-international.org/13.0/#sec-islooselyequal ; Truy cập tháng 8 năm 2022

[^EpsilonBad]: "PLEASE don't follow the code recipe in the accepted answer", Stack Overflow; Daniel Scott; Tháng 7 năm 2019; https://stackoverflow.com/a/56967003/228852 ; Truy cập tháng 8 năm 2022
