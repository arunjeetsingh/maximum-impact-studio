---
layout: post
title: "Two AI Coders, One Repo: How I Have Claude Code and Codex Review Each Other's Work"
date: 2026-06-30
author: Arun Singh
description: "I run Claude Code and Codex against the same Git repo with one hard rule: every change is a pull request, and neither tool merges. Then each reviews the other's PR. It turns two solo coders into a team with built-in code review."
image: /assets/img/posts/two-ai-coders-cover.png
excerpt_separator: <!--more-->
---

<figure class="post-figure">
  <img src="{{ '/assets/img/posts/two-ai-coders-cover.png' | relative_url }}" alt="Two robot AI coders facing a shared code repository, reviewing each other's pull request in a loop, with a human hand on the merge button" loading="lazy">
</figure>

TLDR: I run two AI coding tools, Claude Code and Codex, pointed at the same Git repo, with one hard rule: every change goes in as a pull request, and neither of them merges. Then I have one tool review the other's PR, and the other one fixes the comments. It's a tiny bit of process that turns two solo coders into a team with built-in code review. It's how both of the apps at [maximumimpact.studio/apps](https://maximumimpact.studio/apps/) got built and are kept maintainable.
<!--more-->

## Why bother?

AI coding tools are great, but a single tool reviewing its own work has a blind spot: it tends to agree with itself. The fix is the same thing we already know works for humans, a second set of eyes. So instead of one tool, I use two (Claude Code and Codex), and I make them check each other. Different models catch different things, and you get real code review. For high-impact architectural changes I add myself to the loop. It also keeps me, the human, firmly in the driver's seat. Nothing important lands in my codebase until I've seen it.

## The setup

Here's the exact workflow I use:

1. **Use a GitHub.com repo to version control your code.** This is the shared source of truth both tools work against. I made a [post about this last week]({% post_url 2026-06-22-build-a-github-resume-with-openclaw %}).
2. **Clone the repo locally.** Give both Claude Code and Codex separate local copies so they can keep working versions separate. You can just have either tool do this for you, just ask them with a folder location.
3. **Point both Claude Code and Codex at their respective repo folders.** They're now working on the same codebase, independently.
4. **Make a rule for both of them: every new change goes in as a pull request, and never merge** and ask them to add it to their memory, not just the context. This is the key rule. They open pull requests (PRs) but do not merge them. That way nothing goes into your main branch until you and/or the reviewing tool has reviewed it.
5. **Whenever one of them creates a PR, have the other tool become the reviewer and review it.** Literally just say something like: "review PR 19 and leave review comments."
6. **Have the tool that wrote the code fix the review comments.** Point it back at its own PR, tell it to address the comments. Ask it to be thorough and also address the optional or low-priority comments because they'll sometimes get lazy and leave those out.
7. **The crucial next step is to have the reviewer tool verify all the comments were resolved.** This is where you find out how lazy the code-writing tool was when addressing review comments :)
8. **Once you're done with the cycle, merge the changes.** It is also a good idea to build a PR-level continuous integration (CI) test suite and a continuous deployment (CD) pipeline so any changes are testable by you (or an eval agent!) right away.
9. **The same thing works for tech designs, not just code.** I keep design docs in the repo as markdown files, so the tools can review each other's proposals the exact same way. One drafts the design, the other critiques it, before a single line of code is written.

## Why each rule matters

- **GitHub as the hub.** Both tools speak Git/PR fluently, so a shared repo is the natural meeting point.
- **The "PR, never merge" rule.** This is the key guardrail to avoid an unmaintainable code base. It forces every change through review and keeps the merge button in your hands. It's also why this is safe to run with capable tools. They propose, you (and the other tool) dispose.
- **Cross-review instead of self-review.** Two different models disagree in useful ways. One will flag an edge case, a naming issue, or a sketchy assumption the other one glossed over.
- **Designs in markdown.** Catching a bad design in review is far cheaper than catching it after it's built. Treating design docs like code with versioning, PRs, and reviews baked in pulls that feedback all the way to the front.

## Does it actually work?

Both of the apps on [maximumimpact.studio/apps](https://maximumimpact.studio/apps/) were built this way. Claude Code and Codex pointed at the same repo, opening PRs, reviewing each other, and never merging without my sign-off. [TokenCounter]({% post_url 2026-05-31-announcing-tokencounter %}) (a token spend tracker for iOS and Android) and [Chal Rickshaw]({% post_url 2026-06-19-chal-rickshaw-ios-public-beta %}) (an endless-runner game) both shipped on this loop.

## Try it

You don't need anything fancy, a GitHub repo, both tools installed, and the discipline to enforce the "PR, never merge" rule. Give it a shot on your next project and let the two of them keep each other honest.
