
**Single Responsibility Principle (SRP)** არის SOLID პრინციპების პირველი და ერთ-ერთი ყველაზე მნიშნველოვანი ნაწილი. მისი მთავარი იდეაა:

	კლასს უნდა ჰქონდეს მხოლოდ ერთი პასუხისმგებლოვა და უნდა იყოს მხოლოდ ერთი მიზეზი მის შესაცვლელად

ეს ნიშნავს რომ **ერთი კლასი უნდა შეიცავდეს მხოლოდ ერთ საქმეს და არ უნდა შეიცავდეს სხვადასხვა პასუხიმგებლობებს.

---

## 1. რატომ არის SRP მნიშვნელოვანი

- **მარტივი ცვლილება:** თუ კლასს აქვს მხოლოდ ერთი პასუხისმგებლობა, მასში ცვლილებების შეტრანა გავლენას არ იქონიებს სხვა ფუნქციონალზე.
- **მოდულარობა:** კოდი უფრო ლოგიკურ ნაწილებად იყოფა.
- **ტესტირება:** SRP-ის დაცვა **მარტივს ხდის Unit Testing-ს**
- **რეფაქტორინგი და გაფართოება:** ცვლილებები მხოლოდ კონკრეტულ ადგილას ხდება, რაც ხელს უწყობს კოდის მენეჯმენტს.
---
## 2. SRP-ის დარღვევის მაგალითი

მოვიყვანოთ კლასის მაგალითი, რომელიც არღვევს **Single Reponsibility Principle**-ს.

```php
class User {
    public function register(string $username, string $password) {
        // რეგისტრაციის ლოგიკა
        echo "User registered: $username\n";

        // პაროლის დაშიფვრა
        $hashedPassword = password_hash($password, PASSWORD_BCRYPT);

        // მონაცემთა ბაზაში შენახვა
        $this->saveToDatabase($username, $hashedPassword);

        // ელ-ფოსტის გაგზავნა
        $this->sendEmail($username);
    }

    private function saveToDatabase(string $username, string $hashedPassword) {
        echo "Saving $username to database...\n";
    }

    private function sendEmail(string $username) {
        echo "Sending welcome email to $username...\n";
    }
}
```

🚨 **პრობლემები:**
- **მრავალი პასუხისმგებლობა (Multiple Responsibilities):
	- რეგისტრაციის ლოგიკა.
	- პაროლის დაშიფვრა.
	- მონაცემთა ბაზაში შენახვა.
	- ელ-ფოსტის გაგზავნა.

თუ მოგვიწევს **ელ-ფოსტის გაგზავნის ლოგიკის შეცვლა,** მოგვიწევს **User კლასის რედაქტირება,** რაც არასწორია, რადგან **მომხმარებლის რეგისტრაციას არ უნდა ქონდეს კავშირი ელ-ფოსტის გაგზავნასთან.**

---

## 3. SRP-ის დაცვა- სწორი მიდგომა
დავყოთ კლასი ისე, რომ **ყველა პასუხისმგებლობას თავისი კლასი ჰქონდეს**.

**მომხმარებლის რეგისტრაციის კლასი:**
```php
class UserService {
    private PasswordHasher $passwordHasher;
    private UserRepository $userRepository;
    private EmailService $emailService;

    public function __construct(PasswordHasher $passwordHasher, UserRepository $userRepository, EmailService $emailService) {
        $this->passwordHasher = $passwordHasher;
        $this->userRepository = $userRepository;
        $this->emailService = $emailService;
    }

    public function register(string $username, string $password) {
        echo "User registered: $username\n";
        
        // პაროლის დაშიფვრა
        $hashedPassword = $this->passwordHasher->hash($password);

        // მონაცემთა ბაზაში შენახვა
        $this->userRepository->save($username, $hashedPassword);

        // ელ-ფოსტის გაგზავნა
        $this->emailService->sendWelcomeEmail($username);
    }
}
```

**პაროლის დამუშავების კლასი:**
```php
class PasswordHasher {
    public function hash(string $password): string {
        return password_hash($password, PASSWORD_BCRYPT);
    }
}
```

**მომხმარებლის მონაცემთა ბაზაში შენახვა (Repository Pattern):**
```php
class UserRepository {
    public function save(string $username, string $hashedPassword) {
        echo "Saving $username to database...\n";
    }
}
```

**ელ-ფოსტის გაგზავნის სერვისი:**
```php
class EmailService {
    public function sendWelcomeEmail(string $username) {
        echo "Sending welcome email to $username...\n";
    }
}
```

**✅ ამ ჩასწორებით ჩვენ მოვხსენოთ SRP დარღვევები**

- **UserService** მხოლოდ მომხმარებლის რეგისტრაციას უზრუნველყოფს.
- **PasswordHasher** მხოლოდ პაროლის დაშიფვრაზეა პასუხიმგებელი.
- **UserRepository** მონაცემთა ბაზაში ინფორმაციის შენახვას უზრუნველყოფს.
- **EmailService** მხოლოდ ელ-ფოსტის გაგზავნის ფუნციას ითავსებს.

---

**🚀 დასკვნა**

**✅ Single Responsibility Principle (SRP) არის კარგი პრაქტიკა, რადგან:**
- ამცირებს **კოდის სირთულეს** დახდის მას უფრო ორგანიზებულს.
- ხელს უწყობს უკეთეს და გამარტივებულ **Unit Testing**-ს.
- ამცირებს **ცვლილებების გავლენას (Ripple Effect)** მთელ პროექტში.
- უფრო მარტივცს ხდის **კოდის მართვას და გაფართოებას.

**🔥 როდის გამოვიყენოთ?**
- როცა **კლასს ბევრი ფუნქცია აქვს** და **განსხვავებული პასუხისმგებლობები ერთ კლასშია თავმოყრილი.
- როცა ცვლილებების შეტანისას **მიუხედავად ერთი ფუნქციის ცვლილებისა, სხვა ფუნქციებიც იძულებით იცვლება.**
- როდესაც კოდი **გახდება რთულად გასაგები** ან **სხვადასხვა დეველოპერები ერთსა და იმავე კლასს განსხვავებული მიზნით იყენებენ.** 