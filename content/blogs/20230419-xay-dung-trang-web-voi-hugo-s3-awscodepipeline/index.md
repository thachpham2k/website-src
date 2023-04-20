---
title: "Xây dựng website cá nhân với Hugo, S3 và AWS Codepipeline"
date: 2023-04-19T00:00:00+07:00
draft: false
github_link: ""
author: "Thạch Phạm"
tags:
  - Hugoweb
  - AWS Codepipeline
  - S3
image: /images/blogs/20230419-xay-dung-trang-web-voi-hugo-s3-awscodepipeline.png
description: "Sẽ ra sao nếu trang web được dựng bằng công cụ của AWS"
toc: 
---
## Ý tưởng

Hôm trước có lên bài về việc tạo static web với Hugo với công cụ là Github và Github Action, chúng ta có thể thực hiện tương tự với các dịch vụ của AWS. Ví dụ, ta có thể sử dụng Amazon S3 để lưu trữ trang web và Amazon CloudFront để phân phối nội dung. Ta cũng có thể sử dụng AWS CodePipeline và AWS CodeBuild để tự động hóa việc triển khai trang web mỗi khi có sự thay đổi trong mã nguồn.

Bài viết này sẽ đi sâu vào việc sử dụng các dịch vụ của AWS để hộ trợ việc tạo static web với hugo.

## Tạo S3 Static web

Bước đầu tiên vẫn là tạo trang web trước, nội dung tính sau 😂. Để host trang web tĩnh trên AWS, chúng ta có thể sử dụng tính năng Static website hosting của Amazon S3. Tuy nhiên, trước khi sử dụng tính năng này, chúng ta cần phải tạo một bucket S3 để lưu trữ các file của trang web. Sau đó, ta có thể cấu hình các quyền truy cập cho bucket S3 để cho phép trang web được truy cập từ bên ngoài. Việc sử dụng tính năng **Static website hosting** của S3 sẽ giúp cho việc host trang web trở nên đơn giản và hiệu quả hơn.

### Tạo S3 Bucket

Việc cần thực hiện là tạo một S3 bucket với tên và tag nhất định. Tuy nhiên, để dễ dàng cho việc lưu trữ và chia sẻ website, cần phải kích hoạt **Object Ownership** và vô hiệu hóa các tùy chọn **Block Public Access** trên bucket.

![Kích hoạt Object Ownership](./images/s3-acls_enabled.png)

![Tắt tùy chọn Block Public Access](./images/S3-Block-Public-Access.png)

### S3 Static Web Hosting

Để kích hoạt tính năng này, trước tiên bạn cần chọn vào S3 bucket muốn bật tính năng, sau đó chọn tab Properties, kéo xuống cuối trang và chọn Enable Static Web Hosting, sau đó nhập `index.html` vào phần **Index document**.

![Bật tính năng S3 static web hosting](./images/s3-static-web-hosting.png)

Tuy nhiên, khi truy cập vào S3 static web bằng tên miền, có thể gặp phải lỗi 403. Lỗi này xảy ra do S3 bucket chưa được public, do đó người dùng bên ngoài không thể truy cập được.

![Lỗi 403 khi truy cập S3 static web](./images/s3-static-web-403-response.png)

### Thêm quyền getObject

Để cho người dùng có thể truy cập vào trang web static trên S3 mà không gặp phải lỗi 403, cần phải thêm quyền truy cập cho bucket. Việc này có thể được thực hiện bằng cách truy cập vào S3 bucket, chọn tab **Permissions**, và chọn phần **Bucket policy**. Sau đó, nhập đoạn mã sau vào phần policy (nhớ điền S3 Bucket name vào phần ARN nha).

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "Statement1",
			"Principal": "*",
			"Effect": "Allow",
			"Action": [
				"s3:GetObject"
			],
			"Resource": [
				"arn:aws:s3:::<S3 Bucket name>/*"
			]
		}
	]
}
```

Lúc này thử truy cập bằng S3 Static web url thì sẽ gặp lỗi 404 Not Found vì nội dung của trang web đang bị trống (không tìm thấy file index.html)

![Lỗi 404 khi truy cập S3 static web](./images/s3-static-web-404-response.png)

## Tạo AWS Codepipeline

Để tự động hóa việc triển khai trang web mỗi khi có sự thay đổi trong mã nguồn (được lưu trữ trong Code Commit) chúng ta có thể sử dụng AWS CodePipeline và AWS CodeBuild.

### AWS CodeCommit

Đây là nơi sẽ chứa mã nguồn của web (Github in AWS).

Tạo file `buildspec.yml`

### AWS CodeBuild



### AWS CodePipeline

## Vấn đề gặp phải và giải pháp

## Lời kết
