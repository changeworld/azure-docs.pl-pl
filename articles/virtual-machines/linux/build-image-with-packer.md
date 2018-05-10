---
title: Tworzenie obrazów maszyn wirtualnych systemu Linux platformy Azure z pakujący | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać pakujący do tworzenia obrazów maszyn wirtualnych systemu Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/03/2018
ms.author: iainfou
ms.openlocfilehash: 7d7ba6a493cca3dd14829e6527136af6df424c05
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Jak używać pakujący do tworzenia obrazów maszyny wirtualnej systemu Linux na platformie Azure
Każda maszyna wirtualna (VM) na platformie Azure jest tworzony z obrazu, który definiuje dystrybucji systemu Linux i wersji systemu operacyjnego. Obrazy mogą obejmować wstępnie zainstalowane aplikacje i konfiguracje. Portalu Azure Marketplace zawiera wiele obrazów pierwszy i innych firm najczęściej używane dystrybucje i środowisk aplikacji, lub można utworzyć własne niestandardowe obrazy dostosowane do potrzeb użytkownika. W tym artykule szczegółowo przedstawiają, jak korzystać z narzędzia typu open source [pakujący](https://www.packer.io/) do definiowania i tworzenie niestandardowych obrazów na platformie Azure.


## <a name="create-azure-resource-group"></a>Tworzenie grupy zasobów platformy Azure
Podczas procesu tworzenia pakujący tworzy tymczasowy zasobów Azure zbudował źródłowej maszyny Wirtualnej. Aby przechwycić tego źródła do użycia jako obraz maszyny Wirtualnej, należy zdefiniować grupę zasobów. Dane wyjściowe z procesu tworzenia pakujący znajduje się w tej grupie zasobów.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Utwórz poświadczenia platformy Azure
Pakujący jest uwierzytelniany w usłudze Azure przy użyciu nazwy głównej usługi. Podmiot zabezpieczeń usługi Azure jest tożsamość zabezpieczeń korzystających z aplikacji, usługami i automatyzacja takich narzędzi jak pakujący. Kontrolowanie i definiowanie uprawnień określające, jakie operacje nazwy głównej usługi można wykonać na platformie Azure.

Utwórz usługę podmiotu zabezpieczeń z [az ad sp utworzyć do rbac](/cli/azure/ad/sp#create-for-rbac) i poświadczenia, które wymaga pakujący wyjściowe:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Przykład danych wyjściowych z poprzedniego polecenia jest następujący:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Aby uwierzytelniać na platformie Azure, również należy uzyskać identyfikator subskrypcji platformy Azure z [Pokaż konto az](/cli/azure/account#az_account_show):

```azurecli
az account show --query "{ subscription_id: id }"
```

Dane wyjściowe z tych dwóch poleceń należy użyć w następnym kroku.


## <a name="define-packer-template"></a>Zdefiniuj szablon pakujący
Aby tworzyć obrazy, należy utworzyć szablon w formacie JSON. W szablonie należy zdefiniować producentów i provisioners wykonujące procesu tworzenia rzeczywistych. Ma pakujący [administracyjnej dla platformy Azure](https://www.packer.io/docs/builders/azure.html) który można zdefiniować zasobów platformy Azure, takich jak poświadczenia główne usługi utworzony w poprzednim kroku.

Utwórz plik o nazwie *ubuntu.json* i wklej następującą zawartość. Wprowadź własne wartości dla następujących elementów:

| Parametr                           | Gdzie można uzyskać |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Pierwszy wiersz danych wyjściowych z `az ad sp` Utwórz polecenie - *appId* |
| *client_secret*                     | Drugi wiersz danych wyjściowych z `az ad sp` Utwórz polecenie - *hasła* |
| *tenant_id*                         | Trzeci wiersz danych wyjściowych z `az ad sp` Utwórz polecenie - *dzierżawy* |
| *IDENTYFIKATOR_SUBSKRYPCJI*                   | Dane wyjściowe z `az account show` polecenia |
| *managed_image_resource_group_name* | Nazwa grupy zasobów, utworzonego w pierwszym kroku |
| *managed_image_name*                | Nazwa obrazu dysków zarządzanych w tworzonym |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Ten szablon tworzy obraz Ubuntu 16.04 LTS, instaluje NGINX, a następnie deprovisions maszyny Wirtualnej.

> [!NOTE]
> Po rozwinięciu na ten szablon służy do poświadczeń użytkownika należy dostosować polecenie administracyjnej deprovisions agentem Azure, aby odczytać `-deprovision` zamiast `deprovision+user`.
> `+user` Flagi usuwa wszystkie konta użytkowników ze źródłowej maszyny Wirtualnej.


## <a name="build-packer-image"></a>Utwórz obraz pakujący
Jeśli nie masz jeszcze pakujący zainstalowany na komputerze lokalnym [postępuj zgodnie z instrukcjami instalacji pakujący](https://www.packer.io/docs/install/index.html).

Utworzyć obraz, określając pakujący Twojego pliku szablonu w następujący sposób:

```bash
./packer build ubuntu.json
```

Przykład danych wyjściowych z poprzedniego polecenia jest następujący:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Trwa kilka minut, aż pakujący do tworzenia maszyny Wirtualnej, uruchom provisioners i wyczyścić wdrożenia.


## <a name="create-vm-from-azure-image"></a>Tworzenie maszyny Wirtualnej z obrazu platformy Azure
Można teraz utworzyć Maszynę wirtualną z obrazu z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Określ obraz został utworzony z `--image` parametru. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* z *myPackerImage* i generuje klucze SSH, jeśli jeszcze nie istnieje:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Jeśli chcesz utworzyć maszyn wirtualnych w innej grupie zasobów lub regionie niż obraz pakujący, określ identyfikator obrazu, a nie nazwę obrazu. Możesz uzyskać identyfikator obrazu z [Pokaż obraz az](/cli/azure/image#az-image-show).

Trwa kilka minut, aby utworzyć maszynę Wirtualną. Po utworzeniu maszyny Wirtualnej, zanotuj `publicIpAddress` wyświetlanych przez wiersza polecenia platformy Azure. Ten adres jest używany do uzyskania dostępu do witryny NGINX za pośrednictwem przeglądarki sieci web.

Aby zezwolić na ruch internetowy do maszyny wirtualnej, otwórz port 80 z Internetu za pomocą polecenia [az vm open-port](/cli/azure/vm#open-port):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Test maszyny Wirtualnej i NGINX
Teraz możesz otworzyć przeglądarkę internetową i wprowadzić ciąg `http://publicIpAddress` na pasku adresu. Podaj własny publiczny adres IP z procesu tworzenia maszyny wirtualnej. Jak w poniższym przykładzie zostanie wyświetlona strona NGINX domyślne:

![Domyślna witryna serwera NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Kolejne kroki
W tym przykładzie użyto pakujący do utworzenia obrazu maszyny Wirtualnej z NGINX już zainstalowana. Można tego obrazu maszyny Wirtualnej będą widoczne obok istniejących przepływów pracy wdrażania, takie jak wdrożyć aplikację na maszyny wirtualne utworzone na podstawie obrazu z Ansible, Chef lub Puppet.

Dla szablonów pakujący dodatkowe przykładowe dla innych dystrybucjach systemu Linux, zobacz [tego repozytorium GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).