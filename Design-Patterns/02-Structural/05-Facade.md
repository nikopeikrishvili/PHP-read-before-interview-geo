**Facade Pattern** არის **სტრუქტურული დიზაინ პატერნის** ტიპი, რომელიც გვთავაზობს გამარტივებულ ინტერფეისს კომპლექსულრ სისტემებთან ურთიერთობისთვის. იგი გამოიყენება როდესაც საჭიროა სისტემის შიდა სირთულეების დამალვა და მომხმარებლისთვის მარტივი და ინტიუციული API-ს შეთავაზება.

#### როდის გამოვიყენოთ Facade Pattern?
- როდესაც სისტემა შედგება ბევრი დამოკიდებულებისგან და მომხმარებეს სჭირდება მარტივი ინტერფეისი.
- როდესაც გვსურს არსებული კოდის ორგანიზება და სიმარტივის გაზრდა.
- როდესაც გვსურს მრავალშრიანი (Layered) არქიტექტურის დანერგვა.

---

#### Facade პატერნის იმპლემენტაცია

**წარმოვიდგინოთ სცენარი: ვიდეო ფაილის გადაყვანა სხვა ფორმატში**

##### 1. რთული ქვესისტემები (დამხმარე კლასი)

სისტემას აქვს სხვადასხვა კლასები, რომლებიც საჭიროა ვიდეოს კონვერტაციისთვის:

```php
<?php

class VideoFile {
    private string $filename;

    public function __construct(string $filename) {
        $this->filename = $filename;
    }

    public function getFilename(): string {
        return $this->filename;
    }
}

class CodecFactory {
    public static function getCodec(string $format) {
        if ($format === "mp4") {
            return new MP4Codec();
        } elseif ($format === "avi") {
            return new AVICodec();
        }
        throw new Exception("Unsupported format!");
    }
}

class MP4Codec {
    public function compress(VideoFile $file) {
        echo "Compressing {$file->getFilename()} to MP4 format.\n";
    }
}

class AVICodec {
    public function compress(VideoFile $file) {
        echo "Compressing {$file->getFilename()} to AVI format.\n";
    }
}

class AudioMixer {
    public function mix() {
        echo "Mixing audio...\n";
    }
}

class VideoConverter {
    public function convert(VideoFile $file, string $format) {
        echo "Converting {$file->getFilename()} to {$format}...\n";
        $codec = CodecFactory::getCodec($format);
        $codec->compress($file);
        (new AudioMixer())->mix();
        echo "Conversion complete!\n";
    }
}
```

ზემოთ მოცემული კლასები ურთიერთქმედებენ, მაგრამ ისინი დამოუკიდებელია და საკმაოდ რთულია მათი უშუალოდ გამოყენება.

---

##### 2. Facade კლასი: ვიდეო კონვერტაციის მარტივი API

```php
<?php

class VideoConversionFacade {
    private VideoConverter $converter;

    public function __construct() {
        $this->converter = new VideoConverter();
    }

    public function convertVideo(string $filename, string $format) {
        $file = new VideoFile($filename);
        $this->converter->convert($file, $format);
    }
}
```

---

##### 3. Facade ის გამოყენება 

```php
<?php

$converter = new VideoConversionFacade();
$converter->convertVideo("sample_video.mov", "mp4");

```

---

#### სარგებელი Facade Pattern ის გამოყენებით:

✅ **მარტივი ინტერფეისი** - მომხმარებელს მხოლოდ VideoConversionFacade კლასთან ურთიერთობს.    
✅ **შიდა სირთულეების დამალვა** - რთული ქვესისტემები მომხმარებლისთვის უხილავია.    
✅ **მოდულარობა** - შესაძლებელია ქვესისტემების შეცვლა Facade-ის API-ის შეცვლის გარეშე.    
✅ **უკეთესი კოდის ორგანიზება** - უფრო მარტივი და გასაგები კოდი.

---


#### 🚀 დასკვნა

**Facade Pattern** ძალიან გამოსადეგია, როდესაც გვსურს კომპლექსური სისტემის გამარტივება და მომხმარებლისთვის ინტუიციური ინტერფეისის შეთავაზება. PHP ში მისი გამოყენება განსაკუთრებით სასარგებლოად, როდესაც ვმუშაობთ მრავალშრიან არქიტექტურაზე, API-ებზე ან რთულ ბიზნეს ლოგიკაზე.