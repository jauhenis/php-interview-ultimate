# Ultimate PHP Interview Questions & Answers

This file contains a curated list of PHP interview questions and answers, merged from multiple sources and categorized by level (Junior, Middle, Senior).

## Table of Contents
1. [PHP Basics & Language Features](#1-php-basics--language-features)
2. [Object-Oriented Programming (OOP)](#2-object-oriented-programming-oop)
3. [Software Architecture & Principles](#3-software-architecture--principles)
4. [System Design Principles (Middle+)](#4-system-design-principles-middle)
5. [Design Patterns](#5-design-patterns)
6. [PHP 7/8+ New Features](#6-php-78-new-features)
7. [MySQL & Databases](#7-mysql--databases)
8. [PostgreSQL](#8-postgresql)
9. [Laravel & Symfony](#9-laravel--symfony)
10. [Tools & Composer](#10-tools--composer)
11. [Caching & Redis](#11-caching--redis)
12. [Kafka](#12-kafka)
13. [Infrastructure, Docker & DevOps](#13-infrastructure-docker--devops)
14. [Testing & Quality](#14-testing--quality)
15. [Security](#15-security)
16. [Web & API](#16-web--api)
17. [Highload & Scalability](#17-highload--scalability)
18. [Clean Code & Best Practices](#18-clean-code--best-practices)
19. [Elasticsearch](#19-elasticsearch)
20. [Tricky Questions](#20-tricky-questions)
21. [Laravel Plugins](#21-laravel-plugins)
22. [Long-Running (RoadRunner)](#22-long-running-roadrunner)
23. [PSR & PER Standards](#23-psr--per-standards)
24. [Basic Algorithms](#24-basic-algorithms)
25. [HaPHPiness - Best Things in PHP](#25-haphpiness---best-things-in-php)
26. [LeetCode Solutions](#26-leetcode-solutions)
27. [Regexp](#27-regexp)
---

## 1. PHP Basics & Language Features

## Floating point numbers (float)
- https://www.php.net/manual/en/language.types.float.php

### Why are floating-point numbers often inaccurate in PHP?
**Answer:** Floating-point numbers have limited precision and are stored in binary format (IEEE 754). This means that some decimal fractions (like 0.1 or 0.2) cannot be represented exactly.
[Detailed Floating Point Explained](answers/floating_point_numbers.md)

### Junior
#### What does PHP stand for and what is its main purpose?
**Answer:** PHP originally stood for "Personal Home Page," but it now stands for "PHP: Hypertext Preprocessor." It is a server-side scripting language designed for web development. Its main purpose is to generate dynamic web content, handle form data, interact with databases, manage sessions, and more.

#### Is PHP a case-sensitive language?
**Answer:** Yes, PHP is case-sensitive for variable names. However, function names, class names, and built-in constructs (like `echo`, `if`, etc.) are case-insensitive.

#### What are the rules for naming a PHP variable?
**Answer:** 
- Must start with a `$` sign.
- Followed by a letter or underscore.
- Can only contain letters, numbers, and underscores (A-z, 0-9, and _).
- Cannot start with a number.
- Variable names are case-sensitive.

#### What are the data types in PHP?
**Answer:** 
- **Scalar:** `int`, `float`, `string`, `bool`.
- **Compound:** `array`, `object`, `callable`, `iterable`.
- **Special:** `resource`, `null`.
[Detailed Data Types Guide](answers/data_types.md)

#### What is the difference between `empty()`, `is_null()`, and `isset()`?
**Answer:** 
- `empty()`: Returns true if the variable is an empty string, 0, false, null, or an empty array.
- `is_null()`: Returns true only if the variable is null.
- `isset()`: Returns true if the variable is set and is not null.

#### What is the difference between `echo` and `print`?
**Answer:** 
- `echo` is a language construct, while `print` is a function (though it behaves like a construct).
- `echo` is slightly faster and can take multiple parameters.
- `print` always returns 1, so it can be used in expressions.

#### What are `include()`, `require()`, `include_once()`, and `require_once()`?
**Answer:** 
- `include()`: Includes a file. If it fails, it emits a warning and continues.
- `require()`: Includes a file. If it fails, it emits a fatal error and stops execution.
- `*_once()`: Similar to their counterparts, but ensure the file is only included once.

#### What are the different types of arrays in PHP?
**Answer:** 
- **Indexed arrays:** Arrays with a numeric index.
- **Associative arrays:** Arrays with named keys.
- **Multidimensional arrays:** Arrays containing one or more arrays.

#### What is the difference between single-quoted and double-quoted strings?
**Answer:** 
- **Single-quoted:** Literals. Variables and most escape sequences are not interpreted.
- **Double-quoted:** Variables are interpolated, and escape sequences (like `\n`, `\t`) are interpreted.

#### What is the difference between `$message` and `$$message`?
**Answer:** `$message` is a regular variable. `$$message` is a "variable variable," which uses the value of `$message` as the name of another variable.

#### What is meant by "escaping to PHP"?
**Answer:** It refers to embedding PHP code within HTML using tags like `<?php ... ?>`.

#### What are the rules to determine the "truth" of any value (truthiness)?
**Answer:** In PHP, values are "truthy" unless they are: `null`, `false`, `0`, `0.0`, `""` (empty string), `"0"`, or `[]` (empty array).

#### What is the difference between constants and variables?
**Answer:** Constants are defined using `define()` or `const` and cannot be changed or undefined once set. Variables can change their value at any time.

#### Name some built-in constants in PHP.
**Answer:** `PHP_VERSION`, `PHP_OS`, `DEFAULT_INCLUDE_PATH`, `DIRECTORY_SEPARATOR`, `E_ERROR`, `E_WARNING`.

#### What is the use of `explode()` and `implode()`?
**Answer:** 
- `explode()`: Splits a string into an array by a delimiter.
- `implode()`: Joins array elements into a string using a delimiter.

#### What is the difference between `unset()` and `unlink()`?
**Answer:** 
- `unset()`: Destroys a variable or an element in an array.
- `unlink()`: Deletes a file from the server.

### Middle
#### What are Magic Constants in PHP?
**Answer:** Magic constants are predefined constants that change depending on where they are used.
- `__LINE__`, `__FILE__`, `__DIR__`, `__FUNCTION__`, `__CLASS__`, `__TRAIT__`, `__METHOD__`, `__NAMESPACE__`, `ClassName::class`.

#### What are Generators?
**Answer:** Generators provide an easy way to implement simple iterators using the `yield` keyword, allowing you to iterate through data without building an array in memory.
[Generators and yield](answers/generators.md)

#### What is the difference between a closure and an anonymous function?
**Answer:** An anonymous function is a function without a name. A closure is an anonymous function that can "close over" variables from its parent scope using the `use` keyword.

#### What is Type Hinting?
**Answer:** Type hinting allows you to specify the expected data type for function arguments and return values. This helps catch errors and improves code readability.

#### What is Garbage Collection in PHP?
**Answer:** 
PHP uses a reference counting mechanism and a cyclic garbage collector to automatically free memory occupied by objects and variables that are no longer reachable.

- **Reference Counting:** In PHP 7+, the `refcount` is moved from the `zval` itself into the complex value structures (`zend_string`, `zend_array`, etc.). Simple types (like `int` or `bool`) are not reference-counted. When a `refcount` reaches zero, the memory is freed.
- **What is a "Cycle"?** A cycle occurs when objects point to each other (circular references), preventing their `refcounts` from reaching zero even after being unset.
- **How it cleans:** When the "root buffer" reaches its limit, the collector temporarily decrements internal refcounts. If a refcount hits zero, the object is marked as garbage and swept.
- **Performance:** GC prevents memory leaks (crucial for daemons/workers) but adds CPU overhead during collection cycles.

[Detailed Garbage Collection Guide](answers/garbage_collector.md)

#### What is a zval and what is its basic structure? ⭐ **Important**
**Answer:** A `zval` (Zend value) is the fundamental data structure used by the Zend Engine to represent any PHP variable. In PHP 7+, it was significantly optimized to reduce memory usage (down to 16 bytes) and avoid unnecessary heap allocations.

**Internal Structure (PHP 7+):**
1. **value**: An 8-byte union storing the actual data (e.g., `lval` for integers, `dval` for doubles, or pointers like `*str`, `*arr`, `*obj`).
2. **u1 (type_info)**: A 4-byte union containing the **type tag** (e.g., `IS_STRING`, `IS_LONG`) and **type flags** (indicating if the value is reference-counted or participates in garbage collection).
3. **u2**: A 4-byte union used for auxiliary data (e.g., hash collision chains in arrays).

**Key Differences from PHP 5:**
- **Size**: Reduced from 24/32 bytes to 16 bytes.
- **Reference Counting**: Moved from the `zval` itself into the complex value structures (`zend_string`, `zend_array`, etc.).
- **Simple Types**: Types like `null`, `bool`, `int`, and `float` are stored directly in the `zval` and are no longer reference-counted or heap-allocated.

[Detailed Zval Structure Guide](https://www.phpinternalsbook.com/php7/zvals/basic_structure.html)


#### What is copy-on-write in PHP, and when does passing by value duplicate data? ⭐ **Important**
**Answer:** **Copy-on-write (CoW)** is an optimization where PHP shares a single `zval` (memory storage) between multiple variables until one is modified.
- **Read-only**: Passing a large array by value only shares the `zval` (no memory duplication).
- **Modification**: Mutating the array triggers a "separation," creating a real copy in memory.
- **Objects**: Pass by **handle**, so property changes are visible outside without the array-style CoW duplication.
Do not use `&` purely for memory optimization; PHP's CoW is already efficient for read-heavy access.
[Detailed Copy-on-Write Guide](answers/copy_on_write.md)

#### What is OPCache?
**Answer:** OPCache is a caching engine for PHP that stores precompiled script bytecode in shared memory, eliminating the need for PHP to load and parse scripts on each request.

#### How does Error Handling work in PHP?
**Answer:** PHP uses several mechanisms:
- **Error Reporting:** Configured via `error_reporting()` and `display_errors` in `php.ini`.
- **Try-Catch Blocks:** For handling `Exceptions` and `Errors` (since PHP 7).
- **Custom Error Handlers:** Using `set_error_handler()` and `set_exception_handler()`.

### Senior
#### What are the risks of using PHP as a long-lived process (daemon)?
**Answer:** Memory leaks and global state contamination are the biggest risks, as variables and objects persist between requests.
[Running PHP as a Daemon](answers/php_advanced_extras/#php-as-a-daemon-swoole-roadrunner)

#### How does the Opcache work?
**Answer:** OpCache improves performance by storing precompiled bytecode in shared memory, avoiding the need for PHP to load, parse, and compile scripts on every request.

#### How does the opcache work since 8.0 and new JIT?
**Answer:** OpCache with JIT (since 8.0) identifies "hot" code and compiles it into native machine code, allowing the CPU to execute it directly, bypassing the Zend VM interpreter loop.

#### What do you know about JIT?
**Answer:** JIT (Just-In-Time) is highly effective for CPU-intensive tasks but typically offers minimal benefits for standard I/O-bound web applications. It allows PHP to be used for more than just web development (e.g., machine learning, image processing).
[Detailed OpCache and JIT Guide](answers/opcache_jit.md)

#### Why does `var_dump(0.1 + 0.2 == 0.3)` return `bool(false)`?
**Answer:**
This occurs because floating-point numbers in PHP (and most other languages) follow the **IEEE 754** standard, which represents them in binary. Many decimal fractions, such as `0.1` and `0.2`, cannot be represented exactly as finite binary fractions.
- When you add `0.1 + 0.2`, the internal result is slightly different from exactly `0.3` (it's approximately `0.30000000000000004`).
- **Comparison:** Never use `==` to compare floats. Instead, use a small epsilon: `abs(($a + $b) - $c) < 0.00001`.

**Database and Financial precision:**
- In databases, you must store these values as **`DECIMAL`** (or `NUMERIC`) instead of `float` or `double`.
- **Reason:** `DECIMAL` stores numbers as fixed-point values (or strings internally), ensuring that calculations remain exact, which is critical for monetary values. Floating-point types are for approximate scientific values where speed is more important than absolute precision.
[Detailed Data Types Guide](answers/data_types.md)

---

## 2. Object-Oriented Programming (OOP)

### Junior
#### What is a Class and an Object?
**Answer:** 
- **Class:** A blueprint or template for creating objects. It defines properties (attributes) and methods (behaviors).
- **Object:** An instance of a class. It is a concrete realization of the blueprint with its own state.

#### What are the main characteristics of OOP?
**Answer:** 
- **Encapsulation:** Bundling data and methods that operate on that data within a single unit (class) and restricting direct access.
- **Inheritance:** A mechanism where a subclass inherits properties and behaviors from a superclass.
- **Polymorphism:** The ability of different classes to be treated as instances of the same interface or parent class.
- **Abstraction:** Hiding complex implementation details and showing only the essential features of the object.

#### What are access modifiers in PHP?
**Answer:** 
- `public`: Accessible from anywhere.
- `protected`: Accessible within the class itself and by inheriting classes.
- `private`: Accessible only within the class that defines it.

#### What are constructors and destructors?
**Answer:** 
- `__construct()`: A special method called automatically when an object is instantiated.
- `__destruct()`: A special method called when an object is destroyed or the script ends.

### Middle
#### What is the difference between `this`, `self`, and `static`?
**Answer:** 
- `$this`: Refers to the current object instance.
- `self`: Refers to the current class. Used for static properties/methods.
- `static`: Refers to the called class (Late Static Binding).
[this vs self vs static](answers/this_self_static.md)

#### What is the difference between an Interface and an Abstract Class?
**Answer:** 
- **Interface:** Defines a contract. Can only have public methods and no implementation. A class can implement multiple interfaces.
- **Abstract Class:** Can have both abstract methods and concrete methods with implementation. A class can only extend one abstract class.
[Interface vs Abstract Class Comparison](answers/interface_vs_abstract.md)

#### What are Traits?
**Answer:** Traits are a mechanism for code reuse in single inheritance languages like PHP. They allow you to include sets of methods in several independent classes.

#### What is Method Overriding?
**Answer:** Method overriding occurs when a subclass provides a specific implementation for a method that is already defined in its parent class.

#### What are the different types of inheritance?
**Answer:** 
- **Single:** A class inherits from one parent class.
- **Multilevel:** A class inherits from a parent that is itself a subclass.
- **Hierarchical:** Multiple subclasses inherit from a single parent class.
- **Hybrid:** A combination of two or more of the above (using traits/interfaces).
*Note: PHP does not support Multiple Inheritance directly.*

#### What are Namespaces in PHP?
**Answer:** Namespaces provide a way in which to group related classes, functions and constants to avoid naming collisions.

#### What is the difference between Abstraction and Encapsulation?
**Answer:** 
- **Abstraction:** Hiding complex implementation details and showing only the essential features of the object (Focus on "What").
- **Encapsulation:** Bundling data and methods together and restricting direct access to some of the object's components (Focus on "How").

#### What are Magic Methods?
**Answer:** Special methods that start with `__` and are triggered by specific events (e.g., `__get`, `__set`, `__call`, `__toString`, `__sleep`, `__wakeup`).


### Senior
#### What is Method Overloading in PHP?
**Answer:** PHP does not support traditional method overloading (same name, different arguments). Instead, it uses magic methods like `__call()` to simulate it.
[Method Overloading in PHP](answers/method_overloading.md)

---

## 3. Software Architecture & Principles

### Middle
#### What are SOLID Principles?
**Answer:** 
- **S:** Single Responsibility
- **O:** Open/Closed
- **L:** Liskov Substitution
- **I:** Interface Segregation
- **D:** Dependency Inversion
[SOLID Principles Guide](answers/solid_principles.md)

#### What is CQRS?
**Answer:** Command Query Responsibility Segregation. A pattern that separates reading data (Query) from writing/updating data (Command).

#### What is the difference between an Entity and a Value Object in DDD?
**Answer:** 
- **Entity:** An object with a unique identity (ID) that remains constant even if its properties change (e.g., a User).
- **Value Object:** An object defined solely by its attributes; two instances with the same values are identical. They are typically immutable (e.g., Money, Color, Address).

#### What is the main difference between Hexagonal and Onion architecture?
**Answer:** 
- **Hexagonal (Ports & Adapters):** Focuses on separating the application from external concerns using Ports (interfaces) and Adapters (implementations). It doesn't prescribe internal layering.
- **Onion Architecture:** Uses concentric layers where dependencies always point inward. The Domain Model is at the core, surrounded by Domain Services, Application Services, and finally Infrastructure/UI.
[Architecture Deep Dive](answers/architecture_advanced.md) | [Practical Examples](answers/architecture_examples.md)

### Senior
#### What are Anemic and Rich models?
**Answer:** 
- **Anemic Model:** Models that only contain data (getters/setters) but no business logic.
- **Rich Model:** Models that encapsulate both data and business logic (Domain-Driven Design).
[Anemic vs Rich Model Deep Dive](answers/architecture_advanced.md)

#### What is GRASP?
**Answer:** General Responsibility Assignment Software Patterns. A set of principles for assigning responsibilities to classes and objects.
[GRASP Patterns Guide](answers/architecture_advanced.md)

#### What are the pros and cons of Microservices?
**Answer:** 
- **Pros:** Independent scaling, speed and agility, technology independence, and fault isolation.
- **Cons:** Management complexity, data consistency issues (distributed transactions), network latency, and complex monitoring.
[Microservices Architecture](answers/architecture_highload.md)

---

## 4. System Design Principles (Middle+)

### Middle

#### What is System Design and why is it important?
**Answer:** System design is the process of defining the architecture, components, modules, interfaces, and data for a system to satisfy specified requirements. It is crucial because a well-designed system can scale efficiently, handle failures gracefully, and be maintained cost-effectively. Poor design can lead to massive financial losses and system-wide outages.

#### What is the difference between Functional and Non-Functional requirements?
**Answer:** 
- **Functional Requirements:** Define *what* the system should do. These are specific features like "a user can book a scooter" or "a user can see their ride history."
- **Non-Functional Requirements:** Define *how* the system should perform. These include qualities like scalability (handling 100k requests), availability (99.9% uptime), reliability, and security.

#### Explain Vertical vs. Horizontal Scaling.
**Answer:** 
- **Vertical Scaling (Scaling Up):** Increasing the capacity of a single server (e.g., adding more CPU, RAM, or faster SSDs). It is simple but limited by the physical capacity of a single machine and becomes very expensive at the high end.
- **Horizontal Scaling (Scaling Out):** Adding more servers to the pool and distributing the load across them using a Load Balancer. It allows for virtually unlimited growth and provides better fault tolerance, as the system can continue to work if one node fails.

#### What are the main strategies for scaling a database?
**Answer:** 
- **Replication:** Creating copies of the database (Master/Slave). Usually, the Master handles writes, while Slaves handle read queries to distribute the load.
- **Sharding:** Distributing data across multiple independent database servers (shards) based on a specific key (e.g., User ID, Geolocation). Each shard holds a subset of the total data.
- **Partitioning:** Splitting a large table into smaller, more manageable logical pieces within a single database instance to improve query performance.

#### What is a Sharding Key and how do you choose a good one?
**Answer:** A sharding key is the attribute used to determine which shard a specific piece of data belongs to. A good sharding key ensures:
- **Even Distribution:** Data is spread uniformly across shards, avoiding "hot spots."
- **Query Efficiency:** It minimizes the need for "cross-shard" queries by keeping related data together.
Common keys include User ID, Tenant ID, or a Hash of a unique attribute.

#### What are the best practices for designing a REST API?
**Answer:** Use plural nouns for resources (e.g., /users), standard HTTP methods (GET, POST, PUT, DELETE), versioning (e.g., /v1/...), and proper HTTP status codes to indicate the outcome of the request.

#### What is the relationship between Latency, Throughput, and Concurrency?
**Answer:** They are related by **Little's Law**: `Concurrency = Throughput × Latency`.
- **Latency:** The time it takes to process a single request.
- **Throughput:** The number of requests the system can handle per unit of time.
- **Concurrency:** The number of requests being processed simultaneously.
If you increase the number of parallel requests (concurrency) without increasing the system's throughput, the latency will automatically increase, leading to a bottleneck.

#### What is a Load Balancer and what are the common balancing algorithms?
**Answer:** A Load Balancer (e.g., Nginx, HAProxy, AWS ELB) is a component that distributes incoming network traffic across multiple servers to ensure no single server becomes overwhelmed.
Common algorithms include:
- **Round Robin:** Requests are distributed sequentially.
- **Least Connections:** Sends traffic to the server with the fewest active connections.
- **IP Hash:** The client's IP address determines which server receives the request (useful for session persistence).
- **Latency-Aware:** The balancer tracks historical response times and avoids "slow" nodes.

#### What are Circuit Breaker and Back Pressure patterns?
**Answer:** 
- **Circuit Breaker:** Monitors calls to a failing service. If the error rate exceeds a threshold, the "circuit opens," and the system stops calling the service, returning a fast fallback error instead of waiting for timeouts. This prevents cascading failures.
- **Back Pressure:** A mechanism where a system "pushes back" against a high rate of incoming requests that it cannot process. Instead of letting queues grow indefinitely (which increases latency), it rejects new requests (e.g., HTTP 429) or asks the client to slow down.

#### What is Idempotency in API design and why is it important?
**Answer:** An idempotent operation is one that has the same effect regardless of how many times it is executed. For example, `PUT` and `DELETE` should be idempotent, while `POST` is typically not.
In distributed systems, idempotency is critical for handling network failures. If a client doesn't receive a confirmation due to a timeout, it might retry the request. If the API is idempotent, the retry won't cause unintended side effects (like charging a customer twice).

### Senior

#### How can you reduce Tail Latency in a distributed system?
**Answer:** Tail latency (P99+) can be reduced using several techniques:
- **Hedged Requests (Tied Requests):** Sending the same request to multiple replicas and using the result from the first one that responds.
- **Request Coalescing:** Combining identical concurrent requests into a single backend call.
- **Background Jittering:** Adding random delays to scheduled tasks to prevent "thundering herd" problems.
- **Deadline Propagation:** Passing the maximum allowed time for a request to all sub-services so they can abort early if the deadline is already passed.
- **Latency-Aware Balancing:** Avoiding nodes that are currently performing poorly.

#### Compare different Sharding routing strategies: Application-level, Proxy, and Coordinator.
**Answer:** 
- **Application-Level Routing:** The application code contains the logic to determine which shard to use. It's fast (no extra network hop) but hard to maintain as the sharding logic is duplicated across services.
- **Proxy-Based Routing:** A separate layer (e.g., Vitess, ProxySQL) sits between the app and the database. It handles routing, making sharding transparent to the app. It adds a small network hop but centralizes management.
- **Coordinator-Based:** A separate service (like Zookeeper or a custom metadata service) keeps track of shard locations. Clients query the coordinator to find the correct shard. It's robust but complex to implement.

#### Compare 2PC, 3PC, TCC, and the Saga Pattern.
**Answer:** 
- **2PC (Two-Phase Commit):** A synchronous protocol for distributed transactions. It ensures strict consistency but is blocking and can be a performance bottleneck.
- **3PC (Three-Phase Commit):** An extension of 2PC that adds a "Pre-commit" phase to reduce blocking in case of coordinator failure. It is rarely used in production.
- **TCC (Try-Confirm-Cancel):** An application-level pattern. You reserve resources (Try), and then either finalize (Confirm) or release (Cancel). It is flexible but complex to implement.
- **Saga Pattern:** A sequence of local transactions where each step has a corresponding "compensating transaction" to undo changes if a failure occurs. It is highly scalable and common in microservices but offers only eventual consistency.

#### Explain the difference between Eventual and Strong Consistency.
**Answer:** 
- **Eventual Consistency:** Replicas will eventually converge to the same value, but there might be a delay where different users see different data. It provides high availability and low latency. Suitable for comments, likes, or product catalogs.
- **Strong Consistency:** Guarantees that once a write is successful, any subsequent read will return the new value. It is essential for financial transactions, inventory management, or booking systems.
- **Linearizability:** A stronger form of consistency where operations appear to happen instantaneously and in the order they were issued.

#### What is Tail Latency and why does it matter in distributed systems?
**Answer:** Tail latency refers to the high latency experienced by a small percentage of requests (e.g., the 99.9th percentile). In a large-scale system where one user request might trigger hundreds of sub-requests to various microservices, a high tail latency in even one service can slow down the entire response for the user, making it a critical metric for system reliability.
[System Design Principles Deep Dive](answers/system_design_principles.md)

---

## 5. Design Patterns
Design patterns are strictly related to [Clean Code & Best Practices](#18-clean-code--best-practices).

### Junior
#### What are the main categories of Design Patterns?
**Answer:** Design patterns are generally categorized into three groups:
- **Creational Patterns:** Focus on object creation mechanisms.
- **Structural Patterns:** Focus on how classes and objects are composed to form larger structures.
- **Behavioral Patterns:** Focus on communication between objects.

#### What is the Singleton Pattern?
**Answer:** A pattern that ensures a class has only one instance and provides a global point of access to it. It is a Creational Pattern.

### Middle
#### What is the Factory Method Pattern?
**Answer:** Factory Method is a creational design pattern that provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created.

**Common Usages / Pros & Cons:**
- **Pros:** You avoid tight coupling between the creator and the concrete products. Single Responsibility Principle. You can move the product creation code into one place in the program, making the code easier to support. Open/Closed Principle. You can introduce new types of products into the program without breaking existing client code.
- **Cons:** The code may become more complicated since you need to introduce a lot of new subclasses to implement the pattern. The best-case scenario is when you’re introducing the pattern into an existing hierarchy of creator classes.

#### What is the Abstract Factory Pattern?
**Answer:** Abstract Factory is a creational design pattern that lets you produce families of related objects without specifying their concrete classes.

**Common Usages / Pros & Cons:**
- **Pros:** You can be sure that the products you’re getting from a factory are compatible with each other. You avoid tight coupling between concrete products and client code. Single Responsibility Principle. You can extract the product creation code into one place, making the code easier to support. Open/Closed Principle. You can introduce new variants of products without breaking existing client code.
- **Cons:** The code may become more complicated than it should be, since a lot of new interfaces and classes are introduced along with the pattern.

#### What is the Builder Pattern?
**Answer:** Builder is a creational design pattern that lets you construct complex objects step by step. The pattern allows you to produce different types and representations of an object using the same construction code.

**Common Usages / Pros & Cons:**
- **Pros:** You can construct objects step-by-step, defer construction steps or run steps recursively. You can reuse the same construction code when building various representations of products. Single Responsibility Principle. You can isolate complex construction code from the business logic of the product.
- **Cons:** The overall complexity of the code increases since the pattern requires creating multiple new classes.

#### What is the Prototype Pattern?
**Answer:** Prototype is a creational design pattern that lets you copy existing objects without making your code dependent on their classes.

**Common Usages / Pros & Cons:**
- **Pros:** You can clone objects without coupling to their concrete classes. You can get rid of repeated initialization code in favor of cloning pre-configured prototypes. You can produce complex objects more conveniently. You get an alternative to inheritance when dealing with configuration presets for complex objects.
- **Cons:** Cloning complex objects that have circular references might be very tricky.

#### What is the Adapter Pattern?
**Answer:** Adapter is a structural design pattern that allows objects with incompatible interfaces to collaborate.

**Common Usages / Pros & Cons:**
- **Pros:** Single Responsibility Principle. You can separate the interface or data conversion code from the primary business logic of the program. Open/Closed Principle. You can introduce new types of adapters into the program without breaking the existing client code, as long as they work with the adapters through the client interface.
- **Cons:** The overall complexity of the code increases because you need to introduce a set of new interfaces and classes. Sometimes it’s simpler just to change the service class so that it fits with the rest of your code.

#### What is the Bridge Pattern?
**Answer:** Bridge is a structural design pattern that lets you split a large class or a set of closely related classes into two separate hierarchies—abstraction and implementation—which can be developed independently of each other.

**Common Usages / Pros & Cons:**
- **Pros:** You can create platform-independent classes and apps. The client code works with high-level abstractions. It isn’t exposed to the platform details. Open/Closed Principle. You can introduce new abstractions and implementations independently from each other. Single Responsibility Principle. You can focus on high-level logic in the abstraction and on platform details in the implementation.
- **Cons:** You might make the code more complicated by applying the pattern to a highly cohesive class.

#### What is the Composite Pattern?
**Answer:** Composite is a structural design pattern that lets you compose objects into tree structures and then work with these structures as if they were individual objects.

**Common Usages / Pros & Cons:**
- **Pros:** You can work with complex tree structures more conveniently: use polymorphism and recursion to your advantage. Open/Closed Principle. You can introduce new element types into the app without breaking the existing code, which now works with the object tree.
- **Cons:** It might be difficult to provide a common interface for classes whose functionality differs too much. In certain scenarios, you’d need to overgeneralize the component interface, making it harder to comprehend.

#### What is the Decorator Pattern?
**Answer:** Decorator is a structural design pattern that lets you attach new behaviors to objects by placing these objects inside special wrapper objects that contain the behaviors.

**Common Usages / Pros & Cons:**
- **Pros:** You can extend an object’s behavior without making a new subclass. You can add or remove responsibilities from an object at runtime. You can combine several behaviors by wrapping an object into multiple decorators. Single Responsibility Principle. You can divide a monolithic class that implements many possible variants of behavior into several smaller classes.
- **Cons:** It’s hard to remove a specific wrapper from the wrappers stack. It’s hard to implement a decorator in such a way that its behavior doesn’t depend on the order in the decorators stack. The initial configuration code of layers might look pretty ugly.

#### What is the Facade Pattern?
**Answer:** Facade is a structural design pattern that provides a simplified interface to a library, a framework, or any other complex set of classes.

**Common Usages / Pros & Cons:**
- **Pros:** You can isolate your code from the complexity of a subsystem.
- **Cons:** A facade can become a god object coupled to all classes of an app.

#### What is the Flyweight Pattern?
**Answer:** Flyweight is a structural design pattern that lets you fit more objects into the available amount of RAM by sharing common parts of state between multiple objects instead of keeping all of the data in each object.

**Common Usages / Pros & Cons:**
- **Pros:** You can save lots of RAM, assuming your program has tons of similar objects.
- **Cons:** You might be exchanging RAM for CPU cycles when part of the state data needs to be recalculated every time someone calls a flyweight method. The code becomes much more complicated. New team members will always be wondering why the state of an entity was separated in such a way.

#### What is the Proxy Pattern?
**Answer:** Proxy is a structural design pattern that lets you provide a substitute or placeholder for another object. A proxy controls access to the original object, allowing you to perform something either before or after the request gets through to the original object.

**Common Usages / Pros & Cons:**
- **Pros:** You can control the service object without clients knowing about it. You can manage the lifecycle of the service object when clients don’t care about it. The proxy works even if the service object isn’t ready or is not available. Open/Closed Principle. You can introduce new proxies without changing the service or clients.
- **Cons:** The code may become more complicated since you need to introduce a lot of new classes. The response from the service might get delayed.

#### What is the Chain of Responsibility Pattern?
**Answer:** Chain of Responsibility is a behavioral design pattern that lets you pass requests along a chain of handlers. Upon receiving a request, each handler decides either to process the request or to pass it to the next handler in the chain.

**Common Usages / Pros & Cons:**
- **Pros:** You can control the order of request handling. Single Responsibility Principle. You can decouple classes that invoke operations from classes that perform operations. Open/Closed Principle. You can introduce new handlers into the app without breaking the existing client code.
- **Cons:** Some requests may end up unhandled.

#### What is the Command Pattern?
**Answer:** Command is a behavioral design pattern that turns a request into a stand-alone object that contains all information about the request. This transformation lets you pass requests as a method arguments, delay or queue a request’s execution, and support undoable operations.

**Common Usages / Pros & Cons:**
- **Pros:** Single Responsibility Principle. You can decouple classes that invoke operations from classes that perform these operations. Open/Closed Principle. You can introduce new commands into the app without breaking existing client code. You can implement undo/redo. You can implement deferred execution of operations. You can assemble a set of simple commands into a complex one.
- **Cons:** The code may become more complicated since you’re introducing a whole new layer between senders and receivers.

#### What is the Mediator Pattern?
**Answer:** Mediator is a behavioral design pattern that lets you reduce chaotic dependencies between objects. The pattern restricts direct communications between the objects and forces them to collaborate only via a mediator object.

**Common Usages / Pros & Cons:**
- **Pros:** Single Responsibility Principle. You can extract the communications between various components into a single place, making it easier to comprehend and maintain. Open/Closed Principle. You can introduce new mediators without having to change the actual components. You can reduce coupling between various components of a program. You can reuse individual components more easily.
- **Cons:** Over time a mediator can evolve into a God Object.

#### What is the Iterator Pattern?
**Answer:** Iterator is a behavioral design pattern that lets you traverse elements of a collection without exposing its underlying representation (list, stack, tree, etc.).

**Common Usages / Pros & Cons:**
- **Pros:** Single Responsibility Principle. You can clean up the client code and the collections by extracting bulky traversal algorithms into separate classes. Open/Closed Principle. You can implement new types of collections and iterators and pass them to existing code without breaking anything. You can iterate over the same collection in parallel because each iterator object contains its own traversal state. For the same reason, you can delay a traversal and continue it when needed.
- **Cons:** Applying the pattern can be an overkill if your app only works with simple collections. Using an iterator may be less efficient than going through elements of some specialized collections directly.

#### What is the Memento Pattern?
**Answer:** Memento is a behavioral design pattern that lets you save and restore the previous state of an object without revealing the details of its implementation.

**Common Usages / Pros & Cons:**
- **Pros:** You can produce snapshots of the object’s state without violating its encapsulation. You can simplify the originator’s code by letting the caretaker maintain the history of the originator’s state.
- **Cons:** The app might consume lots of RAM if clients create mementos too often. Caretakers should track the originator’s lifecycle to be able to destroy obsolete mementos. Most dynamic programming languages, such as PHP, Python and JavaScript, can’t guarantee that the state within the memento stays untouched.

#### What is the Observer Pattern?
**Answer:** Observer is a behavioral design pattern that lets you define a subscription mechanism to notify multiple objects about any events that happen to the object they’re observing.

**Common Usages / Pros & Cons:**
- **Pros:** Open/Closed Principle. You can introduce new subscriber classes without having to change the publisher’s code (and vice versa if there’s a publisher interface). You can establish relations between objects at runtime.
- **Cons:** Subscribers are notified in random order.

**SplObserver and SplSubject in SPL (Standard PHP Library), and a link https://www.php.net/manual/en/class.splobserver.php**

#### What is the State Pattern?
**Answer:** State is a behavioral design pattern that lets an object alter its behavior when its internal state changes. It appears as if the object changed its class.

**Common Usages / Pros & Cons:**
- **Pros:** Single Responsibility Principle. Organize the code related to particular states into separate classes. Open/Closed Principle. Introduce new states without changing existing state classes or the context. Simplify the code of the context by eliminating bulky state machine conditionals.
- **Cons:** Applying the pattern can be overkill if a state machine has only a few states or rarely changes.

#### What is the Strategy Pattern?
**Answer:** Strategy is a behavioral design pattern that lets you define a family of algorithms, put each of them into a separate class, and make their objects interchangeable.

**Common Usages / Pros & Cons:**
- **Pros:** You can swap algorithms used inside an object at runtime. You can isolate the implementation details of an algorithm from the code that uses it. You can replace inheritance with composition. Open/Closed Principle. You can introduce new strategies without having to change the context.
- **Cons:** If you only have a couple of algorithms and they rarely change, there’s no real reason to overcomplicate the program with new classes and interfaces that come along with the pattern. Clients must be aware of the differences between strategies to be able to select a proper one. A lot of modern programming languages have functional type support that lets you implement different versions of an algorithm inside a set of anonymous functions. Then you could use these functions exactly as you’d have used the strategy objects, but without bloating your code with extra classes and interfaces.

#### What is the Template Method Pattern?
**Answer:** Template Method is a behavioral design pattern that defines the skeleton of an algorithm in the superclass but lets subclasses override specific steps of the algorithm without changing its structure.

**Common Usages / Pros & Cons:**
- **Pros:** You can let clients override only certain parts of a large algorithm, making them less affected by changes that happen to other parts of the algorithm. You can pull the duplicate code into a superclass.
- **Cons:** Some clients may be limited by the provided skeleton of an algorithm. You might violate the Liskov Substitution Principle by suppressing a default step implementation via a subclass. Template methods tend to be harder to maintain the more steps they have.

#### What is the Visitor Pattern?
**Answer:** Visitor is a behavioral design pattern that lets you separate algorithms from the objects on which they operate.

**Common Usages / Pros & Cons:**
- **Pros:** Open/Closed Principle. You can introduce a new behavior that can work with objects of different classes without changing these classes. Single Responsibility Principle. You can move multiple versions of the same behavior into the same class. A visitor object can accumulate some useful information while working with various objects. This might be handy when you want to traverse some complex object structure, such as an object tree, and apply the visitor to these objects.
- **Cons:** You need to update all visitors each time a class gets added to or removed from the element hierarchy. Visitors might lack the necessary access to the private fields and methods of the elements that they’re supposed to work with.

### Senior
#### What is Dependency Injection?
**Answer:** A design pattern where an object receives its dependencies from the outside rather than creating them itself. This promotes loose coupling and easier testing.

#### What is the difference between Dependency Injection and Dependency Inversion?
**Answer:** 
- **Dependency Injection (DI):** A *pattern* for passing dependencies to an object from the outside (e.g., via constructor or setter).
- **Dependency Inversion (DIP):** A *principle* (SOLID) stating that high-level modules should depend on abstractions (interfaces), not concrete implementations (low-level modules). DI is one of the ways to implement DIP.

#### How does the Liskov Substitution Principle (LSP) apply to PHP's type hinting and return types?
**Answer:** 
- **Covariance:** Allows a child class to return a *more specific* type than the parent (supported in PHP 7.4+).
- **Contravariance:** Allows a child class to accept a *less specific* (wider) type than the parent (supported in PHP 7.4+).
LSP requires that child classes do not strengthen preconditions (contravariance) and do not weaken postconditions (covariance).
[SOLID Principles Guide](answers/solid_principles.md)

#### What is the difference between the Factory Pattern and Dependency Injection?
**Answer:** Factory pattern is about *creating* objects, while Dependency Injection is about *providing* already created dependencies to an object.

#### What are the main design patterns used in Symfony and Doctrine?
**Answer:**
- **Symfony:** Front Controller, Dependency Injection, Event Dispatcher.
- **Doctrine:** Data Mapper, Unit of Work, Repository.
[Design Patterns in Symfony & Doctrine](answers/design_patterns.md)

---

## 6. PHP 7/8+ New Features

### Junior
#### What are Named Arguments (introduced in PHP 8.0)?
**Answer:** Named arguments allow you to pass values by parameter name instead of position, making the code more readable and allowing you to skip optional parameters.
```php
htmlspecialchars($string, double_encode: false);
```

#### What are Union Types (introduced in PHP 8.0)?
**Answer:** Union types allow you to specify multiple types for a parameter, property, or return value, instead of just one.
```php
function sum(int|float $a, int|float $b): int|float {
    return $a + $b;
}
```

#### What are Match Expressions (introduced in PHP 8.0)?
**Answer:** A safer and more concise alternative to `switch` that uses strict comparison and returns a value.
```php
$status = match($code) {
    200, 201 => 'success',
    default => 'unknown',
};
```

#### What is the Nullsafe Operator (introduced in PHP 8.0)?
**Answer:** The `?->` operator allows you to safely access properties or methods on a potentially null value without throwing an error.
```php
$country = $session?->user?->getAddress()?->country;
```

#### What is the `#[Override]` attribute (introduced in PHP 8.3)?
**Answer:** The `#[Override]` attribute ensures that a method with the same name exists in a parent class or interface. This helps catch errors during refactoring.
```php
class Child extends ParentClass {
    #[Override]
    public function existingMethod() {}
}
```

#### What is the `json_validate()` function (introduced in PHP 8.3)?
**Answer:** Checks if a string is a valid JSON without decoding it, which uses less memory.
```php
if (json_validate($jsonString)) {
    // string is valid JSON
}
```

#### What is dynamic class constant fetch (introduced in PHP 8.3)?
**Answer:** A simplified syntax to fetch class constants using a variable.
```php
$name = 'STATUS_ACTIVE';
echo MyClass::{$name};
```

#### What are the `null`, `false`, and `true` standalone types (introduced in PHP 8.2)?
**Answer:** These can now be used as standalone types for return values, parameters, and properties.
```php
public function alwaysFalse(): false { return false; }
```

#### What are the new `array_*()` functions (introduced in PHP 8.4)?
**Answer:** PHP 8.4 introduced several useful array functions: `array_find()`, `array_find_key()`, `array_any()`, and `array_all()`.
```php
$firstEven = array_find([1, 2, 3, 4], fn($v) => $v % 2 === 0); // 2
```

#### What are the `array_first()` and `array_last()` functions (introduced in PHP 8.5)?
**Answer:** PHP 8.5 added native functions to get the first and last elements of an array without moving the internal array pointer.
```php
$arr = ['a' => 1, 'b' => 2, 'c' => 3];
$first = array_first($arr); // 1
$last = array_last($arr);   // 3
```

### Middle
#### What are Enums (introduced in PHP 8.1)?
**Answer:** Enumerations allow you to define a set of named constants, providing type safety for fixed sets of values.
```php
enum Status: string {
    case Pending = 'pending';
    case Active = 'active';
}
```

#### What are Readonly Properties (introduced in PHP 8.1)?
**Answer:** Properties that cannot be modified after they are initialized.
```php
class BlogData {
    public readonly string $title;
    public function __construct(string $title) {
        $this->title = $title;
    }
}
```

#### What is the First-class callable syntax (introduced in PHP 8.1)?
**Answer:** A simplified syntax to get a reference to any function or method.
```php
$fn = strlen(...);
echo $fn('hello'); // 5
```

#### What is the `never` return type (introduced in PHP 8.1)?
**Answer:** Indicates that a function will never return normally (it either throws an exception or calls `exit()`).
```php
function redirect(string $url): never {
    header("Location: $url");
    exit;
}
```

#### What is Constructor Property Promotion (introduced in PHP 8.0)?
**Answer:** A shorthand syntax to define and initialize class properties directly in the constructor.
```php
class User {
    public function __construct(public string $name, public int $age) {}
}
```

#### What are Typed Class Constants (introduced in PHP 8.3)?
**Answer:** Class constants can now have type declarations, similar to properties and parameters.
```php
class User {
    const string ROLE = 'admin';
}
```

#### What is Asymmetric Visibility (introduced in PHP 8.4)?
**Answer:** Properties can now have different visibility for reading and writing.
```php
class User {
    public private(set) string $name;
}
```

#### What are Property Hooks (introduced in PHP 8.4)?
**Answer:** Property hooks allow defining custom logic for getting and setting property values directly in the property declaration.
```php
class User {
    public string $fullName {
        get => "{$this->firstName} {$this->lastName}";
    }
}
```

### Senior
#### What are Readonly Classes (introduced in PHP 8.2)?
**Answer:** Marking a class as `readonly` makes all its properties readonly and prevents dynamic properties.
```php
readonly class Post {
    public function __construct(public string $title) {}
}
```

#### What are Disjunctive Normal Form (DNF) Types (introduced in PHP 8.2)?
**Answer:** Allows combining union and intersection types. Intersection types must be grouped with parentheses.
```php
public function process((A&B)|null $entity) { ... }
```

#### What are Intersection Types (introduced in PHP 8.1)?
**Answer:** Allow declaring that a parameter or return value must implement multiple interfaces or classes simultaneously.
```php
public function count(Countable&Iterator $it) { ... }
```

#### What are Fibers (introduced in PHP 8.1)?
**Answer:** A way to create lightweight coroutines for non-blocking I/O and asynchronous tasks. They allow pausing and resuming execution within a single thread.

#### What is the Pipe Operator (introduced in PHP 8.5)?
**Answer:** The `|>` operator allows chaining function calls in a more readable way, passing the result of the left side as the first argument to the right side.
```php
$result = "  hello  " |> 'trim' |> 'strtoupper';
```

[PHP 8.0 Features](answers/php80_features.md)
[PHP 8.1 Features](answers/php81_features.md)
[PHP 8.2 Features](answers/php82_features.md)
[PHP 8.3 Features](answers/php83_features.md)
[PHP 8.4 Features](answers/php84_features.md)
[PHP 8.5 Features](answers/php85_features.md)

---

## 7. MySQL & Databases

### Junior
#### How to connect to a MySQL database using PHP?
**Answer:** You can use the `mysqli` extension or `PDO` (PHP Data Objects). 
```php
// PDO Example
$pdo = new PDO('mysql:host=localhost;dbname=test', 'user', 'pass');
```

#### What is the `EXPLAIN` statement and how to read its output?

<span className="badge badge--primary margin-bottom--md">Middle</span>

**Answer:** The `EXPLAIN` statement provides information about how MySQL executes a query. It shows which indexes are used, how tables are joined, and the estimated number of rows examined. Key columns include `type` (join type, where `const` and `ref` are good, while `ALL` is a full table scan), `key` (the actual index used), and `Extra` (additional info like `Using filesort` or `Using temporary`, which often indicate performance issues).

[Detailed Guide on EXPLAIN](answers/mysql_explain.md)

---

### What is the difference between `mysqli` and `PDO`?
**Answer:** 
- `mysqli` is specific to MySQL, while `PDO` supports multiple database systems (PostgreSQL, SQLite, etc.).
- `PDO` supports named placeholders in prepared statements.

#### What is the difference between `WHERE` and `HAVING`?
**Answer:** `WHERE` filters rows *before* aggregation, while `HAVING` filters *after* aggregation (usually with `GROUP BY`).

#### What is a Primary Key?
**Answer:** A primary key is a column (or a set of columns) that uniquely identifies each record in a table. It cannot be null.

#### What is the difference between `CHAR` and `VARCHAR`?
**Answer:** `CHAR` is fixed-length (padded with spaces), while `VARCHAR` is variable-length (uses only the necessary space plus 1-2 bytes for length). `CHAR` is slightly faster for very short, uniform data (like country codes), while `VARCHAR` saves space for varying text lengths.

#### What are the main numeric data types in MySQL?
**Answer:**
- **TINYINT:** 1 byte (-128 to 127).
- **SMALLINT:** 2 bytes (-32,768 to 32,767).
- **INT:** 4 bytes (-2.1B to 2.1B).
- **BIGINT:** 8 bytes.
- **DECIMAL:** Fixed-point for financial data (exact precision).
- **FLOAT/DOUBLE:** Floating-point for approximate values.

#### What is the difference between `DATETIME` and `TIMESTAMP`?
**Answer:**
- **DATETIME:** 8 bytes. Range: 1000-01-01 to 9999-13-31. Constant across time zones.
- **TIMESTAMP:** 4 bytes. Range: 1970-01-01 to 2038-01-19. Converted to/from UTC based on the current time zone.

#### What are the `ENUM` and `SET` types?
**Answer:**
- **ENUM:** A string object that can have only one value chosen from a list of permitted values.
- **SET:** A string object that can have zero or more values chosen from a list of permitted values (up to 64).

#### What is the difference between `BLOB` and `TEXT`?
**Answer:** Both store large amounts of data, but `TEXT` is for character strings (respects character sets/collations), while `BLOB` (Binary Large Object) is for binary data (images, files) and is treated as a byte string.

#### What is the default port for MySQL?
**Answer:** 3306.

#### What is the difference between `CHAR_LENGTH` and `LENGTH`?
**Answer:** `CHAR_LENGTH` counts the number of characters, while `LENGTH` counts the number of bytes.

#### What do `%` and `_` represent in a `LIKE` statement?
**Answer:** 
- `%` matches any sequence of characters (zero or more).
- `_` matches exactly one character.

#### What are the different types of Joins in MySQL?
**Answer:**
- **(INNER) JOIN:** Returns records that have matching values in both tables.
- **LEFT (OUTER) JOIN:** Returns all records from the left table, and the matched records from the right table.
- **RIGHT (OUTER) JOIN:** Returns all records from the right table, and the matched records from the left table.
- **CROSS JOIN:** Returns the Cartesian product of the two tables (every row from the first table joined with every row from the second).
- **Self Join:** A regular join, but the table is joined with itself.

#### What is the difference between an Implicit and Explicit Join?
**Answer:**
- **Implicit Join:** Uses the `FROM` clause to list multiple tables and the `WHERE` clause to specify the join condition (e.g., `SELECT * FROM TableA, TableB WHERE TableA.id = TableB.a_id`).
- **Explicit Join:** Uses the `JOIN` keyword and the `ON` clause to specify the join condition (e.g., `SELECT * FROM TableA JOIN TableB ON TableA.id = TableB.a_id`). Explicit joins are generally preferred for readability and avoiding accidental Cartesian products.

#### What are the main string functions in MySQL?
**Answer:**
- `CONCAT(str1, str2, ...)`: Joins two or more strings.
- `CONCAT_WS(separator, str1, str2, ...)`: Joins strings with a separator.
- `LENGTH(str)` / `CHAR_LENGTH(str)`: Returns the length of a string in bytes/characters.
- `LEFT(str, len)` / `RIGHT(str, len)`: Returns a specified number of characters from the start/end.
- `SUBSTRING(str, start, len)`: Extracts a substring.
- `SUBSTRING_INDEX(str, delimiter, count)`: Returns a substring before a specified number of delimiter occurrences.
- `UPPER(str)` / `LOWER(str)`: Converts to uppercase/lowercase.
- `TRIM(str)` / `LTRIM(str)` / `RTRIM(str)`: Removes leading/trailing spaces.
- `REPLACE(str, from_str, to_str)`: Replaces occurrences of a substring.
- `INSERT(str, start, len, new_str)`: Replaces a portion of a string with another string.
- `LOCATE(substr, str)`: Returns the position of the first occurrence of a substring.
- `REVERSE(str)`: Reverses the characters in a string.
- `REPEAT(str, count)`: Repeats a string a specified number of times.
- `LPAD(str, len, pad)` / `RPAD(str, len, pad)`: Pads a string to a certain length.

#### What are the main numeric functions in MySQL?
**Answer:**
- `ABS(n)`: Returns the absolute value.
- `CEIL(n)` / `CEILING(n)`: Rounds up to the nearest integer.
- `FLOOR(n)`: Rounds down to the nearest integer.
- `ROUND(n, d)`: Rounds to a specified number of decimal places.
- `TRUNCATE(n, d)`: Truncates to a specified number of decimal places.
- `RAND()`: Returns a random floating-point value between 0 and 1.
- `SQRT(n)`: Returns the square root.
- `POWER(x, y)`: Returns $x$ raised to the power of $y$.
- `SIGN(n)`: Returns -1, 0, or 1 depending on whether the number is negative, zero, or positive.

#### What are the main date and time functions in MySQL?
**Answer:**
- `NOW()` / `SYSDATE()` / `CURRENT_TIMESTAMP()`: Returns current date and time.
- `CURDATE()` / `CURRENT_DATE()`: Returns current date.
- `CURTIME()` / `CURRENT_TIME()`: Returns current time.
- `UTC_DATE()` / `UTC_TIME()`: Returns current UTC date/time.
- `DATE_ADD(date, INTERVAL expr unit)` / `DATE_SUB(...)`: Adds/subtracts time intervals.
- `DATEDIFF(expr1, expr2)`: Returns the number of days between two dates.
- `DATE_FORMAT(date, format)`: Formats a date as specified.
- `EXTRACT(unit FROM date)`: Extracts a part of the date (YEAR, MONTH, DAY, HOUR, MINUTE, SECOND, etc.).
- `DAYOFMONTH()`, `DAYOFWEEK()`, `DAYOFYEAR()`, `LAST_DAY()`: Returns specific day information.
- `MONTH()`, `YEAR()`, `QUARTER()`, `WEEK()`: Returns specific date parts.
- `HOUR()`, `MINUTE()`, `SECOND()`: Returns specific time parts.
- `TIME_TO_SEC(time)`: Converts time to seconds since midnight.

#### What are the flow control functions in MySQL?
**Answer:**
- `IF(expr1, expr2, expr3)`: If `expr1` is true, returns `expr2`, else `expr3`.
- `IFNULL(expr1, expr2)`: If `expr1` is not NULL, returns `expr1`, else `expr2`.
- `COALESCE(list)`: Returns the first non-NULL value in the list.
- `CASE WHEN condition THEN result [ELSE result] END`: Evaluates a list of conditions and returns the corresponding result.

#### What is the difference between `WHERE` and `HAVING`?
**Answer:** `WHERE` filters rows *before* aggregation, while `HAVING` filters *after* aggregation (usually with `GROUP BY`).

#### What is the difference between a `WHERE` clause and a `JOIN ... ON` condition?
**Answer:**
- **`JOIN ... ON`**: Defines how tables are linked together. It is processed *during* the join operation.
- **`WHERE`**: Filters the resulting rows *after* the join has been logically formed.
- **Performance**: For `INNER JOIN`, they are often functionally equivalent as the optimizer can move conditions between them. For `OUTER JOIN` (LEFT/RIGHT), the placement is critical: a condition in `ON` determines which rows are joined (keeping all rows from the "outer" table), while a condition in `WHERE` filters the final result set (potentially turning a LEFT JOIN into an INNER JOIN if it filters out NULLs).

### Middle
#### What is a Transaction?
**Answer:** A sequence of database operations that are treated as a single unit of work. They must follow the ACID properties.

#### What are the ACID properties?
**Answer:** 
- **Atomicity:** All-or-nothing. Ensures that all operations within a transaction are completed; if any fails, the entire transaction is rolled back.
- **Consistency:** Ensures that a transaction transforms the database from one valid state to another, maintaining all predefined rules.
- **Isolation:** Prevents concurrent transactions from interfering with each other.
- **Durability:** Once a transaction is committed, its changes are permanent, even in the case of a system failure.

#### What are the main Transaction Control commands?
**Answer:**
- `START TRANSACTION` (or `BEGIN`): Starts a new transaction.
- `COMMIT`: Saves changes permanently.
- `ROLLBACK`: Undoes changes since the start of the transaction.
- `SAVEPOINT`: Sets a point within a transaction to which you can later roll back.
- `ROLLBACK TO SAVEPOINT`: Reverts changes only up to the specified savepoint.

#### What is Autocommit in MySQL?
**Answer:** A setting where every individual SQL statement is treated as a transaction and is automatically committed immediately after execution. It can be disabled using `SET autocommit = 0`.

#### What is a Deadlock?
**Answer:** A situation where two or more transactions are waiting for each other to release locks, creating a cycle of dependencies that prevents any of them from proceeding. MySQL typically detects this and rolls back one of the transactions.

#### What are the main storage engines in MySQL?
**Answer:** 
- **InnoDB:** Supports transactions, row-level locking, and foreign keys (Default).
- **MyISAM:** Does not support transactions, uses table-level locking, faster for read-heavy operations.
- **MEMORY:** Stores data in RAM for very fast access, but data is lost if the server restarts.

#### What is a View?
**Answer:** A virtual table based on the result-set of an SQL query. It does not store data itself.

#### What is a Trigger?
**Answer:** A database object that automatically executes in response to certain events (INSERT, UPDATE, DELETE) on a particular table.

#### What is InnoDB?
**Answer:** A storage engine for MySQL that supports transactions, foreign keys, and row-level locking. It is the default engine.

#### What is an Index and why is it used?
**Answer:** An index is a data structure used to quickly locate records in a table. It improves query speed but can slow down inserts/updates.

#### How are indexes stored?
**Answer:** Most database indexes (including MySQL's InnoDB) are stored as **B-trees** (specifically B+Trees). They are "horizontally large" (high fan-out) and shallow, which minimizes the number of disk I/O operations needed to find a value. This structure allows for fast filtering (O(log n)) and efficient range scans. Proper ordering in composite indexes is crucial: generally, equality conditions and high-selectivity columns should come first, followed by range conditions.

#### What is the difference between `UNION` and `UNION ALL`?
**Answer:** `UNION` removes duplicate rows from the result set, while `UNION ALL` includes all rows, including duplicates. To use `UNION`, the number and data types of columns in both `SELECT` statements must match.

#### How to perform conditional calculations in a `SELECT` query?
**Answer:** Use the `CASE` statement or `IF()` function. For example, to apply different interest rates based on a balance:
```sql
SELECT name, balance,
  CASE 
    WHEN balance < 3000 THEN balance * 1.1
    ELSE balance * 1.3
  END AS total
FROM accounts;
```

#### What is the difference between `IN` and `BETWEEN`?
**Answer:**
- **IN:** Checks if a value matches any value in a specified list or subquery.
- **BETWEEN:** Checks if a value falls within a specified range (inclusive of start and end values).

#### What are Aggregate Functions in MySQL?
**Answer:** Functions that perform a calculation on a set of values and return a single value. Common ones: `COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`.

#### What is the purpose of `GROUP BY` and `HAVING`?
**Answer:** `GROUP BY` groups rows that have the same values into summary rows. `HAVING` is used to filter these groups based on a condition (since `WHERE` cannot be used with aggregate functions).

#### What is a Subquery?
**Answer:** A query nested inside another query (SELECT, INSERT, UPDATE, or DELETE). They can be correlated (dependent on the outer query) or non-correlated (independent).

#### What is the `EXISTS` operator?
**Answer:** A logical operator used in a `WHERE` clause to check if a subquery returns any rows. It returns true if the subquery returns one or more records. It is often more efficient than `IN` for checking existence.

#### What is the difference between `ANY` and `ALL` when used with subqueries?
**Answer:**
- **ANY (or SOME):** Returns true if the comparison is true for *at least one* value in the subquery result.
- **ALL:** Returns true only if the comparison is true for *all* values in the subquery result.

#### What are the common column constraints (attributes)?
**Answer:**
- **NOT NULL:** Ensures a column cannot have a NULL value.
- **UNIQUE:** Ensures all values in a column are different.
- **PRIMARY KEY:** A combination of NOT NULL and UNIQUE.
- **FOREIGN KEY:** Prevents actions that would destroy links between tables.
- **DEFAULT:** Sets a default value for a column if none is specified.
- **CHECK:** Ensures that the values in a column satisfy a specific condition.
- **AUTO_INCREMENT:** Automatically generates a unique number for each new row.

#### What are the `ON DELETE` and `ON UPDATE` actions for Foreign Keys?
**Answer:**
- **CASCADE:** Automatically delete or update rows in the child table when the parent row is deleted/updated.
- **SET NULL:** Set the foreign key column in the child table to NULL.
- **RESTRICT / NO ACTION:** Reject the delete or update operation in the parent table if related rows exist in the child table.

#### What is the `CONSTRAINT` operator used for?
**Answer:** It is used to define and name constraints (PRIMARY KEY, UNIQUE, FOREIGN KEY, CHECK) on a table. Naming constraints allows you to easily drop or modify them later using their names.

#### What is the difference between `LIKE` and `REGEXP`?
**Answer:**
- **LIKE:** Uses simple wildcards (`%` for any string, `_` for any single character).
- **REGEXP:** Uses powerful regular expressions (`^`, `$`, `.`, `[ ]`, `|`, etc.) for complex pattern matching.

#### How can you modify an existing table structure?
**Answer:** Using the `ALTER TABLE` command to add, delete, or modify columns and constraints.

#### What is the difference between MariaDB and MySQL?
**Answer:** MariaDB is an open-source fork of MySQL created by the original developers after Oracle's acquisition of Sun Microsystems (which owned MySQL). While it maintains high compatibility, MariaDB offers more storage engines, performance improvements, and remains fully open-source (GPL), whereas MySQL has both a community and an enterprise version.
[MariaDB vs MySQL Comparison](answers/mysql_advanced.md#9-mariadb-vs-mysql)

### Senior
#### What is Sharding and Partitioning?
**Answer:** 
- **Partitioning:** Splitting a large table into smaller, more manageable pieces within the same database server.
- **Sharding:** Distributing data across multiple independent database servers (horizontal scaling).

#### What is a Clustered Index vs a Non-Clustered Index?
**Answer:** 
- **Clustered Index:** Determines the physical order of data in the table. There can only be one per table (usually the primary key).
- **Non-Clustered Index:** A separate structure that contains the indexed values and pointers to the data rows.

#### What isolation levels do you know?
**Answer:** The standard isolation levels are Read Uncommitted, Read Committed, Repeatable Read, and Serializable. They define the trade-off between consistency and performance in concurrent transactions.
[In-depth Database Isolation Levels Guide](answers/mysql_advanced.md#5-transaction-isolation-levels)

#### What is the difference between Read Committed and Dirty Read?
**Answer:** A Dirty Read occurs in Read Uncommitted where a transaction sees uncommitted data from another transaction. Read Committed prevents this by ensuring a transaction only sees data that has already been committed.
[In-depth Database Isolation Levels Guide](answers/mysql_advanced.md#5-transaction-isolation-levels)

#### Provide examples of transaction isolation levels and the issues they address.
**Answer:**
- **Read Uncommitted:** Reading a bank balance being updated but not yet committed (Dirty Read).
- **Read Committed:** A report reading the same data twice and getting different results because another transaction committed an update in between (Non-Repeatable Read).
- **Repeatable Read:** Ensuring a consistent snapshot of data throughout a long-running transaction, even if other transactions commit changes.
- **Serializable:** Financial systems where absolute correctness is required, preventing all concurrency issues like Phantom Reads at the cost of performance.
[In-depth Database Isolation Levels Guide](answers/mysql_advanced.md#51-practical-examples-of-isolation-levels)

#### What is the difference between MySQL and PostgreSQL?
**Answer:** MySQL is known for its speed and ease of use in web development, while PostgreSQL is an object-relational database known for its advanced features, strict SQL compliance, and better handling of complex queries and high-concurrency workloads.
[MySQL vs PostgreSQL Comparison](answers/mysql_advanced.md#10-mysql-vs-postgresql)

#### We have a filter with 3 columns from one table: `sku` (unique), `creation_date`, and an `integer_field` (values 1, 2, or 3). How would you index this for maximum performance?
**Answer:** Create a **composite index** including all three fields. For maximum performance, the order should be: **sku** (highest selectivity), then **integer_field** (exact matches), and finally **creation_date** (range filters). This order ensures that the most selective filter narrows down the results immediately, while the range filter comes last to allow the preceding parts of the index to be fully utilized.
[Composite Index Strategy](answers/mysql_advanced.md#11-composite-index-strategy)

#### You have this SQL query. Is something wrong about it and how would you improve it?
`SELECT * FROM table ORDER BY id LIMIT :limit OFFSET :offset`
- The `id` is Primary Key, AutoIncrement, and indexed.

**Answer:**
Using `OFFSET` for pagination is inefficient for large datasets because MySQL must scan and discard all rows before the offset. For example, `OFFSET 1000000` requires reading 1 million rows before returning the requested ones, leading to high I/O and CPU usage.

**Optimization (Cursor-based Pagination):**
- **Initial Request:** `SELECT * FROM table ORDER BY id LIMIT :limit`
- **Subsequent Requests:** Get the **last ID** from the current page (e.g., `:last_id`) and use it to skip previous rows:
  `SELECT * FROM table WHERE id > :last_id ORDER BY id LIMIT :limit`
This ensures the query remains fast regardless of the page number, as it uses the primary key index to jump directly to the starting point.

#### You have these SQL queries, what indexes would you use?
1. `SELECT * FROM transactions WHERE account_id = :account_id AND transaction_type = :transaction_type AND created_at BETWEEN :start AND :end`
2. `SELECT * FROM transactions WHERE account_id = :account_id AND transaction_type = :transaction_type ORDER BY created_at`
- `account_id`: bigint, ~100.000 variants
- `transaction_type`: int, 15 variants
- `created_at`: DATETIME, DEFAULT NOW()

**Answer:**
Use a composite index: `idx (account_id, transaction_type)`.

**Reasoning:**
- It is better to filter first by `account_id` to minimize the scope significantly (high selectivity).
- Then, filter by `transaction_type`.
- In the second query, `created_at` is used for ordering. While adding it to the index could avoid a `filesort`, it might not always be beneficial to include it if it's not used to narrow down the data much in most queries, or if the filtered result set is small enough that a post-select order is efficient.

#### You have this code, is there anything wrong about it?
```php
public function addBalance(float $sum, User $user) {
    $balance = $this->repo->getUserBalance($user->getId());
    $balance += $sum;
    $this->repo->updateUserBalance($balance, $user->getId());
}
// In getUserBalance: SELECT balance FROM Users WHERE id = :id
// In updateUserBalance: UPDATE Users SET balance = :balance WHERE id = :id
```

**Answer:**
This code is vulnerable to **race conditions** (Lost Updates). If two concurrent processes read the same balance before either updates it, one update will overwrite the other.

**In-depth explanation:**
- At the weakest isolation level (**Read Uncommitted**), a **Dirty Read** might occur (reading uncommitted data).
- Even with default isolation levels, concurrent "read-modify-write" cycles lead to data loss.
- **Solution:** Use **`SELECT ... FOR UPDATE`** in MySQL. This locks the specific row, forcing other transactions to wait until the lock is released (after the `UPDATE` is committed).

**Correct Implementation:**
```php
$this->db->transaction(function() use ($sum, $user) {
    // SELECT balance FROM Users WHERE id = :id FOR UPDATE
    $balance = $this->repo->getUserBalanceForUpdate($user->getId());
    $balance += $sum;
    $this->repo->updateUserBalance($balance, $user->getId());
});
```

#### Can you provide concrete SQL examples of how different isolation levels and locks behave in a concurrent environment?
**Answer:**
Understanding the exact behavior of isolation levels and locks is crucial for debugging race conditions. Detailed step-by-step scenarios (including T1/T2 timelines) for Dirty Reads, Non-Repeatable Reads, and Phantoms, as well as concrete examples of Exclusive (`FOR UPDATE`) vs. Shared (`LOCK IN SHARE MODE`) locks and Deadlocks, are provided in the guide.
[Detailed MySQL Isolation and Locking Scenarios](answers/mysql_advanced.md#52-detailed-isolation-level-scenarios)

#### How to ensure idempotency when inserting records (e.g., in a message broker consumer)?
**Answer:** Use **Unique Constraints** on relevant business keys (e.g., `(order_id, user_id)`). When a message is retried (at-least-once delivery), the database will block the second insert. Your application should catch the "Duplicate Key" error and treat it as a success or a known state to ensure consistent results.

#### How to manage capacity limits (e.g., maximum attendees) in a high-concurrency environment?
**Answer:**
1.  **Pessimistic Locking**: `SELECT * FROM meetups WHERE id = 1 FOR UPDATE;` to serialize all registrations for that meetup.
2.  **Denormalization + CHECK Constraint**: Add a `booked_seats` column and `CHECK (booked_seats <= total_seats)`. Update the counter in the same transaction as the registration. This lets the DB enforce the limit.
[Detailed Guide on SQL Concurrency and Limits](answers/sql_concurrency.md)

#### What are `xmin` and `xmax` in the context of database rows (e.g., PostgreSQL)?
**Answer:** These are hidden system columns used for **MVCC (Multi-Version Concurrency Control)**.
- **PostgreSQL**:
  - `xmin`: Stores the ID of the transaction that inserted the row.
  - `xmax`: Stores the ID of the transaction that deleted or updated the row.
- **MySQL (InnoDB)**:
  - `DB_TRX_ID`: Similar to `xmin`, stores the ID of the transaction that created the row version.
  - `DB_ROLL_PTR`: Pointer to the previous version in the **Undo Log** (used to reconstruct older versions).
  - `DB_ROW_ID`: Hidden row ID if no primary key exists.
Understanding these is key to explaining how different transactions "see" different versions of data simultaneously without blocking each other.

#### When should you use Redis for locking instead of native SQL features?
**Answer:** Use Redis for **Rate Limiting**, **Circuit Breakers**, or **Cross-Node Task Synchronization** (e.g., ensuring a cron job runs once). Avoid using Redis for data consistency *inside* the database. SQL features (Unique Indexes, Constraints, Row Locks) are more robust, handle failure scenarios better (no TTL issues), and don't add extra points of failure or overhead to the data layer.

---

## 8. PostgreSQL

### Junior

#### What is PostgreSQL and how does it differ from MySQL?
**Answer:** PostgreSQL is a powerful, open-source object-relational database system (ORDBMS). Unlike MySQL, which is purely relational, PostgreSQL supports advanced data types (like arrays and geometric types) and is known for its strict SQL compliance and extensibility.
[MySQL vs PostgreSQL Comparison](answers/mysql_vs_postgresql.md)

#### What are the main numeric data types in PostgreSQL?
**Answer:** 
- **integers:** `smallint` (2 bytes), `integer` (4 bytes), `bigint` (8 bytes).
- **floating-point:** `real` (4 bytes), `double precision` (8 bytes).
- **fixed-point:** `numeric` or `decimal` (exact precision for financial data).
- **autoincrement:** `serial`, `smallserial`, `bigserial`.
[Detailed PostgreSQL Data Types](answers/postgresql_data_types.md)

#### What is the difference between `VARCHAR(n)`, `CHAR(n)`, and `TEXT` in PostgreSQL?
**Answer:** 
- `CHAR(n)`: Fixed-length, padded with spaces.
- `VARCHAR(n)`: Variable-length with a maximum limit.
- `TEXT`: Variable-length with no specific limit.
In PostgreSQL, there is no performance difference between these three types, so `TEXT` or `VARCHAR` (without `n`) is often preferred for flexibility.

#### How do you define a Primary Key in PostgreSQL?
**Answer:** You can use `PRIMARY KEY` at the column level or table level. It uniquely identifies each row and cannot be NULL.
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username TEXT NOT NULL
);
```

#### What is a Foreign Key and how is it used?
**Answer:** A foreign key links a column in one table to the primary key of another table. It ensures referential integrity.
```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE
);
```

#### What is the difference between `INNER JOIN` and `LEFT JOIN`?
**Answer:** `INNER JOIN` returns only matching rows from both tables. `LEFT JOIN` returns all rows from the left table and matching rows from the right table (with NULLs if no match exists).
[PostgreSQL Table Relations](answers/postgresql_relations.md)

### Middle

#### What are the `CHECK` and `UNIQUE` constraints?
**Answer:** 
- `UNIQUE`: Ensures all values in a column (or group of columns) are distinct.
- `CHECK`: Ensures that values in a column satisfy a specific boolean condition (e.g., `price > 0`).
[PostgreSQL Constraints](answers/postgresql_constraints.md)

#### What are Arrays in PostgreSQL?
**Answer:** PostgreSQL allows columns to store arrays of any built-in or user-defined base type.
```sql
CREATE TABLE posts (
    tags TEXT[]
);
INSERT INTO posts (tags) VALUES ('{"php", "postgres"}');
```
[PostgreSQL Complex Data Structures](answers/postgresql_complex_types.md)

#### What is an `ENUM` type in PostgreSQL?
**Answer:** An `ENUM` is a data type that comprises a static, ordered set of values.
```sql
CREATE TYPE status AS ENUM ('pending', 'active', 'closed');
```
[PostgreSQL Complex Data Structures](answers/postgresql_complex_types.md)

#### What is the difference between `WHERE` and `HAVING` in PostgreSQL?
**Answer:** `WHERE` filters rows before grouping/aggregation. `HAVING` filters the results after `GROUP BY` has been applied.

#### What is a View and why is it used?
**Answer:** A **View** is a virtual table created from an SQL query. It doesn't store data itself but displays data from underlying tables. It's used to simplify complex queries, enhance security (by hiding sensitive columns), and provide a consistent interface for users.
[Detailed Guide on Views](answers/sql_views.md)

#### What are Aggregate Functions in PostgreSQL?
**Answer:** Functions that compute a single result from a set of input values, such as `COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`, and `STRING_AGG()`.

#### What is a Materialized View and how does it differ from a regular View?
**Answer:** A regular **View** is a virtual table that re-runs its query every time it is accessed. A **Materialized View** stores the actual query results on disk (like a table). This provides significant performance gains for complex queries but requires manual or scheduled refreshes to stay up-to-date.
[Detailed Guide on Views](answers/sql_views.md)

### Senior

#### What is MVCC (Multi-Version Concurrency Control) in PostgreSQL?
**Answer:** MVCC allows multiple transactions to see a consistent snapshot of the database without locking each other. It uses hidden columns like `xmin` and `xmax` to track row versions.

#### What are Correlated Subqueries?
**Answer:** A subquery that refers to columns from the outer query. It is executed once for each row processed by the outer query.
```sql
SELECT name, (SELECT AVG(price) FROM products p2 WHERE p2.category = p1.category)
FROM products p1;
```

#### How do `GROUPING SETS`, `ROLLUP`, and `CUBE` work?
**Answer:** These are extensions of `GROUP BY` for generating multiple grouping sets in a single query (useful for reporting and analytics).
- `ROLLUP`: Generates hierarchical subtotals.
- `CUBE`: Generates subtotals for all possible combinations of columns.
- `GROUPING SETS`: Allows specifying exact sets of columns to group by.

#### How can you modify a table structure using `ALTER TABLE`?
**Answer:** You can add/drop columns, change types, and add/remove constraints.
```sql
ALTER TABLE users ADD COLUMN email TEXT UNIQUE;
ALTER TABLE users ALTER COLUMN age TYPE SMALLINT;
```

---

## 9. Laravel & Symfony

### Junior
#### What is the MVC architecture and how does Laravel implement it?
**Answer:** MVC stands for Model-View-Controller. 
- **Model:** Represents the data structure (Eloquent).
- **View:** Displays the data (Blade templates).
- **Controller:** Handles logic and user input.

#### What is Eloquent ORM?
**Answer:** Laravel's built-in Object-Relational Mapper that allows you to interact with your database using PHP objects instead of raw SQL.

#### What is Blade?
**Answer:** Blade is Laravel's powerful and simple templating engine, allowing you to use plain PHP in your templates with a clean syntax.

#### What is Routing in Laravel?
**Answer:** Mapping HTTP requests (URLs) to specific controller actions or closures.

#### What are Named Routes?
**Answer:** Assigning a name to a route to allow generating URLs or redirects by referencing the name instead of the raw URL.

#### What are Route Groups?
**Answer:** A way to group multiple routes that share common attributes (like middleware or prefixes).

#### What are Seeders and Factories?
**Answer:** 
- **Seeders:** Populate the database with test/dummy data.
- **Factories:** Define the blueprint for generating dummy model data.

#### What is Soft Delete?
**Answer:** Marking a record as deleted without actually removing it from the database, typically using a `deleted_at` column.

### Middle
#### What is the Laravel Service Container?
**Answer:** A powerful tool for managing class dependencies and performing dependency injection.
[Service Container Deep Dive](answers/laravel_service_container.md)

#### What are Collections?
**Answer:** A wrapper around PHP arrays that provides a powerful, fluent interface for manipulating data (e.g., `map`, `filter`, `reduce`).

#### What is Middleware?
**Answer:** A filter that inspects and filters HTTP requests entering your application (e.g., authentication, logging).

#### What are Service Providers?
**Answer:** The central place of all Laravel application bootstrapping. They bind services into the Service Container.

#### What are Migrations?
**Answer:** Version control for your database, allowing you to define your table structure in PHP code.

#### What is the difference between Authentication and Authorization?
**Answer:** 
- **Authentication:** Verifying *who* the user is (e.g., login).
- **Authorization:** Verifying *what* the user is allowed to do (e.g., permissions).

### Senior
#### What are Facades in Laravel?
**Answer:** They provide a "static" interface to classes available in the Service Container. They serve as "proxy" classes for accessing underlying implementations.

#### What are Events and Listeners?
**Answer:** A way to decouple components in your application. An event is dispatched, and one or more listeners react to it.

#### What are Contracts in Laravel?
**Answer:** A set of interfaces that define the core services provided by the framework. They allow for swapping underlying implementations.

#### What are Service Container and Service Provider roles?
**Answer:** 
- **Service Container:** The registry of all dependencies.
- **Service Provider:** The classes that register and boot those dependencies into the container.

---

## 10. Tools & Composer

### Junior
#### What is Composer?
**Answer:** A dependency manager for PHP that allows you to declare the libraries your project depends on and manages (install/update) them for you.

#### What is the difference between `composer.json` and `composer.lock`?
**Answer:** 
- `composer.json`: Lists the required dependencies and their version constraints.
- `composer.lock`: Stores the exact versions of the packages that were installed.

### Middle
#### What is Autoloading and how does Composer handle it?
**Answer:** Autoloading automatically loads class files when they are needed. Composer uses PSR-4 (and others) to map namespaces to directories.

---

## 11. Caching & Redis

### Junior
#### What is Caching?
**Answer:** Storing data in a temporary storage area (like RAM) to retrieve it faster on subsequent requests.

### Middle
#### Redis vs Memcached
**Answer:** 
- **Redis:** Supports various data types (strings, lists, sets, etc.), persistence, and pub/sub.
- **Memcached:** Simple key-value store, mostly for simple caching.
[Redis vs Memcached Comparison](answers/caching.md)

---

## 12. Kafka

### Middle
#### How does Kafka ensure exactly-once processing, and what are the challenges involved?
**Answer:**
Kafka provides exactly-once semantics (EOS) using:
- **Idempotent producers**: Ensure retries don’t lead to duplicates.
- **Transactions**: Group multiple Kafka operations into a single atomic unit.
- **Consumer offset management**: When using Kafka Streams or transactional consumers, offsets are committed atomically.

**Challenges:**
- Requires idempotency on the consumer side.
- Need to enable `acks=all`, `enable.idempotence=true` in producers.
- Increased latency due to transactional writes.

#### What is the role of the high-water mark (HW) in Kafka, and how does it impact consumers?
**Answer:**
- **High-Water Mark (HW)** is the last offset that is considered “committed” and can be read by consumers.
- If a follower lags behind, it won’t get promoted to a leader because it doesn’t have the HW.
- Consumers cannot read uncommitted data (behind HW), ensuring consistency.

**Tricky Scenario:** If a broker crashes before HW is updated, some committed messages may disappear temporarily, but they will be recovered after leader election.

#### How does Kafka handle leader failure, and what happens to in-flight messages?
**Answer:**
- Kafka uses ZooKeeper/KRaft to elect a new leader from in-sync replicas (ISR).
- In-flight messages (`ack=1` or `ack=0`) may be lost if they weren’t replicated.
- If `acks=all`, the message is safe only if the leader had successfully replicated to ISRs before crashing.
- Uncommitted messages (below HW) may be lost in a failover scenario.

**Edge Case:** If there are no in-sync replicas, Kafka will pause writes to prevent data loss until a follower catches up.

#### Explain log compaction in Kafka. How does it differ from retention policies?
**Answer:**
- **Log compaction** retains at least one version of each key, deleting older versions.
- **Retention policies** (`log.retention.ms`, `log.retention.bytes`) delete messages based on time or size, regardless of key.

**Use Case:**
- Compaction is useful for changelogs (latest state updates) in databases.
- Retention is useful for event logs (like user activity tracking).

**Tricky Question:** What happens if a consumer never reads a compacted topic?
If the consumer was down for too long, it may lose some historical updates because old messages are deleted.

#### How does Kafka prevent duplicate messages in a producer retry scenario?
**Answer:**
Kafka uses idempotent producers to prevent duplicates when retries happen:
- Each message has a sequence number attached.
- The broker remembers the last acknowledged sequence number per producer.
- If a producer resends a message, Kafka discards duplicates.

**Tricky Scenario:** If a producer restarts and gets a new producer ID, old sequence tracking resets, and duplicates can appear.

#### How does Kafka handle backpressure when consumers lag behind?
**Answer:**
- Consumers lag when they process messages slower than they arrive.
- Kafka handles backpressure using `pause()`/`resume()` APIs in Kafka Consumer.
- If consumer lag is too high:
  - Increase consumer count in a consumer group.
  - Scale up processing power (e.g., batch processing).
  - Use rate limiting on the producer side.

#### What is the difference between in-sync replicas (ISR), out-of-sync replicas (OSR), and under-replicated partitions?
**Answer:**
- **ISR (In-Sync Replicas)**: Replicas that have caught up with the leader.
- **OSR (Out-of-Sync Replicas)**: Lagging followers that are behind.
- **Under-Replicated Partitions (URP)**: Any partition where the ISR count is less than the replication factor.

**Tricky Question:** If a Kafka topic has a replication factor of 3 and ISR = 1, what does that mean?
Only one broker is in sync (high risk of data loss). If the leader crashes, there may be no safe replicas to promote.

### Senior
#### Why is unclean leader election dangerous in Kafka?
**Answer:**
- When `unclean.leader.election.enable=true`, an out-of-sync follower can be elected as leader, potentially losing committed messages.
- This can cause data inconsistencies between producers and consumers.
- In production, always keep this `false` unless availability is more important than consistency.

#### How does Kafka Streams handle stateful processing efficiently?
**Answer:**
- Uses **RocksDB** as an embedded database for local state storage.
- Uses **changelogs topics** to recover state after crashes.
- Uses `punctuation()` for periodic processing without full reprocessing.

**Tricky Case:** If a Kafka Streams app restarts and changelog topic is deleted, all local state is lost and must be recomputed.

#### How can you monitor and debug consumer lag in Kafka?
**Answer:**
- Use `kafka-consumer-groups.sh` to check consumer lag.
- Monitor JMX metrics (`kafka.consumer:type=ConsumerLag`).
- Set up lag-based alerting in Prometheus/Grafana/New Relic.

**Tricky Debugging Scenario:**
If consumer lag is increasing despite multiple instances, check:
- Rebalance storms due to inefficient partition assignment.
- Slow deserialization (optimize message size).
- Consumer GC pauses (tune JVM settings).

#### How to implement distributed logging using Kafka in a PHP application?
**Answer:**
Kafka is an excellent buffer for high-volume logs (ELK/Graylog stack). To implement it in PHP:
- Use `php-rdkafka` as the producer.
- In your application, instead of writing directly to a file or database, send log events to a Kafka topic (e.g., `app_logs`).
- Configure the producer to use small batches (`queue.buffering.max.ms`) to avoid high overhead per message.
- Use log levels (e.g., `ERROR`, `INFO`) as partition keys to group logs efficiently.
- On the other side, use a Kafka consumer or a tool like Logstash to ingest logs into Elasticsearch.

[Detailed Kafka Guide](answers/kafka.md)

---

## 13. Infrastructure, Docker & DevOps

### Junior
#### What is Docker?
**Answer:** A platform for developing, shipping, and running applications in containers, ensuring that the application runs the same in any environment.

### Middle
#### Docker ENV vs ARG
**Answer:** 
- `ARG`: Variables available only during the image build process.
- `ENV`: Variables available during the build and also while the container is running.

#### What are Docker Volumes?
**Answer:** A way to persist data generated by and used by Docker containers, separated from the container's lifecycle.

#### What is the difference between Horizontal and Vertical scaling?
**Answer:** 
- **Vertical Scaling:** Adding more resources (CPU, RAM) to a single server.
- **Horizontal Scaling:** Adding more servers to distribute the load.

### Senior
#### What is CI/CD?
**Answer:** 
- **Continuous Integration (CI):** Automating the building and testing of code when changes are committed.
- **Continuous Delivery/Deployment (CD):** Automating the deployment of code to production or staging environments.

#### RabbitMQ vs Kafka
**Answer:** 
- **RabbitMQ:** A message broker that focuses on delivering messages to consumers (Push model). Great for complex routing.
- **Kafka:** A distributed streaming platform that focuses on high throughput and data persistence (Pull model). Great for log processing.

---

## 14. Testing & Quality

### Junior
#### Why is it important to write tests?
**Answer:** Tests ensure that the code works as expected, prevent regressions (bugs introduced after changes), document the behavior of the code, and make refactoring safer. They also help in designing better architecture by encouraging modularity.

#### What is a Unit Tests?
**Answer:** A type of software testing where individual units or components of a software are tested in isolation. In PHP, PHPUnit is the standard tool.

#### What kind of Unit Tests frameworks did you use?
**Answer:** The primary framework for PHP is **PHPUnit**. I've also used **Pest**, which is built on top of PHPUnit and offers a more expressive, functional syntax.

#### What is static analyzer tools?
**Answer:** Static analysis tools analyze code without executing it. They help in finding potential bugs, security vulnerabilities, code style violations, and other issues early in the development process.

#### How the Tests Coverage is measured?
**Answer:** Test coverage (or Code Coverage) is a metric that measures the percentage of code lines executed by your test suite. It is measured by tracking which parts of the code are hit during test execution, typically using tools like **Xdebug** or **PCOV**.

### Middle
#### What static analyzer tools did you use?
**Answer:** I have used **PHPStan** for finding bugs and improving code quality, **Psalm** for advanced static analysis (especially for large codebases), and **PHP_CodeSniffer (PHPCS)** for maintaining code style consistency.

#### What is the difference between unit, integration and feature tests?
**Answer:** 
- **Unit tests:** Test isolated components (e.g., a single method) in isolation from dependencies (often using mocks).
- **Integration tests:** Test interaction between multiple components (e.g., how a service interacts with a repository or a database).
- **Feature tests:** Test a full user story or an API endpoint from start to finish, often involving multiple layers of the application.

#### What method of unit tests cover measure did you use?
**Answer:** Commonly used methods include **Line Coverage** (percentage of executed lines), **Branch Coverage** (checking all paths through conditions), and **Path Coverage**. PHPUnit usually reports line coverage using drivers like Xdebug or PCOV.

#### What is Integration Testing?
**Answer:** Testing how different modules or components of your application work together as a group.

#### What is Mocking?
**Answer:** Creating a fake version of an object that simulates the behavior of the real object, used to isolate the unit of code being tested.

#### TDD vs BDD
**Answer:** 
- **TDD (Test-Driven Development):** Writing tests *before* writing the actual code. Focuses on implementation.
- **BDD (Behavior-Driven Development):** Writing tests based on expected behavior and user stories (e.g., Behat). Focuses on communication.

### Senior
#### What is Mutation Testing?
**Answer:** A type of testing where small changes (mutations) are introduced into the code to check if the existing tests fail. This measures the effectiveness of the tests.

---

## 15. Security

### Junior
#### What is the difference between Hashing and Encryption?
**Answer:** 
- **Hashing:** A one-way function that produces a fixed-length string from input. It cannot be reversed (e.g., `password_hash`).
- **Encryption:** A two-way function where data can be converted to ciphertext and then decrypted back to plaintext with a key.

#### Why do we use Salting when hashing passwords?
**Answer:** To prevent attackers from using precomputed tables (like Rainbow Tables) to crack passwords. A unique salt is added to each password before hashing.

### Middle
#### What is SQL Injection and how to prevent it?
**Answer:** A vulnerability where an attacker can execute malicious SQL statements. Prevented by using prepared statements and parameterized queries.

#### What is XSS (Cross-Site Scripting) and how to prevent it?
**Answer:** An attack where malicious scripts are injected into web pages. Prevented by escaping output (using `htmlspecialchars`) and sanitizing user input.

#### What is CSRF (Cross-Site Request Forgery) and how to prevent it?
**Answer:** An attack that forces an authenticated user to execute unwanted actions on a web application. Prevented by using CSRF tokens.

#### What is JWT (JSON Web Token)?
**Answer:** A compact, URL-safe means of representing claims to be transferred between two parties. Often used for stateless authentication.

### Senior
#### What is the OWASP Top 10?
**Answer:** A standard awareness document for developers and web application security, representing a broad consensus about the most critical security risks to web applications.

---

## 16. Web & API

### Junior
#### What is REST API?
**Answer:** REST (Representational State Transfer) is an architectural style for designing networked applications. It relies on a stateless, client-server, cacheable communications protocol — and in virtually all cases, the HTTP protocol is used.
[Read more: What is REST API?](https://www.ibm.com/think/topics/rest-apis)
#### What are the main HTTP methods used in REST?
**Answer:**
- `GET`: Retrieve a resource.
- `POST`: Create a new resource.
- `PUT`: Update/replace an existing resource.
- `PATCH`: Partially update an existing resource.
- `DELETE`: Delete a resource.
- `OPTIONS`: Describe the communication options for the target resource.
- `HEAD`: Same as GET but returns only the headers (no body).
#### What is the difference between GET and POST?
**Answer:** 
- `GET`: Data is sent in the URL. Used for retrieving data.
- `POST`: Data is sent in the request body. Used for submitting data.

#### What are HTTP Status Codes? Give examples.
**Answer:** 
- `200 OK`: Success.
- `201 Created`: Resource successfully created.
- `400 Bad Request`: Client error.
- `401 Unauthorized`: User not authenticated.
- `403 Forbidden`: User authenticated but not allowed.
- `404 Not Found`: Resource not found.
- `500 Internal Server Error`: Server error.

### Middle
#### What does "stateless" mean in the context of REST?
**Answer:** Each request from a client to a server must contain all the information necessary to understand and process the request. The server does not store any client context between requests.

#### SOAP vs REST
**Answer:** 
- **SOAP:** Protocol-based, uses XML, rigid structure, good for security and ACID compliance.
- **REST:** Architectural style, uses JSON/XML, flexible, lightweight, and uses standard HTTP.

#### REST vs JSON-RPC
**Answer:** 
- **REST:** Resource-oriented, uses HTTP methods (GET, POST, etc.) and status codes.
- **JSON-RPC:** Method-oriented, usually uses POST to a single endpoint with a JSON payload specifying the method and parameters.

#### Can you implement your own HTTP method like POSTAWESOME instead of POST? Would it be in compliance with REST?
**Answer:**
Technically, yes, you can define and use custom HTTP methods. The HTTP protocol allows for extension, and most web servers and clients can be configured to handle them.
However, it would **not** be fully in compliance with the **Uniform Interface** constraint of REST. REST emphasizes a standardized set of methods (the standard HTTP verbs) to ensure interoperability, predictability, and to leverage existing web infrastructure (like caches and proxies) that only understand standard methods. Using `POSTAWESOME` would break these benefits and make your API harder to consume.

---

## 17. Highload & Scalability

### Middle
#### What is Load Balancing?
**Answer:** Distributing network or application traffic across multiple servers to improve responsiveness and availability.

### Senior
#### How to optimize a slow GET endpoint that retrieves many records?
**Answer:** 
- Use pagination.
- Use caching (Redis/Memcached).
- Optimize database queries (indexing, select only needed columns).
- Use Eager Loading to avoid the N+1 problem.

#### What is the CAP Theorem?
**Answer:** In a distributed system, you can only provide two of the following three guarantees: Consistency, Availability, and Partition Tolerance.

#### What is Database Replication?
**Answer:** Copying data from one database server (Master) to another (Slave) to improve reliability, fault tolerance, and performance.

---

## 18. Clean Code & Best Practices

### Junior
#### What are DRY and KISS?
**Answer:** 
- **DRY:** Don't Repeat Yourself.
- **KISS:** Keep It Simple, Stupid.

#### What is YAGNI?
**Answer:** You Ain't Gonna Need It. Don't implement features until they are actually needed.

### Middle
#### What is Composition over Inheritance?
**Answer:** A principle that suggests achieving polymorphic behavior and code reuse by composing objects with other objects that implement the desired functionality, rather than inheriting from a base or parent class.

### Senior
#### Imagine, that you have an old class in monolith, this class has many methods and is needing change, how would you refactor?
**Answer:** 
1. **Understand & Test:** Write tests for the existing functionality to ensure no regressions are introduced during refactoring.
2. **Identify Responsibilities:** Identify the core responsibilities of the class and extract them into smaller, single-purpose classes (SRP).
3. **Decouple:** Use Dependency Injection to decouple the class from its dependencies, making it more testable.
4. **Incremental Refactoring:** Refactor in small, incremental steps, running tests after each change.
5. **Apply Design Patterns:** Use patterns like Strategy, Factory, or Service objects to handle complex logic or multiple conditional paths.
[Detailed Design Patterns Guide](answers/design_patterns.md)

#### What is the Law of Demeter?
**Answer:** A design guideline that says a module should not know about the innards of the objects it manipulates. "Don't talk to strangers."

---

## 19. Elasticsearch

### Middle
#### What is Elasticsearch and its main features?
**Answer:** A distributed, RESTful search and analytics engine built on top of Apache Lucene. It provides fast search capabilities across large datasets.
[Elasticsearch Features](answers/elasticsearch.md)

#### What is an Inverted Index?
**Answer:** The core data structure of Elasticsearch, which maps terms (words) to the documents where they occur, allowing for very fast searches.

#### What are Analyzers?
**Answer:** Components that process text during indexing or searching. They consist of a Character Filter, a Tokenizer, and a Token Filter.

---

## 20. Tricky Questions

### Junior
#### What is the difference between `==` and `===`?
**Answer:** 
- `==`: Equality (checks value, performs type juggling).
- `===`: Identity (checks both value and type).

#### What happens when you compare "10" and 10 using `==`?
**Answer:** It returns `true` because of type juggling.

#### What does the following code output?
```php
function foo(&$var) {
    $var++;
}
$a = 5;
foo($a);
echo $a;
```
**Answer:** `6`. The `&` operator passes the variable by reference, so the modification inside the function affects the original variable.

### Middle
#### What is the difference between `array_merge()` and the `+` operator for arrays?
**Answer:** 
- `array_merge()`: For string keys, the latter value overrides the former. For numeric keys, values are appended and re-indexed.
- `+`: For string keys, the former value is kept, and the latter is ignored. For numeric keys, it also keeps the former.

#### How do static properties behave in inherited classes?
**Answer:** Static properties are shared among all classes in an inheritance hierarchy unless they are explicitly overridden in a child class.

### Senior
#### Why is it risky to use PHP as a daemon?
**Answer:** PHP was originally designed for short-lived requests. In a long-lived process, memory leaks in your code or libraries will eventually crash the process.

---

## 21. Laravel Plugins

### Junior
#### What are some of the most popular official Laravel plugins?
**Answer:** Laravel provides an extensive ecosystem of official packages to extend its functionality. Some of the most widely used ones include:
[Detailed Laravel Plugins Guide](answers/laravel_plugins.md)
- **Laravel Horizon:** A beautiful dashboard and code-driven configuration for Redis-powered queues.
- **Laravel Breeze:** A minimal, simple implementation of all of Laravel's authentication features.
- **Laravel Sanctum:** Provides a featherweight authentication system for APIs and SPAs.
- **Laravel Socialite:** A fluent interface to OAuth authentication with various providers (Google, GitHub, etc.).
- **Laravel Sail:** A light-weight CLI for interacting with Laravel's default Docker configuration.

### Middle
#### What is the purpose of Laravel Horizon and Laravel Octane?
**Answer:** 
- **Laravel Horizon:** Provides a dashboard to monitor job throughput, runtime, and failures in Redis queues. It allows for code-driven configuration of queue workers.
- **Laravel Octane:** Supercharges application performance by keeping it "bootstrapped" in memory using high-powered servers like Swoole or RoadRunner.

#### What are Laravel Livewire and Laravel Inertia?
**Answer:**
- **Laravel Livewire:** A full-stack framework that allows building dynamic interfaces using only PHP and minimal JavaScript.
- **Laravel Inertia:** Allows building single-page apps using classic server-side routing and controllers with modern frontend frameworks like Vue or React.

### Senior
#### Compare Laravel Pulse and Laravel Telescope.
**Answer:**
- **Laravel Pulse:** Designed for **real-time monitoring** of application health and performance in production (e.g., slow routes, high CPU usage).
- **Laravel Telescope:** A **debug assistant** for local development or staging, providing deep insight into requests, exceptions, logs, database queries, and more.

#### Provide a comprehensive list of Laravel plugins with their descriptions and documentation links for versions 11, 12, and 13.
**Answer:**
Below is a list of prominent Laravel packages and plugins, their short descriptions, the Laravel version they were introduced in (or became major parts of the ecosystem), and their documentation links.

| Plugin | Short Description | Since | v11 Docs | v12 Docs | v13 Docs |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Horizon** | Dashboard and code-driven configuration for Redis queues. | 5.5 | [Link](https://laravel.com/docs/11.x/horizon) | [Link](https://laravel.com/docs/12.x/horizon) | [Link](https://laravel.com/docs/13.x/horizon) |
| **Livewire** | Full-stack framework for building dynamic interfaces in PHP. | 2019 | [Link](https://livewire.laravel.com) | [Link](https://livewire.laravel.com) | [Link](https://livewire.laravel.com) |
| **Octane** | Performance booster using Swoole or RoadRunner. | 8.0 | [Link](https://laravel.com/docs/11.x/octane) | [Link](https://laravel.com/docs/12.x/octane) | [Link](https://laravel.com/docs/13.x/octane) |
| **Telescope** | Elegant debug assistant for monitoring application state. | 5.7 | [Link](https://laravel.com/docs/11.x/telescope) | [Link](https://laravel.com/docs/12.x/telescope) | [Link](https://laravel.com/docs/13.x/telescope) |
| **Sanctum** | API and SPA authentication (formerly Airlock). | 7.0 | [Link](https://laravel.com/docs/11.x/sanctum) | [Link](https://laravel.com/docs/12.x/sanctum) | [Link](https://laravel.com/docs/13.x/sanctum) |
| **Pulse** | Real-time health and performance monitoring tool. | 10.x | [Link](https://laravel.com/docs/11.x/pulse) | [Link](https://laravel.com/docs/12.x/pulse) | [Link](https://laravel.com/docs/13.x/pulse) |
| **Reverb** | First-party, high-performance WebSocket server. | 11.0 | [Link](https://laravel.com/docs/11.x/reverb) | [Link](https://laravel.com/docs/12.x/reverb) | [Link](https://laravel.com/docs/13.x/reverb) |
| **Breeze** | Minimal authentication scaffolding. | 8.0 | [Link](https://laravel.com/docs/11.x/starter-kits#laravel-breeze) | [Link](https://laravel.com/docs/12.x/starter-kits#laravel-breeze) | [Link](https://laravel.com/docs/13.x/starter-kits#laravel-breeze) |
| **Jetstream** | Advanced application scaffolding (Inertia/Livewire). | 8.0 | [Link](https://jetstream.laravel.com) | [Link](https://jetstream.laravel.com) | [Link](https://jetstream.laravel.com) |
| **Cashier** | Subscription billing with Stripe or Paddle. | 4.2 | [Link](https://laravel.com/docs/11.x/cashier) | [Link](https://laravel.com/docs/12.x/cashier) | [Link](https://laravel.com/docs/13.x/cashier) |
| **Dusk** | Browser automation and testing API. | 5.4 | [Link](https://laravel.com/docs/11.x/dusk) | [Link](https://laravel.com/docs/12.x/dusk) | [Link](https://laravel.com/docs/13.x/dusk) |
| **Scout** | Full-text search for Eloquent models. | 5.3 | [Link](https://laravel.com/docs/11.x/scout) | [Link](https://laravel.com/docs/12.x/scout) | [Link](https://laravel.com/docs/13.x/scout) |
| **Socialite** | OAuth authentication with various providers. | 5.0 | [Link](https://laravel.com/docs/11.x/socialite) | [Link](https://laravel.com/docs/12.x/socialite) | [Link](https://laravel.com/docs/13.x/socialite) |
| **Sail** | CLI for interacting with Laravel's Docker environment. | 8.x | [Link](https://laravel.com/docs/11.x/sail) | [Link](https://laravel.com/docs/12.x/sail) | [Link](https://laravel.com/docs/13.x/sail) |
| **Pennant** | Simple, lightweight feature flag package. | 10.x | [Link](https://laravel.com/docs/11.x/pennant) | [Link](https://laravel.com/docs/12.x/pennant) | [Link](https://laravel.com/docs/13.x/pennant) |
| **Folio** | Page-based routing for Laravel applications. | 10.x | [Link](https://laravel.com/docs/11.x/folio) | [Link](https://laravel.com/docs/12.x/folio) | [Link](https://laravel.com/docs/13.x/folio) |
| **Volt** | Elegantly build Livewire components in a single file. | 10.x | [Link](https://livewire.laravel.com/docs/volt) | [Link](https://livewire.laravel.com/docs/volt) | [Link](https://livewire.laravel.com/docs/volt) |
| **Pint** | Opinionated PHP code style fixer for minimalists. | 9.x | [Link](https://laravel.com/docs/11.x/pint) | [Link](https://laravel.com/docs/12.x/pint) | [Link](https://laravel.com/docs/13.x/pint) |
| **Envoy** | Simple task runner for remote servers. | 5.1 | [Link](https://laravel.com/docs/11.x/envoy) | [Link](https://laravel.com/docs/12.x/envoy) | [Link](https://laravel.com/docs/13.x/envoy) |
| **Prompts** | Beautiful, user-friendly forms for CLI applications. | 10.x | [Link](https://laravel.com/docs/11.x/prompts) | [Link](https://laravel.com/docs/12.x/prompts) | [Link](https://laravel.com/docs/13.x/prompts) |
| **Echo** | Real-time event broadcasting client. | 5.3 | [Link](https://laravel.com/docs/11.x/broadcasting) | [Link](https://laravel.com/docs/12.x/broadcasting) | [Link](https://laravel.com/docs/13.x/broadcasting) |

---

## 22. Long-Running (RoadRunner)

### Middle
#### What is RoadRunner and how does it work?
**Answer:** RoadRunner is a high-performance PHP application server, load-balancer, and process manager written in Go. It works as a persistent process that manages a pool of PHP workers. When a request comes in, RoadRunner passes it to an available PHP worker via a high-performance binary protocol (Goridge). The PHP worker stays alive after the request is finished, eliminating the overhead of re-initializing the PHP engine and your application framework (bootstrapping) for every request.
[Detailed RoadRunner Guide](answers/roadrunner.md)
![How it works](https://docs.roadrunner.dev/images/rr-scheme.png)

#### What is a PHP Worker in the context of RoadRunner?
**Answer:** A PHP worker is a script that runs in a continuous loop. It uses the `Spiral\RoadRunner\Worker` class to wait for requests from the RoadRunner server, process them (often via PSR-7), and send back responses. Workers can communicate with the server via standard pipes (default), TCP, or Unix sockets. Since RoadRunner 2.0, any warnings or output to `STDOUT` are automatically forwarded to `STDERR` to prevent protocol corruption.

#### How does RoadRunner manage the Pool of workers?
**Answer:** RoadRunner maintains a pool of PHP workers and can dynamically scale them. It uses a supervision strategy to restart workers based on various limits defined in `.rr.yaml`:
- `max_jobs`: Number of requests a worker can handle before being retired.
- `max_memory`: Memory limit per worker.
- `ttl`: Maximum lifetime for a worker.
- `idle_ttl`: Maximum duration a worker can spend in idle mode.
For local development, `pool.debug = true` can be used to allocate a worker only when a request arrives, simplifying debugging.

#### What should a developer keep in mind when writing code for a long-running PHP environment?
**Answer:** 
- **Memory Leaks:** Any leaked memory will accumulate over time; use `supervisor.max_worker_memory` as a safeguard.
- **State Persistence:** Static variables and global state persist between requests, which can lead to data leaks or unexpected behavior if not reset.
- **Resource Management:** Database connections and file handles should be re-validated or properly closed, as they might time out or stay open across many requests.
- **Error Handling:** Uncaught exceptions might crash the worker; they should be caught and reported back to RoadRunner via `$worker->error()`.

#### What is the Key-Value (KV) plugin in RoadRunner?
**Answer:** The KV plugin provides a unified interface to interact with various storage backends for simple key-value storage. It offloads caching or state management from PHP to the Go server. Supported drivers include:
- `redis` (distributed)
- `memcached` (distributed)
- `boltdb` (file-based)
- `memory` (fast, local to the RR instance)

#### How do Queues and Jobs work in RoadRunner?
**Answer:** RoadRunner acts as a job consumer and manager, receiving jobs from various brokers and pushing them to PHP workers. This centralizes job management without needing separate CLI processes. Supported drivers/brokers include:
- `amqp`, `beanstalk`, `redis`, `sqs`, `nats`, `boltdb`, and `memory`.
![Queues](https://docs.roadrunner.dev/images/jobs-scheme.png)

#### How does RoadRunner handle HTTP requests and headers?
**Answer:** RoadRunner's HTTP plugin acts as a fast web server that converts incoming requests into PSR-7 compatible objects for PHP workers. It supports Gzip compression, custom headers, and middleware at the Go level. It can also handle response streaming, allowing PHP to send large payloads to the client incrementally.

### Senior
#### What is the Centrifuge plugin in RoadRunner?
**Answer:** The Centrifuge plugin integrates RoadRunner with Centrifugo or its Go-based equivalent, enabling real-time messaging (WebSockets, SockJS) in PHP applications. RoadRunner handles thousands of persistent connections in Go, while the PHP workers only handle business logic and events.

#### What is the purpose of the Service plugin?
**Answer:** The Service plugin allows RoadRunner to run and monitor any binary or script as a sub-process (sidecar). It ensures the process is always running, automatically restarting it if it fails. This is ideal for background tasks like consumers, metrics exporters, or proxy servers.

#### How does the Locks plugin work?
**Answer:** The Locks plugin provides a distributed locking mechanism. It allows PHP workers to synchronize access to shared resources across multiple workers or even multiple RoadRunner instances using backends like Redis or local memory.

#### What are the best practices for running RoadRunner in Production?
**Answer:**
- **Process Supervision:** Use `systemd` or `supervisord` to keep the main RoadRunner process running.
- **Worker Limits:** Always set `max_jobs` or `max_memory` to prevent memory bloat.
- **Health Monitoring:** Use the `status` plugin for health and readiness checks.
- **OS Configuration:** Increase the number of allowed file descriptors (`ulimit -n`).
- **Logs:** Configure structured logging (JSON) for easier ingestion by log management tools.

#### What is Response Streaming and Health Checks in RoadRunner?
**Answer:**
- **Response Streaming:** Allows PHP to stream data to the client incrementally, reducing memory usage for large file downloads or real-time data feeds.
- **Health Checks:** The `status` plugin provides `/health` (is the server alive?) and `/ready` (is there at least one free worker?) endpoints. These are essential for Kubernetes liveness/readiness probes or load balancer health checks.
#### How does RoadRunner integrate with Temporal?
**Answer:** RoadRunner can serve as a worker for Temporal, an orchestration engine for complex, stateful, and long-running workflows. PHP developers can write workflow and activity logic in PHP, while RoadRunner handles the communication with the Temporal server via the Goridge protocol.

---

## 23. PSR & PER Standards

### Junior
#### What PSR documentation is covering Basic Coding Standards?
**Answer: PSR-1**
PSR-1 aims to ensure a high degree of technical interoperability between shared PHP code. It covers basic coding elements such as files, namespaces, classes, and methods.
- Use only `<?php` and `<?=` tags.
- Use only UTF-8 without BOM for PHP code.
- Files should either declare symbols (classes, functions, constants, etc.) or cause side effects (generate output, change .ini settings, etc.) but should not do both.
- Namespaces and classes must follow PSR-4.
- Class names must be declared in `StudlyCaps`.
- Class constants must be declared in all upper case with underscore separators.
- Method names must be declared in `camelCase`.

#### What PSR documentation is covering Extended Coding Standards?
**Answer: PSR-12**
PSR-12 is an extension of PSR-2 (which it superseded) and provides a more comprehensive set of coding style rules. **Note:** It has been superseded by the living [PER Coding Style](#23-psr--per-standards) standard.
- Indentation must be 4 spaces, no tabs.
- Line length soft limit is 80 characters, hard limit 120.
- Braces for classes and methods must go on a new line.
- Visibility must be declared on all properties and methods.
- Type hints are required for parameters and return values where possible.

#### What PSR documentation is covering Autoloading?
**Answer: PSR-4**
PSR-4 describes a specification for autoloading classes from file paths. It is the modern replacement for PSR-0.
- It maps namespaces to directory structures.
- It allows for a "prefix" (base directory) for a namespace.
- **Note on PSR-0:** PSR-0 was the previous standard but was deprecated because it was less flexible (e.g., it required the full namespace to be reflected in the directory structure, and underscores in class names were treated as directory separators).

### Middle
#### What PSR documentation is covering Logging Interface?
**Answer: PSR-3**
PSR-3 provides a common interface for logging libraries. It defines a `LoggerInterface` with methods for 8 log levels (debug, info, notice, warning, error, critical, alert, emergency). This allows applications to be independent of the specific logging implementation (like Monolog).

#### What PSR documentation is covering HTTP Message Interface?
**Answer: PSR-7**
PSR-7 describes common interfaces for representing HTTP messages (Requests and Responses).
- HTTP messages are treated as immutable objects.
- Covers requests, responses, uploaded files, URIs, and streams.
- Facilitates interoperability between different HTTP-related libraries and frameworks.

#### What PSR documentation is covering HTTP Handlers?
**Answer: PSR-15**
PSR-15 defines interfaces for HTTP server-side components: Request Handlers and Middleware.
- Middleware allows processing incoming requests and outgoing responses in layers.
- Works in conjunction with PSR-7 HTTP messages to provide a standard way of handling web requests.

### Senior
#### What PSR documentation is covering Container interface?
**Answer: PSR-11**
PSR-11 provides a common interface for dependency injection containers.
- Defines `get($id)` and `has($id)` methods.
- Allows libraries to retrieve entries from a container in a standardized way, making them container-agnostic.

#### What PSR documentation is covering Event Dispatcher?
**Answer: PSR-14**
PSR-14 defines a common interface for dispatching events and listening for them.
- Consists of an Event Dispatcher, a Listener Provider, and the Event object.
- Provides a standard way to implement extension points in PHP applications and libraries.

#### What PSR documentation is covering HTTP Factory?
**Answer: PSR-17**
PSR-17 defines interfaces for factories that create PSR-7 compatible HTTP objects.
- Since PSR-7 objects are immutable, factories provide a standard way to create new instances (Requests, Responses, Streams, etc.) without depending on a specific implementation.

#### [IMPORTANT] What is PER Coding Style and why is it important?
**Answer: PER Coding Style**
The **PHP Evolved Recommendation (PER)** for Coding Style is the modern successor to PSR-12. Unlike PSRs, which are static once accepted, PERs are "living documents" that can be updated to reflect new PHP features and evolving best practices.
- **Successor to PSR-12:** It expands and replaces PSR-12 while still requiring adherence to PSR-1 (Basic Coding Standards).
- **Living Standard:** It is updated to include rules for new PHP syntax such as constructor property promotion, enums, readonly properties, and more.
- **Goal:** To provide a comprehensive, evolving set of coding style rules that keep pace with the language's development, ensuring consistent code across the PHP ecosystem.

#### Summary of other PSR Standards
**What PSR documentation covers [Standard]?**
- **PSR-0: Autoloading Standard** - Deprecated in favor of PSR-4.
- **PSR-2: Coding Style Guide** - Deprecated in favor of PSR-12.
- **PSR-12: Extended Coding Style** - Superseded by [PER Coding Style](https://www.php-fig.org/per/coding-style/).
- **PSR-5: PHPDoc Standard (Draft)** - Standard for docblocks.
- **PSR-6: Caching Interface** - Common interface for caching libraries.
- **PSR-13: Hypermedia Links** - Interface for hypermedia links.
- **PSR-16: Simple Cache** - A simplified caching interface.
- **PSR-18: HTTP Client** - Interface for sending HTTP requests.
- **PSR-19: PHPDoc tags (Draft)** - Specific PHPDoc tags.
- **PSR-20: Clock** - Interface for reading the time.
- **PSR-21: Internationalization (Draft)** - i18n standards.
- **PSR-22: Application Tracing (Draft)** - Tracing standards.

---

## 24. Basic Algorithms

### Junior

#### How would you reverse a string in PHP?
**Answer:** There are several ways to reverse a string in PHP, ranging from using built-in functions to manual implementations that handle multi-byte characters. [Detailed Guide](answers/reverse_string.md)

- **Junior:** Use the built-in `strrev()` function.
- **Middle:** Use a manual `for` loop, which demonstrates understanding of string byte structure but is not multi-byte safe.
- **Senior:** Use a multi-byte safe approach (e.g., `mb_str_split()`) to handle UTF-8 characters correctly.

```php
// Junior
echo strrev("hello"); // "olleh"

// Senior (UTF-8 safe)
function mb_strrev(string $string, string $encoding = null): string {
    $chars = mb_str_split($string, 1, $encoding ?: mb_internal_encoding());
    return implode('', array_reverse($chars));
}
```

#### What is Linear Search and how is it implemented in PHP?
**Answer:** Linear Search is the simplest search algorithm. It checks every element in a list sequentially until it finds the target value or reaches the end of the list.
- **Complexity:** O(n).
- **Use Case:** Small datasets or unsorted lists.

```php
function linearSearch(array $arr, $target): int
{
    foreach ($arr as $index => $value) {
        if ($value === $target) {
            return $index;
        }
    }
    return -1;
}
```

#### What is Binary Search and what are its requirements?
**Answer:** Binary Search is a fast search algorithm that works by repeatedly dividing the search interval in half.
- **Requirement:** The array **must be sorted**.
- **Complexity:** O(log n).

```php
function binarySearch(array $arr, $target): int
{
    $low = 0;
    $high = count($arr) - 1;

    while ($low <= $high) {
        $mid = floor(($low + $high) / 2);
        if ($arr[$mid] === $target) {
            return $mid;
        }
        if ($arr[$mid] < $target) {
            $low = $mid + 1;
        } else {
            $high = $mid - 1;
        }
    }
    return -1;
}
```

#### What is Bubble Sort?
**Answer:** Bubble Sort is a simple sorting algorithm that repeatedly steps through the list, compares adjacent elements, and swaps them if they are in the wrong order. This process is repeated until the list is sorted.
- **Complexity:** O(n²).
- **Use Case:** Educational purposes or very small datasets.

```php
function bubbleSort(array $arr): array
{
    $n = count($arr);
    for ($i = 0; $i < $n; $i++) {
        for ($j = 0; $j < $n - $i - 1; $j++) {
            if ($arr[$j] > $arr[$j + 1]) {
                // Swap
                $temp = $arr[$j];
                $arr[$j] = $arr[$j + 1];
                $arr[$j + 1] = $temp;
            }
        }
    }
    return $arr;
}
```

### Middle

#### How does Quick Sort work?
**Answer:** Quick Sort is a "divide and conquer" algorithm. It picks an element as a "pivot" and partitions the array around the pivot, so that elements smaller than the pivot are on the left and larger ones are on the right. It then recursively sorts the sub-arrays.
- **Complexity:** O(n log n) average, O(n²) worst case.

```php
function quickSort(array $arr): array
{
    if (count($arr) < 2) {
        return $arr;
    }

    $pivot = $arr[0];
    $left = $right = [];

    for ($i = 1; $i < count($arr); $i++) {
        if ($arr[$i] < $pivot) {
            $left[] = $arr[$i];
        } else {
            $right[] = $arr[$i];
        }
    }

    return array_merge(quickSort($left), [$pivot], quickSort($right));
}
```

#### What is Merge Sort and what is its main advantage?
**Answer:** Merge Sort is another "divide and conquer" algorithm. It divides the array into two halves, recursively sorts them, and then merges the sorted halves.
- **Advantage:** It has a guaranteed O(n log n) time complexity, even in the worst case. It is also a "stable" sort.

```php
function mergeSort(array $arr): array
{
    $count = count($arr);
    if ($count <= 1) {
        return $arr;
    }

    $mid = (int)($count / 2);
    $left = array_slice($arr, 0, $mid);
    $right = array_slice($arr, $mid);

    return merge(mergeSort($left), mergeSort($right));
}

function merge(array $left, array $right): array
{
    $result = [];
    while (count($left) > 0 && count($right) > 0) {
        if ($left[0] <= $right[0]) {
            $result[] = array_shift($left);
        } else {
            $result[] = array_shift($right);
        }
    }
    return array_merge($result, $left, $right);
}
```

#### Where should you use these algorithms in real-world PHP development?
**Answer:**
1. **Search Optimization:** Use **Binary Search** when you have a large, static, sorted dataset (e.g., searching in a large sorted list of product IDs or names).
2. **Resource Management:** **Queues** (FIFO) are used for background job processing (e.g., Laravel Queues, RabbitMQ) to handle tasks like sending emails without blocking the main request.
3. **Parsing:** **Stacks** (LIFO) or recursive algorithms are used for parsing structured data like HTML, XML, or custom template engines to ensure tags/brackets are correctly nested.
4. **Data Presentation:** **Sorting** algorithms are essential for displaying data in a specific order (alphabetical, by price, by date).

---

## 25. HaPHPiness - Best things in PHP

This section highlights modern PHP features and the overall "happiness" of the ecosystem, as inspired by [haphpiness.com](https://haphpiness.com/).

### Junior

#### What are Named Arguments?
**Answer:** Named arguments (introduced in PHP 8.0) allow you to pass values by parameter name instead of position.
[See PHP 8.0 Features](#what-are-named-arguments-introduced-in-php-80)

#### What are Union Types and Intersection Types?
**Answer:** Union types (8.0) allow multiple types for a parameter; Intersection types (8.1) require a value to satisfy multiple type constraints.
[See PHP 8.0 Features](#what-are-union-types-introduced-in-php-80)

#### What are Enums?
**Answer:** Enumerations (8.1) provide type safety for a fixed set of named constants.
[See PHP 8.1 Features](#what-are-enums-introduced-in-php-81)

#### What are str_contains(), str_starts_with(), and str_ends_with()?
**Answer:** These are consistent, readable functions for common string operations introduced in PHP 8.0.
[Detailed HaPHPiness Guide](answers/haphpiness.md#string-functions)

#### What is Array Unpacking with String Keys?
**Answer:** Introduced in PHP 8.1, the spread operator (`...`) now supports string keys for merging arrays.
[Detailed HaPHPiness Guide](answers/haphpiness.md#array-unpacking)

#### What is the Built-in Development Server?
**Answer:** PHP includes a simple web server for development purposes, started with `php -S localhost:8000`.
[Detailed HaPHPiness Guide](answers/haphpiness.md#dev-server)

#### What are Arrow Functions?
**Answer:** Introduced in PHP 7.4, they offer a concise syntax for short closures with automatic variable capture.
[Detailed HaPHPiness Guide](answers/haphpiness.md#arrow-functions)

#### What are Match Expressions?
**Answer:** A safer, stricter alternative to `switch` that returns a value (PHP 8.0).
[Detailed HaPHPiness Guide](answers/haphpiness.md#match-expressions)

#### What is the Null Coalescing Operator?
**Answer:** The `??` operator (and `??=` assignment) provides a clean way to handle null values and defaults.
[Detailed HaPHPiness Guide](answers/haphpiness.md#null-coalescing)

#### What are Numeric Literal Separators?
**Answer:** Using underscores to improve the readability of large numbers (e.g., `1_000_000`).
[Detailed HaPHPiness Guide](answers/haphpiness.md#numeric-separators)

### Middle

#### What are Fibers?
**Answer:** Lightweight coroutines for non-blocking I/O (PHP 8.1).
[See PHP 8.1 Features](#what-are-fibers-introduced-in-php-81)

#### What are Attributes?
**Answer:** First-class metadata (annotations) natively supported by the PHP engine since 8.0.
[Detailed HaPHPiness Guide](answers/haphpiness.md#attributes)

#### What is the First-class Callable Syntax?
**Answer:** A clean way to create a `Closure` from a function or method using `(...)`.
[See PHP 8.1 Features](#what-is-the-first-class-callable-syntax-introduced-in-php-81)

#### What is the Nullsafe Operator?
**Answer:** The `?->` operator allows safe method/property access on potentially null values.
[See PHP 8.0 Features](#what-is-the-nullsafe-operator-introduced-in-php-80)

#### What are Property Hooks?
**Answer:** Logic for `get` and `set` defined directly on property declarations (PHP 8.4).
[See PHP 8.4 Features](#what-are-property-hooks-introduced-in-php-84)

#### What is Asymmetric Visibility?
**Answer:** Different visibility for reading and writing a property (e.g., `public private(set)`).
[See PHP 8.4 Features](#what-is-asymmetric-visibility-introduced-in-php-84)

#### What is the Pipe Operator?
**Answer:** A way to chain function calls from left to right using `|>` (PHP 8.5).
[See PHP 8.5 Features](#what-is-the-pipe-operator-introduced-in-php-85)

#### What is array_is_list()?
**Answer:** A function to check if an array is a sequential list with keys starting from 0.
[Detailed HaPHPiness Guide](answers/haphpiness.md#array-is-list)

#### What are WeakMaps?
**Answer:** Maps where keys are objects that don't prevent those objects from being garbage collected.
[Detailed HaPHPiness Guide](answers/haphpiness.md#weakmap)

#### What are Named Capture Groups in preg_match?
**Answer:** Using `(?P<name>...)` in regex to get named matches instead of just numeric indices.
[Detailed HaPHPiness Guide](answers/haphpiness.md#named-capture-groups)

#### What is the Spaceship Operator?
**Answer:** The `<=>` operator for three-way comparison, returning -1, 0, or 1.
[Detailed HaPHPiness Guide](answers/haphpiness.md#spaceship-operator)

#### What is Array Destructuring with Keys?
**Answer:** Pulling specific values out of an associative array using the `[...]` syntax.
[Detailed HaPHPiness Guide](answers/haphpiness.md#array-destructuring)

### Senior

#### What is #[\NoDiscard]?
**Answer:** An attribute that warns if a function's return value is ignored (PHP 8.5).
[Detailed HaPHPiness Guide](answers/haphpiness.md#nodiscard)

#### What are Fatal Error Backtraces?
**Answer:** Since PHP 8.5, fatal errors (like timeouts) include a full stack trace for easier debugging.
[Detailed HaPHPiness Guide](answers/haphpiness.md#fatal-error-backtraces)

#### What is the URI Extension?
**Answer:** A built-in, immutable, and standards-compliant URL parsing extension (PHP 8.5).
[Detailed HaPHPiness Guide](answers/haphpiness.md#uri-extension)

#### What is FFI (Foreign Function Interface)?
**Answer:** Allows calling C functions and using C data structures directly from PHP (7.4+).
[Detailed HaPHPiness Guide](answers/haphpiness.md#ffi)

#### What are PHPStan and Psalm?
**Answer:** Static analysis tools that provide TypeScript-grade type safety for PHP codebases.
[Detailed HaPHPiness Guide](answers/haphpiness.md#static-analysis)

#### What is NativePHP?
**Answer:** A framework for building native desktop and mobile applications using PHP and Laravel.
[Detailed HaPHPiness Guide](answers/haphpiness.md#nativephp)

## 26. LeetCode Solutions

This section contains solutions to LeetCode challenges, focusing on PHP implementations.

For a full list of solutions organized by pages, please visit the [LeetCode Solutions Documentation](./docs/docs/questions/26-leetcode-solutions.mdx).

### Summary of Solutions
- [Page 1 (Challenges 1-35)](./docs/docs/answers/leetcode/page-1.mdx)
- [Page 2](./docs/docs/answers/leetcode/page-2.mdx)
- [Page 3](./docs/docs/answers/leetcode/page-3.mdx)
- [Page 4](./docs/docs/answers/leetcode/page-4.mdx)
- [Page 5](./docs/docs/answers/leetcode/page-5.mdx)

## 27. Regexp

### Junior

#### What is Regex?
**Answer:** Regex (Regular Expression) is a powerful tool for text matching, searching, and manipulation. In PHP, it is implemented through the PCRE library.
[Detailed Regexp Guide](answers/regexp.md#1-advanced-pcre-features)

#### Why is Regex good?
**Answer:** It is concise, powerful, and extremely efficient when written correctly. It allows complex text patterns to be described in a single line.
[Detailed Regexp Guide](answers/regexp.md#4-the-elements-of-good-regex-style)

#### How to use Regex in PHP?
**Answer:** PHP uses `preg_*` functions like `preg_match()`, `preg_match_all()`, and `preg_replace()`. Patterns must be enclosed in delimiters (e.g., `/pattern/`).
[Detailed Regexp Guide](answers/regexp.md#5-php-regex-functions-in-depth)

#### What is the difference between `preg_match` and `preg_match_all`?
**Answer:** `preg_match` stops after the first match, while `preg_match_all` finds all occurrences in the string.
[Detailed Regexp Guide](answers/regexp.md#5-php-regex-functions-in-depth)

### Middle

#### How would you build a complex Regex step-by-step?
**Answer:** 1. Identify what to ignore (quotes, tags). 2. Identify the target pattern. 3. Combine using the "Best Trick" (`Ignore|Match|(Capture)`). 4. Optimize using AGRA (Anchor, Greed, Repeat, Atomic).
[Detailed Regexp Guide](answers/regexp.md#3-step-by-step-tutorial-building-a-complex-regex)

#### What is the "Best Regex Trick Ever"?
**Answer:** It is a technique to match patterns while excluding specific contexts (like quotes or comments) using a simple `Ignore|Match|(Capture)` pattern.
[Detailed Regexp Guide](answers/regexp.md#2-the-best-regex-trick-ever-exclude-context)

#### What is `preg_filter`?
**Answer:** It is similar to `preg_replace`, but it only returns the elements that actually matched the pattern, effectively filtering the input array.
[Detailed Regexp Guide](answers/regexp.md#5-php-regex-functions-in-depth)

### Senior

#### What are Atomic Groups and why use them?
**Answer:** `(?>...)` groups prevent backtracking. They "lock" a match, preventing the engine from trying alternative paths, which avoids "catastrophic backtracking" and improves performance.
[Detailed Regexp Guide](answers/regexp.md#atomic-groups)

#### Is ChatGPT good at regex?
**Answer:** While helpful for drafts, ChatGPT often lacks deep expertise. It can produce "dot-star soup" (inefficient patterns) or make syntax errors. Expert review is always needed.
[Detailed Regexp Guide](answers/regexp.md#6-is-chatgpt-good-at-regex)



