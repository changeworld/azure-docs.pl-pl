---
title: Zarządzanie zasobami — witryna Azure portal
description: Użyj witryny Azure portal i usługi Azure Resource Manager do zarządzania zasobami. Pokazuje, jak wdrażać i usuwać zasoby.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 00d260a74807774d5bf226c3ec00a6b84f93b8d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248348"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal

Dowiedz się, jak korzystać z [witryny Azure Portal](https://portal.azure.com) za pomocą [usługi Azure Resource Manager](overview.md) do zarządzania zasobami platformy Azure. Aby zarządzać grupami zasobów, zobacz [Zarządzanie grupami zasobów platformy Azure przy użyciu witryny Azure Portal](manage-resource-groups-portal.md).

Inne artykuły na temat zarządzania zasobami:

- [Zarządzanie zasobami platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resources-cli.md)
- [Zarządzanie zasobami platformy Azure przy użyciu programu Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Wdrażanie zasobów w grupie zasobów

Po utworzeniu szablonu Usługi Resource Manager można użyć witryny Azure Portal, aby wdrożyć zasoby platformy Azure. Aby utworzyć szablon, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](../templates/quickstart-create-templates-use-the-portal.md). Aby wdrożyć szablon przy użyciu portalu, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i witryny Azure Portal](../templates/deploy-portal.md).

## <a name="open-resources"></a>Otwarte zasoby

Zasoby platformy Azure są zorganizowane według usług platformy Azure i grup zasobów. Poniższe procedury pokazują, jak otworzyć konto magazynu o nazwie **mystorage0207**. Maszyna wirtualna znajduje się w grupie zasobów o nazwie **mystorage0207rg**.

Aby otworzyć zasób według typu usługi:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W lewym okienku wybierz usługę platformy Azure. W takim przypadku **konta magazynu**.  Jeśli nie widzisz usługi na liście, wybierz **pozycję Wszystkie usługi**, a następnie wybierz typ usługi.

    ![otwieranie zasobu platformy Azure w portalu](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Wybierz zasób, który chcesz otworzyć.

    ![otwieranie zasobu platformy Azure w portalu](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Konto magazynu wygląda następująco:

    ![otwieranie zasobu platformy Azure w portalu](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Aby otworzyć zasób według grupy zasobów:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W lewym okienku wybierz **pozycję Grupy zasobów,** aby wyświetlić listę zasobów w grupie.
3. Wybierz zasób, który chcesz otworzyć. 

## <a name="manage-resources"></a>Zarządzanie zasobami

Podczas wyświetlania zasobu w portalu są wyświetlane opcje zarządzania tym konkretnym zasobem.

![zarządzanie zasobami platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Zrzut ekranu przedstawia opcje zarządzania dla maszyny wirtualnej platformy Azure. Można wykonywać operacje, takie jak uruchamianie, ponowne uruchamianie i zatrzymywanie maszyny wirtualnej.

## <a name="delete-resources"></a>Usuwanie zasobów

1. Otwórz zasób w portalu. Aby zapoznać się z instrukcjami, zobacz [Otwieranie zasobów](#open-resources).
2. Wybierz pozycję **Usuń**. Poniższy zrzut ekranu przedstawia opcje zarządzania maszyną wirtualną.

    ![usuwanie zasobu platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Wpisz nazwę zasobu, aby potwierdzić usunięcie, a następnie wybierz pozycję **Usuń**.

Aby uzyskać więcej informacji o tym, jak usługa Azure Resource Manager zamawia usuwanie zasobów, zobacz [Usuwanie grupy zasobów usługi Azure Resource Manager](delete-resource-group.md).

## <a name="move-resources"></a>Przenoszenie zasobów

1. Otwórz zasób w portalu. Aby zapoznać się z instrukcjami, zobacz [Otwieranie zasobów](#open-resources).
2. Wybierz **pozycję Przenieś**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla konta magazynu.

    ![przenoszenie zasobu platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Wybierz **pozycję Przenieś do innej grupy zasobów** lub Przenieś do innej **subskrypcji** w zależności od potrzeb.

Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resources"></a>Blokowanie zasobów

Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usunięcie lub zmodyfikowanie krytycznych zasobów, takich jak subskrypcja platformy Azure, grupa zasobów lub zasób. 

1. Otwórz zasób w portalu. Aby zapoznać się z instrukcjami, zobacz [Otwieranie zasobów](#open-resources).
2. Wybierz pozycję **Blokady**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla konta magazynu.

    ![blokowanie zasobu platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Wybierz **pozycję Dodaj**, a następnie określ właściwości blokady.

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resources"></a>Tagowanie zasobów

Oznaczanie pomaga logicznie organizować grupę zasobów i zasoby. 

1. Otwórz zasób w portalu. Aby zapoznać się z instrukcjami, zobacz [Otwieranie zasobów](#open-resources).
2. Wybierz pozycję **Tagi**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla konta magazynu.

    ![tag zasobu platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Określ właściwości znacznika, a następnie wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji, zobacz [Organizowanie zasobów platformy Azure za pomocą tagów](tag-resources.md#portal).

## <a name="monitor-resources"></a>Monitorowanie zasobów

Po otwarciu zasobu portal przedstawia domyślne wykresy i tabele do monitorowania tego typu zasobu. Poniższy zrzut ekranu przedstawia wykresy dla maszyny wirtualnej:

![monitorowanie zasobu platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Możesz wybrać ikonę pinezki w prawym górnym rogu wykresów, aby przypiąć wykres do pulpitu nawigacyjnego. Aby dowiedzieć się więcej o pracy z pulpitami nawigacyjnymi, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Zarządzanie dostępem do zasobów

[Kontrola dostępu oparta na rolach (RBAC)](../../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o usłudze Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](overview.md).
- Aby poznać składnię szablonu Menedżera zasobów, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](../templates/template-syntax.md).
- Aby dowiedzieć się, jak tworzyć szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić schematy szablonów usługi Azure Resource Manager, zobacz [odwołanie do szablonu](/azure/templates/).
