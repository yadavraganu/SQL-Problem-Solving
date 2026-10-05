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

-- ============================================
-- CURRENT DATE/TIME FUNCTIONS
-- ============================================
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

-- ============================================
-- DATEADD: add/subtract an interval to a date
-- Syntax: DATEADD(datepart, number, date)
-- ============================================
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

-- ============================================
-- DATEDIFF: number of datepart BOUNDARIES crossed
-- Syntax: DATEDIFF(datepart, startdate, enddate)
-- Note: counts boundaries, not full elapsed units
-- ============================================
SELECT
    DATEDIFF(DAY,    '2026-01-01', '2026-09-09') AS DaysBetween,    -- Days between two dates
    DATEDIFF(MONTH,  '2026-01-31', '2026-02-01') AS MonthsBetween,  -- Returns 1 (month boundary crossed, only 1 day apart!)
    DATEDIFF(YEAR,   '2025-12-31', '2026-01-01') AS YearsBetween,   -- Returns 1 (year boundary crossed)
    DATEDIFF(HOUR,   '2026-09-09 08:00', '2026-09-09 17:30') AS HoursBetween,   -- Returns 9
    DATEDIFF(MINUTE, '2026-09-09 08:00', '2026-09-09 17:30') AS MinutesBetween, -- Returns 570
    DATEDIFF_BIG(MILLISECOND, '2020-01-01', GETDATE()) AS BigMsBetween          -- bigint result for large ranges
;

-- Accurate age in years (handles birthdays not yet reached this year)
DECLARE @DOB DATE = '1990-11-15';
SELECT 
    DATEDIFF(YEAR, @DOB, GETDATE()) 
    - CASE WHEN DATEADD(YEAR, DATEDIFF(YEAR, @DOB, GETDATE()), @DOB) > CAST(GETDATE() AS DATE) 
           THEN 1 ELSE 0 END AS AgeInYears;

-- ============================================
-- DATE CONSTRUCTION & BOUNDARIES
-- ============================================
SELECT
    EOMONTH(GETDATE())              AS EndOfThisMonth,    -- Last day of current month
    EOMONTH(GETDATE(), 1)           AS EndOfNextMonth,    -- Last day of next month (2nd arg = month offset)
    EOMONTH(GETDATE(), -1)          AS EndOfLastMonth,    -- Last day of previous month
    DATEADD(DAY, 1, EOMONTH(GETDATE(), -1)) AS StartOfThisMonth, -- First day of current month
    DATEFROMPARTS(2026, 9, 9)       AS BuiltDate,         -- Build a date from Y, M, D
    DATETIMEFROMPARTS(2026, 9, 9, 14, 30, 0, 0) AS BuiltDateTime, -- Build datetime (y,m,d,h,min,sec,ms)
    TIMEFROMPARTS(14, 30, 0, 0, 0)  AS BuiltTime,         -- Build time (h,min,sec,fractions,precision)
    DATETRUNC(MONTH, GETDATE())     AS TruncToMonth,      -- SQL Server 2022+: truncate to first of month
    DATETRUNC(YEAR, GETDATE())      AS TruncToYear        -- SQL Server 2022+: truncate to Jan 1
;

-- Start of day / start of month for older versions (pre-2022)
SELECT
    DATEADD(DAY,   DATEDIFF(DAY,   0, GETDATE()), 0) AS StartOfToday,
    DATEADD(MONTH, DATEDIFF(MONTH, 0, GETDATE()), 0) AS StartOfMonth,
    DATEADD(YEAR,  DATEDIFF(YEAR,  0, GETDATE()), 0) AS StartOfYear
;

-- ============================================
-- SHORTHAND DATE FUNCTIONS & VALIDATION
-- ============================================
SELECT
    YEAR(GETDATE())     AS YearNum,     -- Same as DATEPART(YEAR, ...)
    MONTH(GETDATE())    AS MonthNum,    -- Same as DATEPART(MONTH, ...)
    DAY(GETDATE())      AS DayNum,      -- Same as DATEPART(DAY, ...)
    ISDATE('2026-09-09') AS IsValidDate, -- 1 = valid date string, 0 = not valid
    ISDATE('2026-13-45') AS IsInvalidDate
;

-- ============================================
-- FORMAT: .NET-style formatting (returns nvarchar)
-- Syntax: FORMAT(value, format_string [, culture])
-- Note: convenient but slower than CONVERT on big tables
-- ============================================
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

-- ============================================
-- CONVERT with style codes (date -> string)
-- Syntax: CONVERT(data_type, expression [, style])
-- ============================================
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

-- ============================================
-- STRING FUNCTIONS
-- ============================================
SELECT
    LEN('  Hello  ')                  AS LenValue,        -- 7 (ignores trailing spaces, counts leading)
    DATALENGTH('Hello')               AS DataLengthValue, -- 5 bytes (counts trailing spaces; NVARCHAR = 2 bytes/char)
    LEFT('SQL Server', 3)             AS LeftChars,       -- 'SQL'
    RIGHT('SQL Server', 6)            AS RightChars,      -- 'Server'
    SUBSTRING('SQL Server', 5, 6)     AS SubstringValue,  -- 'Server' (start position is 1-based, length = 6)
    CHARINDEX('Server', 'SQL Server') AS CharIndexValue,  -- 5 (position of first match, 0 if not found)
    CHARINDEX('x', 'SQL Server')      AS CharIndexNotFound, -- 0
    PATINDEX('%[0-9]%', 'abc123')     AS PatIndexValue,   -- 4 (position of first digit; supports wildcards)
    REPLACE('2026-09-09', '-', '/')   AS ReplaceValue,    -- '2026/09/09'
    STUFF('ABCDEF', 2, 3, 'xyz')      AS StuffValue,      -- 'AxyzEF' (delete 3 chars from pos 2, insert 'xyz')
    UPPER('sql')                      AS UpperValue,      -- 'SQL'
    LOWER('SQL')                      AS LowerValue,      -- 'sql'
    REVERSE('abc')                    AS ReverseValue,    -- 'cba'
    REPLICATE('ab', 3)                AS ReplicateValue,  -- 'ababab'
    SPACE(5)                          AS FiveSpaces,      -- 5 blank characters
    TRANSLATE('2*[3+4]', '[]*', '()+') AS TranslateValue, -- Character-by-character swap (SQL 2017+)
    ASCII('A')                        AS AsciiValue,      -- 65
    CHAR(65)                          AS CharValue,       -- 'A'
    UNICODE(N'€')                     AS UnicodeValue,    -- 8364
    NCHAR(8364)                       AS NCharValue,      -- '€'
    QUOTENAME('My Table')             AS QuoteNameValue,  -- '[My Table]' (safe object name quoting)
    SOUNDEX('Smith')                  AS SoundexValue,    -- 'S530' (phonetic code)
    DIFFERENCE('Smith', 'Smyth')      AS DifferenceValue  -- 0–4 similarity of SOUNDEX codes (4 = very similar)
;

-- ============================================
-- TRIM FUNCTIONS
-- ============================================
SELECT
    LTRIM('   abc   ')  AS LeftTrim,   -- 'abc   ' (removes leading spaces)
    RTRIM('   abc   ')  AS RightTrim,  -- '   abc' (removes trailing spaces)
    TRIM('   abc   ')   AS BothTrim,   -- 'abc' (SQL 2017+; removes both sides)
    TRIM('#' FROM '##abc##') AS TrimChar -- 'abc' (SQL 2017+; trim specific characters)
;

-- ============================================
-- CONCATENATION
-- ============================================
SELECT
    'John' + ' ' + 'Smith'              AS PlusConcat,      -- 'John Smith' (NULL + anything = NULL)
    CONCAT('John', ' ', 'Smith')        AS ConcatValue,     -- 'John Smith' (treats NULL as empty string)
    CONCAT('A', NULL, 'B')              AS ConcatWithNull,  -- 'AB'
    CONCAT_WS(', ', 'Pune', 'MH', 'IN') AS ConcatWsValue    -- 'Pune, MH, IN' (separator first; skips NULLs)
;

-- ============================================
-- STRING_AGG & STRING_SPLIT (SQL Server 2017+ / 2016+)
-- ============================================
-- STRING_AGG: combine rows into one delimited string (aggregate)
SELECT 
    DepartmentID,
    STRING_AGG(EmployeeName, ', ') WITHIN GROUP (ORDER BY EmployeeName) AS EmployeeList
FROM Employees
GROUP BY DepartmentID;

-- STRING_SPLIT: split a delimited string into rows (column name is "value")
SELECT value AS Item
FROM STRING_SPLIT('red,green,blue', ',');

-- With ordinal position (SQL Server 2022+ / Azure SQL)
SELECT value, ordinal
FROM STRING_SPLIT('red,green,blue', ',', 1);

-- ============================================
-- NUMERIC / MATH FUNCTIONS
-- ============================================
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
    LOG10(100)           AS Log10Value,     -- 2
    PI()                 AS PiValue,        -- 3.14159265358979
    17 % 5               AS ModuloValue,    -- 2 (remainder)
    RAND()               AS RandomFloat,    -- Random float between 0 and 1
    RAND(42)             AS SeededRandom,   -- Repeatable random using a seed
    CAST(5 AS DECIMAL(10,2)) / 2 AS DecimalDivision -- 2.50 (integer / integer = integer, so cast first!)
;

-- ============================================
-- NULL HANDLING & CONDITIONAL FUNCTIONS
-- ============================================
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

-- ============================================
-- DATA TYPE CONVERSION
-- ============================================
SELECT
    CAST('123' AS INT)                  AS CastToInt,        -- 123 (ANSI standard)
    CONVERT(INT, '123')                 AS ConvertToInt,     -- 123 (SQL Server specific, supports style codes)
    CAST(GETDATE() AS DATE)             AS CastToDate,       -- Date only
    CAST(123.456 AS INT)                AS CastDecimalToInt, -- 123 (truncates, does not round)
    TRY_CAST('abc' AS INT)              AS TryCastFail,      -- NULL instead of error
    TRY_CONVERT(DATE, '2026-13-45')     AS TryConvertFail,   -- NULL instead of error
    TRY_CONVERT(DATE, '2026-09-09')     AS TryConvertOk,     -- 2026-09-09
    PARSE('09/09/2026' AS DATE USING 'en-GB') AS ParseValue, -- Culture-aware parse (slow; avoid on large tables)
    TRY_PARSE('xyz' AS DATE USING 'en-US')    AS TryParseFail, -- NULL on failure
    ISNUMERIC('123.45')                 AS IsNumericValue    -- 1 (caution: also returns 1 for '$', ',', '1e5'; prefer TRY_CAST)
;

-- ============================================
-- AGGREGATE FUNCTIONS
-- ============================================
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

-- GROUPING with HAVING (filter on aggregates)
SELECT DeptID, COUNT(*) AS EmpCount, AVG(Salary) AS AvgSalary
FROM Employees
GROUP BY DeptID
HAVING COUNT(*) > 5;                              -- WHERE filters rows; HAVING filters groups

-- ROLLUP / CUBE / GROUPING SETS for subtotals
SELECT DeptID, JobTitle, SUM(Salary) AS TotalSalary
FROM Employees
GROUP BY ROLLUP (DeptID, JobTitle);               -- Adds subtotal per DeptID and a grand total row

-- ============================================
-- WINDOW (ANALYTIC) FUNCTIONS
-- Syntax: function() OVER (PARTITION BY ... ORDER BY ...)
-- ============================================
SELECT
    EmployeeName, DeptID, Salary,

    -- Ranking functions
    ROW_NUMBER() OVER (PARTITION BY DeptID ORDER BY Salary DESC) AS RowNum,    -- 1,2,3,4 (always unique)
    RANK()       OVER (PARTITION BY DeptID ORDER BY Salary DESC) AS RankVal,   -- 1,2,2,4 (ties share rank, gaps follow)
    DENSE_RANK() OVER (PARTITION BY DeptID ORDER BY Salary DESC) AS DenseRank, -- 1,2,2,3 (ties share rank, no gaps)
    NTILE(4)     OVER (ORDER BY Salary DESC)                     AS Quartile,  -- Splits rows into 4 equal buckets

    -- Offset functions
    LAG(Salary, 1, 0)  OVER (PARTITION BY DeptID ORDER BY HireDate) AS PrevSalary, -- Previous row's value (default 0)
    LEAD(Salary, 1, 0) OVER (PARTITION BY DeptID ORDER BY HireDate) AS NextSalary, -- Next row's value (default 0)
    FIRST_VALUE(Salary) OVER (PARTITION BY DeptID ORDER BY HireDate) AS FirstSalary, -- First value in the partition
    LAST_VALUE(Salary)  OVER (PARTITION BY DeptID ORDER BY HireDate
                              ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS LastSalary, -- Needs full frame!

    -- Aggregates as window functions
    SUM(Salary) OVER (PARTITION BY DeptID)                          AS DeptTotal,     -- Total repeated on every row
    SUM(Salary) OVER (ORDER BY HireDate ROWS UNBOUNDED PRECEDING)   AS RunningTotal,  -- Running total
    AVG(Salary) OVER (ORDER BY HireDate ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS MovingAvg3, -- 3-row moving average
    Salary * 100.0 / SUM(Salary) OVER (PARTITION BY DeptID)         AS PctOfDept      -- Share of department total
FROM Employees;

-- Top N per group (very common pattern)
WITH Ranked AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY DeptID ORDER BY Salary DESC) AS rn
    FROM Employees
)
SELECT * FROM Ranked WHERE rn <= 3;               -- Top 3 earners in each department

-- Remove duplicates (keep the first row per key)
WITH Dups AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY Email ORDER BY CreatedDate) AS rn
    FROM Customers
)
DELETE FROM Dups WHERE rn > 1;

-- ============================================
-- JSON FUNCTIONS (SQL Server 2016+)
-- ============================================
DECLARE @json NVARCHAR(MAX) = N'{"name":"Asha","address":{"city":"Pune"},"skills":["SQL","Python"]}';

SELECT
    ISJSON(@json)                        AS IsValidJson,   -- 1 = valid JSON
    JSON_VALUE(@json, '$.name')          AS NameValue,     -- 'Asha' (scalar value)
    JSON_VALUE(@json, '$.address.city')  AS CityValue,     -- 'Pune' (nested path)
    JSON_VALUE(@json, '$.skills[0]')     AS FirstSkill,    -- 'SQL' (array index is 0-based)
    JSON_QUERY(@json, '$.address')       AS AddressObject, -- {"city":"Pune"} (object/array, not scalar)
    JSON_MODIFY(@json, '$.name', 'Ravi') AS ModifiedJson   -- Returns JSON with name updated
;

-- OPENJSON: shred JSON into rows/columns
SELECT *
FROM OPENJSON(@json, '$.skills');                 -- Returns key, value, type columns

-- FOR JSON: turn query results into JSON
SELECT EmployeeName, Salary FROM Employees FOR JSON PATH;

-- ============================================
-- SYSTEM & METADATA FUNCTIONS
-- ============================================
SELECT
    @@VERSION          AS SqlServerVersion,  -- Version and edition text
    @@SERVERNAME       AS ServerName,        -- Name of the SQL Server instance
    DB_NAME()          AS CurrentDatabase,   -- Current database name
    SCHEMA_NAME()      AS DefaultSchema,     -- Default schema of the current user
    SUSER_SNAME()      AS LoginName,         -- Login name of the current connection
    USER_NAME()        AS DatabaseUser,      -- Database user name
    HOST_NAME()        AS ClientMachine,     -- Name of the client computer
    APP_NAME()         AS ApplicationName,   -- Application name of the connection
    @@SPID             AS SessionId,         -- Current session ID
    NEWID()            AS NewGuid,           -- New random GUID (uniqueidentifier)
    OBJECT_ID('dbo.Employees') AS ObjectIdValue, -- Object ID (NULL if object doesn't exist)
    COL_LENGTH('dbo.Employees', 'EmployeeName') AS ColumnLength -- Column length in bytes
;

-- After INSERT / UPDATE / DELETE
INSERT INTO Employees (EmployeeName) VALUES ('Asha');
SELECT
    @@ROWCOUNT        AS RowsAffected,    -- Rows affected by the last statement (read it immediately!)
    SCOPE_IDENTITY()  AS NewIdentity,     -- Last identity value in current scope (preferred)
    @@IDENTITY        AS LastIdentity,   -- Last identity in session, any scope (can be affected by triggers)
    IDENT_CURRENT('Employees') AS TableIdentity -- Last identity for a table, any session
;

-- ============================================
-- ERROR HANDLING FUNCTIONS (use inside CATCH)
-- ============================================
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

-- ============================================
-- COMMON REAL-WORLD DATE PATTERNS
-- ============================================
-- Rows from today only
SELECT * FROM Orders 
WHERE OrderDate >= CAST(GETDATE() AS DATE) 
  AND OrderDate <  DATEADD(DAY, 1, CAST(GETDATE() AS DATE));

-- Rows from the last 30 days
SELECT * FROM Orders WHERE OrderDate >= DATEADD(DAY, -30, GETDATE());

-- Rows from the current month (index-friendly, avoids wrapping the column in a function)
SELECT * FROM Orders 
WHERE OrderDate >= DATEADD(DAY, 1, EOMONTH(GETDATE(), -1))
  AND OrderDate <  DATEADD(DAY, 1, EOMONTH(GETDATE()));

-- Monthly summary
SELECT 
    YEAR(OrderDate)  AS OrderYear,
    MONTH(OrderDate) AS OrderMonth,
    SUM(Amount)      AS MonthlyTotal
FROM Orders
GROUP BY YEAR(OrderDate), MONTH(OrderDate)
ORDER BY OrderYear, OrderMonth;
```
