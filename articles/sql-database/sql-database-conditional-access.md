---
title: Dostęp warunkowy — usługi Azure SQL Database i Data Warehouse | Microsoft docs
description: Dowiedz się, jak skonfigurować dostęp warunkowy dla usługi Azure SQL Database i Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 03/29/2019
ms.openlocfilehash: 79d15a46affb2a6b7159ba080d4235073c59919c
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648987"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Dostęp warunkowy (MFA) przy użyciu usługi Azure SQL Database i Data Warehouse  

Azure [bazy danych SQL](sql-database-technical-overview.md), [wystąpienia zarządzanego](sql-database-managed-instance.md), i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) obsługują dostęp warunkowy Microsoft. 

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

Poniższe kroki pokazują sposób konfigurowania bazy danych SQL do wymuszania zasad dostępu warunkowego.  

## <a name="prerequisites"></a>Wymagania wstępne  
- Należy skonfigurować usługi SQL Database lub SQL Data Warehouse, aby zapewnić obsługę uwierzytelniania usługi Azure Active Directory. Aby poznać konkretne kroki, zobacz [Konfigurowanie i zarządzanie nimi w usłudze Azure Active Directory uwierzytelnianie przy użyciu bazy danych SQL Database lub SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Po włączeniu usługi Multi-Factor authentication, należy połączyć z w obsługiwanych narzędzia, takiego jak najnowszej wersji środowiska SSMS. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie wieloskładnikowe Konfigurowanie usługi Azure SQL Database, SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurowanie urzędu certyfikacji dla bazy danych/magazyn danych Azure SQL  
1. Zaloguj się do portalu, wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **dostępu warunkowego**. Aby uzyskać więcej informacji, zobacz [informacje techniczne dotyczące usługi Azure Active Directory dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![w bloku dostępu warunkowego](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. W **zasady dostępu warunkowego** bloku kliknij **nowe zasady**, podaj nazwę, a następnie kliknij przycisk **reguły**.  
3. W obszarze **przypisania**, wybierz opcję **użytkowników i grup**, sprawdź **Wybieranie użytkowników i grup**, a następnie wybierz użytkownika lub grupy, aby dostęp warunkowy. Kliknij przycisk **wybierz**, a następnie kliknij przycisk **gotowe** aby zaakceptować wybór.  
   ![Wybierz użytkowników i grupy](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Wybierz **aplikacje w chmurze**, kliknij przycisk **Wybierz aplikacje**. Możesz wyświetlać wszystkie aplikacje dostępne dla dostępu warunkowego. Wybierz **usługi Azure SQL Database**, kliknij u dołu **wybierz**, a następnie kliknij przycisk **gotowe**.  
   ![select SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Jeśli nie możesz znaleźć **usługi Azure SQL Database** wymienione w poniższym zrzucie ekranu trzeci, wykonaj następujące czynności:   
   - Zaloguj się do swojego wystąpienia bazy danych/magazyn danych SQL Azure przy użyciu narzędzia SSMS przy użyciu konta administratora usługi AAD.  
   - Wykonaj `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Zaloguj się do usługi AAD i sprawdź, czy usługi Azure SQL Database i Data Warehouse są wyświetlane w aplikacji w Twojej usłudze AAD.  

5. Wybierz **kontrole dostępu**, wybierz opcję **Grant**, a następnie sprawdź zasady, które chcesz zastosować. W tym przykładzie wybierzemy **Wymagaj uwierzytelniania wieloskładnikowego**.  
   ![Wybierz udzielanie dostępu](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Podsumowanie  
Wybranej aplikacji (Azure SQL Database), dzięki czemu można połączyć do bazy danych/magazyn danych SQL Azure przy użyciu usługi Azure AD Premium, teraz wymusza wybrane zasady dostępu warunkowego **wymagane uwierzytelnianie wieloskładnikowe.**  
Masz pytania dotyczące usługi Azure SQL Database i Data Warehouse dotyczące uwierzytelniania wieloskładnikowego, skontaktuj się z pomocą MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Kolejne kroki  

Aby zapoznać się z samouczkiem, zobacz [Zabezpieczanie usługi Azure SQL Database](sql-database-security-tutorial.md).
