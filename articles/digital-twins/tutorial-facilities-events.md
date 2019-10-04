---
title: 'Samouczek: Przechwytywanie zdarzeń z cyfrowego obszaru bliźniaczych reprezentacji na platformie Azure | Microsoft Docs'
description: Dowiedz się, jak odbierać powiadomienia ze swoich miejsc, integrując usługę Azure Digital bliźniaczych reprezentacji z Logic Apps, wykonując czynności opisane w tym samouczku.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/23/2019
ms.openlocfilehash: 26976956722d77e2dfb8c17734c207b2667c0126
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949174"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Samouczek: otrzymywanie powiadomień z cyfrowych miejsc bliźniaczych reprezentacji na platformie Azure za pomocą Logic Apps

Po wdrożeniu wystąpienia usługi Azure Digital bliźniaczych reprezentacji należy zainicjować obsługę miejsc i zaimplementować funkcje niestandardowe do monitorowania określonych warunków, można powiadomić administratora biura za pośrednictwem poczty e-mail w przypadku wystąpienia monitorowanych warunków.

W [pierwszym samouczku](tutorial-facilities-setup.md)został skonfigurowany wykres przestrzenny budynku urojonego. Pomieszczenie w budynku zawiera czujniki dla ruchu, dwutlenku węgla i temperatury. W [drugim samouczku](tutorial-facilities-udf.md)zainicjowano tworzenie grafu i zdefiniowanej przez użytkownika funkcji monitorowania tych wartości czujnika i wyzwalania powiadomień, gdy pomieszczenie jest puste, a temperatura i ditlenek węgla są w wygodnym zakresie. 

W tym samouczku pokazano, jak można zintegrować te powiadomienia z Azure Logic Apps do wysyłania wiadomości e-mail, gdy jest dostępny taki pokój. Administrator pakietu Office może korzystać z tych informacji, aby ułatwić pracownikom książkę najbardziej wydajnego pokoju spotkań.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Integruj zdarzenia z Azure Event Grid.
> * Powiadamiaj zdarzenia za pomocą Logic Apps.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że [skonfigurowano](tutorial-facilities-setup.md) i [zainicjowano administracyjną](tutorial-facilities-udf.md) konfigurację usługi Azure Digital bliźniaczych reprezentacji. Przed kontynuowaniem upewnij się, że masz:

- [Konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Wystąpienie cyfrowego bliźniaczych reprezentacji uruchomione.
- [Próbki Digital bliźniaczych reprezentacji C# ](https://github.com/Azure-Samples/digital-twins-samples-csharp) pobrane i wyodrębnione na komputerze służbowym.
- Aby uruchomić przykład, [zestaw .NET Core SDK w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na komputerze deweloperskim. Uruchom `dotnet --version`, aby sprawdzić, czy odpowiednia wersja jest zainstalowana.
- Konto pakietu Office 365 do wysyłania wiadomości e-mail z powiadomieniami.

> [!TIP]
> Jeśli zainicjowano nowe wystąpienie, użyj unikatowej nazwy wystąpienia bliźniaczych reprezentacji Digital.

## <a name="integrate-events-with-event-grid"></a>Integrowanie zdarzeń z Event Grid

W tej sekcji skonfigurujesz [Event Grid](../event-grid/overview.md) zbierania zdarzeń z wystąpienia usługi Azure Digital bliźniaczych reprezentacji i przekierowania ich do [programu obsługi zdarzeń](../event-grid/event-handlers.md) , takiego jak Logic Apps.

### <a name="create-an-event-grid-topic"></a>Tworzenie tematu siatki zdarzeń

[Temat z siatką zdarzeń](../event-grid/concepts.md#topics) udostępnia interfejs do kierowania zdarzeń generowanych przez funkcję zdefiniowaną przez użytkownika. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. W lewym okienku wybierz pozycję **Utwórz zasób**. 

1. Wyszukaj i wybierz **temat Event Grid**. Wybierz pozycję **Utwórz**.

1. Wprowadź **nazwę** tematu usługi Event Grid i wybierz **subskrypcję**. Wybierz **grupę zasobów** , która została użyta lub utworzona dla wystąpienia bliźniaczych reprezentacji cyfrowego, oraz **Lokalizacja**. Wybierz pozycję **Utwórz**. 

    [@no__t — 1Create tematu siatki zdarzeń](./media/tutorial-facilities-events/create-event-grid-topic.png)](./media/tutorial-facilities-events/create-event-grid-topic.png#lightbox)

1. Przejdź do tematu usługi Event Grid z grupy zasobów, wybierz pozycję **Przegląd**i skopiuj wartość **punktu końcowego tematu** do pliku tymczasowego. Ten adres URL będzie potrzebny w następnej sekcji. 

1. Wybierz pozycję **klucze dostępu**i skopiuj **YOUR_KEY_1** i **YOUR_KEY_2** do pliku tymczasowego. Te wartości będą potrzebne do utworzenia punktu końcowego w następnej sekcji.

    [klucze siatki @no__t 1Event](./media/tutorial-facilities-events/event-grid-keys.png)](./media/tutorial-facilities-events/event-grid-keys.png#lightbox)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Tworzenie punktu końcowego dla tematu usługi Event Grid

1. W oknie wiersza polecenia upewnij się, że jesteś w folderze **Occupancy-quickstart\src** próbki Digital bliźniaczych reprezentacji.

1. Otwórz plik **actions\createEndpoints.YAML** w edytorze Visual Studio Code. Upewnij się, że ma ona następującą zawartość:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: <Primary connection string for your Event Grid>
      secondaryConnectionString: <Secondary connection string for your Event Grid>
      path: <Event Grid Topic Name without https:// and /api/events, e.g. eventgridname.region.eventgrid.azure.net>
    ```

1. Zastąp symbol zastępczy `<Primary connection string for your Event Grid>` wartością **YOUR_KEY_1**.

1. Zastąp symbol zastępczy `<Secondary connection string for your Event Grid>` wartością **YOUR_KEY_2**.

1. Zastąp symbol zastępczy **ścieżki ścieżką** i ścieżką do tematu usługi Event Grid. Pobierz tę ścieżkę, usuwając **https://** i końcowe ścieżki zasobów z adresu URL **punktu końcowego tematu** . Powinien wyglądać podobnie do tego formatu: *yourEventGridName.yourLocation.eventgrid.Azure.NET*.

    > [!IMPORTANT]
    > Wprowadź wszystkie wartości bez żadnych cudzysłowów. Upewnij się, że istnieje co najmniej jeden znak spacji po dwukropku w pliku YAML. Możesz również sprawdzić zawartość pliku YAML przy użyciu dowolnego modułu sprawdzania YAML online, takiego jak [to narzędzie](https://onlineyamltools.com/validate-yaml).

1. Zapisz i zamknij plik. W oknie polecenia Uruchom następujące polecenie i zaloguj się po wyświetleniu monitu. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   To polecenie tworzy punkt końcowy dla Event Grid. 

   [![Endpoints dla Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)](./media/tutorial-facilities-events/dotnet-create-endpoints.png#lightbox)

## <a name="notify-events-with-logic-apps"></a>Powiadamiaj zdarzenia za pomocą Logic Apps

Za pomocą usługi [Azure Logic Apps](../logic-apps/logic-apps-overview.md) można tworzyć zautomatyzowane zadania dla zdarzeń odebranych z innych usług. W tej sekcji skonfigurujesz Logic Apps, aby tworzyć powiadomienia e-mail dla zdarzeń kierowanych z czujników przestrzennych, z pomocą [tematu usługi Event Grid](../event-grid/overview.md).

1. W lewym okienku [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**.

1. Wyszukaj i wybierz nowy zasób **aplikacji logiki** . Wybierz pozycję **Utwórz**.

1. Wprowadź **nazwę** zasobu aplikacji logiki, a następnie wybierz swoją **subskrypcję**, **grupę zasobów**i **lokalizację**. Wybierz pozycję **Utwórz**.

    [@no__t — 1Create zasobu Logic Apps](./media/tutorial-facilities-events/create-logic-app.png)](./media/tutorial-facilities-events/create-logic-app.png#lightbox)

1. Otwórz zasób Logic Apps podczas jego wdrażania, a następnie otwórz okienko **Projektant aplikacji logiki** . 

1. Wybierz wyzwalacz **gdy wystąpi zdarzenie zasobów Event Grid** . Po wyświetleniu monitu zaloguj się do dzierżawy przy użyciu konta platformy Azure. Jeśli zostanie wyświetlony monit, wybierz opcję **Zezwalaj na dostęp** do zasobu Event Grid. Wybierz pozycję **Kontynuuj**.

1. W oknie **gdy wystąpi zdarzenie zasobu (wersja zapoznawcza)** : 
   
   a. Wybierz **subskrypcję** , która została użyta do utworzenia tematu usługi Event Grid.

   b. Wybierz **Microsoft. EventGrid. temats** dla **typu zasobu**.

   s. Wybierz zasób Event Grid z pola rozwijanego **nazwa zasobu**.

   [@no__t — okienko projektanta aplikacji 1Logic](./media/tutorial-facilities-events/logic-app-resource-event.png)](./media/tutorial-facilities-events/logic-app-resource-event.png#lightbox)

1. Wybierz przycisk **nowy krok** .

1. W oknie **Wybierz akcję** :

   a. Wyszukaj frazę **JSON analizowanie składni**i wybierz akcję **Przeanalizuj dane JSON** .

   b. W polu **zawartość** wybierz pozycję **treść** z listy **zawartość dynamiczna** .

   s. Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. Wklej następujący ładunek JSON, a następnie wybierz pozycję **gotowe**.

    ```JSON
    {
    "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
      "ResourceType": "Space",
      "Payload": "\"Air quality is poor.\"",
      "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
    },
    "eventType": "UdfCustom",
    "eventTime": "0001-01-01T00:00:00Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
    }
    ```

    Ten ładunek ma fikcyjne wartości. Logic Apps używa tego przykładowego ładunku do wygenerowania *schematu*.

    [@no__t — Przeanalizuj okno JSON aplikacji 1Logic dla Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)](./media/tutorial-facilities-events/logic-app-parse-json.png#lightbox)

1. Wybierz przycisk **nowy krok** .

1. W oknie **Wybierz akcję** :

   a. Wybierz pozycję **formant > warunek** lub **warunek** wyszukiwania na liście **Akcje** . 

   b. W polu tekstowym **Wybierz wartość** wybierz pozycję **Typ zdarzenia** z listy **zawartości dynamicznej** dla okna **Analiza JSON** .

   s. W drugim polu tekstowym **Wybierz wartość** wprowadź `UdfCustom`.

   [@no__t — warunki 1Selected](./media/tutorial-facilities-events/logic-app-condition.png)](./media/tutorial-facilities-events/logic-app-condition.png#lightbox)

1. W oknie **Jeśli prawda** :

   a. Wybierz pozycję **Dodaj akcję**, a następnie wybierz pozycję **Office 365 Outlook**.

   b. Z listy **Akcje** wybierz pozycję **Wyślij wiadomość e-mail**. Wybierz pozycję **Zaloguj** i Użyj poświadczeń konta e-mail. Jeśli zostanie wyświetlony monit, wybierz opcję **Zezwalaj na dostęp** .

   s. W polu **do** wprowadź swój identyfikator poczty e-mail, aby otrzymywać powiadomienia. W polu **temat**wprowadź tekst **Digital bliźniaczych reprezentacji Notification, aby uzyskać niską jakość powietrza w miejscu**. Następnie wybierz pozycję **TopologyObjectId** z listy **zawartości dynamicznej** , aby **przeanalizować dane JSON**.

   Wykres. W obszarze **treść** w tym samym oknie wprowadź tekst podobny do tego: **niska jakość powietrza wykryte w pokoju i należy dostosować temperaturę**. Możesz korzystać z elementów z listy **zawartości dynamicznej** .

   [@no__t — 1Logic aplikacje "Wyślij wiadomość e-mail"](./media/tutorial-facilities-events/logic-app-send-email.png)](./media/tutorial-facilities-events/logic-app-send-email.png#lightbox)

1. Wybierz przycisk **Zapisz** znajdujący się u góry okienka **projektanta aplikacji logiki** .

1. Pamiętaj, aby symulować dane czujnika, przechodząc do folderu **połączenia urządzenia** w próbce Digital bliźniaczych reprezentacji w oknie wiersza polecenia i uruchamiając `dotnet run`.

W ciągu kilku minut należy zacząć otrzymywać powiadomienia e-mail z tego zasobu Logic Apps. 

   [powiadomienie ![Email](./media/tutorial-facilities-events/logic-app-notification.png)](./media/tutorial-facilities-events/logic-app-notification.png#lightbox)

Aby przestać otrzymywać te wiadomości e-mail, przejdź do zasobu Logic Apps w portalu i wybierz okienko **Przegląd** . Wybierz pozycję **Wyłącz**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz zatrzymać Eksplorowanie usługi Azure Digital bliźniaczych reprezentacji w tym momencie, możesz usunąć zasoby utworzone w tym samouczku:

1. W menu po lewej stronie w [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**, wybierz grupę zasobów Digital bliźniaczych reprezentacji, a następnie wybierz pozycję **Usuń**.

    > [!TIP]
    > Jeśli wystąpił problem z usunięciem wystąpienia bliźniaczych reprezentacji Digital, aktualizacja usługi została wdrożona przy użyciu poprawki. Spróbuj usunąć wystąpienie.

2. W razie potrzeby usuń przykładowe aplikacje na komputerze służbowym.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wizualizować dane czujnika, analizować trendy i wykrywać anomalie, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Samouczek: wizualizacja i analiza zdarzeń z miejsc bliźniaczych reprezentacji cyfrowych platformy Azure przy użyciu Time Series Insights](tutorial-facilities-analyze.md)

Możesz również dowiedzieć się więcej na temat wykresów analizy przestrzennej i modeli obiektów w usłudze Azure Digital bliźniaczych reprezentacji:

> [!div class="nextstepaction"]
> [Zrozumienie modeli obiektów Digital bliźniaczych reprezentacji i grafu analizy przestrzennej](concepts-objectmodel-spatialgraph.md)
