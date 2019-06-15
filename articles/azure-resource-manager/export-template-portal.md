---
title: Eksportowanie szablonu usługi Azure Resource Manager przy użyciu witryny Azure portal
description: Eksportowanie szablonu usługi Azure Resource Manager z zasobów w ramach subskrypcji za pomocą witryny Azure portal.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: ea9499da3dac67635a48704f439f6592c6ed467e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65515387"
---
# <a name="single-and-multi-resource-export-to-template-in-azure-portal"></a>Eksportowanie pojedynczych i wielu zasobów do szablonu w witrynie Azure portal

Aby ułatwić tworzenie szablonów usługi Azure Resource Manager, możesz wyeksportować szablon z istniejących zasobów. Wyeksportowany szablon ułatwia zrozumienie składni JSON i właściwości, które wdrażasz swoje zasoby. Aby zautomatyzować przyszłych wdrożeniach, rozpoczynać wyeksportowany szablon i zmodyfikuj go odpowiednio do danego scenariusza.

Usługi Resource Manager pozwala wybrać jeden lub więcej zasobów do eksportowania do szablonu. Możesz skupić się na potrzebne w szablonie zasoby.

## <a name="choose-the-right-export-option"></a>Wybierz opcję prawo eksportu

Istnieją dwa sposoby eksportowania szablonu:

* **Eksportowanie z grupy zasobów lub zasobu**. Ta opcja powoduje wygenerowanie nowego szablonu z istniejących zasobów. Wyeksportowany szablon jest "snapshot" bieżący stan grupy zasobów. Możesz wyeksportować całą grupę zasobów lub określonych zasobów w tej grupie zasobów.

* **Eksportowanie przed wdrożeniem lub z historii**. Ta opcja umożliwia pobranie dokładną kopię szablonu użyć dla wdrożenia.

W zależności od wybranej opcji wyeksportowane szablony mają różne cechy.

| Z grupy zasobów lub zasobu | Przed przystąpieniem do wdrożenia lub z historii |
| --------------------- | ----------------- |
| Szablon jest migawki bieżącego stanu zasobów. Obejmuje ona wszelkie ręczne zmiany wprowadzone po wdrożeniu. | Szablon zawiera tylko stan zasobów w czasie wdrażania. Wszelkie ręczne zmiany wprowadzone po wdrożeniu nie są uwzględniane. |
| Możesz wybrać zasoby z grupy zasobów do wyeksportowania. | Uwzględniono wszystkie zasoby dla określonego wdrożenia. Nie można wybrać podzbiór tych zasobów, lub Dodaj zasoby, które zostały dodane w innym czasie. |
| Szablon zawiera wszystkie właściwości dla zasobów, w tym niektóre właściwości, które normalnie nie ustawisz podczas wdrażania. Możesz chcieć usunąć lub wyczyścić te właściwości, przed ponownym użyciem tego szablonu. | Szablon zawiera tylko właściwości, które są potrzebne do wdrożenia. Szablon jest gotowy do użycia. |
| Szablon prawdopodobnie nie zawiera wszystkich parametrów, które są potrzebne do ponownego wykorzystania. Większość wartości właściwości są zakodowane w szablonie. Aby przeprowadzić ponowne wdrożenie szablonu w innych środowiskach, należy dodać parametry, które zwiększają możliwość konfigurowania zasobów. | Szablon zawiera parametry, które ułatwiają do ponownego wdrożenia w różnych środowiskach. |

Eksportowanie szablonu z grupy zasobów lub zasobu, gdy:

* Zachodzi potrzeba przechwytywania zmianami w zasobach, które zostały wprowadzone po oryginalnego wdrożenia.
* Chcesz wybrać, które zasoby są eksportowane.

Eksportowanie szablonu przed wdrożeniem lub z historii, gdy:

* Chcesz, aby ponowne łatwe użycie szablonu.
* Nie należy do uwzględnienia zmian wprowadzonych po oryginalnego wdrożenia.

## <a name="export-template-from-resource-group"></a>Eksportowanie szablonu z grupy zasobów

Aby wyeksportować jeden lub więcej zasobów z grupy zasobów:

1. Wybierz grupę zasobów, która zawiera zasoby, które chcesz wyeksportować.

1. Aby wyeksportować wszystkie zasoby w grupie zasobów, zaznacz wszystko i następnie **Eksportuj szablon**. **Eksportuj szablon** opcja staje się dostępne tylko po wybraniu co najmniej jeden zasób.

   ![Eksportuj wszystkie zasoby](./media/export-template-portal/select-all-resources.png)

1. Aby wybrać określone zasoby eksportu, zaznacz pola wyboru obok tych zasobów. Następnie wybierz **Eksportuj szablon**.

   ![Wybierz zasoby do wyeksportowania](./media/export-template-portal/select-resources.png)

1. Wyeksportowany szablon jest wyświetlany i jest dostępna do pobrania.

   ![Wyświetlanie szablonu](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>Eksportowanie szablonu z zasobu

Aby wyeksportować jeden zasób:

1. Wybierz grupę zasobów zawierającą zasób, który chcesz wyeksportować.

1. Wybierz zasób do wyeksportowania.

   ![Wybierz zasób](./media/export-template-portal/select-link-resource.png)

1. Dla tego zasobu, wybierz **Eksportuj szablon** w okienku po lewej stronie.

   ![Eksportuj zasób](./media/export-template-portal/export-single-resource.png)

1. Wyeksportowany szablon jest wyświetlany i jest dostępna do pobrania. Szablon zawiera tylko pojedynczy zasób.

## <a name="export-template-before-deployment"></a>Eksportowanie szablonu przed przystąpieniem do wdrożenia

1. Wybierz usługę platformy Azure, którą chcesz wdrożyć.

1. Podaj wartości dla nowej usługi.

1. Po pomyślnym sprawdzania poprawności, ale przed rozpoczęciem wdrażania, wybierz **Pobierz szablon do automatyzacji**.

   ![Pobieranie szablonu](./media/export-template-portal/download-before-deployment.png)

1. Szablon jest wyświetlany i jest dostępny do pobrania.

   ![Wyświetlanie szablonu](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>Eksportowanie szablonu po wdrożeniu

Możesz wyeksportować szablon, który został użyty do wdrożenia istniejących zasobów. Szablon, który jest pobierany jest dokładnie jeden, który został użyty do wdrożenia.

1. Wybierz grupę zasobów, które chcesz wyeksportować.

1. Wybierz link w obszarze **wdrożeń**.

   ![Wybierz pozycję Historia wdrażania](./media/export-template-portal/select-deployment-history.png)

1. Wybierz jedno z wdrożeń z historii wdrożenia.

   ![Wybieranie pozycji wdrożenie](./media/export-template-portal/select-details.png)

1. Wybierz **szablonu**. Szablon użyty dla tego wdrożenia jest wyświetlany i jest dostępna do pobrania.

   ![Wybierz szablon](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Kolejne kroki

- Aby poznać usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](./resource-group-overview.md).
- Aby dowiedzieć się składni szablonu usługi Resource Manager, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](./resource-group-authoring-templates.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić schematy szablonów usługi Azure Resource Manager, zobacz [odwołanie do szablonu](/azure/templates/).