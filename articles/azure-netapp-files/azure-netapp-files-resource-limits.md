---
title: Limity zasobów dla plików NetApp platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano limity zasobów usługi Azure NetApp Files i sposób żądania zwiększenia limitu zasobów.
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
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: ac660b20d519e49e832e979603f763fa672757a5
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637399"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limity zasobów dla usługi Azure NetApp Files

Opis limitów zasobów dla plików NetApp usługi Azure ułatwia zarządzanie woluminami.

## <a name="resource-limits"></a>Limity zasobów

W poniższej tabeli opisano limity zasobów dla plików NetApp platformy Azure:

|  Zasób  |  Limit domyślny  |  Możliwość regulacji za pomocą wezwania do obsługi  |
|----------------|---------------------|--------------------------------------|
|  Liczba kont NetApp w regionie platformy Azure   |  10    |  Tak   |
|  Liczba puli pojemności na konto NetApp   |    25     |   Tak   |
|  Liczba woluminów na pulę pojemności     |    500   |    Tak     |
|  Liczba migawek na wolumin       |    255     |    Nie        |
|  Liczba podsieci delegowanych do plików NetApp platformy Azure (Microsoft.NetApp/volumes) na sieć wirtualną platformy Azure    |   1   |    Nie    |
|  Liczba używanych usług IP w sieci wirtualnej (w tym natychmiast równorzędne sieci wirtualne) z plikami NetApp platformy Azure   |    1000   |    Nie   |
|  Minimalny rozmiar pojedynczej puli pojemności   |  4 TiB     |    Nie  |
|  Maksymalny rozmiar puli pojedynczej pojemności    |  500 TiB   |   Nie   |
|  Minimalny rozmiar pojedynczej objętości    |    100 Gib    |    Nie    |
|  Maksymalny rozmiar pojedynczej głośności     |    100 TiB    |    Nie    |
|  Maksymalny rozmiar pojedynczego pliku     |    16 TiB    |    Nie    |    
|  Maksymalny rozmiar pojedynczego katalogu      |    320 MB    |    Nie    |    
|  Maksymalna liczba plików[(maxfiles)](#maxfiles)na wolumin     |    100 milionów    |    Tak    |    

Aby uzyskać więcej informacji, zobacz Często [zadawane pytania dotyczące zarządzania pojemnością](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="maxfiles-limits"></a>Limity maxfiles<a name="maxfiles"></a> 

Woluminy usługi Azure NetApp Files mają limit o nazwie *maxfiles*. Limit plików max jest liczbą plików, które może zawierać wolumin. Limit plików max dla woluminu usługi Azure NetApp Files jest indeksowany na podstawie rozmiaru (przydziału) woluminu. Limit plików max dla woluminu zwiększa się lub zmniejsza z szybkością 20 milionów plików na TiB aprowizowanego rozmiaru woluminu. 

Usługa dynamicznie dostosowuje limit maxfiles dla woluminu na podstawie jego aprowizacji. Na przykład wolumin skonfigurowany początkowo o rozmiarze 1 TiB miałby limit plików maxowych wynoszący 20 milionów. Kolejne zmiany rozmiaru woluminu spowodowałoby automatyczną korektę limitu maxfiles na podstawie następujących reguł: 

|    Wielkość woluminu (przydział)     |  Automatyczna regulacja limitu maxfiles    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 milionów     |
|    >= 1 TiB, ale < 2 TiB    |    40 milionów     |
|    >= 2 TiB, ale < 3 TiB    |    60 milionów     |
|    >= 3 TiB, ale < 4 TiB    |    80 milionów     |
|    >= 4 TiB                |    100 milionów    |

W przypadku dowolnego rozmiaru woluminu można zainicjować [żądanie pomocy technicznej](#limit_increase) w celu zwiększenia limitu plików maxpomiarowych powyżej 100 milionów.

## <a name="request-limit-increase"></a>Zwiększenie limitu żądań<a name="limit_increase"></a> 

Można utworzyć żądanie pomocy technicznej platformy Azure, aby zwiększyć regulowane limity z powyższej tabeli. 

Z płaszczyzny nawigacji w portalu Azure: 

1. Kliknij **pozycję Pomoc + obsługa**.
2. Kliknij **+ Nowe żądanie pomocy technicznej**.
3. Na karcie Podstawy podaj następujące informacje: 
    1. Typ problemu: Wybierz **limity usługi i subskrypcji (przydziały)**.
    2. Subskrypcje: Wybierz subskrypcję zasobu, który jest potrzebny przydział zwiększony.
    3. Typ przydziału: Wybierz **magazyn: Limity plików NetApp platformy Azure**.
    4. Kliknij **przycisk Dalej: Rozwiązania**.
4. Na karcie Szczegóły:
    1. W polu Opis podaj następujące informacje dotyczące odpowiedniego typu zasobu:

        |  Zasób  |    Zasoby nadrzędne      |    Żądane nowe limity     |    Powód zwiększenia kwot       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Subscription ID (Identyfikator subskrypcji)*   |  *Żądany nowy maksymalny numer **konta***    |  *Jaki scenariusz lub przypadek użycia monituje o żądanie?*  |
        |  Pula    |  *Identyfikator subskrypcji, identyfikator URI konta*  |  *Żądano nowego maksymalnego numeru **puli***   |  *Jaki scenariusz lub przypadek użycia monituje o żądanie?*  |
        |  Wolumin  |  *Identyfikator subskrypcji, identyfikator URI konta, identyfikator URI puli*   |  *Żądana nowa maksymalna liczba **woluminów***     |  *Jaki scenariusz lub przypadek użycia monituje o żądanie?*  |
        |  Pliki maxfiles  |  *Identyfikator subskrypcji, identyfikator URI konta, identyfikator URI puli, identyfikator URI woluminu*   |  *Żądana nowa maksymalna liczba **plików maksymalnych***     |  *Jaki scenariusz lub przypadek użycia monituje o żądanie?*  |    

    2. Określ odpowiednią metodę pomocy technicznej i podaj informacje o umowie.

    3. Kliknij **przycisk Dalej: Przejrzyj + utwórz,** aby utworzyć żądanie. 


## <a name="next-steps"></a>Następne kroki  

- [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Model kosztów usługi Azure NetApp Files](azure-netapp-files-cost-model.md)
