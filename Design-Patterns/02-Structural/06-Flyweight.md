**Flyweight pattern** არის სტრუქტურული დიზაინის პატერნი, რომელიც გამოიყენება მეხსიერების ოპტიმიზაციისთვის, განსაკუთრებით მაშინ, როცა ბევრი ობიექტი შეიცავს განმეორებით მონაცემებს. ეს პატერნი განასხვავებს **საერთო (Shared) და უნიკალურ (Unique) მონაცემებს**, რითიც ამცირებს მეხსიერების მოხმარებას.

**Flyweight pattern** შეიძლება გამოყენებულ იქნას, როდესაც საჭიროა დიდი რაოდენობის ობიექტების შექმნა, და მათი მონაცემების ნაწილი განმეორებადია. მაგალითად, თუ გვაქვს **გრაფიკული ელემენტები, ტექსტური სიმბოლოები ან მონაცემთა ქეშირება**, Flyweight პატერნი - ის გამოყენება საგრძნობლად შეამცირებს რესურსების ხარჯვას.

---
#### Flyweight გამოყენების სცენარი

ვიმსჯელოთ მაგალითად, რომ გვაქვს **ავტომობილების რეგისტრაციის სისტემა**, სადაც საჭიროა ასობით ერთნაირი მოდელის ავტომობილის მონაცემების დამუშავება. Flyweihght პატერნი დაგვეხმარება **განმეორებადი მონაცემების (მარკა, მოდელი, ფერი) გაყოფაში**, ნაცვლად იმისა, რომ ისინი ყველა ობიექტში ცალცალკე ვინახოთ.

---

#### Flyweight პატერნის იმპლემენტაცია

##### 1. Flyweight ინტერფეისი

დავიწყოთ. იმ ინტერფეისით, რომელსაც ყველა Flyweight ობიექტი დაიცავს.
```php
interface CarFlyweight {
    public function getCarDetails(): string;
}
```

##### 2. კონკრეტული Flyweight კლასი

ეს კლასი შეიცავს **საერთო საზიარო მონაცემებს** (მარკა,მოდელი,ფერი).
```php
class SharedCar implements CarFlyweight {
    private string $make;
    private string $model;
    private string $color;

    public function __construct(string $make, string $model, string $color) {
        $this->make = $make;
        $this->model = $model;
        $this->color = $color;
    }

    public function getCarDetails(): string {
        return "Car: {$this->make} {$this->model}, Color: {$this->color}";
    }
}
```

##### 3. Flyweight Factory (ობიექტის ხელახლა გამოყენება)

ეს Factory კლასი უზრუნველყოფს Flyweight ობიექტების შენახვას და **და ახალი ობიექტების შექმნას მხოლოდ მაშინ, როცა ეს აუცილებელია**.
```php
class CarFlyweightFactory {
    private array $flyweights = [];

    public function getCar(string $make, string $model, string $color): CarFlyweight {
        $key = md5($make . $model . $color);

        if (!isset($this->flyweights[$key])) {
            $this->flyweights[$key] = new SharedCar($make, $model, $color);
        }

        return $this->flyweights[$key];
    }

    public function getFlyweightCount(): int {
        return count($this->flyweights);
    }
}
```

##### 4. კონკრეტურლი ობიექტის შექმნა (განსხვავებული მონაცემებით)

როდესაც საჭიროა ავტომობილის დამატება სისტემაში, ვამატებთ უნიკალურ ინფორმაციას (მაგალითად, მფლობელის სახელი, სანომრე ნიშანი), ხოლო **არაუნიკალური მონაცემები** (მოდელი, მარკა, ფერი) მივიღოთ Flyweight Factory - დან.
```php
class CarRegistration {
    private CarFlyweight $sharedCar;
    private string $owner;
    private string $licensePlate;

    public function __construct(CarFlyweight $sharedCar, string $owner, string $licensePlate) {
        $this->sharedCar = $sharedCar;
        $this->owner = $owner;
        $this->licensePlate = $licensePlate;
    }

    public function getRegistrationDetails(): string {
        return "{$this->sharedCar->getCarDetails()}, Owner: {$this->owner}, Plate: {$this->licensePlate}";
    }
}
```

##### 5. Flyweight პატერნის გამოყენება

ახლა, შევქმნათ **CarFlyWeightFactory** და გამოვიყენოთ Flyweight ობიექტები.
```php
$factory = new CarFlyweightFactory();

$car1 = new CarRegistration($factory->getCar("Toyota", "Corolla", "Red"), "John Doe", "ABC-123");
$car2 = new CarRegistration($factory->getCar("Toyota", "Corolla", "Red"), "Jane Smith", "XYZ-789");
$car3 = new CarRegistration($factory->getCar("BMW", "X5", "Black"), "Alice Johnson", "DEF-456");

echo $car1->getRegistrationDetails() . PHP_EOL;
echo $car2->getRegistrationDetails() . PHP_EOL;
echo $car3->getRegistrationDetails() . PHP_EOL;

echo "Total Flyweight objects created: " . $factory->getFlyweightCount() . PHP_EOL;
```

##### 6. შედეგი

```
Car: Toyota Corolla, Color: Red, Owner: John Doe, Plate: ABC-123
Car: Toyota Corolla, Color: Red, Owner: Jane Smith, Plate: XYZ-789
Car: BMW X5, Color: Black, Owner: Alice Johnson, Plate: DEF-456
Total Flyweight objects created: 2
```

როგორც ვხედავთ მიუხედავად იმისა, რომ ჩვენ გვაქვს **სამი რეგისტრირებული მანქანა**, Factory-მ შექმნა **მხოლოდ ორი Flyweight ობიექტი**, რადგან "Toyota Corolla, Red" ობიექტი **ორ სხვადასხვა მანქანაში** გამოიყენება.

---

#### Flyweight პატერნის უპირატესობები

✅ **მეხსიერების ოპრიმიზაცია** - ერთი და იგივე მონაცემების დუბლირების თავიდან აცილება.    
✅ **Performance ის გაუმჯობესება** - ნაკლები ობიექტის შექმნა ნიშნავს ნაკლებ რესურსებსის ხარჯვას.    
✅ **სტრუქტურული გამჭვირვალობა** - Factory უზრუნველყოფს ობიექტების მართვას.    

---

#### 🚀 დასკვნა

**Flyweight pattern** განსაკუთრებით სასარგებლოად, როდესაც გვაქვს **ბევრი ობიექტი განმეორებადი მონაცემებით**.  მისი გამოყენება **დატვირთულ სისტემებში** (მაგ., ქეშირება, თამაშების განვითარება, მონაცემთა ვიზუალიზაცია) მნიშვნელოვნად ამცირებს მეხსიერების მოხმარებას და აუმჯობესებს პროგრამის მუშაობას.    

გაითვალისწინეთ, რომ **არ არის საჭირო Flyweight-ის გამოყენება ყველგან**, რადგან მისი არასწორი გამოყენება კოდის გართულებას და მართვის სირთულეებს გამოიწვევს.