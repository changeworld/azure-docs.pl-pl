---
title: Azure Active Directory auth - SQL Azure (omówienie) | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu korzystania z usługi Azure Active Directory do uwierzytelniania przy użyciu bazy danych SQL, zarządzane wystąpienia i magazyn danych SQL
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/07/2018
ms.author: mireks
ms.openlocfilehash: fe3864c3f765ff4858deede798d5641a55dd8aef
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>Użyj uwierzytelniania usługi Azure Active Directory do uwierzytelniania przy użyciu bazy danych SQL, zarządzane wystąpienia lub SQL Data Warehouse
Uwierzytelnianie usługi Active Directory systemu Azure jest mechanizmu nawiązywania połączenia z bazą danych SQL Azure Microsoft i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD). Przy użyciu uwierzytelniania usługi Azure AD mogą centralnie zarządzać tożsamości użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie identyfikator udostępnia jedno miejsce do zarządzania użytkownikami bazy danych i upraszcza zarządzanie uprawnieniami. Następujące korzyści:

* Zapewnia zamiast uwierzytelniania programu SQL Server.
* Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach bazy danych.
* Umożliwia obrotu hasła w jednym miejscu.
* Klienci mogą zarządzać uprawnień do bazy danych przy użyciu grup zewnętrzne (Azure AD).
* Można go wyeliminować zapisywania haseł przez włączenie zintegrowanego uwierzytelniania systemu Windows i innych metod uwierzytelniania obsługiwanych przez usługę Azure Active Directory.
* Usługa Azure AD authentication używa użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
* Usługi Azure AD obsługuje uwierzytelnianie na podstawie tokenu dla aplikacji łączenia z bazą danych SQL.
* Usługa Azure AD authentication obsługuje usług AD FS (Federacja domen) lub native użytkownika i hasło uwierzytelniania lokalnej usługi Azure Active Directory bez synchronizacji domeny.  
* Usługi Azure AD obsługuje połączenia z programu SQL Server Management Studio, które używają uniwersalnych uwierzytelnianie usługi Active Directory, która obejmuje usługi Multi-Factor Authentication (MFA).  Silne uwierzytelnianie za pomocą różnych opcji weryfikacji łatwe obejmuje MFA — połączenie telefoniczne, wiadomość tekstowa, karty inteligentne z numeru pin lub powiadomienie aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz [SSMS obsługę usługi Azure AD MFA z bazy danych SQL i usługi SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).  

>  [!NOTE]  
>  Dołączanie do programu SQL Server uruchomionego na maszynie Wirtualnej platformy Azure nie jest obsługiwane przy użyciu konta usługi Azure Active Directory. Zamiast tego użyj domeny konta usługi Active Directory.  

Kroki konfiguracji obejmują następujących procedur, aby skonfigurować i korzystać z uwierzytelniania usługi Azure Active Directory.

1. Utworzyć i wypełnić usługi Azure AD.
2. Opcjonalnie: Skojarz lub zmienić usługi active directory, który jest aktualnie powiązany z subskrypcją platformy Azure.
3. Tworzenie administratora usługi Azure Active Directory na serwerze bazy danych SQL Azure wystąpienia zarządzane lub [magazyn danych SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Konfigurowanie komputerów klienckich.
5. Utwórz użytkowników zawartej bazy danych w bazie danych mapowany na tożsamość usługi Azure AD.
6. Połączenia z bazą danych przy użyciu tożsamości usługi Azure AD.

> [!NOTE]
> Aby dowiedzieć się, jak utworzyć i wypełnić usługi Azure AD, a następnie skonfiguruj usługi Azure AD z bazy danych SQL Azure, zarządzane wystąpienia i magazyn danych SQL, zobacz [Konfigurowanie usługi Azure AD z bazy danych SQL Azure](sql-database-aad-authentication-configure.md).
>

## <a name="trust-architecture"></a>Architektura zaufania
Poniższy diagram ogólny zawiera podsumowanie architektury rozwiązania przy użyciu uwierzytelniania usługi Azure AD z bazy danych SQL Azure. Tego samego pojęcia mają zastosowanie do usługi SQL Data Warehouse. Aby obsługiwać hasło macierzysty użytkownika usługi Azure AD, jest uważany za tylko części chmury i Azure AD/usługi Azure SQL Database. Do obsługi uwierzytelniania federacyjnym (lub użytkownika i hasło dla poświadczeń systemu Windows), komunikacja z bloku usług AD FS jest wymagana. Strzałki oznaczają ścieżki komunikacji.

![diagram uwierzytelniania usługi AAD][1]

Poniższy diagram wskazuje federacyjnego zaufania i relacji hostingu, zezwalających na połączenia klienta z bazy danych poprzez przesłanie tokenu. Token jest uwierzytelniony przez usługę Azure AD i jest zaufany przez bazę danych. Odbiorcy 1 może reprezentować usługi Azure Active Directory z macierzystego użytkowników lub usługi Azure AD z użytkowników federacyjnych. Odbiorcy 2 reprezentuje możliwe rozwiązanie, łącznie z importowanych użytkowników. w tym przykładzie pochodzące z federacyjnych usługi Azure Active Directory z usług AD FS są synchronizowane z usługą Azure Active Directory. Należy zrozumieć, że dostęp do bazy danych przy użyciu uwierzytelniania usługi Azure AD wymaga obsługi subskrypcji skojarzonych z usługą Azure AD. Tej samej subskrypcji może służyć do tworzenia programu SQL Server obsługującym bazę danych SQL Azure lub usługi SQL Data Warehouse.

![Relacja subskrypcji][2]

## <a name="administrator-structure"></a>Struktura administratora
Podczas korzystania z uwierzytelniania usługi Azure AD, istnieją dwa konta administratora bazy danych SQL server i zarządzane wystąpienia; oryginalny administratora programu SQL Server i administratora usługi Azure AD. Tego samego pojęcia mają zastosowanie do usługi SQL Data Warehouse. Tylko administrator na podstawie konta usługi Azure AD można utworzyć pierwszy użytkownik bazy danych usługi Azure AD zawarte w bazie danych użytkownika. Identyfikator logowania administratora usługi Azure AD może być użytkownika usługi Azure AD lub grupy w usłudze Azure AD. Jeśli administrator konta grupy, może służyć przez dowolnego członka grupy, włączania wielu administratorów usługi Azure AD dla wystąpienia programu SQL Server. Przy użyciu konta grupy jako administrator zwiększa możliwości zarządzania, umożliwiając centralnie dodawania i usuwania członków grupy w usłudze Azure AD bez zmiany użytkowników lub uprawnienia w bazie danych SQL. W dowolnym momencie można skonfigurować tylko jeden administrator usługi Azure AD (użytkownik lub grupa).

![Struktura administratora][3]

## <a name="permissions"></a>Uprawnienia
Aby utworzyć nowych użytkowników, musisz mieć `ALTER ANY USER` uprawnień w bazie danych. `ALTER ANY USER` Można można udzielić uprawnienia do innych użytkowników bazy danych. `ALTER ANY USER` Uprawnień odbywa się również z kont administratora serwera i bazy danych użytkowników z `CONTROL ON DATABASE` lub `ALTER ON DATABASE` uprawnienia dla tej bazy danych i członkowie `db_owner` roli bazy danych.

Aby utworzyć użytkownika zawartej bazy danych usługi Azure SQL Database, zarządzane wystąpienia lub SQL Data Warehouse, należy połączyć do bazy danych lub wystąpienia przy użyciu tożsamości usługi Azure AD. Aby utworzyć pierwszy użytkownik zawartej bazy danych, należy połączyć do bazy danych przy użyciu administrator usługi Azure AD (który jest właścicielem bazy danych). To jest przedstawiona w [Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure Active Directory z bazą danych SQL lub SQL Data Warehouse](sql-database-aad-authentication-configure.md). Uwierzytelniania usługi Azure AD jest możliwe tylko wtedy, jeśli administrator usługi Azure AD została utworzona dla serwera Azure SQL Database lub SQL Data Warehouse. Jeśli administrator usługi Azure Active Directory został usunięty z serwera, istniejących użytkowników usługi Azure Active Directory utworzony wcześniej w programie SQL Server można już połączyć z bazą danych przy użyciu swoich poświadczeń usługi Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkcje platformy Azure AD i ograniczenia
Następujące elementy członkowskie programu Azure AD można udostępnić w Azure SQL server lub SQL Data Warehouse:

- Natywny elementów członkowskich: element członkowski utworzone w usłudze Azure AD w domenie zarządzanej lub w domenie klienta. Aby uzyskać więcej informacji, zobacz [Dodaj własną nazwę domeny do usługi Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
- Elementy członkowskie domeny federacyjnej: element członkowski utworzone w usłudze Azure AD z domeny federacyjnej. Aby uzyskać więcej informacji, zobacz [Microsoft Azure obsługuje teraz federacji z usługi Active Directory systemu Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).
- Importowany członków z innych usługi Azure AD, którzy są członkami domeny native lub federacyjnych.
- Grup usługi Active Directory tworzone jako grupy zabezpieczeń.

Azure AD ograniczenia związane z wystąpienia zarządzane:
- Tylko administrator usługi Azure AD można utworzyć bazy danych, ograniczone do jednej bazy danych użytkowników usługi Azure AD, a nie ma to uprawnienie
- Własność bazy danych:
  - Azure AD podmiot zabezpieczeń nie można zmienić właściciela bazy danych (ALTER autoryzacji ON DATABASE) i nie można ustawić jako właściciela.
  - Dla baz danych utworzonych przez administratora usługi Azure AD jest ustawiony nie własność (pole owner_sid w sys.sysdatabases jest 0x1).
- Nie można zarządzać agenta SQL, gdy zalogowany za pomocą usługi Azure AD podmiotów. 
- Nie można spersonifikować administratora usługi Azure AD przy użyciu EXECUTE AS
- Połączenie DAC nie jest obsługiwane z podmiotami zabezpieczeń usługi Azure AD. 

Te funkcje systemu zwracać wartości NULL, gdy wykonywane w ramach podmiotów zabezpieczeń usługi Azure AD:
- `SUSER_ID()`
- `SUSER_NAME(<admin ID>)`
- `SUSER_SNAME(<admin SID>)`
- `SUSER_ID(<admin name>)`
- `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Łączenie za pomocą tożsamości usługi Azure AD

Uwierzytelnianie usługi Active Directory platformy Azure obsługuje połączenia z bazą danych przy użyciu tożsamości usługi Azure AD z następujących metod:

* Przy użyciu zintegrowanego uwierzytelniania systemu Windows
* Za pomocą głównej nazwy usługi Azure AD i hasła
* Przy użyciu tokenu uwierzytelniania aplikacji

### <a name="additional-considerations"></a>Dodatkowe zagadnienia

* W celu zwiększenia możliwości zarządzania, zaleca się udostępniania dedykowanych usługi Azure AD grupy jako administrator.   
* Tylko jeden administrator usługi Azure AD (użytkownik lub grupa) można skonfigurować dla serwera bazy danych SQL Azure, zarządzane wystąpienia lub magazyn danych SQL Azure w dowolnym momencie.   
* Tylko administrator usługi Azure AD dla programu SQL Server początkowo mogą łączyć się serwera bazy danych SQL Azure, zarządzane wystąpienia lub magazyn danych SQL Azure przy użyciu konta usługi Azure Active Directory. Administrator usługi Active Directory można skonfigurować na kolejnych usługi Azure AD bazy danych użytkowników.   
* Zaleca się ustawiania limitu czasu połączenia do 30 sekund.   
* SQL Server 2016 Management Studio i SQL Server Data Tools dla programu Visual Studio 2015 (wersja 14.0.60311.1April 2016 lub nowszy) obsługują uwierzytelniania usługi Azure Active Directory. (Uwierzytelniania usługi azure AD jest obsługiwana przez **dostawcy danych .NET Framework dla serwera SQL**; co najmniej wersji .NET Framework 4.6). W związku z tym najnowsze wersje tych narzędzi i aplikacji warstwy danych (DAC i. Pliku BACPAC) można używać uwierzytelniania usługi Azure AD.   
* [ODBC w wersji 13.1](https://www.microsoft.com/download/details.aspx?id=53339) obsługuje jednak uwierzytelniania usługi Azure Active Directory `bcp.exe` nie można połączyć przy użyciu uwierzytelniania usługi Azure Active Directory, ponieważ używa starszej Dostawca ODBC.   
* `sqlcmd` obsługuje usługi Azure Active Directory uwierzytelniania, począwszy od wersji dostępnej w sklepie 13.1 [Centrum pobierania](http://go.microsoft.com/fwlink/?LinkID=825643).   
* SQL Server Data Tools dla programu Visual Studio 2015 wymaga co najmniej wersji kwietnia 2016 narzędzi danych (wersja 14.0.60311.1). Obecnie użytkowników usługi Azure AD nie są wyświetlane w Eksploratorze obiektów program SSDT. Jako obejście, wyświetlania informacji o użytkownikach w [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
* [6.0 sterownik JDBC firmy Microsoft dla programu SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) usługi Azure AD obsługuje uwierzytelnianie. Zobacz też [ustawienie właściwości połączenia](https://msdn.microsoft.com/library/ms378988.aspx).   
* Program PolyBase nie można uwierzytelnić przy użyciu uwierzytelniania usługi Azure AD.   
* Usługa Azure AD authentication jest obsługiwana dla bazy danych SQL w portalu Azure **Importuj bazę danych** i **eksportu bazy danych** bloków. Importowanie i eksportowanie przy użyciu uwierzytelniania usługi Azure AD jest również obsługiwany z polecenia programu PowerShell.   
* Usługa Azure AD authentication jest obsługiwana dla bazy danych SQL, zarządzane wystąpienia i SQL Data Warehouse przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure Active Directory z bazą danych SQL lub SQL Data Warehouse](sql-database-aad-authentication-configure.md) i [SQL Server — az programu sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się, jak utworzyć i wypełnić usługi Azure AD, a następnie skonfiguruj usługi Azure AD z bazy danych SQL Azure lub usługi Azure SQL Data Warehouse, zobacz [Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure Active Directory z bazy danych SQL, zarządzane wystąpienia lub SQL Data Warehouse ](sql-database-aad-authentication-configure.md).
- Dostęp i kontrola w usłudze SQL Database zostały omówione w temacie [Kontrola dostępu w usłudze SQL Database](sql-database-control-access.md).
- Dane logowania, użytkownicy i role bazy danych w usłudze SQL Database zostały omówione w temacie [Logins, users, and database roles](sql-database-manage-logins.md) (Dane logowania, użytkownicy i role bazy danych).
- Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń bazy danych, zobacz [Principals](https://msdn.microsoft.com/library/ms181127.aspx) (Podmioty zabezpieczeń).
- Aby uzyskać więcej informacji na temat ról bazy danych, zobacz [Database roles](https://msdn.microsoft.com/library/ms189121.aspx) (Role bazy danych).
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

