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


<?php

/**
 * Send an SMS using Africa's Talking API.
 *
 * @param string $username  Your Africa's Talking username (e.g., 'sandbox' for testing)
 * @param string $apiKey    Your Africa's Talking API key
 * @param string $sender    Sender ID or shortcode (e.g., 'AFRICASTKNG' or approved sender ID)
 * @param string $recipients Comma-separated phone numbers in international format (e.g., '+2547XXXXXXXX')
 * @param string $message   The message text to send
 * @return array            Result with 'success' status and API response or error
 */
function sendSms($username, $apiKey, $sender, $recipients, $message)
{
    // Define the API URL (change to sandbox URL if using sandbox)
    $url = 'https://api.africastalking.com/version1/messaging';

    // Build the POST payload
    $data = [
        'username' => $username,
        'to'       => $recipients,
        'message'  => $message,
        'from'     => $sender
    ];

    // Initialize cURL
    $ch = curl_init();
    curl_setopt_array($ch, [
        CURLOPT_URL            => $url,
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_POST           => true,
        CURLOPT_POSTFIELDS     => http_build_query($data),
        CURLOPT_HTTPHEADER     => [
            "apiKey: $apiKey",
            'Content-Type: application/x-www-form-urlencoded'
        ]
    ]);

    // Execute the request
    $response = curl_exec($ch);

    // Handle cURL errors
    if (curl_errno($ch)) {
        $error = 'cURL error: ' . curl_error($ch);
        curl_close($ch);
        return ['success' => false, 'error' => $error];
    }

    curl_close($ch);

    // Decode response
    $responseData = json_decode($response, true);

    return ['success' => true, 'response' => $responseData];
}

// ---------- Example Usage ----------

// Set your Africa's Talking credentials
$username   = 'sandbox'; // Use 'sandbox' for testing, or your live username
$apiKey     = 'your_api_key_here';
$sender     = 'AFRICASTKNG'; // Use 'AFRICASTKNG' for sandbox, or your approved sender ID
$recipients = '+254711XXXXXX'; // Replace with recipient's phone number in international format
$message    = 'Hello, this is a test SMS from Africa\'s Talking!';

// Send the SMS
$result = sendSms($username, $apiKey, $sender, $recipients, $message);

// Handle the result
if ($result['success']) {
    $response = $result['response'];
    $recipientsInfo = $response['SMSMessageData']['Recipients'] ?? [];

    if (count($recipientsInfo) > 0 && $recipientsInfo[0]['status'] === 'Success') {
        echo "✅ SMS sent to " . $recipientsInfo[0]['number'] . "\n";
        echo "📩 Message ID: " . $recipientsInfo[0]['messageId'] . "\n";
        echo "💰 Cost: " . $recipientsInfo[0]['cost'] . "\n";
    } else {
        echo "❌ SMS failed. Response:\n";
        print_r($response);
    }
} else {
    echo "🚫 Error sending SMS: " . $result['error'];
}
