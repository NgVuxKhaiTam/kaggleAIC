# 🚀 AIC2025 Kaggle Web Interface

## Tổng quan
Hệ thống web interface đầy đủ cho Kaggle notebook với khả năng tìm kiếm multi-model (BEiT3 + SigLIP).

## Files cần thiết

### 1. Backend Files
- `backend_aic.py` - Định nghĩa models (Cell 1)
- `kaggle_web_backend.py` - Flask web server (Cell 2)

### 2. HTML Interface Files (Upload to /kaggle/working/)
- `kaggle_index.html` - Giao diện tìm kiếm chính
- `kaggle_instructions.html` - Hướng dẫn setup
- `kaggle_error.html` - Trang báo lỗi

## Hướng dẫn setup

### Bước 1: Upload Files
1. Upload 3 file HTML vào `/kaggle/working/` trong Kaggle notebook
2. Copy nội dung `backend_aic.py` vào Cell 1
3. Copy nội dung `kaggle_web_backend.py` vào Cell 2

### Bước 2: Chạy Notebook
```python
# Cell 1: Load models
# Paste backend_aic.py content here

# Cell 2: Start web server  
# Paste kaggle_web_backend.py content here
```

### Bước 3: Truy cập Web Interface
- Sau khi chạy Cell 2, sẽ có Ngrok URL hiển thị
- Click vào URL để truy cập web interface
- Ví dụ: `https://xxxxx-xx-xx-xxx-xxx.ngrok-free.app`

## Tính năng

### 🔍 Tìm kiếm
- **Text Search**: Tìm kiếm bằng mô tả văn bản
- **Image Search**: Tìm kiếm bằng upload ảnh
- **Multi-Model**: BEiT3, SigLIP, hoặc Aggregated

### 🤖 Models
- **BEiT3**: Model gốc với embedding 1024 chiều
- **SigLIP**: Vision-language model với embedding 1536 chiều  
- **Aggregated**: Kết hợp cả 2 models với tùy chọn intersection

### 📊 Features
- Xem ảnh kết quả được serve trực tiếp từ Kaggle datasets
- Play video clips tương ứng từ local storage
- Hiển thị distance scores cho từng model
- Responsive design tối ưu cho mobile
- Real-time status check
- **Bandwidth efficient**: Media files không qua Ngrok

## API Endpoints

### Core Endpoints
- `GET /` - Web interface chính
- `GET /setup` - Hướng dẫn setup
- `GET /api/status` - System status

### Search Endpoints
- `POST /api/search/text` - BEiT3 text search
- `POST /api/search/text/siglip` - SigLIP text search
- `POST /api/search/text/aggregated` - Multi-model text search
- `POST /api/search/image` - BEiT3 image search
- `POST /api/search/image/siglip` - SigLIP image search
- `POST /api/search/image/aggregated` - Multi-model image search

### Media Serving
- `GET /api/image/<filename>` - Serve images directly from `/kaggle/input/` datasets
- `GET /api/video/<filename>` - Serve videos directly from `/kaggle/input/` datasets

**Note**: Media files được serve trực tiếp từ Kaggle datasets, không qua Ngrok để tiết kiệm bandwidth và tăng tốc độ loading.

## Architecture Overview

### Data Flow
```
User Browser → Ngrok URL → Kaggle Notebook Web Server
                ↓
        Search API calls
                ↓
        Direct file serving from /kaggle/input/
        (No Ngrok transfer for media files)
```

### Performance Benefits
- **Web Interface**: Serve qua Ngrok (chỉ HTML/CSS/JS - lightweight)
- **API Calls**: Qua Ngrok (JSON responses - minimal data)
- **Images/Videos**: Direct serving từ Kaggle datasets (no bandwidth cost)
- **Result**: Fast loading + minimal Ngrok usage

## Troubleshooting

### Models không initialize
```bash
# Check trong Kaggle cell:
print("BEiT3:", globals().get('is_initialized', lambda: False)())
print("SigLIP:", globals().get('is_siglip_initialized', lambda: False)())
```

### HTML files không tìm thấy
- Đảm bảo upload đúng `/kaggle/working/`
- Check file permissions
- Restart notebook nếu cần

### Ngrok issues
- Check Kaggle secrets có `NGROK_MAIN` token
- Restart web server
- Enable internet access trong notebook

## Cấu trúc thư mục
```
/kaggle/working/
├── kaggle_index.html          # Main interface
├── kaggle_instructions.html   # Setup guide  
├── kaggle_error.html         # Error page
└── kaggle_web_backend.py     # Web server (Cell 2)

Cell 1: backend_aic.py content
Cell 2: kaggle_web_backend.py content
```

## Lưu ý quan trọng
- ✅ GPU phải được enable
- ✅ Internet access phải được enable  
- ✅ Models mất 2-3 phút để load lần đầu
- ✅ Ngrok URL sẽ thay đổi mỗi lần restart
- ✅ Static files được serve trực tiếp từ `/kaggle/working/`
- ✅ **Media optimization**: Images/videos serve từ `/kaggle/input/` không qua Ngrok
- ✅ **Bandwidth saving**: Chỉ có web interface đi qua Ngrok, media files thì direct access

## Example Usage

### Text Search
```json
POST /api/search/text
{
  "text": "beautiful sunset over mountains",
  "top_k": 10,
  "model": "beit3"
}
```

### Image Search
```javascript
// Upload file via form data
const formData = new FormData();
formData.append('image', file);
formData.append('top_k', 10);
formData.append('model', 'siglip');
```

### Aggregated Search
```json
POST /api/search/text/aggregated
{
  "text": "person walking in the park",
  "top_k": 15,
  "intersection_only": true
}
```
