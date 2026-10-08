# HFT Simulation Platform — Đặc tả yêu cầu hệ thống (V1)

**Trạng thái:** DRAFT — REQUIRES TEAM / OWNER REVIEW

**Cơ sở nghiệp vụ:**

- OQ-001 → OQ-014: APPROVED.
- OQ-015 → OQ-046: PROPOSED — OWNER REVIEW REQUIRED.
- Yêu cầu phụ thuộc toàn bộ hoặc một phần vào quyết định đề xuất vẫn là **PROVISIONAL**.

**Ngày rà soát nguồn:** 2026-10-07.

Đây là bản tiếng Việt của [REQUIREMENTS.md](REQUIREMENTS.md). Bản dịch giữ nguyên ID, trạng thái, số liệu và quan hệ truy vết; không tạo thêm quyết định hoặc lần phê duyệt nghiệp vụ.

## 1. Mục đích

Chuyển [Đặc tả nghiệp vụ](BUSINESS.md) hiện tại thành các yêu cầu hệ thống V1 có thể kiểm chứng, trung lập về công nghệ cho dự án mô phỏng của nhóm sinh viên. Tài liệu xác định hành vi nghiệp vụ quan sát được và cách kiểm chứng, không mô tả sàn tiền thật hoặc hệ thống HFT production. Tài liệu không phê duyệt đề xuất nghiệp vụ và không cho phép bắt đầu Kiến trúc / Thiết kế hệ thống hoặc triển khai.

## 2. Phạm vi

Phần cốt lõi V1 đã duyệt là giao dịch spot mô phỏng đủ vốn trên BTC/USDT, ETH/USDT và SOL/USDT, dùng Limit Order và Market Order có bảo vệ giá, ưu tiên Giá–Thời gian, ngăn tự khớp theo tài khoản và phí Maker/Taker cấu hình được. Các chi tiết đề xuất bao gồm cách tính vốn/khoản giữ, settlement, vòng đời, thí nghiệm, quan sát thị trường, danh mục/PnL, chiến lược và nghiên cứu thời gian. Mỗi chi tiết giữ nguyên phạm vi phê duyệt riêng.

Chủ thể nghiệp vụ gồm người giao dịch thủ công (Human Trader), chiến lược thuật toán (Algorithmic Strategy), sàn/nền tảng giao dịch (Exchange / Trading Platform), quản trị viên (Administrator) và tài khoản thanh khoản/kịch bản (Liquidity / Scenario Account). Trách nhiệm của họ không phải thiết kế vai trò truy cập kỹ thuật. Order của người tham gia thể hiện ý định; chỉ khớp thực sự đủ điều kiện mới tạo Trade. Cấp vốn, giữ vốn, hủy và yêu cầu chưa khớp không phải thực hiện giao dịch.

## 3. Cơ sở nghiệp vụ và trạng thái phê duyệt

Nguồn có thẩm quyền là [BUSINESS.md](BUSINESS.md), đặc biệt mục 31 (26 quy tắc BR), mục 32 (46 quyết định OQ) và mục 30 (19 bất biến). Các phần diễn giải cung cấp định nghĩa, ví dụ tính toán và chi tiết dùng bên dưới. [Bản tiếng Việt](BUSINESS.vi.md) và [PDF tiếng Việt](HFT-Simulation-Platform-Business-Specification-V1-Vietnamese.pdf) đã được kiểm tra làm nguồn hỗ trợ; không phát hiện mâu thuẫn đáng kể về phê duyệt hoặc chính sách nghiệp vụ. Markdown tiếng Anh vẫn là nguồn chính. Bản dịch hoặc PDF không tạo lần phê duyệt riêng.

Nguồn đã có khuyến nghị cụ thể cho mọi OQ đề xuất, không còn là các phương án trắc nghiệm chưa chọn. Giữ các khuyến nghị đó dưới dạng yêu cầu tạm thời. Vốn, mức phí, ngày và độ trễ kịch bản là đầu vào cấu hình tường minh, không phải mặc định chung còn thiếu. Những chi tiết hạn chế chưa được xác định trong mục 14 vẫn là TBD, không thay thế khuyến nghị nghiệp vụ đã chọn.

OQ-032 nhắc lại việc hoãn lệnh nâng cao đã được duyệt tại OQ-004. Nó không bổ sung yêu cầu hệ thống và không mở lại quyết định loại trừ đã duyệt; tác động của nó được ghi rõ trong mục 11.

## 4. Mô hình trạng thái yêu cầu

| Trạng thái | Ý nghĩa |
|------------|---------|
| APPROVED | Yêu cầu chỉ thể hiện hành vi OQ đã duyệt và định nghĩa nghiệp vụ hỗ trợ. Không có nghĩa toàn bộ tài liệu yêu cầu này đã được rà soát hoặc chấp nhận. |
| PROVISIONAL | Ít nhất một phần yêu cầu dựa trên OQ đề xuất. Toàn bộ yêu cầu và tiêu chí của nó chờ chủ dự án/nhóm rà soát nghiệp vụ. |
| TBD | Chi tiết chưa có quyết định nghiệp vụ. Đây là dấu hiệu thiếu thông tin, không phải hành vi đã duyệt hoặc trạng thái phê duyệt yêu cầu bổ sung. |

Use case và kịch bản nghiệm thu nhận trạng thái PROVISIONAL nếu có bất kỳ yêu cầu thành phần nào là tạm thời. Luồng con đã duyệt không phê duyệt cả luồng tổng hợp tạm thời. Tham chiếu BR nguồn có thể chứa nhiều điều khoản; mỗi yêu cầu liệt kê chính xác các OQ hỗ trợ điều khoản của nó. Thay đổi một BR vẫn đòi hỏi rà soát mọi yêu cầu tham chiếu BR đó.

Bất biến tổng quát không phê duyệt chi tiết riêng của dự án: số lượng ban đầu không đổi, tên trạng thái kết thúc, chính sách treasury và cấm hiệu chỉnh Trade ngoại lệ vẫn giữ phụ thuộc đề xuất. Yêu cầu có các phụ thuộc này không trở thành đã duyệt chỉ nhờ nguyên tắc nhất quán chung.

| Nhóm yêu cầu | APPROVED | PROVISIONAL | Tổng |
|-------------|----------|-------------|------|
| Chức năng | 21 | 53 | 74 |
| Phi chức năng | 1 | 9 | 10 |
| Toàn bộ yêu cầu | 22 | 62 | 84 |

Có 11 use case và 31 kịch bản nghiệm thu. Số lượng này không tính các tiêu chí chấp nhận riêng và các dòng bất biến nguồn. Không khẳng định đã chạy kiểm thử nghiệm thu: đây là bản nháp tài liệu đã được rà soát trước triển khai.

## 5. Thuật ngữ / Ngôn ngữ quy phạm

**PHẢI / KHÔNG ĐƯỢC (SHALL / SHALL NOT)** xác định hành vi bắt buộc/bị cấm theo trạng thái của mục. **CÓ THỂ (MAY)** biểu thị sự cho phép, không phải bảo đảm. Với mục PROVISIONAL, PHẢI xác định hành vi đề xuất nếu được chấp nhận; đây không phải bằng chứng phê duyệt nghiệp vụ. Dữ kiện kiểm chứng giả định đủ vốn, đầu vào hợp lệ và các tài khoản khác nhau, trừ khi trường hợp đó kiểm tra rõ một ngoại lệ. Giá ví dụ là mô phỏng; mức phí ví dụ không phải mặc định.

| Thuật ngữ | Ý nghĩa trong đặc tả |
|-----------|---------------------|
| Base / Quote Asset | Tài sản được mua/bán / tài sản dùng thanh toán; ví dụ BTC / USDT. |
| Notional | Số lượng Base đã thực hiện × giá thực hiện, trước phí, tính bằng USDT. |
| Incoming / Resting Order | Lệnh mới vào đang khớp nhu cầu đủ điều kiện / nhu cầu Limit đã có hiệu lực nằm chờ. |
| Original / Filled Quantity | Số lượng Base yêu cầu / số lượng Base thực sự đã khớp lũy kế. |
| Unexecuted / Active Remaining Quantity | Số lượng ban đầu trừ đã khớp / số lượng còn đủ điều kiện giao dịch, bằng không sau kết thúc. |
| Platform acceptance sequence | Thứ tự chính thức, duy nhất của các lệnh được chấp nhận; giờ máy khách không quyết định ưu tiên. |
| Available / Locked Balance | Tài sản sở hữu có thể dùng cho cam kết mới / tài sản sở hữu đã giữ cho cam kết hiện có. |
| GTC | Good Till Cancelled; thời hạn hiệu lực của Limit V1, tuân theo quy tắc kết thúc đã xác định. |
| STP | Self-Trade Prevention: hủy phần đang chờ đủ điều kiện của chính tài khoản; lệnh mới vào tiếp tục. |
| Maker / Taker | Phía đang chờ / phía mới vào của một Trade cụ thể. |
| Simulation Funding | Cấp vốn mô phỏng tường minh, không bao giờ là Trade, khối lượng, thực hiện giao dịch hoặc lợi nhuận giao dịch. |
| Fee Treasury | Nơi nhận phí USDT mô phỏng thuộc nền tảng, riêng từng thí nghiệm, không giao dịch, theo đề xuất. |
| Last Price / Best Bid / Best Ask | Giá Trade thực sự mới nhất / Bid còn hiệu lực cao nhất / Ask còn hiệu lực thấp nhất. |
| PnL / Cost Basis | Lãi lỗ / giá vốn được gán; phương pháp V1 đề xuất dùng bình quân gia quyền. |
| Experiment clock | Thời gian ảo chính thức đề xuất cho mô phỏng có giới hạn, khác giờ máy khách hoặc thời gian thực thi thật. |
| `floor8(x)` / `ceil8(x)` | Làm tròn xuống / lên đến 8 chữ số thập phân theo đơn vị tài sản đã nêu. |
| Unavailable | Giá trị không có cơ sở nghiệp vụ hợp lệ; không âm thầm biểu diễn thành không hoặc thay bằng khái niệm khác. |

Giá tính bằng USDT trên một đơn vị Base; số lượng ghi rõ BTC, ETH hoặc SOL. Phân biệt tỷ lệ và phần trăm: 0.01 điểm phần trăm = 0.0001 ở dạng tỷ lệ. Làm tròn hiển thị giá trị suy ra không bao giờ thay đổi nghĩa vụ. ID bất biến và OQ/BR giữ nguyên ý nghĩa nguồn.

## 6. Yêu cầu chức năng

Mỗi tiêu chí đánh số có tham chiếu ổn định `<requirement ID>/AC-<number>` (ID yêu cầu/số tiêu chí). Ví dụ FR-MAT-002/AC-2 là tiêu chí thứ hai của FR-MAT-002. Mục 9 bổ sung kịch bản tổng hợp; mục 11 ánh xạ cả hai hình thức kiểm chứng. Tiêu chí có cùng trạng thái với yêu cầu.

### 6.1 Tài khoản / Người tham gia

#### FR-ACT-001 — Phạm vi tài sản mô phỏng

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI biểu diễn BTC, ETH, SOL và USDT hoàn toàn dưới dạng tài sản mô phỏng. V1 KHÔNG ĐƯỢC cung cấp giao dịch tiền thật, lưu ký hoặc quyền sở hữu tiền mã hóa thật, quy đổi, nạp, rút, chuyển khoản ngân hàng, thanh toán qua blockchain hay tích hợp dữ liệu/sàn giao dịch thật bên ngoài.

**Truy vết nghiệp vụ**

- OQ-002
- OQ-005
- BR-MKT-003
- BR-BAL-001
- BUSINESS.md, mục 3, 5.

**Tiêu chí chấp nhận**

1. Số dư của bất kỳ tài sản nào trong bốn tài sản chỉ biểu thị đơn vị mô phỏng, không tạo quyền đòi nhận hoặc quy đổi tài sản thật.
2. Không hoạt động V1 nào cung cấp nạp, rút, lưu ký thật, thanh toán blockchain hay thao tác với sàn/dữ liệu bên ngoài.

#### FR-ACT-002 — Vốn mô phỏng ban đầu

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI cho phép cấu hình số USDT ban đầu của tài khoản giao dịch thông thường và PHẢI cho phép số dư Base ban đầu bằng không. Hệ thống KHÔNG ĐƯỢC áp đặt một số USDT khởi đầu chung cho mọi trường hợp.

**Truy vết nghiệp vụ**

- OQ-003
- BR-SIM-001
- BUSINESS.md, mục 5.

**Tiêu chí chấp nhận**

1. Với hai cấu hình USDT ban đầu đã khai báo, mỗi tài khoản thông thường nhận số vốn được chọn cho kịch bản của mình.
2. Tài khoản có thể bắt đầu với USDT và BTC, ETH, SOL bằng không; riêng quy tắc đã duyệt chưa yêu cầu mọi lần cấp vốn chỉ diễn ra lúc thiết lập.

#### FR-ACT-003 — Tách biệt vốn của người tham gia

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI cung cấp tài khoản giao dịch và nguồn số dư riêng cho mỗi người tham gia thủ công, chiến lược và kịch bản thanh khoản trong một thí nghiệm. V1 KHÔNG ĐƯỢC hỗ trợ vốn dùng chung giữa chiến lược hoặc chuyển tài sản giữa tài khoản.

**Truy vết nghiệp vụ**

- OQ-043
- BR-ACT-002
- BUSINESS.md, mục 4.

**Tiêu chí chấp nhận**

1. Với một người tham gia thủ công, một chiến lược và một kịch bản thanh khoản, mỗi bên có nguồn số dư riêng; bên này không thể chi tiêu nguồn vốn của bên khác.
2. V1 không hỗ trợ phân bổ vốn hiện có từ tài khoản này sang tài khoản khác; Simulation Funding chịu quy định riêng tại FR-ADM-003.

### 6.2 Mô phỏng / Thí nghiệm

#### FR-SIM-001 — Thí nghiệm có giới hạn và đồng hồ ảo

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI vận hành các thí nghiệm mô phỏng có giới hạn, với đồng hồ thí nghiệm ảo được khai báo bắt đầu tại 00:00:00 UTC vào ngày của kịch bản. Giao dịch PHẢI liên tục khi thí nghiệm chạy, không có đấu giá hoặc phiên giao dịch hằng ngày; thời gian thí nghiệm KHÔNG ĐƯỢC tiến khi đồng hồ dừng.

**Truy vết nghiệp vụ**

- OQ-035
- BR-SIM-002
- BUSINESS.md, mục 24, 29, 32.

**Tiêu chí chấp nhận**

1. Với một ngày kịch bản đã khai báo, thời gian ảo ban đầu là 00:00:00 UTC của ngày đó, không phụ thuộc giờ địa phương của máy khách.
2. Khi đồng hồ thí nghiệm dừng, các khoảng thời gian thí nghiệm không tiến; không phiên hằng ngày hoặc đấu giá nào ngắt thí nghiệm đang chạy. Chi tiết điều khiển đồng hồ vẫn là TBD-002.

#### FR-SIM-002 — Kết thúc thí nghiệm

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI làm cho việc kết thúc thí nghiệm có hiệu lực theo thứ tự nghiệp vụ chính thức, hủy mọi số lượng còn hiệu lực với lý do EXPERIMENT_ENDED, giải phóng khoản giữ chưa dùng và giữ nguyên số dư kết quả, lịch sử Trade, các lần khớp và phí. Hệ thống KHÔNG ĐƯỢC đảo ngược Trade trước đó hoặc gắn nhãn EXPIRED cho việc kết thúc này.

**Truy vết nghiệp vụ**

- OQ-023
- OQ-027
- OQ-030
- OQ-031
- OQ-035
- OQ-042
- OQ-045
- BR-BAL-003
- BR-ORD-003
- BR-ORD-007
- BR-SIM-002
- BR-TRD-001
- BUSINESS.md, mục 13–15, 32.

**Tiêu chí chấp nhận**

1. Khi kết thúc có hiệu lực đối với một Limit Order đang chờ đã khớp một phần, phần còn hiệu lực trở thành không, trạng thái là CANCELLED và lý do là EXPERIMENT_ENDED.
2. Chỉ khoản giữ chưa dùng trở lại số dư khả dụng; các lần chuyển quyền sở hữu và phí đã thực hiện vẫn nằm trong lịch sử thí nghiệm được lưu giữ.

#### FR-SIM-003 — Đặt lại bằng thí nghiệm riêng và bảo toàn lịch sử

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI thực hiện đặt lại kịch bản bằng cách kết thúc/lưu giữ thí nghiệm cũ và bắt đầu một thí nghiệm riêng với vốn cấp và lịch sử được khai báo riêng. Hệ thống KHÔNG ĐƯỢC chuyển tài sản mô phỏng giữa các thí nghiệm.

**Truy vết nghiệp vụ**

- OQ-035
- OQ-042
- OQ-045
- BR-SIM-002
- BR-TRD-001
- BUSINESS.md, mục 16, 28, 32.

**Tiêu chí chấp nhận**

1. Sau khi đặt lại, số dư và Trade cũ vẫn thuộc thí nghiệm cũ; số dư khởi đầu mới đến từ Simulation Funding được nhận diện riêng.
2. Lần chạy mới không xóa hoặc ghi đè lần chạy trước và không nhận tài sản của lần trước thông qua chuyển tài sản.

#### FR-SIM-004 — Thanh khoản kịch bản theo quy tắc thông thường

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI tạo thanh khoản ban đầu của kịch bản bằng các Limit Order định trước, đủ vốn, từ tài khoản kịch bản, được gửi theo thứ tự hành động đã khai báo khi thí nghiệm chạy. Riêng việc cấp vốn lúc thiết lập PHẢI để sổ lệnh trống. Lượng tài sản của kịch bản PHẢI hữu hạn; không bảo đảm có nhà tạo lập thị trường hoạt động liên tục, không tự bổ sung tài sản và không có ưu tiên khớp đặc biệt.

**Truy vết nghiệp vụ**

- OQ-003
- OQ-005
- OQ-010
- OQ-011
- OQ-035
- OQ-036
- OQ-043
- BR-ACT-001
- BR-ACT-002
- BR-SIM-001
- BUSINESS.md, mục 20, 27, 32.

**Tiêu chí chấp nhận**

1. Nếu chỉ có cấp vốn thiết lập mà chưa gửi lệnh, sổ lệnh trống; khi được gửi, các Limit Order kịch bản tạo nhu cầu chờ thông thường hoặc tạo khớp lệnh thực sự.
2. Dùng hết tài sản của tài khoản kịch bản không tự bổ sung lại tài sản; thị trường trống hoặc chỉ có một phía vẫn là kết quả hợp lệ.
3. Tài khoản kịch bản có thể tiếp tục tham gia bằng chiến lược theo các quy tắc vốn, khớp lệnh và STP thông thường.

### 6.3 Lệnh

#### FR-ORD-001 — Loại lệnh và số lượng Base

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI chỉ hỗ trợ Limit Order và Market Order ở cả phía BUY và SELL, với số lượng lệnh được biểu thị bằng Base Asset của cặp giao dịch. V1 KHÔNG ĐƯỢC hỗ trợ Market Buy theo ngân sách Quote hoặc các lệnh Stop, Stop-Limit, Trailing Stop, Iceberg, Pegged hay lệnh nâng cao/có điều kiện khác.

**Truy vết nghiệp vụ**

- OQ-004
- OQ-006
- BR-ORD-004
- BUSINESS.md, mục 3, 6, 8.

**Tiêu chí chấp nhận**

1. Market Buy BTC/USDT yêu cầu số lượng BTC, không phải chỉ thị chi tiêu một ngân sách USDT; Market Sell cũng chỉ định BTC.
2. Chỉ thị lệnh nâng cao/có điều kiện nằm ngoài V1; không bổ sung hành vi kích hoạt lệnh.

#### FR-ORD-002 — Điều kiện giá của Limit Order

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI coi giá BUY Limit là giá thực hiện tối đa và giá SELL Limit là giá thực hiện tối thiểu cho mỗi đơn vị. Limit Order mới vào PHẢI khớp với nhu cầu đối ứng đang chờ đủ điều kiện trước khi phần GTC còn lại nằm trên sổ.

**Truy vết nghiệp vụ**

- OQ-009
- OQ-012
- BR-ORD-005
- BR-ORD-002
- ORD-INV-003
- BUSINESS.md, mục 7, 17.

**Tiêu chí chấp nhận**

1. BUY LIMIT tại 90,000 USDT/BTC có thể khớp với Ask tại 86,000 USDT/BTC nhưng không được khớp tại 91,000 USDT/BTC.
2. SELL LIMIT tại 85,000 USDT/BTC có thể khớp với Bid tại 87,000 USDT/BTC nhưng không được khớp tại 84,000 USDT/BTC.

#### FR-ORD-003 — Phần còn lại GTC của Limit Order

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI dùng GTC cho mọi Limit Order và PHẢI giữ phần chưa khớp của Limit đủ điều kiện nằm chờ đến khi được khớp, hủy có hiệu lực hoặc kết thúc theo một quy tắc kết thúc V1 đã xác định khác. Hệ thống PHẢI bảo toàn các lần khớp trước đó mà không bảo đảm sẽ khớp trong tương lai.

**Truy vết nghiệp vụ**

- OQ-007
- OQ-012
- BR-ORD-002
- BUSINESS.md, mục 7, 11, 15.

**Tiêu chí chấp nhận**

1. Với Limit Buy 10 BTC và chỉ 4 BTC đối ứng đủ điều kiện ngay lập tức, 4 BTC được khớp và 6 BTC còn lại nằm chờ tại giá giới hạn mua.
2. Nếu không có đối ứng đủ điều kiện ngay lập tức, Limit Order đã chấp nhận có thể nằm chờ mà chưa khớp phần nào.

#### FR-ORD-004 — Market Order chỉ thực hiện ngay

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI chỉ thực hiện Market Order với đối ứng đang chờ, đủ điều kiện và có sẵn ngay lúc đó, bảo toàn các lần khớp một phần đã hoàn tất và chấm dứt ngay mọi phần chưa khớp. Phần còn lại của Market KHÔNG ĐƯỢC nằm trên sổ và KHÔNG ĐƯỢC yêu cầu người giao dịch phải chỉ định IOC.

**Truy vết nghiệp vụ**

- OQ-006
- OQ-007
- OQ-012
- BR-ORD-002
- BR-ORD-004
- BUSINESS.md, mục 8, 11, 15.

**Tiêu chí chấp nhận**

1. Market yêu cầu 5 BTC nhưng chỉ có 3 BTC đủ điều kiện ngay lập tức sẽ khớp 3 BTC đó và chấm dứt 2 BTC còn lại, không đảo ngược các lần khớp.
2. Khi không có thanh khoản đủ điều kiện, không có Trade và không có thực hiện giao dịch; toàn bộ phần còn lại kết thúc và không nằm chờ.

#### FR-ORD-005 — Giá bảo vệ tối đa tuyệt đối của Market Buy

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI yêu cầu giá thực hiện tối đa tuyệt đối cho mọi Market Buy và KHÔNG ĐƯỢC khớp cao hơn giá đó. Chỉ thị trượt giá theo phần trăm KHÔNG ĐƯỢC thay thế giá này. Biên giá PHẢI giới hạn giá thực hiện cho mỗi đơn vị, còn phí được xét riêng.

**Truy vết nghiệp vụ**

- OQ-008
- BR-ORD-004
- ORD-INV-003
- BUSINESS.md, mục 8, 17.

**Tiêu chí chấp nhận**

1. Với Market Buy 2 BTC có trần 86,000 USDT/BTC, có 1 BTC được chào tại 86,000 và đối ứng khác tại 87,000 USDT/BTC, chỉ 1 BTC trong biên giá được khớp.
2. Thiếu giá tối đa hoặc chỉ cung cấp chỉ thị theo phần trăm không thể cho phép thực hiện Market Buy; giá đúng bằng mức tối đa vẫn đủ điều kiện.

#### FR-ORD-006 — Giá bảo vệ tối thiểu tuyệt đối của Market Sell

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI yêu cầu giá thực hiện tối thiểu tuyệt đối cho mọi Market Sell và KHÔNG ĐƯỢC khớp thấp hơn giá đó. Chỉ thị theo phần trăm KHÔNG ĐƯỢC thay thế giá tối thiểu tuyệt đối; bảo vệ giá KHÔNG ĐƯỢC mang ý nghĩa bảo đảm khớp hết hoặc khớp đúng tại mức tối thiểu.

**Truy vết nghiệp vụ**

- OQ-008
- BR-ORD-004
- ORD-INV-003
- BUSINESS.md, mục 8, 17.

**Tiêu chí chấp nhận**

1. Market Sell có giá tối thiểu 80,000 USDT/BTC có thể khớp với Bid đang chờ tại 82,000 và 80,000 nhưng không bao giờ tại 79,000 USDT/BTC.
2. Thiếu giá tối thiểu hoặc chỉ cung cấp chỉ thị theo phần trăm không thể cho phép thực hiện Market Sell; phần còn lại kết thúc nếu không còn Bid đủ điều kiện.

#### FR-ORD-007 — Phạm vi Time In Force riêng biệt

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI phân biệt loại lệnh với Time In Force và KHÔNG ĐƯỢC cung cấp IOC hoặc FOK như chỉ thị riêng trong V1. Thời hạn hiệu lực của Limit PHẢI là GTC và Market PHẢI tự mang tính chất chỉ thực hiện ngay.

**Truy vết nghiệp vụ**

- OQ-012
- BR-ORD-002
- BUSINESS.md, mục 15.

**Tiêu chí chấp nhận**

1. Yêu cầu Market có bảo vệ giá hợp lệ không cần lựa chọn IOC riêng.
2. V1 không cung cấp chỉ thị FOK yêu cầu khớp toàn bộ hoặc lựa chọn IOC cho Limit Order.

#### FR-ORD-008 — Bước giá và giới hạn giá

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI kiểm tra mọi giá Limit và giá bảo vệ Market theo bước 0.01 USDT trên một đơn vị Base, trong khoảng gồm cả hai đầu 0.01–1,000,000.00 USDT trên một đơn vị Base cho cả ba cặp. Hệ thống PHẢI từ chối giá lệch bước hoặc ngoài khoảng trước khi giao dịch, không làm tròn giá đầu vào.

**Truy vết nghiệp vụ**

- OQ-017
- BR-PREC-001
- BUSINESS.md, mục 6.

**Tiêu chí chấp nhận**

1. Các giá 0.01 và 1,000,000.00 đạt kiểm tra giới hạn giá; 0, 1,000,000.01 và 86,000.001 không đạt.
2. Cùng phép kiểm tra bước giá và khoảng giá được áp dụng cho giá Limit, giá tối đa Market Buy và giá tối thiểu Market Sell.

#### FR-ORD-009 — Bước số lượng

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI yêu cầu số lượng lệnh dương và là bội số của 0.000001 BTC, 0.00001 ETH hoặc 0.001 SOL theo từng cặp tương ứng. Hệ thống PHẢI từ chối yêu cầu lệch bước mà không làm tròn; số lượng khớp và phần còn hiệu lực PHẢI tiếp tục là bội số hợp lệ của bước số lượng.

**Truy vết nghiệp vụ**

- OQ-018
- BR-PREC-001
- BUSINESS.md, mục 6.

**Tiêu chí chấp nhận**

1. 0.000001 BTC, 0.00001 ETH và 0.001 SOL đạt kiểm tra bước tương ứng; 0.0000015 BTC, 0.000015 ETH và 0.0015 SOL không đạt.
2. Số lượng bằng không hoặc âm không thể giao dịch; kiểm tra bước số lượng không được âm thầm thay đổi số lượng yêu cầu.

#### FR-ORD-010 — Số lượng tối thiểu và không có giá trị giao dịch tối thiểu

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI đặt số lượng lệnh tối thiểu bằng một bước số lượng của cặp, không có notional tối thiểu hay mức tối thiểu riêng cho lần khớp/phần còn lại. Hệ thống KHÔNG ĐƯỢC chấm dứt phần còn lại dương là bội số hợp lệ chỉ vì số lượng nhỏ.

**Truy vết nghiệp vụ**

- OQ-018
- OQ-019
- BR-PREC-001
- BUSINESS.md, mục 6.

**Tiêu chí chấp nhận**

1. Lệnh bằng một bước đạt kiểm tra số lượng tối thiểu dù notional hợp lệ nhỏ, nhưng vẫn phải đáp ứng các kiểm tra khác.
2. Phần còn lại bằng một bước vẫn đủ điều kiện theo quy tắc lệnh thông thường và không bị loại bởi chính sách khớp tối thiểu bổ sung.

#### FR-ORD-011 — Số lượng tối đa và không có trần notional

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI giới hạn số lượng ban đầu của mỗi lệnh tối đa lần lượt là 100 BTC, 1,000 ETH hoặc 100,000 SOL, không có trần notional bổ sung.

**Truy vết nghiệp vụ**

- OQ-020
- BR-PREC-001
- BUSINESS.md, mục 6.

**Tiêu chí chấp nhận**

1. Số lượng đúng bằng từng mức tối đa đạt kiểm tra số lượng tối đa, nhưng vẫn phải đáp ứng kiểm tra khác và đủ vốn.
2. 100.000001 BTC, 1,000.00001 ETH và 100,000.001 SOL vượt mức tối đa tương ứng và bị từ chối trước khi giao dịch.

#### FR-ORD-012 — Năm trạng thái vòng đời quan sát được

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI hiển thị đúng năm trạng thái lệnh V1: OPEN, PARTIALLY_FILLED, FILLED, CANCELLED và REJECTED, với ý nghĩa và chuyển trạng thái trong bảng vòng đời bên dưới. FILLED, CANCELLED và REJECTED PHẢI là trạng thái kết thúc và KHÔNG ĐƯỢC trở lại hiệu lực.

**Truy vết nghiệp vụ**

- OQ-025
- OQ-026
- OQ-027
- BR-ORD-003
- BUSINESS.md, mục 13.

**Tiêu chí chấp nhận**

1. Limit được chấp nhận ban đầu có kết quả OPEN, PARTIALLY_FILLED hoặc FILLED; Market được chấp nhận ban đầu có kết quả FILLED hoặc CANCELLED.
2. OPEN có thể chuyển sang PARTIALLY_FILLED, FILLED hoặc CANCELLED; PARTIALLY_FILLED có thể giữ PARTIALLY_FILLED hoặc chuyển sang FILLED hay CANCELLED.
3. Lần khớp hoặc yêu cầu hủy về sau không thể khiến lệnh đã kết thúc trở lại hiệu lực.

#### FR-ORD-013 — Chấp nhận lệnh không có trạng thái NEW

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI biểu diễn việc chấp nhận dưới dạng sự kiện nghiệp vụ, tiếp theo là kết quả ban đầu tương ứng, và KHÔNG ĐƯỢC hiển thị NEW như một trạng thái lệnh V1.

**Truy vết nghiệp vụ**

- OQ-025
- OQ-027
- BR-ORD-003
- BUSINESS.md, mục 13.

**Tiêu chí chấp nhận**

1. Limit đã chấp nhận, chưa khớp và đang nằm chờ được quan sát ở trạng thái OPEN, không phải NEW.
2. Lệnh mới vào đã chấp nhận và khớp hết được quan sát ở trạng thái FILLED; yêu cầu không hợp lệ là REJECTED mà không được chấp nhận.

#### FR-ORD-014 — Yêu cầu hủy và kết quả hủy

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI ghi nhận kết quả hủy có hiệu lực và KHÔNG ĐƯỢC hiển thị CANCEL_PENDING như trạng thái lệnh. Riêng yêu cầu hủy đang chờ KHÔNG ĐƯỢC đồng nghĩa với việc hủy đã có hiệu lực.

**Truy vết nghiệp vụ**

- OQ-026
- OQ-031
- BR-ORD-003
- BR-ORD-007
- BUSINESS.md, mục 13, 14.

**Tiêu chí chấp nhận**

1. Lệnh có yêu cầu hủy vẫn tuân theo thứ tự chính thức của lệnh/lần khớp cho đến khi kết quả hủy cuối cùng có hiệu lực.
2. Hủy quá muộn trả về kết quả cuối cùng thay vì khẳng định yêu cầu đó đã đảo ngược lần khớp trước.

#### FR-ORD-015 — Lý do kết thúc

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI phân biệt USER_REQUEST, SELF_TRADE_PREVENTION, MARKET_REMAINDER và EXPERIMENT_ENDED với các trạng thái vòng đời. Mọi Market Order hợp lệ chưa khớp hết, kể cả không khớp phần nào, PHẢI kết thúc ở CANCELLED với lý do MARKET_REMAINDER.

**Truy vết nghiệp vụ**

- OQ-027
- OQ-035
- BR-ORD-003
- BR-SIM-002
- BUSINESS.md, mục 13.

**Tiêu chí chấp nhận**

1. Market chưa khớp hết do thanh khoản hoặc biên bảo vệ kết thúc ở CANCELLED/MARKET_REMAINDER, không bao giờ có trạng thái cuối PARTIALLY_FILLED.
2. Hủy theo yêu cầu người dùng, ngăn tự khớp và kết thúc thí nghiệm có lý do riêng tương ứng, không tạo thêm trạng thái lệnh.

#### FR-ORD-016 — Hạch toán số lượng lệnh

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI duy trì 0 ≤ Filled Quantity ≤ Original Quantity và Original Quantity = Filled Quantity + Unexecuted Quantity. Original Quantity PHẢI không đổi. Active Remaining Quantity PHẢI bằng Unexecuted Quantity khi lệnh còn hiệu lực và bằng không trong mọi trạng thái kết thúc; mỗi Trade thực sự PHẢI chỉ đóng góp một lần vào số lượng đã khớp.

**Truy vết nghiệp vụ**

- OQ-027
- OQ-028
- BR-ORD-003
- BR-ORD-007
- ORD-INV-001
- ORD-INV-002
- TRD-INV-003
- BUSINESS.md, mục 6, 11, 13, 30.

**Tiêu chí chấp nhận**

1. Với lệnh ban đầu 10 BTC đã thực hiện 4 BTC và hủy phần còn lại, số lượng ban đầu là 10 BTC, đã khớp 4 BTC, chưa thực hiện 6 BTC và còn hiệu lực 0 BTC.
2. Quan sát lại một Trade đã có không làm số lượng đã khớp tăng lần nữa; lệnh khớp hết có số lượng đã khớp bằng số lượng ban đầu và không còn phần hiệu lực.

#### FR-ORD-017 — Không sửa lệnh

**Trạng thái:** PROVISIONAL

**Yêu cầu**

V1 KHÔNG ĐƯỢC sửa số lượng ban đầu, giá Limit hoặc biên bảo vệ Market của một lệnh hiện có.

**Truy vết nghiệp vụ**

- OQ-028
- BR-ORD-007
- ORD-INV-001
- BUSINESS.md, mục 6, 14, 32.

**Tiêu chí chấp nhận**

1. Yêu cầu thay đổi số lượng hoặc giá của lệnh hiện có không thể thay đổi cam kết hoặc độ ưu tiên của lệnh đó.
2. Thay đổi ý định giao dịch phải dùng luồng hủy rồi gửi mới được xác định riêng.

#### FR-ORD-018 — Hủy riêng rồi gửi lệnh mới riêng

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI chỉ hỗ trợ thay đổi ý định bằng cách hủy có hiệu lực phần còn lại của lệnh cũ, sau đó gửi một lệnh mới độc lập, với kiểm tra tính hợp lệ, kiểm tra vốn và ưu tiên chấp nhận mới. Hệ thống KHÔNG ĐƯỢC cung cấp thao tác cancel-and-replace kết hợp hoặc khôi phục lệnh cũ khi yêu cầu mới thất bại.

**Truy vết nghiệp vụ**

- OQ-010
- OQ-021
- OQ-028
- OQ-029
- OQ-031
- BR-ORD-007
- BR-BAL-004
- BR-ORD-001
- BUSINESS.md, mục 14, 32.

**Tiêu chí chấp nhận**

1. Sau khi hủy có hiệu lực và gửi lệnh mới hợp lệ tại cùng giá, lệnh mới nhận thứ tự chấp nhận mới và không thể kế thừa vị trí hàng chờ cũ.
2. Nếu yêu cầu mới bị từ chối, phần còn lại cũ vẫn kết thúc và các lần khớp trước vẫn được giữ nguyên.

#### FR-ORD-019 — Không tự hết hạn GTC

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống KHÔNG ĐƯỢC áp dụng hết hạn tự động, thời hạn GTC tối đa trong thí nghiệm hoặc trạng thái EXPIRED. Kết thúc thí nghiệm PHẢI dùng CANCELLED/EXPERIMENT_ENDED.

**Truy vết nghiệp vụ**

- OQ-012
- OQ-027
- OQ-030
- OQ-035
- BR-ORD-002
- BR-ORD-003
- BR-ORD-007
- BR-SIM-002
- BUSINESS.md, mục 15, 32.

**Tiêu chí chấp nhận**

1. Chỉ việc thời gian tiến lên không làm Limit GTC đang hiệu lực hết hạn trong một thí nghiệm đang chạy.
2. Kết thúc thí nghiệm chấm dứt phần GTC còn hiệu lực bằng hủy lệnh, không tạo EXPIRED.

#### FR-ORD-020 — Hủy có hiệu lực phần còn lại của chính tài khoản

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI chỉ cho phép người tham gia hủy phần chưa khớp còn hiệu lực của chính tài khoản mình. Hủy có hiệu lực PHẢI ngăn phần đó được thực hiện về sau, đồng thời bảo toàn các lần khớp và phí trước đó. Kết quả quá muộn/đã khớp hết hoặc đã kết thúc KHÔNG ĐƯỢC gây thêm thay đổi hay giải phóng trùng; yêu cầu không tồn tại hoặc đã bị từ chối PHẢI được coi là không có lệnh đang hiệu lực để hủy.

**Truy vết nghiệp vụ**

- OQ-023
- OQ-027
- OQ-031
- BR-ORD-003
- BR-ORD-007
- BR-BAL-003
- ORD-INV-004
- BAL-INV-003
- BUSINESS.md, mục 14.

**Tiêu chí chấp nhận**

1. Hủy phần còn lại 0.6 BTC sau khi đã khớp hợp lệ 0.4 BTC vẫn giữ giao dịch mua 0.4 BTC và chỉ giải phóng khoản giữ chưa dùng.
2. Sau khi khớp hết, hủy trả về quá muộn/đã khớp hết; sau lần hủy trước, trả về đã kết thúc; cả hai không hoàn lại tài sản đã trao đổi hoặc phí.
3. Cố hủy lệnh của tài khoản khác không thể chấm dứt lệnh đó.

Bảng sau là **PROVISIONAL**, thuộc FR-ORD-012 / FR-ORD-015 / FR-ORD-016, từ BUSINESS.md mục 13 và OQ-025, OQ-026, OQ-027:

| Trạng thái | Ý nghĩa | Kết thúc? |
|------------|---------|-----------|
| OPEN | Limit GTC đã chấp nhận, đang chờ và chưa khớp. | Không |
| PARTIALLY_FILLED | Limit GTC đã khớp một phần và còn phần đang chờ hiệu lực. | Không |
| FILLED | Filled Quantity bằng Original Quantity. | Có |
| CANCELLED | Phần chưa khớp đã kết thúc; Filled Quantity có thể bằng không hoặc dương. | Có |
| REJECTED | Kiểm tra tính hợp lệ/vốn thất bại trước chấp nhận, Trade hoặc tác động STP. | Có |

Các nhãn vòng đời không hạ trạng thái đã duyệt của việc kết thúc phần Market còn lại hoặc hành vi GTC; chúng là cách biểu diễn đề xuất cho những kết quả đó.

### 6.4 Khớp lệnh

#### FR-MAT-001 — Ưu tiên giá tốt hơn

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI khớp nhu cầu đủ điều kiện, ngược phía, cùng cặp theo giá tốt hơn trước: Ask thấp nhất cho BUY mới vào và Bid cao nhất cho SELL mới vào.

**Truy vết nghiệp vụ**

- OQ-010
- BR-ORD-001
- BUSINESS.md, mục 10.

**Tiêu chí chấp nhận**

1. Ask đến sau tại 85,000 USDT/BTC được chọn trước Ask đến trước tại 86,000 USDT/BTC bởi BUY có thể trả cả hai giá.
2. SELL đủ điều kiện ở cả hai giá chọn Bid 85,500 trước Bid 85,000 USDT/BTC; lệnh ở cặp khác không phải đối ứng.

#### FR-MAT-002 — Ưu tiên cùng giá theo thứ tự chấp nhận

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI ưu tiên lệnh đang chờ cùng giá theo thứ tự chấp nhận chính thức, duy nhất của nền tảng: được chấp nhận sớm hơn được ưu tiên. Dấu thời gian của máy khách, thời gian hiển thị bằng nhau và số lượng lớn hơn KHÔNG ĐƯỢC thay đổi ưu tiên đó. Phần còn lại nằm chờ PHẢI giữ thứ tự chấp nhận gốc của lệnh.

**Truy vết nghiệp vụ**

- OQ-010
- BR-ORD-001
- ORD-INV-005
- BUSINESS.md, mục 10.

**Tiêu chí chấp nhận**

1. Với Ask A 0.4 BTC có thứ tự #1001 và Ask B 2 BTC có thứ tự #1002, cùng tại 86,000 USDT/BTC, BUY 1 BTC khớp 0.4 BTC của A trước 0.6 BTC của B.
2. Đổi dấu thời gian máy khách của B thành thời điểm sớm hơn hoặc cho A và B cùng thời gian hiển thị không đưa B lên trước A.
3. Tăng số lượng của B không cho phép B vượt A; phần còn lại đang chờ của lệnh đã khớp một phần giữ thứ tự gốc.

#### FR-MAT-003 — Giá thực hiện là giá lệnh đang chờ

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI thực hiện mỗi Trade tại giá của lệnh đang chờ được khớp, trong cả hai biên giá áp dụng cho các lệnh. Giá Limit/bảo vệ của lệnh mới vào PHẢI xác định tính đủ điều kiện thay vì thay thế giá thực hiện. Hai bên PHẢI thống nhất cặp giao dịch, số lượng Base và giá thực hiện.

**Truy vết nghiệp vụ**

- OQ-008
- OQ-009
- BR-ORD-005
- BR-ORD-004
- ORD-INV-003
- TRD-INV-002
- BUSINESS.md, mục 7, 16, 17.

**Tiêu chí chấp nhận**

1. BUY đang chờ tại 90,000 USDT/BTC được khớp bởi SELL mới vào có giá Limit 86,000 sẽ thực hiện tại 90,000; đổi phía nào nằm chờ thì giá là 86,000.
2. Market Buy có trần 90,000 không thể khớp với Ask đang chờ tại 91,000 USDT/BTC; nhiều mức giá đang chờ đủ điều kiện được thực hiện tại giá riêng của từng mức.

#### FR-MAT-004 — Hủy phần đang chờ của chính tài khoản

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI kiểm tra danh tính tài khoản giao dịch tại đối ứng đang chờ đủ điều kiện tiếp theo. Nếu hai lệnh thuộc cùng tài khoản, hệ thống PHẢI hủy phần còn hiệu lực của lệnh đang chờ, phân biệt SELF_TRADE_PREVENTION với hủy theo người dùng, bảo toàn các lần khớp trước và tiếp tục xử lý lệnh mới vào. Hệ thống KHÔNG ĐƯỢC chỉ bỏ qua rồi để phần tự đối ứng còn hiệu lực hoặc gom các tài khoản khác nhau theo cùng chủ sở hữu con người.

**Truy vết nghiệp vụ**

- OQ-011
- BR-ORD-006
- BR-ACT-001
- TRD-INV-004
- BUSINESS.md, mục 10.

**Tiêu chí chấp nhận**

1. Với BUY mới vào của A, SELL đang chờ đủ điều kiện 0.4 BTC của A, sau đó là SELL đủ điều kiện 0.6 BTC của B, phần đang chờ của A bị hủy và BUY mới vào tiếp tục đến B.
2. Các lần khớp trước của lệnh đang chờ bị hủy vẫn được giữ nguyên; lệnh của hai tài khoản khác nhau không bị coi là tự đối ứng chỉ vì cùng chủ sở hữu con người.

#### FR-MAT-005 — STP không tạo tác động thực hiện giao dịch

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống KHÔNG ĐƯỢC tạo Trade, khối lượng giao dịch, cập nhật Last Price hoặc phí cho một lần tự khớp bị ngăn. STP PHẢI được phép thay đổi độ sâu sổ lệnh do loại bỏ số lượng đang chờ đã hủy.

**Truy vết nghiệp vụ**

- OQ-011
- BR-ORD-006
- TRD-INV-004
- BUSINESS.md, mục 10, 23.

**Tiêu chí chấp nhận**

1. Khi đối ứng đủ điều kiện duy nhất thuộc cùng tài khoản, số lượng bị ngăn không làm tăng số Trade, khối lượng Base/Quote, Last Price hoặc phí.
2. Nếu lệnh mới vào sau đó khớp với tài khoản khác, chỉ Trade thực sự đó mới có tác động thực hiện giao dịch.

#### FR-MAT-006 — Số lượng Trade xác định cho từng cặp lệnh đủ điều kiện

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI thực hiện số lượng Base còn hiệu lực nhỏ hơn của mỗi cặp lệnh đủ điều kiện không tự đối ứng dưới dạng một Trade trước khi tiếp tục khớp nếu cần.

**Truy vết nghiệp vụ**

- OQ-031
- BR-ORD-007
- BUSINESS.md, mục 10, 32.

**Tiêu chí chấp nhận**

1. Với hai lượng đối ứng còn hiệu lực đủ điều kiện 0.4 BTC và 1 BTC, cặp lệnh tạo một Trade 0.4 BTC, để lại 0.6 BTC trên lệnh lớn hơn.
2. Hệ thống không tùy ý chia cặp đó thành các Trade nhỏ hơn khiến kết quả làm tròn phí theo từng Trade thay đổi.

#### FR-MAT-007 — Thứ tự chính thức của lệnh, hủy và kết thúc

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI xác định hiệu lực của lệnh, hủy và kết thúc thí nghiệm theo thứ tự nghiệp vụ chính thức. Hệ thống PHẢI hoàn tất toàn bộ kết quả thực hiện ngay của mỗi lệnh mới vào, gồm STP và mọi lần khớp ngay, trước khi hành động tiếp theo có hiệu lực. Dấu thời gian máy khách KHÔNG ĐƯỢC ghi đè thứ tự này.

**Truy vết nghiệp vụ**

- OQ-010
- OQ-031
- OQ-035
- BR-ORD-001
- BR-ORD-007
- BR-SIM-002
- BUSINESS.md, mục 10, 14, 32.

**Tiêu chí chấp nhận**

1. Nếu thực hiện giao dịch đi trước hủy có hiệu lực, lần khớp vẫn giữ nguyên và hủy chỉ dừng phần còn lại; nếu hủy đi trước một hành động đặt lệnh về sau, phần đã hủy không thể khớp.
2. Hành động hủy/kết thúc đến sau không thể chen giữa STP/các lần khớp thuộc cùng kết quả thực hiện ngay của một lệnh mới vào.

### 6.5 Số dư / Giữ vốn

#### FR-BAL-001 — Spot mô phỏng đủ vốn

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI chỉ cho phép cam kết BUY bằng Quote khả dụng và cam kết SELL bằng Base khả dụng đang sở hữu, kể cả nghĩa vụ phí đang được áp dụng. Hệ thống KHÔNG ĐƯỢC cung cấp vay, margin, đòn bẩy, bán khống, futures, perpetuals, options hoặc phái sinh khác.

**Truy vết nghiệp vụ**

- OQ-002
- OQ-005
- OQ-013
- BR-BAL-001
- BR-FEE-001
- BAL-INV-004
- BUSINESS.md, mục 3, 12.

**Tiêu chí chấp nhận**

1. Tài khoản không thể bán Base mình không sở hữu hoặc cấp vốn BUY bằng tín dụng.
2. Số dư khả dụng và số dư khóa luôn không âm; phí được áp dụng không thể tạo nghĩa vụ thiếu vốn.

#### FR-BAL-002 — Tài sản đã giữ không được dùng cho cam kết khác

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI ngăn tài sản đã giữ cho một cam kết được dùng để cấp vốn cho cam kết khác, kể cả các cam kết USDT giữa BTC/USDT, ETH/USDT và SOL/USDT.

**Truy vết nghiệp vụ**

- OQ-005
- BR-BAL-001
- BAL-INV-001
- BUSINESS.md, mục 12, 30.

**Tiêu chí chấp nhận**

1. USDT đã cam kết cho lệnh BTC còn hiệu lực không thể được lệnh ETH hoặc SOL dùng lại.
2. Base đã khóa không thể đồng thời cấp vốn cho hai cam kết SELL độc lập.

#### FR-BAL-003 — Kiểm tra đủ toàn bộ vốn trước chấp nhận và STP

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI kiểm tra vốn cho toàn bộ số lượng yêu cầu theo biên bảo vệ trước khi chấp nhận, tạo Trade hoặc có tác động STP. Thiếu vốn khả dụng PHẢI tạo REJECTED, không khớp hay âm thầm giảm số lượng, và KHÔNG ĐƯỢC dựa vào việc giải phóng lệnh đang chờ của chính tài khoản về sau.

**Truy vết nghiệp vụ**

- OQ-021
- OQ-022
- OQ-027
- BR-BAL-004
- BR-BAL-002
- BR-ORD-003
- BUSINESS.md, mục 8, 12, 13.

**Tiêu chí chấp nhận**

1. Nếu khoản giữ cần thiết là 180,360 USDT nhưng chỉ có 180,359 USDT khả dụng, yêu cầu là REJECTED dù thanh khoản đang hiển thị có vẻ tốn ít vốn hơn.
2. Với một lệnh đang chờ của chính tài khoản vốn đủ điều kiện đối ứng, yêu cầu mới vào thiếu vốn không tạo Trade và không hủy lệnh đang chờ đó.

#### FR-BAL-004 — Giữ vốn cho Limit Buy

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI giữ `ceil8(q × limit × (1 + max(rM, rT)))` USDT cho Limit Buy, trong đó q là số lượng Base còn hiệu lực và rM/rT là mức phí maker/taker đã cấu hình ở dạng tỷ lệ. Khoản giữ PHẢI đủ cho việc đổi vai trò từ Taker sang Maker về sau.

**Truy vết nghiệp vụ**

- OQ-013
- OQ-014
- OQ-015
- OQ-016
- OQ-022
- OQ-046
- BR-BAL-002
- BR-FEE-001
- BR-PREC-001
- BUSINESS.md, mục 12.

**Tiêu chí chấp nhận**

1. Với 1 BTC tại 80,000 USDT/BTC, maker 0.1% và taker 0.2%, khoản giữ cần thiết là 80,160 USDT.
2. Nếu maker lớn hơn taker, mức maker lớn hơn vẫn quyết định khoản giữ của Limit Buy; làm tròn lên số cần giữ đến 8 chữ số thập phân USDT.

#### FR-BAL-005 — Giữ vốn cho Market Buy có bảo vệ giá

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI giữ `ceil8(q × maximum execution price × (1 + rT))` USDT cho Market Buy, dùng toàn bộ số lượng Base yêu cầu và mức phí taker thay vì chi phí ước tính từ sổ đang thấy.

**Truy vết nghiệp vụ**

- OQ-008
- OQ-015
- OQ-016
- OQ-022
- OQ-046
- BR-BAL-002
- BR-PREC-001
- BUSINESS.md, mục 12.

**Tiêu chí chấp nhận**

1. Với 2 BTC có trần 90,000 USDT/BTC và taker 0.2%, khoản giữ cần thiết là 180,360 USDT.
2. Phần vốn giữ cho phí không cho phép khớp trên mức giá tối đa 90,000 USDT/BTC.

#### FR-BAL-006 — Giữ Base cho Sell

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI giữ số lượng Base còn hiệu lực của SELL cho cả Limit Sell và Market Sell, không giữ thêm USDT; phí USDT của SELL PHẢI được trừ từ tiền bán thực tế.

**Truy vết nghiệp vụ**

- OQ-015
- OQ-016
- OQ-022
- BR-BAL-002
- BR-FEE-002
- BUSINESS.md, mục 12.

**Tiêu chí chấp nhận**

1. Với 3 BTC sở hữu, giữ vốn cho SELL 2 BTC để lại 1 BTC khả dụng và 2 BTC khóa.
2. Khớp 0.5 BTC để lại 1.5 BTC cam kết cho phần còn hiệu lực; phí bán được lấy từ số USDT thu được.

#### FR-BAL-007 — Tính lại khoản giữ và giải phóng dư sau từng Trade

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI hạch toán chi phí Trade thực tế, tính lại khoản giữ cho số lượng còn hiệu lực và giải phóng phần dư ngay sau mỗi Trade theo công thức giữ vốn tương ứng.

**Truy vết nghiệp vụ**

- OQ-014
- OQ-015
- OQ-016
- OQ-022
- OQ-023
- OQ-024
- OQ-046
- BR-BAL-002
- BR-BAL-003
- BR-STL-001
- BAL-INV-002
- BAL-INV-003
- BUSINESS.md, mục 12, 18.

**Tiêu chí chấp nhận**

1. Với Limit Buy 1 BTC tại 80,000 USDT/BTC, maker 0.1%, taker 0.2%, lần khớp taker 0.4 BTC tại 75,000 tốn 30,060 USDT và để lại khoản giữ 48,096 USDT.
2. Từ khoản giữ ban đầu 80,160 USDT, phần dư giải phóng là 2,004 USDT; nếu ban đầu có 100,000 USDT thì khả dụng trở thành 21,844 và khóa là 48,096 USDT.

#### FR-BAL-008 — Giải phóng khoản giữ chưa dùng khi kết thúc

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI giải phóng toàn bộ khoản giữ chưa dùng khi phần còn lại của lệnh kết thúc, không hoàn lại tài sản đã trao đổi hoặc phí đã trả. Quan sát lặp hoặc hủy một lệnh đã kết thúc KHÔNG ĐƯỢC giải phóng cùng khoản vốn lần nữa.

**Truy vết nghiệp vụ**

- OQ-014
- OQ-015
- OQ-016
- OQ-022
- OQ-023
- OQ-024
- OQ-027
- OQ-031
- OQ-046
- BR-BAL-002
- BR-BAL-003
- BR-STL-001
- BR-ORD-003
- BR-ORD-007
- BR-FEE-001
- BR-FEE-002
- BR-PREC-001
- BAL-INV-002
- BAL-INV-003
- BUSINESS.md, mục 12, 14.

**Tiêu chí chấp nhận**

1. Trong ví dụ mua một phần có phí, hủy 0.6 BTC còn lại giải phóng 48,096 USDT và để lại 69,940 USDT khả dụng, 0 USDT khóa, đồng thời giữ nguyên 0.4 BTC đã mua.
2. Phí 60 USDT đã trả và notional 30,000 USDT đã thực hiện không được hoàn; lặp lại yêu cầu hủy không đổi số dư.

### 6.6 Trade / Settlement

#### FR-TRD-001 — Thông tin nghiệp vụ của Trade

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI mô tả mỗi Trade thực sự bằng người mua, người bán, cặp giao dịch, số lượng Base, giá thực hiện của lệnh đang chờ, thời điểm/thứ tự xảy ra, các lệnh tham gia, vai trò maker/taker theo Trade và phí từng bên. Đây PHẢI là các dữ kiện nghiệp vụ; thông tin công khai PHẢI tuân theo quy tắc công bố riêng.

**Truy vết nghiệp vụ**

- OQ-009
- OQ-014
- OQ-015
- OQ-016
- OQ-024
- OQ-037
- BR-ORD-005
- BR-FEE-001
- BR-FEE-002
- BR-STL-001
- BR-MKT-002
- TRD-INV-002
- BUSINESS.md, mục 16, 18.

**Tiêu chí chấp nhận**

1. Hai bên của cùng Trade thống nhất số lượng, cặp và giá thực hiện dù phí có thể khác nhau.
2. Gửi lệnh, giữ vốn, Simulation Funding và tự khớp bị ngăn không tạo bản ghi thực hiện giao dịch thực sự như vậy.

#### FR-TRD-002 — Chuyển giao quyền sở hữu ngay

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI settlement ngay từng Trade thực sự: người mua nhận đủ Base và trả notional cộng phí mua; người bán giao lượng Base đó và nhận notional trừ phí bán; Fee Treasury nhận cả hai khoản phí. Base nhận được và USDT thuần thu được PHẢI khả dụng ngay, không có giai đoạn số dư chờ settlement.

**Truy vết nghiệp vụ**

- OQ-014
- OQ-015
- OQ-016
- OQ-023
- OQ-024
- OQ-046
- BR-STL-001
- BR-BAL-003
- BR-FEE-001
- BR-FEE-002
- STL-INV-001
- STL-INV-002
- BUSINESS.md, mục 18.

**Tiêu chí chấp nhận**

1. Với 1 BTC tại 86,000 USDT/BTC, người mua Taker 0.2% và người bán Maker 0.1%, người mua trả 86,172 USDT, người bán nhận 85,914 USDT và treasury nhận 258 USDT.
2. Chỉ số lượng đã khớp được chuyển giao; số lượng chưa khớp còn lại không tạo trao đổi tài sản.

#### FR-TRD-003 — Tác động settlement đúng một lần

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI áp dụng các lần chuyển tài sản và phí của mỗi Trade đúng một lần. Báo cáo hoặc quan sát lại Trade đó KHÔNG ĐƯỢC settlement lần nữa, tăng lại tổng số lượng đã khớp hoặc giải phóng vốn lần nữa.

**Truy vết nghiệp vụ**

- OQ-024
- OQ-046
- BR-STL-001
- STL-INV-003
- TRD-INV-003
- BUSINESS.md, mục 18, 30.

**Tiêu chí chấp nhận**

1. Sau khi một Trade được settlement, trình bày lại cùng Trade không làm đổi số dư của bất kỳ người tham gia hoặc treasury nào.
2. Số lượng đã khớp của mỗi lệnh tham gia chỉ tính số lượng Trade đó một lần.

#### FR-TRD-004 — Lịch sử Trade bất biến

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI giữ các Trade đã xác nhận như dữ kiện lịch sử bất biến và V1 KHÔNG ĐƯỢC hỗ trợ chỉnh sửa, xóa, đảo ngược, hiệu chỉnh ngoại lệ hoặc viết lại chúng bằng quyền quản trị. Simulation Funding KHÔNG ĐƯỢC dùng để che giấu việc hiệu chỉnh Trade.

**Truy vết nghiệp vụ**

- OQ-045
- BR-TRD-001
- TRD-INV-001
- BUSINESS.md, mục 16, 32.

**Tiêu chí chấp nhận**

1. Quản trị viên không thể đổi giá, số lượng, đối tác hoặc phí của Trade đã hoàn tất, xóa Trade hay hoàn tác việc chuyển quyền sở hữu của nó.
2. Thí nghiệm không hợp lệ vẫn được giữ với cách xử lý tính hợp lệ nghiên cứu tại FR-ADM-004, thay vì sửa lịch sử Trade.

### 6.7 Phí

#### FR-FEE-001 — Phí mô phỏng cấu hình được

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI hỗ trợ cấu hình tường minh mức phí Maker và Taker mô phỏng, có thể bằng nhau, khác nhau hoặc bằng không. Phí đang áp dụng PHẢI được phản ánh nhất quán trong kiểm tra vốn, khoản giữ, settlement, số dư, kết quả chiến lược và PnL được hỗ trợ.

**Truy vết nghiệp vụ**

- OQ-013
- OQ-014
- BR-FEE-001
- BUSINESS.md, mục 26.

**Tiêu chí chấp nhận**

1. Kịch bản cấu hình phí bằng không không phát sinh phí giao dịch; kịch bản có mức Maker/Taker khác nhau dùng vai trò tương ứng của từng Trade.
2. Phí đã thu được phản ánh nhất quán trong các thông tin số dư/hiệu quả được hỗ trợ; chọn mức không trong ví dụ không tạo mặc định cho mọi thí nghiệm.

#### FR-FEE-002 — Maker và Taker theo từng Trade

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI xác định phía đang chờ là Maker và phía mới vào là Taker riêng cho từng Trade. Hệ thống KHÔNG ĐƯỢC gán một vai trò Maker/Taker cố định cho toàn bộ lệnh.

**Truy vết nghiệp vụ**

- OQ-014
- BR-FEE-001
- BUSINESS.md, mục 16, 26.

**Tiêu chí chấp nhận**

1. Limit Buy mới vào 2 BTC khớp với Ask đang chờ 1 BTC trong vai trò Taker; phần còn lại 1 BTC của nó nằm chờ và là Maker khi SELL đến sau khớp với nó.
2. Mỗi lần thực hiện dùng vai trò của lần đó dù cả hai lần khớp thuộc cùng lệnh BUY.

#### FR-FEE-003 — Fee Treasury bằng USDT

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI thu phí cả hai bên bằng USDT và ghi có đúng các khoản đó vào Fee Treasury thuộc nền tảng trong thí nghiệm, không tham gia giao dịch và ban đầu bằng không. Phí mua PHẢI được trả thêm ngoài notional; phí bán PHẢI lấy từ tiền bán. Khoản treasury nhận KHÔNG ĐƯỢC tính là lợi nhuận chiến lược.

**Truy vết nghiệp vụ**

- OQ-015
- BR-FEE-002
- BUSINESS.md, mục 18, 26.

**Tiêu chí chấp nhận**

1. Treasury của thí nghiệm mới bắt đầu với 0 USDT; tổng phí trừ của người mua và người bán đúng bằng phần tăng của treasury.
2. Vốn treasury không thể gửi lệnh giao dịch và không được ghi nhận là lợi nhuận giao dịch của người tham gia.

#### FR-FEE-004 — Tính và làm tròn phí theo từng Trade

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI tính phí từng bên cho từng Trade bằng `floor8(notional × applicable rate)` USDT. Hệ thống KHÔNG ĐƯỢC áp dụng phí tối thiểu, hoàn phí, chiết khấu, token phí riêng, phí làm tròn ẩn hoặc tính phí gộp theo toàn lệnh.

**Truy vết nghiệp vụ**

- OQ-014
- OQ-015
- OQ-016
- OQ-046
- BR-FEE-002
- BR-PREC-001
- BUSINESS.md, mục 26, 32.

**Tiêu chí chấp nhận**

1. Với notional 86,000 USDT, mức 0.1% tạo phí 86 USDT và mức 0.2% tạo phí 172 USDT.
2. Phí tính chính xác 0.000000000001 USDT được làm tròn xuống 0.00000000 USDT, không có mức phí tối thiểu; phí được làm tròn xuống riêng cho mỗi Trade thực sự và từng bên.

#### FR-FEE-005 — Kiểm tra mức phí và cố định trong thí nghiệm

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI yêu cầu khai báo tường minh mức Maker và Taker lúc thiết lập, mỗi mức trong khoảng gồm cả hai đầu 0% đến 1%, theo bước 0.01 điểm phần trăm (0.0001 ở dạng tỷ lệ). Hệ thống PHẢI giữ cả hai mức cố định trong thí nghiệm và KHÔNG ĐƯỢC suy ra mặc định ngầm.

**Truy vết nghiệp vụ**

- OQ-016
- OQ-042
- BR-FEE-002
- BR-SIM-002
- BUSINESS.md, mục 26, 32.

**Tiêu chí chấp nhận**

1. 0%, 0.01% và 1% đạt kiểm tra mức phí; mức âm, 1.01% và 0.015% không đạt.
2. Thí nghiệm đang chạy không thể đổi một trong hai mức; so sánh với mức khác phải dùng thí nghiệm riêng đã cấu hình.

### 6.8 Dữ liệu thị trường

#### FR-MKT-001 — Chính xác các thị trường V1

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI hỗ trợ đúng BTC/USDT, ETH/USDT và SOL/USDT, dùng USDT làm Quote Asset chung. V1 KHÔNG ĐƯỢC cung cấp Quote Asset khác hoặc cặp chéo.

**Truy vết nghiệp vụ**

- OQ-001
- OQ-002
- BR-MKT-003
- BUSINESS.md, mục 3, 5.

**Tiêu chí chấp nhận**

1. Mỗi thị trường hỗ trợ xác định BTC, ETH hoặc SOL là Base và USDT là Quote.
2. BTC/ETH và mọi cặp bổ sung đều ngoài tập thị trường V1.

#### FR-MKT-002 — Toàn bộ độ sâu sổ lệnh tổng hợp

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI hiển thị toàn bộ số lượng đang chờ còn hiệu lực được tổng hợp tại mỗi mức giá của từng cặp. Việc tổng hợp KHÔNG ĐƯỢC gộp quyền sở hữu hoặc ưu tiên chấp nhận của từng lệnh.

**Truy vết nghiệp vụ**

- OQ-037
- BR-MKT-002
- BUSINESS.md, mục 9, 23.

**Tiêu chí chấp nhận**

1. Hai Ask còn hiệu lực 0.4 và 0.6 BTC tại 86,000 USDT/BTC xuất hiện thành 1 BTC ở mức giá đó nhưng vẫn giữ ưu tiên khớp riêng.
2. Mọi mức giá còn hiệu lực đều được thể hiện; hủy phần còn lại làm giảm số lượng tại mức giá của nó dù không có Trade.

#### FR-MKT-003 — Quan sát hàng chờ và kết quả của chính tài khoản

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI hiển thị cho mỗi người tham gia các lệnh còn hiệu lực của chính mình, ưu tiên chấp nhận, số lệnh và lượng Base còn hiệu lực đứng trước tại cùng giá, các lần khớp, phí, số dư và lý do kết thúc. Quan sát hàng chờ PHẢI tính mọi lệnh cùng giá được chấp nhận trước và còn hiệu lực, đồng thời KHÔNG ĐƯỢC mang ý nghĩa bảo đảm thực hiện trong tương lai.

**Truy vết nghiệp vụ**

- OQ-010
- OQ-027
- OQ-037
- BR-ORD-001
- BR-ORD-003
- BR-MKT-002
- BUSINESS.md, mục 23.

**Tiêu chí chấp nhận**

1. Nếu lệnh đang chờ của mình đứng sau hai lượng cùng giá 0.4 và 0.6 BTC, quan sát là hai lệnh và 1 BTC phía trước.
2. Quan sát về sau phản ánh những lệnh phía trước đã khớp hoặc kết thúc; vị trí hiển thị không hứa hẹn sẽ khớp.

#### FR-MKT-004 — Lịch sử Trade công khai ẩn danh

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI cung cấp lịch sử Trade thực sự ở dạng ẩn danh và KHÔNG ĐƯỢC tiết lộ danh tính hoặc lịch sử lệnh riêng lẻ của tài khoản khác trong thông tin thị trường cho người tham gia.

**Truy vết nghiệp vụ**

- OQ-037
- BR-MKT-002
- BUSINESS.md, mục 16, 23.

**Tiêu chí chấp nhận**

1. Người tham gia có thể quan sát giá và số lượng thực hiện công khai mà không nhận danh tính đối tác hoặc lịch sử lệnh của tài khoản khác.
2. Chi tiết lệnh của chính mình vẫn khả dụng theo FR-MKT-003 mà không công khai cho tài khoản khác.

#### FR-MKT-005 — Giá chào tốt nhất và giá trị không khả dụng

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI báo Best Bid là Bid còn hiệu lực cao nhất, Best Ask là Ask còn hiệu lực thấp nhất và Spread bằng Best Ask trừ Best Bid. Nếu thiếu một phía thì giá tốt nhất phía đó PHẢI không khả dụng; nếu thiếu bất kỳ phía nào thì Spread PHẢI không khả dụng thay vì bằng không.

**Truy vết nghiệp vụ**

- OQ-037
- OQ-044
- BR-MKT-001
- BR-MKT-002
- BUSINESS.md, mục 9, 19, 32.

**Tiêu chí chấp nhận**

1. Best Bid 85,500 và Best Ask 86,000 USDT/BTC tạo Spread 500 USDT/BTC.
2. Sổ chỉ có một phía có giá phía thiếu và Spread không khả dụng, không dùng giá tham chiếu kịch bản làm giá chào.

#### FR-MKT-006 — Last Price chỉ từ Trade thực sự

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI để Last Price không khả dụng cho đến Trade thực sự đầu tiên, sau đó dùng Trade thực sự mới nhất theo thứ tự thực hiện chính thức. Hệ thống KHÔNG ĐƯỢC thay Last Price bằng Simulation Funding, giá tham chiếu mua vào của kịch bản, giá chào của lệnh, hủy lệnh, tự khớp bị ngăn hoặc giá bên ngoài.

**Truy vết nghiệp vụ**

- OQ-003
- OQ-011
- OQ-044
- BR-MKT-001
- BR-SIM-001
- BR-ORD-006
- MKT-INV-001
- SIM-INV-001
- TRD-INV-004
- BUSINESS.md, mục 19, 23, 32.

**Tiêu chí chấp nhận**

1. Trước mọi Trade, cấp vốn và lệnh đang chờ vẫn để Last Price không khả dụng.
2. Sau Trade tại 87,000 USDT/BTC, hủy lệnh chào giá thay đổi độ sâu nhưng giữ Last Price 87,000; chỉ Trade thực sự về sau mới cập nhật nó.

#### FR-MKT-007 — Khối lượng Base và giá trị Quote trước phí

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI tính mỗi Trade thực sự một lần theo từng cặp, bằng số lượng Base và notional USDT trước phí, cho mỗi khoảng một phút của thí nghiệm và lũy kế cả thí nghiệm. Hệ thống PHẢI loại trừ phí, gửi lệnh, cấp vốn, hủy và STP, đồng thời KHÔNG ĐƯỢC cung cấp khối lượng cửa sổ trượt 24 giờ trong V1.

**Truy vết nghiệp vụ**

- OQ-003
- OQ-011
- OQ-035
- OQ-038
- OQ-039
- BR-MKT-001
- BR-MKT-004
- MKT-INV-002
- BUSINESS.md, mục 23, 24.

**Tiêu chí chấp nhận**

1. Một Trade 0.5 BTC tại 86,000 USDT/BTC thêm 0.5 BTC và 43,000 USDT khối lượng, không nhân đôi vì có hai đối tác.
2. Phí và hoạt động không phải thực hiện giao dịch không thêm khối lượng; tổng lũy kế bằng tổng các Trade thực sự trong thí nghiệm.

#### FR-MKT-008 — OHLCV một phút

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI tạo OHLCV một phút theo giờ UTC ảo của thí nghiệm, tính cả đầu khoảng và loại trừ cuối khoảng. Open/Close PHẢI dùng giá Trade thực sự đầu/cuối theo thứ tự thực hiện chính thức, High/Low là cực đại/cực tiểu và khối lượng là tổng Trade thực sự. Các Trade cùng thời điểm PHẢI theo thứ tự thực hiện.

**Truy vết nghiệp vụ**

- OQ-035
- OQ-038
- OQ-039
- BR-MKT-001
- BR-MKT-004
- BUSINESS.md, mục 24.

**Tiêu chí chấp nhận**

1. Với phút trong nguồn có các giá 86,000, 88,000, 85,000, 87,000 và số lượng tương ứng 1, 0.5, 2, 0.5 BTC, OHLC là 86,000/88,000/85,000/87,000 USDT/BTC; khối lượng là 4 BTC và 343,500 USDT.
2. Trade đúng tại 10:01:00 thuộc khoảng bắt đầu 10:01:00, không phải phút trước; thứ tự đầu/cuối của các Trade cùng thời điểm theo thứ tự thực hiện nghiệp vụ.

#### FR-MKT-009 — Nến trống và nến cuối chưa đủ phút

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI báo OHLC không khả dụng và khối lượng Base/Quote bằng không cho khoảng trống, không mang giá trước sang để tạo giá nến giả. Hệ thống PHẢI giữ và gắn nhãn khoảng cuối chưa hoàn tất khi thí nghiệm kết thúc. Last Price CÓ THỂ tiếp tục giữ riêng giá của Trade thực sự gần nhất.

**Truy vết nghiệp vụ**

- OQ-035
- OQ-039
- OQ-044
- BR-MKT-001
- BR-MKT-004
- BUSINESS.md, mục 24.

**Tiêu chí chấp nhận**

1. Phút trống sau một Trade có OHLC không khả dụng và khối lượng bằng không dù Last Price vẫn biết được.
2. Nếu kết thúc giữa một phút, nến được giữ của phút đó được gắn nhãn khoảng cuối chưa đủ phút.

### 6.9 Danh mục / PnL

#### FR-PF-001 — Số dư và chỉ tiêu danh mục

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI báo số dư khả dụng, khóa và tổng theo từng tài sản; vị thế spot gồm cả tài sản khóa; phí; giá vốn bình quân gia quyền; PnL đã/chưa thực hiện theo từng Base bằng USDT; và tổng giá trị danh mục bằng USDT khi định giá được mọi tài sản nắm giữ khác không. Tổng số dư PHẢI bằng khả dụng cộng khóa.

**Truy vết nghiệp vụ**

- OQ-024
- OQ-033
- OQ-034
- BR-STL-001
- BR-PF-001
- BUSINESS.md, mục 12, 25.

**Tiêu chí chấp nhận**

1. Giữ vốn chuyển tài sản từ khả dụng sang khóa mà không làm giảm vị thế hoặc tổng lượng sở hữu.
2. Số dư đã biết và PnL đã thực hiện vẫn khả dụng ngay cả khi một phần định giá thị trường không khả dụng.

#### FR-PF-002 — Giá vốn bình quân gia quyền và PnL đã thực hiện

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI chỉ dùng giá vốn bình quân gia quyền: BUY cộng notional và phí mua vào tổng giá vốn; SELL loại `floor8(basis before × sold quantity / quantity before)` USDT khỏi giá vốn, trừ khi bán hết thì PHẢI loại toàn bộ giá vốn còn lại. PnL đã thực hiện PHẢI bằng tiền bán thuần trừ giá vốn đã loại. Giá vốn bình quân PHẢI bằng tổng giá vốn chia số lượng nắm giữ và không khả dụng khi lượng nắm giữ bằng không.

**Truy vết nghiệp vụ**

- OQ-015
- OQ-016
- OQ-033
- OQ-046
- BR-PF-001
- BR-FEE-002
- BR-PREC-001
- BUSINESS.md, mục 25.

**Tiêu chí chấp nhận**

1. Trong trường hợp không phí, mua 1 BTC tại 80,000 và 1 BTC tại 90,000 tạo tổng giá vốn 170,000 USDT và bình quân 85,000 USDT/BTC; bán 0.5 BTC tại 88,000 tạo PnL đã thực hiện 1,500 USDT và còn giá vốn 127,500 USDT.
2. Phần dư do làm tròn tỷ lệ ở lại vị thế còn lại; lần bán hết cuối cùng loại toàn bộ giá vốn dư, không trừ phí hai lần.

#### FR-PF-003 — Giá vốn của tài sản được cấp

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI gán cho Base được cấp một giá tham chiếu mua vào kịch bản khai báo tường minh, hợp lệ trên bước giá của cặp, và giá vốn ban đầu bằng số lượng được cấp nhân giá đó. Hệ thống PHẢI tách vốn được cấp, PnL đã/chưa thực hiện và phí; cấp vốn KHÔNG ĐƯỢC tạo lợi nhuận giao dịch hoặc Last Price.

**Truy vết nghiệp vụ**

- OQ-003
- OQ-017
- OQ-018
- OQ-033
- OQ-042
- OQ-046
- BR-PF-001
- BR-SIM-001
- BR-SIM-002
- BR-PREC-001
- SIM-INV-001
- BUSINESS.md, mục 25.

**Tiêu chí chấp nhận**

1. Cấp 1 BTC với giá tham chiếu mua vào đã khai báo 80,000 USDT/BTC ghi nhận giá vốn 80,000 USDT và vốn cấp, nhưng không có Trade, lợi nhuận giao dịch hoặc Last Price.
2. USDT được cấp là vốn, không phải lợi nhuận; phí đã tính trong giá vốn/tiền thu không bị trừ lần thứ hai khi đo hiệu quả.

#### FR-PF-004 — Định giá theo Last Price và độ cũ của giá

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI định giá Base nắm giữ khác không bằng Last Price của thí nghiệm và tính PnL chưa thực hiện bằng giá trị định giá trừ giá vốn còn lại, không trừ phí bán giả định trong tương lai. Nếu không có giá đó, định giá tài sản nắm giữ, PnL chưa thực hiện tương ứng và tổng giá trị danh mục PHẢI không khả dụng. Last Price cũ hơn 60 giây thí nghiệm PHẢI được giữ nhưng gắn cờ cũ kèm tuổi giá; các giá trị PHẢI được mô tả là tham khảo, không bảo đảm tiền thu khi thanh lý.

**Truy vết nghiệp vụ**

- OQ-033
- OQ-034
- OQ-044
- OQ-046
- BR-PF-001
- BR-MKT-001
- BR-PREC-001
- BUSINESS.md, mục 19, 25.

**Tiêu chí chấp nhận**

1. Với 1.5 BTC, giá vốn 127,500 USDT và Last Price 87,000 USDT/BTC, giá trị định giá là 130,500 và PnL chưa thực hiện 3,000 USDT.
2. Trước Trade đầu tiên, giá vốn tham chiếu của Base được cấp không thay thế giá định giá còn thiếu; số dư đã biết và PnL đã thực hiện vẫn hiển thị.
3. Last Price có tuổi đúng 60 giây chưa phải cũ hơn 60; khi tuổi lớn hơn thì được gắn cờ cũ và giữ lại.

### 6.10 Giao dịch thuật toán

#### FR-ALGO-001 — Chiến lược tham gia theo quy tắc thông thường

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI hỗ trợ chiến lược giao dịch thuật toán quan sát thông tin thị trường mô phỏng và vốn riêng để gửi lệnh V1 thông thường, hủy lệnh còn hiệu lực của mình hoặc không hành động. Chiến lược PHẢI tuân theo các quy tắc vốn, ưu tiên, bảo vệ giá và STP thông thường, đồng thời đánh giá số lượng thực sự khớp thay vì giả định đã thực hiện toàn bộ yêu cầu.

**Truy vết nghiệp vụ**

- OQ-004
- OQ-005
- OQ-008
- OQ-010
- OQ-011
- OQ-040
- OQ-043
- BR-ALGO-001
- BR-ACT-002
- BR-ORD-004
- BR-BAL-001
- BR-ORD-001
- BR-ORD-006
- BUSINESS.md, mục 28.

**Tiêu chí chấp nhận**

1. Chiến lược yêu cầu 2 BTC nhưng chỉ nhận 0.5 BTC phải quan sát đúng lần khớp đó và không thể coi số lượng chưa khớp là tài sản đã sở hữu.
2. Chiến lược không có đặc quyền khớp, vốn dùng chung hoặc quyền vượt ưu tiên hàng chờ so với người tham gia thông thường.

#### FR-ALGO-002 — Chạy lại kịch bản tổng hợp và phạm vi nghiên cứu

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI hỗ trợ chạy lại kịch bản tổng hợp có kiểm soát dưới dạng thí nghiệm riêng, với vốn ban đầu, mức phí, lịch hành động, giả định thời gian và quyết định của người tham gia được khai báo. V1 KHÔNG ĐƯỢC cung cấp backtesting dữ liệu lịch sử, bộ công cụ tối ưu hóa hoặc chợ chiến lược, hay tuyên bố lần chạy tổng hợp chứng minh hiệu quả trên thị trường lịch sử.

**Truy vết nghiệp vụ**

- OQ-035
- OQ-040
- OQ-041
- OQ-042
- BR-ALGO-001
- BR-SIM-002
- BUSINESS.md, mục 28, 29.

**Tiêu chí chấp nhận**

1. So sánh lặp lại xác định các đầu vào kịch bản và quyết định của người tham gia được dùng trong từng lần chạy riêng; đổi quyết định chiến lược là đổi đầu vào.
2. Phát lại dữ liệu lịch sử không được trình bày là khả năng V1; chạy lại tổng hợp không tạo tuyên bố hiệu quả trên thị trường thật.

### 6.11 Thí nghiệm HFT

#### FR-HFT-001 — Độ trễ quan sát và hành động

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI hỗ trợ độ trễ quan sát và hành động không âm, được khai báo riêng cho từng người tham gia, cố định lúc thiết lập cho một thí nghiệm và đo theo đồng hồ thí nghiệm ảo. Độ trễ quan sát PHẢI ảnh hưởng thông tin thị trường nào đã đến người tham gia; độ trễ hành động PHẢI ảnh hưởng thời điểm lệnh/yêu cầu hủy đi vào thứ tự nghiệp vụ chính thức. Độ trễ KHÔNG ĐƯỢC vượt qua ưu tiên hiện có; jitter thời gian ngẫu nhiên PHẢI được hoãn.

**Truy vết nghiệp vụ**

- OQ-010
- OQ-031
- OQ-035
- OQ-041
- BR-ALGO-001
- BR-ORD-001
- BR-ORD-007
- BUSINESS.md, mục 29.

**Tiêu chí chấp nhận**

1. Cho phép độ trễ bằng không; độ trễ âm không hợp lệ và không thể thay đổi độ trễ đã cấu hình trong thí nghiệm.
2. Quan sát trước đó có thể khác sổ lệnh khi hành động phát sinh có hiệu lực; thay đổi độ trễ ở lần chạy khác không thể đổi thứ tự các lệnh đã chấp nhận.
3. Độ phân giải đồng hồ và chi tiết phân phối thông tin có độ trễ vẫn là TBD-002, không cho phép bổ sung chính sách ưu tiên mới.

#### FR-HFT-002 — Chỉ tiêu số lượng, giá và chi phí thực hiện

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI báo theo từng lệnh: tỷ lệ khớp (`filled / original`), giá thực hiện bình quân gia quyền theo số lượng (`executed notional / filled Base`), phí thực tế, chi phí mua bình quân gồm phí hoặc tiền bán thuần trên mỗi đơn vị Base đã khớp, số lệnh/số lượng cùng giá đứng trước lệnh của mình và PnL danh mục kết quả. Giá/chi phí bình quân PHẢI không khả dụng khi không có phần nào khớp.

**Truy vết nghiệp vụ**

- OQ-013
- OQ-014
- OQ-015
- OQ-016
- OQ-033
- OQ-037
- OQ-041
- OQ-046
- BR-ALGO-001
- BR-FEE-001
- BR-FEE-002
- BR-PF-001
- BR-MKT-002
- BR-PREC-001
- BUSINESS.md, mục 19, 29.

**Tiêu chí chấp nhận**

1. Với 1 BTC tại 86,000 và 1 BTC tại 87,000 trên yêu cầu 2 BTC, tỷ lệ khớp là 1 và giá thực hiện bình quân là 86,500 USDT/BTC trước phí.
2. Yêu cầu không khớp có tỷ lệ khớp 0 nhưng giá thực hiện bình quân và chi phí/tiền thu đơn vị không khả dụng, thay vì giá bằng không.

#### FR-HFT-003 — Trượt giá so với giá đã quan sát khi quyết định

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI đo trượt giá so với giá đối ứng tốt nhất mà người tham gia thực sự quan sát lúc quyết định: Ask cho BUY và Bid cho SELL. Trượt giá bất lợi trên mỗi đơn vị PHẢI bằng bình quân trừ tham chiếu với BUY và tham chiếu trừ bình quân với SELL; phần trăm PHẢI bằng chênh lệch đó chia tham chiếu nhân 100. Thiếu giá tham chiếu hoặc lần khớp PHẢI cho trượt giá không khả dụng. Chỉ tiêu này KHÔNG ĐƯỢC thay thế bảo vệ giá bắt buộc của Market.

**Truy vết nghiệp vụ**

- OQ-008
- OQ-041
- OQ-046
- BR-ORD-004
- BR-ALGO-001
- BR-PREC-001
- BUSINESS.md, mục 21, 29.

**Tiêu chí chấp nhận**

1. BUY bình quân 86,500 so với Ask quan sát 86,000 USDT/BTC cho trượt giá bất lợi 500 USDT/BTC và hiển thị 0.5814%; SELL bình quân 85,000 so với Bid quan sát 85,500 cũng bất lợi 500 USDT/BTC.
2. Giá được cải thiện cho trượt giá bất lợi âm; không có giá đã quan sát lúc quyết định hoặc không có lần khớp thì chỉ tiêu không khả dụng.

#### FR-HFT-004 — Thời gian khớp và kết quả hủy

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI báo thời gian đến lần khớp đầu tiên, thời gian đến khi khớp hết và kết quả hủy có hiệu lực theo đồng hồ thí nghiệm. Nếu chưa có lần khớp đầu/khớp hết thì chỉ tiêu thời gian tương ứng PHẢI không khả dụng thay vì bằng không; thời gian hiển thị trùng nhau PHẢI vẫn theo thứ tự nghiệp vụ chính thức. Mốc bắt đầu đo PHẢI giữ là TBD-003 cho đến khi được làm rõ trong cơ sở nghiệp vụ.

**Truy vết nghiệp vụ**

- OQ-026
- OQ-031
- OQ-035
- OQ-041
- BR-ALGO-001
- BR-ORD-003
- BR-ORD-007
- BUSINESS.md, mục 29.

**Tiêu chí chấp nhận**

1. Lệnh có một lần khớp một phần nhưng chưa khớp hết có thời gian đến khớp hết không khả dụng; lệnh chưa khớp lần nào cũng có thời gian đến lần khớp đầu không khả dụng.
2. Báo cáo thời gian phân biệt hủy thành công với quá muộn/đã kết thúc; việc kiểm chứng giá trị thời gian chính xác còn chờ TBD-003.

### 6.12 Quản trị

#### FR-ADM-001 — Simulation Funding không tạo thực hiện giao dịch giả

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI cho phép quản trị viên cấp tài sản mô phỏng đã duyệt cho tài khoản thanh khoản/kịch bản chuyên biệt và PHẢI nhận diện các khoản cấp này là Simulation Funding. Cấp vốn KHÔNG ĐƯỢC tạo Trade, thực hiện giao dịch, khối lượng, lợi nhuận giao dịch hoặc Last Price; quản trị viên KHÔNG ĐƯỢC tạo Trade trực tiếp hoặc ép khớp để đáp ứng lệnh.

**Truy vết nghiệp vụ**

- OQ-003
- BR-SIM-001
- SIM-INV-001
- BUSINESS.md, mục 5, 23, 27.

**Tiêu chí chấp nhận**

1. Cấp BTC và USDT thay đổi số dư được cấp nhưng không tạo thực hiện giao dịch, khối lượng, PnL giao dịch hoặc giá thị trường.
2. Thanh khoản xuất hiện qua lệnh thông thường được gửi, không phải biến khoản cấp vốn thành đối ứng bị ép buộc.

#### FR-ADM-002 — Tài khoản được cấp vốn không có đặc quyền giao dịch

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI áp dụng kiểm tra vốn, ưu tiên khớp và STP theo tài khoản thông thường cho tài khoản được quản trị viên cấp vốn, không miễn trừ hoặc ép thực hiện.

**Truy vết nghiệp vụ**

- OQ-003
- OQ-005
- OQ-010
- OQ-011
- BR-ACT-001
- BR-BAL-001
- BR-ORD-001
- BR-ORD-006
- BUSINESS.md, mục 4, 27.

**Tiêu chí chấp nhận**

1. Lệnh kịch bản đến sau không thể vượt lệnh người giao dịch đến trước cùng giá chỉ vì quản trị viên đã cấp tài sản cho nó.
2. Nghĩa vụ kịch bản thiếu vốn hoặc lần khớp cùng tài khoản không được hưởng ngoại lệ quản trị.

#### FR-ADM-003 — Cấu hình kịch bản chỉ lúc thiết lập

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI cho phép quản trị viên khai báo vốn ban đầu, giá tham chiếu mua vào của tài sản được cấp, mức Maker/Taker, lịch hành động kịch bản và độ trễ từng người tham gia trước khi bắt đầu. Tài khoản thông thường PHẢI bắt đầu với USDT đã cấu hình và Base bằng không; tài khoản kịch bản CÓ THỂ nhận Base. Số lượng Base được cấp lúc thiết lập PHẢI theo bước số lượng của cặp và khoản cấp USDT theo bước 0.00000001 USDT. Hệ thống KHÔNG ĐƯỢC cho phép cấp vốn trong lúc chạy, sửa Trade/giá tùy ý hoặc ép khớp.

**Truy vết nghiệp vụ**

- OQ-003
- OQ-016
- OQ-017
- OQ-018
- OQ-033
- OQ-035
- OQ-041
- OQ-042
- OQ-046
- BR-SIM-001
- BR-SIM-002
- BR-FEE-002
- BR-PF-001
- BR-PREC-001
- BR-ALGO-001
- BUSINESS.md, mục 5, 25–29, 32.

**Tiêu chí chấp nhận**

1. Thiết lập có số vốn, giá vốn tài sản được cấp, mức phí và giả định thời gian tường minh, không có mặc định chung; khoản cấp tuân theo các bước số đã khai báo.
2. Khi đã chạy, cấp vốn bổ sung hoặc thay đổi mức phí/độ trễ cố định không thể thay đổi thí nghiệm đó; so sánh với cấu hình khác dùng một thí nghiệm riêng.

#### FR-ADM-004 — Xử lý thí nghiệm không hợp lệ

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI giữ lịch sử của thí nghiệm không hợp lệ với nhãn tường minh về tính hợp lệ nghiên cứu, kết thúc nó và dùng thí nghiệm riêng để chạy mới. Hệ thống KHÔNG ĐƯỢC xóa lịch sử không hợp lệ hoặc che giấu hiệu chỉnh dưới dạng cấp vốn.

**Truy vết nghiệp vụ**

- OQ-035
- OQ-042
- OQ-045
- BR-SIM-002
- BR-TRD-001
- BUSINESS.md, mục 16, 32.

**Tiêu chí chấp nhận**

1. Gắn nhãn một lần chạy không hợp lệ cho nghiên cứu vẫn giữ nguyên Trade, giá, phí và kết quả gốc cùng nhãn đó.
2. Thí nghiệm thay thế nhận vốn ban đầu và lịch sử mới đã khai báo; các bản ghi Trade cũ không đổi.

## 7. Yêu cầu phi chức năng

Các yêu cầu này xác định chất lượng và ranh giới nghiệp vụ, không chọn kiến trúc hoặc công nghệ. Quy tắc nghiệp vụ bằng số đã có trong nguồn được phân biệt với mục tiêu hiệu năng vận hành chưa xác định.

### NFR-DET-001 — Ưu tiên xác định của lệnh đã chấp nhận

**Trạng thái:** APPROVED

**Yêu cầu**

Hệ thống PHẢI thiết lập thứ tự chấp nhận chính thức, duy nhất và xác định cho các lệnh đã chấp nhận, đồng thời PHẢI giải thích được ưu tiên cùng giá từ thứ tự đó, không phụ thuộc dấu thời gian máy khách và kích thước lệnh.

**Truy vết nghiệp vụ**

- OQ-010
- BR-ORD-001
- ORD-INV-005
- BUSINESS.md, mục 10, 30.

**Tiêu chí chấp nhận**

1. Với cùng thứ tự lệnh đã chấp nhận và cùng sổ đối ứng đủ điều kiện, lựa chọn theo giá/thứ tự có thể lặp lại, kể cả khi thời gian hiển thị bằng nhau.
2. Chỉ thay đổi dấu thời gian máy khách không làm đổi ưu tiên hàng chờ của lệnh đã chấp nhận.

### NFR-DET-002 — Kết quả nghiệp vụ xác định

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI tạo ranh giới từng Trade, làm tròn phí, thứ tự hủy/kết thúc và thứ tự nến khi trùng thời điểm nhất quán với cùng thứ tự hành động nghiệp vụ có hiệu lực và cùng cấu hình. Cách tạo ra thứ tự đó PHẢI nằm ngoài tài liệu này.

**Truy vết nghiệp vụ**

- OQ-016
- OQ-031
- OQ-035
- OQ-039
- OQ-046
- BR-ORD-007
- BR-FEE-002
- BR-MKT-004
- BR-PREC-001
- BUSINESS.md, mục 10, 14, 24, 32.

**Tiêu chí chấp nhận**

1. Lặp lại một cặp đủ điều kiện với cùng số lượng còn hiệu lực tạo cùng số lượng của một Trade và cùng kết quả làm tròn phí.
2. Kết quả Trade/hủy/kết thúc và OHLC khi trùng thời điểm theo thứ tự thực hiện chính thức đã khai báo, không dùng giờ máy khách để phân xử.

### NFR-COR-001 — Nhất quán tài sản và nghĩa vụ

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI bảo toàn từng tài sản mô phỏng trên tổng người tham gia và Fee Treasury khi giao dịch, hạch toán Simulation Funding riêng, giữ số dư khả dụng/khóa không âm và ngăn settlement hoặc giải phóng khoản giữ trùng. Riêng việc giữ/giải phóng PHẢI chỉ thay đổi khả năng sử dụng thay vì tổng quyền sở hữu.

**Truy vết nghiệp vụ**

- OQ-003
- OQ-005
- OQ-014
- OQ-015
- OQ-016
- OQ-022
- OQ-023
- OQ-024
- OQ-046
- BR-SIM-001
- BR-BAL-001
- BR-BAL-002
- BR-BAL-003
- BR-STL-001
- BR-FEE-001
- BR-FEE-002
- BR-PREC-001
- BAL-INV-001
- BAL-INV-002
- BAL-INV-003
- BAL-INV-004
- STL-INV-001
- STL-INV-002
- STL-INV-003
- BUSINESS.md, mục 12, 18, 30.

**Tiêu chí chấp nhận**

1. Trong ví dụ hai Trade của nguồn, USDT cuối cùng là 27,656 người mua + 171,828 người bán + 516 treasury = 200,000; BTC cuối cùng là 2 người mua + 1 người bán = 3 BTC.
2. Trước/sau giữ hoặc giải phóng khi không có Trade/cấp vốn, tổng đơn vị sở hữu không đổi; quan sát lại settlement đã có không thay đổi gì.

### NFR-COR-002 — Nghĩa vụ thập phân chính xác

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI biểu diễn mọi số dư tài sản bằng đơn vị chính xác đến 8 chữ số thập phân. Hệ thống PHẢI tính notional thực hiện chính xác từ giá và số lượng hợp lệ mà không làm tròn notional khi settlement, làm tròn xuống phí/giá vốn loại theo tỷ lệ đến 8 chữ số, làm tròn lên khoản giữ mua đến 8 chữ số và giữ lại phần dư vốn/giá vốn. Các quy tắc này KHÔNG ĐƯỢC lựa chọn kiểu dữ liệu lập trình.

**Truy vết nghiệp vụ**

- OQ-017
- OQ-018
- OQ-022
- OQ-033
- OQ-046
- BR-PREC-001
- BR-BAL-002
- BR-PF-001
- BUSINESS.md, mục 6, 12, 25, 32.

**Tiêu chí chấp nhận**

1. Tại bước giá 0.01 USDT/BTC và bước BTC 0.000001 BTC, notional 0.00000001 USDT là chính xác, không bị bỏ hoặc làm tròn lên đơn vị lớn hơn.
2. Bán hết loại toàn bộ giá vốn dư; vốn chưa dùng vẫn thuộc chủ sở hữu và giải phóng không làm mất đơn vị thập phân.

### NFR-COR-003 — Làm tròn hiển thị không ảnh hưởng nghĩa vụ

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI làm tròn half-up các giá/chi phí suy ra khi hiển thị đến 8 chữ số thập phân và phần trăm đến 4 chữ số thập phân. Làm tròn hiển thị KHÔNG ĐƯỢC thay đổi giá Trade, số lượng, phí, khoản giữ, số dư hoặc nghĩa vụ giá vốn.

**Truy vết nghiệp vụ**

- OQ-034
- OQ-041
- OQ-046
- BR-PREC-001
- BR-PF-001
- BR-ALGO-001
- BUSINESS.md, mục 19, 21, 25, 32.

**Tiêu chí chấp nhận**

1. Notional đã thực hiện 260,000 USDT trên 3 BTC hiển thị giá thực hiện bình quân 86,666.66666667 USDT/BTC.
2. Trượt giá bất lợi 500 / 86,000 × 100 hiển thị 0.5814%; giá trị hiển thị không được dùng để tính lại nghĩa vụ đã settlement.

### NFR-AUD-001 — Giải thích được kết quả thí nghiệm

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI cho phép giải thích kết quả của người tham gia thông qua ưu tiên chấp nhận của chính họ, lần khớp/phí thực tế, trạng thái/lý do cuối, kết quả hủy có hiệu lực và lịch sử thí nghiệm được giữ. Hệ thống PHẢI phân biệt cấp vốn, thực hiện giao dịch và nhãn tính hợp lệ nghiên cứu mà không tiết lộ lịch sử tài khoản khác bị cấm công bố.

**Truy vết nghiệp vụ**

- OQ-025
- OQ-026
- OQ-027
- OQ-031
- OQ-037
- OQ-042
- OQ-045
- BR-ORD-003
- BR-ORD-007
- BR-MKT-002
- BR-SIM-002
- BR-TRD-001
- BUSINESS.md, mục 13, 14, 16, 23, 32.

**Tiêu chí chấp nhận**

1. Lệnh đã khớp một phần rồi hủy có thể được giải thích bằng số lượng khớp lũy kế, lý do kết thúc và vốn chưa dùng được giải phóng, không khẳng định Trade trước đã bị đảo ngược.
2. Lần chạy không hợp lệ cho nghiên cứu vẫn giữ dữ kiện thực hiện gốc và nhãn tính hợp lệ tường minh. Chi tiết thời hạn lưu/quyền truy cập vẫn là TBD-005.

### NFR-REP-001 — Tái lập thí nghiệm tổng hợp

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI hỗ trợ tái lập kết quả giao dịch tổng hợp khi kịch bản, vốn cấp, mức phí, lịch hành động, giả định thời gian, quyết định người tham gia và thứ tự hành động có hiệu lực chính thức giống nhau. Quyết định hoặc thời gian thay đổi PHẢI được nhận diện là đầu vào thay đổi; khả năng tái lập KHÔNG ĐƯỢC mang ý nghĩa chứng minh lợi nhuận lịch sử hoặc thị trường thật.

**Truy vết nghiệp vụ**

- OQ-010
- OQ-016
- OQ-031
- OQ-035
- OQ-040
- OQ-041
- OQ-042
- OQ-046
- BR-ORD-001
- BR-ORD-007
- BR-FEE-002
- BR-SIM-002
- BR-ALGO-001
- BR-PREC-001
- BUSINESS.md, mục 28, 29, 32.

**Tiêu chí chấp nhận**

1. Hai lần chạy riêng có đầu vào khai báo và thứ tự hành động có hiệu lực giống nhau có thể được so sánh về sự bằng nhau của các lần khớp, giá, phí, số dư cuối và thống kê từ Trade.
2. Lần chạy đổi chiến lược hoặc độ trễ không được mô tả là tái lập với đầu vào giống nhau. Chi tiết thứ tự lịch trùng thời điểm vẫn là TBD-002.

### NFR-SEC-001 — Ranh giới quyền sở hữu và công bố nghiệp vụ

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI thực thi việc chỉ hủy lệnh của chính tài khoản, tách biệt vốn người tham gia và giữ riêng danh tính/lịch sử lệnh riêng lẻ của tài khoản khác trong quan sát của người tham gia. Hệ thống KHÔNG ĐƯỢC suy ra thiết kế định danh hoặc kiểm soát truy cập kỹ thuật từ mô tả chủ thể nghiệp vụ.

**Truy vết nghiệp vụ**

- OQ-031
- OQ-037
- OQ-043
- BR-ORD-007
- BR-MKT-002
- BR-ACT-002
- BUSINESS.md, mục 4, 14, 23.

**Tiêu chí chấp nhận**

1. Người tham gia không thể hủy lệnh của tài khoản khác, chi vốn của tài khoản đó hoặc lấy lịch sử lệnh riêng của nó qua thông tin dành cho chính tài khoản mình.
2. Thông tin Trade công khai vẫn ẩn danh; đăng ký, khả năng quan sát của quản trị viên và quyền chi tiết vẫn là TBD-004.

### NFR-SEC-002 — Giới hạn nghiệp vụ của quản trị viên

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI giới hạn quyền chuẩn bị kịch bản của quản trị viên để không thể tạo thực hiện giao dịch giả, vượt quy tắc giao dịch thông thường, cấp vốn trong lúc chạy hoặc viết lại lịch sử Trade. Kết quả không hợp lệ PHẢI vẫn là dữ kiện lịch sử có nhãn, thay vì Trade được quản trị viên hiệu chỉnh.

**Truy vết nghiệp vụ**

- OQ-003
- OQ-042
- OQ-045
- BR-SIM-001
- BR-SIM-002
- BR-TRD-001
- BUSINESS.md, mục 4, 16, 27, 32.

**Tiêu chí chấp nhận**

1. Cấp vốn quản trị không tạo Trade hoặc giá và không thể sửa một Trade trước bằng cách che giấu hiệu chỉnh.
2. Thí nghiệm đang chạy từ chối thay đổi cấp vốn/cấu hình bị quy tắc nghiệp vụ cấm, đồng thời giữ lịch sử hiện có.

### NFR-PERF-001 — Mục tiêu hiệu năng và năng lực vẫn là TBD

**Trạng thái:** PROVISIONAL

**Yêu cầu**

Hệ thống PHẢI phân biệt độ trễ quan sát/hành động ảo và kết quả thí nghiệm với hiệu năng nền tảng được đo. Các mục tiêu định lượng về thời gian đáp ứng, thông lượng, năng lực, số người tham gia đồng thời, tính sẵn sàng và phục hồi PHẢI giữ là TBD cho đến khi chủ dự án xác định tải công việc và quyết định nghiệm thu; V1 KHÔNG ĐƯỢC tuyên bố độ trễ HFT production hoặc kết quả đo mạng/phần cứng thật.

**Truy vết nghiệp vụ**

- OQ-041
- BR-ALGO-001
- BUSINESS.md, mục 29.

**Tiêu chí chấp nhận**

1. Đặc tả này không xác lập ngưỡng hiệu năng vận hành bằng số; quy tắc giá cũ sau 60 giây và nến một phút là ngữ nghĩa nghiệp vụ, không phải mục tiêu hiệu năng nền tảng.
2. Nghiệm thu hiệu năng được ghi rõ còn chờ TBD-006; riêng độ trễ hành động mô phỏng không phải bằng chứng tốc độ thực hiện thật.

## 8. Use case

Các luồng ngắn gọn dưới đây kết hợp các yêu cầu bên trên. Trạng thái áp dụng cho toàn bộ luồng, gồm cả kết quả thay thế.

### UC-001 — Gửi Limit Buy

**Trạng thái:** PROVISIONAL

**Chủ thể chính:** Người giao dịch thủ công (việc tham gia của chiến lược/kịch bản chịu quy định riêng)

**Điều kiện trước:** Tài khoản có đủ USDT khả dụng cho toàn bộ khoản giữ gồm phí; thí nghiệm đang chạy và yêu cầu đáp ứng quy tắc cặp, giá, số lượng.

**Kích hoạt:** Người tham gia gửi số lượng Base và giá BUY Limit.

**Luồng chính**

1. Kiểm tra yêu cầu và toàn bộ vốn trước chấp nhận hoặc STP; giữ USDT cần thiết và xác lập ưu tiên chấp nhận.
2. Khớp với Ask đủ điều kiện theo giá rồi thứ tự chấp nhận, áp dụng STP và giá thực hiện của lệnh đang chờ.
3. Settlement từng Trade và tính lại/giải phóng phần dư; để phần GTC còn lại nằm chờ với thứ tự gốc.

**Luồng thay thế / Thất bại:** Yêu cầu không hợp lệ/thiếu vốn là REJECTED mà không tạo tác động; không có Ask đủ điều kiện thì OPEN; khớp một phần còn dư thì PARTIALLY_FILLED; khớp hết thì FILLED.

**Điều kiện sau:** Các lần khớp, phí, số dư thực tế và phần còn lại đang chờ đủ vốn nếu có phản ánh đúng kết quả.

**Yêu cầu liên quan:** FR-ORD-002, FR-ORD-003, FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-ORD-012, FR-MAT-001, FR-MAT-002, FR-MAT-003, FR-MAT-004, FR-BAL-003, FR-BAL-004, FR-BAL-007, FR-TRD-002, FR-SIM-001.

**Truy vết nghiệp vụ**

- OQ-007, OQ-008, OQ-009, OQ-010, OQ-011, OQ-012, OQ-013, OQ-014, OQ-015, OQ-016, OQ-017, OQ-018, OQ-019, OQ-020, OQ-021, OQ-022, OQ-023, OQ-024, OQ-025, OQ-026, OQ-027, OQ-035, OQ-046
- BR-ACT-001, BR-BAL-002, BR-BAL-003, BR-BAL-004, BR-FEE-001, BR-FEE-002, BR-ORD-001, BR-ORD-002, BR-ORD-003, BR-ORD-004, BR-ORD-005, BR-ORD-006, BR-PREC-001, BR-SIM-002, BR-STL-001

### UC-002 — Gửi Limit Sell

**Trạng thái:** PROVISIONAL

**Chủ thể chính:** Người giao dịch thủ công

**Điều kiện trước:** Người tham gia sở hữu đủ Base khả dụng; thí nghiệm đang chạy; giá và số lượng yêu cầu hợp lệ.

**Kích hoạt:** Người tham gia gửi số lượng Base và giá SELL Limit.

**Luồng chính**

1. Kiểm tra và giữ toàn bộ Base, sau đó chấp nhận với ưu tiên chính thức.
2. Khớp với Bid đủ điều kiện, áp dụng ưu tiên giá/thứ tự, STP và giá đang chờ.
3. Nhận USDT thuần theo từng Trade; tiếp tục bảo đảm vốn cho phần GTC còn lại bằng Base còn lại.

**Luồng thay thế / Thất bại:** Thiếu Base hoặc đầu vào không hợp lệ bị từ chối trước khi có tác động; không khớp thì nằm chờ ở OPEN; kết quả khớp một phần/toàn bộ theo vòng đời.

**Điều kiện sau:** Base đã khớp được bán, tiền thuần khả dụng và Base chưa khớp còn hiệu lực tiếp tục được giữ.

**Yêu cầu liên quan:** FR-ORD-002, FR-ORD-003, FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-ORD-012, FR-MAT-001, FR-MAT-002, FR-MAT-003, FR-MAT-004, FR-BAL-003, FR-BAL-006, FR-TRD-002, FR-SIM-001.

**Truy vết nghiệp vụ**

- OQ-007, OQ-008, OQ-009, OQ-010, OQ-011, OQ-012, OQ-014, OQ-015, OQ-016, OQ-017, OQ-018, OQ-019, OQ-020, OQ-021, OQ-022, OQ-023, OQ-024, OQ-025, OQ-026, OQ-027, OQ-035, OQ-046
- BR-ACT-001, BR-BAL-002, BR-BAL-003, BR-BAL-004, BR-FEE-001, BR-FEE-002, BR-ORD-001, BR-ORD-002, BR-ORD-003, BR-ORD-004, BR-ORD-005, BR-ORD-006, BR-PREC-001, BR-SIM-002, BR-STL-001

### UC-003 — Gửi Market Buy có bảo vệ giá

**Trạng thái:** PROVISIONAL

**Chủ thể chính:** Người giao dịch thủ công

**Điều kiện trước:** Người tham gia cung cấp số lượng Base và giá tối đa tuyệt đối; có đủ toàn bộ vốn theo biên bảo vệ gồm phí trong thí nghiệm đang chạy.

**Kích hoạt:** Người tham gia gửi yêu cầu BUY có bảo vệ giá.

**Luồng chính**

1. Kiểm tra yêu cầu và toàn bộ khoản giữ theo biên bảo vệ trước chấp nhận hoặc STP.
2. Khớp ngay với Ask đủ điều kiện không cao hơn giá tối đa, áp dụng ưu tiên/STP thông thường và settlement theo từng Trade.
3. Chấm dứt phần còn lại và giải phóng khoản giữ chưa dùng; không đưa nhu cầu Market vào sổ.

**Luồng thay thế / Thất bại:** Biên giá/số lượng không hợp lệ hoặc thiếu vốn cho REJECTED; khớp một phần hoặc không khớp kết thúc ở CANCELLED/MARKET_REMAINDER; khớp hết cho FILLED.

**Điều kiện sau:** Chỉ các lần khớp và phí thực tế được giữ; không còn phần Market hiệu lực hoặc khoản giữ chưa dùng.

**Yêu cầu liên quan:** FR-ORD-001, FR-ORD-004, FR-ORD-005, FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-ORD-012, FR-ORD-015, FR-MAT-001, FR-MAT-002, FR-MAT-003, FR-MAT-004, FR-BAL-003, FR-BAL-005, FR-BAL-008, FR-TRD-002, FR-SIM-001.

**Truy vết nghiệp vụ**

- OQ-004, OQ-006, OQ-007, OQ-008, OQ-009, OQ-010, OQ-011, OQ-012, OQ-014, OQ-015, OQ-016, OQ-017, OQ-018, OQ-019, OQ-020, OQ-021, OQ-022, OQ-023, OQ-024, OQ-025, OQ-026, OQ-027, OQ-031, OQ-035, OQ-046
- BR-ACT-001, BR-BAL-002, BR-BAL-003, BR-BAL-004, BR-FEE-001, BR-FEE-002, BR-ORD-001, BR-ORD-002, BR-ORD-003, BR-ORD-004, BR-ORD-005, BR-ORD-006, BR-ORD-007, BR-PREC-001, BR-SIM-002, BR-STL-001

### UC-004 — Gửi Market Sell có bảo vệ giá

**Trạng thái:** PROVISIONAL

**Chủ thể chính:** Người giao dịch thủ công

**Điều kiện trước:** Người tham gia sở hữu lượng Base yêu cầu ở trạng thái khả dụng và chỉ định giá tối thiểu tuyệt đối; thí nghiệm đang chạy.

**Kích hoạt:** Người tham gia gửi yêu cầu SELL có bảo vệ giá.

**Luồng chính**

1. Kiểm tra và giữ toàn bộ Base trước chấp nhận hoặc STP.
2. Khớp ngay với Bid đủ điều kiện không thấp hơn giá tối thiểu, áp dụng ưu tiên/STP thông thường.
3. Settlement các lần khớp với phí bán trừ từ tiền thu; chấm dứt và giải phóng phần Base chưa dùng còn lại.

**Luồng thay thế / Thất bại:** Yêu cầu không hợp lệ/thiếu Base là REJECTED; không khớp hoặc khớp một phần kết thúc ở CANCELLED/MARKET_REMAINDER mà không đảo ngược lần khớp; khớp hết là FILLED.

**Điều kiện sau:** Tiền bán thuần thực tế khả dụng; không có phần Market còn lại nằm chờ.

**Yêu cầu liên quan:** FR-ORD-001, FR-ORD-004, FR-ORD-006, FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-ORD-012, FR-ORD-015, FR-MAT-001, FR-MAT-002, FR-MAT-003, FR-MAT-004, FR-BAL-003, FR-BAL-006, FR-BAL-008, FR-TRD-002, FR-SIM-001.

**Truy vết nghiệp vụ**

- OQ-004, OQ-006, OQ-007, OQ-008, OQ-009, OQ-010, OQ-011, OQ-012, OQ-014, OQ-015, OQ-016, OQ-017, OQ-018, OQ-019, OQ-020, OQ-021, OQ-022, OQ-023, OQ-024, OQ-025, OQ-026, OQ-027, OQ-031, OQ-035, OQ-046
- BR-ACT-001, BR-BAL-002, BR-BAL-003, BR-BAL-004, BR-FEE-001, BR-FEE-002, BR-ORD-001, BR-ORD-002, BR-ORD-003, BR-ORD-004, BR-ORD-005, BR-ORD-006, BR-ORD-007, BR-PREC-001, BR-SIM-002, BR-STL-001

### UC-005 — Khớp một phần

**Trạng thái:** APPROVED

**Chủ thể chính:** Người giao dịch

**Điều kiện trước:** Limit hoặc Market có bảo vệ giá hợp lệ, đủ vốn có lượng đối ứng đủ điều kiện ngay lập tức nhỏ hơn số lượng yêu cầu.

**Kích hoạt:** Lệnh mới vào khớp với nhu cầu đủ điều kiện có sẵn.

**Luồng chính**

1. Thực hiện số lượng đủ điều kiện và bảo toàn các Trade kết quả.
2. Với Limit, giữ phần GTC còn lại theo ưu tiên gốc; với Market, chấm dứt phần còn lại ngay.

**Luồng thay thế / Thất bại:** Không có thanh khoản thì không tạo Trade; Limit có thể nằm chờ còn Market kết thúc; bảo vệ giá có thể giới hạn số lượng đủ điều kiện.

**Điều kiện sau:** Các lần khớp đã hoàn tất được giữ và không loại lệnh nào được giả định khớp hết. Use case này chủ ý để nhãn vòng đời và cơ chế settlement đề xuất cho các yêu cầu riêng của chúng.

**Yêu cầu liên quan:** FR-ORD-003, FR-ORD-004, FR-ORD-005, FR-ORD-006, FR-MAT-002, FR-MAT-003.

**Truy vết nghiệp vụ**

- OQ-006, OQ-007, OQ-008, OQ-009, OQ-010, OQ-012
- BR-ORD-001, BR-ORD-002, BR-ORD-004, BR-ORD-005

### UC-006 — Ngăn tự khớp

**Trạng thái:** APPROVED

**Chủ thể chính:** Sàn / Nền tảng giao dịch

**Điều kiện trước:** Lệnh mới vào đã chấp nhận, đủ vốn gặp đối ứng đang chờ đủ điều kiện tiếp theo thuộc cùng tài khoản giao dịch.

**Kích hoạt:** Nền tảng so sánh danh tính hai tài khoản giao dịch.

**Luồng chính**

1. Hủy phần đang chờ còn hiệu lực của chính tài khoản, giữ các lần khớp trước và phân biệt STP với hủy theo người dùng.
2. Không tạo self-Trade, khối lượng, cập nhật Last Price hoặc phí.
3. Tiếp tục lệnh mới vào với đối ứng đủ điều kiện tiếp theo.

**Luồng thay thế / Thất bại:** Tài khoản khác không bị gom thành tự đối ứng theo chủ sở hữu con người; nếu không còn đối ứng đủ điều kiện khác, áp dụng quy tắc phần còn lại của loại lệnh mới vào.

**Điều kiện sau:** Phần đang chờ của chính tài khoản đã bị loại và chỉ Trade thực sự với tài khoản khác mới có tác động thực hiện giao dịch.

**Yêu cầu liên quan:** FR-MAT-004, FR-MAT-005, FR-ORD-003, FR-ORD-004.

**Truy vết nghiệp vụ**

- OQ-006, OQ-007, OQ-011, OQ-012
- BR-ACT-001, BR-ORD-002, BR-ORD-004, BR-ORD-006

### UC-007 — Hủy Limit Order còn hiệu lực

**Trạng thái:** PROVISIONAL

**Chủ thể chính:** Người tham gia sở hữu lệnh

**Điều kiện trước:** Người tham gia có phần còn lại của Limit còn hiệu lực trong chính tài khoản.

**Kích hoạt:** Người tham gia yêu cầu hủy.

**Luồng chính**

1. Xác định hiệu lực hủy theo thứ tự chính thức; riêng yêu cầu đang chờ chưa phải thành công.
2. Chỉ chấm dứt phần còn hiệu lực và ghi CANCELLED/USER_REQUEST.
3. Giải phóng khoản giữ chưa dùng, đồng thời giữ toàn bộ Trade và phí trước đó.

**Luồng thay thế / Thất bại:** Lần khớp trước vẫn giữ nguyên; đã khớp hết cho quá muộn/đã khớp hết; đã kết thúc cho đã kết thúc; lệnh không tồn tại/đã từ chối hoặc thuộc tài khoản khác không thể được hủy qua luồng này.

**Điều kiện sau:** Phần bị hủy có hiệu lực không thể khớp về sau và không có giải phóng lặp.

**Yêu cầu liên quan:** FR-ORD-014, FR-ORD-015, FR-ORD-016, FR-ORD-020, FR-MAT-007, FR-BAL-008.

**Truy vết nghiệp vụ**

- OQ-010, OQ-014, OQ-015, OQ-016, OQ-022, OQ-023, OQ-024, OQ-026, OQ-027, OQ-028, OQ-031, OQ-035, OQ-046
- BR-BAL-002, BR-BAL-003, BR-FEE-001, BR-FEE-002, BR-ORD-001, BR-ORD-003, BR-ORD-007, BR-PREC-001, BR-SIM-002, BR-STL-001

### UC-008 — Từ chối lệnh thiếu vốn/không hợp lệ

**Trạng thái:** PROVISIONAL

**Chủ thể chính:** Người tham gia gửi lệnh

**Điều kiện trước:** Lệnh được đề nghị thiếu toàn bộ vốn khả dụng hoặc vi phạm quy tắc V1 về loại lệnh, cặp, bảo vệ giá, giá hoặc số lượng.

**Kích hoạt:** Hệ thống kiểm tra yêu cầu đã gửi.

**Luồng chính**

1. Kiểm tra các quy tắc nghiệp vụ tương ứng và toàn bộ vốn trước chấp nhận hoặc tác động STP.
2. Trả REJECTED mà không tạo Trade, hủy lệnh trước đó, âm thầm làm tròn hoặc giảm số lượng.

**Luồng thay thế / Thất bại:** Nếu yêu cầu vượt qua mọi kiểm tra, tiếp tục use case gửi lệnh tương ứng; dự kiến giải phóng do STP về sau không khắc phục thiếu vốn hiện tại.

**Điều kiện sau:** Yêu cầu bị từ chối không tạo lệnh đã chấp nhận hoặc tác động thực hiện giao dịch; các cam kết hiện có không đổi.

**Yêu cầu liên quan:** FR-ORD-001, FR-ORD-005, FR-ORD-006, FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-ORD-012, FR-BAL-003, FR-MKT-001.

**Truy vết nghiệp vụ**

- OQ-001, OQ-002, OQ-004, OQ-006, OQ-008, OQ-017, OQ-018, OQ-019, OQ-020, OQ-021, OQ-022, OQ-025, OQ-026, OQ-027
- BR-BAL-002, BR-BAL-004, BR-MKT-003, BR-ORD-003, BR-ORD-004, BR-PREC-001

### UC-009 — Chuẩn bị kịch bản mô phỏng

**Trạng thái:** PROVISIONAL

**Chủ thể chính:** Quản trị viên

**Điều kiện trước:** Một thí nghiệm riêng đang ở giai đoạn thiết lập; đã có các đầu vào kịch bản tường minh.

**Kích hoạt:** Quản trị viên chuẩn bị vốn ban đầu, mức phí, hành động kịch bản và giả định thời gian.

**Luồng chính**

1. Khai báo ngày kịch bản, số vốn, giá vốn tài sản được cấp, mức phí và độ trễ người tham gia.
2. Cấp vốn chỉ-USDT cho tài khoản thông thường và tài sản chuyên biệt cho tài khoản kịch bản theo bước hợp lệ, nhận diện Simulation Funding riêng.
3. Khai báo Limit Order kịch bản đủ vốn để gửi theo thứ tự hành động khi chạy; giữ sổ trống cho đến khi lệnh được gửi.

**Luồng thay thế / Thất bại:** Số tiền/mức phí không hợp lệ không được chấp nhận; không hỗ trợ cấp vốn khi chạy; giá trị kịch bản còn thiếu phải được cung cấp thay vì tự suy ra.

**Điều kiện sau:** Kịch bản có đầu vào đã khai báo mà không tạo Trade, khối lượng, Last Price hoặc lợi nhuận giả; chi tiết bắt đầu/điều khiển vẫn là TBD-001.

**Yêu cầu liên quan:** FR-ACT-002, FR-ACT-003, FR-SIM-001, FR-SIM-004, FR-FEE-005, FR-PF-003, FR-HFT-001, FR-ADM-001, FR-ADM-002, FR-ADM-003.

**Truy vết nghiệp vụ**

- OQ-003, OQ-005, OQ-010, OQ-011, OQ-016, OQ-017, OQ-018, OQ-031, OQ-033, OQ-035, OQ-036, OQ-041, OQ-042, OQ-043, OQ-046
- BR-ACT-001, BR-ACT-002, BR-ALGO-001, BR-BAL-001, BR-FEE-002, BR-ORD-001, BR-ORD-006, BR-ORD-007, BR-PF-001, BR-PREC-001, BR-SIM-001, BR-SIM-002

### UC-010 — Chạy chiến lược thuật toán

**Trạng thái:** PROVISIONAL

**Chủ thể chính:** Chiến lược giao dịch thuật toán

**Điều kiện trước:** Thí nghiệm đang chạy có vốn riêng được giao, quan sát mô phỏng, độ trễ cố định và quyết định/đầu vào chiến lược đã khai báo.

**Kích hoạt:** Chiến lược quyết định gửi, hủy hoặc không hành động.

**Luồng chính**

1. Dùng thông tin đã quan sát lúc quyết định để chọn hành động V1 thông thường.
2. Áp dụng độ trễ hành động trước khi vào thứ tự chính thức; áp dụng cùng quy tắc khớp đủ vốn và STP như người tham gia khác.
3. Quan sát lần khớp thực tế, phí, hàng chờ của mình, kết quả hủy và các chỉ tiêu chất lượng thực hiện/PnL được hỗ trợ.

**Luồng thay thế / Thất bại:** Hành động có thể bị từ chối, khớp một phần, kết thúc không khớp hoặc hủy quá muộn; thiếu tham chiếu/lần khớp cho chỉ tiêu tương ứng không khả dụng, không suy ra thành công.

**Điều kiện sau:** Kết quả chiến lược phản ánh kết quả mô phỏng thực tế; chạy lại với chiến lược/độ trễ khác là thay đổi đầu vào thí nghiệm.

**Yêu cầu liên quan:** FR-ACT-003, FR-ALGO-001, FR-ALGO-002, FR-HFT-001, FR-HFT-002, FR-HFT-003, FR-HFT-004, FR-MAT-007, FR-PF-004.

**Truy vết nghiệp vụ**

- OQ-004, OQ-005, OQ-008, OQ-010, OQ-011, OQ-013, OQ-014, OQ-015, OQ-016, OQ-026, OQ-031, OQ-033, OQ-034, OQ-035, OQ-037, OQ-040, OQ-041, OQ-042, OQ-043, OQ-044, OQ-046
- BR-ACT-002, BR-ALGO-001, BR-BAL-001, BR-FEE-001, BR-FEE-002, BR-MKT-001, BR-MKT-002, BR-ORD-001, BR-ORD-003, BR-ORD-004, BR-ORD-006, BR-ORD-007, BR-PF-001, BR-PREC-001, BR-SIM-002

### UC-011 — Kết thúc thí nghiệm mô phỏng

**Trạng thái:** PROVISIONAL

**Chủ thể chính:** Sàn / Nền tảng giao dịch xử lý kết thúc; chủ thể có quyền khởi phát và điều kiện kích hoạt kết thúc vẫn là TBD-001

**Điều kiện trước:** Thí nghiệm tồn tại với hoạt động lịch sử và có thể có phần lệnh còn hiệu lực.

**Kích hoạt:** Kết thúc thí nghiệm có hiệu lực theo thứ tự nghiệp vụ chính thức.

**Luồng chính**

1. Hoàn tất các hành động nghiệp vụ đi trước kết thúc theo thứ tự đã xác định.
2. Hủy mọi phần còn hiệu lực với EXPERIMENT_ENDED và giải phóng khoản giữ chưa dùng.
3. Giữ số dư, Trade, phí và lịch sử, đồng thời gắn nhãn nến cuối chưa đủ phút nếu có.

**Luồng thay thế / Thất bại:** Lệnh đã khớp hết/đã kết thúc không có phần còn hiệu lực để dừng; lịch sử không hợp lệ được giữ với nhãn tính hợp lệ; đặt lại tạo thí nghiệm riêng không chuyển tài sản.

**Điều kiện sau:** Không còn phần lệnh hiệu lực sau kết thúc và thực hiện giao dịch trước đó không bị đảo ngược.

**Yêu cầu liên quan:** FR-SIM-002, FR-SIM-003, FR-MAT-007, FR-MKT-009, FR-TRD-004, FR-ADM-004.

**Truy vết nghiệp vụ**

- OQ-010, OQ-023, OQ-027, OQ-030, OQ-031, OQ-035, OQ-039, OQ-042, OQ-044, OQ-045
- BR-BAL-003, BR-MKT-001, BR-MKT-004, BR-ORD-001, BR-ORD-003, BR-ORD-007, BR-SIM-002, BR-TRD-001

## 9. Kịch bản nghiệm thu

Tất cả trường hợp là kịch bản nghiệm thu ở mức đặc tả, không phải kiểm thử đã chạy. Kịch bản đã duyệt tránh nhãn trạng thái và cơ chế còn đề xuất. Dữ kiện tạm thời không phê duyệt quy tắc đề xuất của nó. Tiêu chí riêng tại mục 6–7 vẫn cần được đáp ứng dù không được lặp trong một kịch bản tổng hợp.

### AS-001 — Giá tốt hơn đứng trước giá kém hơn đến sớm

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-MAT-001, FR-ORD-002.

- **Cho trước** Ask đến trước 1 BTC tại 86,000 và Ask đến sau 1 BTC tại 85,000 USDT/BTC, từ các tài khoản khác nhau đủ điều kiện.
- **Khi** BUY LIMIT đủ vốn cho 1 BTC tại 86,000 USDT/BTC đến.
- **Thì** Ask 85,000 khớp trước; tương tự, đối với SELL, Bid tại 85,500 đứng trước Bid tại 85,000 USDT/BTC.

**Truy vết nghiệp vụ:** OQ-009, OQ-010, OQ-012, BR-ORD-001, BR-ORD-002, BR-ORD-005.

### AS-002 — Thứ tự chấp nhận khi cùng giá

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-MAT-002, NFR-DET-001.

- **Cho trước** Ask A 0.4 BTC có thứ tự #1001 và Ask B 2 BTC có thứ tự #1002, đều tại 86,000 USDT/BTC.
- **Khi** BUY 1 BTC đủ vốn và đủ điều kiện ở giá đó đến.
- **Thì** A khớp 0.4 BTC trước, B khớp 0.6 BTC và B giữ thứ tự của mình cho 1.4 BTC còn lại.

**Truy vết nghiệp vụ:** OQ-010, BR-ORD-001.

### AS-003 — Dấu thời gian máy khách không đổi ưu tiên

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-MAT-002, NFR-DET-001.

- **Cho trước** hàng chờ như AS-002, trong đó B mang dấu thời gian máy khách sớm hơn hoặc hai lệnh có cùng thời gian hiển thị.
- **Khi** ưu tiên được xác định.
- **Thì** A vẫn đứng trước vì #1001 trước #1002; giờ máy khách hay thời gian hiển thị bằng nhau đều không thay đổi điều đó.

**Truy vết nghiệp vụ:** OQ-010, BR-ORD-001.

### AS-004 — Số lượng lớn hơn không được vượt hàng chờ

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-MAT-002.

- **Cho trước** Ask 0.4 BTC đến trước và Ask 2 BTC đến sau tại cùng giá.
- **Khi** nhu cầu mới vào đủ điều kiện được phân bổ.
- **Thì** 0.4 BTC đến trước khớp trước; lệnh lớn hơn đến sau không thể vượt lên.

**Truy vết nghiệp vụ:** OQ-010, BR-ORD-001.

### AS-005 — Giá thực hiện theo phía đang chờ

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-MAT-003.

- **Cho trước** BUY đang chờ tại 90,000 USDT/BTC và SELL mới vào với giá Limit 86,000 USDT/BTC.
- **Khi** hai lệnh khớp nhau.
- **Thì** thực hiện tại 90,000 USDT/BTC; trong trường hợp riêng với SELL chờ tại 86,000 và BUY mới vào tại 90,000, giá thực hiện là 86,000.

**Truy vết nghiệp vụ:** OQ-008, OQ-009, BR-ORD-004, BR-ORD-005.

### AS-006 — Limit khớp một phần để phần GTC còn lại nằm chờ

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-ORD-002, FR-ORD-003, FR-MAT-002.

- **Cho trước** BUY LIMIT đủ vốn cho 10 BTC tại 90,000 USDT/BTC và chỉ 4 BTC đủ điều kiện ngay tại Ask đang chờ 86,000.
- **Khi** việc khớp ngay hoàn tất.
- **Thì** 4 BTC vẫn là số lượng đã thực hiện, 6 BTC nằm chờ tại 90,000 với thứ tự chấp nhận gốc và không bảo đảm sẽ khớp về sau.

**Truy vết nghiệp vụ:** OQ-007, OQ-009, OQ-010, OQ-012, BR-ORD-001, BR-ORD-002, BR-ORD-005.

### AS-007 — Market khớp một phần hoặc không khớp đều kết thúc phần dư

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-ORD-004.

- **Cho trước** Market Buy đủ vốn cho 5 BTC và chỉ 3 BTC đủ điều kiện ngay trong biên bảo vệ.
- **Khi** việc khớp hoàn tất.
- **Thì** 3 BTC vẫn là số lượng đã thực hiện, 2 BTC kết thúc mà không nằm chờ; một trường hợp riêng không có thanh khoản đủ điều kiện không tạo Trade và chấm dứt toàn bộ 5 BTC.

**Truy vết nghiệp vụ:** OQ-006, OQ-007, OQ-012, BR-ORD-002, BR-ORD-004.

### AS-008 — Giá tối đa tuyệt đối của Market Buy

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-ORD-005, FR-ORD-004, FR-MAT-003.

- **Cho trước** Market Buy đủ vốn cho 2 BTC có trần 86,000 USDT/BTC, với Ask 1 BTC tại 86,000 và 2 BTC tại 87,000.
- **Khi** khớp lệnh áp dụng mức tối đa.
- **Thì** chỉ 1 BTC được thực hiện tại 86,000 và 1 BTC còn lại kết thúc; phí không thể cho phép giá thực hiện đơn vị cao hơn.

**Truy vết nghiệp vụ:** OQ-006, OQ-007, OQ-008, OQ-009, OQ-012, BR-ORD-002, BR-ORD-004, BR-ORD-005.

### AS-009 — Giá tối thiểu tuyệt đối của Market Sell

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-ORD-006, FR-ORD-004, FR-MAT-003.

- **Cho trước** Market Sell đủ vốn cho 3 BTC có giá tối thiểu 80,000 USDT/BTC và Bid mỗi mức 1 BTC tại 82,000, 80,000 và 79,000.
- **Khi** khớp lệnh áp dụng mức tối thiểu.
- **Thì** 1 BTC khớp ở mỗi mức 82,000 và 80,000; 1 BTC cuối kết thúc mà không thực hiện tại 79,000.

**Truy vết nghiệp vụ:** OQ-006, OQ-007, OQ-008, OQ-009, OQ-012, BR-ORD-002, BR-ORD-004, BR-ORD-005.

### AS-010 — STP hủy lệnh chờ tự đối ứng và tiếp tục

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-MAT-004.

- **Cho trước** BUY mới vào của tài khoản A gặp phần SELL đang chờ đủ điều kiện 0.4 BTC của A, tiếp theo là SELL đủ điều kiện 0.6 BTC của B.
- **Khi** khớp lệnh đến lệnh chờ của chính A.
- **Thì** phần đang chờ của A bị hủy với nhận diện STP riêng, các lần khớp trước của nó vẫn giữ nguyên và lệnh mới vào tiếp tục với đối ứng B 0.6 BTC.

**Truy vết nghiệp vụ:** OQ-011, BR-ACT-001, BR-ORD-006.

### AS-011 — STP không có tác động thực hiện trên thị trường

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-MAT-005.

- **Cho trước** chỉ có đối ứng đủ điều kiện cùng tài khoản, với số Trade, khối lượng, Last Price và phí đã ghi nhận.
- **Khi** STP hủy phần đang chờ đó.
- **Thì** các chỉ tiêu thực hiện giữ nguyên; độ sâu có thể giảm nhưng không tạo self-Trade, khối lượng, cập nhật Last Price hoặc phí.

**Truy vết nghiệp vụ:** OQ-011, BR-ORD-006.

### AS-012 — Cấp vốn là vốn, không phải hoạt động giao dịch

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-ACT-002, FR-ADM-001, FR-ADM-002.

- **Cho trước** quản trị viên chọn vốn mô phỏng ban đầu và cấp tài sản cho tài khoản kịch bản.
- **Khi** Simulation Funding được ghi nhận.
- **Thì** chỉ vốn được cấp thay đổi; không tạo Trade, thực hiện giao dịch, khối lượng, lợi nhuận hoặc Last Price, và các lệnh kịch bản sau đó tuân theo vốn/ưu tiên/STP thông thường.

**Truy vết nghiệp vụ:** OQ-003, OQ-005, OQ-010, OQ-011, BR-ACT-001, BR-BAL-001, BR-ORD-001, BR-ORD-006, BR-SIM-001.

### AS-013 — Một lệnh là Taker trước rồi Maker sau

**Trạng thái:** APPROVED

**Yêu cầu liên quan:** FR-FEE-001, FR-FEE-002, FR-ORD-003, FR-MAT-003.

- **Cho trước** SELL đang chờ 1 BTC tại 86,000 và BUY LIMIT mới vào đủ vốn cho 2 BTC tại 90,000 USDT/BTC.
- **Khi** BUY khớp với Ask, để phần còn lại nằm chờ rồi SELL đủ điều kiện đến sau khớp với nó.
- **Thì** BUY là Taker ở Trade 86,000 và Maker ở Trade 90,000; vai trò thuộc từng Trade.

**Truy vết nghiệp vụ:** OQ-007, OQ-008, OQ-009, OQ-012, OQ-013, OQ-014, BR-FEE-001, BR-ORD-002, BR-ORD-004, BR-ORD-005.

### AS-014 — Từ chối thiếu vốn trước STP

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-BAL-003, FR-BAL-005.

- **Cho trước** Market Buy 2 BTC có trần 90,000 USDT/BTC, phí taker 0.2%, chỉ 180,359 USDT khả dụng và một SELL đang chờ của chính tài khoản đã giữ BTC, vốn đủ điều kiện đối ứng.
- **Khi** kiểm tra vốn được thực hiện.
- **Thì** không đạt yêu cầu đủ 180,360 USDT; yêu cầu là REJECTED, không khớp, không giảm số lượng và không hủy SELL đang chờ của chính tài khoản.

**Truy vết nghiệp vụ:** OQ-008, OQ-015, OQ-016, OQ-021, OQ-022, OQ-027, OQ-046, BR-BAL-002, BR-BAL-004, BR-ORD-003, BR-PREC-001.

### AS-015 — Tính lại và giải phóng khoản giữ

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-BAL-004, FR-BAL-007, FR-BAL-008.

- **Cho trước** số dư ban đầu 100,000 USDT, BUY LIMIT 1 BTC tại 80,000 USDT/BTC, maker 0.1%, taker 0.2%.
- **Khi** 0.4 BTC khớp với Ask 75,000 USDT/BTC, sau đó hủy 0.6 BTC còn lại.
- **Thì** khoản giữ ban đầu 80,160; chi phí thực hiện 30,060; khoản giữ sau khớp 48,096; giải phóng dư 2,004; sau hủy có 69,940 USDT khả dụng và vẫn sở hữu 0.4 BTC.

**Truy vết nghiệp vụ:** OQ-013, OQ-014, OQ-015, OQ-016, OQ-022, OQ-023, OQ-024, OQ-027, OQ-031, OQ-046, BR-BAL-002, BR-BAL-003, BR-FEE-001, BR-FEE-002, BR-ORD-003, BR-ORD-007, BR-PREC-001, BR-STL-001.

### AS-016 — Hủy giữ nguyên các lần khớp trước

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-ORD-020, FR-ORD-016, FR-BAL-008.

- **Cho trước** lệnh ban đầu 1 BTC đã thực hiện 0.4 BTC.
- **Khi** hủy có hiệu lực chấm dứt phần còn lại 0.6 BTC.
- **Thì** đã khớp vẫn là 0.4, chưa thực hiện 0.6, còn hiệu lực thành 0 BTC và chỉ vốn chưa dùng được giải phóng; thực hiện giao dịch và phí của nó vẫn được giữ.

**Truy vết nghiệp vụ:** OQ-014, OQ-015, OQ-016, OQ-022, OQ-023, OQ-024, OQ-027, OQ-028, OQ-031, OQ-046, BR-BAL-002, BR-BAL-003, BR-FEE-001, BR-FEE-002, BR-ORD-003, BR-ORD-007, BR-PREC-001, BR-STL-001.

### AS-017 — Lệnh đã kết thúc không trở lại hiệu lực

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-ORD-012, FR-ORD-015, FR-ORD-016.

- **Cho trước** các lệnh ở FILLED, CANCELLED hoặc REJECTED.
- **Khi** hoạt động về sau hoặc một yêu cầu hủy khác tham chiếu chúng.
- **Thì** không lệnh nào trở lại OPEN/PARTIALLY_FILLED; phần còn hiệu lực vẫn bằng không; Market hợp lệ không khớp kết thúc ở CANCELLED/MARKET_REMAINDER thay vì REJECTED.

**Truy vết nghiệp vụ:** OQ-025, OQ-026, OQ-027, OQ-028, OQ-035, BR-ORD-003, BR-ORD-007, BR-SIM-002.

### AS-018 — Settlement và cộng dồn mỗi Trade một lần

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-TRD-002, FR-TRD-003, FR-FEE-002, FR-FEE-004, NFR-COR-001.

- **Cho trước** Trade 1 BTC tại 86,000 USDT/BTC với người mua Taker 0.2%, người bán Maker 0.1%.
- **Khi** settlement diễn ra rồi cùng Trade đó được báo cáo lại.
- **Thì** người mua trả 86,172 USDT, người bán nhận 85,914, treasury nhận 258 và 1 BTC chuyển chủ một lần; báo cáo lặp không đổi số dư, phí hoặc tổng đã khớp.

**Truy vết nghiệp vụ:** OQ-003, OQ-005, OQ-014, OQ-015, OQ-016, OQ-022, OQ-023, OQ-024, OQ-046, BR-BAL-001, BR-BAL-002, BR-BAL-003, BR-FEE-001, BR-FEE-002, BR-PREC-001, BR-SIM-001, BR-STL-001.

### AS-019 — Thống kê thị trường tính Trade thực sự một lần

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-MKT-006, FR-MKT-007, FR-MKT-008.

- **Cho trước** bốn Trade trong một phút tại 86,000/88,000/85,000/87,000 USDT/BTC cho 1/0.5/2/0.5 BTC, cùng các sự kiện cấp vốn, hủy và STP.
- **Khi** thống kê thị trường được tạo.
- **Thì** Last Price là 87,000; khối lượng Base là 4 BTC; khối lượng Quote trước phí là 343,500 USDT; chỉ bốn Trade đóng góp OHLCV và phí không làm tăng khối lượng.

**Truy vết nghiệp vụ:** OQ-003, OQ-011, OQ-035, OQ-038, OQ-039, OQ-044, BR-MKT-001, BR-MKT-004, BR-ORD-006, BR-SIM-001.

### AS-020 — Nến trống không tạo giá giả

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-MKT-006, FR-MKT-009.

- **Cho trước** một phút không có Trade thực sự tiếp sau phút có Trade mới nhất tại 87,000 USDT/BTC.
- **Khi** nến trống được báo cáo.
- **Thì** OHLC không khả dụng và hai khối lượng bằng không, còn Last Price riêng có thể vẫn là 87,000; không mang giá sang nến.

**Truy vết nghiệp vụ:** OQ-003, OQ-011, OQ-035, OQ-039, OQ-044, BR-MKT-001, BR-MKT-004, BR-ORD-006, BR-SIM-001.

### AS-021 — Quản trị viên không thể viết lại Trade

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-TRD-004, FR-ADM-004, NFR-SEC-002.

- **Cho trước** một thí nghiệm có Trade đã xác nhận về sau bị coi là không hợp lệ cho nghiên cứu.
- **Khi** quản trị viên muốn hiệu chỉnh kết quả giao dịch của nó.
- **Thì** không hỗ trợ sửa/xóa/đảo ngược Trade hoặc dùng cấp vốn để hiệu chỉnh; lịch sử gốc được giữ với nhãn tính hợp lệ tường minh và lần chạy mới dùng thí nghiệm riêng.

**Truy vết nghiệp vụ:** OQ-003, OQ-035, OQ-042, OQ-045, BR-SIM-001, BR-SIM-002, BR-TRD-001.

### AS-022 — Kết thúc thí nghiệm bảo toàn lịch sử thực hiện

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-SIM-002, FR-SIM-003.

- **Cho trước** Limit GTC đã khớp một phần, còn phần hiệu lực và khoản giữ chưa dùng.
- **Khi** kết thúc thí nghiệm có hiệu lực theo thứ tự chính thức.
- **Thì** phần còn lại trở thành CANCELLED/EXPERIMENT_ENDED, số lượng hiệu lực bằng không, khoản giữ chưa dùng được giải phóng và lịch sử Trade, phí, số dư kết quả vẫn được giữ; đặt lại dùng lịch sử riêng.

**Truy vết nghiệp vụ:** OQ-023, OQ-027, OQ-030, OQ-031, OQ-035, OQ-042, OQ-045, BR-BAL-003, BR-ORD-003, BR-ORD-007, BR-SIM-002, BR-TRD-001.

### AS-023 — Bước số chính xác và giới hạn lệnh

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011.

- **Cho trước** các yêu cầu đáp ứng những điều kiện khác và đủ vốn.
- **Khi** giá và số lượng được kiểm tra.
- **Thì** các giá trị biên gồm cả hai đầu của bước giá/số lượng đạt kiểm tra tương ứng; giá trị lệch bước, dưới tối thiểu hoặc trên tối đa không đạt mà không làm tròn/đổi số lượng; không bổ sung ngưỡng notional hoặc kích thước lần khớp.

**Truy vết nghiệp vụ:** OQ-017, OQ-018, OQ-019, OQ-020, BR-PREC-001.

### AS-024 — Độ chính xác phí và giới hạn cấu hình

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-FEE-003, FR-FEE-004, FR-FEE-005, NFR-COR-002.

- **Cho trước** mức maker/taker khai báo tường minh và notional BTC hợp lệ nhỏ nhất 0.00000001 USDT.
- **Khi** mức 0.01% được áp dụng theo từng Trade/từng bên.
- **Thì** phí chính xác 0.000000000001 USDT được làm tròn xuống bằng không mà không có mức tối thiểu; mức phí không hợp lệ không qua thiết lập và mức cố định không thể đổi lúc chạy.

**Truy vết nghiệp vụ:** OQ-014, OQ-015, OQ-016, OQ-017, OQ-018, OQ-022, OQ-033, OQ-042, OQ-046, BR-BAL-002, BR-FEE-002, BR-PF-001, BR-PREC-001, BR-SIM-002.

### AS-025 — Thứ tự hành động chính thức và thay lệnh

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-MAT-006, FR-MAT-007, FR-ORD-014, FR-ORD-018, FR-ORD-020, NFR-DET-002.

- **Cho trước** một lần khớp mới vào, yêu cầu hủy lệnh còn hiệu lực và yêu cầu thay thế đến sau.
- **Khi** các hành động có hiệu lực theo thứ tự chính thức.
- **Thì** toàn bộ kết quả thực hiện ngay của một lệnh mới vào hoàn tất trước hành động tiếp theo; khớp trước vẫn giữ nguyên, hủy trước ngăn phần đó khớp sau; lệnh thay thế nhận kiểm tra/ưu tiên mới và thất bại không khôi phục lệnh cũ.

**Truy vết nghiệp vụ:** OQ-010, OQ-016, OQ-021, OQ-023, OQ-026, OQ-027, OQ-028, OQ-029, OQ-031, OQ-035, OQ-039, OQ-046, BR-BAL-003, BR-BAL-004, BR-FEE-002, BR-MKT-004, BR-ORD-001, BR-ORD-003, BR-ORD-007, BR-PREC-001, BR-SIM-002.

### AS-026 — Giá vốn bình quân, phí và vốn chưa có giá thị trường

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-PF-001, FR-PF-002, FR-PF-003, FR-PF-004.

- **Cho trước** các giao dịch mua không phí trong nguồn gồm 1 BTC tại 80,000 và 1 BTC tại 90,000 USDT/BTC.
- **Khi** 0.5 BTC được bán tại 88,000 và Last Price về sau là 87,000 USDT/BTC.
- **Thì** PnL đã thực hiện là 1,500, giá vốn còn lại 127,500 và PnL chưa thực hiện 3,000 USDT; trong trường hợp riêng chỉ cấp vốn chưa có Trade, định giá Base/tổng giá trị danh mục không khả dụng và vốn không phải lợi nhuận.

**Truy vết nghiệp vụ:** OQ-003, OQ-015, OQ-016, OQ-017, OQ-018, OQ-024, OQ-033, OQ-034, OQ-042, OQ-044, OQ-046, BR-FEE-002, BR-MKT-001, BR-PF-001, BR-PREC-001, BR-SIM-001, BR-SIM-002, BR-STL-001.

### AS-027 — Độ sâu ẩn danh và hàng chờ của chính mình

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-ACT-003, FR-MKT-002, FR-MKT-003, FR-MKT-004, FR-MKT-005, NFR-SEC-001.

- **Cho trước** hai lệnh cùng giá đến trước với 0.4 và 0.6 BTC đứng trước lệnh của người tham gia.
- **Khi** người tham gia quan sát sổ và thử tác động lên tài khoản khác.
- **Thì** hàng chờ phía trước là hai lệnh/1 BTC, độ sâu được tổng hợp, danh tính/lịch sử tài khoản khác vẫn riêng tư và không thể dùng vốn/hủy lệnh tài khoản khác; thiếu một phía làm giá tốt nhất phía đó và Spread không khả dụng.

**Truy vết nghiệp vụ:** OQ-010, OQ-027, OQ-031, OQ-037, OQ-043, OQ-044, BR-ACT-002, BR-MKT-001, BR-MKT-002, BR-ORD-001, BR-ORD-003, BR-ORD-007.

### AS-028 — Ranh giới phút và khoảng cuối chưa hoàn tất

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-SIM-001, FR-MKT-008, FR-MKT-009.

- **Cho trước** các Trade tại 10:00:59 và đúng 10:01:00 trên đồng hồ thí nghiệm, kể cả nhiều Trade cùng thời điểm.
- **Khi** kết thúc thí nghiệm trong phút 10:01.
- **Thì** Trade tại ranh giới thuộc phút 10:01, Open/Close khi cùng thời điểm theo thứ tự thực hiện và khoảng cuối chưa hoàn tất được giữ kèm nhãn.

**Truy vết nghiệp vụ:** OQ-035, OQ-038, OQ-039, OQ-044, BR-MKT-001, BR-MKT-004, BR-SIM-002.

### AS-029 — Chiến lược tổng hợp và chỉ tiêu thực hiện có độ trễ

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-ALGO-001, FR-ALGO-002, FR-HFT-001, FR-HFT-002, FR-HFT-003, FR-HFT-004, NFR-REP-001, NFR-COR-003.

- **Cho trước** chiến lược với độ trễ cố định đã khai báo quan sát Ask 86,000 USDT/BTC rồi mua 2 BTC với bình quân 86,500.
- **Khi** kết quả tổng hợp được báo cáo và chuẩn bị một lần chạy lại riêng có kiểm soát.
- **Thì** trượt giá bất lợi là 500 USDT/BTC hay 0.5814%; phí và số lượng khớp thực tế quyết định chi phí; thời gian đến sự kiện khớp chưa đạt không khả dụng; đổi độ trễ là đổi đầu vào và mốc bắt đầu đo vẫn là TBD-003.

**Truy vết nghiệp vụ:** OQ-004, OQ-005, OQ-008, OQ-010, OQ-011, OQ-013, OQ-014, OQ-015, OQ-016, OQ-026, OQ-031, OQ-033, OQ-034, OQ-035, OQ-037, OQ-040, OQ-041, OQ-042, OQ-043, OQ-046, BR-ACT-002, BR-ALGO-001, BR-BAL-001, BR-FEE-001, BR-FEE-002, BR-MKT-002, BR-ORD-001, BR-ORD-003, BR-ORD-004, BR-ORD-006, BR-ORD-007, BR-PF-001, BR-PREC-001, BR-SIM-002.

### AS-030 — Kiểm soát thiết lập và tài sản kịch bản hữu hạn

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-SIM-004, FR-ADM-003, FR-FEE-005, FR-ORD-017, FR-ORD-019.

- **Cho trước** kịch bản đã cấu hình với tài khoản được cấp vốn và Limit Order định trước.
- **Khi** bắt đầu thí nghiệm, tài sản được sử dụng và quản trị viên thử cấp vốn trong lúc chạy hoặc sửa lệnh.
- **Thì** Limit Order kịch bản định trước, đủ vốn tạo độ sâu kịch bản ban đầu, còn phần GTC của người tham gia thông thường cũng có thể thêm độ sâu; tài sản không tự bổ sung; không hỗ trợ cấp vốn/sửa lệnh bị cấm; GTC còn lại không hết hạn chỉ vì thời gian tiến.

**Truy vết nghiệp vụ:** OQ-003, OQ-005, OQ-010, OQ-011, OQ-012, OQ-016, OQ-017, OQ-018, OQ-027, OQ-028, OQ-030, OQ-033, OQ-035, OQ-036, OQ-041, OQ-042, OQ-043, OQ-046, BR-ACT-001, BR-ACT-002, BR-ALGO-001, BR-FEE-002, BR-ORD-002, BR-ORD-003, BR-ORD-007, BR-PF-001, BR-PREC-001, BR-SIM-001, BR-SIM-002.

### AS-031 — Phạm vi và tuyên bố của V1

**Trạng thái:** PROVISIONAL

**Yêu cầu liên quan:** FR-ACT-001, FR-MKT-001, FR-ORD-001, FR-ORD-007, FR-BAL-001, FR-ALGO-002, NFR-PERF-001.

- **Cho trước** mô tả khả năng của V1.
- **Khi** phạm vi và tuyên bố hiệu năng được rà soát.
- **Thì** chỉ cung cấp ba thị trường spot mô phỏng đủ vốn và loại lệnh Limit/Market có bảo vệ giá; chỉ thị nâng cao và tích hợp thị trường thật vẫn được hoãn, còn nghiệm thu hiệu năng vận hành định lượng vẫn là TBD.

**Truy vết nghiệp vụ:** OQ-001, OQ-002, OQ-004, OQ-005, OQ-006, OQ-012, OQ-013, OQ-035, OQ-040, OQ-041, OQ-042, BR-ALGO-001, BR-BAL-001, BR-FEE-001, BR-MKT-003, BR-ORD-002, BR-ORD-004, BR-SIM-002.

## 10. Bất biến nghiệp vụ

Các ý nghĩa dưới đây giữ nội dung BUSINESS.md mục 30 mà không đổi ID. Cột cuối xác định ranh giới trạng thái yêu cầu, không phê duyệt bất biến thêm lần nữa. Một nguyên tắc nghiệp vụ đã duyệt có thể có chi tiết thực thi riêng của dự án còn tạm thời; tuân theo trạng thái từng yêu cầu liên quan. Mỗi yêu cầu được ánh xạ đều có tiêu chí chấp nhận tại mục 6–7.

| Bất biến nguồn | Ý nghĩa được giữ nguyên | Yêu cầu bao phủ | Ranh giới phê duyệt |
|---------------|-------------------------|-----------------|--------------------|
| ORD-INV-001 | `0 ≤ filled ≤ original`; lượng ban đầu không đổi trong V1. | FR-ORD-016, FR-ORD-017 | Chi tiết dự án PROVISIONAL; khái niệm nghiệp vụ nguồn không tạo phê duyệt riêng. |
| ORD-INV-002 | `original = filled + unexecuted`; active remaining bằng không khi kết thúc. | FR-ORD-016 | Chi tiết dự án PROVISIONAL; khái niệm nghiệp vụ nguồn không tạo phê duyệt riêng. |
| ORD-INV-003 | Execution tuân thủ cả hai biên áp dụng, gồm bảo vệ Market. | FR-ORD-002, FR-ORD-005, FR-ORD-006, FR-MAT-003 | Hành vi APPROVED. |
| ORD-INV-004 | Hủy có hiệu lực chặn fill tương lai của phần dư; fill cũ giữ nguyên. | FR-ORD-020, FR-MAT-007 | Chi tiết dự án PROVISIONAL; khái niệm nghiệp vụ nguồn không tạo phê duyệt riêng. |
| BAL-INV-001 | Tài sản dự trữ không cấp vốn cho lệnh khác, kể cả khác cặp. | FR-BAL-002, NFR-COR-001 | Nguyên tắc đã duyệt; chi tiết tạm thời vẫn được tách riêng. |
| BAL-INV-002 | Giữ/trả dự trữ chỉ đổi khả dụng, không đổi tổng sở hữu. | FR-BAL-007, FR-BAL-008, NFR-COR-001 | Chi tiết dự án PROVISIONAL; khái niệm nghiệp vụ nguồn không tạo phê duyệt riêng. |
| BAL-INV-003 | Trả dự trữ chưa dùng không hoàn tài sản đã trao đổi hay phí đã trả. | FR-BAL-008, FR-ORD-020 | Chi tiết dự án PROVISIONAL; khái niệm nghiệp vụ nguồn không tạo phê duyệt riêng. |
| TRD-INV-001 | Trade đã xác nhận là sự kiện lịch sử bất biến. | FR-TRD-004 | Chi tiết dự án PROVISIONAL; khái niệm nghiệp vụ nguồn không tạo phê duyệt riêng. |
| TRD-INV-002 | Hai bên thống nhất cặp, lượng Base và giá lệnh đang chờ. | FR-MAT-003, FR-TRD-001 | Nguyên tắc đã duyệt; chi tiết tạm thời vẫn được tách riêng. |
| TRD-INV-003 | Mỗi Trade đóng góp lượng đúng một lần vào filled của mỗi lệnh tham gia. | FR-ORD-016, FR-TRD-003 | Chi tiết dự án PROVISIONAL; khái niệm nghiệp vụ nguồn không tạo phê duyệt riêng. |
| STL-INV-001 | Settlement nhất quán sở hữu ở mọi bên, gồm bên nhận phí. | FR-TRD-002, NFR-COR-001 | Chi tiết dự án PROVISIONAL; khái niệm nghiệp vụ nguồn không tạo phê duyệt riêng. |
| STL-INV-002 | Giao dịch bảo toàn từng tài sản giữa người tham gia và Fee Treasury; tách Simulation Funding. | FR-TRD-002, FR-ADM-001, NFR-COR-001 | Nguyên tắc đã duyệt; chi tiết tạm thời vẫn được tách riêng. |
| STL-INV-003 | Chuyển tài sản/phí của một Trade áp dụng một lần, không thêm lần vì được báo lại. | FR-TRD-003 | Chi tiết dự án PROVISIONAL; khái niệm nghiệp vụ nguồn không tạo phê duyệt riêng. |
| MKT-INV-001 | Last Price chỉ phản ánh Trade thực gần nhất theo thứ tự execution. | FR-MKT-006 | Chi tiết dự án PROVISIONAL; khái niệm nghiệp vụ nguồn không tạo phê duyệt riêng. |
| MKT-INV-002 | Volume đếm mỗi Trade một lần, không một lần cho mỗi đối tác. | FR-MKT-007 | Chi tiết dự án PROVISIONAL; khái niệm nghiệp vụ nguồn không tạo phê duyệt riêng. |
| ORD-INV-005 | Cùng giá ưu tiên theo acceptance sequence, không theo client time hay lượng lớn. | FR-MAT-002, NFR-DET-001 | Hành vi APPROVED. |
| TRD-INV-004 | Không Trade giữa lệnh cùng tài khoản; STP hủy dư đang chờ, cặp bị ngăn không có phí/volume/Last Price. | FR-MAT-004, FR-MAT-005 | Hành vi APPROVED. |
| BAL-INV-004 | Cam kết được nhận không đòi tài sản không khả dụng; Available/Locked không âm. | FR-BAL-001, FR-BAL-003, FR-BAL-004, FR-BAL-005, FR-BAL-006 | Nguyên tắc đã duyệt; chi tiết tạm thời vẫn được tách riêng. |
| SIM-INV-001 | Cấp vốn không là execution, volume, lợi nhuận và không tự tạo giá thị trường. | FR-ADM-001, FR-PF-003 | Nguyên tắc đã duyệt; chi tiết tạm thời vẫn được tách riêng. |

## 11. Ma trận truy vết

### 11.1 Nghiệp vụ → Yêu cầu: quyết định

Mọi OQ đã được kiểm tra riêng về tác động ở mức hệ thống. Mỗi dòng liệt kê phụ thuộc yêu cầu trực tiếp; ma trận BR còn hỗ trợ rà soát tác động ở mức quy tắc. Theo các yêu cầu được ánh xạ sang mục 11.3 để tìm tiêu chí riêng, kịch bản tổng hợp và use case. Ví dụ, thay đổi OQ-027 đòi hỏi rà soát mọi yêu cầu trong dòng đó và mọi mục kiểm chứng kết nối với chúng.

OQ-032 chủ ý không tạo yêu cầu mới: nó nhắc lại việc loại trừ lệnh nâng cao đã duyệt tại OQ-004, được thể hiện bằng FR-ORD-001. Dòng OQ-032 là ghi chú tác động, không phải phụ thuộc đề xuất của yêu cầu đã duyệt đó. Cả 45 quyết định còn lại đều có yêu cầu bao phủ trực tiếp. Tham số kịch bản đã chọn không phải khoảng trống bao phủ.

| Quyết định nghiệp vụ | Trạng thái nguồn | Yêu cầu / Tác động đã kiểm tra |
|----------------------|------------------|-------------------------------|
| OQ-001 | APPROVED | FR-MKT-001 |
| OQ-002 | APPROVED | FR-ACT-001, FR-BAL-001, FR-MKT-001 |
| OQ-003 | APPROVED | FR-ACT-002, FR-SIM-004, FR-MKT-006, FR-MKT-007, FR-PF-003, FR-ADM-001, FR-ADM-002, FR-ADM-003, NFR-COR-001, NFR-SEC-002 |
| OQ-004 | APPROVED | FR-ORD-001, FR-ALGO-001 |
| OQ-005 | APPROVED | FR-ACT-001, FR-SIM-004, FR-BAL-001, FR-BAL-002, FR-ALGO-001, FR-ADM-002, NFR-COR-001 |
| OQ-006 | APPROVED | FR-ORD-001, FR-ORD-004 |
| OQ-007 | APPROVED | FR-ORD-003, FR-ORD-004 |
| OQ-008 | APPROVED | FR-ORD-005, FR-ORD-006, FR-MAT-003, FR-BAL-005, FR-ALGO-001, FR-HFT-003 |
| OQ-009 | APPROVED | FR-ORD-002, FR-MAT-003, FR-TRD-001 |
| OQ-010 | APPROVED | FR-SIM-004, FR-ORD-018, FR-MAT-001, FR-MAT-002, FR-MAT-007, FR-MKT-003, FR-ALGO-001, FR-HFT-001, FR-ADM-002, NFR-DET-001, NFR-REP-001 |
| OQ-011 | APPROVED | FR-SIM-004, FR-MAT-004, FR-MAT-005, FR-MKT-006, FR-MKT-007, FR-ALGO-001, FR-ADM-002 |
| OQ-012 | APPROVED | FR-ORD-002, FR-ORD-003, FR-ORD-004, FR-ORD-007, FR-ORD-019 |
| OQ-013 | APPROVED | FR-BAL-001, FR-BAL-004, FR-FEE-001, FR-HFT-002 |
| OQ-014 | APPROVED | FR-BAL-004, FR-BAL-007, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-FEE-001, FR-FEE-002, FR-FEE-004, FR-HFT-002, NFR-COR-001 |
| OQ-015 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-004, FR-BAL-005, FR-BAL-006, FR-BAL-007, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-FEE-003, FR-FEE-004, FR-PF-002, FR-HFT-002, NFR-COR-001 |
| OQ-016 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-004, FR-BAL-005, FR-BAL-006, FR-BAL-007, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-FEE-004, FR-FEE-005, FR-PF-002, FR-HFT-002, FR-ADM-003, NFR-DET-002, NFR-COR-001, NFR-REP-001 |
| OQ-017 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-008, FR-PF-003, FR-ADM-003, NFR-COR-002 |
| OQ-018 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-009, FR-ORD-010, FR-PF-003, FR-ADM-003, NFR-COR-002 |
| OQ-019 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-010 |
| OQ-020 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-011 |
| OQ-021 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-018, FR-BAL-003 |
| OQ-022 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-003, FR-BAL-004, FR-BAL-005, FR-BAL-006, FR-BAL-007, FR-BAL-008, NFR-COR-001, NFR-COR-002 |
| OQ-023 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-020, FR-BAL-007, FR-BAL-008, FR-TRD-002, NFR-COR-001 |
| OQ-024 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-007, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-TRD-003, FR-PF-001, NFR-COR-001 |
| OQ-025 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-012, FR-ORD-013, NFR-AUD-001 |
| OQ-026 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-012, FR-ORD-014, FR-HFT-004, NFR-AUD-001 |
| OQ-027 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-012, FR-ORD-013, FR-ORD-015, FR-ORD-016, FR-ORD-019, FR-ORD-020, FR-BAL-003, FR-BAL-008, FR-MKT-003, NFR-AUD-001 |
| OQ-028 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-016, FR-ORD-017, FR-ORD-018 |
| OQ-029 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-018 |
| OQ-030 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-019 |
| OQ-031 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-014, FR-ORD-018, FR-ORD-020, FR-MAT-006, FR-MAT-007, FR-BAL-008, FR-HFT-001, FR-HFT-004, NFR-DET-002, NFR-AUD-001, NFR-REP-001, NFR-SEC-001 |
| OQ-032 | PROPOSED — OWNER REVIEW REQUIRED | Không tạo yêu cầu mới: nhắc lại OQ-004; rà soát FR-ORD-001 về tính nhất quán mà không đổi trạng thái APPROVED của yêu cầu này. |
| OQ-033 | PROPOSED — OWNER REVIEW REQUIRED | FR-PF-001, FR-PF-002, FR-PF-003, FR-PF-004, FR-HFT-002, FR-ADM-003, NFR-COR-002 |
| OQ-034 | PROPOSED — OWNER REVIEW REQUIRED | FR-PF-001, FR-PF-004, NFR-COR-003 |
| OQ-035 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-001, FR-SIM-002, FR-SIM-003, FR-SIM-004, FR-ORD-015, FR-ORD-019, FR-MAT-007, FR-MKT-007, FR-MKT-008, FR-MKT-009, FR-ALGO-002, FR-HFT-001, FR-HFT-004, FR-ADM-003, FR-ADM-004, NFR-DET-002, NFR-REP-001 |
| OQ-036 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-004 |
| OQ-037 | PROPOSED — OWNER REVIEW REQUIRED | FR-TRD-001, FR-MKT-002, FR-MKT-003, FR-MKT-004, FR-MKT-005, FR-HFT-002, NFR-AUD-001, NFR-SEC-001 |
| OQ-038 | PROPOSED — OWNER REVIEW REQUIRED | FR-MKT-007, FR-MKT-008 |
| OQ-039 | PROPOSED — OWNER REVIEW REQUIRED | FR-MKT-007, FR-MKT-008, FR-MKT-009, NFR-DET-002 |
| OQ-040 | PROPOSED — OWNER REVIEW REQUIRED | FR-ALGO-001, FR-ALGO-002, NFR-REP-001 |
| OQ-041 | PROPOSED — OWNER REVIEW REQUIRED | FR-ALGO-002, FR-HFT-001, FR-HFT-002, FR-HFT-003, FR-HFT-004, FR-ADM-003, NFR-COR-003, NFR-REP-001, NFR-PERF-001 |
| OQ-042 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-SIM-003, FR-FEE-005, FR-PF-003, FR-ALGO-002, FR-ADM-003, FR-ADM-004, NFR-AUD-001, NFR-REP-001, NFR-SEC-002 |
| OQ-043 | PROPOSED — OWNER REVIEW REQUIRED | FR-ACT-003, FR-SIM-004, FR-ALGO-001, NFR-SEC-001 |
| OQ-044 | PROPOSED — OWNER REVIEW REQUIRED | FR-MKT-005, FR-MKT-006, FR-MKT-009, FR-PF-004 |
| OQ-045 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-SIM-003, FR-TRD-004, FR-ADM-004, NFR-AUD-001, NFR-SEC-002 |
| OQ-046 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-004, FR-BAL-005, FR-BAL-007, FR-BAL-008, FR-TRD-002, FR-TRD-003, FR-FEE-004, FR-PF-002, FR-PF-003, FR-PF-004, FR-HFT-002, FR-HFT-003, FR-ADM-003, NFR-DET-002, NFR-COR-001, NFR-COR-002, NFR-COR-003, NFR-REP-001 |

### 11.2 Nghiệp vụ → Yêu cầu: quy tắc

Các dòng BR được ánh xạ đúng theo ID. Quy tắc nguồn tổng hợp có thể hỗ trợ nhiều yêu cầu hẹp hơn với tập OQ chính xác khác nhau; điều khoản được dẫn không âm thầm đưa vào chính sách không liên quan. Không yêu cầu APPROVED nào viện dẫn BR đề xuất.

| Quy tắc nghiệp vụ | Trạng thái nguồn | Tham chiếu yêu cầu |
|-------------------|------------------|-------------------|
| BR-ORD-001 | APPROVED | FR-ORD-018, FR-MAT-001, FR-MAT-002, FR-MAT-007, FR-MKT-003, FR-ALGO-001, FR-HFT-001, FR-ADM-002, NFR-DET-001, NFR-REP-001 |
| BR-ORD-002 | APPROVED | FR-ORD-002, FR-ORD-003, FR-ORD-004, FR-ORD-007, FR-ORD-019 |
| BR-ORD-003 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-012, FR-ORD-013, FR-ORD-014, FR-ORD-015, FR-ORD-016, FR-ORD-019, FR-ORD-020, FR-BAL-003, FR-BAL-008, FR-MKT-003, FR-HFT-004, NFR-AUD-001 |
| BR-BAL-001 | APPROVED | FR-ACT-001, FR-BAL-001, FR-BAL-002, FR-ALGO-001, FR-ADM-002, NFR-COR-001 |
| BR-BAL-002 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-003, FR-BAL-004, FR-BAL-005, FR-BAL-006, FR-BAL-007, FR-BAL-008, NFR-COR-001, NFR-COR-002 |
| BR-BAL-003 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-020, FR-BAL-007, FR-BAL-008, FR-TRD-002, NFR-COR-001 |
| BR-BAL-004 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-018, FR-BAL-003 |
| BR-TRD-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-SIM-003, FR-TRD-004, FR-ADM-004, NFR-AUD-001, NFR-SEC-002 |
| BR-STL-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-007, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-TRD-003, FR-PF-001, NFR-COR-001 |
| BR-MKT-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-MKT-005, FR-MKT-006, FR-MKT-007, FR-MKT-008, FR-MKT-009, FR-PF-004 |
| BR-MKT-002 | PROPOSED — OWNER REVIEW REQUIRED | FR-TRD-001, FR-MKT-002, FR-MKT-003, FR-MKT-004, FR-MKT-005, FR-HFT-002, NFR-AUD-001, NFR-SEC-001 |
| BR-PF-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-PF-001, FR-PF-002, FR-PF-003, FR-PF-004, FR-HFT-002, FR-ADM-003, NFR-COR-002, NFR-COR-003 |
| BR-ACT-001 | APPROVED | FR-SIM-004, FR-MAT-004, FR-ADM-002 |
| BR-ORD-004 | APPROVED | FR-ORD-001, FR-ORD-004, FR-ORD-005, FR-ORD-006, FR-MAT-003, FR-ALGO-001, FR-HFT-003 |
| BR-ORD-005 | APPROVED | FR-ORD-002, FR-MAT-003, FR-TRD-001 |
| BR-ORD-006 | APPROVED | FR-MAT-004, FR-MAT-005, FR-MKT-006, FR-ALGO-001, FR-ADM-002 |
| BR-FEE-001 | APPROVED | FR-BAL-001, FR-BAL-004, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-FEE-001, FR-FEE-002, FR-HFT-002, NFR-COR-001 |
| BR-FEE-002 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-006, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-FEE-003, FR-FEE-004, FR-FEE-005, FR-PF-002, FR-HFT-002, FR-ADM-003, NFR-DET-002, NFR-COR-001, NFR-REP-001 |
| BR-MKT-003 | APPROVED | FR-ACT-001, FR-MKT-001 |
| BR-SIM-001 | APPROVED | FR-ACT-002, FR-SIM-004, FR-MKT-006, FR-PF-003, FR-ADM-001, FR-ADM-003, NFR-COR-001, NFR-SEC-002 |
| BR-PREC-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-BAL-004, FR-BAL-005, FR-BAL-008, FR-FEE-004, FR-PF-002, FR-PF-003, FR-PF-004, FR-HFT-002, FR-HFT-003, FR-ADM-003, NFR-DET-002, NFR-COR-001, NFR-COR-002, NFR-COR-003, NFR-REP-001 |
| BR-ORD-007 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-014, FR-ORD-016, FR-ORD-017, FR-ORD-018, FR-ORD-019, FR-ORD-020, FR-MAT-006, FR-MAT-007, FR-BAL-008, FR-HFT-001, FR-HFT-004, NFR-DET-002, NFR-AUD-001, NFR-REP-001, NFR-SEC-001 |
| BR-SIM-002 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-001, FR-SIM-002, FR-SIM-003, FR-ORD-015, FR-ORD-019, FR-MAT-007, FR-FEE-005, FR-PF-003, FR-ALGO-002, FR-ADM-003, FR-ADM-004, NFR-AUD-001, NFR-REP-001, NFR-SEC-002 |
| BR-ACT-002 | PROPOSED — OWNER REVIEW REQUIRED | FR-ACT-003, FR-SIM-004, FR-ALGO-001, NFR-SEC-001 |
| BR-MKT-004 | PROPOSED — OWNER REVIEW REQUIRED | FR-MKT-007, FR-MKT-008, FR-MKT-009, NFR-DET-002 |
| BR-ALGO-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-ALGO-001, FR-ALGO-002, FR-HFT-001, FR-HFT-002, FR-HFT-003, FR-HFT-004, FR-ADM-003, NFR-COR-003, NFR-REP-001, NFR-PERF-001 |

### 11.3 Yêu cầu → Kiểm chứng

Mỗi yêu cầu ánh xạ tới các tiêu chí chấp nhận đánh số của chính nó. Cột kịch bản và use case bổ sung ngữ cảnh kiểm chứng rộng hơn; “Tiêu chí riêng” nghĩa là yêu cầu được thể hiện đầy đủ bằng tiêu chí riêng, không tạo use case không có cơ sở. Tham chiếu như FR-ACT-001/AC-1 xác định tiêu chí đánh số dưới đúng tiêu đề yêu cầu. Tiêu chí có chi tiết chưa giải quyết vẫn ghi rõ giới hạn TBD.

| Yêu cầu | Trạng thái | Tiêu chí chấp nhận | Kịch bản nghiệm thu | Use case |
|---------|------------|-------------------|---------------------|----------|
| FR-ACT-001 | APPROVED | FR-ACT-001/AC-1, FR-ACT-001/AC-2 | AS-031 | Tiêu chí riêng |
| FR-ACT-002 | APPROVED | FR-ACT-002/AC-1, FR-ACT-002/AC-2 | AS-012 | UC-009 |
| FR-ACT-003 | PROVISIONAL | FR-ACT-003/AC-1, FR-ACT-003/AC-2 | AS-027 | UC-009, UC-010 |
| FR-SIM-001 | PROVISIONAL | FR-SIM-001/AC-1, FR-SIM-001/AC-2 | AS-028 | UC-001, UC-002, UC-003, UC-004, UC-009 |
| FR-SIM-002 | PROVISIONAL | FR-SIM-002/AC-1, FR-SIM-002/AC-2 | AS-022 | UC-011 |
| FR-SIM-003 | PROVISIONAL | FR-SIM-003/AC-1, FR-SIM-003/AC-2 | AS-022 | UC-011 |
| FR-SIM-004 | PROVISIONAL | FR-SIM-004/AC-1, FR-SIM-004/AC-2, FR-SIM-004/AC-3 | AS-030 | UC-009 |
| FR-ORD-001 | APPROVED | FR-ORD-001/AC-1, FR-ORD-001/AC-2 | AS-031 | UC-003, UC-004, UC-008 |
| FR-ORD-002 | APPROVED | FR-ORD-002/AC-1, FR-ORD-002/AC-2 | AS-001, AS-006 | UC-001, UC-002 |
| FR-ORD-003 | APPROVED | FR-ORD-003/AC-1, FR-ORD-003/AC-2 | AS-006, AS-013 | UC-001, UC-002, UC-005, UC-006 |
| FR-ORD-004 | APPROVED | FR-ORD-004/AC-1, FR-ORD-004/AC-2 | AS-007, AS-008, AS-009 | UC-003, UC-004, UC-005, UC-006 |
| FR-ORD-005 | APPROVED | FR-ORD-005/AC-1, FR-ORD-005/AC-2 | AS-008 | UC-003, UC-005, UC-008 |
| FR-ORD-006 | APPROVED | FR-ORD-006/AC-1, FR-ORD-006/AC-2 | AS-009 | UC-004, UC-005, UC-008 |
| FR-ORD-007 | APPROVED | FR-ORD-007/AC-1, FR-ORD-007/AC-2 | AS-031 | Tiêu chí riêng |
| FR-ORD-008 | PROVISIONAL | FR-ORD-008/AC-1, FR-ORD-008/AC-2 | AS-023 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-ORD-009 | PROVISIONAL | FR-ORD-009/AC-1, FR-ORD-009/AC-2 | AS-023 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-ORD-010 | PROVISIONAL | FR-ORD-010/AC-1, FR-ORD-010/AC-2 | AS-023 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-ORD-011 | PROVISIONAL | FR-ORD-011/AC-1, FR-ORD-011/AC-2 | AS-023 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-ORD-012 | PROVISIONAL | FR-ORD-012/AC-1, FR-ORD-012/AC-2, FR-ORD-012/AC-3 | AS-017 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-ORD-013 | PROVISIONAL | FR-ORD-013/AC-1, FR-ORD-013/AC-2 | Tiêu chí riêng | Tiêu chí riêng |
| FR-ORD-014 | PROVISIONAL | FR-ORD-014/AC-1, FR-ORD-014/AC-2 | AS-025 | UC-007 |
| FR-ORD-015 | PROVISIONAL | FR-ORD-015/AC-1, FR-ORD-015/AC-2 | AS-017 | UC-003, UC-004, UC-007 |
| FR-ORD-016 | PROVISIONAL | FR-ORD-016/AC-1, FR-ORD-016/AC-2 | AS-016, AS-017 | UC-007 |
| FR-ORD-017 | PROVISIONAL | FR-ORD-017/AC-1, FR-ORD-017/AC-2 | AS-030 | Tiêu chí riêng |
| FR-ORD-018 | PROVISIONAL | FR-ORD-018/AC-1, FR-ORD-018/AC-2 | AS-025 | Tiêu chí riêng |
| FR-ORD-019 | PROVISIONAL | FR-ORD-019/AC-1, FR-ORD-019/AC-2 | AS-030 | Tiêu chí riêng |
| FR-ORD-020 | PROVISIONAL | FR-ORD-020/AC-1, FR-ORD-020/AC-2, FR-ORD-020/AC-3 | AS-016, AS-025 | UC-007 |
| FR-MAT-001 | APPROVED | FR-MAT-001/AC-1, FR-MAT-001/AC-2 | AS-001 | UC-001, UC-002, UC-003, UC-004 |
| FR-MAT-002 | APPROVED | FR-MAT-002/AC-1, FR-MAT-002/AC-2, FR-MAT-002/AC-3 | AS-002, AS-003, AS-004, AS-006 | UC-001, UC-002, UC-003, UC-004, UC-005 |
| FR-MAT-003 | APPROVED | FR-MAT-003/AC-1, FR-MAT-003/AC-2 | AS-005, AS-008, AS-009, AS-013 | UC-001, UC-002, UC-003, UC-004, UC-005 |
| FR-MAT-004 | APPROVED | FR-MAT-004/AC-1, FR-MAT-004/AC-2 | AS-010 | UC-001, UC-002, UC-003, UC-004, UC-006 |
| FR-MAT-005 | APPROVED | FR-MAT-005/AC-1, FR-MAT-005/AC-2 | AS-011 | UC-006 |
| FR-MAT-006 | PROVISIONAL | FR-MAT-006/AC-1, FR-MAT-006/AC-2 | AS-025 | Tiêu chí riêng |
| FR-MAT-007 | PROVISIONAL | FR-MAT-007/AC-1, FR-MAT-007/AC-2 | AS-025 | UC-007, UC-010, UC-011 |
| FR-BAL-001 | APPROVED | FR-BAL-001/AC-1, FR-BAL-001/AC-2 | AS-031 | Tiêu chí riêng |
| FR-BAL-002 | APPROVED | FR-BAL-002/AC-1, FR-BAL-002/AC-2 | Tiêu chí riêng | Tiêu chí riêng |
| FR-BAL-003 | PROVISIONAL | FR-BAL-003/AC-1, FR-BAL-003/AC-2 | AS-014 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-BAL-004 | PROVISIONAL | FR-BAL-004/AC-1, FR-BAL-004/AC-2 | AS-015 | UC-001 |
| FR-BAL-005 | PROVISIONAL | FR-BAL-005/AC-1, FR-BAL-005/AC-2 | AS-014 | UC-003 |
| FR-BAL-006 | PROVISIONAL | FR-BAL-006/AC-1, FR-BAL-006/AC-2 | Tiêu chí riêng | UC-002, UC-004 |
| FR-BAL-007 | PROVISIONAL | FR-BAL-007/AC-1, FR-BAL-007/AC-2 | AS-015 | UC-001 |
| FR-BAL-008 | PROVISIONAL | FR-BAL-008/AC-1, FR-BAL-008/AC-2 | AS-015, AS-016 | UC-003, UC-004, UC-007 |
| FR-TRD-001 | PROVISIONAL | FR-TRD-001/AC-1, FR-TRD-001/AC-2 | Tiêu chí riêng | Tiêu chí riêng |
| FR-TRD-002 | PROVISIONAL | FR-TRD-002/AC-1, FR-TRD-002/AC-2 | AS-018 | UC-001, UC-002, UC-003, UC-004 |
| FR-TRD-003 | PROVISIONAL | FR-TRD-003/AC-1, FR-TRD-003/AC-2 | AS-018 | Tiêu chí riêng |
| FR-TRD-004 | PROVISIONAL | FR-TRD-004/AC-1, FR-TRD-004/AC-2 | AS-021 | UC-011 |
| FR-FEE-001 | APPROVED | FR-FEE-001/AC-1, FR-FEE-001/AC-2 | AS-013 | Tiêu chí riêng |
| FR-FEE-002 | APPROVED | FR-FEE-002/AC-1, FR-FEE-002/AC-2 | AS-013, AS-018 | Tiêu chí riêng |
| FR-FEE-003 | PROVISIONAL | FR-FEE-003/AC-1, FR-FEE-003/AC-2 | AS-024 | Tiêu chí riêng |
| FR-FEE-004 | PROVISIONAL | FR-FEE-004/AC-1, FR-FEE-004/AC-2 | AS-018, AS-024 | Tiêu chí riêng |
| FR-FEE-005 | PROVISIONAL | FR-FEE-005/AC-1, FR-FEE-005/AC-2 | AS-024, AS-030 | UC-009 |
| FR-MKT-001 | APPROVED | FR-MKT-001/AC-1, FR-MKT-001/AC-2 | AS-031 | UC-008 |
| FR-MKT-002 | PROVISIONAL | FR-MKT-002/AC-1, FR-MKT-002/AC-2 | AS-027 | Tiêu chí riêng |
| FR-MKT-003 | PROVISIONAL | FR-MKT-003/AC-1, FR-MKT-003/AC-2 | AS-027 | Tiêu chí riêng |
| FR-MKT-004 | PROVISIONAL | FR-MKT-004/AC-1, FR-MKT-004/AC-2 | AS-027 | Tiêu chí riêng |
| FR-MKT-005 | PROVISIONAL | FR-MKT-005/AC-1, FR-MKT-005/AC-2 | AS-027 | Tiêu chí riêng |
| FR-MKT-006 | PROVISIONAL | FR-MKT-006/AC-1, FR-MKT-006/AC-2 | AS-019, AS-020 | Tiêu chí riêng |
| FR-MKT-007 | PROVISIONAL | FR-MKT-007/AC-1, FR-MKT-007/AC-2 | AS-019 | Tiêu chí riêng |
| FR-MKT-008 | PROVISIONAL | FR-MKT-008/AC-1, FR-MKT-008/AC-2 | AS-019, AS-028 | Tiêu chí riêng |
| FR-MKT-009 | PROVISIONAL | FR-MKT-009/AC-1, FR-MKT-009/AC-2 | AS-020, AS-028 | UC-011 |
| FR-PF-001 | PROVISIONAL | FR-PF-001/AC-1, FR-PF-001/AC-2 | AS-026 | Tiêu chí riêng |
| FR-PF-002 | PROVISIONAL | FR-PF-002/AC-1, FR-PF-002/AC-2 | AS-026 | Tiêu chí riêng |
| FR-PF-003 | PROVISIONAL | FR-PF-003/AC-1, FR-PF-003/AC-2 | AS-026 | UC-009 |
| FR-PF-004 | PROVISIONAL | FR-PF-004/AC-1, FR-PF-004/AC-2, FR-PF-004/AC-3 | AS-026 | UC-010 |
| FR-ALGO-001 | PROVISIONAL | FR-ALGO-001/AC-1, FR-ALGO-001/AC-2 | AS-029 | UC-010 |
| FR-ALGO-002 | PROVISIONAL | FR-ALGO-002/AC-1, FR-ALGO-002/AC-2 | AS-029, AS-031 | UC-010 |
| FR-HFT-001 | PROVISIONAL | FR-HFT-001/AC-1, FR-HFT-001/AC-2, FR-HFT-001/AC-3 | AS-029 | UC-009, UC-010 |
| FR-HFT-002 | PROVISIONAL | FR-HFT-002/AC-1, FR-HFT-002/AC-2 | AS-029 | UC-010 |
| FR-HFT-003 | PROVISIONAL | FR-HFT-003/AC-1, FR-HFT-003/AC-2 | AS-029 | UC-010 |
| FR-HFT-004 | PROVISIONAL | FR-HFT-004/AC-1, FR-HFT-004/AC-2 | AS-029 | UC-010 |
| FR-ADM-001 | APPROVED | FR-ADM-001/AC-1, FR-ADM-001/AC-2 | AS-012 | UC-009 |
| FR-ADM-002 | APPROVED | FR-ADM-002/AC-1, FR-ADM-002/AC-2 | AS-012 | UC-009 |
| FR-ADM-003 | PROVISIONAL | FR-ADM-003/AC-1, FR-ADM-003/AC-2 | AS-030 | UC-009 |
| FR-ADM-004 | PROVISIONAL | FR-ADM-004/AC-1, FR-ADM-004/AC-2 | AS-021 | UC-011 |
| NFR-DET-001 | APPROVED | NFR-DET-001/AC-1, NFR-DET-001/AC-2 | AS-002, AS-003 | Tiêu chí riêng |
| NFR-DET-002 | PROVISIONAL | NFR-DET-002/AC-1, NFR-DET-002/AC-2 | AS-025 | Tiêu chí riêng |
| NFR-COR-001 | PROVISIONAL | NFR-COR-001/AC-1, NFR-COR-001/AC-2 | AS-018 | Tiêu chí riêng |
| NFR-COR-002 | PROVISIONAL | NFR-COR-002/AC-1, NFR-COR-002/AC-2 | AS-024 | Tiêu chí riêng |
| NFR-COR-003 | PROVISIONAL | NFR-COR-003/AC-1, NFR-COR-003/AC-2 | AS-029 | Tiêu chí riêng |
| NFR-AUD-001 | PROVISIONAL | NFR-AUD-001/AC-1, NFR-AUD-001/AC-2 | Tiêu chí riêng | Tiêu chí riêng |
| NFR-REP-001 | PROVISIONAL | NFR-REP-001/AC-1, NFR-REP-001/AC-2 | AS-029 | Tiêu chí riêng |
| NFR-SEC-001 | PROVISIONAL | NFR-SEC-001/AC-1, NFR-SEC-001/AC-2 | AS-027 | Tiêu chí riêng |
| NFR-SEC-002 | PROVISIONAL | NFR-SEC-002/AC-1, NFR-SEC-002/AC-2 | AS-021 | Tiêu chí riêng |
| NFR-PERF-001 | PROVISIONAL | NFR-PERF-001/AC-1, NFR-PERF-001/AC-2 | AS-031 | Tiêu chí riêng |

## 12. Hoãn / Ngoài phạm vi

Trạng thái áp dụng cho quyết định hoãn. Loại trừ còn đề xuất vẫn là PROVISIONAL; các ví dụ ở đây không cho phép triển khai trong tương lai. Định nghĩa khái niệm được hoãn trong BUSINESS.md phục vụ học tập, không phải yêu cầu V1.

| Khả năng nằm ngoài V1 | Trạng thái hoãn | Cơ sở nghiệp vụ chính xác |
|-----------------------|-----------------|--------------------------|
| Giao dịch tiền thật; quyền/quy đổi tiền mã hóa thật | APPROVED | OQ-002; BR-MKT-003; BUSINESS.md mục 3 |
| Lưu ký thật; chuyển khoản ngân hàng; nạp và rút | APPROVED | OQ-002; BR-MKT-003; BUSINESS.md mục 3 |
| Thanh toán blockchain | APPROVED | OQ-002; BR-MKT-003 |
| Tích hợp sàn giao dịch thật hoặc dữ liệu thị trường bên ngoài | APPROVED | OQ-002; BR-MKT-003 |
| Quote Asset, cặp giao dịch bổ sung hoặc cặp chéo | APPROVED | OQ-001; BR-MKT-003 |
| Vay, margin, đòn bẩy, bán khống | APPROVED | OQ-005; BR-BAL-001 |
| Futures, perpetuals, options, phái sinh khác | APPROVED | OQ-005; BR-BAL-001 |
| Market Buy theo ngân sách Quote | APPROVED | OQ-006; BR-ORD-004 |
| Chỉ thị IOC/FOK riêng | APPROVED | OQ-012; BR-ORD-002 |
| Stop, Stop-Limit, Trailing Stop, Iceberg, Pegged, lệnh nâng cao/có điều kiện khác | APPROVED | OQ-004; BR-ORD-004; OQ-032 chỉ nhắc lại loại trừ đã duyệt này |
| Gom các tài khoản theo chủ sở hữu con người để STP | APPROVED | OQ-011; BR-ORD-006 |
| Sửa lệnh | PROVISIONAL | OQ-028; BR-ORD-007 |
| Cancel-and-replace kết hợp | PROVISIONAL | OQ-029; BR-ORD-007 |
| GTC tự hết hạn, thời hạn GTC tối đa, trạng thái EXPIRED | PROVISIONAL | OQ-030; BR-ORD-007 |
| Backtesting dữ liệu lịch sử, bộ công cụ tối ưu hóa, chợ chiến lược, tính năng bộ nghiên cứu tổ chức | PROVISIONAL | OQ-040; BR-ALGO-001; BUSINESS.md mục 28 |
| Nhà tạo lập thị trường do hệ thống bảo đảm hoạt động liên tục và tự bổ sung tài sản | PROVISIONAL | OQ-036; BR-ACT-002; BUSINESS.md mục 27 |
| Vốn chiến lược dùng chung và chuyển tài sản giữa tài khoản | PROVISIONAL | OQ-043; BR-ACT-002 |
| Cấp vốn trong lúc chạy và chuyển tài sản giữa thí nghiệm | PROVISIONAL | OQ-042; BR-SIM-002 |
| Hiệu chỉnh, sửa, xóa hoặc đảo ngược Trade, kể cả ngoại lệ quản trị | PROVISIONAL | OQ-045; BR-TRD-001 |
| Jitter thời gian ngẫu nhiên | PROVISIONAL | OQ-041; BR-ALGO-001 |
| Giá vốn danh mục theo lô thuế/FIFO | PROVISIONAL | OQ-033; BR-PF-001; BUSINESS.md mục 25 |
| Khối lượng cửa sổ trượt 24 giờ và khoảng nến khác một phút | PROVISIONAL | OQ-038, OQ-039; BR-MKT-004 |
| Tuyên bố độ trễ/hiệu năng HFT production và đo kiểm mạng/phần cứng thật | PROVISIONAL | OQ-041; BR-ALGO-001; BUSINESS.md mục 29 đã xác định đây không phải sàn HFT production |
| Triển khai tuân thủ pháp lý thực tế | APPROVED — loại trừ phạm vi | OQ-002; BUSINESS.md mục 3; không tạo thêm khả năng tuân thủ |

## 13. Hoãn kiến trúc và triển khai

Tài liệu này **KHÔNG quyết định** bất kỳ nội dung nào sau đây. Chúng thuộc giai đoạn **Kiến trúc / Thiết kế hệ thống** về sau và cần được cho phép riêng:

- Monolith hay microservices, ranh giới dịch vụ hoặc phân chia trách nhiệm Go / Java / C++.
- Kafka, Redis, PostgreSQL, lược đồ cơ sở dữ liệu hoặc cách triển khai lưu trữ bền vững.
- REST API, giao thức WebSocket, định dạng thông điệp hoặc thiết kế giao diện trao đổi.
- Docker, Kubernetes, nhà cung cấp đám mây, cấu trúc triển khai hoặc hạ tầng.
- Mô hình luồng, cách thực hiện đồng thời, chiến lược khóa hoặc công nghệ tạo thứ tự.

Thứ tự nghiệp vụ chính thức, settlement ngay, số thập phân chính xác và giữ lịch sử xác định hành vi có ý nghĩa quan sát được từ bên ngoài. Chúng không chọn cơ chế giao dịch dữ liệu, kiểu lập trình, phương thức truyền, môi trường chạy hoặc mô hình lưu trữ. Bản nháp không bổ sung mã nguồn, khung dịch vụ, kế hoạch triển khai hoặc lựa chọn công nghệ.

## 14. Yêu cầu còn mở / Mục TBD

Các khoảng trống sau được phát hiện khi dẫn xuất yêu cầu; chúng không phải quyết định nghiệp vụ mới, mặc định đề xuất hay phê duyệt tự tạo. Việc giải quyết thuộc rà soát nghiệp vụ của chủ dự án/nhóm. Quy tắc đã duyệt vẫn có hiệu lực; OQ đề xuất cụ thể vẫn là PROVISIONAL, không bị xóa thành “chưa biết”.

| Khoảng trống | Quyết định / Thông tin nghiệp vụ còn thiếu | Nguồn và yêu cầu bị ảnh hưởng | Ranh giới hiện tại |
|--------------|---------------------------------------------|------------------------------|--------------------|
| TBD-001 | Quyền bắt đầu/kết thúc cụ thể, thời lượng/điều kiện kích hoạt kết thúc, vòng đời thí nghiệm đầy đủ và xử lý yêu cầu khi thời gian dừng. Nguồn không xác định tính năng tạm dừng/tiếp tục tổng quát. | OQ-035, OQ-042; FR-SIM-001, FR-SIM-002, FR-ADM-003; UC-009, UC-011 | Đã xác định lần chạy có giới hạn và tác động kết thúc; không tự tạo trạng thái thí nghiệm bổ sung hoặc giả định chỉ Administrator được kết thúc. |
| TBD-002 | Độ chính xác và giới hạn thời gian ảo/độ trễ, cách cung cấp quan sát bị trễ, thông tin số dư/lần khớp của chính mình có cùng độ trễ hay không và cách phân xử nghiệp vụ cho các hành động đến/kết thúc cùng lịch ngoài nguyên tắc thứ tự chính thức. | OQ-010, OQ-031, OQ-035, OQ-041; FR-MAT-007, FR-HFT-001, NFR-REP-001 | Giữ độ trễ cố định không âm và ưu tiên chấp nhận hiện có. Cách triển khai tạo thứ tự vẫn được hoãn, không phải khoảng trống yêu cầu cần giải bằng kỹ thuật tại đây. |
| TBD-003 | Mốc tham chiếu cho thời gian đến lần khớp đầu/khớp hết: nguồn chưa chọn lúc người tham gia quyết định, gửi, đến nền tảng hay được chấp nhận. | OQ-041; FR-HFT-004; AS-029 | Lần khớp thiếu/chưa đạt có chỉ tiêu không khả dụng; nghiệm thu giá trị thời gian chính xác chờ quyết định nghiệp vụ về mốc đo. |
| TBD-004 | Định danh/quản lý tài khoản người tham gia, khả năng quan sát/quyền quản trị chi tiết và quan hệ giữa chiến lược chạy trên tài khoản kịch bản với quy tắc tài khoản riêng từng người tham gia. | OQ-031, OQ-037, OQ-040, OQ-042, OQ-043; BUSINESS.md mục 4, 14, 23, 27; FR-ACT-003, FR-SIM-004, NFR-SEC-001 | Đã xác định chỉ hủy lệnh của mình và giữ riêng lịch sử tài khoản khác. Không bổ sung hành vi đăng ký/đăng nhập, quyền xem không giới hạn cho admin hoặc vốn người tham gia dùng chung. |
| TBD-005 | Thời hạn giữ lịch sử, nhu cầu xuất dữ liệu và chính sách truy cập chi tiết sau thí nghiệm. | OQ-035, OQ-042, OQ-045; FR-SIM-003, FR-TRD-004, NFR-AUD-001 | Giữ lịch sử và nhãn tính hợp lệ nghiên cứu. Không tự đặt thời hạn lưu, định dạng xuất hoặc ngoại lệ xóa dành cho quản trị viên. |
| TBD-006 | Mục tiêu định lượng về độ trễ, thông lượng, năng lực, người dùng đồng thời, tính sẵn sàng và phục hồi; tải công việc đại diện và điều kiện đo. | BUSINESS.md mục 29; OQ-041; NFR-PERF-001 | Mọi mục tiêu vận hành bằng số vẫn là TBD. Không tuyên bố HFT production hoặc tự đặt ngưỡng mức dịch vụ. |

Số dư ban đầu từng kịch bản, mức phí trong giới hạn đề xuất, ngày/lịch hành động, giá tham chiếu mua vào và độ trễ người tham gia là **đầu vào cấu hình**, không phải mặc định chung cần tự đặt. Thuật toán chiến lược, kể cả logic tạo lập thị trường thích ứng, không được thiết kế trong tài liệu yêu cầu này; việc không thiết kế chúng không cấm chiến lược tham gia thông thường vốn được cho phép. OQ-015 đến OQ-046 vẫn cần chủ dự án/nhóm phê duyệt rõ ràng trước khi yêu cầu tạm thời trở thành đã duyệt.

## 15. Danh sách kiểm tra rà soát yêu cầu

Các mục đã đánh dấu ghi nhận rà soát tính nhất quán tài liệu vào 2026-10-07, **không phải** chấp nhận của con người, hoàn tất triển khai hoặc kiểm thử hành vi đã chạy.

- [x] Đã kiểm tra riêng toàn bộ 46 OQ về tác động yêu cầu; ghi rõ OQ-032 chủ ý không tạo yêu cầu độc lập mới.
- [x] Thể hiện mọi OQ-001 đến OQ-014 đã duyệt mà không đưa chi tiết vốn, trạng thái, phí hoặc thí nghiệm còn đề xuất vào yêu cầu đã duyệt của chúng.
- [x] Giữ mọi yêu cầu có bất kỳ phụ thuộc OQ-015 đến OQ-046 ở PROVISIONAL; giữ trạng thái nguồn đề xuất trong ma trận quyết định và quy tắc.
- [x] Dùng ID OQ và BR chính xác, giữ đủ 19 ID/ý nghĩa bất biến và cung cấp ánh xạ yêu cầu tới tiêu chí/kịch bản/use case.
- [x] Giữ Market kết thúc ngay phần dư, bảo vệ tuyệt đối, giá thực hiện của lệnh đang chờ, ưu tiên giá/chấp nhận và không vượt hàng theo giờ máy khách hay kích thước.
- [x] Giữ STP hủy phần đang chờ của chính tài khoản, tiếp tục lệnh mới vào và không tạo Trade/khối lượng/Last Price/phí cho đối ứng bị ngăn.
- [x] Giữ phân loại Maker/Taker theo từng Trade và trường hợp cùng một lệnh là Taker trước, Maker sau.
- [x] Tách Simulation Funding khỏi Trade, thực hiện giao dịch, khối lượng, lợi nhuận giao dịch và Last Price.
- [x] Có các kịch bản khớp một phần, thiếu vốn, giữ/giải phóng, giữ lịch sử sau hủy, trạng thái kết thúc, settlement một lần, dữ liệu từ Trade, nến trống, lịch sử bất biến và kết thúc thí nghiệm.
- [x] Đã kiểm tra phép tính, đơn vị, quy tắc thập phân, tham chiếu nguồn, định dạng bảng và liên kết nguồn cục bộ.
- [x] Ghi chi tiết thiếu cơ sở là TBD; không thêm mặc định nghiệp vụ, mục tiêu hiệu năng vận hành bằng số, lựa chọn kiến trúc hoặc cách triển khai.
- [ ] Chủ dự án/nhóm rà soát quyết định nghiệp vụ tạm thời và yêu cầu tạm thời tương ứng.
- [ ] Chủ dự án/nhóm giải quyết các TBD áp dụng và ghi kết quả thực tế qua quy trình rà soát nghiệp vụ.
- [ ] Chủ dự án/nhóm chính thức chấp nhận đặc tả yêu cầu này; tài liệu không khẳng định đã có phê duyệt đó.
