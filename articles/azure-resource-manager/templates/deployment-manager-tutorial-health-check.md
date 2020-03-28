---
title: Sprawdzanie kondycji usługi Azure Deployment Manager
description: Sprawdzanie kondycji umożliwia bezpieczne wdrażanie zasobów platformy Azure za pomocą usługi Azure Deployment Manager.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 765c73a3ab8d5fa8939abe597d0141b24b59ac52
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76152481"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Samouczek: Używanie sprawdzania kondycji w usłudze Azure Deployment Manager (publiczna wersja zapoznawcza)

Dowiedz się, jak zintegrować sprawdzanie kondycji w [usłudze Azure Deployment Manager](./deployment-manager-overview.md). Ten samouczek jest oparty na [samouczku Użyj usługi Azure Deployment Manager z szablonami Menedżera zasobów.](./deployment-manager-tutorial.md) Musisz ukończyć ten samouczek, zanim przejdziesz do tego.

W szablonie wdrażania używanym w [usłudze Korzystanie z usługi Azure Deployment Manager z szablonami Menedżera zasobów](./deployment-manager-tutorial.md)użyto kroku oczekiwania. W tym samouczku należy zastąpić krok oczekiwania krokiem sprawdzania kondycji.

> [!IMPORTANT]
> Jeśli twoja subskrypcja jest oznaczona dla Canary, aby przetestować nowe funkcje platformy Azure, można użyć tylko usługi Azure Deployment Manager do wdrożenia w regionach Kanary. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie symulatora usługi sprawdzania kondycji
> * Poprawianie szablonu wdrożenia
> * Wdrażanie topologii
> * Wdrażanie wdrożenia ze stanem w złej kondycji
> * Weryfikowanie wdrożenia
> * Wdrażanie wdrożenia ze stanem dobrej kondycji
> * Weryfikowanie wdrożenia
> * Oczyszczanie zasobów

Dodatkowe zasoby:

* [Odwołanie do interfejsu API REST programu Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Przykład usługi Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Pełne [korzystanie z usługi Azure Deployment Manager z szablonami Menedżera zasobów](./deployment-manager-tutorial.md).

## <a name="install-the-artifacts"></a>Instalowanie artefaktów

Pobierz [szablony i artefakty](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) i rozpakuj je lokalnie, jeśli tego nie zrobiłeś. A następnie uruchom skrypt programu PowerShell znaleziony w [przygotowaniu artefaktów](./deployment-manager-tutorial.md#prepare-the-artifacts). Skrypt tworzy grupę zasobów, tworzy kontener magazynu, tworzy kontener obiektów blob, przekazuje pobrane pliki, a następnie tworzy token sygnatury dostępu Współdzielonego.

Zrób kopię adresu URL za pomocą tokenu sygnatury dostępu Współdzielonego. Ten adres URL jest potrzebny do wypełnienia pola w dwóch plikach parametrów, pliku parametrów topologii oraz pliku parametrów wprowadzania.

Otwórz createadmservicetopology.parameters.json i zaktualizuj wartości **projectName** i **artifactSourceSASLocation**.

Otwórz createaDMRollout.Parameters.json i zaktualizuj wartości **projectName** i **artifactSourceSASLocation**.

## <a name="create-a-health-check-service-simulator"></a>Tworzenie symulatora usługi sprawdzania kondycji

W produkcji zazwyczaj używasz jednego lub więcej dostawców monitorowania. Aby integracja kondycji była tak łatwa, jak to możliwe, firma Microsoft współpracuje z niektórymi z najlepszych firm monitorujących kondycję usług, aby zapewnić proste rozwiązanie do kopiowania/wklejania w celu zintegrowania kontroli kondycji z wdrożeniami. Aby uzyskać listę tych firm, zobacz [Dostawcy monitorowania kondycji](./deployment-manager-health-check.md#health-monitoring-providers). Na potrzeby tego samouczka należy utworzyć [funkcję platformy Azure,](/azure/azure-functions/) aby symulować usługę monitorowania kondycji. Ta funkcja przyjmuje kod stanu i zwraca ten sam kod. Szablon usługi Azure Deployment Manager używa kodu stanu, aby określić, jak kontynuować wdrażanie.

Następujące dwa pliki są używane do wdrażania funkcji platformy Azure. Nie musisz pobierać tych plików, aby przejść przez samouczek.

* Szablon Menedżera zasobów [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json)znajdujący się pod adresem . Wdrożyć ten szablon, aby utworzyć funkcję platformy Azure.
* Plik pocztowy kodu źródłowego funkcji [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip)platformy Azure, . Ten kod pocztowy się wywoływany przez szablon Menedżera zasobów.

Aby wdrożyć funkcję platformy Azure, wybierz pozycję **Wypróbuj,** aby otworzyć powłokę usługi Azure Cloud, a następnie wklej następujący skrypt do okna powłoki.  Aby wkleić kod, kliknij prawym przyciskiem myszy okno powłoki, a następnie wybierz polecenie **Wklej**.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Aby zweryfikować i przetestować funkcję platformy Azure:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Otwórz grupę zasobów.  Domyślna nazwa to nazwa projektu z **dodatkiem rg.**
1. Wybierz usługę aplikacji z grupy zasobów.  Domyślną nazwą usługi aplikacji jest nazwa projektu z dołączeną **aplikacją webapp.**
1. Rozwiń **pozycję Funkcje**, a następnie wybierz pozycję **HttpTrigger1**.

    ![Sprawdzanie kondycji usługi Azure Deployment Manager w usłudze Azure Function](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Wybierz ** &lt;/> Pobierz adres URL funkcji**.
1. Wybierz **pozycję Kopiuj,** aby skopiować adres URL do schowka.  Adres URL jest podobny do:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Zastąp `{healthStatus}` w adresie URL kod stanu. W tym samouczku użyj **złej kondycji,** aby przetestować scenariusz w złej kondycji i użyj **zdrowego** lub **ostrzeżenia,** aby przetestować zdrowy scenariusz. Utwórz dwa adresy URL, jeden ze stanem w złej kondycji, a drugi ze stanem dobrej kondycji. Przykłady:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Aby ukończyć ten samouczek, potrzebne są oba adresy URL.

1. Aby przetestować symulator monitorowania kondycji, otwórz adresy URL utworzone w ostatnim kroku.  Wyniki dla stanu niezdrowego są podobne do:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Poprawianie szablonu wdrożenia

Celem tej sekcji jest pokazanie, jak dołączyć krok sprawdzania kondycji w szablonie wdrażania.

1. Otwórz **createADMRollout.json** utworzony w [usłudze Korzystanie z usługi Azure Deployment Manager z szablonami Menedżera zasobów](./deployment-manager-tutorial.md). Ten plik JSON jest częścią pobierania.  Zobacz [Wymagania wstępne](#prerequisites).
1. Dodaj jeszcze dwa parametry:

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

1. Zastąp definicję zasobu kroku oczekiwania definicją zasobu kroku sprawdzania kondycji:

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

    Na podstawie definicji wdrożenie przebiega, jeśli stan kondycji jest *w dobrej kondycji* lub *ostrzeżenie*.

1. Zaktualizuj **dependsON** definicji wdrożenia, aby uwzględnić nowo zdefiniowany krok sprawdzania kondycji:

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Aktualizuj **stepGroups,** aby uwzględnić krok sprawdzania kondycji. **HealthCheckStep** jest wywoływana w **postDeploymentSteps** **stepGroup2**. **stepGroup3** i **stepGroup4** są wdrażane tylko wtedy, gdy stan dobrej kondycji jest *w dobrej kondycji* lub *ostrzeżenie*.

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

    Jeśli porównasz sekcję **stepGroup3** przed i po jej zmianie, ta sekcja zależy teraz od **stepGroup2**.  Jest to konieczne, gdy **stepGroup3** i kolejne grupy kroków zależą od wyników monitorowania kondycji.

    Poniższy zrzut ekranu ilustruje zmodyfikowane obszary i sposób użycia kroku sprawdzania kondycji:

    ![Szablon sprawdzania kondycji usługi Azure Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Wdrażanie topologii

Uruchom następujący skrypt programu PowerShell, aby wdrożyć topologię. Potrzebujesz tego samego **CreateADMServiceTopology.json** i **CreateADMServiceTopology.Parameters.json,** które zostały użyte w [usłudze Azure Deployment Manager z szablonami Menedżera zasobów](./deployment-manager-tutorial.md).

```azurepowershell
# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Sprawdź, czy topologia usługi oraz podstawowe zasoby zostały utworzone pomyślnie, korzystając z witryny Azure Portal:

![Samouczek dotyczący usługi Azure Deployment Manager — wdrożone zasoby topologii usługi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Opcja **Pokaż ukryte typy** musi być zaznaczona, aby wyświetlić zasoby.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Wdrażanie wdrożenia ze stanem w złej kondycji

Użyj adresu URL stanu w złej kondycji utworzonego w [trybie Tworzenie symulatora usługi sprawdzania kondycji](#create-a-health-check-service-simulator). Potrzebujesz poprawionego **createaDMServiceTopology.json** i tego samego **createaDMServiceTopology.Parameters.json,** który został użyty w [usłudze Azure Deployment Manager z szablonami Resource Manager](./deployment-manager-tutorial.md).

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
> `New-AzResourceGroupDeployment`jest wywołaniem asynchroniza. Komunikat o powodzenie oznacza tylko, że wdrożenie zostało pomyślnie rozpoczęte. Aby zweryfikować wdrożenie, użyj programu `Get-AZDeploymentManagerRollout`.  Zobacz następną procedurę.

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

Następujące przykładowe dane wyjściowe pokazują, że wdrożenie nie powiodło się ze względu na stan złej kondycji:

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

Po zakończeniu wdrażania zostanie wyświetlona jedna dodatkowa grupa zasobów utworzona dla zachodnich stanów USA.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Wdrażanie wdrożenia ze stanem dobrej kondycji

Powtórz tę sekcję, aby ponownie wdrożyć wdrożenie z adresem URL stanu w dobrej kondycji.  Po zakończeniu wdrażania zostanie wyświetlona jeszcze jedna grupa zasobów utworzona dla wschodnich stanów USA.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
2. Przejdź do nowo utworzonych aplikacji internetowych w obrębie nowych grup zasobów utworzonych przez wdrożenie wprowadzania.
3. Otwórz aplikację internetową w przeglądarce internetowej. Sprawdź lokalizację i wersję w pliku index.html.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Użyj pola **Filtruj według nazwy**, aby zawęzić listę grup zasobów utworzonych w tym samouczku. Powinny istnieć 3–4 grupy:

    * projectName>rg : zawiera zasoby Menedżera wdrażania. ** &lt;**
    * projectName>ServiceWUSrg : zawiera zasoby zdefiniowane przez ServiceWUS. ** &lt;**
    * projectName>ServiceEUSrg : zawiera zasoby zdefiniowane przez ServiceEUS. ** &lt;**
    * Grupa zasobów dla tożsamości zarządzanej zdefiniowanej przez użytkownika.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.
5. Powtórz dwa ostatnie kroki, aby usunąć inne grupy zasobów utworzone w ramach tego samouczka.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak korzystać z funkcji sprawdzania kondycji usługi Azure Deployment Manager. Aby dowiedzieć się więcej, zobacz [dokumentację usługi Azure Resource Manager](/azure/azure-resource-manager/).
