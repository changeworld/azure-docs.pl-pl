---
title: DevOps dla potoku pozyskiwania danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zastosować praktyki DevOps do implementacji potoku pozyskiwania danych używanego do przygotowywania danych do szkolenia modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247184"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps dla potoku pozyskiwania danych

W większości scenariuszy rozwiązanie pozyskiwania danych jest kompozycją skryptów, wywołania usługi i potoku aranżującego wszystkie działania. W tym artykule dowiesz się, jak zastosować praktyki DevOps do cyklu życia rozwoju wspólnego potoku pozyskiwania danych. Potok przygotowuje dane do szkolenia modelu uczenia maszynowego.

## <a name="the-solution"></a>Rozwiązanie

Należy wziąć pod uwagę następujący przepływ pracy pozyskiwania danych:

![potok pozyskiwania danych](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

W tym podejściu dane szkoleniowe są przechowywane w magazynie obiektów blob platformy Azure. Potok usługi Azure Data Factory pobiera dane z wejściowego kontenera obiektów blob, przekształca je i zapisuje dane w wyjściowym kontenerze obiektów blob. Ten kontener służy jako [magazyn danych](concept-data.md) dla usługi Azure Machine Learning. Po przygotowaniu danych potoku fabryki danych wywołuje potok uczenia maszynowego szkolenia do uczenia modelu. W tym konkretnym przykładzie transformacja danych jest wykonywana przez notes języka Python, uruchomiony w klastrze usługi Azure Databricks. 

## <a name="what-we-are-building"></a>Co budujemy

Podobnie jak w przypadku każdego rozwiązania programowego, pracuje nad nim zespół (na przykład Inżynierowie danych). 

![cicd-data-łękiwanie](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Współpracują i udostępniają te same zasoby platformy Azure, takie jak usługa Azure Data Factory, usługa Azure Databricks, konto usługi Azure Storage i takie. Kolekcja tych zasobów jest środowisko programistyczne. Inżynierowie danych przyczyniają się do tej samej bazy kodu źródłowego. Proces ciągłej integracji zestawia kod, sprawdza go za pomocą testów jakości kodu, testów jednostkowych i tworzy artefakty, takie jak testowany kod i szablony usługi Azure Resource Manager. Proces ciągłego dostarczania wdraża artefakty w środowiskach podrzędnych. W tym artykule pokazano, jak zautomatyzować procesy ciągłej integracji i dysków CD za pomocą [usługi Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Zarządzanie kontrolą źródła

Członkowie zespołu pracują na nieco inne sposoby współpracy nad kodem źródłowym notesu języka Python i kodem źródłowym usługi Azure Data Factory. Jednak w obu przypadkach kod jest przechowywany w repozytorium kontroli źródła (na przykład Azure DevOps, GitHub, GitLab), a współpraca jest zwykle oparta na jakimś modelu rozgałęzienia (na przykład [GitFlow).](https://datasift.github.io/gitflow/IntroducingGitFlow.html)

### <a name="python-notebook-source-code"></a>Kod źródłowy notesu języka Python

Inżynierowie danych pracują z kodem źródłowym notesu języka Python lokalnie w IDE (na przykład [Visual Studio Code)](https://code.visualstudio.com)lub bezpośrednio w obszarze roboczym Databricks. Ten ostatni daje możliwość debugowania kodu w środowisku programistycznym. W każdym przypadku kod zostanie scalony do repozytorium zgodnie z zasadami rozgałęzienia. Zaleca się przechowywanie kodu w `.py` plikach, `.ipynb` a nie w formacie notebooka Jupyter. Poprawia czytelność kodu i umożliwia automatyczne sprawdzanie jakości kodu w procesie ciągłej integracji.

### <a name="azure-data-factory-source-code"></a>Kod źródłowy fabryki danych platformy Azure

Kod źródłowy potoków usługi Azure Data Factory jest kolekcją plików json generowanych przez obszar roboczy. Zwykle inżynierowie danych pracują z projektantem wizualnym w obszarze roboczym usługi Azure Data Factory, a nie bezpośrednio z plikami kodu źródłowego. Skonfiguruj obszar roboczy za pomocą repozytorium kontroli źródła, zgodnie z opisem w [dokumentacji usługi Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration) Dzięki tej konfiguracji inżynierowie danych mogą współpracować nad kodem źródłowym zgodnie z preferowanym przepływem pracy rozgałęzienia.    

## <a name="continuous-integration-ci"></a>Ciągła integracja (CI)

Ostatecznym celem procesu ciągłej integracji jest zebranie wspólnej pracy zespołu z kodu źródłowego i przygotowanie go do wdrożenia w środowiskach podrzędnych. Podobnie jak w przypadku zarządzania kodem źródłowym ten proces jest inny dla notesów języka Python i potoków usługi Azure Data Factory. 

### <a name="python-notebook-ci"></a>Notebook Pythona CI

Proces ciągłej integracji notesów języka Python pobiera kod z gałęzi współpracy (na przykład ***wzorzec*** lub ***rozwijanie)*** i wykonuje następujące działania:
* Linting kodu
* Testowanie jednostek
* Zapisywanie kodu jako artefaktu

Poniższy fragment kodu demonstruje implementację tych kroków w potoku ***yaml*** usługi Azure DevOps:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

Potoku używa ***flake8*** do linting kodu Języka Python. Uruchamia testy jednostkowe zdefiniowane w kodzie źródłowym i publikuje wyniki linting i test, dzięki czemu są one dostępne na ekranie wykonywania potoku platformy Azure:

![linting-testy jednostkowe](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Jeśli linting i testowanie jednostkowe zakończy się pomyślnie, potok skopiuje kod źródłowy do repozytorium artefaktów, które ma być używane przez kolejne kroki wdrażania.

### <a name="azure-data-factory-ci"></a>Fabryka danych platformy Azure

Proces ciągłej integracji dla potoku usługi Azure Data Factory jest wąskim gardłem w całej historii ciągłej integracji/ciągłego wdrażania dla potoku pozyskiwania danych. Nie ma ***ciągłej*** integracji. Artefakt, który można wdrożyć dla usługi Azure Data Factory, to zbiór szablonów usługi Azure Resource Manager. Jedynym sposobem, aby utworzyć te szablony jest kliknięcie przycisku ***publikowania*** w obszarze roboczym usługi Azure Data Factory. Nie ma tu automatyzacji.
Inżynierowie danych łączą kod źródłowy ze swoich gałęzi funkcji z gałęzią współpracy, na przykład ***wzorcem*** lub ***rozwijaniem***. Następnie ktoś z przyznanymi uprawnieniami klika przycisk ***publikowania,*** aby wygenerować szablony usługi Azure Resource Manager z kodu źródłowego w gałęzi współpracy. Po kliknięciu przycisku obszar roboczy sprawdza poprawność potoków (pomyśl o nim jako linting i testowania jednostek), generuje szablony usługi Azure Resource Manager (pomyśl o tym jako o tworzeniu) i zapisuje wygenerowane szablony do gałęzi technicznej ***adf_publish*** w tym samym repozytorium kodu (pomyśl o tym jak o artefaktach publikowania). Ta gałąź jest tworzony automatycznie przez obszar roboczy usługi Azure Data Factory. Ten proces jest opisany w szczegółach w [dokumentacji usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment).

Ważne jest, aby upewnić się, że wygenerowane szablony usługi Azure Resource Manager są niezależne od środowiska. Oznacza to, że wszystkie wartości, które mogą się różnić od między środowiskami są parametryzowane. Usługa Azure Data Factory jest wystarczająco inteligentny, aby udostępnić większość takich wartości, jak parametry. Na przykład w poniższym szablonie właściwości połączenia z obszarem roboczym usługi Azure Machine Learning są udostępniane jako parametry:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Jednak można udostępnić właściwości niestandardowe, które nie są obsługiwane przez obszar roboczy usługi Azure Data Factory domyślnie. W scenariuszu tego artykułu potok usługi Azure Data Factory wywołuje notes języka Python przetwarzania danych. Notes akceptuje parametr o nazwie pliku danych wejściowych.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Ta nazwa jest inna w przypadku środowisk ***Dev,*** ***QA,*** ***UAT***i ***PROD.*** W złożonym potoku z wieloma działaniami może istnieć kilka właściwości niestandardowych. Dobrą praktyką jest zebranie wszystkich tych wartości w jednym miejscu i zdefiniowanie ich jako ***zmiennych potoku:***

![adf-zmienne](media/how-to-cicd-data-ingestion/adf-variables.png)

Działania potoku mogą odnosić się do zmiennych potoku, podczas gdy faktycznie z nich korzystają:

![adf-notebook-parametry](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Obszar roboczy usługi Azure Data Factory ***domyślnie nie*** udostępnia zmiennych potoku jako parametrów szablonów usługi Azure Resource Manager. Obszar roboczy używa [domyślnego szablonu parametryzacji,](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) który dyktuje, jakie właściwości potoku powinny być udostępniane jako parametry szablonu usługi Azure Resource Manager. Aby dodać zmienne potoku do listy, zaktualizuj sekcję "Microsoft.DataFactory/factorys/pipelines" [domyślnego szablonu parametryzacji](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) za pomocą następującego fragmentu kodu i umieść plik json wynikowy w katalogu głównym folderu źródłowego:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

W ten sposób wymusi obszar roboczy usługi Azure Data Factory, aby dodać zmienne do listy parametrów po kliknięciu przycisku ***publikowania:***

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

Wartości w pliku json są wartościami domyślnymi skonfigurowane w definicji potoku. Oczekuje się, że zostaną one zastąpione wartościami środowiska docelowego po wdrożeniu szablonu usługi Azure Resource Manager.

## <a name="continuous-delivery-cd"></a>Ciągłe dostarczanie (CD)

Proces ciągłego dostarczania pobiera artefakty i wdraża je w pierwszym środowisku docelowym. Upewnia się, że rozwiązanie działa przez uruchomienie testów. Jeśli się powiedzie, kontynuuje do następnego środowiska. Potok platformy Azure cd składa się z wielu etapów reprezentujących środowiska. Każdy etap zawiera [wdrożenia](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) i [zadania,](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) które wykonują następujące kroki:
* Wdrażanie notesu języka Python w obszarze roboczym usługi Azure Databricks
* Wdrażanie potoku usługi Azure Data Factory 
* Uruchamianie potoku
* Sprawdzanie wyniku pozyskiwania danych

Etapy potoku można skonfigurować za pomocą [zatwierdzeń](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) i [bram,](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) które zapewniają dodatkową kontrolę nad rozwojem procesu wdrażania w łańcuchu środowisk.

### <a name="deploy-a-python-notebook"></a>Wdrażanie notesu języka Python

Poniższy fragment kodu definiuje [wdrożenie](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) potoku platformy Azure, które kopiuje notes języka Python do klastra Databricks:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

Artefakty produkowane przez pw. ***$(Pipeline.Workspace)*** W takim przypadku zadanie wdrażania odnosi się do artefaktu ***di-notesów*** zawierającego notes języka Python. To [wdrożenie](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) używa [rozszerzenia Databricks Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) do kopiowania plików notesu do obszaru roboczego Databricks.
Etap ***Deploy_to_QA*** zawiera odwołanie do ***grupy zmiennych devops-ds-qa-vg*** zdefiniowanej w projekcie Azure DevOps. Kroki na tym etapie odnoszą się do zmiennych z tej grupy zmiennych (na przykład $(DATABRICKS_URL), $(DATABRICKS_TOKEN)). Chodzi o to, że następny etap (na przykład ***Deploy_to_UAT***) będzie działać z tymi samymi nazwami zmiennych zdefiniowanymi we własnej grupie zmiennych o zakresie UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Wdrażanie potoku usługi Azure Data Factory

Artefakt, który można wdrożyć dla usługi Azure Data Factory, to szablon usługi Azure Resource Manager. W związku z tym zostanie wdrożony za pomocą zadania ***wdrażania grupy zasobów platformy Azure,*** jak pokazano w następującym urywek:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
Wartość parametru data filename pochodzi ze zmiennej $(DATA_FILE_NAME) zdefiniowanej w grupie zmiennych etapowych kontroli jakości. Podobnie wszystkie parametry zdefiniowane w ***ARMTemplateForFactory.json*** mogą zostać zastąpione. Jeśli tak nie jest, używane są wartości domyślne.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Uruchamianie potoku i sprawdzanie wyniku pozyskiwania danych

Następnym krokiem jest upewnienie się, że wdrożone rozwiązanie działa. Następująca definicja zadania uruchamia potok usługi Azure Data Factory ze [skryptem programu PowerShell](https://github.com/microsoft/DataOps/tree/master/adf/utils) i wykonuje notes języka Python w klastrze usługi Azure Databricks. Notes sprawdza, czy dane zostały poprawnie pożyczone i sprawdza poprawność pliku danych wynikowego o nazwie $(bin_FILE_NAME).

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

Ostatnie zadanie w zadaniu sprawdza wynik wykonania notesu. Jeśli zwraca błąd, ustawia stan wykonania potoku nie powiodło się.

## <a name="putting-pieces-together"></a>Łączenie elementów

Wynikiem tego artykułu jest ci/CD azure potoku, który składa się z następujących etapów:
* CI
* Wdrażanie w celu kontroli jakości
    * Wdrażanie w programie Databricks + Wdrażanie w ujmiju ADF
    * Test integracji

Zawiera szereg etapów ***wdrażania*** równych liczbie posiadanych środowisk docelowych. Każdy etap ***wdrażania*** zawiera dwa wdrożenia, które są [uruchamiane](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) równolegle i [zadanie,](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) które jest uruchamiane po wdrożeniach w celu przetestowania rozwiązania w środowisku.

Przykładowa implementacja potoku jest montowana w następującym fragmentie ***kodu yaml:***

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Następne kroki

* [Kontrola źródła w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control)
* [Ciągła integracja i dostarczanie w fabryce danych platformy Azure](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps for Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
