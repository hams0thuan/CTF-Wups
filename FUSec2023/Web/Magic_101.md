![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/bf203b6a-81e5-4b33-a70d-fbb7b26f4313)## Macgic 101 

### Link: https://magic.fptunisecathon2023.tech/

### Initial reconnaissance:

- Vào website ta thấy có 3 chức năng `Home`, `Try`, `Donation` nhưng chỉ duy nhất chức năng `Try` có thể khai thác được điều gì đó 😒

![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/cf3c8591-0bf7-4b7c-9257-d811ca3165ae)


- Khi thấy parameter `content` cho phép sử dụng các loại thẻ 


![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/4cd9b658-dd61-4a4e-8951-c6edab0defc4)


mình đã nghĩ ngay tới XSS và mình thử với một payload đơn giản `<script>alert('XSS')</script>` và kết quả là thành công. 


![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/b97d6814-e166-497b-948b-25095c2a563c)


- Mình thử tiếp với payload `<script>alert(document.cookie)</script>`

![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/93836335-ac33-4492-aac9-1943c5e3febc)

và mình nhận được thông báo thấy không khả nghi lắm, lúc này mới ngỡ ra thử thách này vốn dĩ đã dính XSS reflect ngay từ đầu nhưng do incident nào đó mà ở website Firefox không bị mà ở trên browser Chromium của Burp Suite lại hiển thị mình đã biết bị sai hướng.

![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/0865b818-9515-4d03-86f3-44f86e7aa669)

- Mình đã phải mua hint và nhận được là `SSTI Python Flask` nhưng khi mình inject payload `{{{7*7}}}` thì website thông báo khá cay cú

![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/ebcb4bed-3f80-45cd-a836-c92688d40c9a)

- Điều trên chính tỏ server đã filter một cặp `{}` và chỉ hiển thị nội dung bên trong đó. Thậm chí response cũng không hiển thị thêm bất kỳ thông tin gì, với một bài blackbox như vậy khá là guessing 😨.

![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/dcb3a3a7-f92a-438c-9a34-dfe17c98e5a1)

- Một lúc sau thì author và ban ra đề đã cập nhật lại hint là một lỗ hổng khác `python flask format string` khiến mình thật sự trầm cảm và tức giận khi bị úm alaba trap =))

 ![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/f892d69c-3e4d-41ef-bcb7-7dd3c93f8507)

- Khi nhận được hint này mình đã thử một một payload bất chợt nghĩ ra `{self}` vì biểu thức này rất hay được sử dụng trong cái payload template engine của Python Flask và mình nhận được kết quả khá bất ngờ.

![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/5a12cb04-b5ad-4bf0-8f0a-a0e68f1a2d04)

- Sau đó mình có hỏi anh author và nhận được hint như bên dưới và mình suy ra dần payload đầu tiên giúp mình hiểu được hơn về thử thách này `{self.__init__.__globals__}`

![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/23d7f6f9-93ab-443e-9e4f-fe0890585276)

![image](https://github.com/hams0thuan/CTF-Wups/assets/93731698/7421201d-cf80-4506-bc5b-d294a900a746)

- Tới đây mình dựa theo hint của author và search thêm một document hack tricks có liên quan tới lỗ hổng này [Bypass Python sandboxes](https://book.hacktricks.xyz/generic-methodologies-and-resources/python/bypass-python-sandboxes#dissecting-python-objects).

- Anh author có đưa mình một hint cực mạnh để giúp mình có thể liên kết các biểu thức lại với nhau 
