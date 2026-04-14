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

## Modern Frontend

Check out the new **[Modular Interview Questions Documentation](docs/)**!
- **Modularized**: Each question is an independent module.
- **Categorized**: Junior, Middle, Senior, and Expert levels.
- **Themed**: Beautiful, readable UI with Tailwind CSS.
- **Searchable**: Easy navigation through topics.

## Table of Contents
- [About](#about)
- [Source Repositories](#source-repositories)
- [Source Projects and Learning Web Sites](#source-projects-and-learning-web-sites)
- [Project Structure](#project-structure)
- [Key Topics Covered](#key-topics-covered)

## About
This project consolidates different repositories into one place, making it easier to search and study various interview questions. It includes answers for both core PHP and popular frameworks like Laravel and Symfony, as well as related technologies such as MySQL, Redis, Docker, and more.

## Source Repositories
The content in this repository is sourced from the following GitHub projects:

- [glaphire/interview_questions_and_answers](https://github.com/glaphire/interview_questions_and_answers)
- [Sabbir-Hossain12/PHP-LARAVEL-Interview-questions](https://github.com/Sabbir-Hossain12/PHP-LARAVEL-Interview-questions)
- [Devinterview-io/php-interview-questions](https://github.com/Devinterview-io/php-interview-questions)
- [zsoro2/php-interview-questions](https://github.com/zsoro2/php-interview-questions)

## Source Projects and Learning Web Sites
The content that was used to learn Design Patterns, Refactoring principles:
- [Refactoring Guru](https://refactoring.guru/)
- [Backend Roadmap](https://roadmap.sh/backend)
- [HaPHPiness](https://haphpiness.com/)
- [Metanit](https://metanit.com/)
- Partially written and organized using [Gemini](https://gemini.google.com/), [Cursor](https://www.cursor.com/), and [JetBrains AI](https://www.jetbrains.com/ai/).

## Project Structure
The repository is organized into subdirectories under `src/`, each representing a source repository:

- `src/interview_questions_and_answers/`: Detailed questions and answers covering a wide range of topics from core PHP to architecture.
- `src/PHP-LARAVEL-Interview-questions/`: PHP and Laravel specific questions by Sabbir Hossain.
- `src/php-interview-questions/`: A collection of questions from Devinterview.io.
- `src/zsoro-php-interview-questions/`: PHP interview questions by zsoro.

## Development

### Frontend Development (Docusaurus)
To run the frontend locally from the root:
1. `npm run install:docs`
2. `npm run dev`

Alternatively, from the `docs` directory:
1. `cd docs`
2. `npm install`
3. `npm start` or `npm run dev`

To lint and format the codebase:
From the root: `npm run lint` or `npm run format`
From the `docs` directory: `npm run lint` or `npm run format`

### Docker
Alternatively, you can build and run the frontend using Docker:
```bash
docker compose up --build
```
The documentation will be available at `http://localhost:8888` (served via Nginx) or `http://localhost:3333` (served via Node).

To run the frontend in **development mode** with hot-reloading:
```bash
docker compose run --service-ports node npm run dev
```
The documentation will be available at `http://localhost:3333`.

## Key Topics Covered
- The documentation is now organized into 24 major categories, including:
- **Core PHP**: Data types, generators, magic constants, PHP features (8.0+), and more.
- **HaPHPiness**: Modern PHP features and best things in PHP.
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
