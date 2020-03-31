---
title: Resetowanie TCP modułu równoważenia obciążenia na bezczynniu na platformie Azure
titleSuffix: Azure Load Balancer
description: W tym artykule dowiedz się więcej o równoważącym obciążenia platformy Azure z dwukierunkowymi pakietami RST TCP na podstawie limitu czasu bezczynnego.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: d3d836ddea8d07a25ad09e6f19d9f17a680decd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294402"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Moduł równoważenia obciążenia z resetowaniem TCP na bezczynniu

Standardowego [modułu równoważenia obciążenia](load-balancer-standard-overview.md) można użyć do utworzenia bardziej przewidywalnego zachowania aplikacji dla scenariuszy, włączając resetowanie TCP na bezczynności dla danej reguły. Domyślnym zachowaniem modułu równoważenia obciążenia jest dyskretne upuszczanie przepływów po osiągnięciu limitu czasu bezczynności przepływu.  Włączenie tej funkcji spowoduje, że moduł równoważenia obciążenia będzie wysyłał dwukierunkowe resetowanie TCP (pakiet TCP RST) przy po przekroju czasu bezczynnego.  Spowoduje to poinformowanie punktów końcowych aplikacji, że połączenie zostało przesunął limit czasu i nie jest już użyteczny.  Punkty końcowe można natychmiast ustanowić nowe połączenie w razie potrzeby.

![Resetowanie TCP modułu równoważenia obciążenia](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
Zmień to zachowanie domyślne i włączysz wysyłanie resetów TCP na podstawie limitu czasu bezczynnego na przychodzących regułach TRANSLATORA, regułach równoważenia obciążenia i [regułach wychodzących](https://aka.ms/lboutboundrules).  Po włączeniu według reguły moduł równoważenia obciążenia wysyła dwukierunkowe resetowanie TCP (pakiety TCP RST) zarówno do punktów końcowych klienta, jak i serwera w czasie limitu czasu bezczynności dla wszystkich pasujących przepływów.

Punkty końcowe odbierające pakiety TCP RST natychmiast zamykają odpowiednie gniazdo. Zapewnia to natychmiastowe powiadomienie o punktach końcowych, że wystąpiło zwolnienie połączenia, a wszelkie przyszłe komunikaty dotyczące tego samego połączenia TCP nie powiodą się.  Aplikacje mogą przeczyścić połączenia, gdy gniazdo zostanie zamknięte i ponownie nawiązują połączenia zgodnie z potrzebami, bez oczekiwania na przesunienie czasu połączenia TCP.

W wielu scenariuszach może to zmniejszyć konieczność wysyłania TCP (lub warstwy aplikacji) keepalives odświeżyć czas bezczynności przepływu. 

Jeśli czas trwania bezczynności przekracza czas trwania dozwolony przez konfigurację lub aplikacja pokazuje niepożądane zachowanie z włączonymi resetowaniami TCP, może być konieczne użycie funkcji keepalives TCP (lub zachowaalives warstwy aplikacji) do monitorowania żywotności połączeń TCP.  Ponadto keepalives może również pozostać przydatne, gdy połączenie jest bliższe gdzieś w ścieżce, szczególnie keepalives warstwy aplikacji.  

Dokładnie zbadać cały scenariusz końca do końca, aby zdecydować, czy można korzystać z włączania resetowania TCP, dostosowując limit czasu bezczynność i czy dodatkowe kroki mogą być wymagane w celu zapewnienia żądanego zachowania aplikacji.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Włączanie resetowania TCP przy po przekroczeniem limitu czasu bezczynnego

Korzystając z wersji interfejsu API 2018-07-01, można włączyć wysyłanie dwukierunkowych resetów TCP na podstawie limitu czasu bezczynności na podstawie reguły:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="region-availability"></a><a name="regions"></a>Dostępność regionu

Dostępne we wszystkich regionach.

## <a name="limitations"></a>Ograniczenia

- Protokół TCP RST wysyłany tylko podczas połączenia TCP w stanie ESTABLISHED.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [standardowym równoważącym obciążenia](load-balancer-standard-overview.md).
- Dowiedz się więcej o [regułach ruchu wychodzącego](load-balancer-outbound-rules-overview.md).
- [Konfigurowanie protokołu TCP RST przy przekroju czasu bezczynnego](load-balancer-tcp-idle-timeout.md)
