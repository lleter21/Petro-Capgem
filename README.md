<h2>Login to Azure</h2>

![image](https://github.com/user-attachments/assets/bbaaaaa5-314b-40dc-80d3-34def72e6265)

<h2></h2>

<h2>Practical Task 1: Introduction to Microsoft Entra ID</h2>
Create a basic Microsoft Entra ID setup for an organization to manage identity and access.</p>
<b>Requirements</b>:</p>
1. Create a new Microsoft Entra ID tenant.

![image](https://github.com/user-attachments/assets/7e9519bb-8faf-4add-847e-148019a65511)


2. Add at least two users to the directory.

A) Login to Az CLI:

![image](https://github.com/user-attachments/assets/f404af62-103d-4f81-b8d3-9b109a32661f)

Logged in succesfully:

![image](https://github.com/user-attachments/assets/12ac4bbf-cd23-4543-8ee7-4b66c73e37fe)

B) Create users:

![image](https://github.com/user-attachments/assets/5f046950-87b8-4bab-ba13-02cb1494a6fa)

adminUserPassword=$(openssl rand -base64 10) </p>
adminUserObject=$(az ad user create --display-name "Van Gogh" --password "$adminUserPassword" --user-principal-name "vang@tvpeter0521gmail.onmicrosoft.com" --mail-nickname "vang")
