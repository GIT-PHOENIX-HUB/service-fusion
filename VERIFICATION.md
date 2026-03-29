# VERIFICATION.md — service-fusion
**Triage date:** 2026-03-28
**Wave:** D-7
**Assessor:** Phoenix Echo

## Assessment
ServiceFusion plugin repo. Contains skills/, agents/, hooks/ (live MCP/skill code), and commands/ (plugin commands). Skills and agents relocated; commands and guide to delete.

## Skills/MCP Found
| Item | Content |
|------|---------|
| skills/servicefusion-operations/ | SKILL.md + references/ — SF operations skill definition |
| agents/sf-operations-agent.md | SF operations agent spec |
| hooks/hooks.json | Plugin hooks config |

## Triage Counts
| Folder | Files Copied |
|--------|-------------|
| archive_for_delete/ | 3 (commands/, PLUGIN_DEVELOPMENT_GUIDE.md, CODEOWNERS) |
| archive_for_review/ | 0 |
| scheduled_to_relocate/ | 3 (skills/, agents/, hooks/) |

## Relocation Target
`PHOENIX_UNIFIED_PROD` hub → `/plugins/service-fusion/` or `/skills/service-fusion/`
