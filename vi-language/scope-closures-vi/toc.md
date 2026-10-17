# Bạn Chưa Biết JS Đâu: Phạm Vi & Closures (Scope & Closures) - Tái bản lần 2

## Mục lục

* Lời tựa
* Lời nói đầu
* Chương 1: Scope là gì?
    * Về cuốn sách này
    * Biên dịch (Compiled) vs. Thông dịch (Interpreted)
    * Biên dịch mã nguồn
    * Ngôn ngữ của trình biên dịch
    * "Gian lận": Sửa đổi phạm vi lúc thực thi (Run-Time Scope Modifications)
    * Lexical Scope
* Chương 2: Minh họa Lexical Scope
    * Những viên bi, những cái xô và bong bóng... Ôi trời!
    * Cuộc trò chuyện giữa những người bạn
    * Phạm vi lồng nhau (Nested Scope)
    * Tiếp tục cuộc trò chuyện
* Chương 3: Chuỗi phạm vi (The Scope Chain)
    * "Tra cứu" (Lookup) (phần lớn) mang tính khái niệm
    * Hiện tượng che khuất (Shadowing)
    * Phạm vi của tên hàm
    * Hàm mũi tên (Arrow Functions)
    * Rút lui
* Chương 4: Xoay quanh phạm vi toàn cục (Global Scope)
    * Tại sao lại cần phạm vi toàn cục?
    * Chính xác thì phạm vi toàn cục nằm ở đâu?
    * This toàn cục (Global This)
    * Nhận thức toàn cục
* Chương 5: Vòng đời (không mấy) bí mật của biến
    * Khi nào tôi có thể sử dụng một biến?
    * Hoisting: Lại một phép ẩn dụ nữa
    * Khai báo lại?
    * Các biến chưa được khởi tạo (tức là TDZ)
    * Cuối cùng cũng được khởi tạo
* Chương 6: Hạn chế phơi bày phạm vi (Limiting Scope Exposure)
    * Phơi bày tối thiểu (Least Exposure)
    * Ẩn giấu bên trong phạm vi (hàm) thông thường
    * Giới hạn phạm vi bằng các khối lệnh (Blocks)
    * Khai báo hàm bên trong khối lệnh
    * Vượt qua các rào cản khối lệnh
* Chương 7: Sử dụng Closures
    * Nhìn nhận Closure
    * Vòng đời của Closure và Thu gom rác (Garbage Collection - GC)
    * Một góc nhìn thay thế
    * Tại sao lại là Closure?
    * Đến gần hơn với Closure
* Chương 8: Mẫu thiết kế Module (The Module Pattern)
    * Đóng gói và Phơi bày tối thiểu (POLE)
    * Module là gì?
    * Node CommonJS Modules
    * ES Modules hiện đại (ESM)
    * Rời khỏi Scope
* Phụ lục A: Khám phá sâu hơn
    * Các phạm vi ngầm định (Implied Scopes)
    * Hàm ẩn danh vs. Hàm có tên
    * Hoisting: Hàm và Biến
    * Lý lẽ bảo vệ cho `var`
    * Thực chất TDZ là gì?
    * Các callback đồng bộ có phải là closure không?
    * Các biến thể module kinh điển
* Phụ lục B: Luyện tập
    * Những xô đựng bi
    * Closure (PHẦN 1)
    * Closure (PHẦN 2)
    * Closure (PHẦN 3)
    * Modules
    * Lời giải gợi ý

