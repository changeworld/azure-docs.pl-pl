---
title: Skaluj zasoby
description: W tym artykule wyjaśniono, jak skalować bazę danych przez dodanie lub usunięcie przydzielonych zasobów.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: c4366b2718271b1e27325e6946c5016e9230cea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835916"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamicznie skaluj zasoby bazy danych przy minimalnym przestoju

Usługa Azure SQL Database umożliwia dynamiczne dodawanie większej ilości zasobów do bazy danych przy minimalnym [przestoju;](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/) jednak istnieje przełącznik w okresie, w którym łączność jest tracona do bazy danych na krótki czas, które mogą być złagodzone przy użyciu logiki ponawiania.

## <a name="overview"></a>Omówienie

Gdy zapotrzebowanie na aplikację rośnie z kilku urządzeń i klientów do milionów, usługa Azure SQL Database skaluje się na bieżąco przy minimalnym przestoju. Skalowalność jest jedną z najważniejszych cech PaaS, która umożliwia dynamiczne dodawanie większej ilości zasobów do usługi w razie potrzeby. Usługa Azure SQL Database umożliwia łatwą zmianę zasobów (zasilania procesora, pamięci, przepływności we/wy i magazynu) przydzielonych do baz danych.

Można zmniejszyć problemy z wydajnością ze względu na zwiększone użycie aplikacji, które nie mogą być ustalone przy użyciu indeksowania lub kwerendy przepisać metody. Dodanie większej ilości zasobów umożliwia szybkie reagowanie, gdy baza danych osiągnie bieżące limity zasobów i potrzebuje więcej energii do obsługi przychodzącego obciążenia. Usługa Azure SQL Database umożliwia również skalowanie w dół zasobów, gdy nie są one potrzebne do obniżenia kosztów.

Nie musisz się martwić o zakup sprzętu i zmianę podstawowej infrastruktury. Skalowanie bazy danych można łatwo wykonać za pomocą witryny Azure portal za pomocą suwaka.

![Skalowanie wydajności bazy danych](media/sql-database-scalability/scale-performance.svg)

Usługa Azure SQL Database oferuje [model zakupów oparty na usłudze DTU](sql-database-service-tiers-dtu.md) oraz model zakupów oparty na [parcie wirtualnym.](sql-database-service-tiers-vcore.md)

- [Model zakupów oparty na jednostce DTU](sql-database-service-tiers-dtu.md) oferuje połączenie zasobów obliczeniowych, pamięci i we/wy w trzech warstwach usług, aby obsługiwać lekkie i ciężkie obciążenia baz danych: Basic, Standard i Premium. Poziomy wydajności w każdej warstwie udostępniają różne kombinacje tych zasobów i można do nich dodawać kolejne zasoby magazynu.
- [Model zakupów oparty na wynikach vCore](sql-database-service-tiers-vcore.md) umożliwia wybranie liczby owo wirtualnych, ilości lub pamięci oraz ilości i szybkości przechowywania. Ten model zakupów oferuje trzy warstwy usług: ogólnego przeznaczenia, krytyczne dla firmy i hiperskali.

Pierwszą aplikację można utworzyć na małej, pojedynczej bazie danych po niskich kosztach miesięcznie w warstwie usług podstawowego, standardowego lub ogólnego przeznaczenia, a następnie zmienić jej warstwę usług ręcznie lub programowo w dowolnym momencie na warstwę usług Premium lub Business Critical, aby spełnić wymagania potrzeb rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów oraz płacenie tylko za potrzebne zasoby i tylko gdy są używane.

> [!NOTE]
> Dynamiczna skalowalność różni się od skalowania automatycznego. Skalowanie automatyczne jest wtedy, gdy usługa jest skalowana automatycznie na podstawie kryteriów, podczas gdy dynamiczna skalowalność pozwala na ręczne skalowanie przy minimalnym przestoju.

Pojedyncza baza danych Azure SQL Database obsługuje ręczną dynamiczną skalowalność, ale nie skalowanie automatyczne. Aby zapewnić środowisko bardziej *zautomatyzowane*, rozważ użycie elastycznych pul baz danych, które umożliwiają bazom danych udostępnianie zasobów w puli w zależności od potrzeb.
Istnieją jednak skrypty, które mogą pomóc zautomatyzować skalowalność dla pojedynczej bazy danych SQL Azure. Na przykład zobacz temat [Monitorowanie i skalowanie pojedynczej bazy danych SQL za pomocą programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

Możesz zmienić [warstwy usług jednostek DTU](sql-database-service-tiers-dtu.md) lub [charakterystyki rdzeni wirtualnych](sql-database-vcore-resource-limits-single-databases.md) w dowolnym momencie przy minimalnych przestojach w działaniu aplikacji (zwykle poniżej czterech sekund). W przypadku wielu firm i aplikacji możliwość tworzenia baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza jeśli wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. W tym scenariuszu należy użyć puli elastycznej z pewną liczbą eDTU, które są współużytkowane przez wiele baz danych w puli.

![Wprowadzenie do usługi SQL Database: jednostki DTU z jedną bazą danych według warstwy i poziomu.](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Wszystkie trzy smaki usługi Azure SQL Database oferują pewną możliwość dynamicznego skalowania baz danych:

- W [przypadku pojedynczej bazy danych](sql-database-single-database-scale.md)można użyć modeli [jednostek DTU](sql-database-dtu-resource-limits-single-databases.md) lub [vCore](sql-database-vcore-resource-limits-single-databases.md) do zdefiniowania maksymalnej ilości zasobów, które zostaną przypisane do każdej bazy danych.
- [Wystąpienie zarządzane](sql-database-managed-instance.md) używa trybu [rdzeni wirtualnych](sql-database-managed-instance.md#vcore-based-purchasing-model) i umożliwia zdefiniowanie maksymalnych rdzeni procesora CPU i maksymalnej ilości miejsca do magazynowania przydzielonego do wystąpienia. Wszystkie bazy danych w wystąpieniu będą współużytkować zasoby przydzielone do wystąpienia.
- [Pule elastyczne](sql-database-elastic-pool-scale.md) umożliwiają definiowanie maksymalnego limitu zasobów na grupę baz danych w puli.

Inicjowanie skalowania w górę lub skalowania w dół akcji w dowolnym z smaków spowoduje ponowne uruchomienie procesu aparatu bazy danych i przeniesienie go do innej maszyny wirtualnej w razie potrzeby. Przenoszenie procesu aparatu bazy danych do nowej maszyny wirtualnej jest **procesem online,** w którym można kontynuować korzystanie z istniejącej usługi Azure SQL Database, gdy proces jest w toku. Gdy aparat docelowej bazy danych jest w pełni zainicjowany i gotowy do przetwarzania zapytań, połączenia zostaną [przełączone ze źródła do docelowego aparatu bazy danych.](sql-database-single-database-scale.md#impact) 


> [!NOTE]
> Po zakończeniu procesu skalowania w górę/w dół można oczekiwać krótkiego przerwania połączenia. Jeśli zaimplementowano [logikę ponawiania próby dla standardowych błędów przejściowych,](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)nie zauważysz pracy awaryjnej.

## <a name="alternative-scale-methods"></a>Alternatywne metody skalowania

Skalowanie zasobów jest najprostszym i najbardziej skutecznym sposobem na zwiększenie wydajności bazy danych bez zmiany bazy danych lub kodu aplikacji. W niektórych przypadkach nawet najwyższe warstwy usług, rozmiary obliczeń i optymalizacje wydajności mogą nie obsługiwać obciążenia w udany i opłacalny sposób. W takim przypadku dostępne są te dodatkowe opcje skalowania bazy danych:

- [Skalowanie w poziomie odczytu](sql-database-read-scale-out.md) jest dostępną funkcją, w której otrzymujesz jedną replikę danych tylko do odczytu, w której można wykonywać wymagające zapytania tylko do odczytu, takie jak raporty. Replika tylko do odczytu będzie obsługiwać obciążenie tylko do odczytu bez wpływu na użycie zasobów w podstawowej bazie danych.
- [Dzielenie na fragmenty bazy danych](sql-database-elastic-scale-introduction.md) to zestaw technik, który umożliwia dzielenie danych na kilka baz danych i skalowanie ich niezależnie.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat zwiększania wydajności bazy danych przez zmianę kodu bazy danych, zobacz [Znajdowanie i stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md).
- Aby uzyskać informacje na temat zezwalania na wbudowaną inteligencję baz danych w celu optymalizacji bazy danych, zobacz [Automatyczne dostrajanie](sql-database-automatic-tuning.md).
- Aby uzyskać informacje na temat skalowania w poziomie odczytu w usłudze Azure SQL Database, zobacz, jak używać replik tylko do [odczytu do obciążenia obciążeń zapytań tylko do odczytu.](sql-database-read-scale-out.md)
- Aby uzyskać informacje na temat dzielenia na fragmenty bazy danych, zobacz [Skalowanie w poziomie za pomocą usługi Azure SQL Database](sql-database-elastic-scale-introduction.md).
