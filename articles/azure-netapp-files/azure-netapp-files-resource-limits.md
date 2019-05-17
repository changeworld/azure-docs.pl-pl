---
title: Limity zasobów dla usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: W tym artykule opisano limity dla zasobów usługi Azure Files NetApp, w tym limity dla konta, pojemności pul, woluminy, migawki i delegowanego podsieci NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: b55467d77beb8f97b8e392b72682268ae0407e54
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826377"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limity zasobów dla usługi Azure NetApp Files

Opis limity zasobów dla usługi Azure Files NetApp ułatwia zarządzanie woluminami.

## <a name="resource-limits"></a>Limity zasobów

W poniższej tabeli opisano limity zasobów dla usługi Azure Files NetApp:

|  Resource  |  Limit domyślny  |  Zmienianych za pośrednictwem żądania pomocy technicznej  |
|----------------|---------------------|--------------------------------------|
|  Liczba kont NetApp na subskrypcję platformy Azure   |  10    |  Tak   |
|  Liczba pul pojemność na jednym koncie NetApp   |    25     |   Tak   |
|  Liczba woluminów na pulę pojemności     |    500   |    Tak     |
|  Liczba migawek na każdym woluminie       |    255     |    Nie        |
|  Liczba podsieci delegować domenę do usługi Azure Files NetApp (Microsoft.NetApp/volumes) na sieć wirtualną platformy Azure    |   1   |    Nie    |
|  Maksymalna liczba maszyn wirtualnych (w tym wirtualne sieci równorzędne) który może nawiązać woluminu     |    1000   |    Nie   |
|  Minimalny rozmiar puli jednej pojemności   |  4 TiB     |    Nie  |
|  Maksymalny rozmiar puli jednej pojemności    |  500 TiB   |   Nie   |
|  Minimalny rozmiar pojedynczego woluminu    |    100 GiB    |    Nie    |
|  Maksymalna przypisany przydział pojedynczy wolumin *   |   92 TiB   |    Nie   |
|  Maksymalny rozmiar jednego woluminu *     |    100 TiB    |    Nie       |

* Ręcznie utworzone woluminu lub rozmiar maksymalnie 92 TiB. Jednak woluminu można powiększać maksymalnie 100 TiB w scenariuszu nadwyżkowe. Zobacz [modelu kosztów dla usługi Azure Files NetApp](azure-netapp-files-cost-model.md) szczegółowe informacje na temat nadwyżki zdolności produkcyjnych. 

## <a name="request-limit-increase"></a>Zażądać zwiększenia limitu 

Możesz utworzyć żądanie pomocy technicznej platformy Azure, możesz zwiększyć limity zmienianych w powyższej tabeli. 

Płaszczyzny Nawigacja w portalu Azure: 

1. Kliknij przycisk **Pomoc i obsługa techniczna**.
2. Kliknij przycisk **+ nowe żądanie pomocy technicznej**.
3. Na karcie podstawy Podaj następujące informacje: 
    1. Typ problemu: Wybierz **limity usług i subskrypcji (przydziały)**.
    2. Subskrypcje: Wybierz subskrypcję, dla wymagających zwiększenia limitu przydziału zasobu.
    3. Typ limitu przydziału: Wybierz **magazynu: Limity usługi Azure Files NetApp**.
    4. Kliknij pozycję **Next: Rozwiązania**.
4. Na karcie Szczegóły:
    1. W polu Opis Podaj następujące informacje dotyczące danego typu zasobu:

        |  Resource  |    Zasoby nadrzędnego      |    Żądane nowe limity     |    Przyczyna zwiększenia limitu przydziału       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Subscription ID (Identyfikator subskrypcji)*   |  *Żądany maksymalny nowe **konta** numer*    |  *Jakie przypadek scenariusza lub użyj monitu żądania?*  |
        |  Pula    |  *Identyfikator subskrypcji, identyfikator URI konta*  |  *Żądany maksymalny nowe **puli** numer*   |  *Jakie przypadek scenariusza lub użyj monitu żądania?*  |
        |  Wolumin  |  *Identyfikator subskrypcji, identyfikator URI, konta puli identyfikatorów URI*   |  *Żądany maksymalny nowe **woluminu** numer*     |  *Jakie przypadek scenariusza lub użyj monitu żądania?*  |

    2. Określ odpowiednie obsługuje metody i podaj swoje informacje kontraktu.

    3. Kliknij pozycję **Next: Przeglądanie + tworzenie** do utworzenia żądania. 


## <a name="next-steps"></a>Kolejne kroki  

- [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Model kosztów dla usługi Azure Files NetApp](azure-netapp-files-cost-model.md)
