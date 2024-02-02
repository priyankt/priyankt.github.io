---
layout: post
title:  Sending SMS in India via AWS SNS
tag: tech
category: tech
date: 2023-11-10 11:00:00 +0530
---
At KOFFi, our mission is to help small and medium businesses to earn better returns on their idle cash as compared to bank FD (fixed deposits). We do that by simplifying the process of investing in the debt mutual funds that pay higher interest in the short term. Don’t believe? Try our [KOFFi returns calculator](https://app.koffi.com/calculator) to compare KOFFi returns with FD. Why am I promoting KOFFi in an AWS post? Because the need for sending SMS started with SEBI mandating KOFFi to verify the investors phone number & email during signup. Since everyone in India is now pretty familiar with the system of sending OTP via SMS to their phone numbers, we decided to verify users phone numbers via OTP. Our entire stack is on AWS, so it made sense that we send SMS too via AWS.

## Distributed Ledger Technology (DLT)

In an effort to control promotional spam, SMS fraud, and unsolicited messaging, the Telephone Regulatory Authority of India (TRAI) has enforced new guidelines for SMS marketing. It *mandates the whitelisting of all SMS templates and registration of your company's entities, Sender IDs (Headers), and user consent* in a centralized DLT portal provided by Indian mobile operators.

India’s telecom regulatory body TRAI decided to implement DLT to control promotional spam, fraud, and unsolicited messaging. If you want to send SMS to Indian number via domestic route, you have to register with DLT. Sending SMS via international route is unaffected. Keep in mind that sending SMS via International route costs 20x more than local route so registering with DLT always makes sense. You can learn more about DLT on [TRAI’s website](https://docs.webengage.com/docs/trai-sms-dlt-regulations-india).

### How do we register with DLT?
- There are multiple third-party providers that facilitate DLT registration. BSNL, JIO, smartping from Videocon, Airtel, etc. After inquiring a bit, we received positive reviews for smartping and decided to go with it. There is a one time cost of around ₹ 5000.
- Once you register, you have to apply for Sender ID. It is a 6 char code that appears to the receiver of your SMS. For example, SBICRM, URBNCP, ICIOTP, etc. Important: We registered KOFFi as Sender ID but AWS mandates that the Sender ID has to be 6 char so we also registered KOFFFi.
- You have to create a template of SMS that you want to send and get it approved. There are few rules around SMS templates as well. Important: If you are sending OTP, make sure you select category as Service Implicit else carrier will block your SMS if the recipient is registered with DND.

### How to integrate DLT with AWS?
- Once you have the Entity ID, Sender ID & Template ID, you need to raise a support ticket with AWS to associate your Sender ID with your account. Additionally, you need to raise a ticket to increase your spending limit & move to production.
- Once the Sender ID is registered, you need to include the Sender ID, SMSType, EntityId and TemplateId as MessageAttributes while sending the SMS. More info can be found on [AWS docs website](https://docs.aws.amazon.com/sns/latest/dg/sms_publish-to-phone.html). Following is the python code for reference,

```python
client.publish( 
  PhoneNumber=self.mobile,
  Message=msg,
  MessageAttributes={
    'AWS.SNS.SMS.SenderID': {'DataType':'String','StringValue': SMS_SENDER_ID},
    'AWS.SNS.SMS.SMSType': {'DataType': 'String', 'StringValue': 'Transactional'},
    'AWS.MM.SMS.EntityId': {'DataType': 'String', 'StringValue': SMS_ENTITY_ID},
    'AWS.MM.SMS.TemplateId': {'DataType': 'String', 'StringValue': SMS_TEMPLATE_ID}
  }
)
```

Make sure you integrate CloudWatch to log successful and failed SMS else you won't be able to tell the reason behind failure. The CloudWatch groups are automatically created for success & failed SMS. It might take a while for the groups to appear in CloudWatch (could take up to 2-3 days).

Refer below image for the log groups that will be created.
![CloudWatch log groups](/assets/cloudwatch-logs.png){: w="700" h="400" }
CloudWatch log groups for successful & failed SMS_
