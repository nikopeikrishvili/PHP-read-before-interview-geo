პროგრამული უზრუნველყოფის განვითარებაში მნიშვნელოვანია მოდულების ერთმანეთთან დამოუკიდებლობის შენარჩუნება.
როდესაც ობიექტებს ერთმანეთთან უშუალო კომუნიკაცია აქვთ, კოდის მართვა რთულდება, ხოლო სისტმის გაფართოება - უფრო მტკივნეული ხდება. ამ პრობლემის გადასაჭრელად მოიყენება **Mediator (მედიატორი) პატერნი**.

#### რა არის Mediator პატერნი?

Mediator არის ქცევითი ტიპის პატერნი , რომელიც უზრუნველყოფს ობიექტებს შორის კომუნიკაციის ცენტრალიზაციას. მისი მთავარი დანიშნულებაა ურთიერთდამოკიდებულებების შემცირება და კომპონენტებს შორის მოქნილი კავშირის უზრუნველყოფა.

Mediator-ის გამოყენებით, ობიქტები ერთმანეთს პირდაპირ არ ეხმიანებია, ამის ნაცვლად, ისინი ყველა შეტყობინებას აგზავნიან **მედიატორთან**, რომელიც მართავს კომუნიკაციას.

---

#### Mediator ის იმპლემენტაცია

##### 1. Mediator ინტერფეისი

პირველი ნაბიჯი არის **Mediator** ინტერფეისის განსაზღვრა, რომელიც უზრუნველყოფს ობიექტების კომუნიკაციას.
```php
interface Mediator {
    public function notify(object $sender, string $event): void;
}
```
ეს ინტერფეისი შეიცავს **notify()** მეთოდს რომელიც იღებს ობიექტს (გამომგზავნს) და ივენთის სახელს.

##### 2. კომპონენტის შექმნა

დავუშვათ, გვაქვს ორი კომპონენტი  - **User** და **ChatRoom**. ისინი უშუალოდ არ ეკონტრაქტებიან ერთმანეთს, არამედ მედიატორის მეშვეობით.

```php
class User {
    private string $name;
    private Mediator $mediator;

    public function __construct(string $name, Mediator $mediator) {
        $this->name = $name;
        $this->mediator = $mediator;
    }

    public function sendMessage(string $message): void {
        echo "{$this->name} გზავნის: $message\n";
        $this->mediator->notify($this, "message_sent");
    }
}
```

ეს კლასი **User** წარმოადგენს მომხმარებელს, რომელიც მედიატორის დახმარებით გზავნის შეტყობინებას.

##### 3. Mediator - ის იმპლემენტაცია

ახლა შევქმნათ **ChatRoomMediator**, რომელიც მართავს კომუნიკაციას მომხმარებლებს შორის.

```php
class ChatRoomMediator implements Mediator {
    private array $users = [];

    public function addUser(User $user): void {
        $this->users[] = $user;
    }

    public function notify(object $sender, string $event): void {
        if ($event === "message_sent") {
            echo "Mediator: შეტყობინება დამუშავდა ჩატში.\n";
        }
    }
}
```

ამ შემთხვევაში, **ChatRoomMediator** ახდენს შეტყობინების დამუშავებას.

##### 4. გამოყენება

დავამატოთ მომხმარებლები და გამოვიყენოთ მედიატორი:
```php
$chatMediator = new ChatRoomMediator();

$user1 = new User("ნიკა", $chatMediator);
$user2 = new User("მარი", $chatMediator);

$chatMediator->addUser($user1);
$chatMediator->addUser($user2);

$user1->sendMessage("გამარჯობა, როგორ ხარ?");
```


##### 5. შედეგი

```
ნიკა გზავნის: გამარჯობა, როგორ ხარ?
Mediator: შეტყობინება დამუშავდა ჩატრუმში.
```

---

#### 🚀 დასკვნა

Mediator პატერნი სასარგებლოა იმ შემთხვევებში, როდესაც საჭიროა ობიექტებს შორის ურთიერთქმედების ცენტრალიზაცია. ის ეხმარება სისტემის მოდულარობას და კოდის სისუფთავის შენარჩუნებას.