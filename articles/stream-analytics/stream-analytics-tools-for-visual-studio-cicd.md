---
title: Wykonuj ciągłe integrowanie i tworzyć aplikacje za pomocą narzędzia Stream Analytics
description: W tym artykule opisano jak skonfigurować ciągłą integrację i procesu wdrażania za pomocą Visual Studio tools dla usługi Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2017
ms.openlocfilehash: 641254be37ac0019ee6a256fc99f96fc3bfb75a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761509"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>Wykonuj ciągłe integrowanie i tworzyć aplikacje za pomocą narzędzia Stream Analytics
W tym artykule opisano sposób użycia Konfigurowanie procesu ciągłej integracji i ciągłego wdrażania przy użyciu narzędzia Azure Stream Analytics dla programu Visual Studio.

Użyj wersji 2.3.0000.0 lub nowszy z [usługi Stream Analytics tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) uzyskać pomoc techniczną dotyczącą programu MSBuild.

Pakiet NuGet jest dostępna: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Zapewnia on program MSBuild, uruchamianie lokalne oraz narzędzia wdrażania, które obsługują proces ciągłej integracji i ciągłego wdrażania projektów programu Visual Studio dla usługi Stream Analytics. 
> [!NOTE]
> Pakiet NuGet może służyć tylko w przypadku 2.3.0000.0 lub nowszej wersji narzędzia Stream Analytics Tools for Visual Studio. W przypadku projektów utworzonych w poprzednich wersjach programu Visual Studio tools po prostu otwórz je za pomocą 2.3.0000.0 lub nowszej wersji, a następnie zapisz. Następnie nowe możliwości są włączone. 

Aby uzyskać więcej informacji, zobacz [usługi Stream Analytics tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Jak standardowe środowisko Visual Studio program MSBuild aby zbudować projekt masz dwie opcje. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz **kompilacji**. Możesz również użyć **MSBuild** pakietu NuGet w wierszu polecenia.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Gdy projekt programu Visual Studio Stream Analytics pomyślnie skompilowana, generuje ona następujące pliki szablonów dwie usługi Azure Resource Manager w obszarze **bin / Debug na detalicznego / Deploy** folderu: 

*  Plik szablonu usługi Resource Manager

       [ProjectName].JobTemplate.json 

*  Plik parametrów usługi Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Domyślne parametry w pliku parameters.json pochodzą z ustawień w projekcie programu Visual Studio. Jeśli chcesz wdrożyć do innego środowiska, w związku z tym Zastąp parametry.

> [!NOTE]
> Za pomocą poświadczeń domyślnych wartości są ustawione na wartość null. Jesteś *wymagane* można ustawić wartości przed wdrożeniem w chmurze.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Dowiedz się więcej na temat [wdrażanie przy użyciu pliku szablonu usługi Resource Manager i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Dowiedz się więcej na temat [używanie obiektu jako parametr w szablonie usługi Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Aby używać tożsamości zarządzanej dla usługi Azure Data Lake Store Gen1 jako ujścia danych wyjściowych, należy zapewnić dostęp do jednostki, przed wdrożeniem na platformie Azure przy użyciu programu PowerShell usługi. Dowiedz się więcej na temat [wdrażanie Gen1 Azure Data Lake Store za pomocą tożsamości zarządzanej przy użyciu szablonu usługi Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Narzędzie wiersza polecenia

### <a name="build-the-project"></a>Kompilowanie projektu
Pakiet NuGet jest narzędziem wiersza polecenia o nazwie **SA.exe**. Obsługuje ona kompilacja projektu i lokalnego testowania na dowolne maszyny, które można użyć w ciągłej integracji i ciągłego dostarczania procesu. 

Domyślnie pliki wdrożenia znajdują się w bieżącym katalogu. Można określić ścieżkę wyjściową przy użyciu następującego parametru - OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testowanie skrypt lokalnie

Jeśli projekt jest określona lokalne pliki wejściowe w programie Visual Studio, możesz uruchomić test zautomatyzowanego skryptu, używając *localrun* polecenia. Wynik wyjściowy jest umieszczany w bieżącym katalogu.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generuj plik definicji zadania, za pomocą interfejsu API z programu PowerShell Stream Analytics

*Ramię* polecenie przyjmuje szablonu zadania i pliki parametrów szablonu zadania wygenerowanych przez kompilację jako dane wejściowe. Następnie łączy je w plik JSON definicji zadania, który może służyć za pomocą interfejsu API z programu PowerShell Stream Analytics.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Przykład:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


