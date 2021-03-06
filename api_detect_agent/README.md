<h1>Detect agent</h1>

### Cài đặt
```
    pip3 / pip install -r requirements.txt
```
### Chạy server
```
python app.py
```

### API
http://127.0.0.1:5000/agent

Input: Json
```
{
    "link" : "https://m.facebook.com/abcd"
}
```

Output: Kết quả ở collection **"agent"** trong mongoDB
```
{
    "feature": "timeline",
    "isAGENT": "Yes",
    "time_detect": 5.071559906005859
}
```

## Chi tiết
Trong thư mục model chứa 2 mô hình phân loại page và post
- **Post**:
     + **text.py**: code phân loại post có liên quan tới bất động sản hay không
     + **data_post**: data để fit vào model
     + **classify_post.sav**, **model_post.pkl**: model sau khi được fit data
- **Page**: phân loại 1 page có liên quan tới bất động sản không, tương tự với post

Các tiêu chí hiện tại để đánh giá 1 user có phải là môi giới hay không sắp xếp theo thứ tự giảm dần từ trên xuống

- Trên timeline có nhiều hơn 5 bài post về bđs
- Trên các group chỉ định có nhiều hơn 5 bài post về bđs
- Nơi làm việc là công ty bđs
- Có nhiều hơn 7 page đã like liên quan về bđs
- Bio có 1 đoạn text có liên quan tới mua bán bđs