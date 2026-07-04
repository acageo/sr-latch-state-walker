# Synchronous SR Latch State Walker

A low-level, first-principles implementation of a synchronous Finite State Machine (FSM) designed using discrete NPN transistor memory and highly optimized combinational steering logic. 

This project demonstrates the transition from abstract mathematical logic (Karnaugh Maps) to physical circuit topology, optimizing component count to the absolute minimum footprint instead of relying on pre-packaged integrated logic chips.

## 🔗 Project Links
* **Live Prototyping Bench:** [Tinkercad Project Link](https://www.tinkercad.com/things/aeGNTzE02Ms-202607021600-neat-fulffy)
  * *Technical Note on Simulation Behavior:* Due to the tight, zero-propagation-delay feedback loop modeling inside the Tinkercad digital engine, triggering the `RESET Q` condition creates an instantaneous combinational race condition that can cause the simulator thread to deadlock. This is a known software artifact of ideal SPICE/digital matrix solvers when processing perfectly synchronized, zero-delay feedback loops. A physical hardware implementation natively bypasses this via real-world transistor propagation delays ($\approx 10\text{ns}$), which break the mathematical symmetry.

---

## 🗺️ State Machine & Logic Architecture
The state walker processes a multi-state sequence (including pedestrian traffic signal behaviors like blinking yellow, green, and red states) driven by an input clock/button pulse ($L$).

### 1. Mathematical Optimization (K-Maps)
Through rigorous minimization using Karnaugh Maps and exploiting "Don't Care" conditions, the raw next-state expressions were collapsed into highly efficient, minimal product terms:
* **Set P (High Bit):** $S_p = L \cdot \overline{S_1} \cdot S_0$
* **Reset P (High Bit):** $R_p = L \cdot S_1 \cdot S_0$
* **Set Q (Low Bit):** $S_q = L \cdot \overline{S_0}$
* **Reset Q (Low Bit):** $R_q = L \cdot S_0$

### 2. Cascaded Gate Reduction
Instead of utilizing specialized 3-input AND gates or sprawling OR gate networks, a critical hardware optimization was implemented. By recognizing that the sub-expression **$L \cdot S_0$** is identical in both the high-bit Set and Reset equations, the output of the $R_q$ gate was cascaded directly into the inputs of the $P$ latch steering logic. 

This dropped the entire combinational steering engine down to exactly **four uniform 2-input AND gates**, drastically reducing physical component count, wiring complexity, and propagation paths.

---

## 🛠️ Hardware Implementation Details
* **Memory Core:** Dual cross-coupled NOR latches constructed entirely out of discrete NPN transistors (Resistor-Transistor Logic / RTL configuration).
* **Clock Subsystem:** Integrated local frequency generator acting as a localized blinker subsystem coupled with manual step inputs to advance states.

### 🧪 Overcoming Simulation Deadlocks
During virtual testing, the circuit encountered a classic simulator deadlock—an infinite mathematical calculation loop caused by the zero-delay feedback model of perfectly synchronized ideal components. 

To break the software symmetry, real-world physical intuition was applied: a **$1\text{ pF}$ micro-capacitor kick** was introduced to the base of a single transistor. This added a tiny, asymmetric propagation delay that allowed the simulator to resolve states cleanly, exactly mimicking real-world component tolerances and variations.

---

## 📁 Repository Structure
* `/Schematics` - Circuit layouts (Explicit vs. Optimistic Cascaded Models).
* `/K-Maps` - Truth tables and minimization matrices.
* `/Simulation` - Links and documentation regarding the Tinkercad prototyping bench.
