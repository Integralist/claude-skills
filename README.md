# Claude Code Skills

This repository contains a collection of **global Claude Code Skills** designed to be used across all projects when working with Claude AI.

## What are Claude Code Skills?

Claude Code Skills are custom code capabilities that extend Claude's ability to perform specific tasks, follow particular patterns, or apply specialized knowledge when writing, reviewing, or refactoring code. These skills act as reusable instructions that help Claude understand your preferences, coding standards, and project-specific requirements.

Code Skills can include:
- **Coding conventions** and style preferences
- **Architecture patterns** and best practices
- **Testing strategies** and patterns
- **Documentation standards**
- **Security guidelines** and common vulnerability patterns
- **Framework-specific** patterns and idioms
- **Domain-specific** knowledge and approaches

## Global Skills

The skills in this repository are intended to be **global** — meaning they apply across all projects rather than being specific to a single codebase. These are universal patterns, conventions, and best practices that you want Claude to follow consistently regardless of which project you're working on.

Examples of global skills include:
- Preferring functional programming patterns
- Always including error handling
- Writing comprehensive tests with specific patterns
- Following specific documentation formats (JSDoc, docstrings, etc.)
- Applying security best practices consistently
- Using particular naming conventions

## How to Use These Skills

To use these skills with Claude:

1. Reference the appropriate skills from this repository when starting a conversation with Claude
2. Include the skill descriptions in your Claude project settings or system prompts
3. Ask Claude to apply specific skills when working on particular tasks

## Structure

Skills should be organized by category or domain to make them easy to discover and apply:

```
skills/
├── coding-standards/
├── testing/
├── security/
├── documentation/
└── architecture/
```

## Contributing

When adding new skills to this repository:

1. Ensure the skill is truly **global** and applicable across multiple projects
2. Write clear, concise descriptions that Claude can easily understand
3. Include examples where helpful
4. Avoid project-specific details or configurations
5. Test the skill with Claude to ensure it produces the desired behavior

## License

This repository contains personal coding skills and preferences. Feel free to use and adapt these skills for your own projects.
