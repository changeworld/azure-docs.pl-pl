---
title: Tworzenie wielu punktów końcowych dla modelu
titleSuffix: ML Studio (classic) - Azure
description: Tworzenie wielu modeli usługi Machine Learning i sieci web punkty końcowe usługi z tego samego algorytmu, ale szkolenia różnych zestawów danych przy użyciu programu PowerShell.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 9c1557efd87bf75ec59c9b65112b2bb3d0c678db
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153625"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Tworzenie modeli Studio (klasycznych) i punktów końcowych usługi sieci Web przy użyciu programu PowerShell w ramach jednego eksperymentu

Poniżej przedstawiono typowe nauczanym problemem maszyny: Aby utworzyć wiele modeli, które mają tego samego przepływu pracy szkolenia i używają tego samego algorytmu. Jednak aby użytkownicy posiadali zestawów danych szkoleniowych różnych jako dane wejściowe. W tym artykule pokazano, jak to zrobić na dużą skalę w Azure Machine Learning Studio (klasyczny) przy użyciu tylko jednego eksperymentu.

Załóżmy na przykład, że jesteś właścicielem firmy udzielają wypożyczeń rowerów globalnego. Chcesz utworzyć model regresji, aby przewidzieć żądanie dzierżawy na podstawie danych historycznych. Masz 1000 wypożyczeń lokalizacjach na całym świecie i zostały zebrane dla każdej lokalizacji zestawu danych. Obejmują one ważne funkcje takich jak daty, godziny, pogody i ruchu, które są specyficzne dla każdej lokalizacji.

Można uczenie modelu raz przy użyciu scalonych wersję wszystkich zestawów danych we wszystkich lokalizacjach. Jednak każdy z lokalizacji ma unikatowe środowisko. Dlatego lepszym rozwiązaniem byłoby uczenia modelu regresji osobno dla każdej lokalizacji przy użyciu zestawu danych. W ten sposób każdy uczonego modelu można wziąć pod uwagę rozmiarów różnych magazynu, woluminu, lokalizacji geograficznej, populacji, ruch roweru przyjaznego środowiska i więcej.

To rozwiązanie może być najlepszym rozwiązaniem, ale nie chcesz tworzyć 1 000 eksperymentów szkoleniowych w Azure Machine Learning Studio (klasyczny) z każdą z nich reprezentującą unikatową lokalizację. Oprócz jest trudne zadanie, wydaje się być także nieefektywne od każdego doświadczenia miałby te same składniki, z wyjątkiem zestaw danych szkoleniowych.

Na szczęście można to zrobić za pomocą [interfejsu API ponownego szkolenia Azure Machine Learning Studio (klasycznego)](/azure/machine-learning/studio/retrain-machine-learning-model) i automatyzując zadanie przy użyciu [Azure Machine Learning Studio (klasycznego) programu PowerShell](powershell-module.md).

> [!NOTE]
> Aby wprowadzić swój przykład działają szybciej, Zmniejsz liczbę lokalizacje od 1000 do 10. Jednak te same zasady i procedury dotyczą 1000 lokalizacji. Jednak jeśli chcesz szkolenie z 1000 zestawów danych możesz zechcieć do równoległego uruchamiania następujących skryptów programu PowerShell. Jak to zrobić, wykracza poza zakres tego artykułu, ale możesz odnaleźć przykłady programu PowerShell wielowątkowości w Internecie.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Konfigurowanie eksperymentu szkolenia
Skorzystaj z przykładowego [eksperymentu szkoleniowego](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) , który znajduje się w [Cortana Intelligence Gallery](https://gallery.azure.ai). Otwórz ten eksperyment w obszarze roboczym [Azure Machine Learning Studio (klasycznym)](https://studio.azureml.net) .

> [!NOTE]
> Aby można było skorzystać z tego przykładu, można użyć standardowego obszaru roboczego zamiast obszaru roboczego warstwy bezpłatna. Utwórz jeden punkt końcowy dla każdego klienta — w sumie 10 punktów końcowych — i wymaga obszaru roboczego warstwy standardowa, ponieważ obszaru roboczego warstwy bezpłatna jest ograniczona do 3 punktów końcowych.
> 
> 

Eksperyment używa modułu **Import danych** do importowania zestawu danych szkoleniowych *customer001. csv* z konta usługi Azure Storage. Załóżmy, że zebrano zbiory danych szkoleń ze wszystkich lokalizacji dzierżawy rowerów i zapisano je w tej samej lokalizacji magazynu obiektów blob z nazwami plików z *rentalloc001. csv* do *rentalloc10. csv*.

![Moduł czytnika importuje dane z obiektu blob platformy Azure](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Należy zauważyć, że moduł **danych wyjściowych usługi sieci Web** został dodany do modułu **uczenie modelu** .
Podczas tego eksperymentu jest wdrażana jako usługę sieci web, punkt końcowy skojarzony z danych wyjściowych zwracanych uczonego modelu w formacie pliku .ilearner.

Należy również pamiętać, że należy skonfigurować parametr usługi sieci Web, który określa adres URL, który jest wykorzystywany przez moduł **importu danych** . Dzięki temu można użyć parametru do określenia zestawów danych szkoleniowych poszczególnych do nauczenia modelu, dla każdej lokalizacji.
Istnieją inne sposoby może zostało to zrobione. Za pomocą zapytań SQL i parametr usługi sieci web do pobierania danych z bazy danych SQL Azure. Można też użyć modułu **danych wejściowych usługi sieci Web** do przekazywania zestawu danych do usługi sieci Web.

![Przeszkolone dane wyjściowe modułu modelu do modułu wyjściowego usługi sieci Web](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Teraz Uruchommy ten eksperyment szkoleniowy przy użyciu domyślnej wartości *rental001. csv* jako zestawu danych szkoleniowych. Jeśli wyświetli się dane wyjściowe modułu **szacowania** (kliknij dane wyjściowe i wybierz polecenie **wizualizator**), zobaczysz znośnego wydajność *AUC* = 0,91. W tym momencie możesz przystąpić do wdrażania usługi sieci web w taki sposób, poza tym eksperymentu szkolenia.

## <a name="deploy-the-training-and-scoring-web-services"></a>Wdrażanie szkolenia i oceniania usługi sieci web
Aby wdrożyć usługę szkoleniową sieci Web, kliknij przycisk **Skonfiguruj usługę sieci Web** pod kanwą eksperymentu i wybierz pozycję **Wdróż usługę sieci Web**. Wywoływanie tej usługi sieci web "Szkolenia wypożyczeń rowerów".

Teraz zajdzie potrzeba wdrożenia usługi internetowej przyznawania ocen.
Aby to zrobić, kliknij pozycję **Skonfiguruj usługę sieci Web** pod kanwą i wybierz opcję **predykcyjna usługa sieci Web**. Spowoduje to utworzenie oceniania eksperymentu.
Należy wprowadzić kilka zmian pomocnicza, dzięki którym działa jako usługa sieci web. Usuń kolumnę etykietę "cnt" w danych wejściowych i ogranicza dane wyjściowe z identyfikatorem wystąpienia i odpowiednie przewidzianej wartości.

Aby zapisać to działanie, możesz otworzyć [eksperyment predykcyjny](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) w galerii, która została już przygotowana.

Aby wdrożyć usługę sieci Web, uruchom eksperyment predykcyjny, a następnie kliknij przycisk **Wdróż usługę sieci Web** pod kanwą. Nazwa usługi internetowej przyznawania ocen "Oceniania wypożyczeń rowerów".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Tworzenie punktów końcowych usługi sieci web identyczne 10 przy użyciu programu PowerShell
Ta usługa sieci web jest dostarczany z domyślny punkt końcowy. Ale nie jesteś zainteresowany jako domyślny punkt końcowy, ponieważ nie można zaktualizować. Co należy zrobić jest utworzenie 10 dodatkowych punktów końcowych, jednej dla każdej lokalizacji. Można to zrobić za pomocą programu PowerShell.

Najpierw należy skonfigurować środowisko PowerShell:

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

Teraz utworzono 10 punktów końcowych i wszystkie zawierają ten sam szkolony model przeszkolony w *customer001. csv*. Można je wyświetlić w witrynie Azure portal.

![Wyświetlanie listy modeli szkolonych w portalu](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aktualizuj punkty końcowe do użycia osobnych szkolenia zestawów danych przy użyciu programu PowerShell
Następnym krokiem jest zaktualizować punktów końcowych przy użyciu modeli jednoznacznie skonfigurowanych pod kątem indywidualnych danych każdego klienta. Najpierw należy utworzyć te modele z poziomu usługi sieci Web **szkoleń rowerowych** . Wróćmy do usługi sieci Web **szkoleń rowerowych** . Należy wywołać jej punkt końcowy usługi BES w 10 razy z 10 szkolenia różnych zestawów danych w celu wygenerowania 10 różnych modeli. Aby to zrobić, użyj polecenia cmdlet programu **InovkeAmlWebServiceBESEndpoint** PowerShell.

Musisz również podać poświadczenia dla konta usługi BLOB Storage w `$configContent`. Mianowicie, w polach `AccountName`, `AccountKey`i `RelativeLocation`. `AccountName` może być jedną z nazw kont, jak widać na karcie **Azure Portal** (*Storage* ). Po kliknięciu konta magazynu `AccountKey` można znaleźć, naciskając przycisk **Zarządzaj kluczami dostępu** u dołu i kopiując *podstawowy klucz dostępu*. `RelativeLocation` jest ścieżką względną do magazynu, w którym będzie przechowywany nowy model. Na przykład ścieżka `hai/retrain/bike_rental/` w poniższym skrypcie wskazuje kontener o nazwie `hai`, a `/retrain/bike_rental/` są podfolderami. Obecnie nie można tworzyć podfolderów za pomocą interfejsu użytkownika portalu, ale istnieje [kilka eksploratorów usługi Azure Storage](../../storage/common/storage-explorers.md) , które umożliwiają wykonanie tej czynności. Zaleca się utworzenie nowego kontenera w magazynie do przechowywania nowych przeszkolonych modeli (plików iLearner) w następujący sposób: ze strony magazynu kliknij przycisk **Dodaj** u dołu i nadaj mu nazwę `retrain`. Podsumowując, niezbędne zmiany w poniższym skrypcie odnoszą się do `AccountName`, `AccountKey`i `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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
> Punkt końcowy usługi BES jest jedynym trybem obsługiwane dla tej operacji. Rekordy zasobów nie można używać do uczone modele do produkcji.
> 
> 

Jak pokazano powyżej, zamiast tworzenia 10 różnych BES zadania konfiguracji pliki json, możesz dynamicznie utworzyć ciąg konfiguracji. Następnie podawanie go do parametru *jobConfigString* polecenia cmdlet **InvokeAmlWebServceBESEndpoint** . Jest tak naprawdę nie trzeba przechowywać kopię na dysku.

Jeśli wszystko przebiegnie prawidłowo, po czasie powinno zostać wyświetlone 10. iLearner plików z *model001. iLearner* do *model010. iLearner*w ramach konta usługi Azure Storage. Teraz możesz przystąpić do aktualizowania 10 punktów końcowych usługi sieci Web z tymi modelami za pomocą polecenia cmdlet **AmlWebServiceEndpoint** programu PowerShell. Ponownie należy pamiętać, że tylko stosowanie poprawek innych niż domyślne punkty końcowe, programowo wcześniej utworzonego.

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

Uruchamiać to dość szybko. Po zakończeniu wykonywania, zostanie pomyślnie utworzono 10 punktów końcowych usługi predykcyjne sieci web. Każdy z nich będzie zawierać uczonego modelu jednoznacznie skonfigurowanych pod kątem określonego zestawu danych do lokalizacji dzierżawy, wszystko z jednego eksperyment. Aby to sprawdzić, możesz spróbować wywołać te punkty końcowe za pomocą polecenia cmdlet **InvokeAmlWebServiceRRSEndpoint** , podając te same dane wejściowe. Należy się spodziewać Zobacz wyniki przewidywań różnych, ponieważ modele są uczone z użyciem różnych szkolenia zestawów.

## <a name="full-powershell-script"></a>Pełen skrypt programu PowerShell
Poniżej przedstawiono listę pełny kod źródłowy:

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
