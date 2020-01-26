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
ms.openlocfilehash: 8e9ab9dddad35708b58d32802452789adf84a19e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759469"
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

## <a name="connection-security"></a>Bezpieczeństwo połączenia
Zabezpieczenia połączeń dotyczą sposobu ograniczania i zabezpieczania połączeń z bazą danych przy użyciu reguł zapory i szyfrowania połączeń.

Reguły zapory są używane przez serwer i bazę danych w celu odrzucania prób połączenia z adresów IP, które nie zostały jawnie listy dozwolonych. Aby zezwolić na połączenia z publicznego adresu IP aplikacji lub komputera klienckiego, należy najpierw utworzyć regułę zapory na poziomie serwera przy użyciu Azure Portal, interfejsu API REST lub programu PowerShell. 

Najlepszym rozwiązaniem jest maksymalne ograniczenie zakresu adresów IP przepuszczanych przez zaporę serwera.  Aby uzyskać dostęp do Azure SQL Data Warehouse z komputera lokalnego, upewnij się, że Zapora w sieci i komputer lokalny zezwalają na komunikację wychodzącą na porcie TCP 1433.  

Usługa Azure Synapse używa reguł zapory adresów IP na poziomie serwera. Nie obsługuje reguł zapory adresów IP na poziomie bazy danych. Aby uzyskać więcej informacji, zobacz temat [Azure SQL Database regułami zapory](../sql-database/sql-database-firewall-configure.md)

Połączenia z SQL Data Warehouse są domyślnie szyfrowane.  Modyfikowanie ustawień połączenia w celu wyłączenia szyfrowania jest ignorowane.

## <a name="authentication"></a>Authentication
Uwierzytelnianie to sposób potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. SQL Data Warehouse obecnie obsługuje uwierzytelnianie SQL Server przy użyciu nazwy użytkownika i hasła oraz Azure Active Directory. 

Po utworzeniu serwera logicznego bazy danych należy określić nazwę logowania „server admin” przy użyciu nazwy użytkownika i hasła. Przy użyciu tych poświadczeń można uwierzytelniać się w dowolnej bazie danych na tym serwerze jako właściciel bazy danych lub "dbo" za pośrednictwem SQL Server uwierzytelniania.

Jednak najlepszym rozwiązaniem jest użycie przez użytkowników w organizacji innego konta do uwierzytelniania. W ten sposób można ograniczyć uprawnienia przyznane aplikacji i zmniejszyć ryzyko złośliwych działań w przypadku, gdy kod aplikacji jest narażony na ataki iniekcji SQL. 

Aby utworzyć SQL Server uwierzytelnionego użytkownika, nawiąż połączenie z bazą danych **Master** na serwerze za pomocą identyfikatora logowania administratora serwera i Utwórz nowy identyfikator logowania do serwera.  Dobrym pomysłem jest również utworzenie użytkownika w bazie danych Master Synapse Użytkownicy platformy Azure. Utworzenie użytkownika w bazie danych Master umożliwia użytkownikowi logowanie się przy użyciu narzędzi, takich jak program SSMS, bez określania nazwy bazy.  Umożliwia także używanie Eksploratora obiektów do wyświetlania wszystkich baz danych w programie SQL Server.

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

Aby przyznać użytkownikowi uprawnienia do wykonywania dodatkowych operacji, takich jak tworzenie nazw logowania lub tworzenie nowych baz danych, przypisz użytkownika do `Loginmanager` i `dbmanager` ról w bazie danych Master. 

Aby uzyskać więcej informacji na temat dodatkowych ról i uwierzytelniania do SQL Database, zobacz temat [Zarządzanie bazami danych i logowaniami w Azure SQL Database](../sql-database/sql-database-manage-logins.md).  Aby uzyskać więcej informacji na temat nawiązywania połączenia przy użyciu Azure Active Directory, zobacz [nawiązywanie połączenia przy użyciu uwierzytelniania Azure Active Directory](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autoryzacja
Autoryzacja odnosi się do tego, co można zrobić w bazie danych po uwierzytelnieniu i nawiązaniu połączenia. Uprawnienia do autoryzacji są określane przez członkostwo w rolach i uprawnienia. Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień. Aby zarządzać rolami, można użyć następujących procedur składowanych:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Konto administratora serwera, za pomocą którego nawiązujesz połączenie, jest członkiem roli db_owner, która ma uprawnienia do wykonywania wszystkich funkcji w bazie danych. Zapisz to konto, aby móc wdrażać uaktualnienia schematów i wykonywać inne operacje zarządzania. Używaj konta „ApplicationUser” z bardziej ograniczonymi uprawnienia do nawiązywania połączenia pomiędzy swoją aplikacją a bazą danych aplikacji, korzystając z minimalnych uprawnień wymaganych przez aplikację.

Istnieją sposoby dalszej ograniczania możliwości użytkownika w hurtowni danych:

* [Uprawnienia](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15) szczegółowe pozwalają kontrolować, które operacje można wykonywać na poszczególnych kolumnach, tabelach, widokach, schematach, procedurach i innych obiektach w bazie danych. Używaj szczegółowych uprawnień, aby mieć największą kontrolę i udzielić minimalnych uprawnień. 
* [Role bazy danych](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15) inne niż db_datareader i db_datawriter mogą służyć do tworzenia bardziej zaawansowanych kont użytkowników aplikacji lub mniej zaawansowanych kont zarządzania. Wbudowane role bazy danych zapewniają łatwy sposób przyznawania uprawnień, ale mogą powodować udzielanie większej liczby uprawnień niż jest to konieczne.
* [Procedury składowane](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15) umożliwiają ograniczenie czynności wykonywanych w bazie danych.

Poniższy przykład daje dostęp do odczytu do schematu zdefiniowanego przez użytkownika.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Zarządzanie bazami danych i serwerami logicznymi z Azure Portal lub przy użyciu interfejsu API Azure Resource Manager jest kontrolowane przez przypisania roli konta użytkownika portalu. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach w Azure Portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure).

## <a name="encryption"></a>Szyfrowanie
Transparent Data Encryption (TDE) pomaga chronić przed zagrożeniem złośliwymi działaniami przez szyfrowanie i odszyfrowywanie danych przy użyciu funkcji Rest. Podczas szyfrowania bazy danych skojarzone kopie zapasowe i pliki dziennika transakcji są szyfrowane bez konieczności wprowadzania jakichkolwiek zmian w aplikacjach. TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego zwanego kluczem szyfrowania bazy danych. 

W SQL Database klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera SQL Database. Firma Microsoft automatycznie obraca te certyfikaty co najmniej co 90 dni. Używany algorytm szyfrowania to AES-256. Ogólny opis TDE można znaleźć w temacie [transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15).

Bazę danych można zaszyfrować przy użyciu [Azure Portal](sql-data-warehouse-encryption-tde.md) lub [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje i przykłady dotyczące łączenia się z magazynem przy użyciu różnych protokołów, zobacz [Connect to SQL Data Warehouse](sql-data-warehouse-connect-overview.md).
