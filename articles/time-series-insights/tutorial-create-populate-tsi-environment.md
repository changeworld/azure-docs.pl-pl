---
title: 'Samouczek: Tworzenie środowiska usługi Azure Time Series Insights | Microsoft Docs'
description: Dowiedz się, jak utworzyć środowisko usługi Time Series Insights wypełniane przy użyciu danych z symulowanych urządzeń.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 3c097ec74f45248a41c6dd4df80cbbd927a9b5ed
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827439"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Samouczek: Tworzenie środowiska usługi Azure Time Series Insights

Ten samouczek przeprowadzi Cię przez proces tworzenia środowiska Time Series Insights, wypełniony danymi z symulowanych urządzeń. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie środowiska usługi Time Series Insights 
> * Tworzenie rozwiązania symulacji urządzeń zawierającego usługę IoT Hub
> * Łączenie środowiska usługi Time Series Insights do usługi IoT hub
> * Uruchamianie symulacji urządzenia przesyłanie strumieniowe danych do środowiska usługi Time Series Insights
> * Sprawdzanie symulowanych danych telemetrycznych

## <a name="video"></a>Wideo

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Dowiedz się, jak używać akceleratora rozwiązań Azure IoT do generowania danych i wprowadzenie do usługi Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Usługa Azure konto logowania musi również należeć do tej subskrypcji **właściciela** roli. Zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](/azure/role-based-access-control/role-assignments-portal) Aby uzyskać więcej informacji.

## <a name="overview"></a>Omówienie

Środowisko usługi Time Series Insights jest gdzie zbierania i przechowywania danych urządzenia. Po umieszczeniu w środowisku usługi Time Series Insights [Eksplorator usługi Time Series Insights](time-series-quickstart.md) i [czas serii Insights — interfejs API zapytań](/rest/api/time-series-insights/ga-query-api) może służyć do tworzenia zapytań i analizowania danych. Usługa IoT Hub jest punkt połączenia używany przez wszystkie urządzenia (symulowanych lub fizycznych) do nawiązania bezpiecznego połączenia i transmisji danych do chmury platformy Azure. [Omówienie Insights serii czasu](time-series-insights-overview.md) zauważa, że usługi Azure IoT Hub stanowi również źródłem zdarzenia dla danych przesyłanych strumieniowo do środowiska usługi Time Series Insights. W tym samouczku [akcelerator rozwiązań IoT](/azure/iot-accelerators/) Generowanie i przesyłanie strumieniowe przykładowe dane telemetrii do usługi IoT Hub.

>[!TIP]
> Akceleratory rozwiązań IoT udostępniają korporacyjnej wstępnie skonfigurowanych rozwiązań, które umożliwiają przyspieszenie rozwoju niestandardowych rozwiązań IoT.

## <a name="create-an-environment"></a>Tworzenie środowiska

Najpierw należy utworzyć środowisko usługi Time Series Insights w Twojej subskrypcji platformy Azure:

1. Zaloguj się do witryny [Azure portal](https://portal.azure.com) przy użyciu konta platformy Azure.  
1. W menu u góry po lewej stronie wybierz pozycję **+ Utwórz zasób**.  
1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz **Time Series Insights**.  

   [![Wybierz opcję Tworzenie środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Na stronie **Środowisko Time Series Insights** wypełnij wymagane parametry:

   Parametr|Opis
   ---|---
   **Nazwa środowiska** | Wybierz unikatową nazwę dla środowiska usługi Time Series Insights. Nazwa użytkownika używana przez Eksplorator usługi Time Series Insights oraz interfejsów API zapytań.
   **Subskrypcja** | Subskrypcje są kontenerami zasobów platformy Azure. Wybierz subskrypcję, aby utworzyć środowisko usługi Time Series Insights.
   **Grupa zasobów** | Grupa zasobów jest kontenerem zasobów platformy Azure. Wybierz istniejącą grupę zasobów lub Utwórz nową grupę dla zasobu środowiska usługi Time Series Insights.
   **Lokalizacja** | Wybierz region centrum danych dla danego środowiska usługi Time Series Insights. Aby uniknąć kosztów dodano przepustowości i opóźnienia, najlepiej jest zapewnienie środowiska usługi Time Series Insights, w tym samym regionie, co inne zasoby IoT.
   **Cennik jednostki SKU** | Wybierz potrzebną przepustowość. Generuje najniższy koszt i wydajności modułu uruchamiającego, wybierz pozycję `S1`.
   **Dyspozycyjność** | Dyspozycyjność jest mnożnikiem stosowanym do prędkości wejściowej, pojemności oraz kosztów związanych z wybraną jednostką SKU.  Po utworzeniu można zmienić pojemność. Na najniższym koszcie wybierz o pojemności 1.

   Po zakończeniu kliknij pozycję **Utwórz**, aby rozpocząć proces aprowizowania.

   [![Tworzenie zasobu środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Sprawdź **powiadomienia** panelu, aby monitorować ukończenie wdrożenia.  

   [![Wdrażanie środowiska Series Insights czasu zakończyło się pomyślnie](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

Następnie należy utworzyć rozwiązania symulacji urządzenia, które spowoduje wygenerowanie danych testowych, aby wypełnić środowiska usługi Time Series Insights:

1. W oddzielnym oknie lub kartę, przejdź do [azureiotsolutions.com](https://www.azureiotsolutions.com). Zaloguj się przy użyciu tego samego konta subskrypcji platformy Azure, a następnie wybierz pozycję **symulacji urządzenia** akceleratora.

   [![Uruchom akceleratora symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Wprowadź wymagane parametry na **rozwiązania tworzenia symulacji urządzenia** strony.

   Parametr|Opis
   ---|---
   **Nazwa rozwiązania** | Unikatowa wartość służąca do tworzenia nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów.
   **Subskrypcja** | Określ tę samą subskrypcję, używany do tworzenia środowiska usługi Time Series Insights w poprzedniej sekcji.
   **Region** | Określ tego samego regionu, który służy do tworzenia środowiska usługi Time Series Insights w poprzedniej sekcji.
   **Wdróż opcjonalne zasoby platformy Azure** | Pozostaw zaznaczone pole wyboru **IoT Hub**, ponieważ symulowane urządzenia zostaną użyte do połączenia/strumieniowania danych.

   Po zakończeniu kliknij przycisk **tworzenie rozwiązania** do aprowizowania zasobów platformy Azure z tego rozwiązania. Może potrwać 6 – 7 minut, aby ukończyć ten proces.

   [![Aprowizacja rozwiązania symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Po zakończeniu aprowizacji tekst powyżej nowe rozwiązanie ulegnie zmianie z **aprowizacji...**  do **gotowe**:

   >[!IMPORTANT]
   > Nie klikaj jeszcze przycisku **Uruchom**! Nie zamykaj też tej strony sieci WWW, gdyż powrócisz do niej później.

   [![Inicjowanie obsługi administracyjnej pełną rozwiązania symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Teraz wróć do portalu Azure i sprawdź nowo utworzone zasoby w ramach swojej subskrypcji. W portalu na stronie **Grupy zasobów** można zauważyć nową grupę zasobów utworzoną przy użyciu parametru **Nazwa rozwiązania** w ostatnim kroku. Ponadto będą tam wszystkie zasoby utworzone w celu obsługi rozwiązania symulacji urządzeń:

   [![Zasoby rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-environment-to-the-iot-hub"></a>Łączenie środowiska w Centrum IoT Hub

Wiesz już, jak utworzyć dwa zestawy zasobów, każdy we własnej grupie zasobów:

- Puste środowisko usługi Time Series Insights.
- Urządzenie symulacji rozwiązania zasobów, w tym Centrum IoT hub, generowane przez akcelerator rozwiązań.

Przypominamy, że symulowane urządzenia muszą nawiązać połączenia z usługą IoT Hub w celu strumieniowego przesyłania danych urządzenia. Przepływ danych do środowiska usługi Time Series Insights, należy wprowadzić zmiany w konfiguracji do usługi IoT hub i środowiska usługi Time Series Insights.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Konfiguracja usługi IoT Hub: definiowanie grupy konsumentów

Usługa IoT Hub zawiera różne punkty końcowe, służące do udostępniania funkcji innym aktorom. Punkt końcowy „Zdarzenia” umożliwia innym aplikacjom używanie danych, ponieważ są one przesyłane strumieniowo do wystąpienia usługi IoT Hub. W szczególności „grupy użytkowników” udostępniają aplikacjom mechanizm do nasłuchiwania i pobierania danych z usługi IoT Hub.

Następnie należy zdefiniować nową **grupy odbiorców** właściwości na rozwiązania symulacji urządzenia IoT hub **punkcie końcowym zdarzenia**.

1. W portalu Azure przejdź do strony **Omówienie** należącej do grupy zasobów utworzonej dla rozwiązania symulacji urządzeń, a następnie wybierz zasób IoT Hub:

   [![Grupa zasobów rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Zwróć również uwagę na parametr **Nazwa** zasobu IoT Hub wygenerowanego dla rozwiązania, gdyż będziesz odwoływać się do niego później.

1. Przewiń w dół i wybierz stronę **Punkty końcowe**, a następnie wybierz punkt końcowy **Zdarzenia**. Na stronie punktu końcowego **Właściwości** wprowadź unikatową nazwę punktu końcowego w obszarze grupy użytkowników „$Default”, a następnie kliknij przycisk **Zapisz**:

   [![Punkty końcowe usługi IoT Hub rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="environment-configuration-define-an-event-source"></a>Konfiguracji środowiska: określania źródła zdarzeń

Teraz z nowego centrum IoT hub **grupy odbiorców** punktu końcowego zdarzeń do środowiska usługi Time Series Insights jako **źródła zdarzeń**.

1. Przejdź do **Przegląd** stronie grupy zasobów, możesz utworzyć dla środowiska usługi Time Series Insights, a następnie wybierz środowisko usługi Time Series Insights:

   [![Grupy zasobów w środowisku Series Insights czasu i środowiska](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. Na stronie środowiska usługi Time Series Insights wybierz **źródła zdarzeń**, następnie kliknij przycisk **+ Dodaj**.

   [![Omówienie środowiska Series Insights czasu](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Wprowadź wymagane parametry na **nowe źródło zdarzeń** strony.

   Parametr|Opis
   ---|---
   **Nazwa źródła zdarzeń** | Wymaga unikatowej wartości, która jest używana jako nazwa źródła zdarzeń.
   **Element źródłowy** | Wybierz **usługi IoT Hub**.
   **Opcja importu** | Wybierz domyślną `Use IoT hub from available subscriptions`. Wybór tej opcji spowoduje, że następna lista rozwijana zostanie wypełniona dostępnymi subskrypcjami.
   **Subskrypcja** | Wybierz tę samą subskrypcję, w którym utworzono środowiska usługi Time Series Insights i zasobów symulacji urządzenia.
   **Nazwa IoT Hub** | Domyślnie powinna to być zanotowana wcześniej nazwa usługi IoT Hub. W przeciwnym przypadku wybierz prawidłową usługę IoT Hub.
   **Nazwa zasady IoT Hub** | Wybierz pozycję **iothubowner**.
   **Grupa użytkowników IoT Hub** | Powinna być taka sama jak nazwa grupy użytkowników IoT Hub utworzonej wcześniej. Jeśli tak nie jest, wybierz prawidłową nazwę grupy użytkowników.
   **Format serializacji zdarzeń** | Pozostaw wartości domyślne `JSON`.
   **Nazwa właściwości znacznika czasu** | Określ jako `timestamp`.

   Po zakończeniu kliknij przycisk **Utwórz**, aby dodać źródło zdarzeń. Po powrocie do grupy zasobów **Przegląd** strony, wraz z zasobu środowiska usługi Time Series Insights, zostanie wyświetlony nowy zasób "Źródła zdarzeń usługi Time Series Insights".

   [![Czas Series Insights środowiska nowe źródło zdarzeń](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Uruchamianie symulacji urządzenia do transmisji danych

Teraz, gdy wszystkie zadania konfiguracji jest pełna, jest czasu na wypełnienie środowiska usługi Time Series Insights z przykładowymi danymi z symulowanych urządzeń.

Przypominamy, że w [sekcji Tworzenie symulacji urządzeń](#create-a-device-simulation) akcelerator utworzył kilka zasobów Azure w celu obsługi naszego rozwiązania. Oprócz omawianej już usługi IoT Hub utworzono również aplikację sieci WWW Azure App Service, która służy do tworzenia i przesyłania danych telemetrycznych symulowanych urządzeń.

1. Wróć do swojego [Pulpitu nawigacyjnego akceleratorów rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard). W razie potrzeby zaloguj się ponownie, używając tego samego konta platformy Azure, które było używane w tym samouczku. Teraz możesz kliknąć pozycję **Uruchom** przycisku w ramach rozwiązania "Symulacji urządzenia".

     [![Pulpit nawigacyjny akceleratorów rozwiązania](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. W tym momencie zostanie uruchomiona aplikacja sieci Web symulacji urządzeń, której ładowanie początkowe może potrwać kilka sekund. Zostanie również wyświetlony monit o zgodę na przyznanie aplikacji sieci Web uprawnienia „Zaloguj się i odczytaj profil”. To uprawnienie umożliwia aplikacji, które można pobrać informacji o profilu użytkownika, które są niezbędne do obsługi działanie aplikacji.

     [![Zgody aplikacji sieci web symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Raz **Instalatora symulacji** ładowania strony, wprowadź wymagane parametry.

   Parametr|Opis
   ---|---
   **Docelowa usługa IoT Hub** | Wybierz **Użyj wstępnie przygotowany usługi IoT Hub**.
   **Model urządzenia** | Wybierz pozycję **Chiller** (Chłodnia).
   **Liczba urządzeń**  | Wprowadź `1000` w obszarze **kwota**.
   **Częstotliwość telemetrii** | Wprowadź `10` sekund.
   **Czas trwania symulacji** | Wybierz **zakończone:** i wprowadź `5` minut.

   Gdy skończysz, kliknij przycisk **Rozpocznij symulację**. Symulacja będzie trwać łącznie 5 minut. Dane będą generowane przez 1000 symulowanych urządzeń co 10 sekund.  

   [![Instalator symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Podczas trwania symulacji można co 10 sekund zaobserwować aktualizowanie pól **Total messages** (Całkowita liczba komunikatów) i **Messages per second** (Komunikaty na sekundę). Symulacja zakończy się po około 5 minutach, po czym nastąpi powrót do strony **Konfiguracja symulacji**.

   [![Symulacja urządzenia z systemem](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Sprawdzanie danych telemetrycznych

W tej sekcji końcowej upewnieniu się, że dane telemetryczne został wygenerowany i przechowywane w środowisku usługi Time Series Insights. Aby sprawdzić dane, należy skorzystać z eksploratora Time Series Insights, używanego do wykonywania zapytań i analizowania danych telemetrycznych.

1. Wróć do grupy zasobów w środowisku usługi Time Series Insights **Przegląd** strony. Wybierz środowisko usługi Time Series Insights.

   [![Grupy zasobów w środowisku Series Insights czasu i środowiska](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. W środowisku usługi Time Series Insights **Przegląd** kliknij **URL Eksploratora usługi Time Series Insights** otwarcie Eksploratora usługi Time Series Insights.

   [![Eksplorator usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Eksplorator usługi Time Series Insights będzie załadować i uwierzytelnianie przy użyciu portalu konta platformy Azure. Po początkowej widoku widoczne w obszarze wykresu czy środowisko usługi Time Series Insights w rzeczywistości został wypełniony danymi symulowanych danych telemetrycznych. Do odfiltrowania mniejszego przedziału czasu użyj listy rozwijanej dostępnej w lewym górnym rogu. Następnie wprowadź przedział czasu na tyle duży, aby obejmował czas trwania symulacji urządzeń. Następnie kliknij przycisk wyszukiwania powiększanie klasy.

   [![Filtr zakresu czasu explorer czas Series Insights](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Zawężanie zakres czasu umożliwia wykres, aby powiększyć do chwilowego distinct transferu danych do usługi IoT hub i środowiska usługi Time Series Insights. Należy również zwrócić uwagę na tekst **Streaming complete** (Zakończono przesyłanie strumieniowe), który zawiera całkowitą liczbę znalezionych zdarzeń. Możesz również przeciągnąć **rozmiar interwału** suwak, aby kontrolować stopień szczegółowości kreślenia wykresu.

   [![Zakres czasu explorer Series Insights czasu filtrowany widok](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Ponadto można również lewym region, aby filtrowanie zakresu, a następnie kliknij prawym przyciskiem myszy i użyj **Eksploruj zdarzenia** Aby wyświetlić szczegóły zdarzenia w tabelarycznych **zdarzenia** widoku.

   [![Zakres czasu explorer Series Insights czasu filtrowany widok i zdarzenia](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ten samouczek tworzy kilka usług Azure działa, pozwalające obsługiwać rozwiązanie symulacji środowiska i urządzeń usługi Time Series Insights. Jeśli chcesz zakończyć lub odłożyć pracę z tą serią przewodników, pamiętaj o usunięciu wszystkich zasobów, aby uniknąć niepotrzebnych kosztów.

W menu portalu Azure po lewej stronie:

1. Kliknij przycisk **grup zasobów** ikonę, a następnie wybierz grupę zasobów utworzoną w ramach środowiska usługi Time Series Insights. W górnej części strony kliknij opcję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie kliknij przycisk **Usuń**.

1. Kliknij ikonę **Grupy zasobów**, a następnie wybierz grupę zasobów, która została utworzona przez akcelerator rozwiązania symulacji urządzeń. W górnej części strony kliknij **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie kliknij przycisk **Usuń**

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie środowiska usługi Time Series Insights 
> * Tworzenie rozwiązania symulacji urządzeń zawierającego usługę IoT Hub
> * Łączenie środowiska usługi Time Series Insights do usługi IoT hub
> * Uruchamianie symulacji urządzenia przesyłanie strumieniowe danych do środowiska usługi Time Series Insights
> * Sprawdzanie symulowanych danych telemetrycznych

Teraz gdy wiesz, jak utworzyć środowiska usługi Time Series Insights, Dowiedz się, jak utworzyć aplikację sieci web, która zużywa dane ze środowiska usługi Time Series Insights:

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej usługi Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)
