# Wireless_Project
### **1. Introduction**
- **Focus:** Establishes the context of SLAM and highlights the inconsistency problem with EKF-based SLAM.
- **Why this section?** It lays the groundwork for understanding why inconsistency in EKF is critical to SLAM's reliability.
- **Transition to Next Topic:** Since inconsistency affects the performance, the paper progresses to analyze the root cause through observability theory.

---

### **2. SLAM Observability Analysis**
- **Focus:** Discusses observability, a fundamental property for state estimation, and examines the nonlinear SLAM system's observability. It then compares this with the observability properties of the EKF linearized model.
- **Key Insights:**
  - The nonlinear SLAM system is unobservable with three degrees of freedom (position and orientation of the global frame).
  - The standard EKF incorrectly assumes higher observability, introducing inconsistency.
- **Why this section?** To identify how EKF's inconsistency originates from flawed observability assumptions.
- **Transition to Next Topic:** Moves towards solving this inconsistency by introducing a new approach to EKF formulation.

---

### **3. First Estimates Jacobian (FEJ)-EKF**
- **Focus:** Proposes a modified EKF algorithm, FEJ-EKF, which maintains observability consistency.
- **Key Features:**
  - Uses the initial estimates of state variables for Jacobian computation rather than continuously updating them.
  - Aligns the EKF's observable subspace with that of the nonlinear system.
- **Why this section?** To present a concrete solution to the inconsistency problem identified earlier.
- **Transition to Next Topic:** Evaluates the performance of FEJ-EKF against standard EKF and other alternatives.

---

### **4. Simulation Results**
- **Focus:** Validates the theoretical claims through extensive Monte Carlo simulations across different SLAM scenarios.
- **Key Findings:**
  - FEJ-EKF performs almost as well as the ideal EKF (oracle version) in accuracy and consistency.
  - It outperforms the standard EKF and robocentric mapping in terms of consistency.
- **Why this section?** Demonstrates the practical advantages of FEJ-EKF.
- **Transition to Next Topic:** Summarizes the conclusions and implications of the work.

---

### **5. Conclusions**
- **Focus:** Recaps the contributions, theoretical findings, and the effectiveness of the FEJ-EKF.
- **Why this section?** Provides a closure, emphasizing the importance of consistent filtering for SLAM and suggesting directions for future research.

---

The paper flows logically:
1. Introduces the inconsistency problem.
2. Analyzes its root cause using observability.
3. Proposes a solution (FEJ-EKF).
4. Validates the solution with simulations.
5. Summarizes and concludes.

The inconsistency in EKF-based SLAM occurs due to **errors in observability assumptions** made during the linearization process. Here's a detailed explanation of the *why* and *how*:

---

### **1. Why Does Inconsistency Occur?**
Inconsistency arises from a mismatch between:
- **The underlying nonlinear SLAM system's observability properties**: The nonlinear SLAM system has three unobservable degrees of freedom (DOF) — these correspond to the global position and orientation of the robot.
- **The EKF's linearized model's observability properties**: The linearized error-state system model of EKF-SLAM incorrectly assumes higher observability (two unobservable DOFs instead of three).

This mismatch leads to the EKF estimating "spurious information" — artificial reductions in uncertainty along the directions where no actual information is available. This results in:
1. **Overconfident estimates**: The covariance matrix of the state becomes unrealistically small, reflecting overconfidence in the estimates.
2. **Bias in state estimation**: The filter misinterprets the measurements, leading to degraded performance.

---

### **2. How Does Inconsistency Occur?**

#### **A. Linearization at Incorrect Points**
- EKF uses a **linearized model** of the SLAM system at every step.
- The Jacobians for state and measurement models are computed using the **latest estimates**, which differ at every timestep.
- This causes the EKF's linearized system to have an observable subspace of incorrect dimensionality, introducing inconsistency.

#### **B. Mismatched Observability**
- In the true nonlinear system:
  - The global position and orientation of the robot are unobservable because only **relative** measurements (e.g., distances to landmarks) are available.
  - This results in three unobservable DOFs.
- In the EKF:
  - The Jacobians evaluated at varying state estimates create an **unobservable subspace of only two dimensions**.
  - The EKF gains artificial information in the third (missing) unobservable direction, often associated with the robot's orientation.

#### **C. Error Accumulation**
- EKF updates the state estimate and covariance matrix with incorrect assumptions about observability.
- Over time, this leads to:
  - Shrinking covariance estimates in unobservable directions (e.g., global orientation).
  - Discrepancies between the true system behavior and EKF predictions.

#### **D. Impact of Observability Mismatch**
1. **Artificial Confidence**:
   - The EKF incorrectly assumes that measurements reduce uncertainty in unobservable directions (e.g., global frame rotation and translation).
   - This artificially inflates confidence in the state estimates.
2. **Measurement Overuse**:
   - Repeated observations of landmarks do not add information about the robot's global orientation or position.
   - However, EKF assumes that these observations reduce uncertainty in all directions, further exacerbating inconsistency.

---

### **Illustrative Example**
- **True Scenario**: A stationary robot observes a new landmark multiple times.
  - The true SLAM system knows that the new landmark provides no information about the robot's orientation.
- **EKF Behavior**: The EKF's covariance matrix for the robot's orientation shrinks incorrectly, assuming that the new landmark provides orientation information.
  - This leads to overconfidence in orientation estimates, causing bias and inconsistency.

---

### **Root Cause**
The inconsistency is fundamentally caused by the **linearization process**:
1. EKF evaluates Jacobians at varying state estimates instead of fixed (true or initial) values.
2. This shifts the observable subspace at every timestep, leading to spurious information gain.

---

### **Key Takeaway**
Inconsistency in EKF-SLAM arises from:
1. The incorrect assumption of observability in the linearized system.
2. The improper handling of state and measurement Jacobians, which changes the system's observable properties dynamically.

The paper addresses this issue by proposing the **First Estimates Jacobian (FEJ)-EKF**, which ensures that Jacobians are computed using the first available estimates for state variables, aligning the EKF's observability properties with the true nonlinear SLAM system.

