---
title: Moc obliczeniową planowania dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Więcej informacji na temat możliwości obliczeniowych, planowanie wdrożenia usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 04/03/2019
ms.custom: ''
ms.openlocfilehash: 437e55b1a2907418fe47f418245431fa1c882b80
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915690"
---
# <a name="azure-stack-compute-capacity-planning"></a>Usługa Azure Stack obliczeń, planowania pojemności
[Rozmiarów maszyn wirtualnych obsługiwanych w usłudze Azure Stack](./user/azure-stack-vm-sizes.md) są podzbiorem obsługiwane na platformie Azure. Azure nakłada ograniczenia zasobów wzdłuż wektorów wiele, aby uniknąć nadmierne zużycie zasobów (serwera lokalnego i poziomu usług). Bez nakładania pewne ograniczenia użycia dzierżawy, środowisk dzierżawy będzie niekorzystnie wpłynąć na w przypadku pozostałych dzierżaw overconsume zasobów. Dla danych sieciowych wychodzących z maszyny Wirtualnej istnieją limity przepustowości w miejscu w usłudze Azure Stack, które odpowiadają ograniczenia platformy Azure. Dla zasobów magazynu limity operacji We/Wy magazynu zostały wdrożone w usłudze Azure Stack, aby uniknąć podstawowe nadmierne zużycie zasobów przez dzierżawy, aby uzyskać dostęp do magazynu.  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>Umieszczanie maszyny Wirtualnej i celi rdzeń wirtualny na fizycznych
W usłudze Azure Stack nie ma możliwości dla dzierżawy określić określonego serwera na potrzeby umieszczenia maszyny Wirtualnej. Jedyna kwestią podczas umieszczania maszyn wirtualnych to, czy jest wystarczająca ilość pamięci na hoście dla tego typu maszyny Wirtualnej. Usługa Azure Stack nie overcommit pamięci. jednak overcommit liczby rdzeni jest dozwolone. Ponieważ algorytmy umieszczania wyglądają na istniejący wirtualny na fizycznych celi rdzeń jako składnik, każdy host może mieć inny wskaźnik. 

Na platformie Azure do zapewniania wysokiej dostępności w systemie produkcyjnym wielu maszyn wirtualnych, maszyny wirtualne są umieszczane w zestaw dostępności, aby zostać rozmieszczone w wielu domenach błędów. W usłudze Azure Stack domeny błędów w zestawie dostępności jest zdefiniowany jako jeden węzeł w jednostce skalowania.

Infrastruktury Azure Stack jest odporna na awarie, podstawową używaną technologią (klaster trybu failover) nadal jest naliczana pewien przestój w przypadku maszyn wirtualnych na serwerze fizycznym nimi objęte w przypadku awarii sprzętu. Obecnie usługa Azure Stack obsługuje o dostępności zestawu z maksymalnie trzy domeny błędów, aby były zgodne z platformą Azure. Maszyny wirtualne umieszczone w zestawie dostępności będą fizycznie odizolowane od siebie nawzajem Dzięki rozdzieleniu je możliwie najbardziej równomiernie przez wiele domen błędów (węzłów usługi Azure Stack). W przypadku awarii sprzętu maszyn wirtualnych na podstawie domena błędów nie powiodło się będzie można ponownie uruchomiony w ramach innych węzłów, ale jeśli to możliwe, przechowywane w oddzielnych domenach błędów z innymi maszynami wirtualnymi w tym samym zestawie dostępności. Gdy sprzęt powróci do trybu online, maszyn wirtualnych zostanie ponownie zbilansowana do utrzymania wysokiej dostępności.

Inny pojęcia, które jest używane przez platformę Azure w celu zapewnienia wysokiej dostępności jest w formie domeny aktualizacji w zestawach dostępności. Domena aktualizacji to logiczne grupowanie odpowiednich elementów sprzętu, które mogą być w tym samym czasie poddawane konserwacji lub ponownie uruchamiane. W usłudze Azure Stack maszyny wirtualne działają migracji w trybie online hosty w klastrze, przed ich odpowiedniego hosta jest aktualizowana. Ponieważ podczas aktualizacji hosta bez przestojów dzierżawy, funkcji domeny aktualizacji w usłudze Azure Stack występuje tylko w przypadku szablonu zgodności z platformą Azure.

## <a name="azure-stack-resiliency-resources"></a>Zasoby odporności platformy Azure Stack
Aby umożliwić poprawek i aktualizacji usługi Azure Stack zintegrowany system i aby była odporna na awarie sprzętu fizycznego, część pamięci całego serwera jest zarezerwowane i niedostępne do umieszczania maszyn wirtualnych (VM) dzierżawy.

Jeśli serwer ulegnie awarii, maszyny wirtualne hostowane na serwerze nie powiodło się zostanie ponownie uruchomiony na pozostałych, które są dostępne serwery w celu zapewnienia dostępności maszyny Wirtualnej. Podobnie podczas procesu poprawek i aktualizacji wszystkich maszyn wirtualnych uruchomionych na serwerze zostanie się na żywo poddana migracji do innego serwera dostępne. Tej maszyny Wirtualnej zarządzania lub przenoszenia może odbyć się tylko w przypadku rezerwowanie pojemności, aby umożliwić ponowne uruchomienie lub migracji nastąpi.

Następujące obliczenia powoduje całkowita, która jest dostępna pamięć, która może służyć do umieszczenia maszyn wirtualnych dzierżawcy. Jest to pojemność pamięci dla całości jednostki skalowania usługi Azure Stack.

  Dostępna pamięć do umieszczenia maszyny Wirtualnej = łączna ilość pamięci serwera — odporność rezerwy — użycie pamięci przez uruchamianie maszyn wirtualnych — Azure Stack infrastruktury obciążenie <sup>1</sup>

  Rezerwa odporności = H + R, * ((N-1) * H) + V * (N-2)

> Gdzie:
> - H = rozmiar pamięci na jednym serwerze
> - N = rozmiar jednostki skalowania (liczba serwerów)
> - R = rezerwy systemu operacyjnego na obciążenie systemu operacyjnego<sup>2</sup>
> - V = największą maszynę Wirtualną w jednostce skalowania

  <sup>1</sup> infrastruktury azure Stack obciążenie = 230 GB

  <sup>2</sup> rezerwy systemu operacyjnego na obciążenie = 15% tego węzła pamięci. System operacyjny wartość jest szacowana i będzie zależeć od pojemności pamięci fizycznej serwera i koszty ogólne systemu operacyjnego.

Wartość V, największą maszynę Wirtualną w jednostce skalowania opiera się dynamicznie w dzierżawie największy rozmiar pamięci maszyny Wirtualnej. Na przykład największą wartość maszyny Wirtualnej może być, 7 GB lub 112 GB lub wszystkie inne obsługiwane rozmiar maszyny Wirtualnej pamięci w rozwiązaniu usługi Azure Stack.

Powyższe obliczenie jest szacunkowe i ulegną zmianie na podstawie bieżącej wersji usługi Azure Stack. Możliwość wdrażania usług i maszyn wirtualnych dzierżawy opiera się na szczegółowe informacje na temat wdrożonym rozwiązaniem. To obliczenie przykład jest tylko przewodnik i nie bezwzględne odpowiedź umożliwia wdrażanie maszyn wirtualnych.



## <a name="next-steps"></a>Kolejne kroki
[Planowanie pojemności magazynu](capacity-planning-storage.md)
