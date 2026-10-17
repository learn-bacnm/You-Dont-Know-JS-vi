# You Don't Know JS Yet: Objects & Classes - Tái bản lần 2
# Chương 5: Ủy quyền (Delegation)

| GHI CHÚ: |
| :--- |
| Nội dung đang được hoàn thiện |

Chúng ta đã khám phá cặn kẽ về object, prototype, class, và giờ là từ khóa `this`. Nhưng bây giờ chúng ta sẽ xem xét lại những gì đã học từ trước đến nay từ một góc nhìn có đôi chút khác biệt.

Sẽ ra sao nếu bạn có thể tận dụng toàn bộ sức mạnh của các cơ chế object, prototype và `this` động cùng nhau mà không cần dùng đến `class` hay bất kỳ hậu duệ nào của nó?

Trên thực tế, tôi cho rằng bản chất của JS ít hướng lớp (class-oriented) hơn nhiều so với những gì từ khóa `class` thể hiện ra bên ngoài. Bởi vì JS là một ngôn ngữ động, dựa trên prototype (prototypal language), điểm mạnh thực sự của nó chính là... *sự ủy quyền* (*delegation*).

## Lời mở đầu

Trước khi bắt đầu tìm hiểu về ủy quyền, tôi muốn đưa ra một lời cảnh báo. Góc nhìn này về cơ chế `[[Prototype]]` của object và ngữ cảnh hàm `this` trong JS *không phải* là xu hướng chủ đạo (mainstream). Đây *không phải* là cách mà các tác giả framework và thư viện sử dụng JS. Theo hiểu biết của tôi, bạn sẽ không tìm thấy bất kỳ ứng dụng lớn nào ngoài kia sử dụng mẫu hình (pattern) này.

Vậy thì tại sao tôi lại dành cả một chương cho một mẫu hình như vậy, nếu nó ít phổ biến đến thế?

Câu hỏi rất hay. Câu trả lời dí dỏm là: vì đây là sách của tôi và tôi có thể làm những gì mình thích!

Nhưng câu trả lời sâu xa hơn là, vì tôi nghĩ rằng việc phát triển sự hiểu biết *này* về một trong những trụ cột cốt lõi của ngôn ngữ sẽ giúp ích cho bạn *ngay cả khi* tất cả những gì bạn làm sau này chỉ là dùng các mẫu hình JS theo phong cách `class`.

Để làm rõ, ủy quyền không phải là phát minh của tôi. Nó đã tồn tại như một mẫu thiết kế (design pattern) trong nhiều thập kỷ. Và trong một thời gian dài, các lập trình viên đã tranh luận rằng sự ủy quyền qua prototype (prototypal delegation) *chỉ* là hình thức động của tính kế thừa.[^TreatyOfOrlando] Nhưng tôi nghĩ việc đánh đồng hai khái niệm này là một sai lầm.[^ClassVsPrototype]

Đối với mục đích của chương này, tôi sẽ trình bày sự ủy quyền, được triển khai thông qua các cơ chế của JS, như một mẫu thiết kế thay thế, nằm ở đâu đó giữa hướng lớp (class-orientation) và các mẫu hình object-closure/module.

Bước đầu tiên là *giải cấu trúc* (de-construct) cơ chế `class` thành các phần riêng lẻ của nó. Sau đó chúng ta sẽ chọn lọc và kết hợp các phần lại theo một cách hơi khác biệt.

## Rốt cuộc thì Hàm khởi tạo (Constructor) là gì?

Trong Chương 3, chúng ta đã thấy `constructor(..)` là điểm khởi đầu chính cho việc khởi dựng một thể hiện (instance) của `class`. Nhưng `constructor(..)` thực chất không làm bất kỳ công việc *tạo ra* (creation) nào cả, nó chỉ thực hiện công việc *khởi tạo* (initialization). Nói cách khác, instance đã được tạo sẵn vào thời điểm `constructor(..)` chạy và khởi tạo nó -- ví dụ: các phép gán kiểu `this.whatever`.

Vậy công việc *tạo ra* thực sự diễn ra ở đâu? Trong toán tử `new`. Như phần "Kích hoạt ngữ cảnh New" trong Chương 4 đã giải thích, có bốn bước mà từ khóa `new` thực hiện; bước đầu tiên trong số đó là việc tạo ra một object rỗng mới (chính là instance). `constructor(..)` thậm chí còn chưa được gọi cho đến tận bước 3 trong toàn bộ nỗ lực của `new`.

Nhưng `new` không phải là cách duy nhất -- hay thậm chí có lẽ không phải là cách tốt nhất -- để *tạo ra* một "instance" của object. Hãy xem xét:

```js
// một "constructor" không dùng class
function Point2d(x,y) {
    // tạo một object (1)
    var instance = {};

    // khởi tạo instance (3)
    instance.x = x;
    instance.y = y;

    // trả về instance (4)
    return instance;
}

var point = Point2d(3,4);

point.x;                    // 3
point.y;                    // 4
```

Không hề có `class`, chỉ là một định nghĩa hàm thông thường (`Point2d(..)`). Không có lời gọi `new`, chỉ là một lời gọi hàm thông thường (`Point2d(3,4)`). Và cũng không có các tham chiếu `this`, chỉ là các phép gán thuộc tính object thông thường (`instance.x = ..`).

Thuật ngữ thường được dùng nhất để chỉ mẫu code này là `Point2d(..)` ở đây đóng vai trò như một *hàm xưởng* (*factory function*). Việc gọi hàm này dẫn đến việc khởi dựng (tạo và khởi tạo) một object, và trả nó về cho chúng ta. Đó là một mẫu hình cực kỳ phổ biến, ít nhất cũng phổ biến ngang ngửa với code hướng lớp.

Tôi đã chú thích `(1)`, `(3)`, và `(4)` trong đoạn code đó, chúng tương ứng một cách đại khái với các bước 1, 3, và 4 của thao tác `new`. Nhưng bước 2 ở đâu?

Nếu bạn còn nhớ, bước 2 của `new` là liên kết object (được tạo ở bước 1) với một object khác, thông qua khe (slot) `[[Prototype]]` của nó (xem Chương 2). Vậy chúng ta có thể muốn liên kết object `instance` của mình với object nào? Chúng ta có thể liên kết nó với một object chứa các hàm mà ta muốn liên kết/sử dụng cùng với instance của mình.

Hãy sửa đổi đoạn code trước đó:

```js
var prototypeObj = {
    toString() {
        return `(${this.x},${this.y})`;
    },
}

// một "constructor" không dùng class
function Point2d(x,y) {
    // tạo một object (1)
    var instance = {
        // liên kết [[Prototype]] của instance (2)
        __proto__: prototypeObj,
    };

    // khởi tạo instance (3)
    instance.x = x;
    instance.y = y;

    // trả về instance (4)
    return instance;
}

var point = Point2d(3,4);

point.toString();           // (3,4)
```

Bây giờ bạn thấy phép gán `__proto__` đang thiết lập liên kết `[[Prototype]]` nội bộ, vốn là bước 2 còn thiếu. Tôi dùng `__proto__` ở đây chỉ nhằm mục đích minh họa; việc sử dụng `setPrototypeOf(..)` như đã trình bày trong Chương 4 cũng sẽ hoàn thành nhiệm vụ tương tự.

### Instance Factory *Mới* (*New*)

Bạn nghĩ điều gì sẽ xảy ra nếu chúng ta dùng `new` để gọi hàm `Point2d(..)` như hiển thị ở đây?

```js
var anotherPoint = new Point2d(5,6);

anotherPoint.toString(5,6);         // (5,6)
```

Khoan đã! Chuyện gì đang diễn ra ở đây? Một hàm xưởng thông thường, không phải `class`, lại được gọi với từ khóa `new`, như thể nó là một `class`. Liệu điều đó có làm thay đổi bất kỳ kết quả nào của đoạn code không?

Không... và có. `anotherPoint` ở đây hoàn toàn là cùng một object như khi tôi không dùng `new`. Nhưng! Object mà `new` tạo ra, liên kết và gán làm ngữ cảnh `this` thì sao? Object *đó* hoàn toàn bị phớt lờ và vứt bỏ, để rồi cuối cùng bị bộ thu gom rác (garbage collector) của JS dọn dẹp. Đáng tiếc là engine JS không thể đoán trước được việc bạn sẽ không dùng object mà bạn đã yêu cầu `new` tạo ra, vì vậy nó vẫn luôn được tạo ra ngay cả khi không dùng đến.

Đúng vậy! Việc dùng từ khóa `new` đối với một factory function có thể *tạo cảm giác* thuận tiện hoặc quen thuộc hơn, nhưng nó khá lãng phí, ở chỗ nó tạo ra **hai** object, và vứt bỏ một trong hai một cách uổng phí.

### Khởi tạo Factory (Factory Initialization)

Trong ví dụ code hiện tại, hàm `Point2d(..)` vẫn trông rất giống một `constructor(..)` bình thường của một định nghĩa `class`. Nhưng điều gì sẽ xảy ra nếu chúng ta chuyển code khởi tạo sang một hàm riêng biệt, chẳng hạn đặt tên là `init(..)`:

```js
var prototypeObj = {
    init(x,y) {
        // khởi tạo instance (3)
        this.x = x;
        this.y = y;
    },
    toString() {
        return `(${this.x},${this.y})`;
    },
}

// một "constructor" không dùng class
function Point2d(x,y) {
    // tạo một object (1)
    var instance = {
        // liên kết [[Prototype]] của instance (2)
        __proto__: prototypeObj,
    };

    // khởi tạo instance (3)
    instance.init(x,y);

    // trả về instance (4)
    return instance;
}

var point = Point2d(3,4);

point.toString();           // (3,4)
```

Lời gọi `instance.init(..)` tận dụng liên kết `[[Prototype]]` được thiết lập qua phép gán `__proto__`. Do đó, nó *ủy quyền* ngược lên chuỗi prototype đến `prototypeObj.init(..)`, và kích hoạt nó với ngữ cảnh `this` là `instance` -- thông qua phép gán *ngữ cảnh ngầm định* (*implicit context*) (xem Chương 4).

Hãy tiếp tục quá trình giải cấu trúc. Hãy sẵn sàng cho một cú hoán đổi bất ngờ!

```js
var Point2d = {
    init(x,y) {
        // khởi tạo instance (3)
        this.x = x;
        this.y = y;
    },
    toString() {
        return `(${this.x},${this.y})`;
    },
};
```

Khoan đã, cái gì cơ!? Tôi đã loại bỏ hàm `Point2d(..)`, và thay vào đó đổi tên `prototypeObj` thành `Point2d`. Thật kỳ lạ.

Nhưng bây giờ hãy nhìn vào phần còn lại của code:

```js
// các bước 1, 2, và 4
var point = { __proto__: Point2d, };

// bước 3
point.init(3,4);

point.toString();           // (3,4)
```

Và một bước tinh chỉnh cuối cùng: hãy sử dụng một tiện ích tích hợp sẵn mà JS cung cấp cho chúng ta, có tên là `Object.create(..)`:

```js
// các bước 1, 2, và 4
var point = Object.create(Point2d);

// bước 3
point.init(3,4);

point.toString();           // (3,4)
```

`Object.create(..)` thực hiện những thao tác nào?

1. tạo ra một object rỗng hoàn toàn mới, từ hư vô.

2. liên kết `[[Prototype]]` của object rỗng mới đó với object `.prototype` của hàm (hoặc object được truyền vào).

Nếu những điều đó trông quen thuộc, thì đó là vì chúng chính xác là hai bước đầu tiên của từ khóa `new` (xem Chương 4).

Bây giờ hãy ghép tất cả lại với nhau:

```js
var Point2d = {
    init(x,y) {
        this.x = x;
        this.y = y;
    },
    toString() {
        return `(${this.x},${this.y})`;
    },
};

var point = Object.create(Point2d);

point.init(3,4);

point.toString();           // (3,4)
```

Hmmm. Hãy dành chút thời gian để suy ngẫm về những gì đã được rút ra ở đây. Nó so với cách tiếp cận `class` thì như thế nào?

Mẫu hình này loại bỏ các từ khóa `class` và `new`, nhưng mang lại kết quả hoàn toàn tương tự. *Cái giá phải trả*? Thao tác `new` duy nhất đã được tách thành hai câu lệnh: `Object.create(Point2d)` và `point.init(3,4)`.

#### Giúp tôi tái cấu trúc với!

Nếu việc tách riêng hai thao tác đó làm phiền bạn -- liệu nó có *quá mức giải cấu trúc* không!? -- thì chúng luôn có thể được kết hợp lại trong một hàm trợ giúp xưởng (factory helper) nhỏ:

```js
function make(objType,...args) {
    var instance = Object.create(objType);
    instance.init(...args);
    return instance;
}

var point = make(Point2d,3,4);

point.toString();           // (3,4)
```

| MẸO: |
| :--- |
| Một hàm trợ giúp factory `make(..)` như vậy hoạt động tổng quát cho bất kỳ kiểu object nào, miễn là bạn tuân theo quy ước ngầm hiểu rằng mỗi `objType` bạn liên kết tới đều có một hàm tên là `init(..)` trên đó. |

Và dĩ nhiên, bạn vẫn có thể tạo bao nhiêu instance tùy thích:

```js
var point = make(Point2d,3,4);

var anotherPoint = make(Point2d,5,6);
```

## Loại bỏ Tư duy Hướng lớp (Ditching Class Thinking)

Thành thật mà nói, sự *giải cấu trúc* mà chúng ta vừa trải qua chỉ mang lại đoạn code hơi khác một chút, và có thể tốt hơn một chút hoặc tệ hơn một chút so với phong cách `class`. Nếu sự ủy quyền chỉ dừng lại ở đó, có lẽ nó thậm chí còn không đủ hữu ích cho một chú thích cuối trang, chứ đừng nói đến cả một chương sách.

Nhưng đây là lúc chúng ta sẽ thực sự bắt đầu gạt bỏ bản thân tư duy hướng lớp sang một bên, chứ không chỉ riêng cú pháp của nó.

Thiết kế hướng lớp vốn dĩ tạo ra một hệ thống phân cấp *phân loại* (*classification*), nghĩa là cách chúng ta phân chia và nhóm các đặc tính lại, rồi xếp chồng chúng theo chiều dọc trong một chuỗi kế thừa. Hơn nữa, việc định nghĩa một subclass là sự chuyên biệt hóa của base class tổng quát. Việc khởi tạo instance là sự cụ thể hóa của class tổng quát.

Hành vi trong một hệ thống phân cấp lớp truyền thống là sự hợp thành theo chiều dọc (vertical composition) qua các tầng của chuỗi kế thừa. Trong nhiều thập kỷ qua, đã có những nỗ lực, và thậm chí đôi khi trở nên khá phổ biến, nhằm làm phẳng các hệ thống phân cấp kế thừa sâu, và ưu tiên sự hợp thành theo chiều ngang (horizontal composition) hơn thông qua *mixins* và các ý tưởng liên quan.

Tôi không khẳng định rằng có điều gì sai trái với những cách tiếp cận code đó. Nhưng tôi đang nói rằng chúng không phải là cách JS hoạt động *tự nhiên*, do đó việc áp dụng chúng vào JS đã là một con đường dài, quanh co, phức tạp, và đã tích tụ thêm nhiều cú pháp sắc thái khác nhau để lắp ghép chắp vá lên trên trụ cột cốt lõi `[[Prototype]]` và `this` của JS.

Trong phần còn lại của chương này, tôi dự định loại bỏ cả cú pháp của `class` *lẫn* tư duy về *class*.

## Minh họa về Sự ủy quyền (Delegation Illustrated)

Vậy ủy quyền là về điều gì? Về cốt lõi, đó là việc hai hoặc nhiều *thực thể* (*things*) chia sẻ nỗ lực để hoàn thành một nhiệm vụ.

Thay vì định nghĩa một *thực thể* cha tổng quát `Point2d` đại diện cho hành vi dùng chung mà một tập hợp gồm một hoặc nhiều *thực thể* con `point` / `anotherPoint` kế thừa từ đó, sự ủy quyền đưa chúng ta đến việc xây dựng chương trình với các *thực thể* ngang hàng (peers) riêng biệt hợp tác với nhau.

Tôi sẽ phác thảo điều đó trong một vài đoạn code:

```js
var Coordinates = {
    setX(x) {
        this.x = x;
    },
    setY(y) {
        this.y = y;
    },
    setXY(x,y) {
        this.setX(x);
        this.setY(y);
    },
};

var Inspect = {
    toString() {
        return `(${this.x},${this.y})`;
    },
};

var point = {};

Coordinates.setXY.call(point,3,4);
Inspect.toString.call(point);         // (3,4)

var anotherPoint = Object.create(Coordinates);

anotherPoint.setXY(5,6);
Inspect.toString.call(anotherPoint);  // (5,6)
```

Hãy cùng phân tích những gì đang xảy ra ở đây.

Tôi đã định nghĩa `Coordinates` như một object cụ thể nắm giữ một số hành vi mà tôi liên kết với việc thiết lập tọa độ điểm (`x` và `y`). Tôi cũng đã định nghĩa `Inspect` như một object cụ thể nắm giữ một số logic kiểm tra gỡ lỗi, chẳng hạn như `toString()`.

Sau đó, tôi tạo thêm hai object cụ thể nữa, `point` và `anotherPoint`.

`point` không có `[[Prototype]]` cụ thể nào (mặc định: `Object.prototype`). Bằng cách sử dụng phép gán *ngữ cảnh tường minh* (*explicit context*) (xem Chương 4), tôi kích hoạt các tiện ích `Coordinates.setXY(..)` và `Inspect.toString()` trong ngữ cảnh của `point`. Đó là cái mà tôi gọi là *sự ủy quyền tường minh* (*explicit delegation*).

`anotherPoint` được liên kết `[[Prototype]]` tới `Coordinates`, chủ yếu là để thêm một chút tiện lợi. Điều đó cho phép tôi sử dụng phép gán *ngữ cảnh ngầm định* với `anotherPoint.setXY(..)`. Nhưng tôi vẫn có thể chia sẻ `anotherPoint` một cách *tường minh* làm ngữ cảnh cho lời gọi `Inspect.toString()`. Đó là cái mà tôi gọi là *sự ủy quyền ngầm định* (*implicit delegation*).

**Đừng bỏ lỡ điều *này*:** Chúng ta vẫn đạt được sự hợp thành (composition): chúng ta đã hợp thành các hành vi từ `Coordinates` và `Inspect`, trong các lời gọi hàm tại runtime cùng với việc chia sẻ ngữ cảnh `this`. Chúng ta không cần phải gộp các hành vi đó lại lúc viết code thành một `class` duy nhất (hoặc một hệ thống phân cấp `class` cha-con) để `point` / `anotherPoint` kế thừa. Tôi thích gọi sự hợp thành tại thời điểm thực thi (runtime) này là **hợp thành ảo** (**virtual composition**).

*Điểm mấu chốt* ở đây là: không có object nào trong số bốn object này là cha hay con. Tất cả chúng đều là bạn bè đồng cấp (peers) của nhau, và tất cả chúng đều có những mục đích khác nhau. Chúng ta có thể tổ chức hành vi của mình thành các phần logic (trên từng object tương ứng), và chia sẻ ngữ cảnh thông qua `this` (và tùy chọn qua liên kết `[[Prototype]]`), điều này mang lại kết quả hợp thành tương tự như các mẫu hình khác mà chúng ta đã xem xét cho đến nay trong cuốn sách.

*Đó* chính là trái tim của mẫu hình **ủy quyền** (**delegation**), theo cách mà JS thể hiện nó.

| MẸO: |
| :--- |
| Trong ấn bản đầu tiên của bộ sách này, cuốn sách này ("this & Object Prototypes") đã đặt ra một thuật ngữ, "OLOO", viết tắt của "Objects Linked to Other Objects" (Các Object Liên Kết Với Các Object Khác) -- để đối lập với "OO" ("Object Oriented" - Hướng đối tượng). Trong ví dụ trước, bạn có thể thấy bản chất của OLOO: tất cả những gì chúng ta có chỉ là các object, được liên kết và hợp tác với các object khác. Tôi thấy điều này thật đẹp trong sự đơn giản của nó. |

## Hợp thành các Object Ngang hàng (Composing Peer Objects)

Hãy đưa *sự ủy quyền này* đi xa hơn nữa.

Trong đoạn code trước, `point` và `anotherPoint` chỉ đơn thuần chứa dữ liệu, và các hành vi mà chúng ủy quyền tới nằm trên các object khác (`Coordinates` và `Inspect`). Nhưng chúng ta có thể thêm các hành vi trực tiếp vào bất kỳ object nào trong một chuỗi ủy quyền, và các hành vi đó thậm chí có thể tương tác với nhau, tất cả thông qua phép thuật của *sự hợp thành ảo* (*virtual composition*) (chia sẻ ngữ cảnh `this`).

Để minh họa, chúng ta sẽ phát triển ví dụ về *tọa độ điểm* hiện tại của mình thêm một chút. Và như một phần thưởng bổ sung, chúng ta sẽ thực sự vẽ các điểm của mình lên một phần tử `<canvas>` trong DOM. Hãy cùng xem:

```js
var Canvas = {
    setOrigin(x,y) {
        this.ctx.translate(x,y);

        // lật ngược ngữ cảnh canvas theo chiều dọc,
        // để tọa độ hoạt động giống như trên đồ thị
        // 2d (x,y) thông thường
        this.ctx.scale(1,-1);
    },
    pixel(x,y) {
        this.ctx.fillRect(x,y,1,1);
    },
    renderScene() {
        // xóa canvas
        var matrix = this.ctx.getTransform();
        this.ctx.resetTransform();
        this.ctx.clearRect(
            0, 0,
            this.ctx.canvas.width,
            this.ctx.canvas.height
        );
        this.ctx.setTransform(matrix);

        this.draw();  // <-- draw() ở đâu ra?
    },
};

var Coordinates = {
    setX(x) {
        this.x = Math.round(x);
    },
    setY(y) {
        this.y = Math.round(y);
    },
    setXY(x,y) {
        this.setX(x);
        this.setY(y);
        this.render();   // <-- render() ở đâu ra?
    },
};

var ControlPoint = {
    // ủy quyền cho Coordinates
    __proto__: Coordinates,

    // LƯU Ý: phải có phần tử <canvas id="my-canvas">
    // trong DOM
    ctx: document.getElementById("my-canvas")
        .getContext("2d"),

    rotate(angleRadians) {
        var rotatedX = this.x * Math.cos(angleRadians) -
            this.y * Math.sin(angleRadians);
        var rotatedY = this.x * Math.sin(angleRadians) +
            this.y * Math.cos(angleRadians);
        this.setXY(rotatedX,rotatedY);
    },
    draw() {
        // vẽ điểm
        Canvas.pixel.call(this,this.x,this.y);
    },
    render() {
        // xóa canvas, và vẽ lại
        // control-point của chúng ta
        Canvas.renderScene.call(this);
    },
};

// đặt gốc tọa độ logic (0,0) tại vị trí
// vật lý này trên canvas
Canvas.setOrigin.call(ControlPoint,100,100);

ControlPoint.setXY(30,40);
// [vẽ điểm (30,40) lên canvas]

// ..
// sau đó:

// xoay điểm quanh gốc tọa độ (0,0)
// 90 độ ngược chiều kim đồng hồ
ControlPoint.rotate(Math.PI / 2);
// [vẽ điểm (-40,30) lên canvas]
```

Được rồi, đó là một lượng code khá lớn để tiếp thu. Hãy dành thời gian và đọc lại đoạn code vài lần. Tôi đã thêm một vài object cụ thể mới (`Canvas` và `ControlPoint`) bên cạnh object `Coordinates` trước đó.

Hãy chắc chắn rằng bạn nhìn thấy và hiểu được các tương tác giữa ba object cụ thể này.

`ControlPoint` được liên kết (thông qua `__proto__`) để *ủy quyền ngầm định* (chuỗi `[[Prototype]]`) tới `Coordinates`.

Đây là một *sự ủy quyền tường minh*: `Canvas.setOrigin.call(ControlPoint,100,100);`; tôi đang gọi `Canvas.setOrigin(..)` trong ngữ cảnh của `ControlPoint`. Việc đó có tác dụng chia sẻ `ctx` với `setOrigin(..)`, thông qua `this`.

`ControlPoint.setXY(..)` ủy quyền *ngầm định* tới `Coordinates.setXY(..)`, nhưng vẫn nằm trong ngữ cảnh của `ControlPoint`. Đây là một chi tiết quan trọng rất dễ bị bỏ sót: bạn có thấy `this.render()` bên trong `Coordinates.setXY(..)` không? Cái đó đến từ đâu? Vì ngữ cảnh `this` là `ControlPoint` (chứ không phải `Coordinates`), nó đang gọi `ControlPoint.render()`.

`ControlPoint.render()` *ủy quyền tường minh* tới `Canvas.renderScene()`, một lần nữa vẫn nằm trong ngữ cảnh của `ControlPoint`. `renderScene()` gọi `this.draw()`, nhưng cái đó đến từ đâu? Vâng, vẫn đến từ `ControlPoint` (thông qua ngữ cảnh `this`).

Và `ControlPoint.draw()`? Nó *ủy quyền tường minh* tới `Canvas.pixel(..)`, một lần nữa vẫn trong ngữ cảnh `ControlPoint`.

Cả ba object đều có các phương thức kết thúc bằng việc gọi lẫn nhau. Nhưng những lời gọi này không hề bị gắn cứng (hard-wired). `Canvas.renderScene()` không gọi `ControlPoint.draw()`, nó gọi `this.draw()`. Điều đó rất quan trọng, vì nó có nghĩa là `Canvas.renderScene()` linh hoạt hơn để sử dụng trong một ngữ cảnh `this` khác -- ví dụ, với một loại object *điểm* khác ngoài `ControlPoint`.

Chính thông qua ngữ cảnh `this`, và chuỗi `[[Prototype]]`, mà ba object này về cơ bản được trộn lẫn (hợp thành) ảo cùng nhau, khi cần thiết ở mỗi bước, để chúng làm việc cùng nhau **như thể chúng là một object duy nhất thay vì ba object riêng biệt**.

Đó chính là *vẻ đẹp* của sự hợp thành ảo được hiện thực hóa bởi mẫu hình ủy quyền trong JS.

### Ngữ cảnh Linh hoạt (Flexible Context)

Tôi đã đề cập ở trên rằng chúng ta có thể dễ dàng thêm các object cụ thể khác vào sự kết hợp này. Dưới đây là một ví dụ:

```js
var Coordinates = { /* .. */ };

var Canvas = {
    /* .. */
    line(start,end) {
        this.ctx.beginPath();
        this.ctx.moveTo(start.x,start.y);
        this.ctx.lineTo(end.x,end.y);
        this.ctx.stroke();
    },
};

function lineAnchor(x,y) {
    var anchor = {
        __proto__: Coordinates,
        render() {},
    };
    anchor.setXY(x,y);
    return anchor;
}

var GuideLine = {
    // LƯU Ý: phải có phần tử <canvas id="my-canvas">
    // trong DOM
    ctx: document.getElementById("my-canvas")
        .getContext("2d"),

    setAnchors(sx,sy,ex,ey) {
        this.start = lineAnchor(sx,sy);
        this.end = lineAnchor(ex,ey);
        this.render();
    },
    draw() {
        // vẽ điểm
        Canvas.line.call(this,this.start,this.end);
    },
    render() {
        // xóa canvas, và vẽ lại
        // đường gióng của chúng ta
        Canvas.renderScene.call(this);
    },
};

// đặt gốc tọa độ logic (0,0) tại vị trí
// vật lý này trên canvas
Canvas.setOrigin.call(GuideLine,100,100);

GuideLine.setAnchors(-30,65,45,-17);
// [vẽ đường thẳng từ (-30,65) đến (45,-17)
//   trên canvas]
```

Điều đó khá tuyệt vời, tôi nghĩ vậy!

Nhưng tôi nghĩ một lợi ích khác ít rõ ràng hơn là việc có các object được liên kết động thông qua ngữ cảnh `this` có xu hướng làm cho việc kiểm thử độc lập các phần khác nhau của chương trình trở nên dễ dàng hơn đôi chút.

Ví dụ, `Object.setPrototypeOf(..)` có thể được sử dụng để thay đổi động liên kết `[[Prototype]]` của một object, ủy quyền nó cho một object khác chẳng hạn như một mock object. Hoặc bạn có thể định nghĩa lại một cách động `GuideLine.draw()` và `GuideLine.render()` để *ủy quyền tường minh* tới một `MockCanvas` thay vì `Canvas`.

Từ khóa `this`, và liên kết `[[Prototype]]`, là một cơ chế vô cùng linh hoạt khi bạn thấu hiểu và tận dụng chúng một cách triệt để.

## Tại sao lại là *This*? (Why *This*?)

Được rồi, hy vọng rằng đến đây bạn đã thấy rõ mẫu hình ủy quyền phụ thuộc rất nhiều vào dữ liệu đầu vào ngầm định (implicit input), chia sẻ ngữ cảnh thông qua `this` thay vì thông qua một tham số tường minh.

Bạn hoàn toàn có quyền hỏi, tại sao không luôn luôn truyền ngữ cảnh đó một cách tường minh? Chúng ta chắc chắn có thể làm như vậy, nhưng... để truyền thủ công ngữ cảnh cần thiết, chúng ta sẽ phải thay đổi hầu như mọi chữ ký hàm (function signature), và bất kỳ vị trí gọi hàm (call-site) tương ứng nào.

Hãy xem lại ví dụ ủy quyền `ControlPoint` trước đó, và triển khai nó mà không dùng bất kỳ sự chia sẻ ngữ cảnh `this` theo hướng ủy quyền nào. Hãy chú ý cẩn thận đến những điểm khác biệt:

```js
var Canvas = {
    setOrigin(ctx,x,y) {
        ctx.translate(x,y);
        ctx.scale(1,-1);
    },
    pixel(ctx,x,y) {
        ctx.fillRect(x,y,1,1);
    },
    renderScene(ctx,entity) {
        // xóa canvas
        var matrix = ctx.getTransform();
        ctx.resetTransform();
        ctx.clearRect(
            0, 0,
            ctx.canvas.width,
            ctx.canvas.height
        );
        ctx.setTransform(matrix);

        entity.draw();
    },
};

var Coordinates = {
    setX(entity,x) {
        entity.x = Math.round(x);
    },
    setY(entity,y) {
        entity.y = Math.round(y);
    },
    setXY(entity,x,y) {
        this.setX(entity,x);
        this.setY(entity,y);
        entity.render();
    },
};

var ControlPoint = {
    // LƯU Ý: phải có phần tử <canvas id="my-canvas">
    // trong DOM
    ctx: document.getElementById("my-canvas")
        .getContext("2d"),

    setXY(x,y) {
        Coordinates.setXY(this,x,y);
    },
    rotate(angleRadians) {
        var rotatedX = this.x * Math.cos(angleRadians) -
            this.y * Math.sin(angleRadians);
        var rotatedY = this.x * Math.sin(angleRadians) +
            this.y * Math.cos(angleRadians);
        this.setXY(rotatedX,rotatedY);
    },
    draw() {
        // vẽ điểm
        Canvas.pixel(this.ctx,this.x,this.y);
    },
    render() {
        // xóa canvas, và vẽ lại
        // control-point của chúng ta
        Canvas.renderScene(this.ctx,this);
    },
};

// đặt gốc tọa độ logic (0,0) tại vị trí
// vật lý này trên canvas
Canvas.setOrigin(ControlPoint.ctx,100,100);

// ..
```

Thành thật mà nói, một số người trong các bạn có thể thích phong cách code đó hơn. Và điều đó hoàn toàn ổn nếu bạn thuộc nhóm quan điểm đó. Đoạn code này tránh hoàn toàn `[[Prototype]]`, và chỉ dựa vào số ít các tham chiếu cơ bản dạng `this.` tới các thuộc tính và phương thức.

Ngược lại, phong cách ủy quyền mà tôi đang cổ vũ trong chương này có vẻ xa lạ và sử dụng việc chia sẻ `[[Prototype]]` cùng `this` theo những cách mà bạn có thể chưa quen. Để sử dụng một phong cách như vậy một cách hiệu quả, bạn sẽ phải đầu tư thời gian và luyện tập để xây dựng sự quen thuộc sâu sắc hơn.

Nhưng theo quan điểm của tôi, "cái giá" của việc né tránh sự hợp thành ảo thông qua ủy quyền có thể cảm nhận được trên khắp tất cả các chữ ký hàm và vị trí gọi hàm; tôi thấy chúng rối rắm hơn rất nhiều. Việc truyền ngữ cảnh tường minh đó quả là một gánh nặng (tax).

Thực tế, tôi sẽ không bao giờ ủng hộ phong cách code đó chút nào. Nếu bạn muốn tránh sự ủy quyền, có lẽ tốt nhất là cứ gắn bó với code theo phong cách `class`, như đã thấy trong Chương 3. Như một bài tập dành cho người đọc, hãy thử chuyển đổi các đoạn code `ControlPoint` / `GuideLine` trước đó sang sử dụng `class`.

[^TreatyOfOrlando]: "Treaty of Orlando"; Henry Lieberman, Lynn Andrea Stein, David Ungar; 6 tháng 10 năm 1987; https://web.media.mit.edu/~lieber/Publications/Treaty-of-Orlando-Treaty-Text.pdf ; PDF; Truy cập tháng 7 năm 2022

[^ClassVsPrototype]: "Classes vs. Prototypes, Some Philosophical and Historical Observations"; Antero Taivalsaari; 22 tháng 4 năm 1996; https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.56.4713&rep=rep1&type=pdf ; PDF; Truy cập tháng 7 năm 2022
