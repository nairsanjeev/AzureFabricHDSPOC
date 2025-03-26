Here are the detailed steps and sample code to create a FHIR API service in Azure and ingest Synthea data using the `$import` API. Further you can follow the steps for Fabric HDS set up

### Step 1: Create a FHIR Service in Azure

1. **Create a Workspace**:
   - Go to the Azure portal.
   - Create a new resource group if you don't have one.
   - Search for "Azure Health Data Services" and create a new workspace.

2. **Create a FHIR Service**:
   - Within the workspace, add a new FHIR service.
   - Configure the FHIR service settings as needed.

### Step 2: Register a Client Application

1. **Register an Application in Microsoft Entra ID**:
   - Go to the Azure Active Directory in the Azure portal.
   - Register a new application and note down the Client ID and Tenant ID.
   - Create a client secret for the application.

2. **Grant API Permissions**:
   - Assign the necessary API permissions to the registered application.
   - Grant admin consent for the permissions.

### Step 3: Ingest Synthea Data Using `$import` API

1. **Generate Synthea Data**:
   - Download and run the Synthea tool to generate synthetic patient data in FHIR format.
   - Example command to generate data:
     ```bash
     ./run_synthea -p 1000
     ```

2. **Prepare Data for Import**:
   - Convert the generated data into NDJSON format if not already.
   - Upload the NDJSON files to an Azure Blob Storage container.

3. **Import Data Using `$import` API**:
   - Use the following sample code to import data into the FHIR service.

```python
import requests
import json
from azure.identity import DefaultAzureCredential

# Replace with your values
fhir_service_url = "https://<your-fhir-service>.azurehealthcareapis.com"
blob_url = "https://<your-storage-account>.blob.core.windows.net/<your-container>/<your-file>.ndjson"
client_id = "<your-client-id>"
tenant_id = "<your-tenant-id>"
client_secret = "<your-client-secret>"

# Get access token
credential = DefaultAzureCredential()
token = credential.get_token("https://<your-fhir-service>.azurehealthcareapis.com/.default")

# Set headers
headers = {
    "Authorization": f"Bearer {token.token}",
    "Content-Type": "application/json"
}

# Create import request payload
payload = {
    "inputFormat": "application/fhir+ndjson",
    "inputSource": blob_url,
    "mode": "InitialLoad"
}

# Send import request
response = requests.post(f"{fhir_service_url}/$import", headers=headers, data=json.dumps(payload))

# Check response
if response.status_code == 202:
    print("Import started successfully.")
else:
    print(f"Failed to start import: {response.status_code} - {response.text}")
```

### Additional Resources

- [Get started with the FHIR service in Azure Health Data Services](https://learn.microsoft.com/en-us/azure/healthcare-apis/fhir/get-started-with-fhir)
- [Import data into the FHIR service](https://learn.microsoft.com/en-us/azure/healthcare-apis/fhir/import-data)

  Sure! Here are the detailed steps to query a FHIR API using the REST Client extension in Visual Studio Code.

### Step 1: Install REST Client Extension

1. **Open Visual Studio Code**.
2. **Install REST Client Extension**:
   - Click on the Extensions icon on the left sidebar.
   - Search for "REST Client".
   - Install the REST Client extension.

### Step 2: Create a `.http` File

1. **Create a New File**:
   - Create a new file in Visual Studio Code and name it `fhir-api.http`.

2. **Define Variables**:
   - Add the following variables to the top of the file:
     ```http
     @fhirurl = https://<your-fhir-service>.azurehealthcareapis.com
     @clientid = <your-client-id>
     @clientsecret = <your-client-secret>
     @tenantid = <your-tenant-id>
     ```

### Step 3: Get Microsoft Entra Access Token

1. **Add Request to Get Access Token**:
   - Add the following request to get the access token:
     ```http
     ### Get Access Token
     # @name getAADToken
     POST https://login.microsoftonline.com/{{tenantid}}/oauth2/token
     Content-Type: application/x-www-form-urlencoded

     grant_type=client_credentials&resource={{fhirurl}}&client_id={{clientid}}&client_secret={{clientsecret}}&scope={{fhirurl}}/.default
     ```

2. **Extract Access Token**:
   - Add the following line to extract the access token from the response:
     ```http
     @token = {{getAADToken.response.body.access_token}}
     ```

### Step 4: Query FHIR API

1. **Add Request to Query FHIR API**:
   - Add the following request to query the FHIR API for patient data:
     ```http
     ### Get Patient Data
     GET {{fhirurl}}/Patient
     Authorization: Bearer {{token}}
     ```

2. **Send Request**:
   - Click on the "Send Request" button that appears above each request to execute it.

### Example `.http` File

Here's how your `fhir-api.http` file should look:

```http
@fhirurl = https://<your-fhir-service>.azurehealthcareapis.com
@clientid = <your-client-id>
@clientsecret = <your-client-secret>
@tenantid = <your-tenant-id>

### Get Access Token
# @name getAADToken
POST https://login.microsoftonline.com/{{tenantid}}/oauth2/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource={{fhirurl}}&client_id={{clientid}}&client_secret={{clientsecret}}&scope={{fhirurl}}/.default

@token = {{getAADToken.response.body.access_token}}

### Get Patient Data
GET {{fhirurl}}/Patient
Authorization: Bearer {{token}}
```

### Additional Resources

- [Access Azure Health Data Services using REST Client](https://learn.microsoft.com/en-us/azure/healthcare-apis/fhir/using-rest-client)[1](https://learn.microsoft.com/en-us/azure/healthcare-apis/fhir/using-rest-client)
- [Using VSCode REST Client Extension](https://techcommunity.microsoft.com/blog/healthcareandlifesciencesblog/calling-rest-apis-from-the-ide/3145949)[2](https://techcommunity.microsoft.com/blog/healthcareandlifesciencesblog/calling-rest-apis-from-the-ide/3145949)

Feel free to ask if you have any questions or need further assistance!


Follow these steps for Fabric set up

https://learn.microsoft.com/en-us/industry/healthcare/healthcare-data-solutions/ahds-data-export-configure?toc=%2Findustry%2Fhealthcare%2Ftoc.json&bc=%2Findustry%2Fbreadcrumb%2Ftoc.json
