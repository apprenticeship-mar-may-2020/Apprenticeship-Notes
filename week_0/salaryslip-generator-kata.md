##### Salary Slip Kata:
https://github.com/codurance/SalarySlipKata


###### Comments on the kata on Slack
I haven’t had a chance to try this kata yet - i think i will try this afternoon.
i think what you’re saying makes sense, because the given
SalarySlipGenerator
signature stipulates that it returns the salarySlip object. however, you could argue that a salarySlip is by definition formatted: so maybe the salarySlip object should have the interface for the formatted data?

##### i don’t know if that made sense or not … sorry

but rather than a salarySlip just containing data, maybe it should publicly give the data in a salarySlip format. though that might violate srp

the alternative i think is to treat this kata like the bank kata, and for salarySlip just to return data, then to pass onto a printer and test that that logs the formatted data. was that what you were thinking of?

SalarySlip object is a domain object, so it shouldn't have behavior I think. We thought having the Service generating the salarySlip POJO and then passing it to the formater.

yeah, just re-reading the briefing. i think you’re right

So the service has the dependency on the generator and the formater, so our domain object (salarySlip) has no knowledge of the external world

i think that makes the most sense. but these lines in the criteria are confusing:

Ewan Sheldon  3 minutes ago
The entry point should be the following interface, which you can not change:
Java:
      public class SalarySlipGenerator {
        public SalarySlip generateFor(Employee employee);
      }
:+1:
1


Alexis Pavlidis  3 minutes ago
Ohh right, didn't see that one

it almost implies that all you need to do in the kata is return a data object … but that can’t really be right

but if I do that, then who is printing the salarySlip? :stuck_out_tongue:

i know :thinking_face: it is confusing

I dont think the generator should be responsible of printing...

no it shouldn’t

but, they don’t actually mention printing. just
The monthly salary slip should contain the below:

so do they just mean they want the objects you return to calculate that data. and they’ve given you the data they expect in that format just to make it easy for you to read

I thought there was a formatter involved

why not ! i think it is more interesting to implement it with a formatter (and some sort of service class)

This was where I started with Alexis:
![screenshot_from_2020-02-28_11-37-26](https://user-images.githubusercontent.com/27693622/75550238-69002e80-5a29-11ea-8d51-c6539a74873b.png)


##### Alexis mentioned snake case. I downloaded string manipulation:
https://stackoverflow.com/questions/17350359/in-intellij-how-do-i-toggle-between-camel-case-and-underscore-spaced

```
I use a plugin called String Manipulation which has the capabilities you're looking for (and more).

Select historyOfPresentIllness and press Alt+M to bring up the plugin menu, then press:

5 - To snake_case (or to camelCase) which converts to history_of_present_illness
6 - To hyphen-case (or to snake_case) which converts to history-of-present-illness
To make this easier, you could set up a shortcut at File | Settings | Keymap.
```

##### The name of this Kata - SalarySlipGenerator is slightly misleading.
- It appears that it is referring merely to a generator of slips
- However this can't quite be the case because there must be some functionality for storing the salary. If this is the case then we should also use a similar process to the AccountShould test in the bankkata description (https://www.youtube.com/watch?v=XHnuMjah6ps)

There is clearly some difference between the SalarySlipGenerator and the Bank kata:
![Screenshot 2020-02-28 at 16 09 05](https://user-images.githubusercontent.com/27693622/75564826-c0f85e80-5a44-11ea-8fad-9705f9eabe3a.png)

##### To restate the issue:
###### Salary slip kata
**Problem description:** Salary slip generator for UK companies.

A typical salary slip contains employee details like employee 
id, employee name and their monthly salary details like their 
gross salary, national insurance contributions, tax-free 
allowance, taxable income and tax payable.

Salary slips are generated each month for every employee.

##### Acceptance criteria:
- Salary slip generator should pass an employee with its 
Employee Id, Employee Name and Annual Gross Salary
Salary slip should contain the Employee ID, Employee Name, 
Gross Salary, National Insurance contributions, Tax-free 
allowance, Taxable income and Tax payable for the month
- The entry point should be the following interface, which you 
can not change:
Java:
```
public class SalarySlipGenerator {
  public SalarySlip generateFor(Employee employee);
}
```

##### This is the bank kata:
###### Exercise 05 - Outside-in TDD flow Objective:
Learn and practice the double loop of TDD 
(read blog post by Emily Bache, also watch videos on http://codurance.com/videos/ from under sections Oustide-In TDD - part I, II & III)

Test application from outside, according to side effect

##### Problem description: Bank kata
Create a simple bank application with the following features:
Deposit into Account
Withdraw from an Account
Print a bank statement to the console.
The entry point should be the following interface, which you can not change. Don't worry about introducing abstractions on Money or Date as this is not the point of the exercise.




##### This was an example of an attempt at the SalarySlip Kata:
https://github.com/alvarogarcia7/salaryslip-kata-java/blob/master/src/test/java/com/example/kata/salaryslip/unit/SalarySlipShould.java

I am looking at this and have made some progress with this test on the feature:
```
package com.codurance.salaryslipkata;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InOrder;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.mockito.Mockito.verify;

@ExtendWith(MockitoExtension.class)
public class SalarySlipFeature {

  private SalarySlipGenerator salarySlipGenerator;

  @Mock
  Console console;

  @Mock
  Employee employee;

  @BeforeEach
  void setUp() {
    salarySlipGenerator = new SalarySlipGenerator();
  }

  @Test
  public void generate_monthly_salary_slip(){
    salarySlipGenerator.generateFor(employee);

    InOrder inOrder = Mockito.inOrder(console);

    inOrder.verify(console).printLine("Employee ID: 12345");
    inOrder.verify(console).printLine("John J Doe");
    inOrder.verify(console).printLine("£416.67");
  }
}

```
I have now got to a unit test for SalarySlipGenerator.

It is interesting that the error for the Feature Test now shows the same error as that shown in Sandro Mancuso's video:
https://www.youtube.com/watch?v=XHnuMjah6ps

![Screenshot 2020-02-28 at 15 31 57](https://user-images.githubusercontent.com/27693622/75561842-8f30c900-5a3f-11ea-974a-d2ba84b1a41c.png)

##### This is the InOrder method on Mockito:
```
 InOrder inOrder = inOrder(firstMock, secondMock);
 
 inOrder.verify(firstMock).add("was called first");
 inOrder.verify(secondMock).add("was called second");
```

#### This is the salaryslip kata answer from Sandro Mancuso:
This should be the model to work from:
https://github.com/sandromancuso/salaryslipkata/blob/master/src/test/java/acceptance/steps/GenerateSalarySlipFeature.java


These are the tests for S Mancuso SalarySlip:
```
    @Test
    @Parameters({
            "11000.00, 11000.00, 0.00, 0.00",
            "5000.00, 5000.00, 0.00, 0.00"
    })
    public void
    calculate_zero_percent_tax_for_earnings_up_to_11000(BigDecimal annualGrossSalary,
                                                     BigDecimal taxFreeAllowance,
                                                     BigDecimal taxableIncome,
                                                     BigDecimal taxPayable) {
        TaxInfo taxInfo = new TaxInfo(annualGrossSalary);

        assertThat(taxInfo.taxFreeAllowance()).isEqualTo(taxFreeAllowance);
        assertThat(taxInfo.taxableIncome()).isEqualTo(taxableIncome);
        assertThat(taxInfo.taxPayable()).isEqualTo(taxPayable);
    }

    @Test
    @Parameters({
            "12000.00, 11000.00, 1000.00, 200.00",
            "30000.00, 11000.00, 19000.00, 3800.00",
            "43000.00, 11000.00, 32000.00, 6400.00"
    })
    public void
    calculate_20_percent_tax_for_earnings_between_11000_and_43000(BigDecimal annualGrossSalary,
                                                                    BigDecimal taxFreeAllowance,
                                                                    BigDecimal taxableIncome,
                                                                    BigDecimal taxPayable) {
        TaxInfo taxInfo = new TaxInfo(annualGrossSalary);

        assertThat(taxInfo.taxFreeAllowance()).isEqualTo(taxFreeAllowance);
        assertThat(taxInfo.taxableIncome()).isEqualTo(taxableIncome);
        assertThat(taxInfo.taxPayable()).isEqualTo(taxPayable);
    }

    @Test
    @Parameters({
            "45000.00, 11000.00, 34000.00, 7200.00",
            "50000.00, 11000.00, 39000.00, 9200.00",
            "100000.00, 11000.00, 89000.00, 29200.00"
    })
    public void
    calculate_40_percent_tax_for_earnings_between_43000_and_150000(BigDecimal annualGrossSalary,
                                                        BigDecimal taxFreeAllowance,
                                                        BigDecimal taxableIncome,
                                                        BigDecimal taxPayable) {
        TaxInfo taxInfo = new TaxInfo(annualGrossSalary);

        assertThat(taxInfo.taxFreeAllowance()).isEqualTo(taxFreeAllowance);
        assertThat(taxInfo.taxableIncome()).isEqualTo(taxableIncome);
        assertThat(taxInfo.taxPayable()).isEqualTo(taxPayable);
    }

    @Test
    @Parameters({
            "101000, 10500.00, 90500.00, 29800.00",
            "111000, 5500.00, 105500.00, 35800.00",
            "122000, 0.00, 122000.00, 42400.00",
            "150000, 0.00, 150000.00, 53600.00"
    })
    public void
    have_tax_free_allowance_decreased_by_1_for_every_2_pounds_for_salaries_above_100K(
                                                        BigDecimal annualGrossSalary,
                                                        BigDecimal taxFreeAllowance,
                                                        BigDecimal taxableIncome,
                                                        BigDecimal taxPayable) {
        TaxInfo taxInfo = new TaxInfo(annualGrossSalary);

        assertThat(taxInfo.taxFreeAllowance()).isEqualTo(taxFreeAllowance);
        assertThat(taxInfo.taxableIncome()).isEqualTo(taxableIncome);
        assertThat(taxInfo.taxPayable()).isEqualTo(taxPayable);
    }

    @Test
    @Parameters({
            "160000.00, 0.00, 160000.00, 58100.00",
            "200000.00, 0.00, 200000.00, 76100.00"
    })
    public void
    calculate_45_percent_tax_for_earnings_above_150000(
                                                BigDecimal annualGrossSalary,
                                                BigDecimal taxFreeAllowance,
                                                BigDecimal taxableIncome,
                                                BigDecimal taxPayable) {
        TaxInfo taxInfo = new TaxInfo(annualGrossSalary);

        assertThat(taxInfo.taxFreeAllowance()).isEqualTo(taxFreeAllowance);
        assertThat(taxInfo.taxableIncome()).isEqualTo(taxableIncome);
        assertThat(taxInfo.taxPayable()).isEqualTo(taxPayable);
    }

```