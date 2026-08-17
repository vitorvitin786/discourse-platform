# Contributing to Discourse Platform

Thank you for your interest in contributing! This document provides guidelines for contributing to the project.

## Code of Conduct

Be respectful, inclusive, and professional in all interactions.

## Getting Started

1. Fork the repository
2. Clone your fork: `git clone https://github.com/your-username/discourse-platform.git`
3. Create a feature branch: `git checkout -b feature/your-feature-name`
4. Set up your development environment: `npm install && docker-compose up -d`
5. Make your changes
6. Test your changes: `npm test`
7. Commit with clear messages: `git commit -m "feat: description of changes"`
8. Push to your fork: `git push origin feature/your-feature-name`
9. Create a Pull Request

## Commit Message Convention

Use conventional commits:
- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation
- `style:` Code style (formatting, missing semicolons)
- `refactor:` Code refactoring
- `perf:` Performance improvements
- `test:` Tests
- `chore:` Build, dependencies, tooling

Example: `feat: add message search functionality`

## Pull Request Process

1. Update documentation if needed
2. Add tests for new functionality
3. Ensure all tests pass: `npm test`
4. Ensure code is linted: `npm run lint`
5. Ensure types are correct: `npm run type-check`
6. Provide a clear description of changes in the PR
7. Link related issues

## Architecture Guidelines

- Keep components small and focused
- Use TypeScript for type safety
- Follow the existing project structure
- Prefer functional components
- Use React hooks appropriately
- Ensure components are accessible
- Test edge cases

## Performance Guidelines

- Minimize bundle size
- Use lazy loading for routes and components
- Optimize images and media
- Avoid unnecessary re-renders
- Use virtualization for long lists
- Implement efficient caching strategies
- Profile before optimizing

## Security Guidelines

- Never commit secrets or credentials
- Validate all user inputs
- Sanitize data before rendering
- Use HTTPS in production
- Follow OWASP guidelines
- Report security issues privately

## Questions?

Open an issue for discussion or reach out to the maintainers.
