---
title: Zabezpieczanie bazy danych
description: Porady dotyczące zabezpieczania bazy danych w Azure SQL Data Warehouse tworzenia rozwiązań.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c51a945bae7cc0b03c219bc041d64f4703baef19
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692578"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Zabezpieczanie bazy danych w SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Przegląd zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Uwierzytelnianie](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Szyfrowanie (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

W tym artykule przedstawiono podstawowe informacje dotyczące zabezpieczania bazy danych Azure SQL Data Warehouse. W szczególności ten artykuł ułatwia rozpoczęcie pracy z zasobami w celu ograniczania dostępu, ochrony danych i monitorowania działań w bazie danych.

## <a name="connection-security"></a>Zabezpieczenia połączeń
Zabezpieczenia połączeń dotyczą sposobu ograniczania i zabezpieczania połączeń z bazą danych przy użyciu reguł zapory i szyfrowania połączeń.

Reguły zapory są używane zarówno przez serwer, jak i przez bazę danych do odrzucania prób połączenia z adresów IP, które nie zostały jawnie wymienione na liście dozwolonych hostów. Aby zezwolić na połączenia z publicznego adresu IP aplikacji lub komputera klienckiego, należy najpierw utworzyć regułę zapory na poziomie serwera przy użyciu Azure Portal, interfejsu API REST lub programu PowerShell. Najlepszym rozwiązaniem jest maksymalne ograniczenie zakresu adresów IP przepuszczanych przez zaporę serwera.  Aby uzyskać dostęp do Azure SQL Data Warehouse z komputera lokalnego, upewnij się, że Zapora w sieci i komputer lokalny zezwalają na komunikację wychodzącą na porcie TCP 1433.  

SQL Data Warehouse używa reguł zapory na poziomie serwera. Nie obsługuje reguł zapory na poziomie bazy danych. Aby uzyskać więcej informacji, zobacz [Azure SQL Database firewall][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

Połączenia z SQL Data Warehouse są domyślnie szyfrowane.  Modyfikowanie ustawień połączenia w celu wyłączenia szyfrowania jest ignorowane.

## <a name="authentication"></a>Authentication
Uwierzytelnianie to sposób potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. SQL Data Warehouse obecnie obsługuje uwierzytelnianie SQL Server przy użyciu nazwy użytkownika i hasła oraz Azure Active Directory. 

Po utworzeniu serwera logicznego bazy danych należy określić nazwę logowania „server admin” przy użyciu nazwy użytkownika i hasła. Przy użyciu tych poświadczeń można uwierzytelniać się w dowolnej bazie danych na tym serwerze jako właściciel bazy danych lub "dbo" za pośrednictwem SQL Server uwierzytelniania.

Jednak najlepszym rozwiązaniem jest użycie przez użytkowników w organizacji innego konta do uwierzytelniania. W ten sposób można ograniczyć uprawnienia przyznane aplikacji i zmniejszyć ryzyko złośliwych działań w przypadku, gdy kod aplikacji jest narażony na ataki iniekcji SQL. 

Aby utworzyć SQL Server uwierzytelnionego użytkownika, nawiąż połączenie z bazą danych **Master** na serwerze za pomocą identyfikatora logowania administratora serwera i Utwórz nowy identyfikator logowania do serwera.  Ponadto dobrym pomysłem jest utworzenie użytkownika w bazie danych Master dla Azure SQL Data Warehouse użytkowników. Utworzenie użytkownika w bazie danych Master umożliwia użytkownikowi logowanie się przy użyciu narzędzi, takich jak program SSMS, bez określania nazwy bazy.  Umożliwia także używanie Eksploratora obiektów do wyświetlania wszystkich baz danych w programie SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Następnie nawiąż połączenie z **bazą danych SQL Data Warehouse** za pomocą identyfikatora logowania administratora serwera i Utwórz użytkownika bazy danych na podstawie utworzonego identyfikatora logowania serwera.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Aby przyznać użytkownikowi uprawnienia do wykonywania dodatkowych operacji, takich jak tworzenie nazw logowania lub tworzenie nowych baz danych, przypisz użytkownika do `Loginmanager` i `dbmanager` ról w bazie danych Master. Aby uzyskać więcej informacji na temat dodatkowych ról i uwierzytelniania do SQL Database, zobacz temat [Zarządzanie bazami danych i logowaniami w Azure SQL Database][Managing databases and logins in Azure SQL Database].  Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z SQL Data Warehouse przy użyciu uwierzytelniania Azure Active Directory][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autoryzacja
Autoryzacja odnosi się do tego, co można zrobić w ramach bazy danych Azure SQL Data Warehouse. Uprawnienia do autoryzacji są określane przez członkostwo w rolach i uprawnienia. Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień. Aby zarządzać rolami, można użyć następujących procedur składowanych:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Konto administratora serwera, za pomocą którego nawiązujesz połączenie, jest członkiem roli db_owner, która ma uprawnienia do wykonywania wszystkich funkcji w bazie danych. Zapisz to konto, aby móc wdrażać uaktualnienia schematów i wykonywać inne operacje zarządzania. Używaj konta „ApplicationUser” z bardziej ograniczonymi uprawnienia do nawiązywania połączenia pomiędzy swoją aplikacją a bazą danych aplikacji, korzystając z minimalnych uprawnień wymaganych przez aplikację.

Istnieją sposoby dalszej granicy, co użytkownik może zrobić, korzystając z Azure SQL Data Warehouse:

* [Uprawnienia][Permissions] szczegółowe pozwalają kontrolować, które operacje można wykonywać na poszczególnych kolumnach, tabelach, widokach, schematach, procedurach i innych obiektach w bazie danych. Używaj szczegółowych uprawnień, aby mieć największą kontrolę i udzielić minimalnych uprawnień. 
* [Role bazy danych][Database roles] inne niż db_datareader i db_datawriter mogą służyć do tworzenia bardziej zaawansowanych kont użytkowników aplikacji lub mniej zaawansowanych kont zarządzania. Wbudowane role bazy danych zapewniają łatwy sposób przyznawania uprawnień, ale mogą powodować udzielanie większej liczby uprawnień niż jest to konieczne.
* [Procedury składowane][Stored procedures] umożliwiają ograniczenie czynności wykonywanych w bazie danych.

Poniższy przykład daje dostęp do odczytu do schematu zdefiniowanego przez użytkownika.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Zarządzanie bazami danych i serwerami logicznymi z Azure Portal lub przy użyciu interfejsu API Azure Resource Manager jest kontrolowane przez przypisania roli konta użytkownika portalu. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach w Azure Portal][Role-based access control in Azure portal].

## <a name="encryption"></a>Szyfrowanie
Azure SQL Data Warehouse Transparent Data Encryption (TDE) pomaga chronić przed zagrożeniami złośliwych działań przez szyfrowanie i odszyfrowywanie danych przy użyciu funkcji Rest.  Podczas szyfrowania bazy danych skojarzone kopie zapasowe i pliki dziennika transakcji są szyfrowane bez konieczności wprowadzania jakichkolwiek zmian w aplikacjach. TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego zwanego kluczem szyfrowania bazy danych. 

W SQL Database klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera SQL Database. Firma Microsoft automatycznie obraca te certyfikaty co najmniej co 90 dni. Algorytm szyfrowania używany przez SQL Data Warehouse to AES-256. Ogólny opis TDE można znaleźć w temacie [transparent Data Encryption][Transparent Data Encryption].

Bazę danych można zaszyfrować przy użyciu [Azure Portal][Encryption with Portal] lub [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje i przykłady dotyczące łączenia się z SQL Data Warehouse przy użyciu różnych protokołów, zobacz [Connect to SQL Data Warehouse][Connect to SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
