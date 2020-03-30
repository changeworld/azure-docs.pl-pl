---
title: Tworzenie wielu punktów końcowych dla modelu
titleSuffix: ML Studio (classic) - Azure
description: Program PowerShell służy do tworzenia wielu modeli uczenia maszynowego i punktów końcowych usługi sieci web z tym samym algorytmem, ale z różnymi zestawami danych szkoleniowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 4f8bb1f20dcc3a7900e3347616018a6e156962d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218187"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Tworzenie modeli studio (klasycznych) i punktów końcowych usługi sieci Web za pomocą programu PowerShell z jednego eksperymentu

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Oto typowy problem uczenia maszynowego: Chcesz utworzyć wiele modeli, które mają ten sam przepływ pracy szkolenia i używać tego samego algorytmu. Ale chcesz, aby miały różne zestawy danych szkoleniowych jako dane wejściowe. W tym artykule pokazano, jak to zrobić na dużą skalę w usłudze Azure Machine Learning Studio (klasyczna) przy użyciu tylko jednego eksperymentu.

Załóżmy na przykład, że posiadasz globalną wypożyczalnię rowerów franczyzową. Chcesz utworzyć model regresji, aby przewidzieć zapotrzebowanie na wynajem na podstawie danych historycznych. Masz 1000 lokalizacji wynajmu na całym świecie i zebrałeś zestaw danych dla każdej lokalizacji. Obejmują one ważne funkcje, takie jak data, godzina, pogoda i ruch, które są specyficzne dla każdej lokalizacji.

Można trenować modelu raz przy użyciu scalonej wersji wszystkich zestawów danych we wszystkich lokalizacjach. Ale każda z twoich lokalizacji ma unikalne środowisko. Dlatego lepszym rozwiązaniem byłoby trenowanie modelu regresji oddzielnie przy użyciu zestawu danych dla każdej lokalizacji. W ten sposób każdy przeszkolony model może uwzględniać różne rozmiary sklepów, objętość, lokalizację geograficzną, populację, środowisko ruchu przyjazne dla rowerów i inne.

To może być najlepsze podejście, ale nie chcesz tworzyć 1000 eksperymentów szkoleniowych w usłudze Azure Machine Learning Studio (klasyczny) z każdym z nich reprezentujących unikatową lokalizację. Oprócz tego, że jest przytłaczające zadanie, również wydaje się nieefektywne, ponieważ każdy eksperyment będzie miał wszystkie te same składniki, z wyjątkiem zestawu danych szkolenia.

Na szczęście można to osiągnąć za pomocą [interfejsu API przekwalifikowania usługi Azure Machine Learning Studio (klasycznego)](/azure/machine-learning/studio/retrain-machine-learning-model) i automatyzacji zadania za pomocą [programu Azure Machine Learning Studio (klasyczny) program PowerShell.](powershell-module.md)

> [!NOTE]
> Aby przyspieszyć uruchamianie próbki, zmniejsz liczbę lokalizacji z 1000 do 10. Ale te same zasady i procedury mają zastosowanie do 1000 lokalizacji. Jeśli jednak chcesz trenować z 1000 zestawów danych, można uruchomić następujące skrypty programu PowerShell równolegle. Jak to zrobić jest poza zakresem tego artykułu, ale można znaleźć przykłady programu PowerShell wielowątkową w Internecie.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Konfigurowanie eksperymentu szkoleniowego
Użyj przykładowego [eksperymentu szkoleniowego,](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) który jest w [Galerii Analizy Cortany](https://gallery.azure.ai). Otwórz ten eksperyment w swoim obszarze roboczym [usługi Azure Machine Learning Studio (klasycznym).](https://studio.azureml.net)

> [!NOTE]
> Aby wykonać ten przykład, można użyć standardowego obszaru roboczego, a nie wolnego obszaru roboczego. Utworzyć jeden punkt końcowy dla każdego klienta — w sumie 10 punktów końcowych — i wymaga standardowego obszaru roboczego, ponieważ wolny obszar roboczy jest ograniczony do 3 punktów końcowych.
> 
> 

Eksperyment używa **modułu Importuj dane** do importowania zestawu danych szkoleniowych *customer001.csv* z konta magazynu platformy Azure. Załóżmy, że zebrałeś zestawy danych szkoleniowych ze wszystkich lokalizacji wypożyczalni rowerów i przechowywałeś je w tej samej lokalizacji przechowywania obiektów blob z nazwami plików od *rentalloc001.csv* do *rentalloc10.csv*.

![Moduł programu Reader importuje dane z obiektu blob platformy Azure](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Należy zauważyć, że moduł **wyjścia usługi sieci Web** został dodany do modułu modelu **pociągu.**
Gdy ten eksperyment jest wdrażany jako usługa sieci web, punkt końcowy skojarzony z tym wyjściem zwraca uczony model w formacie pliku .ilearner.

Należy również pamiętać, że skonfigurowano parametr usługi sieci web, który definiuje adres URL używany przez moduł **Importuj dane.** Dzięki temu można użyć parametru, aby określić indywidualne zestawy danych szkolenia do szkolenia modelu dla każdej lokalizacji.
Istnieją inne sposoby, które można było to zrobić. Za pomocą kwerendy SQL z parametrem usługi sieci web można uzyskać dane z bazy danych sql azure. Można też użyć modułu **wprowadzania usługi sieci Web,** aby przekazać zestaw danych do usługi sieci web.

![Moduł modelu przeszkolonego wyprowadza się do modułu wyjściowego usługi sieci Web](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Teraz uruchommy ten eksperyment szkoleniowy przy użyciu domyślnej wartości *rental001.csv* jako zestawu danych szkoleniowych. Jeśli wyświetlisz dane wyjściowe modułu **Oceń** (kliknij dane wyjściowe i wybierz **wizualizuj**), możesz zobaczyć, że otrzymujesz przyzwoitą wydajność *AUC* = 0,91. W tym momencie można przystąpić do wdrażania usługi sieci web z tego eksperymentu szkoleniowego.

## <a name="deploy-the-training-and-scoring-web-services"></a>Wdrażanie szkoleń i oceniania usług sieci web
Aby wdrożyć usługę sieci Web szkolenia, kliknij przycisk **Skonfiguruj usługę sieci Web** poniżej kanwy eksperymentu i wybierz pozycję **Wdrażanie usługi sieci Web**. Nazwij tę usługę internetową "Bike Rental Training".

Teraz musisz wdrożyć usługę sieci web oceniania.
Aby to zrobić, kliknij pozycję **Skonfiguruj usługę sieci Web** pod obszarem roboczym i wybierz pozycję **Predictive Web Service**. Spowoduje to utworzenie eksperymentu oceniania.
Musisz wprowadzić kilka drobnych korekt, aby działał jako usługa internetowa. Usuń kolumnę etykiety "cnt" z danych wejściowych i ogranicz dane wyjściowe tylko do identyfikatora wystąpienia i odpowiadającej jej przewidywanej wartości.

Aby zaoszczędzić sobie na tej pracy, możesz otworzyć [eksperyment predykcyjny](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) w galerii, która została już przygotowana.

Aby wdrożyć usługę sieci web, uruchom eksperyment predykcyjny, a następnie kliknij przycisk **Wdrażanie usługi sieci Web** poniżej kanwy. Nazwij usługę internetową punktacji "Bike Rental Scoring".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Tworzenie 10 identycznych punktów końcowych usługi sieci Web za pomocą programu PowerShell
Ta usługa sieci web jest zawiera domyślny punkt końcowy. Ale nie jesteś tak zainteresowany domyślnym punktem końcowym, ponieważ nie można go zaktualizować. Co należy zrobić, to utworzyć 10 dodatkowych punktów końcowych, po jednym dla każdej lokalizacji. Można to zrobić za pomocą programu PowerShell.

Najpierw należy skonfigurować środowisko programu PowerShell:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Następnie uruchom następujące polecenie programu PowerShell:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Teraz utworzono 10 punktów końcowych i wszystkie zawierają ten sam przeszkolony model przeszkolony na *customer001.csv*. Można je wyświetlić w witrynie Azure portal.

![Wyświetlanie listy wyszkolonych modeli w portalu](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aktualizowanie punktów końcowych w celu używania oddzielnych zestawów danych szkoleniowych przy użyciu programu PowerShell
Następnym krokiem jest zaktualizowanie punktów końcowych za pomocą modeli jednoznacznie przeszkolonych na podstawie indywidualnych danych każdego klienta. Ale najpierw musisz wyprodukować te modele z usługi internetowej **Bike Rental Training.** Wróćmy do usługi internetowej **Szkolenia wypożyczania rowerów.** Musisz wywołać jego punkt końcowy BES 10 razy z 10 różnych zestawów danych szkoleniowych w celu wytworzenia 10 różnych modeli. Użyj polecenia cmdlet **Programu PowerShell InovkeAmlWebBESEndpoint,** aby to zrobić.

Należy również podać poświadczenia dla konta magazynu obiektów `$configContent`blob do . Mianowicie, na `AccountName`polach `AccountKey`, `RelativeLocation`i . `AccountName` Może to być jedna z nazw kont, jak widać w **witrynie Azure portal** *(Karta Magazyn).* Po kliknięciu konta magazynu `AccountKey` można je znaleźć, naciskając przycisk **Zarządzaj klawiszami dostępu** u dołu i kopiując *podstawowy klucz dostępu*. Jest `RelativeLocation` to ścieżka względem magazynu, w którym będzie przechowywany nowy model. Na przykład ścieżka `hai/retrain/bike_rental/` w poniższym skrypcie wskazuje na kontener o nazwie `hai`i `/retrain/bike_rental/` są podfolderami. Obecnie nie można utworzyć podfolderów za pośrednictwem interfejsu użytkownika portalu, ale istnieje [kilka Eksploratorów usługi Azure Storage,](../../storage/common/storage-explorers.md) które umożliwiają to zrobić. Zaleca się utworzenie nowego kontenera w magazynie w celu przechowywania nowych wyszkolonych modeli (.iLearner plików) **Add** w następujący sposób: `retrain`ze strony magazynu kliknij przycisk Dodaj u dołu i nazwij go . Podsumowując, niezbędne zmiany w następującym skrypcie `RelativeLocation` odnoszą`"retrain/model' + $seq + '.ilearner"`się do `AccountName`, `AccountKey`i (: ).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> Punkt końcowy BES jest jedynym obsługiwanym trybem dla tej operacji. RRS nie może być używany do produkcji wyszkolonych modeli.
> 
> 

Jak widać powyżej, zamiast konstruowania 10 różnych plików json konfiguracji zadania BES, dynamicznie utworzyć ciąg konfiguracji zamiast. Następnie należy go przesyłać do parametru *jobConfigString* polecenia cmdlet **InvokeAmlWebServceBESEndpoint.** Naprawdę nie ma potrzeby, aby zachować kopię na dysku.

Jeśli wszystko pójdzie dobrze, po pewnym czasie powinieneś zobaczyć 10 .iLearner plików, z *model001.ilearner* do *model010.ilearner*, na koncie usługi Azure storage. Teraz możesz przystąpić do aktualizacji 10 punktów końcowych usługi sieci web z tych modeli przy użyciu **patch-AmlWebServiceEndpoint** powershell polecenia cmdlet. Pamiętaj ponownie, że można załatać tylko domyślne punkty końcowe utworzone programowo wcześniej.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Powinno to działać dość szybko. Po zakończeniu wykonywania, zostanie pomyślnie utworzony 10 predykcyjnych punktów końcowych usługi sieci web. Każdy z nich będzie zawierał przeszkolony model, który jest indywidualnie przeszkolony w zestawie danych specyficzny dla lokalizacji wynajmu, a wszystko to z jednego eksperymentu szkoleniowego. Aby to sprawdzić, można spróbować wywołać te punkty końcowe przy użyciu polecenia cmdlet **InvokeAmlWebServiceRRSEndpoint,** zapewniając im te same dane wejściowe. Należy oczekiwać, aby zobaczyć różne wyniki prognozowania, ponieważ modele są szkolone z różnych zestawów szkoleniowych.

## <a name="full-powershell-script"></a>Pełny skrypt programu PowerShell
Oto lista pełnego kodu źródłowego:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
