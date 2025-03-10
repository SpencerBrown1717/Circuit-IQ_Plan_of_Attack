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
