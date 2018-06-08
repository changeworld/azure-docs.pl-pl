---
title: Ponownie ucz istniejącej usługi sieci web predykcyjnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ponownie ucz modelu i usługi sieci web, aby użyć nowo uczonego modelu w usłudze Azure Machine Learning aktualizacji.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.openlocfilehash: d399c8c3a47d374549d7ea7815567d7b879b49c8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835304"
---
# <a name="retrain-an-existing-predictive-web-service"></a>Ponownie ucz istniejącej usługi sieci web predykcyjnej
W tym dokumencie opisano proces ponownego trenowania w następującym scenariuszu:

* Masz eksperyment uczenia i eksperyment predykcyjny, która została wdrożona jako usługi sieci web operationalized.
* Masz nowe dane, które mają usługi sieci web predykcyjnych, aby wykonać jego oceniania.

> [!NOTE]
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia do subskrypcji, do którego należy wdrożyć usługę sieci web. Aby uzyskać więcej informacji, zobacz [zarządzania usługi sieci Web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning](manage-new-webservice.md).

Począwszy od istniejącej usługi sieci web, a eksperymentów, musisz wykonaj następujące kroki:

1. Aktualizowanie modelu.
   1. Zmodyfikuj eksperymentu uczenia do obsługi sieci web usługi wejścia i wyjścia.
   2. Wdróż eksperyment uczenia jako usługę sieci web ponownego trenowania.
   3. Użyj usługi wykonywania wsadowego usługi (BES) eksperyment uczenia, aby ponownie ucz modelu.
2. Aby zaktualizować eksperyment predykcyjny, należy użyć polecenia cmdlet programu PowerShell usługi Azure Machine Learning.
   1. Zaloguj się do swojego konta usługi Azure Resource Manager.
   2. Pobierz definicję usługi sieci web.
   3. Eksportowanie definicji usługi sieci web w formacie JSON.
   4. Aktualizacja odwołania do obiektu blob ilearner w formacie JSON.
   5. Importuj dane JSON do definicji usługi sieci web.
   6. Aktualizuje usługę sieci web do nowej definicji usługi sieci web.

## <a name="deploy-the-training-experiment"></a>Wdrażanie eksperyment uczenia
Aby wdrożyć eksperyment uczenia ponownego trenowania Usługa sieci web, należy dodać sieci web usługi wejściach i wyjściach do modelu. Łącząc *wyjście usługi sieci Web* modułu do eksperymentu *[Train Model] [ train-model]* moduł, możesz włączyć eksperyment uczenia utworzyć nowy model przeszkolone używanej w eksperymencie predykcyjnej. Jeśli masz *Evaluate Model* modułu, można także dołączyć dane wyjściowe usługi sieci web, aby uzyskać wyniki oceny jako dane wyjściowe.

Aby zaktualizować eksperymentu uczenia:

1. Połącz *wejściowego usługi sieci Web* modułu do wprowadzonych danych (na przykład *Clean Missing Data* modułu). Zwykle ma upewnij się, że dane wejściowe są przetwarzane w taki sam sposób jak oryginalne dane szkolenia.
2. Połącz *wyjście usługi sieci Web* modułu do danych wyjściowych z *Train Model* modułu.
3. Jeśli masz *Evaluate Model* modułu i chcesz wyświetlić wyników oceny, Połącz *wyjście usługi sieci Web* modułu do danych wyjściowych z *Evaluate Model* modułu.

Uruchamianie eksperymentu.

Następnie należy wdrożyć eksperyment uczenia jako usługę sieci web, który spowoduje utworzenie uczonego modelu i wyniki oceny modelu.

W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web**, a następnie wybierz **wdrażanie usługi sieci Web [New]**. Zostanie otwarty portal usługi sieci Web systemu Azure Machine Learning **wdrażanie usługi sieci Web** strony. Wpisz nazwę usługi sieci web, wybierz plan płatności, a następnie kliknij przycisk **Wdróż**. Metody wykonywania wsadowego usługi można używać tylko do tworzenia modeli przeszkolone.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Ponownie ucz modelu nowymi danymi za pomocą usługi BES
W tym przykładzie używamy C# do tworzenia aplikacji ponownego trenowania. Umożliwia także Python lub R przykładowy kod do wykonania tego zadania.

Aby wywołać ponownego trenowania interfejsów API:

1. Tworzenie aplikacji konsolowej C# w programie Visual Studio: **nowy** > **projektu** > **Visual C#** > **Windows Desktop klasycznego** > **aplikacji konsoli (.NET Framework)**.
2. Zaloguj się do portalu usługi sieci Web usługi Machine Learning.
3. Kliknij opcję usługi sieci web, z którymi pracujesz.
4. Kliknij przycisk **korzystać**.
5. W dolnej części **Consume** strony w **przykładowy kod** kliknij **partii**.
6. Skopiuj przykładowy kod C# dla wykonywania wsadowego i wklej go do pliku Program.cs. Upewnij się, że przestrzeń nazw pozostanie niezmieniona.

Dodaj pakiet NuGet Microsoft.AspNet.WebApi.Client, jak określono w komentarzach. Można dodać odwołania do Microsoft.WindowsAzure.Storage.dll, najpierw należy zainstalować [biblioteki klienta usługi Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage).

Poniższy zrzut ekranu przedstawia **Consume** w portalu usługi sieci Web systemu Azure Machine Learning.

![Korzystanie ze strony][1]

### <a name="update-the-apikey-declaration"></a>Deklaracja apikey aktualizacji
Zlokalizuj **apikey** deklaracji:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

W **zużycie podstawowe informacje o** sekcji **Consume** strony Znajdź klucz podstawowy i skopiuj go do **apikey** deklaracji.

### <a name="update-the-azure-storage-information"></a>Zaktualizuj informacje o usłudze Azure Storage
BES przykładowy kod operacji przekazywania plików z dysku lokalnego (na przykład "C:\temp\CensusIpnput.csv") do usługi Azure Storage, procesy i zapisuje wyniki wstecz do magazynu Azure.

Po zakończeniu eksperymentu, wynikowy przepływu pracy powinny być podobne do następujących:

![Wynikowa przepływu pracy po uruchomieniu][4]

1. Zaloguj się do Portalu Azure.
2. W kolumnie nawigacji po lewej stronie kliknij **więcej usług**, wyszukaj **kont magazynu**i zaznacz je.
3. Z listy kont magazynu wybierz jeden do przechowywania retrained modelu.
4. W kolumnie nawigacji po lewej stronie kliknij **klucze dostępu**.
5. Skopiuj i Zapisz **podstawowy klucz dostępu**.
6. W kolumnie nawigacji po lewej stronie kliknij **kontenery**.
7. Wybierz kontener istniejącą lub Utwórz nową i Zapisz nazwę.

Zlokalizuj *StorageAccountName*, *StorageAccountKey*, i *StorageContainerName* deklaracje i zaktualizuj wartości zapisane w portalu.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Również Pamiętaj, że plik wejściowy jest dostępny w lokalizacji określonej w kodzie.

### <a name="specify-the-output-location"></a>Określ lokalizację danych wyjściowych
Po określeniu lokalizacji danych wyjściowych w ładunku żądania rozszerzenie pliku, który jest określony w *RelativeLocation* muszą być określone jako `ilearner`. Zobacz poniższy przykład:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Poniżej przedstawiono przykład ponownego trenowania danych wyjściowych:

![Ponownego trenowania danych wyjściowych][6]

## <a name="evaluate-the-retraining-results"></a>Ocena ponownego trenowania wyników
Po uruchomieniu aplikacji, danych wyjściowych zawiera adres URL i tokenu sygnatury dostępu współdzielonego, który są niezbędne do uzyskania dostępu wyniki oceny.

Można wyświetlić wyniki wydajności retrained modelu, łącząc *BaseLocation*, *RelativeLocation*, i *SasBlobToken* z wyników danych wyjściowych dla *output2* (jak pokazano w poprzednim ponownego trenowania obrazu wyjściowego) i wklejanie pełny adres URL na pasku adresu przeglądarki.

Przeanalizuj wyniki do ustalenia, czy nowo trenowanego modelu pełni wystarczająco również zastąpić istniejący.

Kopiuj *BaseLocation*, *RelativeLocation*, i *SasBlobToken* z wyników danych wyjściowych.

## <a name="retrain-the-web-service"></a>Ponownie ucz usługi sieci web
Gdy retrain nową usługę sieci web, należy zaktualizować definicji usługi predykcyjnej sieci web do odwołania do nowego uczonego modelu. Definicja usługi sieci web jest reprezentacji wewnętrznej trenowanego modelu usługi sieci web, a nie można bezpośrednio modyfikować. Upewnij się, że są pobierania definicji usługi sieci web dla predykcyjnej eksperymentu, a nie eksperyment uczenia.

## <a name="sign-in-to-azure-resource-manager"></a>Zaloguj się do usługi Azure Resource Manager
Musisz najpierw zalogować się do konta z platformy Azure w środowisku PowerShell przy użyciu [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) polecenia cmdlet.

## <a name="get-the-web-service-definition-object"></a>Pobierz obiekt definicji usługi sieci Web
Następnie Pobierz obiekt definicji usługi sieci Web przez wywołanie metody [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) polecenia cmdlet.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Aby określić nazwę grupy zasobów z istniejącej usługi sieci web, uruchom polecenie cmdlet Get-AzureRmMlWebService bez żadnych parametrów do wyświetlenia w ramach subskrypcji usługi sieci web. Znajdź usługę sieci web, a następnie sprawdź jej identyfikatora usługi sieci web Nazwa grupy zasobów jest czwartym elementem w identyfikatorze, zaraz po *resourceGroups* elementu. W poniższym przykładzie nazwa grupy zasobów jest domyślna-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Alternatywnie można określić nazwę grupy zasobów istniejącej usługi sieci web, zaloguj się do portalu usługi sieci Web systemu Azure Machine Learning. Wybierz usługę sieci web. Nazwa grupy zasobów jest piąty elementu adres URL usługi sieci web zaraz po *resourceGroups* elementu. W poniższym przykładzie nazwa grupy zasobów jest domyślna-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Eksport obiektu definicji usługi sieci Web w formacie JSON
Aby zmodyfikować definicję uczonego modelu do korzystania z nowo trenowanego modelu, należy najpierw użyć [AzureRmMlWebService eksportu](https://msdn.microsoft.com/library/azure/mt767935.aspx) polecenia cmdlet, aby wyeksportować do pliku w formacie JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Aktualizacja odwołania do obiektu blob ilearner
Zasoby, odszukaj [nauczony model], zaktualizuj *uri* wartość w *locationInfo* węzła o identyfikatorze URI obiektu ilearner blob. Identyfikator URI jest generowany przez łączenie *BaseLocation* i *RelativeLocation* z danych wyjściowych BES ponownego trenowania wywołania.

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

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importuj dane JSON na obiekt definicji usługi sieci Web
Należy użyć [AzureRmMlWebService importu](https://msdn.microsoft.com/library/azure/mt767925.aspx) polecenia cmdlet, aby przekonwertować zmodyfikowany plik JSON do obiekt definicji usługi sieci Web za pomocą którego można użyć do zaktualizowania predicative eksperymentu.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Aktualizacja usługi sieci web
Na koniec użyj [AzureRmMlWebService aktualizacji](https://msdn.microsoft.com/library/azure/mt767922.aspx) polecenia cmdlet, aby zaktualizować eksperyment predykcyjny.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
