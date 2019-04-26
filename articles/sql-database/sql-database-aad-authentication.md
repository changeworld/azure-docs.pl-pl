---
title: Azure Active Directory uwierzytelnianie — Azure SQL | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobie używania usługi Azure Active Directory do uwierzytelniania przy użyciu bazy danych SQL Database, wystąpienia zarządzanego i SQL Data Warehouse
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/20/2019
ms.openlocfilehash: 1318cd3d1c0c51889cc70b6836d06d6d6ee70c24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387402"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Użyj uwierzytelniania usługi Azure Active Directory do uwierzytelniania przy użyciu języka SQL

Uwierzytelnianie usługi Azure Active Directory jest mechanizmem nawiązywania połączenia z usługą Azure [bazy danych SQL](sql-database-technical-overview.md), [wystąpienia zarządzanego](sql-database-managed-instance.md), i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości na platformie Azure Usługi Active Directory (Azure AD). 

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

Przy użyciu uwierzytelniania usługi Azure AD można centralnie zarządzać tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie identyfikator udostępnia jedno miejsce do zarządzania użytkownikami bazy danych i upraszcza zarządzanie uprawnieniami. Następujące korzyści:

- Jego stanowi alternatywę dla uwierzytelniania programu SQL Server.
- Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników między serwerami bazy danych.
- Umożliwia rotacja hasła w jednym miejscu.
- Klienci mogą zarządzać uprawnienia bazy danych przy użyciu grup zewnętrznych (Azure AD).
- Je wyeliminować zapisywania haseł, włączając zintegrowane uwierzytelnianie Windows i inne formy uwierzytelniania obsługiwane przez usługę Azure Active Directory.
- Uwierzytelnianie usługi Azure AD do uwierzytelniania tożsamości na poziomie bazy danych korzysta z użytkowników zawartej bazy danych.
- Usługa Azure AD obsługuje tokenów uwierzytelniania dla aplikacji, do łączenia z bazą danych SQL.
- Uwierzytelnianie usługi Azure AD obsługuje lokalnej usługi Azure Active Directory bez synchronizacji domeny usług AD FS (domeny federacyjnej) lub macierzystym użytkownika i hasło uwierzytelniania.
- Usługa Azure AD obsługuje połączenia z SQL Server Management Studio, które używają uniwersalne uwierzytelnianie usługi Active Directory, w tym usługi Multi-Factor Authentication (MFA).  Uwierzytelnianie wieloskładnikowe obejmuje silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji — połączenie telefoniczne, wiadomość SMS, kart inteligentnych przy użyciu numeru pin lub powiadomienie aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz [SSMS Obsługa usługi Azure AD MFA przy użyciu bazy danych SQL Database i SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).
- Usługa Azure AD obsługuje podobne połączenia z SQL Server Data Tools (SSDT), które używają interaktywne uwierzytelnianie usługi Active Directory. Aby uzyskać więcej informacji, zobacz [pomocy technicznej usługi Azure Active Directory w SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory).

> [!NOTE]  
> Nawiązywanie połączenia z SQL Server uruchomiony na Maszynie wirtualnej platformy Azure nie jest obsługiwana przy użyciu konta usługi Azure Active Directory. Zamiast tego użyj domeny konta usługi Active Directory.  

Kroki konfiguracji obejmują poniższe procedury, aby skonfigurować i korzystać z uwierzytelniania usługi Azure Active Directory.

1. Utwórz i wypełnij usługi Azure AD.
2. Opcjonalnie: Skojarz lub zmienić usługi active directory, która jest aktualnie skojarzona z subskrypcją platformy Azure.
3. Utwórz administratora usługi Azure Active Directory dla serwera usługi Azure SQL Database, wystąpienia zarządzanego lub [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Konfigurowanie komputerów klienckich.
5. Utwórz użytkowników zawartej bazy danych w bazie danych mapowany do tożsamości usługi Azure AD.
6. Łączenie z bazą danych przy użyciu tożsamości usługi Azure AD.

> [!NOTE]
> Aby dowiedzieć się, jak utworzyć i wypełnić usługi Azure AD, a następnie skonfiguruj usługę Azure AD za pomocą usługi Azure SQL Database, wystąpienia zarządzanego i SQL Data Warehouse, zobacz [Konfigurowanie usługi Azure AD z usługą Azure SQL Database](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Architektura zaufania

Poniższy diagram ogólny znajduje się podsumowanie architekturę rozwiązania przy użyciu uwierzytelniania usługi Azure AD z usługą Azure SQL Database. Te same pojęcia dotyczą SQL Data Warehouse. Aby zapewnić obsługę hasła macierzystym użytkownika usługi Azure AD, uznaje się tylko część w chmurze i usługi Azure AD/usługi Azure SQL Database. Aby obsługiwać uwierzytelnianie Sfederowane (lub użytkownika i hasło dla poświadczeń Windows) jest wymagana komunikacja z blokiem usług AD FS. Strzałki wskazują ścieżek komunikacji.

![diagram uwierzytelniania usługi AAD][1]

Poniższy diagram wskazuje federacyjnego zaufania i relacji hostingu, które pozwalają na łączenie z bazą danych, przesyłając żądanie tokenu klientów. Token jest uwierzytelniany przez usługę Azure AD i jest zaufany przez bazę danych. Odbiorca 1 może reprezentować usługi Azure Active Directory za pomocą natywnego użytkowników lub usługi Azure AD za pomocą użytkowników federacyjnych. Odbiorcy 2 reprezentuje możliwe rozwiązania, w tym importowanych użytkowników. w tym przykładzie pochodzące z federacyjnych usługi Azure Active Directory za pomocą usług AD FS są synchronizowane z usługą Azure Active Directory. Jest ważne dowiedzieć się, że dostęp do bazy danych przy użyciu uwierzytelniania usługi Azure AD wymaga, że hostingu subskrypcja jest skojarzona z usługą Azure AD. Tej samej subskrypcji może służyć do tworzenia programu SQL Server do hostowania bazy danych Azure SQL Database lub SQL Data Warehouse.

![Relacja subskrypcji][2]

## <a name="administrator-structure"></a>Struktura administratora

Korzystając z uwierzytelniania usługi Azure AD, istnieją dwa konta administratora dla serwera bazy danych SQL i wystąpienie zarządzane usługi; oryginalny administrator programu SQL Server i administrator usługi Azure AD. Te same pojęcia dotyczą SQL Data Warehouse. Tylko przez administratora, które są oparte na konto usługi Azure AD można utworzyć pierwszego użytkownika bazy danych usługi Azure AD, znajdujących się w bazie danych użytkownika. Identyfikator logowania administratora usługi Azure AD może być użytkownikiem usługi Azure AD lub grupy usługi Azure AD. Jeśli administrator jest konto grupy, może służyć przez dowolnego członka grupy, umożliwiając wielu administratorów usługi Azure AD dla wystąpienia programu SQL Server. Jako administrator przy użyciu konta grupy rozszerza możliwości zarządzania, umożliwiając centralnie Dodawanie i usuwanie członków grupy w usłudze Azure AD bez wprowadzania zmian do użytkowników lub uprawnienia w bazie danych SQL. W dowolnym momencie można skonfigurować tylko jeden administrator usługi Azure AD (użytkownika lub grupy).

![Struktura administratora][3]

## <a name="permissions"></a>Uprawnienia

Aby utworzyć nowych użytkowników, konieczne jest posiadanie `ALTER ANY USER` uprawnień w bazie danych. `ALTER ANY USER` Uprawnienia mogą otrzymać każdemu użytkownikowi bazy danych. `ALTER ANY USER` Uprawnienie jest również w posiadaniu kont administratora serwera i bazy danych użytkowników z `CONTROL ON DATABASE` lub `ALTER ON DATABASE` uprawnienia dla tej bazy danych i członkowie `db_owner` roli bazy danych.

Aby utworzyć użytkownika zawartej bazy danych w usłudze Azure SQL Database, wystąpienia zarządzanego lub SQL Data Warehouse, należy połączyć do bazy danych lub wystąpienia przy użyciu tożsamości usługi Azure AD. Aby utworzyć pierwszy użytkownika zawartej bazy danych, należy połączyć do bazy danych przy użyciu administrator usługi Azure AD (który jest właścicielem bazy danych). Jest to zaprezentowane w [Konfigurowanie i zarządzanie nimi w usłudze Azure Active Directory uwierzytelnianie przy użyciu bazy danych SQL Database lub SQL Data Warehouse](sql-database-aad-authentication-configure.md). Dowolne uwierzytelnianie usługi Azure AD jest możliwe tylko wtedy, jeśli administrator usługi Azure AD została utworzona dla serwera usługi Azure SQL Database lub SQL Data Warehouse. Jeśli administrator usługi Azure Active Directory został usunięty z serwera, istniejących użytkowników usługi Azure Active Directory, utworzony wcześniej w programie SQL Server można już połączyć z bazą danych przy użyciu swoich poświadczeń usługi Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkcje usługi Azure AD i ograniczenia

- Następujące elementy członkowskie programu Azure AD mogą być udostępniane w usłudze Azure SQL server lub SQL Data Warehouse:

  - Natywne elementy członkowskie: Element członkowski utworzone w usłudze Azure AD w domenie zarządzanej lub w domenie klienta. Aby uzyskać więcej informacji, zobacz [Dodaj własną nazwę domeny do usługi Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Elementy członkowskie domeny federacyjnej: Element członkowski utworzone w usłudze Azure AD z domeny federacyjnej. Aby uzyskać więcej informacji, zobacz [Microsoft Azure obsługuje teraz federacji z usługą Active Directory systemu Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Zaimportowane członków z innych usługi Azure AD, którzy są członkami domeny natywny lub federacyjnego.
  - Grupy usługi Active Directory tworzone jako grupy zabezpieczeń.

- Użytkownicy usługi Azure AD, które są częścią grupy, która ma `db_owner` roli serwera nie można użyć **[CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** składni względem usługi Azure SQL Database i Azure SQL Data Warehouse. Zostanie wyświetlony następujący błąd:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Udziel `db_owner` roli bezpośrednio do poszczególnych usługi Azure AD użytkownika, aby uniknąć **CREATE DATABASE SCOPED CREDENTIAL** problem.

- Te funkcje systemu zwracają wartości NULL, gdy wykonywane w ramach jednostki usługi Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="manage-instances"></a>Zarządzanie wystąpieniami

- Jednostki serwera w usłudze Azure AD (logowania) i użytkowników, które są obsługiwane jako funkcja w wersji zapoznawczej dla [wystąpienia zarządzane przez usługę](sql-database-managed-instance.md).
- Ustawienie podmiotów zabezpieczeń serwera usługi Azure AD (logowania) mapowane na grupę usługi Azure AD, jako właściciel bazy danych nie jest obsługiwana w [wystąpienia zarządzane przez usługę](sql-database-managed-instance.md).
    - Rozszerzenie tego jest fakt, że grupa jest dodawana jako część `dbcreator` roli serwera, użytkownicy z tej grupy mogą nawiązać połączenie z wystąpieniem zarządzanym i tworzyć nowe bazy danych, ale nie będzie dostępu do bazy danych. Jest to, ponieważ nowy właściciel bazy danych jest SA, a nie użytkownika usługi Azure AD. Ten problem nie manifestu, jeśli indywidualnego użytkownika jest dodawany do `dbcreator` roli serwera.
- Wykonywanie zadań i zarządzania agenta SQL jest obsługiwana dla podmiotów zabezpieczeń serwera usługi Azure AD (logowania).
- Operacje tworzenia kopii zapasowej oraz przywracania bazy danych mogą być wykonywane przy użyciu jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Funkcja inspekcji wszystkich instrukcji odnoszących się do podmiotów zabezpieczeń serwera usługi Azure AD (logowania) i zdarzenia uwierzytelniania jest obsługiwana.
- Dedykowane połączenie administratora dla jednostek serwera usługi Azure AD (logowania), które są członkami roli serwera sysadmin jest obsługiwane.
    - Obsługiwane za pomocą narzędzia SQLCMD i SQL Server Management Studio.
- Wyzwalacze logowania są obsługiwane dla zdarzeń logowania przychodzących z jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Wiadomości e-mail programu Service Broker i bazą danych może być instalację za pomocą usługi Azure AD podmiot zabezpieczeń serwera (identyfikator logowania).


## <a name="connecting-using-azure-ad-identities"></a>Łączenie za pomocą tożsamości usługi Azure AD

Uwierzytelnianie usługi Azure Active Directory obsługuje następujące metody nawiązywania połączenia z bazą danych przy użyciu tożsamości usługi Azure AD:

- Przy użyciu zintegrowanego uwierzytelniania Windows
- Za pomocą nazwy głównej usługi Azure AD i hasła
- Przy użyciu tokenu uwierzytelniania aplikacji

Następujące metody uwierzytelniania są obsługiwane w przypadku podmiotów zabezpieczeń serwera usługi Azure AD (logowania) (**publicznej wersji zapoznawczej**):

- Azure Active Directory Password
- Usługa Azure Active Directory — zintegrowana
- Usługa Azure Active Directory Universal za pomocą usługi MFA
- Usługa Azure Active Directory interaktywne


### <a name="additional-considerations"></a>Dodatkowe zagadnienia

- Aby zwiększyć możliwości zarządzania, zaleca się aprowizowanie dedykowane usłudze Azure AD grupy jako administrator.   
- Tylko jeden administrator usługi Azure AD (użytkownika lub grupy) można skonfigurować dla serwera Azure SQL Database lub Azure SQL Data Warehouse w dowolnym momencie.
  - Dodanie jednostek serwera usługi Azure AD (logowania) dla wystąpienia zarządzanego (**publicznej wersji zapoznawczej**) umożliwia możliwość tworzenia wielu usługi Azure AD serwera podmiotów zabezpieczeń (logowania), które mogą być dodawane do `sysadmin` roli.
- Tylko administrator usługi Azure AD dla programu SQL Server początkowo można połączyć się serwerem Azure SQL Database, wystąpienia zarządzanego lub Azure SQL Data Warehouse przy użyciu konta usługi Azure Active Directory. Administrator usługi Active Directory można skonfigurować kolejne usługi Azure AD bazy danych użytkowników.   
- Firma Microsoft zaleca ustawienie limitu czasu połączenia do 30 sekund.   
- SQL Server 2016 Management Studio i SQL Server Data Tools dla programu Visual Studio 2015 (wersja 14.0.60311.1April 2016 lub nowszej) obsługuje uwierzytelnianie usługi Azure Active Directory. (Uwierzytelnianie usługi azure AD jest obsługiwany przez **.NET Framework Data Provider Pro SqlServer**; co najmniej wersji .NET Framework 4.6). W związku z tym najnowszych wersji tych narzędzi i aplikacji warstwy danych (DAC i. BACPAC) można użyć uwierzytelniania usługi Azure AD.   
- Począwszy od wersji 15.0.1, [narzędzia sqlcmd](/sql/tools/sqlcmd-utility) i [narzędzie bcp](/sql/tools/bcp-utility) obsługuje przeprowadzić uwierzytelnianie interakcyjne Active Directory za pomocą usługi MFA.
- SQL Server Data Tools dla programu Visual Studio 2015 wymaga co najmniej wersji kwietnia 2016 r. Data Tools (wersja 14.0.60311.1). Obecnie użytkownicy usługi Azure AD nie są wyświetlane w Eksploratorze obiektów programu SSDT. Jako obejście, Wyświetl użytkowników w [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [6.0 w sterownik JDBC firmy Microsoft dla programu SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) usługi Azure AD obsługuje uwierzytelnianie. Zobacz też [ustawienie właściwości połączenia](https://msdn.microsoft.com/library/ms378988.aspx).   
- Program PolyBase nie można uwierzytelnić przy użyciu uwierzytelniania usługi Azure AD.   
- Uwierzytelnianie usługi Azure AD jest obsługiwana dla usługi SQL Database w witrynie Azure portal **Importuj bazę danych** i **eksportowania bazy danych** bloków. Importowanie i eksportowanie przy użyciu uwierzytelniania usługi Azure AD jest również obsługiwany z polecenia programu PowerShell.   
- Uwierzytelnianie usługi Azure AD jest obsługiwane dla bazy danych SQL Database, wystąpienia zarządzanego i SQL Data Warehouse przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie i zarządzanie nimi w usłudze Azure Active Directory uwierzytelnianie przy użyciu bazy danych SQL Database lub SQL Data Warehouse](sql-database-aad-authentication-configure.md) i [SQL Server — az sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak utworzyć i wypełnić usługi Azure AD, a następnie skonfiguruj usługę Azure AD za pomocą usługi Azure SQL Database lub Azure SQL Data Warehouse, zobacz [Konfigurowanie i zarządzanie uwierzytelnianiem usługi Azure Active Directory za pomocą bazy danych SQL Database, wystąpienia zarządzanego lub SQL Data Warehouse ](sql-database-aad-authentication-configure.md).
- Samouczek korzystania z jednostek serwera usługi Azure AD (logowania) w połączeniu z wystąpienia zarządzane przez usługę, zobacz [podmiotów serwera usługi Azure AD (logowania) za pomocą wystąpienia zarządzanego](sql-database-managed-instance-aad-security-tutorial.md)
- Dostęp i kontrola w usłudze SQL Database zostały omówione w temacie [Kontrola dostępu w usłudze SQL Database](sql-database-control-access.md).
- Dane logowania, użytkownicy i role bazy danych w usłudze SQL Database zostały omówione w temacie [Logins, users, and database roles](sql-database-manage-logins.md) (Dane logowania, użytkownicy i role bazy danych).
- Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń bazy danych, zobacz [Principals](https://msdn.microsoft.com/library/ms181127.aspx) (Podmioty zabezpieczeń).
- Aby uzyskać więcej informacji na temat ról bazy danych, zobacz [Database roles](https://msdn.microsoft.com/library/ms189121.aspx) (Role bazy danych).
- Dla składni na temat tworzenia podmiotów zabezpieczeń serwera (logowania) dla wystąpienia zarządzanego usługi Azure AD, zobacz [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
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
