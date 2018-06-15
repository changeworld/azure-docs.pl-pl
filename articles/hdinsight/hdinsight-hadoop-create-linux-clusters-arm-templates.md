---
title: Tworzenie klastrów Hadoop przy użyciu szablonów - Azure HDInsight | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia klastrów dla usługi HDInsight przy użyciu szablonów usługi Resource Manager
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: ae5085eb52210844b364113a436fa033da9daac7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202357"
---
# <a name="create-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Tworzenie klastrów Hadoop w usłudze HDInsight przy użyciu szablonów usługi Resource Manager
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

W tym artykule dowiesz się kilka sposobów tworzenia klastrów Azure HDInsight za pomocą szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Informacje na temat innych funkcji i narzędzi tworzenia klastra, kliknij selektor karty w górnej części strony, lub zobacz [metody tworzenia klastrów](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Postępuj zgodnie z instrukcjami w tym artykule, potrzebne są:

* [Subskrypcji platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Program Azure PowerShell i/lub Azure CLI.

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager
Szablon usługi Resource Manager ułatwia tworzenie następujących resoruces aplikacji w jednej, skoordynowanej operacji:
* Klastry usługi HDInsight i ich zasoby zależne (takie jak domyślne konto magazynu)
* Inne zasoby (takie jak bazy danych SQL Azure do narzędzia Apache sqoop)

W szablonie można zdefiniować zasoby, które są wymagane dla aplikacji. Należy także określić parametry wdrożenia wprowadzanie wartości dla różnych środowisk. Szablon składa się z kodu JSON i wyrażeń, których można używać do tworzenia wartości dla danego wdrożenia.

Można znaleźć przykłady szablonu usługi HDInsight w [szablonów Szybki Start Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Użyj wielu platform [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) z [rozszerzenia usługi Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) lub edytora tekstu, aby zapisać szablon do pliku na stację roboczą. 

Aby uzyskać więcej informacji na temat szablonów usługi Resource Manager zobacz następujące artykuły:

* [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Wdrażanie aplikacji przy użyciu szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Generowanie szablonów

Menedżer zasobów umożliwia eksportowanie szablonu usługi Resource Manager z istniejących zasobów w ramach subskrypcji przy użyciu różnych narzędzi. Możesz użyć wygenerowanego szablonu, aby dowiedzieć się więcej o składni szablonu lub aby zautomatyzować ponowne wdrożenie rozwiązania, w razie potrzeby.

- Portalu Azure: zobacz [Eksportowanie szablonu usługi Azure Resource Manager z istniejących zasobów](../azure-resource-manager/resource-manager-export-template.md).
- Program Azure PowerShell: Zobacz [szablony eksportu usługi Azure Resource Manager przy użyciu programu PowerShell](../azure-resource-manager/resource-manager-export-template-powershell.md).
- Azure CLI: Zobacz [szablony eksportu usługi Azure Resource Manager z wiersza polecenia platformy Azure](../azure-resource-manager/resource-manager-export-template-cli.md).


## <a name="deploy-using-the-portal"></a>Wdrażanie przy użyciu portalu

Można wdrożyć szablonu usługi Resource Manager przy użyciu portalu Azure. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów z szablonu niestandardowego](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Można wdrożyć szablonu usługi Resource Manager przy użyciu programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) i [Wdróż prywatnej szablonu usługi Resource Manager z tokenu sygnatury dostępu Współdzielonego i programu Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia

Można wdrożyć szablonu usługi Resource Manager przy użyciu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [wdrożenie zasobów z szablonami usługi Resource Manager i interfejsu wiersza polecenia Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) i [Wdróż prywatnej szablonu usługi Resource Manager z tokenu sygnatury dostępu Współdzielonego i interfejsu wiersza polecenia Azure](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST
Można wdrożyć szablonu usługi Resource Manager przy użyciu interfejsu API REST. Aby uzyskać więcej informacji, zobacz [wdrożenie zasobów z szablonami usługi Resource Manager i interfejsu REST API usługi Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Wdrażanie za pomocą programu Visual Studio
 Tworzenie projektu grupy zasobów i wdrożyć go na platformie Azure za pomocą interfejsu użytkownika za pomocą programu Visual Studio. Należy wybrać typ zasobów do uwzględnienia w projekcie. Te zasoby są automatycznie dodawane do szablonu usługi Resource Manager. Projekt zapewnia również skrypt programu PowerShell do wdrożenia szablonu.

Aby obejrzeć wprowadzenie do korzystania z programu Visual Studio z grupami zasobów, zobacz [tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule uzyskanych kilka sposobów tworzenia klastra usługi HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* Dla usługi HDInsight więcej powiązanych szablony, zobacz [szablonów Szybki Start Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Na przykład wdrażania zasobów za pomocą biblioteki klienta .NET, zobacz [wdrażanie zasobów przy użyciu bibliotek .NET oraz szablonu](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Szczegółowy przykład wdrażania aplikacji, zobacz [udostępniania i wdrażanie mikrousług przewidywalnego na platformie Azure](../app-service/app-service-deploy-complex-application-predictably.md).
* Aby uzyskać wskazówki dotyczące wdrażania rozwiązania w różnych środowiskach, zobacz [Development and test environments in Microsoft Azure](../solution-dev-test-environments.md) (Środowiska projektowe i testowe na platformie Microsoft Azure).
* Informacje na temat części szablonu usługi Azure Resource Manager, zobacz [tworzenia szablonów](../azure-resource-manager/resource-group-authoring-templates.md).
* Aby uzyskać listę funkcji, można użyć w szablonie usługi Azure Resource Manager, zobacz [szablonu funkcji](../azure-resource-manager/resource-group-template-functions.md).
