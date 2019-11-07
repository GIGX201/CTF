Chào các bạn mình là GIGX thuộc team H2DP

Mình không quen viết Write up lắm với văn vở hơi sida  nên mọi người xem xin đừng ném đá :))

# Vào vấn đề:

Khi xem qua function kiểm tra file type trong function fileinfo mình thấy 1 đoạn if đáng ngờ chứa string "SVATTT file" và kèm theo sau nó một function khác mà ở đây mình đã rename thành MESS_F

![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/1.png)

Từ đống if trên mình lại nhận ra 5 kí tự đầu cần nhét vào file input là SVATTT.

Tiến hành phân tích hàm MESS_F mình thấy  input sau khi đc mã hóa ở function sub_401977 sẽ được so sánh qua hàm memcmp với size là 34h. Từ đó mình đoán được input có độ dài là 52 kí tự.

![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/2.png)

Mình tiến hành sửa input thành 52 kí tự và bắt đầu bằng SVATTT sau đó quay lại phân tích sub_401977 mình nhận ra rằng input được xor tại function sub_4017C0 chứa bên trong nó:

![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/3.png)

Tiếp tục phân tích hàm này + việc trace và watch mình thu đc key stream:	

![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/4.png)
![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/5.png)

89 9d 70 2b d5 cc 00 50 05 a1 77 9d df 7d 0b 2b 66 90 3e 9e 23 09 d0 b4 d5 80 cb 7a 06 1b 5e ec 93 be 68 fc ee 43 61 66 85 d9 70 F3 A1 57 B8 2E B9 68 49 CB

Quay lại chỗ memcmp tiến hành xor với hex string đc cho trước: 

![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/6.png)

DA CB 31 7F 81 98 20 38 6C CF 03 A7 FF 04 64 5E 46 FD 5F E7 03 7E B1 DA BB E1 EB 0E 67 70 3B CC F2 9E 04 93 81 28 41 07 F1 F9 07 9A CF 36 DE 42 97 0C 25 A7

Sau khi xor thì được  1 cái hint :
SVATTT hint: you may wanna take a look at winafl.dll
Qua việc monitor mình phát hiện winalf-fuzz sẽ call drrun.exe và drrun.exe sẽ call winalf.dll
Phân tích winalf.dll:

![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/7.png)

đoạn này sẽ wrap function sub_91E40 vào memcmp,  khi fileinfo.exe call memcmp thì sub_91E40 sẽ được thực thi trước, và nếu đúng thì fileinfo sẽ call abort() => nguyên nhân crash

![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/8.png)

check function sub_91E40, function này sẽ thay đổi đoạn hex string chứa hint lúc đầu thành đoạn hex string chứa flag:

![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/9.png)

![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/10.png)

 hex string chứa flag:
DA CB 31 7F 81 98 7B 38 71 D5 07 A7 F0 52 6F 52 08 F1 53 F1 51 60 BF 9A BA F2 AC 55 62 74 3D 9F BC D9 1A 93 9B 33 3E 39 E1 AB 07 81 C0 27 96 46 CD 05 25 B6
xor với keystream thì được: 
`SVATTT{http://dynamorio.org/docs/group__drwrap.html}`
kiểm tra lại với input như trên:

![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/11.png)

![](https://raw.githubusercontent.com/GIGX201/CTF/master/SVATTT2019/12.png)

Như vậy flag ở đây là: 
# `SVATTT{http://dynamorio.org/docs/group__drwrap.html}`

