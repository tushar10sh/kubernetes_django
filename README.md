# kubernetes_django

Step by Step guide to deploy Django application in Kubernetes with Postgres DB (as StatefulSet), Redis as Cache Backend and Celery as worker backend.
Flower is also deployed for celery workload visualizations.

<u>1. Build django docker image </u>
  a. Apart from django server deployment, other deployments such as redis, celery and django migrations jobs will require a container image that doesn't have 
     a <i>CMD python manage.py runserver 0.0.0.0:8000</i>. 
  b. To build this image, make sure this line is commented and build a container image 
     <i> docker build -t local/kube_django_testapp:pre . </i>
  c. Now for django server deployment build another image with this line not commented so that server is started as soon as container is up.
     <i> docker build -t local/kube_django_testapp:part2 . </i>
  d. Transfer these images to your worker nodes either via a local private docker registry setup or manuall saving this image as TAR 
     and loading into each node via
     <i> docker save -o image-pre.tar local/kube_django_testapp:pre </i>
     <i> docker save -o image-part2.tar local/kube_django_testapp:part2 </i>
     <i> docker load --input image-pre.tar </i>
     <i> docker load --input image-part2.tar </i>
<u>2. Start Postgres</u>
  a. Create postgres secret
     <i> kubectl create -f deploy/kubernetes/postgres/secrets.yaml </i>
  b. Create PV
     <i> kubectl create -f deploy/kubernetes/postgres/volume.yaml </i>
  c. Create postgres statefulset
     <i> kubectl create -f deploy/kubernetes/postgres/deployment.yaml </i>
  d. Expose postgres as service 
     <i> kubectl create -f deploy/kubernetes/postgres/service.yaml </i>

<u>3. Start Redis</u>
  a. Create redis deployment
     <i> kubectl create -f deploy/kubernetes/redis/deployment.yaml </i>
  b. Expose redis as service
     <i> kubectl create -f deploy/kubernetes/redis/service.yaml </i>

<u>4. Django app deployments</u>
  a. Create django deployments 
     <i> kubectl create -f deploy/kubernetes/django/deployment.yaml </i>
  b. Expose django as service 
     <i> kubectl create -f deploy/kubernetes/django/service.yaml </i>
 
<u>5. Start Django Migrations </u>
  <i> kubectl create -f deploy/kubernetes/django/job-migration.yaml </i>
  
<u>6. Start celery </u>
  <i> kubectl create -f deploy/kubernetes/celery/worker-deployment.yaml </i>
<u>7. Start flower </u>
  <i> kubectl create -f deploy/kubernetes/flower/deployment.yaml </i>
  <i> kubectl create -f deploy/kubernetes/flower/service.yaml </i>
