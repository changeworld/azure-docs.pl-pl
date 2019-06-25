---
title: Używanie usługi Azure Deployment Manager z szablonami usługi Resource Manager | Microsoft Docs
description: Użyj szablonów usługi Resource Manager z usługą Azure Deployment Manager, aby wdrażać zasoby platformy Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/31/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4d6832346fb25ee09d97575c755cb2d85eb73cbe
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206602"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Samouczek: Użyj sprawdzania kondycji w usłudze Azure Deployment Manager (publiczna wersja zapoznawcza)

Dowiedz się, jak zintegrować sprawdzenie kondycji w [Azure Deployment Manager](./deployment-manager-overview.md). Ten samouczek jest oparty o [użycia Azure Deployment Manager przy użyciu szablonów usługi Resource Manager](./deployment-manager-tutorial.md) samouczka. Należy wykonać ten samouczek, przed kontynuowaniem pracy z tym kontem.

W szablonie wdrożenia, używane w [użycia Azure Deployment Manager przy użyciu szablonów usługi Resource Manager](./deployment-manager-tutorial.md), używany jest krok oczekiwania. W tym samouczku należy zamienić kroku oczekiwania kroku sprawdzenia kondycji.

> [!IMPORTANT]
> Jeśli Twoja subskrypcja jest oznaczony do Canary w celu przetestowania nowych funkcji platformy Azure, Azure Deployment Manager można używać tylko do wdrożenia regiony Canary. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Utworzyć usługi symulatora sprawdzania kondycji
> * Popraw szablon wdrożenia
> * Wdrażanie topologii
> * Wdrażanie wdrożenie w stanie złej kondycji
> * Weryfikacja wdrożenia wdrożenia
> * Wdrażanie wdrożenie o dobrej kondycji
> * Weryfikacja wdrożenia wdrożenia
> * Oczyszczanie zasobów

Dodatkowe zasoby:

- [Odwołanie do interfejsu REST API usługi Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Próbka Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Pełne [użycia Azure Deployment Manager przy użyciu szablonów usługi Resource Manager](./deployment-manager-tutorial.md).
* Pobierz [szablonów i artefaktów](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) używanego w ramach tego samouczka.

## <a name="create-a-health-check-service-simulator"></a>Utworzyć usługi symulatora sprawdzania kondycji

W środowisku produkcyjnym zazwyczaj używa się co najmniej jeden dostawców monitorowania. W celu integracji kondycji tak proste, jak to możliwe, Microsoft pracował nad z niektórymi monitorowania firmy mogą udostępniać prostego kopiowania/wklejania rozwiązania do integracji kontroli kondycji z wdrożeniami kondycji najważniejszych usług. Aby uzyskać listę tych firm, zobacz [dostawców monitorowania kondycji](./deployment-manager-health-check.md#health-monitoring-providers). Na potrzeby tego samouczka utworzysz [funkcji platformy Azure](/azure/azure-functions/) do symulacji monitorowania usługi kondycji. Ta funkcja przyjmuje kod stanu i zwraca ten sam kod. Szablon Azure Deployment Manager używa kod stanu, aby określić jak przystąpić do wdrażania.

Następujące dwa pliki są używane do wdrażania funkcji platformy Azure. Nie trzeba pobierać te pliki do wykonywania kroków samouczka.

* Szablon usługi Resource Manager znajduje się w [ https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json ](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json). Możesz wdrożyć ten szablon służy do tworzenia funkcji platformy Azure.
* Plik zip kodu źródłowego funkcji platformy Azure, [ http://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip ](http://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip). Tego pliku zip o nazwie jest wywoływana przez szablon usługi Resource Manager.

Aby wdrożyć funkcję platformy Azure, wybierz **wypróbuj** aby otworzyć usługa Azure Cloud shell, a następnie wklej poniższy skrypt do okna powłoki.  Aby wkleić kod, kliknij prawym przyciskiem myszy w oknie shell, a następnie wybierz pozycję **Wklej**.

> [!IMPORTANT]
> **projectName** w programie PowerShell skrypt służy do generowania nazwy dla usług platformy Azure, które są wdrażane w ramach tego samouczka. Różne usługi platformy Azure mają różne wymagania dotyczące nazw. Aby upewnić się, że wdrożenie zakończy się pomyślnie, wybierz nazwę zawierającą mniej niż 12 znaków tylko z małych liter i cyfr.
> Zapisz kopię nazwę projektu. Możesz użyć tego samego projectName instrukcje z samouczka.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

Aby sprawdzić i przetestować funkcję platformy Azure:

1. Otwórz [portal Azure](https://portal.azure.com).
1. Otwórz grupę zasobów.  Domyślna nazwa jest nazwą projektu, przy użyciu **rg** dołączane.
1. Wybierz usługi app service z grupy zasobów.  Domyślna nazwa usługi app service jest nazwą projektu, przy użyciu **aplikacji sieci Web** dołączane.
1. Rozwiń **funkcje**, a następnie wybierz pozycję **HttpTrigger1**.

    ![Sprawdzenie kondycji usługi platformy Azure Deployment Manager funkcji platformy Azure](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Wybierz  **&lt;/ > Pobierz adres URL funkcji**.
1. Wybierz **kopiowania** można skopiować adres URL do Schowka.  Adres URL jest podobny do:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Zastąp `{healthStatus}` w adresie URL z kodem stanu. W tym samouczku **złej kondycji** do przetestowania scenariusza złej kondycji i użyj jednego **dobrej kondycji** lub **ostrzeżenie** do przetestowania scenariusza dobrej kondycji. Utwórz dwa adresy URL o złej kondycji, a druga z dobrej kondycji. Przykłady:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Potrzebne zarówno adresy URL, aby ukończyć ten samouczek.

1. Aby przetestować symulator monitorowania kondycji, należy otworzyć adresów URL, które zostały utworzone w ostatnim kroku.  Wyniki dla złej kondycji, są podobne do:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Popraw szablon wdrożenia

Ta sekcja ma na celu pokazują, jak dołączyć kroku sprawdzenia kondycji szablonu wdrożenia. Nie masz utworzyć własny plik CreateADMRollout.json do ukończenia tego samouczka. Szablon wprowadzania poprawione jest udostępniony w ramach konta magazynu, który jest używany w kolejnych sekcjach.

1. Otwórz **CreateADMRollout.json**. Ten plik JSON jest częścią pliki do pobrania.  Zobacz [Wymagania wstępne](#prerequisites).
1. Dodaj jeszcze z dwoma parametrami:

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

1. Zastąp definicję zasobu kroku oczekiwania, podając definicję zasobu kroku sprawdzenia kondycji:

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

    Na podstawie definicji, wdrażanie będzie kontynuowane, jeśli stan kondycji *dobrej kondycji* lub *ostrzeżenie*.

1. Aktualizacja **dependsON** definicji wdrożenia obejmujący kroku sprawdzenia kondycji nowo zdefiniowane:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Aktualizacja **stepGroups** obejmujący kroku sprawdzenia kondycji. **HealthCheckStep** jest wywoływana w **postDeploymentSteps** z **stepGroup2**. **stepGroup3** i **stepGroup4** są wdrażane tylko, jeśli stan kondycji *dobrej kondycji* lub *ostrzeżenie*.

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

    Jeśli porównasz **stepGroup3** sekcji przed i po niej poprawek w tej sekcji teraz zależy od **stepGroup2**.  Jest to konieczne, kiedy **stepGroup3** i grup w kolejnym kroku są zależne od wyników monitorowania kondycji.

    Poniższy zrzut ekranu przedstawia obszarów, modyfikować i korzystania z kroku sprawdzenia kondycji:

    ![Szablon sprawdzania kondycji w usłudze Azure Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Wdrażanie topologii

Aby uprościć samouczka, topologia szablonów i artefaktów są udostępniane w następujących lokalizacjach, dzięki czemu nie trzeba przygotować swoją własną kopią. Jeśli chcesz użyć własnego, postępuj zgodnie z instrukcjami [samouczka: Azure Deployment Manager za pomocą szablonów usługi Resource Manager](./deployment-manager-tutorial.md).

* Szablon topologii: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json
* Magazyn artefaktów: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Aby wdrożyć topologię, wybierz **wypróbuj** do Otwórz usługę Cloud shell, a następnie wklej skrypt programu PowerShell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

Sprawdź, czy topologia usługi oraz podstawowe zasoby zostały utworzone pomyślnie, korzystając z witryny Azure Portal:

![Samouczek dotyczący usługi Azure Deployment Manager — wdrożone zasoby topologii usługi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Opcja **Pokaż ukryte typy** musi być zaznaczona, aby wyświetlić zasoby.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Wdrażanie wdrożenie w stanie złej kondycji

Aby uprościć samouczka, szablon wprowadzania poprawione jest udostępniony w następujących lokalizacjach, dzięki czemu nie trzeba przygotować swoją własną kopią. Jeśli chcesz użyć własnego, postępuj zgodnie z instrukcjami [samouczka: Azure Deployment Manager za pomocą szablonów usługi Resource Manager](./deployment-manager-tutorial.md).

* Szablon topologii: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json
* Magazyn artefaktów: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Użyj adresu URL o złej kondycji, został utworzony w [utworzyć usługi symulatora sprawdzania kondycji](#create-a-health-check-service-simulator). Aby uzyskać **managedIdentityID**, zobacz [tworzenie zarządzanych tożsamości przypisanych przez użytkownika](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` to wywołanie asynchroniczne. Powodzenie komunikatu tylko oznacza, że wdrożenie zostało pomyślnie uruchomione. Aby zweryfikować wdrożenie, użyj `Get-AZDeploymentManagerRollout`.  Zobacz następną procedurę.

Aby sprawdzić postęp wdrożenia za pomocą następującego skryptu programu PowerShell:

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

Następujące przykładowe dane wyjściowe pokazuje wdrożenia nie powiodło się z powodu złej kondycji:

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

Po zakończeniu wdrożenia zostanie wyświetlona jedna grupa dodatkowych zasobów utworzonych dla regionu zachodnie stany USA.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Wdrażanie wdrożenia przy użyciu stanu kondycji

Powtórz tę sekcję, aby ponownie wdrożyć wdrożenie z adresem URL dobrej kondycji.  Po zakończeniu wdrożenia zostanie wyświetlona jedna więcej grupa zasobów utworzona w regionie wschodnie stany USA.

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

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób użycia funkcji sprawdzania kondycji systemu Azure Deployment Manager. Aby dowiedzieć się więcej, zobacz [dokumentację usługi Azure Resource Manager](/azure/azure-resource-manager/).
