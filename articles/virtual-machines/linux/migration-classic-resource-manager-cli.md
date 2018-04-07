---
title: Migrowanie maszyn wirtualnych do Menedżera zasobów przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono za pośrednictwem obsługiwanych platform migracji zasobów z klasycznego do usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
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
ms.openlocfilehash: f986246e74305789eb2978a95fd6a3e51accd25e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migracja zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure
Te kroki pokazują, jak używać poleceń Azure interfejsu wiersza polecenia (CLI), aby migrować infrastruktury jako zasoby usługi (IaaS) z klasycznym modelu wdrażania modelu wdrażania usługi Azure Resource Manager. Wymaga artykułu [Azure CLI 1.0](../../cli-install-nodejs.md). Ponieważ 2.0 interfejsu wiersza polecenia Azure ma zastosowanie tylko dla zasobów usługi Azure Resource Manager, nie można użyć dla tej migracji.

> [!NOTE]
> Wszystkie czynności opisane w tym miejscu są idempotentności. Jeśli masz problem niż nieobsługiwanej funkcji lub błąd konfiguracji, zalecamy możesz ponowić próbę o przygotowaniu przerwania lub przekazania operacji. Platforma spróbuj wykonać akcję ponownie.
> 
> 

<br>
Oto schemat blokowy, aby określić kolejność kroków muszą być wykonywane podczas procesu migracji

![Zrzut ekranu przedstawiający kroki migracji](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Krok 1: Przygotowanie do migracji
Poniżej przedstawiono kilka najlepsze rozwiązania, które są zalecane jako ocenić Migrowanie zasobów IaaS z klasycznego do Menedżera zasobów:

* Zapoznaj się z artykułem [lista nieobsługiwanych konfiguracji lub funkcji](../windows/migration-classic-resource-manager-overview.md). Jeśli masz maszyn wirtualnych, które używają nieobsługiwane konfiguracje i funkcje, zaleca się poczekać do obsługi funkcji/konfiguracji zostaną ogłoszone. Alternatywnie można usunąć tej funkcji lub wychodzenia tej konfiguracji, aby umożliwić migrację, jeśli go odpowiada Twoim potrzebom.
* Jeśli mają automatyczny skrypty, które obecnie wdrażanie infrastruktury i aplikacji, spróbuj utworzyć podobnych konfiguracji testu przy użyciu tych skryptów do migracji. Można też skonfigurować próbkę środowiskami przy użyciu portalu Azure.

> [!IMPORTANT]
> Bramy aplikacji nie są obecnie obsługiwane dla migracji ze środowiska klasycznego do Menedżera zasobów. Aby przeprowadzić migrację z bramy aplikacji klasycznej sieci wirtualnej, należy usunąć bramę, przed uruchomieniem operacji Prepare, aby przenieść sieć. Po zakończeniu migracji należy ponownie podłączyć bramy w usłudze Azure Resource Manager. 
>
>Bram usługi ExpressRoute nawiązywania obwody usługi ExpressRoute w innej subskrypcji nie może być migrowane automatycznie. W takiej sytuacji należy usunąć bramę usługi ExpressRoute, migrację sieci wirtualnej i Utwórz ponownie bramę. Zobacz [ExpressRoute migracji obwody i skojarzone sieci wirtualne z klasycznego modelu wdrażania usługi Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) Aby uzyskać więcej informacji.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Krok 2: Ustaw swoją subskrypcję i zarejestrować dostawcę
Scenariusze migracji należy skonfigurować środowisko dla obu classic i Menedżera zasobów. [Zainstaluj interfejs wiersza polecenia Azure](../../cli-install-nodejs.md) i [Wybierz subskrypcję](/cli/azure/authenticate-azure-cli).

Zaloguj się do swojego konta.

    azure login

Wybierz subskrypcję platformy Azure przy użyciu następującego polecenia.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Rejestracja jest relacją jeden krok czasu, ale należy jednak wykonać jeden raz, przed podjęciem próby wykonania migracji. Bez rejestrowania pojawi się następujący komunikat o błędzie 
> 
> *Element BadRequest: Subskrypcja nie jest zarejestrowana do migracji.* 
> 
> 

Zarejestruj się w dostawcy zasobów migracji za pomocą następującego polecenia. Należy pamiętać, że w niektórych przypadkach to polecenie upłynie limit czasu. Jednak powiedzie się rejestracji.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Poczekaj pięć minut dla rejestracji zakończyć. Za pomocą następującego polecenia można sprawdzić stan zatwierdzenia. Upewnij się, że jest RegistrationState `Registered` przed kontynuowaniem.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Teraz przełącznik interfejsu wiersza polecenia do `asm` tryb.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 3: Upewnij się, że masz wystarczająco dużo Vcpu maszyny wirtualnej Azure Resource Manager w regionie Azure bieżącego wdrożenia lub sieci Wirtualnej
W tym kroku musisz przełączyć się do `arm` tryb. To zrobić przy użyciu następującego polecenia.

```
azure config mode arm
```

Interfejsu wiersza polecenia następujące polecenie służy do sprawdzania bieżąca liczba Vcpu, do których masz usługi Azure Resource Manager. Aby dowiedzieć się więcej na temat przydziały vCPU, zobacz [limity i usługi Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Po zakończeniu sprawdzenia tego kroku, możesz przełączyć do `asm` tryb.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Krok 4: Opcja 1 — migracji maszyn wirtualnych w usłudze w chmurze
Pobierz listę usług w chmurze za pomocą następującego polecenia, a następnie wybierz usługę w chmurze, które chcesz migrować. Należy pamiętać, że jeśli maszyny wirtualne w usłudze w chmurze znajdują się w sieci wirtualnej, lub jeśli dysponują role sieć web/proces roboczy zostanie wyświetlony komunikat o błędzie.

    azure service list

Uruchom następujące polecenie, aby uzyskać nazwę wdrożenia usługi w chmurze z pełne dane wyjściowe. W większości przypadków nazwa wdrożenia jest taka sama jak nazwa usługi w chmurze.

    azure service show <serviceName> -vv

Po pierwsze sprawdzanie poprawności w przypadku migrowania usługi w chmurze przy użyciu następujących poleceń:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Przygotuj maszyny wirtualne w usłudze w chmurze do migracji. Dostępne są dwie opcje do wyboru.

Jeśli chcesz przeprowadzić migrację maszyn wirtualnych do sieci wirtualnych utworzonych przez platformę, użyj następującego polecenia.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Jeśli chcesz przeprowadzić migrację do istniejącej sieci wirtualnej w modelu wdrażania usługi Resource Manager, użyj następującego polecenia.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Po operacji prepare zakończy się pomyślnie, można sprawdzić za pomocą pełne dane wyjściowe do pobrania stanu migracji maszyn wirtualnych i upewnij się, że są one `Prepared` stanu.

    azure vm show <vmName> -vv

Sprawdź konfigurację dla zasobów przygotowanego przy użyciu interfejsu wiersza polecenia lub w portalu Azure. Jeśli chcesz powrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia.

    azure service deployment abort-migration <serviceName> <deploymentName>

Jeśli przygotowane Konfiguracja wygląda dobrze, można przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Krok 4: Opcja 2 — migracji maszyn wirtualnych w sieci wirtualnej
Wybierz sieć wirtualną, która mają zostać zmigrowane. Należy pamiętać, że jeśli sieć wirtualna zawiera role sieć web/proces roboczy lub maszyn wirtualnych z nieobsługiwaną konfiguracją, otrzymasz komunikat o błędzie weryfikacji.

Pobierz wszystkie sieci wirtualne w subskrypcji przy użyciu następującego polecenia.

    azure network vnet list

Dane wyjściowe będą wyglądać mniej więcej tak:

![Zrzut ekranu przedstawiający wiersza polecenia o nazwie całej sieci wirtualnej wyróżnione.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

W powyższym przykładzie **virtualNetworkName** jest całą nazwę **"Classicubuntu16 classicubuntu16 grupy"**.

Po pierwsze sprawdzanie poprawności w przypadku migrowania sieci wirtualnej przy użyciu następującego polecenia:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Przygotowania do migracji sieci wirtualnej wybranych przez użytkownika, za pomocą następującego polecenia.

    azure network vnet prepare-migration <virtualNetworkName>

Sprawdź konfigurację przygotować maszyn wirtualnych przy użyciu interfejsu wiersza polecenia lub w portalu Azure. Jeśli chcesz powrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia.

    azure network vnet abort-migration <virtualNetworkName>

Jeśli przygotowane Konfiguracja wygląda dobrze, można przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Krok 5: Migrowanie konta magazynu
Po zakończeniu migracji maszyn wirtualnych, firma Microsoft zaleca migracji konta magazynu.

Przygotowanie konta magazynu dla migracji za pomocą następującego polecenia

    azure storage account prepare-migration <storageAccountName>

Sprawdź konfigurację dla konta magazynu przygotowanego przy użyciu interfejsu wiersza polecenia lub w portalu Azure. Jeśli chcesz powrócić do poprzedni stan nie jest jeszcze gotowa do migracji, użyj następującego polecenia.

    azure storage account abort-migration <storageAccountName>

Jeśli przygotowane Konfiguracja wygląda dobrze, można przejść do przodu i zatwierdzić zasobów za pomocą następującego polecenia.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager obsługiwane platformy](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Techniczne szczegółowe informacje na temat obsługiwanych platform migracji ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migracja zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager przy użyciu programu PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Narzędzia społeczności z migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migrowania zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
