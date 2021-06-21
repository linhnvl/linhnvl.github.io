---
layout: post
title: Tôi đã tạo Terminal yêu thích của mình như thế nào?
image: /img/2021-07-20/tmux.png
---

**Mở đầu**

Đối với 1 lập trình viên thì có 3 ứng dụng gần như luôn được sử dụng thường xuyên đó là: Trình duyệt(Chrome, Firefox, Safari..), Trình IDE (Vs Code, Sublime, Atom...) và Terminal.

Ở bài này mình sẽ chia sẻ về cách mình đã customize lại Terminal để có giao diện đẹp và làm việc hiệu quả hơn nhiều lần so với Terminal mặc định.

**Bước 1: Thay đổi giao diện**

Terminal mặc định của Ubuntu sẽ có một màu tím chán ngắt, vừa đơn điệu vừa không gây thiện cảm cho người dùng. Vì thế mình đã dùng ZSH để thay đổi giao diện cho nó.

Cài đặt zsh `sudo apt-get install zsh` và cài đặt oh-my-zsh theo hướng dẫn ở trang chủ [oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh#basic-installation)

Với oh-my-zsh ta có thể cài đặt phím tắt, cài các plugin và thay đổi theme của terminal rất dễ dàng. Theme mà mình đang sử dụng là ***agnoster*** vừa đẹp mắt lại vừa hài hoà về màu sắc. Install theme agnoster và Powerline Font theo hướng dẫn của tác giả Hồ Việt Tuấn ở [bài viết này](https://viblo.asia/p/terminal-bot-te-nhat-va-de-su-dung-voi-terminator-va-zsh-tren-ubuntu-pDOKjkrXzPr#_huong-dan-cai-dat-themes-agnoster-6).

Và thành quả sau khi thay đổi giao diện.
![photo-collage](/img/2021-07-20/photo-collage.png)

**Bước 2: Thay đổi cách dùng cơ bản**

Với Terminal mặc định thì việc chia màn hình, chia tab, thay đổi việc phím tắt tuy là đều được hỗ trợ nhưng vẫn tương đối khó dùng, với ***Tmux*** thì sẽ khắc phục hết được những yếu điểm đó và còn giúp quản lý được tất cả config một cách dễ dàng.

`sudo apt install -y tmux`

Sau khi cài đặt thì Tmux sẽ tự động tích hợp vào Terminal, tổ hợp phím mặc định của Tmux sẽ hơi khó dùng với người mói tiếp cận, nhưng sau khi đã thành thục thì bạn sẽ thấy tiện dụng và nhanh hơn rất nhiều.

Tham khảo các phím tắt [tmux cheatsheet](https://tmuxcheatsheet.com/).

Tính năng hay nhất của Tmux đó chính là lưu giữ được session của Terminal. Tương tự như ở Chrome thì ta có thể lưu lại tất cả các Tab đang mở, để khi tắt trình duyệt rồi mở lại thì các Tab đang mở vẫn giữ nguyên không hề bị mất đi.

![Demo session](/img/2021-07-20/session.gif)

**Bước 3: Thêm các dòng config cho zsh và tmux**

Đối với Zsh thì việc cài thêm các plugin, đặt thêm các alias sẽ rất dễ dàng, bởi vì ở trang chủ oh-my-zsh đã hướng dẫn rất chi tiết. Còn đối với tmux thì mọi thứ sẽ khó khăn hơn một chút :v, bởi vì phần docs ở trên trang chủ viết khá khó hiểu. Đại khái là để config lại các phím tắt, giao diện của tmux thì cần phải tạo 1 file `.tmux.conf` ở  trong thư mục /home, sau đó thêm các config theo cú pháp ở [đây](https://github.com/tmux/tmux/wiki/Getting-Started#configuring-tmux)

Chẳng hạn như mình đã thêm config để đổi default prefix key của tmux từ *Ctrl+b* sang *Ctrl+a* để  vừa dễ bấm, vừa nhanh hơn khi phải thao tác nhiều.

```
# change default prefix key
set -g prefix C-a
unbind C-b
bind C-a send-prefix
```

Có thể chia màn hình, resize màn hình và chuyển đổi nhanh giữa các tab
![Demo split window](/img/2021-07-20/split_window.gif)

**Bước 4: Tạo dotfile để đem đi khắp mọi nơi**

Để có thể dễ dàng apply lại các config khi phải chuyển sang máy tính khác hoặc là phải cài lại Ubuntu, ta có thể  bỏ hết các file config của zsh, tmux, vs code vào 1 folder thường được gọi là *dotfiles*, sau đó push lên github để lưu trữ. Khi cần phải apply lại thì chỉ cần clone folder về và copy đến thư mục home là được rồi :D

Dotfiles của mình: [dotfiles](https://github.com/linhnvl/dotfiles)

Chúc các bạn sẽ có được một Terminal yêu thích để mỗi khi nhìn vào nó sẽ có thêm động lực và niềm vui khi code (dance)

Happy coding !!!
