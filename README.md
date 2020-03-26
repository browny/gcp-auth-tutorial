# gcp-auth-tutorial

[![Open in Cloud
Shell](https://gstatic.com/cloudssh/images/open-btn.png)](https://console.cloud.google.com/cloudshell/open?git_repo=https://github.com/browny/gcp-auth-tutorial&tutorial=README.md)


## Preparation

### 1. Config project

```bash
gcloud config set project <PROJECT_ID>
```

### 2. Create a container based VM

```bash
gcloud compute instances create lab --zone=asia-east1-b --machine-type=g1-small --image-family=cos-stable --image-project=cos-cloud
```

### 3. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```

### 4. Back to Cloud Shell

Exit from `lab` VM instance

```bash
exit
```

## Cloud SDK (by user account)

### 1. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```

### 2. Run an environment with gcloud SDK

```bash
docker run -v $(pwd):/lab -ti google/cloud-sdk:latest bash
```

### 3. Authentication and set project
 
```bash
gcloud auth login
```

```bash
gcloud config set project <PROJECT_ID>
```

```bash
gcloud config list
```

### 4. Create compute engine instance

```bash
gcloud compute instances create vm-sdk-user --zone=asia-east1-b --machine-type=f1-micro
```

### 5. Back to Cloud Shell

Exit from container in `lab` VM instance

```bash
exit
```

Exit from `lab` VM instance

```bash
exit
```

## Cloud SDK (by service account)

### 1. Locate to IAM/Service Accounts

- Go to IAM & Admin

<walkthrough-menu-navigation sectionId="IAM_ADMIN_SECTION"></walkthrough-menu-navigation>

- Go to <walkthrough-spotlight-pointer cssSelector="#cfctest-section-nav-item-serviceaccounts"
  text="Service Accounts"> </walkthrough-spotlight-pointer>

### 2. Create service account

1. Go to <walkthrough-spotlight-pointer cssSelector="[aria-label='Create service account']"
text="Create"> </walkthrough-spotlight-pointer>
1. Input a meaningful name (e.g. vm-creator) -> Click `CREATE`
1. Bind roles: `Compute Instance Admin (v1)` -> Click `CONTINUE`
1. Download a generated JSON key -> Click `+ CREATE KEY` -> Select `JSON` -> Click `CREATE`

### 3. Upload key onto `lab` VM instance

1. Upload to Cloud Shell first by UI  
1. Copy key into `lab` VM instance

  ```bash
  gcloud compute scp <SERVICE_ACCOUNT_KEY_PATH> lab:~/ --zone=asia-east1-b
  ```

### 4. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```

### 5. Run an environment with gcloud SDK

```bash
docker run -v $(pwd):/lab -ti google/cloud-sdk:latest bash
```

### 5. Athentication and set project

```bash
gcloud auth activate-service-account --key-file <SERVICE_ACCOUNT_KEY_PATH>
```

```bash
gcloud config set project <PROJECT_ID>
```

### 6. Create compute engine instance

```bash
gcloud compute instances create vm-sdk-sva --zone=asia-east1-b --machine-type=f1-micro
```

Failed? How to fix it?

```
Grant the service account the `iam.serviceAccountUser` role on `default compute engine service account`
```

### 7. Back to Cloud Shell

Exit from container in `lab` VM instance

```bash
exit
```

Exit from `lab` VM instance

```bash
exit
```

## Cloud APIs (by user account)

### 1. Upload template request body

```bash
gcloud compute scp ./resources/vm.json lab:~/ --zone=asia-east1-b
```

### 2. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```

### 3. Run an environment with gcloud SDK

```bash
docker run -v $(pwd):/lab -ti google/cloud-sdk:latest bash
```

### 4. Authentication and set project
 
```bash
gcloud auth login
```

```bash
gcloud config set project <PROJECT_ID>
```

### 5. Prepare API request body
 
```bash
export PROJECT_ID=<YOUR_PROJECT_ID>
```

```bash
sed "s/PROJECT_ID/$PROJECT_ID/; s/NAME/vm-api-user/" /lab/vm.json > /lab/tmp.json
```

### 6. Create compute engine instance

```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @/lab/tmp.json \
 https://www.googleapis.com/compute/v1/projects/$PROJECT_ID/zones/asia-east1-b/instances
```

### 7. Back to Cloud Shell

Exit from container in `lab` VM instance

```bash
exit
```

Exit from `lab` VM instance

```bash
exit
```

## Cloud APIs (by service account)

### 1. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```

### 2. Run an environment with gcloud SDK

```bash
docker run -v $(pwd):/lab -ti google/cloud-sdk:latest bash
```

### 3. Athentication and set project

```bash
gcloud auth activate-service-account --key-file <SERVICE_ACCOUNT_KEY_PATH>
```

### 4. Prepare API request body
 
```bash
export PROJECT_ID=<YOUR_PROJECT_ID>
```

```bash
sed "s/PROJECT_ID/$PROJECT_ID/; s/NAME/vm-api-sva/" /lab/vm.json > /lab/tmp.json
```

### 5. Create compute engine instance

```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @/lab/tmp.json \
 https://www.googleapis.com/compute/v1/projects/$PROJECT_ID/zones/asia-east1-b/instances
```

### 6. Back to Cloud Shell

Exit from container in `lab` VM instance

```bash
exit
```

Exit from `lab` VM instance

```bash
exit
```

## Cloud APIs (by user account ADC)

### 1. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```

### 2. Run an environment with gcloud SDK

```bash
docker run -v $(pwd):/lab -ti google/cloud-sdk:latest bash
```

### 3. Athentication and set project

```bash
unset GOOGLE_APPLICATION_CREDENTIALS
```

```bash
gcloud auth application-default login
```

### 4. Prepare API request body
 
```bash
export PROJECT_ID=<YOUR_PROJECT_ID>
```

```bash
sed "s/PROJECT_ID/$PROJECT_ID/; s/NAME/vm-api-user-adc/" /lab/vm.json > /lab/tmp.json
```

### 5. Create compute engine instance

```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth application-default print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @/lab/tmp.json \
 https://www.googleapis.com/compute/v1/projects/$PROJECT_ID/zones/asia-east1-b/instances
```

### 6. Back to Cloud Shell

Exit from container in `lab` VM instance

```bash
exit
```

Exit from `lab` VM instance

```bash
exit
```

## Cloud APIs (by service account ADC)

### 1. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```

### 2. Run an environment with gcloud SDK

```bash
docker run -v $(pwd):/lab -ti google/cloud-sdk:latest bash
```

### 3. Athentication and set project

```bash
export GOOGLE_APPLICATION_CREDENTIALS=<SERVICE_ACCOUNT_KEY_PATH>
```

### 4. Prepare API request body
 
```bash
export PROJECT_ID=<YOUR_PROJECT_ID>
```

```bash
sed "s/PROJECT_ID/$PROJECT_ID/; s/NAME/vm-api-sva-adc/" /lab/vm.json > /lab/tmp.json
```

### 5. Create compute engine instance

```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth application-default print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @/lab/tmp.json \
 https://www.googleapis.com/compute/v1/projects/$PROJECT_ID/zones/asia-east1-b/instances
```

### 6. Back to Cloud Shell

Exit from container in `lab` VM instance

```bash
exit
```

Exit from `lab` VM instance

```bash
exit
```

## ML APIs (by user account, NOT supported)

### 1. Upload template request body

```bash
gcloud compute scp ./resources/request.json lab:~/ --zone=asia-east1-b
```

### 2. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```

### 3. Run an environment with gcloud SDK

```bash
docker run -v $(pwd):/lab -ti google/cloud-sdk:latest bash
```

### 4. Athentication and set project

```bash
gcloud auth login
```

### 5. Make vision API call
 
```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @/lab/request.json \
 https://vision.googleapis.com/v1/images:annotate
```

### 6. Failed? Why?

gcloud auth login act as an oauth client, its project is NOT the project which enabled Vision API

### 7. Back to Cloud Shell

Exit from container in `lab` VM instance

```bash
exit
```

Exit from `lab` VM instance

```bash
exit
```

## ML APIs (by API key)

### 1. Create API Key

- Go to IAM & Admin

<walkthrough-menu-navigation sectionId="API_SECTION"></walkthrough-menu-navigation>

- Go to <walkthrough-spotlight-pointer cssSelector="#cfctest-section-nav-item-credentials"
  text="Credentials"> </walkthrough-spotlight-pointer>

- Click <walkthrough-spotlight-pointer cssSelector="[aria-label='Create credential']"
text="CREATE CREDENTIALS"> </walkthrough-spotlight-pointer>

### 2. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```

### 3. Run an environment with gcloud SDK

```bash
docker run -v $(pwd):/lab -ti google/cloud-sdk:latest bash
```

### 4. Make vision API call

```bash
export API_KEY=<CREATED_API_KEY>
```

```bash
curl -X POST \
 -H "Content-Type: application/json; charset=utf-8" \
 -d @/lab/request.json \
 https://vision.googleapis.com/v1/images:annotate?key=$API_KEY
```

### 5. Back to Cloud Shell

Exit from container in `lab` VM instance

```bash
exit
```

Exit from `lab` VM instance

```bash
exit
```

## ML APIs (by service account)

### 1. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```

### 2. Run an environment with gcloud SDK

```bash
docker run -v $(pwd):/lab -ti google/cloud-sdk:latest bash
```

### 3. Athentication and set project

```bash
gcloud auth activate-service-account --key-file <SERVICE_ACCOUNT_KEY_PATH>
```

### 4. Make vision API call
 
```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @/lab/request.json \
 https://vision.googleapis.com/v1/images:annotate
```

### 5. Back to Cloud Shell

Exit from container in `lab` VM instance

```bash
exit
```

Exit from `lab` VM instance

```bash
exit
```

## ML APIs (by service account ADC)

### 1. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```

### 2. Run an environment with gcloud SDK

```bash
docker run -v $(pwd):/lab -ti google/cloud-sdk:latest bash
```

### 3. Athentication and set project

```bash
export GOOGLE_APPLICATION_CREDENTIALS=<SERVICE_ACCOUNT_KEY_PATH>
```

### 4. Make vision API call
 
```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth application-default print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @/lab/request.json \
 https://vision.googleapis.com/v1/images:annotate
```

### 5. Back to Cloud Shell

Exit from container in `lab` VM instance

```bash
exit
```

Exit from `lab` VM instance

```bash
exit
```

