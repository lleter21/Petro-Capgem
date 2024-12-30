<h2>Login to Azure</h2>

![image](https://github.com/user-attachments/assets/bbaaaaa5-314b-40dc-80d3-34def72e6265)

<h2></h2>

<h2>Practical Task 1: Introduction to Microsoft Entra ID</h2>
Create a basic Microsoft Entra ID setup for an organization to manage identity and access.</p>
<b>Requirements</b>:</p>
<h4>1. Create a new Microsoft Entra ID tenant.</h4>

![image](https://github.com/user-attachments/assets/7e9519bb-8faf-4add-847e-148019a65511)


<h4>2. Add at least two users to the directory.</h4>

<ins>A) Login to Az CLI:</ins>

![image](https://github.com/user-attachments/assets/f404af62-103d-4f81-b8d3-9b109a32661f)

Logged in succesfully:

![image](https://github.com/user-attachments/assets/12ac4bbf-cd23-4543-8ee7-4b66c73e37fe)

<ins>B) Create users:</ins>

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

<ins>C) Validate users creation:</ins>
![image](https://github.com/user-attachments/assets/f060cf25-4669-4b48-872b-ce85b8889595)


<h4>3. Create two groups named Developers and Admins.</h4>

<ins>A) Create group: Developers</ins>

![image](https://github.com/user-attachments/assets/b493338b-4a47-4a7e-9226-73fd3a447c62)

<ins>A) Create group: Admins</ins>

![image](https://github.com/user-attachments/assets/d5ec2ae9-563f-4efe-992c-de469c163f1b)

<h4>4. Assign the users to appropriate groups.</h4>

<ins>A) Get User IDs:</ins>

<code>
developerUserObjectId=$(echo "$developerUserObject" | jq .id | tr -d '"')
adminUserObjectId=$(echo "$adminUserObject" | jq .id | tr -d '"')
</code>

![image](https://github.com/user-attachments/assets/cca93c08-5cb7-4e53-a513-222377ebcb6d)

<ins>B) Get groups ID:</ins>

![image](https://github.com/user-attachments/assets/0ca80329-fc32-4cf8-baec-70ea84a7215c)

<code>
adGroups=$(az ad group list | jq '.[]')
developerGrpId=$(echo "$adGroups" | jq 'select(.displayName=="Developers")' | jq '.id' | tr -d '"')
adminGrpId=$(echo "$adGroups" | jq 'select(.displayName=="Admins")' | jq '.id' | tr -d '"')
</code>

<ins>C) Assign the users to appropriate groups:</ins>

![image](https://github.com/user-attachments/assets/876afc71-bf7d-47e2-9cc6-426d43a377a1)

<code>
az ad group member add --group "$developerGrpId" --member-id "$developerUserObjectId"
az ad group member add --group "$adminGrpId" --member-id "$adminUserObjectId"
</code>

<ins>D) Verify users in groups:</ins>
![image](https://github.com/user-attachments/assets/33c94913-fbab-4a63-b474-0f461c974247)

![image](https://github.com/user-attachments/assets/9de9c2bf-d7a2-42f4-9c4f-490fc42fcb34)


<h4>5. Assign the Global Reader role to the Admins group.</h4>

<ins>A) Get Global Reader role Id:</ins>

Global Reader: f2ef992c-3afb-46b9-b7cf-a126ee74c451 
![image](https://github.com/user-attachments/assets/86531cca-57cf-434e-9226-054f938e4ac7)

<ins>B) Assign Global Reader role to Admins group:</ins>

![image](https://github.com/user-attachments/assets/da4dee07-dff9-408f-99cd-c952b5590bbd)

<code>
Body="{'principalId':'{$adminGrpId}', 'roleDefinitionId': 'f2ef992c-3afb-46b9-b7cf-a126ee74c451', 'directoryScopeId': '/'}"
az rest --method POST --uri 'https://graph.microsoft.com/v1.0/roleManagement/directory/roleAssignments' --headers "Content-Type=application/json" --body "$Body"
</code>

<h4>6. Assign the Application Developerrole to the Developers group.</h4>

<ins>A) Get Application Developer role Id:</ins>

Application Developer: cf1c38e5-3621-4004-a7cb-879624dced7c
![image](https://github.com/user-attachments/assets/cb4e529b-a104-4101-a964-b8a623ec153e)

<ins>B) Assign Application Developer role to Developer group:</ins>

![image](https://github.com/user-attachments/assets/c635a197-7c91-4166-85e7-0a4cb15ee53e)


<code>
Body="{'principalId':'{$developerGrpId}', 'roleDefinitionId': 'cf1c38e5-3621-4004-a7cb-879624dced7c', 'directoryScopeId': '/'}"
az rest --method POST --uri 'https://graph.microsoft.com/v1.0/roleManagement/directory/roleAssignments' --headers "Content-Type=application/json" --body "$Body"
</code>
