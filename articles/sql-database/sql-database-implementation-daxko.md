---
title: Usługi Azure SQL Database platformy Azure analizą przypadku firmy - Daxko/CSI | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak Daxko/CSI używa bazy danych SQL, przyspieszyć cykl tworzenia i zwiększyć jej działu pomocy technicznej i wydajność
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: ee06039242afe5b5e92cf2fecc4c3fa9bff0b78b
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214373"
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI użyła systemu Azure, przyspieszyć cykl tworzenia i zwiększyć jej działu pomocy technicznej i wydajność
![Daxko/CSI Logo](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Oprogramowanie Daxko/CSI zmierzyła się z zespołem wyzwanie: jej klientów, przydatności i rekreacja centrów był rośnie szybko, dzięki Powodzenie swoje rozwiązanie kompleksowe oprogramowania korporacyjnego, ale Nadążanie za potrzeb infrastruktury IT dla tego klienta rosnący podstawowy został badania firmy personel IT. Firmy coraz częściej zostało ograniczone przez rosnący koszty operacji, szczególnie w przypadku zarządzania jego rosnącą baz danych. Co gorsza że koszty operacji został Wycinanie na zasoby do tworzenia nowych inicjatyw, takich jak nowe funkcje mobilności dla oprogramowania firmy.

Zgodnie z Davidem Molina, Dyrektor ds. rozwoju produktów w Daxko/CSI dostarczanego przez platformę Azure CSI oprogramowania przy użyciu modelu platforma jako usługa (PaaS), za pomocą ona potrzebna, aby uprościć zarządzanie bazą danych, zwiększyć skalowalność i Zwolnij część zasobów, aby skoncentrować się na oprogramowanie zamiast ops. "Usługa azure SQL Database była to doskonałe rozwiązanie dla nas. Nie martwiąc się o zachowanie programu SQL Server, klastra trybu failover i wszystko, czego potrzebuje innych infrastruktury jest idealne rozwiązanie dla nas."

Od migracji na platformę Azure, oprogramowanie CSI musi Operatorzy tylko dwóch Zarządzanie ponad 600 bazy danych klientów. Firma używa pul elastycznych usługi Azure SQL Database, aby przenieść bazy danych klientów na podstawie rozmiaru i potrzebujesz.

Molina będzie się powtarzał, "klientom uznało zmiany od razu. Przed pul elastycznych od czasu do czasu mieli oni przekroczeń limitu czasu i innych problemów w okresach dużego ruchu. Dzięki pulom elastycznym platformy Azure mogą serii stosownie do potrzeb i korzystać z oprogramowania bez problemów."

Oprócz poprawę wydajności w przypadku klientów, pule elastyczne usługi Azure zwolnienie zasobów CSI oprogramowania, aby skupić się na rozwijaniu nowych usług i funkcji, zamiast zajmowanie się operacje i zarządzanie. Te zasoby IT pomogła oprogramowania CSI poprawić jej oprogramowania korporacyjnego oferty SpectrumNG ułatwia angażowanie elementów członkowskich w przypadku treningów, zwiększyć wydajność pracowników i oferują pracowników i członków mobilny dostęp dla zadania interaktywnego i powiadomienia w czasie rzeczywistym.

Azure pomogła również CSI oprogramowania przyspieszyć i poprawić programowanie i cyklu jakości (pytań i odpowiedzi), należy włączyć opcje automatyzacji. W przypadku implementacji platformy Azure w firmie menedżerów kompilacji można spakować składników za pomocą kliknięcia przycisku. Zgodnie z opisem Molina "w ramach cyklu tworzenia wydań, pytań i odpowiedzi jest teraz możliwość wdrażania w środowisku testowym na platformie Azure ścisłe naśladowanie naszego stosu w środowisku produkcyjnym. Firma Microsoft od razu wdrażać kompilacje do naszego środowiska deweloperskiego do Zweryfikuj zmiany. To duża zaleta dla nas, ponieważ nie mamy parzystości do testowania, wcześniej".

## <a name="offloading-to-the-cloud"></a>Przeniesienie do chmury
Przed przejściem do chmury, oprogramowania CSI było pomyślnie tworzone na własną infrastrukturę wielodostępne w lokalnym centrum danych w Houston. Więcej informacji na firmę, sterowaną zwiększa stara narastania zakupu, inicjowania obsługi i utrzymywania wszystkich sprzęt i oprogramowanie wymagane do obsługi klientów. IT zaoszczędziła w celu obsługi operacji stało się innym wąskiego gardła, które doprowadziło do spowalniają działanie aprowizowania nowych zasobów i wdrażanie nowych usług dla klientów.

Oprogramowanie CSI przeanalizowaniu opcje usług w chmurze dla eliminując tym obciążenie tak, aby go skupić się na jej kodzie, zamiast jego operacje. Firma odnalezione, że wielu dostawców chmurze najpopularniejsze oferuje tylko usługę infrastruktura jako usługa (IaaS) rozwiązania, które nadal wymaga dużych informatykami, aby zarządzać stosu IaaS. Na koniec oprogramowania CSI określić, że rozwiązania PaaS platformy Azure jest najlepszym rozwiązaniem dla swoich potrzeb. Wyjaśnia Molina "Azure pobiera przeszkadza, oprogramowanie sprzętu i systemu, dzięki czemu możemy skupić się na naszych ofert oprogramowania, przy jednoczesnym zmniejszeniu wymaganych naszych nakład pracy działu IT."

## <a name="making-the-transition-to-azure"></a>Dlatego przejście na platformę Azure
Po wybraniu platformy Azure dla swojego rozwiązania PaaS, oprogramowanie CSI rozpoczęło, migrujesz swoją infrastrukturę zaplecza i bazy danych do chmury. Przed Azure klienci SpectrumNG są niezbędne do zainstalowania aplikacji klienckiej, która przekazywane za pomocą usługi Windows Communication Foundation (WCF) na zapleczu. Zgodnie z Molina "chociaż niektórzy klienci hostowanych w ich własnych centrów danych, utworzyliśmy produktu, który ma być wielodostępnej. Firma Microsoft hostowane w centrum danych w Houston, przy użyciu programu SQL Server jako magazyn danych.

"Nasz produkt oferuje również dołączona skierowaną do elementu członkowskiego portalu sieci web (napisana w języku ASP.net), który został zaprojektowany w celu być oznaczony biały spełnić obecność w sieci web klienta i interfejsu API SOAP do obsługi stron online i integracja żadnych innych firm".

Migracja do chmury nie zająć wiele czasu dla architektury. Zgodnie z Molina, "Większość wysiłków omówione zmianę sposobu, możemy odczytać informacji o pliku konfiguracji, modyfikacji scentralizowane parametry połączenia i automatyzowanie opakowania, przekazywanie i wdrażania wydań."

Do tworzenia automatyzacji kompilacji, programistów CSI używane programu Azure PowerShell i interfejsów API REST do tworzenia pakietów i przekazać je w środowisku przejściowym wersji każdej nocy.
Ogólną przejście do platformy Azure wdrożenie oparte na chmurze poszło szybko i sprawnie zespół IT oprogramowania CSI. Wyjaśnia Molina "we wszystkich z nich, mieliśmy środowiska w wersji beta w chmurze w ciągu trzech do czterech tygodni pracy nad projektem. To było Zaskakujące korzyść dla nas."

Po skonfigurowaniu i środowiska testowego, oprogramowanie CSI rozpoczęcia migracji klientów. Klienci już korzystający z oprogramowania CSI hostingu niemal bezproblemowej była przejścia. W przypadku migracji z lokalnego wdrożenia klientów, niektóre dodatkowy czas trwania migracji do chmury, ale była nadal głównie bezproblemowe dla klientów i CSI oprogramowania.

W przypadku nowych klientów oprogramowania CSI przez personel INFORMATYCZNY Użyj następującego procesu dołączyć je do platformy Azure:

1. Skrypty programu PowerShell platformy Azure są używane do uruchomienia nowej bazy danych dla klienta; wszystkich klientów rozpoczyna pracę w ramach warstwy premium w celu zapewnienia wystarczającej przepływności początkowej przejścia.
2. Jeśli to możliwe, oprogramowanie CSI korzysta z Kreatora migracji SQL Azure do przenoszenia istniejących danych do wystąpienia usługi Azure SQL Database.
3. Na koniec programu Microsoft SQL Server Integration Services (SSIS) są używane, aby uzgodnić wszelkie rozbieżności danych lub wykonanie czyszczenia danych zgodnie z potrzebami.

Już dziś około 99% CSI oprogramowania klientów są hostowane na platformie Azure w czterech regionalnych centrach danych (północno-środkowych, południowo-środkowych stanach Wschód i zachód). Przez umieszczenie centrów danych w regionie geograficznym każdego klienta, czas oczekiwania jest ograniczone do minimum.

## <a name="azure-elastic-pools-free-up-it-resources"></a>Pule elastyczne usługi Azure Uwolnij zasoby IT
Kilka funkcji platformy Azure pomogły oprogramowania CSI shift miałyby infrastrukturę i operacje, które skupia się do funkcji i rozwoju skupia się. Może być największa zaleta zostało z elastycznymi pulami.

Oprogramowanie CSI udostępnia obecnie około 550 baz danych dla klientów. Przed pul elastycznych trudno było zarządzać oznacza wiele baz danych w ramach struktury warstwy. Menedżerowie OPS było przypisać warstwy wydajności, w zależności od potrzeb dużego ruchu klientów, które wymagane znaczące IT zasobów obciążenie. Dzięki pulom elastycznym menedżerów można przypisać dzierżaw premium lub standardowa puli, zgodnie z potrzebami i następnie przenieść klientów na podstawie rozmiaru i potrzebujesz. Klienci niemal natychmiast; mieli świadomość skutków pul elastycznych przed pul elastycznych klienci miał przekroczeń limitu czasu i innych problemów w okresach dużego ruchu użycia, ale dzięki pulom elastycznym, klienci będą mogli doświadczyć gwałtownymi wzrostami liczby działań zgodnie z potrzebami i może nadal używać SpectrumNG bez problemów.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Usługa Azure aktywnej replikacji geograficznej przyspiesza raportowania
Wielu klientów oprogramowania CSI są również korzystanie z platformy Azure aktywną replikację geograficzną. Przy użyciu aktywnej replikacji geograficznej maksymalnie cztery odczytu pomocniczych baz danych można skonfigurować w centrum danych z tej samej lub różnych regionach. CSI oprogramowanie korzysta z aktywnej replikacji geograficznej na dwa sposoby: najpierw pomocnicze bazy danych są dostępne w przypadku awarii centrum danych lub z brakiem, aby nawiązać połączenie z podstawowej bazy danych. i po drugie, pomocniczych baz danych można odczytać i może służyć do odciążenia obciążeń tylko do odczytu, takich jak zadania raportowania. Niektórzy klienci oprogramowania CSI użyć tej korzyści, aby przyspieszyć raportowania przepływów pracy.

## <a name="csi-software-application-logic-and-architecture"></a>Architektura i logiki aplikacji oprogramowania CSI
SpectrumNG korzysta z ról sieci web. Ponieważ aplikacja jest wielodostępne, usługi WCF służy do obsługi żądań początkowego połączenia od klientów. Jak Molina stany, "żądanie identyfikuje każdego klienta, która następnie umożliwia nam w stworzeniu parametry połączenia w poziomie do swoich baz danych w celu, niezależnie od rodzaju musimy."

Swoje usługi w warstwie sieci web oprogramowania CSI korzysta z Azure automatycznego skalowania, zależnie od dnia i godziny. Dostępne zasoby są automatycznie zwiększyć większego użycia w godzinach, zgodnie ze strefą czasową każdego regionalnym centrum danych. Zasoby są ustawione również skalować w dół w weekendy, kiedy są niższe potrzeb klienta.

![Architektura Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Rysunek 1. Roli procesu roboczego usług w chmurze pobiera dane ze strukturą z usługi Azure SQL Database i częściowo ustrukturyzowane dane z usługi table storage. Użytkownicy SpectrumNG oddziałują, czy dane za pomocą chmury usługi roli sieci web.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Za pomocą aplikacji sieci web i warstwę planu sieci web dla aplikacji mobilnych
Przy użyciu usługi Azure SQL Database zwolnione zasoby dotyczące oprogramowania CSI nowych inicjatyw, łącznie z pełną platform przenośnych na podstawie niestandardowego interfejsu API hostowanych w usłudze Azure web apps. Platforma umożliwia przypadku treningów, członków i personelu, sprawdzić harmonogramy, Zarezerwuj klas oraz odbieranie komunikatów za pomocą urządzeń przenośnych.

Platforma korzysta dotycząca architektury zorientowanej na usługi (SOA) do tworzenia pojedynczego składnika — takich jak system sprzedaży lub punktach sprzedaży (POS) systemu — Przenieś ją na bieżąco do innego planu sieci web, a następnie uruchom usługę do obsługi tego składnika, przy równoczesnym zachowaniu wszystkich elementów na oryginalny plan sieci web. Ta możliwość zapewnia ogromną elastyczność CSI oprogramowania, a także pomaga, niskich kosztów.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Możliwości oprogramowania CSI deweloperom skoncentrowanie się na aplikacjach i usługach
Usługa Azure SQL Database nie jest po prostu boon SpectrumNG klientom, którzy Ciesz się szybką, niezawodną usługę, również to duża zaleta dla oprogramowania CSI personel IT i deweloperów. Dzięki przeniesieniu operacji na platformie Azure w chmurze, oprogramowanie CSI zmniejszyć obciążenie zasobów i infrastruktury, znacznie skrócić czas jego cyklowi rozwoju i nie jest już potrzeby micromanage baz danych w celu zoptymalizowania wydajności dla swoich dzierżaw.

## <a name="more-information"></a>Więcej informacji
* Aby dowiedzieć się więcej na temat pule elastyczne usługi Azure, zobacz [pul elastycznych](sql-database-elastic-pool.md).
* Aby dowiedzieć się więcej na temat narzędzia graficzne bazy danych i elastyczne skalowanie, zobacz [narzędzi elastycznej bazy danych i elastyczne skalowanie](sql-database-elastic-scale-get-started.md).
* Aby dowiedzieć się więcej na temat migracji bazy danych programu SQL Server, zobacz [Migrowanie bazy danych programu SQL Server na platformę Azure](sql-database-cloud-migrate.md).
* Aby dowiedzieć się więcej na temat aktywnej replikacji geograficznej, zobacz [aktywnej replikacji geograficznej](sql-database-geo-replication-overview.md).
* Aby dowiedzieć się więcej na temat usługi Azure Service Bus, zobacz [usługi Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Aby dowiedzieć się więcej na temat automatycznego skalowania, zobacz [skalowaniu usług cloud services](../cloud-services/cloud-services-how-to-scale-portal.md).

