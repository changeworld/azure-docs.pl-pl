---
title: Wdrażaj zasoby platformy Azure za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Wdrożyć swoje zasoby za pomocą witryny Azure portal i usługi Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: a171d9b4f054c942eebb08e7e11dd1164545f408
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460322"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Deploy resources with Resource Manager templates and Azure portal (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure Portal)

Dowiedz się, jak używać [witryny Azure portal](https://portal.azure.com) z [usługi Azure Resource Manager](resource-group-overview.md) do wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej o zarządzaniu swoimi zasobami, zobacz [zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal](manage-resources-portal.md).

Wdrażanie zasobów platformy Azure przy użyciu witryny Azure portal, zwykle obejmuje dwa kroki:

- Utwórz grupę zasobów.
- Wdrażanie zasobów w grupie zasobów.

Ponadto można także wdrożyć szablon usługi Azure Resource Manager do tworzenia zasobów platformy Azure.

W tym artykule przedstawiono obie metody.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

1. Aby utworzyć nową grupę zasobów, wybierz **grup zasobów** z [witryny Azure portal](https://portal.azure.com).

   ![Wybór grup zasobów](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. W obszarze grupy zasobów wybierz **Dodaj**.

   ![Dodaj grupę zasobów](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Wybierz lub wprowadź następujące wartości właściwości:

    - **Subskrypcja**: Wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: Nazwij grupę zasobów.
    - **Region**: Określ lokalizację platformy Azure. Jest to, gdy grupa zasobów przechowuje metadane dotyczące zasobów. Ze względu na zasady zgodności można określić miejsce przechowywania tych metadanych. Ogólnie rzecz biorąc firma Microsoft zaleca, aby określić lokalizację, w której będą znajdować się większość zasobów. Przy użyciu tej samej lokalizacji można uproszczenie szablonu.

   ![Ustawianie wartości grupy](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Wybierz pozycję **Przegląd + utwórz**.
1. Sprawdź wartości, a następnie wybierz **Utwórz**.
1. Wybierz **Odśwież** zanim zobaczysz nową grupę zasobów, na liście.

## <a name="deploy-resources-to-a-resource-group"></a>Wdrażanie zasobów w grupie zasobów

Po utworzeniu grupy zasobów, możesz wdrożyć zasoby do grupy w portalu Marketplace. Portal Marketplace oferuje rozwiązania wstępnie zdefiniowane dla typowych scenariuszy.

1. Aby uruchomić wdrożenie, zaznacz **Utwórz zasób** z [witryny Azure portal](https://portal.azure.com).

   ![Nowy zasób](./media/resource-group-template-deploy-portal/new-resources.png)

1. Znajdź typ zasobu, który chcesz wdrożyć. Zasoby są zorganizowane w kategoriach. Jeśli nie widać danego rozwiązania, które chcesz wdrożyć, możesz wyszukać rynku go. Poniższy zrzut ekranu pokazuje, że jest zaznaczony serwer Ubuntu.

   ![Wybierz typ zasobu](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. W zależności od typu wybranego zasobu masz kolekcję odpowiednich właściwości można ustawić przed wdrożeniem. Dla wszystkich typów należy wybrać docelowa grupa zasobów. Na poniższej ilustracji przedstawiono sposób tworzenia maszyny wirtualnej systemu Linux i wdrożyć ją na utworzoną grupę zasobów.

   ![Tworzenie grupy zasobów](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Alternatywnie możesz zdecydować się na utworzenie grupy zasobów, podczas wdrażania zasobów. Wybierz **Utwórz nową** i nadaj nazwę grupie zasobów.

1. Rozpocznie się wdrożenie. Wdrożenie może potrwać kilka minut. Niektóre zasoby trwać dłużej niż inne zasoby. Po zakończeniu wdrażania zostanie wyświetlone powiadomienie. Wybierz **przejdź do zasobu** do otwarcia

   ![Wyświetl powiadomienia](./media/resource-group-template-deploy-portal/view-notification.png)

1. Po wdrożeniu Twoich zasobów, można dodać więcej zasobów do grupy zasobów, wybierając **Dodaj**.

   ![Dodaj zasób](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Wdrażanie zasobów za pomocą szablonu niestandardowego

Jeśli chcesz wykonać wdrożenie, ale nie mogą używać tych szablonów w portalu Marketplace, można utworzyć szablon niestandardowy, który definiuje infrastrukturę Twojego rozwiązania. Aby dowiedzieć się więcej na temat tworzenia szablonów, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> W interfejsie portalu nie obsługuje odwołujące się do [wpisu tajnego z usługi Key Vault](resource-manager-keyvault-parameter.md). Zamiast tego należy użyć [PowerShell](resource-group-template-deploy.md) lub [wiersza polecenia platformy Azure](resource-group-template-deploy-cli.md) do wdrażania szablonu, lokalnie lub za pośrednictwem zewnętrznego identyfikatora URI.

1. Aby wdrożyć szablon niestandardowy, za pośrednictwem portalu, wybierz **Utwórz zasób**, wyszukaj **szablonu**. a następnie wybierz **wdrożenie szablonu**.

   ![Wdrożenie szablonu wyszukiwania](./media/resource-group-template-deploy-portal/search-template.png)

1. Wybierz pozycję **Utwórz**.
1. Zostaną wyświetlone kilka opcji umożliwiających tworzenie szablonu:

    - **Utwórz własny szablon w edytorze**: Tworzenie szablonu przy użyciu szablonu portalu edytora.  Edytor jest w stanie dodać schemat szablonu zasobów.
    - **Wspólnych szablonów**: Istnieją cztery templatess wspólne do tworzenia maszyny wirtualnej systemu Linux, maszyna wirtualna Windows, aplikacji sieci web i bazy danych Azure SQL.
    - **Załaduj szablon szybkiego startu usługi GitHub**: Użyj istniejącego [szablony szybkiego startu](https://azure.microsoft.com/resources/templates/).

   ![Opcje wyświetlania](./media/resource-group-template-deploy-portal/see-options.png)

    Ten samouczek zawiera instrukcję podczas ładowania szablonu szybkiego startu.

1. W obszarze **Załaduj szablon szybkiego startu usługi GitHub**, wpisz lub wybierz **101-storage konta create**.

    Dostępne są dwie opcje:

    - **Wybierz szablon**: wdrożenia szablonu.
    - **Edytuj szablon**: Edytuj szablon szybkiego startu, przed jego wdrożeniem.

1. Wybierz **Edytuj szablon** do eksplorowania edytor szablonów portalu. Szablon jest ładowany w edytorze. Zwróć uwagę, istnieją dwa parametry: **storageAccountType** i **lokalizacji**.

   ![Tworzenie szablonu](./media/resource-group-template-deploy-portal/show-json.png)

1. Wprowadź drobne zmiany do szablonu. Na przykład aktualizacji **storageAccountName** zmienną:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Wybierz pozycję **Zapisz**. Spowoduje to wyświetlenie interfejs wdrażania szablonu portalu. Należy zauważyć dwa parametry, które są zdefiniowane w szablonie.
1. Wprowadź lub wybierz wartości właściwości:

    - **Subskrypcja**: Wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: Wybierz **Utwórz nową** i nadaj nazwę.
    - **Lokalizacja**: Wybierz lokalizację platformy Azure.
    - **Typ konta magazynu**: Użyj wartości domyślnej.
    - **Lokalizacja**: Użyj wartości domyślnej.
    - **Wyrażam zgodę na powyższe warunki i postanowienia**: (wybierz)

1. Wybierz pozycję **Kup**.

## <a name="next-steps"></a>Kolejne kroki

- Aby wyświetlić dzienniki inspekcji, zobacz [inspekcji operacji przy użyciu usługi Resource Manager](./resource-group-audit.md).
- Aby rozwiązać błędy związane z wdrażaniem, zobacz [wyświetlanie operacji wdrażania](./resource-manager-deployment-operations.md).
- Aby wyeksportować szablon z wdrożenia lub grupy zasobów, zobacz [eksportu usługi Azure Resource Manager](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Aby bezpiecznie wdrożyć usługę w wielu regionach, zobacz [Azure Deployment Manager](./deployment-manager-overview.md).
