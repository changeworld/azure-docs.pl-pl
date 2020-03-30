---
title: Wybieranie odpowiedniego typu wdrożenia — usługa Azure Database for MySQL
description: W tym artykule opisano, jakie czynniki należy wziąć pod uwagę przed wdrożeniem usługi Azure Database for MySQL jako infrastruktury jako usługi (IaaS) lub platformy jako usługi (PaaS).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 50bcd42189b1bcc945d726277975892f07f1baa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255529"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Wybierz odpowiednią opcję Serwera MySQL na platformie Azure

Dzięki platformie Azure obciążenia serwera MySQL mogą być uruchamiane w infrastrukturze hostowanego maszyny wirtualnej jako usługa (IaaS) lub jako hostowana platforma jako usługa (PaaS). PaaS ma wiele opcji wdrażania i istnieją warstwy usług w ramach każdej opcji wdrażania. Wybierając między usługami IaaS i PaaS, należy zdecydować, czy chcesz zarządzać bazą danych, stosować poprawki i tworzyć kopie zapasowe, czy też chcesz delegować te operacje na platformę Azure.

Podejmując decyzję, rozważ następujące dwie opcje:

- **Usługa Azure Database for MySQL**. Ta opcja jest w pełni zarządzanym aparatem bazy danych MySQL opartym na stabilnej wersji wersji społeczności MySQL. Ta relacyjnej bazy danych jako usługi (DBaaS), hostowane na platformie azure w chmurze, należy do kategorii branżowej PaaS.

  Za pomocą zarządzanego wystąpienia MySQL na platformie Azure można użyć wbudowanych funkcji, które w przeciwnym razie wymagają rozbudowanej konfiguracji, gdy serwer MySQL jest lokalny lub na maszynie Wirtualnej platformy Azure.

  Podczas korzystania z MySQL jako usługi, płacisz jak idziesz z opcji skalowania w górę lub skalowania w poziomie dla większej kontroli bez przerwy. W przeciwieństwie do autonomicznego serwera MySQL, usługa Azure Database for MySQL ma dodatkowe funkcje, takie jak wbudowana wysoka dostępność, inteligencja i zarządzanie.

- **MySQL na maszynach wirtualnych platformy Azure**. Opcja ta należy do kategorii branżowej IaaS. Dzięki tej usłudze można uruchomić serwer MySQL wewnątrz w pełni zarządzanej maszyny wirtualnej na platformie chmurowej platformy Azure. Wszystkie najnowsze wersje mysql można zainstalować na maszynie wirtualnej IaaS.

  W najbardziej znaczącej różnicy od usługi Azure Database dla MySQL MySQL na maszynach wirtualnych platformy Azure oferuje kontrolę nad aparatem bazy danych. Jednak ten formant odbywa się kosztem odpowiedzialności za zarządzanie maszynami wirtualnymi i wieloma zadaniami administrowania bazą danych (DBA). Zadania te obejmują utrzymywanie i poprawianie serwerów bazy danych, odzyskiwanie bazy danych i projektowanie o wysokiej dostępności.

Główne różnice między tymi opcjami są wymienione w poniższej tabeli:

|            | Azure Database for MySQL | MySQL na maszynach wirtualnych platformy Azure    |
|:-------------------|:-----------------------------|:--------------------|
| Umowa dotyczącą poziomu usług (SLA)                | Oferty SLA dostępności na 99,99%| Dostępność do 99,95% z co najmniej dwoma wystąpieniami w tym samym zestawie dostępności.<br/><br/>Dostępność 99,9% z pojedynczą maszyną wirtualną wystąpienia przy użyciu magazynu w wersji premium.<br/><br/>99,99% przy użyciu stref dostępności z wieloma wystąpieniami w wielu zestawach dostępności.<br/><br/>Zobacz [umowy SLA maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Poprawki systemu operacyjnego        | Automatyczny  | Zarządzane przez klientów |
| Łatanie MySQL     | Automatyczny  | Zarządzane przez klientów |
| Wysoka dostępność | Model wysokiej dostępności (HA) jest oparty na wbudowanych mechanizmach pracy awaryjnej w przypadku wystąpienia przerwania na poziomie węzła. W takich przypadkach usługa automatycznie tworzy nowe wystąpienie i dołącza magazyn do tego wystąpienia. | Klienci projektują, implementują, testują i utrzymują wysoką dostępność. Możliwości mogą obejmować klastrowanie, replikację itp.|
| Nadmiarowość stref | Obecnie nie jest obsługiwana | Maszyny wirtualne platformy Azure można skonfigurować do uruchamiania w różnych strefach dostępności. W przypadku rozwiązania lokalnego klienci muszą tworzyć, zarządzać i obsługiwać własne dodatkowe centrum danych.|
| Scenariusze hybrydowe | Dzięki [replikacji danych](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)można synchronizować dane z zewnętrznego serwera MySQL z usługą Azure Database for MySQL. Serwer zewnętrzny może być lokalny, na maszynach wirtualnych lub usługa bazy danych hostowana przez innych dostawców chmury.<br/><br/> Dzięki funkcji [repliki odczytu](https://docs.microsoft.com/azure/mysql/concepts-read-replicas) można replikować dane z usługi Azure Database dla serwera głównego MySQL do maksymalnie pięciu serwerów replik tylko do odczytu. Repliki znajdują się w tym samym regionie platformy Azure lub w różnych regionach. Repliki tylko do odczytu są asynchronicznie aktualizowane przy użyciu technologii replikacji binlog.| Zarządzane przez klientów
| Tworzenie kopii zapasowych i przywracanie | Automatycznie tworzy [kopie zapasowe serwera](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) i przechowuje je w magazynie skonfigurowanym przez użytkownika, który jest lokalnie nadmiarowy lub geograficznie nadmiarowy. Usługa wykonuje pełne, różnicowe i rejestry kopii zapasowych transakcji | Zarządzane przez klientów |
| Monitorowanie operacji bazy danych | Oferuje klientom możliwość [ustawiania alertów](https://docs.microsoft.com/azure/mysql/concepts-monitoring) dotyczących operacji bazy danych i działania po osiągnięciu progów. | Zarządzane przez klientów |
| Zaawansowana ochrona przed zagrożeniami | Zapewnia [zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal). Ta ochrona wykrywa nietypowe działania, które wskazują na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. | Klienci muszą zbudować tę ochronę dla siebie.
| Odzyskiwanie po awarii | Przechowuje automatyczne kopie zapasowe w skonfigurowanej [lokalnie nadmiarowej lub geograficznie nadmiarowej pamięci masowej.](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal) Kopie zapasowe mogą również przywrócić serwer do punktu w czasie. Okres przechowywania wynosi od 7 do 35 dni. Przywracanie odbywa się za pomocą witryny Azure portal. | W pełni zarządzane przez klientów. Obowiązki obejmują między innymi planowanie, testowanie, archiwizowanie, przechowywanie i przechowywanie. Dodatkową opcją jest użycie magazynu usług Azure Recovery Services do utworzenia kopii zapasowej maszyn wirtualnych i baz danych platformy Azure na maszynach wirtualnych. Ta opcja jest w wersji zapoznawczej. |
| Zalecenia dotyczące wydajności | Zapewnia klientom zalecenia dotyczące wydajności oparte na [plikach](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) dziennika użycia generowanych przez system. Zalecenia pomagają zoptymalizować obciążenia. | Zarządzane przez klientów |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motywacje biznesowe przy wyborze PaaS lub IaaS

Istnieje kilka czynników, które mogą mieć wpływ na decyzję o wybraniu PaaS lub IaaS do hostowania baz danych MySQL.

### <a name="cost"></a>Koszty

Ograniczone finansowanie jest często podstawowym czynnikiem, który określa najlepsze rozwiązanie do hostowania baz danych. To prawda, niezależnie od tego, czy jesteś startupem z niewielką gotówką, czy zespołem w ustalonej firmie, która działa pod ścisłymi ograniczeniami budżetowymi. W tej sekcji opisano podstawy rozliczeń i licencjonowania na platformie Azure, które mają zastosowanie do usługi Azure Database for MySQL i MySQL na maszynach wirtualnych platformy Azure.

#### <a name="billing"></a>Rozliczenia

Usługa Azure Database for MySQL jest obecnie dostępna jako usługa w kilku warstwach z różnymi cenami zasobów. Wszystkie zasoby są rozliczane co godzinę według stałej stawki. Aby uzyskać najnowsze informacje na temat aktualnie obsługiwanych warstw usług, rozmiarów obliczeń i ilości magazynu, zobacz [model zakupów oparty na procesorach wirtualnych](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Można dynamicznie dostosowywać warstwy usług i rozmiary obliczeń, aby dopasować je do różnych potrzeb aplikacji w zakresie przepływności. Opłaty są naliczane za wychodzący ruch internetowy przy regularnych [szybkościach przesyłania danych.](https://azure.microsoft.com/pricing/details/data-transfers/)

Dzięki usłudze Azure Database for MySQL firma Microsoft automatycznie konfiguruje, łata i uaktualnia oprogramowanie bazy danych. Te zautomatyzowane działania obniżają koszty administracyjne. Ponadto usługa Azure Database for MySQL ma wbudowane funkcje [tworzenia kopii zapasowych.](https://docs.microsoft.com/azure/mysql/concepts-backup) Te możliwości pomagają osiągnąć znaczne oszczędności kosztów, zwłaszcza gdy masz dużą liczbę baz danych. W przeciwieństwie do MySQL na maszynach wirtualnych platformy Azure można wybrać i uruchomić dowolną wersję MySQL. Bez względu na to, jakiej wersji MySQL używasz, płacisz za aprowizowanego VM i koszty dla określonego typu licencji MySQL używane.

Usługa Azure Database for MySQL zapewnia wbudowaną wysoką dostępność dla wszelkiego rodzaju przerw na poziomie węzła przy jednoczesnym zachowaniu 99,99% gwarancji umowy SLA dla usługi. Jednak dla bazy danych wysokiej dostępności w maszynach wirtualnych, klienci powinni używać opcji wysokiej dostępności, takich jak [replikacja MySQL,](https://dev.mysql.com/doc/refman/8.0/en/replication.html) które są dostępne w bazie danych MySQL. Korzystanie z obsługiwanej opcji wysokiej dostępności nie zapewnia dodatkowej umowy SLA. Ale to pozwala osiągnąć więcej niż 99.99% dostępność bazy danych przy dodatkowych kosztach i administracyjnych narzutów.

Aby uzyskać więcej informacji na temat cen, zobacz następujące artykuły:
* [Cennik usługi Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* [Ceny maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administracja

Dla wielu firm decyzja o przejściu na usługę w chmurze dotyczy tyle samo odciążania złożoności administracji, co kosztów. Z IaaS i PaaS, Microsoft:

- Administruje podstawową infrastrukturą.
- Automatycznie replikuje wszystkie dane w celu zapewnienia odzyskiwania po awarii.
- Konfiguruje i uaktualnia oprogramowanie bazy danych.
- Zarządza równoważeniem obciążenia.
- Czy przezroczyste trybu fail-over, jeśli występuje awaria serwera.

Na poniższej liście opisano względy administracyjne dla każdej opcji:

* Dzięki usłudze Azure Database for MySQL możesz nadal administrować bazą danych. Ale nie trzeba już zarządzać aparatem bazy danych, systemem operacyjnym ani sprzętem. Przykłady elementów, które można nadal administrować:

  - Bazy danych
  - Logowanie
  - Dostrajanie indeksu
  - Dostrajanie zapytań
  - Inspekcja
  - Zabezpieczenia

  Ponadto konfigurowanie wysokiej dostępności do innego centrum danych wymaga minimalnej lub żadnej konfiguracji lub administrowania.

* Dzięki mysql na maszynach wirtualnych platformy Azure masz pełną kontrolę nad systemem operacyjnym i konfiguracją wystąpienia serwera MySQL. Za pomocą maszyny Wirtualnej można zdecydować, kiedy zaktualizować lub uaktualnić system operacyjny i oprogramowanie bazy danych. Można również zdecydować, kiedy zainstalować dodatkowe oprogramowanie, takie jak aplikacja antywirusowa. Niektóre zautomatyzowane funkcje są dostarczane w celu znacznie uproszczenia poprawek, tworzenia kopii zapasowych i wysokiej dostępności. Można kontrolować rozmiar maszyny Wirtualnej, liczbę dysków i ich konfiguracje magazynu. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Czas przejść na platformę Azure

* Usługa Azure Database for MySQL to właściwe rozwiązanie dla aplikacji zaprojektowanych w chmurze, gdy produktywność deweloperów i szybki czas na rynek nowych rozwiązań mają kluczowe znaczenie. Dzięki funkcji programowej, która jest jak DBA, usługa jest odpowiednia dla architektów chmury i deweloperów, ponieważ zmniejsza potrzebę zarządzania podstawowym systemem operacyjnym i bazą danych.

* Jeśli chcesz uniknąć czasu i kosztów nabycia nowego sprzętu lokalnego, MySQL na maszynach wirtualnych platformy Azure jest właściwym rozwiązaniem dla aplikacji, które wymagają bazy danych MySQL lub dostępu do funkcji MySQL w systemie Windows lub Linux. To rozwiązanie jest również odpowiednie do migracji istniejących lokalnych aplikacji i baz danych na platformę Azure w stanie nienaruszonym, w przypadkach, gdy usługa Azure Database for MySQL jest źle dopasowana.

  Ponieważ nie ma potrzeby zmiany warstw prezentacji, aplikacji i danych, można zaoszczędzić czas i budżet na rearchitecting istniejącego rozwiązania. Zamiast tego można skupić się na migracji wszystkich rozwiązań na platformę Azure i adresowania niektórych optymalizacji wydajności, które mogą wymagać platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Usługi Azure Database dla cen MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
* Rozpocznij od [utworzenia pierwszego serwera](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal).
