---
layout: post
title: Tôi đã tạo docker image lên dockerhub như thế nào?
image: /img/2021-04-20/docker.png
---


**Tạo Docker repository**

Đầu tiên, chúng ta cần tạo 1 repository trên Docker Hub, đây sẽ là nơi lưu trữ image của chúng ta.

Để tạo repository, hãy đăng nhập Docker Hub và truy cập trang:
[create repository](https://hub.docker.com/repository/create)

**Tạo 1 repo trên Github và liên kết với Dockerhub**

Ta sẽ push 1 repo có Dockerfile lên Github, sau đó liên kết với docker hub
[linking-account](https://hub.docker.com/settings/linked-accounts)

![Image 1](/img/2021-04-20/linking-github.png)

Sau đó chỉ cần click vào "Save and Build" và chờ đợi một chút là ta đã có thể tạo được 1 docker image cho riêng mình :p

Đây là thành quả

![Image 2](/img/2021-04-20/build-success.png)

**Sử dụng image đã tạo**

Để sử dụng image, ta lần lượt dùng các command `docker login` và `docker pull linhnvl/ruby-image:latest`

![Image 3](/img/2021-04-20/pull-image.png)

Happy coding !!!
