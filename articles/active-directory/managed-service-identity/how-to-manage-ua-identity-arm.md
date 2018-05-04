---
title: Tworzenie i usuwanie użytkownika przypisać tożsamość usługi zarządzane przy użyciu usługi Azure Resource Manager
description: Krok po kroku instrukcje dotyczące sposobu tworzenia i usuwania użytkowników przypisać tożsamość usługi zarządzane przy użyciu zasobów Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 96fd86a2af3372f53973c95ee11ab35ccee1e63e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="create-list-and-delete-a-user-assigned-identity-using-azure-resource-manager"></a>Utwórz listę i usunąć tożsamości przypisane przez użytkownika, za pomocą usługi Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tożsamość usługi zarządzanej udostępnia usługi Azure za pomocą tożsamości zarządzanych w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule należy utworzyć użytkownika przypisane tożsamości zarządzanych za pomocą usługi Azure Resource Manager.

Nie jest możliwe do wyświetlania i usuwania użytkownik, któremu przypisano tożsamość za pomocą szablonu usługi Azure Resource Manager.  Zobacz następujące artykuły, aby tworzyć i wyświetlać tożsamość użytkownika z przypisanym:

- [Przypisać tożsamości użytkownika z listy](how-to-manage-ua-identity-cli.md#list-user-assigned-identities)
- [Usuń tożsamość przypisane przez użytkownika](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-identity)
## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli znasz zarządzane tożsamość usługi, zapoznaj się [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między systemu przypisane i przypisać tożsamość użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

Logowanie do platformy Azure lokalnie lub za pośrednictwem portalu Azure, użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się również, że używane konto należy do roli, która daje uprawnienia do zapisu na maszynie Wirtualnej (na przykład roli "Współautor maszyny wirtualnej").

## <a name="template-creation-and-editing"></a>Tworzenie szablonu i edytowania

Zgodnie z platformy Azure portalu i skryptów, szablony usługi Azure Resource Manager zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowany przez grupę zasobów platformy Azure. Kilka opcje są dostępne do edycji szablonu i wdrażania, zarówno lokalnych, jak i oparte na portalu, w tym:

- Przy użyciu [niestandardowego szablonu z portalu Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), co pozwala na tworzyć szablon od podstaw albo oparte na istniejących typowe lub [szablon szybkiego startu](https://azure.microsoft.com/documentation/templates/).
- Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z dowolnej [oryginalnego wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), lub z [bieżący stan wdrożenia](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Przy użyciu lokalnego [edytora JSON (na przykład w kodzie VS)](../../azure-resource-manager/resource-manager-create-first-template.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
- Za pomocą programu Visual Studio [projektu grupy zasobów platformy Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do tworzenia i wdrażania szablonu. 

## <a name="create-a-user-assigned-identity"></a>Utwórz użytkownika przypisane tożsamości 

Aby utworzyć użytkownika przypisanego tożsamości, szablon. Zastąp `<USER ASSIGNED IDENTITY NAME>` wartości o własne wartości:

> [!IMPORTANT]
> Tworzenie użytkownika z przypisanym tożsamości z znaki specjalne (np. podkreślenie) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Sprawdzanie dostępności aktualizacji.  Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

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
      "name": "[parameters('<USER ASSIGNED IDENTITY NAME>')]",
      "apiVersion": "2015-08-31-PREVIEW",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('<USER ASSIGNED IDENTITY NAME>')]"
      }
  }
}
```
## <a name="related-content"></a>Zawartość pokrewna

Aby uzyskać informacje dotyczące sposobu przypisywania tożsamości użytkowników przypisanych do maszyny Wirtualnej platformy Azure przy użyciu usługi Azure Resource Manager szablonu, zobacz temat [konfigurowania tożsamości usługi maszyn wirtualnych zarządzanych za pomocą szablonu](qs-configure-template-windows-vm.md).


 
