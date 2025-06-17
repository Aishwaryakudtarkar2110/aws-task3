# AWS VPC Peering: Demo1-VPC ↔ Demo2-VPC

This project demonstrates setting up **two VPCs** in the same AWS region and establishing a **VPC peering connection** to enable secure, private cross-VPC communication.

---

## 🎯 Goal

- Deploy **Demo1-VPC** and **Demo2-VPC** in the same region  
- Peer them securely  
- Update route tables to allow communication across CIDR blocks  

---

## ✅ What You Did

- Created two separate VPCs, e.g.:

  | VPC Name     | CIDR Block     |
  |--------------|----------------|
  | Demo1-VPC    | 10.10.0.0/16   |
  | Demo2-VPC    | 10.20.0.0/16   |

- Established a **VPC peering connection** between them  
- **Accepted** the peering request for the accepter VPC  
- Updated route tables on both sides to route traffic via the peering connection  
- Verified connectivity (e.g. ping or SSH between instances)

---

## 🛠️ Deployment via AWS CLI

```bash
# 1. Create VPCs
aws ec2 create-vpc --cidr-block 10.10.0.0/16 --tag-specifications ResourceType=vpc,Tags=[{Key=Name,Value=Demo1-VPC}]
aws ec2 create-vpc --cidr-block 10.20.0.0/16 --tag-specifications ResourceType=vpc,Tags=[{Key=Name,Value=Demo2-VPC}]

# Assume VPC IDs returned are VPC1_ID and VPC2_ID

# 2. Create VPC peering connection
aws ec2 create-vpc-peering-connection \
  --vpc-id $VPC1_ID \
  --peer-vpc-id $VPC2_ID \
  --tag-specifications ResourceType=vpc-peering-connection,Tags=[{Key=Name,Value=DemoVPCPeering}]

# 3. Accept the peering from the accepter side
aws ec2 accept-vpc-peering-connection --vpc-peering-connection-id $PEERING_ID

# 4. Update route tables
aws ec2 create-route --route-table-id $RTB1_ID \
  --destination-cidr-block 10.20.0.0/16 \
  --vpc-peering-connection-id $PEERING_ID

aws ec2 create-route --route-table-id $RTB2_ID \
  --destination-cidr-block 10.10.0.0/16 \
  --vpc-peering-connection-id $PEERING_ID
