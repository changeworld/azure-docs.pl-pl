---
title: Replikowanie danych do Azure Database for MySQL.
description: W tym artykule opisano replikację danych w Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 889c2e75e9eee0586c709b032dbb6d1c58d45102
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142055"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikowanie danych do Azure Database for MySQL

Replikacja typu data-in pozwala synchronizować dane z zewnętrznego serwera MySQL do usługi Azure Database for MySQL. Serwer zewnętrzny może być lokalny, w maszynach wirtualnych lub w usłudze bazy danych hostowanej przez innych dostawców chmury. Replikacja typu data-in jest wykonywana za pomocą technologii replikacji opartej na pozycji w pliku dziennika binarnego (binlog) natywnej dla programu MySQL. Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Kiedy używać replikacja typu data-in
Główne scenariusze, które należy wziąć pod uwagę przy użyciu replikacja typu data-in są następujące:

- **Synchronizacja danych hybrydowych:** Za pomocą replikacja typu data-in można zachować synchronizację danych między serwerami lokalnymi i Azure Database for MySQL. Ta synchronizacja jest przydatna do tworzenia aplikacji hybrydowych. Ta metoda jest atrakcyjna, gdy masz istniejący lokalny serwer baz danych, ale chcesz przenieść dane do regionu bliżej użytkowników końcowych.
- **Synchronizacja z obsługą kilku chmur:** W przypadku złożonych rozwiązań w chmurze Użyj replikacja typu data-in do synchronizowania danych między Azure Database for MySQL i różnymi dostawcami chmury, w tym maszyn wirtualnych i usług baz danych hostowanych w tych chmurach.

## <a name="limitations-and-considerations"></a>Ograniczenia i zagadnienia

### <a name="data-not-replicated"></a>Dane nie zostały zreplikowane
[*Baza danych systemu MySQL*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) na serwerze głównym nie jest replikowana. Zmiany kont i uprawnień na serwerze głównym nie są replikowane. Jeśli utworzysz konto na serwerze głównym, a to konto musi uzyskać dostęp do serwera repliki, ręcznie Utwórz to samo konto na stronie serwera repliki. Aby zrozumieć, jakie tabele są zawarte w systemowej bazie danych, zobacz [Podręcznik programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Wymagania
- Wersja serwera głównego musi być nowsza niż wersja 5,6. 
- Wersje serwerów głównych i replik muszą być takie same. Na przykład oba muszą być w wersji 5,6 lub muszą mieć wersję MySQL w wersji 5,7.
- Każda tabela musi mieć klucz podstawowy.
- Serwer główny powinien korzystać z aparatu programu MySQL InnoDB.
- Użytkownik musi mieć uprawnienia do konfigurowania rejestrowania plików binarnych i tworzenia nowych użytkowników na serwerze głównym.
- Jeśli na serwerze głównym jest włączony protokół SSL, upewnij się, że certyfikat urzędu certyfikacji SSL podany dla domeny został uwzględniony w `mysql.az_replication_change_master` procedurze składowanej. Zapoznaj się z [](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) poniższymi przykładami i `master_ssl_ca` parametrem.
- Upewnij się, że adres IP serwera głównego został dodany do reguł zapory serwera repliki usługi Azure Database for MySQL. Zaktualizuj reguły zapory za pomocą [witryny Azure Portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Upewnij się, że maszyna hostująca serwer główny zezwala na ruch przychodzący i wychodzący na porcie 3306.
- Upewnij się, że serwer główny ma **publiczny adres IP** lub że system DNS jest dostępny publicznie.

### <a name="other"></a>Inne
- Replikacja danych jest obsługiwana tylko w warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.
- Globalne identyfikatory transakcji (GTID) nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować replikację danych](howto-data-in-replication.md)
- Więcej informacji [na temat replikowania na platformie Azure przy użyciu replik odczytu](concepts-read-replicas.md)
