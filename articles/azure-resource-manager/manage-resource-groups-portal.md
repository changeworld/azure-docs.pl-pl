---
title: Zarządzanie grupami usługi Azure Resource Manager przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: Zarządzanie grupami usługi Azure Resource Manager za pomocą witryny Azure portal.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: cb1eb5ac27c53f4c0d48fe3644febc62f848486d
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484699"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Zarządzanie grupami zasobów usługi Azure Resource Manager przy użyciu witryny Azure portal

Dowiedz się, jak używać [witryny Azure portal](https://portal.azure.com) z [usługi Azure Resource Manager](resource-group-overview.md) na zarządzanie grupami zasobów platformy Azure. Aby zarządzać zasobami platformy Azure, zobacz [zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal](./manage-resources-portal.md).

Inne artykuły o zarządzaniu grupami zasobów:

- [Zarządzanie grupami zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure](./manage-resources-cli.md)
- [Zarządzanie grupami zasobów platformy Azure przy użyciu programu Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Co to jest grupa zasobów

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Ogólnie rzecz biorąc Dodaj zasoby, które współużytkują ten sam cykl życia w tej samej grupie zasobów, więc łatwe wdrażanie, aktualizowanie i usuwanie ich jako grupa.

Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

Grupa zasobów przechowuje metadane dotyczące zasobów. Po określeniu lokalizacji grupy zasobów, określasz, gdzie są przechowywane metadane.

## <a name="create-resource-groups"></a>Tworzenie grupy zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **grup zasobów**

    ![Dodaj grupę zasobów](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Wybierz pozycję **Dodaj**.
4. Wprowadź następujące wartości:

   - **Subskrypcja**: Wybierz swoją subskrypcję platformy Azure. 
   - **Grupa zasobów**: Wprowadź nazwę nowej grupy zasobów. 
   - **Region**: Wybierz lokalizację platformy Azure, taką jak **środkowe stany USA**.

     ![Utwórz grupę zasobów](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Wybierz **przeglądanie + tworzenie**
6. Wybierz pozycję **Utwórz**. Zajmuje kilka sekund, aby utworzyć grupę zasobów.
7. Wybierz **Odśwież** z górnego menu, aby odświeżyć listy grup zasobów, a następnie wybierz grupę nowo utworzonego zasobu, aby go otworzyć. Lub wybierz **powiadomień**(ikonę dzwonka) z góry, a następnie wybierz pozycję **przejdź do grupy zasobów** otworzyć nowo utworzonej grupy zasobów

    ![Przejdź do grupy zasobów](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Wyświetl listę grup zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Aby wyświetlić listę grup zasobów, wybierz **grup zasobów**

    ![Przeglądaj grupy zasobów](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Aby dostosować informacji wyświetlanych dla grup zasobów, wybierz opcję **Upravit sloupce**. Poniższy zrzut ekranu pokazuje dodanie kolumny można dodać do wyświetlenia:

## <a name="open-resource-groups"></a>Otwórz zasób grupy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Grupy zasobów**.
3. Wybierz grupę zasobów, który chcesz otworzyć.

## <a name="delete-resource-groups"></a>Usuwanie grupy zasobów

1. Otwórz grupę zasobów, które chcesz usunąć.  Zobacz [Otwórz grup zasobów](#open-resource-groups).
2. Wybierz pozycję **Usuń grupę zasobów**.

    ![Usuń grupę zasobów platformy azure](./media/manage-resource-groups-portal/delete-group.png)

Aby uzyskać więcej informacji o jak usługi Azure Resource Manager porządkuje usuwania zasobów, zobacz [Usuwanie grupy zasobów usługi Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Wdrażanie zasobów w grupie zasobów

Po utworzeniu szablonu usługi Resource Manager, można użyć witryny Azure portal do wdrażania zasobów platformy Azure. W celu tworzenia szablonu, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md). Aby wdrażanie szablonu przy użyciu portalu, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure portal](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Przenieś do innej grupy zasobów lub subskrypcji

Możesz przenieść zasoby w grupie, do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resource-groups"></a>Zablokuj grup zasobów

Zablokowanie uniemożliwia innym użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie krytyczne zasoby, takie jak subskrypcji platformy Azure, grupę zasobów lub zasobu. 

1. Otwórz grupę zasobów, które chcesz usunąć.  Zobacz [Otwórz grup zasobów](#open-resource-groups).
2. W okienku po lewej stronie wybierz **blokuje**.
3. Można dodać blokady do grupy zasobów, wybierz **Dodaj**.
4. Wprowadź **Nazwa blokady**, **zablokować typu**, i **uwagi**. Typy blokady obejmują **tylko do odczytu**, i **Usuń**.

    ![Grupa zasobów platformy azure blokady](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Aby uzyskać więcej informacji, zobacz [blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Tag grupy zasobów

Znaczniki można dodawać do grup zasobów i zasobów w celu logicznego uporządkowania Twoich zasobów. Aby uzyskać informacje, zobacz [porządkowanie zasobów na platformie Azure za pomocą tagów](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Eksportowanie grupy zasobów do szablonów

Po pomyślnym skonfigurowaniu grupy zasobów można wyświetlić szablon usługi Resource Manager dla grupy zasobów. Eksportowanie szablonu oferuje dwie korzyści:

- Zautomatyzuj przyszłych wdrożeń rozwiązania, ponieważ szablon zawiera całej infrastruktury.
- Dowiedz się składni szablonu, spoglądając na JavaScript Object Notation (JSON) reprezentujący rozwiązania.

Istnieją dwa sposoby eksportowania szablonu:

- Możesz wyeksportować prawdziwy Szablon użyty do wdrożenia. W wyeksportowanym szablonie wszystkie parametry i zmienne występują dokładnie tak, jak w oryginalnym szablonie. Takie podejście jest przydatne, jeśli zasoby zostały wdrożone za pośrednictwem portalu i chcesz zobaczyć szablon, na podstawie którego utworzono te zasoby. Ten szablon jest gotowy do użycia. 
- Możesz wyeksportować wygenerowany szablon, który reprezentuje bieżący stan grupy zasobów. Wyeksportowany szablon nie jest oparty na żadnym szablonie użytym do wdrożenia. Zamiast tego tworzy szablon, który jest "snapshot" lub "Kopia zapasowa" grupy zasobów. W wyeksportowanym szablonie zawartych jest wiele zakodowanych wartości i prawdopodobnie mniej parametrów, niż się zwykle definiuje. Użyj tej opcji, aby przeprowadzić ponowne wdrożenie zasobów w tej samej grupie zasobów. Aby użyć tego szablonu do innej grupy zasobów, może być znacznie go zmodyfikować.

### <a name="export-templates-from-deployment-history"></a>Eksportowanie szablonów z historii wdrożenia

Ta metoda umożliwia wyeksportowanie szablonów w przypadku niektórych wdrożeń. Jeśli zasoby zostały zmienione z portalu lub są dodawani lub usuwani zasobów w wielu wdrożeniach, zobacz [eksportowanie szablonów z grup zasobów](#export-templates-from-resource-groups).

1. Otwórz grupę zasobów, które chcesz wyeksportować.  Zobacz [Otwórz grup zasobów](#open-resource-groups).
2. W okienku po lewej stronie wybierz **wdrożeń**, lub wybierz link w obszarze **wdrożeń**.  Na poniższym zrzucie ekranu przedstawiono **4 Powodzenie** ponieważ wystąpiły cztery rozdzielonych wdrożeń w cztery nazwą innego wdrożenia. Może zostać wyświetlony **1 Powodzenie**.

    ![Szablony eksportowanie grupy zasobów platformy Azure](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history.png)

3. Wybierz jedno z wdrożeń z listy.
4. W okienku po lewej stronie wybierz **szablonu**. Usługa Resource Manager pobiera następujące sześć plików:

   - **Szablon** — szablon, który definiuje infrastrukturę Twojego rozwiązania. Po utworzeniu konta magazynu za pośrednictwem portalu usługa Resource Manager użyła szablonu w celu jego wdrożenia i zapisała ten szablon do użytku w przyszłości.
   - **Parametry** — plik parametrów, który służy do przekazywania wartości podczas wdrażania. Zawiera on wartości, które podano podczas pierwszego wdrażania. Podczas ponownego wdrażania szablonu można zmienić dowolne z tych wartości.
   - **Interfejs wiersza polecenia** — plik skryptu interfejsu wiersza polecenia platformy Azure, którego można użyć do wdrożenia szablonu.
   - **PowerShell** — plik skryptu programu Azure PowerShell, którego możesz użyć do wdrożenia szablonu.
   - **.NET** — klasa platformy .NET, której możesz użyć do wdrożenia szablonu.
   - **Ruby** — klasa języka Ruby, której możesz użyć do wdrożenia szablonu.

     Domyślnie portal zawiera szablon.

5. Wybierz **Pobierz** dotyczące eksportowania szablonu na komputerze lokalnym.

    ![Szablony eksportowanie grupy zasobów platformy Azure](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history-download.png)

<a name="export-templates-from-resource-groups"></a>
### <a name="export-templates-from-resource-groups"></a>Eksportowanie szablonów z grup zasobów

Jeśli zmieniono zasobów z poziomu portalu lub dodane/Usuń zasoby w wielu wdrożeniach, szablon pobrany z historii wdrożenia nie odzwierciedla bieżący stan grupy zasobów. W tej sekcji pokazano, jak wyeksportować szablon, który reprezentuje bieżący stan grupy zasobów. Jest on przeznaczony jako migawkę grupy zasobów, które służy do ponownego wdrożenia w tej samej grupie zasobów. Aby użyć wyeksportowanego szablonu do innych rozwiązań, należy go znacznie zmodyfikować.

1. Otwórz grupę zasobów, które chcesz wyeksportować.  Zobacz [Otwórz grup zasobów](#open-resource-groups).
2. W okienku po lewej stronie wybierz **Eksportuj szablon**. Usługa Resource Manager pobiera następujące sześć plików:

   - **Szablon** — szablon, który definiuje infrastrukturę Twojego rozwiązania. Po utworzeniu konta magazynu za pośrednictwem portalu usługa Resource Manager użyła szablonu w celu jego wdrożenia i zapisała ten szablon do użytku w przyszłości.
   - **Parametry** — plik parametrów, który służy do przekazywania wartości podczas wdrażania. Zawiera on wartości, które podano podczas pierwszego wdrażania. Podczas ponownego wdrażania szablonu można zmienić dowolne z tych wartości.
   - **Interfejs wiersza polecenia** — plik skryptu interfejsu wiersza polecenia platformy Azure, którego można użyć do wdrożenia szablonu.
   - **PowerShell** — plik skryptu programu Azure PowerShell, którego możesz użyć do wdrożenia szablonu.
   - **.NET** — klasa platformy .NET, której możesz użyć do wdrożenia szablonu.
   - **Ruby** — klasa języka Ruby, której możesz użyć do wdrożenia szablonu.

     Domyślnie portal zawiera szablon.
3. Wybierz **Pobierz** dotyczące eksportowania szablonu na komputerze lokalnym.

Niektóre wyeksportowanymi szablonami konieczne niektóre zmiany, zanim będzie można ich użyć. Aby dowiedzieć się, jak opracowywać szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).

### <a name="export-template-before-deploying"></a>Eksportowanie szablonu przed wdrożeniem

Aby zdefiniować zasób, można użyć portalu.  Przed wdrożeniem zasobu, można wyświetlać i eksportowania szablonu. Aby uzyskać instrukcje, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md).

### <a name="fix-export-issues"></a>Rozwiązywanie problemów z eksportowaniem

Nie wszystkie typy zasobów obsługują funkcję eksportowania szablonu. Wyświetlane są tylko eksportowanie problemy podczas eksportowania z grupy zasobów, a nie z historii wdrożenia. Jeśli ostatnie wdrożenie dokładnie reprezentuje bieżący stan grupy zasobów, należy wyeksportować szablon z historii wdrożenia — nie na podstawie grupy zasobów. Tylko wyeksportować z grupy zasobów, gdy wprowadzono zmiany w grupie zasobów, które nie są zdefiniowane w pojedynczym szablonie.

Aby rozwiązać problemy eksportu, należy ręcznie dodać brakujące zasoby do szablonu. Komunikat o błędzie zawiera typy zasobów, których nie można eksportować. Znajdź ten typ zasobów w [dokumentacji szablonu](/azure/templates/). Aby na przykład ręcznie dodać bramę sieci wirtualnej, zobacz [Microsoft.Network/virtualNetworkGateways template reference](/azure/templates/microsoft.network/virtualnetworkgateways) (Dokumentacja szablonu Microsoft.Network/virtualNetworkGateways). Odwołanie do szablonu zapewnia JSON, aby dodać nowy zasób do szablonu.

Po otrzymaniu w formacie JSON dla zasobu, musisz pobrać wartości zasobu. Widać wartości dla zasobu za pomocą operacji pobierania w interfejsie API REST dla typu zasobu. Na przykład, aby uzyskać wartości dla swojej bramy sieci wirtualnej, zobacz [bramy sieci wirtualnej — Pobierz](/rest/api/network-gateway/virtualnetworkgateways/get).

## <a name="manage-access-to-resource-groups"></a>Zarządzanie dostępem do grup zasobów

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby poznać usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](./resource-group-overview.md).
- Aby dowiedzieć się składni szablonu usługi Resource Manager, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](./resource-group-authoring-templates.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić schematy szablonów usługi Azure Resource Manager, zobacz [odwołanie do szablonu](/azure/templates/).