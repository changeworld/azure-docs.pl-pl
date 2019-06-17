---
title: Ciągła integracja i ciągłe dostarczanie w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść potoków usługi Data Factory z jednego środowiska (rozwoju, testowania, produkcji) do innego za pomocą ciągłej integracji i ciągłego dostarczania.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
author: gauravmalhot
ms.author: gamal
ms.reviewer: maghan
manager: craigg
ms.openlocfilehash: 76962975705ff53a292f41a0a54e42c5f2991a2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002585"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Ciągła integracja i dostarczanie (CI/CD) w usłudze Azure Data Factory

Ciągła Integracja jest rozwiązaniem polegającym na każdej ze zmian gotowe do testowania Twojej bazy kodu i automatycznie możliwie jak najszybciej. Ciągłe dostarczanie poniżej, testy, które będzie się działo podczas ciągłej integracji i wypychanie zmian do systemu przejściowych lub produkcyjnych.

Usługi Azure Data Factory ciągła integracja i dostarczanie oznacza, że przenoszenie potoków usługi Data Factory z jednego środowiska (rozwoju, testowania, produkcji) do innego. Celu ciągłej integracji i dostarczania umożliwia integrację interfejs użytkownika usługi Data Factory przy użyciu szablonów usługi Azure Resource Manager. Interfejs użytkownika usługi Data Factory można wygenerować szablonu usługi Resource Manager, po wybraniu **szablonu ARM** opcje. Po wybraniu **szablonu ARM wyeksportować**, portalu generuje szablonu usługi Resource Manager dla usługi data factory i pliku konfiguracji, który zawiera wszystkie ciągi połączeń i innych parametrów. Następnie należy utworzyć jeden plik konfiguracji dla poszczególnych środowisk (tworzenia, testowania, produkcji). Główny plik szablonu usługi Resource Manager pozostaje taka sama dla wszystkich środowisk.

Wprowadzenie dziewięć minutę i pokaz działania tej funkcji Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-manager-template-for-each-environment"></a>Utwórz szablon usługi Resource Manager dla każdego środowiska
Wybierz **szablonu ARM wyeksportować** eksportowania szablonu usługi Resource Manager dla fabryki danych w środowisku programistycznym.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Następnie przejdź do fabryki danych testowych i produkcyjnych fabryki danych i wybierz **szablonu ARM importu**.

![](media/continuous-integration-deployment/continuous-integration-image2.png)

Ta akcja spowoduje przejście do witryny Azure portal, gdzie można zaimportować wyeksportowanego szablonu. Wybierz **Utwórz własny szablon w edytorze** i następnie **Załaduj plik** i wybierz wygenerowany szablon usługi Resource Manager. Podaj ustawienia i data factory, a cały potok jest importowany w środowisku produkcyjnym.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Wybierz **Załaduj plik** wybierz wyeksportowany szablon usługi Resource Manager i podaj wszystkie wartości konfiguracji (na przykład, połączonych usług).

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**Parametry połączenia**. Można znaleźć informacje wymagane do utworzenia parametry połączenia zawierają artykuły dotyczące poszczególnych łączników. Na przykład usługi Azure SQL Database, zobacz [kopiowanie danych do i z usługi Azure SQL Database przy użyciu usługi Azure Data Factory](connector-azure-sql-database.md). Aby sprawdzić prawidłowych parametrów połączenia — dla połączonej usługi, na przykład — możesz również otworzyć widok kodu dla zasobu w interfejs użytkownika usługi Data Factory. W widoku kodu jednak część klucza, konto lub hasło parametrów połączenia zostaną usunięte. Aby otworzyć widok kodu, wybierz ikonę wyróżnione na poniższym zrzucie ekranu.

![Otwórz widok kodu, aby wyświetlić parametry połączenia](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>Cykl życia ciągłej integracji
Oto całego cyklu życia dla ciągłej integracji i dostarczania, którego można używać po włączeniu integrację z usługą Azure repozytoriów Git w interfejs użytkownika usługi Data Factory:

1.  Konfigurowanie tworzenia fabryki danych, korzystając z repozytoriów platformy Azure, w którym wszystkie deweloperzy mogą tworzyć zasoby usługi Data Factory, takie jak potoki, zestawy danych i tak dalej.

1.  Następnie deweloperzy mogą zmodyfikować zasoby, takie jak potoki. Ponieważ oni wprowadzić swoje zmiany, można wybrać **debugowania** aby zobaczyć, jak potok jest uruchamiany przy użyciu najnowszych zmian.

1.  Po deweloperów są odpowiednie zmiany, mogą utworzyć żądanie ściągnięcia z gałęzi do gałęzi głównej (lub gałęzi współpracy), można pobrać ich zmiany przeglądane przez elementy równorzędne.

1.  Po dokonaniu zmian w gałęzi głównej, można opublikować fabryką rozwoju w wybierając **Publikuj**.

1.  Gdy zespół jest gotowy do wspierania zmian fabryki testu i fabryki produkcji, ich można wyeksportować szablon usługi Resource Manager z głównej gałęzi lub dowolnej innej gałęzi w przypadku, gdy ich gałęzi głównej kopię na żywo tworzenia fabryki danych.

1.  Wyeksportowany szablon usługi Resource Manager można wdrożyć z różnymi plikami parametrów fabryki testu i fabryki produkcji.

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatyzowanie ciągła Integracja z usługą Azure potoki wydań

Poniżej przedstawiono procedurę konfigurowania wersji potoki usługi Azure, więc można zautomatyzować wdrażanie fabryki danych w wielu środowiskach.

![Diagram przedstawiający ciągłej integracji z potokiem, Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Wymagania

-   Subskrypcji platformy Azure, połączone z Team Foundation Server lub repozytoriów platformy Azure przy użyciu [*punktu końcowego usługi Azure Resource Manager*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Data Factory z skonfigurowano integrację z Git repozytoriów platformy Azure.

-    [Usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) zawierające wpisy tajne.

### <a name="set-up-an-azure-pipelines-release"></a>Ustawianie wersji potoki usługi Azure

1.  Przejdź do strony usługi repozytoriów platformy Azure, w tym samym projekcie jako skonfigurowane za pomocą usługi Data Factory.

1.  Kliknij menu u góry **potoki Azure** &gt; **wersji** &gt; **Tworzenie definicji wydania**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Wybierz **pusty procesu** szablonu.

1.  Wprowadź nazwę środowiska.

1.  Dodaj artefaktów usługi Git i wybierz w tym samym repozytorium, które są skonfigurowane przy użyciu usługi Data Factory. Wybierz `adf_publish` na gałąź domyślną z najnowszą wersją domyślne.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Dodaj zadanie wdrożenia usługi Resource Manager platformy Azure:

    a.  Utwórz nowe zadanie, wyszukaj **wdrożenie grupy zasobów Azure**i dodaj go.

    b.  W ramach zadania wdrażania wybierz subskrypcję, grupy zasobów i lokalizacji docelowej usługi Data Factory i podaj poświadczenia, jeśli to konieczne.

    c.  Wybierz **tworzenia lub aktualizacji grupy zasobów** akcji.

    d.  Wybierz **...** w **szablonu** pola. Przeglądaj w poszukiwaniu szablonu usługi Resource Manager (*ARMTemplateForFactory.json*) który został utworzony przez akcję publikowania w portalu. Ten plik w folderze `<FactoryName>` z `adf_publish` gałęzi.

    e.  Te same czynności wykonasz pliku parametrów. Wybierz właściwy plik, w zależności od tego, czy utworzono kopię lub używasz domyślnego pliku *ARMTemplateParametersForFactory.json*.

    f.  Wybierz **...** obok pozycji **przesłanianie parametrów szablonu** pola, a następnie wprowadź informacje dla elementu docelowego fabryki danych. Dla poświadczeń, które pochodzą z usługi key vault, użyj takiej samej nazwie klucza tajnego w następującym formacie: Nazwa klucza tajnego zakładając, że jest `cred1`, wprowadź `"$(cred1)"` (w cudzysłowie).

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. Wybierz **przyrostowe** tryb wdrożenia.

    > [!WARNING]
    > Jeśli wybierzesz **Complete** tryb wdrożenia, istniejące zasoby mogą zostać usunięte, wraz ze wszystkimi zasobami w docelowej grupie zasobów, które nie są zdefiniowane w szablonie usługi Resource Manager.

1.  Zapisz potoku tworzenia wersji.

1.  Tworzenie nowej wersji, w tym potoku wydania.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>Opcjonalnie — Pobierz wpisy tajne z usługi Azure Key Vault

W przypadku wpisów tajnych, aby przekazać szablonu usługi Azure Resource Manager zaleca się wraz z wydaniem potoki platformy Azure przy użyciu usługi Azure Key Vault.

Istnieją dwa sposoby, aby obsłużyć wpisy tajne:

1.  Dodawanie kluczy tajnych do pliku parametrów. Aby uzyskać więcej informacji, zobacz [użycia usługi Azure Key Vault do przekazywania wartości parametru secure podczas wdrażania](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Utwórz kopię pliku parametrów, który jest przekazywany do gałęzi publikowania i ustaw wartości parametrów, które chcesz pobrać z usługi key vault o następującym formacie:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   Gdy ta metoda klucz tajny są pobierane z usługi key vault automatycznie.

    -   Plik parametrów musi znajdować się w w gałęzi publikowania.

1.  Dodaj [zadania usługi Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) przed przystąpieniem do wdrożenia usługi Resource Manager platformy Azure, które są opisane w poprzedniej sekcji:

    -   Wybierz **zadania** kartę, Utwórz nowe zadanie, wyszukaj **usługi Azure Key Vault** i dodaj go.

    -   W ramach zadania usługi Key Vault, wybierz subskrypcję, w którym został utworzony magazyn kluczy, podaj poświadczenia, jeśli to konieczne, a następnie wybierz magazyn kluczy.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Udziel uprawnień do agenta potoki usługi Azure
Zadanie usługi Azure Key Vault może zakończyć się niepowodzeniem czasu środowiska uruchomieniowego fIntegration błąd odmowa dostępu. Pobieranie dzienników w wersji, a następnie zlokalizuj `.ps1` plików za pomocą polecenia można nadać uprawnienia do agenta potoki usługi Azure. Polecenie można uruchomić bezpośrednio lub możesz skopiować identyfikator podmiotu zabezpieczeń z pliku i ręcznie dodać zasad dostępu w witrynie Azure portal. (*Uzyskać* i *listy* są minimalne uprawnienia wymagane).

### <a name="update-active-triggers"></a>Aktualizacja aktywnej wyzwalaczy
Wdrażanie może zakończyć się niepowodzeniem, jeśli zostanie podjęta próba aktualizacji active wyzwalaczy. Aby zaktualizować active wyzwalaczy, musisz ręcznie je uruchamiać i zatrzymywać ich po wdrożeniu. W tym celu można dodać zadania programu Azure Powershell, jak pokazano w poniższym przykładzie:

1.  Na karcie zadania w tej wersji, wyszukaj **programu Azure Powershell** i dodaj go.

1.  Wybierz **usługi Azure Resource Manager** jako połączenie typu, a następnie wybierz swoją subskrypcję.

1.  Wybierz **wbudowany skrypt** jako skrypt typu, a następnie podaj swój kod. Wyzwalacze zatrzymuje się w poniższym przykładzie:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Można wykonać podobne kroki i użyć podobny kod (z `Start-AzDataFactoryV2Trigger` funkcji) do ponownego uruchomienia wyzwalacze po wdrożeniu.

> [!IMPORTANT]
> Ciągła integracja i scenariusze wdrażania typu środowiska Integration Runtime w różnych środowiskach musi być taka sama. Na przykład, jeśli masz *może być samodzielnie hostowane* tego samego środowiska IR Integration Runtime (IR) w środowisku programistycznym, musi być typu *może być samodzielnie hostowane* w innych środowiskach, takich jak testowych i produkcyjnych również. Podobnie, jeśli udostępniasz środowiska integration Runtime na wiele etapów, musisz skonfigurować środowiska Integration Runtime jako *połączonej, może być samodzielnie hostowane* we wszystkich środowiskach, takich jak programowania, testowania i produkcji.

## <a name="sample-deployment-template"></a>Przykładowy szablon wdrożenia

Poniżej przedstawiono przykładowy szablon wdrożenia, który można zaimportować w potokach platformy Azure.

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>Przykładowy skrypt, aby zatrzymać i ponownie uruchomić wyzwalaczy i wyczyścić

Poniżej przedstawiono przykładowy skrypt, aby zatrzymać wyzwalaczy przed przystąpieniem do wdrożenia i później ponownego uruchomienia wyzwalaczy. Skrypt zawiera również kod, aby usunąć zasoby, które zostały usunięte. Aby zainstalować najnowszą wersję programu Azure PowerShell, zobacz [Instalowanie programu Azure PowerShell na Windows przy użyciu funkcji PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Parametry niestandardowe za pomocą szablonu usługi Resource Manager

Jeśli jesteś w trybie GIT, można zastąpić domyślne właściwości w szablonie usługi Resource Manager, aby ustawić właściwości, które są parametryzowane szablonu i właściwości, które są zakodowane. Można zastąpić domyślny szablon parametryzacji w następujących scenariuszach:

* Użyj zautomatyzowanej ciągłej integracji/ciągłego Dostarczania i chcesz zmienić niektóre właściwości podczas wdrażania usługi Resource Manager, ale właściwości nie są parametryzowane domyślnie.
* Fabryką jest tak duża, że domyślny szablon usługi Resource Manager jest nieprawidłowa, ponieważ zawiera on więcej niż maksymalna dozwolona wartość parametrów (256).

W tych warunkach, aby zastąpić domyślny szablon parametryzacji, Utwórz plik o nazwie *arm szablonu parametrów definition.json* w folderze głównym repozytorium. Nazwa pliku musi dokładnie odpowiadać. Data Factory podejmie próbę odczytu tego pliku, niezależnie od gałęzi korzystasz obecnie w portalu usługi Azure Data Factory, ale nie tylko z gałęzi współpracy. Możesz utworzyć lub edytować plik z prywatnych gałęzi, który pozwala testować zmiany za pomocą **szablonu ARM wyeksportować** w interfejsie użytkownika. Następnie plik można scalić z gałęzią współpracy. Jeśli plik nie zostanie znaleziony, użyty zostanie szablon domyślny.


### <a name="syntax-of-a-custom-parameters-file"></a>Składnia plików niestandardowych parametrów

Oto niektóre wytyczne do użycia podczas tworzenia pliku parametrów niestandardowych. Plik, który składa się z sekcji dla każdego typu jednostki: wyzwalacz, potok, linkedservice, dataset, integrationruntime i tak dalej.
* Wprowadź ścieżkę właściwości w obszarze Typ odpowiedniej jednostki.
* Gdy ustawisz nazwę właściwości jako "\*'', wskazujesz, że chcesz zdefiniować parametry wszystkich właściwości na jej podstawie (tylko do poziomu pierwszego poziomu nie rekursywnie). Możesz też podać wszelkie wyjątki od tej reguły.
* Wartość właściwości jest ustawiony jako ciąg, wskazują, chcesz zdefiniować parametry właściwości. Użyj formatu `<action>:<name>:<stype>`.
   *  `<action>` może być jedną z następujących znaków:
      * `=` oznacza, że Zachowaj bieżącą wartość jako wartość domyślna parametru.
      * `-` oznacza, że nie jest przechowywana wartość domyślna parametru.
      * `|` jest przypadkiem szczególnym dotyczących wpisów tajnych w usłudze Azure Key Vault dla parametrów połączenia lub klucze.
   * `<name>` jest nazwą parametru. Jeśli to pole jest puste, przyjmuje nazwę właściwości. Jeśli wartość zaczyna się od `-` znak jest skrócona nazwa. Na przykład `AzureStorage1_properties_typeProperties_connectionString` będzie skrócony do `AzureStorage1_connectionString`.
   * `<stype>` jest typem parametru. Jeśli `<stype>` jest pusta, jest domyślnym typem `string`. Obsługiwane wartości: `string`, `bool`, `number`, `object`, i `securestring`.
* Po określeniu tablicy w pliku definicji wskazujesz, że dopasowania właściwości w szablonie jest tablicą. Fabryka danych wykonuje iterację przez wszystkie obiekty w tablicy przy użyciu definicji, który jest określony w obiekcie środowiska Integration Runtime w tablicy. Drugi obiekt ciągu, staje się nazwę właściwości, która jest używana jako nazwa parametru dla każdej iteracji.
* Nie jest możliwe definicji, które są specyficzne dla wystąpienia zasobu. Dowolna definicja ma zastosowanie do wszystkich zasobów tego typu.
* Domyślnie wszystkie ciągi bezpieczny, takie jak wpisy tajne usługi Key Vault i bezpieczne ciągów, takich jak parametry połączenia, klucze i tokeny, są parametryzowane.
 
## <a name="sample-parameterization-template"></a>Przykładowy szablon parametryzacji

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

### <a name="explanation"></a>Explanation:

#### <a name="pipelines"></a>Potoki
    
* Wszystkie właściwości w ścieżce działania/typeProperties/waitTimeInSeconds jest sparametryzowanych. Oznacza to, że wszystkie działania w potoku, który ma właściwość poziomie kodu o nazwie `waitTimeInSeconds` (na przykład `Wait` działania) jest sparametryzowane jako liczbę, o domyślnej nazwie. Jednak nie będzie mieć wartość domyślną w szablonie usługi Resource Manager. Podczas wdrażania usługi Resource Manager będzie wymagane dane wejściowe.
* Podobnie, właściwość o nazwie `headers` (na przykład w `Web` działania) jest sparametryzowana przy użyciu typu `object` (JObject). Ma wartość domyślną, która ma taką samą wartość jak fabryki źródła.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Tylko właściwości, a wszystkie właściwości w ścieżce `typeProperties` są parametryzowane z wartościami domyślnymi odpowiednich. Na przykład, począwszy od schematu obecnie istnieją dwie właściwości w obszarze **IntegrationRuntimes** właściwości typu: `computeProperties` i `ssisProperties`. Oba typy właściwości są tworzone przy użyciu odpowiednich domyślne wartości i typy (obiekt).

#### <a name="triggers"></a>Wyzwalacze

* W obszarze `typeProperties`, mają zdefiniowane dwie właściwości. Pierwsza z nich jest `maxConcurrency`, który jest określony, będzie mieć wartość domyślną, a typ może być `string`. Ma ona domyślną nazwę parametru `<entityName>_properties_typeProperties_maxConcurrency`.
* `recurrence` Sparametryzowane opcji właściwości również. Znajdujący się w nim wszystkie właściwości na tym poziomie są określone parametry powinny jako ciągi przy użyciu wartości domyślnych i nazw parametrów. Wyjątek stanowi `interval` właściwość, która jest sparametryzowane jako typ liczbowy i z nazwą parametru z sufiksem `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Podobnie `freq` właściwości jest ciągiem i jest sparametryzowane jako ciąg. Jednak `freq` właściwość jest sparametryzowane bez wartości domyślnej. Nazwa jest skrócony i sufiks. Na przykład `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Połączone usługi jest unikatowa. Ponieważ usługi połączone i zestawy danych potencjalnie może mieć kilka typów, możesz podać dostosowywania specyficznych dla typu. Na przykład może Załóżmy, że dla wszystkich połączonych usług typu `AzureDataLakeStore`, wybierz szablon będzie stosowany i dla wszystkich innych (za pośrednictwem \*) innego szablonu zostaną zastosowane.
* W powyższym przykładzie `connectionString` właściwości parametry powinny jako `securestring` wartość, nie będzie mieć wartość domyślną, a jej nazwę skróconą parametru, która jest sufiks `connectionString`.
* Właściwość `secretAccessKey`, jednak ma miejsce `AzureKeyVaultSecret` (na przykład `AmazonS3` połączoną usługę). W efekcie zostanie automatycznie sparametryzowany jako klucz tajny usługi Azure Key Vault i zostanie pobrany z magazynu kluczy, która jest konfigurowana w fabryce źródła. Można także sam parametryzuj magazynu kluczy.

#### <a name="datasets"></a>Zestawy danych

* Mimo że dostosowania specyficznych dla typu jest dostępna dla zestawów danych, konfiguracji można przekazać jawnie bez \*-konfiguracji na poziomie. W poprzednim przykładzie, wszystkie właściwości zestawu danych w ramach `typeProperties` są parametryzowane.

Można zmienić domyślnego parametryzacji szablonu, ale jest bieżącego szablonu. Będzie to przydatne, jeśli chcesz tylko dodać jedną dodatkową właściwość jako parametru, ale też, jeśli nie chcesz utracić parameterizations istniejących i muszą one utworzone ponownie.


```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

**Przykład**: Dodaj usługi Databricks interaktywne identyfikator klastra (z połączoną usługę Databricks) do pliku parametrów:

```
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```


## <a name="linked-resource-manager-templates"></a>Połączone szablony usługi Resource Manager

Jeśli skonfigurowano ciągłej integracji i ciągłego wdrażania (CI/CD) fabryk danych, można zaobserwować, że wraz z rozwojem większe fabryką napotkasz limity szablonu usługi Resource Manager, takich jak maksymalna liczba zasobów lub maksymalna ładunku w zasobie Szablon menedżera. Dla scenariuszy, takich jak te, wraz z generowania pełnego szablonu usługi Resource Manager dla fabryki Data Factory teraz również generuje szablonów połączonej usługi Resource Manager. Co w efekcie masz ładunku całej fabryki podzielone na kilka plików, tak aby nie wystąpiły wyżej limity.

Jeśli masz skonfigurowane usługi Git, połączonymi szablonami wygenerowana i zapisana wraz z pełną szablonów usługi Resource Manager w `adf_publish` gałęzi, w obszarze Nowy folder o nazwie `linkedTemplates`.

![Folder połączony szablonów usługi Resource Manager](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Szablony połączonej usługi Resource Manager mają zwykle głównego szablonu i zestawu szablonów podrzędny połączony z poziomu głównego. Nosi nazwę szablonu nadrzędnego `ArmTemplate_master.json`, o nazwie szablony podrzędnych za pomocą wzorca `ArmTemplate_0.json`, `ArmTemplate_1.json`i tak dalej. Aby przenieść z przy użyciu pełny szablon usługi Resource Manager do korzystania z połączonymi szablonami, należy zaktualizować Twojego zadania ciągłej integracji/ciągłego wdrażania, aby wskazywał `ArmTemplate_master.json` zamiast wskazujący `ArmTemplateForFactory.json` (czyli pełny szablon usługi Resource Manager). Resource Manager wymaga przekazania połączonymi szablonami na konto magazynu, dzięki czemu są one dostępne na platformie Azure podczas wdrażania. Aby uzyskać więcej informacji, zobacz [wdrażanie połączonymi szablonami ARM za pomocą usługi VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Pamiętaj, aby dodać skrypty fabryki danych w potoku ciągłej integracji/ciągłego wdrażania, przed i po nim zadania wdrażania.

Jeśli nie masz skonfigurowane w usłudze Git połączone szablony są dostępne za pośrednictwem **szablonu ARM wyeksportować** gestu.

## <a name="best-practices-for-cicd"></a>Najlepsze rozwiązania dotyczące ciągłej integracji/ciągłego wdrażania

Jeśli używasz integrację z usługą Git przy użyciu usługi data factory, a masz potok ciągłej integracji/ciągłego wdrażania, który przenosi zmiany od etapu programowania do testu, a następnie do środowiska produkcyjnego, zaleca się następujące najlepsze rozwiązania:

-   **Integracja z usługą Git**. Tylko należy skonfigurować tworzenia fabryki danych przy użyciu integrację z usługą Git. Zmiany testowych i produkcyjnych są wdrażane za pośrednictwem ciągłej integracji/ciągłego wdrażania, i nie trzeba również integrację z usługą Git.

-   **Skrypt ciągła Integracja/ciągłe wdrażanie fabryki danych**. Przed wykonaniem kroku wdrażania usługi Resource Manager w ciągłej integracji/ciągłego wdrażania należy zadbać rzeczy, takich jak zatrzymywanie wyzwalaczy i innego rodzaju oczyszczania fabryki. Firma Microsoft zaleca używanie [ten skrypt](#sample-script-to-stop-and-restart-triggers-and-clean-up) jak ta odpowiada za wszystkie te rzeczy. Uruchom skrypt, jeden raz przed wdrożeniem i po po przy użyciu odpowiednich flagi.

-   **Udostępnianie i środowiska Integration Runtime**. Środowiska Integration Runtime to jeden ze składników infrastrukturalnych w fabryce danych, które podlegają zmianom rzadziej i są podobne dla wszystkich etapów w ramach ciągłej integracji/ciągłego wdrażania. Co w efekcie usługi Data Factory oczekuje, że będziesz mieć taką samą nazwę i ten sam typ środowiska Integration Runtime we wszystkich etapów cyklu CI/CD. Jeśli chcesz udostępnić środowiska Integration Runtime we wszystkich etapów — na przykład własne środowiska Integration Runtime — jednym ze sposobów na udostępnianie jest hostingu własne środowisko IR w fabryce trójargumentowy, tylko dla zawierający udostępnionego środowiska Integration Runtime. Następnie można ich używać w Dev/Test/produkcji jako typ IR połączone.

-   **Magazyn kluczy**. Gdy używasz zalecanych usługi Azure Key Vault, na podstawie połączonych usług, możesz korzystać z jego zalety jeden poziom dalsze, potencjalnie utrzymywanie oddzielnych magazynów kluczy dla Dev/Test/produkcji. Można również skonfigurować poziomów uprawnień osobne dla każdego z nich. Może nie chcieć członków zespołu musi mieć uprawnienia do kluczy tajnych produkcji. Zalecamy również można zachować tych samych nazw kluczy tajnych na wszystkich etapach. Przechowywanie takich samych nazwach, nie trzeba zmieniać szablonów usługi Resource Manager w ciągłej integracji/ciągłego wdrażania, ponieważ jedynym elementem, który musi zostać zmieniona nazwa magazynu kluczy, który jest jeden z parametrów szablonu usługi Resource Manager.

## <a name="unsupported-features"></a>Nieobsługiwane funkcje

-   Nie można opublikować poszczególnych zasobów, ponieważ zależą od siebie nawzajem jednostek fabryki danych. Na przykład wyzwalacze są zależne od potoki, potoki są zależne od zestawów danych i innych potoki itp. Śledzenie zmiany zależności jest trudne. Jeśli jest to możliwe wybrać zasoby, aby ręcznie opublikować byłoby możliwe do wybrania tylko podzbiór cały zestaw zmian, które mogłyby prowadzić do nieoczekiwanego zachowania rzeczy po opublikowaniu.

-   Nie można opublikować z prywatnych gałęzi.

-   Nie można hostować projektów Bitbucket.
