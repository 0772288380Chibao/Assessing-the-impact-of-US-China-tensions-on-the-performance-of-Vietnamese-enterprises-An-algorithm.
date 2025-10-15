# Assessing-the-impact-of-US-China-tensions-on-the-performance-of-Vietnamese-enterprises-An-algorithm.
**Tổng quan dự án**

Dự án này là một chương trình Python được thiết kế để tự động thu thập, lọc và phân tích dữ liệu bài báo từ trang web, với mục tiêu nghiên cứu các bài viết liên quan đến căng thẳng thương mại giữa Hoa Kỳ và Trung Quốc từ năm 2014 trở đi. Chương trình sử dụng các kỹ thuật web scraping, và phân tích dữ liệu để trích xuất thông tin, kiểm tra từ khóa, và tạo báo cáo thống kê theo tháng. Kết quả được lưu dưới dạng file JSON, Excel, và tích hợp với Google Drive để đảm bảo khả năng lưu trữ và tái sử dụng.

**LƯU Ý**

Do code lấy dữ liệu tin tức qua sitemap, để lấy được tin tức thì trang web cần phải lưu sitemap những bài báo lại, nếu sitemap không được cung cấp hoặc bị xóa thì cách khai thác dữ liệu này sẽ không thể thực hiện

**Mục tiêu chính của dự án**:

- Thu thập dữ liệu:Lấy danh sách bài báo từ sitemap của báo.

- Lọc nội dung: Xác định các bài báo liên quan đến Hoa Kỳ, Trung Quốc, các chủ đề thương mại, và căng thẳng địa chính trị, loại bỏ nội dung không liên quan (ví dụ: phim ảnh, thể thao).

- Phân tích thống kê: Tạo bảng tổng hợp số lượng bài báo và tần suất từ khóa theo tháng để hỗ trợ nghiên cứu xu hướng.

- Lưu trữ và xuất dữ liệu: Lưu dữ liệu thô và kết quả phân tích vào file JSON và Excel, đồng thời tích hợp với Google Drive.

- Cấu trúc và chức năng của chương trình
# 1. Khởi tạo và nhập thư viện

- Chương trình sử dụng một bộ thư viện Python để thực hiện các tác vụ từ scraping đến phân tích dữ liệu:

- Web scraping: requests, BeautifulSoup, selenium, newspaper để tải và trích xuất nội dung trang web.

- Xử lý dữ liệu: pandas để tạo và thao tác với bảng dữ liệu, json để lưu trữ dữ liệu có cấu trúc.

- Xử lý ngôn ngữ tự nhiên: nltk để tóm tắt bài báo.

- Quản lý hệ thống: os, datetime, re để xử lý file, ngày tháng, và làm sạch dữ liệu.

- Tối ưu hóa hiệu suất: concurrent.futures để xử lý song song, tăng tốc độ thu thập dữ liệu.

- Tích hợp Google Drive: google.colab để lưu kết quả lên đám mây.

# 2. Thu thập bài báo từ sitemap

Chương trình bắt đầu bằng việc truy cập sitemap của Báo Đầu Tư để lấy danh sách URL bài báo:

- Hàm get_sitemap_urls: Tạo danh sách các URL sitemap theo năm và tháng (từ 2014 đến 2025).

- Hàm get_article_urls_from_sitemap: Phân tích file sitemap XML để trích xuất URL bài báo và ngày chỉnh sửa cuối (lastmod).

- Hàm crawl_articles_from_sitemaps: Tổng hợp danh sách bài báo, lọc chỉ giữ lại các bài từ 01/01/2014 trở đi để phù hợp với phạm vi nghiên cứu.

# 3. Xử lý và lọc bài báo

Sau khi có danh sách bài báo, chương trình thực hiện các bước xử lý chi tiết:

## Hàm process_article:

Tải nội dung bài báo bằng requests với cơ chế thử lại (retry) để xử lý lỗi kết nối.

Sử dụng thư viện newspaper để trích xuất tiêu đề, nội dung, và tạo tóm tắt bằng NLP.

Kiểm tra ngày xuất bản bằng hàm is_within_date_range để đảm bảo bài báo nằm trong khoảng từ 2014.

Kiểm tra từ khóa với hàm get_matching_keywords, yêu cầu bài báo phải chứa ít nhất một từ khóa từ bốn danh mục:

Hoa Kỳ: "Mỹ", "Washington", "Nhà Trắng", v.v.

Trung Quốc: "Trung Quốc", "Bắc Kinh", v.v.

Chủ đề thương mại: "thuế quan", "xuất khẩu", "WTO", v.v.

Căng thẳng: "chiến tranh", "trừng phạt", "xung đột", v.v.

Loại bỏ bài báo chứa từ khóa không liên quan như "phim", "bóng đá", "hẹn hò".

Nếu bài báo đáp ứng tiêu chí, lưu nội dung vào file .txt bằng hàm save_to_text_file với thông tin tiêu đề, URL, ngày, nội dung, và tóm tắt.

## Hàm crawl_and_process_articles:

Sử dụng ThreadPoolExecutor với 20 luồng (threads) để xử lý song song, tối ưu hóa hiệu suất.

Theo dõi các URL đã xử lý để tránh trùng lặp.

Tổng hợp danh sách bài báo đáp ứng tiêu chí (approved_articles) và danh sách tất cả bài báo từ sitemap (all_article_infos).

# 4. Phân tích và tạo báo cáo

Chương trình tạo hai loại báo cáo thống kê để hỗ trợ phân tích xu hướng:

## Hàm create_month_summary:

Tạo bảng dữ liệu (DataFrame) tổng hợp theo tháng, bao gồm:

Tổng số bài báo trong tháng (total_count).

Số bài báo được duyệt theo tiêu chí từ khóa (approved_count).

Tỷ lệ bài báo được duyệt (approved_ratio).

Bảng này giúp phân tích xu hướng số lượng bài báo liên quan qua thời gian.

## Hàm create_keyword_frequency_df:

Tạo bảng tần suất xuất hiện của các từ khóa theo tháng.

Tính tỷ lệ xuất hiện của mỗi từ khóa (số lần xuất hiện chia cho tổng số bài báo trong tháng).

Bảng này hỗ trợ phân tích mức độ quan tâm đến các chủ đề cụ thể, như "thuế quan" hay "chiến tranh thương mại".

# 5. Lưu trữ và xuất kết quả

Lưu dữ liệu thô: Danh sách bài báo được duyệt được lưu vào file JSON (approved_articles_baodautu.json) để hỗ trợ tái sử dụng và kiểm tra.

Xuất báo cáo: Các bảng dữ liệu được xuất ra ba file Excel và lưu lên Google Drive:

approved_articles_baodautu.xlsx: Danh sách bài báo được duyệt với tiêu đề, URL, ngày, nội dung, tóm tắt, và từ khóa.

month_summary_baodautu.xlsx: Bảng tổng hợp số lượng bài báo theo tháng.

keyword_frequency_baodautu.xlsx: Bảng tần suất từ khóa theo tháng.
# KẾT QUẢ CỦA NGHIÊN CỨU
<img width="615" height="326" alt="image" src="https://github.com/user-attachments/assets/069af761-dfee-4008-9d3a-11a6ad42264a" />

