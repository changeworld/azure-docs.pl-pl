---
title: Dodaj nazwę główną usługi do roli administratora Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak dodać nazwę główną usługi Automation do roli administratora serwera Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b75740e9bff714ad68c93bea7e387e60da2f1c59
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212495"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Dodawanie jednostki usługi do roli administratora serwera 

 Aby zautomatyzować nienadzorowane zadania programu PowerShell, jednostka usługi musi mieć uprawnienia **administratora serwera** na zarządzanym serwerze Analysis Services. W tym artykule opisano sposób dodawania nazwy głównej usługi do roli Administratorzy serwera na platformie Azure jako serwer. Można to zrobić przy użyciu SQL Server Management Studio lub szablonu Menedżer zasobów.
 
> [!NOTE]
> W przypadku operacji serwera przy użyciu poleceń cmdlet Azure PowerShell, jednostka usługi musi również należeć do roli **właściciela** zasobu w [Access Control opartej na rolach (RBAC) na platformie Azure](../role-based-access-control/overview.md). 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed ukończeniem tego zadania musisz mieć nazwę główną usługi zarejestrowaną w Azure Active Directory.

[Tworzenie jednostki usługi —  Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)  
[Tworzenie jednostki usługi — PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Używanie programu SQL Server Management Studio

Administratorów serwera można skonfigurować przy użyciu SQL Server Management Studio (SSMS). Aby wykonać to zadanie, musisz mieć uprawnienia [administratora serwera](analysis-services-server-admins.md) na platformie Azure jako serwer. 

1. W programie SSMS Połącz się z platformą Azure jako serwerem.
2. W obszarze **Właściwości serwera** > **zabezpieczenia**kliknij przycisk **Dodaj**.
3. W obszarze **Wybierz użytkownika lub grupę**Wyszukaj zarejestrowaną aplikację według nazwy, wybierz pozycję, a następnie kliknij przycisk **Dodaj**.

    ![Wyszukaj konto jednostki usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Sprawdź identyfikator konta nazwy głównej usługi, a następnie kliknij przycisk **OK**.
    
    ![Wyszukaj konto jednostki usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Korzystanie z szablonu Menedżer zasobów

Administratorzy serwera można również skonfigurować, wdrażając serwer Analysis Services przy użyciu szablonu Azure Resource Manager. Tożsamość, w której działa wdrożenie, musi należeć do roli **współautor** dla zasobu w [Access Control opartej na rolach (RBAC) na platformie Azure](../role-based-access-control/overview.md).

> [!IMPORTANT]
> Nazwę główną usługi należy dodać przy użyciu `app:{service-principal-client-id}@{azure-ad-tenant-id}`format.

Poniższy szablon Menedżer zasobów wdraża serwer Analysis Services z określoną jednostką usługi dodaną do roli administratora Analysis Services:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="related-information"></a>Informacje pokrewne

* [Pobierz moduł SQL Server PowerShell](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Pobierz narzędzie SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


