# Implement the following plan
Follow the `Instructions` to implement the `Plan` then `Report` the completed work.

## Instructions
- Read the plan, think hard about the plan and implement the plan.

## Parameters

If this command is called with the --ff parameter, it means you should build the feature completely. In this case, you should reach out to the user only a) if you are done, b) if you have question or c) if you find any blocks.

If the command is not called with any parameter or you find the --phases parameter in the arguments, then you should proceed with a phased approach to the development, which means you should stop after each phase and ask the user for review before continuing to the next stage. 

## Plan
$ARGUMENTS

## Report

In any mode (ff or phases), at the end of each phase:

- Update the plan.md file, marking completed tasks as done `[X]`
- Add comments to the completed phase with a summary of what was accomplished, specially if you changed the plan for some reason.


In any mode (ff or phases), when you are done: 

- Summarize the work you've just done in a concise bullet point list.
- Report the files and total lines changed with `git diff --stat`