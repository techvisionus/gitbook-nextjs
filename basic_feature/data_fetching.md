# Lấy dữ liệu (Data fetching)
Trong phần tài liệu về trang(page), chúng ta đã giải thích việc Next.js có 2 hình thức của pre-rendering: Static Generation và Server-side Rendering. Trong phần này, chúng ta sẽ cùng nói sâu hơn về chiến lược lấy dữ liệu (Data fetching) với mỗi trường hợp. Tôi khuyên bạn hãy tới phần [tài liệu page](https://nextjs.org/docs/basic-features/pages) trước nếu b chưa đọc nó.

Chúng ta sẽ nói về 3 hàm riêng biệt của Next.js có thể dùng để lấy dữ liệu cho pre-rendering:
	

 - `getStaticProps` (Static Generation): lấy dữ liệu trong build time.
 - `getStaticPaths` (Static Generation): Đường dẫn động đặc biệt cho trang pre-render dựa trên dữ liệu.
 - `getServerSideProps` (Server-side Rendering): Lấy dữ liệu trên mỗi request.

Thêm vào đó, ta sẽ tóm tắt về việc lấy dữ liệu ở phía client.

## [`getStaticProps`  (Static Generation)](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation)

Nếu bạn export một hàm `async` gọi `getStaticProps` từ một page, Next.js sẽ pre-render page này lúc build time sử dụng props trả về `getStaticProps`.
```jsx
export async function getStaticProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  }
}
```
Tham số `context` là một object chứa đựng các key như sau đây:

- `params` chứa tham số đường dẫn (route) cho pages sử dụng dynamic routes, Ví dụ, nếu một page có tên: `[id].js`, sau đó `params` sẽ giống như `{id: ... }`. Để biết thêm, hãy ghé qua [Dynamic Routing documentation](https://nextjs.org/docs/routing/dynamic-routes). Bạn nên dùng cùng với `getStaticPaths`, chúng ta sẽ giải thích sau.
- `preview` là `true` nếu trang (page) trong chế độ preview và nói cách khác `undefined`. Xem tại [Preview Mode documentation](https://nextjs.org/docs/advanced-features/preview-mode).
- `previewData` chứa dữ liệu preview được set bởi `setPreviewData`. Xem tại [Preview Mode documentation](https://nextjs.org/docs/advanced-features/preview-mode).
- `locales` chứa tất cả ngôn ngữ hỗ trợ (nếu bạn cho phép [Internationalized Routing](https://nextjs.org/docs/advanced-features/i18n-routing)).
- `defaultLocale` chứa các cấu hình ngôn ngữ mặc định (nếu cho phép [Internationalized Routing](https://nextjs.org/docs/advanced-features/i18n-routing))

`getStaticProps` nên được trả về các đối tượng sau:

 - `props` - Một đối tượng tùy chọn với props sẽ được nhận về các page component. Nó nên là một đối tượng nối tiếp hóa [serializable object](https://en.wikipedia.org/wiki/Serialization)
 - `revalidate` Một số lượng giây tùy chọn sau khi 1 trang re-generation có thể xuất hiện. Mặc định là `false`. Khi `revalidate` đặt là `false` có nghĩa là sẽ không có sự xác nhận lại (revalidation), vì vậy trang (page) sẽ được cached cho đến lần build tiếp theo.
 - `notFound` - Một giá trị boolean tùy chọn cho phép page trả về trạng thái 404 và page. Dưới đây là ví dụ cách chúng làm việc:
  ```js
export async function getStaticProps(context) {
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  if (!data) {
    return {
      notFound: true,
    }
  }

  return {
    props: { data }, // will be passed to the page component as props
  }
}
```
**Note**: `noteFound` không cần thiết cho chế độ `fallback: false` như đường dẫn duy nhất trả về từ `getStaticPaths` sẽ được pre-rendered

**Note**: Với `notFound: true` thì page sẽ trả về 404 dù trước đó nó có được generated thành công đi nữa. Điều này có ý nghĩa hỗ trợ các trường hợp như là người dùng generated nội dung bị gỡ bỏ bởi tác giả

 - `redirect` - Một giá trị chuyển hướng tùy chọn cho phép chuyển hướng tới các nguồn ở trong và ngoài. Nó phải phù hợp với `{ destination: string, permanent: boolean }`. Trong vài trường hợp hiếm gặp, ta cần gán 1 code trạng thái tùy chỉnh với các http clients để chuyển hướng đúng đắn. Những trường hợp như vậy, bạn có thể sử dụng đặc tính `statusCode` thay thế đặc tính `permanent`, nhưng không phải cùng lúc. Dưới đây là code ví dụ về cách hoạt động:
 
```js
export async function getStaticProps(context) {
  const res = await fetch(`https://...`)
  const data = await res.json()

  if (!data) {
    return {
      redirect: {
        destination: '/',
        permanent: false,
      },
    }
  }

  return {
    props: { data }, // will be passed to the page component as props
  }
}
```

**Note** Chuyển hướng lúc built time hiện đang không được cho phép và nếu chuyển hướng tới đường link đã biết thì chúng nên được thêm vào `next.config.js`

**Note** Bạn có thể import modules ở scope tầng trên trong `getStaticProps`. Imports sử dụng trong `getStaticProps` sẽ [không được gói cho client side](https://nextjs.org/docs/basic-features/data-fetching#write-server-side-code-directly).

**Note** Bạn không nên sử dụng `fetch()` để gọi API route trong `getStaticProps`. Thay vào đó hãy nhập trực tiếp logic sử dụng trong API route. Cũng có thể cần phải cấu trúc lại code 1 chút cho cách này.

### Ví dụ
Dưới đây là một ví dụ sử dụng `getStaticProps` để lấy danh sách các bài posts blog từ một CMS (Content management system). Hãy xem ví dụ dưới đây
```jsx
// posts will be populated at build time by getStaticProps()
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// This function gets called at build time on server-side.
// It won't be called on client-side, so you can even do
// direct database queries. See the "Technical details" section.
export async function getStaticProps() {
  // Call an external API endpoint to get posts.
  // You can use any data fetching library
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // By returning { props: { posts } }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```
Khi nào thì ta nên sử dụng `getStaticProps`??
Nên dùng `getStaticProps` nếu:
- Data yêu cầu render page có sẵn lúc build time trước mỗi request của user.
- Data đến từ một headless CMS
- Data có thể được cached
- Trang (page) phải được pre-rendered (để SEO tốt) và phải nhanh - `getStaticProps` sinh HTML và JSON files, cả 2 có thể được cached bởi CDN để gia tăng hiệu suất.

