---
description: Create or update the feature specification from a requirements file, card, or natural language feature description.
argument-hint: [text-card-or-path]
model: opus
---

# Feature Discovery

You're writing a plan to implement a net new feature that will add value to the application.

The text in the arguments is the requirements source. Your first step here is to analyze what is the source for the feature arguments, which could be: 

- A string (requirements typed in natural language)
- A file in the local filesystem
- A card in a project management software that you can access through MCP Tools

Based on the identified source, acquire the necessary context by reviewing what was types, reading the file or pulling the card from the tool. 

Given that feature description, do this:

<arguments>
#$ARGUMENTS
</arguments>

You **MUST** consider the user input before proceeding (if not empty).
If the input is empty, error now and ask for a proper input.

## Setup

1. Create a slug for this feature (<feature_slug>), should be a git branch friendly name that is not in the ./specs folder (not repeated)
2. Create the folder at ./specs/<feature_slug>
3. Make sure we are in a branch with the same name. If not, create it.
4. Parse user description from Input
    If empty: ERROR "No feature description provided"
5. Extract key concepts from description
    Identify: actors, actions, data, constraints

## Analysis

Go through the cards, parents and children if required, and build an initial understanding of what needs to be build. Think carefully about what is requested, ensure you understand exactly: 
    - Why this is being built (context)
    - What is the expected outcome for this issue? (goal)
    - How should it be built, just directionally, not in details (approach)
    - If it requires using new APIs/tools, do you understand them?
    - How should this be tested?
    - What are the dependencies?
    - What are the constraints?
Anything that isn't clear MUST be recorded as a question / clarification needed.

Once you have a good understanding of what is being built, save it in the specs/<feature_slug>/spec.md file and ask the human to review it.

Your goal at this point is to extract the feature requirements from the functionality perspective, not to define its architecure. 

## General Guidelines

## Quick Guidelines

- Focus on **WHAT** users need and **WHY**.
- Avoid HOW to implement (no tech stack, APIs, code structure) for now.
- DO NOT create any checklists that are embedded in the spec. That will be a separate command.

## Spec Format

```md
# Feature Specification: [FEATURE NAME]

**Feature Branch**: `[###-feature-name]`  
**Input**: User description: "$ARGUMENTS"

## Context and Understanding 

[ Explain what you understand from the user requirements. What is the user trying to build and what is the motivation for it? What is wrong / missing today and needs to change?]

## Feature Description
<describe the feature in detail, including its purpose and value to users>

## Requirements *(mandatory)*

<!--
  ACTION REQUIRED: The content in this section represents placeholders.
  Fill them out with the right functional requirements.
-->

### Proposed solution

<!--
  ACTION REQUIRED: How will this feature really work? Think about user journeys, APIs, reports. Do we need tests? Do we need configuration changes? Are we handling deployment? Create as many stories as its required to deliver this.
-->
Examples:

- **US-001**: User Story for an important feature
- **US-002**: User Story for the backend service
- **US-003**: User Story for the frontend implementation
- **US-004**: User Story for configuration
- **US-005**: User Story for unit testings


### Functional Requirements

- **FR-001**: System MUST [specific capability, e.g., "allow users to create accounts"]
- **FR-002**: System MUST [specific capability, e.g., "validate email addresses"]  
- **FR-003**: Users MUST be able to [key interaction, e.g., "reset their password"]
- **FR-004**: System MUST [data requirement, e.g., "persist user preferences"]
- **FR-005**: System MUST [behavior, e.g., "log all security events"]

*Example of marking unclear requirements:*

- **FR-006**: System MUST authenticate users via [NEEDS CLARIFICATION: auth method not specified - email/password, SSO, OAuth?]
- **FR-007**: System MUST retain user data for [NEEDS CLARIFICATION: retention period not specified]

## Success Criteria *(mandatory)*

<!--
  ACTION REQUIRED: Define measurable success criteria.
  These must be technology-agnostic and measurable.
-->

### Measurable Outcomes

- **SC-001**: [Measurable metric, e.g., "Users can complete account creation in under 2 minutes"]
- **SC-002**: [Measurable metric, e.g., "System handles 1000 concurrent users without degradation"]
- **SC-003**: [User satisfaction metric, e.g., "90% of users successfully complete primary task on first attempt"]
- **SC-004**: [Business metric, e.g., "Reduce support tickets related to [X] by 50%"]

## Clarification Needed
<optionally list any questions and clarifications required to properly design the feature. A developer will answer them in time. If the user answers your clarification questions, you should update the documentation.>

## Notes
<optionally list any additional notes, future considerations, or context that are relevant to the feature that will be helpful to the developer>
```

After creating the spec file, present your questions to the human.

Ask the questions in `Clarification Needed` section in one single message. Provide alternatives and suggest your preferred appraoch. 

Example:
```
I have finished my initial review. In the analysis process, I identified some issues that would benefit from clarification. I am providing each of them below. You can reply only for the items you want to change. Any item you don't reply to, I'll consider you accepted my suggestion. You can reply to all of them in the same message. 

1. Insert your question here provididing details on context and why it's important
A) Option 1 
B) Option 2
C) Option 3
D) Tell me what to do. 

(repeat this process for each question)
```

Once the human provides you with the answers, MUST update the spec.md file and provide the human answer to your clarification questions so you can refer to them later. 

Once you are done and the discovery is complete, run the /design command. 