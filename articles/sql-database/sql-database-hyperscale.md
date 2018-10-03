---
title: Usługi Azure SQL Database na dużą skalę — omówienie | Dokumentacja firmy Microsoft
description: W tym temacie opisano warstwę usługi na dużą skalę w modelu zakupu opartego na rdzeniach wirtualnych w usłudze Azure SQL Database oraz wyjaśniono, jak to różni się od warstwy usług ogólnego przeznaczenia i krytyczne dla działania firmy.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/23/2018
ms.openlocfilehash: 18f354f931995731bf4f0da47e830a575e96fc7c
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237776"
---
# <a name="what-is-the-hyperscale-service-tier-preview-in-azure-sql-database"></a>Co to jest warstwę usługi na dużą skalę (wersja zapoznawcza) w usłudze Azure SQL Database?

Warstwy usług na dużą skalę w usłudze Azure SQL Database jest najnowsza warstwy usług w modelu zakupu opartego na rdzeniach wirtualnych. Ta warstwa usługi jest wysoce skalowalny magazyn i warstwy wydajności obliczeniowej, który korzysta z architektury platformy Azure do skalowania magazynu i zasoby obliczeniowe dla usługi Azure SQL Database w znacznym stopniu po przekroczeniu limitów dostępnych dla firm i ogólnego przeznaczenia Warstwy usług krytycznych.

> [!IMPORTANT]
> Warstwy usług na dużą skalę jest obecnie w publicznej wersji zapoznawczej i jest dostępny w niektórych regionach platformy Azure. Aby region pełną listę, zobacz [na dużą skalę usługa warstwy dostępne regiony](#hyperscale-regions)

## <a name="what-are-the-capabilities-of-the-hyperscale-service-tier"></a>Jakie są możliwości warstwy usług na dużą skalę

Warstwy usług na dużą skalę w usłudze Azure SQL Database zapewnia następujące dodatkowe funkcje:

- Obsługa maksymalnie 100 TB rozmiar bazy danych 
- Szybsze dużych baz danych kopie zapasowe (oparte na migawki plików)
- Szybsze bazy danych przywraca (oparte na migawki plików) 
- Ogólną wydajność ze względu na większą przepływność dziennika i krótszy czas zatwierdzenia transakcji, niezależnie od ilości danych 
- Szybkie skalowanie w poziomie — można udostępnić co najmniej jeden węzeł tylko do odczytu dla odciążania obciążenia odczytu i do użytku jako spełniają hot
- Szybkie skalowanie w górę — możesz można w stałym czasie skalować zasoby obliczeniowe, aby pomieścić dużych obciążeń, jak i kiedy potrzebne i następnie skalowanie zasobów obliczeniowych wróci, gdy nie jest potrzebny. 

Warstwy usługi w Hiperskali usuwa wielu praktycznych limitów tradycyjnie widoczne w bazach danych w chmurze. Gdzie większość innych baz danych są ograniczone zasoby dostępne w jednym węźle baz danych w warstwie usługi w Hiperskali mają bez tych ograniczeń. Za pomocą jego architekturę elastycznego magazynu można skalować w poziomie pojemność magazynu zgodnie z potrzebami. W rzeczywistości baz danych na dużą skalę, nie są tworzone z zdefiniowany maksymalny rozmiar. Bazę danych na dużą skalę wraz ze wzrostem, stosownie do potrzeb — i opłata jest naliczana tylko za pojemność, której używasz. Warstwy usług na dużą skalę intensywnie odczytujących obciążeń zapewnia szybkiego skalowania w poziomie przez udostępnienie dodatkowe repliki do odczytu, w razie potrzeby przeniesienie obciążeń odczytu. 

Ponadto czas wymagany do tworzenia kopii zapasowych bazy danych lub Skaluj w górę lub w dół nie jest już powiązany z ilością danych w bazie danych. Bazy danych na dużą skalę kopię zapasową można wykonywać niemal natychmiastowe. Baza danych w dziesiątki terabajtów można również skalować w górę lub w dół, w ciągu kilku minut. Ta funkcja powoduje zwolnienie z wątpliwości dotyczących są zapakowane w przez wybrane opcje konfiguracji początkowej. 

Aby uzyskać więcej informacji na temat rozmiarów wystąpień obliczeniowych dla warstwy usług na dużą skalę, zobacz [Wybieranie warstwy usług (rdzeń wirtualny), obliczeniowych, pamięci, magazynu i zasoby We/Wy](sql-database-service-tiers-vcore.md).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kto powinien rozważyć warstwę usługi na dużą skalę

O dużej skali, z którą warstwę usług jest przeznaczone głównie dla klientów, którzy mają dużych baz danych albo w środowisku lokalnym i chcesz modernizuj swoje aplikacje dzięki przeniesieniu do chmury lub dla klientów, którzy są już w chmurze i są ograniczone przez maksymalny rozmiar bazy danych ograniczenia (1 – 4 TB). Celem jest także dla klientów, którzy wyszukiwania o wysokiej wydajności i wysokiej skalowalności dla magazynu i obliczeń.

Warstwy usługi w Hiperskali obsługuje wszystkich obciążeń programu SQL Server, ale przede wszystkim jest on zoptymalizowany pod kątem OLTP. Warstwy usługi w Hiperskali obsługuje także hybrydowych i analityczne obciążenia (składnicy danych). 

> [!IMPORTANT]
> Pule elastyczne nie obsługują warstwy usług na dużą skalę.

## <a name="architecture-distributing-functions-to-isolate-capabilities"></a>Architektura: Dystrybucja funkcji do izolowania możliwości

W przeciwieństwie do tradycyjnych baz danych, które mają scentralizowane wszystkie funkcje zarządzania danych w jednej lokalizacji/process (nawet więc o nazwie rozproszonych baz danych w środowisku produkcyjnym już dziś wiele kopii aparatu monolityczne danych) oddziela bazę danych na dużą skalę aparat przetwarzania zapytań, gdzie semantyka różnych aparatów danych rozdzielić ze składników, które zapewniają długoterminowego przechowywania i trwałości dla danych. W ten sposób pojemności magazynu można sprawnie skalować w poziomie w zakresie, w jakim potrzebne (początkowa docelowy to 100 TB). Repliki tylko do odczytu udostępniać te same składniki obliczeniowych, więc żadna kopia danych jest wymagany do uruchomienia nowej repliki do odczytu.

Na poniższym diagramie przedstawiono różne rodzaje węzłów w bazie danych o dużej skali:

![architektura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Bazę danych na dużą skalę zawiera różne rodzaje węzłów:
 
### <a name="compute-node"></a>Węzeł obliczeniowy

Węzeł obliczeniowy jest, gdzie znajduje się w aparacie relacyjnym, dzięki czemu występują wszystkie elementy języka, przetwarzanie zapytań i tak dalej. Wszystkie interakcje użytkownika z bazy danych na dużą skalę możliwe za pomocą węzłów obliczeniowych. Obliczenia węzły mają oparte na dyskach SSD (oznaczony RBPEX - odporne na błędy rozszerzenie puli buforów na powyższym diagramie) pamięci podręczne, aby zminimalizować liczbę sieci dwustronne wymagane do pobierania strony danych. Ma w jednym węźle obliczeniowym podstawowy, w których są przetwarzane obciążenia odczytu i zapisu oraz transakcji. Istnieją co najmniej jeden węzeł pomocnicza usługa obliczeniowa, które działają jako gorąca wstrzymania węzłów na potrzeby trybu failover, a także działać jako węzły obliczeniowe tylko do odczytu dla odciążania, przeczytaj obciążeń (Jeśli ta funkcja jest konieczne).

### <a name="page-server-node"></a>Węzeł serwera strony

Serwery na stronie są systemy reprezentujący aparatu magazynu skalowanych w poziomie.  Każdy serwer strony jest odpowiedzialny za podzbiór stron w bazie danych.  Nominalnie każdy serwer strony steruje 1 terabajt danych. Żadne dane nie są udostępniane w więcej niż jeden serwer strony (poza repliki, które są przechowywane w celu zapewnienia nadmiarowości i dostępności). Zadania serwera strony jest obsługiwać strony bazy danych w węzłach obliczeniowych na żądanie i zachować strony, zaktualizować, ponieważ transakcje aktualizacji danych. Serwery na stronie są zawsze na bieżąco przez odtworzenie rekordów dziennika usługi dziennika. Serwery na stronie utrzymaniem oparte na dyskach SSD pamięci podręczne, aby zwiększyć wydajność. Możliwość długoterminowego magazynowania danych strony jest przechowywana w usłudze Azure Storage dla dodatkowej niezawodności.

### <a name="log-service-node"></a>Węzeł usługi dziennika

Węzła usługi log akceptuje rekordów dziennika z węzła obliczeniowego podstawowego utrzymuje się je w trwałość pamięci podręcznej i przekazuje rekordy dziennika do pozostałych węzłów obliczeniowych (dzięki czemu mogą aktualizować ich pamięciami podręcznymi) oraz na serwerach odpowiednie strony, aby dane mogą być zaktualizowane t w tym miejscu. W ten sposób wszystkie zmiany danych z węzła obliczeniowego podstawowego są propagowane przez usługę log do wszystkich węzłów obliczeniowych dodatkowej i serwery na stronie. Na koniec rekordy dziennika są przekazywane do magazynu długoterminowego w usłudze Azure Storage, która jest repozytorium nieograniczony magazyn. Ten mechanizm eliminuje konieczność obcinanie dziennika częste. Usługa dziennika ma również lokalnej pamięci podręcznej, aby przyspieszyć dostęp.

### <a name="azure-storage-node"></a>Węzeł usługi Azure storage

Węzeł usługi Azure storage jest ostatecznym miejscem docelowym danych z serwerów strony. Ten magazyn jest używany do wykonywania kopii zapasowych, a także jak w przypadku replikacji między regionami platformy Azure. Tworzenie kopii zapasowych składają się z migawkami plików danych. Przywracanie operacji są szybkie z migawek i przywrócenie danych do dowolnego punktu w czasie. 

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Kopie zapasowe są base migawki plików i dlatego są prawie natychmiastowe. Rozdzielenie magazynu i mocy obliczeniowej Włącz wypychanie szczegółów operacji wykonywania kopii zapasowej i przywracania do warstwy magazynu zmniejszyć obciążenie przetwarzania w węźle obliczeniowym podstawowego. W rezultacie kopii zapasowej dużej bazy danych nie ma wpływu na wydajność węzła obliczeniowego podstawowego. Podobnie przeprowadzać operacje przywracania są wykonywane tylko przez kopiowanie migawki plików i jako takie nie rozmiar operacji danych. Aby przeprowadzać operacje przywracania w obrębie tego samego konta magazynu operacja przywracania jest szybkie.

## <a name="scale-and-performance-advantages"></a>Zalety skalowalność i wydajność

Dzięki możliwości szybkiego uruchomienia dodatkowych tylko do odczytu węzłów obliczeniowych w górę/w dół Hiperskali architektura umożliwia znaczne odczyt możliwości skalowania i można także zwolnić węzła obliczeniowego podstawowego do obsługi liczby żądań zapisu. Ponadto węzły obliczeniowe można skalować w górę/w dół szybko z uwagi na architekturę magazyn udostępniony architektury na dużą skalę. 

### <a name="a-namehyperscale-regions-hyperscale-service-tier-available-regions"></a><a name="hyperscale-regions"> Dostępne regiony warstwę usługi na dużą skalę
Warstwy usług na dużą skalę jest obecnie w publicznej wersji zapoznawczej i jest dostępna w następujących regionach świadczenia usługi Azure: EastUS1 EastUS2, WestUS2, CentralUS, NorthCentralUS, WestEurope, NorthEurope, UKWest, AustraliaEast, AustraliaSouthEast, SouthEastAsia, JapanEast, KoreaCentral

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacji o warstwach usługi, zobacz [warstwy usług](sql-database-service-tiers.md)
- Zobacz [Przegląd zasobów limity na serwerze logicznym](sql-database-resource-limits-logical-server.md) uzyskać informacji dotyczących ograniczeń na poziomach serwera i subskrypcji.
- Zakupu limity modelu pojedynczej bazy danych, zobacz [usługi Azure SQL Database oparty na rdzeniach wirtualnych zakupem modelu limity dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md).
- Dla funkcji i listy porównanie, zobacz [typowe funkcje SQL](sql-database-features.md).
