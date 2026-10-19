# CFMRSP-FCT: Benchmark Instances and Solutions

Supporting data and solution workbooks for the paper:

> **A Conflict-Free Integrated Routing and Scheduling Approach for In-Plant Milk-Run Systems**

**Ali İhsan Boyacı, Alpaslan Fığlalı**  
Kocaeli University, Department of Industrial Engineering

This repository provides 24 benchmark instances and accompanying solution workbooks for the **Conflict-Free Milk-Run Routing and Scheduling Problem with Fixed Cycle Time (CFMRSP-FCT)** investigated in the paper.

The files include layout information, transport tasks, instance parameters, task assignments, vehicle routes, schedules, and objective values. Solution information is organized separately for **Model 1** and the **iterative solution approach**.

---

## 1. Overview

The instances are generated on two layouts:

| Layout | Code | Area (m²) | Aisles | Narrow-aisle ratio | Tasks | Tow trains (TTs) |
|---|---|---|---|---|---|---|
| Simplified | `S` | 900 | 66 | {25%, 50%} | {25, 50} | {2, 3, 4} |
| Factory-inspired | `F` | 1650 | 96 | {25%, 50%} | {50, 100} | {3, 4, 5} |

Combining the parameter levels gives **12 instances per layout and 24 instances in total**. The instance configurations are summarized in Tables 3–5 of the paper.

Instance identifiers are `S1`–`S12` for the simplified layout and `F1`–`F12` for the factory-inspired layout.

All values use consistent units: **distances in metres (m), times in seconds (s), and travel speed in metres per second (m/s).**

---

## 2. Repository structure

| File or directory | Contents |
|---|---|
| `README.md` | Repository documentation |
| `LICENSE` | License information |
| `data/` | Input workbooks for the 24 instances |
| `solutions/model_1/` | Model 1 solution workbooks and reports for instances without a feasible solution |
| `solutions/iterative_approach/` | Solution workbooks for the iterative approach |

Input workbooks are named using the instance identifier followed by `_data.xlsx`, such as `S1_data.xlsx` and `F12_data.xlsx`.

Solution filenames identify the corresponding instance and solution method.

---

## 3. Input workbook contents

Each `*_data.xlsx` workbook contains the following six sheets.

### 3.1 `Layout and numbering`

A reference diagram showing the physical layout, including the supermarket, warehouse or parking area, manufacturing cells, nodes, and aisles.

Nodes are represented by circles and aisles by boxes. This sheet links the numeric identifiers used in the other sheets to the physical layout.

### 3.2 `Node coordinates`

Coordinates of the points in the layout.

| Column | Unit | Description |
|---|---|---|
| `Point_ID` | — | Point identifier |
| `X (m)` | m | Horizontal coordinate |
| `Y (m)` | m | Vertical coordinate |
| `Type` | — | `starting node`, `node`, `demand aisle point`, or `supply aisle point` |

### 3.3 `Distance matrix`

A symmetric matrix containing the lengths of direct connections between nodes. Node identifiers label the first row and column.

- Entry `(i, j)` gives the direct aisle length between nodes `i` and `j`.
- A value of `0` indicates that the nodes are not directly connected.
- The matrix is symmetric: `d(i,j) = d(j,i)`.

Aisle service points lie on the connections between nodes and therefore do not appear as separate nodes in this matrix.

### 3.4 `Narrow aisles`

A symmetric binary matrix identifying narrow aisles. Node identifiers label the first row and column.

- Entry `(i, j)` is `1` if the connection between nodes `i` and `j` is classified as narrow and is subject to the narrow-aisle conflict constraints.
- Entry `(i, j)` is `0` otherwise.
- Every `1` corresponds to an existing connection in the distance matrix.
- The matrix is symmetric.

### 3.5 `Task list`

The transport tasks to be served during one cycle.

| Column | Unit | Description |
|---|---|---|
| `Task_ID` | — | Task identifier |
| `Demand_aisle` | — | Aisle where boxes are delivered |
| `Supply_aisle` | — | Aisle where boxes are picked up |
| `Boxes (per cycle)` | boxes/cycle | Number of boxes required by the task in each cycle |

### 3.6 `Instance parameters`

Scalar parameters defining the instance.

| Column | Description |
|---|---|
| `Parameter` | Parameter symbol or name |
| `Value` | Numerical value |
| `Unit` | Unit of measurement |
| `Description` | Parameter definition |

The parameters include:

- Number of tow trains (`k`)
- Travel speed (`v`, m/s)
- Handling time per delivered box (`BBS`, s/box)
- Handling time per picked-up box (`ABS`, s/box)
- Tow-train capacity (`C`, boxes)
- Fixed cycle time (`CS`, s)
- Minimum temporal separation between conflicting traversals (`δ`, s)

Vehicle capacity and cycle time are generated as described in Section 5.1 of the paper.

---

## 4. Solution workbook contents

Solution workbooks contain three sheets.

### 4.1 `Solution_Summary`

Provides the solution method, objective value, total travel time, total waiting time, total service time, and solution status.

Depending on the available information, the sheet also includes lower bounds, optimality gaps, and reported solution times. A vehicle-level summary provides task quantities, movement counts, travel times, waiting times, and return times.

The objective is calculated as:

**Objective value = total travel time + total waiting time**

Pickup and delivery handling times contribute to the schedule and cycle duration but are excluded from the objective value.

### 4.2 `Task_Assignments`

Identifies the tow train assigned to each task and its pickup and delivery steps.

| Column | Description |
|---|---|
| `Task_ID` | Task identifier |
| `TT_ID` | Assigned tow train |
| `Supply_aisle_ID` | Pickup aisle |
| `Demand_aisle_ID` | Delivery aisle |
| `Boxes` | Number of boxes assigned to the task |
| `Pickup_step` | Movement step at which pickup occurs |
| `Delivery_step` | Movement step at which delivery occurs |

The schedules follow the cyclic operating convention of the paper. A delivery may precede the corresponding pickup within the displayed cycle when the delivered boxes are carried over from the preceding cycle.

### 4.3 `Routes_and_Schedules`

Lists the movements of each tow train in sequence.

| Column | Description |
|---|---|
| `TT_ID` | Tow-train identifier |
| `Step` | Movement sequence number |
| `From_node` | Origin node |
| `To_node` | Destination node |
| `Aisle_ID` | Service aisle identifier; `0` indicates no service aisle identifier |
| `Service_flag` | `1` when pickup or delivery service is performed |
| `Entry_time_s` | Entry time of the traversal |
| `Exit_time_s` | Exit time of the traversal |
| `Travel_time_s` | Travel time |
| `Pickup_boxes` | Number of boxes picked up |
| `Delivery_boxes` | Number of boxes delivered |
| `Service_time_s` | Total pickup and delivery handling time |
| `Waiting_time_s` | Waiting time |
| `Elapsed_time_s` | Exit time minus entry time |
| `Objective_contribution_s` | Travel time plus waiting time |

Times are measured from the beginning of the displayed cycle.

### 4.4 Solution status

For Model 1 instances `F9`, `F10`, `F11`, and `F12`, no feasible solution was found within the reported time limit. Their workbooks report `NO_FEASIBLE_SOLUTION_FOUND`; objective values and gaps are marked as `N/A`, and the assignment and routing sheets contain column headers without solution records. This status does not establish that the instances are infeasible.

---

## 5. Implementation and validation

The computational experiments described in the paper were implemented in **MATLAB R2022b** using **Gurobi 10.0.1**.
The input and solution workbooks support inspection and independent validation of the instance data and schedules.

When loading an input workbook:

- Read node coordinates and direct connections from `Node coordinates` and `Distance matrix`.
- Read transport requirements from `Task list`.
- Read narrow-aisle classifications from `Narrow aisles`.
- Read capacity, cycle time, handling times, speed, and temporal separation from `Instance parameters`.

Exclude header rows and identifier columns when importing numeric matrices.

When checking a solution, use the matching instance workbook and verify task coverage, route continuity, handling times, vehicle capacity, cycle-time limits, narrow-aisle conflicts, and the objective calculation.

---

## 6. Citation

If you use these instances or solution workbooks, please cite the associated paper:

Boyacı, A. İ., & Fığlalı, A. A Conflict-Free Integrated Routing and Scheduling Approach for In-Plant Milk-Run Systems. (Full citation and DOI will be added upon publication.)

---

## 7. License and contact

- License: MIT (see the LICENSE file). The instances may be reused with attribution.
- Contact: Ali İhsan Boyacı — ali.ihsan@kocaeli.edu.tr
