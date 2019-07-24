---
title: Odczytaj repliki w Azure Database for MariaDB
description: W tym artykule opisano Odczytaj repliki dla Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: e6bbe15727a6f989d8c16c67591d39d7870d5708
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874894"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Odczytaj repliki w Azure Database for MariaDB

Funkcja Read Replica umożliwia replikowanie danych z serwera Azure Database for MariaDB do serwera tylko do odczytu. Można replikować z serwera głównego do maksymalnie pięciu replik. Repliki są aktualizowane asynchronicznie przy użyciu opartej na lokalizacji pliku dziennika binarnego (binlog) aparatu MariaDB z globalnym IDENTYFIKATORem transakcji (GTID). Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji binlog](https://mariadb.com/kb/en/library/replication-overview/).

> [!IMPORTANT]
> Replikę odczytu można utworzyć w tym samym regionie, w którym znajduje się serwer główny, lub w dowolnym innym wybranym regionie świadczenia usługi Azure. Repliki odczytu (ten sam region i wiele regionów) są obecnie dostępne w publicznej wersji zapoznawczej.

Repliki to nowe serwery, którymi można zarządzać podobnie jak regularne Azure Database for MariaDB serwery. Dla każdej repliki odczytu są naliczane opłaty za zasoby obliczeniowe rdzeni wirtualnych i magazyn w GB/miesiąc.

Więcej informacji na temat replikacji GTID można znaleźć w dokumentacji dotyczącej [replikacji MariaDB](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>Kiedy używać repliki odczytu

Funkcja odczytu repliki pomaga zwiększyć wydajność i skalowalność obciążeń intensywnie korzystających z odczytu. Obciążenia odczytu mogą być odizolowane dla replik, podczas gdy obciążenia zapisu mogą być kierowane do wzorca.

Typowy scenariusz polega na tym, że obciążenia analizy biznesowej i analizy używają repliki odczytu jako źródła danych do raportowania.

Ponieważ repliki są tylko do odczytu, nie zmniejszają bezpośrednio obciążeń związanych z pojemnością zapisu na serwerze głównym. Ta funkcja nie jest przeznaczona dla obciążeń intensywnie korzystających z pisania.

Funkcja odczytu repliki korzysta z replikacji asynchronicznej. Ta funkcja nie jest przeznaczona do scenariuszy replikacji synchronicznej. Nastąpi wymierne opóźnienie między serwerem głównym a repliką. Dane z repliki ostatecznie staną się spójne z danymi na serwerze głównym. Użyj tej funkcji dla obciążeń, które mogą obsłużyć to opóźnienie.

Odczyt replik może usprawnić plan odzyskiwania po awarii. Jeśli wystąpi awaria regionalna, a serwer główny jest niedostępny, można skierować obciążenie do repliki w innym regionie. Aby to zrobić, najpierw Zezwól na zapisanie przez replikę za pomocą funkcji Zatrzymaj replikację. Następnie można przekierować aplikację, aktualizując parametry połączenia. Więcej informacji znajduje się w sekcji [Zatrzymaj replikację](#stop-replication) .

## <a name="create-a-replica"></a>Tworzenie repliki

Jeśli serwer główny nie ma istniejących serwerów repliki, wzorzec zostanie najpierw uruchomiony ponownie w celu samodzielnego przygotowania do replikacji.

Po uruchomieniu przepływu pracy tworzenia repliki zostanie utworzony pusty serwer Azure Database for MariaDB. Nowy serwer jest wypełniony danymi znajdującymi się na serwerze głównym. Czas utworzenia zależy od ilości danych na serwerze głównym oraz czasu od ostatniego cotygodniowej pełnej kopii zapasowej. Czas może się wahać od kilku minut do kilku godzin.

> [!NOTE]
> Jeśli nie masz skonfigurowanego alertu magazynu na serwerach, zalecamy wykonanie tej czynności. Alert informuje o tym, kiedy serwer zbliża się do limitu magazynu, co wpłynie na replikację.

Dowiedz się [, jak utworzyć replikę odczytu w Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Nawiązywanie połączenia z repliką

Podczas tworzenia repliki nie są dziedziczone reguły zapory ani punkt końcowy usługi sieci wirtualnej serwera głównego. Te reguły należy skonfigurować niezależnie dla repliki.

Replika dziedziczy konto administratora z serwera głównego. Wszystkie konta użytkowników na serwerze głównym są replikowane do replik odczytu. Można nawiązać połączenie z repliką odczytu tylko przy użyciu kont użytkowników, które są dostępne na serwerze głównym.

Możesz połączyć się z repliką przy użyciu nazwy hosta i prawidłowego konta użytkownika, tak jak w przypadku zwykłego serwera Azure Database for MariaDB. W przypadku serwera o nazwie Moja replika z nazwą administratora administrator można nawiązać połączenie z repliką przy użyciu interfejsu wiersza polecenia MySQL:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

W wierszu polecenia wprowadź hasło dla konta użytkownika.

## <a name="monitor-replication"></a>Monitoruj replikację

Azure Database for MariaDB zapewnia wartość **opóźnienia replikacji w sekundach** w Azure monitor. Ta Metryka jest dostępna tylko dla replik.

Ta Metryka jest obliczana przy `seconds_behind_master` użyciu metryki dostępnej w `SHOW SLAVE STATUS` MariaDB polecenia.

Ustaw Alert, aby poinformować Cię, gdy zwłoka replikacji osiągnie wartość, która nie jest akceptowalna dla obciążenia.

## <a name="stop-replication"></a>Zatrzymaj replikację

Można zatrzymać replikację między serwerem głównym a repliką. Po zatrzymaniu replikacji między serwerem głównym a repliką odczytu replika stanie się serwerem autonomicznym. Dane na serwerze autonomicznym to dane, które były dostępne w replice w momencie uruchomienia polecenia Zatrzymaj replikację. Serwer autonomiczny nie jest przechwytywany z serwerem głównym.

Gdy zdecydujesz się zatrzymać replikację do repliki, utraci ona wszystkie linki do poprzedniego wzorca i innych replik. Między wzorcem a jego repliką nie ma automatycznej pracy awaryjnej.

> [!IMPORTANT]
> Serwer autonomiczny nie może zostać ponownie utworzony w replice.
> Przed zatrzymaniem replikacji w replice odczytu upewnij się, że replika ma wszystkie wymagane dane.

Dowiedz się, jak [zatrzymać replikację do repliki](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Zagadnienia i ograniczenia

### <a name="pricing-tiers"></a>Warstwy cenowe

Repliki odczytu są obecnie dostępne tylko w warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="master-server-restart"></a>Ponowne uruchamianie serwera głównego

Gdy tworzysz replikę dla wzorca, który nie ma istniejących replik, wzorzec zostanie najpierw uruchomiony ponownie w celu przygotowania się do replikacji. Weź pod uwagę i wykonaj te operacje w okresie poza szczytem.

### <a name="new-replicas"></a>Nowe repliki

Replika odczytu jest tworzona jako nowy serwer Azure Database for MariaDB. Nie można wykonać istniejącego serwera w replice. Nie można utworzyć repliki innej repliki odczytu.

### <a name="replica-configuration"></a>Konfiguracja repliki

Replika jest tworzona przy użyciu tej samej konfiguracji serwera co serwer główny. Po utworzeniu repliki kilka ustawień można zmienić niezależnie od serwera głównego: generowanie obliczeń, rdzeni wirtualnych, magazyn, okres przechowywania kopii zapasowej i wersja aparatu MariaDB. Warstwę cenową można także zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub z niej.

> [!IMPORTANT]
> Przed zaktualizowaniem konfiguracji serwera głównego do nowych wartości, zaktualizuj konfigurację repliki do wartości równej lub wyższej. Ta akcja zapewnia, że replika może być zachowywana wraz ze wszystkimi zmianami wprowadzonymi do wzorca.

### <a name="stopped-replicas"></a>Repliki zatrzymane

Jeśli zatrzymasz replikację między serwerem głównym a repliką odczytu, zatrzymana replika stanie się autonomicznym serwerem, który akceptuje oba operacje odczytu i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

### <a name="deleted-master-and-standalone-servers"></a>Usunięto serwery główne i autonomiczne

Po usunięciu serwera głównego replikacja zostaje zatrzymana dla wszystkich replik odczytu. Te repliki stają się serwerami autonomicznymi. Sam serwer główny jest usuwany.

### <a name="user-accounts"></a>Konta użytkowników

Użytkownicy na serwerze głównym są replikowana do replik odczytu. Można nawiązać połączenie z repliką odczytu tylko przy użyciu kont użytkowników dostępnych na serwerze głównym.

### <a name="server-parameters"></a>Parametry serwera

Aby zapobiec synchronizacji danych i uniknąć utraty lub uszkodzenia danych, niektóre parametry serwera są blokowane przed aktualizacją podczas korzystania z replik odczytu.

Następujące parametry serwera są blokowane na serwerach głównych i repliki:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

[`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) Parametr jest zablokowany na serwerach repliki.

### <a name="other"></a>Inne

- Tworzenie repliki repliki nie jest obsługiwane.
- Tabele w pamięci mogą spowodować, że repliki nie zostaną zsynchronizowane. Jest to ograniczenie technologii replikacji MariaDB.
- Upewnij się, że tabele serwera głównego mają klucze podstawowe. Brak kluczy podstawowych może spowodować opóźnienia replikacji między serwerem głównym a replikami.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak tworzyć repliki odczytu i zarządzać nimi przy użyciu Azure Portal](howto-read-replicas-portal.md)
