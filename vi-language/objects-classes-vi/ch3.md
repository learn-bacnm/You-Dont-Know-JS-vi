# Bạn Chưa Biết JS Đâu: Đối Tượng & Lớp - Tái bản lần 2
# Chương 3: Lớp Đối Tượng (Classy Objects)

| GHI CHÚ: |
| :--- |
| Đang trong quá trình hoàn thiện (Work in progress) |

Mẫu thiết kế lớp (class-design pattern) nhìn chung đòi hỏi việc định nghĩa một *loại sự vật* (lớp - class), bao gồm dữ liệu (các thành viên - members) và các hành vi (các phương thức - methods), và sau đó tạo ra một hoặc nhiều *thể hiện* (instances) cụ thể từ định nghĩa lớp này dưới dạng các đối tượng thực tế có thể tương tác và thực thi các tác vụ. Hơn nữa, lập trình hướng lớp (class-orientation) cho phép khai báo mối quan hệ giữa hai hoặc nhiều lớp thông qua cơ chế được gọi là "kế thừa" (inheritance), nhằm phái sinh các "lớp con" (subclasses) mới và được tăng cường thêm tính năng, kết hợp và thậm chí tái định nghĩa các hành vi.

Trước ES6 (2015), các lập trình viên JS đã mô phỏng các khía cạnh của thiết kế hướng lớp (còn gọi là "hướng đối tượng") bằng cách sử dụng các hàm và đối tượng thông thường, cùng với cơ chế `[[Prototype]]` (như đã giải thích trong chương trước) -- thường được gọi là "các lớp nguyên mẫu" (prototypal classes).

Nhưng trong niềm vui và sự nhẹ nhõm của rất nhiều lập trình viên, ES6 đã giới thiệu cú pháp chuyên dụng, bao gồm các từ khóa `class` và `extends`, để diễn đạt thiết kế hướng lớp một cách mang tính khai báo (declarative) hơn.

Tại thời điểm `class` của ES6 được giới thiệu, cú pháp chuyên dụng mới này gần như hoàn toàn *chỉ là đường cú pháp* (syntactic sugar) giúp việc định nghĩa lớp trở nên thuận tiện và dễ đọc hơn. Tuy nhiên, qua nhiều năm kể từ ES6, `class` đã trưởng thành và phát triển thành một cơ chế tính năng hạng nhất độc lập, tích lũy một lượng đáng kể cú pháp chuyên dụng và các hành vi phức tạp vượt xa khả năng của "lớp nguyên mẫu" trước ES6.

Mặc dù giờ đây `class` gần như không còn nét tương đồng nào với phong cách viết mã "lớp nguyên mẫu" trước đây, engine JS bên dưới vẫn *chỉ* đang liên kết các đối tượng với nhau thông qua cơ chế `[[Prototype]]` sẵn có. Nói cách khác, `class` không phải là một trụ cột riêng biệt của ngôn ngữ (như `[[Prototype]]`), mà giống như chiếc *Đầu cột* hoa mỹ, lộng lẫy ngự trên đỉnh của cây cột/trụ đó.

Dù vậy, vì phong cách viết mã bằng `class` hiện nay đã thay thế hầu như toàn bộ cách viết "lớp nguyên mẫu" trước đó, nội dung chính ở đây sẽ chỉ tập trung vào `class` và các khía cạnh cụ thể của nó. Vì mục đích lịch sử, chúng ta sẽ đề cập ngắn gọn về phong cách "lớp nguyên mẫu" cũ trong phần phụ lục.

## Khi Nào Tôi Nên Hướng Lớp Cho Mã Nguồn Của Mình? (When Should I Class-Orient My Code?)

Lập trình hướng lớp là một mẫu thiết kế (design pattern), đồng nghĩa với việc nó là một sự lựa chọn về cách bạn tổ chức thông tin và hành vi trong chương trình của mình. Nó có cả ưu điểm lẫn nhược điểm. Nó không phải là một giải pháp vạn năng cho mọi tác vụ.

Vậy làm thế nào để bạn biết khi nào mình nên sử dụng lớp?

Theo phương diện lý thuyết, hướng lớp là một cách phân chia miền nghiệp vụ (business domain) của một chương trình thành một hoặc nhiều mảnh mà mỗi mảnh có thể được định nghĩa bằng một phân loại "là-một" (is-a): gom nhóm một sự vật vào tập hợp (hoặc các tập hợp) các đặc tính mà sự vật đó chia sẻ với những sự vật tương tự khác. Bạn sẽ nói "X là một Y", nghĩa là X có (ít nhất) tất cả các đặc tính của một sự vật thuộc loại Y.

Chẳng hạn, hãy xem xét máy tính. Chúng ta có thể nói máy tính là một thiết bị điện (electrical), vì nó sử dụng dòng điện (điện áp, ampe, v.v.) làm năng lượng. Hơn nữa nó còn là thiết bị điện tử (electronic), bởi vì nó điều khiển dòng điện vượt xa việc đơn thuần định tuyến các electron chạy xung quanh (điện/từ trường), tạo ra một mạch điện có ý nghĩa nhằm thao tác dòng điện thực hiện các tác vụ phức tạp hơn. Ngược lại, một chiếc đèn bàn cơ bản chỉ là thiết bị điện, chứ không hẳn là thiết bị điện tử.

Do đó, chúng ta có thể định nghĩa một lớp `Electrical` để mô tả những gì thiết bị điện cần và có thể làm. Sau đó chúng ta có thể định nghĩa thêm một lớp `Electronic`, và quy định rằng ngoài việc là một thiết bị điện, những thứ `Electronic` còn thao tác dòng điện để tạo ra các kết quả chuyên biệt hóa hơn.

Đây là nơi mà lập trình hướng lớp bắt đầu tỏa sáng. Thay vì định nghĩa lại toàn bộ các đặc tính của `Electrical` trong lớp `Electronic`, chúng ta có thể định nghĩa `Electronic` theo cách mà nó "chia sẻ" hoặc "kế thừa" những đặc tính đó từ `Electrical`, rồi sau đó mở rộng/định nghĩa lại các hành vi độc nhất làm nên một thiết bị điện tử. Mối quan hệ này giữa hai lớp -- được gọi là "kế thừa" (inheritance) -- là một khía cạnh cốt lõi của lập trình hướng lớp.

Vì vậy, hướng lớp là một cách tư duy về các thực thể mà chương trình của chúng ta cần, phân loại chúng vào các nhóm dựa trên các đặc tính của chúng (chúng chứa thông tin gì, những thao tác nào có thể được thực hiện trên dữ liệu đó), và xác định các mối quan hệ giữa các nhóm đặc tính khác nhau.

Nhưng chuyển từ góc nhìn lý thuyết sang một góc nhìn thực dụng hơn một chút: nếu chương trình của bạn cần nắm giữ và sử dụng đồng thời nhiều tập hợp (các thể hiện) dữ liệu/hành vi giống nhau, bạn *có thể* hưởng lợi từ lập trình hướng lớp.

### Đã Đến Lúc Cho Một Ví Dụ (Time For An Example)

Dưới đây là một minh họa ngắn gọn.

Vài thập kỷ trước, ngay sau khi tôi hoàn thành gần như toàn bộ chương trình cử nhân Khoa học Máy tính ở trường đại học, tôi bắt đầu công việc lập trình viên phần mềm chuyên nghiệp đầu tiên của mình. Tôi được giao nhiệm vụ tự mình xây dựng một hệ thống theo dõi chấm công và tính lương. Tôi đã xây dựng backend bằng PHP (sử dụng MySQL làm cơ sở dữ liệu) và dùng JS cho giao diện (vào thời kỳ đó JS vẫn còn rất non trẻ, vào khoảng đầu thế kỷ này).

Bởi vì chương trình Khoa học Máy tính của tôi nhấn mạnh rất nhiều vào lập trình hướng lớp xuyên suốt các khóa học, tôi đã rất háo hức áp dụng tất cả mớ lý thuyết đó vào thực tế. Đối với thiết kế chương trình của mình, tôi đã định nghĩa khái niệm thực thể "bảng chấm công" (timesheet) như một tập hợp gồm 2-3 thực thể "tuần" (week), và mỗi "tuần" là một tập hợp gồm 5-7 thực thể "ngày" (day), và mỗi "ngày" là một tập hợp các thực thể "công việc" (task).

Nếu tôi muốn biết có bao nhiêu giờ đã được ghi nhận trong một thể hiện bảng chấm công, tôi có thể gọi thao tác `totalTime()` trên thể hiện đó. Bảng chấm công định nghĩa thao tác này bằng cách lặp qua tập hợp các tuần của nó, gọi `totalTime()` trên từng tuần và tính tổng các giá trị. Mỗi tuần lại làm điều tương tự cho tất cả các ngày của nó, và mỗi ngày lại làm điều tương tự cho tất cả các công việc của nó.

Khái niệm được minh họa ở đây, một trong những nền tảng của các mẫu thiết kế như hướng lớp, được gọi là *tính đóng gói* (encapsulation). Mỗi cấp độ thực thể đóng gói (ví dụ: kiểm soát, ẩn giấu, trừu tượng hóa) các chi tiết nội bộ (dữ liệu và hành vi) trong khi đưa ra một giao diện bên ngoài hữu ích.

Nhưng riêng tính đóng gói thì chưa phải là lý do đầy đủ để biện minh cho lập trình hướng lớp. Các mẫu thiết kế khác cũng cung cấp khả năng đóng gói đầy đủ.

Thiết kế lớp của tôi đã tận dụng tính kế thừa như thế nào? Tôi có một lớp cơ sở (base class) định nghĩa một tập hợp các thao tác như `totalTime()`, và mỗi loại lớp thực thể của tôi đều kế thừa/tạo lớp con từ lớp cơ sở này. Điều đó có nghĩa là mỗi lớp đều kế thừa khả năng tính tổng thời gian này, nhưng mỗi lớp lại áp dụng các phần mở rộng và định nghĩa riêng của chúng cho các chi tiết nội bộ về *cách thức* thực hiện công việc đó.

Còn có một khía cạnh khác của mẫu thiết kế này đang cùng phát huy tác dụng, đó là *tính kết hợp* (composition): mỗi thực thể được định nghĩa như một tập hợp của các thực thể khác.

### Đơn Lẻ so với Nhiều Thể Hiện (Single vs Multiple)

Tôi đã đề cập ở trên rằng một cách thực dụng để quyết định xem bạn có cần lập trình hướng lớp hay không là liệu chương trình của bạn có chứa nhiều thể hiện của một loại/kiểu hành vi (hay còn gọi là "lớp") duy nhất hay không. Trong ví dụ về bảng chấm công, chúng ta có 4 lớp: Timesheet, Week, Day, và Task. Nhưng đối với mỗi lớp, chúng ta đều có đồng thời nhiều thể hiện.

Nếu thay vào đó chúng ta chỉ cần một thể hiện duy nhất của một lớp, chẳng hạn như chỉ một thứ `Computer` vốn là thể hiện của lớp `Electronic`, lớp này lại là lớp con của lớp `Electrical`, thì lập trình hướng lớp có thể không đem lại quá nhiều lợi ích. Đặc biệt, nếu chương trình không cần tạo một thể hiện của lớp `Electrical`, thì không có lợi ích cụ thể nào trong việc tách biệt `Electrical` khỏi `Electronic`, do đó chúng ta không thực sự nhận được sự trợ giúp nào từ khía cạnh kế thừa của lập trình hướng lớp.

Vì vậy, nếu bạn thấy mình đang thiết kế một chương trình bằng cách phân chia miền bài toán nghiệp vụ thành các "lớp" thực thể khác nhau, nhưng trong mã nguồn thực tế của chương trình, bạn chỉ cần duy nhất một *thứ* cụ thể của một loại/định nghĩa hành vi (hay còn gọi là "lớp"), thì rất có thể bạn thực sự không cần đến lập trình hướng lớp. Có những mẫu thiết kế khác có thể phù hợp và hiệu quả hơn nhiều với nỗ lực của bạn.

Nhưng nếu bạn thấy mình muốn định nghĩa các lớp, và các lớp con kế thừa từ chúng, đồng thời bạn sẽ khởi tạo một hoặc nhiều lớp đó nhiều lần, thì hướng lớp là một ứng cử viên sáng giá. Và để thực hiện lập trình hướng lớp trong JS, bạn sẽ cần đến từ khóa `class`.

## Giữ Mã Nguồn Mang Phong Cách `class` (Keep It `class`y)

`class` dùng để định nghĩa một khai báo (declaration) hoặc một biểu thức (expression) cho một lớp. Dưới dạng một khai báo, một định nghĩa lớp xuất hiện ở vị trí của một câu lệnh và trông như thế này:

```js
class Point2d {
    // ..
}
```

Dưới dạng một biểu thức, một định nghĩa lớp xuất hiện ở vị trí của một giá trị và có thể có tên hoặc là ẩn danh:

```js
// biểu thức lớp có tên (named class expression)
const pointClass = class Point2d {
    // ..
};

// biểu thức lớp ẩn danh (anonymous class expression)
const anotherClass = class {
    // ..
};
```

Nội dung phần thân của một `class` thường bao gồm một hoặc nhiều định nghĩa phương thức:

```js
class Point2d {
    setX(x) {
        // ..
    }
    setY(y) {
        // ..
    }
}
```

Bên trong phần thân `class`, các phương thức được định nghĩa mà không cần từ khóa `function`, đồng thời không có dấu phân cách `,` hay `;` giữa các định nghĩa phương thức.

| GHI CHÚ: |
| :--- |
| Bên trong khối lệnh `class`, tất cả mã nguồn luôn chạy ở chế độ nghiêm ngặt (strict-mode) ngay cả khi không có chỉ thị `"use strict"` xuất hiện trong tệp hay trong các hàm của nó. Cụ thể, điều này ảnh hưởng đến hành vi của `this` đối với các lời gọi hàm, như sẽ được giải thích trong Chương 4. |

### Hàm Khởi Tạo (The Constructor)

Một phương thức đặc biệt mà mọi lớp đều có được gọi là "hàm khởi tạo" (constructor). Nếu bị bỏ qua, một hàm khởi tạo rỗng mặc định sẽ được ngầm định trong định nghĩa lớp.

Hàm khởi tạo được gọi mỗi khi một thể hiện `new` của lớp được tạo ra:

```js
class Point2d {
    constructor() {
        console.log("Here's your new instance!");
    }
}

var point = new Point2d();
// Here's your new instance!
```

Mặc dù cú pháp gợi ý rằng thực sự tồn tại một hàm có tên là `constructor`, JS định nghĩa một hàm như đã chỉ định, nhưng với tên của lớp (`Point2d` ở trên):

```js
typeof Point2d;       // "function"
```

Tuy nhiên, nó không *chỉ* là một hàm thông thường; loại hàm đặc biệt này hành xử hơi khác một chút:

```js
Point2d.toString();
// class Point2d {
//   ..
// }

Point2d();
// TypeError: Class constructor Point2d cannot
// be invoked without 'new'

Point2d.call({});
// TypeError: Class constructor Point2d cannot
// be invoked without 'new'
```

Bạn có thể khởi tạo bao nhiêu thể hiện khác nhau của một lớp tùy ý:

```js
var one = new Point2d();
var two = new Point2d();
var three = new Point2d();
```

Mỗi biến `one`, `two`, và `three` ở đây đều là các đối tượng là những thể hiện độc lập của lớp `Point2d`.

| GHI CHÚ: |
| :--- |
| Mỗi đối tượng `one`, `two`, và `three` đều có một liên kết `[[Prototype]]` tới đối tượng `Point2d.prototype` (xem Chương 2). Trong đoạn mã này, `Point2d` vừa là một định nghĩa `class` vừa là hàm khởi tạo cùng tên. |

Nếu bạn thêm một thuộc tính vào đối tượng `one`:

```js
one.value = 42;
```

Thuộc tính đó giờ đây chỉ tồn tại duy nhất trên `one`, và hoàn toàn không tồn tại theo bất kỳ cách nào mà các đối tượng độc lập `two` hoặc `three` có thể truy cập được:

```js
two.value;      // undefined
three.value;    // undefined
```

### Các Phương Thức Của Lớp (Class Methods)

Như đã trình bày ở trên, một định nghĩa lớp có thể bao gồm một hoặc nhiều định nghĩa phương thức:

```js
class Point2d {
    constructor() {
        console.log("Here's your new instance!");
    }
    setX(x) {
        console.log(`Setting x to: ${x}`);
        // ..
    }
}

var point = new Point2d();

point.setX(3);
// Setting x to: 3
```

Thuộc tính `setX` (phương thức) *trông như thể* nó tồn tại trên (thuộc sở hữu của) đối tượng `point` ở đây. Nhưng đó chỉ là ảo ảnh. Mỗi phương thức của lớp thực chất được thêm vào đối tượng `prototype`, một thuộc tính của hàm khởi tạo.

Vì vậy, `setX(..)` chỉ tồn tại dưới dạng `Point2d.prototype.setX`. Vì `point` được liên kết `[[Prototype]]` với `Point2d.prototype` (xem Chương 2) thông qua việc khởi tạo bằng từ khóa `new`, tham chiếu `point.setX(..)` sẽ duyệt qua chuỗi `[[Prototype]]` và tìm thấy phương thức đó để thực thi.

Các phương thức lớp chỉ nên được gọi thông qua một thể hiện; `Point2d.setX(..)` không hoạt động vì *không hề có* thuộc tính nào như vậy trên chính hàm `Point2d`. Bạn *có thể* gọi `Point2d.prototype.setX(..)`, nhưng điều đó nhìn chung không đúng chuẩn mực/không được khuyến khích trong phong cách lập trình hướng lớp tiêu chuẩn. Hãy luôn truy cập các phương thức lớp thông qua các thể hiện.

## Từ Khóa `this` Của Thể Hiện Lớp (Class Instance `this`)

Chúng ta sẽ tìm hiểu từ khóa `this` một cách chi tiết hơn nhiều trong một chương tiếp theo. Nhưng khi xét trong mối liên hệ với mã nguồn hướng lớp, từ khóa `this` nhìn chung tham chiếu đến thể hiện hiện tại vốn là ngữ cảnh của bất kỳ lời gọi phương thức nào.

Trong hàm khởi tạo, cũng như bất kỳ phương thức nào, bạn có thể sử dụng cú pháp `this.` để thêm hoặc truy cập các thuộc tính trên thể hiện hiện tại:

```js
class Point2d {
    constructor(x,y) {
        // thêm các thuộc tính vào thể hiện hiện tại
        this.x = x;
        this.y = y;
    }
    toString() {
        // truy cập các thuộc tính từ thể hiện hiện tại
        console.log(`(${this.x},${this.y})`);
    }
}

var point = new Point2d(3,4);

point.x;                // 3
point.y;                // 4

point.toString();       // (3,4)
```

Bất kỳ thuộc tính nào không giữ giá trị là hàm, được thêm vào một thể hiện lớp (thường là thông qua hàm khởi tạo), đều được gọi là *thành viên* (members), trái ngược với thuật ngữ *phương thức* (methods) dùng cho các hàm có thể thực thi.

Trong khi phương thức `point.toString()` đang chạy, tham chiếu `this` của nó đang trỏ vào cùng một đối tượng mà `point` tham chiếu. Đó là lý do tại sao cả `point.x` và `this.x` đều cho ra cùng một giá trị `3` mà hàm khởi tạo đã thiết lập bằng thao tác `this.x = x`.

### Các Trường Công Khai (Public Fields)

Thay vì định nghĩa một thành viên thể hiện lớp theo kiểu mệnh lệnh (imperatively) thông qua `this.` trong hàm khởi tạo hoặc phương thức, các lớp có thể định nghĩa các *trường* (fields) theo kiểu khai báo (declaratively) trong phần thân `class`, tương ứng trực tiếp với các thành viên sẽ được tạo trên mỗi thể hiện:

```js
class Point2d {
    // đây là các trường công khai
    x = 0
    y = 0

    constructor(x,y) {
        // thiết lập các thuộc tính (trường) trên thể hiện hiện tại
        this.x = x;
        this.y = y;
    }
    toString() {
        // truy cập các thuộc tính từ thể hiện hiện tại
        console.log(`(${this.x},${this.y})`);
    }
}
```

Các trường công khai có thể có giá trị khởi tạo, như minh họa ở trên, nhưng điều đó không bắt buộc. Nếu bạn không khởi tạo giá trị cho một trường trong định nghĩa lớp, hầu như bạn luôn nên khởi tạo nó trong hàm khởi tạo.

Các trường cũng có thể tham chiếu lẫn nhau, thông qua cú pháp truy cập tự nhiên `this.`:

```js
class Point3d {
    // đây là các trường công khai
    x
    y = 4
    z = this.y * 5

    // ..
}
```

| MẸO: |
| :--- |
| Bạn phần lớn có thể xem các khai báo trường công khai như thể chúng xuất hiện ở đầu `constructor(..)`, mỗi trường được gắn tiền tố ngầm định là `this.` mà bạn được phép lược bỏ trong cú pháp thân `class` mang tính khai báo. Nhưng có một điểm gài bẫy! Xem phần "Thật Là Super!" ở phía sau để biết thêm thông tin về nó. |

Tương tự như tên thuộc tính được tính toán (computed property names - xem Chương 1), tên trường cũng có thể được tính toán:

```js
var coordName = "x";

class Point2d {
    // trường công khai được tính toán tên
    [coordName.toUpperCase()] = 42

    // ..
}

var point = new Point2d(3,4);

point.x;        // 3
point.y;        // 4

point.X;        // 42
```

#### Tránh Làm Điều Này (Avoid This)

Một mẫu thiết kế đã xuất hiện và trở nên khá phổ biến, nhưng tôi tin chắc rằng đó là một phản mẫu (anti-pattern) đối với `class`, trông như sau:

```js
class Point2d {
    x = null
    y = null
    getDoubleX = () => this.x * 2

    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
    toString() { /* .. */ }
}

var point = new Point2d(3,4);

point.getDoubleX();    // 6
```

Bạn có thấy trường chứa một hàm mũi tên `=>` không? Tôi cho rằng đây là điều cấm kỵ. Nhưng tại sao? Hãy cùng phân tích những gì đang diễn ra.

Đầu tiên, tại sao người ta lại làm điều này? Bởi vì các lập trình viên JS dường như luôn bực bội với các quy tắc ràng buộc `this` động (xem Chương 4), nên họ ép buộc ràng buộc `this` thông qua hàm mũi tên `=>`. Bằng cách đó, bất kể `getDoubleX()` được gọi như thế nào, nó luôn được gắn chặt (ràng buộc `this`) với thể hiện cụ thể đó. Đó là một sự tiện lợi dễ hiểu mà ai cũng mong muốn, nhưng... nó phản bội lại bản chất của chính trụ cột `this` / `[[Prototype]]` của ngôn ngữ. Bằng cách nào?

Hãy xem xét đoạn mã tương đương với đoạn mã trước:

```js
class Point2d {
    constructor(x,y) {
        this.x = null;
        this.y = null;
        this.getDoubleX = () => this.x * 2;

        this.x = x;
        this.y = y;
    }
    toString() { /* .. */ }
}

var point = new Point2d(3,4);

point.getDoubleX();    // 6
```

Bạn có nhận ra vấn đề không? Hãy nhìn kỹ xem. Tôi sẽ đợi.

...

Từ đầu đến giờ chúng ta đã nói rõ nhiều lần rằng các định nghĩa `class` đặt các phương thức của chúng lên đối tượng `prototype` của hàm khởi tạo của lớp -- đó là nơi chúng thuộc về! -- sao cho chỉ có duy nhất một hàm cho mỗi định nghĩa và nó được kế thừa (chia sẻ) bởi tất cả các thể hiện. Đó là những gì sẽ diễn ra với `toString()` trong đoạn mã trên.

Nhưng còn `getDoubleX()` thì sao? Về bản chất nó là một phương thức của lớp, nhưng nó sẽ không được JS xử lý hoàn toàn giống như `toString()`. Hãy xem xét:

```js
Object.hasOwn(point,"x");               // true -- tốt
Object.hasOwn(point,"toString");        // false -- tốt
Object.hasOwn(point,"getDoubleX");      // true -- ôi thôi :(
```

Bây giờ bạn đã thấy rồi chứ? Bằng cách định nghĩa một giá trị hàm và gắn nó như một thuộc tính trường/thành viên, chúng ta đang đánh mất bản chất phương thức nguyên mẫu dùng chung của hàm đó, và nó trở thành thuộc tính riêng của từng thể hiện. Điều đó có nghĩa là chúng ta đang tạo ra một thuộc tính hàm mới **cho mỗi thể hiện**, thay vì nó chỉ được tạo một lần duy nhất trên `prototype` của hàm khởi tạo lớp.

Điều đó gây lãng phí về mặt hiệu năng và bộ nhớ, dù chỉ là một lượng nhỏ. Riêng điều đó thôi cũng đã đủ lý do để tránh nó.

Nhưng tôi muốn lập luận rằng điều quan trọng hơn rất nhiều là: những gì bạn đã làm với mẫu này đã vô hiệu hóa chính lý do tại sao việc sử dụng `class` và các phương thức nhận biết `this` lại hữu ích/mạnh mẽ ngay từ đầu!

Nếu bạn cất công định nghĩa các phương thức lớp với các tham chiếu `this.` xuyên suốt chúng, nhưng rồi sau đó bạn lại khóa/ràng buộc hầu hết hoặc tất cả các phương thức đó vào một thể hiện đối tượng cụ thể, thì về cơ bản bạn đã đi một vòng quanh thế giới chỉ để sang nhà hàng xóm bên cạnh.

Nếu tất cả những gì bạn muốn là (các) hàm được cố định tĩnh vào một "ngữ cảnh" cụ thể, và không cần bất kỳ tính động hay sự chia sẻ nào, thì thứ bạn cần chính là... **closure**. Và bạn thật may mắn: tôi đã viết hẳn một cuốn sách trọn vẹn trong bộ sách này ("Scope & Closures") về cách sử dụng closure để các hàm ghi nhớ/truy cập phạm vi được định nghĩa tĩnh của chúng (hay còn gọi là "ngữ cảnh"). Đó là một cách tiếp cận thích hợp hơn nhiều, và đơn giản hơn nhiều để viết mã nhằm đạt được mục đích của bạn.

Đừng lạm dụng/sử dụng sai `class` và biến nó thành một tập hợp closure được thổi phồng, ca tụng quá mức.

Để nói cho rõ ràng, tôi *không* hề nói: đừng bao giờ dùng hàm mũi tên `=>` bên trong các lớp.

Tôi *thực sự* đang nói: đừng bao giờ gắn một hàm mũi tên `=>` làm thuộc tính thể hiện để thay thế cho một phương thức lớp nguyên mẫu động, dù là do thói quen vô thức, hay lười biếng gõ ít ký tự hơn, hay vì sự tiện lợi sai lầm trong việc ràng buộc `this`.

Trong một chương tiếp theo, chúng ta sẽ đi sâu vào việc tìm hiểu và tận dụng hợp lý toàn bộ sức mạnh của cơ chế `this` động.

## Kế Thừa Lớp (Class Extension)

Cách để mở khóa sức mạnh của kế thừa lớp là thông qua từ khóa `extends`, vốn xác lập mối quan hệ giữa hai lớp:

```js
class Point2d {
    x = 3
    y = 4

    getX() {
        return this.x;
    }
}

class Point3d extends Point2d {
    x = 21
    y = 10
    z = 5

    printDoubleX() {
        console.log(`double x: ${this.getX() * 2}`);
    }
}

var point = new Point2d();

point.getX();                   // 3

var anotherPoint = new Point3d();

anotherPoint.getX();            // 21
anotherPoint.printDoubleX();    // double x: 42
```

Hãy dành vài giây để đọc lại đoạn mã đó và đảm bảo bạn hiểu đầy đủ những gì đang xảy ra.

Lớp cơ sở `Point2d` định nghĩa các trường (thành viên) có tên là `x` và `y`, và gán cho chúng các giá trị ban đầu lần lượt là `3` và `4`. Nó cũng định nghĩa một phương thức `getX()` để truy cập thành viên thể hiện `x` này và trả về nó. Chúng ta thấy hành vi đó được minh họa trong lời gọi phương thức `point.getX()`.

Nhưng lớp `Point3d` mở rộng từ `Point2d`, biến `Point3d` thành một lớp phái sinh (derived-class), lớp con (child-class), hoặc (phổ biến nhất) là phân lớp (subclass). Trong `Point3d`, thuộc tính `x` được kế thừa từ `Point2d` được tái khởi tạo với một giá trị khác là `21`, cũng như `y` bị ghi đè giá trị từ `4` thành `10`.

Nó cũng thêm một trường/phương thức thành viên mới là `z`, cùng với một phương thức `printDoubleX()`, bản thân phương thức này lại gọi `this.getX()`.

Khi `anotherPoint.printDoubleX()` được gọi, phương thức kế thừa `this.getX()` do đó được thực thi, và phương thức đó tham chiếu đến `this.x`. Vì `this` đang trỏ vào thể hiện của lớp (tức là `anotherPoint`), giá trị mà nó tìm thấy giờ đây là `21` (thay vì `3` từ thành viên `x` của đối tượng `point`).

### Mở Rộng Biểu Thức (Extending Expressions)

// TODO: đề cập đến `class Foo extends ..` trong đó `..` là một biểu thức, không phải là tên lớp

### Ghi Đè Phương Thức (Overriding Methods)

Ngoài việc ghi đè một trường/thành viên trong lớp con, bạn cũng có thể ghi đè (tái định nghĩa) một phương thức:

```js
class Point2d {
    x = 3
    y = 4

    getX() {
        return this.x;
    }
}

class Point3d extends Point2d {
    x = 21
    y = 10
    z = 5

    getX() {
        return this.x * 2;
    }
    printX() {
        console.log(`double x: ${this.getX()}`);
    }
}

var point = new Point3d();

point.printX();       // double x: 42
```

Lớp con `Point3d` ghi đè phương thức kế thừa `getX()` để mang lại cho nó hành vi khác biệt. Tuy nhiên, bạn vẫn có thể khởi tạo lớp cơ sở `Point2d`, và khi đó nó sẽ cho ra một đối tượng sử dụng định nghĩa ban đầu (`return this.x;`) cho `getX()`.

Nếu bạn muốn truy cập một phương thức kế thừa từ một lớp con ngay cả khi nó đã bị ghi đè, bạn có thể sử dụng `super` thay vì `this`:

```js
class Point2d {
    x = 3
    y = 4

    getX() {
        return this.x;
    }
}

class Point3d extends Point2d {
    x = 21
    y = 10
    z = 5

    getX() {
        return this.x * 2;
    }
    printX() {
        console.log(`x: ${super.getX()}`);
    }
}

var point = new Point3d();

point.printX();       // x: 21
```

Khả năng cho phép các phương thức cùng tên ở các cấp bậc khác nhau của hệ thống phân cấp kế thừa thể hiện hành vi khác nhau khi được truy cập trực tiếp, hoặc truy cập tương đối bằng `super`, được gọi là *tính đa hình phương thức* (method polymorphism). Đây là một phần rất mạnh mẽ của lập trình hướng lớp khi được sử dụng thích hợp.

### Thật Là Super! (That's Super!)

Bên cạnh việc một phương thức lớp con truy cập định nghĩa phương thức kế thừa (ngay cả khi đã bị ghi đè trên lớp con) thông qua tham chiếu `super.`, hàm khởi tạo của lớp con bắt buộc phải gọi hàm khởi tạo của lớp cơ sở được kế thừa một cách thủ công thông qua lời gọi hàm `super(..)`:

```js
class Point2d {
    x
    y
    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
}

class Point3d extends Point2d {
    z
    constructor(x,y,z) {
        super(x,y);
        this.z = z;
    }
    toString() {
        console.log(`(${this.x},${this.y},${this.z})`);
    }
}

var point = new Point3d(3,4,5);

point.toString();       // (3,4,5)
```

| CẢNH BÁO: |
| :--- |
| Một hàm khởi tạo lớp con được định nghĩa tường minh *bắt buộc* phải gọi `super(..)` để chạy phần khởi tạo của lớp được kế thừa, và việc này phải diễn ra trước khi hàm khởi tạo lớp con thực hiện bất kỳ tham chiếu nào đến `this` hoặc kết thúc/trả về. Nếu không, một ngoại lệ thời gian chạy (runtime exception) sẽ bị ném ra khi hàm khởi tạo lớp con đó được gọi (thông qua `new`). Nếu bạn bỏ qua hàm khởi tạo lớp con, hàm khởi tạo mặc định sẽ tự động -- thật may mắn! -- gọi `super()` giúp bạn. |

Một sắc thái cần lưu ý: nếu bạn định nghĩa một trường (công khai hoặc riêng tư) bên trong một lớp con, và định nghĩa tường minh một `constructor(..)` cho lớp con này, các quá trình khởi tạo trường sẽ không được xử lý ở đầu hàm khởi tạo, mà diễn ra *ở giữa* lời gọi `super(..)` và bất kỳ đoạn mã tiếp theo nào trong hàm khởi tạo.

Hãy chú ý kỹ đến thứ tự của các thông báo trên console ở đây:

```js
class Point2d {
    x
    y
    constructor(x,y) {
        console.log("Running Point2d(..) constructor");
        this.x = x;
        this.y = y;
    }
}

class Point3d extends Point2d {
    z = console.log("Initializing field 'z'")

    constructor(x,y,z) {
        console.log("Running Point3d(..) constructor");
        super(x,y);

        console.log(`Setting instance property 'z' to ${z}`);
        this.z = z;
    }
    toString() {
        console.log(`(${this.x},${this.y},${this.z})`);
    }
}

var point = new Point3d(3,4,5);
// Running Point3d(..) constructor
// Running Point2d(..) constructor
// Initializing field 'z'
// Setting instance property 'z' to 5
```

Như các thông báo console đã minh họa, việc khởi tạo trường `z = ..` diễn ra *ngay sau* lời gọi `super(x,y)`, *trước khi* ``console.log(`Setting instance...`)`` được thực thi. Bạn có thể hình dung việc khởi tạo trường giống như được gắn vào phần cuối của lời gọi `super(..)`, nhờ đó chúng chạy trước bất kỳ mã nào khác trong hàm khởi tạo lớp con.

#### Lớp Nào? (Which Class?)

Bạn có thể cần xác định trong hàm khởi tạo xem lớp đó đang được khởi tạo trực tiếp hay đang được khởi tạo từ một lớp con thông qua một lời gọi `super()`. Chúng ta có thể sử dụng "thuộc tính giả" (pseudo property) đặc biệt `new.target`:

```js
class Point2d {
    // ..

    constructor(x,y) {
        if (new.target === Point2d) {
            console.log("Constructing 'Point2d' instance");
        }
    }

    // ..
}

class Point3d extends Point2d {
    // ..

    constructor(x,y,z) {
        super(x,y);

        if (new.target === Point3d) {
            console.log("Constructing 'Point3d' instance");
        }
    }

    // ..
}

var point = new Point2d(3,4);
// Constructing 'Point2d' instance

var anotherPoint = new Point3d(3,4,5);
// Constructing 'Point3d' instance
```

### Nhưng Là Loại Thể Hiện Nào? (But Which Kind Of Instance?)

Bạn có thể muốn kiểm tra nội quan (introspect) một thể hiện đối tượng nhất định để xem liệu nó có phải là thể hiện của một lớp cụ thể hay không. Chúng ta thực hiện điều này bằng toán tử `instanceof`:

```js
class Point2d { /* .. */ }
class Point3d extends Point2d { /* .. */ }

var point = new Point2d(3,4);

point instanceof Point2d;           // true
point instanceof Point3d;           // false

var anotherPoint = new Point3d(3,4,5);

anotherPoint instanceof Point2d;    // true
anotherPoint instanceof Point3d;    // true
```

Có vẻ kỳ lạ khi thấy `anotherPoint instanceof Point2d` cho kết quả `true`. Để hiểu rõ hơn lý do tại sao, có lẽ việc trực quan hóa cả hai chuỗi `[[Prototype]]` sẽ rất hữu ích:

```
Point2d.prototype
        /       \
       /         \
      /           \
  point   Point3d.prototype
                    \
                     \
                      \
                    anotherPoint
```

Toán tử `instanceof` không chỉ nhìn vào đối tượng hiện tại, mà duyệt qua toàn bộ hệ thống phân cấp kế thừa lớp (chuỗi `[[Prototype]]`) cho đến khi nó tìm thấy một sự trùng khớp. Do đó, `anotherPoint` vừa là một thể hiện của `Point3d` vừa là một thể hiện của `Point2d`.

Để minh họa sự thật này rõ ràng hơn một chút, một cách khác (kém thuận tiện hơn) để thực hiện cùng loại kiểm tra như `instanceof` là sử dụng tiện ích (được kế thừa từ `Object.prototype`) mang tên `isPrototypeOf(..)`:

```js
Point2d.prototype.isPrototypeOf(point);             // true
Point3d.prototype.isPrototypeOf(point);             // false

Point2d.prototype.isPrototypeOf(anotherPoint);      // true
Point3d.prototype.isPrototypeOf(anotherPoint);      // true
```

Tiện ích này làm sáng tỏ hơn lý do tại sao cả `Point2d.prototype.isPrototypeOf(anotherPoint)` và `anotherPoint instanceof Point2d` đều cho kết quả `true`: đối tượng `Point2d.prototype` *thực sự nằm* trong chuỗi `[[Prototype]]` của `anotherPoint`.

Nếu thay vào đó bạn muốn kiểm tra xem thể hiện đối tượng có phải được tạo ra *chỉ và trực tiếp* bởi một lớp nhất định hay không, hãy kiểm tra thuộc tính `constructor` của thể hiện:

```js
point.constructor === Point2d;          // true
point.constructor === Point3d;          // false

anotherPoint.constructor === Point2d;   // false
anotherPoint.constructor === Point3d;   // true
```

| GHI CHÚ: |
| :--- |
| Thuộc tính `constructor` được hiển thị ở đây *không* thực sự hiện diện trên (thuộc quyền sở hữu của) các đối tượng thể hiện `point` hoặc `anotherPoint`. Vậy nó đến từ đâu!? Nó nằm trên đối tượng prototype được liên kết `[[Prototype]]` của mỗi đối tượng: `Point2d.prototype.constructor === Point2d` và `Point3d.prototype.constructor === Point3d`. |

### "Kế Thừa" Là Chia Sẻ, Không Phải Sao Chép ("Inheritance" Is Sharing, Not Copying)

Có vẻ như `Point3d`, khi `extends` lớp `Point2d`, về bản chất đang nhận được một bản *sao chép* (copy) của toàn bộ hành vi được định nghĩa trong `Point2d`. Hơn nữa, có vẻ như thể hiện đối tượng cụ thể `anotherPoint` nhận được các phương thức được *sao chép xuống* từ `Point3d` (và mở rộng ra, cũng từ `Point2d`).

Tuy nhiên, đó không phải là mô hình tư duy chính xác khi xét về cách triển khai tính hướng lớp của JS. Hãy nhớ lại định nghĩa lớp cơ sở và lớp con này, cũng như việc khởi tạo `anotherPoint`:

```js
class Point2d {
    x
    y
    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
}

class Point3d extends Point2d {
    z
    constructor(x,y,z) {
        super(x,y);
        this.z = z;
    }
    toString() {
        console.log(`(${this.x},${this.y},${this.z})`);
    }
}

var anotherPoint = new Point3d(3,4,5);
```

Nếu bạn kiểm tra đối tượng `anotherPoint`, bạn sẽ thấy nó chỉ có các thuộc tính `x`, `y`, và `z` (các thành viên thể hiện) trên chính nó, chứ không hề có phương thức `toString()`:

```js
Object.hasOwn(anotherPoint,"x");                       // true
Object.hasOwn(anotherPoint,"y");                       // true
Object.hasOwn(anotherPoint,"z");                       // true

Object.hasOwn(anotherPoint,"toString");                // false
```

Phương thức `toString()` đó nằm ở đâu? Trên đối tượng prototype:

```js
Object.hasOwn(Point3d.prototype,"toString");    // true
```

Và `anotherPoint` có quyền truy cập vào phương thức đó thông qua liên kết `[[Prototype]]` của nó (xem Chương 2). Nói cách khác, các đối tượng prototype **chia sẻ quyền truy cập** vào (các) phương thức của chúng với (các) lớp con và (các) thể hiện. (Các) phương thức vẫn nằm yên tại chỗ, chứ không hề được sao chép xuống chuỗi kế thừa.

Dù cú pháp `class` có đẹp đẽ đến đâu, đừng quên những gì thực sự đang diễn ra bên dưới lớp cú pháp: JS *chỉ* đang liên kết các đối tượng với nhau dọc theo một chuỗi `[[Prototype]]`.

## Hành Vi Lớp Tĩnh (Static Class Behavior)

Cho đến nay chúng ta đã nhấn mạnh hai vị trí khác nhau để dữ liệu hoặc hành vi (phương thức) cư trú: trên prototype của hàm khởi tạo, hoặc trên thể hiện. Nhưng có một lựa chọn thứ ba: trên chính bản thân hàm khởi tạo (đối tượng hàm).

Trong một hệ thống hướng lớp truyền thống, các phương thức được định nghĩa trên một lớp không phải là những thứ cụ thể mà bạn có thể gọi hoặc tương tác. Bạn phải khởi tạo một lớp để có được một đối tượng cụ thể nhằm gọi các phương thức đó. Các ngôn ngữ dựa trên nguyên mẫu như JS làm mờ ranh giới này đi một chút: tất cả các phương thức do lớp định nghĩa đều là những hàm "thực sự" nằm trên prototype của hàm khởi tạo, và do đó bạn có thể gọi chúng. Nhưng như tôi đã khẳng định trước đó, bạn thực sự *không nên* làm như vậy, vì đây không phải là cách JS giả định bạn sẽ viết `class` của mình, và có một số hành vi trường hợp biên kỳ lạ mà bạn có thể gặp phải. Tốt nhất là nên đi trên con đường hẹp mà `class` đã vạch ra cho bạn.

Không phải tất cả hành vi mà chúng ta định nghĩa và muốn liên kết/tổ chức cùng với một lớp đều *cần* phải nhận biết được một thể hiện. Hơn nữa, đôi khi một lớp cần định nghĩa công khai dữ liệu (như các hằng số) mà các lập trình viên sử dụng lớp đó cần truy cập, độc lập với bất kỳ thể hiện nào họ có thể đã tạo ra hay chưa.

Vậy, một hệ thống lớp làm thế nào để cho phép định nghĩa dữ liệu và hành vi có sẵn trong một lớp nhưng độc lập với (không nhận biết) các đối tượng được khởi tạo? **Các thuộc tính và hàm tĩnh** (Static properties and functions).

| GHI CHÚ: |
| :--- |
| Tôi sẽ sử dụng "thuộc tính tĩnh" (static property) / "hàm tĩnh" (static function), thay vì "thành viên" (member) / "phương thức" (method), để phân biệt rõ ràng hơn giữa các thành viên gắn với thể hiện / các phương thức nhận biết thể hiện, và các thuộc tính không thuộc thể hiện và các hàm không nhận biết thể hiện. |

Chúng ta sử dụng từ khóa `static` trong phần thân `class` để phân biệt các định nghĩa này:

```js
class Point2d {
    // các phần tĩnh của lớp
    static origin = new Point2d(0,0)
    static distance(point1,point2) {
        return Math.sqrt(
            ((point2.x - point1.x) ** 2) +
            ((point2.y - point1.y) ** 2)
        );
    }

    // các thành viên và phương thức của thể hiện
    x
    y
    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
    toString() {
        return `(${this.x},${this.y})`;
    }
}

console.log(`Starting point: ${Point2d.origin}`);
// Starting point: (0,0)

var next = new Point2d(3,4);
console.log(`Next point: ${next}`);
// Next point: (3,4)

console.log(`Distance: ${
    Point2d.distance( Point2d.origin, next )
}`);
// Distance: 5
```

`Point2d.origin` là một thuộc tính tĩnh, tình cờ chứa một thể hiện đã được khởi tạo của lớp chúng ta. Và `Point2d.distance(..)` là một hàm tĩnh tính toán khoảng cách tọa độ Đề-các hai chiều giữa hai điểm.

Dĩ nhiên, chúng ta có thể đặt hai thứ này ở nơi khác thay vì đặt làm các phần tử `static` trên định nghĩa lớp. Nhưng vì chúng liên quan trực tiếp đến lớp `Point2d`, nên việc tổ chức chúng ở đó là *hợp lý nhất*.

| GHI CHÚ: |
| :--- |
| Đừng quên rằng khi bạn sử dụng cú pháp `class`, cái tên `Point2d` thực chất là tên của một hàm khởi tạo mà JS định nghĩa. Vì vậy `Point2d.origin` chỉ là một truy cập thuộc tính thông thường trên đối tượng hàm đó. Đó là điều tôi muốn nói ở đầu phần này khi đề cập đến vị trí thứ ba để lưu trữ các *thứ* liên quan đến lớp; trong JS, các phần tử `static` được lưu trữ dưới dạng các thuộc tính trên hàm khởi tạo. Hãy cẩn thận đừng nhầm lẫn chúng với các thuộc tính được lưu trữ trên `prototype` của hàm khởi tạo (phương thức) và các thuộc tính được lưu trữ trên thể hiện (thành viên). |

### Khởi Tạo Thuộc Tính Tĩnh (Static Property Initializations)

Giá trị trong một phép khởi tạo tĩnh (`static whatever = ..`) có thể bao gồm các tham chiếu `this`, vốn tham chiếu đến chính bản thân lớp (thực tế là hàm khởi tạo) thay vì tham chiếu đến một thể hiện:

```js
class Point2d {
    // các phần tĩnh của lớp
    static originX = 0
    static originY = 0
    static origin = new this(this.originX,this.originY)

    // ..
}
```

| CẢNH BÁO: |
| :--- |
| Tôi không khuyến khích bạn thực sự áp dụng mẹo `new this(..)` mà tôi đã minh họa ở đây. Đoạn mã đó chỉ nhằm mục đích minh họa. Mã nguồn sẽ dễ đọc hơn nhiều với `new Point2d(this.originX,this.originY)`, vì vậy hãy ưu tiên cách tiếp cận đó. |

Một chi tiết quan trọng không nên bỏ qua: không giống như các phép khởi tạo trường công khai chỉ diễn ra một khi quá trình khởi tạo (bằng `new`) xảy ra, các phép khởi tạo tĩnh của lớp luôn chạy *ngay lập tức* sau khi `class` được định nghĩa. Hơn nữa, thứ tự của các phép khởi tạo tĩnh rất quan trọng; bạn có thể hình dung các câu lệnh như thể chúng đang được đánh giá từng câu một.

Cũng giống như các thành viên của lớp, các thuộc tính tĩnh không bắt buộc phải được khởi tạo giá trị (mặc định: `undefined`), nhưng việc khởi tạo chúng phổ biến hơn nhiều. Việc khai báo một thuộc tính tĩnh không có giá trị khởi tạo (`static whatever`) không mang lại nhiều tác dụng; Truy cập `Point2d.whatever` hay `Point2d.nonExistent` đều sẽ cho kết quả là `undefined`.

Gần đây (trong ES2022), từ khóa `static` đã được mở rộng để nó có thể định nghĩa một khối bên trong thân `class` cho việc khởi tạo phức tạp hơn của các phần tử `static`:

```js
class Point2d {
    // các phần tĩnh của lớp
    static origin = new Point2d(0,0)
    static distance(point1,point2) {
        return Math.sqrt(
            ((point2.x - point1.x) ** 2) +
            ((point2.y - point1.y) ** 2)
        );
    }

    // khối khởi tạo tĩnh (kể từ ES2022)
    static {
        let outerPoint = new Point2d(6,8);
        this.maxDistance = this.distance(
            this.origin,
            outerPoint
        );
    }

    // ..
}

Point2d.maxDistance;        // 10
```

`let outerPoint = ..` ở đây không phải là một tính năng `class` đặc biệt; nó hoàn toàn giống như một khai báo `let` bình thường trong bất kỳ khối phạm vi thông thường nào (xem cuốn sách "Scope & Closures" thuộc bộ sách này). Chúng ta chỉ đơn thuần khai báo một thể hiện cục bộ của `Point2d` gán cho `outerPoint`, sau đó sử dụng giá trị đó để tính toán phép gán cho thuộc tính tĩnh `maxDistance`.

Các khối khởi tạo tĩnh cũng rất hữu ích cho những việc như câu lệnh `try..catch` bao quanh các phép tính toán biểu thức.

### Kế Thừa Tĩnh (Static Inheritance)

Các phần tử tĩnh của lớp được kế thừa bởi các lớp con (rõ ràng rồi, dưới dạng các phần tử tĩnh!), có thể bị ghi đè, và `super` có thể được dùng cho các tham chiếu lớp cơ sở (và tính đa hình hàm tĩnh), tất cả đều hoạt động theo cách gần như tương tự như cách kế thừa hoạt động với các thành viên/phương thức của thể hiện:

```js
class Point2d {
    static origin = /* .. */
    static distance(x,y) { /* .. */ }

    static {
        // ..
        this.maxDistance = /* .. */;
    }

    // ..
}

class Point3d extends Point2d {
    // các phần tĩnh của lớp
    static origin = new Point3d(
        // ở đây, các tham chiếu `this.origin` sẽ không
        // hoạt động (tự tham chiếu), vì vậy chúng ta
        // sử dụng tham chiếu `super.origin` thay thế
        super.origin.x, super.origin.y, 0
    )
    static distance(point1,point2) {
        // ở đây, super.distance(..) là Point2d.distance(..),
        // nếu chúng ta cần gọi nó

        return Math.sqrt(
            ((point2.x - point1.x) ** 2) +
            ((point2.y - point1.y) ** 2) +
            ((point2.z - point1.z) ** 2)
        );
    }

    // các thành viên/phương thức thể hiện
    z
    constructor(x,y,z) {
        super(x,y);     // <-- đừng quên dòng này!
        this.z = z;
    }
    toString() {
        return `(${this.x},${this.y},${this.z})`;
    }
}

Point2d.maxDistance;        // 10
Point3d.maxDistance;        // 10
```

Như bạn có thể thấy, thuộc tính tĩnh `maxDistance` mà chúng ta định nghĩa trên `Point2d` đã được kế thừa như một thuộc tính tĩnh trên `Point3d`.

| MẸO: |
| :--- |
| Hãy nhớ rằng: bất cứ khi nào bạn định nghĩa một hàm khởi tạo lớp con, bạn sẽ cần gọi `super(..)` trong đó, thường là câu lệnh đầu tiên. Tôi thấy điều này cực kỳ dễ bị quên lãng. |

Đừng bỏ qua hành vi cơ bản của JS ở đây. Giống như kế thừa phương thức đã thảo luận trước đó, "kế thừa" tĩnh *không phải* là việc sao chép các thuộc tính/hàm tĩnh này từ lớp cơ sở sang lớp con; đó là sự chia sẻ thông qua chuỗi `[[Prototype]]`. Cụ thể, hàm khởi tạo `Point3d()` đã được JS thay đổi liên kết `[[Prototype]]` (từ mặc định là `Function.prototype`) thành `Point2d`, đó là điều cho phép `Point3d.maxDistance` ủy quyền cho `Point2d.maxDistance`.

Điều thú vị nữa, có lẽ bây giờ chỉ mang ý nghĩa lịch sử, cần lưu ý rằng kế thừa tĩnh -- vốn là một phần của bộ tính năng cơ chế `class` ban đầu của ES6! -- là một tính năng cụ thể vượt ra ngoài mức "chỉ là đường cú pháp". Kế thừa tĩnh, như chúng ta thấy minh họa ở đây, *không* thể đạt được/mô phỏng trong JS trước ES6, theo phong cách mã lớp nguyên mẫu cũ. Đó là một hành vi mới đặc biệt chỉ được giới thiệu kể từ ES6.

## Hành Vi Lớp Riêng Tư (Private Class Behavior)

Mọi thứ chúng ta đã thảo luận cho đến nay như một phần của định nghĩa `class` đều hiển thị/truy cập công khai, hoặc dưới dạng các thuộc tính/hàm tĩnh trên lớp, các phương thức trên `prototype` của hàm khởi tạo, hoặc các thuộc tính thành viên trên thể hiện.

Nhưng làm thế nào để bạn lưu trữ thông tin không thể nhìn thấy từ bên ngoài lớp? Đây từng là một trong những tính năng được yêu cầu nhiều nhất và là lời phàn nàn lớn nhất đối với `class` của JS, cho đến khi nó cuối cùng được giải quyết trong ES2022.

`class` hiện hỗ trợ cú pháp mới để khai báo các trường riêng tư (thành viên thể hiện) và các phương thức riêng tư. Ngoài ra, các thuộc tính/hàm tĩnh riêng tư cũng hoàn toàn khả thi.

### Động Lực? (Motivation?)

Trước khi chúng ta minh họa cách triển khai các phần tử riêng tư trong `class`, điều đáng suy ngẫm là tại sao đây lại là một tính năng hữu ích?

Với các mẫu thiết kế hướng closure (một lần nữa, xem cuốn sách "Scope & Closures" thuộc bộ sách này), chúng ta tự động có sẵn tính "riêng tư" được tích hợp sẵn. Khi bạn khai báo một biến bên trong một phạm vi, nó không thể được nhìn thấy bên ngoài phạm vi đó. Chấm hết. Giảm phạm vi hiển thị của một khai báo là điều hữu ích trong việc ngăn ngừa xung đột không gian tên (các biến trùng tên).

Nhưng điều quan trọng hơn nữa là đảm bảo thiết kế "phòng thủ" đúng đắn cho phần mềm, cái gọi là "Nguyên tắc Đặc quyền Tối thiểu" (Principle of Least Privilege - POLP) [^POLP]. POLP quy định rằng chúng ta chỉ nên phơi bày một phần thông tin hoặc khả năng trong phần mềm của mình ra phạm vi bề mặt nhỏ nhất cần thiết.

Việc để lộ quá mức sẽ khiến phần mềm của chúng ta gặp phải một số vấn đề làm phức tạp thêm việc bảo mật/bảo trì phần mềm, bao gồm việc một đoạn mã khác hành động độc hại để thực hiện điều gì đó mà mã của chúng ta không mong đợi hoặc không chủ ý. Hơn nữa, còn có mối lo ngại ít nghiêm trọng hơn nhưng cũng không kém phần rắc rối là các phần khác của phần mềm phụ thuộc vào (sử dụng) các phần mã mà đáng lẽ chúng ta phải giữ kín như một chi tiết triển khai nội bộ bị ẩn giấu. Một khi mã khác đã dựa vào các chi tiết triển khai của mã chúng ta, chúng ta sẽ không thể tái cấu trúc (refactor) mã của mình mà không có nguy cơ phá vỡ các phần khác của chương trình.

Vì vậy, tóm lại, chúng ta *nên* ẩn giấu các chi tiết triển khai nếu chúng không cần thiết phải phơi bày. Theo nghĩa này, hệ thống `class` của JS có cảm giác hơi quá dễ dãi ở chỗ mọi thứ mặc định đều là công khai. Các tính năng lớp riêng tư là một sự bổ sung đáng hoan nghênh cho thiết kế phần mềm đúng đắn hơn.

#### Quá Riêng Tư? (Too Private?)

Nói đi thì cũng phải nói lại, tôi phải giội một gáo nước lạnh vào bữa tiệc lớp riêng tư này.

Tôi đã gợi ý mạnh mẽ rằng bạn chỉ nên sử dụng `class` nếu bạn thực sự định tận dụng hầu hết hoặc tất cả những gì lập trình hướng lớp mang lại cho bạn. Nếu không, bạn sẽ thích hợp hơn khi sử dụng các tính năng trụ cột cốt lõi khác của JS để tổ chức mã nguồn, chẳng hạn như với mẫu closure.

Một trong những khía cạnh quan trọng nhất của lập trình hướng lớp là kế thừa lớp con, như chúng ta đã thấy minh họa vô số lần từ đầu chương này đến giờ. Thử đoán xem điều gì sẽ xảy ra với một thành viên/phương thức riêng tư trong lớp cơ sở, khi nó được kế thừa bởi một lớp con?

Các thành viên/phương thức riêng tư là riêng tư **chỉ đối với lớp mà chúng được định nghĩa**, và **không** được kế thừa theo bất kỳ cách nào bởi một lớp con. Ôi thôi rồi.

Điều đó thoạt nghe có vẻ không phải là mối bận tâm quá lớn, cho đến khi bạn bắt đầu làm việc với `class` và các thành viên/phương thức riêng tư trong phần mềm thực tế. Bạn có thể nhanh chóng vấp phải tình huống cần truy cập một phương thức riêng tư, hoặc thậm chí thường xuyên hơn là chỉ một thành viên riêng tư, từ lớp con, để lớp con có thể mở rộng/tăng cường hành vi của lớp cơ sở theo mong muốn. Và bạn có thể sẽ hét lên trong bực bội khá nhanh một khi nhận ra điều này là không thể.

Điều xảy ra tiếp theo chắc chắn là một quyết định vụng về: liệu bạn có quay lại biến nó thành công khai, để lớp con có thể truy cập được? Ngao ngán thật. Hoặc, tệ hơn, bạn có cố gắng tái thiết kế lại lớp cơ sở để uốn éo cấu trúc của các thành viên/phương thức của nó, sao cho có thể phần nào khắc phục được sự thiếu hụt quyền truy cập này? Điều đó thường kéo theo việc tham số hóa quá mức (với các biến riêng tư làm giá trị tham số mặc định) một cách kiệt sức cho các phương thức, và những mẹo tương tự khác. Càng ngao ngán hơn gấp đôi.

Thành thật mà nói, không có câu trả lời nào thực sự thỏa đáng ở đây. Nếu bạn có kinh nghiệm với lập trình hướng lớp trong các ngôn ngữ lớp truyền thống hơn như Java hoặc C++, bạn có thể sẽ hoài nghi tại sao chúng ta không có phạm vi hiển thị *protected* (được bảo vệ) nằm ở giữa *public* và *private*. Đó chính xác là mục đích của *protected*: giữ cho thứ gì đó là riêng tư đối với một lớp VÀ bất kỳ lớp con nào của nó. Các ngôn ngữ đó cũng có các tính năng *friend* (bạn bè), nhưng điều đó nằm ngoài phạm vi thảo luận của chúng ta ở đây.

Đáng buồn thay, JS không chỉ không có phạm vi hiển thị *protected*, mà dường như (ngay cả khi nó hữu ích đến thế!) nó khó có khả năng trở thành một tính năng của JS. Nó đã được thảo luận rất chi tiết trong hơn một thập kỷ (ngay cả trước khi ES6 xuất hiện), và đã có nhiều đề xuất cho nó.

Tôi không nên nói rằng nó sẽ *không bao giờ* xảy ra, bởi vì đó không phải là lập luận chắc chắn trong bất kỳ phần mềm nào. Nhưng nó rất khó xảy ra, bởi vì nó thực sự phản bội lại chính trụ cột mà `class` được xây dựng dựa trên đó. Nếu bạn tò mò, hoặc (có khả năng hơn) tin chắc rằng *chắc chắn phải có cách nào đó*, tôi sẽ đề cập đến sự không tương thích của phạm vi truy cập *protected* bên trong các cơ chế của JS trong một phần phụ lục.

Điểm mấu chốt ở đây là, tính đến thời điểm hiện tại, JS không có phạm vi hiển thị *protected*, và nó sẽ không có trong tương lai gần. Và phạm vi hiển thị *protected* trong thực tế hữu ích hơn nhiều so với phạm vi hiển thị *private*.

Vì vậy, chúng ta quay trở lại câu hỏi: **Tại sao bạn lại muốn bận tâm đến việc làm cho bất kỳ nội dung nào của `class` trở nên riêng tư?**

Nếu thành thật mà nói: có lẽ bạn không nên. Hoặc có lẽ bạn nên. Điều đó tùy thuộc vào bạn. Chỉ cần bước vào đó với sự hiểu biết đầy đủ về những rào cản này.

### Các Thành Viên/Phương Thức Riêng Tư (Private Members/Methods)

Bạn đang rất hào hứng muốn thấy cú pháp cho phạm vi truy cập *private* kỳ diệu này rồi đúng không? Xin đừng trút giận lên người đưa tin nếu bạn cảm thấy tức giận hay buồn bã trước những gì bạn sắp chứng kiến.

```js
class Point2d {
    // các phần tĩnh
    static samePoint(point1,point2) {
        return point1.#ID === point2.#ID;
    }

    // các phần riêng tư
    #ID = null
    #assignID() {
        this.#ID = Math.round(Math.random() * 1e9);
    }

    // các phần công khai
    x
    y
    constructor(x,y) {
        this.#assignID();
        this.x = x;
        this.y = y;
    }
}

var one = new Point2d(3,4);
var two = new Point2d(3,4);

Point2d.samePoint(one,two);         // false
Point2d.samePoint(one,one);         // true
```

Không, JS đã không làm điều hợp lẽ thường là đưa vào từ khóa `private` như cách họ đã làm với `static`. Thay vào đó, họ đưa vào ký tự `#`. (hãy chèn một câu nói đùa dở tệ về việc thế hệ millennial trên mạng xã hội thích các hashtag, hay đại loại thế)

| MẸO: |
| :--- |
| Và vâng, có hàng triệu cuộc thảo luận về lý do tại sao lại không. Tôi có thể dành hàng chương sách để kể lại toàn bộ lịch sử đó, nhưng thành thật mà nói tôi chẳng buồn bận tâm. Tôi thấy cú pháp này xấu xí, và nhiều người khác cũng vậy. Nhưng một số người lại thích nó! Nếu bạn thuộc nhóm thứ hai, mặc dù tôi hiếm khi làm điều như thế này, tôi chỉ muốn nói: **hãy chấp nhận nó đi**. Đã quá muộn cho bất kỳ sự tranh luận hay thỉnh cầu nào nữa rồi. |

Cú pháp `#whatever` (bao gồm cả dạng `this.#whatever`) chỉ hợp lệ bên trong phần thân `class`. Nó sẽ ném ra các lỗi cú pháp nếu được sử dụng bên ngoài một `class`.

Không giống như các trường công khai/thành viên thể hiện, các trường riêng tư/thành viên thể hiện riêng tư *bắt buộc* phải được khai báo trong phần thân `class`. Bạn không thể thêm một thành viên riêng tư vào khai báo lớp một cách linh động khi đang ở trong phương thức constructor; các phép gán kiểu `this.#whatever = ..` chỉ hoạt động nếu trường riêng tư `#whatever` đã được khai báo trong thân lớp. Hơn nữa, mặc dù các trường riêng tư có thể được gán lại giá trị, chúng không thể bị xóa bỏ bằng toán tử `delete` khỏi một thể hiện, theo cách mà một trường công khai/thành viên lớp có thể làm.

#### Kế Thừa Lớp Con + Phần Tử Riêng Tư (Subclassing + Privates)

Tôi đã cảnh báo trước đó rằng việc tạo lớp con với các lớp có các thành viên/phương thức riêng tư có thể là một cái bẫy hạn chế. Nhưng điều đó không có nghĩa là chúng không thể được sử dụng cùng nhau.

Bởi vì "kế thừa" trong JS là sự chia sẻ (thông qua chuỗi `[[Prototype]]`), nếu bạn gọi một phương thức được kế thừa trong một lớp con, và phương thức kế thừa đó lần lượt truy cập/gọi các phần tử riêng tư trong lớp chủ (lớp cơ sở) của nó, thì điều này vẫn hoạt động bình thường:

```js
class Point2d { /* .. */ }

class Point3d extends Point2d {
    z
    constructor(x,y,z) {
        super(x,y);
        this.z = z;
    }
}

var one = new Point3d(3,4,5);
```

Lời gọi `super(x,y)` trong hàm khởi tạo này kích hoạt hàm khởi tạo lớp cơ sở được kế thừa (`Point2d(..)`), bản thân nó lại truy cập phương thức riêng tư `#assignID()` của `Point2d` (xem đoạn mã trước). Không có ngoại lệ nào bị ném ra, mặc dù `Point3d` không thể trực tiếp nhìn thấy hoặc truy cập các phần tử riêng tư `#ID` / `#assignID()` vốn thực sự được lưu trữ trên thể hiện (ở đây có tên là `one`).

Trên thực tế, ngay cả hàm tĩnh kế thừa `static samePoint(..)` cũng sẽ hoạt động từ cả `Point3d` hoặc `Point2d`:

```js
Point2d.samePoint(one,one);         // true
Point3d.samePoint(one,one);         // true
```

Thực ra, điều đó không có gì đáng ngạc nhiên, bởi vì:

```js
Point2d.samePoint === Point3d.samePoint;
```

Tham chiếu hàm được kế thừa *chính xác là cùng một hàm* với tham chiếu hàm cơ sở; nó không phải là một bản sao nào đó của hàm. Bởi vì hàm đang được xét không có tham chiếu `this` bên trong nó, nên bất kể nó được gọi từ đâu, nó đều sẽ tạo ra cùng một kết quả.

Tuy nhiên, vẫn thật đáng tiếc khi `Point3d` không có cách nào để truy cập/gây ảnh hưởng, hay thậm chí thực sự nhận biết được, các phần tử riêng tư `#ID` / `#assignID()` từ `Point2d`:

```js
class Point2d { /* .. */ }

class Point3d extends Point2d {
    z
    constructor(x,y,z) {
        super(x,y);
        this.z = z;

        console.log(this.#ID);      // sẽ ném ra lỗi!
    }
}
```

| CẢNH BÁO: |
| :--- |
| Lưu ý rằng đoạn mã này ném ra một lỗi cú pháp tĩnh sớm (early static syntax error) tại thời điểm định nghĩa lớp `Point3d`, trước khi có cơ hội tạo ra một thể hiện của lớp. Ngoại lệ tương tự cũng sẽ bị ném ra nếu tham chiếu là `super.#ID` thay vì `this.#ID`. |

#### Kiểm Tra Sự Tồn Tại (Existence Check)

Hãy nhớ rằng chỉ có bản thân `class` mới biết về, và do đó có thể kiểm tra xem trường/phương thức riêng tư đó có tồn tại hay không.

Bạn có thể muốn kiểm tra xem một trường/phương thức riêng tư có tồn tại trên một thể hiện đối tượng hay không. Ví dụ (như trình bày dưới đây), bạn có thể có một hàm tĩnh hoặc phương thức trong một lớp, nhận vào một tham chiếu đối tượng bên ngoài được truyền vào. Để kiểm tra xem tham chiếu đối tượng được truyền vào có thuộc cùng lớp này hay không (và do đó có các thành viên/phương thức riêng tư tương tự trong đó hay không), về cơ bản bạn cần thực hiện một phép "kiểm tra nhãn hiệu" (brand check) đối với đối tượng đó.

Một phép kiểm tra như vậy có thể khá rườm rà, bởi vì nếu bạn truy cập một trường riêng tư vốn không tồn tại trên đối tượng, bạn sẽ nhận được một ngoại lệ JS bị ném ra, đòi hỏi logic `try..catch` xấu xí.

Nhưng có một cách tiếp cận gọn gàng hơn, được gọi là "kiểm tra nhãn hiệu thuận tiện" (ergonomic brand check), sử dụng từ khóa `in`:

```js
class Point2d {
    // các phần tĩnh
    static samePoint(point1,point2) {
        // "kiểm tra nhãn hiệu thuận tiện" (ergonomic brand checks)
        if (#ID in point1 && #ID in point2) {
            return point1.#ID === point2.#ID;
        }
        return false;
    }

    // các phần riêng tư
    #ID = null
    #assignID() {
        this.#ID = Math.round(Math.random() * 1e9);
    }

    // các phần công khai
    x
    y
    constructor(x,y) {
        this.#assignID();
        this.x = x;
        this.y = y;
    }
}

var one = new Point2d(3,4);
var two = new Point2d(3,4);

Point2d.samePoint(one,two);         // false
Point2d.samePoint(one,one);         // true
```

Phép kiểm tra `#privateField in someObject` sẽ không ném ra ngoại lệ nếu trường đó không được tìm thấy, vì vậy nó an toàn để sử dụng mà không cần `try..catch` và cho ra kết quả boolean đơn giản.

#### Thoát Ra Bên Ngoài (Exfiltration)

Mặc dù một thành viên/phương thức có thể được khai báo với phạm vi hiển thị *private*, nó vẫn có thể bị trích xuất/thoát ra bên ngoài (exfiltrated) từ một thể hiện lớp:

```js
var id, func;

class Point2d {
    // các phần riêng tư
    #ID = null
    #assignID() {
        this.#ID = Math.round(Math.random() * 1e9);
    }

    // các phần công khai
    x
    y
    constructor(x,y) {
        this.#assignID();
        this.x = x;
        this.y = y;

        // trích xuất ra ngoài (exfiltration)
        id = this.#ID;
        func = this.#assignID;
    }
}

var point = new Point2d(3,4);

id;                     // 7392851012 (...chẳng hạn)

func;                   // function #assignID() { .. }
func.call(point,42);

func.call({},100);
// TypeError: Cannot write private member #ID to an
// object whose class did not declare it
```

Mối bận tâm chính ở đây là hãy cẩn thận khi truyền các phương thức riêng tư dưới dạng hàm gọi lại (callbacks) (hoặc phơi bày các phần tử riêng tư cho các phần khác của chương trình theo bất kỳ cách nào). Không có gì ngăn cản bạn làm như vậy, điều này có thể tạo ra sự tiết lộ tính riêng tư ngoài ý muốn.

### Phần Tử Tĩnh Riêng Tư (Private Statics)

Các thuộc tính và hàm tĩnh cũng có thể sử dụng `#` để được đánh dấu là riêng tư:

```js
class Point2d {
    static #errorMsg = "Out of bounds."
    static #printError() {
        console.log(`Error: ${this.#errorMsg}`);
    }

    // các phần công khai
    x
    y
    constructor(x,y) {
        if (x > 100 || y > 100) {
            Point2d.#printError();
        }
        this.x = x;
        this.y = y;
    }
}

var one = new Point2d(30,400);
// Error: Out of bounds.
```

Hàm tĩnh riêng tư `#printError()` ở đây có từ khóa `this`, nhưng nó đang tham chiếu đến lớp `Point2d`, chứ không phải là một thể hiện. Do đó, `#errorMsg` và `#printError()` độc lập với các thể hiện và vì vậy phù hợp nhất khi là các phần tử tĩnh. Hơn nữa, không có lý do gì để chúng có thể truy cập được từ bên ngoài lớp, nên chúng được đánh dấu là riêng tư.

Hãy nhớ rằng: các phần tử tĩnh riêng tư cũng tương tự không được kế thừa bởi các lớp con giống như các thành viên/phương thức riêng tư.

#### Bẫy Cần Chú Ý: Kế Thừa Lớp Con Với Thuộc Tính Tĩnh Riêng Tư và `this` (Gotcha: Subclassing With Static Privates and `this`)

Hãy nhớ lại rằng các phương thức được kế thừa, khi được gọi từ một lớp con, hoàn toàn không gặp vấn đề gì khi truy cập (thông qua các tham chiếu kiểu `this.#whatever`) bất kỳ phần tử riêng tư nào từ chính lớp cơ sở của chúng:

```js
class Point2d {
    // ..

    getID() {
        return this.#ID;
    }

    // ..
}

class Point3d extends Point2d {
    // ..

    printID() {
        console.log(`ID: ${this.getID()}`);
    }
}

var point = new Point3d(3,4,5);
point.printID();
// ID: ..
```

Đoạn mã đó hoạt động hoàn toàn bình thường.

Thật không may, và (đối với tôi) khá bất ngờ/thiếu nhất quán, điều tương tự lại không đúng với các phần tử tĩnh riêng tư được truy cập từ các hàm tĩnh công khai được kế thừa:

```js
class Point2d {
    static #errorMsg = "Out of bounds."
    static printError() {
        console.log(`Error: ${this.#errorMsg}`);
    }

    // ..
}

class Point3d extends Point2d {
    // ..
}

Point2d.printError();
// Error: Out of bounds.

Point3d.printError === Point2d.printError;
// true

Point3d.printError();
// TypeError: Cannot read private member #errorMsg
// from an object whose class did not declare it
```

Hàm tĩnh `printError()` được kế thừa (chia sẻ thông qua `[[Prototype]]`) từ `Point2d` sang `Point3d` hoàn toàn tốt đẹp, đó là lý do tại sao các tham chiếu hàm là giống hệt nhau. Giống như đoạn mã không chứa hàm tĩnh ở ngay phía trên, bạn có thể đã kỳ vọng rằng lời gọi tĩnh `Point3d.printError()` sẽ phân giải thông qua chuỗi `[[Prototype]]` về vị trí lớp cơ sở ban đầu của nó (`Point2d`), từ đó cho phép nó truy cập phần tử tĩnh riêng tư `#errorMsg` của lớp cơ sở.

Nhưng nó thất bại, như được thể hiện ở câu lệnh cuối cùng trong đoạn mã đó. Lý do nó thất bại ở đây, nhưng lại không thất bại ở đoạn mã trước, là một vòng xoắn não phức tạp. Tôi sẽ không đào sâu vào lời giải thích *tại sao* ở đây, thẳng thắn mà nói là vì giải thích chuyện đó khiến tôi sôi máu.

Tuy nhiên, có một *cách khắc phục*. Trong hàm tĩnh, thay vì dùng `this.#errorMsg`, hãy đổi thành `Point2d.#errorMsg`, và giờ thì nó hoạt động:

```js
class Point2d {
    static #errorMsg = "Out of bounds."
    static printError() {
        // tham chiếu đã được sửa vvvvvv
        console.log(`Error: ${Point2d.#errorMsg}`);
    }

    // ..
}

class Point3d extends Point2d {
    // ..
}

Point2d.printError();
// Error: Out of bounds.

Point3d.printError();
// Error: Out of bounds.  <-- phù, giờ thì nó hoạt động rồi!
```

Nếu các hàm tĩnh công khai đang được kế thừa, hãy sử dụng tên lớp để truy cập bất kỳ phần tử tĩnh riêng tư nào thay vì sử dụng các tham chiếu `this.`. Hãy coi chừng cạm bẫy này!

## Ví Dụ Về Lớp (Class Example)

Được rồi, chúng ta đã trình bày một loạt các tính năng lớp riêng lẻ. Tôi muốn kết thúc chương này bằng cách thử minh họa một tập hợp mẫu gồm các khả năng này trong một ví dụ duy nhất bớt đơn giản/giả định hơn một chút.

```js
class CalendarItem {
    static #UNSET = Symbol("unset")
    static #isUnset(v) {
        return v === this.#UNSET;
    }
    static #error(num) {
        return this[`ERROR_${num}`];
    }
    static {
        for (let [idx,msg] of [
            "ID is already set.",
            "ID is unset.",
            "Don't instantiate 'CalendarItem' directly.",
        ].entries()) {
            this[`ERROR_${(idx+1)*100}`] = msg;
        }
    }
    static isSameItem(item1,item2) {
        if (#ID in item1 && #ID in item2) {
            return item1.#ID === item2.#ID;
        }
        else {
            return false;
        }
    }

    #ID = CalendarItem.#UNSET
    #setID(id) {
        if (CalendarItem.#isUnset(this.#ID)) {
            this.#ID = id;
        }
        else {
            throw new Error(CalendarItem.#error(100));
        }
    }

    description = null
    startDateTime = null

    constructor() {
        if (new.target !== CalendarItem) {
            let id = Math.round(Math.random() * 1e9);
            this.#setID(id);
        }
        else {
            throw new Error(CalendarItem.#error(300));
        }
    }
    getID() {
        if (!CalendarItem.#isUnset(this.#ID)) {
            return this.#ID;
        }
        else {
            throw new Error(CalendarItem.#error(200));
        }
    }
    getDateTimeStr() {
        if (this.startDateTime instanceof Date) {
            return this.startDateTime.toUTCString();
        }
    }
    summary() {
        console.log(`(${
            this.getID()
        }) ${
            this.description
        } at ${
            this.getDateTimeStr()
        }`);
    }
}

class Reminder extends CalendarItem {
    #complete = false;  // <-- không có ASI, bắt buộc cần dấu chấm phẩy

    [Symbol.toStringTag] = "Reminder"
    constructor(description,startDateTime) {
        super();

        this.description = description;
        this.startDateTime = startDateTime;
    }
    isComplete() {
        return !!this.#complete;
    }
    markComplete() {
        this.#complete = true;
    }
    summary() {
        if (this.isComplete()) {
            console.log(`(${this.getID()}) Complete.`);
        }
        else {
            super.summary();
        }
    }
}

class Meeting extends CalendarItem {
    #getEndDateTimeStr() {
        if (this.endDateTime instanceof Date) {
            return this.endDateTime.toUTCString();
        }
    }

    endDateTime = null;  // <-- không có ASI, bắt buộc cần dấu chấm phẩy

    [Symbol.toStringTag] = "Meeting"
    constructor(description,startDateTime,endDateTime) {
        super();

        this.description = description;
        this.startDateTime = startDateTime;
        this.endDateTime = endDateTime;
    }
    getDateTimeStr() {
        return `${
            super.getDateTimeStr()
        } - ${
            this.#getEndDateTimeStr()
        }`;
    }
}
```

Hãy dành chút thời gian để đọc và nghiền ngẫm những định nghĩa `class` đó. Bạn có nhận ra hầu hết các tính năng `class` mà chúng ta đã thảo luận trong chương này không?

| GHI CHÚ: |
| :--- |
| Một câu hỏi bạn có thể thắc mắc: tại sao tôi không chuyển phần logic lặp lại về việc thiết lập `description` và `startDateTime` từ cả hai hàm khởi tạo của lớp con vào trong hàm khởi tạo của lớp cơ sở duy nhất? Đây là một điểm tinh tế, nhưng ý định của tôi không phải là để `CalendarItem` bị khởi tạo trực tiếp; đó là thứ mà theo thuật ngữ hướng lớp chúng ta gọi là một "lớp trừu tượng" (abstract class). Đó là lý do tại sao tôi sử dụng `new.target` để ném ra lỗi nếu lớp `CalendarItem` bị khởi tạo trực tiếp! Vì vậy, tôi không muốn ngụ ý thông qua chữ ký hàm rằng hàm khởi tạo `CalendarItem(..)` nên được sử dụng trực tiếp. |

Bây giờ hãy xem ba lớp này hoạt động:

```js
var callMyParents = new Reminder(
    "Call my parents to say hi",
    new Date("July 7, 2022 11:00:00 UTC")
);
callMyParents.toString();
// [object Reminder]
callMyParents.summary();
// (586380912) Call my parents to say hi at
// Thu, 07 Jul 2022 11:00:00 GMT
callMyParents.markComplete();
callMyParents.summary();
// (586380912) Complete.
callMyParents instanceof Reminder;
// true
callMyParents instanceof CalendarItem;
// true
callMyParents instanceof Meeting;
// false


var interview = new Meeting(
    "Job Interview: ABC Tech",
    new Date("June 23, 2022 08:30:00 UTC"),
    new Date("June 23, 2022 09:15:00 UTC")
);
interview.toString();
// [object Meeting]
interview.summary();
// (994337604) Job Interview: ABC Tech at Thu,
// 23 Jun 2022 08:30:00 GMT - Thu, 23 Jun 2022
// 09:15:00 GMT
interview instanceof Meeting;
// true
interview instanceof CalendarItem;
// true
interview instanceof Reminder;
// false


Reminder.isSameItem(callMyParents,callMyParents);
// true
Meeting.isSameItem(callMyParents,interview);
// false
```

Phải thừa nhận rằng, một số phần trong ví dụ này hơi mang tính gượng ép. Nhưng thành thật mà nói, tôi nghĩ hầu hết tất cả những điều này đều là những cách sử dụng hợp lý và đáng tin cậy của các tính năng `class` khác nhau.

Nhân tiện, có lẽ có cả triệu cách khác nhau để cấu trúc logic đoạn mã trên. Tôi hoàn toàn không khẳng định đây là cách *đúng đắn* hay *tốt nhất* để làm điều đó. Như một bài tập dành cho người đọc, hãy thử tự tay viết lại nó và ghi chú lại những điều bạn đã làm khác với cách tiếp cận của tôi.

[^POLP]: "Principle of Least Privilege", Wikipedia; https://en.wikipedia.org/wiki/Principle_of_least_privilege ; Truy cập tháng 7 năm 2022
