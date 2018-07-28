---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 7/26/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: fc98206b5940ce75ddf26cfd905b1e0a89e2d85c
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39326248"
---
| | Standardowy SKU | Podstawowy SKU |
| --- | --- | --- |
| Rozmiar puli zaplecza | Obsługuje maksymalnie 1000 wystąpień | Obsługuje maksymalnie 100 wystąpień |
| Punkty końcowe puli zaplecza | Każda maszyna wirtualna w jednej sieci wirtualnej, program blend maszyn wirtualnych i zestawów dostępności w tym zestawy skalowania maszyn wirtualnych. | Ustaw maszyn wirtualnych w skali dostępności pojedynczy zestaw lub maszyny wirtualnej. |
| Strefy dostępności | W ramach podstawowej jednostki SKU strefowo nadmiarowy i strefowych frontonów dla ruchu przychodzącego i przetrwać awarie strefy, równoważenia obciążenia międzystrefowego przepływy ruchu wychodzącego, wychodzący mapowania. | Nie dotyczy|
| Diagnostyka | Usługa Azure Monitor metryk wielowymiarowych łącznie bajtów i pakietów liczników, kondycja sondowania stanu, próby nawiązania połączenia (TCP SYN), kondycja połączenia wychodzące (SNAT udane i nieudane przepływami), pomiarów płaszczyzny danych active | Usługa Azure Log Analytics dla publicznej usługi Load Balancer tylko alert wyczerpania SNAT, liczba kondycji puli zaplecza. |
| Zaświadczanie o kondycji portów | Wewnętrzny moduł równoważenia obciążenia | Nie dotyczy |
| Zabezpieczanie domyślnie | Domyślne zamknięte dla publicznych punktów końcowych adresów IP i równoważenia obciążenia i sieciowej grupy zabezpieczeń należy jawnie dozwolonych dla ruchu do przepływu. | Otwórz domyślna sieciowa grupa zabezpieczeń jest opcjonalne. |
| [Połączenia wychodzące](../articles/load-balancer/load-balancer-outbound-connections.md) | Wiele frontonów za pomocą poszczególnych rezygnacji reguły równoważenia obciążenia. Scenariusz wychodzącego _musi_ jawnie tworzyć dla maszyny wirtualnej można było korzystać z łączności wychodzącej.  Punkty końcowe usługi sieci wirtualnej jest osiągalna bez łączności wychodzącej i są wliczane przetworzonych danych.  Wszystkie publiczne adresy IP, łącznie z usługami PaaS platformy Azure nie jest dostępna jako punkty końcowe usługi sieci wirtualnej, musi być dostępny za pośrednictwem połączenia wychodzącego i liczba kierunku przetworzonych danych. Połączenia wychodzące przy użyciu domyślnego SNAT tylko wewnętrznego modułu równoważenia obciążenia działa jako maszynę wirtualną, nie są dostępne. Wychodzące programowania SNAT jest protokół transportowy określonych oparta na protokole reguły równoważenia obciążenia dla ruchu przychodzącego. | Fronton pojedynczego wybranych losowo, jeśli podano wiele frontonów.  Gdy tylko wewnętrznego modułu równoważenia obciążenia działa jako maszyna wirtualna, używana jest domyślna SNAT. |
| [Wiele frontonów](../articles/load-balancer/load-balancer-multivip-overview.md) | Dla ruchu przychodzącego i [ruchu wychodzącego](../articles/load-balancer/load-balancer-outbound-connections.md) | Tylko transfer przychodzący |
| [Sonda kondycji zachowanie w dół](../articles/load-balancer/load-balancer-custom-probe-overview.md) | Połączenia TCP pozostaną aktywne na wystąpienie sondy w dół __i__ na wszystkich sondy w dół. | TCP połączenia pozostaną aktywne na wystąpienie sondy w dół. Wszystkie połączenia TCP zakończenie na wszystkich sondy w dół. |
| Operacje zarządzania | Większość operacji < 30 sekund | 60-ponad 90 sekund typowy. |
| Umowa SLA | dostępność przez 99,99% dla ścieżki danych za pomocą dwóch dobrej kondycji maszyn wirtualnych. | Niejawne w umowie SLA maszyn wirtualnych. | 
| Cennik | Naliczane na podstawie liczby reguł, przetworzone dane przychodzące lub wychodzące skojarzonych zasobów.  | Brak opłat |
|  |  |  |