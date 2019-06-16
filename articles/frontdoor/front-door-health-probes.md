---
title: Usługa Azure drzwiami frontowymi — monitorowanie kondycji zaplecza | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, jak usługa drzwiami frontowymi Azure monitoruje kondycję usługi zaplecza
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
ms.openlocfilehash: 59a3bac39437b91eeee3b005bd23476a34a308b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736585"
---
# <a name="health-probes"></a>Sondy kondycji

Aby można było ustalić kondycję poszczególnych pól zaplecza, każde środowisko drzwiami frontowymi okresowo wysyła syntetyczne żądania HTTP/HTTPS do każdego skonfigurowanego zaplecza. Usługa Front Door następnie używa odpowiedzi z tych sond, aby określić „najlepsze” zaplecza, do których powinna kierować rzeczywiste żądania klientów.


## <a name="supported-protocols"></a>Obsługiwane protokoły

Drzwiami frontowymi obsługuje sondy do wysyłania za pośrednictwem protokołów HTTP lub HTTPS. Te sondy są wysyłane przy użyciu tych samych portów TCP co skonfigurowane na potrzeby routingu żądań klientów i nie można ich zmienić.

## <a name="health-probe-responses"></a>Odpowiedzi sond kondycji

| Responses  | Opis | 
| ------------- | ------------- |
| Określanie kondycji  |  Kod stanu 200 OK wskazuje, że wewnętrznej bazy danych jest w dobrej kondycji. Cała reszta jest uznawany za błąd. Jeśli z jakiegokolwiek powodu (łącznie z awarii sieci) prawidłową odpowiedź HTTP nie została odebrana sondowania, sondy jest traktowana jako błąd.|
| Pomiary opóźnienia  | Opóźnienie to czas zegarowy mierzony od momentu bezpośrednio przed wykonaniem możemy wysłać do momentu, gdy firma Microsoft otrzyma ostatniego bajtu odpowiedzi na żądania sondowania. Używamy nowego połączenia protokołu TCP dla każdego żądania, więc ten pomiar nie jest ukierunkowane zaplecza przy użyciu istniejących połączeń bez wyłączania zasilania.  |

## <a name="how-front-door-determines-backend-health"></a>Jak drzwiami frontowymi określa kondycję wewnętrznej bazy danych

Usługa drzwiami frontowymi używa tego samego trzy kroki proces poniżej wszystkich algorytmów w celu ustalenia kondycji.

1. Wyklucz wyłączone zaplecza.

2. Wyklucz zapleczy, które mają błędy sondy kondycji:
    * Zaznacz to pole wyboru jest wykonywane przez spojrzenie na ostatnich _n_ odpowiedzi sond kondycji. Jeśli co najmniej _x_ są w dobrej kondycji, wewnętrznej bazy danych jest uznawany za dobrej kondycji.

    * _n_ jest skonfigurowany, zmieniając właściwość SampleSize ustawienia równoważenia obciążenia.

    * _x_ jest skonfigurowany, zmieniając właściwość SuccessfulSamplesRequired ustawienia równoważenia obciążenia.

3. Poza zestaw dobrej kondycji zaplecza w puli zaplecza drzwiami frontowymi dodatkowo miary i przechowuje czas oczekiwania (czas błądzenia) dla poszczególnych pól zaplecza.


## <a name="complete-health-probe-failure"></a>Awaria sondy kondycji ukończone

W przypadku awarii sond kondycji każdego zaplecza w puli zaplecza, drzwiami frontowymi uwzględnia wszystkie zaplecza dobrej kondycji i kieruje ruchem w dystrybucji działanie okrężne we wszystkich z nich.

Gdy dowolnego zaplecza powróci do stanu dobrej kondycji, drzwiami frontowymi wznowi normalne algorytm równoważenia obciążenia.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
