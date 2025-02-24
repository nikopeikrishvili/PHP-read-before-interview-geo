
**Strategy** პატერნი ერთ-ერთი ყველაზე გავრცელებული ქცევითი ტიპის დიზაინის პატერნია რომელიც საშუალებას გვაძლევს, დინამიურად შევცვალოთ ობიექტის ქცევა გაშვების დროს.

ეს პატერნი განსაკუთრებით გამოსადეგია, როცა გვჭირდება ერთი დაიგივე ალგორითმის სხვადასხვა რეალიზაცია, რომელთა გადართვა შესაძლებელია კოდის კუბლირების გარეშე.

---

### პრობლემის აღწერა
დავუშვათ, გვაქვს გადახდის სისტემა, რომელიც სხვადასხვა მეთოდს (PayPal, საბანკო ბარათი, კრიპტოვალუტა და ა.შ) იყენებს.
ტრადიციული მიდგომით, შეიძლება გადახდის ლოგიკა ჩავწეროთ დიდ if-else ბლოკში, რაც კოდის წაკითხვას და გაფართოებას გაართულებს.
```php
class PaymentProcessor {
    public function pay($method, $amount) {
        if ($method === 'paypal') {
            echo "გადახდა PayPal-ით: $amount USD";
        } elseif ($method === 'credit_card') {
            echo "გადახდა საბანკო ბარათით: $amount USD";
        } else {
            echo "უცნობი გადახდის მეთოდი!";
        }
    }
}

$processor = new PaymentProcessor();
$processor->pay('paypal', 100);
```

აქ პრობლემა ის არის, რომ ყოველი ახალი გადახდის მეთოდის დამატება კოდის ცვლილებას მოითხოვს, რაც არღვევს. **Open/Closed პრინციპს**.

---

#### Strategy პატერნის გამოყენება

ამ პრობლემის გადაჭრისთვის გამოვიყენოთ Strategy პატერნი და თითიოეული გადახდის მეთოდი ცალკე კლასად გამოვყოთ.

##### 1. ვქმნით strategy ინტერფეისს

```php
interface PaymentStrategy {
    public function pay($amount);
}
```

##### 2. ვქმნით კონკრეტულ სტრატეგიებს (გადახდის მეთოდებს)

```php
class PayPalPayment implements PaymentStrategy {
    public function pay($amount) {
        echo "გადახდა PayPal-ით: $amount USD";
    }
}

class CreditCardPayment implements PaymentStrategy {
    public function pay($amount) {
        echo "გადახდა საბანკო ბარათით: $amount USD";
    }
}

class CryptoPayment implements PaymentStrategy {
    public function pay($amount) {
        echo "გადახდა კრიპტოვალუტით: $amount USD";
    }
}
```

##### 3. ვქმნით კონტექსტ კლასს, რომელიც სტრატეგიას იყენებს
```php
class PaymentContext {
    private $paymentStrategy;

    public function __construct(PaymentStrategy $paymentStrategy) {
        $this->paymentStrategy = $paymentStrategy;
    }

    public function executePayment($amount) {
        $this->paymentStrategy->pay($amount);
    }
}
```


##### 4. Strategy პატერნის გამოყენება კოდში
```php
$paypalPayment = new PaymentContext(new PayPalPayment());
$paypalPayment->executePayment(100);

$cardPayment = new PaymentContext(new CreditCardPayment());
$cardPayment->executePayment(200);

$cryptoPayment = new PaymentContext(new CryptoPayment());
$cryptoPayment->executePayment(300);
```

##### 5. შედეგი
```php
გადახდა PayPal-ით: 100 USD
გადახდა საბანკო ბარათით: 200 USD
გადახდა კრიპტოვალუტით: 300 USD                                                                                    
```

---

#### უპირატესობები

Strategy პატერნის გამოყენების შემდეგ:
- **კოდ8 უფრო მოწესრიგებული და გაფართოებადი გახდა** - ახალი გადახდის მეთოდის დამატება შესაძლებელია ძირითადი კლასის ცვლილების გარეშე.
- **მოდულარობა** - თითოეული გადახდის მეთოდი დამოუკიდებელ კლასებშია და ადვილად გამოსაცვლელია.
- **კოდის გამარტივება** - თავიდან ავიცილეთ if-else ბლოკები.

ამ პატერნის გამოყენება არა მხოლოდ გადახდის სისტემებში, არამედ ნებისმიერ პროექტში შეიძლება, სადაც საჭიროა მოქნილი ალგორითმების მართვა, მაგალითად, მონაცემთა სორტირება, ლოგირების სისტემა და ა.შ

---

#### 🚀 დასკვნა

**Strategy** პატერნი არის ძლიერი ინსტრუმენტი, რომელიც პროგრამისტებს საშუალებას აძლევს, სისტემატურად მართონ სხვადასხვა ქცევა, დაიცვალ SOLID პრინციპები და კოდი უფრო მოდულარული და ადვილად შესანარჩუნებელი გახადონ.