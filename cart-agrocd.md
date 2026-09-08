roboshop cart argocd deployment:
-----------------------------------

Please clone below repository cart-argocd 

`https://github.com/iam-vanimina/cart-argocd.git `



`cd  /cart-argocd`

Make changes helm values as per your tags or version or image url etc ..

Make changes in application.yaml (mention your github repo url and create k8s roboshop namespace )

`kubectl apply -f application.yaml  `

In my case my github repo is 

`https://github.com/iam-vanimina/cart-argocd.git `

github repo act as the truth for the argocd.
