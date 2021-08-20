# IPPanel SMS Php API SDK

This repository contains open source PHP client for `ippanel` api. Documentation can be found at: <http://docs.ippanel.com>.

## نصب

نصب با کامپوزر:

```bash
composer require jalallinux/ippanel-php-rest-sdk
```

اگر از کامپوزر استفاده نمیکنید. میتوانید پکیج را بصورت مستقیم دانلود کنید:

```bash
wget https://github.com/jalallinux/ippanel-php-rest-sdk/archive/master.zip
```

## نیازمندی‌ها
جهت استفاده از این پکیج به موارد زیر نیاز خواهید داشت:
- PHP >= 7.0
- ext-curl
- ext-json


## متدها و نحوه استفاده

### ایجاد کلاینت
‍‍‍```php
use IPPanel\Client;

$client = new Client('YOUR_API_KEY')
```

### دریافت موجودی اعتبار
```php
$credit = $client->getCredit();
```

### ارسال پیامک
```php
$originator = '5000012345'; // شماره فرستنده
$recipients = ['09123456789', '09111111111']; // شماره‌های گیرنده
$message = 'Hello world!'; // متن پیام

$bulkId = $client->send($originator, $recipients, $message);
```

### دریافت اطلاعات پیام
```php
$message = $client->getMessage($bulkId);

echo $message->status;
echo $message->cost;
echo $message->sentAt;
```

### دریافت وضعیت تحویل پیام
```php
[$statuses, $paginationInfo] = $client->fetchStatuses($bulkId);

foreach ($statuses as $status) {
    echo "Recipient: $status->recipient, Status: $status->status";
}

echo "Total: $paginationInfo->total";
```

### دریافت پیام‌های ورودی
```php
[$messages, $paginationInfo] = $client->fetchInbox();

foreach ($messages as $message) {
    echo "Received message $message->message from number $message->sender in line $message->number";
}
```

### ایجاد الگوی پیام‌های پرتکرار
```php
$pattern = $client->createPattern('Your otp is %code%.');

echo $pattern->code; // شناسه الگو
```

### ارسال پیام با استفاده از الگو
```php
$patternCode = '12eb1cbb'; // شناسه الگو
$originator = '5000012345'; // شماره فرستنده
$recipient = '09123456789'; // شماره گیرنده
$values = ['code' => 12345];

$bulkId = $client->sendPattern($patternCode, $originator, $recipient, $values);
```

### مدیریت خطا
```php
use IPPanel\Errors\Error;
use IPPanel\Errors\HttpException;

try{
    $bulkID = $client->send("9810001", ["98912xxxxx"], "ippanel is awesome");
} catch (Error $e) { // ippanel error
    var_dump($e->unwrap()); // بدنه خطای اصلی
    echo $e->getCode();

    if ($e->code() == ResponseCodes::ErrUnprocessableEntity) {
        echo "Unprocessable entity";
    }
} catch (HttpException $e) { // http error
    var_dump($e->getMessage()); // متن خطا
    echo $e->getCode();
}
``` 
