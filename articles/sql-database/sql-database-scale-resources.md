---
title: Usługi Azure SQL Database skalowanie zasobów | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób Skaluj bazę danych, dodając lub usuwając przydzielone zasoby.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: c918abdc635e7a4a831e367e159354bb752e95e6
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743107"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamiczne skalowanie zasobów bazy danych, przy minimalnych przestojach

Usługa Azure SQL Database umożliwia dynamiczne dodawanie większej ilości zasobów do bazy danych z minimalnym czasem przestojów.

## <a name="overview"></a>Przegląd

Gdy zapotrzebowanie na Twoją aplikację wzrośnie z garstki urządzeń i klientów do milionów użytkowników, bazy danych SQL Azure umożliwia skalowanie na bieżąco z minimalnym czasem przestojów. Skalowalność to jedną z najważniejszych cech rozwiązania PaaS, która pozwala na dynamiczne dodawanie większej ilości zasobów do usługi, gdy potrzebne. Usługa Azure SQL Database można łatwo zmienić zasoby (mocy procesora CPU, pamięci, przepustowości operacji We/Wy i magazynu) przydzielone do bazy danych.

Można rozwiązać problemy z wydajnością, ze względu na zwiększone użycie twojej aplikacji, nie można rozwiązać za pomocą indeksowania lub metody ponownego pisania zapytań. Dodawanie większej ilości zasobów, można szybko reagować na bazie danych osiągnie bieżące ograniczenia zasobów i potrzebuje więcej energii do obsługi obciążenia przychodzącego. Usługa Azure SQL Database umożliwia także dół zasobów, gdy nie są wymagane do obniżenia kosztów.

Nie musisz przejmować się zakupu sprzętu i zmieniając podstawowej infrastruktury. Skalowanie bazy danych można łatwo zrobić za pośrednictwem portalu Azure za pomocą suwaka.

![Skalowanie wydajności bazy danych](media/sql-database-scalability/scale-performance.svg)

Usługa Azure SQL Database oferuje [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).

- [Modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) oferuje połączenie wystąpień obliczeniowych, pamięci i zasobów we/wy w trzech warstwach usług do obsługi niewielkich obciążeń bazy danych: Podstawowa, Standardowa i Premium. Poziomy wydajności w każdej warstwie udostępniają różne kombinacje tych zasobów i można do nich dodawać kolejne zasoby magazynu.
- [Modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) pozwala wybrać liczbę rdzeni wirtualnych, ilość lub pamięć i wielkość i szybkość magazynu. Ten model zakupu oferuje trzy warstwy usługi: Ogólnego przeznaczenia i krytyczne dla działania, na dużą skalę.

Możesz utworzyć pierwszą aplikację na małej, pojedynczej bazy danych na niskim poziomie kosztów miesięcznie w warstwie podstawowa, standardowa lub ogólnego przeznaczenia i następnie zmienić warstwę usługi ręcznie lub programowo w dowolnym momencie do warstwy usług Premium lub krytyczne dla działania firmy, aby spełnić ne zewnętrznego źródła danych rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów oraz płacenie tylko za potrzebne zasoby i tylko gdy są używane.

> [!NOTE]
> Dynamiczna skalowalność różni się od skalowania automatycznego. O skalowaniu automatycznym mówimy, gdy usługa jest skalowana automatycznie na podstawie kryteriów, natomiast dynamiczna skalowalność uwzględnia skalowanie ręczne bez przestojów.

Pojedyncza baza danych Azure SQL Database obsługuje ręczną dynamiczną skalowalność, ale nie skalowanie automatyczne. Aby zapewnić środowisko bardziej *zautomatyzowane*, rozważ użycie elastycznych pul baz danych, które umożliwiają bazom danych udostępnianie zasobów w puli w zależności od potrzeb.
Istnieją jednak skrypty, które mogą pomóc zautomatyzować skalowalność dla pojedynczej bazy danych SQL Azure. Na przykład zobacz temat [Monitorowanie i skalowanie pojedynczej bazy danych SQL za pomocą programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

Możesz zmienić [warstwy usługi jednostki DTU](sql-database-service-tiers-dtu.md) lub [charakterystyki — rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md) w dowolnym momencie przy minimalnych przestojach do Twojej aplikacji (zwykle poniżej czterech sekund). W przypadku wielu firm i aplikacji możliwość tworzenia baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza jeśli wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. W tym scenariuszu należy użyć puli elastycznej z określoną liczbą jednostek Edtu, które są współużytkowane przez wiele baz danych w puli.

![Wprowadzenie do usługi SQL Database: Pojedynczą bazę danych jednostek Dtu według warstwy i poziomu](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Wszystkich trzech wersjach usługi Azure SQL Database oferuje możliwość ich dynamicznie skalować bazy danych:

- Za pomocą [pojedynczej bazy danych](sql-database-single-database-scale.md), można użyć dowolnego [jednostek DTU](sql-database-dtu-resource-limits-single-databases.md) lub [— rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md) modele, aby określić maksymalną ilość zasobów, które zostaną przypisane do każdej bazy danych.
- A [wystąpienia zarządzanego](sql-database-managed-instance.md) używa [rdzeni wirtualnych](sql-database-managed-instance.md#vcore-based-purchasing-model) tryb i pozwala na zdefiniowanie maksymalna liczba rdzeni procesora CPU i maksymalnej pamięci przydzielonej do wystąpienia. Wszystkie bazy danych w ramach wystąpienia będą współdzielić zasobów przydzielonych dla wystąpienia.
- [Pule elastyczne](sql-database-elastic-pool-scale.md) umożliwiają zdefiniowanie limit maksymalny zasobów dla grupy baz danych w puli.

> [!NOTE]
> Można oczekiwać, że podziału krótki połączenia podczas skalowania w górę/dół procesu zostało zakończone. Jeśli zaimplementowano [Logika ponawiania próby dla błędów przejściowych standardowa](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), można nie zauważyć przełączenie w tryb failover.

## <a name="alternative-scale-methods"></a>Skala alternatywnych metod

Skalowanie zasobów jest najłatwiejszym i najbardziej skutecznym sposobem, aby zwiększyć wydajność bazy danych bez konieczności zmieniania kodu bazy danych lub aplikacji. W niektórych przypadkach nawet najwyższej warstwy usług, rozmiarów wystąpień obliczeniowych i optymalizacji wydajności może nie obsługiwać obciążenia o pomyślnym i ekonomiczna metoda. W tym przypadku masz te dodatkowe funkcje umożliwiające skalowanie bazy danych:

- [Odczyt skalowalnego w poziomie](sql-database-read-scale-out.md) to funkcja dostępna w którym otrzymujesz jedna replika przeznaczona tylko do odczytu danych gdzie wykonania wymagających zapytań tylko do odczytu, takich jak raporty. Repliki tylko do odczytu będzie obsługiwać obciążenia tylko do odczytu bez wpływu na obciążenie zasobów w głównej bazie danych.
- [Fragmentowanie bazy danych](sql-database-elastic-scale-introduction.md) to zbiór technik umożliwia podzielić dane na kilka baz danych i ich niezależne skalowanie.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje dotyczące poprawy wydajności bazy danych, zmieniając kod bazy danych, zobacz [Znajdowanie i stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md).
- Aby dowiedzieć się, jak umożliwiając analizy wbudowaną bazą danych, zoptymalizuj bazę danych, zobacz [dostrajania automatycznego](sql-database-automatic-tuning.md).
- Aby uzyskać informacji na temat odczytu skalowalnego w poziomie w usłudze Azure SQL Database, zobacz jak [załadować równoważenia obciążeń związanych z zapytaniami tylko do odczytu są używane tylko do odczytu repliki](sql-database-read-scale-out.md).
- Aby uzyskać informacji na temat fragmentowanie bazy danych, zobacz [skalowanie w poziomie za pomocą usługi Azure SQL Database](sql-database-elastic-scale-introduction.md).
