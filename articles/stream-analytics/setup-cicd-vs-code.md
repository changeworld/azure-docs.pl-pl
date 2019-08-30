---
title: Korzystanie z Azure Stream Analytics pakietem npm/CD
description: W tym artykule opisano, jak Azure Stream Analytics za pomocą pakietu npm/CD pozostała do konfigurowania ciągłej integracji i wdrażania.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: df9afaaeeb7e41c111fe6bd053047095a9cb9349
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173343"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>Korzystanie z Stream Analytics pakietem npm/CD
W tym artykule opisano Azure Stream Analytics, jak za pomocą pakietu npm/CD-ROM i skonfigurować ciągłą integrację i proces wdrażania.

## <a name="build-the-vs-code-project"></a>Kompiluj projekt VS Code

Możesz włączyć ciągłą integrację i wdrażanie dla Azure Stream Analytics zadań przy użyciu pakietu **ASA-streamanalytics-cicd** npm. Pakiet npm udostępnia narzędzia do generowania szablonów Azure Resource Manager [Stream Analytics projektów Visual Studio Code](quick-create-vs-code.md). Można go używać w systemach Windows, macOS i Linux bez instalowania Visual Studio Code.

Po pobraniu [pakietu](https://www.npmjs.com/package/azure-streamanalytics-cicd)Użyj następującego polecenia, aby uzyskać dane wyjściowe szablonów Azure Resource Manager. Argument **scriptPath** jest ścieżką bezwzględną do pliku **asaql** w projekcie. Upewnij się, że pliki asaproj. JSON i JobConfig. JSON znajdują się w tym samym folderze, w którym znajduje się plik skryptu. Jeśli **outputPath** nie zostanie określony, szablony zostaną umieszczone w folderze **Deploy** w folderze **bin** projektu.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Przykład (na macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

W przypadku pomyślnego skompilowania Stream Analytics Visual Studio Code projektu generowane są następujące dwa pliki szablonów Azure Resource Manager w folderze **bin/[Debug/retails]/Deploy** : 

*  Plik szablonu Menedżer zasobów

       [ProjectName].JobTemplate.json 

*  Plik parametrów Menedżer zasobów

       [ProjectName].JobTemplate.parameters.json   

Parametry domyślne w pliku Parameters. JSON pochodzą z ustawień w projekcie Visual Studio Code. Jeśli chcesz wdrożyć program w innym środowisku, Zastąp odpowiednio parametry.

> [!NOTE]
> Dla wszystkich poświadczeń wartości domyślne są ustawiane na wartość null. Musisz ustawić wartości przed wdrożeniem w chmurze.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Dowiedz się więcej na temat sposobu [wdrażania przy użyciu pliku szablonu Menedżer zasobów i Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Dowiedz się więcej na temat [używania obiektu jako parametru w szablonie Menedżer zasobów](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Aby użyć tożsamości zarządzanej dla Azure Data Lake Store Gen1 jako ujścia danych wyjściowych, musisz zapewnić dostęp do jednostki usługi przy użyciu programu PowerShell przed wdrożeniem na platformie Azure. Dowiedz się więcej na temat sposobu [wdrażania ADLS Gen1 z zarządzaną tożsamością przy użyciu szablonu Menedżer zasobów](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Następne kroki

* [Szybki start: Tworzenie Azure Stream Analytics zadania w chmurze w Visual Studio Code (wersja zapoznawcza)](quick-create-vs-code.md)
* [Testowanie Stream Analytics zapytań lokalnie za pomocą Visual Studio Code (wersja zapoznawcza)](vscode-local-run.md)
* [Eksplorowanie Azure Stream Analytics z Visual Studio Code (wersja zapoznawcza)](vscode-explore-jobs.md)
