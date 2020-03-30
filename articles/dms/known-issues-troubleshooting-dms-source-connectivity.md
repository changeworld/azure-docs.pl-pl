---
title: Problemy z łączeniem źródłowych baz danych
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak rozwiązywać znane problemy/błędy skojarzone z łączeniem usługi migracji bazy danych Azure Database ze źródłowymi bazami danych.
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
ms.openlocfilehash: 3eef7476a729c7b83290b9d8d86ba06524bed72b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297095"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Troubleshoot DMS errors when connecting to source databases (Rozwiązywanie problemów z błędami usługi DMS podczas nawiązywania połączenia ze źródłowymi bazami danych)

Poniższy artykuł zawiera szczegółowe informacje na temat rozwiązywania potencjalnych problemów, które mogą wystąpić podczas podłączania usługi migracji bazy danych Azure Database (DMS) do źródłowej bazy danych. Każda poniższa sekcja odnosi się do określonego typu źródłowej bazy danych, zawierając listę błędu, który może wystąpić wraz ze szczegółami i łączami do informacji o rozwiązywaniu problemów z łącznością.

## <a name="sql-server"></a>SQL Server

Potencjalne problemy związane z łączeniem się ze źródłową bazą danych programu SQL Server i sposób ich rozwiązania znajdują się w poniższej tabeli.

| Błąd         | Szczegóły dotyczące przyczyn i rozwiązywania problemów |
| ------------- | ------------- |
| Połączenie SQL nie powiodło się. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest poprawna i czy program SQL Server jest skonfigurowany do zezwalania na połączenia zdalne.<br> | Ten błąd występuje, jeśli usługa nie może zlokalizować serwera źródłowego. Aby rozwiązać ten problem, zobacz artykuł [Błąd podczas łączenia się ze źródłowym programem SQL Server podczas korzystania z portu dynamicznego lub nazwanego wystąpienia](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Błąd 53** — połączenie SQL nie powiodło się. (Również w przypadku kodów błędów 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Ten błąd występuje, jeśli usługa nie może połączyć się z serwerem źródłowym. Aby rozwiązać ten problem, zapoznaj się z następującymi zasobami, a następnie spróbuj ponownie. <br><br>  [Interaktywny podręcznik użytkownika w celu rozwiązania problemu z łącznością](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Wymagania wstępne dotyczące migracji programu SQL Server do bazy danych SQL Azure](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Wymagania wstępne dotyczące migracji programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Błąd 18456** - Nie powiodło się logowanie.<br> | Ten błąd występuje, jeśli usługa nie może połączyć się ze źródłową bazą danych przy użyciu podanych poświadczeń T-SQL. Aby rozwiązać ten problem, sprawdź wprowadzone poświadczenia. Można również zapoznać się z [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) lub dokumentami rozwiązywania problemów wymienionymi w notatce poniżej tej tabeli, a następnie spróbować ponownie. |
| Nieprawidłowo sformułowana wartość{0}AccountName ' pod warunkiem. Oczekiwany format nazwy konta to Nazwa_domeny\Nazwa_użytkownika<br> | Ten błąd występuje, jeśli użytkownik wybierze uwierzytelnianie systemu Windows, ale podaje nazwę użytkownika w nieprawidłowym formacie. Aby rozwiązać ten problem, podaj nazwę użytkownika w odpowiednim formacie uwierzytelniania systemu Windows lub wybierz pozycję **Uwierzytelnianie SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Potencjalne problemy związane z połączeniem ze źródłową bazą danych AWS RDS MySQL i sposobu ich rozwiązania znajdują się w poniższej tabeli.

| Błąd         | Szczegóły dotyczące przyczyn i rozwiązywania problemów |
| ------------- | ------------- |
| **Błąd [2003]**[HY000] - połączenie nie powiodło się. BŁĄD [HY000] [Sterownik MySQL][ODBC x.x(w] Nie można połączyć się z serwerem MySQL na "{server}" (10060) | Ten błąd występuje, jeśli sterownik MySQL ODBC nie może połączyć się z serwerem źródłowym. Aby rozwiązać ten problem, zapoznaj się z dokumentami rozwiązywania problemów wymienionymi w notatce poniżej tej tabeli, a następnie spróbuj ponownie.<br> |
| **Błąd [2005]**[HY000] - połączenie nie powiodło się. BŁĄD [HY000] [MySQL][STEROWNIK ODBC x.x(w] Nieznany host serwera MySQL '{server}' | Ten błąd występuje, jeśli usługa nie może znaleźć hosta źródłowego w usługie RDS. Problem może być albo dlatego, że wymienione źródło nie istnieje lub występuje problem z infrastrukturą RDS. Aby rozwiązać ten problem, zapoznaj się z dokumentami rozwiązywania problemów wymienionymi w notatce poniżej tej tabeli, a następnie spróbuj ponownie.<br> |
| **Błąd [1045]**[HY000] - połączenie nie powiodło się. BŁĄD [HY000] [MySQL][ODBC x.x(w) sterownik] Odmowa dostępu dla użytkownika '{user}'@'{server}' (przy użyciu hasła: TAK) | Ten błąd występuje, jeśli sterownik MySQL ODBC nie może połączyć się z serwerem źródłowym z powodu nieprawidłowych poświadczeń. Sprawdź wprowadzone poświadczenia. Jeśli problem będzie się powtarzał, sprawdź, czy komputer źródłowy ma poprawne poświadczenia. Może być konieczne zresetowanie hasła w konsoli. Jeśli problem nadal występuje, zapoznaj się z dokumentami rozwiązywania problemów wymienionymi w notatce poniżej tej tabeli, a następnie spróbuj ponownie.<br> |
| **Błąd [9002]**[HY000] - połączenie nie powiodło się. BŁĄD [HY000] [Sterownik MySQL][ODBC x.x(w] Ten ciąg połączenia może nie być właściwy. Odwiedź portal, aby uzyskać referencje.| Ten błąd występuje, jeśli połączenie kończy się niepowodzeniem z powodu problemu z ciągiem połączenia. Sprawdź, czy podany ciąg połączenia jest prawidłowy. Aby rozwiązać ten problem, zapoznaj się z dokumentami rozwiązywania problemów wymienionymi w notatce poniżej tej tabeli, a następnie spróbuj ponownie.<br> |
| **Błąd w rejestrowaniu binarnym. Zmienna binlog_format ma wartość "{value}". Zmień go na "wiersz".** | Ten błąd występuje, jeśli występuje błąd w rejestrowaniu binarnym; zmienna binlog_format ma niewłaściwą wartość. Aby rozwiązać ten problem, zmień binlog_format w grupie parametrów na "ROW", a następnie uruchom ponownie wystąpienie. Aby uzyskać więcej informacji, zobacz [binary logging options and variables](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) or [AWS RDS MySQL Database Log Files documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Aby uzyskać więcej informacji na temat rozwiązywania problemów związanych z łączeniem się ze źródłową bazą danych usługi AWS RDS MySQL, zobacz następujące zasoby:
> * [Rozwiązywanie problemów z łącznością usług Pulpitu zdalnego amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Jak rozwiązać problemy z połączeniem się z wystąpieniem bazy danych Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Potencjalne problemy związane z połączeniem ze źródłową bazą danych AWS RDS PostgreSQL i sposobu ich rozwiązania znajdują się w poniższej tabeli.

| Błąd         | Szczegóły dotyczące przyczyn i rozwiązywania problemów |
| ------------- | ------------- |
| **Błąd [101]**[08001] - połączenie nie powiodło się. BŁĄD [08001] upłynął limit czasu. | Ten błąd występuje, jeśli sterownik Postgres nie może połączyć się z serwerem źródłowym. Aby rozwiązać ten problem, zapoznaj się z dokumentami rozwiązywania problemów wymienionymi w notatce poniżej tej tabeli, a następnie spróbuj ponownie. |
| **Błąd: parametr wal_level ma wartość "{value}". Zmień go na "logiczny", aby umożliwić replikację.** | Ten błąd występuje, jeśli parametr wal_level ma nieprawidłową wartość. Aby rozwiązać ten problem, zmień rds.logical_replication w grupie parametrów na 1, a następnie uruchom ponownie wystąpienie. Aby uzyskać więcej informacji, zobacz Wymagania wstępne dotyczące [migracji do usługi Azure PostgreSQL przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) lub [PostgreSQL w amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Aby uzyskać więcej informacji na temat rozwiązywania problemów związanych z łączeniem się ze źródłową bazą danych AWS RDS PostgreSQL, zobacz następujące zasoby:
> * [Rozwiązywanie problemów z łącznością usług Pulpitu zdalnego amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Jak rozwiązać problemy z połączeniem się z wystąpieniem bazy danych Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>Serwer SQL usługi AWS RDS

Potencjalne problemy związane z połączeniem ze źródłową bazą danych programu AWS RDS SQL Server i sposób ich rozwiązania znajdują się w poniższej tabeli.

| Błąd         | Szczegóły dotyczące przyczyn i rozwiązywania problemów |
| ------------- | ------------- |
| **Błąd 53** — połączenie SQL nie powiodło się. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub nie był dostępny. Sprawdź, czy nazwa wystąpienia jest poprawna i czy program SQL Server jest skonfigurowany do zezwalania na połączenia zdalne. (dostawca: dostawca nazwanych potoków, błąd: 40 - Nie można otworzyć połączenia z programem SQL Server | Ten błąd występuje, jeśli usługa nie może połączyć się z serwerem źródłowym. Aby rozwiązać ten problem, zapoznaj się z dokumentami rozwiązywania problemów wymienionymi w notatce poniżej tej tabeli, a następnie spróbuj ponownie. |
| **Błąd 18456** - Nie powiodło się logowanie. Nie powiodło się logowanie użytkownika "{user}" | Ten błąd występuje, jeśli usługa nie może połączyć się ze źródłową bazą danych z podanymi poświadczeniami T-SQL. Aby rozwiązać ten problem, sprawdź wprowadzone poświadczenia. Można również zapoznać się z [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) lub dokumentami rozwiązywania problemów wymienionymi w notatce poniżej tej tabeli i spróbować ponownie. |
| **Błąd 87** - Parametry połączenia są nieprawidłowe. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest poprawna i czy program SQL Server jest skonfigurowany do zezwalania na połączenia zdalne. (dostawca: interfejsy sieciowe SQL, błąd: 25 - Parametry połączenia są nieprawidłowe) | Ten błąd występuje, jeśli usługa nie może połączyć się z serwerem źródłowym z powodu nieprawidłowego ciągu połączenia. Aby rozwiązać ten problem, sprawdź podany ciąg połączenia. Jeśli problem będzie się powtarzał, zapoznaj się z dokumentami rozwiązywania problemów wymienionymi w notatce poniżej tej tabeli, a następnie spróbuj ponownie. |
| **Błąd — certyfikat serwera nie jest zaufany.** Połączenie zostało pomyślnie nawiązane z serwerem, ale następnie wystąpił błąd podczas procesu logowania. (dostawca: dostawca SSL, błąd: 0 - Łańcuch certyfikatów został wystawiony przez urząd, który nie jest zaufany.) | Ten błąd występuje, jeśli używany certyfikat nie jest zaufany. Aby rozwiązać ten problem, należy znaleźć certyfikat, któremu można ufać, a następnie włączyć go na serwerze. Alternatywnie można wybrać opcję Certyfikat zaufania podczas nawiązywania połączenia. Tę czynność należy podjąć tylko wtedy, gdy znasz używany certyfikat i ufasz mu. <br> Połączenia TLS, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia — są podatne na ataki typu man-in-the-middle. Nie należy polegać na tls przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są podłączone do Internetu. <br> Aby uzyskać więcej informacji, zobacz [Korzystanie z SSL z wystąpieniem lub samouczkiem bazy danych programu Microsoft SQL Server:](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) [migrowanie serwera SQL Server usług pulpitu zdalnego na platformę Azure przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Błąd 300** — użytkownik nie ma wymaganych uprawnień. Uprawnienia VIEW SERVER STATE zostały odrzucone w obiekcie "{server}", bazie danych "{database}" | Ten błąd występuje, jeśli użytkownik nie ma uprawnień do przeprowadzania migracji. Aby rozwiązać ten problem, zapoznaj się [z grant uprawnień serwera — Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) lub [samouczek: Migrowanie programu RDS SQL Server na platformę Azure przy użyciu usługi DMS,](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) aby uzyskać więcej informacji. |

> [!NOTE]
> Aby uzyskać więcej informacji na temat rozwiązywania problemów związanych z łączeniem się ze źródłem usług pulpitu zdalnego programu AWS SQL Server, zobacz następujące zasoby:
>
> * [Usuwanie błędów łączności z programem SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Jak rozwiązać problemy z połączeniem się z wystąpieniem bazy danych Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Znane problemy

* [Znane problemy/ograniczenia migracji z migracjami online do bazy danych SQL usługi Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Znane problemy/ograniczenia migracji z migracjami online do usługi Azure Database dla mysql](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Znane problemy/ograniczenia migracji z migracjami online do usługi Azure Database dla postgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Następne kroki

* Zobacz artykuł [Usługa migracji bazy danych Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Zobacz artykuł [Jak skonfigurować parametry serwera w usłudze Azure Database for MySQL przy użyciu portalu Azure.](https://docs.microsoft.com/azure/mysql/howto-server-parameters)
* Zobacz artykuł [Omówienie wymagań wstępnych dotyczących korzystania z usługi migracji bazy danych Platformy Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Zobacz [często](https://docs.microsoft.com/azure/dms/faq)zadawane pytania dotyczące korzystania z usługi migracji bazy danych platformy Azure .
