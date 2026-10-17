# You Don't Know JS Yet: Get Started - Tái bản lần 2
# Phụ lục A: Tìm hiểu sâu hơn

Trong phụ lục này, chúng ta sẽ đi sâu khám phá một số chủ đề từ nội dung các chương chính một cách chi tiết hơn. Hãy xem nội dung này như một phần xem trước tùy chọn về một vài chi tiết mang tính sắc thái được đề cập xuyên suốt phần còn lại của bộ sách.

## Giá trị và Tham chiếu (Values vs. References)

Trong Chương 2, chúng ta đã giới thiệu hai loại giá trị chính: kiểu nguyên thủy (primitives) và đối tượng (objects). Nhưng chúng ta vẫn chưa thảo luận về một điểm khác biệt then chốt giữa hai loại này: cách các giá trị này được gán và truyền đi.

Trong nhiều ngôn ngữ, lập trình viên có thể chọn giữa việc gán/truyền một giá trị dưới dạng chính giá trị đó (by value), hoặc dưới dạng một tham chiếu tới giá trị đó (by reference). Tuy nhiên, trong JS, quyết định này hoàn toàn được xác định bởi kiểu của giá trị. Điều này gây ngạc nhiên cho rất nhiều lập trình viên từ các ngôn ngữ khác khi họ mới bắt đầu sử dụng JS.

Nếu bạn gán/truyền chính bản thân một giá trị, giá trị đó sẽ được sao chép. Ví dụ:

```js
var myName = "Kyle";

var yourName = myName;
```

Ở đây, biến `yourName` có một bản sao riêng biệt của chuỗi `"Kyle"` từ giá trị được lưu trữ trong `myName`. Đó là bởi vì giá trị này là kiểu nguyên thủy, và các giá trị nguyên thủy luôn luôn được gán/truyền dưới dạng **bản sao giá trị (value copies)**.

Dưới đây là cách bạn có thể chứng minh rằng có hai giá trị riêng biệt liên quan:

```js
var myName = "Kyle";

var yourName = myName;

myName = "Frank";

console.log(myName);
// Frank

console.log(yourName);
// Kyle
```

Bạn có thấy `yourName` không hề bị ảnh hưởng bởi việc gán lại `myName` thành `"Frank"` không? Đó là vì mỗi biến nắm giữ bản sao giá trị riêng của nó.

Ngược lại, tham chiếu (references) là ý niệm về việc có hai hoặc nhiều biến cùng trỏ vào một giá trị, sao cho việc chỉnh sửa giá trị dùng chung này sẽ được phản ánh qua bất kỳ lượt truy cập nào thông qua các tham chiếu đó. Trong JS, chỉ có các giá trị đối tượng (mảng, đối tượng, hàm, v.v.) mới được coi là tham chiếu.

Hãy xem xét:

```js
var myAddress = {
    street: "123 JS Blvd",
    city: "Austin",
    state: "TX"
};

var yourAddress = myAddress;

// I've got to move to a new house!
myAddress.street = "456 TS Ave";

console.log(yourAddress.street);
// 456 TS Ave
```

Vì giá trị được gán cho `myAddress` là một đối tượng, nó được nắm giữ/gán theo tham chiếu, và do đó phép gán cho biến `yourAddress` là một bản sao của tham chiếu, chứ không phải bản thân giá trị đối tượng. Đó là lý do tại sao giá trị được cập nhật gán cho `myAddress.street` lại được phản ánh khi chúng ta truy cập `yourAddress.street`. Cả `myAddress` và `yourAddress` đều có các bản sao của tham chiếu trỏ tới cùng một đối tượng dùng chung duy nhất, nên việc cập nhật một bên cũng chính là cập nhật cả hai bên.

Một lần nữa, JS quyết định hành vi sao chép theo giá trị (value-copy) hay sao chép theo tham chiếu (reference-copy) dựa trên kiểu giá trị. Các kiểu nguyên thủy được giữ theo giá trị, các đối tượng được giữ theo tham chiếu. Trong JS không có cách nào để ghi đè (override) điều này theo cả hai chiều.

## Rất nhiều dạng hàm (So Many Function Forms)

Hãy nhớ lại đoạn mã này từ phần "Functions" trong Chương 2:

```js
var awesomeFunction = function(coolThings) {
    // ..
    return amazingStuff;
};
```

Biểu thức hàm (function expression) ở đây được gọi là một *biểu thức hàm ẩn danh (anonymous function expression)*, vì nó không có định danh tên nằm giữa từ khóa `function` và danh sách tham số `(..)`. Điểm này khiến nhiều lập trình viên JS bối rối vì kể từ ES6, JS thực hiện một cơ chế "suy luận tên" (name inference) trên một hàm ẩn danh:

```js
awesomeFunction.name;
// "awesomeFunction"
```

Thuộc tính `name` của một hàm sẽ tiết lộ tên được cung cấp trực tiếp của nó (trong trường hợp khai báo hàm - declaration) hoặc tên được suy luận của nó trong trường hợp biểu thức hàm ẩn danh. Giá trị đó thường được các công cụ dành cho lập trình viên (developer tools) sử dụng khi kiểm tra giá trị của hàm hoặc khi báo cáo dấu vết ngăn xếp lỗi (error stack trace).

Vì vậy, ngay cả một biểu thức hàm ẩn danh *có thể* cũng nhận được một cái tên. Tuy nhiên, việc suy luận tên chỉ xảy ra trong các trường hợp hạn chế chẳng hạn như khi biểu thức hàm được gán (bằng dấu `=`). Ví dụ, nếu bạn truyền một biểu thức hàm làm đối số cho một lời gọi hàm, việc suy luận tên sẽ không diễn ra; thuộc tính `name` sẽ là một chuỗi rỗng, và console của lập trình viên thường sẽ báo cáo là "(anonymous function)".

Ngay cả khi một cái tên được suy luận, **nó vẫn là một hàm ẩn danh.** Tại sao lại như vậy? Bởi vì cái tên được suy luận chỉ là một giá trị chuỗi siêu dữ liệu (metadata), chứ không phải là một định danh khả dụng để tham chiếu đến hàm. Một hàm ẩn danh không có định danh để tự tham chiếu đến chính nó từ bên trong chính nó — dùng cho đệ quy (recursion), hủy đăng ký sự kiện (event unbinding), v.v.

Hãy so sánh dạng biểu thức hàm ẩn danh với:

```js
// let awesomeFunction = ..
// const awesomeFunction = ..
var awesomeFunction = function someName(coolThings) {
    // ..
    return amazingStuff;
};

awesomeFunction.name;
// "someName"
```

Biểu thức hàm này là một *biểu thức hàm có tên (named function expression)*, vì định danh `someName` được liên kết trực tiếp với biểu thức hàm tại thời điểm biên dịch (compile time); việc liên kết với định danh `awesomeFunction` vẫn chỉ diễn ra tại runtime tại thời điểm thực thi câu lệnh đó. Hai định danh này không nhất thiết phải trùng khớp; đôi khi việc đặt chúng khác nhau là hợp lý, nhưng lúc khác đặt chúng giống nhau lại tốt hơn.

Hãy chú ý rằng tên hàm rõ ràng, định danh `someName`, sẽ được ưu tiên hơn khi gán một *tên* cho thuộc tính `name`.

Vậy biểu thức hàm nên có tên hay ẩn danh? Các quan điểm về điều này rất khác nhau. Hầu hết các lập trình viên thường không bận tâm đến việc sử dụng các hàm ẩn danh. Chúng ngắn hơn, và chắc chắn là phổ biến hơn nhiều trong phạm vi rộng lớn của mã nguồn JS ngoài kia.

Theo quan điểm của tôi, nếu một hàm tồn tại trong chương trình của bạn, nó phải có một mục đích; nếu không, hãy loại bỏ nó! Và nếu nó có một mục đích, nó sẽ có một cái tên tự nhiên mô tả mục đích đó.

Nếu một hàm có tên, bạn — tác giả của mã nguồn — nên đưa tên đó vào trong mã, để người đọc không phải suy đoán tên đó từ việc đọc và tự thực thi mã nguồn của hàm đó trong đầu. Ngay cả một thân hàm đơn giản như `x * 2` cũng phải đọc mới có thể suy luận ra cái tên như "double" hoặc "multBy2"; việc tốn thêm chút công sức suy nghĩ đó là không cần thiết khi bạn chỉ cần bỏ ra một giây để đặt tên cho hàm là "double" hoặc "multBy2" *một lần duy nhất*, giúp người đọc tiết kiệm công sức suy nghĩ lặp đi lặp lại đó mỗi khi đọc lại mã trong tương lai.

Đáng tiếc là ở một số khía cạnh, tính đến đầu năm 2020, JS có rất nhiều dạng định nghĩa hàm khác nhau (và có thể còn nhiều hơn nữa trong tương lai!).

Dưới đây là một số dạng khai báo khác:

```js
// generator function declaration
function *two() { .. }

// async function declaration
async function three() { .. }

// async generator function declaration
async function *four() { .. }

// named function export declaration (ES6 modules)
export function five() { .. }
```

Và đây là một số dạng biểu thức hàm (trong số rất nhiều dạng!):

```js
// IIFE
(function(){ .. })();
(function namedIIFE(){ .. })();

// asynchronous IIFE
(async function(){ .. })();
(async function namedAIIFE(){ .. })();

// arrow function expressions
var f;
f = () => 42;
f = x => x * 2;
f = (x) => x * 2;
f = (x,y) => x * y;
f = x => ({ x: x * 2 });
f = x => { return x * 2; };
f = async x => {
    var y = await doSomethingAsync(x);
    return y * 2;
};
someOperation( x => x * 2 );
// ..
```

Hãy nhớ rằng các biểu thức hàm mũi tên (arrow function expressions) là **ẩn danh về mặt cú pháp (syntactically anonymous)**, nghĩa là cú pháp không cung cấp cách nào để gắn trực tiếp một định danh tên cho hàm. Biểu thức hàm này có thể nhận được một cái tên suy luận, nhưng chỉ khi nó nằm ở một trong các dạng gán, chứ không phải ở dạng (phổ biến hơn!) là được truyền làm đối số cho một lời gọi hàm (như ở dòng cuối của đoạn mã trên).

Vì tôi không nghĩ việc sử dụng thường xuyên các hàm ẩn danh trong chương trình của bạn là một ý hay, nên tôi không ủng hộ việc lạm dụng cú pháp hàm mũi tên `=>`. Loại hàm này thực sự có một mục đích cụ thể (đó là xử lý từ khóa `this` theo phạm vi từ vựng - lexical `this`), nhưng điều đó không có nghĩa là chúng ta nên sử dụng nó cho mọi hàm mà chúng ta viết. Hãy dùng công cụ thích hợp nhất cho từng công việc.

Các hàm cũng có thể được xác định bên trong các định nghĩa class và định nghĩa đối tượng kiểu literal (object literal definitions). Chúng thường được gọi là các "phương thức" (methods) khi ở các dạng này, mặc dù trong JS thuật ngữ này không có nhiều sự khác biệt rõ rệt so với "hàm":

```js
class SomethingKindaGreat {
    // class methods
    coolMethod() { .. }   // no commas!
    boringMethod() { .. }
}

var EntirelyDifferent = {
    // object methods
    coolMethod() { .. },   // commas!
    boringMethod() { .. },

    // (anonymous) function expression property
    oldSchool: function() { .. }
};
```

Phù! Quả là có rất nhiều cách khác nhau để định nghĩa hàm.

Không có lối tắt đơn giản nào ở đây cả; bạn chỉ cần dần quen thuộc với tất cả các dạng hàm để có thể nhận ra chúng trong mã nguồn hiện có và sử dụng chúng một cách thích hợp trong mã bạn viết. Hãy nghiên cứu kỹ và thực hành nhé!

## So sánh điều kiện có ép kiểu (Coercive Conditional Comparison)

Đúng vậy, tiêu đề phần này quả là hơi dài dòng và khó đọc. Nhưng chúng ta đang nói về điều gì? Chúng ta đang nói về việc các biểu thức điều kiện cần thực hiện các phép so sánh theo hướng ép kiểu (coercion-oriented comparisons) để đưa ra quyết định.

Các câu lệnh `if` và toán tử ba ngôi `? :`, cũng như các mệnh đề kiểm tra trong các vòng lặp `while` và `for`, đều thực hiện phép so sánh giá trị ngầm định. Nhưng là loại so sánh nào? Nó là "nghiêm ngặt" (strict) hay "ép kiểu" (coercive)? Thật ra là cả hai.

Hãy xem xét:

```js
var x = 1;

if (x) {
    // will run!
}

while (x) {
    // will run, once!
    x = false;
}
```

Bạn có thể nghĩ về các biểu thức điều kiện `(x)` này như sau:

```js
var x = 1;

if (x == true) {
    // will run!
}

while (x == true) {
    // will run, once!
    x = false;
}
```

Trong trường hợp cụ thể này -- giá trị của `x` là `1` -- mô hình tư duy (mental model) đó có tác dụng, nhưng nó không còn chính xác khi xét rộng hơn. Hãy xem xét:

```js
var x = "hello";

if (x) {
    // will run!
}

if (x == true) {
    // won't run :(
}
```

Rắc rối rồi. Vậy câu lệnh `if` thực sự đang làm gì? Đây mới là mô hình tư duy chính xác hơn:

```js
var x = "hello";

if (Boolean(x) == true) {
    // will run
}

// which is the same as:

if (Boolean(x) === true) {
    // will run
}
```

Vì hàm `Boolean(..)` luôn trả về một giá trị có kiểu boolean, nên `==` hay `===` trong đoạn mã này không còn quan trọng; cả hai đều sẽ làm điều tương tự. Nhưng phần quan trọng là nhận ra rằng trước khi so sánh, một quá trình ép kiểu (coercion) đã diễn ra, từ bất kỳ kiểu nào hiện tại của `x` sang boolean.

Bạn đơn giản là không thể tránh khỏi việc ép kiểu trong các phép so sánh của JS. Hãy nghiêm túc đối mặt và học về chúng.

## "Class" theo kiểu Prototype (Prototypal "Classes")

Trong Chương 3, chúng ta đã giới thiệu về prototype và chỉ ra cách chúng ta có thể liên kết các đối tượng thông qua một chuỗi prototype (prototype chain).

Một cách khác để thiết lập các liên kết prototype như vậy từng đóng vai trò là tiền thân (thành thực mà nói thì khá xấu xí) cho sự thanh lịch của hệ thống `class` trong ES6 (xem Chương 2, "Classes"), và được gọi là các prototypal class (lớp dựa trên prototype).

| MẸO: |
| :--- |
| Mặc dù phong cách viết mã này ngày nay khá hiếm gặp trong JS, nhưng điều kỳ lạ là người ta vẫn rất thường hay hỏi về nó trong các cuộc phỏng vấn xin việc! |

Trước tiên, hãy nhớ lại phong cách lập trình `Object.create(..)`:

```js
var Classroom = {
    welcome() {
        console.log("Welcome, students!");
    }
};

var mathClass = Object.create(Classroom);

mathClass.welcome();
// Welcome, students!
```

Ở đây, đối tượng `mathClass` được liên kết qua prototype của nó với một đối tượng `Classroom`. Thông qua liên kết này, lời gọi hàm `mathClass.welcome()` được ủy nhiệm (delegate) cho phương thức được định nghĩa trên `Classroom`.

Mô hình prototypal class sẽ gán nhãn cho hành vi ủy nhiệm này là "kế thừa" (inheritance), và định nghĩa nó theo cách khác (với cùng hành vi) như sau:

```js
function Classroom() {
    // ..
}

Classroom.prototype.welcome = function hello() {
    console.log("Welcome, students!");
};

var mathClass = new Classroom();

mathClass.welcome();
// Welcome, students!
```

Theo mặc định, tất cả các hàm đều tham chiếu đến một đối tượng rỗng tại thuộc tính có tên là `prototype`. Bất chấp cách đặt tên gây nhầm lẫn này, đây **không phải** là *prototype* của hàm (nơi hàm được liên kết prototype tới), mà đúng hơn là đối tượng prototype để *liên kết tới* khi các đối tượng khác được tạo ra bằng cách gọi hàm với từ khóa `new`.

Chúng ta thêm một thuộc tính `welcome` trên đối tượng rỗng đó (được gọi là `Classroom.prototype`), trỏ tới hàm `hello()`.

Sau đó, `new Classroom()` tạo ra một đối tượng mới (được gán cho `mathClass`), và liên kết prototype của nó với đối tượng `Classroom.prototype` hiện có.

Mặc dù `mathClass` không có thuộc tính/hàm `welcome()`, nó vẫn ủy nhiệm thành công sang hàm `Classroom.prototype.welcome()`.

Khuôn mẫu "prototypal class" này hiện nay bị phản đối mạnh mẽ (strongly discouraged), để ưu tiên sử dụng cơ chế `class` của ES6:

```js
class Classroom {
    constructor() {
        // ..
    }

    welcome() {
        console.log("Welcome, students!");
    }
}

var mathClass = new Classroom();

mathClass.welcome();
// Welcome, students!
```

Bên dưới bề mặt, cùng một liên kết prototype được thiết lập, nhưng cú pháp `class` này phù hợp với mẫu thiết kế hướng lớp (class-oriented design pattern) một cách gọn gàng, rõ ràng hơn nhiều so với "prototypal classes".
