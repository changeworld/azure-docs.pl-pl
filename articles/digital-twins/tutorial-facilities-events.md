---
title: 'Samouczek: Przechwytywanie zdarzeń z przestrzeni usługi Azure Digital Twins | Microsoft Docs'
description: Dowiedz się, w jaki sposób otrzymywać powiadomienia z przestrzeni po zintegrowaniu usługi Azure Digital Twins z usługą Logic Apps, korzystając z kroków opisanych w tym samouczku.
services: digital-twins
author: alinamstanciu
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/18/2018
ms.author: alinast
ms.openlocfilehash: 2b84fa2fd8053ca4dc7ef0ad246d29b2bba3dae5
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484711"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Samouczek: otrzymywanie powiadomień z przestrzeni usługi Azure Digital Twins przy użyciu usługi Logic Apps

Po wdrożeniu wystąpienia usługi Azure Digital Twins, aprowizowaniu przestrzeni i wdrożeniu funkcji niestandardowej do monitorowania konkretnych warunków możesz powiadomić administratora biura za pomocą wiadomości e-mail, gdy wystąpią monitorowane warunki.

W [pierwszym samouczku](tutorial-facilities-setup.md) skonfigurowano graf przestrzenny fikcyjnego budynku. Znajdowało się w nim pomieszczenie z czujnikami ruchu, dwutlenku węgla oraz temperatury. W [drugim samouczku](tutorial-facilities-udf.md) aprowizowano graf oraz funkcję zdefiniowaną przez użytkownika, która monitoruje te wartości czujników i wyzwala powiadomienia, gdy pomieszczenie jest puste, a poziomy temperatury oraz dwutlenku węgla są w odpowiednich zakresach. 

W tym samouczku pokazano, jako zintegrować te powiadomienia z usługą Azure Logic Apps, tak aby wysyłać wiadomości e-mail, gdy takie pomieszczenie jest dostępne. Administrator biura może na podstawie tych informacji pomóc pracownikom zarezerwować pomieszczenie sprzyjające najwyższej produktywności.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Integrowanie zdarzeń z usługą Azure Event Grid.
> * Powiadamianie o zdarzeniach za pomocą usługi Logic Apps.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że [skonfigurowano](tutorial-facilities-setup.md) i [aprowizowano](tutorial-facilities-udf.md) konfigurację usługi Azure Digital Twins. Przed kontynuowaniem upewnij się, że masz:

- [Konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uruchomione wystąpienie usługi Digital Twins.
- Pobrane i wyodrębnione [przykłady usługi Digital Twins w języku C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) na maszynie roboczej.
- [Zestaw SDK .NET Core w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na komputerze deweloperskim w celu uruchomienia przykładu. Uruchom polecenie `dotnet --version`, aby sprawdzić, czy zainstalowano prawidłową wersję.
- Konto usługi Office 365 do wysyłania powiadomień e-mail.

## <a name="integrate-events-with-event-grid"></a>Integrowanie zdarzeń z usługą Event Grid

W tej sekcji skonfigurujesz usługę [Event Grid](../event-grid/overview.md), która będzie zbierała zdarzenia z wystąpienia usługi Azure Digital Twins i przekierowywała je do [procedury obsługi zdarzeń](../event-grid/event-handlers.md), na przykład do usługi Logic Apps.

### <a name="create-an-event-grid-topic"></a>Tworzenie tematu usługi Event Grid

[Tematy usługi Event Grid](../event-grid/concepts.md#topics) udostępniają interfejs umożliwiający trasowanie zdarzeń wygenerowanych przez funkcję zdefiniowaną przez użytkownika. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**. 

1. Wyszukaj i wybierz pozycję **Temat usługi Event Grid**. Wybierz pozycję **Utwórz**.

1. Wprowadź **nazwę** tematu usługi Event Grid i wybierz **subskrypcję**. Wybierz **grupę zasobów** używaną lub utworzoną dla wystąpienia usługi Digital Twins i **lokalizację**. Wybierz pozycję **Utwórz**. 

    ![Tworzenie tematu usługi Event Grid](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Przejdź do tematu usługi Event Grid z grupy zasobów, wybierz pozycję **Omówienie**, a następnie skopiuj wartość dla **punktu końcowego tematu** do pliku tymczasowego. Ten adres URL będzie potrzebny w następnej sekcji. 

1. Wybierz pozycję **Klucze dostępu** i skopiuj **TWÓJ_KLUCZ_1** i **TWÓJ_KLUCZ_2** do pliku tymczasowego. Te wartości będą potrzebne do utworzenia punktu końcowego w kolejnej sekcji.

    ![Klucze usługi Event Grid](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Tworzenie punktu końcowego dla tematu usługi Event Grid

1. W oknie polecenia upewnij się, że jesteś w folderze **occupancy-quickstart\src** przykładu usługi Digital Twins.

1. Otwórz plik **actions\createEndpoints.yaml** w edytorze programu Visual Studio Code. Upewnij się, że ma on następującą zawartość:

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

1. Zastąp symbol zastępczy `<Primary connection string for your Event Grid>` wartością **TWÓJ_KLUCZ_1**.

1. Zastąp symbol zastępczy `<Secondary connection string for your Event Grid>` wartością **TWÓJ_KLUCZ_2**.

1. Zastąp symbol zastępczy dla **ścieżki** ze ścieżką tematu usługi event grid. Aby uzyskać tę ścieżkę, usuń ciąg **https://** i końcowe ścieżki zasobów z adresu URL **punktu końcowego tematu**. Powinna mieć ona mniej więcej taki format:*nazwa_usługi_Event_Grid.lokalizacja.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Wprowadź wszystkie wartości bez żadnych cudzysłowów. Upewnij się, że po dwukropkach w pliku YAML znajduje się co najmniej jeden znak spacji. Możesz także sprawdzić poprawność zawartości pliku YAML online przy użyciu dowolnego modułu sprawdzania poprawności YAML, na przykład [tego narzędzia](https://onlineyamltools.com/validate-yaml).

1. Zapisz i zamknij plik. W oknie polecenia uruchom następujące polecenie i po pojawieniu się monitu zaloguj się. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   To polecenie tworzy punkt końcowy dla usługi Event Grid. 

   ![Punkty końcowe dla usługi Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)

## <a name="notify-events-with-logic-apps"></a>Powiadamianie o zdarzeniach za pomocą usługi Logic Apps

Usługa [Azure Logic Apps](../logic-apps/logic-apps-overview.md) umożliwia tworzenie zautomatyzowanych zadań dla zdarzeń odbieranych z innych usług. W tej sekcji skonfigurujesz usługę Logic Apps do tworzenia wiadomości e-mail z powiadomieniami dla zdarzeń trasowanych z czujników przestrzennych za pomocą [tematu usługi Event Grid](../event-grid/overview.md).

1. W okienku po lewej stronie witryny [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób**.

1. Wyszukaj i wybierz nowy zasób **aplikacji logiki**. Wybierz pozycję **Utwórz**.

1. Wprowadź **nazwę** zasobu aplikacji logiki, a następnie wybierz **subskrypcję**, **grupę zasobów** oraz **lokalizację**. Wybierz pozycję **Utwórz**.

    ![Tworzenie zasobu usługi Logic Apps](./media/tutorial-facilities-events/create-logic-app.png)

1. Otwórz zasób usługi Logic Apps, a następnie otwórz okienko **Projektant aplikacji logiki**. 

1. Wybierz **występuje, gdy zdarzenie usługi Event Grid zasobów** wyzwalacza. Po wyświetleniu monitu zaloguj się w dzierżawie za pomocą konta platformy Azure. Wybierz **zezwolić na dostęp** dla zasobu usługi Event Grid, jeśli zostanie wyświetlony monit. Wybierz **nadal**.

1. W oknie **Gdy następuje zdarzenie usługi Event Grid (wersja zapoznawcza)** : 
   
   a. Wybierz **subskrypcję** użytą wcześniej do utworzenia tematu usługi Event Grid.

   b. Wybierz **Typ zasobu** **Microsoft.EventGrid.Topics**.

   c. Wybierz zasób usługi Event Grid z listy rozwijanej w polu **Nazwa zasobu**.

   ![Okienko Projektant aplikacji logiki](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Wybierz przycisk **Nowy krok**.

1. W oknie **Wybierz akcję**:

   a. wyszukaj frazę **przeanalizuj dane json** i wybierz akcję **Przeanalizuj dane JSON**.

   b. W polu **Zawartość** wybierz pozycję **Treść** na liście **Zawartość dynamiczna**.

   c. Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. Wklej następujący ładunek JSON, a następnie wybierz przycisk **Gotowe**.

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

    Ten ładunek zawiera fikcyjne wartości. Usługa Logic Apps wykorzystuje ten ładunek przykładowy do wygenerowania *schematu*.

    ![Okno Przeanalizuj dane JSON usługi Logic Apps dla usługi Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Wybierz przycisk **Nowy krok**.

1. W oknie **Wybierz akcję**:

   a. Wybierz pozycję **Kontrolka > Warunek** lub wyszukaj pozycję **Warunek** na liście **Akcje**. 

   b. W pierwszym polu tekstowym **Wybierz wartość** wybierz pozycję **eventType** na liście **Zawartość dynamiczna** w oknie **Przeanalizuj dane JSON**.

   c. W drugim polu tekstowym **Wybierz wartość** wprowadź ciąg `UdfCustom`.

   ![Wybrane warunki](./media/tutorial-facilities-events/logic-app-condition.png)

1. W oknie**W przypadku wartości true**:

   a. Wybierz kolejno pozycje **Dodaj akcję** i  **Office 365 Outlook**.

   b. Na liście **Akcje** wybierz pozycję **Wyślij wiadomość e-mail**. Wybierz pozycję **Zaloguj** i wprowadź poświadczenia konta e-mail. Wybierz **zezwolić na dostęp** Jeśli zostanie wyświetlony monit.

   c. W oknie **Do** wprowadź swój identyfikator poczty e-mail, aby otrzymywać powiadomienia. W polu **Temat** wprowadź tekst **Powiadomienie usługi Digital Twins dotyczące niskiej jakości powietrza**. Następnie wybierz pozycję **TopologyObjectId** na liście **Zawartość dynamiczna** pozycji **Przeanalizuj dane JSON**.

   d. W obszarze **Treść** w tym samym oknie wprowadź tekst podobny do następującego: **W pomieszczeniu wykryto, że powietrze jest niskiej jakości, a temperatura wymaga regulacji**. Możesz samodzielnie opracowywać treść, korzystając z elementów na liście **Zawartość dynamiczna**.

   ![Opcje „Wyślij wiadomość e-mail” usługi Logic Apps](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Wybierz przycisk **Zapisz** u góry okienka **Projektant aplikacji logiki**.

1. Pamiętaj o symulowaniu danych z czujników — przejdź do folderu **device-connectivity** przykładu usługi Digital Twins w oknie polecenia i uruchom polecenie `dotnet run`.

W ciągu kilku minut powinny zacząć przychodzić powiadomienia w formie wiadomości e-mail z zasobu usługi Logic Apps. 

   ![Powiadomienie e-mail](./media/tutorial-facilities-events/logic-app-notification.png)

Aby zrezygnować z otrzymywania tych wiadomości e-mail, przejdź do zasobu usługi Logic Apps w portalu i wybierz okienko **Przegląd**. Wybierz **wyłączyć**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz kontynuować pracy z usługą Azure Digital Twins, możesz usunąć zasoby utworzone w tym samouczku:

1. W menu po lewej stronie w witrynie [Azure Portal](https://portal.azure.com) wybierz przycisk **Wszystkie zasoby**, wybierz grupę zasobów usługi Digital Twins i wybierz polecenie **Usuń**.

    > [!TIP]
    > Jeśli podczas usuwania wystąpienia usługi Digital Twins wystąpił problem, została wdrożona aktualizacja usługi zawierająca poprawkę. Ponów próbę usunięcia wystąpienia.

2. Jeśli będzie to konieczne, możesz usunąć przykładowe aplikacje na komputerze służbowym.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, w jaki sposób wizualizować dane z czujników, analizować trendy i wykrywać anomalie, przejdź do kolejnego samouczka:

> [!div class="nextstepaction"]
> [Samouczek: wizualizowanie i analizowanie zdarzeń z przestrzeni usługi Azure Digital Twins przy użyciu usługi Time Series Insights](tutorial-facilities-analyze.md)

Możesz także dowiedzieć się więcej na temat wykresów analizy przestrzennej i modeli obiektów w usłudze Azure Digital Twins:

> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph (Czym są modele obiektów i wykresy analizy przestrzennej w usłudze Digital Twins)](concepts-objectmodel-spatialgraph.md)
