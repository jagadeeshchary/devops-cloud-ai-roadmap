1. A container is a package of dependencies, runtimes, libraries and all other pre requisities for an application.
Better Version

A container is a lightweight package that contains an application along with its runtime, libraries, dependencies, configuration, and everything required to run consistently across different environments.

2. Companies use containers because containers installs the applications with all the dependencies that are required to run the application in any server.

Better Version

Companies use containers because they provide a consistent environment. The application runs the same on a developer's laptop, test server, and production server without dependency conflicts.

3. Docker is a tool that builds and runs the Containers.


4. Kubernetes is the tool which manages the Containers like restarting, scalling etc.

Better Version

Kubernetes is a container orchestration platform that manages Pods by automating deployment, scaling, self-healing, and networking.

5. A Pod contains one or more containers which requires sharing network and storage.
6. A Node contains one or more pods.

Better Version

A Node is a physical or virtual machine in a Kubernetes cluster that runs one or more Pods.

7. A Cluster has one or more nodes which is managed by Kubernetes, which will run the pods in it to manage the containers.

Better Version

A Kubernetes Cluster consists of one or more Nodes managed by the Control Plane. The cluster provides an environment to deploy and manage Pods.

8. A Desired State is the count set by the admin or the user, where kubernetes will replicate the pods that many times when a failure occurs.



9. If a pod crashes, it will we recreated by the kubernetes based on the Desired State till that is met.
10. If a worker Node crashes, Kubernetes will recreates the pods from the crashed Node into a healthy worker nodes when sufficient resources (CPU, memory, etc.) are available.
