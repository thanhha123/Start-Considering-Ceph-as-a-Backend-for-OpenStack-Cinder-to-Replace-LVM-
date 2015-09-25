# Start-Considering-Ceph-as-a-Backend-for-OpenStack-Cinder-to-Replace-LVM

Trở lại buổi summit Juno. Tôi đã tham dự hầu hết các phiên họp, thảo luận về lưu trữ và đã vô cùng sốc về việc các nhà cung cấp lưu trữ tránh không dùng Ceph. Tuy nhiên LVM, các backend lưu trữ cho Cinder đã luôn được nhắc đến. Có lẽ, nó là một dấu hiệu cho thấy Ceph được tham gia hơn? Nói về LVM, các cuộc khảo sát OpenStack ngoái cho thấy nó là backend được sử dụng nhiều hơn.

##Sự hình thành

Trước hết hãy xem làm thế nào lưu trữ các hệ sinh thái OpenStack Cinder. Một mặt, bạn có mã nguồn mở trình điều khiển LVM mặc định được coi là rẻ và là một cách khá phổ thông để bắt đầu cung cấp block storage cho máy ảo của bạn (đó là sự thật).  Mặt khác, bạn có có bản quyền như EMC, NetApp và Solidfire được coi là giải pháp thực sự cho việc triển khai block storage Cinder khi họ cung cấp tính sẵn sàng và hiệu suất cao. Oh! Đợi một giây, tôi cảm thấy một cái gì đó kỳ lạ ở đây ... OH vâng, chúng tôi hoàn toàn chuyển sang phạm vi giải pháp đáng tin cậy, khả năng mở rộng và mạnh mẽ khác như Ceph và Sheepdog. Cả hai đều có một kiến trúc phân tán sẵn sàng cao. Vì vậy, thay thế dần bằng mã nguồn mở cho block storage trong OpenStack. Nếu bạn đọc blog này bạn chắc chắn hiểu tôi yêu ceph và nó mạnh đến cỡ nào. Bài viết này không có ý định để đưa dầu vào lửa và bắt đầu một cuộc tranh luận lớn. Tôi sẽ chỉ cố gắng để được thực tế, trung thực và công bằng.

##Tại sao phải thay thế LVM bằng Ceph

Để cung cấp cho bạn một số nền tảng. Cinder là một thành phần OpenStack chịu trách nhiệm cung cấp block storage cho máy ảo. Cinder có 27 storage lưu trữ. Trong đó chỉ có 4 là mã nguồn mở, đó là các giải pháp:

  - Ceph RBD
  
  - GlusterFS
  
  - NFS
  
  - LVM
  
Bắt đầu cho việc Ceph làm backend cho OpenStack. Bạn có thể bắt đầu với Ceph với số lượng mount máy ảo nhiều hơn so với backend LVM hay NFS.

Để có thể hiểu được Cinder, LVM là dễ dàng sử dụng và tôi tin rằng nó là một bước đệm tốt mà bạn phải đi qua ít nhất một lần trong kinh nghiệm của OpenStack của bạn. Tuy nhiên điều này sẽ không kéo dài, như yêu cầu hiện nay, bạn chắc chắn sẽ xem xét những giải pháp có khả năng mở rộng hơn, nhằm cung cấp một số chức năng sẵn sàng cao. Hai tính chất này dường như thiếu trong LVM. Cinder đã cung cấp nhiều tính năng và hỗ trợ multi-backend. Việc đáp ứng tính sẵn sàng cao thật sự khó khăn. Nếu không may máy chủ LVM bị ngừng hoạt động, cả hệ thống Cinder cũng sẽ ngừng hoạt động theo

Đây là lý do tại sao bạn đang tìm kiếm một giải pháp cho độ tin cậy, tính sẵn có và mạnh mẽ. 3 yếu tố là những gì mà Ceph đáp ứng được.

Ceph là một phân phối, giải pháp lưu trữ mã nguồn mở mở rộng, thống nhất!

Việc học Ceph là khá khó khăn nhưng tôi tin bạn sẽ thu được nhiều ích lợi từ việc học nó. Để bắt đầu với Ceph bạn cần ít nhất là 3 máy không. Vâng câu trả lời là có và không. Có nó được đề nghị để bắt đầu với 3 máy đặc biệt là nếu bạn muốn kiểm tra sự sao chép và cơ chế phục hồi. Tuy nhiên những 3 loại máy này không phải được dành riêng, vì vậy bạn có thể dễ dàng dùng chung  compute node với các đơn vị lưu trữ của bạn. Thực tế nếu bạn muốn bắt đầu một cách nghiêm túc với OpenStack, bạn sẽ cần ít nhất 3 máy. Bạn có thể dễ dàng đặt vào một chổ tất cả mọi thứ , nhờ đó bạn sẽ có thể kiểm tra các tính năng như tính sẵn sàng cao, live migration,v.v…

Về cơ bản 3 máy của bạn sẽ chạy:

HAProxy | Keepalived

OpenStack API

MySQL Galera | RabbitMQ | MonggoDB

Nova-compute

Ceph

Thiết lập này có thể được xem xét như một sandbox, nơi bạn có thể thử nghiệm những điều khác nhau từ OpenStack.

Nếu bạn là một nhà phát triển và điều duy nhất bạn tìm kiếm là một môi trường dev, sẽ sau đó bạn có thể quan tâm vào Devstack Ceph. Với sự giúp đỡ của nó, bạn sẽ có thể có hiệu quả và nhanh chóng tải khởi động một môi trường dev và hãy khai thác OpenStack với Ceph. Tôi mong đợi để xem bản vá lỗi này đi vào Devstack khá sớm (tôi cũng đã nói điều này trong nhiều tháng nay ...).

Đừng có nghĩ là tôi sai, mục đích không phải là từ chối LVM hay bất cứ thứ gì tương tự. Mà chỉ là tôi tin rằng Ceph là con đường để đi khi nói về lưu trữ trong OpenStack. Một nghiên cứu cho thấy Ceph đứng ở vị trí thứ 2 chỉ sau LVM ( Ceph là 58 và LVM là 63). Những gì tôi muốn chứng tỏ trong bài viết này là chúng ta có thể tiến xa hơn. 

##Nguồn bài viết 

Source: http://www.sebastien-han.fr/blog/2014/06/02/start-considering-ceph-as-a-backend-for-openstack-cinder-replace-lvm/
