# You Don't Know JS Yet: Scope & Closures - 2nd Edition
# Chương 4: Xoay quanh Phạm vi Toàn cục (Global Scope)

Chương 3 đã nhiều lần đề cập đến "phạm vi toàn cục" (global scope), nhưng bạn có thể vẫn đang tự hỏi tại sao phạm vi ngoài cùng nhất của một chương trình lại quan trọng đến thế trong JS hiện đại. Phần lớn công việc ngày nay được thực hiện bên trong các hàm (functions) và mô-đun (modules) thay vì ở phạm vi toàn cục.

Liệu chỉ cần khẳng định cụt lủn: "Hãy tránh dùng phạm vi toàn cục," thế là xong chuyện?

Phạm vi toàn cục của một chương trình JS là một chủ đề phong phú, mang lại nhiều tiện ích và sắc thái hơn bạn tưởng. Chương này trước tiên sẽ khám phá lý do tại sao phạm vi toàn cục (vẫn) hữu ích và gắn liền mật thiết với việc viết các chương trình JS ngày nay, sau đó xem xét sự khác biệt về vị trí và *cách truy cập* phạm vi toàn cục trong các môi trường JS khác nhau.

Hiểu thấu đáo về phạm vi toàn cục là điều cốt yếu để bạn làm chủ việc sử dụng lexical scope nhằm cấu trúc nên các chương trình của mình.

## Tại sao lại cần Phạm vi Toàn cục?

Có lẽ độc giả sẽ chẳng ngạc nhiên khi biết rằng hầu hết các ứng dụng đều được cấu thành từ nhiều (đôi khi là rất nhiều!) file JS riêng lẻ. Vậy chính xác thì tất cả các file rời rạc đó được kết nối lại với nhau trong một ngữ cảnh thực thi (runtime context) duy nhất của JS engine như thế nào?

Xét trên các ứng dụng chạy trên trình duyệt, có ba cách chính.

Thứ nhất, nếu bạn trực tiếp sử dụng ES modules (không biên dịch/transpile chúng sang một định dạng đóng gói mô-đun khác), các file này sẽ được môi trường JS tải độc lập từng file một. Sau đó, mỗi mô-đun sẽ `import` các tham chiếu tới bất kỳ mô-đun nào khác mà nó cần truy cập. Các file mô-đun riêng biệt này tương tác với nhau hoàn toàn thông qua những câu lệnh import dùng chung này, mà không cần bất kỳ phạm vi bao ngoài (outer scope) dùng chung nào.

Thứ hai, nếu bạn sử dụng một công cụ đóng gói (bundler) trong quy trình bản dựng (build process), tất cả các file thường được ghép (nối chuỗi) lại với nhau trước khi chuyển tới trình duyệt và JS engine, nơi sau đó chỉ xử lý một file lớn duy nhất. Ngay cả khi tất cả các phần của ứng dụng cùng nằm trong một file đơn lẻ, vẫn cần một cơ chế nào đó để mỗi phần đăng ký một *tên gọi* (name) cho các phần khác tham chiếu tới, cũng như một phương tiện để việc truy cập đó có thể diễn ra.

Trong một số cấu hình đóng gói (build setups), toàn bộ nội dung của file được bọc trong một phạm vi bao ngoài duy nhất, chẳng hạn như một hàm bao bọc (wrapper function), universal module (UMD—xem Phụ lục A), v.v. Mỗi phần có thể tự đăng ký để các phần khác truy cập thông qua các biến cục bộ trong phạm vi dùng chung đó. Ví dụ:

```js
(function wrappingOuterScope(){
    var moduleOne = (function one(){
        // ..
    })();

    var moduleTwo = (function two(){
        // ..

        function callModuleOne() {
            moduleOne.someMethod();
        }

        // ..
    })();
})();
```

Như đã thấy, các biến cục bộ `moduleOne` và `moduleTwo` bên trong phạm vi hàm `wrappingOuterScope()` được khai báo để các mô-đun này có thể truy cập lẫn nhau nhằm phối hợp hoạt động.

Mặc dù phạm vi của `wrappingOuterScope()` là một hàm chứ không phải toàn bộ phạm vi toàn cục của môi trường, nó đóng vai trò như một dạng "phạm vi toàn ứng dụng" (application-wide scope), một cái thùng chứa tất cả các định danh cấp cao nhất (top-level identifiers), dù không phải trong phạm vi toàn cục thực sự. Về mặt đó, nó giống như một kẻ đóng thế cho phạm vi toàn cục.

Và cuối cùng, cách thứ ba: cho dù có sử dụng công cụ đóng gói cho ứng dụng hay không, hoặc các file (không phải ES module) chỉ đơn giản được tải riêng rẽ trong trình duyệt (thông qua các thẻ `<script>` hoặc các cơ chế tải tài nguyên JS động khác), nếu không có một phạm vi bao quanh duy nhất bao trùm tất cả các phần này, thì **phạm vi toàn cục** (global scope) là cách duy nhất để chúng phối hợp với nhau:

Một file được đóng gói theo kiểu này thường trông giống như sau:

```js
var moduleOne = (function one(){
    // ..
})();
var moduleTwo = (function two(){
    // ..

    function callModuleOne() {
        moduleOne.someMethod();
    }

    // ..
})();
```

Ở đây, vì không có phạm vi hàm nào bao quanh, các khai báo `moduleOne` và `moduleTwo` này đơn giản được thả vào phạm vi toàn cục. Điều này thực chất cũng tương tự như việc các file không được ghép nối mà được tải riêng rẽ:

module1.js:

```js
var moduleOne = (function one(){
    // ..
})();
```

module2.js:

```js
var moduleTwo = (function two(){
    // ..

    function callModuleOne() {
        moduleOne.someMethod();
    }

    // ..
})();
```

Nếu các file này được tải riêng biệt dưới dạng các file .js độc lập thông thường trong môi trường trình duyệt, mỗi khai báo biến ở cấp cao nhất (top-level) sẽ trở thành một biến toàn cục, bởi vì phạm vi toàn cục là tài nguyên dùng chung duy nhất giữa hai file riêng biệt này—chúng là các chương trình độc lập, theo góc nhìn của JS engine.

Ngoài việc (có khả năng) giải thích vị trí mã nguồn ứng dụng tồn tại trong thời gian chạy (runtime) và cách mỗi phần có thể truy cập các phần khác để phối hợp, phạm vi toàn cục còn là nơi:

* JS cung cấp sẵn các thành phần tích hợp (built-ins):

    - các kiểu nguyên thủy: `undefined`, `null`, `Infinity`, `NaN`
    - các đối tượng gốc (natives): `Date()`, `Object()`, `String()`, v.v.
    - các hàm toàn cục: `eval()`, `parseInt()`, v.v.
    - các namespace: `Math`, `Atomics`, `JSON`
    - các bạn bè hữu hảo của JS: `Intl`, `WebAssembly`

* Môi trường lưu trữ (host) JS engine cung cấp các thành phần tích hợp của riêng nó:

    - `console` (và các phương thức của nó)
    - DOM (`window`, `document`, v.v.)
    - bộ hẹn giờ (`setTimeout(..)`, v.v.)
    - các web platform API: `navigator`, `history`, geolocation, WebRTC, v.v.

Đây chỉ là một vài trong số rất nhiều *biến toàn cục* (globals) mà chương trình của bạn sẽ tương tác.

| GHI CHÚ: |
| :--- |
| Node cũng phơi bày (expose) một số phần tử "toàn cục", nhưng về mặt kỹ thuật chúng không nằm trong phạm vi `global`: `require()`, `__dirname`, `module`, `URL`, v.v. |

Hầu hết các lập trình viên đều đồng ý rằng phạm vi toàn cục không nên chỉ là bãi rác cho mọi biến trong ứng dụng của bạn. Đó là một đống lộn xộn các lỗi tiềm tàng chỉ chực chờ phát sinh. Nhưng cũng không thể phủ nhận rằng phạm vi toàn cục là một chất *keo dán* quan trọng cho hầu như mọi ứng dụng JS.

## Phạm vi Toàn cục Này Chính xác Nằm ở Đâu?

Có vẻ như hiển nhiên rằng phạm vi toàn cục nằm ở phần ngoài cùng nhất của một file; nghĩa là, không nằm trong bất kỳ hàm hay khối (block) nào khác. Nhưng mọi chuyện không hoàn toàn đơn giản như vậy.

Các môi trường JS khác nhau xử lý phạm vi của chương trình, đặc biệt là phạm vi toàn cục, theo những cách khác nhau. Khá phổ biến khi các lập trình viên JS ấp ủ những quan niệm sai lầm mà bản thân họ thậm chí không hề nhận ra.

### "Window" của Trình duyệt

Xét về cách đối đãi với phạm vi toàn cục, môi trường *thuần khiết* nhất mà JS có thể chạy là dưới dạng một file .js độc lập được tải trong môi trường trang web trên trình duyệt. Ý tôi không phải "thuần khiết" theo nghĩa là không có gì được tự động thêm vào—rất nhiều thứ có thể được thêm vào!—mà là xét theo góc độ can thiệp tối thiểu vào mã nguồn hoặc sự can thiệp ít nhất đến hành vi phạm vi toàn cục như mong đợi của nó.

Hãy xem xét file .js này:

```js
var studentName = "Kyle";

function hello() {
    console.log(`Hello, ${ studentName }!`);
}

hello();
// Hello, Kyle!
```

Đoạn mã này có thể được tải trong môi trường trang web bằng thẻ `<script>` nội tuyến (inline), thẻ `<script src=..>` trong mã HTML, hoặc thậm chí là một phần tử DOM `<script>` được tạo động. Trong cả ba trường hợp, các định danh `studentName` và `hello` đều được khai báo trong phạm vi toàn cục.

Điều đó có nghĩa là nếu bạn truy cập đối tượng toàn cục (thường là `window` trong trình duyệt), bạn sẽ tìm thấy các thuộc tính cùng tên ở đó:

```js
var studentName = "Kyle";

function hello() {
    console.log(`Hello, ${ window.studentName }!`);
}

window.hello();
// Hello, Kyle!
```

Đó là hành vi mặc định mà người ta mong đợi khi đọc đặc tả kỹ thuật của JS: phạm vi ngoài cùng *chính là* phạm vi toàn cục và `studentName` được tạo ra một cách hợp lệ như một biến toàn cục.

Đó là điều tôi muốn nói về sự *thuần khiết*. Nhưng thật không may, điều đó không phải lúc nào cũng đúng với tất cả các môi trường JS bạn gặp phải, và điều này thường gây ngạc nhiên cho các lập trình viên JS.

#### Toàn cục Che bóng Toàn cục (Globals Shadowing Globals)

Hãy nhớ lại cuộc thảo luận về shadowing (và bỏ che bóng toàn cục - global unshadowing) từ Chương 3, nơi một khai báo biến có thể ghi đè và ngăn chặn quyền truy cập vào một khai báo cùng tên từ phạm vi bên ngoài.

Một hệ quả bất thường của sự khác biệt giữa biến toàn cục và thuộc tính toàn cục cùng tên là, chỉ trong phạm vi toàn cục, một thuộc tính của đối tượng toàn cục có thể bị che bóng bởi một biến toàn cục:

```js
window.something = 42;

let something = "Kyle";

console.log(something);
// Kyle

console.log(window.something);
// 42
```

Khai báo `let` thêm một biến toàn cục `something` nhưng không tạo ra thuộc tính trên đối tượng toàn cục (xem Chương 3). Hệ quả là định danh lexical `something` che bóng thuộc tính `something` của đối tượng toàn cục.

Việc tạo ra sự phân kỳ (divergence) giữa đối tượng toàn cục và phạm vi toàn cục hầu như chắc chắn là một ý tưởng tồi. Những người đọc mã của bạn gần như chắc chắn sẽ bị vấp váp và bối rối.

Một cách đơn giản để tránh cạm bẫy này với các khai báo toàn cục: hãy luôn sử dụng `var` cho các biến toàn cục. Dành riêng `let` và `const` cho phạm vi khối (xem "Tạo phạm vi với các khối lệnh" trong Chương 6).

#### Các Biến Toàn cục của DOM (DOM Globals)

Tôi đã khẳng định rằng môi trường JS chạy trên trình duyệt có hành vi phạm vi toàn cục *thuần khiết* nhất mà chúng ta từng thấy. Tuy nhiên, nó không hoàn toàn *thuần khiết*.

Một hành vi đáng ngạc nhiên trong phạm vi toàn cục mà bạn có thể bắt gặp với các ứng dụng JS trên trình duyệt: một phần tử DOM có thuộc tính `id` sẽ tự động tạo ra một biến toàn cục tham chiếu đến phần tử đó.

Hãy xem xét đoạn mã đánh dấu này:

```text
<ul id="my-todo-list">
   <li id="first">Write a book</li>
   ..
</ul>
```

Và mã JS cho trang đó có thể bao gồm:

```js
first;
// <li id="first">..</li>

window["my-todo-list"];
// <ul id="my-todo-list">..</ul>
```

Nếu giá trị `id` là một tên định danh lexical hợp lệ (như `first`), biến lexical sẽ được tạo ra. Nếu không, cách duy nhất để truy cập biến toàn cục đó là thông qua đối tượng toàn cục (`window[..]`).

Việc tự động đăng ký tất cả các phần tử DOM mang thuộc tính `id` thành các biến toàn cục là một hành vi cũ kế thừa từ các trình duyệt thời kỳ đầu, tuy nhiên hành vi này vẫn phải được giữ lại vì có quá nhiều trang web cũ vẫn dựa vào nó. Lời khuyên của tôi là đừng bao giờ sử dụng các biến toàn cục này, mặc dù chúng sẽ luôn được âm thầm tạo ra.

#### Có Gì Trong Một Cái Tên (Window)?

Một điều kỳ quặc khác về phạm vi toàn cục trong JS chạy trên trình duyệt:

```js
var name = 42;

console.log(name, typeof name);
// "42" string
```

`window.name` là một "biến toàn cục" được xác định trước trong ngữ cảnh trình duyệt; nó là một thuộc tính trên đối tượng toàn cục, vì vậy nó trông giống như một biến toàn cục thông thường (dẫu cho nó chẳng hề "bình thường" chút nào).

Chúng ta đã sử dụng `var` cho khai báo của mình, điều này **không hề** che bóng thuộc tính toàn cục `name` đã được định nghĩa trước. Điều đó có nghĩa là, trên thực tế, khai báo `var` bị bỏ qua, vì đã có sẵn một thuộc tính đối tượng phạm vi toàn cục mang tên đó. Như chúng ta đã thảo luận trước đó, nếu sử dụng `let name`, chúng ta đã che bóng `window.name` bằng một biến `name` toàn cục riêng biệt.

Nhưng hành vi thực sự đáng ngạc nhiên là mặc dù chúng ta gán số `42` cho `name` (và do đó là gán cho `window.name`), khi truy xuất giá trị của nó, nó lại trả về một chuỗi `"42"`! Trong trường hợp này, sự kỳ dị là do `name` thực chất là một getter/setter đã được định nghĩa sẵn trên đối tượng `window`, vốn khăng khăng bắt giá trị của nó phải là một giá trị chuỗi. Thật đáng sợ!

Ngoại trừ một số trường hợp ngoại lệ hiếm hoi như ID của phần tử DOM và `window.name`, JS chạy dưới dạng một file độc lập trong trang trình duyệt có một số hành vi phạm vi toàn cục *thuần khiết* nhất mà chúng ta sẽ bắt gặp.

### Web Workers

Web Workers là một phần mở rộng của nền tảng web dựa trên hành vi của JS trên trình duyệt, cho phép một file JS chạy trong một luồng (thread) hoàn toàn riêng biệt (về mặt hệ điều hành) với luồng đang chạy chương trình JS chính.

Vì các chương trình Web Worker này chạy trên một luồng riêng biệt, chúng bị hạn chế trong việc giao tiếp với luồng ứng dụng chính, nhằm tránh/hạn chế tình trạng tranh chấp (race conditions) và các biến chứng phức tạp khác. Chẳng hạn, mã nguồn Web Worker không có quyền truy cập vào DOM. Tuy nhiên, một số Web API vẫn được cung cấp cho worker, chẳng hạn như `navigator`.

Vì Web Worker được đối xử như một chương trình hoàn toàn riêng biệt, nó không chia sẻ phạm vi toàn cục với chương trình JS chính. Tuy nhiên, JS engine của trình duyệt vẫn đang chạy mã, vì vậy chúng ta có thể mong đợi sự *thuần khiết* tương tự trong hành vi phạm vi toàn cục của nó. Do không có quyền truy cập DOM, bí danh `window` cho phạm vi toàn cục sẽ không tồn tại.

Trong Web Worker, tham chiếu đối tượng toàn cục thường được thực hiện thông qua `self`:

```js
var studentName = "Kyle";
let studentID = 42;

function hello() {
    console.log(`Hello, ${ self.studentName }!`);
}

self.hello();
// Hello, Kyle!

self.studentID;
// undefined
```

Cũng giống như với các chương trình JS chính, các khai báo `var` và `function` tạo ra các thuộc tính đối ứng trên đối tượng toàn cục (hay còn gọi là `self`), trong khi các khai báo khác (`let`, v.v.) thì không.

Vì vậy, một lần nữa, hành vi phạm vi toàn cục mà chúng ta thấy ở đây đạt độ *thuần khiết* gần như tuyệt đối khi chạy các chương trình JS; thậm chí có lẽ nó còn *thuần khiết* hơn vì không có DOM chen vào làm xáo trộn mọi thứ!

### Developer Tools Console/REPL

Hãy nhớ lại từ Chương 1 trong cuốn *Bắt đầu (Get Started)* rằng Developer Tools (Công cụ dành cho lập trình viên) không tạo ra một môi trường JS tuân thủ hoàn toàn theo tiêu chuẩn. Chúng có xử lý mã JS, nhưng chúng cũng thiên về việc tối ưu hóa tương tác trải nghiệm người dùng sao cho thân thiện nhất với các lập trình viên (trải nghiệm nhà phát triển - developer experience, hay DX).

Trong một số trường hợp, việc ưu tiên DX khi gõ vào các đoạn mã JS ngắn, thay vì tuân theo các bước nghiêm ngặt thông thường để xử lý một chương trình JS hoàn chỉnh, sẽ tạo ra những khác biệt có thể quan sát được về hành vi của mã giữa chương trình thực tế và công cụ. Ví dụ, một số điều kiện lỗi áp dụng cho một chương trình JS có thể được nới lỏng và không hiển thị khi mã được nhập vào developer tool.

Xét theo các cuộc thảo luận của chúng ta ở đây về phạm vi, những khác biệt quan sát được trong hành vi có thể bao gồm:

* Hành vi của phạm vi toàn cục

* Hoisting (xem Chương 5)

* Các từ khóa khai báo phạm vi khối (`let` / `const`, xem Chương 6) khi được dùng ở phạm vi ngoài cùng nhất

Mặc dù có vẻ như khi sử dụng console/REPL, các câu lệnh được nhập ở phạm vi ngoài cùng nhất đang được xử lý trong phạm vi toàn cục thực sự, nhưng điều đó không hoàn toàn chính xác. Những công cụ như vậy thường mô phỏng vị trí phạm vi toàn cục ở một mức độ nào đó; đó là sự mô phỏng, chứ không phải sự tuân thủ nghiêm ngặt. Những môi trường công cụ này ưu tiên sự tiện lợi cho lập trình viên, điều đó có nghĩa là đôi khi (chẳng hạn như với các cuộc thảo luận hiện tại của chúng ta về phạm vi), hành vi quan sát được có thể sai lệch so với đặc tả kỹ thuật của JS.

Điều cốt lõi rút ra là Developer Tools, mặc dù được tối ưu hóa để thuận tiện và hữu ích cho nhiều hoạt động phát triển khác nhau, **không** phải là môi trường phù hợp để xác định hoặc kiểm chứng các hành vi rõ ràng và tinh tế trong ngữ cảnh một chương trình JS thực tế.

### ES Modules (ESM)

ES6 đã giới thiệu hỗ trợ hạng nhất (first-class support) cho mẫu thiết kế mô-đun (được đề cập trong Chương 8). Một trong những tác động rõ ràng nhất của việc sử dụng ESM là cách nó thay đổi hành vi của phạm vi cấp cao nhất có thể quan sát được trong một file.

Hãy nhớ lại đoạn mã này ở phần trước (chúng ta sẽ điều chỉnh nó sang định dạng ESM bằng cách sử dụng từ khóa `export`):

```js
var studentName = "Kyle";

function hello() {
    console.log(`Hello, ${ studentName }!`);
}

hello();
// Hello, Kyle!

export hello;
```

Nếu đoạn mã đó nằm trong một file được tải dưới dạng ES module, nó vẫn sẽ chạy hoàn toàn giống như vậy. Tuy nhiên, các tác động có thể quan sát được, xét từ góc độ tổng thể của ứng dụng, sẽ khác.

Mặc dù được khai báo ở cấp cao nhất của file (mô-đun), trong phạm vi ngoài cùng rõ ràng nhất, `studentName` và `hello` không phải là các biến toàn cục. Thay vào đó, chúng mang phạm vi toàn mô-đun (module-wide), hay nếu bạn muốn, là "toàn cục của mô-đun" ("module-global").

Tuy nhiên, trong một mô-đun không có "đối tượng phạm vi toàn mô-đun" ngầm định nào để các khai báo cấp cao nhất này được thêm vào dưới dạng thuộc tính, giống như khi các khai báo xuất hiện ở cấp cao nhất của các file JS không phải mô-đun. Điều này không có nghĩa là các biến toàn cục không thể tồn tại hoặc không thể được truy cập trong các chương trình như vậy. Chỉ là các biến toàn cục không được *tạo ra* bằng cách khai báo biến ở phạm vi cấp cao nhất của một mô-đun.

Phạm vi cấp cao nhất của mô-đun là hậu duệ của phạm vi toàn cục, gần như thể toàn bộ nội dung của mô-đun được bọc trong một hàm. Do đó, tất cả các biến tồn tại trong phạm vi toàn cục (cho dù chúng có nằm trên đối tượng toàn cục hay không!) đều khả dụng dưới dạng các định danh lexical từ bên trong phạm vi của mô-đun.

ESM khuyến khích giảm thiểu sự phụ thuộc vào phạm vi toàn cục, nơi bạn import bất kỳ mô-đun nào bạn cần để mô-đun hiện tại hoạt động. Do đó, bạn sẽ ít thấy việc sử dụng phạm vi toàn cục hoặc đối tượng toàn cục của nó hơn.

Tuy nhiên, như đã lưu ý trước đó, vẫn có rất nhiều biến toàn cục của JS và web mà bạn sẽ tiếp tục truy cập từ phạm vi toàn cục, cho dù bạn có nhận ra điều đó hay không!

### Node

Một khía cạnh của Node thường khiến các lập trình viên JS bất ngờ là Node coi mọi file .js đơn lẻ mà nó tải, bao gồm cả file chính mà bạn dùng để khởi động tiến trình Node, là một *mô-đun* (ES module hoặc CommonJS module, xem Chương 8). Tác động thực tế là cấp cao nhất trong các chương trình Node của bạn **không bao giờ thực sự là phạm vi toàn cục**, theo cách mà nó diễn ra khi tải một file không phải mô-đun trong trình duyệt.

Tính đến thời điểm viết cuốn sách này, Node gần đây đã bổ sung hỗ trợ cho ES modules. Nhưng ngoài ra, ngay từ đầu Node đã hỗ trợ định dạng mô-đun được gọi là "CommonJS", trông giống như sau:

```js
var studentName = "Kyle";

function hello() {
    console.log(`Hello, ${ studentName }!`);
}

hello();
// Hello, Kyle!

module.exports.hello = hello;
```

Trước khi xử lý, Node bọc đoạn mã đó trong một hàm một cách hiệu quả, sao cho các khai báo `var` và `function` nằm trong phạm vi hàm bao bọc đó, **chứ không** được coi là các biến toàn cục.

Hãy hình dung đoạn mã phía trên được Node nhìn nhận như thế này (chỉ mang tính minh họa, không phải mã thực tế):

```js
function Module(module,require,__dirname,...) {
    var studentName = "Kyle";

    function hello() {
        console.log(`Hello, ${ studentName }!`);
    }

    hello();
    // Hello, Kyle!

    module.exports.hello = hello;
}
```

Sau đó, Node về cơ bản sẽ gọi hàm `Module(..)` được thêm vào đó để chạy mô-đun của bạn. Bạn có thể thấy rõ ở đây lý do tại sao các định danh `studentName` và `hello` không mang tính toàn cục, mà được khai báo trong phạm vi mô-đun.

Như đã lưu ý trước đó, Node định nghĩa một số "biến toàn cục" như `require()`, nhưng chúng thực ra không phải là các định danh trong phạm vi toàn cục (cũng không phải là thuộc tính của đối tượng toàn cục). Chúng được đưa (inject) vào phạm vi của mỗi mô-đun, về cơ bản hơi giống với các tham số được liệt kê trong khai báo hàm `Module(..)`.

Vậy làm thế nào để bạn định nghĩa các biến toàn cục thực sự trong Node? Cách duy nhất để làm điều đó là thêm thuộc tính vào một "biến toàn cục" khác được cung cấp tự động của Node, điều trớ trêu là nó lại có tên là `global`. `global` là một tham chiếu đến đối tượng phạm vi toàn cục thực sự, có phần giống như việc sử dụng `window` trong môi trường JS trên trình duyệt.

Hãy xem xét:

```js
global.studentName = "Kyle";

function hello() {
    console.log(`Hello, ${ studentName }!`);
}

hello();
// Hello, Kyle!

module.exports.hello = hello;
```

Ở đây chúng ta thêm `studentName` dưới dạng một thuộc tính trên đối tượng `global`, và sau đó trong câu lệnh `console.log(..)`, chúng ta có thể truy cập `studentName` như một biến toàn cục thông thường.

Hãy nhớ rằng, định danh `global` không phải do JS định nghĩa; nó được định nghĩa riêng bởi Node.

## Global This

Điểm lại các môi trường JS mà chúng ta đã xem xét cho đến nay, một chương trình có thể hoặc không thể:

* Khai báo một biến toàn cục ở phạm vi cấp cao nhất với các khai báo `var` hoặc `function`—hoặc `let`, `const`, và `class`.

* Đồng thời thêm các khai báo biến toàn cục làm thuộc tính của đối tượng phạm vi toàn cục nếu `var` hoặc `function` được sử dụng cho khai báo.

* Tham chiếu đến đối tượng phạm vi toàn cục (để thêm hoặc truy xuất các biến toàn cục, dưới dạng thuộc tính) bằng `window`, `self`, hoặc `global`.

Tôi nghĩ thật công bằng khi nói rằng việc truy cập và hành vi của phạm vi toàn cục phức tạp hơn hầu hết các lập trình viên vẫn tưởng, như các phần trước đã minh họa. Nhưng sự phức tạp chưa bao giờ rõ ràng hơn việc cố gắng tìm ra một tham chiếu áp dụng phổ quát cho đối tượng phạm vi toàn cục.

Tuy nhiên, một "mẹo" khác để có được tham chiếu đến đối tượng phạm vi toàn cục trông như sau:

```js
const theGlobalScopeObject =
    (new Function("return this"))();
```

| GHI CHÚ: |
| :--- |
| Một hàm có thể được xây dựng động từ mã được lưu trữ trong một giá trị chuỗi với hàm khởi tạo `Function()`, tương tự như `eval(..)` (xem "Gian lận: Sửa đổi Phạm vi trong Thời gian chạy" ở Chương 1). Một hàm như vậy sẽ tự động được chạy ở non-strict-mode (vì lý do tương thích ngược) khi được gọi bằng cú pháp gọi hàm `()` thông thường như được trình bày; `this` của nó sẽ trỏ tới đối tượng toàn cục. Xem cuốn sách thứ ba trong loạt sách này, *Objects & Classes*, để biết thêm thông tin về việc xác định các binding của `this`. |

Như vậy, chúng ta có `window`, `self`, `global`, và cái mẹo `new Function(..)` xấu xí này. Có quá nhiều cách khác nhau để cố gắng tiếp cận đối tượng toàn cục này. Mỗi cách đều có ưu và nhược điểm riêng.

Tại sao không giới thiệu thêm một cách nữa!?!?

Kể từ ES2020, JS cuối cùng đã định nghĩa một tham chiếu chuẩn hóa tới đối tượng phạm vi toàn cục, được gọi là `globalThis`. Vì vậy, tùy thuộc vào độ mới của các JS engine mà mã của bạn chạy trên đó, bạn có thể sử dụng `globalThis` thay cho bất kỳ cách tiếp cận nào khác kể trên.

Chúng ta thậm chí có thể thử định nghĩa một polyfill đa môi trường an toàn hơn trên các môi trường JS trước thời kỳ `globalThis`, chẳng hạn như:

```js
const theGlobalScopeObject =
    (typeof globalThis != "undefined") ? globalThis :
    (typeof global != "undefined") ? global :
    (typeof window != "undefined") ? window :
    (typeof self != "undefined") ? self :
    (new Function("return this"))();
```

Phù! Điều đó chắc chắn không phải là lý tưởng, nhưng nó sẽ hoạt động nếu bạn thấy mình cần một tham chiếu phạm vi toàn cục đáng tin cậy.

(Tên được đề xuất `globalThis` đã gây khá nhiều tranh cãi trong khi tính năng này được thêm vào JS. Cụ thể, tôi và nhiều người khác cảm thấy tham chiếu "this" trong tên gọi của nó gây hiểu lầm, vì lý do bạn tham chiếu đối tượng này là để truy cập vào phạm vi toàn cục, chứ không bao giờ là để truy cập vào một dạng binding `this` toàn cục/mặc định nào đó. Đã có nhiều cái tên khác được cân nhắc, nhưng vì nhiều lý do mà bị loại trừ. Thật không may, cái tên được chọn cuối cùng lại là giải pháp bất đắc dĩ cuối cùng. Nếu bạn định tương tác với đối tượng phạm vi toàn cục trong các chương trình của mình, để giảm bớt sự nhầm lẫn, tôi thực sự khuyên bạn nên chọn một cái tên tốt hơn, chẳng hạn như cái tên `theGlobalScopeObject` (dài đến buồn cười nhưng chính xác!) được sử dụng ở đây.)

## Thấu hiểu Toàn cục (Globally Aware)

Phạm vi toàn cục hiện diện và gắn liền với mọi chương trình JS, mặc dù các mẫu hiện đại để tổ chức mã thành các mô-đun đã giảm bớt phần lớn sự phụ thuộc vào việc lưu trữ các định danh trong không gian tên đó.

Tuy nhiên, khi mã nguồn của chúng ta ngày càng sinh sôi vượt ra ngoài ranh giới của trình duyệt, điều đặc biệt quan trọng là chúng ta phải nắm vững sự khác biệt trong cách thức hoạt động của phạm vi toàn cục (và đối tượng phạm vi toàn cục!) trên các môi trường JS khác nhau.

Với bức tranh toàn cảnh về phạm vi toàn cục giờ đây đã rõ nét hơn, chương tiếp theo sẽ lại đi sâu vào các chi tiết sâu hơn của lexical scope, xem xét cách thức và thời điểm các biến có thể được sử dụng.
