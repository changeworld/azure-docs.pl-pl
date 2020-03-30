---
title: Dodawanie jednostki usługi do roli administratora usług Azure Analysis Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać jednostkę usługi automatyzacji do roli administratora serwera usług Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 1370f65405963ebf825e986e6801607a0d96156e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298092"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Dodawanie jednostki usługi do roli administratora serwera 

 Aby zautomatyzować zadania programu PowerShell nienadzorowane, podmiot usługi musi mieć uprawnienia **administratora serwera** na zarządzanym serwerze usług Analysis Services. W tym artykule opisano sposób dodawania jednostki usługi do roli administratorów serwera na serwerze usługi Azure AS. Można to zrobić za pomocą programu SQL Server Management Studio lub szablonu Menedżera zasobów.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed wykonaniem tego zadania musisz mieć jednostkę usługi zarejestrowaną w usłudze Azure Active Directory.

[Tworzenie jednostki usługi — witryna Azure portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Tworzenie jednostki usługi — PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Używanie programu SQL Server Management Studio

Administratorzy serwerów można skonfigurować przy użyciu programu SQL Server Management Studio (SSMS). Aby wykonać to zadanie, musisz mieć uprawnienia [administratora serwera](analysis-services-server-admins.md) na serwerze usługi Azure AS. 

1. W systemie SSMS połącz się z serwerem usługi Azure AS.
2. W oknie**Zabezpieczenia** **właściwości** > serwera kliknij pozycję **Dodaj**.
3. W **obszarze Wybierz użytkownika lub grupę**wyszukaj zarejestrowaną aplikację według nazwy, wybierz pozycję, a następnie kliknij pozycję **Dodaj**.

    ![Wyszukiwanie konta głównego dostawcy usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Sprawdź identyfikator konta jednostki usługi, a następnie kliknij przycisk **OK**.
    
    ![Wyszukiwanie konta głównego dostawcy usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Używanie szablonu Menedżera zasobów

Administratorzy serwera można również skonfigurować, wdrażając serwer usług Analysis Services przy użyciu szablonu usługi Azure Resource Manager. Tożsamość uruchomionego wdrożenia musi należeć do roli **współautora** zasobu w [kontroli dostępu opartej na rolach platformy Azure (RBAC).](../role-based-access-control/overview.md)

> [!IMPORTANT]
> Podmiot usługi należy dodać przy `app:{service-principal-client-id}@{azure-ad-tenant-id}`użyciu formatu .

Następujący szablon Menedżera zasobów wdraża serwer usług Analysis Services z określoną jednostką usługi dodaną do roli Administrator usług Analysis Services:

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

## <a name="using-managed-identities"></a>Korzystanie z tożsamości zarządzanych

Tożsamość zarządzaną można również dodać do listy Administratorzy usług Analysis Services. Na przykład może mieć [aplikację logiki z tożsamością zarządzaną przypisaną do systemu](../logic-apps/create-managed-service-identity.md)i chcesz udzielić jej możliwości administrowania serwerem usług Analysis Services.

W większości części witryny Azure portal i interfejsów API tożsamości zarządzane są identyfikowane przy użyciu ich identyfikator obiektu jednostki usługi. Jednak Analysis Services wymaga, aby były identyfikowane przy użyciu ich identyfikator klienta. Aby uzyskać identyfikator klienta dla jednostki usługi, można użyć interfejsu wiersza polecenia platformy Azure:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Alternatywnie można użyć programu PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Następnie można użyć tego identyfikatora klienta w połączeniu z identyfikatorem dzierżawy, aby dodać tożsamość zarządzana do listy Administratorzy usług Analysis Services, jak opisano powyżej.

## <a name="related-information"></a>Informacje pokrewne

* [Pobieranie modułu programu SQL Server PowerShell](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Pobieranie SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


