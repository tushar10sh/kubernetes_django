# kubernetes_django

Step by Step guide to deploy Django application in Kubernetes with Postgres DB (as StatefulSet), Redis as Cache Backend and Celery as worker backend.
Flower is also deployed for celery workload visualizations.

<b>1. Build django docker image </b>
  <br>
  a. Apart from django server deployment, other deployments such as redis, celery and django migrations jobs will require a container image that doesn't have 
  <br>
     <code>CMD python manage.py runserver 0.0.0.0:8000</code>. 
  <br>
  in Dockerfile.
  <br>
  b. To build this image, make sure this line is commented and build a container image 
  <br>
     <code> docker build -t local/kube_django_testapp:pre . </code>
  <br>
  c. Now for django server deployment build another image with this line not commented so that server is started as soon as container is up.
     <code> docker build -t local/kube_django_testapp:part2 . </code>
  <br>
  d. Transfer these images to your worker nodes either via a local private docker registry setup or manuall saving this image as TAR 
     and loading into each node via
     
     docker save -o image-pre.tar local/kube_django_testapp:pre
     
     docker save -o image-part2.tar local/kube_django_testapp:part2
     
     docker load --input image-pre.tar
     
     docker load --input image-part2.tar
     
     
<b>2. Start Postgres</b>


  a. Create postgres secret
  
     kubectl create -f deploy/kubernetes/postgres/secrets.yaml
     
  b. Create PV
  
     kubectl create -f deploy/kubernetes/postgres/volume.yaml
     
  c. Create postgres statefulset
  
     kubectl create -f deploy/kubernetes/postgres/deployment.yaml
     
  d. Expose postgres as service 
  
     kubectl create -f deploy/kubernetes/postgres/service.yaml

<u>3. Start Redis</u>

  a. Create redis deployment
  
     kubectl create -f deploy/kubernetes/redis/deployment.yaml
     
  b. Expose redis as service
  
     kubectl create -f deploy/kubernetes/redis/service.yaml
     

<u>4. Django app deployments</u>

  a. Create django deployments 
  
     kubectl create -f deploy/kubernetes/django/deployment.yaml
     
  b. Expose django as service 
  
     kubectl create -f deploy/kubernetes/django/service.yaml
     
 
<u>5. Start Django Migrations </u>

  <code> kubectl create -f deploy/kubernetes/django/job-migration.yaml </code>
  
  
<u>6. Start celery </u>

  <code> kubectl create -f deploy/kubernetes/celery/worker-deployment.yaml </code>
  
<u>7. Start flower </u>

  <code> kubectl create -f deploy/kubernetes/flower/deployment.yaml </code>
  
  <code> kubectl create -f deploy/kubernetes/flower/service.yaml </code>
  
