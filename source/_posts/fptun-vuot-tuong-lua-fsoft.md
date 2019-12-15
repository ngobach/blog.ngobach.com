---
title: FPTun - Công cụ vượt tường lửa cho FSOFTers
date: 2019-12-15 13:58:54
tags: golang, fptun
---

Đau đầu vì cái tường lửa khốn nạn của FSOFT? Cứu tinh của bạn đây 😘.

🏠 https://github.com/ngobach/fptun
⬇️ https://github.com/ngobach/fptun/releases/latest

<!-- more -->

### Giới thiệu


Bất cứ ai đang hoặc đã là một FSOFTer chắc hẳn khó có thể 'độ' nổi tường lửa của FSOFT.
Với tiêu chí chặn nhầm còn hơn bỏ sót mà hầu hết các trang web đều bị chặn, thậm chí cả Github 🤕.

_Và mình cũng không phải là một ngoại lệ._

Mấy ngày đầu đặt chân vào FSOFT - cũng là công ty đầu tiên - mình phải làm quen với một cái gọi là 'môi trường cách ly'.
Okay chặn các trang giải trí và Facebook thì ko sao. Đằng này chặn luôn cả Github? Ơ thế đọc document kiểu gì? Qua điện thoại à 😳? (À mà đúng là có một số ông anh chỗ mình phải đọc docs qua điện thoại thật).

### Xây dựng công cụ

Trước khi xây dựng công cụ này mình vẫn phải ăn bám một kỹ thuật đó là SSH tunnelling.
Chắc hẳn ai quan tâm cũng biết FSoft chặn toàn bộ TCP và UDP packet ra internet và mọi traffic đều phải đi qua 1 con corporation proxy mang tên: **fsoft-proxy**. Đợt của mình khi vào FSoft thì con proxy này mới chỉ hoạt động được trên HTTP thôi còn đối với HTTPS nó chỉ bắt được target hostname (thông qua SNI), và chặn theo kiểu blacklisting, tức là tất cả các website đều có thể truy cập. Traffic qua HTTPS nó ko thể làm gì được nên mình đã có thể lợi dụng nó để bypass bằng cách mở SSHD của con VPS trên cổng 443.

```
+---------+    +-------------+    +------------+   +-----------+
|   YOU   +--->+ FSOFT PROXY +--->+  YOUR VPS  +-->+ INTERNET! |
+---------+    +-------------+    +------------+   +-----------+
```

Tuy nhiên đợt đầu 2018 khi mà FSOFT bắt đầu triển khai tường lửa cho HTTPS traffic thì cách này không còn hiệu quả nữa 😂

#### Bế tắc

À không hẳn là bế tắc. Một ngày đẹp trời khác mình tìm ra thêm một lỗ hổng của con fsoft-proxy này: khi mở 1 `HTTP CONNECT` request qua fsoft proxy đến địa chỉ IP thay vì hostname thì sẽ thành công, traffic ko hề bị reencrypt. Điều này có nghĩa là, từ bên trong mạng của FSOFT bạn có thể mở bất cứ TCP connection đến bất cứ địa chỉ nào ở internet...

> Here I come motherducker

Tuy nhiên đây mới chỉ là cách khai thác. Một vẫn đề nữa là trên mạng ko có công cụ nào giúp mình có thể khai thác lỗi này. Vậy thì thôi ta lại phải dùng đến đôi bàn tay rồi.
Tiện đang trong quá trình học và tìm hiểu Golang, công cụ này của mình đã ra đời.

### Sử dụng dư nào?

Mở link download ở trên sẽ dẫn bạn đến trang release Github. Nhặt đúng phiên bản hệ điều hành của bạn và chiến thôi.
File tải về là tool CLI, bạn đặt vào chỗ nào đó tiện nhất. Có thể là **System32** trên Windows hoặc **/usr/local/bin** trên *nix. Và nên đổi tên file lại thành **fptun** cho dễ thao tác nhé. 

> Tip: Đối với OSX/Linux thì sau khi tải về cần `chmod +x fptun` mới có thể sử dụng được.

![Screenshot](/images/fptun.png)

Trước tiên bạn cần xác định địa chỉ của con fsoft proxy. Mình không biết ở các tòa nhà khác nó như nào còn ở FPT Duy Tân nó sẽ là `fsoft-proxy:8080`.
Biết được địa chỉ của proxy rồi bạn chỉ cần chạy tool của mình:

```sh
fptun -port 6969 -proxy fsoft-proxy:8080
```

Nhớ thay các tham số trên với các giá trị tương ứng của bạn nhé.

Vậy là xong, giờ bạn chỉ cần đổi proxy ở các apps của bạn về `http://localhost:6969` và tận hưởng thôi 🙌.

### Một vài lưu ý

> With great power, comes great responsibility!

- Công cụ chỉ mới được test đối với Firewall của FPT Software tại FPT Building Duy Tân.
- Cẩn trọng khi sử dụng công cụ này. Đã có nhiều incidents để các bạn học tập.
