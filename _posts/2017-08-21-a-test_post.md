---
layout: post
title:  "A Test Post"
date:   2017-08-21 12:24:13 +0300
categories: php
---

### Basic Usage

Just to give you the 10,000-foot view (:airplane:) of the package.

```php?start_inline=1
// create a gregorian date using PHP's built-in DateTime class
$gregorian = new DateTime('next monday');

// just pass it to Andegna\DateTime constractor and you will get $ethiopian date
$ethipic = new Andegna\DateTime($gregorian);
```

Format it

```php?start_inline=1
// format it
// ሰኞ፣ ግንቦት ፯ ቀን (ሥላሴ) 00:00:00 እኩለ፡ሌሊት EAT ፳፻፱ (ማርቆስ) ዓ/ም
echo $ethipic->format(Andegna\Constants::DATE_GEEZ_ORTHODOX);

// ሰኞ፣ ግንቦት 07 ቀን 00:00:00 እኩለ፡ሌሊት EAT 2009 ዓ/ም
echo $ethipic->format(Andegna\Constants::DATE_ETHIOPIAN);
```