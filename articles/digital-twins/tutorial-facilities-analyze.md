---
title: 'Samouczek: analizowanie zdarzeń z usługi Azure Digital bliźniaczych reprezentacji Setup | Microsoft Docs'
description: Dowiedz się, jak wizualizować i analizować zdarzenia z cyfrowych miejsc bliźniaczych reprezentacji na platformie Azure za pomocą Azure Time Series Insights, wykonując kroki opisane w tym samouczku.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/23/2019
ms.openlocfilehash: af776372026bf9affcda03fa9188b854ebedfc73
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949800"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-by-using-time-series-insights"></a>Samouczek: wizualizacja i analiza zdarzeń z cyfrowych miejsc bliźniaczych reprezentacji na platformie Azure przy użyciu Time Series Insights

Po wdrożeniu wystąpienia usługi Azure Digital bliźniaczych reprezentacji należy zainicjować obsługę miejsc i zaimplementować funkcję niestandardową w celu monitorowania określonych warunków, można wizualizować zdarzenia i dane pochodzące ze swoich miejsc, aby szukać trendów i anomalii.

W [pierwszym samouczku](tutorial-facilities-setup.md)został skonfigurowany wykres przestrzenny budynku urojonego, z pomieszczeniem zawierającym czujniki ruchu, ditlenku węgla i temperatury. W [drugim samouczku](tutorial-facilities-udf.md)zainicjowano tworzenie grafu i funkcji zdefiniowanej przez użytkownika. Funkcja monitoruje te wartości czujnika i wyzwala powiadomienia dla właściwych warunków. Oznacza to, że pomieszczenie jest puste, a poziomy poziomów ditlenku węgla są normalne.

W tym samouczku pokazano, jak zintegrować powiadomienia i dane pochodzące z konfiguracji Digital bliźniaczych reprezentacji systemu Azure za pomocą Azure Time Series Insights. Następnie można wizualizować wartości czujnika w czasie. Można wyszukać trendy, takie jak pomieszczenie, które jest najbardziej używane, a które najgorętszym razy dziennie. Możesz również wykrywać anomalie, takie jak pokoje, stuffier i hotter, lub czy obszar w budynku wysyła ciągle wysokie wartości temperatury, wskazując uszkodzenie stanu powietrza.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Przesyłanie strumieniowe danych przy użyciu usługi Azure Event Hubs.
> * Analizuj przy użyciu Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że [skonfigurowano](tutorial-facilities-setup.md) i [zainicjowano administracyjną](tutorial-facilities-udf.md) konfigurację usługi Azure Digital bliźniaczych reprezentacji. Przed kontynuowaniem upewnij się, że masz:

- [Konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Wystąpienie cyfrowego bliźniaczych reprezentacji uruchomione.
- [Próbki Digital bliźniaczych reprezentacji C# ](https://github.com/Azure-Samples/digital-twins-samples-csharp) pobrane i wyodrębnione na komputerze służbowym.
- Aby uruchomić przykład, [zestaw .NET Core SDK w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na komputerze deweloperskim. Uruchom `dotnet --version`, aby sprawdzić, czy odpowiednia wersja jest zainstalowana.

> [!TIP]
> Jeśli zainicjowano nowe wystąpienie, użyj unikatowej nazwy wystąpienia bliźniaczych reprezentacji Digital.

## <a name="stream-data-by-using-event-hubs"></a>Przesyłanie strumieniowe danych przy użyciu Event Hubs

Za pomocą usługi [Event Hubs](../event-hubs/event-hubs-about.md) można utworzyć potok do przesyłania strumieniowego danych. W tej sekcji pokazano, jak utworzyć centrum zdarzeń jako łącznik między wystąpieniami bliźniaczych reprezentacji cyfrowych i Time Series Insights platformy Azure.

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. W lewym okienku wybierz pozycję **Utwórz zasób**.

1. Wyszukaj i wybierz **Event Hubs**. Wybierz pozycję **Utwórz**.

1. Wprowadź **nazwę** dla przestrzeni nazw Event Hubs. Wybierz pozycję **standardowa** dla **warstwy cenowej**, **subskrypcji**, **grupy zasobów** , która została użyta dla wystąpienia bliźniaczych reprezentacji cyfrowych, i **lokalizacji**. Wybierz pozycję **Utwórz**.

1. W obszarze wdrażanie przestrzeni nazw Event Hubs Wybierz okienko **Przegląd** , a następnie wybierz pozycję **Przejdź do zasobu**.

    [Przestrzeń nazw centrów @no__t 1Event po wdrożeniu](./media/tutorial-facilities-analyze/open-event-hub-ns.png)](./media/tutorial-facilities-analyze/open-event-hub-ns.png#lightbox)

1. W okienku **Przegląd** przestrzeni nazw Event Hubs wybierz przycisk **centrum zdarzeń** u góry.
    [@no__t — przycisk centrum 1Event](./media/tutorial-facilities-analyze/create-event-hub.png)](./media/tutorial-facilities-analyze/create-event-hub.png#lightbox)

1. Wprowadź **nazwę** centrum zdarzeń, a następnie wybierz pozycję **Utwórz**.

   Po wdrożeniu centrum zdarzeń pojawia się ono w okienku **Event Hubs** przestrzeni nazw Event Hubs z **aktywnym** stanem. Wybierz to centrum zdarzeń, aby otworzyć jego okienko **przeglądu** .

1. Wybierz przycisk **Grupa odbiorców** u góry, a następnie wprowadź nazwę, taką jak **tsievents** dla grupy odbiorców. Wybierz pozycję **Utwórz**.

    [@no__t — Grupa konsumentów centrum 1Event](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   Po utworzeniu grupy odbiorców zostanie ona wyświetlona na liście w dolnej części okienka **Przegląd** centrum zdarzeń.

1. Otwórz okienko **zasady dostępu współdzielonego** dla centrum zdarzeń, a następnie wybierz przycisk **Dodaj** . Wprowadź **ManageSend** jako nazwę zasad, upewnij się, że wszystkie pola wyboru są zaznaczone, a następnie wybierz pozycję **Utwórz**.

    [@no__t — parametry połączenia centrum 1Event](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

1. Otwórz utworzone zasady ManageSend i skopiuj wartości **parametrów połączenia — klucz podstawowy** i **Parametry połączenia — klucz pomocniczy** do pliku tymczasowego. Te wartości będą potrzebne do utworzenia punktu końcowego dla centrum zdarzeń w następnej sekcji.

### <a name="create-an-endpoint-for-the-event-hub"></a>Tworzenie punktu końcowego centrum zdarzeń

1. W oknie wiersza polecenia upewnij się, że jesteś w folderze **Occupancy-quickstart\src** przykładu Digital bliźniaczych reprezentacji.

1. Otwórz plik **actions\createEndpoints.YAML** w edytorze. Zastąp zawartość następującym:

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

1. Zastąp symbole zastępcze `Primary_connection_string_for_your_event_hub` wartością **parametrów połączenia — kluczem podstawowym** dla centrum zdarzeń. Upewnij się, że format tych parametrów połączenia jest następujący:

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Zastąp symbole zastępcze `Secondary_connection_string_for_your_event_hub` wartością **parametrów połączenia — klucz pomocniczy** centrum zdarzeń. Upewnij się, że format tych parametrów połączenia jest następujący: 

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Zastąp symbole zastępcze `Name_of_your_Event_Hub` nazwą centrum zdarzeń.

    > [!IMPORTANT]
    > Wprowadź wszystkie wartości bez żadnych cudzysłowów. Upewnij się, że istnieje co najmniej jeden znak spacji po dwukropku w pliku YAML. Możesz również sprawdzić zawartość pliku YAML przy użyciu dowolnego modułu sprawdzania YAML online, takiego jak [to narzędzie](https://onlineyamltools.com/validate-yaml).

1. Zapisz i zamknij plik. Uruchom następujące polecenie w oknie wiersza polecenia i zaloguj się przy użyciu konta platformy Azure po wyświetleniu monitu.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Tworzy dwa punkty końcowe dla centrum zdarzeń.

   [![Endpoints dla Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Analizowanie za pomocą Time Series Insights

1. W lewym okienku [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**. 

1. Wyszukaj i wybierz zasób **Time Series Insights** ogólnej dostępności (ga). Wybierz pozycję **Utwórz**.

1. Wprowadź **nazwę** wystąpienia Time Series Insights, a następnie wybierz swoją **subskrypcję**. Wybierz **grupę zasobów** , która została użyta dla swojego wystąpienia bliźniaczych reprezentacji Digital, i **lokalizację**. Wybierz pozycję **Dalej: przycisk Źródło zdarzenia** lub kartę **Źródło zdarzenia** .

    [![Selections do tworzenia wystąpienia Time Series Insights](./media/tutorial-facilities-analyze/create-tsi.png)](./media/tutorial-facilities-analyze/create-tsi.png#lightbox)

1. Na karcie **Źródło zdarzenia** wprowadź **nazwę**, wybierz pozycję **centrum zdarzeń** jako **Typ źródła**i upewnij się, że pozostałe wartości są wybrane prawidłowo. W polu **Nazwa zasad dostępu do centrum zdarzeń**wybierz pozycję **ManageSend** , a następnie wybierz grupę odbiorców utworzoną w poprzedniej sekcji dla **grupy użytkowników centrum zdarzeń**. Wybierz pozycję **Recenzja + Utwórz**.

    [![Selections do tworzenia źródła zdarzeń](./media/tutorial-facilities-analyze/tsi-event-source.png)](./media/tutorial-facilities-analyze/tsi-event-source.png#lightbox)

1. W okienku **Recenzja + tworzenie** Przejrzyj wprowadzone informacje i wybierz pozycję **Utwórz**.

1. W okienku wdrożenie wybierz właśnie utworzony zasób Time Series Insights. Zostanie otwarte okienko **Przegląd** środowiska Time Series Insightsowego.

1. Wybierz przycisk **Przejdź do środowiska** w górnej części strony. Jeśli zostanie wyświetlone ostrzeżenie dotyczące dostępu do danych, Otwórz okienko **zasady dostępu do danych** dla wystąpienia Time Series Insights, wybierz pozycję **Dodaj**, wybierz opcję **współautor** jako rolę i wybierz odpowiedniego użytkownika.

1. Przycisk **Przejdź do środowiska** otwiera [Eksploratora Time Series Insights](../time-series-insights/time-series-insights-explorer.md). Jeśli nie są wyświetlane żadne zdarzenia, Symuluj zdarzenia urządzenia, przechodząc do projektu **połączenia urządzenia** w próbce Digital bliźniaczych reprezentacji i uruchamiając `dotnet run`.

1. Po wygenerowaniu kilku zdarzeń symulowanych Wróć do Eksploratora Time Series Insights i wybierz przycisk Odśwież u góry. Powinny być widoczne wykresy analityczne, które są tworzone dla symulowanych danych czujników. 

    [![Chart w Eksploratorze Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer.png)](./media/tutorial-facilities-analyze/tsi-explorer.png#lightbox)

1. W Eksploratorze Time Series Insights można generować wykresy i map cieplnych dla różnych zdarzeń i danych z pokojów, czujników i innych zasobów. Po lewej stronie Użyj pól **miary** i **Podziel według** , aby utworzyć własne wizualizacje. 

   Na przykład wybierz pozycję **zdarzenia** dla **miary** i **DIGITALTWINS-SensorHardwareId** dla **dzielenia przez**, aby wygenerować mapę cieplną dla każdej z czujników. Mapę cieplną będzie wyglądać podobnie do poniższej ilustracji:

   [![Heatmap w Eksploratorze Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz zrezygnować z eksplorowania usługi Azure Digital bliźniaczych reprezentacji poza tym punktem, możesz usunąć zasoby utworzone w ramach tego samouczka:

1. W menu po lewej stronie w [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**, wybierz grupę zasobów Digital bliźniaczych reprezentacji, a następnie wybierz pozycję **Usuń**.

    > [!TIP]
    > Jeśli wystąpił problem z usunięciem wystąpienia bliźniaczych reprezentacji Digital, aktualizacja usługi została wdrożona przy użyciu poprawki. Spróbuj usunąć wystąpienie.

2. W razie potrzeby usuń przykładowe aplikacje na komputerze służbowym.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat wykresów i modeli obiektów analizy przestrzennej w usłudze Azure Digital bliźniaczych reprezentacji.

> [!div class="nextstepaction"]
> [Zrozumienie modeli obiektów Digital bliźniaczych reprezentacji i grafu analizy przestrzennej](concepts-objectmodel-spatialgraph.md)
