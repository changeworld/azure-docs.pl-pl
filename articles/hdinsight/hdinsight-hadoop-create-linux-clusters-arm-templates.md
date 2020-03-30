---
title: Tworzenie klastrów Apache Hadoop przy użyciu szablonów — Azure HDInsight
description: Dowiedz się, jak tworzyć klastry dla usługi HDInsight przy użyciu szablonów Menedżera zasobów
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 9498f2cf56f0bfe20d0806e5dc9872403dabb180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979108"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów Menedżera zasobów

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

W tym artykule dowiesz się kilka sposobów tworzenia klastrów usługi Azure HDInsight przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Wdrażanie aplikacji za pomocą szablonu usługi Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md). Aby dowiedzieć się więcej o innych narzędziach i funkcjach tworzenia klastra, kliknij selektor kart u góry tej strony lub zobacz [Metody tworzenia klastra](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Subskrypcja platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Narzędzie Azure PowerShell i/lub interfejsu wiersza polecenia platformy Azure.

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Szablon Menedżera zasobów ułatwia tworzenie następujących zasobów dla aplikacji w jednej, skoordynowanej operacji:
* Klastry HDInsight i ich zasoby zależne (takie jak domyślne konto magazynu).
* Inne zasoby (takie jak Azure SQL Database do [używania Apache Sqoop](https://sqoop.apache.org/)).

W szablonie można zdefiniować zasoby, które są potrzebne dla aplikacji. Należy również określić parametry wdrażania do wartości wejściowych dla różnych środowisk. Szablon składa się z JSON i wyrażeń, które można utworzyć do konstruowania wartości dla wdrożenia.

Przykłady szablonów usługi HDInsight można znaleźć w [szablonach szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Użyj wieloplatformowego [programu Visual Studio Code](https://code.visualstudio.com/#alt-downloads) z [rozszerzeniem Menedżera zasobów](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) lub edytorem tekstu, aby zapisać szablon w pliku na stacji roboczej.

Aby uzyskać więcej informacji na temat szablonów Menedżera zasobów, zobacz następujące artykuły i przykłady:

* [Szablony usługi Azure Resource Manager autora](../azure-resource-manager/templates/template-syntax.md)
* [Wdrażanie aplikacji za pomocą szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* Odwołanie do szablonu [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)
* [Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Generowanie szablonów

Menedżer zasobów umożliwia eksportowanie szablonu Menedżera zasobów z istniejących zasobów w ramach subskrypcji przy użyciu różnych narzędzi. Możesz użyć wygenerowanego szablonu, aby dowiedzieć się więcej o składni szablonu lub aby zautomatyzować ponowne wdrożenie rozwiązania, w razie potrzeby. Aby uzyskać więcej informacji, zobacz [Eksportowanie szablonów](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Wdrażanie przy użyciu portalu

Szablon Usługi Resource Manager można wdrożyć za pomocą witryny Azure portal. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów z szablonu niestandardowego](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Szablon Usługi Resource Manager można wdrożyć przy użyciu programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i szablonu](../azure-resource-manager/templates/deploy-powershell.md) prywatnego menedżera zasobów usługi Azure PowerShell i [wdrażanie go za pomocą tokena Sygnatury dostępu Współdzielonego i programu Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Szablon Usługi Resource Manager można wdrożyć przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Menedżera zasobów i szablonu interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md) oraz [wdrażania prywatnego szablonu Menedżera zasobów z tokenem Sygnatury dostępu Współdzielonego i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST

Szablon Menedżera zasobów można wdrożyć przy użyciu interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Menedżera zasobów i interfejsu API REST Menedżera zasobów](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Wdrażanie za pomocą programu Visual Studio

 Użyj programu Visual Studio, aby utworzyć projekt grupy zasobów i wdrożyć go na platformie Azure za pośrednictwem interfejsu użytkownika. Można wybrać typ zasobów do uwzględnienia w projekcie. Zasoby te są automatycznie dodawane do szablonu Menedżera zasobów. Projekt udostępnia również skrypt programu PowerShell do wdrożenia szablonu.

Aby zapoznać się z wprowadzeniem do korzystania z programu Visual Studio z grupami zasobów, zobacz [Tworzenie i wdrażanie grup zasobów platformy Azure za pośrednictwem programu Visual Studio.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Następne kroki

W tym artykule poznaliśmy kilka sposobów tworzenia klastra HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* Aby uzyskać więcej szablonów związanych z programem HDInsight, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Na przykład wdrażania zasobów za pośrednictwem biblioteki klienta platformy .NET zobacz [Wdrażanie zasobów przy użyciu bibliotek .NET i szablonu](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aby uzyskać szczegółowy przykład wdrażania aplikacji, zobacz [udostępnianie i wdrażanie mikrousług przewidywalnie na platformie Azure.](../app-service/deploy-complex-application-predictably.md)
* Aby uzyskać wskazówki dotyczące wdrażania rozwiązania w różnych środowiskach, zobacz [Development and test environments in Microsoft Azure](../solution-dev-test-environments.md) (Środowiska projektowe i testowe na platformie Microsoft Azure).
* Aby dowiedzieć się więcej o sekcjach szablonu usługi Azure Resource Manager, zobacz [Szablony tworzenia](../azure-resource-manager/templates/template-syntax.md).
* Aby uzyskać listę funkcji, których można używać w szablonie usługi Azure Resource Manager, zobacz [Funkcje szablonu](../azure-resource-manager/templates/template-functions.md).
