---
title: Dostęp warunkowy
description: Dowiedz się, jak skonfigurować dostęp warunkowy dla usługi Azure SQL Database i Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124906"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Dostęp warunkowy (MFA) z usługą Azure SQL Database i usługa Azure Synapse Analytics

Usługa Azure [SQL Database](sql-database-technical-overview.md), [wystąpienie zarządzane](sql-database-managed-instance.md)i usługa Azure [Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) obsługują dostęp warunkowy firmy Microsoft. 

> [!NOTE]
> W tym temacie stosuje się do serwera SQL platformy Azure oraz do bazy danych SQL i usługi Azure Synapse, które są tworzone na serwerze SQL platformy Azure. Dla uproszczenia bazy danych SQL jest używany podczas odwoływania się do bazy danych SQL i platformy Azure Synapse.

Poniższe kroki pokazują, jak skonfigurować bazę danych SQL, aby wymusić zasady dostępu warunkowego.  

## <a name="prerequisites"></a>Wymagania wstępne  
- W usłudze Azure Synapse należy skonfigurować bazę danych SQL lub pulę SQL w celu obsługi uwierzytelniania usługi Azure Active Directory. Aby uzyskać określone kroki, zobacz [Konfigurowanie uwierzytelniania usługi Azure Active Directory i zarządzanie nim za pomocą bazy danych SQL lub usługi Azure Synapse](sql-database-aad-authentication-configure.md).  
- Gdy uwierzytelnianie wieloskładnikowe jest włączone, należy połączyć się z obsługiwanym narzędziem, takim jak najnowszy program SSMS. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania wieloskładnikowego usługi Azure SQL Database dla programu SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurowanie urzędu certyfikacji dla bazy danych/dw usługi Azure SQL  
1. Zaloguj się do portalu, wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję Dostęp **warunkowy**. Aby uzyskać więcej informacji, zobacz [Odwołanie techniczne dostępu warunkowego usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Ostrze dostępu warunkowego](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. W bloku **Zasady dostępu warunkowego** kliknij pozycję **Nowe zasady**, podaj nazwę, a następnie kliknij pozycję **Konfiguruj reguły**.  
3. W obszarze **Przydziały**wybierz **pozycję Użytkownicy i grupy**, zaznacz pozycję Wybierz użytkowników i **grupy**, a następnie wybierz użytkownika lub grupę dla dostępu warunkowego. Kliknij **pozycję Wybierz**, a następnie kliknij pozycję **Gotowe,** aby zaakceptować wybór.  
   ![wybieranie użytkowników i grup](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Wybierz **pozycję Aplikacje w chmurze**, kliknij pozycję **Wybierz aplikacje**. Zobaczysz wszystkie aplikacje dostępne dla dostępu warunkowego. Wybierz **pozycję Azure SQL Database**, u dołu kliknij pozycję **Wybierz**, a następnie kliknij przycisk **Gotowe**.  
   ![wybieranie bazy danych SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Jeśli nie możesz znaleźć **usługi Azure SQL Database** wymienionej na poniższym trzecim zrzucie ekranu, wykonaj następujące kroki:   
   - Zaloguj się do wystąpienia bazy danych/dw usługi Azure SQL przy użyciu usługi SSMS przy użyciu konta administratora usługi AAD.  
   - Wykonaj `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`plik .  
   - Zaloguj się do usługi AAD i sprawdź, czy usługa Azure SQL Database i Azure Synapse są wymienione w aplikacjach w usłudze AAD.  

5. Wybierz **pozycję Formanty dostępu**, wybierz pozycję **Przyznaj**, a następnie sprawdź zasady, które chcesz zastosować. W tym przykładzie wybierzmy **opcję Wymagaj uwierzytelniania wieloskładnikowego**.  
   ![wybierz dostęp do dotacji](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Podsumowanie  
Wybrana aplikacja (Azure SQL Database) umożliwiająca łączenie się z usługą Azure SQL DB/DW przy użyciu usługi Azure AD Premium, teraz wymusza wybrane zasady dostępu warunkowego, **wymagane uwierzytelnianie wieloskładnikowe.**  
W przypadku pytań dotyczących usługi Azure SQL Database i MFAforSQLDB@microsoft.comusługi Azure Synapse dotyczących uwierzytelniania wieloskładnikowego należy skontaktować się z programem .  

## <a name="next-steps"></a>Następne kroki  

Aby zapoznać się z samouczkiem, zobacz [Zabezpieczanie bazy danych SQL platformy Azure](sql-database-security-tutorial.md).
