---
title: Używanie funkcji cloud-init do ustawiania nazwy hosta dla maszyny Wirtualnej z systemem Linux
description: Jak używać cloud-init do dostosowywania maszyny Wirtualnej systemu Linux podczas tworzenia za pomocą interfejsu wiersza polecenia platformy Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 631b8ef83d5fbf10ec401df7432b23238f2ae2e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969163"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Ustawianie nazwy hosta dla maszyny Wirtualnej z systemem Linux na platformie Azure za pomocą funkcji cloud-init
W tym artykule pokazano, jak używać [cloud-init](https://cloudinit.readthedocs.io) do konfigurowania określonej nazwy hosta na maszynie wirtualnej (VM) lub zestawów skalowania maszyny wirtualnej (VMSS) w czasie inicjowania obsługi administracyjnej na platformie Azure. Te skrypty init w chmurze są uruchamiane przy pierwszym rozruchu po zainicjowaniu zasobów przez platformę Azure. Aby uzyskać więcej informacji na temat działania cloud-init na platformie Azure i obsługiwanych dystrybucji systemu Linux, zobacz [omówienie cloud-init](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Ustawianie nazwy hosta za pomocą cloud-init
Domyślnie nazwa hosta jest taka sama jak nazwa maszyny Wirtualnej podczas tworzenia nowej maszyny wirtualnej na platformie Azure.  Aby uruchomić skrypt init w chmurze, aby zmienić tę domyślną nazwę hosta podczas tworzenia maszyny Wirtualnej `--custom-data` na platformie Azure z [az vm create](/cli/azure/vm), określ plik init w chmurze za pomocą przełącznika.  

Aby wyświetlić proces uaktualniania w działaniu, utwórz plik w bieżącej powłoce o nazwie *cloud_init_hostname.txt* i wklej następującą konfigurację. W tym przykładzie utwórz plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_hostname.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **edytora nano.** Upewnij się, że cały plik cloud-init jest kopiowany poprawnie, zwłaszcza w pierwszym wierszu.  

```yaml
#cloud-config
hostname: myhostname
```

Przed wdrożeniem tego obrazu należy utworzyć grupę zasobów za pomocą polecenia [tworzenie grupy az.](/cli/azure/group) Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz utwórz maszynę wirtualną z [az vm utworzyć](/cli/azure/vm) `--custom-data cloud_init_hostname.txt` i określić plik cloud-init w następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Po utworzeniu interfejsu wiersza polecenia platformy Azure zawiera informacje o maszynie Wirtualnej. Użyj `publicIpAddress` do SSH do maszyny Wirtualnej. Wpisz swój własny adres w następujący sposób:

```bash
ssh <publicIpAddress>
```

Aby wyświetlić nazwę maszyny `hostname` Wirtualnej, użyj polecenia w następujący sposób:

```bash
hostname
```

Maszyna wirtualna powinna zgłosić nazwę hosta jako tę wartość ustawioną w pliku cloud-init, jak pokazano w poniższym przykładzie danych wyjściowych:

```bash
myhostname
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady zmian konfiguracji w chmurze, zobacz następujące elementy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchamianie menedżera pakietów w celu zaktualizowania istniejących pakietów przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmienianie nazwy hosta lokalnego maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Instalowanie pakietu aplikacji, aktualizowanie plików konfiguracyjnych i wstrzykiwanie kluczy](tutorial-automate-vm-deployment.md)
