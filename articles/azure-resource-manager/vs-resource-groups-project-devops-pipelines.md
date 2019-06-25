---
title: Ciągła Integracja/ciągłe dostarczanie za pomocą szablonów potoki usługi Azure i usługi Resource Manager
description: W tym artykule opisano sposób konfigurowania ciągłej integracji w potokach platformy Azure przy użyciu projekty wdrażania grupy zasobów platformy Azure w programie Visual Studio do wdrażania szablonów usługi Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191462"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integracja z szablonów usługi Resource Manager z potokiem, Azure

Visual Studio zawiera projekt grupy zasobów platformy Azure do tworzenia szablonów i wdrażanie ich do subskrypcji platformy Azure. Ten projekt można zintegrować z Azure potoków ciągłej integracji i ciągłego wdrażania (CI/CD).

Istnieją dwa sposoby wdrażania szablonów przy użyciu potoków usługi Azure:

* **Dodaj zadanie, które uruchamia skrypt programu Azure PowerShell**. Ta opcja ma tę zaletę, zapewniając spójność w całym cyklu, ponieważ używają tego samego skryptu, który znajduje się w projekcie programu Visual Studio (Wdróż AzureResourceGroup.ps1). Skrypt etapów artefakty z projektu do konta magazynu usługi Resource Manager można uzyskać dostęp. Artefakty są elementy w projekcie, takie jak połączonymi szablonami, skrypty i pliki binarne aplikacji. Następnie skrypt służy do wdrażania szablonu.

* **Dodawanie podzadań do kopiowania i wdrożenia zadań**. Tej opcji można używać zamiast do skryptu projektu. Należy skonfigurować dwa zadania w potoku. Jedno zadanie przygotowuje artefakty i inne zadanie służy do wdrażania szablonu.

W tym artykule opisano obie te metody.

## <a name="prepare-your-project"></a>Przygotowanie projektu programu

W tym artykule założono, że projekt programu Visual Studio i organizacji DevOps platformy Azure są gotowe do utworzenia potoku. Poniższe kroki pokazują jak upewnić się, że wszystko jest gotowe:

* Masz organizacji DevOps platformy Azure. Jeśli nie masz, [utworzyć jedno za darmo](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Jeśli Twój zespół ma już organizacji DevOps platformy Azure, upewnij się, że jesteś administratorem projektu DevOps platformy Azure, którego chcesz użyć.

* Skonfigurowano [połączenia usługi](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) do subskrypcji platformy Azure. Wykonywanie zadań w potoku w obszarze tożsamość jednostki usługi. Aby uzyskać instrukcje dotyczące tworzenia połączenia, zobacz [Utwórz projekt DevOps](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Masz projekt programu Visual Studio, który został utworzony na podstawie **grupy zasobów platformy Azure** początkowy szablon. Aby uzyskać informacji na temat tworzenia tego typu projektu, zobacz [tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* Projekt programu Visual Studio jest [podłączony do projektu DevOps platformy Azure](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Tworzenie potoku

1. Jeśli potok nie zostały dodane wcześniej, musisz utworzyć nowy potok. W Twojej organizacji DevOps platformy Azure wybierz **potoki** i **nowy potok**.

   ![Dodaj nowy potok](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Określ, gdzie znajduje się kod. Na poniższej ilustracji przedstawiono, wybierając **Azure repozytoriów Git**.

   ![Wybierz źródło kodu](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Wybierz repozytorium, które zawiera kod dla Twojego projektu z tego źródła.

   ![Wybierz repozytorium](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Wybierz typ potoku do utworzenia. Możesz wybrać **potoku Starter**.

   ![Wybierz potoku](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Możesz przystąpić do dodawania zadania programu Azure PowerShell lub Kopiuj plik i wdrożenia zadań.

## <a name="azure-powershell-task"></a>Zadanie usługi Azure PowerShell

W tej sekcji pokazano, jak skonfigurować ciągłe wdrażanie za pomocą pojedynczego zadania, które uruchamia skrypt programu PowerShell w projekcie. Tworzy następującego pliku YAML [zadań programu Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' 
    azurePowerShellVersion: LatestVersion
```

Po ustawieniu zadania na `AzurePowerShell@3`, potok używa poleceń z modułu AzureRM do uwierzytelniania połączenia. Domyślnie skrypt programu PowerShell w projekcie programu Visual Studio używa modułu AzureRM. Jeśli zaktualizowano skrypt, aby użyć [modułu Az](/powershell/azure/new-azureps-module-az), ustaw zadania `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Aby uzyskać `azureSubscription`, podaj nazwę połączenia usługi został utworzony.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Aby uzyskać `scriptPath`, podaj ścieżkę względną z pliku potoku do skryptu. Możesz przejrzeć w repozytorium, aby wyświetlić ścieżkę.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Jeśli nie potrzebujesz do etapu artefaktów, po prostu przekaż nazwę i lokalizację grupy zasobów do zastosowania podczas wdrażania. Skrypt Visual Studio tworzy grupę zasobów, jeśli jeszcze nie istnieje.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Jeśli zachodzi potrzeba etapu artefakty do istniejącego konta magazynu, użyj:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Teraz, że rozumiesz, jak utworzyć zadanie, Przejdźmy przez kroki, aby edytować potoku.

1. Otwórz potok i zastąp jego zawartość kodem swoje YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Wybierz pozycję **Zapisz**.

   ![Zapisz potoku](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Udostępnij komunikat dla zatwierdzenia i zatwierdź bezpośrednio **wzorca**.

1. Po wybraniu **Zapisz**, potok kompilacji jest uruchamiana automatycznie. Wróć do podsumowania dla potoku kompilacji, a następnie obejrzyj stanu.

   ![Wyświetlanie wyników](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Możesz wybrać aktualnie uruchomionych potoku, aby zobaczyć szczegółowe informacje o zadaniach. Po zakończeniu zobaczysz wyniki dla każdego kroku.

## <a name="copy-and-deploy-tasks"></a>Skopiuj i wdrożenia zadań

W tej sekcji pokazano, jak skonfigurować ciągłe wdrażanie przy użyciu dwóch zadań Przygotuj artefakty do wdrożenia szablonu. 

Pokazano w poniższym YAML [zadanie kopiowania plików platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Istnieje kilka elementów do poprawiania środowiska tego zadania. `SourcePath` Wskazuje lokalizację artefaktów względną do pliku potoku. W tym przykładzie pliki znajdują się w folderze o nazwie `AzureResourceGroup1` co było nazwę projektu.

```yaml
SourcePath: '<path-to-artifacts>'
```

Aby uzyskać `azureSubscription`, podaj nazwę połączenia usługi został utworzony.

```yaml
azureSubscription: '<your-connection-name>'
```

Nazwa magazynu i kontener Podaj nazwy konta magazynu i kontener, którego chcesz użyć do przechowywania artefaktów. Konto magazynu musi istnieć.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Pokazano w poniższym YAML [zadania wdrożenia grupy zasobów platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

Istnieje kilka elementów do poprawiania środowiska tego zadania. Aby uzyskać `azureSubscription`, podaj nazwę połączenia usługi został utworzony.

```yaml
azureSubscription: '<your-connection-name>'
```

Aby uzyskać `resourceGroupName` i `location`, podaj nazwę i lokalizację grupy zasobów, które mają zostać wdrożone na. Zadanie tworzy grupę zasobów, jeśli nie istnieje.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

Łącza do zadań wdrażania do szablonu o nazwie `WebSite.json` i plik parametrów o nazwie WebSite.parameters.json. Użyj nazw plików szablonu oraz parametrów.

Teraz, że rozumiesz sposób tworzenia zadania, Przejdźmy przez kroki, aby edytować potoku.

1. Otwórz potok i zastąp jego zawartość kodem swoje YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
   - task: AzureResourceGroupDeployment@2
     displayName: 'Deploy template'
     inputs:
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. Wybierz pozycję **Zapisz**.

1. Udostępnij komunikat dla zatwierdzenia i zatwierdź bezpośrednio **wzorca**.

1. Po wybraniu **Zapisz**, potok kompilacji jest uruchamiana automatycznie. Wróć do podsumowania dla potoku kompilacji, a następnie obejrzyj stanu.

   ![Wyświetlanie wyników](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Możesz wybrać aktualnie uruchomionych potoku, aby zobaczyć szczegółowe informacje o zadaniach. Po zakończeniu zobaczysz wyniki dla każdego kroku.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać instrukcje krok po kroku proces przy użyciu potoków platformy Azure przy użyciu szablonów usługi Resource Manager, zobacz [samouczka: Ciągła integracja szablonów usługi Azure Resource Manager za pomocą potoków Azure](resource-manager-tutorial-use-azure-pipelines.md).
