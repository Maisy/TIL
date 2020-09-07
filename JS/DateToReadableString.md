# Parse date to readable string (feat. `date-fns`)
- date-fns@2.15.0 이상
- `formatDuration`을 사용하면 4 years 2 months 20 days ... 와 같이 나옴
- years -> yr / months -> mo 와 같이 표현하길원해서 이렇게짬


### goal
```
2016/01/15 12:00:00 ~ 2020/04/04 19:05:00 4 yr 2 mo
2020/01/15 12:00:00 ~ 2020/04/04 19:05:00 2 mo 20 days
2020/04/01 12:00:12 ~ 2020/04/04 19:05:00 3 days 7 hr'
2020/04/04 12:00:00 ~ 2020/04/04 19:05:00 7 hr 5 min'
2020/04/04 12:05:00 ~ 2020/04/04 19:05:00 7 hr
2020/04/04 19:00:12 ~ 2020/04/04 19:05:00 4 min 48 sec
2020/04/04 19:04:11 ~ 2020/04/04 19:05:00 49 sec
```

---
### code
```ts
import { format, intervalToDuration, Duration } from 'date-fns';

type DurationKey = 'years' | 'months' | 'days' | 'hours' | 'minutes' | 'seconds';
const unitLabelMap: { years: string; months: string; days: string; hours: string; minutes: string; seconds: string } = {
  years: 'yr',
  months: 'mo',
  days: 'days',
  hours: 'hr',
  minutes: 'min',
  seconds: 'sec',
};


const parseSimpleUnitDate = (durationObj: Duration, startUnitIndex: number) => {
  const unitList: string[] = Object.keys(unitLabelMap).slice(startUnitIndex, startUnitIndex + 2);
  return unitList
    .filter((unit: DurationKey) => durationObj[unit] !== 0)
    .map((unit: DurationKey) => `${durationObj[unit]} ${unitLabelMap[unit]}`)
    .join(' ');
};

const simpleReadableParser = (start: Date, end: Date) => {
  const duration = intervalToDuration({
    start,
    end,
  });

  const { years, months, days, hours } = duration;
  if (years) {
    return parseSimpleUnitDate(duration, 0);
  }
  if (months) {
    return parseSimpleUnitDate(duration, 1);
  }
  if (days) {
    return parseSimpleUnitDate(duration, 2);
  }
  if (hours) {
    return parseSimpleUnitDate(duration, 3);
  }
  return parseSimpleUnitDate(duration, 4);
};


const parseDate: (date: Date) => string = date => {
  return format(date, 'yyyy/MM/dd HH:mm:ss');
};

```

### test
```js
const fromList = [
  new Date(2020, 8, 1, 12, 0, 0),
  new Date(2016, 0, 15, 12, 0, 0), //4 years 2 months
  new Date(2020, 0, 15, 12, 0, 0), //2 months 20 days
  new Date(2020, 3, 1, 12, 0, 12), //3 days 7 hours
  new Date(2020, 3, 4, 12, 0, 0), //7 hours 5 minutes
  new Date(2020, 3, 4, 12, 5, 0), //7 hours
  new Date(2020, 3, 4, 19, 0, 12), //4 minutes 48 seconds
  new Date(2020, 3, 4, 19, 4, 11), // 49 seconds
]; 
const toTime = new Date(2020, 3, 4, 19, 5, 0);

console.log(
  fromList.map(fromDate => `${parseDate(fromDate)} ~ ${parseDate(toTime)} ${simpleReadableParser(fromDate, toTime)}`)
);
```
