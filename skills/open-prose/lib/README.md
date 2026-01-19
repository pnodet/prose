# OpenProse Standard Library

Core programs that ship with OpenProse. These are production-quality, well-tested programs for common tasks.

## Programs

| Program | Description |
|---------|-------------|
| `inspector.prose` | Post-run analysis for runtime fidelity and task effectiveness |
| `vm-improver.prose` | Analyzes inspections and proposes PRs to improve the VM |
| `program-improver.prose` | Analyzes inspections and proposes PRs to improve .prose source |

## The Improvement Loop

These programs form a recursive improvement cycle:

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   Run Program  ──►  Inspector  ──►  VM Improver ──► PR     │
│        ▲                │                                   │
│        │                ▼                                   │
│        │         Program Improver ──► PR                    │
│        │                │                                   │
│        └────────────────┘                                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

1. **Run any .prose program** — generates execution artifacts
2. **Inspector analyzes the run** — produces verdict, scores, findings
3. **VM Improver** reads inspection, proposes VM spec/implementation changes
4. **Program Improver** reads inspection, proposes source code changes
5. **PRs get reviewed and merged** — VM and programs improve
6. **Repeat** — next run benefits from improvements

## Usage

```bash
# Inspect a completed run
prose run lib/inspector.prose
# Inputs: run_path, depth (light|deep), target (vm|task|all)

# Propose VM improvements based on inspection
prose run lib/vm-improver.prose
# Inputs: inspection_path, prose_repo

# Propose program improvements based on inspection
prose run lib/program-improver.prose
# Inputs: inspection_path, run_path
```

## Compounding Intelligence

All three programs use `persist: user` agents to maintain state across projects:

- **Inspector's index agent** tracks all inspections ever run
- **VM Improver** (future) could track which VM issues have been fixed
- **Program Improver** (future) could track common anti-patterns

This means running these programs repeatedly builds institutional knowledge about what works and what doesn't.

## Design Principles

Programs in `lib/` follow these principles:

1. **Production-ready** — Tested, documented, handles edge cases
2. **Composable** — Can be imported via `use` in other programs
3. **User-scoped state** — Cross-project utilities use `persist: user`
4. **Minimal dependencies** — No external services required
5. **Clear contracts** — Well-defined inputs and outputs
6. **Incremental value** — Useful even in simple mode, more powerful with depth
