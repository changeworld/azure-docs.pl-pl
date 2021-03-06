---
title: Kody błędów dla maszyn wirtualnych i grup skalowania platformy Azure i zestawów skalowania
description: Dowiedz się więcej o kodach błędów, które można zobaczyć podczas korzystania z maszyn wirtualnych punktowych i skalowania ustawionych wystąpień.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547819"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Komunikaty o błędach dla maszyn wirtualnych punktowych i zestawów skalowania

Oto kilka możliwych kodów błędów, które można otrzymać podczas korzystania z maszyn wirtualnych punktowych i zestawów skalowania.


| Klucz | Komunikat | Opis |
|-----|---------|-------------|
| SkuNotAvailable | Żądana\<warstwa zasobu\>' zasobu ' nie\<\>jest obecnie dostępna w lokalizacji ' lokalizacji ' dla subskrypcji '\<subscriptionID\>'. Spróbuj użyć innej warstwy lub wdrożyć w innej lokalizacji. | Nie ma wystarczającej pojemności spot platformy Azure w tej lokalizacji, aby utworzyć maszynę wirtualną lub skalowanie zestawu wystąpienia. |
| EksmisjaPolicjaCanBeSetOnlyOnLyOnAzureSpotVirtualMachines  |  Zasady eksmisji można ustawić tylko na maszynach wirtualnych spot platformy Azure. | Ta maszyna wirtualna nie jest maszyną wirtualną punktową, więc nie można ustawić zasad eksmisji. |
| Zestaw AzureSpotVMNotSupportedInAvailabilitySet  |  Usługa Azure Spot Virtual Machine nie jest obsługiwana w zestawie dostępności. | Musisz wybrać użycie maszyny Wirtualnej punktowej lub użyć maszyny Wirtualnej w zestawie dostępności, nie można wybrać obu. |
| Usługa AzureSpotFeatureNotEnabledForSubscription  |  Subskrypcja nie jest włączona za pomocą funkcji Spot platformy Azure. | Użyj subskrypcji, która obsługuje maszyny wirtualne spot. |
| VMPriorityNiezastosowano  |  Określona wartość{0}priorytetu ' ' nie może{1}być zastosowana do maszyny wirtualnej '' ponieważ podczas tworzenia maszyny wirtualnej nie określono żadnego priorytetu. | Określ priorytet podczas tworzenia maszyny Wirtualnej. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Nie można wykonać{0}operacji ' '{1} ponieważ podana cena maksymalna{2} 'USD' jest niższa{3}niż bieżąca cena spot 'USD' dla rozmiaru maszyny wirtualnej spot platformy Azure '. | Wybierz wyższą cenę maksymalną. Aby uzyskać więcej informacji, zobacz informacje o cenach dla [systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) lub [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| Maksymalna wartośćvalueInvalid  |  Nieprawidłowa maksymalna wartość ceny. Jedynymi obsługiwanymi wartościami dla ceny maksymalnej są -1 lub dziesiętne większe od zera. Maksymalna wartość ceny -1 wskazuje, że maszyna wirtualna Azure Spot nie zostanie eksmitowana ze względu na cenę. | Wprowadź prawidłową cenę maksymalną. Aby uzyskać więcej informacji, zobacz ceny dla [systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) lub [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Maksymalna zmiana ceny nie jest dozwolona, gdy maszyna wirtualna '{0}jest aktualnie przydzielona. Proszę zdyskontować i spróbuj ponownie. | Zatrzymaj\Zdelegalizuj maszynę wirtualną, aby można było zmienić cenę maksymalną. |
| MaxPriceChangeNieużkwolenie | Maksymalna zmiana ceny nie jest dozwolona. | Nie można zmienić maksymalnej ceny dla tej maszyny Wirtualnej. |
| Usługa AzureSpotIsNotSupportedForThisAPIVersion  |  Usługa Azure Spot nie jest obsługiwana dla tej wersji interfejsu API. | Wersja interfejsu API musi być 2019-03-01. |
| Usługa AzureSpotIsNotSupportedForThisVMSize  |  Usługa Azure Spot nie jest obsługiwana dla tego rozmiaru {0}maszyny Wirtualnej. | Wybierz inny rozmiar maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Spot Virtual Machines](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Maksymalna cena jest obsługiwana tylko dla maszyn wirtualnych platformy Azure Spot. | Aby uzyskać więcej informacji, zobacz [Spot Virtual Machines](./linux/spot-vms.md). |
| Obsługiwane przez MoveResourcesWithAzureSpotVMNotSupportowane  |  Żądanie przenieś zasobów zawiera maszynę wirtualną usługi Azure Spot. Obecnie nie jest to obsługiwane. Sprawdź szczegóły błędu dla identyfikatorów maszyn wirtualnych. | Nie można przenosić maszyn wirtualnych punktowych. |
| MoveResourcesWithAzureSpotVmssNotSupportowane  |  Żądanie przenieś zasobów zawiera zestaw skalowania maszyny wirtualnej usługi Azure Spot. Obecnie nie jest to obsługiwane. Sprawdź szczegóły błędu dla identyfikatorów zestawu skalowania maszyny wirtualnej. | Nie można przenosić wystąpień zestawu skali punktowej. |
| EfemeryczneOSDisksNotSupportedForSpotVMs | Dyski efemerycznego systemu operacyjnego nie są obsługiwane dla maszyn wirtualnych punktowych. | Użyj zwykłego dysku systemu operacyjnego dla maszyny Wirtualnej spot. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Usługa Azure Spot Virtual Machine nie jest obsługiwana w zestawie skalowania maszyny wirtualnej w trybie aranżacji maszyny wirtualnej. | Ustaw tryb aranżacji na skalę maszyny wirtualnej ustawioną w celu użycia wystąpień spot. |


**Kolejne kroki** Aby uzyskać więcej informacji, zobacz [spot Maszyny wirtualne](./linux/spot-vms.md).