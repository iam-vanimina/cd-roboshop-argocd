roboshop rabbitmq argocd deployment:
-----------------------------------

Please clone below repository rabbitmq-argocd 

`https://github.com/iam-vanimina/rabbitmq-argocd.git `



`cd  /rabbitmq-argocd`

Make changes helm values as per your tags or version or image url etc ..

Make changes in application.yaml (mention your github repo url and create k8s roboshop namespace )

`kubectl apply -f application.yaml  `

In my case my github repo is 

`https://github.com/iam-vanimina/rabbitmq-argocd.git `

github repo act as the truth for the argocd.
