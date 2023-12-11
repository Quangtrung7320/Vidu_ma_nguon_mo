import cv2
import numpy as np
import tkinter as tk
from tkinter import filedialog

def choose_image():
    # Mở hộp thoại để chọn ảnh từ thư mục
    file_path = filedialog.askopenfilename()

    # Kiểm tra xem người dùng đã chọn ảnh hay chưa
    if file_path:
        # Đọc ảnh từ đường dẫn đã chọn
        image = cv2.imread(file_path, cv2.IMREAD_GRAYSCALE)

        # Kiểm tra xem ảnh có tồn tại không
        if image is None:
            print("Không thể đọc ảnh. Hãy chắc chắn rằng đường dẫn đến ảnh là đúng.")
            return

        # Áp dụng GaussianBlur để giảm nhiễu
        blurred = cv2.GaussianBlur(image, (5, 5), 0)

        # Phát hiện biên bằng phương pháp Canny
        edges = cv2.Canny(blurred, 50, 150)

        # Hiển thị ảnh gốc và ảnh đã tách biên
        cv2.imshow('Original Image', image)
        cv2.imshow('Edge Image', edges)
        # Chờ người dùng nhấn phím bất kỳ để đóng cửa sổ
        cv2.waitKey(0)
        cv2.destroyAllWindows()

# Tạo cửa sổ đầu tiên
root = tk.Tk()

# Tạo nút để chọn ảnh
button = tk.Button(root, text="Chọn ảnh", command=choose_image)
button.pack(pady=20)

# Chạy vòng lặp sự kiện của cửa sổ
root.mainloop()
