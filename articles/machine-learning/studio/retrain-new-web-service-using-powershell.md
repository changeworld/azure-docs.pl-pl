---
title: Ponowne szkolenie przy użyciu programu PowerShell usługi sieci web nowej usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Dowiedz się, jak programowe ponowne trenowanie modelu i zaktualizować usługę sieci web, aby używać nowo uczonego modelu w usłudze Azure Machine Learning, za pomocą poleceń cmdlet programu PowerShell usługi Machine Learning Management.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 069a3022cf9b6423b95e8f9f35686965d2654be7
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631082"
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Ponowne szkolenie usługi sieci web opartych na nowe Resource Manager przy użyciu poleceń cmdlet programu PowerShell usługi Machine Learning Management
Gdy retrain się nowej usługi sieci web, należy zaktualizować definicji usługi predykcyjne sieci web, aby odwoływać się do nowego modelu uczonego.

## <a name="prerequisites"></a>Wymagania wstępne
Należy skonfigurować eksperyment i eksperyment predykcyjny, jak pokazano w [Retrain Machine Learning models programowo](retrain-models-programmatically.md).

> [!IMPORTANT]
> Należy wdrożyć eksperyment predykcyjny jako usługi Azure Resource Manager (nowy) na podstawie usługę machine learning w sieci web.
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia w ramach subskrypcji, do której możesz wdrażanie usługi sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md).

Aby uzyskać dodatkowe informacje na temat usługi sieci web wdrażanie, zobacz [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).

Ten proces wymaga zainstalowanego polecenia cmdlet usługi Azure Machine Learning. Uzyskać instalowania poleceń cmdlet usługi Machine Learning, zobacz [polecenia cmdlet usługi Azure Machine Learning](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/) odwołania w witrynie MSDN.

Z ponownego trenowania danych wyjściowych, należy skopiować następujące informacje:

* BaseLocation
* RelativeLocation

Dostępne są następujące kroki, które należy wykonać:

1. Zaloguj się do swojego konta usługi Azure Resource Manager.
2. Pobierz definicję usługi sieci web
3. Eksportowanie definicji usługi sieci Web jako dane JSON
4. Aktualizuj odwołanie do obiektu blob ilearner w formacie JSON.
5. Importuj dane JSON w definicji usługi sieci Web
6. Aktualizacja usługi sieci web za pomocą nowych definicji usługi sieci Web

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Zaloguj się do swojego konta usługi Azure Resource Manager
Najpierw musisz zarejestrować się do konta platformy Azure z w obrębie za pomocą środowiska PowerShell [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) polecenia cmdlet.

## <a name="get-the-web-service-definition"></a>Pobierz definicję usługi sieci Web
Następnie Pobierz usługę sieci Web, wywołując [Get AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservice) polecenia cmdlet. Definicja usługi sieci Web jest wewnętrznej reprezentacji uczonego modelu usługi sieci web i nie można bezpośrednio modyfikować. Upewnij się, Twoje eksperyment predykcyjny i nie eksperymentu szkolenia pobierają definicji usługi sieci Web.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Aby określić nazwę grupy zasobów istniejącej usługi sieci web, należy uruchomić polecenie cmdlet Get-AzureRmMlWebService bez żadnych parametrów, aby wyświetlić usługi sieci web w ramach subskrypcji. Znajdź usługę sieci web, a następnie Przyjrzyj się jego identyfikatora usługi internetowej. Nazwa grupy zasobów jest czwarty element w identyfikatorze, zaraz po *resourceGroups* elementu. W poniższym przykładzie nazwa grupy zasobów jest domyślna-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Alternatywnie aby określić nazwę grupy zasobów istniejącej usługi sieci web, zaloguj się do portalu usług sieci Web Microsoft Azure Machine Learning. Wybierz usługę sieci web. Nazwa grupy zasobów jest piąty element adresu URL usługi sieci web, tuż po *resourceGroups* elementu. W poniższym przykładzie nazwa grupy zasobów jest domyślna-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Eksportowanie definicji usługi sieci Web jako dane JSON
Aby zmodyfikować definicję do trenowanego modelu, aby używać nowo Trenowanego modelu, należy najpierw użyć [AzureRmMlWebService eksportu](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) polecenia cmdlet, aby wyeksportować je do pliku formatu JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Aktualizuj odwołanie do obiektu blob ilearner w formacie JSON.
W zasoby, zlokalizuj [uczony model], zaktualizuj *uri* wartość w *locationInfo* węzła o identyfikatorze URI obiektu ilearner blob. Identyfikator URI jest generowany przez łączenie *BaseLocation* i *RelativeLocation* z danych wyjściowych usługi BES ponownego trenowania wywołania. Spowoduje to zaktualizowanie ścieżkę do odwołania do nowego modelu uczonego.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition"></a>Importuj dane JSON w definicji usługi sieci Web
Należy użyć [AzureRmMlWebService importu](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) polecenia cmdlet, aby przekonwertować zmodyfikowany plik JSON do definicji usługi sieci Web używanej do aktualizacji definicji usługi sieci Web.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Aktualizacja usługi sieci web za pomocą nowych definicji usługi sieci Web
Na koniec użyj [AzureRmMlWebService aktualizacji](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) polecenia cmdlet można zaktualizować definicji usługi sieci Web.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Podsumowanie
Za pomocą poleceń cmdlet programu PowerShell usługi Machine Learning do zarządzania, można zaktualizować uczony model predykcyjne usługi sieci Web, scenariusze, takie jak:

* Model okresowe ponowne szkolenie przy użyciu nowych danych.
* Dystrybucja modelu klientom celem jest umożliwienie im Ponowne szkolenie modelu przy użyciu własnych danych.

