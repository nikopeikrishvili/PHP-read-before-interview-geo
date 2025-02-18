**📌 რას ნიშნავს Encapsulation?**

🔹 **Encapsulation (ინკაფსულაცია)** არის **ობიექტზე ორიენტირებული პროგრამირების (OOP)** ერთერთი მთავარი პრინციპი, რომელიც გულისხმობს **მონაცემების და მეთოდების დაცვას** გარე ჩარევებისგან.    
🔹 **ძირითადი იდეა -** კლასის შიგნით არსებული მონაცემები და მეთოდები **გარედან არ უნდა იყოს ხელმისაწვდომი, თუ ეს საჭირო არ არის.**    
🔹**ეს უზრუნველყოფს მონაცემების უსაფრთხოებას და კონტროლს.**    

---
## 1. როგორ მუშაობს Encapsulation?
**Encapsulation** მიიღწევა **წვდომის მოდიფიკატორებით (Access Modifiers),** რომელიც განსაზღვრავს, თუ როგორ შეიძლება წვდომა კლასის ცვლილებაზე და მეთოდებზე.

**PHP ში წვდომის მოდიფიქატორები:**

| **წვდომის მოდიფიქატორი** | **აღწერა**                                              |
| ------------------------ | ------------------------------------------------------- |
| **public**               | ხელმისაწვდომია ყველგან                                  |
| **protected**            | ხელმისაწვდომია მხოლოდ ამავე კლასში და შვილობილ კლასებში |
| **private**              | ხელმისაწვდომია მხოლოდ იმავე კლასში                      |

---

## 2. მაგალითი: Encapsulation PHP - ში

**📌 Public vs Private vs Protected**

```php
<?php

class User {
    public string $username; // ყველასთვის ხელმისაწვდომია
    protected string $email; // მხოლოდ ამ კლასში და შვილობილ კლასებში ხელმისაწვდომია
    private string $password; // მხოლოდ ამ კლასში ხელმისაწვდომია

    public function __construct(string $username, string $email, string $password) {
        $this->username = $username;
        $this->email = $email;
        $this->password = $password;
    }

    public function getEmail(): string {
        return $this->email; // დაშვებულია, რადგან ეს მეთოდი ამავე კლასშია
    }

    private function getPassword(): string {
        return $this->password; // დაშვებულია მხოლოდ ამავე კლასში
    }
}

$user = new User("Niko", "niko@example.com", "secret123");

echo $user->username . PHP_EOL; // Niko (public ველი)
echo $user->getEmail() . PHP_EOL; // niko@example.com (protected წვდომა შესაძლებელია ამ მეთოდით)

// ❌ შეცდომები:
// echo $user->email; // შეცდომა: protected
// echo $user->password; // შეცდომა: private
// echo $user->getPassword(); // შეცდომა: private

?>
```

✅ **აქ password private-ია, ამიტომ მისი წვდომა პირდაპირ შეუძლებელია, ხოლო email - protected-ია და მხოლოდ getEmail() მეთოდით შეგვიძლია მივწვდეთა რადგან ის piblic არის.**    
**✅ ეს გვაძლევს კონტროლს იმაზე, თუ როგორ და როდის შეიძლება მონაცემებზე წვდომა.**    

---
## 3. Encapsulation Getter და Setter მეთოდებით

Encapsulation-ის ერთერთი საუკეთესო პრაქტიკაა **getter და setter მეთოდების გამოყენება**

**📌 მაგალითი: Getter და Setter მეთოდები** 

```php
<?php

class BankAccount {
    private float $balance;

    public function __construct(float $initialBalance) {
        $this->balance = $initialBalance;
    }

    public function getBalance(): float {
        return $this->balance; // getter მეთოდი - კითხვისთვის
    }

    public function deposit(float $amount): void {
        if ($amount > 0) {
            $this->balance += $amount;
        }
    }

    public function withdraw(float $amount): void {
        if ($amount > 0 && $amount <= $this->balance) {
            $this->balance -= $amount;
        }
    }
}

$account = new BankAccount(100);
echo "Initial Balance: " . $account->getBalance() . PHP_EOL; // 100

$account->deposit(50);
echo "After Deposit: " . $account->getBalance() . PHP_EOL; // 150

$account->withdraw(30);
echo "After Withdrawal: " . $account->getBalance() . PHP_EOL; // 120

// ❌ შეცდომა: $account->balance = 1000; // არ შეიძლება, რადგან balance private-ია

?>
```

✅ **აქ balance არის private, ამიტომ მისი პირდაპირი შეცვლა შეუძლებელია, რაც უსაფრთხოების ერთ-ერთი კარგი პრაქტიკაა.**    
**✅ მხოლოდ deposit() და withdraw() მეთოდებით შეიძლება ბალანსის ცვლილება.    

---
## 4. Protected - მემკვიდრეობასთან ერთად (Inheritance + Encapsulation)

თუ გვსურს, რომ შვილობილმა კლასებმა შეძლონ მონაცემებზე წვდომა, **protected** მოდიფიქატორი გამოვიყენოთ.

**📌 მაგალითი:**

```php
<?php

class Person {
    protected string $name;

    public function __construct(string $name) {
        $this->name = $name;
    }
}

class Employee extends Person {
    private string $position;

    public function __construct(string $name, string $position) {
        parent::__construct($name);
        $this->position = $position;
    }

    public function getDetails(): string {
        return "Name: {$this->name}, Position: {$this->position}";
    }
}

$employee = new Employee("Gio", "Developer");
echo $employee->getDetails(); // Name: Gio, Position: Developer

// ❌ შეცდომა: echo $employee->name; // name არის protected, ამიტომ ვერ მივწვდებით
?>
```

✅ **protected $name შვილობილ კლასში (Employee) ხელმისაწვდომია, მაგრამ გარე სამყაროსთვის დახურულია.**    
**✅ ეს უზრუნველყოფს მონაცემების დახვას, მაგრამ ინარჩუნებს მემკვიდრეობის გადაცემის შესაძლებლობას.**    

---

## 5. Readonly (PHP 8.1+ -მონაცემების ერთჯერადი ინიციალიზაცია)

PHP 8.1-ში გაჩნდა **readonly** ატრიბუტი, რომელიც უზრუნველყოფს, რომ ცვლადი **მხოლოდ ერთხელ განისაზღვროს და აღარ შეიცვვალოს** .

**📌 მაგალითი:**

```php
<?php

class User {
    public function __construct(
        public readonly string $username
    ) {}
}

$user = new User("Niko");
echo $user->username . PHP_EOL; // Niko

// ❌ შეცდომა: $user->username = "Gio"; // Readonly თვისება შეცვლას არ იძლევა

?>
```

✅ **readonly ატრიბუტი, აკონტროლებს, რომ მონაცემი ინიციალიზაციის შემდეგ აღარ შეიცვალოს.**    
**✅ გამოიყენება სამივე წვდომის კონტროლთან public, protected, private, თუ წვდომის კონტროლს არ ვუწერთ წინ ავტომატურად იღებს public წვდომის კონტროლს.**    

---
## 6. Encapsulation - ის უპირატესობები

✅ **მონაცემების დაცვა (Data Hiding)** - კონტროლი თუ როგორ ჩანს პარამეტრები, მეთოდები შვილობილ კლასებში ან გარედან.    
✅ **მოდულარობა (Modularity)** - კლასების უკეთ ორგანიზება.    
✅ **კოდის მდგრადობა (Code Maintainability)** - კოდის მარტივად ჩასწორება და გაუმჯობესება.    
✅ **წვდომის კონტროლი (Access Control)** - კონკრეტული მონაცემები და მეთოდები ხელმისაწვდომია მხოლოდ საჭირო ადგილებში.    
✅ **ამომხმარებლისთვის მხოლოდ აუცილებელი ფუნქციონალის გამოტანა** - პროგრამისტს შეუძლია აკონტროლოს, თუ რომელი მონაცემები უნდა იყოს საჯარო და რომელი - დახურული.

---

**🚀 დასკვნა**

- **Encapsulation** არის **OOP-ის ერთ-ერთი მათავარი პრინციპი**, რომელიც **მ,ონაცემების დაცვას და კონტროლს უზრუნველყოფს.**    
- **Access Modifiers (public, protected, private) განსაზღვრავს, თუ სად შეიძლება წვდომა მონაცემებზე.**    
- **Getter და Setter მეთოდები მონაცემების უსაფრთხო მართვას უწყობენ ხელს.**    
- **Encapsulation ეხმარება კოდს, იყოს უფრო დახული, ორგანიზებული და ადვილად მართვადი.**    

✅ **თუ გინდა, რომ მონაცემები დაცული იყოს, და კოდი იყოს სტრუქტურირებული – გამოიყენე Encapsulation!** 🚀🔥