---
title: Co to jest usługa Azure SQL Database?
description: 'Wprowadzenie do bazy danych SQL: szczegóły techniczne i możliwości relacyjnego systemu zarządzania bazami danych firmy Microsoft (RDBMS) w chmurze.'
keywords: wprowadzenie do usługi sql, co to jest sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 209b4136678e6f04666b4a2b6180f4768bf6afc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500813"
---
# <a name="what-is-the-azure-sql-database-service"></a>Co to jest usługa Azure SQL Database?

Usługa Azure SQL Database to relacyjna baza danych ogólnego przeznaczenia, świadczona jako usługa zarządzana. Dzięki niemu można utworzyć warstwę magazynu danych o wysokiej dostępności i wysokiej wydajności dla aplikacji i rozwiązań na platformie Azure. Baza danych SQL może być właściwym wyborem dla różnych nowoczesnych aplikacji w chmurze, ponieważ umożliwia przetwarzanie zarówno danych relacyjnych, jak i [struktur nierelacyjnych,](sql-database-multi-model-features.md)takich jak wykresy, JSON, przestrzenne i XML.

Jest on oparty na najnowszej stabilnej wersji [aparatu bazy danych programu Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Można użyć zaawansowanych funkcji przetwarzania zapytań, takich jak [wysokowydajne technologie w pamięci](sql-database-in-memory.md) i [inteligentne przetwarzanie zapytań.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json) W rzeczywistości najnowsze możliwości programu SQL Server są wydawane najpierw do bazy danych SQL, a następnie do samego programu SQL Server. Otrzymujesz najnowsze możliwości programu SQL Server bez narzutów za poprawki lub uaktualnianie, przetestowane w milionach baz danych. 

Baza danych SQL umożliwia łatwe definiowanie i skalowanie wydajności w dwóch różnych modelach zakupu: [modelu zakupów opartego na wynikach vCore](sql-database-service-tiers-vcore.md) i [modelu zakupu opartego na UTU.](sql-database-service-tiers-dtu.md) Baza danych SQL to w pełni zarządzana usługa, która ma wbudowaną wysoką dostępność, kopie zapasowe i inne typowe operacje konserwacji. Firma Microsoft obsługuje wszystkie poprawki i aktualizowanie kodu SQL i systemu operacyjnego. Nie musisz zarządzać podstawową infrastrukturą.

> [!NOTE]
> Aby uzyskać odpowiednie terminy i ich definicje, zobacz [glosariusz terminów bazy danych SQL](sql-database-glossary-terms.md).

## <a name="deployment-models"></a>Modele wdrażania

Usługa Azure SQL Database udostępnia następujące opcje wdrażania na potrzeby bazy danych Azure SQL Database:

![Diagram opcji wdrażania](./media/sql-database-technical-overview/deployment-options.png)

- [Pojedyncza baza danych](sql-database-single-database.md) reprezentuje w pełni zarządzaną, izolowana baza danych. Możesz użyć tej opcji, jeśli masz nowoczesne aplikacje w chmurze i mikrousług, które wymagają jednego niezawodnego źródła danych. Pojedyncza baza danych jest podobna do [zawartej bazy danych](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) w [programie Microsoft SQL Server Database Engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [Wystąpienie zarządzane](sql-database-managed-instance.md) jest w pełni zarządzanym wystąpieniem [aparatu bazy danych programu Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Zawiera zestaw baz danych, które mogą być używane razem. Użyj tej opcji, aby ułatwić migrację lokalnych baz danych programu SQL Server do chmury platformy Azure oraz aplikacji, które muszą korzystać z funkcji bazy danych udostępnianych przez aparat baz danych programu SQL Server.
- [Pula elastyczna](sql-database-elastic-pool.md) to zbiór [pojedynczych baz danych](sql-database-single-database.md) z udostępnionym zestawem zasobów, takich jak procesor CPU lub pamięć. Pojedyncze bazy danych mogą być przenoszone do i z puli elastycznej.

> [!IMPORTANT]
> Aby zrozumieć różnice funkcji między bazą danych SQL i programem SQL Server, a także różnice między różnymi opcjami wdrażania bazy danych SQL Azure, zobacz [funkcje bazy danych SQL](sql-database-features.md).

Baza danych SQL zapewnia przewidywalną wydajność z wieloma typami zasobów, warstwami usług i rozmiarami obliczeń. Zapewnia dynamiczną skalowalność bez przestojów, wbudowaną inteligentną optymalizację, globalną skalowalność i dostępność oraz zaawansowane opcje zabezpieczeń. Te możliwości pozwalają skupić się na szybkim tworzeniu aplikacji i przyspieszaniu czasu na rynku, a nie na zarządzaniu maszynami wirtualnymi i infrastrukturą. Usługa bazy danych SQL znajduje się obecnie w 38 centrach danych na całym świecie, dzięki czemu można uruchomić bazę danych w centrum danych w pobliżu.

## <a name="scalable-performance-and-pools"></a>Skalowalna wydajność i pule

Można zdefiniować ilość przydzielonych zasobów. 
- Z pojedynczych baz danych, każda baza danych jest odizolowana od innych i jest przenośny. Każdy ma własną gwarantowaną ilość zasobów obliczeniowych, pamięci i magazynu. Ilość zasobów przypisanych do bazy danych jest dedykowana tej bazie danych i nie jest udostępniana innym bazom danych na platformie Azure. Można dynamicznie [skalować zasoby pojedynczej bazy danych](sql-database-single-database-scale.md) w górę i w dół. Opcja pojedynczej bazy danych zapewnia różne zasoby obliczeniowe, pamięci i magazynu dla różnych potrzeb. Na przykład można uzyskać od 1 do 80 vCores lub 32 GB do 4 TB. [Warstwa usług hiperskali](sql-database-service-tier-hyperscale.md) dla pojedynczej bazy danych umożliwia skalowanie do 100 TB z możliwością szybkiego tworzenia kopii zapasowych i przywracania.
- Za pomocą pul elastycznych można przypisać zasoby, które są współużytkowane przez wszystkie bazy danych w puli. Można utworzyć nową bazę danych lub przenieść istniejące pojedyncze bazy danych do puli zasobów, aby zmaksymalizować wykorzystanie zasobów i zaoszczędzić pieniądze. Ta opcja umożliwia również dynamiczne [skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md) w górę i w dół.
- W przypadku wystąpień zarządzanych każde wystąpienie zarządzane jest izolowane od innych wystąpień z gwarantowanymi zasobami. W wystąpieniu zarządzanym bazy danych wystąpień współużytkuje zestaw zasobów. Można dynamicznie [skalować zasoby wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md) w górę i w dół.

Pierwszą aplikację można utworzyć na małej, pojedynczej bazie danych przy niskich kosztach miesięcznie w warstwie usług ogólnego przeznaczenia. Następnie można zmienić jego warstwy usług ręcznie lub programowo w dowolnym momencie do warstwy usług o znaczeniu krytycznym dla firmy, aby zaspokoić potrzeby rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Płacisz tylko za zasoby, których potrzebujesz, gdy ich potrzebujesz.

*Skalowalność dynamiczna* różni się od *skalowania automatycznego*. O skalowaniu automatycznym mówimy, gdy usługa jest skalowana automatycznie na podstawie kryteriów, natomiast dynamiczna skalowalność uwzględnia skalowanie ręczne bez przestojów. Opcja pojedynczej bazy danych obsługuje ręczną skalowalność dynamiczną, ale nie skalowanie automatyczne. Aby zapewnić środowisko bardziej zautomatyzowane, rozważ użycie elastycznych pul baz danych, które umożliwiają bazom danych udostępnianie zasobów w puli w zależności od potrzeb. Inną opcją jest użycie skryptów, które mogą pomóc zautomatyzować skalowalność dla pojedynczej bazy danych. Na przykład zobacz [Używanie programu PowerShell do monitorowania i skalowania pojedynczej bazy danych](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Modele zakupów

Baza danych SQL oferuje następujące modele zakupów:
- [Model zakupów oparty na wynikach vCore](sql-database-service-tiers-vcore.md) umożliwia wybranie liczby owo wirtualnych, ilości pamięci oraz ilości i szybkości przechowywania. Model zakupów oparty na wynikach wirtualnych umożliwia również korzystanie z [usługi Azure Hybrid Benefit dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) w celu uzyskania oszczędności kosztów. Aby uzyskać więcej informacji na temat korzyści hybrydowych platformy Azure, zobacz sekcję "Często zadawane pytania" w dalszej części tego artykułu.
- [Model zakupów oparty na jednostce DTU](sql-database-service-tiers-dtu.md) oferuje połączenie zasobów obliczeniowych, pamięci i we/wy w trzech warstwach usług, aby obsługiwać obciążenia bazy danych w stanie lekkim i dużym. Rozmiary obliczeń w ramach każdej warstwy zapewniają inną kombinację tych zasobów, do których można dodać dodatkowe zasoby magazynu.
- [Model bezserwerowy](sql-database-serverless.md) automatycznie skaluje obliczenia na podstawie zapotrzebowania na obciążenia i rozlicza ilość obliczeń używanych na sekundę. Warstwa obliczeniowa bezserwerowa automatycznie wstrzymuje również bazy danych w okresach nieaktywnych, gdy rozliczany jest tylko magazyn, i automatycznie wznawia bazy danych po powrocie aktywności.

### <a name="service-tiers"></a>Warstwy usług

Usługa Azure SQL Database oferuje trzy warstwy usług, które są przeznaczone dla różnych typów aplikacji:
- [Warstwa usług ogólnego przeznaczenia/standardu](sql-database-service-tier-general-purpose.md) przeznaczona dla typowych obciążeń. Oferuje opcje zrównoważonego obliczeń i pamięci masowej zorientowane na budżet.
- [Warstwa usług Business Critical/Premium](sql-database-service-tier-business-critical.md) przeznaczona dla aplikacji OLTP o wysokim współczynniku transakcji i najniższym opóźnieniu we/wy. Oferuje najwyższą odporność na awarie przy użyciu kilku replik izolowanych.
- [Warstwa usług hiperskali](sql-database-service-tier-hyperscale.md) przeznaczonych dla bardzo dużej bazy danych OLTP i możliwości automatycznego skalowania pamięci masowej i skalowania obliczeń płynnie.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Pule elastyczne umożliwiające zmaksymalizowanie wykorzystania zasobów

W przypadku wielu firm i aplikacji możliwość tworzenia pojedynczych baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza gdy wzorce użycia są względnie przewidywalne. Nieprzewidywalne wzorce użycia mogą utrudniać zarządzanie kosztami i modelem biznesowym. [Pule elastyczne](sql-database-elastic-pool.md) zostały zaprojektowane, aby rozwiązać ten problem. Zasoby wydajności można przydzielić do puli, a nie do pojedynczej bazy danych. Płacisz za zasoby wydajności zbiorczej puli, a nie za wydajność pojedynczej bazy danych.

   ![Grafika przedstawiająca elastyczne pule w wersjach podstawowych, standardowych i premium](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

W przypadku pul elastycznych nie trzeba skupiać się na wybieraniu wydajności bazy danych w górę i w dół, ponieważ zapotrzebowanie na zasoby zmienia się. Bazy danych w puli używają zasobów wydajności puli elastycznej w miarę potrzeb. Buforowane bazy danych zużywają, ale nie przekraczają limitów puli, więc koszt pozostaje przewidywalny, nawet jeśli użycie poszczególnych baz danych nie.

Możesz [dodawać i usuwać bazy danych do puli](sql-database-elastic-pool-manage-portal.md), skalowanie aplikacji z kilku baz danych do tysięcy, wszystko w ramach budżetu, który można kontrolować. Można również kontrolować minimalne i maksymalne zasoby dostępne dla baz danych w puli, aby upewnić się, że żadna baza danych w puli nie używa wszystkich zasobów puli i że każda buforowana baza danych ma gwarantowaną minimalną ilość zasobów. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji typu "oprogramowanie jako usługa" (SaaS), które używają pul elastycznych, zobacz [Wzorce projektowania dla aplikacji SaaS z bazą danych SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Skrypty mogą ułatwić monitorowanie i skalowanie elastycznych pul baz danych. Na przykład zobacz [Używanie programu PowerShell do monitorowania i skalowania puli elastycznej SQL w bazie danych SQL.](scripts/sql-database-monitor-and-scale-pool-powershell.md)

> [!IMPORTANT]
> Wystąpienie zarządzane nie obsługuje pul elastycznych. Zamiast tego wystąpienie zarządzane jest zbiorem baz danych wystąpień, które współużytkuje zasoby wystąpienia zarządzanego.

### <a name="blend-single-databases-with-pooled-databases"></a>Mieszaj pojedyncze bazy danych z bazami danych w puli

Można mieszać pojedyncze bazy danych z pulami elastycznymi i zmieniać warstwy usług pojedynczych baz danych i pul elastycznych, aby dostosować się do sytuacji. Możesz również łączyć i dopasowywać inne usługi platformy Azure z bazą danych SQL, aby spełnić unikatowe potrzeby w zakresie projektowania aplikacji, zwiększyć efektywność kosztów i zasobów oraz odblokować nowe możliwości biznesowe.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Możliwości rozbudowanego monitorowania i zgłaszania alertów

Usługa Azure SQL Database udostępnia zaawansowane funkcje monitorowania i rozwiązywania problemów, które pomagają uzyskać głębszy wgląd w charakterystykę obciążenia. Te funkcje i narzędzia obejmują:
 - Wbudowane możliwości monitorowania zapewniane przez najnowszą wersję aparatu baz danych programu SQL Server. Umożliwiają one znajdowanie szczegółowych informacji o wydajności w czasie rzeczywistym. 
 - Funkcje monitorowania paaS udostępniane przez platformę Azure, które umożliwiają monitorowanie i rozwiązywanie problemów z dużą liczbą wystąpień bazy danych.

[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store), wbudowana funkcja monitorowania programu SQL Server, rejestruje wydajność zapytań w czasie rzeczywistym i umożliwia identyfikowanie potencjalnych problemów z wydajnością i najlepszych konsumentów zasobów. Automatyczne dostrajanie i zalecenia zapewniają porady dotyczące zapytań z regressed wydajności i brakujące lub zduplikowane indeksy. Automatyczne dostrajanie w bazie danych SQL umożliwia ręczne stosowanie skryptów, które mogą rozwiązać problemy, lub umożliwienie zastosowaniu poprawki w bazie danych SQL. Baza danych SQL można również przetestować i sprawdzić, czy poprawka zapewnia pewne korzyści i zachować lub przywrócić zmianę w zależności od wyniku. Oprócz query store i funkcje automatycznego dostrajania, można użyć standardowych [dmv i XEvent](sql-database-monitoring-with-dmvs.md) do monitorowania wydajności obciążenia.

Platforma Azure zapewnia [wbudowane](sql-database-performance-guidance.md) narzędzia do monitorowania wydajności i [alertów](sql-database-insights-alerts-portal.md) w połączeniu z ocenami wydajności, które umożliwiają monitorowanie stanu tysięcy baz danych. Korzystając z tych narzędzi, można szybko ocenić wpływ skalowania w górę lub w dół, na podstawie bieżących lub przewidywanych potrzeb w zakresie wydajności. Ponadto usługa SQL Database może [tworzyć metryki i dzienniki diagnostyczne](sql-database-metrics-diag-logging.md), które ułatwiają monitorowanie. Usługę SQL Database można skonfigurować do przechowywania danych dotyczących użycia zasobów, pracowników i sesji oraz połączeń z jednym z następujących zasobów platformy Azure:

- **Usługa Azure Storage:** do archiwizowania ogromnych ilości danych telemetrycznych za niewielką cenę.
- **Usługi Azure Event Hubs:** Do integracji danych telemetrycznych bazy danych SQL z niestandardowego rozwiązania monitorowania lub gorących potoków.
- **Dzienniki usługi Azure Monitor:** dla wbudowanego rozwiązania do monitorowania z funkcjami raportowania, alertów i łagodzenia.

![Diagram architektury monitorowania platformy Azure](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Możliwości dostępności

W tradycyjnym środowisku programu SQL Server zazwyczaj masz co najmniej dwa komputery skonfigurowane lokalnie. Maszyny te mają dokładne, synchronicznie utrzymywane kopie danych w celu ochrony przed awarią pojedynczej maszyny lub komponentu. To środowisko zapewnia wysoką dostępność, ale nie chroni przed klęską żywiołową niszcząc centrum danych.

Odzyskiwanie po awarii zakłada, że zdarzenie katastrofalne jest geograficznie zlokalizowane na tyle, aby mieć inną maszynę lub zestaw maszyn z kopią danych daleko. W programie SQL Server można użyć grup zawsze na dostępność uruchomionych w trybie asynchroniowym, aby uzyskać tę możliwość. Ludzie często nie chcą czekać na replikację, aby stało się tak daleko przed zatwierdzeniem transakcji, więc istnieje możliwość utraty danych, gdy nieplanowane pracy awaryjnej.

Bazy danych w warstwach usług o krytycznym znaczeniu dla systemu premium i biznesowej [już robią coś bardzo podobnego](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) do synchronizacji grupy dostępności. Bazy danych w niższych warstwach usług zapewniają nadmiarowość za pomocą magazynu przy użyciu [innego, ale równoważnego mechanizmu.](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) Wbudowana logika pomaga chronić przed awarią pojedynczej maszyny. Funkcja aktywnej replikacji geograficznej umożliwia ochronę przed katastrofą, w której niszczony jest cały region.

Strefy dostępności platformy Azure próbuje chronić przed awarią budynku pojedynczego centrum danych w jednym regionie. Pomaga chronić przed utratą zasilania lub sieci do budynku. W bazie danych SQL można umieścić różne repliki w różnych strefach dostępności (różne budynki, skutecznie).

W rzeczywistości umowa dotyczącą poziomu usług [(SLA)](https://azure.microsoft.com/support/legal/sla/) platformy Azure, obsługiwana przez globalną sieć centrów danych zarządzanych przez firmę Microsoft, pomaga utrzymać aplikację z systemem 24/7. Platforma Azure w pełni zarządza każdą bazą danych i gwarantuje brak utraty danych i wysoki procent dostępności danych. Platforma Azure automatycznie obsługuje poprawki, tworzenie kopii zapasowych, replikację, wykrywanie awarii, podstawowe potencjalne awarie sprzętu, oprogramowania lub sieci, wdrażanie poprawek błędów, praca awaryjna, uaktualnianie bazy danych i inne zadania konserwacyjne. Dostępność w warstwie Standardowa jest osiągana przez oddzielenie warstw obliczeń i magazynu. Dostępność w układce jest osiągana przez integrację zasobów obliczeniowych i magazynu w jednym węźle pod kątem wydajności, a następnie implementowanie technologii podobnej do grup dostępności zawsze włączone. Aby zapoznać się z pełnym omówień dotyczących wysokiej dostępności usługi Azure SQL Database, zobacz [dostępność bazy danych SQL](sql-database-high-availability.md). 

Ponadto baza danych SQL zapewnia wbudowaną ciągłość działania i globalne funkcje [skalowalności.](sql-database-business-continuity.md) Należą do nich:

- [Automatyczne kopie zapasowe:](sql-database-automated-backups.md)

  Baza danych SQL automatycznie wykonuje pełne, różnicowe i rejestrowe kopie zapasowe baz danych SQL, aby umożliwić przywrócenie do dowolnego punktu w czasie. W przypadku pojedynczych baz danych i puli baz danych można skonfigurować bazę danych SQL database do przechowywania pełnych kopii zapasowych bazy danych w usłudze Azure Storage w celu długoterminowego przechowywania kopii zapasowych. W przypadku wystąpień zarządzanych można również wykonywać kopie zapasowe tylko do kopiowania w celu długoterminowego przechowywania kopii zapasowych.

- [Przywraca punkt w czasie:](sql-database-recovery-using-backups.md)

  Wszystkie opcje wdrażania bazy danych SQL obsługują odzyskiwanie do dowolnego punktu w czasie w okresie przechowywania automatycznego tworzenia kopii zapasowych dla dowolnej bazy danych SQL.
- [Aktywna replikacja geograficzna:](sql-database-active-geo-replication.md)

  Opcje pojedynczej bazy danych i puli baz danych umożliwiają skonfigurowanie maksymalnie czterech czytelnych pomocniczych baz danych w tych samych lub globalnie rozproszonych centrach danych platformy Azure. Na przykład jeśli masz aplikację SaaS z bazą danych katalogu, która ma dużą liczbę równoczesnych transakcji tylko do odczytu, użyj aktywnej replikacji geograficznej, aby włączyć globalną skalę odczytu. Spowoduje to usunięcie wąskich gardeł na podstawowym, które są spowodowane odczytu obciążeń. W przypadku wystąpień zarządzanych należy użyć grup automatycznego trybu failover.
- [Grupy auto-trybu failover:](sql-database-auto-failover-group.md)

  Wszystkie opcje wdrażania bazy danych SQL umożliwiają korzystanie z grup trybu failover, aby włączyć wysoką dostępność i równoważenie obciążenia w skali globalnej. Obejmuje to przezroczystą replikację geograficzną i tryb failover dużych zestawów baz danych, pul elastycznych i wystąpień zarządzanych. Grupy trybu failover umożliwiają tworzenie globalnie rozproszonych aplikacji SaaS przy minimalnym obciążeniu administracyjnym. Pozostawia to wszystkie kompleksowe monitorowanie, routing i aranżacji pracy awaryjnej do bazy danych SQL.
- [Bazy danych nadmiarowe stref:](sql-database-high-availability.md)

  Baza danych SQL umożliwia aprowizowanie baz danych o znaczeniu krytycznym lub biznesowym lub pul elastycznych w wielu strefach dostępności. Ponieważ te bazy danych i pul elastycznych mają wiele replik nadmiarowych dla wysokiej dostępności, umieszczenie tych replik w wielu strefach dostępności zapewnia większą odporność. Obejmuje to możliwość automatycznego odzyskiwania z awarii skali centrum danych, bez utraty danych.

## <a name="built-in-intelligence"></a>Wbudowane narzędzie analizy

Dzięki bazie danych SQL otrzymujesz wbudowaną inteligencję, która pomaga znacznie zmniejszyć koszty uruchamiania i zarządzania bazami danych, a która maksymalizuje wydajność i bezpieczeństwo aplikacji. Program SQL Database, który przez całą dobę pobiera i przetwarza ogromną ilość danych telemetrycznych, a jednocześnie w pełni szanuje prywatność klientów. Różne algorytmy stale oceniają dane telemetryczne, dzięki czemu usługa może uczyć się i dostosowywać za pomocą aplikacji.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatyczne monitorowanie i dostosowywanie wydajności

Usługa SQL Database udostępnia szczegółowy wgląd w zapytania, których monitorowanie jest wymagane. Baza danych SQL uczy się o wzorcach bazy danych i umożliwia dostosowanie schematu bazy danych do obciążenia. Usługa SQL Database udostępnia [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md) pozwalające na przeglądanie i stosowanie akcji dostrajających.

Jednak ciągłe monitorowanie bazy danych jest trudnym i żmudnym zadaniem, szczególnie w przypadku wielu baz danych. [Usługa Intelligent Insights](sql-database-intelligent-insights.md) wykonuje to zadanie, automatycznie monitorując wydajność bazy danych SQL na dużą skalę. Informuje o problemach z obniżeniem wydajności, identyfikuje główną przyczynę każdego problemu i w miarę możliwości dostarcza zaleceń dotyczących poprawy wydajności.

Zarządzanie ogromną liczbą baz danych może być niemożliwe do wykonania wydajnie nawet przy wszystkich dostępnych narzędziach i raportach udostępnianych przez bazę danych SQL i platformę Azure. Zamiast ręcznie monitorować i dostrajać bazę danych, można rozważyć delegowanie niektórych akcji monitorowania i dostrajania do bazy danych SQL przy użyciu [automatycznego dostrajania](sql-database-automatic-tuning.md). Baza danych SQL automatycznie stosuje zalecenia, testy i weryfikuje każdą z jego akcji dostrajania, aby upewnić się, że wydajność stale się poprawia. W ten sposób baza danych SQL automatycznie dostosowuje się do obciążenia w sposób kontrolowany i bezpieczny. Automatyczne dostrajanie oznacza, że wydajność bazy danych jest dokładnie monitorowana i porównywana przed i po każdej akcji dostrajania. Jeśli wydajność nie udoskonali, akcja strojenia zostanie przywrócona.

Wielu naszych partnerów, którzy [uruchamiają aplikacje wielodostępne SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md) na bazie danych SQL, polega na automatycznym dostrajaniu wydajności, aby upewnić się, że ich aplikacje zawsze mają stabilną i przewidywalną wydajność. Z ich punktu widzenia ta funkcja znacznie zmniejsza ryzyko wystąpienia w środku nocy zdarzenia związanego z wydajnością. Ponadto ponieważ część ich bazy klientów używa również sql server, są one przy użyciu tych samych zaleceń indeksowania dostarczonych przez bazę danych SQL, aby pomóc ich klientów programu SQL Server.

W bazie danych [SQL dostępne](sql-database-automatic-tuning.md)są dwa aspekty automatycznego strojenia:

- **Automatyczne zarządzanie indeksami**: identyfikuje indeksy, które powinny zostać dodane w bazie danych, i indeksy, które powinny zostać z niej usunięte.
- **Automatyczna korekta planu:** Identyfikuje problematyczne plany i rozwiązuje problemy z wydajnością planu SQL.

### <a name="adaptive-query-processing"></a>Adaptacyjne przetwarzanie zapytań

Można użyć [adaptacyjnego przetwarzania zapytań,](/sql/relational-databases/performance/intelligent-query-processing)w tym wykonywania przeplotem dla funkcji wycenionych tabelą wielu instrukcji, sprzężenia zwrotnego w trybie wsadowym i sprzężeń adaptacyjnych w trybie wsadowym. Każda z tych funkcji adaptacyjnego przetwarzania zapytań stosuje podobne techniki "ucz się i adaptuj", pomagając w dalszym rozwiązywaniu problemów z wydajnością związanych z problemami optymalizacji zapytań historycznie nierozwiązywalnymi.

## <a name="advanced-security-and-compliance"></a>Zaawansowane zabezpieczenia i zgodność

Usługa SQL Database oferuje szeroką gamę [wbudowanych funkcji zabezpieczeń i zgodności](sql-database-security-overview.md), co ułatwia spełnienie przez aplikację różnych wymagań dotyczących zabezpieczeń i zgodności.

> [!IMPORTANT]
> Firma Microsoft uzyskała certyfikat Azure SQL Database (wszystkie opcje wdrażania) zgodnie z wieloma standardami zgodności. Aby uzyskać więcej informacji, zobacz [Centrum zaufania platformy Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), gdzie można znaleźć najnowszą listę certyfikatów zgodności bazy danych SQL.

### <a name="advance-threat-protection"></a>Wcześniejsza ochrona przed zagrożeniami

Zaawansowane zabezpieczenia danych to ujednolicony pakiet dla zaawansowanych funkcji zabezpieczeń SQL. Obejmuje funkcje wykrywania i klasyfikowania poufnych danych, zarządzania lukami w zabezpieczeniach bazy danych i wykrywania nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych. Zapewnia jedną lokalizację do włączania i zarządzania tymi możliwościami.

- [Odnajdowanie i klasyfikacja danych:](sql-database-data-discovery-and-classification.md)

  Ta funkcja zapewnia funkcje wbudowane w usługę Azure SQL Database do odnajdowania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Zapewnia wgląd w stan klasyfikacji bazy danych i śledzi dostęp do poufnych danych w bazie danych i poza jej granicami.
- [Ocena podatności na zagrożenia:](sql-vulnerability-assessment.md)

  Ta usługa może wykrywać, śledzić i pomagać w korygowania potencjalnych luk w zabezpieczeniach bazy danych. Zapewnia wgląd w stan zabezpieczeń i zapewnia kroki, które można wykonać w celu rozwiązania problemów z zabezpieczeniami oraz zwiększenia bezpieczeństwa bazy danych.
- [Wykrywanie zagrożeń:](sql-database-threat-detection.md)

  Ta funkcja wykrywa nietypowe działania, które wskazują na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do bazy danych lub ich wykorzystania. Stale monitoruje Twoją bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataków polegających na wstrzyknięciu kodu SQL oraz anomalii we wzorcach dostępu do bazy danych. Alerty wykrywania zagrożeń zawierają szczegółowe informacje o podejrzanej aktywności i zalecają działania dotyczące sposobu badania i łagodzenia zagrożenia.

### <a name="auditing-for-compliance-and-security"></a>Inspekcja zgodności i zabezpieczeń

[Inspekcja](sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie magazynu platformy Azure. Inspekcja może pomóc w zachowaniu zgodności z przepisami, zrozumieniu aktywności w bazie danych i uzyskaniu wglądu w rozbieżności i anomalie, które mogą wskazywać na obawy biznesowe lub podejrzewane naruszenia zabezpieczeń.

### <a name="data-encryption"></a>Szyfrowanie danych

Baza danych SQL pomaga zabezpieczyć dane, zapewniając szyfrowanie. W przypadku danych w ruchu używa [zabezpieczeń warstwy transportowej](https://support.microsoft.com/kb/3135244). W przypadku danych w spoczynku używa [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). W przypadku używanych danych używa [się zawsze zaszyfrowanych](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym

Usługa SQL Database umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft dzięki funkcji [integracji usługi Azure Active Directory](sql-database-aad-authentication.md). Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Usługa Azure Active Directory obsługuje [uwierzytelnianie wieloskładnikowe w](sql-database-ssms-mfa-authentication.md) celu zwiększenia bezpieczeństwa danych i aplikacji, jednocześnie obsługując proces logowania jednokrotnego.

## <a name="easy-to-use-tools"></a>Łatwe w użyciu narzędzia

Dzięki usłudze SQL Database tworzenie i konserwowanie aplikacji jest łatwiejsze i bardziej produktywne. Usługa SQL Database pozwala Ci skoncentrować się na tym, co robisz najlepiej: tworzeniu wspaniałych aplikacji. Można zarządzać i rozwijać w bazie danych SQL przy użyciu narzędzi i umiejętności, które już masz.

- [Portal Azure:](https://portal.azure.com/)

  Aplikacja internetowa do zarządzania wszystkimi usługami platformy Azure.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Bezpłatna, do pobrania aplikacja kliencka do zarządzania dowolną infrastrukturą SQL, od programu SQL Server do bazy danych SQL.
- [Narzędzia danych programu SQL Server w programie Visual Studio:](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

  Bezpłatna aplikacja kliencka do pobrania do tworzenia relacyjnych baz danych programu SQL Server, baz danych SQL, pakietów Integration Services, modeli danych usług Analysis Services i raportów reporting services.
- [Kod programu Visual Studio](https://code.visualstudio.com/docs):

  Bezpłatny, do pobrania, edytor kodu open source dla systemów Windows, macOS i Linux. Obsługuje rozszerzenia, w tym [rozszerzenie mssql](https://aka.ms/mssql-marketplace) do wykonywania zapytań microsoft SQL Server, Azure SQL Database i Usługi Azure SQL Data Warehouse.

Baza danych SQL obsługuje tworzenie aplikacji z python, Java, Node.js, PHP, Ruby i .NET na macOS, Linux i Windows. Usługa SQL Database obsługuje te same [biblioteki połączeń](sql-database-libraries.md) co program SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Często zadawane pytania dotyczące bazy danych SQL

### <a name="what-is-the-current-version-of-sql-database"></a>Jaka jest bieżąca wersja bazy danych SQL?

Bieżąca wersja bazy danych SQL to V12. Wersja V11 została wycofana.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Czy mogę kontrolować, kiedy występuje łatanie przestoju?

Nie. Wpływ poprawek zazwyczaj nie jest zauważalne, jeśli stosuje się [logiki ponawiania w](sql-database-develop-overview.md#resiliency) aplikacji. Aby uzyskać więcej informacji, zobacz [Planowanie zdarzeń konserwacji platformy Azure w usłudze Azure SQL Database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Pytania dotyczące korzyści hybrydowych platformy Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Czy istnieją prawa podwójnego zastosowania z korzyścią hybrydową platformy Azure dla programu SQL Server?

Masz 180 dni praw do podwójnego zastosowania licencji, aby upewnić się, że migracje działają bezproblemowo. Po tym 180-dniowym okresie można używać tylko licencji programu SQL Server w chmurze w bazie danych SQL. Nie masz już praw podwójnego zastosowania lokalnie i w chmurze.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Czym różnią się korzyści hybrydowe platformy Azure dla programu SQL Server od mobilności licencji?

Oferujemy korzyści z mobilności licencji dla klientów programu SQL Server z pakietem Software Assurance. Umożliwia to ponowne przypisanie ich licencji do serwerów udostępnionych partnera. Tej korzyści można wykorzystać na platformach Azure IaaS i AWS EC2.

Korzyści hybrydowe platformy Azure dla programu SQL Server różnią się od mobilności licencji w dwóch kluczowych obszarach:

- Zapewnia korzyści ekonomiczne związane z przenoszeniem wysoce zwirtualizowanych obciążeń na platformę Azure. Klienci programu SQL Server Enterprise Edition mogą uzyskać cztery rdzenie na platformie Azure w jednostce SKU ogólnego przeznaczenia dla każdego rdzenia, który posiadają lokalnie dla wysoce zwirtualizowanych aplikacji. Mobilność licencji nie pozwala na żadne specjalne korzyści kosztowe związane z przenoszeniem zwirtualizowanych obciążeń do chmury.
- Zapewnia miejsce docelowe PaaS na platformie Azure (wystąpienie zarządzanej bazy danych SQL), które jest wysoce zgodne z sql server w środowisku lokalnym.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jakie są określone prawa korzyści hybrydowej platformy Azure dla programu SQL Server?

Klienci bazy danych SQL mają następujące prawa skojarzone z korzyścią hybrydową platformy Azure dla programu SQL Server:

|Ślad licencji|Co daje korzyści hybrydowe platformy Azure dla programu SQL Server?|
|---|---|
|Podstawowi klienci programu SQL Server Enterprise Edition z pakietem SA|<li>Może płacić podstawową stawkę na jednostkę SKU ogólnego przeznaczenia lub krytycznej dla firmy</li><br><li>1 rdzeń lokalny = 4 rdzenie w jednostce SKU ogólnego przeznaczenia</li><br><li>1 rdzeń lokalny = 1 rdzeń w jednostce SKU o krytycznym znaczeniu dla firmy</li>|
|Podstawowi klienci programu SQL Server Standard Edition z pakietem SA|<li>Może płacić stawkę podstawową tylko na jednostce SKU ogólnego przeznaczenia</li><br><li>1 rdzeń lokalny = 1 rdzeń w jednostce SKU ogólnego przeznaczenia</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Kontakt z zespołem inżynierów programu SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Zadawaj pytania dotyczące administracji bazami danych.
- [Przepełnienie stosu:](https://stackoverflow.com/questions/tagged/sql-server)Zadawaj pytania dotyczące rozwoju.
- [MsDN Forum](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Zadawaj pytania techniczne.
- [Opinie](https://aka.ms/sqlfeedback): Zgłoś błędy i funkcję żądania.
- [Reddit](https://www.reddit.com/r/SQLServer/): Dyskutować SQL Server.

## <a name="next-steps"></a>Następne kroki

- Zobacz [stronę cennik dla](https://azure.microsoft.com/pricing/details/sql-database/) porównania kosztów i kalkulatory dotyczące pojedynczych baz danych i pul elastycznych.
- Zapoznaj się z tymi przewodnikami Szybki start, aby rozpocząć:

  - [Tworzenia bazy danych SQL w witrynie Azure Portal](sql-database-single-database-get-started.md)  
  - [Tworzenia bazy danych SQL za pomocą interfejsu wiersza polecenia platformy Azure](sql-database-get-started-cli.md)
  - [Tworzenie bazy danych SQL za pomocą programu PowerShell](sql-database-get-started-powershell.md)

- Aby uzyskać zestaw przykładów interfejsu wiersza polecenia platformy Azure i programu PowerShell, zobacz:
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi SQL Database](sql-database-cli-samples.md)
  - [Przykłady programu Azure PowerShell dla usługi SQL Database](sql-database-powershell-samples.md)

- Aby uzyskać informacje o nowych możliwościach w miarę ich ogłaszania, zobacz [Azure Roadmap for SQL Database](https://azure.microsoft.com/roadmap/?category=databases).
- Zobacz [blog usługi Azure SQL Database](https://azure.microsoft.com/blog/topics/database), w którym członkowie zespołu produktów programu SQL Server blogu o wiadomości i funkcje bazy danych SQL.

