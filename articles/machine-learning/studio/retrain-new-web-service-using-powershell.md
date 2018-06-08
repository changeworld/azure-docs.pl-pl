---
title: Ponownie ucz nowej usługi Azure Machine Learning usługi sieci web przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak programowo retrain modelu i usługi sieci web do korzystania z nowo uczonego modelu w usłudze Azure Machine Learning przy użyciu poleceń cmdlet programu PowerShell do zarządzania Machine Learning aktualizacji.
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
ms.openlocfilehash: 8ead74be1c1749d2c40d265af7c596e7a180a057
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835365"
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Ponownie ucz usługi sieci web na podstawie nowego Menedżera zasobów za pomocą poleceń cmdlet programu PowerShell do zarządzania Machine Learning
Gdy retrain nową usługę sieci web, należy zaktualizować definicji usługi predykcyjnej sieci web do odwołania do nowego uczonego modelu.

## <a name="prerequisites"></a>Wymagania wstępne
Należy zdefiniować eksperyment uczenia i eksperyment predykcyjny pokazane [Retrain Machine Learning programowo modele](retrain-models-programmatically.md).

> [!IMPORTANT]
> Eksperyment predykcyjny musi być wdrożony jako maszyna usługi Azure Resource Manager (nowy) na podstawie szkoleniowe dotyczące usługi sieci web.
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia do subskrypcji, do którego należy wdrożyć usługę sieci web. Aby uzyskać więcej informacji, zobacz [zarządzania usługi sieci Web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning](manage-new-webservice.md).

Aby uzyskać dodatkowe informacje na temat usługi sieci web wdrażanie, zobacz [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).

Ten proces wymaga zainstalowanego polecenia cmdlet programu Azure Machine Learning. Instalowanie poleceń cmdlet usługi Machine Learning informacji, zobacz [polecenia cmdlet usługi Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt767952.aspx) odwołania w witrynie MSDN.

Z ponownego trenowania danych wyjściowych, należy skopiować następujące informacje:

* BaseLocation
* RelativeLocation

Dostępne są następujące czynności, które należy wykonać:

1. Zaloguj się do swojego konta usługi Azure Resource Manager.
2. Pobierz definicję usługi sieci web
3. Eksportowanie definicji usługi sieci Web w formacie JSON
4. Aktualizacja odwołania do obiektu blob ilearner w formacie JSON.
5. Zaimportuj dane JSON do definicji usługi sieci Web
6. Aktualizacja usługi sieci web z nowego definicji usługi sieci Web

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Zaloguj się do konta usługi Azure Resource Manager
Musisz najpierw zalogować się do konta z platformy Azure w ramach przy użyciu środowiska PowerShell [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) polecenia cmdlet.

## <a name="get-the-web-service-definition"></a>Pobierz definicję usługi sieci Web
Następnie Pobierz usługę sieci Web przez wywołanie metody [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) polecenia cmdlet. Definicja usługi sieci Web jest reprezentacji wewnętrznej trenowanego modelu usługi sieci web, a nie można bezpośrednio modyfikować. Upewnij się, że są pobierania definicji usługi sieci Web dla predykcyjnej eksperymentu, a nie eksperyment uczenia.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Aby określić nazwę grupy zasobów z istniejącej usługi sieci web, uruchom polecenie cmdlet Get-AzureRmMlWebService bez żadnych parametrów do wyświetlenia w ramach subskrypcji usługi sieci web. Znajdź usługę sieci web, a następnie sprawdź jej identyfikatora usługi sieci web Nazwa grupy zasobów jest czwartym elementem w identyfikatorze, zaraz po *resourceGroups* elementu. W poniższym przykładzie nazwa grupy zasobów jest domyślna-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Alternatywnie można określić nazwę grupy zasobów istniejącej usługi sieci web, zaloguj się do portalu usługi sieci Web Microsoft Azure Machine Learning. Wybierz usługę sieci web. Nazwa grupy zasobów jest piąty elementu adres URL usługi sieci web zaraz po *resourceGroups* elementu. W poniższym przykładzie nazwa grupy zasobów jest domyślna-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Eksportowanie definicji usługi sieci Web w formacie JSON
Aby zmodyfikować definicję do uczonego modelu w celu wykorzystania nowo Trenowanego modelu, należy najpierw użyć [AzureRmMlWebService eksportu](https://msdn.microsoft.com/library/azure/mt767935.aspx) polecenia cmdlet można wyeksportować go do pliku formatu JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Aktualizacja odwołania do obiektu blob ilearner w formacie JSON.
Zasoby, odszukaj [nauczony model], zaktualizuj *uri* wartość w *locationInfo* węzła o identyfikatorze URI obiektu ilearner blob. Identyfikator URI jest generowany przez łączenie *BaseLocation* i *RelativeLocation* z danych wyjściowych BES ponownego trenowania wywołania. Spowoduje to zaktualizowanie ścieżkę do odwołania do nowego trenowanego modelu.

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

## <a name="import-the-json-into-a-web-service-definition"></a>Zaimportuj dane JSON do definicji usługi sieci Web
Należy użyć [AzureRmMlWebService importu](https://msdn.microsoft.com/library/azure/mt767925.aspx) polecenia cmdlet, aby przekonwertować zmodyfikowany plik JSON do definicji usługi sieci Web, który służy do aktualizacji definicji usługi sieci Web.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Aktualizacja usługi sieci web z nowego definicji usługi sieci Web
Na koniec użyj [AzureRmMlWebService aktualizacji](https://msdn.microsoft.com/library/azure/mt767922.aspx) polecenia cmdlet można zaktualizować definicji usługi sieci Web.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Podsumowanie
Polecenia cmdlet programu PowerShell Learning maszyny do zarządzania można aktualizować trenowanego modelu predykcyjnego usługi sieci Web, włączanie scenariuszy, takich jak:

* Model okresowego ponownego trenowania nowymi danymi.
* Dystrybucja klientów przy użyciu modelu w celu umożliwienie je ponownie ucz modelu, używając własnych danych.

