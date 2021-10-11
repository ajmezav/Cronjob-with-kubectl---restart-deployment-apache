## Dockerfile-apache-test

Este repo crea un Docker de un server apache de test, donde yo puedo cambiar el index.php y un archivo de texto para probar funcionalidades de actualizacion automatica de pods sin necesidad de hacer alguna acción en mi cluster de k8s, para esto el cluster de k8s debe tener un cronjob que reinicia el deployment que hace referencia a la imagen de Docker del apache-test cada minuto . El cronjob necesita unos permisos especificos para poder ejecutar los comandos de "kubectl" , por esto se crea un ServiceAccount y se le dan los accesos necesarios por RBAC para que pueda reiniciar un deployment. El deployment tiene la propiedad         imagePullPolicy: Always , de esta forma siempre buscará descargar la ultima version de la imagen de Doccker y no se tendra que manejar un versionamiento en el deploymente , ya que siempre uso el tag de:latest

##### Riesgos ...

* Sí falla no hay forma de devolverse (claro que haciendo intervención manual sí podria actuar) , toca esperar hasta x tiempo para actualizar reglas.

* Los permisos del cronJob, se pueden ver como una "brecha de seguridad" por lo que toca ser muy cuidadoso

* No tengo forma de visualizar como se esta comportando cada cluster , es decir no tengo forma de tener control de mis despliegues (podría crear algun script o algo que reporte en algun lado que la actualización automatica fué un éxito)

* En teoria nada deberia fallar sí ya se probó en un ambiente controlado , pero una configuración adicional en un cluster puede poner a fallar mis despliegues

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
