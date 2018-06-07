---
title: Uwierzytelnianie wieloskładnikowe - Azure SQL | Dokumentacja firmy Microsoft
description: Azure SQL Database i Azure SQL Data Warehouse obsługiwać połączenia z programu SQL Server Management Studio (SSMS), za pomocą uwierzytelniania uniwersalnego usługi Active Directory.
services: sql-database
documentationcenter: ''
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: mireks
ms.openlocfilehash: f3c94f41a4f5d7947b862054263ee07ff8ccd98c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650013"
---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Uwierzytelnianiem uniwersalnym z bazy danych SQL i magazyn danych SQL (Obsługa SSMS MFA)
Azure SQL Database i Azure SQL Data Warehouse obsługi połączeń z programu SQL Server Management Studio (SSMS) przy użyciu *uniwersalnych uwierzytelnianie usługi Active Directory*. 
**Pobierz najnowsze narzędzia SSMS** — na komputerze klienckim, aby pobrać najnowszą wersję programu SSMS, z [pobierania programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Dla wszystkich funkcji w tym temacie Użyj co najmniej 2017 lipca, wersja 17.2.  Okno dialogowe połączenia najnowszych wygląda następująco: ![połączenia uniwersalnego 1mfa](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "zakończeniu pole nazwy użytkownika.")  

## <a name="the-five-authentication-options"></a>Opcje uwierzytelniania pięć  
- Uniwersalnych uwierzytelnianie usługi Active Directory obsługuje dwie metody uwierzytelniania podejścia nieinterakcyjnego (`Active Directory - Password` uwierzytelniania i `Active Directory - Integrated` uwierzytelniania). Nieinterakcyjne `Active Directory - Password` i `Active Directory - Integrated` metody uwierzytelniania mogą być używane w wielu różnych aplikacji (ADO.NET JDBC, ODBC, itp.). Te dwie metody nigdy nie powoduje podręcznych oknach dialogowych.

- `Active Directory - Universal with MFA` uwierzytelnianie jest metody interakcyjnej, który również obsługuje *Azure Multi-Factor Authentication* (MFA). Usługa Azure MFA zabezpiecza dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Zapewnia silne uwierzytelnianie za pomocą różnych opcji weryfikacji łatwe (Rozmowa telefoniczna, wiadomość tekstowa, karty inteligentne z numeru pin lub powiadomienie aplikacji mobilnej), dzięki czemu użytkownicy mogą wybrać metodę preferowany. Interakcyjne MFA z usługą Azure AD może spowodować wyskakujące okno dialogowe do weryfikacji.

Opis usługi Multi-Factor Authentication, zobacz [uwierzytelnianie wieloskładnikowe](../active-directory/authentication/multi-factor-authentication.md).
Kroki konfiguracji, zobacz [uwierzytelnianie wieloskładnikowe skonfigurować bazy danych SQL Azure dla programu SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD nazwy lub dzierżawy parametr Identyfikatora domeny   

Począwszy od [SSMS wersji 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), użytkowników, którzy są importowane do bieżącej usłudze Active Directory z innych katalogów Active Azure jako goście, można podać nazwę domeny usługi Azure AD lub Identyfikatorem dzierżawy podczas nawiązywania połączenia. Goście obejmują zaproszenie z innych usługa Azure AD, konta Microsoft, takich jak outlook.com, hotmail.com, live.com lub innych kont, takich jak gmail.com użytkowników. Informacje te pozwalają **Active Directory uniwersalnej przy użyciu uwierzytelniania MFA** do identyfikowania poprawne urzędu uwierzytelniania. Ta opcja jest również wymagany do obsługi kont Microsoft (MSA), takich jak outlook.com, hotmail.com, live.com lub kont innych niż MSA. Tych użytkowników, którzy chcą uwierzytelnienia za pomocą uwierzytelniania uniwersalnego musisz wprowadzić nazwy domeny usługi Azure AD lub dzierżawy identyfikatora. Tego parametru reprezentuje bieżącej usługi Azure AD domeny nazwy/Identyfikatora dzierżawcy serwera Azure jest połączony z. Na przykład, jeśli serwer Azure jest skojarzony z domeny usługi Azure AD `contosotest.onmicrosoft.com` gdzie użytkownika `joe@contosodev.onmicrosoft.com` znajduje się jako użytkownik zaimportowane z domeny usługi Azure AD `contosodev.onmicrosoft.com`, nazwa domeny wymaganego do uwierzytelnienia tego użytkownika jest `contosotest.onmicrosoft.com`. Gdy użytkownik jest natywny użytkownika usługi Azure AD połączone z serwera usługi Azure i nie ma konta MSA, wymagany jest identyfikator nie domeny nazwy lub dzierżawcy. Aby wprowadzić parametr (począwszy od wersji 17.2 SSMS), w **Połącz z bazą danych** okna dialogowego Uzupełnij okno dialogowe Wybieranie **usługi Active Directory - uniwersalnego za pomocą usługi MFA** uwierzytelniania, kliknij  **Opcje**pełne **nazwy użytkownika** , a następnie kliknij przycisk **właściwości połączenia** kartę. Sprawdź **Identyfikatora nazwy lub dzierżawy domeny AD** polu i podaj urzędu uwierzytelniania, takie jak nazwa domeny (**contosotest.onmicrosoft.com**) lub identyfikator GUID identyfikator dzierżawcy.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Pomoc techniczna firma do firma platformy Azure AD   
Obsługiwane w przypadku scenariusze B2B usługi Azure AD jako goście Azure użytkowników usługi AD (zobacz [co to jest współpraca B2B usługi Azure](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) mogą łączyć się z bazą danych SQL i usługi SQL Data Warehouse tylko jako część członków grupy utworzone w bieżącej usłudze Azure AD i mapować ręcznie przy użyciu języka Transact-SQL `CREATE USER` instrukcji w danej bazie danych. Na przykład jeśli `steve@gmail.com` zachęca się do usługi Azure AD `contosotest` (z domeny usługi Azure Ad `contosotest.onmicrosoft.com`), grupa usługi Azure AD, takich jak `usergroup` muszą być tworzone w usłudze Azure AD, która zawiera `steve@gmail.com` elementu członkowskiego. Następnie tej grupy musi zostać utworzony dla określonej bazy danych (tj. mojabazadanych) przez administratora Azure AD SQL lub Azure AD DBO, wykonując języka Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` instrukcji. Utworzona użytkownika bazy danych jest następnie użytkownik `steve@gmail.com` może się zalogować do `MyDatabase` przy użyciu opcji uwierzytelniania SSMS `Active Directory – Universal with MFA support`. Usergroup, domyślnie ma tylko uprawnienia connect i wszelkie dodatkowe dostęp danych, który musi otrzymać w zwykły sposób. Należy pamiętać, że użytkownik `steve@gmail.com` użytkownika gościa musi zaznacz pole wyboru i Dodaj nazwę domeny AD `contosotest.onmicrosoft.com` w programie SSMS **właściwości połączenia** okno dialogowe. **Identyfikatora nazwy lub dzierżawy domeny AD** opcja jest obsługiwana tylko dla uniwersalnego z użyciem opcji połączenia usługi MFA, w przeciwnym razie jest nieaktywna.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Uniwersalny uwierzytelniania ograniczeń dotyczących korzystania z bazy danych SQL i magazyn danych SQL
- SSMS i SqlPackage.exe są tylko narzędzia aktualnie włączone dla usługi MFA za pomocą uwierzytelniania uniwersalnego usługi Active Directory.
- SSMS wersji 17.2, obsługuje współbieżny dostęp wielu użytkowników za pomocą uwierzytelniania uniwersalnego za pomocą usługi MFA. Wersja 17,0 i 17.1, ograniczone do dziennika w dla wystąpienia programu SSMS do jednego konta usługi Azure Active Directory za pomocą uwierzytelniania uniwersalnego. Aby zalogować się jako innego konta usługi Azure AD, należy użyć innego wystąpienia programu SSMS. (To ograniczenie jest ograniczona do uniwersalnych uwierzytelnianie usługi Active Directory, możesz zalogować się do różnych serwerów przy użyciu uwierzytelniania hasła w usłudze Active Directory, zintegrowane uwierzytelnianie usługi Active Directory lub uwierzytelniania programu SQL Server).
- SSMS obsługuje uwierzytelnianie usługi Active Directory uniwersalnych wizualizacji Eksplorator obiektów, edytora zapytań i Magazyn zapytań.
- SSMS wersji 17.2 zapewnia obsługę DacFx kreatora Wdróż-Export/wyodrębniania danych w bazie danych. Po uwierzytelnieniu określonego użytkownika za pomocą okna dialogowego początkowego uwierzytelniania przy użyciu uwierzytelniania uniwersalnego, funkcje kreatora DacFx taki sam sposób, w przypadku innych metod uwierzytelniania.
- Projektanta tabel SSMS nie obsługuje uwierzytelniania uniwersalnego.
- Istnieją nie dodatkowe wymagania programowe dla uniwersalnych uwierzytelnianie usługi Active Directory, z wyjątkiem tego, że musi używać obsługiwanej wersji programu SSMS.  
- Wersja Active Directory Authentication Library (ADAL) dla uniwersalnych uwierzytelniania został zaktualizowany do najnowszej dostępnej wersji wydanej ADAL.dll 3.13.9. Zobacz [biblioteki uwierzytelniania usługi Active Directory 3.14.1](http://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Kolejne kroki

- Kroki konfiguracji, zobacz [uwierzytelnianie wieloskładnikowe skonfigurować bazy danych SQL Azure dla programu SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Udostępnić innym bazy danych: [SQL bazy danych uwierzytelnianie i autoryzacja: udzielanie dostępu](sql-database-manage-logins.md)  
- Upewnij się, że inne mogły nawiązywać połączenia przez zaporę: [skonfigurować regułę zapory poziomu serwera bazy danych SQL Azure przy użyciu portalu Azure](sql-database-configure-firewall-settings.md)  
- [Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure Active Directory z bazą danych SQL lub SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Struktura aplikacji warstwy danych programu Microsoft SQL Server (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)  
- [Importowanie pliku BACPAC do nowej bazy danych Azure SQL Database](../sql-database/sql-database-import.md)  
- [Eksportowanie bazy danych Azure SQL Database do pliku BACPAC](../sql-database/sql-database-export.md)  
- C# interfejs [IUniversalAuthProvider — interfejs](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Korzystając z **Active Directory - Universal za pomocą usługi MFA** uwierzytelniania ADAL śledzenia jest dostępnych w programie [17,3 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Wyłącz domyślnie można włączyć śledzenie ADAL przy użyciu **narzędzia**, **opcje** menu, w obszarze **usług Azure**, **chmury Azure**,  **ADAL poziom śledzenia okno danych wyjściowych**, a następnie włączenie **dane wyjściowe** w **widoku** menu. Dane śledzenia są dostępne w oknie danych wyjściowych podczas wybierania **usługi Azure Active Directory**.  
