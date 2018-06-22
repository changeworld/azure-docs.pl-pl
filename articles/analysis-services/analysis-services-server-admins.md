---
title: Zarządzanie Administratorzy serwera w usłudze Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać Administratorzy serwera dla serwera usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ec1630f4de70f77c13e335c68aff16180e524c12
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307812"
---
# <a name="manage-server-administrators"></a>Administratorzy serwerów zarządzania
Administratorzy serwera musi być prawidłową użytkownika lub grupy zabezpieczeń w usłudze Azure Active Directory (Azure AD) dla dzierżawcy, w której znajduje się serwer. Można użyć **Administratorzy usług analizy** serwera w portalu Azure lub właściwości serwera w programie SSMS administratorom serwerów zarządzania. 

> [!NOTE]
> Grupy zabezpieczeń musi mieć `MailEnabled` ustawioną właściwość `True`.

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

