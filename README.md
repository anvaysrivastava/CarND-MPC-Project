# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

---

## Writeup

### The model
The code used model defined in `Lesson 19: Chapter 9: Solution, Mind the line`
The model is a kinematic model that uses vehicle's coordinate(x,y), velocity(v), orientation(psi), cross track error(cte), orientation error(epsi).

Using the input we provide actuation in terms of acceleration(a) and steering(delta).

The update equation is inspired by `Lesson 19: Chapter 9: Solution, Mind the line`
![alt-text](https://github.com/anvaysrivastava/CarND-MPC-Project/blob/master/img/updateEquation.png)

You can view the equation in code at `MPC.cpp line 104 to line 109`

### Timestep Length and Elapsed Duration.

I first played with timestep length. And realized 
* for N > 13 the lag is too high as I believe by the time approximations end the car lag gets too long for actuators to be accurate, this result in a lot of swaying in car, even on straigh line.
* for N < 8 the appoximations are too crude to go along with weights on steering smoothness and upper limit on steering angle.
* Hence my final solution was to go for a rounded figure of 10.

For elapsed Duration any value between 0.9 to 0.12 was driving the car in acceptable behavior. Hence I chose the value to be 0.1 seconds. This also gives a good over all roundoff number where car looks 1 second in future with 10 intervals. As well as this coinsidently overlaps with 100 ms lag mentioned about the actuators. 

The variables are present in `MPC.cpp line 8 and 9`

### Polynomial Fitting and MPC Preprocessing

Polynomial ditting is done in code at `MPC.cpp line 82 to line 109`. 

In order to make polynomial fitting easy with reference x, y and psi as 0. In `main.cpp line 104 to line 112` I transformed the waypoints to car's perspective.

### Model Predictive Control with Latency

* I delayed the actuators in the solver to account for latency by feeding them previous acceleration and delta. This is present in `MPC.cpp line 97 to 100`. The con of this approach is that my latency should be a multiple of dt. The solution for that was being discussed in [here](https://discussions.udacity.com/t/calibration-for-the-acceleration-and-steering-angle-for-latency-consideration/276413). However the car was not running stably despite many efforts. I am taking this as an action item in future.
* I also made sure extra weights are assigned to the flux in steering and acceleration change. This makes sure that the car MPC control won't give contrasting steering/ acceleration input in corresponding steps.. This way with N = 10 and dt = 100 ms we can make sure that even if actuators are laggy, they can be handled as errors and approximated out in next iteration of MPC. `MPC.cpp line 46 to 70`


## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `install-mac.sh` or `install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets
    cd uWebSockets
    git checkout e94b6e1
    ```
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.

* **Ipopt and CppAD:** Please refer to [this document](https://github.com/udacity/CarND-MPC-Project/blob/master/install_Ipopt_CppAD.md) for installation instructions.
* [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page). This is already part of the repo so you shouldn't have to worry about it.
* Simulator. You can download these from the [releases tab](https://github.com/udacity/self-driving-car-sim/releases).
* Not a dependency but read the [DATA.md](./DATA.md) for a description of the data sent back from the simulator.


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.

## Tips

1. It's recommended to test the MPC on basic examples to see if your implementation behaves as desired. One possible example
is the vehicle starting offset of a straight line (reference). If the MPC implementation is correct, after some number of timesteps
(not too many) it should find and track the reference line.
2. The `lake_track_waypoints.csv` file has the waypoints of the lake track. You could use this to fit polynomials and points and see of how well your model tracks curve. NOTE: This file might be not completely in sync with the simulator so your solution should NOT depend on it.
3. For visualization this C++ [matplotlib wrapper](https://github.com/lava/matplotlib-cpp) could be helpful.)
4.  Tips for setting up your environment are available [here](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/23d376c7-0195-4276-bdf0-e02f1f3c665d)
5. **VM Latency:** Some students have reported differences in behavior using VM's ostensibly a result of latency.  Please let us know if issues arise as a result of a VM environment.

## Editor Settings

We've purposefully kept editor configuration files out of this repo in order to
keep it as simple and environment agnostic as possible. However, we recommend
using the following settings:

* indent using spaces
* set tab width to 2 spaces (keeps the matrices in source code aligned)

## Code Style

Please (do your best to) stick to [Google's C++ style guide](https://google.github.io/styleguide/cppguide.html).

## Project Instructions and Rubric

Note: regardless of the changes you make, your project must be buildable using
cmake and make!

More information is only accessible by people who are already enrolled in Term 2
of CarND. If you are enrolled, see [the project page](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/f1820894-8322-4bb3-81aa-b26b3c6dcbaf/lessons/b1ff3be0-c904-438e-aad3-2b5379f0e0c3/concepts/1a2255a0-e23c-44cf-8d41-39b8a3c8264a)
for instructions and the project rubric.

## Hints!

* You don't have to follow this directory structure, but if you do, your work
  will span all of the .cpp files here. Keep an eye out for TODOs.

## Call for IDE Profiles Pull Requests

Help your fellow students!

We decided to create Makefiles with cmake to keep this project as platform
agnostic as possible. Similarly, we omitted IDE profiles in order to we ensure
that students don't feel pressured to use one IDE or another.

However! I'd love to help people get up and running with their IDEs of choice.
If you've created a profile for an IDE that you think other students would
appreciate, we'd love to have you add the requisite profile files and
instructions to ide_profiles/. For example if you wanted to add a VS Code
profile, you'd add:

* /ide_profiles/vscode/.vscode
* /ide_profiles/vscode/README.md

The README should explain what the profile does, how to take advantage of it,
and how to install it.

Frankly, I've never been involved in a project with multiple IDE profiles
before. I believe the best way to handle this would be to keep them out of the
repo root to avoid clutter. My expectation is that most profiles will include
instructions to copy files to a new location to get picked up by the IDE, but
that's just a guess.

One last note here: regardless of the IDE used, every submitted project must
still be compilable with cmake and make./

## How to write a README
A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).
