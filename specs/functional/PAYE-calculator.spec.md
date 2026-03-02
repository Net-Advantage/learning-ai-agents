## PAYE Calculator Specification

### Overview
A calculator that estimates New Zealand salary/wage deductions from an annual salary input, displaying a monthly breakdown.

This spec uses IRD-published rates for the NZ tax year 1 April 2025 to 31 March 2026.

### Input
- **Annual Salary**: Gross annual salary (NZD)
- **KiwiSaver employee contribution rate**: 3%, 4%, 6%, 8% or 10% (default 3%)

### Tax Calculations

#### Income tax rates (tax year 1 April 2025 to 31 March 2026)

Source: IRD tax rates for individuals: https://www.ird.govt.nz/income-tax/income-tax-for-individuals/tax-codes-and-tax-rates-for-individuals/tax-rates-for-individuals

Income	Tax Rate
- $0 - $15,600: 10.5%
- $15,601 - $53,500: 17.5%
- $53,501 - $78,100: 30%
- $78,101 - $180,000: 33%
- $180,001 and over: 39%

#### KiwiSaver
- Employee contribution rate can be 3%, 4%, 6%, 8% or 10% of gross pay.

Source: KiwiSaver deductions from employee pay: https://www.ird.govt.nz/kiwisaver/kiwisaver-employers/contributions-and-deductions/kiwisaver-deductions-from-employee-pay

#### ACC Earners' Levy
- Rate for 1 April 2025 to 31 March 2026: $1.67 per $100 (1.67%).
- Deducted on earnings up to a maximum of $152,790 (maximum levy $2,551.59).

Source: IRD ACC earners' levy rates: https://www.ird.govt.nz/acclevy

Note: IRD PAYE deduction tables include ACC earners' levy in the PAYE withheld amount. This calculator shows income tax and ACC earners' levy as separate line items for clarity.

### Calculations
1. Calculate annual income tax using progressive tax brackets.
2. Calculate annual KiwiSaver employee contribution:
	- `KiwiSaver = AnnualSalary × KiwiSaverRate`
3. Calculate annual ACC earners' levy:
	- `ACC = min(AnnualSalary, 152,790) × 1.67%`
4. Calculate annual take-home pay:
	- `TakeHome = AnnualSalary - IncomeTax - KiwiSaver - ACC`
5. Divide all annual amounts by 12 to get monthly figures.

Rounding: display monthly values rounded to 2 decimal places.

### Output Display
The calculator should display:
- **Monthly Gross Salary**: Annual salary ÷ 12
- **Monthly Income Tax (PAYE)**: Annual income tax ÷ 12
- **Monthly KiwiSaver**: Annual KiwiSaver ÷ 12
- **Monthly ACC Levy**: Annual ACC ÷ 12
- **Monthly Take-Home Pay**: Monthly gross - monthly deductions

### Assumptions / Out of Scope
- Assumes a single main salary/wage income stream.
- Does not model tax codes (M/ME/etc), IETC, secondary tax, student loan repayments, child support, tailored tax codes, lump sums, or pay-period rounding rules from the PAYE tables.

For exact PAYE withholding by pay period and tax code, refer to IRD's PAYE deductions calculator: https://www.ird.govt.nz/employing-staff/deductions-from-income/deductions-from-salary-and-wages/work-out-paye-deductions-from-salary-or-wages

### Example
For an annual salary of $60,000:
- Monthly Gross: $5,000.00
- Monthly Income Tax (PAYE): ~$851.71
- Monthly KiwiSaver: $150.00
- Monthly ACC: $83.50
- Monthly Take-Home: ~$3,914.79

