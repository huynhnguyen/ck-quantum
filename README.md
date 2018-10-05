# Hackaton with London Quantum Computing Meetup

* [6 October 2018](https://www.meetup.com/London-Quantum-Computing-Meetup/events/254156028/) (**fully booked 5 weeks in advance!**)

## Preparation before the hackathon (this will save you time on the day!)

1. Please register at [Quantum Experience](https://quantumexperience.ng.bluemix.net/qx/signup) and copy your API token from the ["Advanced"](https://quantumexperience.ng.bluemix.net/qx/account/advanced) tab (click on the "Regenerate" button first).
1. Follow [CK-QISKit instructions](https://github.com/ctuning/ck-qiskit).

## Run QISKit-VQE once

First, deploy a VQE ansatz and optimizer plugins that should just work:
```
$ ck deploy_optimizer vqe --value=optimizer.cobyla
$ ck deploy_ansatz vqe --value=ansatz.universal4
```

Then, launch VQE with the deployed optimizer and ansatz:
```
$ ck run vqe --device=local_qasm_simulator --repetitions=1
```

## Monitor the convergence process (an ASCII-graphics program run in a separate Terminal window)
```
ck run program:visualize-convergence
```


## Easy VQE exploration via optimizer parameters
```
$ ck run vqe --device=<device> --sample_size=<sample_size> --max_iterations=<max_iterations> --repetitions=<repetitions>
```
where:
- `device`: `local_qasm_simulator` (local simulator), `ibmq_qasm_simulator` (remote simulator), `ibmqx4` (remote hardware); by default, QCK will prompt to select one of these target quantum devices (0, 1, 2).
- `sample_size`: the number of times to evaluate the Hamiltonian function on the quantum device ("sampling resolution") per optimizer iteration; by default, 100.
- `max_iterations`: the maximum number of optimizer iterations ("iteration limit"); by default, 80.
- `repetitions`: the number of times to repeat the experiment with the same parameters; by default, 3.

**NB:** The aim is to minimize the [Time-To-Solution](https://nbviewer.jupyter.org/urls/dl.dropbox.com/s/d9iysrawnprjy2w/ck-quantum-hackathon-20180615.ipynb#Time-to-solution-metric) (TTS) metric. As TTS is proportional to `sample_size`, the metric penalizes using higher values of `sample_size` for no good reason! At the same time, a low number of `repetitions` may make it hard to demonstrate solution convergence with a high probability, so using 5-10 `repetitions` is a good guess.

## Advanced VQE exploration via plugins

### See which plugins are deployed (both `soft` and `env` entries)
```
$ ck search --tags=deployed
```

### Removing plugins

#### Removing an optimizer plugin
```
$ ck cleanup vqe --type=optimizer
```

#### Removing an ansatz plugin
```
$ ck cleanup vqe --type=ansatz
```

#### Removing both optimizer and ansatz plugins
```
$ ck cleanup vqe
```

### Working with optimizer plugins

#### Select an optimizer plugin to deploy
```
$ ck deploy_optimizer vqe
```

#### Locate and edit the deployed optimizer plugin (use your favourite text editor instead of `vi`)
```
$ ck plugin_path vqe --type=optimizer
$ vi `ck plugin_path vqe --type=optimizer`
```
**NB:** The optimizer plugin is written in Python.
It is expected to contain only one top-level function.
If you need more, please define them within the top-level one.

### Working with ansatz plugins

#### Select an ansatz plugin to deploy
```
$ ck deploy_ansatz vqe
```

#### Visualize the ansatz circuit (use your favourite image viewer instead of `display`)
```
$ ck run program:visualize-ansatz
$ display `ck find program:visualize-ansatz`/ansatz_circuit.png
```
**NB:** If unsure about the image viewer, try `eog` or `eom` on Linux, `open` on macOS.

#### Locate and edit the deployed ansatz plugin (use your favourite text editor instead of `vi`)
```
$ ck plugin_path vqe --type=ansatz
$ vi `ck plugin_path vqe --type=ansatz`
```
**NB:** The ansatz plugin is written in Python with QISKit.
It is expected to contain only one top-level function.
If you need more, please define them within the top-level one.

## Review the results (e.g. using the time-to-solution parametric metric)
```
$ ck search local:experiment:*                                                          # the list of all of your experimental entries
$ ck find local:experiment:*                                                            # where the experiment directories are located
$ ck time_to_solution vqe --delta=0.015 --prob=0.95                                     # TTS (pick the experiment entry interactively)
OR
$ ck time_to_solution vqe local:experiment:my_experiment_10 --delta=0.015 --prob=0.8    # TTS (supply the experiment from command line)
```

## Upload the results of your experiments to Quantum Collective Knowledge
```
$ ck upload vqe --team=schroedinger-cat-herders                                         # upload (pick the experiment entry interactively)
OR
$ ck upload vqe --team=bell-state-ringers local:experiment:my_experiment_5 local:experiment:my_experiment_13  # upload (supply the experiment from command line)
```
