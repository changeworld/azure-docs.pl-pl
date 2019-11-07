---
title: Udzielanie dostępu do Azure SQL Database i SQL Data Warehouse
description: Dowiedz się więcej na temat udzielania dostępu do Microsoft Azure SQL Database i SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 05/08/2019
ms.openlocfilehash: c115cd7e4d531bfdc7ddbacd4f6eff2a892ea3c3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690755"
---
# <a name="azure-sql-database-and-sql-data-warehouse-access-control"></a>Azure SQL Database i SQL Data Warehouse kontroli dostępu

Aby zapewnić bezpieczeństwo, usługa Azure [SQL Database](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) kontrolować dostęp przy użyciu reguł zapory ograniczających łączność przy użyciu adresu IP, mechanizmy uwierzytelniania wymagają od użytkowników potwierdzenia tożsamości i mechanizmów autoryzacji Ograniczanie użytkowników do określonych działań i danych. 

> [!IMPORTANT]
> Aby zobaczyć przegląd funkcji zabezpieczeń usługi SQL Database, zobacz [omówienie zabezpieczeń usługi SQL](sql-database-security-overview.md). Aby zapoznać się z samouczkiem, zobacz temat [zabezpieczanie Azure SQL Database](sql-database-security-tutorial.md). Omówienie funkcji zabezpieczeń SQL Data Warehouse można znaleźć w temacie [SQL Data Warehouse Security Overview](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)

## <a name="firewall-and-firewall-rules"></a>Zapora i reguły zapory

Usługa Microsoft Azure SQL Database udostępnia usługę relacyjnej bazy danych dla platformy Azure i innych aplikacji internetowych. Aby chronić dane, zapora uniemożliwia wszelki dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, zobacz [Omówienie reguł zapory usługi Azure SQL Database](sql-database-firewall-configure.md)

Usługa Azure SQL Database jest dostępna tylko za pośrednictwem portu TCP 1433. Aby uzyskać dostęp do usługi SQL Database z komputera, upewnij się, że zapora komputera klienta umożliwia wychodzący ruch TCP na porcie TCP 1433. Należy zablokować połączenia przychodzące na porcie TCP 1433, jeśli nie są one wymagane przez inne aplikacje. 

W ramach procesu łączenia wszystkie połączenia przychodzące z maszyny wirtualnej platformy Azure są przekierowywane na inny adres IP i port, unikatowy dla każdej roli procesu roboczego. Numer portu należy do zakresu od 11000 do 11999. Aby uzyskać więcej informacji na temat portów TCP, zobacz [porty powyżej 1433 dla ADO.NET 4,5 i SQL Database2](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Authentication

Usługa SQL Database obsługuje dwa typy uwierzytelniania:

- **Uwierzytelnianie SQL**:

  Ta metoda uwierzytelniania używa nazwy użytkownika i hasła. Podczas tworzenia serwera SQL Database dla bazy danych należy określić nazwę logowania "administrator serwera" przy użyciu nazwy użytkownika i hasła. Przy użyciu tych poświadczeń można wybrać metodę uwierzytelniania w każdej innej bazie danych na tym serwerze jako jej właściciel, czyli „dbo”. 
- **Uwierzytelnianie Azure Active Directory**:

  Ta metoda uwierzytelniania używa tożsamości zarządzanych przez Azure Active Directory i jest obsługiwana w przypadku domen zarządzanych i zintegrowanych. Aby skorzystać z uwierzytelniania przy użyciu usługi Azure Active Directory, należy utworzyć innego administratora serwera o nazwie „Azure AD admin”, co umożliwi administrowanie użytkownikami i grupami usługi Azure AD. Ten administrator może również wykonywać wszystkie operacje, które może wykonać zwykły administrator serwera. Zobacz artykuł [Connecting to SQL Database By Using Azure Active Directory Authentication](sql-database-aad-authentication.md) (Łączenie z usługą SQL Database przy użyciu uwierzytelniania usługi Azure Active Directory), aby poznać krok po kroku sposób tworzenia administratora usługi Azure AD w celu włączenia uwierzytelniania usługi Azure Active Directory.

Aparat bazy danych zamyka połączenia, które pozostawały bezczynne dłużej niż przez 30 minut. Połączenie musi wykonać ponowne logowanie, zanim będzie można z niego skorzystać. Stale aktywne połączenia z usługą SQL Database wymagają ponownej autoryzacji (wykonywanej przez aparat bazy danych) co najmniej raz na 10 godzin. Aparat bazy danych próbuje ponownej autoryzacji przy użyciu podanego pierwotnie hasła, dzięki czemu nie jest wymagane wprowadzanie danych przez użytkownika. Ze względu na wydajność w przypadku resetowania hasła w SQL Database połączenie nie zostanie ponownie uwierzytelnione, nawet jeśli połączenie zostanie zresetowane z powodu puli połączeń. Jest to zachowania inne niż w przypadku lokalnej usługi SQL Server. Jeśli po początkowej autoryzacji połączenia zmieniono hasło, należy zakończyć to połączenie i utworzyć nowe przy użyciu nowego hasła. Użytkownik z uprawnieniem `KILL DATABASE CONNECTION` może w sposób jawny zakończyć połączenie z usługą SQL Database za pomocą polecenia [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql).

Konta użytkowników można utworzyć w bazie danych master i przyznać im uprawnienia we wszystkich bazach danych na serwerze. Istnieje możliwość ich utworzenia w samej bazie danych (są to tzw. użytkownicy zawarci). Aby uzyskać informacje dotyczące tworzenia danych logowania i zarządzania nimi, zobacz [Zarządzanie danymi logowania](sql-database-manage-logins.md). Użyj zawartych baz danych, aby zwiększyć przenośność i skalowalność. Aby uzyskać więcej informacji na temat zawartych użytkowników, zobacz [Użytkownicy zawartej bazy danych — tworzenie przenośnej bazy danych](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) i [Zawarte bazy danych](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases).

Zalecanym najlepszym rozwiązaniem jest używanie przez daną aplikację dedykowanego konta do uwierzytelniania — w ten sposób można ograniczyć uprawnienia nadane aplikacji i zmniejszyć ryzyko złośliwych działań w przypadku, gdy kod aplikacji jest narażony na atak polegający na wstrzyknięciu kodu SQL. Zalecaną metodą jest utworzenie [użytkownika zawartej bazy danych](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), co umożliwia aplikacji bezpośrednie uwierzytelnianie w bazie danych. 

## <a name="authorization"></a>Autoryzacja

Autoryzacja określa, co użytkownik może zrobić w usłudze Azure SQL Database, i jest kontrolowana za pomocą [członkostw roli](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) bazy danych konta użytkownika i [uprawnień na poziomie obiektu](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień. Konto administratora serwera, za pomocą którego nawiązujesz połączenie, jest członkiem roli db_owner, która ma uprawnienia do wykonywania wszystkich funkcji w bazie danych. Zapisz to konto, aby móc wdrażać uaktualnienia schematów i wykonywać inne operacje zarządzania. Używaj konta „ApplicationUser” z bardziej ograniczonymi uprawnienia do nawiązywania połączenia pomiędzy swoją aplikacją a bazą danych aplikacji, korzystając z minimalnych uprawnień wymaganych przez aplikację. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie danymi logowania](sql-database-manage-logins.md).

Dostęp do bazy danych `master` jest zazwyczaj potrzebny tylko administratorom. Rutynowy dostęp do każdej bazy danych użytkownika powinien się odbywać za pośrednictwem użytkowników utworzonych w każdej zawartej bazie danych innych niż administrator. W przypadku korzystania z użytkowników zawartej bazy danych nie trzeba tworzyć nazw logowania w bazie danych `master`. Aby uzyskać więcej informacji, zobacz artykuł [Contained Database Users - Making Your Database Portable](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) (Użytkownicy zawartej bazy danych — tworzenie przenośnej bazy danych).

Należy zapoznać się z następującymi funkcjami, których można użyć do ograniczania lub podnoszenia uprawnień:

- [Personifikacji](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) i [podpisywania modułów](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) można używać do bezpiecznego tymczasowego podnoszenia poziomu uprawnień.
- [Zabezpieczeń na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) można używać do ograniczania zbioru wierszy, do których użytkownik może uzyskać dostęp.
- [Maskowania danych](sql-database-dynamic-data-masking-get-started.md) można używać do ograniczania ujawniania danych wrażliwych.
- [Procedury składowane](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) umożliwiają ograniczenie czynności wykonywanych w bazie danych.

## <a name="next-steps"></a>Następne kroki

- Aby zobaczyć przegląd funkcji zabezpieczeń usługi SQL Database, zobacz [omówienie zabezpieczeń usługi SQL](sql-database-security-overview.md).
- Aby dowiedzieć się więcej o regułach zapory, zobacz [reguły zapory](sql-database-firewall-configure.md).
- Aby dowiedzieć się więcej o użytkownikach i danych logowania, zobacz [Zarządzanie danymi logowania](sql-database-manage-logins.md). 
- Aby zapoznać się z omówieniem aktywnego monitorowania, zobacz [Inspekcja bazy danych](sql-database-auditing.md) i [wykrywanie zagrożeń SQL Database](sql-database-threat-detection.md).
- Aby zapoznać się z samouczkiem, zobacz temat [zabezpieczanie Azure SQL Database](sql-database-security-tutorial.md).
