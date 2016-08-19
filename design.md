
# Resources

## User Input

_User Input_ consists only of a "time" and a "direction".
The "Direction" is one of: `U`, `D`, `L`, or `R`.
The "time" is non-cannonical and the server may decide to ignore it.

## Game State

The _Game State_ is stored in a DynamoDB for online persistence, or a local in-memory data store for the UI. It is updated _each time a user provides input_. **Not** once every frame, or every "tick" or anything like that. This results in a game with zero wasteful network requests or processing.

The game state includes two very important details for each player: The last known position and direction - with the time at which the user input was recorded.

In this way, the UI can continue re-rendering the same state over time, moving the little snakes along their merry path, assuming that as no new user input has occurred -- the snakes are still continuing on their last known paths.

### Generating a new Game State

`Old State + User Input = New State`

At the time of generating a new state, the player's "Time" is set to the current time (or requested time, if within the latency allowance). And their XY coords are updated to be modified by the direction they've been heading since their last input. Their new direction is saved.

In the following example, B was (at tick 1) heading Down. At tick 2 we recieve an update that they'd like to move Left; so their coords are calculated and the new direction/time is set.

Old State (Tick #1)

    Player | Time | X | Y | D
    -------------------------
    A      | 1    | 1 | 1 | R
    B      | 1    | 3 | 3 | D

User Input

    Player: B
    Time: 2
    Direction: Left

New State (Tick #2)

    Player | Time | X | Y | D
    -------------------------
    A     | 1    | 1 | 1 | U
    B     | 2    | 3 | 2 | L

### Rendering the Game State

At a predetermined tick-rate (30hz?) the UI re-renders the state, and positions snakes based off their last known location and direction.
Eg: If the current tick is tick #4, and we use the `New State` example above, then the snakes current positions would be:

Render at tick #4:

    Player | X | Y
    --------------
    A      | 1 | 4
    B      | 1 | 2

Note that X increases rightwards, and Y increases upwards.

      4
      3   
      2 B
      1       A
    X   1 2 3 4
      Y  


# Data Flow

_User input_ immediately causes two things to happen:

- Generating a new local _Game State_, and
- Sending the user input to the User API

When the User API recieves a User Input, it grabs the old state from the DB, generates a new state, and puts that in the DB.

When the DB has a new state saved to it, a notification is sent to all clients with the new state.

When a client recieves a new state, it begins rendering that new state.


# Data Model:

game:
- id
- players
- food
- high_score
- high_score_player_name

player:
- id
- name
- input_time
- direction
- head_x
- head_y
- tail_parts

tail_part:
- x
- y

food:
- x
- y

