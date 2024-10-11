# switchblade-samples
# Welcome to Switchblade!

In this guide we will cover installation of switchblade and some common scenarios. Our contact information is available at the bottom of this document, please do not hesitate to reach out to us if you have any questions!

# Documentation and support

You can find our documentation with examples at https://www.boundless.software/docs/
Feel free to email us any time should you require any assistance at support@boundless.software

# Requirements

- x86_64 platform
- AWS access (`AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` required)
- Azure access (`AZURE_TENANT_ID`, `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`, `AZURE_SUBSCRIPTION_ID` required)
- Kubernetes access
- Switchblade license key

# Installation

1. Create namespace for switchblade

```
kubectl create ns operators
```

2. Install License and AWS Access keys (replace variables or set environment variables accordingly)

```
kubectl create secret -n operators generic aws --from-literal AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID --from-literal AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
```
```
kubectl create secret -n operators generic azure --from-literal AZURE_TENANT_ID=$AZURE_TENANT_ID --from-literal AZURE_CLIENT_ID=$AZURE_CLIENT_ID --from-literal AZURE_CLIENT_SECRET=$AZURE_CLIENT_SECRET --from-literal AZURE_SUBSCRIPTION_ID=$AZURE_SUBSCRIPTION_ID
```
```
kubectl create secret -n operators generic switchblade --from-literal LICENSE_KEY=$LICENSE_KEY
```

3. Create state bucket

```
// use favorite IaC or console or CLI
aws s3api create-bucket --acl private --bucket mycompany-myenvironment-switchblade-state
```

4. Download and extract deployment package

```
wget https://s3.amazonaws.com/software.boundless.distributions/switchblade-0.0.18.tgz
tar xvf switchblade-0.0.18.tgz
```

5. Edit deployment.yaml

```
# update with values from step 3
        - name: AWS_STATE_BUCKET
          value: ""
        - name: AWS_STATE_BUCKET_REGION
          value: ""
```

6. Install yamls into cluster

```
# If installing for first time
kubectl create -f crd.yaml
kubectl create -f rbac.yaml
kubectl apply -f deployment.yaml

# If upgrading
kubectl replace -f crd.yaml
kubectl replace -f rbac.yaml
kubectl apply -f deployment.yaml
```

# Azure Setup for Switchblade

## Create Enterprise Application

1. In the Azure portal, navigate to Azure EntraId > App registrations > New registration.
2. Name your application and click register.
3. Export the Application (client) ID and Directory (tenant) ID.

```bash
export AZURE_TENANT_ID="Your Azure Tenant ID"
export AZURE_CLIENT_ID="Your Azure Client ID"
```

## Generate Client Secret

1. In the Azure portal, go to Azure EntraId > App registrations > Select the Enterprise Application you created.
2. Navigate to Certificates & secrets > New client secret.
3. Choose an expiry period, and click Add.
4. Export the generated secret value.

```bash
export AZURE_CLIENT_SECRET="Your Azure Client Secret"
```

## Assign API Permissions (optional, only required for EntraId resources)

1. App registrations > Select the Enterprise Application you created.
2. Go to API permissions > Add a permission > Microsoft Graph > Application permissions > Select the following permissions:
   - User.ReadWrite.All
   - Directory.ReadWrite.All
   - Group.ReadWrite.All
   - User.ManageIdentities.All
   - User.Export.All
   - AppRoleAssignment.ReadWrite.All
3. Click Add permissions.
4. Grant Admin Consent for default directory.

## Assign Permissions

1. In the Azure portal, go to Entra Id default directory.
2. Navigate to Roles and Administrators.
3. Click on Global Administrator.
4. Click Add assignments > Search for the Enterprise Application you created > Next > Enter justification > Assign.

## Add Application to your subscription

1. In the Azure portal, go to subscriptions.
2. Select the subscription you will add the application to and will be using with Switchblade.
3. Export the subscription ID.

```bash
export AZURE_SUBSCRIPTION_ID="Your Azure Subscription ID"
```

4. Navigate to Access control (IAM) > Add > Add role assignment.
5. Select Privileged Permissions > Owner
6. Click next > Select members > Search for the Enterprise Application you created > Select allow user to assign all roles > Click Assign.
7. Click next > select "Allow user to assign all roles (highly privileged)" > Click Review + assign. > Click Review + assign.