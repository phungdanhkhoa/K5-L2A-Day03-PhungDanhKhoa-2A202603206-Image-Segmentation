# Kiểm tra bài Day03 theo repo — 11/11/2026

Phạm vi đã kiểm: `README.md`, `GUIDE.md`, `RUBRIC.md`, `RULES.md`, `SUBMISSION.md`, `docs/SUBMISSION.md`, `CVAT_SETUP.md`, `CHECKPOINTS.md`, `guideline-mini-sheet.md`, `lab-guide.html`, manifest và schema của từng task. **Không nộp LMS theo yêu cầu của học viên; học viên sẽ tự double-check và tự nộp.**

## Đối chiếu yêu cầu

| Yêu cầu | Bằng chứng / kết quả |
| --- | --- |
| Đủ ba tier và sáu checkpoint, đúng ảnh | 9 task CVAT, đủ 3 + 3 + 2 + 6 = **14 ảnh**; đối chiếu chính xác tên file và kích thước với `data/manifest.json` và thư mục ảnh riêng. |
| Đúng class của từng task | Lấy nguyên bộ nhãn riêng từ `cvat-labels.json`, đối chiếu `classes.json`; Hard đủ 12 class, giữ nguyên dấu cách của `traffic light`/`traffic sign`. Mask chỉ tham chiếu label của đúng task. |
| Semantic / instance / panoptic đúng loại | Easy/CP3/CP4/CP6 là vùng theo class; Medium/CP1/CP2/CP5 là từng instance; Hard có năm stuff và từng thing. Không dùng bounding box thay mask. |
| Save và dữ liệu còn sau tải lại | Đã lưu **216 mask**, đọc lại từ CVAT và giải mã RLE: khớp từng pixel/class/frame với bản tinh chỉnh. Mở Job Medium trong UI thấy 21 MASK SHAPE ở ảnh đầu. |
| Biên, gộp/tách và lớp sai | Đã so ảnh gốc và overlay; có sửa poster giả người, pickup, mảnh xe bị che, giá đỡ xe, sidewalk giả và nắp ca-pô. Xem mục 2–3 của report. Ba vùng mơ hồ còn ghi rõ tại mục 4; không tuyên bố mọi object/class đều đã đúng. |
| Panoptic phủ vùng và chồng lấn | Hai ảnh Hard có coverage **99,60% / 97,83%**, 0 pixel chồng lấn; things được lưu riêng. Coverage không thay mIoU/PQ hoặc chứng minh đếm đúng xe rất nhỏ. |
| Sáu checkpoint | CP1 giữ kính/khe theo quy tắc và bỏ giá đỡ ngoài xe; CP2 tách các xe sát nhau và giữ một ID cho bus cắt rìa; CP5 giữ ID theo vật nhìn thấy; CP3 có pole/traffic sign/sky/road; CP4 chỉ road/sidewalk theo bó vỉa; CP6 không ép bus ngoài schema vào car. |
| Đúng tên / format ZIP | **9 ZIP đọc được, không rỗng**. Semantic dùng Segmentation mask 1.1, instance/panoptic dùng COCO 1.0. ZIP là bản CVAT export nguyên trạng, chỉ đổi tên file bên ngoài. |
| Export không làm đổi mask | Giải mã PNG/COCO RLE trong cả 9 ZIP, đối chiếu tên ảnh, kích thước, class và mask với CVAT: **khớp từng pixel**. [Kết quả và SHA-256](evidence/export-verification.json). |
| Report đủ bốn mục | [2A202603206_PhungDanhKhoa.md](individual/2A202603206_PhungDanhKhoa.md): bảng 9 task, quyết định Medium, lỗi thật đã sửa/Save/export lại, ba ca cụ thể có hai cách hiểu và quyết định/câu hỏi. |
| Công cụ và ngoại lệ được ghi thật | Có ghi AI/Codex, các model, nguồn/revision, cách dùng API local. Việc cho phép AI và miễn object Medium đầu được ghi là **học viên xác nhận giảng viên cho phép**, không nhận là đã tự vẽ tay. |
| Tự chấm có nguồn, không bịa PASS hoặc bonus | Sau khi học viên cung cấp `tiers_gt.zip`, chấm bằng scorer gốc: **46,3/82** (Easy 17,6; Medium 16,4; Hard 12,3). Tám ảnh trùng byte, schema khớp. Chưa có reference checkpoint hoặc điểm coach xác nhận; không suy ra tổng /100. Chỉ cập nhật báo cáo theo yêu cầu, không sửa mask/ZIP sau khi xem reference. |
| Không tải dữ liệu cấm vào fork | Không đưa ground truth, token/password, `.env`, môi trường Python, checkpoint model, cache nhãn, bản sao JPG gốc hoặc script tạo mask lên bài nộp. Preview là dẫn xuất có mask; ảnh gốc liên kết về `data/` đã có. |
| Tên report và tên fork | Report dùng MSSV/họ tên không dấu. **Giữ tên fork L2A hiện tại theo chỉ định của học viên**, dù mẫu chung trong SUBMISSION.md là K5-L2-DAY03. |
| Double-check trước LMS | [Bộ preview 14 ảnh](previews/README.md) và ba ca mơ hồ sẵn cho học viên kiểm. Trợ lý không bấm Submit LMS. |

## Kiểm tra tự động

- `python scripts/inspect_submissions.py --dir submissions`: **9 OK**, 0 thiếu ZIP, 0 lỗi cấu trúc.
- Bộ test gốc `tests/`: **25 test qua** sau khi khôi phục đúng mẫu tài liệu bị thiếu sẵn trong repo; không thay đổi test hoặc mã chấm.
- Kèm test RLE/void/overlap và dịch vụ SAM: **33 test qua, 3 subtest qua**.
- So mask đọc lại từ CVAT và mask trong ZIP: **216 mask khớp**. Đây là kiểm tra tính toàn vẹn dữ liệu, không phải đánh giá accuracy.

Lỗi tài liệu có sẵn ở commit `882b2e7`: `reports/REPORT_TEMPLATE.md` đã bị xóa, nhưng HTML/README và test vẫn dùng đường dẫn này, làm ba test lỗi. Đã khôi phục **nguyên nội dung mẫu từ commit cha** để các link và test hợp lệ. Giữ các mẫu gốc làm tài liệu; bản báo cáo của học viên được chỉ rõ ở đầu README và có tên riêng.

## Tự chấm sau khi nhận reference

[Scorecard](evidence/reference-score/SCORECARD.md) và JSON kèm theo ghi số liệu từ mã chấm nguyên bản; không thay đổi test hoặc scorer. Reference do học viên cung cấp sau bản export ở commit `706f7d12b44888b834572eeca6e95e0a89921124`, SHA-256 archive `bd25ce33fcbff1312ee635d1af23449a921abaf537df9ca6e99085cb46489f49`. Chỉ chuẩn hóa đường dẫn wrapper để cài vào thư mục groundtruth local; nội dung bảy file reference giữ nguyên. Không đưa reference lên GitHub.

Easy mIoU **0,7960**; Medium metric **0,6313**, 58 TP / 18 FP / 13 FN; Hard PQ **0,3849**, SQ **0,6496**, RQ **0,4883**. Điểm thấp ở Hard cho thấy kiểm cấu trúc/toàn vẹn và coverage cao không chứng minh chất lượng nhãn. Học viên yêu cầu giữ nguyên toàn bộ chín ZIP và chỉ cập nhật điểm hiện tại. Sáu checkpoint chưa có reference, nên kết quả hiện tại chỉ là **46,3/82**, chưa phải điểm /100.

Kiểm lại sau cập nhật báo cáo: scorecard chạy lại cho cùng **46,3/82**, JSON khớp kết quả công bố, chín ZIP trùng byte với commit trước reference. Bản sao chỉ chứa file phát hành chạy **25/25 test gốc qua**. Workspace đã cài reference có ba test lỗi vì các test đó yêu cầu không có ground truth; không sửa test để né điều kiện này.

## Mở CVAT local để kiểm thêm

Các link sau chỉ dùng trên máy đã cài CVAT; coach vẫn đọc được ZIP và preview trên fork.

| Task | Job local |
| --- | --- |
| easy_semantic | [Job 5](http://localhost:8080/tasks/6/jobs/5) |
| medium_instance | [Job 6](http://localhost:8080/tasks/7/jobs/6) |
| hard_panoptic | [Job 7](http://localhost:8080/tasks/8/jobs/7) |
| cp1_holes | [Job 8](http://localhost:8080/tasks/9/jobs/8) |
| cp2_slice | [Job 10](http://localhost:8080/tasks/10/jobs/10) |
| cp5_occlusion | [Job 9](http://localhost:8080/tasks/11/jobs/9) |
| cp3_thin | [Job 11](http://localhost:8080/tasks/12/jobs/11) |
| cp4_curb | [Job 12](http://localhost:8080/tasks/13/jobs/12) |
| cp6_coverage | [Job 13](http://localhost:8080/tasks/14/jobs/13) |

Nếu học viên sửa mask khi double-check: Save trong CVAT → export lại đúng format/tên → thay ZIP tương ứng trên fork → kiểm lại cấu trúc. Không sửa JSON/PNG trong ZIP bằng tay. Deadline ghi trong repo: **12/11/2026 12:00 UTC+7**.
