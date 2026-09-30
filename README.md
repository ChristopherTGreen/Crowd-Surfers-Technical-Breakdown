# Crowd-Surfers-Portfolio
Snippet and technical breakdown of the elements worked on for the game, Crowd Surfers, made by GDA.

# :roller_skate: Crowd Surfers - My Role as a Tool Engineer and Game AI Programmer

The purpose of this README, is to serve as a means of explaining my contributions to the game, Crowd Surfers, developed by GDA (Game Design & Art Collaboration Club) at UCSC. Due to not owning the repository, and for brevity, here is a technical breakdown, along with the context, organized until I can get a portfolio site up given my allotted time as a student. 

Link to the project: https://game-design-art-collab.itch.io/crowd-surfers

## :busts_in_silhouette: Roles

### Tool Engineer
My first role during the first half of development and some of the second, was a Tool Engineer. Specifically, I worked on finding out what pipelines were the longest which could be fixed and improved upon efficiently within engine in relation to work between departments. Frequently, I had to communicate and relay requirements and specifications from Design or Programming departments on what was, or was not feasible, to art, for any asset creations. An important lesson I did learn during this time, was not to overthink a problem, and continuously problem solve it for several hours or days straight, led to an interesting case of almost fainting while problem solving tree assets during a work jam session. 

### Game AI Programmer
My second role during the second half of development, was a Game AI programmer, focusing on the Crowd AI for the game. Because no one had tried making the AI for our game, I ended up in a position where I started out being a sole developer on the initial feature. Later on, once the bones were established, tasks were being completed, and the scale of the system growing, I was able to ask and recruit some more programmers onto the team, helping onboard them and basically work together to implement different features. 

## :memo: Quick Breakdown and Overview
### Asset Creation Tool
- Engineered a meter-based vector system instead of scalar bounds to optimize designer workflows in editor
- Using linear algebra and vector/matrix transformations for image splicing, placement and hitbox creation
- Handled data tracking to prevent memory leaks or ghost objects
### Crowd AI
- Implemented main-group to sub-group structure for crowds
- Amortized the merging process
- Partial A* with path splicing
- Spatial Partitioning and Culling

# Technical Breakdown, Struggles, and Planning
## :wrench: Tool
### Asset Pipeline
During one of our early playtests, some programmers were trying to create assets (due to the art pipeline trying to provide sprites, but sadly only right before deadlines), the process was extremely painful. There were around 5 people just watching someone try to suffer converting the size of sprites, scalars, and position alignment, to create a basic building. Taking initiative, I thought, surely there must be a better way to do this which wasn't too difficult to learn, and anyone can pick up. Experimenting, I found Godot's @tool, which allowed a script to run in editor, and influence objects outside of runtime. We could have used blender, but due to the design specifications, and the amount of developers unfamiliar with blender (and others who did know too busy with the main character rigging), we needed a compromise. 

### Calculations
The biggest issue with asset creation, was the fact Godot used mostly scalars, with no dedicated meter system, and if one object in a hierarchy was a different scale to 1.0, every object's scale ended up messed up. Furthermore, the sprite3D ended up scaling based on the sprite's pixel size, resulting in a mess of figuring out a standard for sizes. Later, this caused a massive issue, where the entire map and player was scaled around 16 times the intended size for the engine, resulting in many physics equations being difficult to follow or reaching floating point errors much more easily.

The tool I made, handled the scalars on its own, keeping a meter based system as its root, which considered an objects pixel size, and made the image sit at 1.0 meters, long, wide, and tall. Furthermore, art gives us cut up sprites, for the front of a building, top of a building, or if it was a bus, all sides of the bus. Moving one sprite, means adjusting more, or changing a size means adjusting the rest, and to bypass this, I calculated all the positional alignments and rotational logic required, including stairs, to improve the process of development. By the end I had probably 20 pages, some are below.

<img width="228" height="305" alt="Calculations1" src="https://github.com/user-attachments/assets/f35caba5-f747-4775-9526-bf3d3b204b0d" />
<img width="228" height="305" alt="Calculations2" src="https://github.com/user-attachments/assets/75a62f8f-8b42-4c1c-856d-b9fd9eac37c6" />

Looking back at my code, there are a lot of improvements and coding patterns I learned later which I would have applied, because my code was a mess. Later we had to change from an orthogonal, top-down 3D environment emulating 2D, to an orthogonal, 45° 3D environment emulating 2.5D. Switching the tool to accommodate wasn't hard, part of me kind of wished we had just onboarded people to blender for the process, but the benefit of the tool, was that all the toggles needed for any scripts were set automatically for designers, and shaders worked for the sprite3Ds. 

Before

<img width="135" height="93" alt="before1" src="https://github.com/user-attachments/assets/90fc1d6e-b530-481d-bdd4-811ae1fce3d5" />
<img width="106" height="93" alt="before2" src="https://github.com/user-attachments/assets/bb636b3d-d914-4e97-afd1-125c67c578e6" />

After

<img width="125" height="93" alt="after2" src="https://github.com/user-attachments/assets/9b7c95e1-c127-4656-9516-3fd145f76bfa" />
<img width="106" height="93" alt="after3" src="https://github.com/user-attachments/assets/9c7f80fb-4774-458a-8f52-2b173287ef38" />
<img width="84" height="93" alt="after4" src="https://github.com/user-attachments/assets/036abf95-b165-4fb0-95ad-fcff722847e8" />
<img width="122" height="93" alt="after1" src="https://github.com/user-attachments/assets/7ad40ce6-b1c1-4d48-a4e7-57217e17dd54" />

## :brain: Game AI
### Ideation
Due to design requirements, and the need for early work on levels, level designers were told the AI would be blob based, or an area which slowed the player down upon entering. In the background, I was curious how possible it was to actually create a more dynamic, more impactful crowd while keeping performance high. Having the opportunity to go to GDC, I attended a Game AI roundtable, and one developer from Sucker Punch, detailed how their AI improved from their first to their second game. They went from individual minded AI to a unified, group brain. Along with dinners with some amazing developers from AAA companies, I started figuring out the crowd system for our game.

### Architecture
The crowd system works with a main and sub brain structure, where each member of the group, followed a main anchor, one who did not collide with the player, only the world, which was the only rid to have a nav agent attached. The needed velocity was shared to each member, but as a guide. When the player collides with a member of the crowd, not the anchor, they can be removed from the main group, and need to remerge with their crowd. That disconnected member looks for any other members nearby, and forms a subgroup with allocated sub-anchors, which have their own nav agent trying to remerge with the main group. As they move, back, they merge with any other sub groups who are close enough, creating a unique snake effect for crowds moving in dense areas or moving around buildings. 

To further optimize the system, I amortized checking sub groups and members for their positions. For example, a group of 100, the script only checks the 1st 10, then the next 10, and then the next 10, eventually looping at the end. Furthermore, taking an example from StarCraft and Command & Conquer, I cut off the search of the A* algorithm early, instead of finding the best path fully, and only finds a new path when reaching too far away from the next point, or when reaching the end of the current given path. Finally, there is a basic spatial partition system, which checks if the player is close enough to one of their waypoints, to enable the physics and script, or to disable the current main process until later. 

### Late Development Chaos
5 Days before release, reports of bad performance started reaching me. 2 Days later I checked out the issue, and apparently performance had gotten 4 times as bad, going from roughly 48-60 to 12-15, sometimes lower from what I recall. Apparently, a push was made, built on a separate branch, over the course of time, and it was a physics issue, meaning the profiler was having immense trouble figuring out what the issue was. It was a Find Collision issue, and I only figured it out after a group binary search through the 100 pushes in our GitHub, and noticing it exponentially got worse when multiple crowds collided with each other. Due to bit masking and audio, meant for when the player was near a crowd to play sounds of chatter, the area3Ds, being in (can't remember well) layer 1 or mask 1, the same layer as the world collisions itself, there was a massive issue with the physics engine. Basically, the area3D was not necessarily reading the crowds, but the crowds could read the area3Ds, even though there was no reason for them to read the area3D. Because of 2 area3Ds being detected, it triggered both the broad phase of collision checking for an engine, and the narrow phase, resulting in extensive checking of every single member of a crowd.

An example of how many checks were being made, there was a section of the map of around 12 crowds, each with 2 area3Ds, and 65 members roughly. The total checking required would be around 3120 times, but if there was a chance their area check was big enough to hit other crowd groups, they could be checking around 6-24 total areas, instead of 2. Averaging it out, there would be 4680--18720 checks in every collision check. Despite using primitives, the narrow phase of the physics engine still took a beating, resulting in this massive performance lost. A day before release, I managed to repair the issue, and now I often get obsessed with performance or pre-mature optimization because of how bad of an issue this was, but glad it was resolved in time.

### Early Development Footage and Images
#### Video
https://github.com/user-attachments/assets/f654bb4b-8b16-40b3-b766-cc44a026aaa2

#### Image

<img width="86" height="93" alt="crowd2" src="https://github.com/user-attachments/assets/622415b0-a4f0-4887-b442-acb04964e3be" />
<img width="240" height="93" alt="crowd3" src="https://github.com/user-attachments/assets/2558105d-1608-41eb-b926-626f495e17f2" />
<img width="157" height="93" alt="crowd1" src="https://github.com/user-attachments/assets/287cc6c9-e33f-446d-89c0-4c0ea4b9a615" />


# Summary
Looking back, I would have definitely used more programming techniques like builders, templates, and encapsulation, but given the knowledge I had during the time, and having little experience in game engines, it could have gone much worse. Figuring out how best to optimize the code, or even figuring out the right architecture for the crowd was an amazing experience. Moments where someone brought up where my code struggled, and how to improve it, even if I didn't understand it at first, helped point out where I struggled as a programmer. Honestly, I can't wait for the next GDC!
