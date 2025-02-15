**Liskov Substitution Principle (LSP)** არის SOLID პრინციპების ერთ-ერთი მნიშვნელოვანი ნაწილი, რომელიც ობიექტზე ორიენტირებულ პროგრამირებაში (OOP) გამოიყენება. ის გვკარნახობს რომ **მშობელი კლასის ობიექტი ნების მიერ დროს უნდა შეიცვალოს მათი შვილობილი კლასის ობიექტებით ისე, რომ ამან პროგრამაში შეცდომები არ გამოიწვიოს.

ეს პრინციპი ხშირად გვეხმარება მემკვიდრეობის სწორი გამოყენების დროს, რათა თავიდან ავიცილოთ ისეთი შემთხვევები, როდეესაც შვილკობილი კლასი არღვევს მშობელი კლასის ქცევას.

---

## 1. Liskov Substitution Principle- ის ახსნა

ლისკოვის ჩანაცვლების პრინციპი გვავალდებულებს, რომ **თუ B კლასი მემეკვიდრეობით იღებს A კლასს (A->B), მაშინ B-ს გამოყენება უნდა იყოს შესაძლებელი A-ს მაგივრად, ისე რომ აპლიკაციის ქუნციონირება არ შეიცვალოს.**

თუ შვილობილი კლასი ცვლის მშობელი კლასის ქცევას ისე, რომ კოდი, რომელიც მშობელ კლასს იყენებდა, არასწორად იწყებს მუშაობას, მაშინ LSP დაირღვა.

---

## 2. LSP დარღვევის მაგალითი

**❌ მაგალითი:**

1. **მშობელი კლასი:**
```php
class Rectangle {
    protected int $width;
    protected int $height;

    public function setWidth(int $width): void {
        $this->width = $width;
    }

    public function setHeight(int $height): void {
        $this->height = $height;
    }

    public function getArea(): int {
        return $this->width * $this->height;
    }
}
```

ამ კლასის ლოგიკა სწორია და მისგან გამომდინარე კლასები უნდა ემთხვეოდეს მის ქცევას.

2. **ქვეკლასი, რომელიც არჩვევს LSP-ს**
```php
class Square extends Rectangle {
    public function setWidth(int $width): void {
        $this->width = $width;
        $this->height = $width; // კვადრატი სიგანეს და სიმაღლეს ერთნაირად ცვლის
    }

    public function setHeight(int $height): void {
        $this->height = $height;
        $this->width = $height; // კვადრატი სიმაღლეს და სიგანეს ერთნაირად ცვლის
    }
}
```

3. **LSP-ს დარღვევის ტესტი**
```code
function processRectangle(Rectangle $rectangle) {
    $rectangle->setWidth(5);
    $rectangle->setHeight(10);

    if ($rectangle->getArea() !== 50) {
        throw new Exception("Liskov Substitution Principle is violated!");
    }

    echo "Area: " . $rectangle->getArea() . PHP_EOL;
}

$rectangle = new Rectangle();
processRectangle($rectangle); // OK: Area: 50

$square = new Square();
processRectangle($square); // შეცდომა! LSP დაირღვა
```

**პრობლემა:**

თუ **Square** კლასის ობიეტს გადავცემთ **processRectangle()** ფუნქციას, შედეგი არასწორი გამოვა, რადგან **setWidth()** ან **setHeight()** ორივე პარამეტს ცვლის, რაც **Rectangle** კლასში დაშვებული არ არის.

---
## 3. Liskov Substitution Principle - ის სწორად გამოყენება

ამ პრობლემის გადასაჭრელად, შეგვიძლია მოვახდინოთ რეფაქტორინგი და კლასების მემკვიდრეობა სწორედ განვსაზღვროთ.
ამ შემთხვევაში, **Rectangle** და **Square** კლასები განსხვავებული უნდა იყოს და არ უნდა იყვნენ ერთმანეთთან **[[02-Inheritance (მემკვიდრეობა)]]** ით დაკავშირებულები.

```php
interface Shape {
    public function getArea(): int;
}

class Rectangle implements Shape {
    protected int $width;
    protected int $height;

    public function __construct(int $width, int $height) {
        $this->width = $width;
        $this->height = $height;
    }

    public function getArea(): int {
        return $this->width * $this->height;
    }
}

class Square implements Shape {
    protected int $side;

    public function __construct(int $side) {
        $this->side = $side;
    }

    public function getArea(): int {
        return $this->side * $this->side;
    }
}

function processShape(Shape $shape) {
    echo "Area: " . $shape->getArea() . PHP_EOL;
}

$rectangle = new Rectangle(5, 10);
$square = new Square(5);

processShape($rectangle); // Area: 50
processShape($square);    // Area: 25
```

**გადაწყვეტა:**

✅ შევქმენით **Shape** ინტერფეისი, რომელიც აღწერს **getArea()** მეთოდს.
✅ **Rectangle** და **Square** დამოუკიდებლად ახდენენ ამ ინტერფეისის იმპლემენტაციას, და ერთმანეთთან არ მემკვიდრეობენ.
✅ **processShape()** ფუნქცია მუშაობს სწორად, რადგან **LSP** დაცულია