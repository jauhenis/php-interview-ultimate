---
title: 'Architecture Practical Examples'
slug: '/answers/architecture_examples'
---

# Practical Example: Hexagonal, Onion, and Clean Architecture in PHP

These architectures share a common goal: **keep the business logic (the "Inside" or "Core") independent of external tools** like databases, web servers, and third-party libraries.

Let's implement a simple "User Registration" use case to see how this looks in practice.

---

## 1. The Domain Layer (Core/Inside)

This is the heart of the application. It contains the business rules and domain entities. It has **no dependencies** on any other layer.

```php
namespace App\Domain\Entity;

class User {
    private string $id;
    private string $email;
    private string $password;

    public function __construct(string $id, string $email, string $password) {
        $this->id = $id;
        $this->email = $email;
        $this->password = $password;
    }

    // Domain methods (business logic)
    public function changePassword(string $newPassword): void {
        if (strlen($newPassword) < 8) {
            throw new \InvalidArgumentException("Password too short");
        }
        $this->password = $newPassword;
    }

    public function getEmail(): string { return $this->email; }
    public function getId(): string { return $this->id; }
}
```

---

## 2. The Ports (Interfaces)

The Domain layer defines **what it needs** from the outside world using interfaces. In Hexagonal terms, these are **Driven Ports**.

```php
namespace App\Domain\Repository;

use App\Domain\Entity\User;

interface UserRepositoryInterface {
    public function save(User $user): void;
    public function findByEmail(string $email): ?User;
}
```

---

## 3. The Application Layer (Use Cases)

This layer coordinates the domain objects to achieve a specific goal. It depends only on the Domain layer (and its interfaces).

```php
namespace App\Application\UseCase;

use App\Domain\Entity\User;
use App\Domain\Repository\UserRepositoryInterface;

class RegisterUserUseCase {
    private UserRepositoryInterface $repository;

    public function __construct(UserRepositoryInterface $repository) {
        $this->repository = $repository;
    }

    public function execute(string $email, string $password): void {
        // Business rule check
        if ($this->repository->findByEmail($email)) {
            throw new \Exception("User already exists");
        }

        $user = new User(uniqid(), $email, password_hash($password, PASSWORD_BCRYPT));
        $this->repository->save($user);
    }
}
```

---

## 4. The Infrastructure Layer (Adapters)

This layer contains the concrete implementations (Adapters) that interact with external systems (Database, Mail, API).

```php
namespace App\Infrastructure\Persistence;

use App\Domain\Entity\User;
use App\Domain\Repository\UserRepositoryInterface;
use PDO;

class SqlUserRepository implements UserRepositoryInterface {
    private PDO $pdo;

    public function __construct(PDO $pdo) {
        $this->pdo = $pdo;
    }

    public function save(User $user): void {
        $stmt = $this->pdo->prepare("INSERT INTO users (id, email, password) VALUES (?, ?, ?)");
        $stmt->execute([$user->getId(), $user->getEmail(), '...']);
    }

    public function findByEmail(string $email): ?User {
        // SQL query logic to fetch user...
        return null; // Mocked
    }
}
```

---

## 5. The Presentation Layer (Driving Adapters)

How the outside world interacts with our application (HTTP, CLI).

```php
namespace App\Presentation\Http;

use App\Application\UseCase\RegisterUserUseCase;

class UserController {
    private RegisterUserUseCase $useCase;

    public function __construct(RegisterUserUseCase $useCase) {
        $this->useCase = $useCase;
    }

    public function register(array $requestData): string {
        try {
            $this->useCase->execute($requestData['email'], $requestData['password']);
            return "User registered successfully";
        } catch (\Exception $e) {
            return "Error: " . $e->getMessage();
        }
    }
}
```

---

## How it matches the Architectures:

### 1. Hexagonal Architecture (Ports & Adapters)

- **Ports**: `UserRepositoryInterface`.
- **Adapters**: `SqlUserRepository` (Driven) and `UserController` (Driving).
- **Goal**: You can swap `SqlUserRepository` for `RedisUserRepository` without touching the business logic.

### 2. Onion Architecture

- **Inner Core**: `App\Domain\Entity`.
- **Domain Services/Interfaces**: `App\Domain\Repository`.
- **Application Layer**: `App\Application\UseCase`.
- **Outer Layer**: `App\Infrastructure` and `App\Presentation`.
- **Goal**: Dependencies always point inward: Infrastructure -> Application -> Domain.

### 3. Clean Architecture

- **Entities**: `User`.
- **Use Cases**: `RegisterUserUseCase`.
- **Interface Adapters**: `SqlUserRepository`, `UserController`.
- **Frameworks & Drivers**: The actual database (`PDO`), the web framework (Symfony/Laravel).

---

## Dependency Injection (DI) - Wiring it up

In a real application (using a Service Container), it looks like this:

```php
// Composition Root
$pdo = new PDO('mysql:host=localhost;dbname=test', 'user', 'pass');
$repository = new SqlUserRepository($pdo); // Concrete Adapter
$useCase = new RegisterUserUseCase($repository); // Use Case
$controller = new UserController($useCase); // Controller

// Handling a request
echo $controller->register(['email' => 'test@example.com', 'password' => 'secret123']);
```
