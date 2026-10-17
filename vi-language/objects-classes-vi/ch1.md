# Bạn Chưa Biết JS Đâu: Đối Tượng & Lớp - Tái bản lần 2
# Chương 1: Nền tảng đối tượng

| GHI CHÚ: |
| :--- |
| Đang trong quá trình hoàn thiện (Work in progress) |

> Mọi thứ trong JS đều là đối tượng.

Đây là một trong những "sự thật" phổ biến nhất, nhưng cũng sai lầm nhất, liên tục được lan truyền về JS. Hãy cùng bắt đầu phá vỡ lầm tưởng này.

JS chắc chắn có các đối tượng, nhưng điều đó không đồng nghĩa với việc mọi giá trị đều là đối tượng. Dẫu vậy, đối tượng vẫn được coi là kiểu giá trị quan trọng nhất (và đa dạng nhất!) trong ngôn ngữ, vì vậy việc làm chủ chúng là điều tối quan trọng trên hành trình chinh phục JS của bạn.

Cơ chế đối tượng hiển nhiên là kiểu thùng chứa linh hoạt và mạnh mẽ nhất -- nơi bạn chứa đựng các giá trị khác; mọi chương trình JS bạn viết đều sẽ sử dụng chúng theo cách này hay cách khác. Nhưng đó không phải là lý do duy nhất khiến đối tượng xứng đáng chiếm vị trí hàng đầu trong cuốn sách này. Đối tượng là nền tảng cho trụ cột thứ hai trong ba trụ cột của JS: nguyên mẫu (prototype).

Tại sao nguyên mẫu (cùng với từ khóa `this`, sẽ được đề cập sau trong cuốn sách) lại cốt lõi đối với JS đến mức trở thành một trong ba trụ cột của nó? Trong số nhiều lý do, nguyên mẫu chính là cách mà hệ thống đối tượng của JS có thể thể hiện mẫu thiết kế lớp (class design pattern), một trong những mẫu thiết kế được dựa vào rộng rãi nhất trong toàn bộ thế giới lập trình.

Vì vậy, hành trình của chúng ta ở đây sẽ bắt đầu với các đối tượng, xây dựng sự hiểu biết trọn vẹn về nguyên mẫu, làm sáng tỏ từ khóa `this`, và khám phá hệ thống `class`.

## Về cuốn sách này

Chào mừng bạn đến với cuốn sách thứ 3 trong bộ sách *Bạn Chưa Biết JS Đâu* (You Don't Know JS Yet)! Nếu bạn đã hoàn thành cuốn *Bắt đầu* (Get Started - cuốn đầu tiên) và cuốn *Phạm vi & Bao đóng* (Scope & Closures - cuốn thứ hai), bạn đã đến đúng nơi! Nếu chưa, trước khi tiếp tục, tôi khuyến khích bạn đọc hai cuốn đó làm nền tảng trước khi đi sâu vào cuốn sách này.

Tái bản lần đầu tiên của cuốn sách này có tựa đề là "this & Object Prototypes". Trong cuốn sách đó, trọng tâm của chúng ta bắt đầu với từ khóa `this`, vì nó được cho là một trong những chủ đề gây nhầm lẫn nhất trong toàn bộ JS. Sau đó, cuốn sách đã dành phần lớn thời lượng tập trung vào việc giải thích hệ thống nguyên mẫu và ủng hộ việc áp dụng mẫu "ủy quyền" (delegation) ít được biết đến hơn thay vì các thiết kế lớp. Vào thời điểm viết cuốn sách đó (năm 2014), ES6 vẫn còn gần 2 năm nữa mới hoàn thành, vì vậy tôi cảm thấy những phác thảo ban đầu về từ khóa `class` chỉ đáng nhận được một phần phụ lục ngắn gọn.

Nói rằng rất nhiều điều đã thay đổi trong bối cảnh JS trong gần 8 năm kể từ cuốn sách đó quả là một sự đánh giá thấp. ES6 giờ đã là chuyện xưa cũ; tại thời điểm viết cuốn sách *này*, JS đã trải qua 7 bản cập nhật hàng năm **sau ES6** (từ ES2016 đến ES2022).

Giờ đây, chúng ta vẫn cần nói về cách `this` hoạt động, và cách điều đó liên quan đến các phương thức được gọi trên các đối tượng khác nhau. Và `class` thực sự hoạt động (hầu hết!) thông qua chuỗi nguyên mẫu sâu bên dưới vỏ bọc. Nhưng các lập trình viên JS vào năm 2022 hầu như không còn viết mã để thiết lập kế thừa nguyên mẫu (prototypal inheritance) một cách tường minh nữa. Và dù cho cá nhân tôi có mong muốn khác đi chăng nữa, thì các mẫu thiết kế lớp -- chứ không phải "ủy quyền hành vi" ("behavior delegation") -- mới là cách mà phần lớn việc tổ chức dữ liệu và hành vi (cấu trúc dữ liệu) trong JS được thể hiện.

Cuốn sách này phản ánh thực tế hiện tại của JS: do đó có phụ đề mới, tổ chức và trọng tâm chủ đề mới, cùng với việc viết lại hoàn toàn nội dung của ấn bản trước.

## Đối tượng như những thùng chứa

Một cách phổ biến để tập hợp nhiều giá trị vào một thùng chứa duy nhất là sử dụng một đối tượng. Đối tượng là tập hợp các cặp khóa/giá trị (key/value). Ngoài ra còn có các kiểu phụ (sub-types) của đối tượng trong JS với các hành vi chuyên biệt, chẳng hạn như mảng (được đánh chỉ mục theo số) và thậm chí cả hàm (có thể gọi được); chúng ta sẽ nói thêm về các kiểu phụ này sau.

| GHI CHÚ: |
| :--- |
| Các khóa thường được gọi là "tên thuộc tính" (property names), với sự kết hợp giữa tên thuộc tính và giá trị thường được gọi là "thuộc tính" (property). Cuốn sách này sẽ sử dụng các thuật ngữ đó một cách phân biệt theo cách thức như vậy. |

Các đối tượng JS thông thường thường được khai báo bằng cú pháp literal (cú pháp tường minh), như thế này:

```js
myObj = {
    // ..
};
```

**Lưu ý:** Có một cách khác để tạo đối tượng (sử dụng `myObj = new Object()`), nhưng cách này không phổ biến cũng như không được ưu tiên, và hầu như không bao giờ là cách tiếp cận thích hợp. Hãy trung thành với cú pháp object literal.

Rất dễ bị nhầm lẫn về ý nghĩa của các cặp ngoặc nhọn `{ .. }`, vì JS nạp chồng (overload) các dấu ngoặc nhọn để mang bất kỳ ý nghĩa nào sau đây, tùy thuộc vào ngữ cảnh được sử dụng:

* phân định các giá trị, chẳng hạn như các object literal
* định nghĩa các mẫu phân rã đối tượng (sẽ nói thêm về điều này sau)
* phân định các biểu thức chuỗi nội suy, chẳng hạn như `` `some ${ getNumber() } thing` ``
* định nghĩa các khối lệnh (block), chẳng hạn như trên vòng lặp `if` và `for`
* định nghĩa thân hàm

Mặc dù đôi khi có thể gặp khó khăn khi bạn đọc mã, hãy xem liệu một cặp dấu ngoặc nhọn `{ .. }` có được sử dụng trong chương trình ở nơi mà một giá trị/biểu thức hợp lệ xuất hiện hay không; nếu có, đó là một object literal, nếu không thì đó là một trong những mục đích sử dụng nạp chồng khác.

## Định nghĩa thuộc tính

Bên trong các dấu ngoặc nhọn của object literal, bạn định nghĩa các thuộc tính (tên và giá trị) bằng các cặp `propertyName: propertyValue`, như sau:

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle"
};
```

Các giá trị bạn gán cho thuộc tính có thể là các giá trị nguyên văn (literal), như đã thấy, hoặc có thể được tính toán bằng biểu thức:

```js
function twenty() { return 20; }

myObj = {
    favoriteNumber: (twenty() + 1) * 2,
};
```

Biểu thức `(twenty() + 1) * 2` được đánh giá ngay lập tức, với kết quả (`42`) được gán làm giá trị thuộc tính.

Các nhà phát triển đôi khi tự hỏi liệu có cách nào để định nghĩa một biểu thức cho giá trị thuộc tính mà biểu thức đó "lười biếng" (lazy), nghĩa là nó không được tính toán tại thời điểm gán, mà được tính toán sau đó hay không. JS không có biểu thức lười biếng (lazy expressions), vì vậy cách duy nhất để làm điều đó là bao bọc biểu thức trong một hàm:

```js
function twenty() { return 20; }
function myNumber() { return (twenty() + 1) * 2; }

myObj = {
    favoriteNumber: myNumber   // lưu ý, KHÔNG phải `myNumber()` dưới dạng lời gọi hàm
};
```

Trong trường hợp này, `favoriteNumber` không chứa giá trị số, mà là một tham chiếu hàm. Để tính toán kết quả, tham chiếu hàm đó phải được thực thi một cách tường minh.

### Trông giống JSON?

Bạn có thể nhận thấy rằng cú pháp object-literal mà chúng ta đã thấy cho đến nay rất giống với một cú pháp liên quan, "JSON" (JavaScript Object Notation):

```json
{
    "favoriteNumber": 42,
    "isDeveloper": true,
    "firstName": "Kyle"
}
```

Sự khác biệt lớn nhất giữa object literal của JS và JSON là, đối với các đối tượng được định nghĩa dưới dạng JSON:

1. tên thuộc tính phải được đặt trong dấu ngoặc kép `"`

2. giá trị thuộc tính phải là các giá trị nguyên văn (hoặc là kiểu nguyên thủy, đối tượng, hoặc mảng), chứ không phải biểu thức JS tùy ý

Trong các chương trình JS, một object literal không yêu cầu tên thuộc tính phải đặt trong dấu ngoặc kép -- bạn *có thể* đặt chúng trong ngoặc kép (cho phép `'` hoặc `"`), nhưng thông thường là tùy chọn. Tuy nhiên, có những ký tự hợp lệ trong một tên thuộc tính nhưng không thể đưa vào nếu không có dấu ngoặc kép bao quanh; ví dụ: các chữ số ở đầu hoặc khoảng trắng:

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle",
    "2 nicknames": [ "getify", "ydkjs" ]
};
```

Một sự khác biệt nhỏ khác là, cú pháp JSON -- tức là văn bản sẽ được *phân tích cú pháp* (parsed) dưới dạng JSON, chẳng hạn như từ tệp `.json` -- nghiêm ngặt hơn JS nói chung. Ví dụ, JS cho phép các chú thích (`// ..` và `/* .. */`), và các dấu phẩy `,` ở cuối trong các biểu thức đối tượng và mảng; JSON không cho phép bất kỳ điều nào trong số này. Rất may, JSON vẫn cho phép các khoảng trắng tùy ý.

### Tên thuộc tính

Tên thuộc tính trong object literal hầu như luôn được xử lý/ép kiểu thành các giá trị chuỗi. Một ngoại lệ cho điều này là đối với các "tên" thuộc tính là số nguyên (hoặc "trông giống số nguyên"):

```js
anotherObj = {
    42:       "<-- tên thuộc tính này sẽ được xem như một số nguyên",
    "41":     "<-- ...và tên này cũng vậy",

    true:     "<-- tên thuộc tính này sẽ được xem như một chuỗi",
    [myObj]:  "<-- ...và tên này cũng thế"
};
```

Tên thuộc tính `42` sẽ được coi là tên thuộc tính số nguyên (hay còn gọi là chỉ mục); giá trị chuỗi `"41"` cũng sẽ được coi như vậy vì nó *trông giống* một số nguyên. Ngược lại, giá trị `true` sẽ trở thành tên thuộc tính chuỗi `"true"`, và tham chiếu định danh `myObj`, được *tính toán* thông qua cặp ngoặc vuông `[ .. ]` bao quanh, sẽ ép giá trị của đối tượng thành một chuỗi (thường là mặc định `"[object Object]"`).

| CẢNH BÁO: |
| :--- |
| Nếu bạn thực sự cần sử dụng một đối tượng làm khóa/tên thuộc tính, đừng bao giờ dựa vào việc ép kiểu chuỗi được tính toán này; hành vi của nó rất bất ngờ và gần như chắc chắn không phải là những gì mong đợi, vì vậy các lỗi chương trình rất dễ xảy ra. Thay vào đó, hãy sử dụng một cấu trúc dữ liệu chuyên biệt hơn, được gọi là `Map` (được thêm vào trong ES6), nơi các đối tượng được sử dụng làm "tên" thuộc tính được giữ nguyên thay vì bị ép kiểu thành giá trị chuỗi. |

Như với `[myObj]` ở trên, bạn có thể *tính toán* bất kỳ **tên thuộc tính** nào (khác biệt với việc tính toán giá trị thuộc tính) tại thời điểm định nghĩa object literal:

```js
anotherObj = {
    ["x" + (21 * 2)]: true
};
```

Biểu thức `"x" + (21 * 2)`, biểu thức này phải xuất hiện bên trong dấu ngoặc vuông `[ .. ]`, được tính toán ngay lập tức và kết quả (`"x42"`) được sử dụng làm tên thuộc tính.

### Symbol làm tên thuộc tính

ES6 đã thêm một kiểu giá trị nguyên thủy mới là `Symbol`, thường được sử dụng làm tên thuộc tính đặc biệt để lưu trữ và truy xuất các giá trị thuộc tính. Chúng được tạo thông qua lời gọi hàm `Symbol(..)` (**không** có từ khóa `new`), hàm này chấp nhận một chuỗi mô tả tùy chọn chỉ được sử dụng cho mục đích gỡ lỗi thân thiện hơn; nếu được chỉ định, mô tả này chương trình JS không thể truy cập được và do đó không được sử dụng cho bất kỳ mục đích nào khác ngoài đầu ra gỡ lỗi.

```js
myPropSymbol = Symbol("mô tả tùy chọn, thân thiện cho lập trình viên");
```

| GHI CHÚ: |
| :--- |
| Symbol hơi giống với số hoặc chuỗi, ngoại trừ việc giá trị của chúng là *mờ đục* (opaque) đối với chương trình JS và duy nhất trên toàn cục (globally unique). Nói cách khác, bạn có thể tạo và sử dụng symbol, nhưng JS không cho bạn biết bất cứ điều gì về giá trị cơ bản bên dưới hoặc làm bất cứ điều gì với nó; điều đó được engine JS giữ kín như một chi tiết cài đặt ẩn. |

Tên thuộc tính được tính toán, như đã mô tả trước đó, là cách để định nghĩa một tên thuộc tính symbol trên một object literal:

```js
myPropSymbol = Symbol("mô tả tùy chọn, thân thiện cho lập trình viên");

anotherObj = {
    [myPropSymbol]: "Xin chào, symbol!"
};
```

Tên thuộc tính được tính toán dùng để định nghĩa thuộc tính trên `anotherObj` sẽ là giá trị symbol nguyên thủy thực tế (bất kể nó là gì), chứ không phải là chuỗi mô tả tùy chọn (`"mô tả tùy chọn, thân thiện cho lập trình viên"`).

Bởi vì các symbol là duy nhất trên toàn cục trong chương trình của bạn, nên **không** có khả năng xảy ra xung đột ngẫu nhiên khi một phần của chương trình vô tình định nghĩa một tên thuộc tính trùng với tên mà một phần khác của chương trình đã cố gắng định nghĩa/gán.

Symbol cũng hữu ích để móc vào (hook) các hành vi mặc định đặc biệt của đối tượng, và chúng ta sẽ đề cập chi tiết hơn về điều đó trong phần "Mở rộng MOP" ở chương tiếp theo.

### Thuộc tính rút gọn

Khi định nghĩa một object literal, người ta thường sử dụng tên thuộc tính giống với tên định danh hiện có trong phạm vi đang nắm giữ giá trị mà bạn muốn gán.

```js
coolFact = "người đầu tiên bị phạt vì chạy quá tốc độ đã lái 8 mph";

anotherObj = {
    coolFact: coolFact
};
```

| GHI CHÚ: |
| :--- |
| Điều đó cũng tương tự như định nghĩa tên thuộc tính trong ngoặc kép `"coolFact": coolFact`, nhưng các nhà phát triển JS hiếm khi đặt tên thuộc tính trong ngoặc kép trừ khi thực sự cần thiết. Thật vậy, việc tránh dùng dấu ngoặc kép trừ khi bắt buộc đã trở thành một phong cách mang tính quy ước (idiomatic), vì vậy việc đưa chúng vào khi không cần thiết không được khuyến khích. |

Trong tình huống này, khi tên thuộc tính và định danh biểu thức giá trị giống hệt nhau, bạn có thể bỏ qua phần tên thuộc tính của định nghĩa thuộc tính, dưới dạng định nghĩa được gọi là "thuộc tính rút gọn" (concise property):

```js
coolFact = "người đầu tiên bị phạt vì chạy quá tốc độ đã lái 8 mph";

anotherObj = {
    coolFact   // <-- cú pháp viết tắt thuộc tính rút gọn
};
```

Tên thuộc tính là `"coolFact"` (chuỗi), và giá trị được gán cho thuộc tính là giá trị có trong biến `coolFact` tại thời điểm đó: `"người đầu tiên bị phạt vì chạy quá tốc độ đã lái 8 mph"`.

Lúc đầu, sự tiện lợi khi viết tắt này có vẻ gây nhầm lẫn. Nhưng khi bạn trở nên quen thuộc hơn với việc nhìn thấy tính năng rất phổ biến và được ưa chuộng này được sử dụng, có thể bạn sẽ ưu ái nó để gõ (và đọc!) ít hơn.

### Phương thức rút gọn

Một cách viết tắt tương tự khác là định nghĩa các hàm/phương thức trong một object literal bằng cách sử dụng một dạng ngắn gọn hơn:

```js
anotherObj = {
    // thuộc tính hàm tiêu chuẩn
    greet: function() { console.log("Xin chào!"); },

    // thuộc tính hàm/phương thức rút gọn
    greet2() { console.log("Xin chào người bạn!"); }
};
```

Trong khi chúng ta đang bàn về chủ đề các thuộc tính phương thức rút gọn, chúng ta cũng có thể định nghĩa các hàm generator (một tính năng khác của ES6):

```js
anotherObj = {
    // thay vì:
    //   greet3: function*() { yield "Xin chào mọi người!"; }

    // phương thức generator rút gọn
    *greet3() { yield "Xin chào mọi người!"; }
};
```

Và mặc dù nó không đặc biệt phổ biến, các phương thức/generator rút gọn thậm chí có thể có tên được đặt trong dấu ngoặc kép hoặc được tính toán:

```js
anotherObj = {
    "greet-4"() { console.log("Xin chào khán giả!"); },

    // tên tính toán rút gọn
    [ "gr" + "eet 5" ]() { console.log("Xin chào khán giả!"); },

    // tên generator tính toán rút gọn
    *[ "ok, greet 6".toUpperCase() ]() { yield "Xin chào khán giả!"; }
};
```

### Trải đối tượng

Một cách khác để định nghĩa các thuộc tính tại thời điểm tạo object literal là sử dụng một dạng của cú pháp `...` -- về mặt kỹ thuật nó không phải là một toán tử, nhưng nó chắc chắn giống như một toán tử -- thường được gọi là "trải đối tượng" (object spread).

Dấu `...` khi được sử dụng bên trong một object literal sẽ "trải" (spread) nội dung (các thuộc tính, hay còn gọi là các cặp khóa/giá trị) của một giá trị đối tượng khác vào đối tượng đang được định nghĩa:

```js
anotherObj = {
    favoriteNumber: 12,

    ...myObj,   // trải đối tượng, sao chép nông `myObj`

    greeting: "Xin chào!"
}
```

Việc trải các thuộc tính của `myObj` là sao chép nông (shallow), ở chỗ nó chỉ sao chép các thuộc tính cấp cao nhất (top-level) từ `myObj`; bất kỳ giá trị nào mà các thuộc tính đó nắm giữ chỉ đơn giản được gán sang. Nếu bất kỳ giá trị nào trong số đó là tham chiếu đến các đối tượng khác, bản thân các tham chiếu đó sẽ được gán (bằng cách sao chép tham chiếu), nhưng các giá trị đối tượng bên dưới *không* được nhân bản -- vì vậy bạn kết thúc với nhiều tham chiếu dùng chung trỏ tới cùng (các) đối tượng.

Bạn có thể hình dung việc trải đối tượng giống như một vòng lặp `for` chạy qua từng thuộc tính một và thực hiện phép gán kiểu `=` từ đối tượng nguồn (`myObj`) sang đối tượng đích (`anotherObj`).

Ngoài ra, hãy xem các thao tác định nghĩa thuộc tính này diễn ra "theo thứ tự", từ trên xuống dưới của object literal. Trong đoạn mã trên, vì `myObj` có thuộc tính `favoriteNumber`, việc trải đối tượng cuối cùng sẽ ghi đè lên phép gán thuộc tính `favoriteNumber: 12` từ dòng trước đó. Hơn nữa, nếu `myObj` chứa thuộc tính `greeting` được sao chép sang, dòng tiếp theo (`greeting: "Xin chào!"`) sẽ ghi đè lên định nghĩa thuộc tính đó.

| GHI CHÚ: |
| :--- |
| Việc trải đối tượng cũng chỉ sao chép các thuộc tính *thuộc sở hữu* (owned properties - những thuộc tính nằm trực tiếp trên đối tượng) mà *có thể liệt kê* (enumerable - được phép liệt kê/kê khai). Nó không nhân bản thuộc tính -- theo nghĩa thực sự bắt chước các đặc tính chính xác của thuộc tính -- mà chỉ thực hiện việc sao chép theo kiểu gán đơn giản. Chúng ta sẽ đề cập thêm các chi tiết như vậy trong phần "Bộ mô tả thuộc tính" ở chương tiếp theo. |

Một cách phổ biến mà `...` trải đối tượng được sử dụng là để thực hiện sao chép đối tượng *nông* (shallow object duplication):

```js
myObjShallowCopy = { ...myObj };
```

Hãy nhớ rằng bạn không thể trải `...` vào một giá trị đối tượng hiện có; cú pháp trải đối tượng `...` chỉ có thể xuất hiện bên trong object literal `{ .. }`, vốn đang tạo ra một giá trị đối tượng mới. Để thực hiện sao chép đối tượng nông tương tự nhưng bằng API thay vì cú pháp, hãy xem phần "Các mục đối tượng" ở phần sau của chương này (với nội dung bao quát về `Object.entries(..)` và `Object.fromEntries(..)`).

Nhưng nếu thay vào đó bạn muốn sao chép các thuộc tính đối tượng (theo cách nông) vào một đối tượng *hiện có*, hãy xem phần "Gán thuộc tính" ở phần sau của chương này (với nội dung bao quát về `Object.assign(..)`).

### Sao chép đối tượng sâu

Ngoài ra, vì `...` không thực hiện sao chép đối tượng sâu (deep object duplication) toàn diện, nên việc trải đối tượng nhìn chung chỉ phù hợp để nhân bản các đối tượng chỉ chứa các giá trị nguyên thủy, đơn giản, chứ không chứa tham chiếu đến các đối tượng khác.

Sao chép đối tượng sâu là một thao tác cực kỳ phức tạp và nhiều sắc thái. Việc nhân bản một giá trị như `42` là rõ ràng và đơn giản, nhưng sao chép một hàm (vốn là một loại đối tượng đặc biệt, cũng được nắm giữ theo tham chiếu), hoặc sao chép một tham chiếu đối tượng bên ngoài (không hoàn toàn nằm trong JS), chẳng hạn như một phần tử DOM thì có nghĩa là gì? Và điều gì sẽ xảy ra nếu một đối tượng có các tham chiếu vòng (circular references - chẳng hạn như nơi một đối tượng con cháu lồng nhau giữ một tham chiếu ngược trở lại đối tượng tổ tiên bên ngoài)? Có rất nhiều ý kiến trái chiều về cách xử lý tất cả các trường hợp góc (corner cases) này, và do đó không có tiêu chuẩn duy nhất nào tồn tại cho việc sao chép đối tượng sâu.

Đối với việc sao chép đối tượng sâu, các phương pháp tiếp cận tiêu chuẩn từ trước đến nay là:

1. Sử dụng một tiện ích thư viện đưa ra một quan điểm cụ thể về cách xử lý các hành vi/sắc thái nhân bản.

2. Sử dụng mẹo khứ hồi `JSON.parse(JSON.stringify(..))` -- cách này chỉ "hoạt động" chính xác nếu không có tham chiếu vòng và nếu không có giá trị nào trong đối tượng không thể tuần tự hóa đúng cách bằng JSON (chẳng hạn như các hàm).

Gần đây, một lựa chọn thứ ba đã xuất hiện. Đây không phải là một tính năng của JS, mà là một API đồng hành được cung cấp cho JS bởi các môi trường như nền tảng web. Các đối tượng hiện có thể được sao chép sâu bằng cách sử dụng `structuredClone(..)`[^structuredClone].

```js
myObjCopy = structuredClone(myObj);
```

Thuật toán cơ bản đằng sau tiện ích tích hợp sẵn này hỗ trợ việc nhân bản các tham chiếu vòng, cũng như **nhiều kiểu giá trị hơn** so với mẹo khứ hồi `JSON`. Tuy nhiên, thuật toán này vẫn có những giới hạn của nó, bao gồm việc không hỗ trợ sao chép các hàm hoặc các phần tử DOM.

## Truy cập thuộc tính

Việc truy cập thuộc tính của một đối tượng hiện có tốt nhất nên được thực hiện bằng toán tử `.`:

```js
myObj.favoriteNumber;    // 42
myObj.isDeveloper;       // true
```

Nếu có thể truy cập một thuộc tính theo cách này, bạn rất nên làm như vậy.

Nếu tên thuộc tính chứa các ký tự không thể xuất hiện trong định danh, chẳng hạn như số ở đầu hoặc khoảng trắng, dấu ngoặc vuông `[ .. ]` có thể được sử dụng thay cho `.`:

```js
myObj["2 nicknames"];    // [ "getify", "ydkjs" ]
```

```js
anotherObj[42];          // "<-- tên thuộc tính này sẽ..."
anotherObj["41"];        // "<-- tên thuộc tính này sẽ..."
```

Mặc dù các "tên" thuộc tính bằng số vẫn là số, việc truy cập thuộc tính thông qua dấu ngoặc vuông `[ .. ]` sẽ ép một biểu diễn chuỗi thành một số (ví dụ: `"42"` thành số `42` tương đương), và sau đó truy cập thuộc tính số tương ứng theo đó.

Tương tự như object literal, tên thuộc tính cần truy cập có thể được tính toán thông qua dấu ngoặc vuông `[ .. ]`. Biểu thức có thể là một định danh đơn giản:

```js
propName = "41";
anotherObj[propName];
```

Trên thực tế, những gì bạn đặt giữa các dấu ngoặc vuông `[ .. ]` có thể là bất kỳ biểu thức JS tùy ý nào, không chỉ là các định danh hoặc giá trị nguyên văn như `42` hoặc `"isDeveloper"`. JS trước tiên sẽ đánh giá biểu thức, và giá trị kết quả sau đó sẽ được sử dụng làm tên thuộc tính để tra cứu trên đối tượng:

```js
function howMany(x) {
    return x + 1;
}

myObj[`${ howMany(1) } nicknames`];   // [ "getify", "ydkjs" ]
```

Trong đoạn mã này, biểu thức là một `` `template string literal` `` được phân định bằng dấu back-tick với một biểu thức nội suy của lời gọi hàm `howMany(1)`. Kết quả tổng thể của biểu thức đó là giá trị chuỗi `"2 nicknames"`, sau đó được sử dụng làm tên thuộc tính để truy cập.

### Các mục đối tượng

Bạn có thể lấy danh sách các thuộc tính trong một đối tượng, dưới dạng một mảng các bộ tuple (mảng con gồm hai phần tử) chứa tên và giá trị thuộc tính:

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle"
};

Object.entries(myObj);
// [ ["favoriteNumber",42], ["isDeveloper",true], ["firstName","Kyle"] ]
```

Được thêm vào trong ES6, `Object.entries(..)` truy xuất danh sách các mục này -- chỉ chứa các thuộc tính thuộc sở hữu và có thể liệt kê; xem phần "Bộ mô tả thuộc tính" trong chương tiếp theo -- từ một đối tượng nguồn.

Một danh sách như vậy có thể được lặp qua, có khả năng gán các thuộc tính cho một đối tượng hiện có khác. Tuy nhiên, cũng có thể tạo một đối tượng mới từ danh sách các mục nhập, sử dụng `Object.fromEntries(..)` (được thêm vào trong ES2019):

```js
myObjShallowCopy = Object.fromEntries( Object.entries(myObj) );

// cách tiếp cận thay thế cho cách đã thảo luận trước đó:
// myObjShallowCopy = { ...myObj };
```

### Phân rã

Một cách tiếp cận khác để truy cập các thuộc tính là thông qua phân rã đối tượng (object destructuring - được thêm vào trong ES6). Hãy nghĩ về việc phân rã giống như việc xác định một "mẫu" mô tả cấu trúc của một giá trị đối tượng sẽ "trông như thế nào", và sau đó yêu cầu JS tuân theo "mẫu" đó để truy cập một cách có hệ thống nội dung của một giá trị đối tượng.

Kết quả cuối cùng của việc phân rã đối tượng không phải là một đối tượng khác, mà là một hoặc nhiều phép gán cho các mục tiêu khác (biến, v.v.) từ các giá trị của đối tượng nguồn.

Hãy tưởng tượng loại mã trước ES6 này:

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle"
};

const favoriteNumber = (
    myObj.favoriteNumber !== undefined ? myObj.favoriteNumber : 42
);
const isDev = myObj.isDeveloper;
const firstName = myObj.firstName;
const lname = (
    myObj.lastName !== undefined ? myObj.lastName : "--missing--"
);
```

Những lần truy cập các giá trị thuộc tính và gán cho các định danh khác thường được gọi là "phân rã thủ công". Để sử dụng cú pháp phân rã đối tượng mang tính khai báo, nó có thể trông như thế này:

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle"
};

const { favoriteNumber = 12 } = myObj;
const {
    isDeveloper: isDev,
    firstName: firstName,
    lastName: lname = "--missing--"
} = myObj;

favoriteNumber;   // 42
isDev;            // true
firstName;        // "Kyle"
lname;            // "--missing--"
```

Như đã thấy, việc phân rã đối tượng `{ .. }` giống như một định nghĩa giá trị object literal, nhưng nó xuất hiện ở vế bên trái của toán tử `=` chứ không phải ở vế bên phải nơi một biểu thức giá trị đối tượng sẽ xuất hiện. Điều đó làm cho `{ .. }` ở vế trái trở thành một mẫu phân rã chứ không phải là một định nghĩa đối tượng khác.

Phép phân rã `{ favoriteNumber } = myObj` báo cho JS tìm một thuộc tính có tên là `favoriteNumber` trên đối tượng, và gán giá trị của nó cho một định danh có cùng tên. Trường hợp duy nhất của định danh `favoriteNumber` trong mẫu tương tự như "thuộc tính rút gọn" như đã thảo luận trước đó trong chương này: nếu nguồn (tên thuộc tính) và đích (định danh) giống nhau, bạn có thể bỏ qua một trong số chúng và chỉ liệt kê nó một lần.

Phần `= 12` báo cho JS cung cấp `12` làm giá trị mặc định cho phép gán cho `favoriteNumber`, nếu đối tượng nguồn không có thuộc tính `favoriteNumber`, hoặc nếu thuộc tính đó giữ giá trị `undefined`.

Trong mẫu phân rã thứ hai, mẫu `isDeveloper: isDev` hướng dẫn JS tìm một thuộc tính có tên `isDeveloper` trên đối tượng nguồn và gán giá trị của nó cho một định danh có tên `isDev`. Nó là một kiểu "đổi tên" từ nguồn sang đích. Ngược lại, `firstName: firstName` cung cấp nguồn và đích cho một phép gán, nhưng là dư thừa vì chúng giống hệt nhau; một `firstName` đơn lẻ là đủ ở đây, và thường được ưa chuộng hơn.

Cú pháp `lastName: lname = "--missing--"` kết hợp cả việc đổi tên nguồn-đích và giá trị mặc định (nếu thuộc tính nguồn `lastName` bị thiếu hoặc mang giá trị `undefined`).

Đoạn mã trên kết hợp việc phân rã đối tượng với các khai báo biến -- trong ví dụ này, `const` được sử dụng, nhưng `var` và `let` cũng hoạt động tương tự -- nhưng bản chất nó không phải là một cơ chế khai báo. Phân rã là về truy cập và gán (từ nguồn đến đích), vì vậy nó có thể hoạt động đối với các đích hiện có thay vì khai báo các đích mới:

```js
let fave;

// dấu ngoặc đơn ( ) bao quanh là cú pháp bắt buộc ở đây,
// khi không sử dụng từ khóa khai báo
({ favoriteNumber: fave } = myObj);

fave;  // 42
```

Cú pháp phân rã đối tượng thường được ưa thích hơn vì phong cách khai báo và dễ đọc hơn so với các cú pháp tương đương mang tính mệnh lệnh nặng nề trước thời kỳ ES6. Nhưng đừng lạm dụng việc phân rã quá mức. Đôi khi chỉ cần làm `x = someObj.x` là hoàn toàn tốt rồi!

### Truy cập thuộc tính có điều kiện

Gần đây (trong ES2020), một tính năng được gọi là "chuỗi tùy chọn" (optional chaining) đã được thêm vào JS, giúp tăng cường khả năng truy cập thuộc tính (đặc biệt là truy cập thuộc tính lồng nhau). Dạng chính là toán tử ghép hai ký tự `?.`, chẳng hạn như `A?.B`.

Toán tử này sẽ kiểm tra tham chiếu phía bên tay trái (`A`) xem nó có phải là null'ish (`null` hoặc `undefined`) hay không. Nếu vậy, phần còn lại của biểu thức truy cập thuộc tính sẽ bị ngắn mạch (bỏ qua), và `undefined` được trả về dưới dạng kết quả (ngay cả khi thực tế gặp phải là `null`!). Mặt khác, `?.` sẽ truy cập thuộc tính giống như một toán tử `.` thông thường.

Ví dụ:

```js
myObj?.favoriteNumber
```

Ở đây, việc kiểm tra null'ish được thực hiện đối với `myObj`, có nghĩa là việc truy cập thuộc tính `favoriteNumber` chỉ được thực hiện nếu giá trị trong `myObj` không phải là null'ish. Lưu ý rằng nó không xác minh xem `myObj` có thực sự chứa một đối tượng thực hay không, mà chỉ kiểm tra xem nó có không phải là null'ish hay không. Tuy nhiên, tất cả các giá trị không phải null'ish đều có thể được "truy cập" một cách "an toàn" (không có ngoại lệ JS nào) thông qua toán tử `.`, ngay cả khi không có thuộc tính phù hợp để truy xuất.

Rất dễ nhầm lẫn khi nghĩ rằng việc kiểm tra null'ish là đối với thuộc tính `favoriteNumber`. Nhưng một cách để ghi nhớ chính xác là nhớ rằng dấu `?` nằm ở phía mà việc kiểm tra an toàn được thực hiện, trong khi dấu `.` nằm ở phía chỉ được đánh giá có điều kiện nếu bước kiểm tra không phải null'ish được thông qua.

Thông thường, toán tử `?.` được sử dụng trong các truy cập thuộc tính lồng nhau có thể sâu từ 3 cấp trở lên, chẳng hạn như:

```js
myObj?.address?.city
```

Thao tác tương đương với toán tử `?.` sẽ trông như thế này:

```js
(myObj != null && myObj.address != null) ? myObj.address.city : undefined
```

Một lần nữa, hãy nhớ rằng không có kiểm tra nào được thực hiện đối với thuộc tính ngoài cùng bên phải (`city`) ở đây.

Ngoài ra, `?.` không nên được sử dụng một cách bừa bãi để thay thế cho mọi toán tử `.` đơn lẻ trong các chương trình của bạn. Bất cứ khi nào có thể, bạn nên nỗ lực để biết liệu một truy cập thuộc tính bằng dấu `.` có thành công hay không trước khi thực hiện truy cập. Chỉ sử dụng `?.` khi bản chất của các giá trị được truy cập phải chịu các điều kiện không thể dự đoán/kiểm soát được.

Ví dụ, trong đoạn mã trước, cách sử dụng `myObj?.` có lẽ là sai hướng, bởi vì thực sự không nên có trường hợp bạn bắt đầu một chuỗi truy cập thuộc tính đối với một biến thậm chí có thể không chứa một đối tượng cấp cao nhất (ngoài việc nội dung của nó có khả năng thiếu các thuộc tính nhất định trong các điều kiện nhất định).

Thay vào đó, tôi khuyên bạn nên sử dụng giống như thế này hơn:

```js
myObj.address?.city
```

Và biểu thức đó chỉ nên được sử dụng trong phần chương trình mà bạn chắc chắn rằng `myObj` ít nhất đang chứa một đối tượng hợp lệ (bất kể nó có thuộc tính `address` chứa đối tượng phụ bên trong hay không).

Một dạng khác của toán tử "chuỗi tùy chọn" là `?.[`, được sử dụng khi truy cập thuộc tính bạn muốn làm cho có điều kiện/an toàn yêu cầu một dấu ngoặc vuông `[ .. ]`.

```js
myObj["2 nicknames"]?.[0];   // "getify"
```

Mọi điều đã khẳng định về cách hành xử của `?.` cũng áp dụng tương tự cho `?.[`.

| CẢNH BÁO: |
| :--- |
| Có một dạng thứ ba của tính năng này, được gọi là "lời gọi tùy chọn" (optional call), sử dụng `?.(` làm toán tử. Nó được sử dụng để thực hiện kiểm tra không phải null'ish trên một thuộc tính trước khi thực thi giá trị hàm trong thuộc tính đó. Ví dụ, thay vì `myObj.someFunc(42)`, bạn có thể thực hiện `myObj.someFunc?.(42)`. Dấu `?.(` kiểm tra để đảm bảo `myObj.someFunc` không phải là null'ish trước khi gọi nó (với phần `(42)`). Mặc dù điều đó nghe có vẻ là một tính năng hữu ích, tôi nghĩ điều này đủ nguy hiểm để cảnh báo bạn nên tránh hoàn toàn dạng/cấu trúc này.<br><br>Mối bận tâm của tôi là `?.(` làm cho có vẻ như chúng ta đang đảm bảo rằng hàm "có thể gọi được" (callable) trước khi gọi nó, trong khi thực tế chúng ta chỉ kiểm tra xem nó có không phải là null'ish hay không. Không giống như `?.` có thể cho phép truy cập `.` "an toàn" đối với một giá trị không phải null'ish và cũng không phải là một đối tượng, việc kiểm tra không phải null'ish của `?.(` không "an toàn" tương tự. Nếu thuộc tính được đề cập có bất kỳ giá trị nào không phải null'ish và không phải hàm, chẳng hạn như `true` hoặc `"Hello"`, phần lời gọi `(42)` sẽ được gọi và ném ra một ngoại lệ JS. Nói cách khác, dạng này thật không may đang đội lốt là "an toàn" hơn thực tế, và do đó nên tránh trong hầu hết mọi trường hợp. Nếu một giá trị thuộc tính có thể *không phải là* một hàm, hãy kiểm tra đầy đủ hơn về tính chất hàm của nó trước khi cố gắng gọi nó. Đừng giả vờ rằng `?.(` đang làm điều đó cho bạn, nếu không những người đọc/bảo trì mã của bạn trong tương lai (bao gồm cả chính bạn trong tương lai!) có thể sẽ phải hối tiếc. |

### Truy cập thuộc tính trên các giá trị không phải đối tượng

Điều này nghe có vẻ phản trực giác, nhưng bạn thường có thể truy cập các thuộc tính/phương thức từ các giá trị bản thân chúng không phải là đối tượng:

```js
fave = 42;

fave;              // 42
fave.toString();   // "42"
```

Ở đây, `fave` chứa một giá trị số nguyên thủy `42`. Vậy làm thế nào chúng ta có thể thực hiện `.toString` để truy cập một thuộc tính từ nó, và sau đó dùng `()` để gọi hàm được giữ trong thuộc tính đó?

Đây là một chủ đề sâu sắc hơn rất nhiều so với những gì chúng ta sẽ đề cập trong cuốn sách này; xem cuốn 4, "Types & Grammar", của bộ sách này để biết thêm chi tiết. Tuy nhiên, nhìn lướt qua nhanh: nếu bạn thực hiện truy cập thuộc tính (`.` hoặc `[ .. ]`) đối với một giá trị không phải đối tượng và không phải null'ish, JS theo mặc định sẽ (tạm thời!) ép giá trị đó thành một biểu diễn được bao bọc bởi đối tượng (object-wrapped representation), cho phép truy cập thuộc tính đối với đối tượng được khởi tạo ngầm định đó.

Quá trình này thường được gọi là "đóng hộp" (boxing), giống như việc đặt một giá trị vào bên trong một chiếc "hộp" (thùng chứa đối tượng).

Vì vậy, trong đoạn mã trên, chỉ trong khoảnh khắc mà `.toString` được truy cập trên giá trị `42`, JS sẽ đóng hộp giá trị này thành một đối tượng `Number`, và sau đó thực hiện việc truy cập thuộc tính.

Lưu ý rằng `null` và `undefined` có thể được chuyển thành đối tượng bằng cách gọi `Object(null)` / `Object(undefined)`. Tuy nhiên, JS không tự động đóng hộp các giá trị null'ish này, vì vậy việc truy cập thuộc tính đối với chúng sẽ thất bại (như đã thảo luận trước đó trong phần "Truy cập thuộc tính có điều kiện").

| GHI CHÚ: |
| :--- |
| Đóng hộp (boxing) có một đối trọng: mở hộp (unboxing). Ví dụ, engine JS sẽ lấy một đối tượng bao bọc -- giống như một đối tượng `Number` bao bọc lấy `42` -- được tạo bằng `Number(42)` hoặc `Object(42)` -- và mở gói nó để truy xuất giá trị nguyên thủy `42` bên dưới, bất cứ khi nào một phép toán số học (như `*` hoặc `-`) gặp phải một đối tượng như vậy. Hành vi mở hộp nằm ngoài phạm vi thảo luận của chúng ta, nhưng được đề cập đầy đủ trong tựa sách "Types & Grammar" đã nói ở trên. |

## Gán thuộc tính

Cho dù một thuộc tính được định nghĩa tại thời điểm định nghĩa object literal hay được thêm vào sau đó, việc gán giá trị thuộc tính được thực hiện bằng toán tử `=`, giống như bất kỳ phép gán thông thường nào khác:

```js
myObj.favoriteNumber = 123;
```

Nếu thuộc tính `favoriteNumber` chưa tồn tại, câu lệnh đó sẽ tạo một thuộc tính mới có tên đó và gán giá trị cho nó. Nhưng nếu nó đã tồn tại, câu lệnh đó sẽ gán lại giá trị của nó.

| CẢNH BÁO: |
| :--- |
| Một phép gán `=` cho một thuộc tính có thể thất bại (trong im lặng hoặc ném ra một ngoại lệ), hoặc nó có thể không trực tiếp gán giá trị mà thay vào đó gọi một hàm *setter* thực hiện một (số) thao tác nào đó. Chi tiết hơn về các hành vi này sẽ có trong chương tiếp theo. |

Cũng có thể gán một hoặc nhiều thuộc tính cùng một lúc -- giả sử các thuộc tính nguồn (các cặp tên và giá trị) nằm trong một đối tượng khác -- bằng cách sử dụng phương thức `Object.assign(..)` (được thêm vào trong ES6):

```js
// sao chép nông tất cả các thuộc tính (thuộc sở hữu và có thể liệt kê)
// từ `myObj` vào `anotherObj`
Object.assign(anotherObj,myObj);

Object.assign(
    /*target=*/anotherObj,
    /*source1=*/{
        someProp: "some value",
        anotherProp: 1001,
    },
    /*source2=*/{
        yetAnotherProp: false
    }
);
```

`Object.assign(..)` lấy đối tượng đầu tiên làm đích, và (các) đối tượng thứ hai (và tùy chọn tiếp theo) làm nguồn. Việc sao chép được thực hiện theo cách tương tự như đã mô tả trước đó trong phần "Trải đối tượng".

## Xóa thuộc tính

Khi một thuộc tính đã được định nghĩa trên một đối tượng, cách duy nhất để loại bỏ nó là sử dụng toán tử `delete`:

```js
anotherObj = {
    counter: 123
};

anotherObj.counter;   // 123

delete anotherObj.counter;

anotherObj.counter;   // undefined
```

Trái ngược với quan niệm sai lầm phổ biến, toán tử `delete` trong JS **không** trực tiếp thực hiện bất kỳ việc giải phóng bộ nhớ nào thông qua bộ thu dọn rác (GC - Garbage Collection). Điều duy nhất nó làm là loại bỏ một thuộc tính khỏi một đối tượng. Nếu giá trị trong thuộc tính là một tham chiếu (đến một đối tượng khác/v.v.), và không còn tham chiếu nào khác còn tồn tại đến giá trị đó sau khi thuộc tính bị xóa, giá trị đó có khả năng sẽ đủ điều kiện để bị thu dọn trong một đợt quét tương lai của GC.

Việc gọi `delete` trên bất kỳ thứ gì khác ngoài thuộc tính đối tượng là việc sử dụng sai toán tử `delete`, và sẽ thất bại trong im lặng (ở chế độ non-strict mode) hoặc ném ra một ngoại lệ (ở chế độ strict mode).

Việc xóa một thuộc tính khỏi một đối tượng khác biệt với việc gán cho nó một giá trị như `undefined` hoặc `null`. Một thuộc tính được gán `undefined`, dù là ban đầu hay sau này, vẫn hiện diện trên đối tượng và vẫn có thể bị phát hiện khi liệt kê nội dung.

## Xác định nội dung thùng chứa

Bạn có thể xác định nội dung của một đối tượng theo nhiều cách khác nhau. Để hỏi một đối tượng xem nó có một thuộc tính cụ thể hay không:

```js
myObj = {
    favoriteNumber: 42,
    coolFact: "the first person convicted of speeding was going 8 mph",
    beardLength: undefined,
    nicknames: [ "getify", "ydkjs" ]
};

"favoriteNumber" in myObj;            // true

myObj.hasOwnProperty("coolFact");     // true
myObj.hasOwnProperty("beardLength");  // true

myObj.nicknames = undefined;
myObj.hasOwnProperty("nicknames");    // true

delete myObj.nicknames;
myObj.hasOwnProperty("nicknames");    // false
```

*Có* một sự khác biệt quan trọng giữa cách toán tử `in` và phương thức `hasOwnProperty(..)` hoạt động. Toán tử `in` sẽ không chỉ kiểm tra đối tượng đích được chỉ định, mà nếu không tìm thấy ở đó, nó cũng sẽ tham vấn chuỗi `[[Prototype]]` của đối tượng (sẽ được đề cập trong chương tiếp theo). Ngược lại, `hasOwnProperty(..)` chỉ tham vấn đối tượng đích.

Nếu bạn đang chú ý kỹ, bạn có thể nhận thấy rằng `myObj` dường như có một thuộc tính phương thức gọi là `hasOwnProperty(..)` trên nó, mặc dù chúng ta không định nghĩa phương thức nào như vậy. Đó là vì `hasOwnProperty(..)` được định nghĩa sẵn như một phần tích hợp trên `Object.prototype`, theo mặc định được "kế thừa bởi" tất cả các đối tượng thông thường. Tuy nhiên, vốn dĩ có rủi ro khi truy cập một phương thức "được kế thừa" như vậy. Một lần nữa, chúng ta sẽ nói thêm về các nguyên mẫu trong chương tiếp theo.

### Kiểm tra sự tồn tại tốt hơn

ES2022 (gần như chính thức tại thời điểm viết sách) đã hoàn thiện một tính năng mới, `Object.hasOwn(..)`. Về cơ bản nó thực hiện cùng một công việc như `hasOwnProperty(..)`, nhưng nó được gọi như một hàm trợ giúp tĩnh bên ngoài giá trị đối tượng thay vì thông qua `[[Prototype]]` của đối tượng, giúp cho việc sử dụng an toàn và nhất quán hơn:

```js
// thay vì:
myObj.hasOwnProperty("favoriteNumber")

// bây giờ chúng ta nên ưu tiên:
Object.hasOwn(myObj,"favoriteNumber")
```

Mặc dù (tại thời điểm viết sách) tính năng này mới bắt đầu xuất hiện trong JS, đã có các bản vá polyfill giúp API này khả dụng trong các chương trình của bạn ngay cả khi chạy trong môi trường JS cũ hơn chưa định nghĩa tính năng này. Ví dụ: một bản phác thảo polyfill thay thế nhanh:

```js
// phác thảo polyfill đơn giản cho `Object.hasOwn(..)`
if (!Object.hasOwn) {
    Object.hasOwn = function hasOwn(obj,propName) {
        return Object.prototype.hasOwnProperty.call(obj,propName);
    };
}
```

Việc đưa một bản vá polyfill như vậy vào chương trình của bạn có nghĩa là bạn có thể bắt đầu sử dụng `Object.hasOwn(..)` một cách an toàn để kiểm tra sự tồn tại của thuộc tính bất kể môi trường JS đã tích hợp sẵn `Object.hasOwn(..)` hay chưa.

### Liệt kê toàn bộ nội dung thùng chứa

Chúng ta đã thảo luận về API `Object.entries(..)` trước đó, API này cho chúng ta biết một đối tượng có những thuộc tính nào (miễn là chúng có thể liệt kê được -- sẽ nói thêm trong chương tiếp theo).

Ngoài ra còn có nhiều cơ chế khác khả dụng. `Object.keys(..)` cung cấp cho chúng ta danh sách các tên thuộc tính có thể liệt kê (hay còn gọi là các khóa) trong một đối tượng -- chỉ có tên, không có giá trị; ngược lại, `Object.values(..)` cung cấp cho chúng ta danh sách tất cả các giá trị được giữ trong các thuộc tính có thể liệt kê.

Nhưng nếu chúng ta muốn lấy *tất cả* các khóa trong một đối tượng (có thể liệt kê hoặc không)? `Object.getOwnPropertyNames(..)` dường như làm được những gì chúng ta muốn, ở chỗ nó giống như `Object.keys(..)` nhưng cũng trả về các tên thuộc tính không thể liệt kê. Tuy nhiên, danh sách này **sẽ không** bao gồm bất kỳ tên thuộc tính Symbol nào, vì những thuộc tính đó được coi là các vị trí đặc biệt trên đối tượng. `Object.getOwnPropertySymbols(..)` trả về tất cả các thuộc tính Symbol của một đối tượng. Vì vậy, nếu bạn nối cả hai danh sách đó lại với nhau, bạn sẽ có tất cả nội dung trực tiếp (*thuộc sở hữu*) của một đối tượng.

Tuy nhiên, như chúng ta đã ngụ ý nhiều lần và sẽ trình bày chi tiết đầy đủ trong chương tiếp theo, một đối tượng cũng có thể "kế thừa" nội dung từ chuỗi `[[Prototype]]` của nó. Đây không được coi là nội dung *thuộc sở hữu*, vì vậy chúng sẽ không hiển thị trong bất kỳ danh sách nào trong số này.

Hãy nhớ lại rằng toán tử `in` có khả năng duyệt qua toàn bộ chuỗi để tìm kiếm sự tồn tại của một thuộc tính. Tương tự, vòng lặp `for..in` sẽ duyệt qua chuỗi và liệt kê bất kỳ thuộc tính nào có thể liệt kê (thuộc sở hữu hoặc được kế thừa). Nhưng không có API tích hợp sẵn nào sẽ duyệt qua toàn bộ chuỗi và trả về một danh sách tập hợp kết hợp của cả nội dung *thuộc sở hữu* và *được kế thừa*.

## Các thùng chứa tạm thời

Sử dụng một thùng chứa để chứa nhiều giá trị đôi khi chỉ là một cơ chế vận chuyển tạm thời, chẳng hạn như khi bạn muốn truyền nhiều giá trị cho một hàm thông qua một đối số duy nhất, hoặc khi bạn muốn một hàm trả về nhiều giá trị:

```js
function formatValues({ one, two, three }) {
    // đối tượng thực tế được truyền vào dưới dạng
    // đối số sẽ không thể truy cập được, vì chúng ta
    // đã phân rã nó thành ba biến riêng biệt

    one = one.toUpperCase();
    two = `--${two}--`;
    three = three.substring(0,5);

    // đối tượng này chỉ dùng để vận chuyển
    // cả ba giá trị trong một câu lệnh return duy nhất
    return { one, two, three };
}

// phân rã giá trị trả về từ hàm,
// bởi vì đối tượng được trả về đó chỉ là một thùng chứa tạm thời
// để vận chuyển nhiều giá trị cho chúng ta
const { one, two, three } =

    // đối số dạng đối tượng này là một phương tiện
    // vận chuyển tạm thời cho nhiều giá trị đầu vào
    formatValues({
       one: "Kyle",
       two: "Simpson",
       three: "getify"
    });

one;     // "KYLE"
two;     // "--Simpson--"
three;   // "getif"
```

Object literal được truyền vào `formatValues(..)` ngay lập tức được phân rã tham số, do đó bên trong hàm chúng ta chỉ xử lý ba biến riêng biệt (`one`, `two`, và `three`). Object literal được `return` từ hàm cũng ngay lập tức được phân rã, do đó một lần nữa chúng ta chỉ xử lý ba biến riêng biệt (`one`, `two`, `three`).

Đoạn mã này minh họa cho thành ngữ/mẫu thiết kế rằng một đối tượng đôi khi chỉ là một thùng chứa vận chuyển tạm thời chứ không phải là một giá trị có ý nghĩa tự thân.

## Thùng chứa là tập hợp các thuộc tính

Cách sử dụng phổ biến nhất của các đối tượng là làm thùng chứa cho nhiều giá trị. Chúng ta tạo và quản lý các đối tượng thùng chứa thuộc tính bằng cách:

* định nghĩa các thuộc tính (các vị trí được đặt tên), tại thời điểm tạo đối tượng hoặc sau đó
* gán giá trị, tại thời điểm tạo đối tượng hoặc sau đó
* truy cập các giá trị sau đó, bằng cách sử dụng tên vị trí (tên thuộc tính)
* xóa thuộc tính thông qua `delete`
* xác định nội dung thùng chứa bằng `in`, `hasOwnProperty(..)` / `hasOwn(..)`, `Object.entries(..)` / `Object.keys(..)`, v.v.

Nhưng đối tượng còn có nhiều điều hơn thế nữa chứ không chỉ là các tập hợp tĩnh của tên thuộc tính và giá trị. Trong chương tiếp theo, chúng ta sẽ đi sâu vào bên dưới cơ chế hoạt động để xem chúng thực sự vận hành như thế nào.

[^structuredClone]: "Thuật toán sao chép có cấu trúc (Structured Clone Algorithm)", Đặc tả HTML; https://html.spec.whatwg.org/multipage/structured-data.html#structured-cloning ; Truy cập tháng 7 năm 2022
