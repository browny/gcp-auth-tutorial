# gcp-auth-tutorial

[![Open in Cloud
Shell](https://gstatic.com/cloudssh/images/open-btn.png)](https://console.cloud.google.com/home/dashboard?cloudshell=true&cloudshell_git_repo=https://github.com/browny/gcp-auth-tutorial&cloudshell_tutorial=README.md)

## Preparation

### 1. Config project

```bash
gcloud config set project <PROJECT_ID>
```

### 2. Create an instance without service account

```bash
gcloud compute instances create lab --zone=asia-east1-b --machine-type=f1-micro --no-service-account --no-scopes --image-family=debian-9 --image-project=debian-cloud
```

### 3. Copy sample files to `lab` instance

```
gcloud compute scp --recurse ./resources/* lab:~/ --zone=asia-east1-b
```

### 4. SSH login into `lab` VM

```bash
gcloud compute ssh --zone=asia-east1-b lab
```


## Cloud SDK (by user account)

### 1. Authentication and set project
 
```bash
gcloud auth login
```

```bash
gcloud config set project <PROJECT_ID>
```

```bash
gcloud config list
```

### 2. Create compute engine instance

```bash
gcloud compute instances create vm-sdk-user --zone=asia-east1-b --machine-type=f1-micro
```

### 3. Revoke auth

```bash
gcloud auth revoke --all
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

### 4. Authentication and set project

```bash
gcloud auth activate-service-account --key-file <SERVICE_ACCOUNT_KEY_PATH>
```

```bash
gcloud config set project <PROJECT_ID>
```

### 5. Create compute engine instance

```bash
gcloud compute instances create vm-sdk-sva --zone=asia-east1-b --machine-type=f1-micro
```

Failed? How to fix it?

```
Grant the service account the `iam.serviceAccountUser` role on `default compute engine service account`
```

### 6. Revoke auth

```bash
gcloud auth revoke --all
```


## Cloud APIs (by user account)

### 1. Authentication and set project
 
```bash
gcloud auth login
```

```bash
gcloud config set project <PROJECT_ID>
```

### 2. Prepare API request body
 
```bash
export PROJECT_ID=<YOUR_PROJECT_ID>
```

```bash
sed "s/PROJECT_ID/$PROJECT_ID/; s/NAME/vm-api-user/" vm.json > tmp.json
```

### 3. Create compute engine instance

```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @tmp.json \
 https://www.googleapis.com/compute/v1/projects/$PROJECT_ID/zones/asia-east1-b/instances
```

### 4. Revoke auth

```bash
gcloud auth revoke --all
```


## Cloud APIs (by service account)

### 1. Authentication and set project

```bash
gcloud auth activate-service-account --key-file <SERVICE_ACCOUNT_KEY_PATH>
```

### 2. Prepare API request body
 
```bash
export PROJECT_ID=<YOUR_PROJECT_ID>
```

```bash
sed "s/PROJECT_ID/$PROJECT_ID/; s/NAME/vm-api-sva/" vm.json > tmp.json
```

### 3. Create compute engine instance

```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @tmp.json \
 https://www.googleapis.com/compute/v1/projects/$PROJECT_ID/zones/asia-east1-b/instances
```

### 4. Revoke auth

```bash
gcloud auth revoke --all
```


## Cloud APIs (by user account ADC)

### 1. Authentication and set project

```bash
unset GOOGLE_APPLICATION_CREDENTIALS
```

```bash
gcloud auth application-default login
```

### 2. Prepare API request body
 
```bash
export PROJECT_ID=<YOUR_PROJECT_ID>
```

```bash
sed "s/PROJECT_ID/$PROJECT_ID/; s/NAME/vm-api-user-adc/" vm.json > tmp.json
```

### 3. Create compute engine instance

```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth application-default print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @tmp.json \
 https://www.googleapis.com/compute/v1/projects/$PROJECT_ID/zones/asia-east1-b/instances
```

### 4. Revoke auth

```bash
gcloud auth revoke --all
```


## Cloud APIs (by service account ADC)

### 1. Authentication and set project

```bash
export GOOGLE_APPLICATION_CREDENTIALS=<SERVICE_ACCOUNT_KEY_PATH>
```

### 2. Prepare API request body
 
```bash
export PROJECT_ID=<YOUR_PROJECT_ID>
```

```bash
sed "s/PROJECT_ID/$PROJECT_ID/; s/NAME/vm-api-sva-adc/" vm.json > tmp.json
```

### 3. Create compute engine instance

```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth application-default print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @tmp.json \
 https://www.googleapis.com/compute/v1/projects/$PROJECT_ID/zones/asia-east1-b/instances
```

### 4. Revoke auth

```bash
gcloud auth revoke --all
```


## ML APIs (by user account, NOT supported)

### 1. Authentication and set project

```bash
gcloud auth login
```

### 2. Make vision API call
 
```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @request.json \
 https://vision.googleapis.com/v1/images:annotate
```

### 3. Failed? Why?

gcloud auth login act as an oauth client, its project is NOT the project which enabled Vision API

### 4. Revoke auth

```bash
gcloud auth revoke --all
```


## ML APIs (by API key)

### 1. Create API Key

- Go to IAM & Admin

<walkthrough-menu-navigation sectionId="API_SECTION"></walkthrough-menu-navigation>

- Go to <walkthrough-spotlight-pointer cssSelector="#cfctest-section-nav-item-credentials"
  text="Credentials"> </walkthrough-spotlight-pointer>

- Click <walkthrough-spotlight-pointer cssSelector="[aria-label='Create credential']"
text="CREATE CREDENTIALS"> </walkthrough-spotlight-pointer>

### 2. Make vision API call

```bash
export API_KEY=<CREATED_API_KEY>
```

```bash
curl -X POST \
 -H "Content-Type: application/json; charset=utf-8" \
 -d @request.json \
 https://vision.googleapis.com/v1/images:annotate?key=$API_KEY
```

### 3. Revoke auth

```bash
gcloud auth revoke --all
```


## ML APIs (by service account)

### 1. Authentication and set project

```bash
gcloud auth activate-service-account --key-file <SERVICE_ACCOUNT_KEY_PATH>
```

### 2. Make vision API call
 
```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @request.json \
 https://vision.googleapis.com/v1/images:annotate
```

### 3. Revoke auth

```bash
gcloud auth revoke --all
```


## ML APIs (by service account ADC)

### 1. Authentication and set project

```bash
export GOOGLE_APPLICATION_CREDENTIALS=<SERVICE_ACCOUNT_KEY_PATH>
```

### 2. Make vision API call
 
```bash
curl -X POST \
 -H "Authorization: Bearer "$(gcloud auth application-default print-access-token) \
 -H "Content-Type: application/json; charset=utf-8" \
 --data @request.json \
 https://vision.googleapis.com/v1/images:annotate
```

### 3. Revoke auth

```bash
gcloud auth revoke --all
```

