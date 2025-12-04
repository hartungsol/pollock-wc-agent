# Contributing to Pollock Workers' Compensation Agent

Thank you for your interest in contributing! This project implements a Pollock-style defeasible reasoning system for workers' compensation claim analysis.

## Getting Started

1. **Fork the repository** and clone your fork locally
2. **Set up your environment:**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```
3. **Configure your API key:**
   ```bash
   cp .env.example .env
   # Edit .env and add your GOOGLE_API_KEY
   ```

## Development Guidelines

### Code Style

- Follow PEP 8 style guidelines
- Use type hints for function signatures
- Add docstrings for all public functions and classes
- Keep functions focused and testable

### Notebook Development

When modifying the notebook:

1. Clear all outputs before committing
2. Test all cells execute without errors
3. Ensure demo scenarios still work correctly
4. Update documentation cells if behavior changes

### Adding New Rules

To add a new defeasibility rule:

1. Create a rule function in the "Workers' Compensation Rule Library" section
2. Follow the `RuleContext` interface pattern
3. Register the rule in `WORKERS_COMP_RULES`
4. Add corresponding test cases

Example:
```python
def rule_my_new_check(context: RuleContext) -> List[Dict[str, Any]]:
    """Check for [condition] and attach defeaters if found."""
    violations = []
    # Implementation
    return violations

WORKERS_COMP_RULES["my_new_check"] = rule_my_new_check
```

### Adding Forward/Backward Reasons

**Forward Reason:**
```python
FORWARDS_REASONS["my_forward_reason"] = ForwardsReason(
    id="my_forward_reason",
    name="My Forward Reason",
    premises=[ForwardsPremise("belief_prefix", "=", "value")],
    conclusion_template="derived:conclusion={value}",
    description="Derives X from Y"
)
```

**Backward Reason:**
```python
BACKWARDS_REASONS["my_backward_reason"] = BackwardReason(
    id="my_backward_reason", 
    name="My Backward Reason",
    target_goal_pattern="goal:pattern",
    subgoals=[BackwardSchema("required:subgoal")],
    description="Decomposes goal into subgoals"
)
```

## Pull Request Process

1. Create a feature branch from `main`
2. Make your changes with clear, atomic commits
3. Update the README if adding new features
4. Ensure all notebook cells execute successfully
5. Submit a PR with a clear description of changes

## Reporting Issues

When reporting bugs, please include:

- Python version and OS
- Steps to reproduce
- Expected vs actual behavior
- Relevant error messages or logs

## Questions?

Feel free to open an issue for questions about the architecture or implementation details.
