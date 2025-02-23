**Command Pattern** არის ქცევითი ტიპის დიზაინის პატერნი, რომელიც ხელს უწყობს ბრძანებების (Commands) ინკაფსულაციას უბიექტებად. ეს პატერნი გამოდგება ისეთ შემთხვევაბში, სადაც საჭიროა ოპერაციების ცალკე ობიექტებად აღთქმა, მათი ლოგის შენახვა ან გადადებული შესრულება.

**Command Pattern** შეიძლება გამოვიყენოთ, როცა გვჭირდება undo/redo ოპერაციების, შეწვეტადი (Queue-bases) დავალებების შექმნა ან პლაგინური არქიტექტურის იმპლემენტაცია.

---

#### როგორ მუშაობს Command Pattern?

Command Pattern ის მთავარი კომპონენტებია:
1. **Command ინტერფეისი** - განსაზღვრავს შემსრულებელ (execute) მეთოდს.
2. **Concrete Command კლასები** - კონკრეტული ბრძანებები, რომლებიც ინტერფეისის იმპლემენტაციას ახდენენ.
3. **Receiver (მიმღები)** - ობიექტი, რომელიც უშუალოდ ასრულებს მოქმედებებს.
4. **Invoker (გამომწვევი)** - კლასი, რომელიც ინახავს და ასრულებს ბრძანებებს.
5. **Client (კლიენტი)** - ქმნის ბრძანებას და გადასცემს Invoker - ს.
---

#### Command Pattern  - ის იმპლემენტაცია

##### 1. Command ინტერფეისი
```php
interface Command {
    public function execute(): void;
}
```

##### 2. Receiver (მიმღები)

Receiver კლასში განვათავსებთ იმ მოქმედებას, რომელიც უნდა შესრულდეს.
```php
class Light {
    public function turnOn() {
        echo "Light is ON\n";
    }

    public function turnOff() {
        echo "Light is OFF\n";
    }
}
```


##### 3. კონკრეტული Command კლასები

Concrete Command კლასები ახორციელებს Command ინტერფეისს და მოქმედებენ Receiver-ზე.
```php
class TurnOnLightCommand implements Command {
    private Light $light;

    public function __construct(Light $light) {
        $this->light = $light;
    }

    public function execute(): void {
        $this->light->turnOn();
    }
}

class TurnOffLightCommand implements Command {
    private Light $light;

    public function __construct(Light $light) {
        $this->light = $light;
    }

    public function execute(): void {
        $this->light->turnOff();
    }
}
```


##### 4. Invoker (გამომწვევი)

Invoker ინახავს და მართავს Command ობიექტებს.

```php
class RemoteControl {
    private Command $command;

    public function setCommand(Command $command): void {
        $this->command = $command;
    }

    public function pressButton(): void {
        $this->command->execute();
    }
}
```

##### 5. Client (კლიენტი)

კლიენტი ქმნის Command ობიექტებს და გადასცემს Invoker-ს.

```php
// Receiver ობიექტი
$light = new Light();

// Command ობიექტები
$turnOnCommand = new TurnOnLightCommand($light);
$turnOffCommand = new TurnOffLightCommand($light);

// Invoker ობიექტი
$remote = new RemoteControl();

// განათების ჩართვა
$remote->setCommand($turnOnCommand);
$remote->pressButton(); // Output: Light is ON

// განათების გამორთვა
$remote->setCommand($turnOffCommand);
$remote->pressButton(); // Output: Light is OFF
```

---

#### Command Pattern - ის უპირატესობები

✅ **Loose Coupling (სუსტი დამოკიდებულება)** - კლიენტის კოდი არ არის უშუალოდ დაკავშირებული Receiver-თან.    
✅ **გაძლიერებული გაფართოებადობა** - შესაძელებელია ახალი ბრძანებების დამატება კოდის ცვლილების გარეშე.    
✅**Redo/Undo მხარდაჭერა*** - შესაძლებელია შესრულებული ბრძანებების ლოგირება და უკან დაბრუნება.    
✅ **Queue და მრავალნაკადიანობა** - ბრძანებები შესაძლებელია გადავადდეს ან Queue ში შევინახოთ.

---

#### 🚀 დასკვნა

Command Pattern ერთ-ერთი ძლიერი და მოქნილი დიზაინის პატერნია, რომელიც დაგეხმარებათ **კოდის სტრუტურირებაში, დამოკიდებულებების მინიმიზაციაში** და **მოქმედებების უფრო დინამიურად მართვაში**. ის განსაკუთრებით გამოსადეგია **GUI აპლიკაციებში, Task Queue სისტემებში** და **პლაგინურ არქიტექტურაში**.