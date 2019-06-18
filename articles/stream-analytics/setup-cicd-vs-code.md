---
title: Wykonuj ciągłe integrowanie i tworzyć aplikacje za pomocą pakietu npm usługi Azure Stream Analytics ciągłej integracji/ciągłego wdrażania
description: W tym artykule opisano jak skonfigurować ciągłą integrację i procesu wdrażania za pomocą pakietu npm usługi Azure Stream Analytics ciągłej integracji/ciągłego wdrażania.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: fa5a57afa379c6bbe027be80f400fc176800d289
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158503"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-cicd-npm-package"></a>Wykonuj ciągłe integrowanie i tworzenie przy użyciu pakietów Menedżera npm Stream Analytics ciągłej integracji/ciągłego wdrażania
W tym artykule opisano jak skonfigurować ciągłą integrację i procesu wdrażania za pomocą pakietu npm usługi Azure Stream Analytics ciągłej integracji/ciągłego wdrażania.

## <a name="build-the-vs-code-project"></a>Skompiluj projekt programu VS Code

Możesz włączyć ciągłej integracji i ciągłego wdrażania dla zadań usługi Azure Stream Analytics przy użyciu **asa-streamanalytics-cicd** pakietów Menedżera npm. Pakiet npm udostępnia narzędzia umożliwiające generowanie szablonów usługi Azure Resource Manager z [projektów Stream Analytics Visual Studio Code](quick-create-vs-code.md). Może służyć w Windows, macOS i Linux bez konieczności instalowania programu Visual Studio Code.

Po utworzeniu [pobrano pakiet](https://www.npmjs.com/package/azure-streamanalytics-cicd), użyj następującego polecenia w danych wyjściowych szablonów usługi Azure Resource Manager. **ScriptPath** argument jest ścieżka bezwzględna do **asaql** plik w projekcie. Upewnij się, że asaproj.json i JobConfig.json pliki znajdują się w tym samym folderze przy użyciu pliku skryptu. Jeśli **outputPath** nie zostanie określony, szablony zostaną umieszczone w **Wdróż** folder w węźle projektu **bin** folderu.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Przykład (w systemie macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Gdy projekt Stream Analytics Visual Studio Code pomyślnie skompilowana, generuje ona następujące pliki szablonów dwie usługi Azure Resource Manager w obszarze **bin / Debug na detalicznego / Deploy** folderu: 

*  Plik szablonu usługi Resource Manager

       [ProjectName].JobTemplate.json 

*  Plik parametrów usługi Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Domyślne parametry w pliku parameters.json pochodzą z ustawieniami w projekcie programu Visual Studio Code. Jeśli chcesz wdrożyć do innego środowiska, w związku z tym Zastąp parametry.

> [!NOTE]
> Za pomocą poświadczeń domyślnych wartości są ustawione na wartość null. Jesteś **wymagane** można ustawić wartości przed wdrożeniem w chmurze.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Dowiedz się więcej na temat [wdrażanie przy użyciu pliku szablonu usługi Resource Manager i programu Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Dowiedz się więcej na temat [używanie obiektu jako parametr w szablonie usługi Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Aby używać tożsamości zarządzanej dla usługi Azure Data Lake Store Gen1 jako ujścia danych wyjściowych, należy zapewnić dostęp do jednostki, przed wdrożeniem na platformie Azure przy użyciu programu PowerShell usługi. Dowiedz się więcej na temat [wdrażanie Gen1 Azure Data Lake Store za pomocą tożsamości zarządzanej przy użyciu szablonu usługi Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Kolejne kroki

* [Szybki start: Tworzenie zadania usługi Azure Stream Analytics w chmurze w programie Visual Studio Code (wersja zapoznawcza)](quick-create-vs-code.md)
* [Testowanie zapytań usługi Stream Analytics lokalnie za pomocą programu Visual Studio Code (wersja zapoznawcza)](vscode-local-run.md)
* [Eksplorowanie usługi Azure Stream Analytics za pomocą programu Visual Studio Code (wersja zapoznawcza)](vscode-explore-jobs.md)
