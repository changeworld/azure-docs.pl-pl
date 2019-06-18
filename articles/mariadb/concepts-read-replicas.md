---
title: Odczytu replik w usłudze Azure Database dla serwera MariaDB
description: W tym artykule opisano odczytu replik dla usługi Azure Database dla serwera MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 8abe257090b5159053a37350c9e24cc27073679b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079368"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Odczytu replik w usłudze Azure Database dla serwera MariaDB

Funkcja odczytu replik umożliwia replikowanie danych z usługi Azure Database dla serwera MariaDB serwerowi tylko do odczytu. Można replikować z głównego serwera do maksymalnie pięciu replik w tym samym regionie jako wzorzec. Repliki są aktualizowane, asynchronicznie przy użyciu technologii replikacji na podstawie położenia pliku dziennik binarny (binlog) Aparat MariaDB z transakcją globalny identyfikator (GTID). Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji binlog](https://mariadb.com/kb/en/library/replication-overview/).

> [!IMPORTANT]
> Tym samym regionie odczytu repliki, jest obecnie dostępna w publicznej wersji zapoznawczej.

Repliki są nowe serwery zarządzania podobne do regularnego usługi Azure Database dla serwerów MariaDB. Dla każdego odczytu repliki, opłaty są naliczane dla zainicjowanych zasobów obliczeniowych w rdzeniach wirtualnych zasoby i magazynu w GB / miesiąc.

Aby dowiedzieć się więcej na temat GTID replikacji, zobacz [dokumentacji replikacji MariaDB](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>Kiedy należy używać odczytu repliki

Funkcja odczytu replik pomaga zwiększyć wydajność i skalę intensywnie odczytujących obciążeń. Odczyt obciążenia mogą być izolowane w replikach, podczas obciążenia zapisu może zostać skierowany do poziomu głównego.

Typowy scenariusz polega na zapewnienie Power BI i obciążeń analitycznych pełnić odczytu repliki źródła danych do raportowania.

Ponieważ repliki są tylko do odczytu, nie bezpośrednio zmniejszyć obciążenia pojemności zapisu we wzorcu. Ta funkcja nie jest przeznaczone dla obciążeń intensywnie korzystających z zapisu.

Funkcja replica odczytu używa replikacji asynchronicznej. Ta funkcja nie jest przeznaczona dla scenariuszy replikacji synchronicznej. Będzie mierzalne opóźnienie między główną i repliką. Dane w replice ostatecznie staje się spójne z danymi na głównym. Użyj tej funkcji dla obciążeń, które można uwzględnić to opóźnienie.

Odczytu replik można zwiększyć, plan odzyskiwania po awarii. Jeśli serwer główny jest niedostępny w przypadku regionalnej awarii, można kierować obciążenia, aby repliki w innym regionie. W tym celu należy najpierw umożliwić repliki akceptuje zapisu przy użyciu funkcji replikacji zatrzymania. Następnie można przekierować aplikację, aktualizując parametry połączenia. Dowiedz się więcej w [zatrzymać replikację](#stop-replication) sekcji.

## <a name="create-a-replica"></a>Tworzenie repliki

Jeśli serwer główny nie ma żadnych istniejących serwerów repliki, wzorzec najpierw zostanie uruchomiony ponownie przygotować się do replikacji.

Podczas uruchamiania przepływu pracy repliki utwórz pustą bazę danych Azure dla serwera MariaDB jest tworzony. Nowy serwer jest wypełniony danymi, która znajdowała się na serwerze głównym. Godzina utworzenia zależy od ilości danych na wzorcu i czas od ostatniej pełnej cotygodniowej kopii zapasowej. Czas może wynosić od kilku minut do kilku godzin.

> [!NOTE]
> Jeśli nie masz zestaw alertu magazynu na serwerach, firma Microsoft zaleca, że możesz to zrobić. Ten alert informuje, kiedy zbliża się limit przestrzeni dyskowej, która będzie miało wpływ na replikację serwera.

Dowiedz się, jak [utworzyć odczytu replik w witrynie Azure portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Połącz się z repliką.

Podczas tworzenia repliki nie dziedziczy, reguły zapory lub serwera głównego, punkt końcowy usługi sieci wirtualnej. Te reguły można skonfigurować niezależnie dla repliki.

Repliki dziedziczy konta administratora serwera głównego. Wszystkie konta użytkowników na serwerze głównym są replikowane do odczytu replik. Odczytu repliki można połączyć tylko przy użyciu konta użytkownika, które są dostępne na serwerze głównym.

Możesz połączyć do repliki przy użyciu jego nazwy hosta i prawidłowe konto użytkownika, tak jak w regularnym usługi Azure Database dla serwera MariaDB. Na serwerze o nazwie **myreplica** przy użyciu nazwy użytkownika administratora **myadmin**, można połączyć się z repliką przy użyciu interfejsu wiersza polecenia mysql:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

W wierszu polecenia wprowadź hasło dla konta użytkownika.

## <a name="monitor-replication"></a>Monitorowanie replikacji

Usługa Azure Database dla serwera MariaDB udostępnia **opóźnienie replikacji w ciągu kilku sekund** metryki w usłudze Azure Monitor. Ta metryka jest dostępna tylko replik.

Ta metryka jest obliczany przy użyciu `seconds_behind_master` metryk dostępnych w firmy MariaDB `SHOW SLAVE STATUS` polecenia.

Ustaw alert informujący o tym, gdy opóźnienie replikacji osiągnie wartość, która nie jest dopuszczalne dla obciążenia.

## <a name="stop-replication"></a>Zatrzymywanie replikacji

Można zatrzymać replikacji między serwerem głównym i repliki. Po zatrzymaniu replikacji między głównym serwerem i odczytu repliki, replika staje się serwerem autonomicznym. Dane na serwerze autonomicznym są dane, która była dostępna w replice w momencie uruchomienia polecenia zatrzymania replikacji. Serwer autonomiczny nie zapoznaj się z serwerem głównym.

W przypadku zatrzymania replikacji do repliki utraci wszystkie łącza do jego poprzedniego główny i innymi replikami. Nie ma żadnych automatycznego trybu failover między wzorzec i jego repliką.

> [!IMPORTANT]
> Serwer autonomiczny nie wprowadzać ponownie do repliki.
> Przed zatrzymaniem replikacji tylko do odczytu repliki, upewnij się, że replika zawiera wszystkie dane, których potrzebujesz.

Dowiedz się, jak [zatrzymać replikację do repliki](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Istotne zagadnienia i ograniczenia

### <a name="pricing-tiers"></a>Warstwy cenowe

Repliki do odczytu są obecnie dostępne tylko w warstw cenowych ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

### <a name="master-server-restart"></a>Ponowne uruchomienie serwera głównego

Po utworzeniu repliki dla serwera głównego, który nie ma istniejących replik wzorzec najpierw zostanie uruchomiony ponownie przygotować się do replikacji. Wziąć pod uwagę i wykonywać te operacje podczas okresu poza godzinami.

### <a name="new-replicas"></a>Nowej repliki

Odczytu repliki jest tworzona jako nową bazę danych Azure dla serwera MariaDB. Nie można dokonać istniejący serwer do repliki. Nie można utworzyć replikę innej repliki do odczytu.

### <a name="replica-configuration"></a>Konfiguracji repliki

Replika jest tworzona przy użyciu tej samej konfiguracji serwera jako wzorzec. Po utworzeniu repliki, niektóre ustawienia można zmienić niezależnie z serwera głównego: obliczenia generacji, rdzenie wirtualne, Magazyn, okres przechowywania kopii zapasowej i MariaDB taką wersję aparatu. Warstwę cenową można zmienić niezależnie, z wyjątkiem do lub z warstwy podstawowa.

> [!IMPORTANT]
> Konfiguracja serwera głównego jest aktualizowany do nowych wartości, aktualizacja konfiguracji repliki większa lub równa wartości. Ta akcja zagwarantuje, że repliki może nadążyć za wszelkie zmiany wprowadzone do poziomu głównego.

### <a name="stopped-replicas"></a>Zatrzymano repliki

Zatrzymanie replikacji między głównym serwerem i odczytu repliki, zatrzymania replika staje się serwer autonomiczny, który akceptuje odczytów i zapisów. Serwer autonomiczny nie wprowadzać ponownie do repliki.

### <a name="deleted-master-and-standalone-servers"></a>Usunięto głównego i autonomicznych serwerów

Po usunięciu serwera głównego replikacja jest zatrzymana, aby wszystkie repliki do odczytu. Tych replik stają się serwerów autonomicznych. Sam serwer główny jest usuwany.

### <a name="user-accounts"></a>Konta użytkowników

Użytkownicy na serwerze głównym są replikowane do odczytu replik. Tekst można połączyć tylko do odczytu repliki, przy użyciu konta użytkownika dostępne na serwerze głównym.

### <a name="server-parameters"></a>Parametry serwera

Aby zapobiec danych zsynchronizowane i uniknąć potencjalnych utraty lub uszkodzenia danych, niektórych parametrów serwera są zablokowane z aktualizowana przy użyciu odczytu replik.

Następujące parametry serwera są zablokowane na serwerach zarówno węzła głównego, jak i repliki:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

[ `event_scheduler` ](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) Parametru jest zablokowany na serwerach repliki.

### <a name="other"></a>Inne

- Tworzenie repliki repliki nie jest obsługiwane.
- Tabele w pamięci, może spowodować replik zsynchronizowany. Jest to ograniczenie MariaDB technologii replikacji.
- Upewnij się, że tabele serwera głównego kluczy podstawowych. Brak kluczy podstawowych może spowodować opóźnienie replikacji między węzłem głównym i repliki.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [tworzenie i zarządzanie nimi odczytu replik w witrynie Azure portal](howto-read-replicas-portal.md)
