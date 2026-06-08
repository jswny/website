---
title: "Matching Valid Date Strings with Regex"
description: "A walkthrough for building a regular expression that validates YYYY-MM-DD date strings, including leap years."
pubDate: "2023-06-17T12:00:00-04:00"
heroImage: "/hero/date-strings.jpg"
---
**Regular expressions** are an integral part to any programming language, especially when dealing with parsing. Let's take a look at how to match a date string in the format of `YYYY-MM-DD` using **regex**.

Let's start by defining exactly what we need to do. Dates are quirky, so we are going to have to handle some odd cases. First of all, we need to match strings in the format of `YYYY-MM-DD`, so in simpleton terms, four digits followed by a dash, followed by two digits, followed by another dash and lastly, two more digits. Here are the constraints we need to follow from this point on:

- `YYYY` Can be any combination four digits from **0** to **9** (we aren't limiting ourselves to the current year)
- `MM` Must be in the range of **1** to **12** for the twelve months
- `DD` Must be in the range of **1** to **31** for the possible 31 days in a month
- Dashes `-` in between each group of digits
- We must ensure that `DD` is in the correct range in the context of the month of the string. Those choices being **30** and **31** days for most months, and **28** and **29** days for February, depending on leap years.
- We must also ensure that leap years are handled correctly. That means making sure that February can only have **29** days if the year is divisible by 4.

Let's start by figuring out which months have which amounts of days, their digit equivalents, and an applicable regex which will match each group of months as `MM`:

<table>
  <tr>
    <td>
      # of Days
    </td>
    <td>
      Months
    </td>
    <td>
      Digit Equivalent
    </td>
    <td>
      Regex
    </td>
  </tr>
  <tr>
    <td>
      28
    </td>
    <td>
      Feb (no leap)
    </td>
    <td>
      02
    </td>
    <td>
      <code>(02)</code>
    </td>
  </tr>
  <tr>
    <td>
      29
    </td>
    <td>
      Feb (leap)
    </td>
    <td>
      02
    </td>
    <td>
      <code>(02)</code>
    </td>
  </tr>
  <tr>
    <td>
      30
    </td>
    <td>
      Apr, Jun, Sep, Nov
    </td>
    <td>
      04, 06, 09, 11
    </td>
    <td>
      <code>((0[469])|11)</code>
    </td>
  </tr>
  <tr>
    <td>
      31
    </td>
    <td>
      Jan, Mar, May, Jul, Aug, Oct, Dec
    </td>
    <td>
      01, 03, 05, 07, 08, 10, 12
    </td>
    <td>
      <code>((0[13578])|1[02])</code>
    </td>
  </tr>
</table>

28 Days - Feb (non-leap) - 02 - (02)
29 Days - Feb (leap) - 02 - (02)
30 Days - Apr, Jun, Sep, Nov - 04, 06, 09, 11 - ((0[469])|11)
31 Days - Jan, Mar, May, Jul, Aug, Oct, Dec - 01, 03, 05, 07, 08, 10, 12 - ((0[13578])|1[02])

Now, let's write a few small expressions to match each group of days as `DD`:

28 Days - `((0[1-9])|([1-2][0-8]))`
29 Days - `((0[1-9])|([1-2][0-9]))`
30 Days - `((0[1-9])|([1-2][0-9])|(30))`
31 Days - `((0[1-9])|([1-2][0-9])|(3[0-1]))`

Next, we need to add these two concepts together, including the dash, to generate four statements (one for each category of days). Each one will match the appropriate months with their appropriate amount of days. To do this, we simply combine the expressions that we generated to match the months with the ones that we generated to match the days:

28 Days w/ months - `((02)(-)((0[1-9])|([1-2][0-8])))`
29 Days w/ months - `((02)(-)((0[1-9])|([1-2][0-9])))`
30 Days w/ months - `(((0[469])|11)(-)((0[1-9])|([1-2][0-9])|(30)))`
31 Days w/ months - `(((0[13578])|1[02])(-)((0[1-9])|([1-2][0-9])|(3[0-1])))`

Now that we have handled each different combination of month and day, and written appropriate expressions for each, we need to move on to leap years. Leap years dictate that February can only have up to 29 days when the year is divisible by **4**. Therefore, our task will be to find a regular expression that can match years of four digits each that divide by **4** evenly. To do this, let's start by listing out a few of the numbers which are evenly divisible by **4**, starting from **2000**:

2000
2004
2008
2012
2016
2020
2024
2028
2032
2036
2040
2044
2048

Now, let's divide this list into sections based on the tens column and let's also highlight the last two digits of each number.

20**00**
20**04**
20**08**

20**12**
20**16**

20**20**
20**24**
20**28**

20**32**
20**36**

20**40**
20**44**
20**48**

You should notice a pattern here. Odd-numbered tens places alternate between **2** and **6** in the ones place, while even-numbered tens paces carry either a **0**, **4**, or **8**. We can use this to construct two simple expressions; one for matching two digits which are divisible by **4**, and one for the rest of the digits:

Last 2 digits of year (divisible by 4) - `((0[48])|([2468][048])|([13579][26]))`
Last 2 digits of year (not divisible by 4) - `((0[1235679])|([2468][1235679])|([13579][1345789]))`

Now, let's use this information to finish up leap year handling. We simply have to add `[0-9][0-9]` as the first two digits of the year (which can be anything).

Full year expression (divisible by 4) - `([0-9][0-9]((0[48])|([2468][048])|([13579][26])))`
Full year expression (not divisible by 4) - `([0-9][0-9]((0[1235679])|([2468][1235679])|([13579][1345789])))`

Finally, we have to combine what we've made so far into two different expressions; one for leap years, and one for all other years. To do this, we simply use the full year expression, a dash `-`, and the month and day combinations with or operators `|` between each (using the expression with 29 days in February for the leap year expression, and the expression for 28 days in February for the other expression):

Leap years full expression: 
```javascript
(([0-9][0-9]((0[48])|([2468][048])|([13579][26])))(-)(((02)(-)((0[1-9])|([1-2][0-9])))|(((0[469])|11)(-)((0[1-9])|([1-2][0-9])|(30)))|(((0[13578])|1[02])(-)((0[1-9])|([1-2][0-9])|(3[0-1])))))
```

Non-leap years full expression: 
```javascript
(([0-9][0-9]((0[1235679])|([2468][1235679])|([13579][1345789])))(-)(((02)(-)((0[1-9])|([1-2][0-8])))|(((0[469])|11)(-)((0[1-9])|([1-2][0-9])|(30)))|(((0[13578])|1[02])(-)((0[1-9])|([1-2][0-9])|(3[0-1])))))
```

Finally, we simply combine these two monstrosities together using another or operator `|`:

```javascript
(([0-9][0-9]((0[48])|([2468][048])|([13579][26])))(-)(((02)(-)((0[1-9])|([1-2][0-9])))|(((0[469])|11)(-)((0[1-9])|([1-2][0-9])|(30)))|(((0[13578])|1[02])(-)((0[1-9])|([1-2][0-9])|(3[0-1])))))|(([0-9][0-9]((0[1235679])|([2468][1235679])|([13579][1345789])))(-)(((02)(-)((0[1-9])|([1-2][0-8])))|(((0[469])|11)(-)((0[1-9])|([1-2][0-9])|(30)))|(((0[13578])|1[02])(-)((0[1-9])|([1-2][0-9])|(3[0-1])))))
```

Now we have a precise (but long) regular expression that will match date strings with support for leap years.
