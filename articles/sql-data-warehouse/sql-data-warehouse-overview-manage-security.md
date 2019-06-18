---
title: Zabezpieczanie bazy danych w usłudze SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące zabezpieczania bazy danych w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 179925fc7411a1ccf3de02d7b6298cc66f93bc66
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61126944"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Zabezpieczanie bazy danych w usłudze SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Uwierzytelnianie](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encryption (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

W tym artykule przedstawiono podstawowe informacje dotyczące zabezpieczania bazy danych Azure SQL Data Warehouse. W szczególności, ta ułatwia artykułu, rozpoczęcie pracy z zasobami na potrzeby ograniczania dostępu, ochrony danych i monitorowania działań w bazie danych.

## <a name="connection-security"></a>Zabezpieczenia połączeń
Zabezpieczenia połączeń dotyczą sposobu ograniczania i zabezpieczania połączeń z bazą danych przy użyciu reguł zapory i szyfrowania połączeń.

Reguły zapory są używane zarówno przez serwer, jak i przez bazę danych do odrzucania prób połączenia z adresów IP, które nie zostały jawnie wymienione na liście dozwolonych hostów. Aby zezwolić na połączenia z aplikacji lub publiczny adres IP maszyny klienta, należy najpierw utworzyć regułę zapory na poziomie serwera przy użyciu witryny Azure portal, interfejsu API REST lub programu PowerShell. Najlepszym rozwiązaniem jest maksymalne ograniczenie zakresu adresów IP przepuszczanych przez zaporę serwera.  Dostęp do usługi Azure SQL Data Warehouse z komputera lokalnego, upewnij się, że Zapora w sieci i komputerze lokalnym zezwala na wychodzącą komunikację na porcie TCP 1433.  

Usługa SQL Data Warehouse przy użyciu reguł zapory na poziomie serwera. Nie obsługuje reguły zapory na poziomie bazy danych. Aby uzyskać więcej informacji, zobacz [zapory usługi Azure SQL Database][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

Połączenia z usługi SQL Data Warehouse są domyślnie szyfrowane.  Modyfikowanie ustawień połączenia, umożliwia wyłączenie szyfrowania są ignorowane.

## <a name="authentication"></a>Authentication
Uwierzytelnianie to sposób potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Usługa SQL Data Warehouse obsługuje obecnie uwierzytelniania programu SQL Server przy użyciu nazwy użytkownika i hasła, a w usłudze Azure Active Directory. 

Po utworzeniu serwera logicznego bazy danych należy określić nazwę logowania „server admin” przy użyciu nazwy użytkownika i hasła. Przy użyciu tych poświadczeń, można wybrać metodę uwierzytelniania dowolnej bazy danych na tym serwerze jako właściciel bazy danych, czyli "dbo" przy użyciu uwierzytelniania programu SQL Server.

Jednak najlepszym rozwiązaniem jest użytkowników w organizacji należy użyć innego konta do uwierzytelniania. W ten sposób można ograniczyć uprawnienia nadane aplikacji i zmniejszyć ryzyko złośliwych działań, w przypadku, gdy kod aplikacji jest narażony na atak iniekcji SQL. 

Aby utworzyć serwer uwierzytelniony użytkownik SQL, połączyć się z **wzorca** bazy danych na serwerze z Twojego identyfikatora logowania administratora serwera i utworzyć nowy identyfikator logowania serwera.  Ponadto jest dobry pomysł, aby utworzyć użytkownika w bazie danych master dla użytkowników usługi Azure SQL Data Warehouse. Tworzenie użytkownika w części głównej umożliwia użytkownikowi zalogowanie się przy użyciu narzędzi, takich jak program SSMS bez określenia nazwy bazy danych.  Umożliwia także wyświetlanie wszystkich baz danych na serwerze SQL server za pomocą Eksploratora obiektów.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Następnie połącz się z **bazy danych SQL Data Warehouse** z Twojego identyfikatora logowania administratora serwera i Utwórz użytkownika bazy danych oparty na identyfikatorze logowania serwera, został utworzony.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Aby nadać użytkownikowi uprawnienia do wykonywania dodatkowych operacji, takich jak tworzenie identyfikatorów logowania lub tworzenia nowych baz danych, przypisz użytkownikowi `Loginmanager` i `dbmanager` ról w bazie danych master. Aby uzyskać więcej informacji na temat tych dodatkowych ról i uwierzytelniania w usłudze SQL Database, zobacz [Zarządzanie bazami danych i nazwami logowania w usłudze Azure SQL Database][Managing databases and logins in Azure SQL Database].  Aby uzyskać więcej informacji, zobacz [nawiązywania połączenia z SQL danych magazynu przez przy użyciu usługi Azure Active Directory Authentication][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autoryzacja
Autoryzacja Określa, co można zrobić w bazie danych Azure SQL Data Warehouse. Autoryzacji uprawnienia są określane za pomocą członkostwa w roli i uprawnień. Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień. Aby zarządzać rolami, można użyć następujących procedur składowanych:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Konto administratora serwera, za pomocą którego nawiązujesz połączenie, jest członkiem roli db_owner, która ma uprawnienia do wykonywania wszystkich funkcji w bazie danych. Zapisz to konto, aby móc wdrażać uaktualnienia schematów i wykonywać inne operacje zarządzania. Używaj konta „ApplicationUser” z bardziej ograniczonymi uprawnienia do nawiązywania połączenia pomiędzy swoją aplikacją a bazą danych aplikacji, korzystając z minimalnych uprawnień wymaganych przez aplikację.

Istnieją sposoby dalszego ograniczenia, co użytkownik może zrobić za pomocą usługi Azure SQL Data Warehouse:

* Szczegółowe [uprawnienia] [ Permissions] pozwalają na kontrolki, jakie operacje można na poszczególnych kolumnach, tabele, widoki, schematów, procedury i innych obiektów w bazie danych. Użyj szczegółowych uprawnień ma największą kontrolę i przyznawanie minimalnych niezbędnych uprawnień. 
* [Ról bazy danych] [ Database roles] inne niż db_datareader i db_datawriter mogą służyć do tworzenia bardziej zaawansowanych kont użytkowników aplikacji lub mniej zaawansowanych kont zarządzania. Wbudowane ustalone role bazy danych zapewniają prosty sposób, aby udzielić uprawnień, ale może spowodować udzielanie więcej uprawnień niż jest to konieczne.
* [Procedury składowane] [ Stored procedures] może służyć do ograniczania akcje, które mogą być wykonywane w bazie danych.

Poniższy przykład daje dostęp do odczytu do schematu zdefiniowanych przez użytkownika.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Zarządzanie bazami danych i serwerami logicznymi z witryny Azure portal lub za pomocą interfejsu API usługi Azure Resource Manager jest kontrolowane przez przypisania ról konta użytkownika portalu. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach w witrynie Azure portal][Role-based access control in Azure portal].

## <a name="encryption"></a>Szyfrowanie
Usługa Azure SQL danych magazynu przezroczystego szyfrowania danych (TDE) ułatwia ochronę przed złośliwymi działaniami za pomocą szyfrowania i odszyfrowywania danych magazynowanych.  Podczas szyfrowania bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji są szyfrowane bez konieczności wprowadzania zmian do aplikacji. Funkcja TDE szyfruje magazyn całą bazę danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych. 

W bazie danych SQL klucz szyfrowania bazy danych jest chroniony za pomocą wbudowanego serwera certyfikatu. Certyfikat wbudowanego serwera jest unikatowy dla każdego serwera bazy danych SQL. Microsoft automatycznie przełącza tych certyfikatów, co 90 dni. Algorytm szyfrowania używany przez SQL Data Warehouse to AES-256. Aby uzyskać ogólny opis funkcji TDE, zobacz [funkcji Transparent Data Encryption][Transparent Data Encryption].

Umożliwia ona szyfrowanie przy użyciu bazy danych [witryny Azure portal] [ Encryption with Portal] lub [języka T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje i przykłady dotyczące nawiązywania połączenia usługi SQL Data Warehouse przy użyciu różnych protokołów, zobacz [nawiązywanie połączenia z SQL Data Warehouse][Connect to SQL Data Warehouse].

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
