---
title: Szablon eksportu w witrynie Azure portal
description: Użyj witryny Azure Portal, aby wyeksportować szablon usługi Azure Resource Manager z zasobów w ramach subskrypcji.
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273737"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Eksport pojedynczych i wielozasóbowych do szablonu w witrynie Azure portal

Aby ułatwić tworzenie szablonów usługi Azure Resource Manager, można wyeksportować szablon z istniejących zasobów. Wyeksportowany szablon pomaga zrozumieć składnię JSON i właściwości, które wdrażają zasoby. Aby zautomatyzować przyszłe wdrożenia, należy rozpocząć od wyeksportowanego szablonu i zmodyfikować go w swoim scenariuszu.

Menedżer zasobów umożliwia wybranie jednego lub więcej zasobów do eksportowania do szablonu. Możesz skupić się na dokładnie zasobach, których potrzebujesz w szablonie.

W tym artykule pokazano, jak eksportować szablony za pośrednictwem portalu. Można również użyć [interfejsu API platformy Azure,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [programu Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)lub [interfejsu API REST](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Wybierz odpowiednią opcję eksportu

Istnieją dwa sposoby eksportowania szablonu:

* **Eksportowanie z grupy zasobów lub zasobu**. Ta opcja generuje nowy szablon z istniejących zasobów. Wyeksportowany szablon jest "migawką" bieżącego stanu grupy zasobów. Można wyeksportować całą grupę zasobów lub określone zasoby w ramach tej grupy zasobów.

* **Eksport przed wdrożeniem lub z historii**. Ta opcja pobiera dokładną kopię szablonu używanego do wdrożenia.

W zależności od wybranego wariantu eksportowane szablony mają różne cechy.

| Z grupy zasobów lub zasobu | Przed wdrożeniem lub z historii |
| --------------------- | ----------------- |
| Szablon jest migawką bieżącego stanu zasobów. Zawiera wszelkie ręczne zmiany wprowadzone po wdrożeniu. | Szablon pokazuje tylko stan zasobów w momencie wdrożenia. Wszelkie ręczne zmiany wprowadzone po wdrożeniu nie są uwzględniane. |
| Można wybrać zasoby z grupy zasobów do wyeksportowania. | Wszystkie zasoby dla określonego wdrożenia są uwzględniane. Nie można wybrać podzbiór tych zasobów lub dodać zasoby, które zostały dodane w innym czasie. |
| Szablon zawiera wszystkie właściwości zasobów, w tym niektóre właściwości, które normalnie nie można ustawić podczas wdrażania. Przed ponownym użyciem szablonu można usunąć lub oczyścić te właściwości. | Szablon zawiera tylko właściwości potrzebne do wdrożenia. Szablon jest gotowy do użycia. |
| Szablon prawdopodobnie nie zawiera wszystkich parametrów potrzebnych do ponownego użycia. Większość wartości właściwości są zakodowane w szablonie. Aby ponownie wdrożyć szablon w innych środowiskach, należy dodać parametry, które zwiększają możliwość konfigurowania zasobów.  Można usunąć zaznaczenie **opcji Uwzględnij parametry,** aby można było tworzyć własne parametry. | Szablon zawiera parametry, które ułatwiają ponowne wdrożenie w różnych środowiskach. |

Wyeksportuj szablon z grupy zasobów lub zasobu, gdy:

* Należy przechwycić zmiany zasobów, które zostały wprowadzone po pierwotnym wdrożeniu.
* Chcesz wybrać zasoby, które są eksportowane.

Wyeksportuj szablon przed wdrożeniem lub z historii, gdy:

* Potrzebujesz łatwego do ponownego użycia szablonu.
* Nie trzeba uwzględniać zmian wprowadzonych po pierwotnym wdrożeniu.

## <a name="limitations"></a>Ograniczenia

Podczas eksportowania z grupy zasobów lub zasobu eksportowany szablon jest generowany z [opublikowanych schematów](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) dla każdego typu zasobu. Od czasu do czasu schemat nie ma najnowszej wersji dla typu zasobu. Sprawdź wyeksportowany szablon, aby upewnić się, że zawiera właściwości, których potrzebujesz. W razie potrzeby edytuj eksportowany szablon, aby użyć potrzebnej wersji interfejsu API.

Funkcja szablonu eksportu nie obsługuje eksportowania zasobów usługi Azure Data Factory. Aby dowiedzieć się, jak eksportować zasoby usługi Data Factory, zobacz [Kopiowanie lub klonowanie fabryki danych w usłudze Azure Data Factory](https://aka.ms/exportTemplateViaAdf).

Aby wyeksportować zasoby utworzone za pomocą klasycznego modelu wdrażania, należy [przeprowadzić migrację do modelu wdrażania Menedżera zasobów](https://aka.ms/migrateclassicresourcetoarm).

## <a name="export-template-from-a-resource-group"></a>Eksportowanie szablonu z grupy zasobów

Aby wyeksportować jeden lub więcej zasobów z grupy zasobów:

1. Wybierz grupę zasobów zawierającą zasoby, które chcesz wyeksportować.

1. Zaznacz jeden lub więcej zasobów, zaznaczając pola wyboru.  Aby zaznaczyć wszystkie, zaznacz pole wyboru po lewej stronie **name**. Element menu **Eksportuj szablon** staje się włączony dopiero po wybraniu co najmniej jednego zasobu.

   ![Eksportowanie wszystkich zasobów](./media/export-template-portal/select-all-resources.png)

    Na zrzucie ekranu wybrano tylko konto magazynu.
1. Wybierz **pozycję Eksportuj szablon**.

1. Wyeksportowany szablon jest wyświetlany i jest dostępny do pobrania i wdrożenia.

   ![Pokaż szablon](./media/export-template-portal/show-template.png)

   **Parametry dołączania** są zaznaczone domyślnie.  Po wybraniu tej opcji wszystkie parametry szablonu zostaną uwzględnione podczas generowania szablonu. Jeśli chcesz tworzyć własne parametry, przełącz to pole wyboru, aby ich nie uwzględniać.

## <a name="export-template-from-a-resource"></a>Eksportowanie szablonu z zasobu

Aby wyeksportować jeden zasób:

1. Wybierz grupę zasobów zawierającą zasób, który chcesz wyeksportować.

1. Wybierz zasób, który chcesz wyeksportować, aby otworzyć zasób.

1. Dla tego zasobu wybierz **szablon Eksportuj** w lewym okienku.

   ![Eksportowanie zasobu](./media/export-template-portal/export-single-resource.png)

1. Wyeksportowany szablon jest wyświetlany i jest dostępny do pobrania i wdrożenia. Szablon zawiera tylko pojedynczy zasób. **Parametry dołączania** są zaznaczone domyślnie.  Po wybraniu tej opcji wszystkie parametry szablonu zostaną uwzględnione podczas generowania szablonu. Jeśli chcesz tworzyć własne parametry, przełącz to pole wyboru, aby ich nie uwzględniać.

## <a name="export-template-before-deployment"></a>Eksportowanie szablonu przed wdrożeniem

1. Wybierz usługę platformy Azure, którą chcesz wdrożyć.

1. Wypełnij wartości dla nowej usługi.

1. Po przejściu sprawdzania poprawności, ale przed rozpoczęciem wdrażania, wybierz **pobierz szablon do automatyzacji**.

   ![Pobieranie szablonu](./media/export-template-portal/download-before-deployment.png)

1. Szablon jest wyświetlany i jest dostępny do pobrania i wdrożenia.


## <a name="export-template-after-deployment"></a>Eksportowanie szablonu po wdrożeniu

Można wyeksportować szablon, który został użyty do wdrożenia istniejących zasobów. Szablon, który otrzymasz jest dokładnie ten, który był używany do wdrożenia.

1. Wybierz grupę zasobów, którą chcesz wyeksportować.

1. Wybierz łącze w obszarze **Wdrożenia**.

   ![Wybieranie historii wdrażania](./media/export-template-portal/select-deployment-history.png)

1. Wybierz jedno z wdrożeń z historii wdrażania.

   ![Wybierz wdrożenie](./media/export-template-portal/select-details.png)

1. Wybierz **pozycję Szablon**. Szablon używany dla tego wdrożenia jest wyświetlany i jest dostępny do pobrania.

   ![Wybieranie szablonu](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak eksportować szablony za pomocą [interfejsu wiersza polecenia platformy Azure,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [programu Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)lub interfejsu API [REST](/rest/api/resources/resourcegroups/exporttemplate).
- Aby poznać składnię szablonu Menedżera zasobów, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](template-syntax.md).
- Aby dowiedzieć się, jak tworzyć szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić schematy szablonów usługi Azure Resource Manager, zobacz [odwołanie do szablonu](/azure/templates/).
