# Crowd-Surfers-Portfolio
Snippet and technical breakdown of the elements worked on for the game, Crowd Surfers, made by GDA.

# Crowd Surfers - My Role as a Tool Engineer and Game AI Programmer

The purpose of this README, is to serve as a means of explaining my contributions to the game, Crowd Surfers, developed by GDA (Game Design & Art Collaboration Club) at UCSC. Due to not owning the repository, and for brevity, here is a technical breakdown, along with the context, organized until I can get a portfolio site up given my allotted time as a student. 

## Roles

### Tool Engineer
My first role during the first half of development and some of the second, was a Tool Engineer. Specifically, I worked on finding out what pipelines were the longest which could be fixed and improved upon efficiently within engine in relation to work between departments. Frequently, I had to communicate and relay requirements and specifications from Design or Programming departments on what was, or was not feasible, to art, for any asset creations. An important lesson I did learn during this time, was not to overthink a problem, and continuously problem solve it for several hours or days straight, led to an interesting case of almost fainting while problem solving tree assets during a work jam session. 

### Game AI Programmer
My second role during the second half of development, was a Game AI programmer, focusing on the Crowd AI for the game. Because no one had tried making the AI for our game, I ended up in a position where I started out being a sole developer on the initial feature. Later on, once the bones were established, tasks were being completed, and the scale of the system growing, I was able to ask and recruit some more programmers onto the team, helping onboard them and basically work together to implement different features. 

## Tool
### Asset Pipeline
During one of our early playtests, some programmers were trying to create assets (due to the art pipeline trying to provide sprites, but sadly only right before deadlines), the process was extremely painful. There were around 5 people just watching someone try to suffer converting the size of sprites, scalars, and position alignment, to create a basic building. Taking initiative, I thought, surely there must be a better way to do this which wasn't too difficult to learn, and anyone can pick up. Experimenting, I found Godot's @tool, which allowed a script to run in editor, and influence objects outside of runtime. We could have used blender, but due to the design specifications, and the amount of developers unfamiliar with blender (and others who did know too busy with the main character rigging), we needed a compromise. 

### Calculations
The biggest issue with asset creation, was the fact Godot used mostly scalars, with no dedicated meter system, and if one object in a hierarchy was a different scale to 1.0, every object's scale ended up messed up. Furthermore, the sprite3D ended up scaling based on the sprite's pixel size, resulting in a mess of figuring out a standard for sizes. Later, this caused a massive issue, where the entire map and player was scaled around 16 times the intended size for the engine, resulting in many physics equations being difficult to follow or reaching floating point errors much more easily.

The tool I made, handled the scalars on its own, keeping a meter based system as its root, which considered an objects pixel size, and made the image sit at 1.0 meters, long, wide, and tall. Furthermore, art gives us cut up sprites, for the front of a building, top of a building, or if it was a bus, all sides of the bus. Moving one sprite, means adjusting more, or changing a size means adjusting the rest, and to bypass this, I calculated all the positional alignments and rotational logic required, including stairs, to improve the process of development.



## Key Systems Built

### Player ↔ Bike State System
- Designed a dual-entity system where control dynamically transfers between player and vehicle
Managed visibility, physics, and input handling across multiple states
### Enemy AI (State-Based)
- Implemented AI behavior using structured states (chase, fire, death)
Designed positioning logic to maintain optimal combat distance
Built adaptive targeting (bike vs player depending on state)
### Combat System
- Reusable projectile (bullet) system shared across multiple enemy types
Timer-based firing logic with positional calculations
### Physics Handling Solution
- Solved collision issues between player and enemies by dynamically toggling immovability
Prevented physics conflicts while maintaining gameplay functionality

## Technical Highlights
- State machine-driven behavior for player, bike, and enemies
- Reusable systems across multiple entities
- Real-time physics problem solving and constraint handling

# Running the Project
To run the project in your local environment, follow either these two steps:
Quick Method:
  1) Access the github pages for Blade Cycle
  2) Click and run the link for the page

Longer Method:
  1) Clone the repository to your local machine.
  2) Run VScode.
  3) Download the live server extension.
  4) Run the live server on your local machine.
