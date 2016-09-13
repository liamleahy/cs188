# Artificial Intelligence Contest
## By: Liam Leahy

### Here's a video of a complete game against an opponent.
Unfortunately, we don't have any recordings of games against other bots made by other people because all the recordings were deleted before we knew about it.

 Click the image to be redirected to youtube.
 
[![Youtube Video of Contest Bot in Action](https://img.youtube.com/vi/JZCilVwfRps/0.jpg)](https://www.youtube.com/watch?v=JZCilVwfRps)


This contest was spread out over the course of the semester. Installments were added gradually as new concepts were introduced and we learned more about AI. I worked with my partner, Stephen Qu, on it.

In short, this contest involved playing a game that is similar to capture the flag. There are two teams that start on opposite corners of a Pac-Man map. Each team has two "agents" whose goal is to collect food pellets from the opponent side and return home with them. The game starts with an agent taking a move. However, the opponent can "capture" the agent if that agent is on the opponent's side. Therefore, agents must avoid enemy agents when collecting. The first team to bring at least all but two of the opponents food wins the game. To further complicate things, there is a "power pellet" on each side. If eaten, the team that eats it becomes temporarily invincible and able to consume the opposing team everywhere.

# Our Project Summarized (and overly simplified)
* Employed an offensive-centric strategy that focused on collecting pellets and returning as quickly as possible, with little regard for defending own side's pellets.
* Analyzed map during preprocessing time for choke points and traps. Combined with a good evaluation function for expectimax, allowed our agents to avoid being captured almost 100% of the time when not outnumbered. 
* Choice of strategy is determined by state of the board, and execution of strategy is determined by search with evaluation function optimized for the specific task, such as identifying and collecting clusters of pellets.
* Bayes' net inference and assigning strategies to each agent performed by an "overmind" class that tracks and evaluates the current chosen strategy of each agent at each step.
* State of opponent is inferred by comparing observations between each agent's move. For example, exact location of an opponent can be inferred if a food went missing between turns, even if the opponent is not directly observable.
* Creating "plans" from the clusters and the opponent positions.
* Food "clusters" were dynamically computed which were then used to divide the grid into partitions for more efficient consumption, coordinated between agents.
* Several graphs were constructed which were then used to compute things such as optimal paths between points using A*, approximate tours of food clusters, and "safepoints" and "bottlenecks" which were locations to avoid or to prefer in various circumstances. 

[start_board]: https://raw.githubusercontent.com/liamleahy/cs188/master/boardimage.png "Image of Board"
[distributions]: https://raw.githubusercontent.com/liamleahy/cs188/master/probdistributions.gif "Probability Distribution Coloring on Board"
[clusters]: https://raw.githubusercontent.com/liamleahy/cs188/master/clusters.png "Clusters Found During Game"

![Image of Board][start_board]

There are many different ideas and systems that work together to make it possible for our bot to function as it does. There were time restrictions so we could not simply sit and solve the board for 30 minutes prior to the game starting. Instead, we are given 15 seconds at the beginning of the game and then at most 1 second per turn to return a move. Additionally, there is also a vision restriction. Despite being able to see the entire board for food and power pellets, we cannot clearly observe the location of each of the opponent agents if they are more than 5 spaces away from the closest friendly agent. Instead, we are given a probability distribution of their positions.

* We start by processing the entire board. This takes the longest time (3 seconds of the maximum 15) to complete. This processing involves:
    * Creating a graph representation of the entire board.
    * Finding "bottlenecks", or "choke points" basically dangerous areas that could get an agent stuck. These are used for both offense and defense in that we try to avoid them on offense, but we try to take advantage of them on defense.
    * Creating particle filters for the opponent positions which are continually updated each time we make a new observation concerning the probability distribution over the opponent positions. The coloring of our beliefs concerning the opponent positions can be seen colored in the gif below. ![Probability Distribution Coloring on Board][distributions]
    * Dividing the food into "clusters" so that the agents more efficiently split up the food. The clusters are shown below as the highlighted squares. The different colors show the difference between the clusters for each of our bots. ![Clusters Found During Game][clusters]

* Each turn, the current plans for the agents are reviewed and potentially updated given new knowledge about things like the board, score, opponent positions, etc. The optimal move for a "plan", or sequence of moves, is computed using expectimax along with other algorithms that we came up with.
 
