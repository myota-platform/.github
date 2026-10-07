# Python style and local checks

Python source in the platform, service, deployment, and contracts repositories
uses Ruff with a 79-column formatting target and Python 3.11 compatibility.
The shared GitHub Actions workflow runs on pushes and pull requests. Each
repository also has a pre-commit configuration for local commit and push checks.

## Install local hooks

From the root of any Python repository, run:

```sh
python3 -m pip install -r requirements-dev.txt
./scripts/install-quality-hooks.sh
```

The installed pre-commit hook runs Ruff formatting and lint checks before each
commit and push. CI runs the same checks, so bypassing local hooks does not
bypass the repository check.

To format files after a check fails:

```sh
python3 -m ruff format .
python3 -m ruff check --fix .
```

Ruff enforces core PEP 8 errors, import correctness, and undefined/unused
names. Formatting uses 79 columns. E501 is not independently enabled because
Ruff's formatter cannot safely split every semantic string literal, SQL
statement, or URL; those should still be wrapped or split into adjacent string
literals where doing so preserves clarity and exact content.

## Repositories covered

- `myota-platform`
- `myota-deploy`
- `myota-geodata-service`
- `myota-activity-service`
- `myota-identity-service`
- `myota-programme-service`
- `myota-contracts`
