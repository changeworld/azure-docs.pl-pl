---
title: Typowe problemy — usługa migracji bazy danych platformy Azure
description: Dowiedz się, jak rozwiązywać znane problemy/błędy skojarzone z usługą migracji bazy danych Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: c5d2ad481124f5ae048d010cdf632ee661bbd6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649111"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Rozwiązywanie typowych problemów i błędów usługi migracji bazy danych platformy Azure

W tym artykule opisano niektóre typowe problemy i błędy, które użytkownicy usługi migracji bazy danych platformy Azure mogą natknąć. Artykuł zawiera również informacje dotyczące sposobu rozwiązywania tych problemów i błędów.

## <a name="migration-activity-in-queued-state"></a>Aktywność migracji w stanie kolejki

Podczas tworzenia nowych działań w projekcie usługi migracji bazy danych azure, działania pozostają w stanie kolejki.

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten problem występuje, gdy wystąpienie usługi migracji bazy danych platformy Azure osiągnęło maksymalną pojemność dla bieżących zadań, które są uruchamiane jednocześnie. Każde nowe działanie jest umieszczane w kolejce, dopóki pojemność nie stanie się dostępna. | Sprawdź poprawność wystąpienia usługi migracji danych ma uruchomiony działania w projektach. Można kontynuować tworzenie nowych działań, które automatycznie są dodawane do kolejki do wykonania. Jak tylko którykolwiek z istniejących działań uruchomionych zakończyć, następne działanie w kolejce rozpoczyna się uruchomiony i stan zmienia się stan uruchamiania automatycznie. Nie musisz podejmować żadnych dodatkowych działań, aby rozpocząć migrację aktywności w kolejce.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maksymalna liczba baz danych wybranych do migracji

Następujący błąd występuje podczas tworzenia działania dla projektu migracji bazy danych w celu przeniesienia do usługi Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL Database:

* **Błąd:** Błąd sprawdzania poprawności ustawień migracji", "errorDetail":"Do migracji wybrano więcej niż max liczba "4" obiektów "Bazy danych".

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd jest wyświetlany po wybraniu więcej niż czterech baz danych dla pojedynczego działania migracji. Obecnie każde działanie migracji jest ograniczone do czterech baz danych. | Wybierz cztery lub mniej baz danych na działanie migracji. Jeśli musisz przeprowadzić migrację więcej niż czterech baz danych równolegle, aprowizować inne wystąpienie usługi migracji bazy danych platformy Azure. Obecnie każda subskrypcja obsługuje maksymalnie dwa wystąpienia usługi migracji bazy danych platformy Azure.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Błędy migracji MySQL do usługi Azure MySQL z błędami odzyskiwania

Podczas migracji z MySQL do usługi Azure Database dla MySQL przy użyciu usługi migracji bazy danych Azure działanie migracji kończy się niepowodzeniem z następującym błędem:

* **Błąd**: Błąd migracji bazy danych — zadanie "TaskID" zostało zawieszone z powodu kolejnych błędów odzyskiwania .error: Database migration error - Task 'TaskID' was suspended due to [n] kolejne recovery failures.

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd może wystąpić, gdy użytkownik wykonujący migrację nie ma roli ReplikacjaAdmin i/lub uprawnień klienta replikacji, replikacji replikacji i super (wersje starsze niż MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Upewnij się, że [uprawnienia wstępne](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) dla konta użytkownika są dokładnie skonfigurowane w usłudze Azure Database for MySQL. Na przykład można wykonać następujące kroki, aby utworzyć użytkownika o nazwie "migrateuser" z wymaganymi uprawnieniami:<br>1. TWORZENIE MIGRATEUSER@ UŻYTKOWNIKA"% ZIDENTYFIKOWANE PRZEZ "tajne"; <br>2. przyznać wszystkie przywileje db_name.* "migrateuser"@'%% określone jako "tajne"; powtórz ten krok, aby udzielić dostępu do większej liczby baz danych <br>3. Przyznaj slave replikacji na *.* "migrateuser"@'%% oznaczony jako "tajny";<br>4. Udziel klienta replikacji na *.* "migrateuser"@'%% oznaczony jako "tajny";<br>5. Równanie uprawnień; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Błąd podczas próby zatrzymania usługi migracji bazy danych Azure

Podczas zatrzymywania wystąpienia usługi migracji bazy danych Azure pojawia się następujący błąd:

* **Błąd:** Nie można zatrzymać usługi. Error: {'error':{'code':'InvalidRequest','message':'Co najmniej jedno działanie jest aktualnie uruchomione. Aby zatrzymać usługę, poczekaj, aż działania zostaną zakończone, lub zatrzymaj te działania ręcznie i spróbuj ponownie.'}}

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd jest wyświetlany, gdy wystąpienie usługi, które próbujesz zatrzymać, obejmuje działania, które są nadal uruchomione lub obecne w projektach migracji. <br><br><br><br><br><br> | Upewnij się, że nie ma żadnych działań uruchomionych w wystąpieniu usługi migracji bazy danych platformy Azure, którą próbujesz zatrzymać. Można również usunąć działania lub projekty przed podjęciem próby zatrzymania usługi. Poniższe kroki ilustrują sposób usuwania projektów w celu oczyszczenia wystąpienia usługi migracji przez usunięcie wszystkich uruchomionych zadań:<br>1. Install-Module -Nazwa AzureRM.DataMigration <br>2. Konto logowania AzureRmAccount <br>3. Select-AzureRmSubscription -SubscriptionName "\<subName>" <br> 4. Usuń-AzureRmDataMigrationProject \<-Nazwa projektuName> -ResourceGroupName \<rgName> -ServiceName \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Błąd podczas próby uruchomienia usługi migracji bazy danych Platformy Azure

Podczas uruchamiania wystąpienia usługi migracji bazy danych Azure pojawia się następujący błąd:

* **Błąd:** Uruchomienie usługi nie powiedzie się. Błąd: {'errorDetail':'Usługa nie została uruchomiony, skontaktuj się z pomocą techniczną firmy Microsoft'}

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd jest wyświetlany, gdy poprzednie wystąpienie nie powiodło się wewnętrznie. Ten błąd występuje rzadko, a zespół inżynierów jest świadomy tego. <br> | Usuń wystąpienie usługi, której nie można uruchomić, a następnie aprowizuj nową, aby ją zastąpić. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Błąd podczas przywracania bazy danych podczas migracji sql do wystąpienia zarządzanego usługi Azure SQL DB

Podczas migracji online z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database funkcja cutover kończy się niepowodzeniem z następującym błędem:

* **Błąd:** Operacja przywracania nie powiodła się dla operacji o identyfikatorze "operationId". Kod "AuthorizationFailed", Komunikat "Klient 'clientId' z identyfikatorem obiektu 'objectId' nie ma autoryzacji do wykonywania akcji "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read" przez zakres "/subscriptions/subscriptionId".'.

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten błąd wskazuje, że podmiot zabezpieczeń aplikacji używany do migracji online z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database nie ma uprawnień contribute w ramach subskrypcji. Niektóre wywołania interfejsu API z wystąpieniem zarządzanym w chwili obecnej wymagają tego uprawnienia do subskrypcji dla operacji przywracania. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Użyj `Get-AzureADServicePrincipal` polecenia cmdlet programu `-ObjectId` PowerShell z komunikatem o błędzie dostępnym, aby wyświetlić nazwę wyświetlaną używanego identyfikatora aplikacji.<br><br> Sprawdź poprawność uprawnień do tej aplikacji i upewnij się, że ma [rolę współautora](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) na poziomie subskrypcji. <br><br> Zespół inżynierów usługi migracji bazy danych azure pracuje, aby ograniczyć wymagany dostęp z bieżącej roli współtworzenia w ramach subskrypcji. Jeśli masz wymagania biznesowe, które nie zezwalają na korzystanie z roli contribute, skontaktuj się z pomocą techniczną platformy Azure, aby uzyskać dodatkową pomoc. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Błąd podczas usuwania karty sieciowej skojarzonej z usługą migracji bazy danych Azure

Podczas próby usunięcia karty interfejsu sieciowego skojarzonej z usługą migracji bazy danych Azure próba usunięcia kończy się niepowodzeniem z powodu tego błędu:

* **Błąd:** Nie można usunąć karty sieciowej skojarzonej z usługą migracji bazy danych azure ze względu na usługę DMS wykorzystującą kartę sieciową

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten problem występuje, gdy wystąpienie usługi migracji bazy danych azure może nadal być obecny i zużywa karty sieciowej. <br><br><br><br><br><br><br><br> | Aby usunąć tę kartę sieciową, usuń wystąpienie usługi DMS, które automatycznie usuwa kartę sieciową używaną przez usługę.<br><br> **Ważne:** Upewnij się, że usunięte wystąpienie usługi migracji bazy danych platformy Azure nie ma uruchomionych działań.<br><br> Po usunięciu wszystkich projektów i działań skojarzonych z wystąpieniem usługi migracji bazy danych Azure można usunąć wystąpienie usługi. Karta sieciowa używana przez wystąpienie usługi jest automatycznie czyszczona jako część usuwania usługi. |

## <a name="connection-error-when-using-expressroute"></a>Błąd połączenia podczas korzystania z usługi ExpressRoute

Podczas próby nawiązania połączenia ze źródłem w kreatorze projektu usługi Azure Database Migration Service, połączenie nie powiedzie się po dłuższym czasie oczekiwania, jeśli źródło używa usługi ExpressRoute dla połączeń.

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Podczas korzystania z [usługi ExpressRoute](https://azure.microsoft.com/services/expressroute/)usługa migracji bazy danych Azure [database wymaga](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) inicjowania obsługi administracyjnej trzech punktów końcowych usługi w podsieci sieci wirtualnej skojarzonej z usługą:<br> -- Punkt końcowy usługi Service Bus<br> -- Punkt końcowy magazynu<br> -- Docelowy punkt końcowy bazy danych (np. punkt końcowy SQL, punkt końcowy usługi Cosmos DB)<br><br><br><br><br> | [Włącz](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) wymagane punkty końcowe usługi dla łączności usługi ExpressRoute między źródłem a usługą Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Błąd limitu czasu oczekiwania blokady podczas migrowania bazy danych MySQL do bazy danych Platformy Azure dla mysql

Podczas migracji bazy danych MySQL do usługi Azure Database dla wystąpienia MySQL za pośrednictwem usługi migracji bazy danych Azure migracja kończy się niepowodzeniem z następującym błędem limitu czasu oczekiwania blokady:

* **Błąd**: Błąd migracji bazy danych - Nie można załadować pliku - Nie można uruchomić procesu ładowania pliku 'n' RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 Komunikat: [MySQL][STEROWNIK ODBC][mysqld] Limit czasu oczekiwania blokady przekroczony; spróbuj ponownie uruchomić transakcję

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten błąd występuje, gdy migracja kończy się niepowodzeniem z powodu limitu czasu oczekiwania blokady podczas migracji. | Rozważ zwiększenie wartości parametru serwera **"innodb_lock_wait_timeout"**. Najwyższa dozwolona wartość to 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Błąd podczas łączenia się ze źródłowym programem SQL Server podczas korzystania z portu dynamicznego lub wystąpienia o nazwie

Podczas próby połączenia usługi migracji bazy danych Platformy Azure ze źródłem programu SQL Server, które działa na nazwanym wystąpieniu lub porcie dynamicznym, połączenie kończy się niepowodzeniem z powodu tego błędu:

* **Błąd**: -1 - połączenie SQL nie powiodło się. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest poprawna i czy program SQL Server jest skonfigurowany do zezwalania na połączenia zdalne. (dostawca: interfejsy sieciowe SQL, błąd: 26 - Określono błąd lokalizowania serwera/wystąpienia)

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten problem występuje, gdy źródłowe wystąpienie programu SQL Server, z którym usługa Azure Database Migration Service próbuje się połączyć, ma port dynamiczny lub używa nazwanego wystąpienia. Usługa SQL Server Browser nasłuchuje portu UDP 1434 dla połączeń przychodzących z nazwanym wystąpieniem lub podczas korzystania z portu dynamicznego. Port dynamiczny może ulec zmianie przy każdym ponownym uruchomieniu usługi PROGRAMU SQL Server. Można sprawdzić port dynamiczny przypisany do wystąpienia za pośrednictwem konfiguracji sieci w programie SQL Server Configuration Manager.<br><br><br> |Sprawdź, czy usługa migracji bazy danych Platformy Azure może łączyć się ze źródłową usługą przeglądarki programu SQL Server na porcie UDP 1434 i wystąpieniem programu SQL Server za pośrednictwem dynamicznie przypisanego portu TCP, stosownie do przypadku. |

## <a name="additional-known-issues"></a>Dodatkowe znane problemy

* [Znane problemy/ograniczenia migracji z migracjami online do bazy danych SQL usługi Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Znane problemy/ograniczenia migracji z migracjami online do usługi Azure Database dla mysql](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Znane problemy/ograniczenia migracji z migracjami online do usługi Azure Database dla postgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Następne kroki

* Zobacz artykuł [Usługa migracji bazy danych Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Zobacz artykuł [Jak skonfigurować parametry serwera w usłudze Azure Database for MySQL przy użyciu portalu Azure.](https://docs.microsoft.com/azure/mysql/howto-server-parameters)
* Zobacz artykuł [Omówienie wymagań wstępnych dotyczących korzystania z usługi migracji bazy danych Platformy Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Zobacz [często](https://docs.microsoft.com/azure/dms/faq)zadawane pytania dotyczące korzystania z usługi migracji bazy danych platformy Azure .
