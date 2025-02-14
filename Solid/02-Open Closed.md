**კლასები უნდა იყვნენ ღია გაფართოებისთვის, მაგრამ დახურული ცვლილებებისთვის.**
ეს ნიშნავს, რომ არსებული კოდის შეცვლის გარეშე უნდა შეგვეძლოს ფუნქციონალის დამატება.

**❌ ცუდი მაგალითი:**

```php
class Discount {
    public function calculate($amount, $type) {
        if ($type === 'gold') {
            return $amount * 0.8;
        } elseif ($type === 'silver') {
            return $amount * 0.9;
        }
        return $amount;
    }
}
```

**❌ თუ ახალი ტიპის ფასდაკლების დამატება დაგვჭირდება, კოდი უნდა შევცვალოთ


**✅ კარგი მაგალითი:**

```php
interface DiscountStrategy {
    public function apply($amount);
}

class GoldDiscount implements DiscountStrategy {
    public function apply($amount) {
        return $amount * 0.8;
    }
}

class SilverDiscount implements DiscountStrategy {
    public function apply($amount) {
        return $amount * 0.9;
    }
}

class Discount {
    public function calculate($amount, DiscountStrategy $strategy) {
        return $strategy->apply($amount);
    }
}
```

**✅ ახალი ფასდაკლების დამატება კოდის შეცვლის გარეშე შეგვიძლია.**
