---
title: Zagadnienia dotyczące wydajności dla usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: W tym artykule opisano zagadnienia dotyczące wydajności dla usługi Azure Files NetApp.
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
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454141"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Kwestie dotyczące wydajności dla usługi Azure NetApp Files

[Limit przepływności](azure-netapp-files-service-levels.md) dla woluminu jest określane przez połączenie przydziału przypisanych do woluminu i poziom wybranej usługi. Po wprowadzeniu planów wydajności dotyczące usługi Azure Files NetApp, należy zrozumieć kilka zagadnień. 

## <a name="quota-and-throughput"></a>Limit przydziału i przepływności  

Limit przepływności jest tylko jeden wyznacznik rzeczywistej wydajności, która zostanie zrealizowana.  

Magazyn typowe zagadnienia dotyczące wydajności, łącznie z zapisu i odczytu mieszanego, rozmiar transferu, losowych lub sekwencyjnych wzorców i wieloma innymi czynnikami przyczyni się do wydajności całkowitej dostarczane.  

Maksymalna przepływność empiryczne obserwowanej podczas testowania jest 4 500 MiB/s.  W warstwie magazynu Premium limit przydziału woluminu z 70.31 TiB przydzieli limit przepustowości, która jest wystarczająco wysoka, aby osiągnąć ten poziom wydajności.  

Jeśli rozważasz przypisywanie woluminu kwoty przydziału poza 70.31 TiB dodatkowego przydziału można przypisać wolumin do przechowywania dodatkowych danych. Jednak dodano limitu przydziału nie spowoduje dalsze zwiększenie rzeczywista przepływność.  

Zobacz [wydajności testy dla usługi Azure Files NetApp](azure-netapp-files-performance-benchmarks.md) Aby uzyskać dodatkowe informacje.

## <a name="overprovisioning-the-volume-quota"></a>Celi limit przydziału woluminu

Jeśli wydajność obciążenia jest powiązana limit przepływności, istnieje możliwość overprovision limit przydziału woluminu wyższego poziomu przepływności i uzyskać lepszą wydajność.  

Na przykład, jeśli wolumin w warstwie Premium storage ma tylko 500 GiB danych, ale wymaga 128 MiB/s przepływności, możesz ustawić limit przydziału na 2 TiB tak, aby poziom przepływności odpowiednio (64 MiB/s na TB * 2 TiB = 128 MiB/s).  

Jeśli wolumin do osiągnięcia wyższej przepływności można spójnie overprovision, wyższego poziomu usługi zamiast tego Rozważ użycie.  W powyższym przykładzie można osiągnąć ten sam limit przepływności o połowę limit przydziału woluminu przy użyciu warstwy magazynowania Ultra zamiast (128 MiB/s na TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dynamicznie zwiększenie lub zmniejszenie limit przydziału woluminu

Czy wymagania dotyczące wydajności są w istocie tymczasowe, czy została zwiększona wymagań dotyczących wydajności w ustalonym czasie, dynamicznie można zwiększyć lub zmniejszyć limit przydziału woluminu, aby natychmiast dostosować limit przepływności.  Należy zwrócić uwagę następujące kwestie: 

* Limit przydziału woluminu można zwiększyć lub zmniejszyć bez konieczności wstrzymywanie operacji We/Wy i dostęp do woluminu zostanie przerwana lub nie dotyczy.  

    Można dostosować przydziału podczas aktywnej transakcji we/wy dla woluminu.  Należy pamiętać, że ten limit przydziału woluminu nigdy nie można zmniejszyć poniżej ilość danych logicznych, która jest przechowywana w woluminie.

* Gdy limit przydziału woluminu zostanie zmieniona, analogiczna zmiana limit przepływności jest prawie natychmiastowe. 

    Zmiana przerwań lub nie mieć wpływ na dostęp do woluminu lub operacji We/Wy.  

* Dostosowywanie limit przydziału woluminu wymaga zmian w zakresie rozmiaru puli pojemności.  

    Dynamiczne i bez wywierania wpływu na dostępność woluminu lub we/wy można dostosować rozmiar pojemności w puli.

## <a name="next-steps"></a>Kolejne kroki

- [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Testy porównawcze wydajności dla usługi Azure Files NetApp](azure-netapp-files-performance-benchmarks.md)