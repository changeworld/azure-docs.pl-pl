---
title: Replikacja wg danych — usługa Azure Database dla mysql
description: Dowiedz się więcej o używaniu replikacji danych do synchronizacji z serwera zewnętrznego w usłudze Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20be34191355e6ade40e0f3b218818bfa5345a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533236"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikowanie danych do bazy danych platformy Azure dla mysql

Replikacja w danych umożliwia synchronizowanie danych z zewnętrznego serwera MySQL z usługą Azure Database for MySQL. Serwer zewnętrzny może być lokalny, na maszynach wirtualnych lub usługa bazy danych hostowana przez innych dostawców chmury. Replikacja typu data-in jest wykonywana za pomocą technologii replikacji opartej na pozycji w pliku dziennika binarnego (binlog) natywnej dla programu MySQL. Aby dowiedzieć się więcej o replikacji binlog, zobacz [omówienie replikacji binlogu MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Kiedy używać replikacji danych
Główne scenariusze, które należy wziąć pod uwagę przy użyciu replikacji danych w są:

- **Hybrydowa synchronizacja danych:** Dzięki replikacji danych można przechowywać dane synchronizowane między serwerami lokalnymi i usługą Azure Database for MySQL. Ta synchronizacja jest przydatna do tworzenia aplikacji hybrydowych. Ta metoda jest atrakcyjna, gdy masz istniejący serwer lokalnej bazy danych, ale chcesz przenieść dane do regionu bliżej użytkowników końcowych.
- **Synchronizacja wielochmurowa:** W przypadku złożonych rozwiązań w chmurze użyj replikacji danych do synchronizowania danych między usługą Azure Database dla MySQL i różnymi dostawcami chmury, w tym maszynami wirtualnymi i usługami bazy danych hostowanymi w tych chmurach.
 
W przypadku scenariuszy migracji użyj [usługi migracji bazy danych Azure](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Ograniczenia i istotne zagadnienia

### <a name="data-not-replicated"></a>Dane nie są replikowane
[*Baza danych systemu mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) na serwerze głównym nie jest replikowana. Zmiany na kontach i uprawnieniach na serwerze głównym nie są replikowane. Jeśli utworzysz konto na serwerze głównym i to konto musi uzyskać dostęp do serwera repliki, ręcznie utwórz to samo konto po stronie serwera repliki. Aby dowiedzieć się, jakie tabele znajdują się w systemowej bazie danych, zobacz [podręcznik MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="requirements"></a>Wymagania
- Wersja serwera głównego musi być co najmniej MySQL w wersji 5.6. 
- Wersje serwerów wzorcowych i replik muszą być takie same. Na przykład oba muszą być MySQL w wersji 5.6 lub oba muszą być MySQL w wersji 5.7.
- Każda tabela musi mieć klucz podstawowy.
- Serwer główny powinien używać aparatu MySQL InnoDB.
- Użytkownik musi mieć uprawnienia do konfigurowania rejestrowania binarnego i tworzenia nowych użytkowników na serwerze głównym.
- Jeśli serwer główny ma włączoną protokół SSL, upewnij się, że certyfikat `mysql.az_replication_change_master` urzędu certyfikacji SSL przewidziany dla domeny został uwzględniony w procedurze składowanej. Zapoznaj się z [poniższymi przykładami](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) i parametrem. `master_ssl_ca`
- Upewnij się, że adres IP serwera głównego został dodany do reguł zapory serwera repliki usługi Azure Database for MySQL. Zaktualizuj reguły zapory za pomocą [witryny Azure Portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Upewnij się, że maszyna hostująca serwer główny zezwala na ruch przychodzący i wychodzący na porcie 3306.
- Upewnij się, że serwer główny ma **publiczny adres IP,** system DNS jest publicznie dostępny lub ma w pełni kwalifikowaną nazwę domeny (FQDN).

### <a name="other"></a>Inne
- Replikacja w danych jest obsługiwana tylko w warstwach cenowych ogólnego przeznaczenia i zoptymalizowanej pod kątem pamięci.
- Globalne identyfikatory transakcji (GTID) nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować replikację danych](howto-data-in-replication.md)
- Dowiedz się więcej o [replikowaniu na platformie Azure za pomocą replik odczytu](concepts-read-replicas.md)
- Dowiedz się, jak [migrować dane przy minimalnym przestoju przy użyciu usługi DMS](howto-migrate-online.md)