# ⚙️ AI for Embedded Systems: Engineer-Grade Prompt Toolkit

## 130+ production-tested prompt templates for firmware, debugging, hardware & IoT work

> Every template follows the **CONTEXT + ROLE + TASK + CONSTRAINTS + OUTPUT** structure used by working embedded engineers.
> Copy → fill the [brackets] → paste into ChatGPT / Claude / Gemini / DeepSeek / any LLM.
> Written by an embedded developer for embedded developers — not generic AI fluff.

---

## 📦 Product Info

| Item | Detail |
|------|--------|
| Name | AI for Embedded Systems: Engineer-Grade Prompt Toolkit |
| Content | 130+ templates in 12 chapters + 6 advanced combo workflows |
| Format | Markdown (Obsidian / Notion / Typora) + PDF |
| Audience | Firmware engineers, IoT developers, hardware startups, students |
| Coverage | STM32 · ESP32 · C/C++ · RTOS · debugging · protocols · build systems · CI |
| Updates | Free lifetime updates (V2 adds FreeRTOS deep-dive + Zephyr pack) |

---

## Contents

1. Firmware Development (STM32 / ESP32 / bare-metal)
2. RTOS & Task Scheduling (FreeRTOS)
3. Debugging: Hard Faults, Watchdogs & Heisenbugs
4. Datasheets & Register-Level Work
5. Code Review & Refactoring
6. Hardware Bring-Up & PCB / Schematic Support
7. Communication Protocols (UART / I2C / SPI / CAN / USB / MQTT)
8. Build Systems & Toolchains (CMake / Make / PlatformIO / VSCode)
9. Testing, CI & Hardware-in-the-Loop
10. Documentation & Technical Writing
11. AI-Assisted Workflow Recipes (power-user combos)
12. 12 Common AI Mistakes Embedded Engineers Make

---

# 1. Firmware Development

## 1.1 Generate an STM32 HAL peripheral init

```
Target: STM32[F103/F407/G0/G4/...], HAL library, CubeMX-style init.
Peripheral: [USART2 / I2C1 / SPI1 / TIM3 / ADC1 / DMA...].
Requirements: [baud rate / clock / pinout / interrupt or polling].
My task: write the C init function + usage example.
Constraints:
1. Use HAL functions only, no LL unless asked
2. Handle error returns (HAL_StatusTypeDef)
3. Configure NVIC and clock enabling
4. Add comments explaining each configuration field
Output: one .c snippet with init + a minimal usage example in main().
```

**Example:**

```
Target: STM32F103C8, HAL, USART2 at 115200 8N1, PA2=TX PA3=RX, polling read.
Task: init function + blocking echo loop.
```

## 1.2 ESP-IDF project scaffold

```
Role: senior ESP32 firmware engineer.
Task: outline the file structure and key files for an ESP-IDF project that
does [WiFi + MQTT + OTA + [sensor over I2C]].
Deliver:
1. Directory tree (components/ layout)
2. sdkconfig defaults I should set (partition table for OTA, flash size)
3. The event loop skeleton: WIFI_EVENT, IP_EVENT, MQTT_EVENT handlers
4. Error-handling pattern for esp_err_t
Constraints: ESP-IDF v[5.x], C language, no Arduino framework.
```

## 1.3 Bare-metal state machine

```
Design a [button debounce / UART parser / menu navigation] state machine.
Language: C, no RTOS, for [MCU].
Deliver:
1. Enum of states + events
2. State transition table (Markdown)
3. Event queue design (ring buffer) if needed
4. Implementation sketch with no blocking waits
Constraints: interrupt-safe, no malloc, <[1KB] RAM.
```

## 1.4 Power optimization

```
Analyze my power budget: MCU [STM32L4], running [LoRa sleep/wake cycle].
Current: sleep [X] uA, active [Y] mA @ [Z] MHz, wake every [T] seconds.
Task: give a concrete plan to reduce average current, covering:
1. Clock config (MSI/HSI16, voltage scaling)
2. Peripheral gating (which peripherals to turn off)
3. Sleep mode choice (STOP2 vs STANDBY) + wake source
4. Code patterns (enter low-power before sleep, disable unused IRQs)
Output: prioritized list by expected power saving, with code snippets.
```

## 1.5 Flash wear leveling / EEPROM emulation

```
I need to store [config values, count N=64, each 4 bytes] in internal flash
of [STM32G0] (page size 2KB, 128KB total).
Design a simple wear-leveling scheme: sector layout, write strategy,
read strategy, and the C API (struct + functions).
Constraints: survive power loss at any moment, max [100k] erase cycles,
use HAL_FLASH. Output: header + implementation + state diagram.
```

## 1.6 Bootloader skeleton

```
Design a dual-bank / [app-boot] bootloader for [STM32] with OTA via [UART/ESP].
Deliver:
1. Memory map (bootloader region, app region, swap/flag area)
2. Boot decision flow (check flag + CRC, jump to app with correct MSP)
3. OTA receive loop: framing, checksum, write-to-bank, set flag, reset
4. App-side changes needed (linker script, vector table relocation)
Constraints: bootloader < [16KB], no RTOS, robust to interrupted transfer.
```

---

# 2. RTOS & Task Scheduling

## 2.1 FreeRTOS architecture review

```
Here is my task list and their requirements:
- [task A: 1 kHz, deadline 1ms, from ISR]
- [task B: 50 Hz, jitter < 5ms]
- [task C: rare, heavy, 100ms compute]
- [task D: blocking on UART]
Task: propose task priorities, stack sizes (estimate), and which
synchronization primitive to use for each (queue / semaphore / event group /
task notification). Explain priority-inversion risk and how to mitigate.
```

## 2.2 ISR → task handoff pattern

```
Show the correct FreeRTOS pattern for a [timer/interrupt] feeding a task:
1. ISR: minimal work + BaseType_t xHigherPriorityTaskWoken + xSemaphoreGiveFromISR
2. Task: block on semaphore with timeout, process, report via queue
3. Pitfalls: don't call vTaskDelay in ISR, don't use printf in ISR
Deliver: full compilable example with both files (isr.c, task.c).
```

## 2.3 Deadlock / priority inversion diagnosis

```
I have a system with tasks [A,B,C], mutexes [M1,M2], queue [Q].
Symptoms: [task B stops responding after X minutes].
List the 5 most likely causes in FreeRTOS, ordered by probability, and for
each give: how to confirm (which trace/config), and the fix. Assume
configUSE_MUTEXES=1, no priority inheritance explanation needed beyond basics.
```

## 2.4 Timing analysis

```
Task: compute worst-case response time for my task set:
[Task, period, WCET, deadline, priority]
Use rate-monotonic feasibility check (Liu & Layland) + response time analysis.
Show the math, tell me if the set is schedulable, and what to change if not
(reorder priorities? merge tasks? move work to ISR?).
```

## 2.5 Stack usage estimation

```
I have tasks: [describe]. MCU: [STM32F4, 192KB RAM].
Estimate realistic stack sizes including ISR nesting (max [2] nested ISRs,
each ~[256]B), printf usage ([yes/no]), and math libs. Show the formula and
a table. Also tell me how to verify with [FreeRTOS task stack high-water mark /
MCU stack pointer monitoring] and add the debug code.
```

---

# 3. Debugging

> **✂️ Free sample ends here.** The full toolkit contains chapters 1–12: RTOS & scheduling, register-level work, code review, hardware bring-up, protocols, build systems, testing/CI, documentation, power-user AI workflows, and the 12 common AI mistakes.
>
> Get the full 130+ template pack → [zaowu digital studio](https://wild0408.github.io/ou-digital-studio/)
