---
title: 'Samouczek: Analizowanie zdarzeń w usłudze Time Series Insights — Azure Digital Twins| Dokumenty firmy Microsoft'
description: Dowiedz się, w jaki sposób wizualizować i analizować zdarzenia z przestrzeni usługi Azure Digital Twins przy użyciu usługi Azure Time Series Insights, korzystając z kroków opisanych w tym samouczku.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 38bd1755ed87050cf8b91a0a82f6e5f1d2af9db5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75933862"
---
# <a name="tutorial-visualize-and-analyze-events-from-azure-digital-twins-by-using-time-series-insights"></a>Samouczek: wizualizuj i analizuj zdarzenia z usług Azure Digital Twins przy użyciu usługi Time Series Insights

Po wdrożeniu wystąpienia usługi Azure Digital Twins, aprowizowaniu przestrzeni i wdrożeniu funkcji niestandardowej do monitorowania konkretnych warunków, możesz wizualizować zdarzenia i dane przychodzące z tych przestrzeni, aby wyszukiwać trendy i anomalie.

W [pierwszym samouczku](tutorial-facilities-setup.md) skonfigurowano wykres przestrzenny fikcyjnego budynku, w którym znajdowało się pomieszczenie z czujnikami ruchu, dwutlenku węgla oraz temperatury. W [drugim samouczku](tutorial-facilities-udf.md) aprowizowano wykres oraz funkcję zdefiniowaną przez użytkownika. Funkcja monitoruje wartości tego czujnika i wyzwala powiadomienia w przypadku odpowiednich warunków. To znaczy w przypadku, gdy pomieszczenie jest puste, a poziomy temperatury i dwutlenku węgla są normalne.

W tym samouczku pokazano, w jaki sposób zintegrować powiadomienia i dane przychodzące z konfiguracji usługi Azure Digital Twins za pomocą usługi Azure Time Series Insights. Można wówczas wizualizować wartości z czujników na przestrzeni czasu. Możesz wyszukiwać trendy, na przykład aby dowiedzieć się, które pomieszczenie jest najczęściej używane i w jakich porach dnia. Możesz również wykrywać anomalie, na przykład to, który pokój wydaje się bardziej duszny i w którym jest wyższa temperatura lub czy z jakiegoś obszaru w budynku wysyłane są stale wysokie wartości temperatury, co wskazuje na wadliwe działanie klimatyzacji.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przesyłanie strumieniowe danych za pomocą usługi Azure Event Hubs.
> * Analizowanie za pomocą usługi Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że [skonfigurowano](tutorial-facilities-setup.md) i [aprowizowano](tutorial-facilities-udf.md) konfigurację usługi Azure Digital Twins. Przed kontynuowaniem upewnij się, że masz:

- [Konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uruchomione wystąpienie usługi Digital Twins.
- Pobrane i wyodrębnione [przykłady usługi Digital Twins w języku C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) na maszynie roboczej.
- [Zestaw SDK .NET Core w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na komputerze deweloperskim w celu uruchomienia przykładu. Uruchom polecenie `dotnet --version`, aby sprawdzić, czy zainstalowano prawidłową wersję.

> [!TIP]
> Użyj unikatowej nazwy wystąpienia digital twins, jeśli inicjujesz inicjowanie obsługi administracyjnej nowego wystąpienia.

## <a name="stream-data-by-using-event-hubs"></a>Przesyłanie strumieniowe danych za pomocą usługi Event Hubs

Korzystając z usługi [Event Hubs](../event-hubs/event-hubs-about.md), możesz utworzyć potok, aby przesyłać strumieniowo dane. W tej sekcji pokazano, w jaki sposób utworzyć centrum zdarzeń, które będzie służyło jako łącznik pomiędzy wystąpieniami usług Azure Digital Twins i Time Series Insights.

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**.

1. Wyszukaj i wybierz pozycję **Event Hubs**. Wybierz **pozycję Utwórz**.

    [![Tworzenie obszaru nazw centrów zdarzeń](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png#lightbox)

1. Wprowadź **nazwę** dla przestrzeni nazw usługi Event Hubs. Wybierz **warstwę cenową****Standardowa**, **subskrypcję**, **grupę zasobów** użytą dla wystąpienia usługi Azure Digital Twins oraz **lokalizację**. Wybierz **pozycję Utwórz**.

1. We wdrożeniu obszaru nazw Centrum zdarzeń wybierz **okienko Przegląd,** a następnie wybierz pozycję **Przejdź do zasobu**.

    [![Przestrzeń nazw usługi Event Hubs po wdrożeniu](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png)](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png#lightbox)

1. W okienku **Omówienie** przestrzeni nazw usługi Event Hubs wybierz przycisk **Centrum zdarzeń** znajdujący się u góry strony.
    [![Przycisk Centrum zdarzeń](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png#lightbox)

1. Wprowadź **nazwę** dla centrum zdarzeń, a następnie wybierz pozycję **Utwórz**.

   Po wdrożeniu centrum zdarzeń jest ono wyświetlane w okienku **Event Hubs** przestrzeni nazw usługi Event Hubs, a jego stan jest **Aktywny**. Wybierz to centrum zdarzeń, aby otworzyć okienko **Omówienie**.

1. Wybierz przycisk **Grupa konsumentów** znajdujący się u góry strony, a następnie wprowadź nazwę grupy konsumentów, na przykład **tsievents**. Wybierz **pozycję Utwórz**.

    [![Grupa konsumentów centrum zdarzeń](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   Po utworzeniu grupa konsumentów jest wyświetlana na liście u dołu okienka **Omówienie** centrum zdarzeń.

1. Otwórz okienko **Zasady dostępu współużytkowanego** dla centrum zdarzeń i wybierz przycisk **Dodaj**. Jako nazwę zasad wprowadź **ManageSend**, upewnij się, wszystkie pola wyboru są zaznaczone, a następnie wybierz pozycję **Utwórz**.

    [![Parametry połączenia centrum zdarzeń](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

    > [!TIP]
    > Sprawdź, czy tworzysz zasady sygnatury dostępu Współdzielonego dla wystąpienia centrum zdarzeń, a nie obszaru nazw.

1. Otwórz utworzone zasady **ManageSend** i skopiuj wartości **dla ciągu połączenia — klucza podstawowego** i ciągu połączenia — **klucza pomocniczego** do pliku tymczasowego. Te wartości będą potrzebne do utworzenia punktu końcowego centrum zdarzeń w kolejnej sekcji.

### <a name="create-an-endpoint-for-the-event-hub"></a>Tworzenie punktu końcowego dla centrum zdarzeń

1. W oknie polecenia upewnij się, że jesteś w folderze **occupancy-quickstart\src** przykładu usługi Azure Digital Twins.

1. Otwórz plik **actions\createEndpoints.yaml** w edytorze. Zastąp zawartość pliku następującym kodem:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. Zastąp `Primary_connection_string_for_your_event_hub` symbole zastępcze wartością **ciągu Połączenia — klucza podstawowego** dla centrum zdarzeń. Upewnij się, że format parametrów połączenia jest następujący:

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Zastąp `Secondary_connection_string_for_your_event_hub` symbole zastępcze wartością **ciągu Połączenia — klucza pomocniczego** dla centrum zdarzeń. Upewnij się, że format parametrów połączenia jest następujący: 

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Zastąp symbole zastępcze `Name_of_your_Event_Hub` nazwą centrum zdarzeń.

    > [!IMPORTANT]
    > Wprowadź wszystkie wartości bez żadnych cudzysłowów. Upewnij się, że po dwukropkach w pliku YAML znajduje się co najmniej jeden znak spacji. Zawartość pliku YAML można również sprawdzić poprawnie, korzystając z dowolnego internetowego modułu sprawdzania poprawności YAML, takiego jak [to narzędzie.](https://onlineyamltools.com/validate-yaml)

1. Zapisz i zamknij plik. Uruchom następujące polecenie w oknie polecenia i po pojawieniu się monitu zaloguj się na koncie platformy Azure.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Utworzone zostaną dwa punkty końcowe centrum zdarzeń.

   [![Punkty końcowe usługi Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Analizowanie za pomocą usługi Time Series Insights

1. W okienku po lewej stronie witryny [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób**. 

1. Wyszukaj i wybierz zasób ogólnej dostępności (GA) **usługi Time Series Insights.** Wybierz **pozycję Utwórz**.

1. Wprowadź **nazwę** wystąpienia usługi Time Series Insights, a następnie wybierz swoją **subskrypcję**. Wybierz **grupę zasobów** używaną dla wystąpienia usługi Digital Twins i **lokalizację**. Wybierz **przycisk Dalej:** Źródło zdarzenia lub kartę **Źródło zdarzenia.**

    [![Opcje tworzenia wystąpienia usługi Time Series Insights](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png)](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png#lightbox)

1. Na karcie **Źródło zdarzeń** wprowadź **pozycję Nazwa**, wybierz Centrum **zdarzeń** jako **typ źródła**i upewnij się, że inne wartości są poprawnie wybrane, aby odwoływać się do utworzonego centrum zdarzeń. Wybierz **pozycję ManageSend** for **Event Hub access policy name**, a następnie wybierz grupę odbiorców utworzoną w poprzedniej sekcji dla grupy odbiorców Centrum **zdarzeń**. Wybierz pozycję **Przegląd + utwórz**.

    [![Opcje tworzenia źródła zdarzeń](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png)](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png#lightbox)

1. W okienku **Recenzja + Utwórz** przejrzyj wprowadzone informacje i wybierz pozycję **Utwórz**.

1. W okienku wdrażania wybierz utworzony zasób Usługi Time Series Insights. Otwiera **okienko Przegląd** środowiska usługi Time Series Insights.

1. Wybierz przycisk **Przejdź do środowiska** u góry. Jeśli otrzymasz ostrzeżenie dotyczące dostępu do danych, otwórz okienko **Zasady dostępu do danych** dla swojego wystąpienia usługi Time Series Insights, wybierz pozycję **Dodaj**, wybierz rolę **Współautor**, a następnie wybierz odpowiedniego użytkownika.

1. Wybranie przycisku **Przejdź do środowiska** powoduje otwarcie [eksploratora usługi Time Series Insights](../time-series-insights/time-series-insights-explorer.md). Jeśli nie zostaną wyświetlone żadne zdarzenia, zasymuluj zdarzenia urządzenia, przechodząc do projektu **device-connectivity** przykładu usługi Digital Twins i uruchamiając polecenie `dotnet run`.

1. Po wygenerowaniu kilku symulowanych zdarzeń wróć do eksploratora usługi Time Series Insights i wybierz przycisk Odśwież u góry strony. Pojawią się wykresy analityczne tworzone dla symulowanych danych z czujników. 

    [![Wykres w eksploratorze usługi Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png)](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png#lightbox)

1. W eksploratorze usługi Time Series Insights można wygenerować wykresy oraz mapy cieplne dla różnych zdarzeń i danych pobranych z pomieszczeń, czujników i innych zasobów. Po lewej stronie użyj list rozwijanych **MIARA** oraz **PODZIAŁ WEDŁUG**, aby utworzyć własne wizualizacje. 

   Możesz na przykład wybrać pozycję **Zdarzenia** w obszarze **MIARA** oraz pozycję **DigitalTwins-SensorHardwareId** w obszarze **PODZIAŁ WEDŁUG**, aby wygenerować mapę cieplną dla każdego czujnika. Mapa cieplna będzie wyglądać podobnie jak na poniższej ilustracji:

   [![Mapa cieplna w eksploratorze usługi Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz kontynuować pracy z usługą Azure Digital Twins, możesz usunąć zasoby utworzone w tym samouczku:

1. W menu po lewej stronie w witrynie [Azure Portal](https://portal.azure.com) wybierz przycisk **Wszystkie zasoby**, wybierz grupę zasobów usługi Digital Twins, a następnie wybierz polecenie **Usuń**.

    > [!TIP]
    > Jeśli podczas usuwania wystąpienia usługi Digital Twins wystąpił problem, została wdrożona aktualizacja usługi zawierająca poprawkę. Ponów próbę usunięcia wystąpienia.

2. Jeśli będzie to konieczne, możesz usunąć przykładowe aplikacje na komputerze służbowym.

## <a name="next-steps"></a>Następne kroki

Przejdź do kolejnego artykułu, aby dowiedzieć się więcej na temat wykresów analizy przestrzennej i modeli obiektów w usłudze Azure Digital Twins.

> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph (Czym są modele obiektów i wykresy analizy przestrzennej w usłudze Digital Twins)](concepts-objectmodel-spatialgraph.md)
