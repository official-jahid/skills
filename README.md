# Agent Skills

A collection of specialized skills for software development, organized by language and purpose. Each skill provides guidance, best practices, and implementation patterns for specific domains.

## Structure

```
skills/
├── typescript/         # TypeScript/JavaScript (15 skills)
├── python/             # Python (9 skills)
├── c/                  # C (3 skills)
├── cpp/                # C++ (4 skills)
├── csharp/             # C# (5 skills)
├── javascript/         # JavaScript (5 skills)
└── powershell/         # PowerShell (4 skills)
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
| `api-creation` | REST API patterns with Node.js and Express |
| `terminal-app` | CLI applications - argument parsing, progress bars |
| `website-creation` | Web applications with Next.js |

## Python Skills

| Skill | Description |
|-------|-------------|
| `flask-python` | Flask web application development patterns |
| `keyauth-integration` | KeyAuth authentication for license systems |
| `dll-injection` | Windows process DLL injection with pyinjector |
| `pyinstaller-build` | Building executables with PyInstaller |
| `discord-bot` | Discord bot development and workflows |
| `windows-memory-manipulation` | Windows memory scanning with PyMem |
| `api-creation` | REST API patterns with Flask and FastAPI |
| `process-memory` | Process memory manipulation - ReadProcessMemory, AOB scanning |
| `terminal-app` | CLI applications - argument parsing, progress bars, prompts |

## C Skills

| Skill | Description |
|-------|-------------|
| `memory-management` | Dynamic memory allocation, malloc/calloc/realloc/free patterns |
| `pointers` | Pointer arithmetic, function pointers, void pointers, safety patterns |
| `linux-app` | Linux application development - CLI tools, systemd services |

## C++ Skills

| Skill | Description |
|-------|-------------|
| `memory-management` | Smart pointers, RAII, move semantics, modern C++ memory safety |
| `process-memory` | ReadProcessMemory, WriteProcessMemory, AOB scanning |
| `windows-api` | Windows API patterns - kernel32, user32 functions, handles |
| `memory-monitor` | Performance counters, memory tracking, leak detection |

## C# Skills

| Skill | Description |
|-------|-------------|
| `inheritance` | Inheritance, polymorphism, abstract classes, interfaces |
| `async-await` | Async/await patterns, task-based async, cancellation tokens |
| `windows-desktop-app` | WinForms, WPF, MSI/EXE deployment |
| `local-auth` | Local authentication - user credentials, sessions, password storage |
| `cloud-auth` | Cloud authentication - OAuth2, JWT, refresh tokens, 2FA |

## JavaScript Skills

| Skill | Description |
|-------|-------------|
| `fetch-api` | HTTP requests, error handling, file upload with Fetch API |
| `async-patterns` | Promises, async/await, concurrency, retry patterns |
| `api-creation` | REST API patterns with Node.js and Express |
| `discord-bot` | Discord bot development with discord.js |
| `terminal-app` | CLI applications - argument parsing, output formatting |

## PowerShell Skills

| Skill | Description |
|-------|-------------|
| `scripting` | Cmdlets, modules, error handling, and automation scripts |
| `wrapper` | Wrappers for executables, COM objects, .NET assemblies |
| `batch-files` | Windows batch file patterns for cmd.exe environments |
| `automation` | File operations, registry changes, scheduled tasks |

## Kotlin Skills

| Skill | Description |
|-------|-------------|
| `android-app` | Android application patterns - activities, fragments, Room database |

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