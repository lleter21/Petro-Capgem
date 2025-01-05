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

<h2>Practical Task 2: Enabling Single Sign-On (SSO) and Multi-Factor Authentication (MFA)
 (RBAC)</h2>
 Configure Single Sign-On (SSO) and Multi-Factor Authentication (MFA) for users in a Microsoft Entra ID
directory to enhance identity and access security.</p>
<b>Requirements</b>:</p>
<h4>1. Enable Single Sign-On (SSO) for your Microsoft Entra ID tenant.</h4>

<ins>A) Creating Enterprise application:</ins>

<img width="1243" alt="image" src="https://github.com/user-attachments/assets/1a74cd67-ec46-41c0-835e-1ae8e7278782" />

<ins>B) Go to SSO section and insert:</ins>
Identifier (Entity ID) - IAMShowcase
Reply URL(Assertion Consumer Service URL) - https://sptest.iamshowcase.com/acs</ins>

![image](https://github.com/user-attachments/assets/ca0f6efa-ab2a-446c-aecb-c0877d213e7b)

<ins>C) Download Federation Metadata XML:</ins>

![image](https://github.com/user-attachments/assets/8826acdd-7890-4a1c-876e-1aa904f16d59)

<ins>D) Open a new tab and access https://sptest.iamshowcase.com/. Click on "Instructions" and select "SP Initiated SSO.":</ins>

<img width="545" alt="image" src="https://github.com/user-attachments/assets/2e411566-293a-4a0f-8411-0dbfc9518c7f" />

<ins>E) Upload and submit Federation Metadata XML file downloaded earlier:</ins>

![image](https://github.com/user-attachments/assets/97f41059-834c-49e6-8536-f7b7234f6c7e)

<ins>F) Add users/groups to Enterprise Application (SSO-test-app):</ins>

![image](https://github.com/user-attachments/assets/826057b0-80ae-4067-a293-e4889455644f)

![image](https://github.com/user-attachments/assets/817973f5-02e0-4ffe-b21b-e13f7b20001a)



<h4>2. Enforce Multi-Factor Authentication (MFA) for all users in the directory.
</h4>

<ins>A) Open Entra Id => Security section:</ins>

![image](https://github.com/user-attachments/assets/e4753aec-cb5d-4a69-89c3-9a0b6aec8d9f)

<ins>B) Go to Conditional Access:</ins>

![image](https://github.com/user-attachments/assets/ce5bf6f7-86c5-460f-a3fd-fc4d50cdc426)

<ins>C) Click Create new policy:</ins>

![image](https://github.com/user-attachments/assets/d9aff629-354e-4fbe-aaa0-753d3c01d914)

<ins>D) Enable it for all cloud apps/users and require multifactor authentication:</ins>

![image](https://github.com/user-attachments/assets/2206247a-49ca-43fd-a3b0-165dda50bb33)

<h4>3. Configure conditional access policies to require MFA for high-risk sign-ins.
</h4>

<ins>A) Go to the same policy created earlier (MFA MFA enforcement) and enable sign-ins risk to high</ins>
![image](https://github.com/user-attachments/assets/82a605cd-fa6e-4996-b761-e4078d30cd43)

<h4>3.4. Verify that SSO and MFA settings are correctly applied for the users.</h4>

<ins>A) Logout from Azure and Login again, you will see Microsoft Authenticator window to enable MFA</ins>

![image](https://github.com/user-attachments/assets/d323aae5-d421-4be8-bf05-d99a7987518e)

<ins>B) To test SSO, open recently created application - SSO-test-app and click test this application</ins>
![image](https://github.com/user-attachments/assets/a1c035bf-77bf-4f63-9ce4-c1baee91d51f)

<ins>B) Press test sign in</ins>

![image](https://github.com/user-attachments/assets/fcb5fc59-4e51-4499-94c4-75f07f6f3a86)

<ins>C) Pick all accounts one by one to check it</ins>

<img width="722" alt="image" src="https://github.com/user-attachments/assets/5df4451c-bdd4-45c1-ba78-f99dc20f718d" />

<ins>C) Verify result of SSO login</ins>

![image](https://github.com/user-attachments/assets/2bf493a7-891f-4528-a4b2-0082534fe50f)


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

<ins>A) Create Key Vault:</ins>
![image](https://github.com/user-attachments/assets/c177be9f-6bb4-4d0d-87d0-1c6e4085889c)

<code>
az keyvault create --resource-group "pett-test" --location "westeurope" --name "pett-kv" --sku "standard" --enable-rbac-authorization false
</code>

<ins>B) Verify Key Vault creation:</ins>
![image](https://github.com/user-attachments/assets/5db8f369-cb10-45ec-b7e3-23c6454ffd91)

<h4>2. Add a secret to the Key Vault (e.g., a database connection string).</h4>

<ins>A) Create Key Vault secret:</ins>

![image](https://github.com/user-attachments/assets/4a822d1a-6eaa-4fac-bd90-2f72431a8fee)

<code>
az keyvault secret set --name ConnectionString-messaging --vault-name pett-kv --value "server=(LocalDb)\\MSSQLLocalDb;database=messaging;trusted_connection=yes;Pooling=false;"
</code>
<ins>B) Verify Key Vault secret:</ins>

![image](https://github.com/user-attachments/assets/8310bc3c-90c3-4aa3-8753-15cb0a2cb7ae)

<h4>3. Set access policies to grant the Application Developer role (assigned to the Developers group)
permission to retrieve secrets from the Key Vault.</h4>

<ins>A) Assign Get, List permissions for Developers:</ins>

![image](https://github.com/user-attachments/assets/cec38a6a-8398-4d62-b346-777dd0b1df55)

<ins>A) Verify Get, List permissions for Developers:</ins>

![image](https://github.com/user-attachments/assets/c67684c9-f9b4-4c84-9dc3-d05c535d3072)

<h4>4. Verify that only members of the Developers group can access the stored secret.</h4>

<ins>A) Verify from developers perspective:</ins>
![image](https://github.com/user-attachments/assets/9b59e7ac-46b4-475f-b457-98d230a6d8bb)

![image](https://github.com/user-attachments/assets/27481489-136b-4b3b-83e6-d2d9cfeb2c18)

<ins>B) Verify from admins perspective:</ins>

![image](https://github.com/user-attachments/assets/a0633e8b-79e5-4b98-baf0-d116ded99e1c)
![image](https://github.com/user-attachments/assets/8c29bae0-c84f-417f-bfe9-1b69632d27ed)


<h2>Practical Task 5: Creating and Assigning Basic Azure Policies</h2>
Define and assign Azure Policies to enforce compliance with organizational standards for resource
management.</p>
<b>Requirements</b>:</p>
<h4>1. Create an Azure Policy to enforce tagging for all newly created resources with a specific tag (e.g.,
Environment: Development).</h4>
<ins>A) Create policy based on builtin definition require a tag and its value on resources:</ins>

![image](https://github.com/user-attachments/assets/3e11c6dd-4c4c-4cdd-885c-cd8bedd82248)
![image](https://github.com/user-attachments/assets/503a0b66-5668-4da1-9abe-aea96b135a1c)
![image](https://github.com/user-attachments/assets/65f274b7-c923-4080-8525-11611fcb8a2b)
![image](https://github.com/user-attachments/assets/5253c9d0-768d-428b-876c-36705512b1c3)

<ins>A) Since we enabled enforcement, we need to verify that we cannot create any resources without specific tag</ins>

![image](https://github.com/user-attachments/assets/4898a8b5-8f15-4eec-84be-08050e77ad3e)


<h4>2. Assign the policy to a resource group.</h4>

![image](https://github.com/user-attachments/assets/9e2ae683-9089-4f77-8eb5-f81fec994130)

<h4>3. Verify that any new resource created in the resource group without the required tag is marked as
non-compliant.</h4>

<ins>A) We have to disable enforcement to create any resource to verify it</ins>

![image](https://github.com/user-attachments/assets/90aaafeb-66d9-429d-9a56-f04d7db03753)

![image](https://github.com/user-attachments/assets/1ddf1de6-7d79-4e8a-98cb-07262e4022b5)

<ins>B) Verification of compliance statuses </ins>

![image](https://github.com/user-attachments/assets/4e9e79c5-ce81-4a10-8ca0-31d0214f829b)

![image](https://github.com/user-attachments/assets/d805531d-614f-461d-bf7f-4588aeb52253)

![image](https://github.com/user-attachments/assets/98d1be4a-2c5b-4f7f-9c2d-359557afa9d6)

<h4>4. Review and document the compliance status of the resource group.
</h4>
![image](https://github.com/user-attachments/assets/96e1719d-b9f7-4923-aca9-1d8944f2250b)

<h2>Practical Task 6: Using Policy Effects to Enforce Compliance</h2>
Configure Azure Policies with different policy effects to enforce compliance and manage resources
according to organizational standards.</p>
<b>Requirements</b>:</p>
<h4>1. Create a policy with the Audit effect to monitor and log untagged resources within a resource
group</h4>

<ins>A) Create definition </ins>

![image](https://github.com/user-attachments/assets/9625b835-5fd7-4dca-bee5-57ac26c554c9)

<ins>B) Verification of new resource created </ins>
![image](https://github.com/user-attachments/assets/503e1b11-8563-4945-a89b-cce301e9ab99)

<h4>2.Create a policy with the DeployIfNotExists effect to automatically add a specific tag (Owner: IT) to
any newly created resource.</h4>

<ins>A) Create definition and asssignment </ins>

![image](https://github.com/user-attachments/assets/50d6e9d7-ef75-45bf-b880-6d0a0b7c97bf)

![image](https://github.com/user-attachments/assets/934fcc4e-0dcd-442c-8367-116f861ce22a)

Custom User-Assigned Managed Identity with Contributor role (should`ve been used Tag Contributor)

![image](https://github.com/user-attachments/assets/44eacd57-ad16-4dc3-a159-8033a37f29fa)

<ins>B) Check the policy </ins>

![image](https://github.com/user-attachments/assets/089b30b7-66c7-43ad-b1c7-160aca5d6e8e)

If we trigger remediation, it works:

![image](https://github.com/user-attachments/assets/d1626768-44d0-4641-99dc-06144286c79c)

However, tag applies only to resource group level, not to resources layer for some reason:

![image](https://github.com/user-attachments/assets/776047c4-1098-4131-a70b-0669fd5f377b)

![image](https://github.com/user-attachments/assets/dbece26e-e8af-4b68-96bf-e824c3adac91)
