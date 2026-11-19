# 💻 CHEM 281 Lab: Agentic Engineering

## 🧪 Goal

The goal of this lab is to:

1. Familiarize yourself with **engineering using ai agents**.
2. Learn how to **write impactful prompts and trace ai generated code**. 
3. Practice using **cheminformatics code** and **C++ and pybind11**.
4. Use the provided ideas.md file and **riprap project scaffolding** and **containerized execution** to **prompt the ai agent to write code**.

---

## 🗂️ Provided

- An `ideas.md` file to set up the initial requirements from the cheminformatics library.
- The agentic engineering framework [riprap](https://github.com/MolSSI/riprap).

---

## 💻 Setup
Install [Copier](https://copier.readthedocs.io/en/stable/#installation).

Create a new riprap project by copying the template:
```bash
copier copy gh:MolSSI/riprap my-new-project
```

Answer the prompted questions:
```text
🎤Human-readable name of your project (e.g. "My Project")
Cheminformatics Code
🎤One sentence-description of what your project does
Cheminformatics code to represent and manipulate molecules
🎤 Base image for the development tooling layer
ubuntu:24.04
🎤 Which command-line coding agents should be installed?
Choose your specific agent or just install all of them (default is all)
🎤 Primary programming language?
Python
🎤 Create skeletal Python package files?
n for No
🎤 Create Sphinx documentation skeleton?
n for No
🎤 Where should project dependencies come from?
Dependencies from pip only (no conda)

Any other questions answer as you like!
```

Launch the container where you will run your agents in:
```
./rr.sh
```

From here you should be able to run `claude`, `codex` or `opencode`. If you have API keys or log-in creditials for claude or codex you can use them BUT `opencode` has free tier models that you can use for the purposes of this lab.

## ✅ Tasks
The goal of this lab to create a cheminformatics software package that can help you represent and manipulate molecules. Theres a lot of work that goes into writing cheminformatics software and many edge cases but we will simplify it to only working with simple SMILES based molecules or from .xyz files with no 3D coordinate generation. 

### Investigating Ai web chats
Before looking at the ideas.md file for the requirements we want our software to have, I want you to prompt any web based ai chat of your choice with the following prompt:

```
Create a cheminformatics code that can represent molecules. It should be able to read and write molecules. The code should also include the following functionality:

Calculate molecular descriptors
Similarity searching
```

Once it finishes working on it, examine the code and try to run it on your local system. Answer the following questions:
- What language(s) did it use to write the code with?
- What was the system designed like? OOP vs functional?
- What features are present? How can you create a new molecule? From a file or SMILES or SMARTS or build it out? Can it generate 3D coordinates? 
- What output formats does it let you write with and what input formats does it accept?
- Were any tests generated? How much of the code is covered by the tests?
- Does the Ai give you any feedback on the design decisions it took or why it did certain things? 
    - In the comments or as part of the output it provided?

### Investigating Agentic Engineering
The `ideas.md` in this repository contains a more thorough prompt with design decisions and pseudo function definitions. The goal of this is to provide a good foundation of how you want the code to look and work to narrow the scope of what the agent may want to do. This is the starting point and the code and architecture will be refined as you work to develop the software. Examine the file and understand what we want the architecture and functions to look like. You may also want to research or refresh some of the concepts like an adjacency list for graph representation, SMILES, and similarity searching with fingerprints.

Once you are comfortable with the requirements of the code. You can start building out the second iteration which riprap calls the architecture. Under the `rqm` directory you will see an `ARCHITECTURE.md` file, which is currently empty. `riprap` takes a disciplined AI engineering approach forcing the agents to adhere to a structured, multi-stage engineering methodology:

1) Architecture
2) Planning
3) Implementation

There are agent skills available for all 3 of these stages to help provide more context to the agent when working on each stage (see `.riprap/managed/skills`). The idea is that your agent will take your prompt (`ideas.md`) and build out each stage and you will have be able to trace your output code back through the stages and have a ledger of what you asked the agent to do. For each skill the agent will also ask clarifying questions and ask for you to make design decisions, **feel free to make any decisions you want here, your final code solution will reflect the decisions you make!** Take some time to look through the agent skills to see what context is provided for them.

#### rr-architecture
So to start development process you can start your agent with `claude`, `codex` or `opencode`, and use the architecture skill on the ideas.md file like so:

```
codex
$rr-architecture Use this skill to fill out the ARCHITECTURE.md file for a cheminformatics library described in ideas.md

claude
/rr-architecture Use this skill to fill out the ARCHITECTURE.md file for a cheminformatics library described in ideas.md

opencode
/skills
# pick rr-architecture
/rr-architecture Use this skill to fill out the ARCHITECTURE.md file for a cheminformatics library described in ideas.md
```

Note: *The agent will also ask for permission to run certain commands, you should read them to see what they are trying to do and decide to let them do it. Approval fatigue with agentic coding is a real thing and since we are running in a containerized environment, the damage it can do to our local system if it runs the wrong command is limited.*

Note: *If you have approval fatigue you can disable permissions in opencode by Ctrl-P and then searching for Enable auto-approve permissions*

As the agent works through the ideas.md file to build out the ARCHITECTURE.md it will prompt you with more questions and you should answer them to the best of your ability. Its okay to take time to look up answers or research what the best solution might be.

Once completed the agent should tell you what it did and what the key recorded commitments in the `ARCHTECTURE.md` file are. Look through the file and make sure everything is sound and makes sense to you. Sometimes the agent can make decisions you don't agree with and it didn't ask you. At this stage you can prompt it again to update what it wrote down with what you want.

Note: *You could in theory just update the `ARCHITECTURE.md` file yourself but this is not recommended because it would not be reflected in the agents context so its best to get the agent to update the file with a prompt than updating it yourself.*

#### rr-plan
Once you are happy with the `ARCHITECTURE.md` file, you can run the `rr-plan` skill with the prompt below which will break up the work into sequential steps in the most reasonable order, "planning" the actual development.

```
/rr-plan Considering the rqm/ARCHITECTURE.md file what would be the best place to start? Can you divide the key work into reasonably ordered phases? Once you have a reasonable plan, write this to a plan.md file.
```

Once the planning phase is done and you have a `plan.md` file, you will see distinct phases the agent thinks would be the best way to develop the library. Once this is done, and you have the high level planning file you will go into a loop to implement all the phases one by one.

The first step of the loop is to call `rr-plan` on the phase you are working on from `plan.md`:

```
/rr-plan Let's start planning Phase 1 of plan.md
# Answer any of the questions it asks.
```
After this step in the loop the agent will write out markdown file(s) for this phase of the requirements, and you can find the files under `rqm/`. You will also find that there are tags for the different requirements `rq-XXXXX`. These are stamps which will also be found in our implemented code so that we can map the requirements to the code as a form of traceability. These are not generated from the LLM, but are created from a script in `.riprap/managed/skills/rr-plan/rqm.sh`. You will also notice [gherkin](https://www.ranorex.com/blog/gherkin-overview-use-cases-and-format/) scenarios for testing.

#### rr-implement
The second step in the loop is to run the rr-implement skill to start implementing the code:

```
/rr-implement Lets implement phase 1 against against rqm/foundation.md
```
Note: *This may take a while*

Once the agent is finished working and has handed control back to you, look through the implemented code and start testing to see if the code builds and the tests run. Also examine the code to see if there are any mistakes or issues.

So now you have finished 1 loop of the agent driven development! Once you are happy with how the first phase was implemented you can move on to the other phases and start the loop again, with running `rr-plan` on the next phase to generate requirements .md files and then running `rr-implement` with them.
