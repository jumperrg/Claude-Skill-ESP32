ESP32 Embedded Architecture Skill (v1.0)

SCOPE
This skill defines strict coding rules for:

- ESP32 only
- Arduino framework via PlatformIO
- FreeRTOS-based architecture
- LVGL UI systems (optional)
- ESP-IDF usage only when strictly necessary

---

SYSTEM ARCHITECTURE

1. Task-per-file rule (STRICT)
   Each RTOS task MUST be placed in its own .cpp file.

Rules:

- 1 file = 1 task = 1 responsibility
- No mixed subsystems

Example:
appTask.cpp
wifiTask.cpp
bluetoothTask.cpp
sensorTask.cpp

---

2. AppTask (SYSTEM BRAIN)
   There is exactly ONE central controller: AppTask.

Responsibilities:

- Receive all system commands via FreeRTOS queue
- Execute switch(cmd)
- Manage system-wide state transitions
- Control UI screen switching

Forbidden:

- Any other task modifying system state
- Direct UI calls outside AppTask

---

3. AppTask LOOP PATTERN (MANDATORY)

AppCommand cmd;

while (1)
{
if (xQueueReceive(appCommandQueue, &cmd, portMAX_DELAY) == pdTRUE)
{
switch (cmd)
{
case CMD_SWITCH_TO_SCR_MAIN:
// system action
break;

      case CMD_SWITCH_TO_SCR_BT:
        // system action
        break;

      default:
        break;
    }

}
}

---

4. COMMUNICATION RULES

Allowed:

- FreeRTOS queues
- Event groups (if needed)

Forbidden:

- Direct task-to-task function calls
- Shared mutable global state between subsystems

---

FREE RTOS RULES

When to use FreeRTOS:

- More than one subsystem exists
- Any asynchronous behavior exists:
  WiFi, BLE, UI, sensors, audio, networking

Task isolation rule:

- Each task runs independently
- Never block system-critical tasks
- Communicate only via queues/events

---

NAMING CONVENTIONS

Files:

- camelCase.cpp (appTask.cpp, wifiTask.cpp)

Classes:

- UpperCamelCase (AppTask, WifiController)

Functions:

- camelCase

Variables:

- camelCase

---

CODE STYLE

- Indentation: 2 spaces
- Prefer minimal code
- Avoid over-engineering
- Keep logic explicit

---

CLEANUP RULE

Every ~10 meaningful changes:

- remove unused code
- remove dead variables
- remove obsolete functions
- remove outdated comments
- simplify logic
- update context.md

---

LIBRARY RULES

Arduino vs ESP-IDF:

- Arduino is DEFAULT
- ESP-IDF allowed ONLY when necessary:
  - Arduino API insufficient
  - low-level hardware control required
  - performance-critical operations

Rule:
Prefer Arduino. Use ESP-IDF only when strictly necessary.

---

LIBRARY MANIFEST (MANDATORY FILE)

All highly preferred libraries are defined in:
libraryManifest.md

Rule:

- Prefer libraries listed in manifest
- Any other library is allowed, but under review

If missing functionality:

- Use any library

---

FORBIDDEN PATTERNS

- Multiple tasks in one file
- System logic outside AppTask
- Direct inter-task function calls
- Hidden global state systems
- Unapproved library usage

---

BEHAVIORAL RULES

MUST:

- enforce task-per-file structure
- use AppTask as system controller
- use queue-based communication
- respect naming conventions
- prefer minimal solutions
- validate against libraryManifest

MUST NOT:

- invent new architecture patterns
- bypass AppTask
- introduce hidden dependencies
- use non-whitelisted libraries
- over-engineer abstractions

---

RESULT

Deterministic ESP32 firmware architecture with strict RTOS separation, controlled dependencies, and minimal reproducible design.
