# Lab 21 — Phân tích rủi ro AI qua case study thực tế

- Họ và tên: Đỗ Lê Việt Anh
- MSSV / mã học viên: 2A202602491
- Lớp: K04-L34-P2
- Ngành đã chọn: Y tế / symptom checker / health assistant

### 1. Industry Risk Snapshot

| Nội dung | Đánh giá của tôi và lý do |
| --- | --- |
| Những tác hại chính có thể xảy ra | (1) Chẩn đoán/cảnh báo sai (bỏ sót bệnh nặng hoặc báo động giả) làm bệnh nhân điều trị muộn hoặc không cần thiết. (2) Thiên lệch giữa các nhóm dân cư khiến một nhóm bệnh nhân bị tiếp cận chăm sóc kém hơn. (3) Lộ hoặc lạm dụng hồ sơ bệnh án. (4) Bác sĩ quá tin AI (automation bias) hoặc mệt mỏi vì quá nhiều cảnh báo (alert fatigue). Người bị ảnh hưởng: bệnh nhân, bác sĩ/điều dưỡng, bệnh viện. |
| Mức độ high-stakes | **Cao.** Quyết định y tế tác động trực tiếp đến sức khỏe và tính mạng, nhiều hậu quả khó hoặc không thể đảo ngược (ví dụ điều trị muộn nhiễm trùng huyết). Bệnh nhân thường không có khả năng tự kiểm chứng kết quả của AI. |
| Dữ liệu nhạy cảm có thể được sử dụng | Hồ sơ bệnh án điện tử (EHR), chẩn đoán, kết quả xét nghiệm, đơn thuốc, hình ảnh y khoa, lịch sử thanh toán/chi phí chăm sóc, thông tin nhân khẩu học như chủng tộc, tuổi. (Bài này không dùng dữ liệu thật của bệnh nhân.) |
| Nhu cầu human review | **Cao.** Bác sĩ/điều dưỡng phải xem xét kết quả AI *trước* khi ra quyết định điều trị hoặc phân bổ nguồn lực. Bộ phận quản trị chất lượng/data science của bệnh viện phải kiểm tra mô hình *trước khi triển khai* và định kỳ sau triển khai (hiệu năng thực tế, công bằng giữa các nhóm). Lý do: sai sót có thể gây hại nghiêm trọng, và các case dưới đây cho thấy lỗi có thể tồn tại âm thầm nhiều năm. |

### 2. Case study 1 — Epic Sepsis Model bỏ sót phần lớn ca nhiễm trùng huyết

#### Brief Case

- Tổ chức / sản phẩm AI: Epic Sepsis Model (ESM), mô hình dự đoán nhiễm trùng huyết tích hợp sẵn trong phần mềm hồ sơ bệnh án điện tử của Epic Systems; được đánh giá độc lập bởi Michigan Medicine (Đại học Michigan).
- Thời gian, địa điểm / bối cảnh: Hoa Kỳ. Nghiên cứu xác thực bên ngoài dùng dữ liệu từ 06/12/2018 đến 20/10/2019 tại hệ thống y tế học thuật Michigan Medicine; công bố ngày 21/06/2021.
- AI được dùng để làm gì: Chấm điểm nguy cơ nhiễm trùng huyết của bệnh nhân nội trú, phát cảnh báo để đội lâm sàng phát hiện sớm và điều trị kịp thời.
- Vấn đề hoặc sự kiện đáng chú ý: Khi đánh giá độc lập, mô hình có khả năng phân biệt kém và tạo nhiều cảnh báo. Nhóm tác giả kết luận mô hình bỏ sót phần lớn ca nhiễm trùng huyết và chỉ phát hiện thêm rất ít ca mà bác sĩ chưa nhận ra.
- Số liệu có nguồn (cùng một nghiên cứu, ngưỡng điểm ≥ 6, 38.455 lượt nhập viện của 27.697 bệnh nhân, trong đó 2.552 lượt, tức 6,6%, có nhiễm trùng huyết):
  - Độ nhạy (sensitivity) 33%; độ đặc hiệu (specificity) 83%; AUC 0,63 (KTC 95%: 0,62–0,64); giá trị tiên đoán dương (PPV) 12%.
  - Mô hình phát cảnh báo ở 18% tổng số lượt nhập viện (6.971 / 38.455).
  - Bỏ sót 67% ca nhiễm trùng huyết (1.709 / 2.552).
  - Cần đánh giá khoảng 8 bệnh nhân để tìm ra 1 ca nhiễm trùng huyết.
  - Mô hình chỉ xác định thêm 7% số ca nhiễm trùng huyết mà bác sĩ đã bỏ sót.
- Nguồn: Wong A, Otles E, Donnelly JP, et al. *External Validation of a Widely Implemented Proprietary Sepsis Prediction Model in Hospitalized Patients* — JAMA Internal Medicine — 21/06/2021 — https://jamanetwork.com/journals/jamainternalmedicine/fullarticle/2781307 — mục Results.
- Phân biệt bằng chứng và nhận định: *Nguồn xác nhận:* các số liệu hiệu năng và tải cảnh báo ở trên, đo tại **một** hệ thống bệnh viện. *Tôi suy luận:* tỷ lệ cảnh báo cao (18%) kết hợp PPV thấp (12%) có thể gây mệt mỏi cảnh báo; kết quả ở bệnh viện khác có thể khác. Nghiên cứu này không chứng minh trực tiếp số bệnh nhân bị tổn hại hay tử vong vì lỗi của mô hình.

#### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Bệnh nhân nội trú đang diễn tiến nhiễm trùng huyết; đội lâm sàng dựa vào (hoặc bị làm phiền bởi) cảnh báo của mô hình để quyết định đánh giá và dùng kháng sinh sớm. |
| Stakeholder bị ảnh hưởng | Bệnh nhân nội trú; bác sĩ và điều dưỡng nhận cảnh báo; bệnh viện (tài nguyên, trách nhiệm); nhà cung cấp phần mềm. |
| Failure mode | Harmful advice (cảnh báo sai: bỏ sót ca thật và báo động giả nhiều); có thể kéo theo over-reliance hoặc mệt mỏi cảnh báo (nhận định của tôi, nghiên cứu không đo trực tiếp). |
| Layer bắt đầu lỗi | **Model** (khả năng phân biệt kém, AUC 0,63). *Tôi suy luận thêm:* có thể kèm lỗi **UX** do ngưỡng cảnh báo gây quá tải; nguồn không kết luận nguyên nhân gốc. |
| Harm xảy ra là gì? | *Đã được đo (có nguồn):* mô hình bỏ sót 67% ca nhiễm trùng huyết và tạo cảnh báo ở 18% lượt nhập viện. *Nguy cơ (chưa có nguồn chứng minh trong nghiên cứu này):* bệnh nhân được điều trị muộn, hoặc bác sĩ bỏ qua cảnh báo thật do mệt mỏi cảnh báo. |
| Harm lens | Injury (nguy cơ tổn hại thể chất do phát hiện và điều trị muộn nhiễm trùng huyết). |
| Severity | High — nhiễm trùng huyết là cấp cứu đe dọa tính mạng nếu điều trị muộn (nhận định của tôi). |
| Scale | Số liệu đo được: 38.455 lượt nhập viện tại một bệnh viện. Số bệnh nhân toàn quốc chưa đủ dữ liệu; tựa bài báo gọi mô hình là "widely implemented" nên phạm vi tiềm năng rộng (nhận định). |
| Probability | Đo được: 67% ca nhiễm trùng huyết bị mô hình bỏ sót. Xác suất gây hại thực tế cho bệnh nhân chưa đủ dữ liệu vì bác sĩ còn có phán đoán độc lập. |
| Frequency | Lặp lại mỗi khi mô hình chạy trên bệnh nhân nội trú; 18% lượt nhập viện bị cảnh báo trong giai đoạn nghiên cứu. |
| Vì sao? | Căn cứ vào số liệu của Wong et al. (2021). Giới hạn: một bệnh viện, một giai đoạn; nghiên cứu đo hiệu năng dự báo, không đo kết cục bệnh nhân; phần "nguy cơ" là suy luận của tôi. Human review cần có: bác sĩ xem cảnh báo cùng dấu hiệu lâm sàng; bệnh viện kiểm định cục bộ mô hình trước khi dùng. |

### 3. Case study 2 — Thuật toán quản lý chăm sóc sức khỏe thiên lệch chủng tộc

#### Brief Case

- Tổ chức / sản phẩm AI: Một thuật toán dự đoán nguy cơ thương mại (không nêu tên trong bài báo) dùng để chọn bệnh nhân vào chương trình điều phối chăm sóc (care coordination); nhóm nghiên cứu Obermeyer và cộng sự phân tích dữ liệu từ một bệnh viện học thuật lớn.
- Thời gian, địa điểm / bối cảnh: Hoa Kỳ; bài báo đăng trên tạp chí *Science* ngày 25/10/2019.
- AI được dùng để làm gì: Xếp hạng điểm nguy cơ để quyết định bệnh nhân nào được đưa vào chương trình chăm sóc bổ sung.
- Vấn đề hoặc sự kiện đáng chú ý: Thuật toán dùng **chi phí y tế** làm đại diện (proxy) cho **nhu cầu chăm sóc**. Vì bệnh nhân da đen thường nhận ít dịch vụ y tế hơn dù bệnh nặng như nhau, thuật toán đánh giá họ khỏe hơn thực tế và loại họ khỏi chương trình.
- Số liệu có nguồn:
  - Dữ liệu phân tích: 6.079 bệnh nhân da đen và 43.539 bệnh nhân da trắng.
  - Tỷ lệ bệnh nhân da đen trong chương trình có thể tăng từ 17,7% lên 46,5% nếu sửa thiên lệch.
  - Các thuật toán tương tự được dùng cho khoảng 200 triệu người mỗi năm (theo bài tường thuật).
  - Nhóm nghiên cứu xây mô hình thay thế (dựa trên chi phí có thể tránh được và các đợt bùng phát bệnh mạn tính), giảm thiên lệch khoảng 84%.
- Nguồn: (1) Obermeyer Z, Powers B, Vogeli C, Mullainathan S. *Dissecting racial bias in an algorithm used to manage the health of populations* — Science, 366(6464) — 25/10/2019 — https://www.science.org/doi/10.1126/science.aax2342. (2) *How computer algorithms help spread racial bias in US healthcare, and how they can help fix it* — TechXplore — 25/10/2019 — https://techxplore.com/news/2019-10-algorithms-racial-bias-healthcare.amp (nguồn của các số liệu 17,7%, 46,5%, 200 triệu, 84% mà tôi đã đọc).
- Phân biệt bằng chứng và nhận định: *Nguồn xác nhận:* cơ chế proxy chi phí, cỡ mẫu và các tỷ lệ trên. *Tôi chưa tự đọc được toàn văn bài Science* (trang tạp chí chặn truy cập tự động), nên các số liệu lấy qua bài tường thuật; cần đối chiếu lại với bài gốc trước khi nộp. *Tôi suy luận:* hậu quả cụ thể cho từng bệnh nhân (bệnh nặng hơn do thiếu chăm sóc) là suy ra từ phát hiện "cùng mức điểm thì bệnh nhân da đen ốm nặng hơn".

#### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Bước chấm điểm nguy cơ để quyết định ai được vào chương trình điều phối chăm sóc dành cho bệnh nhân phức tạp. |
| Stakeholder bị ảnh hưởng | Bệnh nhân da đen (bị loại bất công); toàn bộ bệnh nhân cần chăm sóc bổ sung; nhân viên chương trình; bệnh viện và đơn vị bảo hiểm sử dụng thuật toán. |
| Failure mode | Bias / fairness: thiên lệch hệ thống do chọn biến mục tiêu sai (chi phí không phản ánh đúng nhu cầu sức khỏe). |
| Layer bắt đầu lỗi | **Model** (chọn nhãn/biến mục tiêu khi thiết kế). Dữ liệu chi phí phản ánh bất bình đẳng sẵn có trong tiếp cận y tế. |
| Harm xảy ra là gì? | *Đã được đo (có nguồn):* ở cùng điểm rủi ro, bệnh nhân da đen ốm nặng hơn bệnh nhân da trắng và ít được vào chương trình hơn (17,7% so với 46,5% nếu sửa). *Nguy cơ suy luận:* thiếu chăm sóc bổ sung làm sức khỏe xấu đi; bài này không đo kết cục lâm sàng cụ thể. |
| Harm lens | Opportunity loss (mất cơ hội vào chương trình chăm sóc); có nguy cơ injury (sức khỏe xấu đi do thiếu chăm sóc, suy luận của tôi, không đo trong nghiên cứu). |
| Severity | High — ảnh hưởng đến tiếp cận chăm sóc cho người bệnh phức tạp (nhận định của tôi). |
| Scale | Rất lớn: các thuật toán tương tự được dùng cho khoảng 200 triệu người/năm (theo bài tường thuật). |
| Probability | Cao (đánh giá của tôi): thiên lệch có tính hệ thống vì nằm ngay trong cách xác định mục tiêu của mô hình; nghiên cứu đo được chênh lệch 17,7% so với 46,5%. |
| Frequency | Liên tục, mỗi lần chạy chấm điểm; âm thầm vì không có thông báo lỗi. |
| Vì sao? | Căn cứ vào Obermeyer et al. (2019) và bài tường thuật TechXplore. Giới hạn: số liệu chi tiết lấy qua nguồn thứ cấp; dữ liệu từ một bệnh viện; không đo kết cục sức khỏe cuối cùng. Human review cần có: nhóm quản trị dữ liệu kiểm tra công bằng theo nhóm dân cư trước và sau triển khai; bác sĩ có quyền đưa bệnh nhân vào chương trình bất kể điểm số. |

### 4. Case study 3 — Các symptom checker công khai chẩn đoán và phân loại mức khẩn cấp thiếu chính xác

#### Brief Case

- Tổ chức / sản phẩm AI: 23 dịch vụ symptom checker (công cụ kiểm tra triệu chứng) miễn phí, công khai, bằng tiếng Anh; nghiên cứu kiểm toán (audit study) của Semigran, Linder, Gidengil, Mehrotra (Harvard Medical School và các đơn vị liên kết).
- Thời gian, địa điểm / bối cảnh: Hoa Kỳ; nghiên cứu đăng trên *BMJ* năm 2015 (BMJ 2015;351:h3480).
- AI được dùng để làm gì: Người dùng nhập triệu chứng; công cụ đưa ra danh sách chẩn đoán khả dĩ và lời khuyên phân loại (cấp cứu / khám không khẩn cấp / tự chăm sóc).
- Vấn đề hoặc sự kiện đáng chú ý: Nhóm nghiên cứu cho 45 ca bệnh chuẩn hóa (vignette) chia đều ba mức khẩn cấp vào các công cụ và đo độ chính xác chẩn đoán cũng như lời khuyên phân loại.
- Số liệu có nguồn (770 lượt đánh giá chuẩn hóa, 23 công cụ, 45 ca bệnh):
  - Chẩn đoán đúng được xếp **đầu tiên** trong 34% lượt đánh giá; nằm trong **20 chẩn đoán đầu** trong 58%.
  - Lời khuyên phân loại phù hợp trong 57% lượt đánh giá.
  - Theo mức khẩn cấp: phù hợp ở 80% ca cấp cứu, 55% ca không khẩn cấp, 33% ca chỉ cần tự chăm sóc.
- Nguồn: Semigran HL, Linder JA, Gidengil C, Mehrotra A. *Evaluation of symptom checkers for self diagnosis and triage: audit study* — BMJ 2015;351:h3480 — https://www.bmj.com/content/351/bmj.h3480 ; tóm tắt tại AHRQ PSNet: https://psnet.ahrq.gov/issue/evaluation-symptom-checkers-self-diagnosis-and-triage-audit-study
- Phân biệt bằng chứng và nhận định: *Nguồn xác nhận:* các tỷ lệ chính xác trên ca bệnh chuẩn hóa. *Lưu ý nguồn:* trang BMJ chặn truy cập tự động nên các con số chính xác (34%, 58%, 57%, 80/55/33%) tôi lấy từ tóm tắt tìm được qua tìm kiếm web; trang PSNet (mở trực tiếp được) chỉ nêu số xấp xỉ, khớp với các số này. Cần mở bài BMJ để đối chiếu trước khi nộp. *Tôi suy luận:* người dùng thật có thể làm theo lời khuyên sai; nghiên cứu dùng ca bệnh giả định, không đo hậu quả trên bệnh nhân thật, và dữ liệu năm 2015 nên các công cụ hiện nay có thể đã thay đổi.

#### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Người dùng tự nhập triệu chứng ở nhà và dựa vào kết quả để quyết định đi cấp cứu, đi khám hay tự chăm sóc. |
| Stakeholder bị ảnh hưởng | Người dùng/bệnh nhân (đặc biệt người có triệu chứng nặng); hệ thống y tế (quá tải khi người bệnh nhẹ được khuyên đi khám); nhà phát triển công cụ. |
| Failure mode | Harmful advice (lời khuyên phân loại sai, cả thiếu lẫn thừa) và chẩn đoán không chính xác; có thể kéo theo over-reliance (nhận định của tôi, nguồn không đo). |
| Layer bắt đầu lỗi | **Model** (logic suy luận/phân loại triệu chứng; chẩn đoán đúng chỉ đứng đầu 34%). Chưa đủ bằng chứng về layer UX/Grounding; giả thuyết riêng: cách trình bày lời khuyên như kết luận có thể làm người dùng tin quá mức. |
| Harm xảy ra là gì? | *Đã đo (có nguồn):* lời khuyên phân loại phù hợp 57%; ca cấp cứu nhận lời khuyên phù hợp 80% (tức 20% không phù hợp). *Nguy cơ (chưa đo trên người thật):* người có bệnh nặng bị trì hoãn đi viện khi công cụ khuyên chưa cần; người bệnh nhẹ bị khuyên đi khám không cần thiết. |
| Harm lens | Injury (nguy cơ tổn hại thể chất do trì hoãn điều trị); misinformation (thông tin chẩn đoán/lời khuyên sai). |
| Severity | High với ca cấp cứu bị phân loại sai; Low–Medium với ca nhẹ bị khuyên đi khám thừa (đánh giá của tôi). |
| Scale | Chưa đủ dữ liệu để đánh giá số người dùng thật. Căn cứ có được: nghiên cứu đo 770 lượt đánh giá trên ca bệnh chuẩn hóa; các công cụ miễn phí và công khai nên phạm vi tiếp cận tiềm năng rộng (nhận định, không có số liệu). |
| Probability | Đo được: 43% lượt phân loại không phù hợp trong thử nghiệm. Xác suất gây hại thực tế cho người dùng chưa được nghiên cứu này đo, nên chưa đủ dữ liệu. |
| Frequency | Mỗi lần người dùng sử dụng công cụ; sai sót xuất hiện thường xuyên trong thử nghiệm (43% lượt phân loại không phù hợp, 66% lượt không xếp chẩn đoán đúng đầu tiên). |
| Vì sao? | Căn cứ vào Semigran et al. (2015). Giới hạn: ca bệnh giả định, dữ liệu 2015, không đo kết cục bệnh nhân. Human review cần có: cảnh báo rõ "không thay thế bác sĩ", luôn khuyên gặp nhân viên y tế với triệu chứng nặng, nhà phát triển kiểm tra định kỳ bằng ca bệnh chuẩn. |
