---
title: Model kosztów dla plików NetApp platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano model kosztów dla usługi Azure NetApp Files do zarządzania wydatkami z usługi.
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
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70995115"
---
# <a name="cost-model-for-azure-netapp-files"></a>Model kosztów usługi Azure NetApp Files 

Opis modelu kosztów dla usługi Azure NetApp Files ułatwia zarządzanie wydatkami z usługi.

## <a name="calculation-of-capacity-consumption"></a>Obliczanie zużycia zdolności produkcyjnych

Usługa Azure NetApp Files jest rozliczana z aprowizowanego pojemność magazynu.  Aprowizowana pojemność jest przydzielana przez tworzenie puli pojemności.  Pule pojemności są rozliczane na podstawie $/provisioned-GiB/month w odstępach godzinowych. Minimalny rozmiar dla pojedynczej puli pojemności wynosi 4 TiB, a pule pojemności można następnie rozszerzyć w przyrostach 1-TiB. Woluminy są tworzone w pulach pojemności.  Każdemu woluminowi jest przypisywany przydział, który zmniejsza się z pojemności aprowizacji pukonom. Przydział, który można przypisać do woluminów, waha się od co najmniej 100 GiB do maksymalnie 100 TiB.  

W przypadku aktywnej objętości zużycie zdolności produkcyjnych względem przydziału opiera się na pojemności logicznej (skutecznej).

Jeśli rzeczywiste zużycie pojemności woluminu przekracza jego przydział magazynowania, wolumin może nadal rosnąć. Zapisy będą nadal dozwolone, o ile rzeczywisty rozmiar woluminu jest mniejszy niż limit systemowy (100 TiB).  

Całkowita wykorzystana zdolność produkcyjna w puli zdolności produkcyjnych w stosunku do jej aprowizowana kwota jest sumą większej z przypisanego przydziału lub rzeczywistego zużycia wszystkich woluminów w puli: 

   ![Obliczanie całkowitej wykorzystanej zdolności produkcyjnej](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Poniższy diagram ilustruje te pojęcia.  
* Mamy pulę pojemności z 4 TiB aproważnej pojemności.  Pula zawiera trzy woluminy.  
    * Tom 1 jest przypisany przydział 2 TiB i ma 800 GiB zużycia.  
    * Tom 2 jest przypisany do przydziału 1 TiB i ma 100 GiB zużycia.  
    * Tom 3 jest przypisany przydział 500 GiB, ale ma 800 GiB zużycia (overage).  
* Pula pojemności jest mierzona dla 4 TiB pojemności (kwota aprowizowana).  
    3.8 Zużywa się moc TiB (2 TiB i 1 TiB kontyngentu z tomów 1 i 2 oraz 800 GiB rzeczywistego zużycia dla objętości 3). I 200 GiB zdolności pozostaje.

   ![Pula pojemności z trzema woluminami](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Nadmierna wydajność  

Gdy całkowita pojemność używanej puli przekracza jego pojemność aprowizowana, zapisy danych są nadal dozwolone.  Po okresie prolongaty (jedna godzina), jeśli wykorzystana pojemność puli nadal przekracza jej aprowizowana pojemność, rozmiar puli zostanie automatycznie zwiększony w przyrostach 1 TiB, dopóki aprowizowana pojemność nie będzie większa niż całkowita wykorzystana pojemność.  Na przykład na powyższej ilustracji, jeśli tom 3 nadal rośnie, a rzeczywiste zużycie osiągnie 1,2 TiB, a następnie po okresie prolongaty pula zostanie automatycznie przesunięta się na 5 TiB.  W rezultacie pojemność puli aprowizowana (5 TiB) przekracza używaną pojemność (4,2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Ręczne zmiany rozmiaru basenu  

Można ręcznie zwiększyć lub zmniejszyć rozmiar puli. Obowiązują jednak następujące ograniczenia:
* Minimalne i maksymalne limity usług  
    Zobacz artykuł o [limitach zasobów](azure-netapp-files-resource-limits.md).
* Przyrost 1-TiB po początkowym minimalnym zakupie 4-TiB
* Minimalny przyrost rozliczeń o jedną godzinę
* Rozmiar puli aprowizacji nie może być zmniejszona do mniej niż całkowita liczba wykorzystanych w puli.

## <a name="behavior-of-maximum-size-pool-overage"></a>Zachowanie maksymalnej wielkości przekroju puli   

Maksymalny rozmiar puli pojemności, do której można utworzyć lub zmienić jego rozmiar, wynosi 500 TiB.  Gdy całkowita ilość zużytej pojemności w puli pojemności przekracza 500 TiB, wystąpią następujące sytuacje:
* Zapisy danych będą nadal dozwolone (jeśli objętość jest poniżej maksymalnej wartości systemu 100 TiB).
* Po godzinnym okresie prolongaty pula zostanie automatycznie przesięknięta w przyrostach 1-TiB, dopóki pojemność aprowizowana puli nie przekroczy całkowitej używanej pojemności.
* Dodatkowe aprowizowanych i rozliczanych pojemności puli przekraczającej 500 TiB nie można użyć do przypisania przydziału woluminu. Nie można również użyć do rozszerzenia limitów QoS wydajności.  
    Zobacz [poziomy usług](azure-netapp-files-service-levels.md) dotyczące limitów wydajności i rozmiaru QoS.

Poniższy diagram ilustruje następujące pojęcia:
* Mamy pulę pojemności z warstwą pamięci masowej Premium i pojemnością 500 TiB. Pula zawiera dziewięć tomów.
    * Woluminom od 1 do 8 przypisano przydział 60 TiB każdy.  Całkowita zużyta pojemność wynosi 480 TiB.  
        Każdy wolumin ma limit QoS 3,75 GiB/s przepustowości (60 TiB * 64 MiB/s).  
    * Tom 9 jest przypisany przydział 20 TiB.  
        Tom 9 ma limit QoS 1,25 GiB/s przepustowości (20 TiB * 64 MiB/s).
* Tom 9 jest scenariuszem przeceniania. Ma 25 TiB rzeczywistego zużycia.  
    * Po godzinnym okresie prolongaty pula pojemności zostanie przesuń na 505 TiB.  
        Oznacza to, że całkowita zużyta pojemność = 8 * 60-TiB przydział dla woluminów od 1 do 8 i 25 TiB rzeczywistego zużycia dla woluminu 9.
    * Pojemność naliczona wynosi 505 TiB.
    * Nie można zwiększyć przydziału woluminu dla woluminu 9 (ponieważ całkowity przydział przypisany dla puli nie może przekraczać 500 TiB).
    * Dodatkowa przepustowość QoS może nie być przypisana (ponieważ całkowita wartość QoS dla puli jest nadal oparta na 500 TiB).

   ![Pula pojemności z dziewięcioma woluminami](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Zużycie pojemności migawek 

Zużycie pojemności migawek w usłudze Azure NetApp Files jest naliczane od przydziału woluminu nadrzędnego.  W rezultacie współudzi to samo stawki rozliczeniowe co pula pojemności, do której należy wolumin.  Jednak w przeciwieństwie do woluminu aktywnego zużycie migawki jest mierzone na podstawie zużywanej zdolności przyrostowej.  Migawki plików NetApp platformy Azure mają charakter różnicowy. W zależności od szybkości zmiany danych migawki często zużywają znacznie mniejszą pojemność niż pojemność logiczna aktywnego woluminu. Załóżmy na przykład, że masz migawkę woluminu 500-GiB, który zawiera tylko 10 GiB danych różnicowych. Pojemność, która jest obciążona przydziałem woluminu dla tej migawki będzie 10 GiB, a nie 500 GiB. 

## <a name="next-steps"></a>Następne kroki

* [Strona z cennikiem usługi Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
