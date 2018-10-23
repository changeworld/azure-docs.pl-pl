---
title: Przechwytywanie zdarzeń z przestrzeni usługi Azure Digital Twins | Microsoft Docs
description: Dowiedz się, w jaki sposób otrzymywać powiadomienia z przestrzeni po zintegrowaniu usługi Azure Digital Twins z usługą Logic Apps, korzystając z kroków opisanych w tym samouczku.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 91dd16938efbd1e24419352f66e3238646a77e8a
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323432"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-using-logic-apps"></a>Samouczek: otrzymywanie powiadomień z przestrzeni usługi Azure Digital Twins przy użyciu usługi Logic Apps

Po wdrożeniu wystąpienia usługi Azure Digital Twins, aprowizowaniu przestrzeni i wdrożeniu funkcji niestandardowej do monitorowania konkretnych warunków, możesz powiadomić administratora biura za pomocą wiadomości e-mail, gdy wystąpią monitorowane warunki. 

W [pierwszym samouczku](tutorial-facilities-setup.md) skonfigurowano wykres przestrzenny fikcyjnego budynku, w którym znajdowało się pomieszczenie z czujnikami ruchu, dwutlenku węgla oraz temperatury. W [drugim samouczku](tutorial-facilities-udf.md) aprowizowano wykres oraz funkcję niestandardową, nazywaną funkcją zdefiniowaną przez użytkownika, która monitoruje te wartości czujników i wyzwala powiadomienia, gdy pomieszczenie jest puste, a poziomy temperatury oraz dwutlenku węgla są w odpowiednich zakresach. W tym samouczku pokazano, jako zintegrować te powiadomienia z usługą Azure Logic Apps, tak aby wysyłać wiadomości e-mail, gdy takie pomieszczenie jest dostępne. Administrator biura może na podstawie tych informacji pomóc pracownikom zarezerwować pomieszczenie sprzyjające najwyższej produktywności. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Integrowanie zdarzeń z usługą Event Grid
> * Powiadamianie o zdarzeniach za pomocą aplikacji logiki
    
## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że [skonfigurowano](tutorial-facilities-setup.md) i [aprowizowano](tutorial-facilities-udf.md) konfigurację usługi Azure Digital Twins. Przed kontynuowaniem upewnij się, że masz:
- [Konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uruchomione wystąpienie usługi Digital Twins.
- Pobrane i wyodrębnione [przykłady usługi Digital Twins w języku C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) na maszynie roboczej.
- [Zestaw SKD .NET Core w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na komputerze deweloperskim w celu uruchomienia przykładu. Uruchom polecenie `dotnet --version`, aby sprawdzić, czy zainstalowano prawidłową wersję. 
- Konto usługi Office 365 do wysyłania powiadomień e-mail.

## <a name="integrate-events-with-event-grid"></a>Integrowanie zdarzeń z usługą Event Grid 
W tej sekcji skonfigurujesz usługę [Event Grid](../event-grid/overview.md), która będzie zbierała zdarzenia z wystąpienia usługi Digital Twins i przekierowywała je do [procedury obsługi zdarzeń](../event-grid/event-handlers.md), na przykład do usługi Logic Apps.

### <a name="create-event-grid-topic"></a>Tworzenie tematu usługi Event Grid
[Tematy usługi Event Grid](../event-grid/concepts.md#topics) udostępniają interfejs umożliwiający trasowanie zdarzeń wygenerowanych przez funkcję zdefiniowaną przez użytkownika. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Na panelu nawigacyjnym po lewej stronie kliknij pozycję **Utwórz zasób**. 

1. Wyszukaj i wybierz pozycję **Temat usługi Event Grid**. Kliknij pozycję **Utwórz**.

1. Wprowadź **nazwę** tematu usługi Event Grid i wybierz **subskrypcję**. Wybierz **grupę zasobów** używaną lub utworzoną dla wystąpienia usługi Digital Twins i **lokalizację**. Kliknij pozycję **Utwórz**. 

    ![Tworzenie tematu usługi Event Grid](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Przejdź do tematu usługi Event Grid z grupy zasobów, kliknij pozycję **Omówienie**, a następnie skopiuj wartość dla **punktu końcowego tematu** do pliku tymczasowego. Ten adres URL będzie potrzebny w kolejnej sekcji. 

1. Kliknij pozycję **Klucze dostępu** i skopiuj **Klucz 1** i **Klucz 2** do pliku tymczasowego. Te wartości będą potrzebne do utworzenia punktu końcowego w kolejnej sekcji.

    ![Klucze usługi Event Grid](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Tworzenie punktu końcowego dla tematu usługi Event Grid

1. W oknie polecenia upewnij się, że jesteś w folderze **_occupancy-quickstart\src_** przykładu usługi Digital Twins.

1. Otwórz plik **_actions\createEndpoints.yaml_** w edytorze programu Visual Studio Code. Upewnij się, że ma on następującą zawartość:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_Event_Grid
      secondaryConnectionString: Secondary_connection_string_for_your_Event_Grid
      path: Event_Grid_Topic_Path
    ```

1. Zastąp symbol zastępczy `Primary_connection_string_for_your_Event_Grid` wartością **Klucza 1**. 

1. Zastąp symbol zastępczy `Secondary_connection_string_for_your_Event_Grid` wartością **Klucza 2**.

1. Zastąp symbol zastępczy `Event_Grid_Topic_Path` ścieżką tematu usługi Event Grid. Aby uzyskać tę ścieżkę, usuń ciąg *https://* i końcowe ścieżki zasobów z adresu URL **punktu końcowego tematu**. Powinna mieć ona mniej więcej taki format:**nazwa_usługi_Event_Grid.lokalizacja.eventgrid.azure.net**. 

    > [!IMPORTANT]
    > Wprowadź wszystkie wartości bez żadnych cudzysłowów. Upewnij się, że po dwukropkach w pliku *YAML* znajduje się co najmniej jeden znak spacji. Możesz także sprawdzić poprawność zawartości pliku *YAML* online przy użyciu dowolnego modułu sprawdzania poprawności YAML, na przykład [tego narzędzia](https://onlineyamltools.com/validate-yaml).

1. Zapisz i zamknij plik. W oknie polecenia uruchom następujące polecenie i po pojawieniu się monitu zaloguj się. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   To polecenie tworzy punkt końcowy dla usługi Event Grid. 

   ![Punkty końcowe dla usługi Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)


## <a name="notify-events-with-logic-app"></a>Powiadamianie o zdarzeniach za pomocą aplikacji logiki
Usługa [Azure Logic Apps](../logic-apps/logic-apps-overview.md) umożliwia tworzenie zautomatyzowanych zadań dla zdarzeń odbieranych z innych usług. W tej sekcji skonfigurujesz usługę Logic Apps do tworzenia wiadomości e-mail z powiadomieniami dla zdarzeń trasowanych z czujników przestrzennych za pomocą [tematu usługi Event Grid](../event-grid/overview.md).

1. W okienku nawigacji po lewej stronie witryny [Azure Portal](https://portal.azure.com) kliknij przycisk **Utwórz zasób**.

1. Wyszukaj i wybierz nowy zasób **aplikacji logiki**. Kliknij pozycję **Utwórz**.

1. Wprowadź **nazwę** aplikacji logiki, a następnie wybierz **subskrypcję**, **grupę zasobów** oraz **lokalizację**. Kliknij pozycję **Utwórz**.

    ![Tworzenie aplikacji logiki](./media/tutorial-facilities-events/create-logic-app.png)

1. Otwórz wdrożoną aplikację logiki, a następnie otwórz okienko **Projektant aplikacji logiki**. 

1. Wybierz wyzwalacz **Gdy następuje zdarzenie usługi Event Grid**. Po wyświetleniu monitu **zaloguj się** w dzierżawie za pomocą konta platformy Azure. Gdy pojawi się monit, potwierdź ustawienie **Zezwól na dostęp** do usługi Event Grid. Kliknij pozycję **Kontynuuj**.

1. W oknie **Gdy następuje zdarzenie usługi Event Grid (wersja zapoznawcza)** 
    1. Wybierz **subskrypcję** użytą wcześniej do utworzenia usługi Event Grid.

    1. Wybierz **Typ zasobu** **Microsoft.EventGrid.Topics**.

    1. Wybierz zasób usługi Event Grid z listy rozwijanej w polu **Nazwa zasobu**.

    ![Tworzenie aplikacji logiki](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Kliknij przycisk **Nowy krok**.

1. W oknie **Wybierz akcję**
    1. wyszukaj frazę *przeanalizuj dane json* i wybierz akcję **Przeanalizuj dane JSON**.

    1. Kliknij pole **Zawartość** i wybierz pozycję **Treść** na liście **Zawartość dynamiczna**.

    1. Kliknij pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. Wklej następujący ładunek JSON, a następnie kliknij przycisk **Gotowe**.

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
    
    Ten ładunek zawiera fikcyjne wartości. Aplikacja logiki wykorzystuje ten ładunek przykładowy do wygenerowania **schematu**.
    
    ![Analizowanie danych JSON w aplikacji logiki dla usługi Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Kliknij przycisk **Nowy krok**.

1. W oknie **Wybierz akcję**
    1. wyszukaj i wybierz pozycję **Formant warunku** na liście **Akcje**. 

    1. Kliknij pierwsze pole tekstowe **Wybierz wartość** i wybierz pozycję **eventType** na liście **Zawartość dynamiczna** w oknie **Przeanalizuj dane JSON**.

    1. Kliknij drugie pole tekstowe **Wybierz wartość** i wpisz ciąg *UdfCustom*.

    ![Analizowanie danych JSON w aplikacji logiki dla usługi Event Grid](./media/tutorial-facilities-events/logic-app-condition.png)

1. W oknie**W przypadku wartości true**
    1. kliknij kolejno pozycje **Dodaj akcję** i  *Office 365 Outlook*.

    1. Na liście **Akcje** wybierz opcję **Wyślij wiadomość e-mail**. Kliknij przycisk **Zaloguj** i wprowadź poświadczenia konta e-mail. Po wyświetleniu monitu kliknij przycisk **Zezwól na dostęp**.

    1. W oknie **Do** wprowadź swój identyfikator poczty e-mail, aby otrzymywać powiadomienia. W polu **Temat** wprowadź tekst *Powiadomienie usługi Digital Twins dotyczące niskiej jakości powietrza*, a następnie wybierz pozycję **TopologyObjectId** na liście **Zawartość dynamiczna** pozycji **Przeanalizuj dane JSON**.

    1. W polu **Treść** w tym samym oknie wprowadź tekst podobny do następującego: *Wykryto niską jakość powietrza w pomieszczeniu. Należy dostosować temperaturę*. Możesz samodzielnie opracowywać treść, korzystając z elementów na liście **Zawartość dynamiczna**, jak pokazano poniżej.

    ![Wysyłanie wiadomości e-mail w aplikacji logiki](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Kliknij przycisk **Zapisz** u góry okienka **Projektant aplikacji logiki**.

1. Pamiętaj o symulowaniu danych z czujników — przejdź do folderu **_device-connectivity_** przykładu usługi Digital Twin w oknie polecenia i uruchom polecenie `dotnet run`.

W ciągu kilku minut powinny zacząć przychodzić powiadomienia w formie wiadomości e-mail z aplikacji logiki. 

   ![Wysyłanie wiadomości e-mail w aplikacji logiki](./media/tutorial-facilities-events/logic-app-notification.png)

Aby zrezygnować z otrzymywania tych wiadomości e-mail, przejdź do pozycji **Aplikacja logiki** w portalu i wybierz okienko **Omówienie**. Kliknij przycisk **Wyłącz**.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz kontynuować pracy z usługą Azure Digital Twins, możesz usunąć zasoby utworzone w tym samouczku:

1. W menu po lewej stronie w witrynie [Azure Portal](http://portal.azure.com) kliknij przycisk **Wszystkie zasoby**, wybierz grupę zasobów usługi Digital Twins i kliknij polecenie **Usuń**.
2. Jeśli będzie to konieczne, możesz usunąć także przykładowe aplikacje na komputerze służbowym. 


## <a name="next-steps"></a>Następne kroki

Możesz przejść do kolejnego samouczka, aby dowiedzieć się, w jaki sposób wizualizować dane z czujników, analizować trendy i wykrywać anomalie. 
> [!div class="nextstepaction"]
> [Samouczek: wizualizowanie i analizowanie zdarzeń z przestrzeni usługi Azure Digital Twins przy użyciu usługi Time Series Insights](tutorial-facilities-analyze.md)

Możesz także przejść dalej, aby dowiedzieć się więcej na temat wykresów analizy przestrzennej i modeli obiektów w usłudze Azure Digital Twins. 
> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph (Czym są modele obiektów i wykresy analizy przestrzennej w usłudze Digital Twins)](concepts-objectmodel-spatialgraph.md)