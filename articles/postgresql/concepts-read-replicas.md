---
title: Repliki do odczytu w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano odczytu replik w usłudze Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 270231b2ad7d94789595cfa4e681cf6c2b0f0541
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657879"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Przeczytaj repliki w bazie danych Azure database for PostgreSQL

> [!IMPORTANT]
> Funkcja odczytu repliki jest w publicznej wersji zapoznawczej.

Funkcja odczytu replik umożliwia replikowanie danych z usługi Azure Database for postgresql w warstwie serwera (master) serwerom maksymalnie pięć tylko do odczytu (repliki do odczytu) w tym samym regionie platformy Azure. Odczytu replik asynchronicznie są aktualizowane przy użyciu technologii replikacji natywnego aparatu PostgreSQL.

Repliki są nowe serwery, które mogą być zarządzane w podobny sposób jak normalne autonomicznej bazy danych Azure Database dla serwerów MySQL. W przypadku każdej repliki do odczytu są naliczane opłaty za zaaprowizowane środowisko obliczeniowe (rozliczane w rdzeniach wirtualnych) i zaaprowizowany magazyn (rozliczany w GB/miesiąc).

Odwiedź stronę [porad strony, aby dowiedzieć się, jak tworzyć i zarządzać nimi replik](howto-read-replicas-portal.md).

## <a name="when-to-use-read-replicas"></a>Kiedy należy używać odczytu replik
Funkcja odczytu repliki jest przeznaczona dla ułatwienia zwiększenia wydajności i skalowalności intensywnie odczytujących obciążeń. Na przykład obciążeniami odczytu może być izolowane do replik, podczas obciążenia zapisu może zostać skierowany do poziomu głównego.

Typowy scenariusz polega na zapewnienie Power BI i obciążeń analitycznych pełnić odczytu repliki źródła danych do raportowania.

Ponieważ repliki tylko do odczytu, ich nie bezpośrednio złagodzić obciążeń pojemności zapisu we wzorcu, a więc ta funkcja nie jest przeznaczona dla obciążeń intensywnie korzystających z zapisu.

Funkcja odczytu replik używa replikacji asynchronicznej PostgreSQL i nie jest przeznaczona dla scenariuszy replikacji synchronicznej. Będzie mierzalne opóźnienie między główną i repliką. Dane w replice staje się ostatecznie spójne z danymi na głównym. Użyj tej funkcji dla obciążeń, które można uwzględnić to opóźnienie.

## <a name="creating-a-replica"></a>Tworzenie repliki
Na serwerze głównym musi być **azure.replication_support** równa REPLIKI. Zmiana ten parametr wymaga ponownego uruchomienia serwera, aby zastosować zmiany. (**Azure.replication_support** parametr dotyczy tylko warstw ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci).

Podczas inicjowania przepływu pracy tworzenia repliki jest tworzony puste — Azure Database for postgresql w warstwie serwera. Nowy serwer jest wypełniony danymi, która znajdowała się na serwerze głównym. Czas potrzebny do utworzenia nowej repliki zależy od ilości danych na wzorcu i czas od ostatniej pełnej cotygodniowej kopii zapasowej. To może należeć do zakresu od kilku minut do kilku godzin.

Funkcja odczytu replik używa PostgreSQL fizycznych replikacji (replikacja nie logicznych). Przesyłanie strumieniowe replikacji przy użyciu miejsc replikacji to domyślny tryb działania. Gdy jest to konieczne, wysyłania dziennika jest używany dla — wyrównywanie.

> [!NOTE]
> Jeśli nie masz już zestaw alertu magazynu na serwerach, który zalecamy, aby poinformować, gdy serwer zbliża się limit przestrzeni dyskowej, ponieważ ma to wpływu na replikację.

[Dowiedz się, jak utworzyć odczytu replik w witrynie Azure portal](howto-read-replicas-portal.md).

## <a name="connecting-to-a-replica"></a>Nawiązywanie połączenia z repliki
Podczas tworzenia repliki nie dziedziczą reguły zapory lub serwera głównego, punkt końcowy usługi sieci wirtualnej. Te reguły można skonfigurować niezależnie dla repliki.

Repliki dziedziczy jej konta administratora serwera głównego. Wszystkie konta użytkowników na serwerze głównym są replikowane do odczytu replik. Tekst można połączyć tylko do odczytu repliki, przy użyciu konta użytkownika dostępne na serwerze głównym.

Możesz nawiązać repliki przy użyciu jego nazwy hosta i prawidłowe konto użytkownika, tak jak w regularnym — Azure Database for postgresql w warstwie serwera. Na przykład jeśli nazwa serwera jest myreplica, a nazwa użytkownika administratora jest myadmin można połączyć do niego z narzędzia psql w następujący sposób:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```
i w wierszu wprowadź hasło dla konta użytkownika.

## <a name="monitoring-replication"></a>Monitorowanie replikacji
Brak **maksymalna zwłoka między replikami** metryk dostępnych w usłudze Azure Monitor. Ta metryka jest dostępna na serwerze głównym tylko. Metryka przedstawia opóźnienie w bajtach między główną i najbardziej opóźnione repliką. 

Firma Microsoft udostępnia również **Lag repliki** metryki w usłudze Azure Monitor. Ta metryka jest dostępna tylko replik. 

Metryka jest obliczana na podstawie widoku pg_stat_wal_receiver:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```
Należy pamiętać, metryki Lag repliki przedstawia czas, od czasu ostatniego powtórzone transakcji. Jeśli nie ma żadnych transakcji, występujących na wzorca usługi, metryki odzwierciedla to opóźnienie czasowe.

Firma Microsoft zaleca, aby ustawić alert informujący o tym, gdy opóźnienie repliki osiągnie wartość, która nie jest dopuszczalne dla obciążenia. 

Aby uzyskać dodatkowe informacje na ten temat kwerendy serwera głównego bezpośrednio po to, aby uzyskać opóźnienie replikacji w bajtach wszystkich replik.
Na stronie 10:
```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

Na stronie 9.6 i poniżej:
```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Jeśli serwer główny lub replika zostanie ponownie uruchomiony, czas, jaki zajmuje ponowne uruchomienie, a następnie zapoznaj się zostaną odzwierciedlone w metryce Lag repliki.

## <a name="stopping-replication-to-a-replica"></a>Zatrzymywanie replikacji do repliki
Można zatrzymać replikację między wzorca i repliki. To spowoduje, że repliki w celu ponownego uruchomienia, aby usunąć jego ustawienia replikacji. Po replikacji została zatrzymana między główny i serwer repliki, na serwerze repliki staje się serwer autonomiczny. Dane na serwerze autonomicznym są dane, która była dostępna w replice w czasie, którą zainicjowano polecenie zatrzymania replikacji. Ten serwer autonomiczny nie zapoznaj się z serwerem głównym.

Ten serwer nie wprowadzać ponownie do repliki.

Upewnij się, że replika zawiera wszystkie dane, które wymagają, aby zatrzymać replikację.

Możesz [Dowiedz się, jak zatrzymać repliki w dokumentację zawierającą instrukcje](howto-read-replicas-portal.md).


## <a name="considerations"></a>Zagadnienia do rozważenia

### <a name="preparing-for-replica"></a>Przygotowywanie do repliki
**Azure.replication_support** musi być równa REPLIKI na serwerze głównym można było utworzyć replikę. Zmiana ten parametr wymaga ponownego uruchomienia serwera, aby zastosować zmiany. Ten parametr dotyczy tylko warstw ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

### <a name="stopped-replicas"></a>Zatrzymano repliki
Jeśli chcesz zatrzymać replikację między głównego i repliką replika zostanie uruchomiony ponownie, aby zastosować tę zmianę. Replika stanie się serwerem odczytu i zapisu. Później nie może on na replikę ponownie.

### <a name="replicas-are-new-servers"></a>Repliki są nowe serwery
Repliki są tworzone jako nowe — Azure Database for postgresql w warstwie serwerów. Nie można dokonać istniejących serwerów do replik.

### <a name="replica-server-configuration"></a>Konfiguracja serwera repliki
Serwer funkcji replica są tworzone przy użyciu tej samej konfiguracji serwera jako głównej, która zawiera następujące konfiguracje:
- Warstwa cenowa
- Generacja obliczeń
- Rdzenie wirtualne
- Magazyn
- Okres przechowywania kopii zapasowej
- Opcja nadmiarowości kopii zapasowej
- Wersja aparatu PostgreSQL

Po utworzeniu repliki warstwa cenowa (z wyjątkiem do i z Basic), generacja obliczeń, rdzenie wirtualne, Magazyn i okres przechowywania kopii zapasowej można zmienić niezależnie od serwera głównego.

> [!IMPORTANT]
> Zanim Konfiguracja serwera głównego jest aktualizowany do nowych wartości, konfiguracji tych replik należy zaktualizować większa lub równa wartości. Dzięki temu, że repliki są w stanie na bieżąco ze zmianami wprowadzonymi do poziomu głównego.

W szczególności Postgres wymaga wartość serwera repliki max_connections parametru jest większa lub równa wartości wzorca w przeciwnym razie replika nie zostanie uruchomiona. W usłudze Azure Database for PostgreSQL max_connections ma wartość w zależności od jednostki sku. Aby uzyskać więcej informacji, przeczytaj [doc limity](concepts-limits.md). 

Takie próby aktualizacji, która narusza tę zasadę doprowadzi do błędu.


### <a name="deleting-the-master"></a>Usuwanie poziomu głównego
Po usunięciu serwera głównego wszystkich odczytu replik stają się serwerów autonomicznych. Repliki zostanie uruchomiony ponownie w celu odzwierciedlenia tej zmiany.

### <a name="other"></a>Inne
- Repliki do odczytu można tworzyć tylko w tym samym regionie platformy Azure jako wzorzec.
- Tworzenie repliki repliki nie jest obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
- [Jak utworzyć i zarządzać nimi odczytu replik w witrynie Azure portal](howto-read-replicas-portal.md).
