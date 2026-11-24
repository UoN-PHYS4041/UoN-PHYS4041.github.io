(sec-hardware)=
# Running on real quantum computers

One of the most exciting parts of this course is that **you can run your own circuits on real quantum computers, for free**. Every notebook in this course is written so that switching from a simulator to real hardware means changing a single line: the `backend`.

This page explains how to get access. You don't need hardware access for the course. Everything can be done on simulators, and the [projects](projects.md) don't require it. But running on a real device, and seeing how its results differ from the ideal simulation, makes for a much richer discussion in your paper.

| Provider | Devices | Free allocation | How to get it |
|---|---|---|---|
| **IBM Quantum** (class account) | IBM Heron superconducting QPUs (100+ qubits) | 10 min of QPU time per month, per student | Invitation from the module convenor |
| **IBM Quantum** (Open Plan) | as above | 10 min per rolling 28 days | Sign up yourself |
| **Quantum Inspire** (QuTech, Delft) | Small superconducting and spin-qubit chips (2–9 qubits), e.g. Tuna-5, Tuna-9, Starmon-7 | Free account | Sign up yourself |

Allocations and device lists change often. Always check the provider's website for the current details.

```{important}
Real devices are a shared, limited resource. **Always test your code on a simulator, and then on a fake (noisy) backend, before submitting to real hardware.** Your jobs will wait in a queue, which can take minutes to hours. Keep the number of circuits and shots small.
```

## IBM Quantum

### Option A: class account (recommended)

The module has an IBM Quantum *Classroom* account. If you'd like to use it, email the convenor and you'll get an invitation to IBM Quantum Platform. Accept it, and you'll have your own instance with 10 minutes of QPU time per month. No credit card is needed. Then follow the steps under [Save your credentials](#ibm-save-credentials).

### Option B: your own Open Plan account

1. Go to [quantum.cloud.ibm.com](https://quantum.cloud.ibm.com) and log in with an IBMid (create one if needed) or a Google account.
2. Create an **instance** on the **Open Plan**. Open Plan instances are created in the `us-east` region.
3. On the dashboard, create an **API key**. Copy it straight away, because it is only shown once.
4. On the *Instances* page, copy your instance's **CRN** (hover over it and click the copy icon).

(ibm-save-credentials)=
### Save your credentials (once)

Run this **once** in Python, pasting in your own API key and CRN. Don't put your key in a notebook that you share or submit!

```python
from qiskit_ibm_runtime import QiskitRuntimeService

QiskitRuntimeService.save_account(
    token="<your-API-key>",
    instance="<your-instance-CRN>",
    set_as_default=True,
    overwrite=True,
)
```

Your credentials are saved in `~/.qiskit/qiskit-ibm.json`.

### Running a circuit

```python
from qiskit_ibm_runtime import QiskitRuntimeService, SamplerV2 as Sampler
from qiskit.transpiler import generate_preset_pass_manager

service = QiskitRuntimeService()                                  # loads your saved account
backend = service.least_busy(operational=True, simulator=False)   # pick the least busy QPU
print(backend.name)

pm = generate_preset_pass_manager(backend=backend, optimization_level=3)
isa_circuit = pm.run(qc)                   # qc: your circuit, including measurements

job = Sampler(mode=backend).run([isa_circuit], shots=1000)
print(job.job_id())                        # keep this: you can retrieve results later
counts = job.result()[0].data.meas.get_counts()
```

This is exactly the code used with the fake backend `FakeFez()` in the course notebooks. You can check your queue position and remaining time on the IBM Quantum Platform website. To pick up a job's results later:

```python
job = service.job("<job-id>")
result = job.result()
```

## Quantum Inspire

[Quantum Inspire](https://www.quantum-inspire.com) is QuTech's quantum computing platform. It offers free accounts with access to small hardware devices and emulators.

1. Create a free account through **My QI** at [compute.quantum-inspire.com](https://compute.quantum-inspire.com).
2. Log in from a terminal, inside the course environment:
   ```bash
   uv run qi login
   ```
   This opens a browser to authenticate, then stores your credentials locally.
3. List the available backends and run a circuit:
   ```python
   from qiskit import transpile
   from qiskit_quantuminspire.qi_provider import QIProvider

   provider = QIProvider()
   print(provider.backends())                 # see what is online today

   backend = provider.get_backend("Tuna-5")    # use a name from the list above
   qc_compiled = transpile(qc, backend)        # map to the device's native gates and connectivity
   job = backend.run(qc_compiled, shots=1024)
   counts = job.result().get_counts()
   ```

Use `provider.get_backend("QX emulator")` to test your code on Quantum Inspire's simulator first. Quantum Inspire's devices are small (a handful of qubits), which is plenty for the circuits in Lectures 2–4 and a great opportunity to compare different hardware platforms.

## Other providers

- **IQM Resonance** has a free *Starter* tier with a monthly credit allowance on IQM's superconducting devices. Its main SDK is Qrisp. Qiskit support exists but is not part of the course environment.
- **Amazon Braket** and **Microsoft Azure Quantum** give access to many hardware types (trapped ions, neutral atoms, photonics), but hardware time is paid.
