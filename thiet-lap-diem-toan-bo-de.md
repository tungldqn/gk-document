# Tài liệu Yêu cầu Tính năng: Thiết lập điểm toàn bộ đề

| Thông tin              | Nội dung                               |
| ---------------------- | -------------------------------------- |
| **Tên tính năng**      | Thiết lập điểm toàn bộ đề              |
| **Module**             | Quản lý bài kiểm tra (Quiz Management) |
| **Phiên bản tài liệu** | 1.0                                    |
| **Ngày tạo**           | 2026-06-18                             |
| **Trạng thái**         | Draft                                  |

---

## 1. Tổng quan

### 1.1. Mô tả

Tính năng **Thiết lập điểm toàn bộ đề** cho phép giáo viên/người tạo đề thi nhập một mức điểm tổng cho toàn bộ bài kiểm tra. Hệ thống sẽ tự động chia đều mức điểm đó cho tất cả các câu hỏi trong đề, thay thế điểm số hiện tại của từng câu hỏi.

### 1.2. Mục tiêu

- Giúp người dùng nhanh chóng chuẩn hóa thang điểm của bài kiểm tra (ví dụ: đặt tổng điểm là 10, 100, ...) mà không cần chỉnh từng câu hỏi.
- Đảm bảo tổng điểm toàn đề khớp với yêu cầu nghiệp vụ.

### 1.3. Người dùng liên quan

| Vai trò                  | Mô tả                                                       |
| ------------------------ | ----------------------------------------------------------- |
| Giáo viên / Người tạo đề | Sử dụng tính năng để thiết lập và quản lý điểm bài kiểm tra |

---

## 2. Màn hình liên quan

**Màn hình:** Trang chi tiết bài kiểm tra → Tab danh sách câu hỏi

![Tổng quan màn hình Chi tiết bài kiểm tra](images/01-tong-quan-man-hinh.png)
_Tổng quan màn hình Chi tiết bài kiểm tra — vùng thống kê trên cùng và toolbar bên dưới_

---

## 3. Mô tả các thành phần giao diện

### 3.1. Vùng thống kê tổng quan (Summary Bar)

Nằm ở đầu trang, hiển thị 3 thẻ thông tin:

| Thẻ                       | Nội dung hiển thị                                               |
| ------------------------- | --------------------------------------------------------------- |
| Số lượng câu hỏi          | Tổng số câu hỏi hợp lệ trong đề                                 |
| Tổng điểm của bài tập     | Tổng điểm hiện tại của đề (cập nhật realtime sau khi thiết lập) |
| Thời gian dự kiến làm bài | Tổng thời gian của các câu hỏi                                  |

![Vùng Summary Bar — 3 thẻ thống kê](images/02-summary-bar.png)
_Vùng Summary Bar — 3 thẻ: Số lượng câu hỏi, Tổng điểm, Thời gian dự kiến_

### 3.2. Toolbar "Chi tiết bài tập"

Nằm bên dưới Summary Bar, gồm các điều khiển theo thứ tự từ trái sang phải:

1. **Nhãn "Chi tiết bài tập"**
2. **Toggle "Hiển thị đáp án"** — Bật/tắt hiển thị đáp án đúng trên danh sách câu hỏi
3. **Toggle "Thiết lập điểm toàn bộ đề"** — Bật/tắt tính năng thiết lập điểm tổng
4. **Input điểm + nút "Áp dụng"** _(chỉ hiển thị khi tính năng đang BẬT)_
5. **Nút "+ Thêm câu hỏi"** (góc phải)

![Toolbar khi tính năng TẮT](images/03-toolbar-off.png)
_Toolbar khi switch "Thiết lập điểm toàn bộ đề" ở trạng thái OFF_

![Toolbar khi tính năng BẬT](images/04-toolbar-on.png)
_Toolbar khi switch ở trạng thái ON — có thêm ô nhập điểm và nút "Áp dụng"_

---

## 4. Luồng nghiệp vụ chính

### 4.1. Luồng BẬT tính năng (Kích hoạt lần đầu)

```
Người dùng bật Toggle
        │
        ▼
Hiển thị Modal "Thiết lập điểm toàn bộ đề"
        │
        ├── Người dùng nhấn "Hủy"
        │       └── Đóng modal, Toggle về OFF
        │
        └── Người dùng nhập điểm → nhấn "Xác nhận"
                │
                ├── [Điểm <= 0] → Thông báo lỗi, giữ modal
                │
                └── [Điểm > 0]
                        │
                        ▼
                Lưu tổng điểm vào đề thi (DB)
                        │
                        ▼
                Tính điểm mỗi câu = round(tổng điểm / số câu, 2)
                        │
                        ▼
                Cập nhật điểm từng câu hỏi (DB)
                        │
                        ▼
                Tải lại dữ liệu đề thi
                        │
                        ▼
                Thông báo thành công
                Toggle giữ trạng thái ON
                Hiển thị ô nhập điểm inline
```

**Modal "Thiết lập điểm toàn bộ đề":**

| Thuộc tính   | Nội dung                                                                                                                                                     |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Tiêu đề      | Thiết lập điểm toàn bộ đề                                                                                                                                    |
| Mô tả        | Thiết lập điểm cho toàn bộ đề sẽ ghi đè và chia đều điểm của từng câu hỏi. Vui lòng nhập điểm muốn thiết lập cho toàn bộ đề vào ô bên dưới và nhấn xác nhận. |
| Input        | Ô nhập số điểm (chỉ nhận giá trị số dương)                                                                                                                   |
| Nút Hủy      | Đóng modal, không thực hiện thay đổi                                                                                                                         |
| Nút Xác nhận | Thực hiện thiết lập điểm                                                                                                                                     |
| Icon         | Settings (màu xanh dương)                                                                                                                                    |

![Modal Thiết lập điểm toàn bộ đề](images/05-modal-thiet-lap-diem.png)
_Modal Thiết lập điểm toàn bộ đề — hiển thị ô nhập điểm và 2 nút Hủy / Xác nhận_

### 4.2. Luồng CHỈNH SỬA điểm (Khi tính năng đang BẬT)

Sau khi tính năng được kích hoạt, người dùng có thể thay đổi tổng điểm trực tiếp từ toolbar mà không cần mở modal:

```
Người dùng thay đổi giá trị trong ô nhập điểm inline
        │
        ├── Nhấn phím Enter
        │       └── Thực hiện handleSetTotalPoint(giá trị mới)
        │
        └── Nhấn nút "Áp dụng"
                └── Thực hiện handleSetTotalPoint(giá trị mới)

handleSetTotalPoint:
        ├── [Điểm <= 0] → Thông báo lỗi "Điểm toàn bộ đề phải lớn hơn 0"
        └── [Điểm > 0] → Thực hiện tương tự luồng 4.1
```

![Inline edit điểm trên Toolbar](images/06-inline-edit-diem.png)
_Ô nhập điểm inline và nút "Áp dụng" xuất hiện trên toolbar khi tính năng đang BẬT_

### 4.3. Luồng TẮT tính năng (Hủy thiết lập điểm)

```
Người dùng tắt Toggle (khi đang BẬT)
        │
        ▼
Hiển thị Modal xác nhận "Xác nhận huỷ bỏ"
        │
        ├── Người dùng nhấn "Hủy"
        │       └── Đóng modal, Toggle về ON (không thay đổi)
        │
        └── Người dùng nhấn "Xác nhận"
                │
                ▼
        Xóa tổng điểm khỏi đề thi (set null)
                │
                ▼
        Tải lại dữ liệu đề thi
                │
                ▼
        Thông báo thành công "Hủy bỏ điểm toàn bộ đề thành công"
        Toggle về OFF, ẩn ô nhập điểm inline
```

> **⚠️ Lưu ý nghiệp vụ quan trọng:** Khi tắt tính năng, điểm của từng câu hỏi riêng lẻ **KHÔNG** bị reset về giá trị cũ. Các câu hỏi vẫn giữ nguyên mức điểm đã được phân phối từ lần thiết lập gần nhất. Chỉ thông tin tổng điểm cấp độ đề thi bị xóa.

**Modal "Xác nhận huỷ bỏ":**

| Thuộc tính   | Nội dung                                 |
| ------------ | ---------------------------------------- |
| Tiêu đề      | Xác nhận huỷ bỏ                          |
| Mô tả        | Bạn có chắc muốn huỷ bỏ điểm toàn bộ đề? |
| Nút Hủy      | Đóng modal, không thay đổi               |
| Nút Xác nhận | Thực hiện hủy bỏ                         |
| Icon         | Trash (màu đỏ)                           |

![Modal xác nhận hủy bỏ điểm toàn bộ đề](images/07-modal-huy-bo.png)
_Modal xác nhận hủy bỏ — icon Trash màu đỏ, 2 nút Hủy / Xác nhận_

---

## 5. Luồng liên quan: Xóa câu hỏi khi tính năng đang BẬT

Khi người dùng xóa một câu hỏi trong khi tính năng Thiết lập điểm toàn bộ đề đang hoạt động, hệ thống tự động phân phối lại điểm:

```
Người dùng xóa câu hỏi
        │
        ▼
Xác nhận xóa (modal xác nhận xóa)
        │
        ▼
Xóa câu hỏi (đánh dấu is_deleted = true)
        │
        ▼
[Còn câu hỏi trong đề VÀ tổng điểm > 0]
        │
        ▼
Phân phối lại điểm cho các câu hỏi còn lại
(tổng điểm / số câu còn lại)
        │
        ▼
Tải lại dữ liệu
```

> **⚠️ Lưu ý:** Nếu đề không còn câu hỏi nào sau khi xóa, bước phân phối điểm bị bỏ qua.

---

## 6. Ảnh hưởng khi thêm / sửa câu hỏi bình thường

> **Màn hình:** Thêm câu hỏi (`/bai-kiem-tra/{examId}/them-cau-hoi`) hoặc Sửa câu hỏi khi loại câu hỏi **không phải** câu chùm (group question).

### 6.1. Trạng thái ô nhập điểm trên Navbar

Khi đề thi đã có thiết lập tổng điểm (`total_points`), ô nhập điểm trên navbar bị **disable** — người dùng không thể tự sửa điểm câu hỏi thủ công. Điểm sẽ được hệ thống tự tính và gán.

![Navbar câu hỏi bình thường — ô điểm bị disable khi tính năng đang BẬT](images/08-navbar-normal-diem-disabled.png)
_Ô nhập điểm trên Navbar bị mờ (disable) khi đề đang có thiết lập tổng điểm_

### 6.2. Luồng thêm câu hỏi mới

```
Người dùng nhấn "Lưu"
        │
        ▼
Tính điểm phân phối:
distributedPoint = round(total_points / (số_câu_gốc_hiện_tại + 1), 2)
        │
        ▼
Gán điểm câu mới = distributedPoint
        │
        ▼
Lưu câu hỏi mới vào DB
        │
        ▼
[Đề đã có câu hỏi cũ VÀ total_points > 0]
        │
        ▼
Phân phối lại điểm cho tất cả câu cũ = distributedPoint
        │
        ▼
Quay về trang danh sách câu hỏi
        Toast: "Thêm câu hỏi thành công"
```

> **⚠️ Lưu ý:** `số_câu_gốc_hiện_tại` chỉ đếm câu hỏi có `parent_id = null` (tức câu hỏi gốc, không tính câu con của câu chùm). Câu mới đang thêm chưa được tính nên cộng `+1` vào mẫu số.

### 6.3. Luồng sửa câu hỏi

Khi **sửa** câu hỏi bình thường, không có logic phân phối điểm đặc biệt — điểm câu hỏi không bị thay đổi hay phân phối lại sau khi lưu. Ô điểm vẫn bị disable, không cho sửa thủ công.

---

## 7. Ảnh hưởng khi thêm / sửa câu hỏi nhóm (câu chùm)

> **Màn hình:** Thêm câu hỏi hoặc Sửa câu hỏi khi loại câu hỏi là **câu chùm** (group question / group).

### 7.1. Validation điểm trước khi lưu

Trước khi lưu (cả thêm mới lẫn sửa), hệ thống kiểm tra xem tổng điểm của câu nhóm có vượt quá mức điểm được phân phối hay không.

**Cách tính `questionsPoint` (điểm của câu nhóm):**

| Loại tính điểm câu nhóm                           | Cách lấy `questionsPoint`                            |
| ------------------------------------------------- | ---------------------------------------------------- |
| Tính theo nhóm (`whole_group` / có `score_table`) | Lấy giá trị lớn nhất trong `score_table` của câu cha |
| Tính theo từng câu con (`per_question`)           | Tổng điểm của tất cả các câu con                     |

**Cách tính `distributedPoint` (mức điểm phân phối mỗi câu):**

| Thao tác          | Công thức                                                              |
| ----------------- | ---------------------------------------------------------------------- |
| Thêm câu nhóm mới | `total_points / (số_câu_gốc_hiện_tại + 1)`                             |
| Sửa câu nhóm      | `total_points / số_câu_gốc_hiện_tại` (câu này đã được tính trong tổng) |

**Kết quả validation:**

- `questionsPoint <= distributedPoint` → cho phép lưu
- `questionsPoint > distributedPoint` → **chặn lưu**, hiển thị toast error:

> _"Tổng điểm của nhóm câu hỏi (X điểm) vượt quá điểm phân phối còn lại của đề thi (Y điểm). Vui lòng điều chỉnh điểm của nhóm câu hỏi hoặc tăng tổng điểm của đề thi."_

![Toast error khi điểm câu nhóm vượt mức phân phối](images/09-toast-error-vuot-diem-phan-phoi.png)
_Toast error hiển thị khi tổng điểm câu chùm vượt quá mức điểm được phân phối từ tổng điểm đề_

### 7.2. Luồng thêm câu nhóm mới

```
Người dùng nhấn "Lưu"
        │
        ▼
Validate: questionsPoint <= total_points / (số_câu_gốc + 1)?
        │
        ├── [Vượt quá] → Toast error, chặn lưu
        │
        └── [Hợp lệ]
                │
                ▼
        Lưu câu nhóm cha + tất cả câu con vào DB
                │
                ▼
        [Có câu hỏi cũ trong đề VÀ total_points > 0]
                │
                ▼
        Tính lại distributedPoint = round(total_points / (số_câu_gốc + 1), 2)
                │
                ▼
        Phân phối lại điểm cho tất cả câu cũ = distributedPoint
                │
                ▼
        Quay về trang danh sách
        Toast: "Thêm câu hỏi thành công"
```

![Luồng thêm câu nhóm thành công](images/10-them-cau-nhom-thanh-cong.png)
_Màn hình form tạo câu chùm khi đề đang có thiết lập tổng điểm_

### 7.3. Luồng sửa câu nhóm

```
Người dùng nhấn "Lưu"
        │
        ▼
Validate: questionsPoint <= total_points / số_câu_gốc?
(không +1 vì câu này đã được tính trong số_câu_gốc)
        │
        ├── [Vượt quá] → Toast error, chặn lưu
        │
        └── [Hợp lệ]
                │
                ▼
        Cập nhật câu nhóm cha + các câu con
                │
                ▼
        Quay về trang danh sách
        Toast: "Sửa câu hỏi thành công"
```

> **⚠️ Lưu ý:** Khi **sửa** câu nhóm, điểm của các câu hỏi khác trong đề **KHÔNG** bị phân phối lại. Chỉ có thao tác **thêm mới** mới trigger phân phối lại.

---

## 8. Quy tắc nghiệp vụ (Business Rules)

| STT   | Quy tắc                     | Chi tiết                                                                                                                  |
| ----- | --------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| BR-01 | Tổng điểm phải dương        | Giá trị nhập phải > 0. Hệ thống báo lỗi nếu <= 0                                                                          |
| BR-02 | Phân phối đồng đều          | Điểm mỗi câu = `round(tổng điểm / số câu hỏi, 2)` (làm tròn 2 chữ số thập phân)                                           |
| BR-03 | Chỉ câu hỏi gốc             | Chỉ áp dụng cho câu hỏi gốc (không phải câu hỏi con của câu chùm). Câu hỏi con được xử lý thông qua câu chùm cha          |
| BR-04 | Câu chùm (Group question)   | Với câu chùm có `score_table` trong metadata: cập nhật cả trường `point` và giá trị lớn nhất trong `score_table`          |
| BR-05 | Không hoàn tác điểm câu hỏi | Khi tắt tính năng, điểm riêng lẻ của từng câu hỏi giữ nguyên, chỉ thông tin tổng điểm cấp đề bị xóa                       |
| BR-06 | Tự động phân phối lại       | Khi xóa câu hỏi (và tổng điểm > 0), điểm được tự động phân phối lại cho các câu còn lại                                   |
| BR-07 | Trạng thái khởi tạo         | Nếu đề đã có `total_points` lưu trong DB, toggle hiển thị trạng thái ON và ô nhập điểm inline hiển thị ngay khi vào trang |

---

## 9. Trạng thái giao diện

| Trạng thái     | Toggle | Ô nhập điểm inline    | Mô tả                                  |
| -------------- | ------ | --------------------- | -------------------------------------- |
| Chưa thiết lập | OFF    | Ẩn                    | Đề chưa có tổng điểm                   |
| Đang thiết lập | ON     | Hiển thị (có giá trị) | Đề đang có tổng điểm, có thể chỉnh sửa |

---

## 10. Thông báo hệ thống (Toast Messages)

| Tình huống                       | Loại    | Nội dung                                                                                                                                                           |
| -------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Thiết lập điểm thành công        | Success | Thiết lập điểm toàn bộ đề thành công                                                                                                                               |
| Hủy bỏ điểm thành công           | Success | Hủy bỏ điểm toàn bộ đề thành công                                                                                                                                  |
| Thêm câu hỏi thành công          | Success | Thêm câu hỏi thành công                                                                                                                                            |
| Sửa câu hỏi thành công           | Success | Sửa câu hỏi thành công                                                                                                                                             |
| Điểm nhập vào <= 0               | Error   | Điểm toàn bộ đề phải lớn hơn 0                                                                                                                                     |
| Điểm câu nhóm vượt mức phân phối | Error   | Tổng điểm của nhóm câu hỏi (X điểm) vượt quá điểm phân phối còn lại của đề thi (Y điểm). Vui lòng điều chỉnh điểm của nhóm câu hỏi hoặc tăng tổng điểm của đề thi. |
| Lỗi hệ thống                     | Error   | Có lỗi xảy ra !!!                                                                                                                                                  |

---

## 11. Xử lý trạng thái Loading

Trong khi hệ thống đang thực hiện cập nhật:

- Ô nhập điểm inline bị **disable** (không cho phép chỉnh sửa)
- Nút "Áp dụng" bị **disable**
- Nút "Xác nhận" trong modal hiển thị trạng thái loading

---

## 12. Câu hỏi mở / Điểm cần làm rõ

| STT  | Câu hỏi                                                                            | Người xử lý        | Trạng thái |
| ---- | ---------------------------------------------------------------------------------- | ------------------ | ---------- |
| Q-01 | Khi tắt tính năng, điểm câu hỏi có nên reset về giá trị trước khi thiết lập không? | BA / Product Owner | Mở         |
| Q-02 | Có giới hạn tối đa cho tổng điểm không?                                            | BA / Product Owner | Mở         |
| Q-03 | Hành vi khi đề không có câu hỏi nào — toggle có được phép bật không?               | Dev / BA           | Mở         |

---

_Tài liệu này được tạo dựa trên phân tích source code tại:_

- _`src/components/Quiz/Detail/ListQuiz/index.tsx`_
- _`src/components/Quiz/Form/Navbar/index.tsx`_
- _`src/components/Quiz/Form/Navbar/NavbarForGroupQuestion.tsx`_
- _`src/hooks/use-distribute-exam-points.ts`_
