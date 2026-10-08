# Agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate the custom agent team building Mona's Project Pulse dashboard.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 (copilot) | Breaks work into phases, assigns explicit file scopes, coordinates the specialists, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 4.7 (copilot) | Researches the repository and relevant documentation, then produces implementation steps, dependencies, edge cases, validation expectations, and file assignments. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements the assigned application logic with explicit errors and testable behavior; can also create the Project Pulse launch configuration when assigned. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.1 Pro (copilot) | Designs the dashboard's UI/UX, accessibility, information hierarchy, responsive behavior, and polished visual treatment. | `.github/agents/designer.agent.md` |

The workflow begins with the Planner, then the Orchestrator schedules Coder and Designer work sequentially or in parallel according to file ownership and dependencies. All agents leave staging, commits, and pushes under learner control.
