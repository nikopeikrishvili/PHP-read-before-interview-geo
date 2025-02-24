
**Template Method** - ერთ-ერთი მნიშნვნელოვანი ქცევითი ტიპის დიზაინის პატერნია, რომელიც გამოიყენება ალგორითმის სტრუქტურის განსაზღვრას და მის ნაწილობრივ იმპლემენტაციას.

Template Method განსაკუთრებით სასარგებლოად, როდესაც გვაქვს საერთო პროცესის რამდენიმა ვარიანტი, რომელთა სტრუქტურა ერთნაირია, მაგრამ ზოგიეთი კონკრეტული ნაბიჯი განსზვავდება.

---

#### Template Method - ის პრინციპი

Template Method პატერნი გულისხმობს, რომ:

1. განვსაზღვროთ ალგორითმის ძირითადი სტრუქტურა აბსტრაქტულ კლასში.
2. ალგორითმის კონკრეტული ნაწილი ჭტამომავალ კლასებში გამოვყოთ, რათა მათი ქცევის დაკონფიგურირება იყოს შესაძლებელი.
3. ზოგადი ალგორითმი არ შეიცვლება, ხოლო შვილობილ კლასებს შეუძლიათ თავიანთი საჭიროებების მიხედვით შეცვალონ ან დაამატონ კონკრეტული ქმედებები.

---
#### Template Method - ის იმპლემენტაცია

დავუშვათ, რომ ვქმნით **რეპორტინგის სისტემას**, სადაც სხვადასხვა ფორმატის რეპორტის (CSV, HTML) გენერაცია უნდა შევძლოთ.

##### 1. აბსტრაქტული კლასი - ReportGenerator
```php
abstract class ReportGenerator {
    // Template Method – განსაზღვრავს ანგარიშის შექმნის ზოგად პროცესს
    public function generateReport() {
        $data = $this->fetchData();
        $formattedData = $this->formatData($data);
        $this->exportReport($formattedData);
    }

    // აბსტრაქტული მეთოდები, რომლებიც შვილობილ კლასებში უნდა განვსაზღვროთ
    abstract protected function fetchData();
    abstract protected function formatData($data);
    abstract protected function exportReport($formattedData);
}
```

აქ **generateReport()** არის **Template Method**, რომელიც განსაზღვრავს რეპორტის შექმნის საერთო სტრუქტურას.

##### 2. ფორმატის იმპლემენტაცია

```php
class CSVReport extends ReportGenerator {
    protected function fetchData() {
        return [
            ["ID", "Name", "Salary"],
            [1, "John Doe", 5000],
            [2, "Jane Smith", 6000]
        ];
    }

    protected function formatData($data) {
        $csv = "";
        foreach ($data as $row) {
            $csv .= implode(",", $row) . "\n";
        }
        return $csv;
    }

    protected function exportReport($formattedData) {
        file_put_contents("report.csv", $formattedData);
        echo "CSV ანგარიში შექმნილია!\n";
    }
}
```

##### 3. HTML რეპორტის იმპლემენტაცია

```php
class HTMLReport extends ReportGenerator {
    protected function fetchData() {
        return [
            ["ID" => 1, "Name" => "John Doe", "Salary" => 5000],
            ["ID" => 2, "Name" => "Jane Smith", "Salary" => 6000]
        ];
    }

    protected function formatData($data) {
        $html = "<table border='1'><tr><th>ID</th><th>Name</th><th>Salary</th></tr>";
        foreach ($data as $row) {
            $html .= "<tr><td>{$row['ID']}</td><td>{$row['Name']}</td><td>{$row['Salary']}</td></tr>";
        }
        $html .= "</table>";
        return $html;
    }

    protected function exportReport($formattedData) {
        file_put_contents("report.html", $formattedData);
        echo "HTML ანგარიში შექმნილია!\n";
    }
}
```


##### 4. Template Method - ის გამოყენება


```php
$csvReport = new CSVReport();
$csvReport->generateReport(); // ქმნის report.csv ფაილს

$htmlReport = new HTMLReport();
$htmlReport->generateReport(); // ქმნის report.html ფაილს
```

##### 5. შედეგი

```
CSV ანგარიში შექმნილია!
HTML ანგარიში შექმნილია!
```

---

#### Template Method ის უპირატესობები

✅ **კოდის განმეორების შემცირება** - ალგორითმის სტრუქტურა ერთ ადგილზე ინახება.    
✅ **გადამწყვეტი ნაბიჯების კონფიგურირებადობა** - შვილობილ კლასებს შეუძლეათ გადაწერონ (Override) კონკრეტული ნაბიჯები.    
✅ **კოდის მოქნილობა** - ახალი ანგარიშის ფორმატის დამატება შესაძლებელია მხოლოდ ახალი კლასის შექმნით, არსებული ლოგიკის ცვლილების გარეშე.

---

#### 🚀 დასკვნა

Template Method პატერნი განსაკუთრებით გამოსადეგია, როდესაც გვსურს, რომ ალგორითმის ძირითადი სტრუქტურა უცვლელი დარჩეს, ხოლო მისი ცალკეული ეტაპები კონკრეტულ იმპლემენტაციაში განსხვავდებოდეს. მისი გამოყენება მარტივია და კოდის ორგანიზებულობას მნიშვნელოვნად აუმჯობესებს.