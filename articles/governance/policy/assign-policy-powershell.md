---
title: Tworzenie zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu programu Azure PowerShell
description: Używając programu Azure PowerShell, utwórz przypisanie usługi Azure Policy, aby zidentyfikować niezgodne zasoby.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: b5f4306fc1627e679f8f59a92bae4124a48cbd42
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856472"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Tworzenie przypisania usługi Azure Policy w celu identyfikacji niezgodnych zasobów przy użyciu usługi Azure PowerShell

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów. W tym przewodniku Szybki start utworzysz przypisanie zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych. Po zakończeniu zidentyfikujesz maszyny wirtualne, które są *niezgodne* z przypisaniem zasad.

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza polecenia lub skryptów. W tym przewodniku objaśniono tworzenie przypisania zasad za pomocą modułu Az. Zasady identyfikują niezgodne zasoby w środowisku platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli ta czynność nie została jeszcze wykonana, zainstaluj klienta [ARMClient](https://github.com/projectkudu/ARMClient). Jest to narzędzie, które wysyła żądania HTTP do interfejsów API opartych na usłudze Azure Resource Manager.
- Przed rozpoczęciem upewnij się, że masz zainstalowaną najnowszą wersję programu Azure PowerShell. Zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps), aby uzyskać szczegółowe informacje.
- Zarejestruj dostawcę zasobów usługi Policy Insights przy użyciu programu Azure PowerShell. Dzięki zarejestrowaniu dostawcy zasobów masz pewność, że subskrypcja będzie z nim współpracować. Aby zarejestrować dostawcę zasobów, należy mieć uprawnienia do wykonywania operacji rejestrowania dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów:

  ```azurepowershell-interactive
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Aby uzyskać więcej informacji na temat rejestrowania i przeglądania dostawców zasobów, zobacz [Dostawcy zasobów i ich typy](../../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku Szybki start utworzysz przypisanie zasad i przypiszesz definicję *Audit VMs without managed disks* (Przeprowadź inspekcję maszyn wirtualnych bez dysków zarządzanych). Ta definicja zasad identyfikuje zasoby, które nie spełniają warunków określonych w definicji zasad.

Uruchom następujące polecenia, aby utworzyć nowe przypisanie zasad:

```azurepowershell-interactive
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Poprzednie polecenia korzystają z następujących informacji:

- **Nazwa** — rzeczywista nazwa przypisania.  W tym przykładzie użyto nazwy *audit-vm-manageddisks*.
- **DisplayName** — nazwa wyświetlana przypisania zasad. W tym przypadku użyj nazwy przypisania *Audit VMs without managed disks Assignment* (Przeprowadź inspekcję maszyn wirtualnych bez dysków zarządzanych).
- **Definicja** — identyfikator definicji zasad, na podstawie której tworzysz przypisanie. W tym przypadku jest to identyfikator definicji zasad *Audit VMs that do not use managed disks* (Przeprowadź inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych).
- **Zakres** — zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Może obejmować zarówno subskrypcje, jak i grupy zasobów. Pamiętaj, aby zastąpić fragment &lt;zakres&gt; nazwą grupy zasobów.

Teraz możesz zidentyfikować niezgodne zasoby, aby poznać stan zgodności Twojego środowiska.

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Skorzystaj z poniższych informacji w celu zidentyfikowania zasobów niezgodnych z utworzonym przypisaniem zasad. Uruchom następujące polecenia:

```azurepowershell-interactive
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Aby uzyskać więcej informacji na temat identyfikatorów przypisań zasad, zobacz [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment).

Następnie uruchom poniższe polecenie, aby uzyskać identyfikatory niezgodnych zasobów, które są danymi wyjściowymi przekazywanymi do pliku JSON:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Wyniki powinny wyglądać podobnie do następujących:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Wyniki są porównywalne z zawartością zwykle wyświetlaną w obszarze **Niezgodne zasoby** w widoku witryny Azure Portal.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące polecenie:

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start została przypisana definicja zasad mających na celu zidentyfikowanie niezgodnych zasobów w środowisku platformy Azure.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)