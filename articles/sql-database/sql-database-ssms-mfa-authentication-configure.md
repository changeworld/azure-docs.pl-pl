---
title: Konfigurowanie uwierzytelniania wieloskładnikowego — Azure SQL | Microsoft Docs
description: Dowiedz się, jak używać uwierzytelniania wieloskładnikowego w programie SSMS dla SQL Database i SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/25/2018
ms.openlocfilehash: 1bccfd8ac363b21053c45ed489e943a1b488f41f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566511"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurowanie uwierzytelniania wieloskładnikowego dla SQL Server Management Studio i usługi Azure AD

W tym temacie pokazano, jak używać usługi Azure Active Directory Authentication (MFA) z SQL Server Management Studio. Usługi Azure AD MFA można używać podczas nawiązywania połączenia z programem SSMS lub sqlpackage. exe z platformą Azure [SQL Database](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Aby zapoznać się z omówieniem Azure SQL Database usługi uwierzytelniania wieloskładnikowego, zobacz [uniwersalne uwierzytelnianie z SQL Database i SQL Data Warehouse (Obsługa programu SSMS dla usługi MFA)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

## <a name="configuration-steps"></a>Kroki konfiguracji

1. **Konfigurowanie Azure Active Directory** — Aby uzyskać więcej informacji, zobacz [administrowanie katalogiem usługi Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrowanie tożsamości lokalnych z usługą Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Dodawanie własnej nazwy domeny do usługi Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) [. Microsoft Azure teraz obsługuje Federacji z systemem Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)i [Zarządzanie usługą Azure AD przy użyciu programu Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Konfigurowanie usługi MFA** — Aby uzyskać instrukcje krok po kroku, zobacz artykuł [co to jest uwierzytelnianie wieloskładnikowe systemu Azure?](../active-directory/authentication/multi-factor-authentication.md), [dostęp warunkowy (MFA) z Azure SQL Database i magazynem danych](sql-database-conditional-access.md). (Pełny dostęp warunkowy wymaga Azure Active Directory w warstwie Premium (Azure AD). Ograniczone uwierzytelnianie MFA jest dostępne w przypadku standardowej usługi Azure AD.
3. **Konfigurowanie SQL Database lub SQL Data Warehouse na potrzeby uwierzytelniania w usłudze Azure AD** — Aby uzyskać instrukcje krok po kroku, zobacz [nawiązywanie połączenia z SQL Database lub SQL Data Warehouse przy użyciu uwierzytelniania Azure Active Directory](sql-database-aad-authentication.md).
4. **Pobierz program SSMS** — na komputerze klienckim Pobierz najnowszą wersję programu ssms z [SQL Server Management Studio pobierania (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Dla wszystkich funkcji w tym temacie należy użyć co najmniej lipca 2017, wersja 17,2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Nawiązywanie połączenia przy użyciu uwierzytelniania uniwersalnego za pomocą programu SSMS

Poniższe kroki pokazują, jak nawiązać połączenie z usługą SQL Database lub SQL Data Warehouse przy użyciu najnowszego programu SSMS.

1. Aby nawiązać połączenie przy użyciu uwierzytelniania uniwersalnego, w oknie dialogowym **łączenie z serwerem** wybierz opcję **Active Directory — uniwersalna z obsługą usługi MFA**. (Jeśli widzisz **Active Directory uniwersalnego uwierzytelniania** , nie jesteś w najnowszej wersji programu SSMS).  
   ![1mfa-universal-connect][1]  
2. Wypełnij pole **Nazwa użytkownika** z poświadczeniami Azure Active Directory w formacie `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Jeśli łączysz się jako użytkownik-Gość, musisz kliknąć przycisk **Opcje**, a następnie w oknie dialogowym **Właściwości połączenia** wypełnić pole **nazwa domeny usługi AD lub identyfikator dzierżawy** . Aby uzyskać więcej informacji, zobacz [uniwersalne uwierzytelnianie przy użyciu SQL Database i SQL Data Warehouse (Obsługa programu SSMS dla usługi MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Jak zwykle w przypadku SQL Database i SQL Data Warehouse, należy kliknąć przycisk **Opcje** i określić bazę danych w oknie dialogowym **Opcje** . (Jeśli połączony użytkownik jest użytkownikiem-gościem (tj. joe@outlook.com), należy zaznaczyć pole wyboru i dodać bieżącą nazwę domeny usługi AD lub identyfikator dzierżawy jako część opcji. Zobacz [uniwersalne uwierzytelnianie przy użyciu SQL Database i SQL Data Warehouse (Obsługa programu SSMS dla usługi MFA)](sql-database-ssms-mfa-authentication.md). Następnie kliknij przycisk **Connect** (Połącz).  
5. Gdy pojawi się okno dialogowe **Logowanie do konta** , podaj konto i hasło tożsamości Azure Active Directory. Jeśli użytkownik jest częścią domeny federacyjnej z usługą Azure AD, nie jest wymagane hasło.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > W przypadku uwierzytelniania uniwersalnego przy użyciu konta, które nie wymaga uwierzytelniania MFA, można nawiązać połączenie w tym punkcie. W przypadku użytkowników wymagających uwierzytelniania wieloskładnikowego wykonaj następujące czynności:
   >  
   
6. Mogą pojawić się dwa okna dialogowe konfiguracji usługi MFA. Ta operacja jednorazowa zależy od ustawienia administratora usługi MFA i dlatego może być opcjonalna. W przypadku domeny z obsługą usługi MFA ten krok jest czasami wstępnie zdefiniowany (na przykład domena wymaga od użytkowników korzystania z karty inteligentnej i numeru PIN).  
   ![3mfa-setup][3]  
7. Drugie możliwe okno dialogowe jednorazowe pozwala wybrać szczegóły metody uwierzytelniania. Możliwe opcje są konfigurowane przez administratora.  
   ![4mfa — Weryfikuj-1][4]  
8. Azure Active Directory wysyła do Ciebie informacje potwierdzające. Po otrzymaniu kodu weryfikacyjnego wprowadź go w polu **Wprowadź kod weryfikacyjny** , a następnie kliknij przycisk **Zaloguj**.  
   ![5mfa-verify-2][5]  

Po zakończeniu weryfikacji program SSMS nawiązuje połączenie zwykle z założeniem prawidłowych poświadczeń i dostępu do zapory.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem Azure SQL Database usługi uwierzytelniania wieloskładnikowego, zobacz uniwersalne uwierzytelnianie z [SQL Database i SQL Data Warehouse (Obsługa programu SSMS dla usługi MFA)](sql-database-ssms-mfa-authentication.md).  
- Przyznaj innym osobom dostęp do bazy danych: [SQL Database uwierzytelnianie i autoryzacja: Udzielanie dostępu](sql-database-manage-logins.md)  
- Upewnij się, że inne osoby mogą łączyć się za pomocą zapory: [Konfigurowanie reguły zapory na poziomie serwera Azure SQL Database przy użyciu Azure Portal](sql-database-configure-firewall-settings.md)  
- W przypadku korzystania **z Active Directory — uniwersalne z** uwierzytelnianiem MFA, śledzenie ADAL jest dostępne od programu [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Domyślnie wyłączone jest śledzenie ADAL przy użyciu **narzędzi**, menu **opcji** , w obszarze **usługi platformy Azure**, **Azure Cloud**, **ADAL okno dane wyjściowe poziom śledzenia**, a następnie przez włączenie **danych wyjściowych** w menu **Widok** . Ślady są dostępne w oknie danych wyjściowych w przypadku wybrania **opcji Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

