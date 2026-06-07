## DAX Measures Reference
 # Supplier Trend Analysis — Power BI

DAX Measures

- Total Sales
Total Sales = SUM( Sales[Sale Total] )

- Sales Last Year
Sales LY =
    CALCULATE(
        [Total Sales],
        SAMEPERIODLASTYEAR( Dates[Date] )
    )

- Sales 2 Years Prior
Sales 2 Yrs Prior =
    CALCULATE(
        [Total Sales],
        SAMEPERIODLASTYEAR( SAMEPERIODLASTYEAR( Dates[Date] ) )
    )

% Sales Growth to Last Year
% Sales Growth to LY =
    DIVIDE( [Total Sales] - [Sales LY], [Sales LY] ) * 100

- Total Purchases
Total Purchases =
    SUMX( Sales, Sales[Order Quantity] * Sales[Purchase Value] )

- Cumulative Sales (Selected)
Cumulative Sales (Selected) =
    CALCULATE(
        [Total Sales],
        FILTER(
            ALLSELECTED( Dates ),
            Dates[Date] <= MAX( Dates[Date] )
        )
    )

- Cumulative Sales Last Year
Cumulative Sales LY =
    CALCULATE(
        [Sales LY],
        FILTER(
            ALLSELECTED( Dates ),
            Dates[Date] <= MAX( Dates[Date] )
        )
    )

- Cumulative Sales 2 Years Prior
Cumulative Sales 2 Years Prior =
    CALCULATE(
        [Sales 2 Yrs Prior],
        FILTER(
            ALLSELECTED( Dates ),
            Dates[Date] <= MAX( Dates[Date] )
        )
    )

# Total Profits
- Total Profits = [Total Sales] - [Total Purchases]

# Profit Margin
- Profit Margin = DIVIDE( [Total Profits], [Total Sales], 0 )

# Profits Last Year
- Profits LY =
    CALCULATE( [Total Profits], SAMEPERIODLASTYEAR( Dates[Date] ) )

# Lifetime Profits
- Lifetime Profits =
    CALCULATE(
        [Total Profits],
        ALLEXCEPT( Sales, Suppliers[Supplier Names] )
    )

# Lifetime Sales
- Lifetime Sales =
    CALCULATE(
        [Total Sales],
        ALLEXCEPT( Sales, Suppliers[Supplier Names] )
    )

# Lifetime Profit Margin
- Lifetime Profit Margin = DIVIDE( [Lifetime Profits], [Lifetime Sales], 0 )

# Last Sale Amount
- Last Sale Amount =
    CALCULATE(
        [Total Sales],
        LASTNONBLANK( Sales[OrderDate], [Total Sales] )
    )

# Last Sales Date
- Last Sales Date = LASTNONBLANK( Sales[OrderDate], [Total Sales] )

# DAX Measure — Supplier Profits Analysis

# Avg Profits per Day
- Avg Profits per Day =
    AVERAGEX(
        VALUES( Dates[Date] ),
        [Total Profits]
    )

# Calculated Columns

- Quarter = "Q" & Dates[QuarterOfYear]

- Year_Month = [MonthName] & "_" & Dates[Year]

# Suppliers Sales — Suppliers Table
- Suppliers Sales = [Total Sales]
Note: Context transition operation — row context is converted to filter context when referencing the [Total Sales] measure.

# Cumulated Sales — Suppliers Table
- Cumulated Sales =
    CALCULATE(
        [Total Sales],
        ALL( Suppliers ),
        Suppliers[Suppliers Sales] >= EARLIER( Suppliers[Suppliers Sales] )
    )

# Cumulated Percentage — Suppliers Table
- Cumulated Percentage =
    DIVIDE(
        Suppliers[Cumulated Sales],
        SUM( Suppliers[Suppliers Sales] ),
        0
    )

# Suppliers Sales Group — Suppliers Table
- Suppliers Sales Group =
    SWITCH(
        TRUE(),
        Suppliers[Cumulated Percentage] <= 0.7, "Top Suppliers",
        Suppliers[Cumulated Percentage] <= 0.9, "Mid. Suppliers",
        "Lower Suppliers"
    )
