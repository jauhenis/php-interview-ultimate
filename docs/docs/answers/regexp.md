# Regular Expressions (Regex): The Complete Guide

Regular Expressions (Regex) are a powerful syntax that allows you to match strings with specific patterns. Think of it as a suped-up text search shortcut that uses quantifiers, pattern collections, special characters, and capture groups to create extremely advanced search patterns.

Regex is essential for tasks like:
- Analyzing command line output
- Parsing user input
- Examining server or program logs
- Handling text files with consistent syntax (CSV, etc.)
- Reading configuration files
- Searching and refactoring code

---

## 1. Regex Cheat Sheet {#1-regex-cheat-sheet}

### Quantifiers
| Token | Description |
| :--- | :--- |
| `a\|b` | Match either "a" or "b" |
| `?` | Zero or one |
| `+` | One or more |
| `*` | Zero or more |
| `*?` | Zero or more, but stop after first match |
| `{N}` | Exactly N number of times |
| `{N, M}` | From N to M number of times |

### General Tokens
| Token | Description |
| :--- | :--- |
| `.` | Any character |
| `\n` | Newline character |
| `\t` | Tab character |
| `\s` | Any whitespace character (including `\t`, `\n`, etc.) |
| `\S` | Any non-whitespace character |
| `\w` | Any word character (Upper/lowercase letters, 0-9, `_`) |
| `\W` | Any non-word character |
| `\b` | Word boundary (Matches between characters) |
| `\B` | Non-word boundary |
| `^` | The start of a line |
| `$` | The end of a line |
| `\\` | The literal character "\" |

### Pattern Collections
| Token | Description |
| :--- | :--- |
| `[A-Z]` | Match any uppercase character from "A" to "Z" |
| `[a-z]` | Match any lowercase character from "a" to "z" |
| `[0-9]` | Match any number |
| `[asdf]` | Match any character that's either "a", "s", "d", or "f" |
| `[^asdf]` | Match any character that's not any of the following: "a", "s", "d", "f" |

### Flags
| Token | Description |
| :--- | :--- |
| `g` | Global, match more than once |
| `m` | Force `$` and `^` to match each newline individually |
| `i` | Make the regex case-insensitive |

### Groups
| Token | Description |
| :--- | :--- |
| `(...)` | Capture group |
| `(?:...)` | Non-capture group |
| `(?<name>...)` | Named capture group called "name" |

### Named Back Reference
| Token | Description |
| :--- | :--- |
| `\k<name>` | Reference named capture group "name" in query |

### Lookahead and Lookbehind
| Token | Description |
| :--- | :--- |
| `(?!)` | Negative lookahead |
| `(?=)` | Positive lookahead |
| `(?<!)` | Negative lookbehind |
| `(?<=)` | Positive lookbehind |

---

## 2. Basics: What does a regex look like? {#2-basics-what-does-a-regex-look-like}

In its simplest form, a regex looks like the word you are searching for: `/Test/` matches "Test".

However, regex can be much more complex. For example, a phone number pattern:
`^(?:\d{3}-){2}\d{4}$`

```text
Pattern: ^(?:\d{3}-){2}\d{4}$
Match: "555-555-5555"
No Match: "555-abc-5555"
```

A more readable (but longer) version of the same regex is:
`^[0-9]{3}-[0-9]{3}-[0-9]{4}$`

---

## 3. How to Read and Write Regexes {#3-how-to-read-and-write-regexes}

### Quantifiers
Quantifiers define how many times a character or group should be searched for:
- `a|b`: Match either "a" or "b"
- `?`: Zero or one
- `+`: One or more
- `*`: Zero or more
- `{N}`: Exactly N times
- `{N,}`: N or more times
- `{N,M}`: Between N and M times
- `*?`: Zero or more, but stop after the first match (lazy matching)

**Example**: `Hello{1,3}`
```text
Pattern: Hello{1,3}
Matches: "Hello", "Helloo", "Hellooo"
```

**Combined Example**: `He?llo{2}`
```text
Pattern: He?llo{2}
Matches: "Helloo", "Hlloo"
```

#### Greedy vs. Lazy Matching
By default, quantifiers are **greedy**—they match as much as possible.
- `Hi+` matches "Hi" and "Hiiiiii".
- `Hi+?` (lazy) matches only "Hi" even if more "i"s follow.

Using `.` (any character) with greedy/lazy matching:
- `H.*llo` (greedy)
```text
String: "Hellollollo"
Match: "Hellollollo"
```

- `H.*?llo` (lazy)
```text
String: "Hellollollo"
Match: "Hello"
```

### Pattern Collections
Collections allow searching for a set of characters:
- `[A-Z]`: Any uppercase letter
- `[a-z]`: Any lowercase letter
- `[0-9]`: Any digit
- `[asdf]`: Any of "a", "s", "d", "f"
- `[^asdf]`: Any character *except* "a", "s", "d", "f"
- `[0-9A-Z]`: Any digit or uppercase letter

**Combining with tokens**: You can include tokens in collections. For example, `[A-Z\s]` matches any uppercase letter or whitespace character.

### General Tokens
- `.` : Any character
- `\n` : Newline
- `\t` : Tab
- `\s` : Any whitespace (space, tab, newline)
- `\S` : Any non-whitespace character
- `\w` : Any word character (A-Z, a-z, 0-9, and `_`)
- `\W` : Any non-word character
- `\b` : Word boundary (the edge between `\w` and `\W`). Matches between characters.
```text
Pattern: \b\w+\b
String: "This is a string"
Matches: "This", "is", "a", "string"
```

- `\B` : Non-word boundary
- `^` : Start of a line
```text
Pattern: ^\w+
String: "This is a string"
Match: "This"
```

- `$` : End of a line
```text
Pattern: \w+$
String: "This is a string"
Match: "string"
```

- `\\` : Literal backslash

**Line Boundaries Example**: `$\s+` can be used to find all whitespace between the end of one line and the start of the next (useful for minification).

### Character Escaping
To match characters that have special meaning in regex (like `.`, `*`, `\`), use a backslash to escape them:
- `\\n` matches the literal string "\n" rather than a newline character.

---

## 4. Usage in Code (PHP Examples) {#4-usage-in-code-php-examples}

While most languages support regex, syntax varies slightly. Here are common PHP implementations using the PCRE extension:

### Creating and Searching
```php
// preg_match returns 1 if match found, 0 if not
preg_match('/[a-z]/', 'apple', $matches); // Returns 1, $matches[0] is "a"
preg_match('/[a-z]/', '123', $matches);   // Returns 0, $matches is empty

// preg_match_all finds all occurrences
preg_match_all('/[a-z]/', 'abc', $matches); // Returns 3, $matches[0] is ["a", "b", "c"]
```

### Replacing Strings
```php
function youSayHelloISayGoodbye(string $str): string {
  // Matches Hello, hello, Hi, hi, Hey, or hey
  return preg_replace('/[Hh]ello|[Hh]i|[Hh]ey/', 'Goodbye', $str);
}
// String: "Hello, hi there!" -> Result: "Goodbye, Goodbye there!"
```

### Global Search vs. JavaScript
In PHP, `preg_match` finds the first occurrence, and `preg_match_all` finds all. Unlike JavaScript's `/g` flag on a regex object, PHP functions themselves determine if the search is global.
```php
$str = "test1 test2 test3";
preg_match_all('/test\d/', $str, $matches);
// $matches[0] contains ["test1", "test2", "test3"]
```

---

## 5. Flags {#5-flags}
Flags are modifiers appended after the last slash:
- `g` (Global): Match all occurrences.
- `i` (Case-insensitive): Ignore case.
- `m` (Multiline): `^` and `$` match start/end of each line.

---

## 6. Groups {#6-groups}
Groups are defined by parentheses and allow you to treat multiple characters as a single unit.

### Capture vs. Non-capturing Groups
- `(...)`: Capture group. Matches the content and remembers it for later use (e.g., `$1` in replacement).
- `(?:...)`: Non-capturing group. Matches the content but doesn't "remember" it.

### Named Capture Groups
- `(?<name>...)`: A group named "name".
- **Example**: `/Testing (?<num>\d{3})/` -> Replace with `Hello $<num>`.
```text
String: "Testing 123"
Result: "Hello 123"
```

### Backreferences
- `\k<name>`: Matches whatever was captured in the group named "name".
- **Example**: `/.*(?<name>James). \k<name>,.*/`
```text
Match: "Hello there James. James, how are you doing?"
No Match: "Hello there James. Frank, how are you doing?"
```

### Lookahead and Lookbehind
- `(?=...)`: Positive lookahead (must follow).
- `(?!...)`: Negative lookahead (must NOT follow).
- `(?<=...)`: Positive lookbehind (must precede).
- `(?<!...)`: Negative lookbehind (must NOT precede).

**Example (Lookahead)**: `/B(?!A)/`
```text
String: "BC" -> Match: "B"
String: "BA" -> No Match
```

---

## 7. Advanced PCRE Features (PHP-Specific) {#7-advanced-pcre-features}

In PHP, the **PCRE** engine provides even more powerful features.

### Atomic Groups `(?>...)` {#atomic-groups}
Forbids backtracking once a match is locked. Prevents "catastrophic backtracking".

### Backtracking Control: `(*SKIP)(*F)`
- `(*F)`: Forces a failure.
- `(*SKIP)`: Skips to the current position if a later match fails.

### Recursion `(?R)` or `(?1)`
Allows matching nested structures like balanced parentheses: `/\((?:[^()]+|(?R))*\)/`.

---

## 8. "The Best Regex Trick Ever" (Exclude Context) {#8-the-best-regex-trick-ever-exclude-context}

Pattern: `IgnoreThis|IgnoreThat|MatchThis|(CaptureWhatIWant)`

### Example: Match `Tarzan` but NOT in double quotes
```php
$regex = '/"[^"]*"|(\bTarzan\b)/';
$text = 'Tarzan likes Jane. "Tarzan is in quotes". Tarzan is free.';
preg_match_all($regex, $text, $matches);
$realMatches = array_filter($matches[1]); 
// Results: ["Tarzan", "Tarzan"] (excludes the one in quotes)
```

---

## 9. Step-by-Step Tutorial: Building a Complex Regex {#9-step-by-step-tutorial-building-a-complex-regex}

**Goal**: Extract valid **E-mail addresses** from HTML, but ignore those inside `<code>` tags or `href="..."` attributes.

1. **Identify IGNORE**: `<code>.*?<\/code>` and `href="[^"]*"`
2. **Identify MATCH**: `[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}`
3. **Combine**: `<code>.*?<\/code>|href="[^"]*"|([a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})`

---

## 10. The Elements of Good Regex Style (AGRA) {#10-the-elements-of-good-regex-style}

1. **A - Anchor**: Use `^`, `$`, `\A`, `\z`.
2. **G - Greed**: Choose `*` vs `*?` carefully.
3. **R - Repeat**: Avoid `.*`. Be specific (e.g., `[^"]*`).
4. **A - Atomic**: Use `(?>...)` where backtracking is unnecessary.

---

## 11. PHP Regex Functions in Depth {#11-php-regex-functions-in-depth}

PHP provides several functions for working with regular expressions through the PCRE (Perl Compatible Regular Expressions) extension.

### `preg_match` vs `preg_match_all`
- **`preg_match($pattern, $subject, &$matches)`**: Searches for a match and returns 1 if found, 0 if not, or false on error. It stops searching after the first match.
- **`preg_match_all($pattern, $subject, &$matches)`**: Searches for all matches and returns the number of full pattern matches found.

### `preg_replace` and `preg_filter`
- **`preg_replace($pattern, $replacement, $subject)`**: Replaces all matches with the replacement string.
- **`preg_filter($pattern, $replacement, $subject)`**: Similar to `preg_replace`, but returns the results only where a match was found. If the input is an array, it effectively filters the array.

### Delimiters
In PHP, regex patterns must be enclosed in delimiters. While `/` is most common, you can use any non-alphanumeric character (e.g., `#`, `~`, `!`).
```php
preg_match('#[a-z]#', 'apple'); // Valid
```

---

## 12. Is ChatGPT good at Regex? {#12-is-chatgpt-good-at-regex}

While ChatGPT and other LLMs are great for drafting regular expressions, they have significant limitations:

1. **Dot-Star Soup**: They often resort to inefficient patterns like `.*` instead of more specific character classes like `[^"]*`.
2. **Catastrophic Backtracking**: They may generate patterns that cause exponential search times when faced with non-matching strings.
3. **Syntax Errors**: They sometimes confuse flavors (e.g., using Python-specific features in a PHP context).

**Verdict**: Use AI for inspiration, but always verify patterns using tools like `regex101.com` and perform code reviews for performance-critical expressions.

---

### Sources:
- [The Complete Guide to Regular Expressions (CoderPad)](https://coderpad.io/blog/development/the-complete-guide-to-regular-expressions-regex/)
- [RexEgg: The Best Regex Trick Ever](https://www.rexegg.com/regex-best-trick.html)
- [RexEgg: Regex Style Guide](https://www.rexegg.com/regex-style.php)
