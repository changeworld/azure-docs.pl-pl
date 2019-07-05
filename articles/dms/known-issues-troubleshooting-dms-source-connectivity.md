---
title: Artykuł dotyczący rozwiązywania problemów/błędy związane z nawiązaniem połączenia z usługi Azure Database Migration Service, aby źródłowe bazy danych znane | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu rozwiązywania problemów ze znanych problemów/błędy związane z nawiązaniem połączenia z usługi Azure Database Migration Service, aby źródłowe bazy danych.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: a4ebd1d4c85631cc6ebc1f5787e7545b78d62b5e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462852"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Rozwiązywanie problemów z błędami DMS, podczas nawiązywania połączenia źródłowe bazy danych

Następujący artykuł zawiera szczegółowe informacje o sposobie rozwiązania potencjalnych problemów, które mogą wystąpić podczas nawiązywania połączenia z usługi Azure Database Migration Service (DMS) do źródłowej bazy danych. Każda sekcja poniżej odnosi się do określonego typu źródłowej bazy danych, lista błąd może wystąpić wraz z szczegółów i linki do informacji o tym, jak rozwiązywać problemy z łącznością.

## <a name="sql-server"></a>Oprogramowanie SQL Server

Potencjalne problemy skojarzone z nawiązywania połączenia źródłowej bazy danych programu SQL Server, i sposoby ich rozwiązywania znajdują się w poniższej tabeli.

| Błąd         | Przyczyna i rozwiązywanie problemów z szczegółów |
| ------------- | ------------- |
| Połączenia z serwerem SQL nie powiodło się. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest poprawny i że program SQL Server jest skonfigurowany do zezwalania na połączenia zdalne.<br> | Ten błąd występuje, jeśli usługa nie może zlokalizować serwer źródłowy. Aby rozwiązać ten problem, zobacz artykuł [błąd podczas łączenia ze źródłem programu SQL Server w przypadku korzystania z portów dynamicznych lub nazwane wystąpienie](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Błąd 53** — połączenie SQL nie powiodło się. (Ponadto w przypadku błędu kody 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Ten błąd występuje, jeśli usługa nie można połączyć z serwera źródłowego. Aby rozwiązać ten problem, zobacz następujące zasoby, a następnie spróbuj ponownie. <br><br>  [Przewodnik użytkownika interaktywnego, aby rozwiązać problem z łącznością](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Wymagania wstępne dotyczące migracji programu SQL Server do usługi Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Wymagania wstępne dotyczące migracji programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Błąd 18456** — logowanie nie powiodło się.<br> | Ten błąd występuje, jeśli usługa nie może nawiązać połączenia źródłowej bazy danych przy użyciu podanych poświadczeń języka T-SQL. Aby rozwiązać ten problem, Sprawdź wprowadzone poświadczenia. Może również dotyczyć [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) lub do rozwiązywania problemów z dokumentów, na liście Uwaga poniżej tabeli, a następnie spróbuj ponownie. |
| Źle sformułowaną wartość AccountName "{0}" podano. Oczekiwany format dla wartości AccountName to Nazwa_domeny\nazwa_użytkownika<br> | Ten błąd występuje, gdy użytkownik wybierze uwierzytelniania Windows, ale zawiera nazwę użytkownika w nieprawidłowym formacie. Aby rozwiązać ten problem, podaj nazwę użytkownika w prawidłowym formacie do uwierzytelniania Windows lub wybierz **uwierzytelniania SQL**. |

## <a name="aws-rds-mysql"></a>Usługi AWS RDS MySQL

Potencjalne problemy skojarzone z nawiązywania połączenia źródłowej bazy danych usługi AWS RDS MySQL, i sposoby ich rozwiązywania znajdują się w poniższej tabeli.

| Błąd         | Przyczyna i rozwiązywanie problemów z szczegółów |
| ------------- | ------------- |
| **Błąd [2003]** [HY000] - połączenie nie powiodło się. Błąd [HY000] [MySQL] [sterownik ODBC x.x(w)] nie może połączyć się z serwerem MySQL na "{server}" (10060) | Ten błąd występuje, jeśli sterownik MySQL ODBC nie można połączyć z serwera źródłowego. Aby rozwiązać ten problem, można znaleźć w dokumentach rozwiązywania problemów wymienione w uwagi pod tą tabelą, a następnie spróbuj ponownie.<br> |
| **Błąd [2005]** [HY000] - połączenie nie powiodło się. Błąd [HY000] hosta serwera MySQL nieznany [MySQL] [sterownik ODBC x.x(w)] "{server}" | Ten błąd występuje, jeśli usługa nie może znaleźć hosta źródłowego na RDS. Ten problem może być źródłowych na liście nie istnieje lub jest problem z infrastruktury usług pulpitu zdalnego. Aby rozwiązać ten problem, można znaleźć w dokumentach rozwiązywania problemów wymienione w uwagi pod tą tabelą, a następnie spróbuj ponownie.<br> |
| **Błąd [1045]** [HY000] - połączenie nie powiodło się. Błąd [HY000] [MySQL] [sterownik ODBC x.x(w)] dostępu dla użytkownika {user}'@'{server} (przy użyciu hasła: YES) | Ten błąd występuje, jeśli sterownik MySQL ODBC nie można połączyć z serwera źródłowego z powodu nieprawidłowych poświadczeń. Sprawdź poświadczenia, które zostały wprowadzone. Jeśli problem będzie się powtarzać, sprawdź, czy ten komputer źródłowy ma poprawne poświadczenia. Może być konieczne zresetowanie hasła w konsoli. Jeśli problem nadal występuje, zobacz dokumenty dotyczące rozwiązywania problemów na liście uwagi pod tą tabelą, a następnie spróbuj ponownie.<br> |
| **Błąd [9002]** [HY000] - połączenie nie powiodło się. Błąd [HY000] [MySQL] [sterownik ODBC x.x(w)] Parametry połączenia mogą nie być odpowiednie. Odwiedź portal odwołań.| Ten błąd występuje, jeśli połączenie kończy się niepowodzeniem z powodu problemu z parametrami połączenia. Upewnij się, w podanym ciągu połączenia jest prawidłowy. Aby rozwiązać ten problem, można znaleźć w dokumentach rozwiązywania problemów wymienione w uwagi pod tą tabelą, a następnie spróbuj ponownie.<br> |
| **Wystąpił błąd podczas rejestrowania binarnego. Zmienna binlog_format ma wartość "{value}". Można go zmienić na "wiersz".** | Ten błąd występuje, jeśli występuje błąd podczas rejestrowania binarnego; Zmienna binlog_format ma nieprawidłową wartość. Aby rozwiązać ten problem, zmień binlog_format w grupie parametru "Wiersz", a następnie wykonaj ponowny rozruch wystąpienia. Aby uzyskać więcej informacji, zobacz do [zmiennych i opcje rejestrowania binarnego](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) lub [dokumentacji pliki dziennika dla bazy danych MySQL usługi AWS RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Aby uzyskać więcej informacji na temat rozwiązywania problemów związanych z nawiązywania połączenia z usługi AWS RDS MySQL źródłową bazę danych zobacz następujące zasoby:
> * [Rozwiązywanie problemów z problemów z połączeniem pulpitu zdalnego Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Jak rozwiązać problemy z połączeniem z Moje wystąpienia bazy danych Amazon usług pulpitu zdalnego?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>Usługi AWS RDS PostgreSQL

Potencjalne problemy skojarzone z nawiązywania połączenia źródłowej bazy danych usługi AWS RDS PostgreSQL, i sposoby ich rozwiązywania znajdują się w poniższej tabeli.

| Błąd         | Przyczyna i rozwiązywanie problemów z szczegółów |
| ------------- | ------------- |
| **Błąd [101]** [08001] - połączenie nie powiodło się. Upłynął limit czasu błąd [08001]. | Ten błąd występuje, jeśli sterownik Postgres nie można połączyć z serwera źródłowego. Aby rozwiązać ten problem, można znaleźć w dokumentach rozwiązywania problemów wymienione w uwagi pod tą tabelą, a następnie spróbuj ponownie. |
| **Błąd: Wal_level parametr ma wartość "{value}". Można go zmienić na logiczne, aby umożliwić replikacji.** | Ten błąd występuje, jeśli wal_level parametr ma nieprawidłową wartość. Aby rozwiązać ten problem, zmień rds.logical_replication w grupie parametru na wartość 1, a następnie wykonaj ponowny rozruch wystąpienia. Aby uzyskać więcej informacji, zobacz do [wymagania wstępne dotyczące migracji do usługi Azure PostgreSQL przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) lub [bazy danych PostgreSQL na Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Aby uzyskać więcej informacji na temat rozwiązywania problemów związanych z nawiązywania połączenia z usługi AWS RDS PostgreSQL źródłową bazę danych zobacz następujące zasoby:
> * [Rozwiązywanie problemów z problemów z połączeniem pulpitu zdalnego Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Jak rozwiązać problemy z połączeniem z Moje wystąpienia bazy danych Amazon usług pulpitu zdalnego?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Potencjalne problemy skojarzone z nawiązywania połączenia źródłowej bazy danych usługi AWS RDS programu SQL Server, i sposoby ich rozwiązywania znajdują się w poniższej tabeli.

| Błąd         | Przyczyna i rozwiązywanie problemów z szczegółów |
| ------------- | ------------- |
| **Błąd 53** — połączenie SQL nie powiodło się. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie znaleziono lub nie była dostępna. Sprawdź, czy nazwa wystąpienia jest poprawny i że program SQL Server jest skonfigurowany do zezwalania na połączenia zdalne. (Dostawca: Dostawca nazwanych potoków, błąd: 40 - nie można otworzyć połączenia z programem SQL Server | Ten błąd występuje, jeśli usługa nie można połączyć z serwera źródłowego. Aby rozwiązać ten problem, można znaleźć w dokumentach rozwiązywania problemów wymienione w uwagi pod tą tabelą, a następnie spróbuj ponownie. |
| **Błąd 18456** — logowanie nie powiodło się. Nie można zalogować użytkownika "{user}" | Ten błąd występuje, jeśli usługa nie może połączyć się z źródłowej bazy danych przy użyciu podanych poświadczeń języka T-SQL. Aby rozwiązać ten problem, Sprawdź wprowadzone poświadczenia. Może również dotyczyć [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) lub do rozwiązywania problemów z dokumentów, na liście Uwaga poniżej tabeli i spróbuj ponownie. |
| **Błąd 87** — parametry połączenia są nieprawidłowe. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest poprawny i że program SQL Server jest skonfigurowany do zezwalania na połączenia zdalne. (Dostawca: Interfejsy sieciowe programu SQL, błąd: 25 — ciąg połączenia jest nieprawidłowy) | Ten błąd występuje, jeśli usługa nie może połączyć się z serwera źródłowego ze względu na nieprawidłowe parametry połączenia. Aby rozwiązać ten problem, sprawdź podane parametry połączenia. Jeśli problem będzie się powtarzać, można znaleźć w dokumentach rozwiązywania problemów wymienione w uwagi pod tą tabelą, a następnie spróbuj ponownie. |
| **Błąd — certyfikat serwera nie jest zaufany.** Pomyślnie ustanowiono połączenie z serwerem, ale wystąpił błąd podczas procesu logowania. (Dostawca: Dostawca protokołu SSL, błąd: 0 — łańcuch certyfikatów został wystawiony przez urząd który nie jest zaufany.) | Ten błąd występuje, jeśli certyfikat używany nie jest zaufany. Aby rozwiązać ten problem, należy można znaleźć certyfikatu, który jest zaufany, a następnie włączyć ją na serwerze. Alternatywnie można wybrać opcję certyfikat relacji zaufania podczas nawiązywania połączenia. Tę akcję należy podjąć, tylko wtedy, gdy użytkownicy zaznajomieni z certyfikatem używanym jej ufasz. <br> Połączenia SSL, które są szyfrowane przy użyciu certyfikatu z podpisem własnym nie zapewniają wysoki poziom bezpieczeństwa, ponieważ są one podatne na ataki typu man-in--middle. Nie należy polegać na protokołu SSL przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem. <br> Aby uzyskać więcej informacji, zobacz do [przy użyciu protokołu SSL z wystąpienia bazy danych programu Microsoft SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) lub [samouczka: Migrowanie usług pulpitu zdalnego programu SQL Server na platformie Azure przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Błąd 300** — użytkownik nie ma wymaganych uprawnień. Nie przyznano uprawnienia VIEW SERVER STATE dla obiektu "{server}", baza danych "{baza danych}" | Ten błąd występuje, jeśli użytkownik nie ma uprawnień do przeprowadzenia migracji. Aby rozwiązać ten problem, zapoznaj się [UDZIEL uprawnień serwera - języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) lub [samouczka: Migrowanie usług pulpitu zdalnego programu SQL Server na platformie Azure przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) Aby uzyskać więcej informacji. |

> [!NOTE]
> Aby uzyskać więcej informacji na temat rozwiązywania problemów związanych z łączenia ze źródłem usługi AWS RDS programu SQL Server zobacz następujące zasoby:
>
> * [Usuwanie błędów łączności z programem SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Jak rozwiązać problemy z połączeniem z Moje wystąpienia bazy danych Amazon usług pulpitu zdalnego?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Znane problemy

* [Znane problemy dotyczące/migracja ograniczeń dotyczących migracji online do usługi Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Ograniczenia znanych problemów/migracja online migracji do usługi Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Ograniczenia znanych problemów/migracja online migracji do usługi Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Kolejne kroki

* Wyświetl artykuł [usługi migracji bazy danych programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Wyświetl artykuł [jak konfigurować parametry serwera w usłudze Azure Database for MySQL za pomocą witryny Azure portal](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Wyświetl artykuł [Przegląd wymagań wstępnych dotyczących używania usługi Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Zobacz [— często zadawane pytania dotyczące korzystania z usługi Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
