
roboshop frontend argocd deployment:
-----------------------------------

Please clone below repository frontend-argocd 

`https://github.com/iam-vanimina/frontend-argocd.git `



`cd  /frontend-argocd`

Make changes helm values as per your tags or version or image url etc ..

Make changes in application.yaml (mention your github repo url and create k8s roboshop namespace )

`kubectl apply -f application.yaml  `

In my case my github repo is 

`https://github.com/iam-vanimina/frontend-argocd.git `

github repo act as the truth for the argocd.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Initially we have created docker desktop Kubernetes with worker nodes.

<img width="938" height="485" alt="image" src="https://github.com/user-attachments/assets/f89a4a95-eeca-40db-8f9d-33c2501652e7" />


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------


`kubectl get nodes `



<img width="420" height="75" alt="image" src="https://github.com/user-attachments/assets/b2015c93-baa7-420c-bd5b-262f53142a94" />

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
view the information of nodes 

`kubectl get node -o wide `

<img width="951" height="107" alt="image" src="https://github.com/user-attachments/assets/06c5cf1d-790a-4f5e-a89f-1deddd4aa0ca" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

view the information of all pods in roboshop namespace

 `kubectl get pods -n roboshop`

 <img width="387" height="212" alt="image" src="https://github.com/user-attachments/assets/3dfdd951-97c7-4730-9807-657b280a28d2" />


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

view the information of all services in roboshop namespace

`kubectl get svc -n roboshop`

<img width="451" height="158" alt="image" src="https://github.com/user-attachments/assets/79ec2109-17e3-4f6a-9675-f8ecc9525986" />


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------




-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------





