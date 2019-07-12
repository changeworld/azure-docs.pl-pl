---
title: Ciągłe dostarczanie funkcji aktualizacji kodu za pomocą usługi Azure DevOps — usługi Azure Functions
description: Dowiedz się, jak skonfigurować potok DevOps platformy Azure przeznaczonego usługi Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 0fdad0caa2deef0d7d55b30a85632f72f4ff0ecc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594456"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Ciągłe dostarczanie za pomocą DevOps platformy Azure

Automatyczne wdrażanie funkcji do aplikacji usługi Azure Functions przy użyciu [potoki Azure](/azure/devops/pipelines/).

Masz dwie opcje do definiowania potoku:

- **Plik YAML**: Plik YAML opisuje potoku. Ten plik może zawierać kroki kompilacji i sekcji wersji. Plik YAML musi być w tym samym repozytorium co aplikacja.
- **Szablon**: Szablony są gotowe do użycia zadań, które kompilowania lub wdrażania aplikacji.

## <a name="yaml-based-pipeline"></a>Na podstawie YAML potoku

Aby utworzyć potok na podstawie YAML, najpierw Utwórz swoją aplikację, a następnie wdrożyć tę aplikację.

### <a name="build-your-app"></a>Tworzenie aplikacji

Tworzenie aplikacji w potokach Azure zależy od języka programowania aplikacji. Każdy język ma czynności konkretnej kompilacji, które tworzenie artefaktów wdrożenia. Artefakt wdrażania jest używany do wdrażania aplikacji funkcji na platformie Azure.

#### <a name="net"></a>.NET

Poniższy przykład służy do tworzenia pliku YAML, aby utworzyć aplikację platformy .NET:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

Poniższy przykład służy do tworzenia pliku YAML, do tworzenia aplikacji w języku JavaScript:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

Poniższy przykład służy do tworzenia pliku YAML, do tworzenia aplikacji w języku Python. Python jest obsługiwana tylko w przypadku systemu Linux usługi Azure Functions.

```yaml
pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

Poniższy przykład służy do tworzenia pliku YAML, aby utworzyć pakiet aplikacji programu PowerShell. Program PowerShell jest obsługiwana tylko w przypadku systemu Windows Azure Functions.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>Wdrażanie aplikacji

W pliku YAML, w zależności od systemu operacyjnego hostingu, musi zawierać jedną z poniższych przykładów kodu YAML.

#### <a name="windows-function-app"></a>Aplikacja funkcji Windows

Poniższy fragment kodu służy do wdrażania aplikacji funkcji Windows:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Aplikacja funkcji w systemie Linux

Poniższy fragment kodu służy do wdrażania aplikacji funkcji systemu Linux:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Potok na podstawie szablonu

Szablony DevOps platformy Azure są wstępnie zdefiniowane grupy zadań, które kompilowania lub wdrażania aplikacji.

### <a name="build-your-app"></a>Tworzenie aplikacji

Tworzenie aplikacji w potokach Azure zależy od języka programowania aplikacji. Każdy język ma czynności konkretnej kompilacji, które tworzenie artefaktów wdrożenia. Artefakt wdrożenia używane do aktualizowania aplikacji funkcji na platformie Azure.

Aby użyć kompilacji wbudowanych szablonów, podczas tworzenia nowego potoku kompilacji, wybierz **Edytor Klasyczny** umożliwia tworzenie potoku za pomocą projektanta szablonów.

![Wybierz edytor klasyczny potoki usługi Azure](media/functions-how-to-azure-devops/classic-editor.png)

Po skonfigurowaniu źródła kodu, szablony kompilacji wyszukiwania dla usługi Azure Functions. Wybierz szablon, który jest zgodny z językiem aplikacji.

![Wybierz szablon usługi Azure Functions kompilacji](media/functions-how-to-azure-devops/build-templates.png)

W niektórych przypadkach artefaktów kompilacji mają strukturę określonego folderu. Może być konieczne wybranie **nazwy folderu głównego Prepend do ścieżek archiwizacji** pole wyboru.

![Możliwość dołączana nazwa folderu głównego](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplikacji JavaScript

Jeśli aplikacji JavaScript ma zależność od modułów macierzystych Windows, należy zaktualizować wersję puli agenta do **hostowany program VS2017**.

![Zaktualizuj wersję puli agenta](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Wdrażanie aplikacji

Podczas tworzenia nowego potoku wydania wyszukiwanie szablonu wydania usługi Azure Functions.

![Wyszukaj szablon wydania usługi Azure Functions](media/functions-how-to-azure-devops/release-template.png)

Wdrażanie do miejsca wdrożenia nie jest obsługiwane w szablonie wydania.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Tworzenie potoku kompilacji przy użyciu wiersza polecenia platformy Azure

Aby utworzyć potok kompilacji na platformie Azure, użyj `az functionapp devops-pipeline create` [polecenia](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). Potok kompilacji jest tworzony do kompilowania i wydawania żadnych zmian w kodzie, które zostały wprowadzone w repozytorium. Polecenie spowoduje wygenerowanie nowego pliku YAML, który definiuje potok kompilacji i wydania i zatwierdzeń go do repozytorium. Wymagania wstępne dotyczące tego polecenia są zależne od lokalizacji kodu.

- Jeśli swój kod w usłudze GitHub:

    - Konieczne jest posiadanie **zapisu** uprawnienia dla Twojej subskrypcji.

    - Musisz być administratorem projektu w DevOps platformy Azure.

    - Musi mieć uprawnienia do tworzenia usługi GitHub osobistego tokenu dostępu (PAT) które ma wystarczające uprawnienia. Aby uzyskać więcej informacji, zobacz [osobisty token dostępu GitHub wymagania dotyczące uprawnień.](https://aka.ms/azure-devops-source-repos)

    - Musi mieć uprawnienia do zatwierdzania do głównej gałęzi w repozytorium GitHub, dzięki czemu możesz zatwierdzić wygenerowany automatycznie plik YAML.

- Jeśli swój kod w repozytoriach usługi Azure:

    - Konieczne jest posiadanie **zapisu** uprawnienia dla Twojej subskrypcji.

    - Musisz być administratorem projektu w DevOps platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Przegląd [omówienia usługi Azure Functions](functions-overview.md).
- Przegląd [DevOps platformy Azure — omówienie](/azure/devops/pipelines/).
