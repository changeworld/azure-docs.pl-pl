---
title: Jak tworzyć i usuwać przypisane przez użytkownika tożsamości zarządzanej przy użyciu usługi Azure Resource Manager
description: Krok po kroku instrukcje dotyczące sposobu tworzenia i usuwania przypisanych do użytkowników zarządzanych tożsamości za pomocą usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: adfa7ebfd911bfcbc88e01030777e91c48841784
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338349"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Tworzenie, wyświetlanie i usuwanie przypisanych przez użytkownika tożsamości zarządzanej przy użyciu usługi Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure za pomocą tożsamości zarządzanej w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule utworzysz przypisanych przez użytkownika tożsamości zarządzanej przy użyciu usługi Azure Resource Manager.

Nie jest możliwe do wyświetlania i usuwania przypisanych przez użytkownika tożsamości zarządzanej przy użyciu szablonu usługi Azure Resource Manager.  Zobacz następujące artykuły, aby utworzyć i tożsamości zarządzanej listy przypisanych przez użytkownika:

- [Lista przypisanych przez użytkownika tożsamości zarządzanej](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Usuń przypisanych przez użytkownika z tożsamości zarządzanej](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje, w tym artykule, Twoje konto musi następujące przypisania roli:
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli, aby utworzyć, odczytać (lista), aktualizowanie i usuwanie tożsamości zarządzanej przypisanych przez użytkownika.

## <a name="template-creation-and-editing"></a>Tworzenie szablonu i edytowanie

Zgodnie z platformą Azure portal i skryptów, szablony usługi Azure Resource Manager umożliwiają wdrażać nowe lub zmodyfikowane zasoby zdefiniowane przez grupę zasobów platformy Azure. Jest dostępnych kilka opcji edycję szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

- Za pomocą [niestandardowego szablonu w portalu Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na Utwórz szablon od podstaw albo oparte na typowych istniejących lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
- Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z poziomu [oryginalnego wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), lub z [bieżący stan wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Za pomocą lokalnego [edytora JSON (np. programu VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
- Za pomocą programu Visual Studio [projekt grupy zasobów platformy Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Aby utworzyć i wdrożyć szablon. 

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie zarządzanych tożsamości przypisanych przez użytkownika 

Aby utworzyć przypisanych przez użytkownika tożsamości zarządzanej, szablon. Zastąp `<USER ASSIGNED IDENTITY NAME>` wartości wybranymi samodzielnie wartościami:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2015-08-31-PREVIEW",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące sposobu przypisywania użytkownik przypisany zarządzanych tożsamości na Maszynie wirtualnej platformy Azure przy użyciu usługi Azure Resource Manager szablon, zobacz temat [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu szablonów](qs-configure-template-windows-vm.md).


 
