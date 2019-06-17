---
title: Tworzenie obrazów maszyn wirtualnych platformy Azure z systemem Linux za pomocą usługi Packer | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć obrazy maszyn wirtualnych systemu Linux na platformie Azure za pomocą usługi Packer
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.openlocfilehash: c0ec2616d8bdcf3cfd6d649f12e9bfceea33690a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65467745"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Jak utworzyć obrazy maszyn wirtualnych z systemem Linux na platformie Azure za pomocą usługi Packer
Każda maszyna wirtualna (VM) na platformie Azure jest tworzony z obrazu, który definiuje dystrybucji systemu Linux i wersji systemu operacyjnego. Obrazy mogą obejmować wstępnie zainstalowane aplikacje i konfiguracje. W portalu Azure Marketplace udostępnia wiele obrazów pierwszy i innych firm dla dystrybucje najbardziej typowe i środowiska aplikacji, lub możesz utworzyć własne niestandardowe obrazy dopasowany do Twoich potrzeb. Ten artykuł szczegółowo opisuje sposób użycia narzędzia typu open source [Packer](https://www.packer.io/) do definiowania i tworzenie niestandardowych obrazów na platformie Azure.

> [!NOTE]
> Platforma Azure zapewnia teraz usługę, Kreator obrazów platformy Azure (wersja zapoznawcza), definiowanie i tworzenie własnych niestandardowych obrazów. Kreator obrazów platformy Azure w oparciu Packer, więc nawet za pomocą istniejących skryptów administracyjnej powłoki usługi Packer go. Aby rozpocząć korzystanie z Kreatora obrazów platformy Azure, zobacz [tworzenie maszyny Wirtualnej z systemem Linux przy użyciu Kreatora obrazów platformy Azure](image-builder.md).


## <a name="create-azure-resource-group"></a>Utwórz grupę zasobów platformy Azure
Podczas procesu kompilacji narzędzia Packer tworzy tymczasowy zasobów platformy Azure opiera się źródłowa maszyna wirtualna. Aby przechwycić tego źródła do użycia jako obraz maszyny Wirtualnej, należy zdefiniować grupy zasobów. Dane wyjściowe z procesu kompilacji narzędzia Packer znajduje się w tej grupie zasobów.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure
Narzędzia packer uwierzytelnia się za pomocą platformy Azure przy użyciu nazwy głównej usługi. Jednostka usługi platformy Azure to tożsamość zabezpieczeń używanej z aplikacjami, usługami i narzędzia automatyzacji, takie jak usługi Packer. Ty określasz i zdefiniować uprawnienia dotyczące jakie operacje nazwy głównej usługi można wykonać na platformie Azure.

Tworzenie usługi podmiotu zabezpieczeń za pomocą [az ad sp create-for-rbac](/cli/azure/ad/sp) i dane wyjściowe poświadczenia, których potrzebuje Packer:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Przykład danych wyjściowych z poprzedniego polecenia jest następująca:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Aby uwierzytelniać się na platformie Azure, trzeba będzie również Uzyskaj identyfikator subskrypcji platformy Azure za pomocą [Pokaż konta az](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

Dane wyjściowe z tych dwóch poleceń można użyć w następnym kroku.


## <a name="define-packer-template"></a>Definiowanie szablonu usługi Packer
Aby skompilować obrazy, Utwórz szablon w formacie JSON. W szablonie i są definiowane Konstruktorzy provisioners wykonujących procesu rzeczywistej kompilacji. Zawiera narzędzia packer [administracyjnej dla platformy Azure](https://www.packer.io/docs/builders/azure.html) , pozwala na zdefiniowanie zasobów platformy Azure, takich jak poświadczenia nazwy głównej usługi, utworzony w poprzednim kroku.

Utwórz plik o nazwie *ubuntu.json* i wklej następującą zawartością. Wprowadź własne wartości dla następujących elementów:

| Parametr                           | Gdzie można uzyskać |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Pierwszy wiersz danych wyjściowych z `az ad sp` utworzyć polecenie - *appId* |
| *client_secret*                     | W wierszu danych wyjściowych z drugim `az ad sp` utworzyć polecenie - *hasła* |
| *tenant_id*                         | Trzeci wiersz danych wyjściowych z `az ad sp` utworzyć polecenie - *dzierżawy* |
| *subscription_id*                   | Dane wyjściowe z `az account show` polecenia |
| *managed_image_resource_group_name* | Nazwa grupy zasobów, utworzonego w pierwszym kroku |
| *managed_image_name*                | Nazwa obrazu dysku zarządzanego, który jest tworzony |


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

Ten szablon tworzy obraz Ubuntu 16.04 LTS, instaluje serwer NGINX, a następnie deprovisions maszyny Wirtualnej.

> [!NOTE]
> Po rozwinięciu na tym szablonie do poświadczeń użytkownika należy dostosować polecenia administracyjnej deprovisions agenta platformy Azure do odczytu `-deprovision` zamiast `deprovision+user`.
> `+user` Flagi usuwa wszystkie konta użytkowników ze źródłowej maszyny Wirtualnej.


## <a name="build-packer-image"></a>Tworzenie obrazu usługi Packer
Jeśli nie masz jeszcze usługi Packer zainstalowane na komputerze lokalnym [postępuj zgodnie z instrukcjami instalacji narzędzia Packer](https://www.packer.io/docs/install/index.html).

Tworzenie obrazu, określając usługi Packer pliku szablonu w następujący sposób:

```bash
./packer build ubuntu.json
```

Przykład danych wyjściowych z poprzedniego polecenia jest następująca:

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

Trwa kilka minut, zanim Packer tworzenie maszyny Wirtualnej, uruchom provisioners i wyczyścić wdrożenia.


## <a name="create-vm-from-azure-image"></a>Tworzenie maszyny Wirtualnej na podstawie obrazów systemu Azure
Teraz można utworzyć Maszynę wirtualną z obrazu za pomocą [tworzenie az vm](/cli/azure/vm). Określ obraz, który został utworzony z `--image` parametru. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* z *myPackerImage* i generowanie kluczy SSH, jeśli jeszcze nie istnieje:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Jeśli chcesz utworzyć maszyny wirtualne w regionie niż obrazu usługi Packer lub innej grupy zasobów, należy określić identyfikator obrazu, a nie nazwę obrazu. Można uzyskać identyfikator obrazu z [az image show](/cli/azure/image#az-image-show).

Trwa kilka minut, aby utworzyć maszynę Wirtualną. Po utworzeniu maszyny Wirtualnej Zanotuj `publicIpAddress` wyświetlane w interfejsie wiersza polecenia platformy Azure. Ten adres jest używany na dostęp do witryny serwera NGINX za pośrednictwem przeglądarki internetowej.

Aby zezwolić na ruch internetowy do maszyny wirtualnej, otwórz port 80 z Internetu za pomocą polecenia [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Test maszyny Wirtualnej i serwera NGINX
Teraz możesz otworzyć przeglądarkę internetową i wprowadzić ciąg `http://publicIpAddress` na pasku adresu. Podaj własny publiczny adres IP z procesu tworzenia maszyny wirtualnej. Jak w poniższym przykładzie jest wyświetlona domyślna strona serwera NGINX:

![Domyślna witryna serwera NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Kolejne kroki
Można również użyć istniejących skryptów administracyjnej narzędzia Packer za pomocą [kreatora obrazów platformy Azure](image-builder.md).
