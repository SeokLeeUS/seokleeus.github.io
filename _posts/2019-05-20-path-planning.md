---
title:  "Path Planning"
categories: post
mathjax: true
---
![Path_planning](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Path_planning/path_planning_gif.gif)
## Summary: 
- The aim of this project is to determine a path for the simulation vehicle to follow. The challenge is the vehicle will encounter random traffic situation. It may face slower speed vehicle in front of it.  

## Simulation result (please click the below thumbnail to view a video):
[![video result](https://img.youtube.com/vi/FiTJYFPapfI/hqdefault.jpg)](https://youtu.be/FiTJYFPapfI) 

## Things to consider:
- how to make the car goes straight between the lane
- how to make the car follow the path 
- how to reduce jerk at the cold start
- how to reduce the vehicle speed when facing the front car to avoid collision. 
- dummy test why the title doesn't change

## Resource to tackle the remaining work:

- So far, the below is the code to change the lane to the left when there's a vehicle in front of my car:
```
if (prev_size > 0)
							{
								car_s = end_path_s;
							}

							bool too_close = false;
							for (int i = 0; i < sensor_fusion.size(); i++)
							{
								//car is in my lane
								float d = sensor_fusion[i][6];
								if (d < (2 + 4 * lane + 2) && d >(2 + 4 * lane - 2))
								{
									double vx = sensor_fusion[i][3]; // the vehicle speed in x coordinate when the vehicle is on my lane 
									double vy = sensor_fusion[i][4]; // the vehicle speed in y coordinate when the vehicle is on my lane
									double check_speed = sqrt(vx * vx + vy * vy); // absolute vehicle speed 
									double check_car_s = sensor_fusion[i][5];
									check_car_s += ((double)prev_size * .02 * check_speed); // project next s point 
									// if the car is in front of me and the gap is less than 30 meter, 
									if ((check_car_s > car_s) && ((check_car_s - car_s) < 30))
									{
										//ref_vel = 29.5;
										too_close = true;
										if (lane > 0)
										{
											lane = 0; // makes the lane change to left hand side(lane(0): d= 2 for middle waypoint of left lane
											//lane =1; d=4 for middle waypoint of the middle lane. 
										}

									}
								}
							}

							if (too_close)
							{
								ref_vel -= .224;
							}
							else if (ref_vel < 49.5)
							{
								ref_vel += .224;
							}
```
- I had conversation with Udacity instructor. Here is the summary to take into account in order to determine the next lane change:
1. Consider a (good) cost function per lane (or finite state machine) to determine lane change better or stay in the lane.Hybrid A* is not a recommended method to determine the optimum lane. 
2. Try to estimate traffic condition 5 seconds ahead to find what the best path would be. Look for behavioral planning quiz/ Gaussian Naive classifier as a reference to determine where to be in the future.
3. Smoothing the path following is done via spline function. 
4. Utilize Frenet coordinates, path smoothing via spline, then finite state machine to consider what maneuver the driver should take.  
5. Try to look for helper function which is not used in main.cpp during walkthrough to see if how it can be useful for the lane change. 

## Skill gap
1. Finite State Machine (FSM) 
2. Cost function 
3. How to utilize Finite State Machine (FSM) (or cost function) for lane change maneuver when necessary. 

## Work flow (after picking up some ideas from [Udacity's course material](https://www.youtube.com/watch?v=7sI3VHFPP0w&feature=youtu.be)):

- a. Cost function (weight factor): compute weight factor (cost function) per lane. 
As I know "d" value from sensor fusion data, when a "d" projects if there's a vehicle in front of my car, determine weight factor to penalize (closer to zero is good sign for lane change, the larger, the danger to make a lane change) by looking the distance ("s") and target speed difference. Here is an example of weight factor calculation block:
```
if (lane_1) 
            {
              double vx = sensor_fusion[i][3];
              double vy = sensor_fusion[i][4];
              double check_speed = sqrt(vx * vx + vy * vy);
              double check_car_f = sensor_fusion[i][5];
              double check_car_b = sensor_fusion[i][5];
              check_car_f += ((double)prev_size * .02 * check_speed); // project next s point 
              check_car_b -= ((double)prev_size * .02 * check_speed); //car behind
              // checking gap between front/back cars
              if ((check_car_f>car_s)&&((check_car_f-car_s)>f_g)||((check_car_b<car_s)&&(car_s-check_car_b)>r_g))  
              {
                dist_cost = exp(-abs((check_car_f-car_s)/30));
                weightfactor1  = abs((target_speed-check_speed)/target_speed) + dist_cost; // move to lane 0

              }
              else
              {
                weightfactor1 = 1.0;
              }
            }
```

- b. Finite State Machine (FSM): FSM logic works in this way. Determine if there's a vehicle in front of my car within a calibratible range (30 m by default) in a lane, then create nested if-else if- else statement to consider if it's okay to steer left or right for lane change. Otherwise, keep the lane. The decision factor is driven by the pre-calculated weighting factor. As an example (see the below code snippet), when the vehicle is in the middle lane (lane_1), but my car approaches to the front vehicle, check the weight factor of each lane, the lane showing the least weight factor is the next lane the vehicle make a move. 

```
 if ((lane == 1) && (!lane_0) && (!lane_2))
                {
                  //weightfactor1 = designated_wf;
                  if ((weightfactor0 < weightfactor2) && (abs(weightfactor0 - weightfactor2) > weight_tol))
                  {
                    lane = 0;
                    lane_flag = 0;
                  }
                  else if ((weightfactor0 > weightfactor2) && (abs(weightfactor0 - weightfactor2)>weight_tol))
                  {
                    lane = 2;
                    lane_flag = 1;
                  }
                  else if ((weightfactor0 == 1) && (weightfactor2 == 1)) // dummy testing 
                  {
                    lane = 1;
                    lane_flag = 1.1;
                  }
                  else
                  {
                    lane = 1;
                    lane_flag = 99999;
                  }
                }
```
- c. troubleshooting:
print out command used to display essential information.each corner case displays the flag (lane_flag) to know where the logic is staged at which state:

```
            cout << "cl:"<<too_close<<",ln: "<<lane<<",d: "<<d<<",l_f:"<<lane_flag<<",L0: "<<lane_0<<",L2: "<<lane_2<<",L1:"<<lane_1<<",wf: "<<"["<<weightfactor0<<","<<weightfactor1<<","<<weightfactor2<<"]"<<"end_d: "<<end_path_d<<endl;
```
```
   else if ((weightfactor1 == 0) &&(weightfactor2 ==0)) // dummy rare corner case
                  {
                    lane = 1;
                    lane_flag = 11.1;
                  }
                  else
                  {
                    lane = 2;
                    lane_flag = 12;
                  }

```
## Simulation result (please click the below thumbnail):
[![video result](https://img.youtube.com/vi/FiTJYFPapfI/hqdefault.jpg)](https://youtu.be/FiTJYFPapfI) 



## Discussion:

- The code is not efficient for certain. I need to modify it more readable,reliable, repurposeful code. 
- I have done lots of troubleshooting (I nearly consumed more than 30 hours of GPU time for this project) and made a quite a bit of adjustment in coding. Some unexpected corner cases kept me busy think differently, that results in messy code at the end..I need to learn more on algorithmic thinking and structured way to generate an efficient code..
- There are certain unexpected scenarios where the vehicle is surrounded by 3 vehicles (each lane blocks my car), then my vehicle tends to make a steer where it shouldn't be. I need to adjust the weight factor tolerance (weight_tol, see the below code snippet) to keep the lane by offsetting the weight factor difference from my lane to prevent unexpected lane change... 
```
if ((weightfactor1 < weightfactor2)&& (abs(weightfactor1 - weightfactor2) > weight_tol))
                  {
                    lane = 1;
                    lane_flag = 11;
                  }
```
- Learned a lot on c++ coding, but still a lot more to go.... Practice by looking at other's efficient code will help. 
- at the moment, the code only allows the lane change when it gets closer to the front vehicle within 30m range. I think it would be okay to project 50m or more if there's vehicle, then it's not a bad idea to make a lane change earlier to the empty lane. 

## References:

### a. FSM

![FSM strength_weakness](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Path_planning/finite_state_machine.png)

![FSM_highway](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Path_planning/finite_state_machine_highway.png)
![FSM_transition_function](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Path_planning/FSM_transition_function.png)

### b. Cost function: 
![FSM_cost_function_speed](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Path_planning/FSM_cost_func_speed_penalty.png)

### c. Example of cost function:
![cost_function](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_Path_planning/cost_function.png)


|COST FUNCTION NUMBER                                            |VERBAL DESCRIPTION|
|:---                                                            |:-                  |
|1|Penalizes trajectories that attempt to accelerate at a rate which is not possible for the vehicle|
|2|Penalizes trajectories that drive off the road|
|3|Penalizes trajectories that exceed the speed limit|
|4|Penalizes trajectories that do not stay near the center of the lane. 
|5|Rewards trajectories that stay near the target lane. 

### d. All figures appeared in this page are taken from Udacity's course material. 
