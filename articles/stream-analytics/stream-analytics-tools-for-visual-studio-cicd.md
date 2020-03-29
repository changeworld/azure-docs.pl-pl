---
title: Korzystanie z pakietu NuGet usługi Azure Stream Analytics
description: W tym artykule opisano sposób używania pakietu NuGet usługi Azure Stream Analytics CI/CD do konfigurowania ciągłego procesu integracji i wdrażania.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 11e68aaa7c70d4f888c0009bc28d9bb90f431f3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354458"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Użyj pakietu Azure Stream Analytics CI/CD NuGet do integracji i programowania 
W tym artykule opisano sposób korzystania z pakietu Azure Stream Analytics CI/CD NuGet w celu skonfigurowania ciągłej integracji i procesu wdrażania.

Aby uzyskać pomoc techniczną dla usługi MSBuild, użyj wersji 2.3.0000.0 lub wyższej [narzędzia usługi Stream Analytics dla programu Visual Studio.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

Dostępny jest pakiet NuGet: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Zapewnia narzędzia MSBuild, uruchamianie lokalne i wdrażanie, które obsługują proces ciągłej integracji i wdrażania [projektów programu Stream Analytics Visual Studio.](stream-analytics-vs-tools.md) 
> [!NOTE]
> Pakiet NuGet może być używany tylko z wersją 2.3.0000.0 lub wyższej usługi Stream Analytics Tools for Visual Studio. Jeśli masz projekty utworzone w poprzednich wersjach narzędzi programu Visual Studio, po prostu otwórz je w wersji 2.3.0000.0 lub nowszej i zapisz. Następnie nowe możliwości są włączone. 

Aby uzyskać więcej informacji, zobacz [Narzędzia analizy strumienia dla programu Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Podobnie jak standardowe środowisko programu Visual Studio MSBuild, aby utworzyć projekt masz dwie opcje. Możesz kliknąć projekt prawym przyciskiem myszy, a następnie wybrać polecenie **Kompilacja**. Można również użyć **MSBuild** w pakiecie NuGet z wiersza polecenia.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Gdy projekt programu Stream Analytics Visual Studio tworzy pomyślnie, generuje następujące dwa pliki szablonów usługi Azure Resource Manager w **folderze bin/[Debug/Retail]/Deploy:** 

*  Plik szablonu Menedżera zasobów

       [ProjectName].JobTemplate.json 

*  Plik parametrów Menedżera zasobów

       [ProjectName].JobTemplate.parameters.json   

Domyślne parametry w pliku parameters.json pochodzą z ustawień w projekcie programu Visual Studio. Jeśli chcesz wdrożyć w innym środowisku, należy odpowiednio zastąpić parametry.

> [!NOTE]
> Dla wszystkich poświadczeń wartości domyślne są ustawione na wartość null. Przed wdrożeniem w **chmurze należy** ustawić wartości.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Dowiedz się więcej o [wdrażaniu za pomocą pliku szablonu Usługi Resource Manager i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Dowiedz się więcej o [używaniu obiektu jako parametru w szablonie Menedżera zasobów](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Aby użyć tożsamości zarządzanej dla usługi Azure Data Lake Store Gen1 jako ujścia danych wyjściowych, należy zapewnić dostęp do jednostki usługi przy użyciu programu PowerShell przed wdrożeniem na platformie Azure. Dowiedz się więcej o [wdrażaniu szablonu ADLS Gen1 z zarządzaną tożsamością za pomocą szablonu Menedżera zasobów](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Narzędzie wiersza polecenia

### <a name="build-the-project"></a>Kompilowanie projektu
Pakiet NuGet zawiera narzędzie wiersza polecenia o nazwie **SA.exe**. Obsługuje tworzenie projektów i lokalne testowanie na dowolnej maszynie, których można używać w procesie ciągłej integracji i ciągłego dostarczania. 

Pliki wdrażania są domyślnie umieszczane w bieżącym katalogu. Ścieżkę wyjściową można określić przy użyciu następującego parametru -OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testowanie skryptu lokalnie

Jeśli projekt określił lokalne pliki wejściowe w programie Visual Studio, można uruchomić test skryptu automatycznego przy użyciu polecenia *localrun.* Wynik wyjściowy jest umieszczany w bieżącym katalogu.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generowanie pliku definicji zadania do użycia w interfejsie API programu PowerShell usługi Stream Analytics

Polecenie *arm* przyjmuje szablon zadania i pliki parametrów szablonu zadania generowane za pomocą kompilacji jako dane wejściowe. Następnie łączy je w plik JSON definicji zadania, który może być używany z interfejsem API programu PowerShell usługi Stream Analytics.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Przykład:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania chmury usługi Azure Stream Analytics w programie Visual Studio](stream-analytics-quick-create-vs.md)
* [Testowanie zapytań usługi Stream Analytics lokalnie za pomocą programu Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Eksplorowanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio](stream-analytics-vs-tools.md)
