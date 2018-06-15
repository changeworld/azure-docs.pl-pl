---
title: Użyj init chmury, aby ustawić nazwę hosta dla maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dostosowywanie maszyny Wirtualnej systemu Linux podczas tworzenia 2.0 interfejsu wiersza polecenia platformy Azure przy użyciu init chmury
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: a858a12ec81db7ae1c0a7b7cfea06fa2abdcdcc6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29124028"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Użyj init chmury, aby ustawić nazwę hosta dla maszyny Wirtualnej systemu Linux na platformie Azure
W tym artykule przedstawiono sposób użycia [init chmury](https://cloudinit.readthedocs.io) skonfigurować określonej nazwy hosta na maszynie wirtualnej lub maszyny wirtualnej (VM) zestawach skali (VMSS) na inicjowanie obsługi administracyjnej czas na platformie Azure. Skrypty te init chmury są uruchamiane po pierwszym uruchomieniu komputera po zasoby zostały udostępnione przez platformę Azure. Aby uzyskać więcej informacji na temat chmury inicjowania działania natywnie Azure i obsługiwanych dystrybucjach systemu Linux, zobacz [init chmury — omówienie](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Ustaw nazwę hosta z inicjowaniem chmury
Domyślnie nazwa hosta jest taka sama jak nazwa maszyny Wirtualnej podczas tworzenia nowej maszyny wirtualnej na platformie Azure.  Do uruchomienia skryptu init chmury można zmienić tej nazwy hosta domyślnego podczas tworzenia maszyny Wirtualnej na platformie Azure z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create), określ plik init chmury z `--custom-data` przełącznika.  

Aby wyświetlić procesu uaktualniania w akcji, należy utworzyć plik w bieżącym powłoki o nazwie *cloud_init_hostname.txt* i wklej następującą konfigurację. Na przykład można utworzyć pliku w powłoce chmury nie na komputerze lokalnym. Można użyć dowolnego edytora, którego chcesz. Wprowadź `sensible-editor cloud_init_hostname.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że poprawnie skopiować pliku całego init chmury szczególnie pierwszego wiersza.  

```yaml
#cloud-config
hostname: myhostname
```

Przed wdrożeniem tego obrazu, należy utworzyć nową grupę zasobów o [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz, utwórz maszynę Wirtualną z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) i określ plik init chmury z `--custom-data cloud_init_hostname.txt` w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Po utworzeniu interfejsu wiersza polecenia Azure zawiera informacje o maszynie Wirtualnej. Użyj `publicIpAddress` do SSH do maszyny Wirtualnej. Wprowadź własne adres e-mail w następujący sposób:

```bash
ssh <publicIpAddress>
```

Aby wyświetlić nazwę maszyny Wirtualnej, użyj `hostname` polecenia w następujący sposób:

```bash
hostname
```

Maszyna wirtualna Zgłoś nazwa hosta jako tego wartość ustawiona w pliku chmury init, jak pokazano w poniższym przykładzie danych wyjściowych:

```bash
myhostname
```

## <a name="next-steps"></a>Kolejne kroki
Przykłady dodatkowe init chmury zmian konfiguracji zobacz następujące tematy:
 
- [Dodaj dodatkowe użytkownika w systemie Linux na maszynie Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety po pierwszym uruchomieniu komputera](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i wstrzyknąć kluczy](tutorial-automate-vm-deployment.md)