---
title: Wdrażanie zasobów w witrynie Azure portal
description: Użyj witryny Azure portal i zarządzania zasobami platformy Azure, aby wdrożyć zasoby w grupie zasobów w ramach subskrypcji.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: ea2faf810b3a5d1b8fa46575201022a501cc7d58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153441"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Wdrażanie zasobów za pomocą szablonów ARM i witryny Azure Portal

Dowiedz się, jak używać [witryny Azure Portal](https://portal.azure.com) za pomocą [szablonów usługi Azure Resource Manager (ARM)](overview.md) w celu wdrożenia zasobów platformy Azure. Aby dowiedzieć się więcej o zarządzaniu zasobami, zobacz [Zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal](../management/manage-resources-portal.md).

Wdrażanie zasobów platformy Azure przy użyciu witryny Azure portal zwykle obejmuje dwa kroki:

- Utwórz grupę zasobów.
- Wdrażanie zasobów w grupie zasobów.

Ponadto można również wdrożyć szablon ARM do tworzenia zasobów platformy Azure.

W tym artykule przedstawiono obie metody.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

1. Aby utworzyć nową grupę zasobów, wybierz **grupę zasobów** z [witryny Azure Portal](https://portal.azure.com).

   ![Wybieranie grup zasobów](./media/deploy-portal/select-resource-groups.png)

1. W obszarze Grupy zasobów wybierz pozycję **Dodaj**.

   ![Dodawanie grupy zasobów](./media/deploy-portal/add-resource-group.png)

1. Wybierz lub wprowadź następujące wartości właściwości:

    - **Subskrypcja**: Wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: Nadaj grupie zasobów nazwę.
    - **Region:** Określ lokalizację platformy Azure. W tym miejscu grupa zasobów przechowuje metadane dotyczące zasobów. Ze względu na zgodność można określić, gdzie te metadane są przechowywane. Ogólnie rzecz biorąc zaleca się określenie lokalizacji, w której będzie mieszkać większość zasobów. Korzystanie z tej samej lokalizacji może uprościć szablon.

   ![Ustawianie wartości grupy](./media/deploy-portal/set-group-properties.png)

1. Wybierz pozycję **Przegląd + utwórz**.
1. przejrzyj wartości, a następnie wybierz pozycję **Utwórz**.
1. Wybierz **pozycję Odśwież,** zanim będzie można wyświetlić nową grupę zasobów na liście.

## <a name="deploy-resources-to-a-resource-group"></a>Wdrażanie zasobów w grupie zasobów

Po utworzeniu grupy zasobów można wdrożyć zasoby w grupie z marketplace. Marketplace zawiera wstępnie zdefiniowane rozwiązania dla typowych scenariuszy.

1. Aby rozpocząć wdrażanie, wybierz pozycję **Utwórz zasób** z [witryny Azure Portal](https://portal.azure.com).

   ![Nowy zasób](./media/deploy-portal/new-resources.png)

1. Znajdź typ zasobu, który chcesz wdrożyć. Zasoby są zorganizowane w kategorie. Jeśli nie widzisz określonego rozwiązania, które chcesz wdrożyć, możesz go wyszukać w portalu Marketplace. Poniższy zrzut ekranu pokazuje, że wybrano serwer Ubuntu.

   ![Wybierz typ zasobu](./media/deploy-portal/select-resource-type.png)

1. W zależności od typu wybranego zasobu masz kolekcję odpowiednich właściwości do ustawienia przed wdrożeniem. Dla wszystkich typów należy wybrać docelową grupę zasobów. Na poniższej ilustracji pokazano, jak utworzyć maszynę wirtualną systemu Linux i wdrożyć ją w utworzonej grupie zasobów.

   ![Tworzenie grupy zasobów](./media/deploy-portal/select-existing-group.png)

   Alternatywnie można zdecydować o utworzeniu grupy zasobów podczas wdrażania zasobów. Wybierz **pozycję Utwórz nowy** i nadaj grupie zasobów nazwę.

1. Rozpocznie się wdrażanie. Wdrożenie może potrwać kilka minut. Niektóre zasoby zajmują więcej czasu niż inne zasoby. Po zakończeniu wdrażania pojawi się powiadomienie. Wybierz **pozycję Przejdź do zasobu,** aby otworzyć

   ![Wyświetl powiadomienie](./media/deploy-portal/view-notification.png)

1. Po wdrożeniu zasobów można dodać więcej zasobów do grupy zasobów, wybierając pozycję **Dodaj**.

   ![Dodawanie zasobu](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Wdrażanie zasobów z szablonu niestandardowego

Jeśli chcesz wykonać wdrożenie, ale nie używasz żadnych szablonów w portalu Marketplace, możesz utworzyć niestandardowy szablon definiujący infrastrukturę rozwiązania. Aby dowiedzieć się więcej o tworzeniu szablonów, zobacz [Opis struktury i składni szablonów ARM](template-syntax.md).

> [!NOTE]
> Interfejs portalu nie obsługuje odwoływania się do [klucza tajnego z magazynu kluczy.](key-vault-parameter.md) Zamiast tego użyj [programu PowerShell](deploy-powershell.md) lub [interfejsu wiersza polecenia platformy Azure,](deploy-cli.md) aby wdrożyć szablon lokalnie lub z zewnętrznego identyfikatora URI.

1. Aby wdrożyć dostosowany szablon za pośrednictwem portalu, wybierz pozycję **Utwórz zasób**, wyszukaj **szablon**. a następnie wybierz pozycję **Wdrażanie szablonu**.

   ![Wdrażanie szablonu wyszukiwania](./media/deploy-portal/search-template.png)

1. Wybierz **pozycję Utwórz**.
1. Dostępnych jest kilka opcji tworzenia szablonu:

    - **Zbuduj własny szablon w edytorze:** utwórz szablon za pomocą edytora szablonów portalu.  Edytor jest w stanie dodać schemat szablonu zasobu.
    - **Typowe szablony:** Istnieją cztery typowe szablony do tworzenia maszyny wirtualnej systemu Linux, maszyny wirtualnej systemu Windows, aplikacji sieci web i bazy danych SQL platformy Azure.
    - **Ładowanie szablonu szybkiego startu usługi GitHub**: używanie istniejących [szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/).

   ![Opcje widoku](./media/deploy-portal/see-options.png)

    Ten samouczek zawiera instrukcje dotyczące ładowania szablonu przewodnika Szybki start.

1. W obszarze **Załaduj szablon szybki start usługi GitHub**wpisz lub wybierz **101-storage-account-create**.

    Dostępne są dwie opcje:

    - **Wybierz szablon:** wdrożyć szablon.
    - **Edytuj szablon:** edytuj szablon przewodnika Szybki start przed jego wdrożeniem.

1. Wybierz **pozycję Edytuj szablon,** aby eksplorować edytor szablonów portalu. Szablon jest ładowany do edytora. Zwróć uwagę, że istnieją dwa parametry: **storageAccountType** i **lokalizacja**.

   ![Tworzenie szablonu](./media/deploy-portal/show-json.png)

1. Wprowadzać drobne zmiany w szablonie. Na przykład zaktualizuj zmienną **storageAccountName** do:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Wybierz **pozycję Zapisz**. Teraz zostanie wyświetlony interfejs wdrażania szablonu portalu. Zwróć uwagę na dwa parametry zdefiniowane w szablonie.
1. Wprowadź lub wybierz wartości właściwości:

    - **Subskrypcja**: Wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: Wybierz **pozycję Utwórz nowy** i nadaj nazwę.
    - **Lokalizacja:** Wybierz lokalizację platformy Azure.
    - **Typ konta magazynu:** Użyj wartości domyślnej.
    - **Lokalizacja:** Użyj wartości domyślnej.
    - **Wyrażam zgodę na powyższe warunki i postanowienia**: (wybierz)

1. Wybierz pozycję **Kup**.

## <a name="next-steps"></a>Następne kroki

- Aby wyświetlić dzienniki inspekcji, zobacz [Inspekcja operacji z Menedżerem zasobów](../management/view-activity-logs.md).
- Aby rozwiązać problemy z błędami wdrażania, zobacz [Wyświetlanie operacji wdrażania](deployment-history.md).
- Aby wyeksportować szablon z grupy wdrożeń lub zasobów, zobacz [Eksportowanie szablonów ARM](export-template-portal.md).
- Aby bezpiecznie wdrożyć usługę w wielu regionach, zobacz [Usługa Azure Deployment Manager](deployment-manager-overview.md).
