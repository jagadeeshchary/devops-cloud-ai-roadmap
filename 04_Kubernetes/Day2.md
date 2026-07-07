1. Deployement defines the desired state andii mangages pods with replicaset, which helps in scaling, fail safe, recreating etc.

2. ReplicaSet is a controller in Deployment which is assigned with a desired state number, it maintains the number of pods to meet this desired number for the application.

3. Pods cant be created in production directly because they dont have a failsafe mechanism, if such pod is deleted or gone nothing can be done to restore it back.

4. Deployment defines the desired state, ReplicaSet manages the number of pods based on the desired state, Pod has one or more containers which share storage or network, Container is a package of application, its dependencies, runtimes libraries etc.

5. The purpose of spec section is YAML is the set the ReplicaSet count, templete, name of the image for the create of deployement or pod or container etc.

6. When we scale from 1 to 3 replicas, Replicaset checks the number of pods running and since the current will be 1 it will create 2 more pods to meet the desired state of 3.

7. When we scale from 3 to 1 replicas, ReplicaSet check the number of pods running and since the current is 3 and 2 are additional pods for the required desired state, it will terminate the 2 addition pods.
