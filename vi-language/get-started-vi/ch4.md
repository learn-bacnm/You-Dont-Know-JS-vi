# You Don't Know JS Yet: Bắt đầu - Tái bản lần 2
# Chương 4: Bức tranh toàn cảnh (The Bigger Picture)

Cuốn sách này đã khái quát những điều bạn cần nắm rõ khi *bắt đầu* với JS. Mục tiêu là lấp đầy các khoảng trống mà những độc giả mới làm quen với JS có thể từng vấp phải trong những lần đầu tiếp cận ngôn ngữ. Tôi cũng hy vọng rằng xuyên suốt cuốn sách, chúng ta đã gợi mở đủ những chi tiết sâu hơn để khơi dậy tính tò mò muốn đào sâu hơn nữa vào ngôn ngữ này trong bạn.

Các cuốn sách còn lại trong bộ sách này chính là nơi chúng ta sẽ mổ xẻ toàn bộ phần còn lại của ngôn ngữ, với mức độ chi tiết hơn rất nhiều so với những gì chúng ta có thể thực hiện trong vài chương ngắn ngủi ở đây.

Tuy nhiên, hãy nhớ dành thời gian từ tốn. Thay vì vội vã chuyển sang cuốn sách tiếp theo với nỗ lực đọc lướt qua hết tất cả các cuốn thật nhanh, hãy dành chút thời gian xem lại các nội dung trong cuốn sách này. Hãy dành thêm thời gian xem qua mã nguồn trong các dự án hiện tại của bạn và so sánh những gì bạn thấy với những gì đã được thảo luận cho đến nay.

Khi bạn đã sẵn sàng, chương cuối cùng này sẽ chia cấu trúc của ngôn ngữ JS thành ba trụ cột chính, sau đó cung cấp một lộ trình ngắn gọn về những gì đáng mong đợi từ phần còn lại của bộ sách, cũng như gợi ý của tôi về cách bạn nên tiếp tục. Ngoài ra, đừng bỏ qua phần phụ lục, đặc biệt là Phụ lục B, "Thực hành, Thực hành, Thực hành!".

## Trụ cột 1: Phạm vi và Closure (Scope and Closure)

Việc tổ chức các biến thành các đơn vị phạm vi (hàm, khối lệnh) là một trong những đặc tính nền tảng nhất của bất kỳ ngôn ngữ nào; có lẽ không có đặc tính nào khác tạo ra tác động lớn hơn đến cách các chương trình hoạt động.

Các phạm vi (scope) giống như những chiếc xô, và các biến giống như những viên bi bạn bỏ vào những chiếc xô đó. Mô hình phạm vi của một ngôn ngữ giống như những quy tắc giúp bạn xác định viên bi màu nào sẽ vào chiếc xô có màu tương ứng.

Các phạm vi lồng vào nhau, và đối với bất kỳ biểu thức (expression) hay câu lệnh (statement) nào, chỉ những biến ở cấp độ phạm vi lồng ghép đó hoặc ở các phạm vi cao hơn/bên ngoài mới có thể truy cập được; các biến từ các phạm vi thấp hơn/bên trong đều bị ẩn và không thể truy cập.

Đây là cách các phạm vi hoạt động trong hầu hết các ngôn ngữ, được gọi là phạm vi từ vựng (lexical scope). Ranh giới của các đơn vị phạm vi và cách tổ chức các biến trong chúng được xác định tại thời điểm chương trình được phân tích cú pháp (biên dịch - compiled). Nói cách khác, đó là một quyết định tại thời điểm tác giả viết mã (author-time): nơi bạn đặt một hàm/phạm vi trong chương trình sẽ quyết định cấu trúc phạm vi của phần chương trình đó.

JS có phạm vi từ vựng (lexically scoped), mặc dù nhiều người khẳng định là không phải, bởi vì có hai đặc điểm riêng biệt trong mô hình của nó vốn không xuất hiện ở các ngôn ngữ có phạm vi từ vựng khác.

Đặc điểm đầu tiên thường được gọi là *hoisting*: khi tất cả các biến được khai báo ở bất kỳ đâu trong một phạm vi đều được đối xử như thể chúng được khai báo ở đầu phạm vi đó. Đặc điểm còn lại là các biến được khai báo bằng `var` có phạm vi theo hàm (function scoped), ngay cả khi chúng xuất hiện bên trong một khối lệnh (block).

Cả hoisting lẫn `var` có phạm vi hàm đều không đủ để ủng hộ cho nhận định rằng JS không có phạm vi từ vựng. Khai báo `let`/`const` có một hành vi lỗi kỳ lạ gọi là "Vùng chết tạm thời" (Temporal Dead Zone - TDZ), dẫn đến việc biến có thể quan sát thấy sự tồn tại nhưng không thể sử dụng được. Mặc dù TDZ có vẻ kỳ lạ khi bắt gặp, nhưng nó *cũng* không hề phủ nhận phạm vi từ vựng. Tất cả những điều này chỉ là những phần độc đáo của ngôn ngữ mà mọi nhà phát triển JS nên học và hiểu rõ.

Closure là kết quả tự nhiên của lexical scope khi ngôn ngữ coi hàm là các giá trị hạng nhất (first-class values), giống như JS. Khi một hàm tham chiếu đến các biến từ một phạm vi bên ngoài, và hàm đó được truyền đi như một giá trị rồi thực thi trong các phạm vi khác, nó vẫn duy trì quyền truy cập vào các biến thuộc phạm vi ban đầu của nó; đó chính là closure.

Trên toàn bộ ngành lập trình, nhưng đặc biệt là trong JS, closure thúc đẩy nhiều mẫu lập trình quan trọng nhất, bao gồm cả module. Theo quan điểm của tôi, các module hoàn toàn *thuận theo bản chất* (with the grain) nhất có thể khi nói đến việc tổ chức mã nguồn trong JS.

Để tìm hiểu sâu hơn về phạm vi, closure và cách các module hoạt động, hãy đọc Cuốn 2, *Scope & Closures*.

## Trụ cột 2: Prototype

Trụ cột thứ hai của ngôn ngữ là hệ thống prototype (nguyên mẫu). Chúng ta đã đề cập sâu về chủ đề này trong Chương 3 ("Prototypes"), nhưng tôi chỉ muốn đưa ra thêm một vài nhận xét về tầm quan trọng của nó.

JS là một trong số rất ít ngôn ngữ mà bạn có tùy chọn tạo đối tượng trực tiếp và rõ ràng mà không cần phải định nghĩa cấu trúc của chúng trong một class trước.

Trong nhiều năm, mọi người đã triển khai mẫu thiết kế class dựa trên các prototype—cái gọi là "kế thừa theo nguyên mẫu" (prototypal inheritance, xem Phụ lục A, "Prototypal 'Classes'")—và sau đó với sự ra đời của từ khóa `class` trong ES6, ngôn ngữ đã đặt cược gấp đôi (doubled-down) vào xu hướng lập trình theo phong cách hướng đối tượng/class.

Nhưng tôi nghĩ sự tập trung đó đã làm lu mờ vẻ đẹp và sức mạnh của hệ thống prototype: khả năng hai đối tượng kết nối đơn giản với nhau và hợp tác một cách linh hoạt (trong quá trình thực thi hàm/phương thức) thông qua việc chia sẻ ngữ cảnh `this`.

Class chỉ là một khuôn mẫu bạn có thể xây dựng dựa trên sức mạnh như vậy. Nhưng một cách tiếp cận khác, theo một hướng đi rất khác biệt, là hãy đón nhận đối tượng đúng bản chất là đối tượng, hoàn toàn quên class đi, và để các đối tượng hợp tác thông qua chuỗi prototype (prototype chain). Điều này được gọi là *ủy quyền hành vi* (behavior delegation). Tôi cho rằng việc ủy quyền (delegation) mạnh mẽ hơn kế thừa class (class inheritance) như một phương thức để tổ chức hành vi và dữ liệu trong các chương trình của chúng ta.

Nhưng kế thừa class lại nhận được gần như mọi sự chú ý. Và phần còn lại thuộc về lập trình hàm (functional programming - FP), như một kiểu thiết kế chương trình "đối lập với class" (anti-class). Điều này làm tôi buồn, bởi vì nó dập tắt mọi cơ hội khám phá sự ủy quyền như một giải pháp thay thế khả thi.

Tôi khuyến khích bạn dành nhiều thời gian đào sâu vào Cuốn 3, *Objects & Classes*, để thấy việc ủy quyền đối tượng nắm giữ tiềm năng lớn hơn nhiều so với những gì chúng ta có thể đã nhận ra. Đây không phải là một thông điệp chống lại `class`, mà chủ đích là một thông điệp "class không phải là cách duy nhất để sử dụng các đối tượng" mà tôi muốn nhiều nhà phát triển JS cân nhắc hơn.

Tôi cho rằng việc ủy quyền đối tượng *thuận theo bản chất* của JS hơn nhiều so với class (chúng ta sẽ bàn thêm về *bản chất - grain* ngay sau đây).

## Trụ cột 3: Kiểu dữ liệu và Ép kiểu (Types and Coercion)

Trụ cột thứ ba của JS cho đến nay là phần bị bỏ qua nhiều nhất trong bản chất tự nhiên của JS.

Đại đa số các nhà phát triển đều có những hiểu lầm sâu sắc về cách các *kiểu dữ liệu* (types) hoạt động trong các ngôn ngữ lập trình, và đặc biệt là cách chúng hoạt động trong JS. Một làn sóng quan tâm mạnh mẽ trong cộng đồng JS rộng lớn đã bắt đầu chuyển dịch sang các cách tiếp cận "kiểu tĩnh" (static typing), sử dụng các công cụ nhận biết kiểu như TypeScript hoặc Flow.

Tôi đồng ý rằng các nhà phát triển JS nên học hỏi nhiều hơn về kiểu dữ liệu, và nên học nhiều hơn về cách JS quản lý việc chuyển đổi kiểu dữ liệu. Tôi cũng đồng ý rằng các công cụ nhận biết kiểu có thể giúp ích cho các nhà phát triển, với giả định rằng họ đã tiếp thu và vận dụng kiến thức này ngay từ đầu!

Nhưng tôi hoàn toàn không đồng ý rằng kết luận tất yếu của việc này là quy chụp cơ chế kiểu của JS là tồi tệ và chúng ta cần che đậy các kiểu của JS bằng các giải pháp bên ngoài ngôn ngữ. Chúng ta không nhất thiết phải theo con đường "kiểu tĩnh" thì mới thông minh và vững vàng với các kiểu trong chương trình của mình. Vẫn có những lựa chọn khác, nếu bạn sẵn sàng đi *ngược chiều số đông* (against the grain of the crowd) và *thuận theo bản chất* của JS (một lần nữa, chúng ta sẽ nói rõ hơn về điều này).

Có thể khẳng định rằng, trụ cột này quan trọng hơn hai trụ cột còn lại, theo nghĩa là sẽ không có chương trình JS nào làm được điều gì hữu ích nếu nó không tận dụng đúng cách các kiểu giá trị của JS, cũng như việc chuyển đổi (ép kiểu - coercion) các giá trị giữa các kiểu.

Ngay cả khi bạn yêu thích TypeScript/Flow, bạn sẽ không tận dụng được tối đa các công cụ đó hoặc các cách tiếp cận viết mã đó nếu bạn không am hiểu sâu sắc về cách bản thân ngôn ngữ quản lý các kiểu giá trị.

Để tìm hiểu thêm về các kiểu dữ liệu và việc ép kiểu trong JS, hãy đón đọc Cuốn 4, *Types & Grammar*. Nhưng xin đừng bỏ qua chủ đề này chỉ vì bạn luôn nghe người ta bảo rằng chúng ta chỉ nên dùng `===` và quên phần còn lại đi.

Nếu không học trụ cột này, nền tảng của bạn trong JS nhiều nhất cũng chỉ là lung lay và không hoàn thiện.

## Thuận theo bản chất (With the Grain)

Tôi có một vài lời khuyên muốn chia sẻ về việc tiếp tục hành trình học JS của bạn, cũng như con đường bạn đi qua phần còn lại của bộ sách này: hãy để ý đến *thớ vân/bản chất* (grain) (hãy nhớ lại những lần nhắc đến *grain* ở các phần trước trong chương này).

Đầu tiên, hãy xem xét *thớ vân* (như trong thớ gỗ) về cách hầu hết mọi người tiếp cận và sử dụng JS. Có lẽ bạn đã nhận thấy rằng những cuốn sách này đi ngược lại *thớ vân* đó ở nhiều khía cạnh. Trong YDKJSY, tôi tôn trọng độc giả đủ để giải thích tất cả các phần của JS, chứ không chỉ một số phần phổ biến được chọn lọc. Tôi tin rằng bạn vừa có khả năng vừa xứng đáng nhận được kiến thức đó.

Nhưng đó không phải là những gì bạn sẽ tìm thấy ở rất nhiều tài liệu khác ngoài kia. Điều đó cũng có nghĩa là bạn càng tuân theo và gắn bó với các hướng dẫn từ bộ sách này—bạn suy nghĩ cẩn thận và tự mình phân tích điều gì là tốt nhất cho mã nguồn của mình—thì bạn sẽ càng trở nên nổi bật. Điều đó có thể là điều tốt mà cũng có thể là điều xấu. Nếu bạn muốn bứt phá khỏi đám đông, bạn sẽ phải tách khỏi cách mà đám đông đang làm!

Nhưng tôi cũng từng được nhiều người kể lại rằng họ đã trích dẫn một số chủ đề/lời giải thích từ bộ sách này trong buổi phỏng vấn xin việc, và người phỏng vấn đã bảo ứng viên rằng họ sai; thực tế, theo phản ánh thì nhiều người đã bị mất cơ hội việc làm vì lý do đó.

Hết mức có thể, tôi luôn nỗ lực trong những cuốn sách này để cung cấp thông tin hoàn toàn chính xác về JS, nhìn chung được tham chiếu trực tiếp từ chính đặc tả chuẩn (specification). Nhưng tôi cũng đưa vào khá nhiều quan điểm cá nhân về cách bạn có thể diễn giải và sử dụng JS để mang lại lợi ích tốt nhất cho các chương trình của mình. Tôi không trình bày quan điểm cá nhân như một sự thật hiển nhiên (fact), hay ngược lại. Bạn sẽ luôn biết rõ đâu là sự thật và đâu là ý kiến cá nhân trong những cuốn sách này.

Các sự thật về JS thực sự không phải là vấn đề để tranh cãi. Hoặc là đặc tả có nêu điều đó, hoặc là không. Nếu bạn không thích những gì đặc tả viết, hoặc cách tôi truyền đạt lại nó, hãy đem việc đó lên khiếu nại với TC39! Nếu bạn đang trong một buổi phỏng vấn và họ khẳng định bạn sai về mặt sự thật kỹ thuật, hãy đề nghị họ ngay tại lúc đó xem liệu bạn có thể tra cứu nó trong đặc tả được không. Nếu người phỏng vấn không chịu cân nhắc lại, thì bạn cũng chẳng nên muốn làm việc ở một nơi như vậy.

Nhưng nếu bạn chọn đồng tình với quan điểm của tôi, bạn phải sẵn sàng bảo vệ những lựa chọn đó bằng lý do *tại sao* bạn lại cảm thấy như vậy. Đừng chỉ lặp lại như vẹt những gì tôi nói. Hãy làm chủ quan điểm của bạn. Hãy bảo vệ chúng. Và nếu ai đó mà bạn từng hy vọng được làm việc cùng lại không đồng ý, hãy ngẩng cao đầu bước đi. Thế giới JS rất rộng lớn, và có thừa không gian cho nhiều cách làm khác nhau.

Nói cách khác, đừng ngại đi ngược lại *thớ vân số đông* (against the grain), như tôi đã làm với bộ sách này và tất cả các bài giảng của mình. Không ai có thể bảo bạn cách tận dụng JS tốt nhất là gì; đó là điều do bạn tự quyết định. Tôi chỉ đang cố gắng trao quyền cho bạn để bạn tự đưa ra những kết luận của riêng mình, bất kể chúng là gì.

Mặt khác, có một *bản chất tự nhiên* (grain) mà bạn thực sự nên chú ý và tuân theo: *bản chất tự nhiên* trong cách JS vận hành, ở cấp độ ngôn ngữ. Có những điều hoạt động tốt và tự nhiên trong JS khi có sự luyện tập và cách tiếp cận đúng đắn, và có những điều bạn thực sự không nên cố làm trong ngôn ngữ này.

Liệu bạn có thể khiến chương trình JS của mình trông giống như một chương trình Java, C# hoặc Perl không? Thế còn Python, Ruby, hay thậm chí là PHP? Ở các mức độ khác nhau, chắc chắn là bạn làm được. Nhưng liệu bạn có nên làm vậy không?

Không, tôi không nghĩ bạn nên làm thế. Tôi nghĩ bạn nên học và đón nhận cách thức của JS, và làm cho các chương trình JS của bạn mang đậm "chất JS" (JS'y) nhất có thể trong thực tế. Một số người sẽ nghĩ điều đó đồng nghĩa với việc lập trình cẩu thả và tùy tiện, nhưng tôi hoàn toàn không có ý đó. Ý tôi chỉ là JS có rất nhiều mẫu và thành ngữ (idioms) dễ nhận diện là "đặc trưng của JS", và đi thuận theo *bản chất* (grain) đó là con đường chung dẫn tới thành công tốt đẹp nhất.

Cuối cùng, có lẽ *thói quen/bản chất* quan trọng nhất cần nhận biết là cách mà (các) chương trình hiện tại bạn đang làm việc, và các nhà phát triển bạn đang làm việc cùng, đang thực hiện mọi thứ. Đừng đọc những cuốn sách này xong rồi cố gắng thay đổi *toàn bộ nếp làm việc đó* trong các dự án hiện tại của bạn chỉ sau một đêm. Cách tiếp cận đó sẽ luôn thất bại.

Bạn sẽ phải chuyển dịch những thứ này từng chút một, theo thời gian. Hãy nỗ lực xây dựng sự đồng thuận với các đồng nghiệp lập trình viên về lý do tại sao việc xem xét và cân nhắc lại một cách tiếp cận là quan trọng. Nhưng hãy làm như vậy với chỉ từng chủ đề nhỏ một tại một thời điểm, và hãy để các so sánh mã nguồn trước và sau tự nói lên phần lớn câu chuyện. Hãy tập hợp mọi người trong nhóm lại để cùng thảo luận, và thúc đẩy những quyết định dựa trên sự phân tích và bằng chứng từ mã nguồn thay vì dựa trên quán tính "các lập trình viên kỳ cựu của chúng ta từ trước tới nay vẫn luôn làm như vậy".

Đó là lời khuyên quan trọng nhất mà tôi có thể truyền đạt để giúp bạn học JS. Hãy luôn không ngừng tìm kiếm những cách tốt hơn để sử dụng những gì JS mang lại cho chúng ta nhằm tạo ra mã nguồn dễ đọc hơn. Mọi người làm việc trên mã nguồn của bạn, bao gồm cả chính bạn trong tương lai, sẽ cảm ơn bạn vì điều đó!

## Thứ tự đọc (In Order)

Vậy là bây giờ bạn đã có một góc nhìn rộng mở hơn về những gì còn lại cần khám phá trong JS, cũng như thái độ đúng đắn để tiếp cận phần còn lại của cuộc hành trình.

Thế nhưng một trong những câu hỏi thực tế phổ biến nhất mà tôi nhận được tại thời điểm này là: "Tôi nên đọc các cuốn sách theo thứ tự nào?" Có một câu trả lời trực tiếp... nhưng nó cũng còn tùy thuộc.

Gợi ý của tôi dành cho hầu hết các độc giả là hãy tiến bước qua bộ sách này theo thứ tự sau:

1. Bắt đầu với một nền tảng vững chắc về JS từ *Bắt đầu* (*Get Started* - Cuốn 1) -- tin mừng là bạn đã gần đọc xong cuốn này rồi!

2. Trong *Phạm vi & Closure* (*Scope & Closures* - Cuốn 2), hãy đào sâu vào trụ cột đầu tiên của JS: phạm vi từ vựng (lexical scope), cách nó hỗ trợ closure, và cách mẫu module tổ chức mã nguồn.

3. Trong *Đối tượng & Class* (*Objects & Classes* - Cuốn 3), hãy tập trung vào trụ cột thứ hai của JS: cách `this` của JS hoạt động, cách các prototype của đối tượng hỗ trợ ủy quyền (delegation), và cách prototype kích hoạt cơ chế `class` để tổ chức mã nguồn theo phong cách hướng đối tượng (OO).

4. Trong *Kiểu & Ngữ pháp* (*Types & Grammar* - Cuốn 4), hãy giải quyết trụ cột thứ ba và cũng là trụ cột cuối cùng của JS: các kiểu dữ liệu và ép kiểu dữ liệu, cũng như cách cú pháp và ngữ pháp của JS định hình cách chúng ta viết mã.

5. Với **ba trụ cột** đã được định hình vững chắc, *Đồng bộ & Bất đồng bộ* (*Sync & Async* - Cuốn 5) sau đó sẽ khám phá cách chúng ta sử dụng luồng điều khiển (flow control) để mô hình hóa sự thay đổi trạng thái trong các chương trình của mình, cả đồng bộ (ngay lập tức) lẫn bất đồng bộ (theo thời gian).

6. Bộ sách khép lại với *ES.Next & Xa hơn nữa* (*ES.Next & Beyond* - Cuốn 6), một cái nhìn hướng về tương lai gần và trung hạn của JS, bao gồm nhiều tính năng khác nhau có khả năng sẽ sớm xuất hiện trong các chương trình JS của bạn.

Đó là thứ tự dự kiến để đọc bộ sách này.

Tuy nhiên, các Cuốn 2, 3 và 4 nhìn chung có thể được đọc theo bất kỳ thứ tự nào, tùy thuộc vào chủ đề nào bạn cảm thấy tò mò nhất và thoải mái khám phá trước nhất. Nhưng tôi không khuyên bạn bỏ qua bất kỳ cuốn nào trong ba cuốn này—ngay cả *Types & Grammar*, dù một số bạn sẽ rất dễ bị cám dỗ làm điều đó!—kể cả khi bạn nghĩ rằng mình đã nắm vững chủ đề đó rồi.

Cuốn 5 (*Sync & Async*) mang tính quyết định để hiểu sâu về JS, nhưng nếu bạn bắt đầu đi sâu vào và thấy nó quá khó nuốt, cuốn sách này có thể được hoãn lại cho đến khi bạn có nhiều kinh nghiệm hơn với ngôn ngữ. Càng viết nhiều JS (và càng phải vật lộn với nó!), bạn sẽ càng trân trọng cuốn sách này hơn. Vì vậy đừng ngần ngại quay lại với nó vào một thời điểm sau đó.

Cuốn sách cuối cùng trong bộ, *ES.Next & Beyond*, ở một số khía cạnh có thể đứng độc lập. Nó có thể được đọc ở cuối cùng, như tôi gợi ý, hoặc ngay sau *Get Started* nếu bạn đang tìm kiếm một con đường tắt để mở rộng tầm nhìn của mình về toàn bộ những gì JS có. Cuốn sách này cũng có nhiều khả năng nhận được các bản cập nhật trong tương lai, vì vậy có thể bạn sẽ muốn xem lại nó định kỳ.

Dù bạn chọn tiếp tục với YDKJSY theo cách nào, trước tiên hãy xem các phần phụ lục của cuốn sách này, đặc biệt là thực hành các đoạn mã trong Phụ lục B, "Thực hành, Thực hành, Thực hành!" Tôi đã nhắc bạn là nên đi thực hành chưa nhỉ!? Không có cách nào tốt hơn để học lập trình bằng việc tự tay viết mã.
