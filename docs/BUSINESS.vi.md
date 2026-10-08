# HFT Simulation Platform — Đặc tả nghiệp vụ

## 1. Mục đích tài liệu

**DRAFT — REQUIRES FINAL OWNER REVIEW**

Tài liệu là nguồn tham chiếu nghiệp vụ chung cho nhóm sáu sinh viên CNTT, giúp review một mô hình V1 nhất quán mà không đòi hỏi kiến thức tài chính chuyên sâu. Kiến trúc, công nghệ và triển khai nằm ngoài phạm vi.

[Bản tiếng Anh](BUSINESS.md) thể hiện cùng quyết định, ID, ví dụ và phạm vi phê duyệt. Bản dịch không tạo ra một lần phê duyệt riêng. Khi quyết định thay đổi, phải cập nhật cả hai bản.

| Nhãn | Ý nghĩa |
|------|---------|
| ESTABLISHED DOMAIN CONCEPT | Kiến thức nghiệp vụ phổ biến; các thị trường thực tế có thể áp dụng chính sách khác nhau. |
| PROJECT V1 RULE — APPROVED | Quyết định OQ-001 đến OQ-014 đã được chủ dự án duyệt, là quy tắc chính thức cho V1. |
| PROJECT V1 RULE — PROPOSED — OWNER REVIEW REQUIRED | Khuyến nghị cụ thể do Codex chọn cho OQ-015 đến OQ-046. Tài liệu dùng chúng để mô tả V1 đề xuất; chủ dự án chưa phê duyệt. |

Mục 32 là sổ quyết định. Giữ ID OQ cũ để truy vết dù mỗi mục đã có một lựa chọn cụ thể. Mục 31 ghi trạng thái từng quy tắc BR. Bản này không còn câu hỏi nhiều lựa chọn chưa được giải quyết.

Quy ước: dấu phẩy phân cách hàng nghìn, dấu chấm phân cách thập phân: `90,000 USDT`, `0.4 BTC`. Giá có đơn vị Quote/Base. Ví dụ giả định đủ vốn và các bên thuộc tài khoản khác nhau, trừ khi nói rõ. **Kịch bản phí bằng không** là cấu hình hai mức phí bằng không, không có nghĩa V1 bỏ tính năng phí. Các con số đều mô phỏng.

## 2. Tổng quan miền sản phẩm

**ESTABLISHED DOMAIN CONCEPT.** Nền tảng kết nối người mua và bán theo quy tắc:

**Người giao dịch → Lệnh → Sổ lệnh → Khớp lệnh → Trade → Settlement → Dữ liệu thị trường / Danh mục**

Đây là luồng ý nghĩa nghiệp vụ, không phải kiến trúc phần mềm. Lệnh (Order) thể hiện ý định; giao dịch đã khớp (Trade) là kết quả thực hiện. Từ chối, hủy hoặc thiếu đối ứng có thể khiến lệnh không tạo Trade nào. Market Order V1 tiêu thụ thanh khoản đang chờ và không nằm lại sổ.

Ví dụ: Bob có SELL đang chờ `1 BTC @ 86,000`. Alice gửi BUY LIMIT chấp nhận đến `90,000`. Hai bên trao đổi `1 BTC` lấy `86,000 USDT` trước phí. Settlement chuyển quyền sở hữu; lịch sử thị trường ghi nhận Trade.

**APPROVED — OQ-001–OQ-014:** V1 là sàn spot mô phỏng đủ vốn, có ba thị trường, Market có bảo vệ giá, Limit GTC, khớp xác định, ngăn tự khớp và phí Maker/Taker cấu hình theo từng Trade.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-040, OQ-041:** V1 có chiến lược tự động và thí nghiệm vị trí hàng chờ, chất lượng thực hiện, độ trễ mô phỏng. Hoãn backtesting dữ liệu lịch sử.

## 3. Ranh giới sản phẩm

**PROJECT V1 RULE — APPROVED — OQ-001, OQ-002, OQ-004, OQ-005, OQ-006, OQ-012.** Chỉ có BTC/USDT, ETH/USDT, SOL/USDT; tài sản BTC, ETH, SOL, USDT mô phỏng; spot đủ vốn; Limit và Market; Limit dùng GTC, Market chỉ thực hiện ngay.

Ngoài V1: tiền thật; lưu ký, sở hữu hay quy đổi tiền mã hóa thật; chuyển khoản ngân hàng; nạp/rút/thanh toán blockchain; kết nối môi giới thật; tích hợp dữ liệu thị trường bên ngoài; triển khai tuân thủ pháp lý thực tế. Không tuyên bố HFT production.

Hoãn vay, đòn bẩy, margin, bán khống, phái sinh, perpetual, futures, options, Quote Asset khác, cặp chéo, Market Buy theo ngân sách Quote, chỉ thị IOC/FOK và các lệnh nâng cao. Lệnh nâng cao gồm Stop, Stop-Limit, Trailing Stop, Iceberg, Pegged và các lệnh điều kiện khác.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-028, OQ-029, OQ-030, OQ-040, OQ-045:** cũng hoãn sửa lệnh, thao tác cancel-and-replace kết hợp, hết hạn tự động, backtesting lịch sử và sửa Trade. Vẫn cho phép hủy riêng rồi gửi lệnh mới riêng. Khả năng mở rộng tương lai không phải quyền triển khai ngay.

## 4. Chủ thể nghiệp vụ

Đây là trách nhiệm nghiệp vụ, không phải thiết kế phân quyền kỹ thuật.

| Chủ thể | Mục tiêu và hoạt động | Cơ sở quyết định |
|---------|----------------------|------------------|
| Human Trader | Quan sát, đặt lệnh đủ vốn, hủy phần còn hiệu lực của mình, xem kết quả. | Quy tắc giao dịch đã duyệt; thông tin đề xuất ở OQ-033, OQ-037. |
| Algorithmic Strategy | Quyết định tự động từ dữ liệu và vốn được giao. | Đề xuất OQ-040, OQ-043. |
| Exchange / Trading Platform | Áp dụng quy tắc khớp, tạo Trade, phản ánh settlement và hoạt động thị trường. | Quy tắc khớp đã duyệt; chi tiết settlement/dữ liệu là đề xuất. |
| Administrator | Chuẩn bị thí nghiệm và cấp vốn mô phỏng; không tạo Trade để đáp ứng lệnh. | OQ-003 đã duyệt; kiểm soát thí nghiệm đề xuất tại OQ-042. |
| Liquidity / Scenario Account | Cung cấp thanh khoản bằng lệnh thông thường có đủ vốn. | OQ-003 đã duyệt; mô hình tham gia đề xuất OQ-036, OQ-043. |

**APPROVED — OQ-003, OQ-005, OQ-010, OQ-011:** tài khoản được admin cấp vốn không được miễn kiểm tra vốn, ưu tiên khớp hay STP.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-043:** mỗi người tham gia thủ công, chiến lược hoặc kịch bản thanh khoản dùng một tài khoản và nguồn vốn riêng trong thí nghiệm. V1 không chia sẻ vốn giữa chiến lược hoặc chuyển tài sản giữa tài khoản. STP không gom các tài khoản khác nhau theo cùng chủ sở hữu con người.

## 5. Tài sản và cặp giao dịch

**ESTABLISHED DOMAIN CONCEPT.** Tài sản cơ sở (Base Asset) được mua/bán; tài sản định giá (Quote Asset) biểu thị giá và thanh toán. Giá trị giao dịch trước phí (Notional) bằng số lượng Base nhân giá.

| Cặp đã duyệt | Base | Quote | Ví dụ trước phí |
|-------------|------|-------|-----------------|
| BTC/USDT | BTC | USDT | BUY `0.25 BTC @ 80,000` chi `20,000 USDT`. |
| ETH/USDT | ETH | USDT | BUY `2 ETH @ 3,000` chi `6,000 USDT`. |
| SOL/USDT | SOL | USDT | SELL `10 SOL @ 150` nhận `1,500 USDT`. |

**APPROVED — OQ-001, OQ-002:** đây là chính xác ba thị trường và bốn tài sản V1. Tên quen thuộc không tạo quyền sở hữu tiền mã hóa thật. Quote chung là USDT; cặp chéo và dữ liệu/venue bên ngoài nằm ngoài V1.

**APPROVED — OQ-003:** tài khoản thường nhận số USDT ban đầu cấu hình được, có thể có BTC/ETH/SOL ban đầu bằng không. Admin được cấp tài sản mô phỏng đã duyệt cho tài khoản thanh khoản/kịch bản. Đây là **Simulation Funding**, không phải Trade, volume, lợi nhuận hay execution. Chỉ lệnh thông thường từ tài khoản đủ vốn mới đưa thanh khoản vào sổ.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-042:** chỉ cấp vốn lúc chuẩn bị thí nghiệm; tài khoản thường bắt đầu bằng USDT cấu hình và Base bằng không. Tài khoản kịch bản có thể được cấp Base. Phải ghi rõ số vốn từng kịch bản; không ngầm chọn một số dư mặc định chung. Mục 25 giải thích giá vốn của tài sản được cấp.

## 6. Lệnh

**ESTABLISHED DOMAIN CONCEPT.** Một Order tạo được không, một hoặc nhiều Trade. BUY chi Quote để nhận Base; SELL giao Base để nhận Quote.

| Đại lượng | Ý nghĩa |
|-----------|---------|
| Original Quantity | Lượng Base đặt ban đầu, không đổi trong vòng đời lệnh V1. |
| Filled Quantity | Tổng lượng Base của các Trade đã khớp cho lệnh. |
| Unexecuted Quantity | Original trừ Filled, gồm cả phần đã kết thúc chưa thực hiện. |
| Active Remaining Quantity | Phần chưa khớp còn được phép giao dịch; bằng không sau khi lệnh kết thúc. |

**APPROVED — OQ-004, OQ-006, OQ-008:** mọi lệnh V1 dùng lượng Base. Limit có giá giới hạn; Market Buy có giá tối đa; Market Sell có giá tối thiểu. “Chi tối đa `100,000 USDT`” không phải Market Buy V1. Biên giá ràng buộc đơn giá khớp; phí được cấp vốn riêng.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-017, OQ-018, OQ-019, OQ-020, OQ-046:** dùng các giá trị phục vụ giảng dạy sau, không coi là quy định của sàn thật:

| Cặp | Bước giá USDT | Khoảng giá USDT, gồm hai biên | Bước lượng = lượng đặt tối thiểu | Lượng tối đa mỗi lệnh |
|-----|--------------|------------------------------|----------------------------------|----------------------|
| BTC/USDT | `0.01` | `0.01`–`1,000,000.00` | `0.000001 BTC` | `100 BTC` |
| ETH/USDT | `0.01` | `0.01`–`1,000,000.00` | `0.00001 ETH` | `1,000 ETH` |
| SOL/USDT | `0.01` | `0.01`–`1,000,000.00` | `0.001 SOL` | `100,000 SOL` |

Áp dụng cho cả giá Limit lẫn biên bảo vệ Market. Số lượng phải dương, đúng bội của bước và trong giới hạn. Không thêm giới hạn notional tối thiểu/tối đa. Giá trị sai bị từ chối trước giao dịch, không âm thầm làm tròn hoặc giảm lệnh. Lượng khớp và dư còn hiệu lực vẫn đúng bước; không kiểm tra một mức tối thiểu khác cho chúng. Phần dư dương không thể nhỏ hơn một bước.

## 7. Hành vi Limit Order

**ESTABLISHED DOMAIN CONCEPT.** BUY LIMIT đặt giá tối đa; SELL LIMIT đặt giá tối thiểu. Đây là biên chấp nhận, không phải yêu cầu phải khớp đúng giá. Tham khảo [Investor.gov — Types of Orders](https://www.investor.gov/introduction-investing/investing-basics/how-stock-markets-work/types-orders).

| Lệnh đến | Lệnh đối ứng đang chờ | Kết quả V1 trước phí |
|----------|----------------------|----------------------|
| BUY LIMIT `1 BTC @ 90,000` | SELL `1 BTC @ 86,000` | Khớp `86,000`; người mua được giá tốt hơn `4,000 USDT`. |
| BUY LIMIT `1 BTC @ 90,000` | SELL `1 BTC @ 91,000` | Không khớp mức này. |
| SELL LIMIT `1 BTC @ 85,000` | BUY `1 BTC @ 87,000` | Khớp `87,000`; người bán được giá tốt hơn `2,000 USDT`. |
| SELL LIMIT `1 BTC @ 85,000` | BUY `1 BTC @ 84,000` | Không khớp mức này. |

**APPROVED — OQ-009, OQ-012:** Limit đến khớp với đối ứng hợp lệ đang chờ theo giá của đối ứng; phần dư nằm lại dưới dạng GTC. Limit có thể khớp ngay, không nhất thiết phải chờ. Có thể không khớp gì; phần dư cũng không được bảo đảm sẽ khớp sau này.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-022, OQ-023:** BUY dự trữ Quote đủ theo giá giới hạn và mức phí lớn hơn; giải phóng dư sau mỗi Trade. Hoãn sửa lệnh (**OQ-028, OQ-029**).

## 8. Hành vi Market Order

**APPROVED — OQ-006, OQ-007, OQ-008, OQ-012:** Market Buy/Sell yêu cầu lượng Base, chỉ khớp ngay trong biên giá tuyệt đối bắt buộc và kết thúc phần dư. BUY lấy Ask từ thấp lên; SELL lấy Bid từ cao xuống. BUY có giá tối đa, SELL có giá tối thiểu; không thay bằng ngưỡng phần trăm trượt giá.

Ví dụ Market Buy `2 BTC`, tối đa `90,000`. Ask đang chờ gồm `1 BTC @ 86,000` và `2 BTC @ 87,000`. Mua `1 BTC` ở mỗi mức, chi `173,000 USDT` trước phí. Giá bình quân là `173,000 / 2 = 86,500`.

Nếu giá tối đa là `86,000`, chỉ mua `1 BTC`; `1 BTC` còn lại kết thúc ngay. Không có thanh khoản hợp lệ thì không tạo Trade và kết thúc không thực hiện. Partial Fill đã xảy ra vẫn giữ nguyên. Phần dư Market không vào sổ; người dùng không cần gửi IOC.

Market Sell `2 BTC`, tối thiểu `80,000`, bán được vào Bid `82,000` và `80,000`, nhưng không vào `79,000`. Bảo vệ giá không bảo đảm đủ lượng hay khớp đúng biên.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-021, OQ-022, OQ-027:** kiểm tra đủ vốn cho toàn bộ lượng được bảo vệ trước khi nhận lệnh, kể cả khi sổ mỏng. Lệnh sai/thiếu vốn là REJECTED. Market hợp lệ kết thúc FILLED nếu đủ lượng, hoặc CANCELLED với lý do MARKET_REMAINDER nếu còn dư, kể cả không khớp gì. Mục 13 quy định ý nghĩa trạng thái.

## 9. Sổ lệnh

**ESTABLISHED DOMAIN CONCEPT.** Sổ lệnh (Order Book) chứa ý định đang chờ còn hiệu lực, không phải lịch sử Trade. Trong V1, chỉ Limit nằm chờ.

| Phía | Giá USDT/BTC | Lượng còn hiệu lực BTC |
|------|--------------|------------------------|
| Ask | `88,000` | `3` |
| Ask | `87,000` | `2` |
| Ask tốt nhất | `86,000` | `1` |
| Bid tốt nhất | `85,500` | `1.5` |
| Bid | `85,000` | `2` |
| Bid | `84,000` | `4` |

Best Bid là Bid cao nhất `85,500`; Best Ask là Ask thấp nhất `86,000`; Spread là `500 USDT/BTC`. Ask `1 BTC` có thể gồm lệnh `0.4` và `0.6 BTC`; tổng hợp mức giá không gộp quyền sở hữu hoặc thứ tự ưu tiên.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-037, OQ-044:** công bố toàn bộ độ sâu tổng hợp theo giá, giá tốt nhất và thông tin lệnh/vị trí hàng chờ của chính người tham gia. Không công bố danh tính và lịch sử từng lệnh của tài khoản khác. Phía rỗng có giá tốt nhất không xác định; thiếu một phía thì Spread không xác định, không phải bằng không. Hủy lệnh có thể đổi báo giá mà không đổi Last Price.

## 10. Quy tắc khớp lệnh

**ESTABLISHED DOMAIN CONCEPT.** Khớp lệnh chọn ý định đối chiều, cùng cặp và đủ điều kiện. Price-Time là một cách phổ biến; [CME giải thích các cách phân bổ khác nhau](https://www.cmegroup.com/education/articles-and-reports/overview-what-makes-ags-markets-work).

**PROJECT V1 RULE — APPROVED — OQ-009, OQ-010:**

1. Giá tốt hơn trước: Ask thấp nhất cho BUY, Bid cao nhất cho SELL.
2. Cùng giá: lệnh có **platform acceptance sequence** sớm hơn trước.
3. Số lượng lớn hơn không được vượt lệnh cùng giá đến trước.
4. Giá Trade là giá của lệnh đang chờ được khớp.

Nền tảng thiết lập thứ tự chấp nhận duy nhất, xác định cho mọi lệnh được nhận. A, B, C có thể nhận `#1001`, `#1002`, `#1003` dù cùng thời gian hiển thị. Timestamp từ client không bao giờ quyết định ưu tiên. Phần dư nằm chờ giữ acceptance sequence ban đầu của lệnh. Cách kỹ thuật tạo sequence không thuộc tài liệu này.

Ví dụ A: Ask A sớm hơn `1 BTC @ 86,000`; Ask B muộn hơn `1 BTC @ 85,000`. BUY LIMIT mới `1 BTC @ 86,000` khớp B trước tại `85,000`.

Ví dụ B: Ask A `0.4 BTC @ 86,000`, sequence `#1001`; Ask B `2 BTC @ 86,000`, sequence `#1002`. BUY LIMIT mới `1 BTC @ 86,000` lấy `0.4` của A rồi `0.6` của B; B còn `1.4 BTC`. Với Bid, chiều giá đảo lại: `85,500` ưu tiên trước `85,000` khi SELL đến.

### Ngăn tự khớp — Self-Trade Prevention

**APPROVED — OQ-011:** khi đối ứng tiếp theo đủ điều kiện thuộc cùng tài khoản với lệnh đến, **hủy phần dư còn hiệu lực của lệnh đang chờ đó**, ghi SELF_TRADE_PREVENTION khác hủy do người dùng, không tạo Trade và tiếp tục tìm đối ứng khác. Không được chỉ bỏ qua rồi giữ lệnh tự sở hữu trong sổ.

Ví dụ BUY của tài khoản A gặp SELL của A `0.4 BTC @ 86,000`, rồi SELL của B `0.6 BTC @ 86,000`. Hủy phần dư SELL của A; chỉ trao đổi `0.6 BTC` với B tạo Trade. Cặp tự khớp bị ngăn tạo **không Trade, không volume, không cập nhật Last Price, không phí**. Các fill trước đó của lệnh bị hủy vẫn giữ nguyên. Hoãn STP theo nhóm chủ sở hữu nhiều tài khoản.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-031:** với mỗi cặp lệnh hợp lệ, lấy lượng Base còn hiệu lực nhỏ hơn để tạo một Trade, rồi tiếp tục đối ứng đang chờ kế tiếp nếu cần. Như vậy ranh giới từng Trade và phí theo Trade đều xác định. Toàn bộ kết quả khớp ngay của một lệnh đến, gồm STP và fills, xảy ra trước hành động lệnh/hủy có hiệu lực tiếp theo. Đây là quy tắc thứ tự nghiệp vụ, không chỉ định cơ chế xử lý kỹ thuật.

## 11. Khớp một phần

**ESTABLISHED DOMAIN CONCEPT.** Filled Quantity cộng dồn các Trade. Xét BUY LIMIT đến `10 BTC @ 90,000`, phí bằng không, tiêu thụ các Ask đã nằm chờ:

| Trade | Lượng | Giá lệnh đang chờ | Đã khớp cộng dồn | Chưa khớp |
|-------|-------|-------------------|------------------|-----------|
| 1 | `4 BTC` | `86,000` | `4 BTC` | `6 BTC` |
| 2 | `3 BTC` | `87,000` | `7 BTC` | `3 BTC` |
| 3 | `3 BTC` | `88,000` | `10 BTC` | `0 BTC` |

Tổng chi `344,000 + 261,000 + 264,000 = 869,000 USDT`; bình quân `86,900`. Đây là giá của Ask đã nằm chờ, không phải giá SELL mới được áp lên một BUY đã nằm chờ từ trước.

**APPROVED — OQ-007, OQ-012:** nếu chỉ Trade 1 xảy ra ngay, Limit GTC còn `6 BTC` nằm tại `90,000`. Market Buy tương đương thì kết thúc phần dư. SELL đến sau khớp BUY đang chờ này tại `90,000`, không theo giới hạn thấp hơn của SELL mới.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-027:** nếu hủy phần dư Limit, filled là `4`, unexecuted là `6`, active remaining là `0 BTC`. CANCELLED vẫn có thể chứa lịch sử Trade hợp lệ.

## 12. Số dư và dự trữ tài sản

**ESTABLISHED DOMAIN CONCEPT.** Available Balance dùng được cho cam kết mới; Locked/Reserved Balance vẫn thuộc tài khoản nhưng đã cam kết. Dự trữ không chuyển quyền sở hữu. Với settlement ngay được đề xuất, tổng mỗi tài sản bằng Available cộng Locked.

**APPROVED — OQ-005, OQ-013:** giao dịch phải đủ vốn và tính phí nhất quán. **Tài sản đã dự trữ cho một lệnh không được đồng thời cấp vốn cho lệnh khác**, kể cả giữa ba thị trường dùng chung USDT.

### Chính sách dự trữ được khuyến nghị

**PROPOSED — OWNER REVIEW REQUIRED — OQ-015, OQ-016, OQ-021, OQ-022, OQ-023, OQ-024, OQ-046.** Phí bằng USDT. BUY trả notional cộng phí; SELL trả phí từ tiền bán. Gọi `q` là lượng Base còn hiệu lực, `b` là biên giá BUY, `rM`/`rT` là tỷ lệ maker/taker dạng phân số. Làm tròn tiền dự trữ **lên 8 chữ số thập phân USDT**:

| Lệnh | Dự trữ cần thiết |
|------|-----------------|
| BUY LIMIT | `q × limit price × (1 + max(rM, rT))`, làm tròn lên. |
| MARKET BUY | `q × maximum execution price × (1 + rT)`, làm tròn lên. |
| SELL LIMIT hoặc MARKET SELL | `q` Base; không giữ thêm USDT vì trừ phí từ tiền bán. |

Phí không âm, tối đa `1%` và cố định trong thí nghiệm (mục 26). BUY LIMIT đủ vốn khi chuyển từ taker sang maker, kể cả maker đắt hơn. Phải dự trữ đủ trước mọi khớp/STP; thiếu Available thì từ chối, không giảm lượng. Không dựa vào việc STP sắp hủy một lệnh cũ để cấp vốn trước cho lệnh đến.

Sau mỗi fill, thanh toán chi phí thực tế, tính lại dự trữ của phần còn hiệu lực và trả ngay phần dư. Lệnh kết thúc thì trả toàn bộ dự trữ chưa dùng. Làm tròn phí xuống theo từng Trade như mục 26 giúp chia nhỏ fill không làm tổng phí vượt mức dự trữ bảo thủ.

### Ví dụ BUY

**Phí bằng không:** có `100,000 USDT`; BUY LIMIT `1 BTC @ 80,000` giữ `80,000`, còn `20,000` Available. Khớp ngay `0.4 BTC` với Ask đang chờ `75,000`, chi `30,000`. Giữ `48,000` cho `0.6 BTC` còn lại; trả dư `2,000`. Available `22,000`, Locked `48,000`, tổng `70,000 USDT`; nhận `0.4 BTC`. Hủy phần dư thì toàn bộ `70,000 USDT` khả dụng. Tiền được trả dư không phải lợi nhuận.

**Có phí:** maker `0.1%`, taker `0.2%` là cấu hình minh họa. BUY trên giữ `80,160`, còn `19,840`. Trade notional `30,000` thêm `60` phí taker. Dự trữ còn `48,096`; trả dư `2,004`. Available `21,844`, Locked `48,096`, tổng `69,940 USDT`. Các mức phí này không phải mặc định bắt buộc.

Market Buy `2 BTC`, tối đa `90,000`, taker `0.2%` cần `180,360 USDT` dù Ask hiện tại rẻ hơn. Biên giá là giới hạn đơn giá; `360` thêm vào dành cho phí, không cho phép mua trên `90,000`.

### Ví dụ SELL

Có `3 BTC`, SELL LIMIT `2 BTC @ 86,000` giữ `2`, còn `1 BTC` Available. Sau đó bán `0.5 BTC` theo giá của SELL đang chờ là `86,000`: còn `1` Available, `1.5 BTC` Locked. Với maker `0.1%`, notional `43,000` trừ phí `43` còn `42,957 USDT`. Hủy phần dư để có `2.5 BTC` Available; không hoàn lại `0.5 BTC` đã bán hay phí đã trả.

## 13. Vòng đời lệnh

**PROJECT V1 RULE — PROPOSED — OWNER REVIEW REQUIRED — OQ-025, OQ-026, OQ-027.** Dùng năm trạng thái quan sát được. NEW không phải trạng thái V1 hiển thị; việc chấp nhận là sự kiện nghiệp vụ. Không có trạng thái lệnh CANCEL_PENDING. Gửi yêu cầu hủy chưa đồng nghĩa hủy thành công.

| Trạng thái | Ý nghĩa | Kết thúc? |
|------------|---------|-----------|
| OPEN | Limit GTC đã nhận, đang chờ, chưa có fill. | Không |
| PARTIALLY_FILLED | Limit GTC đã có fill và còn phần dư đang chờ. | Không |
| FILLED | Filled Quantity bằng Original Quantity. | Có |
| CANCELLED | Phần chưa khớp đã kết thúc; Filled có thể bằng không hoặc dương. | Có |
| REJECTED | Sai điều kiện/vốn trước khi chấp nhận, trước Trade hay tác động STP. | Có |

Kết quả ban đầu: Limit hợp lệ → OPEN, PARTIALLY_FILLED hoặc FILLED; Market hợp lệ → FILLED hoặc CANCELLED; yêu cầu không hợp lệ → REJECTED. Market khớp một phần vẫn giữ lịch sử fill nhưng không kết thúc ở PARTIALLY_FILLED.

Limit đang chờ có các chuyển tiếp: OPEN → PARTIALLY_FILLED/FILLED/CANCELLED; PARTIALLY_FILLED → PARTIALLY_FILLED/FILLED/CANCELLED. Lệnh đã kết thúc không hoạt động lại. Active Remaining bằng Unexecuted khi lệnh còn hiệu lực, bằng không với mọi lệnh kết thúc. Filled vẫn cộng dồn.

Lý do hủy gồm USER_REQUEST, SELF_TRADE_PREVENTION, MARKET_REMAINDER, EXPERIMENT_ENDED; chúng không phải trạng thái mới. MARKET_REMAINDER bao gồm thiếu thanh khoản hoặc chạm biên bảo vệ, kể cả không có fill. Hoãn EXPIRED và chuyển tiếp sửa lệnh (**OQ-028, OQ-030**). Kết thúc thí nghiệm là đề xuất **OQ-035, OQ-042**.

## 14. Hủy lệnh

**ESTABLISHED DOMAIN CONCEPT.** **Cancel Order != Reverse Trade.** Chỉ hủy phần chưa khớp còn hiệu lực. Hủy phần dư không hoàn lại tài sản và phí của execution đã xảy ra.

Ví dụ phí bằng không: BUY `1 BTC` đã mua `0.4 BTC` với `30,000 USDT`; hủy `0.6 BTC` còn lại vẫn giữ giao dịch mua trước đó và chỉ giải phóng dự trữ chưa dùng. FILLED không còn lượng nào để ngăn khớp.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-031:** chấp nhận lệnh, quyết định hủy và kết thúc thí nghiệm tuân theo thứ tự nghiệp vụ có thẩm quyền. Client timestamp không thay thế nó. Một lệnh đến hoàn tất toàn bộ kết quả khớp ngay trước khi hành động lệnh/hủy tiếp theo có hiệu lực. Đây là quy tắc ưu tiên nghiệp vụ, không chỉ định luồng xử lý kỹ thuật.

Fill có hiệu lực trước thì được giữ; hủy sau chỉ dừng lượng còn hiệu lực lúc đó. Hủy có hiệu lực trước thì phần đó không thể khớp sau. Nếu đã FILLED, kết quả là “quá muộn/đã khớp đủ”; nếu đã hủy, là “đã kết thúc”; không sửa lịch sử hoặc trả dự trữ lần hai. Yêu cầu không tồn tại hoặc bị REJECTED không có lệnh hoạt động để hủy. Người tham gia chỉ hủy lệnh thuộc tài khoản mình.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-028, OQ-029:** đổi ý định bằng việc hủy thành công phần dư cũ rồi gửi lệnh mới độc lập. Không có thao tác amend/cancel-and-replace kết hợp. Lệnh mới kiểm tra lại mọi điều kiện và nhận ưu tiên mới; nếu bị từ chối, lệnh cũ không được phục hồi.

## 15. Thời hạn và điều kiện hiệu lực — Time In Force

**ESTABLISHED DOMAIN CONCEPT.** TIF mô tả thời gian duy trì và yêu cầu khớp đủ, tách khỏi loại lệnh. [Investor.gov](https://www.investor.gov/introduction-investing/general-resources/news-alerts/alerts-bulletins/investor-bulletins-14) giải thích các khái niệm phổ biến:

| Khái niệm | Ý nghĩa chung | Trạng thái V1 |
|-----------|---------------|---------------|
| GTC — Good Till Cancelled | Còn hiệu lực đến khi khớp đủ/hủy, theo quy tắc venue. | APPROVED cho mọi Limit. |
| IOC — Immediate Or Cancel | Khớp ngay phần hợp lệ và hủy dư. | Khái niệm nghiệp vụ — chưa hỗ trợ chỉ thị IOC riêng trong V1. |
| FOK — Fill Or Kill | Khớp đủ ngay hoặc không khớp gì. | Khái niệm nghiệp vụ — chưa hỗ trợ trong V1. |

Yêu cầu `5 BTC` chỉ có `3 BTC` hợp lệ ngay: Limit GTC V1 có thể khớp `3`, để `2` chờ; Market V1 khớp `3`, kết thúc `2`. FOK theo khái niệm thị trường sẽ khớp không, không phải khớp `3` rồi đảo ngược.

**APPROVED — OQ-007, OQ-012:** Market vốn đã chỉ thực hiện ngay, không cần người dùng chỉ định IOC. GTC kéo dài đến khi khớp đủ, hủy hoặc gặp quy tắc kết thúc V1 đã xác định.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-030, OQ-035:** không tự hết hạn hay giới hạn tuổi GTC trong thí nghiệm. Kết thúc thí nghiệm hủy phần dư với EXPERIMENT_ENDED, không dùng EXPIRED.

## 16. Trade

**ESTABLISHED DOMAIN CONCEPT.** Trade là trao đổi đã khớp, không phải gửi lệnh, dự trữ, cấp vốn hay cặp tự khớp bị ngăn. Một BUY `2 BTC` có thể tạo hai Trade với SELL `0.5` và `1.5 BTC`.

Ý nghĩa nghiệp vụ của Trade gồm bên mua/bán, cặp, lượng Base, giá khớp, thời gian/thứ tự xảy ra, các lệnh tham gia, vai trò maker/taker và phí từng bên. Đây không phải định nghĩa lưu trữ hay giao diện. Dữ liệu công khai không tiết lộ danh tính đối tác theo **đề xuất OQ-037**.

**APPROVED — OQ-009, OQ-014:** mỗi Trade dùng giá của lệnh đang chờ, xác định bên đang chờ là Maker và bên đến là Taker. Hai bên có cùng lượng và giá nhưng có thể trả phí khác nhau.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-045:** không sửa, xóa, đảo ngược hoặc điều chỉnh ngoại lệ Trade đã xác nhận trong V1. Nếu thí nghiệm không hợp lệ cho nghiên cứu, giữ nguyên và gắn nhãn lịch sử không hợp lệ, kết thúc rồi mở thí nghiệm riêng. Không dùng cấp vốn để che giấu việc sửa Trade.

## 17. Giá khớp

**PROJECT V1 RULE — APPROVED — OQ-009.** **Execution Price = giá của lệnh đang chờ được khớp.** Giá Limit hoặc bảo vệ Market của lệnh đến chỉ xác định đủ điều kiện.

| Lệnh đang chờ | Lệnh đến | Giá khớp |
|---------------|----------|----------|
| 10:00 BUY LIMIT `1 BTC @ 90,000` | 10:01 SELL LIMIT `1 BTC @ 86,000` | `90,000` — giá BUY đang chờ. |
| 10:00 SELL LIMIT `1 BTC @ 86,000` | 10:01 BUY LIMIT `1 BTC @ 90,000` | `86,000` — giá SELL đang chờ. |

Thời gian chỉ minh họa thứ tự đến; acceptance sequence quyết định ưu tiên thực tế. Hai biên tương thích trong khoảng `86,000`–`90,000`, nhưng V1 không chọn trung điểm hay giá lệnh đến. Bên đến có thể được cải thiện giá. Nhiều mức đang chờ tạo execution ở từng mức tương ứng.

BUY đang chờ `90,000` khớp Market Sell có tối thiểu `80,000` tại `90,000`. SELL đang chờ `91,000` không khớp Market Buy có tối đa `90,000`. Phí tách biệt với biên đơn giá.

## 18. Thanh toán giao dịch — Settlement

**ESTABLISHED DOMAIN CONCEPT.** Settlement thực hiện chuyển giao tài sản của từng Trade. **PROPOSED — OWNER REVIEW REQUIRED — OQ-015, OQ-023, OQ-024, OQ-046:** settlement ngay từng Trade; Base nhận được và USDT ròng từ bán dùng được ngay, không có nhóm số dư chờ settlement.

Người mua trả notional cộng phí bằng USDT, nhận đủ Base. Người bán giao lượng Base đó, nhận notional trừ phí. Cả hai phí chuyển vào **Fee Treasury** mô phỏng riêng, thuộc nền tảng trong thí nghiệm, không giao dịch; bắt đầu bằng không và không tính là lợi nhuận chiến lược.

Ví dụ đầy đủ: Alice có `200,000 USDT`, Bob có `3 BTC`, treasury `0`. Bob đặt trước hai SELL `0.5 BTC` và `1.5 BTC`, cùng giá `86,000`. Alice gửi BUY LIMIT `2 BTC @ 90,000`. Phí minh họa Maker `0.1%`, Taker `0.2%`.

| Thời điểm | Tài khoản | Available USDT | Locked USDT | Available BTC | Locked BTC |
|-----------|-----------|----------------|-------------|---------------|------------|
| Trước lệnh | Alice | `200,000` | `0` | `0` | `0` |
| Trước lệnh | Bob | `0` | `0` | `3` | `0` |
| Sau dự trữ | Alice | `19,640` | `180,360` | `0` | `0` |
| Sau dự trữ | Bob | `0` | `0` | `1` | `2` |
| Sau Trade đầu: `0.5 BTC` | Alice | `21,644` | `135,270` | `0.5` | `0` |
| Sau Trade đầu | Bob | `42,957` | `0` | `1` | `1.5` |
| Sau Trade đầu | Fee Treasury | `129` | `0` | `0` | `0` |
| Sau Trade thứ hai: `1.5 BTC` | Alice | `27,656` | `0` | `2` | `0` |
| Sau Trade thứ hai | Bob | `171,828` | `0` | `1` | `0` |
| Sau Trade thứ hai | Fee Treasury | `516` | `0` | `0` | `0` |

Trade 1: notional `43,000`, phí mua `86`, phí bán `43`. Trade 2: notional `129,000`, phí mua `258`, phí bán `129`. Cuối cùng USDT bảo toàn: `27,656 + 171,828 + 516 = 200,000`; BTC: `2 + 1 = 3`. Sau Trade 1 cũng đúng: `21,644 + 135,270 + 42,957 + 129 = 200,000`.

Partial Fill chỉ settlement lượng thực sự khớp. Trả dự trữ không phải chuyển cho đối tác hay thu nhập. Một Trade settlement một lần. Bảo toàn số đơn vị tài sản không bảo đảm định giá danh mục bất biến. Simulation Funding được trình bày riêng khi đối chiếu tổng tài sản thí nghiệm.

## 19. Các khái niệm giá thị trường

**ESTABLISHED DOMAIN CONCEPT.** Last Price là giá Trade gần nhất; Best Bid/Ask mô tả lệnh hiện đang chờ; Average Execution Price bằng tổng notional đã khớp chia lượng Base đã khớp, không gồm phí.

Ví dụ BUY đến khớp `1 BTC @ 86,000`, rồi `2 BTC @ 87,000`. Bình quân hiển thị 8 chữ số là `86,666.66666667`. Sau đó giả định Best Bid `85,500`, Best Ask `88,000`. Last Price `87,000`: cả bốn đại lượng khác nhau.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-034, OQ-044, OQ-046:** chưa có Trade đầu tiên thì Last Price không xác định. Không có fill thì giá bình quân không xác định, không bằng không. Báo giá có giới hạn lượng, không bảo đảm giá thanh lý. Giá suy ra làm tròn gần nhất đến 8 chữ số, trường hợp đúng nửa làm tròn lên (half-up); không thay đổi giá Trade hay số tiền cấp vốn.

## 20. Thanh khoản

**ESTABLISHED DOMAIN CONCEPT.** Thanh khoản (Liquidity) là khả năng giao dịch một lượng mong muốn mà không phải chấp nhận giá kém đi quá nhiều. Độ sâu đo lượng ở các mức giá; Spread hẹp chưa đủ.

Với Market Buy đủ vốn `2 BTC`, tối đa `90,000`, phí bằng không:

| Sổ Ask | Giá trị Quote khớp | Giá bình quân |
|--------|-------------------|---------------|
| Sâu: `5 BTC @ 86,000` | `172,000 USDT` | `86,000` |
| Mỏng: `0.2 BTC @ 86,000`; `0.3 BTC @ 87,000`; `1.5 BTC @ 90,000` | `17,200 + 26,100 + 135,000 = 178,300 USDT` | `89,150` |

Cùng yêu cầu nhưng sổ mỏng tốn thêm `6,300 USDT`. Nếu tối đa `87,000`, chỉ mua `0.5 BTC`, kết thúc `1.5 BTC` còn lại. Đủ tiền không bảo đảm có đối ứng. Sổ quan sát có thể thay đổi trước lúc hành động có hiệu lực.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-036:** tài khoản kịch bản đủ vốn tạo thanh khoản ban đầu bằng lệnh thường; tồn kho hữu hạn, không được tự động bổ sung.

## 21. Trượt giá

**ESTABLISHED DOMAIN CONCEPT.** Trượt giá (Slippage) so sánh execution với một giá tham chiếu đã xác định. Nó liên quan độ sâu, hành động người khác và thời điểm; tách biệt với phí.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-041:** lấy giá đối ứng tốt nhất mà người tham gia thực sự đã quan sát lúc quyết định: Best Ask cho BUY, Best Bid cho SELL. Trượt bất lợi mỗi Base bằng `bình quân − tham chiếu` cho BUY, `tham chiếu − bình quân` cho SELL. Dương là kém hơn, âm là tốt hơn. Phần trăm bằng chênh lệch chia tham chiếu rồi nhân 100. Thiếu tham chiếu hoặc fill thì không xác định.

BUY bình quân `86,500`, Ask tham chiếu `86,000`: bất lợi `500 USDT/BTC`, khoảng `0.5814%`; với `2 BTC`, chi thêm trước phí `1,000 USDT`. SELL bình quân `85,000`, Bid tham chiếu `85,500` cũng bất lợi `500`.

**APPROVED — OQ-008:** phép đo này không thay thế biên bảo vệ giá Market tuyệt đối bắt buộc. Biên có thể ngăn giá xấu bằng việc kết thúc dư; không bảo đảm đủ lượng.

## 22. Tác động thị trường

**ESTABLISHED DOMAIN CONCEPT.** Tác động thị trường (Market Impact) là thay đổi do giao dịch gây ra. Giả định Last Price `86,000`; Ask `0.5 BTC @ 86,000`, `0.5 @ 87,000`, `2 @ 88,000`. Market Buy `2 BTC`, tối đa `88,000`, khớp qua cả ba mức.

Bình quân `(43,000 + 43,500 + 88,000) / 2 = 87,250`. Trade cuối đặt Last Price `88,000`; Ask đó còn `1 BTC`. Nếu ban đầu có ít nhất `2 BTC @ 86,000`, lệnh không cần đến giá cao hơn.

Đây là tác động tức thời, không dự đoán giá vĩnh viễn. Slippage đo execution so với tham chiếu; impact nói về thay đổi do lệnh. Hủy lệnh có thể đổi báo giá mà không đổi Last Price.

## 23. Dữ liệu thị trường

**ESTABLISHED DOMAIN CONCEPT.** Market Data gồm độ sâu sổ, giá tốt nhất, Trade, Last Price, volume và OHLCV. Yêu cầu đặt lệnh, cấp vốn và hủy không phải khối lượng đã giao dịch.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-037, OQ-038, OQ-044:** cung cấp toàn bộ độ sâu tổng hợp, Best Bid/Ask/Spread, lịch sử Trade ẩn danh, Last Price và volume theo cả Base lẫn notional USDT trước phí. Mỗi Trade thực tế được đếm một lần theo từng cặp, trong mỗi phút và tổng lũy kế thí nghiệm. Phí tách riêng, không tăng Quote volume. V1 không có thống kê 24 giờ trượt.

Người tham gia xem được lệnh của mình, ưu tiên acceptance, số lệnh và lượng cùng giá đứng trước, fill, phí, số dư và lý do kết thúc. Hàng chờ tính mọi lệnh đang hoạt động cùng giá có ưu tiên sớm hơn; đây là quan sát, không bảo đảm fill tương lai. Danh tính/lịch sử từng lệnh của tài khoản khác không công khai.

**APPROVED — OQ-003, OQ-011:** Simulation Funding và cặp tự khớp bị ngăn không tạo Trade/volume/Last Price. STP có thể đổi độ sâu do hủy lệnh đang chờ. Tích hợp dữ liệu ngoài nằm ngoài V1 (**OQ-002**).

## 24. Nến và OHLCV

**ESTABLISHED DOMAIN CONCEPT.** Open/High/Low/Close lần lượt là giá Trade đầu/cao nhất/thấp nhất/cuối trong khoảng. Volume cộng lượng khớp thực, mỗi Trade một lần.

| Thời gian thí nghiệm | Giá USDT/BTC | Lượng BTC | Notional USDT |
|---------------------|--------------|-----------|---------------|
| 10:00:05 | `86,000` | `1` | `86,000` |
| 10:00:20 | `88,000` | `0.5` | `44,000` |
| 10:00:35 | `85,000` | `2` | `170,000` |
| 10:00:50 | `87,000` | `0.5` | `43,500` |

Phút đó có Open `86,000`, High `88,000`, Low `85,000`, Close `87,000`, Base Volume `4 BTC`, Quote Volume `343,500 USDT`. Biểu đồ suy ra từ Trade, không quyết định giá. Close không phải bình quân.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-035, OQ-039:** chỉ có nến một phút, dùng đồng hồ thí nghiệm ảo của nền tảng, bắt đầu `00:00:00 UTC` vào ngày kịch bản được khai báo. Khoảng gồm đầu nhưng không gồm cuối: Trade `10:01:00` thuộc phút kế tiếp. Các Trade cùng thời gian theo thứ tự thực hiện nghiệp vụ có thẩm quyền.

Phút rỗng có OHLC không xác định, volume bằng không; không sao chép giá cũ thành nến giả. Last Price riêng vẫn có thể giữ Trade cuối thực tế. Cuối thí nghiệm giữ và gắn nhãn khoảng cuối chưa đủ phút. Khi đồng hồ thí nghiệm dừng, thời gian không trôi; giờ client không chia nhóm Trade hay quyết định ưu tiên.

## 25. Danh mục và PnL

**ESTABLISHED DOMAIN CONCEPT.** Portfolio mô tả tài sản nắm giữ; Position là lượng nắm giữ; Cost Basis là giá vốn phân bổ; Realized PnL là lãi/lỗ của lượng đã bán; Unrealized PnL là định giá lãi/lỗ lượng còn giữ. Tài sản Locked vẫn nằm trong danh mục.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-033, OQ-034, OQ-046:** V1 báo cáo Available/Locked/Total, vị thế spot, giá vốn bình quân, PnL đã/chưa thực hiện từng Base bằng USDT, phí và tổng equity USDT khi định giá được mọi tài sản có số dư khác không. Chọn duy nhất **giá vốn bình quân gia quyền**; hoãn kế toán tax-lot/FIFO.

BUY cộng notional và phí mua vào tổng giá vốn. SELL loại phần vốn `giá vốn trước bán × lượng bán / lượng trước bán`, làm tròn xuống 8 chữ số USDT; bán hết thì loại toàn bộ vốn còn lại. Realized PnL bằng tiền bán ròng trừ vốn đã loại. Phần lẻ làm tròn ở lại vị thế, không biến mất. Giá vốn bình quân bằng tổng vốn chia lượng; không xác định khi số lượng bằng không.

Phí bằng không: mua `1 BTC @ 80,000` và `1 @ 90,000`, vốn `170,000`, bình quân `85,000`. Bán `0.5 @ 88,000`: realized `1,500`, vốn còn `127,500`. Last Price `87,000` cho unrealized `130,500 − 127,500 = 3,000 USDT`.

Nếu cả ba execution đều là taker với phí `0.2%`: vốn `170,340`, bình quân `85,170`; bán `0.5` nhận ròng `43,912`, loại vốn `42,585`, realized `1,327`. Vốn còn `127,755`, unrealized `2,745` tại cùng Last Price. Phí là cấu hình kịch bản, không phải mặc định.

Base được cấp ban đầu có giá tham chiếu vốn khai báo rõ, đúng bước/khoảng giá của cặp; vốn ban đầu bằng lượng nhân giá đó. Việc cấp tăng vốn được tài trợ, không tạo trading PnL hay Last Price. Cấp USDT cũng không phải lợi nhuận. Báo cáo tách vốn cấp, realized, unrealized và phí; phí đã nằm trong vốn/tiền bán không được trừ lần hai.

Định giá theo Last Price của thí nghiệm, không thay bằng tham chiếu chuẩn bị. Chưa có Trade đầu tiên thì định giá/unrealized của Base có lượng khác không và tổng equity chưa xác định; realized và số dư vẫn xem được. Last Price cũ hơn `60` giây thí nghiệm được giữ nhưng gắn cờ stale cùng tuổi giá. Đây là định giá tham khảo, không bảo đảm tiền thanh lý; không trừ phí bán giả định trong unrealized.

## 26. Phí

**PROJECT V1 RULE — APPROVED — OQ-013, OQ-014.** Hỗ trợ phí Maker/Taker cấu hình được, kể cả bằng không. Phân loại **từng Trade**: lệnh đang chờ là Maker, lệnh đến là Taker; hai tỷ lệ có thể bằng hoặc khác nhau. Phí phải nhất quán trong cấp vốn, dự trữ, settlement, số dư và hiệu quả giao dịch.

Ví dụ SELL đang chờ `1 BTC @ 86,000`; BUY LIMIT đến `2 BTC @ 90,000`. Trade 1 khớp `1 BTC @ 86,000`: BUY Taker, SELL Maker. BUY dư `1 BTC` nằm tại `90,000`. SELL mới đến khớp nó ở `90,000`: chính BUY đó lúc này là Maker. Limit không mặc nhiên làm Maker ở mọi fill.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-015, OQ-016:** phí hai bên bằng USDT, chuyển vào Fee Treasury. Mỗi bên của mỗi Trade trả `notional × tỷ lệ vai trò`, **làm tròn xuống 8 chữ số USDT**. Không phí tối thiểu, rebate, chiết khấu, token phí riêng hoặc tính gộp phí theo Order. Phí rất nhỏ có thể về không, không có khoản làm tròn ẩn. Bên trả mất đúng số treasury nhận.

Bắt buộc khai báo cả maker/taker lúc chuẩn bị, mỗi mức từ `0%` đến `1%`, bước `0.01` điểm phần trăm (`0.0001` dạng phân số). Giữ cố định cả thí nghiệm để không làm thiếu dự trữ lệnh cũ. Cho phép bằng không; không có mức phí mặc định ngầm.

Notional `86,000`: maker `0.1%` thu `86`, taker `0.2%` thu `172 USDT`. Nếu người bán Maker và mua Taker: mua trả `86,172`, bán nhận `85,914`, treasury nhận `258`. Phí Quote tối đa `1%` luôn được trang trải bằng tiền bán. Quy tắc thập phân và dự trữ ở mục 6, 12 và OQ-046.

## 27. Nhà tạo lập thị trường

**ESTABLISHED DOMAIN CONCEPT.** Market Maker cung cấp ý định mua và bán. Bid `85,500`, Ask `86,000` tạo thanh khoản nhưng Spread `500` không phải lợi nhuận chắc chắn: có thể chỉ một phía khớp, tồn kho tăng, giá và phí thay đổi kết quả.

**APPROVED — OQ-003:** admin cấp tài sản cho tài khoản riêng; tài khoản phải giao dịch qua sổ. Cấm admin tạo Trade trực tiếp, ép khớp hoặc sửa Last Price trực tiếp.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-036, OQ-043:** tạo thanh khoản ban đầu bằng các Limit đã khai báo của tài khoản kịch bản đủ vốn, gửi theo thứ tự hành động khi thí nghiệm đã chạy. Chỉ cấp vốn lúc chuẩn bị thì sổ vẫn rỗng. Các tài khoản này cũng có thể chạy chiến lược tự động thí nghiệm theo cùng quy tắc. Không cung cấp Maker luôn hoạt động được bảo đảm, tự bổ sung tồn kho hay ưu tiên riêng. Không thiết kế chiến lược tạo lập thích nghi trong tài liệu này. Sổ rỗng hoặc chỉ có một phía là kết quả hợp lệ.

## 28. Giao dịch thuật toán

**ESTABLISHED DOMAIN CONCEPT.** Algorithmic Trading tự động quyết định đặt, hủy hoặc không hành động. Quantitative Trading dùng bằng chứng định lượng. Exchange Matching xác định các lệnh đã gửi được khớp thế nào theo quy tắc venue. Chiến lược yêu cầu `2 BTC` phải đánh giá fill thực, không mặc định đã mua `2 BTC`.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-040:** V1 cho chiến lược dùng dữ liệu thị trường mô phỏng và số dư riêng để đặt/hủy lệnh V1 bình thường. Có thể chạy lại kịch bản tổng hợp trong thí nghiệm mới với vốn, phí, lịch hành động và giả định thời gian được khai báo. Tái lập đòi hỏi cùng kịch bản và quyết định của người tham gia; thay chiến lược là thay đầu vào thí nghiệm.

Hoãn backtesting lịch sử, tích hợp dữ liệu/venue ngoài, bộ tối ưu hóa và chợ chiến lược. Backtesting vẫn là khái niệm học tập hữu ích: đánh giá trên dữ liệu quá khứ phải nêu giả định fill, thanh khoản, phí, thời gian. Chạy lại kịch bản tổng hợp không chứng minh hiệu quả trên thị trường lịch sử.

## 29. HFT

**ESTABLISHED DOMAIN CONCEPT.** HFT liên quan quyết định tự động thường xuyên, nhạy độ trễ, vị trí hàng chờ và chất lượng thực hiện; không đơn giản là “bot giao dịch nhanh”. [Tổng quan nghiên cứu HFT lịch sử của SEC](https://www.sec.gov/marketstructure/research/hft_lit_review_march_2014.pdf) cung cấp nền tảng khái niệm.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-041:** mỗi người tham gia có độ trễ quan sát và hành động không âm khai báo rõ, cố định lúc chuẩn bị cho cả thí nghiệm. So sánh cấu hình trễ khác nhau ở các thí nghiệm riêng; hoãn dao động thời gian ngẫu nhiên. Mọi độ trễ dùng đồng hồ thí nghiệm ảo. Trễ quan sát ảnh hưởng dữ liệu người tham gia đã thấy; trễ hành động ảnh hưởng thời điểm lệnh/hủy đến thứ tự nghiệp vụ của nền tảng. Timestamp của người tham gia hay tốc độ cấu hình không vượt quyền ưu tiên đã có.

Quan sát theo lệnh: tỷ lệ khớp `filled / original`, giá khớp bình quân theo lượng, slippage so với báo giá quan sát lúc quyết định, phí thực, chi phí mua/tiền bán ròng mỗi đơn vị có tính phí, thời gian đến fill đầu/khớp đủ và kết quả hủy có hiệu lực. Chưa có fill đầu/chưa đủ thì chỉ số thời gian tương ứng không xác định, không bằng không. Quan sát thêm số lệnh/lượng cùng giá đứng trước và PnL danh mục. Thời gian dùng khoảng trên đồng hồ thí nghiệm; thời gian hiển thị bằng nhau vẫn theo thứ tự nghiệp vụ.

Ví dụ A và B cùng đặt `1 BTC @ 86,000`, A sớm hơn. BUY `0.5 BTC` khớp A, B không được fill. Đổi độ trễ của B trong thí nghiệm khác có thể đổi thứ tự chấp nhận, nhưng không sửa ưu tiên ngược về quá khứ.

Đây là nền tảng mô phỏng/nghiên cứu HFT, không phải venue HFT production. Benchmark mạng/phần cứng thật và cam kết latency production ngoài phạm vi V1. Báo cáo độ trễ tổng hợp và kết quả giao dịch là kết quả mô phỏng, không là lợi nhuận thực hay hiệu năng hạ tầng thực.

## 30. Bất biến nghiệp vụ

Phân biệt tính nhất quán chung với chính sách V1. Chi tiết đề xuất không tự được duyệt khi diễn đạt như yêu cầu của mô hình khuyến nghị.

| ID | Bất biến | Cơ sở |
|----|----------|-------|
| ORD-INV-001 | `0 ≤ filled ≤ original`; lượng ban đầu không đổi trong V1. | Khái niệm chung; không sửa lệnh là đề xuất OQ-028. |
| ORD-INV-002 | `original = filled + unexecuted`; active remaining bằng không khi kết thúc. | Khái niệm chung; thuật ngữ đề xuất OQ-027. |
| ORD-INV-003 | Execution tuân thủ cả hai biên áp dụng, gồm bảo vệ Market. | APPROVED OQ-008, OQ-009. |
| ORD-INV-004 | Hủy có hiệu lực chặn fill tương lai của phần dư; fill cũ giữ nguyên. | Khái niệm chung; thứ tự đề xuất OQ-031. |
| BAL-INV-001 | Tài sản dự trữ không cấp vốn cho lệnh khác, kể cả khác cặp. | Khái niệm chung; đủ vốn APPROVED OQ-005. |
| BAL-INV-002 | Giữ/trả dự trữ chỉ đổi khả dụng, không đổi tổng sở hữu. | Khái niệm chung. |
| BAL-INV-003 | Trả dự trữ chưa dùng không hoàn tài sản đã trao đổi hay phí đã trả. | Khái niệm chung. |
| TRD-INV-001 | Trade đã xác nhận là sự kiện lịch sử bất biến. | Khái niệm chung; cấm sửa là đề xuất OQ-045. |
| TRD-INV-002 | Hai bên thống nhất cặp, lượng Base và giá lệnh đang chờ. | Khái niệm chung; giá APPROVED OQ-009. |
| TRD-INV-003 | Mỗi Trade đóng góp lượng đúng một lần vào filled của mỗi lệnh tham gia. | Khái niệm chung. |
| STL-INV-001 | Settlement nhất quán sở hữu ở mọi bên, gồm bên nhận phí. | Khái niệm chung; treasury USDT đề xuất OQ-015, OQ-024. |
| STL-INV-002 | Giao dịch bảo toàn từng tài sản giữa người tham gia và Fee Treasury; tách Simulation Funding. | Khái niệm chung; nơi nhận phí là đề xuất. |
| STL-INV-003 | Chuyển tài sản/phí của một Trade áp dụng một lần, không thêm lần vì được báo lại. | Khái niệm chung. |
| MKT-INV-001 | Last Price chỉ phản ánh Trade thực gần nhất theo thứ tự execution. | Khái niệm chung; khởi tạo đề xuất OQ-044. |
| MKT-INV-002 | Volume đếm mỗi Trade một lần, không một lần cho mỗi đối tác. | Khái niệm chung; khoảng đo đề xuất OQ-038. |
| ORD-INV-005 | Cùng giá ưu tiên theo acceptance sequence, không theo client time hay lượng lớn. | APPROVED OQ-010. |
| TRD-INV-004 | Không Trade giữa lệnh cùng tài khoản; STP hủy dư đang chờ, cặp bị ngăn không có phí/volume/Last Price. | APPROVED OQ-011. |
| BAL-INV-004 | Cam kết được nhận không đòi tài sản không khả dụng; Available/Locked không âm. | Đủ vốn đã APPROVED; công thức đề xuất OQ-021, OQ-022. |
| SIM-INV-001 | Cấp vốn không là execution, volume, lợi nhuận và không tự tạo giá thị trường. | APPROVED OQ-003. |

## 31. Quy tắc nghiệp vụ và trạng thái phê duyệt

Giữ các ID BR hiện có. Quy tắc chứa lựa chọn mới vẫn là **PROPOSED — OWNER REVIEW REQUIRED**, dù áp dụng nguyên tắc đã duyệt. Dòng APPROVED chỉ dẫn quyết định chủ dự án đã duyệt.

| ID | Quy tắc | Lý do | Trạng thái | Quyết định |
|----|---------|-------|------------|------------|
| BR-ORD-001 | Giá tốt hơn, rồi acceptance sequence sớm hơn; không ưu tiên theo client time hay lượng lớn. | Thứ tự xác định. | APPROVED | OQ-010 |
| BR-ORD-002 | Limit GTC; Market chỉ ngay và không để dư trong sổ; hoãn IOC/FOK riêng. | Tách loại khỏi hiệu lực. | APPROVED | OQ-007, OQ-012 |
| BR-ORD-003 | Dùng OPEN, PARTIALLY_FILLED, FILLED, CANCELLED, REJECTED và chuyển tiếp/lý do ở mục 13. | Đủ mô tả kết quả, tránh trạng thái thừa. | PROPOSED — OWNER REVIEW REQUIRED | OQ-025, OQ-026, OQ-027 |
| BR-BAL-001 | Chỉ spot mô phỏng đủ vốn; không tín dụng, đòn bẩy, bán khống, phái sinh. | Đủ tài sản cho nghĩa vụ. | APPROVED | OQ-002, OQ-005 |
| BR-BAL-002 | BUY giữ cả phí, SELL giữ Base theo mục 12. | Đủ vốn cho bảo vệ giá và đổi maker/taker. | PROPOSED — OWNER REVIEW REQUIRED | OQ-015, OQ-016, OQ-022, OQ-046 |
| BR-BAL-003 | Tính lại và trả dư sau mỗi Trade; trả phần chưa dùng khi kết thúc. | Số dư khả dụng đúng nghĩa vụ còn lại. | PROPOSED — OWNER REVIEW REQUIRED | OQ-023, OQ-024 |
| BR-BAL-004 | Thiếu toàn bộ vốn thì từ chối trước acceptance/STP; không tự giảm lượng. | Tránh nghĩa vụ không thanh toán được. | PROPOSED — OWNER REVIEW REQUIRED | OQ-021 |
| BR-TRD-001 | Không sửa, xóa, đảo ngược hay điều chỉnh Trade trong V1. | Giữ lịch sử giải thích được. | PROPOSED — OWNER REVIEW REQUIRED | OQ-045 |
| BR-STL-001 | Settlement ngay từng Trade một lần, gồm phí; lượng chưa khớp không chuyển tài sản. | Nhất quán sở hữu khả dụng. | PROPOSED — OWNER REVIEW REQUIRED | OQ-024, OQ-046 |
| BR-MKT-001 | Last Price/tổng hợp Trade từ execution mô phỏng thật; không thay bằng giá khởi tạo/ngoài. | Tách đầu vào khỏi execution. | PROPOSED — OWNER REVIEW REQUIRED | OQ-038, OQ-039, OQ-044 |
| BR-MKT-002 | Toàn bộ độ sâu tổng hợp và quan sát hàng chờ tài khoản mình; không lộ danh tính/lịch sử tài khoản khác. | Hỗ trợ nghiên cứu hàng chờ. | PROPOSED — OWNER REVIEW REQUIRED | OQ-037 |
| BR-PF-001 | Báo số dư, vị thế, giá vốn bình quân có phí, PnL đã/chưa thực hiện. | Một phương pháp vốn hữu ích. | PROPOSED — OWNER REVIEW REQUIRED | OQ-033, OQ-034 |
| BR-ACT-001 | Tài khoản admin cấp dùng quy tắc vốn, khớp và ngăn tự khớp thông thường. | Không ép đối ứng ưu tiên. | APPROVED | OQ-003, OQ-005, OQ-010, OQ-011 |
| BR-ORD-004 | Chỉ Limit và Market có bảo vệ; BUY/SELL đều dùng lượng Base. | Ý định và biên rõ ràng. | APPROVED | OQ-004, OQ-006, OQ-008 |
| BR-ORD-005 | Khớp theo giá lệnh đang chờ được chọn. | Giải thích cải thiện giá nhất quán. | APPROVED | OQ-009 |
| BR-ORD-006 | Hủy phần dư tự sở hữu đang chờ, không tạo tự Trade, tiếp tục lệnh đến. | STP cấp tài khoản có lịch sử riêng. | APPROVED | OQ-011 |
| BR-FEE-001 | Cấu hình Maker/Taker kể cả không phí; phân loại đang chờ/đến từng Trade. | Một Order có thể đổi vai trò. | APPROVED | OQ-013, OQ-014 |
| BR-FEE-002 | Mỗi bên trả USDT vào treasury; phí xuống 8 chữ số, không tối thiểu/rebate; tỷ lệ có giới hạn, cố định mỗi thí nghiệm. | Phí chính xác và đủ vốn. | PROPOSED — OWNER REVIEW REQUIRED | OQ-015, OQ-016 |
| BR-MKT-003 | Chính xác BTC/USDT, ETH/USDT, SOL/USDT và chỉ BTC/ETH/SOL/USDT mô phỏng. | Ranh giới thị trường rõ. | APPROVED | OQ-001, OQ-002 |
| BR-SIM-001 | USDT đầu kỳ cấu hình; cấp tài sản đã duyệt cho kịch bản bằng Simulation Funding, không tạo Trade trực tiếp. | Tạo vốn thanh khoản không bịa execution. | APPROVED | OQ-003 |
| BR-PREC-001 | Áp dụng biên giá/lượng mục 6, mọi tài sản chính xác 8 chữ số và quy tắc làm tròn mục 32. | Lượng xác định, không thất thoát vô cớ. | PROPOSED — OWNER REVIEW REQUIRED | OQ-017, OQ-018, OQ-019, OQ-020, OQ-046 |
| BR-ORD-007 | Hủy riêng rồi lệnh mới ưu tiên mới; không amend, replace kết hợp, expiry. | Cam kết ổn định. | PROPOSED — OWNER REVIEW REQUIRED | OQ-028, OQ-029, OQ-030, OQ-031, OQ-032 |
| BR-SIM-002 | Thí nghiệm hữu hạn, chỉ cấp vốn/cấu hình lúc chuẩn bị; kết thúc hủy dư và giữ lịch sử. | Reset thành thí nghiệm riêng. | PROPOSED — OWNER REVIEW REQUIRED | OQ-035, OQ-042, OQ-045 |
| BR-ACT-002 | Tài khoản đủ vốn riêng mỗi chủ thể; độ sâu đầu kỳ bằng Limit kịch bản thông thường. | Thanh khoản/vốn hữu hạn truy được. | PROPOSED — OWNER REVIEW REQUIRED | OQ-036, OQ-043 |
| BR-MKT-004 | Volume Base/USDT từng phút và lũy kế thí nghiệm; nến một phút; phút rỗng không có OHLC. | Tổng hợp thị trường nhất quán. | PROPOSED — OWNER REVIEW REQUIRED | OQ-038, OQ-039 |
| BR-ALGO-001 | Có chiến lược/thí nghiệm hàng chờ, timing tổng hợp và đo execution quality; hoãn backtesting lịch sử. | Giá trị nghiên cứu trong phạm vi vừa sức. | PROPOSED — OWNER REVIEW REQUIRED | OQ-040, OQ-041 |

## 32. Sổ quyết định nghiệp vụ

OQ-001 đến OQ-014 đã được chủ dự án duyệt. OQ-015 đến OQ-046 mỗi mục có một khuyến nghị cụ thể cần duyệt cuối. Dòng đề xuất nhắc lại điều đã duyệt, như hoãn Stop Order, không mở lại quyết định đó. Giá trị cấu hình của kịch bản là tham số có chủ đích, không phải câu hỏi chính sách còn bỏ ngỏ.

| ID | Quyết định | Lý do | Trạng thái |
|----|------------|-------|------------|
| OQ-001 | Chính xác BTC/USDT, ETH/USDT, SOL/USDT; USDT là Quote chung. Hoãn Quote khác và cặp chéo. | Cố định phạm vi thị trường. | APPROVED |
| OQ-002 | BTC, ETH, SOL, USDT chỉ là đơn vị mô phỏng; không lưu ký, sở hữu, nạp, rút, quy đổi tài sản thật hay settlement blockchain. Tích hợp dữ liệu/venue ngoài không thuộc V1. | Sàn hoàn toàn mô phỏng. | APPROVED |
| OQ-003 | USDT ban đầu tài khoản thường cấu hình được; Base có thể bằng không. Admin cấp tài sản đã duyệt cho tài khoản thanh khoản/kịch bản riêng. Simulation Funding không phải Trade, volume, lợi nhuận, execution; tài khoản được cấp vẫn theo quy tắc, không ép fill bỏ qua sổ. | Cấp vốn không tạo hoạt động giao dịch giả. | APPROVED |
| OQ-004 | Chỉ Limit và Market; hoãn Stop, Stop-Limit, Trailing Stop, Iceberg, Pegged và lệnh nâng cao/điều kiện khác. TIF tách riêng. | Phạm vi loại lệnh rõ. | APPROVED |
| OQ-005 | Chỉ spot mô phỏng đủ vốn: BUY dùng Quote khả dụng; SELL dùng Base sở hữu khả dụng. Không vay, đòn bẩy, margin, bán khống, phái sinh, perpetual, futures, options. | Không nghĩa vụ thiếu vốn. | APPROVED |
| OQ-006 | Market Buy/Sell chỉ định lượng Base. Market Buy yêu cầu nhận tối đa lượng đó; hoãn mua theo ngân sách Quote. Vốn/bảo vệ/kết thúc theo quy tắc riêng. | Ý định lượng rõ ràng. | APPROVED |
| OQ-007 | Market lấy thanh khoản hợp lệ ngay, giữ Partial Fill và kết thúc mọi phần dư không nằm sổ. Không có thanh khoản hợp lệ thì không có Trade/execution. Limit GTC có thể giữ dư hoạt động. | Không đảo fill vì thiếu thanh khoản. | APPROVED |
| OQ-008 | Market Buy bắt buộc giá tối đa tuyệt đối; Market Sell bắt buộc giá tối thiểu tuyệt đối. Chỉ khớp trong biên và kết thúc dư. Không thay bằng phần trăm. | Bảo vệ giá cụ thể. | APPROVED |
| OQ-009 | Trade theo giá lệnh đang chờ được khớp; biên của lệnh đến chỉ là giới hạn. Nhiều mức đang chờ có thể tạo nhiều giá Trade. | Giá xác định, có cải thiện. | APPROVED |
| OQ-010 | Price-Time: giá tốt hơn rồi acceptance sequence duy nhất có thẩm quyền của nền tảng; lượng lớn không vượt. Client timestamp không quyết định ưu tiên, kể cả cùng thời gian hiển thị. Không quy định kỹ thuật tạo sequence. | Thứ tự xác định, giải thích được. | APPROVED |
| OQ-011 | Ngăn cùng tài khoản bằng hủy dư tự sở hữu đang chờ đủ điều kiện, ghi STP riêng và tiếp tục lệnh đến. Cặp bị ngăn không có Trade/volume/Last Price/phí. Hoãn nhóm chủ sở hữu nhiều tài khoản. | Loại tự đối ứng không bịa execution. | APPROVED |
| OQ-012 | Limit chỉ GTC; Market vốn chỉ thực hiện ngay và kết thúc dư. Hoãn IOC/FOK riêng; Market không cần IOC. GTC chỉ kết thúc do khớp đủ/hủy/quy tắc kết thúc V1 xác định khác. | Tách Order Type khỏi TIF. | APPROVED |
| OQ-013 | Có phí mô phỏng cấu hình được, kể cả bằng không; nhất quán vốn, dự trữ, settlement, số dư, kết quả chiến lược và PnL được hỗ trợ. | Chi phí đối chiếu được xuyên suốt. | APPROVED |
| OQ-014 | Maker/Taker cấu hình bằng hoặc khác nhau. Từng Trade: bên đang chờ Maker, bên đến Taker; cùng Order có thể đổi vai trò ở execution sau. | Phân loại theo đóng góp thanh khoản. | APPROVED |
| OQ-015 | Hai bên trả USDT vào Fee Treasury nền tảng riêng trong thí nghiệm, không giao dịch và ban đầu bằng không. BUY cộng phí ngoài notional, SELL trừ phí từ tiền bán. | Một tài sản phí, sở hữu rõ. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-016 | Tính từng bên từng Trade: notional × tỷ lệ, xuống 8 chữ số USDT; không phí tối thiểu, rebate, giảm giá hay phí gộp Order. Bắt buộc maker/taker mỗi mức 0%–1%, bước 0.01 điểm phần trăm, cố định cả thí nghiệm. | Phí xác định, đủ từ tiền bán/dự trữ. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-017 | Cả ba cặp bước giá 0.01 USDT, khoảng gồm biên 0.01–1,000,000.00, kể cả bảo vệ Market. Từ chối giá sai bước/ngoài khoảng. | Lưới giá giảng dạy đơn giản. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-018 | Bước lượng: BTC 0.000001, ETH 0.00001, SOL 0.001 đơn vị Base. Từ chối sai bước, không làm tròn yêu cầu. | Lượng chia được chính xác. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-019 | Lượng tối thiểu là một bước của cặp; không minimum notional hay ngưỡng riêng cho fill/dư. | Không hủy giả tạo phần dư nhỏ hợp lệ. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-020 | Lượng Base tối đa mỗi Order: BTC 100, ETH 1,000, SOL 100,000. Không thêm trần notional. | Giới hạn rõ, không cần chọn giá tham chiếu. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-021 | Kiểm tra đủ toàn bộ vốn trước acceptance/STP; thiếu thì từ chối, không fill hoặc tự giảm lượng. Không dựa vào tự hủy sắp tới để cấp vốn. | Lệnh được nhận luôn đủ nghĩa vụ. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-022 | BUY LIMIT giữ lượng × limit × (1 + mức maker/taker lớn nhất); MARKET BUY giữ lượng × giá tối đa × (1 + taker), làm tròn lên 8 chữ số USDT. SELL giữ lượng Base; trừ phí từ tiền bán. | Đủ vốn bảo vệ giá và chuyển vai trò. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-023 | Sau mỗi Trade tính lại dự trữ của dư còn hiệu lực, trả ngay phần thừa. Phần dư kết thúc thì trả toàn bộ chưa dùng. | Không giữ thừa hay trả hai lần. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-024 | Settlement mô phỏng ngay từng Trade; Base nhận và USDT ròng dùng được ngay. Không có giai đoạn số dư chờ settlement. | Sở hữu đủ vốn đơn giản. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-025 | Không quan sát trạng thái NEW; acceptance là sự kiện, sau đó là trạng thái ban đầu ở mục 13. | Tránh mơ hồ mới/đã nhận. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-026 | Không trạng thái CANCEL_PENDING. Ghi kết quả hủy cuối; yêu cầu đang chờ không có nghĩa đã hiệu lực. | Tách ý định/kết quả không thêm trạng thái. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-027 | Năm trạng thái OPEN, PARTIALLY_FILLED, FILLED, CANCELLED, REJECTED; ba trạng thái cuối là terminal. Market chưa đủ/không fill kết thúc CANCELLED/MARKET_REMAINDER; phân biệt lý do user, STP, cuối thí nghiệm. Giữ fill cũ; active remaining bằng không khi kết thúc. | Bao phủ mọi kết quả V1. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-028 | Hoãn sửa Order; lượng/giá/biên ban đầu không đổi. | Cam kết và ưu tiên ổn định. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-029 | Hủy có hiệu lực riêng, rồi lệnh mới độc lập; không cancel-and-replace kết hợp. Lệnh mới kiểm tra vốn, nhận sequence mới; từ chối lệnh mới không khôi phục lệnh cũ. | Rủi ro thay lệnh và công bằng rõ. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-030 | Không EXPIRED, hết hạn tự động hay trần tuổi GTC trong thí nghiệm. Cuối thí nghiệm dùng CANCELLED/EXPERIMENT_ENDED. | Nhất quán GTC. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-031 | Thứ tự nghiệp vụ nền tảng quyết định lệnh/hủy/kết thúc; mỗi cặp hợp lệ tạo một Trade với lượng còn hiệu lực nhỏ hơn. Hoàn tất kết quả ngay của lệnh đến trước hành động hiệu lực kế tiếp. Fill trước giữ nguyên, hủy trước chặn dư. Quá muộn/đã kết thúc không tạo thay đổi nữa. | Trade, phí và race xác định mà không thiết kế kỹ thuật. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-032 | Tiếp tục hoãn Stop/Stop-Limit và mọi lệnh nâng cao; không định nghĩa trigger V1. Giữ nguyên OQ-004 đã duyệt. | Không thêm mô hình lệnh điều kiện. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-033 | Có số dư, vị thế spot, phí, giá vốn bình quân, realized/unrealized. BUY cộng vốn gồm phí; SELL loại vốn tỷ lệ xuống 8 chữ số, bán hết loại hết vốn. Base được cấp dùng giá vốn kịch bản khai báo, không là lợi nhuận miễn phí. | Một phương pháp kế toán hữu ích. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-034 | Định giá Last Price thí nghiệm. Base khác không chưa có giá khiến định giá/unrealized của nó và tổng equity không xác định. Giá cũ hơn 60 giây thí nghiệm vẫn giữ nhưng đánh dấu stale và tuổi giá. Không trừ phí thanh lý giả định. | Định giá tham khảo trung thực. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-035 | Thí nghiệm hữu hạn, đồng hồ ảo khai báo bắt đầu 00:00:00 UTC ngày kịch bản. Giao dịch liên tục khi chạy; kết thúc hủy mọi dư hoạt động và giữ số dư/lịch sử. Không đấu giá hay phiên hằng ngày. | Tái lập, ranh giới rõ. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-036 | Thanh khoản đầu kỳ bằng Limit kịch bản đủ vốn khai báo trước, theo thứ tự hành động khi thí nghiệm chạy; chỉ cấp vốn chuẩn bị thì sổ vẫn rỗng. Chiến lược có thể tiếp tục theo quy tắc thường. Không đảm bảo Maker tự động hay tự bổ sung tồn kho. | Thanh khoản hữu hạn cho thí nghiệm kiểm soát. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-037 | Toàn bộ độ sâu tổng hợp theo giá, Trade ẩn danh, giá tốt nhất; số lệnh/lượng đứng trước, ưu tiên, fill/lý do của lệnh mình. Không lộ danh tính/lịch sử cá nhân tài khoản khác. Phía rỗng không có giá trị. | Nghiên cứu hàng chờ không tiết lộ thừa. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-038 | Mỗi Trade thực đếm một lần theo cặp, theo Base và notional USDT trước phí, mỗi phút và lũy kế thí nghiệm. Loại phí, cấp vốn, hủy, STP; không volume 24 giờ trượt. | Đơn vị, đối tượng, khoảng rõ. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-039 | Nến một phút theo giờ UTC thí nghiệm; gồm đầu, loại cuối; cùng thời gian theo thứ tự execution. Phút rỗng OHLC không xác định, volume không; không sao chép giá cũ thành nến. Gắn nhãn khoảng cuối chưa đầy. | Biểu đồ Trade tái lập được. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-040 | Có chiến lược tự động và thí nghiệm kịch bản tổng hợp chạy lại được. Hoãn backtesting dữ liệu lịch sử và bộ nghiên cứu cấp tổ chức. | Có tương tác quant, phạm vi vừa sức. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-041 | Đặt trễ quan sát/hành động không âm cố định mỗi người mỗi thí nghiệm; so cấu hình khác nhau giữa thí nghiệm, không dao động ngẫu nhiên. Quan sát hàng chờ mình, tỷ lệ fill, giá bình quân, slippage theo quote lúc quyết định, phí, chi phí/tiền ròng đơn vị có phí, thời gian fill, kết quả hủy, PnL theo giờ thí nghiệm. Slippage bất lợi dương; thiếu tham chiếu/fill thì chỉ số không xác định. Hoãn cam kết/benchmark latency production. | Giải thích chất lượng thực hiện và timing. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-042 | Admin cấu hình vốn, giá vốn Base cấp, phí, kịch bản trước khi chạy; tài khoản thường chỉ USDT đầu kỳ. Không cấp vốn lúc chạy, sửa Trade/giá tùy ý hay ép fill. End/reset lưu thí nghiệm cũ và bắt đầu thí nghiệm riêng; không chuyển tài sản giữa thí nghiệm. | Quyền chuẩn bị không sửa lịch sử/hiệu quả. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-043 | Mỗi người thủ công, chiến lược, kịch bản thanh khoản có tài khoản/vốn riêng; không chung vốn hoặc chuyển tài sản giữa tài khoản. Cùng quy tắc; không gộp tài khoản khác nhau để STP. | Vốn quy thuộc rõ, STP đúng cấp tài khoản. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-044 | Last Price không có trước Trade đầu; giá tốt nhất/Spread thiếu vẫn không xác định. Tham chiếu vốn kịch bản gắn nhãn riêng, không là quote thực hiện được, Last Price hay giá thay thế để định giá. Không tích hợp giá ngoài V1. | Không bịa lịch sử thị trường. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-045 | Không điều chỉnh, đảo ngược, xóa hay sửa Trade trong V1. Giữ lịch sử thí nghiệm sai với nhãn tính hợp lệ nghiên cứu, rồi mở thí nghiệm riêng. Không dùng cấp vốn che giấu sửa Trade. | Sự kiện lịch sử giải thích được. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-046 | Mọi số dư có 8 chữ số thập phân chính xác. Cấp Base đúng bước lượng; cấp USDT đúng 0.00000001. Tick × step cho notional chính xác, không làm tròn notional settlement. Phí/vốn loại tỷ lệ xuống 8; dự trữ BUY lên 8; giữ phần tiền/vốn dư. Hiển thị giá/giá vốn suy ra half-up 8, phần trăm 4; làm tròn hiển thị không đổi nghĩa vụ. | Bảo toàn đơn vị, không chọn kiểu dữ liệu lập trình. | PROPOSED — OWNER REVIEW REQUIRED |

## 33. Bảng thuật ngữ

Định nghĩa phục vụ học tập; trạng thái chính sách theo sổ quyết định. Khái niệm hoãn vẫn là kiến thức hữu ích.

| English Term | Giải thích | Ví dụ / phân biệt V1 |
|--------------|------------|----------------------|
| Trader / Trading Account | Người tham gia / danh tính giao dịch có vốn riêng. | STP so tài khoản, không gom theo chủ sở hữu con người. |
| Asset | Đơn vị sở hữu và trao đổi được. | BTC, ETH, SOL, USDT mô phỏng. |
| Base / Quote Asset | Tài sản mua/bán / tài sản định giá và thanh toán. | BTC / USDT trong BTC/USDT. |
| Symbol / Trading Pair | Thị trường Base/Quote có tên. | ETH/USDT. |
| Spot Trading | Trao đổi chính các tài sản. | Nhận BTC để đổi USDT. |
| Order / Trade | Ý định giao dịch / trao đổi thực đã khớp. | Hủy dư không đảo Trade. |
| BUY / SELL | Nhận / giao đơn vị Base. | SELL SOL nhận USDT. |
| Limit Order / Limit Price | Ý định có giá / biên mua tối đa hoặc bán tối thiểu. | BUY LIMIT 90,000 có thể khớp 86,000. |
| Market Order | Thực hiện ngay với đối ứng hợp lệ. | V1 có biên giá tuyệt đối và không nằm chờ. |
| Quote-budget Buy | Mua theo ngân sách thay vì lượng Base. | Khái niệm nghiệp vụ — hoãn ngoài V1. |
| Original / Filled Quantity | Lượng Base yêu cầu / thực đã khớp. | Đặt 10, khớp 4. |
| Unexecuted / Active Remaining | Chưa thực hiện / còn được thực hiện. | Hủy 6 còn lại: unexecuted 6, active remaining 0. |
| Notional | Lượng × giá khớp trước phí. | 0.5 × 86,000 = 43,000 USDT. |
| Fill / Partial Fill | Execution / thực hiện chưa đủ lượng gốc. | 0.4 BTC của lệnh 1 BTC. |
| Order Book / Resting Order | Các ý định đang chờ / lệnh còn hiệu lực trong sổ. | V1 chỉ dư Limit nằm chờ. |
| Incoming / Aggressive Order | Ý định mới đến / ý định lấy thanh khoản đang chờ. | Limit đủ điều kiện có thể lấy thanh khoản ngay. |
| Bid / Ask | Đề nghị mua / bán. | Bid 85,500; Ask 86,000. |
| Best Bid / Best Ask | Bid cao nhất / Ask thấp nhất. | Có thể đổi mà không có Trade. |
| Spread | Best Ask trừ Best Bid. | 500 khi báo giá 85,500 / 86,000. |
| Price Level / Aggregated Depth | Một mức giá / tổng lượng còn hiệu lực theo giá. | 0.4 + 0.6 = 1 BTC tại 86,000. |
| Matching | Phân bổ đối ứng đủ điều kiện. | Giá trước, thứ tự nền tảng nhận sau. |
| Price-Time Priority / FIFO | Giá tốt hơn trước, cùng giá thì ưu tiên sớm hơn. | Lệnh muộn lớn hơn không vượt. |
| Acceptance Sequence | Thứ tự nền tảng nhận có thẩm quyền. | #1001 trước #1002 dù cùng thời gian hiển thị. |
| Queue Position | Vị trí giữa các lệnh cùng giá. | Số lệnh và lượng đứng trước lệnh mình. |
| Execution Price | Đơn giá Trade thực tế. | V1 luôn dùng giá lệnh đang chờ được khớp. |
| Price Improvement | Giá thực hiện tốt hơn biên/tham chiếu của bên đến. | Mua 86,000 thay vì giới hạn 90,000. |
| Average Execution Price | Tổng notional khớp / lượng Base khớp. | 173,000 / 2 = 86,500, không gồm phí. |
| Last Price | Giá Trade thực gần nhất. | Chưa có Trade đầu thì không xác định. |
| Reference Price / Midpoint | Giá so sánh đã đặt tên / trung điểm hai giá xác định. | Midpoint không phải quy tắc giá khớp V1. |
| Available / Locked Balance | Tài sản dùng lại được / đã cam kết cho lệnh hoạt động. | Cả hai vẫn thuộc tài khoản. |
| Reservation / Release | Cam kết tài sản / trả phần cam kết chưa dùng về khả dụng. | Release không phải lợi nhuận. |
| Settlement | Hoàn tất trao đổi tài sản thực, gồm phí. | Đề xuất thực hiện ngay từng Trade. |
| Cancellation | Kết thúc phần dư còn hiệu lực. | Fill và phí cũ không đổi. |
| OPEN / PARTIALLY_FILLED | Đang chờ chưa có fill / đang chờ đã có fill. | Trạng thái Limit chưa kết thúc được đề xuất. |
| FILLED / CANCELLED / REJECTED | Đã đủ / dư đã kết thúc / không qua bước chấp nhận. | Các trạng thái kết thúc được đề xuất. |
| NEW / CANCEL_PENDING / EXPIRED | Các tên trạng thái khác có thể gặp trong ngành. | Không là trạng thái quan sát được của V1 đề xuất. |
| Termination Reason | Lý do tách biệt trạng thái. | USER_REQUEST, SELF_TRADE_PREVENTION, MARKET_REMAINDER, EXPERIMENT_ENDED. |
| Amendment / Cancel-and-Replace | Thay cam kết cũ / thao tác thay lệnh kết hợp. | Hoãn; V1 đề xuất hủy riêng rồi đặt mới. |
| Time In Force (TIF) | Chỉ thị hiệu lực/mức hoàn tất, tách khỏi loại lệnh. | Limit V1 dùng GTC. |
| GTC | Good Till Cancelled. | Chờ đến khớp đủ/hủy hoặc cuối thí nghiệm quy định. |
| IOC | Immediate Or Cancel: khớp ngay phần có thể, kết thúc dư. | Hoãn chỉ thị riêng; Market không cần nó. |
| FOK | Fill Or Kill: đủ ngay hoặc không khớp. | Hoãn; không phải khớp một phần rồi đảo ngược. |
| Self-Trade Prevention (STP) | Ngăn execution trong cùng tài khoản. | Hủy dư tự sở hữu đang chờ, lệnh đến tiếp tục. |
| Liquidity / Market Maker | Khả năng khớp lượng / bên cung cấp ý định mua và bán. | Tài khoản kịch bản không được bảo đảm bù vốn. |
| Inventory | Tài sản một bên đang nắm giữ. | BTC dùng cho SELL kịch bản. |
| Slippage | Chênh execution so với tham chiếu rõ ràng. | Đề xuất quy ước bất lợi là dương. |
| Market Impact | Thay đổi thị trường do giao dịch gây ra. | Lấy hết Ask thấp có thể nâng Last Price. |
| Price Protection | Biên execution Market tuyệt đối V1. | BUY tối đa 90,000; SELL tối thiểu 80,000. |
| Market Data / Volume | Quan sát ý định/execution / lượng hoặc giá trị thực giao dịch. | Cấp vốn không tạo volume. |
| Candlestick / OHLCV | Tổng hợp Open, High, Low, Close, Volume theo khoảng. | Đề xuất nến một phút từ Trade. |
| Portfolio / Position | Tài sản và hiệu quả / lượng nắm giữ. | BTC Locked vẫn thuộc Position. |
| Cost Basis / Average Cost | Vốn mua phân bổ / vốn mỗi đơn vị giữ. | Đề xuất bình quân gia quyền có phí mua. |
| Realized / Unrealized PnL | Lãi/lỗ lượng đã bán / ước tính lượng còn giữ. | Cấp vốn không là lợi nhuận. |
| Equity / Liquidation Value | Định giá danh mục / tiền thu nếu bán tài sản. | Equity theo Last Price không bảo đảm tiền thanh lý. |
| Fee / Fee Treasury | Phí giao dịch / nơi nhận phí mô phỏng của nền tảng. | Đề xuất USDT bảo toàn cùng số dư treasury. |
| Maker / Taker | Bên cung cấp đang chờ / bên đến tiêu thụ trong một Trade. | Một lệnh có thể take trước rồi make sau. |
| Tick / Quantity Step (Lot) | Bước giá / bước lượng Base hợp lệ. | Bước giá đề xuất 0.01 USDT. |
| Residual / Rounding | Phần dư sau phân bổ / phép xử lý độ chính xác quy định. | Giữ vốn lẻ đến khi bán hết Position. |
| Simulation Funding | Cấp vốn mô phỏng lúc chuẩn bị. | Không Trade, execution, volume hay profit. |
| Experiment / Experiment Clock | Kịch bản hữu hạn / thời gian ảo có thẩm quyền. | Reset tạo lịch sử riêng. |
| Algorithmic / Quantitative Trading | Quyết định tự động / dựa trên phân tích định lượng. | Chiến lược không kiểm soát quy tắc khớp. |
| Backtesting | Đánh giá trên quá khứ với giả định execution. | Hoãn backtesting dữ liệu lịch sử ngoài V1. |
| HFT / Latency | Giao dịch tự động thường xuyên nhạy thời gian / độ trễ giữa sự kiện. | Trễ quan sát/hành động mô phỏng là đầu vào nghiên cứu. |
| Execution Quality | Giá, lượng, chi phí, hoàn tất và thời điểm của fill thực. | Nhanh nhưng không fill chưa chắc tốt hơn. |
| Stop / Stop-Limit / Trailing Stop | Ý định market/limit theo trigger / trigger đi theo tham chiếu. | Hoãn, không có ngữ nghĩa trigger V1. |
| Iceberg / Pegged Order | Chỉ hiển thị một phần lượng / giá gắn tham chiếu. | Khái niệm nghiệp vụ — hoãn ngoài V1. |
| Margin / Leverage / Short Selling | Dùng tín dụng hoặc khuếch đại tiếp xúc / bán tài sản chưa sở hữu. | Ngoài spot đủ vốn V1. |
| Derivative / Futures / Perpetual / Option | Hợp đồng dựa trên giá trị cơ sở, khác nhau về thời hạn/quyền thực hiện. | Khái niệm nghiệp vụ — tất cả hoãn ngoài V1. |
| Business Invariant | Điều kiện nhất quán trong mô hình đã nêu. | Một Trade không settlement hai lần. |

## 34. Câu hỏi kiểm tra hiểu biết của nhóm

Giải thích lý do, không chỉ nhắc tên. Phân biệt quyết định đã duyệt và chi tiết khuyến nghị.

1. Mua BTC/USDT làm tài sản nào vào/ra mỗi tài khoản? SELL SOL/USDT khác thế nào về đơn vị?
2. Vì sao Order không phải Trade, và cấp vốn admin không phải cả hai?
3. BUY đang chờ 90,000 gặp SELL đến giới hạn 86,000: giá khớp là gì, vì sao?
4. Đảo bên nào nằm chờ: vì sao giá khớp trở thành 86,000?
5. Một Limit Order làm Taker ở Trade này và Maker ở Trade sau như thế nào?
6. Vì sao Market Buy dùng lượng Base và giá tối đa tuyệt đối thay cho ngân sách Quote hoặc phần trăm slippage?
7. Market Buy 5 BTC chỉ có 3 BTC hợp lệ thì sao? Nếu không có lượng hợp lệ thì sao?
8. Vì sao dư Limit GTC nằm chờ còn dư Market không bao giờ nằm chờ?
9. Cùng thời gian hiển thị, acceptance sequence và số lượng ảnh hưởng ưu tiên thế nào?
10. Lệnh đến gặp lệnh đang chờ cùng tài khoản: hủy lệnh nào, cấm những tác động thị trường/phí nào?
11. Vì sao STP không ngăn chỉ vì hai tài khoản khác nhau cùng một chủ sở hữu con người?
12. Mức giá tổng hợp khác từng Order ra sao; số lệnh/lượng đứng trước giúp nghiên cứu điều gì?
13. Vì sao lệnh BTC/USDT và ETH/USDT không được tái dùng cùng USDT đã giữ?
14. Tính dự trữ Market Buy 2 BTC, tối đa 90,000, phí taker 0.2%.
15. Vì sao BUY LIMIT giữ theo phí maker/taker lớn hơn dù hiện đang là taker?
16. Giải thích chi phí 30,060, dự trữ còn 48,096 và trả dư 2,004 trong ví dụ có phí ở mục 12.
17. Vì sao SELL chỉ giữ Base vẫn đủ trả phí USDT theo biên tỷ lệ đề xuất?
18. Đối chiếu tài sản người mua, bán và treasury sau mỗi Trade tại mục 18.
19. Vì sao CANCELLED có thể Filled Quantity dương? Sự kiện nào phân biệt user hủy, STP và dư Market?
20. Vì sao hủy quá muộn không đảo Trade? Lệnh thay thế gửi riêng có ưu tiên nào?
21. Giải thích Last Price, Best Bid, Best Ask, Average Execution Price có thể cùng khác nhau.
22. Phân biệt slippage, impact và phí. Vì sao có bảo vệ bắt buộc vẫn có thể không đủ fill?
23. Phút rỗng, Trade cùng thời gian và Trade đúng ranh giới phút ảnh hưởng nến đề xuất ra sao?
24. Giá vốn bình quân, phí, tài sản được cấp và Last Price thiếu/cũ ảnh hưởng PnL, định giá thế nào?
25. Quyết định nào đã APPROVED, quyết định nào cần chủ dự án duyệt cuối; vì sao thí nghiệm hàng chờ/độ trễ không chứng minh hiệu năng HFT production?

## 35. Nhật ký review

### Trạng thái review

**DRAFT — REQUIRES FINAL OWNER REVIEW**

OQ-001 đến OQ-014 giữ APPROVED. OQ-015 đến OQ-046 giữ PROPOSED — OWNER REVIEW REQUIRED trong cả hai bản. Không khẳng định chủ dự án đã duyệt cuối những khuyến nghị này.

### Nhật ký review

| Date | Reviewer | Section | Decision / Comment |
|------|----------|---------|--------------------|
| 2026-10-06 | Codex, cập nhật theo chỉ dẫn chủ dự án | 1–35; OQ-001–OQ-014 | Đưa vào các quyết định chủ dự án đã phê duyệt rõ trong yêu cầu. Không được cung cấp tên người review. |
| 2026-10-06 | Codex | 1–35; OQ-015–OQ-046 | Chọn khuyến nghị V1 cụ thể cần chủ dự án review; rà soát nhất quán toàn tài liệu và đồng bộ hai bản Anh/Việt. Đây không phải phê duyệt cuối của chủ dự án. |
