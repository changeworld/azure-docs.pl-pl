---
title: Limity zasobów dla Azure NetApp Files | Microsoft Docs
description: Opisuje limity dotyczące Azure NetApp Files zasobów, w tym limity dla kont NetApp, pul pojemności, woluminów, migawek i delegowanej podsieci.
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 763b3961bfd621f05ab5cd021526b0d6d109c020
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881721"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limity zasobów dla usługi Azure NetApp Files

Zrozumienie limitów zasobów Azure NetApp Files ułatwia zarządzanie woluminami.

## <a name="resource-limits"></a>Limity zasobów

W poniższej tabeli opisano limity zasobów dla Azure NetApp Files:

|  Resource  |  Limit domyślny  |  Regulowane przez żądanie pomocy technicznej  |
|----------------|---------------------|--------------------------------------|
|  Liczba kont NetApp na subskrypcję platformy Azure   |  10    |  Tak   |
|  Liczba pul pojemności na konto NetApp   |    25     |   Yes   |
|  Liczba woluminów na pulę pojemności     |    500   |    Tak     |
|  Liczba migawek na wolumin       |    255     |    Nie        |
|  Liczba podsieci delegowanych do Azure NetApp Files (Microsoft. NetApp/Volumes) na platformie Azure Virtual Network    |   1   |    Nie    |
|  Maksymalna liczba adresów IP używanych w sieci wirtualnej (w tym Komunikacja równorzędna sieci wirtualnych) z Azure NetApp Files    |    1000   |    Nie   |
|  Minimalny rozmiar puli o pojedynczej pojemności   |  4 TiB     |    Nie  |
|  Maksymalny rozmiar puli o pojedynczej pojemności    |  500 TiB   |   Nie   |
|  Minimalny rozmiar pojedynczego woluminu    |    100 GiB    |    Nie    |
|  Maksymalny przypisany limit przydziału pojedynczego woluminu *   |   92 TiB   |    Nie   |
|  Maksymalny rozmiar pojedynczego woluminu *     |    100 TiB    |    Nie       |
|  Maksymalna liczba plików (węzłów i) na wolumin     |    50 000 000    |    Nie    |    

\* Wolumin można ręcznie utworzyć lub zmienić jego rozmiar na Maximally 92 TiB. Jednak wolumin może wzrosnąć do 100 TiB w scenariuszu nadwyżki. Aby uzyskać szczegółowe informacje o nadmiernej pojemności, zobacz [model kosztów dla Azure NetApp Files](azure-netapp-files-cost-model.md) . 

## <a name="request-limit-increase"></a>Zwiększenie limitu żądań 

Możesz utworzyć żądanie pomocy technicznej platformy Azure, aby zwiększyć regulowane limity z powyższej tabeli. 

Ze płaszczyzny nawigacyjnej Azure Portal: 

1. Kliknij pozycję **Pomoc i obsługa techniczna**.
2. Kliknij pozycję **+ nowe żądanie obsługi**.
3. Na karcie podstawowe podaj następujące informacje: 
    1. Typ problemu: Wybierz pozycję **usługi i limity subskrypcji (przydziały)** .
    2. Subskrypcje: Wybierz subskrypcję zasobu, dla którego chcesz zwiększyć przydział.
    3. Typ limitu przydziału: Wybierz **magazyn: Limity**Azure NetApp Files.
    4. Kliknij pozycję **Next: Rozwiązania**.
4. Na karcie Szczegóły:
    1. W polu Opis podaj następujące informacje dotyczące odpowiedniego typu zasobu:

        |  Resource  |    Zasoby nadrzędne      |    Żądane nowe limity     |    Przyczyna zwiększenia limitu przydziału       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Subscription ID (Identyfikator subskrypcji)*   |  *Żądany nowy maksymalny numer **konta***    |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |
        |  Pula    |  *Identyfikator subskrypcji, identyfikator URI konta*  |  *Żądany nowy numer **puli***   |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |
        |  Wolumin  |  *Identyfikator subskrypcji, identyfikator URI konta, identyfikator URI puli*   |  *Żądany nowy maksymalny numer **woluminu***     |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |

    2. Określ odpowiednią metodę obsługi i podaj informacje o kontrakcie.

    3. Kliknij pozycję **Next: Przejrzyj i Utwórz** , aby utworzyć żądanie. 


## <a name="next-steps"></a>Kolejne kroki  

- [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Model kosztów dla Azure NetApp Files](azure-netapp-files-cost-model.md)
