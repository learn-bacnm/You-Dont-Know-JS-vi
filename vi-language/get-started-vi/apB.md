# You Don't Know JS Yet: Bắt đầu - Tái bản lần 2
# Phụ lục B: Thực hành, thực hành, và thực hành!

Trong phụ lục này, chúng ta sẽ cùng khám phá một số bài tập và lời giải gợi ý tương ứng. Những bài tập này chỉ nhằm mục đích giúp bạn *bắt đầu* luyện tập các khái niệm đã học trong cuốn sách.

## Luyện tập phép so sánh (Comparisons)

Hãy cùng luyện tập làm việc với các kiểu giá trị và phép so sánh (Chương 4, Trụ cột 3) - nơi mà ép kiểu (coercion) sẽ cần phải tham gia vào.

Hàm `scheduleMeeting(..)` nhận vào một thời gian bắt đầu (ở định dạng 24 giờ dưới dạng chuỗi "hh:mm") và thời lượng cuộc họp (số phút). Nó sẽ trả về `true` nếu cuộc họp hoàn toàn nằm trong ngày làm việc (theo các mốc thời gian được chỉ định trong `dayStart` và `dayEnd`); trả về `false` nếu cuộc họp vượt quá giới hạn của ngày làm việc.

```js
const dayStart = "07:30";
const dayEnd = "17:45";

function scheduleMeeting(startTime,durationMinutes) {
    // ..TODO..
}

scheduleMeeting("7:00",15);     // false
scheduleMeeting("07:15",30);    // false
scheduleMeeting("7:30",30);     // true
scheduleMeeting("11:30",60);    // true
scheduleMeeting("17:00",45);    // true
scheduleMeeting("17:30",30);    // false
scheduleMeeting("18:00",15);    // false
```

Trước hết, hãy tự mình thử giải bài toán này. Hãy cân nhắc việc sử dụng các toán tử so sánh bằng (equality) và so sánh quan hệ (relational comparison), cũng như cách ép kiểu tác động đến đoạn mã này. Khi bạn đã có mã hoạt động được, hãy *so sánh* (compare) (các) giải pháp của bạn với đoạn mã trong phần "Lời giải gợi ý" ở cuối phụ lục này.

## Luyện tập Closure

Bây giờ hãy cùng luyện tập với closure (Chương 4, Trụ cột 1).

Hàm `range(..)` nhận một số làm đối số đầu tiên, đại diện cho số đầu tiên trong dải số mong muốn. Đối số thứ hai cũng là một số đại diện cho điểm kết thúc của dải số (bao gồm cả giá trị đó - inclusive). Nếu đối số thứ hai bị bỏ qua, thì hàm sẽ trả về một hàm khác đang đợi nhận đối số đó.

```js
function range(start,end) {
    // ..TODO..
}

range(3,3);    // [3]
range(3,8);    // [3,4,5,6,7,8]
range(3,0);    // []

var start3 = range(3);
var start4 = range(4);

start3(3);     // [3]
start3(8);     // [3,4,5,6,7,8]
start3(0);     // []

start4(6);     // [4,5,6]
```

Trước hết hãy tự mình thử giải quyết bài này.

Sau khi đã có mã hoạt động được, hãy *so sánh* (các) giải pháp của bạn với đoạn mã trong phần "Lời giải gợi ý" ở cuối phụ lục này.

## Luyện tập Prototypes

Cuối cùng, hãy cùng thực hành với `this` và các đối tượng được liên kết thông qua prototype (Chương 4, Trụ cột 2).

Hãy định nghĩa một máy đánh bạc (slot machine) với ba cuộn quay (reels) có thể quay độc lập (`spin()`), và sau đó hiển thị (`display()`) nội dung hiện tại của tất cả các cuộn quay.

Hành vi cơ bản của một cuộn quay đơn lẻ được định nghĩa trong đối tượng `reel` bên dưới. Nhưng máy đánh bạc cần các cuộn quay riêng biệt—các đối tượng ủy quyền (delegate) đến `reel`, và mỗi cuộn quay đều có thuộc tính `position` của riêng nó.

Một cuộn quay chỉ *biết cách* hiển thị (`display()`) biểu tượng khe hiện tại của nó, nhưng một máy đánh bạc thông thường sẽ hiển thị ba biểu tượng trên mỗi cuộn: khe hiện tại (`position`), một khe phía trên (`position - 1`), và một khe phía dưới (`position + 1`). Do đó, việc hiển thị máy đánh bạc cuối cùng sẽ phải hiển thị một lưới các biểu tượng kích thước 3 x 3.

```js
function randMax(max) {
    return Math.trunc(1E9 * Math.random()) % max;
}

var reel = {
    symbols: [
        "♠", "♥", "♦", "♣", "☺", "★", "☾", "☀"
    ],
    spin() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        this.position = (
            this.position + 100 + randMax(100)
        ) % this.symbols.length;
    },
    display() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        return this.symbols[this.position];
    }
};

var slotMachine = {
    reels: [
        // máy đánh bạc này cần 3 cuộn riêng biệt
        // gợi ý: Object.create(..)
    ],
    spin() {
        this.reels.forEach(function spinReel(reel){
            reel.spin();
        });
    },
    display() {
        // TODO
    }
};

slotMachine.spin();
slotMachine.display();
// ☾ | ☀ | ★
// ☀ | ♠ | ☾
// ♠ | ♥ | ☀

slotMachine.spin();
slotMachine.display();
// ♦ | ♠ | ♣
// ♣ | ♥ | ☺
// ☺ | ♦ | ★
```

Trước hết hãy tự mình thử giải bài này.

Gợi ý:

* Sử dụng toán tử lấy số dư `%` (modulo) để quay vòng `position` khi bạn truy cập các biểu tượng theo vòng tròn quanh cuộn quay.

* Sử dụng `Object.create(..)` để tạo một đối tượng và liên kết prototype của nó tới một đối tượng khác. Một khi đã được liên kết, cơ chế ủy quyền (delegation) cho phép các đối tượng chia sẻ ngữ cảnh `this` trong quá trình gọi phương thức.

* Thay vì sửa đổi trực tiếp đối tượng cuộn quay để hiển thị từng vị trí trong ba vị trí, bạn có thể sử dụng một đối tượng tạm thời khác (lại dùng `Object.create(..)`) với thuộc tính `position` của riêng nó để ủy quyền từ đó.

Khi bạn đã có mã chạy đúng, hãy *so sánh* (các) giải pháp của bạn với đoạn mã trong phần "Lời giải gợi ý" ở cuối phụ lục này.

## Lời giải gợi ý

Hãy ghi nhớ rằng những lời giải gợi ý này chỉ đơn thuần là: những gợi ý. Có rất nhiều cách khác nhau để giải các bài tập thực hành này. Hãy so sánh cách tiếp cận của bạn với những gì bạn thấy ở đây, và cân nhắc ưu cũng như nhược điểm của từng cách.

Lời giải gợi ý cho bài thực hành "Phép so sánh" (Trụ cột 3):

```js
const dayStart = "07:30";
const dayEnd = "17:45";

function scheduleMeeting(startTime,durationMinutes) {
    var [ , meetingStartHour, meetingStartMinutes ] =
        startTime.match(/^(\d{1,2}):(\d{2})$/) || [];

    durationMinutes = Number(durationMinutes);

    if (
        typeof meetingStartHour == "string" &&
        typeof meetingStartMinutes == "string"
    ) {
        let durationHours =
            Math.floor(durationMinutes / 60);
        durationMinutes =
            durationMinutes - (durationHours * 60);
        let meetingEndHour =
            Number(meetingStartHour) + durationHours;
        let meetingEndMinutes =
            Number(meetingStartMinutes) +
            durationMinutes;

        if (meetingEndMinutes >= 60) {
            meetingEndHour = meetingEndHour + 1;
            meetingEndMinutes =
                meetingEndMinutes - 60;
        }

        // tái tạo các chuỗi thời gian đầy đủ
        // (để việc so sánh trở nên dễ dàng hơn)
        let meetingStart = `${
            meetingStartHour.padStart(2,"0")
        }:${
            meetingStartMinutes.padStart(2,"0")
        }`;
        let meetingEnd = `${
            String(meetingEndHour).padStart(2,"0")
        }:${
            String(meetingEndMinutes).padStart(2,"0")
        }`;

        // LƯU Ý: vì tất cả các biểu thức đều là chuỗi,
        // các phép so sánh ở đây là so sánh theo thứ tự bảng chữ cái,
        // nhưng ở đây lại an toàn vì chúng là các chuỗi thời gian đầy đủ
        // (nghĩa là "07:15" < "07:30")
        return (
            meetingStart >= dayStart &&
            meetingEnd <= dayEnd
        );
    }

    return false;
}

scheduleMeeting("7:00",15);     // false
scheduleMeeting("07:15",30);    // false
scheduleMeeting("7:30",30);     // true
scheduleMeeting("11:30",60);    // true
scheduleMeeting("17:00",45);    // true
scheduleMeeting("17:30",30);    // false
scheduleMeeting("18:00",15);    // false
```

----

Lời giải gợi ý cho bài thực hành "Closure" (Trụ cột 1):

```js
function range(start,end) {
    start = Number(start) || 0;

    if (end === undefined) {
        return function getEnd(end) {
            return getRange(start,end);
        };
    }
    else {
        end = Number(end) || 0;
        return getRange(start,end);
    }


    // **********************

    function getRange(start,end) {
        var ret = [];
        for (let i = start; i <= end; i++) {
            ret.push(i);
        }
        return ret;
    }
}

range(3,3);    // [3]
range(3,8);    // [3,4,5,6,7,8]
range(3,0);    // []

var start3 = range(3);
var start4 = range(4);

start3(3);     // [3]
start3(8);     // [3,4,5,6,7,8]
start3(0);     // []

start4(6);     // [4,5,6]
```

----

Lời giải gợi ý cho bài thực hành "Prototypes" (Trụ cột 2):

```js
function randMax(max) {
    return Math.trunc(1E9 * Math.random()) % max;
}

var reel = {
    symbols: [
        "♠", "♥", "♦", "♣", "☺", "★", "☾", "☀"
    ],
    spin() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        this.position = (
            this.position + 100 + randMax(100)
        ) % this.symbols.length;
    },
    display() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        return this.symbols[this.position];
    }
};

var slotMachine = {
    reels: [
        Object.create(reel),
        Object.create(reel),
        Object.create(reel)
    ],
    spin() {
        this.reels.forEach(function spinReel(reel){
            reel.spin();
        });
    },
    display() {
        var lines = [];

        // hiển thị tất cả 3 hàng trên máy đánh bạc
        for (
            let linePos = -1; linePos <= 1; linePos++
        ) {
            let line = this.reels.map(
                function getSlot(reel){
                    var slot = Object.create(reel);
                    slot.position = (
                        reel.symbols.length +
                        reel.position +
                        linePos
                    ) % reel.symbols.length;
                    return slot.display();
                }
            );
            lines.push(line.join(" | "));
        }

        return lines.join("\n");
    }
};

slotMachine.spin();
slotMachine.display();
// ☾ | ☀ | ★
// ☀ | ♠ | ☾
// ♠ | ♥ | ☀

slotMachine.spin();
slotMachine.display();
// ♦ | ♠ | ♣
// ♣ | ♥ | ☺
// ☺ | ♦ | ★
```

Đến đây là kết thúc cuốn sách này. Nhưng giờ đã đến lúc bạn tìm kiếm các dự án thực tế để thực hành những ý tưởng này rồi. Hãy cứ tiếp tục viết code, bởi vì đó chính là cách học tốt nhất!
