# Add, override and output git notes

Action allowing to manipulate git notes in several stages of your pipeline.

"A typical use of notes is to supplement a commit message without changing the commit itself." 

[man 1 git-notes](https://git-scm.com/docs/git-notes)

One use case example is, which made me create this, was that I needed to pass on a single string from one workflow A to another workflow B, B was triggered on A's completion.
It seemed too overkill to upload an artifact to do it so, and the information being passed on was contextual to the commit, so here we are.

This is the only use case for now. 

> Only tested on linux & mac.

> If you are Adding / Overriding notes and not only reading, you need to clone your repository with a PAT(Personal Access Token).

## Usage

```yaml
- id: example-run
  name: Notes Action
  uses: marcoslopes/git-notes-action
  with:
    # Required, which object to attach note to
    sha: ${{ github.sha }}
    # Optional, Notes message, if present will Add / Override*
    message: "optional message"
    # Optional, Remote to fetch / push notes to
    remote: "origin"
    # Optional, repository path, defaults to current directory
    path: "."
    # Optional, notes ref
    ref: "commits"
    # Optional, override note with current 'message' if true
    override: false
    # Optional, git config user.name
    name: "Git Notes Action"
    # Optional, git config user.email
    email: "git-notes-action@users.noreply.github.com"
```

## Examples

## writing value
```yaml
...

- name: checkout source
  uses: actions/checkout@v2
  with:
    fetch-depth: 1
    token: ${{ secrets.GH_PERSONAL_ACCESS_TOKEN }}

- id: save-value
  uses: marcoslopes/git-notes-action@v1
  with:
    sha: ${{ github.sha }}
    ref: 'some-other-scope'
    message: "${{ steps.some-previous-step.outputs.some-value }}"

```

```
# download all notes refs
git fetch origin "refs/notes/*:refs/notes/*" -f

# show notes from all refs
git log --show-notes="*"

# show notes from some-other-scope ref
git log --show-notes="some-other-scope"

...

commit 0000000000000000000000000000000000000000 (HEAD -> main, origin/main)
Author: Marcos Antonio Lopes <marcos@domain.com>
Date:   Sun Oct 00 00:00:00 2021 +1300

    test notes commit

Notes (some-other-scope):
    some-value-from-some-previous-step-output

...
```

## reading value

```yaml
...

- id: get-value
  uses: marcoslopes/git-notes-action@v1
  with:
      sha: ${{ github.sha }}
      ref: 'some-other-scope'
  
- id: use-value
  run: |
      echo "value from note ${{ steps.get-value.outputs.message }}"

...
```
