პოლიმორფიზმი ნიშნავს ობიექტის შესაძლებლობას, იმოქმედოს სხვადასხვა ფორმით ერთსა და იმავე ინტერფეისზე დაყრდნობით, ეს მეთოდი აძლევს შესაძლებლობას კლასებს ერთი და იგივე მეთოდი სხვადასხვა ნაირად განახორციელონ.

**მადალითად:** 

```php
<?php
interface Animal {
    public function makeSound();
}

class Dog implements Animal {
    public function makeSound() {
        return "Bark!";
    }
}

class Cat implements Animal {
    public function makeSound() {
        return "Meow!";
    }
}

function makeAnimalSound(Animal $animal) {
    echo $animal->makeSound() . PHP_EOL;
}

$dog = new Dog();
$cat = new Cat();

makeAnimalSound($dog); // Bark!
makeAnimalSound($cat); // Meow!
?>
```

ეს არის პოლიმორფიზმი რადგანაც Dog და Car კლასები Animal ინტერფეისის იმპლემენტაციას აკეთებენ მაგრამ განსხვავებული იმპლემენტაცია აქვთ makeSound მეთოდის
იგივე შედეგს შეგვიძლია მივაღწიოთ აბსტრაქტული კლასების გამოყენების შემთხვევაში.

## Method Overriding (მეთოდის გადაფარვა)

Method Overriding არის დინამიური პოლიმორფიზმის ერთერთი გზა, რომელიც გვაძლევს საშუალებას რომ შვილობილმა კლასმა მშობელი კლასის მეთოდი გადაწეროს და შეცვალოს მისი ლოგიკა.
მაგალითი:
```php
<?php
class ParentClass {
    public function showMessage() {
        return "Hello from ParentClass";
    }
}

class ChildClass extends ParentClass {
    public function showMessage() {
        return "Hello from ChildClass";
    }
}

$parent = new ParentClass();
$child = new ChildClass();

echo $parent->showMessage() . PHP_EOL; // Hello from ParentClass
echo $child->showMessage() . PHP_EOL;  // Hello from ChildClass
?>
```

**✅ აქ ChildClass მა გადაფარა ParentClass ის showMessage მეთოდი**
