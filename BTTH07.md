## Bộ prompt “AI Innovation Day” (CoT \+ Prompt Chaining \+ Context Management)

### 1\. Sơ đồ chuỗi

```
[KHỐI RÀNG BUỘC CỐ ĐỊNH] ─ dán vào MỌI prompt
        │
P1 Khả thi (CoT) → P2 Lịch trình → P3 Ngân sách (CoT) → P4 Phân công → P5 Truyền thông
        │                                                                    │
        └──────────────── P6 Kiểm tra & hiệu chỉnh (CoT) ◄──────────────────┘
                                   │ lỗi → quay lại prompt gây lỗi, sửa rồi chạy lại P6
                                   ▼
                  P7 Tóm tắt ngữ cảnh → dán vào hội thoại mới
```

Khối ràng buộc cố định (dán đầu mọi prompt):

```
<rang_buoc>
Sự kiện: AI Innovation Day | 150 sinh viên (KHÔNG đổi) | 1 ngày | 10 nhân sự (KHÔNG đổi)
Ngân sách tối đa: 15.000.000đ (KHÔNG vượt; dự phòng ≥ 5%)
Mục tiêu: sinh viên hiểu và trải nghiệm AI trong học tập và công việc
Thông tin chưa có thì ghi [giả định] hoặc [cần xác nhận], KHÔNG tự bịa.
</rang_buoc>
```

### 2\. Bộ prompt

P1: Phân tích khả thi (CoT)

```
<rang_buoc>[...]</rang_buoc>
Hãy suy nghĩ từng bước một: (1) chi phí/đầu người, (2) tỷ lệ nhân sự/sinh viên, (3) khả thi về thời gian và địa điểm, (4) liệt kê rủi ro kèm mức độ và biện pháp. Ghi công thức và số cụ thể ở mỗi bước.
Trả về: Nhật ký suy luận → Bảng rủi ro → Kết luận (Khả thi/Có điều kiện/Không) + danh sách điều kiện.
```

P2: Lịch trình (nhận output P1)

```
<rang_buoc>[...]</rang_buoc> <input>[OUTPUT P1]</input>
Lập lịch 1 ngày theo khung giờ, gồm: check-in, khai mạc, talk, workshop thực hành, tea break, nghỉ trưa, mini game, bế mạc.
Quy tắc: không chồng giờ; mỗi hoạt động ghi số người tham gia, số khách mời và số BTC cần; có 2 tea break.
Trả bảng: Giờ | Hoạt động | Khách mời | Nhân sự cần | Vật tư cần.
```

P3: Dự toán (CoT) (nhận P1 \+ P2)

```
<rang_buoc>[...]</rang_buoc> <input>[P1 + P2]</input>
Hãy suy nghĩ từng bước: mỗi khoản chi phải truy về một hoạt động trong lịch trình P2. Ghi công thức (đơn giá × số lượng).
Sau cùng cộng tổng và kiểm tra: Tổng ≤ 15.000.000 và dự phòng ≥ 5%. Nếu vượt, nêu khoản cắt rồi tính lại.
Trả bảng: Hạng mục | Công thức | Thành tiền | Tỷ lệ % | Liên kết lịch trình.
```

P4: Phân công (nhận P2 \+ P3)

```
<rang_buoc>[...]</rang_buoc> <input>[P2 + P3]</input>
Phân công đúng 10 người. Mỗi người ghi: vai trò, nhiệm vụ trước/trong sự kiện, khung giờ cao điểm.
Bắt buộc: mỗi hoạt động ở P2 và mỗi khoản chi ở P3 đều có người phụ trách; không ai quá tải hai khung giờ cao điểm trùng nhau.
```

P5: Truyền thông trước sự kiện (nhận P1–P4)

```
<rang_buoc>[...]</rang_buoc> <input>[P1–P4]</input>
Lập kế hoạch truyền thông theo mốc T-21 → T-1: kênh, nội dung, người phụ trách (theo P4), ngân sách (theo P3).
Số đăng ký cho phép vượt 150 để bù vắng mặt, nhưng check-in tối đa 150. Ghi rõ cơ chế này.
```

P6: Kiểm tra & hiệu chỉnh (CoT) (nhận P1–P5)

```
<rang_buoc>[...]</rang_buoc> <input>[P1–P5]</input>
Hãy suy nghĩ từng bước, đối chiếu từng tiêu chí và trả bảng [Tiêu chí | Đạt/Lỗi | Bằng chứng | Cách sửa]:
(1) tổng ngân sách ≤ 15tr, dự phòng ≥ 5%; (2) 150 SV, 10 nhân sự không đổi; (3) mọi khoản chi khớp lịch trình; (4) mọi hoạt động có người phụ trách; (5) số suất ăn uống khớp số người; (6) không chồng giờ; (7) rủi ro P1 đều có biện pháp.
Nếu có lỗi: sửa trực tiếp, in lại phần bị sửa, rồi kiểm tra lại lần 2.
```

P7: Tóm tắt ngữ cảnh (cuối mỗi phiên)

```
Tóm tắt dự án để tiếp tục ở hội thoại mới, ≤ 350 từ, theo đúng 5 mục: 
1. Ràng buộc cố định  2. Quyết định đã chốt (kèm số liệu)  3. Giả định chưa xác nhận  4. Việc còn dở  5. Lệnh tiếp tục.
Không diễn giải lại, chỉ giữ dữ kiện.
```

Quy tắc chuyển hội thoại: mở chat mới, dán bản tóm tắt P7 \+ bảng ngân sách (P3 đã sửa), rồi gõ: *“Dựa trên bản tóm tắt, tiếp tục từ mục ‘Việc còn dở’. Không thay đổi các quyết định đã chốt.”*

---

### 3\. Kết quả chạy thử

> Các giả định (đơn giá, khách mời, địa điểm) là ví dụ, ban tổ chức cần xác nhận.

#### 3.1 Phân tích khả thi

* Bước 1: 15.000.000 ÷ 150 \= 100.000đ/sinh viên, đủ cho sự kiện 1 ngày trong trường.  
* Bước 2: 150 ÷ 10 \= 15 sinh viên/nhân sự, chấp nhận được. Workshop chia 10 nhóm × 15 người.  
* Bước 3: Chương trình khoảng 8 giờ 45 phút với 2 talk và 2 workshop, vừa đủ thời gian.

| Rủi ro | Mức độ | Biện pháp |
| ----- | ----- | ----- |
| Địa điểm phải trả phí | Cao | Mượn hội trường của trường (giả định miễn phí). Nếu phải thuê, cắt quà mini game trước |
| Wi-Fi quá tải 150 thiết bị | Cao | Kiểm tra trước 1 ngày, chuẩn bị 4G dự phòng, in sẵn tài liệu |
| Vắng mặt | Trung bình | Nhận đăng ký \~180, check-in tối đa 150, có danh sách chờ |
| Khách mời hủy | Trung bình | Có 1 người thay thế, thành viên CLB sẵn sàng chia sẻ |
| Công cụ AI giới hạn lượt | Trung bình | Ưu tiên công cụ miễn phí, có 500.000đ dự phòng credit |

Kết luận: Khả thi có điều kiện. Cần (1) địa điểm miễn phí, (2) Wi-Fi ổn định, (3) dùng công cụ AI miễn phí là chính.

#### 3.2 Lịch trình

| Giờ | Hoạt động | Nhân sự cần |
| ----- | ----- | ----- |
| 08:00–08:30 | Check-in, phát thẻ tên | 3 |
| 08:30–08:45 | Khai mạc | 2 |
| 08:45–09:45 | Talk 1 (khách mời 1): AI trong học tập | 2 |
| 09:45–10:00 | Tea break 1 | 3 |
| 10:00–11:30 | Workshop 1: dùng AI hỗ trợ học tập (10 nhóm) | 6 mentor |
| 11:30–13:00 | Nghỉ trưa (sinh viên tự túc, BTC ăn trưa) | 2 |
| 13:00–14:00 | Talk 2 (khách mời 2): AI trong công việc | 2 |
| 14:00–14:15 | Tea break 2 | 3 |
| 14:15–15:45 | Workshop 2: AI Challenge theo nhóm | 6 mentor |
| 15:45–16:30 | Trình bày, chấm (khách mời 3), trao giải | 3 |
| 16:30–16:45 | Bế mạc, khảo sát | 2 |

#### 3.3 Dự toán ngân sách

Quy ước: 165 suất (150 SV \+ 10 BTC \+ 3 khách mời \+ 2 dự phòng).

| Hạng mục | Công thức | Thành tiền (đ) | % | Liên kết |
| ----- | ----- | ----: | ----: | ----- |
| Âm thanh, màn hình/máy chiếu | trọn gói | 2.500.000 | 16,7% | Mọi hoạt động |
| Backdrop, banner, standee | trọn gói | 1.500.000 | 10,0% | Check-in, khai mạc |
| In ấn (thẻ tên, tài liệu, khảo sát, giấy khen) | trọn gói | 1.000.000 | 6,7% | Check-in, bế mạc |
| Tea break 2 lần | 165 × 2 × 12.000 | 3.960.000 | 26,4% | 09:45, 14:00 |
| Cơm trưa BTC \+ khách mời | 15 × 50.000 | 750.000 | 5,0% | 11:30 |
| Quà mini game, giải thưởng | trọn gói | 1.500.000 | 10,0% | 15:45 |
| Quà tri ân 3 khách mời | 3 × 500.000 | 1.500.000 | 10,0% | Talk 1, 2, chấm giải |
| Công cụ AI / credit demo | trọn gói | 500.000 | 3,3% | Workshop 1, 2 |
| Truyền thông (poster, quảng cáo FB) | trọn gói | 800.000 | 5,3% | Kế hoạch T-21 → T-1 |
| Dự phòng | 15.000.000 − 14.010.000 | 990.000 | 6,6% | Rủi ro P1 |
| Tổng |  | 15.000.000 | 100% | Không vượt ngân sách |

#### 3.4 Phân công 10 nhân sự

| \# | Vai trò | Nhiệm vụ chính | Cao điểm |
| ----- | ----- | ----- | ----- |
| 1 | Trưởng BTC | Điều phối tổng, xử lý sự cố, làm việc với nhà trường | Cả ngày |
| 2 | Chương trình | MC, canh giờ, kịch bản | 08:30, 15:45 |
| 3 | Nội dung & khách mời | Liên hệ 3 khách mời, chuẩn bị đề AI Challenge | Talk 1, 2 |
| 4 | Kỹ thuật | Âm thanh, máy chiếu, Wi-Fi, tài khoản AI | Workshop 1, 2 |
| 5 | Hậu cần | Tea break, cơm trưa, bố trí phòng | 09:45, 14:00 |
| 6 | Tài chính | Thu chi, đối chiếu ngân sách, hóa đơn | Trước/sau sự kiện |
| 7 | Truyền thông 1 | Thiết kế, viết bài, teaser | T-21 → T-1 |
| 8 | Truyền thông 2 | Fanpage, trả lời tin nhắn, chụp quay | Cả ngày |
| 9 | Check-in | Đăng ký, danh sách chờ, phát thẻ | 08:00–08:30 |
| 10 | Mentor & khảo sát | Hỗ trợ nhóm workshop, phát/thu khảo sát | Workshop 1, 2 |

Ở workshop, nhân sự 2, 4, 5, 8 cùng làm mentor, đủ 6 mentor/10 nhóm (một mentor phụ trách 1–2 nhóm).

#### 3.5 Kế hoạch truyền thông

| Mốc | Nội dung | Kênh | Phụ trách | Ngân sách |
| ----- | ----- | ----- | ----- | ----- |
| T-21 → T-18 | Teaser “AI Innovation Day”, chốt form đăng ký | Fanpage, TikTok | \#7 |  |
| T-17 → T-14 | Công bố khách mời, mở đăng ký (nhận \~180, giữ 150 chỗ) | Fanpage, group sinh viên, Zalo lớp | \#7, \#8 | 400.000 (quảng cáo) |
| T-13 → T-7 | Nội dung “AI giúp gì cho việc học”, poster tại trường | Fanpage, poster, TikTok | \#7 | 400.000 (in poster, thiết kế) |
| T-6 → T-4 | Đếm ngược, đăng ký sắp đầy, danh sách chờ | Fanpage, Zalo | \#8 |  |
| T-3 → T-1 | Gửi QR check-in, lịch trình, nhắc mang laptop/điện thoại | Email, Zalo | \#9 |  |
| T-0 | Livestream/ảnh trực tiếp | Fanpage, TikTok | \#8 |  |

#### 3.6 Báo cáo kiểm tra & hiệu chỉnh

| Tiêu chí | Kết quả | Bằng chứng / Cách sửa |
| ----- | ----- | ----- |
| Tổng ngân sách ≤ 15tr |  Lỗi (bản nháp 1\) | Tea break 15.000đ/suất → 4.950.000, tổng bằng đúng 15.000.000, dự phòng \= 0 (vi phạm ≥ 5%). Đã sửa: giảm còn 12.000đ/suất → dự phòng 990.000 (6,6%) |
| 150 SV, 10 nhân sự giữ nguyên |  Đạt | Đăng ký \~180 chỉ là danh sách chờ, check-in tối đa 150 |
| Chi phí khớp lịch trình |  Đạt | Mỗi khoản đều có cột “Liên kết” |
| Mọi hoạt động có người phụ trách |  Đạt | Bảng 3.4 |
| Số suất khớp số người |  Đạt | 165 suất tea break, 15 suất cơm trưa (13 người \+ 2 dự phòng) |
| Không chồng giờ |  Đạt | Lịch 08:00 → 16:45 liên tục |
| Rủi ro có biện pháp |  Đạt | Bảng rủi ro 3.1 |
| Điểm còn mở |  | Địa điểm miễn phí chưa xác nhận; nếu phải thuê, cắt quà mini game trước, giữ nguyên tea break |

#### 3.7 Bản tóm tắt ngữ cảnh (dán vào hội thoại mới)

```
<tom_tat_du_an>
1. RÀNG BUỘC: AI Innovation Day | 150 SV (không đổi) | 1 ngày | 10 nhân sự (không đổi) | ≤ 15.000.000đ, dự phòng ≥ 5% | Mục tiêu: hiểu và trải nghiệm AI trong học tập, công việc.
2. ĐÃ CHỐT: 
- Lịch 08:00–16:45: check-in, khai mạc, Talk 1 (08:45), Workshop 1 (10:00–11:30), nghỉ trưa, Talk 2 (13:00), Workshop 2 "AI Challenge" (14:15–15:45), trao giải, bế mạc. 2 tea break (09:45, 14:00).
- Ngân sách: âm thanh 2,5tr | backdrop 1,5tr | in ấn 1,0tr | tea break 3,96tr (165 suất × 2 × 12k) | cơm trưa BTC 0,75tr | quà mini game 1,5tr | quà khách mời 1,5tr | công cụ AI 0,5tr | truyền thông 0,8tr | dự phòng 0,99tr = 15.000.000.
- 10 vai trò: Trưởng BTC, Chương trình, Nội dung & khách mời, Kỹ thuật, Hậu cần, Tài chính, TT1, TT2, Check-in, Mentor & khảo sát.
- Truyền thông T-21 → T-0; nhận ~180 đăng ký, check-in tối đa 150.
3. GIẢ ĐỊNH CHƯA XÁC NHẬN: địa điểm miễn phí; đơn giá tea break 12k, cơm 50k; 3 khách mời nhận quà 500k; công cụ AI miễn phí là chính.
4. VIỆC CÒN DỞ: xác nhận địa điểm, Wi-Fi; chốt tên khách mời; soạn đề AI Challenge; viết nội dung bài đăng.
5. LỆNH TIẾP TỤC: Dựa trên bản tóm tắt, tiếp tục từ mục 4. Không thay đổi mục 1 và 2.
</tom_tat_du_an>
```

