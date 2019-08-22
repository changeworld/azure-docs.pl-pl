---
title: Co to jest usługa Azure SQL Database? | Microsoft Docs
description: 'Wprowadzenie do usługi SQL Database: szczegóły techniczne i możliwości systemu zarządzania relacyjnymi bazami danych (RDBMS) w chmurze firmy Microsoft.'
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
ms.openlocfilehash: 1c4fae5c41f4f23c4a7fe3135b602133aa69aacd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873729"
---
# <a name="what-is-azure-sql-database-service"></a>Co to jest usługa Azure SQL Database

Azure SQL Database to usługa zarządzana relacyjnej bazy danych ogólnego przeznaczenia, która umożliwia tworzenie warstwy magazynu danych o wysokiej dostępności dla aplikacji i rozwiązań w chmurze Microsoft Azure. SQL Database może być właściwym wyborem dla różnych nowoczesnych aplikacji w chmurze, ponieważ umożliwia korzystanie z zaawansowanych funkcji przetwarzania zarówno danych relacyjnych, jak i nierelacyjnych [struktur](sql-database-multi-model-features.md) , takich jak wykresy, JSON, przestrzenne i XML. Jest on oparty na najnowszej stabilnej wersji [aparatu bazy danych Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) i umożliwia korzystanie z bogatego zestawu zaawansowanych funkcji przetwarzania zapytań, takich jak [wysoka wydajność technologii w pamięci](sql-database-in-memory.md) i [inteligentne przetwarzanie zapytań. ](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json).
Zgodnie ze strategią firmy Microsoft skupiającej się na chmurze najnowsze funkcjonalności programu SQL Server są wydawane najpierw w usłudze SQL Database, a dopiero później w samym programie SQL Server. Podejście to umożliwia użytkownikom dostęp do najnowszych możliwości programu SQL Server bez potrzeby wdrażania poprawek lub wykonywania uaktualnień — a na dodatek funkcje te są już przetestowane w milionach baz danych. SQL Database umożliwia łatwe definiowanie i skalowanie wydajności w ramach dwóch różnych modeli zakupów: [modelu zakupu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md) i [modelu zakupu opartego](sql-database-service-tiers-dtu.md)na jednostkach DTU. SQL Database jest w pełni zarządzana usługa, która ma wbudowane dostępności, kopie zapasowe i inne typowe operacje konserwacyjne. Firma Microsoft obsługuje bezproblemowo wszystkie poprawki i aktualizowanie kodu SQL i systemu operacyjnego, a następnie wyodrębnia wszystkie zarządzanie podstawową infrastrukturą.

> [!NOTE]
> Słownik terminów w Azure SQL Database można znaleźć w artykule [SQL Database słownik terminów](sql-database-glossary-terms.md)

Usługa Azure SQL Database udostępnia następujące opcje wdrażania na potrzeby bazy danych Azure SQL Database:

![deployment-options](./media/sql-database-technical-overview/deployment-options.png)

- [Pojedyncza baza danych](sql-database-single-database.md) reprezentuje w pełni zarządzaną bazę danych, która jest idealnym wyborem dla nowoczesnych aplikacji i mikrousług w chmurze, które wymagają pojedynczego wiarygodnego źródła danych. Pojedyncza baza danych jest podobna do [zawartych baz](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) danych w [aparacie Microsoft SQL Server Database](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [Wystąpienie zarządzane](sql-database-managed-instance.md) to w pełni zarządzane wystąpienie [aparatu bazy danych Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) zawierające zestaw baz danych, które mogą być używane razem. Jest to idealne rozwiązanie umożliwiające łatwe Migrowanie lokalnych baz danych SQL Server do chmury platformy Azure i dla aplikacji, które muszą korzystać z zaawansowanych funkcji bazy danych, które zapewnia SQL Server aparat bazy danych.
- [Elastyczna Pula](sql-database-elastic-pool.md) to zbiór [pojedynczych baz danych](sql-database-single-database.md) z współdzielonym zestawem zasobów, takich jak procesor CPU lub pamięć. Pojedyncze bazy danych można przenieść do i z puli elastycznej.

> [!IMPORTANT]
> Aby zrozumieć różnice między funkcjami SQL Database i SQL Server, a także różnice między różnymi Azure SQL Database opcjami wdrażania, zobacz [funkcje SQL](sql-database-features.md).

SQL Database zapewnia przewidywalną wydajność z wieloma typami zasobów, warstwami usług i rozmiarami obliczeniowymi, które zapewniają dynamiczną skalowalność bez przestojów, wbudowaną inteligentną optymalizację, globalną skalowalność i dostępność oraz zaawansowane zabezpieczenia Opcje — wszystko z administracją niemal zero. Te możliwości pozwalają Ci skoncentrować się na szybkim tworzeniu aplikacji i skracaniu czasu wejścia na rynek, a nie na poświęcaniu cennego czasu i cennych zasobów na zarządzanie maszynami wirtualnymi i infrastrukturą. Usługa SQL Database jest obecnie dostępna w 38 centrach danych na całym świecie z coraz większą liczbą centrów danych regularnie dołączanych, dzięki czemu możesz uruchomić swoją bazę danych w centrum danych położonym w pobliżu.

## <a name="scalable-performance-and-pools"></a>Skalowalna wydajność i pule

Wszystkie typy SQL Database umożliwiają zdefiniowanie ilości zasobów, które zostaną przypisane. 
- W przypadku pojedynczych baz danych każda baza danych jest odizolowana od siebie i przenośna, z własnym zagwarantowaną ilością zasobów obliczeniowych, pamięci i magazynu. Ilość zasobów przypisanych do bazy danych jest przeznaczona dla tej bazy danych i nie będzie współdzielona z innymi bazami danych w chmurze platformy Azure. Daje również możliwość dynamicznego [skalowania zasobów pojedynczej bazy danych](sql-database-single-database-scale.md) w górę i w dół. Pojedyncza baza danych oferuje różne zasoby obliczeniowe, pamięci i magazynu dla różnych potrzeb, które różnią się od 1 do 80 rdzeni wirtualnych, 32 GB do 4 TB itd. [Warstwa usługi](sql-database-service-tier-hyperscale.md) dla jednej bazy danych umożliwia skalowanie do 100 TB przy użyciu funkcji szybkiego tworzenia kopii zapasowych i przywracania.
- W przypadku pul elastycznych można przypisywać zasoby, które będą współużytkowane przez wszystkie bazy danych w puli. Można utworzyć nową bazę danych lub przenieść istniejące pojedyncze bazy danych do puli zasobów, aby zmaksymalizować wykorzystanie zasobów i zaoszczędzić pieniądze oraz dynamicznie [skalować zasoby puli elastycznej](sql-database-elastic-pool-scale.md) w górę i w dół.
- Z wystąpieniami zarządzanymi każde wystąpienie zarządzane jest odizolowane od innych wystąpień z gwarantowanymi zasobami. W wystąpieniu zarządzanym bazy danych wystąpienia korzystają z zestawu zasobów i umożliwiają dynamiczne [skalowanie zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md) w górę i w dół.

Możesz utworzyć swoją pierwszą aplikację w małej, pojedynczej bazie danych w niskich kosztach miesięcznie w warstwie usług ogólnego przeznaczenia, a następnie ręcznie zmienić jej warstwę usługi lub programowo w dowolnym momencie do warstwy usługi krytycznej dla działania firmy, aby zaspokoić potrzeby rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów oraz płacenie tylko za potrzebne zasoby i tylko gdy są używane.

Dynamiczna skalowalność różni się od skalowania automatycznego. O skalowaniu automatycznym mówimy, gdy usługa jest skalowana automatycznie na podstawie kryteriów, natomiast dynamiczna skalowalność uwzględnia skalowanie ręczne bez przestojów. Pojedyncza baza danych obsługuje ręczną skalowalność, ale nie Skalowanie automatyczne. Aby zapewnić środowisko bardziej *zautomatyzowane*, rozważ użycie elastycznych pul baz danych, które umożliwiają bazom danych udostępnianie zasobów w puli w zależności od potrzeb. Istnieją jednak skrypty, które mogą pomóc zautomatyzować skalowalność dla pojedynczej bazy danych. Aby zapoznać się z przykładem, zobacz [Używanie programu PowerShell do monitorowania i skalowania pojedynczej bazy danych](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>Modele zakupów, warstwy usług, rozmiary obliczeń i kwoty magazynu

SQL Database oferuje dwa modele zakupu:
- [Model zakupu oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md) umożliwia wybranie liczby rdzeni wirtualnych, ilości lub pamięci oraz ilości i szybkości magazynu. Model zakupu oparty na rdzeń wirtualny umożliwia również korzystanie z [Korzyść użycia hybrydowego platformy Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) w celu uzyskania oszczędności kosztów. Aby uzyskać więcej informacji na temat Korzyść użycia hybrydowego platformy Azure, zobacz [często zadawane pytania](#sql-database-frequently-asked-questions-faq).
- [Model zakupów opartych na](sql-database-service-tiers-dtu.md) jednostkach DTU oferuje mieszankę zasobów obliczeniowych, pamięci i operacji we/wy w trzech warstwach usług w celu obsługi lekkich i intensywnych obciążeń związanych z bazami danych. Rozmiary obliczeniowe w poszczególnych warstwach zapewniają inną kombinację tych zasobów, do których można dodać dodatkowe zasoby magazynu.

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Pule elastyczne umożliwiające zmaksymalizowanie wykorzystania zasobów

W przypadku wielu firm i aplikacji możliwość tworzenia pojedynczych baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza gdy wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. [Pule elastyczne](sql-database-elastic-pool.md) zostały zaprojektowane, aby rozwiązać ten problem. Koncepcja jest prosta. Zasoby wydajności są przydzielane do puli, a nie do pojedynczej bazy danych i płatne za zbiorowe zasoby wydajności puli, a nie za wydajność pojedynczej bazy danych.

   ![pule elastyczne](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Pule elastyczne nie wymagają koncentrowania się na skalowaniu wydajności bazy danych w górę i w dół w miarę zmian zapotrzebowania na zasoby. Bazy danych w puli używają zasobów wydajności puli elastycznej w miarę potrzeb. Bazy danych w puli zużywają zasoby, ale nie przekraczają limitów puli, więc koszt pozostaje przewidywalny nawet wtedy, gdy nie można przewidzieć użycia poszczególnych baz danych. Ponadto możliwe jest [dodawanie i usuwanie baz danych w puli](sql-database-elastic-pool-manage-portal.md) i skalowanie aplikacji od kilku do tysięcy baz danych — wszystko w ramach kontrolowanego budżetu. Możesz również kontrolować minimum i maksimum zasobów dostępnych dla baz danych w puli w celu zapewnienia, że żadna baza danych w puli nie używa wszystkich zasobów i jednocześnie każda baza danych w puli ma gwarantowaną minimalną ilość zasobów. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

Skrypty mogą ułatwić monitorowanie i skalowanie elastycznych pul baz danych. Na przykład zobacz temat [Monitorowanie i skalowanie elastycznej puli SQL w usłudze Azure SQL Database za pomocą programu PowerShell](scripts/sql-database-monitor-and-scale-pool-powershell.md)

> [!IMPORTANT]
> Wystąpienie zarządzane nie obsługuje pul elastycznych. Zamiast tego wystąpienie zarządzane jest zbiorem baz danych wystąpień, które współużytkują zasoby wystąpienia zarządzanego.

### <a name="blend-single-databases-with-pooled-databases"></a>Mieszaj pojedyncze bazy danych z bazami danych w puli

Możesz mieszać pojedyncze bazy danych z elastycznymi pulami i szybko i łatwo zmieniać warstwy usług pojedynczych baz danych i elastycznych pul, aby dostosować je do swojej sytuacji. Korzystając z siły i zasięgu platformy Azure, możesz łączyć inne usługi Azure z usługą SQL Database i dopasowywać je do niej, aby zaspokoić potrzeby unikatowego projektu aplikacji, zwiększyć efektywność kosztową i poprawić wykorzystanie zasobów oraz otworzyć się na nowe możliwości biznesowe.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Możliwości rozbudowanego monitorowania i zgłaszania alertów

Azure SQL Database udostępnia zestaw zaawansowanych funkcji monitorowania i rozwiązywania problemów, które mogą ułatwić uzyskanie pełnych informacji o cechach obciążenia. Funkcje i narzędzia mogą być klasyfikowane jako:
 - Wbudowane funkcje monitorowania dostępne w najnowszej wersji aparatu SQL Server Database, które umożliwiają znalezienie szczegółowych informacji o wydajności w czasie rzeczywistym. 
 - Możliwości monitorowania PaaS zapewniane przez platformę Azure, która umożliwia łatwe monitorowanie dużej liczby wystąpień bazy danych oraz zawiera porady dotyczące rozwiązywania problemów, które mogą pomóc w rozwiązywaniu problemów z wydajnością.

Najważniejszym mechanizmem monitorowania aparatu bazy danych, który należy wykorzystać, jest składnik [magazynu zapytań](sql-database-operate-query-store.md) , który rejestruje wydajność zapytań w czasie rzeczywistym i umożliwia zidentyfikowanie potencjalnych problemów z wydajnością i najlepszych odbiorcy zasobów. Dostrajanie automatyczne i zalecenia zawierają porady dotyczące zapytań dotyczących wydajności uległa pogorszeniu oraz brakujących lub zduplikowanych indeksów. Dostrajanie automatyczne w Azure SQL Database umożliwia ręczne zastosowanie skryptów, które mogą rozwiązać problemy, lub pozwolić Azure SQL Database na zastosowanie poprawki, testowanie i weryfikowanie zapewnia pewne korzyści, a także zachowuje lub przywraca zmiany w zależności od wyniku. Oprócz funkcji magazynu zapytań i dostrajania automatycznego można także monitorować wydajność obciążeń przy użyciu standardowych [widoków DMV i systemu XEvent](sql-database-monitoring-with-dmvs.md) .

Platforma Azure udostępnia wbudowane narzędzia do monitorowania i [generowania alertów](sql-database-insights-alerts-portal.md) [wydajności](sql-database-performance.md) połączone z ocenami wydajności, które umożliwiają łatwe monitorowanie stanu tysięcy baz danych. Za pomocą tych narzędzi możesz szybko ocenić wpływ skalowania w górę lub w dół na podstawie bieżących lub przewidywanych wymagań dotyczących wydajności. Ponadto usługa SQL Database może [tworzyć metryki i dzienniki diagnostyczne](sql-database-metrics-diag-logging.md), które ułatwiają monitorowanie. Usługę SQL Database można skonfigurować do przechowywania danych dotyczących użycia zasobów, pracowników i sesji oraz połączeń z jednym z następujących zasobów platformy Azure:

- **Azure Storage**: W przypadku archiwizowania ogromnych ilości danych telemetrycznych w niewielkiej cenie
- **Centrum zdarzeń platformy Azure**: W celu integracji danych telemetrycznych SQL Database z niestandardowym rozwiązaniem monitorowania lub potokami aktywnymi
- **Azure monitor dzienników**: Wbudowane rozwiązanie do monitorowania z raportowaniem, alertami i możliwościami ograniczającymi.

    ![architektura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Możliwości dostępności

W tradycyjnych środowiskach SQL Server zwykle istnieje (co najmniej) 2 maszyny skonfigurowane z dokładną (synchronicznie przechowywaną) kopią danych (przy użyciu funkcji, takich jak grupy dostępności AlwaysOn lub wystąpienia klastra trybu failover) w celu ochrony przed Niepowodzenie pojedynczej maszyny/składnika. Zapewnia to wysoką dostępność, ale nie chroni przed klęską żywiołową, która niszczy Twoje centrum danych.

Odzyskiwanie awaryjne polega na tym, że krytyczne zdarzenie będzie wystarczająco geograficznie lokalizowane, aby można było korzystać z innej maszyny/zestawu maszyn z kopią danych.  W SQL Server można używać zawsze dostępnych grup dostępności działających w trybie Async, aby uzyskać tę możliwość.  Szybkość lekkich problemów zwykle oznacza, że osoby nie chcą czekać na odczekanie przed zatwierdzeniem transakcji, dzięki czemu istnieje możliwość utraty danych w przypadku nieplanowanego przejścia w tryb failover.

Bazy danych w warstwach usługi krytycznej Premium i Business [mają już coś podobne](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) do synchronizacji grupy dostępności. Bazy danych w mniejszych warstwach usług zapewniają nadmiarowość za pośrednictwem magazynu przy użyciu [innego, ale równoważnego mechanizmu](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Istnieje logika chroniąca przed awarią pojedynczej maszyny.  Aktywna funkcja replikacji geograficznej zapewnia możliwość ochrony przed awarią, w której cały region jest niszczony.

Strefy dostępności platformy Azure to problem z wysoką dostępnością.  Próbuje ona chronić przed awarią pojedynczego budynku centrum danych w jednym regionie.  W związku z tym chcemy chronić przed utratą mocy lub sieci do budynku. W programie SQL Azure to działanie będzie działać przez umieszczenie różnych replik w różnych strefach dostępności (różne budynki, efektywnie) i w inny sposób działające tak jak wcześniej.

W rzeczywistości na platformie Azure branżowa jest wiodąca w 99,99% umowa dotycząca poziomu usług [(SLA)](https://azure.microsoft.com/support/legal/sla/), obsługiwana przez globalną sieć centrów danych zarządzanych przez firmę Microsoft, dzięki czemu aplikacja będzie działać 24/7. Platforma Azure w pełni zarządza każdą bazą danych i nie gwarantuje utraty danych i dużej wartości procentowej dostępności danych. Platforma Azure automatycznie obsługuje stosowanie poprawek, tworzenie kopii zapasowych, replikację, wykrywanie błędów, bazowe potencjalne awarie sprzętu, oprogramowania lub sieci, wdrażanie poprawek usterek, przełączanie w tryb failover, uaktualnianie bazy danych oraz inne zadania konserwacji. Dostępność w warstwie Standardowa jest osiągana przez oddzielenie warstw obliczeń i magazynu. Dostępność w warstwie Premium jest realizowana przez integrację zasobów obliczeniowych i magazynu w jednym węźle pod kątem wydajności, a następnie zaimplementowanie technologii podobnej do zawsze dostępnych grup dostępności w ramach okładek. Pełne omówienie możliwości wysokiej dostępności Azure SQL Database można znaleźć w temacie [SQL Database Availability (dostępność](sql-database-high-availability.md)). Ponadto usługa SQL Database oferuje wbudowane funkcje [zapewnienia ciągłości działalności biznesowej i globalnej skalowalności](sql-database-business-continuity.md), takie jak:

- **[Automatyczne kopie zapasowe](sql-database-automated-backups.md)** :

  SQL Database automatycznie wykonuje pełne i różnicowe kopie zapasowe dziennika transakcji baz danych Azure SQL, aby umożliwić przywracanie do dowolnego punktu w czasie. W przypadku pojedynczych baz danych i baz danych w puli można skonfigurować SQL Database do przechowywania pełnych kopii zapasowych bazy danych w usłudze Azure Storage na potrzeby długoterminowego przechowywania kopii zapasowych. W przypadku wystąpień zarządzanych można także wykonywać kopie zapasowe tylko do kopiowania na potrzeby długoterminowego przechowywania w usłudze Backup.

- **[Przywracanie do punktu w czasie](sql-database-recovery-using-backups.md)** :

  Wszystkie SQL Database opcje wdrażania obsługują odzyskiwanie do dowolnego punktu w czasie w okresie przechowywania automatycznego tworzenia kopii zapasowej dla dowolnej bazy danych Azure SQL.
- **[Aktywna replikacja geograficzna](sql-database-active-geo-replication.md)** :

  Pojedyncza baza danych i bazy danych w puli umożliwiają skonfigurowanie maksymalnie czterech pomocniczych baz danych w jednym lub globalnie rozproszonych centrach danych platformy Azure.  Na przykład, jeśli masz aplikację SaaS z bazą danych katalogu charakteryzującą się dużą liczbą równoczesnych transakcji tylko do odczytu, włącz aktywną replikację geograficzną, aby umożliwić globalne skalowanie odczytu i pozbyć się wąskich gardeł w podstawowej bazie danych, spowodowanych obciążeniami odczytu. W przypadku wystąpień zarządzanych należy użyć grup z obsługą trybu failover.
- **[Grupy autotrybu failover](sql-database-auto-failover-group.md)** :

  Wszystkie SQL Database opcje wdrażania umożliwiają korzystanie z grup trybu failover w celu zapewnienia wysokiej dostępności i równoważenia obciążenia na skalę globalną, w tym przezroczystą replikację geograficzną i przełączenie w tryb failover dużych zestawów baz danych, pul elastycznych i wystąpień zarządzanych. Grupy trybu failover umożliwiają tworzenie globalnie dystrybuowanych aplikacji SaaS z minimalnym obciążeniem administracyjnym, które opuszczają całą złożoną organizację monitorowania, routingu i trybu failover do SQL Database.
- **[Nadmiarowe bazy danych stref](sql-database-high-availability.md)** :

  SQL Database pozwala na udostępnianie krytycznych baz danych Premium lub Business lub pul elastycznych w wielu strefach dostępności. Ponieważ te bazy danych oraz elastyczne pule mają wiele nadmiarowych replik zapewniających wysoką dostępność, umieszczenie tych replik w wielu strefach dostępności zapewnia wyższą niezawodność, w tym możliwość automatycznego odzyskania po awarii w skali centrum danych bez utraty danych.

## <a name="built-in-intelligence"></a>Wbudowane narzędzie analizy

Usługa SQL Database udostępnia wbudowane narzędzie analizy, które pomaga znacznie zmniejszyć koszty działania baz danych i zarządzania nimi oraz zmaksymalizować wydajność i zabezpieczenia aplikacji. Nieprzerwanie obsługując miliony obciążeń klientów, usługa SQL Database zbiera i przetwarza ogromne ilości danych telemetrycznych, w pełni przestrzegając przy tym zasad ochrony prywatności klientów. Rozmaite algorytmy stale analizują dane telemetryczne, dzięki czemu usługa może zdobywać wiedzę i dostosować się do Twojej aplikacji. Dzięki tej analizie usługa funkcjonuje ze zwiększoną wydajnością wynikającą z uwzględnienia zaleceń dostosowanych do określonego obciążenia.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatyczne monitorowanie i dostosowywanie wydajności

Usługa SQL Database udostępnia szczegółowy wgląd w zapytania, których monitorowanie jest wymagane. Usługa SQL Database uzyskuje informacje o wzorcach Twoich baz danych i umożliwia dostosowanie schematu bazy danych do obciążenia. Usługa SQL Database udostępnia [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md) pozwalające na przeglądanie i stosowanie akcji dostrajających.

Jednak nieprzerwane monitorowanie bazy danych jest trudnym i żmudnym zadaniem, szczególnie w przypadku pracy z wieloma bazami danych. Funkcja [Intelligent Insights](sql-database-intelligent-insights.md) zapewnia to przez automatyczne monitorowanie wydajności bazy danych SQL w odpowiedniej skali oraz informowanie o problemach ze spadkiem wydajności. Identyfikuje główną przyczynę problemu i oferuje zalecenia dotyczące podnoszenia wydajności, jeśli to możliwe.

Wydajne zarządzanie olbrzymią liczbą baz danych może być niemożliwe, nawet przy zastosowaniu wszystkich dostępnych narzędzi i raportów udostępnianych przez usługę SQL Database i witrynę Azure Portal. Zamiast ręcznego monitorowania i dostrajania bazy danych można rozważyć delegowanie niektórych działań monitorowania i dostrajania do usługi SQL Database przy użyciu funkcji [automatycznego dostrajania](sql-database-automatic-tuning.md). SQL Database automatycznie stosuje zalecenia, testy i weryfikuje każdą z jej akcji dostrajania, aby zapewnić, że wydajność będzie stale ulepszana. W ten sposób usługa SQL Database automatycznie dostosowuje się do obciążenia w kontrolowany i bezpieczny sposób. Automatyczne dostrajanie oznacza, że wydajność bazy danych jest dokładnie monitorowana i porównywana przed i po wykonaniu każdej akcji dostrajania. W przypadku, gdy wydajność nie uległa poprawie, akcja dostrajania zostanie wycofana.

Dzisiaj wielu naszych partnerów uruchamiających [wielodostępne aplikacje SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md) korzystające z usługi SQL Database korzysta z automatycznego dostrajania wydajności, mając pewność, że ich aplikacje zawsze działają ze stabilną i przewidywalną wydajnością. Z ich punktu widzenia ta funkcja znacznie zmniejsza ryzyko wystąpienia w środku nocy zdarzenia związanego z wydajnością. Ponadto ponieważ niektórzy ich klienci używają także programu SQL Server, stosują oni te same zalecenia indeksowania podane przez usługę SQL Database w odniesieniu do klientów korzystających z programu SQL Server.

W usłudze [SQL Database dostępne są](sql-database-automatic-tuning.md) dwa aspekty automatycznego dostrajania:

- **Automatyczne zarządzanie indeksami**: Identyfikuje indeksy, które powinny zostać dodane w bazie danych, i indeksy, które powinny zostać usunięte.
- **Automatyczna korekta planu**: Identyfikuje plany problematyczne i rozwiązuje problemy z wydajnością planu SQL (wkrótce dostępne w SQL Server 2017).

### <a name="adaptive-query-processing"></a>Adaptacyjne przetwarzanie zapytań

Do usługi SQL Database dodajemy również rodzinę [funkcji adaptacyjnego przetwarzania zapytań](/sql/relational-databases/performance/intelligent-query-processing), takich jak przeplatane wykonywanie funkcji o wielu instrukcjach zwracających tabelę, informacja zwrotna przydziału pamięci w trybie wsadowym i adaptacyjne sprzężenia w trybie wsadowym. Każda z tych adaptacyjnych funkcji przetwarzania zapytań stosuje podobne techniki „poznaj i dostosuj” ułatwiające dalsze rozwiązywanie problemów dotyczących wydajności związanych z historycznie trudnymi do rozwiązania problemami z optymalizacją zapytań.

## <a name="advanced-security-and-compliance"></a>Zaawansowane zabezpieczenia i zgodność

Usługa SQL Database oferuje szeroką gamę [wbudowanych funkcji zabezpieczeń i zgodności](sql-database-security-overview.md), co ułatwia spełnienie przez aplikację różnych wymagań dotyczących zabezpieczeń i zgodności.

> [!IMPORTANT]
> Azure SQL Database (wszystkie opcje wdrażania) mają certyfikat dla wielu standardów zgodności. Aby uzyskać więcej informacji, zobacz [Centrum zaufania Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , w którym można znaleźć najbardziej aktualną listę SQL Database certyfikatów zgodności.

### <a name="advance-threat-protection"></a>Advanced Threat Protection

Zaawansowane zabezpieczenia danych to ujednolicony pakiet zaawansowanych funkcji zabezpieczeń SQL. Obejmuje to funkcję wykrywania i klasyfikowania danych poufnych, likwidowanie luk w zabezpieczeniach bazy danych i wykrywanie nietypowych działań, które mogą wskazywać, że baza danych jest zagrożona. Zapewnia ona pojedynczą lokalizację, w której można włączać te możliwości i zarządzać nimi.

- [Klasyfikacja & odnajdywania danych](sql-database-data-discovery-and-classification.md):

  Ta funkcja (obecnie dostępna w wersji zapoznawczej) oferuje funkcje wbudowane w Azure SQL Database na potrzeby odnajdywania, klasyfikowania i etykietowania & ochrony poufnych danych w bazach danych. Za jej pomocą można zapewniać wgląd w stan klasyfikacji bazy danych oraz śledzić dostęp do danych poufnych w bazie danych i poza jej granicami.
- [Ocena luk](sql-vulnerability-assessment.md)w zabezpieczeniach:

  Ta usługa może wykrywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. Zapewnia wgląd w stan zabezpieczeń i zapewnia kroki, które można wykonać w celu rozwiązania problemów z zabezpieczeniami oraz zwiększenia bezpieczeństwa bazy danych.
- [Wykrywanie zagrożeń](sql-database-threat-detection.md):

  Ta funkcja wykrywa nietypowe działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do bazy danych lub jej wykorzystania. Stale monitoruje Twoją bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataków polegających na wstrzyknięciu kodu SQL oraz anomalii we wzorcach dostępu do bazy danych. Alerty wykrywania zagrożeń zawierają szczegółowe informacje o podejrzanych działaniach i zalecaną akcję dotyczącą sposobu badania i łagodzenia zagrożeń.

### <a name="auditing-for-compliance-and-security"></a>Inspekcja zgodności i zabezpieczeń

[Inspekcja](sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

### <a name="data-encryption"></a>Szyfrowanie danych

SQL Database zabezpiecza dane, zapewniając szyfrowanie danych w ruchu przy użyciu protokołu [Transport Layer Security](https://support.microsoft.com/kb/3135244), w przypadku danych przechowywanych przy użyciu [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)oraz danych używanych z funkcją [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym

Usługa SQL Database umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft dzięki funkcji [integracji usługi Azure Active Directory](sql-database-aad-authentication.md). Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Usługa Azure Active Directory obsługuje [uwierzytelnianie wieloskładnikowe](sql-database-ssms-mfa-authentication.md) (MFA, Multi-Factor Authentication) w celu zwiększenia bezpieczeństwa danych i aplikacji, korzystając z procesu jednokrotnego logowania.

### <a name="compliance-certification"></a>Certyfikacja zgodności

Usługa SQL Database jest poddawana regularnym inspekcjom i ma certyfikat kilku standardów zgodności. Aby uzyskać więcej informacji, zobacz [Centrum zaufania Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , w którym można znaleźć najbardziej aktualną listę SQL Database certyfikatów zgodności.

## <a name="easy-to-use-tools"></a>Łatwe w użyciu narzędzia

Dzięki usłudze SQL Database tworzenie i konserwowanie aplikacji jest łatwiejsze i bardziej produktywne. Usługa SQL Database pozwala Ci skoncentrować się na tym, co robisz najlepiej: tworzeniu wspaniałych aplikacji. W usłudze SQL Database możesz zarządzać i projektować, korzystając z narzędzi i umiejętności, które już masz.

- **[Azure Portal](https://portal.azure.com/)** :

  Aplikacja oparta na sieci Web do zarządzania wszystkimi usługami platformy Azure
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)** :

  Bezpłatna, do pobrania aplikacja kliencka do zarządzania dowolną infrastrukturą SQL, od SQL Server do SQL Database
- **[Narzędzia danych SQL Server w programie Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)** :

  Bezpłatna, do pobrania aplikacja kliencka służąca do tworzenia SQL Server relacyjnych baz danych, baz danych SQL Azure, pakietów usług Integration Services, Analysis Services modeli danych i raportów usług Reporting Services.
- **[Visual Studio Code](https://code.visualstudio.com/docs)** :

  Bezpłatny, dostępny do pobrania Edytor kodu dla systemów Windows, macOS i Linux obsługujący rozszerzenia, w tym [rozszerzenie MSSQL](https://aka.ms/mssql-marketplace) służący do wykonywania zapytań dotyczących Microsoft SQL Server, Azure SQL Database i SQL Data Warehouse.

Usługa SQL Database obsługuje tworzenie aplikacji za pomocą narzędzi Python, Java, Node.js, PHP, Ruby i .NET w systemach MacOS, Linux i Windows. Usługa SQL Database obsługuje te same [biblioteki połączeń](sql-database-libraries.md) co program SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions-faq"></a>SQL Database często zadawane pytania

### <a name="what-is-the-current-version-of-sql-database"></a>Co to jest bieżąca wersja SQL Database

Bieżąca wersja SQL Database to V12. Wersja v11 została wycofana.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Czy mogę kontrolować, kiedy występuje poprawka przestoju

Nie. Skutki zastosowania poprawek zwykle nie są zauważalne, jeśli w aplikacji [](sql-database-develop-overview.md#resiliency) jest stosowana logika ponawiania. Aby uzyskać więcej informacji o sposobach przygotowania do planowanych zdarzeń konserwacji w usłudze Azure SQL Database, zobacz [Planowanie zdarzeń konserwacji platformy Azure w Azure SQL Database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Pytania Korzyść użycia hybrydowego platformy Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Czy istnieją podwójne prawa do używania z Korzyść użycia hybrydowego platformy Azure dla SQL Server

Masz 180 dni od podwójnego prawa do używania licencji, aby zapewnić bezproblemowe działanie migracji. Po upływie tego 180ego okresu licencja SQL Server może być używana tylko w chmurze w usłudze SQL Database i nie ma praw do użycia lokalnie i w chmurze.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Jak Korzyść użycia hybrydowego platformy Azure SQL Server różni się od przenoszenia licencji

Obecnie firma Microsoft oferuje korzyści z zakresu mobilności licencji do SQL Server klientom z programem Software Assurance, który umożliwia ponowne przypisanie licencji do współużytkowanych serwerów innych firm. Ta korzyść może być używana w przypadku usług Azure IaaS i AWS EC2.
Korzyść użycia hybrydowego platformy Azure SQL Server różni się od przenoszenia licencji w dwóch kluczowych obszarach:

- Zapewnia ekonomiczne korzyści związane z przenoszenium wysoce zwirtualizowanych obciążeń na platformę Azure. Klienci korzystający z usług SQL EE mogą uzyskać 4 rdzenie na platformie Azure w ramach jednostki SKU Ogólnego przeznaczenia dla każdego rdzenia lokalnego dla wysoce zwirtualizowanych aplikacji. Mobilność licencji nie pozwala na przenoszenie zwirtualizowanych obciążeń do chmury z uwzględnieniem specjalnych kosztów.
- Zapewnia miejsce docelowe PaaS na platformie Azure (SQL Database wystąpienia zarządzanego), które jest wysoce zgodne z SQL Server lokalnymi

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jakie są określone prawa Korzyść użycia hybrydowego platformy Azure dla SQL Server

SQL Database klienci będą mieć następujące prawa skojarzone z Korzyść użycia hybrydowego platformy Azure dla SQL Server:

|Wpływ na licencję|Co Korzyść użycia hybrydowego platformy Azure na SQL Server uzyskać użytkownika?|
|---|---|
|Klienci z wersjami SQL Server Enterprise Core z pakietem SA|<li>Może być płacona stawka podstawowa dla Ogólnego przeznaczenia lub Krytyczne dla działania firmy jednostki SKU</li><br><li>1 rdzeń lokalnego = 4 rdzenie w jednostce SKU Ogólnego przeznaczenia</li><br><li>1 rdzeń lokalnego = 1 rdzeń w jednostce SKU Krytyczne dla działania firmy</li>|
|Klienci z wersjami SQL Server Standard Core z pakietem SA|<li>Można uiścić stawkę bazową tylko dla Ogólnego przeznaczenia jednostki SKU</li><br><li>1 rdzeń lokalnego = 1 rdzeń w jednostce SKU Ogólnego przeznaczenia</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Kontakt z zespołem inżynierów programu SQL Server

- [Serwer Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Pytania dotyczące administrowania bazą danych
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Zadawaj pytania dotyczące programowania
- [Fora MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Zadawaj pytania techniczne
- [Opinie](https://aka.ms/sqlfeedback): Raportowanie usterek i funkcji żądania
- [Reddit](https://www.reddit.com/r/SQLServer/): Omówienie SQL Server

## <a name="next-steps"></a>Następne kroki

- Zapoznać się z [cennikiem](https://azure.microsoft.com/pricing/details/sql-database/) zawierającym porównania i kalkulatory kosztów dla pojedynczej bazy danych i pul elastycznych.
- Zobacz te przewodniki Szybki start, aby rozpocząć pracę:

  - [Tworzenia bazy danych SQL w witrynie Azure Portal](sql-database-single-database-get-started.md)  
  - [Tworzenia bazy danych SQL za pomocą interfejsu wiersza polecenia platformy Azure](sql-database-get-started-cli.md)
  - [Tworzenie bazy danych SQL za pomocą programu PowerShell](sql-database-get-started-powershell.md)

- Aby uzyskać zestaw przykładów interfejsu wiersza polecenia platformy Azure i programu PowerShell, zobacz:
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi SQL Database](sql-database-cli-samples.md)
  - [Przykłady programu Azure PowerShell dla usługi SQL Database](sql-database-powershell-samples.md)

 - Aby uzyskać informacje o nowych możliwościach w miarę ich ogłaszania, zobacz 
   - **[Zaplanuj platformę Azure dla SQL Database](https://azure.microsoft.com/roadmap/?category=databases)** — miejscu, aby dowiedzieć się, co nowego i co dalej.
  - **[Blog Azure SQL Database](https://azure.microsoft.com/blog/topics/database)** — miejsce, w którym SQL Server członków zespołu ds. produktów o SQL Database wiadomościach i funkcjach.

