---
title: Ponowne szkolenie istniejącej usługi internetowej predykcyjna | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ponowne szkolenie modelu i zaktualizować usługę sieci web, aby używać nowo uczonego modelu w usłudze Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 9fb3ccee9a50fa7758547fffe2573104679aef7e
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263455"
---
# <a name="retrain-an-existing-predictive-web-service"></a>Ponowne szkolenie istniejącej usługi internetowej predykcyjne
W tym dokumencie opisano proces ponownego trenowania w następującym scenariuszu:

* Masz eksperymentu szkolenia i eksperyment predykcyjny, który ma być wdrożony jako zoperacjonalizowanej usługi sieci web.
* Masz nowe dane mają usługę sieci web predykcyjne używać do wykonywania swoich oceniania.

> [!NOTE]
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia w ramach subskrypcji, do której możesz wdrażanie usługi sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md).

Począwszy od swojej istniejącej usługi sieci web i eksperymentów, musisz wykonaj następujące kroki:

1. Aktualizowanie modelu.
   1. Zmodyfikuj eksperymentu szkolenia umożliwiające usługi sieci web w danych wejściowych i wyjściowych.
   2. Wdrażanie eksperymentu szkolenia jako usługi sieci web ponownego trenowania.
   3. Umożliwia ponowne szkolenie modelu eksperymentu szkolenia usługi Batch Execution Service (BES).
2. Aby zaktualizować eksperyment predykcyjny, należy użyć polecenia cmdlet programu PowerShell usługi Azure Machine Learning.
   1. Zaloguj się do swojego konta usługi Azure Resource Manager.
   2. Pobierz definicję usługi sieci web.
   3. Eksportowanie definicji usługi sieci web jako dane JSON.
   4. Aktualizuj odwołanie do obiektu blob ilearner w formacie JSON.
   5. Importuj dane JSON w definicji usługi sieci web.
   6. Zaktualizuj usługę sieci web przy użyciu nowych definicji usługi sieci web.

## <a name="deploy-the-training-experiment"></a>Wdrażanie eksperymentu szkolenia
Aby wdrożyć eksperymentu szkolenia jako ponownego trenowania usługi sieci web, należy dodać do modelu usługi sieci web w danych wejściowych i wyjściowych. Łącząc *danych wyjściowych usługi sieci Web* modułów na eksperyment *[Train Model][train-model]* moduł, możesz włączyć eksperymentu szkolenia, aby generuje nowy uczonego modelu używanego w eksperymencie predykcyjne. Jeśli masz *Evaluate Model* modułu, można również dołączyć dane wyjściowe usługi sieci web, aby wyświetlić wyniki oceny jako dane wyjściowe.

Aby zaktualizować eksperymentu szkolenia:

1. Połącz *danych wejściowych usługi internetowej* moduł do usługi danych wejściowych (na przykład *Clean Missing Data* modułu). Zazwyczaj należy upewnić się, że dane wejściowe są przetwarzane w taki sam sposób jak oryginalne dane szkoleniowe.
2. Połącz *danych wyjściowych usługi sieci Web* modułu z danymi wyjściowymi swoje *Train Model* modułu.
3. Jeśli masz *Evaluate Model* modułu chcesz wysłać dane wyjściowe wyniki oceny, Połącz *danych wyjściowych usługi sieci Web* modułu z danymi wyjściowymi swoje *Evaluate Model* modułu.

Uruchom eksperyment.

Następnie należy wdrożyć eksperymentu szkolenia jako usługę sieci web, który produkuje uczonego modelu i wyniki oceny modelu.

W dolnej części obszaru roboczego eksperymentu, kliknij przycisk **ustawić usługę sieci Web**, a następnie wybierz pozycję **wdrażanie usługi sieci Web [New]**. W portalu usług sieci Web Azure Machine Learning zostanie otwarty na **wdrażanie usługi sieci Web** strony. Wpisz nazwę usługi sieci web, wybierz plan płatności, a następnie kliknij **Wdróż**. Metoda wykonywanie wsadowe służy tylko do tworzenia przeszkolone modele.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Ponowne szkolenie modelu za pomocą nowych danych przy użyciu usługi BES
W tym przykładzie używamy C# do tworzenia aplikacji ponownego trenowania. Umożliwia także przykładowy kod języka Python lub R, aby wykonać to zadanie.

Do wywoływania interfejsów API do ponownego trenowania:

1. Tworzenie aplikacji konsolowej C# w programie Visual Studio: **New** > **projektu** > **Visual C#** > **Windows Klasyczny pulpit** > **Aplikacja konsoli (.NET Framework)**.
2. Zaloguj się do portalu usług sieci Web Machine Learning.
3. Kliknij usługę sieci web, którą pracujesz z.
4. Kliknij przycisk **używanie**.
5. W dolnej części **zużywania** strony w **przykładowy kod** kliknij **partii**.
6. Skopiuj przykładowy kod C# dla wykonywania wsadowego i wkleić go do pliku Program.cs. Upewnij się, że przestrzeń nazw pozostaną niezmienione.

Dodaj pakiet NuGet Microsoft.AspNet.WebApi.Client, jak określono w komentarzach. Aby dodać odwołanie do Microsoft.WindowsAzure.Storage.dll, może najpierw musisz zainstalować [biblioteki klienta usługi Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage).

Poniższy zrzut ekranu przedstawia **zużywania** strony w portalu usług sieci Web Azure Machine Learning.

![Używanie stron][1]

### <a name="update-the-apikey-declaration"></a>Aktualizacja deklaracji apikey
Znajdź **apikey** deklaracji:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

W **informacje podstawowe użycie** części **zużywania** stronie Znajdź klucz podstawowy i skopiuj go do **apikey** deklaracji.

### <a name="update-the-azure-storage-information"></a>Zaktualizuj informacje o usłudze Azure Storage
Przykładowy kod BES służy do przekazywania pliku z dysku lokalnego (na przykład "C:\temp\CensusIpnput.csv") do usługi Azure Storage, przetwarza je i zapisuje wyniki z powrotem do usługi Azure Storage.

Po uruchomieniu eksperymentu, wynikowy przepływu pracy powinna być podobna do następującej:

![Wynikowy przepływu pracy po uruchomieniu][4]

1. Zaloguj się do Portalu Azure.
2. W kolumnie nawigacji po lewej stronie kliknij **więcej usług**, wyszukaj **kont magazynu**, a następnie wybierz ją.
3. Z listy kont magazynu wybierz jeden do przechowywania retrained modelu.
4. W kolumnie nawigacji po lewej stronie kliknij **klucze dostępu**.
5. Skopiuj i Zapisz **podstawowy klucz dostępu**.
6. W kolumnie nawigacji po lewej stronie kliknij **kontenery**.
7. Wybierz istniejący kontener, lub Utwórz nową i Zapisz nazwę.

Znajdź *StorageAccountName*, *StorageAccountKey*, i *StorageContainerName* deklaracji i zaktualizuj wartości, które zostały zapisane w portalu.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Możesz także sprawdzić plik wejściowy jest dostępny w lokalizacji, który określisz w kodzie.

### <a name="specify-the-output-location"></a>Określanie lokalizacji wyjściowej
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

Oto przykład danych wyjściowych ponownego trenowania:

![Ponowne szkolenie danych wyjściowych][6]

## <a name="evaluate-the-retraining-results"></a>Ocena ponownego trenowania wyników
Po uruchomieniu aplikacji, dane wyjściowe obejmują adres URL i token sygnatury dostępu współdzielonego, które są niezbędne uzyskiwać dostęp do wyników oceny.

Wyniki wydajności retrained modelu można wyświetlić, łącząc *BaseLocation*, *RelativeLocation*, i *SasBlobToken* z wyników danych wyjściowych dla *output2* (pokazany na wcześniejszej ilustracji ponownego trenowania danych wyjściowych) i wklejając pełny adres URL w pasku adresu przeglądarki.

Sprawdź wyniki, aby ustalić, czy nowo uczonego modelu wykonuje wystarczająco dobrze, aby zamienić istniejący.

Kopiuj *BaseLocation*, *RelativeLocation*, i *SasBlobToken* z danych wyjściowych wyników.

## <a name="retrain-the-web-service"></a>Ponowne szkolenie usługi sieci web
Gdy retrain się nowej usługi sieci web, należy zaktualizować definicji usługi predykcyjne sieci web, aby odwoływać się do nowego modelu uczonego. Definicja usługi sieci web jest wewnętrznej reprezentacji uczonego modelu usługi sieci web i nie można bezpośrednio modyfikować. Upewnij się, Twoje eksperyment predykcyjny i nie eksperymentu szkolenia pobierają definicji usługi sieci web.

## <a name="sign-in-to-azure-resource-manager"></a>Zaloguj się do usługi Azure Resource Manager
Należy najpierw zalogować do konta platformy Azure w środowisku programu PowerShell przy użyciu [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) polecenia cmdlet.

## <a name="get-the-web-service-definition-object"></a>Pobierz obiekt definicji usługi sieci Web
Następnie Pobierz obiekt definicji usługi sieci Web, wywołując [Get AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservice) polecenia cmdlet.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Aby określić nazwę grupy zasobów istniejącej usługi sieci web, należy uruchomić polecenie cmdlet Get-AzureRmMlWebService bez żadnych parametrów, aby wyświetlić usługi sieci web w ramach subskrypcji. Znajdź usługę sieci web, a następnie Przyjrzyj się jego identyfikatora usługi internetowej. Nazwa grupy zasobów jest czwarty element w identyfikatorze, zaraz po *resourceGroups* elementu. W poniższym przykładzie nazwa grupy zasobów jest domyślna-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Alternatywnie aby określić nazwę grupy zasobów istniejącej usługi sieci web, zaloguj się do portalu usług sieci Web Azure Machine Learning. Wybierz usługę sieci web. Nazwa grupy zasobów jest piąty element adresu URL usługi sieci web, tuż po *resourceGroups* elementu. W poniższym przykładzie nazwa grupy zasobów jest domyślna-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Eksport obiektu definicji usługi sieci Web w formacie JSON
Aby zmodyfikować definicję nauczonemu modelowi w celu korzystania z nowo trenowanego modelu, należy najpierw użyć [AzureRmMlWebService eksportu](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) polecenia cmdlet, aby wyeksportować je do pliku formatu JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Aktualizuj odwołanie do obiektu blob ilearner
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

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importuj dane JSON na obiekt definicji usługi sieci Web
Należy użyć [AzureRmMlWebService importu](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) polecenia cmdlet, aby przekonwertować zmodyfikowany plik JSON do obiektu definicji usługi sieci Web, który umożliwia zaktualizowanie predicative eksperymentu.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Aktualizacja usługi sieci web
Na koniec użyj [AzureRmMlWebService aktualizacji](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) polecenia cmdlet, aby zaktualizować eksperyment predykcyjny.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
