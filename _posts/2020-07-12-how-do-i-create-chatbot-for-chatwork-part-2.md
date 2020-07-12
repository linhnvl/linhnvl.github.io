---
layout: post
title: Tôi đã tạo chatbot cho Chatwork như thế nào (Phần 2)?
image: /img/giphy.png
---

Sau khi đã tạo được một con bot để nhắc nhở công việc ở phần trước, ở phần này mình sẽ thêm tính năng để cho con Bot bớt "ngu" hơn :v

**Mục tiêu đặt ra:**
- Con Bot có thể tự động search hình ảnh dựa theo nội dung tin nhắn nhận được, sau đó tải ảnh về và upload lên lại chatwork.

Để làm được tính năng này, ta cần phải làm những công việc sau:

***1)Tạo thêm webhook bắt những sự kiện của Chatwork để giúp con Bot có thể tương tác được với người khác***

Ta sẽ tạo webhook trên trang chủ Chatwork, và chọn Acount Event để bắt các sự kiện như TO, TOALL, REPLY.
![Image 1](/img/2020-07-12/hook_1.png)

Dựa theo document của [Webhook](https://developer.chatwork.com/vi/webhook.html) ta sẽ biết được định dạng response trả về, từ đó có thể lấy được các thông tin như nội dung tin nhắn, id của người gửi,...

***2)Gọi đến API của Giphy và search ảnh gif với từ khóa là nội dung tin nhắn đã nhận***

Để giao tiếp với api của Giphy ta cũng làm tương tự như với Chatwork, ta cũng vào [trang chủ](https://developers.giphy.com/docs/api#quick-start-guide) tạo app, rồi lấy token sau đó gọi đến Search Endpoint, với params q là nội dung tin nhắn nhận được ở bước 1.
![Image 2](/img/2020-07-12/giphy_api.png)

***3)Dựa theo kết quả để tải ảnh về và sau đó upload lên Chatwork***

Dựa theo response trả về của Giphy ta sẽ lấy được url của ảnh, sau đó tải ảnh về thư mục local, tiếp đó gọi đến api upload file để tải ảnh lên lại.
![Image 3](/img/2020-07-12/post_man.png)

Và đây là thành quả :D
![Image 4](/img/2020-07-12/cw_1.png)
![Image 5](/img/2020-07-12/cw_2.png)


Github: https://github.com/linhnvl/Chatphy

Happy coding !!!
