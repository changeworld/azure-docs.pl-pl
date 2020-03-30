---
title: Przenoszenie aplikacji siatki sieci szkieletowej usług do innego regionu
description: Zasoby siatki sieci szkieletowej usług można przenieść, wdrażając kopię bieżącego szablonu do nowego regionu platformy Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908165"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Przenoszenie aplikacji siatki sieci szkieletowej usług do innego regionu platformy Azure

W tym artykule opisano sposób przenoszenia aplikacji sieci szkieletowej usługi i jej zasobów do innego regionu platformy Azure. Zasoby można przenieść do innego regionu z wielu powodów. Na przykład w odpowiedzi na awarie, aby uzyskać funkcje lub usługi dostępne tylko w określonych regionach, aby spełnić wewnętrzne wymagania dotyczące zasad i nadzoru lub w odpowiedzi na wymagania dotyczące planowania zdolności produkcyjnych.

 [Usługa Mesh sieci szkieletowej nie obsługuje](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) możliwość bezpośredniego przenoszenia zasobów w regionach platformy Azure. Można jednak przenieść zasoby pośrednio, wdrażając kopię bieżącego szablonu usługi Azure Resource Manager do nowego regionu docelowego, a następnie przekierowując ruch przychodzący i zależności do nowo utworzonej aplikacji siatki sieci szkieletowej usług.

## <a name="prerequisites"></a>Wymagania wstępne

* Kontroler transferu danych przychodzących (na przykład [brama aplikacji),](https://docs.microsoft.com/azure/application-gateway/)który służy jako pośrednik routingu ruchu między klientami a aplikacją sieci szkieletowej usługi
* Dostępność siatki sieci szkieletowej usługi (wersja`westus`zapoznawcza) w docelowym regionie platformy Azure ( , `eastus`, lub `westeurope`)

## <a name="prepare"></a>Przygotowanie

1. Zrób "migawkę" bieżącego stanu aplikacji sieci szkieletowej usługi, eksportując szablon i parametry usługi Azure Resource Manager z najnowszego wdrożenia. Aby to zrobić, wykonaj kroki opisane w [szablonie Eksportuj po wdrożeniu](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) przy użyciu witryny Azure portal. Można również użyć [interfejsu API platformy Azure,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [programu Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)lub [interfejsu API REST](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate).

2. W stosownych przypadkach [wyeksportuj inne zasoby w tej samej grupie zasobów w](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group) celu ponownego rozmieszczenia w regionie docelowym.

3. Przejrzyj (i edytuj, w razie potrzeby) wyeksportowany szablon, aby upewnić się, że istniejące wartości właściwości są tymi, których chcesz użyć w regionie docelowym. Nowy `location` (region platformy Azure) jest parametrem, który zostanie podać podczas ponownego wdrożenia.

## <a name="move"></a>Move

1. Utwórz nową grupę zasobów (lub użyj istniejącej) w regionie docelowym.

2. Za pomocą wyeksportowanego szablonu wykonaj kroki opisane w obszarze [Wdrażanie zasobów z szablonu niestandardowego](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) przy użyciu witryny Azure portal. Można również użyć [interfejsu API platformy Azure,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli) [programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)lub [interfejsu API REST](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest).

3. Aby uzyskać wskazówki dotyczące przenoszenia powiązanych zasobów, takich jak [konta usługi Azure Storage,](../storage/common/storage-account-move.md)zapoznaj się ze wskazówkami dotyczącymi poszczególnych usług wymienionych w temacie [Przenoszenie zasobów platformy Azure między regionami.](../azure-resource-manager/management/move-region.md)

## <a name="verify"></a>Weryfikuj

1. Po zakończeniu wdrażania przetestuj punkt końcowy aplikacji, aby sprawdzić funkcjonalność aplikacji.

2. Stan aplikacji można również zweryfikować, sprawdzając stan aplikacji[(pokaz aplikacji az mesh)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)i przeglądając dzienniki aplikacji i[(az mesh code-package-log)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)za pomocą interfejsu [wiersza polecenia sieci szkieletowej usługi Azure.](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli)

## <a name="commit"></a>Zatwierdzenie

Po potwierdzeniu równoważnych funkcji aplikacji sieci szkieletowej usług w regionie docelowym skonfiguruj kontroler transferu danych przychodzących (na przykład [brama aplikacji),](../application-gateway/redirect-overview.md)aby przekierować ruch do nowej aplikacji.

## <a name="clean-up-source-resources"></a>Oczyszczanie zasobów źródłowych

Aby zakończyć przenoszenie aplikacji Siatki sieci szkieletowej usług, [usuń aplikację źródłową i/lub nadrzędną grupę zasobów](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Następne kroki

* [Przenoszenie zasobów platformy Azure między regionami](../azure-resource-manager/management/move-region.md)
* [Obsługa przenoszenia zasobów platformy Azure między regionami](../azure-resource-manager/management/region-move-support.md)
* [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Obsługa operacji przenoszenia dla zasobów](../azure-resource-manager/management/move-support-resources.md
)
