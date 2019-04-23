---
title: Repliki do odczytu w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano funkcję odczytu repliki, w usłudze Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: f340f1e42b6993a1f834ab05570c669d4241222b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789978"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Repliki do odczytu w usłudze Azure Database for PostgreSQL

Funkcja odczytu replik pozwala na replikowanie danych z usługi Azure Database for postgresql w warstwie serwera na serwer tylko do odczytu. Można replikować z głównego serwera do maksymalnie pięciu replik w tym samym regionie platformy Azure. Repliki są aktualizowane asynchronicznie przy użyciu technologii replikacji natywnego aparatu PostgreSQL.

Repliki są nowe serwery zarządzania podobne do regularnego — Azure Database for postgresql w warstwie serwerów. Dla każdego odczytu repliki, opłaty są naliczane dla zainicjowanych zasobów obliczeniowych w rdzeniach wirtualnych zasoby i magazynu w GB / miesiąc.

Dowiedz się, jak [tworzenie i zarządzanie nimi replik](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Kiedy należy używać odczytu repliki
Funkcja odczytu replik pomaga zwiększyć wydajność i skalę intensywnie odczytujących obciążeń. Odczyt obciążenia mogą być izolowane w replikach, podczas obciążenia zapisu może zostać skierowany do poziomu głównego.

Typowy scenariusz polega na zapewnienie Power BI i obciążeń analitycznych pełnić odczytu repliki źródła danych do raportowania.

Ponieważ repliki są tylko do odczytu, nie bezpośrednio zmniejszyć obciążenia pojemności zapisu we wzorcu. Ta funkcja nie jest przeznaczone dla obciążeń intensywnie korzystających z zapisu.

Funkcja odczytu replik używa replikacji asynchronicznej PostgreSQL. Ta funkcja nie jest przeznaczona dla scenariuszy replikacji synchronicznej. Będzie mierzalne opóźnienie między główną i repliką. Dane w replice ostatecznie staje się spójne z danymi na głównym. Użyj tej funkcji dla obciążeń, które można uwzględnić to opóźnienie.

## <a name="create-a-replica"></a>Tworzenie repliki
Na serwerze głównym musi być `azure.replication_support` parametr **REPLIKI**. Po zmianie tego parametru ponownego uruchomienia serwera jest wymagany, aby zmiana zaczęła obowiązywać. ( `azure.replication_support` Parametr dotyczy tylko warstw ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci).

Po uruchomieniu przepływu pracy tworzenia repliki, pustą bazę danych Azure dla serwera PostgreSQL jest tworzony. Nowy serwer jest wypełniony danymi, która znajdowała się na serwerze głównym. Godzina utworzenia zależy od ilości danych na wzorcu i czas od ostatniej pełnej cotygodniowej kopii zapasowej. Czas może wynosić od kilku minut do kilku godzin.

Funkcję odczytu repliki korzysta z replikacji postgresql w warstwie fizycznej, replikacji nie logiczne. Przesyłanie strumieniowe replikacji przy użyciu miejsc replikacji to domyślny tryb działania. Gdy jest to konieczne, wysyłania dziennika jest używany do zapoznać się z nimi.

> [!NOTE]
> Jeśli nie masz zestaw alertu magazynu na serwerach, firma Microsoft zaleca, że możesz to zrobić. Ten alert informuje, kiedy zbliża się limit przestrzeni dyskowej, która będzie miało wpływ na replikację serwera.

Dowiedz się, jak [utworzyć odczytu replik w witrynie Azure portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Połącz się z repliką.
Podczas tworzenia repliki nie dziedziczy, reguły zapory lub serwera głównego, punkt końcowy usługi sieci wirtualnej. Te reguły można skonfigurować niezależnie dla repliki.

Repliki dziedziczy konta administratora serwera głównego. Wszystkie konta użytkowników na serwerze głównym są replikowane do odczytu replik. Odczytu repliki można połączyć tylko przy użyciu konta użytkownika, które są dostępne na serwerze głównym.

Możesz połączyć do repliki przy użyciu jego nazwy hosta i prawidłowe konto użytkownika, tak jak w regularnym — Azure Database for postgresql w warstwie serwera. Na serwerze o nazwie **myreplica** przy użyciu nazwy użytkownika administratora **myadmin**, repliki można nawiązać za pomocą narzędzia psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

W wierszu polecenia wprowadź hasło dla konta użytkownika.

## <a name="monitor-replication"></a>Monitorowanie replikacji
Azure Database for PostgreSQL oferuje **opóźnienie między maksymalna liczba replik** metryki w usłudze Azure Monitor. Ta metryka jest dostępna na serwerze głównym tylko. Metryka przedstawia opóźnienie w bajtach między główną i repliką większość opóźnione. 

Usługa Azure Database for PostgreSQL udostępnia również **Lag repliki** metryki w usłudze Azure Monitor. Ta metryka jest dostępna tylko replik. 

Metryka jest obliczana na podstawie `pg_stat_wal_receiver` widoku:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```

Metryka Lag repliki przedstawia czas od ostatniej transakcji powtórzony. Jeśli nie ma żadnych transakcji, pojawiają się na serwerze głównym, metryki odzwierciedla to opóźnienie czasowe.

Ustaw alert informujący o tym, gdy opóźnienie repliki osiągnie wartość, która nie jest dopuszczalne dla obciążenia. 

Aby uzyskać dodatkowe informacje na ten temat kwerendy serwera głównego bezpośrednio po to, aby uzyskać opóźnienie replikacji w bajtach wszystkich replik.

W wersji 10 PostgreSQL:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

W wersji 9.6 lub starszej PostgreSQL:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Jeśli ponowne uruchomienie serwera głównego lub odczytu repliki, czas, jaki zajmuje ponowne uruchomienie i zapoznać się z nimi jest widoczny w metryki Lag repliki.

## <a name="stop-replication"></a>Zatrzymaj replikację
Można zatrzymać replikacji między serwerem głównym i repliki. Akcja zatrzymania powoduje, że repliki, aby ponownie uruchomić i usunąć jego ustawienia replikacji. Po zatrzymaniu replikacji między głównym serwerem i odczytu repliki, replika staje się serwerem autonomicznym. Dane na serwerze autonomicznym są dane, która była dostępna w replice w momencie uruchomienia polecenia zatrzymania replikacji. Serwer autonomiczny nie zapoznaj się z serwerem głównym.

> [!IMPORTANT]
> Serwer autonomiczny nie wprowadzać ponownie do repliki.
> Przed zatrzymaniem replikacji tylko do odczytu repliki, upewnij się, że replika zawiera wszystkie dane, których potrzebujesz.

Dowiedz się, jak [zatrzymać replikację do repliki](howto-read-replicas-portal.md).


## <a name="considerations"></a>Zagadnienia do rozważenia

Ta sekcja zawiera podsumowanie zagadnień dotyczących funkcji odczytu replik.

### <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem odczytu repliki, `azure.replication_support` parametru musi być równa **REPLIKI** na serwerze głównym. Po zmianie tego parametru ponownego uruchomienia serwera jest wymagany, aby zmiana zaczęła obowiązywać. `azure.replication_support` Parametr dotyczy tylko warstw ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

### <a name="new-replicas"></a>Nowej repliki
Odczytu repliki jest tworzona jako nowego serwera Azure Database for PostgreSQL. Nie można dokonać istniejący serwer do repliki. Odczytu repliki można tworzyć tylko w tym samym regionie platformy Azure jako wzorzec. Nie można utworzyć replikę innej repliki do odczytu.

### <a name="replica-configuration"></a>Konfiguracji repliki
Replika jest tworzona przy użyciu tej samej konfiguracji serwera jako wzorzec. Po utworzeniu repliki, niektóre ustawienia można zmienić niezależnie z serwera głównego: obliczenia generacji, rdzenie wirtualne, Magazyn i okres przechowywania kopii zapasowej. Warstwę cenową można zmienić niezależnie, z wyjątkiem do lub z warstwy podstawowa.

> [!IMPORTANT]
> Konfiguracja serwera głównego jest aktualizowany do nowych wartości, aktualizacja konfiguracji repliki większa lub równa wartości. Ta akcja zagwarantuje, że repliki może nadążyć za wszelkie zmiany wprowadzone do poziomu głównego.

PostgreSQL wymaga wartości `max_connections` parametru odczytu repliki, być większa lub równa wartości wzorca; w przeciwnym repliki nie będą naliczane. W usłudze Azure Database for PostgreSQL `max_connections` wartość parametru opiera się na jednostkę SKU. Aby uzyskać więcej informacji, zobacz [limity w usłudze Azure Database for PostgreSQL](concepts-limits.md). 

Jeśli spróbuj zaktualizować wartości serwera, ale nie jest zgodna z limitami, otrzymasz komunikat o błędzie.

### <a name="stopped-replicas"></a>Zatrzymano repliki
Zatrzymanie replikacji między głównym serwerem i odczytu repliki, replikę powoduje ponowne uruchomienie do zastosowania zmiany. Zatrzymano replika staje się serwer autonomiczny, który akceptuje odczytów i zapisów. Serwer autonomiczny nie wprowadzać ponownie do repliki.

### <a name="deleted-master-and-standalone-servers"></a>Usunięto głównego i autonomicznych serwerów
Po usunięciu serwera głównego, wszystkie jego odczytu repliki stają się serwerów autonomicznych. Repliki zostaną ponownie uruchomione w celu odzwierciedlenia tej zmiany.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [tworzenie i zarządzanie nimi odczytu replik w witrynie Azure portal](howto-read-replicas-portal.md).
