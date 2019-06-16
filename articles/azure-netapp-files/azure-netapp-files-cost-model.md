---
title: Model kosztów dla usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: W tym artykule opisano model kosztów dla usługi Azure Files NetApp zarządzania wydatki związane z usługi.
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
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: b06e3366224b90899dd3f9f9439edf897de82794
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65524223"
---
# <a name="cost-model-for-azure-netapp-files"></a>Model kosztów usługi Azure NetApp Files 

Omówienie modelu kosztów dla usługi Azure Files NetApp ułatwia zarządzanie wydatków z usługi.

## <a name="calculation-of-capacity-consumption"></a>Obliczanie zużycia pojemności

Usługa Azure Files NetApp jest rozliczana na pojemności aprowizowanego magazynu.  Zaprowizowaną pojemnością jest przydzielany, tworząc pule pojemności.  Pule pojemności są rozliczani według $/ aprowizowane GiB/miesiąc w przyrostach co godzinę. Minimalny rozmiar puli jednej pojemności to 4 TiB i pule pojemności można później rozszerzyć, w przyrostach co 1 TiB. Woluminy są tworzone w pulach pojemności.  Każdy wolumin jest przypisany przydział tego zmniejsza przy użyciu pojemności elastycznie w puli. Limit przydziału, który można przypisać do woluminów zakresów z co najmniej 100 GiB maksymalnie 92 TiB.  

Dla woluminu active zużycia pojemności dla limitu przydziału opiera się na logicznych pojemności (aktywne).

Użycie rzeczywistą wydajność, ilość przekracza przydział magazynowania, można nadal rosnąć woluminu. Zapisy nadal będzie możliwe, tak długo, jak rozmiar rzeczywisty woluminu jest mniejszy niż limit systemowy (100 TiB).  

Łączna pojemność używane w puli pojemności względem jego elastycznie kwota jest sumą jest większy niż przypisany przydział lub rzeczywiste zużycie wszystkie woluminy w puli: 

   ![Obliczenia całkowitej używana pojemność](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Poniższy diagram ilustruje te pojęcia.  
* Mamy pojemność puli za 4 TiB zaprowizowaną pojemnością.  Pula zawiera trzy woluminy.  
    * Wolumin 1 jest przypisany przydział 2 TiB i ma 800 GiB zużycia.  
    * Wolumin 2 jest przypisany przydział 1 TiB i ma 100 GiB zużycia.  
    * Wolumin 3 jest przypisany przydział 500 GiB, ale ma 800 GiB zużycia (nadwyżka).  
* Pojemność puli jest mierzone na 4 TiB pojemności (kwota elastycznie).  
    3.8 zużyciu TiB pojemności (2 TiB i 1 TiB przydziału z woluminów 1 i 2 oraz 800 GiB rzeczywistego zużycia dla woluminu 3). I pozostałego 200 GiB pojemności.

   ![Pojemność puli przy użyciu trzech woluminów](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Nadwyżkowe elementy w poziom zużycia pojemności  

Stosowania łączna pojemność puli przekracza jego zaprowizowaną pojemnością, opłata za zapisywanie danych są nadal dozwolone.  Po zakończeniu okresu prolongaty (jedna godzina) Jeśli używana pojemność puli nadal przekracza jego aprowizowaną pojemność, następnie rozmiar puli jest automatycznie zwiększana z przyrostem równym 1 TiB aż zaprowizowaną pojemnością jest większa niż łączna pojemność używane.  Na przykład na ilustracji powyżej, jeśli stale rośnie, Volume 3, a rzeczywiste zużycie osiągnie 1,2 TiB następnie po zakończeniu okresu prolongaty zostanie automatycznie być rozmiar puli wynosi 5 TiB.  Powoduje to, że pojemność puli elastycznie (5 TiB) przekracza używana pojemność (4.2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Ręczne zmiany rozmiaru puli  

Można ręcznie zwiększyć lub zmniejszyć rozmiar puli. Jednakże obowiązują następujące ograniczenia:
* Minimalne i maksymalne limity usługi  
    Zapoznaj się z artykułem [limity zasobów](azure-netapp-files-resource-limits.md).
* Inkrementacja 1 TiB, po początkowej TiB 4 minimalna wartość zakupu
* Godziny minimalne przyrostu rozliczeń
* Nie można zmniejszyć rozmiar puli elastycznie aby była ona mniejsza niż łączna liczba zużytych pojemności w puli.

## <a name="behavior-of-maximum-size-pool-overage"></a>Zachowanie nadwyżka maksymalny rozmiar puli   

Maksymalny rozmiar puli pojemność, która została utworzona, lub zmień rozmiar na wynosi 500 TiB.  Stosowania całkowitej pojemności w puli pojemności przekracza 500 TiB nastąpi następujących sytuacjach:
* Opłata za zapisywanie danych nadal będzie możliwe, (Jeśli wolumin jest poniżej systemu maksymalnie 100 TiB).
* Po upływie okresu jednej godziny puli zostanie automatycznie dopasowane w przyrostach co 1 TiB, dopóki pojemność puli aprowizowane przekracza łączna pojemność używane.
* Dodatkowe elastycznie i rozliczane pojemność puli, przekraczającej 500 TiB nie można używać do przypisywania limit przydziału woluminu. On również nie można rozwinąć limity QoS wydajności.  
    Zobacz [poziomów usług](azure-netapp-files-service-levels.md) o limity wydajności i rozmiaru QoS.

Poniższy diagram ilustruje te pojęcia:
* Mamy pojemności puli magazynu w warstwie Premium i pojemności 500 TiB. Pula zawiera dziewięć woluminów.
    * Od 1 do 8 woluminy są przypisywane przydziału 60 TiB.  Całkowita pojemność używane jest 480 TiB.  
        Każdy wolumin ma limit QoS 3,75 GiB/s przepływności (60 TiB * 64 MiB/s).  
    * Wolumin 9 jest przypisywany limit 20 TiB.  
        Wolumin 9 jest objęta limitem QoS 1,25 GiB/s przepływności (60 TiB * 64 MiB/s).
* Wolumin 9 jest użycie nadwyżkowe scenariusza. Ma ona 25 TiB rzeczywistego wykorzystania.  
    * Po upływie okresu jednej godziny będą rozmiar puli pojemności 505 TiB.  
        Oznacza to, że łączna liczba używana pojemność = 8 * 60-przydziału TiB dla woluminów od 1 do 8, a 25 TiB rzeczywiste zużycie 9 woluminu.
    * Billed pojemność to 505 TiB.
    * Nie można zwiększyć limit przydziału woluminu dla woluminów, 9, (ponieważ jest to łączna liczba przypisany przydział dla puli nie może przekraczać 500 TiB).
    * Nie można przypisać dodatkowej przepływności QoS (ponieważ jest to łączna liczba zasad QoS dla tej puli nadal opiera się na 500 TiB).

   ![Pojemność puli z dziewięciu woluminów](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Użycie pojemności migawek 

Poziom zużycia pojemności migawek w usłudze Azure Files NetApp jest obciążana za limit przydziału woluminu nadrzędnego.  W rezultacie udziałów rozliczeń odzyskującą pojemność puli, do której należy dany wolumin.  Jednak w przeciwieństwie do woluminu aktywnego zużycie migawki jest mierzony na podstawie pojemności przyrostowe używane.  Usługa Azure migawki plików NetApp są różnicowej charakter. W zależności od szybkości zmian danych migawek często zużywać znacznie mniej miejsca niż logiczne pojemność woluminu aktywnego. Na przykład załóżmy, że masz migawki woluminu 500 GiB, która zawiera tylko 10 GiB danych różnicowych. Pojemność, która jest obciążana za limit przydziału woluminu dla tej migawki może być 10 GiB, nie 500 GiB. 

## <a name="next-steps"></a>Kolejne kroki

* [Usługa Azure Files NetApp, cennik](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
