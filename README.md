# Swiggy Order Analytics – Power BI Dashboard

A Power BI dashboard for analyzing food-delivery order performance across revenue, orders, customers, restaurants, cities, cuisines, payment methods, cancellations, and delivery time.

## Repository Name

`swiggy-order-analytics-powerbi`

## Project Overview

This project transforms order-level data into an interactive business intelligence dashboard. The dashboard provides KPI monitoring, trend analysis, restaurant ranking, payment-method distribution, city-level performance, cuisine/order-status analysis, and restaurant rating versus average order value analysis.

## Business Objectives

- Monitor total revenue and order volume.
- Track average order value.
- Measure cancellation rate.
- Monitor average delivery time.
- Identify restaurants contributing the most net revenue.
- Analyze revenue trends over time.
- Understand payment-method order share.
- Compare revenue and order volume by city.
- Analyze order status across cuisines.
- Explore the relationship between restaurant rating and average order value.

## Dataset

The project uses three tables:

### Orders
- OrderID
- CustomerID
- RestaurantID
- OrderDate
- OrderTime
- DeliveryTimeMin
- ItemsCount
- OrderValue
- DiscountAmount
- DeliveryFee
- PaymentMethod
- OrderStatus

### Customers
- CustomerID
- CustomerName
- Gender
- Age
- City
- SignupDate

### Restaurants
- RestaurantID
- RestaurantName
- City
- Cuisine
- Rating
- CostForTwo
- FoodType

## Data Model

```text
Customers
CustomerID (1)
       |
       | CustomerID
       v
Orders
CustomerID (*)
RestaurantID (*)
       ^
       | RestaurantID
       |
Restaurants
RestaurantID (1)
```

Recommended relationships:

- `Customers[CustomerID]` → `Orders[CustomerID]`
- `Restaurants[RestaurantID]` → `Orders[RestaurantID]`

Both should be one-to-many relationships from the dimension table to Orders.

## Key DAX

### Net Revenue

Create this as a **calculated column**:

```DAX
Net Revenue =
Orders[OrderValue]
    - Orders[DiscountAmount]
    + Orders[DeliveryFee]
```

### Total Revenue

```DAX
Total Revenue =
SUM(Orders[Net Revenue])
```

### Total Orders

```DAX
Total Orders =
COUNTROWS(Orders)
```

### Average Order Value

```DAX
Avg Order Value =
DIVIDE([Total Revenue], [Total Orders], 0)
```

### Cancellation Rate

```DAX
Cancellation Rate =
DIVIDE(
    CALCULATE(
        [Total Orders],
        Orders[OrderStatus] = "Cancelled"
    ),
    [Total Orders],
    0
)
```

### Average Delivery Time

```DAX
Avg Delivery Time =
AVERAGE(Orders[DeliveryTimeMin])
```

## Calculated Columns

### Order Month

```DAX
Order Month =
FORMAT(Orders[OrderDate], "MMM YYYY")
```

### Delivery Speed

```DAX
Delivery Speed =
IF(
    Orders[DeliveryTimeMin] <= 30,
    "Fast",
    "Slow"
)
```

### Discount Percentage

```DAX
Discount % =
DIVIDE(
    Orders[DiscountAmount],
    Orders[OrderValue],
    0
)
```

### Day Type

```DAX
Day Type =
IF(
    WEEKDAY(Orders[OrderDate], 2) > 5,
    "Weekend",
    "Weekday"
)
```

## Dashboard Visuals

| Visual | Configuration |
|---|---|
| KPI Cards | Total Revenue, Total Orders, Avg Order Value, Cancellation Rate |
| Bar Chart | Top restaurants by Net Revenue |
| Line Chart | Net Revenue by Order Month |
| Donut Chart | Order share by Payment Method |
| City Performance | Revenue and Order Count by City |
| Stacked Bar | Order Status split by Cuisine |
| Scatter Chart | Restaurant Rating vs Avg Order Value, sized by Order Count |

## Dashboard Filters

The dashboard includes interactive filters for:

- Payment Method
- City
- Order Status
- Cuisine
- Order Date

## Tools Used

- Microsoft Power BI
- DAX
- Power Query
- Relational data modeling

