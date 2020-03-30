---
title: Usługa Azure Active Directory
description: Dowiedz się, jak używać usługi Azure Active Directory do uwierzytelniania za pomocą bazy danych SQL, wystąpienia zarządzanego i magazynu danych SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 02/20/2019
ms.openlocfilehash: ef20c5b8babdf378b88997ae2fd7b98350c31319
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124921"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Używanie uwierzytelniania usługi Azure Active Directory do uwierzytelniania za pomocą języka SQL

Uwierzytelnianie usługi Azure Active Directory to mechanizm łączenia się z usługą Azure [SQL Database](sql-database-technical-overview.md), [wystąpienie zarządzane](sql-database-managed-instance.md)i magazyn danych [SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD). 

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

W przypadku uwierzytelniania usługi Azure AD można centralnie zarządzać tożsamościami użytkowników bazy danych oraz innymi usługami firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie tożsamościami oznacza jedną lokalizację do zarządzania użytkownikami bazy danych i prostsze zarządzanie uprawnieniami. Oto przykładowe korzyści:

- Zapewnia alternatywę dla uwierzytelniania programu SQL Server.
- Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach bazy danych.
- Umożliwia obracanie hasła w jednym miejscu.
- Klienci mogą zarządzać uprawnieniami bazy danych przy użyciu zewnętrznych grup (Azure AD).
- Można wyeliminować przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez usługę Azure Active Directory.
- Uwierzytelnianie usługi Azure AD używa użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
- Usługa Azure AD obsługuje uwierzytelnianie oparte na tokenach dla aplikacji łączących się z bazą danych SQL.
- Uwierzytelnianie usługi Azure AD obsługuje usługę ADFS (federacja domen) lub natywne uwierzytelnianie użytkownika/hasła dla lokalnej usługi Azure Active Directory bez synchronizacji domeny.
- Usługa Azure AD obsługuje połączenia z programu SQL Server Management Studio, które używają uniwersalnego uwierzytelniania usługi Active Directory obejmującej usługę Multi-Factor Authentication (MFA).  Usługa MFA obejmuje silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji, takich jak połączenia telefoniczne, wiadomości SMS, karty inteligentne z numerem PIN lub powiadomienia przez aplikacje mobilne. Aby uzyskać więcej informacji, zobacz [Obsługa programu SSMS na potrzeby usługi Azure AD MFA przy użyciu usług SQL Database i SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).
- Usługa Azure AD obsługuje podobne połączenia z narzędziami SQL Server Data Tools (SSDT), które używają interaktywnego uwierzytelniania usługi Active Directory. Aby uzyskać więcej informacji, zobacz [Azure Active Directory support in SQL Server Data Tools (SSDT) (Obsługa usługi Azure Active Directory w narzędziach SQL Server Data Tools (SSDT))](/sql/ssdt/azure-active-directory).

> [!NOTE]  
> Łączenie się z programem SQL Server uruchomionym na maszynie Wirtualnej platformy Azure nie jest obsługiwane przy użyciu konta usługi Azure Active Directory. Zamiast tego użyj konta usługi Active Directory w domenie.  

Kroki konfiguracji obejmują następujące procedury konfigurowania i używania uwierzytelniania usługi Azure Active Directory.

1. Tworzenie i wypełnianie usługi Azure AD.
2. Opcjonalnie: skojarz lub zmień usługę active directory, która jest obecnie skojarzona z subskrypcją platformy Azure.
3. Utwórz administratora usługi Azure Active Directory dla serwera bazy danych SQL Azure, wystąpienia zarządzanego lub [magazynu danych SQL Azure.](https://azure.microsoft.com/services/sql-data-warehouse/)
4. Skonfiguruj komputery klienckie.
5. Tworzenie użytkowników zawartej bazy danych w bazie danych mapowane do tożsamości usługi Azure AD.
6. Połącz się z bazą danych przy użyciu tożsamości usługi Azure AD.

> [!NOTE]
> Aby dowiedzieć się, jak tworzyć i wypełniać usługę Azure AD, a następnie konfigurować usługę Azure AD za pomocą usługi Azure SQL Database, wystąpienie zarządzane i magazyn danych SQL, zobacz [Konfigurowanie usługi Azure AD za pomocą usługi Azure SQL Database](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Architektura zaufania

Poniższy diagram wysokiego poziomu podsumowuje architekturę rozwiązania przy użyciu uwierzytelniania usługi Azure AD za pomocą usługi Azure SQL Database. Te same pojęcia dotyczą usługi SQL Data Warehouse. Aby obsługiwać hasło użytkownika natywnego usługi Azure AD, uwzględniana jest tylko część chmury i usługa Azure AD/Azure SQL Database. Do obsługi uwierzytelniania federacyjnego (lub użytkownika/hasła dla poświadczeń systemu Windows) wymagana jest komunikacja z blokiem ADFS. Strzałki wskazują ścieżki komunikacyjne.

![schemat ujedlatek aad][1]

Poniższy diagram wskazuje federacji, zaufania i hostingu relacji, które umożliwiają klientowi połączyć się z bazą danych przez przesłanie tokenu. Token jest uwierzytelniony przez usługę Azure AD i jest zaufany przez bazę danych. Klient 1 może reprezentować usługę Azure Active Directory z użytkownikami natywnymi lub usługę Azure AD z użytkownikami federatywnymi. Klient 2 reprezentuje możliwe rozwiązanie, w tym importowanych użytkowników; w tym przykładzie pochodzących z federacyjnej usługi Azure Active Directory z usługą ADFS są synchronizowane z usługą Azure Active Directory. Ważne jest, aby zrozumieć, że dostęp do bazy danych przy użyciu uwierzytelniania usługi Azure AD wymaga, aby subskrypcja hostingu jest skojarzona z usługą Azure AD. Ta sama subskrypcja musi służyć do tworzenia programu SQL Server obsługującego usługę Azure SQL Database lub SQL Data Warehouse.

![relacja subskrypcji][2]

## <a name="administrator-structure"></a>Struktura administratora

Podczas korzystania z uwierzytelniania usługi Azure AD istnieją dwa konta administratora dla serwera bazy danych SQL i wystąpienia zarządzanego; pierwotnego administratora programu SQL Server i administratora usługi Azure AD. Te same pojęcia dotyczą usługi SQL Data Warehouse. Tylko administrator na podstawie konta usługi Azure AD można utworzyć pierwszy użytkownik bazy danych zawierającej usługę Azure AD w bazie danych użytkowników. Logowanie administratora usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Gdy administrator jest kontem grupy, może być używany przez dowolnego członka grupy, włączając wielu administratorów usługi Azure AD dla wystąpienia programu SQL Server. Korzystanie z konta grupy jako administratora zwiększa możliwości zarządzania, umożliwiając centralne dodawanie i usuwanie członków grupy w usłudze Azure AD bez zmiany użytkowników lub uprawnień w bazie danych SQL. Tylko jeden administrator usługi Azure AD (użytkownik lub grupa) można skonfigurować w dowolnym momencie.

![struktura administracyjna][3]

## <a name="permissions"></a>Uprawnienia

Aby utworzyć nowych użytkowników, `ALTER ANY USER` musisz mieć uprawnienia w bazie danych. Uprawnienie `ALTER ANY USER` można udzielić każdemu użytkownikowi bazy danych. `ALTER ANY USER` Uprawnienie jest również przechowywane przez konta administratora `CONTROL ON DATABASE` serwera `ALTER ON DATABASE` i użytkowników bazy danych z `db_owner` uprawnieniami lub uprawnieniami dla tej bazy danych oraz przez członków roli bazy danych.

Aby utworzyć użytkownika zawartej bazy danych w usłudze Azure SQL Database, wystąpieniu zarządzanym lub magazynie danych SQL, należy połączyć się z bazą danych lub wystąpieniem przy użyciu tożsamości usługi Azure AD. Aby utworzyć pierwszego użytkownika bazy danych, należy połączyć się z bazą danych przy użyciu administratora usługi Azure AD (który jest właścicielem bazy danych). Zostało to zademonstrowane w temacie [Konfigurowanie uwierzytelniania usługi Azure Active Directory i zarządzanie nim za pomocą bazy danych SQL lub magazynu danych SQL](sql-database-aad-authentication-configure.md). Uwierzytelnianie usługi Azure AD jest możliwe tylko wtedy, gdy administrator usługi Azure AD został utworzony dla usługi Azure SQL Database lub serwera usługi SQL Data Warehouse. Jeśli administrator usługi Azure Active Directory został usunięty z serwera, istniejący użytkownicy usługi Azure Active Directory utworzone wcześniej wewnątrz programu SQL Server nie mogą już łączyć się z bazą danych przy użyciu poświadczeń usługi Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkcje i ograniczenia usługi Azure AD

- Następujących członków usługi Azure AD można aprowizować na serwerze SQL platformy Azure lub usługi SQL Data Warehouse:

  - Natywnych członków: członek utworzony w usłudze Azure AD w domenie zarządzanej lub w domenie klienta. Aby uzyskać więcej informacji, zobacz [Dodawanie własnej nazwy domeny do usługi Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Członkowie domeny federacyjnej: członek utworzony w usłudze Azure AD z domeną federacyjnej. Aby uzyskać więcej informacji, zobacz [Microsoft Azure obsługuje teraz federację z usługą Active Directory systemu Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Zaimportowani członkowie z innych użytkowników usługi Azure AD, którzy są natywnymi lub sfederowanymi członkami domeny.
  - Grupy usługi Active Directory utworzone jako grupy zabezpieczeń.

- Użytkownicy usługi Azure AD, którzy `db_owner` są częścią grupy, która ma rolę serwera, nie mogą używać składni **[POŚWIADCZEŃ O ZAKRESIE BAZY DANYCH w](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** usłudze Azure SQL Database i Usługi Azure SQL Data Warehouse. Zostanie wyświetlony następujący błąd:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Udziel `db_owner` roli bezpośrednio poszczególnym użytkownikom usługi Azure AD, aby ograniczyć problem **z poświadczeniami o zakresie bazy danych.**

- Te funkcje systemowe zwracają wartości NULL, gdy są wykonywane w ramach jednostki usługi Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Wystąpienia zarządzane

- Podmioty serwera usługi Azure AD (loginy) i użytkownicy są obsługiwani jako funkcja w wersji zapoznawczej dla [wystąpień zarządzanych.](sql-database-managed-instance.md)
- Ustawianie podmiotów zabezpieczeń serwera usługi Azure AD (loginów) mapowanych do grupy usługi Azure AD jako właściciela bazy danych nie jest obsługiwane w [wystąpieniach zarządzanych](sql-database-managed-instance.md).
    - Rozszerzenie jest to, że gdy grupa jest `dbcreator` dodawany jako część roli serwera, użytkownicy z tej grupy można połączyć się z wystąpieniem zarządzanym i utworzyć nowe bazy danych, ale nie będzie mógł uzyskać dostępu do bazy danych. Dzieje się tak, ponieważ nowy właściciel bazy danych jest SA, a nie użytkownik usługi Azure AD. Ten problem nie objawia się, jeśli `dbcreator` pojedynczy użytkownik zostanie dodany do roli serwera.
- Zarządzanie agentem SQL i wykonywanie zadań jest obsługiwane dla podmiotów serwera usługi Azure AD (logowania).
- Operacje tworzenia kopii zapasowej oraz przywracania bazy danych mogą być wykonywane przy użyciu jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Inspekcja wszystkich instrukcji związanych z podmiotami serwera usługi Azure AD (logowania) i zdarzeniami uwierzytelniania jest obsługiwana.
- Obsługiwane jest dedykowane połączenie administratora dla podmiotów serwerowych (logowania) serwera usługi Azure AD, które są członkami roli serwera sysadmin.
    - Obsługiwane przez SQLCMD Utility i SQL Server Management Studio.
- Wyzwalacze logowania są obsługiwane dla zdarzeń logowania przychodzących z jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Service Broker i poczta bazy danych można skonfigurować przy użyciu jednostki serwera usługi Azure AD (login).


## <a name="connecting-using-azure-ad-identities"></a>Łączenie przy użyciu tożsamości usługi Azure AD

Uwierzytelnianie usługi Azure Active Directory obsługuje następujące metody łączenia się z bazą danych przy użyciu tożsamości usługi Azure AD:

- Hasło usługi Azure Active Directory
- Zintegrowana usługa Azure Active Directory
- Uniwersalna usługa Azure Active Directory z usługą MFA
- Korzystanie z uwierzytelniania tokenu aplikacji

Następujące metody uwierzytelniania są obsługiwane dla podmiotów serwerowych usługi Azure AD (logowania)**(publiczna wersja zapoznawcza):**

- Hasło usługi Azure Active Directory
- Zintegrowana usługa Azure Active Directory
- Uniwersalna usługa Azure Active Directory z usługą MFA


### <a name="additional-considerations"></a>Dodatkowe zagadnienia

- Aby zwiększyć łatwość zarządzania, zaleca się aprowizowanie dedykowanej grupy usługi Azure AD jako administratora.   
- Tylko jeden administrator usługi Azure AD (użytkownik lub grupa) można skonfigurować dla serwera bazy danych SQL Azure lub usługi Azure SQL Data Warehouse w dowolnym momencie.
  - Dodanie podmiotów serwera usługi Azure AD (loginy) dla wystąpień zarządzanych **(publiczna wersja zapoznawcza)** umożliwia utworzenie wielu podmiotów `sysadmin` serwera usługi Azure AD (logowania), które można dodać do roli.
- Tylko administrator usługi Azure AD dla programu SQL Server może początkowo połączyć się z serwerem bazy danych SQL Azure, wystąpieniem zarządzanym lub magazynem danych SQL Azure przy użyciu konta usługi Azure Active Directory. Administrator usługi Active Directory może konfigurować kolejnych użytkowników bazy danych usługi Azure AD.   
- Zalecamy ustawienie limitu czasu połączenia na 30 sekund.   
- Sql Server 2016 Management Studio i SQL Server Data Tools for Visual Studio 2015 (wersja 14.0.60311.1 kwietnia 2016 lub nowsze) obsługują uwierzytelnianie usługi Azure Active Directory. (Uwierzytelnianie usługi Azure AD jest obsługiwane przez **dostawcę danych programu .NET Framework dla programu SqlServer**; co najmniej w wersji .NET Framework 4.6). Dlatego najnowsze wersje tych narzędzi i aplikacji warstwy danych (DAC i . BACPAC) można użyć uwierzytelniania usługi Azure AD.   
- Począwszy od wersji 15.0.1, [narzędzie sqlcmd](/sql/tools/sqlcmd-utility) i [narzędzie bcp](/sql/tools/bcp-utility) obsługują interaktywne uwierzytelnianie usługi Active Directory za pomocą usługi MFA.
- Sql Server Data Tools for Visual Studio 2015 wymaga co najmniej wersji narzędzia danych z kwietnia 2016 r. (wersja 14.0.60311.1). Obecnie użytkownicy usługi Azure AD nie są wyświetlane w Eksploratorze obiektów SSDT. Aby obejść ten problem, wyświetl użytkowników w [pliku sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Sterownik JDBC firmy Microsoft 6.0 dla programu SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) obsługuje uwierzytelnianie usługi Azure AD. Zobacz [też: Ustawianie właściwości połączenia](https://msdn.microsoft.com/library/ms378988.aspx).   
- PolyBase nie może uwierzytelniać się przy użyciu uwierzytelniania usługi Azure AD.   
- Uwierzytelnianie usługi Azure AD jest obsługiwane dla bazy danych SQL przez obiekty **bloku importu bazy danych** i eksportu bazy danych **usługi** Azure portal. Importowanie i eksportowanie przy użyciu uwierzytelniania usługi Azure AD jest również obsługiwane z polecenia programu PowerShell.   
- Uwierzytelnianie usługi Azure AD jest obsługiwane dla bazy danych SQL, wystąpienia zarządzanego i magazynu danych SQL przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania usługi Azure Active Directory i zarządzanie nim za pomocą bazy danych SQL lub magazynu danych SQL](sql-database-aad-authentication-configure.md) i programu SQL Server — [az sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak tworzyć i wypełniać usługę Azure AD, a następnie konfigurować usługę Azure AD za pomocą usługi Azure SQL Database lub usługi Azure SQL Data Warehouse, zobacz [Konfigurowanie uwierzytelniania usługi Azure Active Directory i zarządzanie nim za pomocą bazy danych SQL, wystąpienia zarządzanego lub magazynu danych SQL](sql-database-aad-authentication-configure.md).
- Aby zapoznać się z samouczkiem using Azure AD server principals (logins) with Managed Instances, zobacz [Podmioty serwera usługi Azure AD (loginy) z wystąpieniami zarządzanymi](sql-database-managed-instance-aad-security-tutorial.md)
- Aby uzyskać omówienie loginów, użytkowników, ról bazy danych i uprawnień w bazie danych SQL, zobacz [Logowania, użytkownicy, role baz danych i uprawnienia](sql-database-manage-logins.md).
- Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń bazy danych, zobacz [Principals](https://msdn.microsoft.com/library/ms181127.aspx) (Podmioty zabezpieczeń).
- Aby uzyskać więcej informacji na temat ról bazy danych, zobacz [Database roles](https://msdn.microsoft.com/library/ms189121.aspx) (Role bazy danych).
- Aby uzyskać składnię tworzenia podmiotów serwerowych (logowania) serwera usługi Azure AD dla wystąpień zarządzanych, zobacz [TWORZENIE LOGOWANIA](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- Aby uzyskać więcej informacji na temat reguł zapory w usłudze SQL Database, zobacz [Omówienie reguł zapory usługi SQL Database](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
