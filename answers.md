Neil Chaudhari
CSS 382
Answers.md file for search project

Question 1: DFS
DFS explores one branch as deeply as possible before backtracking, so the red overlay reflects that depth-first pattern rather than a spatially balanced sweep. That is roughly what I expected. Pacman does not actually move through every explored square; the overlay shows states the search algorithm considered, while Pacman only follows the final path to the goal.

Tests:
python pacman.py -l tinyMaze -p SearchAgent
python pacman.py -l mediumMaze -p SearchAgent
python pacman.py -l bigMaze -z .5 -p SearchAgent

Question 2: BFS
I implemented BFS as graph search using a queue and a visited set. States are marked visited when they are enqueued so each state is expanded at most once, and the first goal found is at the shallowest depth. 

Tests:
python pacman.py -l mediumMaze -p SearchAgent -a fn=bfs
python pacman.py -l bigMaze -p SearchAgent -a fn=bfs -z .5
python eightpuzzle.py


Question 3: UCS
I implemented uniform-cost graph search with a priority queue, where priority is the path cost so far. I track the best known cost to each state and only keep/expand entries that match the current best cost, so UCS correctly handles different step-cost functions.

Tests:
- `python pacman.py -l mediumMaze -p SearchAgent -a fn=ucs` -> total cost 68
- `python pacman.py -l mediumDottedMaze -p StayEastSearchAgent` -> total cost 1 (very low)
- `python pacman.py -l mediumScaryMaze -p StayWestSearchAgent` -> total cost 68719479864 (very high)

Question 4: A* Search
I implemented A* graph search using a priority queue with priority g(n) + h(n), where g(n) is the path cost so far and h(n) is the heuristic value. I also track the best known g-cost per state and skip stale higher-cost entries, so the algorithm remains optimal with a consistent heuristic like Manhattan distance.

Tests:
python pacman.py -l bigMaze -z .5 -p SearchAgent -a fn=astar,heuristic=manhattanHeuristic
- A*: total cost 210, nodes expanded 549

python pacman.py -l bigMaze -z .5 -p SearchAgent -a fn=ucs
- UCS: total cost 210, nodes expanded 620

openMaze behavior:
- DFS: cost 298, expanded 576 (finds a much longer path)
- BFS: cost 54, expanded 682
- UCS: cost 54, expanded 682 (same as BFS here because step costs are uniform)
- A* (Manhattan): cost 54, expanded 535 (same optimal path, fewer expansions)

Question 5: Finding All the Corners
I implemented `CornersProblem` with an abstract state representation `(position, visitedCorners)`, where `visitedCorners` is a 4-boolean tuple indicating which corners have been reached. The start state marks the initial corner as visited when applicable, the goal test checks whether all four booleans are true, and successors update only position/corner-visit information (no full GameState stored).

Tests:
python pacman.py -l tinyCorners -p SearchAgent -a fn=bfs,prob=CornersProblem
- total cost 28, nodes expanded 252

python pacman.py -l mediumCorners -p SearchAgent -a fn=bfs,prob=CornersProblem
- total cost 106, nodes expanded 1966

Question 6: Corners Problem Heuristic
I implemented a non-trivial consistent heuristic that returns the maximum Manhattan distance from Pacman's current position to any unvisited corner. It is admissible because Manhattan distance never overestimates true path cost in a maze, and consistent because a single move changes Manhattan distance by at most 1.

Test:
python pacman.py -l mediumCorners -p AStarCornersAgent -z 0.5
- total cost 106, nodes expanded 1136

Question 7: Eating All The Dots
I implemented `foodHeuristic` as the maximum maze distance from Pacman's current position to any remaining food dot, with distance values cached in `problem.heuristicInfo`. This is non-trivial, non-negative, and consistent: maze distance is a true shortest-path metric, so one move can reduce the estimate by at most 1.

Tests:
python pacman.py -l testSearch -p AStarFoodSearchAgent
- total cost 7, nodes expanded 10

python pacman.py -l trickySearch -p AStarFoodSearchAgent
- total cost 60, nodes expanded 4137

Question 8: Suboptimal Search
I implemented `findPathToClosestDot` by solving an `AnyFoodSearchProblem` with BFS, after defining the goal test as reaching any food square. This makes the agent greedily pick the nearest dot each time.

Test:
python pacman.py -l bigSearch -p ClosestDotSearchAgent -z .5
- path cost 350
