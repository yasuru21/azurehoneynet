# Azure Sentinel Lab and SOC Honeynet Deployment and Configuration Guide

This guide walks you through the complete process of setting up, configuring, and testing security incidents in Azure Sentinel using virtual machines, workbooks, and incident response. By the end of this lab, you will have a better understanding of how Azure Sentinel detects threats and how you can respond to and remediate them.

## Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Step 1: Setting Up the Windows Virtual Machine (VM)](#step-1-setting-up-the-windows-virtual-machine-vm)
4. [Step 2: Setting Up Linux Virtual Machine (VM)](#step-2-setting-up-linux-virtual-machine-vm)
5. [Step 3: Configuring Azure Sentinel](#step-3-configuring-azure-sentinel)
6. [Step 4: Deploying Azure Key Vault](#step-4-deploying-azure-key-vault)
7. [Step 5: Creating Azure Sentinel Workbooks](#step-5-creating-azure-sentinel-workbooks)
8. [Step 6: Incident Response and Remediation](#step-6-incident-response-and-remediation)
9. [Step 7: Security Audits and Posture Improvements](#step-7-security-audits-and-posture-improvements)
10. [Step 8: Cost Management and Cleanup](#step-8-cost-management-and-cleanup)
11. [Conclusion](#conclusion)

---

## Overview

In this guide, we will:
- Set up Windows and Linux VMs in Azure.
- Configure Azure Sentinel for security monitoring.
- Deploy key resources, such as Azure Key Vault.
- Create workbooks to monitor security incidents.
- Simulate and respond to security incidents.
- Harden the security environment and improve the security posture.

---

## Prerequisites

Before you begin, ensure you have:
- An active Azure subscription.
- Basic knowledge of Azure services (VMs, Sentinel, NSGs, etc.).
- Permissions to create VMs, Sentinel, and other Azure resources.

---

## Step 1: Setting Up the Windows Virtual Machine (VM)

1. **Navigate to the Azure Portal**:
   - Sign in to your Azure account and go to the portal.
   
2. **Create a New Resource Group**:
   - In the search bar, type **Resource Groups**.
   - Click **Create**, give it a name like `cylab`, and choose a region closest to you.
   
3. **Create a Windows VM**:
   - In the search bar, type **Virtual Machines**.
   - Click **Create** and choose **Azure Virtual Machine**.
   - In the configuration:
     - Choose the **Resource Group** you created earlier.
     - Name the VM something like `windows-vm`.
     - Select **Windows 10 Pro** for the image.
     - Choose a size based on your requirements (recommended: 2 vcpus).
     - Set up **Administrator Account** credentials (e.g., Username: `adminuser`, Password: `YourStrongPassword`).
   
4. **Configure Networking**:
   - Under **Networking**, select or create a new Virtual Network (VNet).
   - Configure the **Network Security Group (NSG)** with the following inbound rules:
     - **Allow RDP (3389)** from your IP.
     - Optionally allow **HTTP (80)** for web traffic.
   
5. **Review and Create**:
   - Review all settings and click **Create** to deploy the VM.
   
6. **Connect to the Windows VM**:
   - Once the VM is deployed, go to **Virtual Machines** and select your newly created VM.
   - Click **Connect**, choose **RDP**, and download the RDP file.
   - Open the RDP file, enter the credentials you created, and log in to the Windows VM.

---

## Step 2: Setting Up Linux Virtual Machine (VM)

1. **Create a Linux VM**:
   - Go to **Virtual Machines** again and click **Create**.
   - Select **Ubuntu Server 20.04 LTS** for the image.
   - Set up a username and SSH key or password for access.

2. **Configure Networking**:
   - Configure the **NSG** to allow SSH (Port 22) from your IP address only.
   
3. **Review and Create**:
   - Click **Create** to deploy the Linux VM.
   
4. **Connect to the Linux VM**:
   - Use SSH to connect to your Linux VM by following the instructions on the **Connect** tab.
     ```bash
     ssh <username>@<vm-public-ip>
     ```

---

## Step 3: Configuring Azure Sentinel

1. **Enable Sentinel**:
   - In the Azure portal, search for **Azure Sentinel**.
   - Click **Create** and attach it to a **Log Analytics Workspace** (if you don’t have one, create it now).

2. **Connect Data Sources**:
   - Under **Configuration**, connect the **Windows Security Event** and **Linux Syslog** data sources for your VMs.
   - Ensure you have enabled log forwarding for both machines.

---

## Step 4: Deploying Azure Key Vault

1. **Create a Key Vault**:
   - In the search bar, type **Key Vault** and select **Create**.
   - Fill out the resource details (e.g., name: `test-keyvault`, resource group: `cylab`).
   
2. **Create a Secret in Key Vault**:
   - Go to **Secrets** in the Key Vault and create a new secret (e.g., name: `test-secret`).
   
3. **Query Logs**:
   - Go to **Azure Diagnostics** and **Storage Blob Logs** to query the activity related to creating and accessing the secret.

---

## Step 5: Creating Azure Sentinel Workbooks

1. **Navigate to Sentinel Workbooks**:
   - In Azure Sentinel, go to **Workbooks** under the **Configuration** section.
   
2. **Create Workbooks**:
   - For each scenario (Windows RDP, SQL Authentication Failure, Linux SSH, Brute Force Map), follow these steps:
     1. Click **Add Workbook** and go to the **Advanced Editor**.
     2. Copy and paste the corresponding JSON from GitHub into the editor.
     3. Save each workbook within the same resource group (e.g., `cylab`).
   
3. **Visualize the Data**:
   - The workbooks will start collecting data from the VMs and show visualizations based on security incidents such as failed login attempts, brute force attacks, and more.

---

## Step 6: Incident Response and Remediation

1. **View and Simulate Incidents**:
   - Go to **Sentinel Incidents** to view logged incidents such as brute force attacks or privilege escalations.
   - Manually trigger incidents by failing to log into the VMs (SSH or RDP).
   
2. **Harden the Network**:
   - For each detected incident, restrict access to the affected VM:
     - In **NSG**, limit SSH and RDP access to your IP address only.
   
3. **Close Incidents**:
   - Once the issue is resolved, close incidents in Sentinel and add notes about remediation steps (e.g., hardened NSG rules).

---

## Step 7: Security Audits and Posture Improvements

1. **Run Security Queries**:
   - Use Azure Log Analytics to query logs for the last 24 hours and gather metrics like failed login attempts, suspicious IP addresses, etc.
   
2. **Improve Secure Score**:
   - Go to **Azure Defender for Cloud** and view the **Secure Score**.
   - Follow the remediation steps provided (e.g., enabling firewalls, auditing VM activity).
   
3. **Enable Regulatory Compliance**:
   - Go to **Regulatory Compliance** under **Defender for Cloud** and enable NIST or other required standards.

---

## Step 8: Cost Management and Cleanup

1. **Check Costs**:
   - Go to **Cost Management** to view your expenses. Azure services can incur charges even when idle, so monitor this regularly.
   
2. **Delete Resources**:
   - Once the lab is complete, delete the resource groups, VMs, and other associated services.
   - Check **Entra** (Azure AD) and delete any test users created.
   
3. **Turn Off Defender for Cloud Plans**:
   - Go to **Defender for Cloud > Environment Settings** and turn off all Defender plans to prevent recurring charges.

---

## Conclusion

You have successfully completed the Azure Sentinel lab! You now have experience in setting up key resources, configuring Sentinel, responding to incidents, and hardening your security environment. Be sure to monitor costs and return to the portal to check for any remaining charges.

For further learning, consider tracking logs over 24 hours to gather more insights or explore more advanced features in Azure Sentinel.
