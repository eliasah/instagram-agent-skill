---
name: ig-init
description: >-
  Set up the files every other skill in this pack reads - brand.md (who the
  owner is, what they offer, what they may claim) and voice.md (how they sound)
  in ~/.claude/instagram/ - by asking, never by guessing from the profile. Use
  when the user says "set up", "init", "onboard me", "start here", "write my
  brand.md", "write my voice.md", or another skill reports brand.md missing.
---

# ig-init

Run once, then again whenever something about the account changes. Every other
skill starts by reading what this one writes. Without it, each skill asks the
same questions again.

## The rule

Ask. Do not infer. A bio, flags, a handle, a link or a few posts are not enough
to decide someone's range, audience, offers or credentials. If the user pastes
a profile, use it to suggest answers, then have them confirm or correct each
one. Anything not confirmed stays blank. Blank means "ask me" to every other
skill, which is the right default.

## Step 1: check what exists

Look in `~/.claude/instagram/` for `brand.md`, `voice.md`, `log.md` and
`plan.md`.

- A file that exists is never overwritten. Read it, list its empty fields, and
  offer to fill only those. Changing a filled field needs the user to say so.
- Nothing there: first run. Say so in one line and go on.

## Step 2: brand.md

Use `brand/TEMPLATE.md` from this plugin if it is available. If not (skills
copied without the rest of the repo), these are the fields:

- Range: topics covered, all of them; topics not covered
- Audience: who they are talking to; language(s) for captions, on-screen text
  and DMs
- Goal: what the account is for right now, one goal
- Offers: what they sell or point to, and where each lives
- Credentials: exact wording, copied word for word
- Claims not to make
- task_tracker (optional): where fix lists go, or blank

Ask in one batched message, grouped as above, not one field per turn. Write
answers in the user's own words. Do not polish, shorten or upgrade
credentials or claims, and do not run this file through `/ig-human`: it is
reference data, not copy.

## Step 3: voice.md

Ask for three of the user's own reels (transcripts or links they can paste the
words of) or captions. Infer how they sound from those: pace, words they use,
words they avoid, swearing, emoji, face or voiceover. Shape it like
`templates/voice.md` from this plugin when available.

Voice is the one thing inferred, and only from their own words. Who they are,
who they talk to and what they sell come from `brand.md`, not from the samples.
Show the inferred voice and get a yes before writing it. No samples: write the
questions from the template as blanks and say `/ig-reel` will ask again.

## Step 4: the other files

Create empty `log.md` and `plan.md` if missing, each with a one-line header,
so `/ig-reel`, `/ig-plan` and `/ig-audit` find them.

## Writing

Create `~/.claude/instagram/` if needed and write the files. These files hold
facts about the user: never write them inside a git repository, and never
commit them.

If the path is not writable (claude.ai chat, a sandbox, no file tools), print
each file in its own fenced block with the path above it, tell the user where
to save it, and finish. Do not fail the run over it.

## Output

Short:

```
INIT DONE
brand.md   written, 2 fields blank: credentials, task_tracker
voice.md   written from 3 captions
log.md     created
plan.md    created

Blank fields get asked for when a skill needs them.
Next: /ig-profile to score the profile, or /ig-plan for the week.
```

Nothing is posted, sent or changed on Instagram.
