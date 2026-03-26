# Landing Zone Accelerator trên AWS cho Ngành Y tế

## Mục lục
1. [Tổng quan](#tổng-quan)
2. [Các Framework Bảo mật](#các-framework-bảo-mật)
3. [Các Security Controls](#các-security-controls)
4. [Cấu trúc Tổ chức](#cấu-trúc-tổ-chức)
5. [Sơ đồ Kiến trúc](#sơ-đồ-kiến-trúc)
6. [Chi phí](#chi-phí)
7. [Hướng dẫn Cài đặt](#hướng-dẫn-cài-đặt)
8. [Các Cân nhắc Bổ sung](#các-cân-nhắc-bổ-sung)
9. [Tài liệu Tham khảo](#tài-liệu-tham-khảo)
10. [Đóng góp](#đóng-góp)
11. [Giấy phép](#giấy-phép)
12. [Lịch sử Phiên bản](#lịch-sử-phiên-bản)

## Tổng quan

**Landing Zone Accelerator (LZA) cho Ngành Y tế** là một bản triển khai chuyên biệt cho ngành y tế của giải pháp [Landing Zone Accelerator trên AWS](https://aws.amazon.com/solutions/implementations/landing-zone-accelerator-on-aws/), được thiết kế để tuân thủ các best practices của AWS và phù hợp với nhiều framework tuân thủ toàn cầu. Được xây dựng trên nền tảng các tài khoản AWS Control Tower tiêu chuẩn, bao gồm `Management`, `Audit`, và `LogArchive`, LZA cho Ngành Y tế triển khai thêm các tài nguyên giúp thiết lập nền tảng sẵn sàng về bảo mật, tuân thủ và khả năng vận hành. Điều quan trọng cần lưu ý là giải pháp Landing Zone Accelerator, tự bản thân nó, sẽ không giúp bạn đạt được tuân thủ hoàn toàn. Nó cung cấp hạ tầng nền tảng để từ đó các giải pháp bổ sung có thể được tích hợp. Bạn phải xem xét, đánh giá và phê duyệt giải pháp phù hợp với các tính năng bảo mật, công cụ và cấu hình cụ thể của tổ chức bạn.

## Các Framework Bảo mật

Ngành y tế chịu sự quản lý chặt chẽ bởi nhiều quy định. LZA cho Ngành Y tế cung cấp thêm các guardrails để giúp giảm thiểu các mối đe dọa mà khách hàng y tế phải đối mặt. LZA cho Ngành Y tế không nhằm mục đích đầy đủ tính năng để tuân thủ hoàn toàn, mà nhằm giúp đẩy nhanh quá trình di chuyển lên cloud và tái cấu trúc cloud cho các tổ chức phục vụ ngành y tế. Mặc dù đã nỗ lực rất nhiều để giảm thiểu công sức xây dựng hạ tầng sẵn sàng cho production một cách thủ công, bạn vẫn cần tùy chỉnh theo nhu cầu kinh doanh riêng của mình.

Giải pháp này bao gồm các controls từ các framework ở nhiều khu vực địa lý khác nhau, bao gồm HIPAA, NCSC, ENS High, C5, và Fascicolo Sanitario Elettronico. Nếu bạn đang triển khai giải pháp Landing Zone Accelerator trên AWS cho Ngành Y tế, vui lòng tham khảo ý kiến đội ngũ AWS của bạn để hiểu các controls cần thiết đáp ứng yêu cầu của bạn.

## Các Security Controls

Các controls này được tạo dưới dạng guardrails phát hiện (detective) hoặc phòng ngừa (preventative) trong môi trường AWS thông qua các AWS Config rules hoặc Service Control Policies (SCPs). Trong file `organization-config.yaml` có các phần khai báo SCPs, Tagging Policies, và Backup Policies. SCPs có thể rất cụ thể cho từng tổ chức và workload(s) của nó, cần được xem xét và chỉnh sửa để đáp ứng yêu cầu cụ thể của bạn. Các policy mẫu đã được cung cấp cho các mục sau:

- `Service Control Policies`: Một service control policy đã được cung cấp trong `service-control-policies/scp-hlc-base-root.json` nhằm ngăn chặn các tài khoản rời khỏi tổ chức của bạn hoặc vô hiệu hóa block public access. Policy `service-control-policies/scp-hlc-hipaa-service.json` là ví dụ về policy có thể được sử dụng để đảm bảo chỉ các dịch vụ đủ điều kiện HIPAA mới được sử dụng trong một OU hoặc tài khoản cụ thể. Điều quan trọng cần lưu ý là SCPs không được cập nhật tự động và các thay đổi trong danh sách dịch vụ đủ điều kiện HIPAA cần được cập nhật thủ công. Tuy nhiên, đây là ví dụ về cách tổ chức của bạn có thể đảm bảo rằng một danh sách chọn lọc các dịch vụ AWS được sử dụng cho các trường hợp sử dụng cụ thể.
- `Tagging Policies`: Một tagging policy mẫu đã được cung cấp trong `tagging-policies/org-tag-policy.json` để minh họa cách định nghĩa tag `Cost Center` trên toàn bộ AWS Organization. Một tagging policy bổ sung, `tagging-policies/healthcare-org-tag-policy.json`, cho thấy cách bạn có thể mở rộng thêm các policy này để định nghĩa `Environment Type` cho các workload `Prod`, `QA`, và `Dev`, `Data Classification` để theo dõi các workload nhạy cảm và không nhạy cảm như `PHI` và `Company Confidential` và cách áp dụng chúng cho các dịch vụ AWS cụ thể. Policy mẫu nên được chỉnh sửa để phản ánh các cost center của tổ chức bạn sao cho các tài nguyên được cung cấp bởi LZA được tự động gắn tag theo yêu cầu kinh doanh của bạn.
- `Backup policies`: Một backup policy mẫu đã được cung cấp trong `backup-policies/org-backup-policies.json` làm ví dụ về cách lên lịch backup cùng với các cài đặt quản lý lifecycle và retention.

Trong file `security-config.yaml`, các dịch vụ bảo mật AWS có thể được cấu hình như AWS Config, AWS Security Hub, và bật mã hóa lưu trữ. Các alarm và metric bổ sung đã được cung cấp để thông báo cho bạn về các hoạt động trong môi trường AWS Cloud của bạn. Để xem danh sách đầy đủ các dịch vụ và cài đặt có thể cấu hình, xem [Hướng dẫn Triển khai LZA trên AWS](#tài-liệu-tham-khảo) trong phần tài liệu tham khảo bên dưới. File này cũng chứa các AWS Config rules tạo nên danh sách các detective guardrails được sử dụng để đáp ứng nhiều controls từ các framework khác nhau. Các rules này được triển khai thông qua sự kết hợp của các dịch vụ AWS:

- AWS Security Hub
  - AWS Foundational Security Best Practices v1.0.0
  - CIS AWS Foundations Benchmark v1.4.0
  - NIST Special Publication 800-53 Revision 5
- AWS Config
  - Các rules có trong conformance pack mẫu [Operational Best Practices for HIPAA Security](https://docs.aws.amazon.com/config/latest/developerguide/operational-best-practices-for-hipaa_security.html).
- AWS Audit Manager
  - Các rules có trong các standard framework dựng sẵn [HIPAA Security Rule: Feb 2003](https://docs.aws.amazon.com/audit-manager/latest/userguide/HIPAA.html) và [HIPAA Omnibus Final Rule](https://docs.aws.amazon.com/audit-manager/latest/userguide/HIPAA-omnibus-rule.html).

File `global-config.yaml` chứa các cài đặt cho phép kích hoạt regions, ghi log tập trung sử dụng AWS CloudTrail và Amazon CloudWatch Logs cùng thời gian lưu giữ log để giúp bạn đáp ứng nhu cầu kiểm toán và giám sát cụ thể.

Bạn được khuyến khích xem xét các cài đặt này để hiểu rõ hơn những gì đã được cấu hình và những gì cần thay đổi cho yêu cầu cụ thể của bạn.

## Cấu trúc Tổ chức

Các tài khoản Healthcare LZA được tạo và tổ chức như sau:

<!--
```sh
+-- Root
|   +-- Management
|   +-- Security
|       +-- LogArchive
|       +-- Audit
|   +-- Infrastructure
|       +-- Infra-Prod
|       +-- Infra-Dev
|   +-- HIS
|       +-- HIS-Non-Prod
|   +-- EIS
|       +-- EIS-Prod
```
-->

![Cấu trúc Tổ chức Healthcare LZA](./images/LZA_Healthcare_Org_Structure.png)

Organizational Unit (OU) Health Information System (HIS) đại diện cho cấu trúc logic nơi chứa các workload có dữ liệu nhạy cảm, chẳng hạn như dữ liệu kinh doanh quan trọng hoặc Personal Health Information (PHI). Trong khi đó, OU Executive Information System (EIS) dành cho các workload kinh doanh có thể không yêu cầu cùng mức độ kiểm soát quy định. Cấu trúc OU này được cung cấp sẵn cho bạn. Tuy nhiên, bạn có thể tự do thay đổi cấu trúc tổ chức, các Organizational Units (OUs), và tài khoản để đáp ứng nhu cầu cụ thể của mình. Để biết thêm thông tin về cách tổ chức cấu trúc AWS OU và tài khoản tốt nhất, vui lòng tham khảo phần Recommended OUs and accounts trong mục [Các Cân nhắc Bổ sung](#các-cân-nhắc-bổ-sung) bên dưới khi bạn bắt đầu thử nghiệm với LZA cho Ngành Y tế.

## Sơ đồ Kiến trúc

Cấu trúc Tổ chức AWS LZA cho Ngành Y tế
![Kiến trúc Healthcare LZA](./images/LZA_Healthcare_Summary_Diagram.png)

Mặc định, LZA cho Ngành Y tế xây dựng cấu trúc tổ chức như trên, ngoại trừ OU `Management` và `Security` được bạn định nghĩa trước khi khởi chạy LZA. Sơ đồ kiến trúc bên dưới nêu bật các triển khai chính:

- OU `Infrastructure`
  - Chứa OU `Infra-Dev` và OU `Infra-Prod` dưới OU `Infrastructure`
  - Chứa tài khoản `Network-Prod` dưới OU `Infra-Prod`
  - Tài khoản `Network-Prod` chứa Transit Gateway cho việc định tuyến hạ tầng
  - `Network-Prod` chứa VPC `Network-Endpoints` và VPC `Network_Inspection` tại `us-east-1`
  - Mỗi VPC sử dụng CIDR block /22 trong dải RFC-1918 10.0.0.0/8
- OU `HIS`
  - Chứa OU `HIS-Non-Prod` dưới OU `HIS`
  - Chứa tài khoản `HIS-Dev` dưới OU `HIS-Non-Prod`
  - Chứa một VPC duy nhất tại `us-east-1`
  - VPC sử dụng CIDR block /16 trong dải RFC-1918 10.0.0.0/8
- OU `EIS`
  - Chứa OU `EIS-Prod` dưới OU `HIS`
  - Chứa tài khoản `EIS-Prod` dưới OU `EIS-Prod`
  - Chứa một VPC duy nhất tại `us-east-1`
  - VPC sử dụng CIDR block /16 trong dải RFC-1918 10.0.0.0/8

Sơ đồ Mạng AWS LZA cho Ngành Y tế
![Sơ đồ Mạng Healthcare LZA](./images/LZA_Healthcare_Network_Diagram_v3.1.png)

Các tài khoản trong OU `EIS` và `HIS` đại diện cho hạ tầng tiêu chuẩn để triển khai development hoặc production cho các workload của bạn.

OU `Infrastructure` cung cấp các chức năng chuyên biệt sau:

- Tài khoản `Network-Prod` chứa VPC `Network-Inspection` để kiểm tra lưu lượng AWS cũng như định tuyến lưu lượng đến và đi từ Internet. Nếu một route table được định nghĩa, ví dụ `Network-Main-Segregated`, lưu lượng sẽ đi từ VPC `HIS-Dev` qua Transit Gateway `Network-Main`, nơi nó có thể được kiểm tra\* trước khi bị blackhole hoặc tiếp tục đến internet hoặc đích cuối cùng.
- Tài khoản `Network` cũng chứa VPC `Network-Endpoints`, dùng để lưu trữ các VPC interface endpoints tập trung; tất cả các spoke VPCs sẽ sử dụng các endpoints tập trung này thông qua Transit Gateway. Vị trí tập trung này và các route tables tương ứng cho phép bạn thiết kế mạng hiệu quả và phân chia kiểm soát truy cập phù hợp.

**\*Lưu ý:** File network-configs.yaml không cung cấp giải pháp kiểm tra mạng để kiểm tra lưu lượng. Cần cấu hình bổ sung để triển khai thiết bị kiểm tra mạng hoặc cân nhắc sử dụng [AWS Network Firewall](https://aws.amazon.com/network-firewall/) cùng với việc cập nhật route tables.

## Chi phí

Bạn chịu trách nhiệm về chi phí của các dịch vụ AWS được sử dụng khi chạy giải pháp này. Tính đến tháng 9 năm 2022, chi phí chạy giải pháp này sử dụng Landing Zone Accelerator với các file cấu hình y tế và AWS Control Tower tại Region US East (N. Virginia) trong môi trường thử nghiệm không có workload hoạt động là khoảng $400-$500 USD mỗi tháng. Khi các dịch vụ AWS và workload bổ sung được triển khai, chi phí sẽ tăng lên.

| Dịch vụ AWS                                      | Chi phí mỗi tháng |
| ------------------------------------------------- | ------------------ |
| AWS CloudTrail                                    | $4.30              |
| Amazon CloudWatch                                 | $8.75              |
| Amazon Config                                     | $35.55             |
| Amazon GuardDuty                                  | $5.75              |
| Amazon AWS Key Management Services (AWS KMS)      | $15.65             |
| Amazon Route 53                                   | $2.00              |
| Amazon Simple Storage Service (Amazon S3)         | $1.48              |
| Amazon Virtual Private Cloud (Amazon VPC)         | $301.56            |
| Amazon AWS Security Hub                           | $44.32             |
| Amazon Secrets Manager                            | $0.48              |
| Amazon Simple Notification Services (Amazon SNS)  | $0.42              |
| Tổng chi phí hàng tháng                           | $420.26            |

## Hướng dẫn Cài đặt

Để xem tổng quan chung về các file cấu hình LZA cho Ngành Y tế, xem [Tóm tắt Cấu hình](documentation/config-summary.md).

Hướng dẫn gồm ba phần:

1. [Điều kiện Tiên quyết](documentation/1-prereqs.md) - Bao gồm thu thập thông tin, các phụ thuộc bên ngoài cho lần triển khai đầu tiên và thiết lập tài khoản quản lý AWS Organizations ban đầu.
2. [Cài đặt](documentation/2-install.md) - Triển khai LZA ban đầu, triển khai kiến trúc tham chiếu y tế và tùy chỉnh.
3. [Sau Triển khai](documentation/3-post-deployment.md) - Các cân nhắc để tiếp tục phát triển landing zone.

## Các Cân nhắc Bổ sung

Mặc dù Healthcare LZA hướng đến việc áp dụng các best practices cho khách hàng y tế một cách có hệ thống, nó cố ý tránh _quá cứng nhắc_ để tôn trọng thực tế riêng biệt của từng tổ chức. Hãy coi Healthcare LZA cơ bản là điểm khởi đầu tốt, nhưng hãy ghi nhớ mục tiêu của bạn khi bắt đầu tùy chỉnh cho yêu cầu kinh doanh cụ thể. Từ góc độ này, AWS cung cấp các tài nguyên mà bạn nên tham khảo khi bắt đầu tùy chỉnh triển khai Healthcare LZA:

1. Bộ file cấu hình này đã được thử nghiệm với AWS Control Tower phiên bản 3.3. Bắt đầu từ AWS Control Tower phiên bản 3.0, Control Tower hỗ trợ sử dụng AWS CloudTrail Organization Trail. File global-config.yaml hiển thị organizationTrail được đặt là false vì nó được kích hoạt thông qua thiết lập AWS Control Tower.
2. Tham khảo bài blog [Best Practices] cho Organizational Units với AWS Organizations để có cái nhìn tổng quan.
3. [Recommended OUs and accounts]. Phần này của Whitepaper `Organizing your AWS Environment Using Multiple Accounts` thảo luận về việc triển khai các OU có mục đích cụ thể ngoài các OU nền tảng được thiết lập bởi LZA. Ví dụ, bạn có thể muốn thiết lập OU `Sandbox` để thử nghiệm, OU `Policy Staging` để kiểm tra an toàn các thay đổi policy trước khi triển khai rộng hơn, hoặc OU `Suspended` để giữ, hạn chế và cuối cùng là ngừng sử dụng các tài khoản không còn cần thiết.
4. [AWS Security Reference Architecture] (SRA). SRA "là bộ hướng dẫn toàn diện để triển khai đầy đủ các dịch vụ bảo mật AWS trong môi trường multi-account." Tài liệu này nhằm giúp bạn khám phá "bức tranh toàn cảnh" về bảo mật AWS và các dịch vụ liên quan đến bảo mật để xác định kiến trúc phù hợp nhất với yêu cầu bảo mật riêng của tổ chức bạn.
5. Transit Gateway Flow logs không được bật mặc định, hãy làm việc với đội ngũ AWS của bạn để xác định xem việc bật TGW Flow logs có giúp bạn đáp ứng các yêu cầu quy định và tổ chức hay không.

## Tài liệu Tham khảo

- [Implementation Guide] LZA trên AWS. Đây là tài liệu chính thức của dự án Landing Zone Accelerator và là điểm khởi đầu của bạn. Sử dụng hướng dẫn triển khai để thiết lập môi trường của bạn và sau đó quay lại dự án này để tùy chỉnh cho ngành y tế.
- Repository GitHub [LZA Accelerator] của AWS Labs. Codebase chính thức của dự án Landing Zone Accelerator.

<!-- Hyperlinks -->

[Best Practices]: https://aws.amazon.com/blogs/mt/best-practices-for-organizational-units-with-aws-organizations/
[Recommended OUs and accounts]: https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/recommended-ous-and-accounts.html
[AWS Security Reference Architecture]: https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/welcome.html
[Implementation Guide]: https://docs.aws.amazon.com/solutions/latest/landing-zone-accelerator-on-aws/landing-zone-accelerator-on-aws.pdf
[LZA Accelerator]: https://github.com/awslabs/landing-zone-accelerator-on-aws
[Operational Best Practices for HIPAA Security]: https://docs.aws.amazon.com/config/latest/developerguide/operational-best-practices-for-hipaa_security.html
[VPC Sharing: key considerations and best practices]: https://aws.amazon.com/blogs/networking-and-content-delivery/vpc-sharing-key-considerations-and-best-practices/

## Đóng góp

Xem [CONTRIBUTING](CONTRIBUTING.md) để biết thêm thông tin.

## Giấy phép

Thư viện này được cấp phép theo Giấy phép MIT-0. Xem file [LICENSE](LICENSE).

## Lịch sử Phiên bản
### v1.9.0-a - 31/10/2024
- Thay đổi: Phiên bản này đã được thử nghiệm sử dụng LZA v1.9.0
- Sửa lỗi: Không có

### v1.9.0-b - 16/03/2025
- Thay đổi:
  - Cập nhật README.md này để phản ánh chi phí cấu hình giảm - vì inspection networking không được bao gồm mặc định.
  - Cập nhật 1-prereqs.md và 2-install.md với hướng dẫn bổ sung về cài đặt home_region.
  - Cập nhật config-summary.md để phản ánh các config rules được triển khai.
  - Cập nhật config/service-control-policies/scp-hcl-hipaa-service.json và config/service-control-policies/Readme.md để phản ánh thay đổi danh sách HIPAA Eligible Services tính đến 12/2024.
- Sửa lỗi: Không có

### v1.9.0-c - 17/05/2025
- Thay đổi:
  - 02/2025
    - Thay đổi SCP Readme để phản ánh đổi tên dịch vụ SageMaker.
    - "Amazon SageMaker [excludes Studio Lab, Ground Truth Plus, Public Workforce and Vendor Workforce for all features]" --> "Amazon SageMaker AI [formerly Amazon Sagemaker, excludes Studio Lab, Ground Truth Plus, Public Workforce and Vendor Workforce for all features]"
  - 03/2025
    - Thêm "AWS Fargate [ECS and EKS engines only]".
    - Thêm các API OpenSearch và SSM bổ sung.
    - Thêm opensearch ingestion và opensearch serverless (apis osis:* và aoss:*).
    - Thêm sms quicksetup (api ssm-quicksetup:*).
  - Thêm Tools Monitoring / Telemetry.
        - APIs pi:* và applicationinsights:*
  - Cập nhật để phản ánh Danh sách HIPAA Eligible Services ngày 16/04/2025
- Sửa lỗi: Không có

### v1.9.0-d - 17/06/2025
- Thay đổi:
  - 06/2025
    - Đổi tên AWS Security Hub thành AWS Security Hub CSPM (formerly AWS Security Hub)
    - Thêm tool API qdeveloper:*
    - Sửa lỗi chính tả trong README chính "high regulated" thành "highly regulated"
- Sửa lỗi: Không có

### v1.9.0-e - 29/11/2025
- Thay đổi:
  - 11/2025
    - Thêm "Amazon Bedrock AgentCore" - bedrock-agentcore:*
    - Thêm "AWS Parallel Computing Service (PCS)" - pcs:*
    - Thêm "Aurora DSQL" api - dsql:*
    - Đổi tên "Amazon QuickSight" thành "Amazon Quick Suite [formerly Amazon QuickSight]" - api vẫn là quicksight:*
    - Cập nhật AWS Transcribe thành "AWS Transcribe [Includes Healthscribe]" - api vẫn là transcribe:*
    - Đổi tên "Amazon AppStream 2.0" thành "Amazon WorkSpaces Applications [formerly known as Amazon AppStream 2.0]" - api vẫn là appstream:*
    - Thêm tool "Resource Group Tagging" - api tag:*
    - Thêm tool "Tag Editor" - api resource-explorer:*
    - Xóa "AWS RoboMaker" - api robomaker:*
- Sửa lỗi: Không có

### v1.9.0-f - 26/03/2026
- Thay đổi:
  - 03/2026
    - Cập nhật phiên bản Control Tower Landing Zone từ 3.3 lên 4.0
    - Dịch README.md sang tiếng Việt, backup bản tiếng Anh sang README-en.md
- Sửa lỗi: Không có
