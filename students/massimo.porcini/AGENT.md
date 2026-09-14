# Course conventions

When the user closes a course session (they will say so
explicitly: "close session N", "I finished session N", etc.):

1. Verify that it meets the acceptance criteria for that session.
2. Update `progress.md` (at the root of my folder):
   - Mark the completed session's `- [ ]` checkboxes as `- [x]`.
   - If you find it useful, add notes in the "**Notes:**" section of that session.
3. Commit with the prefix `[Agent/Model]: session N - session close`
   and a message that lists what was built.
4. Push my branch `student/massimo.porcini` and create the milestone tag:
   `git tag -a massimo.porcini/sN -m "Session N complete"` and then
   `git push --follow-tags`.

Commit convention: `[Agent/Model]: session N - Description in the imperative`.
I work on my branch `student/massimo.porcini` (never on `main`) and only
modify files inside my folder `students/massimo.porcini/`.