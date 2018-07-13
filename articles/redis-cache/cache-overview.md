---
title: Co to jest usługa Azure Redis Cache? | Microsoft Docs
description: Dowiedz się, co to jest usługa Azure Redis Cache i jak jest zwykle używana.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 585dcd120c42562b1520d4454f9d04e445553101
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096196"
---
# <a name="what-is-azure-redis-cache"></a>Co to jest usługa Azure Redis Cache

Usługa Azure Redis Cache jest oparta na popularnej [pamięci podręcznej Redis](https://redis.io/) typu „open source”. Zazwyczaj jest używana jako pamięć podręczna w celu poprawy wydajności i skalowalności systemów, które są silnie uzależnione od magazynów danych na zapleczu. Poprawa wydajności jest możliwa dzięki tymczasowemu kopiowaniu często używanych danych do szybkiego magazynu znajdującego się blisko aplikacji. W przypadku [pamięci podręcznej Redis](https://redis.io/) ten szybki magazyn jest umieszczany w pamięci za pomocą usługi Redis Cache zamiast ładowania z dysku przez bazę danych.

Usługa Azure Redis Cache może także służyć jako magazyn struktury danych w pamięci, rozproszona nierelacyjna baza danych i broker komunikatów. Wydajność aplikacji jest zwiększana dzięki wykorzystaniu małego opóźnienia i wysokiej przepustowości aparatu Redis.

Usługa Azure Redis Cache zapewnia dostęp do zabezpieczonej, dedykowanej pamięci podręcznej Redis zarządzanej przez firmę Microsoft, która jest hostowana na platformie Azure i dostępna z dowolnej aplikacji na platformie Azure lub poza nią.

## <a name="why-use-azure-redis-cache"></a>Dlaczego warto korzystać z usługi Azure Redis Cache

Istnieje wiele typowych wzorców wykorzystania usługi Redis Cache do obsługi architektury aplikacji lub poprawy wydajności aplikacji. Niektóre z najczęstszych obejmują następujące:

| Wzorce      | Opis                                        |
| ------------ | -------------------------------------------------- |
| [Odkładanie do pamięci podręcznej](cache-web-app-cache-aside-leaderboard.md) | Ponieważ baza danych może być duża, ładowanie całej bazy danych do pamięci podręcznej nie jest zalecane. Często stosuje się wzorzec [odkładania do pamięci podręcznej](https://docs.microsoft.com/azure/architecture/patterns/cache-aside), aby do pamięci podręcznej ładować elementy danych tylko w razie potrzeby. Gdy system dokonuje zmian w danych zaplecza, może w tym samym czasie zaktualizować też pamięć podręczną, która jest dystrybuowana do innych klientów. Ponadto system może ustawić okres ważności dla elementów danych albo stosować zasady eksmisji, które doprowadzą do ponownego załadowania aktualizacji danych do pamięci podręcznej.|
| [Buforowanie zawartości](cache-aspnet-output-cache-provider.md) | Większość stron internetowych jest generowanych na podstawie szablonów z nagłówkami, stopkami, paskami narzędzi, menu itp. W praktyce nie zmieniają się one często i nie powinny być generowane dynamicznie. Zastosowanie wewnątrzpamięciowej pamięci podręcznej, takiej jak usługa Azure Redis Cache, zapewni serwerom internetowym szybki dostęp do tego typu zawartości statycznej w porównaniu z magazynami danych na zapleczu. Ten wzorzec skraca czas przetwarzania i obciążenie serwera w stosunku do wymaganych przy dynamicznym generowaniu zawartości. Dzięki niemu serwery internetowe mogą odpowiadać szybciej, a Ty możesz zmniejszyć liczbę serwerów wymaganych do obsługi obciążeń. Usługa Azure Redis Cache udostępnia dostawcę Redis Output Cache Provider, aby obsługiwać ten wzorzec przy użyciu platformy ASP.NET.|
| [Buforowanie sesji użytkownika](cache-aspnet-session-state-provider.md) | Ten wzorzec jest najczęściej używany dla koszyków z zakupami i innego typu informacji o historii użytkownika, które aplikacja internetowa może chcieć skojarzyć z plikami cookie użytkownika. Przechowywanie zbyt wielu danych w pliku cookie może mieć negatywny wpływ na wydajność, ponieważ rozmiar pliku cookie rośnie, a plik jest przekazywany i weryfikowany przy każdym żądaniu. Typowym rozwiązaniem jest używanie pliku cookie jako klucza do wykonywania zapytania o dane w bazie danych zaplecza. Skojarzenie informacji z użytkownikiem za pomocą wewnątrzpamięciowej pamięci podręcznej, takiej jak usługa Azure Redis Cache, jest znacznie szybsze niż interakcja z pełną relacyjną bazą danych. |
| Kolejkowanie zadań i komunikatów | Gdy aplikacje odbierają żądania, operacje skojarzone z żądaniem często potrzebują dodatkowego czasu na wykonanie. Typowym wzorcem odroczenia dłużej wykonywanych operacji jest dodanie ich do kolejki, która jest przetwarzana później i w miarę możliwości przez inny serwer. Ta metoda odraczania pracy jest nazywana kolejkowaniem zadań. Istnieje wiele składników oprogramowania przeznaczonych do obsługi kolejek zadań. Usługa Redis Cache również dobrze sprawdza się w tej roli jako kolejka rozproszona.|
| Transakcje rozproszone | Częstym wymaganiem względem aplikacji jest, aby były w stanie wykonywać serie poleceń względem magazynu danych na zapleczu w ramach jednej operacji (niepodzielnej). Wszystkie polecenia muszą się powieść lub wszystkie muszą zostać wycofane do stanu początkowego. Usługa Redis Cache obsługuje wykonywanie partii poleceń jako jednej operacji w formie [transakcji](https://redis.io/topics/transactions). |

## <a name="azure-redis-cache-offerings"></a>Oferty usługi Azure Redis Cache

Usługa Azure Redis Cache jest dostępna w następujących warstwach:

| Warstwa | Opis |
|---|---|
Podstawowa | Pamięć podręczna z jednym węzłem. Ta warstwa obsługuje wiele rozmiarów pamięci (250 MB–53 GB). Idealnie nadaje się do programowania/testowania i obsługi obciążeń niekrytycznych. Warstwa Podstawowa nie ma umowy dotyczącej poziomu usług (SLA) |
| Standardowa (Standard) | Replikowana pamięć podręczna w postaci dwóch węzłów (podstawowy/dodatkowy) zarządzanych przez firmę Microsoft i objętych umową SLA zapewniającą wysoką dostępność (99,9%). |
| Premium | Warstwa Premium jest warstwą przeznaczoną dla przedsiębiorstw. Pamięci podręczne w warstwie Premium obsługują więcej funkcji i mają większą przepływność przy niższych opóźnieniach. Pamięci podręczne w warstwie Premium są wdrażane na mocniejszym sprzęcie, zapewniając większą wydajność w porównaniu do warstw Podstawowa i Standardowa. Ta zaleta oznacza, że przepływność dla pamięci podręcznej o tym samym rozmiarze będzie większa w warstwie Premium niż w warstwie Standardowa. |

> [!TIP]
> Więcej informacji na temat rozmiaru, przepływności i przepustowości pamięci podręcznych w warstwie Premium znajduje się w [często zadawanych pytaniach na temat usługi Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).
>

Pamięć podręczną można po utworzeniu skalować w górę do wyższej warstwy. Skalowanie w dół do niższej warstwy nie jest obsługiwane. Aby uzyskać instrukcje skalowania krok po kroku, zobacz [Jak skalować usługę Azure Redis Cache](cache-how-to-scale.md) i [Jak zautomatyzować operację skalowania](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparision"></a>Porównanie funkcji

Strona [Cennik usługi Redis Cache](https://azure.microsoft.com/pricing/details/cache/) zawiera szczegółowe porównanie wszystkich warstw. W poniższej tabeli opisano niektóre funkcje obsługiwane przez warstwy:

| Opis funkcji | Premium | Standardowa (Standard) | Podstawowa |
| ------------------- | :-----: | :------: | :---: |
| [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Trwałość danych Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Klaster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Zabezpieczanie za pomocą reguł zapory](cache-configure.md#firewall) |✔|✔|✔|
| [Większe bezpieczeństwo i izolacja z użyciem sieci wirtualnej](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [Aktualizacje harmonogramu](cache-administration.md#schedule-updates) |✔|-|-|
| [Geo-replication](cache-how-to-geo-replication.md) (Replikacja geograficzna) |✔|-|-|
| [Ponowne uruchamianie](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Następne kroki

* [Szybki start z aplikacją internetową platformy ASP.NET](cache-web-app-howto.md) Tworzenie prostej aplikacji internetowej platformy ASP.NET, która korzysta z usługi Azure Redis Cache.
* [Szybki start z platformą .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Tworzenie aplikacji platformy .NET, która korzysta z usługi Azure Redis Cache.
* [Szybki start z platformą .NET Core](cache-dotnet-core-quickstart.md) Tworzenie aplikacji platformy .NET Core, która korzysta z usługi Azure Redis Cache.
* [Szybki start z platformą Node.js](cache-nodejs-get-started.md) Tworzenie prostej aplikacji platformy Node.js, która korzysta z usługi Azure Redis Cache.
* [Szybki start z językiem Java](cache-java-get-started.md) Tworzenie aplikacji Java, która korzysta z usługi Azure Redis Cache.
* [Szybki start z językiem Python](cache-python-get-started.md) Tworzenie aplikacji Python, która korzysta z usługi Azure Redis Cache.
