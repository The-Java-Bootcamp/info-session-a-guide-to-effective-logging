# Mastering Java's Logger Class: From println to Professional Logging

## Learning Objectives:

- Understand the limitations of `System.out.println` and the benefits of using Java's `Logger` class
- Learn how to set up and use Java's `Logger` class effectively
- Comprehend and apply different logging levels
- Implement basic file logging and exception handling

In the world of Java development, effective logging is a crucial skill that separates novice programmers from
professionals. While many beginners start with `System.out.println` for debugging, this approach quickly shows its
limitations in real-world applications. This info session will guide you from basic println debugging to professional
logging using Java's `Logger` class, a fundamental tool in every Java developer's toolkit.

### The Pitfalls of System.out.println

Let's begin by examining a simple example using `System.out.println`:

```java
public class PrintlnExample {
    public static void main(String[] args) {
        System.out.println("Application started");
        int result = 10 / 2;
        System.out.println("Result: " + result);
        System.out.println("Application ended");
    }
}
```

While this seems straightforward, it lacks several crucial features that become important as your applications grow in
complexity.

### Introducing Java's `Logger` Class

Enter Java's `Logger` class, which addresses these limitations and provides a robust logging framework.

Now, let's look at how we can improve this using Java's `Logger` class:

```java
import java.util.logging.Logger;
import java.util.logging.Level;

public class LoggerExample {
    private static final Logger LOGGER = Logger.getLogger(LoggerExample.class.getName());

    public static void main(String[] args) {
        LOGGER.info("Application started");
        int a = 10, b = 2;
        LOGGER.log(Level.FINE, "Dividing {0} by {1}", new Object[]{a, b});
        int result = a / b;
        LOGGER.log(Level.INFO, "Result: {0}", result);
        LOGGER.info("Application ended");
    }
}

```

### Understanding Logger Levels

Java's `Logger` class provides different levels of logging, allowing you to control the verbosity of your logs. The main
levels, in order of decreasing severity, are:

- `SEVERE`: For critical issues that may cause the application to fail
- `WARNING`: For potential problems that don't prevent the application from running
- `INFO`: For general information about the application's state
- `CONFIG`: For configuration-related messages
- `FINE, FINER, FINEST`: For increasingly detailed debugging information

By using these levels appropriately, you can easily filter logs based on their importance.

### Parameterized Logging

The line `LOGGER.log(Level.FINE, "Dividing {0} by {1}", new Object[]{a, b})` demonstrates parameterized logging.
The `new Object[]{a, b}` creates an array of objects that are inserted into the log message where the placeholders `{0}`
and `{1}` are. This approach is more efficient than string concatenation and provides a cleaner way to include variable
values in your log messages.

### Practical Example: Logging in a BankAccount Class

Let's expand on this with a more practical example:

```java
import java.util.logging.*;
import java.io.IOException;

public class BankAccount {
    private static final Logger LOGGER = Logger.getLogger(BankAccount.class.getName());
    private String accountNumber;
    private double balance;

    static {
        try {
            FileHandler fileHandler = new FileHandler("bank_operations.log", true);
            SimpleFormatter formatter = new SimpleFormatter();
            fileHandler.setFormatter(formatter);
            LOGGER.addHandler(fileHandler);
            LOGGER.setLevel(Level.ALL);
        } catch (IOException e) {
            LOGGER.log(Level.SEVERE, "Failed to set up file logging", e);
        }
    }

    public BankAccount(String accountNumber, double initialBalance) {
        this.accountNumber = accountNumber;
        this.balance = initialBalance;
        LOGGER.log(Level.INFO, "Created new account: {0} with initial balance: ${1}",
                new Object[]{accountNumber, initialBalance});
    }

    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
            LOGGER.log(Level.INFO, "Deposited ${0} into account {1}. New balance: ${2}",
                    new Object[]{amount, accountNumber, balance});
        } else {
            LOGGER.warning("Attempted to deposit invalid amount: $" + amount);
        }
    }

    public static void main(String[] args) {
        BankAccount account = new BankAccount("1234567890", 1000.0);
        account.deposit(500.0);
        account.deposit(-50.0);  // This should log a warning
    }
}

```

Let's break down this example and explain its key components:

**Static Logger Instance:**

`private static final Logger LOGGER = Logger.getLogger(BankAccount.class.getName());`

Here, we create a static logger instance for the `BankAccount` class. This ensures that all instances of `BankAccount`
share the same logger, providing consistent logging across the application.

**Static Initializer Block:**

```java
static {
    try {
        FileHandler fileHandler = new FileHandler("bank_operations.log", true);
        SimpleFormatter formatter = new SimpleFormatter();
        fileHandler.setFormatter(formatter);
        LOGGER.addHandler(fileHandler);
        LOGGER.setLevel(Level.ALL);
    } catch (IOException e) {
        LOGGER.log(Level.SEVERE, "Failed to set up file logging", e);
    }
}
```

This static block is executed when the `BankAccount` class is loaded, before any instances are created. It sets up file
logging:

- We create a FileHandler to write logs to `bank_operations.log`. The `true` parameter means we'll append to the file if
  it exists.
- We set a `SimpleFormatter` to format our log messages.
- We add the `FileHandler` to our logger and set the logging level to `ALL`, which means we'll capture all log messages.
- If setting up file logging fails, we log a `SEVERE` message. Note that this will go to the console since file logging
  isn't set up yet.

**Constructor:**

```java
public BankAccount(String accountNumber, double initialBalance) {
    this.accountNumber = accountNumber;
    this.balance = initialBalance;
    LOGGER.log(Level.INFO, "Created new account: {0} with initial balance: ${1}",
            new Object[]{accountNumber, initialBalance});
}
```

The constructor initializes a new account and logs an `INFO` message about the creation. We use parameterized logging
here for efficiency and clarity.

**Deposit Method:**

```java
public void deposit(double amount) {
    if (amount > 0) {
        balance += amount;
        LOGGER.log(Level.INFO, "Deposited ${0} into account {1}. New balance: ${2}",
                new Object[]{amount, accountNumber, balance});
    } else {
        LOGGER.warning("Attempted to deposit invalid amount: $" + amount);
    }
}
```

This method demonstrates using different log levels:

- For successful deposits, we log an `INFO` message with details of the transaction.
- For invalid deposits `(amount <= 0)`, we log a `WARNING` message.

**Main Method:**

```java
public static void main(String[] args) {
    BankAccount account = new BankAccount("1234567890", 1000.0);
    account.deposit(500.0);
    account.deposit(-50.0);  // This should log a warning
}
```

This method demonstrates creating an account and performing transactions, which will generate various log messages.

This `BankAccount` class showcases several important logging concepts:

- Setting up file logging for persistent log storage
- Using a static initializer to configure logging when the class is loaded
- Applying different log levels (`INFO`, `WARNING`) for various scenarios
- Using parameterized logging for efficient and readable log messages
- Including relevant context (account numbers, amounts) in log messages

By logging account creation and transactions, we create an audit trail that can be invaluable for debugging, monitoring
account activity, and potentially for regulatory compliance in a real banking application.

### Advanced Logging with SLF4J

Now that we've explored Java's built-in logging, let's take a step further and look at `SLF4J`, a popular logging facade
that allows you to easily switch between different logging frameworks.

To use `SLF4J`, you need to include the `SLF4J` API and a concrete logging implementation in your project. For example,
if you're using Maven, you might include these dependencies:

```xml

<dependencies>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>1.7.32</version>
    </dependency>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.6</version>
    </dependency>
</dependencies>
```

This setup uses `Logback` as the actual logging implementation. One of the great things about `SLF4J` is that you can
switch to a different logging framework just by changing the dependency, without modifying your logging code.

Let's rewrite our previous example using `SLF4J`:

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class SLF4JLoggerExample {
    private static final Logger LOGGER = LoggerFactory.getLogger(SLF4JLoggerExample.class);

    public static void main(String[] args) {
        LOGGER.info("Application started");

        int a = 10, b = 2;
        LOGGER.debug("Dividing {} by {}", a, b);
        try {
            int result = divide(a, b);
            LOGGER.info("Result: {}", result);
        } catch (ArithmeticException e) {
            LOGGER.error("An error occurred during division", e);
        }

        LOGGER.info("Application ended");
    }

    public static int divide(int a, int b) {
        return a / b;
    }
}
```

This `SLF4J` example demonstrates several key advantages:

- Simplified API: `SLF4J` provides a clean, easy-to-use API. Notice how we don't need to use `Level.INFO` or
  new `Object[]{}` anymore.
- Efficient Parameterized Logging: `SLF4J` supports parameterized logging out of the box, using `{}` as placeholders.
  This is not only more readable but also more efficient than string concatenation.
- Exception Logging: `SLF4J` makes it easy to log exceptions along with a message, as shown in the catch block.
- Flexibility: By using `SLF4J`, you can easily switch between different logging implementations (
  like `Logback`, `Log4j`, or `java.util.logging`) just by changing your dependency, without altering your code.
- Performance: `SLF4J` is designed to be performant, with minimal overhead when logging is disabled.

By adopting SLF4J, you're not only using a powerful and flexible logging solution, but you're also future-proofing your
code against potential changes in logging requirements or preferences.

### Best Practices and Common Pitfalls

- Use appropriate log levels
- Include relevant context in log messages
- Avoid logging sensitive information
- Be mindful of performance impact
- Configure logging appropriately for different environments (development, production)

### Challenges for Further Exploration

- Modify the `BankAccount` class to include a `withdraw` method, using appropriate log levels for successful withdrawals
  and insufficient funds scenarios.
- Implement a daily rolling file handler that creates a new log file each day.
- Create a method to change the log level dynamically, allowing for more detailed logging when needed for
  troubleshooting.

### Conclusion: The Importance of Proper Logging

As you continue your Java journey, remember that effective logging is not just about recording what happened – it's
about providing yourself and your team with the tools to understand, debug, and improve your applications. And always
remember: friends don't let friends use `System.out.println` for debugging!
This playful reminder encapsulates an important truth in Java development. While `System.out.println` might seem like a
quick and easy solution for debugging, it's akin to using a sledgehammer when you need a scalpel. Professional Java
developers understand that proper logging with the Logger class or facades like `SLF4J` offers numerous advantages:

- Granular control over log levels
- Easy configuration and redirection of log output
- Built-in support for timestamps and thread information
- Improved performance, especially in production environments
- Better organization and searchability of log messages
- Flexibility to switch between logging implementations

By adopting proper logging practices early in your career, you're not just writing better code – you're developing
better habits that will serve you well as you tackle more complex projects and collaborate with other developers. So the
next time you're tempted to sprinkle System.out.println statements throughout your code, remember this lesson and reach
for a proper logging solution instead. Your future self (and your teammates) will thank you!
Happy coding, and may your logs always be informative, efficient, and exactly as verbose as you need them to be!
