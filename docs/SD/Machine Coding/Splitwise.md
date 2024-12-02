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

``` java title="User.java"
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


``` java title="Expense.java"
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