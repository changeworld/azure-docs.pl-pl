---
title: Udział plików dla klastra Azure DC/OS
description: Tworzenie i zainstalować udział plików w klastrze DC/OS usługi kontenera platformy Azure
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: c1c318f4204efd24a2d9d3d83bb1cb71f5775bdb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2017
ms.locfileid: "26331206"
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Tworzenie i zainstalować udział plików w klastrze DC/OS

Ten samouczek zawiera szczegóły dotyczące sposobu tworzenia udziału plików na platformie Azure i zainstalować go na każdy agent i głównego klastra DC/OS. Konfigurowanie udziału plików ułatwia udostępnianie plików w klastrze, takich jak konfiguracja, access, dzienników i. W tym samouczku, wykonywane są następujące zadania:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Storage
> * Tworzenie udziału plików
> * Instalowanie udziału w klastrze DC/OS

Należy klastra ACS DC/OS, aby wykonać kroki opisane w tym samouczku. W razie potrzeby [w tym przykładzie skrypt](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) można utworzyć.

Dla tego samouczka wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Tworzenie udziału plików w systemie Microsoft Azure

Przed rozpoczęciem korzystania z udziału plików na platformę Azure z klastrem usługi ACS DC/OS, należy utworzyć konto i pliku udziału magazynu. Uruchom następujący skrypt, aby utworzyć udział magazynu i plików. Zaktualizuj parametry z thoes ze środowiska.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Instalowanie udziału w klastrze

Następnie udziału plików musi ma zostać zainstalowany na każdej maszynie wirtualnej w klastrze. To zadanie zostało wykonane przy użyciu narzędzia cifs/protokołu. Operacja instalacji można wykonać ręcznie na każdym węźle klastra lub przez uruchomienie skryptu przed każdym węźle w klastrze.

W tym przykładzie dwa skrypty są uruchamiane, co należy zainstalować udział plików na platformę Azure, a drugie, aby uruchomić ten skrypt w każdym węźle klastra DC/OS.

Po pierwsze nazwa konta magazynu platformy Azure i klucz dostępu są wymagane. Uruchom następujące polecenia, aby uzyskać te informacje. Zwróć uwagę, te wartości są używane w kolejnym kroku.

Nazwa konta magazynu:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Klucz dostępu do konta magazynu:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Następnie Uzyskaj FQDN wzorca DC/OS i zapisze go w zmiennej.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Skopiuj klucz prywatny do węzła głównego. Ten klucz jest potrzebne do utworzenia ssh połączenia z wszystkich węzłów w klastrze. Zaktualizuj nazwę użytkownika, jeśli wartości innych niż domyślne był używany podczas tworzenia klastra. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Utwórz połączenie SSH z wzorcem (lub pierwszego serwera głównego) klastra systemu DC/OS. Zaktualizuj nazwę użytkownika, jeśli wartości innych niż domyślne był używany podczas tworzenia klastra.

```azurecli-interactive
ssh azureuser@$FQDN
```

Utwórz plik o nazwie **cifsMount.sh**i skopiuj do niego następującą zawartość. 

Ten skrypt jest używany do zainstalowania udziału plików na platformę Azure. Aktualizacja `STORAGE_ACCT_NAME` i `ACCESS_KEY` zmiennych z informacji zebranych wcześniej.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Utworzyć drugi plik o nazwie **getNodesRunScript.sh** i skopiuj następującą zawartość do pliku. 

Ten skrypt umożliwia odnalezienie wszystkich węzłów klastra, a następnie wykonuje **cifsMount.sh** skrypt, aby zainstalować udział plików na każdym.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Uruchom skrypt, aby zainstalować udział plików na platformę Azure na wszystkich węzłach klastra.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

Udział plików jest teraz dostępny w `/mnt/share/dcosshare` w każdym węźle klastra.

## <a name="next-steps"></a>Następne kroki

W tym samouczku platformy Azure udziału plików została udostępniona klastra DC/OS, wykonując kroki:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Storage
> * Tworzenie udziału plików
> * Instalowanie udziału w klastrze DC/OS

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat integracji Azure kontenera rejestru z DC/OS na platformie Azure.  

> [!div class="nextstepaction"]
> [Równoważenie obciążenia aplikacji](container-service-dcos-acr.md)
