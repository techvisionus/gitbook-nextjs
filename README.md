
---

description: >-

Nextjs được phát triển dựa trên Reactjs với sự khác biệt về cách render ứng dụng web. Với Reactjs

là ứng dụng kiểu Client Side Rendering (CSR) - ứng dụng được render trên máy của người dùng. Nhưng

có 1 số hạn chế bởi cách render này đó là việc các search engine như google, bing,...sẽ rất khó

có craw được dữ liệu từ các trang web CSR (do thời gian để tải trang đầu tiên lâu hơn so với việc render

trang web từ server). Chính từ nhu cầu này, Nextjs được xây dựng để thực hiện Server side Rendering với Reactjs.

---

  

# Tại sao nextjs

  

<!-- {% hint style="info" %}

We are teaching an updated and improved FSDL as an official UC Berkeley course Spring 2021.

Sign up to receive updates on our lectures as they're released — and to optionally participate in a synchronous learning community. -->
  

Server side rendering có nghĩa là thực hiện JavaScript ở phía server để render rồi sau đó trả kết quả (html) về phía client.

Ở các ứng dụng Single Page Application (SPA), việc rendering được thực hiện ở phía client, cho nên khi chuyển màn hình hay có biến

đổi gì ở màn hình, những nơi nào cần thiết sẽ được rendering lại mà không cần phải load cả trang như cách làm truyền thống. Nó giúp người

dùng có trải nghiệm tốt hơn. Nhưng có 2 vấn đề:

  

* Thời gian trả về view đầu tiên (first view) lâu hơn so với phương pháp trả html trực tiếp từ Server về thì SPA sẽ tốn nhiều thời gian hơn. Bởi vì sau khi Server trả response về, phía Client phải thực hiện JavaScript mới tạo được html để hiển thị. Mất nhiều thời gian hiển thị First View có thể khiến chúng ta mất nhiều người dùng

* SEO Trong trường hợp không SSR, thì tại thời điểm Server trả response, html chưa được sinh ra, nên không thể chắc chắn rằng clawler của Search Engine sẽ nhận biết được content của trang web. Nghĩa là trang web của sẽ bị yếu thế trong SEO (người dùng khó tìm kiếm thấy trang web).

  

Chính vì vậy bằng việc kết hợp giữa SPA và SSR, chúng ta sẽ lấy được mỗi điểm tốt của mỗi phương pháp. Thực hiện bằng cách ta sẽ cho SSR ở lần đầu tiên, và kể từ sau đó các biến đổi màn hình sẽ được thực hiện như một ứng dụng SPA. Nextjs được xây dựng dựa trên Reactjs (framework xây dựng các trang web SPA) chính là để thực hiện công việc như vậy.

**Isomophic (Universal) JavaScript**
Đây là một cơ chế để dùng chung source code giữa frontend và backend. Ví dụ như khi chúng ta phát triển ứng dụng mà ngôn ngữ dùng ở Frontend và Backend khác nhau, thì việc validation ở Frontend và Backend phải được thực hiện một cách riêng biệt. Điều đó khiến cùng một logic chúng ta phải thực hiện đối với nhiều ngôn ngữ khác nhau, vừa tốn công code, maintain lại càng khó. Với Isomophic JavaScript ta có thể thực hiện điều đó

# Tổng kết

Sử dụng nextjs nhằm mục đích:

 - Dễ dàng thực hiện Server side rendering (để khắc phục nhược điểm của SPA là rất khó cho các search engine như google tìm thấy trang web từ đó khiến người dùng khó có thể tìm thấy trang bằng cách search google). 
 - Thực hiện tư tưởng Isomophic Javascript 
  

## Tài liệu này dành cho ai

  Đây là tài liệu dành cho tất cả mọi người. Những người có nhu cầu tìm hiểu về Nextjs

  

We will not review the fundamentals of deep learning \(gradient descent, backpropagation, convolutional neural networks, recurrent neural networks, etc\), so you should review those materials first if you are rusty.

  

## Course Content

  

{% page-ref page="course-content/setting-up-machine-learning-projects/" %}

  

{% page-ref page="course-content/infrastructure-and-tooling/" %}

  

{% page-ref page="course-content/data-management/" %}

  

{% page-ref page="course-content/ml-teams/" %}

  

{% page-ref page="course-content/training-and-debugging/" %}

  

{% page-ref page="course-content/testing-and-deployment/" %}

  

{% page-ref page="course-content/research-areas.md" %}

  

## Guest Lectures

  

{% page-ref page="guest-lectures/xavier-amatriain.md" %}

  

{% page-ref page="guest-lectures/chip-huyen-nvidia.md" %}

  

{% page-ref page="guest-lectures/lukas-biewald-weights-and-biases.md" %}

  

{% page-ref page="guest-lectures/jeremy-howard-fast.ai.md" %}

  

{% page-ref page="guest-lectures/richard-socher-salesforce.md" %}

  

{% page-ref page="guest-lectures/raquel-urtasun-uber-atg.md" %}

  

{% page-ref page="guest-lectures/yangqing-jia-alibaba.md" %}

  

{% page-ref page="guest-lectures/andrej-karpathy-tesla.md" %}

  

{% page-ref page="guest-lectures/jai-ranganathan-keeptruckin.md" %}

  

{% page-ref page="guest-lectures/franziska-bell-toyota-research.md" %}