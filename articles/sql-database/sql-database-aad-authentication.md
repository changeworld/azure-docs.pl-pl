---
title: Uwierzytelnianie Azure Active Directory — Azure SQL
description: Dowiedz się, jak używać Azure Active Directory do uwierzytelniania za pomocą SQL Database, wystąpienia zarządzanego i SQL Data Warehouse
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
ms.openlocfilehash: 4516f75d80345312a6ca3b6dac3e5156d7e239e8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691370"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Używanie uwierzytelniania Azure Active Directory na potrzeby uwierzytelniania przy użyciu programu SQL Server

Uwierzytelnianie Azure Active Directory to mechanizm łączenia się z usługą Azure [SQL Database](sql-database-technical-overview.md), [wystąpieniem zarządzanym](sql-database-managed-instance.md)i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD). 

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

W przypadku uwierzytelniania usługi Azure AD można centralnie zarządzać tożsamościami użytkowników bazy danych oraz innymi usługami firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie IDENTYFIKATORami zapewnia pojedyncze miejsce do zarządzania użytkownikami bazy danych i upraszcza zarządzanie uprawnieniami. Oto przykładowe korzyści:

- Stanowi alternatywę dla SQL Server uwierzytelniania.
- Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach baz danych.
- Umożliwia obrót hasła w jednym miejscu.
- Klienci mogą zarządzać uprawnieniami bazy danych przy użyciu zewnętrznych grup (Azure AD).
- Może on wyeliminować przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez Azure Active Directory.
- Uwierzytelnianie usługi Azure AD korzysta z użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
- Usługa Azure AD obsługuje uwierzytelnianie oparte na tokenach dla aplikacji łączących się z SQL Database.
- Uwierzytelnianie w usłudze Azure AD obsługuje uwierzytelnianie w usługach ADFS (Domain Federation) lub natywne uwierzytelnienie użytkownika/hasła dla lokalnego Azure Active Directory bez synchronizacji z domeną.
- Usługa Azure AD obsługuje połączenia z programu SQL Server Management Studio, które używają uniwersalnego uwierzytelniania usługi Active Directory obejmującej usługę Multi-Factor Authentication (MFA).  Usługa MFA obejmuje silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji, takich jak połączenia telefoniczne, wiadomości SMS, karty inteligentne z numerem PIN lub powiadomienia przez aplikacje mobilne. Aby uzyskać więcej informacji, zobacz [Obsługa programu SSMS na potrzeby usługi Azure AD MFA przy użyciu usług SQL Database i SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).
- Usługa Azure AD obsługuje podobne połączenia z narzędziami SQL Server Data Tools (SSDT), które używają interaktywnego uwierzytelniania usługi Active Directory. Aby uzyskać więcej informacji, zobacz [Azure Active Directory support in SQL Server Data Tools (SSDT) (Obsługa usługi Azure Active Directory w narzędziach SQL Server Data Tools (SSDT))](/sql/ssdt/azure-active-directory).

> [!NOTE]  
> Nawiązywanie połączenia z usługą SQL Server działającej na maszynie wirtualnej platformy Azure nie jest obsługiwane przy użyciu konta Azure Active Directory. Zamiast tego użyj konta Active Directory domeny.  

Czynności konfiguracyjne obejmują następujące procedury w celu skonfigurowania i użycia uwierzytelniania Azure Active Directory.

1. Utwórz i wypełnij usługę Azure AD.
2. Opcjonalne: Skojarz lub Zmień usługę Active Directory, która jest aktualnie skojarzona z subskrypcją platformy Azure.
3. Utwórz Azure Active Directory administratora dla serwera Azure SQL Database, wystąpienia zarządzanego lub [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Skonfiguruj komputery klienckie.
5. Utwórz użytkowników zawartej bazy danych w bazie danych zamapowanej na tożsamości usługi Azure AD.
6. Nawiąż połączenie z bazą danych za pomocą tożsamości usługi Azure AD.

> [!NOTE]
> Aby dowiedzieć się, jak utworzyć i wypełnić usługę Azure AD, a następnie skonfigurować usługę Azure AD za pomocą Azure SQL Database, wystąpienia zarządzanego i SQL Data Warehouse, zobacz [Konfigurowanie usługi Azure AD za pomocą Azure SQL Database](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Architektura zaufania

Poniższy diagram wysokiego poziomu podsumowuje architekturę rozwiązania przy użyciu uwierzytelniania usługi Azure AD za pomocą Azure SQL Database. Te same koncepcje dotyczą SQL Data Warehouse. Aby można było obsługiwać natywne hasło użytkownika usługi Azure AD, jest uwzględniana tylko część chmury i usługa Azure AD/Azure SQL Database. Aby zapewnić obsługę uwierzytelniania federacyjnego (lub użytkownika/hasła dla poświadczeń systemu Windows), wymagana jest komunikacja z blokiem ADFS. Strzałki oznaczają ścieżki komunikacji.

![Diagram uwierzytelniania usługi AAD][1]

Na poniższym diagramie przedstawiono relacje Federacji, zaufania i hostingu, które umożliwiają klientowi łączenie się z bazą danych przez przesłanie tokenu. Token jest uwierzytelniany przez usługę Azure AD i jest traktowany jako zaufany przez bazę danych. Klient 1 może reprezentować Azure Active Directory z natywnymi użytkownikami lub usługą Azure AD z użytkownikami federacyjnymi. Klient 2 oznacza możliwe rozwiązanie, w tym importowani użytkownicy; w tym przykładzie pochodzą z Azure Active Directory federacyjnej z usługą ADFS, które są synchronizowane z Azure Active Directory. Ważne jest, aby zrozumieć, że dostęp do bazy danych przy użyciu uwierzytelniania usługi Azure AD wymaga, aby subskrypcja hostingu była skojarzona z usługą Azure AD. Ta sama subskrypcja musi być używana do tworzenia SQL Server obsługujących Azure SQL Database lub SQL Data Warehouse.

![Relacja subskrypcji][2]

## <a name="administrator-structure"></a>Struktura administratora

W przypadku korzystania z uwierzytelniania usługi Azure AD istnieją dwa konta administratorów dla serwera SQL Database i wystąpienia zarządzanego; oryginalny administrator SQL Server i administrator usługi Azure AD. Te same koncepcje dotyczą SQL Data Warehouse. Tylko administrator oparty na koncie usługi Azure AD może utworzyć pierwszego użytkownika zawartej bazy danych usługi Azure AD w bazie danych użytkownika. Identyfikator logowania administratora usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli administrator jest kontem grupy, może być używany przez dowolnego członka grupy, co umożliwia wielu administratorom usługi Azure AD dla wystąpienia SQL Server. Użycie konta grupy jako administrator rozszerza możliwości zarządzania, umożliwiając centralne Dodawanie i usuwanie członków grupy w usłudze Azure AD bez zmiany użytkowników ani uprawnień w SQL Database. W dowolnej chwili można skonfigurować tylko jednego administratora usługi Azure AD (użytkownika lub grupę).

![Struktura administratora][3]

## <a name="permissions"></a>Uprawnienia

Aby utworzyć nowych użytkowników, musisz mieć uprawnienie `ALTER ANY USER` w bazie danych programu. Uprawnienie `ALTER ANY USER` można przyznać każdemu użytkownikowi bazy danych. Uprawnienia `ALTER ANY USER` są również przechowywane przez konta administratora serwera i użytkowników bazy danych z uprawnieniami `CONTROL ON DATABASE` lub `ALTER ON DATABASE` dla tej bazy danych oraz przez członków `db_owner` roli bazy danych.

Aby utworzyć użytkownika zawartej bazy danych w Azure SQL Database, wystąpieniu zarządzanym lub SQL Data Warehouse, musisz nawiązać połączenie z bazą danych lub wystąpieniem przy użyciu tożsamości usługi Azure AD. Aby utworzyć pierwszego zawartego użytkownika bazy danych, musisz nawiązać połączenie z bazą danych przy użyciu administratora usługi Azure AD (który jest właścicielem bazy danych). Jest to zademonstrowane w temacie [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim za pomocą SQL Database lub SQL Data Warehouse](sql-database-aad-authentication-configure.md). Każde uwierzytelnianie usługi Azure AD jest możliwe tylko wtedy, gdy administrator usługi Azure AD został utworzony dla serwera Azure SQL Database lub SQL Data Warehouse. Jeśli administrator Azure Active Directory został usunięty z serwera, istniejące Azure Active Directory użytkownicy utworzeni wcześniej w programie SQL Server nie mogą już łączyć się z bazą danych przy użyciu poświadczeń Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkcje i ograniczenia dotyczące usługi Azure AD

- W programie Azure SQL Server lub SQL Data Warehouse mogą być obsługiwane następujące elementy członkowskie usługi Azure AD:

  - Natywni Członkowie: członek utworzony w usłudze Azure AD w domenie zarządzanej lub w domenie klienta. Aby uzyskać więcej informacji, zobacz [Dodawanie własnej nazwy domeny do usługi Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Członkowie domeny federacyjnej: członek utworzony w usłudze Azure AD z domeną federacyjną. Aby uzyskać więcej informacji, zobacz [Microsoft Azure teraz obsługuje Federacji z systemem Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Zaimportowani członkowie z innej usługi Azure AD są członkami domeny natywnej lub federacyjnej.
  - Grupy Active Directory utworzone jako grupy zabezpieczeń.

- Użytkownicy usługi Azure AD będący częścią grupy, która ma rolę serwera `db_owner`, nie mogą używać składni **[poświadczeń "CREATE DATABASE scoped](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** " w odniesieniu do Azure SQL Database i Azure SQL Data Warehouse. Zostanie wyświetlony następujący błąd:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Przyznaj `db_owner` rolę bezpośrednio do użytkownika usługi Azure AD w celu ograniczenia problemu dotyczącego **tworzenia poświadczeń z określonym zakresem bazy danych** .

- Te funkcje systemowe zwracają wartości NULL, jeśli są wykonywane w ramach podmiotów zabezpieczeń usługi Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Wystąpienia zarządzane

- Nazwy główne i użytkownicy serwera usługi Azure AD są obsługiwane jako funkcja w wersji zapoznawczej dla [wystąpień zarządzanych](sql-database-managed-instance.md).
- Ustawienie podmiotów zabezpieczeń serwera usługi Azure AD (nazw logowania) mapowanych na grupę usługi Azure AD jako właściciel bazy danych nie jest obsługiwane w [wystąpieniach zarządzanych](sql-database-managed-instance.md).
    - Rozszerzenie to polega na tym, że po dodaniu grupy jako części roli serwera `dbcreator` użytkownicy z tej grupy mogą łączyć się z wystąpieniem zarządzanym i tworzyć nowe bazy danych, ale nie będą mogli uzyskać dostępu do bazy danych. Wynika to z faktu, że nowy właściciel bazy danych jest skojarzeniem zabezpieczeń, a nie użytkownikiem usługi Azure AD. Ten problem nie jest manifestem, jeśli pojedynczy użytkownik zostanie dodany do roli serwera `dbcreator`.
- Zarządzanie agentami SQL i wykonywanie zadań jest obsługiwane dla podmiotów zabezpieczeń serwera usługi Azure AD.
- Operacje tworzenia kopii zapasowej oraz przywracania bazy danych mogą być wykonywane przy użyciu jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Obsługiwane są inspekcje wszystkich instrukcji związanych z podmiotami zabezpieczeń serwera usługi Azure AD i zdarzeniami uwierzytelniania.
- Obsługiwane jest dedykowane połączenie administratora dla podmiotów zabezpieczeń serwera usługi Azure AD (logowania), które są członkami roli serwera sysadmin.
    - Obsługiwane przez Narzędzie SQLCMD i SQL Server Management Studio.
- Wyzwalacze logowania są obsługiwane dla zdarzeń logowania przychodzących z jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Service Broker i wiadomości e-mail można skonfigurować przy użyciu podmiotu zabezpieczeń serwera usługi Azure AD.


## <a name="connecting-using-azure-ad-identities"></a>Nawiązywanie połączenia przy użyciu tożsamości usługi Azure AD

Azure Active Directory Authentication obsługuje następujące metody łączenia się z bazą danych przy użyciu tożsamości usługi Azure AD:

- Azure Active Directory hasło
- Azure Active Directory zintegrowany
- Azure Active Directory Universal with MFA
- Korzystanie z uwierzytelniania za pomocą tokenu aplikacji

Następujące metody uwierzytelniania są obsługiwane dla podmiotów zabezpieczeń serwera usługi Azure AD (w**publicznej wersji zapoznawczej**):

- Azure Active Directory hasło
- Azure Active Directory zintegrowany
- Azure Active Directory Universal with MFA


### <a name="additional-considerations"></a>Dodatkowe zagadnienia

- W celu zwiększenia możliwości zarządzania zalecamy udostępnienie dedykowanej grupy usługi Azure AD jako administrator.   
- W dowolnym momencie można skonfigurować tylko jednego administratora usługi Azure AD (użytkownika lub grupę) dla serwera Azure SQL Database lub Azure SQL Data Warehouse.
  - Dodanie podmiotów zabezpieczeń serwera usługi Azure AD dla wystąpień zarządzanych (**publiczna wersja zapoznawcza**) umożliwia tworzenie wielu podmiotów nazw (Logins) serwera usługi Azure AD, które można dodać do roli `sysadmin`.
- Tylko administrator usługi Azure AD dla SQL Server może początkowo połączyć się z serwerem Azure SQL Database, wystąpieniem zarządzanym lub Azure SQL Data Warehouse przy użyciu konta Azure Active Directory. Administrator Active Directory może skonfigurować kolejnych użytkowników bazy danych usługi Azure AD.   
- Zalecamy ustawienie limitu czasu połączenia na 30 sekund.   
- SQL Server 2016 Management Studio i SQL Server Data Tools for Visual Studio 2015 (wersja 14.0.60311.1 kwietnia 2016 lub nowszy) obsługuje Azure Active Directory uwierzytelnianie. (Uwierzytelnianie usługi Azure AD jest obsługiwane przez **.NET Framework dostawca danych dla programu SqlServer**; co najmniej w wersji .NET Framework 4,6). W związku z tym najnowsze wersje tych narzędzi i aplikacji warstwy danych (DAC i. BACPAC) może korzystać z uwierzytelniania usługi Azure AD.   
- Począwszy od wersji 15.0.1, [narzędzia sqlcmd](/sql/tools/sqlcmd-utility) i obsługi [narzędzia bcp](/sql/tools/bcp-utility) Active Directory interakcyjnego uwierzytelniania przy użyciu usługi MFA.
- Narzędzia SQL Server Data Tools for Visual Studio 2015 wymagają co najmniej wersji 2016 z kwietnia (wersja za14.0.60311.1na). Obecnie użytkownicy usługi Azure AD nie są wyświetlani w SSDT Eksplorator obiektów. Aby obejść ten element, Wyświetl użytkowników w pliku [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Sterownik Microsoft JDBC 6,0 dla SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) obsługuje uwierzytelnianie w usłudze Azure AD. Ponadto zobacz [Ustawianie właściwości połączenia](https://msdn.microsoft.com/library/ms378988.aspx).   
- Nie można uwierzytelnić przy użyciu uwierzytelniania usługi Azure AD.   
- Uwierzytelnianie usługi Azure AD jest obsługiwane dla SQL Database przez Azure Portal **importowania bazy danych** i **eksportowania baz danych** . Importowanie i eksportowanie przy użyciu uwierzytelniania usługi Azure AD jest również obsługiwane przez polecenie programu PowerShell.   
- Uwierzytelnianie usługi Azure AD jest obsługiwane dla SQL Database, wystąpienia zarządzanego i SQL Data Warehouse za pomocą interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim za pomocą SQL Database lub SQL Data Warehouse](sql-database-aad-authentication-configure.md) i [SQL Server-AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć i wypełnić usługę Azure AD, a następnie skonfigurować usługę Azure AD za pomocą Azure SQL Database lub Azure SQL Data Warehouse, zobacz [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim za pomocą SQL Database, wystąpienia zarządzanego lub SQL Data Warehouse](sql-database-aad-authentication-configure.md).
- Aby zapoznać się z samouczkiem dotyczącym używania podmiotów zabezpieczeń serwera usługi Azure AD (logowania) z wystąpieniami zarządzanymi, zobacz [nazwy główne serwera usługi Azure AD z wystąpieniami zarządzanymi](sql-database-managed-instance-aad-security-tutorial.md)
- Dostęp i kontrola w usłudze SQL Database zostały omówione w temacie [Kontrola dostępu w usłudze SQL Database](sql-database-control-access.md).
- Dane logowania, użytkownicy i role bazy danych w usłudze SQL Database zostały omówione w temacie [Logins, users, and database roles](sql-database-manage-logins.md) (Dane logowania, użytkownicy i role bazy danych).
- Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń bazy danych, zobacz [Principals](https://msdn.microsoft.com/library/ms181127.aspx) (Podmioty zabezpieczeń).
- Aby uzyskać więcej informacji na temat ról bazy danych, zobacz [Database roles](https://msdn.microsoft.com/library/ms189121.aspx) (Role bazy danych).
- Aby zapoznać się ze składnią tworzenia podmiotów zabezpieczeń serwera usługi Azure AD (logowania) dla wystąpień zarządzanych, zobacz [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
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
