MySQL - ში  ტრანზაქციის იზოლაციის დონეები აღწერენ იმ მეთოდებს, რომლითაც სხვადასხვა ტრანზაქციები იზოლირებულად მუშაობენ, როცა ერთდოულად რამდენიმე ტრანზაქცია ხდება ერთ და იმავე მონაცემებზე. თითოეული დონე განსაზღვრავს, თუ რამდენად შეძლებენ ერთდოული ტრანზაქციები ერთმანეთში შეცდომის გამოწვევას ან მონაცემების რედაქტირებას. MySQL-ში არსებობს **ოთხი** ძირითადი იზოლაციის დონე:

### სავარჯიშო ცხრილი
```sql
CREATE TABLE account (
    account_id INT AUTO_INCREMENT PRIMARY KEY,
    account_name VARCHAR(255) NOT NULL,
    balance DECIMAL(10, 2) NOT NULL
);

-- დავამატოთ რამდენიმე სატესტო მონაცემი
INSERT INTO account (account_name, balance) VALUES 
('John Doe', 1000.00),
('Alice Smith', 1500.50),
('Bob Johnson', 2000.75);
```
### Read Uncommitted 
ეს ყველაზე დაბალი დონეა, ამ დონეზე, ერთი ტრანზაქციის მიმდინარეობის დროს თუ გავუშვებთ მეორე ტრანზქციას, და მეორე ტრანზაქცია კითხულობს იმ მონაცემებს რომელზეც პირველი ტრანზაქცია ახდენს მანიპულირებას, ჩვენ წავიკიხავთ იმ მონაცემებსაც კი რომელიც შეიცვალა მაგრამ ჯერე **COMMIT** არ მოხდა. 
**შედეგს რომელსაც ეს დონე გვაძლევს ასევე უწოდებენ Dirty Read ს**.

#### მაგალით:

##### სესია 1:
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;  
START TRANSACTION;  
-- შეცვალეთ ბალანსი  
UPDATE account SET balance = balance - 100 WHERE account_name = 'John Doe';  
-- არ სრულდება COMMIT-ს სანამ მეორე სესია ვერ დაიწყებს წაკითხვას  
COMMIT;
```

##### სესია 2:
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;  
START TRANSACTION;  
-- წაიკითხეთ 'John Doe'-ის ბალანსი, სანამ პირველი ტრანზაქცია არ დასრულდება  
SELECT * FROM account WHERE account_name = 'John Doe';  
COMMIT;
```

##### შედეგი:
თუ **John Doe** ის საწყისი ბალანსი იყო 1000, პირველი ტრანზაქცია ცდილობს ბალანსის შემცირებას 100 ერთეულით, და თუ პარალელურად სანამ **COMMIT** მოხდება, გავუშვებთ მეორე ტრანზაქციას **READ UNCOMMITTED** იზოლაციის დონით, ჩვენ წავიკითხავთ იმ მონაცემებს რომელთა და-**COMMIT**-ება ჯერ არ მოხდა პირველ ტრანზაქციაში.

### Serializable

ეს ყველაზე მაღალი დონეა, სადაც ტრანზაქციები ერთმანეთისგან სრულიად ზოლირებულები არიან. ამ დონეზე ტრანზაქციები თანმიმდევრულად ხდება და თუ ერთი ტრანზაქცია დაიწყება იმ დროს როცა პირველი სრულდება, ის დაელოდება მის შესრულებას.

#### მაგალითი

##### სესია 1:
```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;  
START TRANSACTION;  
SELECT * FROM account WHERE account_id = 1 FOR UPDATE;  
select sleep(10);  
COMMIT;
```

##### სესია 2:
```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;  
select * from account where account_id=1;  
START TRANSACTION;  
UPDATE account SET balance = balance - 50 WHERE account_id = 1;  
COMMIT;  
select * from account where account_id=1;
```

##### შედეგი
როგორც ვნახავთ, მეორე სესიაში, სანამ პირველის ტრანზაქცია დასრულდებოდა, პირველი SELECT ეგრევე სრულდება, და გვიბრუნებს ჯერ კიდეც მონაცემებს რომლებიც არ და-**COMMIT**-და პირველ სესიაში, მეორე სერიაში **SELECT** შესრულდა დაუყოვნებლივ, ხოლო **SELECT ... FOR UPDATE** ელოდებოდა პირველი სესიის დასრულებას.
### Repeatable Read

ამ დონეზე თუ ტრანზაქცია ერთხელ უკვე წაიკითხავს მონაცემებს, ის კვლავ მიიღებს იმავე მნიშვნელობას მომავალში, მიუხედავად იმისა, რომ სხვა ტრანზაქციების მიერ ეს მონაცემების შეცვლილია. **Non-Repeatable reads** პრობლემა აქ აღარ გვხვდება, თუმცა "**Phantom read**" ის შესაძლებლობა მაინც არსებობს.

#### მაგალითი:

##### სესია 1:
```sql
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;  
START TRANSACTION; 
-- გაუშვით სანამ მეორე ტრანზაქცია დაიწყება
SELECT * FROM account WHERE account_id = 1;  
-- გაუშვით მას მერე რაც მეორე ტრანზაქცია დასრულდება - შედეგი იგივე იქნება
SELECT * FROM account WHERE account_id = 1;  
COMMIT;
```

##### სესია 2:
```sql
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;  
START TRANSACTION;  
SELECT * FROM account WHERE account_id = 1;  
UPDATE account SET balance = balance - 50 WHERE account_id = 1;  
SELECT * FROM account WHERE account_id = 1;  
COMMIT;
```

##### შედეგი:
როგორც დავინახავთ, პირველ ტრანზაქციაში **SELECT** ყოველთვის ერთი და იგივე შედეგს მოგვცემს, მაშინაც კი როცა მეორე ტრანზაქცია და-**COMMIT**-დება.


### Read Committed

ამ დონეზე ტრანზაქცია მხოლოდ იმ მონაცემებს წაიკითხავს, რომელიც სხვა ტრანზაქციის მიერ უკვე დამუშავებულია და **COMMIT** მოხდა.
ეს დონე თავიდან აგვარიდებს **Dirty Read** პრობლემას, მაგრამ მანდ შეიძლება ვერ მოაგვაროს პრობლემა რომელიც **Repeatable Read** ის დროს გვხვდება.

#### მაგალითი:

##### სესია 1:
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;  
START TRANSACTION;  
UPDATE account SET balance = balance - 100 WHERE account_id = 1;  
COMMIT;
```

##### სესია 2: 
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;  
START TRANSACTION;  
SELECT * FROM account WHERE account_id = 1;  
COMMIT;
```

##### შედეგი
როგორც დავინახავთ მეორე ტრანზაქცია დაინახავს ძველ მონაცემებს სანამ პირველი ტრანზაქციის **COMMIT** მოხდება, ხოლო პირველი ტრანზაქციის **COMMIT** ის შემდეგ მას შემდეგ რაც მონაცემები განახლდება მეორე ტრანზაქცია დაუყოვნებლივ მიიღებს ახალ მონაცემებს, მიუხედავად იმისა მეორე ტრანზაქციაში **COMMIT** დასრულდა თუ არა