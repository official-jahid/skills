# Agent Skills

A collection of specialized skills for software development, organized by language and purpose. Each skill provides guidance, best practices, and implementation patterns for specific domains.

## Structure

```
skills/
├── typescript/         # TypeScript/JavaScript skills (12 skills)
├── python/             # Python skills (6 skills)
├── c/                  # C skills (2 skills)
├── cpp/                # C++ skills (1 skill)
├── csharp/             # C# skills (2 skills)
└── javascript/         # JavaScript skills (2 skills)
```

## TypeScript Skills

| Skill | Description |
|-------|-------------|
| `better-auth-best-practices` | Better Auth server and client setup, database adapters, sessions, plugins |
| `better-auth-security-best-practices` | Rate limiting, CSRF protection, secure cookies, OAuth security |
| `create-auth-skill` | Authentication scaffolding workflow for TS/JS apps |
| `email-and-password-best-practices` | Email verification, password reset, password policies |
| `two-factor-authentication-best-practices` | TOTP, OTP, backup codes, 2FA flows |
| `prisma-cli` | Prisma ORM CLI commands reference |
| `prisma-client-api` | Prisma Client query and CRUD operations |
| `prisma-database-setup` | Database provider configuration guides |
| `prisma-upgrade-v7` | Migration from Prisma v6 to v7 |
| `shadcn` | shadcn/ui component management and rules |
| `react-hook-form-zod` | React form validation with Zod schemas |
| `find-skills` | Discovering and installing agent skills |

## Python Skills

| Skill | Description |
|-------|-------------|
| `flask-python` | Flask web application development patterns |
| `keyauth-integration` | KeyAuth authentication for license systems |
| `dll-injection` | Windows process DLL injection with pyinjector |
| `pyinstaller-build` | Building executables with PyInstaller |
| `discord-bot` | Discord bot development and workflows |
| `windows-memory-manipulation` | Windows memory scanning with PyMem |

## C Skills

| Skill | Description |
|-------|-------------|
| `memory-management` | Dynamic memory allocation, malloc/calloc/realloc/free patterns |
| `pointers` | Pointer arithmetic, function pointers, void pointers, safety patterns |

## C++ Skills

| Skill | Description |
|-------|-------------|
| `memory-management` | Smart pointers, RAII, move semantics, modern C++ memory safety |

## C# Skills

| Skill | Description |
|-------|-------------|
| `inheritance` | Inheritance, polymorphism, abstract classes, interfaces |
| `async-await` | Async/await patterns, task-based async, cancellation tokens |

## JavaScript Skills

| Skill | Description |
|-------|-------------|
| `fetch-api` | HTTP requests, error handling, file upload with Fetch API |
| `async-patterns` | Promises, async/await, concurrency, retry patterns |

## Usage

Skills are designed to be referenced by agent systems. Each skill follows a standard format:

- **Frontmatter** (YAML) with metadata: `name`, `description`, `license`, `metadata.version`
- **Content** with sections for quick start, patterns, best practices, and examples

## Publishing to skills.sh

To publish skills to the skills.sh registry:

1. Ensure each skill has proper frontmatter with `name`, `description`, and `license`
2. Use `npx skills init` to initialize the skill structure
3. Run `npx skills add . -g` to publish globally
4. Each skill folder should contain a `SKILL.md` file

## License

MIT - See [LICENSE](LICENSE) for details.