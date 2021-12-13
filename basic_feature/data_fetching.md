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

TypeScript: dùng `getStaticProps`

Với TypeScript, bạn có thể dùng `GetStaticProps` từ `next`:
```ts
import { GetStaticProps } from 'next'

export const getStaticProps: GetStaticProps = async (context) => {
  // ...
}
```
Nếu bạn muốn tham khảo từ props, bạn có thể dùng `InferGetStaticPropsType<typeof getStaticProps>` như sau:
```tsx
import { InferGetStaticPropsType } from 'next'

type Post = {
  author: string
  content: string
}

export const getStaticProps = async () => {
  const res = await fetch('https://.../posts')
  const posts: Post[] = await res.json()

  return {
    props: {
      posts,
    },
  }
}

function Blog({ posts }: InferGetStaticPropsType<typeof getStaticProps>) {
  // will resolve posts to type Post[]
}

export default Blog
```

Note: Next.js có thời gian timeout mặc định của static generation là 60 giây. Nếu có page mới không hoàn thành generate trong thời gian timeout, nó sẽ generate thêm 3 lần nữa. Nếu lần 4 không thành, quá trình build thất bại. Thời gian timeout này có thể thay đổi bằng cách cấu hình như sau:
```js
// next.config.js
module.exports = {
  // time in seconds of no pages generating during static
  // generation before timing out
  staticPageGenerationTimeout: 90,
}
```
### [Incremental Static Regeneration](https://nextjs.org/docs/basic-features/data-fetching#incremental-static-regeneration)

Next.js cho phép bạn tạo hoặc cập nhật static page sau khi build trang web, Incremental Static Regeneration (ISR) cho phép bạn dùng static-generation trên mỗi page cơ bản, **mà không cần build lại cả trang web**. Với ISR, bạn có thể giữ lại nhiều lợi ích của static page khi scale lên hàng triệu page.

Xem xét ví dụ về `getStaticProps` trước đó, nhưng giờ Incremental Static Regeneration cho phép thông qua đặc tính `revalidate`:

```jsx
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
// It may be called again, on a serverless function, if
// revalidation is enabled and a new request comes in
export async function getStaticProps() {
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  return {
    props: {
      posts,
    },
    // Next.js will attempt to re-generate the page:
    // - When a request comes in
    // - At most once every 10 seconds
    revalidate: 10, // In seconds
  }
}

// This function gets called at build time on server-side.
// It may be called again, on a serverless function, if
// the path has not been generated.
export async function getStaticPaths() {
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // We'll pre-render only these paths at build time.
  // { fallback: blocking } will server-render pages
  // on-demand if the path doesn't exist.
  return { paths, fallback: 'blocking' }
}

export default Blog
```

Khi một request đươc làm cho 1 page được pre-rendered lúc build time, nó sẽ khởi tạo cached page.

- Mỗi request tới page sau request khởi tạo và trước 10s đều được cached và tức thời.
- Sau 10s, request tiếp theo sẽ vẫn show được cached page
- Next.js trigger một regeneration của page trong background.
- Mỗi page được generate thành công, Next.js sẽ vô hiệu hóa cache và hiển thị page được cập nhật. Nếu background regeneration thất bại, page cũ sẽ không thay đổi. 

Khi một request được tạo ra từ đường dẫn không được generated, Next.js sẽ server-side page trong request đầu tiên. Request tương lai sẽ phục vụ static file từ cache.

Để biết hêm về cache globally và xử lý rollback, tham khảo [Incremental Static Regeneration](https://vercel.com/docs/next.js/incremental-static-regeneration).

Đọc file: sử dụng `process.cwd()`

File có thể được đọc trực tiếp từ filesystem trong `getStaticProps`.

Để làm điều này chúng ta sẽ phải có đường dẫn đầy đủ tới file.

Từ khi Next.js biên dịch code thành các danh mục riêng biệt bạn có thể sử dụng `__dirname` như là đường dẫn nó sẽ trả về khác với danh mục các page.

Thay vì bạn sử dụng `process.cwd()` cho bạn danh mục nơi mà Next.js được thực thi.

```jsx
import { promises as fs } from 'fs'
import path from 'path'

// posts will be populated at build time by getStaticProps()
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>
          <h3>{post.filename}</h3>
          <p>{post.content}</p>
        </li>
      ))}
    </ul>
  )
}

// This function gets called at build time on server-side.
// It won't be called on client-side, so you can even do
// direct database queries. See the "Technical details" section.
export async function getStaticProps() {
  const postsDirectory = path.join(process.cwd(), 'posts')
  const filenames = await fs.readdir(postsDirectory)

  const posts = filenames.map(async (filename) => {
    const filePath = path.join(postsDirectory, filename)
    const fileContents = await fs.readFile(filePath, 'utf8')

    // Generally you would parse/transform the contents
    // For example you can transform markdown to HTML here

    return {
      filename,
      content: fileContents,
    }
  })
  // By returning { props: { posts } }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts: await Promise.all(posts),
    },
  }
}

export default Blog
```
**Chi tiết kĩ thuật**

**Chỉ chạy lúc build time**

Bởi vì `getStaticProps` chạy lúc build time, nó không nhận dữ liệu mà chỉ có sẵn trong suốt thời gian request, chẳng hạn như tham số truy vấn hoặc HTTP header vì nó sinh trang HTML tĩnh.

**Viết code server-side trực tiếp**

Chú ý rằng `getStaticProps` chạy chỉ trên server-side. Nó sẽ không bao giờ được chạy trên client-side. Nó sẽ không như vậy dù JS bundle trên browser. Điều này có nghĩa bạn có thể viết code như là query database trực tiếp mà không gửi chúng tới browser. Bạn không nên lấy một API route từ `getStaticProps` - thay vào đó, bạn có thể viết code server-side trong `getStaticProps`.

Bạn có thể sử dụng công cụ này để kiểm chứng Next.js loại bỏ những gì khỏi client-side bundle.

**Static Generated cả HTML và JSON**

Khi một page với `getStaticProps` được pre-rendered lúc build time, thêm vào trang HTML, Next.js sinh một JSON file giữ kết quả của việc chạy `getStaticProps`.

File JSON này sẽ được sử dụng trong client-side routing thông qua `next/link` ([documentation](https://nextjs.org/docs/api-reference/next/link)) hoặc `next/router` ([documentation](https://nextjs.org/docs/api-reference/next/router)). Khi bạn điều hướng tới page được pre-rendered sử dụng  `getStaticProps`, Next.js lấy JSON file (pre-computed lúc build time) và dùng nó như props cho page component. Có nghĩa là page client-side chuyển tiếp sẽ không gọi `getStaticProps` vì chỉ JSON đã xuất được sử dụng.


Khi sử dụng Incremental Static Generation `getStaticProps` sẽ được thực thi bên ngoài để generate JSON phục vụ client-side navigation. Bạn có thể thấy điều này trong form của nhiều request được tạo ra trong cùng page, tuy nhiên điều này có chủ đích và không ảnh hướng tới người dùng cuối.

**Duy nhất trên page**

`getStaticProps` có thể chỉ xuất từ 1 page. Bạn không thể xuất nó từ non-page files.

Một trong những lý do của giới hạn này là React cần phải có rất cả yêu cầu dữ liệu trước khi page được rendered.

Ban phải dùng `export async function getStaticProps() {}` - nó sẽ không hoạt động nếu bạn thêm `getStaticProps` như là thuộc tính của page component.

**Chạy trên mỗi request khi phát triển (development)** 

Trong phát triển (`next dev`), `getStaticProps` sẽ được gọi trên mỗi request.

**Preview mode**

Trong một số trường hợp, bạn có thể muốn bypass tạm thời Static Generation và render page lúc request time thay vì build time. Ví dụ, bạn có thể sử dụng 1 headless CMS và muốn preview bản draft trước khi chúng được published.

Use case này được hỗ trợ bởi Next.js bằng tính năng **Preview Mode**. Đọc thêm tại [Preview Mode documentation](https://nextjs.org/docs/advanced-features/preview-mode).

## `GetStaticPaths` (Static Generation)

Nếu 1 page có đường dẫn động (dynamic routes) và dùng `getStaticProps` thì cần định nghĩa 1 list  các đường dẫn (paths) phải có để rendered HTML lúc build time.

Nếu bạn export 1 gọi hàm `async` là `getStaticPaths` từ 1 page để sử dụng dynamic routes, Next.js sẽ pre-render tất cả đường dẫn bằng `getStaticPaths`.
```jsx
export async function getStaticPaths() {
  return {
    paths: [
      { params: { ... } } // See the "paths" section below
    ],
    fallback: true, false, or 'blocking' // See the "fallback" section below
  };
}
```
**The `paths` key (required)**
Key `path` quyết định đường dẫn (path) nào sẽ được pre-rendered. Ví dụ, giả định rằng bạn có 1 page sử dụng dynamic routes tên `pages/posts/[id].js` . Nếu bạn export `getStaticPaths` từ page này và trả về `paths` như sau:
```js
return {
  paths: [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ],
  fallback: ...
}
```
Sau đó Next.js sẽ generate tĩnh `posts/1` và `posts/2` lúc build time sử dụng page component trong `pages/posts/[id]/js`.

Chú ý rằng value của mỗi `params` phải phù hợp với các tham số được dùng trong tên page:

- Nếu tên page là `pages/posts/[postId]/[commentId]`, thì `params` nên chứa `postId` và `commentId`.
- Nếu tên page dùng catch-all route, ví dụ `pages/[...slug]`, thì `params` nên chứa `slug` có mảng (array). Ví dụ nếu mảng này là `['foo', 'bar']`.
- Nếu page sử dụng 1 catch-all route tùy chọn, cung cấp `null`, `[]`, `undefined` hoặc `false` để render đường dẫn root-most. Ví dụ, nếu bạn cung cấp `slug: false` cho `pages/[[...slug]]`, Next.js sẽ generate tĩnh trang `/`.

Khóa `fallback` (required)

Đối tượng trả về bởi `getStaticPaths` phải chứa khóa `fallback`

`fallback: false`

Nếu `fallback` là `false` thì mọi path không trả về bởi `getStaticPaths` sẽ dẫn về trang 404. Bạn có thể làm điều này nếu bạn có 1 số lượng nhỏ đường dẫn để pre-render - vì vậy tất cả chúng được generate tĩnh lúc build time. Nó cũng có ích khi page mới không được thêm thường xuyên. Nếu bạn thêm vài item vào data source và cần render page mới, bạn có thể chạy lại.

Dưới đây là 1 ví dụ về pre-render 1 bài blog gọi `pages/posts/[id].js`. Danh sách bài blog sẽ được lấy từ 1 CMS và trả về bởi `getStaticPaths`. Sau đó với mỗi page, nó lấy dữ liệu bài post từ 1 CMS sử dụng `getStaticProps`. Xem thêm tại [Pages documentation](https://nextjs.org/docs/basic-features/pages).

```jsx
// pages/posts/[id].js

function Post({ post }) {
  // Render post...
}

// This function gets called at build time
export async function getStaticPaths() {
  // Call an external API endpoint to get posts
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // We'll pre-render only these paths at build time.
  // { fallback: false } means other routes should 404.
  return { paths, fallback: false }
}

// This also gets called at build time
export async function getStaticProps({ params }) {
  // params contains the post `id`.
  // If the route is like /posts/1, then params.id is 1
  const res = await fetch(`https://.../posts/${params.id}`)
  const post = await res.json()

  // Pass post data to the page via props
  return { props: { post } }
}

export default Post
```

**`fallback: true`**
- Nếu `fallback` là `true`, sau đó hành vi của `getStaticProps` sẽ rendered HTML lúc build time bởi `getStaticProps`.
- Đường dẫn (paths) không được generate lúc build time sẽ không dẫn đến trang 404. Thay vào đó, Next.js sẽ cung cấp 1 phiên bản `fallback` của page trên request đầu tiên về 1 path như vậy. Chú ý: phiên bản `fallback` này sẽ không thể phục vụ cho crawler như google, bing,... và thay vào đó sẽ render 1 path trong chế độ `blocking`.
- Trong nền, Next.js sẽ generate tĩnh 1 request path HTML và JSON. Điều này bao gồm cả chạy `getStaticProps`