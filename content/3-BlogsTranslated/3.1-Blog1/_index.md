# Simplifying multi-account storage with Amazon EFS and Amazon EKS

by Samyak Kathane, Raja Pamuluri, and Venkat Penmetsa – 01 OCT 2025  
Categories: [Amazon EFS](https://aws.amazon.com/blogs/storage/category/storage/amazon-elastic-file-system-efs/), [Amazon EKS](https://aws.amazon.com/blogs/storage/category/compute/amazon-kubernetes-service/), [Expert (400)](https://aws.amazon.com/blogs/storage/category/learning-levels/expert-400/), [Technical How-to](https://aws.amazon.com/blogs/storage/category/post-types/technical-how-to/) | [Permalink](https://aws.amazon.com/blogs/storage/mastering-cross-account-amazon-efs-seamlessly-mount-amazon-efs-on-amazon-eks-cluster/) | [Comments](https://aws.amazon.com/blogs/storage/mastering-cross-account-amazon-efs-seamlessly-mount-amazon-efs-on-amazon-eks-cluster/#Comments)

## Overview

Organizations increasingly adopt multi-account AWS strategies for stronger security, governance, and operational efficiency. With Amazon EFS you can flexibly share POSIX files across accounts and mount the same file system to multiple Amazon EKS clusters. This guide shows how to set up cross-account mounts for one EFS file system in a **shared services** account and attach it to an EKS cluster in another account.

## Architecture and notes

- Establish secure network connectivity between VPCs in the two accounts via VPC peering or AWS Transit Gateway.  
- Deploy EFS in Regional mode and create mount targets in every AZ to avoid inter-AZ data transfer costs.  
- Use **AZ IDs** (for example, `use1-az1`) instead of AZ names (`us-east-1a`) to point to the same physical location across accounts.  
- Mount by File System ID and use an Amazon Route 53 private hosted zone for internal DNS resolution.  
- Install AWS EFS CSI Driver ≥ 1.36.0 on EKS nodes; see [installing amazon-efs-utils](https://docs.aws.amazon.com/efs/latest/ug/installing-amazon-efs-utils.html).

## Example accounts

- **EFS account (shared services):** `111111111111`, File System ID: `fs-0c492f870b90c1c9a`.  
- **EKS account:** `222222222222`, cluster `EKS-cross-account-cluster`.

## Steps

### Step 1: Check EFS in account 111111111111

1. Open the EFS console, choose the Region, and open the file system ID.  
2. In **Network**, ensure mount targets exist in every AZ. If missing, create them following [Managing mount targets](https://docs.aws.amazon.com/efs/latest/ug/accessing-fs.html#manage-mt-console).

### Step 2: Record EFS networking info

1. From the **Network** tab, note **VPC ID**, **AZ ID**, and **IPv4** for each mount target. You’ll use this for Route 53 and connectivity checks.

### Step 3: Record EKS cluster info in account 222222222222

1. In the EKS console, select cluster `EKS-cross-account-cluster` and the appropriate Region.  
2. In **Networking**, capture the **VPC ID** and **Cluster security group**.

### Step 4: Create a VPC peering connection between the two VPCs

1. From the EKS account (222222222222), go to **VPC > Peering connections > Create peering connection**.  
2. Set **VPC requester** to the EKS VPC (Step 3); **Account** to **Another Account** and enter the EFS account ID `111111111111`.  
3. Region: choose the same Region (for example, `us-west-1`). **VPC accepter**: paste the EFS VPC ID (Step 2).  
4. Ensure the two VPC CIDRs do not overlap. Choose **Create**.

### Step 5: Accept the peering request in the EFS account

1. Sign in to account `111111111111`, navigate to **VPC > Peering connections**.  
2. Select the request with status *Pending acceptance* and click **Accept request**. The status changes to *Active*.

### Step 6: Update route tables in both accounts

1. In the EKS account, open **Route tables**, choose the table used by EKS nodes, and select **Edit routes**.  
2. Add route: **Destination** = CIDR of the EFS VPC; **Target** = the peering connection created. Save.  
3. Repeat in the EFS account: Destination = CIDR of the EKS VPC; Target = the same peering connection.

### Step 7: Open inbound NFS on the EFS Security Group

1. In the EFS account, on the file system **Network** tab, copy the Security Group ID attached to mount targets.  
2. Go to **VPC > Security groups**, select that SG, choose **Edit inbound rules**.  
3. Add a rule:  
   - **Type:** NFS  
   - **Source:** `222222222222/sg-<cluster-sg-id>` (Owner ID of the EKS account + the EKS cluster SG from Step 3).

### Step 8: Create a Route 53 private hosted zone in the EKS account

1. Create one private hosted zone per AZ ID using the pattern:  
   `<availability-zone-id>.<file-system-id>.efs.<region>.amazonaws.com`
2. Associate the hosted zone with the EKS VPC.  
3. In each hosted zone, add an A record pointing to the mount target IP for that AZ (from Step 2).  
   Example in `us-west-1` with AZ IDs `usw1-az3` and `usw1-az1`:  
   - `usw1-az3.fs-0c492f870b90c1c9a.efs.us-west-1.amazonaws.com` → IP of mount target in `usw1-az3`  
   - `usw1-az1.fs-0c492f870b90c1c9a.efs.us-west-1.amazonaws.com` → IP of mount target in `usw1-az1`

### Step 9: Create a cross-account IAM role in the EFS account

1. Set environment variables:
   ```bash
   export EKS_ACCOUNT_ID=222222222222
   export EFS_ACCOUNT_ID=111111111111
   ```
2. Create a trust policy for `EFSCrossAccountAccessRole` allowing the EKS account to assume it:
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
3. Download the sample policy and scope Resource to the file system ARN:
   ```bash
   curl -s https://raw.githubusercontent.com/kubernetes-sigs/aws-efs-csi-driver/master/examples/kubernetes/cross_account_mount/iam-policy-examples/describe-mount-target-example.json -o describe_mt.json
   FS_ARN=$(aws efs describe-file-systems --file-system-id fs-0c492f870b90c1c9a --query 'FileSystems[].FileSystemArn' --output text)
   sed -i "s#\"Resource\" : \"\\*\"#\"Resource\" : \"${FS_ARN}\"#g" describe_mt.json
   aws iam create-policy --policy-name EFSDescribeMountTargetIAMPolicy --policy-document file://describe_mt.json
   aws iam attach-role-policy \
     --role-name EFSCrossAccountAccessRole \
     --policy-arn arn:aws:iam::111111111111:policy/EFSDescribeMountTargetIAMPolicy
   ```

### Step 10: Configure the EKS cluster (account 222222222222)

1. Install utilities:
   ```bash
   curl -sLO https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz
   tar -xzf eksctl_Linux_amd64.tar.gz -C /tmp
   sudo mv /tmp/eksctl /usr/local/bin
   ```
2. Update kubeconfig:
   ```bash
   aws eks update-kubeconfig --name EKS-cross-account-cluster --region us-west-1
   ```
3. Associate an OIDC provider (if not already):
   ```bash
   eksctl utils associate-iam-oidc-provider \
     --region us-west-1 \
     --cluster EKS-cross-account-cluster \
     --approve
   ```
4. Create an IAM role for the Amazon EFS CSI driver and a service account:
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
5. Create a policy allowing the CSI driver to assume the cross-account role:
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
6. Create a secret containing the role ARN for the CSI driver:
   ```bash
   kubectl create secret generic x-account \
     --namespace kube-system \
     --from-literal=awsRoleArn="arn:aws:iam::111111111111:role/EFSCrossAccountAccessRole" \
     --from-literal=crossaccount="true"
   ```
7. Install the EFS CSI driver add-on:
   ```bash
   eksctl create addon \
     --cluster EKS-cross-account-cluster \
     --name aws-efs-csi-driver \
     --service-account-role-arn arn:aws:iam::222222222222:role/${ROLE}
   ```

### Step 11: Create the StorageClass and demo app

1. `sc.yaml` – StorageClass with cross-account mount enabled:
   ```yaml
   apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
     name: efs-sc
   provisioner: efs.csi.aws.com
   mountOptions:
     - tls
     - iam
     - crossaccount   # Enable cross-account mount
   parameters:
     provisioningMode: efs-ap
     fileSystemId: fs-0c492f870b90c1c9a
     directoryPerms: "700"
     csi.storage.k8s.io/provisioner-secret-name: x-account
     csi.storage.k8s.io/provisioner-secret-namespace: kube-system
   ```
2. `app.yaml` – sample PVC and Pod:
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
3. Deploy:
   ```bash
   kubectl create namespace efs-demo
   kubectl apply -f sc.yaml
   kubectl apply -f app.yaml
   ```
4. Verify:
   ```bash
   kubectl get sc
   kubectl get pvc -n efs-demo
   kubectl get pv
   kubectl get pod -n efs-demo
   kubectl exec -it efs-app -n efs-demo -- cat /data/out
   ```

## Conclusion

Cross-account mounting for Amazon EFS lets you share data safely across AWS accounts, maintaining isolation while collaborating effectively. With correct VPC peering, route tables, security groups, Route 53 DNS, IAM permissions, and the EFS CSI Driver, Amazon EKS clusters can access a shared EFS file system without duplicating data, optimizing cost and operations.

## Authors

**Samyak Kathane** – Senior Solutions Architect specializing in AWS storage (Amazon EFS), helping customers build reliable, high-performance, cost-optimized systems.  
**Raja Pamuluri** – Senior Storage Solutions Architect focused on the Energy sector, helping customers design and deploy large-scale cloud storage solutions.  
**Venkat Penmetsa** – Senior Technical Account Manager specializing in Amazon EKS, helping customers operate and optimize Kubernetes on AWS.

