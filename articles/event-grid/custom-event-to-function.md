---
title: 'Szybki start: wysyłanie zdarzeń niestandardowych do funkcji platformy Azure — siatka zdarzeń'
description: 'Szybki start: publikuj temat i subskrybuj to zdarzenie za pomocą usługi Azure Event Grid i interfejsu wiersza polecenia lub portalu platformy Azure. Funkcja platformy Azure jest używana dla punktu końcowego.'
services: event-grid
keywords: ''
author: banisadr
ms.author: babanisa
ms.date: 11/15/2019
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 5e38571cf84537fd722093b96cd277743e8ce80c
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292150"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Szybki start: kierowanie zdarzeń niestandardowych do funkcji platformy Azure z siatką zdarzeń

Azure Event Grid to usługa obsługi zdarzeń dla chmury. Usługa Azure Functions jest jednym z obsługiwanych programów obsługi zdarzeń. W tym artykule omówiono użycie witryny Azure Portal w celu utworzenia tematu niestandardowego, zasubskrybowania go i wyzwolenia zdarzenia pozwalającego na wyświetlenie wyniku. Wysyłasz zdarzenia do funkcji platformy Azure.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Tworzenie funkcji platformy Azure

Przed zasubskrybowanie tematu niestandardowego utwórzmy funkcję do obsługi zdarzeń. W witrynie Azure portal kliknij przycisk "Utwórz zasób" i wpisz "function", a następnie wybierz "Aplikacja funkcji" i kliknij przycisk Utwórz. Wybierz opcję "Utwórz nowe" w grupie zasobów i nadaj jej nazwę. Użyjesz tego do końca samouczka. Nadaj aplikacji funkcji nazwę, pozostaw przełącznik "Publikuj" na "Kod", wybierz dowolny czas wykonywania i region, a następnie naciśnij przycisk create.

Gdy aplikacja function jest gotowa, przejdź do niej i kliknij przycisk "+ Nowa funkcja". Wybierz "W portalu" dla środowiska programistycznego i naciśnij przycisk kontynuuj. W obszarze Utwórz funkcję wybierz opcję "Więcej szablonów", aby wyświetlić więcej szablonów, a następnie wyszukaj "Wyzwalacz siatki zdarzeń platformy Azure" i wybierz go. Jeśli po raz pierwszy używasz tego wyzwalacza, może być trzeba kliknąć przycisk "Zainstaluj", aby zainstalować rozszerzenie.

![Wyzwalacz siatki zdarzeń funkcji](./media/custom-event-to-function/grid-trigger.png)

Po zainstalowaniu rozszerzenia kliknij przycisk Kontynuuj, nadaj funkcji nazwę, a następnie naciśnij przycisk Utwórz.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Tworzenie tematu niestandardowego

Temat usługi Event Grid udostępnia zdefiniowany przez użytkownika punkt końcowy, w którym publikowane są zdarzenia. 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).
2. Wybierz **pozycję Wszystkie usługi** w menu nawigacyjnym po lewej stronie, wyszukaj pozycję **Siatka zdarzeń**i wybierz pozycję Tematy siatki **zdarzeń**. 

    ![Wybieranie tematów siatki zdarzeń](./media/custom-event-to-function/select-event-grid-topics.png)
3. Na stronie **Tematy siatki zdarzeń** wybierz pozycję + **Dodaj** na pasku narzędzi. 

    ![Przycisk Dodaj temat siatki zdarzeń](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. Na stronie **Tworzenie tematu** wykonaj następujące czynności:

    1. Podaj unikatową **nazwę** tematu niestandardowego. Nazwa tematu musi być unikatowa, ponieważ jest reprezentowana przez wpis DNS. Nie należy używać nazwy widocznej na obrazie. Zamiast tego utwórz własną nazwę — musi mieć od 3 do 50 znaków i może zawierać wyłącznie wartości a–z, A–Z, 0–9 i „-”.
    2. Wybierz swoją **subskrypcję** platformy Azure.
    3. Wybierz tę samą grupę zasobów z poprzednich kroków.
    4. Wybierz **lokalizację** dla tematu siatki zdarzeń.
    5. Zachowaj domyślny **schemat siatki zdarzeń** dla pola Schemat **zdarzeń.** 

       ![Strona Utwórz temat](./media/custom-event-to-function/create-custom-topic.png)
    6. Wybierz **pozycję Utwórz**. 

5. Po utworzeniu tematu niestandardowego zostanie wyświetlone powiadomienie z informacją o powodzeniu. Wybierz **pozycję Przejdź do grupy zasobów**. 

   ![Wyświetlanie powiadomienia z informacją o powodzeniu](./media/custom-event-to-function/success-notification.png)

6. Na stronie **Grupa zasobów** wybierz temat siatki zdarzeń. 

   ![Wybieranie zasobu tematu siatki zdarzeń](./media/custom-event-to-function/select-event-grid-topic.png)

7. Zostanie wyświetlona strona **Temat siatki zdarzeń** dla siatki zdarzeń. Zachowaj otwartą stronę. Można go użyć w dalszej części przewodnika Szybki start. 

    ![Strona główna tematu siatki wydarzenia](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Subskrybowanie do tematu niestandardowego

Zasubskrybowanie tematu pozwala poinformować usługę Event Grid o tym, które zdarzenia chcesz śledzić i gdzie mają być one wysyłane.

1. Teraz na stronie **Temat siatki zdarzeń** dla tematu niestandardowego wybierz + **Subskrypcja zdarzeń** na pasku narzędzi.

   ![Dodawanie subskrypcji zdarzeń](./media/custom-event-to-function/new-event-subscription.png)

2. Na stronie **Tworzenie subskrypcji zdarzeń** wykonaj następujące czynności:
    1. Wprowadź **nazwę** subskrypcji zdarzenia.
    3. Wybierz **funkcję platformy Azure** dla typu punktu **końcowego**. 
    4. Wybierz **pozycję Wybierz punkt końcowy**. 

       ![Podawanie wartości związanych z subskrypcją zdarzeń](./media/custom-event-to-function/provide-subscription-values.png)

    5. Dla punktu końcowego funkcji wybierz subskrypcję platformy Azure i grupę zasobów, w której znajduje się aplikacja funkcji, a następnie wybierz aplikację funkcji i funkcję utworzoną wcześniej. Wybierz pozycję **Potwierdź wybór**.

       ![Podawanie adresu URL punktu końcowego](./media/custom-event-to-function/provide-endpoint.png)

    6. Na stronie **Utwórz subskrypcję zdarzeń** wybierz pozycję **Utwórz**.

## <a name="send-an-event-to-your-topic"></a>Wysyłanie zdarzenia do tematu

Teraz wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego. Możesz wysłać zdarzenie testowe do niestandardowego tematu za pomocą interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Zwykle dane zdarzenia są wysyłane przez aplikację lub usługę platformy Azure.

W pierwszym przykładzie użyto interfejsu wiersza polecenia platformy Azure. Pobierany jest adres URL i klucz dla tematu niestandardowego oraz przykładowe dane zdarzenia. Użyj nazwy tematu niestandardowego dla wartości `<topic name>`. Tworzy ona przykładowe dane zdarzenia. Element `data` danych JSON to ładunek zdarzenia. W tym polu można umieścić dowolną poprawnie sformułowaną zawartość JSON. Można też używać pola tematu do zaawansowanego routingu i filtrowania. CURL to narzędzie, które wysyła żądania HTTP.


### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
1. W witrynie Azure portal wybierz pozycję **Cloud Shell**. Wybierz **bash** w lewym górnym rogu okna Powłoki chmury. 

    ![Powłoka chmury - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Uruchom następujące polecenie, aby uzyskać **punkt końcowy** dla tematu: Po skopiowaniu i wklejeniu polecenia należy zaktualizować **nazwę tematu** i **nazwę grupy zasobów** przed uruchomieniem polecenia. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Uruchom następujące polecenie, aby uzyskać **klucz** dla tematu niestandardowego: Po skopiowaniu i wklejeniu polecenia należy zaktualizować **nazwę tematu** i nazwę **grupy zasobów** przed uruchomieniem polecenia. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Skopiuj następującą instrukcję z definicją zdarzenia i naciśnij klawisz **ENTER**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Uruchom następujące polecenie **Curl,** aby zaksięgować zdarzenie:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
Drugi przykład obejmuje wykonanie podobnych kroków przy użyciu programu PowerShell.

1. W witrynie Azure **Cloud Shell** portal wybierz cloud `https://shell.azure.com/`shell (alternatywnie przejdź do ). Wybierz program **PowerShell** w lewym górnym rogu okna Powłoki chmury. Zobacz przykładowy obraz okna **powłoki chmury** w sekcji interfejsu wiersza polecenia platformy Azure.
2. Ustaw następujące zmienne. Po skopiowaniu i wklejeniu każdego polecenia należy zaktualizować **nazwę tematu** i **nazwę grupy zasobów** przed uruchomieniem polecenia:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. Uruchom następujące polecenia, aby uzyskać **punkt końcowy** i **klucze** dla tematu:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Przygotuj wydarzenie. Skopiuj i uruchom instrukcje w oknie Powłoka chmury. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. Użyj polecenia cmdlet **Invoke-WebRequest,** aby wysłać zdarzenie. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Sprawdź w Podglądzie siatki zdarzeń
Zdarzenie zostało wyzwolone, a usługa Event Grid wysłała komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Przejdź do funkcji wyzwalania siatki zdarzeń i otwórz dzienniki. Powinna zostać wyświetlone kopia ładunku danych zdarzenia w dziennikach. Jeśli nie upewnij się, że najpierw otworzysz okno dzienników lub natkniesz się ponownie, a następnie spróbuj ponownie wysłać zdarzenie testowe.

![Dziennik wyzwalacza funkcji pomyślnej](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli zamierzasz kontynuować pracę z tym zdarzeniem, nie usuwaj zasobów utworzonych w tym artykule. W przeciwnym razie usuń zasoby utworzone w ramach tego artykułu.

1. Wybierz **pozycję Grupy zasobów** w menu po lewej stronie. Jeśli nie widzisz go w menu po lewej stronie, wybierz **polecenie Wszystkie usługi** w menu po lewej stronie i wybierz pozycję Grupy **zasobów**. 
2. Wybierz grupę zasobów, aby uruchomić stronę **Grupa zasobów.** 
3. Wybierz **pozycję Usuń grupę zasobów** na pasku narzędzi. 
4. Potwierdź usunięcie, wprowadzając nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**. 

    ![Grupy zasobów](./media/custom-event-to-function/delete-resource-groups.png)

    Inna grupa zasobów widoczna na obrazie została utworzona i użyta przez okno Powłoka chmury. Usuń go, jeśli nie planujesz później korzystać z okna Powłoki chmury. 

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak tworzyć tematy i subskrypcje zdarzeń. Dowiedz się więcej na temat tego, co może Ci ułatwić usługa Event Grid:

- [Event Grid — informacje](overview.md)
- [Kierowanie zdarzeń usługi Blob Storage do niestandardowego internetowego punktu końcowego](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorowanie zmian maszyn wirtualnych za pomocą usług Azure Event Grid i Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md)
