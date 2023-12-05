import tkinter as tk
from tkinter import filedialog
from PIL import Image, ImageTk
import numpy as np
import cv2
def apply_thresholds(image_path, threshold_type):
    # Đọc ảnh từ đường dẫn
    img = cv2.imread(image_path)


    # Định nghĩa các bộ lọc
    kernel_3x3 = np.ones((3, 3), np.float32) / 9
    kernel_5x5 = np.ones((5, 5), np.float32) / 25
    kernel_identity = np.array([[0, 0, 0], [0, 1, 0], [0, 0, 0]])
    kernel_smooth = np.array([[0, 0, 0], [0, 1, 0], [0, 0, 0]])

    # Áp dụng ngưỡng tương ứng
    if threshold_type == "identity":
        filtered_image = cv2.filter2D(img, -1, kernel_identity)
    elif threshold_type == "smooth":
        filtered_image = cv2.filter2D(img, -1, kernel_smooth)
    elif threshold_type == "3x3":
        filtered_image = cv2.filter2D(img, -1, kernel_3x3)
    elif threshold_type == "5x5":
        filtered_image = cv2.filter2D(img, -1, kernel_5x5)
    else:
        return

    # Chuyển đổi ảnh từ OpenCV sang định dạng hỗ trợ bởi Pillow
    filtered_image = cv2.cvtColor(filtered_image, cv2.COLOR_BGR2RGB)
    filtered_image = Image.fromarray(filtered_image)

    # Hiển thị ảnh đã xử lý trong cửa sổ mới
    filtered_window = tk.Toplevel()
    filtered_window.title("Filtered Image")
    filtered_window.geometry("400x400")
    filtered_canvas = tk.Canvas(filtered_window, width=400, height=400)
    filtered_canvas.pack()
    filtered_photo = ImageTk.PhotoImage(filtered_image)
    filtered_canvas.create_image(0, 0, anchor=tk.NW, image=filtered_photo)
    filtered_window.mainloop()
def apply_smooth_filter(image_path, output_path, kernel_size=5):
    # Đọc ảnh từ đường dẫn
    image = cv2.imread(image_path)

    # Kiểm tra xem ảnh có được đọc thành công không
    if image is None:
        print("Không thể đọc ảnh.")
        return

    # Áp dụng bộ lọc làm mịn (Smooth filter) bằng hàm cv2.GaussianBlur
    smooth_image = cv2.GaussianBlur(image, (kernel_size, kernel_size), 0)

    # Lưu ảnh sau khi áp dụng bộ lọc làm mịn
    cv2.imwrite(output_path, smooth_image)

    # Hiển thị ảnh gốc và ảnh sau khi áp dụng bộ lọc (chỉ để tham khảo)
    cv2.imshow("Original Image", image)
    cv2.imshow("Smoothed Image", smooth_image)
    cv2.waitKey(0)
    cv2.destroyAllWindows()
def select_image():
    # Mở hộp thoại chọn tệp
    file_path = filedialog.askopenfilename(initialdir="/", title="Select Image", filetypes=(("Image files", "*.jpg *.jpeg *.png"), ("All files", "*.*")))
    if file_path:
        # Hiển thị đường dẫn tệp trên giao diện
        selected_image_label.config(text=file_path)

def apply_identity_threshold():
    image_path = selected_image_label.cget("text")
    apply_thresholds(image_path, "identity")

def apply_3x3_threshold():
    image_path = selected_image_label.cget("text")
    apply_thresholds(image_path, "3x3")

def apply_5x5_threshold():
    image_path = selected_image_label.cget("text")
    apply_thresholds(image_path, "5x5")

# Tạo cửa sổ giao diện
window = tk.Tk()
window.title("Image Thresholding")
window.geometry("400x200")

# Tạo nút chọn ảnh
select_image_button = tk.Button(window, text="Select Image", command=select_image)
select_image_button.pack(pady=10)

# Hiển thị đường dẫn tệp đã chọn
selected_image_label = tk.Label(window, text="")
selected_image_label.pack()
## Tạo nút min anh mooth
smooth_button = tk.Button(window, text="smooth", command=apply_smooth_filter)
smooth_button.pack(pady=5)
# Tạo nút áp dụng ngưỡng identity
identity_button = tk.Button(window, text="Identity", command=apply_identity_threshold)
identity_button.pack(pady=5)

# Tạo nút áp dụng ngưỡng 3x3
_3x3_button = tk.Button(window, text="3x3", command=apply_3x3_threshold)
_3x3_button.pack(pady=5)

# Tạo nút áp dụng ngưỡng 5x5
_5x5_button = tk.Button(window, text="5x5", command=apply_5x5_threshold)
_5x5_button.pack(pady=5)
# Chạy vòng lặp giao diện
window.mainloop()
