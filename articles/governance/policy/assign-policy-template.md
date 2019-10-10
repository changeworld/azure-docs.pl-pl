---
title: Utwórz przypisanie przy użyciu szablonu Menedżer zasobów
description: W tym artykule omówiono procedurę tworzenia przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu szablonu Menedżer zasobów.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
ms.openlocfilehash: 842b29160e3abaea5edc4bfb4f2e1c345e3ffc85
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254804"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu szablonu Menedżer zasobów

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów.
Ten przewodnik Szybki start przeprowadzi Cię przez proces tworzenia przypisania zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny wirtualne, które nie korzystają z dysków zarządzanych. Są one *niezgodne* z przypisaniem zasad.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku szybki start utworzysz przypisanie zasad i przypiszesz wbudowaną definicję zasad o nazwie *Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych*. Aby zapoznać się z częściową listą dostępnych wbudowanych zasad, zobacz [Azure Policy Samples](./samples/index.md).

Istnieje kilka metod tworzenia przypisań zasad. W tym przewodniku Szybki Start zostanie użyty [szablon szybkiego startu](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Oto kopia szablonu:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Usługa Azure Policy jest bezpłatna.  Aby uzyskać więcej informacji, zobacz [omówienie Azure Policy](./overview.md).

1. Wybierz następujący obraz, aby zalogować się do Azure Portal i otworzyć szablon:

   [@no__t — 1Deploy szablonu zasad na platformie Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości:

   | Nazwa | Wartość |
   |------|-------|
   | Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
   | Grupa zasobów | Wybierz pozycję **Utwórz nowy**, określ nazwę, a następnie wybierz przycisk **OK**. Na zrzucie ekranu nazwa grupy zasobów to *mypolicyquickstart @ no__t-1Date w MMDD > RG*. |
   | Lokalizacja | Wybierz region. Na przykład **Środkowe stany USA**. |
   | Nazwa przypisania zasad | Określ nazwę przydziału zasad. Jeśli chcesz, możesz użyć wyświetlania definicji zasad. Na przykład **Przeprowadź inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych**. |
   | Nazwa RG | Określ nazwę grupy zasobów, do której chcesz przypisać zasady. W tym przewodniku szybki start Użyj wartości domyślnej **[resourceName (). Name]** . **[resourceing ()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)** to funkcja szablonu, która pobiera grupę zasobów. |
   | Identyfikator definicji zasad | Określ **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4C19-B460-a2d36003525a**. |
   | Wyrażam zgodę na powyższe warunki i postanowienia | Zaznaczenia |

1. Wybierz pozycję **Kup**.

Dodatkowe zasoby:

- Aby znaleźć więcej przykładów szablonów, zobacz [szablon szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Aby wyświetlić odwołanie do szablonu, przejdź do pozycji [Dokumentacja szablonu platformy Azure](/azure/templates/microsoft.authorization/allversions).
- Aby dowiedzieć się, jak opracowywać szablony Menedżer zasobów, zobacz [dokumentację dotyczącą Azure Resource Manager](/azure/azure-resource-manager/).
- Aby uzyskać informacje na temat wdrażania na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Wybierz pozycję **Zgodność** w lewej części strony. Znajdź utworzone przypisanie zasad **Audit VMs that do not use managed disks** (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

![Strona przeglądu zgodności zasad](./media/assign-policy-template/policy-compliance.png)

Jeśli istnieją jakiekolwiek zasoby niezgodne z nowym przypisaniem, zostaną one wyświetlone w obszarze **Niezgodne zasoby**.

Aby uzyskać więcej informacji, zobacz [jak działa zgodność](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące kroki:

1. Wybierz pozycję **Zgodność** (lub **Przypisania**) w lewej części strony usługi Azure Policy i znajdź utworzone przypisanie zasad **Audit VMs that do not use managed disks** (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

1. Kliknij prawym przyciskiem myszy **maszyny wirtualne inspekcji, które nie używają przypisania zasad dysków zarządzanych** , i wybierz pozycję **Usuń przypisanie**.

   ![Usuwanie przypisania ze strony przeglądu zgodności](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przypisano wbudowaną definicję zasad do zakresu i ocenił swój raport zgodności. Definicja zasady zawiera sprawdzenie, czy wszystkie zasoby w ramach zakresu są zgodne, oraz określenie niezgodnych zasobów.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)