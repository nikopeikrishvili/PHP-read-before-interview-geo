**Observer (დამკვირვებელი)** არის ქცევითი ტიპის პატერნი, რომელიც გამოიყენება ობიექტებს შორის დამოკიდებულების სამართავად. ეს პატერნი საშუალებას გვაძლევს, რომ ერთი ობიექტის ცვლილებები ავტომატურად აისახოს მის დამკვირვებელზე ისე, რომ მათ შორის პირდაპირი დამოკიდებულება არ არსებობდეს.

#### როგორ გამოვიყენოთ ეს პატერნი?

Observer პატერნი განსაკუთრებით სასარგებლოად, როდესაც გვჭირდება:
- ერთი ობიექტის **ცვლილებებზე** რეაგეირება სხვა ობიექტების მიერ.
- **Loose Coupling (სუსტი კავშირი)** ობიექტებს შორიდ, რათა ცვლილებები მარტივად მოხდეს.
- **ივენთების** მართვა პროგრამაში.

#### Observer პატერნის იმპლემენტაცია

ქვევით მოცემულია მაგალითი, თუ როგორ შეგვიძლია ამ პატერნის გამოყენება.

##### 1. Subject კლასის შექმნა

ეს კლასი ინახავს დამკვირვებლების სიას და მათ ეცნობება, როდესაც მონაცემი იცვლება.
```php
<?php

interface Subject {
    public function attach(Observer $observer);
    public function detach(Observer $observer);
    public function notify();
}

class NewsPublisher implements Subject {
    private array $observers = [];
    private string $latestNews;

    public function attach(Observer $observer) {
        $this->observers[] = $observer;
    }

    public function detach(Observer $observer) {
        $this->observers = array_filter($this->observers, fn($obs) => $obs !== $observer);
    }

    public function notify() {
        foreach ($this->observers as $observer) {
            $observer->update($this->latestNews);
        }
    }

    public function setNews(string $news) {
        $this->latestNews = $news;
        $this->notify(); // ყველა დამკვირვებელი მიიღებს განახლებულ სიახლეს
    }
}
```

##### 2. Observar (დამკვირვებელი) ინტერფეისი და კონკრეტული დამკვირვებელი

**Observer** ინტერფეისი განსაზღვრავს **update()** მეთოდს, რომელიც განახლდება **subject -ის ცვლილებისას**.

```php
<?php

interface Observer {
    public function update(string $news);
}

class EmailSubscriber implements Observer {
    private string $name;

    public function __construct(string $name) {
        $this->name = $name;
    }

    public function update(string $news) {
        echo "📧 {$this->name} მიიღო ახალი სიახლე: $news\n";
    }
}

class SmsSubscriber implements Observer {
    private string $phoneNumber;

    public function __construct(string $phoneNumber) {
        $this->phoneNumber = $phoneNumber;
    }

    public function update(string $news) {
        echo "📲 SMS გაგზავნილია ({$this->phoneNumber}): $news\n";
    }
}
```

##### 3. გამოყენება
```php
<?php

$newsPublisher = new NewsPublisher();

$emailSubscriber = new EmailSubscriber("ნიკო");
$smsSubscriber = new SmsSubscriber("555-123456");

$newsPublisher->attach($emailSubscriber);
$newsPublisher->attach($smsSubscriber);

$newsPublisher->setNews("PHP 8.3 გამოვიდა ახალი ფუნქციებით!");
$newsPublisher->setNews("Observer პატერნი მარტივად აითვისე PHP-ში!");
```


##### 4. შედეგი

```
📧 ნიკო მიიღო ახალი სიახლე: PHP 8.3 გამოვიდა ახალი ფუნქციებით!
📲 SMS გაგზავნილია (555-123456): PHP 8.3 გამოვიდა ახალი ფუნქციებით!
📧 ნიკო მიიღო ახალი სიახლე: Observer პატერნი მარტივად აითვისე PHP-ში!
📲 SMS გაგზავნილია (555-123456): Observer პატერნი მარტივად აითვისე PHP-ში!
```

---

#### 🚀 დასკვნა

Observer პატერნი იდეალურია შემთხვევებისთვის, როდესაც გვინდა, რომ ერთმა ობიექტმა ავტომატურად ცნობოს სხვებს ცვლილების შესახებ. მისი იმპლემენტაცია მარტივია და იძლევა მოქნილობას რომ მოვახდინოთ ივენთებზე რეაგირება და შევამციროთ კავშირი კლასებს შორის