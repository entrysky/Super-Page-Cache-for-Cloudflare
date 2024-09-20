# Super-Page-Cache-for-Cloudflare
Super Page Cache for Cloudflare — Guide for using Remove Cache Buster Query Parameter feature (when using Cache Everything page rule)
Khi nói về plugin cache trên WordPress, mọi người thường nghĩ ngay đến việc tăng tốc độ tải trang và cải thiện khả năng chịu tải của server. ít ai hiểu cache, đơn giản là chuyển các file động (như PHP, MySQL) thành các file tĩnh (như HTML).

Nếu chỉ dừng lại ở đây, mọi chuyện sẽ quá đơn giản và các plugin cache sẽ rất giống nhau. Do đó, các nhà phát triển đã bổ sung thêm hàng loạt tính năng khác như CDN, xử lý ảnh, defer JavaScript, xóa CSS không sử dụng, Lazy Loading, lưu trữ Google Fonts cục bộ, và nhiều tính năng khác để “chào hàng”.

Các plugin cache hiện nay thường là giải pháp “tất cả trong một” để tối ưu hóa WordPress, hơn là chỉ đơn thuần thực hiện chức năng cache.

Phân loại các loại cache:
Cache cấp L0: Các plugin cache viết trên PHP, lưu trữ file tĩnh trên ổ cứng. Những người phát triển loại cache này thường khoe khoang rằng cache của họ có khả năng tùy biến tốt hơn và có nhiều tính năng mạnh mẽ hơn.
Cache cấp L1: Bao gồm các công nghệ như FastCGI, Varnish, Redis, LiteSpeed Cache …. thường được đánh giá cao hơn vì khả năng chịu tải tốt hơn. Tuy nhiên, việc cấu hình phức tạp và dễ gây nhầm lẫn khi xóa cache là nhược điểm của chúng.
Sức mạnh của Cache cấp L2 (Edge Caching CDN):
Cache cấp L2, hay Edge Caching CDN, là bước tiến mới trong công nghệ cache. Thay vì lưu trữ file cache trên hosting gốc, nó được lưu trữ trên nhiều cụm server phân bố toàn cầu của các nhà cung cấp CDN. Điều này giúp cải thiện tốc độ truy cập cho người dùng từ khắp nơi trên thế giới, tăng khả năng chịu tải của website gần như vô hạn.

Khi người dùng truy cập vào trang web, họ sẽ được chuyển thẳng tới các server CDN này thay vì phải tải nội dung từ server gốc. Nhờ thế, ngay cả khi server gốc bị tắt (hoặc gặp lỗi), trang web vẫn có thể truy cập bình thường.

Cách cấu hình Cache Rules trên Cloudflare:
Trước đây, việc cấu hình cache trên Cloudflare khá phức tạp. Tuy nhiên, từ năm 2022, Cloudflare đã giới thiệu tính năng Cache Rules, giúp việc cấu hình trở nên đơn giản hơn.

Dùng 1 thời gian, khi đã quen với cách vận hành của cache rules, sẽ thấy việc cấu hình cache trên Cloudflare có khi đơn giản hơn cả chạy qua các plugin ấy

1. Xử lý cache qua Cloudflare Cache Rules

Mặc định sẽ cache tất cả trang, bypass các rule theo URI Path, URI Query String, Cookie, Extensions
2. Xóa cache qua plugin Cloudflare chính thức

Cũng có thể dùng các plugin khác có tính năng xóa cache Cloudflare như Super Page Cache for Cloudflare
3. Tùy chỉnh cho từng trang trên Cache Rules

Ở bài viết trước đây về Super Page Cache for Cloudflare, mình cũng từng viết qua, nhưng cách đây ít hôm vào lại hướng dẫn gốc của tác giả isaumya tại Github, thấy anh ấy đã cập nhập lại rule, siêu ngắn gọn, dễ hiểu và rất dễ quản lý (bản cũ anh ấy dùng 4 Cache Rules + 1 Page Rules, bản này chỉ dùng duy nhất 1 Cache Rules)

Nếu đã làm theo ở bài viết cũ, thì vào Cloudflare, tắt hết các Cache Rules, Page Rules cũ đi

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Tắt luôn Cache Level: Cache Everything, vì không dùng nữa

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Sau đó xóa hết cache Cloudflare đi

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Cấu hình Cache Rules
Truy cập vào Cache Rules -> Create new Cache Rule -> Edit Expression
Rule name (required): đặt 1 tên bất kì tùy thích 😛 SPCFC Cache Rule ➜ Cache Eligible Requests chẳng hạn

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Bên trong Use expression builder, điền vào đoạn code bên dưới (hay thế example.com bằng domain của bạn)

(http.host eq "example.com" and not starts_with(http.request.uri.path, "/wp-admin") and not starts_with(http.request.uri.path, "/wp-login") and not starts_with(http.request.uri.path, "/wp-json/") and not starts_with(http.request.uri.path, "/wc-api/") and not starts_with(http.request.uri.path, "/edd-api/") and not starts_with(http.request.uri.path, "/mepr/") and not http.request.uri.path contains "/register/" and not http.request.uri.path contains "/dashboard/" and not http.request.uri.path contains "/members-area/" and not http.request.uri.path contains "/wishlist-member/" and not http.request.uri.path contains "phs_downloads-mbr" and not http.request.uri.path contains "/checkout/" and not http.request.uri.path contains ".xsl" and not http.request.uri.path contains ".xml" and not http.request.uri.path contains ".php" and not starts_with(http.request.uri.query, "s=") and not starts_with(http.request.uri.query, "p=") and not http.request.uri.query contains "nocache" and not http.request.uri.query contains "nowprocket" and not http.cookie contains "wordpress_logged_in_" and http.cookie ne "comment_" and not http.cookie contains "woocommerce_" and not http.cookie contains "wordpressuser_" and not http.cookie contains "wordpresspass_" and not http.cookie contains "wordpress_sec_" and not http.cookie contains "yith_wcwl_products" and not http.cookie contains "edd_items_in_cart" and not http.cookie contains "it_exchange_session_" and not http.cookie contains "comment_author" and not http.cookie contains "dshack_level" and not http.cookie contains "auth_" and not http.cookie contains "noaffiliate_" and not http.cookie contains "mp_session" and not http.cookie contains "xf_" and not http.cookie contains "mp_globalcart_" and not http.cookie contains "wp-resetpass-" and not http.cookie contains "upsell_customer" and not http.cookie contains "wlmapi" and not http.cookie contains "wishlist_reg")
Cache status (required) -> Eligible for cache
Cache key (optional) ấn vào + Add
Bật Cache deception armor
Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Xong xuôi thì ấn vào Deploy

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Nếu bạn cài đặt server qua 1 số tool, script như Webinoly, đôi khi tác giả tự tạo thêm 1 số header như “no-cache”, sẽ khiến Cache Rules chạy lỗi, có thể dùng tùy chọn Edge TTL -> Ignore cache-control header and use this TTL để bỏ qua luôn các header này
Cấu hình isaumya chia sẽ rất an toàn, chạy ổn định trên WordPress blog và Woocommerce mặc định

Xóa cache qua plugin Cloudflare chính thức
Download và setup như 1 plugin thông thường

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Settings -> Speed -> Bật Auto Purge Content On Update

Có 1 vĩ nhân đã từng nói, có 2 thứ trên đời này, chỉ có chúa mới hiểu 😀 1 là tại sao code viết thế lại chạy không lỗi 😛 2 là xử lý …. xóa cache 😀 Nói để thấy chuyện xóa cache nó cực kì rối rắm và phức tạp, xóa hết thì mất ý nghĩa của việc tạo cache, xóa không đủ thì rất dễ gây lỗi

Mình đã dùng plugin Cloudflare chính thức trong thời gian khá dài, cấu hình đơn giản, xóa cache ổn, ít lỗi 😛
Bạn cũng có thể dùng Super Page Cache for Cloudflare để xóa cacle Cloudflare, tính năng xóa mạnh hơn, do có thêm tính năng chỉ xóa các trang, không xóa cache ở các hình ảnh đã được cache nên hiệu quả mạnh hơn, còn có thể kết hợp được với rất nhiều hình thức cache khác, chưa kể nó còn có 1 button nhỏ trên admin bar, giúp việc xóa cache thuận tiện hơn

Cá nhân mình không khuyến khích dùng Super Page Cache for Cloudflare để xóa cache, vì cấu hình ban đầu hơi nhiều, plugin Cloudflare chính thức đơn giản hơn, chỉ cần bật duy nhất 1 tùy chọn
Trong trường hợp bạn dùng VPS riêng, thích nghịch và không ngại nghịch có thể thử 3 plugin viết riêng cho vấn đề này

Cloudflare Auto Cache Purge: tự động xóa cache Cloudflare khi bài viết, trang, danh mục, hoặc thẻ được thêm, sửa, hoặc xóa. (không hỗ trợ xóa cache khi có comment mới)
Cloudflare Cache Clear: thêm một menu vào admin bar để xóa nhanh cache Cloudflare
Cloudflare Preload Cache: tự động preload cache bài viết, trang khi tạo, cập nhật hoặc đưa vào thùng rác, bổ xung thêm nút “Preload Cache” bên phải admin bar nếu cần preload cache thủ công. (không hỗ trợ preload cache khi có comment mới)
Tập trung vào tính năng xóa và preload Cloudflare cache, hiệu quả theo mình là hơn plugin chính thức từ Cloudflare vì có thể bổ xung các link cụ thể muốn xóa, có thêm preload và có sẵn button để xóa hoặc preload nhanh thủ công, dùng sẽ tiện hơn, khi preload cũng rất nhẹ, vì chạy thông qua Cron và PHP CLI

Tùy chỉnh cho từng trang trên Cache Rules
Thông thường chúng ta cài đặt thêm rất nhiều plugin để quản lý trên WordPress, nên tùy trang mà cần thêm các rule vào để bypass cache, không thì nó sẽ cache loạn lên

Ở phiên bản này, phần Cache Rules hiện ra theo giao diện WEB, nên rất trực quan, ta có thể thêm các rule dựa vào cấu hình sẵn có của tác giả

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Đầu tiên là URI Path
Nó sẽ bắt đầu bằng URI Path, tiếp theo là does not contain

Phần điền vào thì bạn có thể chặn link nào tùy thích

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Cụ thể mình có dùng 1 plugin, nó quản lý các sub qua đường dẫn https://bibica.net/comment-subscriptions/

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Mình sẽ điền /comment-subscriptions/ vào Cache Rules qua URI Path

Tiếp theo là Extensions
Nó sẽ bắt đầu bằng URI Path, tiếp theo là does not contain cuối cùng là định dạng file muốn bypass cache

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Mặc định theo rule thì các file có đuôi là .xsl .xml .php sẽ không cache, bạn muốn add thêm file có đuôi nào không cache thì dùng cấu trúc tương tự như thế

URI Query String
Nó sẽ bắt đầu bằng URI Query String, tiếp theo là does not start with

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Phần chặn sẽ là phần phía sau ? ở link

https://bibica.net/?s=cdn, là tìm kiếm từ cdn trên trang, mặc định giá trị tìm kiếm (s=) sẽ không cache

Trên trang bibica.net, mình set phần comment, mặc định người lần đầu tiên comment trên trang, sẽ phải duyệt tay, từ lần thứ 2 trở đi thì sẽ tự được đồng ý, và do mình set comment có thể được sửa trong 5 phút, trong trường hợp type sai chính tả còn ấn sửa 😀

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Query String trên link có dạng là ?unapproved=, mình sẽ add thêm 1 rule, điền vào trong rule phần unapproved=, đúng theo công thức của tác giả

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Cuối cùng là Cookie
Giá trị Cookie thời điểm Cloudflare vừa ra mắt, chỉ có trên các gói Enterprise, giờ tài khoản FREE cũng có thể sử dụng thông qua Cache Rules

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Nó sẽ bắt đầu bằng Cookie, tiếp theo là does not contain, cuối cùng là tên các cookie

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Để xem cookie, bạn có thể comment, login vào tài khoản, add vào giỏ hàng sản phẩm, hoặc chạy bất cứ trang nào để kiểm tra

Cụ thể mình sẽ ấn F12 trên trình duyệt, đi qua tab Cookies, sau đó thử comment 1 phát, xem các cookie hiện ra như thế nào

Trên trang bibica.net, sau khi comment, nó sẽ sinh ra 4 cookie comment_author, comment_author_email, comment_author_url, và SimpleCommentEditing

3 cookie đầu do cùng bắt đầu là comment_author, nên chỉ cần chặn 1 rule về comment_author là sẽ có tác dụng cho 3 cookie còn lại, mình sẽ bổ xung thêm vào SimpleCommentEditing luôn cho chắc kèo 😀

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

4 tùy chỉnh hiện tại tại bài này (URI Path, URI Query String, Cookie, Extensions) nó gần như đủ cho mọi thứ bạn cần để bypass, lý thuyết chỉ với Cache Rules, bạn có thể xử lý triệt để hoàn toàn vấn đề cache khi dùng Cloudflare

Trong trường hợp bạn dùng plugin nào đó lạ lạ, cứ google hoặc hỏi tác giả xem dùng plugin cache thì cần bypass cái gì, sau đó điền vào Cloudflare Cache Rules là được 😛

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Về tùy chọn Cache by device type thì mình hiểu là Cloudflare sẽ tách ra, cache riêng cho desktop, phone …. các bản cache khác nhau, trang nhiều đối tượng khách dùng thiết bị di động xem bài hay mua hàng thì có thể bật lên

Ignore query string và Enable query string sort nên tắt cho 99% các trang, vì phần lớn chúng ta quản lý chằng chịt các link qua query string, cụ thể như thèng bibica.net đang dùng, ?w=300 và ?w=500 là 2 tấm ảnh khác nhau, tắt đi thì tất cả sẽ trở thành ảnh gốc, không còn tạo thumbnail nữa

Enable query string sort thì đôi khi nó làm loạn query string, khiến khách A và B nhầm thành 1 người, nói chung là nên tắt

An toàn nhất thì bạn chỉ cần cài đặt như mặc định tác giả khuyến cáo, sau đó bổ xung các rule riêng của trang mình đang chạy là đủ hiệu quả, mình dùng thì thấy các vấn đề như admin login …. ổn định, không gặp vấn đề gì

Để kiểm tra xem mọi việc chạy có đúng chưa thì có thể nhìn response header cf-cache-status

Trong trang quản trị, hoặc các trang bypass, không cache sẽ luôn có giá giá trị DYNAMIC

Phía user thì sẽ thể hiện 3 trang thái

EXPIRED: trạng thái sau khi chúng ta xóa cache

MISS: trang chưa được tạo cache (thường xuất hiện sau khi xuất hiện EXPIRED)

HIT: trang đã được tạo cache (thường xuất hiện ngay sau khi xuất hiện MISS)

Thông thường mình cũng ít nhìn cái này, đa phần thêm rule, chặn 1 trang hay hành động nào đó, mới cần kiểm tra xem mọi thứ chạy đúng không 😀

Khuyết điểm?
Cloudflare quản lý thời gian lưu trữ cache hơi phức tạp và họ không công bố cụ thể thời gian lưu trữ cache như thế nào, với các trang có lượt truy cập quá thấp kiểu thèng bibica.net, thường cache bị xóa rất nhanh, đa phần < 24h, với các trang có lượt view khủng, thời gian lưu trữ cache sẽ lâu hơn

Vấn đề này không quan trọng lắm, trang ít truy cập quá có thể tự preload cache định kì hàng giờ hoặc hàng ngày để tạo mới lại cache nếu bị xóa đi là ổn

Tổng kết
Không có gì phàn nàn về tốc độ và hiệu năng cache vì miễn bàn 😀 quan trọng nhất Cloudflare còn miễn phí 😀 dịch vụ Edge Caching CDN nào nói nhanh hơn Cloudflare thì cứ thử vào speedvitals.com chạy 2 lần bài test, xem có tốt hơn con số bên dưới không rồi nói tiếp :]]

Cache Rules Cloudflare – Cache siêu cấp vô địch cho WordPress

Người dùng cuối, không cần quá nhiều kiến thức, hoàn toàn có thể tự làm chủ hệ thống cache trên WordPress chỉ với Cache Rules, 1 ưu điểm nữa, nó có thể chạy trên tất cả webserver và tất cả nền tảng, vô cùng đa năng

Về cache mình nghĩ là Cache Rules làm tốt, về clear cache, tạm mình cũng chưa quá ấn tượng với plugin nào, plugin Cloudflare chính chủ thì được cái nhẹ, dễ dùng, ít lỗi, Super Page Cache for Cloudflare thì xóa cache và tạo preload cache tốt hơn, nhưng hơi nặng xíu, kết quả là mình phải viết ra 3 cái plugin nhỏ để tự xử lý vấn đề này

Các trang dạng bán hàng, thường xuyên thêm sửa xóa số lượng sản phẩm, các dữ liệu liên thông các trang với nhau, comment liên tục để trả lời khách hàng thì xử lý hơi nhiều, có điều với 4 tùy chọn bypass là có thể xử lý được
