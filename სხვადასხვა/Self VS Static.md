PHP ში self და static ორივე გამოიყენება კლასის შიგნით მეთოდებსა და თვისებებზე წვდომისთვის, მაგრამ მათ შორის მნიშვნელოვანი განსხვავებაა დინამიურ მემკვიდრეობასთან დაკავშირებით.

## 1. self კომპილაციის დროს განისაზღვრება

- self გამოიყენება **იმავე კლასში** გამოცხადებული სტატიკური მეთოდების ან propertie ების მისაწვდომად
- **არ ხდება მემკვიდრეობით გადაცემა (late static binding არ მუშაობს 

**მაგალითი

```php
<?php
class ParentClass {
    public static function whoAmI() {
        return self::class; // ყოველთვის ParentClass-ს დააბრუნებს
    }
}

class ChildClass extends ParentClass {}

echo ChildClass::whoAmI(); // გამოიტანს "ParentClass"
?>
```

✅ **აქ self::class ParentClass ზე მიუთითებს, მიუხედავად იმისა რომ ის იქნა გამოძახებული ChildClass იდან
❌ self ყოველთვის მითითებული კლასის კონტექსტს იყენებს, და არ იცვლება მემკვიდრეობა
## 2. static (Late static binding)

- static დინამიურია და **მემკვიდრეობით მიიღებს იმ კლასის სახელს საიდანაც გამოიძახეს.**
- გამოიყენება როცა გვინდა რომ მეთოდი ან თვისება მემკვიდრეობით შეიცვალოს.

**მაგალითი**
```php
<?php
class ParentClass {
    public static function whoAmI() {
        return static::class; // late static binding
    }
}

class ChildClass extends ParentClass {}

echo ChildClass::whoAmI(); // გამოიტანს "ChildClass"
?>
```

✅ **აქ static::class მემკვიდრეობით სწორედ გადაეცემა და აბრუნებს ChildClass ს.
✅ ეს Late static binding ის მაგალითია.

## 3. self vs static შედარება

| მახასიათებელი     | self                                                 | static                                                      |
| ----------------- | ---------------------------------------------------- | ----------------------------------------------------------- |
| თანმიმდევრულობა   | ყოველთვის იმავე კლასზე მიუთითებს                     | იცვლება მემკვიდრეობა (Late static binding)                  |
| გამოყენება        | self::method()                                       | static::method()                                            |
| მემკვიდრეობა      | არ იცვლება შვილობილ კლასებში                         | იცვლება შვილობილ კლასებში                                   |
| როდის გამოვიყენოთ | როცა გვინდა, რომ მეთოდი მხოლოდ ამავე კლასში იმუშავოს | როცა გვინდა, რომ მემკვიდრეობით შვილობილ კლასებზეც შეიცვალოს |

## 4. რეალური მაგალითი
**მოდი, ვნახოთ, როგორ შეიძლება ეს გამოვიყენოთ პრაქტიკაში.**

```php
<?php
class ParentClass {
    public static function create() {
        return new static(); // late static binding
    }
}

class ChildClass extends ParentClass {}

$child = ChildClass::create();
echo get_class($child); // გამოიტანს "ChildClass"
?>
```

✅ **აქ static ის გამოყენება ნიშნავს რომ ChiuldClass::create(); დააბრუნებს ChildClass ის ობიექტს, და არა ParentClass-ის.

**🔹** **როდის გამოვიყენოთ self და როდის static?

- **self** - როცა გვინდა, რომ მემკვიდრეობით გადაცემა მოხდეს და მხოლოდ იმავე კლასში იმუშაოს.
- **static** - როცა გვჭირდება, რომ შვილობილმა კლასებმა შეძლონს საკუთარი ლოგიკის მემკვიდრეობით გადაცემა.
✅ **static უფრო დინამიური და მოქნილია, მაგრამ self უფრო მკაცრად განსახღვრულია და გვიწესებს შეზღუდვას მემკვიდრეობაზე** 