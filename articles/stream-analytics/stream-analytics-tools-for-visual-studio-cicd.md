---
title: Ciągłej integracji i opracowywania narzędzia analiza strumienia
description: W tym artykule opisano, jak za pomocą narzędzi Visual Studio dla usługi Azure Stream Analytics Konfigurowanie ciągłej integracji i procesu wdrażania.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 9/27/2017
ms.openlocfilehash: e4e831c602255df66f4c86ffa17336f51d2b52f7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>Ciągłej integracji i opracowywania narzędzia analiza strumienia
W tym artykule opisano sposób użycia na potrzeby ciągłej integracji i wdrażania proces narzędzia analiza strumienia Azure dla programu Visual Studio.

Użyj wersji 2.3.0000.0 lub nowszego z [Stream Analytics tools dla Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) uzyskać pomoc techniczną dotyczącą programu MSBuild.

Pakiet NuGet jest dostępny: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Oferuje MSBuild, przebiegu lokalnego i narzędzia wdrażania, które obsługują ciągłej integracji i wdrażania proces projektów programu Visual Studio analizy strumienia. 
> [!NOTE] 
Pakiet NuGet można tylko mający 2.3.0000.0 lub nowszą wersją narzędzia do analizy strumienia dla programu Visual Studio. Jeśli masz projektów utworzonych w poprzednich wersjach programu Visual Studio tools, wystarczy otworzyć, mający 2.3.0000.0 lub nowszą wersję i Zapisz. Włączać nowe funkcje. 

Aby uzyskać więcej informacji, zobacz [Stream Analytics tools dla Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Podobnie jak standardowe środowisko programu Visual Studio MSBuild aby zbudować projekt masz dwie opcje. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz **kompilacji**. Można też użyć **MSBuild** pakietu NuGet z wiersza polecenia.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Projektu programu Visual Studio analizy strumienia kompilacje pomyślnie, generuje następujące pliki szablonów dwóch usługi Azure Resource Manager w obszarze **bin / debugowania na detalicznego / Deploy** folderu: 

*  Plik szablonu usługi Resource Manager

       [ProjectName].JobTemplate.json 

*  Menedżer zasobów pliku parametrów

       [ProjectName].JobTemplate.parameters.json   

Domyślne parametry w pliku parameters.JSON następującym kodem są z ustawień projektu Visual Studio. Jeśli chcesz wdrożyć w innym środowisku, w związku z tym Zastąp parametry.

> [!NOTE] 
Za pomocą poświadczeń wartości domyślne są ustawione na wartość null. Jesteś *wymagane* można ustawić wartości przed wdrożeniem w chmurze.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Dowiedz się więcej o sposobie [wdrożyć przy użyciu pliku szablonu usługi Resource Manager i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Dowiedz się więcej o sposobie [obiekt używany jako parametr w szablonie usługi Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).


## <a name="command-line-tool"></a>Narzędzie wiersza polecenia

### <a name="build-the-project"></a>Kompilowanie projektu
Pakiet NuGet zawiera narzędzie wiersza polecenia o nazwie **SA.exe**. Obsługuje ona kompilacji projektu i testowania lokalnego na komputerze dowolnego, którego można użyć w ciągłej integracji i procesu ciągłego dostarczania. 

Domyślnie pliki wdrożenia znajdują się w bieżącym katalogu. Można określić ścieżki wyjściowej przy użyciu następującego parametru - OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Przetestować skrypt lokalnie

Jeśli projektu określono plików wejściowych lokalnych w programie Visual Studio, możesz uruchomić test zautomatyzowanego skryptu za pomocą *localrun* polecenia. Wynik wyjścia znajduje się w bieżącym katalogu.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generuj plik definicji zadania do użycia przy użyciu interfejsu API środowiska PowerShell usługi analiza strumienia

*Arm* polecenie przyjmuje szablonu zadania i pliki parametrów szablonu zadania wygenerowanych przez kompilację jako dane wejściowe. Następnie łączy je w pliku JSON definicji zadania, który może być używany z interfejs API środowiska PowerShell usługi analiza strumienia.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Przykład:
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


