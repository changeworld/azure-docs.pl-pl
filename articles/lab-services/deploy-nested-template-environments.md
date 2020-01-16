---
title: Wdrażanie zagnieżdżonych środowisk szablonów Menedżer zasobów w programie Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak wdrożyć zagnieżdżone szablony Azure Resource Manager, aby zapewnić środowiska z Azure DevTest Labs.
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
ms.openlocfilehash: 675d2c670f5bc11c1d8b61bc96313e408f788dc3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976550"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Wdróż zagnieżdżone szablony Azure Resource Manager dla środowisk testowych
Wdrożenie zagnieżdżone umożliwia wykonywanie innych szablonów Azure Resource Manager z poziomu szablonu Menedżer zasobów głównego. Pozwala to na rozmieszczenie wdrożenia w zestaw szablonów przeznaczonych dla konkretnych celów. Zapewnia ona korzyści z testowania, ponownego użycia i czytelności. W [przypadku wdrażania zasobów platformy Azure za pomocą połączonych szablonów](../azure-resource-manager/templates/linked-templates.md) znajduje się dobry przegląd tego rozwiązania z kilkoma przykładami kodu. W tym artykule przedstawiono przykład, który jest specyficzny dla Azure DevTest Labs. 

## <a name="key-parameters"></a>Parametry klucza
Chociaż możesz utworzyć własny szablon Menedżer zasobów od podstaw, zalecamy użycie [projektu grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) w programie Visual Studio, co ułatwia tworzenie i debugowanie szablonów. Po dodaniu zagnieżdżonego zasobu wdrożenia do azuredeploy. JSON program Visual Studio dodaje kilka elementów, aby uczynić szablon bardziej elastycznym. Te elementy obejmują podfolder z szablonem pomocniczym i plikiem parametrów, nazwami zmiennych w głównym pliku szablonu i dwa parametry lokalizacji przechowywania dla nowych plików. **_ArtifactsLocation** i **_artifactsLocationSasToken** są kluczowymi parametrami, z których korzysta DevTest Labs. 

Jeśli nie wiesz, jak współpracuje DevTest Labs z środowiskami, zobacz [Tworzenie środowisk wielu maszyn wirtualnych i zasobów PaaS przy użyciu szablonów Azure Resource Manager](devtest-lab-create-environment-from-arm.md). Szablony są przechowywane w repozytorium połączonym z laboratorium w DevTest Labs. Po utworzeniu nowego środowiska z tymi szablonami pliki są przenoszone do kontenera usługi Azure Storage w laboratorium. Aby można było identyfikować i kopiować pliki zagnieżdżone, DevTest Labs identyfikuje parametry _artifactsLocation i _artifactsLocationSasToken i kopiuje podfoldery do kontenera magazynu. Następnie automatycznie wstawia do parametrów lokalizację i token sygnatury dostępu współdzielonego (SaS). 

## <a name="nested-deployment-example"></a>Przykład zagnieżdżonego wdrożenia
Oto prosty przykład wdrożenia zagnieżdżonego:

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

Folder w repozytorium zawierającym ten szablon ma podfolder `nestedtemplates` z plikami **NestOne. JSON** i **NestOne. Parameters. JSON**. W pliku **azuredeploy. JSON**identyfikator URI dla szablonu jest tworzony przy użyciu lokalizacji artefaktów, zagnieżdżony folder szablonów, zagnieżdżona nazwa szablonu. Podobnie identyfikatory URI parametrów są kompilowane przy użyciu lokalizacji artefaktów, zagnieżdżonego folderu szablonów i pliku parametrów dla szablonu zagnieżdżonego. 

Poniżej znajduje się obraz tej samej struktury projektu w programie Visual Studio: 

![Struktura projektu w programie Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Możesz dodać dodatkowe foldery w folderze podstawowym, ale nie są one bardziej szczegółowe niż jeden poziom. 

## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje o środowiskach można znaleźć w następujących artykułach: 

- [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowisk publicznych w programie Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Podłącz środowisko do sieci wirtualnej laboratorium w Azure DevTest Labs](connect-environment-lab-virtual-network.md)