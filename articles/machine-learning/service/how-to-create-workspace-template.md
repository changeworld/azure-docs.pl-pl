---
title: Tworzenie obszaru roboczego przy użyciu szablonu Azure Resource Manager
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak utworzyć nowy obszar roboczy usługi Azure Machine Learning przy użyciu szablonu Azure Resource Manager.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 89c24512e50dedbf10d145088ec77c2e6e303d1e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873165"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Tworzenie obszaru roboczego dla usługi Azure Machine Learning przy użyciu szablonu Azure Resource Manager

W tym artykule przedstawiono kilka sposobów tworzenia obszaru roboczego usługi Azure Machine Learning przy użyciu szablonów Azure Resource Manager. Szablon Menedżer zasobów ułatwia tworzenie zasobów jako jednej, skoordynowanej operacji. Szablon to dokument JSON, który definiuje zasoby, które są zbędne dla wdrożenia. Może również określać parametry wdrożenia. Parametry są używane do udostępniania wartości wejściowych przy użyciu szablonu.

Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcji platformy Azure**. Jeśli go nie masz, wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning Service](https://aka.ms/AMLFree).

* Aby użyć szablonu z interfejsu wiersza polecenia, musisz mieć [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) lub [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Następujący szablon Menedżer zasobów może służyć do tworzenia obszaru roboczego usługi Azure Machine Learning i skojarzonych zasobów platformy Azure:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Ten szablon umożliwia utworzenie następujących usług platformy Azure:

* Grupa zasobów platformy Azure
* Konto magazynu platformy Azure
* W usłudze Azure Key Vault
* Azure Application Insights
* Rejestr kontenerów platformy Azure
* Obszar roboczy usługi Azure Machine Learning

Grupa zasobów to kontener, w którym znajdują się usługi. Różne usługi są wymagane przez obszar roboczy Azure Machine Learning.

Przykładowy szablon ma dwa parametry:

* **Lokalizacja** , w której zostaną utworzone grupy zasobów i usługi.

    Szablon będzie korzystać z lokalizacji wybranej dla większości zasobów. Wyjątkiem jest usługa Application Insights, która nie jest dostępna we wszystkich lokalizacjach, w których znajdują się inne usługi. W przypadku wybrania lokalizacji, w której jest ona niedostępna, usługa zostanie utworzona w lokalizacji Południowo-środkowe stany USA.

* **Nazwa obszaru roboczego**, która jest przyjazną nazwą obszaru roboczego Azure Machine Learning.

    Nazwy innych usług są generowane losowo.

Aby uzyskać więcej informacji na temat szablonów, zobacz następujące artykuły:

* [Tworzenie szablonów Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Wdrażanie aplikacji za pomocą szablonów Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Typy zasobów Microsoft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Wykonaj kroki opisane w sekcji [wdrażanie zasobów z szablonu niestandardowego](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Po nadejściu ekranu __Edytuj szablon__ wklej szablon z tego dokumentu.
1. Wybierz pozycję __Zapisz__ , aby użyć szablonu. Podaj następujące informacje i zaakceptuj wymienione warunki i postanowienia:

   * Subskrypcja: Wybierz subskrypcję platformy Azure, która ma być używana dla tych zasobów.
   * Grupa zasobów: Wybierz lub Utwórz grupę zasobów, w której mają być zawarte usługi.
   * Nazwa obszaru roboczego: Nazwa do użycia dla obszaru roboczego Azure Machine Learning, który zostanie utworzony. Nazwa obszaru roboczego musi zawierać od 3 do 33 znaków. Może zawierać tylko znaki alfanumeryczne i znak "-".
   * Lokalizacja: Wybierz lokalizację, w której zostaną utworzone zasoby.

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów z szablonu niestandardowego](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

W tym przykładzie przyjęto założenie, że szablon został zapisany w `azuredeploy.json` pliku o nazwie w bieżącym katalogu:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) i [wdrażanie prywatnego szablonu Menedżer zasobów z tokenem SAS i Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W tym przykładzie przyjęto założenie, że szablon został zapisany w `azuredeploy.json` pliku o nazwie w bieżącym katalogu:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md) oraz [wdrażanie szablonu prywatnego Menedżer zasobów z tokenem SAS i interfejsem wiersza polecenia platformy Azure](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault zasad dostępu i szablonów Azure Resource Manager

Gdy używasz szablonu Azure Resource Manager do tworzenia obszaru roboczego i skojarzonych zasobów (w tym Azure Key Vault), wiele razy. Przykładowo wielokrotne użycie szablonu z tymi samymi parametrami w ramach potoku ciągłej integracji i wdrażania.

Większość operacji tworzenia zasobów za pomocą szablonów to idempotentne, ale Key Vault czyści zasady dostępu za każdym razem, gdy szablon jest używany. Wyczyszczenie zasad dostępu powoduje przerwanie dostępu do Key Vault wszystkich istniejących obszarów roboczych, które go używają. Na przykład funkcje zatrzymywania/tworzenia maszyny wirtualnej Azure Notebooks mogą zakończyć się niepowodzeniem.  

Aby uniknąć tego problemu, zalecamy zastosowanie jednej z następujących metod:

*  Nie Wdrażaj szablonu więcej niż raz dla tych samych parametrów. Lub Usuń istniejące zasoby przed użyciem szablonu, aby utworzyć je ponownie.
  
* Przejrzyj zasady dostępu Key Vault a następnie użyj tych zasad, aby ustawić właściwość accessPolicies szablonu.
* Sprawdź, czy zasób Key Vault już istnieje. Jeśli tak, nie należy go ponownie tworzyć za pomocą szablonu. Na przykład Dodaj parametr, który umożliwia wyłączenie tworzenia zasobu Key Vault, jeśli już istnieje.

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Menedżer zasobów interfejsu API REST](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
