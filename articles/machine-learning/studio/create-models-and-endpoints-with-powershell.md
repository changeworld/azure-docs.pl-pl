---
title: Tworzenie wielu modeli z doświadczenia | Dokumentacja firmy Microsoft
description: Tworzenie wielu modeli uczenia maszynowego i sieci web punktów końcowych usługi przy użyciu tego samego algorytmu, ale szkolenia różnych zestawów danych za pomocą programu PowerShell.
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
manager: mwinkle
editor: cgronlun
ms.assetid: 1076b8eb-5a0d-4ac5-8601-8654d9be229f
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.openlocfilehash: 17354891b50138911f36314620f0c826db4b5dac
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833669"
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Tworzenie wielu modeli usługi Machine Learning i punktów końcowych usługi sieci Web na podstawie jednego eksperymentu przy użyciu programu PowerShell
W tym miejscu jest to powszechny problem learning maszyny: Aby utworzyć wiele modeli, w tym samym przepływie pracy szkolenia, które używają tego samego algorytmu. Jednak użytkownicy powinni mieć szkolenia różnych zestawów danych, jako dane wejściowe. W tym artykule opisano w tym celu na dużą skalę w usłudze Azure Machine Learning Studio za pomocą tylko jednego eksperymentu.

Załóżmy na przykład, że właścicielem firmy franchisingowe roweru globalnego dzierżawy. Chcesz skompilować modelu regresji do prognozowania żądanie dzierżawy na podstawie danych historycznych. Masz 1000 lokalizacje wynajem całym świecie i zostały zebrane zestawu danych dla każdej lokalizacji. Obejmują one ważne funkcje takich jak daty, godziny, pogody i ruchu, które są specyficzne dla każdej lokalizacji.

Można uczenia modelu raz przy użyciu wersji scalonych wszystkie zestawy danych we wszystkich lokalizacjach. Jednak każdy z lokalizacji ma unikatowy środowiska. Dlatego lepszym rozwiązaniem byłoby uczenia modelu regresji oddzielnie dla każdej lokalizacji za pomocą zestawu danych. W ten sposób każdego uczonego modelu można wziąć pod uwagę magazynu różne rozmiary, woluminu, geography, populacji, ruch roweru przyjaznego środowiska i więcej.

Może to być najlepszym rozwiązaniem, ale nie chcesz utworzyć 1000 szkolenia eksperymentów w usłudze Azure Machine Learning z każdą z nich reprezentujący unikatową lokalizację. Oprócz jest utrudnione zadań, należy również wydaje się mało wydajne, ponieważ każdego doświadczenia byłyby same składników z wyjątkiem zestawu danych szkoleniowych.

Na szczęście, możesz to zrobić przy użyciu [ponownego trenowania interfejsu API uczenia maszynowego Azure](retrain-models-programmatically.md) i automatyzację zadań z [programu PowerShell usługi Azure Machine Learning](powershell-module.md).

> [!NOTE]
> Aby przykładu szybsze, Zmniejsz liczbę lokalizacji, z 1 000 do 10. Jednak z zasadami i procedurami stosować do 1000 lokalizacji. Jednak jeśli chcesz uczenia z 1000 zestawów danych można równolegle poniższych skryptów programu PowerShell. Jak to zrobić, wykracza poza zakres tego artykułu, ale można znaleźć przykłady PowerShell wielowątkowości w Internecie.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Konfigurowanie eksperyment uczenia
Skorzystaj z przykładu [eksperyment uczenia](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) w [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com). Otwórz tego eksperymentu w Twojej [Azure Machine Learning Studio](https://studio.azureml.net) obszaru roboczego.

> [!NOTE]
> Aby można było skorzystać z tego przykładu, można użyć standardowego obszaru roboczego, a nie wolnego obszaru roboczego. Utwórz jeden punkt końcowy dla każdego klienta — dla wszystkich punktów końcowych 10 - i wymaga standardowe obszaru roboczego, ponieważ obszar roboczy wolne wynosi 3 punkty końcowe. Jeśli masz tylko wolnego obszaru roboczego, można zmienić skrypty umożliwiające tylko tej lokalizacji.
> 
> 

Używa eksperyment **Import danych** modułu, aby zaimportować zestaw danych szkoleniowych *customer001.csv* z kontem magazynu platformy Azure. Załóżmy, że masz zbierane szkolenia zestawów danych z wszystkich lokalizacji wynajem roweru i zapisał je w tej samej lokalizacji magazynu obiektów blob z nazwami plików od *rentalloc001.csv* do *rentalloc10.csv*.

![Obraz](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Należy pamiętać, że **wyjście usługi sieci Web** moduł został dodany do **Train Model** modułu.
Po wdrożeniu tego eksperymentu jako usługę sieci web punktu końcowego skojarzone z danych wyjściowych zwracanych uczonego modelu w formacie pliku .ilearner.

Należy również zauważyć, skonfigurować parametr usługi sieci web, który określa adres URL który **i zaimportuj dane** korzysta z modułu. Umożliwia to parametr umożliwia określenie poszczególnych szkolenia zestawów danych, do nauczenia modelu, dla każdej lokalizacji.
Istnieją inne sposoby można zostało to zrobione. Zapytania SQL z parametrem usługi sieci web umożliwia pobranie danych z bazy danych SQL Azure. Lub użyć **wprowadzania usługi sieci Web** modułu do przekazania w zestawie danych do usługi sieci web.

![Obraz](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Teraz załóżmy Uruchom ten eksperyment uczenia przy użyciu wartości domyślnej *rental001.csv* jako zestawu danych szkoleniowych. Jeśli możesz wyświetlić dane wyjściowe **Evaluate** modułu (kliknij wyjściowy i wybierz **wizualizacja**), widać Ci zadowalający wydajność *AUC* = 0,91. W tym momencie możesz przystąpić do wdrażania usługi sieci web poza tym eksperyment uczenia.

## <a name="deploy-the-training-and-scoring-web-services"></a>Wdrażanie szkolenia i oceniania usług sieci web
Aby wdrożyć usługę sieci web szkolenia, kliknij przycisk **ustawić usługę sieci Web** poniżej obszaru roboczego eksperymentu i wybrać **wdrażanie usługi sieci Web**. Wywołania tej usługi sieci web "" roweru wynajem szkolenia".

Teraz należy wdrożyć oceniania usługi sieci web.
Aby to zrobić, kliknij przycisk **ustawić usługę sieci Web** poniżej kanwy i wybierz **predykcyjnej usługi sieci Web**. Spowoduje to utworzenie oceniania eksperymentu.
Należy dostosować kilka pomocnicza do własnych preferencji jako usługę sieci web. Usuń kolumnę etykiety "cnt" z danych wejściowych i ograniczyć dane wyjściowe do identyfikatora wystąpienia i odpowiednie przewidzianej wartości.

Aby zaoszczędzić pracy, możesz otworzyć [eksperyment predykcyjny](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) w galerii, które zostało już przygotowane.

Aby wdrożyć usługę sieci web, uruchom eksperyment predykcyjny, a następnie kliknij przycisk **wdrażanie usługi sieci Web** znajdujący się poniżej obszaru roboczego. Oceniania usługi sieci web "Roweru oceniania dzierżawa" name ".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Tworzenie punktów końcowych usługi sieci web identyczne 10 przy użyciu programu PowerShell
Ta usługa sieci web jest dostarczany z domyślnego punktu końcowego. Ale nie jesteś zainteresowany jako domyślny punkt końcowy, ponieważ nie można zaktualizować. Co należy zrobić jest utworzenie 10 dodatkowych punktów końcowych, po jednej dla każdej lokalizacji. Można to zrobić przy użyciu programu PowerShell.

Po pierwsze możesz skonfigurować środowiska PowerShell:

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

Teraz utworzyć 10 punktów końcowych i wszystkie zawierają takie same uczenia modelu ćwiczenie *customer001.csv*. Można je wyświetlić w portalu Azure.

![Obraz](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Zaktualizuj punktów końcowych do użycia osobnych szkolenia zestawów danych przy użyciu programu PowerShell
Następnym krokiem jest aby zaktualizować punkty końcowe z modelami jednoznacznie ćwiczenie poszczególnych danych każdego klienta. Jednak najpierw potrzebne do tworzenia tych modeli z **roweru wynajem szkolenia** usługi sieci web. Wróć do **roweru wynajem szkolenia** usługi sieci web. należy wywołać punktu końcowego usługi BES 10 razy z 10 szkolenia różnych zestawów danych, aby można było utworzyć 10 różne modele. Użyj **InovkeAmlWebServiceBESEndpoint** polecenia cmdlet programu PowerShell, aby to zrobić.

Należy również podać poświadczenia dla konta magazynu obiektów blob do `$configContent`. To znaczy, w polach `AccountName`, `AccountKey`, i `RelativeLocation`. `AccountName` Może być jedną z nazwy konta, jak pokazano w **portalu Azure** (*magazynu* kartę). Po kliknięciu na koncie magazynu, jego `AccountKey` można znaleźć, naciskając klawisz **Zarządzaj kluczami dostępu** znajdujący się u dołu i kopiowanie *podstawowy klucz dostępu*. `RelativeLocation` Jest ścieżką względną wobec miejsca przechowywania nowego modelu. Na przykład ścieżka `hai/retrain/bike_rental/` w następujących punktach skryptu do kontenera o nazwie `hai`, i `/retrain/bike_rental/` znajdują się podfoldery. Obecnie nie można utworzyć podfoldery za pośrednictwem interfejsu użytkownika portalu, ale istnieją [kilka eksploratory usługi Storage Azure](../../storage/common/storage-explorers.md) umożliwiające zrobić. Zalecane jest, Utwórz nowy kontener w Twoim magazynie do przechowywania nowe modele przeszkolone (.iLearner pliki) w następujący sposób: na stronie magazynu, kliknij **Dodaj** znajdujący się u dołu i nadaj mu nazwę `retrain`. Podsumowując, wymagane zmiany dotyczące poniższy skrypt odnoszą się do `AccountName`, `AccountKey`, i `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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
> Punkt końcowy usługi BES jest tylko obsługiwany tryb dla tej operacji. Rekordy zasobów nie może służyć do tworzenia modeli przeszkolone.
> 
> 

Jak pokazano powyżej, zamiast tworzenia 10 różnych BES zadania konfiguracji json plików, można dynamicznie utworzyć ciągu konfiguracji. Następnie źródła danych do *jobConfigString* parametr **InvokeAmlWebServceBESEndpoint** polecenia cmdlet. To naprawdę nie trzeba przechowywać kopię na dysku.

Jeśli wszystko odbędzie się poprawnie, po chwili powinno być widoczne 10 plików .iLearner, z *model001.ilearner* do *model010.ilearner*, na Twoim koncie magazynu Azure. Teraz wszystko jest gotowe do aktualizacji 10 oceniania punktów końcowych usługi sieci web przy użyciu tych modeli przy użyciu **AmlWebServiceEndpoint poprawki** polecenia cmdlet programu PowerShell. Ponownie należy pamiętać, że tylko stosowania poprawek innych niż domyślne punkty końcowe, programowo utworzonego wcześniej.

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

To powinno być ono uruchomione stosunkowo szybko. Po zakończeniu wykonywania pracy będzie pomyślnie utworzono 10 punktów końcowych usługi sieci web predykcyjnej. Każdy z nich będzie zawierać uczonego modelu jednoznacznie ćwiczenie określonego zestawu danych do lokalizacji wynajem, wszystko z eksperyment pojedynczego szkolenia. Aby to sprawdzić, możesz spróbować wywoływania tych punktów końcowych przy użyciu **InvokeAmlWebServiceRRSEndpoint** polecenia cmdlet, zapewnienie im z tymi samymi danymi wejściowymi. Należy oczekiwać zobaczyć wyniki prognozowania inny, ponieważ modele są uczone z szkolenia różnych zestawów.

## <a name="full-powershell-script"></a>Pełna skrypt programu PowerShell
Oto lista pełny kod źródłowy:

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
