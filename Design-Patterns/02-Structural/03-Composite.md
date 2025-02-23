**Composite პატერნი** არის სტრუქტურული დიზაინის პატერნი,  რომელიც გვაძლევს საშუალებას ერთი და იგივე ინტერფეისით ვმართოთ როგორც ინდივიდუალური ობიექტები, ისე მათი კოლექციები. ეს პატერნი განსაკუთრებით გამოსადეგია იერარქიული სტრიქტირების, მაგალითად, ხეების (Tree Structures) შესაქმნლეად.

PHP-ში **Composite Pattern** გვაძლევს საშუალებას, დავამუშავოთ ობიექტის სტრუქტურა ისე, თითქოს ისინი ერთიანი ელემენტია. მოდით, განვიხილოთ მისი გამოყენება რეალურ პროექტში.

---

#### როდის გამოვიყენოთ Composite Pattern?

- როცა გვსურს ობიექტების სტრუქტურას **იერარქიული სტრუქტურა** მივცეთ (მაგ., ფაილების სისტემა, მენიუები, გრაფიკული ელემენტები).
- როცა საჭიროა **ინდივიდუალური და ჯგუფური ობიექტების ერთიანად დამუშავება.**
- როცა გვსურს **მოდულარობა და მოქნილობა**, რათა საჭიროებისამებრ დავამატოთ ახალი კომპონენტები.
---

#### მაგალითი, ფაილების სისტემა

გავაკეთოთ ფაილებისა და დირექტორიების სტრუცტურა **Composite Pattern** ის გამოყენებით.

##### 1. ვქმნით კომპონენტს (ინტერფეისს)
```php
<?php

interface FileSystemComponent {
    public function show();
}
```

ეს ინტერფეისი ყველა ელემენტს (როგორც ფაილებს, ისე დირექტორიებს) ავალდებულებს, რომ ჰქონდეთ **show()** მეთოდი.

##### 2. ვქმნით ფაილის კლასს

```php
class File implements FileSystemComponent {
    private string $name;

    public function __construct(string $name) {
        $this->name = $name;
    }

    public function show() {
        echo "File: " . $this->name . PHP_EOL;
    }
}
```

##### 3. ვქმნით დირექტორიის კლასს

```php
class Directory implements FileSystemComponent {
    private string $name;
    private array $children = [];

    public function __construct(string $name) {
        $this->name = $name;
    }

    public function add(FileSystemComponent $component) {
        $this->children[] = $component;
    }

    public function show() {
        echo "Directory: " . $this->name . PHP_EOL;
        foreach ($this->children as $child) {
            echo "  ";
            $child->show();
        }
    }
}
```

**Directory** კლასი **კომპოზიტია** და შეიძლება შეიცავდეს როგორც ფაილებს, ისე სხვა დირექტორიებს.

##### 4. ვიყენებთ ჩვენს პატერნს

```php
// ინდივიდუალური ფაილები
$file1 = new File("document.txt");
$file2 = new File("photo.jpg");

// საქაღალდე (დირექტორია)
$folder1 = new Directory("My Documents");
$folder1->add($file1);
$folder1->add($file2);

// მეორე საქაღალდე (დირექტორია)
$folder2 = new Directory("Projects");
$file3 = new File("project_code.php");
$folder2->add($file3);

// მთავარი საქაღალდე, რომელიც შეიცავს ორ საქაღალდეს
$root = new Directory("Root");
$root->add($folder1);
$root->add($folder2);

// ვაჩვენებთ მთლიან სტრუქტურას
$root->show();
```

##### 5. შედეგი

ამ კოდის გაშვებისას მივიღებთ შემდეგ შედეგს:
```php
Directory: Root
  Directory: My Documents
    File: document.txt
    File: photo.jpg
  Directory: Projects
    File: project_code.php
```

როგორც ვხედავთ, **Directory** ობიექტს შეუძლია შეიცავდეს როგორც **File** - ებს, ასე სხვა **Directory** - ებს, და ყველა მათგანს ენთნაირად ვამუშავებთ **show()** მეთოდის გამოყენებით.

#### 🚀 დასკვნა


**Composite Pattern** PHP-ში ძალიან მოსახერხებელია იერარქიული სტრუტურების შესაქმნელად, რაც გვაძლევს:
- **მოდულარობას** - შეგვიძლია მარტივად დავამატოთ ან წავშალოთ კომპონენტები.
- **მოქნილობას** - მომხმარებეს არ სჭირდება იცოდეს, ინდივიდუალირ ობიექტთან აქვთ საქმე თუ კომპოზიტთან.
- **გადამუშავებადობას** - კოდის სტრუქტურა იშლება და მისი მართვა მარტივია.

ეს პატერნი აქტიურად მგამოიყყენება ისეთ პროექტებში, როგორიცაა:
- ფაილების მართვის სისტემები.
- მენიუს სტრუქტურები.
- გრაფიკული ელემენტების მართვა.
- ორგანიზაციული სტრუქტურები.