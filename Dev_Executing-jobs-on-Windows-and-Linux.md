A piece of common functionality identified for CodaLab is the need to execute computations on remote servers running Windows ([Trello card](https://trello.com/c/SmI2dneY)) or Linux ([Trello card](https://trello.com/c/uvjdcy5H)).

### Scenarios

The current work on CodaLab experiments emphasizes execution on the local machine. In this model, the CodaLab site acts as a web-accessible store of datasets and programs. In order to execute code, researchers first install the CodaLab execution engine on their own workstation. Then, the local executor is able to pull existing bundles from the CodaLab site so that they can be used as input to new experiments. Experiments run locally and produce new results. Some experiments may be discarded but worthwhile experiments can be preserved by asking the local executor to push them to the CodaLab web sites. By pushing an experiment to CodaLab, we mean uploading a set of bundles which provide a complete definition of the experiment such that it can be reproduced. 

So-called local execution is important because it provides the researcher with a familiar (command-line) interface and a very fluent way to experiment. However, there are situations when local, interactive execution is not desired. For example:

* **Evaluating competition submissions**. A competition organizer does not want to evaluate every submission manually. Instead, when a competition participant uploads a submission, the submission should be evaluated automatically by a running service. The running service may be hosted by CodaLab on behalf of the competition organizer or it may be hosted directly by the competition organizer.
 
 As the experimentation platform becomes reality, we can imagine that a competition participant would leverage CodaLab's local execution engine to prepare a submission. Indeed, a submission is nothing but an experiment with input data supplied by the competition organizer and the program supplied by the participant. However, pushing the finished experiment to the CodaLab site would have a different meaning because it would entail making a submission to the appropriate competition. The submission would trigger a signal to the computation service that it should evaluate the given submission.

* **Simplified analysis**. Some users have small datasets which could be better analyzed by applying machine learning techniques. As the owner of one of those datasets, I should be able to upload it to the CodaLab web site, select some methods of analysis (which may be recommended), click the "Analyze" button and get results. For a more specific scenario, see the original specification (link? "I am a biologist and I just gathered some measurements of gene expression under various conditions in a spreadsheet..."). Doing the analysis requested by the user requires the ability to quickly run computations on the CodaLab servers.

* **Systematic analysis**. Quoting from the original specification (link?): "The system proactively runs programs on datasets in the background, and does some meta-learning".

* **Create experiments in the cloud**. In the future, users may be empowered to create and run experiments directly in the CodaLab web site. There would be no requirement to first download a local execution engine.

Related scenarios:

* **CodaLab site long-running tasks**. Any time the CodaLab web site needs to perform a long-running task, the task needs to be delegated to a background worker. The same infrastructure used to run computations can be re-used.

### Approach

At a high-level, we have the bipolar system depicted below. On one end, tasks are being produced. An homogeneous set of workers --located at the other end-- can carry out the tasks. A given task only needs to be executed once by one of the workers. The task producing entity needs the ability to delegate a task to the worker pool and needs to be notified of the task progress until it is done.

![Figure 1]()
_Figure 1_

The system should:
* Be secure: workers should have access to the information they need and nothing more.
* Have minimal latency: end-user should see less than one second response time for lightweight tasks.
* Be simple to setup: a competition organizer should be able to setup a worker pool with relative ease.
* Be simple, reliable and available.  

We are proposing to leverage cloud-based, durable queues to connect task producers and task consumers. [Windows Azure Service Bus Queues](http://www.windowsazure.com/en-us/develop/python/how-to-guides/service-bus-queues/) are simple to use across a variety of platforms and languages. [Service Bus Queues](http://msdn.microsoft.com/en-us/library/hh767287.aspx) provide First In/First Out message delivery across competing consumers. They support at least once and at most once delivery. They are durable, offer the ability to schedule a message for later delivery and offer latency below 100ms. Service Bus Queues also provides [various authentication mechanisms](http://msdn.microsoft.com/en-us/library/windowsazure/dn170478.aspx).

Following is a sketch of the workflow that would take place for the case when a competition organizer supplies a set of their compute nodes.

First, the organizer comes to CodaLab web site to create a competition and specifies that compute workers will be hosted outside of the CodaLab servers. A dedicated queue is created to allow CodaLab to communicate with the compute workers. The organizer is given connection information to the queue and connection information to the response queue (response from the workers to the CodaLab site). Given a set of instructions (or scripts or tools), the organizer deploys the compute workers and configures them. This includes establishing credentials and configuration that allows communication with CodaLab (receiving a task, reading/writing bundles, writing standard output/error, giving updates about task status). Later, when a submission is made for the competition, the CodaLab web site will direct the evaluation task to the competition-specific workers. A message will be placed on the dedicated queue; a worker will pick up the message and do the work; the worker will acknowledge that the task is complete by placing a message on the return queue; a watch process on the CodaLab site will pick up the message and do the bookkeeping that will update the site's database. 

### Task breakdown

1. CodaLab site queue & task management.
    1. Ability to manage a list of queues. Each queue represents a worker pool.
    1. Task model.
    1. Ability to produce and receive tasks. Base producer/receiver implementation including working out details of authentication/authorization.
    1. Background worker to monitor running tasks.
    1. Testability.
    1. Integration with competition creation/editing.
1. Compute workers 
    1. Linux.
        1. Service implementation and installation on existing VM.
        1. User-friendly VM provisioning. Include defining base software on the VM (a la MLCOMP).
    1. Windows.
        1. Service implementation and installation on existing VM.
        1. User-friendly Deployment automation. Include defining base software on the VM (a la MLCOMP).
    1. Shared.
        1. Execution driver. This should be using the new execution driver which is currently being prototypes; otherwise the existing driver would be re-used and would need validation/testing on Linux.
        2. Ability to direct standard output/error directly to Blobs. Need port of current approach to Python.
   
### Assumptions and open questions

1. There is an assumption that the compute workers have read-write access to bundles via a service. Without the service, they could access Blob storage directly but Shared Access Signature would have to be given appropriately.
1. The compute worker will execute user-provided code. An open question is how to sandbox the user-provided program on each target platform (Windows + Linux). What did MLCOMP do? Is this a technology issue or a terms of use issue?
2. What long term management issues will arise? Patches need to be applied to software, certificates expire...
