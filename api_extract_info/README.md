<h1>Extract info</h1>

### Chuẩn bị

1. Cài đặt
```
    pip3 / pip install -r requirements.txt
```

2. Chạy lệnh để clone model phát hiện khuôn mặt

```
git clone https://github.com/timesler/facenet-pytorch.git facenet_pytorch
```

3. Download checkpoint của model **detect_object** từ link và để trong thư mục hiện tại: https://s3.console.aws.amazon.com/s3/buckets/rever-ai?region=ap-southeast-1&prefix=hcmut/models/


### Chạy server
```
python app.py
```

### API
Input: Json
```
{
    "link" : "https://m.facebook.com/abcd"
}
```
1. Tìm mặt chính chủ: http://127.0.0.1:5000/face

Đầu tiên dùng model **MTCNN** ở **facenet_pytorch** để cắt các khuôn mặt có trong ảnh của user trong thư mục ***user/id/img*** ở ngoài thư mục gốc và resize về 96x96, các khuôn mặt này được chứa trong thư mục ***data_process***, sau khi API xử lí xong, các ảnh trong thư mục ***data_process*** sẽ bị xóa. Tiếp theo sẽ dùng model **Siamese** ở **face_reg** để duyệt qua tất cả khuôn mặt, những ảnh cùng 1 khuôn mặt sẽ được đánh cùng 1 nhãn. Nhãn nào được xuất hiện nhiều nhất thì những khuôn mặt của gương mặt đó là chính chủ. 

**Output**: Kết quả ở **user/id/main_face.json** chứa số thứ tự các ảnh có chứa gương mặt chính chủ.
```
{
    "main_face": ["39", "0", "30", "42", "40", "23", "24", "46", "14", "45", "18", "43", "19", "1", "36", "22", "13", "25", "6", "41", "47"],
     "time": 96.86205339431763
}
```

2. Trích xuất thông tin gia đình: http://127.0.0.1:5000/family

Tiêu chí đầu tiên là xét thuộc tính **Mối quan hệ** trong collection **profile** đã được crawl, nếu họ không cập nhật thông tin này hoặc không public thì sẽ xét tiếp là hình ảnh **avatar** của họ có chứa nhiều người hay không, nếu chứa trên 3 người có thể kết luận người đó có gia đình, nếu không thì sẽ kết luận chưa có thông tin về gia đình.

**Output**: Kết quả ở collection **"extract_family"** trong mongoDB
```
{
    "family": "Yes",
    "time": 2.8518447875976562
}
```

3. Trích xuất thông tin phương tiện: http://127.0.0.1:5000/vehicle

Duyệt qua tất cả ảnh của người dùng, trả về list của các ảnh chứa ô tô và mô tô. Nếu không có ảnh nào có ô tô thì sẽ kết luận không có thông tin, nếu có ảnh ô tô, kiểm tra ảnh này có nằm trong danh sách các ảnh chứa khuôn mặt chính chủ ở trong file **main_face.json**, nếu có thì kết luận ảnh chứa ô tô cùng chính chủ, nếu không thì kết luận ảnh chứa ô tô nhưng không có chính chủ. Tương tự đối với mô tô.

**Output**: Kết quả ở collection **"extract_vehicle"** trong mongoDB
```
{
    "car": "Car without main face",
    "moto": "Moto without main face",
    "time": 11.403420686721802
}
```
## Chi tiết
- **data_process**: thư mục chứa ảnh khuôn mặt được cắt ra từ ảnh của user khi chạy API tìm gương mặt chính chủ
- **detect_object**: model detect ô tô và mô tô
- **face_reg**: model siamese để tính sự tương đồng giữa 2 khuôn mặt
- **facenet_pytorch**: model phát hiện khuôn mặt

