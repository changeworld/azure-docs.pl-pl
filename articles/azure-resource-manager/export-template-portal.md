---
title: Eksportuj szablon Menedżer zasobów — Azure Portal
description: Użyj Azure Portal, aby wyeksportować szablon Azure Resource Manager z zasobów w ramach subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tomfitz
ms.openlocfilehash: 0605e24590fa2d702a1385429a7808a7e1226809
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532342"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Eksport jednego i kilku zasobów do szablonu w Azure Portal

Aby pomóc w tworzeniu szablonów Azure Resource Manager, można wyeksportować szablon z istniejących zasobów. Wyeksportowany szablon pomaga zrozumieć składnię i właściwości JSON, które wdrażają zasoby. Aby zautomatyzować przyszłe wdrożenia, Zacznij od wyeksportowanego szablonu i zmodyfikuj go dla danego scenariusza.

Menedżer zasobów umożliwia wybranie jednego lub większej liczby zasobów do wyeksportowania do szablonu. Możesz skupić się na dokładnie żądanych zasobach w szablonie.

W tym artykule przedstawiono sposób eksportowania szablonów za pomocą portalu. Można również użyć interfejsu [wiersza polecenia platformy Azure](manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)lub [API REST](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Wybierz opcję eksportu z prawej strony

Istnieją dwa sposoby eksportowania szablonu:

* **Eksportuj z grupy zasobów lub zasobu**. Ta opcja generuje nowy szablon z istniejących zasobów. Wyeksportowany szablon jest "migawką" bieżącego stanu grupy zasobów. Można wyeksportować całą grupę zasobów lub konkretne zasoby w ramach tej grupy zasobów.

* **Eksportuj przed wdrożeniem lub z historii**. Ta opcja pobiera dokładną kopię szablonu użytego do wdrożenia.

W zależności od wybranej opcji, eksportowane szablony mają różne jakości.

| Z grupy zasobów lub zasobu | Przed wdrożeniem lub historią |
| --------------------- | ----------------- |
| Szablon jest migawką bieżącego stanu zasobów. Obejmuje ona wszystkie zmiany ręczne dokonane po wdrożeniu. | Szablon pokazuje tylko stan zasobów w czasie wdrażania. Wszystkie zmiany ręczne wprowadzone po wdrożeniu nie są uwzględniane. |
| Możesz wybrać zasoby z grupy zasobów do wyeksportowania. | Uwzględniono wszystkie zasoby dla określonego wdrożenia. Nie można wybrać podzestawu tych zasobów ani dodać zasobów, które zostały dodane w innym czasie. |
| Szablon zawiera wszystkie właściwości zasobów, w tym niektóre właściwości, które normalnie nie zostały ustawione podczas wdrażania. Przed ponownym użyciem szablonu warto usunąć lub wyczyścić te właściwości. | Szablon zawiera tylko właściwości, które są związane z wdrożeniem. Szablon jest gotowy do użycia. |
| Szablon prawdopodobnie nie zawiera wszystkich parametrów, które są potrzebne do ponownego użycia. Większość wartości właściwości jest zakodowana w szablonie. Aby ponownie wdrożyć szablon w innych środowiskach, należy dodać parametry, które zwiększają możliwości konfiguracji zasobów. | Szablon zawiera parametry, które ułatwiają ponowne wdrażanie w różnych środowiskach. |

Wyeksportuj szablon z grupy zasobów lub zasobu, gdy:

* Musisz przechwycić zmiany w zasobach, które zostały wprowadzone po pierwotnym wdrożeniu.
* Chcesz wybrać zasoby, które mają zostać wyeksportowane.

Wyeksportuj szablon przed wdrożeniem lub z historii, gdy:

* Chcesz, aby szablon był łatwy do ponownego użycia.
* Nie trzeba dołączać zmian wprowadzonych po oryginalnym wdrożeniu.

## <a name="export-template-from-a-resource-group"></a>Eksportuj szablon z grupy zasobów

Aby wyeksportować jeden lub więcej zasobów z grupy zasobów:

1. Wybierz grupę zasobów zawierającą zasoby, które chcesz wyeksportować.

1. Wybierz co najmniej jeden zasób, zaznaczając pola wyboru.  Aby zaznaczyć wszystkie, zaznacz pole wyboru po lewej stronie **nazwy**. Element menu **Eksportuj szablon** zostanie włączony dopiero po wybraniu co najmniej jednego zasobu.

   ![Eksportuj wszystkie zasoby](./media/export-template-portal/select-all-resources.png)

    Na zrzucie ekranu jest zaznaczone tylko konto magazynu.
1. Wybierz pozycję **Eksportuj szablon**.

1. Zostanie wyświetlony wyeksportowany szablon, który jest dostępny do pobrania i wdrożenia.

   ![Pokaż szablon](./media/export-template-portal/show-template.png)

## <a name="export-template-from-a-resource"></a>Eksportuj szablon z zasobu

Aby wyeksportować jeden zasób:

1. Wybierz grupę zasobów zawierającą zasób, który chcesz wyeksportować.

1. Wybierz zasób, który chcesz wyeksportować, aby otworzyć zasób.

1. W przypadku tego zasobu wybierz pozycję **Eksportuj szablon** w lewym okienku.

   ![Eksportuj zasób](./media/export-template-portal/export-single-resource.png)

1. Zostanie wyświetlony wyeksportowany szablon, który jest dostępny do pobrania i wdrożenia. Szablon zawiera tylko jeden zasób.

## <a name="export-template-before-deployment"></a>Eksportuj szablon przed wdrożeniem

1. Wybierz usługę platformy Azure, którą chcesz wdrożyć.

1. Wprowadź wartości dla nowej usługi.

1. Po przekazaniu walidacji, ale przed rozpoczęciem wdrażania, wybierz pozycję **Pobierz szablon do automatyzacji**.

   ![Pobieranie szablonu](./media/export-template-portal/download-before-deployment.png)

1. Szablon jest wyświetlany i jest dostępny do pobrania i wdrożenia.


## <a name="export-template-after-deployment"></a>Eksportuj szablon po wdrożeniu

Możesz wyeksportować szablon, który został użyty do wdrożenia istniejących zasobów. Pobrany szablon jest dokładnie taki, który został użyty do wdrożenia.

1. Wybierz grupę zasobów, którą chcesz wyeksportować.

1. Wybierz link w obszarze **wdrożenia**.

   ![Wybierz historię wdrożenia](./media/export-template-portal/select-deployment-history.png)

1. Wybierz jedno z wdrożeń z historii wdrażania.

   ![Wybieranie wdrożenia](./media/export-template-portal/select-details.png)

1. Wybierz **szablon**. Zostanie wyświetlony szablon użyty dla tego wdrożenia, który jest dostępny do pobrania.

   ![Wybierz szablon](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak eksportować szablony przy użyciu interfejsu [wiersza polecenia platformy Azure](manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)lub [API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Aby poznać składnię szablonu Menedżer zasobów, zobacz [Opis struktury i składni Azure Resource Manager szablonów](./resource-group-authoring-templates.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [Samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić Azure Resource Manager Schematy szablonów, zobacz [Dokumentacja szablonu](/azure/templates/).
