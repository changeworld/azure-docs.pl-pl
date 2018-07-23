---
title: Jak utworzyć i usunąć użytkownika przypisane tożsamości usługi zarządzanej przy użyciu usługi Azure Resource Manager
description: Krok po kroku instrukcje dotyczące sposobu tworzenia i usuwania użytkowników przypisane tożsamości usługi zarządzanej przy użyciu zasobów platformy Azure.
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
ms.openlocfilehash: 42d5b55e0bddf2d027810bfdf146de9bfee8a0fb
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188135"
---
# <a name="create-list-and-delete-a-user-assigned-identity-using-azure-resource-manager"></a>Tworzenie, wyświetlanie i usuwanie tożsamości przypisanych przez użytkownika, za pomocą usługi Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure za pomocą tożsamości zarządzanej w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule utworzysz użytkownik, któremu przypisano tożsamość zarządzana przy użyciu usługi Azure Resource Manager.

Nie jest możliwe do wyświetlania i usuwania użytkownik, któremu przypisano tożsamość przy użyciu szablonu usługi Azure Resource Manager.  Zobacz następujące artykuły, aby tworzyć i wyświetlać listę tożsamości przypisanych przez użytkownika:

- [Lista tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-cli.md#list-user-assigned-identities)
- [Usuwanie tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-identity)
## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje, w tym artykule, Twoje konto musi następujące przypisania roli:
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli, aby utworzyć, odczytać (lista), aktualizowanie i usuwanie tożsamości przypisanych przez użytkownika.

## <a name="template-creation-and-editing"></a>Tworzenie szablonu i edytowanie

Zgodnie z platformą Azure portal i skryptów, szablony usługi Azure Resource Manager umożliwiają wdrażać nowe lub zmodyfikowane zasoby zdefiniowane przez grupę zasobów platformy Azure. Jest dostępnych kilka opcji edycję szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

- Za pomocą [niestandardowego szablonu w portalu Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na Utwórz szablon od podstaw albo oparte na typowych istniejących lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
- Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z poziomu [oryginalnego wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), lub z [bieżący stan wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Za pomocą lokalnego [edytora JSON (np. programu VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
- Za pomocą programu Visual Studio [projekt grupy zasobów platformy Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Aby utworzyć i wdrożyć szablon. 

## <a name="create-a-user-assigned-identity"></a>Tworzenie tożsamości przypisanej przez użytkownika 

Aby utworzyć tożsamości przypisanych przez użytkownika, szablon. Zastąp `<USER ASSIGNED IDENTITY NAME>` wartości wybranymi samodzielnie wartościami:

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
## <a name="related-content"></a>Powiązana zawartość

Aby uzyskać informacje dotyczące sposobu przypisywania tożsamości przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure przy użyciu usługi Azure Resource Manager szablon, zobacz temat [Konfigurowanie tożsamości usługi zarządzanej maszyny Wirtualnej przy użyciu szablonu](qs-configure-template-windows-vm.md).


 
