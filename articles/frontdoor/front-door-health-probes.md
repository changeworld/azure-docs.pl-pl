---
title: Usługa frontonu platformy Azure — monitorowanie kondycji zaplecza | Microsoft Docs
description: Ten artykuł pomaga zrozumieć, jak usługa Azure front-drzwi monitoruje kondycję zasobie
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 289b05a2c50a2b4af50eb2114515a49bb653cf1a
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742391"
---
# <a name="health-probes"></a>Sondy kondycji

Aby określić kondycję każdego zaplecza, każdy ze środowisk czołowych okresowo wysyła żądanie HTTP/HTTPS do każdego ze skonfigurowanych frontonów. Usługa Front Door następnie używa odpowiedzi z tych sond, aby określić „najlepsze” zaplecza, do których powinna kierować rzeczywiste żądania klientów. Należy zauważyć, że ze względu na to, że od drzwi do przodu jest globalnie wiele środowisk brzegowych, sonda kondycji żąda dużej ilości danych, jak wiele żądań na sekundę zależy od skonfigurowanej częstotliwości sondowania kondycji. 



## <a name="supported-protocols"></a>Obsługiwane protokoły

Drzwi tylne obsługują wysyłanie sond za pośrednictwem protokołów HTTP i HTTPS. Te sondy są wysyłane przy użyciu tych samych portów TCP co skonfigurowane na potrzeby routingu żądań klientów i nie można ich zmienić.

## <a name="health-probe-responses"></a>Odpowiedzi sondy kondycji

| Responses  | Opis | 
| ------------- | ------------- |
| Określanie kondycji  |  Kod stanu 200 OK wskazuje, że zaplecze jest w dobrej kondycji. Wszystko inne jest uznawane za niepowodzenie. Jeśli z jakiegoś powodu (w tym awarii sieci) nie odebrano prawidłowej odpowiedzi HTTP dla sondy, sonda jest traktowana jako błąd.|
| Pomiar opóźnienia  | Opóźnienie to czas zegara ściany mierzony od momentu natychmiast po wysłaniu żądania sondy do momentu otrzymania ostatniego bajtu odpowiedzi. Dla każdego żądania używane jest nowe połączenie TCP, więc pomiary nie są wliczane do zachodzących istniejących połączeń ciepłej.  |

## <a name="how-front-door-determines-backend-health"></a>Jak drzwi z przodu określają kondycję zaplecza

Usługa frontonu platformy Azure używa tego samego dwuetapowego procesu dla wszystkich algorytmów w celu określenia kondycji.

1. Wyklucz wyłączone punkty końcowe.

2. Wyklucz nadkończenie, które mają Błędy sond kondycji:
    * Aby to zrobić, należy przejrzeć odpowiedzi z ostatnich _n_ sondy kondycji. Jeśli co najmniej _x_ jest w dobrej kondycji, zaplecze jest traktowana jako dobra kondycja.

    * _n_ jest skonfigurowany przez zmianę właściwości SampleSize w ustawieniach równoważenia obciążenia.

    * wartość _x_ jest konfigurowana przez zmianę właściwości SuccessfulSamplesRequired w ustawieniach równoważenia obciążenia.

3. Poza zestawem nieprawidłowych punktów końcowych w puli zaplecza z przodu są dodatkowo stosowane i utrzymywane opóźnienie (czas błądzenia) dla każdego zaplecza.


## <a name="complete-health-probe-failure"></a>Ukończ błąd sondy kondycji

Jeśli sondy kondycji dla każdego zaplecza w puli zaplecza nie powiedzie się, a następnie drzwi przede wszystkim są w dobrej kondycji i przekierowują ruch w ramach rozkładu okrężnego między wszystkimi nimi.

Gdy dowolna zaplecze powróci do stanu dobrej kondycji, następnie drzwi do przodu spowodują wznowienie normalnego algorytmu równoważenia obciążenia.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
