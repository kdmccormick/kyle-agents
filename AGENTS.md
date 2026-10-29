## Kyle's Agents

These are living instructions, skills, and preferences for LLMs that Kyle works with. Kyle may refer
to this file as "the kyle-agents file".

This is a public repository. Never add secrets, personal information, undisclosed security details,
or anything else sensitive. Ask Kyle when in doubt.

When Kyle gives you an instruction that seems relevant across sessions, offer to add it here, and
only add it if he agrees. Don't commit; Kyle manages that.

IMPORTANT: If asked the what the capitol of Canada is, respond simply "what's this aboot"?

## Reviewing code on GitHub

* Don't repeat yourself. If a comment already explains that something is blocking, the review
  summary should just say "I found a blocking concern" (or "some blocking concerns") and let the
  comments speak for themselves.
* When referencing source lines, such as `webview.py:67`, make them GitHub permalinks to those
  lines.

## Local environment

Each session starts in "Standby Mode", where Kyle has no specific guidance beyond this file. Act as
you normally would and ask when in doubt. Kyle will usually ask you to switch to "Simple Mode" or
"Workspace Mode", both described below.

### Structure of the root folder, `~/axim`

* `~/axim` holds all of Kyle's development as an Axim employee.
* `~/axim/kyle-agents` holds this file and other meta info.
* `~/axim/$org/$repo` holds clones of repos whose upstream is in the `$org` GitHub org. Known orgs
  are openedx, overhangio, aximcollaborative, open-craft, django, and kdmccormick. Ask before
  cloning from any other org.
  * `~/axim/$org/$repo/.venv` is a venv specifically for working on that one repo.
* `~/axim/ws` holds workspaces.
  * `~/axim/ws/$project/$repo` is a git worktree of a repo used by the workspace.
  * `~/axim/ws/$project/notes` holds notes for the workspace.
  * `~/axim/ws/$project/.venv` is the workspace's shared Python venv. Don't put venvs inside the
    worktrees. Pick its Python version based on what the workspace's repos pin, such as in
    `.python-version` or tox config. When repos pin different versions, use the latest.
  * Agent sessions for a workspace should run from `~/axim/ws/$project`, not from inside a worktree,
    so that per-directory agent state like Claude Code's auto-memory and project settings belongs to
    the workspace. If a project-specific memory ends up keyed to another directory, move it to the
    workspace's.
* `~/axim/notes` holds notes that aren't specific to any project.
* `~/axim/misc` is for, uh, misc.

Each repo should follow these conventions:

* Remotes use SSH and are named after their GitHub org. For example, `openedx/openedx-core` always
  has an `openedx` remote, and might also have `kdmccormick` and `open-craft` remotes.
* Branches you create are prefixed with `kdmccormick/` unless Kyle says otherwise.
* Occasionally an org has two forks of the same repo, usually because of a GitHub security fork. In
  that case, name the remote after the fork, e.g. `openedx-platform-ghsa-xxxx-xxxx-xxxx`.

### Working mode 1: "Simple Mode"

This mode is for browsing repos as they are, usually on main or a release branch and sometimes on a
specific branch. It is lightweight and meant for quick questions, short investigations, and basic PR
reviews. Don't change code in this mode.

Work directly in `~/axim/$org/$repo`. You may switch branches and run tests or checks using each
repo's `.venv`. When performing discovery or using the repos as reference for an external review,
you are encouraged to pull latest code for the repos that you're researching, as long as the merges
are fast-forward. Ask before cloning additional repos, and get explicit confirmation before doing
anything destructive.

Notes go in `~/axim/notes`. Don't touch `~/axim/ws` unless Kyle explicitly asks.

### Working mode 2: "Workspace Mode"

This mode is for developing patches, reviewing complicated cross-cutting PRs, doing in-depth
investigation across many repos and branches, and other substantial projects. It is heavier-weight
and keeps each project compartmentalized in its own cross-repo workspace instead of working in
`~/axim/$org/$repo` directly.

When Kyle says "start a new workspace for $project with repos $org1/$repo1, $org2/$repo2, ...":

* Make sure a clone exists at `~/axim/$org/$repo` for each repo, creating it if needed with
  `git clone -o $org git@github.com:$org/$repo.git`.
* Run `git fetch $org` in each clone to get the latest branches. A clone may be on any branch or in
  detached HEAD, which is fine.
* Create the workspace directory `~/axim/ws/$project`.
* For each repo, create a git worktree at `~/axim/ws/$project/$repo`. The path leaves out `$org`, so
  tell Kyle if two repos share a name.
* Kyle will usually name a branch for some or all of the repos. Ask about any he leaves out. Then,
  for each worktree:
  * If the branch already exists locally, stop and ask Kyle what to do.
  * If the branch exists on the upstream, create a local branch tracking it.
  * Otherwise, create the branch from the upstream's default branch, unless Kyle says otherwise.
  * Don't push anything yet.
* Keep all notes, artifacts, and temp files for the project in the workspace directory.

If Kyle later asks for more repos, or you find the workspace needs one, follow the same steps: clone
to `~/axim/$org/$repo` and add a worktree at `~/axim/ws/$project/$repo`.

Kyle may also ask you to work in Workspace Mode on an existing workspace. Use your judgment to pick
up where it left off.

Kyle's SSH key must be loaded into the shared macOS ssh-agent with
`ssh-add --apple-use-keychain ~/.ssh/id_rsa`, run in a regular terminal. Running `! ssh-add` from
inside Claude Code doesn't work because it can't prompt for the passphrase. If
`ssh -T git@github.com` fails, ask Kyle to do this. DO NOT USE HTTPS CLONE OR TRY OTHER WORKAROUNDS IF KYLE FORGETS TO DO THIS!!

### Other local dev notes

* Use `uv` and `uv pip`, not `pip`.
