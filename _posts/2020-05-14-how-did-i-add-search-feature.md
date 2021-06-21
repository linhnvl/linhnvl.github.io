---
layout: post
title: Tôi đã thêm tính năng tìm kiếm như thế nào?
image: /img/search_image.jpg
---

Mỗi trang web thì đều có các tính năng cơ bản là thêm, sửa, xóa, tìm kiếm gọi tắt là CRUD. Trong bài này tôi sẽ thêm chức năng tìm kiếm cho trang web cho trang blog của mình.
Blog này đang là dạng web tĩnh nên sẽ không có các xử lí phía server như các trang web động, nên để thêm chức năng tìm kiếm ta phải sử dụng công cụ của Google là Custom Search Engine.


Đầu tiên ta truy cập vào trang [tạo công cụ tìm kiếm](https://programmablesearchengine.google.com/cse/create/new). Sau đó nhập đầy đủ các thông tin cho công cụ tìm kiếm.
![Image 1](/img/i_1.png)

Sau đó chọn **Get the code** để nhận mã.
![Image 2](/img/i3.png)

Tiếp theo tạo file search.md trong thư mục gốc website với nội dung tương tự như sau (thay nội dung nằm trong cặp thẻ <div id="search-box"> đến </div> bằng đoạn code copy được ở trang Google Custom Search vừa rồi).
Vậy là ta đã xây dựng thành công công cụ tìm kiếm cho website của mình rồi. Bạn có thể bắt đầu sử dụng tại <website url>/search/ (vd https://linhnvl.github.io/search/).

![Image 3](/img/i_4.png)
