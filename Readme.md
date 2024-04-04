# Các chức năng của chương trình

1. Xóa các folder đệm (kết quả lần trước, thư mục nháp xử lý file trung gian)
2. Đọc file data mới
    - Nếu có lock.txt thì bỏ qua, nếu không có thì đọc danh sách files
    - Nếu file đã tồn tại trong local thì bỏ qua, nếu không thì tải file
    - Đọc các file mới nhất trong n ngày (`n=days_of_data_newest_to_train`)
3. Đọc file data csv:
    - Đọc file csv
    - Tìm cột `pressure` hoặc `pressure_1s`, nếu là `pressure_1s` thì đổi tên thành `pressure`
    - Tìm Max, Min của `pressure`
    - Nhóm data lại theo các tiêu chí `data_selected__groupby + ['min', 'max']`
    - Lưu các nhóm lại thành file csv vào thư mục trung gian `local_log_data_dir_input_processed`
    -
4. Train các mô hình:
    - Đọc từng file trong thư mục trung gian ra để train độc lập
    - Tạo dữ liệu ràng buộc với công thức hàm PINN
        - `y = ax + b`: y=PI * (R)^2 * G * n + b
            - R: bán kính của mặt tiếp xúc
            - G: gia tốc trọng trường
            - n: Number of cylinder * delta (hệ số điều chỉnh góc nghiêng thực tế của đồ thị)
        - `calculator_add_percent_data`: số % dữ liệu được thêm vào so với tổng số dữ liệu đã có
        - Tìm b tự động theo dữ liệu training.
    - Thêm dữ liệu PINN vào dữ liệu training
    - Train
5. Save Data
    - Filter testing data:
        - Giữ lại dữ liệu duy nhất (không lặp lại) và
        - Giữ lại dữ liệu trải đều từ đầu đến cuối của dải dữ liệu
        - Giữ lại tối đa 2000 mẫu dữ liệu mỗi loại
    - Predict
    - Lưu log với `Mean Squared Error`
    - Predict Min, Max pressure
    - Lưu file kết quả vào csv vào `local_dir_Predicted`
    - Lưu ảnh kết quả vào `local_dir_Predicted`
6. Copy kết quả sang `remote_remote_directory_output`
7. Update:
    - V3.0: thêm exception error handling nếu không download được dữ liệu (không kết nối được, k có file, không download được), chương trình vẫn chạy bình thường
    - V3.0: Thêm error exception phần copy dữ liệu kết quả, nếu không kết nối được, không upload đươợc file,... cũng không sao
    - V4.0: Thêm data liên quan đến 6sigma (-3, -2, -1, 0, 1, 2, 3 sigma)
    - V5.0: Thêm hàm lọc dữ liệu vào data training. Loại bỏ dữ liệu lỗi.
8. 