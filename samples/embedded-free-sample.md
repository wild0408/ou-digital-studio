# ⚙️ AI for Embedded Systems: Complete Engineering Workflow Toolkit

## 12 step-by-step workflows · 130+ prompt templates · 25 printable checklists

> Not another "prompt pack". This is a **complete engineering operating system** for
> using AI on real embedded projects — from first power-on to release.
>
> **Three layers:**
> 1. **Workflows (Part 1)** — step-by-step engineering processes (bring-up, debugging, review, release). Each workflow says *when* to do *what*, and which prompts to run at each step.
> 2. **Prompt Templates (Part 2)** — 130+ battle-tested templates in 12 chapters, structured as **Context + Role + Task + Constraints + Output**.
> 3. **Checklists (Part 3)** — 25 one-page printable checklists you can stick on the wall or paste into your ticketing system.
>
> Written by an embedded developer, for embedded developers. Works with ChatGPT, Claude, Gemini, DeepSeek, or any LLM.

---

## 📦 Product Info

| Item | Detail |
|------|--------|
| Name | AI for Embedded Systems: Complete Engineering Workflow Toolkit |
| Content | 12 workflows + 130+ prompt templates + 25 checklists (bilingual EN/中文) |
| Format | Markdown (Obsidian/Notion/Typora) + PDF |
| Audience | Firmware engineers, IoT developers, hardware startups, embedded students |
| Coverage | STM32 · ESP32 · C/C++ · RTOS · debugging · protocols · build systems · CI · release |
| Updates | Free lifetime updates (V2: FreeRTOS deep-dive + Zephyr; V3: automotive ISO 26262 / AUTOSAR) |

---

## How This Toolkit Is Different (why it's not a "prompt pack")

- **Prompt packs give you templates but no process.** You still have to know *which prompt to run first*.
- **This toolkit gives you the process.** Each workflow is a decision tree: what to do, what to ask, what to check, when to stop.
- **Checklists catch what prompts miss.** AI is good at generating; checklists make sure you didn't skip the human steps (measure before trusting, verify reset cause, check supply chain...).

---

## Contents

### Part 1 — 12 Engineering Workflows
| # | Workflow | When to use |
|---|----------|-------------|
| W1 | New Board Bring-Up | Day one of a new board |
| W2 | Peripheral Bring-Up | First time enabling UART/I2C/SPI/ADC |
| W3 | HardFault / Crash Debugging | Reset loop, crash, "works then dies" |
| W4 | Watchdog Runaway Investigation | Device resets periodically |
| W5 | Intermittent "Heisenbug" Hunt | Bug disappears under the debugger |
| W6 | Pre-Merge Code Review Gate | Before merging any feature |
| W7 | MISRA-C Compliance Pass | Before safety review / release |
| W8 | Power Budget Optimization | Battery life too short |
| W9 | Protocol Integration | Adding I2C/SPI/UART/CAN/MQTT to a system |
| W10 | OTA / Bootloader Rollout | First OTA on real hardware |
| W11 | CI + HIL Test Pipeline | Setting up automated testing |
| W12 | Release & Documentation | Shipping a version |

### Part 2 — 130+ Prompt Templates (12 chapters)
1. Firmware Development · 2. RTOS & Task Scheduling · 3. Debugging · 4. Datasheets & Registers · 5. Code Review & Refactoring · 6. Hardware Bring-Up & PCB · 7. Communication Protocols · 8. Build Systems & Toolchains · 9. Testing & CI · 10. Documentation · 11. AI-Assisted Workflow Recipes · 12. Common AI Mistakes

### Part 3 — 25 Printable Checklists
Power-on · schematic review · UART · I2C · SPI · ADC · DMA/cache · clock · hardfault · watchdog · intermittent bug · RTOS design · ISR safety · stack · memory · power · bootloader/OTA · code review · MISRA · unit test · HIL · release · documentation · part selection · data/privacy

---
# Part 1 — 12 Engineering Workflows

> How to use: pick the workflow for your situation, follow the steps in order.
> At each step, run the referenced prompt templates (Part 2) and tick the
> checklists (Part 3). The **exit criteria** tell you when you're really done.

---

## W1 · New Board Bring-Up (Day 1)

**Objective:** Get a brand-new board to a stable, known state without frying it.

1. **Visual + continuity inspection** — checklist C1 (power-on). Measure VCC/GND shorts before applying power.
2. **Power up with current limit** — bench supply at expected max current, monitor draw. If current > expected, stop.
3. **Check rails and reset** — verify 3.3V/1.8V rails, reset pin state, boot strap pins. Run checklist C4 (clock) when the MCU answers.
4. **Get a heartbeat** — minimum firmware: GPIO toggle + UART "hello" (prompt 1.1 / 1.2). This proves toolchain, flash, clock, and UART all work.
5. **Verify debug connection** — SWD read-back of a known register; confirm flash programming works.
6. **Bring up peripherals one at a time** — use workflow W2.
7. **Document** — record measured values in the bring-up log (prompt 10.3).

**Exit criteria:** Board powers on at expected current, heartbeat LED blinks, UART prints, SWD re-flashes cleanly 3 times, measurements logged.

---

## W2 · Peripheral Bring-Up

**Objective:** Enable each peripheral methodically, isolating failures to one subsystem.

1. **Start with UART** (simplest debug channel) — init + echo (prompt 1.1, checklist C4).
2. **Verify with a scope/logic analyzer** — do not trust "it compiles". Check TX/RX at the pin.
3. **I2C/SPI next** — run checklist C5/C6; verify ACK on the bus before reading data.
4. **Interrupts** — enable one IRQ, toggle a GPIO in the ISR, confirm on the scope.
5. **DMA** — only after polling works; run checklist C8 (cache/coherency) for M7-class cores.
6. **ADC** — checklist C7 (noise); calibrate VREFINT before trusting readings.
7. **Log a pass/fail table** per peripheral with measured values.

**Exit criteria:** every peripheral has a scope/analyzer confirmation (not just "printf says OK"), and a pass/fail table exists.

---

## W3 · HardFault / Crash Debugging

**Objective:** Turn a crash into a precise root cause, fast.

1. **Capture the fault registers** — implement the fault handler from prompt 3.1 that prints stacked PC/LR/CFSR. This is step 1, always.
2. **Decode the registers** — run prompt 3.1 with the values; it will classify alignment/pointer/stack issues.
3. **Get the call stack** — read LR, walk the stack; check for stack overflow (checklist C15).
4. **Disable optimization** — reproduce at -O0. If it only crashes at -O2, suspect UB/volatile (prompt 3.3).
5. **Binary-search the trigger** — isolate subsystems (prompt 3.6 methodology).
6. **Fix + add regression test** — never fix blind; write the test that catches it (prompt 9.3).

**Exit criteria:** root cause identified and named (not "I moved things and it stopped"), fix in place, regression test committed.

---

## W4 · Watchdog Runaway Investigation
> **✂️ Free sample ends here.** The full toolkit continues with:
> **Workflows W4–W12** (watchdog, heisenbug, review gate, MISRA, power, protocols, OTA, CI/HIL, release) · **130+ prompt templates in 12 chapters** · **25 printable checklists**.
>
> Get the full **Complete Engineering Workflow Toolkit** (bilingual EN/中文) → https://wild0408.github.io/ou-digital-studio/
