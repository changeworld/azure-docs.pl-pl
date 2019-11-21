---
title: 'Quickstart: New policy assignment with templates'
description: In this quickstart, you use a Resource Manager template to create a policy assignment to identify non-compliant resources.
ms.date: 03/13/2019
ms.topic: quickstart
ms.openlocfilehash: d85d02408b05ccaef608a2bb951a48e5a8526da6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74216796"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Quickstart: Create a policy assignment to identify non-compliant resources by using a Resource Manager template

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów.
Ten przewodnik Szybki start przeprowadzi Cię przez proces tworzenia przypisania zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny wirtualne, które nie korzystają z dysków zarządzanych. Są one *niezgodne* z przypisaniem zasad.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

In this quickstart, you create a policy assignment and assign a built-in policy definition called *Audit VMs that do not use managed disks*. For a partial list of available built-in policies, see [Azure Policy samples](./samples/index.md).

There are several methods for creating policy assignments. In this quickstart, you use a [quickstart template](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Here is a copy of the template:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Azure Policy service is free.  For more information, see [Overview of Azure Policy](./overview.md).

1. Select the following image to sign in to the Azure portal and open the template:

   [![Deploy the Policy template to Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Select or enter the following values:

   | Nazwa | Wartość |
   |------|-------|
   | Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
   | Grupa zasobów | Select **Create new**, specify a name, and then select **OK**. In the screenshot, the resource group name is *mypolicyquickstart\<Date in MMDD>rg*. |
   | Lokalizacja | Wybierz region. Na przykład **Środkowe stany USA**. |
   | Policy Assignment Name | Specify a policy assignment name. You can use the policy definition display if you want. For example, **Audit VMs that do not use managed disks**. |
   | Rg Name | Specify a resource group name where you want to assign the policy to. In this quickstart, use the default value **[resourceGroup().name]** . **[resourceGroup()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)** is a template function that retrieves the resource group. |
   | Policy Definition ID | Specify **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | I agree to the terms and conditions stated above | (Select) |

1. Wybierz pozycję **Kup**.

Some additional resources:

- To find more samples templates, see [Azure Quickstart template](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- To see the template reference, go to [Azure template reference](/azure/templates/microsoft.authorization/allversions).
- To learn how to develop Resource Manager templates, see [Azure Resource Manager documentation](/azure/azure-resource-manager/).
- To learn subscription-level deployment, see [Create resource groups and resources at the subscription level](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Wybierz pozycję **Zgodność** w lewej części strony. Znajdź utworzone przypisanie zasad **Audit VMs that do not use managed disks** (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

![Policy compliance overview page](./media/assign-policy-template/policy-compliance.png)

Jeśli istnieją jakiekolwiek zasoby niezgodne z nowym przypisaniem, zostaną one wyświetlone w obszarze **Niezgodne zasoby**.

For more information, see [How compliance works](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące kroki:

1. Wybierz pozycję **Zgodność** (lub **Przypisania**) w lewej części strony usługi Azure Policy i znajdź utworzone przypisanie zasad **Audit VMs that do not use managed disks** (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

1. Right-click the **Audit VMs that do not use managed disks** policy assignment and select **Delete assignment**.

   ![Delete an assignment from the compliance overview page](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Następne kroki

In this quickstart, you assigned a built-in policy definition to a scope and evaluated its compliance report. Definicja zasady zawiera sprawdzenie, czy wszystkie zasoby w ramach zakresu są zgodne, oraz określenie niezgodnych zasobów.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)