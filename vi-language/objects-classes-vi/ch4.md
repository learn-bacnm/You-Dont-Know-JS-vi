# Bạn Chưa Biết JS Đâu: Đối Tượng & Lớp (Objects & Classes) - Tái bản lần 2
# Chương 4: Từ khóa This hoạt động như thế nào (This Works)

| GHI CHÚ: |
| :--- |
| Đang trong quá trình hoàn thiện (Work in progress) |

Cho đến thời điểm hiện tại, chúng ta đã thấy từ khóa `this` được sử dụng khá nhiều, nhưng vẫn chưa thực sự đi sâu tìm hiểu chính xác cách thức nó hoạt động trong JS. Đã đến lúc chúng ta phải làm điều đó.

Nhưng để hiểu đúng về `this` trong JS, bạn cần gạt bỏ mọi định kiến sẵn có, đặc biệt là những giả định xuất phát từ cách `this` hoạt động trong các ngôn ngữ lập trình khác mà bạn có thể đã từng có kinh nghiệm.

Đây là điều quan trọng nhất cần nắm được về `this`: việc xác định giá trị nào (thường là một đối tượng) mà `this` trỏ tới không được quyết định tại thời điểm viết mã (author time), mà được xác định tại thời điểm chạy (runtime). Điều đó có nghĩa là bạn không thể chỉ nhìn vào một hàm có nhận thức về `this` (`this`-aware function) — ngay cả một phương thức bên trong định nghĩa `class` — mà biết chắc chắn `this` sẽ chứa giá trị gì khi hàm đó thực thi.

Thay vào đó, bạn phải tìm từng nơi hàm đó được gọi, và quan sát *cách thức* nó được gọi (thậm chí việc nó được gọi ở *đâu* cũng không quan trọng). Đó là cách duy nhất để trả lời trọn vẹn câu hỏi `this` sẽ trỏ tới cái gì.

Trên thực tế, một hàm nhận thức về `this` duy nhất có thể được gọi theo ít nhất bốn cách khác nhau, và bất kỳ cách tiếp cận nào trong số đó cũng sẽ dẫn đến việc gán một `this` khác nhau cho lần gọi hàm cụ thể đó.

Vì vậy, câu hỏi thông thường mà chúng ta hay đặt ra khi đọc mã — "`this` trong hàm này trỏ tới cái gì?" — thực chất không phải là một câu hỏi hợp lệ. Câu hỏi bạn thực sự phải hỏi là: "Khi hàm được gọi theo một cách thức cụ thể nào đó, thì giá trị `this` nào sẽ được gán cho lần gọi ấy?"

Nếu đầu óc bạn đã bắt đầu xoay như chong chóng khi mới đọc phần giới thiệu chương này... thì tốt lắm! Hãy chuẩn bị tinh thần để tái cấu trúc lại cách bạn tư duy về `this` trong JS.

## Nhận thức về This (This Aware)

Tôi vừa dùng cụm từ "nhận thức về `this`" (`this`-aware) cách đây ít phút. Nhưng chính xác thì ý tôi là gì?

Đó là bất kỳ hàm nào có chứa từ khóa `this` bên trong nó.

Nếu một hàm không hề chứa `this` ở bất cứ đâu, thì các quy tắc về hành vi của `this` sẽ không ảnh hưởng đến hàm đó theo bất kỳ cách nào. Nhưng nếu nó *thực sự* chứa dù chỉ một từ khóa `this`, thì bạn tuyệt đối không thể xác định hàm sẽ hành xử ra sao nếu không tìm ra, đối với từng lần gọi hàm, `this` sẽ trỏ tới đâu.

Nó giống như việc từ khóa `this` là một vị trí giữ chỗ (placeholder) trong một khuôn mẫu (template). Giá trị thay thế cho vị trí giữ chỗ đó không được ấn định khi ta viết mã; nó được quyết định trong lúc mã đang chạy.

Bạn có thể nghĩ rằng tôi chỉ đang chơi chữ ở đây. Dĩ nhiên, khi bạn viết chương trình, bạn viết ra tất cả các lời gọi đến từng hàm, vậy thì bạn đã xác định `this` sẽ là gì ngay từ khi viết mã rồi, phải không? Đúng không nào!?

Không nhanh thế đâu!

Trước hết, không phải lúc nào bạn cũng viết toàn bộ phần mã gọi (invoke) (các) hàm của mình. (Các) hàm nhận thức về `this` của bạn có thể được truyền dưới dạng (các) hàm gọi lại (callback) cho một đoạn mã khác, dù là trong chính cơ sở mã của bạn, hay trong một framework/thư viện tiện ích của bên thứ ba, hay thậm chí bên trong một cơ chế tích hợp sẵn của ngôn ngữ hoặc môi trường đang lưu trữ chương trình của bạn.

Nhưng ngay cả khi bỏ qua việc truyền hàm dưới dạng callback, nhiều cơ chế trong JS vẫn cho phép các hành vi có điều kiện tại thời điểm chạy để quyết định giá trị nào (một lần nữa, thường là đối tượng) sẽ được thiết lập cho `this` của một lần gọi hàm cụ thể. Do đó, mặc dù bạn có thể đã viết toàn bộ đoạn mã đó, thì *cùng lắm* bạn cũng sẽ phải tự chạy thử trong đầu các điều kiện/nhánh rẽ khác nhau tác động đến lần gọi hàm đó.

Và tại sao tất cả những điều này lại quan trọng?

Bởi vì không chỉ riêng bạn, tác giả của đoạn mã, mới cần phải tìm hiểu những thứ này. Mà là *từng người đọc mã* của bạn, mãi mãi về sau. Nếu bất kỳ ai (kể cả chính bạn trong tương lai) muốn đọc một đoạn mã định nghĩa một hàm nhận thức về `this`, điều đó chắc chắn đồng nghĩa với việc để hiểu đầy đủ và dự đoán được hành vi của nó, người đó sẽ phải tìm kiếm, đọc và hiểu từng lần gọi của hàm đó.

### This khiến tôi bối rối (This Confuses Me)

Công bằng mà nói, điều đó cũng đúng một phần nếu chúng ta xét đến các tham số của hàm. Để hiểu cách một hàm sẽ hoạt động, chúng ta cần biết những gì đang được truyền vào nó. Vì vậy, bất kỳ hàm nào có ít nhất một tham số, theo một nghĩa tương tự, đều là hàm *nhận thức về đối số* (argument-aware) — nghĩa là, (những) đối số nào được truyền vào và gán cho (các) tham số của hàm.

Nhưng với tham số, chúng ta thường có thêm một chút gợi ý từ chính hàm đó về việc các tham số sẽ làm gì và chứa gì.

Chúng ta thường thấy tên của các tham số được khai báo ngay trong phần đầu của hàm (function header), điều này giải thích rất nhiều về bản chất/mục đích của chúng. Và nếu có các giá trị mặc định cho tham số, chúng ta thường thấy chúng được khai báo nội dòng với các mệnh đề `= giá_trị_nào_đó`. Hơn nữa, tùy thuộc vào phong cách viết mã của tác giả, chúng ta có thể thấy trong vài dòng đầu tiên của hàm một tập hợp các logic áp dụng cho các tham số này; đây có thể là các khẳng định kiểm tra giá trị (các giá trị không được phép, v.v.), hoặc thậm chí là các biến đổi (chuyển đổi kiểu, định dạng, v.v.).

Trên thực tế, `this` rất giống một tham số của hàm, nhưng nó là một tham số ngầm định (implicit parameter) chứ không phải tham số tường minh (explicit). Bạn không thấy bất kỳ dấu hiệu nào cho thấy `this` sẽ được sử dụng trong phần đầu của hàm ở bất cứ đâu. Bạn phải đọc toàn bộ thân hàm để xem `this` có xuất hiện ở nơi nào hay không.

Tên của "tham số" này luôn là `this`, vì vậy chúng ta không nhận được nhiều gợi ý về bản chất/mục đích của nó từ một cái tên chung chung như vậy. Thực tế trong lịch sử đã có rất nhiều sự nhầm lẫn về việc "this" thậm chí có nghĩa là gì. Và chúng ta hiếm khi thấy có bất cứ thao tác nào được thực hiện để xác thực/chuyển đổi/v.v. giá trị `this` áp dụng cho một lần gọi hàm. Trên thực tế, hầu như tất cả mã nhận thức về `this` mà tôi từng thấy đều mặc nhiên giả định tham số `this` đang nắm giữ chính xác giá trị mà nó mong đợi. Thật là **một cái bẫy dẫn tới những lỗi bất ngờ!**

### Vậy This là gì? (So What Is This?)

Nếu `this` là một tham số ngầm định, thì mục đích của nó là gì? Cái gì đang được truyền vào?

Hy vọng rằng bạn đã đọc cuốn sách "Scope & Closures" (Phạm vi & Bao đóng) trong bộ sách này. Nếu chưa, tôi đặc biệt khuyến khích bạn quay lại đọc cuốn sách đó sau khi đọc xong cuốn này. Trong cuốn sách đó, tôi đã giải thích chi tiết về cách thức hoạt động của phạm vi (và cả closure!), một đặc tính đặc biệt quan trọng của hàm.

Phạm vi từ vựng (lexical scope, bao gồm tất cả các biến được bao đóng lại) đại diện cho một ngữ cảnh *tĩnh* (static context) để các tham chiếu định danh từ vựng của hàm được đánh giá dựa trên đó. Nó cố định/tĩnh bởi vì tại thời điểm viết mã, khi bạn đặt các khai báo hàm và biến trong các phạm vi (lồng nhau) khác nhau, những quyết định đó đã được cố định và không bị ảnh hưởng bởi bất kỳ điều kiện runtime nào.

Ngược lại, một ngôn ngữ lập trình khác có thể cung cấp phạm vi *động* (dynamic scope), nơi mà ngữ cảnh cho các tham chiếu biến của một hàm không được xác định bởi các quyết định tại thời điểm viết mã mà bởi các điều kiện tại thời điểm chạy. Một hệ thống như vậy chắc chắn sẽ linh hoạt hơn ngữ cảnh tĩnh — mặc dù đi kèm với sự linh hoạt thường là sự phức tạp.

Nói cho rõ ràng: phạm vi của JS luôn luôn và chỉ là từ vựng và *tĩnh* (nếu chúng ta bỏ qua các mánh lới ngoài chế độ strict mode như `eval(..)` và `with`). Tuy nhiên, một trong những điều thực sự mạnh mẽ về JS là nó cung cấp một cơ chế khác có tính linh hoạt và khả năng tương tự như phạm vi *động*.

Cơ chế `this`, về mặt bản chất, chính là ngữ cảnh *động* (dynamic context, chứ không phải phạm vi - scope); đó là cách mà một hàm nhận thức về `this` có thể được gọi một cách linh động đối với các ngữ cảnh khác nhau — một điều bất khả thi với closure và các định danh phạm vi từ vựng!

### Tại sao This lại ngầm định đến vậy? (Why Is This So Implicit?)

Bạn có thể tự hỏi tại sao một thứ quan trọng như ngữ cảnh *động* lại được xử lý như một đầu vào ngầm định cho một hàm, thay vì là một đối số tường minh được truyền vào.

Đó là một câu hỏi rất quan trọng, nhưng chưa phải là câu hỏi mà chúng ta có thể trả lời trọn vẹn ngay bây giờ. Hãy tạm giữ câu hỏi đó lại nhé.

### Chúng ta có thể tiếp tục với điều này không? (Can We Get On With This?)

Vậy tại sao tôi lại dài dòng về chủ đề *này* (`this`) suốt vài trang vừa qua? Bạn đã hiểu rồi, đúng không!? Bạn đã sẵn sàng bước tiếp.

Quan điểm của tôi là: bạn, tác giả của đoạn mã, và tất cả những người đọc mã khác kể cả sau này hàng năm hay hàng thập kỷ, đều cần phải nhận thức về `this`. Đó là sự lựa chọn, là gánh nặng mà bạn đặt lên việc đọc đoạn mã đó. Và đúng vậy, điều đó cũng áp dụng cho việc chọn sử dụng `class` (xem Chương 3), vì hầu hết các phương thức của lớp sẽ nhận thức về `this` do tính tất yếu.

Hãy nhận thức về sự lựa chọn `this` *này* trong mã bạn viết. Hãy thực hiện nó một cách có chủ đích, và làm theo cách mang lại nhiều lợi ích kết quả hơn là gánh nặng. Hãy đảm bảo rằng việc sử dụng `this` trong mã của bạn *tự khẳng định được giá trị xứng đáng của nó* (carries its own weight).

Hãy để tôi diễn đạt theo cách *này*: đừng sử dụng mã nhận thức về `this` trừ khi bạn thực sự có thể biện minh cho nó, và bạn đã cân nhắc kỹ lưỡng các chi phí. Chỉ vì bạn đã thấy nhiều ví dụ mã vung vãi `this` khắp nơi trong mã của người khác, không có nghĩa là `this` thuộc về đoạn mã *này* mà bạn đang viết.

Cơ chế `this` trong JS, kết hợp với sự ủy quyền `[[Prototype]]`, là một trụ cột cực kỳ mạnh mẽ của ngôn ngữ này. Nhưng như câu nói sáo rỗng quen thuộc: "sức mạnh càng lớn, trách nhiệm càng cao". Theo kinh nghiệm cá nhân, mặc dù tôi thực sự thích và trân trọng trụ cột *này* của JS, có lẽ chưa đến 5% mã JS tôi từng viết sử dụng nó. Và khi tôi dùng, tôi dùng nó với sự tiết chế. Nó không phải là khả năng JS mặc định, sẵn sàng mang ra dùng của tôi.

## Chính là nó! (This Is It!)

Được rồi, thế là đủ cho bài thuyết giảng dài dòng. Bạn đã sẵn sàng đi sâu vào đoạn mã `this` này rồi, phải không?

Hãy xem lại (và mở rộng) `Point2d` từ Chương 3, nhưng chỉ dưới dạng một đối tượng với các thuộc tính dữ liệu và các hàm trên đó, thay vì dùng `class`:

```js
var point = {
    x: null,
    y: null,

    init(x,y) {
        this.x = x;
        this.y = y;
    },
    rotate(angleRadians) {
        var rotatedX = this.x * Math.cos(angleRadians) -
            this.y * Math.sin(angleRadians);
        var rotatedY = this.x * Math.sin(angleRadians) +
            this.y * Math.cos(angleRadians);
        this.x = rotatedX;
        this.y = rotatedY;
    },
    toString() {
        return `(${this.x},${this.y})`;
    },
};
```

Như bạn có thể thấy, các hàm `init(..)`, `rotate(..)`, và `toString()` đều có nhận thức về `this`. Bạn có thể có thói quen cho rằng tham chiếu `this` rõ ràng sẽ luôn chứa đối tượng `point`. Nhưng điều đó hoàn toàn không được đảm bảo theo bất kỳ cách nào.

Hãy luôn tự nhắc nhở bản thân khi bạn đi qua phần còn lại của chương này: giá trị `this` của một hàm được xác định bởi *cách thức* hàm đó được gọi. Điều đó có nghĩa là bạn không thể nhìn vào định nghĩa của hàm, cũng như nơi hàm được định nghĩa (thậm chí ngay cả `class` bao bọc nó!). Trên thực tế, hàm được gọi từ đâu thậm chí còn không quan trọng.

Chúng ta chỉ cần nhìn vào *cách thức* các hàm được gọi; đó là yếu tố duy nhất có ý nghĩa quyết định.

### Lời gọi với ngữ cảnh ngầm định (Implicit Context Invocation)

Hãy xem xét lời gọi này:

```js
point.init(3,4);
```

Chúng ta đang gọi hàm `init(..)`, nhưng hãy để ý dấu `point.` ở phía trước nó? Đây là một ràng buộc *ngữ cảnh ngầm định* (implicit context). Nó nói với JS: hãy gọi hàm `init(..)` với `this` tham chiếu đến `point`.

Đó là cách thức *bình thường* mà chúng ta kỳ vọng một `this` hoạt động, và đó cũng là một trong những cách phổ biến nhất mà chúng ta gọi các hàm. Vì vậy, lời gọi điển hình mang lại cho chúng ta kết quả trực quan. Đó là một điều tốt!

### Lời gọi với ngữ cảnh mặc định (Default Context Invocation)

Nhưng điều gì xảy ra nếu chúng ta làm như thế này?

```js
const init = point.init;
init(3,4);
```

Bạn có thể giả định rằng chúng ta sẽ nhận được kết quả tương tự như đoạn mã trước. Nhưng đó không phải là cách thức phép gán `this` trong JS hoạt động.

*Vị trí gọi hàm* (call-site) cho hàm này là `init(3,4)`, điều này khác với `point.init(3,4)`. Khi không có *ngữ cảnh ngầm định* (`point.`), cũng như không có bất kỳ loại cơ chế gán `this` nào khác, phép gán *ngữ cảnh mặc định* (default context) sẽ diễn ra.

Vậy `this` sẽ tham chiếu đến cái gì khi `init(3,4)` được gọi như thế?

*Nó phụ thuộc.*

Ồ không. Phụ thuộc ư? Nghe có vẻ rắc rối.

Đừng lo, nó không tệ như bạn tưởng đâu. Phép gán *ngữ cảnh mặc định* phụ thuộc vào việc đoạn mã có đang ở chế độ strict-mode hay không. Nhưng may mắn thay, hầu như toàn bộ mã JS ngày nay đều chạy trong strict-mode; ví dụ, ESM (ES Modules) luôn chạy trong strict-mode, cũng như mã bên trong một khối `class`. Và hầu như tất cả mã JS được biên dịch chuyển đổi (transpiled qua Babel, TypeScript, v.v.) đều được viết để khai báo strict-mode.

Vì vậy, gần như hầu hết thời gian, mã JS hiện đại sẽ chạy trong strict-mode, và do đó ngữ cảnh *gán mặc định* sẽ không "phụ thuộc" vào bất cứ thứ gì; nó khá đơn giản: `undefined`. Thế thôi!

| GHI CHÚ: |
| :--- |
| Hãy nhớ rằng: `undefined` không có nghĩa là "chưa được định nghĩa" (not defined); nó có nghĩa là "được định nghĩa với giá trị rỗng đặc biệt `undefined`". Tôi biết, tôi biết chứ... tên gọi và ý nghĩa không khớp nhau. Đó là gánh nặng di sản lịch sử của ngôn ngữ mà bạn phải chấp nhận. (nhún vai) |

Điều đó có nghĩa là `init(3,4)`, nếu chạy trong strict-mode, sẽ ném ra một ngoại lệ. Tại sao? Bởi vì tham chiếu `this.x` trong `init(..)` là một truy cập thuộc tính `.x` trên `undefined` (tức là `undefined.x`), điều này không được phép:

```js
"use strict";

var point = { /* .. */ };

const init = point.init;
init(3,4);
// TypeError: Cannot set properties of
// undefined (setting 'x')
```

Hãy dừng lại một chút và suy ngẫm: tại sao JS lại chọn mặc định ngữ cảnh là `undefined`, để bất kỳ lời gọi *ngữ cảnh mặc định* nào của một hàm nhận thức về `this` đều thất bại với một ngoại lệ như vậy?

Bởi vì một hàm nhận thức về `this` **luôn luôn cần một `this`**. Lời gọi `init(3,4)` không hề cung cấp một `this`, vì vậy đó *thực sự là* một sai lầm, và *nên* làm nảy sinh một ngoại lệ để sai lầm đó có thể được sửa chữa. Bài học rút ra: đừng bao giờ gọi một hàm nhận thức về `this` mà không cung cấp cho nó một `this`!

Chỉ để cho đầy đủ: trong chế độ non-strict mode ít phổ biến hơn, *ngữ cảnh mặc định* là đối tượng toàn cục (global object) — JS định nghĩa nó là `globalThis`, trong JS trên trình duyệt về cơ bản nó là một bí danh của `window`, còn trong Node nó là `global`. Vì vậy, khi `init(3,4)` chạy trong non-strict mode, biểu thức `this.x` chính là `globalThis.x` — còn được gọi là `window.x` trong trình duyệt, hoặc `global.x` trong Node. Do đó, `globalThis.x` được gán bằng `3` và `globalThis.y` được gán bằng `4`.

```js
// không có strict-mode ở đây, hãy cẩn thận!

var point = { /* .. */ };

const init = point.init;
init(3,4);

globalThis.x;   // 3
globalThis.y;   // 4
point.x;        // null
point.y;        // null
```

Điều đó thật đáng tiếc, bởi vì gần như chắc chắn đó *không* phải là kết quả mong muốn. Không chỉ tệ hại khi nó tạo ra một biến toàn cục, mà nó còn *không hề* thay đổi thuộc tính trên đối tượng `point` của chúng ta, vì vậy lỗi chương trình chắc chắn sẽ xảy ra.

| CẢNH BÁO: |
| :--- |
| Ái chà! Không ai muốn các biến toàn cục vô tình được tạo ra một cách ngầm định từ khắp mọi nơi trong mã. Bài học là: hãy luôn đảm bảo mã của bạn đang chạy trong strict-mode! |

### Lời gọi với ngữ cảnh tường minh (Explicit Context Invocation)

Các hàm cũng có thể được gọi với *ngữ cảnh tường minh* (explicit context), bằng cách sử dụng các tiện ích tích hợp sẵn `call(..)` hoặc `apply(..)`:

```js
var point = { /* .. */ };

const init = point.init;

init.call( point, 3, 4 );
// hoặc: init.apply( point, [ 3, 4 ] )

point.x;        // 3
point.y;        // 4
```

`init.call(point,3,4)` thực chất tương đương với `point.init(3,4)`, ở chỗ cả hai đều gán `point` làm ngữ cảnh `this` cho lần gọi `init(..)`.

| GHI CHÚ: |
| :--- |
| Cả hai tiện ích `call(..)` và `apply(..)` đều nhận đối số đầu tiên là một giá trị ngữ cảnh `this`; giá trị đó hầu như luôn là một đối tượng, nhưng về mặt kỹ thuật có thể là bất kỳ giá trị nào (số, chuỗi, v.v.). Tiện ích `call(..)` nhận các đối số tiếp theo và truyền thẳng chúng tới hàm được gọi, trong khi `apply(..)` kỳ vọng đối số thứ hai của nó là một mảng các giá trị để truyền làm đối số. |

Có vẻ kỳ cục khi dự tính gọi một hàm theo phong cách gán *ngữ cảnh tường minh* (`call(..)` / `apply(..)`) trong chương trình của bạn. Nhưng nó hữu ích hơn nhiều so với cái nhìn ban đầu.

Hãy nhớ lại đoạn mã gốc:

```js
var point = {
    x: null,
    y: null,

    init(x,y) {
        this.x = x;
        this.y = y;
    },
    rotate(angleRadians) { /* .. */ },
    toString() {
        return `(${this.x},${this.y})`;
    },
};

point.init(3,4);

var anotherPoint = {};
point.init.call( anotherPoint, 5, 6 );

point.x;                // 3
point.y;                // 4
anotherPoint.x;         // 5
anotherPoint.y;         // 6
```

Bạn có thấy điều tôi vừa làm ở đó không?

Tôi muốn định nghĩa `anotherPoint`, nhưng tôi không muốn lặp lại các định nghĩa của các hàm `init(..)`, `rotate(..)`, và `toString()` từ `point`. Vì vậy, tôi đã "mượn" một tham chiếu hàm, `point.init`, và thiết lập một cách tường minh đối tượng rỗng `anotherPoint` làm ngữ cảnh `this`, thông qua `call(..)`.

Khi `init(..)` đang chạy tại thời điểm đó, `this` bên trong nó sẽ tham chiếu đến `anotherPoint`, và đó là lý do tại sao các thuộc tính `x` / `y` (các giá trị tương ứng là `5` / `6`) được thiết lập ở đó.

Bất kỳ hàm nhận thức về `this` nào cũng có thể được mượn như thế này: `point.rotate.call(anotherPoint, ..)`, `point.toString.call(anotherPoint)`.

#### Xem lại lời gọi với ngữ cảnh ngầm định (Revisiting Implicit Context Invocation)

Một cách tiếp cận khác để chia sẻ hành vi giữa `point` và `anotherPoint` có thể là:

```js
var point = { /* .. */ };

var anotherPoint = {
    init: point.init,
    rotate: point.rotate,
    toString: point.toString,
};

anotherPoint.init(5,6);

anotherPoint.x;         // 5
anotherPoint.y;         // 6
```

Đây là một cách khác để "mượn" các hàm, bằng cách thêm các tham chiếu được chia sẻ tới các hàm trên bất kỳ đối tượng đích nào (ví dụ: `anotherPoint`). Lời gọi tại vị trí gọi hàm `anotherPoint.init(5,6)` là phong cách tự nhiên/tiện dụng hơn, dựa trên phép gán *ngữ cảnh ngầm định*.

Có vẻ như cách tiếp cận này sạch sẽ hơn một chút khi so sánh `anotherPoint.init(5,6)` với `point.init.call(anotherPoint,5,6)`.

Nhưng nhược điểm chính là phải sửa đổi bất kỳ đối tượng đích nào với các tham chiếu hàm chia sẻ như vậy, điều này có thể rườm rà, thủ công và dễ xảy ra lỗi. Đôi khi cách tiếp cận như vậy là chấp nhận được, nhưng nhiều khi khác, việc gán *ngữ cảnh tường minh* bằng `call(..)` / `apply(..)` lại được ưu tiên hơn.

### Lời gọi với ngữ cảnh New (New Context Invocation)

Cho đến nay chúng ta đã thấy ba cách gán ngữ cảnh khác nhau tại vị trí gọi hàm: *mặc định* (default), *ngầm định* (implicit), và *tường minh* (explicit).

Cách thứ tư để gọi một hàm, và gán `this` cho lần gọi đó, là sử dụng từ khóa `new`:

```js
var point = {
    // ..

    init: function() { /* .. */ }

    // ..
};

var anotherPoint = new point.init(3,4);

anotherPoint.x;     // 3
anotherPoint.y;     // 4
```

| MẸO: |
| :--- |
| Ví dụ này có một chút sắc thái tinh tế cần được giải thích. Cú pháp `init: function() { .. }` hiển thị ở đây — cụ thể là một biểu thức hàm (function expression) được gán cho một thuộc tính — là bắt buộc để hàm có thể được gọi một cách hợp lệ với từ khóa `new`. Từ các đoạn mã trước, dạng phương thức súc tích `init() { .. }` định nghĩa một hàm *không thể* được gọi với `new`. |

Bạn thường thấy `new` được sử dụng với `class` để tạo các thể hiện (instance). Nhưng với tư cách là một cơ chế nền tảng bên dưới của ngôn ngữ JS, `new` về bản chất không phải là một thao tác dành riêng cho `class`.

Theo một nghĩa nào đó, từ khóa `new` "chiếm quyền điều khiển" (hijack) một hàm và ép hành vi của nó vào một chế độ khác so với một lời gọi thông thường. Dưới đây là 4 bước đặc biệt mà JS thực hiện khi một hàm được gọi với `new`:

1. tạo ra một đối tượng rỗng hoàn toàn mới, từ hư không.

2. liên kết thuộc tính `[[Prototype]]` của đối tượng rỗng mới đó với đối tượng `.prototype` của hàm (xem Chương 2).

3. gọi hàm với ngữ cảnh `this` được thiết lập trỏ tới đối tượng rỗng mới đó.

4. nếu hàm không trả về giá trị đối tượng của riêng nó một cách tường minh (bằng câu lệnh `return ..`), mặc định coi lời gọi hàm đó sẽ trả về đối tượng mới (từ các bước 1-3).

| CẢNH BÁO: |
| :--- |
| Bước 4 ngụ ý rằng nếu bạn gọi `new` trên một hàm mà hàm đó *thực sự* trả về đối tượng của riêng nó — như `return { .. }`, v.v. — thì đối tượng mới từ các bước 1-3 sẽ *không* được trả về. Đó là một cạm bẫy ranh mãnh cần chú ý, vì nó thực sự vứt bỏ đối tượng mới đó trước khi chương trình có cơ hội nhận và lưu trữ tham chiếu đến nó. Về cơ bản, `new` không bao giờ nên được dùng để gọi một hàm có (các) câu lệnh `return ..` tường minh bên trong. |

Để hiểu 4 bước của `new` này một cách cụ thể hơn, tôi sẽ minh họa chúng bằng mã, như một cách thay thế cho việc sử dụng từ khóa `new`:

```js
// giải pháp thay thế cho:
//   var anotherPoint = new point.init(3,4)

var anotherPoint;
// đây là một khối lệnh trần để ẩn các khai báo
// `let` cục bộ
{
    // (Bước 1)
    let tmpObj = {};

    // (Bước 2)
    Object.setPrototypeOf(
        tmpObj, point.init.prototype
    );
    // hoặc: tmpObj.__proto__ = point.init.prototype

    // (Bước 3)
    let res = point.init.call(tmpObj,3,4);

    // (Bước 4)
    anotherPoint = (
        typeof res !== "object" ? tmpObj : res
    );
}
```

Rõ ràng, lời gọi bằng `new` đã tinh gọn chuỗi các bước thủ công đó!

| MẸO: |
| :--- |
| Lệnh `Object.setPrototypeOf(..)` trong bước 2 cũng có thể được thực hiện thông qua thuộc tính `__proto__`, chẳng hạn như `tmpObj.__proto__ = point.init.prototype`, hoặc thậm chí như một phần của đối tượng nguyên mẫu (bước 1) với `tmpObj = { __proto__: point.init.prototype }`. |

Bỏ qua một số thủ tục hình thức của các bước này, hãy nhớ lại một đoạn mã trước đó và xem cách `new` đạt được kết quả tương tự:

```js
var point = { /* .. */ };

// cách tiếp cận này:
var anotherPoint = {};
point.init.call(anotherPoint,5,6);

// có thể được thực hiện tương đương như sau:
var yetAnotherPoint = new point.init(5,6);
```

Cách đó trông gọn gàng hơn một chút! Nhưng có một cảnh báo ở đây.

Việc sử dụng các hàm khác mà `point` nắm giữ đối với `anotherPoint` / `yetAnotherPoint`, chúng ta sẽ không muốn thực hiện với `new`. Tại sao? Bởi vì `new` đang tạo ra một đối tượng *mới*, nhưng đó không phải là điều chúng ta muốn nếu chúng ta có ý định gọi một hàm đối với một đối tượng đã tồn tại.

Thay vào đó, chúng ta có thể sẽ sử dụng phép gán *ngữ cảnh tường minh*:

```js
point.rotate.call( anotherPoint, /*angleRadians=*/Math.PI );

point.toString.call( yetAnotherPoint );
// (5,6)
```

### Điểm lại về This (Review This)

Chúng ta đã thấy bốn quy tắc gán ngữ cảnh `this` trong các lời gọi hàm. Hãy sắp xếp chúng theo thứ tự ưu tiên:

1. Hàm có được gọi với `new` hay không, để tạo và thiết lập một `this` *mới*?

2. Hàm có được gọi với `call(..)` hoặc `apply(..)` hay không, để thiết lập `this` một cách *tường minh*?

3. Hàm có được gọi với một tham chiếu đối tượng tại vị trí gọi hàm hay không (ví dụ: `point.init(..)`), để thiết lập `this` một cách *ngầm định*?

4. Nếu không thuộc các trường hợp trên... chúng ta có đang ở chế độ non-strict mode không? Nếu có, hãy gán *mặc định* `this` thành `globalThis`. Nhưng nếu đang ở chế độ strict-mode, hãy gán *mặc định* `this` thành `undefined`.

Các quy tắc này, *theo đúng thứ tự này*, là cách thức JS xác định `this` cho một lần gọi hàm. Nếu nhiều quy tắc cùng khớp với một vị trí gọi hàm (ví dụ: `new point.init.call(..)`), quy tắc đầu tiên trong danh sách khớp sẽ chiến thắng.

Thế là xong, bây giờ bạn đã làm chủ từ khóa `this`. Ồ, chưa hẳn đâu. Còn rất nhiều sắc thái cần được đề cập. Nhưng bạn đang đi đúng hướng rồi đấy!

## Mũi tên trỏ tới đâu đó (An Arrow Points Somewhere)

Mọi điều tôi đã khẳng định cho đến nay về `this` trong các hàm, và cách nó được xác định dựa trên vị trí gọi hàm, đều dựa trên một giả định khổng lồ: đó là bạn đang làm việc với một hàm (hoặc phương thức) *thông thường* (regular function).

Vậy một hàm *bất thường* (irregular function) là gì?!? Trông nó như thế này:

```js
const x = x => x <= x;
```

| GHI CHÚ: |
| :--- |
| Vâng, tôi đang hơi mỉa mai và không công bằng một chút khi gọi một arrow function là "bất thường" và sử dụng một ví dụ gượng ép như vậy. Đó là một trò đùa thôi, được chứ? |

Dưới đây là một ví dụ thực tế về một arrow function `=>`:

```js
const clickHandler = evt =>
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();
```

Để so sánh, hãy để tôi hiển thị phiên bản tương đương không dùng arrow:

```js
const clickHandler = function(evt) {
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();
};
```

Hoặc nếu chúng ta quay về phong cách cổ điển một chút — đây là phong cách ruột của tôi! — chúng ta có thể thử dạng khai báo hàm độc lập:

```js
function clickHandler(evt) {
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();
}
```

Hoặc nếu hàm xuất hiện dưới dạng một phương thức trong định nghĩa `class`, hoặc dưới dạng một phương thức súc tích trong một đối tượng nguyên mẫu, nó sẽ trông như thế này:

```js
// ..
clickHandler(evt) {
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();
}
```

Điều tôi thực sự muốn tập trung vào là cách mỗi dạng hàm này sẽ hành xử liên quan đến tham chiếu `this` của chúng, và liệu dạng `=>` đầu tiên có khác biệt so với các dạng khác hay không (gợi ý: có đấy!). Nhưng hãy bắt đầu với một câu đố nhỏ để xem bạn có đang chú ý theo dõi hay không.

Đối với mỗi dạng hàm vừa hiển thị, làm thế nào để chúng ta biết từng `this` sẽ tham chiếu đến cái gì?

### Vị trí gọi hàm ở đâu? (Where's The Call-site?)

Hy vọng rằng bạn đã trả lời đại loại như: "trước tiên, chúng ta cần xem các hàm được gọi như thế nào."

Rất chuẩn xác.

Giả sử chương trình của chúng ta trông như thế này:

```js
var infoForm = {
    theFormElem: null,
    theSubmitBtn: null,

    init() {
        this.theFormElem =
            document.getElementById("the-info-form");
        this.theSubmitBtn =
            theFormElem.querySelector("button[type=submit]");

        // đây có phải là vị trí gọi hàm không?
        this.theSubmitBtn.addEventListener(
            "click",
            this.clickHandler,
            false
        );
    },

    // ..
}
```

À, thú vị thật. Một nửa số độc giả chưa từng thấy mã DOM API thực tế như `getElementById(..)`, `querySelector(..)`, và `addEventListener(..)` bao giờ. Tôi vừa nghe thấy tiếng chuông bối rối reo lên rồi đấy!

| GHI CHÚ: |
| :--- |
| Xin lỗi nhé, tôi đang làm lộ tuổi thật của mình ở đây rồi. Tôi đã làm những thứ này đủ lâu để nhớ lại thời điểm chúng ta viết loại mã đó từ rất lâu trước khi chúng ta có các tiện ích như jQuery làm bừa bộn mã nguồn với các ký tự `$` ở khắp mọi nơi. Và sau nhiều năm tiến hóa của front-end, có vẻ như chúng ta đã cập bến một nơi nào đó "hiện đại" hơn khá nhiều — ít nhất, đó là giả định phổ biến hiện nay. |

Tôi đoán nhiều bạn ngày nay đã quen nhìn thấy mã framework thành phần (React, v.v.) đại loại như thế này:

```jsx
// ..

infoForm(props) {
    return (
        <form ref={this.theFormElem}>
            <button type=submit onClick=this.clickHandler>
                Click Me
            </button>
        </form>
    );
}

// ..
```

Tất nhiên, có rất nhiều cách khác mà đoạn mã đó có thể được định hình, tùy thuộc vào việc bạn đang sử dụng framework này hay framework khác, v.v.

Hoặc có thể bạn thậm chí không còn sử dụng các component theo phong cách `class` / `this` nữa, vì bạn đã chuyển mọi thứ sang hooks và closures. Dù thế nào đi nữa, phục vụ cho mục đích thảo luận của chúng ta, chương *này* hoàn toàn nói về `this`, vì vậy chúng ta cần bám sát phong cách viết mã như trên, để có mã liên quan đến cuộc thảo luận.

Và cả hai đoạn mã trước đó đều không hiển thị hàm `clickHandler` được định nghĩa như thế nào. Nhưng tôi đã nói đi nói lại từ đầu đến giờ, điều đó không quan trọng; tất cả những gì quan trọng là... cái gì nào? hãy đồng thanh với tôi... tất cả những gì quan trọng là hàm được gọi *như thế nào*.

Vậy `clickHandler` đang được gọi như thế nào? Vị trí gọi hàm là gì, và nó khớp với quy tắc gán ngữ cảnh nào?

### Ẩn khỏi tầm mắt (Hidden From Sight)

Nếu bạn đang bị mắc kẹt, đừng lo lắng. Tôi đang cố tình làm cho điều này trở nên khó khăn, để chỉ ra một điều rất quan trọng.

Khi các ràng buộc trình xử lý sự kiện `"click"` hoặc `onClick=` diễn ra, trong cả hai trường hợp, chúng ta đều chỉ định `this.clickHandler`, điều này ngụ ý rằng có một đối tượng ngữ cảnh `this` với một thuộc tính trên đó tên là `clickHandler`, đang nắm giữ định nghĩa hàm của chúng ta.

Vậy thì, `this.clickHandler` có phải là vị trí gọi hàm không? Nếu phải, thì quy tắc gán nào sẽ được áp dụng? Quy tắc *ngữ cảnh ngầm định* (#3) chăng?

Đáng tiếc là không phải.

Vấn đề là, **chúng ta thực sự không thể nhìn thấy vị trí gọi hàm** trong chương trình này. Ồ không.

Nếu chúng ta không thể thấy vị trí gọi hàm, làm sao chúng ta biết hàm thực sự sẽ được gọi *như thế nào*?

*Đó* chính là điểm mấu chốt mà tôi muốn nêu ra.

Việc chúng ta truyền vào `this.clickHandler` không có ý nghĩa gì cả. Đó chỉ đơn thuần là một tham chiếu đến một giá trị đối tượng hàm. Nó không phải là một vị trí gọi hàm (call-site).

Bên dưới hậu trường, ở đâu đó bên trong một framework, thư viện, hoặc thậm chí chính môi trường JS, khi người dùng nhấp vào nút bấm, một tham chiếu đến hàm `clickHandler(..)` sẽ được gọi. Và như chúng ta đã ám chỉ, vị trí gọi hàm đó thậm chí sẽ truyền đối tượng sự kiện DOM vào làm đối số `evt`.

Vì chúng ta không thể nhìn thấy vị trí gọi hàm, chúng ta phải *tưởng tượng* ra nó. Liệu nó có thể trông như thế này...?

```js
// ..
eventCallback( domEventObj );
// ..
```

Nếu đúng như vậy, quy tắc `this` nào sẽ được áp dụng? Quy tắc *ngữ cảnh mặc định* (#4) ư?

Hoặc, điều gì xảy ra nếu vị trí gọi hàm trông như thế này...?

```js
// ..
eventCallback.call( domElement, domEventObj );
```

Bây giờ quy tắc `this` nào sẽ được áp dụng? Quy tắc *ngữ cảnh tường minh* (#2) đúng không?

Trừ khi bạn mở và xem mã nguồn của framework/thư viện, hoặc đọc tài liệu/đặc tả kỹ thuật, bạn sẽ không *biết* được điều gì sẽ xảy ra tại vị trí gọi hàm đó. Điều đó có nghĩa là việc dự đoán, rốt cuộc, `this` trỏ tới cái gì trong hàm `clickHandler` mà bạn viết, là... nói một cách nhẹ nhàng... hơi bị vòng vèo rắc rối.

### *This* này đã sai (*This* Is Wrong)

Để giúp bạn không phải chịu thêm đau đớn ở đây, tôi sẽ đi thẳng vào vấn đề.

Khá nhiều triển khai của cơ chế xử lý sự kiện click sẽ làm điều gì đó tương tự như `.call(..)`, và họ sẽ thiết lập phần tử DOM (ví dụ: nút bấm) mà trình lắng nghe sự kiện được gắn vào, làm *ngữ cảnh tường minh* cho lần gọi hàm.

Hừm... điều đó có ổn không, hay nó sẽ là một vấn đề?

Hãy nhớ lại rằng hàm `clickHandler(..)` của chúng ta nhận thức về `this`, và tham chiếu `this.theFormElem` của nó ngụ ý việc tham chiếu đến một đối tượng có thuộc tính `theFormElem`, thuộc tính này lại đang trỏ tới phần tử `<form>` cha. Các nút bấm DOM, theo mặc định, không hề có thuộc tính `theFormElem` trên chúng.

Nói cách khác, tham chiếu `this` mà trình xử lý sự kiện của chúng ta được thiết lập gần như chắc chắn là sai. Rất tiếc!

Trừ khi chúng ta muốn viết lại hàm `clickHandler`, chúng ta sẽ cần phải sửa chữa điều đó.

### Sửa chữa `this` (Fixing `this`)

Hãy xem xét một số phương án để giải quyết việc gán sai này. Để giữ sự tập trung, tôi sẽ bám vào phong cách gắn sự kiện này cho cuộc thảo luận:

```js
this.submitBtn.addEventListener(
    "click",
    this.clickHandler,
    false
);
```

Đây là một cách để giải quyết nó:

```js
// lưu trữ một tham chiếu cố định tới ngữ cảnh
// `this` hiện tại
var context = this;

this.submitBtn.addEventListener(
    "click",
    function handler(evt){
        return context.clickHandler(evt);
    },
    false
);
```

| MẸO: |
| :--- |
| Hầu hết mã JS cũ hơn sử dụng cách tiếp cận này sẽ viết kiểu như `var self = this` thay vì tên `context` mà tôi đang đặt ở đây. "Self" là một từ ngắn hơn, và nghe có vẻ ngầu hơn. Nhưng nó cũng mang ý nghĩa ngữ nghĩa hoàn toàn sai lầm. Từ khóa `this` không phải là một tham chiếu "bản thân" (self) đến hàm, mà là ngữ cảnh cho lần gọi hàm hiện tại đó. Thoạt nhìn chúng có vẻ giống nhau, nhưng chúng là những khái niệm hoàn toàn khác biệt, khác nhau một trời một vực như quả táo với một bài hát của The Beatles vậy. Vì vậy... phỏng theo lời bài hát của họ: "Này lập trình viên, đừng làm mọi thứ tồi tệ đi. Hãy lấy một `self` buồn bã và biến nó thành `context` tốt hơn." |

Điều gì đang diễn ra ở đây? Tôi nhận ra rằng đoạn mã bao bọc bên ngoài, nơi lời gọi `addEventListener` sẽ chạy, có một ngữ cảnh `this` hiện tại hoàn toàn chính xác, và chúng ta cần đảm bảo cùng ngữ cảnh `this` đó được áp dụng khi `clickHandler(..)` được gọi.

Tôi đã định nghĩa một hàm bao quanh (`handler(..)`) và sau đó ép buộc vị trí gọi hàm trông như sau:

```js
context.clickHandler(evt);
```

| MẸO: |
| :--- |
| Quy tắc gán ngữ cảnh `this` nào được áp dụng ở đây? Đúng rồi, quy tắc *ngữ cảnh ngầm định* (#3). |

Bây giờ, việc vị trí gọi hàm bên trong của thư viện/framework/môi trường trông như thế nào không còn quan trọng nữa. Nhưng, tại sao?

Bởi vì bây giờ chúng ta *thực sự* nắm quyền kiểm soát vị trí gọi hàm. `handler(..)` được gọi như thế nào, hay `this` của nó được gán ra sao không còn quan trọng. Điều quan trọng duy nhất là khi `clickHandler(..)` được gọi, ngữ cảnh `this` được thiết lập thành những gì chúng ta mong muốn.

Tôi đã thực hiện được mánh khóe đó không chỉ bằng cách định nghĩa một hàm bao bọc (`handler(..)`) để có thể kiểm soát vị trí gọi hàm, mà... và điều này rất quan trọng, đừng bỏ lỡ... tôi đã định nghĩa `handler(..)` như một hàm KHÔNG nhận thức về `this`! Không có từ khóa `this` nào bên trong `handler(..)`, do đó bất kỳ `this` nào được thiết lập (hoặc không) bởi thư viện/framework/môi trường đều hoàn toàn không liên quan.

Dòng lệnh `var context = this` có tính quyết định đối với mánh khóe này. Nó định nghĩa một biến từ vựng `context`, đây không phải là một từ khóa đặc biệt nào cả, giữ một bản chụp nhanh giá trị trong `this` bên ngoài. Sau đó bên trong `clickHandler`, chúng ta chỉ đơn thuần tham chiếu đến một biến từ vựng (`context`), không hề có từ khóa `this` ma thuật/tương đối nào.

### This từ vựng (Lexical This)

Nhân tiện, tên gọi của mẫu hình này là "this từ vựng" (lexical this), có nghĩa là một `this` hoạt động giống như một biến phạm vi từ vựng thay vì giống như một ràng buộc ngữ cảnh động.

Nhưng hóa ra JS có một cách dễ dàng hơn để thực hiện trò ma thuật "lexical this". Bạn đã sẵn sàng cho màn lật mở mánh khóe chưa!?

...

Chính là arrow function `=>`! Tada!

Đúng vậy, hàm `=>`, không giống như tất cả các dạng hàm khác, đặc biệt ở chỗ nó chẳng có gì đặc biệt cả. Hay đúng hơn, nó không hề định nghĩa bất cứ điều gì đặc biệt cho hành vi của `this`.

Trong một hàm `=>`, từ khóa `this`... **không phải là một từ khóa**. Nó hoàn toàn không khác gì bất kỳ biến nào khác, như `context` hay `happyFace` hay `foobarbaz`.

Hãy để tôi minh họa điểm *này* một cách trực tiếp hơn:

```js
function outer() {
    console.log(this.value);

    // định nghĩa và trả về một hàm
    // "bên trong"
    var inner = () => {
        console.log(this.value);
    };

    return inner;
}

var one = {
    value: 42,
};
var two = {
    value: "sad face",
};

var innerFn = outer.call(one);
// 42

innerFn.call(two);
// 42   <-- không phải "sad face"
```

Lời gọi `innerFn.call(two)` đáng lẽ, đối với bất kỳ định nghĩa hàm *thông thường* nào, sẽ cho ra kết quả `"sad face"` ở đây. Nhưng vì hàm `inner` mà chúng ta định nghĩa và trả về (và gán cho `innerFn`) là một arrow function `=>` *bất thường*, nó không có hành vi `this` đặc biệt nào, mà thay vào đó có hành vi "lexical this".

Khi hàm `innerFn(..)` (tức `inner(..)`) được gọi, ngay cả khi có phép gán *ngữ cảnh tường minh* thông qua `.call(..)`, phép gán đó cũng hoàn toàn bị phớt lờ.

| GHI CHÚ: |
| :--- |
| Tôi không chắc tại sao các arrow function `=>` thậm chí lại có các phương thức `call(..)` / `apply(..)` trên chúng, vì chúng thực chất là những hàm không có tác dụng gì (silent no-op). Tôi đoán là vì tính nhất quán với các hàm thông thường. Nhưng như chúng ta sẽ thấy sau này, có những điểm không nhất quán khác giữa các hàm *thông thường* và các arrow function `=>` *bất thường*. |

Khi bắt gặp một `this` (`this.value`) bên trong một arrow function `=>`, `this` được đối xử như một biến từ vựng bình thường, không phải là một từ khóa đặc biệt. Và vì bản thân hàm đó không có biến `this` nào, JS sẽ làm những gì nó luôn làm với các biến từ vựng: nó đi lên một cấp phạm vi từ vựng — trong trường hợp này là hàm bao quanh `outer(..)`, và nó kiểm tra xem có bất kỳ `this` nào được đăng ký trong phạm vi đó hay không.

May mắn thay, `outer(..)` là một hàm *thông thường*, có nghĩa là nó có một từ khóa `this` bình thường. Và lời gọi `outer.call(one)` đã gán `one` cho `this` của nó.

Vì vậy, `innerFn.call(two)` đang gọi `inner()`, nhưng khi `inner()` tra cứu một giá trị cho `this`, nó nhận được... `one`, chứ không phải `two`.

#### Trở lại với... Nút bấm (Back To The... Button)

Bạn đã nghĩ tôi sẽ chơi chữ và nói "tương lai" (future) ở đó, phải không!?

Một cách trực tiếp và thích hợp hơn để giải quyết vấn đề trước đó của chúng ta, nơi chúng ta đã dùng `var context = this` để có được một dạng hành vi "lexical this" giả lập, là sử dụng arrow function `=>`, vì tính năng thiết kế cốt lõi của nó chính là... "lexical this".

```js
this.submitBtn.addEventListener(
    "click",
    evt => this.clickHandler(evt),
    false
);
```

Bùm! Vấn đề đã được giải quyết! Thả mic xuống nào!

Hãy lắng nghe tôi về điều *này*: arrow function `=>` *không phải* — tôi xin nhắc lại, *không phải* — sinh ra để gõ ít ký tự hơn. Mục đích chính của việc hàm `=>` được thêm vào JS là cung cấp cho chúng ta hành vi "lexical this" mà không cần phải dùng đến các mánh khóe kiểu `var context = this` (hoặc tệ hơn là `var self = this`).

| MẸO: |
| :--- |
| Nếu bạn cần "lexical this", hãy luôn ưu tiên dùng arrow function `=>`. Nếu bạn không cần "lexical this", thì... arrow function `=>` có thể không phải là công cụ tốt nhất cho công việc. |

#### Thời khắc thú nhận (Confession Time)

Tôi đã luôn nói trong suốt chương này rằng bạn viết hàm như thế nào và viết hàm ở đâu *không có liên quan gì* đến việc `this` của nó sẽ được gán ra sao.

Đối với các hàm thông thường, điều đó là đúng. Nhưng khi chúng ta xem xét một arrow function `=>` bất thường, điều đó không còn hoàn toàn chính xác nữa.

Bạn có nhớ dạng `=>` ban đầu của `clickHandler` từ phần trước trong chương không?

```js
const clickHandler = evt =>
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();
```

Nếu chúng ta sử dụng dạng đó, trong cùng ngữ cảnh với việc gắn sự kiện của chúng ta, nó có thể trông như thế này:

```js
const clickHandler = evt =>
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();

this.submitBtn.addEventListener("click",clickHandler,false);
```

Rất nhiều lập trình viên thích rút gọn nó hơn nữa, thành một arrow function `=>` nội dòng (inline):

```js
this.submitBtn.addEventListener(
    "click",
    evt => evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation(),
    false
);
```

Khi chúng ta viết một arrow function `=>`, chúng ta biết chắc chắn rằng ràng buộc `this` của nó sẽ chính là ràng buộc `this` hiện tại của bất kỳ hàm bao bọc nào đang chạy, bất kể vị trí gọi hàm của arrow function `=>` trông như thế nào. Nói cách khác, *cách thức* chúng ta viết arrow function `=>`, và *nơi* chúng ta viết nó, thực sự có ý nghĩa quan trọng.

Tuy nhiên, điều đó không trả lời trọn vẹn câu hỏi về `this`. Nó chỉ chuyển câu hỏi sang việc *hàm bao bọc được gọi như thế nào*. Thực ra, việc tập trung vào vị trí gọi hàm vẫn là yếu tố duy nhất mang tính quyết định.

Nhưng sắc thái mà tôi thú nhận là đã lược bỏ cho đến thời điểm *này* là: việc chúng ta xem xét vị trí gọi hàm *nào* mới là quan trọng, chứ không phải *bất kỳ* vị trí gọi hàm nào trong ngăn xếp lệnh gọi (call stack) hiện tại. Vị trí gọi hàm quan trọng chính là lời gọi hàm gần nhất trong call stack hiện tại ***thực sự thực hiện việc gán ngữ cảnh `this`***.

Vì một arrow function `=>` không bao giờ có một vị trí gọi hàm thực hiện gán `this` (dù thế nào đi nữa), vị trí gọi hàm đó không liên quan đến câu hỏi. Chúng ta phải tiếp tục bước ngược lên call stack cho đến khi tìm thấy một lời gọi hàm *có* thực hiện gán `this` — ngay cả khi bản thân hàm được gọi đó không nhận thức về `this`.

**ĐÓ** mới là vị trí gọi hàm duy nhất có ý nghĩa.

#### Tìm đúng vị trí gọi hàm (Find The Right Call-Site)

Hãy để tôi minh họa bằng một mớ hỗn độn phức tạp gồm một loạt các hàm/lời gọi lồng nhau:

```js
globalThis.value = { result: "Sad face" };

function one() {
    function two() {
        var three = {
            value: { result: "Hmmm" },

            fn: () => {
                const four = () => this.value;
                return four.call({
                    value: { result: "OK", },
                });
            },
        };
        return three.fn();
    };
    return two();
}

new one();          // ???
```

Bạn có thể chạy thử qua (cơn ác mộng) đó trong đầu và xác định xem điều gì sẽ được trả về từ lời gọi `new one()` không?

Nó có thể là bất kỳ giá trị nào trong số này:

```js
// từ `four.call(..)`:
{ result: "OK" }

// hoặc, từ đối tượng `three`:
{ result: "Hmmm" }

// hoặc, từ `globalThis.value`:
{ result: "Sad face" }

// hoặc, đối tượng rỗng từ lời gọi `new`:
{}
```

Call stack cho lời gọi `new one()` đó là:

```
four         |
three.fn     |
two          | (this = globalThis)
one          | (this = {})
[ global ]   | (this = globalThis)
```

Vì `four()` và `fn()` đều là các arrow function `=>`, các vị trí gọi hàm `three.fn()` và `four.call(..)` không thực hiện gán `this`; do đó, chúng không liên quan đến truy vấn của chúng ta. Lời gọi tiếp theo cần xem xét trong call stack là gì? `two()`. Đó là một hàm thông thường (nó có thể tiếp nhận phép gán `this`), và vị trí gọi hàm khớp với quy tắc gán *ngữ cảnh mặc định* (#4). Vì chúng ta không ở trong strict-mode, `this` được gán là `globalThis`.

Khi `four()` đang chạy, `this` chỉ là một biến bình thường. Sau đó nó nhìn vào hàm chứa nó (`three.fn()`), nhưng nó lại thấy một hàm không có `this`. Vì vậy, nó đi lên một cấp độ khác, và tìm thấy một hàm *thông thường* `two()` có định nghĩa `this`. Và `this` đó là `globalThis`. Do đó, biểu thức `this.value` phân giải thành `globalThis.value`, trả về cho chúng ta... `{ result: "Sad face" }`.

...

Hãy hít thở sâu một hơi. Tôi biết rằng có rất nhiều thứ cần phải xử lý trong đầu. Và công bằng mà nói, đó là một ví dụ cực kỳ gượng ép. Bạn sẽ gần như không bao giờ thấy tất cả sự phức tạp đó trộn lẫn trong một call stack duy nhất.

Nhưng bạn chắc chắn sẽ bắt gặp các call stack hỗn hợp trong các chương trình thực tế. Bạn cần phải thoải mái với kiểu phân tích mà tôi vừa minh họa, để có thể lần ngược call stack cho đến khi tìm thấy vị trí gọi hàm gán `this` gần đây nhất.

Hãy nhớ câu ngạn ngữ tôi đã trích dẫn trước đó: "sức mạnh càng lớn, trách nhiệm càng cao". Việc lựa chọn mã theo định hướng `this` (ngay cả `class`) đồng nghĩa với việc lựa chọn cả tính linh hoạt mà nó mang lại cho chúng ta, cũng như việc cần phải thành thạo điều hướng call stack để hiểu cách nó sẽ hành xử.

Đó là cách duy nhất để viết (và sau này là đọc!) mã nhận thức về `this` một cách hiệu quả.

### Ràng buộc là điều tất yếu (This Is Bound To Come Up)

Lùi lại một chút, có một tùy chọn khác nếu bạn không muốn sử dụng hành vi "lexical this" của arrow function `=>` để xử lý chức năng của trình xử lý sự kiện nút bấm.

Ngoài `call(..)` / `apply(..)` — hãy nhớ rằng chúng trực tiếp gọi hàm! — các hàm JS còn có một tiện ích thứ ba được tích hợp sẵn, được gọi là `bind(..)` — xin nói rõ là nó *không* gọi hàm.

Tiện ích `bind(..)` định nghĩa một phiên bản hàm bao bọc/được ràng buộc *mới*, trong đó `this` của nó được thiết lập sẵn và cố định, và không thể bị ghi đè bằng một `call(..)` hay `apply(..)`, hoặc thậm chí bởi một đối tượng *ngữ cảnh ngầm định* tại vị trí gọi hàm:

```js
this.submitBtn.addEventListener(
    "click",
    this.clickHandler.bind(this),
    false
);
```

Vì tôi đang truyền vào một hàm đã được ràng buộc `this` làm trình xử lý sự kiện, nên việc tiện ích đó cố gắng thiết lập `this` như thế nào cũng không còn quan trọng nữa, bởi vì tôi đã ép buộc `this` thành những gì tôi mong muốn: giá trị của `this` từ ngữ cảnh gọi hàm bao quanh.

#### Ràng buộc cứng đâu có mới (Hardly New)

Mẫu hình này thường được gọi là "ràng buộc cứng" (hard binding), vì chúng ta đang tạo ra một tham chiếu hàm được liên kết chặt chẽ với một `this` cụ thể. Rất nhiều bài viết về JS đã tuyên bố rằng arrow function `=>` về cơ bản chỉ là cú pháp viết tắt cho việc ràng buộc cứng `bind(this)`. Không phải vậy đâu. Hãy cùng tìm hiểu sâu hơn.

Nếu bạn định tự tạo ra một tiện ích `bind(..)`, nó có thể trông đại loại như *thế này*:

```js
function bind(fn,context) {
    return function bound(...args){
        return fn.apply(context,args);
    };
}
```

| GHI CHÚ: |
| :--- |
| Đây không phải là cách `bind(..)` thực sự được triển khai trong công cụ JS. Hành vi thực tế tinh vi và có nhiều sắc thái hơn. Tôi chỉ đang minh họa một phần hành vi của nó trong đoạn mã này. |

Trông có quen không? Nó đang sử dụng mánh khóe "lexical this" giả lập quen thuộc thời xưa. Và bên dưới hậu trường, nó là một phép gán *ngữ cảnh tường minh*, trong trường hợp này là thông qua `apply(..)`.

Khoan đã... chẳng phải điều đó có nghĩa là chúng ta có thể làm điều đó đơn giản bằng một arrow function `=>` sao?

```js
function bind(fn,context) {
    return (...args) => fn.apply(context,args);
}
```

Hừm... không hẳn đâu. Giống như hầu hết mọi thứ trong JS, có một chút sắc thái tinh vi. Hãy để tôi minh họa:

```js
// triển khai ứng viên, dùng để so sánh
function fakeBind(fn,context) {
    return (...args) => fn.apply(context,args);
}

// đối tượng thử nghiệm
function thisAwareFn() {
    console.log(`Value: ${this.value}`);
}

// dữ liệu đối chứng
var obj = {
    value: 42,
};

// thí nghiệm
var f = thisAwareFn.bind(obj);
var g = fakeBind(thisAwareFn,obj);

f();            // Value: 42
g();            // Value: 42

new f();        // Value: undefined
new g();        // <--- ???
```

Đầu tiên, hãy nhìn vào lời gọi `new f()`. Phải thừa nhận rằng đó là một cách sử dụng kỳ lạ, khi gọi `new` trên một hàm đã được ràng buộc cứng. Có lẽ rất hiếm khi bạn làm như vậy. Nhưng nó cho thấy một điều khá thú vị. Mặc dù `f()` đã được ràng buộc cứng vào ngữ cảnh `this` của `obj`, toán tử `new` vẫn có thể chiếm quyền điều khiển `this` của hàm ràng buộc cứng đó và liên kết lại nó với đối tượng mới được tạo ra và còn rỗng. Đối tượng đó không có thuộc tính `value`, đó là lý do tại sao chúng ta thấy `"Value: undefined"` được in ra.

Nếu bạn cảm thấy điều đó thật kỳ lạ, tôi đồng ý. Đó là một sắc thái góc cạnh quái gở. Đó không phải là thứ bạn có khả năng sẽ khai thác. Nhưng tôi chỉ ra điều này không chỉ để đố vui. Hãy tham khảo lại bốn quy tắc được trình bày trước đó trong chương này. Bạn có nhớ tôi đã khẳng định thứ tự ưu tiên của chúng, và `new` đứng đầu bảng (#1), xếp trước quy tắc gán *tường minh* `call(..)` / `apply(..)` (#2) không?

Vì chúng ta có thể coi `bind(..)` là một biến thể của quy tắc đó, bây giờ chúng ta thấy thứ tự ưu tiên đó đã được chứng minh. `new` có mức ưu tiên cao hơn, và có thể ghi đè, ngay cả đối với một hàm đã được ràng buộc cứng. Điều đó có khiến bạn nghĩ rằng hàm ràng buộc cứng có lẽ không hề "cứng" đến thế không?!

Nhưng... điều gì sẽ xảy ra với lời gọi `new g()`, vốn đang gọi `new` trên arrow function `=>` được trả về? Bạn có dự đoán kết quả tương tự như `new f()` không?

Rất tiếc phải làm bạn thất vọng.

Dòng lệnh đó thực sự sẽ ném ra một ngoại lệ, bởi vì một hàm `=>` không thể được sử dụng với từ khóa `new`.

Nhưng tại sao? Câu trả lời tốt nhất của tôi, bản thân tôi không phải là người có thẩm quyền trong TC39, là về mặt khái niệm và thực tế, một arrow function `=>` không phải là một hàm có `this` được ràng buộc cứng, mà nó là một hàm hoàn toàn không có `this`. Do đó, `new` chẳng có ý nghĩa gì đối với một hàm như vậy, vì vậy JS đơn giản là cấm nó.

| GHI CHÚ: |
| :--- |
| Nhớ lại phần trước, khi tôi chỉ ra rằng các arrow function `=>` có `call(..)`, `apply(..)`, và thực sự thậm chí cả `bind(..)`. Nhưng chúng ta đã thấy rằng các hàm như vậy về cơ bản bỏ qua các tiện ích này như các thao tác vô tác dụng (no-ops). Theo tôi, hơi kỳ lạ khi các arrow function `=>` có tất cả các tiện ích đó dưới dạng no-ops truyền qua, nhưng đối với từ khóa `new`, điều đó lại không phải là một no-op truyền qua, mà lại bị cấm đoán bằng một ngoại lệ. |

Nhưng điểm mấu chốt là: một arrow function `=>` *không phải* là một dạng cú pháp của `bind(this)`.

### Thất bại trong trận chiến này (Losing This Battle)

Trở lại một lần nữa với ví dụ về trình xử lý sự kiện nút bấm của chúng ta:

```js
this.submitBtn.addEventListener(
    "click",
    this.clickHandler,
    false
);
```

Có một mối bận tâm sâu sắc hơn mà chúng ta vẫn chưa giải quyết.

Chúng ta đã thấy một số cách tiếp cận khác nhau để xây dựng một tham chiếu hàm callback khác nhằm truyền vào đó, thay thế cho `this.clickHandler`.

Nhưng cho dù chúng ta chọn cách nào trong số đó, chúng đều đang tạo ra một hàm hoàn toàn khác theo nghĩa đen, chứ không chỉ là sửa đổi tại chỗ đối với hàm `clickHandler` hiện tại của chúng ta.

Tại sao điều đó lại quan trọng?

Trước hết, chúng ta càng tạo ra (và tạo lại) nhiều hàm, thì thời gian xử lý (dù rất nhỏ) và bộ nhớ (thường khá nhỏ) chúng ta tiêu tốn càng nhiều. Và khi chúng ta tạo lại một tham chiếu hàm, rồi vứt bỏ cái cũ đi, điều đó cũng để lại bộ nhớ chưa được thu hồi nằm vất vưởng xung quanh, gây áp lực lên bộ thu gom rác (garbage collector - GC) thường xuyên hơn, buộc nó phải tạm dừng toàn bộ vũ trụ chương trình của chúng ta trong giây lát để dọn dẹp và thu hồi bộ nhớ đó.

Nếu việc gắn trình lắng nghe sự kiện này là một thao tác diễn ra một lần duy nhất, thì chẳng có gì to tát. Nhưng nếu nó xảy ra lặp đi lặp lại nhiều lần, các tác động đến hiệu năng cấp hệ thống *có thể* bắt đầu tích tụ lại. Bạn đã bao giờ thấy một chuyển động animation vốn mượt mà bỗng nhiên bị giật khựng chưa? Rất có thể đó là do GC bắt đầu hoạt động, dọn dẹp một đống bộ nhớ có thể thu hồi.

Nhưng một mối lo ngại khác là, đối với những thứ như trình xử lý sự kiện, nếu chúng ta định gỡ bỏ một trình lắng nghe sự kiện tại một thời điểm nào đó sau này, chúng ta cần phải giữ lại tham chiếu đến chính xác cùng một hàm mà chúng ta đã gắn ban đầu. Nếu chúng ta đang sử dụng một thư viện/framework, thường (nhưng không phải luôn luôn!) họ sẽ lo liệu chi tiết công việc hậu trường bẩn thỉu đó cho bạn. Nhưng nếu không, trách nhiệm thuộc về chúng ta để đảm bảo rằng bất kể hàm nào chúng ta định gắn vào, chúng ta phải giữ lấy một tham chiếu phòng khi cần đến nó sau này.

Vì vậy, quan điểm tôi muốn đưa ra là: việc thiết lập sẵn một phép gán `this`, bất kể bạn làm theo cách nào, để nó có thể dự đoán được, đều đi kèm với một cái giá. Một cái giá ở cấp độ hệ thống và một cái giá về độ phức tạp/bảo trì chương trình. Nó *không bao giờ* miễn phí cả.

Một cách phản ứng với thực tế đó là quyết định: được rồi, chúng ta sẽ tạo sẵn toàn bộ các tham chiếu hàm đã được gán `this` đó một lần duy nhất, ngay từ đầu. Bằng cách đó, chúng ta chắc chắn sẽ giảm thiểu cả áp lực hệ thống và áp lực mã nguồn xuống mức tối thiểu.

Nghe có vẻ hợp lý đúng không? Khoan đã.

#### Ràng buộc trước ngữ cảnh hàm (Pre-Binding Function Contexts)

Nếu bạn có một tham chiếu hàm đơn lẻ cần được ràng buộc `this`, và bạn sử dụng một mũi tên `=>` hoặc một lời gọi `bind(this)`, tôi không thấy có vấn đề gì với điều đó.

Nhưng nếu hầu hết hoặc tất cả các hàm nhận thức về `this` trong một phân đoạn mã của bạn được gọi theo những cách mà `this` không phải là ngữ cảnh có thể dự đoán được như bạn mong đợi, và vì vậy bạn quyết định rằng mình cần phải ràng buộc cứng tất cả chúng... Tôi nghĩ đó là một tín hiệu cảnh báo lớn cho thấy bạn đang tiếp cận vấn đề sai cách.

Xin hãy nhớ lại cuộc thảo luận trong phần "Tránh điều này" (Avoid This) từ Chương 3, vốn bắt đầu bằng đoạn mã này:

```js
class Point2d {
    x = null
    getDoubleX = () => this.x * 2

    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
    toString() { /* .. */ }
}

var point = new Point2d(3,4);
```

Bây giờ hãy tưởng tượng chúng ta đã làm điều này với đoạn mã đó:

```js
const getX = point.getDoubleX;

// sau đó, ở một nơi khác

getX();         // 6
```

Như bạn có thể thấy, vấn đề chúng ta đang cố gắng giải quyết cũng giống hệt như những gì chúng ta đã giải quyết ở đây trong chương này. Đó là chúng ta muốn có thể gọi một tham chiếu hàm như `getX()`, và muốn nó *có nghĩa* và *hoạt động giống như* `point.getDoubleX()`. Nhưng các quy tắc của `this` trên các hàm *thông thường* lại không hoạt động theo cách đó.

Vì vậy, chúng ta đã sử dụng một arrow function `=>`. Chẳng có gì to tát, phải không!?

Sai rồi.

Vấn đề gốc rễ thực sự là chúng ta đang *muốn* hai điều mâu thuẫn nhau từ mã của mình, và chúng ta đang cố gắng sử dụng cùng một *cây búa* cho cả hai *chiếc đinh*.

Chúng ta muốn có một phương thức nhận thức về `this` được lưu trữ trên prototype của `class`, để chỉ có một định nghĩa duy nhất cho hàm đó, và tất cả các lớp con cũng như các thể hiện của chúng ta đều chia sẻ cùng một hàm đó một cách đẹp đẽ. Và cách tất cả chúng cùng chia sẻ là thông qua sức mạnh của ràng buộc `this` động.

Nhưng đồng thời, chúng ta *cũng* muốn các tham chiếu hàm đó duy trì việc gán `this` một cách kỳ diệu cho thể hiện của chúng ta khi chúng ta truyền các tham chiếu hàm đó đi khắp nơi và các đoạn mã khác chịu trách nhiệm về vị trí gọi hàm.

Nói cách khác, đôi khi chúng ta muốn một biểu thức như `point.getDoubleX` có nghĩa là: "hãy cho tôi một tham chiếu đã được gán `this` cho `point`", và những lúc khác chúng ta lại muốn cùng biểu thức `point.getDoubleX` đó có nghĩa là: hãy cho tôi một tham chiếu hàm có thể gán `this` động để nó có thể nhận ngữ cảnh tôi cần một cách thích hợp tại thời điểm này.

Có lẽ JS có thể cung cấp một toán tử khác ngoài dấu `.`, chẳng hạn như `::` hoặc `->` hoặc thứ gì đó tương tự, cho phép bạn phân biệt loại tham chiếu hàm mà bạn đang tìm kiếm. Trên thực tế, đã có một đề xuất lâu năm về một toán tử ràng buộc `this` (`::`), thỉnh thoảng lại thu hút sự chú ý, rồi sau đó dường như lại bị đình trệ. Ai biết được, có thể một ngày nào đó một toán tử như vậy cuối cùng sẽ xuất hiện, và chúng ta sẽ có những lựa chọn tốt hơn.

Nhưng tôi rất nghi ngờ rằng ngay cả khi nó xuất hiện vào một ngày nào đó, nó cũng sẽ tạo ra một tham chiếu hàm hoàn toàn mới, chính xác như các phương pháp tiếp cận `=>` hoặc `bind(this)` mà chúng ta đã thảo luận. Nó sẽ không đến như một giải pháp hoàn hảo và miễn phí. Sẽ luôn có một sự giằng co giữa việc muốn cùng một hàm đôi khi linh hoạt về `this` và đôi khi có thể dự đoán được về `this`.

Điều mà các tác giả JS viết mã theo hướng `class` thường xuyên vấp phải, sớm hay muộn, chính là sự giằng co này. Và bạn có biết họ làm gì không?

Họ không hề xem xét *chi phí* của việc đơn giản là ràng buộc trước tất cả các phương thức nhận thức về `this` của lớp dưới dạng các arrow function `=>` trong các thuộc tính thành viên. Họ không nhận ra rằng điều đó đã hoàn toàn đánh bại toàn bộ mục đích của chuỗi `[[Prototype]]`. Và họ không nhận ra rằng nếu ngữ cảnh cố định là điều họ *thực sự cần*, thì có một cơ chế hoàn toàn khác trong JS phù hợp hơn nhiều cho mục đích đó.

#### Một cái nhìn khắt khe hơn (Take A More Critical Look)

Vì vậy, khi bạn làm loại việc như thế này:

```js
class Point2d {
    x = null
    y = null
    getDoubleX = () => this.x * 2
    toString = () => `(${this.x},${this.y})`

    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
}

var point = new Point2d(3,4);
var anotherPoint = new Point2d(5,6);

var f = point.getDoubleX;
var g = anotherPoint.toString;

f();            // 6
g();            // (5,6)
```

Tôi xin thốt lên: "eo ôi!", đối với các phương thức nhận thức về `this` bị ràng buộc cứng `getDoubleX()` và `toString()` ở đó. Đối với tôi, đó là một mùi mã (code smell). Nhưng đây là một cách tiếp cận thậm chí còn *tệ hơn* từng được nhiều lập trình viên ưa chuộng trong quá khứ:

```js
class Point2d {
    x = null
    y = null

    constructor(x,y) {
        this.x = x;
        this.y = y;
        this.getDoubleX = this.getDoubleX.bind(this);
        this.toString = this.toString.bind(this);
    }
    getDoubleX() { return this.x * 2; }
    toString() { return `(${this.x},${this.y})`; }
}

var point = new Point2d(3,4);
var anotherPoint = new Point2d(5,6);

var f = point.getDoubleX;
var g = anotherPoint.toString;

f();            // 6
g();            // (5,6)
```

Nhân đôi sự khó chịu ("double ick").

Trong cả hai trường hợp, bạn đang sử dụng cơ chế `this` nhưng lại hoàn toàn phản bội/triệt tiêu nó, bằng cách tước bỏ toàn bộ tính năng động mạnh mẽ của `this`.

Bạn thực sự ít nhất nên cân nhắc cách tiếp cận thay thế này, cách tiếp cận bỏ qua hoàn toàn toàn bộ cơ chế `this`:

```js
function Point2d(px,py) {
    var x = px;
    var y = py;

    return {
        getDoubleX() { return x * 2; },
        toString() { return `(${x},${y})`; }
    };
}

var point = Point2d(3,4);
var anotherPoint = Point2d(5,6);

var f = point.getDoubleX;
var g = anotherPoint.toString;

f();            // 6
g();            // (5,6)
```

Bạn thấy không? Không có `this` xấu xí hay phức tạp nào làm rối rắm đoạn mã đó hoặc phải lo lắng về các trường hợp góc cạnh. Phạm vi từ vựng cực kỳ đơn giản và trực quan.

Khi tất cả những gì chúng ta muốn là hầu hết/tất cả các hành vi của hàm đều có một ngữ cảnh cố định và có thể dự đoán được, thì giải pháp thích hợp nhất, giải pháp đơn giản nhất và thậm chí có hiệu năng cao nhất, chính là các biến từ vựng và bao đóng phạm vi (scope closure).

Khi bạn cất công rải các tham chiếu `this` khắp nơi trên một đoạn mã, rồi sau đó bạn lại chặt đứt đôi chân của toàn bộ cơ chế đó bằng "lexical this" `=>` hoặc `bind(this)`, bạn đã chọn làm cho mã trở nên dài dòng hơn, phức tạp hơn và cồng kềnh quá mức. Và bạn chẳng nhận lại được điều gì có lợi hơn từ nó, ngoại trừ việc chạy theo trào lưu của `this` (và `class`).

...

Hít một hơi thật sâu. Trấn tĩnh lại nào.

Tôi đang tự nói với chính mình đấy, không phải nói bạn đâu. Nhưng nếu những gì tôi vừa nói làm bạn khó chịu, thì tôi cũng đang nói với bạn đấy!

Được rồi, nghe này. Đó chỉ là ý kiến của cá nhân tôi. Nếu bạn không đồng ý, điều đó hoàn toàn bình thường. Nhưng hãy áp dụng cùng một mức độ nghiêm ngặt khi suy nghĩ về cách thức hoạt động của các cơ chế này, giống như tôi đã làm, khi bạn quyết định những kết luận mà bạn muốn đạt tới.

## Các biến thể (Variations)

Trước khi kết thúc cuộc thảo luận dài về `this`, có một vài biến thể bất thường trong các lời gọi hàm mà chúng ta nên thảo luận.

### Các lời gọi hàm gián tiếp (Indirect Function Calls)

Bạn có nhớ ví dụ này từ phần trước trong chương không?

```js
var point = {
    x: null,
    y: null,

    init(x,y) {
        this.x = x;
        this.y = y;
    },
    rotate(angleRadians) { /* .. */ },
    toString() { /* .. */ },
};

var init = point.init;
init(3,4);                  // bị hỏng!
```

Đoạn mã này bị hỏng vì vị trí gọi hàm `init(3,4)` không cung cấp tín hiệu gán `this` cần thiết. Nhưng có những cách khác để chứng kiến sự cố tương tự. Ví dụ:

```js
(1,point.init)(3,4);        // bị hỏng!
```

Cú pháp trông kỳ lạ này trước tiên đang đánh giá một biểu thức `(1,point.init)`, vốn là một biểu thức chuỗi dấu phẩy (comma series expression). Kết quả của một biểu thức như vậy là giá trị được đánh giá cuối cùng, trong trường hợp này là tham chiếu hàm (được nắm giữ bởi `point.init`).

Do đó, kết quả sẽ đưa tham chiếu hàm đó vào ngăn xếp biểu thức, rồi sau đó gọi giá trị đó với `(3,4)`. Đó là một lời gọi hàm gián tiếp. Và kết quả là gì? Nó thực sự khớp với quy tắc gán *ngữ cảnh mặc định* (#4) mà chúng ta đã xem xét trước đó trong chương.

Do đó, trong non-strict mode, `this` cho lời gọi `point.init(..)` sẽ là `globalThis`. Nếu chúng ta ở trong strict-mode, nó sẽ là `undefined`, và thao tác `this.x = x` sau đó sẽ ném ra một ngoại lệ do truy cập không hợp lệ thuộc tính `x` trên giá trị `undefined`.

Có một số cách khác nhau để tạo ra một lời gọi hàm gián tiếp. Ví dụ:

```js
(()=>point.init)()(3,4);    // bị hỏng!
```

Và một ví dụ khác về lời gọi hàm gián tiếp là mẫu biểu thức hàm được gọi ngay lập tức (IIFE - Immediately Invoked Function Expression):

```js
(function(){
    // `this` được gán thông qua quy tắc "mặc định"
})();
```

Như bạn có thể thấy, giá trị biểu thức hàm được đưa vào ngăn xếp biểu thức, và sau đó nó được gọi với `()` ở cuối.

Nhưng còn đoạn mã này thì sao:

```js
(point.init)(3,4);
```

Kết quả của đoạn mã đó sẽ là gì?

Theo cùng lập luận mà chúng ta đã thấy trong các ví dụ trước, có lý khi cho rằng biểu thức `point.init` đưa giá trị hàm vào ngăn xếp biểu thức, rồi sau đó được gọi gián tiếp với `(3,4)`.

Tuy nhiên, không hẳn như vậy! Ngữ pháp của JS có một quy tắc đặc biệt để xử lý dạng lời gọi `(someIdentifier)(..)` giống như thể nó là `someIdentifier(..)` (không có dấu `(..)` bao quanh tên định danh).

Bạn đang tự hỏi tại sao ai đó lại muốn ép buộc *ngữ cảnh mặc định* cho phép gán `this` thông qua một lời gọi hàm gián tiếp?

### Truy cập `globalThis` (Accessing `globalThis`)

Trước khi trả lời câu hỏi đó, hãy giới thiệu một cách khác để thực hiện phép gán `this` cho hàm gián tiếp. Cho đến nay, các mẫu gọi hàm gián tiếp được hiển thị đều nhạy cảm với strict-mode. Nhưng điều gì sẽ xảy ra nếu chúng ta muốn một phép gán `this` cho hàm gián tiếp không phụ thuộc vào strict-mode?

Hàm khởi tạo `Function(..)` nhận một chuỗi mã và định nghĩa động hàm tương đương. Tuy nhiên, nó luôn làm như vậy như thể hàm đó được khai báo trong phạm vi toàn cục. Và hơn nữa, nó đảm bảo hàm đó *không* chạy trong strict-mode, bất kể trạng thái strict-mode của chương trình là gì.

Một trường hợp sử dụng ngách của phép gán `this` cho hàm gián tiếp không phụ thuộc vào strict-mode như vậy là để có được một tham chiếu đáng tin cậy đến đối tượng toàn cục thực sự trước thời điểm đặc tả JS chính thức định nghĩa định danh `globalThis` (ví dụ: trong một bản polyfill cho nó):

```js
"use strict";

var gt = new Function("return this")();
gt === globalThis;                      // true
```

Trên thực tế, một kết quả tương tự có thể đạt được bằng cách sử dụng mẹo toán tử dấu phẩy (xem phần trước) và `eval(..)`:

```js
"use strict";

function getGlobalThis() {
    return (1,eval)("this");
}

getGlobalThis() === globalThis;      // true
```

| GHI CHÚ: |
| :--- |
| `eval("this")` sẽ nhạy cảm với strict-mode, nhưng `(1,eval)("this")` thì không, và do đó nó cung cấp cho chúng ta `globalThis` một cách đáng tin cậy trong bất kỳ chương trình nào. |

Thật không may, cả hai cách tiếp cận `new Function(..)` và `(1,eval)(..)` đều có một hạn chế quan trọng: đoạn mã đó sẽ bị chặn trong mã JS chạy trên trình duyệt nếu ứng dụng được phân phối với một số hạn chế nhất định của Chính sách Bảo mật Nội dung (CSP - Content-Security-Policy), vốn cấm đánh giá mã động (vì lý do bảo mật).

Chúng ta có thể vượt qua điều này không? Có, hầu như là được. [^globalThisPolyfill]

Đặc tả JS nói rằng một hàm getter được định nghĩa trên đối tượng toàn cục, hoặc trên bất kỳ đối tượng nào kế thừa từ nó (như `Object.prototype`), sẽ chạy hàm getter đó với ngữ cảnh `this` được gán cho `globalThis`, bất kể chế độ strict-mode của chương trình là gì.

```js
// Được chuyển thể từ: https://mathiasbynens.be/notes/globalthis#robust-polyfill
function getGlobalThis() {
    Object.defineProperty(Object.prototype,"__get_globalthis__",{
        get() { return this; },
        configurable: true
    });
    var gt = __get_globalthis__;
    delete Object.prototype.__get_globalthis__;
    return gt;
}

getGlobalThis() === globalThis;      // true
```

Vâng, cách đó cực kỳ ranh mãnh và gai góc. Nhưng đó chính là `this` trong JS!

### Hàm gắn thẻ mẫu chuỗi (Template Tag Functions)

Còn một biến thể lời gọi hàm bất thường nữa mà chúng ta nên đề cập: các hàm gắn thẻ mẫu (tagged template functions).

Các chuỗi mẫu (template strings) — thứ mà tôi thích gọi là các chuỗi mẫu có nội suy giá trị (interpolated literals) — có thể được "gắn thẻ" (tagged) bằng một hàm tiền tố, hàm này được gọi với nội dung đã được phân tích cú pháp của chuỗi mẫu:

```js
function tagFn(/* .. */) {
    // ..
}

tagFn`actually a function invocation!`;
```

Như bạn có thể thấy, không có cú pháp gọi hàm `(..)`, chỉ có hàm thẻ (`tagFn`) xuất hiện trước `` `template literal` ``; khoảng trắng được phép xuất hiện giữa chúng, nhưng rất hiếm thấy.

Bất chấp vẻ ngoài kỳ lạ, hàm `tagFn(..)` sẽ được gọi. Nó được truyền vào danh sách một hoặc nhiều chuỗi ký tự đã được phân tích cú pháp từ chuỗi mẫu, cùng với bất kỳ giá trị biểu thức nội suy nào bắt gặp được.

Chúng ta sẽ không đề cập đến tất cả các ngóc ngách của các hàm gắn thẻ mẫu — chúng thực sự là một trong những tính năng mạnh mẽ và thú vị nhất từng được thêm vào JS — nhưng vì chúng ta đang nói về phép gán `this` trong các lời gọi hàm, để cho đầy đủ chúng ta cần nói về cách `this` sẽ được gán.

Dạng khác của hàm thẻ mà bạn có thể bắt gặp là:

```js
var someObj = {
    tagFn() { /* .. */ }
};

someObj.tagFn`also a function invocation!`;
```

Đây là lời giải thích dễ hiểu: `` tagFn`..` `` và `` someObj.tagFn`..` `` mỗi cái sẽ có hành vi gán `this` tương ứng với các vị trí gọi hàm như `tagFn(..)` và `someObj.tagFn(..)`. Nói cách khác, `` tagFn`..` `` hành xử theo quy tắc gán *ngữ cảnh mặc định* (#4), còn `` someObj.tagFn`..` `` hành xử theo quy tắc gán *ngữ cảnh ngầm định* (#3).

May mắn cho chúng ta, chúng ta không cần phải lo lắng về các quy tắc gán `new` hoặc `call(..)` / `apply(..)`, vì những dạng đó không thể thực hiện được với các hàm thẻ.

Cần phải chỉ ra rằng khá hiếm khi một hàm gắn thẻ mẫu chuỗi được định nghĩa có nhận thức về `this`, vì vậy rất khó có khả năng bạn sẽ cần áp dụng các quy tắc này. Nhưng để đề phòng, bây giờ bạn đã *nắm rõ* rồi đấy.

## Luôn nhận biết (Stay Aware)

Vậy đó, đó là tất cả về `this`. Tôi sẵn sàng cá rằng đối với nhiều người trong số các bạn, nó có phần... phải nói sao nhỉ, phức tạp hơn... những gì bạn có thể đã mong đợi.

Tin tốt lành, có lẽ, là trong thực tế bạn không thường xuyên vấp phải tất cả những điều phức tạp khác nhau này. Nhưng bạn càng sử dụng `this` nhiều, nó càng đòi hỏi bạn và những người đọc mã của bạn phải hiểu cách nó thực sự hoạt động.

Bài học ở đây là bạn nên có chủ đích và nhận thức rõ về mọi khía cạnh của `this` trước khi rải nó khắp nơi trong mã của mình. Hãy đảm bảo bạn đang sử dụng nó một cách hiệu quả nhất và tận dụng tối đa trụ cột quan trọng này của JS.

[^globalThisPolyfill]: "A horrifying globalThis polyfill in universal JavaScript"; Mathias Bynens; Ngày 18 tháng 4 năm 2019; https://mathiasbynens.be/notes/globalthis#robust-polyfill ; Truy cập tháng 7 năm 2022

