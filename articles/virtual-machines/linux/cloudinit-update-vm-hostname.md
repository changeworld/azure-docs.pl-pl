---
title: Użyj pakietu cloud-init, aby ustawić nazwę hosta dla maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak dostosować Maszynę wirtualną systemu Linux podczas tworzenia przy użyciu wiersza polecenia platformy Azure za pomocą pakietu cloud-init
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1e437200ec6af22d104f9878e7bdfd20141759fb
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668201"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Użyj pakietu cloud-init, aby ustawić nazwę hosta dla maszyny Wirtualnej z systemem Linux na platformie Azure
W tym artykule dowiesz się, jak używać [pakietu cloud-init](https://cloudinit.readthedocs.io) skonfigurować określonej nazwy hosta na maszynie wirtualnej lub maszyny wirtualnej (VM) zestawów skalowania (zestawu skalowania maszyn wirtualnych) na inicjowanie obsługi administracyjnej czas na platformie Azure. Skrypty te pakietu cloud-init są uruchamiane podczas pierwszego rozruchu po zasoby zostały udostępnione przez platformę Azure. Aby uzyskać więcej informacji o tym, jak pakietu cloud-init działa natywnie na platformie Azure i obsługiwane dystrybucje systemu Linux, zobacz [Omówienie pakietu cloud-init](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Ustaw nazwę hosta, za pomocą pakietu cloud-init
Domyślnie, nazwa hosta jest taka sama jak nazwa maszyny Wirtualnej podczas tworzenia nowej maszyny wirtualnej na platformie Azure.  Aby uruchomić skrypt pakietu cloud-init można zmienić tej domyślnej nazwy hosta, podczas tworzenia maszyny Wirtualnej na platformie Azure przy użyciu [tworzenie az vm](/cli/azure/vm), określ plik cloud-init za pomocą `--custom-data` przełącznika.  

Aby zobaczyć proces uaktualniania w akcji, Utwórz plik w bieżącej powłoce o nazwie *cloud_init_hostname.txt* i Wklej poniższą konfigurację. W tym przykładzie należy utworzyć plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_hostname.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że poprawnie skopiować pliku całego pakietu cloud-init szczególnie pierwszy wiersz.  

```yaml
#cloud-config
hostname: myhostname
```

Przed wdrożeniem tego obrazu, należy utworzyć grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz maszynę Wirtualną z [tworzenie az vm](/cli/azure/vm) i określ plik cloud-init za pomocą `--custom-data cloud_init_hostname.txt` w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Po utworzeniu wiersza polecenia platformy Azure zawiera informacje o maszynie Wirtualnej. Użyj `publicIpAddress` SSH z maszyną wirtualną. Podaj własny adres w następujący sposób:

```bash
ssh <publicIpAddress>
```

Aby wyświetlić nazwę maszyny Wirtualnej, użyj `hostname` polecenia w następujący sposób:

```bash
hostname
```

Maszyna wirtualna powinien wysyłać raporty nazwy hosta, jako że wartości ustawione w pliku cloud-init, jak pokazano w następujących przykładowych danych wyjściowych:

```bash
myhostname
```

## <a name="next-steps"></a>Następne kroki
Przykłady dodatkowe pakietu cloud-init zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety podczas pierwszego rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizować pliki konfiguracji i wstawić kluczy](tutorial-automate-vm-deployment.md)
