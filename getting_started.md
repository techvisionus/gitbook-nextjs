**Khởi đầu**
Chào mừng bạn đến với gitbook nextjs. Nếu bạn chưa có kinh nghiệm với nextjs, bạn có thể tham khảo bộ tài liệu này của chúng tôi.

**Yêu cầu hệ thống**
Yêu cầu phiên bản nodejs từ 12.22.0 trở lên
Hệ điều hành


## Thiết lập
Đầu tiên để tạo một ứng dụng Nextjs ta sẽ chạy câu lệnh: 

```bash
yarn create next-app
# hoặc
npx create-next-app@latest
```
*yarn (hay tương tự với npx) là một công cụ quản lý gói phần mềm nguồn mở giúp các lập trình viên có thể sử dụng và chia sẻ các gói phần mềm. Yarn thực hiện các công việc với tốc độ rất nhanh, bảo mật cao và đáng tin cậy)*

Nếu muốn khởi tạo 1 project typescript ta có thể thêm '--typescript':
Chạy lệnh: 

```bash
yarn create next-app --typescript
# hoặc
npx create-next-app@latest --typescript
```
## Thiết lập thủ công
Bạn có thể cài đặt 'next', 'react', 'react-down' trong project như sau: 

```bash
npm install next react react-dom
# or
yarn add next react react-dom
```
Mở file package.json ta thấy đoạn script như sau: 
```json
"scripts": {
  "dev": "next dev",
  "build": "next build",
  "start": "next start",
  "lint": "next lint"
}
```

-   `dev`  - Chạy  [`next dev`](https://nextjs.org/docs/api-reference/cli#development)  sẽ khởi chạy ứng dụng trong development mode. Ví dụ: `yarn next dev`
-   `build`  - Chạy  [`next build`](https://nextjs.org/docs/api-reference/cli#build)  sẽ khởi chạy ứng dụng trong production mode. Ví dụ `yarn next build`
-   `start`  - Chạy  [`next start`](https://nextjs.org/docs/api-reference/cli#production)  sẽ khởi chạy production server. Ví dụ `yarn next start`
-   `lint`  - Chạy  [`next lint`](https://nextjs.org/docs/api-reference/cli#lint)  sẽ thiết lập nextjs được xây dựng trong cấu hình ESLint. Ví dụ `yarn next lint`

Nextjs được xây dựng xung quanh định nghĩa 1 trang (pages). Mỗi page là một React component xuất ra từ file `.js`, `.jsx`, `.ts`, or `.tsx`trong danh mục `pages`