## BasicAer

The `qiskit.BasicAer` object is not available in Qiskit 1.0. See the [basicaer migration section](https://docs.quantum.ibm.com/migration-guides/qiskit-1.0-features#global-instances-and-functions) for migration options.

## Aer

The `qiskit.Aer` object is not available in Qiskit 1.0. Instead, use the same object from the `qiskit_aer` namespace, which is a drop-in replacement. To install `qiskit_aer`, run:

```bash
pip install qiskit-aer
```

## execute

The `qiskit.execute` function is not available in Qiskit 1.0. This function served as a high-level wrapper around the `transpile` and `run` functions in Qiskit. Instead of `qiskit.execute`, use the `transpile` function followed by `backend.run()`.

```python
# Legacy path
from qiskit import execute

job = execute(circuit, backend)

# New path
from qiskit import transpile

new_circuit = transpile(circuit, backend)
job = backend.run(new_circuit)
```
Alternatively, the Sampler primitive is semantically equivalent to the removed qiskit.execute function. The class BackendSampler is a generic wrapper for backends that do not support primitives:
```python
from qiskit.primitives import BackendSampler

sampler = BackendSampler(backend)
job = sampler.run(circuit)
```

## QuantumCircuit Gates

The following gate methods have been removed in favor of more established methods that append the same gates:

| Removed                     | Alternative                   |
|-----------------------------|-------------------------------|
| `QuantumCircuit.cnot`        | `QuantumCircuit.cx`           |
| `QuantumCircuit.toffoli`     | `QuantumCircuit.ccx`          |
| `QuantumCircuit.fredkin`     | `QuantumCircuit.cswap`        |
| `QuantumCircuit.mct`         | `QuantumCircuit.mcx`          |
| `QuantumCircuit.i`           | `QuantumCircuit.id`           |
| `QuantumCircuit.squ`         | `QuantumCircuit.unitary`      |

The following circuit methods have been removed. Instead, these gates can be applied to a circuit with `QuantumCircuit.append`.

| Removed                      | Alternative (append)          |
|------------------------------|-------------------------------|
| `QuantumCircuit.diagonal`     | `DiagonalGate`                |
| `QuantumCircuit.hamiltonian`  | `HamiltonianGate`             |
| `QuantumCircuit.isometry`     | `Isometry`                    |
| `QuantumCircuit.iso`          | `Isometry`                    |
| `QuantumCircuit.uc`           | `UCGate`                      |
| `QuantumCircuit.ucrx`         | `UCRXGate`                    |
| `QuantumCircuit.ucry`         | `UCRYGate`                    |
| `QuantumCircuit.ucrz`         | `UCRZGate`                    |

For example, for a `DiagonalGate`:

```python
from qiskit.circuit import QuantumCircuit
from qiskit.circuit.library import DiagonalGate  # new location in the circuit library

circuit = QuantumCircuit(2)
circuit.h([0, 1])  # some initial state

gate = DiagonalGate([1, -1, -1, 1])
qubits = [0, 1]  # qubit indices on which to apply the gate
circuit.append(gate, qubits)  # apply the gate
```

## qiskit.providers

### basicaer

Most of the functionality in the `qiskit.providers.basicaer` module has been replaced with the new `qiskit.providers.basic_provider` module, except for the `UnitarySimulatorPy` and `StatevectorSimulatorPy` classes, which have been removed; their functionality was already contained in the `quantum_info` module.

The migration to the new paths is straightforward. You can replace most classes in `qiskit.providers.basicaer` with their `qiskit.providers.basic_provider` counterpart (drop-in replacement). Note that the following classes have new paths and names:

| Removed                          | Alternative                           |
|-----------------------------------|---------------------------------------|
| `qiskit.providers.basicaer`       | `qiskit.providers.basic_provider`     |
| `BasicAerProvider`                | `BasicProvider`                       |
| `BasicAerJob`                     | `BasicProviderJob`                    |
| `QasmSimulatorPy`                 | `BasicSimulator`                      |

### Global instances

Be aware of any global instances when migrating to the new module. There is no replacement for the `BasicAer` global instance that could be directly imported as `qiskit.BasicAer`. This means that:

```python
from qiskit import BasicProvider
```

```python
# Previous
from qiskit import BasicAer
backend = BasicAer.get_backend("backend_name")

# Current
from qiskit.providers.basic_provider import BasicProvider
backend = BasicProvider().get_backend("backend_name")
```


The unitary and statevector simulators can be replaced with different quantum_info classes. This is not a drop-in replacement, but the changes are minimal. See the following migration examples:
| Removed                          | Alternative                           |
|-----------------------------------|---------------------------------------|
| `UnitarySimulatorPy`              | `quantum_info.Operator`               |
| `StatevectorSimulatorPy`          | `quantum_info.Statevector`            |

The following examples show the migration paths of the `basicaer` simulators.

#### Statevector simulator

```python
from qiskit import QuantumCircuit

qc = QuantumCircuit(3)
qc.h(0)
qc.h(1)
qc.cx(1, 2)
qc.measure_all()

# Previous
from qiskit import BasicAer
backend = BasicAer.get_backend("statevector_simulator")
statevector = backend.run(qc).result().get_statevector()

# Current
qc.remove_final_measurements()  # no measurements allowed
from qiskit.quantum_info import Statevector
statevector = Statevector(qc)
```
#### QASM simulator

```python
from qiskit import QuantumCircuit
qc = QuantumCircuit(3)
qc.h(0)
qc.h(1)
qc.cx(1, 2)
qc.measure_all()

# Previous
from qiskit import BasicAer
backend = BasicAer.get_backend("qasm_simulator")
result = backend.run(qc).result()

# One current option
from qiskit.providers.basic_provider import BasicProvider
backend = BasicProvider().get_backend("basic_simulator")
result = backend.run(qc).result()

# Another current option is to specify it directly
from qiskit.providers.basic_provider import BasicSimulator
backend = BasicSimulator()
result = backend.run(qc).result()
```



## Source

(https://docs.quantum.ibm.com/migration-guides/qiskit-1.0-features)
