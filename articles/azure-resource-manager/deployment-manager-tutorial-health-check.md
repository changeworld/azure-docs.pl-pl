---
title: Używanie usługi Azure Deployment Manager z szablonami usługi Resource Manager | Microsoft Docs
description: Użyj szablonów usługi Resource Manager z usługą Azure Deployment Manager, aby wdrażać zasoby platformy Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2bdff6195a0dcf93bfc3a596189b062bf4f3ab12
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254976"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Samouczek: korzystanie z kontroli kondycji w usłudze Azure Menedżer wdrażania (publiczna wersja zapoznawcza)

Dowiedz się, jak zintegrować kontrolę kondycji w [usłudze Azure Menedżer wdrażania](./deployment-manager-overview.md). Ten samouczek jest oparty na samouczku [Korzystanie z Menedżer wdrażania platformy Azure z szablonami Menedżer zasobów](./deployment-manager-tutorial.md) . Przed przeprowadzeniem tej czynności należy wykonać ten samouczek.

W szablonie wdrożenia używanym w [usłudze Azure Menedżer wdrażania z szablonami Menedżer zasobów](./deployment-manager-tutorial.md)użyto kroku oczekiwania. W tym samouczku zastąpisz krok oczekiwania z etapem sprawdzania kondycji.

> [!IMPORTANT]
> Jeśli Twoja subskrypcja jest oznaczona jako przeznaczona do testowania nowych funkcji platformy Azure, możesz użyć usługi Azure Menedżer wdrażania tylko do wdrożenia w regionach Kanaryjskich. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie symulatora usługi sprawdzania kondycji
> * Popraw szablon wdrożenia
> * Wdrażanie topologii
> * Wdrażanie wdrożenia ze stanem złej kondycji
> * Weryfikowanie wdrożenia wdrażania
> * Wdrażanie wdrożenia ze stanem kondycji
> * Weryfikowanie wdrożenia wdrażania
> * Oczyszczanie zasobów

Zasoby dodatkowe:

- [Dokumentacja interfejsu API REST usługi Azure Menedżer wdrażania](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Przykład Menedżer wdrażania platformy Azure](https://github.com/Azure-Samples/adm-quickstart).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Ukończ [Korzystanie z usługi Azure Menedżer wdrażania z szablonami Menedżer zasobów](./deployment-manager-tutorial.md).
* Pobierz [Szablony i artefakty](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) , które są używane w tym samouczku.

## <a name="create-a-health-check-service-simulator"></a>Tworzenie symulatora usługi sprawdzania kondycji

W środowisku produkcyjnym zwykle używany jest jeden lub więcej dostawców monitorowania. Aby zapewnić integrację z kondycją tak jak to możliwe, firma Microsoft współpracuje z niektórymi firmami monitorowania kondycji usług w celu zapewnienia prostego rozwiązania do kopiowania/wklejania w celu zintegrowania kontroli kondycji z wdrożeniami. Aby zapoznać się z listą tych firm, zobacz [dostawcy monitorowania kondycji](./deployment-manager-health-check.md#health-monitoring-providers). Na potrzeby tego samouczka utworzysz [funkcję platformy Azure](/azure/azure-functions/) w celu symulowania usługi monitorowania kondycji. Ta funkcja przyjmuje kod stanu i zwraca ten sam kod. Szablon Menedżer wdrażania platformy Azure używa kodu stanu, aby określić, jak kontynuować wdrażanie.

Poniższe dwa pliki są używane do wdrażania funkcji platformy Azure. Nie musisz pobierać tych plików, aby przejść przez samouczek.

* Szablon Menedżer zasobów zlokalizowany w [https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json). Ten szablon zostanie wdrożony w celu utworzenia funkcji platformy Azure.
* Plik zip kodu źródłowego funkcji platformy Azure, [https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip](https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip). Ten plik zip jest wywoływany przez szablon Menedżer zasobów.

Aby wdrożyć funkcję platformy Azure, wybierz **ją** , aby otworzyć usługę Azure Cloud Shell, a następnie wklej następujący skrypt do okna powłoki.  Aby wkleić kod, kliknij prawym przyciskiem myszy okno powłoki, a następnie wybierz polecenie **Wklej**.

> [!IMPORTANT]
> **projectName** w skrypcie programu PowerShell służy do generowania nazw usług platformy Azure, które są wdrożone w tym samouczku. Użyj tej samej wartości **namePrefix** , która została użyta w [Menedżer wdrażania na platformie Azure przy użyciu szablonów Menedżer zasobów](./deployment-manager-tutorial.md) dla projectName.  Różne usługi platformy Azure mają różne wymagania dotyczące nazw. Aby upewnić się, że wdrożenie zakończyło się pomyślnie, wybierz nazwę o długości mniejszej niż 12 znaków i cyfr.
> Zapisz kopię nazwy projektu. Ten sam projectName jest używany w samouczku.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Aby sprawdzić i przetestować funkcję platformy Azure:

1. Otwórz [portal Azure](https://portal.azure.com).
1. Otwórz grupę zasobów.  Nazwa domyślna to nazwa projektu z dołączoną **RG** .
1. Wybierz usługę App Service z grupy zasobów.  Domyślną nazwą usługi App Service jest nazwa projektu z dołączoną **webapp** .
1. Rozwiń pozycję **funkcje**, a następnie wybierz pozycję **HttpTrigger1**.

    ![Azure Menedżer wdrażania Health Check — funkcja Azure Function](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Wybierz pozycję **&lt;/> Pobierz adres URL funkcji**.
1. Wybierz pozycję **Kopiuj** , aby skopiować adres URL do Schowka.  Adres URL jest podobny do:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Zastąp `{healthStatus}` w adresie URL kodem stanu. W tym samouczku użyjesz **złej kondycji** w celu przetestowania scenariusza w złej kondycji i Użyj **zdrowego** lub **ostrzeżenia** , aby przetestować ten scenariusz. Utwórz dwa adresy URL, jeden ze stanem złej kondycji, a drugi ze stanem kondycji. Przykłady:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Aby ukończyć ten samouczek, musisz dysponować obu adresów URL.

1. Aby przetestować symulator monitorowania kondycji, Otwórz adresy URL utworzone w ostatnim kroku.  Wyniki dla stanu złej kondycji są podobne do:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Popraw szablon wdrożenia

Ta sekcja zawiera informacje na temat sposobu dołączania kroku sprawdzania kondycji do szablonu wdrożenia.

1. Otwórz plik **CreateADMRollout. JSON** , który został utworzony przy [użyciu usługi Azure Menedżer wdrażania z szablonami Menedżer zasobów](./deployment-manager-tutorial.md). Ten plik JSON jest częścią pobierania.  Zobacz [Wymagania wstępne](#prerequisites).
1. Dodaj dwa dodatkowe parametry:

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. Zastąp definicję zasobu krok oczekiwania z definicją zasobu kroku sprawdzania kondycji:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    Zgodnie z definicją, wdrożenie jest wykonywane, jeśli kondycja jest w *dobrej* kondycji lub *Ostrzeżenie*.

1. Zaktualizuj **dependsON** definicji wdrożenia, aby uwzględnić nowo zdefiniowany krok sprawdzania kondycji:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Zaktualizuj **stepGroups** , aby uwzględnić krok sprawdzania kondycji. **HealthCheckStep** jest wywoływana w **postDeploymentSteps** of **stepGroup2**. **stepGroup3** i **stepGroup4** są wdrażane tylko wtedy, gdy kondycja jest w dobrej *kondycji* lub *Ostrzeżenie*.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    Jeśli porównano sekcję **stepGroup3** przed zmianą i po niej, ta sekcja będzie zależeć od **stepGroup2**.  Jest to konieczne, gdy **stepGroup3** i kolejne grupy kroków są zależne od wyników monitorowania kondycji.

    Poniższy zrzut ekranu ilustruje modyfikowane obszary i sposób użycia kroku sprawdzania kondycji:

    ![Szablon sprawdzania kondycji usługi Azure Menedżer wdrażania](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Wdrażanie topologii

Uruchom Poniższy skrypt programu PowerShell, aby wdrożyć topologię. Potrzebne są te same **CreateADMServiceTopology. JSON** i **CreateADMServiceTopology. Parameters. JSON** , które były używane w [usłudze Azure Menedżer wdrażania z szablonami Menedżer zasobów](./deployment-manager-tutorial.md).

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the file path to the downloaded tutorial files"

$resourceGroupName = "${projectName}rg"


# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Sprawdź, czy topologia usługi oraz podstawowe zasoby zostały utworzone pomyślnie, korzystając z witryny Azure Portal:

![Samouczek dotyczący usługi Azure Deployment Manager — wdrożone zasoby topologii usługi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Opcja **Pokaż ukryte typy** musi być zaznaczona, aby wyświetlić zasoby.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Wdróż wdrożenie ze stanem złej kondycji

Użyj adresu URL stanu złej kondycji utworzonego w temacie [Tworzenie symulatora usługi sprawdzania kondycji](#create-a-health-check-service-simulator). Potrzebujesz poprawionego pliku **CreateADMServiceTopology. JSON** oraz tego samego pliku **CreateADMServiceTopology. Parameters. JSON** , który został użyty w [systemie Azure Menedżer wdrażania z szablonami Menedżer zasobów](./deployment-manager-tutorial.md).

```azurepowershell-interactive
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json" `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` to wywołanie asynchroniczne. Komunikat o powodzeniu oznacza, że wdrożenie zostało pomyślnie rozpoczęte. Aby zweryfikować wdrożenie, użyj `Get-AZDeploymentManagerRollout`.  Zobacz następną procedurę.

Aby sprawdzić postęp wdrażania przy użyciu następującego skryptu programu PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

Następujące przykładowe dane wyjściowe pokazują, że wdrożenie nie powiodło się z powodu stanu złej kondycji:

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

Po zakończeniu wdrażania zobaczysz jedną dodatkową grupę zasobów utworzoną dla regionu zachodnie stany USA.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Wdrażanie wdrożenia ze stanem kondycji

Powtórz tę sekcję, aby ponownie wdrożyć wdrożenie przy użyciu adresu URL stanu prawidłowości.  Po zakończeniu wdrażania zobaczysz jeszcze jedną grupę zasobów utworzoną dla regionu Wschodnie stany USA.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Otwórz [portal Azure](https://portal.azure.com).
2. Przejdź do nowo utworzonych aplikacji internetowych w obrębie nowych grup zasobów utworzonych przez wdrożenie wprowadzania.
3. Otwórz aplikację internetową w przeglądarce internetowej. Sprawdź lokalizację i wersję w pliku index.html.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Użyj pola **Filtruj według nazwy**, aby zawęzić listę grup zasobów utworzonych w tym samouczku. Powinny istnieć 3–4 grupy:

    * **&lt;namePrefix>rg**: zawiera zasoby usługi Deployment Manager.
    * **&lt;namePrefix>ServiceWUSrg**: zawiera zasoby zdefiniowane przez usługę ServiceWUS.
    * **&lt;namePrefix>ServiceEUSrg**: zawiera zasoby zdefiniowane przez usługę ServiceEUS.
    * Grupa zasobów dla tożsamości zarządzanej zdefiniowanej przez użytkownika.
3. Wybierz nazwę grupy zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.
5. Powtórz dwa ostatnie kroki, aby usunąć inne grupy zasobów utworzone w ramach tego samouczka.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób korzystania z funkcji sprawdzania kondycji w usłudze Azure Menedżer wdrażania. Aby dowiedzieć się więcej, zobacz [dokumentację usługi Azure Resource Manager](/azure/azure-resource-manager/).
