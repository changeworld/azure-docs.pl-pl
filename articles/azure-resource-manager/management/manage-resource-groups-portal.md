---
title: Zarządzanie grupami zasobów — Witryna Azure portal
description: Użyj witryny Azure Portal do zarządzania grupami zasobów za pośrednictwem usługi Azure Resource Manager. Pokazuje, jak tworzyć, listy i usuwać grupy zasobów.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 622c0cb8d58dddcdb13c7dc9412ada3f2e2d93e4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632988"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Zarządzanie grupami zasobów usługi Azure Resource Manager przy użyciu portalu Azure

Dowiedz się, jak korzystać z [witryny Azure Portal](https://portal.azure.com) za pomocą [usługi Azure Resource Manager](overview.md) do zarządzania grupami zasobów platformy Azure. Aby zarządzać zasobami platformy Azure, zobacz [Zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal](manage-resources-portal.md).

Inne artykuły dotyczące zarządzania grupami zasobów:

- [Zarządzanie grupami zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resources-cli.md)
- [Zarządzanie grupami zasobów platformy Azure przy użyciu programu Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Co to jest grupa zasobów

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Ogólnie rzecz biorąc należy dodać zasoby, które współużytkują ten sam cykl życia do tej samej grupy zasobów, dzięki czemu można je łatwo wdrożyć, zaktualizować i usunąć jako grupę.

Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.


## <a name="create-resource-groups"></a>Tworzenie grup zasobów

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybieranie **grup zasobów**

    ![dodawanie grupy zasobów](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Wybierz pozycję **Dodaj**.
4. Wprowadź następujące wartości:

   - **Subskrypcja**: wybierz subskrypcję platformy Azure. 
   - **Grupa zasobów**: Wprowadź nową nazwę grupy zasobów. 
   - **Region**: Wybierz lokalizację platformy Azure, taką jak **Central US**.

     ![tworzenie grupy zasobów](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Wybierz **pozycję Recenzja + Utwórz**
6. Wybierz **pozycję Utwórz**. Utworzenie grupy zasobów zajmuje kilka sekund.
7. Wybierz **polecenie Odśwież** z górnego menu, aby odświeżyć listę grup zasobów, a następnie wybierz nowo utworzoną grupę zasobów, aby ją otworzyć. Lub wybierz **powiadomienie**(ikonę dzwonka) u góry, a następnie wybierz pozycję **Przejdź do grupy zasobów, aby** otworzyć nowo utworzoną grupę zasobów

    ![przejdź do grupy zasobów](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Lista grup zasobów

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Aby wyświetlić listę grup zasobów, wybierz pozycję **Grupy zasobów**

    ![przeglądanie grup zasobów](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Aby dostosować informacje wyświetlane dla grup zasobów, wybierz pozycję **Edytuj kolumny**. Poniższy zrzut ekranu przedstawia kolumny dodawania, które można dodać do ekranu:

## <a name="open-resource-groups"></a>Otwarte grupy zasobów

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz pozycję **Grupy zasobów**.
3. Wybierz grupę zasobów, którą chcesz otworzyć.

## <a name="delete-resource-groups"></a>Usuwanie grup zasobów

1. Otwórz grupę zasobów, którą chcesz usunąć.  Zobacz [Otwarte grupy zasobów](#open-resource-groups).
2. Wybierz pozycję **Usuń grupę zasobów**.

    ![usuwanie grupy zasobów platformy Azure](./media/manage-resource-groups-portal/delete-group.png)

Aby uzyskać więcej informacji o tym, jak usługa Azure Resource Manager zamawia usuwanie zasobów, zobacz [Usuwanie grupy zasobów usługi Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources-to-a-resource-group"></a>Wdrażanie zasobów w grupie zasobów

Po utworzeniu szablonu Usługi Resource Manager można użyć witryny Azure Portal, aby wdrożyć zasoby platformy Azure. Aby utworzyć szablon, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](../templates/quickstart-create-templates-use-the-portal.md). Aby wdrożyć szablon przy użyciu portalu, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i witryny Azure Portal](../templates/deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Przejście do innej grupy zasobów lub subskrypcji

Zasoby w grupie można przenieść do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resource-groups"></a>Blokowanie grup zasobów

Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usunięcie lub zmodyfikowanie krytycznych zasobów, takich jak subskrypcja platformy Azure, grupa zasobów lub zasób. 

1. Otwórz grupę zasobów, którą chcesz usunąć.  Zobacz [Otwarte grupy zasobów](#open-resource-groups).
2. W lewym okienku wybierz pozycję **Blokady**.
3. Aby dodać blokadę do grupy zasobów, wybierz pozycję **Dodaj**.
4. Wprowadź **nazwę blokady,** **typ blokady**i **notatki**. Typy blokad obejmują tylko **do odczytu**i **usuń**.

    ![blokowanie grupy zasobów platformy Azure](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Aby uzyskać więcej informacji, zobacz [Blokowanie zasobów, aby zapobiec nieoczekiwanym zmianom](lock-resources.md).

## <a name="tag-resource-groups"></a>Oznaczanie grup zasobów

Znaczniki można stosować do grup zasobów i zasobów, aby logicznie organizować zasoby. Aby uzyskać więcej informacji, zobacz [Organizowanie zasobów platformy Azure za pomocą tagów](tag-resources.md#portal).

## <a name="export-resource-groups-to-templates"></a>Eksportowanie grup zasobów do szablonów

Aby uzyskać informacje dotyczące eksportowania szablonów, zobacz [Eksport pojedynczych i wielosóbowych do szablonu — Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Zarządzanie dostępem do grup zasobów

[Kontrola dostępu oparta na rolach (RBAC)](../../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o usłudze Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](overview.md).
- Aby poznać składnię szablonu Menedżera zasobów, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](../templates/template-syntax.md).
- Aby dowiedzieć się, jak tworzyć szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić schematy szablonów usługi Azure Resource Manager, zobacz [odwołanie do szablonu](/azure/templates/).
