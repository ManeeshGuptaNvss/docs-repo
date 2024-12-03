# Chain Of Responsibility

Allows multiple handlers to process a request without the sender needing to know which handler will ultimately process it.

## Example implementations

1. Vending Machine
2. ATM 
3. Logger in an application

```mermaid
classDiagram
    class DispenseChain {
        <<interface>>
        +setNextChain(nextChain: DispenseChain)
        +dispense(currency: Currency)
    }

    class Currency {
        -amount: int
        +Currency(amount: int)
        +getAmount(): int
    }

    class QuarterDispenser {
        -nextChain: DispenseChain
        +setNextChain(nextChain: DispenseChain)
        +dispense(currency: Currency)
    }

    class DimeDispenser {
        -nextChain: DispenseChain
        +setNextChain(nextChain: DispenseChain)
        +dispense(currency: Currency)
    }

    class NickelDispenser {
        -nextChain: DispenseChain
        +setNextChain(nextChain: DispenseChain)
        +dispense(currency: Currency)
    }

    class PennyDispenser {
        -nextChain: DispenseChain
        +setNextChain(nextChain: DispenseChain)
        +dispense(currency: Currency)
    }

    class VendingMachine {
        -c1: DispenseChain
        +VendingMachine()
        +dispense(amount: int)
    }

    DispenseChain <|.. QuarterDispenser
    DispenseChain <|.. DimeDispenser
    DispenseChain <|.. NickelDispenser
    DispenseChain <|.. PennyDispenser

    VendingMachine --> QuarterDispenser : uses
    VendingMachine --> DimeDispenser : uses
    VendingMachine --> NickelDispenser : uses
    VendingMachine --> PennyDispenser : uses

    QuarterDispenser --> DispenseChain : sets next
    DimeDispenser --> DispenseChain : sets next
    NickelDispenser --> DispenseChain : sets next
    PennyDispenser --> DispenseChain : sets next

    QuarterDispenser --> Currency : dispenses
    DimeDispenser --> Currency : dispenses
    NickelDispenser --> Currency : dispenses
    PennyDispenser --> Currency : dispenses

```

```mermaid
classDiagram
    class DispenseChain {
        <<interface>>
        +setNextChain(nextChain: DispenseChain)
        +dispense(currency: Currency)
    }

    class Currency {
        -amount: int
        +Currency(amount: int)
        +getAmount(): int
    }

    class VendingMachine {
        -c1: DispenseChain
        +VendingMachine()
        +dispense(amount: int)
    }

    class CoinDispenser {
        -nextDispenser: CoinDispenser
        +setNextDispenser(nextDispenser: CoinDispenser)
        +dispense(currency: Currency)
    }

    VendingMachine "1" --> "1" DispenseChain
    DispenseChain <|.. CoinDispenser
    CoinDispenser --> CoinDispenser : sets next
    CoinDispenser --> Currency : dispenses

```


