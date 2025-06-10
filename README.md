
# AWS Social Messaging / WhatsApp Setup Guide

This document outlines the steps required to set up a WhatsApp phone number and configure AWS services to enable WhatsApp messaging through the AWS Social Messaging platform. It also includes the necessary IAM permissions, SNS configuration, and Meta app setup for integration with CampaignHQ.

---

## 1. Setting Up a WhatsApp Phone Number

> **Note:** If you already have a WhatsApp number connected, you may skip this section.

### Steps:

1. Navigate to **AWS Console** → **Social Messaging** → **WhatsApp Business Accounts**.
2. Click **"Add WhatsApp phone number"** (top-right corner).
3. On the new page, click **"Launch Facebook portal"**.
4. A Facebook embedded flow popup will open. Follow the instructions to:
   - Create or choose a Meta Business Account.
   - Verify the phone number (**must not be already connected to WhatsApp**).
   - Optionally, you may use a Meta-provided US phone number.
5. After completing the flow, return to the AWS Console and click **"Add phone number"**.

### Details to Note for CampaignHQ Integration:

- **Meta Business Account ID** (e.g., `576061365581481`)
- **Phone Number** (e.g., `+1 555-704-7011`)
- **Phone Number ID** (e.g., `phone-number-id-ae5d420295b14bbb9b5f6a23865503a4`)

---

## 2. Creating an IAM User for WhatsApp Messaging

### A. Create a Custom IAM Policy

1. Go to **IAM** → **Policies** → **Create policy**.
2. Select the **JSON** tab and paste the following policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "CampaignHQ",
      "Effect": "Allow",
      "Action": ["social-messaging:SendWhatsAppMessage", "social-messaging:ListLinkedWhatsAppBusinessAccounts"],
      "Resource": "arn:aws:social-messaging:*:{{aws_account_id}}:phone-number-id/*"
    }
  ]
}
```

> Replace `{{aws_account_id}}` with your actual AWS Account ID (visible in the top-right corner of the AWS Console).

3. Click **Next**, give the policy a name (e.g., `CampaignHQWhatsAppPolicy`), and create it.

### B. Create the IAM User

1. Go to **IAM** → **Users** → **Add user**.
2. Enter a username (e.g., `campaignhq-whatsapp`).
3. Uncheck **AWS Management Console access**.
4. Proceed to **Permissions** → **Attach policies directly** → Select the policy created above.
5. Complete the user creation process.

### C. Generate Access Keys

1. Open the newly created user and go to the **Security credentials** tab.
2. Under **Access keys**, click **Create access key**.
3. For use case, select **Third-party service**, then click **Next**.
4. Optionally add a description → Click **Create access key**.
5. Save the **Access Key ID** and **Secret Access Key** securely and share them with the CampaignHQ team. You can also download them as a CSV file.

---

## 3. Configuring SNS for WhatsApp Notifications

### A. Create an SNS Topic

1. Go to **Simple Notification Service** → **Topics**.
2. Click **Create topic**.
3. Name it `CampaignWhatsappHQTopic` and leave all other settings as default.

### B. Create a Subscription

1. Open the newly created topic.
2. Go to the **Subscriptions** tab → Click **Create subscription**.
3. Use the following values:
   - **Protocol**: HTTPS  
   - **Endpoint**: `https://api.campaignhq.co/api/webhooks/aws_whatsapp`

### C. Connect the SNS Topic in AWS Social Messaging

1. Copy the **Topic ARN** (e.g., `arn:aws:sns:{region}:{aws_account_id}:CampaignWhatsappHQTopic`).
2. Go to **Social Messaging** → Select your **Business Account** → **Event destination** tab.
3. Click **Create event destination**:
   - Enable **Message and event publishing**.
   - Choose **Amazon SNS** as destination type.
   - Paste the **Topic ARN**.
   - Save the changes.
  
![Event Destination Setup](https://drive.google.com/uc?export=view&id=1I-2zAo7i4oS5NAPcL2uombOXLKYagnzI)

---

## 4. Meta App Setup for Template Management

To enable CampaignHQ to manage WhatsApp message templates through their platform, a Meta app needs to be linked to your business account.

### Requirements:

- Admin access to your **Meta Business Account**
- Admin access to your **Meta Developer Account**

A representative from CampaignHQ will assist and guide you through this setup. You can schedule the call here - [Schedule Call](https://www.campaignhq.co/schedule-demo/)
