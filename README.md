# LaneSwitchRL-Simulation
RL-based simulation of lane-switching in traffic

## Environment

1. Number of lanes: `5`
2. `Actions`: Switching between adjacent lanes
3. `Clearance Rate`: No. of meters travelled per unit time
4. We observe the relative clearance rate between lanes as they are dependent on each other.
5. Side lanes 1 and 5 only have one adjacent lane (2 and 4). If their relative clearance rate is lower than the neighbours then 0.2 will be added to the clearance rate of the side lanes and subtracted from the neighbours.
6. The converse is true for point 5.
7. Lanes 2, 3 and 4 are affected by both it's neighbours.
8. The changes in points 5, 6 and 7 are computed concurrently.
9. Everything outside consideration is modelled by uncertainty term `N(0,0.1)` as well as `random_event`.
10. The environment clearance rate is modelled by the equation:  
    `vt,j = vt-1,j + N(0,0.1) + random_event + 0.2 × sgn(vt-1,j-1 − vt-1,j) + 0.2 × sgn(vt-1,j+1 − vt-1,j)`  
    Note: If no adjacent lane exists, the sgn() function can be ignored.
11. `Random Event`: A 5% chance per time step for a lane to experience a slowdown of 20%~50% (uniform probability distribution) of vt-1,j, or 5% chance that the clearance rate is increased by 20~40% (also uniform probability distribution) of vt-1,j.
12. `Unit Time`: This refers to every 10 seconds.
13. Decision is based on historical records of `past three time steps` (t-2, t-1, t).
14. The `state` contains three tuples of seven values corresponding to his location dt away from the destination, his lane lt, and the clearance rate vt,j for each of the 5 lanes (j 𝜖 {1,2,3,4,5}).
15. `Reward Calculation`: 
- The reward is based on the distance covered per time step minus a fixed penalty (-10) for each time step. 
- An additional penalty of -5 is applied for lane-changing attempts (whether successful or not).
16. `Action Space`: 
- The agent can attempt to move left (-1), stay in the current lane (0), or move right (1).
- If the action is impossible (e.g., moving left in the leftmost lane), Ah Tan stays in the lane.
17. `Initial Distance`: The distance from the destination (dt) is initialized randomly at the beginning of each episode within a specified range (e.g., 3000 to 5000 meters). The distance decreases over time based on the clearance rate of the lane Ah Tan is in. This was not explicitly mentioned in the problem statement.
18. `Lane Change Success Rate`: When Ah Tan attempts to change lanes, there is only a 50% chance of success. Regardless of whether the lane change succeeds or fails, the penalty for attempting a lane change (-5) is applied. If the lane change fails, Ah Tan remains in the same lane for that time step.
19. `End Condition`: The episode ends when Ah Tan reaches his destination, i.e., when the distance to the destination (dt) becomes less than or equal to zero. At this point, the episode is considered done.