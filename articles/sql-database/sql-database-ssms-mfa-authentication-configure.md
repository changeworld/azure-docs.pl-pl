---
title: Skonfiguruj usługę Multi-Factor authentication — Azure SQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z narzędzia SSMS Multi-Factored uwierzytelniania dla bazy danych SQL i magazyn danych SQL.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: mireks
ms.openlocfilehash: 47e05c5acbcd0c36efb7fcef2f0997aac4f46470
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Skonfiguruj usługę Multi-Factor authentication dla programu SQL Server Management Studio i Azure AD

W tym temacie przedstawiono sposób użycia usługi Azure Active Directory uwierzytelnianie wieloskładnikowe (MFA) z programu SQL Server Management Studio. Usługa Azure AD MFA można podczas nawiązywania połączenia SSMS lub SqlPackage.exe bazy danych SQL Azure i usługi Azure SQL Data Warehouse.

Omówienie uwierzytelniania wieloskładnikowego bazy danych SQL Azure, zobacz [uwierzytelnianiem uniwersalnym z bazy danych SQL i magazyn danych SQL (Obsługa SSMS MFA)](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Kroki konfiguracji

1. **Konfigurowanie usługi Azure Active Directory** — Aby uzyskać więcej informacji, zobacz [administrowanie katalogiem usługi Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrowanie tożsamości lokalnych z usługą Azure Active Directory](../active-directory/active-directory-aadconnect.md), [ Dodaj własną nazwę domeny do usługi Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure obsługuje teraz federacji z usługi Active Directory systemu Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), i [Zarządzanie usługą Azure AD przy użyciu programu Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Skonfigurowanie uwierzytelniania Wieloskładnikowego** — Aby uzyskać instrukcje, zobacz [co to jest usługa Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [warunkowego dostępu (MFA) z bazy danych SQL Azure i magazynem danych](sql-database-conditional-access.md). (Dostępu warunkowego pełnej wymaga Premium usługi Azure Active Directory (Azure AD). Ograniczone MFA jest dostępna z standardowe usługi Azure AD).
3. **Konfigurowanie bazy danych SQL lub magazynu danych SQL na potrzeby uwierzytelniania usługi Azure AD** — Aby uzyskać instrukcje, zobacz [połączenie z bazą danych SQL lub SQL danych magazynu przy użyciu Azure uwierzytelnianie usługi Active Directory](sql-database-aad-authentication.md).
4. **Pobierz narzędzia SSMS** — na komputerze klienckim, aby pobrać najnowsze narzędzia SSMS, z [pobierania programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Dla wszystkich funkcji w tym temacie Użyj co najmniej 2017 lipca, wersja 17.2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Łączenie za pomocą uwierzytelniania uniwersalnego SSMS

Poniższe kroki pokazują, jak nawiązać połączenie SQL Database lub SQL Data Warehouse przy użyciu najnowszych SSMS.

1. Nawiązywanie połączeń za pomocą uwierzytelniania uniwersalnego na **Połącz z serwerem** okno dialogowe, wybierz opcję **usługi Active Directory - uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego**. (Jeśli zostanie wyświetlony **uniwersalnych uwierzytelnianie usługi Active Directory** nie masz najnowszej wersji programu SSMS.)  
   ![1mfa-universal-connect][1]  
2. Zakończenie **nazwy użytkownika** pole przy użyciu poświadczeń usługi Azure Active Directory, w formacie `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Jeśli łączysz się jako użytkownik-Gość, należy kliknąć opcję **opcje**i na **właściwości połączenia** okno dialogowe, pełną **Identyfikatora nazwy lub dzierżawy domeny AD** pole. Aby uzyskać więcej informacji, zobacz [uwierzytelnianiem uniwersalnym z bazy danych SQL i magazyn danych SQL (Obsługa SSMS MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. W zwykły sposób dla bazy danych SQL i magazyn danych SQL, należy kliknąć opcję **opcje** i Określ bazę danych na **opcje** okno dialogowe. (Jeśli podłączonego użytkownika gościa (tj. joe@outlook.com), musisz zaznacz pole wyboru i dodać bieżącej nazwy domeny usługi AD lub Identyfikatorem dzierżawy jako część opcji. Zobacz [uwierzytelnianiem uniwersalnym z bazy danych SQL i magazyn danych SQL (Obsługa SSMS MFA)]()(sql — baza danych ssms-mfa-authentication.md. Następnie kliknij przycisk **Connect** (Połącz).  
5. Gdy **Zaloguj się do swojego konta** zostanie wyświetlone okno dialogowe, podaj konto i hasło tożsamości usługi Azure Active Directory. Hasło nie jest wymagane, jeśli użytkownik należy do domeny Sfederowanych z usługą Azure AD.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Uniwersalny do uwierzytelniania przy użyciu konta, które nie wymagają usługi MFA połączenie w tym momencie. Wymagania uwierzytelniania Wieloskładnikowego użytkownicy nadal następujące czynności:
   >  
   
6. Okna dialogowe dwa ustawienia usługi MFA może być wyświetlany. Jednorazowo operacja zależy od administratora MFA ustawienie i dlatego może być opcjonalny. Dla domeny włączone uwierzytelnianie MFA czasami jest wstępnie zdefiniowana w tym kroku (na przykład domeny wymaga od użytkowników przy użyciu karty inteligentnej i kod pin).  
   ![3mfa-setup][3]  
7. Możliwe drugi raz przez okno dialogowe służy do wybierania szczegóły metody uwierzytelniania. Możliwe opcje są konfigurowane przez administratora.  
   ![4mfa-Sprawdź-1][4]  
8. Azure Active Directory wysyła informacje potwierdzające do Ciebie. Gdy otrzymasz kod weryfikacyjny, wprowadź go do **wprowadź kod weryfikacyjny** i kliknij **Zaloguj**.  
   ![5mfa-verify-2][5]  

Po zakończeniu weryfikacji SSMS łączy zwykle przy założeniu dostępu zapory i prawidłowe poświadczenia.

## <a name="next-steps"></a>Kolejne kroki

- Omówienie uwierzytelniania wieloskładnikowego bazy danych SQL Azure, zobacz uniwersalnych uwierzytelniania za pomocą [bazy danych SQL i magazyn danych SQL (Obsługa SSMS MFA)](sql-database-ssms-mfa-authentication.md).  
- Udostępnić innym bazy danych: [SQL bazy danych uwierzytelnianie i autoryzacja: udzielanie dostępu](sql-database-manage-logins.md)  
- Upewnij się, że inne mogły nawiązywać połączenia przez zaporę: [skonfigurować regułę zapory poziomu serwera bazy danych SQL Azure przy użyciu portalu Azure](sql-database-configure-firewall-settings.md)  
- Korzystając z **Active Directory - Universal za pomocą usługi MFA** uwierzytelniania ADAL śledzenia jest dostępnych w programie [17,3 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Wyłącz domyślnie można włączyć śledzenie ADAL przy użyciu **narzędzia**, **opcje** menu, w obszarze **usług Azure**, **chmury Azure**,  **ADAL poziom śledzenia okno danych wyjściowych**, a następnie włączenie **dane wyjściowe** w **widoku** menu. Dane śledzenia są dostępne w oknie danych wyjściowych podczas wybierania **usługi Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

