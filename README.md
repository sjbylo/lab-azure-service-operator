# Quick Azure Operator Demo with Azure MySQL

# To prepare for the needed secret, create the file "data"

Follow the Azure Service Operator's instructions.  

This is one way to create the needed secret from the Service Principle credentials:

Create a file with the following data:

```
AZURE_TENANT_ID=XXXXXXXX-YYYY-ZZZZ-1111-222222222222
AZURE_SUBSCRIPTION_ID=xxxxxxxx-yyyy-zzzz-9999-888888888888
AZURE_CLIENT_ID=11111111-2222-3333-4444-555555555555
AZURE_CLIENT_SECRET=sssssseeeeecccccrrrrreeeeeettttttt
AZURE_CLOUD_ENV=AzurePublicCloud
```

# Create the needed secret from the "data" file

```
oc create secret generic azureoperatorsettings --from-env-file=data -n openshift-operators 
```

# Install the Operator (as of writing - 0.37.0) with all defaults using OCP Console


# Workaround.  Change the perms for azure-service-operator service account - run this once after ASO is installed: 

```
oc adm policy add-cluster-role-to-user cluster-admin -z azure-service-operator -n openshift-operators --rolebinding-name=azure-service-operator-workaround
```
Note: This was needed for v0.37.0 (as of end Sep 2020) and may not be needed in future versions of ASO. 
See this issue if you need to understand more: https://github.com/Azure/azure-service-operator/issues/1269 


# Create the Azure MySQL Server and needed configurations

First, check the manifests.  For example, check the region you want to deploy mysql in e.g. "location: australiaeast".

Instantiate the manifests:
```
oc create -f deploy-azure-mysql
```

# Wait for all resources provisioned and access the DB with.  The "ksec" script is below.

```
#eval `ksec aro-demo-mysqluser`   
eval `oc get secret aro-demo-mysqluser -o go-template='{{range $k,$v := .data}}{{printf "%s='\''" $k}}{{if not $v}}{{$v}}{{else}}{{$v | base64decode}}{{end}}{{"'\''\n"}}{{end}}'`
```
Note: My useful ksec scripts is also in this repo under the bin dir.

# Log into the DB (install the mysql CLI first!)

Note, the username is made up of "$username@$MySqlServerName"

```
mysql -h$fullyQualifiedServerName -u$username@$MySqlServerName -p$password -D$MySqlDatabaseName
```


