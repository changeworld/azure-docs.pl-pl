---
title: Co to jest usługa Azure SQL Database? | Microsoft Docs
description: 'Wprowadzenie do usługi SQL Database: szczegóły techniczne i możliwości systemu zarządzania relacyjnymi bazami danych (RDBMS) w chmurze firmy Microsoft.'
keywords: wprowadzenie do usługi sql, co to jest sql database
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
ms.date: 01/25/2019
ms.openlocfilehash: 6b4bc5a75f49287ad0425a7fe94f099204b73e44
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469553"
---
# <a name="the-azure-sql-database-service"></a>Usługa Azure SQL Database

Usługa SQL Database jest zarządzaną usługą relacyjnej bazy danych ogólnego przeznaczenia na platformie Microsoft Azure, obsługującą struktury takie jak dane relacyjne, JSON, dane przestrzenne i XML. Usługa SQL Database zapewnia dynamicznie skalowalną wydajność w ramach dwóch różnych modeli zakupów: [model zakupów w oparciu o rdzeń wirtualny](sql-database-service-tiers-vcore.md) i [model zakupów w oparciu o jednostki DTU](sql-database-service-tiers-dtu.md). Usługa SQL Database udostępnia również opcje, takie jak [indeksy magazynu kolumn](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview), używane w skomplikowanych analizach i raportowaniu, oraz [przetwarzanie OLTP danych w pamięci](sql-database-in-memory.md) na potrzeby ekstremalnego przetwarzania transakcyjnego. Firma Microsoft bezproblemowo obsługuje wprowadzanie poprawek i aktualizowanie bazy kodu SQL i ukrywa procesy zarządzania podstawową infrastrukturą.

Usługa Azure SQL Database udostępnia następujące opcje wdrażania na potrzeby bazy danych SQL platformy Azure:

- Jako pojedynczą bazę danych za pomocą swój własny zestaw zasobów zarządzanych za pomocą serwera usługi SQL Database
- Jako bazy danych w puli w [puli elastycznej](sql-database-elastic-pool.md) z współdzielonym zestawem zasobów zarządzanych za pomocą serwera usługi SQL Database
- W ramach kolekcji baz danych, nazywane [wystąpienia zarządzanego](sql-database-managed-instance.md) zawierający system i baz danych użytkownika i udostępnianie zestaw zasobów

Poniższa ilustracja przedstawia te opcje wdrażania:

![deployment-options](./media/sql-database-technical-overview/deployment-options.png)

Usługa SQL Database współdzieli swój kod podstawowy z [aparatem bazy danych programu Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). Zgodnie ze strategią firmy Microsoft skupiającej się na chmurze najnowsze funkcjonalności programu SQL Server są wydawane najpierw w usłudze SQL Database, a dopiero później w samym programie SQL Server. Podejście to umożliwia użytkownikom dostęp do najnowszych możliwości programu SQL Server bez potrzeby wdrażania poprawek lub wykonywania uaktualnień — a na dodatek funkcje te są już przetestowane w milionach baz danych. Aby uzyskać informacje dotyczące nowych możliwości w miarę ich publikowania, zobacz:

- **[Plan usługi Azure SQL Database](https://azure.microsoft.com/roadmap/?category=databases)**:

  Miejsce, aby dowiedzieć się, co nowego i co będzie dalej.

- **[Blog usługi Azure SQL Database](https://azure.microsoft.com/blog/topics/database)**:

  Miejsce, gdzie blogu elementów członkowskich, temat wiadomości bazy danych SQL i funkcje zespołu produktu SQL Server.

> [!IMPORTANT]
> Aby zrozumieć różnice między funkcjami usługi SQL Database i programu SQL Server, zobacz [SQL features (Funkcje języka SQL)](sql-database-features.md).

SQL Database oferuje przewidywalną wydajność z wielu typów zasobów, warstwy usług i rozmiarów wystąpień obliczeniowych, która zapewnia dynamiczną skalowalność nie przestojów, wbudowany mechanizm inteligentnej optymalizacji, globalną skalowalność i dostępność i zaawansowane zabezpieczenia Opcje — wszystko przy bliskich zeru nakładach na administrację. Te możliwości pozwalają Ci skoncentrować się na szybkim tworzeniu aplikacji i skracaniu czasu wejścia na rynek, a nie na poświęcaniu cennego czasu i cennych zasobów na zarządzanie maszynami wirtualnymi i infrastrukturą. Usługa SQL Database jest obecnie dostępna w 38 centrach danych na całym świecie z coraz większą liczbą centrów danych regularnie dołączanych, dzięki czemu możesz uruchomić swoją bazę danych w centrum danych położonym w pobliżu.

## <a name="scalable-performance-and-pools"></a>Skalowalna wydajność i pule

Usługa SQL Database każda baza danych jest odizolowana od innych i przenośna, z własną warstwę usług, w ramach [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) lub [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) i gwarancji obliczenia rozmiaru. SQL Database zapewnia różnych rozmiarów wystąpień obliczeniowych dla różnych potrzeb oraz umożliwia włączanie baz danych do puli w celu optymalnego wykorzystania zasobów i oszczędzać pieniądze.

- Za pomocą [wystąpienie zarządzane usługi SQL Database](sql-database-managed-instance.md), każde wystąpienie jest odizolowane od innych wystąpień z gwarantowanymi zasobami. Aby uzyskać więcej informacji, zobacz [Wystąpienie zarządzane usługi SQL Database](sql-database-managed-instance.md).
- Za pomocą [warstwy usługi w Hiperskali](sql-database-service-tier-hyperscale.md) (wersja zapoznawcza) w zakup modelu rdzeni wirtualnych, można skalować do 100 TB przy użyciu szybkich kopii zapasowej i przywracanie możliwości.

### <a name="adjust-performance-and-scale-without-downtime"></a>Dostosowanie wydajności i skalowanie bez przestojów

Usługa SQL Database oferuje [model zakupu w oparciu o jednostki DTU](sql-database-service-tiers-dtu.md) lub [model zakupu w oparciu o rdzeń wirtualny](sql-database-service-tiers-vcore.md).

- Model zakupu opartego na jednostkach DTU oferuje połączenie wystąpień obliczeniowych, pamięci i zasobów we/wy w trzech warstwach usług do obsługi niewielkich obciążeń bazy danych: Basic, Standard i Premium. Obliczenia rozmiarów w każdej warstwie udostępniają różne kombinacje tych zasobów, do których można dodawać kolejne zasoby magazynu.
- Model zakupu w oparciu o rdzeń wirtualny pozwala wybrać liczbę rdzeni wirtualnych, ilość pamięci oraz wielkość i szybkość magazynu.

Możesz utworzyć pierwszą aplikację na małej, pojedynczej bazy danych na niskim poziomie kosztów miesięcznie w warstwie ogólnego przeznaczenia i następnie zmienić warstwę usługi ręcznie lub programowo w dowolnym momencie do warstwy usług krytycznych biznesowych do potrzeb rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów oraz płacenie tylko za potrzebne zasoby i tylko gdy są używane.

> [!IMPORTANT]
> [Warstwy usługi w Hiperskali](sql-database-service-tier-hyperscale.md) jest obecnie dostępna w publicznej wersji zapoznawczej. Nie zaleca się jeszcze uruchamianie dowolnego obciążenia produkcyjnego w bazach danych na dużą skalę. Nie można zaktualizować bazy danych na dużą skalę do innych warstw usług. W celu badania firma Microsoft zaleca, Utwórz kopię bieżącej bazy danych i zaktualizowania kopii do warstwy usług na dużą skalę.

Dynamiczna skalowalność różni się od skalowania automatycznego. O skalowaniu automatycznym mówimy, gdy usługa jest skalowana automatycznie na podstawie kryteriów, natomiast dynamiczna skalowalność uwzględnia skalowanie ręczne bez przestojów. Pojedyncza baza danych obsługuje ręcznego dynamiczną skalowalność, ale nie automatycznego skalowania. Aby zapewnić środowisko bardziej *zautomatyzowane*, rozważ użycie elastycznych pul baz danych, które umożliwiają bazom danych udostępnianie zasobów w puli w zależności od potrzeb. Istnieją jednak skrypty, które mogą pomóc zautomatyzować skalowalność dla pojedynczej bazy danych. Aby uzyskać przykład, zobacz [Użyj programu PowerShell do monitorowania i skalowania pojedynczej bazy danych](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Pule elastyczne umożliwiające zmaksymalizowanie wykorzystania zasobów

W przypadku wielu firm i aplikacji możliwość tworzenia pojedynczych baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza gdy wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. [Pule elastyczne](sql-database-elastic-pool.md) zostały zaprojektowane, aby rozwiązać ten problem. Koncepcja jest prosta. Przydzielasz zasoby wydajności do puli, a nie poszczególnych baz danych i płać za zasoby zbiorczą wydajnością puli, a nie dla wydajności pojedynczej bazy danych.

   ![pule elastyczne](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Pule elastyczne nie wymagają koncentrowania się na skalowaniu wydajności bazy danych w górę i w dół w miarę zmian zapotrzebowania na zasoby. Bazy danych w puli używają zasobów wydajności puli elastycznej w miarę potrzeb. Bazy danych w puli zużywają zasoby, ale nie przekraczają limitów puli, więc koszt pozostaje przewidywalny nawet wtedy, gdy nie można przewidzieć użycia poszczególnych baz danych. Ponadto możliwe jest [dodawanie i usuwanie baz danych w puli](sql-database-elastic-pool-manage-portal.md) i skalowanie aplikacji od kilku do tysięcy baz danych — wszystko w ramach kontrolowanego budżetu. Możesz również kontrolować minimum i maksimum zasobów dostępnych dla baz danych w puli w celu zapewnienia, że żadna baza danych w puli nie używa wszystkich zasobów i jednocześnie każda baza danych w puli ma gwarantowaną minimalną ilość zasobów. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

Skrypty mogą ułatwić monitorowanie i skalowanie elastycznych pul baz danych. Na przykład zobacz temat [Monitorowanie i skalowanie elastycznej puli SQL w usłudze Azure SQL Database za pomocą programu PowerShell](scripts/sql-database-monitor-and-scale-pool-powershell.md)

> [!IMPORTANT]
> Wystąpienie zarządzane usługi SQL Database nie obsługuje pul elastycznych.

### <a name="blend-single-databases-with-pooled-databases"></a>Mieszaj pojedyncze bazy danych z bazami danych w puli

W obu przypadkach — pojedyncza baza danych i pule elastyczne — nie ma ograniczenia do jednego rozwiązania. Możesz łączyć pojedyncze bazy danych z pulami elastycznymi i zmieniać warstwy usług pojedynczych baz danych i pul elastycznych, szybko i łatwo dostosować do swojej sytuacji. Korzystając z siły i zasięgu platformy Azure, możesz łączyć inne usługi Azure z usługą SQL Database i dopasowywać je do niej, aby zaspokoić potrzeby unikatowego projektu aplikacji, zwiększyć efektywność kosztową i poprawić wykorzystanie zasobów oraz otworzyć się na nowe możliwości biznesowe.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Możliwości rozbudowanego monitorowania i zgłaszania alertów

Jednak jak porównać względną wydajność pojedynczych baz danych i pul elastycznych? Skąd wiadomo, kiedy kliknąć, aby zatrzymać regulowanie w górę i w dół we właściwym miejscu? Narzędzi [wbudowanego monitorowania wydajności](sql-database-performance.md) i [alertów](sql-database-insights-alerts-portal.md) możesz używać w połączeniu z ocenami wydajności. Za pomocą tych narzędzi możesz szybko ocenić wpływ skalowania w górę lub w dół na podstawie bieżących lub przewidywanych wymagań dotyczących wydajności. Zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) Aby uzyskać szczegółowe informacje.

Ponadto usługa SQL Database może [tworzyć metryki i dzienniki diagnostyczne](sql-database-metrics-diag-logging.md), które ułatwiają monitorowanie. Usługę SQL Database można skonfigurować do przechowywania danych dotyczących użycia zasobów, pracowników i sesji oraz połączeń z jednym z następujących zasobów platformy Azure:

- **Azure Storage**: W celu archiwizowania ogromnych ilości danych telemetrycznych za niewielką cenę
- **Centrum zdarzeń platformy Azure**: Do integracji danych telemetrycznych usługi SQL Database z niestandardowym rozwiązaniem monitorowania lub potokami
- **Usługi Azure Log Analytics**: Aby uzyskać wbudowanego rozwiązania monitorowania obejmującego raportowania, zgłaszania alertów i łagodzenia możliwości.

    ![architektura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Możliwości dostępności

Umowa dotycząca poziomu usług [(SLA)](https://azure.microsoft.com/support/legal/sla/) o czołowej w branży dostępności 99,99% dla platformy Azure, która jest obsługiwana przez globalną sieć centrów danych zarządzanych przez firmę Microsoft, pomaga zapewnić działanie aplikacji przez 24 godziny na dobę, 7 dni w tygodniu. Platforma Azure w pełni zarządza każdą usługą Azure SQL Database i gwarantuje, że nie nastąpi żadna utrata danych oraz że dane będą dostępne przez bardzo wysoki odsetek czasu. Platforma Azure automatycznie obsługuje stosowanie poprawek, tworzenie kopii zapasowych, replikację, wykrywanie błędów, bazowe potencjalne awarie sprzętu, oprogramowania lub sieci, wdrażanie poprawek usterek, przełączanie w tryb failover, uaktualnianie bazy danych oraz inne zadania konserwacji. Dostępność w warstwie Standardowa jest osiągana przez oddzielenie warstw obliczeń i magazynu. Premium dostępność jest osiągana integracji zasobów obliczeniowych i magazynu na jednym węźle wydajności, a następnie wdrażania technologii podobne do zawsze włączonych grup dostępności w sposób niewidoczny. Aby uzyskać pełne omówienie możliwości wysokiej dostępności usługi Azure SQL Database, zobacz [dostępności bazy danych SQL](sql-database-high-availability.md). Ponadto usługa SQL Database oferuje wbudowane funkcje [zapewnienia ciągłości działalności biznesowej i globalnej skalowalności](sql-database-business-continuity.md), takie jak:

- **[Automatyczne kopie zapasowe](sql-database-automated-backups.md)**:

  SQL Database automatycznie wykonuje pełne, różnicowe kopie zapasowe oraz kopie zapasowe dziennika transakcji.
- **[W momencie przywracania](sql-database-recovery-using-backups.md)**:

  Usługa SQL Database obsługuje odzyskiwanie do dowolnego punktu w czasie w okresie przechowywania automatycznych kopii zapasowych.
- **[Aktywna replikacja geograficzna](sql-database-active-geo-replication.md)**:

  Bazy danych SQL pozwala na skonfigurowanie maksymalnie czterech odczytu pomocniczych baz danych w jednym centrów danych platformy Azure w tej samej lub w globalnie rozproszonych.  Na przykład, jeśli masz aplikację SaaS z bazą danych katalogu charakteryzującą się dużą liczbą równoczesnych transakcji tylko do odczytu, włącz aktywną replikację geograficzną, aby umożliwić globalne skalowanie odczytu i pozbyć się wąskich gardeł w podstawowej bazie danych, spowodowanych obciążeniami odczytu.
- **[Automatyczny tryb failover grupy](sql-database-auto-failover-group.md)**:

  SQL Database pozwala włączyć wysoką dostępność i równoważenie obciążeń w skali globalnej, w tym przezroczystą replikację geograficzną i tryb failover dla dużych zestawów baz danych i pul elastycznych. Grupy trybu failover i aktywna replikacja geograficzna umożliwiają tworzenie globalnie rozproszonych aplikacji SaaS przy minimalnym narzucie prac administracyjnych, pozostawiając po stronie usługi SQL Database całość złożonego monitorowania, routing i organizację trybu failover.
- **[Strefowo nadmiarowe bazy danych](sql-database-high-availability.md)**:

  Usługa SQL Database umożliwia można aprowizować — wersja Premium lub krytyczne dla działania firmy baz danych lub pule elastyczne w wielu strefach dostępności. Ponieważ te bazy danych oraz elastyczne pule mają wiele nadmiarowych replik zapewniających wysoką dostępność, umieszczenie tych replik w wielu strefach dostępności zapewnia wyższą niezawodność, w tym możliwość automatycznego odzyskania po awarii w skali centrum danych bez utraty danych.  

## <a name="built-in-intelligence"></a>Wbudowane narzędzie analizy

Usługa SQL Database udostępnia wbudowane narzędzie analizy, które pomaga znacznie zmniejszyć koszty działania baz danych i zarządzania nimi oraz zmaksymalizować wydajność i zabezpieczenia aplikacji. Nieprzerwanie obsługując miliony obciążeń klientów, usługa SQL Database zbiera i przetwarza ogromne ilości danych telemetrycznych, w pełni przestrzegając przy tym zasad ochrony prywatności klientów. Rozmaite algorytmy stale analizują dane telemetryczne, dzięki czemu usługa może zdobywać wiedzę i dostosować się do Twojej aplikacji. Dzięki tej analizie usługa funkcjonuje ze zwiększoną wydajnością wynikającą z uwzględnienia zaleceń dostosowanych do określonego obciążenia.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatyczne monitorowanie i dostosowywanie wydajności

Usługa SQL Database udostępnia szczegółowy wgląd w zapytania, których monitorowanie jest wymagane. Usługa SQL Database uzyskuje informacje o wzorcach Twoich baz danych i umożliwia dostosowanie schematu bazy danych do obciążenia. Usługa SQL Database udostępnia [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md) pozwalające na przeglądanie i stosowanie akcji dostrajających.

Jednak nieprzerwane monitorowanie bazy danych jest trudnym i żmudnym zadaniem, szczególnie w przypadku pracy z wieloma bazami danych. Funkcja [Intelligent Insights](sql-database-intelligent-insights.md) zapewnia to przez automatyczne monitorowanie wydajności bazy danych SQL w odpowiedniej skali oraz informowanie o problemach ze spadkiem wydajności. Identyfikuje główną przyczynę problemu i oferuje zalecenia dotyczące podnoszenia wydajności, jeśli to możliwe.

Wydajne zarządzanie olbrzymią liczbą baz danych może być niemożliwe, nawet przy zastosowaniu wszystkich dostępnych narzędzi i raportów udostępnianych przez usługę SQL Database i witrynę Azure Portal. Zamiast ręcznego monitorowania i dostrajania bazy danych można rozważyć delegowanie niektórych działań monitorowania i dostrajania do usługi SQL Database przy użyciu funkcji [automatycznego dostrajania](sql-database-automatic-tuning.md). Usługa SQL Database automatycznie stosuje zalecenia, testy i sprawdza każdą z akcji dostrajania, upewniając się, że następuje poprawa wydajności. W ten sposób usługa SQL Database automatycznie dostosowuje się do obciążenia w kontrolowany i bezpieczny sposób. Automatyczne dostrajanie oznacza, że wydajność bazy danych jest dokładnie monitorowana i porównywana przed i po wykonaniu każdej akcji dostrajania. W przypadku, gdy wydajność nie uległa poprawie, akcja dostrajania zostanie wycofana.

Dzisiaj wielu naszych partnerów uruchamiających [wielodostępne aplikacje SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md) korzystające z usługi SQL Database korzysta z automatycznego dostrajania wydajności, mając pewność, że ich aplikacje zawsze działają ze stabilną i przewidywalną wydajnością. Z ich punktu widzenia ta funkcja znacznie zmniejsza ryzyko wystąpienia w środku nocy zdarzenia związanego z wydajnością. Ponadto ponieważ niektórzy ich klienci używają także programu SQL Server, stosują oni te same zalecenia indeksowania podane przez usługę SQL Database w odniesieniu do klientów korzystających z programu SQL Server.

W usłudze [SQL Database dostępne są](sql-database-automatic-tuning.md) dwa aspekty automatycznego dostrajania:

- **Automatyczne Zarządzanie indeksami**: Identyfikuje indeksy, które powinny zostać dodane w bazie danych i indeksów, które powinny zostać usunięte.
- **Automatyczna korekta planu**: Identyfikuje plany, problemy i rozwiązuje problemy z wydajnością planów SQL (dostępne wkrótce, dostępnych w programie SQL Server 2017).

### <a name="adaptive-query-processing"></a>Adaptacyjne przetwarzanie zapytań

Do usługi SQL Database dodajemy również rodzinę [funkcji adaptacyjnego przetwarzania zapytań](/sql/relational-databases/performance/adaptive-query-processing), takich jak przeplatane wykonywanie funkcji o wielu instrukcjach zwracających tabelę, informacja zwrotna przydziału pamięci w trybie wsadowym i adaptacyjne sprzężenia w trybie wsadowym. Każda z tych adaptacyjnych funkcji przetwarzania zapytań stosuje podobne techniki „poznaj i dostosuj” ułatwiające dalsze rozwiązywanie problemów dotyczących wydajności związanych z historycznie trudnymi do rozwiązania problemami z optymalizacją zapytań.

## <a name="advanced-security-and-compliance"></a>Zaawansowane zabezpieczenia i zgodność

Usługa SQL Database oferuje szeroką gamę [wbudowanych funkcji zabezpieczeń i zgodności](sql-database-security-overview.md), co ułatwia spełnienie przez aplikację różnych wymagań dotyczących zabezpieczeń i zgodności.

### <a name="advance-threat-protection"></a>Advanced Threat Protection

Zaawansowane zabezpieczenia danych SQL jest ujednoliconego pakietu dla zaawansowane funkcje zabezpieczeń programu SQL. Obejmuje to funkcję wykrywania i klasyfikowania danych poufnych, likwidowanie luk w zabezpieczeniach bazy danych i wykrywanie nietypowych działań, które mogą wskazywać, że baza danych jest zagrożona. Zapewnia ona pojedynczą lokalizację, w której można włączać te możliwości i zarządzać nimi.

- [Odnajdywanie i klasyfikacja danych](sql-database-data-discovery-and-classification.md):

  Ta funkcja (obecnie w wersji zapoznawczej) zapewnia funkcje wbudowane w usłudze Azure SQL Database na potrzeby odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Za jej pomocą można zapewniać wgląd w stan klasyfikacji bazy danych oraz śledzić dostęp do danych poufnych w bazie danych i poza jej granicami.
- [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md):

  Ta usługa może odnaleźć, śledzenie i ułatwić korygowanie potencjalnych luk w bazie danych. Zapewnia wgląd w stan zabezpieczeń i zapewnia kroki, które można wykonać w celu rozwiązania problemów z zabezpieczeniami oraz zwiększenia bezpieczeństwa bazy danych.
- [Wykrywanie zagrożeń](sql-database-threat-detection.md):

  Ta funkcja wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania bazy danych. Stale monitoruje Twoją bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataków polegających na wstrzyknięciu kodu SQL oraz anomalii we wzorcach dostępu do bazy danych. Alerty wykrywania zagrożeń zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.

### <a name="auditing-for-compliance-and-security"></a>Inspekcja zgodności i zabezpieczeń

[Inspekcja bazy danych SQL](sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie magazynu Azure. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

### <a name="data-encryption"></a>Szyfrowanie danych

Usługa SQL Database zabezpiecza dane, szyfrując przesyłane dane za pomocą protokołu [Transport Layer Security](https://support.microsoft.com/kb/3135244), dane magazynowane za pomocą funkcji [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), a używane dane za pomocą funkcji [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym

Usługa SQL Database umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft dzięki funkcji [integracji usługi Azure Active Directory](sql-database-aad-authentication.md). Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Usługa Azure Active Directory obsługuje [uwierzytelnianie wieloskładnikowe](sql-database-ssms-mfa-authentication.md) (MFA, Multi-Factor Authentication) w celu zwiększenia bezpieczeństwa danych i aplikacji, korzystając z procesu jednokrotnego logowania.

### <a name="compliance-certification"></a>Certyfikacja zgodności

Usługa SQL Database jest poddawana regularnym inspekcjom i ma certyfikat kilku standardów zgodności. Aby uzyskać więcej informacji, zobacz witrynę [Centrum zaufania Microsoft Azure](https://azure.microsoft.com/support/trust-center/), w której można znaleźć aktualną listę [certyfikatów zgodności usługi SQL Database](https://azure.microsoft.com/support/trust-center/services/).

## <a name="easy-to-use-tools"></a>Łatwe w użyciu narzędzia

Dzięki usłudze SQL Database tworzenie i konserwowanie aplikacji jest łatwiejsze i bardziej produktywne. Usługa SQL Database pozwala Ci skoncentrować się na tym, co robisz najlepiej: tworzeniu wspaniałych aplikacji. W usłudze SQL Database możesz zarządzać i projektować, korzystając z narzędzi i umiejętności, które już masz.

- **[Witryna Azure portal](https://portal.azure.com/)**:

  Aplikacja sieci web do zarządzania wszystkich usług platformy Azure
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)**:

  Bezpłatna, dostępna do pobrania aplikacja kliencka do zarządzania dowolną infrastrukturą SQL — od programu SQL Server do usługi SQL Database
- **[SQL Server Data Tools in Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)**:

  Bezpłatna, dostępna do pobrania aplikacja kliencka do projektowania relacyjnych baz danych programu SQL Server, bazy danych Azure SQL, pakiety usług Integration Services, modele danych usług Analysis Services i raporty usług Reporting Services.
- **[Visual Studio Code](https://code.visualstudio.com/docs)**:

  Edytor kodu bezpłatny, dostępny do pobrania, typu open source dla Windows, macOS i Linux obsługujący rozszerzenia, w tym [rozszerzenie mssql](https://aka.ms/mssql-marketplace) do wykonywania zapytań programu Microsoft SQL Server, usługi Azure SQL Database i SQL Data Warehouse.

Usługa SQL Database obsługuje tworzenie aplikacji za pomocą narzędzi Python, Java, Node.js, PHP, Ruby i .NET w systemach MacOS, Linux i Windows. Usługa SQL Database obsługuje te same [biblioteki połączeń](sql-database-libraries.md) co program SQL Server.

## <a name="engage-with-the-sql-server-engineering-team"></a>Kontakt z zespołem inżynierów programu SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Zadawaj pytania dotyczące administrowania bazy danych
- [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-server): Zadawaj pytania dotyczące programowania
- [Fora MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Zadawaj pytania techniczne
- [Opinie](https://aka.ms/sqlfeedback): Tworzenie raportów usterek i poprosić o dodanie funkcji
- [Reddit](https://www.reddit.com/r/SQLServer/): Omówienie programu SQL Server

## <a name="next-steps"></a>Kolejne kroki

- Zapoznać się z [cennikiem](https://azure.microsoft.com/pricing/details/sql-database/) zawierającym porównania i kalkulatory kosztów dla pojedynczej bazy danych i pul elastycznych.
- Zobacz te przewodniki Szybki start, aby rozpocząć pracę:

  - [Tworzenia bazy danych SQL w witrynie Azure Portal](sql-database-get-started-portal.md)  
  - [Tworzenia bazy danych SQL za pomocą interfejsu wiersza polecenia platformy Azure](sql-database-get-started-cli.md)
  - [Tworzenie bazy danych SQL za pomocą programu PowerShell](sql-database-get-started-powershell.md)

- Aby uzyskać zestaw przykładów interfejsu wiersza polecenia platformy Azure i programu PowerShell, zobacz:
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi SQL Database](sql-database-cli-samples.md)
  - [Przykłady programu Azure PowerShell dla usługi SQL Database](sql-database-powershell-samples.md)
