# PHP Sessions: In-Depth Guide

Session support in PHP is a robust mechanism for preserving state across multiple requests. Understanding its internal workings is crucial for building scalable and secure applications and is a frequent topic in Middle and Senior level interviews.

## 1. The Internal Lifecycle

A PHP session follows a strict, well-defined lifecycle. Understanding this is essential for debugging issues like session data loss or concurrent request blocking.

### Phase 1: Request Start & Identification
- **Client Side**: When a browser makes a request, it checks its cookie jar. If it finds a cookie matching the session name (default `PHPSESSID`) and the current domain/path, it sends it in the `Cookie` header.
- **Server Side**: PHP receives the request. If `session.auto_start` is enabled (not recommended), PHP starts the lifecycle immediately. Otherwise, it waits for an explicit `session_start()` call.

### Phase 2: Initialization (`session_start()`)
This is the most critical phase where several things happen in sequence:
1.  **Session ID Retrieval**: PHP looks for the ID in the request. If none is found, it generates a new cryptographically secure random string.
2.  **Open Save Handler**: PHP calls the `open` method of the configured save handler (e.g., opens a file or connects to Redis).
3.  **Locking (Exclusive Lock)**: If using the `files` handler, PHP performs an `flock()` (exclusive lock) on the session file. This ensures that only **one script at a time** can access this specific user's session data.
4.  **Reading & Deserialization**: PHP reads the contents of the session storage. It then uses the `session.serialize_handler` to convert the string back into a PHP array and populates the `$_SESSION` superglobal.
5.  **Cookie Management**: If a new session was started, PHP adds a `Set-Cookie` header to the response buffer to tell the browser to store the session ID.

### Phase 3: Script Execution
- The developer can now freely read from and write to `$_SESSION`.
- **Note**: Changes made to the array are only in memory (RAM) at this stage. They are not yet persisted to the storage backend.

### Phase 4: Termination & Persistence (Commit)
The session is persisted when the script ends or when `session_write_close()` is called:
1.  **Serialization**: PHP takes the current state of `$_SESSION` and converts it into a serialized string.
2.  **Writing**: The serialized string is written to the storage backend (e.g., overwritten in the session file or updated in Redis).
3.  **Unlocking**: The exclusive lock is released, allowing other waiting requests from the same user to proceed.
4.  **Close Save Handler**: The save handler's `close` method is called.

### Phase 5: Garbage Collection (Probabilistic)
Before or after the session is handled, PHP might run its Garbage Collector (GC):
- It checks `session.gc_maxlifetime` against the last modification time of all session files.
- It only runs if a "lottery" is won based on `session.gc_probability / session.gc_divisor`.

> **Pro Tip:** Use `session_write_close()` as soon as you are done writing to the session. This releases the lock early and allows concurrent requests from the same user to proceed without waiting for the first one to finish.

## 2. Concurrency and Locking

By default, PHP sessions are **blocking**. If a user opens three tabs that all call `session_start()`, the second and third tabs will hang until the first one finishes (or calls `session_write_close()`).

*   **Impact**: High-latency requests (like long-polling or heavy reports) can block the entire user experience.
*   **Solution**: Use `session_write_close()` for read-heavy operations or switch to a non-blocking storage like Redis (though some Redis handlers also implement locking).

## 3. Storage and Serialization

### Save Handlers (`session.save_handler`)
-   **`files`**: Default. Simple but doesn't scale across multiple servers.
-   **`redis` / `memcached`**: Recommended for production. Allows multiple web servers to share session data.
-   **`user`**: Allows you to implement `SessionHandlerInterface` to store sessions anywhere (e.g., a SQL database).

### Serialization Handlers (`session.serialize_handler`)
-   **`php`**: The internal PHP format. Cannot handle numeric keys or special characters in keys easily.
-   **`php_binary`**: A more compact binary format.
-   **`php_serialize`**: Recommended. Uses the standard `serialize()`/`unserialize()` functions. It is the most robust and handles complex data structures best.

**Warning:** Never store **Resources** (like `PDO` connections, file handles, or GD images) in sessions. They cannot be serialized and will be lost or corrupted.

## 4. Garbage Collection (GC)

Since sessions don't "log out" automatically, PHP needs to clean up old session files. This is handled by a probabilistic mechanism:

-   **`session.gc_maxlifetime`**: Seconds after which data is seen as 'garbage' (default 1440s / 24 mins).
-   **`session.gc_probability` / `session.gc_divisor`**: The probability that GC is started. By default, it's `1 / 1000` (0.1% chance on every `session_start()`).

> **Note:** In some systems (like Debian/Ubuntu), the probability is set to 0, and a system cron job cleans up the `/var/lib/php/sessions` directory instead.

## 5. Security Best Practices

### Session Fixation
An attacker provides a victim with a known session ID (e.g., via a link).
-   **Defense**: Call `session_regenerate_id(true)` immediately after a user logs in. This changes the ID and deletes the old one.
-   **Strict Mode**: Enable `session.use_strict_mode = 1`. This prevents PHP from accepting an uninitialized session ID provided by the client.

### Session Hijacking
An attacker steals a valid session ID (e.g., via XSS or sniffing).
-   **Defense**:
    *   Use **HTTPS** and set `session.cookie_secure = 1`.
    *   Set `session.cookie_httponly = 1` to prevent access via JavaScript.
    *   Set `session.cookie_samesite = "Lax"` or `"Strict"` to prevent CSRF.

### Configuration (`php.ini`)
```ini
session.use_strict_mode = 1
session.cookie_httponly = 1
session.cookie_secure = 1
session.cookie_samesite = "Lax"
session.gc_maxlifetime = 3600
session.use_only_cookies = 1
```

## 6. Interview Questions "Cheat Sheet"

1.  **How do you handle concurrent requests blocking each other?**
    *   Use `session_write_close()` as soon as possible.
2.  **Why can't you store a database connection in a session?**
    *   Resources cannot be serialized; they represent a live connection that cannot be "frozen" and "thawed" as a string.
3.  **How to share sessions between two different subdomains?**
    *   Set `session.cookie_domain = ".example.com"`.
4.  **What happens if `session_start()` is called after output?**
    *   It fails with a "headers already sent" warning because it needs to send the session cookie in the HTTP header.
5.  **How do sessions scale horizontally?**
    *   Use a centralized store like Redis or Memcached instead of the local filesystem.
