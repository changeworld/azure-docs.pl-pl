---
title: Artykuł na temat rozwiązywania typowych problemów/problemów związanych z użyciem usługi Azure Database Migration Service znane | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak rozwiązywać typowe znanych problemów/błędów związanych z użyciem usługi Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/22/2019
ms.openlocfilehash: 5a7c6c4553f46e8a7308995e05d6c06c0eb10f27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002208"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Rozwiązywanie typowych problemów z usługi Azure Database Migration Service, jak i błędy

W tym artykule opisano niektóre typowe problemy i błędy, które użytkownicy usługi Azure Database Migration Service mogą pochodzić między. Artykuł zawiera również informacje dotyczące sposobu rozwiązania tych problemów i błędów.

## <a name="migration-activity-in-queued-state"></a>Działania migracji w stanie umieszczenia w kolejce

Po utworzeniu nowych działań w projekcie usługi Azure Database Migration Service działania pozostają w stanie umieszczenia w kolejce.

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten problem występuje, gdy wystąpienie usługi Azure Database Migration Service osiągnął maksymalną pojemność w zakresie bieżących zadań, które jednocześnie uruchomić. Wszelkie nowe działanie znajduje się w kolejce do momentu pojemność stanie się dostępny. | Sprawdź poprawność Data Migration Service, wystąpienie posiada uruchamiania działań w projektach. Możesz nadal tworzyć nowe działania, które automatycznie dodane do kolejki w celu wykonania. Tak szybko, jak wykonać jedną z istniejących uruchamiania działań, następne działanie w kolejce zacznie działać, a stan zmieni się na uruchomiona automatycznie. Nie trzeba podejmować żadnych dodatkowych działań, aby rozpocząć migrację działania umieszczonych w kolejce.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maksymalna liczba baz danych wybrana do migracji

Wystąpił następujący błąd podczas tworzenia działania dla projektu migracji bazy danych w przypadku przenoszenia bazy danych SQL Azure lub usługi Azure SQL Database wystąpienia zarządzanego:

* **Błąd**: Błąd weryfikacji ustawień migracji","errorDetail":"więcej niż maksymalna liczba "4" obiekty "Bazy danych" została wybrana do migracji".

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd wyświetlane, gdy wybrano więcej niż cztery bazy danych dla działania migracji. W chwili obecnej każde działanie migracji jest ograniczona do czterech baz danych. | Wybierz czterech lub mniejszej liczby baz danych na działanie migracji. Jeśli potrzebujesz więcej niż cztery równoległe Migrowanie baz danych, aprowizowanie inne wystąpienie usługi Azure Database Migration Service. Obecnie Każda subskrypcja obsługuje maksymalnie dwa wystąpienia usługi Azure Database Migration Service.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Błędy migracji MySQL do usługi Azure MySQL z błędami odzyskiwania

Podczas migracji z MySQL do usługi Azure Database for MySQL za pomocą usługi Azure Database Migration Service, działania migracji nie powiedzie się z następującym błędem:

* **Błąd**: Błąd migracji bazy danych — zadanie "TaskID" zostało wstrzymane z powodu błędów kolejnych odzyskiwania [n].

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd może wystąpić, gdy użytkownik, wykonując migrację brakuje rolę ReplicationAdmin i/lub uprawnienia REPLICATION CLIENT, REPLIKACJA i SUPER (wersje starsze niż MySQL niż 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Upewnij się, że [uprawnień wstępnych](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) użytkownika konta są skonfigurowane dokładnie w usłudze Azure Database for MySQL — wystąpienia. Na przykład aby utworzyć użytkownika o nazwie "migrateuser" z wymaganymi uprawnieniami należy wykonać następujące czynności:<br>1. Tworzenie użytkownika migrateuser@'% "IDENTYFIKOWANY przez 'klucz tajny.'; <br>2. Przyznawanie uprawnień wszystkich na db_name.* do "migrateuser'@'%" identyfikowany przez 'klucz tajny.'; Powtórz ten krok, aby przyznać dostęp do większej liczby baz danych <br>3. Przyznania podrzędnego replikacji na *.* Aby "migrateuser'@'%" identyfikowany przez 'klucz tajny.';<br>4. Udziel klienta replikacji na *.* Aby "migrateuser'@'%" identyfikowany przez 'klucz tajny.';<br>5. Flush uprawnień. |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Wystąpił błąd podczas próby zatrzymania usługi Azure Database Migration Service

Otrzymujesz następujący błąd podczas zatrzymywania wystąpień usługi Azure Database Migration Service:

* **Błąd**: Nie można zatrzymać usługi. Błąd: {"error": {"code": "InvalidRequest", "message": "jedno lub więcej działań są aktualnie uruchomione. Aby zatrzymać usługę, zaczekaj, aż działania zostały ukończone lub Zatrzymaj działania ręcznie i spróbuj ponownie. "}}

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd wyświetlane, gdy wystąpienie usługi, którą próbujesz zatrzymać obejmuje działania, które nadal są uruchomione lub przedstawić w projektach migracji. <br><br><br><br><br><br> | Upewnij się, że nie ma żadnych działań, uruchomiony w wystąpieniu usługi Azure Database Migration Service, w przypadku próby zatrzymania. Można również usunąć działań lub projektów przed podjęciem próby zatrzymania usługi. Poniższe kroki ilustrują usuwanie projektów, aby wyczyścić wystąpienie usługi migracji, usuwając wszystkie uruchomione zadania:<br>1. Install-Module-nazwa AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription -SubscriptionName "<subName>" <br> 4. Remove-AzureRmDataMigrationProject -Name <projectName> -ResourceGroupName <rgName> -ServiceName <serviceName> -DeleteRunningTask |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Błąd podczas przywracania bazy danych, gdy wystąpienie zarządzane SQL Migrowanie do usługi Azure SQL DB

Po wykonaniu migracji do usługi online z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database jednorazowej migracji zakończy się niepowodzeniem z powodu następującego błędu:

* **Błąd**: Operacja przywracania nie powiodła się dla "operationId" identyfikator operacji. Kod "AuthorizationFailed", komunikat "klient"clientId"o identyfikatorze obiektu"objectId"nie ma autoryzacji do wykonania akcji"Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read"w zakresie" /subscriptions/ Identyfikator subskrypcji ".".

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten błąd wskazuje podmiotu zabezpieczeń aplikacji używanych na potrzeby migracji online z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database nie wpływa uprawnień w ramach subskrypcji. Niektóre wywołania interfejsu API za pomocą wystąpienia zarządzanego w chwili obecnej wymagają tego uprawnienia, w ramach subskrypcji dla operacji przywracania. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Użyj `Get-AzureADServicePrincipal` polecenia cmdlet programu PowerShell przy użyciu `-ObjectId` dostępne z komunikatu o błędzie, aby wyświetlić listę identyfikator używany nazwę wyświetlaną.<br><br> Weryfikowanie uprawnień do tej aplikacji i upewnij się, ma ona [rola "Współautor"](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) na poziomie subskrypcji. <br><br> Azure Database migracji usługi zespołu inżynierów programu dokłada starań, aby ograniczyć wymagany dostęp z bieżącego współtworzyć roli w ramach subskrypcji. Jeśli masz wymaganiem biznesowym, który nie zezwala na używanie współtworzyć roli, skontaktuj się z działem pomocy technicznej platformy Azure, aby uzyskać dodatkową pomoc. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Wystąpił błąd podczas usuwania karty Sieciowej skojarzonych z usługi Azure Database Migration Service

Podczas próby usunięcia skojarzone z usługi Azure Database Migration Service karty interfejsu sieciowego, próba usunięcia zakończy się niepowodzeniem z powodu następującego błędu:

* **Błąd**: Nie można usunąć kart Sieciowych powiązanych z usługi Azure Database Migration Service ze względu na usługę DMS, przy użyciu interfejsu Sieciowego

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten problem występuje, gdy wystąpienie usługi Azure Database Migration Service nadal mogą być obecne i używania karty sieciowej. <br><br><br><br><br><br><br><br> | Aby usunąć tę kartę Sieciową, usuń wystąpienia usługi DMS, która automatycznie usuwa karty Sieciowej używanej przez usługę.<br><br> **Ważne**: Upewnij się, że wystąpienia usługi Azure Database Migration Service, usunięcie nie ma uruchomionych działań.<br><br> Po usunięciu wszystkich projektów i czynnościami związanymi z wystąpieniem usługi Azure Database Migration Service możesz usunąć wystąpienie usługi. Karty Sieciowej używanej przez wystąpienie usługi jest automatycznie czyszczone jako część usuwania usługi. |

## <a name="connection-error-when-using-expressroute"></a>Błąd połączenia podczas korzystania z usługi ExpressRoute

Podczas próby nawiązania połączenia ze źródłem w Kreatorze projektu usługi Azure Database Migration service, połączenie nie powiedzie się po dłuższy limit czasu, jeśli źródło jest przy użyciu usługi ExpressRoute dla łączności.

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Korzystając z [ExpressRoute](https://azure.microsoft.com/services/expressroute/), Azure Database Migration Service [wymaga](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) trzy punkty końcowe usługi w podsieci sieci wirtualnej skojarzony z usługą aprowizacji:<br> — Punkt końcowy Usługa Service Bus<br> — Punkt końcowy magazynu<br> — Docelowa baza danych punktu końcowego, (np. SQL punktu końcowego, punktu końcowego usługi Cosmos DB)<br><br><br><br><br> | [Włącz](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) punktów końcowych usługi wymagane dla połączenia usługi ExpressRoute między źródłem i Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>Błąd upływu limitu czasu podczas migracji bazy danych MySQL do usługi Azure MySQL

Podczas migracji bazy danych MySQL do usługi Azure Database for MySQL wystąpienie przy użyciu usługi Azure Database Migration Service, migracja nie powiedzie się z powodu następującego błędu limitu czasu:

* **Błąd**: Błąd migracji bazy danych — nie można załadować pliku — nie można uruchomić proces ładowania pliku "n" RetCode: SQL_ERROR SqlState: HY000 NativeError: Komunikat 1205: [MySQL] [sterownik ODBC] [mysqld] blokady czas oczekiwania przekracza; Spróbuj ponownie uruchomić transakcji

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten błąd występuje, gdy migracja nie powiedzie się z powodu przekroczenia limitu czasu oczekiwania dla blokady, podczas migracji. | Rozważ zwiększenie wartości parametru server **"innodb_lock_wait_timeout"** . Dozwolone jest 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Błąd podczas łączenia ze źródłem programu SQL Server w przypadku korzystania z portów dynamicznych lub nazwane wystąpienie

Podczas próby nawiązania połączenia usługi Azure Database Migration Service do źródła programu SQL Server, które jest uruchamiane w nazwanym wystąpieniu lub port dynamiczny, połączenie kończy się niepowodzeniem z powodu następującego błędu:

* **Błąd**: -1 - Połączenie SQL nie powiodło się. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest prawidłowa i że program SQL Server jest skonfigurowany do zezwalania na połączenia zdalne. (Dostawca: Interfejsy sieciowe programu SQL, błąd: 26 — błąd podczas znajdowania/podanego wystąpienia)

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten problem występuje, gdy wystąpieniem programu SQL Server, który próbuje połączyć się z usługi Azure Database Migration Service, ma port dynamiczny albo jest używane nazwane wystąpienie. Usługi SQL Server Browser nasłuchuje na porcie UDP 1434 dla połączeń przychodzących do nazwanego wystąpienia lub podczas korzystania z portów dynamicznych. Port dynamiczny mogą ulec zmianie w każdym ponownym uruchomieniu usługi SQL Server. Możesz sprawdzić portów dynamicznych przypisanych do wystąpienia za pomocą konfiguracji sieci w programie SQL Server Configuration Manager.<br><br><br> |Sprawdź, czy usługi Azure Database Migration Service można połączyć ze źródła usługi SQL Server Browser na porcie UDP 1434 i wystąpienie programu SQL Server za pośrednictwem portu TCP przypisywany dynamicznie, jeśli ma to zastosowanie. |

## <a name="additional-known-issues"></a>Dodatkowe znanych problemów

* [Znane problemy dotyczące/migracja ograniczeń dotyczących migracji online do usługi Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Ograniczenia znanych problemów/migracja online migracji do usługi Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Ograniczenia znanych problemów/migracja online migracji do usługi Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Kolejne kroki

* Wyświetl artykuł [usługi migracji bazy danych programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Wyświetl artykuł [jak konfigurować parametry serwera w usłudze Azure Database for MySQL za pomocą witryny Azure portal](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Wyświetl artykuł [Przegląd wymagań wstępnych dotyczących używania usługi Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Zobacz [— często zadawane pytania dotyczące korzystania z usługi Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
