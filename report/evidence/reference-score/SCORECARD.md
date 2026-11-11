# Day-3 Segmentation — Scorecard

**Total: 46.3 / 82**

| Task | Group | Type | Metric | Points |
| --- | --- | --- | ---: | ---: |
| easy_semantic | tiers | semantic | 0.796 | 17.6 / 20 |
| medium_instance | tiers | instance | 0.631 | 16.4 / 32 |
| hard_panoptic | tiers | panoptic | 0.385 | 12.3 / 30 |

Chấm ngày 11/11/2026 bằng scorer gốc của repo trên ZIP đã export trước khi nhận reference (commit `706f7d12b44888b834572eeca6e95e0a89921124`). Reference: `tiers_gt.zip` do học viên cung cấp; tám ảnh trùng byte và schema khớp. Chỉ cập nhật báo cáo, không sửa mask/ZIP sau khi xem reference. Chưa có ground truth checkpoint, chưa phải tổng /100 hoặc điểm coach xác nhận.

Kết quả chi tiết: [Easy](easy.json), [Medium](medium.json), [Hard](hard.json), [scorecard JSON](scorecard.json). Các file này chỉ chứa số liệu chấm, không chứa mask reference.
