---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: a4c9ec133b3686a92cec7e7c8d4552c1302e3074
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158525"
---
Aby móc używać interfejsu wiersza polecenia platformy Azure oraz szablonów i poleceń usługi Resource Manager w celu wdrażania obciążeń i zasobów platformy Azure przy użyciu grup zasobów, potrzebne jest konto platformy Azure. Jeśli nie posiadasz konta, możesz skorzystać z [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

Jeśli jeszcze nie zainstalowano interfejsu wiersza polecenia platformy Azure i nie nawiązano połączenia z subskrypcją, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](../articles/cli-install-nodejs.md), ustaw tryb na wartość `arm` przy użyciu polecenia `azure config mode arm` i nawiąż połączenie z platformą Azure przy użyciu polecenia `azure login`.

## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- Klasyczny interfejs wiersza polecenia Azure — Nasz interfejs wiersza polecenia dla klasycznego i zasobów zarządzania modeli wdrażania (w tym artykule)
- [Interfejs wiersza polecenia Azure](../articles/virtual-machines/linux/cli-manage.md) — naszej nowej generacji interfejsu wiersza polecenia dla modelu wdrażania usługi resource management

## <a name="basic-azure-resource-manager-commands-in-azure-classic-cli"></a>Podstawowe polecenia usługi Azure Resource Manager w klasycznym wiersza polecenia platformy Azure

W tym artykule omówiono podstawowe polecenia, które mają za pomocą klasycznego wiersza polecenia platformy Azure do zarządzania i interakcji z zasobami (głównie maszynami wirtualnymi) w ramach subskrypcji platformy Azure.  Aby uzyskać bardziej szczegółową pomoc dotyczącą konkretnych przełączników wiersza polecenia i opcji, możesz użyć pomocy online dotyczącej poleceń i opcji, wpisując polecenie `azure <command> <subcommand> --help` lub `azure help <command> <subcommand>`.

> [!NOTE]
> Te przykłady nie obejmują operacji opartych na szablonach, które są zazwyczaj zalecane w przypadku wdrożeń maszyn wirtualnych w usłudze Resource Manager. Aby uzyskać informacje, zobacz [Używanie interfejsu wiersza polecenia platformy Azure z usługą Resource Manager](../articles/xplat-cli-azure-resource-manager.md) i [Wdrażanie maszyn wirtualnych i zarządzanie nimi przy użyciu szablonów usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

| Zadanie | Resource Manager |
| --- | --- |
| Tworzenie podstawowej maszyny wirtualnej |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Uzyskaj element `image-urn` z polecenia `azure vm image list`. Zapoznaj się z przykładami w [tym artykule](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). |
| Tworzenie maszyny wirtualnej z systemem Linux |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| Tworzenie maszyny wirtualnej z systemem Windows |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| Wyświetlanie listy maszyn wirtualnych |`azure  vm list [options]` |
| Uzyskiwanie informacji o maszynie wirtualnej |`azure  vm show [options] <resource_group> <name>` |
| Uruchamianie maszyny wirtualnej |`azure vm start [options] <resource_group> <name>` |
| Zatrzymywanie maszyny wirtualnej |`azure vm stop [options] <resource_group> <name>` |
| Cofanie przydziału maszyny wirtualnej |`azure vm deallocate [options] <resource-group> <name>` |
| Ponowne uruchamianie maszyny wirtualnej |`azure vm restart [options] <resource_group> <name>` |
| Usuwanie maszyny wirtualnej |`azure vm delete [options] <resource_group> <name>` |
| Przechwytywanie maszyny wirtualnej |`azure vm capture [options] <resource_group> <name>` |
| Tworzenie maszyny wirtualnej na podstawie obrazu użytkownika |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanego dysku |`azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| Dodawanie dysku danych do maszyny wirtualnej |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| Usuwanie dysku danych z maszyny wirtualnej |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| Dodawanie rozszerzenia ogólnego do maszyny wirtualnej |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Dodawanie do maszyny wirtualnej rozszerzenia dostępu do maszyny wirtualnej |`azure vm reset-access [options] <resource-group> <name>` |
| Dodawanie rozszerzenia platformy Docker do maszyny wirtualnej |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| Usuwanie rozszerzenia maszyny wirtualnej |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Używanie zasobów maszyny wirtualnej |`azure vm list-usage [options] <location>` |
| Pobieranie wszystkich rozmiarów maszyn wirtualnych |`azure vm sizes [options]` |

## <a name="next-steps"></a>Kolejne kroki
* Aby zapoznać się w dodatkowymi przykładami poleceń interfejsu wiersza polecenia wykraczającymi poza podstawy zarządzana maszynami wirtualnymi, zobacz [Używanie interfejsu wiersza polecenia platformy Azure z usługą Azure Resource Manager](../articles/virtual-machines/azure-cli-arm-commands.md).
