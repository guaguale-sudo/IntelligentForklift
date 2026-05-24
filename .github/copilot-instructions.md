# AI Coding Guidelines for Intelligent Forklift STM32 Project

## Project Overview
This is an STM32F103-based embedded system for controlling an intelligent forklift. It uses STM32 HAL library for peripheral abstraction. The system receives movement commands via UART (direction: 'F'/'B', speed: 1-9, button state), controls a DC motor using PWM (TIM2 CH1) for speed and GPIO (PA1) for direction.

## Architecture
- **Main Loop**: Processes action flags set by UART interrupt in `main.c` while(1) loop.
- **Peripherals**: TIM2 (PWM for motor speed), TIM3 (unused), UART1 (command input), GPIO for direction control.
- **Data Flow**: UART RX interrupt → parse 3-byte commands → set flags → main loop executes movement (set direction GPIO, PWM duty cycle, delay, stop).
- **HAL Usage**: Strict adherence to STM32 HAL functions; no direct register manipulation.

## Developer Workflows
- **Build**: Use CMake presets - `cmake --preset Debug` then `cmake --build --preset Debug` (Ninja generator, arm-none-eabi-gcc toolchain).
- **Flash**: Use OpenOCD: `openocd -f interface/stlink.cfg -f target/stm32f1x.cfg -c "program build/Debug/IntelligentForklift.elf verify reset exit"` or ST-Link CLI.
- **Debug**: Connect ST-Link, use GDB with `arm-none-eabi-gdb build/Debug/IntelligentForklift.elf` and `target remote :3333` (OpenOCD on port 3333).

## Coding Conventions
- **Custom Code Placement**: Add user code only within `/* USER CODE BEGIN/END */` sections in CubeMX-generated files (e.g., `main.c`, `stm32f1xx_it.c`).
- **HAL Patterns**: Initialize peripherals with `MX_*_Init()` functions; use HAL callbacks like `HAL_UART_RxCpltCallback` for interrupts.
- **UART Command Parsing**: Receive fixed-length commands in interrupt callback, set global flags for main loop processing (avoid blocking operations in ISR).
- **PWM Control**: Use `__HAL_TIM_SET_COMPARE(&htimX, TIM_CHANNEL_Y, duty)` for speed; start PWM with `HAL_TIM_PWM_Start`.
- **GPIO Direction**: `HAL_GPIO_WritePin(GPIOA, GPIO_PIN_1, GPIO_PIN_RESET/SET)` for motor direction.
- **Error Handling**: Call `Error_Handler()` on HAL init failures; no custom error recovery implemented.

## Key Files
- `Core/Src/main.c`: Main application logic, UART parsing, motor control.
- `IntelligentForklift.ioc`: CubeMX configuration (pins, clocks, peripherals).
- `CMakeLists.txt` / `CMakePresets.json`: Build system setup.
- `Drivers/STM32F1xx_HAL_Driver/`: HAL library sources (do not modify).

## Integration Points
- **HAL Dependencies**: Links `stm32cubemx` library with defines `USE_HAL_DRIVER`, `STM32F103xB`.
- **External Tools**: Requires arm-none-eabi-gcc, OpenOCD/ST-Link for flashing.
- **No External Libraries**: Pure HAL, no additional dependencies beyond CMSIS/HAL.</content>
<parameter name="filePath">c:\Workspace\03TECH\STM32\STM32HAL\IntelligentForklift\.github\copilot-instructions.md