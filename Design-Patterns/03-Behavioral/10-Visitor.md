
**Visitor** (სტუმარი) არის ქცევითი ტიპის პატერნი, რომელიც საშუალებას გვაძლევს გამოვაცალკევოთ ალგორითმები იმ ობიექტებისგან, რომლებზეც ისინი მუშაობენ. ეს პატენი განსაკუთრებით გამოსადეგია, როდესაც საჭიროა არსებულ კლასებზე ახალი ფუნქციონალიდ დამატება ისე, რომ არ შეიცვალოს მათი კოდი.


#### როდის გამოვიყენოთ Visitor პატერნი?
- როცა გვინდა არსებულ კალსებში ახალი ფუნქციონალის დამატება **მემკვიდრეობის გარეშე**.
- როდესაც სხვადასხვა ტიპის ობიექტებზე გვსურს ერთნაირი ოპერაციების შესრულება.
- როდესაც კლასების სტრუქტურა ხშირად იცვლება, მაგრამ მათზე შესრულებული ოპერაციები უცვლელია.
---

#### Visitor პატერნის იმპლემენტაცია 

##### 1. ინტერფეისის შექმნა ელემენტებისთვის

პირველ რიგში, დავწეროთ **Element** ინტერფეისი, რომელსაც ექნება **accept()** მეთოდი:

```php
<?php
interface Element {
    public function accept(Visitor $visitor);
}
```

##### 2. კონკრეტული ელემენტების შექმნა

შევქმნათ ორი კლასი, რომლებიც იმპლემენტაციას გაუწევენ **Element** ინტერფეისს:

```php
class Book implements Element {
    private string $title;
    private float $price;

    public function __construct(string $title, float $price) {
        $this->title = $title;
        $this->price = $price;
    }

    public function getTitle(): string {
        return $this->title;
    }

    public function getPrice(): float {
        return $this->price;
    }

    public function accept(Visitor $visitor) {
        $visitor->visitBook($this);
    }
}

class DVD implements Element {
    private string $title;
    private float $price;

    public function __construct(string $title, float $price) {
        $this->title = $title;
        $this->price = $price;
    }

    public function getTitle(): string {
        return $this->title;
    }

    public function getPrice(): float {
        return $this->price;
    }

    public function accept(Visitor $visitor) {
        $visitor->visitDVD($this);
    }
}
```


##### 3. Visitor ინტერფეისი

Visitor ინტერფეისი შეიცავს მეთოდებს თითოეული ელემენტის ტიპისთვის:
```php
interface Visitor {
    public function visitBook(Book $book);
    public function visitDVD(DVD $dvd);
}
```

##### 4. კონკრეტული ვიზიტორების შექმნა

შევქმნათ **DiscountVisitor**, რომელიც დაითვლის ფასდაკლებას:
```php
class DiscountVisitor implements Visitor {
    public function visitBook(Book $book) {
        echo "ფასდაკლება წიგნზე '{$book->getTitle()}': " . ($book->getPrice() * 0.9) . " ლარი\n";
    }

    public function visitDVD(DVD $dvd) {
        echo "ფასდაკლება DVD-ზე '{$dvd->getTitle()}': " . ($dvd->getPrice() * 0.85) . " ლარი\n";
    }
}
```

##### 5. გამოყენება

ახლა, შევქმნათ ობიექტები და გამოვიყენოთ **Visitor**:
```php
$items = [
    new Book("PHP Design Patterns", 50),
    new DVD("OOP in PHP", 30)
];

$discountVisitor = new DiscountVisitor();

foreach ($items as $item) {
    $item->accept($discountVisitor);
}
```

##### 5. შედეგი

ზემოთ მოყვანილი კოდის გაშვებისას მივიღებთ შემდეგ შედეგს:
```
ფასდაკლება წიგნზე 'PHP Design Patterns': 45 ლარი
ფასდაკლება DVD-ზე 'OOP in PHP': 25.5 ლარი
```


---

#### 🚀 დასკვნა

როგორც ვდედავთ, **DiscountVisitor** კლასმა განახორციელა სხვადასხვა ტიპის ელემენტებზე ფასდაკლება ისე, რომ უშუალოდ **Book** და **DVD** კლასებში ცვლილებები არ დაგვჭირდა. ეს აჩვენებს **Visitor** პატერნის ძალას - ჩვენ შეგვიძლია ახალი ოპერაციები დავამატოთ კლასებზე მათი მოპდიფიკაციის გარეშე, რაც პროგრამის გაფართოებას უფრო მარტივს და მოქნილს ხდის.

ეს პატერნი განსაკუთრებით სასარგებლოად, როდესაც გვაქვს რთული სტრუქტურის ობიექტები, რომელთაც სხვადასხვა ტიპის დამუშავება სჭირდებათ, მაგალითა:
- ფასების კალკულაცია.
- რეპორტინგი.
- სერიალიზაცია და ლოგირება.