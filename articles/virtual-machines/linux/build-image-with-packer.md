---
title: Tworzenie obrazów maszyn wirtualnych platformy Linux za pomocą usługi Packer
description: Dowiedz się, jak używać programu Packer do tworzenia obrazów maszyn wirtualnych systemu Linux na platformie Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.openlocfilehash: 3aec50b8c8f2033b7340bde15ea7670c1a0b6bb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534223"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Jak używać programu Packer do tworzenia obrazów maszyn wirtualnych systemu Linux na platformie Azure
Każda maszyna wirtualna (VM) na platformie Azure jest tworzona na podstawie obrazu definiujące dystrybucję systemu Linux i wersję systemu operacyjnego. Obrazy mogą zawierać wstępnie zainstalowane aplikacje i konfiguracje. Portal Azure Marketplace udostępnia wiele obrazów pierwszej i innej firmy dla większości typowych dystrybucji i środowisk aplikacji lub można tworzyć własne obrazy niestandardowe dostosowane do twoich potrzeb. W tym artykule opisano, jak używać narzędzia open source [Packer](https://www.packer.io/) do definiowania i tworzenia obrazów niestandardowych na platformie Azure.

> [!NOTE]
> Platforma Azure ma teraz usługę Azure Image Builder (preview), która do definiowania i tworzenia własnych obrazów niestandardowych. Usługa Azure Image Builder jest oparta na programie Packer, dzięki czemu można nawet używać istniejących skryptów aprowizacji powłoki Packer. Aby rozpocząć korzystanie z usługi Azure Image Builder, zobacz [Tworzenie maszyny Wirtualnej systemu Linux za pomocą usługi Azure Image Builder](image-builder.md).


## <a name="create-azure-resource-group"></a>Tworzenie grupy zasobów platformy Azure
Podczas procesu kompilacji Packer tworzy tymczasowe zasoby platformy Azure podczas tworzenia źródłowej maszyny Wirtualnej. Aby przechwycić tę źródłową maszynę wirtualną do użycia jako obraz, należy zdefiniować grupę zasobów. Dane wyjściowe z procesu kompilacji packer jest przechowywany w tej grupie zasobów.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Tworzenie poświadczeń platformy Azure
Packer uwierzytelnia się za pomocą platformy Azure przy użyciu jednostki usługi. Podmiot usługi platformy Azure to tożsamość zabezpieczeń, której można używać z aplikacjami, usługami i narzędziami automatyzacji, takimi jak Packer. Można kontrolować i zdefiniować uprawnienia, jakie operacje podmiotu zabezpieczeń usługi można wykonać na platformie Azure.

Utwórz jednostkę usługi za pomocą [az ad sp create-for-rbac](/cli/azure/ad/sp) i wytwórz poświadczenia, których potrzebuje Packer:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Przykład danych wyjściowych z poprzednich poleceń jest następujący:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Aby uwierzytelnić się na platformie Azure, musisz również uzyskać identyfikator subskrypcji platformy Azure przy użyciu [konta az show:](/cli/azure/account)

```azurecli
az account show --query "{ subscription_id: id }"
```

Dane wyjściowe z tych dwóch poleceń są używane w następnym kroku.


## <a name="define-packer-template"></a>Zdefiniuj szablon Pakowacza
Aby utworzyć obrazy, należy utworzyć szablon jako plik JSON. W szablonie można zdefiniować konstruktorów i inicjantów, które wykonują rzeczywisty proces kompilacji. Packer ma [aprowizacji dla platformy Azure,](https://www.packer.io/docs/builders/azure.html) który umożliwia definiowanie zasobów platformy Azure, takich jak poświadczenia jednostki usługi utworzone w poprzednim kroku.

Utwórz plik o nazwie *ubuntu.json* i wklej następującą zawartość. Wprowadź własne wartości dla następujących elementów:

| Parametr                           | Gdzie można uzyskać |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Pierwszy wiersz wyjścia `az ad sp` z polecenia Create - *appId* |
| *client_secret*                     | Drugi wiersz wyjścia `az ad sp` z polecenia Create - *hasło* |
| *tenant_id*                         | Trzeci wiersz wyjścia `az ad sp` z polecenia Create - *tenant* |
| *subscription_id*                   | Wyjście `az account show` z polecenia |
| *managed_image_resource_group_name* | Nazwa grupy zasobów utworzonej w pierwszym kroku |
| *managed_image_name*                | Nazwa utworzonego obrazu dysku zarządzanego |


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

Ten szablon tworzy obraz Ubuntu 16.04 LTS, instaluje NGINX, a następnie usuwa aprowizje maszyny Wirtualnej.

> [!NOTE]
> Jeśli rozwiniesz ten szablon, aby aprowizować poświadczenia użytkownika, dostosuj polecenie `-deprovision` inicjanta, które usuwa aprowizywanie agenta platformy Azure do odczytu, `deprovision+user`a nie .
> Flaga `+user` usuwa wszystkie konta użytkowników ze źródłowej maszyny Wirtualnej.


## <a name="build-packer-image"></a>Tworzenie obrazu packera
Jeśli nie masz jeszcze zainstalowanego packera na komputerze lokalnym, [postępuj zgodnie z instrukcjami instalacji Packera.](https://www.packer.io/docs/install/index.html)

Skompiluj obraz, określając plik szablonu programu Packer w następujący sposób:

```bash
./packer build ubuntu.json
```

Przykład danych wyjściowych z poprzednich poleceń jest następujący:

```output
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

Trwa kilka minut dla packer do tworzenia maszyny Wirtualnej, uruchomić inicjatory i oczyścić wdrożenia.


## <a name="create-vm-from-azure-image"></a>Tworzenie maszyny wirtualnej z usługi Azure Image
Teraz można utworzyć maszynę wirtualną z obrazu z [az vm create](/cli/azure/vm). Określ obraz utworzony `--image` za pomocą parametru. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z *myPackerImage* i generuje klucze SSH, jeśli jeszcze nie istnieją:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Jeśli chcesz utworzyć maszyny wirtualne w innej grupie zasobów lub regionie niż obraz Packer, określ identyfikator obrazu, a nie nazwę obrazu. Identyfikator obrazu można uzyskać za pomocą [programu az image show](/cli/azure/image#az-image-show).

Utworzenie maszyny Wirtualnej zajmuje kilka minut. Po utworzeniu maszyny Wirtualnej należy `publicIpAddress` wziąć pod uwagę wyświetlane przez interfejsu wiersza polecenia platformy Azure. Ten adres jest używany do uzyskiwania dostępu do witryny NGINX za pośrednictwem przeglądarki internetowej.

Aby zezwolić na ruch internetowy do maszyny wirtualnej, otwórz port 80 z Internetu za pomocą polecenia [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Test VM i NGINX
Teraz możesz otworzyć przeglądarkę internetową i wprowadzić ciąg `http://publicIpAddress` na pasku adresu. Podaj własny publiczny adres IP z procesu tworzenia maszyny wirtualnej. Domyślna strona NGINX jest wyświetlana tak, jak w poniższym przykładzie:

![Domyślna witryna serwera NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Następne kroki
Można również użyć istniejących skryptów aprowizacji programu Packer za pomocą [usługi Azure Image Builder](image-builder.md).
