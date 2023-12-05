import numpy as np
import matplotlib.pyplot as plt
from tkinter import Tk, Label, Entry, Button

def process_signal():
    try:
        f = float(freq_entry.get())  # Lấy tần số từ người dùng
        duration = float(duration_entry.get())  # Lấy thời gian từ người dùng

        # Tạo tín hiệu đầu vào
        t = np.linspace(0, duration, 1000)
        x = np.sin(2 * np.pi * f * t)

        # Biến đổi Fourier
        X = np.fft.fft(x)
        freq = np.fft.fftfreq(len(t), t[1]-t[0])

        # Tạo giao diện hiển thị
        fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(8, 6))
        fig.suptitle('Xử lý tín hiệu số')

        # Biểu đồ tín hiệu
        ax1.plot(t, x)
        ax1.set_title('Tín hiệu sin(2*pi*{:.2f}*t)'.format(f))
        ax1.set_xlabel('Thời gian (s)')
        ax1.set_ylabel('Amplitude')
        ax1.grid(True)

        # Biểu đồ biến đổi Fourier
        ax2.plot(freq, np.abs(X))
        ax2.set_title('Biến đổi Fourier của tín hiệu')
        ax2.set_xlabel('Tần số (Hz)')
        ax2.set_ylabel('Amplitude')
        ax2.grid(True)

        # Hiển thị giao diện
        plt.tight_layout()
        plt.show()

    except ValueError:
        # Xử lý lỗi nếu người dùng nhập sai định dạng
        error_label.config(text="Vui lòng nhập số hợp lệ")

# Tạo cửa sổ giao diện
window = Tk()
window.title("Xử lý tín hiệu số")
window.geometry("300x200")

# Tần số
freq_label = Label(window, text="Tần số (Hz):")
freq_label.pack()
freq_entry = Entry(window)
freq_entry.pack()

# Thời gian
duration_label = Label(window, text="Thời gian (s):")
duration_label.pack()
duration_entry = Entry(window)
duration_entry.pack()

# Nút xử lý tín hiệu
process_button = Button(window, text="Xử lý tín hiệu", command=process_signal)
process_button.pack()

# Label lỗi
error_label = Label(window, text="")
error_label.pack()

# Khởi chạy giao diện người dùng
window.mainloop()
