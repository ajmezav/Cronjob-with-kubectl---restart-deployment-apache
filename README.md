## Dockerfile-apache-test


Este repo crea un Docker de un server apache de test, donde el objetivo es poder cambiar el index.php y un archivo de texto, para probar funcionalidades de actualización automática de pods sin necesidad de hacer alguna acción manual en un cluster de k8s, para esto el cluster de k8s debe tener un cronjob ref https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/ que reinicia el deployment que hace referencia a la imagen de Docker del apache-test cada minuto . El cronjob necesita unos permisos específicos para poder ejecutar los comandos de "kubectl" , por esto se crea un ServiceAccount y se le dan los accesos necesarios por RBAC para que pueda reiniciar un deployment. El deployment tiene la propiedad         imagePullPolicy: Always ref: https://kubernetes.io/docs/concepts/containers/images/  , de esta forma siempre buscará descargar la última versión de la imagen de Docker y no se tendrá que manejar un versionamiento en el deployment , ya que siempre se usa el tag de:latest

#### Riesgos ...

* Sí falla el cronjob o falla la actualización del deployment no hay forma de devolverse (claro que haciendo intervención manual sí podría actuar) , toca esperar hasta x tiempo que se actualice de nuevo la imagen de Docker y tenga una versión corregida.

* Los permisos del cronJob, se pueden ver como una "brecha de seguridad" por lo que toca ser muy cuidadoso

* No hay forma de visualizar cómo se está comportando cada cluster , es decir no hay forma de tener control de los despliegues (se podría crear algún script o algo que reporte en algún lado qué la actualización automática fué un éxito)

* En teoría nada debería fallar sí ya se probó en un ambiente controlado , pero una configuración adicional no prevista en un cluster, puede afectar el CronJob o el despliegue automatico del pod


#### Create Image

docker build -t ajmezav/webserver:latest .

#### Push iamge

docker push ajmezav/webserver:latest

#### Run Docker

docker container run -d -p 80:80 ajmezav/webserver:latest

#### Entrar por bash en un Docker

docker exec -it CONTAINER_ID /bin/bash

#### Stop and delete docker

docker container rm CONTAINER_ID -f

#### Delete Iamge 

docker rmi -f ajmezav/webserver:latest

#### Deployment

kubectl apply -f deployment_apache.yaml

kubectl apply -f cronjob.yaml

#### port-forward

kubectl port-forward svc/webserver 8080:80 (puerto del localhost: puerto origen del docker)

#### Entrar al bash de un pod

kubectl exec --stdin --tty POD_NAME -- /bin/bash

#### Ver estado del CronJob

kubectl get cronjob

kubectl describe cronjob NAME_CRONJOB
