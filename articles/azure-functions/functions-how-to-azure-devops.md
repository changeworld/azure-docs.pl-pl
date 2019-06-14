---
title: Ciągłe dostarczanie za pomocą usługi Azure DevOps aktualizacji kodu — funkcja
description: Dowiedz się, jak skonfigurować potok DevOps platformy Azure przeznaczone dla usługi Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: ce57aae1119261c0545b59a037226fdc12ec115f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050661"
---
# <a name="continuous-delivery-using-azure-devops"></a>Ciągłe dostarczanie za pomocą DevOps platformy Azure

Można automatycznie wdrażać do aplikacji funkcji platformy Azure przy użyciu funkcji [potoki Azure](/azure/devops/pipelines/).
Aby zdefiniować potoku, można użyć:

- Plik YAML: Ten plik zawiera opis potoku, może mieć sekcję kroki kompilacji i sekcji wersji. Plik YAML powinien być w tym samym repozytorium co aplikacja.

- Szablony: Szablony są gotowe, wykonywane zadania, które kompilowania lub wdrażania aplikacji.

## <a name="yaml-based-pipeline"></a>Na podstawie YAML potoku

### <a name="build-your-app"></a>Tworzenie aplikacji

Kompilowanie aplikacji w potokach Azure zależy od języka programowania aplikacji.
Każdy język ma kroki konkretnej kompilacji, aby utworzyć artefaktów wdrożenia, który może służyć do wdrażania aplikacji funkcji na platformie Azure.

#### <a name="net"></a>.NET

Poniższy przykład służy do tworzenia pliku YAML, tworzenie aplikacji .NET.

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

Poniższy przykład służy do tworzenia pliku YAML, do tworzenia aplikacji JavaScript:

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

Poniższy przykład umożliwia tworzenie pliku YAML, do tworzenia aplikacji w języku Python, języka Python jest obsługiwana tylko w przypadku systemu Linux usługi Azure Functions:

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

Poniższy przykład umożliwia tworzenie pliku YAML, aby spakować aplikację programu PowerShell, programu PowerShell jest obsługiwana tylko w przypadku systemu Windows Azure Functions:

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

W zależności od obsługi systemu operacyjnego należy umieścić w poniższym przykładzie kodu YAML w pliku YAML.

#### <a name="windows-function-app"></a>Funkcja Windows aplikacji

Poniższy fragment kodu służy do wdrażania aplikacji funkcji Windows

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
```

#### <a name="linux-function-app"></a>Funkcja Linux aplikacji

Poniższy fragment kodu może służyć do wdrożenia aplikacji systemu Linux — funkcja

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
```

## <a name="template-based-pipeline"></a>Potok na podstawie szablonu

Szablony DevOps platformy Azure są wstępnie zdefiniowane grupy zadań, które kompilowania lub wdrażania aplikacji.

### <a name="build-your-app"></a>Tworzenie aplikacji

Kompilowanie aplikacji w potokach Azure zależy od języka programowania aplikacji. Każdy język ma kroki konkretnej kompilacji, aby utworzyć artefaktów wdrożenia, który może służyć do zaktualizowania aplikacji funkcji na platformie Azure.
Aby użyć kompilacji wbudowanych szablonów, podczas tworzenia nowego potoku kompilacji, wybierz **Edytor Klasyczny** umożliwia tworzenie potoku za pomocą projektanta szablonów

![Usługa Azure potoki Edytor klasyczny](media/functions-how-to-azure-devops/classic-editor.png)

Po skonfigurowaniu źródła kodu, wyszukiwanie szablonów kompilacji usługi Azure Functions, a następnie wybierz szablon, który jest zgodny z językiem aplikacji.

![Szablony kompilacji dla usługi Azure Functions](media/functions-how-to-azure-devops/build-templates.png)

W niektórych przypadkach artefaktów kompilacji mają strukturę określonego folderu i może być konieczne Sprawdź **nazwy folderu głównego Prepend do ścieżek archiwizacji** opcji.

![Dodaj Folder główny](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplikacji JavaScript

Jeśli aplikacja języka JavaScript są zależne od modułów macierzystych Windows, musisz zaktualizować:

- Wersja puli agentów do **hostowany program VS2017**

  ![Zmień agenta kompilacji systemu operacyjnego](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Wdrażanie aplikacji

Podczas tworzenia nowego potoku wydania, wyszukaj szablon wydania usługi Azure Functions.

![](media/functions-how-to-azure-devops/release-template.png)

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Tworzenie potoku usługi Azure przy użyciu wiersza polecenia platformy Azure

Za pomocą `az functionapp devops-pipeline create` [polecenia](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create), potoku usługi Azure zostaną utworzone do kompilowania i wydawania żadnych zmian w kodzie w repozytorium. Polecenie Wygeneruj nowy plik YAML, który definiuje potoku kompilacji i wydania, a następnie przekazać go do repozytorium.
Wymagania wstępne dla tego polecenia są zależne od lokalizacji kodu:

- Jeśli swój kod w usłudze GitHub:

    - Musisz mieć **zapisu** uprawnień do Twojej subskrypcji.

    - Jesteś administratorem projektu w DevOps platformy Azure.

    - Masz uprawnienia do tworzenia usługi GitHub osobisty Token dostępu z wystarczającymi uprawnieniami. [Wymagania dotyczące uprawnień osobisty token dostępu usługi GitHub.](https://aka.ms/azure-devops-source-repos)

    - Masz uprawnienia do zatwierdzania do głównej gałęzi w repozytorium GitHub, aby zatwierdzić wygenerowany automatycznie plik YAML.

- Jeśli swój kod w repozytoriach usługi Azure:

    - Musisz mieć **zapisu** uprawnień do Twojej subskrypcji.

    - Jesteś administratorem projektu w DevOps platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

+ [Azure Functions — omówienie](functions-overview.md)
+ [Omówienie usługi Azure DevOps](/azure/devops/pipelines/)
