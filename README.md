# From a standard to a controller: Li-ion cell charging

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/YOUR_REPO/blob/main/MID2_standard_to_controller.ipynb)
![Run notebook](https://github.com/YOUR_USERNAME/YOUR_REPO/actions/workflows/run-notebook.yml/badge.svg)

Midterm project, **option 2**, for *Artificial Intelligence Applications in Engineering (MUH-920)*. The team is two electrical-electronics engineers and one chemical engineer.

Everything is in one notebook: **[`MID2_standard_to_controller.ipynb`](MID2_standard_to_controller.ipynb)**. It follows the course starter notebook (sections 1-5), is saved with all outputs, and needs only `numpy` and `matplotlib`.

## What it does

| Part | Content |
|---|---|
| 1. Rule base | A charging-safety and health check for a battery management system. It has 27 rules as dictionaries (`name / if / then / source`), built from the JEITA charging guide, cell datasheet limits, IEC 62133-2 and the 80 % end-of-life criterion. A sensing layer turns measurements into symbolic facts. |
| 2. Inference and explanation | `forward_chain`, `prove` (backward chaining with a proof tree), `explain_how` and `why_not`. There are three test cases (healthy, fault, borderline at 9.9 °C vs 10.1 °C) and a decision map. |
| 3. Fuzzy system | A Mamdani controller written from scratch. Inputs are cell temperature (5 sets) and SOC (3 sets), the output is charge current (5 sets). The rule table is complete with 15 rules. Membership functions, control surface and rule-strength explanation are included. |
| 4. Simulation | A 3 Ah NMC cell with an equivalent circuit, a thermal model and an illustrative ageing index (SEI growth plus lithium plating). Three controllers are compared: CC-CV with an on-off thermostat, the Part 1 rules used as a crisp JEITA charger, and the fuzzy charger. Tests cover cold, room and hot scenarios, the 0-44 °C range, 10 random seeds and a speed-versus-ageing trade-off. Measures are grouped as error, effort and switching. |
| 5. Discussion | Where the rules are brittle, how an expert should review them, and what a learned model would add or lose. |

## Main results (10 random cells, mean)

| | CC-CV + on-off | JEITA rules | Fuzzy |
|---|---|---|---|
| time to 80 % SOC [min] | 47.0 | **35.5** | 38.9 |
| degradation index (1 = 1C at 25 °C) | 2.03 | 1.86 | **1.67** |
| charger stops / current steps > 0.2C | 0.2 / 0.4 | 2.1 / 4.3 | **0 / 0** |

* Both rule-based and fuzzy charging are clearly better than CC-CV with a thermostat. The thermostat even **deadlocks in a 40 °C ambient**: it waits for 40 °C, which the cell never reaches.
* Over the whole speed-versus-ageing trade-off, the fuzzy and JEITA curves almost coincide. With the same knowledge, the fuzzy controller's advantage is **smoothness** (no stops, steps or chattering), not speed.
* The fuzzy charger is gentlest in the cold, where its degradation index is about 4 times lower. It is too slow at 0 °C, where it does not reach 80 % within 3 hours.
* The simulation exposed a brittle rule. The 1 °C/min self-heating threshold fires on normal Joule heating, which made the JEITA charger stop 21 times in one seed.

![trade-off](figures/tradeoff_speed_vs_ageing.png)
![scenarios](figures/scenarios_time_response.png)
![decision map](figures/rule_base_decision_map.png)
![fuzzy surface](figures/fuzzy_surface.png)

## How to run

* **Colab:** click the badge, then *Runtime → Run all*. It finishes in under a minute.
* **Locally:**
  ```bash
  pip install -r requirements.txt jupyter
  jupyter notebook MID2_standard_to_controller.ipynb
  ```
* **GitHub Actions:** `.github/workflows/run-notebook.yml` executes the whole notebook on every push and uploads the result.

> After creating the repository, replace `YOUR_USERNAME/YOUR_REPO` in this README and in the first cell of the notebook, and put your names in the team line.

## Limitations
The ageing index is semi-empirical and uncalibrated. Only its trend follows the literature (Waldmann et al., 2014). The cell is modelled as a single thermal mass. JEITA zone values follow typical charger ICs and must be checked against the datasheet of the actual cell.

## Team roles (suggested)
| Member | Responsibility |
|---|---|
| EEE 1 | Rule base, sensing layer, inference and explanation (sections 1-2) |
| EEE 2 | Fuzzy controller, cell model, simulations (sections 3-4) |
| ChE | Electrochemistry of plating and SEI, ageing model, rule review, discussion (sections 4.1 and 5) |

## License
Code MIT. The course starter notebook is by Prof. Dr. Utku Kose (CC BY 4.0 / MIT).
