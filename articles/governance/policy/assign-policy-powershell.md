---
title: Tworzenie zasad dla niezgodnych zasobów przy użyciu programu Azure PowerShell
description: Używając programu Azure PowerShell, utwórz przypisanie usługi Azure Policy, aby zidentyfikować niezgodne zasoby.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: f07a760153c4ec15cec5c5bc1348e23152c59cb8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258338"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Tworzenie przypisania usługi Azure Policy w celu identyfikacji niezgodnych zasobów przy użyciu usługi Azure PowerShell

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów. W tym przewodniku Szybki start utworzysz przypisanie zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych. Po zakończeniu zidentyfikujesz maszyny wirtualne, które są *niezgodnych*.

Moduł programu Azure PowerShell jest używany do zarządzania zasobami platformy Azure z poziomu wiersza polecenia lub skryptów.
Ten przewodnik wyjaśnia, jak użyć modułu Az Tworzenie przypisania zasad.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Przed rozpoczęciem upewnij się, że masz zainstalowaną najnowszą wersję programu Azure PowerShell. Zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps), aby uzyskać szczegółowe informacje.
- Zarejestruj dostawcę zasobów usługi Policy Insights przy użyciu programu Azure PowerShell. Dzięki zarejestrowaniu dostawcy zasobów masz pewność, że subskrypcja będzie z nim współpracować. Aby zarejestrować dostawcę zasobów, należy mieć uprawnienia do wykonywania operacji rejestrowania dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Aby uzyskać więcej informacji na temat rejestrowania i przeglądania dostawców zasobów, zobacz [Dostawcy zasobów i ich typy](../../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku Szybki Start utworzysz przypisanie zasad dla *maszyn wirtualnych bez dysków zarządzanych z inspekcji* definicji. Ta definicja zasad identyfikuje maszyny wirtualne, które nie korzystają z dysków zarządzanych.

Uruchom następujące polecenia, aby utworzyć nowe przypisanie zasad:

```azurepowershell-interactive
# Get a reference to the resource group that will be the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition that will be assigned
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Poprzednie polecenia korzystają z następujących informacji:

- **Nazwa** — rzeczywista nazwa przypisania. W tym przykładzie użyto nazwy *audit-vm-manageddisks*.
- **DisplayName** — nazwa wyświetlana przypisania zasad. W tym przypadku użyj nazwy przypisania *Audit VMs without managed disks Assignment* (Przeprowadź inspekcję maszyn wirtualnych bez dysków zarządzanych).
- **Definicja** — identyfikator definicji zasad, na podstawie której tworzysz przypisanie. W tym przypadku jest to identyfikator definicji zasad *Audit VMs that do not use managed disks* (Przeprowadź inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych).
- **Zakres** — zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Może obejmować zarówno subskrypcje, jak i grupy zasobów. Pamiętaj, aby zastąpić fragment &lt;zakres&gt; nazwą grupy zasobów.

Teraz możesz zidentyfikować niezgodne zasoby, aby poznać stan zgodności Twojego środowiska.

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Skorzystaj z poniższych informacji w celu zidentyfikowania zasobów niezgodnych z utworzonym przypisaniem zasad. Uruchom następujące polecenia:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Aby uzyskać więcej informacji na temat pobierania stanu zasad, zobacz [Get AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Wyniki powinny wyglądać podobnie do następujących:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

Wyniki zgodne, zostanie wyświetlony w **zgodność zasobów** karcie przypisania zasad w widoku witryny Azure portal.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące polecenie:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start została przypisana definicja zasad mających na celu zidentyfikowanie niezgodnych zasobów w środowisku platformy Azure.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)