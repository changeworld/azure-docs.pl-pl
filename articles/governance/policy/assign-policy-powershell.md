---
title: Utwórz zasady dla niezgodnych zasobów przy użyciu Azure PowerShell
description: Użyj Azure PowerShell, aby utworzyć przypisanie Azure Policy w celu zidentyfikowania niezgodnych zasobów.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: azure-policy
ms.openlocfilehash: 7e86436b1b87ab4eec3a971ec19e7dc0ffb106b3
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978122"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu Azure PowerShell

Pierwszym krokiem w zrozumieniu zgodności na platformie Azure jest zidentyfikowanie stanu zasobów. W tym przewodniku szybki start utworzysz przypisanie zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych. Po zakończeniu zidentyfikujesz maszyny wirtualne, które nie są *zgodne*.

Moduł Azure PowerShell służy do zarządzania zasobami platformy Azure z poziomu wiersza polecenia lub skryptów.
W tym przewodniku wyjaśniono, jak używać AZ module do tworzenia przypisania zasad.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed rozpoczęciem upewnij się, że zainstalowano najnowszą wersję Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz temat [Install Azure PowerShell module](/powershell/azure/install-az-ps) .
- Zarejestruj dostawcę zasobów usługi Azure Policy Insights przy użyciu Azure PowerShell. Rejestracja dostawcy zasobów zapewnia, że Twoja subskrypcja współpracuje z usługą. Aby zarejestrować dostawcę zasobów, musisz mieć uprawnienie do rejestrowania dostawcy zasobów. Ta operacja jest uwzględniona w rolach współautor i Owner. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Aby uzyskać więcej informacji na temat rejestrowania i przeglądania dostawców zasobów, zobacz [dostawcy zasobów i ich typy](../../azure-resource-manager/resource-manager-supported-services.md) .

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku szybki start utworzysz przypisanie zasad dla *maszyn wirtualnych inspekcji bez definicji dysków zarządzanych* . Ta definicja zasad identyfikuje maszyny wirtualne, które nie korzystają z dysków zarządzanych.

Uruchom następujące polecenia, aby utworzyć nowe przypisanie zasad:

```azurepowershell-interactive
# Get a reference to the resource group that will be the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition that will be assigned
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Powyższe polecenia wykorzystują następujące informacje:

- **Nazwa** — rzeczywista nazwa przypisania. W tym przykładzie użyto *inspekcji-VM-manageddisks* .
- **DisplayName** — nazwa wyświetlana przypisania zasad. W takim przypadku używasz *maszyn wirtualnych inspekcji bez przypisania dysków zarządzanych*.
- **Definicja** — definicja zasad, na podstawie której tworzysz przypisanie. W takim przypadku jest IDENTYFIKATORem *maszyn wirtualnych inspekcji definicji zasad, które nie korzystają z dysków zarządzanych*.
- **Zakres** — zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Może to być zakres od subskrypcji do grup zasobów. Pamiętaj, aby zastąpić &lt;scope @ no__t-1 nazwą grupy zasobów.

Teraz możesz zidentyfikować niezgodne zasoby, aby zrozumieć stan zgodności środowiska.

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Poniższe informacje służą do identyfikowania zasobów, które nie są zgodne z utworzonym przypisaniem zasad. Uruchom następujące polecenia:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Aby uzyskać więcej informacji na temat uzyskiwania stanu zasad, zobacz [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Wyniki przypominają następujący przykład:

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

Wyniki są zgodne z informacjami wyświetlanymi na karcie **zgodność zasobów** w przypisaniu zasad w widoku Azure Portal.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzone przypisanie, użyj następującego polecenia:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przypisano definicję zasad w celu zidentyfikowania niezgodnych zasobów w środowisku platformy Azure.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia, czy nowe zasoby są zgodne, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)