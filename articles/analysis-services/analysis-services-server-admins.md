---
title: Zarządzaj administratorami serwera w usługach Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać Administratorzy serwera dla serwera usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 00a4de5f463133054ff4821cce74b3dc859a07c2
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603570"
---
# <a name="manage-server-administrators"></a>Zarządzanie administratorami serwerów

Administratorzy serwera musi być prawidłową użytkownikowi lub grupie zabezpieczeń w usłudze Azure Active Directory (Azure AD) dla dzierżawy, w której znajduje się serwer. Możesz użyć **Administratorzy usług Analysis Services** serwera w witrynie Azure portal, właściwości serwera w programie SSMS, programu PowerShell lub interfejsu API REST Zarządzanie administratorami serwerów. 

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

## <a name="powershell"></a>PowerShell

Użyj [New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) polecenia cmdlet, aby określić parametr administratora podczas tworzenia nowego serwera. <br>
Użyj [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver) polecenia cmdlet, aby zmodyfikować parametru administratora dla istniejącego serwera.

## <a name="rest-api"></a>Interfejs API REST

Użyj [Utwórz](https://docs.microsoft.com/rest/api/analysisservices/servers/create) określać właściwości asAdministrator podczas tworzenia nowego serwera. <br>
Użyj [aktualizacji](https://docs.microsoft.com/rest/api/analysisservices/servers/update) określać właściwości asAdministrator podczas modyfikowania istniejącego serwera. <br>



## <a name="next-steps"></a>Kolejne kroki 

[Uwierzytelnianie i uprawnienia użytkownika](analysis-services-manage-users.md)  
[Zarządzanie rolami bazy danych i użytkowników](analysis-services-database-users.md)  
[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)  

