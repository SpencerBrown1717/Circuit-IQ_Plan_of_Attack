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


---------------------------------------------------------------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------------------------------------------------------------

Next phases after MVP works (curently can generate board when hard coded 3.8.2025 )

Basic UI and user input exposed issues with input to "director" agent. 

Implementing Langchain to build more robust agents and give greater control (3.9.25)

Will build RAG vector databse to help with DataSheet input and extraction - to make it more like Cursor.Ai for PCB 

will need to have a 3D viewer and let user edit with NLP example move USB-C 2mm to left or remove USB-C and recreate baord. 

Below is predicted workflow for next steps after consistant (NLP via -> UI -> to Director -> to worker agents -> gerber zip) is reliably producing Gerbers that render in JLCPCB viewer. 



---------------------------------------------------------------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------------------------------------------------------------

# CircuitIQ Technical Features (curent) 

## AI/LLM Integration
- Uses Anthropic's Claude API for all generative tasks -> now langchain
- Tailored prompts for each specific PCB layer
- System prompts to enforce valid Gerber output format
- Error handling and retries for API calls

## Parallel Processing
- Pipeline architecture with three distinct stages: -> 6 worker agents not 3  but top and bottom layers are most important. 
  1. Core layers (top and bottom copper)
  2. Dependent layers (silkscreen and solder mask)
  3. Final layers (drill and manufacturing)
- Concurrent processing within each stage for efficiency

## Intelligent Caching
- Response caching based on context hashing
- Avoids redundant LLM API calls         -> was making 3 calls each time so 1 + 6 x 3 for no reason .... was expensive till fixed, would be worth it if it improved code 3 times, however it was calling it 3 times new so redundant. 
- Memory-efficient storage of previous results

## Context Sharing
- Information sharing between layers for consistency     -> is not working yet, might add colector agent for all 6 (for 2 layer baord) worker agents to make sure the text in each is effective for board. 
- Extraction of component positions, via placements, and other critical data
- Ensures alignment between different PCB layers    -> this will be a forever chalenge as LLM's improve so will this system, we can work on it but for MVP it is not fesable with highly chalenging boards. (2 layers are great, but 8 layer baord will require more agents, incresing cost and complexity but not materialy, would be a "permium" plan just add 1 - 2 agent per aditional board, 1 for the extra layer and 1 for comunicating to botom agent that it is a 4 layer baord and thus other agents need to act acordingly. 

## Validation & Quality Control
- Gerber code validation against manufacturing requirements
- File format checks for compatibility with industry standards
- Required command presence verification
- Proper file termination checking -> eyeballig it is fine for MVP, but for later stagges you will need a 2d viewr and built in algorithms for bast practices and rule checks, in thorey you could pay a fixed fee, say 500 usd for a baord, and have it run 10x or 100x or 1000 x times and check what board is the best, you take it as a great starting point and improve or augment as needed. Plan is 20 usd a month with a usage limit on agents similar again to Cursor. 

## File Management
- Organized file structure for output
- ZIP packaging for convenient downloading -> will need to add ability for zips to become "trusted" as of now they are likley riddled with security risks. 
- Flexible cleanup options
- Support for datasheet processing as additional context

## Error Handling
- Robust exception handling throughout the pipeline
- Graceful degradation with informative error messages
- Job status tracking for user feedback
- Retry mechanisms for transient failures


User experince and iteration plan of attack as of 3.9.25 - Spencer Brown 

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
#8. Repeat until user is happy - or iterate 1000 times for fixed amount of quieries, let user select model, cost and what they want to pay, claud 3.7 for 50 usd get 50 runs hypotheticaly,, deepseek for 50 usd and get 250 runs and user will recive best 5 boards. 

Prefer monthly subsription to flat fee, so user bears responability to iterate not soly relying on agents who can make simple errors at this time.



The premis is that.... if AGI is actualy coming ... and AI will be used to fight and win wars, i can't imagine them being bested by a zip file. 



