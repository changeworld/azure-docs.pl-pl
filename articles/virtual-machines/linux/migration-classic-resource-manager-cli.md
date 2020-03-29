---
title: Migrowanie maszyn wirtualnych do Menedżera zasobów przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule oprzeszczony przez obsługiwaną przez platformę migrację zasobów z klasycznego do usługi Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: c41292a05e5c857cd0b1c120784a400f2f5410ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945355"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrowanie zasobów rozwiązania IaaS z modelu klasycznego do modelu opartego na usłudze Azure Resource Manager przy użyciu interfejsu wiersza polecenia

> [!IMPORTANT]
> Obecnie około 90% maszyn wirtualnych IaaS korzysta z [usługi Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Od 28 lutego 2020 r. klasyczne maszyny wirtualne zostały przestarzałe i zostaną całkowicie wycofane 1 marca 2023 r. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym uszczukaniu i [o tym, jak wpływa na Ciebie](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

W tych krokach pokazano, jak używać poleceń interfejsu wiersza polecenia platformy Azure (CLI) do migracji zasobów infrastruktury jako usługi (IaaS) z klasycznego modelu wdrażania do modelu wdrażania usługi Azure Resource Manager. Artykuł wymaga [klasycznego interfejsu wiersza polecenia platformy Azure](../../cli-install-nodejs.md). Ponieważ narzędzie cli platformy Azure ma zastosowanie tylko do zasobów usługi Azure Resource Manager, nie można go używać do tej migracji.

> [!NOTE]
> Wszystkie operacje opisane w tym miejscu są idempotentne. Jeśli masz problem inny niż nieobsługicona funkcja lub błąd konfiguracji, zaleca się ponowienie próby operacji przygotowania, przerwania lub zatwierdzenia. Platforma spróbuje ponownie wykonać akcję.
> 
> 

<br>
Oto schemat blokowy do identyfikowania kolejności, w jakiej kroki muszą być wykonywane podczas procesu migracji

![Zrzut ekranu przedstawiający kroki migracji](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Krok 1: Przygotowanie do migracji
Oto kilka sprawdzonych rozwiązań zalecanych podczas oceny migracji zasobów IaaS z klasycznego do Menedżera zasobów:

* Zapoznaj się [z listą nieobsługiwanych konfiguracji lub funkcji](../windows/migration-classic-resource-manager-overview.md). Jeśli masz maszyny wirtualne, które używają nieobsługiwał konfiguracji lub funkcji, zaleca się czekać na funkcję/konfigurację pomocy technicznej, które mają być ogłoszone. Alternatywnie można usunąć tę funkcję lub wyprowadzić się z tej konfiguracji, aby włączyć migrację, jeśli odpowiada ona Twoim potrzebom.
* Jeśli masz zautomatyzowane skrypty, które wdrażają infrastrukturę i aplikacje dzisiaj, spróbuj utworzyć podobną konfigurację testów przy użyciu tych skryptów do migracji. Alternatywnie można skonfigurować przykładowe środowiska przy użyciu witryny Azure portal.

> [!IMPORTANT]
> Bramy aplikacji nie są obecnie obsługiwane w przypadku migracji z klasycznego do Menedżera zasobów. Aby przeprowadzić migrację klasycznej sieci wirtualnej z bramą aplikacji, usuń bramę przed uruchomieniem operacji Przygotowanie w celu przeniesienia sieci. Po zakończeniu migracji ponownie połącz bramę w usłudze Azure Resource Manager. 
>
>Bramy usługi ExpressRoute łączące się z obwodami usługi ExpressRoute w innej subskrypcji nie mogą być migrowane automatycznie. W takich przypadkach usuń bramę usługi ExpressRoute, zmigruj sieć wirtualną i ponownie stwórz bramę. Aby uzyskać więcej informacji, zobacz [Migrowanie obwodów usługi ExpressRoute i skojarzonych sieci wirtualnych z modelu wdrażania klasycznego do modelu wdrażania Menedżera zasobów.](../../expressroute/expressroute-migration-classic-resource-manager.md)
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Krok 2: Ustaw subskrypcję i zarejestruj dostawcę
W przypadku scenariuszy migracji należy skonfigurować środowisko zarówno dla klasycznego, jak i Menedżera zasobów. [Zainstaluj platformę Azure CLI](../../cli-install-nodejs.md) i [wybierz subskrypcję](/cli/azure/authenticate-azure-cli).

Zaloguj się na swoje konto.

    azure login

Wybierz subskrypcję platformy Azure za pomocą następującego polecenia.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Rejestracja jest krokiem jednorazowym, ale należy to zrobić raz przed podjęciem próby migracji. Bez rejestracji zostanie wyświetlony następujący komunikat o błędzie 
> 
> *BadRequest: Subskrypcja nie jest zarejestrowana do migracji.* 
> 
> 

Zarejestruj się u dostawcy zasobów migracji za pomocą następującego polecenia. Należy zauważyć, że w niektórych przypadkach to polecenie przesunie się. Rejestracja zakończy się jednak sukcesem.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Poczekaj pięć minut na zakończenie rejestracji. Stan zatwierdzenia można sprawdzić za pomocą następującego polecenia. Upewnij się, że `Registered` registrationState jest przed kontynuowaniem.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Teraz przełącz `asm` cli do trybu.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 3: Upewnij się, że masz wystarczającą liczbę procesorów wirtualnych maszyny wirtualnej usługi Azure Resource Manager w regionie platformy Azure w bieżącym wdrożeniu lub sieci wirtualnej
W tym kroku musisz przełączyć `arm` się do trybu. Zrób to za pomocą następującego polecenia.

```
azure config mode arm
```

Za pomocą następującego polecenia INTERFEJSU WIERSZA POLECENIA można sprawdzić bieżącą liczbę procesorów wirtualnych w usłudze Azure Resource Manager. Aby dowiedzieć się więcej o przydziałach vCPU, zobacz [Limity i Usługa Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Po zakończeniu weryfikacji tego kroku możesz przełączyć `asm` się z powrotem do trybu.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Krok 4: Opcja 1 — migrowanie maszyn wirtualnych w usłudze w chmurze
Pobierz listę usług w chmurze za pomocą następującego polecenia, a następnie wybierz usługę w chmurze, którą chcesz przeprowadzić migrację. Należy zauważyć, że jeśli maszyny wirtualne w usłudze w chmurze znajdują się w sieci wirtualnej lub mają role sieci web/procesu roboczego, zostanie wyświetlony komunikat o błędzie.

    azure service list

Uruchom następujące polecenie, aby uzyskać nazwę wdrożenia usługi w chmurze z danych wyjściowych. W większości przypadków nazwa wdrożenia jest taka sama jak nazwa usługi w chmurze.

    azure service show <serviceName> -vv

Najpierw sprawdź, czy można przeprowadzić migrację usługi w chmurze za pomocą następujących poleceń:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Przygotuj maszyny wirtualne w usłudze w chmurze do migracji. Masz dwie opcje do wyboru.

Jeśli chcesz przeprowadzić migrację maszyn wirtualnych do sieci wirtualnej utworzonej przez platformę, użyj następującego polecenia.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Jeśli chcesz przeprowadzić migrację do istniejącej sieci wirtualnej w modelu wdrażania Menedżera zasobów, użyj następującego polecenia.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Po zakończeniu operacji przygotowania można przeglądać pełne dane wyjściowe, aby uzyskać stan migracji maszyn wirtualnych `Prepared` i upewnić się, że są one w stanie.

    azure vm show <vmName> -vv

Sprawdź konfigurację przygotowanych zasobów przy użyciu interfejsu wiersza polecenia lub witryny Azure portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia.

    azure service deployment abort-migration <serviceName> <deploymentName>

Jeśli przygotowana konfiguracja wygląda dobrze, można przejść do przodu i zatwierdzić zasoby za pomocą następującego polecenia.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Krok 4: Opcja 2 - Migrowanie maszyn wirtualnych w sieci wirtualnej
Wybierz sieć wirtualną, którą chcesz przeprowadzić migrację. Należy zauważyć, że jeśli sieć wirtualna zawiera role sieci web/procesu roboczego lub maszyny wirtualne z nieobsługiwałymi konfiguracjami, zostanie wyświetlony komunikat o błędzie sprawdzania poprawności.

Pobierz wszystkie sieci wirtualne w subskrypcji za pomocą następującego polecenia.

    azure network vnet list

Dane wyjściowe będą przypominać następujące:

![Zrzut ekranu przedstawiający wiersz polecenia z wyróżnioną całą nazwą sieci wirtualnej.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

W powyższym przykładzie **virtualNetworkName** to cała nazwa **"Group classicubuntu16 classicubuntu16"**.

Najpierw sprawdź, czy można przeprowadzić migrację sieci wirtualnej za pomocą następującego polecenia:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Przygotuj wybraną sieć wirtualną do migracji za pomocą następującego polecenia.

    azure network vnet prepare-migration <virtualNetworkName>

Sprawdź konfigurację przygotowanych maszyn wirtualnych przy użyciu interfejsu wiersza polecenia lub witryny Azure portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia.

    azure network vnet abort-migration <virtualNetworkName>

Jeśli przygotowana konfiguracja wygląda dobrze, można przejść do przodu i zatwierdzić zasoby za pomocą następującego polecenia.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Krok 5: Migrowanie konta magazynu
Po zakończeniu migracji maszyn wirtualnych zaleca się migrację konta magazynu.

Przygotowanie konta magazynu do migracji za pomocą następującego polecenia

    azure storage account prepare-migration <storageAccountName>

Sprawdź konfigurację dla przygotowanego konta magazynu przy użyciu interfejsu wiersza polecenia lub witryny Azure portal. Jeśli nie jesteś gotowy do migracji i chcesz wrócić do starego stanu, użyj następującego polecenia.

    azure storage account abort-migration <storageAccountName>

Jeśli przygotowana konfiguracja wygląda dobrze, można przejść do przodu i zatwierdzić zasoby za pomocą następującego polecenia.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Następne kroki

* [Omówienie migracji zasobów usługi IaaS obsługiwanej przez platformę z klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą programu PowerShell](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Narzędzia społecznościowe pomagające w migracji zasobów IaaS z klasycznego do usługi Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migracji zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
