# PID Control Submission

## Why I chose manual tuning
So as the title of this section says I went with manual tuning. One reason being that there are only 3 parameters to tune. So I figured that manual tuning might be a good point to start.
Furthermore I experimented a bit with the twiddle implementation from the lesson with Sebastian and I have to say, that I don't like it. It is a local optimizer that requires good guesses of initial parameters and parameter increments. If the guesses are not good it get's stuck in a local minimum, so it doesn't guarantee a good solution.

## How I ended up with my solution
### Effect of _P_ component 
At first I started with a simple _P_ controller, to get a feeling for the project. Seeing that the CTE never exceeds 10 (E1), I chose the proportional factor to be in the order of (E-2). Like this I could guarantee that it doesn't exceed the maximum steering magnitude of 1. I started with 0.09. However that value led to instability: the oscillation around the reference line became bigger and bigger. I slowly tuned down, until I found a value, that was more or less stable. I think it was around 0.06.

### Effect of _D_ component

Afterwards I started adding the _D_ component to the controller in order to avoid oscillation. Initial thought led me to believe, that the open loop system has I characteristics: a small constant steering angle yields a growing error. To compensate for that behaviour I believed that the magnitude of the _D_ component should be E1. The oscillation got far less. 

### Effect of _I_ component

I didn't have the impression, that the steering had an offset, that would require an I component. However, I started noticing something awkward: The vehicle was not turning smoothly. I guess that has to do with the simulator. If you look closely at the demarcations on either side, you see, that the lines are not curved. They are made of straight lines with different angles. This means that the reference line probably is not smooth either. So to avoid these rough quick and small turns each time the reference line changes it's angle, I tried to smoothen the steering angle with an _I_ component. 
The _I_ component tends to get very large due to summation, so it is best to weight it with a smaller factor than the _P_ and _D_ factors. I started with the magnitude E-3 (one smaller than _P_) and was pretty surprised to see, that it was a fitting magnitude. 

### Remaining tuning 
Obviously adding an _I_ component to the controller brought back oscillation. I tried to keep it down by increasing the _D_ factor and/or going down with the _I_ factor. I couldn't get rid of the oscillation entirely, but from what I saw the controller makes it around the track. 

## Implementation Details
I decided against using the provided class, because the controller can be implemented in a few lines. Instead of having the previous cte `prev_cte`and sum of ctes `sum_cte` as class members, I declared them as variables outside the lambda and capture them. That way I can also preserve state and hardly need to change the given code. 