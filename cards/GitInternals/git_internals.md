Q: What are the four object types in Git's object store?
A: Blob (file content), Tree (directory listing), Commit (snapshot + metadata), Tag (annotated pointer to an object).

---

Q: What is a Git blob?
A: An object that stores the raw content of a file — no filename, no path. Two files with identical content share the same blob object (content-addressed).

---

Q: What is a Git tree object?
A: An object that represents a directory — it contains a list of entries, each mapping a filename to a blob (file) or another tree (subdirectory), plus file mode.

---

Q: What is a Git commit object?
A: An object that records a pointer to a root tree, author, committer, timestamp, commit message, and one or more parent commit hashes — forming the DAG history.

---

Q: What is the Git object store and how are objects addressed?
A: A content-addressed key-value store in `.git/objects/`. Every object is identified by the SHA-1 (or SHA-256) hash of its content — the same content always produces the same hash.

---

Q: What is a Git ref?
A: A named pointer to a commit SHA stored as a file under `.git/refs/`. Branches, tags, and HEAD are all refs — they make human-readable names for specific commits.

---

Q: What is HEAD in Git?
A: A special ref that points to the currently checked-out branch (or directly to a commit in detached HEAD state). It tells Git where the next commit will be added.

---

Q: What is the difference between `git merge` and `git rebase`?
A: `merge` creates a new merge commit that joins two branch histories, preserving both timelines. `rebase` replays commits from one branch on top of another, producing a linear history but rewriting commit SHAs.

---

Q: What is a fast-forward merge?
A: When the target branch has no new commits since the feature branch diverged, Git simply moves the branch pointer forward — no merge commit is created.

---

Q: What is `git reflog`?
A: A local log of every movement of HEAD and branch pointers, including rebases, resets, and checkouts. It lets you recover commits that appear lost after a destructive operation.

---

Q: What is `git bisect` used for?
A: A binary search through commit history to find the exact commit that introduced a bug. You mark commits as good or bad and Git checks out the midpoint until the culprit is found.

---

Q: What is the difference between `git reset --soft`, `--mixed`, and `--hard`?
A: `--soft` moves HEAD only (staged and working tree unchanged). `--mixed` (default) moves HEAD and unstages changes. `--hard` moves HEAD and discards all staged and working tree changes.

---

Q: What is a Git packfile?
A: A compressed binary file that bundles many loose objects together with delta compression. Git creates packfiles during `git gc` or `git push` to reduce storage and transfer size.

---

Q: What does `git cherry-pick` do?
A: Applies the changes introduced by a specific commit onto the current branch, creating a new commit with the same diff but a different SHA and parent.

---

Q: What is the difference between a lightweight tag and an annotated tag?
A: A lightweight tag is just a ref pointing directly to a commit. An annotated tag is a full Git object with its own SHA, tagger identity, timestamp, and message — suitable for releases.
