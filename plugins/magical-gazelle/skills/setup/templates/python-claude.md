# Project Name

## Stack

- Python 3.13+
- uv for package management, project setup, and running commands
- pytest for testing
- ruff for linting and formatting (200-char line length)

## Code Standards

### Style

- Modern Python 3.10+ syntax: `list[str]` not `List[str]`, `str | None` not `Optional[str]`
- Type hint parameters when the type isn't obvious from the name
- Type hint return values only when non-obvious (skip `-> str` on `__str__`, skip `-> bool` on `is_active`)
- Always type hint dataclass fields
- f-strings for string formatting in code, but `%s` placeholders in logging calls
- `pathlib` over `os.path`
- Frozen dataclasses for data transfer objects — no plain dicts for structured data
- Use keyword-only arguments (`*`) for functions with 3+ parameters
- Explicit is better than implicit. If it's not obvious, name it clearly.

### Structure

- Organize by domain/feature, not by layer type
- One view/router file per model or domain concept
- Business logic belongs in service classes, not in models or views
- Keep modules small and focused — one concept per file
- Use `__init__.py` exports to define public API of each package

### Models

- All models inherit from a base class with UUID primary key, timestamps, and user tracking
- Choices use `TextChoices` / `IntegerChoices` as nested classes
- Explicit `on_delete` behavior on all ForeignKeys (PROTECT, CASCADE, SET_NULL)
- Type hints on custom methods only when the return type is non-obvious
- Validate with `full_clean()` before save in service logic

### Error Handling

- Custom exception hierarchies with error codes for structured error responses
- Include context (IDs, names) and fix hints in exceptions where possible
- Services accumulate errors rather than raising immediately when processing batches
- Use specific exception types, not bare `except Exception`
- Validate at system boundaries (user input, external APIs), trust internal code

### Logging

- Module-level logger: `logger = logging.getLogger(__name__)`
- Use `%s` parametrized messages, NOT f-strings: `logger.info("Processing %s", obj.id)`
- Include IDs, counts, and context in log messages

### Dependencies

- Use `uv add` to add dependencies and `uv remove` to remove them
- Dependencies are managed in `pyproject.toml` via uv
- Prefer stdlib solutions over adding dependencies for simple tasks

## Testing

### Commands

```bash
uv run pytest             # run all tests
uv run pytest tests/unit/ # run unit tests only
uv run pytest -x          # stop on first failure
uv run ruff check .       # lint
uv run ruff format .      # format
```

### Conventions

- Use pytest (never unittest)
- Class-based test organization — one test class per model or feature
- Use Model Bakery (`baker.make()`) for test fixtures, not manual object creation
- Use `conftest.py` for shared fixtures with factory patterns
- Use `@pytest.mark.parametrize` for testing multiple inputs
- Test model validation with `full_clean()` and `pytest.raises(ValidationError)`
- Test observable behavior, not implementation details
- Mock only at system boundaries (external APIs, file I/O) — use real database in tests
- Tests ship with features — same commit, not deferred
- Security fixes ALWAYS include a regression test
- Bug fixes include a test that would have caught the bug
- Split tests into `unit/` and `integration/` for larger apps

## Git

- Conventional commits: `feat(scope): ...`, `fix(scope): ...`, `refactor: ...`
- One logical change per commit
- Keep PRs focused — one feature or fix per PR
