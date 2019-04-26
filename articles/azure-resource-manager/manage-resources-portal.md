---
title: Zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: Użyj witryny Azure portal i usługi Azure Resource Manager do zarządzania zasobami.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550755"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal

Dowiedz się, jak używać [witryny Azure portal](https://portal.azure.com) z [usługi Azure Resource Manager](resource-group-overview.md) do zarządzania zasobami platformy Azure. Aby zarządzać grupami zasobów, zobacz [Zarządzanie grup zasobów platformy Azure przy użyciu witryny Azure portal](./manage-resource-groups-portal.md).

Inne artykuły dotyczące zarządzania zasobami:

- [Zarządzanie zasobami platformy Azure przy użyciu wiersza polecenia platformy Azure](./manage-resources-cli.md)
- [Zarządzanie zasobami platformy Azure przy użyciu programu Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Wdrażanie zasobów w grupie zasobów

Po utworzeniu szablonu usługi Resource Manager, można użyć witryny Azure portal do wdrażania zasobów platformy Azure. W celu tworzenia szablonu, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md). Aby wdrażanie szablonu przy użyciu portalu, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure portal](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Otwarte zasoby

Zasoby platformy Azure są zorganizowane przez usługi platformy Azure i grup zasobów. Poniższe procedury pokazuje, jak otworzyć konto magazynu o nazwie **mystorage0207**. Maszyna wirtualna znajduje się w grupie zasobów o nazwie **mystorage0207rg**.

Aby otworzyć zasób przez typ usługi:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W okienku po lewej stronie wybierz usługę platformy Azure. W tym przypadku **kont magazynu**.  Jeśli nie widzisz usługi na liście, wybierz **wszystkich usług**, a następnie wybierz typ usługi.

    ![Otwórz zasób platformy azure w portalu](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Wybierz zasób, który chcesz otworzyć.

    ![Otwórz zasób platformy azure w portalu](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Konto usługi storage wygląda następująco:

    ![Otwórz zasób platformy azure w portalu](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Aby otworzyć grupę zasobów, zasobów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W okienku po lewej stronie wybierz **grup zasobów** Aby wyświetlić listę zasobów w grupie.
3. Wybierz zasób, który chcesz otworzyć. 

## <a name="manage-resources"></a>Zarządzanie zasobami

Podczas przeglądania zasobu w portalu, zobaczysz opcje służące do zarządzania tego zasobu.

![Zarządzanie zasobami platformy Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Zrzut ekranu przedstawia opcje zarządzania na maszynie wirtualnej platformy Azure. Można wykonać operacji, takich jak uruchamianie, ponowne uruchamianie i zatrzymywanie maszyny wirtualnej.

## <a name="delete-resources"></a>Usuwanie zasobów

1. Otwórz zasób w portalu. Aby uzyskać instrukcje, zobacz [Otwieranie zasobów](#open-resources).
2. Wybierz pozycję **Usuń**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla maszyny wirtualnej.

    ![Usuwanie zasobów platformy azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Wpisz nazwę zasobu, aby potwierdzić usunięcie, a następnie wybierz **Usuń**.

Aby uzyskać więcej informacji o jak usługi Azure Resource Manager porządkuje usuwania zasobów, zobacz [Usuwanie grupy zasobów usługi Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Przenoszenie zasobów

1. Otwórz zasób w portalu. Aby uzyskać instrukcje, zobacz [Otwieranie zasobów](#open-resources).
2. Wybierz **przenieść**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla konta magazynu.

    ![Przenoszenie zasobów platformy azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Wybierz **przenieść do innej grupy zasobów** lub **Moeve do innej subskrypcji** w zależności od potrzeb.

Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resources"></a>Blokowanie zasobów

Zablokowanie uniemożliwia innym użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie krytyczne zasoby, takie jak subskrypcji platformy Azure, grupę zasobów lub zasobu. 

1. Otwórz zasób w portalu. Aby uzyskać instrukcje, zobacz [Otwieranie zasobów](#open-resources).
2. Wybierz **blokuje**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla konta magazynu.

    ![informacje o zasobie blokady platformy azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Wybierz **Dodaj**, a następnie określ właściwości blokady.

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resources"></a>Tagowanie zasobów

Znakowanie pozwala logicznie organizowania swoją grupę zasobów i zasobów. 

1. Otwórz zasób w portalu. Aby uzyskać instrukcje, zobacz [Otwieranie zasobów](#open-resources).
2. Wybierz pozycję **Tagi**. Poniższy zrzut ekranu przedstawia opcje zarządzania dla konta magazynu.

    ![Tag usługi azure resource](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Określ właściwości etykiety, a następnie wybierz pozycję **Zapisz**.

Aby uzyskać informacje, zobacz [porządkowanie zasobów na platformie Azure za pomocą tagów](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Monitorowanie zasobów

Podczas otwierania zasobu w portalu jest wyświetlany domyślny wykresów i tabel do monitorowania tego typu zasobu. Poniższy zrzut ekranu przedstawia wykresów dla maszyny wirtualnej:

![Usługa azure Monitor zasobów](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Możesz wybrać ikonę pinezki w prawym górnym rogu wykresu, aby przypiąć wykres do pulpitu nawigacyjnego. Aby dowiedzieć się więcej o pracy z pulpitów nawigacyjnych, zobacz [tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Zarządzanie dostępem do zasobów

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby poznać usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](./resource-group-overview.md).
- Aby dowiedzieć się składni szablonu usługi Resource Manager, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](./resource-group-authoring-templates.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić schematy szablonów usługi Azure Resource Manager, zobacz [odwołanie do szablonu](/azure/templates/).