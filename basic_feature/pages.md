    Tài liệu này được sử dụng cho nextjs phiên bản 9.3 trở lên. Nếu bạn đang sử dụng phiên bản cũ 
    hơn hãy tham khảo tại [trang chủ](https://nextjs.org/docs/tag/v9.2.2/basic-features/pages).
Trong Nextjs một trang (page) là một React component được xuất ra từ các file `.js`, `.jsx`, `.ts` hoặc `.tsx` trong thư mục `pages`. Mỗi trang được liên kết tới đường dẫn dựa trên tên file.

**Ví dụ** Nếu bạn tạo file `pages/about.js` xuất ra một React component như dưới đây, từ đó có thể truy cập được tới `/about`.
```jsx
function About() {
  return <div>About</div>
}

export default About
```

**Pages với các đường dẫn động**

Nextjs hỗ trợ các page với đường dẫn động. Ví dụ: ta tạo file như sau: `pages/post/[id].js`, thì sẽ có thể truy cập được tới các đường dẫn `posts/1`, `posts/2`, ..`

    Tìm hiểu thêm về đường dẫn động (dynamic routing) tại 

[Tài liệu](https://nextjs.org/docs/routing/dynamic-routes)

**Tiền render (Pre-rendering)**
Mặc định, Nextjs thực hiện pre-render mỗi page. Có nghĩa là Nextjs sẽ sinh HTML cho mỗi page trước, thay vì làm tất cả bởi javaScript tại phía máy khách (client side). Tiền render (pre rendering có thể giúp nâng cao hiệu suất và khả năng SEO).
Mỗi phần HTML sinh ra sẽ được leien kết tới những phần code JavaScript tối thiểu cần thiết cho mỗi page. Khi 1 page được tải lên browser, JavaScript sẽ chạy và hoàn thiện page (Quá trình này được gọi là hydration - render lai giữa server side và client side)

**Hai phần của tiền render**
Nextjs có 2 phần của tiền render (pre-rendering): Sinh tĩnh (Static Generation) và sinh phía server (Server side Rendering). Điều khác biệt chính là khi nó sinh mã HTML của một trang (page).

 - Sinh tĩnh (Static Generation) - được khuyến nghị. HTML được sinh trong quá trình tạo và được tái sử dụng trên mỗi request.
 - Sinh phía server (Server-side Rendering): Mã HTML được sinh sau mỗi request.
 
Quan trọng là Nextjs cho ta được quyền chọn cách render nào mà ta muốn ở mỗi page. Ta có thể tạo một ứng dụng Nextjs lai (Hybrid) bằng cách sử dụng sinh tĩnh (Static Generation) cho hầu hết các pages và sử dụng Server-side Rendering cho các phần còn lại.

Tôi khuyến nghị sử dụng Static Generation hơn là Server-side Rendering vì lý do hiệu năng. Sinh tĩnh (Static generated) một pages có thể được cached bởi CDN mà không cần thêm cấu hình để đẩy hiệu năng lên. Tuy nhiên, trong cài truowgnf hợp Server-side Rendering là lựa chọn duy nhất.
Bạn cũng có thể sử dụng Client-side Rendering cùng với Static Generation hoặc Server-side Rendering. Điều đó có nghĩa 1 vài thành phần của trang (page) có thể hoàn toàn được render bởi JavaScript phía client. Đọc thêm tại [Data Fetching](https://nextjs.org/docs/basic-features/data-fetching#fetching-data-on-the-client-side) 