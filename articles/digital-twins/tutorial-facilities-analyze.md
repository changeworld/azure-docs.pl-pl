---
title: Analizowanie zdarzeń z konfiguracji usługi Azure Digital Twins | Microsoft Docs
description: Dowiedz się, w jaki sposób wizualizować i analizować zdarzenia z przestrzeni usługi Azure Digital Twins przy użyciu usługi Azure Time Series Insights, korzystając z kroków opisanych w tym samouczku.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1366cafe5d2c526e86905c108b9c7b865aac8f69
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323464"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-using-time-series-insights"></a>Samouczek: wizualizowanie i analizowanie zdarzeń z przestrzeni usługi Azure Digital Twins przy użyciu usługi Time Series Insights

Po wdrożeniu wystąpienia usługi Azure Digital Twins, aprowizowaniu przestrzeni i wdrożeniu funkcji niestandardowej do monitorowania konkretnych warunków, możesz wizualizować zdarzenia i dane przychodzące z tych przestrzeni, aby wyszukiwać trendy i anomalie. 

W [pierwszym samouczku](tutorial-facilities-setup.md) skonfigurowano wykres przestrzenny fikcyjnego budynku, w którym znajdowało się pomieszczenie z czujnikami ruchu, dwutlenku węgla oraz temperatury. W [drugim samouczku](tutorial-facilities-udf.md) aprowizowano wykres oraz funkcję zdefiniowaną przez użytkownika. Funkcja monitoruje wartości z tych czujników i wyzwala powiadomienia, kiedy warunki są prawidłowe, czyli kiedy pomieszczenie jest puste, a temperatura i zawartość dwutlenku węgla są na normalnym poziomie. W tym samouczku pokazano, w jaki sposób zintegrować powiadomienia i dane przychodzące z konfiguracji usługi Digital Twins za pomocą usługi Azure Time Series Insights. Można następnie wizualizować wartości czujników w czasie i wyszukiwać trendy, dotyczące na przykład tego, które pomieszczenie jest najczęściej używane lub jakie są pory najwyższej aktywności. Możesz również wykrywać anomalie, na przykład to, który pokój wydaje się bardziej duszny i w którym jest wyższa temperatura lub czy z jakiegoś obszaru w budynku wysyłane są stale wysokie wartości temperatury, co wskazuje na wadliwe działanie klimatyzacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przesyłanie strumieniowe danych za pomocą usługi Event Hubs
> * Analizowanie za pomocą usługi Time Series Insights

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że [skonfigurowano](tutorial-facilities-setup.md) i [aprowizowano](tutorial-facilities-udf.md) konfigurację usługi Azure Digital Twins. Przed kontynuowaniem upewnij się, że masz:
- [Konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uruchomione wystąpienie usługi Digital Twins.
- Pobrane i wyodrębnione [przykłady usługi Digital Twins w języku C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) na maszynie roboczej.
- [Zestaw SKD .NET Core w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na komputerze deweloperskim w celu uruchomienia przykładu. Uruchom polecenie `dotnet --version`, aby sprawdzić, czy zainstalowano prawidłową wersję. 


## <a name="stream-data-using-event-hubs"></a>Przesyłanie strumieniowe danych za pomocą usługi Event Hubs
W usłudze [Event Hubs](../event-hubs/event-hubs-about.md) możesz utworzyć potok, aby przesyłać strumieniowo dane. W tej sekcji pokazano, w jaki sposób utworzyć centrum zdarzeń, które będzie służyło jako łącznik pomiędzy wystąpieniami usług Digital Twins i TSI.

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

1. Zaloguj się w [portalu Azure](https://portal.azure.com).

1. Na panelu nawigacyjnym po lewej stronie kliknij pozycję **Utwórz zasób**. 

1. Wyszukaj i wybierz pozycję **Event Hubs**. Kliknij pozycję **Utwórz**.

1. Wprowadź **nazwę** dla przestrzeni nazw usługi Event Hubs, wybierz **warstwę cenową** *Standardowa*, **subskrypcję**, **grupę zasobów** użytą dla wystąpienia usługi Azure Digital Twins oraz **lokalizację**. Kliknij pozycję **Utwórz**. 

1. Po wdrożeniu przejdź do *wdrożenia* przestrzeni nazw usługi Event Hubs i kliknij przestrzeń nazw w obszarze **ZASÓB**.

    ![Przestrzeń nazw centrum zdarzeń](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. W okienku **Omówienie** przestrzeni nazw usługi Event Hubs kliknij przycisk **Centrum zdarzeń** znajdujący się u góry strony. 
    ![Centrum zdarzeń](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Wpisz **nazwę** centrum zdarzeń, a następnie kliknij polecenie **Utwórz**. Po wdrożeniu pojawi się ono w okienku **Event Hubs** przestrzeni nazw usługi Event Hubs, a jego **STAN** będzie *Aktywny*. Kliknij to centrum zdarzeń, aby otworzyć okienko **Omówienie**.

1. Kliknij przycisk **Grupa konsumentów** znajdujący się u góry strony, a następnie wprowadź nazwę grupy konsumentów, na przykład *tsievents*. Kliknij pozycję **Utwórz**.
    ![Grupa konsumentów centrum zdarzeń](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   Po utworzeniu grupa konsumentów zostanie wyświetlona na liście u dołu okienka **Omówienie** centrum zdarzeń. 

1. Otwórz okienko **Zasady dostępu współużytkowanego** dla centrum zdarzeń i kliknij przycisk **Dodaj**. **Utwórz** zasadę o nazwie *ManageSend* i sprawdź, czy zaznaczono wszystkie pola wyboru. 

    ![Parametry połączenia centrum zdarzeń](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Otwórz utworzoną zasadę *ManageSend* i skopiuj wartości **Parametry połączenia — klucz podstawowy** i **Parametry połączenia — klucz pomocniczy** do pliku tymczasowego. Te wartości będą potrzebne do utworzenia punktu końcowego centrum zdarzeń w kolejnej sekcji.

### <a name="create-endpoint-for-the-event-hub"></a>Tworzenie punktu końcowego dla centrum zdarzeń

1. W oknie polecenia upewnij się, że jesteś w folderze **_occupancy-quickstart\src** przykładu usługi Digital Twins.

1. Otwórz plik **_actions\createEndpoints.yaml_** w edytorze. Zastąp zawartość pliku następującym kodem:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    ```

1. Zastąp symbole zastępcze `Primary_connection_string_for_your_event_hub` wartością **Parametry połączenia — klucz podstawowy** dla centrum zdarzeń. Upewnij się, że format parametrów połączenia jest następujący:
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
```

1. Zastąp symbole zastępcze `Secondary_connection_string_for_your_event_hub` wartością **Parametry połączenia — klucz pomocniczy** dla centrum zdarzeń. Upewnij się, że format parametrów połączenia jest następujący: 
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
```

1. Zastąp symbole zastępcze `Name_of_your_Event_Hubs_namespace` nazwą przestrzeni nazw usługi Event Hubs.

    > [!IMPORTANT]
    > Wprowadź wszystkie wartości bez żadnych cudzysłowów. Upewnij się, że po dwukropkach w pliku *YAML* znajduje się co najmniej jeden znak spacji. Możesz także sprawdzić poprawność zawartości pliku *YAML* online przy użyciu dowolnego modułu sprawdzania poprawności YAML, na przykład [tego narzędzia](https://onlineyamltools.com/validate-yaml).


1. Zapisz i zamknij plik. Uruchom następujące polecenie w oknie polecenia i po pojawieniu się monitu zaloguj się na koncie platformy Azure.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   Utworzone zostaną dwa punkty końcowe centrum zdarzeń.

   ![Punkty końcowe usługi Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Analizowanie za pomocą usługi Time Series Insights

1. W okienku nawigacji po lewej stronie witryny [Azure Portal](https://portal.azure.com) kliknij przycisk **Utwórz zasób**. 

1. Wyszukaj i wybierz nowy zasób usługi **Time Series Insights**. Kliknij pozycję **Utwórz**.

1. Wprowadź **nazwę** wystąpienia usługi Time Series Insights, a następnie wybierz swoją **subskrypcję**. Wybierz **grupę zasobów** używaną dla wystąpienia usługi Digital Twins i **lokalizację**. Kliknij pozycję **Utwórz**.

    ![Tworzenie usługi TSI](./media/tutorial-facilities-analyze/create-tsi.png)

1. Po wdrożeniu otwórz środowisko usługi Time Series Insights, a następnie otwórz okienko **Źródła zdarzeń**. Kliknij przycisk **Dodaj** znajdujący się u góry, aby dodać grupę konsumentów.

1. W okienku **Nowe źródło zdarzeń** wprowadź **nazwę** i upewnij się, że inne wartości są zaznaczone prawidłowo. Wybierz pozycję *ManageSend* jako **nazwę zasad centrum zdarzeń**, a następnie wybierz *grupę konsumentów* utworzoną w poprzedniej sekcji jako **grupę konsumentów centrum zdarzeń**. Kliknij pozycję **Utwórz**.

    ![Źródło zdarzenia usługi TSI](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Otwórz okienko **Omówienie** środowiska usługi Time Series Insights, a następnie kliknij przycisk **Przejdź do środowiska** znajdujący się u góry strony. Jeśli otrzymasz *ostrzeżenie dotyczące dostępu do danych*, otwórz okienko **Zasady dostępu do danych** dla swojego wystąpienia usługi TSI, kliknij polecenie **Dodaj**, wybierz rolę **Współautor**, a następnie wybierz odpowiedniego użytkownika.

1. Wybranie przycisku **Przejdź do środowiska** powoduje otwarcie [eksploratora usługi Time Series Insights](../time-series-insights/time-series-insights-explorer.md). Jeśli nie zostaną wyświetlone żadne zdarzenia, zasymuluj zdarzenia urządzenia, przechodząc do projektu **_device-connectivity_** przykładu usługi Digital Twins i uruchamiając polecenie `dotnet run`.

1. Po wygenerowaniu kilku symulowanych zdarzeń wróć do eksploratora usługi Time Series Insights i kliknij przycisk Odśwież u góry strony. Powinny być widoczne wykresy analityczne tworzone dla symulowanych danych czujnika. 

    ![Eksplorator usługi TSI](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. W eksploratorze usługi Time Series można wygenerować wykresy oraz mapy cieplne dla różnych zdarzeń i danych pobranych z pomieszczeń, czujników i innych zasobów. Po lewej stronie kliknij listy rozwijane **MIARA** oraz **PODZIAŁ WEDŁUG**, aby utworzyć własne wizualizacje. Możesz na przykład wybrać pozycję *Zdarzenia* w obszarze **MIARA** oraz pozycję *DigitalTwins-SensorHardwareId* w obszarze **PODZIAŁ WEDŁUG**, aby wygenerować mapę cieplną dla każdego czujnika, podobnie jak na następującej ilustracji:

    ![Eksplorator usługi TSI](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz kontynuować pracy z usługą Azure Digital Twins, możesz usunąć zasoby utworzone w tym samouczku:

1. W menu po lewej stronie w witrynie [Azure Portal](http://portal.azure.com) kliknij przycisk **Wszystkie zasoby**, wybierz grupę zasobów usługi Digital Twins i kliknij polecenie **Usuń**.
2. Jeśli będzie to konieczne, możesz usunąć także przykładowe aplikacje na komputerze służbowym. 


## <a name="next-steps"></a>Następne kroki

Przejdź do kolejnego artykułu, aby dowiedzieć się więcej na temat wykresów analizy przestrzennej i modeli obiektów w usłudze Azure Digital Twins. 
> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph (Czym są modele obiektów i wykresy analizy przestrzennej w usłudze Digital Twins)](concepts-objectmodel-spatialgraph.md)

