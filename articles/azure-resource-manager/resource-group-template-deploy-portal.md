---
title: Wdrażaj zasoby platformy Azure za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Wdrożyć swoje zasoby za pomocą witryny Azure portal i usługi Azure Resource Manager.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 7b28129a3afe9f78d0ef749fa0c7759082c5f758
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60520424"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Deploy resources with Resource Manager templates and Azure portal (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure Portal)

W tym artykule pokazano, jak [witryny Azure portal](https://portal.azure.com) z [usługi Azure Resource Manager](resource-group-overview.md) do wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej o zarządzaniu swoimi zasobami, zobacz [zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal](manage-resources-portal.md).

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

1. Aby utworzyć pustą grupę zasobów, wybierz **grup zasobów**.

   ![Wybór grup zasobów](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. W obszarze grupy zasobów wybierz **Dodaj**.

   ![Dodaj grupę zasobów](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Nadaj jej nazwę i lokalizację, a jeśli to konieczne, wybierz subskrypcję. Należy podać lokalizację dla grupy zasobów, ponieważ grupa zasobów przechowuje metadane dotyczące zasobów. Ze względu na zasady zgodności można określić miejsce przechowywania tych metadanych. Ogólnie rzecz biorąc firma Microsoft zaleca, aby określić lokalizację, w której będą znajdować się większość zasobów. Przy użyciu tej samej lokalizacji można uproszczenie szablonu.

   ![Ustawianie wartości grupy](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Po zakończeniu ustawiania właściwości wybierz **Utwórz**.

1. Aby wyświetlić nowej grupy zasobów, wybierz **Odśwież**.

   ![Odśwież grup zasobów](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Wdrażanie zasobów z poziomu witryny Marketplace

Po utworzeniu grupy zasobów, możesz wdrożyć zasoby do niego w witrynie Marketplace. Portal Marketplace oferuje rozwiązania wstępnie zdefiniowane dla typowych scenariuszy.

1. Aby uruchomić wdrożenie, zaznacz **Utwórz zasób**.

   ![Nowy zasób](./media/resource-group-template-deploy-portal/new-resources.png)

1. Znajdź typ zasobu, który chcesz wdrożyć.

   ![Wybierz typ zasobu](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Jeśli nie widać danego rozwiązania, które chcesz wdrożyć, możesz wyszukać rynku go. Na przykład, aby znaleźć rozwiązania Wordpress, zacznij pisać **Wordpress** i wybierz odpowiednią opcję.

   ![Wyszukaj w usłudze marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. W zależności od typu wybranego zasobu masz kolekcję odpowiednich właściwości można ustawić przed wdrożeniem. Dla wszystkich typów należy wybrać docelowa grupa zasobów. Na poniższej ilustracji przedstawiono sposób tworzenia aplikacji sieci web i wdrożyć ją na utworzoną grupę zasobów.

   ![Tworzenie grupy zasobów](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Alternatywnie możesz zdecydować się na utworzenie grupy zasobów, podczas wdrażania zasobów. Wybierz **Utwórz nową** i nadaj nazwę grupie zasobów.

   ![Utwórz nową grupę zasobów](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Rozpocznie się wdrożenie. Wdrożenie może potrwać kilka minut. Po zakończeniu wdrażania zostanie wyświetlone powiadomienie.

   ![Wyświetl powiadomienia](./media/resource-group-template-deploy-portal/view-notification.png)

1. Po wdrożeniu Twoich zasobów, można dodać więcej zasobów do grupy zasobów, wybierając **Dodaj**.

   ![Dodaj zasób](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Wdrażanie zasobów za pomocą szablonu niestandardowego

Jeśli chcesz wykonać wdrożenie, ale nie mogą używać tych szablonów w portalu Marketplace, można utworzyć szablon niestandardowy, który definiuje infrastrukturę Twojego rozwiązania. Aby dowiedzieć się więcej na temat tworzenia szablonów, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> W interfejsie portalu nie obsługuje odwołujące się do [wpisu tajnego z usługi Key Vault](resource-manager-keyvault-parameter.md). Zamiast tego należy użyć [PowerShell](resource-group-template-deploy.md) lub [wiersza polecenia platformy Azure](resource-group-template-deploy-cli.md) do wdrażania szablonu, lokalnie lub za pośrednictwem zewnętrznego identyfikatora URI.

1. Aby wdrożyć szablon niestandardowy, za pośrednictwem portalu, wybierz **Utwórz zasób**i wyszukaj **wdrożenie szablonu** dopiero po jej wybraniu, korzystając z opcji.

   ![Wdrożenie szablonu wyszukiwania](./media/resource-group-template-deploy-portal/search-template.png)

1. Wybierz pozycję **Utwórz**.

   ![Wybieranie pozycji Utwórz](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Widzisz kilka opcji tworzenia szablonu. Wybierz pozycję **Utwórz własny szablon w edytorze**.

   ![Opcje wyświetlania](./media/resource-group-template-deploy-portal/see-options.png)

1. Masz pustego szablonu, który jest dostępny do dostosowywania.

   ![Tworzenie szablonu](./media/resource-group-template-deploy-portal/blank-template.png)

1. Możesz ręcznie edytować ze składnią pliku JSON lub wybrać wstępnie utworzonego szablonu z [galerii szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/). Jednak w tym artykule użyto **Dodaj zasób** opcji.

   ![Edytuj szablon](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Wybierz **konta magazynu** i podaj nazwę. Po zakończeniu podawania wartości wybierz pozycję **OK**.

   ![Wybieranie konta magazynu](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. Edytor automatycznie dodaje JSON dla tego typu zasobu. Należy zauważyć, że zawiera on parametr definiujący typ konta magazynu. Wybierz pozycję **Zapisz**.

   ![Wyświetlanie szablonu](./media/resource-group-template-deploy-portal/show-json.png)

1. Teraz użytkownik może zostać wdrożone zasoby zdefiniowane w szablonie. Aby wdrożyć, zaakceptuj warunki i postanowienia, a następnie wybierz **zakupu**.

   ![Wdrażanie szablonu](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Wdrażanie zasobów za pomocą szablonu, na swoim koncie

Portal umożliwia zapisywanie szablonu do konta platformy Azure i wdrażając go ponownie później. Aby uzyskać więcej informacji dotyczących szablonów, zobacz [tworzenie i wdrażanie pierwszego szablonu usługi Azure Resource Manager](resource-manager-create-first-template.md).

1. Zapisane szablonów, wybierz **więcej usług**.

   ![Więcej usług](./media/resource-group-template-deploy-portal/more-services.png)

1. Wyszukaj **szablony** i wybierz tę opcję.

   ![Wyszukaj szablony](./media/resource-group-template-deploy-portal/find-templates.png)

1. Z listy szablonów na swoim koncie wybierz ten, który chcesz pracować.

   ![Zapisane szablony](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Wybierz **Wdróż** ponownie wdrożyć ten szablon zapisany.

   ![Zapisany szablon wdrażania](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Kolejne kroki

- Aby wyświetlić dzienniki inspekcji, zobacz [inspekcji operacji przy użyciu usługi Resource Manager](./resource-group-audit.md).
- Aby rozwiązać błędy związane z wdrażaniem, zobacz [wyświetlanie operacji wdrażania](./resource-manager-deployment-operations.md).
- Aby wyeksportować szablon z wdrożenia lub grupy zasobów, zobacz [eksportu usługi Azure Resource Manager](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Aby bezpiecznie wdrożenia usługi w wielu regionach, zobacz [Azure Deployment Manager](./deployment-manager-overview.md).
