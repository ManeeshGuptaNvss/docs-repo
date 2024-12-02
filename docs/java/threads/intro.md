# Threads Basics

A thread can be created using 2 methods
- extending thread class
- implementing runnable interface

Runnable interface is a functional interface.
```mermaid
classDiagram
    class Runnable {
        <<interface>>
        +run()
    }

    class Thread {
        +start()
        +run()
        +join()
        +sleep(long millis)
        +currentThread()
        +getName()
        +setName(String name)
        +getId()
        +getState()
        +interrupt()
        +isInterrupted()
    }

    Runnable <|.. Thread

```


``` mermaid
---
title: Thread Life Cycle
---
stateDiagram-v2
direction LR
    [*] --> New
    New --> Terminated
		New --> Active
		state Active{
			direction LR
			Runnable-->Running : run
			Running-->Runnable : yield
		}
		Active --> Blocked
		Blocked --> Active
		Active --> Waiting
		Waiting --> Active
		Active --> TimedWaiting
		TimedWaiting --> Active
	  
	  Blocked --> Terminated
	  TimedWaiting --> Terminated
	  Waiting --> Terminated
    Active --> Terminated
    Terminated --> [*]

```