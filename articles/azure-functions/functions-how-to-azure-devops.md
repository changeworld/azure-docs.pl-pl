---
title: Ciągłe aktualizowanie kodu aplikacji funkcji przy użyciu usługi Azure DevOps
description: Dowiedz się, jak skonfigurować potok azure devops przeznaczony dla usług Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.openlocfilehash: 5e2fc8fb06248e2cdad9067c56647da6d9626b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255749"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Ciągłe dostarczanie przy użyciu usługi Azure DevOps

Funkcję można automatycznie wdrożyć w aplikacji Usługi Azure Functions przy użyciu [usługi Azure Pipelines](/azure/devops/pipelines/).

Dostępne są dwie opcje definiowania potoku:

- **Plik YAML**: Plik YAML opisuje potok. Plik może mieć sekcję kroków kompilacji i sekcję wydania. Plik YAML musi znajdować się w tym samym repozytorium co aplikacja.
- **Szablon:** Szablony to gotowe zadania, które budują lub wdrażają aplikację.

## <a name="yaml-based-pipeline"></a>Potok oparty na YAML

Aby utworzyć potok oparty na YAML, najpierw skompiluj aplikację, a następnie wdrożyć aplikację.

### <a name="build-your-app"></a>Kompiluj aplikację

Sposób tworzenia aplikacji w potokach platformy Azure zależy od języka programowania aplikacji. Każdy język ma określone kroki kompilacji, które tworzą artefakt wdrożenia. Artefakt wdrożenia jest używany do wdrażania aplikacji funkcji na platformie Azure.

# <a name="c"></a>[C\#](#tab/csharp)

Do utworzenia pliku YAML w celu utworzenia aplikacji .NET można użyć następującego przykładu:

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
    modifyOutputPath: false
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
    artifactName: 'drop'
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Do utworzenia pliku YAML w celu utworzenia aplikacji JavaScript można użyć następującego przykładu:

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
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

Można użyć jednego z następujących przykładów, aby utworzyć plik YAML do tworzenia aplikacji dla określonej wersji języka Python. Python jest obsługiwany tylko dla aplikacji funkcyjnych działających w systemie Linux.

**Wersja 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Wersja 3.6**

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
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Poniższy przykład służy do tworzenia pliku YAML do pakowania aplikacji programu PowerShell. Program PowerShell jest obsługiwany tylko dla systemu Windows Azure Functions.

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
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Wdrażanie aplikacji

W zależności od systemu operacyjnego hostingu należy dołączyć do pliku YAML jedną z następujących próbek YAML.

#### <a name="windows-function-app"></a>Aplikacja funkcji systemu Windows

Do wdrożenia aplikacji funkcji systemu Windows można użyć następującego fragmentu kodu:

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

#### <a name="linux-function-app"></a>Aplikacja funkcji Linux

Do wdrożenia aplikacji z funkcją systemu Linux można użyć następującego fragmentu kodu:

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

## <a name="template-based-pipeline"></a>Potok oparty na szablonie

Szablony w usłudze Azure DevOps są wstępnie zdefiniowane grupy zadań, które kompilacji lub wdrażania aplikacji.

### <a name="build-your-app"></a>Kompiluj aplikację

Sposób tworzenia aplikacji w potokach platformy Azure zależy od języka programowania aplikacji. Każdy język ma określone kroki kompilacji, które tworzą artefakt wdrożenia. Artefakt wdrożenia służy do aktualizowania aplikacji funkcji na platformie Azure.

Aby użyć wbudowanych szablonów kompilacji, podczas tworzenia nowego potoku kompilacji, wybierz pozycję **Użyj edytora klasycznego,** aby utworzyć potok przy użyciu szablonów projektanta.

![Wybierz edytor klasyczny usługi Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

Po skonfigurowaniu źródła kodu wyszukaj szablony kompilacji usługi Azure Functions. Wybierz szablon, który pasuje do twojego języka aplikacji.

![Wybieranie szablonu kompilacji usług Azure Functions](media/functions-how-to-azure-devops/build-templates.png)

W niektórych przypadkach artefakty kompilacji mają określoną strukturę folderów. Może być konieczne zaznaczenie pola wyboru **Nazwa folderu głównego dołączania do archiwizacji ścieżek.**

![Opcja dołączania nazwy folderu głównego](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplikacje JavaScript

Jeśli aplikacja JavaScript ma zależność od modułów natywnych systemu Windows, należy zaktualizować wersję puli agentów do **hostowanego programu VS2017**.

![Aktualizowanie wersji puli agentów](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Wdrażanie aplikacji

Podczas tworzenia nowego potoku wydania wyszukaj szablon wydania usługi Azure Functions.

![Wyszukiwanie szablonu wydania usługi Azure Functions](media/functions-how-to-azure-devops/release-template.png)

Wdrażanie w gnieździe wdrażania nie jest obsługiwane w szablonie wydania.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Tworzenie potoku kompilacji przy użyciu interfejsu wiersza polecenia platformy Azure

Aby utworzyć potok kompilacji na `az functionapp devops-pipeline create` platformie Azure, użyj [polecenia](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). Potok kompilacji jest tworzony do tworzenia i zwalniania wszelkich zmian kodu, które są wprowadzane w repozytorium. Polecenie generuje nowy plik YAML, który definiuje potok kompilacji i wydania, a następnie zatwierdza go do repozytorium. Wymagania wstępne dla tego polecenia zależą od lokalizacji kodu.

- Jeśli kod znajduje się w usłudze GitHub:

    - Musisz mieć uprawnienia **do zapisu** dla subskrypcji.

    - Musisz być administratorem projektu w usłudze Azure DevOps.

    - Musisz mieć uprawnienia do tworzenia tokenu dostępu osobistego GitHub (PAT), który ma wystarczające uprawnienia. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące uprawnień GItHub PAT.](https://aka.ms/azure-devops-source-repos)

    - Musisz mieć uprawnienia do zatwierdzania do gałęzi głównej w repozytorium GitHub, dzięki czemu można zatwierdzić automatyczniegenerowany plik YAML.

- Jeśli kod znajduje się w repozytorium platformy Azure:

    - Musisz mieć uprawnienia **do zapisu** dla subskrypcji.

    - Musisz być administratorem projektu w usłudze Azure DevOps.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [omówieniem usługi Azure Functions](functions-overview.md).
- Przejrzyj [omówienie usługi Azure DevOps](/azure/devops/pipelines/).
