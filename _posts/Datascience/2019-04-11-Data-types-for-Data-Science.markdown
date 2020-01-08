---
title: Data types for Data Science
permalink: /2019/04/11/data_types_for_data_science
---
### There and Back Again a DateTime Journey
**From String to date time**
* The `datetime` module is part of the Python standard library
* Use the `datetime type` from inside the `datetime` module
* `.strptime()` method converts from a string to a `datetime` object
```python
from datetime import datetime
parking_violations_date = '04/11/2019'
date_dt = datetime.strptime(parking_violation_date,'%m/%d%Y')
print(date_dt)
```
**Time Format Strings**

Directive|Meaning|Example
:--------:|:-----:|:-----:
%d | Day of the month as a zero-padded decimal number | 01, 02,...,31
%m | Month as a zero-padded decimal number | 01, 02,....,12
%Y | Year with century as a decimal number | 0001, 0002,...,9999

**Datetime to String**
* `strftime()` method uses a format string to convert a datetime object to a string
```python
In [1]: date_dt.strftime('%m/%d/%Y')
Out[1]: '04/11/2019'
```
* `isoformat()` method outputs a datetime as an ISO standard string
```python
In [1]: date_dt.isoformat()
Out[1]: '2019-04-11T00:00:00'
```
**What is the deal with now**
* `.now()` method returns the current local datetime
* `.utcnow()` method returns the current UTC datetime
```python
In [1]: from datetime import datetime

In [2]: local_dt = datetime.now()

In [3]: print(local_dt)
Out[3]: 2019-04-11 15:34:09.311971

In [4]: utc_dt = datetime.utcnow()

In [5]: print(utc_dt)
Out[5]: 2019-04-11 08:34:31.433155

```
**Time zones**
* Naive datetime objects have no timezone data
* Aware datetime objects have a timezone
* Timezone data is available via the `pytz` module via the `timezone` object
* Aware object have `.astimezone()` so you can get the time in another `timezone`
