---
title: Skonfigurować uwierzytelnianie wieloskładnikowe — Azure SQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać uwierzytelniania Multi-Factored za pomocą programu SSMS dla bazy danych SQL Database i SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: b12508fbd0aec3bc6c697c920cd8ff94e3ac3396
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67119175"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurowanie uwierzytelniania wieloskładnikowego dla programu SQL Server Management Studio i usługi Azure AD

W tym temacie dowiesz się, jak używać usługi Azure Active Directory Multi-Factor authentication (MFA) przy użyciu programu SQL Server Management Studio. Usługa Azure AD MFA można używać podczas nawiązywania połączenia z programu SSMS lub SqlPackage.exe Azure [bazy danych SQL](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Omówienie uwierzytelniania wieloskładnikowego w usłudze Azure SQL Database, zobacz [uniwersalne uwierzytelnianie przy użyciu bazy danych SQL Database i SQL Data Warehouse (Obsługa programu SSMS dla usługi MFA)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

## <a name="configuration-steps"></a>Kroki konfiguracji

1. **Konfigurowanie usługi Azure Active Directory** — Aby uzyskać więcej informacji, zobacz [administrowanie katalogiem usługi Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrowanie tożsamości lokalnych z usługą Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [ Dodaj własną nazwę domeny do usługi Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure obsługuje teraz federacji z usługą Active Directory systemu Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), i [zarządzania usługi Azure AD przy użyciu programu Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Skonfiguruj usługę MFA** — instrukcje krok po kroku, zobacz [co to jest usługa Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [warunkowego dostępu (MFA) za pomocą usługi Azure SQL Database i Data Warehouse](sql-database-conditional-access.md). (Wymaga pełnego dostępu warunkowego, Premium usługi Azure Active Directory (Azure AD). Ograniczone usługi MFA jest dostępna przy użyciu standardowych usługi Azure AD).
3. **Konfigurowanie bazy danych SQL Database lub SQL Data Warehouse dla usługi Azure AD Authentication** — instrukcje krok po kroku, zobacz [nawiązywania połączenia z bazą danych SQL lub SQL danych magazynu przez przy użyciu usługi Azure Active Directory Authentication](sql-database-aad-authentication.md).
4. **Pobierz program SSMS** — na komputerze klienckim pobieranie najnowszej wersji środowiska SSMS z [Pobierz SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). W przypadku wszystkich funkcji w tym temacie Użyj co najmniej lipca 2017, wersja 17.2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Połączenie za pomocą uniwersalne uwierzytelnianie za pomocą programu SSMS

Poniższe kroki pokazują, jak połączyć się z bazą danych SQL lub SQL Data Warehouse przy użyciu najnowszej wersji środowiska SSMS.

1. Nawiązywanie połączenia przy użyciu uniwersalne uwierzytelnianie na **Połącz z serwerem** okno dialogowe, wybierz opcję **usługi Active Directory — uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego**. (Jeśli zostanie wyświetlony **Active Directory Universal Authentication** nie używasz najnowszej wersji programu SSMS.)  
   ![1mfa-universal-connect][1]  
2. Wykonaj **nazwa_użytkownika** pole przy użyciu poświadczeń usługi Azure Active Directory, w formacie `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Jeśli łączysz się jako użytkownika-gościa, należy kliknąć przycisk **opcje**i na **właściwości połączenia** okno dialogowe, pełną **Identyfikatora nazwy lub dzierżawy domeny AD** pole. Aby uzyskać więcej informacji, zobacz [uniwersalne uwierzytelnianie przy użyciu bazy danych SQL Database i SQL Data Warehouse (Obsługa programu SSMS dla usługi MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Jak zwykle dla bazy danych SQL Database i SQL Data Warehouse, należy kliknąć przycisk **opcje** i Określ bazę danych na **opcje** okno dialogowe. (Jeśli połączonego użytkownika jest użytkownikiem-gościem (czyli joe@outlook.com), musisz zaznacz pole wyboru i dodać bieżącej nazwy domeny usługi AD lub identyfikator dzierżawy jako część opcji. Zobacz [uniwersalne uwierzytelnianie przy użyciu bazy danych SQL Database i SQL Data Warehouse (Obsługa programu SSMS dla usługi MFA)](sql-database-ssms-mfa-authentication.md). Następnie kliknij przycisk **Connect** (Połącz).  
5. Gdy **Zaloguj się do swojego konta** pojawi się okno dialogowe, podaj konto i hasło dla tożsamości usługi Azure Active Directory. Hasło nie jest wymagane, jeśli użytkownik należy do domeny federacyjnej z usługą Azure AD.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Uniwersalne uwierzytelniania przy użyciu konta, które nie wymagają usługi MFA możesz łączyć się na tym etapie. Dla użytkowników korzystających z uwierzytelniania Wieloskładnikowego Kontynuuj wykonując następujące kroki:
   >  
   
6. Może pojawić się dwa MFA okien dialogowych. Ten jeden raz operacja zależy od administratora usługi MFA, ustawienia i w związku z tym może być opcjonalny. Dla domeny włączono uwierzytelnianie MFA czasami jest wstępnie zdefiniowane w tym kroku (na przykład domeny wymaga od użytkowników przy użyciu karty inteligentnej i numer pin).  
   ![3mfa-setup][3]  
7. Drugi najbardziej jeden raz, okno dialogowe umożliwia Wybierz szczegóły metody uwierzytelniania. Możliwe opcje są konfigurowane przez administratora.  
   ![4mfa-Sprawdź-1][4]  
8. Azure Active Directory wysyła informacje potwierdzające do Ciebie. Gdy otrzymasz kod weryfikacyjny, wprowadź go do **wprowadź kod weryfikacyjny** polu, a następnie kliknij przycisk **Zaloguj**.  
   ![5mfa-verify-2][5]  

Po zakończeniu weryfikacji SSMS łączy, zwykle przy założeniu prawidłowe poświadczenia i dostęp przez zaporę.

## <a name="next-steps"></a>Kolejne kroki

- Omówienie uwierzytelniania wieloskładnikowego w usłudze Azure SQL Database, zobacz uniwersalne uwierzytelnianie dzięki [bazy danych SQL Database i SQL Data Warehouse (Obsługa programu SSMS dla usługi MFA)](sql-database-ssms-mfa-authentication.md).  
- Udziel osobom dostęp do bazy danych: [Bazy danych uwierzytelnianie i autoryzacja SQL: Udzielanie dostępu](sql-database-manage-logins.md)  
- Upewnij się, że inne osoby mogły nawiązywać połączenia przez zaporę: [Skonfiguruj regułę zapory na poziomie serwera usługi Azure SQL Database przy użyciu witryny Azure portal](sql-database-configure-firewall-settings.md)  
- Korzystając z **Active Directory Universal za pomocą usługi MFA** uwierzytelniania ADAL śledzenia jest dostępna począwszy [17,3 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Wyłączone domyślnie, można włączyć śledzenie biblioteki ADAL przy użyciu **narzędzia**, **opcje** menu, w obszarze **usług platformy Azure**, **w chmurze platformy Azure**,  **Biblioteki ADAL poziom śledzenia okno danych wyjściowych**, a następnie włączenie **dane wyjściowe** w **widoku** menu. Ślady są dostępne w oknie danych wyjściowych po wybraniu **opcji usługi Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

