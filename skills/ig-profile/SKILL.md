---
name: ig-profile
description: >-
  Score an Instagram profile out of 100 against a 12-part rubric and rewrite
  the parts that lose points - name field, bio, link, highlights, pinned three,
  grid. Use when the user says "optimize my profile", "fix my bio", "score my
  Instagram", "why don't people follow me", or pastes their profile and asks how
  it reads.
---

# ig-profile

Almost everybody optimises the wrong thing here. The profile is not a
storefront people browse. It is a **decision screen**, arrived at from one
reel, and it gets about three seconds to answer one question: is there more of
that here, and is it for me.

## Step 0: the brand brief

Read `~/.claude/instagram/brand.md` if it exists. If a field you need is
empty, ask. Do not infer it. Niche, range, audience, offers, credentials and
claims never come from a bio, flags, a handle or a few posts. Credentials go in
with the exact wording in `brand.md`. A claim that is not in `brand.md`, or is
listed under "Claims not to make", is not written: ask, or leave `{{claim}}`.
If the file does not exist, ask only for the fields this run needs and point
the user to `/ig-init`.

## Input

Ask the user to paste or screenshot: the name field, the handle, the bio, what
the link points to, the highlight names, what is pinned, and the first nine
grid covers. A screenshot of the top of the profile plus the first two grid
rows is enough for a first pass.

Do not log into Instagram on their behalf.

## Read the last entry first

If `~/.claude/instagram/profile.md` exists, read its last `## ` entry before
scoring. You need three things from it: the total, the score per rubric item,
and the fixes with their status.

Then, after scoring this run:

- Open the report with one line: `Score changed from X to Y. Items changed:
  name_field 2->10, pinned_three 0->10.` List only items whose score moved. If
  nothing moved, say so.
- A fix marked `[x]` in the last entry is done. Do not propose it again. A fix
  still `[ ]` whose change now shows on the profile is also done: mark it `[x]`
  in the new entry and say it landed. A fix still `[ ]` that has not landed is
  carried forward, same id, not rewritten from scratch.
- Copy every done fix into the new entry as `[x]`, so the last entry alone
  holds the full list.

No file means first run. Say so and skip the delta line.

## Score it

Read `rubric.json` in this folder. Twelve items, 100 points, each with what
full marks looks like and how it usually fails. Score every item, show the
table, give the total. Be honest. Most profiles land in the 30s and 40s on the
first pass and a generous score is useless.

```
PROFILE SCORE  38/100

  name field       2/12   name only, no words anyone searches
  bio first line   3/12   three nouns and a coffee emoji
  pinned three     0/10   nothing pinned
  highlights       2/8    "Random", "Life", "2023"
  grid legibility  4/8    six of nine covers are a face mid-sentence
  ...
```

## Then rewrite, in this order

Fix in descending order of points lost. Do not rewrite everything at once, the
user has to actually go and change each of these.

**1. The name field (30 characters).** The bold line under the photo, not the
handle. This is the field Instagram search matches against, and most accounts
put a name in it and nothing else. Format that works:
`{Name} | {what you do, in searched words}`. Give three options.

**2. Bio line one.** Who this is for and what changes. Not a job title, not
adjectives, not a pipe-separated list of identities. The rest of the 150
characters carries one piece of proof or one plain offer.

**3. The pinned three.** Three slots, three different jobs: the best proof, the
clearest explanation of the offer, the best introduction to the person. This is
the highest-leverage fix on the whole profile and it takes four taps. An
unpinned grid shows whatever was posted last, which is a coin flip.

**4. Highlights.** Four to six, named for the questions a buyer asks: Pricing,
Results, How it works, About. Not "Random". Delete the rest.

**5. The link.** One destination that matches what the bio just promised. Five
are allowed; two is already a menu and a menu converts worse than a door.

**6. Grid covers.** The first nine at thumbnail size. Reel covers get chosen,
not left as whatever frame one was. Four words of text on a cover makes a grid
readable in one glance.

## Output

Short. Analysis first, then a line that is exactly `===COPY===`, then only the
paste-ready text.

Above the line: the delta line (from the second run on), the score table, the
fixes in fix-first order with one line of why each, and the projected score if
every fix lands. Show the projection honestly. If the rewrite reaches 84 and
not 98, say 84, and say what the rest needs, which is usually a grid, a story
habit and a pinned post that does not exist yet. None of that is a rewrite.

Below the line: the rewrites as copy-ready blocks in fix-first order, each run
through `/ig-human`. Nothing else.

Nothing is saved to Instagram by this skill. The user edits each field.

## Save the entry

Append one entry to `~/.claude/instagram/profile.md` (create the folder and
file if missing). Never edit or delete earlier entries. Store the score you
measured, not the projected one.

```
## 2026-11-04
total: 38/100
name_field: 2/12
bio_first_line: 3/12
bio_body: 2/8
pinned_three: 0/10
link: 4/8
highlights: 2/8
grid_legibility: 4/8
photo: 5/6
handle: 4/6
category_contact: 3/6
recent_activity: 6/10
story_presence: 3/6
fixes:
- [ ] name_field: add searched words after the name
- [ ] pinned_three: pin proof, offer, intro
- [x] link: one destination (done 2026-10-12)
```

One line per rubric item, using the ids in `rubric.json`. One fix per line,
keyed by the rubric id it fixes, so the next run can match it. The user can
tick a fix `[x]` by hand; that counts as done.

If the path is not writable (claude.ai chat, a sandbox, no file tools), print
the entry in a fenced block above `===COPY===`, tell the user to paste it at
the end of their `profile.md`, and carry on. Do not fail the run over it.

## Turn the fixes into tasks

After saving the entry, one task per open fix. Where the tasks go comes from
the `task_tracker` field in `brand.md`, never from this file or a guess.

- **Tracker set and reachable** (a connector or tool for it is available in
  this session): create one task per open fix that has no task yet. Title:
  `ig-profile: <rubric_id>: <change>`. Body: the paste-ready copy for that fix,
  if it has one. Record the task link on the fix line in the entry:
  `- [ ] name_field: add searched words after the name (task: <link>)`.
- **On a re-run**, for every fix that is now done, mark its task done in the
  tracker, using the link on the fix line, or the title if there is no link.
  Do not create a second task for a fix that already has one.
- **No tracker set, or none reachable:** write `~/.claude/instagram/fixes.md`
  next to `profile.md` and stop there. Do not build a tracker client or call an
  API by hand. Same lines as the entry's `fixes:` list, newest run on top under
  a `## <date>` heading. On a re-run, tick the matching lines `[x]` in
  `fixes.md` instead.

If `fixes.md` is not writable either, the printed entry already carries the
fix list. Say so and finish.

Tasks are the only thing this skill writes outside the user's own files. It
never posts, sends or edits anything on Instagram.
