---
title:  "Resume Update"
categories: post
mathjax: true
---

[![Resume](https://raw.githubusercontent.com/SeokLeeUS/seokleeus.github.io/master/_images/_Resume/Resume_Seok_Lee.jpg)]({{https://github.com/SeokLeeUS/seokleeus.github.io}}/assets/Seok_Lee_Resume_in_R_May042021.pdf)

I tried to update my resume using R markdown. The draft turned out well. Here is the code:

```R
---
title: "Seok Lee"
author: "Seok Lee"
date: "`r Sys.Date()`"
output:
  
  pagedown::html_resume:
    self_contained: true
  html_document:
    df_print: paged
  pdf_document: default
---

Aside
================================================================================

![Seok Lee](/Users/test/Documents/R/resume/SeokLee_3.jpg){width=80%}
<!-- ![Seok Lee](`r here::here("SeokLee_3.jpg")`) -->

<!---Research Engineer Specialized in s/w Testing | Plant Modeling | Data Analysis | 3d Virtual Simulation | Controller Design | Model Based Design | Business Data Analytics-->

Contact Info {#contact}
--------------------------------------------------------------------------------

- <i class="fa fa-envelope"></i> seokl2@illinois.edu
- <i class="fa fa-github"></i> [https://github.com/seokleeus](https://seokleeus.github.io)
- <i class="fa fa-phone"></i> +1 734-890-6340


Tools {#Tools}
--------------------------------------------------------------------------------

Data analytics: R | Python | MATLAB 

Engineering: Simulink | CarSIM | AMESim | Targetlink | CAN | C++ | Tensorflow | OpenCV | Keras | Unreal 

License {#License}
--------------------------------------------------------------------------------

[Professional Engineer(Mechanical Eng- Thermal & Fluid, MI license # 6201066645)](https://account.ncees.org/rn/1659426-1032221-e6515b2)


Disclaimer {#disclaimer}
--------------------------------------------------------------------------------

<!--This resume was made with the R package [**pagedown**](https://github.com/rstudio/pagedown).-->

Last updated on `r Sys.Date()`.


Main
================================================================================

Seok Lee {#title}
--------------------------------------------------------------------------------

---

### Deliver quality in uncertain environment:

*Uncertainty* is everywhere. In the engineering field, there are often no defined requirements or clear direction.Trial and error is a working way to discover unknowns. I learned and developed hands-on project management where uncertainty is the norm. At the same time, the exposure to the research environment where there's minimal guidance to demand creative outputs fortifies me. In both sides of the world, I am able to deliver high quality deliverable where uncertainty rises and falls.  

### Leadership is meant to enable others:

*Leadership* is not something you can sandbox- fail, improve, and repeat because the human emotion is involved. I noticed that leadership may end up performance degradation adversely when it’s exercised wrong.I envisioned leadership as an art of harmonizing humane emotion while creating an environment to deliver the project deliverable spontaneously. The formal education in MBA enabled me to utilize few tools to enhance my past earned leadership skills to enable others to achieve the group's career goal.  

### Solve complex problem:

*My passion* lies in developing solutions for complex systemic issues. The journey to prove the proposed solutions at system level makes feel rewarding and that is where my excitement arises. 

### Open minded in global setting:

*Exposure* to the global presence in working in European region enlightened me to carry a holistic view of corporate operation. I see why one size fits all strategy wouldn't work because of different labor resources, locality, and custom. Specialized in corporate globalization in MBA certainly broadens and affirms my collective view from international assignment. 

### Experienced in data driven approach:

*Acquiring* insights from data is easier said than done. The data size is unwelcoming and overwhelms at first. Data mining is monotonous and tedious. Insufficient data requires long waiting and change of data acquisition method. At then end, frustration arrives when the conclusive pattern or an insight isn't seen. Unskillful coding and visualization will add stress under time demanding project. However, the delight after discovering patterns or estimating / forecasting next behavior endures hectic prepping work. I have gone through in both engineering or business analytic areas.       

### A versatile in engineering field: 
Following engineering specialties synergize in different disciplines:

##### - *Mathematical Modeling for System*
##### - *Data Analytic Driven Decision Making*
##### - *Virtual Simulation to Mimic Real World*
##### - *Design Estimator to Predict Nonlinearity*
##### - *Model Based Approach to Develop Readable and Simplified s/w Code*
##### - *AI, Vision Sensing for Pattern Recognition, Path Following*
##### - *Business Data Analytics to Predict Patterns*


\pagebreak

---

Education {data-icon=graduation-cap data-concise=true}
--------------------------------------------------------------------------------

### University of Illinois- Urbana-Champaign

MBA-Business analytics | Global challenge

Urbana, IL

03/2020--07/2021


### University of Michigan at Dearborn

M.S. in Mechanical Engineering

Dearborn, MI

01/2005--04/2010

<!-- [Project: Brake Design of Low Mass Vehicle](https://www.dropbox.com/s/gale44hbqk36ixf/IAVS%20report-Brake%20design%20modeling%20for%20LMV_Nov192018.pdf?dl=0) -->

### Korea Aerospace University

Bsc. in Mechanical Engineering

Korea

1998--2005


Industry Experience {data-icon=suitcase data-concise=true}
--------------------------------------------------------------------------------

### Ford Motor Company

Research Engineer- Driving Assist Technology

Dearborn, MI, USA

04/2019--Present

Mathematical Modeling for the Virtual Sensor Using Ray Tracing | Virtual Sensor Visualization in Unreal | Detailed Simulation Scene Generation in CarSIM |Co-Sim Framework for Vehicle Dynamics | 3d Scene Generation | Object Detection Using Virtual Camera

### Ford Motor Company

Autonomous Chassis Controls Engineer

Dearborn, MI, USA

11/2015--04/2019

MBD s/w Architecture Definition | Software (s/w) Testing on Autonomous Chassis Application | Development of Large Scale Modeling | Driving Simulation | MIL Modeling

### Cummins Engine Company

Diagnostic Team Leader

Columbus, IN, USA

04/2014--11/2015

- A team lead role to develop calibration contents for diesel emission controls diagnostics for Heavy Duty application. The work involved in emission test cell testing, vehicle testing, simulation unit testing, and data analysis.

### Cummins Engine Company

Technical Specialist

Darlington, UK

07/2011--04/2014

- Led aftertreatment diagnostic calibration development for Euro VI 4.5L/6.7L Cummins midrange engine
- Led/managed s/w HIL testing & maintenance team for engine/aftertreatment system

### Cummins Engine Company

Senior Control Engineer

Eindhoven, the Netherlands

10/2007--07/2011

- A liaison to support s/w development for a Dutch truck OEM (DAF trucks, NV). The main jobs to validate emission controls s/w, support diagnostics interface development, and fine tuning through various testing (engine cell, vehicle, HiL)
- Led/support aftertreatment J1939 CAN definition/diagnostic fault handling architecture requirement

Academic Research Experience {data-icon=flask}
--------------------------------------------------------------------------------

### Graduate Research Assistant

University of Michigan

Dearborn, MI

2005--2010

- Brake Modeling and Design of Low Mass Vehicle (LMV)
<!--	- Designed conventional brake system for LMV and developed brake diagram GUI
	- Modeled hydraulic brake system using AMESim
- LMV Dynamic Vehicle Model Development 
	- Developed 8 Degree of Freedom (DoF) vehicle model using MATLAB/SIMULINK
	- Validated the vehicle model using CarSim
- Tire Simulation Model and Estimation Module Development 
	- Developed/Compared Pacejka5.2, Milliken, and Dugoff Tire Model using MATLAB/SIMULINK
	- Developed Extended Kalman Filter (EKF) algorithm for tire force estimation 
- Development of Anti Lock Brake System (ABS) Control Algorithm
	- Developed wheel slip control algorithm using Sliding Mode Control (SMC)
	- Designed alternative sliding surface in SMC -->
- Hydraulic Circuit Modeling of ABS
<!--	- Developed ABS hydraulic circuit using AMESim
	- Developed Pulse Width Modulation (PWM)-SMC control algorithm for ABS application-->


Publications {data-icon=file}
--------------------------------------------------------------------------------


### Investigation of Sliding-Surface Design on the Performance of Sliding Mode Controller in Antilock Braking Systems

[IEEE Vehicular Technology, Volume 57 issue 2](https://ieeexplore.ieee.org/document/4357200?reload=true)

N/A

2008

Taehyun Shim, Sehyun Chang, **Seok Lee**

### Technical report- Brake design and modeling of Low Mass Vehicle
 
[IAVS (Institute of Advance Vehicle System), University of Michigan- Dearborn ](https://www.dropbox.com/s/gale44hbqk36ixf/IAVS%20report-Brake%20design%20modeling%20for%20LMV_Nov192018.pdf?dl=0) 
 
N/A

2007

 **Seok Lee**
 
 
### Development of a Brake System for Lightweight Vehicle

[IMECE2006-15437, pp. 229-238; 10 pages](https://doi.org/10.1115/IMECE2006-15437)

N/A

2006

 **Seok Lee** , Taehyun Shim , Byung-Kwan Cho
 
```
