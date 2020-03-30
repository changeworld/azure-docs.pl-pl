---
title: Replikacja w danych — usługa Azure Database dla mariadb
description: Dowiedz się więcej o używaniu replikacji danych do synchronizacji z serwera zewnętrznego w usłudze Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 1fbcc1fb27d5e6df4641f79c0d634580f74000b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532064"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikowanie danych do bazy danych platformy Azure dla mariadb

Replikacja typu data-in umożliwia synchronizowanie danych z serwera MariaDB uruchomionego lokalnie, na maszynach wirtualnych lub w usługach bazy danych hostowanych przez innych dostawców chmury do usługi Azure Database for MariaDB. Replikacja typu data-in jest wykonywana za pomocą technologii replikacji opartej na pozycji w pliku dziennika binarnego (binlog) natywnej dla programu MariaDB. Aby dowiedzieć się więcej o replikacji binlog, zobacz [omówienie replikacji binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Kiedy używać replikacji danych
Główne scenariusze, które należy wziąć pod uwagę przy użyciu replikacji danych w są:

- **Hybrydowa synchronizacja danych:** Dzięki replikacji danych można przechowywać dane synchronizowane między serwerami lokalnymi i usługą Azure Database for MariaDB. Ta synchronizacja jest przydatna do tworzenia aplikacji hybrydowych. Ta metoda jest atrakcyjna, gdy masz istniejący serwer lokalnej bazy danych, ale chcesz przenieść dane do regionu bliżej użytkowników końcowych.
- **Synchronizacja wielochmurowa:** W przypadku złożonych rozwiązań w chmurze użyj replikacji danych do synchronizowania danych między usługą Azure Database dla MariaDB i różnymi dostawcami chmury, w tym maszynami wirtualnymi i usługami bazy danych hostowanymi w tych chmurach.

## <a name="limitations-and-considerations"></a>Ograniczenia i istotne zagadnienia

### <a name="data-not-replicated"></a>Dane nie są replikowane
Baza [*danych systemu mysql*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) na serwerze głównym nie jest replikowana. Zmiany na kontach i uprawnieniach na serwerze głównym nie są replikowane. Jeśli utworzysz konto na serwerze głównym i to konto musi uzyskać dostęp do serwera repliki, należy ręcznie utworzyć to samo konto po stronie serwera repliki. Aby dowiedzieć się, jakie tabele znajdują się w systemowej bazie danych, zobacz [dokumentację MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Wymagania
- Wersja serwera głównego musi być co najmniej MariaDB w wersji 10.2.
- Wersje serwerów wzorcowych i replik muszą być takie same. Na przykład oba muszą być MariaDB w wersji 10.2.
- Każda tabela musi mieć klucz podstawowy.
- Serwer główny powinien używać aparatu InnoDB.
- Użytkownik musi mieć uprawnienia do konfigurowania rejestrowania binarnego i tworzenia nowych użytkowników na serwerze głównym.
- Jeśli serwer główny ma włączoną protokół SSL, upewnij się, że certyfikat `mariadb.az_replication_change_master` urzędu certyfikacji SSL przewidziany dla domeny został uwzględniony w procedurze składowanej. Zapoznaj się z [poniższymi przykładami](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) i parametrem. `master_ssl_ca`
- Upewnij się, że adres IP serwera głównego został dodany do reguł zapory serwera repliki usługi Azure Database for MariaDB. Zaktualizuj reguły zapory za pomocą [witryny Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli).
- Upewnij się, że maszyna hostująca serwer główny zezwala na ruch przychodzący i wychodzący na porcie 3306.
- Upewnij się, że serwer główny ma **publiczny adres IP,** system DNS jest publicznie dostępny lub ma w pełni kwalifikowaną nazwę domeny (FQDN).

### <a name="other"></a>Inne
- Replikacja w danych jest obsługiwana tylko w warstwach cenowych ogólnego przeznaczenia i zoptymalizowanej pod kątem pamięci.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować replikację danych](howto-data-in-replication.md).
