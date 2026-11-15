# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased]

### Added
- Expanded section "12. Tools & Composer" with 16 new questions (Junior/Middle/Senior): SemVer constraints, `require` vs `require-dev`, `vendor` directory, autoload types and production optimization, committing `composer.lock`, scripts and plugins, platform requirements, dependency conflicts, `provide`/`replace`/`conflict`, dependency security, private packages and monorepos, Git basics (linked to the existing Git guide), Xdebug, Rector and PHP-CS-Fixer, profiling. Added the missing `composer install` vs `composer update` question to `QUESTIONS.md` and its Russian translation to match the Docusaurus docs.
- Comprehensive guide for PHP 8.5 new features in both English and Russian.
- Detailed information about the new URI extension (RFC 3986 & WHATWG) in PHP 8.5.
- Documentation for the "Clone With" syntax introduced in PHP 8.5.
- New section "2. Sessions in PHP" in `QUESTIONS.md` and Docusaurus documentation.

### Changed
- **Renumbered all documentation sections** starting from section 2 to accommodate the new Sessions chapter.
- Updated PHP 8.5 Pipe Operator (`|>`) examples to use the correct first-class callable syntax (`(...)`).
- Moved `json_validate()` documentation from PHP 8.2 to the correct PHP 8.3 section.
- Synchronized all technical updates across English and Russian translations in root files and Docusaurus content.
- Updated `README.md` to reflect the new section numbering and contents.

### Fixed
- Corrected PHP namespaces for the new URI extension in PHP 8.5 technical guides.
- Fixed syntax errors in PHP 8.5 code examples in `QUESTIONS.md`.
