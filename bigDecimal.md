# 📘 BigDecimal Cheat Sheet (Java – Financial Calculations)

A quick reference guide for using `BigDecimal` in financial systems such as currency conversion, fees, and transaction processing.

---

## 🧠 Why BigDecimal?

- Exact decimal precision (no floating-point errors)
- Required for money, FX, and fees
- Supports controlled rounding
- Deterministic and audit-friendly

---

## ⚠️ Creation (VERY IMPORTANT)

### ✅ Correct
```java
new BigDecimal("0.1");
BigDecimal.valueOf(0.1);
```
### ❌ Wrong
```java
new BigDecimal(0.1); // precision issue
```
### 🔢 Constants
```java
BigDecimal.ZERO;
BigDecimal.ONE;
BigDecimal.TEN;
```
### ⚠️ Division (Always specify rounding)
```java
a.divide(b, 2, RoundingMode.HALF_UP);

```

### 🎯 Comparison (NEVER use == or equals)
```java
a.compareTo(b);
```
|Result|Meaning|  
| :--- | :--- |
|0|equal|  
|> 0	|a > b|  
|< 0	|a < b|  

Example
```java
if (amount.compareTo(balance) > 0) {
throw new RuntimeException("Insufficient balance");
}
```

### 🎚️ Rounding & Scale
```java
amount.setScale(2, RoundingMode.HALF_UP);
```
#### Common Modes
HALF_UP → standard (₹10.125 → ₹10.13)  
DOWN → truncate  
UP → always round up  
### 💱 FX Calculation
Formula
Converted = Amount × BaseRate × (1 + Margin)  
Code
```java
BigDecimal effectiveRate =
baseRate.multiply(BigDecimal.ONE.add(margin));

BigDecimal converted =
amount.multiply(effectiveRate)
.setScale(2, RoundingMode.HALF_UP);
```

### 💸 Fee Calculation
Percentage Fee
```java
BigDecimal fee = amount.multiply(percentage);
```
Minimum Fee
```java
fee = fee.max(minFee);
```
Final Fee
```java
fee = fee.setScale(2, RoundingMode.HALF_UP);
```
###  🔄 Full Flow (FX + Fee)
```java
BigDecimal fee = amount.multiply(feePercent)
.max(minFee);

BigDecimal netAmount = amount.subtract(fee);

BigDecimal effectiveRate =
baseRate.multiply(BigDecimal.ONE.add(margin));

BigDecimal converted =
netAmount.multiply(effectiveRate)
.setScale(2, RoundingMode.HALF_UP);
```

###  ⚠️ Common Mistakes (INTERVIEW GOLD)
#### ❌ Using double
double x = 0.1 + 0.2;
#### ❌ Wrong margin logic
baseRate.multiply(margin); // wrong
#### ❌ Rounding too early
rate.setScale(2); // avoid
#### ❌ equals instead of compareTo
a.equals(b); // scale-sensitive
#### 🔍 equals vs compareTo (Tricky!)
new BigDecimal("10.0").equals(new BigDecimal("10.00")); // false ❌  
new BigDecimal("10.0").compareTo(new BigDecimal("10.00")); // 0 ✅

### 🧵 Immutability

BigDecimal is immutable:

a.add(b); // does NOT modify a

### ✅ Always assign:

a = a.add(b);
### 🚀 Performance Tip (Senior-Level)
BigDecimal is slower than double
For high-scale systems:
Store money as long (cents/paise)
Convert to BigDecimal only when needed
### 🧾 Utility Helper
```java
public class MoneyUtils {

    public static BigDecimal round(BigDecimal value) {
        return value.setScale(2, RoundingMode.HALF_UP);
    }

    public static BigDecimal percentage(BigDecimal amount, BigDecimal percent) {
        return amount.multiply(percent);
    }
}
```

# BigDecimal vs. Long (Scaled Integer) for Financial Data


| Feature | `BigDecimal` | `Long` (Cents/Paise) |
| :--- | :--- | :--- |
| **Accuracy** | **Perfect**. Arbitrary precision. | **High**. Fixed decimal places. |
| **Performance** | **Slower**. Object-based overhead. | **Fastest**. Native CPU math. |
| **Rounding** | **Built-in**. `HALF_UP`, `HALF_EVEN`. | **Manual**. Requires custom logic. |
| **Memory** | **Heavy**. Significant object overhead. | **Light**. Exactly 8 bytes per value. |
| **Syntax** | **Verbose**. Requires method calls. | **Clean**. Uses standard operators. |
| **Scale** | **Automatic**. Tracks decimals for you. | **Manual**. You must manage units. |
| **Overflow** | **None**. Limited only by memory. | **Possible**. Limit is ~9 quintillion. |
| **Use Case** | Banking, E-commerce, Tax reports. | Trading, High-speed gaming. |

## Quick Recommendation
- Use **BigDecimal** for standard business apps to ensure safety and compliance.
- Use **Long** for performance-critical systems where speed and memory are bottlenecks.
