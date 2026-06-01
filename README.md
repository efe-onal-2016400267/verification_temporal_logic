# Verification of Temporal Logic (MCMAS) 🤖🔍

A repository dedicated to the formal verification of Multi-Agent Systems (MAS) using **MCMAS** (Model Checker for Multi-Agent Systems). This project focuses on modeling agent behaviors and verifying specifications written in various temporal and strategic logics, including **CTL** (Computation Tree Logic), **LTL** (Linear Temporal Logic), and **ATL** (Alternating-time Temporal Logic), alongside epistemic and cooperation modalities.

---

## 🧠 Core Concepts

### 1. Linear Temporal Logic (LTL)
LTL reasons about a single, linear timeline of events. It is useful for verifying paths of execution.
* **Operators**:
  - `X` (Next): The property holds in the next state.
  - `F` (Future/Eventually): The property will hold at some point in the future.
  - `G` (Globally/Always): The property holds in all future states.
  - `U` (Until): The first property holds until the second property becomes true.

### 2. Computation Tree Logic (CTL)
CTL is a branching-time logic where the future can branch into multiple paths. It combines path quantifiers with temporal operators.
* **Path Quantifiers**:
  - `A` (All paths): The property must hold along all possible execution paths.
  - `E` (Exists a path): There exists at least one execution path where the property holds.
* **Common Formulas**: `AG` (Always Globally), `EF` (Eventually Exists), `AF` (Always Eventually).

### 3. Alternating-time Temporal Logic (ATL)
ATL generalizes CTL by introducing strategic operators. It allows reasoning about what coalitions of agents can achieve, regardless of how other agents behave.
* **Operators**:
  - `<<G>> F` (Coalition $G$ can guarantee that eventually $F$ holds).
  - `[[G]] G` (Coalition $G$ cannot prevent the system from always being in a state where the property holds).

---

## 🛠️ MCMAS Overview

**MCMAS** is an open-source model checker specifically tailored for the verification of multi-agent systems. It takes input files written in **ISPL** (Interpreted Systems Programming Language) and verifies whether the system satisfies the specified temporal, epistemic, and strategic formulas.

### Structure of an ISPL File
An ISPL file typically consists of:
1. **Agent Definitions**: States, Actions, Protocols, and Evolution rules for each agent (including the Environment).
2. **Evaluation Relation**: Maps atomic propositions to states of the agents.
3. **Init States**: Defines the initial state(s) of the system.
4. **Formula Specifications**: The CTL, ATL, or epistemic formulas to be verified.

---

## 🚀 Getting Started

### 1. Prerequisites
- **MCMAS**: Download and install the MCMAS binary from the [official MCMAS website](http://vas.it.uu.se/mcmas/).
- **Python 3.8+** (for any auxiliary analysis or parsing scripts).

### 2. Installation
If you are using any Python-based helper scripts or wrappers included in this repository, install the required dependencies via `requirements.txt`:

```bash
pip install -r requirements.txt
```

---

## 💻 Usage

To run the model checker on an ISPL specification file:

```bash
mcmas your_model.ispl
```

### Example ISPL Specification
Below is a basic template of an ISPL file verifying a simple mutual exclusion protocol:

```ispl
Semantics=SingleAssignment;

Agent Environment
    Vars:
        turn: {0, 1};
    end Vars
    Actions = {none};
    Protocol:
        Other: {none};
    end Protocol
    Evolution:
        turn=0 if turn=1;
        turn=1 if turn=0;
    end Evolution
end Agent

Agent Agent1
    Vars:
        state: {idle, requesting, critical};
    end Vars
    Actions = {request, enter, exit};
    Protocol:
        state=idle: {request};
        state=requesting and Environment.turn=0: {enter};
        state=critical: {exit};
    end Protocol
    Evolution:
        state=requesting if state=idle and Action=request;
        state=critical if state=requesting and Action=enter;
        state=idle if state=critical and Action=exit;
    end Evolution
end Agent

Evaluation
    Agent1InCritical if Agent1.state=critical;
end Evaluation

InitStates
    Environment.turn=0 and Agent1.state=idle;
end InitStates

Formula
    -- Verify that Agent1 can eventually enter the critical section (CTL)
    EF(Agent1InCritical);
end Formula
```
