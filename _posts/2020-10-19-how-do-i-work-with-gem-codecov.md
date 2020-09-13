---
layout: post
title: Tôi đã làm việc với Gem Codecov như thế nào?
image: /img/2020-10-19/im1.jpeg
---

Tiếp nối chủ đề về Unit Test ở lần trước, lần này mình sẽ chia sẻ về cách hoạt động của Gem Codecov từ đó rút ra được những kinh nghiệm khi làm việc với Gem này.

**Mở đầu**

Gem Codecov là một công cụ giúp chúng ta cải thiện quy trình review và chất lượng của các dòng code. Codecov còn cung cấp các công cụ tích hợp cao để nhóm, hợp nhất, lưu trữ và so sánh các báo cáo phạm vi.

Code coverage (độ bao phủ code) là một phép đo lường để biểu thị những dòng code nào đã được thực thi bởi các dòng test. Có 3 thuật ngữ để miêu tả độ biểu thị đó:
- hit:  biểu thị rằng các dòng code đã được thực thi bởi các dòng test
- partial: biểu thị rằng các dòng code chưa được thực thi đầy đủ, vẫn còn một số chỗ chưa được thực thi
- miss: biểu thị các dòng code chưa hề được bao phủ

Công thức tính tỉ lệ bao phủ là: hits / (hits + partials + misses)

**Cách hoạt động**

Gem codecov sẽ đựơc tích hợp vào CI để hiển thị tỉ lệ bao phủ, số dòng code bị tác động bởi những thay đổi trong 1 Pull Request.
![Image 1](/img/2020-10-19/img2.png)

Khi ta sửa code trong 1 hàm nào đó, thì Gem Codecov sẽ tính toán để biểu thị cho ta những file nào sẽ bị ảnh hưởng ở trong bảng Impacted Files.
Ta cũng có thể xem được các dòng cụ thể nào đang bị miss hoặc partial trong report của codecov.
![Image 2](/img/2020-10-19/img3.png)

**Một số kinh nghiệm rút ra**

- Gem codecov sẽ dựa vào base commit tức là commit ở nhánh trước đó để so sánh. Ví dụ ta tạo 1 PR để merge vào nhánh develop thì base commit chính là commit ở nhánh develop. Do đó, ở PR đầu tiên sau khi tích hợp Gem Codecov vào CI thì nó sẽ chưa so sánh được các file thay đổi
- Ta sẽ hay gặp trường hợp là khi ta sửa code trong 1 file nhưng khi tạo PR thì Codecov lại báo lỗi ở 1 file khác, dẫn đến CI bị fail. 
  
  Ví dụ: ta sửa code ở 1 hàm trong model User, mà hàm đó lại được gọi ở các model Company có quan hệ với bảng User. Nếu như ta chưa viết test cho model Company thì gem Codecov sẽ phát hiện và báo fail ngay lập tức.

  Do đó, ta phải đảm bảo những dòng code viết ra đều phải tracking được độ ảnh hưởng và có những dòng test bao phủ cho nó ngay trong PR.
- Nếu apply gem Codecov theo hướng dẫn trong [github](https://github.com/codecov/codecov-ruby) thì sẽ rất hay gặp lỗi không tạo được report hoặc tính tỉ lệ cover không đúng. 
  
  Để khắc phục lỗi đó thì ta nên dùng các cách khác thường được tích hợp luôn trong CI. Ví dụ: CircleCI có hỗ  trợ sẵn orbs Codecov với version mới nhất.
  ```
  orbs:
    codecov: codecov/codecov@1.1.1
  ```

Việc áp dụng gem Codecov vào CI đang ngày càng phổ biến và rất hữu ích khi apply vào một project lớn đã release chẳng hạn. Nó sẽ giúp chúng ta cover được những trường hợp làm giảm tỉ lệ bao phủ hoặc làm crash app.

Happy coding !!!
