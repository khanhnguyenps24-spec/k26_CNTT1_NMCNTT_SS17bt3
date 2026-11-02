# PHẦN 1. BÁO CÁO PHÂN TÍCH (I/O)

## 1. Phân tích bằng Inspect Element và Computed
Sử dụng F12 - Elements, chọn lần lượt hai phần tử "Nội dung bài học”và “Danh sách video”, sau đó mở tab Computed để kiểm tra Box Model.
Mỗi phần tử được phân tích theo 4 lớp:
**Content - Padding - Border - Margin**
### Phần tử 1: “Nội dung bài học”
Cần ghi nhận trong Computed:
**Nhận xét:** Nếu phần tử có padding lớn nhưng chỉ quan sát khoảng cách bên ngoài thì dễ nhầm padding là margin. Padding làm tăng vùng bên trong của phần tử, còn margin tạo khoảng cách bên ngoài phần tử.
### Phần tử 2: “Danh sách video”
Thành phần
Giá trị thực tế cần kiểm tra
Content width
… px
## 2. Nguyên nhân chồng lấn
Hiện tượng hai khung bị đè lên nhau có thể xuất phát từ việc tổng kích thước thực tế của phần tử lớn hơn vùng bố trí mà layout dự kiến.
Có thể biểu diễn:

```
Kích thước thực tế
= Content
+ Padding
+ Border
+ Margin
```

Đặc biệt cần kiểm tra:

-   height hoặc width cố định.
    
-   padding quá lớn.
    
-   margin âm, ví dụ margin-top: -...px.
    
-   position: absolute hoặc position: relative kết hợp với top/left.
    
-   box-sizing.
    
-   overflow.
    
-   Các phần tử cha có chiều cao cố định.
    

Nếu `box-sizing: content-box`, giá trị `width`/`height` thường chỉ áp dụng cho **Content**, còn Padding và Border được cộng thêm vào kích thước bên ngoài.

Ví dụ:

```
.box {
    width: 500px;
    padding: 20px;
    border: 1px solid;
}
```

Chiều rộng thực tế sẽ lớn hơn 500px vì Padding và Border được cộng thêm.

## 3. Dự đoán khi thay đổi Margin mà không kiểm tra Padding

Không nên chỉ giảm hoặc tăng `margin` để che hiện tượng chồng lấn.

Ví dụ:

```
.video-list {
    margin-top: 20px;
}
```

Nếu nguyên nhân thật sự là:

```
.video-list {
    padding-top: 40px;
}
```
thì việc thay đổi margin chỉ làm thay đổi vị trí bên ngoài, trong khi kích thước nội bộ vẫn không thay đổi.
Hậu quả có thể là:
1.  Hai khung vẫn bị chồng lấn.
2.  Khoảng cách giữa các thành phần trở nên bất thường.
 3.  Nội dung bên trong bị đẩy xuống hoặc vượt khỏi vùng dự kiến.
   Vì vậy cần xác định **Padding, Border và Margin riêng biệt** trước khi chỉnh CSS.

# PHẦN 2. THIẾT KẾ GIẢI PHÁP

## 1. Nguyên tắc sửa

Ưu tiên sửa theo thứ tự:

```
Kiểm tra Box Model
        |
Xác định phần tử gây tràn/chồng lấn
        |
Kiểm tra width/height
        |
Kiểm tra padding
        |
Kiểm tra margin
        |
Kiểm tra position/overflow
        |
Thử nghiệm trong Styles
```
## 2. Thiết lập Box Model an toàn

Có thể thử:

```
.lesson-content,
.video-list {
    box-sizing: border-box;
}
```

Cách này giúp width và height bao gồm cả **Padding + Border**, từ đó hạn chế tình trạng kích thước thực tế vượt quá kích thước được khai báo.

## 3. Tách hai khung nội dung

Nếu hai khung đang nằm quá sát nhau, có thể điều chỉnh khoảng cách bên ngoài bằng margin:

```
.lesson-content {
    margin-bottom: 16px;
}

.video-list {
    margin-top: 16px;
}
```

Nếu giao diện sử dụng Flexbox hoặc Grid, nên ưu tiên `gap`:

```
.container {
    display: flex;
    flex-direction: column;
    gap: 16px;
}
```

Cách này giúp tạo khoảng cách giữa hai khung mà không cần sử dụng margin âm.

## 4. Điều chỉnh Padding

Nếu Computed cho thấy Padding quá lớn, giảm về mức hợp lý:

```
.lesson-content {
    padding: 16px;
}

.video-list {
    padding: 16px;
}
```
## 5. Kiểm tra Position

Nếu một phần tử có:

```
position: absolute;
```

cần kiểm tra lại vì phần tử absolute được đưa ra khỏi luồng bố trí thông thường và có thể đè lên phần tử khác.

Nếu không thực sự cần định vị tuyệt đối, có thể thử:

```
position: static;
```

hoặc để phần tử trở lại layout bình thường bằng Flexbox/Grid.

## 6. Kết quả mong muốn
Sau khi chỉnh sửa, cấu trúc nên đạt:
Hai khung phải nằm trong luồng layout bình thường, không đè lên nhau; Padding dùng để tạo khoảng cách **bên trong**, Border xác định **ranh giới**, còn Margin/Gap dùng để tạo khoảng cách **giữa các khung**.

