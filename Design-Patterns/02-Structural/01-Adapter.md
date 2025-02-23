**Adapter** არის **Structural** ტიპის დიზაინის პატერნი, რომელიც გამოიყენება, როცა გვჭირდება ორი შეუთავსებელი ინტერფეისის მქონდე კომპონენტების ერთმანეთთან დაკავშირება. იგი მოქმედებს როგორც "მთარგმენილი", რომელიც ერთის ინტერფეისს გარდაქმნის მეორესთვის მისაღებ ფორმატში.

Adapter პატერნი განსაკუთრებით გამოსადეგია როცა გვსურს არსებული კოდის ან მესამე მხარის ბიბლიოთეკების ხელახლა გამოყენება ისე, რომ მათი ცვლილება არ მოგვიწიოს.

---
##### Adapter პატერნის გამოყენება

მოდით, განვიცილოთ რეალური მაგალითი, სადაც გვაქვს ძველი და ახალი გადახდის სისტემები. გვინდა,  რომ ძველი გადახდის სისტემა თავსებადი გავხადოთ ახალ ინტერფეისთან.

---

###### ძველი გადახდის კლასი
```php
class OldPaymentSystem {
    public function sendPayment($amount) {
        return "Paid $amount using Old Payment System.";
    }
}
```

მეთოდი **sendPayment()** იღებს თანხას და აბრუნებს გადახდის სტატუსს.

---

###### ახალი გადახდის ინტერფეისი
```php
interface NewPaymentInterface {
    public function pay(int $amount): string;
}
```
ეს ახალი სტანდარტიზირებული გადახდის ინტერფეისი გვიჩვენებს თუ როგორი უნდა იყოს გადახდის მეთოდი.

---

###### Adapter კლასი
```php
class PaymentAdapter implements NewPaymentInterface {
    private $oldPaymentSystem;

    public function __construct(OldPaymentSystem $oldPaymentSystem) {
        $this->oldPaymentSystem = $oldPaymentSystem;
    }

    public function pay(int $amount): string {
        return $this->oldPaymentSystem->sendPayment($amount);
    }
}
```

**PaymentAdapter** კლასი ახდენს **NewPaymentInterface** ის იმპლემენტაციას. **pay()** მეთოდი ინტერფეისს შეესაბამება, მაგრამ კულისებში ძველ **sendPayment()** მეთოდს იძახებს.

---

###### კლიენტის კოდი
```php
$oldPayment = new OldPaymentSystem();
$adapter = new PaymentAdapter($oldPayment);

echo $adapter->pay(100); 
```

###### შედეგი:

```php
Paid 100 using Old Payment System.
```


---

##### Adapter პატერნის უპირატესობები
✅ **შეუთავსებელი კოდის ინტერგრაცია** - შესაძლებელია ძველი კოდის ან მესამე მხარის ბიბლიოთეკის გამოყენება ახალ სტანდარტთან.
✅ **კოდის მოქნილობა** - ადვილად შეიძლება ახალი API-ის ან სისტემების ინტეგრაცია არსებულ კოდთან.
✅ **Single Responsibility Principle (SRP)** - განსხვავებული პასუხისმგებლობის მქონდე კოდები განცალკევება.

---

**🚀 დასკვნა**
Adapter პატერნი PHP-ში გამოსადეგია, როცა საჭიროა არსებული კლასის ფუნქციონალი მოდიფიკაციის გარეშე ახალი ინტერფეისის შესაბამისი გახდეს. ეს განსაკუთრებით გამოსადეგია API ინტეგრაციებში, მემკვიდრეობითობის მართვაში და სისტემების მოდულარიზაციაში.