---
title: Zdarzenia niestandardowe dla usługi Azure Event Grid przy użyciu witryny Azure Portal | Microsoft Docs
description: Za pomocą usługi Azure Event Grid i programu PowerShell można opublikować temat i subskrybować dane zdarzenie.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 04/05/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: a16a9bdb866803a65bf7204628b735bd9ac60aee
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302563"
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>Tworzenie i kierowanie zdarzeń niestandardowych za pomocą witryny Azure Portal i usługi Event Grid

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym artykule omówiono tworzenie tematu niestandardowego, subskrybowanie go i wyzwalanie zdarzenia w celu wyświetlenia wyniku za pomocą witryny Azure Portal. Wysyłasz zdarzenie do funkcji platformy Azure, która rejestruje dane zdarzenia. Po zakończeniu przekonasz się, że dane zdarzenia zostały wysłane do punktu końcowego i zarejestrowane.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-custom-topic"></a>Tworzenie tematu niestandardowego

Temat usługi Event Grid udostępnia zdefiniowany przez użytkownika punkt końcowy, w którym publikowane są zdarzenia. 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

1. Aby utworzyć temat niestandardowy, wybierz pozycję **Utwórz zasób**. 

   ![Tworzenie zasobu](./media/custom-event-quickstart-portal/create-resource.png)

1. Wyszukaj pozycję *Temat usługi Event Grid* i wybierz ją z dostępnych opcji.

   ![Wyszukiwanie tematu usługi Event Grid](./media/custom-event-quickstart-portal/search-event-grid.png)

1. Wybierz pozycję **Utwórz**.

   ![Kroki początkowe](./media/custom-event-quickstart-portal/select-create.png)

1. Podaj unikatową nazwę dla tematu niestandardowego. Nazwa tematu musi być unikatowa, ponieważ jest reprezentowana przez wpis DNS. Nie należy używać nazwy użytej na obrazie. Zamiast tego utwórz własną nazwę. Wybierz jeden z [obsługiwanych regionów](overview.md). Podaj nazwę grupy zasobów. Wybierz pozycję **Utwórz**.

   ![Podawanie wartości tematu usługi Event Grid](./media/custom-event-quickstart-portal/create-custom-topic.png)

1. Po utworzeniu tematu niestandardowego zostanie wyświetlone powiadomienie z informacją o powodzeniu.

   ![Wyświetlanie powiadomienia z informacją o powodzeniu](./media/custom-event-quickstart-portal/success-notification.png)

   Jeśli wdrożenie nie powiodło się, sprawdź, co było przyczyną błędu. Wybierz pozycję **Wdrożenie nie powiodło się**.

   ![Wybieranie pozycji Wdrożenie nie powiodło się](./media/custom-event-quickstart-portal/select-failed.png)

   Wybierz komunikat o błędzie.

   ![Wybieranie pozycji Wdrożenie nie powiodło się](./media/custom-event-quickstart-portal/failed-details.png)

   Na poniższej ilustracji przedstawiono wdrożenie, które zostało zakończone niepowodzeniem, ponieważ nazwa dla tematu niestandardowego jest już używana. Jeśli zostanie wyświetlony ten błąd, spróbuj ponownie wykonać wdrożenie z inną nazwą.

   ![Konflikt nazw](./media/custom-event-quickstart-portal/name-conflict.png)

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Przed zasubskrybowaniem tematu utwórzmy punkt końcowy dla komunikatów o zdarzeniach. W tym artykule używasz usługi Azure Functions do utworzenia aplikacji funkcji dla punktu końcowego.

1. Aby utworzyć funkcję, wybierz pozycję **Utwórz zasób**.

   ![Tworzenie zasobu](./media/custom-event-quickstart-portal/create-resource-small.png)

1. Wybierz pozycję **Obliczanie** i **Aplikacja funkcji**.

   ![Tworzenie funkcji](./media/custom-event-quickstart-portal/create-function.png)

1. Podaj unikatową nazwę dla funkcji platformy Azure. Nie należy używać nazwy użytej na obrazie. Wybierz grupę zasobów utworzoną w ramach tego artykułu. Jako plan hostingu użyj **planu Zużycie**. Użyj sugerowanego nowego konta magazynu. Po podaniu wartości wybierz pozycję **Utwórz**.

   ![Podawanie wartości funkcji](./media/custom-event-quickstart-portal/provide-function-values.png)

1. Po zakończeniu wdrożenia wybierz pozycję **Przejdź do zasobu**.

   ![Przechodzenie do zasobu](./media/custom-event-quickstart-portal/go-to-resource.png)

1. Obok pozycji **Funkcje** wybierz pozycję **+**.

   ![Dodawanie funkcji](./media/custom-event-quickstart-portal/add-function.png)

1. Z dostępnych opcji wybierz pozycję **Funkcja niestandardowa**.

   ![Funkcja niestandardowa](./media/custom-event-quickstart-portal/select-custom-function.png)

1. Przewiń w dół do pozycji **Wyzwalacz usługi Event Grid**. Wybierz pozycję **C#**.

   ![Wybieranie wyzwalacza usługi Event Grid](./media/custom-event-quickstart-portal/select-event-grid-trigger.png)

1. Zaakceptuj wartości domyślne, a następnie wybierz pozycję **Utwórz**.

   ![Nowa funkcja](./media/custom-event-quickstart-portal/new-function.png)

Twoja funkcja jest teraz gotowa do odbierania zdarzeń.

## <a name="subscribe-to-a-topic"></a>Subskrybowanie tematu

Subskrybowanie tematu ma poinformować usługę Event Grid o tym, które zdarzenia chcesz śledzić i gdzie mają być one wysyłane.

1. W funkcji platformy Azure wybierz pozycję **Dodaj subskrypcję usługi Event Grid**.

   ![Dodawanie subskrypcji usługi Event Grid](./media/custom-event-quickstart-portal/add-event-grid-subscription.png)

1. Podaj wartości dla subskrypcji. Jako typ tematu wybierz wartość **Tematy usługi Event Grid**. Dla subskrypcji i grupy zasobów wybierz grupę subskrypcję i grupę zasobów, w ramach której utworzono temat niestandardowy. Na przykład wybierz nazwę tematu niestandardowego. Punkt końcowy subskrybenta jest wstępnie wypełniany adresem URL tej funkcji.

   ![Podawanie wartości subskrypcji](./media/custom-event-quickstart-portal/provide-subscription-values.png)

1. Przed wyzwoleniem zdarzenia otwórz dzienniki funkcji w celu wyświetlenia danych zdarzenia podczas ich wysyłania. W dolnej części funkcji platformy Azure wybierz pozycję **Dzienniki**.

   ![Wybieranie dzienników](./media/custom-event-quickstart-portal/select-logs.png)

Teraz wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego. Aby uprościć ten artykuł, użyliśmy usługi Cloud Shell do wysłania przykładowych danych zdarzenia do tematu niestandardowego. Zwykle dane zdarzenia są wysyłane przez aplikację lub usługę platformy Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>Wysyłanie zdarzenia do tematu

Możesz wysłać zdarzenie testowe do niestandardowego tematu za pomocą interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

W pierwszym przykładzie użyto interfejsu wiersza polecenia platformy Azure. Pobierany jest adres URL i klucz dla tematu oraz przykładowe dane zdarzenia. Użyj nazwy tematu dla `<topic_name>`. Aby wyświetlić pełne zdarzenie, użyj polecenia `echo "$body"`. Element `data` danych JSON to ładunek zdarzenia. W tym polu można umieścić dowolną poprawnie sformułowaną zawartość JSON. Można też używać pola tematu do zaawansowanego routingu i filtrowania. CURL to narzędzie, które wysyła żądania HTTP.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Drugi przykład obejmuje wykonanie podobnych kroków przy użyciu programu PowerShell.

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic-name>

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

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Zdarzenie zostało wyzwolone, a usługa Event Grid wysłała komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Sprawdź dzienniki, aby wyświetlić dane zdarzenia.

![Wyświetlanie dzienników](./media/custom-event-quickstart-portal/view-log-entry.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz kontynuować pracę z tym zdarzeniem, nie usuwaj zasobów utworzonych w tym artykule. W przeciwnym razie usuń zasoby utworzone w ramach tego artykułu.

Wybierz grupę zasobów, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak tworzyć tematy niestandardowe i subskrypcje zdarzeń. Dowiedz się więcej na temat tego, co może Ci ułatwić usługa Event Grid:

- [Event Grid — informacje](overview.md)
- [Kierowanie zdarzeń usługi Blob Storage do niestandardowego internetowego punktu końcowego](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorowanie zmian maszyn wirtualnych za pomocą usług Azure Event Grid i Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md)
