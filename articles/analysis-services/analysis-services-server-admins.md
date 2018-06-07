---
title: Zarządzanie Administratorzy serwera w usłudze Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać Administratorzy serwera dla serwera usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 59aaf07edf387d40f47d7d82dcb116802a003dc1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596172"
---
# <a name="manage-server-administrators"></a>Administratorzy serwerów zarządzania
Administratorzy serwera musi być prawidłowa nazwa użytkownika lub grupy w usłudze Azure Active Directory (Azure AD) dla dzierżawcy, w której znajduje się serwer. Można użyć **Administratorzy usług analizy** serwera w portalu Azure lub właściwości serwera w programie SSMS administratorom serwerów zarządzania. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Aby dodać administratorom serwerów przy użyciu portalu Azure
1. W portalu dla serwera, kliknij przycisk **Administratorzy usług analizy**.
2. W  **\<nazwa_serwera > — Administratorzy usług analizy**, kliknij przycisk **Dodaj**.
3. W **dodać administratorom serwerów**wybierz kont użytkowników z usługi Azure AD lub zaprosić użytkowników zewnętrznych za pomocą adresu e-mail.

    ![Administratorzy serwera w portalu Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Aby dodać administratorom serwerów przy użyciu narzędzia SSMS
1. Kliknij prawym przyciskiem myszy serwer > **właściwości**.
2. W **właściwości serwera analiz**, kliknij przycisk **zabezpieczeń**.
3. Kliknij przycisk **Dodaj**, a następnie wprowadź adres e-mail dla użytkownika lub grupy w usługi Azure AD.
   
    ![Dodawanie administratorów serwera w programie SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Kolejne kroki 
[Uwierzytelnianie i uprawnienia użytkownika](analysis-services-manage-users.md)  
[Zarządzanie ról bazy danych i użytkowników](analysis-services-database-users.md)  
[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)  

