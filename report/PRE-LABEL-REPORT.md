# Báo cáo thực hành PointPillars — Day 13

Giữ bản đã điền ngoài Git, trong thư mục nhóm private do LC thu. Đây là kiểm tra formative; không ghi điểm của người khác.

## Nhóm và provenance

- Mã nhóm/phòng:
- Thành viên: xem `TEAMMATES.md` (họ tên/MSSV, vai trò từng lượt).
- Trạng thái: `executed-by-group` / `executed-on-room-LC-machine` / `provided-results`.
- Người thực sự chạy; ngày/giờ; hệ máy/architecture:
- Image tag và image ID; phiên bản repo:
- PCD được cấp / frame_id; nơi được phép chạy; fingerprint nếu LC cấp:
- Checkpoint: PointPillars KITTI có sẵn trong image; ghi checkpoint ID/hash nếu LC cấp:
- Phạm vi: front-window; score threshold:
- Giả định kênh thứ tư/intensity và nguồn z_ground:

## Ba lượt inference thật

| Lượt | delta | Pillar XY | Số hộp | mean_z | File JSON/Side/CSV | Quan sát có bằng chứng |
| --- | --- | --- | --- | --- | --- | --- |
| A | 0 | 0.16 | 1 | 0.330 | run-A/boxes-demo-delta-0-voxel-0.16.json; run-A/summary.csv | Chỉ có 1 hộp, z trung bình 0.330 m; đây là baseline không dịch z trước model. |
| B | 1.73 | 0.16 | 13 | 1.034 | run-B/boxes-demo-delta-1.73-voxel-0.16.json; run-B/summary.csv | Khi dịch input trước model, số hộp tăng lên 13 và mean_z = 1.034 m; phản ánh model giữ nguyên cấu trúc với z bị dịch theo delta. |
| C | 1.73 | 0.32 | 6 | 1.091 | run-C/boxes-demo-delta-1.73-voxel-0.32.json; run-C/summary.csv | Tăng voxel size lên 0.32 làm số hộp giảm còn 6, mean_z = 1.091 m; cho thấy độ mịn lưới ảnh hưởng lượng hộp và phân bố z. |

- A/B: thay input trước model có khác dịch cùng một hằng số cho output không? Vì sao?
- B/C: thấy gì khi đổi pillar? Có đủ bằng chứng để nói cấu hình nào tốt hơn không?
- Giới hạn ROI và góc Side ảnh hưởng cách đọc miss/yaw thế nào?
- JSON nào còn chưa đủ cơ sở để import? Cần kiểm gì tiếp?

## Ca QC có kiểm soát — không import CVAT

| Ca | Số hộp lệch z / tổng hộp | Lượng lệch | Class/x/y/yaw có đổi? | Dừng batch, kiểm từng hộp hay chưa rõ? | Bằng chứng |
| --- | --- | --- | --- | --- | --- |
| case-correct | 0/13 | 0 m | Không; x/y/yaw/class giữ nguyên | Không có dời z; là bản copy gốc của source prediction | case-correct.json; source_prediction_sha256 = 2ffb4e85d1a8746b1f290f6704204bf57e5521ae3df98ea81473a087a066fcfc |
| case-batch-z | 13/13 | -1.805 m cho mọi hộp (= delta + z_ground) | Không; chỉ z bị dịch xuống, class/x/y/yaw không đổi | Dừng batch, tác động đồng loạt lên toàn bộ 13 hộp | case-batch-z.json; z của tất cả box đều giảm đi 1.805 m so với prediction gốc |
| case-one-box-z | 1/13 | -1.805 m cho box đầu tiên | Không; chỉ box đầu tiên bị lệch z, class/x/yaw còn nguyên | Dừng từng hộp, không phải lỗi batch toàn cục | case-one-box-z.json; chỉ z của hộp đầu tiên giảm đi 1.805 m, các hộp còn lại giữ nguyên |

Ghi rõ helper tạo biến đổi có chủ đích từ prediction, không phải kết quả inference riêng hoặc nhãn đúng.

## Nhận xét cá nhân

Mỗi thành viên tự viết một mục: vai trò đã làm; một quan sát A/B/C có dẫn file hoặc hộp/vùng; diễn giải phép z thuận/ngược; một quyết định lỗi batch và hành động; điều chưa chắc. Chỉ đọc kết quả chuẩn bị trước thì ghi rõ chưa tự chạy.

## LC ghi nhận riêng

- Quyền dùng PCD/image và đúng ca:
- Có chạy thật / chỉ phân tích; còn cần lượt thực hành bổ sung:
- Output đủ, giữ bản gốc, không đưa ca lỗi vào CVAT:
- Nhận xét từng thành viên và quyết định dừng pipeline:
- Đồng ý chuyển sang chỉnh/QC / cần bổ sung; lý do:
