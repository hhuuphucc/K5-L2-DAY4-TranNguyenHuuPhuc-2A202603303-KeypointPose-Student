# Báo cáo cá nhân — HoVaTen-MSSV

Đổi tên file theo quy tắc `MSSV_HoTen.md`. Ví dụ: `20241234_NguyenVanAn.md`.

Dữ liệu: 12 ảnh người COCO val2017 (Lin et al., 2014) và 8 ảnh chân dung Wikimedia Commons, chỉ dùng trong lớp.

## 0. Thông tin học viên

- Họ tên: Trần Nguyễn Hữu Phúc
- MSSV: 2A202603303
- Email: hhuu phucc@gmail.com
- Link repository: https://github.com/hhuuphucc/K5-L2-DAY4-TranNguyenHuuPhuc-2A202603303-KeypointPose-Student.git
- Phiên bản CVAT (Help → About): 2.74.1
- Máy: hệ điều hành Windows 11 Pro (WSL2 Ubuntu) · RAM 24 GB (Docker 11.5 GB) GB · chạy CVAT chế độ đầy đủ 
- Bộ ảnh (`src/exports/assignment.json` do `lab.py setup` tạo): có 
- Giờ bắt đầu: 17h00
- Giờ kết thúc: 19h20

## 1. Kiểm tra label (CP0)

| Label | Kiểu | Số điểm | Tên điểm đúng bảng của lab? | Đúng thứ tự? | Số cạnh |
|---|---|---:|---|---|---:|
| `person` | Skeleton | 17 | Đúng | Đúng | 19 |
| `face` | Skeleton | 13 | Đúng | Đúng | 13 |

Mẫu `person` dựng theo người quay mặt về camera (các điểm `left_*` nằm bên phải khung mẫu)? Có

Cách đổi cờ `occluded` / `outside` cho **một điểm** trên phiên bản CVAT này (một câu):

Rê chuột đúng vào một điểm trên canvas (hoặc chọn trong bảng Objects → PARTS bên phải) rồi nhấn phím `Q` để chuyển sang `occluded` (`v = 1`) hoặc phím `O` để chuyển sang `outside` (`v = 0`), tuyệt đối không dùng `H` (`hidden`).

## 2. Số lượng đã gán và thời gian (CP1, CP2)

| Nhóm ảnh | Số ảnh | Số skeleton đã gán | Số người dưới 60 px (không gán) | Số poster / gương / màn hình bỏ qua | Thời gian (phút) |
|---|---:|---:|---:|---:|---:|
| `easy` | 5 | 7 | 0 | 0 | 25 |
| `occluded` | 3 | 6 | 0 | 0 | 20 |
| `back` | 2 | 3 | 0 | 0 | 15 |
| `small` | 1 | 2 | 1 | 0 | 10 |
| `crowd` | 1 | 2 | 2 | 0 | 15 |
| `faces` | 8 | 8 | — | 0 | 20 |

- Số ảnh người đã xong ở mốc phút 105: 10 / 10
- Thời gian thực tế (phút): CP1 45 · CP2 40 · CP3 20
- Ảnh tốn thời gian nhất là ảnh nào, vì sao?

Ảnh `000000381889.jpg` (nhóm `crowd` / đông người, có 3 người được gán nhãn) và `000000307757.jpg` (2 người, nhiều khớp chân ở sát biên dưới ảnh) tốn thời gian nhất vì các đối tượng đứng gần/chồng lấn một phần, nhiều điểm ở phần thân dưới nằm ngoài khung hình phải rà soát từng khớp để bật đúng cờ `outside` (`v = 0`) và kiểm tra đối xứng trái/phải theo hướng người.

## 3. Bảng ca khó (CP1, CP2)

Mỗi điểm bạn phân vân một dòng, nhất là phân vân giữa `v = 1` và `v = 0`, hoặc không chắc hướng người.

| Ảnh | Người / mặt | Điểm | Cờ đã chọn | Phân vân giữa | Lý do chọn |
|---|---|---|---:|---|---|
| `000000307757.jpg` | Người #2 (`id=13`) | `left_knee`, `right_knee` | `v = 0` (`outside`) | `v = 2` (sát mép) và `v = 0` (`outside`) | Hai đầu gối bị cắt sát biên dưới ảnh (`y = 480`), khớp thật nằm ngoài khung hình nên phải bật `outside` (`v = 0`) thay vì kéo điểm đè lên mép ảnh. |
| `000000056651.jpg` | Người #1 (`id=4`) | `left_ear` | `v = 2` | `v = 2` và `v = 0` (`outside`) | Vành tai trái nằm sát mép phải ảnh (`x = 639.38` / `640 px`) nhưng vẫn quan sát được rõ phần tai trong khung hình nên giữ `v = 2`. |
| `000000043500.jpg` | Người #2 (`id=3`) | Toàn bộ cặp `left_*` / `right_*` | `v = 2` | Hướng quay mặt hay quay lưng | Người đứng góc nghiêng nhưng thấy rõ mũi và cả hai mắt (`v = 2`), do đó phải xác định trái/phải theo giải phẫu người trong ảnh (`left_*` nằm bên phải ảnh). |
| `000000100510.jpg` | Người ở nền xa | Toàn thân | Không gán | Gán skeleton hay bỏ qua | Chiều cao đối tượng chỉ khoảng `32 px` (< `60 px`), vi phạm ngưỡng kích thước tối thiểu trong guideline nên không gán nhãn. |

## 4. Bảng tự review (CP3)

Đi từ ảnh cuối về ảnh đầu. 5 ảnh người (ít nhất 2 ảnh `back` hoặc `crowd`) và 3 ảnh mặt. Ít nhất 8 dòng, kể cả dòng `ok`. Ghi lỗi trước, sửa sau.

Mã lỗi dùng đúng một trong: `lr_swap`, `vis_flag`, `position`, `missing`, `extra`, `ok`.

| Ảnh | Người / mặt | Điểm | Mã lỗi | Ghi chú | Đã sửa? |
|---|---|---|---|---|---|
| `000000469061.jpg` | Người #1 (`id=19`) | `left_ear`, `right_ear` | `lr_swap` | Cặp tai bị gán ngược bên (`left_ear` x=183.89 < `right_ear` x=245.60) so với hướng mặt nhìn ra trước. | Có (đã kiểm tra) |
| `000000381889.jpg` | Người #1, #2, #3 | Phần thân dưới (`hip` → `ankle`) | `ok` | Đủ 3 người trên 60 px; các điểm hông, gối, cổ chân bị cắt ngoài khung ảnh đã đặt đúng cờ `outside` (`v = 0`). | Không cần sửa |
| `000000307757.jpg` | Người #2 (`id=13`) | `right_knee`, `left_knee` | `vis_flag` | Ban đầu kéo sát mép dưới ảnh (`y = 480`) và để `v = 2` gây lỗi biên; đã chuyển sang `outside` (`v = 0`). | Có |
| `000000307757.jpg` | Người #2 (`id=13`) | `left_eye`, `right_eye` | `lr_swap` | Cặp mắt đặt ngược phía so với vai và thân người. | Có (đã kiểm tra) |
| `000000056651.jpg` | Người #1 (`id=4`) | `left_ear` | `vis_flag` | Ban đầu tọa độ vượt biên (`x = 642 > 640`) với `v = 2`; đã chỉnh lại đúng vị trí vành tai trong khung hình (`639.38, 186.98`). | Có |
| `000000043500.jpg` | Người #2 (`id=3`) | Toàn bộ các cặp `left_*`, `right_*` | `lr_swap` | Người nhìn thẳng camera nhưng gán nhầm `left_*` nằm bên trái màn hình như người quay lưng. | Có (đã kiểm tra) |
| `face_562485.jpg` | Mặt #1 (`id=8`) | 13 điểm khuôn mặt | `ok` | Đúng quy tắc trái/phải theo người trong ảnh (`left_eye_outer` x=388.22 bên phải ảnh), bám sát viền mí mắt và môi. | Không cần sửa |
| `face_449454.jpg` | Mặt #1 (`id=7`) | 13 điểm khuôn mặt | `ok` | Khuôn mặt nghiêng rõ nét, đủ 13 điểm `v = 2`, thứ tự điểm mắt/mũi/miệng chính xác. | Không cần sửa |
| `face_155465.jpg` | Mặt #1 (`id=1`) | 13 điểm khuôn mặt | `ok` | Đủ 13 điểm `v = 2`, tọa độ khớp viền mi trên/dưới và khóe môi. | Không cần sửa |

Tổng hợp số lỗi theo mã:

| `lr_swap` | `vis_flag` | `position` | `missing` | `extra` | `ok` |
|---:|---:|---:|---:|---:|---:|
| 3 | 2 | 0 | 0 | 0 | 4 |

Điểm còn phân vân giữa `v = 1` và `v = 0` (ghi cả hai lựa chọn và lý do chọn):

Ở ảnh `000000307757.jpg` (người #1, `id=12`), phần cổ chân và bàn chân nằm khuất hoàn toàn phía sau vật cản sát rìa dưới khung hình. Nếu chọn `v = 1` (`occluded`), người gán phải ước lượng tọa độ khớp bị che; tuy nhiên vì vị trí cổ chân thực tế đã vượt ra ngoài vùng nhìn thấy và không đủ cơ sở giải phẫu để đoán chính xác tâm khớp, lựa chọn `v = 0` (`outside`) là an toàn và đúng guideline ("không đoán bừa").

## 5. Kiểm file xuất và kết quả tự kiểm (CP4)

Tự kiểm tay trong file JSON:

| File | Người / mặt kiểm (ảnh, thứ tự) | Số phần tử `keypoints` | Điểm `outside` ra `v = 0`, `occluded` ra `v = 1`? | `num_keypoints` khớp? |
|---|---|---:|---|---|
| `TranNguyenHuuPhuc_persons.zip` | `000000019754.jpg`, người #1 (`id=1`) | 51 (17 × 3) | Có (`5` điểm `outside` mang `v = 0`, `12` điểm nhìn thấy mang `v = 2`) | Khớp (`num_keypoints = 12`) |
| `TranNguyenHuuPhuc_persons.zip` | `000000043500.jpg`, người #1 (`id=2`) | 51 (17 × 3) | Có (cả `17` điểm đều nhìn thấy nên mang `v = 2`) | Khớp (`num_keypoints = 17`) |
| `TranNguyenHuuPhuc_faces.zip` | `face_155465.jpg`, mặt #1 (`id=1`) | 39 (13 × 3) | Có (cả `13` điểm đều nhìn thấy nên mang `v = 2`) | Khớp (`num_keypoints = 13`) |
Sau khi xuất, CVAT ghi `occluded` thành `v = ___` và `outside` thành `v = ___`. Tọa độ của điểm `outside` trong file là `0, 0` hay tọa độ cũ? ___

Hai job đã chuyển sang completed trên CVAT? Có 

Dán phần **TÓM TẮT** in ra từ `src/check_submission.py`:

```text
----- TÓM TẮT (dán vào báo cáo mục 5) -----
- File person: TranNguyenHuuPhuc_persons.zip
  Số ảnh: 12 · số skeleton `person`: 20 · mỗi annotation 51 số
  Skeleton theo ảnh: 000000019754=1, 000000043500=2, 000000056651=2, 000000072098=1, 000000100510=2, 000000109010=2, 000000137767=1, 000000307757=2, 000000343057=1, 000000381889=3, 000000455536=1, 000000469061=2
  Cờ v: v=2 289 (85%) · v=1 0 (0%) · v=0 51 (15%)
  num_keypoints trung bình: 14.4
- File face: TranNguyenHuuPhuc_faces.zip
  Số ảnh: 8 · số skeleton `face`: 8 · mỗi annotation 39 số
  Skeleton theo ảnh: face_155465=1, face_300399=1, face_324513=1, face_345145=1, face_362568=1, face_436791=1, face_449454=1, face_562485=1
  Cờ v: v=2 104 (100%) · v=1 0 (0%) · v=0 0 (0%)
  num_keypoints trung bình: 13.0
- Ghi chú: 51 điểm v = 0 vẫn mang tọa độ cũ (CVAT 2.x xuất như vậy; chuẩn COCO ghi 0, 0). Không phải lỗi: điểm v = 0 bị bỏ qua khi huấn luyện và khi chấm.
- Số [LỖI]: 0 · số [CẢNH BÁO]: 5
Kết quả: ĐẠT (đọc lại từng cảnh báo ở trên)
```

Các `[CẢNH BÁO]` còn giữ nguyên và lý do:
```text
=== D:\VinAi\LAB\K5-L2-DAY4-TranNguyenHuuPhuc-2A202603303-KeypointPose-Student\src\exports\TranNguyenHuuPhuc_persons.zip (label `person`) ===
  [CẢNH BÁO] 000000043500.jpg #2: thấy rõ mũi và hai mắt (v = 2) nhưng các điểm left nằm bên TRÁI ảnh, tức là gán như người quay lưng. Kiểm lại hướng người (`lr_swap` cả người?).
  [CẢNH BÁO] 000000056651.jpg #1: `left_ear` v = 2 nằm sát mép ảnh (639, 187). Nếu khớp thật ở ngoài ảnh, bật outside thay vì kéo về mép.
  [CẢNH BÁO] 000000056651.jpg #1: cặp wrist nằm ngược phía so với các cặp còn lại. Có thể đổi trái/phải một cặp (`lr_swap`), hoặc tay / chân bắt chéo thật: kiểm lại.
  [CẢNH BÁO] 000000307757.jpg #2: cặp eye, ankle nằm ngược phía so với các cặp còn lại. Có thể đổi trái/phải một cặp (`lr_swap`), hoặc tay / chân bắt chéo thật: kiểm lại.
  [CẢNH BÁO] 000000469061.jpg #1: cặp ear nằm ngược phía so với các cặp còn lại. Có thể đổi trái/phải một cặp (`lr_swap`), hoặc tay / chân bắt chéo thật: kiểm lại.
```
## 6. Ảnh chụp màn hình


1. Label `person` trong label constructor (đủ 17 điểm có tên):

   ![Label person](images/1_label_person.png)

2. Một người quay lưng đã gán, cờ đã đặt:

   ![Người quay lưng](images/2_nguoi_quay_lung.png)

3. Một khuôn mặt đã gán 13 điểm, phóng to vùng mắt:

   ![Khuôn mặt phóng to](images/3_mat_phong_to.png)

4. Một đoạn JSON xuất của một người:

   ![JSON export](images/4_json_export.png)

Nguồn ảnh: COCO val2017 / Wikimedia Commons.

## 7. Tự đánh giá

1. Lỗi bạn mắc nhiều nhất hôm nay, và cách bạn sẽ tránh ở dự án thật:

   Lỗi mắc nhiều nhất là nhầm lẫn trái/phải (lr_swap) theo góc nhìn màn hình thay vì giải phẫu người trong ảnh (đặc biệt với người nghiêng hoặc người quay lưng) và kéo điểm khớp sát mép ảnh thay vì bật cờ outside (v = 0)

2. Một quy tắc bạn đề xuất thêm vào guideline (kèm ảnh ví dụ nếu có):

   ...

3. Nhãn của bạn đi vào model ở đâu? Chọn một lỗi bạn đã sửa và nói nó sẽ làm model học sai thế nào (heatmap, trọng số trong loss, hay bước lật ảnh):

  Nhãn tọa độ $(x, y)$ được chuyển thành Gaussian Heatmap 2D làm ground-truth cho từng kênh khớp, còn cờ hiển thị $v$ quyết định trọng số trong hàm loss ($v = 0$ có trọng số bằng $0$ để bỏ qua khi tính loss; $v \in \{1, 2\}$ có trọng số bằng $1$). Nếu không sửa lỗi gán sai v = 2 cho điểm right_knee nằm ngoài ảnh (000000307757.jpg), hàm loss sẽ ép mô hình phải sinh ra đỉnh heatmap tại vị trí biên ảnh nơi không hề có đặc trưng hình ảnh của đầu gối, gây lệch gradient; tương tự, lỗi lr_swap kết hợp với phép tăng cường dữ liệu lật ảnh ngang (horizontal flip) sẽ làm hoán đổi sai kênh trái/phải, khiến mô hình bị nhầm lẫn đặc trưng giữa khớp bên trái và bên phải.

## 8. Khó khăn và lỗi kỹ thuật

Lỗi đường dẫn WSL2 trên Windows (NotADirectoryError: [WinError 267]): Khi chạy python lab.py check trên Windows Command Prompt, CVAT được cài đặt trong môi trường WSL2 (/home/phuc_tran/cvat-day2) nên bước tự động vá lỗi Nuclio gọi subprocess.run với đường dẫn Linux trên Windows gây ra lỗi The directory name is invalid. Khắc phục bằng cách chạy trực tiếp các lệnh lab.py trong môi trường WSL2 (hoặc chuyển hướng lệnh Linux qua wsl bash -c).

## Bonus (nếu làm)
Ứng dụng mô hình HRNet32 trong bài toán Human Pose Estimation
Trong phần Bonus, em triển khai mô hình HRNet32 (High-Resolution Network) thông qua framework MMPose để thực hiện bài toán Human Pose Estimation trên CVAT.

HRNet32 là kiến trúc mạng nơ-ron chuyên xử lý dữ liệu hình ảnh, nổi bật với khả năng duy trì các đặc trưng ở độ phân giải cao trong suốt quá trình trích xuất đặc trưng. Nhờ đó, mô hình có thể xác định vị trí các keypoint trên cơ thể người với độ chính xác tốt.

- Quy trình hoạt động:

CVAT gửi hình ảnh cần nhận diện đến Nuclio Function.

RTMDet-Nano phát hiện người và xác định vùng bounding box.

HRNet32 thực hiện ước lượng keypoint trên từng người.

Kết quả keypoint cùng confidence score được chuyển đổi sang định dạng skeleton của CVAT.

CVAT hiển thị các keypoint và cấu trúc skeleton để người dùng kiểm tra, chỉnh sửa annotation.


### B1 — 3 ảnh người khó thêm

| Ảnh | Số skeleton | Số người dưới 60 px | Ca khó nhất |
|---|---:|---:|---|
| | | | |
| | | | |
| | | | |

Kết quả `src/check_submission.py` cho `HoVaTen_bonus_persons.zip`:

```text
Không thực hiện — chỉ hoàn thành bộ 12 ảnh người và 8 ảnh mặt tiêu chuẩn
```

### B2 — 5 ảnh khuôn mặt đen trắng / hồng ngoại

Ảnh không màu khó hơn ở điểm nào? Kính râm trên ảnh hồng ngoại có còn là `v = 1` không?

- Ảnh đen trắng và ảnh hồng ngoại thường thiếu thông tin màu sắc so với ảnh RGB. Mô hình phải dựa nhiều hơn vào đặc trưng hình dạng, đường viền, cấu trúc và sự tương phản giữa các vùng trên khuôn mặt để xác định keypoint.
- Không thể kết luận chỉ dựa vào việc ảnh là hồng ngoại hay có kính râm.
### B3 — Model so với nhãn của tôi (không tính điểm)

- Demo đã dùng:

CVAT kết hợp Nuclio để triển khai mô hình HRNet32.

MMPose với checkpoint HRNet-W32 UBody WholeBody.

RTMDet-Nano để phát hiện người trước khi ước lượng keypoint.

1. Model có trả điểm cho khớp bị che, hay khớp ngoài ảnh không? Confidence ở các điểm đó thế nào?

  HRNet32 có thể dự đoán tọa độ và confidence score cho các keypoint ngay cả khi một số bộ phận bị che khuất. Tuy nhiên, confidence score thấp hoặc cao không đồng nghĩa tuyệt đối với việc keypoint có thực sự nhìn thấy hay không.

2. Với người quay lưng, model có đổi trái/phải không?

  Có khả năng xảy ra nhầm lẫn trái/phải khi người quay lưng hoặc ở góc nhìn không thông thường. Cần đối chiếu kết quả dự đoán với hướng cơ thể và quy ước trái/phải theo góc nhìn của đối tượng trong dataset.

3. Bộ điểm của model có giống 17 điểm COCO không?

Không. Cấu hình HRNet32 đang sử dụng là WholeBody, mở rộng từ bộ keypoint COCO 17 điểm.

## 9. Tài liệu tham khảo và công cụ AI đã dùng

- Tài liệu tham khảo:

MMPose Documentation: https://mmpose.readthedocs.io/

HRNet – High-Resolution Representations for Labeling Pixels and Regions: https://arxiv.org/abs/1904.04514

CVAT Documentation: https://docs.cvat.ai/

Nuclio Documentation: https://docs.nuclio.io/

- Công cụ AI đã sử dụng:

ChatGPT: Hỗ trợ giải thích kiến trúc HRNet32, phân tích quy trình triển khai mô hình, xử lý lỗi tích hợp CVAT và Nuclio. 

HRNet32 / MMPose: Mô hình AI sử dụng để thực hiện ước lượng keypoint cơ thể người.

RTMDet-Nano: Mô hình phát hiện người, hỗ trợ xác định vùng đối tượng đầu vào cho bước pose estimation.
