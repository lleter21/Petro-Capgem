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

<h4>7. Verify that the role assignments function as expected for both groups.</h4>

<ins>A) Verification for development group:</ins>

![image](https://github.com/user-attachments/assets/2361fafa-2822-4b0f-a356-4a75b0cff211)

<ins>B) Verification for admin group:</ins>

![image](https://github.com/user-attachments/assets/542bfd84-e5ae-41d6-b339-ea87406af600)


<h2>Practical Task 3: Implementing Role-Based Access Control (RBAC)</h2>
Implement Role-Based Access Control (RBAC) in Azure to manage access to resources based on roles and
ensure fine-grained access management.</p>
<b>Requirements</b>:</p>
<h4>1. Create a custom role named Resource Viewer with read-only permissions for a specific resource
group.</h4>

<ins>A) Create Custom role file for specific resource group:</ins>
![image](https://github.com/user-attachments/assets/71d70b92-9cda-49bc-b1cf-1233331af3be)

<ins>B) Create resource group pett-test:</ins>

![image](https://github.com/user-attachments/assets/4314412c-9331-4770-8df1-8a5f15e4ef9f)

<ins>C) Create Custom role in Azure:</ins>
![image](https://github.com/user-attachments/assets/d8ca6ba8-9ca3-4038-ae09-c751ad3a0823)

<ins>D) Verify custom role:</ins>
![image](https://github.com/user-attachments/assets/9722670a-fa1c-4948-9d7f-f116a71f66df)

<h4>2. Assign the Resource Viewer role to the Developers group created earlier.</h4>

<ins>B) Assign RBAC to Developers group:</ins>

![image](https://github.com/user-attachments/assets/3499fa64-81c4-433e-9ae4-fa043efcb9b4)

<code>
az role assignment create --assignee "$developerGrpId" --role "Resource Viewer" --scope "/subscriptions/d3709b2c-d52e-4ed8-9a9d-3f76cc03a646/resourceGroups/pett-test"
</code>

<ins>B) Verify role assignment:</ins>

![image](https://github.com/user-attachments/assets/e25c1d9a-417c-4463-9446-a6c18f013df5)

<h4>3. Assign the built-in Contributor role to the Admins group for the same resource group.</h4>

<ins>A) Assign RBAC to Admin group:</ins>

![image](https://github.com/user-attachments/assets/a622cc73-a99b-4ea0-9c5c-3de248615095)

<ins>B) Verify role assignment:</ins>

![image](https://github.com/user-attachments/assets/d5e0f3ea-95b6-4b18-a432-2114594a3089)

<h4>4. Verify that members of the Developers group have only read access and members of the Admins
group have full access to the resource group.</h4>

<ins>A) Verify Admin access of Van Gogh:</ins>

![image](https://github.com/user-attachments/assets/c5511873-0e4e-460f-8ab6-224037cdcb4b)
![image](https://github.com/user-attachments/assets/7e4c38c0-440c-49c7-b424-83e7861fac64)


<ins>B) Verify Developer access of Michelangelo Buonarroti:</ins>

![image](https://github.com/user-attachments/assets/0cd17308-4717-4551-a30a-3e39a5d8defd)
![image](https://github.com/user-attachments/assets/de72cd6e-6038-49b3-acce-5a7992a5ec0f)


<h2>Practical Task 4: Securing Sensitive Information with Azure Key Vault</h2>
Set up Azure Key Vault to securely store and manage sensitive information such as keys, secrets, and
certificates.</p>
<b>Requirements</b>:</p>
<h4>1. Create a new Azure Key Vault in your subscription.</h4>

