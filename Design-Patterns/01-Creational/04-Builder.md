**Builder** მშენებელი არის Creational ტიპის დიზაინის პატერნი, რომელიც გამოიყენება რთული ობიექტების კონსტრუციის გამარტივებისთვის. მისი მთავარი იდეაა ობიექტის აგება ეტაპობრივად, კომპლექსური კონსტრიტოორების თავიდან ასაცილებლად.

Builder პატერნი განსაკუთრებით სასარგებლოად, როცა ობიექტს ბევრი პარამეტრი აქვს და მათი კომბინაციები სხვადასხვა ვარიანტებში გამოიყენება.

---
##### როდის უნდა გამოვიყენოთ Builder?
- ობიექტი რთული სტრუქტურისაა და მისი აგება მოითხოვს რამდენიმე ნაბიჯს.
- საჭიროა სხვადასხვა ბერსიით აგებული ერთი და იგივე ტიპის ობიექტების შექმნა.
- კონსტრუქტორი ძალიან გრძელია და კოდის წაკითხვა რთულდება.

---

##### Builder პატერნის იმპლემენტაცია

შევქმნათ **Car** კლასის ობიექტი **Builder** პატერნის გამოყენებით.

###### პროდუქტის კლასი (Product)

ეს არის კლასი რომლის შექმნაც გვინდა:
```php
class Car {
    public string $engine;
    public int $seats;
    public bool $gps;
    
    public function __construct(string $engine, int $seats, bool $gps) {
        $this->engine = $engine;
        $this->seats = $seats;
        $this->gps = $gps;
    }

    public function showSpecifications(): void {
        echo "Engine: {$this->engine}, Seats: {$this->seats}, GPS: " . ($this->gps ? 'Yes' : 'No') . PHP_EOL;
    }
}
```

###### Builder ინტერფეისი
ეს ინტერფეისი განსაზღვრავს, რა მეთოდები უნდა ფქონდეს ყველა ბილდერს.

```php
interface CarBuilder {
    public function setEngine(string $type): CarBuilder;
    public function setSeats(int $count): CarBuilder;
    public function setGPS(bool $hasGPS): CarBuilder;
    public function build(): Car;
}
```

###### კონკრეტურლი ბილდერი (Concrete Builder)

ეს კლასი უშუალოდ აშენებს **Car** ობიექტს.

```php
class ConcreteCarBuilder implements CarBuilder {
    private string $engine;
    private int $seats;
    private bool $gps;

    public function setEngine(string $type): CarBuilder {
        $this->engine = $type;
        return $this;
    }

    public function setSeats(int $count): CarBuilder {
        $this->seats = $count;
        return $this;
    }

    public function setGPS(bool $hasGPS): CarBuilder {
        $this->gps = $hasGPS;
        return $this;
    }

    public function build(): Car {
        return new Car($this->engine, $this->seats, $this->gps);
    }
}
```

###### Director (დირექტორი)

**Director** მართავს ობიექტის შექმნის პროცესს. მისი მიზანია **Builder**-ის საშუალებით კონკრეტული ობიექტების სტანდარტული ვერსიების შექმნა.

```php
class CarDirector {
    public function buildSportsCar(CarBuilder $builder): Car {
        return $builder
            ->setEngine('V8')
            ->setSeats(2)
            ->setGPS(true)
            ->build();
    }

    public function buildFamilyCar(CarBuilder $builder): Car {
        return $builder
            ->setEngine('V6')
            ->setSeats(5)
            ->setGPS(false)
            ->build();
    }
}
```

###### გამოყენება (კლიენტის კოდი)

```php
$builder = new ConcreteCarBuilder();
$director = new CarDirector();

$sportsCar = $director->buildSportsCar($builder);
$familyCar = $director->buildFamilyCar($builder);

$sportsCar->showSpecifications(); // Engine: V8, Seats: 2, GPS: Yes
$familyCar->showSpecifications(); // Engine: V6, Seats: 5, GPS: No
```

**🚀 დასკვნა**

**Builder პატერნი** გვაძლევს მოქნილობას რთული ობიექტების შექმნისას. მისი გამოყენება განსაკუთებით სასარგებლოად მაშინ, როცა გვაქვს **მრავალფაზიანი კონსტრუქცია** ან **ბევრი პარამეტრი**, რომელიც შეიძლება სხვადასხვა გზით იყოს კონფიგურირებული.

**Builder** - ის მთავარი უპირატესობები:

✅ კოდის უკეთესი ორგანიზება.     
✅ მარტივი გაფართოება.    
✅ ობიექტების კონფიგურაციის გამარტივება.    