**Factory Method** არის **Creational Design Pattern**, რომელიც ხელს უწყობს კოდის მოდულარობას და გაფართოებადობას. მას ობიექტის შემნის ლოკიგა გადააქვს ცალკეულ **Factory** კლასში, რაც უზრუნველყოფს დაბალ დამოკიდებულებას კლასებს შორის (**Loose coupling**).

---

### როდის გამოვიყენოთ Factory Method?

✅ როცა გვინდა, რომ კოდი **მოქნილი და მარტივად გაფართოებადი** იყოს.    
✅ როცა **არ** გვინდა, რომ კლიენტის კოდმა იცოდეს კონკრეტული კლასის დეტალებზე.    
✅ როცა ერთი ინტერფეისით უნდა შევქმნათ სხვადასხვა ტიპის ობიექტები.

---

### Factory Method - ის იმპლემენტაცია
წარმოიდგინე, რომ გაქვს **ტრანსპორტირების სისტემა**, რომელიც მოიცავს **სატვირთო მანქანას (Truck), გემს (Ship) და თვითმფრინავს (Airplane)**. Factory Method საშუალებას მოგვცემს, რომ ამ ოობიექტების შექმმნა კონტოლირებად და მოქნილ ფორმაში განვახორციელოთ.

---

##### 1. პირდაპირი იმპლემენტაციის პრობლემა (Factory Method - ის გარეშე):

ქვემოთ მოცემულია მარტივი იმპლემენტაცია **Factory Method-ის გარეშე**:
```php
<?php

interface Transport {
    public function deliver(): void;
}

class Truck implements Transport {
    public function deliver(): void {
        echo "Delivering by land using a truck.\n";
    }
}

class Ship implements Transport {
    public function deliver(): void {
        echo "Delivering by sea using a ship.\n";
    }
}

class Airplane implements Transport {
    public function deliver(): void {
        echo "Delivering by air using an airplane.\n";
    }
}

// გამოყენება
$truck = new Truck();
$truck->deliver();

$ship = new Ship();
$ship->deliver();
```

✅ კოდუ მუშაობს, მაგრამ პრობლემა ისაა, **❌რომ კლიენტის კოდუ პირდაპირ მიბმული არის კონკრეტულ იმპლემენტაციებზე (new Truck(), new Ship()).**    
❌ თუ მოგვიანებით **ახალი ტრანსპორტისა დამატება დაგვჭირდება**, მაგალითად **Drone**, მოგვიწევს კლიენტის კოდის შეცვლა, რაც არღვევს Open/Closed პრინციპს.

---
##### 2. Factory Method - ის გამოყენება
Factory Method-ის მიხედვით, თითოეულ ტრანსპორტს **მისი ქარხანა (Factory)** უნდა გქონდეს, რომელიც მის შექმნას უზრუნველყოფს.

**შევქმნათ Factory ინტერფეისი:**

```php
<?php

interface TransportFactory {
    public function createTransport(): Transport;
}
```

**შევქმნათ კონკრეტული Factory კლასები თითოეული ტრანსპორტისთვის**
```php
<?php

class TruckFactory implements TransportFactory {
    public function createTransport(): Transport {
        return new Truck();
    }
}

class ShipFactory implements TransportFactory {
    public function createTransport(): Transport {
        return new Ship();
    }
}

class AirplaneFactory implements TransportFactory {
    public function createTransport(): Transport {
        return new Airplane();
    }
}
```

**კლიენტის კოდი (მთავარი პროგრამა):**
```php
<?php

function deliverGoods(TransportFactory $factory) {
    $transport = $factory->createTransport();
    $transport->deliver();
}

// Truck-ის გამოყენება
$truckFactory = new TruckFactory();
deliverGoods($truckFactory);

// Ship-ის გამოყენება
$shipFactory = new ShipFactory();
deliverGoods($shipFactory);

// Airplane-ის გამოყენება
$airplaneFactory = new AirplaneFactory();
deliverGoods($airplaneFactory);
```

---
##### 3. Factory Method-ის უპირატესობები
✅ **მოქნილი არქიტექტურა** - ახალი ტრანსპორტის დამატებისას არ გვჭირდება არსებული კოდის შეცვლა.
✅ **კოდის მოდულარობა** - კლიენტის კოდი არ არის მიბმული კონკრეტულ კლასებსზე.
✅ **Open/Closed პრინციპის დაცვა** - ახალი კლასი შეიძლება დავამატოთ Factory კლასის გამოყენებით.

---

**🚀 დასკვნა**

Factory Method არის **მოსახერხებელი და მოქნილი მიდგომა ობიექტების შექმნის პროცესის სამართავად**. PHP-ში მისი გამოყენება განსაკუთრებით სასარგებლოად, როდესაც გინდა **მოდულარული და გაფართოებადი აპლიკაციების** შექმნა.