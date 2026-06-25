# YATAIGA (Preview)

**Simulate and optimize your quantum system using natural language / LaTeX.**

YATAIGA is a [Streamlit](https://streamlit.io/) web app for building, simulating,
and inspecting the time evolution of small quantum systems. You describe the
system, its Hamiltonian, and its control pulses through a simple guided UI —
entering operators directly in LaTeX (e.g. `\ket{e}\bra{g}`) — and the app
integrates the dynamics for you and plots how the populations and phases of your
chosen states evolve in time.

Under the hood the simulation is powered by [QuTiP](https://qutip.org/) via the
bundled `rdquantum` package.

## Features

- **Define quantum species** with named energy levels (e.g. `Rb` with levels `g e`).
- **Compose multi-body quantum systems** as tensor products of species
  (e.g. `Rb Rb Cs` for `|Rb⟩ ⊗ |Rb⟩ ⊗ |Cs⟩`).
- **Build Hamiltonians in LaTeX**, with a per-term constant (e.g. `2\pi`) and a
  transition operator (e.g. `\ket{e}\bra{g} + \ket{g}\bra{e}`).
- **Shape control pulses** — `square`, `cos`, or `sin` — with parameters
  exposed automatically from the pulse definition.
- **Choose target subsystems** for each operator, selected automatically from
  all valid placements within the system.
- **Simulate the dynamics** with QuTiP's Schrödinger solver (`sesolve`), choosing
  the initial state, operation time, and number of samples.
- **Visualize state evolution** — amplitude and phase of selected target states
  plotted over time.

## Project structure

```
.
├── app.py                 # Streamlit entry point
├── expt/                  # Streamlit UI layer
│   ├── expt.py            # `Expt` — drives the step-by-step UI workflow
│   └── set_pulse/         # Helpers to render pulse-parameter inputs
├── RDQuantum/             # `rdquantum` simulation engine (installable package)
│   └── rdquantum/qsim/
│       ├── qsim.py        # `QSim` — composes & runs the simulation
│       ├── qsystem.py     # `QSystem` — tensor-product quantum system
│       ├── quanta.py      # `Quanta` — a quantum species & its energy levels
│       ├── hamiltonian.py # `Hamiltonian` — operators + pulses, compiled for QuTiP
│       ├── operator.py    # `Operator` — builds density-matrix operators
│       ├── pulse/         # Pulse shapes and time-dependent coefficients
│       └── noise.py       # `Noise` — placeholder for open-system dynamics
├── requirements.txt
└── LICENSE                # Apache License 2.0
```

The app (`expt`) is a thin UI layer that collects user input and delegates the
physics to the `rdquantum` engine. `QSim.run_expt` builds the QuTiP Hamiltonian
(`Hamiltonian.compile`) and integrates it with `qutip.sesolve` (or `mesolve`
once noise is supported).

## Requirements

- Python 3.11+
- Dependencies (see `requirements.txt`): `numpy`, `matplotlib`, `sympy`,
  `streamlit`, the local `rdquantum` package (which pulls in `qutip`), and
  `antlr4-python3-runtime==4.11` (required by SymPy's LaTeX parser).

## Installation

```bash
# Clone the repository
git clone https://github.com/qfoam/yataiga-streamlit-preview.git
cd yataiga-streamlit-preview

# (Recommended) create and activate a virtual environment
python -m venv env
source env/bin/activate

# Install dependencies, including the local rdquantum package
pip install -r requirements.txt
```

> **Note:** `requirements.txt` installs `./RDQuantum` directly, so the
> `rdquantum` engine and its `qutip` dependency are set up for you.

## Usage

Launch the Streamlit app:

```bash
streamlit run app.py
```

Then follow the guided workflow in the browser:

1. **Quantum Species** 👻 — add a species and its energy levels
   (e.g. name `Rb`, levels `g e`).
2. **Quantum System** ⚛️ — combine species into a system
   (e.g. `Rb Rb Cs`).
3. **Operator** 🤖 — add a Hamiltonian term: a pulse label (e.g. `\Omega`),
   a constant (e.g. `2\pi`), and an operator in LaTeX
   (e.g. `\ket{e}\bra{g} + \ket{g}\bra{e}`).
4. **Pulse** 🔦 — choose a pulse shape (`square`, `cos`, `sin`) and its
   parameters.
5. **Target Subsystem** 🎯 — select which subsystem(s) each operator acts on,
   then **Submit** to assemble the simulator.
6. **Collect Data** 🏃‍♀️ — set the initial state (e.g. `00` for `|00⟩`),
   operation time (µs), and number of samples, then run the simulation and plot
   the amplitude and phase of your chosen target states over time.

## Status

This is a **preview**. Some capabilities are stubbed or in progress, including
open-system (noise / Lindblad) dynamics, additional pulse shapes such as
super-Gaussian, and a planned LaTeX → recipe translation layer.

## License

Licensed under the [Apache License 2.0](LICENSE).
