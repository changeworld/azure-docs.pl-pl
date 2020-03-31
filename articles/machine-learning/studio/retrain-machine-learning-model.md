---
title: Ponowne szkolenie usługi sieci web
titleSuffix: ML Studio (classic) - Azure
description: Dowiedz się, jak zaktualizować usługę sieci web, aby użyć nowo przeszkolonego modelu uczenia maszynowego w usłudze Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 218c1c98a2ed775ae86c1657156991879708cc7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217936"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Ponowne szkolenie i wdrażanie modelu uczenia maszynowego

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Przekwalifikowanie jest jednym ze sposobów zapewnienia, że modele uczenia maszynowego pozostają dokładne i oparte na najbardziej odpowiednich dostępnych danych. W tym artykule pokazano, jak przeszkolić i wdrożyć model uczenia maszynowego jako nową usługę sieci web w Studio (klasyczny). Jeśli chcesz przeszkolić klasyczną usługę sieci web, [zobacz ten artykuł in jak to zrobić.](retrain-classic-web-service.md)

W tym artykule przyjęto założenie, że wdrożono już predykcyjną usługę sieci web. Jeśli nie masz jeszcze predykcyjnej usługi sieci web, [dowiedz się, jak wdrożyć usługę sieci web Studio (klasyczną) tutaj.](deploy-a-machine-learning-web-service.md)

Wykonaj następujące kroki, aby przeszkolić i wdrożyć nową usługę sieci web uczenia maszynowego:

1. Wdrażanie **usługi sieci web przekwalifikowania**
1. Trenuj nowy model za pomocą **usługi przekwalifikowania sieci Web**
1. Zaktualizuj istniejący **eksperyment predykcyjny,** aby użyć nowego modelu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Wdrażanie usługi przekwalifikowania sieci web

Przekwalifikowanie usługi sieci web umożliwia ponowne przeszkolenie modelu przy nowym zestawie parametrów, takich jak nowe dane, i zapisanie go na później. Po podłączeniu **danych wyjściowych usługi sieci Web** do modelu **pociągu,** eksperyment szkoleniowy wyprowadza nowy model do użycia.

Aby wdrożyć usługę sieci web ponownego szkolenia, należy wykonać następujące czynności:

1. Podłącz moduł **wprowadzania danych usługi sieci Web** do danych. Zazwyczaj chcesz upewnić się, że dane wejściowe są przetwarzane w taki sam sposób, jak oryginalne dane szkoleniowe.
1. Podłącz moduł **wyjścia usługi sieci Web** do danych wyjściowych modelu **pociągu**.
1. Jeśli masz moduł **Oceniaj model,** możesz podłączyć moduł **wyjścia usługi sieci Web,** aby wysuwać wyniki oceny
1. Uruchom eksperyment.

    Po uruchomieniu eksperymentu wynikowy przepływ pracy powinien być podobny do następującego obrazu:

    ![Wynikowy przepływ pracy](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Teraz można wdrożyć eksperyment szkolenia jako przekwalifikowanie usługi sieci web, która wyprowadza wyniki oceny uczony model i model.

1. U dołu obszaru roboczego eksperymentu kliknij pozycję **Konfiguruj usługę sieci Web**
1. Wybierz **pozycję Wdrażanie usługi sieci Web [Nowy]**. Portal usług Azure Machine Learning Web Services zostanie otwarty na stronie **Wdrażanie usługi sieci Web.**
1. Wpisz nazwę usługi sieci web i wybierz plan płatności.
1. Wybierz pozycję **Wdróż**.

## <a name="retrain-the-model"></a>Ponowne szkolenie modelu

W tym przykładzie używamy języka C# do utworzenia aplikacji ponownego trenowania. Można również użyć przykładowego kodu języka Python lub R, aby wykonać to zadanie.

Aby wywołać interfejsy API ponownego szkolenia, należy wykonać następujące czynności:

1. Tworzenie aplikacji konsoli języka C# w programie Visual Studio: **Nowa** > aplikacja klasycznej konsoli**klasycznej**systemu Windows > **(.NET Framework)****w programie Project** > **Visual C#** > Windows Classic .
1. Zaloguj się do portalu usług sieci Web uczenia maszynowego.
1. Kliknij usługę sieci web, z którą pracujesz.
1. Kliknij **pozycję Pochówotwórz**.
1. U dołu strony **Zużycie** w sekcji **Przykładowy kod** kliknij pozycję **Partia**.
1. Skopiuj przykładowy kod języka C# do wykonania partii i wklej go do pliku Program.cs. Upewnij się, że obszar nazw pozostaje nienaruszony.

Dodaj pakiet NuGet Microsoft.AspNet.WebApi.Client, jak określono w komentarzach. Aby dodać odwołanie do pliku Microsoft.WindowsAzure.Storage.dll, może być konieczne [zainstalowanie biblioteki klienta dla usług Usługi Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage).

Poniższy zrzut ekranu przedstawia **zużycie** strony w portalu usług Azure Machine Learning Web Services.

![Strona zużywają](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Aktualizowanie deklaracji apikey

Znajdź deklarację **apikey:**

    const string apiKey = "abc123"; // Replace this with the API key for the web service

W sekcji **Informacje o zużyciu podstawowym** na stronie **Zużycie** znajdź klucz podstawowy i skopiuj go do deklaracji **apikey.**

### <a name="update-the-azure-storage-information"></a>Aktualizowanie informacji usługi Azure Storage

Przykładowy kod BES przekazuje plik z dysku lokalnego (na przykład "C:\temp\CensusInput.csv") do usługi Azure Storage, przetwarza go i zapisuje wyniki z powrotem do usługi Azure Storage.

1. Logowanie do witryny Azure Portal
1. W lewej kolumnie nawigacji kliknij pozycję **Więcej usług**, wyszukaj **konta magazynu**i wybierz je.
1. Z listy kont magazynu wybierz jeden z nich, aby przechowywać ponownie przeszkolony model.
1. W lewej kolumnie nawigacji kliknij pozycję **Klawisze programu Access**.
1. Skopiuj i zapisz **podstawowy klucz dostępu**.
1. W lewej kolumnie nawigacji kliknij pozycję **Obiekty Blob**.
1. Wybierz istniejący kontener lub utwórz nowy i zapisz nazwę.

Znajdź oświadczenia *StorageAccountName*, *StorageAccountKey*i *StorageContainerName* i zaktualizuj wartości zapisane w portalu.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Należy również upewnić się, że plik wejściowy jest dostępny w lokalizacji określonej w kodzie.

### <a name="specify-the-output-location"></a>Określ lokalizację wyjściową

Po określeniu lokalizacji wyjściowej w ładunku żądania rozszerzenie pliku, który jest określony `ilearner`w *RelativeLocation* musi być określony jako .

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Oto przykład przekwalifikowania danych wyjściowych:

![Przekwalifikowanie danych wyjściowych](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Oceń wyniki przekwalifikowania

Po uruchomieniu aplikacji dane wyjściowe zawiera adres URL i token podpisów dostępu współdzielonego, które są niezbędne do uzyskania dostępu do wyników oceny.

Wyniki wydajności przekwalifikowanego modelu można wyświetlić, łącząc *baselocation*, *RelativeLocation*i *SasBlobToken* z wyników wyjściowych dla *output2* i wklejając pełny adres URL na pasku adresu przeglądarki.

Sprawdź wyniki, aby ustalić, czy nowo przeszkolony model działa lepiej niż istniejący.

Zapisz *BaseLocation*, *RelativeLocation*i *SasBlobToken* z wyników wyjściowych.

## <a name="update-the-predictive-experiment"></a>Aktualizowanie eksperymentu predykcyjnego

### <a name="sign-in-to-azure-resource-manager"></a>Logowanie się do usługi Azure Resource Manager

Najpierw zaloguj się do konta platformy Azure z poziomu środowiska programu PowerShell przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

### <a name="get-the-web-service-definition-object"></a>Pobierz obiekt definicji usługi sieci Web

Następnie pobierz obiekt definicji usługi sieci Web, wywołując polecenie cmdlet [Get-AzMlWebService.](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice)

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Aby określić nazwę grupy zasobów istniejącej usługi sieci web, uruchom polecenie cmdlet Get-AzMlWebService bez żadnych parametrów, aby wyświetlić usługi sieci web w ramach subskrypcji. Znajdź usługę sieci web, a następnie spójrz na jej identyfikator usługi sieci web. Nazwa grupy zasobów jest czwartym elementem w identyfikatorze, tuż po *resourceGroups* element. W poniższym przykładzie nazwa grupy zasobów jest Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Alternatywnie, aby określić nazwę grupy zasobów istniejącej usługi sieci web, zaloguj się do portalu usług Azure Machine Learning Web Services. Wybierz usługę sieci web. Nazwa grupy zasobów jest piątym elementem adresu URL usługi sieci web, tuż po *resourceGroups* element. W poniższym przykładzie nazwa grupy zasobów jest Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Eksportowanie obiektu definicji usługi sieci Web jako JSON

Aby zmodyfikować definicję modelu uczonego w celu użycia nowo uczonego modelu, należy najpierw użyć polecenia cmdlet [Export-AzMlWebService,](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) aby wyeksportować go do pliku w formacie JSON.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Zaktualizuj odwołanie do obiektu blob ilearner

W zasobach zlokalizuj [przeszkolony model], zaktualizuj wartość *uri* w węźle *locationInfo* za pomocą identyfikatora URI obiektu blob ilearner. Identyfikator URI jest generowany przez połączenie *BaseLocation* i *RelativeLocation* z danych wyjściowych wywołania ponownego trenowania BES.

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

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importowanie usługi JSON do obiektu definicji usługi sieci Web

Polecenie cmdlet [Import-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) służy do konwersji zmodyfikowanego pliku JSON z powrotem do obiektu definicji usługi sieci Web, za pomocą którego można zaktualizować eksperyment predykcyjny.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>Aktualizowanie usługi sieci Web

Na koniec użyj polecenia cmdlet [Update-AzMlWebService,](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) aby zaktualizować eksperyment predykcyjny.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zarządzaniu usługami sieci Web lub śledzeniu wielu przebiegów eksperymentów, zobacz następujące artykuły:

* [Poznaw portalu usług sieci Web](manage-new-webservice.md)
* [Zarządzanie iteracjami eksperymentów](manage-experiment-iterations.md)
