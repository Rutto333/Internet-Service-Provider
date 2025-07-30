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



# Sending SMS using Africa's Talking API

This PHP script demonstrates how to send an SMS using the Africa's Talking API.

## PHP Code

```php
<?php
function sendSms($username, $apiKey, $sender, $recipients, $message) {
    // Set the URL for the Africa's Talking SMS API
    $url = 'https://api.africastalking.com/version1/messaging';

    // Prepare the POST fields
    $data = [
        'username' => $username,
        'to'       => $recipients,
        'message'  => $message,
        'from'  => $sender
    ];

    // Initialize cURL
    $ch = curl_init();

    // Set cURL options
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($data));
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        "apiKey: $apiKey",
        'Content-Type: application/x-www-form-urlencoded'
    ]);

    // Execute cURL request and get the response
    $response = curl_exec($ch);

    // Check for cURL errors
    if (curl_errno($ch)) {
        $error = 'cURL error: ' . curl_error($ch);
        curl_close($ch);
        return ['success' => false, 'error' => $error];
    }

    // Close the cURL session
    curl_close($ch);

    // Decode and return the response
    $responseData = json_decode($response, true);

    return ['success' => true, 'response' => $responseData];
}

// Example usage:
$username = 'your_username'; // Replace with your Africa's Talking username
$sender = '+254711XXXYYY';   // Replace with the sender name or shortcode
$apiKey   = 'your_api_key';  // Replace with your Africa's Talking API key
$recipients = '+254711XXXYYY';  // Replace with the recipient's phone number (including country code)
$message    = 'Hello, this is a test message from Africa's Talking!';

$result = sendSms($username, $apiKey, $sender, $recipients, $message);

if ($result['success']) {
    echo "SMS sent successfully!";
    print_r($result['response']);
} else {
    echo "Error sending SMS: " . $result['error'];
}

/* Sample Response
{
    "SMSMessageData": {
        "Message": "Sent to 1/1 Total Cost: KES 0.8000",
        "Recipients": [{
            "statusCode": 101,
            "number": "+254711XXXYYY",
            "status": "Success",
            "cost": "KES 0.8000",
            "messageId": "ATPid_SampleTxnId123"
        }]
    }
}
*/
?>
```

## Explanation

- **Function `sendSms`**: This function takes five parameters: `username`, `apiKey`, `sender`, `recipients`, and `message`. It uses cURL to send an HTTP POST request to the Africa's Talking API.

- **cURL Setup**: The script initializes a cURL session, sets the necessary options, including the URL, POST fields, and HTTP headers with the API key.

- **Error Handling**: It checks for cURL errors and returns an error message if any are found.

- **Response Handling**: The response from the API is decoded from JSON and returned as part of an associative array indicating success or failure.

- **Example Usage**: The script includes an example of how to use the `sendSms` function with placeholders for the username, API key, sender, recipient, and message.

- **Sample Response**: A sample response from the API is provided, showing the structure of the data returned upon successfully sending an SMS.

