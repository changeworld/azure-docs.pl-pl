---
title: Grupy trybu failover — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Automatyczny tryb failover grupy jest funkcją bazy danych SQL, która umożliwia zarządzanie replikacji i trybu failover automatyczne / skoordynowanego grupy baz danych na serwerze bazy danych SQL lub wszystkich baz danych w wystąpieniu zarządzanym.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 203d81f3d4872b249103429dde3fcac365fa5753
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483390"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Używanie grup automatyczny tryb failover do włączenia przejrzyste i skoordynowany trybu failover wielu baz danych

Automatyczny tryb failover grupy jest funkcją bazy danych SQL, która umożliwia zarządzanie replikacji i trybu failover dla grupy baz danych na serwerze bazy danych SQL lub wszystkich baz danych w wystąpieniu zarządzanym w innym regionie (obecnie dostępna w publicznej wersji zapoznawczej dla wystąpienia zarządzanego). Używa tych samych podstawowych technologii jako [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md). Należy ręcznie zainicjować trybu failover, lub możesz go delegować do usługi SQL Database, na podstawie zasad zdefiniowanych przez użytkownika. Tę druga opcję umożliwia automatyczne odzyskiwanie wielu powiązanych baz danych w regionie pomocniczym oddalonym po poważnej awarii lub innych nieplanowanego zdarzenia, które powoduje utratę pełnej lub częściowej dostępności usługi SQL Database w regionie podstawowym. Ponadto można użyć odczytu pomocniczych baz danych w celu odciążenia obciążeń związanych z zapytaniami tylko do odczytu. Ponieważ grupy automatyczny tryb failover obejmują wiele baz danych, te bazy danych musi być skonfigurowany na serwerze podstawowym. Podstawowych i pomocniczych serwerów baz danych w grupie trybu failover musi być w tej samej subskrypcji. Automatyczny tryb failover grupy obsługuje replikację wszystkich baz danych w grupie, aby tylko jeden serwer pomocniczy w innym regionie.

> [!NOTE]
> Korzystanie z jednego lub pulami baz danych na serwerze bazy danych SQL wielu pomocnicze bazy danych w tej samej lub różnych regionach, należy zastosować [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md).

Jeśli używasz grup automatyczny tryb failover zasady automatycznej pracy awaryjnej, ewentualnej awarii, która ma wpływ na co najmniej kilka baz danych w grupie skutkuje automatycznej pracy awaryjnej. Ponadto automatyczny tryb failover grupy zapewniają odczytu i zapisu oraz punktów końcowych odbiornika tylko do odczytu, które pozostają bez zmian podczas pracy w trybie Failover. Czy korzystasz z aktywacji ręcznego lub automatycznego trybu failover, pracy awaryjnej przełącza wszystkie pomocnicze bazy danych w grupie do podstawowej. Po zakończeniu pracy w trybie failover bazy danych rekord DNS jest automatycznie aktualizowana do przekierowania punktów końcowych w nowym regionie. Dla określonych danych RPO i RTO, zobacz [omówienie ciągłości](sql-database-business-continuity.md).

Jeśli używasz grup automatyczny tryb failover zasady automatycznej pracy awaryjnej, ewentualnej awarii, który ma wpływ na baz danych na serwerze bazy danych SQL Database lub wystąpienia wynikiem automatycznej pracy awaryjnej. Można zarządzać za pomocą grupy automatyczny tryb failover:

- [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [PowerShell: Grupy trybu failover](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [INTERFEJS API REST: Grupy trybu failover](https://docs.microsoft.com/rest/api/sql/failovergroups).

Po przejściu w tryb failover upewnij się, że wymagania dotyczące uwierzytelniania dla serwera i bazy danych są skonfigurowane na nową podstawową. Aby uzyskać więcej informacji, zobacz [zabezpieczeń bazy danych SQL Database po awarii](sql-database-geo-replication-security-config.md).

Aby osiągnąć rzeczywistych ciągłości działania, dodawanie nadmiarowość bazy danych między centrami danych jest tylko część rozwiązania. Odzyskiwanie aplikacji (usługa) end-to-end, po poważnej awarii wymaga odzyskiwania wszystkich składników wchodzących w skład usługi i usług zależnych. Przykładami tych składników oprogramowania klienta (na przykład przeglądarka za pomocą niestandardowych skryptów JavaScript), frontonów sieci web, magazynu i DNS. Należy koniecznie wszystkie składniki są odporne na awarie tych samych i stają się dostępne w ramach cel czasu odzyskiwania (RTO) aplikacji. W związku z tym należy zidentyfikować wszystkich zależnych usług i zrozumieć gwarancje i możliwości, jakie oferują. Następnie należy wykonać odpowiednie kroki w celu zapewnienia, że funkcje usługi podczas pracy awaryjnej usługi, od których zależy. Aby uzyskać więcej informacji na temat projektowania rozwiązań do odzyskiwania po awarii, zobacz [projektowania rozwiązań w chmurze dla za pomocą odzyskiwania po awarii aktywnej replikacji geograficznej](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Automatyczny tryb failover grupy terminologii i możliwości

- **Grupy trybu failover**

  Grupy trybu failover jest grupą baz danych zarządzanych przez jeden serwer bazy danych SQL lub w ramach jednego wystąpienia zarządzanego, który przełączanie do trybu failover jako jednostka do innego regionu w przypadku wszystkich lub niektórych podstawowych baz danych staną się niedostępne z powodu awarii w regionie podstawowym.

  - **Serwery usługi SQL Database**

     Na serwerze bazy danych SQL niektórych lub wszystkich baz danych użytkownika na jednym serwerze bazy danych SQL można umieścić w grupie trybu failover. Serwer SQL Database obsługuje także wiele grup trybu failover na jednym serwerze bazy danych SQL.

  - **Wystąpienia zarządzanego**
  
     Za pomocą wystąpienia zarządzanego grupy trybu failover zawiera wszystkich baz danych użytkownika w wystąpieniu zarządzanym, i w związku z tym wystąpienie zarządzane obsługuje tylko grupy trybu failover jednym.

- **Podstawowy**

  Serwer usługi SQL Database lub wystąpienia zarządzanego, który jest hostem podstawowych baz danych w grupie trybu failover.

- **Pomocniczy**

  Serwer usługi SQL Database lub wystąpienia zarządzanego, który jest hostem pomocniczych baz danych w grupie trybu failover. Pomocniczy nie może należeć do tego samego regionu podstawowego.

- **Dodawanie pojedynczych baz danych do grupy trybu failover**

  Można umieścić kilka pojedynczych baz danych na tym samym serwerze bazy danych SQL, w tej samej grupy trybu failover. Jeśli dodasz pojedynczej bazy danych do grupy trybu failover, automatycznie tworzy pomocniczą bazę danych przy użyciu tego samego rozmiaru edition i mocy obliczeniowej na serwerze pomocniczym.  Ten serwer jest określona, podczas tworzenia grupy trybu failover. Po dodaniu bazy danych, która ma już pomocniczej bazy danych serwera pomocniczego, ten link replikacji geograficznej jest dziedziczona przez grupę. Po dodaniu bazy danych, która ma już pomocniczej bazy danych na serwerze, który nie jest częścią grupy pracy awaryjnej, nowym serwerem pomocniczym jest tworzony w serwerze pomocniczym.
  
> [!IMPORTANT]
  > W wystąpieniu zarządzanym replikacja wszystkich baz danych użytkownika. Nie można wybrać podzbiór baz danych użytkownika na potrzeby replikacji w grupie trybu failover.

- **Dodawanie baz danych w elastycznej puli do grupy trybu failover**

  W tej samej grupy trybu failover, można umieścić wszystkie lub niektóre bazy danych w puli elastycznej. Jeśli podstawowa baza danych znajduje się w puli elastycznej, pomocnicza jest automatycznie tworzony w elastycznej puli o takiej samej nazwie (Pula dodatkowej). Należy się upewnić, że serwer pomocniczy zawiera pula elastyczna o takiej samej nazwie dokładnie i wystarczającą ilość wolnego zdolność do obsługi dodatkowych baz danych, utworzonych przez grupę trybu failover. Po dodaniu bazy danych w puli, która ma już pomocniczej bazy danych w puli dodatkowej ten link replikacji geograficznej jest dziedziczona przez grupę. Po dodaniu bazy danych, która ma już pomocniczej bazy danych na serwerze, który nie jest częścią grupy pracy awaryjnej, nowym serwerem pomocniczym jest tworzony w dodatkowej puli.
  
  - **Odbiornik odczytu i zapisu grupy trybu failover**

  Rekord CNAME w systemie DNS sformułowany wskazujący na bieżący podstawowy adres URL. Umożliwia aplikacji SQL odczytu i zapisu przezroczyste ponownie nawiązać połączenie z podstawowej bazy danych podstawowego zmianie po włączeniu trybu failover.

  - **Serwer usługi SQL Database rekordu CNAME systemu DNS dla odbiornika do odczytu i zapisu**

     Na serwerze bazy danych SQL, rekordu CNAME systemu DNS dla grupy trybu failover, wskazujący na bieżący podstawowy adres URL został utworzony jako `failover-group-name.database.windows.net`.

  - **Zarządzane rekordu CNAME systemu DNS wystąpienia dla odbiornika do odczytu i zapisu**

     W wystąpieniu zarządzanym, rekordu CNAME systemu DNS dla grupy trybu failover, wskazujący na bieżący podstawowy adres URL został utworzony jako `failover-group-name.zone_id.database.windows.net`.

- **Odbiornik grupy trybu failover tylko do odczytu**

  Rekord CNAME w systemie DNS sformułowany wskazujący odbiornika tylko do odczytu, wskazujący na adres URL pomocniczej. Umożliwia aplikacji SQL tylko do odczytu do jawnego połączenia do regionu pomocniczego, za pomocą określonych reguł równoważenia obciążenia.

  - **Serwer usługi SQL Database rekordu CNAME systemu DNS dla odbiornika tylko do odczytu**

     Na serwerze bazy danych SQL, rekordu CNAME systemu DNS dla odbiornika tylko do odczytu, wskazujący na adres URL pomocniczy został utworzony jako `failover-group-name.secondary.database.windows.net`.

  - **Zarządzane rekordu CNAME systemu DNS wystąpienia dla odbiornika tylko do odczytu**

     W wystąpieniu zarządzanym, rekordu CNAME systemu DNS dla odbiornika tylko do odczytu, wskazujący na adres URL pomocniczy został utworzony jako `failover-group-name.zone_id.database.windows.net`.

- **Zasady automatycznej pracy awaryjnej**

  Domyślnie grupy trybu failover skonfigurowano zasady automatycznej pracy awaryjnej. Usługa SQL Database wyzwala trybu failover, po wykryciu awarii i upłynął okres prolongaty. System musi sprawdzić, czy awaria nie da się ograniczyć przez wbudowane [infrastruktury wysokiej dostępności usługi SQL Database](sql-database-high-availability.md) ze względu na skalę wpływ. Jeśli chcesz kontrolować przepływ pracy trybu failover z aplikacji, można wyłączyć automatyczną pracę awaryjną.

- **Zasady trybu failover tylko do odczytu**

  Domyślnie pracy awaryjnej odbiornika tylko do odczytu jest wyłączona. Zapewnia, że wydajność podstawowej nie ulega zmianie gdy pomocnicza jest w trybie offline. Jednak również oznacza, że sesji tylko do odczytu nie będzie można połączyć, dopóki pomocnicza została odzyskana. Jeśli nieakceptowalne przestojów dla sesji tylko do odczytu i OK, aby tymczasowo używać podstawowej dla ruchu tylko do odczytu i odczytu / zapisu, kosztem potencjalnego obniżenia wydajności podstawowych, można włączyć trybu failover dla odbiornika tylko do odczytu. W takiej sytuacji ruch tylko do odczytu zostanie automatycznie przekierowywane do podstawowego Jeśli pomocnicza nie jest dostępna.

- **Planowany tryb failover**

   Planowanego trybu failover wykonuje pełną synchronizację między podstawowych i pomocniczych baz danych przed pomocnicze przełączników do roli głównej. Jest to gwarancją bez utraty danych. Planowany tryb failover jest używany w następujących scenariuszach:

  - Wykonaj odzyskiwanie po awarii w środowisku produkcyjnym po utracie danych nie jest dopuszczalne
  - Przeniesienie bazy danych do innego regionu
  - Przywrócić bazy danych do regionu podstawowego, po awarii skorygowane (powrót po awarii).

- **Nieplanowany tryb failover**

   Wymuszone lub nieplanowanego trybu failover przełączniki natychmiast pomocniczej do roli podstawowej bez żadnej synchronizacji z podstawowym. Ta operacja spowoduje utratę danych. Nieplanowany tryb failover jest używany jako metoda odzyskiwania podczas awarii podstawowy jest niedostępny. Po powrocie do trybu online oryginalnej podstawowej będzie automatycznie połączyć się ponownie bez synchronizacji i stanie się nowym serwerem pomocniczym.

- **Ręczna praca awaryjna**

  Należy ręcznie zainicjować trybu failover, w dowolnym momencie, niezależnie od konfiguracji automatycznej pracy awaryjnej. Jeśli nie skonfigurowano zasady automatycznej pracy awaryjnej, ręczna praca awaryjna jest wymagany do odzyskiwania baz danych w grupie trybu failover do regionu pomocniczego. Możesz zainicjować wymuszonego lub przyjazna trybu failover (z pełnej synchronizacji danych). One może służyć do przeniesienia podstawowego do regionu pomocniczego. Po zakończeniu trybu failover rekordy DNS są automatycznie aktualizowane do zapewnienia łączności do nowej podstawowej

- **Okres prolongaty z utratą danych**

  Ponieważ podstawowych i pomocniczych baz danych są synchronizowane przy użyciu replikacji asynchronicznej, przełączenie w tryb failover może spowodować utratę danych. Można dostosować zasady automatycznej pracy awaryjnej, aby odzwierciedlić aplikacji tolerancję utraty danych. Konfigurując **GracePeriodWithDataLossHours**, można kontrolować, jak długo system czeka przed przejściem w tryb failover, która prawdopodobnie się wynik utraty danych.

- **Wiele grup trybu failover**

  Można skonfigurować wiele grup trybu failover dla tej samej pary serwerów do kontrolowania skali przejścia w tryb failover. Każda grupa nie powiedzie się za pośrednictwem niezależnie. Jeśli aplikacja wielodostępna używa pul elastycznych, umożliwia ta funkcja mieszać podstawowych i pomocniczych baz danych w każdej puli. W ten sposób można zmniejszyć wpływ awarii połowa dzierżawcy.

  > [!IMPORTANT]
  > Wystąpienie zarządzane nie obsługuje wiele grup trybu failover.
  
## <a name="permissions"></a>Uprawnienia
Uprawnienia dla grupy trybu failover są zarządzane za pośrednictwem [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md). [Współautor serwera SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) rola ma wszystkie uprawnienia wymagane do zarządzania grupy trybu failover. 

### <a name="create-failover-group"></a>Tworzenie grupy trybu failover
Aby utworzyć grupy trybu failover, musisz mieć uprawnienia zapisu RBAC, do podstawowych i pomocniczych serwerów, a także do wszystkich baz danych w grupie trybu failover. Dla wystąpienia zarządzanego musisz mieć uprawnienia zapisu RBAC do obu podstawowych i pomocniczych wystąpienia zarządzanego, ale uprawnienia do poszczególnych baz danych nie są istotne, ponieważ baz danych poszczególne wystąpienia zarządzanego nie dodawane do lub usunięte z grupy trybu failover. 

### <a name="update-a-failover-group"></a>Aktualizowanie grupy trybu failover
Do aktualizacji grupy trybu failover, należy RBAC do zapisu w grupie trybu failover i wszystkimi bazami danych na bieżącym serwerze podstawowym lub wystąpienia zarządzanego.  

### <a name="failover-a-failover-group"></a>Tryb failover grupę trybu failover
Do trybu failover grupę trybu failover, należy do grupy trybu failover w nowym serwerem podstawowym, dostęp do zapisu RBAC lub wystąpienia zarządzanego. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Najlepsze rozwiązania przy użyciu grupy trybu failover przy użyciu pojedynczych baz danych i pul elastycznych

Grupa automatyczny tryb failover musi być skonfigurowany na serwerze podstawowym bazy danych SQL i połącz go na serwer pomocniczy bazy danych SQL w innym regionie platformy Azure.  Grupy mogą obejmować wszystkie lub niektóre bazy danych na tych serwerach. Na poniższym diagramie przedstawiono typową konfigurację aplikacji chmurowych magazynu geograficznie nadmiarowego przy użyciu wielu baz danych i grupy automatyczny tryb failover.

![Automatyczny tryb failover](./media/sql-database-auto-failover-group/auto-failover-group.png)

Podczas projektowania usługi za pomocą ciągłość prowadzenia działalności biznesowej należy pamiętać, należy przestrzegać następujących zasad ogólnych:

- **Użyj jednej lub kilku grup trybu failover można zarządzać trybem failover wielu baz danych**

  Można utworzyć jeden lub wiele grup trybu failover między dwoma serwerami w różnych regionach (podstawowych i pomocniczych serwerów). Każda grupa może zawierać jeden lub kilka baz danych, które mogą zostać odzyskane jako jednostka, w przypadku wszystkich lub niektórych podstawowych baz danych staną się niedostępne z powodu awarii w regionie podstawowym. Grupy trybu failover tworzy pomocniczej geograficznej bazy danych, w tym samym celu usługi jako podstawowy. Jeśli dodasz istniejącej relacji replikacji geograficznej do grupy trybu failover upewnij się, że pomocniczej geograficznej jest skonfigurowany z tej samej warstwy usług i rozmiaru obliczeń jako podstawowy.

- **Użyj odbiornika odczytu i zapisu dla obciążenia OLTP**

  Podczas wykonywania operacji OLTP, użyj `failover-group-name.database.windows.net` jako serwer połączenia i adres URL automatycznie są kierowane do podstawowego. Ten adres URL nie zmienia się po pracy w trybie failover. Należy pamiętać, że przełączenie w tryb failover wymaga aktualizacji, który rekord DNS, dzięki czemu połączenia klienckie są przekierowywane do nowej podstawowej tylko wtedy, gdy klienta pamięci podręcznej DNS są odświeżane.

- **Użyj odbiornika tylko do odczytu dla obciążenia tylko do odczytu**

  Jeśli masz obciążenie logicznie izolowanej tylko do odczytu, która jest odporny na niektórych nieaktualność danych, można użyć pomocniczej bazy danych w aplikacji. W przypadku sesji tylko do odczytu, użyj `failover-group-name.secondary.database.windows.net` jako serwer adresu URL i połączenia automatycznie zostanie skierowany do regionu pomocniczego. Zaleca się wskazanie w parametrach połączenia odczytać intencji za pomocą **ApplicationIntent = tylko do odczytu**.

- **Przygotowanie do obniżenia wydajności**

  Decyzja trybu failover programu SQL jest niezależna od pozostałej części lub innych usług używanych aplikacji. Aplikacja może być wartość "mixed" za pomocą niektóre składniki na jednym regionie, a niektóre w innym. Aby uniknąć obniżenia wydajności, upewnij się, wdrożenie aplikacji nadmiarowy w regionie odzyskiwania po awarii, a następnie postępuj zgodnie z tymi [wytyczne z zakresu bezpieczeństwa sieci](#failover-groups-and-network-security).

  > [!NOTE]
  > Aby użyć parametrów połączenia różnych nie ma aplikacji w regionie odzyskiwania po awarii.  

- **Przygotowanie do utraty danych**

  W przypadku wykrycia awarii SQL oczekiwania przez czas określony przez **GracePeriodWithDataLossHours**. Wartość domyślna to 1 godzina. Jeśli nie możesz zakupić utraty danych, upewnij się, że ustawiony **GracePeriodWithDataLossHours** wystarczająco dużą liczbę, takich jak 24 godziny. Użyj grupy ręcznej pracy awaryjnej niepowodzenie powrót po awarii z pomocniczej do podstawowej.

> [!IMPORTANT]
> Elastycznymi pulami za pomocą 800 lub mniejszej liczby jednostek Dtu i ponad 250 baz danych, przy użyciu replikacji geograficznej mogą wystąpić problemy, w tym już planowanego przejścia w tryb failover i pogorszenie wydajności.  Te problemy są najbardziej prawdopodobne w przypadku obciążeń intensywnie korzystających z zapisu podczas replikacji geograficznej punkty końcowe są znacznie oddalonych od siebie według lokalizacji geograficznej lub wiele dodatkowych punktów końcowych są używane dla każdej bazy danych.  Objawy te problemy są wskazane, gdy opóźnienia replikacji geograficznej zwiększa się wraz z upływem czasu.  To opóźnienie można monitorować za pomocą [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Jeśli te problemy występują, środki zaradcze obejmują zwiększenie liczby jednostek Dtu puli lub zmniejszenie liczby replikacji geograficznej bazy danych w tej samej puli.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Najlepsze rozwiązania przy użyciu grupy trybu failover za pomocą wystąpienia zarządzanego

Grupa automatyczny tryb failover na podstawowe wystąpienie musi być skonfigurowany i podłącz go do dodatkowej wystąpienia w innym regionie platformy Azure.  Wszystkie bazy danych w wystąpieniu będą replikowane do dodatkowej wystąpienia. Na poniższym diagramie przedstawiono typową konfigurację aplikacji chmurowych geograficznie nadmiarowy za pomocą wystąpienia zarządzanego i grupy automatyczny tryb failover.

![Automatyczny tryb failover](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!IMPORTANT]
> Grupy automatyczny tryb failover dla wystąpienia zarządzanego jest w publicznej wersji zapoznawczej.

Jeśli aplikacja używa wystąpienia zarządzanego jako warstwa danych, wykonaj następujące ogólne wytyczne podczas projektowania w celu zachowania ciągłości:

- **Tworzenie dodatkowych wystąpienia w tej samej strefie DNS jako podstawowe wystąpienie**

  Gdy tworzone jest nowe wystąpienie, unikatowy identyfikator jest automatycznie generowane jako strefy DNS i zawarty w nazwę DNS wystąpienia. Wielu domen (SAN) certyfikatu dla tego wystąpienia jest aprowizowana za pomocą pola SAN w formie `zone_id.database.windows.net`. Ten certyfikat może służyć do uwierzytelniania połączeń klientów z wystąpienia usługi w tej samej strefie DNS. Aby zapewnić — przerwana łączność z podstawowego wystąpienia po włączeniu trybu failover w podstawowym i pomocniczym wystąpień musi być w tej samej strefie DNS. Gdy aplikacja jest gotowa do wdrożenia w środowisku produkcyjnym, Utwórz wystąpienie dodatkowej w innym regionie i upewnij się, że współużytkuje strefy DNS przy użyciu podstawowego wystąpienia. Jest to realizowane przez określenie `DNS Zone Partner` opcjonalny parametr przy użyciu witryny Azure portal, programu PowerShell lub interfejsu API REST.

  Aby uzyskać więcej informacji na temat tworzenia dodatkowych wystąpienia w tej samej strefie DNS jako podstawowe wystąpienie zobacz [Zarządzanie grupy trybu failover za pomocą wystąpienia zarządzanego (wersja zapoznawcza)](#powershell-managing-failover-groups-with-managed-instances-preview).

- **Włącz ruch związany z replikacją między dwoma wystąpieniami**

  Ponieważ każde wystąpienie jest odizolowane we własnej sieci wirtualnej, muszą być dozwolone dwa kierunkowego ruch między tymi sieciami wirtualnymi. Zobacz [bramy Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Konfigurowanie grupy trybu failover, aby zarządzać trybem failover całego wystąpienia**

  Grupy trybu failover będzie zarządzać trybem failover dla wszystkich baz danych w wystąpieniu. Po utworzeniu grupy każdej bazy danych w wystąpieniu będzie automatycznie georeplikowanym do wystąpienia usługi dodatkowej. Za pomocą grupy trybu failover nie można zainicjować częściowej trybu failover z podzbiorem baz danych.

  > [!IMPORTANT]
  > Usunięcie bazy danych z wystąpienia elementu podstawowego go zostaną również usunięte automatycznie w wystąpieniu pomocniczej geograficznej.

- **Użyj odbiornika odczytu i zapisu dla obciążenia OLTP**

  Podczas wykonywania operacji OLTP, użyj `failover-group-name.zone_id.database.windows.net` jako serwer połączenia i adres URL automatycznie są kierowane do podstawowego. Ten adres URL nie zmienia się po pracy w trybie failover. Przełączenie w tryb failover obejmuje aktualizowania rekordu DNS, aby połączenia klienckie są przekierowywane do nowej podstawowej tylko wtedy, gdy klienta pamięci podręcznej DNS są odświeżane. Ponieważ wystąpienia dodatkowej udostępni podstawowej strefy DNS, aplikacja kliencka będzie można ponownie połączyć się z nim przy użyciu tego samego certyfikatu SAN.

- **Połączyć się bezpośrednio z dodatkowej bazy danych replikowanej geograficznie zapytań tylko do odczytu**

  Jeśli masz obciążenie logicznie izolowanej tylko do odczytu, która jest odporny na niektórych nieaktualność danych, można użyć pomocniczej bazy danych w aplikacji. Aby połączyć się bezpośrednio z dodatkowej bazy danych replikowanej geograficznie, użyj `server.secondary.zone_id.database.windows.net` jako serwer adresu URL i połączenie staje się bezpośrednio replikowanej geograficznie pomocniczej.

  > [!NOTE]
  > W przypadku niektórych warstw usług Azure SQL Database obsługuje [tylko do odczytu replik](sql-database-read-scale-out.md) załadować równoważenia obciążeń związanych z zapytaniami tylko do odczytu za pomocą pojemność jednej z replik tylko do odczytu i przy użyciu `ApplicationIntent=ReadOnly` parametru w połączeniu ciąg. Po skonfigurowaniu pomocniczej z replikacją geograficzną, można użyć tej funkcji można połączyć się z jednej repliki tylko do odczytu w lokalizacji głównej lub w lokalizacji zreplikowanych geograficznie.
  > - Aby połączyć się z repliką tylko do odczytu w lokalizacji podstawowej, użyj `failover-group-name.zone_id.database.windows.net`.
  > - Aby połączyć z repliki tylko do odczytu w lokalizacji dodatkowej, należy użyć `failover-group-name.secondary.zone_id.database.windows.net`.

- **Przygotowanie do obniżenia wydajności**

  Decyzja trybu failover programu SQL jest niezależna od pozostałej części lub innych usług używanych aplikacji. Aplikacja może być wartość "mixed" za pomocą niektóre składniki na jednym regionie, a niektóre w innym. Aby uniknąć obniżenia wydajności, upewnij się, wdrożenie aplikacji nadmiarowy w regionie odzyskiwania po awarii, a następnie postępuj zgodnie z tymi [wytyczne z zakresu bezpieczeństwa sieci](#failover-groups-and-network-security).

- **Przygotowanie do utraty danych**

  W przypadku wykrycia awarii SQL automatyczne wyzwolenie odczytu i zapisu w tryb failover ma zerową utratę danych, najlepiej naszej wiedzy. W przeciwnym razie oczekiwania przez czas określony przez `GracePeriodWithDataLossHours`. Jeśli określono `GracePeriodWithDataLossHours`, można przygotować utraty danych. Ogólnie rzecz biorąc podczas awarii, Azure preferuje dostępności. Jeśli nie możesz zakupić utraty danych, upewnij się ustawić GracePeriodWithDataLossHours wystarczająco dużą liczbę, takich jak 24 godziny.

  Aktualizacja DNS odbiornika odczytu / zapisu nastąpi natychmiast po zakończeniu pracy w trybie failover jest inicjowane. Ta operacja nie powoduje utraty danych. Jednak przełączania ról bazy danych może potrwać do 5 minut w normalnych warunkach. Dopóki zostanie zakończona, niektóre bazy danych w nowe wystąpienie podstawowego nadal będą tylko do odczytu. Jeśli tryb failover jest inicjowane przy użyciu programu PowerShell, cała operacja jest synchroniczna. Jeśli jest inicjowane z użyciem witryny Azure portal interfejsu użytkownika będzie oznaczać stan ukończenia. Jeśli jest inicjowane, przy użyciu interfejsu API REST, należy użyć mechanizmu sondowania standardowa usługi Azure Resource Manager firmy do monitorowania na zakończenie.

  > [!IMPORTANT]
  > Użyj grupy ręcznej pracy awaryjnej, aby przenieść kolory podstawowe z powrotem do oryginalnej lokalizacji. Po zminimalizowaniu wpływu awarii, które spowodowało przełączenie w tryb failover, można przenieść głównej bazy danych do oryginalnej lokalizacji. Aby to zrobić, należy zainicjować ręczna praca awaryjna grupy.

## <a name="failover-groups-and-network-security"></a>Grupy trybu failover i bezpieczeństwo sieci

W przypadku niektórych aplikacji, które reguły zabezpieczeń wymagają, że dostęp do sieci do warstwy danych jest ograniczony do określonego składnika lub składniki, takie jak maszyny Wirtualnej z systemem usługi sieci web itp. To wymaganie przedstawiono niektóre wyzwania dotyczące projektowania ciągłości biznesowej i sposób korzystania z grup trybu failover. Należy rozważyć następujące opcje podczas implementowania takich ograniczony dostęp.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Korzystanie z reguł sieci wirtualnej i grupy trybu failover

Jeśli używasz [punkty końcowe usługi sieci wirtualnej i reguł](sql-database-vnet-service-endpoint-rule-overview.md) ograniczyć dostęp do bazy danych SQL, należy pamiętać, ten punkt końcowy usługi każdej sieci wirtualnej dotyczy tylko jednego regionu platformy Azure. Punkt końcowy nie uwzględnia innych regionach akceptował komunikację z podsieci. Więc tylko aplikacje klienckie wdrożone w tym samym regionie mogą łączyć się z podstawowej bazy danych. Ponieważ trybu failover zakończyło się z sesji klienta SQL są przekierowane do serwera w innym regionie (informacje pomocnicze), te sesje zakończy się niepowodzeniem, jeśli pochodzi z klienta poza tym regionem. Z tego powodu zasady automatycznej pracy awaryjnej nie można włączyć w przypadku serwerów znajdują się w reguł sieci wirtualnej. Aby zapewnić obsługę ręcznej pracy awaryjnej, wykonaj następujące kroki:

1. Aprowizowanie nadmiarowe kopie składników frontonu w aplikacji (usługa sieci web, maszyny wirtualne itp.) w regionie pomocniczym
2. Konfigurowanie [reguł sieci wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md) osobno dla podstawowego i pomocniczego serwera
3. Włącz [frontonu trybu failover przy użyciu Konfiguracja usługi Traffic manager](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Po wykryciu awarii, należy zainicjować ręcznej pracy awaryjnej. Ta opcja jest zoptymalizowany pod kątem aplikacji, które wymagają spójne opóźnienie między frontonu i warstwy danych i obsługuje funkcję odzyskiwania, gdy frontonu, warstwy danych lub obu wpływ awarii.

> [!NOTE]
> Jeśli używasz **odbiornika tylko do odczytu** aby zrównoważyć obciążenie tylko do odczytu, upewnij się, że to obciążenie jest wykonywane na maszynie Wirtualnej lub innych zasobów w regionie pomocniczym go do połączenia się z dodatkowej bazy danych.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Korzystanie z grup trybu failover i reguł zapory usługi SQL database

Jeśli planem ciągłości biznesowej wymaga trybu failover przy użyciu grup automatycznego przejścia w tryb failover, można ograniczyć dostęp do usługi SQL database przy użyciu reguł zapory tradycyjnych.  Aby zapewnić obsługę automatycznej pracy awaryjnej, wykonaj następujące kroki:

1. [Tworzenie publicznego adresu IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Tworzenie publicznego modułu równoważenia obciążenia](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) i przypisać publiczny adres IP.
3. [Tworzenie sieci wirtualnej i maszynach wirtualnych](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) składników frontonu
4. [Utwórz sieciową grupę zabezpieczeń](../virtual-network/security-overview.md) i skonfigurowanie połączeń przychodzących.
5. Upewnij się, czy połączenia wychodzące są otwarte dla danych Azure SQL database przy użyciu "Sql" [tag usługi](../virtual-network/security-overview.md#service-tags).
6. Tworzenie [reguły zapory bazy danych SQL](sql-database-firewall-configure.md) zezwalająca na ruch przychodzący z publicznego adresu IP, zostanie utworzony w kroku 1.

Aby uzyskać więcej informacji na temat na konfigurowanie wychodzącego dostępu i jakie adres IP do użycia w regułach zapory zobacz [połączeń wychodzących usługi równoważenia obciążenia](../load-balancer/load-balancer-outbound-connections.md).

Powyższa konfiguracja będzie upewnij się, że automatycznej pracy awaryjnej nie blokuje połączenia z składników frontonu i przyjęto założenie, że aplikacja może tolerować dłuższe opóźnienie między frontonem a warstwą danych.

> [!IMPORTANT]
> Aby zagwarantować ciągłość działania w regionalnych przestojów musi zapewnić nadmiarowość geograficzna zarówno składników frontonu, jak i bazy danych.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Włączanie replikacji geograficznej między wystąpienia zarządzanego i sieci wirtualnych

Po skonfigurowaniu grupy trybu failover, od podstawowych i pomocniczych wystąpienia zarządzanego w dwóch różnych regionach, każde wystąpienie jest izolowane, przy użyciu niezależne sieci wirtualnej. Aby zezwolić na ruch związany z replikacją między tymi sieciami wirtualnymi, upewnij się, wykonaj następujące wymagania wstępne są spełnione:

1. Dwa wystąpienia zarządzanego muszą znajdować się w różnych regionach platformy Azure.
2. Drugiej musi być pusta (nie bazy danych użytkownika).
3. Podstawowe i pomocnicze wystąpienia zarządzanego muszą znajdować się w tej samej grupie zasobów.
4. Sieci wirtualnych należących do wystąpienia zarządzane musi być połączone za pośrednictwem [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Globalne wirtualne sieci równorzędne nie jest obsługiwane.
5. Dwie zarządzane wystąpienia sieci wirtualne nie mogą mieć nakładających się adresów IP.
6. Musisz skonfigurować swoje sieci grup zabezpieczeń takich które porty 5022 i zakres 11000 ~ 12000 są otwarte połączenia przychodzącego i wychodzącego dla innych podsieci wystąpienia zarządzanego. To jest, aby zezwolić na ruch związany z replikacją między wystąpieniami

    > [!IMPORTANT]
    > Nieprawidłowo skonfigurowany potencjalnych klientów reguły zabezpieczeń sieciowej grupy zabezpieczeń do operacji kopiowania zablokowane bazy danych.

7. Należy skonfigurować partnera strefy DNS w wystąpieniu dodatkowej. Strefa DNS jest właściwością wystąpienia zarządzanego. Reprezentuje ona częścią nazwy hosta, która następuje nazwa wystąpienia zarządzanego i poprzedza `.database.windows.net` prefiks. Jako losowy ciąg jest generowany podczas tworzenia pierwszego wystąpienia zarządzanego w każdej sieci wirtualnej. Strefa DNS nie można zmodyfikować po utworzeniu wystąpienia zarządzanego, a wszystkie wystąpienia zarządzane w ramach tej samej podsieci mają taką samą wartość strefy DNS. Dla wystąpienia zarządzanego. Konfiguracja grupy trybu failover wystąpienia zarządzanego usługi podstawowej oraz dodatkowej wystąpienia zarządzanego musi mieć taką samą wartość strefy DNS. Można to zrobić, określając parametr DnsZonePartner podczas tworzenia dodatkowych wystąpienia zarządzanego. Właściwość partnera strefy DNS definiuje udostępniania wystąpienia grupy trybu failover z wystąpieniem zarządzanym. Przekazując do innego wystąpienia zarządzanego identyfikatora zasobu jako dane wejściowe DnsZonePartner aktualnie jest tworzone wystąpienie zarządzane dziedziczy taką samą wartość strefy DNS partnera wystąpienia zarządzanego.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uaktualnianie lub zmiany na starszą wersję podstawową bazą danych

Można uaktualnić lub obniżyć podstawowej bazy danych do rozmiaru obliczeniowej (w ramach tej samej warstwy usługi, nie między ogólnego przeznaczenia i krytyczne dla działania firmy) bez odłączania wszystkie pomocnicze bazy danych. Podczas uaktualniania, zalecamy najpierw uaktualnić wszystkie pomocnicze bazy danych, a następnie uaktualnić podstawowy. Przed obniżeniem, odwracanie kolejności: najpierw obniżenia poziomu podstawowego, a następnie obniżyć wszystkie pomocnicze bazy danych. Po uaktualnieniu lub starszą wersję bazy danych do warstwy usług różnych tego zalecenia zostanie wymuszona.

Ta sekwencja jest zalecane specjalnie po to, aby uniknąć tego problemu, gdzie pomocniczego na niższym poziomem jednostki SKU pobiera przeciążona i musi być ponownie wprowadzonych podczas procesu uaktualniania lub starszą. Można także uniknąć problemu, dokonując podstawowego tylko do odczytu, kosztem wpływ na wszystkie obciążenia odczytu i zapisu względem podstawowego. 

> [!NOTE]
> Jeśli utworzono pomocniczej bazy danych jako część konfiguracji grupy trybu failover, który nie jest zalecane w przypadku obniżania pomocniczej bazy danych. To jest, aby upewnić się, że w warstwie danych ma dostatecznie dużą pojemność przetwarzanie regularne obciążenie, po aktywowaniu trybu failover.

## <a name="preventing-the-loss-of-critical-data"></a>Zapobieganie utracie danych o kluczowym znaczeniu

Ze względu na duże opóźnienia sieci rozległej ciągłych kopii używa mechanizmu replikacji asynchronicznej. Replikacja asynchroniczna sprawia, że utrata danych nieuniknione Jeśli wystąpi awaria. Jednak niektóre aplikacje mogą wymagać bez utraty danych. Aby chronić te aktualizacje krytyczne, Twórca aplikacji może wywołać [operacja sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) procedury system natychmiast po zatwierdzanie transakcji. Wywoływanie **operacja sp_wait_for_database_copy_sync** blokuje wątek wywołujący, aż ostatniej transakcji zatwierdzone została przesłana do pomocniczej bazy danych. Jednak go nie czeka na przesyłane transakcji, które mają być powtórzone i zatwierdzona na serwerze pomocniczym. **Operacja sp_wait_for_database_copy_sync** obejmuje łącze określonych ciągłych kopii. Każdy użytkownik z uprawnieniami połączenia podstawowej bazy danych można wywołać tej procedury.

> [!NOTE]
> **Operacja sp_wait_for_database_copy_sync** zapobiega utracie danych po pracy awaryjnej, ale nie gwarantuje pełną synchronizację, aby uzyskać dostęp do odczytu. Opóźnienie spowodowane **operacja sp_wait_for_database_copy_sync** wywołanie procedury mogą być znaczące i zależy od rozmiaru dziennika transakcji w momencie wywołania.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupy trybu failover i w momencie przywracania

Aby dowiedzieć się, jak przy użyciu punktu w czasie przywracania z grupy trybu failover, zobacz [punktu w czasie odzyskiwania (Odzyskiwanie)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Programowe zarządzanie grupy trybu failover

Zgodnie z opisem wcześniej grupy automatyczny tryb failover i aktywna replikacja geograficzna można również zarządzać programowo przy użyciu programu Azure PowerShell i interfejsu API REST. W poniższych tabelach opisano zestaw poleceń dostępnych. Aktywna replikacja geograficzna zawiera zestaw interfejsów API usługi Azure Resource Manager do zarządzania, w tym [interfejs API REST usługi Azure SQL Database](https://docs.microsoft.com/rest/api/sql/) i [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Te interfejsy API korzystają z grup zasobów i obsługuje zabezpieczenia oparte na rolach (RBAC). Aby uzyskać więcej informacji o tym, jak można zaimplementować ról dostępu, zobacz [kontroli dostępu](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Program PowerShell: Zarządzanie za pomocą pojedynczych baz danych i pul elastycznych, tryb failover bazy danych SQL

| Polecenie cmdlet | Opis |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |To polecenie tworzy grupę trybu failover i rejestruje je na serwerach podstawowych i pomocniczych|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Usuwa grupę trybu failover z serwera i usuwa wszystkie pomocnicze bazy danych są uwzględnione grupy |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera konfigurację grupy trybu failover |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modyfikuje konfigurację grupy trybu failover |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Wyzwalacze pracy w trybie failover grupy trybu failover na serwer pomocniczy |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Dodaje jeden lub więcej baz danych do grupy trybu failover usługi Azure SQL Database|
|  | |

> [!IMPORTANT]
> Aby uzyskać przykładowy skrypt, zobacz [Konfiguruj i tryb failover grupę trybu failover dla pojedynczej bazy danych](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

### <a name="powershell-managing-failover-groups-with-managed-instances-preview"></a>Program PowerShell: Zarządzanie grupy trybu failover za pomocą wystąpienia zarządzanego (wersja zapoznawcza)

#### <a name="install-the-newest-pre-release-version-of-powershell"></a>Zainstaluj najnowszą wersję wstępną programu PowerShell

1. Zaktualizuj moduł PowerShellGet 1.6.5 (lub najnowszej wersji zapoznawczej). Zobacz [witryny programu PowerShell w wersji zapoznawczej](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview).

   ```powershell
      install-module PowerShellGet -MinimumVersion 1.6.5 -force
   ```

2. W nowym oknie programu PowerShell wykonaj następujące polecenia:

   ```powershell
      import-module PowerShellGet
      get-module PowerShellGet #verify version is 1.6.5 (or newer)
      install-module azurerm.sql -RequiredVersion 4.5.0-preview -AllowPrerelease –Force
      import-module azurerm.sql
   ```

#### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>Polecenia cmdlet środowiska PowerShell, aby utworzyć grupę trybu failover wystąpienia

| Interfejs API | Opis |
| --- | --- |
| New-AzSqlDatabaseInstanceFailoverGroup |To polecenie tworzy grupę trybu failover i rejestruje je na serwerach podstawowych i pomocniczych|
| Set-AzSqlDatabaseInstanceFailoverGroup |Modyfikuje konfigurację grupy trybu failover|
| Get-AzSqlDatabaseInstanceFailoverGroup |Pobiera konfigurację grupy trybu failover|
| Switch-AzSqlDatabaseInstanceFailoverGroup |Wyzwalacze pracy w trybie failover grupy trybu failover na serwer pomocniczy|
| Remove-AzSqlDatabaseInstanceFailoverGroup | Usuwa grupę trybu failover|

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>INTERFEJS API REST: Zarządzanie grupy trybu failover bazy danych SQL za pomocą jednego i puli baz danych

| Interfejs API | Opis |
| --- | --- |
| [Utwórz lub zaktualizuj grupę trybu Failover](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Tworzy lub aktualizuje grupę trybu failover |
| [Usuwanie grupy trybu Failover](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Usuwa grupę trybu failover z serwera |
| [Praca awaryjna (planowana)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Nie powiedzie się za pośrednictwem z bieżącego serwera podstawowego do tego serwera. |
| [Zezwalaj na utratę danych wymuszenie trybu Failover](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |ails za pośrednictwem z bieżącego serwera podstawowego do tego serwera. Ta operacja może spowodować utratę danych. |
| [Pobierz grupę trybu Failover](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Pobiera grupy trybu failover. |
| [Wyświetlanie listy grup trybu Failover przez serwer](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Wyświetla listę grup trybu failover na serwerze. |
| [Aktualizacja grupy trybu Failover](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Aktualizuje grupę trybu failover. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances-preview"></a>INTERFEJS API REST: Zarządzanie grupy trybu failover przy użyciu wystąpienia zarządzanego (wersja zapoznawcza)

| Interfejs API | Opis |
| --- | --- |
| [Utwórz lub zaktualizuj grupę trybu Failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Tworzy lub aktualizuje grupę trybu failover |
| [Usuwanie grupy trybu Failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Usuwa grupę trybu failover z serwera |
| [Praca awaryjna (planowana)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Nie powiedzie się za pośrednictwem z bieżącego serwera podstawowego do tego serwera. |
| [Zezwalaj na utratę danych wymuszenie trybu Failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |ails za pośrednictwem z bieżącego serwera podstawowego do tego serwera. Ta operacja może spowodować utratę danych. |
| [Pobierz grupę trybu Failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Pobiera grupy trybu failover. |
| [Wyświetlanie listy grup trybu Failover — listy według lokalizacji](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Wyświetla listę grup trybu failover w lokalizacji. |

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przykładowe skrypty Zobacz:
  - [Konfigurowanie pojedynczej bazy danych i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Konfigurowanie bazy danych w puli i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Konfigurowanie grupy trybu failover i wprowadzanie jej w tryb failover dla jednej bazy danych](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- Omówienie ciągłości działania i scenariuszach można znaleźć [omówienie ciągłości działania](sql-database-business-continuity.md)
- Aby dowiedzieć się więcej na temat usługi Azure SQL Database automatyczne kopie zapasowe, zobacz [bazy danych SQL, automatyczne kopie zapasowe](sql-database-automated-backups.md).
- Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych do odzyskania, zobacz [przywrócić bazę danych z kopii zapasowych zainicjowanych przez usługę](sql-database-recovery-using-backups.md).
- Aby dowiedzieć się o wymagania dotyczące uwierzytelniania dla nowym serwerem podstawowym i bazy danych, zobacz [zabezpieczeń bazy danych SQL Database po awarii](sql-database-geo-replication-security-config.md).
