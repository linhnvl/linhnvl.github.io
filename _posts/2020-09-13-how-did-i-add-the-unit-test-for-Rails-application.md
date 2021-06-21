---
layout: post
title: Tôi đã thêm Unit Test cho Rails như thế nào?
image: /img/2020-09-13/rspec.jpg
---

Đối với một Developer thì việc kiểm thử (Testing) trong một project là điều gần như bắt buộc để có được một sản phẩm chất lượng, và đặc biệt trong một project Rails sử dụng Agile.
Nếu các Unit Test được viết càng chặt chẽ, độ bao phủ càng cao thì sẽ nâng cao được sự tự tin, chắc chắn cho các maintainer sau này nếu muốn thêm một chức năng mới nào đó mà không làm ảnh hưởng tới hệ thống.

Mình vừa hoàn thành một task là thêm UT cho 1 project đã có từ lâu, cụ thể  là tăng tỉ lệ bao phủ UT từ hơn 70% lên 95%.
Vấn đề  đặt ra là đây là một project lớn, số dòng code rất nhiều và mình thì không tham gia code từ đầu nên còn rất nhiều thứ chưa nắm được.
Trong bài này mình sẽ chia sẻ các chiến lược, các phương thức để nâng cao độ chặt chẽ, độ bao phủ cho UT mà mình đã tổng hợp được.

**Nên test những file nào trước ?**

Khi sử dụng gem 'simplecov' thì nó sẽ chia các file trong project ra làm các nhóm chính:
- Controllers
- Models
- Mailers
- Helpers
- Jobs
- Services

1) Kinh nghiệm mình rút ra được là nên đi từ những file đơn giản như Mailers, Helpers vì đó là những file không xử lí nhiều logic, nên có thể dễ dàng tiếp cận ngay cả khi chưa nắm được logic của hệ thống.

2) Tiếp theo là những file Models, vì thường trong models sẽ chỉ chứa các relation và validate nên cũng dễ xử lí hơn. Viết test cho các file này sẽ giúp ta hiểu được cấu trúc của database ra sao từ đó hiểu được một cách tổng quan về  business của project.

3) Sau đó là đến các file Controllers, đây là nơi tập trung các logic, xử lí các tính năng của hệ thống. Ta có thể kết hợp viết test cho các file Services, Workers nếu nó được gọi đến ở trong controllers luôn.

4) Cuối cùng là các file còn lại, khi test xong ở controllers thì gần như tỉ lệ bao phủ đã khá cao rồi, nhưng có thể vẫn còn những jobs được đặt chạy theo lịch hằng ngày. Đây cũng là một bước để rà soát lại xem thử có còn sót lại file nào nữa không.

**Test những service của bên thứ 3**

Thường trong các dự án, khi gặp các third-party như AWS, Plusbenlly hay Firebase... thì mọi người thường hay bỏ qua không viết Test. Bởi vì đây là những tác vụ mà chúng ta không trực tiếp xử lí trong hệ thống, ta chỉ việc truyền input và nhận được output từ third-party.
Nhưng để đạt được tỉ lệ bao phủ tốt nhất thì ta vẫn phải viết Test bằng 2 cách chính sau đây:
- Dùng gem 'webmock' để stub các request HTTP đến bên thứ ba
  
  Ta có thể stub request gửi đi sau đó test các giá trị trả về
  ```
  stub_request(:delete, url).with(
    headers: {
      "Authorization" => "Authorization",
      "Accept" => "*/*",
      "Accept-Encoding" => "gzip;q=1.0,deflate;q=0.6,identity;q=0.3",
      "User-Agent" => "Faraday v0.15.4",
      "X-Benlly-Request-Device": "web",
      "X-Benlly-Udid" => X-Benlly-Udid
    }
  ).to_return(status: 200)

  expect(user.deleted?).to eq true
  ```
  Hoặc test xem request gửi đi có đúng hay không
  ```
  expect(WebMock).to have_requested(:post, url).with(
    headers: {
      "Authorization" => "Authorization",
      "Accept" => "*/*",
      "Accept-Encoding" => "gzip;q=1.0,deflate;q=0.6,identity;q=0.3",
      "User-Agent" => "Faraday v0.15.4",
      "X-Benlly-Request-Device" => "web",
      "Authorization" => Authorization
    }
  )
  ```
- Dùng cách mock để các third-party đó trả về  giá trị mong muốn
  ```
  allow_any_instance_of(Aws::SNS::Client).to receive(:create_platform_endpoint).and_return(create_endpoint_response)
  allow_any_instance_of(Aws::SNS::Client).to receive(:publish).and_raise(Aws::SNS::Errors::ServiceError.new(nil, nil))
  ```

**Test việc tạo file, xoá file trong hệ thống**

- Trong project này có một file service để xử lí khi admin export 1 file csv thì đồng thời cũng tạo ra 1 file csv trong hệ thống. Nếu test theo cách bình thường thì mỗi khi chạy test thì nó sẽ sinh ra một file trong hệ thống thực, lâu dần sẽ làm cho application ngày càng nặng hơn.
Dùng gem 'fakefs' sẽ giúp ta tạo ra một thư mục giả chỉ tồn tại trong lúc test và ta có thể thao tác tạo, xoá file tuỳ ý mà k làm ảnh hưởng đến hệ thống thực.
  ```
  FakeFS do
    FileUtils.mkdir_p(Rails.root.join("log"))
    File.open(Rails.root.join("log", "clear_user_export.log"), "wb")

    FileUtils.mkdir_p(Rails.root.join("tmp", "export"))
    File.open(Rails.root.join("tmp", "export", "#{job_id}.csv"), "wb")
  end
  ```

**Test việc gửi mail**

- Ví dụ, ta có một worker như sau:
  ```
  class AdminResetPasswordWorker < MailerWorker
    def perform admin_id
      admin = Admin.find admin_id
      send_mailer AdminMailer.reset_password_email(admin)
    end
  end
  ```
  Rõ ràng worker này chỉ xử lí việc gửi mail reset password cho admin, ở đây ta chỉ có thể test xem liệu email có được gửi đi hay không, còn nội dung email thì đã được test ở trong AdminMailer rồi.
  Ta sẽ dùng cách sau:
  ```
  it "should send email to admin" do
    expect(AdminMailer).to receive(:reset_password_email).with(admin).and_return(double("AdminMailer", deliver: true))
    AdminResetPasswordWorker.perform_async admin.id
  end
  ```
  Đặc biệt, ở đây ta sẽ phải dùng expect trước khi gọi workers, nếu ta gọi workers trước thì lúc đó email thực sẽ được gởi đi từ đó sẽ gây ra lỗi và dòng expect phía dưới cũng bị vô hiệu.

**Có nhất thiết phải tạo file rspec cho tất cả các file worker, services hay không ?**

- Câu trả lời là không. Tuỳ theo logic mà file đó xử lí như thế nào, có phức tạp hay không mà ta có thể kết hợp test luôn trong controller, model mà nó được gọi.
Khi đó là một file xử lý logic phức tạp, ngoài việc tạo ra 1 file rspec để test thì trong khi test controller, model mà nó được gọi ta có thể dùng cách stub để nó trả về giá trị mong muốn.
  ```
  allow(DistanceCalculatorService).to receive(:calculate).and_return 0
  ```
  Việc này sẽ giúp vừa cover được dòng code gọi đến service trong controller, vừa đảm bảo được độ bao phủ của service.

Trên đây là những kinh nghịêm mình rút ra được sau khi vừa hoàn thành 1 task dài 2 tháng của một dự án với vai trò maintainer. Hy vọng có thể giúp được mọi người và cả bản thân mình khi viết Unit Test cho Rails.

Happy coding !!!
