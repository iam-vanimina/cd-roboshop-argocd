# cd-roboshop-argocd
cd-roboshop-argocd ----->  github repo act as truth for the argocd deployment.

Micro services in roboshop:
==========================
frontend:
=========
frontend --- JavaScript ---- runtime environment --- nodejs

backend:
=======

user   -------  JavaScript ---- runtime environment --- nodejs

catalogue ----  JavaScript ---- runtime environment --- nodejs

cart    ------  JavaScript ---- runtime environment --- nodejs

shipping -----  Java   ---- runtime environment --- JRE

payments -----  Python

dispatch -----  Go

  
databases:
=========
redis

mangodb

mysql

rabbitmq

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------




---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Final output after deploying to argocd in k8s (docker desktop)**

roboshop user interface and click on resgister tab

<img width="949" height="467" alt="image" src="https://github.com/user-attachments/assets/7ea5478c-daec-484f-814d-53dce8b9fdb8" />

then user register successfully as below 




<img width="911" height="425" alt="image" src="https://github.com/user-attachments/assets/43415299-323e-4ed3-a6d3-ed6c3f76518a" />


click on categories and select specific robot to order and click on add cart

<img width="936" height="468" alt="image" src="https://github.com/user-attachments/assets/19e50392-6b7e-4c94-aa70-183994b89620" />

then after click on cart tab and click on checkout button

<img width="939" height="476" alt="image" src="https://github.com/user-attachments/assets/79bc3888-9f76-4f96-ba90-4bf0e7b48f92" />

then after it takes to shipping add country and location as below and click on calculate button

<img width="939" height="476" alt="image" src="https://github.com/user-attachments/assets/80e5f1ba-320a-4298-bc7d-d7b2dd9d576e" />

after calculate you can see distance and total cost and after that click on confirm

<img width="938" height="471" alt="image" src="https://github.com/user-attachments/assets/6938555e-2b33-492d-906f-3ae112dd4b24" />

after confirm you can see review oders in payment as below and click on pay now button

<img width="928" height="472" alt="image" src="https://github.com/user-attachments/assets/1f9d0897-8851-4ae6-86a7-d86da41b5035" />


Finally order is placed and customer data orders and shipping information  had been dispatched

<img width="938" height="479" alt="image" src="https://github.com/user-attachments/assets/193fdfd6-fbf4-4b0f-b0d4-6d9b2da826b2" />



Order placed b0af2506-f8c6-44ed-90d6-8dcb156177a2

Thank you for your order Continue shopping


click on login tab and verify order history

<img width="913" height="470" alt="image" src="https://github.com/user-attachments/assets/440ac287-84f2-4838-9e05-4f34961d9f11" />




I hope it helpful to learning microservices and database how they are working in a flow. 

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Author: Venkata Ram Vanimina 

