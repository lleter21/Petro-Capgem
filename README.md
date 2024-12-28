<h2>Login to Azure</h2>

![image](https://github.com/user-attachments/assets/bbaaaaa5-314b-40dc-80d3-34def72e6265)

<h2></h2>

<h2>Practical Task 1: Introduction to Microsoft Entra ID</h2>
Create a basic Microsoft Entra ID setup for an organization to manage identity and access.</p>
<b>Requirements</b>:</p>
<h4>1. Create a new Microsoft Entra ID tenant.</h4>

![image](https://github.com/user-attachments/assets/7e9519bb-8faf-4add-847e-148019a65511)


<h4>2. Add at least two users to the directory.</h4>

<b>A) Login to Az CLI:</b>

![image](https://github.com/user-attachments/assets/f404af62-103d-4f81-b8d3-9b109a32661f)

Logged in succesfully:

![image](https://github.com/user-attachments/assets/12ac4bbf-cd23-4543-8ee7-4b66c73e37fe)

<b>B) Create users:</b>

<table>
  <tr>
    <th>Display Name</th>
    <th>Email</th>
    <th>Short Alias</th>
    <th>Group Name</th>
  </tr>
  <tr>
    <td>Van Gogh</td>
    <td>vang@tvpeter0521gmail.onmicrosoft.com</td>
    <td>vang</td>
    <td>Admin</td>
  </tr>
  <tr>
    <td>Michelangelo Buonarroti</td>
    <td>micb@tvpeter0521gmail.onmicrosoft.com</td>
    <td>micb</td>
    <td>Developer</td>
  </tr>
</table> 

![image](https://github.com/user-attachments/assets/5f046950-87b8-4bab-ba13-02cb1494a6fa)
![image](https://github.com/user-attachments/assets/b3281af0-55b1-4a7f-883a-e999a5712984)

Creating admin user:
<code>
adminUserPassword=$(openssl rand -base64 10)
adminUserObject=$(az ad user create --display-name "Van Gogh" --password "$adminUserPassword" --user-principal-name "vang@tvpeter0521gmail.onmicrosoft.com" --mail-nickname "vang")
</code>

Creating developer user:
<code>
developerUserPassword=$(openssl rand -base64 10)
developerUserObject=$(az ad user create --display-name "Michelangelo Buonarroti" --password "$developerUserPassword" --user-principal-name "micb@tvpeter0521gmail.onmicrosoft.com" --mail-nickname "micb")
</code>

<b>C) Validate users creation:</b>
![image](https://github.com/user-attachments/assets/f060cf25-4669-4b48-872b-ce85b8889595)
