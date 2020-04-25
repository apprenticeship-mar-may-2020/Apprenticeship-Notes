##### ATM Kata

This was the description of what was required:
https://github.com/codurance/apprenticeship/wiki/Bank-Kata-CRC

#### Bank Kata CRC
Pedro Santos edited this page on 22 Jan 2018 · 7 revisions
Kata
In this session, we will do an OO design for a known kata, using paper and pencil only.

The objective is to define a design for the first story of a new product we will be launching soon.
We will be creating a design using CRC cards, following OO design principles such as SOLID, DRY, YAGNI, Tell Don’t Ask, etc.


###### The product is a personal savings ATM machine like this:
For the first release
We will disregard the card reader and user authentication, safe, money counting.
We will implement the Deposit, Withdraw and Print Statement operations.
We have the following GTW definition for our first user story:

Given a kid makes a deposit of 10 on 10-01-2012
And a deposit of 20 on 13-01-2012
And a withdrawal of 5 on 14-01-2012
When they print their bank statement

Then they would see:
date || credit || debit || balance
14/01/2012 || || 5.00 || 25.00
13/01/2012 || 20.00 || || 30.00
10/01/2012 || 10.00 || || 10.00

###### Tips
When you create the cards try to tell a story of each operation using the cards
Don't jump from operation to operation try to finish one before starting another
Try to spot any missing cards in between cards, if you describe an operation and mention something not represented by a card you may want to add a card to represent that missing abstraction
Test your design, look for SOLID violations, OO principles violations, Code smells.
Look for external concepts leaking into the internals of your design
Design outside in to avoid creating abstractions that are not serving a client

This was our solution
![Screenshot 2020-03-13 at 12 36 23](https://user-images.githubusercontent.com/27693622/76621446-44c34800-6527-11ea-99f6-a13cd33a6224.png)

Here we used validate user on the ATM, we also delegated deposit / withdrawal to the Account and requesting transactions. We also delegated Printing the statement to the Printer. The Printer was in charge of formatting transactions and sending formatted to output. This class had a dependency on the Transaction. 

Next we looked at the Account. This included a create transaction and then sending this transaction to the repository. These commands depended on Transaction and Transaction Repository respectively. Next the Transaction Repo had the behaviour of storing or returning a list of transactions and also returning a balance from the last transaction. For both of these it depended on Transaction. The Transaction was a POJO so this contained the amount, date and running balance.

#### In the afternoon we are using the same link:

Bank Kata CRC
Pedro Santos edited this page on 22 Jan 2018 · 7 revisions
Kata
In this session, we will do an OO design for a known kata, using paper and pencil only.

The objective is to define a design for the first story of a new product we will be launching soon.
We will be creating a design using CRC cards, following OO design principles such as SOLID, DRY, YAGNI, Tell Don’t Ask, etc.
The product is a personal savings ATM machine like this:
For the first release
We will disregard the card reader and user authentication, safe, money counting.
We will implement the Deposit, Withdraw and Print Statement operations.
We have the following GTW definition for our first user story:

Given a kid makes a deposit of 10 on 10-01-2012
And a deposit of 20 on 13-01-2012
And a withdrawal of 5 on 14-01-2012
When they print their bank statement

Then they would see:
date || credit || debit || balance
14/01/2012 || || 5.00 || 25.00
13/01/2012 || 20.00 || || 30.00
10/01/2012 || 10.00 || || 10.00

Tips
When you create the cards try to tell a story of each operation using the cards
Don't jump from operation to operation try to finish one before starting another
Try to spot any missing cards in between cards, if you describe an operation and mention something not represented by a card you may want to add a card to represent that missing abstraction
Test your design, look for SOLID violations, OO principles violations, Code smells.
Look for external concepts leaking into the internals of your design
Design outside in to avoid creating abstractions that are not serving a client

##### For this part of the day we are expected to make a Sequence diagram. Here we should use a sequence diagram.

![Screenshot 2020-03-13 at 12 50 49](https://user-images.githubusercontent.com/27693622/76622602-bac8ae80-6529-11ea-8c0f-80b7663b8508.png)

In the above sequence diagram we have Classes such as Moe Customer, TripFinder, Trip class, Bicycle class. Then we use suitable_trips(on_date, of_difficulty, need_bike) which is called on TripFinder. This class then calls the TripClass next with suitable_trips(on_date, of_difficulty). It then uses a for to find a suitable bicycle with suitable_bicycle(trip_date, route_type) which is called on Bicycle.

![Screenshot 2020-03-13 at 12 57 56](https://user-images.githubusercontent.com/27693622/76622884-4d694d80-652a-11ea-91d6-e59cd1897b55.png)

This is all the classes in more detail:

![Screenshot 2020-03-13 at 12 58 48](https://user-images.githubusercontent.com/27693622/76622951-6b36b280-652a-11ea-9cb9-0c9161f8f24a.png)

Here we have a loop method which is called on the Trip class with prepare(preparers). This then loops over each preparer in the array and depending on the class it calls prepare bicycles(bicycles) if it is a mechanic. If the class is a TripCoordinator we then call buy_food(customers). Next we call gas_up(vehicle) and fill_water_tank(vehicle) which is caled if the class is Driver.

###### This is Richard Wild's solution:
https://github.com/richardjwild/bank-kata/tree/master/src/main/java/com/codurance/bank

This included some interesting code such as:
```
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
```

This uses a stream to append the print of transactions to the final output. There is also postTransaction() in Bank Account which looks like this:
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
Here postTransaction(int transactionAmount) is called by deposit(int amount) and withdraw(int amount) respectively ie with -amount or amount. This method creates a stream from all the transactions. It then Maps them all to int after calling Transaction::amount on each one and then uses the .sum() method to add the results together. This is the current balance. This is then added to the Transaction because Transaction has transactionAmount, current time from the clock and the balance + the transactionAmount. If the transactionAmount is positive we would then increase the balance and if it was negative we would then reduce the balance. We then add the transaction to the transactionRepository by using .postTransaction(transaction). The postTransaction in TansactionRepository just adds the transactoin to the transactions array list inside TransactionRepository.
