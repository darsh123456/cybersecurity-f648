Scaling [cybersecurity-f648] With Kubernetes
===========================

Generated On: 2024-11-26 19:01:44 UTC

You can scale your solution with Kubernetes.  To do so, will will need to apply the following YAML files to your Kubernetes cluster.

.. tip::
   Refer to TML documentation for more information on `scaling with Kubernetes <https://tml.readthedocs.io/en/latest/kube.html>`_.

   You can also run the YAML files locally in a 1 node kubernetes cluster called minikube.  Refer to `Installing minikube <https://tml.readthedocs.io/en/latest/kube.html#installing-minikube>`_

.. important:: 
   Below assumes you have a Kubernetes cluster and **kubectl** installed in your Linux environment.

Based on your TML solution [cybersecurity-f648] - if you want to scale your application with Kubernetes - you will need to apply the following YAML files.

.. list-table::

   * - **YML File**
     - **Description**
   * - :ref:`cybersecurity-f648.yml`
     - This is your main solution YAML file.  
 
       It MUST be applied to your Kubernetes cluster.
   * - :ref:`mysql-storage.yml`
     - This is storage allocation for MySQL DB.
 
       It MUST be applied to your Kubernetes cluster.
   * - :ref:`mysql-db-deployment.yml`
     - This is the MySQL deployment YAML.
 
       It MUST be applied to your Kubernetes cluster.
   * - :ref:`privategpt.yml`
     - This is the privateGPT deployment YAML.
 
       This is OPTIONAL.  However, it must be 
 
       applied if using Step 9 DAG.
   * - :ref:`qdrant.yml`
     - This is the Qdrant deployment YAML.
 
       This is OPTIONAL.  However, it must be 
 
       applied if using Step 9 DAG.

kubectl apply command
-----------------

.. important::
   To apply the YAML files below to your Kubernetes cluster simply run this command:

.. code-block:: YAML

   kubectl apply -f mysql-storage.yml -f mysql-db-deployment.yml -f qdrant.yml -f privategpt.yml -f cybersecurity-f648.yml

cybersecurity-f648.yml
------------------------

.. important::
   Copy and Paste this YAML file: cybersecurity-f648.yml - and save it locally.

   Also, MAKE SURE to update any tokens and passwords in this file.

.. code-block:: YAML

   ################# cybersecurity-f648.yml
   
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: cybersecurity-f648
     spec:
       selector:
         matchLabels:
           app: cybersecurity-f648
       replicas: 3 # tells deployment to run 1 pods matching the template
       template:
         metadata:
           labels:
             app: cybersecurity-f648
         spec:
           containers:
           - name: cybersecurity-f648
             image: djmodessa1726/cybersecurity-f648-amd64
             volumeMounts:
             - name: dockerpath
               mountPath: /var/run/docker.sock
             ports:
             - containerPort: 8883
             - containerPort: 37317
             - containerPort: 48035
             - containerPort: 58955
             env:
             - name: TSS
               value: '0'
             - name: SOLUTIONNAME
               value: 'cybersecurity-f648'
             - name: SOLUTIONDAG
               value: 'solution_preprocessing_ml_ai_mqtt_dag-cybersecurity-f648'
             - name: GITUSERNAME
               value: 'darsh123456'
             - name: GITREPOURL
               value: 'https://github.com/darsh123456/raspberrypi.git'
             - name: SOLUTIONEXTERNALPORT
               value: '58955'
             - name: CHIP
               value: 'amd64'
             - name: SOLUTIONAIRFLOWPORT
               value: '37317'
             - name: SOLUTIONVIPERVIZPORT
               value: '48035'
             - name: DOCKERUSERNAME
               value: 'djmodessa1726'
             - name: CLIENTPORT
               value: '8883'
             - name: EXTERNALPORT
               value: '38533'
             - name: KAFKACLOUDUSERNAME
               value: ''
             - name: VIPERVIZPORT
               value: '9005'
             - name: MQTTUSERNAME
               value: 'djmodessa'
             - name: AIRFLOWPORT
               value: '9000'
             - name: GITPASSWORD
               value: '<ENTER GITHUB PASSWORD>'
             - name: KAFKACLOUDPASSWORD
               value: '<Enter API secret>'
             - name: MQTTPASSWORD
               value: '<ENTER MQTT PASSWORD>'
             - name: READTHEDOCS
               value: '<ENTER READTHEDOCS TOKEN>'
             - name: qip 
               value: 'localhost' # This is private GPT IP              
             - name: KUBE
               value: '1'
           volumes: 
           - name: dockerpath
             hostPath:
               path: /var/run/docker.sock
           dnsPolicy: "None"
           dnsConfig:
             nameservers:
               - 8.8.8.8                
               
   ---
     apiVersion: v1
     kind: Service
     metadata:
       name: cybersecurity-f648-service
       labels:
         app: cybersecurity-f648-service
     spec:
       type: NodePort #Exposes the service as a node ports
       ports:
       - port: 8883
         name: p1
         protocol: TCP
         targetPort: 8883
       - port: 37317
         name: p2
         protocol: TCP
         targetPort: 37317
       - port: 48035
         name: p3
         protocol: TCP
         targetPort: 48035
       - port: 58955
         name: p4
         protocol: TCP
         targetPort: 58955
       selector:
         app: cybersecurity-f648

mysql-storage.yml
------------------------

.. important::
   Copy and Paste this YAML file: mysql-storage.yml - and save it locally.

.. code-block:: YAML

      ################# mysql-storage.yml
      apiVersion: v1
      kind: PersistentVolume
      metadata:
        name: mysql-pv-volume
        labels:
          type: local
      spec:
        storageClassName: manual
        capacity:
          storage: 20Gi
        accessModes:
          - ReadWriteOnce
        hostPath:
          path: "/mnt/data"
      ---
      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: mysql-pv-claim
      spec:
        storageClassName: manual
        accessModes:
          - ReadWriteOnce
        resources:
          requests:
            storage: 20Gi

mysql-db-deployment.yml
------------------------

.. important::
   Copy and Paste this YAML file: mysql-db-deployment.yml - and save it locally.

.. code-block:: YAML

      ################# mysql-db-deployment.yml
      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: mysql
      spec:
        selector:
          matchLabels:
            app: mysql
        strategy:
          type: Recreate
        template:
          metadata:
            labels:
              app: mysql
          spec:
            containers:
            - image: maadsdocker/mysql:latest
              name: mysql
              env:
              - name: MYSQL_ROOT_PASSWORD
                value: "raspberry"
              - name: MYSQLDB
                value: "tmlids"
              - name: MYSQLDRIVERNAME
                value: "mysql"
              - name: MYSQLHOSTNAME
                value: "mysql:3306"
              - name: MYSQLMAXCONN
                value: "4"
              - name: MYSQLMAXIDLE
                value: "10"
              - name: MYSQLPASS
                value: "raspberry"
              - name: MYSQLUSER
                value: "root"                  
              ports:
              - containerPort: 3306
                name: mysql
              volumeMounts:
              - name: mysql-persistent-storage
                mountPath: /var/lib/mysql
            volumes:
            - name: mysql-persistent-storage
              persistentVolumeClaim:
                claimName: mysql-pv-claim
      
      ---
      apiVersion: v1
      kind: Service
      metadata:
        name: mysql-service
      spec:
        ports:
        - port: 3306
        selector:
          app: mysql

privategpt.yml
---------------

.. note::
    This YAML is Optional - Use Only If Step 9 Dag is used

.. important::
   Copy and Paste this YAML file: privategpt.yml - and save it locally.

.. note::
   By default this assumes you have a Nvidia GPU in your machine and so it using the Nvidia privateGPT container:

    **image: maadsdocker/tml-privategpt-with-gpu-nvidia-amd64**

   if you DO NOT have a Nvidia GPU installed then change image to:

    **image: maadsdocker/tml-privategpt-no-gpu-amd64**

.. code-block:: YAML

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: privategpt
      spec:
        selector:
          matchLabels:
            app: privategpt
        replicas: 1 # tells deployment to run 1 pods matching the template
        template:
          metadata:
            labels:
              app: privategpt
          spec:
            #hostNetwork: true
            containers:
            - name: privategpt
              image: maadsdocker/tml-privategpt-with-gpu-nvidia-amd64 # IF you DO NOT have NVIDIA GPU use: maadsdocker/tml-privategpt-no-gpu-amd64
              volumeMounts:
              - name: dockerpath
                mountPath: /var/run/docker.sock
              ports:   
              - containerPort: 8001
              env:
              - name: NVIDIA_VISIBLE_DEVICES 
                value: all
              - name: DP_DISABLE_HEALTHCHECKS
                value: xids
              - name: WEB_CONCURRENCY
                value: "1"
              - name: GPU
                value: "1"          
              - name: COLLECTION
                value: "tml"  
              - name: PORT
                value: "8001"  
              - name: CUDA_VISIBLE_DEVICES
                value: "0"  
              - name: TSS
                value: "0"  
            volumes:
            - name: dockerpath
              hostPath:
                path: /var/run/docker.sock
            dnsPolicy: "None"
            dnsConfig:
              nameservers:
                - 8.8.8.8      
         
      ---
      apiVersion: v1
      kind: Service
      metadata:
        name: privategpt-service
        labels:
          app: privategpt-service
      spec:
        type: NodePort #Exposes the service as a node ports
        ports:
        - port: 8001
          name: p1
          protocol: TCP
          targetPort: 8001
        selector:
          app: privategpt
          
          
qdrant.yml
---------------

.. note::
    This YAML is Optional - Use Only If Step 9 Dag is used

.. important::
   Copy and Paste this YAML file: qdrant.yml - and save it locally.

.. code-block:: YAML

      ################# qdrant.yml
      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: qdrant
      spec:
        selector:
          matchLabels:
            app: qdrant
        replicas: 1 
        template:
          metadata:
            labels:
              app: qdrant
          spec:
            #hostNetwork: true
            containers:
            - name: qdrant
              image: qdrant/qdrant 
              ports:   
              - containerPort: 6333
              volumeMounts:
              - mountPath: /qdrant/storage
                name: qdata
            volumes:
            - name: qdata
              hostPath:
                path: /qdrant_storage          
      ---
      apiVersion: v1
      kind: Service
      metadata:
        name: qdrant-service
        labels:
          app: qdrant-service
      spec:
        type: NodePort #Exposes the service as a node ports
        ports:
        - port: 6333
          name: p1
          protocol: TCP
          targetPort: 6333
        selector:
          app: qdrant
          
.. tip::
   The number of replicas can be changed in the **cybersecuritywithprivategpt-3f10.yml** file: look for **replicas**.  You can increase or decrease the number of replicas based on the amout of real-time data you are processing.

   To inside the pods, you can type command: 

    COMMAND: **kubectl exec -it <pod name> \-\- bash** (replace <pod name> with actual pod name)

   To delete the pods type:

    COMMAND: **kubectl delete all \-\-all \-\-all-namespaces**

   To get information on a pod type:

    COMMAND: **kubectl describe pod <pod name>** (replace <pod name> with actual pod name)

   Start minikube with GPU:
     COMMAND: **minikube start –driver docker \-\-container-runtime docker \-\-gpus all**

   Start minikube with NO GPU:
     COMMAND: **minikube start –driver docker**
