---
title: Zagadnienia dotyczące wydajności plików NetApp platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano zagadnienia dotyczące wydajności plików NetApp platformy Azure.
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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67454141"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Kwestie dotyczące wydajności dla usługi Azure NetApp Files

[Limit przepływności](azure-netapp-files-service-levels.md) dla woluminu jest określany przez kombinację przydziału przypisanego do woluminu i wybranego poziomu usług. Podczas tworzenia planów wydajności dotyczących plików NetApp platformy Azure należy zapoznać się z kilkoma zagadnieniami. 

## <a name="quota-and-throughput"></a>Przydział i przepływność  

Limit przepływności jest tylko jednym wyznacznikiem rzeczywistej wydajności, która zostanie zrealizowana.  

Typowe zagadnienia dotyczące wydajności magazynu, w tym mikst odczytu i zapisu, rozmiar transferu, losowe lub sekwencyjne wzorce i wiele innych czynników przyczyni się do całkowitej wydajności.  

Maksymalna przepustowość empiryczna zaobserwowana podczas testów wynosi 4500 MiB/s.  W warstwie magazynu w warstwie Premium przydział woluminu 70,31 TiB udostępni limit przepływności, który jest wystarczająco wysoki, aby osiągnąć ten poziom wydajności.  

Jeśli rozważasz przypisanie kwot przydziału woluminu powyżej 70,31 TiB, dodatkowy przydział może zostać przypisany do woluminu w celu przechowywania dodatkowych danych. Jednak dodatkowy przydział nie spowoduje dalszego wzrostu rzeczywistej przepływności.  

Aby uzyskać dodatkowe informacje, zobacz [wskaźniki wydajności dla plików NetApp usługi Azure.](azure-netapp-files-performance-benchmarks.md)

## <a name="overprovisioning-the-volume-quota"></a>Nadmierna aprowizowanie przydziału woluminu

Jeśli wydajność obciążenia jest związana z limitem przepływności, możliwe jest przepros urozdział przydziału woluminu, aby ustawić wyższy poziom przepływności i osiągnąć wyższą wydajność.  

Na przykład jeśli wolumin w warstwie magazynu w warstwie Premium ma tylko 500 GiB danych, ale wymaga 128 MiB/s przepływności, można ustawić przydział na 2 TiB, tak aby odpowiednio ustawiony poziom przepływności (64 MiB/s na TB * 2 TiB = 128 MiB/s).  

Jeśli konsekwentnie overprovision woluminu w celu osiągnięcia wyższej przepływności, należy rozważyć użycie wyższego poziomu usług zamiast tego.  W powyższym przykładzie można osiągnąć ten sam limit przepływności z połową przydziału woluminu przy użyciu warstwy magazynu Ultra zamiast (128 MiB/s na TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dynamicznie zwiększający lub zmniejszający się przydział wolumenu

Jeśli wymagania dotyczące wydajności mają charakter tymczasowy lub jeśli masz zwiększone zapotrzebowanie na wydajność przez określony czas, można dynamicznie zwiększyć lub zmniejszyć przydział woluminu, aby natychmiast dostosować limit przepływności.  Należy zwrócić uwagę na następujące kwestie: 

* Przydział woluminu można zwiększyć lub zmniejszyć bez konieczności wstrzymywania we/wy, a dostęp do woluminu nie jest przerywany ani wpływa.  

    Przydział można skorygować podczas aktywnej transakcji we/wy względem woluminu.  Należy zauważyć, że przydział woluminu nigdy nie może być zmniejszony poniżej ilości danych logicznych przechowywanych w woluminie.

* Po zmianie przydziału woluminu odpowiednia zmiana limitu przepływności jest niemal natychmiastowa. 

    Zmiana nie przerywa ani nie wpływa na dostęp do woluminu ani we/wy.  

* Dostosowanie przydziału woluminu wymaga zmiany rozmiaru puli pojemności.  

    Rozmiar puli pojemności można regulować dynamicznie i bez wpływu na dostępność woluminu lub we/wy.

## <a name="next-steps"></a>Następne kroki

- [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Testy porównawcze wydajności dla usługi Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)