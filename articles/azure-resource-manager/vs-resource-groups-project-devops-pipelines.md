---
title: Ciągłej integracji/ciągłego wdrażania za pomocą szablonów Azure Pipelines i Menedżer zasobów
description: Opisuje sposób konfigurowania ciągłej integracji w Azure Pipelines przy użyciu projektów wdrażania grupy zasobów platformy Azure w programie Visual Studio w celu wdrażania szablonów Menedżer zasobów.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: ae896fa0820fbd25ed3f2d29c89fbcd56e7fd6f5
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982456"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integrowanie szablonów Menedżer zasobów z Azure Pipelines

Program Visual Studio udostępnia projekt grupy zasobów platformy Azure na potrzeby tworzenia szablonów i wdrażania ich w ramach subskrypcji platformy Azure. Można zintegrować ten projekt z Azure Pipelines na potrzeby ciągłej integracji i ciągłego wdrażania (CI/CD).

Istnieją dwa sposoby wdrażania szablonów przy użyciu Azure Pipelines:

* **Dodaj zadanie, które uruchamia skrypt Azure PowerShell**. Ta opcja ma zalety zapewnienia spójności w całym cyklu życia, ponieważ jest używany ten sam skrypt, który jest zawarty w projekcie programu Visual Studio (Deploy-AzureResourceGroup. ps1). Etapy skryptu są artefaktami z projektu do konta magazynu, do którego Menedżer zasobów może uzyskać dostęp. Artefakty to elementy w projekcie, takie jak połączone szablony, skrypty i pliki binarne aplikacji. Następnie skrypt wdraża szablon.

* **Dodawanie zadań do kopiowania i wdrażania zadań**. Ta opcja oferuje wygodną alternatywę dla skryptu projektu. W potoku konfiguruje się dwa zadania. Jednym etapem zadania artefakty, a inne zadanie wdraża szablon.

W tym artykule przedstawiono oba podejścia.

## <a name="prepare-your-project"></a>Przygotowywanie projektu

W tym artykule przyjęto założenie, że projekt programu Visual Studio i organizacja usługi Azure DevOps są gotowe do tworzenia potoku. W poniższych krokach pokazano, jak upewnić się, że wszystko jest gotowe:

* Masz organizację usługi Azure DevOps. Jeśli go nie masz, [Utwórz go bezpłatnie](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Jeśli Twój zespół ma już organizację usługi Azure DevOps, upewnij się, że jesteś administratorem projektu usługi Azure DevOps, którego chcesz użyć.

* Skonfigurowano [połączenie usługi](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) z subskrypcją platformy Azure. Zadania w potoku są wykonywane w ramach tożsamości nazwy głównej usługi. Aby uzyskać instrukcje dotyczące tworzenia połączenia, zobacz [Tworzenie projektu DevOps](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Masz projekt programu Visual Studio, który został utworzony na podstawie początkowego szablonu **grupy zasobów platformy Azure** . Aby uzyskać informacje na temat tworzenia tego typu projektu, zobacz [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* Projekt programu Visual Studio jest [połączony z projektem usługi Azure DevOps](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Tworzenie potoku

1. Jeśli potok nie został wcześniej dodany, należy utworzyć nowy potok. W organizacji usługi Azure DevOps wybierz pozycję **potoki** i **Nowy potok**.

   ![Dodaj nowy potok](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Określ miejsce przechowywania kodu. Na poniższej ilustracji przedstawiono Wybieranie **Azure Repos git**.

   ![Wybierz źródło kodu](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Z tego źródła wybierz repozytorium, które ma kod dla projektu.

   ![Wybieranie repozytorium](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Wybierz typ potoku, który ma zostać utworzony. Możesz wybrać pozycję **potok początkowy**.

   ![Wybierz potok](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Możesz dodać zadanie Azure PowerShell lub skopiować plik i wdrożyć zadania.

## <a name="azure-powershell-task"></a>Azure PowerShell, zadanie

W tej sekcji pokazano, jak skonfigurować ciągłe wdrażanie przy użyciu pojedynczego zadania, które uruchamia skrypt programu PowerShell w projekcie. Następujący plik YAML tworzy [zadanie Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

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

Po ustawieniu zadania na `AzurePowerShell@3`, potok używa poleceń z modułu AzureRM do uwierzytelniania połączenia. Domyślnie skrypt programu PowerShell w projekcie programu Visual Studio używa modułu AzureRM. Jeśli skrypt został zaktualizowany tak, aby korzystał z [modułu AZ module](/powershell/azure/new-azureps-module-az), Ustaw zadanie na `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

W `azureSubscription`polu podaj nazwę utworzonego połączenia z usługą.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

W `scriptPath`przypadku, podaj ścieżkę względną z pliku potoku do skryptu. Możesz wyszukać w repozytorium, aby zobaczyć ścieżkę.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Jeśli nie musisz przemieścić artefaktów, po prostu przekaż nazwę i lokalizację grupy zasobów, która ma zostać użyta do wdrożenia. Skrypt programu Visual Studio tworzy grupę zasobów, jeśli jeszcze nie istnieje.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Jeśli musisz przemieścić artefakty na istniejącym koncie magazynu, użyj:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Teraz, gdy zrozumiesz, jak utworzyć zadanie, przejdźmy do instrukcji, aby przeprowadzić edycję potoku.

1. Otwórz potok i Zastąp jego zawartość YAML:

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

   ![Zapisywanie potoku](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Podaj komunikat dotyczący zatwierdzenia i przekaż go bezpośrednio do serwera **głównego**.

1. Po wybraniu opcji **Zapisz**potok kompilacji zostanie automatycznie uruchomiony. Wróć do podsumowania potoku kompilacji i obejrzyj stan.

   ![Wyświetlanie wyników](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Możesz wybrać aktualnie uruchomiony potok, aby wyświetlić szczegółowe informacje o zadaniach. Po zakończeniu zobaczysz wyniki dla każdego kroku.

## <a name="copy-and-deploy-tasks"></a>Kopiowanie i wdrażanie zadań

W tej sekcji pokazano, jak skonfigurować ciągłe wdrażanie przy użyciu dwóch zadań do przygotowania artefaktów i wdrożenia szablonu. 

W poniższym YAML przedstawiono [zadanie kopiowania plików platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

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

Istnieje kilka części tego zadania, które można poprawić w danym środowisku. `SourcePath` Wskazuje lokalizację artefaktów względem pliku potoku. W tym przykładzie pliki znajdują się w folderze o nazwie `AzureResourceGroup1` , która jest nazwą projektu.

```yaml
SourcePath: '<path-to-artifacts>'
```

W `azureSubscription`polu podaj nazwę utworzonego połączenia z usługą.

```yaml
azureSubscription: '<your-connection-name>'
```

W polu Nazwa magazynu i kontenera Podaj nazwy konta magazynu i kontenera, które mają być używane do przechowywania artefaktów. Konto magazynu musi istnieć.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

W poniższym YAML przedstawiono [zadanie wdrażania grupy zasobów platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

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

Istnieje kilka części tego zadania, które można poprawić w danym środowisku. W `azureSubscription`polu podaj nazwę utworzonego połączenia z usługą.

```yaml
azureSubscription: '<your-connection-name>'
```

W `resourceGroupName` przypadku `location`systemów i podaj nazwę i lokalizację grupy zasobów, w której chcesz wdrożyć. Zadanie tworzy grupę zasobów, jeśli nie istnieje.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

Zadanie wdrażania łączy się z szablonem o `WebSite.json` nazwie i plikiem parametrów o nazwie website. Parameters. JSON. Użyj nazw szablonu i plików parametrów.

Teraz, gdy zrozumiesz, jak tworzyć zadania, przejdźmy do kroków, aby edytować potok.

1. Otwórz potok i Zastąp jego zawartość YAML:

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

1. Podaj komunikat dotyczący zatwierdzenia i przekaż go bezpośrednio do serwera **głównego**.

1. Po wybraniu opcji **Zapisz**potok kompilacji zostanie automatycznie uruchomiony. Wróć do podsumowania potoku kompilacji i obejrzyj stan.

   ![Wyświetlanie wyników](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Możesz wybrać aktualnie uruchomiony potok, aby wyświetlić szczegółowe informacje o zadaniach. Po zakończeniu zobaczysz wyniki dla każdego kroku.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje krok po kroku dotyczące korzystania z Azure Pipelines z szablonami Menedżer zasobów, [zobacz Samouczek: Ciągła integracja szablonów Azure Resource Manager z Azure Pipelines](resource-manager-tutorial-use-azure-pipelines.md).
