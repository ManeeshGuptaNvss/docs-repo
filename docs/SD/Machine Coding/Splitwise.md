---
icon: fontawesome/solid/arrows-split-up-and-left
---
## Problem Statement

Design and implement an in Memory Expense sharing application where users should be able to add expenses and split it among users. Eg - Splitwise

1. User should be able to add expense which could be divided equally, in percent or exact. For now only splitting equally would work.
2. Expose a function to get all the expense added by user.
3. Expose a function to get balance of a user.
4. Expose a function to get all the expense where the user is part of. 
5. The function to create expense should be able to lake get extra data as per expense type. 
6. For interview we need only equal and exact split to be implemented but solution should beextendible for other type of expense. 
7. In case of exact, it should throw error if sum of shares is not equal to total.

## Approach

- **Interface for Split Calculation**: Define an interface `SplitStrategy` to handle various splitting logic. Each type (e.g., `EqualSplit`, `PercentageSplit`, `ExactSplit`) implements this interface.
- **Expense Class**: Modularize expense creation by leveraging the `SplitStrategy`.
- **SplitType Enum with Factory**: Use a factory pattern to map `SplitType` to the appropriate `SplitStrategy`.
- **Validation**: Add checks for invalid cases like percentage not summing up to 100 or actual amounts not summing up to the total expense.


## Code

``` java title="User.java" linenums="1"
package splitwise;

public class User {
    private final String id;
    private final String name;

    public User(String id, String name) {
        this.id = id;
        this.name = name;
    }
    public String getId() {
        return id;
    }
    @Override
    public String toString() {
        return name;
    }
}
```


``` java title="Expense.java" linenums="1"
package splitwise;

import java.util.List;
import java.util.Map;

public class Expense {
    private final String expenseId;
    private final double amount;
    private final User payer;
    private final List<User> participants;

    public List<User> getParticipants() {
        return participants;
    }

    private final Map<User, Double> splits;

    public Expense(String expenseId, double amount, User payer, List<User> participants, Map<User, Double> splits) {
        this.expenseId = expenseId;
        this.amount = amount;
        this.payer = payer;
        this.participants = participants;
        this.splits = splits;
    }

    @Override
    public String toString() {
        StringBuilder builder = new StringBuilder();
        builder.append("Expense ID: ").append(expenseId).append("\n")
                .append("Payer: ").append(payer).append("\n")
                .append("Amount: ").append(amount).append("\n")
                .append("Participants: ");

        for (User participant : participants) {
            builder.append(participant).append(", ");
        }
        if (!participants.isEmpty()) {
            builder.setLength(builder.length() - 2); // Remove trailing comma and space
        }

        builder.append("\nSplits: \n");
        for (Map.Entry<User, Double> entry : splits.entrySet()) {
            builder.append("  ").append(entry.getKey())
                    .append(": ").append(String.format("%.2f", entry.getValue()))
                    .append("\n");
        }

        return builder.toString();
    }

}
```


``` java title="ExpenseManager.java" linenums="1"
package splitwise;

import splitwise.splitstrategy.SplitType;

import java.util.*;
import java.util.stream.Collectors;

public class ExpenseManager {
    private final Map<String, List<Expense>> userExpenses = new HashMap<>();
    private final Map<String, Map<String, Double>> userBalances = new HashMap<>();

    public void addExpense(String expenseId, double amount, User payer, List<User> participants, SplitType splitType, Map<User, Double> metaData) {
        Map<User, Double> splits = splitType.getStrategy().calculateSplits(amount, participants, metaData);

        Expense expense = new Expense(expenseId, amount, payer, participants, splits);
        userExpenses.computeIfAbsent(payer.getId(), k -> new ArrayList<>()).add(expense);

        for (Map.Entry<User, Double> entry : splits.entrySet()) {
            User participant = entry.getKey();
            double share = entry.getValue();
            if (!participant.getId().equals(payer.getId())) {
                userBalances.computeIfAbsent(participant.getId(), k -> new HashMap<>())
                        .merge(payer.getId(), share, Double::sum);
                userBalances.computeIfAbsent(payer.getId(), k -> new HashMap<>())
                        .merge(participant.getId(), -share, Double::sum);
            }
        }
    }

    public List<Expense> getUserExpenses(String userId) {
        return userExpenses.getOrDefault(userId, new ArrayList<>());
    }

    public String getUserBalance(String userId) {
        Map<String, Double> userBalanceMap = userBalances.get(userId);

        if (userBalanceMap == null || userBalanceMap.isEmpty()) {
            return "No balances found for user with ID: " + userId;
        }

        StringBuilder builder = new StringBuilder();
        builder.append("Balance for user ID: ").append(userId).append("\n");
        for (Map.Entry<String, Double> entry : userBalanceMap.entrySet()) {
            String otherUserId = entry.getKey();
            double balance = entry.getValue();
            builder.append("  With User ID: ").append(otherUserId)
                    .append(" -> ").append(balance >= 0 ? "Owes " : "Owed ")
                    .append(String.format("%.2f", Math.abs(balance)))
                    .append("\n");
        }
        return builder.toString();
    }


    public String getAllUserInvolvedExpenses(String userId) {
        List<Expense> expenses = new ArrayList<>();
        userExpenses.values().forEach(expense ->
                expense.stream()
                        .filter(e -> e.toString().contains(userId))
                        .forEach(expenses::add));
        List<Expense> userExpenses = expenses.stream()
                .filter(expense -> expense.getParticipants().stream()
                        .anyMatch(participant -> participant.getId().equals(userId)))
                .collect(Collectors.toList());

        if (expenses.isEmpty()) {
            return "No expenses found for user with ID: " + userId;
        }

        StringBuilder builder = new StringBuilder();
        builder.append("Expenses involving user ID: ").append(userId).append("\n");
        for (Expense expense : userExpenses) {
            builder.append(expense.toString()).append("\n");
        }
        return builder.toString();
    }
}
```

``` java title="SplitStrategy.java" linenums="1"
package splitwise.splitstrategy;

import splitwise.User;

import java.util.List;
import java.util.Map;

 public interface SplitStrategy {
    Map<User, Double> calculateSplits(double totalAmount, List<User> participants, Map<User, Double> metaData);
}
```

``` java title="EqualSplit.java" linenums="1"
package splitwise.splitstrategy;

import splitwise.User;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class EqualSplit implements SplitStrategy {
    @Override
    public Map<User, Double> calculateSplits(double totalAmount, List<User> participants, Map<User, Double> metaData) {
        double splitAmount = totalAmount / participants.size();
        Map<User, Double> splits = new HashMap<>();
        for (User participant : participants) {
            splits.put(participant, splitAmount);
        }
        return splits;
    }
}
```

``` java title="ExactSplit.java" linenums="1"
package splitwise.splitstrategy;

import splitwise.User;
import java.util.*;

public class ExactSplit implements SplitStrategy {
    @Override
    public Map<User, Double> calculateSplits(double totalAmount, List<User> participants, Map<User, Double> metaData) {
        double totalSplit = metaData.values().stream().mapToDouble(Double::doubleValue).sum();
        if (totalSplit != totalAmount) {
            throw new IllegalArgumentException("Exact amounts must sum up to the total amount.");
        }

        Map<User, Double> splits = new HashMap<>();
        for (User participant : participants) {
            splits.put(participant, metaData.getOrDefault(participant, 0.0));
        }
        return splits;
    }
}
```

``` java title="PercentageSplit.java" linenums="1"
package splitwise.splitstrategy;

import splitwise.User;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class PercentageSplit implements SplitStrategy {
    @Override
    public Map<User, Double> calculateSplits(double totalAmount, List<User> participants, Map<User, Double> metaData) {
        double totalPercentage = metaData.values().stream().mapToDouble(Double::doubleValue).sum();
        if (totalPercentage != 100.0) {
            throw new IllegalArgumentException("Percentages must sum up to 100.");
        }

        Map<User, Double> splits = new HashMap<>();
        for (User participant : participants) {
            double percentage = metaData.getOrDefault(participant, 0.0);
            splits.put(participant, (percentage / 100) * totalAmount);
        }
        return splits;
    }
}

```


``` java title="SplitType.java" linenums="1"
package splitwise.splitstrategy;

public enum SplitType {
    EQUAL(new EqualSplit()),
    PERCENTAGE(new PercentageSplit()),
    EXACT(new ExactSplit());

    private final SplitStrategy strategy;

    SplitType(SplitStrategy strategy) {
        this.strategy = strategy;
    }

    public SplitStrategy getStrategy() {
        return strategy;
    }
}
```

``` java title="ExpenseSharingApp.java" linenums="1"
package splitwise;
import splitwise.splitstrategy.SplitType;

import java.util.*;
public class ExpenseSharingApp {
    public static void main(String[] args) {
        User user1 = new User("1", "Alice");
        User user2 = new User("2", "Bob");
        User user3 = new User("3", "Charlie");

        ExpenseManager manager = new ExpenseManager();

        // Equal Split
        manager.addExpense("exp1", 300, user1, Arrays.asList(user1, user2, user3), SplitType.EQUAL, new HashMap<>());
        System.out.println("Expenses added by Alice:");
        System.out.println(manager.getUserExpenses(user1.getId()));

        // Percentage Split
        Map<User, Double> percentageMetaData = new HashMap<>();
        percentageMetaData.put(user2, 50.0);
        percentageMetaData.put(user3, 50.0);
        manager.addExpense("exp2", 200, user1, Arrays.asList(user1, user2, user3), SplitType.PERCENTAGE, percentageMetaData);
        System.out.println("\nExpenses added by Alice (Percentage Split):");
        System.out.println(manager.getUserExpenses(user1.getId()));

        // Exact Split
        Map<User, Double> exactMetaData = new HashMap<>();
        exactMetaData.put(user2, 100.0);
        exactMetaData.put(user3, 100.0);
        manager.addExpense("exp3", 200, user1, Arrays.asList(user1, user2, user3), SplitType.EXACT, exactMetaData);
        System.out.println("\nExpenses added by Alice (Exact Split):");
        System.out.println(manager.getUserExpenses(user1.getId()));

        // Get balances
        System.out.println("\nBalance of Bob:");
        System.out.println(manager.getUserBalance(user2.getId()));

        System.out.println("\nAll expenses where Charlie is involved:");
        System.out.println(manager.getAllUserInvolvedExpenses(user3.getId()));
    }
}
```