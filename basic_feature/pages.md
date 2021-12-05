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

## Sinh tĩnh - Static Generation (nên dùng)

Nếu 1 trang sử dụng **Static Generation**, trang HTML được sinh ra trong thời gian build. Có nghĩa là trang HTML được sỉnh ra khi chạy `next build`. Phần HTML này sẽ được tái sử dụng trong mỗi request. Nó có thể được cached bởi CDN.
Trong Next.js ta có thể sinh tĩnh 1 page khi có hoặc không có data. Hãy xem trong một số trường hợp.

**Static Generation không có data**
Mặc định, Next.js tiền render trang sử dụng Static Generation không cần data. Ví dụ:
```jsx
function About() {
  return <div>About</div>
}

export default About
```
Lưu ý rằng trang này không cần lấy dữ liệu từ bên ngoài để tiền render (pre-rendered). Trường hợp này, Next.js sinh 1 file HTML mỗi trang trong suốt thời gian build.

**Static Generation với data**
Vài pages cần lấy dữ liệu từ ngoài vào để tiền render (pre-rendering). Có 2 trường hợp và 1 hoặc cả 2 có thể ứng dụng. Trong mỗi trường hợp, ta có thể sử dụng những hàm mà Next.js cung cấp:

1. Nội dung trang phụ thuộc dữ liệu ngoài: Dùng `getStaticProps`.
2. Đường dẫn trang phụ thuộc dữ liệu ngoài: Dùng `getStaticPaths` (thường được dùng thêm với `getStaticProps`).

Trường hợp 1: Nội dung trang phụ thuộc dữ liệu ngoài

**Ví dụ:** Trang blog của bạn cần lấy danh sách các bài block từ một CMS (content management system)

```jsx
// TODO: Need to fetch `posts` (by calling some API endpoint)
//       before this page can be pre-rendered.
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

export default Blog
```
Để lấy dữ liệu trước khi render, Next.js chấp thuận `export` một hàm `async` gọi là `getStaticProps` từ cùng 1 file. Hàm này được gọi lúc build và để ta lấy dữ liệu về `props` của trang lúc tiền render.

```jsx
function Blog({ posts }) {
  // Render posts...
}

// This function gets called at build time
export async function getStaticProps() {
  // Call an external API endpoint to get posts
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
Muốn rõ hơn về `getStaticprops` làm việc như nào, ghé  [Data Fetching documentation](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation).

**Trường hợp 2: Đường dẫn trang phụ thuộc dữ liệu ngoài**

Next.js cho phép ra tạo trang với đường dẫn động. Ví dụ, ta có thể tạo file với tên `pages/posts/[id].js` để đưa ra bài blog dựa trên `id`. Điều này sẽ cho phép bạn thể hiện bài post với `id: 1` khi truy cập `posts/1`

Học nhiều hơn về đường dẫn động (dynamic routing), ghé [Dynamic Routing documentation](https://nextjs.org/docs/routing/dynamic-routes).

Tuy nhiên `id` mà ta muốn tiền render trong thời gian build phụ thuộc vào dữ liệu ngoài.
**Ví dụ:** giả sử ta chỉ thêm 1 bài blog (với `id: 1`) tới database. Trong trường hợp này, ta chỉ muốn tiền render `posts/1` lúc build time.
Sau đó, ta có thể thêm bài post thứ 2 với `id: 2`. Sau đó ta muốn tiền render `posts/2`.

Vậy với đường dẫn trang được tiền pre-rendered phụ thuộc vào dữ liệu ngoài. Để xử lý, Next.js cho phép `export` một hàm `async` gọi là `getSStaticPaths` từ một trang động (`pages/posts/[id].js` trong trường hợp này). Hàm này được gọi trong thời gian build và cho phép ta xasc định đường dẫn nào muốn pre-render.

```jsx
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
```
Trong `pages/posts/[id].js`, ta cần export `getStaticProps` vì vậy ta cần lấy dữ liệu về  bài post với `id` và sử dụng nó để pre-render trang:

```jsx
function Post({ post }) {
  // Render post...
}

export async function getStaticPaths() {
  // ...
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
Để học thêm về `getStaticPaths`, ghé [Data Fetching documentation](https://nextjs.org/docs/basic-features/data-fetching#getstaticpaths-static-generation).

**Khi nào thì nên dùng Static Generation?**
Tôi khuyến nghị bạn sử dụn Static Generation (kể cả với data hay không) bất cứ khi nào có thể bởi vì trang của bạn có thể được build 1 lần và dùng được CDN, điều này nhanh hơn là server phải render page trong mỗi request.

Ta có thể dùng Static Generation với rất nhiều loại trang khác nhau như:
- Marketing pages
- Bài blog và portfolios
- Danh sách sản phẩm thương mại
- Tài liệu và trợ giúp

Có thể hỏi bản thân: "Liệu ta có thể pre-render" trang này trước request của người dùng. Nếu câu trả lời là có, hãy chọn Static Generation.
Nói cách khác Static Generation không tốt nếu ta không thể pre-render 1 trang trước request của người dùng. Có thể trang của bạn hiển thị dữ liệu thường xuyên được cập nhật, và nội dung trang thay đổi trên mỗi request.

Trong trường hợp này, bạn có thể làm một trong số các điều sau:

- Dùng Static Generation với Client-side Rendering: Bạn có thể bỏ qua pre-rendering 1 số phần của trang và sau đó dùng JavaScript client-side render chúng. Để biết thêm chi tiết, tham khảo [Data Fetching documentation](https://nextjs.org/docs/basic-features/data-fetching#fetching-data-on-the-client-side).
- Dùng Server-side Rendering: Next.js pre-render một trang trên mỗi request. Nó sẽ chậm hơn 1 chút vì trang không để được cached mởi CDN, nhưng pre-render trang sẽ luôn được cập nhật. Ta sẽ nói về cách tiếp cận này phía dưới.

**Server-side Rendering**

`Cũng có thể gọi là "SSR" hay "Dynamic Rendering - Render động"`

Nếu 1 trang sử dụng Server-side Rendering, trang HTML được sinh ra trên mỗi request.

Để sử dụng Server-side Rendering cho mỗi page, ta sẽ cần `export` một hàm `async` gọi là `getServerSideProps`. Hàm này sẽ được gọi bởi server trên mỗi request.

Ví dụ, giả sử rằng trang của bạn thường xuyên cần pre-render cập nhật dữ liệu (lấy dữ liệu từ api ngoài). Ta có thể viết `getServerSideProps` để lấy dữ liệu và đưa chúng vào trang (Page) như dưới đây: 

```jsx
function Page({ data }) {
  // Render data...
}

// This gets called on every request
export async function getServerSideProps() {
  // Fetch data from external API
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  // Pass data to the page via props
  return { props: { data } }
}

export default Page
```

Như ta thấy ở trên, `getServerSideProps` đã được sử dụng, tham khảo [Data Fetching documentation](https://nextjs.org/docs/basic-features/data-fetching#getserversideprops-server-side-rendering).

**Tổng kết**

- Static Generation (khuyến nghị): HTML được sinh ra trong lúc build time và sẽ được tái sử dụng trên mỗi request. Để tạo 1 trang dùng Static Generation, có thể export thành phần của trang (component page) hoặc export `getStaticProps` (và `getStaticPaths` nếu cần thiết). Nó rất tốt cho trang vì có thể pre-render trước mỗi request của người dùng. Ta cũng có dùng nó với Client-side Rendering để lấy thêm dữ liệu
- Server-side Rendering: HTML được sinh ra trên mỗi request. Để làm sử dụng Server-side Rendering, export `getServerSideProps`. Bởi vì Server-side Rendering khiến hiệu năng thấp hơn Static Generation, chỉ dùng khi thật cần thiết