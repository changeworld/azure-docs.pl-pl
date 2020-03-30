---
title: Azure Front Door — monitorowanie kondycji zaplecza | Dokumenty firmy Microsoft
description: Ten artykuł pomaga zrozumieć, jak usługi Azure Front Door monitoruje kondycję zaplecza
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
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471578"
---
# <a name="health-probes"></a>Sondy kondycji

W celu określenia kondycji i bliskości każdej wewnętrznej bazy danych z danego środowiska drzwi frontowych, każde środowisko drzwi frontowych okresowo wysyła syntetyczne żądanie HTTP/HTTPS do każdej skonfigurowanej wewnętrznej bazy danych. Usługa Front Door następnie używa odpowiedzi z tych sond, aby określić „najlepsze” zaplecza, do których powinna kierować rzeczywiste żądania klientów. 

> [!WARNING]
> Ponieważ drzwiami frontowymi mają wiele środowisk brzegowych na całym świecie, wolumin żądań sondy kondycji do zaplecza może być dość wysoki — od 25 żądań co minutę do 1200 żądań na minutę, w zależności od skonfigurowanych częstotliwości sondy kondycji. Przy domyślnej częstotliwości sondy 30 sekund wolumin sondy na wewnętrznej podstawie wewnętrznej powinien wynosić około 200 żądań na minutę.

## <a name="supported-protocols"></a>Obsługiwane protokoły

Drzwi frontowe obsługują wysyłanie sond za pośrednictwem protokołów HTTP lub HTTPS. Te sondy są wysyłane przy użyciu tych samych portów TCP co skonfigurowane na potrzeby routingu żądań klientów i nie można ich zmienić.

## <a name="supported-http-methods-for-health-probes"></a>Obsługiwane metody HTTP dla sond kondycji

Drzwi frontowe obsługuje następujące metody HTTP do wysyłania sond kondycji:

1. **POBIERZ:** Metoda GET oznacza pobieranie niezależnie od informacji (w postaci jednostki) jest identyfikowany przez request-URI.
2. **GŁOWA:** Head Metoda jest identyczna z GET, z tą różnicą, że serwer NIE MOŻE zwracać treść wiadomości w odpowiedzi. W przypadku nowych profili drzwi przednich domyślnie metoda sondy jest ustawiona jako HEAD.

> [!NOTE]
> W celu zmniejszenia obciążenia i kosztów zaplecza drzwiami frontowymi zaleca się użycie żądań HEAD dla sond kondycji.

## <a name="health-probe-responses"></a>Odpowiedzi sondy kondycji

| Odpowiedzi  | Opis | 
| ------------- | ------------- |
| Określanie kondycji  |  Kod stanu 200 OK wskazuje, że wewnętrznej bazy danych jest w dobrej kondycji. Wszystko inne jest uważane za porażkę. Jeśli z jakiegokolwiek powodu (w tym awarii sieci) prawidłowa odpowiedź HTTP nie zostanie odebrana dla sondy, sonda jest liczona jako błąd.|
| Opóźnienie pomiaru  | Opóźnienie to czas zegara ściennego mierzony od momentu wysłania żądania sondy do momentu, w którym otrzymamy ostatni bajt odpowiedzi. Używamy nowego połączenia TCP dla każdego żądania, więc ten pomiar nie jest stronniczy w kierunku zaplecza z istniejącymi ciepłymi połączeniami.  |

## <a name="how-front-door-determines-backend-health"></a>Jak drzwi frontowe określają kondycję zaplecza

Usługa Azure Front Door używa tego samego trzyetapowego procesu poniżej we wszystkich algorytmach w celu określenia kondycji.

1. Wyklucz wyłączone zaplecze.

2. Wyklucz zaplecze, które mają błędy sond kondycji:
    * Ten wybór odbywa się za pomocą ostatniego _n_ odpowiedzi sondy kondycji. Jeśli co najmniej _x_ są zdrowe, wewnętrznej bazy danych jest uważany za zdrowy.

    * _n_ jest skonfigurowany przez zmianę SampleSize właściwości w ustawieniach równoważenia obciążenia.

    * _x_ jest skonfigurowany przez zmianę właściwości SuccessfulSamplesRequired w ustawieniach równoważenia obciążenia.

3. Z zestawu zdrowych zaplecza w puli wewnętrznej bazy danych, drzwi frontowe dodatkowo mierzy i utrzymuje opóźnienie (czas podróży w obie strony) dla każdego zaplecza.


## <a name="complete-health-probe-failure"></a>Całkowita awaria sondy kondycji

Jeśli sondy kondycji nie dla każdej wewnętrznej bazy danych w puli wewnętrznej bazy danych, następnie drzwi frontowe uwzględnia wszystkie zaplecza w dobrej kondycji i trasy ruchu w dystrybucji okrężnej całej z nich.

Gdy dowolna wytłumienie powróci do stanu dobrej kondycji, następnie drzwiami frontowymi wznowią normalny algorytm równoważenia obciążenia.

## <a name="disabling-health-probes"></a>Wyłączanie sond kondycji

Jeśli masz pojedynczą wewnętrznej bazy danych w puli wewnętrznej bazy danych, można wyłączyć sondy kondycji zmniejszenie obciążenia wewnętrznej bazy danych aplikacji. Nawet jeśli masz wiele zaplecza w puli wewnętrznej bazy danych, ale tylko jeden z nich jest w stanie włączonym, można wyłączyć sondy kondycji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
