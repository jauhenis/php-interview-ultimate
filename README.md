# This repository contains all the questions and the answers for this topics:
- 100 Must-Know PHP Interview Questions in 2026
- PHP Interview Questions (all level)
- Interview questions and answers

# PHP Interview Questions - Ultimate Answer Repository

[![Docs](https://img.shields.io/badge/docs-modern-blue.svg)](docs/)

This repository is a curated collection of PHP interview questions and answers, gathered from various high-quality sources. It aims to be the ultimate resource for developers preparing for PHP-related interviews.

## 🚀 Ready to get started? Visit the [Documentation Website](https://jauhenis.com/)
- A non-commercial website with a collection of PHP interview questions and answers from this repository.
- Open-source and free to use.

Study the [PHP 8.6 release guide](answers/php86_features.md) in English or [Russian](translate/ru/answers/php86_features.md).

## Question indexes

Browse all 29 topics in the [English question index](QUESTIONS.md) or the [Russian question index](translate/ru/QUESTIONS.md). Both collections use Junior, Middle, and Senior difficulty levels. The documentation site groups questions by topic and offers a header search for finding questions and detailed answers.

## Table of Contents
- [About](#about)
- [Source Repositories](#source-repositories)
- [Source Projects and Learning Web Sites](#source-projects-and-learning-web-sites)
- [Project Structure](#project-structure)
- [Development](#development)
- [Key Topics Covered](#key-topics-covered)

## About
This project consolidates different repositories into one place, making it easier to search and study various interview questions. It includes answers for both core PHP and popular frameworks like Laravel and Symfony, as well as related technologies such as MySQL, Redis, Docker, and more.

## Source Repositories
The content in this repository is sourced from the following GitHub projects:

- [glaphire/interview_questions_and_answers](https://github.com/glaphire/interview_questions_and_answers)
- [Sabbir-Hossain12/PHP-LARAVEL-Interview-questions](https://github.com/Sabbir-Hossain12/PHP-LARAVEL-Interview-questions)
- [Devinterview-io/php-interview-questions](https://github.com/Devinterview-io/php-interview-questions)
- [zsoro2/php-interview-questions](https://github.com/zsoro2/php-interview-questions)
- [doocs/leetcode](https://github.com/doocs/leetcode) - LeetCode solutions in various languages.

## Source Projects and Learning Web Sites
The content that was used to learn Design Patterns, Refactoring principles:
- [Refactoring Guru](https://refactoring.guru/)
- [Backend Roadmap](https://roadmap.sh/backend)
- [HaPHPiness](https://haphpiness.com/)
- [RexEgg](https://www.rexegg.com/)
- [Metanit](https://metanit.com/)
- Partially written and organized using [Gemini](https://gemini.google.com/), [Cursor](https://www.cursor.com/), and [JetBrains AI](https://www.jetbrains.com/ai/).

## Project Structure
The question indexes link to detailed guides in `answers/` and `translate/ru/answers/`. Docusaurus publishes their counterparts from `docs/docs/` and `docs/i18n/ru/`. The `src/` directory contains source material:

- `src/interview_questions_and_answers/`: Detailed questions and answers covering a wide range of topics from core PHP to architecture.
- `src/PHP-LARAVEL-Interview-questions/`: PHP and Laravel specific questions by Sabbir Hossain.
- `src/php-interview-questions/`: A collection of questions from Devinterview.io.
- `src/zsoro-php-interview-questions/`: PHP interview questions by zsoro.

## Development

Run the documentation commands from the repository root using the Compose Node service:

```bash
docker compose up -d node
docker compose exec -w /app node npm run install:docs
docker compose exec -w /app node npm run build
```

For a live preview, run `docker compose run --rm -p 3333:3000 -w /app node npm run dev` and open [localhost:3333](http://localhost:3333). To check or format the frontend, run `npm run lint` or `npm run format` in the same container from `/app`.

## Key Topics Covered
Examples from the 29 topics include:

- **Core PHP**: Data types, generators, magic constants, PHP features (8.0+), and more.
- **Kafka**: High-performance distributed streaming, EOS, and PHP implementation.
- **Regexp**: Comprehensive guide to regular expressions in PHP, including PCRE functions and common tasks.
- **HaPHPiness**: Modern PHP features and best things in PHP.
- **LeetCode Solutions**: Solutions to LeetCode challenges in PHP.
- **Frameworks**: Laravel, Symfony, and Doctrine.
- **Design Patterns & OOP**: SOLID principles, common design patterns, and OOP fundamentals.
- **Databases**: MySQL, PostgreSQL, Redis, Memcached, and ElasticSearch.
- **PostgreSQL**: Advanced features, JSONB, and differences from MySQL.
- **Architecture**: Microservices, highload, and system design.
- **DevOps & Tools**: Docker, Git, and deployment strategies.
- **Testing**: Unit, functional, and integration testing.
- **Clean Code**: Robert C. Martin's Clean Code principles.
- **Algorithms**: Basic and sorting algorithms in PHP.
- **SQL Literacy**: Advanced concurrency, locking strategies, and idempotency.
