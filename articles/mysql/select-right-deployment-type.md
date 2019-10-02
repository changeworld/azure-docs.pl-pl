---
title: Wybieranie odpowiedniego typu wdrożenia dla Azure Database for MySQL
description: W tym artykule opisano zagadnienia, które należy wykonać przed przejściem do infrastruktury jako usługi (IaaS) lub platformy jako usługi (PaaS) dla Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7bcbf379ea8d046c8c477dc716711a6a6ffa1dc9
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813836"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Wybieranie odpowiedniej opcji serwera MySQL na platformie Azure

Na platformie Azure obciążenia serwera MySQL mogą działać w hostowanej maszynie wirtualnej (IaaS) lub jako usługa hostowana (PaaS). PaaS ma wiele opcji wdrażania, a w każdej opcji wdrożenia znajdują się warstwy usług. Podczas decydowania o PaaS lub IaaS należy określić, czy chcesz zarządzać bazą danych, stosować poprawki i tworzyć kopie zapasowe, czy też chcesz delegować te operacje na platformę Azure.</br>

Na podstawie odpowiedzi na to pytanie należy wziąć pod uwagę następujące opcje: <br/>

**Azure Database for MySQL** to w pełni zarządzany aparat bazy danych MySQL oparty na stabilnej wersji Community Edition. Ta relacyjna baza danych jako usługa (DBaaS) hostowana w chmurze platformy Azure znajduje się w kategorii branżowej platformy jako usługi (PaaS). W przypadku wystąpienia zarządzanego programu MySQL na platformie Azure można używać wbudowanych funkcji i funkcji, które wymagają rozbudowanej konfiguracji w przypadku korzystania z serwera MySQL (lokalnie lub na maszynie wirtualnej platformy Azure). W przypadku korzystania z programu MySQL jako usługi możesz skorzystać z opcji w celu skalowania w górę lub w dół w celu uzyskania większej mocy bez przerw w działaniu. Ponadto, w przeciwieństwie do autonomicznego serwera MySQL, Azure Database for MySQL oferuje dodatkowe funkcje, takie jak wbudowana wysoka dostępność, analiza i zarządzanie. <br/><br/>
**Baza danych MySQL na maszynie wirtualnej platformy Azure** znajduje się w kategorii branżowej infrastruktura jako usługa (IaaS) i umożliwia uruchamianie serwera MySQL w ramach w pełni zarządzanej maszyny wirtualnej w chmurze platformy Azure. Wszystkie najnowsze wersje i wersje programu MySQL można zainstalować na maszynie wirtualnej IaaS. Najbardziej znaczącą różnicą od Azure Database for MySQL jest to, że program MySQL na maszynach wirtualnych platformy Azure umożliwia kontrolę nad aparatem bazy danych. Jednak ten formant jest kosztem dodatkowej odpowiedzialności za zarządzanie maszynami wirtualnymi i wielu zadań DBA, takich jak Konserwacja serwera bazy danych/stosowanie poprawek, projekt odzyskiwania i wysokiej dostępności itd.

Główne różnice między tymi opcjami są wymienione w poniższej tabeli:

|            | **Azure Database for MySQL** | **Baza danych MySQL na maszynach wirtualnych platformy Azure**    |
|:-------------------|:-----------------------------|:--------------------|
| **USŁUG**                | Oferuje umowę SLA na 99,99% dostępności| Do 99,95% dostępności z co najmniej 2 wystąpieniem w tym samym zestawie dostępności. <br/>99,9% maszyny wirtualnej z jednym wystąpieniem przy użyciu usługi Premium Storage <br/> 99,99% ze strefą dostępności z co najmniej 2 wystąpieniem w 2 lub większym zestawie dostępności.<br/> Uwaga — [Umowa SLA dotycząca maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **Stosowanie poprawek systemu operacyjnego**        | Automatyczne  | Zarządzane przez klientów |
|**Stosowanie poprawek MySQL**     | Automatyczne  | Zarządzane przez klientów |
| **Wysoka dostępność** | Model wysokiej dostępności (HA) jest oparty na wbudowanych mechanizmach trybu failover w przypadku wystąpienia przerwy na poziomie węzła. W takich przypadkach usługa automatycznie tworzy wystąpienie i dołącza magazyn do nowego wystąpienia. | Wysoka dostępność jest poddawana architekturze, implementowana, przetestowana i obsługiwana przez klienta. Może to obejmować zawsze włączone (klaster trybu failover lub replikacja grupowa), wysyłanie dziennika i replikację transakcyjną, w zależności od używanej wersji aparatu MySQL.|
| **Nadmiarowość stref** | Obecnie nie jest obsługiwane. | Maszyny wirtualne platformy Azure można skonfigurować do uruchamiania w różnych strefach dostępności. W przypadku rozwiązania lokalnego klienci mogą tworzyć i obsługiwać własne pomocnicze centrum danych oraz zarządzać nimi.|
| **Scenariusze hybrydowe** | [Replikacja typu Data-in](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) pozwala synchronizować dane z zewnętrznego serwera MySQL do usługi Azure Database for MySQL. Serwer zewnętrzny może być lokalny, w maszynach wirtualnych lub w usłudze bazy danych hostowanej przez innych dostawców chmury.  <br/> <br/> Funkcja [Read Replica](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) umożliwia replikowanie danych z serwera Azure Database for MySQL (Master) do maksymalnie pięciu serwerów tylko do odczytu (replik) w tym samym regionie świadczenia usługi Azure lub w różnych regionach. Repliki tylko do odczytu są asynchronicznie aktualizowane przy użyciu technologii replikacji binlog.   <br/> <br/> Uwaga — replikacja odczytu między regionami jest obecnie dostępna w publicznej wersji zapoznawczej.| Zarządzane przez klientów <br/>
| **Tworzenie kopii zapasowej i przywracanie** | Automatycznie tworzy [kopie zapasowe serwera](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) i zapisuje je w skonfigurowanym przez użytkownika miejscu magazynu lokalnie nadmiarowego lub geograficznie nadmiarowego. Usługa przyjmuje kopie zapasowe z pełnymi, różnicami i dziennikami transakcji | Zarządzane przez klientów |
| **Operacje monitorowania bazy danych** | Umożliwia klientom [Ustawianie alertów](https://docs.microsoft.com/azure/mysql/concepts-monitoring) dotyczących operacji w bazie danych i działanie po osiągnięciu progów. | Zarządzane przez klientów |
| **Zaawansowana ochrona wątków** | Zapewnia [zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal) , która wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. | Klienci muszą kompilować się dla siebie.
| **Kopie zapasowe (odzyskiwanie po awarii)** | Przechowuje zautomatyzowane kopie zapasowe w ramach użytkownika skonfigurowanego [lokalnie nadmiarowy lub geograficznie nadmiarowy](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal). Kopie zapasowe mogą być również używane do przywracania serwera do punktu w czasie. Okres przechowywania można ustawić od 7-35 dni. Przywracanie można wykonać przy użyciu Azure Portal. <br/> | W pełni zarządzane przez klienta, w tym m.in. Planowanie, testowanie, archiwizowanie, przechowywanie i przechowywanie. Dodatkową opcją jest użycie usługi Azure Recovery Services magazyn do tworzenia kopii zapasowych maszyn wirtualnych i baz danych platformy Azure na maszynach wirtualnych (w wersji zapoznawczej). |
| **Zalecenie dotyczące wydajności** | Zapewnia klientom [zalecenia dotyczące wydajności](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) proaktywnej na podstawie danych telemetrycznych użycia, które ułatwiają Optymalizowanie obciążeń. | Zarządzane przez klientów |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motywacje biznesowe do wyboru PaaS lub IaaS

Istnieje kilka czynników, które mogą mieć wpływ na decyzję, aby wybrać PaaS lub IaaS do hostowania baz danych MySQL:

### <a name="cost"></a>Koszt

Niezależnie od tego, czy jesteś strapped startowym dla środków pieniężnych, czy zespołu w firmie, która działa pod kątem ścisłych ograniczeń budżetowych, ograniczone finansowanie jest często podstawowym zagadnieniem podczas określania najlepszego rozwiązania do hostowania baz danych. W tej sekcji opisano podstawowe informacje dotyczące rozliczeń i licencjonowania na platformie Azure w odniesieniu do Azure Database for MySQL i MySQL na maszynach wirtualnych platformy Azure:

#### <a name="billing"></a>Godzin

Obecnie Azure Database for MySQL jest dostępna jako usługa w kilku warstwach z różnymi cenami dla zasobów, dla których wszystkie są rozliczane godzinowo według ustalonej stawki. Najnowsze informacje dotyczące bieżących obsługiwanych warstw usług, rozmiarów obliczeń i ilości miejsca do magazynowania można znaleźć w temacie [model zakupów oparty na rdzeń wirtualny](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Możesz dynamicznie dostosować warstwy usług i rozmiary obliczeń, aby odpowiadały potrzebom różnych przepływności aplikacji. Opłaty są naliczane za wychodzący ruch internetowy przy zwykłych [stawkach za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/).

W przypadku Azure Database for MySQL oprogramowanie bazy danych jest automatycznie konfigurowane, poprawiane i uaktualniane przez firmę Microsoft, co zmniejsza koszty administracyjne. Ponadto [wbudowane funkcje tworzenia kopii zapasowych](https://docs.microsoft.com/azure/mysql/concepts-backup) pomagają zapewnić znaczne oszczędności, zwłaszcza w przypadku dużej liczby baz danych. Za pomocą programu MySQL na maszynach wirtualnych platformy Azure Możesz wybrać i uruchomić dowolną wersję programu MySQL. Niezależnie od używanej wersji programu MySQL płacisz za zainicjowaną maszynę wirtualną i koszty dla określonego typu licencji używanego dla programu MySQL.

Azure Database for MySQL zapewnia wysoką dostępność dowolnego rodzaju przerw na poziomie węzła i nadal utrzymuje 99,99% umowy SLA dla usługi. Jednak w przypadku wysokiej dostępności bazy danych (HA) w ramach maszyn wirtualnych klient powinien przejść przez opcje wysokiej dostępności dostępne w bazie danych MySQL, takie jak [replikacja MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html). Korzystanie z obsługiwanej opcji wysokiej dostępności nie zapewnia dodatkowej umowy SLA, ale umożliwia uzyskanie > 99,99% dostępności bazy danych z dodatkowym kosztem i narzutem administracyjnym.

Aby uzyskać więcej informacji na temat cen, zobacz następujące zasoby:
* [Cennik Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* [Cennik maszyny wirtualnej](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administracja

W przypadku wielu firm decyzja o przejściu do usługi w chmurze polega na tym, że jest to bardzo dużo na odciążeniu złożoności administracji. Dzięki IaaS i PaaS, firma Microsoft administruje podstawową infrastrukturą i automatycznie replikuje wszystkie dane w celu zapewnienia odzyskiwania po awarii, konfiguruje i uaktualnia oprogramowanie bazy danych, zarządza równoważeniem obciążenia i działa w trybie failover, jeśli istnieje Błąd serwera.

* **Za pomocą Azure Database for MySQL**można nadal administrować swoją bazą danych, ale nie musisz już zarządzać aparatem bazy danych, systemem operacyjnym ani sprzętem. Przykłady elementów, które można nadal administrować, obejmują bazy danych i logowania, dostrajanie indeksów i zapytań oraz inspekcję i zabezpieczenia. Ponadto Konfigurowanie wysokiej dostępności w innym centrum danych wymaga konfiguracji minimalnej lub bez niej.<br/><br/>
* **Za pomocą programu MySQL na maszynach wirtualnych platformy Azure**masz pełną kontrolę nad konfiguracją systemu operacyjnego i wystąpienia serwera MySQL. W przypadku maszyny wirtualnej należy zdecydować, kiedy należy zaktualizować/uaktualnić system operacyjny i oprogramowanie bazy danych oraz kiedy zainstalować dodatkowe oprogramowanie, np. aplikację antywirusową. Niektóre funkcje automatyczne zapewniają znacznie uproszczenie stosowania poprawek, kopii zapasowych i wysokiej dostępności. Ponadto można kontrolować rozmiar maszyny wirtualnej, liczbę dysków i ich konfiguracje magazynu. Aby uzyskać więcej informacji, zobacz rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure.

### <a name="time-to-move-to-azure-br"></a>Czas przejścia na platformę Azure <br/>
* **Azure Database for MySQL** to odpowiednie rozwiązanie dla aplikacji zaprojektowanych w chmurze, gdy produktywność deweloperów i krótki czas na rynek dla nowych rozwiązań jest krytyczny. Dzięki funkcjom programistycznym opartym na usłudze SYSDBA usługa jest idealna dla architektów i deweloperów rozwiązań w chmurze, ponieważ obniża potrzebę zarządzania bazowym systemem operacyjnym i bazą danych.<br/><br/>
* Program **MySQL na maszynach wirtualnych platformy Azure** doskonale nadaje się do istniejących lub nowych aplikacji, które wymagają bazy danych MySQL lub dostępu do funkcji w bazie danych MySQL w systemie Windows/Linux i chcesz uniknąć czasu i kosztów nabycia nowego sprzętu lokalnego. Ta opcja jest również przydatna w przypadku migrowania istniejących lokalnych aplikacji i baz danych na platformę Azure zgodnie z oczekiwaniami — w przypadkach, w których wystąpienie Azure Database for MySQL nie jest dobrym rozwiązaniem. Ponieważ nie ma potrzeby zmieniania warstw prezentacji, aplikacji i danych, możesz zaoszczędzić czas i budżet w przypadku ponownego tworzenia architektury istniejącego rozwiązania. Zamiast tego można skupić się na migrowaniu wszystkich rozwiązań na platformę Azure i rozwiązaniu niektórych optymalizacji wydajności, które mogą być wymagane przez platformę Azure.

## <a name="next-steps"></a>Następne kroki

* Zobacz [cennik Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* Zacznij od [utworzenia pierwszego serwera](https://review.docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).

