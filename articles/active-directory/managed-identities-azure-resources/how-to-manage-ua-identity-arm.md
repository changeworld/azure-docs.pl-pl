---
title: Tworzenie & usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu usługi Azure Resource Manager
description: Instrukcje krok po kroku dotyczące tworzenia i usuwania tożsamości zarządzanych przypisanych przez użytkownika przy użyciu usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 244965da4e22c0808fd1ea9088aa182b27eaf484
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253379"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Tworzenie, wyświetlanie listy i usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu usługi Azure Resource Manager


Tożsamości zarządzane dla zasobów platformy Azure zapewnia usługom platformy Azure z tożsamością zarządzaną w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule utworzysz tożsamość zarządzaną przypisaną przez użytkownika przy użyciu usługi Azure Resource Manager.

Nie można wyświetlić listy i usunąć tożsamości zarządzanej przypisanej przez użytkownika przy użyciu szablonu usługi Azure Resource Manager.  Zobacz następujące artykuły, aby utworzyć i wyświetlić listę tożsamości zarządzanej przypisanej przez użytkownika:

- [Wyświetlanie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Usuwanie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="template-creation-and-editing"></a>Tworzenie i edytowanie szablonu

Podobnie jak w witrynie Azure portal i skrypty, szablony usługi Azure Resource Manager zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowanych przez grupę zasobów platformy Azure. Dostępnych jest kilka opcji edycji i wdrażania szablonów, zarówno lokalnych, jak i opartych na portalu, w tym:

- Korzystanie [z szablonu niestandardowego z portalu Azure Marketplace,](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)który umożliwia utworzenie szablonu od podstaw lub oparcie go na istniejącym szablonie wspólnym lub [Szybki start.](https://azure.microsoft.com/documentation/templates/)
- Wyprowadzając z istniejącej grupy zasobów, eksportując szablon z [oryginalnego wdrożenia](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)lub z [bieżącego stanu wdrożenia](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Za pomocą lokalnego [edytora JSON (na przykład VS Code),](../../azure-resource-manager/resource-manager-create-first-template.md)a następnie przekazywania i wdrażania przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
- Za pomocą [projektu](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) Visual Studio Azure Resource Group zarówno do tworzenia i wdrażania szablonu. 

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika 

Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika, twoje konto wymaga przypisania roli [Współautor tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika, użyj następującego szablonu. Zastąp `<USER ASSIGNED IDENTITY NAME>` wartość własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

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
      "apiVersion": "2018-11-30",
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
## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat przypisywania tożsamości zarządzanej przypisanej przez użytkownika do maszyny Wirtualnej platformy Azure przy użyciu szablonu Usługi Azure Resource Manager, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu szablonów.](qs-configure-template-windows-vm.md)


 
