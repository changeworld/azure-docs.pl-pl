---
title: Wdrażanie zagnieżdżonych środowisk szablonu usługi Resource Manager w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć zagnieżdżonych szablonów usługi Azure Resource Manager umożliwia uzyskanie środowisk przy użyciu usługi Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65835288"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Wdrażanie zagnieżdżonych szablonów usługi Azure Resource Manager dla środowisk testowych
Wdrożenia zagnieżdżonego pozwala wykonać inne szablony usługi Azure Resource Manager z głównego szablonu usługi Resource Manager. Pozwala ona do rozkładania wdrożenia do zestawu szablonów docelowych i określonego celu. Zapewnia korzyści w zakresie testowania, ponowne użycie i czytelności. Artykuł [podczas wdrażania zasobów platformy Azure, za pomocą połączonymi szablonami](../azure-resource-manager/resource-group-linked-templates.md) zapewnia dobre omówienie tego rozwiązania kilka przykładów kodu. W tym artykule przedstawiono przykład, które są specyficzne dla usługi Azure DevTest Labs. 

## <a name="key-parameters"></a>Najważniejsze parametry
Szablon usługi Resource Manager można tworzyć od podstaw, firma Microsoft zaleca użycie [projekt grupy zasobów platformy Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) w programie Visual Studio, która ułatwia programowanie i debugowanie szablonów. Po dodaniu zasobu zagnieżdżonego wdrożenia do azuredeploy.json, Visual Studio dodaje kilka elementów, aby szablon był bardziej elastyczne. Elementy te zawierają podfolder o pomocniczy plik szablon i parametry, nazwy zmiennych w pliku szablonu głównego i dwóch parametrów dla lokalizacji przechowywania dla nowych plików. **_ArtifactsLocation** i **_artifactsLocationSasToken** są kluczowe parametry, których używa usługi DevTest Labs. 

Jeśli nie znasz, jak działa usługa DevTest Labs przy użyciu środowisk, zobacz [tworzenie środowisk z wieloma Maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md). Szablony są przechowywane w repozytorium, połączone z laboratorium w usłudze DevTest Labs. Podczas tworzenia nowego środowiska za pomocą tych szablonów, pliki zostaną przeniesione do kontenera usługi Azure Storage w środowisku laboratoryjnym. Aby można było zidentyfikować i skopiuj zagnieżdżone pliki, DevTest Labs identyfikuje parametry _artifactsLocation i _artifactsLocationSasToken i kopiuje podfoldery do kontenera magazynu. Następnie automatycznie wstawia lokalizacja i sygnatura dostępu współdzielonego (SaS) token do parametrów. 

## <a name="nested-deployment-example"></a>Przykład wdrożenia zagnieżdżonego
Poniżej przedstawiono prosty przykład zagnieżdżonego wdrożenia:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

Folder w repozytorium zawierające ten szablon zawiera podfolder `nestedtemplates` z plikami **NestOne.json** i **NestOne.parameters.json**. W **azuredeploy.json**, identyfikator URI szablonu powstała przy użyciu lokalizacji artefaktów, folder zagnieżdżonych szablonów zagnieżdżonych nazwa pliku szablonu. Podobnie identyfikator URI dla parametrów została stworzona przy użyciu lokalizacji artefaktów, zagnieżdżonych szablonów folderu i pliku parametrów dla zagnieżdżonych szablonów. 

Oto obraz tę samą strukturę projektu w programie Visual Studio: 

![Struktura projektu w programie Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Możesz dodać dodatkowe foldery w folderze podstawowego, ale nie wszystkie głębiej niż jeden poziom. 

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, aby uzyskać szczegółowe informacje o środowiskach: 

- [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowiskach publicznych w usłudze Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Środowisko nawiązać połączenie z sieci wirtualnej w środowisku laboratoryjnym w usłudze Azure DevTest Labs](connect-environment-lab-virtual-network.md)