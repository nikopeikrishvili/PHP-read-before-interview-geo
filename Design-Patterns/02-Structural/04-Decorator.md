**Decorator Pattern** არის ერთ-ერთი სტრუქტურული დიზაინ პატერნი, რომელიც გამოიყენება არსებული კლასის ფუნქციონალის გაფართოებისთვის ისე, რომ არ შეიცვალოს მისი თავდაპირველი კოდის, ეს პატერნი საშუალებას გვაძლევს **დამატებითი ქცევები დინამიურად** მივანიჭოთ ობიექტებს.

PHP-ში **Decorator Pattern** განსაკუთრებით სასარგებლოა, როდესაც გვსურს კლასის გაფართოება მიმკვიდრეობის გარეშე. მაგალითად, შეგვიძლია გამოვიყენოთ ეს პატერნი ისეთი სერვისების გაფართოებისთივს როგორიცაა ლოგირება, ქეშირება ან მონაცემთა გადამუშავება.

---

#### Decorator Pattern - ის სტრუქტურა
1. **Component (კომპონენტი)** - აბსტრაქტული კლასის ან ინტერფეისის სახით განსაზღვრული ძირითადი ობიექტი.
2. **Concrete Component (კონკრეტული კომპონენტი)** - ძირითადი კლასი, რომელიც საჭირო ფუნქციონალს შეიცავს.
3. **Decorator (დეკორატორი)** - აბსტრაქტული კლასი, რომელიც კომპონენტებს ავსებს და მისი გაფართოებისთვის გამოიყენება.
4. **Concrete Decorator (კონკრეტული დეკორატორი)** - დეკორატორის კონკრეტული რეალიზაცია, რომელიც კომპონენტს დამატებით ფუნქციონალს უმაატებს.
---

#### Decorator Pattern ის იმპლემენტაცია
მოდით, განვიხილოთ რეალური მაგალითი - ტექსტის ფორმატირების სისტემა, სადაც გვაქვს **TextFormatter** და სხვადასხვა დეკორატორები, რომლებიც ტექსტს სხვადასხვა სტილში გარდაქმნიან.

##### 1. საბაზისო ინტერფეისი (Component)

```php
<?php

interface TextFormatter {
    public function format(string $text): string;
}
```
ეს ინტერფეისი განსაზღვრავს მეთოდს **format()**, ყველა კლასის მიერ უნდა იყოს იმპლემენტირებული.

##### 2. ძირითადი კლასის იმპლემენტაცია (Concrete Class)

```php
class PlainText implements TextFormatter {
    public function format(string $text): string {
        return $text;
    }
}
```

ეს კჯლასი უბრალოდ აბრუნებს ტექსტს ისეთად, როგორიც არის.

##### 3. დეკორატორის შექმნა (Base Decorator)

```php
class TextDecorator implements TextFormatter {
    protected TextFormatter $formatter;

    public function __construct(TextFormatter $formatter) {
        $this->formatter = $formatter;
    }

    public function format(string $text): string {
        return $this->formatter->format($text);
    }
}
```

არე არის **აბსტრაქტული დეკორატორი**, რომელიც შემდგომში გაფართოებისთვის გამოიყენება.

##### 4. კონკრეტული დეკორატორების შექმნა

**UppercaseDecorator - ტექსტოს ასოების მაღალ რეგისტრში გადაყვანა:**

```php
class UppercaseDecorator extends TextDecorator {
    public function format(string $text): string {
        return strtoupper($this->formatter->format($text));
    }
}
```

**HTMLBoldDecorator - ტექსტის ტეგით გაფორმება:**

```php
class HTMLBoldDecorator extends TextDecorator {
    public function format(string $text): string {
        return '<b>' . $this->formatter->format($text) . '</b>';
    }
}
```


##### 5. გამოყენება

```php
$text = new PlainText();
echo $text->format("Hello, World!"); 
// შედეგი: Hello, World!

$uppercaseText = new UppercaseDecorator($text);
echo $uppercaseText->format("Hello, World!"); 
// შედეგი: HELLO, WORLD!

$boldText = new HTMLBoldDecorator($uppercaseText);
echo $boldText->format("Hello, World!"); 
// შედეგი: <b>HELLO, WORLD!</b>
```


#### Decorator Pattern - ის უპირატესობები

✅ **კოდში ცვლილებების გარეშე გაფართოების შესაძლებლობა** - ძირითადად კლასის შეცვლის გარეშე შეგვიძლია ახალი ფუნქციონალის დამატება.    
✅ **კომპონენტის მოქნილი კომბინირება** - შეგვიძლია დინამიურად დავამატოთ დეკორატორები და სხვადასხვა ფუნქციები.    
✅ **Single Responsibility Principle (SRP) დაცვა** - დეკორატორები ლოგიკას ცალკეულ კლასებში ანაწილებენ.

---

#### 🚀 დასკვნა

**Decorator Pattern** PHP-ში ერთ-ერთი ყველაზე მოქნილი და ეფექტული საშუალებაა **კლასების გაფართოებისთვის** მემკვიდრეობის გამოყენების გარეშე. მისი დახმარებით შესაძლებელია ფუნქციონალის მოდულურად დამატება და კოდის უფრო მარტივად მართვა.