---
title: Przy użyciu uwierzytelniania wieloskładnikowego usługi AAD za pomocą usługi Azure SQL Database i Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Azure SQL Database i Azure SQL Data Warehouse obsługują połączenia z SQL Server Management Studio (SSMS), przy użyciu uwierzytelniania usługi Active Directory Universal.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 10/08/2018
ms.openlocfilehash: ccb78e201b90dfc27f52523348e76da57087bcc8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614176"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>Przy użyciu uwierzytelniania w usłudze Multi-Factor Authentication w usłudze AAD przy użyciu usługi Azure SQL Database i Azure SQL Data Warehouse (Obsługa programu SSMS dla usługi MFA)
Azure SQL Database i Azure SQL Data Warehouse obsługują połączenia z SQL Server Management Studio (SSMS) przy użyciu *Active Directory Universal Authentication*. W tym artykule omówiono różnice między różne opcje uwierzytelniania i ograniczeń związanych z użyciem uniwersalne uwierzytelnianie. 

**Pobieranie najnowszej wersji środowiska SSMS** — na komputerze klienckim, aby pobrać najnowszą wersję programu SSMS, [Pobierz SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Dla wszystkich funkcji omówionych w tym artykule Użyj co najmniej lipca 2017, wersja 17.2.  Okno dialogowe połączenia najnowszej powinien wyglądać podobnie do poniższej ilustracji:
 
  ![Połącz uniwersalnego 1mfa](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "kończy pole nazwy użytkownika.")  

## <a name="the-five-authentication-options"></a>Opcje uwierzytelniania pięć  

Uniwersalne uwierzytelnianie usługi Active Directory obsługuje dwie metody nieinterakcyjnych authentication:
    - `Active Directory - Password` Uwierzytelnianie
    - `Active Directory - Integrated` Uwierzytelnianie

Istnieją dwa uwierzytelnianie nieinterakcyjne modele, które mogą być używane w wielu różnych aplikacji (ADO.NET JDCB, ODC, itp.). Te dwie metody nigdy nie powoduje wyskakujących okien dialogowych: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Metody interakcyjnej jest to również obsługuje usługa Azure Multi-Factor authentication (MFA) jest: 
- `Active Directory - Universal with MFA` 


Usługa Azure MFA zabezpiecza dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Zapewnia ona silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji (połączenie telefoniczne, wiadomość SMS, kart inteligentnych przy użyciu numeru pin lub powiadomienie aplikacji mobilnej), pozwalając użytkownikom na wybór metody odpowiadającej im. Interaktywne MFA za pomocą usługi Azure AD może spowodować wyskakujące okno dialogowe do sprawdzania poprawności.

Aby uzyskać opis usługi Multi-Factor Authentication, zobacz [uwierzytelnianie wieloskładnikowe](../active-directory/authentication/multi-factor-authentication.md).
Kroki konfiguracji opisane w artykule [uwierzytelnianie wieloskładnikowe Konfigurowanie usługi Azure SQL Database, SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Usługa Azure AD nazwy lub dzierżawy parametr Identyfikatora domeny   

Począwszy od [SSMS wersja 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), użytkownicy, którzy są importowane do bieżącej usłudze Active Directory z innych katalogów Active Azure jako użytkowników-gości można podać nazwę domeny usługi Azure AD lub identyfikator dzierżawy, po nawiązaniu połączenia. Użytkownicy-goście obejmują użytkownicy zapraszani z innych usługa Azure AD, konta Microsoft, takich jak outlook.com, hotmail.com, live.com lub innych kont, takich jak gmail.com. Informacje te pozwalają **Active Directory Universal przy użyciu uwierzytelniania usługi MFA** do identyfikowania poprawne urzędu uwierzytelniania. Ta opcja jest również wymagane do obsługi kont Microsoft (MSA), takich jak outlook.com, hotmail.com, live.com lub kont innych niż konta Microsoft. Tych użytkowników, którzy chcą uwierzytelnienia za pomocą uniwersalne uwierzytelnianie, musisz wprowadzić nazwy domeny usługi Azure AD lub dzierżawy identyfikatora. Tego parametru reprezentuje bieżącej usłudze Azure AD domeny nazwy/Identyfikatora dzierżawy serwera usługi Azure jest połączona z. Na przykład, jeśli serwer usługi Azure jest skojarzony z domeną usługi Azure AD `contosotest.onmicrosoft.com` gdzie użytkownika `joe@contosodev.onmicrosoft.com` znajduje się jako użytkownik zaimportowany z usługi Azure AD domeny `contosodev.onmicrosoft.com`, nazwa domeny, wymagane do uwierzytelniania tego użytkownika to `contosotest.onmicrosoft.com`. Gdy użytkownik jest użytkownikiem natywnej usługi Azure AD, które są połączone z serwera usługi Azure i nie jest kontem MSA, wymagany jest identyfikator nazwy lub dzierżawy nie domeny. Wprowadzanie parametrów (począwszy od wersji 17.2 programu SSMS), w **Połącz z bazą danych** okna dialogowego Uzupełnij okno dialogowe Wybieranie **usługi Active Directory — uniwersalnego za pomocą usługi MFA** uwierzytelniania, kliknij  **Opcje**pełne **nazwa_użytkownika** , a następnie kliknij przycisk **właściwości połączenia** kartę. Sprawdź **Identyfikatora nazwy lub dzierżawy domeny AD** polu, a następnie podaj urzędu uwierzytelniania, takie jak nazwa domeny (**contosotest.onmicrosoft.com**) lub identyfikator GUID identyfikatora dzierżawy.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Obsługa firmami usługi Azure AD   
Użytkownicy usługi Azure AD obsługiwane dla scenariuszy B2B usługi Azure AD jako użytkowników-gości (zobacz [czym jest współpraca B2B w usłudze Azure](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) mogą łączyć się z bazy danych SQL Database i SQL Data Warehouse tylko jako część członkowie grupy utworzone w bieżącej usłudze Azure AD i mapować ręcznie za pomocą instrukcji języka Transact-SQL `CREATE USER` instrukcji w danej bazie danych. Na przykład jeśli `steve@gmail.com` zachęca się do usługi Azure AD `contosotest` (z domeną usługi Azure Ad `contosotest.onmicrosoft.com`), grupy usługi Azure AD, takie jak `usergroup` muszą być tworzone w usłudze Azure AD, która zawiera `steve@gmail.com` elementu członkowskiego. Następnie ta grupa musi zostać utworzony dla konkretnej bazy danych (czyli mojabazadanych) przez administratora usługi Azure AD SQL lub programu Azure AD DBO przez wykonanie instrukcji języka Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` instrukcji. Po użytkownik bazy danych jest następnie użytkownik `steve@gmail.com` zalogować się do `MyDatabase` przy użyciu opcji Uwierzytelnianie programu SSMS `Active Directory – Universal with MFA support`. Usergroup, domyślnie ma tylko uprawnienia connect i wszelkie dalsze dostęp do danych, należy mieć uprawnienia w normalny sposób. Należy pamiętać, że użytkownik `steve@gmail.com` jako użytkownik-Gość musi zaznacz pole wyboru i dodać nazwę domeny usługi AD `contosotest.onmicrosoft.com` w programie SSMS **właściwości połączenia** okno dialogowe. **Identyfikatora nazwy lub dzierżawy domeny AD** opcja jest obsługiwana tylko dla uniwersalnego przy użyciu opcji połączenia usługi MFA, w przeciwnym razie jest nieaktywna.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Uniwersalne ograniczenia uwierzytelniania dla bazy danych SQL Database i SQL Data Warehouse
- Program SSMS i SqlPackage.exe narzędzia są dostępne tylko aktualnie włączone dla usługi MFA za pośrednictwem uniwersalne uwierzytelnianie usługi Active Directory.
- SSMS wersji 17.2, obsługuje wielu użytkowników równoczesny dostęp przy użyciu uniwersalne uwierzytelnianie za pomocą usługi MFA. Wersja 17,0 i 17.1, ograniczone dane logowania dla wystąpienia programu SSMS do jednego konta usługi Azure Active Directory przy użyciu uniwersalne uwierzytelnianie. Aby zalogować się jako inny konta usługi Azure AD, należy użyć innego wystąpienia programu SSMS. (To ograniczenie jest ograniczona do uwierzytelniania usługi Active Directory Universal; możesz zalogować się do różnych serwerów przy użyciu uwierzytelniania hasła usługi Active Directory, zintegrowane uwierzytelnianie usługi Active Directory lub uwierzytelnianie programu SQL Server).
- SSMS obsługuje uwierzytelnianie usługi Active Directory Universal Eksplorator obiektów, Edytor zapytań i Query Store wizualizacji.
- SSMS wersji 17.2 zapewnia obsługę DacFx Kreator eksportu/Extract/wdrażanie danych bazy danych. Gdy określony użytkownik jest uwierzytelniany przy użyciu okna dialogowego uwierzytelniania początkowego, używając uniwersalne uwierzytelnianie i funkcje kreatora DacFx taki sam sposób, w przypadku innych metod uwierzytelniania.
- Projektant tabel SSMS nie obsługuje uniwersalne uwierzytelnianie.
- Istnieją nie dodatkowe wymagania programowe dla uniwersalnych uwierzytelniania usługi Active Directory, z tą różnicą, że musisz wykorzystać obsługiwaną wersję programu SSMS.  
- Uniwersalne uwierzytelnianie wersja Active Directory Authentication Library (ADAL) został zaktualizowany do najnowszej wersji wydanej dostępne ADAL.dll 3.13.9. Zobacz [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Kolejne kroki

- Kroki konfiguracji opisane w artykule [uwierzytelnianie wieloskładnikowe Konfigurowanie usługi Azure SQL Database, SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Udziel osobom dostęp do bazy danych: [Bazy danych uwierzytelnianie i autoryzacja SQL: Udzielanie dostępu](sql-database-manage-logins.md)  
- Upewnij się, że inne osoby mogły nawiązywać połączenia przez zaporę: [Skonfiguruj regułę zapory na poziomie serwera usługi Azure SQL Database przy użyciu witryny Azure portal](sql-database-configure-firewall-settings.md)  
- [Konfigurowanie i zarządzanie uwierzytelnianiem usługi Azure Active Directory przy użyciu usługi SQL Database lub SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importowanie pliku BACPAC do nowej bazy danych Azure SQL Database](../sql-database/sql-database-import.md)  
- [Eksportowanie bazy danych Azure SQL Database do pliku BACPAC](../sql-database/sql-database-export.md)  
- Interfejs C# [IUniversalAuthProvider interfejsu](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Korzystając z **Active Directory Universal za pomocą usługi MFA** uwierzytelniania ADAL śledzenia jest dostępna począwszy [17,3 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Wyłączone domyślnie, można włączyć śledzenie biblioteki ADAL przy użyciu **narzędzia**, **opcje** menu, w obszarze **usług platformy Azure**, **w chmurze platformy Azure**,  **Biblioteki ADAL poziom śledzenia okno danych wyjściowych**, a następnie włączenie **dane wyjściowe** w **widoku** menu. Ślady są dostępne w oknie danych wyjściowych po wybraniu **opcji usługi Azure Active Directory**.  
