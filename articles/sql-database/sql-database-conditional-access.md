---
title: Dostęp warunkowy — baza danych Azure SQL i magazynem danych | Doc firmy Microsoft
description: Informacje o sposobie konfigurowania warunkowego dostępu do bazy danych SQL Azure i magazynem danych.
services: sql-database
author: GithubMirek
manager: johammer
ms.custom: security
ms.service: sql-database
ms.topic: article
ms.date: 04/01/2018
ms.author: mireks
ms.openlocfilehash: f7f34bc7a28cdf3a23af54785002b12fba4baf1d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Dostęp warunkowy (MFA) i baza danych Azure SQL i magazynem danych  

Zarówno bazy danych SQL i magazyn danych SQL obsługuje dostęp warunkowy firmy Microsoft. Poniższe kroki przedstawiają sposób konfigurowania bazy danych SQL do wymuszania zasad dostępu warunkowego.  

## <a name="prerequisites"></a>Wymagania wstępne  
- Należy skonfigurować bazy danych SQL lub SQL Data Warehouse w celu obsługi uwierzytelniania usługi Azure Active Directory. Do wykonania określonych kroków, zobacz [Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure Active Directory z bazą danych SQL lub SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Po włączeniu usługi Multi-Factor authentication, należy połączyć z na obsługiwanych narzędzia, takie jak najnowsze narzędzia SSMS. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie wieloskładnikowe skonfigurować bazy danych SQL Azure dla programu SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurowanie urzędu certyfikacji dla bazy danych Azure SQL/magazynu danych  
1.  Zaloguj się do portalu, wybierz **usługi Azure Active Directory**, a następnie wybierz **dostępu warunkowego**. Aby uzyskać więcej informacji, zobacz [informacje techniczne dotyczące usługi Azure Active Directory dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
  ![Blok dostępu warunkowego](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  W **zasady dostępu warunkowego** bloku, kliknij przycisk **nowe zasady**, podaj nazwę, a następnie kliknij przycisk **skonfigurować reguły**.  
3.  W obszarze **przypisania**, wybierz pozycję **użytkowników i grup**, sprawdź **Wybierz użytkowników i grupy**, a następnie wybierz użytkownika lub grupę dostępu warunkowego. Kliknij przycisk **wybierz**, a następnie kliknij przycisk **gotowe** aby zaakceptować wybór.  
  ![Wybierz użytkowników i grup](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  Wybierz **aplikacji w chmurze**, kliknij przycisk **Wybierz aplikacje**. Możesz sprawdzić wszystkie aplikacje dostępne dla dostępu warunkowego. Wybierz **bazy danych SQL Azure**, u dołu kliknij **wybierz**, a następnie kliknij przycisk **gotowe**.  
  ![Wybierz bazę danych SQL](./media/sql-database-conditional-access/select-sql-database.png)  
  Jeśli nie możesz znaleźć **bazy danych SQL Azure** wymienionych w poniższym zrzucie ekranu trzeci, wykonaj następujące czynności:   
  - Zaloguj się do Twojego wystąpienia DW bazy danych SQL Azure przy użyciu narzędzia SSMS przy użyciu konta administratora usługi AAD.  
  - Wykonanie `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
  - Zaloguj się do usługi AAD i sprawdź, czy baza danych SQL Azure i magazynu danych są wyświetlane w aplikacji w sieci usługi AAD.  

5.  Wybierz **dostęp do formantów**, wybierz pozycję **Grant**, a następnie sprawdź zasady, które chcesz zastosować. Na przykład możemy wybierz **wymusić uwierzytelnianie wieloskładnikowe**.  
  ![Wybierz Udziel dostępu](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Podsumowanie  
Wybrana aplikacja (Azure SQL Database) umożliwiający połączenie do usługi Azure SQL bazy danych/magazynu danych przy użyciu usługi Azure AD Premium, teraz wymusza wybrane zasady dostępu warunkowego **wymagane uwierzytelnianie wieloskładnikowe.**  
Pytania dotyczące bazy danych SQL Azure i magazynem danych dotyczących uwierzytelniania wieloskładnikowego, skontaktuj się z MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Kolejne kroki  

Samouczek, zobacz [zabezpieczenia bazy danych SQL Azure](sql-database-security-tutorial.md).
