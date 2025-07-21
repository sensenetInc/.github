name: Bug Report
description: Report a problem or unexpected behavior
title: "[Bug] "
labels: [bug]
body:
- type: markdown
  attributes:
  value: |
  Thanks for reporting a bug! Please provide the following info.

- type: input
  id: environment
  attributes:
  label: Environment
  description: Where did you see the issue? (browser, OS, system, etc.)
  placeholder: e.g., Chrome on macOS
  validations:
  required: true

- type: textarea
  id: steps
  attributes:
  label: Steps to Reproduce
  description: List the exact steps to reproduce the issue.
  validations:
  required: true

- type: textarea
  id: expected
  attributes:
  label: Expected Behavior
  description: What did you expect to happen?
  validations:
  required: true

- type: textarea
  id: actual
  attributes:
  label: Actual Behavior
  description: What actually happened?
  validations:
  required: true
