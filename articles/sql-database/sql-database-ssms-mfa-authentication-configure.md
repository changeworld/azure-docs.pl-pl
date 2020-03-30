---
title: Konfigurowanie uwierzytelniania Multi-Factor Authentication
description: Dowiedz się, jak używać uwierzytelniania wieloskładnikowego z protokołem SSMS dla bazy danych SQL i magazynu danych SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 5d4d410f6fca566dab14e601972952b5996c331a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124890"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurowanie uwierzytelniania wieloskładnikowego dla programu SQL Server Management Studio i usługi Azure AD

W tym temacie pokazano, jak używać uwierzytelniania wieloskładnikowego usługi Azure Active Directory (MFA) w programie SQL Server Management Studio. Usługa Azure AD MFA może być używana podczas łączenia ssms lub sqlpackage.exe z [usługą](sql-database-technical-overview.md) Azure SQL Database i [MAGAZYNEM DANYCH SQL.](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Aby zapoznać się z omówieniem uwierzytelniania wieloskładnikowego usługi Azure SQL Database, zobacz [Uwierzytelnianie uniwersalne z bazą danych SQL i magazynem danych SQL (obsługa usługi SSMS dla usługi MFA).](sql-database-ssms-mfa-authentication.md)

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

## <a name="configuration-steps"></a>Kroki konfiguracji

1. **Konfigurowanie usługi Azure Active Directory** — aby uzyskać więcej informacji, zobacz [Administrowanie katalogiem usługi Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Dodawanie własnej nazwy domeny do usługi Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), Platforma Microsoft Azure obsługuje teraz [federację z usługą Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)i [zarządzaj usługą Azure AD przy użyciu programu Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Konfigurowanie usługi MFA** — instrukcje krok po kroku można znaleźć w [Conditional Access (MFA) with Azure SQL Database and Data Warehouse](sql-database-conditional-access.md)temacie [Uwierzytelnianie wieloskładnikowe platformy Azure?](../active-directory/authentication/multi-factor-authentication.md) (Pełny dostęp warunkowy wymaga usługi Premium Azure Active Directory (Azure AD). Usługa Limited MFA jest dostępna ze standardową usługą Azure AD.
3. **Konfigurowanie bazy danych SQL lub magazynu danych SQL dla uwierzytelniania usługi Azure AD** — instrukcje krok po kroku można znaleźć w temacie Łączenie się z [bazą danych SQL lub magazynem danych SQL przy użyciu uwierzytelniania usługi Azure Active Directory](sql-database-aad-authentication.md).
4. **Pobierz SSMS** - Na komputerze klienckim pobierz najnowsze usługi SSMS, pobierz [z programu DOWNLOAD SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/mt238290.aspx) W przypadku wszystkich funkcji w tym temacie należy użyć co najmniej lipca 2017 r. w wersji 17.2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Łączenie za pomocą uwierzytelniania uniwersalnego za pomocą usługi SSMS

Poniższe kroki pokazują, jak połączyć się z bazą danych SQL lub magazynem danych SQL przy użyciu najnowszego programu SSMS.

1. Aby połączyć się przy użyciu uwierzytelniania uniwersalnego, w oknie dialogowym **Łączenie z serwerem** wybierz pozycję **Active Directory — uniwersalne z obsługą usługi MFA**. (Jeśli widzisz **uwierzytelnianie uniwersalne usługi Active Directory,** nie korzystasz z najnowszej wersji usługi SSMS).  
   ![1mfa-universal-connect][1]  
2. Wypełnij pole **Nazwa użytkownika** poświadczeniami usługi Azure Active `user_name@domain.com`Directory w formacie .  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Jeśli łączysz się jako użytkownik-gość, nie musisz już wypełniać pola nazwa domeny usługi AD ani identyfikatora dzierżawy dla użytkowników-gości, ponieważ usługa SSMS 18.x lub nowsza automatycznie je rozpoznaje. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie uniwersalne z bazą danych SQL i magazynem danych SQL (obsługa usługi SSMS dla usługi MFA).](sql-database-ssms-mfa-authentication.md)
   ![mfa-no-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   Jeśli jednak użytkownik-gość łączy się jako użytkownik-gość przy użyciu systemu SSMS 17.x lub starszego, należy kliknąć przycisk **Opcje**, a następnie w oknie dialogowym **Właściwość połączenia** i ukończyć pole **Nazwa domeny usługi AD lub identyfikator dzierżawy.**
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. Jak zwykle w przypadku bazy danych SQL i magazynu danych SQL należy kliknąć pozycję **Opcje** i określić bazę danych w oknie dialogowym **Opcje.** (Jeśli połączony użytkownik jest użytkownikiem-gościem joe@outlook.com(tj.), należy zaznaczyć to pole wyboru i dodać bieżącą nazwę domeny usługi AD lub identyfikator dzierżawy jako część opcji. Zobacz [Uwierzytelnianie uniwersalne z bazą danych SQL i magazynem danych SQL (obsługa usługi SSMS dla usługi MFA).](sql-database-ssms-mfa-authentication.md) Następnie kliknij przycisk **Connect** (Połącz).  
5. Po **wyświetleniu** okna dialogowego Logowanie do konta podaj konto i hasło tożsamości usługi Azure Active Directory. Hasło nie jest wymagane, jeśli użytkownik jest częścią domeny sfederowanej z usługą Azure AD.  
   ![2mfa-zaloguj się][2]  

   > [!NOTE]
   > W przypadku uwierzytelniania uniwersalnego z kontem, które nie wymaga uwierzytelniania wieloskładnikowego, należy połączyć się w tym momencie. W przypadku użytkowników wymagających usługi MFA należy wykonać następujące czynności:
   >  
   
6. Mogą pojawić się dwa okna dialogowe konfiguracji usługi MFA. Ta jednorazowa operacja zależy od ustawienia administratora usługi MFA i dlatego może być opcjonalna. W przypadku domeny obsługującej usługi MFA ten krok jest czasami wstępnie zdefiniowany (na przykład domena wymaga od użytkowników użycia karty inteligentnej i pinu).  
   ![Konfiguracja 3mfa][3]  
7. Drugie możliwe jednokrotne okno dialogowe umożliwia wybranie szczegółów metody uwierzytelniania. Możliwe opcje są konfigurowane przez administratora.  
   ![4mfa-verify-1][4]  
8. Usługa Azure Active Directory wysyła do Ciebie informacje potwierdzające. Po otrzymaniu kodu weryfikacyjnego wprowadź go w polu **Wprowadź kod weryfikacyjny** i kliknij przycisk **Zaloguj**się .  
   ![5mfa-verify-2][5]  

Po zakończeniu weryfikacji program SSMS łączy zwykle przypuszczając prawidłowe poświadczenia i dostęp do zapory.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem uwierzytelniania wieloskładnikowego usługi Azure SQL Database, zobacz Uwierzytelnianie uniwersalne z [bazą danych SQL i magazynem danych SQL (obsługa usługi SSMS dla usługi MFA).](sql-database-ssms-mfa-authentication.md)  
- Udzielanie innym osobom dostępu do bazy danych: [uwierzytelnianie i autoryzacja bazy danych SQL: udzielanie dostępu](sql-database-manage-logins.md)  
- Upewnij się, że inne osoby mogą łączyć się za pośrednictwem zapory: [Konfigurowanie reguły zapory na poziomie serwera usługi Azure SQL Database przy użyciu witryny Azure portal](sql-database-configure-firewall-settings.md)  
- W przypadku korzystania **z usługi Active Directory — uniwersalnej z** uwierzytelnianiem usługi MFA, śledzenie ADAL jest dostępne począwszy od programu [SSMS 17.3.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) Domyślnie można włączyć śledzenie ADAL za pomocą menu **Narzędzia**, **Opcje,** w obszarze **Usługi Azure**, **Azure Cloud**, **ADAL Output Window Trace Level**, a następnie włączenie danych **wyjściowych** w menu **Widok.** Ślady są dostępne w oknie danych wyjściowych podczas wybierania **opcji usługi Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

