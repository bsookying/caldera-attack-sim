# Core Concepts

* [ability](ability.md) - Atomic ATT&CK-mapped command with platform-specific executors
* [adversary](adversary.md) - Ordered collection of abilities representing a threat actor profile
* [agent](agent.md) - Implant/RAT deployed on a target host that executes ability commands
* [operation](operation.md) - Execution run of an adversary profile against a group of agents
* [fact](fact.md) - Key-value data discovered or seeded during an operation; drives variable substitution
* [planner](planner.md) - Decision engine that determines ability execution order each phase
* [obfuscator](obfuscator.md) - Encoding/obfuscation applied to commands before agent execution
* [payload](payload.md) - Binary or script file served to agents during operations
* [objective](objective.md) - Goal conditions that determine when an operation is complete
