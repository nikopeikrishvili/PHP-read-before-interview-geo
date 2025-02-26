MySQL - ში **JOIN**-ები ძალიან მნიშვნელოვანია, რადგან ისინი საშუალებას აძლევენ მომხმარებელს, ერთი ან რამდენიმე ცხრილის მონაცემები დააკავშიროს და გააერთიანოს. ეს განსაკუთრებით მნიშვნელოვანი ხდება, როდესაც მონაცემები სხვადასხვა ცხრილებშია შენახული და საჭიროა მათი კომბინირება ლოგიკური კავშირის მიხედვით.

#### ცხრილები რომლებზეც ვივარჯიშებთ

ჩვენ გამოვიყენებთ ორ ცხრილს: **employees** და **departments**. 

##### employees ცხრილი

| id  | name  | department_id |
| --- | ----- | ------------- |
| 1   | Alice | 10            |
| 2   | Bob   | 20            |
| 3   | Carol | 10            |
| 4   | Niko  | 40            |
##### departments ცხრილი

| id  | name      |
| --- | --------- |
| 10  | HR        |
| 20  | IT        |
| 30  | Marketing |
| 50  | Legal     |
#### JOIN ტიპები

1. **JOIN**,**INNER JOIN**,**CROSS JOIN**
2. **LEFT JOIN**
3. **RIGHT JOIN**
4. **SELF JOIN**
5. **NATURAL JOIN**


#### JOIN,INNER JOIN,CROSS JOIN

**INNER JOIN,JOIN,CROSS JOIN** (სამივე ერთნაირად იქცევა) ერთ-ერთი ყველაზე ხშირად გამოყენებადი ტიპია. ისინი გააერთიანებენ ორი ცხრილის მონაცემებს მხოლოდ იმ შემთხვევაში როცა შესაბამისი მონაცემები ორივე ცხრილში არსებობს.
*სტანდარტულ SQL ში CROSS JOIN ში არ გამოიყენება **ON***


###### მაგალითი:
```sql
SELECT employees.name, departments.name
FROM employees
INNER JOIN departments
ON employees.department_id = departments.id;
```

###### შედეგი:
| name    | name      |
|---------|-----------|
| Alice   | HR        |
| Bob     | IT        |
| Carol   | HR        |

**შენიშვნა**: როგორც ვხედავთ ჩვენ დაგვიბრუნდა მონაცემები სადაც თანამშრომლებს და დეპარტამენტებს შორის კავშირი არსებობს, შედეგში არ დაბრუნდა თანამშრომელი **Niko** რადგანაც მისი **department_id** არის 40, და დეპარტამენტებში ჩვენ არ გვაქვს ჩანაწერი ასეთი იდენტიფიქატორით. არც დეპარტამენტი **Legal** დაბრუნდა departments ცხრილიდან, რადგანაც ჩვენი მონაცემების მიხედვით, არცერთი თანამშრომელი არ მუშაობს ამ დეპარტამენტში



#### LEFT JOIN (იგივე რაც LEFT OUTER JOIN)

**LEFT JOIN** იძლევა ყველა ჩანაწერს მარცცხენა (პირველ) ცხრილში და იმ ჩანაწერებს მარჯვენე (მეორე) ცხრილიდან, რომლებიც შესაბამისობაში არიან. თუ მარჯვენა ცხრილში არ არის შესაბამისი ჩანაწერი, მაშინ შედეგი იქნება NULL.

##### მგალითი:
```sql
SELECT employees.name, departments.name
FROM employees
LEFT JOIN departments
ON employees.department_id = departments.id;
```

##### შედეგი:
| name  | name |
| ----- | ---- |
| Alice | HR   |
| Bob   | IT   |
| Carol | HR   |
| Niko  | null |

**შენიშვნა:** როგორც ვხედავთ, ამ შემთხვევაში ჩანაწერი **Niko** დაბრუნდა თანამშრომლების ცხრილიდან, მაგრამ დეპარტამენტის სახელი არის **NULL**,
ასევე არ დაბრუნდა **Legal** დეპარტამენტი.

#### RIGHT JOIN (იგივე რაც RIGHT OUTER JOIN)

**RIGHT JOIN** მუშაობს ანალოგიურად როგორც **LEFT JOIN** მაგრამ ის გამოიტანს ყველა ჩანაწერს მარჯვენა ცხრილიდან, და თუ მარცხენაში დამთხვევას ვერ ნახავს ჩაანაცვლებს მას **NULL**-ით.

##### მაგალითი:

```sql
SELECT employees.name, departments.name  
FROM employees  
RIGHT JOIN departments  
ON employees.department_id = departments.id;  
```


##### შედეგი:

| name  | name      |
| ----- | --------- |
| Carol | HR        |
| Alice | HR        |
| Bob   | IT        |
| null  | Marketing |
| null  | Legal     |


**შენიშნვა**: როგორც ვხედავ ყველა ჩანაწერი **departments** ცხრილიდან დაბრუნდა, ხოლო თუ empoyees **ცხრილთან** ჩანაწერის დაკავშირება ვერ მოხერხდა, დაბრუნდა **NULL**..


#### FULL JOIN

**FULL JOIN** MySQL - ში პირდაპირ არ არის ხელმისაწვდომი, თუმცა მისი იმიტაცია შეიძლება გაკეთდეს **LEFT JOIN** და **RIGHT JOIN**-ის კომპბინაციით. 

##### მაგალითი (FULL JOIN ის სიმულაცია):

```sql
SELECT employees.name, departments.name
FROM employees
LEFT JOIN departments
ON employees.department_id = departments.id
UNION
SELECT employees.name, departments.name
FROM employees
RIGHT JOIN departments
ON employees.department_id = departments.id;
```

#####  შედეგი:
| name  | name      |
| ----- | --------- |
| Alice | HR        |
| Bob   | IT        |
| Carol | HR        |
| Niko  | null      |
| null  | Marketing |
| null  | Legal     |

**შენიშვნა**: მიაქციეთ ყურადღება რომ **LEFT** და **RIGHT** - **JOIN** შედეგების გასაერთიანებლად ჩვენ გამოვუყენეთ **UNION** ოპერატორი.
დაგვიბრუნდა ყველა ჩანაწერი **employees** ცხრილიდან ხოლო თუ შესაბამისი დეპარტამენტი არ მოიძებნა ამ შემთხვევაში დაბრუნდა **NULL**, ამის გაერთიანება მოხდა **RIGHT JOIN** რეზულტატთან, სადაც თუ თანამშრომელი ვერ მოიძებნა დეპარტამენტისთვის დაგვიბრუნდა **NULL**..

#### NATURAL JOIN

**NATURAL JOIN** - ი ცრილობს ცხრილებში თვითონ იპოვოს სვეტები ერთიდა იგივე სახელით და მათი კავშირით გვიბუნებს მონაცემებს. მოდი იმისთვის რომ დავინახოთ რეზულტატი ჩვენს **departments** ცხრილში გადავარქვათ **id** სვეტს სახელი და დავარქვათ **department_id** ზუსტად იგივე რაც **employee** ს ცხრილშა.

##### ჩასწორება 1 

```sql
ALTER TABLE departments
CHANGE COLUMN id department_id INT;
```


##### ჩასწორება 2:
```sql
ALTER TABLE departments  
CHANGE COLUMN name department_name VARCHAR(100);
```
თუ ამ ჩასწორებას არ შევიტანთ MySQL ეცდება რომ დაკავშიროს ცხრილები **employee.name** და **departments.name**

ახლა მოვიყვანოთ მაგალითი სადაც დავინახავთ **NATURAL JOIN** - ის მუშაობის პრინციპს
##### მაგალითი :

```sql
SELECT *  
FROM employees  
NATURAL JOIN departments;
```

##### შედეგი:
| department\_id | id  | name  | department\_name |
| -------------- | --- | ----- | ---------------- |
| 10             | 1   | Alice | HR               |
| 20             | 2   | Bob   | IT               |
| 10             | 3   | Carol | HR               |

**შენიშვნა**: ეს შედეგი იგივეა თუ ჩვენ შევასრულებდით

```sql
SELECT *  
FROM employees  
JOIN departments
on employees.department_id=departments.department_id;
```

და ასევე ექვივალენტია ამ Queryის :

```sql
SELECT *  
FROM employees  
 JOIN departments using(department_id);
```