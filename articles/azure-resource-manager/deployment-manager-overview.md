---
title: Praktyki bezpiecznego wdrażania w wielu regionach — Azure Deployment Manager
description: W tym artykule opisano, jak wdrożyć usługę w wielu regionach za pomocą usługi Azure Deployment Manager. Przedstawia on praktyki bezpiecznego wdrażania, aby sprawdzić stabilność wdrożenie, zanim zetknie się we wszystkich regionach.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 6a25444f0207ec5eceb029c5d31d222a31813e22
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066822"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Włącz praktyki bezpiecznego wdrażania w usłudze Azure Deployment Manager (publiczna wersja zapoznawcza)

Aby wdrożyć usługę w wielu regionach i upewnij się, że działa on zgodnie z oczekiwaniami w każdym regionie, Azure Deployment Manager służy do koordynowania etapowe wdrażanie usługi. Tak jak w przypadku wszystkich wdrożeń platformy Azure dla usługi w określają zasoby [szablonów usługi Resource Manager](resource-group-authoring-templates.md). Po utworzeniu szablonów umożliwia Deployment Manager opisano topologii dla Twojej usługi i jak ona powinny być udostępniona.

Program Deployment Manager to funkcja usługi Resource Manager. Rozszerza możliwości podczas wdrażania. Użyj programu Deployment Manager w przypadku złożonych usługa, która ma zostać wdrożone w wielu regionach. Dzięki etapowemu wdrażaniu Twojej usługi możesz znaleźć potencjalne problemy zanim zostanie ona wdrożona we wszystkich regionach. Jeśli nie potrzebujesz dodatkowych środków ostrożności przygotowanych wdrożenia, należy zastosować standard [opcje wdrażania](resource-group-template-deploy-portal.md) dla usługi Resource Manager. Program Deployment Manager bezproblemowo integrują się ze wszystkich istniejących narzędzi innych firm, które obsługują wdrożeń usługi Resource Manager, takie jak ciągła integracja i ciągłe dostarczanie (CI/CD) ofert.

Azure Deployment Manager jest w wersji zapoznawczej. Pomóż nam udoskonalać tę funkcję, zapewniając [opinii](https://aka.ms/admfeedback).

Aby użyć programu Deployment Manager, należy utworzyć cztery pliki:

* Szablon topologii
* Szablon wprowadzania
* Plik parametrów dla topologii
* Plik parametrów dla wdrożenia

Szablon topologii wdrażania przed wdrożeniem szablon wprowadzania.

Dodatkowe zasoby:

- [Odwołanie do interfejsu REST API usługi Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Samouczek: Azure Deployment Manager za pomocą szablonów usługi Resource Manager](./deployment-manager-tutorial.md).
- [Samouczek: Użyj sprawdzania kondycji w usłudze Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
- [Próbka Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Tożsamość i dostęp

Za pomocą programu Deployment Manager [przypisanych przez użytkownika z tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) wykonuje operacje wdrażania. Ta tożsamość jest utworzyć przed rozpoczęciem wdrażania. Musi mieć dostęp do subskrypcji, które wdrażasz usługi i wystarczających uprawnień do ukończenia wdrażania. Aby uzyskać informacje o akcjach udzielone za pośrednictwem ról, zobacz [wbudowane role zasobów platformy Azure](../role-based-access-control/built-in-roles.md).

Tożsamość musi znajdować się w tej samej lokalizacji co wdrożenia.

## <a name="topology-template"></a>Szablon topologii

Szablon topologii opisuje zasobów platformy Azure, które tworzą usługi oraz miejsce ich wdrażania. Na poniższej ilustracji przedstawiono topologii dla usługi w przykładzie:

![Hierarchia od topologii usługi do usług do jednostki usługi](./media/deployment-manager-overview/service-topology.png)

Szablon topologia zawiera następujące zasoby:

* Źródło artefaktu — gdzie są przechowywane z szablonów usługi Resource Manager i parametry
* Topologia usługi — wskazuje źródło artefaktów
  * Usługi — określa lokalizację i identyfikator subskrypcji platformy Azure
    * Jednostki usługi — określa grupę zasobów, tryb wdrożenia i ścieżka do pliku szablonu oraz parametrów

Aby dowiedzieć się, co się stanie, na każdym poziomie, jest przydatne zobaczyć wartości, które należy podać.

![Wartości dla poszczególnych poziomów](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Źródło artefaktu dla szablonów

W szablonie topologii utworzysz źródła artefaktu, który zawiera pliki szablonów i parametry. Źródło artefaktu jest sposobem pobrania plików do wdrożenia. Zobaczysz innego źródła artefaktu dla danych binarnych w dalszej części tego artykułu.

Poniższy kod przedstawia ogólny format źródła artefaktu.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "<root-folder-for-templates>",
        "authentication": {
            "type": "SAS",
            "properties": {
                "sasUri": "<SAS-URI-for-storage-container>"
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [odwołanie do szablonu artifactSources](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Topologia usługi

Poniższy kod przedstawia ogólny format zasób topologii usługi. Należy podać identyfikator zasobu źródła artefaktu, który zawiera szablony i pliki parametrów. Topologia usługi obejmuje wszystkie zasoby usługi. Aby upewnić się, że źródła artefaktu jest dostępna, topologii usługi zależy od niego.

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "resources": [
        {
            "type": "services",
            ...
        }
    ]
}
```

Aby uzyskać więcej informacji, zobacz [odwołanie do szablonu serviceTopologies](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Usługi

Poniższy kod przedstawia ogólny format zasobów usług. Każda usługa służy do Podaj identyfikator subskrypcji w lokalizacji i platformą Azure, będzie używana do wdrażania usługi. Aby wdrożyć w kilku regionach, w celu zdefiniowania usługi dla każdego regionu. Usługa zależy od topologii usługi.

```json
{
    "type": "services",
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service-topology>"
    ],
    "properties": {
        "targetSubscriptionId": "<subscription-ID>",
        "targetLocation": "<location-of-deployed-service>"
    },
    "resources": [
        {
            "type": "serviceUnits",
            ...
        }
    ]
}
```

Aby uzyskać więcej informacji, zobacz [odwołanie do szablonu usługi](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Jednostki usług

Poniższy kod przedstawia ogólny format zasobu jednostki usługi. W każdej jednostce usługi, określ grupę zasobów, [tryb wdrożenia](deployment-modes.md) służące do wdrożenia i ścieżka do pliku szablonu oraz parametrów. Jeśli określisz ścieżki względnej, aby uzyskać szablon i parametry, pełna ścieżka jest tworzona z folderu głównego źródła artefaktów. Można określić ścieżkę bezwzględną szablon i parametry, ale utracisz możliwość do wersji łatwe tworzenie wersji. Jednostki usługi, zależy od usługi.

```json
{
    "type": "serviceUnits",
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service>"
    ],
    "tags": {
        "serviceType": "Service West US Web App"
    },
    "properties": {
        "targetResourceGroup": "<resource-group-name>",
        "deploymentMode": "Incremental",
        "artifacts": {
            "templateArtifactSourceRelativePath": "<relative-path-to-template>",
            "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
        }
    }
}
```

Każdy szablon powinien zawierać powiązane zasoby, które mają zostać wdrożone w jednym kroku. Na przykład jednostka usługi może mieć szablon, który służy do wdrażania wszystkich zasobów dla usługi frontonu.

Aby uzyskać więcej informacji, zobacz [odwołanie do szablonu serviceUnits](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Szablon wprowadzania

Szablon wdrożenia w tym artykule opisano kroki do wykonania podczas wdrażania usługi. Należy określić topologii usługi i zdefiniuj kolejność wdrażania jednostek usługi. Obejmuje on źródła artefaktu do przechowywania plików binarnych dla wdrożenia. W szablonie wdrożenia możesz zdefiniować następującej hierarchii:

* Źródło artefaktu
* Krok
* Wdrożenie
  * Grupy kroków
    * Operacje wdrażania

Na poniższej ilustracji przedstawiono hierarchię szablon wprowadzania:

![Hierarchii od wprowadzania czynności](./media/deployment-manager-overview/Rollout.png)

Każdego wdrożenia może mieć wiele grupy kroków. Każda grupa kroku ma jedną operację wdrożenia, który wskazuje na jednostkę usługi, w topologii usługi.

### <a name="artifact-source-for-binaries"></a>Źródło artefaktu dla danych binarnych

W szablonie wdrożenia tworzenia źródła artefaktu dla danych binarnych, czego potrzebujesz do wdrożenia usługi. To źródło artefaktu jest podobne do [źródła artefaktu dla szablonów](#artifact-source-for-templates), z tą różnicą, że zawiera on skrypty, stron sieci web, skompilowany kod lub inne pliki wymagane przez usługę.

### <a name="steps"></a>Kroki

Można zdefiniować krok do wykonania przed lub po operacji wdrożenia. Obecnie tylko `wait` kroku i krok "test kondycji" są dostępne.

Krok oczekiwania wstrzymuje wdrożenia przed kontynuowaniem. Umożliwia zweryfikowanie, że usługa jest uruchomiona, zgodnie z oczekiwaniami przed wdrożeniem następnej jednostki usługi. Poniższy kod przedstawia ogólny format kroku oczekiwania.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
    "name": "waitStep",
        "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

Właściwość czasu trwania używa [standardem ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). Poprzedni przykład określa oczekiwania jednej minuty.

Aby uzyskać więcej informacji o kroku sprawdzenia kondycji, zobacz [wprowadzenie do usługi Azure Deployment Manager wdrożenie integracji kondycji](./deployment-manager-health-check.md) i [samouczka: Użyj sprawdzania kondycji w usłudze Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

Aby uzyskać więcej informacji, zobacz [kroki odwołanie do szablonu](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Wprowadzanie

Aby upewnić się, że źródła artefaktu jest dostępna, wdrożenia zależy od niego. Wdrożenia definiuje grupy kroków dla każdej jednostki usługi, które zostało wdrożone. Można zdefiniować akcje do wykonania przed lub po wdrożeniu. Na przykład można określić, że wdrożenie oczekiwać po wdrożeniu jednostki usługi. Można zdefiniować kolejność grupy kroków.

Określa obiekt tożsamości [przypisanych przez użytkownika z tożsamości zarządzanej](#identity-and-access) która wykonuje operacje wdrażania.

Poniższy kod przedstawia ogólny format wdrożenia.

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
    "Identity": {
        "type": "userAssigned",
        "identityIds": [
            "<managed-identity-ID>"
        ]
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "properties": {
        "buildVersion": "1.0.0.0",
        "artifactSourceId": "<artifact-source-ID>",
        "targetServiceTopologyId": "<service-topology-ID>",
        "stepGroups": [
            {
                "name": "stepGroup1",
                "dependsOnStepGroups": ["<step-group-name>"],
                "preDeploymentSteps": ["<step-ID>"],
                "deploymentTargetId":
                    "<service-unit-ID>",
                "postDeploymentSteps": ["<step-ID>"]
            },
            ...
        ]
    }
}
```

Aby uzyskać więcej informacji, zobacz [odwołanie do szablonu wprowadzanie](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Plik parametrów

Należy utworzyć dwa pliki parametrów. Jeden plik parametrów jest używana podczas wdrażania topologii usługi, a drugi jest używany do wdrażania wdrożenia. Brak niektórych wartości, musisz upewnić się, są takie same, w obu plikach parametru.

## <a name="containerroot-variable"></a>Zmienna containerRoot

W przypadku wdrożeń numerów wersji ścieżkę do artefaktów zmienia się z każdym wydaniu nowej wersji. Przy pierwszym uruchomieniu wdrożenia ścieżki mogą być `https://<base-uri-blob-container>/binaries/1.0.0.0`. Może być po raz drugi `https://<base-uri-blob-container>/binaries/1.0.0.1`. Program Deployment Manager ułatwia uzyskiwanie ścieżki katalogu poprawny głównego dla bieżącego wdrożenia przy użyciu `$containerRoot` zmiennej. Ta wartość zmienia się z każdą wersją i nie jest znana przed przystąpieniem do wdrożenia.

Użyj `$containerRoot` zmiennej w pliku parametrów szablonu do wdrażania zasobów platformy Azure. W czasie wdrażania ta zmienna jest zastępowana rzeczywistymi wartościami z wdrożenia.

Na przykład podczas wprowadzania utworzyć źródła artefaktu dla binarnego artefaktów.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "[parameters('binaryArtifactRoot')]",
        "authentication" :
        {
            "type": "SAS",
            "properties": {
                "sasUri": "[parameters('artifactSourceSASLocation')]"
            }
        }
    }
},
```

Zwróć uwagę `artifactRoot` i `sasUri` właściwości. Główny artefaktu może być ustawiony na wartość taką jak `binaries/1.0.0.0`. Identyfikator URI sygnatury dostępu Współdzielonego to identyfikator URI do kontenera magazynu przy użyciu tokenu sygnatury dostępu Współdzielonego dla dostępu. Program Deployment Manager automatycznie tworzy wartość `$containerRoot` zmiennej. Łączy ona te wartości w formacie `<container>/<artifactRoot>`.

Plik szablonu oraz parametrów muszą znasz poprawnej ścieżki w celu uzyskania wersji plików binarnych. Na przykład aby wdrożyć pliki dla aplikacji sieci web, utwórz następujący plik parametrów za pomocą zmiennej $containerRoot. Należy użyć dwa ukośniki odwrotne (`\\`) dla ścieżki, ponieważ pierwszy jest znakiem ucieczki.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "deployPackageUri": {
            "value": "$containerRoot\\helloWorldWebAppWUS.zip"
        }
    }
}
```

Następnie użyj tego parametru w szablonie:

```json
{
    "name": "MSDeploy",
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
    "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
    ],
    "tags": {
        "displayName": "WebAppMSDeploy"
    },
    "properties": {
        "packageUri": "[parameters('deployPackageURI')]"
    }
}
```

Wersjonowany wdrożeń, tworząc nowe foldery i przekazywania w tej domenie głównej w miarę wprowadzania aktualizacji. Ścieżka są przekazywane za pośrednictwem do szablonu, który służy do wdrażania zasobów.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono Deployment Manager. Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć za pomocą programu Deployment Manager.

> [!div class="nextstepaction"]
> [Samouczek: Azure Deployment Manager za pomocą szablonów usługi Resource Manager](./deployment-manager-tutorial.md)