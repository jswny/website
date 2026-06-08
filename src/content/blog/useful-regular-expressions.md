---
title: "Useful Regular Expressions"
description: "A collection of regular expressions for common parsing tasks including time, phone numbers, ZIP codes, dates, and more."
pubDate: "2023-06-17T12:00:00-04:00"
heroImage: "/hero/useful-regex.jpg"
---
The following are some useful regular expressions that I've developed over time. These expressions assume that the string you are trying to match is complete. Happy parsing!

## Non-alphanumeric
Matches non-alphanumeric characters.
```javascript
([^a-zA-Z\d])+
```

## Time
Matches valid time strings in the format `HH:MM`.
```javascript
(([0-1][0-9])|(2[0-3])):([0-5][0-9])
```

## Phone
Matches phone numbers in the format `(XXX) XXX-XXXX`.
```javascript
\([0-9][0-9][0-9]\) [0-9][0-9][0-9]\-[0-9][0-9][0-9][0-9]
```

## Zip Code
Matches zip codes in the format `XXXXX` or `XXXXX-XXXX`.
```javascript
([0-9][0-9][0-9][0-9][0-9]\-[0-9][0-9][0-9][0-9])|([0-9][0-9][0-9][0-9][0-9])
```

## Comments
Matches multi-line comment strings which use `/*` and `*/` as separators, such as in Java.
```javascript
^(/\*)(.*)(\*/)
```

## Number Phrase
Matches numbers which are spelled out in words from zero to ninety-nine.
```javascript
((one)|(two)|(three)|(four)|(five)|(six)|(seven)|(eight)|(nine)|(ten)|(twenty)|(thirty)|(forty)|(fifty)|(sixty)|(seventy)|(eighty)|(ninety))(\-((one)|(two)|(three)|(four)|(five)|(six)|(seven)|(eight)|(nine))){0,1}
```

## Roman Numerals
Matches only Roman numerals from 0 to 39.
```javascript
(X{1,3})(I[XV]|V?I{0,3})|(I[XV]|V?I{1,3})|V
```

## Date
Matches date strings in the format of `YYYY-MM-DD` with support for leap years. I've written a guide on how this expression was derived [here](/blog/matching-valid-date-strings-with-regex/).
```javascript
(([0-9][0-9]((0[48])|([2468][048])|([13579][26])))(-)(((02)(-)((0[1-9])|([1-2][0-9])))|(((0[469])|11)(-)((0[1-9])|([1-2][0-9])|(30)))|(((0[13578])|1[02])(-)((0[1-9])|([1-2][0-9])|(3[0-1])))))|(([0-9][0-9]((0[1235679])|([2468][1235679])|([13579][1345789])))(-)(((02)(-)((0[1-9])|([1-2][0-8])))|(((0[469])|11)(-)((0[1-9])|([1-2][0-9])|(30)))|(((0[13578])|1[02])(-)((0[1-9])|([1-2][0-9])|(3[0-1])))))
```

## Even Parity
Matches numbers which have even parity (numbers whose digits add up to an even number).
```javascript
(([02468]*[13579]){2})*[02468]+
```
