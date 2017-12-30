# An Efficient User Verification System via Mouse

<!-- TOC -->

- [Background Knowledge and Insight](#background-knowledge-and-insight)
- [Goal](#goal)
- [Data](#data)
    - [Data Source](#data-source)
    - [Data Processing](#data-processing)
- [Feature(Metrics)](#featuremetrics)
- [Mouse Movement Characterization](#mouse-movement-characterization)
    - [Dependence on different platforms](#dependence-on-different-platforms)
    - [Distance Between Distributions](#distance-between-distributions)
    - [Number of Mouse Clicks in a Real Session](#number-of-mouse-clicks-in-a-real-session)
- [Classifier](#classifier)
- [Limitation](#limitation)
- [Reference](#reference)

<!-- /TOC -->

## Background Knowledge and Insight

* User verification need
    * More frequent
    * Passive
    * Transparent to users
* behavioral biometrics approaches
    * Fingerprints retinal scan
        * Specialized hardware
            * Expensive
            * Unavailable
    * Keystroke
        * Record sensitive user information
            * Username
            * Password
        * Complex structure
* Angle-based metrics
    * Independent of the operating environment

## Goal

* A new user verification system using mouse dynamics
    * Accuracy
    * Quick response
    * Difficult to forge
* A Novel measurement strategy
    * Angle-based metrics
* An experiment involving sessions from over 1,000 unique users

## Data

### Data Source

* Two data sets
    * Controllable environment
        * Controllable set
        * 30 users
            * Different backgrounds
    * Online forum(field)
        * field set
        * 1000 real field users
        * Recorded by JaveScript code
* Raw data movement
    * `<ACTION-TYPE, t, x, y>`
        * `ACTION-TYPE`, {mouse-move, mouse-click}
        * `t`,  timestamp of the mouse action, collected in milliseconds
        * `x`, `y`, coordinate

### Data Processing

* Identify every point-and-click action
    * Continuous mouse movement followed by click
    * `<mouse-move, t_i, x_i, y_i>_c,j`
        * `c`, user
        * `i`, *i*th point-and-click action
        * `j`, *j*th mouse move record
        * `t`, timestamp

## Feature(Metrics)

* Direction
    * For consecutive points $$A$$, $$B$$: $$\vec{AB}$$
* Angle of Curvature
    * For any three consecutive points $$A$$, $$B$$, $$C$$: $$\angle{ABC}$$, angle between $$\vec{AB}$$ and $$\vec{BC}$$
* Curvature Distance
    * For any three consecutive points $$A$$, $$B$$, $$C$$: ratio between $$\vert\vec{AB}\vert$$ to length of perpendicular distance from B to $$\vec{AC}$$
* Speed
    * `the total distance traveled for that action` divided by `the total time taken to complete the action`
* Pause-and-Click
    * Time between the end of the movement and the click event

## Mouse Movement Characterization

### Dependence on different platforms

* OS
* Screen
    * Size
    * Resolution
* Mouse
    * Mouse pointer sensitivity
    * Brand of mouse
* Desk space available near mousepad
* Poor choices(affects measurements)
    * Speed
    * Acceleration
* Uniqueness of angle-based metrics across users

### Distance Between Distributions

* Since angle-based features are continuous variables
* Divided into discrete intervals, $$bins$$
* Calculate PDF for each distribution
* $$\mathrm{PDF}_p = \{p_1, p_2, ..., p_n\}$$
    * $$\mathrm{PDF}_p$$ for distribution $$p$$
    * $$p_i$$ represents the probability of falling into the $$bin_i$$
*  the distance between $$\mathrm{PDF}_p$$, $$\mathrm{PDF}_p$$
    * $$D(p,q)=\sum_i^n \vert p_i-q_i\vert$$

### Number of Mouse Clicks in a Real Session

* Average number of mouse clicks per session being about 15
* User must be identified in fewer than 15 clicks

## Classifier

* 2-class SVM
* RBF kernel
* Decision:
    * Threshold
    * Majority vote
        * Multiple models using sampled data

## Limitation

* Partial Movements
    * Continuous mouse movements without ending in a click
        * Intentionally performed
        * Move its mouse just to stop the screen saver when watching a video
        * Aid reading
        * Moving the mouse to a link, but then decide not to click on it
    * Compare to point-and-clicks
        * More noisy
        * Much more frequent
            * 0.53 mouse clicks per minute
            * 6.58 partial movements per minute

|                  | Equal Error Rate | Verification time |
| ---------------- | ---------------- | ----------------- |
| Point-and-click  | 1.3%             | 38 minutes        |
| Partial movement | 1.9%             | 3 minutes         |

* Scalability problem
    * Common problem for almost all biometrics approaches
* More suitable to work together with other authentication methods

## Reference

* An Efficient User Verification System via Mouse Movements, 2011
* CS 259D Lecture 7
