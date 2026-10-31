# A vision-language model (Claude) as high-level planner for a learned locomotion policy

![demo](media/demo.gif)

![demo: navigation and cube lift](media/demo1.gif)

*Second demo: the model walks to the table and lifts the cube.*

**Problem.** Let a vision-language model drive an ANYmal D in simulation using only the robot camera. The model does not control joints: it chooses skills, and trained policies or PD controllers execute them.

Scene: oil-platform process plant with three tables (red, blue, green) and a cube. Isaac Lab 3.0 / Isaac Sim 6.1.

## Skills the model can call

| Command | Skill | Executed by |
|---|---|---|
| walk a measured distance / turn a measured angle | locomotion | learned walking policy + odometry |
| lift the cube | manipulation | learned arm policy |
| open / close gripper | gripper | PD controller |
| hold an arm pose | arm pose | PD controller |
| "arrived" | check against simulator ground truth (for the operator only) | evaluation module |

Skills can be chained in one message (for example turn 20 degrees, then walk 1 m).

## Architecture

```mermaid
flowchart LR
  U["Operator prompt"] --> C["Claude<br/>sees camera image with guides"]
  C -->|skill commands| I["Interpreter<br/>text to skills"]
  I --> S["Session state machine<br/>SETTLE / WAIT / EXECUTE"]
  S --> H["Skills (one class each,<br/>common interface)"]
  H --> P1["Walking policy"]
  H --> P2["Arm policy"]
  H --> P3["PD gripper / arm"]
  P1 --> SIM["Isaac Sim"]
  P2 --> SIM
  P3 --> SIM
  SIM -->|camera| C
```

Adding a skill means adding one file and one registry line; the session and the interpreter do not change.

## The robot

<img src="media/foto_yo_con_robot.jpg" alt="Me with the ANYmal D at CROB" width="350">

*Me with the ANYmal D at the USP Center for Robotics (CROB).*

## Results

- In 12 of 14 logged navigation runs the robot ended within 0.4 m of a table's parking pose (simulator ground-truth evaluation log).
- In the second demo (above) the model navigated to the table and lifted the cube successfully. Lifting is not yet reliable across all attempts; improving it is the next step.

> *Code is confidential (USP Center for Robotics - CROB) and cannot be shared. Available upon request with lab approval.*
