# Changelog

All notable changes to think-discover-define are documented here.

## [2.2.0] - 2026-04-27

### Changed
- Reorganized monolithic SKILL.md into three phase-specific reference files: `01-SPEC.md`, `02-PLAN.md`, `03-TEST.md`
- Simplified skill description to reflect manual invocation pattern (`/think-discover-define`)
- Updated pipeline from 5-phase (ORIENT → DISCOVER → SPECIFY → PLAN → TEST) to 3-phase (01-SPEC → 02-PLAN → 03-TEST)
- Added `Glob` and `Grep` to `allowed-tools` for improved document discovery during the spec phase
- Simplified philosophy section; consolidated redundant guidance into phase files

### Fixed
- Discovery phase now instructs Claude to propose recommended answers based on codebase analysis before asking the developer

## [1.0.2] - initial release

- Initial plugin marketplace release
