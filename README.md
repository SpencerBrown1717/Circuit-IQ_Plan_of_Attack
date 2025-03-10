# Circuit-IQ_Plan_of_Attack
Text to PCB 

                   +-------------------+
                   |    Boss Agent     |
                   +--------+----------+
                            |
                            | Distributes tasks via
                            | message passing
                            v
    +------+------+------+------+------+------+
    |      |      |      |      |      |      |
    v      v      v      v      v      v      v
    
+------+ +------+ +------+ +------+ +------+ +------+     +---------+
|Copper| |Copper| |Solder| |Solder| |Silk- | |Drill | --> |  Gerber |
| Top  | |Bottom| |Mask T| |Mask B| |screen| |Agent | --> |   ZIP   |
+------+ +------+ +------+ +------+ +------+ +------+     +---------+ 




# CircuitIQ Technical Features

## AI/LLM Integration
- Uses Anthropic's Claude API for all generative tasks
- Tailored prompts for each specific PCB layer
- System prompts to enforce valid Gerber output format
- Error handling and retries for API calls

## Parallel Processing
- Pipeline architecture with three distinct stages:
  1. Core layers (top and bottom copper)
  2. Dependent layers (silkscreen and solder mask)
  3. Final layers (drill and manufacturing)
- Concurrent processing within each stage for efficiency

## Intelligent Caching
- Response caching based on context hashing
- Avoids redundant LLM API calls
- Memory-efficient storage of previous results

## Context Sharing
- Information sharing between layers for consistency
- Extraction of component positions, via placements, and other critical data
- Ensures alignment between different PCB layers

## Validation & Quality Control
- Gerber code validation against manufacturing requirements
- File format checks for compatibility with industry standards
- Required command presence verification
- Proper file termination checking

## File Management
- Organized file structure for output
- ZIP packaging for convenient downloading
- Flexible cleanup options
- Support for datasheet processing as additional context

## Error Handling
- Robust exception handling throughout the pipeline
- Graceful degradation with informative error messages
- Job status tracking for user feedback
- Retry mechanisms for transient failures


---------------------------------------------------------------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------------------------------------------------------------

Next phases after MVP works (curently can great baoard when hard coded 3.8.2025 )

Basic UI and user input exposed issues with input to "director" agent. 

Implementing Langchain to build more robust agents and give greater control (3.9.25)

Will build RAG vector databse to help with DataSheet input and extraction - to make it more like Cursor.Ai for PCB 

will need to have a 3D viewer and let user edit with NLP example move USB-C 2mm to left or remove USB-C and recreate baord. 

Below is predicted workflow for next steps after consistant (NLP via -> UI -> to Director -> to worker agents -> gerber zip) is reliably producing Gerbers that render in JLCPCB viewer. 



---------------------------------------------------------------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------------------------------------------------------------

# Pseudocode for iterative approach:

# 1. Generate initial design
board_state = boss_agent.generate_initial_layout(user_requirements)

# 2. Render board_state in a 2D/3D viewer
show_board_in_viewer(board_state)

# 3. User sees issues => NLP feedback
user_feedback = "Move the USB connector left 2 mm. Increase 5V trace to 0.5 mm."

# 4. Parse feedback into structured changes
parsed_changes = parse_feedback(user_feedback)
# e.g. [
#   {"action": "move_component", "component": "USB_connector", "dx": -2.0},
#   {"action": "update_trace_width", "net": "5V", "width": 0.5}
# ]

# 5. Apply changes to board_state
for change in parsed_changes:
    board_state = apply_change(board_state, change)

# 6. Re-run relevant worker agents
board_state.copper_layer = copper_agent.re_route(board_state)
board_state.silkscreen = silkscreen_agent.update_labels(board_state)
...

# 7. Show updated design
show_board_in_viewer(board_state)
# 8. Repeat until user is happy



