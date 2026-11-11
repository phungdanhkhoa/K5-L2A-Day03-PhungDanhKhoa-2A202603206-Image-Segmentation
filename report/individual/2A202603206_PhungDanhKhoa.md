# Báo cáo thực hành Day 3 — PhungDanhKhoa

- Mã học viên theo tên fork: **2A202603206**.
- Ngày / CVAT local: **11/11/2026**, CVAT **2.74.1**, 9 task / 14 ảnh.
- Công cụ đã dùng: **Codex hỗ trợ AI**, SegFormer B2 Cityscapes, Mask2Former Swin-Tiny COCO Panoptic, YOLO11m-seg, SAM ViT-B chạy PyTorch/MPS trên Mac; CVAT API để lưu mask và CVAT để export ZIP nguyên bản.
- Phạm vi hỗ trợ: học viên xác nhận giảng viên cho phép AI tạo mask để học viên kiểm lại; học viên cũng xác nhận được miễn bước tự vẽ object Medium đầu tiên. Đây là thông tin do học viên cung cấp, không phải xác nhận độc lập từ giảng viên.
- **Trạng thái kiểm của học viên: đang chờ xác nhận sau khi xem bản mask và các ca mơ hồ bên dưới.** Phần quan sát/sửa và kiểm kỹ thuật trong báo cáo này do AI thực hiện; không nhận là học viên đã tự vẽ hoặc đã tự kiểm khi chưa có xác nhận.
- Minh chứng: [preview đủ 14 ảnh và số mask](../previews/README.md), [kiểm tra mask trong ZIP](../evidence/export-verification.json).

## 1. Bài đã nộp

Đã tạo các ZIP theo bảng dưới để đưa lên fork. **Trợ lý không nộp LMS theo yêu cầu; học viên sẽ tự double-check và tự nộp link.** Số mask là số đã lưu/export, không phải số vật đúng đã được coach chấm. Cột điểm chỉ là mức tối đa theo rubric.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | `submissions/easy_semantic.zip` | 3 / 3 | 20 |
| medium_instance | `submissions/medium_instance.zip` | 3 / 3 | 32 |
| hard_panoptic | `submissions/hard_panoptic.zip` | 2 / 2 | 30 |
| cp1_holes | `submissions/cp1_holes.zip` | 1 / 1 | 3 |
| cp2_slice | `submissions/cp2_slice.zip` | 1 / 1 | 3 |
| cp5_occlusion | `submissions/cp5_occlusion.zip` | 1 / 1 | 3 |
| cp3_thin | `submissions/cp3_thin.zip` | 1 / 1 | 3 |
| cp4_curb | `submissions/cp4_curb.zip` | 1 / 1 | 3 |
| cp6_coverage | `submissions/cp6_coverage.zip` | 1 / 1 | 3 |
| **Tổng tối đa** | **9 ZIP** | **14 / 14** | **100** |

Easy và các checkpoint semantic xuất **Segmentation mask 1.1**; Medium, Hard và các checkpoint instance xuất **COCO 1.0**. Không sửa JSON/PNG bên trong ZIP sau export. Tổng 216 mask lưu trong CVAT gồm cả vùng semantic/stuff và object instance. Hai ảnh Hard có coverage pixel đã gán lần lượt khoảng **99,60%** và **97,83%**, không có pixel chồng lấn trong bản hiện tại; các con số này không chứng minh class/instance đúng.

Đã chạy `scripts/inspect_submissions.py`: 9 task `OK`, không thiếu ZIP. Kiểm bổ sung đối chiếu class, tên ảnh, kích thước và mask giải mã trong cả 9 ZIP với dữ liệu đã lưu trong CVAT: khớp từng pixel. Kiểm này chỉ chứng minh dữ liệu xuất không bị thay đổi, không thay thế đánh giá độ chính xác bằng ground truth.

## 2. Một quyết định trước khi dùng gợi ý

- **Object tự vẽ trước gợi ý:** không có trong bản này; bước này được miễn theo xác nhận của học viên nêu ở đầu báo cáo. Không ghi nhận một object AI tạo là object học viên vẽ tay.
- Quyết định gán nhãn đã thực hiện trên ảnh Medium `000000181542.jpg`: người phụ nữ đứng giữa đường, khoảng **x=174–305, y=132–460**, gán `person`; bám phần cơ thể nhìn thấy và không nối mask sang xe máy hoặc nền.
- Poster người trong cửa hiệu phía sau, khoảng **x=270–309, y=63–145**, bị model đề xuất `person`. Đã bỏ vì đó là hình người trên quảng cáo, không phải người thật trong cảnh.
- Xe máy đi qua sau người phụ nữ có các phần nhìn thấy rời nhau: gộp phần thân/bánh của **cùng xe** thành một instance, không vẽ xuyên qua vùng người che. Giữ xe máy khác ở phía sau và bánh xe cắt ở rìa ảnh thành các instance riêng khi có dấu hiệu phân biệt.
- Pickup phía sau được sửa từ gợi ý `car` sang `truck` dựa vào thùng chở hàng nhìn thấy. Các đề xuất SAM nhảy sang vật khác hoặc tăng diện tích quá nhiều bị loại thay vì giữ chỉ vì model có confidence cao.

## 3. Một lỗi tôi tìm thấy và sửa

Các quan sát và thao tác dưới đây do AI thực hiện, chờ học viên kiểm xác nhận:

- Task/ảnh/vùng: `cp1_holes`, `000000144300.jpg`, xe máy Honda và giá đỡ đỏ phía dưới bánh sau.
- Lỗi thuộc loại: **biên / ăn sang vật khác**. Mask ban đầu của xe máy gồm một phần giá đỡ đỏ, nhất là vùng **x≈465–622, y≈311–407**.
- Bằng chứng nhìn thấy: giá đỡ là bộ phận hỗ trợ đặt trên mặt đường, có khung đỏ và bánh lăn riêng; nó nằm ngoài đường bao thân/bánh xe máy.
- Quy tắc và hành động sửa: dùng SAM với điểm âm trên giá đỡ, bỏ phần đỡ ngoài đường bao; giữ một instance xe máy. Theo yêu cầu `cp1_holes`, giữ kính và các khoảng nội bộ trong mask xe, không khoét lỗ tùy tiện. Gộp các mảnh của cùng phương tiện nền nếu model tách sai; trường hợp caravan cần xác nhận taxonomy ở mục 4.
- Sau sửa: **đã lưu vào CVAT, đọc lại để đối chiếu, export lại `cp1_holes.zip` và kiểm giải mã RLE khớp mask đã lưu**.
- Sửa thêm đã thực hiện: bỏ sidewalk giả trên dải chắn bê tông ảnh Easy cao tốc; bỏ nắp ca-pô xe camera khỏi `road`/`sidewalk` trong `cp4_curb`; bỏ xe buýt bị model gán thành `car` trong `cp6_coverage` vì schema không có `bus`.
- Kết quả tự đánh giá với reference: **chưa có điểm**. Chưa tải/đọc ground truth. Việc kiểm metadata release chính thức bị giới hạn API, chưa xác nhận được reference khả dụng. Không tự ghi PASS, top 3 hay bonus. Scorecard ba tier nếu chạy được sau khi reference chính thức phát chỉ tối đa 82, không phải điểm cuối /100.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| `cp1_holes/000000144300.jpg`, caravan bên trái, x≈0–371, y≈0–266 | `bus` theo gợi ý mô hình hoặc caravan/trailer ngoài taxonomy / quy ước `truck` của lớp | Có thân caravan và cửa sổ; không thấy rõ phần đầu kéo/động cơ. Cả hai mô hình đều có gợi ý `bus`, nhưng gợi ý không quyết định correctness. | Bản nháp giữ **một instance `bus`**, không tách phần mái/awning thành xe khác. Cần học viên/coach xác nhận caravan được xếp lớp nào. |
| `cp6_coverage/7daa6479-67988f3f.jpg`, xe buýt trắng giữa ảnh và xe tour bên trái | Ép vào `car` để tăng coverage hoặc để void vì schema thiếu `bus`/`truck` | Tên “New York City Bus”, thân/kích thước xe buýt nhìn thấy rõ; `classes.json` chỉ có road, sidewalk, building, vegetation, sky, car, person. | Bỏ nhãn sai và **giữ void** ở các xe ngoài schema; coverage hiện khoảng **78,85%**. Cần coach xác nhận cách xử lý schema thay vì tô bừa cho đủ 100%. |
| `hard_panoptic/000000350023.jpg`, cụm xe rất xa ở cuối trục đường, x≈220–300, y≈310–380 | Nhiều xe tách rời hoặc một cụm pixel không đủ phân biệt instance | Các xe chỉ rộng vài pixel, một số gợi ý có phần rời nhau; không thể dùng confidence hoặc coverage cao để chứng minh đếm đủ vật. | Giữ các instance có dấu hiệu nhận diện trong bản nháp, **không nhận là đã xác minh đầy đủ số xe rất xa**. Cần xem ở zoom lớn và xin coach quy tắc vật quá nhỏ. |

Nguồn dữ liệu là ảnh và schema đã có trong repo đề bài; không bổ sung ảnh riêng hoặc đáp án. Nguồn model/tài liệu thêm: [Meta SAM](https://github.com/facebookresearch/segment-anything), [NVIDIA SegFormer B2 Cityscapes](https://huggingface.co/nvidia/segformer-b2-finetuned-cityscapes-1024-1024) (revision `d633b2072669ca68d8f8e309de9b52bfdbf6bf72`), [Meta Mask2Former COCO Panoptic](https://huggingface.co/facebook/mask2former-swin-tiny-coco-panoptic) (revision `df6b1142ff50c3276559d9d78f35f6a579c75a77`), [Ultralytics YOLO11](https://docs.ultralytics.com/models/yolo11/), [CVAT AI models](https://docs.cvat.ai/docs/annotation/auto-annotation/ai-models/).
