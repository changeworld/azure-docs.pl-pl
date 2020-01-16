---
title: 'Szybki Start: nowe przypisanie zasad z szablonami'
description: W tym przewodniku szybki start utworzysz przypisanie zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu szablonu Menedżer zasobów.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 8b9b0024e5c15c78c6777b8657839791484d66b5
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980508"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu szablonu Menedżer zasobów

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów.
Ten przewodnik Szybki start przeprowadzi Cię przez proces tworzenia przypisania zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny wirtualne, które nie korzystają z dysków zarządzanych. Są one _niezgodne_ z przypisaniem zasad.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku szybki start utworzysz przypisanie zasad i przypiszesz wbudowaną definicję zasad o nazwie _Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych_. Aby zapoznać się z częściową listą dostępnych wbudowanych zasad, zobacz [Azure Policy Samples](./samples/index.md).

Istnieje kilka metod tworzenia przypisań zasad. W tym przewodniku Szybki Start zostanie użyty [szablon szybkiego startu](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Oto kopia szablonu:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Usługa Azure Policy jest bezpłatna. Aby uzyskać więcej informacji, zobacz [omówienie Azure Policy](./overview.md).

1. Wybierz następujący obraz, aby zalogować się do Azure Portal i otworzyć szablon:

   [![wdrożyć szablon zasad na platformie Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości:

   | Nazwa | Wartość |
   |------|-------|
   | Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
   | Grupa zasobów | Wybierz pozycję **Utwórz nowy**, określ nazwę, a następnie wybierz przycisk **OK**. Na zrzucie ekranu nazwa grupy zasobów to _mypolicyquickstart\<Data w MMDD\>RG_. |
   | Lokalizacja | Wybierz region. Na przykład **Środkowe stany USA**. |
   | Nazwa przypisania zasad | Określ nazwę przydziału zasad. Jeśli chcesz, możesz użyć wyświetlania definicji zasad. Na przykład **Przeprowadź inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych**. |
   | Nazwa RG | Określ nazwę grupy zasobów, do której chcesz przypisać zasady. W tym przewodniku szybki start Użyj wartości domyślnej **[resourceName (). Name]** . **[resourceing ()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** to funkcja szablonu, która pobiera grupę zasobów. |
   | Identyfikator definicji zasad | Określ **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4C19-B460-a2d36003525a**. |
   | Wyrażam zgodę na powyższe warunki i postanowienia | Zaznaczenia |

1. Wybierz pozycję **Kup**.

Dodatkowe zasoby:

- Aby znaleźć więcej przykładów szablonów, zobacz [szablon szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Aby wyświetlić odwołanie do szablonu, przejdź do pozycji [Dokumentacja szablonu platformy Azure](/azure/templates/microsoft.authorization/allversions).
- Aby dowiedzieć się, jak opracowywać szablony Menedżer zasobów, zobacz [dokumentację dotyczącą Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Aby uzyskać informacje na temat wdrażania na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](../../azure-resource-manager/templates/deploy-to-subscription.md).

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