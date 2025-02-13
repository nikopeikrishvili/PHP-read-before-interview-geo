
## 1. Single Responsibility Principle (SRP) - ერთიანი პასუხისმგებლობის პრინციპი

- **თითოეულ კლასს უნდა ჰქონდეს მხოლოდ ერთი პასუხისმგებლობა (მიზანი)**.
- **კლასს უნდა ქონდეს მხოლოდ ერთი მიზეზი შეცვლისთვის.**

**❌ ცუდი მაგალითი:**

```php
class Report {
    public function generateReport() {
        // გენერირება
    }

    public function saveToFile() {
        // ფაილში შენახვა
    }

    public function sendEmail() {
        // ელფოსტის გაგზავნა
    }
}
```

❌ **ამ კლასს სამი სხვადასხვა პასუხისმგებლობა აქვს.**

**✅ კარგი მაგალითი:** 

```php
class ReportGenerator {
    public function generateReport() {
        // გენერირება
    }
}

class FileSaver {
    public function saveToFile($report) {
        // ფაილში შენახვა
    }
}

class EmailSender {
    public function sendEmail($report) {
        // ელფოსტის გაგზავნა
    }
}
```

**✅ აქ, თითოეულ კლასს მხოლოდ ერთი პასუხისმგებლობა აქვს.** 