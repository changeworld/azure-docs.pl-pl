---
title: Usługi Azure SQL Database skalowanie zasobów | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób Skaluj bazę danych, dodając lub usuwając przydzielone zasoby.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/07/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: f55ce511f6ba90c27e149ac90bbd2c8aa0b3c742
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921514"
---
# <a name="scale-database-resources"></a>Skalowanie zasobów bazy danych

Usługa Azure SQL Database umożliwia dynamiczne dodawanie większej ilości zasobów do bazy danych z minimalnym czasem przestojów.

## <a name="overview"></a>Przegląd

Gdy zapotrzebowanie na Twoją aplikację wzrośnie z garstki urządzeń i klientów do milionów użytkowników, bazy danych SQL Azure umożliwia skalowanie na bieżąco z minimalnym czasem przestojów. Skalowalność to jedną z najważniejszych cech rozwiązania PaaS, która pozwala na dynamiczne dodawanie większej ilości zasobów do usługi, gdy potrzebne. Usługa Azure SQL Database można łatwo zmienić zasoby (mocy procesora CPU, pamięci, przepustowości operacji We/Wy i magazynu) przydzielone do bazy danych.  
Można rozwiązać problemy z wydajnością, ze względu na zwiększone użycie twojej aplikacji, nie można rozwiązać za pomocą indeksowania lub metody ponownego pisania zapytań. Dodawanie większej ilości zasobów, można szybko reagować na bazie danych osiągnie bieżące ograniczenia zasobów i potrzebuje więcej energii do obsługi obciążenia przychodzącego. Usługa Azure SQL Database umożliwia także dół zasobów, gdy nie są wymagane do obniżenia kosztów.
Nie musisz przejmować się zakupu sprzętu i zmieniając podstawowej infrastruktury. Skalowanie bazy danych można łatwo zrobić za pośrednictwem portalu Azure za pomocą suwaka.

![Skalowanie wydajności bazy danych](media/sql-database-scalability/scale-performance.svg)

Usługa Azure SQL Database oferuje [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) lub [(wersja zapoznawcza) modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md). 
-   [Modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) oferuje połączenie wystąpień obliczeniowych, pamięci i zasobów we/wy w trzech warstwach usług do obsługi niewielkich obciążeń bazy danych: podstawowa, standardowa i Premium. Poziomy wydajności w każdej warstwie udostępniają różne kombinacje tych zasobów i można do nich dodawać kolejne zasoby magazynu.
-   [Modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) (wersja zapoznawcza) pozwala na wybór liczby rdzeni wirtualnych, ilość lub pamięć i wielkość i szybkość magazynu.
Ponosząc niewielki miesięczny koszt, możesz utworzyć pierwszą aplikację na małej, pojedynczej bazie danych, a następnie w dowolnym momencie ręcznie lub programowo zmienić jej warstwę usług, aby spełnić wymagania rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów oraz płacenie tylko za potrzebne zasoby i tylko gdy są używane.


> [!NOTE]
> Dynamiczna skalowalność różni się od skalowania automatycznego. O skalowaniu automatycznym mówimy, gdy usługa jest skalowana automatycznie na podstawie kryteriów, natomiast dynamiczna skalowalność uwzględnia skalowanie ręczne bez przestojów.
>


Pojedyncza baza danych SQL Azure Database obsługuje ręczną dynamiczną skalowalność, ale nie skalowanie automatyczne. Aby zapewnić środowisko bardziej *zautomatyzowane*, rozważ użycie elastycznych pul baz danych, które umożliwiają bazom danych udostępnianie zasobów w puli w zależności od potrzeb.
Istnieją jednak skrypty, które mogą pomóc zautomatyzować skalowalność dla pojedynczej bazy danych SQL Azure. Na przykład zobacz temat [Monitorowanie i skalowanie pojedynczej bazy danych SQL za pomocą programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).


Wszystkich trzech wersjach usługi Azure SQL Database oferuje możliwość ich dynamicznie skalować bazy danych:
-   W [pojedynczej bazy danych SQL Azure](sql-database-single-database-scale.md), można użyć dowolnego [jednostek DTU](sql-database-dtu-resource-limits-single-databases.md) lub [— rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md) modele, aby określić maksymalną ilość zasobów, które zostaną przypisane do każdej bazy danych.
-   [Wystąpienie zarządzane SQL Azure](sql-database-managed-instance.md) używa [rdzeni wirtualnych](/azure/sql-database/sql-database-managed-instance#vcore-based-purchasing-model-preview) tryb i pozwala na zdefiniowanie maksymalna liczba rdzeni procesora CPU i maksymalnej pamięci przydzielonej do wystąpienia. Wszystkie bazy danych w ramach wystąpienia będą współdzielić zasobów przydzielonych dla wystąpienia.
-   [Pule elastyczne usługi Azure SQL](sql-database-elastic-pool-scale.md) umożliwiają zdefiniowanie limit maksymalny zasobów dla grupy baz danych w puli.

## <a name="alternative-scale-methods"></a>Skala alternatywnych metod
Skalowanie zasobów jest najłatwiejszym i najbardziej skutecznym sposobem, aby zwiększyć wydajność bazy danych bez konieczności zmieniania kodu bazy danych lub aplikacji.
W niektórych przypadkach nawet najwyższych poziomów wydajności i optymalizacje wydajności może nie obsługiwać obciążenia o pomyślnym i ekonomiczna metoda. W tym przypadku masz inne funkcje umożliwiające skalowanie bazy danych:
-   [Odczyt skalowalnego w poziomie](sql-database-read-scale-out.md) to funkcja dostępna w którym otrzymujesz jedna replika przeznaczona tylko do odczytu danych gdzie wykonania wymagających zapytań tylko do odczytu, takich jak raporty. Repliki tylko do czerwonego będzie obsługiwać obciążenia tylko do odczytu bez wpływu na obciążenie zasobów w głównej bazie danych.
-   [Fragmentowanie bazy danych](sql-database-elastic-scale-introduction.md) to zbiór technik umożliwia podzielić dane na kilka baz danych i ich niezależne skalowanie.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać informacje dotyczące poprawy wydajności bazy danych, zmieniając kod bazy danych, zobacz [Znajdowanie i stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md).
- Aby dowiedzieć się, jak umożliwiając analizy wbudowaną bazą danych, zoptymalizuj bazę danych, zobacz [dostrajania automatycznego](sql-database-automatic-tuning.md).
- Aby uzyskać informacji na temat odczytu skalowalnego w poziomie w usłudze Azure SQL Database, zobacz jak [załadować równoważenia obciążeń związanych z zapytaniami tylko do odczytu są używane tylko do odczytu repliki](sql-database-read-scale-out.md).
- Aby uzyskać informacji na temat fragmentowanie bazy danych, zobacz [skalowanie w poziomie za pomocą usługi Azure SQL Database](sql-database-elastic-scale-introduction.md).

