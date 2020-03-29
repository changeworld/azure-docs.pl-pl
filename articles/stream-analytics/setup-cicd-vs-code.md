---
title: Wdrażanie zadania usługi Azure Stream Analytics przy użyciu pakietu npm ciągłej ciągłej integracji/dysków CD
description: W tym artykule opisano sposób używania pakietu npm ciągłej integracji/cd usługi Azure Stream Analytics do konfigurowania ciągłej integracji i wdrażania.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76962225"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Wdrażanie zadania usługi Azure Stream Analytics przy użyciu pakietu npm ciągłej ciągłej integracji/dysków CD 

Za pomocą pakietu Usługi Azure Stream Analytics CI/CD npm można skonfigurować proces ciągłej integracji i wdrażania dla zadań usługi Stream Analytics. W tym artykule opisano sposób używania pakietu npm w ogóle z dowolnym systemem ciągłej integracji/ciągłego wdrażania, a także szczegółowe instrukcje dotyczące wdrażania za pomocą usługi Azure Pipelines.

Aby uzyskać więcej informacji na temat wdrażania za pomocą programu Powershell, zobacz [wdrażanie przy pomocą pliku szablonu Usługi Resource Manager i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Więcej informacji na temat [używania obiektu jako parametru można uzyskać w szablonie Menedżera zasobów.](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)

## <a name="build-the-vs-code-project"></a>Tworzenie projektu vs code

Można włączyć ciągłą integrację i wdrażanie dla zadań usługi Azure Stream Analytics przy użyciu pakietu **asa-streamanalytics-cicd** npm. Pakiet npm zawiera narzędzia do generowania szablonów usługi Azure Resource Manager [projektów kodu programu Stream Analytics Visual Studio Code.](quick-create-vs-code.md) Może być używany w systemach Windows, macOS i Linux bez instalowania kodu programu Visual Studio.

Pakiet można [pobrać](https://www.npmjs.com/package/azure-streamanalytics-cicd) bezpośrednio lub zainstalować [globalnie](https://docs.npmjs.com/downloading-and-installing-packages-globally) za pomocą `npm install -g azure-streamanalytics-cicd` polecenia. Jest to zalecane podejście, które może być również używane w zadaniu skryptu interfejsu wiersza polecenia programu PowerShell lub platformy Azure potoku kompilacji w **potokach platformy Azure.**

Po zainstalowaniu pakietu użyj następującego polecenia, aby wysiedlić szablony usługi Azure Resource Manager. Argument **scriptPath** jest ścieżką bezwzględną do pliku **asaql** w projekcie. Upewnij się, że pliki asaproj.json i JobConfig.json znajdują się w tym samym folderze z plikiem skryptu. Jeśli **ścieżka wyjściowa** nie zostanie określona, szablony zostaną umieszczone w folderze **Wdrażanie** w folderze **bin** projektu.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Przykład (w systemie macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Gdy projekt programu Stream Analytics Visual Studio Code tworzy pomyślnie, generuje następujące dwa pliki szablonów usługi Azure Resource Manager w **folderze bin/[Debug/Retail]/Deploy:** 

*  Plik szablonu Menedżera zasobów

       [ProjectName].JobTemplate.json 

*  Plik parametrów Menedżera zasobów

       [ProjectName].JobTemplate.parameters.json   

Domyślne parametry w pliku parameters.json pochodzą z ustawień w projekcie programu Visual Studio Code. Jeśli chcesz wdrożyć w innym środowisku, należy odpowiednio zastąpić parametry.

> [!NOTE]
> Dla wszystkich poświadczeń wartości domyślne są ustawione na wartość null. Przed wdrożeniem w **chmurze należy** ustawić wartości.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Wdrażanie za pomocą usługi Azure Pipelines

W tej sekcji opisano sposób tworzenia potoków platformy Azure [tworzenie](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) i [zwalnianie](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) potoków przy użyciu npm.

Otwórz przeglądarkę sieci Web i przejdź do projektu usługi Azure Stream Analytics Visual Studio Code.

1. W obszarze **Potoki** w menu nawigacji po lewej stronie wybierz polecenie **Kompilacje**. Następnie wybierz **nowy potok**

   ![Tworzenie nowego potoku platformy Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Wybierz **pozycję Użyj edytora klasycznego,** aby utworzyć potok bez pliku YAML.

3. Wybierz typ źródła, projekt zespołowy i repozytorium. Następnie wybierz pozycję **Kontynuuj**.

   ![Wybierz projekt usługi Azure Stream Analytics](./media/setup-cicd-vs-code/select-repo.png)

4. Na stronie **Wybieranie szablonu** wybierz pozycję **Puste zadanie**.

### <a name="add-npm-task"></a>Dodawanie zadania npm

1. Na stronie **Zadania** wybierz znak plus obok **pozycji Agent zadanie 1**. Wpisz "npm" w wyszukiwaniu zadań i wybierz **npm**.

   ![Wybieranie zadania npm](./media/setup-cicd-vs-code/search-npm.png)

2. Nadaj zadaniu **nazwę wyświetlaną**. Zmień opcję **Polecenie** na *niestandardowe* i wprowadź następujące polecenie w **poleceniu i argumentach**. Pozostaw pozostałe opcje domyślne.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Wprowadzanie konfiguracji zadania npm](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Dodawanie zadania wiersza polecenia

1. Na stronie **Zadania** wybierz znak plus obok **pozycji Agent zadanie 1**. Wyszukaj **wiersz polecenia**.

2. Nadaj zadaniu **nazwę wyświetlaną** i wprowadź następujący skrypt. Zmodyfikuj skrypt za pomocą nazwy repozytorium i nazwy projektu.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Wprowadzanie konfiguracji zadania wiersza polecenia](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Dodawanie zadania plików kopiowania

1. Na stronie **Zadania** wybierz znak plus obok **pozycji Agent zadanie 1**. Wyszukaj **polecenie Kopiuj pliki**. Następnie wprowadź następujące konfiguracje.

   |Parametr|Dane wejściowe|
   |-|-|
   |Nazwa wyświetlana|Kopiuj pliki do: $(build.artifactstagingdirectory)|
   |Folder źródłowy|`$(system.defaultworkingdirectory)`| 
   |Spis treści| `**\Deploy\**` |
   |Folder docelowy| `$(build.artifactstagingdirectory)`|

   ![Wprowadzanie konfiguracji zadania kopiowania](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Dodawanie publikowania artefaktów kompilacji

1. Na stronie **Zadania** wybierz znak plus obok **pozycji Agent zadanie 1**. Wyszukaj **artefakty kompilacji Publikuj** i wybierz opcję z ikoną czarnej strzałki. 

2. Nie należy zmieniać żadnej z domyślnych konfiguracji.

### <a name="save-and-run"></a>Zapisz i uruchom

Po zakończeniu dodawania npm, wiersza polecenia, kopiowania plików i publikowania zadań artefaktów kompilacji wybierz pozycję **Zapisz & kolejki**. Po wyświetleniu monitu wprowadź komentarz zapisu i wybierz pozycję **Zapisz i uruchom**.

## <a name="release-with-azure-pipelines"></a>Zwolnij za pomocą potoków platformy Azure

Otwórz przeglądarkę sieci Web i przejdź do projektu usługi Azure Stream Analytics Visual Studio Code.

1. W obszarze **Potoki** w menu nawigacji po lewej stronie wybierz pozycję **Zwalnia**. Następnie wybierz **pozycję Nowy potok**.

2. Wybierz **zacznij od pustego zadania**.

3. W polu **Artefakty** wybierz pozycję **+ Dodaj artefakt**. W obszarze **Źródło**wybierz właśnie utworzony potok kompilacji i wybierz pozycję **Dodaj**.

   ![Wprowadź artefakt potoku kompilacji](./media/setup-cicd-vs-code/build-artifact.png)

4. Zmień nazwę **etapu 1** na **Wdrażanie zadania w środowisku testowym**.

5. Dodaj nowy etap i nadaj jego nazwę **Wdrażanie zadania w środowisku produkcyjnym**.

### <a name="add-tasks"></a>Dodawanie zadań

1. Z listy rozwijanej zadania wybierz pozycję **Wdrażanie zadania w środowisku testowym**. 

2. Wybierz **+** obok **pozycji Zadanie agenta** i wyszukaj *wdrożenie grupy zasobów platformy Azure*. Wprowadź następujące parametry:

   |Ustawienie|Wartość|
   |-|-|
   |Nazwa wyświetlana| *Wdrażanie myASAjob*|
   |Subskrypcja platformy Azure| Wybierz subskrypcję.|
   |Akcja| *Utwórz lub zaktualizuj grupę zasobów*|
   |Grupa zasobów| Wybierz nazwę grupy zasobów testowych, która będzie zawierać zadanie usługi Stream Analytics.|
   |Lokalizacja|Wybierz lokalizację grupy zasobów testowych.|
   |Lokalizacja szablonu| *Połączony artefakt*|
   |Szablon| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Parametry szablonu|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Zastąp parametry szablonu|-Input_IoTHub1_iotHubNamespace $(test_eventhubname)|
   |Tryb wdrażania|Przyrostowy|

3. Z listy rozwijanej zadania wybierz pozycję **Wdrażanie zadania w środowisku produkcyjnym**.

4. Wybierz **+** obok **pozycji Zadanie agenta** i wyszukaj *wdrożenie grupy zasobów platformy Azure*. Wprowadź następujące parametry:

   |Ustawienie|Wartość|
   |-|-|
   |Nazwa wyświetlana| *Wdrażanie myASAjob*|
   |Subskrypcja platformy Azure| Wybierz subskrypcję.|
   |Akcja| *Utwórz lub zaktualizuj grupę zasobów*|
   |Grupa zasobów| Wybierz nazwę grupy zasobów produkcyjnych, która będzie zawierać zadanie usługi Stream Analytics.|
   |Lokalizacja|Wybierz lokalizację grupy zasobów produkcyjnych.|
   |Lokalizacja szablonu| *Połączony artefakt*|
   |Szablon| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Parametry szablonu|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Zastąp parametry szablonu|-Input_IoTHub1_iotHubNamespace $(eventhubname)|
   |Tryb wdrażania|Przyrostowy|

### <a name="create-release"></a>Tworzenie wersji

Aby utworzyć wydanie, wybierz **pozycję Utwórz zwolnienie** w prawym górnym rogu.

![Tworzenie wersji przy użyciu usługi Azure Pipelines](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby użyć tożsamości zarządzanej dla usługi Azure Data Lake Store Gen1 jako ujścia danych wyjściowych, należy zapewnić dostęp do jednostki usługi przy użyciu programu PowerShell przed wdrożeniem na platformie Azure. Dowiedz się więcej o [wdrażaniu szablonu ADLS Gen1 z zarządzaną tożsamością za pomocą szablonu Menedżera zasobów](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania chmury usługi Azure Stream Analytics w programie Visual Studio Code (Wersja zapoznawcza)](quick-create-vs-code.md)
* [Testowanie zapytań usługi Stream Analytics lokalnie za pomocą kodu programu Visual Studio (wersja zapoznawcza)](visual-studio-code-local-run.md)
* [Eksploruj usługę Azure Stream Analytics za pomocą kodu programu Visual Studio (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)
