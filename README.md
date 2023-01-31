<div align="center">

<img src="https://jktechnosoftltd-my.sharepoint.com/:i:/g/personal/rana_banerjee_jktech_com/EZ7qfTo9kBlBlpknduSoescBZBeSF89lrQZXT4QSjN-oVg?e=Y9YtU4" width="250px">

**JARVIS Agent - ML-Ops made easy  
ML-Ops scheduler & orchestration solution supporting Linux, macOS and Windows**

[![GitHub license](https://img.shields.io/github/license/allegroai/clearml-agent.svg)](https://img.shields.io/github/license/allegroai/clearml-agent.svg)
[![PyPI pyversions](https://img.shields.io/pypi/pyversions/clearml-agent.svg)](https://img.shields.io/pypi/pyversions/clearml-agent.svg)
[![PyPI version shields.io](https://img.shields.io/pypi/v/clearml-agent.svg)](https://img.shields.io/pypi/v/clearml-agent.svg)
[![PyPI Downloads](https://pepy.tech/badge/clearml-agent/month)](https://pypi.org/project/clearml-agent/)
[![Artifact Hub](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/allegroai)](https://artifacthub.io/packages/search?repo=allegroai)
</div>

---

### JARVIS-Agent

#### *Formerly known as Trains Agent*

* Run jobs (experiments) on any local or cloud based resource
* Implement optimized resource utilization policies
* Deploy execution environments with either virtualenv or fully docker containerized with zero effort
* Launch-and-Forget service containers
* [Cloud autoscaling](https://clear.ml/docs/latest/docs/guides/services/aws_autoscaler)
* [Customizable cleanup](https://clear.ml/docs/latest/docs/guides/services/cleanup_service)
*
Advanced [pipeline building and execution](https://clear.ml/docs/latest/docs/guides/frameworks/pytorch/notebooks/table/tabular_training_pipeline)

It is a zero configuration fire-and-forget execution agent, providing a full ML/DL cluster solution.

**Full Automation in 5 steps**

1. JARVIS Server [self-hosted](https://github.com/allegroai/jarvis-server)
   or [free tier hosting](https://app.clear.ml)
2. `pip install jarvis-agent` ([install](#installing-the-jarvis-agent) the JARVIS Agent on any GPU machine:
   on-premises / cloud / ...)
3. Create a [job](https://github.com/allegroai/jarvis/docs/jarvis-task.md) or
   Add [JARVIS](https://github.com/allegroai/jarvis) to your code with just 2 lines
4. Change the [parameters](#using-the-jarvis-agent) in the UI & schedule for [execution](#using-the-jarvis-agent) (or
   automate with an [AutoML pipeline](#automl-and-orchestration-pipelines-))
5. :chart_with_downwards_trend: :chart_with_upwards_trend: :eyes:  :beer:

"All the Deep/Machine-Learning DevOps your research needs, and then some... Because ain't nobody got time for that"

**Try JARVIS now** [Self Hosted](https://github.com/allegroai/jarvis-server)
or [Free tier Hosting](https://app.clear.ml)
<a href="https://app.clear.ml"><img src="https://github.com/allegroai/jarvis-agent/blob/master/docs/screenshots.gif?raw=true" width="100%"></a>

### Simple, Flexible Experiment Orchestration

**The JARVIS Agent was built to address the DL/ML R&D DevOps needs:**

* Easily add & remove machines from the cluster
* Reuse machines without the need for any dedicated containers or images
* **Combine GPU resources across any cloud and on-prem**
* **No need for yaml / json / template configuration of any kind**
* **User friendly UI**
* Manageable resource allocation that can be used by researchers and engineers
* Flexible and controllable scheduler with priority support
* Automatic instance spinning in the cloud

**Using the JARVIS Agent, you can now set up a dynamic cluster with \*epsilon DevOps**

*epsilon - Because we are :triangular_ruler: and nothing is really zero work

### Kubernetes Integration (Optional)

We think Kubernetes is awesome, but it should be a choice. We designed `jarvis-agent` so you can run bare-metal or
inside a pod with any mix that fits your environment.

Find Dockerfiles in the [docker](./docker) dir and a helm Chart in https://github.com/allegroai/jarvis-helm-charts

#### Benefits of integrating existing K8s with JARVIS-Agent

- JARVIS-Agent adds the missing scheduling capabilities to K8s
- Allowing for more flexible automation from code
- A programmatic interface for easier learning curve (and debugging)
- Seamless integration with ML/DL experiment manager
- Web UI for customization, scheduling & prioritization of jobs

**Two K8s integration flavours**

- Spin JARVIS-Agent as a long-lasting service pod
    - use [jarvis-agent](https://hub.docker.com/r/allegroai/jarvis-agent) docker image
    - map docker socket into the pod (soon replaced by [podman](https://github.com/containers/podman))
    - allow the jarvis-agent to manage sibling dockers
    - benefits: full use of the JARVIS scheduling, no need to worry about wrong container images / lost pods etc.
    - downside: Sibling containers
- Kubernetes Glue, map JARVIS jobs directly to K8s jobs
    - Run the [jarvis-k8s glue](https://github.com/allegroai/jarvis-agent/blob/master/examples/k8s_glue_example.py) on
      a K8s cpu node
    - The jarvis-k8s glue pulls jobs from the JARVIS job execution queue and prepares a K8s job (based on provided
      yaml template)
    - Inside the pod itself the jarvis-agent will install the job (experiment) environment and spin and monitor the
      experiment's process
    - benefits: Kubernetes full view of all running jobs in the system
    - downside: No real scheduling (k8s scheduler), no docker image verification (post-mortem only)

### Using the JARVIS Agent

**Full scale HPC with a click of a button**

The JARVIS Agent is a job scheduler that listens on job queue(s), pulls jobs, sets the job environments, executes the
job and monitors its progress.

Any 'Draft' experiment can be scheduled for execution by a JARVIS agent.

A previously run experiment can be put into 'Draft' state by either of two methods:

* Using the **'Reset'** action from the experiment right-click context menu in the JARVIS UI - This will clear any
  results and artifacts the previous run had created.
* Using the **'Clone'** action from the experiment right-click context menu in the JARVIS UI - This will create a new '
  Draft' experiment with the same configuration as the original experiment.

An experiment is scheduled for execution using the **'Enqueue'** action from the experiment right-click context menu in
the JARVIS UI and selecting the execution queue.

See [creating an experiment and enqueuing it for execution](#from-scratch).

Once an experiment is enqueued, it will be picked up and executed by a JARVIS agent monitoring this queue.

The JARVIS UI Workers & Queues page provides ongoing execution information:

- Workers Tab: Monitor you cluster
    - Review available resources
    - Monitor machines statistics (CPU / GPU / Disk / Network)
- Queues Tab:
    - Control the scheduling order of jobs
    - Cancel or abort job execution
    - Move jobs between execution queues

#### What The JARVIS Agent Actually Does

The JARVIS Agent executes experiments using the following process:

- Create a new virtual environment (or launch the selected docker image)
- Clone the code into the virtual-environment (or inside the docker)
- Install python packages based on the package requirements listed for the experiment
    - Special note for PyTorch: The JARVIS Agent will automatically select the torch packages based on the CUDA_VERSION
      environment variable of the machine
- Execute the code, while monitoring the process
- Log all stdout/stderr in the JARVIS UI, including the cloning and installation process, for easy debugging
- Monitor the execution and allow you to manually abort the job using the JARVIS UI (or, in the unfortunate case of a
  code crash, catch the error and signal the experiment has failed)

#### System Design & Flow

<img src="https://github.com/allegroai/jarvis-agent/blob/master/docs/clearml_architecture.png" width="100%" alt="jarvis-architecture">

#### Installing the JARVIS Agent

```bash
pip install jarvis-agent
```

#### JARVIS Agent Usage Examples

Full Interface and capabilities are available with

```bash
jarvis-agent --help
jarvis-agent daemon --help
```

#### Configuring the JARVIS Agent

```bash
jarvis-agent init
```

Note: The JARVIS Agent uses a cache folder to cache pip packages, apt packages and cloned repositories. The default
JARVIS Agent cache folder is `~/.jarvis`

See full details in your configuration file at `~/jarvis.conf`

Note: The **JARVIS agent** extends the **JARVIS** configuration file `~/jarvis.conf`
They are designed to share the same configuration file, see example [here](docs/jarvis.conf)

#### Running the ClearML Agent

For debug and experimentation, start the ClearML agent in `foreground` mode, where all the output is printed to screen

```bash
jarvis-agent daemon --queue default --foreground
```

For actual service mode, all the stdout will be stored automatically into a temporary file (no need to pipe)
Notice: with `--detached` flag, the *jarvis-agent* will be running in the background

```bash
jarvis-agent daemon --detached --queue default
```

GPU allocation is controlled via the standard OS environment `NVIDIA_VISIBLE_DEVICES` or `--gpus` flag (or disabled
with `--cpu-only`).

If no flag is set, and `NVIDIA_VISIBLE_DEVICES` variable doesn't exist, all GPU's will be allocated for
the `jarvis-agent` <br>
If `--cpu-only` flag is set, or `NVIDIA_VISIBLE_DEVICES="none"`, no gpu will be allocated for
the `jarvis-agent`

Example: spin two agents, one per gpu on the same machine:
Notice: with `--detached` flag, the *jarvis-agent* will be running in the background

```bash
jarvis-agent daemon --detached --gpus 0 --queue default
jarvis-agent daemon --detached --gpus 1 --queue default
```

Example: spin two agents, pulling from dedicated `dual_gpu` queue, two gpu's per agent

```bash
jarvis-agent daemon --detached --gpus 0,1 --queue dual_gpu
jarvis-agent daemon --detached --gpus 2,3 --queue dual_gpu
```

##### Starting the JARVIS Agent in docker mode

For debug and experimentation, start the JARVIS agent in `foreground` mode, where all the output is printed to screen

```bash
jarvis-agent daemon --queue default --docker --foreground
```

For actual service mode, all the stdout will be stored automatically into a file (no need to pipe)
Notice: with `--detached` flag, the *jarvis-agent* will be running in the background

```bash
jarvis-agent daemon --detached --queue default --docker
```

Example: spin two agents, one per gpu on the same machine, with default nvidia/cuda:10.1-cudnn7-runtime-ubuntu18.04
docker:

```bash
jarvis-agent daemon --detached --gpus 0 --queue default --docker nvidia/cuda:10.1-cudnn7-runtime-ubuntu18.04
jarvis-agent daemon --detached --gpus 1 --queue default --docker nvidia/cuda:10.1-cudnn7-runtime-ubuntu18.04
```

Example: spin two agents, pulling from dedicated `dual_gpu` queue, two gpu's per agent, with default nvidia/cuda:
10.1-cudnn7-runtime-ubuntu18.04 docker:

```bash
jarvis-agent daemon --detached --gpus 0,1 --queue dual_gpu --docker nvidia/cuda:10.1-cudnn7-runtime-ubuntu18.04
jarvis-agent daemon --detached --gpus 2,3 --queue dual_gpu --docker nvidia/cuda:10.1-cudnn7-runtime-ubuntu18.04
```

##### Starting the JARVIS Agent - Priority Queues

Priority Queues are also supported, example use case:

High priority queue: `important_jobs`  Low priority queue: `default`

```bash
jarvis-agent daemon --queue important_jobs default
```

The **JARVIS Agent** will first try to pull jobs from the `important_jobs` queue, only then it will fetch a job from
the `default` queue.

Adding queues, managing job order within a queue and moving jobs between queues, is available using the Web UI, see
example on our [free server](https://app.clear.ml/workers-and-queues/queues)

##### Stopping the JARVIS Agent

To stop a **JARVIS Agent** running in the background, run the same command line used to start the agent with `--stop`
appended. For example, to stop the first of the above shown same machine, single gpu agents:

```bash
jarvis-agent daemon --detached --gpus 0 --queue default --docker nvidia/cuda:10.1-cudnn7-runtime-ubuntu18.04 --stop
```

### How do I create an experiment on the JARVIS Server? <a name="from-scratch"></a>

* Integrate [JARVIS](https://github.com/allegroai/jarvis) with your code
* Execute the code on your machine (Manually / PyCharm / Jupyter Notebook)
* As your code is running, **JARVIS** creates an experiment logging all the necessary execution information:
    - Git repository link and commit ID (or an entire jupyter notebook)
    - Git diff (we’re not saying you never commit and push, but still...)
    - Python packages used by your code (including specific versions used)
    - Hyper-Parameters
    - Input Artifacts

  You now have a 'template' of your experiment with everything required for automated execution

* In the JARVIS UI, Right-click on the experiment and select 'clone'. A copy of your experiment will be created.
* You now have a new draft experiment cloned from your original experiment, feel free to edit it
    - Change the Hyper-Parameters
    - Switch to the latest code base of the repository
    - Update package versions
    - Select a specific docker image to run in (see docker execution mode section)
    - Or simply change nothing to run the same experiment again...
* Schedule the newly created experiment for execution: Right-click the experiment and select 'enqueue'

### JARVIS-Agent Services Mode <a name="services"></a>

JARVIS-Agent Services is a special mode of JARVIS-Agent that provides the ability to launch long-lasting jobs that
previously had to be executed on local / dedicated machines. It allows a single agent to launch multiple dockers (Tasks)
for different use cases. To name a few use cases, auto-scaler service (spinning instances when the need arises and the
budget allows), Controllers (Implementing pipelines and more sophisticated DevOps logic), Optimizer (such as
Hyper-parameter Optimization or sweeping), and Application (such as interactive Bokeh apps for increased data
transparency)

JARVIS-Agent Services mode will spin **any** task enqueued into the specified queue. Every task launched by
JARVIS-Agent Services will be registered as a new node in the system, providing tracking and transparency capabilities.
Currently jarvis-agent in services-mode supports cpu only configuration. JARVIS-agent services mode can be launched
alongside GPU agents.

```bash
jarvis-agent daemon --services-mode --detached --queue services --create-queue --docker ubuntu:18.04 --cpu-only
```

**Note**: It is the user's responsibility to make sure the proper tasks are pushed into the specified queue.

### AutoML and Orchestration Pipelines <a name="automl-pipes"></a>

The JARVIS Agent can also be used to implement AutoML orchestration and Experiment Pipelines in conjunction with the
JARVIS package.

Sample AutoML & Orchestration examples can be found in the
JARVIS [example/automation](https://github.com/allegroai/jarvis/tree/master/examples/automation) folder.

AutoML examples

- [Toy Keras training experiment](https://github.com/allegroai/jarvis/blob/master/examples/optimization/hyper-parameter-optimization/base_template_keras_simple.py)
    - In order to create an experiment-template in the system, this code must be executed once manually
- [Random Search over the above Keras experiment-template](https://github.com/allegroai/jarvis/blob/master/examples/automation/manual_random_param_search_example.py)
    - This example will create multiple copies of the Keras experiment-template, with different hyper-parameter
      combinations

Experiment Pipeline examples

- [First step experiment](https://github.com/allegroai/jarvis/blob/master/examples/automation/task_piping_example.py)
    - This example will "process data", and once done, will launch a copy of the 'second step' experiment-template
- [Second step experiment](https://github.com/allegroai/jarvis/blob/master/examples/automation/toy_base_task.py)
    - In order to create an experiment-template in the system, this code must be executed once manually

### License

Apache License, Version 2.0 (see the [LICENSE](https://www.apache.org/licenses/LICENSE-2.0.html) for more information)
