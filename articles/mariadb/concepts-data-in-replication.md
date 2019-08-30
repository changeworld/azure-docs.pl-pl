---
title: Replikowanie danych do Azure Database for MariaDB.
description: W tym artykule opisano replikację danych w Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 28c2c01e85120ec17e6f782fb0686a627d50d0d0
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70136742"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikowanie danych do Azure Database for MariaDB

Replikacja typu data-in umożliwia synchronizowanie danych z serwera MariaDB uruchomionego lokalnie, na maszynach wirtualnych lub w usługach bazy danych hostowanych przez innych dostawców chmury do usługi Azure Database for MariaDB. Replikacja typu data-in jest wykonywana za pomocą technologii replikacji opartej na pozycji w pliku dziennika binarnego (binlog) natywnej dla programu MariaDB. Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Kiedy używać replikacja typu data-in
Główne scenariusze, które należy wziąć pod uwagę przy użyciu replikacja typu data-in są następujące:

- **Synchronizacja danych hybrydowych:** Za pomocą replikacja typu data-in można zachować synchronizację danych między serwerami lokalnymi i Azure Database for MariaDB. Ta synchronizacja jest przydatna do tworzenia aplikacji hybrydowych. Ta metoda jest atrakcyjna, gdy masz istniejący lokalny serwer baz danych, ale chcesz przenieść dane do regionu bliżej użytkowników końcowych.
- **Synchronizacja z obsługą kilku chmur:** W przypadku złożonych rozwiązań w chmurze Użyj replikacja typu data-in do synchronizowania danych między Azure Database for MariaDB i różnymi dostawcami chmury, w tym maszyn wirtualnych i usług baz danych hostowanych w tych chmurach.

## <a name="limitations-and-considerations"></a>Ograniczenia i zagadnienia

### <a name="data-not-replicated"></a>Dane nie zostały zreplikowane
[*Baza danych systemu MySQL*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) na serwerze głównym nie jest replikowana. Zmiany kont i uprawnień na serwerze głównym nie są replikowane. Jeśli utworzysz konto na serwerze głównym, a to konto musi uzyskać dostęp do serwera repliki, utwórz je ręcznie na stronie serwera repliki. Aby zrozumieć, jakie tabele są zawarte w systemowej bazie danych, zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Wymagania
- Wersja serwera głównego musi mieć co najmniej MariaDB w wersji 10,2.
- Wersje serwerów głównych i replik muszą być takie same. Na przykład obydwie muszą być MariaDB w wersji 10,2.
- Każda tabela musi mieć klucz podstawowy.
- Serwer główny powinien używać aparatu InnoDB.
- Użytkownik musi mieć uprawnienia do konfigurowania rejestrowania plików binarnych i tworzenia nowych użytkowników na serwerze głównym.
- Jeśli na serwerze głównym jest włączony protokół SSL, upewnij się, że certyfikat urzędu certyfikacji SSL podany dla domeny został uwzględniony w `mariadb.az_replication_change_master` procedurze składowanej. Zapoznaj się z [](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) poniższymi przykładami i `master_ssl_ca` parametrem.
- Upewnij się, że adres IP serwera głównego został dodany do reguł zapory serwera repliki usługi Azure Database for MariaDB. Zaktualizuj reguły zapory za pomocą [witryny Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli).
- Upewnij się, że maszyna hostująca serwer główny zezwala na ruch przychodzący i wychodzący na porcie 3306.
- Upewnij się, że serwer główny ma **publiczny adres IP** lub usługa DNS jest publicznie dostępna

### <a name="other"></a>Inne
- Replikacja danych jest obsługiwana tylko w warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować replikację danych](howto-data-in-replication.md).
