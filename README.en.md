<p align="center">
  <a href="https://tashan.ac.cn/homepage/" target="_blank" rel="noopener noreferrer">
    <img src="docs/assets/tashan.svg" alt="Tashan Logo" width="200" />
  </a>
</p>

<p align="center">
  <strong>Sandbox Simulation Validation Framework</strong><br>
  <em>沙盘推演验证框架</em>
</p>

<p align="center">
  <a href="#overview">Overview</a> •
  <a href="#six-question-criterion">Six Questions</a> •
  <a href="#ecosystem">Ecosystem</a> •
  <a href="#contributing">Contributing</a> •
  <a href="README.md">中文</a>
</p>

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![Status](https://img.shields.io/badge/status-planned-lightgrey)

> 🔲 **Skeleton repository. Development has not started yet.**

---

## Overview

The third layer of the world substrate: the **validation method**. This repository provides a dedicated methodology—**sandbox simulation**—for testing whether the axioms (①) and architecture (②) produce a genuinely closed-loop, coherent system.

This is not ordinary software testing. Each valid sandbox case must contain four components: initial state, triggering event sequence, explicit reasoning chain, and terminal-state judgment with evidence.

Crucially, sandbox simulation is also a **meta-validation** tool: if a system failure cannot be traced to any existing T–PA–G entry, then the theoretical framework itself is incomplete and must be revised.

---

## Six-Question Criterion

The sandbox operationalizes closure verification through six questions:

1. Can the event reach the subject?
2. Is it truly perceived?
3. May it interrupt the current activity?
4. How does the response go?
5. How is state written back?
6. How is the next step scheduled?

---

## Ecosystem

| Layer | Project | Repository | Type | Status |
|-------|---------|-----------|------|:------:|
| World Substrate | ① Axiom Framework | [world-axiom-framework](https://github.com/TashanGKD/world-axiom-framework) | Open Source | 🔲 |
| World Substrate | ② Architecture | [world-three-particle-impl](https://github.com/TashanGKD/world-three-particle-impl) | Open Source | 🔲 |
| World Substrate | **③ Sandbox Validation** ← this repo | [world-sandbox-validation](https://github.com/TashanGKD/world-sandbox-validation) | Open Source | 🔲 |
| Digital Twin | ④ Bootstrap (0→1) | [digital-twin-bootstrap](https://github.com/TashanGKD/digital-twin-bootstrap) | Open Source | 🟡 |
| Digital Twin | ⑤ Iteration (1→100) | [digital-twin-iteration](https://github.com/TashanGKD/digital-twin-iteration) | Open Source | 🔲 |
| Core App | Digital World | TashanGKD/tashan-world (private) | Private | 🔲 |
| Commercial | Twin Platform | TashanGKD/tashan-twin-platform (private) | Private | 🔲 |
| Public Interest | Tashan Forum | [tashan-forum](https://github.com/TashanGKD/tashan-forum) | Open Source | 🔲 |

---

## Contributing

Contributions welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) (coming soon).

---

## License

MIT License. See [LICENSE](LICENSE) for details.
