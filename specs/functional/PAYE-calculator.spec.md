## PAYE Calculator Specification

### Overview
A calculator that computes New Zealand PAYE (Pay As You Earn) tax, KiwiSaver contributions, and ACC levy based on annual salary input, displaying monthly breakdowns.

### Input
- **Annual Salary**: Gross annual salary (NZD)

### Tax Calculations

#### PAYE Tax Rates (2025/2026)

Income	Tax Rate
- $0 - $15,600: 10.5%
- $15,601 - $53,500: 17.5%
- $53,501 - $78,100: 30%
- $78,101 - $180,000: 33%
- $180,001 + 39%

#### KiwiSaver
- Employee contribution: 0%, 3%, 5% of gross salary

#### ACC Earners' Levy
- Current rate: 1.53% of gross salary (up to maximum earnings threshold of $139,384)

### Calculations
1. Calculate annual PAYE tax using progressive tax brackets
2. Calculate annual KiwiSaver contribution (3% of gross salary)
3. Calculate annual ACC levy (1.67% of gross salary, capped at maximum earnings of $152,790)
4. Calculate annual take-home pay = Annual Salary - PAYE - KiwiSaver - ACC
5. Divide all amounts by 12 to get monthly figures

### Output Display
The calculator should display:
- **Monthly Gross Salary**: Annual salary ÷ 12
- **Monthly PAYE Tax**: Annual tax ÷ 12
- **Monthly KiwiSaver**: Annual KiwiSaver ÷ 12
- **Monthly ACC Levy**: Annual ACC ÷ 12
- **Monthly Take-Home Pay**: Monthly gross - monthly deductions

### Example
For an annual salary of $60,000:
- Monthly Gross: $5,000.00
- Monthly PAYE: ~$808.38
- Monthly KiwiSaver: $150.00
- Monthly ACC: $83.50
- Monthly Take-Home: ~$3,958.12

