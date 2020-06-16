---
layout: post
title: Tôi đã tạo chatbot cho Chatwork như thế nào?
image: /img/google-apps-script.png
---

Với mục tiêu đề ra là **tạo được một con chatbot trên chatwork**, ta sẽ đi từng bước để hoàn thành được mục tiêu này.

Đầu tiên ta sẽ tìm hiểu về API của chatwork theo link [này](https://developer.chatwork.com/vi/). 
Có thể thấy, nếu muốn thao tác được với api của chatwork thì ta phải có **API token**, để lấy được token này thì ta sẽ tạo một tài khoản chatwork mới, rồi vào [link](https://www.chatwork.com/service/packages/chatwork/subpackages/api/token.php) và nhập pasword thì sẽ nhận được token.

![Image 1](/img/token.png)

Sau khi lấy được token, với mục đích là tạo bot gửi tin nhắn trong room thì ta cần dùng đến endpoint https://api.chatwork.com/v2/rooms/{room_id}/messages với room_id là dãy số cuối trong url khi vào một room nào đó. Ví dụ: https://www.chatwork.com/#!rid{room_id}

Tiếp theo ta sẽ dùng Google App Script(GAS) để viết code tương tác với api của chatwork.

Giới thiệu một chút về GAS, thì GAS là một trình biên dịch, soạn thảo bằng ngôn ngữ Javascript được cung cấp bởi Google và cho phép người dùng thao tác với các sản phẩm của gói dịch vụ G Suite như Docs, Sheets hay Forms. Với GAS ta có thể làm được một số tính năng như sau:
- Thêm menu, dialog hay sidebar vào Google Doc, Sheet và Form.
- Viết function cho Google Spreadsheet.
- Tạo các ứng dụng web độc lập hay ứng dụng web nhúng trong Google Sites.
- Giao tiếp với các dịch vụ khác của Google như AdSense, Analytics, Calendar, Drive, Gmail và Maps.
- Xây dựng các add-on mở rộng cho Google Docs, Sheets, Forms và đưa chúng lên Add-on store.
- Chuyển một ứng dụng Android thành add-on Android có thể trao đổi dữ liệu với Google Doc hay Google Spreadsheet của người dùng trên thiết bị di động.

Vậy ta phải cần những gì để có thể sử dụng được GAS. Rất đơn giản, chỉ cần một chút kiến thức lập trình cơ bản về JavaScript là đủ, còn editor và môi trường chạy tất cả đã có Google lo :D. Google cũng cung cấp bộ tài liệu hướng dẫn khá chi tiết về GAS trên trang web của họ [tại đây](https://developers.google.com/apps-script/) .

Ta sẽ tạo một project script mới trên GAS [tại đây](https://script.google.com/u/0/home) và đặt tên là chatbot for Chatwork với nội dung như sau:

![Image 2](/img/script.png)

Nhấn nút Run để chạy thử và xem thành quả.

![Image 3](/img/test.png)

Nice! Tiếp theo ta sẽ đặt thời gian để Run đoạn script này sau mỗi 15 phút để nhắc nhở việc uống nước, bằng cách thêm trigger đặt thời gian.

![Image 4](/img/trigger.png)

Happy Coding !
