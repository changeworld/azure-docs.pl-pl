---
title: Ponowne szkolenie i wdrażanie usługi sieci web
titleSuffix: Azure Machine Learning Studio
description: Dowiedz się, jak zaktualizować usługę sieci web do używania nowo uczony model, w usłudze Azure Machine Learning Studio uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 903f2700ad127c9bcc69e69ee125ba62fccf52e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196448"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Ponowne szkolenie i wdrożyć model uczenia maszynowego

Ponowne szkolenie jest jednym ze sposobów, aby upewnić się, że modele uczenia maszynowego pozostają dokładne i oparte na dostępnych danych najbardziej odpowiednie. W tym artykule pokazano, jak ponowne szkolenie i wdrożyć usługi machine learning model jako nowej usługi sieci web w programie Studio. Jeśli szukasz doskonalenie klasyczna usługa sieci web, [wyświetlić w tym artykule.](retrain-classic-web-service.md)

W tym artykule przyjęto założenie, że masz już predykcyjna usługa internetowa wdrożona. Jeśli nie masz jeszcze predykcyjna usługa internetowa, [Dowiedz się, jak wdrożyć usługę sieci web Studio w tym miejscu.](publish-a-machine-learning-web-service.md)

Wykonamy następujące kroki, aby ponowne szkolenie i wdrażanie usługi machine learning nowej usługi sieci web:

1. Wdrażanie **ponownego trenowania usługi sieci web**
1. Szkolenie, nowy model przy użyciu usługi **ponownego trenowania usługi sieci web**
1. Aktualizuj istniejącą **eksperyment predykcyjny** do użycia w nowym modelu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Wdrażanie ponownego trenowania usługi sieci web

Usługi sieci web ponownego trenowania umożliwia ponowne szkolenie modelu za pomocą nowego zestawu parametrów, takich jak nowe dane i zapisz go na później. Po nawiązaniu połączenia **danych wyjściowych usługi sieci Web** do **Train Model**, eksperymentu szkolenia wyświetla nowy model do użycia.

Aby wdrożyć ponownego trenowania usługę sieci web, wykonaj następujące kroki:

1. Połącz **danych wejściowych usługi internetowej** moduł do usługi danych wejściowych. Zazwyczaj należy upewnić się, że dane wejściowe są przetwarzane w taki sam sposób jak oryginalne dane szkoleniowe.
1. Połącz **danych wyjściowych usługi sieci Web** modułu z danymi wyjściowymi swoje **Train Model**.
1. Jeśli masz **Evaluate Model** modułu, można połączyć z **danych wyjściowych usługi sieci Web** modułu w danych wyjściowych wyników oceny
1. Uruchom eksperyment.

    Po uruchomieniu eksperymentu, wynikowy przepływu pracy powinna być podobna do poniższej ilustracji:

    ![Wynikowy przepływu pracy](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Teraz możesz wdrożyć eksperymentu szkolenia jako ponownego trenowania usługę sieci web, która generuje uczonego modelu i wyniki oceny modelu.

1. W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web**
1. Wybierz **wdrażanie usługi sieci Web [New]**. W portalu usług sieci Web Azure Machine Learning zostanie otwarty na **wdrażanie usługi sieci Web** strony.
1. Wpisz nazwę usługi sieci web, a następnie wybierz plan płatności.
1. Wybierz pozycję **Wdróż**.

## <a name="retrain-the-model"></a>Ponowne szkolenie modelu

W tym przykładzie używamy C# do tworzenia aplikacji ponownego trenowania. Umożliwia także przykładowy kod języka Python lub R, aby wykonać to zadanie.

Do wywoływania interfejsów API do ponownego trenowania, wykonaj następujące kroki:

1. Tworzenie C# konsoli aplikacji w programie Visual Studio: **Nowe** > **projektu** > **Visual C#**   >  **Windows Classic Desktop**  >   **Aplikacja konsoli (.NET Framework)**.
1. Zaloguj się do portalu usług sieci Web Machine Learning.
1. Kliknij usługę sieci web, którą pracujesz z.
1. Kliknij przycisk **używanie**.
1. W dolnej części **zużywania** strony w **przykładowy kod** kliknij **partii**.
1. Skopiuj przykładowy kod C# dla wykonywania wsadowego i wkleić go do pliku Program.cs. Upewnij się, że przestrzeń nazw pozostaną niezmienione.

Dodaj pakiet NuGet Microsoft.AspNet.WebApi.Client, jak określono w komentarzach. Aby dodać odwołanie do Microsoft.WindowsAzure.Storage.dll, użytkownik może być konieczne zainstalowanie [biblioteki klienta usługi Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage).

Poniższy zrzut ekranu przedstawia **zużywania** strony w portalu usług sieci Web Azure Machine Learning.

![Używanie stron](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Aktualizacja deklaracji apikey

Znajdź **apikey** deklaracji:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

W **informacje podstawowe użycie** części **zużywania** stronie Znajdź klucz podstawowy i skopiuj go do **apikey** deklaracji.

### <a name="update-the-azure-storage-information"></a>Zaktualizuj informacje o usłudze Azure Storage

Przykładowy kod BES służy do przekazywania pliku z dysku lokalnego (na przykład "C:\temp\CensusInput.csv") do usługi Azure Storage, przetwarza je i zapisuje wyniki z powrotem do usługi Azure Storage.

1. Logowanie do witryny Azure Portal
1. W kolumnie nawigacji po lewej stronie kliknij **więcej usług**, wyszukaj **kont magazynu**, a następnie wybierz ją.
1. Z listy kont magazynu wybierz jeden do przechowywania retrained modelu.
1. W kolumnie nawigacji po lewej stronie kliknij **klucze dostępu**.
1. Skopiuj i Zapisz **podstawowy klucz dostępu**.
1. W kolumnie nawigacji po lewej stronie kliknij **kontenery**.
1. Wybierz istniejący kontener, lub Utwórz nową i Zapisz nazwę.

Znajdź *StorageAccountName*, *StorageAccountKey*, i *StorageContainerName* deklaracji i zaktualizuj wartości, które zostały zapisane w portalu.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Możesz także sprawdzić plik wejściowy jest dostępny w lokalizacji, który określisz w kodzie.

### <a name="specify-the-output-location"></a>Określanie lokalizacji wyjściowej

Po określeniu lokalizacji danych wyjściowych w ładunku żądania rozszerzenie pliku, który jest określony w *RelativeLocation* muszą być określone jako `ilearner`.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Oto przykład danych wyjściowych ponownego trenowania:

![Ponowne szkolenie danych wyjściowych](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Ocena ponownego trenowania wyników

Po uruchomieniu aplikacji, dane wyjściowe obejmują adres URL i token sygnatury dostępu współdzielonego, które są niezbędne uzyskiwać dostęp do wyników oceny.

Wyniki wydajności retrained modelu można wyświetlić, łącząc *BaseLocation*, *RelativeLocation*, i *SasBlobToken* z wyników danych wyjściowych dla *output2* i wklejając pełny adres URL w pasku adresu przeglądarki.

Sprawdź wyniki, aby ustalić, jeśli nowo uczony model działa lepiej, niż istniejący.

Zapisz *BaseLocation*, *RelativeLocation*, i *SasBlobToken* z danych wyjściowych wyników.

## <a name="update-the-predictive-experiment"></a>Aktualizacja eksperyment predykcyjny

### <a name="sign-in-to-azure-resource-manager"></a>Zaloguj się do usługi Azure Resource Manager

Najpierw zaloguj się do konta platformy Azure w środowisku programu PowerShell przy użyciu [Connect AzAccount](/powershell/module/az.profile/connect-azaccount) polecenia cmdlet.

### <a name="get-the-web-service-definition-object"></a>Pobierz obiekt definicji usługi sieci Web

Następnie Pobierz obiekt definicji usługi sieci Web, wywołując [Get AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice) polecenia cmdlet.

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Aby określić nazwę grupy zasobów istniejącej usługi sieci web, należy uruchomić polecenie cmdlet Get-AzMlWebService bez żadnych parametrów, aby wyświetlić usługi sieci web w ramach subskrypcji. Znajdź usługę sieci web, a następnie Przyjrzyj się jego identyfikatora usługi internetowej. Nazwa grupy zasobów jest czwarty element w identyfikatorze, zaraz po *resourceGroups* elementu. W poniższym przykładzie nazwa grupy zasobów jest domyślna-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Alternatywnie aby określić nazwę grupy zasobów istniejącej usługi sieci web, zaloguj się do portalu usług sieci Web Azure Machine Learning. Wybierz usługę sieci web. Nazwa grupy zasobów jest piąty element adresu URL usługi sieci web, tuż po *resourceGroups* elementu. W poniższym przykładzie nazwa grupy zasobów jest domyślna-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Eksport obiektu definicji usługi sieci Web w formacie JSON

Aby zmodyfikować definicję nauczonemu modelowi w celu korzystania z nowo trenowanego modelu, należy najpierw użyć [AzMlWebService eksportu](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) polecenia cmdlet, aby wyeksportować je do pliku formatu JSON.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Aktualizuj odwołanie do obiektu blob ilearner

W zasoby, zlokalizuj [uczony model], zaktualizuj *uri* wartość w *locationInfo* węzła o identyfikatorze URI obiektu ilearner blob. Identyfikator URI jest generowany przez łączenie *BaseLocation* i *RelativeLocation* z danych wyjściowych usługi BES ponownego trenowania wywołania.

     "asset3": {
        "name": "Retrain Sample [trained model]",
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

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importuj dane JSON na obiekt definicji usługi sieci Web

Użyj [AzMlWebService importu](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) polecenia cmdlet, aby przekonwertować zmodyfikowany plik JSON do obiektu definicji usługi sieci Web, który umożliwia zaktualizowanie predicative eksperymentu.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>Aktualizacja usługi sieci web

Na koniec użyj [AzMlWebService aktualizacji](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) polecenia cmdlet, aby zaktualizować eksperyment predykcyjny.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zarządzania usługami sieci web lub śledzić wiele przebiegów eksperymentów, zobacz następujące artykuły:

* [Zapoznaj się z portalu usług sieci Web](manage-new-webservice.md)
* [Zarządzanie iteracjami eksperymentów](manage-experiment-iterations.md)
