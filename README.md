# VisionAI Pro

**Trợ lý thị giác AI hỗ trợ người khiếm thị**, xây dựng bằng Python và Streamlit. Ứng dụng dùng Google Gemini để phân tích ảnh, Tesseract để OCR cục bộ, đồng thời hỗ trợ hỏi đáp, giọng nói và giao diện trợ năng.

## Mục lục

- [Tổng quan](#tổng-quan)
- [Tính năng](#tính-năng)
- [Kiến trúc](#kiến-trúc)
- [Cấu trúc dự án](#cấu-trúc-dự-án)
- [Công nghệ](#công-nghệ)
- [Yêu cầu](#yêu-cầu)
- [Cài đặt và chạy](#cài-đặt-và-chạy)
- [Cấu hình](#cấu-hình)
- [Kiểm thử](#kiểm-thử)
- [Giới hạn và an toàn](#giới-hạn-và-an-toàn)
- [Bảo mật](#bảo-mật)

## Tổng quan

VisionAI Pro cho phép người dùng tải ảnh lên hoặc chủ động chụp ảnh để nhận mô tả bằng tiếng Việt. Người dùng cũng có thể hỏi đáp theo ảnh, tìm vật thể, nghe kết quả hoặc dùng OCR cục bộ để đọc chữ. Camera không phát trực tiếp; ảnh chỉ được chụp sau khi người dùng cấp quyền và bấm chụp.

## Tính năng

| Nhóm | Mô tả |
|---|---|
| Phân tích ảnh | Mô tả bối cảnh, vật thể, chữ, tiền Việt, nguy cơ và gợi ý hỗ trợ di chuyển. |
| Hỏi đáp theo ảnh | Đặt câu hỏi dựa trên ảnh hiện tại; có thể cung cấp ngữ cảnh hội thoại trước đó. |
| Tìm vật thể | Yêu cầu rà ảnh và liệt kê riêng từng vật với vị trí, dấu hiệu quan sát được và độ tin cậy nếu xác định được. |
| OCR cục bộ | Dùng Tesseract để nhận dạng tiếng Việt và tiếng Anh trên máy. |
| Giọng nói | Ghi âm rồi chuyển thành văn bản, gửi câu hỏi cho Gemini và có thể đọc kết quả bằng Edge TTS hoặc Windows TTS. |
| Trợ năng | Chế độ chữ/nút lớn, focus bàn phím rõ, kết quả có thể đọc thành tiếng và giao diện robot Vision. |
| Lịch sử | Lưu lịch sử phân tích cục bộ, xem lại và xuất báo cáo. |

Phản hồi JSON của Gemini được ứng dụng xử lý nội bộ và chuyển thành văn bản/danh sách trước khi hiển thị. Nếu phản hồi không đúng định dạng JSON, ứng dụng có thể giữ nguyên nội dung phản hồi.

## Kiến trúc

```text
Người dùng
   │
   ▼
Giao diện Streamlit (app.py)
   ├── Phân tích ảnh / hỏi đáp ──► Google Gemini API
   ├── Đọc chữ cục bộ ───────────► Tesseract OCR
   ├── Ghi âm ───────────────────► SpeechRecognition / Google Web Speech
   └── Đọc kết quả ──────────────► Edge TTS / Windows TTS
   │
   └── Lịch sử cục bộ ───────────► data/history.json
```

Ứng dụng thử các model Gemini theo thứ tự cấu hình. Khi gặp giới hạn 429, ứng dụng chuyển sang model kế tiếp; lỗi máy chủ 503 được thử lại một lần trước khi chuyển tiếp.

## Cấu trúc dự án

```text
tro-ly-thi-giac/
├── app.py                     # Giao diện và luồng ứng dụng Streamlit
├── modules/
│   ├── ai_vision.py            # Phân tích ảnh, hỏi đáp và dự phòng Gemini
│   ├── accessibility.py       # Định dạng kết quả cho trợ năng
│   ├── local_ocr.py            # OCR cục bộ bằng Tesseract
│   ├── robot_ui.py             # Giao diện robot Vision
│   ├── speech.py               # Nhận dạng và tổng hợp giọng nói
│   ├── utils.py                # Tiện ích dùng chung
│   └── voice_commands.py       # Xử lý lệnh thoại
├── ui/vision_robot/            # Tài nguyên giao diện robot
├── assets/                     # Hình ảnh và tài nguyên giao diện
├── tests/test_core.py          # Kiểm thử cục bộ
├── .streamlit/config.toml      # Cấu hình Streamlit
├── .env.example                # Mẫu biến môi trường
├── requirements.txt            # Thư viện Python
├── check_env.py                # Kiểm tra cấu hình môi trường
├── setup_ocr.ps1               # Hỗ trợ cấu hình Tesseract
└── CHAY_VISIONAI.bat           # Khởi chạy trên Windows
```

## Công nghệ

| Thành phần | Công nghệ |
|---|---|
| Giao diện và ứng dụng | Python, Streamlit |
| Phân tích ảnh và hỏi đáp | Google Gemini API (`google-genai`) |
| Xử lý ảnh | Pillow |
| OCR | Tesseract OCR, `pytesseract` |
| Nhận dạng giọng nói | SpeechRecognition / Google Web Speech |
| Đọc văn bản | Edge TTS, Windows TTS (`pyttsx3`) |

## Yêu cầu

- Windows 10/11.
- Python 3.11 hoặc 3.12.
- API key Google Gemini để dùng phân tích ảnh và hỏi đáp trực tuyến.
- Internet cho Gemini, nhận dạng giọng nói qua Google Web Speech và Edge TTS.
- Tesseract OCR cùng dữ liệu ngôn ngữ `vie` và `eng` nếu cần OCR cục bộ.

## Cài đặt và chạy

Mở PowerShell tại thư mục chứa `app.py`, sau đó chạy:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
Copy-Item .env.example .env
```

Mở `.env`, điền API key vào `GEMINI_API_KEY`, rồi khởi chạy:

```powershell
python -m streamlit run app.py
```

Hoặc dùng `CHAY_VISIONAI.bat` sau khi đã cấu hình môi trường.

## Cấu hình

| Biến | Bắt buộc | Ý nghĩa |
|---|---:|---|
| `GEMINI_API_KEY` | Có, nếu dùng Gemini | API key cá nhân từ [Google AI Studio](https://aistudio.google.com/apikey). |
| `GEMINI_MODELS` | Không | Danh sách model Gemini ưu tiên, phân tách bằng dấu phẩy. |
| `TESSERACT_CMD` | Không | Đường dẫn đến `tesseract.exe` nếu không dùng vị trí cài mặc định. |
| `EDGE_TTS_VOICE` | Không | Giọng đọc Edge TTS; mặc định trong `.env.example`. |
| `EDGE_TTS_RATE`, `EDGE_TTS_PITCH` | Không | Điều chỉnh tốc độ và cao độ đọc. |
| `STT_LANGUAGE` | Không | Ngôn ngữ nhận dạng giọng nói; mặc định là `vi-VN`. |

Danh sách model mặc định trong `.env.example` được thử theo thứ tự. Có thể tùy chỉnh `GEMINI_MODELS`; ứng dụng chỉ chấp nhận mã model Gemini. Dự phòng có thể giúp khi giới hạn áp dụng riêng từng model, nhưng không vượt được quota dùng chung, API key sai hoặc lỗi mạng. Có thể thử kết nối tại **Cài đặt → Kiểm tra kết nối Gemini**.

### Cài OCR tiếng Việt

1. Cài Tesseract OCR cho Windows và chọn dữ liệu ngôn ngữ Vietnamese (`vie`) cùng English (`eng`).
2. Nếu Tesseract không nằm ở vị trí mặc định, điền đường dẫn `tesseract.exe` vào `TESSERACT_CMD` trong `.env`.
3. OCR chạy cục bộ, không cần Gemini API key.

## Kiểm thử

Sau khi cài dependencies, chạy trong thư mục dự án:

```powershell
.\.venv\Scripts\python.exe -m unittest discover -s tests -v
```

Các bài kiểm thử không cần API key và không thay thế kiểm thử kết nối Gemini thật.

## Giới hạn và an toàn

- Phân tích tìm vật thể dùng một lượt gọi Gemini; mô hình có thể bỏ sót vật nhỏ, vật bị che khuất hoặc vật chồng lấp. Kết quả không bảo đảm nhận diện đủ số lượng và cần được đối chiếu với ảnh.
- Hỏi đáp và phân tích ảnh gửi ảnh đã chọn đến Gemini API. OCR cục bộ chạy trên máy.
- Luồng giọng nói là ghi âm → chuyển thành văn bản → Gemini trả lời → đọc kết quả, không phải hội thoại âm thanh thời gian thực.
- Gợi ý di chuyển dựa trên ảnh tĩnh, không đo khoảng cách chính xác, không theo dõi vật đang di chuyển và không xác nhận lối đi an toàn.
- Kết quả AI/OCR chỉ mang tính hỗ trợ; không thay thế thiết bị hỗ trợ hoặc đánh giá chuyên môn.

## Bảo mật

- Không đưa `.env`, API key hoặc thông tin nhạy cảm lên GitHub. Chỉ đưa `.env.example` không chứa khóa bí mật lên repository.
- Tạo `.env` từ `.env.example` trên từng máy và nhập API key riêng.
- Lịch sử phân tích được lưu cục bộ tại `data/history.json`; không dùng để lưu thông tin nhạy cảm.
