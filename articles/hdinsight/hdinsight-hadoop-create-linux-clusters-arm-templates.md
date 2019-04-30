---
title: Tworzenie klastrów usługi Apache Hadoop przy użyciu szablonów — Azure HDInsight
description: Dowiedz się, jak utworzyć klastry na HDInsight przy użyciu szablonów usługi Resource Manager
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 73402421a87d2cf14719ff34201890ea96c90519
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097426"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Tworzenie klastrów usługi Apache Hadoop w HDInsight przy użyciu szablonów usługi Resource Manager
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

W tym artykule dowiesz się kilka sposobów tworzenia klastrów usługi Azure HDInsight przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Aby dowiedzieć się więcej na temat innych narzędzi do tworzenia klastra i funkcji, kliknij selektor karty w górnej części tej strony, lub zobacz [metody tworzenia klastrów](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Postępuj zgodnie z instrukcjami w tym artykule, potrzebne są:

* [Subskrypcji platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Program Azure PowerShell i/lub Azure klasyczny interfejs wiersza polecenia.

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager
Szablon usługi Resource Manager ułatwia utworzenie następujących zasobów dla aplikacji w ramach jednej, skoordynowanej operacji:
* Klastry HDInsight i ich zasoby zależne (takie jak domyślne konto magazynu).
* Inne zasoby (takie jak Azure SQL Database, aby użyć [Apache Sqoop](https://sqoop.apache.org/)).

W szablonie należy zdefiniować zasoby, które są wymagane przez aplikację. Należy również określić parametrów wdrożenia wprowadzanie wartości dla różnych środowisk. Szablon składa się z kodu JSON i wyrażeń, których używasz do tworzenia wartości dla danego wdrożenia.

Można znaleźć przykłady szablonów HDInsight w [szablony szybkiego startu platformy](https://azure.microsoft.com/resources/templates/?term=hdinsight). Użyj dla wielu platform [programu Visual Studio Code](https://code.visualstudio.com/#alt-downloads) z [rozszerzenia usługi Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) lub edytorze tekstów, aby zapisać szablon do pliku na stację roboczą. 

Aby uzyskać więcej informacji na temat szablonów usługi Resource Manager zobacz następujące artykuły i przykłady:

* [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Wdrażanie aplikacji przy użyciu szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions) odwołanie do szablonu
* [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Generowanie szablonów

Usługi Resource Manager umożliwia wyeksportowanie szablonu usługi Resource Manager z istniejących zasobów w subskrypcji przy użyciu różnych narzędzi. Możesz użyć wygenerowanego szablonu, aby dowiedzieć się więcej o składni szablonu lub aby zautomatyzować ponowne wdrożenie rozwiązania, w razie potrzeby. Aby uzyskać więcej informacji, zobacz [eksportowanie szablonów](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="deploy-using-the-portal"></a>Wdrażanie przy użyciu portalu

Możesz wdrożyć szablon usługi Resource Manager przy użyciu witryny Azure portal. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonu niestandardowej](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Możesz wdrożyć szablon usługi Resource Manager przy użyciu programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) i [wdrażanie prywatnego szablonu usługi Resource Manager przy użyciu tokenu sygnatury dostępu Współdzielonego i programu Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

Możesz wdrożyć szablon usługi Resource Manager przy użyciu klasyczny interfejs wiersza polecenia. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) i [wdrażanie prywatnego szablonu usługi Resource Manager przy użyciu tokenu sygnatury dostępu Współdzielonego i wiersza polecenia platformy Azure](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST
Możesz wdrożyć szablon usługi Resource Manager przy użyciu interfejsu API REST. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu REST API usługi Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Wdrażanie za pomocą programu Visual Studio
 Tworzenie projektu grupy zasobów i wdrożyć ją na platformie Azure za pomocą interfejsu użytkownika, należy użyć programu Visual Studio. Należy wybrać typ zasobów do uwzględnienia w projekcie. Te zasoby są automatycznie dodawane do szablonu usługi Resource Manager. Projekt zapewnia również skrypt programu PowerShell, aby wdrożyć szablon.

Wprowadzenie do korzystania z programu Visual Studio z grupami zasobów, zobacz [tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule mają przedstawiono kilka sposobów, aby utworzyć klaster usługi HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* Aby uzyskać więcej HDInsight powiązanych szablonów, zobacz [szablony szybkiego startu platformy](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Aby uzyskać przykład wdrażanie zasobów za pomocą biblioteki klienckiej platformy .NET, zobacz [wdrażanie zasobów za pomocą szablonu i bibliotek .NET](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aby uzyskać szczegółowy przykład wdrażania aplikacji, zobacz [Aprowizowanie i wdrażanie mikrousług przewidywalny na platformie Azure](../app-service/deploy-complex-application-predictably.md).
* Aby uzyskać wskazówki dotyczące wdrażania rozwiązania w różnych środowiskach, zobacz [Development and test environments in Microsoft Azure](../solution-dev-test-environments.md) (Środowiska projektowe i testowe na platformie Microsoft Azure).
* Aby dowiedzieć się więcej na temat części szablonu usługi Azure Resource Manager, zobacz [Tworzenie szablonów](../azure-resource-manager/resource-group-authoring-templates.md).
* Aby uzyskać listę funkcji, można użyć w szablonie usługi Azure Resource Manager, zobacz [funkcje szablonu](../azure-resource-manager/resource-group-template-functions.md).
