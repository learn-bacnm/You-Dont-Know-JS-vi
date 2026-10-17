# You Don't Know JS Yet: Scope & Closures - Tái bản lần 2
# Chương 8: Mẫu thiết kế Module (The Module Pattern)

Trong chương này, chúng ta sẽ khép lại phần nội dung chính của cuốn sách bằng việc khám phá một trong những mẫu tổ chức mã lệnh quan trọng nhất trong toàn bộ giới lập trình: module. Như chúng ta sẽ thấy, các module vốn dĩ được xây dựng từ những gì chúng ta đã tìm hiểu: phần thưởng xứng đáng cho những nỗ lực của bạn trong việc học về lexical scope và closure.

Chúng ta đã xem xét lexical scope ở mọi góc độ, từ phạm vi rộng lớn của global scope xuống qua các block scope lồng nhau, cho đến những chi tiết phức tạp trong vòng đời của biến. Sau đó, chúng ta đã tận dụng lexical scope để thấu hiểu toàn bộ sức mạnh của closure.

Hãy dành một chút thời gian để suy ngẫm xem bạn đã đi được bao xa trong cuộc hành trình này cho đến nay; bạn đã có những bước tiến lớn trong việc hiểu sâu hơn về JS!

Chủ đề trọng tâm của cuốn sách này là: việc thấu hiểu và làm chủ scope cùng closure là chìa khóa để định cấu trúc và tổ chức mã lệnh của chúng ta một cách đúng đắn, đặc biệt là các quyết định về việc lưu trữ thông tin ở đâu trong các biến.

Mục tiêu của chúng ta trong chương cuối này là nhận thức được cách mà các module hiện thực hóa tầm quan trọng của những chủ đề này, nâng chúng từ các khái niệm trừu tượng thành những cải tiến cụ thể, thiết thực trong việc xây dựng các chương trình.

## Đóng gói và Phơi bày tối thiểu (POLE)

Đóng gói (Encapsulation) thường được nhắc đến như một nguyên lý của lập trình hướng đối tượng (OO), nhưng nó mang tính nền tảng và có thể áp dụng rộng rãi hơn thế nhiều. Mục tiêu của đóng gói là gom nhóm hoặc đặt chung thông tin (dữ liệu) và hành vi (hàm) có cùng mục đích phục vụ lại với nhau.

Độc lập với bất kỳ cú pháp hay cơ chế mã lệnh nào, tinh thần đóng gói có thể được hiện thực hóa chỉ đơn giản bằng việc sử dụng các tệp riêng biệt để chứa các phần của chương trình tổng thể có chung mục đích. Nếu chúng ta gom toàn bộ những gì vận hành danh sách kết quả tìm kiếm vào một tệp duy nhất có tên "search-list.js", chúng ta đang đóng gói phần đó của chương trình.

Xu hướng gần đây trong lập trình front-end hiện đại là tổ chức các ứng dụng xung quanh kiến trúc Component càng thúc đẩy tính đóng gói đi xa hơn nữa. Đối với nhiều người, việc hợp nhất mọi thứ cấu thành nên danh sách kết quả tìm kiếm—thậm chí vượt ra ngoài mã lệnh, bao gồm cả markup hiển thị và định kiểu (styling)—vào một đơn vị logic chương trình duy nhất, một thứ hữu hình mà chúng ta có thể tương tác, mang lại cảm giác rất tự nhiên. Và sau đó, chúng ta gắn nhãn cho tập hợp đó là component "SearchList".

Một mục tiêu quan trọng khác là kiểm soát khả năng hiển thị (visibility) của một số khía cạnh nhất định trong dữ liệu và chức năng đã được đóng gói. Hãy nhớ lại từ Chương 6 về nguyên lý *phơi bày tối thiểu* (POLE - Principle of Least Exposure), nhằm mục đích phòng vệ trước các *nguy cơ* khác nhau của việc phơi bày scope quá mức; những nguy cơ này ảnh hưởng đến cả biến lẫn hàm. Trong JS, chúng ta thường triển khai việc kiểm soát khả năng hiển thị thông qua các cơ chế của lexical scope.

Ý tưởng ở đây là gom nhóm các phần chương trình tương tự lại với nhau và giới hạn quyền truy cập bằng lập trình một cách có chọn lọc đối với những phần mà chúng ta coi là chi tiết *riêng tư* (*private*). Những gì không được coi là *private* sau đó sẽ được đánh dấu là *công khai* (*public*), có thể truy cập được từ toàn bộ chương trình.

Hiệu quả tự nhiên của nỗ lực này là tổ chức mã lệnh tốt hơn. Sẽ dễ dàng hơn trong việc xây dựng và bảo trì phần mềm khi chúng ta biết mọi thứ nằm ở đâu, với ranh giới và điểm kết nối rõ ràng, minh bạch. Cũng sẽ dễ dàng hơn để duy trì chất lượng nếu chúng ta tránh được các cạm bẫy của việc phơi bày dữ liệu và chức năng quá mức.

Đây là một số lợi ích chính của việc tổ chức các chương trình JS thành các module.

## Module là gì?

Module là một tập hợp các dữ liệu và hàm liên quan (thường được gọi là các phương thức trong ngữ cảnh này), được đặc trưng bởi sự phân chia giữa các chi tiết ẩn *private* và các chi tiết có thể truy cập *public*, thường được gọi là "public API."

Một module cũng có trạng thái (stateful): nó duy trì một số thông tin theo thời gian, cùng với chức năng để truy cập và cập nhật thông tin đó.

| GHI CHÚ: |
| :--- |
| Một mối quan tâm rộng lớn hơn của mẫu module là đón nhận trọn vẹn việc module hóa ở cấp độ hệ thống thông qua liên kết lỏng (loose-coupling) và các kỹ thuật kiến trúc chương trình khác. Đó là một chủ đề phức tạp vượt xa phạm vi thảo luận của chúng ta, nhưng rất đáng để nghiên cứu thêm ngoài cuốn sách này. |

Để hiểu rõ hơn module là gì, hãy so sánh một số đặc điểm của module với các mẫu mã lệnh hữu ích nhưng chưa hẳn là module.

### Không gian tên (Gom nhóm không trạng thái - Stateless Grouping)

Nếu bạn gom một nhóm các hàm liên quan lại với nhau mà không có dữ liệu, thì bạn thực sự chưa có được tính đóng gói như mong đợi mà một module ám chỉ. Thuật ngữ thích hợp hơn cho việc gom nhóm các hàm *không trạng thái* (*stateless*) này là một không gian tên (namespace):

```js
// namespace, not module
var Utils = {
    cancelEvt(evt) {
        evt.preventDefault();
        evt.stopPropagation();
        evt.stopImmediatePropagation();
    },
    wait(ms) {
        return new Promise(function c(res){
            setTimeout(res,ms);
        });
    },
    isValidEmail(email) {
        return /[^@]+@[^@.]+\.[^@.]+/.test(email);
    }
};
```

`Utils` ở đây là một tập hợp các tiện ích hữu dụng, tuy nhiên tất cả chúng đều là các hàm độc lập với trạng thái (state-independent). Việc tập hợp chức năng lại với nhau nói chung là một thói quen tốt, nhưng điều đó không biến nó thành một module. Đúng hơn là chúng ta đã định nghĩa một namespace `Utils` và tổ chức các hàm dưới namespace đó.

### Cấu trúc dữ liệu (Gom nhóm có trạng thái - Stateful Grouping)

Ngay cả khi bạn gói dữ liệu và các hàm có trạng thái lại với nhau, nếu bạn không giới hạn khả năng hiển thị của bất kỳ thành phần nào trong số đó, thì bạn vẫn chưa đạt đến khía cạnh POLE của việc đóng gói; sẽ không thực sự hữu ích khi gắn nhãn đó là một module.

Hãy xem xét:

```js
// data structure, not module
var Student = {
    records: [
        { id: 14, name: "Kyle", grade: 86 },
        { id: 73, name: "Suzy", grade: 87 },
        { id: 112, name: "Frank", grade: 75 },
        { id: 6, name: "Sarah", grade: 91 }
    ],
    getName(studentID) {
        var student = this.records.find(
            student => student.id == studentID
        );
        return student.name;
    }
};

Student.getName(73);
// Suzy
```

Vì `records` là dữ liệu có thể truy cập công khai, không được ẩn sau một public API, nên `Student` ở đây không thực sự là một module.

`Student` quả thực có khía cạnh dữ liệu-và-chức-năng của việc đóng gói, nhưng lại không có khía cạnh kiểm soát khả năng hiển thị. Tốt nhất nên gắn nhãn đây là một thể hiện (instance) của một cấu trúc dữ liệu.

### Module (Kiểm soát truy cập có trạng thái - Stateful Access Control)

Để thể hiện đầy đủ tinh thần của mẫu module, chúng ta không chỉ cần sự gom nhóm và trạng thái, mà còn cần cả việc kiểm soát truy cập thông qua khả năng hiển thị (private so với public).

Hãy biến `Student` từ phần trước thành một module. Chúng ta sẽ bắt đầu với một dạng mà tôi gọi là "classic module" (module cổ điển), ban đầu được gọi là "revealing module" khi nó mới xuất hiện vào đầu những năm 2000. Hãy xem xét:

```js
var Student = (function defineStudent(){
    var records = [
        { id: 14, name: "Kyle", grade: 86 },
        { id: 73, name: "Suzy", grade: 87 },
        { id: 112, name: "Frank", grade: 75 },
        { id: 6, name: "Sarah", grade: 91 }
    ];

    var publicAPI = {
        getName
    };

    return publicAPI;

    // ************************

    function getName(studentID) {
        var student = records.find(
            student => student.id == studentID
        );
        return student.name;
    }
})();

Student.getName(73);   // Suzy
```

`Student` hiện là một instance của một module. Nó sở hữu một public API với một phương thức duy nhất: `getName(..)`. Phương thức này có thể truy cập dữ liệu `records` ẩn riêng tư.

| CẢNH BÁO: |
| :--- |
| Tôi cần chỉ ra rằng dữ liệu học sinh rõ ràng được hard-code vào định nghĩa module này chỉ nhằm mục đích minh họa. Một module điển hình trong chương trình của bạn sẽ nhận dữ liệu này từ một nguồn bên ngoài, thông thường được tải từ cơ sở dữ liệu, các tệp dữ liệu JSON, các lệnh gọi Ajax, v.v. Dữ liệu sau đó được đưa vào instance của module thường là thông qua (các) phương thức trên public API của module. |

Định dạng module cổ điển (classic module format) hoạt động như thế nào?

Hãy để ý rằng instance của module được tạo ra bằng cách thực thi IIFE `defineStudent()`. IIFE này trả về một object (được đặt tên là `publicAPI`) có một thuộc tính tham chiếu đến hàm bên trong `getName(..)`.

Việc đặt tên object là `publicAPI` là sở thích về mặt phong cách của riêng tôi. Object này có thể được đặt tên bất kỳ theo ý bạn (JS không quan tâm), hoặc bạn có thể chỉ cần trả về một object trực tiếp mà không cần gán nó cho bất kỳ biến có tên nội bộ nào. Sẽ có thêm chi tiết về lựa chọn này trong Phụ lục A.

Từ bên ngoài, `Student.getName(..)` gọi hàm bên trong đã được phơi bày này, hàm này duy trì quyền truy cập vào biến bên trong `records` thông qua closure.

Bạn không *nhất thiết* phải trả về một object có chứa hàm như một trong các thuộc tính của nó. Bạn có thể chỉ cần trả về trực tiếp một hàm, thay cho object đó. Điều đó vẫn thỏa mãn tất cả các thành phần cốt lõi của một classic module.

Nhờ vào cách thức hoạt động của lexical scope, việc định nghĩa các biến và hàm bên trong hàm định nghĩa module bên ngoài của bạn sẽ khiến mọi thứ *mặc định là* private. Chỉ các thuộc tính được thêm vào object public API được trả về từ hàm mới được xuất ra (export) để sử dụng công khai bên ngoài.

Việc sử dụng IIFE ngụ ý rằng chương trình của chúng ta chỉ cần một instance trung tâm duy nhất của module, thường được gọi là một "singleton." Thật vậy, ví dụ cụ thể này đủ đơn giản để không có lý do rõ ràng nào khiến chúng ta cần nhiều hơn một instance của module `Student`.

#### Module Factory (Nhiều Instance)

Nhưng nếu chúng ta muốn định nghĩa một module hỗ trợ nhiều instance trong chương trình của mình, chúng ta có thể điều chỉnh mã lệnh một chút:

```js
// factory function, not singleton IIFE
function defineStudent() {
    var records = [
        { id: 14, name: "Kyle", grade: 86 },
        { id: 73, name: "Suzy", grade: 87 },
        { id: 112, name: "Frank", grade: 75 },
        { id: 6, name: "Sarah", grade: 91 }
    ];

    var publicAPI = {
        getName
    };

    return publicAPI;

    // ************************

    function getName(studentID) {
        var student = records.find(
            student => student.id == studentID
        );
        return student.name;
    }
}

var fullTime = defineStudent();
fullTime.getName(73);            // Suzy
```

Thay vì chỉ định `defineStudent()` dưới dạng một IIFE, chúng ta chỉ cần định nghĩa nó như một hàm độc lập bình thường, vốn thường được gọi trong ngữ cảnh này là một hàm "module factory" (nhà máy sản xuất module).

Sau đó, chúng ta gọi module factory, tạo ra một instance của module mà chúng ta gắn nhãn là `fullTime`. Module instance này ám chỉ một instance mới của scope bên trong, và do đó tạo ra một closure mới mà `getName(..)` nắm giữ đối với `records`. Giờ đây `fullTime.getName(..)` sẽ gọi phương thức trên instance cụ thể đó.

#### Định nghĩa Classic Module

Vì vậy, để làm rõ điều gì làm nên một classic module:

* Phải có một scope bên ngoài, thường là từ một hàm module factory chạy ít nhất một lần.

* Scope bên trong của module phải có ít nhất một mẩu thông tin ẩn đại diện cho trạng thái của module.

* Module phải trả về trên public API của nó một tham chiếu đến ít nhất một hàm có closure trên trạng thái ẩn của module (để trạng thái này thực sự được bảo toàn).

Bạn có thể sẽ bắt gặp các biến thể khác của phương pháp classic module này, chúng ta sẽ xem xét chi tiết hơn trong Phụ lục A.

## Node CommonJS Modules

Trong Chương 4, chúng ta đã giới thiệu định dạng module CommonJS được Node sử dụng. Khác với định dạng classic module đã mô tả trước đó, nơi bạn có thể gom module factory hoặc IIFE cùng với bất kỳ mã lệnh nào khác bao gồm cả các module khác, các module CommonJS dựa trên tệp (file-based); mỗi tệp là một module.

Hãy tinh chỉnh ví dụ module của chúng ta để tuân theo định dạng đó:

```js
module.exports.getName = getName;

// ************************

var records = [
    { id: 14, name: "Kyle", grade: 86 },
    { id: 73, name: "Suzy", grade: 87 },
    { id: 112, name: "Frank", grade: 75 },
    { id: 6, name: "Sarah", grade: 91 }
];

function getName(studentID) {
    var student = records.find(
        student => student.id == studentID
    );
    return student.name;
}
```

Các định danh `records` và `getName` nằm trong scope cấp cao nhất (top-level scope) của module này, nhưng đó không phải là global scope (như đã giải thích trong Chương 4). Do đó, mọi thứ ở đây *theo mặc định* đều là private đối với module.

Để phơi bày thứ gì đó trên public API của một module CommonJS, bạn thêm một thuộc tính vào object rỗng được cung cấp dưới dạng `module.exports`. Trong một số mã nguồn cũ hơn, bạn có thể bắt gặp các tham chiếu chỉ đến `exports` đơn thuần, nhưng để mã lệnh rõ ràng, bạn phải luôn chỉ định đầy đủ tham chiếu đó với tiền tố `module.`.

Vì mục đích phong cách, tôi thích đặt các "exports" của mình ở trên cùng và phần triển khai module ở dưới cùng. Nhưng những export này có thể được đặt ở bất cứ đâu. Tôi đặc biệt khuyên bạn nên tập hợp tất cả chúng lại với nhau, ở đầu hoặc ở cuối tệp của bạn.

Một số lập trình viên có thói quen thay thế object exports mặc định, như thế này:

```js
// defining a new object for the API
module.exports = {
    // ..exports..
};
```

Có một số điểm kỳ lạ với cách tiếp cận này, bao gồm hành vi không mong muốn nếu nhiều module như vậy phụ thuộc vòng tròn (circularly depend) lẫn nhau. Do đó, tôi khuyên bạn không nên thay thế object đó. Nếu bạn muốn gán nhiều export cùng một lúc, sử dụng định nghĩa kiểu object literal, bạn có thể làm như sau:

```js
Object.assign(module.exports,{
   // .. exports ..
});
```

Những gì đang diễn ra ở đây là định nghĩa object literal `{ .. }` với public API của module bạn được chỉ định, và sau đó `Object.assign(..)` thực hiện sao chép nông (shallow copy) tất cả các thuộc tính đó vào object `module.exports` hiện có, thay vì thay thế nó. Đây là sự cân bằng tuyệt vời giữa sự tiện lợi và hành vi module an toàn hơn.

Để đưa một module instance khác vào module/chương trình của bạn, hãy sử dụng phương thức `require(..)` của Node. Giả sử module này nằm tại "/path/to/student.js", đây là cách chúng ta có thể truy cập nó:

```js
var Student = require("/path/to/student.js");

Student.getName(73);
// Suzy
```

`Student` giờ đây tham chiếu đến public API của module ví dụ của chúng ta.

Các module CommonJS hoạt động như các singleton instance, tương tự như phong cách định nghĩa module bằng IIFE đã trình bày trước đó. Bất kể bạn `require(..)` cùng một module bao nhiêu lần, bạn cũng chỉ nhận được các tham chiếu bổ sung đến cùng một module instance duy nhất được chia sẻ đó.

`require(..)` là một cơ chế "được ăn cả, ngã về không" (all-or-nothing); nó bao gồm một tham chiếu đến toàn bộ public API được phơi bày của module. Để truy cập chỉ một phần của API một cách hiệu quả, cách tiếp cận thông thường trông như thế này:

```js
var getName = require("/path/to/student.js").getName;

// or alternately:

var { getName } = require("/path/to/student.js");
```

Tương tự như định dạng classic module, các phương thức được export công khai của API trong module CommonJS nắm giữ các closure đối với các chi tiết nội bộ của module. Đó là cách mà trạng thái singleton của module được duy trì trong suốt vòng đời của chương trình của bạn.

| GHI CHÚ: |
| :--- |
| Trong các câu lệnh `require("student")` của Node, các đường dẫn không tuyệt đối (`"student"`) sẽ giả định phần mở rộng tệp là ".js" và tìm kiếm trong "node_modules". |

## ES Modules hiện đại (ESM)

Định dạng ESM chia sẻ một số điểm tương đồng với định dạng CommonJS. ESM dựa trên tệp (file-based), và các instance của module là các singleton, với mọi thứ *mặc định là* private. Một điểm khác biệt đáng chú ý là các tệp ESM được mặc định là ở chế độ nghiêm ngặt (strict-mode), mà không cần khai báo chỉ thị `"use strict"` ở đầu. Không có cách nào để định nghĩa một ESM ở chế độ không nghiêm ngặt (non-strict-mode).

Thay vì `module.exports` trong CommonJS, ESM sử dụng từ khóa `export` để phơi bày thứ gì đó trên public API của module. Từ khóa `import` thay thế cho câu lệnh `require(..)`. Hãy điều chỉnh "students.js" để sử dụng định dạng ESM:

```js
export { getName };

// ************************

var records = [
    { id: 14, name: "Kyle", grade: 86 },
    { id: 73, name: "Suzy", grade: 87 },
    { id: 112, name: "Frank", grade: 75 },
    { id: 6, name: "Sarah", grade: 91 }
];

function getName(studentID) {
    var student = records.find(
        student => student.id == studentID
    );
    return student.name;
}
```

Thay đổi duy nhất ở đây là câu lệnh `export { getName }`. Như trước đây, các câu lệnh `export` có thể xuất hiện ở bất kỳ đâu trong tệp, mặc dù `export` phải ở top-level scope; nó không thể nằm bên trong bất kỳ block hay hàm nào khác.

ESM cung cấp khá nhiều biến thể về cách chỉ định các câu lệnh `export`. Ví dụ:

```js
export function getName(studentID) {
    // ..
}
```

Mặc dù `export` xuất hiện trước từ khóa `function` ở đây, dạng này vẫn là một khai báo `function` đồng thời được export. Nghĩa là, định danh `getName` được *hoist theo hàm* (*function hoisted* - xem Chương 5), do đó nó có sẵn trong toàn bộ scope của module.

Một biến thể khác được cho phép:

```js
export default function getName(studentID) {
    // ..
}
```

Đây là cái được gọi là "default export" (xuất mặc định), có ngữ nghĩa khác với các export khác. Về bản chất, một "default export" là một cách viết tắt cho những bên sử dụng module khi họ `import`, mang lại cho họ cú pháp ngắn gọn hơn khi họ chỉ cần duy nhất một thành viên API mặc định này.

Các export không phải `default` được gọi là "named exports" (xuất có tên).

Từ khóa `import`—giống như `export`, nó chỉ được sử dụng ở cấp cao nhất của một ESM bên ngoài bất kỳ block hay hàm nào—cũng có một số biến thể về cú pháp. Dạng đầu tiên được gọi là "named import":

```js
import { getName } from "/path/to/students.js";

getName(73);   // Suzy
```

Như bạn có thể thấy, dạng này chỉ import các thành viên public API được đặt tên cụ thể từ một module (bỏ qua bất kỳ thứ gì không được đặt tên rõ ràng), và nó thêm các định danh đó vào top-level scope của module hiện tại. Kiểu import này là phong cách quen thuộc với những ai đã quen dùng cách import package trong các ngôn ngữ như Java.

Nhiều thành viên API có thể được liệt kê bên trong tập hợp `{ .. }`, phân tách nhau bằng dấu phẩy. Một named import cũng có thể được *đổi tên* bằng từ khóa `as`:

```js
import { getName as getStudentName }
   from "/path/to/students.js";

getStudentName(73);
// Suzy
```

Nếu `getName` là một "default export" của module, chúng ta có thể import nó như thế này:

```js
import getName from "/path/to/students.js";

getName(73);   // Suzy
```

Khác biệt duy nhất ở đây là bỏ đi dấu `{ }` bao quanh binding import. Nếu bạn muốn kết hợp một default import với các named import khác:

```js
import { default as getName, /* .. others .. */ }
   from "/path/to/students.js";

getName(73);   // Suzy
```

Ngược lại, biến thể lớn còn lại của `import` được gọi là "namespace import":

```js
import * as Student from "/path/to/students.js";

Student.getName(73);   // Suzy
```

Như bạn có thể thấy rõ, dấu `*` sẽ import mọi thứ được export ra API, cả default lẫn named, và lưu trữ tất cả dưới định danh namespace duy nhất như đã chỉ định. Cách tiếp cận này khớp gần nhất với dạng classic module trong phần lớn lịch sử của JS.

| GHI CHÚ: |
| :--- |
| Tại thời điểm viết cuốn sách này, các trình duyệt hiện đại đã hỗ trợ ESM được vài năm, nhưng việc hỗ trợ ESM ổn định của Node còn khá mới mẻ và đã phát triển trong một thời gian dài. Sự phát triển này có thể sẽ tiếp tục trong một năm hoặc lâu hơn; việc đưa ESM vào JS từ ES6 đã tạo ra một số thách thức về khả năng tương thích đối với khả năng tương tác (interop) của Node với các module CommonJS. Hãy tham khảo tài liệu ESM của Node để biết tất cả các chi tiết mới nhất: https://nodejs.org/api/esm.html |

## Rời khỏi Scope

Cho dù bạn sử dụng định dạng classic module (trình duyệt hoặc Node), định dạng CommonJS (trong Node), hay định dạng ESM (trình duyệt hoặc Node), module vẫn là một trong những cách hiệu quả nhất để định cấu trúc và tổ chức chức năng cũng như dữ liệu trong chương trình của bạn.

Mẫu module là cái kết cho hành trình của chúng ta trong cuốn sách này về việc học cách chúng ta có thể sử dụng các quy tắc của lexical scope để đặt các biến và hàm vào đúng vị trí. POLE là tư thế phòng thủ *mặc định là private* mà chúng ta luôn áp dụng, đảm bảo chúng ta tránh việc phơi bày quá mức và chỉ tương tác với bề mặt public API tối thiểu cần thiết.

Và bên dưới các module, *phép màu* của cách mà toàn bộ trạng thái module của chúng ta được duy trì chính là các closure tận dụng hệ thống lexical scope.

Đó là toàn bộ nội dung chính của cuốn sách. Xin chúc mừng bạn đã đi được một chặng đường dài cho đến lúc này! Như tôi đã nói nhiều lần xuyên suốt cuốn sách, việc tạm dừng, suy ngẫm và thực hành những gì chúng ta vừa thảo luận là một ý tưởng thực sự tốt.

Khi bạn cảm thấy thoải mái và sẵn sàng, hãy xem qua các phụ lục, nơi sẽ đào sâu hơn vào một số ngóc ngách của các chủ đề này, đồng thời thử thách bạn với một số bài tập thực hành để củng cố những gì bạn đã học.
