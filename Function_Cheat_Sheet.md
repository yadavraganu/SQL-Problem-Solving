# SQL Server
```sql
-- ============================================
-- DATEPART: returns numeric values (integers)
-- ============================================
SELECT 
    GETDATE() AS CurrentDateTime,              -- Current system date and time

    DATEPART(YEAR, GETDATE())       AS YearPart,          -- Year number (e.g., 2026)
    DATEPART(QUARTER, GETDATE())    AS QuarterPart,       -- Quarter number (1–4)
    DATEPART(MONTH, GETDATE())      AS MonthPart,         -- Month number (1–12)
    DATEPART(DAY, GETDATE())        AS DayPart,           -- Day of month (1–31)
    DATEPART(DAYOFYEAR, GETDATE())  AS DayOfYearPart,     -- Day number in year (1–366)
    DATEPART(WEEK, GETDATE())       AS WeekOfYearPart,    -- Week number in year
    DATEPART(ISO_WEEK, GETDATE())   AS ISOWeekPart,       -- ISO 8601 week number (weeks start Monday)
    DATEPART(WEEKDAY, GETDATE())    AS WeekDayPart,       -- Day of week (1=Sunday by default)
    DATEPART(HOUR, GETDATE())       AS HourPart,          -- Hour of day (0–23)
    DATEPART(MINUTE, GETDATE())     AS MinutePart,        -- Minute (0–59)
    DATEPART(SECOND, GETDATE())     AS SecondPart,        -- Second (0–59)
    DATEPART(MILLISECOND, GETDATE()) AS MillisecondPart,  -- Milliseconds (0–999)
    DATEPART(MICROSECOND, SYSDATETIME()) AS MicrosecondPart, -- Microseconds (requires SYSDATETIME)
    DATEPART(NANOSECOND, SYSDATETIME())  AS NanosecondPart   -- Nanoseconds (requires SYSDATETIME)
;

-- ============================================
-- DATENAME: returns string values (names/text)
-- ============================================
SELECT 
    GETDATE() AS CurrentDateTime,                -- Current system date and time

    DATENAME(YEAR, GETDATE())     AS YearName,       -- Year as string ('2026')
    DATENAME(QUARTER, GETDATE())  AS QuarterName,    -- Quarter as string ('3')
    DATENAME(MONTH, GETDATE())    AS MonthName,      -- Full month name ('September')
    DATENAME(DAY, GETDATE())      AS DayName,        -- Day of month as string ('9')
    DATENAME(DAYOFYEAR, GETDATE()) AS DayOfYearName, -- Day number in year as string ('253')
    DATENAME(WEEK, GETDATE())     AS WeekName,       -- Week number as string ('37')
    DATENAME(ISO_WEEK, GETDATE()) AS ISOWeekName,    -- ISO week number as string ('37')
    DATENAME(WEEKDAY, GETDATE())  AS WeekDayName,    -- Day of week name ('Wednesday')
    DATENAME(HOUR, GETDATE())     AS HourName,       -- Hour as string ('7')
    DATENAME(MINUTE, GETDATE())   AS MinuteName,     -- Minute as string ('18')
    DATENAME(SECOND, GETDATE())   AS SecondName,     -- Second as string ('0')
    DATENAME(MILLISECOND, GETDATE()) AS MillisecondName, -- Milliseconds as string ('123')
    DATENAME(MICROSECOND, SYSDATETIME()) AS MicrosecondName, -- Microseconds as string
    DATENAME(NANOSECOND, SYSDATETIME())  AS NanosecondName   -- Nanoseconds as string
;

```
