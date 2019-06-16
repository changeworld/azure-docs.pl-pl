---
title: Szablon usługi Azure Resource Manager umożliwia tworzenie obszaru roboczego
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak utworzyć nowy obszar roboczy usługi Azure Machine Learning za pomocą szablonu usługi Azure Resource Manager.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: abe497ed96515e8194fb2ddefd8e7f4cb9908758
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205124"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Użyj szablonu usługi Azure Resource Manager utworzyć obszar roboczy usługi Azure Machine Learning

W tym artykule dowiesz się, Utwórz obszar roboczy usługi Azure Machine Learning, za pomocą szablonów usługi Azure Resource Manager na kilka sposobów. Szablon usługi Resource Manager ułatwia tworzenie zasobów jako jednej, skoordynowanej operacji. Szablon to dokument JSON, który definiuje zasoby, które są potrzebne do wdrożenia. Może on również określać parametrów wdrożenia. Parametry umożliwiają podawanie wartości wejściowych, korzystając z tego szablonu.

Aby uzyskać więcej informacji, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcji platformy Azure**. Jeśli nie masz, spróbuj [bezpłatnej i płatnej wersji usługi Azure Machine Learning](https://aka.ms/AMLFree).

* Aby użyć szablonu z interfejsu wiersza polecenia, musisz mieć albo [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Następujący szablon usługi Resource Manager może służyć do tworzenia obszaru roboczego usługi Azure Machine Learning i skojarzonych zasobów platformy Azure:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Ten szablon umożliwia utworzenie następujących usług platformy Azure:

* Grupa zasobów platformy Azure
* Konto usługi Azure Storage
* W usłudze Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Obszar roboczy usługi Azure Machine Learning

Grupa zasobów to kontener, który zawiera usługi. Różne usługi są wymagane przez obszar roboczy usługi Azure Machine Learning.

Przykładowy szablon zawiera dwa parametry:

* **Lokalizacji** której zostanie utworzona grupa zasobów i usług.

    Szablon będzie używać lokalizacji, którą wybierzesz dla większości zasobów. Wyjątkiem jest usługi Application Insights, która nie jest dostępna we wszystkich lokalizacjach, które są inne usługi. Wybranie lokalizacji, gdy nie są dostępne, usługa zostanie utworzony w lokalizacji południowo-środkowe stany USA.

* **Nazwa obszaru roboczego**, czyli przyjazną nazwę obszaru roboczego usługi Azure Machine Learning.

    Nazwy inne usługi są generowane losowo.

Aby uzyskać więcej informacji dotyczących szablonów zobacz następujące artykuły:

* [Tworzenie szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Wdrażanie aplikacji przy użyciu szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Typy zasobów Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Postępuj zgodnie z instrukcjami w [wdrażanie zasobów za pomocą szablonu niestandardowej](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Po przyjeździe do biura __Edytuj szablon__ ekranu, Wklej w szablonie z tego dokumentu.
1. Wybierz __Zapisz__ używać szablonu. Należy podać następujące informacje i zgody na liście warunków i postanowień:

   * Subskrypcja: Wybierz subskrypcję platformy Azure do użycia dla tych zasobów.
   * Grupa zasobów: Wybierz lub Utwórz grupę zasobów w celu uwzględnienia usług.
   * Nazwa obszaru roboczego: Nazwa do użycia dla obszaru roboczego usługi Azure Machine Learning, która zostanie utworzona. Nazwa obszaru roboczego musi być od 3 do 33 znaków. Może zawierać tylko znaki alfanumeryczne i "-".
   * Lokalizacja: Wybierz lokalizację, w którym zostanie utworzone zasoby.

     ![Parametry szablonu w witrynie Azure portal](media/how-to-create-workspace-template/template-parameters.png)

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonu niestandardowej](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

W tym przykładzie założono, że zapisano szablon do pliku o nazwie `azuredeploy.json` w bieżącym katalogu:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) i [wdrażanie prywatnego szablonu usługi Resource Manager przy użyciu tokenu sygnatury dostępu Współdzielonego i programu Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W tym przykładzie założono, że zapisano szablon do pliku o nazwie `azuredeploy.json` w bieżącym katalogu:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md) i [wdrażanie prywatnego szablonu usługi Resource Manager przy użyciu tokenu sygnatury dostępu Współdzielonego i wiersza polecenia platformy Azure](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu REST API usługi Resource Manager](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
