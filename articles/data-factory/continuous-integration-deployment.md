---
title: Ciągła integracja i ciągłe dostarczanie w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść potoków usługi Data Factory z jednego środowiska (rozwoju, testowania, produkcji) do innego za pomocą ciągłej integracji i ciągłego dostarczania.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: 89ea3d576f20f6e62730b40e0e4f4f8d5f798142
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386992"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Ciągła integracja i dostarczanie (CI/CD) w usłudze Azure Data Factory

Ciągła Integracja jest rozwiązaniem polegającym na każdej ze zmian gotowe do testowania Twojej bazy kodu i automatycznie możliwie jak najszybciej. Ciągłe dostarczanie poniżej, testy, które będzie się działo podczas ciągłej integracji i wypychanie zmian do systemu przejściowych lub produkcyjnych.

Usługi Azure Data Factory ciągła integracja i dostarczanie oznacza, że przenoszenie potoków usługi Data Factory z jednego środowiska (rozwoju, testowania, produkcji) do innego. Celu ciągłej integracji i dostarczania umożliwia integrację interfejs użytkownika usługi Data Factory przy użyciu szablonów usługi Azure Resource Manager. Interfejs użytkownika usługi Data Factory można wygenerować szablonu usługi Resource Manager, po wybraniu **szablonu ARM** opcje. Po wybraniu **szablonu ARM wyeksportować**, portalu generuje szablonu usługi Resource Manager dla usługi data factory i pliku konfiguracji, który zawiera wszystkie ciągi połączeń i innych parametrów. Następnie należy utworzyć jeden plik konfiguracji dla poszczególnych środowisk (tworzenia, testowania, produkcji). Główny plik szablonu usługi Resource Manager pozostaje taka sama dla wszystkich środowisk.

Wprowadzenie dziewięć minutę i pokaz działania tej funkcji Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

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

-   Subskrypcji platformy Azure, połączone z Team Foundation Server lub repozytoriów platformy Azure przy użyciu [ *punktu końcowego usługi Azure Resource Manager*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Data Factory z skonfigurowano integrację z Git repozytoriów platformy Azure.

-   [Usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) zawierające wpisy tajne.

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
Zadanie usługi Azure Key Vault może zakończyć się niepowodzeniem z powodu błędu dostępu po raz pierwszy. Pobieranie dzienników w wersji, a następnie zlokalizuj `.ps1` plików za pomocą polecenia można nadać uprawnienia do agenta potoki usługi Azure. Polecenie można uruchomić bezpośrednio lub możesz skopiować identyfikator podmiotu zabezpieczeń z pliku i ręcznie dodać zasad dostępu w witrynie Azure portal. (*Uzyskać* i *listy* są minimalne uprawnienia wymagane).

### <a name="update-active-triggers"></a>Aktualizacja aktywnej wyzwalaczy
Wdrażanie może zakończyć się niepowodzeniem, jeśli zostanie podjęta próba aktualizacji active wyzwalaczy. Aby zaktualizować active wyzwalaczy, musisz ręcznie je uruchamiać i zatrzymywać ich po wdrożeniu. W tym celu można dodać zadania programu Azure Powershell, jak pokazano w poniższym przykładzie:

1.  Na karcie zadania w tej wersji, wyszukaj **programu Azure Powershell** i dodaj go.

1.  Wybierz **usługi Azure Resource Manager** jako połączenie typu, a następnie wybierz swoją subskrypcję.

1.  Wybierz **wbudowany skrypt** jako skrypt typu, a następnie podaj swój kod. Wyzwalacze zatrzymuje się w poniższym przykładzie:

    ```powershell
    $triggersADF = Get-AzureRmDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Można wykonać podobne kroki i użyć podobny kod (z `Start-AzureRmDataFactoryV2Trigger` funkcji) do ponownego uruchomienia wyzwalacze po wdrożeniu.

> [!IMPORTANT]
> Ciągła integracja i scenariusze wdrażania typu środowiska Integration Runtime w różnych środowiskach musi być taka sama. Na przykład, jeśli masz *może być samodzielnie hostowane* tego samego środowiska IR Integration Runtime (IR) w środowisku programistycznym, musi być typu *może być samodzielnie hostowane* w innych środowiskach, takich jak testowych i produkcyjnych również. Podobnie, jeśli udostępniasz środowiska integration Runtime na wiele etapów, należy skonfigurować urząd skarbowy jako *połączonej, może być samodzielnie hostowane* we wszystkich środowiskach, takich jak programowania, testowania i produkcji.

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

Poniżej przedstawiono przykładowy skrypt, aby zatrzymać wyzwalaczy przed przystąpieniem do wdrożenia i później ponownego uruchomienia wyzwalaczy. Skrypt zawiera również kod, aby usunąć zasoby, które zostały usunięte. Aby zainstalować najnowszą wersję programu Azure PowerShell, zobacz [Instalowanie programu Azure PowerShell na Windows przy użyciu funkcji PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.9.0).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder="$(env:System.DefaultWorkingDirectory)/Dev/",
    [parameter(Mandatory = $false)] [String] $armTemplate="$rootFolder\arm_template.json",
    [parameter(Mandatory = $false)] [String] $ResourceGroupName="sampleuser-datafactory",
    [parameter(Mandatory = $false)] [String] $DataFactoryName="sampleuserdemo2",
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true

)


$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzureRmDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and $_.properties.pipelines.Count -gt 0} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force }
}
else {

    #start Active triggers
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force }

    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzureRmDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #delte resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { Remove-AzureRmDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { Remove-AzureRmDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { Remove-AzureRmDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { Remove-AzureRmDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { Remove-AzureRmDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Parametry niestandardowe za pomocą szablonu usługi Resource Manager

Można zdefiniować niestandardowe parametry szablonu usługi Resource Manager. Po prostu musisz mieć w pliku o nazwie `arm-template-parameters-definition.json` w folderze głównym repozytorium. (Nazwa pliku musi odpowiadać nazwie, pokazano poniżej, dokładnie.) Data Factory próbuje odczytać plik, niezależnie od gałęzi, w którym aktualnie pracujesz w, ale nie tylko z gałęzi współpracy. Jeśli plik nie zostanie znaleziony, Data Factory używa domyślnych parametrów i wartości.

Poniższy przykład przedstawia przykładowy plik parametrów. Użyj tego przykładu jako odwołanie, aby utworzyć plik niestandardowych parametrów. Jeśli plik, który podasz nie jest w nieprawidłowym formacie JSON, Data Factory generuje komunikat o błędzie w konsoli przeglądarki i powraca do domyślnych parametrów i wartości widocznych na interfejs użytkownika usługi Data Factory.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {},
    "Microsoft.DataFactory/factories/integrationRuntimes": {
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
                    }
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
                    "connectionString": "|:-connectionString:secureString"
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
        }
    }
}
```
