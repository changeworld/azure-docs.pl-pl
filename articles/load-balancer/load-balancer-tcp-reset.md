---
title: Load Balancer Resetowanie protokołu TCP przy bezczynności na platformie Azure
titlesuffix: Azure Load Balancer
description: Load Balancer z dwukierunkowym pakietem TCP RST przy limicie czasu bezczynności
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
ms.openlocfilehash: 8485f4b6e8d4ff55de4930b3cfb7a07802cf1d41
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274155"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Load Balancer z resetowaniem protokołu TCP przy bezczynności (publiczna wersja zapoznawcza)

Za pomocą [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md) można utworzyć bardziej przewidywalne zachowanie aplikacji dla Twoich scenariuszy przez włączenie resetowania protokołu TCP dla danej reguły. Domyślne zachowanie Load Balancer polega na dyskretnym porzucaniu przepływów, gdy zostanie osiągnięty limit czasu bezczynności przepływu.  Włączenie tej funkcji spowoduje, że Load Balancer wysyłać dwukierunkowe Resetowanie protokołu TCP (pakiety TCP RST) przy limicie czasu bezczynności.  Spowoduje to wyświetlenie punktów końcowych aplikacji, dla których upłynął limit czasu połączenia i nie będzie już można go używać.  Punkty końcowe mogą natychmiast ustanowić nowe połączenie, jeśli jest to możliwe.

![Load Balancer Resetowanie protokołu TCP](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Load Balancer z funkcją resetowania protokołu TCP na czas bezczynności jest teraz dostępna jako publiczna wersja zapoznawcza. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Należy zmienić to zachowanie domyślne i włączyć opcję wysyłania resetowania protokołu TCP przy bezczynności, aby włączyć przychodzące reguły NAT, reguły równoważenia obciążenia i [reguły ruchu](https://aka.ms/lboutboundrules)wychodzącego.  Po włączeniu dla każdej reguły, Load Balancer wyśle dwukierunkowe Resetowanie TCP (pakiety TCP RST) do punktów końcowych klienta i serwera w czasie bezczynności dla wszystkich zgodnych przepływów.

Punkty końcowe odbierające pakiety TCP RST zamykają odpowiednie gniazda natychmiast. Zapewnia to natychmiastowe powiadomienie do punktów końcowych, w których wystąpiło wydanie połączenia, i wszelka przyszła komunikacja z tym samym połączeniem TCP zakończy się niepowodzeniem.  Aplikacje mogą przeczyścić połączenia, gdy gniazdo zamknie i ponownie nawiąże połączenia w razie potrzeby bez oczekiwania na zakończenie limitu czasu połączenia TCP.

W przypadku wielu scenariuszy może to zmniejszyć konieczność wysłania aktywności protokołu TCP (lub warstwy aplikacji) w celu odświeżenia czasu bezczynności przepływu. 

Jeśli czasy trwania bezczynności przekraczają liczbę dozwolonych przez konfigurację lub aplikacja pokazuje niepożądane zachowanie z włączonym resetowaniem protokołu TCP, może być nadal konieczne użycie obsługi protokołu TCP (lub utrzymywania warstwy aplikacji) w celu monitorowania aktywności połączeń TCP.  Ponadto utrzymywanie aktywności może być również przydatne w przypadku, gdy połączenie jest nawiązywane z serwerem proxy w ścieżce, szczególnie w przypadku warstwy aplikacji.  

Uważnie Przeanalizuj cały kompleksowy scenariusz, aby zdecydować, czy można włączyć Resetowanie protokołu TCP, dostosować limit czasu bezczynności, a jeśli wymagane są dodatkowe kroki, aby zapewnić odpowiednie zachowanie aplikacji.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Włączanie resetowania protokołu TCP po upływie limitu czasu bezczynności

Korzystając z interfejsu API w wersji 2018-07-01, można włączyć wysyłanie dwukierunkowych resetów TCP przy bezczynności dla każdej reguły:

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

## <a name="regions"></a>Dostępność regionów

Dostępne we wszystkich regionach.

## <a name="limitations"></a>Ograniczenia

- Nie można użyć portalu do konfigurowania lub wyświetlania resetowania protokołu TCP.  Zamiast tego użyj szablonów, interfejs API REST, Az interfejsu wiersza polecenia w wersji 2.0 lub programu PowerShell.
- Parametr RST jest wysyłany tylko podczas połączenia TCP w stanie USTANOWIONym.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia](load-balancer-standard-overview.md).
- Poznaj [reguły ruchu](load-balancer-outbound-rules-overview.md)wychodzącego.
