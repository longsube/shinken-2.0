# Tại Shinken host:

Cài đặt các gói cần thiết:

`sudo apt-get install postfix mailutils libsasl2-2 ca-certificates libsasl2-modules -y`

Sau đó postfix sẽ yêu cầu khai báo một số thông số, ta chọn Internet Site và điền một domain như mail.shinken.vdc

Sửa file cấu hình postfix:

`vi /etc/postfix/main.cf`
	
Thêm/sửa các dòng như sau:

    relayhost = [smtp.gmail.com]:587
    smtp_sasl_auth_enable = yes
    smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
    smtp_sasl_security_options = noanonymous
    smtp_tls_CAfile = /etc/postfix/cacert.pem
    smtp_use_tls = yes
    inet_interfaces = all
    inet_protocols = ipv4

Sau đó thêm vào dòng sau:

    [smtp.gmail.com]:587    USERNAME@gmail.com:PASSWORD

Với USERNAME là username của tài khoản gmail dùng để gửi mail, PASSWORD là mật khẩu của tài khoản đó.

```sh
chmod 400 /etc/postfix/sasl_passwd
postmap /etc/postfix/sasl_passwd
cat /etc/ssl/certs/Thawte_Premium_Server_CA.pem | sudo tee -a /etc/postfix/cacert.pem
/etc/init.d/postfix reload
```

Để kiểm tra xem các bước cài đặt đã đúng chưa ta có thể sử dụng lệnh sau để gửi thử một email, đăng nhập vào trình duyện kiểm tra mục thư đã gửi để xem thư đã được gửi đi chưa.

```sh
/etc/init.d/postfix reload
echo "Test email" | mail -s "Shinken" ducnc92@hotmail.com`
```

Để kiểm tra và sửa lỗi ta có thể sử dụng lệnh `mailq` để xem trạng thái của các mail trong hàng đợi, hoặc là xem log của postfix để sửa lỗi.

`tail –f /var/log/mail.log`
	
Một lỗi có thể gặp phải đó là tài khoản gmail được khai báo bên trên có thể sử dụng cơ chế đăng nhập hai nhân tố hoặc mức cao nên có thể postfix không gửi mail được mặc dù cấu hình đúng. Để khắc phục điều này ta có thể tắt các chức năng này đi hoặc tạo một tài khoản mới mà không sử dụng các chức năng này.

Tham khảo tại:
https://accounts.google.com/DisplayUnlockCaptcha
https://support.google.com/accounts/answer/6010255

