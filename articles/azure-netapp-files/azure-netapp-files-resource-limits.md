---
title: Limity zasobów dla Azure NetApp Files | Microsoft Docs
description: Opisuje limity dotyczące zasobów Azure NetApp Files i sposób żądania zwiększenia limitu zasobów.
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
ms.date: 09/20/2019
ms.author: b-juche
ms.openlocfilehash: f7213ddee5d7bdfd41508f5fee66de63cde5b7c4
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170024"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limity zasobów dla usługi Azure NetApp Files

Zrozumienie limitów zasobów Azure NetApp Files ułatwia zarządzanie woluminami.

## <a name="resource-limits"></a>Limity zasobów

W poniższej tabeli opisano limity zasobów dla Azure NetApp Files:

|  Resource  |  Domyślny limit  |  Regulowane przez żądanie pomocy technicznej  |
|----------------|---------------------|--------------------------------------|
|  Liczba kont NetApp na subskrypcję platformy Azure   |  10    |  Tak   |
|  Liczba pul pojemności na konto NetApp   |    25     |   Tak   |
|  Liczba woluminów na pulę pojemności     |    500   |    Tak     |
|  Liczba migawek na wolumin       |    255     |    Nie        |
|  Liczba podsieci delegowanych do Azure NetApp Files (Microsoft. NetApp/Volumes) na platformie Azure Virtual Network    |   1   |    Nie    |
|  Liczba adresów IP w sieci wirtualnej (w tym sieci wirtualnych komunikacji równorzędnej), które mogą uzyskiwać dostęp Azure NetApp Files   |    1000   |    Tak   |
|  Minimalny rozmiar puli o pojedynczej pojemności   |  4 TiB     |    Nie  |
|  Maksymalny rozmiar puli o pojedynczej pojemności    |  500 TiB   |   Nie   |
|  Minimalny rozmiar pojedynczego woluminu    |    100 GiB    |    Nie    |
|  Maksymalny rozmiar pojedynczego woluminu     |    100 TiB    |    Nie    |
|  Maksymalna liczba plików ([maxfiles](#maxfiles)) na wolumin     |    100 000 000    |    Tak    |    
|  Maksymalny rozmiar pojedynczego pliku     |    16 TiB    |    Nie    |    

## Limity maxfiles<a name="maxfiles"></a> 

Woluminy Azure NetApp Files mają limit o nazwie *maxfiles*. Limit maxfiles to liczba plików, które może zawierać wolumin. Limit maxfiles dla woluminu Azure NetApp Files jest indeksowany na podstawie rozmiaru (przydziału) woluminu. Limit maxfiles dla woluminu rośnie lub zmniejsza się o szybkości 20 000 000 plików na TiB rozmiaru woluminu. 

Usługa dynamicznie dostosowuje limit maxfiles dla woluminu na podstawie jego rozmiaru aprowizacji. Na przykład wolumin skonfigurowany początkowo o rozmiarze 1 TiB będzie miał limit maxfiles równy 20 000 000. Kolejne zmiany rozmiaru woluminu spowodują automatyczne ponowne dostosowanie limitu maxfiles w zależności od następujących zasad: 

|    Rozmiar woluminu (przydział)     |  Automatyczne dostosowywanie limitu maxfiles    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 000 000     |
|    > = 1 TiB, ale < 2 TiB    |    40 000 000     |
|    > = 2 TiB, ale < 3 TiB    |    60 000 000     |
|    > = 3 TiB, ale < 4 TiB    |    80 000 000     |
|    > = 4 TiB                |    100 000 000    |

W przypadku dowolnego rozmiaru woluminu można zainicjować [żądanie obsługi](#limit_increase) , aby zwiększyć limit maxfilesy przekraczający 100 000 000.

## Zwiększenie limitu żądań<a name="limit_increase"></a> 

Możesz utworzyć żądanie pomocy technicznej platformy Azure, aby zwiększyć regulowane limity z powyższej tabeli. 

Ze płaszczyzny nawigacyjnej Azure Portal: 

1. Kliknij pozycję **Pomoc i obsługa techniczna**.
2. Kliknij pozycję **+ nowe żądanie obsługi**.
3. Na karcie podstawowe podaj następujące informacje: 
    1. Typ problemu: Wybierz pozycję **usługi i limity subskrypcji (przydziały)** .
    2. Opłaty Wybierz subskrypcję zasobu, dla którego chcesz zwiększyć przydział.
    3. Typ limitu przydziału: Wybierz **magazyn: Limity**Azure NetApp Files.
    4. Kliknij pozycję **Next: Rozwiązania**.
4. Na karcie Szczegóły:
    1. W polu Opis podaj następujące informacje dotyczące odpowiedniego typu zasobu:

        |  Resource  |    Zasoby nadrzędne      |    Żądane nowe limity     |    Przyczyna zwiększenia limitu przydziału       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Subscription ID (Identyfikator subskrypcji)*   |  *Żądany nowy maksymalny numer **konta***    |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |
        |  Pula    |  *Identyfikator subskrypcji, identyfikator URI konta*  |  *Żądany nowy numer **puli***   |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |
        |  Wolumin  |  *Identyfikator subskrypcji, identyfikator URI konta, identyfikator URI puli*   |  *Żądany nowy maksymalny numer **woluminu***     |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |
        |  Maxfiles  |  *Identyfikator subskrypcji, identyfikator URI konta, identyfikator URI puli, identyfikator URI woluminu*   |  *Żądany nowy maksymalny numer **maxfiles***     |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |    

    2. Określ odpowiednią metodę obsługi i podaj informacje o kontrakcie.

    3. Kliknij pozycję **Next: Przejrzyj i Utwórz** , aby utworzyć żądanie. 


## <a name="next-steps"></a>Następne kroki  

- [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Model kosztów dla Azure NetApp Files](azure-netapp-files-cost-model.md)
