# You Don't Know JS Yet: Bắt đầu - Tái bản lần 2
# Chương 2: Khảo sát JS

Cách tốt nhất để học JS là bắt tay vào viết JS.

Để làm được điều đó, bạn cần biết cách ngôn ngữ này vận hành, và đó chính là điều chúng ta sẽ tập trung vào ở đây. Ngay cả khi bạn đã từng lập trình bằng các ngôn ngữ khác trước đây, hãy dành thời gian để làm quen dần với JS, và đảm bảo thực hành từng phần một.

Chương này không phải là một tài liệu tham khảo cặn kẽ về từng ngóc ngách cú pháp của ngôn ngữ JS. Nó cũng không nhằm mục đích trở thành một cuốn cẩm nang vỡ lòng "nhập môn JS" toàn diện.

Thay vào đó, chúng ta sẽ chỉ khảo sát qua một số mảng chủ đề lớn của ngôn ngữ. Mục tiêu của chúng ta là có được *cảm nhận* tốt hơn về nó, để có thể tự tin tiến bước viết các chương trình của riêng mình. Chúng ta sẽ cùng xem lại nhiều chủ đề trong số này với mức độ chi tiết tăng dần khi bạn đi tiếp qua phần còn lại của cuốn sách này, cũng như toàn bộ bộ sách.

Xin đừng kỳ vọng rằng chương này sẽ là một bài đọc lướt qua nhanh chóng. Nó dài và có rất nhiều chi tiết đáng để nghiền ngẫm. Hãy thong thả dành thời gian.

| MẸO: |
| :--- |
| Nếu bạn vẫn đang trong quá trình làm quen với JS, tôi khuyên bạn nên dành nhiều thời gian hơn để đọc kỹ chương này. Hãy xem từng phần, suy ngẫm và khám phá chủ đề đó một lúc. Hãy quan sát các chương trình JS sẵn có và so sánh những gì bạn thấy trong đó với mã lệnh và các lời giải thích (cũng như quan điểm!) được trình bày ở đây. Bạn sẽ gặt hái được nhiều điều hơn từ phần còn lại của cuốn sách và toàn bộ bộ sách này nếu có một nền tảng vững chắc về *bản chất* của JS. |

## Mỗi tập tin là một chương trình

Hầu hết mọi trang web (ứng dụng web) bạn sử dụng đều bao gồm nhiều tập tin JS khác nhau (thường có phần mở rộng là .js). Chúng ta thường dễ xem toàn bộ những thứ đó (ứng dụng) như một chương trình duy nhất. Nhưng JS lại nhìn nhận theo một cách khác.

Trong JS, mỗi tập tin độc lập là một chương trình riêng biệt của chính nó.

Lý do điều này quan trọng chủ yếu xoay quanh việc xử lý lỗi (error handling). Vì JS coi các tập tin là những chương trình, một tập tin có thể bị lỗi (trong quá trình phân tích cú pháp/biên dịch hoặc thực thi) nhưng điều đó chưa chắc đã ngăn cản tập tin tiếp theo được xử lý. Rõ ràng, nếu ứng dụng của bạn phụ thuộc vào năm tập tin .js, và một trong số chúng bị lỗi, thì toàn bộ ứng dụng nhiều khả năng chỉ hoạt động được một phần, nếu may mắn. Điều quan trọng là phải đảm bảo mỗi tập tin hoạt động chuẩn xác, và trong chừng mực có thể, chúng phải xử lý sự cố ở các tập tin khác một cách mượt mà (gracefully) nhất có thể.

Có thể bạn sẽ thấy ngạc nhiên khi coi các tập tin .js riêng biệt là những chương trình JS riêng biệt. Từ góc nhìn trải nghiệm một ứng dụng của bạn, nó trông hoàn toàn giống như một chương trình lớn. Đó là bởi vì quá trình thực thi của ứng dụng cho phép các *chương trình* riêng lẻ này phối hợp và hoạt động như một chương trình duy nhất.

| GHI CHÚ: |
| :--- |
| Nhiều dự án sử dụng các công cụ trong quy trình build (build process tools) nhằm kết hợp các tập tin riêng biệt từ dự án thành một tập tin duy nhất để tải lên trang web. Khi điều này diễn ra, JS sẽ coi tập tin kết hợp duy nhất này là toàn bộ chương trình. |

Cách duy nhất để nhiều tập tin .js độc lập hoạt động như một chương trình duy nhất là chia sẻ trạng thái của chúng (và quyền truy cập vào các chức năng công khai của chúng) thông qua "phạm vi toàn cục" (global scope). Chúng hòa lẫn vào nhau trong không gian tên (namespace) phạm vi toàn cục này, do đó ở runtime chúng hành xử như một thể thống nhất.

Kể từ ES6, JS cũng đã hỗ trợ định dạng module bên cạnh định dạng chương trình JS độc lập truyền thống. Các module cũng hoạt động dựa trên tập tin. Nếu một tập tin được nạp thông qua cơ chế nạp module như câu lệnh `import` hoặc thẻ `<script type=module>`, toàn bộ mã lệnh của nó sẽ được xem như một module duy nhất.

Mặc dù bạn thường không nghĩ về một module—một tập hợp gồm trạng thái và các phương thức được phơi bày công khai để thao tác trên trạng thái đó—như một chương trình độc lập, nhưng JS trên thực tế vẫn xử lý từng module một cách riêng biệt. Tương tự như cách "phạm vi toàn cục" cho phép các tập tin độc lập hòa lẫn vào nhau tại runtime, việc import một module vào một module khác cho phép khả năng tương tác tại runtime giữa chúng.

Bất kể mô hình tổ chức mã lệnh (và cơ chế nạp) nào được sử dụng cho một tập tin (độc lập hay module), bạn vẫn nên coi mỗi tập tin là một chương trình (nhỏ) của riêng nó, sau đó có thể phối hợp với các chương trình (nhỏ) khác để thực hiện các chức năng cho ứng dụng tổng thể của bạn.

## Giá trị (Values)

Đơn vị thông tin cơ bản nhất trong một chương trình là một giá trị. Giá trị là dữ liệu. Chúng là cách chương trình duy trì trạng thái. Các giá trị trong JS có hai dạng: **nguyên thủy** (primitive) và **đối tượng** (object).

Các giá trị được nhúng vào chương trình bằng cách sử dụng các giá trị nguyên văn (*literals*):

```js
greeting("My name is Kyle.");
```

Trong chương trình này, giá trị `"My name is Kyle."` là một chuỗi ký tự nguyên bản (primitive string literal); chuỗi là tập hợp các ký tự có thứ tự, thường được sử dụng để biểu diễn các từ và câu.

Tôi đã sử dụng ký tự dấu ngoặc kép `"` để *phân cách* (bao quanh, tách biệt, định nghĩa) giá trị chuỗi. Nhưng tôi cũng có thể sử dụng ký tự dấu ngoặc đơn `'`. Việc chọn ký tự dấu ngoặc nào hoàn toàn mang tính phong cách viết mã (stylistic). Điều quan trọng, vì mục đích dễ đọc và dễ bảo trì mã nguồn, là hãy chọn một loại và sử dụng nhất quán trong toàn bộ chương trình.

Một lựa chọn khác để phân cách một chuỗi literal là sử dụng ký tự back-tick `` ` ``. Tuy nhiên, lựa chọn này không đơn thuần chỉ là phong cách; nó còn có sự khác biệt về mặt hành vi. Hãy xem xét:

```js
console.log("My name is ${ firstName }.");
// My name is ${ firstName }.

console.log('My name is ${ firstName }.');
// My name is ${ firstName }.

console.log(`My name is ${ firstName }.`);
// My name is Kyle.
```

Giả sử chương trình này đã định nghĩa một biến `firstName` với giá trị chuỗi là `"Kyle"`, chuỗi được phân cách bằng `` ` `` sau đó sẽ tính toán biểu thức biến (được chỉ định bằng `${ .. }`) thành giá trị hiện tại của nó. Điều này được gọi là **nội suy chuỗi** (interpolation).

Chuỗi được phân cách bằng back-tick `` ` `` có thể được sử dụng mà không cần chứa các biểu thức nội suy, nhưng điều đó làm mất đi toàn bộ mục đích của cú pháp chuỗi literal thay thế này:

```js
console.log(
    `Am I confusing you by omitting interpolation?`
);
// Am I confusing you by omitting interpolation?
```

Cách tiếp cận tốt hơn là sử dụng `"` hoặc `'` (nhắc lại: hãy chọn một và kiên định với nó!) cho các chuỗi *trừ khi bạn cần* nội suy; chỉ dành riêng `` ` `` cho các chuỗi có chứa biểu thức nội suy.

Ngoài chuỗi, các chương trình JS thường chứa các giá trị literal nguyên thủy khác như boolean và số:

```js
while (false) {
    console.log(3.141592);
}
```

`while` đại diện cho một loại vòng lặp, một cách để lặp lại các thao tác *trong khi* điều kiện của nó là true.

Trong trường hợp này, vòng lặp sẽ không bao giờ chạy (và sẽ không có gì được in ra), bởi vì chúng ta đã sử dụng giá trị boolean `false` làm điều kiện vòng lặp. `true` sẽ dẫn đến một vòng lặp chạy mãi mãi, vì vậy hãy cẩn thận!

Con số `3.141592`, như bạn có thể biết, là một giá trị xấp xỉ của số PI toán học đến sáu chữ số đầu tiên. Tuy nhiên, thay vì nhúng một giá trị như vậy, bạn thường sẽ sử dụng giá trị `Math.PI` được định nghĩa trước cho mục đích đó. Một biến thể khác của số là kiểu nguyên thủy `bigint` (số nguyên lớn), được sử dụng để lưu trữ các số lớn tùy ý.

Các con số thường được sử dụng nhiều nhất trong các chương trình để đếm số bước, chẳng hạn như số lần lặp vòng lặp, và truy cập thông tin ở các vị trí số (tức là chỉ số mảng - array index). Chúng ta sẽ nói về mảng/đối tượng trong chốc lát, nhưng ví dụ, nếu có một mảng tên là `names`, chúng ta có thể truy cập phần tử ở vị trí thứ hai của nó như sau:

```js
console.log(`My name is ${ names[1] }.`);
// My name is Kyle.
```

Chúng ta đã sử dụng `1` cho phần tử ở vị trí thứ hai, thay vì `2`, bởi vì giống như hầu hết các ngôn ngữ lập trình, chỉ số mảng của JS bắt đầu từ 0 (`0` là vị trí đầu tiên).

Ngoài chuỗi, số và boolean, hai giá trị *nguyên thủy* khác trong các chương trình JS là `null` và `undefined`. Mặc dù có những điểm khác biệt giữa chúng (một số mang tính lịch sử và một số mang tính đương đại), phần lớn cả hai giá trị đều phục vụ mục đích biểu thị sự *rỗng* (hoặc sự vắng mặt) của một giá trị.

Nhiều nhà phát triển thích đối xử nhất quán với cả hai theo cách này, nghĩa là giả định các giá trị này không thể phân biệt được. Nếu cẩn thận, điều này hoàn toàn khả thi. Tuy nhiên, cách an toàn và tốt nhất là chỉ sử dụng `undefined` làm giá trị rỗng duy nhất, ngay cả khi `null` có vẻ hấp dẫn vì gõ phím ngắn hơn!

```js
while (value != undefined) {
    console.log("Still got something!");
}
```

Giá trị nguyên thủy cuối cùng cần lưu ý là symbol, một giá trị phục vụ mục đích đặc biệt, hành xử như một giá trị ẩn không thể đoán trước. Symbol hầu như chỉ được sử dụng làm các key đặc biệt trên các đối tượng:

```js
hitchhikersGuide[ Symbol("meaning of life") ];
// 42
```

Bạn sẽ không bắt gặp việc sử dụng trực tiếp symbol thường xuyên trong các chương trình JS thông thường. Chúng chủ yếu được sử dụng trong mã mức thấp như thư viện và framework.

### Mảng và Đối tượng

Bên cạnh các kiểu nguyên thủy, loại giá trị khác trong JS là giá trị đối tượng (object value).

Như đã đề cập trước đó, mảng là một loại đối tượng đặc biệt bao gồm một danh sách dữ liệu có thứ tự và được lập chỉ mục bằng số:

```js
var names = [ "Frank", "Kyle", "Peter", "Susan" ];

names.length;
// 4

names[0];
// Frank

names[1];
// Kyle
```

Mảng JS có thể chứa bất kỳ kiểu giá trị nào, dù là nguyên thủy hay đối tượng (bao gồm cả các mảng khác). Như chúng ta sẽ thấy ở phần cuối Chương 3, ngay cả các hàm cũng là những giá trị có thể được chứa trong mảng hoặc đối tượng.

| GHI CHÚ: |
| :--- |
| Hàm, cũng giống như mảng, là một dạng đặc biệt (hay còn gọi là kiểu con - sub-type) của đối tượng. Chúng ta sẽ tìm hiểu chi tiết hơn về hàm trong chốc lát. |

Đối tượng mang tính tổng quát hơn: một tập hợp không có thứ tự gồm các giá trị đa dạng được liên kết theo khóa (key). Nói cách khác, bạn truy cập phần tử bằng tên vị trí dạng chuỗi (còn gọi là "khóa" - key hoặc "thuộc tính" - property) thay vì bằng vị trí số của nó (như với mảng). Ví dụ:

```js
var me = {
    first: "Kyle",
    last: "Simpson",
    age: 39,
    specialties: [ "JS", "Table Tennis" ]
};

console.log(`My name is ${ me.first }.`);
```

Ở đây, `me` đại diện cho một đối tượng, và `first` đại diện cho tên của một vị trí thông tin trong đối tượng đó (tập hợp giá trị). Một tùy chọn cú pháp khác để truy cập thông tin trong một đối tượng bằng thuộc tính/khóa của nó là sử dụng dấu ngoặc vuông `[ ]`, chẳng hạn như `me["first"]`.

### Xác định kiểu giá trị

Để phân biệt các giá trị, toán tử `typeof` sẽ cho bạn biết kiểu tích hợp sẵn của nó, nếu là nguyên thủy, hoặc trả về `"object"` nếu ngược lại:

```js
typeof 42;                  // "number"
typeof "abc";               // "string"
typeof true;                // "boolean"
typeof undefined;           // "undefined"
typeof null;                // "object" -- oops, bug!
typeof { "a": 1 };          // "object"
typeof [1,2,3];             // "object"
typeof function hello(){};  // "function"
```

| CẢNH BÁO: |
| :--- |
| `typeof null` thật không may lại trả về `"object"` thay vì `"null"` như mong đợi. Ngoài ra, `typeof` trả về `"function"` cụ thể cho các hàm, nhưng lại không trả về `"array"` như mong đợi đối với mảng. |

Việc chuyển đổi từ kiểu giá trị này sang kiểu giá trị khác, chẳng hạn như từ chuỗi sang số, được gọi trong JS là "ép kiểu" (coercion). Chúng ta sẽ tìm hiểu chi tiết hơn về điều này ở phần sau của chương.

Các giá trị nguyên thủy và giá trị đối tượng có hành vi khác nhau khi chúng được gán hoặc truyền đi. Chúng ta sẽ tìm hiểu những chi tiết này trong Phụ lục A, "Giá trị vs Tham chiếu" (Values vs References).

## Khai báo và sử dụng biến

Để làm rõ một điều có thể chưa rõ ràng trong phần trước: trong các chương trình JS, các giá trị có thể xuất hiện dưới dạng giá trị nguyên bản (literal) (như nhiều ví dụ trước đó minh họa), hoặc chúng có thể được chứa trong các biến; hãy coi biến đơn giản là những thùng chứa giá trị.

Các biến phải được khai báo (tạo ra) để có thể sử dụng. Có nhiều dạng cú pháp khác nhau để khai báo biến (hay còn gọi là các "định danh" - identifiers), và mỗi dạng lại có những hành vi ngầm định khác nhau.

Ví dụ, hãy xem xét câu lệnh `var`:

```js
var myName = "Kyle";
var age;
```

Từ khóa `var` khai báo một biến được sử dụng trong phần đó của chương trình, và tùy chọn cho phép gán giá trị ban đầu.

Một từ khóa tương tự khác là `let`:

```js
let myName = "Kyle";
let age;
```

Từ khóa `let` có một số điểm khác biệt so với `var`, trong đó điểm rõ ràng nhất là `let` cho phép phạm vi truy cập biến hạn chế hơn so với `var`. Điều này được gọi là "phạm vi khối" (block scoping) trái ngược với phạm vi thông thường hay phạm vi hàm (function scoping).

Hãy xem xét:

```js
var adult = true;

if (adult) {
    var myName = "Kyle";
    let age = 39;
    console.log("Shhh, this is a secret!");
}

console.log(myName);
// Kyle

console.log(age);
// Error!
```

Nỗ lực truy cập `age` bên ngoài câu lệnh `if` sẽ dẫn đến lỗi, bởi vì `age` bị giới hạn trong phạm vi khối của `if`, trong khi `myName` thì không.

Phạm vi khối rất hữu ích trong việc giới hạn mức độ lan rộng của các khai báo biến trong chương trình của chúng ta, giúp ngăn ngừa việc trùng lặp tên biến một cách vô tình.

Nhưng `var` vẫn hữu ích ở chỗ nó truyền đạt thông điệp "biến này sẽ được nhìn thấy bởi một phạm vi rộng hơn (của toàn bộ hàm)". Cả hai hình thức khai báo đều có thể phù hợp ở bất kỳ phần cụ thể nào của chương trình, tùy thuộc vào hoàn cảnh.

| GHI CHÚ: |
| :--- |
| Rất nhiều người thường khuyên rằng nên tránh dùng `var` để chuyển sang dùng `let` (hoặc `const`!), thường là vì sự nhầm lẫn về cách hành vi phạm vi của `var` đã hoạt động kể từ thuở sơ khai của JS. Tôi tin rằng đây là một lời khuyên quá mức khắt khe và rốt cuộc không mang lại lợi ích. Lời khuyên đó ngầm cho rằng bạn không có khả năng học và sử dụng một tính năng đúng cách khi kết hợp với các tính năng khác. Tôi tin rằng bạn *có thể* và *nên* học bất kỳ tính năng nào sẵn có, và sử dụng chúng ở những nơi phù hợp! |

Dạng khai báo thứ ba là `const`. Nó giống như `let` nhưng có thêm một giới hạn là phải được cung cấp một giá trị ngay tại thời điểm nó được khai báo, và không thể được gán lại một giá trị khác sau đó.

Hãy xem xét:

```js
const myBirthday = true;
let age = 39;

if (myBirthday) {
    age = age + 1;    // OK!
    myBirthday = false;  // Error!
}
```

Hằng số `myBirthday` không được phép gán lại.

Các biến được khai báo bằng `const` không phải là "không thể thay đổi", chúng chỉ không thể bị gán lại. Việc sử dụng `const` với các giá trị đối tượng là điều không nên, bởi vì những giá trị đó vẫn có thể bị thay đổi mặc dù biến không thể bị gán lại. Điều này dẫn đến sự nhầm lẫn tiềm ẩn về sau, vì vậy tôi nghĩ sẽ khôn ngoan nếu tránh những tình huống như:

```js
const actors = [
    "Morgan Freeman", "Jennifer Aniston"
];

actors[2] = "Tom Cruise";   // OK :(
actors = [];                // Error!
```

Cách sử dụng ngữ nghĩa tốt nhất của `const` là khi bạn có một giá trị nguyên thủy đơn giản mà bạn muốn đặt cho nó một cái tên hữu ích, chẳng hạn như sử dụng `myBirthday` thay vì `true`. Điều này làm cho các chương trình dễ đọc hơn.

| MẸO: |
| :--- |
| Nếu bạn kiên trì chỉ sử dụng `const` với các giá trị nguyên thủy, bạn sẽ tránh được mọi sự nhầm lẫn giữa việc gán lại (không được phép) so với việc đột biến/thay đổi nội dung (được phép)! Đó là cách an toàn nhất và tốt nhất để sử dụng `const`. |

Bên cạnh `var` / `let` / `const`, còn có các dạng cú pháp khác để khai báo định danh (biến) trong các phạm vi khác nhau. Ví dụ:

```js
function hello(myName) {
    console.log(`Hello, ${ myName }.`);
}

hello("Kyle");
// Hello, Kyle.
```

Định danh `hello` được tạo ra trong phạm vi bên ngoài, và nó cũng tự động được liên kết để tham chiếu đến hàm. Nhưng tham số có tên `myName` chỉ được tạo ra bên trong hàm, và do đó chỉ có thể truy cập được bên trong phạm vi của hàm đó. `hello` và `myName` nói chung hành xử như thể được khai báo bằng `var`.

Một cú pháp khác cũng khai báo một biến là mệnh đề `catch`:

```js
try {
    someError();
}
catch (err) {
    console.log(err);
}
```

`err` là một biến có phạm vi khối chỉ tồn tại bên trong mệnh đề `catch`, như thể nó đã được khai báo bằng `let`.

## Hàm (Functions)

Từ "hàm" (function) có nhiều ý nghĩa khác nhau trong lập trình. Ví dụ, trong thế giới của Lập trình Hàm (Functional Programming), "hàm" có một định nghĩa toán học chính xác và hàm ý một bộ quy tắc nghiêm ngặt phải tuân theo.

Trong JS, chúng ta nên hiểu "hàm" theo nghĩa rộng hơn của một thuật ngữ liên quan khác: "thủ tục" (procedure). Một thủ tục là một tập hợp các câu lệnh có thể được gọi một hoặc nhiều lần, có thể được cung cấp một số đầu vào, và có thể trả về một hoặc nhiều đầu ra.

Từ những ngày đầu của JS, định nghĩa hàm trông như thế này:

```js
function awesomeFunction(coolThings) {
    // ..
    return amazingStuff;
}
```

Đây được gọi là một khai báo hàm (function declaration) vì nó xuất hiện như một câu lệnh độc lập, không phải là một biểu thức trong một câu lệnh khác. Mối liên kết giữa định danh `awesomeFunction` và giá trị hàm diễn ra trong giai đoạn biên dịch mã lệnh, trước khi mã lệnh đó được thực thi.

Trái ngược với câu lệnh khai báo hàm, một biểu thức hàm (function expression) có thể được định nghĩa và gán như thế này:

```js
// let awesomeFunction = ..
// const awesomeFunction = ..
var awesomeFunction = function(coolThings) {
    // ..
    return amazingStuff;
};
```

Hàm này là một biểu thức được gán cho biến `awesomeFunction`. Khác với dạng khai báo hàm, một biểu thức hàm không được liên kết với định danh của nó cho đến khi câu lệnh đó chạy tại runtime.

Điều cực kỳ quan trọng cần lưu ý là trong JS, các hàm là những giá trị có thể được gán (như trong đoạn mã này) và được truyền đi khắp nơi. Trên thực tế, các hàm JS là một kiểu đặc biệt của loại giá trị đối tượng. Không phải tất cả các ngôn ngữ đều coi hàm là giá trị, nhưng điều này là thiết yếu để một ngôn ngữ hỗ trợ mô hình lập trình hàm, như JS vẫn làm.

Hàm JS có thể nhận đầu vào là các tham số:

```js
function greeting(myName) {
    console.log(`Hello, ${ myName }!`);
}

greeting("Kyle");   // Hello, Kyle!
```

Trong đoạn mã này, `myName` được gọi là một tham số (parameter), nó hoạt động như một biến cục bộ bên trong hàm. Các hàm có thể được định nghĩa để nhận bất kỳ số lượng tham số nào, từ không có tham số nào trở lên, tùy bạn thấy phù hợp. Mỗi tham số được gán giá trị đối số (argument) mà bạn truyền vào ở vị trí tương ứng đó (`"Kyle"` ở đây) của lệnh gọi.

Các hàm cũng có thể trả về giá trị bằng cách sử dụng từ khóa `return`:

```js
function greeting(myName) {
    return `Hello, ${ myName }!`;
}

var msg = greeting("Kyle");

console.log(msg);   // Hello, Kyle!
```

Bạn chỉ có thể `return` một giá trị duy nhất, nhưng nếu có nhiều giá trị cần trả về, bạn có thể đóng gói chúng vào một đối tượng/mảng duy nhất.

Vì các hàm là các giá trị, chúng có thể được gán làm thuộc tính trên các đối tượng:

```js
var whatToSay = {
    greeting() {
        console.log("Hello!");
    },
    question() {
        console.log("What's your name?");
    },
    answer() {
        console.log("My name is Kyle.");
    }
};

whatToSay.greeting();
// Hello!
```

Trong đoạn mã này, các tham chiếu đến ba hàm (`greeting()`, `question()`, và `answer()`) được đưa vào đối tượng do `whatToSay` nắm giữ. Mỗi hàm có thể được gọi bằng cách truy cập thuộc tính để lấy giá trị tham chiếu hàm. Hãy so sánh phong cách định nghĩa hàm trên một đối tượng rất trực diện này với cú pháp `class` phức tạp hơn được thảo luận ở phần sau của chương này.

Có rất nhiều dạng đa dạng mà các `function` thể hiện trong JS. Chúng ta sẽ đào sâu vào những biến thể này trong Phụ lục A, "Vô vàn hình thức của hàm" (So Many Function Forms).

## So sánh (Comparisons)

Việc đưa ra các quyết định trong chương trình đòi hỏi phải so sánh các giá trị để xác định danh tính và mối quan hệ của chúng với nhau. JS có một số cơ chế cho phép so sánh giá trị, vì vậy chúng ta hãy xem xét kỹ hơn về chúng.

### Tương đương... đại loại thế

Phép so sánh phổ biến nhất trong các chương trình JS đặt ra câu hỏi: "Liệu giá trị X này có *giống như* giá trị Y kia không?" Tuy nhiên, chính xác thì "giống như" thực sự có nghĩa là gì đối với JS?

Vì lý do tiện dụng và lịch sử, ý nghĩa của nó phức tạp hơn so với kiểu so khớp *danh tính chính xác* hiển nhiên. Đôi khi một phép so sánh bằng có ý định so khớp *chính xác tuyệt đối*, nhưng những lúc khác phép so sánh mong muốn lại rộng hơn một chút, cho phép so khớp *gần như tương đồng* hoặc *có thể thay thế cho nhau*. Nói cách khác, chúng ta phải nhận thức được những khác biệt tinh tế giữa so sánh **bằng nhau** (equality) và so sánh **tương đương** (equivalence).

Nếu bạn đã từng dành thời gian làm việc và đọc về JS, chắc chắn bạn đã thấy toán tử được gọi là "ba dấu bằng" `===`, còn được mô tả là toán tử "so sánh bằng nghiêm ngặt" (strict equality). Điều đó có vẻ khá dễ hiểu, phải không? Chắc chắn "nghiêm ngặt" có nghĩa là chặt chẽ, thu hẹp và *chính xác*.

Không hẳn là *chính xác* như vậy đâu.

Đúng vậy, hầu hết các giá trị tham gia vào phép so sánh bằng `===` sẽ phù hợp với trực giác *hoàn toàn giống hệt nhau* đó. Hãy xem xét một số ví dụ:

```js
3 === 3.0;              // true
"yes" === "yes";        // true
null === null;          // true
false === false;        // true

42 === "42";            // false
"hello" === "Hello";    // false
true === 1;             // false
0 === null;             // false
"" === null;            // false
null === undefined;     // false
```

| GHI CHÚ: |
| :--- |
| Một cách khác mà phép so sánh bằng của `===` thường được mô tả là "kiểm tra cả giá trị và kiểu dữ liệu". Trong một số ví dụ chúng ta đã xem xét cho đến nay, như `42 === "42"`, *kiểu dữ liệu* của cả hai giá trị (số, chuỗi, v.v.) dường như là yếu tố phân biệt. Tuy nhiên, mọi chuyện còn nhiều hơn thế. **Tất cả** các phép so sánh giá trị trong JS đều xem xét kiểu của các giá trị được so sánh, chứ không *chỉ riêng* toán tử `===`. Cụ thể, `===` không cho phép bất kỳ hình thức chuyển đổi kiểu nào (hay còn gọi là "ép kiểu" - coercion) trong phép so sánh của nó, trong khi các phép so sánh JS khác *có* cho phép ép kiểu. |

Nhưng toán tử `===` có một số điểm tinh tế, một thực tế mà nhiều nhà phát triển JS thường bỏ qua, dẫn đến những thiệt thòi cho chính họ. Toán tử `===` được thiết kế để *nói dối* trong hai trường hợp giá trị đặc biệt: `NaN` và `-0`. Hãy xem xét:

```js
NaN === NaN;            // false
0 === -0;               // true
```

Trong trường hợp `NaN`, toán tử `===` *nói dối* và bảo rằng một giá trị `NaN` này không bằng một giá trị `NaN` khác. Trong trường hợp `-0` (vâng, đây là một giá trị thực sự, riêng biệt mà bạn có thể chủ ý sử dụng trong các chương trình của mình!), toán tử `===` *nói dối* và bảo rằng nó bằng với giá trị `0` thông thường.

Vì sự *nói dối* về những phép so sánh như vậy có thể gây phiền toái, tốt nhất là nên tránh sử dụng `===` cho chúng. Đối với các phép so sánh `NaN`, hãy sử dụng tiện ích `Number.isNaN(..)`, tiện ích này không hề *nói dối*. Đối với so sánh `-0`, hãy sử dụng tiện ích `Object.is(..)`, tiện ích này cũng không hề *nói dối*. `Object.is(..)` cũng có thể được sử dụng để kiểm tra `NaN` mà không bị *nói dối*, nếu bạn muốn. Một cách hài hước, bạn có thể coi `Object.is(..)` như toán tử "bốn dấu bằng" `====`, phép so sánh thực-sự-rất-nghiêm-ngặt!

Có những lý do lịch sử và kỹ thuật sâu xa hơn cho những lời *nói dối* này, nhưng điều đó không làm thay đổi sự thật rằng `===` thực sự không phải là phép so sánh *hoàn toàn chính xác một cách nghiêm ngặt*, theo nghĩa *nghiêm ngặt* nhất.

Câu chuyện thậm chí còn phức tạp hơn khi chúng ta xem xét các phép so sánh của các giá trị đối tượng (non-primitives). Hãy xem xét:

```js
[ 1, 2, 3 ] === [ 1, 2, 3 ];    // false
{ a: 42 } === { a: 42 }         // false
(x => x * 2) === (x => x * 2)   // false
```

Chuyện gì đang xảy ra ở đây vậy?

Có vẻ hợp lý khi cho rằng việc kiểm tra tính bằng nhau sẽ xem xét *bản chất* hoặc *nội dung* của giá trị; rốt cuộc, `42 === 42` xem xét giá trị `42` thực tế và so sánh nó. Nhưng khi nói đến các đối tượng, một phép so sánh có nhận thức về nội dung thường được gọi là "bằng nhau về mặt cấu trúc" (structural equality).

JS không định nghĩa `===` là *so sánh bằng về cấu trúc* cho các giá trị đối tượng. Thay vào đó, `===` sử dụng *so sánh bằng về danh tính* (identity equality) cho các giá trị đối tượng.

Trong JS, tất cả các giá trị đối tượng đều được giữ bằng tham chiếu (xem "Giá trị vs Tham chiếu" trong Phụ lục A), được gán và truyền bằng cách sao chép tham chiếu (reference-copy), **và** quay lại cuộc thảo luận hiện tại của chúng ta, được so sánh bằng tính bằng nhau của tham chiếu (danh tính). Hãy xem xét:

```js
var x = [ 1, 2, 3 ];

// việc gán là bằng cách sao chép tham chiếu, nên
// y tham chiếu đến *cùng* một mảng như x,
// chứ không phải một bản sao khác của nó.
var y = x;

y === x;              // true
y === [ 1, 2, 3 ];    // false
x === [ 1, 2, 3 ];    // false
```

Trong đoạn mã này, `y === x` là true vì cả hai biến đều giữ một tham chiếu đến cùng một mảng ban đầu. Nhưng các phép so sánh `=== [1,2,3]` đều thất bại vì `y` và `x` lần lượt được so sánh với các mảng mới *khác* `[1,2,3]`. Cấu trúc và nội dung mảng không thành vấn đề trong phép so sánh này, chỉ có **danh tính tham chiếu** mới có ý nghĩa.

JS không cung cấp cơ chế so sánh bằng về mặt cấu trúc cho các giá trị đối tượng, mà chỉ so sánh danh tính tham chiếu. Để thực hiện so sánh bằng về mặt cấu trúc, bạn sẽ cần phải tự mình triển khai các bước kiểm tra đó.

Nhưng hãy coi chừng, nó phức tạp hơn bạn tưởng đấy. Ví dụ: làm thế nào bạn có thể xác định xem hai tham chiếu hàm có "tương đương về mặt cấu trúc" hay không? Ngay cả việc biến chuỗi để so sánh văn bản mã nguồn của chúng cũng sẽ không tính đến những thứ như closure. JS không cung cấp phép so sánh bằng về cấu trúc bởi vì hầu như không thể xử lý nổi tất cả các trường hợp góc cạnh (corner cases)!

### So sánh có ép kiểu (Coercive Comparisons)

Ép kiểu (coercion) có nghĩa là một giá trị của một kiểu được chuyển đổi sang biểu diễn tương ứng của nó ở một kiểu khác (trong chừng mực có thể). Như chúng ta sẽ thảo luận trong Chương 4, ép kiểu là một trụ cột cốt lõi của ngôn ngữ JS, không phải là một tính năng tùy chọn có thể tránh được một cách hợp lý.

Nhưng ở nơi mà việc ép kiểu gặp gỡ các toán tử so sánh (như so sánh bằng), sự bối rối và thất vọng thật không may lại xuất hiện thường xuyên hơn cả.

Hiếm có tính năng JS nào phải hứng chịu nhiều sự phẫn nộ trong cộng đồng JS rộng lớn hơn toán tử `==`, thường được gọi là toán tử "so sánh bằng lỏng lẻo" (loose equality). Phần lớn các bài viết và các cuộc thảo luận công khai về JS đều lên án toán tử này là được thiết kế kém cỏi và nguy hiểm/đầy lỗi khi được sử dụng trong các chương trình JS. Ngay cả người tạo ra ngôn ngữ này, Brendan Eich, cũng từng than thở về việc thiết kế nó là một sai lầm lớn như thế nào.

Từ những gì tôi có thể nhận thấy, phần lớn sự thất vọng này xuất phát từ một danh sách khá ngắn các trường hợp góc cạnh khó hiểu, nhưng một vấn đề sâu xa hơn là quan niệm sai lầm cực kỳ phổ biến rằng nó thực hiện các phép so sánh mà không xem xét kiểu của các giá trị được so sánh.

Toán tử `==` thực hiện phép so sánh bằng tương tự như cách `===` thực hiện. Trên thực tế, cả hai toán tử đều xem xét kiểu của các giá trị được so sánh. Và nếu phép so sánh là giữa cùng một kiểu giá trị, cả `==` và `===` **hoạt động hoàn toàn giống hệt nhau, không có bất kỳ sự khác biệt nào.**

Nếu các kiểu giá trị được so sánh là khác nhau, `==` khác với `===` ở chỗ nó cho phép ép kiểu trước khi so sánh. Nói cách khác, cả hai đều muốn so sánh các giá trị cùng kiểu, nhưng `==` cho phép chuyển đổi kiểu *trước*, và một khi các kiểu đã được chuyển đổi để giống nhau ở cả hai vế, thì `==` sẽ thực hiện điều tương tự như `===`. Thay vì "so sánh bằng lỏng lẻo", toán tử `==` nên được mô tả là "so sánh bằng có ép kiểu" (coercive equality).

Hãy xem xét:

```js
42 == "42";             // true
1 == true;              // true
```

Trong cả hai phép so sánh, các kiểu giá trị là khác nhau, do đó `==` khiến các giá trị không phải là số (`"42"` và `true`) được chuyển đổi thành số (tương ứng là `42` và `1`) trước khi thực hiện các phép so sánh.

Chỉ cần nhận thức được bản chất này của `==`—rằng nó ưu tiên các phép so sánh số nguyên thủy—sẽ giúp bạn tránh được hầu hết các trường hợp góc cạnh rắc rối, chẳng hạn như tránh xa các cạm bẫy như `"" == 0` hoặc `0 == false`.

Có thể bạn đang nghĩ: "Ồ, vậy thì tôi sẽ luôn luôn tránh bất kỳ phép so sánh bằng có ép kiểu nào (bằng cách dùng `===` thay thế) để tránh những trường hợp góc cạnh đó"! Ờ, tiếc là điều đó không hoàn toàn dễ dàng như bạn hy vọng đâu.

Có khả năng rất cao là bạn sẽ sử dụng các toán tử so sánh quan hệ như `<`, `>` (và thậm chí cả `<=` và `>=`).

Cũng giống như `==`, các toán tử này sẽ hoạt động như thể chúng "nghiêm ngặt" nếu các kiểu được so sánh quan hệ đã khớp nhau sẵn, nhưng chúng sẽ cho phép ép kiểu trước (thường là sang số) nếu các kiểu khác nhau.

Hãy xem xét:

```js
var arr = [ "1", "10", "100", "1000" ];
for (let i = 0; i < arr.length && arr[i] < 500; i++) {
    // sẽ chạy 3 lần
}
```

Phép so sánh `i < arr.length` là "an toàn" khỏi việc ép kiểu vì `i` và `arr.length` luôn luôn là số. Tuy nhiên, `arr[i] < 500` lại kích hoạt việc ép kiểu, bởi vì các giá trị `arr[i]` đều là chuỗi. Do đó, các phép so sánh đó trở thành `1 < 500`, `10 < 500`, `100 < 500`, và `1000 < 500`. Vì phép so sánh thứ tư là false, vòng lặp sẽ dừng lại sau lần lặp thứ ba.

Các toán tử quan hệ này thường sử dụng các phép so sánh số, ngoại trừ trường hợp **cả hai** giá trị được so sánh đều là chuỗi sẵn; trong trường hợp này, chúng sử dụng phép so sánh thứ tự bảng chữ cái (như trong từ điển) của các chuỗi:

```js
var x = "10";
var y = "9";

x < y;      // true, coi chừng!
```

Không có cách nào để khiến các toán tử quan hệ này tránh được việc ép kiểu, ngoại trừ việc không bao giờ sử dụng các kiểu không khớp nhau trong các phép so sánh. Mục tiêu đó có lẽ rất đáng khen ngợi, nhưng vẫn rất có khả năng bạn sẽ gặp phải trường hợp mà các kiểu *có thể* khác nhau.

Cách tiếp cận khôn ngoan hơn không phải là trốn tránh các phép so sánh có ép kiểu, mà là đón nhận và học hỏi cặn kẽ mọi ngóc ngách của chúng.

Các phép so sánh có ép kiểu xuất hiện ở những nơi khác trong JS, chẳng hạn như các câu lệnh điều kiện (`if`, v.v.), điều mà chúng ta sẽ xem lại trong Phụ lục A, "So sánh điều kiện có ép kiểu" (Coercive Conditional Comparison).

## Cách chúng ta tổ chức mã nguồn trong JS

Hai mô hình chính để tổ chức mã lệnh (dữ liệu và hành vi) được sử dụng rộng rãi trong toàn bộ hệ sinh thái JS: class và module. Các mô hình này không loại trừ lẫn nhau; nhiều chương trình hoàn toàn có thể và thực tế đã sử dụng cả hai. Các chương trình khác sẽ chỉ trung thành với một mô hình, hoặc thậm chí không dùng mô hình nào cả!

Ở một số khía cạnh, các mô hình này rất khác nhau. Nhưng thú vị là, ở những khía cạnh khác, chúng chỉ là hai mặt của cùng một đồng xu. Để thành thạo JS đòi hỏi phải hiểu cả hai mô hình và biết khi nào chúng phù hợp (và khi nào không!).

### Class

Các thuật ngữ "hướng đối tượng" (object-oriented), "hướng class" (class-oriented), và "class" đều chứa đựng rất nhiều chi tiết và sắc thái; chúng không có một định nghĩa phổ quát duy nhất.

Chúng tôi sẽ sử dụng một định nghĩa phổ biến và có phần truyền thống ở đây, định nghĩa có khả năng quen thuộc nhất đối với những người có nền tảng về các ngôn ngữ "hướng đối tượng" như C++ và Java.

Một class trong một chương trình là định nghĩa về một "kiểu" cấu trúc dữ liệu tùy chỉnh bao gồm cả dữ liệu và các hành vi thao tác trên dữ liệu đó. Các class xác định cách một cấu trúc dữ liệu như vậy hoạt động, nhưng bản thân các class không phải là các giá trị cụ thể. Để có được một giá trị cụ thể mà bạn có thể sử dụng trong chương trình, một class phải được *khởi tạo* (instantiated - bằng từ khóa `new`) một hoặc nhiều lần.

Hãy xem xét:

```js
class Page {
    constructor(text) {
        this.text = text;
    }

    print() {
        console.log(this.text);
    }
}

class Notebook {
    constructor() {
        this.pages = [];
    }

    addPage(text) {
        var page = new Page(text);
        this.pages.push(page);
    }

    print() {
        for (let page of this.pages) {
            page.print();
        }
    }
}

var mathNotes = new Notebook();
mathNotes.addPage("Arithmetic: + - * / ...");
mathNotes.addPage("Trigonometry: sin cos tan ...");

mathNotes.print();
// ..
```

Trong class `Page`, dữ liệu là một chuỗi văn bản được lưu trữ trong một thuộc tính thành viên `this.text`. Hành vi là `print()`, một phương thức in văn bản ra console.

Đối với class `Notebook`, dữ liệu là một mảng gồm các thực thể (instance) của `Page`. Hành vi là `addPage(..)`, một phương thức khởi tạo các trang `Page` mới và thêm chúng vào danh sách, cũng như `print()` (in ra tất cả các trang trong cuốn sổ tay).

Câu lệnh `mathNotes = new Notebook()` tạo ra một thực thể của class `Notebook`, và `page = new Page(text)` là nơi các thực thể của class `Page` được tạo ra.

Hành vi (phương thức) chỉ có thể được gọi trên các thực thể (chứ không phải trên chính các class), chẳng hạn như `mathNotes.addPage(..)` và `page.print()`.

Cơ chế `class` cho phép đóng gói dữ liệu (`text` và `pages`) để được tổ chức cùng với các hành vi của chúng (ví dụ: `addPage(..)` và `print()`). Chương trình tương tự hoàn toàn có thể được xây dựng mà không cần bất kỳ định nghĩa `class` nào, nhưng nó có thể sẽ ít ngăn nắp hơn nhiều, khó đọc và khó suy luận hơn, và dễ phát sinh lỗi cũng như khó bảo trì hơn.

#### Kế thừa trong Class (Class Inheritance)

Một khía cạnh khác vốn có của thiết kế "hướng class" truyền thống, mặc dù ít được sử dụng phổ biến hơn một chút trong JS, là "kế thừa" (inheritance) (và "đa hình" - polymorphism). Hãy xem xét:

```js
class Publication {
    constructor(title,author,pubDate) {
        this.title = title;
        this.author = author;
        this.pubDate = pubDate;
    }

    print() {
        console.log(`
            Title: ${ this.title }
            By: ${ this.author }
            ${ this.pubDate }
        `);
    }
}
```

Class `Publication` này định nghĩa một tập hợp các hành vi chung mà bất kỳ ấn phẩm nào cũng có thể cần.

Bây giờ chúng ta hãy xem xét các loại ấn phẩm cụ thể hơn, như `Book` và `BlogPost`:

```js
class Book extends Publication {
    constructor(bookDetails) {
        super(
            bookDetails.title,
            bookDetails.author,
            bookDetails.pubDate
        );
        this.publisher = bookDetails.publisher;
        this.ISBN = bookDetails.ISBN;
    }

    print() {
        super.print();
        console.log(`
            Publisher: ${ this.publisher }
            ISBN: ${ this.ISBN }
        `);
    }
}

class BlogPost extends Publication {
    constructor(title,author,pubDate,URL) {
        super(title,author,pubDate);
        this.URL = URL;
    }

    print() {
        super.print();
        console.log(this.URL);
    }
}
```

Cả `Book` và `BlogPost` đều sử dụng mệnh đề `extends` để *mở rộng* (kế thừa) định nghĩa chung của `Publication` nhằm bổ sung thêm các hành vi. Lệnh gọi `super(..)` trong mỗi constructor ủy quyền cho constructor của class cha `Publication` thực hiện công việc khởi tạo của nó, và sau đó chúng làm những việc cụ thể hơn tùy theo loại ấn phẩm tương ứng của mình (còn gọi là "class con" - sub-class hoặc child class).

Bây giờ hãy xem xét việc sử dụng các class con này:

```js
var YDKJS = new Book({
    title: "You Don't Know JS",
    author: "Kyle Simpson",
    pubDate: "June 2014",
    publisher: "O'Reilly",
    ISBN: "123456-789"
});

YDKJS.print();
// Title: You Don't Know JS
// By: Kyle Simpson
// June 2014
// Publisher: O'Reilly
// ISBN: 123456-789

var forAgainstLet = new BlogPost(
    "For and against let",
    "Kyle Simpson",
    "October 27, 2014",
    "https://davidwalsh.name/for-and-against-let"
);

forAgainstLet.print();
// Title: For and against let
// By: Kyle Simpson
// October 27, 2014
// https://davidwalsh.name/for-and-against-let
```

Hãy chú ý rằng cả hai thực thể class con đều có một phương thức `print()`, phương thức này ghi đè (override) phương thức `print()` *được kế thừa* từ class cha `Publication`. Mỗi phương thức `print()` bị ghi đè của class con đó đều gọi `super.print()` để thực thi phiên bản được kế thừa của phương thức `print()`.

Việc cả phương thức được kế thừa và phương thức ghi đè có thể có cùng tên và cùng tồn tại được gọi là *tính đa hình* (polymorphism).

Kế thừa là một công cụ mạnh mẽ để tổ chức dữ liệu/hành vi trong các đơn vị logic riêng biệt (các class), nhưng vẫn cho phép class con phối hợp với class cha bằng cách truy cập/sử dụng hành vi và dữ liệu của nó.

### Module

Mô hình module về cơ bản có cùng mục tiêu với mô hình class, đó là nhóm dữ liệu và hành vi lại với nhau thành các đơn vị logic. Cũng giống như class, các module có thể "bao gồm" hoặc "truy cập" dữ liệu và hành vi của các module khác, vì mục đích phối hợp hoạt động.

Nhưng module có một số điểm khác biệt quan trọng so với class. Đáng chú ý nhất là cú pháp hoàn toàn khác nhau.

#### Module Cổ điển (Classic Modules)

ES6 đã bổ sung một dạng cú pháp module vào cú pháp JS gốc, điều mà chúng ta sẽ tìm hiểu trong giây lát. Nhưng từ những ngày đầu của JS, module đã là một mô hình quan trọng và phổ biến được tận dụng trong vô số chương trình JS, ngay cả khi không có một cú pháp chuyên biệt.

Đặc điểm nhận dạng then chốt của một *module cổ điển* là một hàm bao bọc bên ngoài (chạy ít nhất một lần), trả về một "thực thể" của module với một hoặc nhiều hàm được phơi bày ra ngoài có thể thao tác trên dữ liệu nội bộ (ẩn kín) của thực thể module đó.

Bởi vì một module dạng này *chỉ đơn thuần là một hàm*, và việc gọi nó sẽ tạo ra một "thực thể" của module, nên một tên gọi khác cho các hàm này là "hàm tạo module" (module factories).

Hãy xem xét dạng module cổ điển của các class `Publication`, `Book`, và `BlogPost` trước đó:

```js
function Publication(title,author,pubDate) {
    var publicAPI = {
        print() {
            console.log(`
                Title: ${ title }
                By: ${ author }
                ${ pubDate }
            `);
        }
    };

    return publicAPI;
}

function Book(bookDetails) {
    var pub = Publication(
        bookDetails.title,
        bookDetails.author,
        bookDetails.publishedOn
    );

    var publicAPI = {
        print() {
            pub.print();
            console.log(`
                Publisher: ${ bookDetails.publisher }
                ISBN: ${ bookDetails.ISBN }
            `);
        }
    };

    return publicAPI;
}

function BlogPost(title,author,pubDate,URL) {
    var pub = Publication(title,author,pubDate);

    var publicAPI = {
        print() {
            pub.print();
            console.log(URL);
        }
    };

    return publicAPI;
}
```

So sánh các dạng này với các dạng `class`, có nhiều điểm tương đồng hơn là khác biệt.

Dạng `class` lưu trữ các phương thức và dữ liệu trên một thực thể đối tượng, vốn phải được truy cập với tiền tố `this.`. Với module, các phương thức và dữ liệu được truy cập dưới dạng các biến định danh nằm trong phạm vi (scope), mà không cần bất kỳ tiền tố `this.` nào.

Với `class`, "API" của một thực thể là ngầm định trong định nghĩa class—ngoài ra, tất cả dữ liệu và phương thức đều là công khai (public). Với hàm tạo module (module factory function), bạn tạo và trả về một đối tượng một cách tường minh với bất kỳ phương thức nào được phơi bày công khai, và bất kỳ dữ liệu hoặc phương thức nào khác không được tham chiếu sẽ vẫn ở chế độ riêng tư (private) bên trong hàm factory.

Có những biến thể khác của dạng hàm factory này khá phổ biến trên khắp JS, ngay cả trong năm 2020; bạn có thể bắt gặp các dạng này trong các chương trình JS khác nhau: AMD (Asynchronous Module Definition), UMD (Universal Module Definition), và CommonJS (module theo phong cách Node.js cổ điển). Các biến thể chỉ có những điểm khác biệt nhỏ (không hoàn toàn tương thích với nhau). Tuy nhiên, tất cả các dạng này đều dựa trên các nguyên tắc cơ bản giống nhau.

Hãy xem xét cách sử dụng (hay còn gọi là "khởi tạo") của các hàm factory module này:

```js
var YDKJS = Book({
    title: "You Don't Know JS",
    author: "Kyle Simpson",
    publishedOn: "June 2014",
    publisher: "O'Reilly",
    ISBN: "123456-789"
});

YDKJS.print();
// Title: You Don't Know JS
// By: Kyle Simpson
// June 2014
// Publisher: O'Reilly
// ISBN: 123456-789

var forAgainstLet = BlogPost(
    "For and against let",
    "Kyle Simpson",
    "October 27, 2014",
    "https://davidwalsh.name/for-and-against-let"
);

forAgainstLet.print();
// Title: For and against let
// By: Kyle Simpson
// October 27, 2014
// https://davidwalsh.name/for-and-against-let
```

Sự khác biệt duy nhất có thể quan sát được ở đây là không sử dụng `new`, gọi các hàm factory module như các hàm bình thường.

#### ES Modules

ES modules (ESM), được giới thiệu vào ngôn ngữ JS trong ES6, nhằm phục vụ phần lớn tinh thần và mục đích giống như các *module cổ điển* vừa được mô tả, đặc biệt là có tính đến các biến thể và trường hợp sử dụng quan trọng từ AMD, UMD và CommonJS.

Tuy nhiên, cách tiếp cận triển khai thì khác biệt đáng kể.

Thứ nhất, không có hàm bao bọc để *định nghĩa* một module. Ngữ cảnh bao bọc là một tập tin. ESM luôn dựa trên tập tin; một tập tin, một module.

Thứ hai, bạn không tương tác với "API" của module một cách tường minh thông qua đối tượng trả về, mà sử dụng từ khóa `export` để thêm một biến hoặc phương thức vào định nghĩa API công khai của nó. Nếu một thứ gì đó được định nghĩa trong module nhưng không được `export`, thì nó sẽ được giữ kín (giống như với *module cổ điển*).

Thứ ba, và có lẽ là điểm khác biệt đáng chú ý nhất so với các mô hình đã thảo luận trước đây, bạn không "khởi tạo" một ES module, bạn chỉ cần `import` nó để sử dụng thực thể duy nhất của nó. ESM về bản chất là các "singleton", ở chỗ chỉ có một thực thể duy nhất từng được tạo ra, tại thời điểm `import` đầu tiên trong chương trình của bạn, và tất cả các lần `import` khác chỉ nhận được một tham chiếu đến chính thực thể duy nhất đó. Nếu module của bạn cần hỗ trợ nhiều lần khởi tạo, bạn phải cung cấp một hàm factory *theo phong cách module cổ điển* trên định nghĩa ESM của bạn cho mục đích đó.

Trong ví dụ xuyên suốt của chúng ta, chúng tôi giả định việc khởi tạo nhiều thực thể, do đó các đoạn mã sau đây sẽ kết hợp cả ESM và *module cổ điển*.

Hãy xem xét tập tin `publication.js`:

```js
function printDetails(title,author,pubDate) {
    console.log(`
        Title: ${ title }
        By: ${ author }
        ${ pubDate }
    `);
}

export function create(title,author,pubDate) {
    var publicAPI = {
        print() {
            printDetails(title,author,pubDate);
        }
    };

    return publicAPI;
}
```

Để import và sử dụng module này từ một ES module khác như `blogpost.js`:

```js
import { create as createPub } from "publication.js";

function printDetails(pub,URL) {
    pub.print();
    console.log(URL);
}

export function create(title,author,pubDate,URL) {
    var pub = createPub(title,author,pubDate);

    var publicAPI = {
        print() {
            printDetails(pub,URL);
        }
    };

    return publicAPI;
}
```

Và cuối cùng, để sử dụng module này, chúng ta import vào một ES module khác như `main.js`:

```js
import { create as newBlogPost } from "blogpost.js";

var forAgainstLet = newBlogPost(
    "For and against let",
    "Kyle Simpson",
    "October 27, 2014",
    "https://davidwalsh.name/for-and-against-let"
);

forAgainstLet.print();
// Title: For and against let
// By: Kyle Simpson
// October 27, 2014
// https://davidwalsh.name/for-and-against-let
```

| GHI CHÚ: |
| :--- |
| Mệnh đề `as newBlogPost` trong câu lệnh `import` là tùy chọn; nếu bỏ qua, một hàm cấp cao nhất có tên đơn giản là `create(..)` sẽ được import. Trong trường hợp này, tôi đổi tên nó để dễ đọc hơn; tên factory chung chung hơn là `create(..)` sẽ trở nên có tính mô tả ngữ nghĩa rõ ràng hơn về mục đích của nó thành `newBlogPost(..)`. |

Như đã thấy, các ES module có thể sử dụng *module cổ điển* bên trong nếu chúng cần hỗ trợ nhiều lần khởi tạo. Ngoài ra, chúng ta cũng có thể phơi bày một `class` từ module của mình thay vì một hàm factory `create(..)`, với kết quả nhìn chung tương tự. Tuy nhiên, vì tại thời điểm đó bạn đã sử dụng ESM rồi, tôi khuyên bạn nên gắn bó với *module cổ điển* thay vì `class`.

Nếu module của bạn chỉ cần một thực thể duy nhất, bạn có thể bỏ qua các lớp phức tạp bổ sung: `export` trực tiếp các phương thức công khai của nó.

## Hang thỏ ngày càng sâu hơn

Như đã hứa ở phần đầu của chương này, chúng ta vừa lướt qua một phạm vi bề mặt rộng lớn bao gồm các phần chính của ngôn ngữ JS. Đầu óc bạn có thể vẫn còn đang quay cuồng, nhưng điều đó hoàn toàn tự nhiên sau một lượng lớn thông tin dồn dập như vậy!

Ngay cả chỉ với cuộc khảo sát "ngắn gọn" này về JS, chúng ta đã đề cập hoặc gợi mở rất nhiều chi tiết mà bạn nên cân nhắc cẩn thận và đảm bảo rằng mình thấy thoải mái với chúng. Tôi thực sự nghiêm túc khi đưa ra lời khuyên này: hãy đọc lại chương này, có thể là vài lần.

Trong chương tiếp theo, chúng ta sẽ đào sâu hơn nhiều vào một số khía cạnh quan trọng về cách JS vận hành tại phần cốt lõi của nó. Nhưng trước khi bạn lần theo hang thỏ đó xuống sâu hơn nữa, hãy đảm bảo rằng bạn đã dành đủ thời gian để tiêu hóa trọn vẹn những gì chúng ta vừa cùng nhau tìm hiểu ở đây.
