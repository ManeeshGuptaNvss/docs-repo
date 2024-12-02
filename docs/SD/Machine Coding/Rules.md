[Taken From / Reference](https://medium.com/@manav.jain.tube/7-common-mistakes-to-avoid-in-machine-coding-rounds-f8e09461b739)

A keen sense of design, focusing on extensibility, performance, and scalability. 

***Best Practice: Create a proper folder structure: one folder for models, another for the storage layer, and one for the business logic layer. This structure not only keeps your code clean and maintainable but also demonstrates to the interviewer that you have experience with professional coding practices.***

Don’t write entire code in a single file

***Solution: Store references (IDs) rather than entire objects to make your models lightweight and avoid unnecessary dependencies. Using UUIDs is an excellent practice, especially for distributed systems, as it helps with unique identification across platforms.***

A better approach is to separate business logic from data representation by introducing a dedicated service class.

***Correct Approach: Keep business logic in a separate service class, like `BookingService`, while model classes should handle only data. This separation of concerns makes the code more scalable, readable, and maintainable.***

There is no actual database integration required; all implementations should be done in-memory

Candidates often lack experience with implementing efficient in-memory storage. For instance, using a `List` to store user data is inefficient if you frequently need to look up users by ID, as it requires a full list traversal.

**Solution Code: Using a Map for Efficient Lookup. *Use a `Map` (or dictionary) for O(1) key-based lookups, which is much faster and more efficient for large datasets. This is a simple adjustment that can significantly improve your code's efficiency.***

To enhance flexibility, we can introduce an interface for the storage mechanism:

```java
// Define an interface for User storage
interface UserStorage {
    void save(User user);
    User findById(String userId);
}

// In-memory implementation of UserStorage
class InMemoryUserDao implements UserStorage {
    @Override
    public void save(User user) {
        // Logic to save user to in-memory storage
    }

    @Override
    public User findById(String userId) {
        return new User(userId, "John Doe"); // Sample user
    }
}

// UserService now uses UserStorage interface
class UserService {
    private UserStorage userStorage;

    public UserService(UserStorage userStorage) {
        this.userStorage = userStorage; // Loosely coupled
    }

    public void registerUser(User user) {
        userStorage.save(user);
    }

    public User getUser(String userId) {
        return userStorage.findById(userId);
    }
}
```

A red flag for interviewers is raw exceptions or minimal logging without proper error handling. Without a structured error-handling approach, your code becomes harder to debug, and there is no meaningful feedback to handle failures gracefully.

***Instead of throwing generic exceptions, create custom exceptions, like `UserNotFoundException`, that provide more detailed error context. Structured error handling also allows you to take corrective action, log the error meaningfully, or display an error message to the user if needed.***

Many candidates overlook the use of basic design patterns (e.g., Factory, Singleton, etc) and SOLID principles. These patterns and principles make the code reusable and maintainable, reducing redundancy and making it easier to scale. Following SOLID principles — Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, and Dependency Inversion — ensures you’re ready to handle complex systems effectively.

Not having a strong grasp of core programming concepts can also hurt your chances. Some essential concepts to brush up on include:

- *Shallow Copy vs. Deep Copy:* Understand the difference, as this affects object references and memory management.
- *String Mutability:* Knowing when and how to use immutable data structures like strings.
- *Locking Mechanisms:* Be aware of optimistic and pessimistic locking for handling concurrency in multi-threaded environments.