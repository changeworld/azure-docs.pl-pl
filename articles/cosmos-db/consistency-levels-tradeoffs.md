---
title: Dostępność i wydajność kompromisy dla różnych poziomów spójności | Dokumentacja firmy Microsoft
description: Dostępność i wydajność kompromisy dla różnych poziomów spójności w usłudze Azure Cosmos DB.
keywords: spójność, wydajność, usługi azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 061a7c223d0e1c6fa7384d7defe9f84f470236ce
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244073"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels"></a>Dostępność i wydajność kompromisy dla różnych poziomów spójności

Rozproszonych baz danych, opierając się na replikację, wysoką dostępność, małych opóźnień, czy oba rodzaje, wprowadzić podstawowe zależnościami między spójności odczytu, a availability1, latency2 i przepływności. Wyjaśnienie pojęcia spójności danych jako liczne opcje zamiast dwoma skrajnymi poziomami spójnością silną i ostateczną zbliża się do usługi Azure Cosmos DB. Usługa cosmos DB umożliwia deweloperom wybranie pięć dokładnie zdefiniowanych modeli spójności ze spektrum spójności (najsilniejszej do najsłabszej) — **silne**, **powiązana nieaktualność**, **sesji** , **spójny prefiks**, i **ostatecznej**. Każda z pięcioma modelami spójności zapewnia wyczyść wpływ na dostępność i wydajność i jest wspierana przez kompleksowe umowy SLA.

## <a name="consistency-levels-and-latency"></a>Poziomy spójności i opóźnienia

- **Opóźnienie odczytu** dla wszystkich poziomów spójności jest zawsze musi być mniejsza niż 10 milisekund w 99. percentylu i są objęte umową SLA. Średni (w 50. percentyl) odczytu, czas oczekiwania jest zwykle 2 milisekund lub mniej.
- Z wyjątkiem konta usługi Cosmos obejmujące wiele regionów i skonfigurowane za pomocą silnej spójności **opóźnienie zapisu** dla wszystkich poziomów spójności jest zawsze musi być mniejsza niż 10 milisekund w 99. percentylu i jest uzupełniana przez UMOWA SLA. Opóźnienie zapisu średni (w 50. percentyl) jest zazwyczaj 5 MS lub mniej.
- Dla konta usługi Cosmos za pomocą kilku regionach skonfigurowano wysoki poziom spójności (obecnie dostępna w wersji zapoznawczej) **opóźnienie zapisu** musi być mniejsza niż < (2 * RTT) + 10 milisekund w 99. percentylu. RTT między położony dowolnych dwóch regionów skojarzonych z Twoim kontem Cosmos. Dokładny czas oczekiwania RTT jest funkcją szybkość programu światła odległości i dokładnie topologię sieci platformy Azure. Sieci platformy Azure nie zapewnia żadnych opóźnień umowy SLA dla RTT między wszystkie dwóch regionach platformy Azure. Opóźnienia w replikacji bazy danych cosmos są wyświetlane w witrynie Azure portal dla Twojego konta usługi Cosmos, dzięki czemu można monitorować opóźnienia w replikacji między różnych regionów skojarzonych z Twoim kontem Cosmos.

## <a name="consistency-levels-and-throughput"></a>Poziomy spójności i przepływności

- Dla jednostek ru na tyle samo sesja, spójny prefiks i spójności ostatecznej zapewnienia około 2 X odczytu w porównaniu do silnych i powiązana nieaktualność przepływności.
- Dla danego typu operacji zapisu (na przykład, Insert, Replace, Upsert, usuwanie itp.) przepływność zapisu (RUS) jest taka sama dla wszystkich poziomów spójności.

## <a name="consistency-levels-and-durability"></a>Poziomy spójności i niezawodność

Przed operacji zapisu zostało potwierdzone, do klienta, danych jest trwale zatwierdzone na przez kworum replik w regionie, akceptując zapisy. Ponadto jeśli kontener jest skonfigurowany przy użyciu zasad indeksowania spójne, indeks jest również synchronicznie zaktualizowane, replikowane i trwałym zatwierdzeniu przez kworum replik, zanim zapisu zostało potwierdzone, do klienta. 

W poniższej tabeli przedstawiono potencjalne okno utraty danych, w przypadku regionalnej awarii dla konta usługi Cosmos obejmujące wiele regionów.

| **Poziom spójności** | **Okno o możliwej utracie danych w przypadku regionalnej awarii** |
| - | - |
| Silna | Zero |
| Powiązana nieaktualność | Ograniczone do "nieaktualność okna" skonfigurowane przez programistę na konto usługi Cosmos |
| Sesja | Maksymalnie 5 sekund |
| Spójny prefiks | Maksymalnie 5 sekund |
| Ostateczna | Maksymalnie 5 sekund |

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz dowiedzieć się więcej o globalnej dystrybucji i wpływ na ogólną spójność w systemach rozproszonych za pomocą następujących artykułach:

* [Wady i zalety spójności w nowoczesnych rozproszoną bazę danych, projektowanie systemów](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Wysoka dostępność](high-availability.md)
* [Cosmos DB w umowie SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
