# Dependency Checks

## Declared versions

`app/requirements.txt` directly pins all three declared packages:

```text
Flask==3.0.2
gunicorn==22.0.0
pytest==8.0.2
```

The architecture document expects Python 3.11, Flask 3.0.2, and Gunicorn 22.0.0. The audit machine was Python 3.14.0, so runtime compatibility on the target Python 3.11 image remains to be confirmed by CI/container execution.

## Reproducibility evidence

In a clean temporary virtual environment, installation from `app/requirements.txt` completed and:

```text
2 passed in 2.40s
No broken requirements found.
```

Resolved packages included `Flask==3.0.2`, `gunicorn==22.0.0`, `pytest==8.0.2`, `Werkzeug==3.1.9`, `Jinja2==3.1.6`, `itsdangerous==2.2.0`, `blinker==1.9.0`, and `MarkupSafe==3.0.4`. Transitive requirements use ranges and are not lockfile- or hash-pinned.

## Vulnerability evidence

Command: `python -m pip_audit -r app/requirements.txt --progress-spinner off`

Result:

```text
Found 4 known vulnerabilities in 2 packages
flask  3.0.2  PYSEC-2026-2151  Fix: 3.1.3
flask  3.0.2  PYSEC-2026-2151  Fix: 3.1.3
pytest  8.0.2  PYSEC-2026-1845  Fix: 9.0.3
pytest  8.0.2  PYSEC-2026-1845  Fix: 9.0.3
```

The duplicate advisory lines are the tool's output for the affected package records; they are two advisory records for two packages, not four distinct packages.

## Required action

Do not promote this candidate. The owning team must assess and upgrade Flask to at least 3.1.3 and pytest to at least 9.0.3 (or document an approved exception), regenerate a fully resolved, hash-pinned lock set, rerun tests on Python 3.11, and rerun `pip-audit` with zero findings or an approved exception.
