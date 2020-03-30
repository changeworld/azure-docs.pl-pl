---
title: Co to jest usługa Azure Cache for Redis?
description: Dowiedz się więcej o usłudze Azure Cache for Redis, aby włączyć buforowanie pamięci podręcznej, buforowanie zawartości, buforowanie sesji użytkowników, kolejkowanie zadań i wiadomości oraz transakcje rozproszone.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: 38936000e426d560237295105b5456429d9ae16d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79126367"
---
# <a name="azure-cache-for-redis-description"></a>Opis usługi Azure Cache for Redis

Usługa Azure Cache for Redis udostępnia magazyn danych w pamięci oparty na oprogramowaniu typu open source [Redis.](https://redis.io/) Gdy jest używana jako pamięć podręczna, Redis zwiększa wydajność i skalowalność systemów, które w dużym stopniu opierają się na magazynach danych zaplecza. Wydajność jest lepsza, kopiując często używane dane do szybkiego magazynu znajdującego się w pobliżu aplikacji. W przypadku usługi Azure Cache for Redis ten szybki magazyn znajduje się w pamięci, a nie jest ładowany z dysku przez bazę danych.

Usługa Azure Cache for Redis może służyć jako magazyn struktury danych w pamięci, rozproszona nierelacyjnej bazy danych i broker wiadomości. Wydajność aplikacji jest zwiększana dzięki wykorzystaniu małego opóźnienia i wysokiej przepustowości aparatu Redis.

Usługa Azure Cache for Redis zapewnia dostęp do bezpiecznej, dedykowanej pamięci podręcznej Redis. Usługa Azure Cache for Redis jest zarządzana przez firmę Microsoft, hostowana na platformie Azure i dostępna dla dowolnej aplikacji na platformie Azure lub poza nią.

## <a name="using-azure-cache-for-redis"></a>Korzystanie z usługi Azure Cache for Redis

Usługa Azure Cache for Redis zwiększa wydajność aplikacji, obsługując typowe wzorce architektury aplikacji. Niektóre z najczęstszych obejmują następujące:

| Wzorce      | Opis                                        |
| ------------ | -------------------------------------------------- |
| [Odkładanie do pamięci podręcznej](cache-web-app-cache-aside-leaderboard.md) | Bazy danych są często zbyt duże, aby załadować bezpośrednio do pamięci podręcznej. Jest często używać [wzorca od pamięci podręcznej,](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) aby załadować dane do pamięci podręcznej tylko w razie potrzeby. Gdy system wprowadza zmiany w danych, system może również zaktualizować pamięć podręczną, która jest następnie dystrybuowana do innych klientów. Ponadto system można ustawić wygaśnięcia danych lub użyć zasad eksmisji do wyzwalania aktualizacji danych w pamięci podręcznej.|
| [Buforowanie zawartości](cache-aspnet-output-cache-provider.md) | Wiele stron internetowych jest generowanych na podstawie szablonów, które używają zawartości statycznej, takiej jak nagłówki, stopki, banery. Te elementy statyczne nie powinny się często zmieniać. Korzystanie z pamięci podręcznej w pamięci zapewnia szybki dostęp do zawartości statycznej w porównaniu do magazynów danych wewnętrznej bazy danych. Ten wzorzec skraca czas przetwarzania i obciążenie serwera, dzięki czemu serwery sieci web są bardziej responsywne. Może to pozwolić na zmniejszenie liczby serwerów potrzebnych do obsługi obciążeń. Usługa Azure Cache for Redis udostępnia dostawcę pamięci podręcznej wyjścia Redis do obsługi tego wzorca z ASP.NET.|
| [Buforowanie sesji użytkownika](cache-aspnet-session-state-provider.md) | Ten wzorzec jest powszechnie używany z koszykami i innymi danymi historii użytkownika, które aplikacja internetowa może chcieć skojarzyć z plikami cookie użytkownika. Przechowywanie zbyt wielu danych w pliku cookie może mieć negatywny wpływ na wydajność, ponieważ rozmiar pliku cookie rośnie, a plik jest przekazywany i weryfikowany przy każdym żądaniu. Typowe rozwiązanie używa pliku cookie jako klucza do wykonywania zapytań o dane w bazie danych. Skojarzenie informacji z użytkownikiem za pomocą wewnątrzpamięciowej pamięci podręcznej, takiej jak usługa Azure Cache for Redis, jest znacznie szybsze niż interakcja z pełną relacyjną bazą danych. |
| Kolejkowanie zadań i komunikatów | Aplikacje często dodają zadania do kolejki, gdy operacje skojarzone z żądaniem zająć trochę czasu, aby wykonać. Dłuższe operacje są umieszczane w kolejce do przetworzenia w sekwencji, często przez inny serwer.  Ta metoda odraczania pracy jest nazywana kolejkowaniem zadań. Usługa Azure Cache for Redis udostępnia kolejkę rozproszoną, aby włączyć ten wzorzec w aplikacji.|
| Transakcje rozproszone | Aplikacje czasami wymagają serii poleceń względem magazynu danych wewnętrznej bazy danych do wykonania jako pojedyncza operacja niepodzielna. Wszystkie polecenia muszą się powieść lub wszystkie muszą zostać wycofane do stanu początkowego. Usługa Azure Cache for Redis obsługuje wykonywanie partii poleceń jako pojedynczej [transakcji.](https://redis.io/topics/transactions) |

## <a name="azure-cache-for-redis-offerings"></a>Oferta usługi Azure Cache for Redis

Usługa Azure Cache for Redis jest dostępna w następujących warstwach:

| Warstwa | Opis |
|---|---|
Podstawowa (Basic) | Pamięć podręczna z jednym węzłem. Ta warstwa obsługuje wiele rozmiarów pamięci (250 MB - 53 GB) i jest idealna do tworzenia/testowania i obciążeń niekrytycznych. Warstwa Podstawowa nie ma umowy dotyczącej poziomu usług (SLA) |
| Standardowa | Replikowana pamięć podręczna w konfiguracji dwuwęzłowej, podstawowej/pomocniczej zarządzanej przez platformę Azure z usługą SLA o wysokiej dostępności (99,9%) |
| Premium | Warstwa Premium to warstwa gotowa do pracy w przedsiębiorstwie. Pamięci podręczne w warstwie Premium obsługują więcej funkcji i mają większą przepływność przy niższych opóźnieniach. Pamięci podręczne w warstwie Premium są wdrażane na mocniejszym sprzęcie, zapewniając większą wydajność w porównaniu do warstw Podstawowa i Standardowa. Ta zaleta oznacza, że przepływność dla pamięci podręcznej o tym samym rozmiarze będzie wyższa w warstwie Premium w porównaniu do warstwy standardowej. |

> [!TIP]
> Więcej informacji na temat rozmiaru, przepływności i przepustowości pamięci podręcznych w warstwie Premium znajduje się w [często zadawanych pytaniach na temat usługi Azure Cache for Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Po utworzeniu pamięci podręcznej można skalować do wyższej warstwy. Skalowanie w dół do niższej warstwy nie jest obsługiwane. Aby uzyskać instrukcje skalowania krok po kroku, zobacz [Jak skalować usługę Azure Cache for Redis](cache-how-to-scale.md) i [Jak zautomatyzować operację skalowania](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Porównanie funkcji

Strona [Azure Cache for Redis — cennik](https://azure.microsoft.com/pricing/details/cache/) zawiera szczegółowe porównanie wszystkich warstw. W poniższej tabeli opisano niektóre funkcje obsługiwane przez warstwy:

| Opis funkcji | Premium | Standardowa | Podstawowa (Basic) |
| ------------------- | :-----: | :------: | :---: |
| [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Trwałość danych Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Klaster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Zabezpieczanie za pomocą reguł zapory](cache-configure.md#firewall) |✔|✔|✔|
| Szyfrowanie podczas transferu |✔|✔|✔|
| [Większe bezpieczeństwo i izolacja z użyciem sieci wirtualnej](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/eksport](cache-how-to-import-export-data.md) |✔|-|-|
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
