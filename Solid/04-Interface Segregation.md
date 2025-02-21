**Interface Segregation Principle (ISP)** არის SOLID პრინციპების ერთ-ერთ მნიშვნელოვანი ნაწილი, რომელიც მიზნად ისახავს **ინტერფეისების სწორად დაყოფას, რათა კლასი არ იყოს იძულებული იმპლემენტაცია გაუკეთოს მეთოდებს, რომლებიც არ სჭირდება**

---
## 1. Interface Segregation პრობლემა:

მოდით, განვიხილოთ მაგალითი, სადაც ISP პრინციპი დაირღვევა.

**პრობლემა: დიდი, ზედმეტად ფართო ინტერფეისი** (*ზედმეტი მეთოდებით, თუ კლასი ამ ინტერფეისის იმპლემენტაციას შეეცდება ასევე დაირღვევა Single Responsibility Principle იც*).

დავუშვათ, რომ ვქმნით ონლაიონ მაღაზიას, სადაც გვაქვს სხვადასხვა სახის გადახდის მეთოდები (Paypal, Stipe, Bank Transfer და ა.შ). ჩვენ ვქმნით ერთიან ინტერფეისს ყველა გადახდის მეთოდის მხარდასაჭერად:

```php
interface PaymentGateway {
    public function processCreditCardPayment(float $amount): void;
    public function processBankTransfer(float $amount): void;
    public function processCryptoPayment(float $amount): void;
}
```

ახლა, როდესაც გამოვიყენებთ ამ ინტერფეისს სხვადასხვა გადახდის მეთოდისთვის, შეიძლება პრობლემა წარმოიშვას:

```php
class PayPalPayment implements PaymentGateway {
    public function processCreditCardPayment(float $amount): void {
        echo "Processing credit card payment via PayPal: $amount USD\n";
    }

    public function processBankTransfer(float $amount): void {
        throw new Exception("PayPal does not support bank transfers!");
    }

    public function processCryptoPayment(float $amount): void {
        throw new Exception("PayPal does not support crypto payments!");
    }
}
```

**🚨 პრობლემა:**

Paypal-ს არ აქვს ბანკის გადარიცხვა ან კრიპტო გადახდების მხარდაჭერა, მაგრამ მაინც უნდა **დავამატოთი ეს მეთოდები PaymentGateway ინტერფეისის იმპლემენტაციის გამო.** შედეგად, ჩვენ **იძულებულები ვართ** იმპლემენტაცია გავუკეთოთ იმ მეთოდებს, რომლებიც არაფერში გვჭირდება, ან უბრალოდ დავაბრუნოთ შეცდომა.

ეს არღვევს **Interface Segregation Principle** -ს, რადგან კლიენტები (კლასები, რომლებიც ამ ინტეფეისის იმპლემენტაციას აკეთებენ) **იყენებენ ზედმეტად დიდ ინტერფეისს, რომელსაც რეალურად ყველა ფუნციონალი არ სჭირდება.**

---
## 2. interface Segregation-ის სწორი გამოყენება

სწორი მიდგომა იქნება **ინტერფეისების დაყოფა უფრო მცირე და სპეციფიურ ნაწილებად**, რათა თითოეულ გადახის მეთოდს მხოლოდ იმ მეთოდების იმპლემენტაცია მოუწიოს, რომელიც მას ესაჭიროება.
```php
interface CreditCardPayment {
    public function processCreditCardPayment(float $amount): void;
}

interface BankTransferPayment {
    public function processBankTransfer(float $amount): void;
}

interface CryptoPayment {
    public function processCryptoPayment(float $amount): void;
}
```

ახლა, კონკრეტულ გადახდის მეთოდებს მხოლოდ შესაბამისი ინტერფეისების იმპლემენტაცია მოუწევთ:
```php
class PayPalPayment implements CreditCardPayment {
    public function processCreditCardPayment(float $amount): void {
        echo "Processing credit card payment via PayPal: $amount USD\n";
    }
}

class BankTransferPayment implements BankTransferPayment {
    public function processBankTransfer(float $amount): void {
        echo "Processing bank transfer payment: $amount USD\n";
    }
}

class CryptoGateway implements CryptoPayment {
    public function processCryptoPayment(float $amount): void {
        echo "Processing crypto payment: $amount USD\n";
    }
}
```

---
## 3. Interface Segregation - ის სარგებელი

1. **მეტი მოქნილობა:**  კლასებს შეუძლიეათ მხოლოდ საჭირო ინტერფეისების იმპლემენტაცია, რაც კოდს უფრო სუფთას და ორგანიზებულს ხდის.
2. **მარტივი ტესტირება და ჩასწორება:** თუ ინტერფეისები სწორედ არის დაყოფილი, ცვლილებების შეტანა ან ახალი ფუნქციონალის დამატება ბევრად მარტივია.
3. **შემცირებული კავშირის სიმჭიდროვე (coupling):** კოდში შემცირებულია ზედმედი დამოკიდებულება და კლასები უფრო დამოუკიდებლები ხდებიან.
---

**🚀 დასკვნა**

Interface Segregation Principle (ISP) aris SOLID -ის ერთ-ერთი კრიტიკულად მნიშვნელოვანი პრინციპი, რომელიც ხელს უწყობს **ინტერფეისების სწორად დაყიფას, რომ კლიენტის კოდი არ იყოს იძულებული იმპლემენტაცია გაუკეთოს იმ მეთოდებს, რომლებიც მას არ სჭირდება.**

თუ ინტერფეისიები ძალიან დიდია და მოიცავს მეთოდებს, რომლებიც ყველა კლასისთვის რომელიც ამ ინტერფეისის იმპლემენტაციას ახდენს რელევანტური არ არის, მაში ეს ISP პრინციპის დარჩვევაა.

**სწორი მიდგომაა, ინტერფეისები დავყოთ მცირე, სპეციფიკურ ჯგუფებას, რათა თითოეულმა კლასმა იმპლემენტაცია გაუკეთოს მხოლოდ იმ ფუნციებს, რომლებიც მას სჭირდება.** 