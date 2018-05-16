---
title: Wdrażanie zasobów platformy Azure za pomocą portalu Azure | Dokumentacja firmy Microsoft
description: Umożliwia wdrażanie zasobów w portalu Azure i usługi Azure Resource Manager.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: 0174f688cb3fd04fdd41714d54a7371319bae951
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Deploy resources with Resource Manager templates and Azure portal (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure Portal)

W tym temacie przedstawiono sposób użycia [portalu Azure](https://portal.azure.com) z [usługi Azure Resource Manager](resource-group-overview.md) wdrażania zasobów platformy Azure. Aby uzyskać informacje dotyczące zarządzania zasobami, zobacz [zasobów Azure zarządzania za pośrednictwem portalu](resource-group-portal.md).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

1. Aby utworzyć pustej grupy zasobów, wybierz **grup zasobów**.

   ![Wybór grup zasobów](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. W obszarze grupy zasobów, wybierz **Dodaj**.

   ![Dodaj grupę zasobów](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Nadaj nazwę i lokalizację, a w razie potrzeby wybierz subskrypcję. Należy podać lokalizację dla grupy zasobów, ponieważ grupa zasobów są przechowywane metadane dotyczące zasobów. Ze względu na zgodność można określić miejsce przechowywania tych metadanych. Ogólnie rzecz biorąc firma Microsoft zaleca, aby określić lokalizację, w której będą znajdować się większość zasobów. Przy użyciu tej samej lokalizacji może uprościć szablonu.

   ![zestaw wartości grupy](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Po zakończeniu ustawienie właściwości wybierz **Utwórz**.

1. Aby wyświetlić nowej grupy zasobów, wybierz **Odśwież**.

   ![Odśwież grup zasobów](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Wdrażanie zasobów z witryny Marketplace

Po utworzeniu grupy zasobów, zasobów można wdrożyć do niego z witryny Marketplace. Witryny Marketplace udostępnia wstępnie zdefiniowane rozwiązań dla typowych scenariuszy.

1. Aby uruchomić wdrożenia, zaznacz **Utwórz zasób**.

   ![Nowy zasób](./media/resource-group-template-deploy-portal/new-resources.png)

1. Znajdź typ zasobu, który chcesz wdrożyć.

   ![Wybierz typ zasobu](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Jeśli nie ma określonego rozwiązania, które chcesz wdrożyć, możesz wyszukać witryny Marketplace go. Na przykład, aby znaleźć rozwiązania Wordpress, zacznij pisać **Wordpress** i wybierz odpowiednią opcję.

   ![Wyszukiwanie w witrynie marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. W zależności od typu wybranego zasobu masz kolekcję odpowiednie właściwości można ustawić przed przystąpieniem do wdrożenia. Dla wszystkich typów należy wybrać grupę zasobu docelowego. Na poniższej ilustracji przedstawiono sposób tworzenia aplikacji sieci web i wdrożyć ją na utworzoną grupę zasobów.

   ![Tworzenie grupy zasobów](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Alternatywnie można zdecydować utworzyć grupę zasobów, w przypadku wdrażania zasobów. Wybierz **Utwórz nowy** i nadaj nazwę grupie zasobów.

   ![Utwórz nową grupę zasobów](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Rozpoczyna się wdrożenia. Wdrażanie może potrwać kilka minut. Po zakończeniu wdrażania zostanie wyświetlone powiadomienie.

   ![Wyświetl powiadomienie](./media/resource-group-template-deploy-portal/view-notification.png)

1. Po wdrożeniu zasobów, można dodać więcej zasobów do grupy zasobów, wybierając **Dodaj**.

   ![Dodaj zasób](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Wdrażanie zasobów z szablonu niestandardowego

Jeśli chcesz wykonać wdrożenie, ale nie używa tych szablonów w witrynie Marketplace można utworzyć szablon niestandardowy, który definiuje infrastrukturę Twojego rozwiązania. Aby dowiedzieć się więcej na temat tworzenia szablonów, zobacz [poznać strukturę i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).

1. Aby wdrożyć szablon niestandardowy za pośrednictwem portalu, wybierz **Utwórz zasób**i wyszukaj **wdrażania szablonu** dopóki nie zostanie ona wybrana, korzystając z opcji.

   ![Wdrażanie szablonu wyszukiwania](./media/resource-group-template-deploy-portal/search-template.png)

1. Wybierz pozycję **Utwórz**.

   ![Wybierz opcję tworzenia](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Zobacz jest kilka opcji tworzenia szablonu. Wybierz **tworzenia szablonu w edytorze**.

   ![Opcje wyświetlania](./media/resource-group-template-deploy-portal/see-options.png)

1. Masz pusty szablon, który jest dostępny do dostosowywania.

   ![Tworzenie szablonu](./media/resource-group-template-deploy-portal/blank-template.png)

1. Można ręcznie edytować składni JSON, lub wybierz wstępnie przygotowanych szablonu z [galerię szablonów Szybki Start](https://azure.microsoft.com/resources/templates/). W tym artykule, można jednak użyć **Dodaj zasób** opcji.

   ![Edytuj szablon](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Wybierz **konta magazynu** i podaj nazwę. Po zakończeniu, podając wartości, wybierz **OK**.

   ![Wybieranie konta magazynu](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. Edytor automatycznie dodaje JSON dla typu zasobu. Zwróć uwagę, że zawiera on parametr do definiowania typu konta magazynu. Wybierz pozycję **Zapisz**.

   ![Wyświetlanie szablonu](./media/resource-group-template-deploy-portal/show-json.png)

1. Masz teraz opcję, aby wdrożyć zasoby zdefiniowane w szablonie. Aby wdrożyć, akceptujesz warunki i postanowienia, a następnie wybierz **zakupu**.

   ![Wdrażanie szablonu](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Wdrażanie zasobów w ramach szablonu na swoim koncie

Portal umożliwia zapisywanie szablonu do konta platformy Azure i wdróż go ponownie później. Aby uzyskać więcej informacji na temat pracy z tych szablonów, zapisanych [Rozpoczynanie pracy z szablonami prywatnymi w portalu Azure](../marketplace-consumer/mytemplates-getstarted.md).

1. Zapisane szablonów, wybierz **więcej usług**.

   ![Więcej usług](./media/resource-group-template-deploy-portal/more-services.png)

1. Wyszukaj **szablony** i wybierz tę opcję.

   ![Wyszukaj szablony](./media/resource-group-template-deploy-portal/find-templates.png)

1. Z listy szablonów na swoim koncie wybierz jedną, które mają być używane na.

   ![zapisane szablony](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Wybierz **Wdróż** ponownego wdrożenia tego szablonu zapisane.

   ![zapisany szablon wdrażania](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić dzienniki inspekcji, zobacz [inspekcji operacji za pomocą Menedżera zasobów](resource-group-audit.md).
* Aby rozwiązać błędy wdrożenia, zobacz [wyświetlić operacje wdrażania](resource-manager-deployment-operations.md).
* Aby pobrać szablonu z wdrożenia lub grupy zasobów, zobacz [szablonu eksportu usługi Azure Resource Manager z istniejących zasobów](resource-manager-export-template.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
