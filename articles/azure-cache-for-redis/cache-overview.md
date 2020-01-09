---
title: Co to jest usługa Azure Cache for Redis?
description: Dowiedz się, co to jest usługa Azure cache for Redis i typowe zastosowania, w tym pamięć podręczna, buforowanie zawartości, buforowanie sesji użytkownika, obsługa zadań i kolejkowanie komunikatów oraz transakcje rozproszone.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.custom: mvc
ms.date: 03/26/2018
ms.openlocfilehash: 5224be999ff8ff52c2f52568a504095dc5962398
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433420"
---
# <a name="azure-cache-for-redis-description"></a>Opis usługi Azure cache for Redis

Usługa Azure Cache for Redis opiera się na popularnym oprogramowaniu [Redis](https://redis.io/). Zazwyczaj jest używana jako pamięć podręczna w celu poprawy wydajności i skalowalności systemów, które są silnie uzależnione od magazynów danych na zapleczu. Poprawa wydajności jest możliwa dzięki tymczasowemu kopiowaniu często używanych danych do szybkiego magazynu znajdującego się blisko aplikacji. W przypadku usługi [Azure Cache for Redis](https://redis.io/) ten szybki magazyn jest umieszczany w pamięci za pomocą usługi Azure Cache for Redis zamiast ładowania z dysku przez bazę danych.

Pamięć podręczna platformy Azure dla usługi Redis może być również używana jako magazyn struktury danych w pamięci, rozproszona baza danych nierelacyjna i Broker komunikatów. Wydajność aplikacji jest zwiększana dzięki wykorzystaniu małego opóźnienia i wysokiej przepustowości aparatu Redis.

Pamięć podręczna systemu Azure dla usługi Redis zapewnia dostęp do bezpiecznej, dedykowanej pamięci podręcznej Redis. Usługa Azure cache for Redis jest zarządzana przez firmę Microsoft, hostowana na platformie Azure i dostępna dla dowolnej aplikacji w ramach platformy Azure lub poza nią.

## <a name="using-azure-cache-for-redis"></a>Korzystanie z usługi Azure cache for Redis

Istnieje wiele typowych wzorców wykorzystania usługi Azure Cache for Redis do obsługi architektury aplikacji lub poprawy wydajności aplikacji. Niektóre z najczęstszych obejmują następujące:

| Wzorce      | Opis                                        |
| ------------ | -------------------------------------------------- |
| [Odkładanie do pamięci podręcznej](cache-web-app-cache-aside-leaderboard.md) | Ponieważ baza danych może być duża, ładowanie całej bazy danych do pamięci podręcznej nie jest zalecane. Często stosuje się wzorzec [odkładania do pamięci podręcznej](https://docs.microsoft.com/azure/architecture/patterns/cache-aside), aby do pamięci podręcznej ładować elementy danych tylko w razie potrzeby. Gdy system dokonuje zmian w danych zaplecza, może w tym samym czasie zaktualizować też pamięć podręczną, która jest dystrybuowana do innych klientów. Ponadto system może ustawić okres ważności dla elementów danych albo stosować zasady eksmisji, które doprowadzą do ponownego załadowania aktualizacji danych do pamięci podręcznej.|
| [Buforowanie zawartości](cache-aspnet-output-cache-provider.md) | Większość stron sieci Web jest generowanych z szablonów z nagłówkami, stopkami, paskami narzędzi, menu itd. Nie zmieniają się często i nie powinny być generowane dynamicznie. Zastosowanie wewnątrzpamięciowej pamięci podręcznej, takiej jak usługa Azure Cache for Redis, zapewni serwerom internetowym szybki dostęp do tego typu zawartości statycznej w porównaniu z magazynami danych na zapleczu. Ten wzorzec skraca czas przetwarzania i obciążenie serwera w stosunku do wymaganych przy dynamicznym generowaniu zawartości. Dzięki niemu serwery internetowe mogą odpowiadać szybciej, a Ty możesz zmniejszyć liczbę serwerów wymaganych do obsługi obciążeń. Usługa Azure Cache for Redis udostępnia dostawcę Redis Output Cache Provider, aby obsługiwać ten wzorzec przy użyciu platformy ASP.NET.|
| [Buforowanie sesji użytkownika](cache-aspnet-session-state-provider.md) | Ten wzorzec jest najczęściej używany dla koszyków z zakupami i innego typu informacji o historii użytkownika, które aplikacja internetowa może chcieć skojarzyć z plikami cookie użytkownika. Przechowywanie zbyt wielu danych w pliku cookie może mieć negatywny wpływ na wydajność, ponieważ rozmiar pliku cookie rośnie, a plik jest przekazywany i weryfikowany przy każdym żądaniu. Typowym rozwiązaniem jest używanie pliku cookie jako klucza do wykonywania zapytania o dane w bazie danych zaplecza. Skojarzenie informacji z użytkownikiem za pomocą wewnątrzpamięciowej pamięci podręcznej, takiej jak usługa Azure Cache for Redis, jest znacznie szybsze niż interakcja z pełną relacyjną bazą danych. |
| Kolejkowanie zadań i komunikatów | Gdy aplikacje odbierają żądania, operacje skojarzone z żądaniem często potrzebują dodatkowego czasu na wykonanie. Typowym wzorcem odroczenia dłużej wykonywanych operacji jest dodanie ich do kolejki, która jest przetwarzana później i w miarę możliwości przez inny serwer. Ta metoda odraczania pracy jest nazywana kolejkowaniem zadań. Istnieje wiele składników oprogramowania przeznaczonych do obsługi kolejek zadań. Pamięć podręczna systemu Azure dla usługi Redis również służy do obsługi kolejki rozproszonej.|
| Transakcje rozproszone | Częstym wymaganiem względem aplikacji jest, aby były w stanie wykonywać serie poleceń względem magazynu danych na zapleczu w ramach jednej operacji (niepodzielnej). Wszystkie polecenia muszą się powieść lub wszystkie muszą zostać wycofane do stanu początkowego. Usługa Azure Cache for Redis obsługuje wykonywanie partii poleceń jako jednej operacji w formie [transakcji](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Oferta usługi Azure Cache for Redis

Usługa Azure Cache for Redis jest dostępna w następujących warstwach:

| Warstwa | Opis |
|---|---|
Basic | Pamięć podręczna z jednym węzłem. Ta warstwa obsługuje wiele rozmiarów pamięci (250 MB–53 GB). Idealnie nadaje się do programowania/testowania i obsługi obciążeń niekrytycznych. Warstwa Podstawowa nie ma umowy dotyczącej poziomu usług (SLA) |
| Standardowa | Replikowana pamięć podręczna w postaci dwóch węzłów (podstawowy/dodatkowy) zarządzanych przez firmę Microsoft i objętych umową SLA zapewniającą wysoką dostępność (99,9%). |
| Premium | Warstwa Premium jest warstwą gotową dla przedsiębiorstw. Pamięci podręczne w warstwie Premium obsługują więcej funkcji i mają większą przepływność przy niższych opóźnieniach. Pamięci podręczne w warstwie Premium są wdrażane na mocniejszym sprzęcie, zapewniając większą wydajność w porównaniu do warstw Podstawowa i Standardowa. Ta zaleta oznacza, że przepływność dla pamięci podręcznej o takim samym rozmiarze będzie wyższa w porównaniu do warstwy Standardowa. |

> [!TIP]
> Więcej informacji na temat rozmiaru, przepływności i przepustowości pamięci podręcznych w warstwie Premium znajduje się w [często zadawanych pytaniach na temat usługi Azure Cache for Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Pamięć podręczną można po utworzeniu skalować w górę do wyższej warstwy. Skalowanie w dół do niższej warstwy nie jest obsługiwane. Aby uzyskać instrukcje skalowania krok po kroku, zobacz [Jak skalować usługę Azure Cache for Redis](cache-how-to-scale.md) i [Jak zautomatyzować operację skalowania](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Porównanie funkcji

Strona [Azure Cache for Redis — cennik](https://azure.microsoft.com/pricing/details/cache/) zawiera szczegółowe porównanie wszystkich warstw. W poniższej tabeli opisano niektóre funkcje obsługiwane przez warstwy:

| Opis funkcji | Premium | Standardowa | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Trwałość danych Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Klaster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Zabezpieczanie za pomocą reguł zapory](cache-configure.md#firewall) |✔|✔|✔|
| Szyfrowanie danych przesyłanych |✔|✔|✔|
| [Większe bezpieczeństwo i izolacja z użyciem sieci wirtualnej](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [Zaplanowane aktualizacje](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Geo-replication](cache-how-to-geo-replication.md) (Replikacja geograficzna) |✔|-|-|
| [Ponowne uruchamianie](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Następne kroki

* [Szybki start z aplikacją internetową platformy ASP.NET](cache-web-app-howto.md) Tworzenie prostej aplikacji internetowej platformy ASP.NET, która korzysta z usługi Azure Cache for Redis.
* [Szybki start z platformą .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Tworzenie aplikacji platformy .NET, która korzysta z usługi Azure Cache for Redis.
* [Szybki start z platformą .NET Core](cache-dotnet-core-quickstart.md) Tworzenie aplikacji platformy .NET Core, która korzysta z usługi Azure Cache for Redis.
* [Szybki start z platformą Node.js](cache-nodejs-get-started.md) Tworzenie prostej aplikacji platformy Node.js, która korzysta z usługi Azure Cache for Redis.
* [Szybki start z językiem Java](cache-java-get-started.md) Tworzenie prostej aplikacji Java, która korzysta z usługi Azure Cache for Redis.
* [Szybki start z językiem Python](cache-python-get-started.md) Tworzenie aplikacji Python, która korzysta z usługi Azure Cache for Redis.
