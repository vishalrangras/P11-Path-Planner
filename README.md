### Path Planning Project ###

Submitted by - Vishal Rangras

##### Path Planning of autonomous car on Highway using Behavior Planning, Finite Statement Machine, Cost Optimization and Trajectory Generation #####

The goals for this project are to:

1. Utilize ego car localization data and the sensor fusion data of all the vehicles on the different lanes of highway.
2. Plan the maneuver of vehicle based on possible states.
3. Compute the cost of each possible maneuver based on different parameters like speed of vehicle, distance from the front car and rear car on different lanes, lane change action, etc. 
4. Generate the valid trajectory to drive the car such that it does not violate maximum speed, maximum acceleration and maximum jerk criteria.
5. The car should not collide with any other cars on the highway and should be able to drive at least 4.32 miles without any incident.
6. The car should be able to change the lane if another lane has less or no traffic and its safe to change the lane.
7. The car should stay in its lane except when it is changing the lane.

**[Rubric](https://review.udacity.com/#!/rubrics/1020/view) Points**

### Building the Project and Execution ###

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./path_planning`.
5. See the results in simulator

#### Attributions ####

* I am grateful to Udacity's content team for creating such a wonderful project walkthrough, covering crucial aspects of the project and discussing various implementations approaches along with frequently asked questions from various students at the end of walkthrough video.

* My project implementation is based on the approach discussed in the project walkthrough video and I have adopted some ideas from the work of SDCND students Jeremy Shanon, Oleg Potkin and Rana Khalil. I am also thankful to forum mentors, my classroom mentor and other fellow students for their valuable support through different mediums like slack and discussion forum.

### Reflection ###

* Apart from the code discussed in project walkthrough, I have used the concept of finite state machine and cost calculation to generate the trajectory of driving.

* I have implemented and used the helper function `getCarsOfTargetLane()` to process the sensor fusion data and created a collection of all cars for the given lane. I have also used another helper function `getClosestDistanceFromCar()` to identify which is the closest car in front of or behind my car and what is the distance between my car and that closest car.

* Then in my main() method, from line 310 to 352, I have analyzed the sensor data and processed information of all the cars which are driving in the same direction as my car. Using this information, I have computed how many cars are present in each of the 3 lanes of my driving direction along with what is the average traffic speed of each lane.

* From line 389 to 419, I have analyzed the sensor data of the cars which are present in my current lane. I have then checked if any of the present car is at a distance lesser than the acceptable distance or not. If there is any car which is at a shorter distance, then I have set the flag `too_close` to `true` and noted the speed of that vehicle.

* The code block residing from line 422 to 501 contains the logic about what to do if any car is too close to our car in order to avoid the collision and maintain a safe trajectory. This code is divided into the following two parts:

	* **1. Finite Possible State Computation:** From line 429 to 444, I am setting the possible lane values based on which lane I am currently driving in. This logic ensures to prevent maximum jerk violation. For example, if the car is driving at its speed limit of 50 mph in the left most lane, it should not decide to get into right most lane otherwise the passengers may observe a jerk as the car may try to change 2 lanes abruptly in the small time horizon. The right approach for the car would be to first get into the middle lane and then only try to get into the right most lane.

	* **2. Cost Computation and picking best lane to drive:** The code written from line 447 to 501 computes the cost of each lane based on various parameters like: 1. If the target lane for which we are computing the cost requires a vehicle to change its lane. 2. Average speed of the target lane. 3. Distance between my vehicle and the closest car on the target lane. Based on comparing the cost of each possible lane, we then pick the lane with the least cost as the best cost lane and continue to drive on that lane.

**What to do if lane change is not feasible and there is a vehicle too close?**

* In the scenario when all the possible lanes are crowded and there is no way to change the lane, the vehicle starts decelerating so that it can avoid collision. This logic is written from line 491 to 493.  

* In fact I have also provided the deceleration logic immediately at the beginning of `too_close==true` block so that the vehicle does not collide to the front vehicle while it is computing cost for all the lanes and deciding among which lane to pick. Without this extra deceleration logic, initially the car was having collision at some or the other point after completion of 4.32 miles. After adding this logic, I was able to drive upto 11 miles without any collision and after that I turned off the simulator so not sure if it would collide ever.

**Speeding up when the way is clear**

* The code lines from 504 to 507 contains the logic of speeding up when there is no vehicle in front of us at a distance of at least 30 meters and the path is clear to accelerate the vehicle in order to achieve the speed limit of 50 mph.

**Tuning:**

1. The maximum jerk violation can be controlled using the lane change cost value.

2. The collision violation can be controlled using the closest distance cost value and the speed cost value.

3. The maximum acceleration violation was taken care by incrementing or decrementing the `ref_vel` by the value 0.224. 

### Results ###

[image1]: ./img/SS1.png "Result 1"

[image2]: ./img/SS2.png "Result 2"

[image3]: ./img/SS3.png "Result 3"

[image4]: ./img/SS4.png "Result 4"


<h3 align="center"> Decelerating when lane change is not possible and vehicle is too close <h3>

![alt text][image1]


<h3 align="center"> Changing to the right most lane from the middle lane <h3>

![alt text][image2]


<h3 align="center"> Changing to the left most lane from the middle lane <h3>

![alt text][image3]


<h3 align="center"> Best distance without incident : 9.04 miles <h3>

![alt text][image4]


#### Main car's localization Data (No Noise)

["x"] The car's x position in map coordinates

["y"] The car's y position in map coordinates

["s"] The car's s position in frenet coordinates

["d"] The car's d position in frenet coordinates

["yaw"] The car's yaw angle in the map

["speed"] The car's speed in MPH

#### Previous path data given to the Planner

//Note: Return the previous list but with processed points removed, can be a nice tool to show how far along
the path has processed since last time. 

["previous_path_x"] The previous list of x points previously given to the simulator

["previous_path_y"] The previous list of y points previously given to the simulator

#### Previous path's end s and d values 

["end_path_s"] The previous list's last point's frenet s value

["end_path_d"] The previous list's last point's frenet d value

#### Sensor Fusion Data, a list of all other car's attributes on the same side of the road. (No Noise)

["sensor_fusion"] A 2d vector of cars and then that car's [car's unique ID, car's x position in map coordinates, car's y position in map coordinates, car's x velocity in m/s, car's y velocity in m/s, car's s position in frenet coordinates, car's d position in frenet coordinates. 

## Details

1. The car uses a perfect controller and will visit every (x,y) point it recieves in the list every .02 seconds. The units for the (x,y) points are in meters and the spacing of the points determines the speed of the car. The vector going from a point to the next point in the list dictates the angle of the car. Acceleration both in the tangential and normal directions is measured along with the jerk, the rate of change of total Acceleration. The (x,y) point paths that the planner recieves should not have a total acceleration that goes over 10 m/s^2, also the jerk should not go over 50 m/s^3. (NOTE: As this is BETA, these requirements might change. Also currently jerk is over a .02 second interval, it would probably be better to average total acceleration over 1 second and measure jerk from that.

2. There will be some latency between the simulator running and the path planner returning a path, with optimized code usually its not very long maybe just 1-3 time steps. During this delay the simulator will continue using points that it was last given, because of this its a good idea to store the last points you have used so you can have a smooth transition. previous_path_x, and previous_path_y can be helpful for this transition since they show the last points given to the simulator controller with the processed points already removed. You would either return a path that extends this previous path or make sure to create a new path that has a smooth transition with this last path.

