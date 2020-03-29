---
title: Bezpieczne wdrażanie w różnych regionach — Usługa Azure Deployment Manager
description: W tym artykule opisano sposób wdrażania usługi w wielu regionach za pomocą usługi Azure Deployment Manager. Pokazuje bezpieczne praktyki wdrażania, aby zweryfikować stabilność wdrożenia przed wdrożeniem we wszystkich regionach.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 424cd79a6c63200e1f101cf178b1fd2c9083161e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152531"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Włączanie bezpiecznych praktyk wdrażania za pomocą usługi Azure Deployment Manager (publiczna wersja zapoznawcza)

Aby wdrożyć usługę w wielu regionach i upewnić się, że działa zgodnie z oczekiwaniami w każdym regionie, można użyć usługi Azure Deployment Manager do koordynowania etapowego wdrażania usługi. Podobnie jak w przypadku każdego wdrożenia platformy Azure, można zdefiniować zasoby dla usługi w [szablonach Menedżera zasobów](template-syntax.md). Po utworzeniu szablonów należy użyć Menedżera wdrażania, aby opisać topologię usługi i sposób jej wdrożenia.

Menedżer wdrażania jest funkcją Menedżera zasobów. Rozszerza możliwości podczas wdrażania. Menedżer wdrażania należy używać, gdy masz złożoną usługę, która musi zostać wdrożona w kilku regionach. Dzięki etapowemu wdrażaniu Twojej usługi możesz znaleźć potencjalne problemy zanim zostanie ona wdrożona we wszystkich regionach. Jeśli nie potrzebujesz dodatkowych środków ostrożności dotyczących wdrażania etapowego, użyj standardowych [opcji wdrażania](deploy-portal.md) dla Menedżera zasobów. Menedżer wdrażania bezproblemowo integruje się ze wszystkimi istniejącymi narzędziami innych firm, które obsługują wdrożenia Menedżera zasobów, takimi jak ciągła integracja i ciągła dostarczanie (CI/CD).

Usługa Azure Deployment Manager jest w wersji zapoznawczej. Pomóż nam ulepszyć tę funkcję, przekazując [opinię.](https://aka.ms/admfeedback)

Aby korzystać z Menedżera wdrażania, należy utworzyć cztery pliki:

* Szablon topologii
* Szablon wdrażania
* Plik parametrów topologii
* Plik parametrów do wdrożenia

Przed wdrożeniem szablonu wdrożenia należy wdrożyć szablon topologii.

Dodatkowe zasoby:

- [Odwołanie do interfejsu API REST programu Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Samouczek: Użyj usługi Azure Deployment Manager z szablonami Menedżera zasobów](./deployment-manager-tutorial.md).
- [Samouczek: Użyj sprawdzania kondycji w usłudze Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
- [Przykład usługi Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Tożsamość i dostęp

Dzięki Menedżerowi wdrażania [tożsamość zarządzana przypisana przez użytkownika](../../active-directory/managed-identities-azure-resources/overview.md) wykonuje akcje wdrażania. Ta tożsamość jest tworzę przed rozpoczęciem wdrażania. Musi mieć dostęp do subskrypcji, do której wdrażasz usługę, i wystarczające uprawnienia do ukończenia wdrożenia. Aby uzyskać informacje na temat akcji przyznawanych za pośrednictwem ról, zobacz [Wbudowane role zasobów platformy Azure.](../../role-based-access-control/built-in-roles.md)

Tożsamość musi znajdować się w tej samej lokalizacji co wdrożenie.

## <a name="topology-template"></a>Szablon topologii

Szablon topologii opisuje zasoby platformy Azure, które tworzą usługę i gdzie je wdrożyć. Na poniższej ilustracji przedstawiono topologię przykładowej usługi:

![Hierarchia od topologii usług do usług do jednostek usługowych](./media/deployment-manager-overview/service-topology.png)

Szablon topologii zawiera następujące zasoby:

* Źródło artefaktów — gdzie przechowywane są szablony i parametry Menedżera zasobów
* Topologia usługi — wskazuje źródło artefaktu
  * Usługi — określa lokalizację i identyfikator subskrypcji platformy Azure
    * Jednostki serwisowe — określa grupę zasobów, tryb wdrażania i ścieżkę do pliku szablonu i parametru

Aby zrozumieć, co dzieje się na każdym poziomie, warto zobaczyć, które wartości podajesz.

![Wartości dla każdego poziomu](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Źródło artefaktów dla szablonów

W szablonie topologii należy utworzyć źródło artefaktu, w tym pliki szablonów i parametrów. Źródło artefaktu jest sposobem na pobranie plików do wdrożenia. Inne źródło artefaktów dla plików binarnych zostanie wyświetloną w dalszej części tego artykułu.

W poniższym przykładzie przedstawiono ogólny format źródła artefaktu.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "<artifact-source-name>",
  "location": "<artifact-source-location>",
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

Aby uzyskać więcej informacji, zobacz [artifactSources template reference](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Topologia usługi

W poniższym przykładzie przedstawiono ogólny format zasobu topologii usługi. Podać identyfikator zasobu źródła artefaktu, który przechowuje szablony i pliki parametrów. Topologia usługi obejmuje wszystkie zasoby usługi. Aby upewnić się, że źródło artefaktu jest dostępne, topologia usługi zależy od niego.

```json
{
  "type": "Microsoft.DeploymentManager/serviceTopologies",
  "apiVersion": "2018-09-01-preview",
  "name": "<topology-name>",
  "location": "<topology-location>",
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "artifactSourceId": "<resource-ID-artifact-source>"
  },
  "resources": [
    {
      "type": "services",
        ...
        }
    ]
}
```

Aby uzyskać więcej informacji, zobacz [serviceTopologies template reference](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Usługi

W poniższym przykładzie przedstawiono ogólny format zasobu usług. W każdej usłudze podajesz lokalizację i identyfikator subskrypcji platformy Azure do użycia do wdrażania usługi. Aby wdrożyć w kilku regionach, należy zdefiniować usługę dla każdego regionu. Usługa zależy od topologii usługi.

```json
{
  "type": "services",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-name>",
  "location": "<service-location>",
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

Aby uzyskać więcej informacji, zobacz [odwołanie do szablonu usług](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Jednostki usług

W poniższym przykładzie przedstawiono ogólny format zasobu jednostek serwisu. W każdej jednostce serwisowej należy określić grupę zasobów, [tryb wdrażania](deployment-modes.md) używany do wdrożenia oraz ścieżkę do pliku szablonu i parametru. Jeśli określisz ścieżkę względną dla szablonu i parametrów, pełna ścieżka jest konstruowana z folderu głównego w źródle artefaktów. Można określić ścieżkę bezwzględną dla szablonu i parametrów, ale tracisz możliwość łatwego wersji wydań. Jednostka serwisowa zależy od usługi.

```json
{
  "type": "serviceUnits",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-unit-name>",
  "location": "<service-unit-location>",
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

Każdy szablon powinien zawierać powiązane zasoby, które chcesz wdrożyć w jednym kroku. Na przykład jednostka usługi może mieć szablon, który wdraża wszystkie zasoby dla frontonomii usługi.

Aby uzyskać więcej informacji, zobacz [serviceUnits template reference](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Szablon wdrażania

Szablon wdrażania opisuje kroki, które należy wykonać podczas wdrażania usługi. Należy określić topologię usługi, która ma być używana, i zdefiniowanie kolejności wdrażania jednostek serwisowych. Zawiera źródło artefaktu do przechowywania plików binarnych dla wdrożenia. W szablonie wdrażania można zdefiniować następującą hierarchię:

* Źródło artefaktu
* Krok
* Odjeździe
  * Grupy kroków
    * Operacje wdrażania

Na poniższej ilustracji przedstawiono hierarchię szablonu wdrożenia:

![Hierarchia od wdrożenia do kroków](./media/deployment-manager-overview/Rollout.png)

Każde wdrożenie może mieć wiele grup kroków. Każda grupa kroków ma jedną operację wdrażania, która wskazuje jednostkę serwisową w topologii usługi.

### <a name="artifact-source-for-binaries"></a>Źródło artefaktów dla plików binarnych

W szablonie wdrożenia można utworzyć źródło artefaktu dla plików binarnych, które należy wdrożyć w usłudze. To źródło artefaktu jest podobne do [źródła artefaktów dla szablonów,](#artifact-source-for-templates)z tą różnicą, że zawiera skrypty, strony sieci web, skompilowany kod lub inne pliki potrzebne przez usługę.

### <a name="steps"></a>Kroki

Można zdefiniować krok do wykonania przed lub po operacji wdrażania. Obecnie dostępny jest `wait` tylko krok i krok "healthCheck".

Krok oczekiwania wstrzymuje wdrożenie przed kontynuowaniem. Umożliwia sprawdzenie, czy usługa jest uruchomiona zgodnie z oczekiwaniami przed wdrożeniem następnej jednostki serwisowej. W poniższym przykładzie przedstawiono ogólny format kroku oczekiwania.

```json
{
    "type": "Microsoft.DeploymentManager/steps",
    "apiVersion": "2018-09-01-preview",
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

Właściwość czas trwania używa [normy ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). W poprzednim przykładzie określa jednominutowe oczekiwanie.

Aby uzyskać więcej informacji na temat kroku sprawdzania kondycji, zobacz [Wprowadzenie wdrożenia integracji kondycji do usługi Azure Deployment Manager](./deployment-manager-health-check.md) i [samouczek: Użyj sprawdzania kondycji w usłudze Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

Aby uzyskać więcej informacji, zobacz [odwołanie do szablonu kroków](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Wprowadzanie

Aby upewnić się, że źródło artefaktu jest dostępne, wdrożenie zależy od niego. Wdrożenie definiuje grupy kroków dla każdej wdrożonej jednostki serwisowej. Można zdefiniować akcje, które należy podjąć przed wdrożeniem lub po jego wdrożeniu. Na przykład można określić, że wdrożenie czekać po wdrożeniu jednostki serwisowej. Można zdefiniować kolejność grup kroków.

Obiekt tożsamości określa [tożsamość zarządzaną przypisaną przez użytkownika,](#identity-and-access) która wykonuje akcje wdrażania.

W poniższym przykładzie przedstawiono ogólny format wdrożenia.

```json
{
  "type": "Microsoft.DeploymentManager/rollouts",
  "apiVersion": "2018-09-01-preview",
  "name": "<rollout-name>",
  "location": "<rollout-location>",
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

Aby uzyskać więcej informacji, zobacz [odwołanie do szablonu wdrażania](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Plik parametrów

Utworzysz dwa pliki parametrów. Jeden plik parametru jest używany podczas wdrażania topologii usługi, a drugi jest używany do wdrażania. Istnieją pewne wartości, które należy upewnić się, że są takie same w obu plikach parametrów.

## <a name="containerroot-variable"></a>kontenerZachowa zmienna

W zależności od wersji wdrożenia ścieżka do artefaktów zmienia się wraz z każdą nową wersją. Przy pierwszym uruchomieniu wdrożenia ścieżka może `https://<base-uri-blob-container>/binaries/1.0.0.0`być . Po raz drugi `https://<base-uri-blob-container>/binaries/1.0.0.1`może to być . Menedżer wdrażania upraszcza uzyskanie poprawnej ścieżki głównej `$containerRoot` dla bieżącego wdrożenia przy użyciu zmiennej. Ta wartość zmienia się z każdej wersji i nie jest znana przed wdrożeniem.

Użyj `$containerRoot` zmiennej w pliku parametrów dla szablonu, aby wdrożyć zasoby platformy Azure. W czasie wdrażania ta zmienna jest zastępowana rzeczywistymi wartościami z wdrożenia.

Na przykład podczas wdrażania można utworzyć źródło artefaktu dla artefaktów binarnych.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "[variables('rolloutArtifactSource').name]",
  "location": "[parameters('azureResourceLocation')]",
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

Zwróć `artifactRoot` uwagę `sasUri` na właściwości i. Katalog główny artefaktu może być `binaries/1.0.0.0`ustawiony na wartość podobną do . Identyfikator URI sygnatury dostępu współdzielonego jest identyfikatorem URI do kontenera magazynu z tokenem sygnatury dostępu sygnatury dostępu. Menedżer wdrażania automatycznie konstruuje `$containerRoot` wartość zmiennej. Łączy te wartości w `<container>/<artifactRoot>`formacie .

Plik szablonu i parametrów musi znać właściwą ścieżkę do uzyskiwania plików binarnych wersji. Na przykład, aby wdrożyć pliki dla aplikacji sieci web, należy utworzyć następujący plik parametru ze zmienną $containerRoot. Dla ścieżki należy użyć dwóch`\\`ukośników odwrotnych ( ), ponieważ pierwszy z nich jest znakiem ucieczki.

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
  "apiVersion": "2015-08-01",
  "type": "extensions",
  "location": "[parameters('location')]",
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

Zarządzasz wdrożeniami wersjonaszonymi, tworząc nowe foldery i przekazując w tym katalogu głównym podczas wdrażania. Ścieżka przepływa przez szablon, który wdraża zasoby.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o Menedżerze wdrażania. Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć za pomocą Menedżera wdrażania.

> [!div class="nextstepaction"]
> [Samouczek: Używanie usługi Azure Deployment Manager z szablonami Menedżera zasobów](./deployment-manager-tutorial.md)
>
> [Szybki start: wypróbuj usługę Azure Deployment Manager w zaledwie kilka minut](https://github.com/Azure-Samples/adm-quickstart)
