
The iCE40 FPGA PICO is a compact Lattice iCE40UP5K FPGA core board with an onboard LPC11U35 programming/UART interface, Raspberry Pi Pico-compatible pin arrangement, and hardware intended for HDL, FPGA, and RISC-V soft-core experiments.

* **FPGA:** Lattice iCE40UP5K
* **Programming interface:** Onboard LPC11U35 with USB Type-C FPGA programming and UART communication
* **Expansion:** 25 digital I/O pins on the current documented board
* **Onboard resources:** RGB LED, DS18B20U temperature sensor, and two high-speed comparators
* **CPU experiments:** Supports RISC-V soft-core porting
* **Development:** Supports open-source FPGA toolchains

## Why Lattice ICE40UP5K FPGA core board, LPC11U35 programmer, RISC-V porting.

Small FPGA boards are often divided into two extremes. At one end are bare FPGA modules that expose logic pins but expect the user to provide a programmer, USB-to-UART bridge, clocking, test peripherals, and sometimes configuration storage before a first design can be loaded. At the other end are large teaching boards with many fixed peripherals, proprietary programming workflows, and board-specific interfaces that can make it harder to move a design into another project. The iCE40 FPGA PICO is intended to sit between those approaches: it keeps the programmable-logic platform compact while integrating the development functions required for ordinary experiments.


The board is aimed at students learning Verilog and digital logic, FPGA developers building small protocol or signal-processing blocks, makers who want programmable logic in a Pico-style form factor, and developers exploring **RISC-V soft processors implemented inside an FPGA**. The current board material also identifies an RGB LED, a DS18B20U temperature sensor, and two high-speed comparators that can be used for experiments such as Sigma-Delta ADC implementation and frequency counting. These resources make it possible to move beyond a basic LED example without immediately designing a separate carrier board.

The Raspberry Pi Pico-compatible pin arrangement is useful when building experiments around an existing Pico-style physical ecosystem, but electrical and functional compatibility should not be assumed from the mechanical layout alone. This is an FPGA board rather than an RP2040 board, and not every Pico accessory or software library can be transferred directly. Earlier reference material also describes limitations around analog-oriented Pico accessories, so each expansion should be checked against the current pin map and I/O voltage requirements before connection.

The board is not the right choice for a developer who needs a large FPGA, high-speed transceivers, external DDR memory, PCIe, a hardened application processor, or a turnkey Linux-capable SoC. RISC-V support here refers to implementing a **soft CPU inside the FPGA fabric**, not to an onboard hard RISC-V processor. Likewise, the two comparators can support useful measurement-oriented experiments, but they do not turn the core board into a calibrated laboratory ADC, oscilloscope, or frequency counter by themselves. For larger signal-processing designs or very high I/O counts, a higher-density FPGA platform may be more appropriate.

## Quick Start

The normal first-use path is USB power and FPGA configuration through the onboard LPC11U35 interface. The exact software version and example directory need to match the current repository revision, so verify the setup documents before installing a toolchain or overwriting the factory configuration. The steps below deliberately distinguish confirmed board behavior from details that still require repository-specific documentation.

### 1. Connect USB and Power

1. Place the board on a non-conductive surface and inspect the USB Type-C connector, FPGA, LPC11U35, expansion pins, and surrounding components for shipping damage.
2. Use a **data-capable USB Type-C cable** and connect the board to a computer. The Type-C connection is documented for power, FPGA programming, and UART communication.
3. Leave external expansion hardware disconnected for the first power-up. This prevents a wiring error or incompatible Pico accessory from being mistaken for a board problem.

What you should expect is a board that powers normally, does not become unusually hot, and exposes the documented programming interface to the host. Do not assume that an unlit RGB LED means the board is unpowered, because that LED is an FPGA-controlled user resource rather than necessarily a dedicated power indicator. A charge-only USB cable is a common first-boot problem: it can power the board while preventing programming and UART enumeration.

### 2. Install the Toolchain or IDE

The board documentation identifies **Verilog HDL** as the primary FPGA design language and references both Lattice tooling and open-source FPGA development workflows. Lattice Radiant is one documented vendor-tool option for iCE40 development, while the board is also intended to work with open-source FPGA toolchains.

4. Open the reference project from [`project/`](project/) and select the **iCE40UP5K** target corresponding to the populated package.
5. Build the project once without editing the RTL. A clean reference build is more useful for validating the environment than starting with a new empty project.
6. Check the build log for the expected device, constraint file, clock definition, and pin mapping before programming the board.

> [!IMPORTANT]
> Do not select a different iCE40 device just because the project still synthesizes. The logic capacity, memory resources, package pins, configuration settings, and constraints must match the populated iCE40UP5K device and the actual PCB revision.


### 3. Run the First Example

Start with the smallest committed example that exercises only the FPGA and one onboard output. An RGB LED example is a good hardware smoke test because the board includes a dedicated RGB LED, but the exact pin mapping must come from the current constraint file rather than an older board revision.

2. Confirm that the project uses the current iCE40UP5K board constraint file.
3. Build the FPGA bitstream using the documented toolchain.
5. Reset or power-cycle the board only if the documented workflow requires it.
6. Observe the RGB LED or other documented output and compare its behavior with the example README.

Once the first hardware example works, test UART communication before moving on to comparators, the temperature sensor, or a RISC-V soft core. Doing this in layers separates programmer issues from RTL issues and RISC-V software issues. A soft-core project introduces several additional pieces—CPU RTL, memory initialization, firmware compilation, linker configuration, and UART output—so it should not be the first diagnostic test for a new board.

### Troubleshooting First Boot



**Programming succeeds, but the expected peripheral does not respond.** The most likely first checks are the constraint file, clock definition, pin assignment, and active-high/active-low behavior. Do not copy pin numbers from an older UP5K Pico revision without checking the current schematic. Current product material identifies 25 digital I/O pins, while earlier documentation for a prior revision described a different exposed-I/O count.

## Board Overview

The iCE40 FPGA PICO combines programmable logic, FPGA configuration, serial communication, a few useful experimental peripherals, and Pico-style expansion in one small board. The FPGA remains the center of the design; the LPC11U35 is there to make development and communication easier rather than serving as the application processor. This separation is useful when teaching FPGA architecture because user designs still run in the FPGA fabric rather than being delegated to a hidden host MCU.

### Main FPGA

The primary device is the **Lattice iCE40UP5K**, part of the iCE40 UltraPlus FPGA family. It provides programmable logic, embedded memory, dedicated signal-processing resources, clocking resources, and hardened interface blocks that can be used by an RTL design. The exact resource utilization of an application depends on synthesis, place-and-route, and the selected soft IP. For RISC-V projects, part of this FPGA fabric is used to implement the processor itself, with the remaining resources available for memories, peripherals, buses, timers, custom accelerators, and other logic.

### USB and Development Connector


### LPC11U35 Programmer / UART Interface


### Onboard User Resources

The current board documentation identifies **one RGB LED**, **one DS18B20U temperature sensor**, and **two high-speed comparators**. The RGB LED is useful for combinational logic, counters, PWM, state-machine output, and simple debug status. The DS18B20U provides a practical target for implementing a digital temperature-sensor interface directly in FPGA logic. The comparators can be used as building blocks for experiments such as Sigma-Delta ADC conversion and frequency counting.

### Expansion Headers


### Power Circuitry


## Hardware Features

![Onboard resources](images/resources.png)

The board intentionally provides enough built-in hardware to exercise several FPGA design patterns without turning the core module into a large fixed-peripheral training board. The sections below separate FPGA resources from board-level peripherals because the FPGA datasheet capabilities and the PCB's physically exposed resources are not the same thing. Always use the schematic and board constraint file to determine what is actually connected.

### Main IC

* **Lattice iCE40UP5K FPGA** — programmable-logic device used for all user HDL designs and soft-core implementations.
* **RISC-V soft-core support** — suitable for porting compatible open-source RISC-V processors into FPGA logic rather than relying on a hard CPU integrated into the board.
* **Embedded FPGA memory resources** — available to RTL designs and soft processors for buffers, state, ROMs, FIFOs, and application memory according to the selected architecture.
* **DSP resources** — available for arithmetic-oriented FPGA designs such as filters, DDS logic, multiply/accumulate operations, or other signal-processing experiments.
* **FPGA clocking resources** — available for generated and derived clocks within the constraints of the device and board clock source.
* **Hardened peripheral resources** — the iCE40 UltraPlus architecture includes FPGA resources intended to simplify selected serial-interface implementations.


### Onboard I/O

* **1 × RGB LED** — convenient output for first-boot tests, PWM, status indicators, counters, and state-machine exercises.
* **1 × DS18B20U temperature sensor** — provides a real digital sensor interface for timing-sensitive protocol experiments and temperature acquisition.
* **2 × high-speed comparators** — intended for experiments including Sigma-Delta ADC implementation and frequency counting.

The board-level peripherals should be treated as examples of what FPGA logic can control rather than as fixed-function devices. For example, the temperature sensor is useful not only for displaying temperature but also for learning bidirectional/open-drain bus control and timing state machines. Similarly, a comparator can feed a simple counter, a period-measurement engine, or a digital feedback loop depending on the RTL loaded into the FPGA.

### Expansion and Programming

* **USB Type-C** — used for normal power, FPGA programming, and UART communication.
* **LPC11U35 onboard programmer** — avoids the need for a separate external programmer in the documented workflow.
* **Raspberry Pi Pico-compatible physical pin arrangement** — useful when designing compact carriers or adapting suitable Pico-style digital peripherals.
* **25 digital I/O pins** — current documented exposed digital-I/O count.
* **UART communication** — available through the onboard development interface.
* **Open-source FPGA toolchain support** — documented as a supported development direction for the board.
* **RISC-V soft-core porting** — allows FPGA logic and a software-programmable CPU to coexist in one design.

> [!NOTE]
> "Pico-compatible pinout" describes the board's physical/pin-layout concept. It does not mean that RP2040 firmware, MicroPython libraries, or every electrically connected Pico accessory will work on an FPGA design without implementing the required interface logic.

### Power

USB Type-C is the safest documented starting point for powering the board during development. The FPGA, programmer, and user I/O operate from board-generated power rails, but the current source set does not provide a complete current-board power-path specification. Do not apply an external voltage to an expansion power pin until the current schematic confirms the permitted direction and voltage.

* **USB power input:** USB Type-C

> [!WARNING]
> Do not connect 5 V logic directly to FPGA I/O unless the current schematic and iCE40UP5K I/O configuration explicitly permit it. A 5 V power pin and a 5 V-tolerant signal pin are not the same thing.

## Board Dimensions

The final mechanical drawing should show the PCB outline, USB Type-C overhang, component keep-out areas, header center lines, and any mounting-hole locations. If headers are supplied or pre-soldered, the assembled height should be listed separately from bare-PCB thickness. Connector clearance should also be documented for users placing the module inside an enclosure or above a carrier PCB.

| Mechanical item          | Current value                                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------- |

Do not scale dimensions from product photographs. Perspective correction, image resizing, and connector protrusion can all produce incorrect mechanical values. Use the PCB CAD or controlled STEP/drawing file as the authoritative source.

## Pinout

The pinout is the main reference for any external hardware connected to the iCE40 FPGA PICO. Current product material identifies **25 digital I/O pins** and a Raspberry Pi Pico-compatible pin arrangement. A complete pin map must identify both the physical connector position and the corresponding iCE40UP5K package pin so that RTL constraints remain traceable to the actual hardware.

An important revision issue exists in the historical material: an earlier UP5K Pico reference described **28 expansion I/O pins**, while the current product image states **25 digital I/O pins** and also shows additional onboard resources such as the DS18B20U and two comparators. This strongly suggests that pin usage or the board revision has changed. Use the current schematic and current constraints for new projects rather than copying an old `.pcf`, `.lpf`, or pin table without checking it.

The final pin documentation should identify:

* All 25 user-accessible digital I/O signals
* RGB LED connections
* DS18B20U data connection
* Comparator outputs and any associated input connectors
* UART transmit/receive signals
* FPGA configuration/programming signals
* Clock input
* Reset/control signals
* Ground pins and power rails
* Any pins reserved by configuration Flash or the LPC11U35


The iCE40UP5K provides configurable FPGA I/O, so the electrical behavior of an exposed pin also depends on the selected I/O standard in the FPGA project. Do not assume that a pin is safe at 5 V because it occupies the same physical location as a 5 V-tolerant peripheral on another board. The pinout should therefore be used together with the project's constraint file and the device electrical limits.

> [!WARNING]
> The current source package does not include verified per-pin voltage tolerance or maximum current limits for this PCB revision. Treat external signals as belonging to the documented FPGA I/O voltage domain and use level shifting whenever the external logic level is not confirmed compatible.

> [!TIP]
> Keep one board constraint file as the single source of truth for pin assignments and import it into every example. Duplicating pin numbers manually across many projects is an easy way to create revision-specific bugs.

## Applications

![Applications](images/applications.png)

The value of a small FPGA core board is not a single fixed application; it is the ability to replace hardware behavior by changing RTL. The onboard peripherals and programming interface make several project categories practical without a large amount of supporting hardware.

### FPGA and HDL Coursework

The board can serve as a compact target for learning combinational logic, counters, finite-state machines, synchronous design, clock domains, PWM, and reusable Verilog modules. Beginning with the onboard RGB LED keeps the hardware setup simple while students learn the build/program/debug loop. Later exercises can introduce the temperature sensor, UART, and comparator inputs without replacing the FPGA platform.

### RISC-V Soft-Core Experiments


### SPI, I2C, UART, and Custom Protocol Logic

FPGA logic is useful when a protocol requires deterministic timing, unusual framing, several interfaces running in parallel, or a custom state machine. UART communication through the onboard development interface provides a convenient host-facing debug channel. SPI and I2C concepts can be implemented in RTL or explored through suitable FPGA resources and external modules. Each interface example should document the exact expansion pins used and whether those pins conflict with onboard resources.

### Temperature-Sensor Interface Design

The onboard **DS18B20U** provides a practical protocol target without adding a separate sensor module. A Verilog state machine can handle reset, presence detection, commands, timing slots, data capture, and conversion of the returned value. More advanced examples can add CRC checking, averaging, UART output, or a RISC-V software interface.

### Sigma-Delta ADC Experiments


### Frequency Counter and Timing Measurement

Comparator outputs can also feed FPGA counters and timing logic. A design can count transitions over a known gate interval, measure periods between edges, or build reciprocal-frequency measurement logic. The FPGA is well suited to these tasks because several counters and state machines can run concurrently without interrupt latency. The actual measurable input range depends on the comparator input circuit and clocking, so no frequency limit should be published until the hardware example is characterized.

### Pico-Style Expansion and Rapid Prototyping

The Pico-style layout allows the core board to be placed on compatible mechanical carriers and custom experiment boards. This can shorten prototype wiring when the external hardware uses digital signals supported by the current pin mapping. Compatibility still needs to be checked at the electrical and logical level because an RP2040 accessory may depend on software libraries, ADC functionality, or specific RP2040 peripheral behavior that an FPGA design does not provide automatically.

### Custom Hardware Accelerators and Hobbyist Logic Projects

The board can also be used for small custom accelerators, LED engines, waveform generation, digital audio logic, protocol bridges, counters, encoders, small displays, and hobbyist CPU architectures. FPGA logic is useful when several operations need to happen independently at the same time. The iCE40UP5K is a relatively small FPGA, so designs should be built with resource usage in mind rather than assuming the capacity of a large desktop FPGA board.

## Factory Demo

![Factory demo](images/demo.gif)


At minimum, a useful self-test would identify whether the FPGA configures successfully, whether the clock is running, and whether the onboard RGB LED can be controlled. If the shipping demo also accesses the DS18B20U, high-speed comparators, or UART interface, the expected behavior for each resource should be listed explicitly. A UART self-test is especially useful because it checks the path between the FPGA design, LPC11U35, USB connection, and host terminal.


> [!NOTE]
> Do not treat a factory LED animation as proof that every expansion I/O pin or analog/comparator path has been tested. If the production process includes a more complete board test, document that procedure separately under `docs/`.

## Repository Structure

```text
.
├── docs/       Documentation
├── examples/   Self-contained example projects
├── project/    Project files and templates
├── hardware/   Schematic and mechanical references
├── software/   PC-side utilities and drivers
└── images/     Artwork for README and docs
```

`docs/` contains user-facing setup material, pin maps, programming instructions, board-revision notes, RISC-V documentation, and deeper explanations that would make this README too large. Electrical limitations and revision-sensitive information should live here.

`examples/` contains focused FPGA projects that test one concept at a time. Each example should be self-contained and include its own README, pin usage, build instructions, expected output, and tested toolchain version.

`project/` is the primary location for FPGA project templates, board constraint files, reusable RTL structure, and toolchain configuration. The current board definition should be centralized here so examples do not maintain independent copies of the pin map.

`hardware/` contains the board schematic, PCB references, mechanical information, BOM data that can be published, and revision history. Hardware files should state clearly which production revision they represent.

`software/` contains host-side programs, drivers, LPC11U35-related utilities, terminal helpers, programming scripts, and RISC-V firmware tools that are not specific to a single FPGA example.

`images/` contains stable documentation artwork such as board layout, pinout, dimensions, application examples, package contents, and demo media. README image filenames should remain stable where possible to avoid breaking external links to the documentation.

## Documentation

The following repository-relative locations should become the primary documentation entry points:

* [Documentation directory](docs/)
* [Project templates](project/)
* [Hardware references](hardware/)
* [Host-side software](software/)
* [Example projects](examples/)








> [!NOTE]
> Historical UP5K Pico documentation may describe a different I/O count or board resource set. Documents committed to this repository should state the PCB revision they apply to so users do not accidentally mix an earlier 28-I/O reference with the current 25-digital-I/O board.

A documentation file should also identify the toolchain version used when screenshots, commands, or generated project files were created. FPGA tools can change device databases, command-line syntax, generated metadata, and project formats between releases. Recording the version is therefore part of making an FPGA example reproducible.

## Examples

The exact `examples/` directory listing was not included in the source package used to generate this README, so no project is marked complete without verification. The repository should prioritize examples that isolate a single subsystem before combining them into larger designs. This makes the board easier to debug and makes the examples more useful as reusable engineering references.

Suggested/expected example structure:

* [ ] `rgb-led` — first-build test using the onboard RGB LED
* [ ] `uart-loopback` — verifies FPGA-to-LPC11U35 UART communication
* [ ] `counter` — synchronous counter and clock-divider fundamentals
* [ ] `pwm-rgb` — PWM control of the RGB LED
* [ ] `ds18b20u` — reads the onboard temperature sensor
* [ ] `comparator-counter` — counts transitions from a comparator input
* [ ] `sigma-delta-adc` — demonstrates a comparator-based Sigma-Delta acquisition path
* [ ] `spi-master` — minimal SPI controller example
* [ ] `i2c-master` — minimal I2C controller example
* [ ] `riscv-hello` — RISC-V soft core with UART "hello world"
* [ ] `riscv-gpio` — RISC-V software controlling FPGA-mapped GPIO
* [ ] `factory-self-test` — production-style test of documented onboard resources


Each example should build from a fresh clone without requiring files from another example directory. Shared board definitions should come from a documented common package or template rather than being copied and modified independently. RISC-V examples additionally need the exact CPU core revision, compiler version, linker script, memory map, firmware build command, and expected UART output.

> [!TIP]
> Keep the first FPGA example smaller than the first RISC-V example. If an LED design cannot be synthesized, programmed, and run reliably, adding a CPU, memory map, firmware compiler, and serial console only makes the problem harder to isolate.

## Software Compatibility

The board documentation supports FPGA development through a vendor tool flow and indicates compatibility with open-source FPGA toolchains. The exact tested version matrix still needs to be committed. Treat the table below as a compatibility framework rather than a promise for an untested host/tool combination.

| Environment                                  | Primary use                                              | Host support                                            | Status / requirements                                                                                  |
| -------------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |

For Windows, document whether the programming interface is recognized automatically and how the UART appears in Device Manager. If a driver package is required, link only to the official or repository-provided source.



For the open-source flow, record the exact synthesis, place-and-route, packing, and programming versions used by CI or release builds. FPGA toolchains evolve, and reproducibility is much better when a known working environment is documented instead of simply saying "install the latest version."

## Package Contents

The current retail package list was not included in the supplied metadata. Only the core board itself can be treated as confirmed from the available product material. Accessories should not be inferred from photographs or from older training-platform bundles because those may contain expansion boards and modules that are separate products.

* **1 × iCE40 FPGA PICO core board** — Lattice iCE40UP5K FPGA with onboard LPC11U35 programming/UART interface

If this product is sold both as a core board and as part of a larger learning kit, maintain separate package lists for those SKUs. Do not use a photograph of the full learning platform as the package image for the core-board-only SKU. This distinction is especially important because older project documentation describes optional peripherals such as displays, LEDs, buzzers, analog circuits, and other training-board resources that are not necessarily mounted on the core board itself.

## FAQ

### Hardware and I/O

### Which FPGA is installed on the board?


### How many digital I/O pins can I use?

Current product material specifies **25 digital I/O pins**. An older UP5K Pico reference described 28 expansion I/O, so do not assume that an older pin table applies to the current board. The current schematic and constraint file should be treated as authoritative.

### Is it electrically identical to a Raspberry Pi Pico?

No. The board uses a Pico-compatible physical pin arrangement, but it is built around an iCE40UP5K FPGA rather than an RP2040 microcontroller. The available peripheral functions, software environment, ADC behavior, and pin electrical configuration are therefore different. Always verify a Pico accessory against the current FPGA pinout before connection.

### Are the FPGA pins 5 V tolerant?


### What is the DS18B20U used for?

The onboard DS18B20U gives the FPGA a real digital temperature sensor to communicate with without an external module. It is useful for implementing a sensor-protocol state machine, timing logic, data conversion, CRC processing, and RISC-V peripheral interfaces. The exact FPGA pin connection should be taken from the current pin map.

### What are the two high-speed comparators for?

The current board material identifies the comparators for **Sigma-Delta ADC** and **frequency-counter** experiments. A comparator converts an analog threshold crossing into a digital event that FPGA logic can process with deterministic timing. The actual input range, protection, bandwidth, and analog network must be documented before quantitative measurement performance is claimed.

### Programming and Development

### Do I need to buy a separate FPGA programmer?

Not for the normal documented programming workflow. The board integrates an **LPC11U35 programmer** and uses the USB Type-C connection for FPGA configuration. An external programmer may still be useful for low-level recovery or hardware development, but the current source material does not identify one as required for ordinary use.

### Can the same USB connection be used for serial debugging?


### Which HDL should I use?

The reference material identifies **Verilog HDL** for FPGA development. Other HDL front ends should only be documented after they have been tested with the repository's chosen synthesis flow. Examples should stay consistent about language and tool versions so new users can reproduce them.

### Can I use an open-source FPGA toolchain?


### Does Lattice Radiant work with this board?

Lattice Radiant is referenced as a development environment for iCE40 devices and is part of the documented learning material. The project should specify the exact tested release before users create or migrate project files. The onboard programmer workflow also needs to be documented separately from synthesis and place-and-route.

### RISC-V

### Does the board contain a RISC-V processor?

There is no separate hard RISC-V application processor described on the board. Instead, the iCE40UP5K can host a **RISC-V soft core**, meaning the processor is constructed from FPGA logic and memory resources. This makes the board useful for studying CPU architecture and hardware/software co-design.

### Which RISC-V core should I use?


### Can I add custom hardware around the RISC-V CPU?

Yes—that is one of the main reasons to put a soft processor in an FPGA. You can place custom GPIO, timers, protocol engines, counters, PWM blocks, or application-specific logic beside the CPU and expose them through a memory-mapped or custom bus. The exact interconnect and software API depend on the selected RISC-V system.

### Revision and Expansion

### Can I use Raspberry Pi Pico expansion boards?

Some digital Pico-style expansion hardware may be usable when the mechanical pin position, I/O voltage, signal direction, and implemented FPGA logic all match. Compatibility is not automatic because this board does not contain an RP2040 and does not reproduce every Pico peripheral in hardware. Check the current pinout before plugging in a module.

### Why do some older documents say 28 I/O while current material says 25?

The available sources appear to describe different board revisions. The current product image explicitly lists **25 digital I/O pins**, while an earlier UP5K Pico reference describes 28 expansion I/O. This repository should identify the shipping PCB revision and keep revision-specific schematics and constraint files separated so the two mappings are never mixed.

### Where should I start if the FPGA cannot be programmed?


## License

A finalized license was not included in the source package used for this README. Until one or more license files are committed, the repository contents should be treated as **all rights reserved** rather than automatically assumed to be open-source simply because source files can be viewed or downloaded.

If the project is intended to become formally open source, a component-specific licensing model is recommended. Software and example code could use a permissive license such as MIT where appropriate. Hardware design sources could use CERN-OHL or another hardware-focused license, while documentation and original artwork could use CC-BY or CC-BY-SA depending on the project's sharing requirements.


## Contributing

Contributions should be reproducible against a clearly identified hardware revision. When filing a bug, include the **PCB revision, FPGA project or commit, toolchain name and version, operating system, programmer connection, and exact reproduction steps**. For hardware-related failures, also state what is connected to every relevant expansion pin.

Documentation fixes are welcome, particularly corrections to pin mapping, revision differences, toolchain commands, and electrical limitations. Changes to pin numbers or voltage limits should be supported by the current schematic, constraint file, or controlled hardware documentation rather than by an older screenshot or another iCE40 board.

New examples should be self-contained and build from a clean clone. Each example needs a short README describing its purpose, hardware connections, required tools, exact tested toolchain version, build/programming sequence, expected result, and troubleshooting notes. RISC-V examples should additionally document the CPU core revision, firmware compiler, memory map, linker script, and UART configuration.

Hardware changes should be discussed in an issue before a major PCB or pinout redesign begins. This is especially important for changes to USB/programming signals, FPGA configuration Flash, clocking, power rails, comparator inputs, DS18B20U wiring, or the Pico-style expansion interface because those changes can invalidate many examples at once.

Keep pull requests focused: ideally one logical change per PR. A new comparator example should not also reorganize the entire repository, replace unrelated images, and change the RISC-V core. Smaller changes are easier to review, test on real hardware, and carry between board revisions.
