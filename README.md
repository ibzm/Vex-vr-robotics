# Vex-vr-robotics
##  Project Overview
This project contains a Python-based solution for the VEXcode VR platform. The robot is programmed to autonomously explore, map, and escape a randomly generated Dynamic Wall Maze without any prior knowledge of the layout. 

**Video Demonstration:** [https://youtu.be/l829YHjpxCc]

---

## Requirements Met
i wrote this to cover all the Basic and Advanced requirements of the assessment:
- **Basic:** Navigates corridors without hitting walls and successfully escapes the randomly generated maze.
- **Advanced 1 (Map the Maze):** Saves a map of the paths the robot actually explored in its memory, and prints out a clear, text-based grid of this map to the console when it finishes.
- **Advanced 2 (Return Home):** Automatically calculates the shortest path from the exit back to the starting coordinate `(0,0)` and drives back.
- **Advanced 3 (Quickest Route):** After returning to base, the robot draws a green line along the  shortest, most efficient route out of the maze.

---

## Theory & Algorithms Used

To make the robot fast and save memory, this project combines a simple wall-following rule (to explore the maze) with a pathfinding algorithm (to calculate the fastest way out).

### 1. Exploration: The Right-Hand Rule
For the initial exploration phase, the robot uses a Right-Hand Rule algorithm. In a simply connected maze (a maze with no detached walls or loops), keeping the "right hand" on the wall guarantees the robot will eventually find the exit. 
* **Sensor Logic:** The robot checks its Front Distance Sensor before making any forward movements. If the distance to an object is greater than 250mm (one exact grid tile), it moves forward. If not, it registers a wall and turns.
* **Saving Time & Battery: or Stopping Early:** By stopping exploration the moment the exit is found, we prioritize speed and battery efficiency over mapping the entire board. It finds the shortest known path rather than wasting time mapping useless dead-ends on the opposite side of the maze.

### 2. Mapping: Adjacency List 
Instead of pre-allocating a massive 2D array and filling it with empty space, the robot maps the maze dynamically using an **Adjacency List** stored in a Python dictionary. 
* **Memory Optimization:** The robot only records the safe, open floor tiles it actually drives on. 
* **Wall Detection:** The algorithm  knows where the walls are because a wall is simply a boundary where no open path exists in the memory graph.
* **Dynamic Bounding (Auto-Crop):** When printing the map to the console, the algorithm scans the memory for the minimum and maximum X/Y coordinates visited. It only prints the relevant  box of the explored space, acting as an auto-crop tool.

### 3. Pathfinding: Breadth-First Search (BFS)
Once the exit is found, the robot runs a Breadth-First Search (BFS) algorithm on its generated Adjacency List. 
* Because every tile in the VEX VR maze takes the exact same amount of time to cross, the graph is "unweighted." 
* BFS is the ideal algorithm for unweighted grids. It explores outward evenly in all directions step-by-step. Because of this even spread, it naturally trims out all the long dead-ends the robot accidentally explored earlier, returning an array of coordinates representing the absolute shortest path between the Start and the Exit.

### 4. Modular Movement (Modulo Math)
To translate the BFS coordinate array into physical robot movements, a custom `follow_path()` function was written. It calculates the required heading (0=North, 1=East, 2=South, 3=West) and subtracts the robot's current heading. By applying **modulo 4 (`% 4`)** to the difference, the code instantly calculates exactly how many 90-degree right turns the drivetrain needs to execute to face the correct tile.

---

## How to Run
1. Open [VEXcode VR](https://vr.vex.com/).
2. Select **File** > **New Text Project**.
3. Copy and paste the contents of `main.py` from this repository into the editor.
4. Select the **Dynamic Wall Maze** Playground.
5. Open the **Monitor** tab to view the Print Console.
6. Press **Start**.

## References & Examples Used
- **Wall-Following / Maze-Solving Concepts:** [Example 1](https://www.youtube.com/watch?v=xh4l72Lf8W0&t=323s)
- **Maze Solving Algorithmic Approaches:** [Example 2](https://www.youtube.com/watch?v=itAMR71XPWc&t=1988s)
- **Breadth-First Search (BFS) in Python:** [Implementation Tutorial](https://www.youtube.com/watch?v=KdpngPsPRlE)
- **Graph Representation in Python:** [Adjacency List Data Structures](https://www.youtube.com/watch?v=04oDTZmMNGk)
