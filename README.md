# Smart_Traffic_Controller
Smart traffic light controller in Verilog: modular timer + Moore FSM with density-based adaptive green time and a latched pedestrian crossing phase. Simulated in QuestaSim.
# Smart Traffic Controller (Verilog RTL)

A modular traffic signal controller with adaptive green time and a pedestrian crossing phase, written in synthesizable Verilog and verified in QuestaSim 10.7c.

## Features
- Moore FSM: GREEN, YELLOW, RED, PEDESTRIAN
- Adaptive green time from `vehicle_density`: 5 / 10 / 20 cycles
- Latched `pedestrian_request`, served after the next RED
- Parameterized timer (`WIDTH = 32`) connected to the FSM through `timer_expired`
- Asynchronous active-high reset to RED, latch-free logic

## Architecture
Clock -> Timer -> `timer_expired` -> FSM -> Lights

| Module | File |
|---|---|
| Top | `traffic_system_top_final.v` |
| FSM | `traffic_fsm_final.v` |
| Timer | `traffic_timer.v` |
| Testbench | `traffic_system_tb_final.v` |

## Timing (parameters, in clock cycles)
| Phase | Cycles |
|---|---|
| GREEN (low / medium / high) | 5 / 10 / 20 |
| YELLOW | 3 |
| RED | 8 |
| PEDESTRIAN | 6 |

## Ports
| Signal | Dir | Description |
|---|---|---|
| `clk` | in | Clock |
| `reset` | in | Async active-high reset |
| `vehicle_density[1:0]` | in | 00 low, 01 medium, 10/11 high |
| `pedestrian_request` | in | Crossing request pulse |
| `red`, `yellow`, `green` | out | Lamps |
| `pedestrian_active` | out | High during the crossing phase |

## Simulation (Model Sim)
```tcl
vlib work
vlog traffic_timer.v traffic_fsm_final.v traffic_system_top_final.v traffic_system_tb_final.v
vsim traffic_system_tb_final
add wave -r /*
run -all
