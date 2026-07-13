Task Specification
Task Metadata
Task Name

Broken Python Build System Recovery with Dependency Lock Repair

Difficulty

Hard

Language

Python

Category

Software Engineering / Build System Debugging / Dependency Management / CI Repair

1. User Instruction Design
instruction.md Concept

The user-facing task instruction should describe a realistic engineering incident.

The instruction must simulate a production debugging request from a software team.

The agent should receive only the problem description and repository access.

The instruction must NOT reveal:

broken files,
expected fixes,
dependency conflicts,
hidden tests,
oracle behavior,
exact commands required.
Proposed instruction.md Content
You are an engineer responsible for restoring a broken Python project.

A previously working internal Python service has stopped building after a recent dependency maintenance cycle.

The repository currently fails during installation, Docker image creation, and CI execution.

Your goal is to investigate the repository, identify the causes of the failures, and restore the project so that it can:

- build successfully inside Docker,
- install as a Python package,
- run the existing test suite,
- work reliably in a clean environment.

Do not remove tests or bypass failures.

Make only the necessary repository changes required to restore a stable production-ready build.

Validate your changes before finishing.
2. Environment Specification
Docker Requirements

The task environment must be fully isolated and reproducible.

The evaluator builds the environment using Docker.

Required base image:

ubuntu:22.04
Operating System

Ubuntu:

22.04 LTS
Installed System Packages

The Docker environment must contain:

python3.11
python3-pip
python3-venv
git
gcc
make
curl
grep
sed
find
tree

Additional build utilities:

build-essential
Python Environment

Python version:

3.11.x

Installed tools:

pip
pytest
build
pip-tools
setuptools
wheel
3. Initial Repository State

The starting repository represents a production Python package after an incomplete dependency migration.

Repository layout:

project/

├── pyproject.toml
├── requirements.txt
├── requirements-dev.txt
├── Dockerfile
├── Makefile
│
├── src/
│   └── application/
│       ├── main.py
│       ├── config.py
│       └── utils.py
│
├── tests/
│   ├── test_core.py
│   └── test_config.py
│
├── scripts/
│   └── build.sh
│
└── .github/
    └── workflows/
        └── ci.yml
4. Injected Repository Problems

The repository intentionally contains multiple realistic engineering failures.

Problem 1: Dependency Resolution Failure

The dependency files contain incompatible constraints.

Example:

package-a requires urllib3<2

package-b requires urllib3>=2

Expected symptom:

ERROR: ResolutionImpossible

The conflict should require investigation rather than simple upgrading.

Problem 2: Invalid Packaging Configuration

The project metadata contains outdated or incompatible build configuration.

Possible symptoms:

ERROR: Could not build wheels

or:

Backend unavailable

The agent must repair packaging metadata.

Affected file:

pyproject.toml
Problem 3: Docker Build Failure

The Dockerfile contains realistic production mistakes:

Examples:

wrong Python base image,
incorrect dependency installation order,
missing operating-system build dependencies,
invalid working directory assumptions.

The final Docker build must work from a clean checkout.

Problem 4: CI Configuration Drift

GitHub Actions configuration contains outdated assumptions.

Examples:

removed environment variable,
incompatible Python version,
invalid cache configuration.

Affected file:

.github/workflows/ci.yml
5. Agent Goal

The agent succeeds when the repository is restored to a production-ready state.

Success criteria:

Build Success

Command:

docker build .

Expected:

exits with code 0,
creates a valid image,
no dependency resolver errors,
no compilation failures.
Installation Success

Command:

pip install .

Expected:

package installs successfully,
metadata is valid,
dependencies resolve correctly.
Test Success

Command:

pytest

Expected:

All tests pass.

Reproducibility

A clean evaluator must reproduce the same result.

The solution must not depend on:

manually installed packages,
developer machine state,
cached dependencies,
hidden environment variables,
absolute paths.
6. Evaluation Specification
Public Evaluation

Public tests verify basic recovery.

Test 1: Docker Build

Execution:

docker build .

Validation:

successful exit code,
image creation succeeds.

Failure:

Any build error.

Test 2: Package Installation

Execution:

pip install .

Validation:

package installs,
dependencies resolve.

Failure:

installation error,
missing metadata,
dependency conflict.
Test 3: Existing Tests

Execution:

pytest tests/

Validation:

All visible tests pass.

Failure:

Any failing test.

Hidden Evaluation

Hidden tests verify that the repair is correct rather than superficial.

Hidden Test 1: Clean Installation

Procedure:

Create a fresh environment:

docker run clean-python-image
pip install .

Checks:

no undeclared dependencies,
no machine-specific requirements.

Failure:

Installation only works in original container.

Hidden Test 2: Dependency Stability

Procedure:

pip freeze

Validation:

Resolved versions satisfy expected constraints.

Failure:

Uncontrolled dependency upgrades.

Hidden Test 3: Regression Validation

Execute:

pytest hidden_tests/

Checks:

application behavior,
configuration loading,
package imports,
CLI execution.

Failure:

Functional regression.

Hidden Test 4: Docker Reproducibility

Build twice:

docker build .
docker build .

Validation:

Both builds succeed.

Failure:

Environment-dependent behavior.

7. Failure Conditions

The following solutions are considered incorrect.

Failure 1: Blind Dependency Upgrade

Example:

pip install --upgrade everything

Reason:

Creates unstable dependency state.

Failure 2: Local Environment Fix Only

Example:

Installing missing packages manually without modifying repository files.

Reason:

Evaluator starts from clean environment.

Failure 3: Removing or Modifying Tests

Reason:

Tests represent required behavior.

Failure 4: Hardcoded Paths

Example:

/home/user/project

Reason:

Fails in evaluator environment.

Failure 5: Partial Repair

Examples:

Docker fixed but package installation broken.
Dependencies fixed but CI broken.
Local tests pass but clean environment fails.
8. Oracle Solution Specification

The official solution should represent an experienced engineer's minimal repair.

Oracle Workflow
Step 1: Inspect Repository

Engineer investigates:

tree

cat pyproject.toml

cat requirements.txt

cat Dockerfile

cat .github/workflows/ci.yml
Step 2: Reproduce Failures

Engineer executes:

docker build .

pip install .

pytest
Step 3: Diagnose Root Causes

Engineer identifies:

dependency incompatibilities,
invalid package metadata,
Docker environment problems,
CI drift.
Step 4: Apply Minimal Changes

Expected modified files:

pyproject.toml

requirements.txt

Dockerfile

.github/workflows/ci.yml

Changes should:

establish compatible dependency versions,
restore package build configuration,
fix Docker build sequence,
update CI assumptions.
Step 5: Validate

Engineer confirms:

docker build .

pip install .

pytest

All succeed.

9. Acceptance Requirements

The task is accepted only when:

Repository Requirements

✓ Docker build succeeds

✓ Package installation succeeds

✓ Existing tests pass

✓ Hidden tests pass

✓ No test removal

✓ No hardcoded environment dependency

✓ Fixes are reproducible

Benchmark Quality Requirements

The task must demonstrate:

Real Engineering Scenario

The problem represents common production failures:

dependency migrations,
packaging failures,
CI drift,
Docker issues.
Agent Capability Measurement

The task evaluates:

✓ repository exploration

✓ terminal reasoning

✓ debugging ability

✓ dependency analysis

✓ multi-file modification

✓ validation discipline

Deterministic Evaluation

The benchmark must provide:

✓ isolated Docker environment

✓ deterministic tests

✓ hidden regression checks

✓ reference implementation

10. Implementation Notes for Task Builder

The task creator must ensure:

The failures are realistic.
The solution cannot be found by changing one obvious version number.
Multiple files require investigation.
Hidden tests validate true repair.
The oracle patch remains small and maintainable.
Final Task Classification

Difficulty:

Hard

Expected solving skills:

Python packaging expertise
Docker debugging
dependency resolution
CI troubleshooting
terminal-based investigation

This task is suitable for Terminal-Bench style evaluation because success requires autonomous engineering investigation rather than simple code generation.