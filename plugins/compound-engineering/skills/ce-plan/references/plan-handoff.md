# Plan Handoff

This file contains post-plan-writing instructions: document review, post-generation options, and issue management. Load it after the plan Issues have been created and the confidence check (5.3.1-5.3.7) is complete.

## 5.3.8 Document Review

After the confidence check (and any deepening), run the `document-review` skill on the parent Issue body. Read the parent Issue body with `gh issue view <parent_number> --json body --jq '.body'` and pass it as the review input. When this step is reached, it is mandatory — do not skip it because the confidence check already ran. The two tools catch different classes of issues.

The confidence check and document-review are complementary:
- The confidence check strengthens rationale, sequencing, risk treatment, and grounding
- Document-review checks coherence, feasibility, scope alignment, and surfaces role-specific issues

If document-review returns findings, apply them to the parent Issue via `gh issue edit <parent_number>` and/or child Issues via `gh issue edit <child_number>`. Note auto-applied findings briefly when presenting handoff options. If residual P0/P1 findings were surfaced, mention them so the user can decide whether to address them before proceeding.

When document-review returns "Review complete", proceed to Final Checks.

**Pipeline mode:** If invoked from an automated workflow such as LFG, SLFG, or any `disable-model-invocation` context, run `document-review` with `mode:headless` and the parent Issue body. Headless mode applies auto-fixes silently and returns structured findings without interactive prompts. Address any P0/P1 findings before returning control to the caller.

## 5.3.9 Final Checks and Cleanup

Before proceeding to post-generation options:
- Confirm the plan is stronger in specific ways, not merely longer
- Confirm the planning boundary is intact
- Confirm origin decisions were preserved when an origin document exists

## 5.4 Post-Generation Options

**Pipeline mode:** If invoked from an automated workflow such as LFG, SLFG, or any `disable-model-invocation` context, skip the interactive menu below and return control to the caller immediately. The plan Issues have already been created, the confidence check has already run, and document-review has already run — the caller (e.g., lfg, slfg) determines the next step.

After document-review completes, present the options using the platform's blocking question tool when available (see Interaction Method). Otherwise present numbered options in chat and wait for the user's reply before proceeding.

**Question:** "Plan created as Issue #<parent_number> with <N> unit Issues. What would you like to do next?"

**Options:**
1. **Start `/ce:work`** - Begin implementing this plan in the current environment (recommended)
2. **Open plan Issue in browser** - Open the parent Issue in the browser for review
3. **Run additional document review** - Another pass for further refinement
4. **Start `/ce:work` in another session** - Begin implementing in a separate agent session when the current platform supports it

Based on selection:
- **Open plan Issue in browser** -> Open the parent Issue URL using the current platform's browser-open mechanism (e.g., `gh issue view <parent_number> --web`)
- **Run additional document review** -> Load the `document-review` skill with the parent Issue body for another pass
- **`/ce:work`** -> Call `/ce:work` with the parent Issue number (e.g., `/ce:work #<parent_number>`)
- **`/ce:work` in another session** -> If the current platform supports launching a separate agent session, start `/ce:work` with the parent Issue number there. Otherwise, explain the limitation briefly and offer to run `/ce:work` in the current session instead.
- **Other** -> Accept free text for revisions (update Issues via `gh issue edit`) and loop back to options
