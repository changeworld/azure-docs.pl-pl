---
title: Wybieranie odpowiedniego typu wdrożenia dla Azure Database for MariaDB
description: W tym artykule opisano zagadnienia, które należy wykonać przed przejściem do infrastruktury jako usługi (IaaS) lub platformy jako usługi (PaaS) dla Azure Database for MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: fa87748719e2d3775034e5a2850281cf8f1a0e8a
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817438"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Wybierz odpowiednią opcję serwera MariaDB na platformie Azure

Na platformie Azure obciążenia serwera MariaDB mogą działać w hostowanej infrastrukturze maszyny wirtualnej (IaaS) lub jako usługa hostowana (PaaS). PaaS ma wiele opcji wdrażania, a w każdej opcji wdrożenia znajdują się warstwy usług. Podczas decydowania o PaaS lub IaaS należy określić, czy chcesz zarządzać bazą danych, stosować poprawki i tworzyć kopie zapasowe, czy też chcesz delegować te operacje na platformę Azure.</br>

Na podstawie odpowiedzi na to pytanie należy wziąć pod uwagę następujące opcje: <br/>

**Azure Database for MariaDB** to w pełni zarządzany aparat bazy danych MariaDB w oparciu o stabilną wersję programu Community Edition. Ta relacyjna baza danych jako usługa (DBaaS) hostowana w chmurze platformy Azure znajduje się w kategorii branżowej platformy jako usługi (PaaS). Za pomocą zarządzanego wystąpienia programu MariaDB na platformie Azure można używać wbudowanych funkcji i funkcji, które wymagają rozbudowanej konfiguracji w przypadku korzystania z serwera MariaDB (lokalnie lub na maszynie wirtualnej platformy Azure). Korzystając z MariaDB jako usługi, możesz skorzystać z opcji w celu skalowania w górę lub w dół w celu uzyskania większej mocy bez przerw w działaniu. Ponadto, w przeciwieństwie do autonomicznego serwera MariaDB, Azure Database for MariaDB oferuje dodatkowe funkcje, takie jak wbudowana wysoka dostępność, analiza i zarządzanie. <br/><br/>
**MariaDB na maszynie wirtualnej platformy Azure** znajduje się w kategorii branżowej infrastruktura jako usługa (IaaS) i umożliwia uruchamianie MariaDB serwera w ramach w pełni zarządzanej maszyny wirtualnej w chmurze platformy Azure. Wszystkie najnowsze wersje i wersje programu MariaDB można zainstalować na maszynie wirtualnej IaaS. Najbardziej znaczącą różnicą od Azure Database for MariaDB jest to, że MariaDB na maszynach wirtualnych platformy Azure umożliwiają kontrolę nad aparatem bazy danych. Jednak ten formant jest kosztem dodatkowej odpowiedzialności za zarządzanie maszynami wirtualnymi i wielu zadań DBA, takich jak Konserwacja serwera bazy danych/stosowanie poprawek, projekt odzyskiwania i wysokiej dostępności itd.

Główne różnice między tymi opcjami są wymienione w poniższej tabeli:

|            | **Azure Database for MariaDB** | **MariaDB na maszynach wirtualnych platformy Azure**    |
|:-------------------|:-----------------------------|:--------------------|
| **Umowa SLA**                | Oferuje umowę SLA na 99,99% dostępności| Do 99,95% dostępności z co najmniej 2 wystąpieniem w tym samym zestawie dostępności. <br/>99,9% maszyny wirtualnej z jednym wystąpieniem przy użyciu usługi Premium Storage <br/> 99,99% ze strefą dostępności z co najmniej 2 wystąpieniem w 2 lub większym zestawie dostępności.<br/> Uwaga — [Umowa SLA dotycząca maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **Stosowanie poprawek systemu operacyjnego**        | Automatyczny  | Zarządzane przez klientów |
|**Stosowanie poprawek MariaDB**     | Automatyczny  | Zarządzane przez klientów |
| **Wysoka dostępność** | Model wysokiej dostępności (HA) jest oparty na wbudowanych mechanizmach trybu failover w przypadku wystąpienia przerwy na poziomie węzła. W takich przypadkach usługa automatycznie tworzy wystąpienie i dołącza magazyn do nowego wystąpienia. | Wysoka dostępność jest poddawana architekturze, implementowana, przetestowana i obsługiwana przez klienta. Może to obejmować zawsze włączone (klaster trybu failover lub replikacja grupowa), wysyłanie dziennika i replikację transakcyjną, w zależności od używanej wersji aparatu MariaDB.|
| **Nadmiarowość stref** | Obecnie nie jest obsługiwane. | Maszyny wirtualne platformy Azure można skonfigurować do uruchamiania w różnych strefach dostępności. W przypadku rozwiązania lokalnego klienci mogą tworzyć i obsługiwać własne pomocnicze centrum danych oraz zarządzać nimi.|
| **Scenariusze hybrydowe** | [Replikacja typu Data-in](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication) pozwala synchronizować dane z zewnętrznego serwera MariaDB do usługi Azure Database for MariaDB. Serwer zewnętrzny może być lokalny, w maszynach wirtualnych lub w usłudze bazy danych hostowanej przez innych dostawców chmury.  <br/> <br/> Funkcja [Read Replica](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) umożliwia replikowanie danych z serwera Azure Database for MariaDB (Master) do maksymalnie pięciu serwerów tylko do odczytu (replik) w tym samym regionie świadczenia usługi Azure lub w różnych regionach. Repliki tylko do odczytu są asynchronicznie aktualizowane przy użyciu technologii replikacji binlog.   <br/> <br/> Uwaga — replikacja odczytu między regionami jest obecnie dostępna w publicznej wersji zapoznawczej.| Zarządzane przez klientów <br/>
| **Tworzenie kopii zapasowej i przywracanie** | Automatycznie tworzy [kopie zapasowe serwera](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) i zapisuje je w skonfigurowanym przez użytkownika miejscu magazynu lokalnie nadmiarowego lub geograficznie nadmiarowego. Usługa przyjmuje kopie zapasowe z pełnymi, różnicami i dziennikami transakcji | Zarządzane przez klientów |
| **Operacje monitorowania bazy danych** | Umożliwia klientom [Ustawianie alertów](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) dotyczących operacji w bazie danych i działanie po osiągnięciu progów. | Zarządzane przez klientów |
| **Zaawansowana ochrona wątków** | Zapewnia [zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal) , która wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. <br/> <br/> Uwaga — Zaawansowana ochrona wątków jest obecnie dostępna w publicznej wersji zapoznawczej.| Klienci muszą kompilować się dla siebie.
| **Kopie zapasowe (odzyskiwanie po awarii)** | Przechowuje zautomatyzowane kopie zapasowe w ramach użytkownika skonfigurowanego [lokalnie nadmiarowy lub geograficznie nadmiarowy](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal). Kopie zapasowe mogą być również używane do przywracania serwera do punktu w czasie. Okres przechowywania można ustawić od 7-35 dni. Przywracanie można wykonać przy użyciu Azure Portal. <br/> | W pełni zarządzane przez klienta, w tym m.in. Planowanie, testowanie, archiwizowanie, przechowywanie i przechowywanie. Dodatkową opcją jest użycie usługi Azure Recovery Services magazyn do tworzenia kopii zapasowych maszyn wirtualnych i baz danych platformy Azure na maszynach wirtualnych (w wersji zapoznawczej). |
| **Zalecenie dotyczące wydajności** | Zapewnia klientom [zalecenia dotyczące wydajności](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) proaktywnej na podstawie danych telemetrycznych użycia, które ułatwiają Optymalizowanie obciążeń. <br/> <br/> Uwaga — zalecenie dotyczące wydajności jest obecnie dostępne w publicznej wersji zapoznawczej. | Zarządzane przez klientów |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motywacje biznesowe do wyboru PaaS lub IaaS

Istnieje kilka czynników, które mogą mieć wpływ na decyzję dotyczącą wyboru PaaS lub IaaS do hostowania baz danych MariaDB:

### <a name="cost"></a>Koszt

Niezależnie od tego, czy jesteś strapped startowym dla środków pieniężnych, czy zespołu w firmie, która działa pod kątem ścisłych ograniczeń budżetowych, ograniczone finansowanie jest często podstawowym zagadnieniem podczas określania najlepszego rozwiązania do hostowania baz danych. W tej sekcji opisano podstawowe informacje dotyczące rozliczeń i licencjonowania na platformie Azure w odniesieniu do Azure Database for MariaDB i MariaDB na maszynach wirtualnych platformy Azure:

#### <a name="billing"></a>Rozliczenia

Obecnie Azure Database for MariaDB jest dostępna jako usługa w kilku warstwach z różnymi cenami dla zasobów, dla których wszystkie są rozliczane godzinowo według ustalonej stawki. Najnowsze informacje dotyczące bieżących obsługiwanych warstw usług, rozmiarów obliczeń i ilości miejsca do magazynowania można znaleźć w temacie [model zakupów oparty na rdzeń wirtualny](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers). Możesz dynamicznie dostosować warstwy usług i rozmiary obliczeń, aby odpowiadały potrzebom różnych przepływności aplikacji. Opłaty są naliczane za wychodzący ruch internetowy przy zwykłych [stawkach za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/).

W przypadku Azure Database for MariaDB oprogramowanie bazy danych jest automatycznie konfigurowane, poprawiane i uaktualniane przez firmę Microsoft, co zmniejsza koszty administracyjne. Ponadto [wbudowana funkcja tworzenia kopii zapasowych](https://docs.microsoft.com/azure/MariaDB/concepts-backup) pozwala osiągnąć znaczne oszczędności kosztów, zwłaszcza w przypadku dużej liczby baz danych. Dzięki MariaDB na maszynach wirtualnych platformy Azure Możesz wybrać i uruchomić dowolną wersję MariaDB. Bez względu na używaną wersję MariaDB płacisz za zainicjowaną maszynę wirtualną i kosztami dla określonego typu licencji używanymi do MariaDB.

Azure Database for MariaDB zapewnia wysoką dostępność dowolnego rodzaju przerw na poziomie węzła i nadal utrzymuje 99,99% umowy SLA dla usługi. Jednak w przypadku wysokiej dostępności bazy danych (HA) w ramach maszyn wirtualnych klient powinien przejść przez opcje wysokiej dostępności dostępne w bazie danych MariaDB, takie jak [replikacja MariaDB](https://mariadb.com/kb/en/library/setting-up-replication/). Korzystanie z obsługiwanej opcji wysokiej dostępności nie zapewnia dodatkowej umowy SLA, ale umożliwia uzyskanie > 99,99% dostępności bazy danych z dodatkowym kosztem i narzutem administracyjnym.

Więcej informacji na temat cen zawierają następujące zasoby:
* [Cennik Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Cennik maszyny wirtualnej](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administracja

W przypadku wielu firm decyzja o przejściu do usługi w chmurze polega na tym, że jest to bardzo dużo na odciążeniu złożoności administracji. Dzięki IaaS i PaaS, firma Microsoft administruje podstawową infrastrukturą i automatycznie replikuje wszystkie dane w celu zapewnienia odzyskiwania po awarii, konfiguruje i uaktualnia oprogramowanie bazy danych, zarządza równoważeniem obciążenia i działa w trybie failover, jeśli istnieje Błąd serwera.

* **Za pomocą Azure Database for MariaDB**można nadal administrować swoją bazą danych, ale nie musisz już zarządzać aparatem bazy danych, systemem operacyjnym ani sprzętem. Przykładowe elementy, którymi można dalej zarządzać to bazy danych, identyfikatory logowania, dostrajanie indeksów i zapytań oraz inspekcja i zabezpieczenia. Ponadto Konfigurowanie wysokiej dostępności w innym centrum danych wymaga konfiguracji minimalnej lub bez niej.<br/><br/>
* **Dzięki MariaDB na maszynach wirtualnych platformy Azure**masz pełną kontrolę nad konfiguracją systemu operacyjnego i wystąpienia serwera MariaDB. W przypadku maszyny wirtualnej należy zdecydować, kiedy należy zaktualizować/uaktualnić system operacyjny i oprogramowanie bazy danych oraz kiedy zainstalować dodatkowe oprogramowanie, np. aplikację antywirusową. Dostępnych jest kilka funkcji automatycznych, które znacznie upraszczają instalowanie poprawek, tworzenie kopii zapasowej i zapewnianie wysokiej dostępności. Ponadto możesz kontrolować rozmiar maszyny wirtualnej, liczbę dysków oraz ich konfiguracje magazynu. Aby uzyskać więcej informacji, zobacz rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure.

### <a name="time-to-move-to-azure-br"></a>Czas przejścia na platformę Azure <br/>
* **Azure Database for MariaDB** to odpowiednie rozwiązanie dla aplikacji zaprojektowanych w chmurze, gdy produktywność deweloperów i krótki czas na rynek dla nowych rozwiązań jest krytyczny. Dzięki funkcjom programistycznym opartym na usłudze SYSDBA usługa jest idealna dla architektów i deweloperów rozwiązań w chmurze, ponieważ obniża potrzebę zarządzania bazowym systemem operacyjnym i bazą danych.<br/><br/>
* **MariaDB na maszynach wirtualnych platformy Azure** doskonale nadaje się do istniejących lub nowych aplikacji, które wymagają bazy danych MariaDB lub dostępu do funkcji w bazie danych MariaDB w systemie Windows/Linux i chcesz uniknąć czasu i kosztów nabycia nowego sprzętu lokalnego. Ta opcja jest również przydatna w przypadku migrowania istniejących lokalnych aplikacji i baz danych na platformę Azure zgodnie z oczekiwaniami — w przypadkach, w których wystąpienie Azure Database for MariaDB nie jest dobrym rozwiązaniem. Ponieważ nie ma potrzeby zmieniania warstw prezentacji, aplikacji i danych, możesz zaoszczędzić czas i budżet w przypadku ponownego tworzenia architektury istniejącego rozwiązania. Zamiast tego można skupić się na migrowaniu wszystkich rozwiązań na platformę Azure i rozwiązaniu niektórych optymalizacji wydajności, które mogą być wymagane przez platformę Azure.

## <a name="next-steps"></a>Następne kroki

* Zobacz [cennik Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* Rozpoczęcie pracy przez [utworzenie pierwszego serwera](https://review.docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal).

