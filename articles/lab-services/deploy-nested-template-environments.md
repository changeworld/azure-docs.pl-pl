---
title: Wdrażanie zagnieżdżonych środowisk szablonów w laboratoriach devtest platformy Azure
description: Dowiedz się, jak wdrożyć zagnieżdżone szablony usługi Azure Resource Manager w celu zapewnienia środowisk w laboratoriach devtest platformy Azure.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168818"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Wdrażanie zagnieżdżonych szablonów usługi Azure Resource Manager do testowania środowisk
Wdrożenie zagnieżdżone umożliwia wykonywanie innych szablonów usługi Azure Resource Manager z poziomu głównego szablonu Menedżera zasobów. Umożliwia rozkład wdrożenia do zestawu szablonów docelowych i specyficznych dla celu. Zapewnia korzyści w zakresie testowania, ponownego użycia i czytelności. Artykuł [Korzystanie z połączonych szablonów podczas wdrażania zasobów platformy Azure](../azure-resource-manager/templates/linked-templates.md) zawiera dobre omówienie tego rozwiązania z kilkoma przykładami kodu. Ten artykuł zawiera przykład, który jest specyficzny dla usługi Azure DevTest Labs. 

## <a name="key-parameters"></a>Kluczowe parametry
Chociaż można utworzyć własny szablon Menedżera zasobów od podstaw, zaleca się użycie [projektu Grupy zasobów platformy Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) w programie Visual Studio, co ułatwia tworzenie i debugowanie szablonów. Po dodaniu zagnieżdżonego zasobu wdrożenia do azuredeploy.json program Visual Studio dodaje kilka elementów, aby uczynić szablon bardziej elastycznym. Elementy te obejmują podfolder z pomocniczym plikiem szablonu i parametrów, nazwy zmiennych w głównym pliku szablonu i dwa parametry lokalizacji przechowywania nowych plików. **_artifactsLocation** i **_artifactsLocationSasToken** są kluczowe parametry, które devtest labs używa. 

Jeśli nie znasz sposobu, w jaki laboratoria DevTest współpracują ze środowiskami, zobacz [Tworzenie środowisk wielu maszyn wirtualnych i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager.](devtest-lab-create-environment-from-arm.md) Szablony są przechowywane w repozytorium połączonym z laboratorium w DevTest Labs. Podczas tworzenia nowego środowiska z tych szablonów, pliki są przenoszone do kontenera usługi Azure Storage w laboratorium. Aby móc identyfikować i kopiować zagnieżdżone pliki, DevTest Labs identyfikuje _artifactsLocation i _artifactsLocationSasToken parametry oraz kopiuje podfoldery do kontenera magazynu. Następnie automatycznie wstawia lokalizację i token sygnatury dostępu współdzielonego (SaS) do parametrów. 

## <a name="nested-deployment-example"></a>Przykład wdrożenia zagnieżdżonego
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

Folder w repozytorium zawierający ten szablon ma `nestedtemplates` podfolder z plikami **NestOne.json** i **NestOne.parameters.json**. W **pliku azuredeploy.json**identyfikator URI dla szablonu jest budowany przy użyciu lokalizacji artefaktów, zagnieżdżonego folderu szablonu, nazwy pliku szablonu zagnieżdżonego. Podobnie identyfikator URI dla parametrów jest zbudowany przy użyciu lokalizacji artefaktów, zagnieżdżonego folderu szablonu i pliku parametrów dla szablonu zagnieżdżonego. 

Oto obraz tej samej struktury projektu w programie Visual Studio: 

![Struktura projektu w programie Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Można dodać dodatkowe foldery w folderze podstawowym, ale nie głębiej niż jeden poziom. 

## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje na temat środowisk można znaleźć w następujących artykułach: 

- [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowisk publicznych w laboratoriach devtest azure](devtest-lab-configure-use-public-environments.md)
- [Łączenie środowiska z siecią wirtualną laboratorium w laboratorium Azure DevTest Labs](connect-environment-lab-virtual-network.md)