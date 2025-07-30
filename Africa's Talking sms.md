# Africa's Talking SMS Sender (PHP)

This script sends an SMS using [Africa's Talking API](https://africastalking.com/docs/sms/send).

---

## ✅ Requirements

- PHP with cURL enabled
- Africa's Talking account
- Approved sender ID or use `AFRICASTKNG` for sandbox

---

## 🧾 Usage

### 1. Replace Credentials
Edit the following placeholders with your actual credentials:

```php
$username = 'your_username'; // Africa's Talking username
$apiKey   = 'your_api_key';  // Africa's Talking API key
$sender   = '+254711XXXYYY'; // Approved sender ID or shortcode
$recipients = '+254711XXXYYY'; // Recipient phone number in international format
$message  = 'Hello, this is a test message from Africa\'s Talking!';
