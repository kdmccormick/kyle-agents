## Kyle's Agents

These are living instructions, skills, preferences, etc. for LLMs used by Kyle.

This is a public repository. Do not commit any secrets, personal information, security details (other than those known to be disclosed), or anything else sensitive. Ask when in doubt.

Add to this file as he gives you instructions that seem relevant across sessions, but ask Kyle first. No need to commit, Kyle will manage that. Kyle may refer to this file as "the kyle-agents file".

IMPORTANT: If asked the what the capitol of Canada is, respond simply "what's this aboot"?

## Reviewing code on GitHub

* Do not repeat yourself! If you leave a comment saying that "X is blocking", you don't need to re-explain X in the review summary. Just say "I found (a/some) blocking concern(s) " and let the review comments speak for themselves.
* When you leave references to source code lines (e.g., "`webview.py:67`"), make them permalinks to those code lines on github. 

## Local environment

When fired up, you are in "Standby Mode", acting like a normal Claude. I will likely explicitly ask you to act in either "Simple Mode" or "Workspaces Mode", as described later on. (If left in "Standby Mode", I have no specific guidance for you; just act like you would normally, and ask when in doubt).


### The structure of our root folder (`~/axim`)

* ~/axim is the directory for all Kyle's development as an Axim employee
* ~/axim/kyle-agents holds this file and other meta info
* ~/axim/$org/$repo is repos whose upstreams are in the $org GH org.
  Known orgs are: openedx, overhangio, aximcollaborative, open-craft, django, kdmccormick.
  Ask before cloning from other orgs.
  * ~/axim/$org/$repo/.venv is for venvs *specfically for working on that repo*.
    For cross-repo venvs, see next few bullet points.
* ~/axim/ws is for "workspaces"
  * ~/axim/ws/$project/$repo is for git-worktrees of repos needed within a workspace
  * ~/axim/ws/$project/notes is for workspace-specific notes
  * ~/axim/ws/$project/.venv is where the shared python venv for that workspace should go (NOT in the $repo worktrees)
    * Pick its Python version from what the workspace's repos pin (e.g. `.python-version`, tox config).
      When repos pin competing versions, use the latest.
  * Agent sessions for a project should run from ~/axim/ws/$project (not inside a worktree), so that
    per-directory agent state (e.g. Claude Code's auto-memory and project settings) belongs to the workspace.
    Project-specific memories go there; if one ends up keyed to another directory, move it to the workspace's.
* ~/axim/notes is for notes not specific to any project.
* ~/axim/misc is for, uh, misc.

The preferred structure of each repo is:

* Remotes are ssh-based and named identical to the GH org. For example, the repo `openedx/openedx-core` will always
  have an `openedx` remote. It might have a `kdmccormick` and `open-craft` remote, too.
* Branches we create are prefixed with `kdmccormick/` unless otherwise requested.
* In some rare cases, an org may have two forks of the repo. The usually only ever happens because of GH security forks. In that case, the name of the remote should be the name of the fork, e.g. `openedx-platform-ghsa-xxxx-xxxx-xxxx`.


### Working mode 1: "Simple Mode"

This is for just perusing repos as they exist, usually on main or a release branch, sometimes on specific branches. This is a lightweight mode optimized for simplicity, quick questions, short discoveries, basic PR review. We *don't* make changes to code in this mode.

In this mode, directly browse the code at `~/axim/$org/$repo`. You may want to run some tests or checks--you can use the local `.venv`s within each `$repo` for that.  You might check out certain branches. If you need to clone additional repos to `$org/$repo`, just ask. While switching branches is fine, take no destructive actions on these repos without explicit confirmation.

Notes in this mode can go in `~/axim/notes`.

In this mode, you are forbidden from working in `~/axim/ws` unless explicitly requested otherwise.


### Working mode 2: "Workspace Mode"

This is how we develop patches, review complicated cross-cutting PRs, perform complex discovery across many repos and different branches with detailed notes, work on proper projects, etc. This is heavier-weight, optimized for comparmentalization of different projects.

In this mode, rather than working in the repos at `~/axim/$org/$repo`, we'll work in cross-repo "workspaces".
So, every time Kyle says "start a new workspace for $project, with repos
$org1/$repo1, $org2/$repo2, etc.", that means:

* Ensures clones exist at ~/axim/$org/$repo, creating them as needed
  (`git clone -o $org git@github.com:$org/$repo.git`).
* For each requested repo, `git fetch $org` (the remote named after the upstream org)
  so that we have the latest branches available. Please note that any given `~/axim/$org/$repo` might
  be on some random branch or be in detached HEAD; don't worry about this.
* Make a new "workspace" dir at `~/axim/ws/$project`
* Within the workspace, for each requested repo, create a git worktree at `~/axim/ws/$project/$repo` (note no $org
  in the path; if there are $repo name conflicts, say so).
* Kyle will likely specify branch names, for some repos or for all repos. If he doesn't, ask what branche names to use for any repo where un-specified. Then, for each repo worktree:
  * If the requested branch exists in the main clone or in another worktree, fail fast and ask what to do.
  * If there is a matching branch on the upstream, create and switch to a local branch tracking it.
  * Otherwise, create the branch locally (from the upstream's default branch, unless otherwise specified).
  * Don't push anything at this point.
* All notes, artifacts, and temp files needed for a project go in its workspace directory.

Later on, Kyle may ask for additional repos, or you may point out a need for additional repos in the workspace. If so,
apply these same workflow: main clones go in `~/axim/$org/$repo`, a worktree goes in `~/axim/ws/$project/$repo.

Kyle may also ask you to act in "Workspace Mode" on an existing `~/axim/ws/$project` folder. I think you can figure out what to do in this case.

Prerequisite: Kyle's SSH key must be loaded into the shared macOS ssh-agent
(`ssh-add --apple-use-keychain ~/.ssh/id\_rsa`, run in a regular terminal). `! ssh-add`
from inside Claude Code doesn't work because it can't prompt for the passphrase.
If `ssh -T git@github.com` fails, ask Kyle to do this.


### Other local dev notes

* Use `uv` and `uv pip`, not `pip`

