# 1. DATE & TIME FUNCTIONS

## 1.1 Current Date/Time: GETDATE, SYSDATETIME, UTC & Offset
```sql
SELECT
    GETDATE()           AS GetDateValue,        -- datetime, local server time (~3 ms precision)
    SYSDATETIME()       AS SysDateTimeValue,    -- datetime2(7), local server time (100 ns precision)
    GETUTCDATE()        AS GetUtcDateValue,     -- datetime, UTC time
    SYSUTCDATETIME()    AS SysUtcDateTimeValue, -- datetime2(7), UTC time
    SYSDATETIMEOFFSET() AS SysDtOffsetValue,    -- datetimeoffset(7), local time with time zone offset
    CURRENT_TIMESTAMP   AS CurrentTimestampVal, -- ANSI-standard equivalent of GETDATE()
    CAST(GETDATE() AS DATE) AS TodayDateOnly,   -- Date only (time removed)
    CAST(GETDATE() AS TIME) AS NowTimeOnly      -- Time only (date removed)
;
```

## 1.2 DATEPART: Returns Numeric Parts (int)
```sql
SELECT 
    GETDATE() AS CurrentDateTime,                          -- Current system date and time

    DATEPART(YEAR, GETDATE())            AS YearPart,          -- Year number (e.g., 2026)
    DATEPART(QUARTER, GETDATE())         AS QuarterPart,       -- Quarter number (1–4)
    DATEPART(MONTH, GETDATE())           AS MonthPart,         -- Month number (1–12)
    DATEPART(DAY, GETDATE())             AS DayPart,           -- Day of month (1–31)
    DATEPART(DAYOFYEAR, GETDATE())       AS DayOfYearPart,     -- Day number in year (1–366)
    DATEPART(WEEK, GETDATE())            AS WeekOfYearPart,    -- Week number in year
    DATEPART(ISO_WEEK, GETDATE())        AS ISOWeekPart,       -- ISO 8601 week number (weeks start Monday)
    DATEPART(WEEKDAY, GETDATE())         AS WeekDayPart,       -- Day of week (1=Sunday by default, see @@DATEFIRST)
    DATEPART(HOUR, GETDATE())            AS HourPart,          -- Hour of day (0–23)
    DATEPART(MINUTE, GETDATE())          AS MinutePart,        -- Minute (0–59)
    DATEPART(SECOND, GETDATE())          AS SecondPart,        -- Second (0–59)
    DATEPART(MILLISECOND, GETDATE())     AS MillisecondPart,   -- Milliseconds (0–999)
    DATEPART(MICROSECOND, SYSDATETIME()) AS MicrosecondPart,   -- Microseconds (requires SYSDATETIME)
    DATEPART(NANOSECOND, SYSDATETIME())  AS NanosecondPart,    -- Nanoseconds (requires SYSDATETIME)
    DATEPART(TZOFFSET, SYSDATETIMEOFFSET()) AS TzOffsetMinutes -- Time zone offset in minutes (e.g., 330 for IST)
;
```

## 1.3 DATENAME: Returns Text Parts (string)
```sql
SELECT 
    GETDATE() AS CurrentDateTime,                          -- Current system date and time

    DATENAME(YEAR, GETDATE())            AS YearName,          -- Year as string ('2026')
    DATENAME(QUARTER, GETDATE())         AS QuarterName,       -- Quarter as string ('3')
    DATENAME(MONTH, GETDATE())           AS MonthName,         -- Full month name ('September')
    DATENAME(DAY, GETDATE())             AS DayName,           -- Day of month as string ('9')
    DATENAME(DAYOFYEAR, GETDATE())       AS DayOfYearName,     -- Day number in year as string ('253')
    DATENAME(WEEK, GETDATE())            AS WeekName,          -- Week number as string ('37')
    DATENAME(ISO_WEEK, GETDATE())        AS ISOWeekName,       -- ISO week number as string ('37')
    DATENAME(WEEKDAY, GETDATE())         AS WeekDayName,       -- Day of week name ('Wednesday')
    DATENAME(HOUR, GETDATE())            AS HourName,          -- Hour as string ('7')
    DATENAME(MINUTE, GETDATE())          AS MinuteName,        -- Minute as string ('18')
    DATENAME(SECOND, GETDATE())          AS SecondName,        -- Second as string ('0')
    DATENAME(MILLISECOND, GETDATE())     AS MillisecondName,   -- Milliseconds as string ('123')
    DATENAME(MICROSECOND, SYSDATETIME()) AS MicrosecondName,   -- Microseconds as string
    DATENAME(NANOSECOND, SYSDATETIME())  AS NanosecondName     -- Nanoseconds as string
;
```

## 1.4 YEAR, MONTH, DAY, ISDATE: Shorthand & Validation
```sql
SELECT
    YEAR(GETDATE())      AS YearNum,       -- Same as DATEPART(YEAR, ...)
    MONTH(GETDATE())     AS MonthNum,      -- Same as DATEPART(MONTH, ...)
    DAY(GETDATE())       AS DayNum,        -- Same as DATEPART(DAY, ...)
    ISDATE('2026-09-09') AS IsValidDate,   -- 1 = valid date string
    ISDATE('2026-13-45') AS IsInvalidDate  -- 0 = not a valid date
;
```

## 1.5 DATEADD: Add / Subtract an Interval
```sql
SELECT
    DATEADD(DAY, 7, GETDATE())      AS PlusSevenDays,     -- 7 days from now
    DATEADD(DAY, -30, GETDATE())    AS MinusThirtyDays,   -- 30 days ago (negative number subtracts)
    DATEADD(MONTH, 1, GETDATE())    AS PlusOneMonth,      -- Same day next month (adjusts for short months)
    DATEADD(YEAR, -1, GETDATE())    AS MinusOneYear,      -- Same day last year
    DATEADD(QUARTER, 1, GETDATE())  AS PlusOneQuarter,    -- 3 months ahead
    DATEADD(WEEK, 2, GETDATE())     AS PlusTwoWeeks,      -- 14 days ahead
    DATEADD(HOUR, 5, GETDATE())     AS PlusFiveHours,     -- 5 hours ahead
    DATEADD(MINUTE, -15, GETDATE()) AS MinusFifteenMins   -- 15 minutes ago
;
```

## 1.6 DATEDIFF & DATEDIFF_BIG: Count of Datepart BOUNDARIES Crossed
```sql
SELECT
    DATEDIFF(DAY,    '2026-01-01', '2026-09-09')                 AS DaysBetween,    -- Days between two dates
    DATEDIFF(MONTH,  '2026-01-31', '2026-02-01')                 AS MonthsBetween,  -- 1 (month boundary crossed, only 1 day apart!)
    DATEDIFF(YEAR,   '2025-12-31', '2026-01-01')                 AS YearsBetween,   -- 1 (year boundary crossed)
    DATEDIFF(HOUR,   '2026-09-09 08:00', '2026-09-09 17:30')     AS HoursBetween,   -- 9
    DATEDIFF(MINUTE, '2026-09-09 08:00', '2026-09-09 17:30')     AS MinutesBetween, -- 570
    DATEDIFF_BIG(MILLISECOND, '2020-01-01', GETDATE())           AS BigMsBetween    -- bigint result for large ranges
;
```

## 1.7 EOMONTH, DATEFROMPARTS & Friends: Build Dates & Find Boundaries
```sql
SELECT
    EOMONTH(GETDATE())              AS EndOfThisMonth,    -- Last day of current month
    EOMONTH(GETDATE(), 1)           AS EndOfNextMonth,    -- Last day of next month (2nd arg = month offset)
    EOMONTH(GETDATE(), -1)          AS EndOfLastMonth,    -- Last day of previous month
    DATEADD(DAY, 1, EOMONTH(GETDATE(), -1)) AS StartOfThisMonth, -- First day of current month

    DATEFROMPARTS(2026, 9, 9)                          AS BuiltDate,       -- date from Y, M, D
    TIMEFROMPARTS(14, 30, 0, 0, 0)                     AS BuiltTime,       -- time from h, min, sec, fractions, precision
    SMALLDATETIMEFROMPARTS(2026, 9, 9, 14, 30)         AS BuiltSmallDt,    -- smalldatetime from y, m, d, h, min
    DATETIMEFROMPARTS(2026, 9, 9, 14, 30, 0, 0)        AS BuiltDateTime,   -- datetime from y, m, d, h, min, sec, ms
    DATETIME2FROMPARTS(2026, 9, 9, 14, 30, 0, 0, 7)    AS BuiltDateTime2,  -- datetime2 from y, m, d, h, min, sec, fractions, precision
    DATETIMEOFFSETFROMPARTS(2026, 9, 9, 14, 30, 0, 0, 5, 30, 7) AS BuiltDtOffset -- ... + offset hours, offset minutes, precision
;
```

## 1.8 DATETRUNC & DATE_BUCKET (2022+): Truncate and Bucket Dates
```sql
SELECT
    DATETRUNC(YEAR, GETDATE())          AS TruncToYear,     -- (2022+) Jan 1 of this year
    DATETRUNC(QUARTER, GETDATE())       AS TruncToQuarter,  -- (2022+) First day of this quarter
    DATETRUNC(MONTH, GETDATE())         AS TruncToMonth,    -- (2022+) First day of this month
    DATETRUNC(WEEK, GETDATE())          AS TruncToWeek,     -- (2022+) First day of this week (per @@DATEFIRST)
    DATETRUNC(HOUR, GETDATE())          AS TruncToHour,     -- (2022+) Minutes/seconds zeroed
    DATE_BUCKET(WEEK, 1, GETDATE())     AS WeekBucket,      -- (2022+) Start of the 1-week bucket containing the date
    DATE_BUCKET(DAY, 15, GETDATE())     AS FifteenDayBucket -- (2022+) Start of the 15-day bucket
;

-- Pre-2022 equivalents for start of day / month / year
SELECT
    DATEADD(DAY,   DATEDIFF(DAY,   0, GETDATE()), 0) AS StartOfToday,
    DATEADD(MONTH, DATEDIFF(MONTH, 0, GETDATE()), 0) AS StartOfMonth,
    DATEADD(YEAR,  DATEDIFF(YEAR,  0, GETDATE()), 0) AS StartOfYear
;
```

## 1.9 Time Zone Functions: AT TIME ZONE, SWITCHOFFSET, TODATETIMEOFFSET
```sql
SELECT
    SYSDATETIMEOFFSET()                                        AS LocalWithOffset,   -- Server local time + offset
    SWITCHOFFSET(SYSDATETIMEOFFSET(), '+05:30')                AS ToIST_Offset,      -- Same instant, shown in a different offset
    TODATETIMEOFFSET(GETDATE(), '+05:30')                      AS AttachOffset,      -- Attach offset to a datetime (instant changes)
    GETUTCDATE() AT TIME ZONE 'UTC'                            AS TagAsUtc,          -- Mark a UTC value as UTC (datetimeoffset)
    GETUTCDATE() AT TIME ZONE 'UTC' AT TIME ZONE 'India Standard Time' AS UtcToIST   -- Convert UTC -> IST (use Windows zone names)
;

-- List valid time zone names
SELECT name, current_utc_offset, is_currently_dst FROM sys.time_zone_info;
```

## 1.10 Date Utility: @@DATEFIRST
```sql
SELECT
    @@DATEFIRST AS FirstDayOfWeek,  -- 7 = Sunday (US default), 1 = Monday; drives DATEPART(WEEKDAY) and DATETRUNC(WEEK)
    @@LANGUAGE  AS CurrentLanguage  -- Session language (affects month/day names and date formats)
;
```

---

# 2. FORMATTING FUNCTIONS

## 2.1 FORMAT: .NET-Style Formatting (returns nvarchar)
```sql
SELECT
    FORMAT(GETDATE(), 'yyyy-MM-dd')           AS IsoDate,        -- 2026-09-09
    FORMAT(GETDATE(), 'dd/MM/yyyy')           AS DayFirstDate,   -- 09/09/2026
    FORMAT(GETDATE(), 'MMM dd, yyyy')         AS ShortMonthDate, -- Sep 09, 2026
    FORMAT(GETDATE(), 'dddd, MMMM d, yyyy')   AS LongDate,       -- Wednesday, September 9, 2026
    FORMAT(GETDATE(), 'yyyy-MM-dd HH:mm:ss')  AS DateTime24h,    -- 24-hour clock (HH)
    FORMAT(GETDATE(), 'hh:mm tt')             AS Time12h,        -- 12-hour clock with AM/PM
    FORMAT(GETDATE(), 'yyyyMM')               AS YearMonthKey,   -- 202609 (handy for grouping)
    FORMAT(1234567.891, 'N2')                 AS NumberFmt,      -- 1,234,567.89
    FORMAT(1234567.891, 'C', 'en-US')         AS CurrencyUS,     -- $1,234,567.89
    FORMAT(1234567.891, 'C', 'en-IN')         AS CurrencyIN,     -- ₹12,34,567.89
    FORMAT(0.256, 'P1')                       AS PercentFmt,     -- 25.6 %
    FORMAT(7, '000')                          AS ZeroPadded      -- 007
;
```

## 2.2 CONVERT Style Codes: Date → String
```sql
SELECT
    CONVERT(VARCHAR(10), GETDATE(), 101) AS Style101_USA,      -- mm/dd/yyyy
    CONVERT(VARCHAR(10), GETDATE(), 103) AS Style103_British,  -- dd/mm/yyyy
    CONVERT(VARCHAR(10), GETDATE(), 104) AS Style104_German,   -- dd.mm.yyyy
    CONVERT(VARCHAR(10), GETDATE(), 105) AS Style105_Italian,  -- dd-mm-yyyy
    CONVERT(VARCHAR(10), GETDATE(), 110) AS Style110_USADash,  -- mm-dd-yyyy
    CONVERT(VARCHAR(10), GETDATE(), 111) AS Style111_Japan,    -- yyyy/mm/dd
    CONVERT(VARCHAR(8),  GETDATE(), 112) AS Style112_ISOBasic, -- yyyymmdd (no separators)
    CONVERT(VARCHAR(10), GETDATE(), 120) AS Style120_ODBC,     -- yyyy-mm-dd (first 10 chars of full style)
    CONVERT(VARCHAR(19), GETDATE(), 120) AS Style120_Full,     -- yyyy-mm-dd hh:mi:ss
    CONVERT(VARCHAR(23), GETDATE(), 121) AS Style121_Milli,    -- yyyy-mm-dd hh:mi:ss.mmm
    CONVERT(VARCHAR(8),  GETDATE(), 108) AS Style108_Time      -- hh:mi:ss
;
```

## 2.3 STR & FORMATMESSAGE: Number and Template Formatting
```sql
SELECT
    STR(123.456, 7, 2)                            AS StrValue,       -- '  123.46' (total length 7, 2 decimals, right-aligned)
    LTRIM(STR(123.456, 7, 2))                     AS StrTrimmed,     -- '123.46'
    FORMATMESSAGE('Hi %s, you have %d items', 'Asha', 3) AS MessageFmt -- 'Hi Asha, you have 3 items' (%s string, %d int)
;
```

---

# 3. STRING FUNCTIONS

## 3.1 Core String Functions
```sql
SELECT
    LEN('  Hello  ')                  AS LenValue,        -- 7 (ignores trailing spaces, counts leading)
    DATALENGTH('Hello')               AS DataLengthValue, -- 5 bytes (counts trailing spaces; NVARCHAR = 2 bytes/char)
    LEFT('SQL Server', 3)             AS LeftChars,       -- 'SQL'
    RIGHT('SQL Server', 6)            AS RightChars,      -- 'Server'
    SUBSTRING('SQL Server', 5, 6)     AS SubstringValue,  -- 'Server' (start position is 1-based, length = 6)
    CHARINDEX('Server', 'SQL Server') AS CharIndexValue,  -- 5 (position of first match, 0 if not found)
    CHARINDEX('S', 'SQL Server', 2)   AS CharIndexFrom,   -- 5 (3rd arg = start search position)
    PATINDEX('%[0-9]%', 'abc123')     AS PatIndexValue,   -- 4 (position of first digit; supports wildcards)
    REPLACE('2026-09-09', '-', '/')   AS ReplaceValue,    -- '2026/09/09'
    STUFF('ABCDEF', 2, 3, 'xyz')      AS StuffValue,      -- 'AxyzEF' (delete 3 chars from pos 2, insert 'xyz')
    UPPER('sql')                      AS UpperValue,      -- 'SQL'
    LOWER('SQL')                      AS LowerValue,      -- 'sql'
    REVERSE('abc')                    AS ReverseValue,    -- 'cba'
    REPLICATE('ab', 3)                AS ReplicateValue,  -- 'ababab'
    SPACE(5)                          AS FiveSpaces,      -- 5 blank characters
    TRANSLATE('2*[3+4]', '[]*', '()+') AS TranslateValue  -- (2017+) Character-by-character swap -> '2+(3+4)'
;
```

## 3.2 Character Code & Phonetic Functions
```sql
SELECT
    ASCII('A')                        AS AsciiValue,      -- 65
    CHAR(65)                          AS CharValue,       -- 'A'
    UNICODE(N'€')                     AS UnicodeValue,    -- 8364
    NCHAR(8364)                       AS NCharValue,      -- '€'
    QUOTENAME('My Table')             AS QuoteNameValue,  -- '[My Table]' (safe object name quoting)
    QUOTENAME('O''Brien', '''')       AS QuoteNameStr,    -- 'O''Brien' (quote as string literal)
    SOUNDEX('Smith')                  AS SoundexValue,    -- 'S530' (phonetic code)
    DIFFERENCE('Smith', 'Smyth')      AS DifferenceValue  -- 0–4 similarity of SOUNDEX codes (4 = very similar)
;
```

## 3.3 TRIM, LTRIM, RTRIM
```sql
SELECT
    LTRIM('   abc   ')       AS LeftTrim,   -- 'abc   ' (removes leading spaces)
    RTRIM('   abc   ')       AS RightTrim,  -- '   abc' (removes trailing spaces)
    TRIM('   abc   ')        AS BothTrim,   -- (2017+) 'abc' (removes both sides)
    TRIM('#' FROM '##abc##') AS TrimChar,   -- (2017+) 'abc' (trim specific characters)
    TRIM(LEADING '0' FROM '000123')  AS TrimLeading, -- (2022+) '123'
    TRIM(TRAILING '.' FROM 'abc...') AS TrimTrailing -- (2022+) 'abc'
;
```

## 3.4 Concatenation: +, CONCAT, CONCAT_WS
```sql
SELECT
    'John' + ' ' + 'Smith'              AS PlusConcat,      -- 'John Smith' (NULL + anything = NULL)
    CONCAT('John', ' ', 'Smith')        AS ConcatValue,     -- 'John Smith' (treats NULL as empty string)
    CONCAT('A', NULL, 'B')              AS ConcatWithNull,  -- 'AB'
    CONCAT_WS(', ', 'Pune', 'MH', 'IN') AS ConcatWsValue,   -- (2017+) 'Pune, MH, IN' (separator first; skips NULLs)
    CONCAT_WS('-', 'A', NULL, 'C')      AS ConcatWsNull     -- 'A-C' (NULL skipped, no double separator)
;
```

## 3.5 STRING_AGG: Combine Rows into One String (2017+)
```sql
SELECT DepartmentID,
       STRING_AGG(EmployeeName, ', ') WITHIN GROUP (ORDER BY EmployeeName) AS EmployeeList -- 'Asha, Ravi, Sam' (NULLs skipped)
FROM Employees
GROUP BY DepartmentID;
```

## 3.6 STRING_SPLIT: Split a String into Rows (2016+)
```sql
SELECT value AS Item
FROM STRING_SPLIT('red,green,blue', ',');         -- 3 rows: red / green / blue (output column is "value")

SELECT value, ordinal
FROM STRING_SPLIT('red,green,blue', ',', 1);      -- (2022+) 3rd arg = 1 adds an ordinal (position) column
```

## 3.7 STRING_ESCAPE, COMPRESS & DECOMPRESS
```sql
SELECT
    STRING_ESCAPE('He said "hi"', 'json')                  AS JsonEscaped,  -- (2016+) He said \"hi\"
    COMPRESS('some long text')                             AS Compressed,   -- (2016+) varbinary (GZIP)
    CAST(DECOMPRESS(COMPRESS('some long text')) AS VARCHAR(100)) AS Restored -- 'some long text' (cast the varbinary back)
;
```

## 3.8 GREATEST & LEAST: Row-Wise Max / Min (2022+)
```sql
SELECT
    GREATEST(10, 25, 7)         AS GreatestValue,  -- (2022+) 25 (largest of the arguments in one row)
    LEAST(10, 25, 7)            AS LeastValue,     -- (2022+) 7
    GREATEST('a', 'c', 'b')     AS GreatestText    -- 'c'
;
```

---

# 4. NUMERIC / MATH FUNCTIONS

## 4.1 Rounding, Power, Logs & Random
```sql
SELECT
    ABS(-25.5)           AS AbsValue,       -- 25.5
    CEILING(12.1)        AS CeilingValue,   -- 13 (round up)
    FLOOR(12.9)          AS FloorValue,     -- 12 (round down)
    ROUND(123.4567, 2)   AS Round2Dec,      -- 123.4600 (2 decimal places)
    ROUND(123.4567, 0)   AS Round0Dec,      -- 123.0000
    ROUND(1234.5, -2)    AS RoundNegative,  -- 1200.0 (round to nearest hundred)
    ROUND(123.4567, 2, 1) AS RoundTruncate, -- 123.4500 (3rd arg <> 0 truncates instead of rounding)
    POWER(2, 10)         AS PowerValue,     -- 1024
    SQRT(144)            AS SqrtValue,      -- 12
    SQUARE(5)            AS SquareValue,    -- 25
    SIGN(-8)             AS SignValue,      -- -1 (negative), 0 (zero), 1 (positive)
    EXP(1)               AS ExpValue,       -- 2.71828... (e^1)
    LOG(100)             AS NaturalLog,     -- 4.60517... (base e)
    LOG(8, 2)            AS LogBase2,       -- 3 (second arg = base)
    LOG10(100)           AS Log10Value,     -- 2
    17 % 5               AS ModuloValue,    -- 2 (remainder)
    RAND()               AS RandomFloat,    -- Random float between 0 and 1
    RAND(42)             AS SeededRandom,   -- Repeatable random using a seed
    CRYPT_GEN_RANDOM(4)  AS SecureRandom,   -- 4 cryptographically strong random bytes (varbinary)
    CAST(5 AS DECIMAL(10,2)) / 2 AS DecimalDivision -- 2.50 (integer / integer = integer, so cast first!)
;
```

## 4.2 Trigonometric Functions
```sql
SELECT
    PI()            AS PiValue,       -- 3.14159265358979
    SIN(PI()/2)     AS SinValue,      -- 1
    COS(0)          AS CosValue,      -- 1
    TAN(PI()/4)     AS TanValue,      -- ~1
    COT(PI()/4)     AS CotValue,      -- ~1
    ASIN(1)         AS AsinValue,     -- 1.5708 (PI/2)
    ACOS(1)         AS AcosValue,     -- 0
    ATAN(1)         AS AtanValue,     -- 0.7854 (PI/4)
    ATN2(1, 1)      AS Atn2Value,     -- 0.7854 (angle of point (x=1, y=1); args are (y, x))
    DEGREES(PI())   AS DegreesValue,  -- 180
    RADIANS(180)    AS RadiansValue   -- 3.14159...
;
```

---

# 5. DATA TYPE CONVERSION FUNCTIONS

## 5.1 CAST, CONVERT, TRY_CAST, TRY_CONVERT, PARSE
```sql
SELECT
    CAST('123' AS INT)                  AS CastToInt,        -- 123 (ANSI standard)
    CONVERT(INT, '123')                 AS ConvertToInt,     -- 123 (SQL Server specific, supports style codes)
    CAST(GETDATE() AS DATE)             AS CastToDate,       -- Date only
    CAST(123.456 AS INT)                AS CastDecimalToInt, -- 123 (truncates, does not round)
    TRY_CAST('abc' AS INT)              AS TryCastFail,      -- NULL instead of error
    TRY_CONVERT(DATE, '2026-13-45')     AS TryConvertFail,   -- NULL instead of error
    TRY_CONVERT(DATE, '2026-09-09')     AS TryConvertOk,     -- 2026-09-09
    PARSE('09/09/2026' AS DATE USING 'en-GB')  AS ParseValue,   -- Culture-aware parse (slow; avoid on large tables)
    TRY_PARSE('xyz' AS DATE USING 'en-US')     AS TryParseFail, -- NULL on failure
    ISNUMERIC('123.45')                 AS IsNumericValue    -- 1 (caution: also returns 1 for '$', ',', '1e5'; prefer TRY_CAST)
;
```

## 5.2 SQL_VARIANT_PROPERTY & TYPE_NAME: Inspect Types
```sql
SELECT
    SQL_VARIANT_PROPERTY(CAST(5 AS SQL_VARIANT), 'BaseType') AS BaseType, -- 'int'
    TYPE_NAME(TYPE_ID('int'))                                AS TypeName, -- 'int'
    TYPE_ID('varchar')                                       AS TypeId    -- Numeric type id
;
```

---

# 6. NULL HANDLING & CONDITIONAL FUNCTIONS

## 6.1 ISNULL, COALESCE, NULLIF, IIF, CHOOSE, CASE
```sql
SELECT
    ISNULL(NULL, 'Default')              AS IsNullValue,    -- 'Default' (exactly 2 args; result takes type of 1st arg)
    COALESCE(NULL, NULL, 'Third', 'Fourth') AS CoalesceValue, -- 'Third' (first non-NULL from any number of args)
    NULLIF(10, 10)                       AS NullIfEqual,    -- NULL (returns NULL if both args are equal)
    NULLIF(10, 0)                        AS NullIfNotEqual, -- 10
    100 / NULLIF(0, 0)                   AS SafeDivide,     -- NULL instead of divide-by-zero error
    IIF(5 > 3, 'Yes', 'No')              AS IifValue,       -- 'Yes' (inline IF)
    CHOOSE(2, 'Low', 'Medium', 'High')   AS ChooseValue,    -- 'Medium' (picks item by 1-based index)
    CASE 
        WHEN 85 >= 90 THEN 'A'
        WHEN 85 >= 80 THEN 'B'
        ELSE 'C'
    END                                  AS CaseValue       -- 'B' (searched CASE; first true branch wins)
;
```

## 6.2 ISNULL vs COALESCE: Quick Difference
```sql
SELECT
    ISNULL(NULL, 'abcdef')   AS IsNullResult,   -- Type follows 1st arg; a short 1st arg type can truncate the value
    COALESCE(NULL, 'abcdef') AS CoalesceResult  -- Type follows highest-precedence arg; ANSI standard; multi-arg
;
```

---

# 7. AGGREGATE FUNCTIONS

## 7.1 Standard Aggregates
```sql
SELECT
    COUNT(*)                  AS TotalRows,        -- Counts all rows (including NULLs)
    COUNT(Salary)             AS NonNullSalaries,  -- Counts non-NULL values only
    COUNT(DISTINCT DeptID)    AS UniqueDepts,      -- Counts unique non-NULL values
    COUNT_BIG(*)              AS TotalRowsBig,     -- Same as COUNT but returns bigint
    SUM(Salary)               AS TotalSalary,      -- Sum (ignores NULLs)
    AVG(Salary)               AS AvgSalary,        -- Average (ignores NULLs; integer columns give integer average!)
    AVG(CAST(Salary AS DECIMAL(18,2))) AS AvgSalaryDec, -- Cast to keep decimals
    MIN(Salary)               AS MinSalary,        -- Smallest value
    MAX(Salary)               AS MaxSalary,        -- Largest value
    STDEV(Salary)             AS SampleStdDev,     -- Sample standard deviation
    STDEVP(Salary)            AS PopulationStdDev, -- Population standard deviation
    VAR(Salary)               AS SampleVariance,   -- Sample variance
    VARP(Salary)              AS PopulationVariance -- Population variance
FROM Employees;
```

## 7.2 Approximate & Checksum Aggregates
```sql
SELECT
    APPROX_COUNT_DISTINCT(CustomerID) AS ApproxDistinct, -- (2019+) Fast estimate of distinct count on huge tables
    APPROX_PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY Amount) AS ApproxMedian, -- (2022+) Fast approximate median
    APPROX_PERCENTILE_DISC(0.9) WITHIN GROUP (ORDER BY Amount) AS ApproxP90,    -- (2022+) Fast approximate 90th percentile
    CHECKSUM_AGG(CAST(Amount AS INT)) AS TableChecksum   -- Checksum of a column (detect changes in a set of values)
FROM Orders;
```

## 7.3 GROUPING & GROUPING_ID: Identify Subtotal Rows
```sql
SELECT DeptID, JobTitle, SUM(Salary) AS TotalSalary,
       GROUPING(DeptID)              AS IsDeptSubtotal,  -- 1 when DeptID is aggregated in this row (ROLLUP/CUBE), else 0
       GROUPING_ID(DeptID, JobTitle) AS GroupLevel       -- Bitmask: 0 = detail, 1 = JobTitle rolled up, 3 = grand total
FROM Employees
GROUP BY ROLLUP (DeptID, JobTitle);
```

---

# 8. WINDOW (ANALYTIC) FUNCTIONS

## 8.1 Ranking Functions
```sql
-- Syntax: function() OVER (PARTITION BY ... ORDER BY ...)
SELECT
    EmployeeName, DeptID, Salary,
    ROW_NUMBER() OVER (PARTITION BY DeptID ORDER BY Salary DESC) AS RowNum,     -- 1,2,3,4 (always unique)
    RANK()       OVER (PARTITION BY DeptID ORDER BY Salary DESC) AS RankVal,    -- 1,2,2,4 (ties share rank, gaps follow)
    DENSE_RANK() OVER (PARTITION BY DeptID ORDER BY Salary DESC) AS DenseRank,  -- 1,2,2,3 (ties share rank, no gaps)
    NTILE(4)     OVER (ORDER BY Salary DESC)                     AS Quartile    -- Splits rows into 4 equal buckets
FROM Employees;
```

## 8.2 Offset & Value Functions: LAG, LEAD, FIRST_VALUE, LAST_VALUE
```sql
SELECT
    EmployeeName, DeptID, Salary,
    LAG(Salary, 1, 0)  OVER (PARTITION BY DeptID ORDER BY HireDate) AS PrevSalary,   -- Previous row's value (default 0)
    LEAD(Salary, 1, 0) OVER (PARTITION BY DeptID ORDER BY HireDate) AS NextSalary,   -- Next row's value (default 0)
    FIRST_VALUE(Salary) OVER (PARTITION BY DeptID ORDER BY HireDate) AS FirstSalary, -- First value in the partition
    LAST_VALUE(Salary)  OVER (PARTITION BY DeptID ORDER BY HireDate
                              ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS LastSalary, -- Needs full frame!
    LAG(Bonus) IGNORE NULLS OVER (PARTITION BY DeptID ORDER BY HireDate) AS LastKnownBonus -- (2022+) Skip NULLs; also works with LEAD, FIRST_VALUE, LAST_VALUE
FROM Employees;
```

## 8.3 Distribution Functions: PERCENT_RANK, CUME_DIST, PERCENTILE_CONT, PERCENTILE_DISC
```sql
SELECT
    EmployeeName, DeptID, Salary,
    PERCENT_RANK() OVER (PARTITION BY DeptID ORDER BY Salary) AS PctRank,   -- 0 to 1 (relative rank; first row = 0)
    CUME_DIST()    OVER (PARTITION BY DeptID ORDER BY Salary) AS CumeDist,  -- Share of rows with value <= current
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY Salary) OVER (PARTITION BY DeptID) AS MedianCont, -- Interpolated median
    PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY Salary) OVER (PARTITION BY DeptID) AS MedianDisc  -- Median that exists in the data
FROM Employees;
```

## 8.4 Aggregates as Window Functions: Running Totals & Moving Averages
```sql
SELECT
    EmployeeName, DeptID, Salary,
    SUM(Salary) OVER (PARTITION BY DeptID)                          AS DeptTotal,    -- Total repeated on every row
    SUM(Salary) OVER (ORDER BY HireDate ROWS UNBOUNDED PRECEDING)   AS RunningTotal, -- Running total
    AVG(Salary) OVER (ORDER BY HireDate ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS MovingAvg3, -- 3-row moving average
    COUNT(*)    OVER (PARTITION BY DeptID)                          AS DeptHeadcount, -- Rows in the partition
    Salary * 100.0 / SUM(Salary) OVER (PARTITION BY DeptID)         AS PctOfDept     -- Share of department total
FROM Employees;
```

---

# 9. TABLE-VALUED FUNCTIONS

## 9.1 GENERATE_SERIES (2022+): Number / Date Sequences
```sql
SELECT value FROM GENERATE_SERIES(1, 5);                 -- 1,2,3,4,5 (start, stop)
SELECT value FROM GENERATE_SERIES(0, 100, 25);           -- 0,25,50,75,100 (3rd arg = step)
SELECT DATEADD(DAY, value, '2026-01-01') AS CalendarDate
FROM GENERATE_SERIES(0, 6);                              -- 7 consecutive dates (calendar table trick)
```

## 9.2 STRING_SPLIT & OPENJSON (see sections 3.6 and 10)
```sql
SELECT value FROM STRING_SPLIT('a;b;c', ';');            -- Splits a delimited string into rows
SELECT [key], value, type FROM OPENJSON('["x","y"]');    -- Splits a JSON array/object into rows
```

---

# 10. JSON FUNCTIONS

## 10.1 ISJSON, JSON_VALUE, JSON_QUERY, JSON_MODIFY (2016+)
```sql
DECLARE @json NVARCHAR(MAX) = N'{"name":"Asha","address":{"city":"Pune"},"skills":["SQL","Python"]}';

SELECT
    ISJSON(@json)                        AS IsValidJson,   -- 1 = valid JSON
    JSON_VALUE(@json, '$.name')          AS NameValue,     -- 'Asha' (scalar value)
    JSON_VALUE(@json, '$.address.city')  AS CityValue,     -- 'Pune' (nested path)
    JSON_VALUE(@json, '$.skills[0]')     AS FirstSkill,    -- 'SQL' (array index is 0-based)
    JSON_QUERY(@json, '$.address')       AS AddressObject, -- {"city":"Pune"} (object/array, not scalar)
    JSON_MODIFY(@json, '$.name', 'Ravi') AS ModifiedJson   -- Returns JSON with name updated
;
```

## 10.2 OPENJSON: Shred JSON into Rows & Columns (2016+)
```sql
SELECT * FROM OPENJSON(@json, '$.skills');               -- Default output: key, value, type

SELECT *
FROM OPENJSON(@json)
WITH (
    Name NVARCHAR(50) '$.name',                          -- Map JSON paths to typed columns
    City NVARCHAR(50) '$.address.city'
);
```

## 10.3 JSON_OBJECT, JSON_ARRAY, JSON_PATH_EXISTS (2022+)
```sql
SELECT
    JSON_OBJECT('name': 'Asha', 'age': 30)  AS BuiltObject,  -- (2022+) {"name":"Asha","age":30}
    JSON_ARRAY('SQL', 'Python', 1)          AS BuiltArray,   -- (2022+) ["SQL","Python",1]
    JSON_PATH_EXISTS(@json, '$.address.city') AS PathExists  -- (2022+) 1 if the path exists in the JSON
;
```

---

# 11. HASH, ID & SECURITY FUNCTIONS

## 11.1 HASHBYTES, CHECKSUM, NEWID
```sql
SELECT
    HASHBYTES('SHA2_256', 'abc')       AS Sha256Hash,    -- 32-byte hash (also MD5, SHA1, SHA2_512; avoid MD5/SHA1 for security)
    CHECKSUM('a', 'b')                 AS ChecksumValue, -- int hash of a value list (collisions possible; not for security)
    BINARY_CHECKSUM('a', 'b')          AS BinChecksum,   -- Case-sensitive checksum variant
    NEWID()                            AS NewGuid,       -- New random GUID (uniqueidentifier)
    CONVERT(VARCHAR(64), HASHBYTES('SHA2_256', 'abc'), 2) AS Sha256Hex -- Style 2 = hex string without 0x prefix
;
```

## 11.2 User, Role & Session Context Functions
```sql
SELECT
    CURRENT_USER                 AS CurrentUserName,  -- Database user in effect
    SESSION_USER                 AS SessionUserName,  -- Database user for the session
    SYSTEM_USER                  AS SystemUserName,   -- Login name
    ORIGINAL_LOGIN()             AS OriginalLogin,    -- Login that started the session (ignores EXECUTE AS)
    IS_MEMBER('db_owner')        AS IsDbOwner,        -- 1 if member of the database role/Windows group
    IS_SRVROLEMEMBER('sysadmin') AS IsSysAdmin,       -- 1 if member of the server role
    SESSION_CONTEXT(N'TenantId') AS TenantIdValue     -- (2016+) Value set earlier via sp_set_session_context
;
```

---

# 12. SYSTEM & METADATA FUNCTIONS

## 12.1 Server, Database & Connection Info
```sql
SELECT
    @@VERSION          AS SqlServerVersion,  -- Version and edition text
    @@SERVERNAME       AS ServerName,        -- Name of the SQL Server instance
    @@SERVICENAME      AS ServiceName,       -- Instance service name (MSSQLSERVER for default)
    DB_NAME()          AS CurrentDatabase,   -- Current database name
    DB_ID()            AS CurrentDatabaseId, -- Current database id
    SCHEMA_NAME()      AS DefaultSchema,     -- Default schema of the current user
    SUSER_SNAME()      AS LoginName,         -- Login name of the current connection
    USER_NAME()        AS DatabaseUser,      -- Database user name
    HOST_NAME()        AS ClientMachine,     -- Name of the client computer
    APP_NAME()         AS ApplicationName,   -- Application name of the connection
    @@SPID             AS SessionId,         -- Current session ID
    SERVERPROPERTY('ProductVersion') AS ProductVersion, -- e.g., '16.0.1000.6'
    SERVERPROPERTY('Edition')        AS Edition,        -- e.g., 'Developer Edition (64-bit)'
    DATABASEPROPERTYEX(DB_NAME(), 'Recovery') AS RecoveryModel -- FULL / SIMPLE / BULK_LOGGED
;
```

## 12.2 Object & Column Metadata
```sql
SELECT
    OBJECT_ID('dbo.Employees')                              AS ObjectIdValue,  -- Object ID (NULL if object doesn't exist)
    OBJECT_NAME(OBJECT_ID('dbo.Employees'))                 AS ObjectNameValue, -- 'Employees'
    OBJECT_SCHEMA_NAME(OBJECT_ID('dbo.Employees'))          AS ObjectSchema,    -- 'dbo'
    OBJECTPROPERTY(OBJECT_ID('dbo.Employees'), 'IsUserTable') AS IsUserTable,   -- 1 = user table
    COL_LENGTH('dbo.Employees', 'EmployeeName')             AS ColumnLength,    -- Column length in bytes
    COL_NAME(OBJECT_ID('dbo.Employees'), 1)                 AS FirstColumnName, -- Column name by ordinal position
    COLUMNPROPERTY(OBJECT_ID('dbo.Employees'), 'EmployeeID', 'IsIdentity') AS IsIdentityCol -- 1 = identity column
;
```

## 12.3 DML Result Functions: @@ROWCOUNT, SCOPE_IDENTITY (after INSERT / UPDATE / DELETE)
```sql
SELECT
    @@ROWCOUNT        AS RowsAffected,    -- Rows affected by the last statement (read it immediately!)
    ROWCOUNT_BIG()    AS RowsAffectedBig, -- Same, but bigint
    SCOPE_IDENTITY()  AS NewIdentity,     -- Last identity value in current scope (preferred)
    @@IDENTITY        AS LastIdentity,    -- Last identity in session, any scope (can be affected by triggers)
    IDENT_CURRENT('Employees') AS TableIdentity -- Last identity for a table, any session
;
```

## 12.4 Session State Variables
```sql
SELECT
    @@TRANCOUNT     AS OpenTransactions,  -- Number of open transactions in this session (0 = none)
    @@NESTLEVEL     AS NestingLevel,      -- Current procedure/trigger nesting depth
    @@ERROR         AS LastErrorNumber,   -- Error number of last statement (0 = success; resets after every statement)
    @@FETCH_STATUS  AS LastFetchStatus,   -- Last cursor fetch result (0 = ok, -1 = past end, -2 = row missing)
    @@LOCK_TIMEOUT  AS LockTimeoutMs,     -- Session lock timeout in ms (-1 = wait forever)
    @@TEXTSIZE      AS TextSize           -- Max bytes returned for text/image/varchar(max) columns
;
```

---

# 13. ERROR & TRANSACTION FUNCTIONS

## 13.1 ERROR_* Functions (use inside CATCH)
```sql
BEGIN TRY
    SELECT 1 / 0;
END TRY
BEGIN CATCH
    SELECT
        ERROR_NUMBER()    AS ErrNumber,    -- Error number (8134 = divide by zero)
        ERROR_SEVERITY()  AS ErrSeverity,  -- Severity level
        ERROR_STATE()     AS ErrState,     -- State number
        ERROR_LINE()      AS ErrLine,      -- Line number where error occurred
        ERROR_PROCEDURE() AS ErrProcedure, -- Stored procedure name (NULL if ad hoc)
        ERROR_MESSAGE()   AS ErrMessage;   -- Full error message text
END CATCH;
```

## 13.2 XACT_STATE: Transaction Health (use inside CATCH)
```sql
SELECT XACT_STATE() AS TxnState;  -- 1 = active, committable | 0 = no transaction | -1 = doomed (must ROLLBACK)
```

---

# 14. SQL SERVER 2025 ADDITIONS (2025+)

## 14.1 Regular Expression Functions (2025+)
```sql
SELECT
    REGEXP_LIKE('abc123', '^[a-z]+[0-9]+$')            AS RegexMatch,   -- (2025+) Pattern test (use in WHERE / CASE)
    REGEXP_REPLACE('a1b22c333', '[0-9]+', '#')         AS RegexReplace, -- (2025+) Replace matches -> 'a#b#c#'
    REGEXP_SUBSTR('order-4521-x', '[0-9]+')            AS RegexSubstr,  -- (2025+) First match -> '4521'
    REGEXP_INSTR('order-4521-x', '[0-9]+')             AS RegexInstr,   -- (2025+) Position of the match
    REGEXP_COUNT('a1b22c333', '[0-9]+')                AS RegexCount    -- (2025+) Number of matches -> 3
;

SELECT * FROM REGEXP_MATCHES('a1b22c333', '[0-9]+');                   -- (2025+) Table of all matches
SELECT * FROM REGEXP_SPLIT_TO_TABLE('a1b22c333', '[0-9]+');            -- (2025+) Split a string by a regex into rows
```

## 14.2 Fuzzy String Matching & Encoding (2025+)
```sql
SELECT
    EDIT_DISTANCE('kitten', 'sitting')                 AS EditDistance,       -- (2025+) Number of edits between strings
    EDIT_DISTANCE_SIMILARITY('kitten', 'sitting')      AS EditSimilarity,     -- (2025+) 0–100 similarity score
    JARO_WINKLER_DISTANCE('Smith', 'Smyth')            AS JaroWinklerDist,    -- (2025+) Lower = more similar
    JARO_WINKLER_SIMILARITY('Smith', 'Smyth')          AS JaroWinklerSim,     -- (2025+) Higher = more similar
    BASE64_ENCODE(CAST('Hello' AS VARBINARY(MAX)))     AS Base64Text,         -- (2025+) Encode binary to Base64
    CAST(BASE64_DECODE(BASE64_ENCODE(CAST('Hello' AS VARBINARY(MAX)))) AS VARCHAR(20)) AS Decoded -- (2025+) Back to text
;
```

## 14.3 Concatenation, Unicode, Date & Aggregate Additions (2025+)
```sql
SELECT
    'SQL' || ' ' || 'Server'      AS PipeConcat,    -- (2025+) ANSI || string concatenation operator
    UNISTR('\00E9')               AS UnicodeEscape, -- (2025+) 'é' (Unicode escape sequence)
    CURRENT_DATE                  AS CurrentDateVal -- (2025+) ANSI current date (date type)
;

SELECT
    PRODUCT(Factor)               AS ProductAgg     -- (2025+) Multiplies all values in a group
FROM (VALUES (2), (3), (4)) AS t(Factor);            -- 24

SELECT DeptID,
       JSON_ARRAYAGG(EmployeeName)               AS NamesJsonArray, -- (2025+) ["Asha","Ravi"]
       JSON_OBJECTAGG(EmployeeName : Salary)     AS NameSalaryJson  -- (2025+) {"Asha":50000,"Ravi":60000}
FROM Employees
GROUP BY DeptID;
```

## 14.4 Vector Functions (2025+)
```sql
DECLARE @a VECTOR(3) = '[0.1, 0.2, 0.3]';
DECLARE @b VECTOR(3) = '[0.2, 0.1, 0.4]';

SELECT
    VECTOR_DISTANCE('cosine', @a, @b)   AS CosineDistance,  -- (2025+) Also 'euclidean', 'dot'
    VECTOR_NORM(@a, 'norm2')            AS VectorNorm,      -- (2025+) Length of the vector
    VECTOR_NORMALIZE(@a, 'norm2')       AS NormalizedVector -- (2025+) Unit-length vector
;
```
