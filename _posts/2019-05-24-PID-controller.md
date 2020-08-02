---
title:  "PID Controller"
categories: post
mathjax: true
---
![PID](https://github.com/SeokLeeUS/seokleeus.github.io/raw/master/_images/_PID/PID_gif.gif)
## Summary:
- Engage a feedback controller (Proportional Integration Derivative (PID)) to let the vehicle follow the given path in the simulation.
- Required programming language is C++. 

## Result (please click the thumbnails for viewing the video):
[![PIDController video](https://img.youtube.com/vi/xv-AnkbR_LM/hqdefault.jpg)](https://youtu.be/xv-AnkbR_LM)

## To reviewer:
- I see why the reviewer faced the issue to run simulation. 
- Basically, PID code uses argc,argv command to take PID gains. 
- Therefore, I suggested to put the following on Udacity's Workspace command line:
```
./pid -0.05 0 -2.5
``` 
it means 
```
Kp (proportional gain): -0.05
Ki (integral gain)    : 0 
Kd (derivative gain)  : -2.5
```
## The important thing to run simulation on workspace environment:
```
// example
./pid Kp Ki Kd
```
- In this example, Kp:prportional gain, Ki: integral gain, Kd: derivative gain 

This is because of argc,argv command in main function to take PID gain on command prompt:
```
int main(int argc, char *argv[]) {
  uWS::Hub h;

  PID pid;
  /**
   * TODO: Initialize the pid variable.
   */
  double init_Kp = atof(argv[1]);
  double init_Ki = atof(argv[2]);
  double init_Kd = atof(argv[3]);
  pid.Init(init_Kp,init_Ki,init_Kd);
```



## Reference:
-Udacity's PID walkthrough session: https://www.youtube.com/watch?v=YamBuzDjrs8&feature=youtu.be
-Udacity's PID course material (all snapshots appeared in this section are from the course material)

## Project rubrics:

|CRITERIA                                                        |MEETS SPECIFICATIONS|
|:---                                                            |:-                  |
|Your code should compile. | Code must compile without errors with cmake and make.Given that we've made CMakeLists.txt as general as possible, it's recommend that you do not change it unless you can guarantee that your changes will still compile on any platform.|
|The PID procedure follows what was taught in the lessons.|It's encouraged to be creative, particularly around hyperparameter tuning/optimization. However, the base algorithm should follow what's presented in the lessons.|
|Describe the effect each of the P, I, D components had in your implementation.|Student describes the effect of the P, I, D component of the PID algorithm in their implementation. Is it what you expected?Visual aids are encouraged, i.e. record of a small video of the car in the simulator and describe what each component is set to.|
|Describe how the final hyperparameters were chosen.|Student discusses how they chose the final hyperparameters (P, I, D coefficients). This could be have been done through manual tuning, twiddle, SGD, or something else, or a combination|  
|The vehicle must successfully drive a lap around the track.|No tire may leave the drivable portion of the track surface. The car may not pop up onto ledges or roll over any surfaces that would otherwise be considered unsafe (if humans were in the vehicle).|

# working code before manual tuning:

@ main.cpp
```
  // main body code to call out UpdateError/TotalError functions
          pid.UpdateError(cte); //calling UpdateError function
          steer_value = pid.TotalError();  //calling TotalError function
          // DEBUG
          std::cout << "CTE: " << cte << " Steering Value: " << steer_value 
```
```
// set up initializer
int main(int argc, char *argv[]) {
  uWS::Hub h;

  PID pid;
  /**
   * TODO: Initialize the pid variable.
   */
  double init_Kp = atof(argv[1]);
  double init_Ki = atof(argv[2]);
  double init_Kd = atof(argv[3]);
  pid.Init(init_Kp,init_Ki,init_Kd);
```

@PID.cpp
```
//initializer
void PID::Init(double Kp_, double Ki_, double Kd_) {
  /**
   * TODO: Initialize PID coefficients (and errors, if needed)
   */
  PID pid;
  this->Kp = Kp_;
  this->Ki = Ki_;
  this->Kd = Kd_;
  
  p_error = 0;
  i_error = 0;
  d_error = 0;

}
```
```
//UpdateError
void PID::UpdateError(double cte) {
  /**
   * TODO: Update PID errors based on cte.
   */
  
  d_error = cte - p_error;
  p_error = cte;
  i_error += cte;

}
```
```
//TotalError
double PID::TotalError() {
  /**
   * TODO: Calculate and return the total error
   */
  return Kp*p_error + Ki*i_error + Kd*d_error;
  //return 0.0;  // TODO: Add your total error calc here!
}
```
## Tuning process:

|iteration#|Kp|Ki|Kd|description|
|---|---|---|---|---|
|#1 |-0.5|0|0|I was able to make the vehicle move, but the steering front wheel of simulation vehicle oscillates too much.|
|#2 |-0.1|0|20|The simulation vehicle is able to complete a full lap without falling under the bank.However, it still oscillates but a lot shorter oscillation interval as the derivative gain (Kd) tries to attenuates the larger frequency of oscillation.|
|#3 |-0.05|0|20|The frequency of small oscillation reduced. try to even reduce down Kp further?|
|#4 |-0.02|0|20|I don't see much improvement...  probably increase derivative (Kd) gain to attenuate abrupt steering change?|
|#5 |-0.02|0|-50|I see it gets better, (at least follow the path on the road), but there's overshoot when Kd kicks in to attenuate the oscillation.Now it's time to consider integral gain (Ki). |
|#6 |-0.02|-5|-50|Hmm. After adding Ki,  the vehicle at starting point ran out of path and circled around. Ki is wrong value...  |
|#7 |-0.02|-0.5|-50|Still doesn't look right as the vehicle circles around at the start, but at least no oscillation at the steering axle.|
|#8 |-0.02|-0.05|-50|The simulation vehicle follows the track... I noticed the less oscillation on the front steering wheels comparing to step 6. It seems overshoot driven by Kd seems too high so Ki may not be able to help. Better to cut down Kd.|
|#9 |-0.01|-0.05|-25|Kd in half, and Kp in half while holding Ki. a bit better, but I see the oscillation may not be solely driven by overshoot (mainly Kd). Fundamentally, proportionalgain seems too high. needs to  cut down Kp further, and leave Kd as is to see the performance.|
|#10 |-0.005|-0.05|-25|Noticed a bit better performance. Therefore, stick to Kp as is. Try to cut down Kd as there looks like overshoot.|
|#11 |-0.005|-0.05|-20|a bit better than step 11, but still noticing bit of overshoot on the steering axle. I need to correct the vehicle trajectory issue (zigzag behaviour, it's related to Ki, I believe)|
|#12 |-0.005|-0.025|-15|getting worse|
|#13 |-0.005|-0.1|-20|a lot more zigzagging|
|#14 |-0.005|-0.01|-20|a lot less zigzagging (stick to Ki as is), but there's still oscillation on the front steering axle. Kp/Kd needs adjustment.|
|#15 |-0.002|-0.01|-10|a lot better, but cornering response needs improvement as it goes out of path.... return back to step 15. how to mitigate oscillation overshoot on the front axle? cut down more on Kd while removing Ki and increasing Kp... |
|#16 |-0.05|0|-2.5|this gives better result, no out of path, a lot less zigzagging, and oscillation on the front axle.|


## Submission result:

[![PIDController video](https://img.youtube.com/vi/xv-AnkbR_LM/hqdefault.jpg)](https://youtu.be/xv-AnkbR_LM)

## Final notes
- I spent too much time to fix compiler issue on Udacity's workspace. (posted a solution here: [PID workspace issue](https://knowledge.udacity.com/questions/35969))

- I noticed it's unstable during cornering with step #16 PID gain, it can be improved with further adjustment. 

- I tried to run twiddle to adjustment parmaeters while running simulation, but it wasn't successful. I need to revisit twiddle code to see what went wrong. Instead, I manually tuned the gain in an order (p gain first, d gain, introduce i gain, then fine tune bit by bit by looking at the performance of the vehicle.)

```
double PID::twiddle(double cte)
{
  //double sum_pid = this->Kp + this->Ki + this->Kd;
  double dp_p = 1;
  double dp_i = 1;
  double dp_d = 1;
  double sum_dp = dp_p + dp_i + dp_d;
  //double total_err;
  double best_err;
  double steer_value;
  int it = 1;
  while (sum_dp > 0.01)
  {
    this->Kp += dp_p;
    this->Ki += dp_i;
    this->Kd += dp_d;
    UpdateError(cte);
    steer_value =  TotalError();
    if (cte < p_error)
    {
      best_err = cte;
      dp_p *= 1.1;
      dp_i *= 1.1;
      dp_d *= 1.1;
    }
    else
    {
      dp_p -= 2 * dp_p;
      dp_i -= 2 * dp_i;
      dp_d -= 2 * dp_d;
      
      UpdateError(cte);
      steer_value = TotalError();
      if (cte < best_err)
      {
        best_err = cte;
        dp_p *= 1.1;
        dp_i *= 1.1;
        dp_d *= 1.1;
      }
      else
      {
        this->Kp += dp_p;
        this->Ki += dp_i;
        this->Kd += dp_d;
        dp_p *= 0.9;
        dp_i *= 0.9;
        dp_d *= 0.9;
      }
    }
  }
  it++;
  std::cout<<it<<endl;
  return steer_value;
}
```













