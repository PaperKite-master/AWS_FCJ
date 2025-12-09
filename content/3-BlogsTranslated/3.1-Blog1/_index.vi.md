---
title: "Blog 1"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
# Đơn giản hóa quản lý lưu trữ đa tài khoản với Amazon EFS và Amazon EKS

bởi Samyak Kathane, Raja Pamuluri và Venkat Penmetsa – 01 OCT 2025  
Thuộc các danh mục [Amazon EFS](https://aws.amazon.com/blogs/storage/category/storage/amazon-elastic-file-system-efs/), [Amazon EKS](https://aws.amazon.com/blogs/storage/category/compute/amazon-kubernetes-service/), [Expert (400)](https://aws.amazon.com/blogs/storage/category/learning-levels/expert-400/), [Technical How-to](https://aws.amazon.com/blogs/storage/category/post-types/technical-how-to/) | [Permalink](https://aws.amazon.com/blogs/storage/mastering-cross-account-amazon-efs-seamlessly-mount-amazon-efs-on-amazon-eks-cluster/) | [Comments](https://aws.amazon.com/blogs/storage/mastering-cross-account-amazon-efs-seamlessly-mount-amazon-efs-on-amazon-eks-cluster/#Comments)

## Tổng quan

Các tổ chức ngày càng dùng chiến lược đa tài khoản AWS để tăng bảo mật, quản trị và tối ưu vận hành. Với Amazon EFS, bạn có thể chia sẻ tệp POSIX linh hoạt giữa nhiều tài khoản, đồng thời gắn kết (mount) cùng một file system vào nhiều cụm Amazon EKS. Bài viết hướng dẫn thiết lập cross-account mount cho một file system EFS đặt ở tài khoản **shared services** và gắn vào cụm EKS ở tài khoản khác.

## Kiến trúc và lưu ý

- Thiết lập kết nối mạng an toàn giữa VPC ở hai tài khoản qua VPC peering hoặc AWS Transit Gateway.
- Triển khai EFS dạng Regional, tạo mount target ở mọi AZ để giảm chi phí truyền dữ liệu liên AZ.
- Sử dụng **AZ ID** (ví dụ `use1-az1`) thay vì tên AZ (ví dụ `us-east-1a`) để bảo đảm trỏ đúng vị trí vật lý giữa các tài khoản.
- Nên mount bằng File System ID và dùng Amazon Route 53 private hosted zone để phân giải DNS nội bộ.
- Cài AWS EFS CSI Driver ≥ 1.36.0 trên node EKS; tham khảo hướng dẫn [cài amazon-efs-utils](https://docs.aws.amazon.com/efs/latest/ug/installing-amazon-efs-utils.html).

## Tài khoản ví dụ

- **Tài khoản EFS (shared services):** `111111111111`, File System ID: `fs-0c492f870b90c1c9a`.
- **Tài khoản EKS:** `222222222222`, cụm `EKS-cross-account-cluster`.

## Các bước thực hiện

### Bước 1: Kiểm tra EFS trong tài khoản 111111111111

1. Vào EFS console, chọn Region phù hợp và mở file system ID.  
2. Ở tab **Network**, bảo đảm mount target đã tồn tại ở tất cả AZ. Nếu thiếu, tạo theo hướng dẫn [Managing mount targets](https://docs.aws.amazon.com/efs/latest/ug/accessing-fs.html#manage-mt-console).

### Bước 2: Ghi nhận thông tin mạng EFS

1. Từ tab **Network**, lưu **VPC ID**, **AZ ID** và **IPv4** của từng mount target. Thông tin này dùng để cấu hình Route 53 và kiểm tra kết nối.

### Bước 3: Ghi nhận thông tin cụm EKS ở tài khoản 222222222222

1. Vào EKS console, chọn cụm `EKS-cross-account-cluster` và Region tương ứng.  
2. Ở tab **Networking**, ghi **VPC ID** và **Cluster security group**.

### Bước 4: Tạo VPC Peering giữa hai VPC

1. Từ tài khoản EKS (222222222222), vào **VPC > Peering connections > Create peering connection**.  
2. Chọn **VPC requester** là VPC của EKS (Step 3), **Account** là **Another Account** và nhập ID tài khoản EFS `111111111111`.  
3. Region: chọn cùng Region (ví dụ `us-west-1`). **VPC accepter**: dán VPC ID của EFS (Step 2).  
4. Đảm bảo CIDR hai VPC không trùng. Chọn **Create**.

### Bước 5: Chấp nhận peering ở tài khoản EFS

1. Đăng nhập tài khoản 111111111111, vào **VPC > Peering connections**.  
2. Chọn yêu cầu trạng thái *Pending acceptance* và bấm **Accept request**. Trạng thái chuyển sang *Active*.

### Bước 6: Cập nhật route tables ở cả hai tài khoản

1. Trong tài khoản EKS, mở **Route tables**, chọn route table mà node EKS sử dụng, **Edit routes**.  
2. Thêm route: **Destination** = CIDR của VPC EFS; **Target** = Peering connection đã tạo. Lưu thay đổi.  
3. Lặp lại trong tài khoản EFS: Destination = CIDR của VPC EKS; Target = cùng peering connection.

### Bước 7: Mở inbound NFS cho EFS Security Group

1. Ở tài khoản EFS, tab **Network** của file system, sao chép Security Group ID gán cho mount targets.  
2. Vào **VPC > Security groups**, chọn SG đó, **Edit inbound rules**.  
3. Thêm rule:
   - **Type:** NFS  
   - **Source:** `222222222222/sg-<cluster-sg-id>` (Owner ID của tài khoản EKS + SG của cụm EKS từ Step 3).

### Bước 8: Tạo Route 53 Private Hosted Zone trong tài khoản EKS

1. Tạo một private hosted zone cho từng AZ ID theo mẫu:  
   `<availability-zone-id>.<file-system-id>.efs.<region>.amazonaws.com`
2. Gắn hosted zone với VPC của EKS.  
3. Trong mỗi hosted zone, tạo bản ghi A trỏ tới địa chỉ IP mount target tương ứng (lấy từ Step 2).  
   Ví dụ Region `us-west-1` có AZ ID `usw1-az3` và `usw1-az1`:  
   - `usw1-az3.fs-0c492f870b90c1c9a.efs.us-west-1.amazonaws.com` → IP mount target AZ `usw1-az3`  
   - `usw1-az1.fs-0c492f870b90c1c9a.efs.us-west-1.amazonaws.com` → IP mount target AZ `usw1-az1`

### Bước 9: Tạo IAM role cross-account trong tài khoản EFS

1. Đặt biến môi trường:
   ```bash
   export EKS_ACCOUNT_ID=222222222222
   export EFS_ACCOUNT_ID=111111111111
   ```
2. Tạo trust policy cho role `EFSCrossAccountAccessRole` cho phép tài khoản EKS assume:
   ```bash
   cat > efs-cross-account-trust-policy.json <<'EOF'
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": { "AWS": "arn:aws:iam::222222222222:root" },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   EOF

   aws iam create-role \
     --role-name EFSCrossAccountAccessRole \
     --assume-role-policy-document file://efs-cross-account-trust-policy.json
   ```
3. Tải policy mẫu và giới hạn Resource theo ARN file system:
   ```bash
   curl -s https://raw.githubusercontent.com/kubernetes-sigs/aws-efs-csi-driver/master/examples/kubernetes/cross_account_mount/iam-policy-examples/describe-mount-target-example.json -o describe_mt.json
   FS_ARN=$(aws efs describe-file-systems --file-system-id fs-0c492f870b90c1c9a --query 'FileSystems[].FileSystemArn' --output text)
   sed -i "s#\"Resource\" : \"\\*\"#\"Resource\" : \"${FS_ARN}\"#g" describe_mt.json
   aws iam create-policy --policy-name EFSDescribeMountTargetIAMPolicy --policy-document file://describe_mt.json
   aws iam attach-role-policy \
     --role-name EFSCrossAccountAccessRole \
     --policy-arn arn:aws:iam::111111111111:policy/EFSDescribeMountTargetIAMPolicy
   ```

### Bước 10: Cấu hình cụm EKS (tài khoản 222222222222)

1. Cài tiện ích:
   ```bash
   curl -sLO https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz
   tar -xzf eksctl_Linux_amd64.tar.gz -C /tmp
   sudo mv /tmp/eksctl /usr/local/bin
   ```
2. Cập nhật kubeconfig:
   ```bash
   aws eks update-kubeconfig --name EKS-cross-account-cluster --region us-west-1
   ```
3. Liên kết OIDC provider (nếu chưa có):
   ```bash
   eksctl utils associate-iam-oidc-provider \
     --region us-west-1 \
     --cluster EKS-cross-account-cluster \
     --approve
   ```
4. Tạo IAM role cho Amazon EFS CSI driver và service account:
   ```bash
   export ROLE=AmazonEKS_EFS_CSI_DriverRole

   eksctl create iamserviceaccount \
     --name efs-csi-controller-sa \
     --namespace kube-system \
     --cluster EKS-cross-account-cluster \
     --role-name $ROLE \
     --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEFSCSIDriverPolicy \
     --approve

   aws iam attach-role-policy \
     --role-name $ROLE \
     --policy-arn arn:aws:iam::aws:policy/AmazonElasticFileSystemClientFullAccess
   ```
5. Tạo policy cho phép CSI driver assume role cross-account:
   ```bash
   cat > allow-cross-account-assume-policy.json <<'EOF'
   {
     "Version": "2012-10-17",
     "Statement": {
       "Effect": "Allow",
       "Action": "sts:AssumeRole",
       "Resource": "arn:aws:iam::111111111111:role/EFSCrossAccountAccessRole"
     }
   }
   EOF

   aws iam create-policy \
     --policy-name AssumeCrossAccountEFSRole \
     --policy-document file://allow-cross-account-assume-policy.json

   aws iam attach-role-policy \
     --role-name $ROLE \
     --policy-arn arn:aws:iam::222222222222:policy/AssumeCrossAccountEFSRole
   ```
6. Tạo secret chứa role ARN để CSI driver dùng:
   ```bash
   kubectl create secret generic x-account \
     --namespace kube-system \
     --from-literal=awsRoleArn="arn:aws:iam::111111111111:role/EFSCrossAccountAccessRole" \
     --from-literal=crossaccount="true"
   ```
7. Cài add-on EFS CSI driver:
   ```bash
   eksctl create addon \
     --cluster EKS-cross-account-cluster \
     --name aws-efs-csi-driver \
     --service-account-role-arn arn:aws:iam::222222222222:role/${ROLE}
   ```

### Bước 11: Tạo StorageClass và ứng dụng demo

1. `sc.yaml` – StorageClass bật tùy chọn cross-account:
   ```yaml
   apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
     name: efs-sc
   provisioner: efs.csi.aws.com
   mountOptions:
     - tls
     - iam
     - crossaccount   # Cho phép mount chéo tài khoản
   parameters:
     provisioningMode: efs-ap
     fileSystemId: fs-0c492f870b90c1c9a
     directoryPerms: "700"
     csi.storage.k8s.io/provisioner-secret-name: x-account
     csi.storage.k8s.io/provisioner-secret-namespace: kube-system
   ```
2. `app.yaml` – PVC và Pod mẫu:
   ```yaml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: efs-claim
     namespace: efs-demo
   spec:
     accessModes:
       - ReadWriteMany
     storageClassName: efs-sc
     resources:
       requests:
         storage: 5Gi
   ---
   apiVersion: v1
   kind: Pod
   metadata:
     name: efs-app
     namespace: efs-demo
   spec:
     containers:
       - name: app
         image: centos:7
         command: ["/bin/sh"]
         args: ["-c", "while true; do echo $(date -u) >> /data/out; sleep 5; done"]
         volumeMounts:
           - name: persistent-storage
             mountPath: /data
     volumes:
       - name: persistent-storage
         persistentVolumeClaim:
           claimName: efs-claim
   ```
3. Triển khai:
   ```bash
   kubectl create namespace efs-demo
   kubectl apply -f sc.yaml
   kubectl apply -f app.yaml
   ```
4. Kiểm tra:
   ```bash
   kubectl get sc
   kubectl get pvc -n efs-demo
   kubectl get pv
   kubectl get pod -n efs-demo
   kubectl exec -it efs-app -n efs-demo -- cat /data/out
   ```

## Kết luận

Triển khai cross-account mount cho Amazon EFS giúp chia sẻ dữ liệu an toàn giữa nhiều tài khoản AWS, giữ được tính cô lập nhưng vẫn cộng tác hiệu quả. Việc cấu hình đúng VPC peering, route tables, security groups và phân giải DNS qua Route 53, kết hợp quyền IAM và EFS CSI Driver, cho phép các cụm Amazon EKS truy cập một file system EFS chung mà không cần trùng lặp dữ liệu, tối ưu chi phí và vận hành.

## Tác giả

**Samyak Kathane** – Senior Solutions Architect chuyên về lưu trữ AWS (Amazon EFS), hỗ trợ khách hàng xây dựng hệ thống tin cậy, hiệu năng cao và tối ưu chi phí.  
**Raja Pamuluri** – Senior Storage Solutions Architect tập trung lĩnh vực Energy, giúp khách hàng thiết kế và triển khai giải pháp lưu trữ đám mây quy mô lớn.  
**Venkat Penmetsa** – Senior Technical Account Manager, chuyên về Amazon EKS, hỗ trợ khách hàng vận hành và tối ưu Kubernetes trên AWS.

