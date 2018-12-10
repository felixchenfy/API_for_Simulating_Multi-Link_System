# Report of ME314 Final Project
# Part 1
# Simulating Multi-link System
## Feiyu Chen

This is the 1st part of my report. I will describe the simulation scene, coordinates of different objects, calculation of Euler-Lagrange equations and impacts, etc.  

More details about how I make my code generalized are put in the Report_part2.pdf.

## 1. Proposal

My original proposal was to simulate "a two-link pendulum playing triangular ping-pong against walls" (without user input, just motion and impacts).  

However, as I was doing this project, I found it too troublesome to hardcode all the variables, equations, and impacts. So I wrote an API (several encapsulated functions) for creating links (see **Report_part2.pdf**), and then I simulated a multi-link system with 8 DOF, as seen in the image below.

## 2. Figure of the Scene

The above figure shows my simulation scene as well as annotations of the coordinates and transformations.  
![](images/scene_annotated.png)
There are 5 groups of links in the figure:  
1. A pentagon (DOF=3)
2. A 2-link pendulum (DOF=2)
3. A 1-link pendulum (DOF=3, height of the left vertex is fixed)
4. A 1-link wall (DOF=0)
5. Four walls around the main objects (DOF=0)

The x and y axis of each frame are denoted by straight arrows. There are 3 types of frames:  
* <span style="color:red"> *Red frame* </span> is for 3-DOF link that has variables $x$, $y$, and $\theta$.
* <span style="color:green"> *Green frame* </span> is for 1-DOF link, which has variable $\theta$ and can rotate around certain pivot.
* <span style="color:gray"> *Gray frame* </span> is for 0-DOF link, whose two vertices are fixed in certain frame.

The transformations between different frames are denoted by curved arrows in <span style="color:blue"> blue</span>. These transformations connect up several links to form an object.

## 3. Calculation of EL-eqs and Impacts

### 3.1 KE-V
Suppose a link has length $l$. Then I assume its mass to be $l$ and inertia to be $l^2$. The generalized 6x6 body mass M is then obtained.

For each link, I compute the 4x4 matrix representation $g$ of its center frame. Then calculate the body screw $V$ velocity using $g$ and $dg/dt$. Then the kinetic energy is $\frac{1}{2}V^T M V$.

### 3.2 Cons and External Force

There is no external force in my simulation. But it's easy to add -- simply put something on the right side of EL-eqs.

I added one constraint to the 1-link pendulum on the right-up side of the figure. I first computed the {x,y} coords of its left vertex, and then set $y$ as 0. Then I passed it to the EL-eqs.

### 3.3 Detecting Impacts
In my simulation, the impact only happens between a vertex and an edge. An impact happens when:  
1. The vertex has a very small distance to the edge.  
2. The projection from vertex to edge is on the edge.  

I detect the impact by checkin these two conditions.

### 3.4 Impact Update
After detecting one or more impacts, my NDSolve breaks up. Then I do the impact updates for all the impacts one by one, get the new $dq$, and call the NDSolve again.

The logic of my code looks like this:

```
Loop{
    While(no impact && t!=t_end{
        NDSolve
    }
    if(impact){
        impact update
    }else{
        break
    }
}
```

### 4. Result
#### 4.1 How to run
To get the simulation result, please run "run_this.nb". It will opens another three "funcs_xxx.nb", load the functions, and starts to compute the links motions. It takes about 1 minute to compute for 10 seconds simulation.

One video I recored is in "video.mp4".

#### 4.2 What happens in simulation
In my simulation, the pentagon and 2-link pendulum are moving and occassionally impact with each other. The 1-link pendulum on the right-up side is just for showing that my "Constraint" is working.

#### 4.3 Hamiltonian  
Since the energy of my system is supposed to be conserved, I plot the Hamiltonian as seen below. It is conservered over time.
![](images/Halmiltonian.png)

#### 4.4 Others
More techical detials and problems about my project are discussed in Report_part2.md


