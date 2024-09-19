Working with Azure Storage Account using Azure CLI

az login

az account show -0 table

az account set --subscription "Your-Subscription-ID-or-Name"

# 1. Create an Azure Resource Group

resourceGroupName="myResourceGroup"

location="EastUS"

az group create --name $resourceGroupName --location $location

# 2. Create an Azure Storage Account

storageAccountName="mystorageaccount$RANDOM"

storageSku="Standard_LRS"

az storage account create --name $storageAccountName --resource-group $resourceGroupName --location $location --sku $storageSku --kind StorageV2

# 3. Get the Secret Keys of the Azure Storage Account

storageAccountKey=$(az storage account keys list --resource-group $resourceGroupName --account-name $storageAccountName --query "[0].value" --output tsv)

# 4. Create a Public and Private Blob Container

# Public Blob Container

publicContainerName="publiccontainer"

az storage container create --name $publicContainerName --account-name $storageAccountName --account-key $storageAccountKey 

# 5. Private Blob Container

privateContainerName="privatecontainer"

az storage container create --name $privateContainerName --account-name $storageAccountName --account-key $storageAccountKey --public-access off

# 6.Upload a Local File to the Public Blob Container

localFilePath="C:/path/to/your/file.txt"

blobName="uploadedfile.txt"

az storage blob upload --account-name $storageAccountName --account-key $storageAccountKey --container-name $publicContainerName --name $blobName --file $localFilePath

# 7. Display the URL of the Blob File

blobUri=$(az storage blob url --account-name $storageAccountName --container-name $publicContainerName --name $blobName --output tsv)

echo$blobUri

# 8. Delete the Blob

az storage blob delete --account-name $storageAccountName --account-key $storageAccountKey --container-name $publicContainerName --name $blobName

# 9. Delete the Containers

az storage container delete --account-name $storageAccountName --account-key $storageAccountKey --name $publicContainerName

az storage container delete --account-name $storageAccountName --account-key $storageAccountKey --name $privateContainerName

# 10. Delete the Azure Storage Account

az storage account delete --name $storageAccountName --resource-group $resourceGroupName --yes

# 11. Delete the Resource Group

az group delete --name $resourceGroupName --yes --no-wait