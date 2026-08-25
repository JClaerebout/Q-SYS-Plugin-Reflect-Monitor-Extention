# Q-SYS Plugin - Reflect Monitor Extension

## Overview

The **Reflect Monitor Extension Q-SYS Plugin** monitors selected controls on a named Q-SYS component and reports their state through a standard Q-SYS Monitoring Proxy.

It captures a baseline configuration, detects later value changes, updates the Monitoring Proxy status, and creates monitoring log entries with a configurable severity. This is useful for detecting configuration changes or unexpected state changes in components that do not provide the required monitoring behavior themselves.

## Features

- Discovers named components and Monitoring Proxy components in the design
- Monitors between 1 and 32 selected controls
- Displays the current value of every selected control
- Supports a user-friendly alias for each monitored control
- Captures and locks a baseline configuration
- Highlights values that differ from the locked baseline
- Reports changed values through a Q-SYS Monitoring Proxy
- Sends a configurable log entry when a value changes
- Supports `error`, `normal`, and `warning` log severities
- Passes through the selected component's `Status` state when available
- Saves the locked configuration and baseline on the Core

## Plugin Information

| Property | Value |
| --- | --- |
| Name | Reflect Monitor Extension |
| Version | 1.0.0 |
| Author | Jens Claerebout |
| Integration | Named Q-SYS components |
| Monitoring target | Q-SYS Monitoring Proxy |

## Requirements

- A source component with a unique Code Name
- A Q-SYS Monitoring Proxy with a unique Code Name
- The Monitoring Proxy must expose the standard `status`, `log.entry`, `severity`, and `trigger` controls

The source component does not need a `Status` control. When one is present, its status is passed through to the Monitoring Proxy and takes priority over baseline-change reporting.

## Configuration

### Properties

| Property | Description |
| --- | --- |
| `Number of Controls` | Number of control rows to monitor (1-32) |

### Plugin Controls

| Control | Description |
| --- | --- |
| `Component/Plugin` | Selects the named component to monitor |
| `Monitoring Proxy` | Selects the Monitoring Proxy that receives status and log updates |
| `Lock Configuration` | Captures the current values as the baseline and prevents configuration changes |
| `Control` | Selects a control from the source component |
| `Alias` | Optional display name used in status messages and default log text |
| `Value` | Displays the current source-control value and is available as an output pin |
| `LogEntry` | Message sent to the Monitoring Proxy when the value changes |
| `Severity` | Monitoring log severity: `error`, `normal`, or `warning` |

Only the repeated `Value` controls are exposed as output pins. Configuration controls are available in the plugin UI.

## Behavior

### Component Discovery

The plugin discovers components in the running design. Monitoring Proxy components are listed separately from other named components. After selecting a source component, each `Control` row is populated with its available controls.

### Baseline and Locking

When `Lock Configuration` is enabled, the plugin records:

- the selected component and Monitoring Proxy
- the selected controls
- the current value of each selected control
- aliases, log messages, and severities

The component, proxy, control, alias, log-entry, and severity fields are disabled while locked. Unlock the configuration to make changes, then lock it again to capture a new baseline.

### Change Detection

While locked, a monitored value that differs from its baseline is highlighted in orange. The Monitoring Proxy receives a non-OK status containing the changed control and its previous and current values.

A log entry is triggered once when a control first changes. If the value returns to its baseline, the changed state is cleared and a later change can create another log entry.

### Source Status

If the selected source component has a control named `Status`, its numeric status and text are passed to the Monitoring Proxy. A non-OK source status takes priority over detected value changes. If the source has no `Status` control, its status is treated as OK.

### Persistence

The locked configuration and baseline are stored in a text file in the Core's `media` directory. The state filename is derived from the selected Monitoring Proxy Code Name so separate proxy instances can maintain separate state.

## Installation

1. Place `Reflect-Monitor-Extention.qplug` in the Q-SYS plugin directory or deploy it through Q-SYS Designer.
2. Add the plugin to the design.
3. Set `Number of Controls` to the required number of monitored controls.
4. Give the source component and Monitoring Proxy unique Code Names.
5. Open the plugin's **Controls** page.
6. Select the source component and Monitoring Proxy.
7. Select the controls to monitor and optionally configure aliases, log messages, and severities.
8. Enable `Lock Configuration` to capture the baseline.
9. Deploy the design to the Q-SYS Core and verify the Monitoring Proxy status.

## Notes

- Changing the selected source component clears the configured control rows.
- The default log text is generated from the alias or control name and the value present when the control is selected.
- The Monitoring Proxy Code Name identifies the saved state and should remain unique and stable.
- Debug messages are enabled in version 1.0.0 and are written to the Q-SYS script log.

## Known Limitations

- A maximum of 32 controls can be monitored by one plugin instance.
- Control comparison is text-based, so formatting changes can be reported as value changes.
- Component and control discovery occurs when the plugin script starts or when the source selection changes.
- The plugin depends on the standard control names exposed by the Q-SYS Monitoring Proxy.

## License

MIT License

## Author

Jens Claerebout
