---
title: Tworzenie przypisania zasad za pomocą szablonu usługi Resource Manager
description: W tym artykule przedstawiono kroki, aby użyć szablonu usługi Resource Manager do utworzenia przypisania zasad w celu zidentyfikowania niezgodnych zasobów.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 03d15d30deabbc2bd34fbb4fba108be382f94fa1
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65756523"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Szybki start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu szablonu usługi Resource Manager

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów.
Ten przewodnik Szybki start przeprowadzi Cię przez proces tworzenia przypisania zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny wirtualne, które nie korzystają z dysków zarządzanych. Są one *niezgodne* z przypisaniem zasad.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku Szybki Start utworzysz przypisanie zasad i przypisywanie definicji zasad wbudowanych o nazwie *inspekcji maszyn wirtualnych, które nie korzystają z dysków zarządzanych*. Aby przejrzeć częściową listę dostępnych zasad wbudowanych, zobacz [Przykłady zasad](./samples/index.md).

Istnieje kilka metod tworzenia przypisania zasad. W tym przewodniku Szybki Start użyjesz [szablon szybkiego startu](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Oto kopię szablonu:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Usługi zasad platformy Azure jest bezpłatne.  Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Policy](./overview.md).

1. Wybierz poniższy obraz, aby zalogować się do witryny Azure portal i otworzyć szablon:

   [![Wdrażanie szablonu zasad na platformie Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości:

   | Name (Nazwa) | Wartość |
   |------|-------|
   | Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
   | Grupa zasobów | Wybierz **Utwórz nową**, określ nazwę, a następnie wybierz **OK**. Na zrzucie ekranu Nazwa grupy zasobów jest *mypolicyquickstart\<datę w MMDD > rg*. |
   | Lokalizacja | Wybierz region. Na przykład **Środkowe stany USA**. |
   | Nazwa przypisania zasad | Określ nazwę przypisania zasad. Jeśli chcesz, możesz użyć wyświetlanie definicji zasad. Na przykład **inspekcji maszyn wirtualnych, które nie korzystają z dysków zarządzanych**. |
   | Nazwa RG | Określ nazwę grupy zasobów, której chcesz przypisać zasady. W tym przewodniku Szybki Start, użyj wartości domyślnej **[resourceGroup () .name]**. **[resourceGroup()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)**  jest funkcją szablonu, która pobiera grupy zasobów. |
   | Identyfikator definicji zasad | Określ **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Wyrażam zgodę na powyższe warunki i postanowienia | (Wybierz) |

1. Wybierz pozycję **Kup**.

Niektóre dodatkowe zasoby:

- Aby uzyskać więcej przykładów, szablonów, zobacz [szablonu szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Aby wyświetlić odwołania do szablonu, przejdź do [odwołanie do szablonu usługi Azure](/azure/templates/microsoft.authorization/allversions).
- Aby dowiedzieć się, jak opracowywać szablony usługi Resource Manager, zobacz [dokumentacji usługi Azure Resource Manager](/azure/azure-resource-manager/).
- Aby dowiedzieć się, wdrażania na poziomie subskrypcji, zobacz [tworzenia grupy zasobów i zasobów na poziomie subskrypcji](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Wybierz pozycję **Zgodność** w lewej części strony. Znajdź utworzone przypisanie zasad **Audit VMs that do not use managed disks** (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

![Strony Przegląd zgodności zasad](./media/assign-policy-template/policy-compliance.png)

Jeśli istnieją jakiekolwiek zasoby niezgodne z nowym przypisaniem, zostaną one wyświetlone w obszarze **Niezgodne zasoby**.

Aby uzyskać więcej informacji, zobacz [działania zgodności](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące kroki:

1. Wybierz pozycję **Zgodność** (lub **Przypisania**) w lewej części strony usługi Azure Policy i znajdź utworzone przypisanie zasad **Audit VMs that do not use managed disks** (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

1. Kliknij prawym przyciskiem myszy **inspekcji maszyn wirtualnych, które nie korzystają z dysków zarządzanych** przypisanie zasad i wybierz pozycję **Usuń przypisanie**.

   ![Usuwanie przypisania na stronie Przegląd zgodności](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start przypisano wbudowaną definicję zasad do zakresu i ocenić jej raport zgodności. Definicja zasady zawiera sprawdzenie, czy wszystkie zasoby w ramach zakresu są zgodne, oraz określenie niezgodnych zasobów.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)