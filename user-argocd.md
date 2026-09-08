
roboshop user argocd deployment:
-----------------------------------

Please clone below repository user-argocd 

`https://github.com/iam-vanimina/user-argocd.git `



`cd  /user-argocd`

Make changes helm values as per your tags or version or image url etc ..

Make changes in application.yaml (mention your github repo url and create k8s roboshop namespace )

`kubectl apply -f application.yaml  `

In my case my github repo is 

`https://github.com/iam-vanimina/user-argocd.git `

github repo act as the truth for the argocd.
