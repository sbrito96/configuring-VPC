# Configuring a VPC in AWS

## 📌 Project Overview
Amazon Virtual Private Cloud (Amazon VPC) allows you to provision a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define. In this project, I built a **VPC**, created **subnets**, configured **route tables**, and set up network components required to deploy resources such as an Amazon EC2 instance.

![image](https://github.com/user-attachments/assets/a770ef18-eab1-4fd1-9195-9bf5611f06f0)


## 🎯 Objectives
By completing this project, I achieved the following:

✅ Created a **VPC** with a private and public subnet, an **Internet Gateway (IGW)**, and a **NAT Gateway**.  
✅ Configured **route tables** to direct traffic for local and internet-bound communication.  
✅ Launched a **bastion server** in a public subnet.  
✅ Used the bastion server to log in to an instance in a private subnet.  
✅ Deployed an EC2 instance in the private subnet and connected to it via the bastion server.  

---

## **1️⃣ Creating a VPC**

1. Open the **AWS Management Console**, search for **VPC**, and navigate to the VPC Management Console.
2. In the left navigation pane, go to **Your VPCs**.
3. Click **Create VPC** and configure:
   - **Name tag:** `Lab VPC`
   - **IPv4 CIDR block:** `10.0.0.0/16`
   - **IPv6 CIDR block:** `No IPv6 CIDR block`
   - **Tenancy:** `Default`
4. Click **Create VPC**.

![image](https://github.com/user-attachments/assets/b64a23a2-70bf-4680-a504-52e78d5c781f)


6. Enable **DNS hostnames** for EC2 instances:
   - Select the newly created VPC → **Actions** → **Edit VPC settings** → Enable **DNS hostnames** → Save.

![image](https://github.com/user-attachments/assets/e2f1d2ac-e4da-499c-852a-1220c1842b0d)


---

## **2️⃣ Creating Subnets**

### **Public Subnet**
1. In the **VPC console**, go to **Subnets**.
2. Click **Create Subnet** and configure:
   - **VPC ID:** `Lab VPC`
   - **Subnet Name:** `Public Subnet`
   - **IPv4 CIDR Block:** `10.0.0.0/24`
   - **Availability Zone:** Choose the first in the list.
3. Click **Create Subnet**.

![image](https://github.com/user-attachments/assets/e8d0e6cf-8a53-410f-91d1-43bc63dc7f3c)

4. Enable auto-assign public IP:
   - Select `Public Subnet` → **Actions** → **Edit subnet settings** → Enable **auto-assign public IPv4** → Save.

![image](https://github.com/user-attachments/assets/4ec5fddf-dac1-4444-a2fa-504b25a0d1c0)


### **Private Subnet**
1. Repeat the process to create a private subnet:
   - **Subnet Name:** `Private Subnet`
   - **IPv4 CIDR Block:** `10.0.2.0/23`
2. Click **Create Subnet**.

![image](https://github.com/user-attachments/assets/faedaff5-4469-4a27-a9dd-275261799591)


---

## **3️⃣ Creating an Internet Gateway**
1. In the **VPC console**, go to **Internet Gateways**.
2. Click **Create Internet Gateway** and name it `Lab IGW`.
3. Click **Create Internet Gateway**.

![image](https://github.com/user-attachments/assets/f86671f9-a336-4fd1-a0ad-e13b277435e6)

4. Attach the IGW to the VPC:
   - Select `Lab IGW` → **Actions** → **Attach to a VPC** → Select `Lab VPC` → Attach.

![image](https://github.com/user-attachments/assets/84efdf3b-b2ea-4803-bffd-75ed32f465dc)

---

## **4️⃣ Configuring Route Tables**

### **Public Route Table (For Internet Traffic)**
1. In the **VPC console**, go to **Route Tables**.
2. Click **Create Route Table** and configure:
   - **Name:** `Public Route Table`
   - **VPC:** `Lab VPC`
3. Click **Create Route Table**.

![image](https://github.com/user-attachments/assets/ee416be5-8761-44e9-90d4-9f4b230a8dc1)

4. Add a route for internet-bound traffic:
   - Select `Public Route Table` → **Routes tab** → **Edit routes**.
   - Click **Add Route** and configure:
     - **Destination:** `0.0.0.0/0`
     - **Target:** `Internet Gateway` → Select `Lab IGW`.
   - Click **Save changes**.
  
![image](https://github.com/user-attachments/assets/852a92c5-f921-4b67-bc0d-53e57b57c855)

5. Associate the public subnet with this route table:
   - **Subnet associations** → **Edit subnet associations** → Select `Public Subnet` → Save.

![image](https://github.com/user-attachments/assets/c7a506e3-ae47-4e56-ba83-988495078d54)

Now, the public subnet has access to the internet. The private subnet remains isolated.

---

## **5️⃣ Launching a Bastion Server in the Public Subnet**
1. In the **AWS EC2 console**, click **Launch Instances**.
2. Configure:
   - **Name:** `Bastion Server`
   - **AMI:** `Amazon Linux 2023`
   - **Instance Type:** `t3.micro`
   - **VPC:** `Lab VPC`
   - **Subnet:** `Public Subnet`
   - **Auto-assign public IP:** `Enable`
3. Configure Security Group:
   - **Name:** `Bastion Security Group`
   - **Inbound Rule:** Allow SSH (`port 22`) from **Anywhere**.
4. Click **Launch Instance**.

![image](https://github.com/user-attachments/assets/241d75d4-0b04-47ca-a70f-76ca892aadab)
![image](https://github.com/user-attachments/assets/dcb1d27f-a562-4f74-a8aa-8dcc8510be78)
![image](https://github.com/user-attachments/assets/eca26087-91dc-472c-9942-5bbbe669980d)
![image](https://github.com/user-attachments/assets/baccb57c-3013-4e29-8ed7-afc5860cb2a6)

---

## **6️⃣ Creating a NAT Gateway for the Private Subnet**
1. In the **VPC console**, go to **NAT Gateways**.
2. Click **Create NAT Gateway** and configure:
   - **Name:** `Lab NAT Gateway`
   - **Subnet:** `Public Subnet`
   - **Elastic IP:** Allocate a new Elastic IP.
3. Click **Create NAT Gateway**.

![image](https://github.com/user-attachments/assets/98d01a1e-15f3-4546-8d79-d7426ffee80c)

5. Update the **Private Route Table**:
   - Go to **Route Tables** → Select `Private Route Table`.
   - Click **Routes** → **Edit routes** → **Add Route**:
     - **Destination:** `0.0.0.0/0`
     - **Target:** `NAT Gateway` → Select `Lab NAT Gateway`.
   - Click **Save changes**.

![image](https://github.com/user-attachments/assets/3376c30b-fba3-4364-9eae-1f676a26c7d7)

Now, private subnet instances can **access the internet**, but cannot be accessed from outside.

---

## **Testing the Private Subnet**

### **Launching an EC2 Instance in the Private Subnet**
1. Launch a new EC2 instance with:
   - **Name:** `Private Instance`
   - **Subnet:** `Private Subnet`
   - **Security Group:** Allow SSH from `Bastion Security Group`.
2. Copy the **private IP** of this instance.

![image](https://github.com/user-attachments/assets/5ad44e69-05eb-418d-b700-5281aab600d0)
![image](https://github.com/user-attachments/assets/01701804-184e-4fb7-862d-8b531c242321)
![image](https://github.com/user-attachments/assets/931c76c8-71ea-46c7-a5ed-0f7f48aa999a)
![image](https://github.com/user-attachments/assets/96b0c44a-80f4-4154-bd1e-30bd138b8a69)
![image](https://github.com/user-attachments/assets/c20a048a-bf13-48fc-ab96-1e5de075ac27)

### **Connecting via the Bastion Server**
1. Connect to the **Bastion Server** using **EC2 Instance Connect**.

![image](https://github.com/user-attachments/assets/8d0f7c51-a5db-4116-a47a-d8a217d5536f)
![image](https://github.com/user-attachments/assets/1e24e7d1-bc42-4599-bff8-f3d5745d2e9f)

2. From the Bastion Server, SSH into the Private Instance:
   ```bash
   ssh PRIVATE-IP
   ```
3. If prompted, enter `yes` to continue.

![image](https://github.com/user-attachments/assets/a2989824-105a-4d3f-8d2b-4e9fc321c2fd)

### **Testing Internet Connectivity from the Private Instance**
Run the following command to test if the private instance can reach the internet via the NAT Gateway:
```bash
ping -c 3 amazon.com
```
✅ If packets are received, the configuration was successful.
![image](https://github.com/user-attachments/assets/72860a82-916f-41eb-ae24-c3b2f83193dc)

---

## **🚀 Conclusion**
This lab successfully demonstrated how to:
✅ Create a **VPC** with public and private subnets.  
✅ Set up an **Internet Gateway (IGW)** for public subnet access.  
✅ Configure a **NAT Gateway** to allow internet access for private subnet instances.  
✅ Deploy a **bastion server** to enable secure access to private resources.  
✅ Securely connect to a private instance using the **bastion server**.

This setup is commonly used in AWS for **secure, scalable cloud architectures**.
