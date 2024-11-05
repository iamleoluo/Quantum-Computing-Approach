## BasicAer

The `qiskit.BasicAer` object is not available in Qiskit 1.0. See the [basicaer migration section](#) for migration options.

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
##Source

(https://docs.quantum.ibm.com/migration-guides/qiskit-1.0-features)
