**Memento** არის ქცევითი ტიპის დიზაინის პატერნი, რომელიც გამოიყენება ობიექტის მდგომარეობის შენახვისა და საჭირეოების შემთხვევაში აღდგენის მიზნით. ეს პატერნი განსაკუთრებით გამოსადეგია იმ შემთხვევებში, როდესაც გვსურს **"გაუქმების" (Undo) ფუნქციონალი** დავამატოთ აპლიკაციაში.

Memento პატერნის მთავარი იდეაა შიდა მდგომარეობის აღრიცხვა ისე, რომ გარე კოდმა ვერ შეცვალოს ეს მდგომარეობა პირდაპირ.

#### Memento პატერნის სამი ძირითადი კომპონენტი
1. **Originator (წყარო)** - ობიექტი, რომელის მდგომარეობას ვინახავთ.
2. **Memento** ობიექტი რომელიც ინახავს **Originator**-ის მდგომარეობას.
3. **Caretaker (მზრუნველი)** - მართავს Memento ს და საჭიროების შემთხვევაში აღადგენს მდგომარეობას.

---

#### Memento პატერნის იმპლემენტაცია

##### 1. Originator - ობიექტი, რომლის მოდგომარეობას ვინახავთ

```php
class TextEditor {
    private string $text;

    public function __construct() {
        $this->text = "";
    }

    public function write(string $text): void {
        $this->text .= $text;
    }

    public function getText(): string {
        return $this->text;
    }

    public function save(): Memento {
        return new Memento($this->text);
    }

    public function restore(Memento $memento): void {
        $this->text = $memento->getState();
    }
}
```

##### 2. Memento - მდგომარეობის შემნახველი კლასი

```php
class Memento {
    private string $state;

    public function __construct(string $state) {
        $this->state = $state;
    }

    public function getState(): string {
        return $this->state;
    }
}
```

##### 3. Caretaker - Memento -ს მმართველი

```php
class History {
    private array $history = [];

    public function push(Memento $memento): void {
        $this->history[] = $memento;
    }

    public function pop(): ?Memento {
        return array_pop($this->history);
    }
}
```

##### 4. გამოყენება
```php
$editor = new TextEditor();
$history = new History();

$editor->write("პირველი წინადადება. ");
$history->push($editor->save()); // ვინახავთ მდგომარეობას

$editor->write("მეორე წინადადება. ");
$history->push($editor->save()); // ვინახავთ მდგომარეობას

$editor->write("მესამე წინადადება. ");

echo "მიმდინარე ტექსტი: " . $editor->getText() . PHP_EOL;

// უკან დაბრუნება
$editor->restore($history->pop());
echo "გაუქმების შემდეგ: " . $editor->getText() . PHP_EOL;

$editor->restore($history->pop());
echo "კიდევ ერთხელ გაუქმების შემდეგ: " . $editor->getText() . PHP_EOL;
```

##### 5. შედეგი
```
მიმდინარე ტექსტი: პირველი წინადადება. მეორე წინადადება. მესამე წინადადება. 
გაუქმების შემდეგ: პირველი წინადადება. მეორე წინადადება. 
კიდევ ერთხელ გაუქმების შემდეგ: პირველი წინადადება. 
```


---


#### როდის გამოვიყენოთ Memento პატერნი?

✅ თუ გჭირდება **Undo (გაუქმების) ფუნქციონალი**.    
✅ თუ გქსურს **ობიექტის მდგომარეობის სარეზერვო ასლის შექმნა**.    
✅ თუ **მნიშვნელოვანი მონაცემების დაცვა** გჭირდებათ ისე, რომ კლიენტმა ვერ შეცვალოს მათი შიდა სტრუქტურა.

---

#### 🚀 დასკვნა

===Memento პატერნი არის შესანიშნავი გზა **ობიექტის მდგომარეობის დროებით შესანახად** და საჭიროების შემთხვევაში მის აღსადგენად. მისი იმპლემენტაცია მარტივია და განსაკუთრებით გამოსადეგია ისეთ პროგრამებში, სადაც მნიშვნელოვანია **"Undo" ფუნქციონალი**===