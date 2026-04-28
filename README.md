# 🔐 1. Jump Server / Bastion Host

## 📌 What it is

A **Jump Server (Jump Host)** or **Bastion Host** is a **secure intermediary server** that you must connect to **before accessing private/internal servers**.

- It sits in a **public subnet**
- Has **strict security controls**
- Acts as a **single entry point** into a private network

------

## 🎯 Why it’s important

Without it:

- Every private server would need public access ❌ (huge security risk)

With it:

- Only **one hardened server is exposed**
- You get:
  - Centralized access control
  - Logging & auditing
  - Reduced attack surface

------

## 🧠 Real-world analogy

Think of it like:

> A **security guard gate** at a corporate office
>  You can’t directly enter the building—you must go through the guard.

------

## 🧩 Typical Architecture

```
Internet
   ↓
[Bastion Host / Jump Server]  (Public Subnet)
   ↓
[Private Servers: DB, App Servers] (Private Subnet)
```

------

## 💡 Example Scenario

### Scenario: Secure SSH access

- You have:
  - EC2 app server (private subnet)
  - No public IP

### Access flow:

```
ssh user@bastion-host
ssh user@private-server
```

Or using SSH Proxy:

```
ssh -J user@bastion user@private-server
```

------

## 🧱 When do you use it?

- Production environments
- Banking systems
- Internal enterprise apps
- When:
  - Servers must NOT be public
  - You need audit logs of access

------

## 🔒 Security Best Practices

- Disable password login (use SSH keys)
- Enable MFA
- Restrict IP access
- Log all sessions

------

# 🌐 2. NAT Gateway

## 📌 What it is

A **NAT Gateway** allows **private subnet instances** to:

- Access the internet (outbound)
- WITHOUT being publicly accessible

------

## 🎯 Why it’s important

Private servers often need:

- OS updates
- API calls
- Package downloads

But:

- You don’t want them exposed to inbound traffic

------

## 🧠 Key idea

> Outbound allowed ✅
>  Inbound blocked ❌

------

## 🧩 Architecture

```
Private Instance → NAT Gateway → Internet
```

------

## 💡 Example Scenario

A backend server needs to:

- Download updates from Ubuntu repo

Flow:

```
Private EC2 → NAT Gateway → Internet
Internet → NAT Gateway → Private EC2 (response only)
```

------

## ⚙️ Features

- Fully managed
- Highly available
- Scales automatically
- No SSH access

------

# 🖥️ 3. NAT Instance

## 📌 What it is

A **NAT Instance** is:

> A manually configured EC2 instance that performs NAT

------

## 🎯 Why it exists

Before NAT Gateway existed, people used NAT instances.

------

## 🧩 Architecture

```
Private Instance → NAT Instance (EC2) → Internet
```

------

## 💡 Example Scenario

- Launch EC2
- Enable IP forwarding
- Configure iptables

------

## ⚠️ Downsides

- Manual setup
- Needs patching
- Not highly available by default
- Scaling is manual

------

# ⚖️ NAT Gateway vs NAT Instance

| Feature      | NAT Gateway                 | NAT Instance                      |
| ------------ | --------------------------- | --------------------------------- |
| Management   | Fully managed               | Self-managed                      |
| Availability | Highly available (AZ-level) | Single point of failure           |
| Scaling      | Automatic                   | Manual                            |
| Performance  | High throughput             | Limited by instance size          |
| Maintenance  | None                        | You must patch/update             |
| Cost         | Higher                      | Lower (but operational cost high) |
| Security     | AWS-managed                 | You configure                     |

------

## 🔍 Key Difference Summary

- NAT Gateway = **“Set and forget”**
- NAT Instance = **“DIY solution”**

------

# 🧠 When to use what?

## ✅ Use Bastion Host when:

- You need secure admin access to private servers
- You want centralized access control

------

## ✅ Use NAT Gateway when:

- Private servers need internet access
- You want **high availability + low maintenance**

------

## ✅ Use NAT Instance when:

- You want:
  - Custom routing rules
  - Cost optimization (small scale)
- You’re okay managing it

------

# 🔗 Combined Architecture Example

```
                Internet
                    ↓
          [Bastion Host]
                    ↓
        ---------------------
        |                   |
 [Private App Server]   [Private DB]
        |                   |
        -----------↓--------
               NAT Gateway
                    ↓
                Internet
```

------

# 🧪 End-to-End Scenario

### Example: Production Web App

- Bastion Host → Admin SSH access
- Private App Server → Runs backend
- Private DB → No internet access
- NAT Gateway → App server fetches updates

------

# 🚨 Common Mistakes

- ❌ Exposing private servers directly to internet
- ❌ Using NAT Instance without HA
- ❌ Allowing SSH from anywhere (0.0.0.0/0)

------

# 🧾 Quick Summary

| Component    | Purpose                                 |
| ------------ | --------------------------------------- |
| Bastion Host | Secure access into private network      |
| NAT Gateway  | Outbound internet for private resources |
| NAT Instance | Manual alternative to NAT Gateway       |