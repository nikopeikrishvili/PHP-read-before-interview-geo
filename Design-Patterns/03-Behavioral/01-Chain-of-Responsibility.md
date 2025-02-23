
**Chain of Responsibility (CoR)** არის ქცევითი ტიპის დიზაინის პატერნი, რომელიც საშუალებას გვაძლევს შევქმნათ მოთხოვნების გადამუშავების ჯაჭვი, სადაც თითიეული ჯაჭვის ელემენტი გადაწყვეტს უნდა დაამუშავოს მოთხოვნა თუ უნდა გადააგფზავნოს იგი შემდეგ ელემენტზე. ეს პატერნი ხშირად გამოიყენება ლოგირების, ავტორიზაციის, ვალიდაციის, მოთხოვნის დამუშავების და სხვა შემთხვევებშიც.

#### როდის გამოვიყენოთ?
- როდესაც გვსურს მოთხოვნის დამუშავება ერთზე მეტ კომპონენტს შეეძლოს.
- როდესაც არ იცით წინასწარ, კონკრეტულად რომელ კომპონენტს მოუწევს მოთხოვნის დამუშავება.
- როდესაც გსურთღ კოდის მოქნილობა და გაფართოებადობა.

---

#### Chain of Responsibility ის იმპლემენტაცია

მოდით, შევქმნათ მოთხოვნის გადამუშავების ჯაჭვი, რომელიც მოიცავს სხვადასხვა **Handler** კლასებს. წარმოვიდგინოთ, რომ გვაქვს მომხმარებელთა ავტორიზაციის და დაშვების კონტოლის სისტემა.

##### 1. აბსტრაქტული Handler კლასი

```php
<?php
abstract class Handler
{
    protected ?Handler $nextHandler = null;

    public function setNext(Handler $handler): Handler
    {
        $this->nextHandler = $handler;
        return $handler;
    }

    public function handle(string $request): ?string
    {
        if ($this->nextHandler) {
            return $this->nextHandler->handle($request);
        }
        return null;
    }
}
```

ეს კლასი განსაზღვრავს **setNext()** მეთოდს, რომელიც საშუალებას აძლევს ჰენდლერებს ჯაჭვში ჩაებან.

---

##### 2. კონკრეტული ჰენდლერი

**🔸 ავტორიზაციის შემოწმება**
```php
class AuthenticationHandler extends Handler
{
    public function handle(string $request): ?string
    {
        if ($request !== "authenticated_user") {
            return "⛔ Access denied: User not authenticated.";
        }
        return parent::handle($request);
    }
}
```

**🔸 როლის შემოწმება**
```php
class RoleCheckHandler extends Handler
{
    public function handle(string $request): ?string
    {
        if ($request !== "admin") {
            return "⚠️ Access denied: User does not have admin rights.";
        }
        return parent::handle($request);
    }
}
```

**🔸 საბოლოო დამუშავება**

```php
class FinalHandler extends Handler
{
    public function handle(string $request): ?string
    {
        return "✅ Access granted: Welcome, Admin!";
    }
}
```


##### 3. ჯაჭვის შექმნა და ტესტირება

```php
$authHandler = new AuthenticationHandler();
$roleHandler = new RoleCheckHandler();
$finalHandler = new FinalHandler();

$authHandler->setNext($roleHandler)->setNext($finalHandler);

// ვამოწმებთ ავტორიზებულ მომხმარებელს, რომელიც **admin** არ არის
echo $authHandler->handle("authenticated_user"); 
// Output: ⚠️ Access denied: User does not have admin rights.

echo "\n";

// ვამოწმებთ **admin** მომხმარებელს
echo $authHandler->handle("admin"); 
// Output: ✅ Access granted: Welcome, Admin!
```

---

#### 🚀 დასკვნა

##### უპირატესობები:
- **მოდულარობა** - თითოეული ჰენდლერი დამოუკიდებელი და მარტივად დამატებადია.
- **მოქნილობა** - ჯაჭვის სტრუქტურა ადვილად შეიფვლება ან გაფართოვდება.
- **კოდის სისუფთავე** - თავიდან ვირიდებთ გრძელ if-else სტრუქტურებს.
- **მარტივი ტესტირება** - თითოეული ჯაჭვი ცალცალკე შეიძლება გაიტესტოს.

##### სად შეიძლება გამოვიყენოთ
- ავტორიზაციის და დაშვების კონტოლი.
- ლოგირების სისტემები.
- მოთხოვნების პრე-პროცესინგი API - ებში.