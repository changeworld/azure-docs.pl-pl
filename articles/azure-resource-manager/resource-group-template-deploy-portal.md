---
title: Wdrażanie zasobów za pomocą Azure Portal
description: Użyj Azure Portal i Zarządzaj zasobami platformy Azure, aby wdrożyć swoje zasoby w grupie zasobów w ramach subskrypcji.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 6a8842a9fbda11bf48d45152c167d7b072f38119
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150756"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Deploy resources with Resource Manager templates and Azure portal (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure Portal)

Dowiedz się, jak używać [Azure Portal](https://portal.azure.com) z [Azure Resource Manager](resource-group-overview.md) do wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej o zarządzaniu zasobami, zobacz [Zarządzanie zasobami platformy Azure przy użyciu Azure Portal](manage-resources-portal.md).

Wdrażanie zasobów platformy Azure przy użyciu Azure Portal zwykle obejmuje dwa kroki:

- Utwórz grupę zasobów.
- Wdróż zasoby w grupie zasobów.

Ponadto można również wdrożyć szablon Azure Resource Manager, aby utworzyć zasoby platformy Azure.

W tym artykule przedstawiono obie metody.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

1. Aby utworzyć nową grupę zasobów, wybierz pozycję **grupy zasobów** z [Azure Portal](https://portal.azure.com).

   ![Wybierz grupy zasobów](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. W obszarze grupy zasobów wybierz pozycję **Dodaj**.

   ![Dodaj grupę zasobów](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Wybierz lub wprowadź następujące wartości właściwości:

    - **Subskrypcja**: wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: nadaj nazwę grupie zasobów.
    - **Region**: Określ lokalizację platformy Azure. Jest to miejsce, w którym Grupa zasobów przechowuje metadane dotyczące zasobów. Ze względów związanych ze zgodnością warto określić miejsce przechowywania metadanych. Ogólnie rzecz biorąc, zalecamy określenie lokalizacji, w której będą znajdować się większość zasobów. Korzystanie z tej samej lokalizacji może uprościć szablon.

   ![Ustawianie wartości grupy](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Wybierz pozycję **Przegląd + utwórz**.
1. Przejrzyj wartości, a następnie wybierz pozycję **Utwórz**.
1. Wybierz pozycję **Odśwież** , aby wyświetlić nową grupę zasobów na liście.

## <a name="deploy-resources-to-a-resource-group"></a>Wdrażanie zasobów w grupie zasobów

Po utworzeniu grupy zasobów można wdrożyć zasoby w grupie z portalu Marketplace. Portal Marketplace zawiera wstępnie zdefiniowane rozwiązania dla typowych scenariuszy.

1. Aby rozpocząć wdrożenie, wybierz pozycję **Utwórz zasób** na podstawie [Azure Portal](https://portal.azure.com).

   ![Nowy zasób](./media/resource-group-template-deploy-portal/new-resources.png)

1. Znajdź Typ zasobu, który chcesz wdrożyć. Zasoby są zorganizowane w kategorii. Jeśli nie widzisz konkretnego rozwiązania, które chcesz wdrożyć, możesz wyszukać je w portalu Marketplace. Poniższy zrzut ekranu pokazuje, że wybrano serwer Ubuntu.

   ![Wybierz typ zasobu](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. W zależności od typu wybranego zasobu masz kolekcję odpowiednich właściwości do ustawienia przed wdrożeniem. Dla wszystkich typów należy wybrać docelową grupę zasobów. Na poniższej ilustracji pokazano, jak utworzyć maszynę wirtualną z systemem Linux i wdrożyć ją w utworzonej grupie zasobów.

   ![Tworzenie grupy zasobów](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Alternatywnie można utworzyć grupę zasobów podczas wdrażania zasobów. Wybierz pozycję **Utwórz nową** i nadaj grupie zasobów nazwę.

1. Wdrożenie rozpocznie się. Wdrożenie może potrwać kilka minut. Niektóre zasoby będą trwać dłużej niż w przypadku innych zasobów. Po zakończeniu wdrażania zostanie wyświetlone powiadomienie. Wybierz pozycję **Przejdź do zasobu** , aby otworzyć

   ![Wyświetl powiadomienie](./media/resource-group-template-deploy-portal/view-notification.png)

1. Po wdrożeniu zasobów możesz dodać więcej zasobów do grupy zasobów, wybierając pozycję **Dodaj**.

   ![Dodaj zasób](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Wdróż zasoby z szablonu niestandardowego

Jeśli chcesz wykonać wdrożenie, ale nie używasz żadnego z szablonów w portalu Marketplace, możesz utworzyć dostosowany szablon, który definiuje infrastrukturę rozwiązania. Aby dowiedzieć się więcej na temat tworzenia szablonów, zobacz [Opis struktury i składni szablonów Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> Interfejs portalu nie obsługuje odwoływania się do [wpisu tajnego z Key Vault](resource-manager-keyvault-parameter.md). Zamiast tego należy użyć [programu PowerShell](resource-group-template-deploy.md) lub [interfejsu wiersza polecenia platformy Azure](resource-group-template-deploy-cli.md) do wdrożenia szablonu lokalnie lub z zewnętrznego identyfikatora URI.

1. Aby wdrożyć dostosowany szablon za pomocą portalu, wybierz pozycję **Utwórz zasób**, Wyszukaj **szablon**. a następnie wybierz pozycję **Template Deployment**.

   ![Wyszukaj wdrożenie szablonu](./media/resource-group-template-deploy-portal/search-template.png)

1. Wybierz pozycję **Utwórz**.
1. Zobaczysz kilka opcji tworzenia szablonu:

    - **Kompiluj własny szablon w edytorze**: Utwórz szablon przy użyciu edytora szablonów portalu.  Edytor może dodać schemat szablonu zasobu.
    - **Szablony wspólne**: Istnieją cztery popularne szablony służące do tworzenia maszyny wirtualnej z systemem Linux, maszyny wirtualnej systemu Windows, aplikacji sieci Web i bazy danych SQL Azure.
    - **Załaduj szablon szybkiego startu usługi GitHub**: Użyj istniejących [szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/).

   ![Opcje widoku](./media/resource-group-template-deploy-portal/see-options.png)

    Ten samouczek zawiera instrukcje dotyczące ładowania szablonu szybkiego startu.

1. W obszarze **ładowanie szablonu szybkiego startu usługi GitHub**wpisz lub wybierz pozycję **101-Storage-account-Create**.

    Dostępne są dwie opcje:

    - **Wybierz szablon**: Wdróż szablon.
    - **Edytuj szablon**: Edytuj szablon szybkiego startu przed jego wdrożeniem.

1. Wybierz pozycję **Edytuj szablon** , aby poznać Edytor szablonów portalu. Szablon jest ładowany w edytorze. Należy zauważyć, że istnieją dwa parametry: **storageAccountType** i **Location**.

   ![Tworzenie szablonu](./media/resource-group-template-deploy-portal/show-json.png)

1. Wprowadź drobne zmiany w szablonie. Na przykład zaktualizuj zmienną **storageAccountName** do:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Wybierz pozycję **Zapisz**. Teraz zostanie wyświetlony interfejs wdrożenia szablonu portalu. Zwróć uwagę na dwa parametry zdefiniowane w szablonie.
1. Wprowadź lub wybierz wartości właściwości:

    - **Subskrypcja**: wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** i nadaj jej nazwę.
    - **Lokalizacja**: Wybierz lokalizację platformy Azure.
    - **Typ konta magazynu**: Użyj wartości domyślnej.
    - **Lokalizacja**: Użyj wartości domyślnej.
    - **Wyrażam zgodę na powyższe warunki i postanowienia**: (wybierz)

1. Wybierz pozycję **Kup**.

## <a name="next-steps"></a>Następne kroki

- Aby wyświetlić dzienniki inspekcji, zobacz [operacje inspekcji z Menedżer zasobów](./resource-group-audit.md).
- Aby rozwiązać problemy z błędami wdrażania, zobacz [Wyświetlanie operacji wdrażania](./resource-manager-deployment-operations.md).
- Aby wyeksportować szablon z wdrożenia lub grupy zasobów, zobacz [eksportowanie Azure Resource Manager szablonów](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Aby bezpiecznie wdrożyć usługę w wielu regionach, zobacz [Azure Menedżer wdrażania](./deployment-manager-overview.md).
