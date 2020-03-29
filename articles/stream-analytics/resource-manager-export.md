---
title: Eksportowanie szablonu usługi Azure Stream Analytics dla usługi Azure Resource Manager
description: W tym artykule opisano sposób eksportowania szablonu usługi Azure Resource Manager dla zadania usługi Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968936"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Eksportowanie szablonu usługi Azure Stream Analytics dla usługi Azure Resource Manager

[Szablony usługi Azure Resource Manager](../azure-resource-manager/templates/overview.md) umożliwiają implementowanie infrastruktury jako kodu. Szablon jest plikiem notacji obiektu JavaScript (JSON), który definiuje infrastrukturę i konfigurację zasobów. Należy określić zasoby do wdrożenia i właściwości dla tych zasobów.

Możesz ponownie wdrożyć zadanie usługi Azure Stream Analytics, eksportując szablon usługi Azure Resource Manager.

## <a name="open-a-job-in-vs-code"></a>Otwieranie zadania w programie VS Code

Przed eksportem szablonu należy najpierw otworzyć istniejące zadanie usługi Stream Analytics w programie Visual Studio Code. 

Aby wyeksportować zadanie do projektu lokalnego, znajdź zadanie, które chcesz wyeksportować w **Eksploratorze analizy strumienia** w witrynie Azure portal. Na stronie **Kwerenda** wybierz pozycję **Otwórz w programie Visual Studio**. Następnie wybierz opcję **Visual Studio Code**.

![Otwórz zadanie usługi Stream Analytics w programie Visual Studio Code](./media/resource-manager-export/open-job-vs-code.png)

Aby uzyskać więcej informacji na temat używania programu Visual Studio Code do zarządzania zadaniami usługi Stream Analytics, zobacz [Szybki start kodu programu Visual Studio](quick-create-vs-code.md).

## <a name="compile-the-script"></a>Kompilowanie skryptu 

Następnym krokiem jest skompilowanie skryptu zadania do szablonu usługi Azure Resource Manager. Przed skompilowanie skryptu upewnij się, że zadanie ma co najmniej jedno dane wejściowe i jedno dane wyjściowe skonfigurowane. Jeśli nie jest skonfigurowane żadne dane wejściowe lub wyjściowe, należy najpierw skonfigurować dane wejściowe i wyjściowe.

1. W programie Visual Studio Code przejdź do pliku *Transformation.asaql* zadania.

   ![Plik Transformation.asaql w kodzie programu Visual Studio](./media/resource-manager-export/transformation-asaql.png)

1. Kliknij prawym przyciskiem myszy plik *Transformation.asaql* i wybierz polecenie **ASA: Compile Script** z menu.

1. Należy zauważyć, że folder **Wdrażanie** pojawia się w obszarze roboczym usługi Stream Analytics.

1. Zapoznaj się z *plikiem JobTemplate.json,* który jest szablonem usługi Azure Resource Management używanym do wdrażania.

## <a name="complete-the-parameters-file"></a>Uzupełnij plik parametrów

Następnie należy ukończyć plik parametrów szablonu usługi Azure Resource Management.

1. Otwórz plik *JobTemplate.parameters.json* znajdujący się w folderze **Wdrażanie** obszaru roboczego usługi Stream Analytics w programie Visual Studio Code.

1. Należy zauważyć, że klucze wejściowe i wyjściowe mają wartość null. Zastąp wartości null rzeczywistymi kluczami dostępu dla zasobów wejściowych i wyjściowych.

1. Zapisz plik parametrów.

## <a name="deploy-using-templates"></a>Wdrażanie przy użyciu szablonów

Możesz przystąpić do wdrażania zadania usługi Azure Stream Analytics przy użyciu szablonów usługi Azure Resource Manager wygenerowanych w poprzedniej sekcji.

W oknie programu PowerShell uruchom następujące polecenie. Pamiętaj, aby ponownie wykonać pliki *ResourceGroupName*, *TemplateFile*i *TemplateParameterFile* z rzeczywistą nazwą grupy zasobów oraz pełne ścieżki plików do plików *JobTemplate.json* i *JobTemplate.parameters.json* w **folderze Wdrażanie** obszaru roboczego zadania.

Jeśli nie masz skonfigurowany program Azure PowerShell, wykonaj kroki opisane w [module Zainstaluj program Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Następne kroki

* [Testowanie zadań usługi Azure Stream Analytics lokalnie przy użyciu danych wejściowych na żywo przy użyciu kodu programu Visual Studio](visual-studio-code-local-run-live-input.md)

* [Poznaj zadania usługi Azure Stream Analytics za pomocą kodu programu Visual Studio (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)