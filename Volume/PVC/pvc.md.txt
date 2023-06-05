Note: In Kubernetes (K8s), PVC stands for Persistent Volume Claim. PVC is an abstraction layer that allows users to request and use storage resources in a cluster without having to know the underlying details of the storage infrastructure. PVCs are used to dynamically provision and manage the lifecycle of storage volumes for applications running in Kubernetes.

Here are some key points to understand about PVCs in Kubernetes:

Requesting Storage: When a pod needs persistent storage, it creates a PVC object to request the desired amount and type of storage. The PVC specifies the capacity, access mode, and storage class for the requested storage.

Capacity: The capacity field in the PVC specifies the amount of storage required, such as "10Gi" for 10 gigabytes.

Access Modes: PVCs define the access modes needed for the underlying storage. The access modes determine how the storage can be accessed by pods. The three common access modes are:

ReadWriteOnce (RWO): The volume can be mounted as read-write by a single pod.
ReadOnlyMany (ROX): The volume can be mounted as read-only by multiple pods.
ReadWriteMany (RWX): The volume can be mounted as read-write by multiple pods.
The actual support for access modes depends on the underlying storage provider.

Storage Classes: Storage classes are used to define different types or tiers of storage available in the cluster. Each storage class represents a specific provisioner or storage backend and defines the characteristics and capabilities of the storage. PVCs can request a specific storage class to get the desired type of storage.

Dynamic Provisioning: PVCs enable dynamic provisioning of storage. When a PVC is created, Kubernetes will check if a matching Persistent Volume (PV) is available. If not, it will dynamically provision a new PV based on the defined storage class.

Binding and Usage: Once a PVC is created and a PV is bound to it, the PVC can be mounted as a volume in pods. The pod can refer to the PVC by its name, and Kubernetes ensures that the requested storage is available and mounted to the pod as specified.

Reclaim Policies: PVCs have a reclaim policy that determines what happens to the underlying PV when the PVC is deleted. The three reclaim policies are:

Retain: The PV is not deleted automatically, allowing manual reclamation.
Delete: The PV is deleted automatically when the PVC is deleted.
Recycle (Deprecated): The PV is formatted and made available for reuse when the PVC is deleted.
The actual behavior depends on the underlying storage provider and configuration.

PVCs provide an abstraction layer that simplifies the management of persistent storage in Kubernetes. They decouple the application's storage needs from the underlying storage infrastructure, making it easier to provision, use, and manage storage resources in a Kubernetes cluster.  

The application stores logs at location /log/app.log. View the logs.

You can exec in to the container and open the file:
kubectl exec webapp -- cat /log/app.log  


Excercises:

1. Configure a volume to store these logs at /var/log/webapp on the host.


Use the spec provided below.
Name: webapp
Image Name: kodekloud/event-simulator
Volume HostPath: /var/log/webapp
Volume Mount: /log
HInt: Use the command kubectl get po webapp -o yaml > webapp.yaml and add the given properties under the spec.volumes and spec.containers.volumeMounts.
OR
Use the command kubectl run to create a new pod and use the flag --dry-run=client -o yaml to generate the manifest file.

In the manifest file add spec.volumes and spec.containers.volumeMounts property.



After that, run the following command to create a pod called webapp: -

kubectl replace -f webapp.yaml --force


kubectl replace -f: - It will remove the existing resource and will replace it with the new one from the given manifest file.  

Solution:  Create manifest file `webapp-vol.yaml` and apply it to edit 

2. Create a Persistent Volume with the given specification.
Volume Name: pv-log
Storage: 100Mi
Access Modes: ReadWriteMany
Host Path: /pv/log
Reclaim Policy: Retain

solution: create manifest file `pv.yaml` and create


3. Let us claim some of that storage for our application. Create a Persistent Volume Claim with the given specification.  
Volume Name: claim-log-1
Storage Request: 50Mi
Access Modes: ReadWriteOnce  

Solution: create manifest file `claim-log.yaml` and apply  

4. What is the state of the Persistent Volume Claim?  
Run the command: kubectl get pvc and look under the status section.  

5. What is the state of the Persistent Volume?  
Run the command: kubectl get pv and look under the status section.  

6. Why is the claim not bound to the available Persistent Volume?  
Run the command: kubectl get pv,pvc and look under the Access Modes section.
The Access Modes set on the PV and the PVC do not match.  

6. Update the Access Mode on the claim to bind it to the PV.
Delete and recreate the claim-log-1.  
Volume Name: claim-log-1
Storage Request: 50Mi
PVol: pv-log
Status: Bound


solution: 
To delete the existing pvc:

$ kubectl delete pvc claim-log-1 
Solution manifest file to create a `updated-claim.yaml` PVC with correct Access Modes  
Then run kubectl create -f <file-name>.yaml  

7. You requested for 50Mi, how much capacity is now available to the PVC?  
Run the command: kubectl get pvc and look under the capacity section.  

8. Update the webapp pod to use the persistent volume claim as its storage.  
Replace hostPath configured earlier with the newly created PersistentVolumeClaim.
Name: webapp
Image Name: kodekloud/event-simulator
Volume: PersistentVolumeClaim=claim-log-1
Volume Mount: /log 

solution:  
To delete the webapp pod first:

$ kubectl delete po webapp
Add --force flag in above command, if you would like to delete the pod without any delay.  
To create a new webapp pod named 'updated-webapp.yaml` with given properties  
Then run the command kubectl create -f <file-name>.yaml to create a pod from the definition fil  

9. What is the Reclaim Policy set on the Persistent Volume pv-log?   
Run the command: kubectl get pv and look under the Reclaim Policy column.  

10. What would happen to the PV if the PVC was destroyed?  

Note: 
The behavior of a Persistent Volume (PV) when the associated Persistent Volume Claim (PVC) is deleted depends on the reclaim policy set for the PV. The reclaim policy determines how the PV's resources are handled after the PVC is removed. There are three reclaim policies available:

Retain: With the "Retain" reclaim policy, the PV is not automatically deleted when the PVC is removed. The PV and its data remain intact, allowing for manual reclamation or recovery. This policy is useful when you want to retain the data for future use or need to perform manual cleanup tasks before releasing the PV.

Delete: The "Delete" reclaim policy indicates that the PV should be automatically deleted when the PVC is removed. In this case, the storage resources associated with the PV, including any data stored within it, are released and potentially deleted. This policy is suitable for scenarios where the data is disposable and not needed once the PVC is deleted.

Recycle (Deprecated): The "Recycle" reclaim policy is deprecated and no longer recommended for use. It was intended to perform basic cleanup tasks, such as deleting the contents of the PV, when the PVC is deleted. However, it was not widely supported and has been replaced by more advanced storage solutions.  


11. Why is the PVC stuck in Terminating state?  
The PVC was still being used by the webapp pod when we issued the delete command. Until the pod is deleted, the PVC will remain in a terminating state.  

12. Let us now delete the webapp Pod.  
Once deleted, wait for the pod to fully terminate.
Name: webapp  

To delete the pod without any delay and confirmation, run the command kubectl delete pod webapp --force


