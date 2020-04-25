###### Bank Kata
https://katalyst.codurance.com/bank

https://katalyst.codurance.com/bank

https://github.com/richardjwild/bank-kata/blob/master/src/main/java/com/codurance/bank/Transaction.java

This is the description:
```
When designing a big system, we like to base our design on the way that the system will be used. That way, user stories and acceptance criteria become much more than just a finish line: they are a guiding principle for the entire system.

This solves a variety of problems. For example, it eliminates over-engineering (since we only write what we know the user needs). Starting at the public interface moves risk away from the end of the project (nobody wants an integration nightmare when deadline day is looming).

This Kata aims to distill that experience into a problem that can be knocked on the head in a couple of hours, writing a primitive bank account program. In this case, our user interface is just some public methods - assume we're writing a library. But the same principles hold.

It's a fantastic way to practice using acceptance tests to guide your design. If done correctly, the result will be a system that evolves itself with no extraneous effort and no nasty surprises at the end. You will see how the outside-in way of working can be a powerful way of creating object-oriented software.

You can attempt this using Mockist or Classicist TDD, but we find that it's especially well suited to a Mockist approach. Consider using Sandro Mancuso's screencast implementation as a reference.

Write a class named Account that implements the following public interface:

public interface AccountService
{
    void deposit(int amount) 
    void withdraw(int amount) 
    void printStatement()
}
You cannot change the public interface of this class.
Here's the specification for an acceptance test that expresses the desired behaviour for this

Given a client makes a deposit of 1000 on 10-01-2012
And a deposit of 2000 on 13-01-2012
And a withdrawal of 500 on 14-01-2012
When they print their bank statement
Then they would see:

Date       || Amount || Balance
14/01/2012 || -500   || 2500
13/01/2012 || 2000   || 3000
10/01/2012 || 1000   || 1000
We're using ints for the money amounts to keep the auxiliaries as simple as possible. In a real system, we would always use a datatype with guaranteed arbitrary precision, but doing so here would distract from the main purpose of the exercise.
Don't worry about spacing and indentation in the statement output. (You could instruct your acceptance test to ignore whitespace if you wanted to.)
Use the acceptance test to guide your progress towards the solution. Sandro does this by making all unimplemented methods throw an exception, so that he can immediately see what remains to be implemented when he runs the acceptance test.
When in doubt, go for the simplest solution!


```


Bank kata
Andrzej Rehmann edited this page on 11 Nov 2017 · 2 revisions
Think of your personal bank account experience When in doubt, go for the simplest solution.

Requirements:

Deposit and Withdrawal
Transfer
Account statement (date, amount, balance)
Statement printing
Statement filters (just deposits, withdrawal, date)
Given a client makes a deposit of 1000 on 10-01-2012
And a deposit of 2000 on 13-01-2012
And a withdrawal of 500 on 14-01-2012
When she prints her bank statement
Then she would see
```
date || credit || debit || balance
14/01/2012 || || 500.00 || 2500.00
13/01/2012 || 2000.00 || || 3000.00
10/01/2012 || 1000.00 || || 1000.00
```

This is Sandro Mancuso's version:
https://github.com/sandromancuso/bank-kata-outsidein-screencast

This is Richard Wild:
https://github.com/richardjwild/bank-kata

This is the Sandro Mancuso video on Outside in TDD (on the bank kata):
https://codurance.com/videos/2015-05-12-outside-in-tdd-part-1/

There are 3 videos.

This is my version of the bank kata so far:
https://github.com/TomSpencerLondon/Bank-Kata---Java

I have implemented a print statement feature test:
```

package com.codurance.bank;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InOrder;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.mockito.Mockito.inOrder;
import static org.mockito.Mockito.verify;

@ExtendWith(MockitoExtension.class)
public class PrintStatementFeature {
  @Mock
  Console console;

  private Account account;

  @BeforeEach
  void setUp() {
    account = new Account();
  }

  @Test
  public void print_statement_containing_all_transactions(){
    account.deposit(1000);
    account.withdraw(100);
    account.deposit(500);

    account.printStatement();

    InOrder inOrder = inOrder(console);

    inOrder.verify(console).printLine("DATE | AMOUNT | BALANCE");
    inOrder.verify(console).printLine("10/04/2014 | 500.00 | 1400.00");
    inOrder.verify(console).printLine("02/04/2014 | -100.00 | 900.00");
    inOrder.verify(console).printLine("01/04/2014 | 1000.00 | 1000.00");
  }
}
```
This checks that printLine is called in order on console.

So far we have the Account and its methods:
```
package com.codurance.bank;

public class Account {
  public void deposit(int amount) {
  }

  public void withdraw(int amount) {
  }

  public void printStatement() {
  }
}
```
and also Console which has printLine:
```
package com.codurance.bank;

public class Console {
  public void printLine(String text){
    throw new UnsupportedOperationException();
  }
}
```

This is Richard Wild's Bank account should test:
https://github.com/richardjwild/bank-kata/blob/master/src/test/java/com/codurance/bank/BankAccountShould.java
```
package com.codurance.bank;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.runners.MockitoJUnitRunner;

import java.time.LocalDateTime;
import java.util.List;

import static java.util.Arrays.asList;
import static java.util.Collections.emptyList;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;

@RunWith(MockitoJUnitRunner.class)
public class BankAccountShould {

    @Mock
    TransactionRepository transactionRepository;

    @Mock
    Clock clock;

    @Mock
    Statement statement;

    @InjectMocks
    private BankAccount account;

    @Test
    public void post_a_deposit() {
        int amount = 10;
        LocalDateTime timestamp = LocalDateTime.now();
        when(clock.currentTime()).thenReturn(timestamp);
        when(transactionRepository.findAllTransactions()).thenReturn(emptyList());

        account.deposit(amount);

        Transaction depositTransaction = new Transaction(amount, timestamp, amount);
        verify(transactionRepository).postTransaction(depositTransaction);
    }

    @Test
    public void post_a_withdrawal() {
        LocalDateTime timestamp = LocalDateTime.now();
        int amount = 10;
        when(clock.currentTime()).thenReturn(timestamp);
        when(transactionRepository.findAllTransactions()).thenReturn(emptyList());

        account.withdraw(amount);

        Transaction withdrawalTransaction = new Transaction(-amount, timestamp, -amount);
        verify(transactionRepository).postTransaction(withdrawalTransaction);
    }

    @Test
    public void print_a_statement() {
        List<Transaction> listOfTransactions = asList(aTransaction(), aTransaction());
        when(transactionRepository.findAllTransactions()).thenReturn(listOfTransactions);

        account.statement();

        verify(statement).print(listOfTransactions);
    }

    @Test
    public void calculate_running_balance() {
        LocalDateTime timestamp = LocalDateTime.now();
        when(clock.currentTime()).thenReturn(timestamp);
        Transaction firstTransaction = new Transaction(10, timestamp, 10);
        Transaction secondTransaction = new Transaction(-5, timestamp, 5);
        when(transactionRepository.findAllTransactions())
                .thenReturn(emptyList())
                .thenReturn(asList(firstTransaction));

        account.deposit(10);
        account.withdraw(5);

        verify(transactionRepository).postTransaction(firstTransaction);
        verify(transactionRepository).postTransaction(secondTransaction);
    }

    private Transaction aTransaction() {
        return new Transaction(1, LocalDateTime.now(), 0);
    }

}
```


We resolved some module erros by typing:
```
mvn clean install
```

This was Richard Wild's implementation of the print method on Statement using a stream:
```
package com.codurance.bank;

import java.util.Comparator;
import java.util.List;

public class Statement {

    private Output output;
    private final TransactionFormatter formatter;

    public Statement(Output output, TransactionFormatter formatter) {
        this.output = output;
        this.formatter = formatter;
    }

    public void print(List<Transaction> listOfTransactions) {
        StringBuilder stringBuilder = new StringBuilder();

        stringBuilder.append("date || credit || debit || balance");


        listOfTransactions.stream()
                .sorted(Comparator.comparing(Transaction::timestamp).reversed())
                .forEach(transaction -> {
            stringBuilder.append("\n");
            stringBuilder.append(formatter.format(transaction));
        });

        output.print(stringBuilder.toString());
    }
}
```
Here the main part of this class uses stream() on the List:
```
        listOfTransactions.stream()
                .sorted(Comparator.comparing(Transaction::timestamp).reversed())
                .forEach(transaction -> {
            stringBuilder.append("\n");
            stringBuilder.append(formatter.format(transaction));
```
It then sorts the list of transactions according to the timestamp ie LocalDateTime on the Transaction. We used the Calendar class in our implementation. It then reverses them and calls forEach to append to the Stringbuilder and call fomatter.format(transaction) and append this result to the resulting string on each transaction. These methods were quite interesting on the Account:
```
    public void deposit(int amount) {
        postTransaction(amount);
    }

    public void withdraw(int amount) {
        postTransaction(-amount);
    }

    private void postTransaction(int transactionAmount) {
        int balance = transactionRepository.findAllTransactions().stream()
                .mapToInt(Transaction::amount)
                .sum();

        Transaction transaction = new Transaction(
                transactionAmount,
                clock.currentTime(), balance + transactionAmount);
        transactionRepository.postTransaction(transaction);
    }
```
Here we use postTransaction to create a stream and sum the amount from all the transactions. We the create a new Transaction on which we store the balance within the Transaction. This is not necessarily a good idea but does solve the 
problem of adding the balance as we go along in the format section of the TransactionFormatter:
```

package com.codurance.bank;

import java.time.format.DateTimeFormatter;

public class TransactionFormatter {

    public String format(Transaction transaction) {
        if (transaction.amount() > 0) {
            return String.format("%s || %.2f || || %.2f",
                    transaction.timestamp().format(DateTimeFormatter.ofPattern("dd/MM/yyyy")),
                    (float) transaction.amount(),
                    (float) transaction.balance());
        } else {
            return String.format("%s || || %.2f || %.2f",
                    transaction.timestamp().format(DateTimeFormatter.ofPattern("dd/MM/yyyy")),
                    (float) transaction.amount() * -1,
                    (float) transaction.balance());
        }
    }
}
```
This is quite a nice means by which we add the balance incrementally to the Transaction printout. Sandro Mancuso on the other hand uses a slightly cleaner way of building the runningBalance:
```
package com.codurance;

import com.codurance.bankkata.Console;

import java.text.DecimalFormat;
import java.util.List;
import java.util.concurrent.atomic.AtomicInteger;

import static com.google.common.collect.Lists.reverse;
import static java.util.stream.Collectors.toList;

public class StatementPrinter {

	public static final String STATEMENT_HEADER = "DATE | AMOUNT | BALANCE";

	private Console console;

	public StatementPrinter(Console console) {
		this.console = console;
	}

	public void print(List<Transaction> transactionList) {
		printHeader();
		printStatementLinesFor(transactionList);
	}

	private void printHeader() {
		console.printLine(STATEMENT_HEADER);
	}

	private void printStatementLinesFor(List<Transaction> transactionList) {
		List<String> statementLines = statementLines(transactionList);
		reverse(statementLines).forEach(console::printLine);
	}

	private List<String> statementLines(List<Transaction> transactionList) {
		AtomicInteger runningBalance = new AtomicInteger(0);
		return transactionList
							.stream()
							.map(transaction -> statementLine(runningBalance, transaction))
							.collect(toList());
	}

	private String statementLine(AtomicInteger runningBalance, Transaction t) {
		return t.date() +
				" | " +
				formatDecimal(t.amount()) +
				" | " +
				formatDecimal(runningBalance.addAndGet(t.amount()));
	}

	private String formatDecimal(int amount) {
		DecimalFormat decimalFormat = new DecimalFormat("#.00");
		return decimalFormat.format(amount);
	}
}
```
Here Sandro uses:
```
AtomicInteger runningBalance = new AtomicInteger(0);
```
He then calls statementLine on the map after he has turned the transactionList to a stream():
```
		return transactionList
							.stream()
							.map(transaction -> statementLine(runningBalance, transaction))
							.collect(toList());
```
Here we call statementLine on each instance of the Transaction List. In the statementLine() mehtod we call:
```
runningBalance.addAndGet(t.amount())
```
before we order the transactions in reverse order which means that we get a running balance on the transactions. We then use the printStatement which calls reverse using:
```
import static com.google.common.collect.Lists.reverse;
```
and then print each line to the console:
```
reverse(statementLines).forEach(console::printLine);
```

###### Sebastian and My bank kata

Our bank kata started with a Feature test called AccountFeature:
```
package com.codurance.bank_kata;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.mockito.Mockito.*;


//  Date       || Amount || Balance
//          14/01/2012 || -500   || 2500
//          13/01/2012 || 2000   || 3000
//          10/01/2012 || 1000   || 1000
@ExtendWith(MockitoExtension.class)
public class AccountFeature {

  private Account account;
  @Mock
  Clock clock;

  @Mock
  Console console;

  @BeforeEach
  void setUp() {
    TransactionRepository transactionRepository = new TransactionRepository(clock);
    Formatter formatter = new Formatter();
    account = new Account(transactionRepository, formatter);
    console = new Console();
  }

  @Test
  void print_current_statement_to_console_with_transactions() {

    account.deposit(1000);
    account.deposit(2000);
    account.withdraw(500);

    account.printStatement();

    String statement = "Date || Amount || Balance\n" +
                       "14/01/2012 || -500 || 2500\n" +
                       "13/01/2012 || 2000 || 3000\n" +
                       "10/01/2012 || 1000 || 1000\n";

    verify(console, times(1)).print(statement);
  }
}
```
This used @Mock Clock and @Mock Console to set up the dependencies for the Account. we also used a formatter class which we didn't format in the end. We then added a test for AccountShould:
```
package com.codurance.bank_kata;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static java.util.Arrays.asList;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class AccountShould {
  @Mock
  TransactionRepository transactionRepository;

  private Account account;
  @Mock
  private Formatter formatter;

  @BeforeEach
  void setUp() {
    account = new Account(transactionRepository, formatter);
  }

  @Test
  void add_a_deposit() {
    account.deposit(1000);
    verify(transactionRepository, times(1)).addTransaction(1000);
  }

  @Test
  void withdraw() {
    account.withdraw(500);
    verify(transactionRepository, times(1)).addTransaction(-500);
  }

  @Test
  void print_statement() {
    when(transactionRepository.getTransactions()).thenReturn(asList(
            new Transaction(100, "13/04/2014")
    ));
    account.printStatement();
    verify(formatter, times(1)).print(transactionRepository.getTransactions());
  }
}
```
We managed to look at the adding of transactions but we didn't quite get to the more detailed tests on printing the statement:
```

@ExtendWith(MockitoExtension.class)
class TransactionRepositoryShould {
  private TransactionRepository transactionRepository;

  @Mock
  Clock clock;

  @BeforeEach
  void setUp() {
    transactionRepository = new TransactionRepository(clock);
  }

  @Test
  void add_transaction() {
    when(clock.date()).thenReturn("13/04/2014");
    transactionRepository.addTransaction(1000);
    assertEquals(1, transactionRepository.getTransactions().size());
    assertEquals(transactionRepository.getTransactions().get(0), new Transaction(1000, "13/04/2014"));
  }
}
```


###### Bank Kata with Mob Ewan and Alexis:
This was very interesting because we made our own Mocks and also Alexis gave a nice reference for the different types of Mocks:
```
Dummy-> It is an object that implements an interface that all the functions do nothing and return values close to 0 and null. It is used when something requires an argument and neither the test nor the production code cares what happens to that argument
Stub->It is a dummy but returns a value that is consistent with the needs of the test (returns special fixed values). It helps to drive production code through certain execution paths.
Spy -> It is a stub but it remembers certain things about the way it was called an report those things to the test. Check if a method is called, how many times is called, how many arguments were passed in or what are the values of those arguments. Its useful to check that your code called the external service appropriately.
Mocks-> Its a spy but it knows what should happen. It usually contains a "verify" method that returns a boolean. Tests ask the true mock if everything went as expected.
Fake-> it is a simulator, usually they are complicated. Avoid them if possible
Notes are one year old so please correct them if necessary :D (
```
This is the github:
https://github.com/ewansheldon/bank-apprenticeship/blob/master/src/main/java/com/codurance/bank/service/AccountServiceImplementation.java

This will be useful for revision of mocks.


###### Aaron Contreras used insufficient funds exception:
https://github.com/aaron-contreras/bank-kata/blob/master/src/main/java/com/aaroncontreras/BankAccount.java
```
    public void withdraw(int amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException();
        }
        balance -= amount;
        transactions.add(new Transaction(-amount));
    }
```
I quite like this for if the withdrawal is more than the balance.

This is an argument for keeping the Balance in the Transaction as Richard Wild did.


###### This was the overview of numeric data types in Java:
![Screenshot 2020-03-18 at 08 35 54](https://user-images.githubusercontent.com/27693622/76941261-99820c80-68f3-11ea-958b-719f33bde353.png)

https://cs.fit.edu/~ryan/java/language/java-data.html


This was the feedback loop that Jose referred to:

![Screenshot 2020-03-17 at 09 14 33](https://user-images.githubusercontent.com/27693622/76941359-c7ffe780-68f3-11ea-8f89-ef6128d812b7.png)

This was Sebastian's computer:

![Screenshot 2020-03-17 at 09 29 02](https://user-images.githubusercontent.com/27693622/76941407-de0da800-68f3-11ea-8cc8-998e854872a3.png)

This is my one:

![Screenshot 2020-03-17 at 09 28 47](https://user-images.githubusercontent.com/27693622/76941445-f2ea3b80-68f3-11ea-9a41-e8f570796d08.png)

Alexis claimed that 2018 is the best model for mac.



###### This was Richard Wild's response:
for TransactionFormatter
https://github.com/richardjwild/bank-kata/blob/master/src/main/java/com/codurance/bank/TransactionFormatter.java
```
package com.codurance.bank;

import java.time.format.DateTimeFormatter;

public class TransactionFormatter {

    public String format(Transaction transaction) {
        if (transaction.amount() > 0) {
            return String.format("%s || %.2f || || %.2f",
                    transaction.timestamp().format(DateTimeFormatter.ofPattern("dd/MM/yyyy")),
                    (float) transaction.amount(),
                    (float) transaction.balance());
        } else {
            return String.format("%s || || %.2f || %.2f",
                    transaction.timestamp().format(DateTimeFormatter.ofPattern("dd/MM/yyyy")),
                    (float) transaction.amount() * -1,
                    (float) transaction.balance());
        }
    }
}
```
This was for the module not specified error:
https://stackoverflow.com/questions/38018575/error-module-not-specified-intellij-idea

##### Repeat: this was Ewan, Alexis and my bank-apprenticeship kata:
https://github.com/ewansheldon/bank-apprenticeship


##### This was another version of the Date Formatter:
```
public class Class1 {
    public static void main(String args[]) {
      Date date = new Date();
      Calendar calendar = Calendar.getInstance();
      calendar.setTime(date);
      SimpleDateFormat SDF = new SimpleDateFormat("yyyy-MM-dd");//you can use any format that you want, for example:("yyyy/MM/dd")
      String s = SDF.format(date);
      System.out.print(s);
      }
}
```
https://stackoverflow.com/questions/6888696/need-to-show-date-in-yyyy-mm-dd-format-in-java


##### The bank kata includes information on Haskell:
https://katalyst.codurance.com/bank


#### Outside In development:
https://codurance.com/2017/10/23/outside-in-design/

Summary
Risk mitigation is an extremely important activity in any software project. We should not mix exploratory work with feature development.

Code should only be written to satisfy an external need, being from a user, an external system, or another piece of code. We should only build what really needs to be built, nothing more.

Guiding our design and code according to external needs helps us to remain focused on the task at hand and avoid speculative development.

Navigation is a delivery mechanism responsibility and backend should not change when navigation changes. The backend should only provide operations (actions) to the front-end.

In order to achieve continuous delivery and deployment, it is important work in small increments. Slicing features in small vertical slices is a great way to make sure each increment has business value.

Outside-In development is a structured and incremental way to deliver software, focus on delivering just enough behaviour to satisfy an external need.

Note: In the next blog posts we will dive into more details of Outside-In development.


##### This is what the Bank Kata looks like in C#:
https://github.com/Gryff/bank-kata/tree/master/C%23/BankKata/BankKata

```
using System.Collections.Generic;

namespace BankKata
{
    public class Account
    {
        private readonly TransactionRepository _transactionRepository;
        private readonly IClock _clock;
        private readonly IConsole _console;
        private readonly string HEADER = "date       || credit   || debit    || balance";

        public Account(TransactionRepository transactionRepository, IClock clock, IConsole console)
        {
            _clock = clock;
            _transactionRepository = transactionRepository;
            _console = console;
        }

        public void Deposit(int amount)
        {
            var deposit = new Transaction(amount, _clock.Now());
            _transactionRepository.AddDeposit(deposit);
        }

        public void Withdraw(int amount)
        {
            var withdrawal = new Transaction(-amount, _clock.Now());
            _transactionRepository.AddWithdrawal(withdrawal);
        }

        public void PrintStatement()
        {
            _console.PrintLine(HEADER);
            this.PrintTransactions();
        }

        private void PrintTransactions()
        {
            List<Transaction> transactions = _transactionRepository.GetTransactions();

            var formattedTransactions =
                this.FormatTransactions(transactions);

            foreach (var formattedTransaction in formattedTransactions)
            {
                _console.PrintLine(formattedTransaction);
            }
        }

        private List<string> FormatTransactions(List<Transaction> transactions)
        {
            int balance = 0;
            List<string> formattedTransactions = new List<string>();

            foreach (var transaction in transactions)
            {
                balance += transaction.Amount;
                formattedTransactions.Add(
                    $"{transaction.ToOutputFormat()} || {balance}.00");
            }

            formattedTransactions.Reverse();

            return formattedTransactions;
        }
    }
}
```


#### Bank Kata 
```
package com.codurance.bank;

import static org.junit.jupiter.api.Assertions.assertEquals;

import com.codurance.bank.UI.Printer;
import com.codurance.bank.utils.Clock;
import com.codurance.bank.domain.Transaction;
import com.codurance.bank.repository.TransactionRepository;
import com.codurance.bank.service.AccountService;
import com.codurance.bank.service.AccountServiceImplementation;
import java.util.ArrayList;
import java.util.List;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

class AccountServiceImplementationShould {

    private TransactionRepositorySpy transactionRepository;
    private Printer printer;
    private AccountService accountServiceImplementation;
    private String DATE;

    @BeforeEach
    void setUp() {
        transactionRepository = new TransactionRepositorySpy();
        printer = new PrinterDummy();
        ClockMock clock = new ClockMock();
        accountServiceImplementation = new AccountServiceImplementation(transactionRepository,
            printer, clock);
    }

    @Test
    void create_transaction_and_send_to_repository_on_deposit() {
        DATE = "10/10/2012";
        int amount = 1000;
        accountServiceImplementation.deposit(amount);

        assertEquals(amount, transactionRepository.getAmount());
        assertEquals(DATE, transactionRepository.getDate());
    }

    @Test
    void create_transaction_and_send_to_repository_on_withdraw() {
        DATE = "11/10/2012";
        int amount = 1000;
        accountServiceImplementation.withdraw(amount);

        assertEquals(-amount, transactionRepository.getAmount());
        assertEquals(DATE, transactionRepository.getDate());
    }

    @Test
    void print_statement_sends_transactions_to_printer() {
        PrinterSpy printer = new PrinterSpy();

        accountServiceImplementation.printStatement();

        ArrayList<Transaction> transactions = new ArrayList<>();
        assertEquals(transactions, printer.verifyPrinted());
    }

    private class TransactionRepositorySpy implements TransactionRepository {

        private int amount;
        private String date;

        public int getAmount() {
            return amount;
        }

        public String getDate() {
            return date;
        }

        public void save(Transaction transaction) {
            amount = transaction.getAmount();
            date = transaction.getDate();
        }

        public List<Transaction> getAll() {
            return null;
        }
    }

    private class PrinterDummy implements Printer {

        public void print(List<Transaction> transactions) {
        }
    }

    private class ClockMock implements Clock {

        public String getCurrentDate() {
            return DATE;
        }
    }

    private class PrinterSpy implements Printer {

        private List<Transaction> transactionsPrinted = new ArrayList<>();

        @Override
        public void print(List<Transaction> transactions) {
            transactionsPrinted = transactions;
        }

        public List<Transaction> verifyPrinted() {
            return transactionsPrinted;
        }
    }
}
```

This test:
```

    @Test
    void create_transaction_and_send_to_repository_on_deposit() {
        DATE = "10/10/2012";
        int amount = 1000;
        accountServiceImplementation.deposit(amount);

        assertEquals(amount, transactionRepository.getAmount());
        assertEquals(DATE, transactionRepository.getDate());
    }
```
This is the test for deposit(amount) on accountServiceImplementation. We assert that the transactionRepository.getAmount() returns the same as amount. It also checks the DATE against the transactionRepository.getDdate().

This returns the date():
```
package com.codurance.bank.domain;

public class Transaction {

    private int amount;
    private final String date;

    public Transaction(int amount, String date) {
        this.amount = amount;
        this.date = date;
    }

    public int getAmount() {
        return amount;
    }

    public String getDate() {
        return date;
    }
}
```