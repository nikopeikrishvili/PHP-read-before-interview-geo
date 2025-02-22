**Dependency Inversion Principle (DIP)** არის SOLID პრინციპების ერთ-ერთი მთავარი კონცეფცია, რტომელიც კოდის მოქნილობას და მოდულარობას უზრუნველყოფს.

ეს პრინციპიუ გვკარნახობს ორ ძირითად წესს:
1. **მაღალი დონის მოდულები (High-Level Modules) არ უნდა იყვნენ დამოკიდებული დაბალი დონის მოდულებზე (Low-Level Modules). ორივე უნდა იყოს დამოკუდებული აბსტრაქციაზე.**
2. **აბსტრაციების არ უნდა იყოს დამოკიდებული დეტალებზე, არამედ დეტალები უნდა იყოს დამოკიდებული აბსტრაქციებზე.

პირველ რიგში, **არ უნდა გვქონდეს პირდაპირი დამოკიდებულება კონკრეტულ კლასებზე,** არამედ ისინი უნდა გადავცეთ ინტერფეისების და აბსტრაქციების გამოყენებით.

---
## 1. DIP -ის დარღვევის მაგალითი

დავუშვათ,რომ გვაქვს კლასი, რომელიც **ელ-ფოსტის გაგზავნას** უზრუნველყოფს მომხმარებლის რეგისტრაციისას.
```php
class EmailService {
    public function sendEmail(string $to, string $message) {
        echo "Sending email to $to: $message\n";
    }
}

class UserService {
    private EmailService $emailService;

    public function __construct() {
        $this->emailService = new EmailService(); // ❌ DIP დაირღვა!
    }

    public function registerUser(string $email) {
        echo "User registered: $email\n";
        $this->emailService->sendEmail($email, "Welcome to our platform!");
    }
}

// გამოყენება
$userService = new UserService();
$userService->registerUser("user@example.com");
```

⚠️ **პრობლემები:**

- **მჭიდრო კავშირი (Tight Coupling):** UserService **მჭიდროდ არის მიბმული** EmailService-ზე, რაც ართულებს სხვა ტიპის შეტყობინებების (SMS, Push Notification) დამატებას.
- **ცვლილებების სირთულე:** თუ EmailService-ში რამე შეიცვლება, ეს გახდებას მოახდენს UserService-ზეც.
- **რთული ტესტირება:** Unit Testing დროს რთულია UserService-ის ტესტირება EmailService კლასის გარეშე.

## 2. DIP-ის დაცვა (Dependency Injection-ის გამოყენებით)

**სწორი მიდგომაა** UserService არ იყოს დამოკიდებული კონკრეტულ EmailService კლასზე, არამედ იმუშაოს **ინტერფეისის (Interface) მეშვეობით.**

```php
interface NotificationService {
    public function send(string $to, string $message);
}
```

ახლა, **EmailService** იმპლემენტაციას გაუკეთებს ამ ინტერფეისს:

```php
class EmailService implements NotificationService {
    public function send(string $to, string $message) {
        echo "Sending email to $to: $message\n";
    }
}
```

UserService აღარ იქნება კონკრეტულ EmailService-ზე დამოკიდებული, არამედ **აბსტრაქციაზე (NotificationService).**

```php
class UserService {
    private NotificationService $notificationService;

    public function __construct(NotificationService $notificationService) {
        $this->notificationService = $notificationService;
    }

    public function registerUser(string $email) {
        echo "User registered: $email\n";
        $this->notificationService->send($email, "Welcome to our platform!");
    }
}
```

ახლა შეგვიძლია შევქმნათ სხვადასხვა შეტყობინებების სერვისები, როგორიცაა **SMS** და **Push Notification:**

```php
class SmsService implements NotificationService {
    public function send(string $to, string $message) {
        echo "Sending SMS to $to: $message\n";
    }
}
```

---
## 3. DIP - ის სარგებელი

**✅ Loose Coupling - მოხსნილია მჭიდრო კავშირები**

კოდი უფრო მოქნილია, რადგანაც UserService არ არის მიბმული კონკრეტულ EmailService - ზე.

```php
// Email შეტყობინება
$emailService = new EmailService();
$userService = new UserService($emailService);
$userService->registerUser("user@example.com");

// SMS შეტყობინება
$smsService = new SmsService();
$userService = new UserService($smsService);
$userService->registerUser("user@example.com");
```



**✅ მარტივი გაფართოებადობა**

თუ მოგვიანებით დაგვჭირდა **Push Notification** ის დამატება უბრალოდ შევქმნით ახალ კლასს რომელიც იქნება NotificationService ინტერფეისის იმპლემენტაცია.

```php
class PushNotificationService implements NotificationService {
    public function send(string $to, string $message) {
        echo "Sending push notification to $to: $message\n";
    }
}
```
UserService ში არანაირი ცვლილება არ დაგვჭირდება!


**✅ მარტივი ტესტირება**

შეგვიძლია შევქმნათ **Mock ობიექტები** ტესტირებისთვის.

```php
class MockNotificationService implements NotificationService {
    public function send(string $to, string $message) {
        echo "[Mock] Notification sent to $to: $message\n";
    }
}

$mockService = new MockNotificationService();
$userService = new UserService($mockService);
$userService->registerUser("test@example.com");
```

---

## 4.  DIP და Dependency Injection (DI)

**Dependency Inversion** ხშირად მუშაობს **Dependency Injection**-თან, რადგან DI-ის საშუალებით, დამოკიდებულებები კლასს კონსტრუქტორის ან სეტერით მიეწოდება.

ბევრ PHP **Framework** ში (მაგალითად, **Laravel,  Symfony**) **Service Contrainer** ეხმარება DIP-ის ავტომატიზაციას:

```php
// Laravel-ის მაგალითი
app()->bind(NotificationService::class, EmailService::class);

$userService = app(UserService::class);
$userService->registerUser("user@example.com");
```

---

**🚀 დასკვნა**

**✅ Dependency Inversion Principle (DIP) არის კარგი პრაქტიკა, რადგან:**

1. **ამცირებს კლასებს შორის მჭიდრო კავშირს (Tight Coupling)** და ხდის კოდს უფრო მოქნილს.
2. **მარტივად შეგვიძლია ჩავანაცვლოთ დამოკიდებულებები** - შეგვიძლია გამოვიყენოთ SmsService, EmailService, PushNotificationService - UserService ის ცვლილების გარეშე.
3. **უკეთესი ტესტირება:** მარტივია Mock ობიექტების გამოყენება.
4. **კოდი ხდება უფრო სუფთა და სტრუქტურირებული.**

**როდის გამოვიყენოთ:**

- როცა კლასი დამოკიდებულია კონკრეტულ სერვისზე დაგვინდა ის უფრო მოქნილი გავხადოთ.
- როცა გვჭირდება კოდის ადვილად გაფართოება ახალი ფუნქციონალით.
- როცა გვინდა უკეთესი ტესტირება და მოდულარობა.