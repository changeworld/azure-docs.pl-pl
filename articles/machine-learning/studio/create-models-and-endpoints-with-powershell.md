---
title: Utwórz wiele punktów końcowych dla modelu
titleSuffix: Azure Machine Learning Studio
description: Tworzenie wielu modeli usługi Machine Learning i sieci web punkty końcowe usługi z tego samego algorytmu, ale szkolenia różnych zestawów danych przy użyciu programu PowerShell.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: a191a7adc2c43337b663fc44a8ef40df9d8ffef4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773715"
---
# <a name="use-powershell-to-create-studio-models-and-web-service-endpoints-from-one-experiment"></a>Tworzenie modeli Studio i punkty końcowe usługi sieci web na podstawie jednego eksperymentu przy użyciu programu PowerShell

Poniżej przedstawiono typowe nauczanym problemem maszyny: Chcesz utworzyć wiele modeli, które mają tego samego przepływu pracy szkolenia i używają tego samego algorytmu. Jednak aby użytkownicy posiadali zestawów danych szkoleniowych różnych jako dane wejściowe. W tym artykule dowiesz się, jak to zrobić na dużą skalę w usłudze Azure Machine Learning Studio za pomocą tylko jednego eksperymentu.

Załóżmy na przykład, że jesteś właścicielem firmy udzielają wypożyczeń rowerów globalnego. Chcesz utworzyć model regresji, aby przewidzieć żądanie dzierżawy na podstawie danych historycznych. Masz 1000 wypożyczeń lokalizacjach na całym świecie i zostały zebrane dla każdej lokalizacji zestawu danych. Obejmują one ważne funkcje takich jak daty, godziny, pogody i ruchu, które są specyficzne dla każdej lokalizacji.

Można uczenie modelu raz przy użyciu scalonych wersję wszystkich zestawów danych we wszystkich lokalizacjach. Jednak każdy z lokalizacji ma unikatowe środowisko. Dlatego lepszym rozwiązaniem byłoby uczenia modelu regresji osobno dla każdej lokalizacji przy użyciu zestawu danych. W ten sposób każdy uczonego modelu można wziąć pod uwagę rozmiarów różnych magazynu, woluminu, lokalizacji geograficznej, populacji, ruch roweru przyjaznego środowiska i więcej.

Może to być najlepszym rozwiązaniem, ale nie chcesz utworzyć 1000 szkolenia eksperymentów w usłudze Azure Machine Learning Studio z każdym z nich reprezentuje unikatową lokalizację. Oprócz jest trudne zadanie, wydaje się być także nieefektywne od każdego doświadczenia miałby te same składniki, z wyjątkiem zestaw danych szkoleniowych.

Na szczęście, można to zrobić za pomocą [ponownego trenowania interfejsu API usługi Azure Machine Learning Studio](/azure/machine-learning/studio/retrain-machine-learning-model) i automatyzacja zadań za pomocą [programu PowerShell usługi Azure Machine Learning Studio](powershell-module.md).

> [!NOTE]
> Aby wprowadzić swój przykład działają szybciej, Zmniejsz liczbę lokalizacje od 1000 do 10. Jednak te same zasady i procedury dotyczą 1000 lokalizacji. Jednak jeśli chcesz szkolenie z 1000 zestawów danych możesz zechcieć do równoległego uruchamiania następujących skryptów programu PowerShell. Jak to zrobić, wykracza poza zakres tego artykułu, ale możesz odnaleźć przykłady programu PowerShell wielowątkowości w Internecie.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Konfigurowanie eksperymentu szkolenia
Skorzystaj z przykładu [eksperymentu szkolenia](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) w [Cortana Intelligence Gallery](https://gallery.azure.ai). Otwórz ten eksperyment w swojej [Azure Machine Learning Studio](https://studio.azureml.net) obszaru roboczego.

> [!NOTE]
> Aby można było skorzystać z tego przykładu, można użyć standardowego obszaru roboczego zamiast obszaru roboczego warstwy bezpłatna. Utwórz jeden punkt końcowy dla każdego klienta — w sumie 10 punktów końcowych — i wymaga obszaru roboczego warstwy standardowa, ponieważ obszaru roboczego warstwy bezpłatna jest ograniczona do 3 punktów końcowych. Jeśli masz tylko bezpłatny obszar roboczy, można zmienić skrypty w sposób umożliwiający tylko Ty lokalizacje.
> 
> 

Eksperyment wykorzystuje **Import danych** modułu, aby zaimportować zestaw danych szkoleniowych *customer001.csv* z konta usługi Azure storage. Załóżmy, że masz zebranych zestawów danych szkoleniowych z wszystkie lokalizacje wypożyczeń rowerów i zapisał je w tej samej lokalizacji magazynu obiektów blob z nazwami plików w zakresie od *rentalloc001.csv* do *rentalloc10.csv*.

![Moduł czytnika importuje dane z obiektu blob platformy Azure](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Należy pamiętać, że **danych wyjściowych usługi sieci Web** moduł został dodany do **Train Model** modułu.
Podczas tego eksperymentu jest wdrażana jako usługę sieci web, punkt końcowy skojarzony z danych wyjściowych zwracanych uczonego modelu w formacie pliku .ilearner.

Należy również zauważyć, skonfiguruj parametr usługi sieci web, który określa adres URL, **importu danych** korzysta z modułu. Dzięki temu można użyć parametru do określenia zestawów danych szkoleniowych poszczególnych do nauczenia modelu, dla każdej lokalizacji.
Istnieją inne sposoby może zostało to zrobione. Za pomocą zapytań SQL i parametr usługi sieci web do pobierania danych z bazy danych SQL Azure. Możesz też **dane wejściowe usługi sieci Web** modułu do przekazania w zestawie danych do usługi sieci web.

![Dane wyjściowe modułu Uczonego modelu modułu danych wyjściowych usługi sieci Web](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Teraz uruchommy tego eksperymentu szkolenia przy użyciu wartości domyślnej *rental001.csv* jako zestaw danych szkoleniowych. Jeśli wyświetlisz dane wyjściowe **Evaluate** modułu (kliknij danych wyjściowych, a następnie wybierz pozycję **Visualize**), widać Ci znośnego wydajność *AUC* = 0.91. W tym momencie możesz przystąpić do wdrażania usługi sieci web w taki sposób, poza tym eksperymentu szkolenia.

## <a name="deploy-the-training-and-scoring-web-services"></a>Wdrażanie szkolenia i oceniania usługi sieci web
Aby wdrożyć usługę sieci web szkolenia, kliknij **ustawić usługę sieci Web** poniżej obszaru roboczego eksperymentu i wybrać **wdrażanie usługi sieci Web**. Wywoływanie tej usługi sieci web "Szkolenia wypożyczeń rowerów".

Teraz zajdzie potrzeba wdrożenia usługi internetowej przyznawania ocen.
Aby to zrobić, kliknij przycisk **ustawić usługę sieci Web** poniżej obszaru roboczego, a następnie wybierz pozycję **predykcyjne usługi sieci Web**. Spowoduje to utworzenie oceniania eksperymentu.
Należy wprowadzić kilka zmian pomocnicza, dzięki którym działa jako usługa sieci web. Usuń kolumnę etykietę "cnt" w danych wejściowych i ogranicza dane wyjściowe z identyfikatorem wystąpienia i odpowiednie przewidzianej wartości.

Aby zapisać sobie pracę, możesz otworzyć [eksperyment predykcyjny](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) w galerii, które zostało już przygotowane.

Aby wdrożyć usługę sieci web, uruchom eksperyment predykcyjny, a następnie kliknij przycisk **wdrażanie usługi sieci Web** znajdujący się poniżej obszaru roboczego. Nazwa usługi internetowej przyznawania ocen "Oceniania wypożyczeń rowerów".

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

Teraz utworzono 10 punktów końcowych, a wszystkie zawierają takie same uczony model jest uczony w *customer001.csv*. Można je wyświetlić w witrynie Azure portal.

![Wyświetl listę przeszkolone modele w portalu](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aktualizuj punkty końcowe do użycia osobnych szkolenia zestawów danych przy użyciu programu PowerShell
Następnym krokiem jest zaktualizować punktów końcowych przy użyciu modeli jednoznacznie skonfigurowanych pod kątem indywidualnych danych każdego klienta. Jednak najpierw potrzebne do tworzenia tych modeli na podstawie **szkolenia wypożyczeń rowerów** usługi sieci web. Wróć do **szkolenia wypożyczeń rowerów** usługi sieci web. Należy wywołać jej punkt końcowy usługi BES w 10 razy z 10 szkolenia różnych zestawów danych w celu wygenerowania 10 różnych modeli. Użyj **InovkeAmlWebServiceBESEndpoint** polecenia cmdlet programu PowerShell, aby to zrobić.

Należy również podać poświadczenia dla konta usługi blob storage do `$configContent`. To znaczy, w polach `AccountName`, `AccountKey`, i `RelativeLocation`. `AccountName` Może być jednym z nazwy konta, jak pokazano w **witryny Azure portal** (*magazynu* karty). Po kliknięciu na koncie magazynu, jego `AccountKey` można znaleźć, naciskając klawisz **Zarządzaj kluczami dostępu** znajdujący się u dołu i kopiowanie *podstawowy klucz dostępu*. `RelativeLocation` Jest ścieżką względną magazynu, w którym będzie przechowywany nowy model. Na przykład ścieżka `hai/retrain/bike_rental/` w następujących punktach skryptu do kontenera o nazwie `hai`, i `/retrain/bike_rental/` znajdują się podfoldery. Obecnie nie można utworzyć podfoldery, za pośrednictwem portalu, interfejsu użytkownika, ale istnieją [kilka Eksploratory usługi Azure Storage](../../storage/common/storage-explorers.md) pozwala to zrobić. Zalecane jest, Utwórz nowy kontener w usłudze storage do przechowywania nowych modeli uczonego (.iLearner pliki) w następujący sposób: ze strony magazynu, kliknij przycisk **Dodaj** znajdujący się u dołu i nadaj mu nazwę `retrain`. Podsumowując, niezbędne zmiany do następującego skryptu odnoszą się do `AccountName`, `AccountKey`, i `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

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

Jak pokazano powyżej, zamiast tworzenia 10 różnych BES zadania konfiguracji pliki json, możesz dynamicznie utworzyć ciąg konfiguracji. Następnie przesłać go do *jobConfigString* parametru **InvokeAmlWebServceBESEndpoint** polecenia cmdlet. Jest tak naprawdę nie trzeba przechowywać kopię na dysku.

Jeśli wszystko przebiegnie poprawnie, w po chwili 10 plików .iLearner, powinien zostać wyświetlony z *model001.ilearner* do *model010.ilearner*, w ramach konta magazynu platformy Azure. Teraz możesz przystąpić do aktualizacji 10 oceniania punkty końcowe usługi sieci web za pomocą tych modeli za pomocą **Patch-AmlWebServiceEndpoint** polecenia cmdlet programu PowerShell. Ponownie należy pamiętać, że tylko stosowanie poprawek innych niż domyślne punkty końcowe, programowo wcześniej utworzonego.

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

Uruchamiać to dość szybko. Po zakończeniu wykonywania, zostanie pomyślnie utworzono 10 punktów końcowych usługi predykcyjne sieci web. Każdy z nich będzie zawierać uczonego modelu jednoznacznie skonfigurowanych pod kątem określonego zestawu danych do lokalizacji dzierżawy, wszystko z jednego eksperyment. Aby to sprawdzić, możesz spróbować wywoływanie tych punktów końcowych przy użyciu **InvokeAmlWebServiceRRSEndpoint** polecenia cmdlet, dostarczając im z tymi samymi danymi wejściowymi. Należy się spodziewać Zobacz wyniki przewidywań różnych, ponieważ modele są uczone z użyciem różnych szkolenia zestawów.

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
