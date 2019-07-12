---
title: Migrowanie maszyn wirtualnych do usługi Resource Manager przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł przeprowadzi migracji obsługiwanej przez platformę zasobów z wersji klasycznej do usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 0e21a962fb03a42af4cb32fcdf60cd59746a591d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667367"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure
Te kroki pokazują, jak używać poleceń interfejsu wiersza polecenia platformy Azure (CLI) do migracji infrastruktury jako zasoby usługi (IaaS) z klasycznego modelu wdrażania do modelu wdrażania usługi Azure Resource Manager. W artykule [klasycznego wiersza polecenia platformy Azure](../../cli-install-nodejs.md). Ponieważ wiersza polecenia platformy Azure ma zastosowanie tylko dla zasobów usługi Azure Resource Manager, nie można użyć dla tej migracji.

> [!NOTE]
> Wszystkie operacje, które są opisane w tym miejscu są idempotentne. Jeśli masz problem inny niż nieobsługiwana funkcja lub błąd konfiguracji, firma Microsoft zaleca ponów próbę wykonania przygotowania, przerwania lub zatwierdzenia operacji. Platforma spróbuj wykonać akcję ponownie.
> 
> 

<br>
W tym miejscu jest blokowy do identyfikowania zamówienia, w którym kroki należy wykonać podczas procesu migracji

![Zrzut ekranu przedstawiający kroki migracji](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Krok 1: Przygotowanie do migracji
Poniżej przedstawiono kilka najlepszych rozwiązań, które są zalecane, ponieważ oceny migracji zasobów IaaS z klasycznego do usługi Resource Manager:

* Zapoznaj się z artykułem [listą nieobsługiwanych konfiguracji lub funkcji](../windows/migration-classic-resource-manager-overview.md). W przypadku maszyn wirtualnych korzystających z nieobsługiwaną konfiguracją lub funkcji, firma Microsoft zaleca, poczekać do obsługi funkcji/konfiguracji do ogłoszenia. Alternatywnie można usunąć tej funkcji, lub wychodzenia tę konfigurację, aby umożliwić migrację, jeśli jego odpowiada Twoim potrzebom.
* Jeśli zautomatyzowano skrypty, które już dziś wdrażanie infrastruktury i aplikacji, spróbuj utworzyć podobnej konfiguracji testu za pomocą tych skryptów migracji. Alternatywnie możesz skonfigurować przykładowych środowisk przy użyciu witryny Azure portal.

> [!IMPORTANT]
> Bramy aplikacji nie są obecnie obsługiwane dla migracji z wersji klasycznej do usługi Resource Manager. Aby przeprowadzić migrację klasycznej sieci wirtualnej z bramą aplikacji, należy usunąć bramę przed uruchomieniem operacji przygotowania, aby przenieść sieć. Po zakończeniu migracji należy ponownie nawiąż połączenie bramy usługi Azure Resource Manager. 
>
>Nie można zmigrować automatycznie bramy usługi ExpressRoute, nawiązywania połączenia z obwodów usługi ExpressRoute w innej subskrypcji. W takiej sytuacji należy usunąć bramę usługi ExpressRoute, migrację sieci wirtualnej i ponownie utworzyć bramę. Zobacz [migracji usługi ExpressRoute circuits i skojarzonych sieci wirtualnych z klasycznego modelu wdrażania usługi Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) Aby uzyskać więcej informacji.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Krok 2: Ustaw subskrypcję i zarejestrować dostawcę
Scenariusze migracji, należy ustawić konfigurowania środowiska na potrzeby modelach klasycznym i usługi Resource Manager. [Zainstaluj interfejs wiersza polecenia platformy Azure](../../cli-install-nodejs.md) i [wybierz swoją subskrypcję](/cli/azure/authenticate-azure-cli).

Zaloguj się do swojego konta.

    azure login

Wybierz subskrypcję platformy Azure przy użyciu następującego polecenia.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Rejestracja jest jeden krok czasu, ale trzeba zrobić, jeden raz przed próbą wykonania migracji. Bez rejestracji zobaczysz następujący komunikat o błędzie 
> 
> *Element BadRequest: Subskrypcja nie jest zarejestrowana do migracji.* 
> 
> 

Zarejestruj u dostawcy zasobów migracji za pomocą następującego polecenia. Należy pamiętać, że w niektórych przypadkach, to polecenie upłynie limit czasu. Jednakże rejestracja zakończy się pomyślnie.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Poczekaj pięć minut rejestracji zakończyć. Za pomocą następującego polecenia, możesz sprawdzić stan zatwierdzenia. Upewnij się, że RegistrationState `Registered` przed kontynuowaniem.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Teraz przełączać interfejsu wiersza polecenia do `asm` trybu.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 3: Upewnij się, że masz wystarczającej liczby procesorów wirtualnych maszyny wirtualnej usługi Resource Manager platformy Azure w regionie platformy Azure w bieżącym wdrożeniu lub sieci Wirtualnej
W tym kroku musisz przełączyć się do `arm` trybu. W tym za pomocą następującego polecenia.

```
azure config mode arm
```

Można użyć następującego polecenia interfejsu wiersza polecenia, aby sprawdzić bieżącą liczbę procesorów wirtualnych, które masz w usłudze Azure Resource Manager. Aby dowiedzieć się więcej na temat limitów przydziału procesorów wirtualnych, zobacz [limity i usługi Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Po zakończeniu weryfikacji tego kroku, możesz przełączyć się ponownie do `asm` trybu.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Krok 4: Opcja 1 — Migrowanie maszyn wirtualnych w usłudze w chmurze
Pobierz listę usług w chmurze przy użyciu następującego polecenia, a następnie wybierz usługę w chmurze, które mają zostać zmigrowane. Należy pamiętać, że jeśli maszyny wirtualne w usłudze w chmurze znajdują się w sieci wirtualnej lub jeśli są dostępne role sieć web/proces roboczy, zostanie wyświetlony komunikat o błędzie.

    azure service list

Uruchom następujące polecenie, aby uzyskać nazwę wdrożenia usługi w chmurze z pełne dane wyjściowe. W większości przypadków nazwa wdrożenia jest taka sama jak nazwa usługi w chmurze.

    azure service show <serviceName> -vv

Najpierw zweryfikuj, jeśli migrujesz usługę w chmurze przy użyciu następujących poleceń:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Przygotowywanie maszyn wirtualnych w usłudze w chmurze dla migracji. Dostępne są dwie opcje do wyboru.

Jeśli chcesz przeprowadzić migrację maszyn wirtualnych z siecią wirtualną utworzone przez platformę, użyj następującego polecenia.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Jeśli chcesz przeprowadzić migrację do istniejącej sieci wirtualnej w modelu wdrażania usługi Resource Manager, użyj następującego polecenia.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Po pomyślnym operacji przygotowania można przeszukać pełne dane wyjściowe, aby pobrać stan migracji maszyn wirtualnych i upewnij się, że są one w `Prepared` stanu.

    azure vm show <vmName> -vv

Sprawdź konfigurację dla zasobów przygotowanego przy użyciu interfejsu wiersza polecenia lub witryny Azure portal. Jeśli chcesz wrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia.

    azure service deployment abort-migration <serviceName> <deploymentName>

Jeśli przygotowany Konfiguracja wygląda dobrze, możesz przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Krok 4: Opcja 2 — Migrowanie maszyn wirtualnych w sieci wirtualnej
Wybierz sieć wirtualną, którą chcesz migrować. Należy pamiętać, że jeśli sieć wirtualna zawiera maszyny wirtualne lub role sieć web/proces roboczy z nieobsługiwaną konfiguracją, zostanie wyświetlony komunikat o błędzie weryfikacji.

Pobierz wszystkie sieci wirtualne w subskrypcji przy użyciu następującego polecenia.

    azure network vnet list

Dane wyjściowe będą wyglądać mniej więcej tak:

![Zrzut ekranu przedstawiający wiersza polecenia o nazwie całej sieci wirtualnej wyróżnione.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

W powyższym przykładzie **virtualNetworkName** jest cała nazwa **"Classicubuntu16 classicubuntu16 grupy"** .

Najpierw zweryfikuj, jeśli migrujesz sieć wirtualną przy użyciu następującego polecenia:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Przygotuj sieci wirtualnej w wybranej do migracji za pomocą następującego polecenia.

    azure network vnet prepare-migration <virtualNetworkName>

Sprawdź konfigurację dla przygotowanej maszyn wirtualnych przy użyciu interfejsu wiersza polecenia lub witryny Azure portal. Jeśli chcesz wrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia.

    azure network vnet abort-migration <virtualNetworkName>

Jeśli przygotowany Konfiguracja wygląda dobrze, możesz przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Krok 5. Migrację konta magazynu
Po zakończeniu migracji maszyn wirtualnych, zaleca się migrację konta magazynu.

Przygotowywanie konta magazynu do migracji za pomocą następującego polecenia

    azure storage account prepare-migration <storageAccountName>

Sprawdź konfigurację dla konta magazynu przygotowanego przy użyciu interfejsu wiersza polecenia lub witryny Azure portal. Jeśli chcesz wrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia.

    azure storage account abort-migration <storageAccountName>

Jeśli przygotowany Konfiguracja wygląda dobrze, możesz przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Następne kroki

* [Omówienie migracji obsługiwanej przez platformę zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager przy użyciu programu PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Narzędzia społeczności do ułatwiających migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Przejrzyj często zadawane pytania dotyczące migracji zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
