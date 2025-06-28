# First run this or the equivalent:

```bash
(
  echo "$ bun run type-check"
  bun run type-check
  echo -e "\n\n$ bun run lint"
  bun run lint
) > combined_typechecker_and_linter_problems.txt 2>&1
```

---

# Then, tell Claude Code the following:

I need you to start going through combined_typechecker_and_linter_problems.txt (just pick random chunks of 50 lines at a time from anywhere within the file, starting with a random starting line; since I have multiple agents working on this task, I want each agent to work on different problems!) 

As you select your chosen problems, mark them as such by prepending the line with [COMPLETED] so we can keep track of which ones have already been processed-- do this up front so there's no risk of forgetting to do it later and wasting time and compute on errors that are already being worked on or which were previously worked on. (Obviously, when selecting your random lines to work on, you should first filter out any rows that have "[COMPLETED]" in them so you don't accidentally work on already in-progress or completed tasks!)

I want you to be SMART about fixing the problems. For example, if it's a type related problem, never try to use a stupid "band aid" fix and set the type to be Unknown or something dumb like that. If there's an unused variable or import, instead of just deleting it, figure out what we originally intended and whether that import or variable could be usefully and productively employed in the code to improve it so that it's no longer unused or unreferenced.

Make all edits to the existing code files-- don't ever create a duplicative code file with the changes and give it some silly name; for instance, don't correct a problem in ComponentXYZ.tsx in a newly created file called ComponentXYZFixed.tsx or ComponentXYZNew.tsx-- always just revise ComponentXYZ.tsx in place!

CRITICALLY IMPORTANT: You must adhere to ALL guidelines and advice in the NEXTJS15_BEST_PRACTICES.md document. I want to avoid technical debt and endless compatibility shims and workarounds and just fix things once and for all the RIGHT WAY. This code is still in development so we don't care at all about backwards compatibility. Note that we only use bun in this project, never npm. And you MUST check each proposed change against the @NEXT_BEST_PRACTICES_IMPLEMENTATION_PROGRESS.md guide!

When you're done fixing the entire batch of selected problems, you can commit your progress to git with a detailed commit message (but don't go overboard making the commit message super long). Try to complete as much work as possible before coming back to me for more instructions-- what I've already asked you to do should keep you very busy for a while!
