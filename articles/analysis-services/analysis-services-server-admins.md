---
title: Zarządzaj administratorami serwera w usługach Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać Administratorzy serwera dla serwera usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9d8f74bd66fc7c980c4fc5f83492aad7d8a4aa5c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866971"
---
# <a name="manage-server-administrators"></a>Zarządzanie administratorami serwerów
Administratorzy serwera musi być prawidłową użytkownikowi lub grupie zabezpieczeń w usłudze Azure Active Directory (Azure AD) dla dzierżawy, w której znajduje się serwer. Możesz użyć **Administratorzy usług Analysis Services** serwera w witrynie Azure portal lub właściwości serwera w programie SSMS, aby zarządzanie administratorami serwerów. 

> [!NOTE]
> Grupy zabezpieczeń musi mieć `MailEnabled` właściwością `True`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Aby dodać administratorom serwerów przy użyciu witryny Azure portal
1. W portalu usługi dla serwera, kliknij **Administratorzy usług Analysis Services**.
2. W  **\<servername >-Administratorzy usług Analysis Services**, kliknij przycisk **Dodaj**.
3. W **dodać administratorów serwera**wybierz kont użytkowników z usługi Azure AD lub zaprosić użytkowników zewnętrznych za pomocą adresu e-mail.

    ![Administratorzy serwera w witrynie Azure portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Aby dodać administratorom serwerów przy użyciu programu SSMS
1. Kliknij prawym przyciskiem myszy serwer > **właściwości**.
2. W **właściwości serwera analizy**, kliknij przycisk **zabezpieczeń**.
3. Kliknij przycisk **Dodaj**, a następnie wprowadź adres e-mail użytkownika lub grupy w usłudze Azure AD.
   
    ![Dodaj administratorów serwera w programie SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Kolejne kroki 
[Uwierzytelnianie i uprawnienia użytkownika](analysis-services-manage-users.md)  
[Zarządzanie rolami bazy danych i użytkowników](analysis-services-database-users.md)  
[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)  

