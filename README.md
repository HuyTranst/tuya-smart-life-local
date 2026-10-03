# Tuya Smart Life Local
Tích hợp Home Assistant tùy chỉnh để đăng nhập bằng tài khoản email/điện thoại Smart Life hoặc Tuya Smart thông thường,
lấy thông tin nhà/thiết bị từ API di động và
điều khiển trực tiếp các thiết bị được hỗ trợ trên mạng LAN cục bộ bằng khóa cục bộ của chúng.
Tích hợp này không yêu cầu dự án Tuya IoT Cloud. Bạn không cần
nhập `app_id`, `app_secret`, dấu vân tay chứng chỉ hoặc khóa ký gốc.
## Tính năng
- Đăng nhập bằng địa chỉ email hoặc số điện thoại Smart Life/Tuya Smart và
mật khẩu.
- Chọn một hoặc nhiều nhà để đồng bộ hóa. Bạn có thể để trống lựa chọn để tránh
tải thiết bị vào lúc này.
- Lấy thông tin thiết bị, khóa cục bộ, cấu trúc liên kết hub/con, MAC/UUID và dữ liệu DPS ban đầu
từ API di động của Tuya.
- Điều khiển thiết bị cục bộ thông qua TinyTuya trên mạng LAN, mà không cần sử dụng đám mây
các cuộc gọi OpenAPI cho các thao tác bật/tắt.
- Duy trì kết nối TCP cục bộ liên tục với các thiết bị/hub để cập nhật DPS theo thời gian thực.
Luồng dữ liệu tự động bắt đầu khi quá trình phát sóng/quét UDP phát hiện ra địa chỉ IP mạng LAN.
Sau đó, làm mới/đồng bộ hóa một lần và lắng nghe các bản cập nhật đẩy. Các lệnh cũng
ưu tiên cùng một socket luồng để tránh việc thiết bị từ chối kết nối mạng LAN thứ hai; quá trình tích hợp không định kỳ thăm dò trạng thái cục bộ.
- Lắng nghe các bản phát sóng UDP của Tuya và chạy quét mạng LAN để giữ cho dữ liệu phiên bản IP/giao thức luôn được cập nhật khi thiết bị hoặc hub thay đổi chi tiết mạng.
- Bỏ qua các địa chỉ IP công cộng/WAN được trả về bởi API di động và chỉ sử dụng các địa chỉ IP mạng LAN riêng tư cho các lệnh cục bộ.
- Tạo các thực thể chuyển mạch cho các giá trị DPS của nút/công tắc từ `dataPointInfo.dps`.
Nếu `dataPointInfo.dpName` chứa nhãn, các nhãn đó sẽ được sử dụng; nếu không,
các thực thể sẽ sử dụng tên như `Button <dp_id>`.
- Tạo các cảm biến nhị phân cho cảm biến tiếp xúc/cửa Tuya (`mcs`), cảm biến PIR/chuyển động
và cảm biến hiện diện/chiếm dụng (`hps`) bằng cách sử dụng các bản cập nhật DPS cục bộ theo thời gian thực
từ thiết bị hoặc hub chính. - Tạo một cảm biến văn bản cho các nút ngữ cảnh/cảnh Tuya (`wxkg`). Cảm biến
trạng thái là hành động cuối cùng ở dạng `<nút>_<hành động>`, ví dụ: `1_nhấn`,
`1_nhấn đúp`, `1_giữ` hoặc `2_nhấn đúp`.
- Tạo các thực thể quạt cho các thiết bị quạt được nhận dạng, ví dụ: các thiết bị có
giá trị DPS công suất và tốc độ riêng biệt, và cho các điều khiển từ xa quạt hồng ngoại được hỗ trợ.
- Tạo một cảm biến nhị phân `Trực tuyến` chẩn đoán cho các hub để các hub vẫn xuất hiện trong
Home Assistant ngay cả khi chúng không hiển thị các nút điều khiển trực tiếp.
- Tạo các thực thể nút cho điều khiển từ xa hồng ngoại khi API di động Tuya trả về các tải trọng DPS hành động điều khiển từ xa ảo có thể sử dụng được,
bao gồm các phím dự phòng cho điều khiển từ xa DIY và đa phương tiện.
- Phát hiện điều khiển từ xa hồng ngoại cho điều hòa/khí hậu, quạt, đèn, TV/đầu thu kỹ thuật số, âm thanh, máy chiếu và DVD từ API hồng ngoại của Tuya. Các lệnh về khí hậu, quạt, đèn và trình phát đa phương tiện được gửi cục bộ thông qua hub hồng ngoại; Trạng thái được hiển thị lạc quan vì các thiết bị IR
không báo cáo lại trạng thái thực của chúng.
- Dọn dẹp các thực thể/thiết bị lỗi thời khi bạn thay đổi danh sách nhà đã chọn.
## Yêu cầu
- Home Assistant đã cài đặt HACS.
- Home Assistant phải nằm trên cùng mạng LAN/miền phát sóng với các thiết bị Tuya
hoặc hub mà bạn muốn điều khiển cục bộ. Việc tích hợp cần các gói phát sóng UDP của Tuya
để phát hiện thông tin IP/giao thức mạng LAN và mở các luồng TCP thời gian thực.
- Một tài khoản Smart Life/Tuya Smart sở hữu các thiết bị.

- Các thiết bị phải có khóa cục bộ trong API di động và phải hỗ trợ giao thức cục bộ của Tuya
- Đối với điều khiển từ xa IR, hub IR thực phải nằm trên cùng mạng LAN với Home Assistant.
Các điều khiển từ xa ảo như điều khiển TV, điều hòa hoặc quạt IR là các thiết bị phía ứng dụng nằm sau hub,
và các lệnh cuối cùng được gửi qua hub.
Lưu ý quan trọng về mạng: nếu nhà Smart Life nằm trên mạng LAN/mạng con/VLAN khác,
Home Assistant chưa thể tự động kết nối với các thiết bị đó cục bộ.

Tính năng phát hiện xuyên mạng không được hỗ trợ vì cơ chế phát sóng/phát hiện UDP của Tuya
không hoạt động xuyên qua các bộ định tuyến theo mặc định. Việc có thể ping hoặc định tuyến TCP đến một thiết bị
là không đủ cho cơ chế phát hiện tự động hiện tại. Một số
giải pháp thay thế hoặc tích hợp khác có thể cho phép bạn tạm thời trỏ đến một địa chỉ IP thủ công, nhưng
điều đó không được khuyến nghị: khi thiết bị thay đổi địa chỉ IP hoặc phiên bản giao thức,
Home Assistant sẽ không nhận được bản tin phát sóng UDP cần thiết để cập nhật và

việc điều khiển/thời gian thực cục bộ có thể bị gián đoạn. Cấu hình ổn định là chỉ chọn những ngôi nhà
có thiết bị/hub nằm trên cùng miền phát sóng với Home Assistant.
## Cài đặt HACS
1. Mở HACS trong Home Assistant.
2. Vào **Tích hợp**.
3. Mở menu **...** ở góc trên bên phải và chọn
**Kho lưu trữ tùy chỉnh**.
4. Nhập URL kho lưu trữ này:
https://github.com/home-assistant-tools/tuya-smart-life
5. Chọn **Tích hợp** làm danh mục/loại.
6. Nhấp vào **Thêm**.
7. Tìm **Tuya Smart Life Local** trong HACS và nhấp vào **Tải xuống**.
8. Khởi động lại Home Assistant.
## Thiết lập tích hợp
1. Vào **Cài đặt -> Thiết bị & dịch vụ**.
2. Nhấp vào **Thêm tích hợp**.
3. Tìm kiếm **Tuya Smart Life Local**.
4. Nhập địa chỉ email hoặc số điện thoại Smart Life/Tuya Smart và mật khẩu của bạn.
Giữ **Vùng API** ở chế độ **Tự động** trừ khi đăng nhập
Gửi ý kiến phản hồi
