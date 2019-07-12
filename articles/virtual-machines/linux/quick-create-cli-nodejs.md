---
title: Tworzenie maszyny Wirtualnej systemu Linux przy użyciu platformy Azure klasyczny interfejs wiersza polecenia | Dokumentacja firmy Microsoft
description: Tworzenie maszyny Wirtualnej z systemem Linux na platformie Azure przy użyciu platformy Azure klasyczny interfejs wiersza polecenia
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: gwallace
editor: ''
ms.assetid: facb1115-2b4e-4ef3-9905-330e42beb686
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2016
ms.author: v-livech
ms.openlocfilehash: a757e156d48eb27a9e4d9b38cb08d5417f1e35b6
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671072"
---
# <a name="create-a-linux-vm-using-the-azure-classic-cli"></a>Tworzenie maszyny Wirtualnej systemu Linux przy użyciu platformy Azure klasyczny interfejs wiersza polecenia

Ten artykuł pokazuje, jak szybko wdrożyć maszynę wirtualną systemu Linux na platformie Azure przy użyciu polecenia `azure vm quick-create` w interfejsie wiersza polecenia (CLI) platformy Azure. Polecenie `quick-create` umożliwia wdrożenie maszyny wirtualnej wewnątrz podstawowej, bezpiecznej infrastruktury, której można używać do prototypowania lub szybkiego testowania koncepcji.

> [!NOTE]
> Aby utworzyć Maszynę wirtualną przy użyciu wiersza polecenia platformy Azure, zobacz [Utwórz Maszynę wirtualną przy użyciu wiersza polecenia platformy Azure](../windows/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Szybkie wdrożenie maszyny wirtualnej z systemem Linux jest możliwe również przy użyciu witryny [Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W artykule [SSH pliki publicznych i prywatnych kluczy](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="quick-commands"></a>Szybkie polecenia

Poniższy przykład pokazuje, jak wdrożyć maszynę Wirtualną CoreOS i dołączyć klucz Secure Shell (SSH) (argumenty mogą się różnić).

```azurecli
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik

W następującym przewodniku krok po kroku wdrażana jest maszyna wirtualna z systemem UbuntuLTS. Przy każdym kroku wyjaśnione jest, co dany krok robi.

## <a name="vm-quick-create-aliases"></a>Aliasy szybkiego tworzenia maszyny wirtualnej

Szybkim sposobem wyboru dystrybucji jest użycie aliasów interfejsu wiersza polecenia (CLI) platformy Azure przypisanych najbardziej typowym dystrybucjom systemu operacyjnego. Poniższa tabela zawiera listę aliasów dystrybucji (stan dla interfejsu wiersza polecenia platformy Azure w wersji 0.10). Wszystkie wdrożenia, które używają polecenia `quick-create`, są domyślnie maszynami wirtualnymi na dyskach półprzewodnikowych (SSD), zapewniających szybszą aprowizację oraz wyższą wydajność dostępu do dysku. (Te aliasy stanowią niewielką część dystrybucji dostępnych na platformie Azure. Więcej obrazów można znaleźć w witrynie Azure Marketplace, [wyszukując obraz w programie PowerShell](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [w sieci Web](https://azure.microsoft.com/marketplace/virtual-machines/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) albo [przesyłając własny obraz niestandardowy](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

| Alias | Wydawca | Oferta | SKU | Version |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7.2 |najnowsza |
| CoreOS |CoreOS |CoreOS |Stable |najnowsza |
| Debian |credativ |Debian |8 |najnowsza |
| openSUSE |SUSE |openSUSE |13.2 |najnowsza |
| RHEL |Red Hat |RHEL |7.2 |najnowsza |
| UbuntuLTS |Canonical |Ubuntu Server |14.04.4-LTS |najnowsza |

W poniższych sekcjach użyto aliasu `UbuntuLTS` dla opcji **ImageURN** (`-Q`) w celu wdrożenia w systemie Ubuntu 14.04.4 LTS Server.

W poprzednim przykładzie polecenie `quick-create` wywoływało tylko flagę `-M` w celu identyfikacji klucza publicznego SSH do przesłania po wyłączeniu haseł SSH, więc zostanie wyświetlony monit o wprowadzenie następujących argumentów:

* nazwę grupy zasobów (dla pierwszej grupy zasobów platformy Azure jest zazwyczaj odpowiedni dowolny ciąg)
* Nazwa maszyny wirtualnej
* lokalizacja (dobre wartości domyślne to `westus` lub `westeurope`)
* linux (wskazanie platformie Azure pożądanego systemu operacyjnego)
* username

W poniższym przykładzie określono wszystkie wartości, dzięki czemu żadne dalsze monity nie są wymagane. Plik klucza publicznego w formacie ssh-rsa `~/.ssh/id_rsa.pub` działa prawidłowo.

```azurecli
azure vm quick-create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --admin-username myAdminUser \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --image-urn UbuntuLTS
```

Dane wyjściowe powinny wyglądać podobnie do następującego bloku:

```azurecli
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Logowanie się do nowej maszyny wirtualnej
Zaloguj się do maszyny wirtualnej, używając publicznego adresu IP podanego w danych wyjściowych. Można również użyć podanej w pełni kwalifikowanej nazwy domeny (FQDN):

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

Proces logowania powinien wyglądać podobnie do następującego bloku danych wyjściowych:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Kolejne kroki
Polecenie `azure vm quick-create` jest szybkim sposobem wdrożenia maszyny wirtualnej, dzięki któremu możesz zalogować się do powłoki bash i rozpocząć pracę. Jednak użycie polecenia `vm quick-create` nie zapewnia rozbudowanej kontroli ani nie umożliwia tworzenia bardziej złożonego środowiska.  Aby wdrożyć maszynę wirtualną z systemem Linux dostosowaną do swojej infrastruktury, możesz skorzystać z dowolnego z poniższych artykułów:

* [Tworzenie niestandardowego środowiska dla maszyny wirtualnej z systemem Linux poprzez bezpośrednie użycie poleceń interfejsu wiersza polecenia platformy Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny wirtualnej z systemem Linux zabezpieczonej przez protokół SSH na platformie Azure przy użyciu szablonów](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Można również [użyć sterownika platformy Azure `docker-machine` z różnymi poleceniami do szybkiego utworzenia maszyny wirtualnej systemu Linux jako hosta docker](docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
