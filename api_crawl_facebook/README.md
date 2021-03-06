<h1>Crawl facebook</h1>

### Chuẩn bị

1. Cài đặt thư viện ở requirements.txt
```
    pip3 / pip install -r requirements.txt
```

2. Bật docker

3. Chạy lệnh để run docker để crawl bằng splash
```
docker pull scrapinghub/splash
```

```
docker run -p 8050:8050 scrapinghub/splash
```
4. Tải chromedriver.exe để crawl hình ảnh bằng selenium


### Chạy server
```
python app.py
```

### API
1. Login: http://127.0.0.1:5000/login

Đăng nhập vào facebook để lấy cookie, những lần sau crawl không cần đăng nhập lại

Input: Json
```
{
    "acc": "abcd@gmail.com",
    "pass":"12345"
}
```
Output: File cookie.json trong thư mục cookies

2. Bio: http://127.0.0.1:5000/bio

Crawl bio trên facebook

Input: Json
```
{
    "link" : "https://m.facebook.com/abcd"
}

```

3. Check-in: http://127.0.0.1:5000/checkin

Crawl những nơi đã check-in

Input: Json
```
{
    "link" : "https://m.facebook.com/abcd"
}
```

4. Page like: http://127.0.0.1:5000/page

Crawl những page đã like

Input: Json
```
{
    "link" : "https://m.facebook.com/abcd"
}
```

5. Profile: http://127.0.0.1:5000/profile

Crawl thông tin cá nhân

Input: Json
```
{
    "link" : "https://m.facebook.com/abcd"
}
```
6. Timeline: http://127.0.0.1:5000/timeline

Crawl các bài post trên trang cá nhân

Input: Json
```
{
    "link" : "https://m.facebook.com/abcd"
}
```
7. Group: http://127.0.0.1:5000/group

Crawl những bài post trong các group của user

Input: Json
```
{
    "link" : "https://m.facebook.com/abcd",
    "link_groups": [
        "https://m.facebook.com/1",
        "https://m.facebook.com/2",
        "https://m.facebook.com/3"
    ]
}
```
8. Image: http://127.0.0.1:5000/img

Crawl ảnh cá nhân của user

Input: Json
```
{
    "link" : "https://m.facebook.com/abcd"
}
```
Output: 
- Dữ liệu text được đưa lên mongoDB
- Đối với dữ liệu ảnh: đưa link lên mongoDB đồng thời tải ảnh về thư mục **"user/id/img"** ở thư mục gốc

![](./readme_image/1.PNG)
![](./readme_image/2.PNG)
## Chi tiết
- **crawl_img_fb.py** : file crawl ảnh bằng **selenium**

- **get_id.py** : lấy id từ link facebook người dùng bằng **selenium**
- **acc.json** : sau khi chạy API login thì lưu tài khoản, mật khẩu vào file này
- **id_user.txt** : id của facebook cần crawl
- **id_group.txt** : id của các group cần crawl bài post của user trên đó
- các file crawl đặc trưng khác nằm ở thư mục **"crawl_facebook/spiders"**
bằng thư viện **scrapy/splash**