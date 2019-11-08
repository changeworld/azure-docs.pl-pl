---
title: Dostęp warunkowy
description: Dowiedz się, jak skonfigurować dostęp warunkowy dla Azure SQL Database i magazynu danych.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 03/29/2019
ms.openlocfilehash: 9b8c0dbe03e47d32d8194408663973f07a07b1b9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827159"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Dostęp warunkowy (MFA) z Azure SQL Database i magazynem danych  

[SQL Database](sql-database-technical-overview.md)platformy Azure, [wystąpienia zarządzane](sql-database-managed-instance.md)i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) obsługują dostęp warunkowy do firmy Microsoft. 

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

Poniższe kroki pokazują, jak skonfigurować SQL Database, aby wymusić zasady dostępu warunkowego.  

## <a name="prerequisites"></a>Wymagania wstępne  
- Należy skonfigurować SQL Database lub SQL Data Warehouse do obsługi uwierzytelniania Azure Active Directory. Aby zapoznać się z określonymi krokami, zobacz [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim za pomocą SQL Database lub SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- W przypadku włączenia uwierzytelniania wieloskładnikowego należy nawiązać połączenie za pomocą obsługiwanego narzędzia, takiego jak najnowszy program SSMS. Aby uzyskać więcej informacji, zobacz [konfigurowanie Azure SQL Database uwierzytelniania wieloskładnikowego dla SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurowanie urzędu certyfikacji dla usługi Azure SQL DB/DW  
1. Zaloguj się do portalu, wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **dostęp warunkowy**. Aby uzyskać więcej informacji, zobacz [Azure Active Directory informacje techniczne dotyczące dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![blok dostępu warunkowego](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. W bloku **zasady dostępu warunkowego** kliknij pozycję **nowe zasady**, podaj nazwę, a następnie kliknij pozycję **Konfiguruj reguły**.  
3. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**, zaznacz pole wyboru **Użytkownicy i grupy**, a następnie wybierz użytkownika lub grupę do dostępu warunkowego. Kliknij pozycję **Wybierz**, a następnie kliknij pozycję **gotowe** , aby zaakceptować wybór.  
   ![Wybieranie użytkowników i grup](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Wybierz pozycję **aplikacje w chmurze**, a następnie kliknij pozycję **Wybierz aplikacje**. Zobaczysz wszystkie aplikacje dostępne dla dostępu warunkowego. Wybierz pozycję **Azure SQL Database**, a następnie kliknij pozycję **Wybierz**, a następnie kliknij pozycję **gotowe**.  
   ![wybierz SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Jeśli nie możesz znaleźć **Azure SQL Database** wymienionych na poniższym, trzecim zrzucie ekranu, wykonaj następujące czynności:   
   - Zaloguj się do wystąpienia usługi Azure SQL DB/DW przy użyciu programu SSMS z kontem administratora AAD.  
   - Wykonaj `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Zaloguj się do usługi AAD i sprawdź, czy Azure SQL Database i magazyn danych są wymienione w aplikacjach w usłudze AAD.  

5. Wybierz pozycję **Kontrola dostępu**, wybierz pozycję **Udziel**, a następnie sprawdź zasady, które chcesz zastosować. W tym przykładzie wybieramy opcję **Wymagaj uwierzytelniania wieloskładnikowego**.  
   ![wybierz pozycję Udziel dostępu](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Podsumowanie  
Wybrana aplikacja (Azure SQL Database) umożliwiająca nawiązywanie połączenia z usługą Azure SQL DB/DW przy użyciu Azure AD — wersja Premium, teraz wymusza wybrane zasady dostępu warunkowego, **wymaga uwierzytelniania wieloskładnikowego.**  
Pytania dotyczące Azure SQL Database i magazynu danych dotyczącego uwierzytelniania wieloskładnikowego, MFAforSQLDB@microsoft.comkontaktów.  

## <a name="next-steps"></a>Następne kroki  

Aby zapoznać się z samouczkiem, zobacz temat [zabezpieczanie Azure SQL Database](sql-database-security-tutorial.md).
