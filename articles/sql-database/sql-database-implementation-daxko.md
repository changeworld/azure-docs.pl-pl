---
title: Analiza przypadku Azure - Daxko/CSI bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o Daxko/CSI używaniu bazy danych SQL w celu przyspieszenia cyklu programowanie i aby zwiększyć jego działu pomocy technicznej i wydajność
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 3779b32aa1397b2ca0e05e2627241c0bfb7a8622
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI używane Azure w celu przyspieszenia cyklu programowanie i zwiększyć jej działu pomocy technicznej i wydajność
![Daxko/CSI Logo](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Daxko/CSI oprogramowania, które muszą ponieść żądanie: jej klientów centrów przydatności i odtwarzania został rośnie szybko, dzięki użyciu Powodzenie rozwiązanie kompleksowe oprogramowania korporacyjnego, ale zatrzymując dla tego klienta rosnących potrzeb infrastruktury IT podstawowy został testowania firmy personel działu informatycznego. Firma została coraz bardziej ograniczone przez wzrost obciążenia operacji, szczególnie w przypadku zarządzania wzrostu bazy danych. Gorsze obciążenie tej operacji została Wycinanie do zasoby projektowe dla nowych inicjatyw, takie jak nowe funkcje mobilności oprogramowania firmy.

Zgodnie z Molina Dominik, dyrektor rozwoju produktów w Daxko/CSI dostarczana przez platformę Azure CSI oprogramowania z modelem platforma jako usługa (PaaS), ona wymagana w celu uproszczenia zarządzania bazą danych, zwiększenia skalowalności i zwolnić zasoby, aby skoncentrować się na oprogramowania zamiast ops. "Baza danych SQL azure to doskonałe rozwiązanie firmie Microsoft. Nie ma martwić się o zachowaniu programu SQL Server, klastra pracy awaryjnej i wszystkie wymagane przez infrastrukturę programu to idealne rozwiązanie w firmie Microsoft."

Ponieważ migracji do usługi Azure, CSI oprogramowania musi Operatorzy tylko dwa Zarządzanie ponad 600 klienta baz danych. Firma korzysta z puli elastycznej bazy danych SQL Azure przenoszenia baz danych klienta na podstawie rozmiaru i wymagają.

Kontynuuje Molina "naszym klientom mieli świadomość zmiany natychmiast. Przed pule elastyczne czasami miało limity czasu i inne problemy w okresach serii. Używając puli elastycznej platformy Azure mogą serii zgodnie z potrzebami i korzystać z oprogramowania bez problemów."

Oprócz poprawy wydajności w przypadku klientów, Azure pule elastyczne zwalniane zasobów oprogramowania CSI skupić się na projektowanie nowych usług i funkcji, zamiast zajmowanie operacje i zarządzania. Te zasoby IT pomogła oprogramowania CSI, zwiększyć jego oprogramowania korporacyjnego oferty SpectrumNG ułatwiać Uwzględnij elementy członkowskie siłowni i zwiększyć wydajność pracowników i nadaj pracowników i członków dostęp z urządzeń przenośnych interakcyjne zadań i powiadomień w czasie rzeczywistym.

Azure również pomogła oprogramowania CSI przyspieszenia i ulepszania programowanie i cyklu jakości (QA), należy włączyć opcje automatyzacji. Z implementacją Azure firmy menedżerów kompilacji można spakować składników za kliknięcia przycisku. Zgodnie z opisem Molina "w ramach cyklu wersji, pytań i odpowiedzi jest teraz niemożliwe do środowiska testowego na platformie Azure ściśle naśladuje naszych stosu produkcji. Firma Microsoft natychmiast wdrożyć kompilacji do naszej środowiska deweloperskiego do Zweryfikuj zmiany. To duży win firmie Microsoft, ponieważ brakuje parzystości do testowania przed nim."

## <a name="offloading-to-the-cloud"></a>Odciążanie do chmury
Przed przejściem do chmury, CSI oprogramowania pomyślnie były tworzone własnej infrastruktury wielodostępnym w lokalnym centrum danych w Houston. Ponieważ firma rozwinięta, muszą ponosić zwiększa rosnący problemy z zakupu, inicjowanie obsługi i utrzymania wszystkich sprzęt i oprogramowanie wymagane do obsługi klientów. IT personelu obsługi operacji stał się inny "wąskie gardło", która doprowadziła do spowolnienie inicjowania obsługi administracyjnej nowych zasobów i wdrażania nowych usług dla klientów.

CSI oprogramowania przeanalizowaniu możliwości chmury dla eliminując tym obciążenie, dzięki czemu można skupić się na jego kod, zamiast jego operacji. Firma odnalezienie wielu dostawców chmury top tylko oferują infrastruktura jako usługa (IaaS) rozwiązania, które nadal wymagają dużych personelu IT zarządzanie stosu IaaS. W końcu CSI oprogramowania ustaliła, że rozwiązania Azure PaaS jest najlepszym rozwiązaniem dla swoich potrzeb. Wyjaśniono Molina "Azure pobiera przeszkadza, oprogramowania sprzętu i systemu, więc możemy skupić się na zakupione oprogramowanie, jednocześnie zmniejszając narzut na naszych IT."

## <a name="making-the-transition-to-azure"></a>Przejścia do platformy Azure
Po wybraniu Azure swojego rozwiązania PaaS, oprogramowania CSI rozpoczęcia migracji jego infrastruktury wewnętrznej bazy danych i baz danych do chmury. Przed Azure klienci SpectrumNG potrzebne do zainstalowania aplikacji klienckiej, która przekazane za pomocą usługi Windows Communication Foundation (WCF) na wewnętrznej. Zgodnie z Molina "ale niektórzy klienci hostowanej wszystko w ich własnych centrach danych, budujemy limit produktu, który ma być wielodostępnym. Firma Microsoft hostowane wszystko w centrum danych w Houston, przy użyciu programu SQL Server do przechowywania danych.

"Produktu oferty również uwzględnione skierowane do elementu członkowskiego portalu sieci web (napisany w języku ASP.net), który został zaprojektowany jako biały etykietą odpowiadające klienta sieci web obecności i interfejs API SOAP do obsługi stron online i integracja z żadnych innych firm".

Migracja do chmury nie trwać długo dla architektury. Zgodnie z Molina, "Większości nakładu omówione zmianę sposobu możemy odczytać informacje o plikach konfiguracji, modyfikacji scentralizowane ciągu połączenia i automatyzacji tworzenia pakietów, przekazywanie i wdrażanie naszych wersje".

Aby opracować automatyzacji kompilacji, inżynierów oprogramowania CSI używany do tworzenia pakietów i przekazać je do środowiska pomostowego w wersji każdej nocy programu Azure PowerShell i interfejsów API REST.
Ogólny przejścia do wdrożenia usługi Azure opartej na chmurze poszło szybkie i sprawne zespół IT oprogramowania CSI. Wyjaśniono Molina "we wszystkich, było środowisku w wersji beta w chmurze w ciągu trzech do czterech tygodni wykonywania w projekcie. Który to zaskakująco win firmie Microsoft."

Po skonfigurowaniu i środowisku testowym, oprogramowania CSI rozpoczęcia migracji klientów. Klienci korzystający z już hosting CSI oprogramowania przejście zostało prawie bezproblemowe. W przypadku migracji z lokalnego wdrożenia klientów, przez niektóre dodatkowe czas migracji do chmury, ale nadal głównie został słabe wolne dla klientów i CSI oprogramowania.

Dla nowych klientów, CSI oprogramowania przez personel działu informatycznego wykonać poniższe czynności na płycie je do platformy Azure:

1. Skrypty programu PowerShell Azure są używane do pokrętła nowej bazy danych dla klienta; Wszyscy klienci uruchamiane w warstwie premium, zapewnienie wystarczającej ilości początkowej przepływności przejścia.
2. Jeśli to możliwe, oprogramowania CSI przenosi istniejących danych do wystąpienia bazy danych SQL Azure za pomocą Kreatora migracji SQL Azure.
3. Na koniec Microsoft SQL Server Integration Services (SSIS) są używane w celu uzgodnienia niezgodności w danych lub wykonania oczyszczania danych zgodnie z wymaganiami.

Obecnie około 99 procent CSI oprogramowania znajdują się na platformie Azure, centrów cztery regionalne (Północna centralnej, Południowa centralnej, Wschodnia i zachód). Dzięki użyciu centrów danych w regionie geograficznym każdego klienta, czas oczekiwania jest ograniczone do minimum.

## <a name="azure-elastic-pools-free-up-it-resources"></a>Azure pule elastyczne zwolnić zasoby IT
Kilka funkcji Azure pomogły oprogramowania CSI shift przed infrastrukturę i operacje fokus do funkcji i rozwoju fokus. Być może została największych korzyści z pul elastycznych.

Oprogramowanie CSI obecnie zapewnia około 550 bazy danych dla klientów. Przed pule elastyczne został trudno było zarządzać tak wielu baz danych w ramach struktury warstwy. Menedżerowie OPS było przypisać warstwami wydajności w zależności od potrzeb klientów, które są wymagane znaczących IT zasobów narzut serii. Z pule elastyczne menedżerów można przypisać dzierżawcy, premium lub puli standardowej, zgodnie z potrzebami, przenieść klientów w oparciu o rozmiar i muszą. Klienci mieli niemal natychmiast; świadomość skutków pul elastycznych przed pule elastyczne klienci była limity czasu i inne problemy okresach obciążenia serii, ale z pul elastycznych klientów może zgłaszać seria działania zgodnie z potrzebami i może nadal używać SpectrumNG bez problemów.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure aktywna replikacja geograficzna przyspiesza raportowania
Kilka klientów CSI oprogramowania są także korzystanie z platformy Azure aktywna replikacja geograficzna. Z aktywna replikacja geograficzna maksymalnie cztery czytelny pomocniczej bazy danych można skonfigurować w regionach centrum danych z tego samego lub innego. Oprogramowanie CSI korzysta z aktywnej georeplikacji na dwa sposoby: najpierw pomocnicze bazy danych są dostępne w przypadku awarii centrum danych lub brakiem możliwości nawiązania połączenia podstawowej bazy danych; i drugie, dodatkowej bazy danych są do odczytu i może służyć do odciążenia obciążeń tylko do odczytu, takich jak zadania raportowania. Niektórzy klienci CSI oprogramowania umożliwia świadczenie przyspieszanie raportowania przepływów pracy.

## <a name="csi-software-application-logic-and-architecture"></a>Architektura i logiki aplikacji CSI oprogramowania
SpectrumNG używa role sieci web. Ponieważ aplikacja jest wielodostępne, usługi WCF służy do obsługi żądania początkowego połączenia od klientów. Jak Molina stany, "żądanie identyfikuje każdego klienta, które pozwoli następnie utworzenia parametrów połączenia się do ich baz danych w celu niezależnie od konieczne."

Dla warstwy sieci web z jej usługi oprogramowania CSI korzysta z platformy Azure automatyczne skalowanie, oparte na dzień i godzinę. Dostępne zasoby są automatycznie zwiększyć większego użycia w godzinach pracy, zgodnie ze strefą czasową każdego regionalne centrum danych. Zasoby również są ustawione na skali w weekendy, gdy są niższe potrzeb klientów.

![Architektura Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Rysunek 1. Rola proces roboczy usługi w chmurze pobiera dane strukturalne z bazy danych SQL Azure i częściowo ustrukturyzowanych danych z magazynu tabel. SpectrumNG użytkownicy korzystają z, że danych za pośrednictwem chmury usług roli sieci web.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Za pomocą aplikacji sieci web i warstwą plan sieci web dla aplikacji mobilnych
Wprowadzanie nowych inicjatyw, łącznie z pełną platform przenośnych przy użyciu bazy danych SQL Azure zwalniane zasobów CSI oprogramowania na podstawie niestandardowego interfejsu API hostowanych w aplikacjach sieci web platformy Azure. Platforma umożliwia członków siłowni i personelu używanie urządzeń przenośnych sprawdzić harmonogramy zarezerwować klas i odbierania wiadomości.

Platforma używa zorientowane na usługę architektura (SOA) do podjęcia pojedynczego składnika —, takich jak system punktach sprzedaży (POS) lub system sprzedaży — przenieść go na bieżąco inny plan sieci web, a następnie pokrętła usługi do obsługi tego składnika, pozostawiając inne na oryginalnego planu sieci web. Ta możliwość zapewnia elastyczność ogromne CSI oprogramowania, co zapewnia utrzymywanie niskich kosztów.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure umożliwia oprogramowania CSI deweloperzy fokus na aplikacje i usługi
Baza danych SQL Azure nie jest po prostu zwiększa SpectrumNG klientom, którzy korzystają z usługi szybkie i niezawodne, również jest duży win CSI oprogramowania personelu IT i deweloperów. Dzięki przeniesieniu ops na platformie Azure w chmurze, oprogramowanie CSI zmniejszyć obciążenie zasobów i infrastruktury, znacznie skrócić swoim programistycznych i nie musi micromanage baz danych w celu optymalizacji wydajności dla swoich dzierżaw.

## <a name="more-information"></a>Więcej informacji
* Aby uzyskać więcej informacji na temat usługi Azure pule elastyczne, zobacz [pule elastyczne](sql-database-elastic-pool.md).
* Aby dowiedzieć się więcej na temat narzędzia bazy danych i elastyczne skalowanie, zobacz [narzędzi elastycznej bazy danych i elastyczne skalowanie](sql-database-elastic-scale-get-started.md).
* Aby dowiedzieć się więcej na temat migracji bazy danych programu SQL Server, zobacz zobacz [Migrowanie bazy danych programu SQL Server na platformie Azure](sql-database-cloud-migrate.md).
* Aby dowiedzieć się więcej na temat aktywna replikacja geograficzna, zobacz [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md).
* Aby dowiedzieć się więcej na temat usługi Azure Service Bus, zobacz [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Aby dowiedzieć się więcej na temat automatycznego skalowania, zobacz [skalowania usługi w chmurze](../cloud-services/cloud-services-how-to-scale-portal.md).

