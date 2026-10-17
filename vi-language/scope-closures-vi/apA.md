# You Don't Know JS Yet: Scope & Closures - Tái bản lần 2
# Phụ lục A: Khám phá sâu hơn

Bây giờ chúng ta sẽ tìm hiểu một số khía cạnh tinh tế và các trường hợp góc cạnh (corner cases) xoay quanh nhiều chủ đề đã được đề cập trong phần nội dung chính của cuốn sách này. Phụ lục này là tài liệu bổ trợ, có tính chất tùy chọn.

Một số người cảm thấy việc đi quá sâu vào các trường hợp ngoại lệ rắc rối và những luồng ý kiến trái chiều chẳng đem lại gì ngoài sự ồn ào và xao nhãng—người ta cho rằng các lập trình viên sẽ làm việc hiệu quả hơn nếu cứ bám theo những con đường mòn quen thuộc. Cách tiếp cận của tôi từng bị chỉ trích là không thực tế và phản tác dụng. Tôi hiểu và trân trọng góc nhìn đó, dù tôi không nhất thiết phải đồng tình với nó.

Tôi tin rằng việc được trang bị kiến thức vững chắc về cách mọi thứ vận hành sẽ tốt hơn nhiều so với việc chỉ lướt qua các chi tiết bằng những giả định và sự thiếu tò mò. Sau cùng, rồi bạn cũng sẽ gặp phải những tình huống phát sinh từ một ngóc ngách mà bạn chưa từng khám phá. Nói cách khác, bạn sẽ không thể dành trọn thời gian của mình để lướt êm ả trên con đường bằng phẳng (*happy path*). Chẳng lẽ bạn lại không muốn chuẩn bị sẵn sàng cho những cú xóc gập ghềnh không thể tránh khỏi khi phải đi đường địa hình (*off-roading*)?

Những cuộc thảo luận này cũng sẽ mang đậm dấu ấn quan điểm cá nhân của tôi nhiều hơn so với phần nội dung chính, vì vậy hãy ghi nhớ điều đó khi bạn tiếp nhận và cân nhắc những gì được trình bày. Phụ lục này có phần giống như một tuyển tập các bài blog ngắn đi sâu vào các chủ đề khác nhau của cuốn sách. Nó khá dài và đi sâu vào từng ngóc ngách chi tiết, vì vậy hãy thong thả và đừng vội vã lướt qua mọi thứ ở đây.

## Phạm vi ngầm định (Implied Scopes)

Các phạm vi đôi khi được tạo ra ở những nơi không hề hiển nhiên. Trong thực tế, những phạm vi ngầm định này không thường xuyên ảnh hưởng đến hành vi chương trình của bạn, nhưng việc biết rằng chúng đang diễn ra vẫn rất hữu ích. Hãy chú ý đến những phạm vi bất ngờ sau đây:

* Phạm vi tham số (Parameter scope)
* Phạm vi tên hàm (Function name scope)

### Phạm vi tham số (Parameter Scope)

Phép ẩn dụ cuộc trò chuyện trong Chương 2 ngụ ý rằng các tham số hàm về cơ bản giống hệt như các biến được khai báo cục bộ bên trong phạm vi hàm. Nhưng điều đó không phải lúc nào cũng đúng.

Hãy xem xét:

```js
// outer/global scope: RED(1)

function getStudentName(studentID) {
    // function scope: BLUE(2)

    // ..
}
```

Ở đây, `studentID` được xem là một tham số "đơn giản" (simple parameter), do đó nó thực sự hoạt động như một thành viên của phạm vi hàm BLUE(2). Nhưng nếu chúng ta đổi nó thành một tham số không đơn giản (non-simple parameter), thì về mặt kỹ thuật điều đó không còn đúng nữa. Các dạng tham số được coi là không đơn giản bao gồm: tham số có giá trị mặc định, tham số rest (sử dụng `...`), và tham số được giải cấu trúc (destructured parameters).

Hãy xem xét:

```js
// outer/global scope: RED(1)

function getStudentName(/*BLUE(2)*/ studentID = 0) {
    // function scope: GREEN(3)

    // ..
}
```

Tại đây, danh sách tham số về cơ bản trở thành một phạm vi riêng của chính nó, và phạm vi của hàm sau đó được lồng bên trong phạm vi *đó*.

Tại sao? Điều đó tạo ra khác biệt gì? Các dạng tham số không đơn giản giới thiệu nhiều trường hợp biên (corner cases) khác nhau, vì vậy danh sách tham số trở thành một phạm vi riêng để xử lý chúng hiệu quả hơn.

Hãy xem xét:

```js
function getStudentName(studentID = maxID, maxID) {
    // ..
}
```

Giả sử các thao tác diễn ra từ trái sang phải, giá trị mặc định `= maxID` cho tham số `studentID` yêu cầu `maxID` phải tồn tại từ trước (và đã được khởi tạo). Đoạn mã này sẽ tạo ra lỗi TDZ (Chương 5). Lý do là `maxID` được khai báo trong phạm vi tham số, nhưng nó vẫn chưa được khởi tạo do thứ tự của các tham số. Nếu thứ tự tham số bị đảo ngược, không có lỗi TDZ nào xảy ra:

```js
function getStudentName(maxID,studentID = maxID) {
    // ..
}
```

Sự phức tạp thậm chí còn *rắc rối hơn* nếu chúng ta đưa một biểu thức hàm (function expression) vào vị trí tham số mặc định, rồi sau đó biểu thức hàm này có thể tạo closure của riêng nó (Chương 7) bao quanh các tham số trong phạm vi tham số ngầm định này:

```js
function whatsTheDealHere(id,defaultID = () => id) {
    id = 5;
    console.log( defaultID() );
}

whatsTheDealHere(3);
// 5
```

Đoạn mã đó có lẽ nghe hợp lý, bởi vì hàm mũi tên `defaultID()` đóng bao (closure) quanh tham số/biến `id`, biến mà sau đó chúng ta gán lại giá trị thành `5`. Nhưng bây giờ chúng ta hãy đưa vào một định nghĩa che khuất (shadowing definition) cho `id` trong phạm vi hàm:

```js
function whatsTheDealHere(id,defaultID = () => id) {
    var id = 5;
    console.log( defaultID() );
}

whatsTheDealHere(3);
// 3
```

Ối chà! `var id = 5` đang che khuất (shadow) tham số `id`, nhưng closure của hàm `defaultID()` là bao quanh tham số, chứ không phải biến che khuất trong thân hàm. Điều này chứng minh rằng có một bong bóng phạm vi (scope bubble) bao quanh danh sách tham số.

Nhưng nó thậm chí còn kỳ lạ hơn thế nữa!

```js
function whatsTheDealHere(id,defaultID = () => id) {
    var id;

    console.log(`local variable 'id': ${ id }`);
    console.log(
        `parameter 'id' (closure): ${ defaultID() }`
    );

    console.log("reassigning 'id' to 5");
    id = 5;

    console.log(`local variable 'id': ${ id }`);
    console.log(
        `parameter 'id' (closure): ${ defaultID() }`
    );
}

whatsTheDealHere(3);
// local variable 'id': 3   <--- Hả!? Lạ lùng thật!
// parameter 'id' (closure): 3
// reassigning 'id' to 5
// local variable 'id': 5
// parameter 'id' (closure): 3
```

Điểm kỳ lạ ở đây là thông điệp console đầu tiên. Tại thời điểm đó, biến cục bộ che khuất `id` vừa mới được khai báo với `var id`, điều mà Chương 5 khẳng định là thường được tự động khởi tạo thành `undefined` ở đầu phạm vi của nó. Tại sao nó lại không in ra `undefined`?

Trong trường hợp góc cạnh đặc biệt này (vì lý do tương thích ngược với mã nguồn cũ), JS không tự động khởi tạo `id` thành `undefined`, mà khởi tạo nó bằng giá trị của tham số `id` (`3`)!

Mặc dù hai `id` vào thời điểm đó trông giống như chúng là một biến duy nhất, nhưng chúng thực chất vẫn tách biệt (và nằm trong các phạm vi riêng biệt). Phép gán `id = 5` làm cho sự phân kỳ trở nên dễ quan sát: tham số `id` vẫn giữ nguyên giá trị `3` trong khi biến cục bộ trở thành `5`.

Lời khuyên của tôi để tránh bị mắc bẫy bởi những sắc thái kỳ lạ này:

* Đừng bao giờ dùng biến cục bộ để che khuất (shadow) các tham số.

* Tránh sử dụng một hàm tham số mặc định có closure bao quanh bất kỳ tham số nào.

Ít nhất thì bây giờ bạn đã nhận thức được và có thể cẩn trọng với việc danh sách tham số là một phạm vi riêng nếu có bất kỳ tham số nào không phải là tham số đơn giản.

### Phạm vi tên hàm (Function Name Scope)

Trong mục "Function Name Scope" ở Chương 3, tôi đã khẳng định rằng tên của một biểu thức hàm được thêm vào phạm vi của chính hàm đó. Hãy nhớ lại:

```js
var askQuestion = function ofTheTeacher(){
    // ..
};
```

Đúng là `ofTheTeacher` không được thêm vào phạm vi bao ngoài (nơi `askQuestion` được khai báo), nhưng nó cũng không *đơn thuần* được thêm vào phạm vi của hàm theo cách mà bạn có thể đang hình dung. Đó là một trường hợp góc cạnh kỳ lạ khác của phạm vi ngầm định.

Định danh tên của một biểu thức hàm nằm trong phạm vi ngầm định của riêng nó, được lồng giữa phạm vi bao ngoài và phạm vi hàm chính bên trong.

Nếu `ofTheTeacher` nằm trong phạm vi của hàm, chúng ta sẽ kỳ vọng có một lỗi ở đây:

```js
var askQuestion = function ofTheTeacher(){
    // tại sao đây không phải là lỗi khai báo trùng lặp?
    let ofTheTeacher = "Confused, yet?";
};
```

Dạng khai báo `let` không cho phép tái khai báo (xem Chương 5). Nhưng đây lại là hành vi che khuất (shadowing) hoàn toàn hợp lệ chứ không phải tái khai báo, bởi vì hai định danh `ofTheTeacher` nằm trong các phạm vi riêng biệt.

Bạn sẽ hiếm khi gặp phải trường hợp nào mà phạm vi của định danh tên hàm tạo ra sự khác biệt. Nhưng một lần nữa, thật tốt khi biết cách các cơ chế này thực sự vận hành. Để tránh gặp rắc rối, đừng bao giờ che khuất các định danh tên hàm.

## Hàm ẩn danh và Hàm có tên (Anonymous vs. Named Functions)

Như đã thảo luận trong Chương 3, các hàm có thể được biểu diễn dưới dạng có tên hoặc ẩn danh. Việc sử dụng dạng ẩn danh phổ biến hơn rất nhiều, nhưng liệu đó có phải là một ý hay?

Khi bạn cân nhắc việc đặt tên cho các hàm của mình, hãy xem xét:

* Việc suy luận tên (name inference) là không hoàn chỉnh
* Tên lexical cho phép hàm tự tham chiếu chính nó
* Tên hàm là những mô tả hữu ích
* Hàm mũi tên (arrow functions) không có tên lexical
* IIFE cũng cần có tên

### Tên tường minh hay Tên được suy luận?

Mỗi hàm trong chương trình của bạn đều có một mục đích. Nếu nó không có mục đích, hãy loại bỏ nó đi, vì bạn chỉ đang lãng phí dung lượng. Nếu nó *thực sự có* một mục đích, thì *chắc chắn có* một cái tên dành cho mục đích đó.

Đến đây, nhiều độc giả có thể đồng ý với tôi. Nhưng điều đó có nghĩa là chúng ta nên luôn luôn đưa cái tên đó vào trong code không? Đây là chỗ mà tôi sẽ khiến không ít người phải nhướng mày. Tôi xin khẳng định dứt khoát rằng: có!

Trước hết, chữ "anonymous" xuất hiện trong stack trace chẳng hề hữu ích chút nào cho việc gỡ lỗi (debugging):

```js
btn.addEventListener("click",function(){
    setTimeout(function(){
        ["a",42].map(function(v){
            console.log(v.toUpperCase());
        });
    },100);
});
// Uncaught TypeError: v.toUpperCase is not a function
//     at myProgram.js:4
//     at Array.map (<anonymous>)
//     at myProgram.js:3
```

Thật khó chịu. Hãy so sánh với những gì được báo cáo nếu tôi đặt tên cho các hàm:

```js
btn.addEventListener("click",function onClick(){
    setTimeout(function waitAMoment(){
        ["a",42].map(function allUpper(v){
            console.log(v.toUpperCase());
        });
    },100);
});
// Uncaught TypeError: v.toUpperCase is not a function
//     at allUpper (myProgram.js:4)
//     at Array.map (<anonymous>)
//     at waitAMoment (myProgram.js:3)
```

Bạn có thấy các tên `waitAMoment` và `allUpper` xuất hiện và mang lại cho stack trace nhiều thông tin/ngữ cảnh hữu ích hơn cho việc gỡ lỗi không? Chương trình sẽ dễ debug hơn nếu chúng ta sử dụng những cái tên hợp lý cho tất cả các hàm của mình.

| LƯU Ý: |
| :--- |
| Từ "&lt;anonymous>" đáng tiếc vẫn xuất hiện là vì phần triển khai của `Array.map(..)` không hiện diện trong chương trình của chúng ta, mà được tích hợp sẵn bên trong JS engine. Nó không phải do bất kỳ sự nhầm lẫn nào mà chương trình của chúng ta tạo ra với các lối tắt tăng tính dễ đọc. |

Nhân tiện, hãy đảm bảo rằng chúng ta có cùng cách hiểu về thế nào là một hàm có tên:

```js
function thisIsNamed() {
    // ..
}

ajax("some.url",function thisIsAlsoNamed(){
   // ..
});

var notNamed = function(){
    // ..
};

makeRequest({
    data: 42,
    cb /* đây cũng không phải là tên */: function(){
        // ..
    }
});

var stillNotNamed = function butThisIs(){
    // ..
};
```

"Nhưng chờ đã!", bạn có thể nói. Một vài hàm trong số đó *thực sự* có tên mà, đúng không!?

```js
var notNamed = function(){
    // ..
};

var config = {
    cb: function(){
        // ..
    }
};

notNamed.name;
// notNamed

config.cb.name;
// cb
```

Những cái tên này được gọi là tên *được suy luận* (inferred names). Tên được suy luận cũng tốt, nhưng chúng không thực sự giải quyết được toàn bộ mối bận tâm mà tôi đang thảo luận.

### Thiếu vắng tên gọi?

Đúng vậy, những cái tên được suy luận này có thể xuất hiện trong stack trace, điều này chắc chắn tốt hơn so với việc hiển thị "anonymous". Nhưng...

```js
function ajax(url,cb) {
    console.log(cb.name);
}

ajax("some.url",function(){
    // ..
});
// ""
```

Rất tiếc. Các biểu thức `function` ẩn danh được truyền dưới dạng callback không có khả năng nhận một cái tên suy luận, do đó `cb.name` chỉ chứa chuỗi rỗng `""`. Phần lớn tất cả các biểu thức `function`, đặc biệt là các biểu thức ẩn danh, đều được sử dụng làm đối số callback; không có hàm nào trong số này nhận được tên. Vì vậy, việc dựa vào cơ chế suy luận tên (name inference) là không trọn vẹn, nếu không muốn nói là chưa đủ.

Và không chỉ riêng callback gặp thiếu sót với việc suy luận tên:

```js
var config = {};

config.cb = function(){
    // ..
};

config.cb.name;
// ""

var [ noName ] = [ function(){} ];
noName.name
// ""
```

Bất kỳ phép gán biểu thức `function` nào không phải là một *phép gán đơn giản* (simple assignment) cũng sẽ thất bại trong việc suy luận tên. Nói cách khác, trừ khi bạn cẩn thận và có chủ ý về điều đó, hầu như tất cả các biểu thức `function` ẩn danh trong chương trình của bạn trên thực tế sẽ hoàn toàn không có tên.

Cơ chế suy luận tên đơn giản là... không đủ.

Và ngay cả khi một biểu thức `function` *thực sự* nhận được một cái tên suy luận, điều đó vẫn không được tính là một hàm có tên đầy đủ.

### Tôi là ai?

Nếu không có một định danh tên lexical, hàm sẽ không có cách nào từ bên trong để tự tham chiếu đến chính nó. Tự tham chiếu (self-reference) rất quan trọng đối với những việc như đệ quy và xử lý sự kiện:

```js
// bị lỗi
runOperation(function(num){
    if (num <= 1) return 1;
    return num * oopsNoNameToCall(num - 1);
});

// cũng bị lỗi
btn.addEventListener("click",function(){
   console.log("should only respond to one click!");
   btn.removeEventListener("click",oopsNoNameHere);
});
```

Việc bỏ qua tên lexical khỏi callback sẽ khiến việc tự tham chiếu đến hàm một cách đáng tin cậy trở nên khó khăn hơn. Bạn *có thể* khai báo một biến trong phạm vi bao ngoài tham chiếu đến hàm, nhưng biến này lại bị *kiểm soát* bởi phạm vi bao ngoài đó—nó có thể bị gán lại, v.v.—vì vậy nó không đáng tin cậy bằng việc hàm có khả năng tự tham chiếu nội bộ của riêng mình.

### Tên hàm là những mô tả (Names are Descriptors)

Cuối cùng, và tôi nghĩ là quan trọng nhất trong tất cả, việc bỏ qua tên của một hàm khiến người đọc khó nhận biết mục đích của hàm đó là gì khi nhìn lướt qua. Họ phải đọc thêm mã nguồn, bao gồm mã bên trong hàm và mã bao quanh bên ngoài hàm, để tìm ra câu trả lời.

Hãy xem xét:

```js
[ 1, 2, 3, 4, 5 ].filter(function(v){
    return v % 2 == 1;
});
// [ 1, 3, 5 ]

[ 1, 2, 3, 4, 5 ].filter(function keepOnlyOdds(v){
    return v % 2 == 1;
});
// [ 1, 3, 5 ]
```

Hoàn toàn không có lập luận hợp lý nào có thể biện minh rằng việc **bỏ qua** tên `keepOnlyOdds` ở callback đầu tiên lại truyền đạt hiệu quả hơn tới người đọc về mục đích của callback này. Bạn tiết kiệm được 13 ký tự, nhưng lại đánh mất thông tin quan trọng về khả năng đọc hiểu. Tên `keepOnlyOdds` cho người đọc biết rất rõ ràng ngay từ cái nhìn đầu tiên chuyện gì đang diễn ra.

JS engine không quan tâm đến cái tên. Nhưng những con người đọc code của bạn thì hoàn toàn có.

Liệu người đọc có thể nhìn vào `v % 2 == 1` và suy ra nó đang làm gì không? Chắc chắn rồi. Nhưng họ phải tự suy luận mục đích (và cái tên) bằng cách nhẩm thực thi mã trong đầu. Ngay cả một khoảng ngập ngừng ngắn để làm việc đó cũng làm chậm quá trình đọc hiểu code. Một cái tên mang tính mô tả tốt sẽ làm cho quá trình này trở nên gần như tức thì và không tốn chút sức lực nào.

Hãy nghĩ theo cách này: tác giả của đoạn mã này cần phải tìm hiểu mục đích của một hàm bao nhiêu lần trước khi thêm tên vào code? Khoảng một lần. Có thể hai hoặc ba lần nếu họ cần điều chỉnh lại tên. Nhưng những người đọc đoạn mã này sẽ phải tự tìm hiểu tên/mục đích bao nhiêu lần? Mỗi một lần dòng mã này được đọc. Hàng trăm lần? Hàng nghìn lần? Hay nhiều hơn thế?

Bất kể độ dài hay độ phức tạp của hàm ra sao, khẳng định của tôi là tác giả nên tìm ra một cái tên mang tính mô tả tốt và đưa nó vào code. Ngay cả những hàm một dòng trong các câu lệnh `map(..)` và `then(..)` cũng nên được đặt tên:

```js
lookupTheRecords(someData)
.then(function extractSalesRecords(resp){
   return resp.allSales;
})
.then(storeRecords);
```

Tên `extractSalesRecords` cho người đọc biết mục đích của hàm xử lý `then(..)` này *tốt hơn* nhiều so với việc chỉ suy diễn mục đích đó từ việc nhẩm thực thi `return resp.allSales`.

Lý do duy nhất cho việc không đặt tên cho hàm chỉ có thể là sự lười biếng (không muốn gõ thêm vài ký tự) hoặc thiếu sáng tạo (không thể nghĩ ra một cái tên hay). Nếu bạn không thể nghĩ ra một cái tên thích hợp, rất có thể bạn vẫn chưa hiểu rõ hàm đó và mục đích của nó. Hàm đó có lẽ được thiết kế kém, hoặc nó làm quá nhiều việc, và cần được tái cấu trúc lại. Một khi bạn có một hàm được thiết kế tốt, chỉ đảm nhận một mục đích duy nhất, thì tên gọi thích hợp của nó sẽ trở nên rõ ràng.

Đây là một mẹo tôi hay dùng: khi mới bắt đầu viết một hàm, nếu tôi chưa hiểu trọn vẹn mục đích của nó và không thể nghĩ ra một cái tên hay để sử dụng, tôi chỉ dùng `TODO` làm tên. Bằng cách đó, sau này khi xem lại code của mình, tôi dễ dàng tìm thấy những placeholder tên đó, và tôi sẽ có xu hướng (và chuẩn bị tốt hơn!) để quay lại và tìm ra một cái tên hay hơn, thay vì chỉ để nguyên là `TODO`.

Tất cả các hàm đều cần có tên. Từng hàm một. Không có ngoại lệ. Bất kỳ cái tên nào bạn bỏ sót đều đang làm cho chương trình trở nên khó đọc hơn, khó debug hơn, khó mở rộng và bảo trì hơn sau này.

### Hàm mũi tên (Arrow Functions)

Hàm mũi tên **luôn luôn** ẩn danh, ngay cả khi (rất hiếm khi) chúng được sử dụng theo cách mang lại cho chúng một cái tên suy luận. Tôi vừa dành vài trang để giải thích lý do tại sao hàm ẩn danh là một ý tưởng tồi, vì vậy bạn có thể đoán được tôi nghĩ gì về hàm mũi tên rồi đấy.

Đừng sử dụng chúng như một sự thay thế chung cho các hàm thông thường. Chúng ngắn gọn hơn, đúng vậy, nhưng sự ngắn gọn đó phải trả giá bằng việc lược bỏ các dấu phân cách trực quan (visual delimiters) quan trọng giúp não bộ của chúng ta nhanh chóng phân tích những gì đang đọc. Và, xét theo trọng tâm của cuộc thảo luận này, chúng là hàm ẩn danh, điều này cũng khiến chúng trở nên tệ hơn về khả năng đọc hiểu từ góc độ đó.

Hàm mũi tên có mục đích riêng, nhưng mục đích đó không phải là để tiết kiệm thao tác gõ phím. Hàm mũi tên có hành vi *lexical this*, điều này phần nào vượt ra ngoài phạm vi thảo luận của chúng ta trong cuốn sách này.

Tóm tắt ngắn gọn: hàm mũi tên hoàn toàn không định nghĩa từ khóa định danh `this`. Nếu bạn sử dụng `this` bên trong một hàm mũi tên, nó hoạt động chính xác như bất kỳ tham chiếu biến nào khác: chuỗi phạm vi (scope chain) sẽ được tra cứu để tìm phạm vi hàm (hàm thông thường, không phải hàm mũi tên) nơi `this` *được* định nghĩa, và sử dụng giá trị đó.

Nói cách khác, hàm mũi tên đối xử với `this` như bất kỳ biến lexical nào khác.

Nếu bạn đã quen với những mẹo như `var self = this`, hoặc nếu bạn thích gọi `.bind(this)` trên các biểu thức `function` bên trong, chỉ để ép chúng kế thừa `this` từ một hàm bên ngoài như thể nó là một biến lexical, thì hàm mũi tên `=>` chắc chắn là lựa chọn tốt hơn. Chúng được thiết kế đặc thù để giải quyết vấn đề đó.

Vì vậy, trong những trường hợp hiếm hoi bạn cần *lexical this*, hãy sử dụng hàm mũi tên. Đó là công cụ tốt nhất cho công việc đó. Nhưng hãy lưu ý rằng khi làm như vậy, bạn đang chấp nhận những nhược điểm của một hàm ẩn danh. Bạn nên bỏ thêm công sức để giảm thiểu *chi phí* về khả năng đọc hiểu, chẳng hạn như đặt tên biến mang tính mô tả rõ ràng hơn và thêm các chú thích trong code.

### Các biến thể của IIFE

Tất cả các hàm đều nên có tên. Tôi đã nhắc lại điều đó vài lần rồi, đúng không!? Điều đó bao gồm cả IIFE.

```js
(function(){
    // đừng làm thế này!
})();

(function doThisInstead(){
    // ..
})();
```

Làm thế nào để chúng ta nghĩ ra một cái tên cho IIFE? Hãy xác định xem IIFE ở đó để làm gì. Tại sao bạn lại cần một phạm vi ở vị trí đó? Bạn đang ẩn một biến bộ đệm (cache variable) cho hồ sơ học sinh phải không?

```js
var getStudents = (function StoreStudentRecords(){
    var studentRecords = [];

    return function getStudents() {
        // ..
    }
})();
```

Tôi đặt tên cho IIFE là `StoreStudentRecords` bởi vì đó là những gì nó đang làm: lưu trữ hồ sơ học sinh. Mỗi IIFE nên có một cái tên. Không có ngoại lệ.

IIFE thường được định nghĩa bằng cách đặt cặp dấu ngoặc `( .. )` bao quanh biểu thức `function`, như được hiển thị trong các đoạn mã trước đó. Nhưng đó không phải là cách duy nhất để định nghĩa một IIFE. Về mặt kỹ thuật, lý do duy nhất chúng ta sử dụng cặp dấu `( .. )` bao quanh đầu tiên chỉ là để từ khóa `function` không nằm ở vị trí khiến trình phân tích cú pháp JS coi nó là một khai báo hàm (`function` declaration). Nhưng có những cách cú pháp khác để tránh bị phân tích cú pháp thành một khai báo:

```js
!function thisIsAnIIFE(){
    // ..
}();

+function soIsThisOne(){
    // ..
}();

~function andThisOneToo(){
    // ..
}();
```

Các toán tử `!`, `+`, `~`, và một số toán tử một ngôi khác (toán tử có một toán hạng) đều có thể được đặt trước `function` để biến nó thành một biểu thức. Khi đó lời gọi `()` cuối cùng là hợp lệ, biến nó thành một IIFE.

Tôi thực sự khá thích sử dụng toán tử một ngôi `void` khi định nghĩa một IIFE độc lập:

```js
void function yepItsAnIIFE() {
    // ..
}();
```

Lợi ích của `void` là nó truyền đạt rõ ràng ngay từ đầu hàm rằng IIFE này sẽ không trả về bất kỳ giá trị nào.

Dù bạn định nghĩa IIFE của mình theo cách nào đi nữa, hãy dành cho chúng sự trân trọng bằng cách đặt tên cho chúng.

## Hoisting: Hàm và Biến (Hoisting: Functions and Variables)

Chương 5 đã trình bày rõ ràng cả *hoisting hàm* (function hoisting) lẫn *hoisting biến* (variable hoisting). Vì hoisting thường được coi là một sai lầm trong thiết kế của JS, tôi muốn khám phá ngắn gọn lý do tại sao cả hai hình thức hoisting này *có thể* mang lại lợi ích và vẫn nên được cân nhắc.

Hãy xem xét hoisting ở một mức độ sâu hơn bằng cách đánh giá các giá trị sau:

* Mã thực thi trước, khai báo hàm sau cùng
* Vị trí mang tính ngữ nghĩa của các khai báo biến

### Hoisting hàm (Function Hoisting)

Để ôn lại, chương trình này hoạt động được là nhờ *function hoisting*:

```js
getStudents();

// ..

function getStudents() {
    // ..
}
```

Khai báo `function` được hoist trong quá trình biên dịch, điều đó có nghĩa là `getStudents` là một định danh được khai báo cho toàn bộ phạm vi. Ngoài ra, định danh `getStudents` được tự động khởi tạo bằng tham chiếu hàm, cũng ngay tại phần đầu của phạm vi.

Tại sao điều này lại hữu ích? Lý do tôi thích tận dụng *function hoisting* là vì nó đưa mã *thực thi* trong bất kỳ phạm vi nào lên trên cùng, và mọi khai báo hàm tiếp theo xuống bên dưới. Điều này có nghĩa là bạn sẽ dễ dàng tìm thấy đoạn mã sẽ chạy trong bất kỳ khu vực nào hơn, thay vì phải cuộn trang liên tục với hy vọng tìm thấy dấu đóng ngoặc nhọn `}` đánh dấu điểm kết thúc của một phạm vi/hàm ở đâu đó.

Tôi tận dụng vị trí đảo ngược này ở mọi cấp độ phạm vi:

```js
getStudents();

// *************

function getStudents() {
    var whatever = doSomething();

    // các phần việc khác

    return whatever;

    // *************

    function doSomething() {
        // ..
    }
}
```

Khi tôi mới mở một tệp như vậy, dòng đầu tiên chính là mã thực thi khởi động hành vi của nó. Điều đó cực kỳ dễ nhận thấy! Sau đó, nếu tôi cần tìm và kiểm tra `getStudents()`, tôi thích việc dòng đầu tiên của nó cũng là mã thực thi. Chỉ khi cần xem chi tiết của `doSomething()`, tôi mới đi xuống bên dưới để tìm định nghĩa của nó.

Nói cách khác, tôi nghĩ rằng *function hoisting* làm cho mã nguồn dễ đọc hơn thông qua một trình tự đọc trôi chảy, tăng tiến dần từ trên xuống dưới.

### Hoisting biến (Variable Hoisting)

Thế còn *hoisting biến* thì sao?

Mặc dù `let` và `const` đều có hoist, nhưng bạn không thể sử dụng những biến đó trong vùng TDZ của chúng (xem Chương 5). Do đó, cuộc thảo luận sau đây chỉ áp dụng cho các khai báo `var`. Trước khi tiếp tục, tôi phải thừa nhận rằng: trong hầu hết các trường hợp, tôi hoàn toàn đồng ý rằng *variable hoisting* là một ý tưởng tồi:

```js
pleaseDontDoThis = "bad idea";

// mãi về sau
var pleaseDontDoThis;
```

Trong khi thứ tự đảo ngược kiểu đó rất hữu ích cho *function hoisting*, ở đây tôi nghĩ nó thường làm cho mã nguồn trở nên khó suy luận hơn.

Nhưng có một ngoại lệ mà tôi đã tìm thấy, dù khá hiếm hoi, trong quá trình lập trình của chính mình. Nó liên quan đến vị trí tôi đặt các khai báo `var` bên trong một định nghĩa module CommonJS.

Dưới đây là cách tôi thường cấu trúc các định nghĩa module của mình trong Node:

```js
// dependencies
var aModuleINeed = require("very-helpful");
var anotherModule = require("kinda-helpful");

// public API
var publicAPI = Object.assign(module.exports,{
    getStudents,
    addStudents,
    // ..
});

// ********************************
// private implementation

var cache = { };
var otherData = [ ];

function getStudents() {
    // ..
}

function addStudents() {
    // ..
}
```

Hãy chú ý cách các biến `cache` và `otherData` nằm trong phần "private" của bố cục module? Đó là vì tôi không có kế hoạch công khai chúng ra ngoài. Vì vậy, tôi tổ chức module sao cho chúng nằm cạnh các chi tiết triển khai ẩn khác của module.

Nhưng tôi đã gặp một vài trường hợp hiếm hoi khi tôi cần các phép gán cho các giá trị đó diễn ra ở *phía trên*, trước khi tôi khai báo public API được export của module. Ví dụ:

```js
// public API
var publicAPI = Object.assign(module.exports,{
    getStudents,
    addStudents,
    refreshData: refreshData.bind(null,cache)
});
```

Tôi cần biến `cache` phải được gán giá trị từ trước, vì giá trị đó được sử dụng trong quá trình khởi tạo public API (thao tác áp dụng hàm một phần - partial application thông qua `.bind(..)`).

Liệu tôi có nên chuyển `var cache = { .. }` lên trên đầu, phía trên phần khởi tạo public API này không? Chà, có lẽ vậy. Nhưng bây giờ sẽ khó nhận thấy rằng `var cache` là một chi tiết triển khai mang tính *riêng tư* (private). Đây là giải pháp thỏa hiệp mà tôi (khá hiếm khi) áp dụng:

```js
cache = {};   // được dùng ở đây, nhưng khai báo bên dưới

// public API
var publicAPI = Object.assign(module.exports,{
    getStudents,
    addStudents,
    refreshData: refreshData.bind(null,cache)
});

// ********************************
// private implementation

var cache /* = {}*/;
```

Bạn có thấy hiện tượng *variable hoisting* ở đây không? Tôi đã khai báo `cache` ở bên dưới, nơi nó thuộc về một cách hợp lý, nhưng trong trường hợp hiếm hoi này, tôi đã sử dụng nó sớm hơn ở phía trên, trong khu vực cần thiết cho việc khởi tạo của nó. Tôi thậm chí còn để lại một gợi ý về giá trị được gán cho `cache` trong một dòng chú thích.

Đó thực sự là trường hợp duy nhất tôi từng tìm thấy để tận dụng *variable hoisting* nhằm gán giá trị cho một biến sớm hơn trong phạm vi so với khai báo của nó. Nhưng tôi nghĩ đó là một ngoại lệ hợp lý có thể áp dụng một cách cẩn trọng.

## Biện hộ cho `var` (The Case for `var`)

Nhân tiện nói về *variable hoisting*, chúng ta hãy trò chuyện thẳng thắn một chút về `var`, một "kẻ phản diện" ưa thích mà các lập trình viên rất thích đổ lỗi cho nhiều nỗi khổ trong việc phát triển JS. Trong Chương 5, chúng ta đã tìm hiểu `let`/`const` và đã hứa rằng chúng ta sẽ xem lại vị trí của `var` trong toàn bộ bức tranh tổng thể này.

Khi tôi đưa ra lập luận của mình, đừng bỏ lỡ:

* `var` chưa bao giờ bị hỏng cả
* `let` là bạn của bạn
* `const` có tính hữu dụng hạn chế
* Điều tuyệt vời nhất của cả hai thế giới: `var` *và* `let`

### Đừng vội vứt bỏ `var`

`var` vẫn ổn, và hoạt động hoàn toàn bình thường. Nó đã tồn tại suốt 25 năm, và nó sẽ tiếp tục tồn tại, hữu ích và hoạt động tốt trong 25 năm nữa hoặc hơn thế. Những tuyên bố cho rằng `var` bị hỏng, bị deprecated, lỗi thời, nguy hiểm hoặc thiết kế kém chỉ là sự a dua theo trào lưu vô căn cứ.

Điều đó có nghĩa là `var` là từ khóa khai báo thích hợp cho mọi khai báo trong chương trình của bạn không? Chắc chắn là không. Nhưng nó vẫn có chỗ đứng riêng trong các chương trình của bạn. Từ chối sử dụng nó chỉ vì một ai đó trong nhóm chọn một cấu hình linter quá khắt khe bài xích `var` chẳng khác nào việc "chặt mũi cho hả giận" (tự làm hại chính mình).

Được rồi, bây giờ khi tôi đã khiến bạn thực sự chú ý (hoặc bức xúc), hãy để tôi cố gắng giải thích lập trường của mình.

Để làm rõ, tôi là một fan của `let` đối với các khai báo có phạm vi khối (block-scoped declarations). Tôi thực sự không thích TDZ và tôi nghĩ đó là một sai lầm. Nhưng bản thân `let` thì rất tuyệt. Tôi sử dụng nó thường xuyên. Trên thực tế, tôi có lẽ dùng nó nhiều ngang ngửa hoặc thậm chí nhiều hơn so với `var`.

### `const`-antly bối rối

Mặt khác, với `const`, tôi không sử dụng nó thường xuyên. Tôi sẽ không đi sâu vào tất cả các lý do tại sao, nhưng tóm lại là `const` không *xứng đáng với cái giá phải trả* (carrying its own weight). Nghĩa là, trong khi `const` mang lại một chút lợi ích trong một số trường hợp, thì lợi ích đó bị lu mờ trước lịch sử lâu dài đầy rắc rối xung quanh sự nhầm lẫn về `const` ở nhiều ngôn ngữ khác nhau, rất lâu trước khi nó xuất hiện trong JS.

`const` tạo cảm giác giả vờ như nó tạo ra các giá trị không thể thay đổi (immutable values)—một quan niệm sai lầm cực kỳ phổ biến trong cộng đồng lập trình viên ở nhiều ngôn ngữ—trong khi những gì nó thực sự làm chỉ là ngăn chặn việc gán lại (re-assignment).

```js
const studentIDs = [ 14, 73, 112 ];

// về sau

studentIDs.push(6);   // ủa, từ từ đã... cái gì cơ!?
```

Sử dụng `const` với một giá trị có thể đột biến (mutable value - như mảng hoặc đối tượng) là bạn đang giăng một cái bẫy chờ một lập trình viên tương lai (hoặc người đọc code của bạn) sập bẫy, đó là họ hoặc không biết, hoặc vô tình quên mất rằng *tính bất biến của giá trị* (value immutability) hoàn toàn không giống với *tính bất biến của phép gán* (assignment immutability).

Tôi chỉ nghĩ rằng chúng ta không nên đặt ra những cái bẫy như vậy. Lần duy nhất tôi sử dụng `const` là khi tôi gán một giá trị vốn đã bất biến (như `42` hoặc `"Hello, friends!"`), và khi nó rõ ràng là một "hằng số" theo nghĩa là một placeholder có tên cho một giá trị nguyên bản (literal value), vì mục đích ngữ nghĩa. Đó là trường hợp `const` được sử dụng tốt nhất. Tuy nhiên, điều đó khá hiếm trong mã nguồn của tôi.

Nếu việc gán lại biến là một vấn đề nghiêm trọng, thì `const` sẽ hữu ích hơn nhiều. Nhưng việc gán lại biến đơn giản là không phải là vấn đề quá lớn xét về khía cạnh gây ra lỗi (bugs). Có một danh sách dài những thứ dẫn đến lỗi trong các chương trình, nhưng "vô tình gán lại biến" nằm ở vị trí rất, rất thấp trong danh sách đó.

Kết hợp điều đó với thực tế là `const` (và `let`) được thiết kế để sử dụng trong các khối, mà các khối thì thường ngắn, nên bạn chỉ có một phạm vi rất nhỏ trong mã nguồn nơi mà một khai báo `const` có thể áp dụng được. Một `const` ở dòng 1 trong một khối mười dòng chỉ cho bạn biết điều gì đó về chín dòng tiếp theo. Và điều mà nó cho bạn biết thì vốn đã quá hiển nhiên chỉ bằng cách liếc nhìn chín dòng đó: biến không bao giờ nằm ở vế trái của dấu `=`; nó không bị gán lại.

Chỉ có vậy thôi, đó là tất cả những gì `const` thực sự làm được. Ngoài điều đó ra, nó không hữu ích cho lắm. Khi đặt cạnh sự nhầm lẫn đáng kể giữa tính bất biến của giá trị so với tính bất biến của phép gán, `const` đã đánh mất đi rất nhiều sức hấp dẫn của mình.

Một biến `let` (hoặc `var`!) không bao giờ bị gán lại thì về mặt hành vi đã là một "hằng số" rồi, ngay cả khi nó không có sự bảo đảm từ trình biên dịch. Điều đó là đủ tốt trong hầu hết các trường hợp.

### `var` *và* `let`

Trong tâm trí tôi, `const` khá hiếm khi hữu ích, vì vậy đây chỉ là cuộc đua song mã giữa `let` và `var`. Nhưng đây cũng không hẳn là một cuộc đua, bởi vì không nhất thiết chỉ có một người chiến thắng duy nhất. Cả hai đều có thể chiến thắng... trong các cuộc đua khác nhau.

Thực tế là bạn nên sử dụng cả `var` lẫn `let` trong các chương trình của mình. Chúng không thể thay thế cho nhau: bạn không nên dùng `var` ở nơi cần đến `let`, nhưng bạn cũng không nên dùng `let` ở nơi mà `var` là phù hợp nhất.

Vậy chúng ta vẫn nên sử dụng `var` ở đâu? Trong những trường hợp nào thì nó là lựa chọn tốt hơn `let`?

Thứ nhất, tôi luôn sử dụng `var` ở phạm vi cấp cao nhất của bất kỳ hàm nào, bất kể nó nằm ở đầu, giữa hay cuối hàm. Tôi cũng sử dụng `var` trong phạm vi global, mặc dù tôi luôn cố gắng giảm thiểu việc sử dụng phạm vi global.

Tại sao lại dùng `var` cho phạm vi hàm? Bởi vì đó chính xác là những gì `var` làm. Thực sự không có công cụ nào tốt hơn cho công việc tạo phạm vi hàm cho một khai báo hơn là một từ khóa khai báo đã làm chính xác điều đó suốt 25 năm qua.

Bạn *có thể* dùng `let` ở phạm vi cấp cao nhất này, nhưng nó không phải là công cụ tốt nhất cho công việc đó. Tôi cũng thấy rằng nếu bạn dùng `let` ở mọi nơi, thì sẽ khó phân biệt được khai báo nào được thiết kế để có tính cục bộ (localized) và khai báo nào dự định được sử dụng xuyên suốt hàm.

Ngược lại, tôi hiếm khi dùng `var` bên trong một khối. Đó là nhiệm vụ của `let`. Hãy dùng công cụ tốt nhất cho công việc. Nếu bạn thấy một `let`, nó cho bạn biết rằng bạn đang làm việc với một khai báo có tính cục bộ. Nếu bạn thấy `var`, nó cho bạn biết bạn đang làm việc với một khai báo trên toàn phạm vi hàm. Đơn giản vậy thôi.

```js
function getStudents(data) {
    var studentRecords = [];

    for (let record of data.records) {
        let id = `student-${ record.id }`;
        studentRecords.push({
            id,
            record.name
        });
    }

    return studentRecords;
}
```

Biến `studentRecords` được dự định sử dụng trên toàn bộ hàm. `var` là từ khóa khai báo tốt nhất để nói với người đọc điều đó. Ngược lại, `record` và `id` chỉ nhằm mục đích sử dụng trong phạm vi hẹp hơn của vòng lặp, vì vậy `let` là công cụ tốt nhất cho việc đó.

Ngoài lập luận về mặt ngữ nghĩa *công cụ tốt nhất* này, `var` còn có một vài đặc điểm khác mà trong một số trường hợp giới hạn, khiến nó trở nên mạnh mẽ hơn.

Một ví dụ là khi một vòng lặp độc quyền sử dụng một biến, nhưng mệnh đề điều kiện của nó không thể nhìn thấy các khai báo có phạm vi khối bên trong mỗi lần lặp:

```js
function commitAction() {
    do {
        let result = commit();
        var done = result && result.code == 1;
    } while (!done);
}
```

Ở đây, `result` rõ ràng chỉ được sử dụng bên trong khối lệnh, vì vậy chúng ta dùng `let`. Nhưng `done` lại hơi khác một chút. Nó chỉ hữu ích cho vòng lặp, nhưng mệnh đề `while` không thể nhìn thấy các khai báo `let` xuất hiện bên trong vòng lặp. Vì vậy, chúng ta thỏa hiệp và sử dụng `var`, nhờ đó `done` được hoist lên phạm vi bao ngoài nơi mà nó có thể được nhìn thấy.

Cách thay thế—khai báo `done` bên ngoài vòng lặp—sẽ tách biệt nó khỏi nơi nó được sử dụng lần đầu tiên, và buộc bạn phải chọn một giá trị mặc định để gán, hoặc tệ hơn là để nó không được gán giá trị và do đó trông có vẻ mơ hồ đối với người đọc. Tôi nghĩ `var` bên trong vòng lặp là thích hợp hơn ở đây.

Một đặc điểm hữu ích khác của `var` được nhìn thấy với các khai báo bên trong các khối không chủ ý (unintended blocks). Khối không chủ ý là những khối được tạo ra vì cú pháp bắt buộc phải có một khối, nhưng mục đích của lập trình viên không thực sự là tạo ra một phạm vi cục bộ. Minh họa rõ nhất cho phạm vi không chủ ý là câu lệnh `try..catch`:

```js
function getStudents() {
    try {
        // không thực sự là một phạm vi khối
        var records = fromCache("students");
    }
    catch (err) {
        // ối, dự phòng về giá trị mặc định
        var records = [];
    }
    // ..
}
```

Có những cách khác để cấu trúc đoạn mã này, đúng vậy. Nhưng tôi nghĩ đây là cách *tốt nhất*, xét trên nhiều sự đánh đổi khác nhau.

Tôi không muốn khai báo `records` (bằng `var` hoặc `let`) ở bên ngoài khối `try`, rồi sau đó gán giá trị cho nó trong một hoặc cả hai khối. Tôi thích các khai báo ban đầu luôn nằm càng gần càng tốt (lý tưởng nhất là cùng một dòng) với lần sử dụng đầu tiên của biến. Trong ví dụ đơn giản này, khoảng cách đó chỉ là một vài dòng, nhưng trong mã thực tế nó có thể lên tới nhiều dòng hơn. Khoảng cách càng lớn, càng khó để biết bạn đang gán cho biến nào từ phạm vi nào. `var` được sử dụng tại chính nơi gán giá trị thực tế sẽ làm giảm bớt sự mơ hồ.

Cũng lưu ý rằng tôi đã sử dụng `var` trong cả hai khối `try` và `catch`. Đó là vì tôi muốn phát tín hiệu cho người đọc biết rằng dù đi theo nhánh nào, `records` cũng luôn được khai báo. Về mặt kỹ thuật, điều đó hoạt động vì `var` được hoist một lần lên phạm vi hàm. Nhưng đó vẫn là một tín hiệu ngữ nghĩa tuyệt vời để nhắc nhở người đọc về những gì mà một trong hai từ khóa `var` đảm bảo. Nếu `var` chỉ được dùng trong một trong các khối, và bạn chỉ đang đọc khối còn lại, bạn sẽ không dễ dàng phát hiện ra `records` đến từ đâu.

Theo tôi, đây là một "siêu năng lực" nho nhỏ của `var`. Nó không chỉ có thể thoát khỏi các khối `try..catch` không mong muốn, mà nó còn được phép xuất hiện nhiều lần trong phạm vi của một hàm. Bạn không thể làm điều đó với `let`. Điều đó không xấu, nó thực sự là một tính năng hữu ích nho nhỏ. Hãy nghĩ về `var` giống như một lời chú thích mang tính khai báo nhắc nhở bạn, ở mỗi lần sử dụng, biến đó đến từ đâu. "À há, đúng rồi, nó thuộc về toàn bộ hàm."

Siêu năng lực chú thích lặp đi lặp lại này rất hữu ích trong các trường hợp khác:

```js
function getStudents() {
    var data = [];

    // làm gì đó với data
    // .. 50 dòng code nữa ..

    // hoàn toàn chỉ là một chú thích nhắc nhở chúng ta
    var data;

    // lại sử dụng data
    // ..
}
```

Dòng `var data` thứ hai không tái khai báo `data`, nó chỉ là một lời chú thích mang lại lợi ích cho người đọc rằng `data` là một khai báo trên toàn hàm. Bằng cách đó, người đọc không cần phải cuộn lên hơn 50 dòng code để tìm khai báo ban đầu.

Tôi hoàn toàn thoải mái với việc tái sử dụng các biến cho nhiều mục đích xuyên suốt một phạm vi hàm. Tôi cũng hoàn toàn thoải mái với việc hai lần sử dụng một biến bị cách nhau bởi khá nhiều dòng code. Trong cả hai trường hợp, khả năng "tái khai báo" (chú thích) một cách an toàn với `var` giúp đảm bảo tôi có thể biết `data` của mình đến từ đâu, bất kể tôi đang ở đâu trong hàm.

Một lần nữa, thật đáng tiếc, `let` không thể làm được điều này.

Còn có những sắc thái và kịch bản khác khi `var` mang lại sự hỗ trợ hữu ích, nhưng tôi sẽ không nói dông dài về vấn đề này nữa. Điều cần rút ra là `var` có thể hữu ích trong các chương trình của chúng ta bên cạnh `let` (và thi thoảng là `const`). Bạn có sẵn sàng sử dụng một cách sáng tạo các công cụ mà ngôn ngữ JS cung cấp để kể một câu chuyện phong phú hơn cho người đọc của mình không?

Đừng chỉ vứt bỏ một công cụ hữu ích như `var` vì ai đó làm bạn cảm thấy xấu hổ khi nghĩ rằng nó không còn "ngầu" nữa. Đừng né tránh `var` chỉ vì bạn từng bị bối rối một lần vào nhiều năm trước. Hãy học các công cụ này và sử dụng từng công cụ cho những gì chúng làm tốt nhất.

## Chuyện gì xảy ra với TDZ? (What's the Deal with TDZ?)

TDZ (vùng chết tạm thời - temporal dead zone) đã được giải thích trong Chương 5. Chúng ta đã minh họa cách nó xảy ra, nhưng chúng ta đã lướt qua lời giải thích về *lý do tại sao* việc đưa nó vào lại là cần thiết ngay từ đầu. Hãy cùng nhìn nhanh vào các động lực hình thành nên TDZ.

Một số manh mối trong câu chuyện nguồn gốc của TDZ:

* Các `const` không bao giờ được thay đổi
* Tất cả là vấn đề về thời gian
* Liệu `let` nên hoạt động giống `const` hay `var` hơn?

### Nơi mọi thứ bắt đầu

Thực ra TDZ bắt nguồn từ `const`.

Trong giai đoạn đầu phát triển ES6, TC39 đã phải quyết định xem liệu `const` (và `let`) có được hoist lên đầu các khối của chúng hay không. Họ quyết định rằng các khai báo này sẽ hoist, tương tự như cách `var` hoạt động. Nếu không làm như vậy, tôi nghĩ một phần của nỗi lo ngại là sự nhầm lẫn với việc che khuất biến ở giữa phạm vi (mid-scope shadowing), chẳng hạn như:

```js
let greeting = "Hi!";

{
    // điều gì sẽ in ra ở đây?
    console.log(greeting);

    // .. một loạt các dòng code ..

    // bây giờ che khuất biến `greeting`
    let greeting = "Hello, friends!";

    // ..
}
```

Chúng ta nên làm gì với câu lệnh `console.log(..)` đó? Liệu việc nó in ra "Hi!" có hợp lý đối với các lập trình viên JS không? Có vẻ như đó có thể là một cạm bẫy, khi việc che khuất biến chỉ có tác dụng trong nửa sau của khối, nhưng lại không có tác dụng ở nửa đầu. Đó không phải là một hành vi trực quan, mang phong cách JS cho lắm. Vì vậy `let` và `const` phải hoist lên đầu khối, hiển thị xuyên suốt toàn bộ khối.

Nhưng nếu `let` và `const` hoist lên đầu khối (giống như `var` hoist lên đầu hàm), tại sao `let` và `const` lại không tự động khởi tạo (thành `undefined`) theo cách mà `var` làm? Dưới đây là mối bận tâm chính:

```js
{
    // điều gì sẽ in ra ở đây?
    console.log(studentName);

    // về sau

    const studentName = "Frank";

    // ..
}
```

Hãy tưởng tượng rằng `studentName` không chỉ hoist lên đầu khối này, mà còn được tự động khởi tạo thành `undefined`. Trong nửa đầu của khối, `studentName` có thể được quan sát thấy là mang giá trị `undefined`, chẳng hạn như với câu lệnh `console.log(..)` của chúng ta. Khi câu lệnh `const studentName = ..` được chạm tới, lúc này `studentName` mới được gán giá trị `"Frank"`. Từ thời điểm đó trở đi, `studentName` không bao giờ có thể bị gán lại được nữa.

Nhưng, liệu có kỳ lạ hay đáng ngạc nhiên không khi một hằng số có thể quan sát thấy mang hai giá trị khác nhau, đầu tiên là `undefined`, sau đó là `"Frank"`? Điều đó dường như đi ngược lại những gì chúng ta nghĩ về ý nghĩa của một hằng số (`const`ant); nó chỉ nên được quan sát với một giá trị duy nhất.

Vì vậy... bây giờ chúng ta có một vấn đề. Chúng ta không thể tự động khởi tạo `studentName` thành `undefined` (hoặc bất kỳ giá trị nào khác). Nhưng biến đó lại phải tồn tại trong toàn bộ phạm vi. Chúng ta làm gì với khoảng thời gian từ khi nó mới bắt đầu tồn tại (đầu phạm vi) cho đến khi nó được gán giá trị?

Chúng ta gọi khoảng thời gian này là "vùng chết" (dead zone), như trong cụm từ "vùng chết tạm thời" (temporal dead zone - TDZ). Để tránh nhầm lẫn, người ta đã xác định rằng bất kỳ hình thức truy cập nào vào một biến khi nó đang ở trong TDZ đều là bất hợp pháp và phải dẫn đến lỗi TDZ.

Được rồi, dòng lập luận đó quả thực rất có lý, tôi phải thừa nhận như vậy.

### Ai đã "thả" TDZ ra cho `let`?

Nhưng đó mới chỉ là `const`. Thế còn `let` thì sao?

Chà, TC39 đã đưa ra quyết định: vì chúng ta cần TDZ cho `const`, nên chúng ta cũng áp dụng luôn TDZ cho `let`. *Trên thực tế, nếu chúng ta làm cho let có TDZ, thì chúng ta sẽ ngăn cản được tất cả những trò hoisting biến xấu xí mà mọi người hay làm.* Vì vậy, có một góc nhìn về tính nhất quán và có lẽ là một chút nỗ lực định hướng hành vi (social engineering) nhằm thay đổi thói quen của các lập trình viên.

Lập luận phản biện của tôi sẽ là: nếu bạn đang ưu tiên tính nhất quán, hãy nhất quán với `var` thay vì `const`; `let` chắc chắn giống `var` hơn là `const`. Điều đó càng đúng hơn khi họ đã chọn sự nhất quán với `var` cho toàn bộ việc hoisting lên đầu phạm vi. Hãy để `const` là trường hợp đặc thù riêng với TDZ, và để câu trả lời cho TDZ thuần túy là: chỉ cần tránh TDZ bằng cách luôn khai báo các hằng số của bạn ở đầu phạm vi. Tôi nghĩ điều này đáng lẽ sẽ hợp lý hơn.

Nhưng than ôi, mọi chuyện lại không diễn ra theo cách đó. `let` có TDZ vì `const` cần TDZ, và vì `let` cùng `const` bắt chước `var` trong việc hoisting lên đầu phạm vi (khối). Đó, câu chuyện là như vậy. Quá lòng vòng ư? Hãy đọc lại nó thêm vài lần nữa xem.

## Liệu Callback đồng bộ có còn là Closure không? (Are Synchronous Callbacks Still Closures?)

Chương 7 đã trình bày hai mô hình khác nhau để tiếp cận closure:

* Closure là một thực thể hàm ghi nhớ các biến bên ngoài của nó ngay cả khi hàm đó được truyền đi xung quanh và **được gọi trong** các phạm vi khác.

* Closure là một thực thể hàm và môi trường phạm vi của nó được bảo toàn tại chỗ trong khi bất kỳ tham chiếu nào trỏ tới nó được truyền đi xung quanh và **được gọi từ** các phạm vi khác.

Các mô hình này không quá khác biệt nhau, nhưng chúng tiếp cận từ góc nhìn khác nhau. Và góc nhìn khác nhau đó sẽ làm thay đổi những gì chúng ta nhận diện là một closure.

Đừng để bị lạc khi lần theo lối mòn này qua các closure và callback:

* Gọi ngược lại (call back) cái gì (hoặc ở đâu)?
* Có lẽ "callback đồng bộ" không phải là nhãn dán tốt nhất
* Các hàm ***IIF*** không di chuyển xung quanh, tại sao chúng lại cần closure?
* Trì hoãn theo thời gian là chìa khóa của closure

### Callback là gì?

Trước khi chúng ta xem xét lại closure, hãy để tôi dành một chút thời gian để nói về từ "callback". Một chuẩn mực thường được chấp nhận là khi nói "callback" thì đồng nghĩa với cả *callback bất đồng bộ* (asynchronous callbacks) lẫn *callback đồng bộ* (synchronous callbacks). Tôi không nghĩ mình đồng ý rằng đây là một ý hay, vì vậy tôi muốn giải thích lý do tại sao và đề xuất chúng ta nên chuyển từ thuật ngữ đó sang một thuật ngữ khác.

Trước tiên, hãy xem xét một *callback bất đồng bộ*, một tham chiếu hàm sẽ được gọi tại một thời điểm nào đó trong tương lai (*later*). "Callback" (gọi lại) có nghĩa là gì trong trường hợp này?

Nó có nghĩa là đoạn mã hiện tại đã kết thúc hoặc tạm dừng, tự đình chỉ chính nó, và khi hàm được đề cập được gọi sau đó, quá trình thực thi sẽ quay trở lại chương trình đã tạm dừng, tiếp tục chạy nó. Cụ thể, điểm tái gia nhập chính là đoạn mã được bao bọc trong tham chiếu hàm:

```js
setTimeout(function waitForASecond(){
    // đây là nơi JS sẽ gọi ngược lại (call back) vào
    // chương trình khi bộ định thời gian đã trôi qua
},1000);

// đây là nơi chương trình hiện tại kết thúc
// hoặc tạm đình chỉ
```

Trong ngữ cảnh này, "gọi lại" (calling back) rất có ý nghĩa. JS engine đang tiếp tục chương trình đã tạm dừng của chúng ta bằng cách *gọi ngược trở lại* tại một vị trí cụ thể. Được rồi, như vậy một callback mang tính bất đồng bộ.

### Callback đồng bộ?

Nhưng còn *callback đồng bộ* thì sao? Hãy xem xét:

```js
function getLabels(studentIDs) {
    return studentIDs.map(
        function formatIDLabel(id){
            return `Student ID: ${
               String(id).padStart(6)
            }`;
        }
    );
}

getLabels([ 14, 73, 112, 6 ]);
// [
//    "Student ID: 000014",
//    "Student ID: 000073",
//    "Student ID: 000112",
//    "Student ID: 000006"
// ]
```

Chúng ta có nên gọi `formatIDLabel(..)` là một callback không? Tiện ích `map(..)` có thực sự *gọi ngược lại* (calling back) vào chương trình của chúng ta bằng cách thực thi hàm mà chúng ta đã cung cấp không?

Bản thân nó chẳng có gì để *gọi ngược lại* cả, bởi vì chương trình chưa hề tạm dừng hay thoát ra. Chúng ta đang truyền một hàm (tham chiếu hàm) từ một phần này của chương trình sang một phần khác của chương trình, và sau đó nó được gọi ngay lập tức.

Có những thuật ngữ đã được thiết lập khác có thể phù hợp với những gì chúng ta đang làm—truyền vào một hàm (tham chiếu hàm) để một phần khác của chương trình có thể thay mặt chúng ta gọi nó. Bạn có thể nghĩ về điều này như *Dependency Injection* (DI - Tiêm phụ thuộc) hoặc *Inversion of Control* (IoC - Đảo ngược điều khiển).

DI có thể được tóm tắt là việc truyền các phần chức năng cần thiết vào một phần khác của chương trình để nó có thể gọi chúng nhằm hoàn thành công việc của mình. Đó là một mô tả khá chuẩn xác cho lệnh gọi `map(..)` ở trên, phải không? Tiện ích `map(..)` biết cách lặp qua các giá trị của danh sách, nhưng nó không biết phải *làm gì* với những giá trị đó. Đó là lý do tại sao chúng ta truyền hàm `formatIDLabel(..)` cho nó. Chúng ta truyền vào phụ thuộc (dependency).

IoC là một khái niệm khá giống và có liên quan. Đảo ngược điều khiển có nghĩa là thay vì khu vực hiện tại của chương trình kiểm soát những gì đang diễn ra, bạn bàn giao quyền kiểm soát cho một phần khác của chương trình. Chúng ta đã bọc logic tính toán chuỗi nhãn trong hàm `formatIDLabel(..)`, sau đó bàn giao quyền kiểm soát việc thực thi cho tiện ích `map(..)`.

Đáng chú ý, Martin Fowler coi IoC là điểm khác biệt giữa một framework và một library: với library, bạn gọi các hàm của nó; với framework, nó gọi các hàm của bạn. [^fowlerIOC]

Trong ngữ cảnh thảo luận của chúng ta, cả DI hoặc IoC đều có thể hoạt động như một nhãn thay thế cho *callback đồng bộ*.

Nhưng tôi có một đề xuất khác. Hãy gọi (các hàm trước đây được gọi là) *callback đồng bộ* là các *hàm được gọi trung gian* - inter-invoked functions (IIFs). Vâng, chính xác, tôi đang chơi chữ dựa trên IIFE. Những loại hàm này được *gọi trung gian* (inter-invoked), có nghĩa là: một thực thể khác gọi chúng, trái ngược với IIFE, vốn tự gọi chính nó ngay lập tức.

Mối quan hệ giữa một *callback bất đồng bộ* và một IIF là gì? Một *callback bất đồng bộ* là một IIF được gọi một cách bất đồng bộ thay vì đồng bộ.

### Closure đồng bộ?

Bây giờ chúng ta đã gán nhãn lại cho *callback đồng bộ* thành IIF, chúng ta có thể quay lại câu hỏi chính của mình: liệu IIF có phải là một ví dụ về closure không? Rõ ràng, IIF sẽ phải tham chiếu đến (các) biến từ một phạm vi bao ngoài thì nó mới có cơ hội trở thành một closure. Hàm IIF `formatIDLabel(..)` ở phần trước không tham chiếu đến bất kỳ biến nào bên ngoài phạm vi của chính nó, vì vậy nó chắc chắn không phải là một closure.

Thế còn một IIF có các tham chiếu bên ngoài thì sao, đó có phải là closure không?

```js
function printLabels(labels) {
    var list = document.getElementById("labelsList");

    labels.forEach(
        function renderLabel(label){
            var li = document.createElement("li");
            li.innerText = label;
            list.appendChild(li);
        }
    );
}
```

Hàm IIF `renderLabel(..)` bên trong tham chiếu đến `list` từ phạm vi bao quanh nó, vì vậy nó là một IIF *có thể* có closure. Nhưng đây là nơi mà định nghĩa/mô hình chúng ta chọn cho closure phát huy sự khác biệt:

* Nếu `renderLabel(..)` là một **hàm được truyền đi nơi khác**, và hàm đó sau đó được gọi, thì đúng, `renderLabel(..)` đang thực thi một closure, bởi vì closure là thứ duy trì quyền truy cập của nó vào chuỗi phạm vi ban đầu.

* Nhưng nếu, như trong mô hình khái niệm thay thế từ Chương 7, `renderLabel(..)` vẫn ở nguyên vị trí, và chỉ có một tham chiếu trỏ tới nó được truyền cho `forEach(..)`, thì liệu có cần đến closure để bảo toàn chuỗi phạm vi của `renderLabel(..)`, trong khi nó thực thi đồng bộ ngay bên trong phạm vi của chính nó không?

Không. Đó chỉ là phạm vi lexical bình thường.

Để hiểu tại sao, hãy xem xét dạng thay thế này của `printLabels(..)`:

```js
function printLabels(labels) {
    var list = document.getElementById("labelsList");

    for (let label of labels) {
        // chỉ là một lời gọi hàm bình thường trong chính
        // phạm vi của nó, đúng không? Đó không thực sự là closure!
        renderLabel(label);
    }

    // **************

    function renderLabel(label) {
        var li = document.createElement("li");
        li.innerText = label;
        list.appendChild(li);
    }
}
```

Hai phiên bản `printLabels(..)` này về cơ bản là giống nhau.

Phiên bản sau chắc chắn không phải là một ví dụ về closure, ít nhất là không theo bất kỳ ý nghĩa hữu ích hay có thể quan sát được nào. Nó chỉ đơn thuần là phạm vi lexical. Phiên bản trước, với việc `forEach(..)` gọi tham chiếu hàm của chúng ta, về cơ bản cũng là điều tương tự. Nó cũng không phải là closure, mà chỉ là một lời gọi hàm phạm vi lexical thuần túy.

### Trao quyền cho Closure (Defer to Closure)

Nhân tiện, Chương 7 đã đề cập ngắn gọn về partial application và currying (những kỹ thuật *thực sự* dựa vào closure!). Đây là một kịch bản thú vị nơi currying thủ công có thể được sử dụng:

```js
function printLabels(labels) {
    var list = document.getElementById("labelsList");
    var renderLabel = renderTo(list);

    // lần này chắc chắn là closure!
    labels.forEach( renderLabel );

    // **************

    function renderTo(list) {
        return function createLabel(label){
            var li = document.createElement("li");
            li.innerText = label;
            list.appendChild(li);
        };
    }
}
```

Hàm bên trong `createLabel(..)`, hàm mà chúng ta gán cho `renderLabel`, được đóng bao quanh `list`, vì vậy closure chắc chắn đang được tận dụng.

Closure cho phép chúng ta ghi nhớ `list` cho sau này, trong khi chúng ta trì hoãn việc thực thi logic tạo nhãn thực tế từ lời gọi `renderTo(..)` sang các lời gọi `forEach(..)` tiếp theo của IIF `createLabel(..)`. Khoảng thời gian đó có thể chỉ là một tích tắc ngắn ngủi ở đây, nhưng bất kỳ khoảng thời gian nào cũng có thể trôi qua, khi closure làm cầu nối từ lời gọi này sang lời gọi khác.

## Các biến thể Module kinh điển (Classic Module Variations)

Chương 8 đã giải thích pattern module kinh điển, có thể trông như thế này:

```js
var StudentList = (function defineModule(Student){
    var elems = [];

    var publicAPI = {
        renderList() {
            // ..
        }
    };

    return publicAPI;

})(Student);
```

Hãy chú ý rằng chúng ta đang truyền `Student` (một thực thể module khác) vào như một dependency. Nhưng có rất nhiều biến thể hữu ích trên dạng module này mà bạn có thể bắt gặp. Một số gợi ý để nhận biết các biến thể này:

* Liệu module có biết về API của chính nó không?
* Ngay cả khi chúng ta dùng một module loader phức tạp, nó vẫn chỉ là một module kinh điển
* Một số module cần phải hoạt động phổ quát (universally)

### API của tôi đâu rồi?

Đầu tiên, hầu hết các module kinh điển không định nghĩa và sử dụng một `publicAPI` theo cách tôi đã trình bày trong đoạn mã này. Thay vào đó, chúng thường trông như thế này:

```js
var StudentList = (function defineModule(Student){
    var elems = [];

    return {
        renderList() {
            // ..
        }
    };

})(Student);
```

Sự khác biệt duy nhất ở đây là việc trực tiếp trả về đối tượng đóng vai trò là public API cho module, thay vì lưu nó vào một biến `publicAPI` bên trong trước. Đây là cách mà phần lớn các module kinh điển được định nghĩa.

Nhưng cá nhân tôi rất ưa thích, và luôn tự mình sử dụng dạng `publicAPI` ở phía trước. Có hai lý do:

* `publicAPI` là một bộ mô tả mang tính ngữ nghĩa hỗ trợ khả năng đọc hiểu bằng cách làm cho mục đích của đối tượng trở nên rõ ràng hơn.

* Việc lưu trữ một biến `publicAPI` bên trong tham chiếu đến cùng một đối tượng public API bên ngoài được trả về có thể hữu ích nếu bạn cần truy cập hoặc sửa đổi API trong suốt vòng đời của module.

    Ví dụ: bạn có thể muốn gọi một trong các hàm được công khai ra ngoài từ bên trong module. Hoặc bạn có thể muốn thêm hay bớt các phương thức tùy thuộc vào các điều kiện nhất định, hoặc cập nhật giá trị của một thuộc tính được công khai.

    Bất kể trường hợp nào có thể xảy ra, đối với tôi, việc chúng ta *không* duy trì một tham chiếu để truy cập API của chính mình dường như khá ngớ ngẩn. Đúng không?

### Định nghĩa Module Bất đồng bộ - Asynchronous Module Definition (AMD)

Một biến thể khác của dạng module kinh điển là các module theo phong cách AMD (rất phổ biến vài năm trước), chẳng hạn như các module được hỗ trợ bởi tiện ích RequireJS:

```js
define([ "./Student" ],function StudentList(Student){
    var elems = [];

    return {
        renderList() {
            // ..
        }
    };
});
```

Nếu bạn nhìn kỹ vào `StudentList(..)`, đó là một hàm xuất xưởng (factory function) của module kinh điển. Bên trong cỗ máy `define(..)` (được cung cấp bởi RequireJS), hàm `StudentList(..)` được thực thi, truyền cho nó bất kỳ thực thể module nào khác được khai báo là dependencies. Giá trị trả về là một đối tượng đại diện cho public API của module.

Điều này dựa trên các nguyên tắc hoàn toàn giống nhau (bao gồm cả cách closure hoạt động!) như chúng ta đã tìm hiểu với các module kinh điển.

### Module Phổ quát - Universal Modules (UMD)

Biến thể cuối cùng chúng ta sẽ xem xét là UMD, vốn không hẳn là một định dạng cụ thể, chính xác mà giống như một tập hợp các định dạng rất tương đồng nhau. Nó được thiết kế để tạo khả năng tương tác tốt hơn (mà không cần bất kỳ công cụ build nào chuyển đổi) cho các module có thể được tải trong trình duyệt, bởi các trình tải kiểu AMD, hoặc trong Node. Cá nhân tôi vẫn xuất bản nhiều thư viện tiện ích của mình bằng một biến thể của UMD.

Dưới đây là cấu trúc điển hình của một UMD:

```js
(function UMD(name,context,definition){
    // được tải bởi một trình tải dạng AMD?
    if (
        typeof define === "function" &&
        define.amd
    ) {
        define(definition);
    }
    // trong Node?
    else if (
        typeof module !== "undefined" &&
        module.exports
    ) {
        module.exports = definition(name,context);
    }
    // giả định là script độc lập trên trình duyệt
    else {
        context[name] = definition(name,context);
    }
})("StudentList",this,function DEF(name,context){

    var elems = [];

    return {
        renderList() {
            // ..
        }
    };

});
```

Mặc dù trông có vẻ hơi khác thường, UMD thực chất chỉ là một IIFE.

Điểm khác biệt là phần biểu thức `function` chính (ở trên cùng) của IIFE chứa một loạt các câu lệnh `if..else if` để phát hiện xem module đang được tải trong môi trường nào trong số ba môi trường được hỗ trợ.

Dấu `()` cuối cùng thường dùng để gọi IIFE đang được truyền ba đối số: `"StudentsList"`, `this`, và một biểu thức `function` khác. Nếu bạn khớp các đối số đó với các tham số tương ứng, bạn sẽ thấy chúng lần lượt là: `name`, `context`, và `definition`. `"StudentList"` (`name`) là nhãn tên cho module, chủ yếu trong trường hợp nó được định nghĩa như một biến global. `this` (`context`) nhìn chung là `window` (tức là global object; xem Chương 4) để định nghĩa module theo tên của nó.

`definition(..)` được gọi để thực sự lấy định nghĩa của module, và chắc chắn bạn sẽ nhận ra rằng đó chỉ là một dạng module kinh điển!

Không còn nghi ngờ gì nữa, tại thời điểm viết cuốn sách này, ESM (ES Modules) đang trở nên phổ biến và lan rộng nhanh chóng. Nhưng với hàng triệu triệu module được viết trong suốt 20 năm qua, tất cả đều sử dụng một số biến thể tiền ESM của các module kinh điển, việc có thể đọc và hiểu chúng khi bắt gặp vẫn là điều vô cùng quan trọng.

[^fowlerIOC]: *Inversion of Control*, Martin Fowler, https://martinfowler.com/bliki/InversionOfControl.html, 26 tháng 6 năm 2005.
