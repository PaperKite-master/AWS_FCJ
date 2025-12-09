---
title: "Blog 2"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
# Lập trình sáng tạo cho mọi người: Pi Jam mở rộng kỹ năng số cho học sinh thiếu cơ hội ở Ấn Độ với AWS

bởi Mohammed Reda và Aanya Niaz, 01 OCT 2025 – thuộc các danh mục [Customer Solutions](https://aws.amazon.com/blogs/publicsector/category/post-types/customer-solutions/), [EdTechs](https://aws.amazon.com/blogs/publicsector/category/public-sector/education/edtechs/), [Education](https://aws.amazon.com/blogs/publicsector/category/public-sector/education/), [Public Sector](https://aws.amazon.com/blogs/publicsector/category/public-sector/) | [Permalink](https://aws.amazon.com/blogs/publicsector/creative-coding-for-everyone-how-pi-jam-expands-digital-skills-for-underserved-learners-in-india-with-aws/) | [Share](https://aws.amazon.com/blogs/publicsector/creative-coding-for-everyone-how-pi-jam-expands-digital-skills-for-underserved-learners-in-india-with-aws/)


Chính sách Giáo dục Quốc gia 2020 của Ấn Độ (NEP 2020 – [National Education Policy 2020](https://www.education.gov.in/en/nep/about-nep)) kêu gọi công nghệ đóng vai trò trung tâm trong giáo dục công bằng, chất lượng cao, nhấn mạnh “online and digital education: ensuring equitable use of technology.” Tuy nhiên, kết nối đáng tin cậy và thiết bị vẫn còn ngoài tầm với của nhiều học sinh, đặc biệt ở trường nông thôn và trường công:

- Theo báo cáo UDISE+ 2023–24 ([Unified District Information System for Education Plus](https://dashboard.udiseplus.gov.in/#/)), chỉ 53,9% trường học ở Ấn Độ có kết nối internet và 57,2% có máy tính.
- Ở cấp hộ gia đình, 67,6% trẻ em sống trong nhà có smartphone, nhưng 26,1% vẫn không thể truy cập thiết bị này ngay cả khi điện thoại có sẵn, theo [Annual Status of Education Report](https://img.asercentre.org/graphics/householdmajorfindings2.pdf).

Những khoảng cách này hạn chế cơ hội cho hàng triệu học sinh tiếp cận kỹ năng lập trình sáng tạo ([creative computing](https://www.setu.ie/courses/bsc-in-creative-computing)) và giải quyết vấn đề như NEP 2020 đề ra. Đây là nơi [Pi Jam Foundation](https://www.thepijam.org/) — tổ chức phi lợi nhuận cam kết mang kỹ năng lập trình sáng tạo và kỹ năng số đến học sinh thiếu cơ hội bằng [Amazon Web Services](https://aws.amazon.com/) (AWS) — tham gia để thu hẹp khoảng cách số.

## Pi Jam Foundation và Code Mitra: Cloud-native ngay từ đầu

Pi Jam Foundation xây dựng [Code Mitra](https://www.codemitra.org/landing), nền tảng modular mở đưa lập trình sáng tạo đến học sinh và giáo viên ở Ấn Độ. Nền tảng dùng lập trình khối ([block-based programming](https://subjectguides.york.ac.uk/coding/scratch)) để rèn các kỹ năng thế kỷ 21 như logic, giải quyết vấn đề và hợp tác. Các dự án được bản địa hóa, từ mô hình bảo tồn nước ở Kashmir đến tối ưu giao thông ở Maharashtra.

Nền tảng tích hợp với LMS quốc gia như [DIKSHA](https://diksha.gov.in/index.html). Được thiết kế cho môi trường tài nguyên thấp, Code Mitra chạy trên điện thoại Android giá rẻ, hỗ trợ offline; giáo viên có công cụ bồi dưỡng chuyên môn và đồng sáng tạo nội dung địa phương.

Ngay từ đầu, Code Mitra vận hành hoàn toàn trên AWS để đảm bảo độ tin cậy và khả năng mở rộng, tận dụng [AWS Lambda](https://aws.amazon.com/pm/lambda/), [Amazon Aurora](https://aws.amazon.com/rds/aurora/), [Amazon S3](https://aws.amazon.com/s3/), [Amazon CloudFront](https://aws.amazon.com/cloudfront/), [Amazon API Gateway](https://aws.amazon.com/api-gateway/), [Amazon ElastiCache](https://aws.amazon.com/elasticache/) và các dịch vụ managed khác. Pi Jam cũng dùng [Amazon Bedrock](https://aws.amazon.com/bedrock/) để chấm ý tưởng hackathon quy mô lớn và mở rộng AI cho [Scratch](https://scratch.mit.edu/about), giúp học sinh thử nghiệm AI trong môi trường lập trình trực quan.

## Tác động ở quy mô quốc gia

Từ khi ra mắt, Code Mitra đã tiếp cận 1,14 triệu học sinh tại 95% quận của Ấn Độ. 76% học sinh truy cập bài học trên thiết bị giá rẻ; các hoạt động theo bối cảnh đạt tỷ lệ hoàn thành 58% và 30% học sinh nộp ý tưởng gốc. Hơn 11.000 giáo viên tại 14 quận được đào tạo; 71% cho biết hoạt động dễ tùy chỉnh và 84% ghi nhận sự tham gia cao hơn trong lớp.

Về kỹ thuật, Pi Jam báo cáo cải thiện 30% thời gian phản hồi API sau khi hiện đại hóa hạ tầng với AWS managed services, giúp trải nghiệm mượt mà ngay cả ở băng thông thấp.

## Hỗ trợ từ Education Equity Initiative

[AWS Education Equity Initiative](https://aws.amazon.com/about-aws/our-impact/education-equity-initiative/) (EEI) cung cấp promotional credits giúp Pi Jam mở rộng nhanh tại các khu vực thiếu cơ hội. Hỗ trợ này giúp tổ chức hackathon học sinh lớn nhất Ấn Độ – [Eco Creativity and Innovation Hackathon](https://www.codemitra.org/competition/5) với hơn 700.000 người tham gia và 200.000 ý tưởng, đồng thời tài trợ các cổng học tập cấp bang như phiên bản Code Mitra cho bang Telangana. Nhờ đó, học sinh và giáo viên trường công trên toàn bang có lộ trình lập trình sáng tạo kèm tài nguyên địa phương, chứng minh tác động bền vững của EEI.

## Kế hoạch tương lai

Pi Jam đặt mục tiêu tiếp cận thêm 1 triệu học sinh vào 03/2026 và 7 triệu vào 03/2028, ưu tiên trường nông thôn và công lập. Kế hoạch tính năng gồm lộ trình học tập cá nhân hóa dựa trên AI và hệ thống gợi ý nội dung.

## Kết luận

Pi Jam Foundation cho thấy cách tiếp cận cloud-native trên AWS có thể mở rộng lập trình sáng tạo cho học sinh dễ bị bỏ lại phía sau. Với sự hỗ trợ liên tục từ AWS Education Equity Initiative, tổ chức sẵn sàng phục vụ thêm hàng triệu học sinh và đưa cá nhân hóa AI vào trải nghiệm học tập, giúp mọi trẻ em — bất kể thiết bị hay kết nối — sở hữu kỹ năng cần thiết cho tương lai số.

Khám phá cách chương trình giáo dục của bạn tận dụng cloud để mở rộng tiếp cận và cải thiện kết quả học tập tại [AWS Education page](https://aws.amazon.com/education/).

TAGS: [Amazon Aurora](https://aws.amazon.com/blogs/publicsector/tag/amazon-aurora/), [Amazon CloudFront](https://aws.amazon.com/blogs/publicsector/tag/amazon-cloudfront/), [Amazon S3](https://aws.amazon.com/blogs/publicsector/tag/amazon-s3/), [AWS Lambda](https://aws.amazon.com/blogs/publicsector/tag/aws-lambda/), [AWS Public Sector](https://aws.amazon.com/blogs/publicsector/tag/aws-public-sector/), [customer story](https://aws.amazon.com/blogs/publicsector/tag/customer-story/), [EdTech](https://aws.amazon.com/blogs/publicsector/tag/edtech/), [education](https://aws.amazon.com/blogs/publicsector/tag/education/)

**Mohammed Reda**  
Senior solutions architect tại AWS. Ông hỗ trợ các trường học, đại học và công ty EdTech ở Vương quốc Anh áp dụng công nghệ đám mây để cải thiện dịch vụ giáo dục và đổi mới trên AWS.

**Aanya Niaz**  
Global education equity lead tại AWS, tập trung mở rộng tiếp cận công nghệ đám mây để hỗ trợ các tổ chức xây dựng giải pháp học tập sáng tạo, đặc biệt cho học sinh thiếu cơ hội.

