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
ms.openlocfilehash: 42a7ba0c66bd603b19d60c7b3407ae5ca80db28e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65210179"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Samouczek: Tworzenie środowiska usługi Azure Time Series Insights

Ten samouczek przeprowadzi Cię przez proces tworzenia środowiska Time Series Insight (TSI) wypełnianego przy użyciu danych z symulowanych urządzeń. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie środowiska usługi TSI 
> * Tworzenie rozwiązania symulacji urządzeń zawierającego usługę IoT Hub
> * Dołączanie środowiska TSI do usługi IoT Hub
> * Uruchamianie symulacji urządzeń w celu przesyłania danych do środowiska TSI
> * Sprawdzanie symulowanych danych telemetrycznych

## <a name="video"></a>Wideo

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Dowiedz się, jak używać akceleratora rozwiązań Azure IoT do generowania danych i wprowadzenie do usługi Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Usługa Azure konto logowania musi również należeć do tej subskrypcji **właściciela** roli. Zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](/azure/role-based-access-control/role-assignments-portal) Aby uzyskać więcej informacji.

## <a name="overview"></a>Omówienie

Środowisko TSI służy do zbierania i przechowywania danych z urządzeń. Po umieszczeniu w środowisku usługi TSI [Eksplorator usługi TSI](time-series-quickstart.md) i [usługa TSI Przesyła zapytania API](/rest/api/time-series-insights/ga-query-api) może służyć do tworzenia zapytań i analizowania danych. Usługa IoT Hub jest punkt połączenia używany przez wszystkie urządzenia (symulowanych lub fizycznych) do nawiązania bezpiecznego połączenia i transmisji danych do chmury platformy Azure. [Przegląd TSI](time-series-insights-overview.md) zauważa, że usługa Azure IoT Hub służy również jako źródła zdarzeń dla danych przesyłanych strumieniowo do środowiska usługi TSI. W tym samouczku [akcelerator rozwiązań IoT](/azure/iot-accelerators/) Generowanie i przesyłanie strumieniowe przykładowe dane telemetrii do usługi IoT Hub.

>[!TIP]
> Akceleratory rozwiązań IoT udostępniają korporacyjnej wstępnie skonfigurowanych rozwiązań, które umożliwiają przyspieszenie rozwoju niestandardowych rozwiązań IoT.

## <a name="create-a-tsi-environment"></a>Tworzenie środowiska usługi TSI

Najpierw utwórz środowisko TSI w Twojej subskrypcji platformy Azure:

1. Zaloguj się do witryny [Azure portal](https://portal.azure.com) przy użyciu konta platformy Azure.  
1. W menu u góry po lewej stronie wybierz pozycję **+ Utwórz zasób**.  
1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz **Time Series Insights**.  

   [![Wybierz opcję Tworzenie środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Na stronie **Środowisko Time Series Insights** wypełnij wymagane parametry:

   Parametr|Opis
   ---|---
   **Nazwa środowiska** | Wybierz unikatową nazwę dla środowiska TSI. Nazwa użytkownika używana przez Eksplorator usługi TSI i interfejsów API zapytań.
   **Subskrypcja** | Subskrypcje są kontenerami zasobów platformy Azure. Wybierz subskrypcję, aby utworzyć środowisko usługi TSI.
   **Grupa zasobów** | Grupa zasobów jest kontenerem zasobów platformy Azure. Wybierz istniejącą grupę zasobów lub Utwórz nową grupę dla zasobu środowiska TSI.
   **Lokalizacja** | Wybierz region centrum danych dla używanego środowiska TSI. Aby uniknąć kosztów i opóźnień spowodowanych dodatkową przepustowością, najlepiej przechowywać środowiska TSI w tym samym regionie co inne zasoby IoT.
   **Cennik jednostki SKU** | Wybierz potrzebną przepustowość. Generuje najniższy koszt i wydajności modułu uruchamiającego, wybierz pozycję `S1`.
   **Dyspozycyjność** | Dyspozycyjność jest mnożnikiem stosowanym do prędkości wejściowej, pojemności oraz kosztów związanych z wybraną jednostką SKU.  Po utworzeniu można zmienić pojemność. Na najniższym koszcie wybierz o pojemności 1.

   Po zakończeniu kliknij pozycję **Utwórz**, aby rozpocząć proces aprowizowania.

   [![Tworzenie zasobu środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Sprawdź **powiadomienia** panelu, aby monitorować ukończenie wdrożenia.  

   [![Wdrażanie środowiska Series Insights czasu zakończyło się pomyślnie](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

Następnie należy utworzyć rozwiązanie polegające na symulacji urządzeń, które wygeneruje dane testowe służące do wypełnienia środowiska TSI:

1. W oddzielnym oknie lub kartę, przejdź do [azureiotsolutions.com](https://www.azureiotsolutions.com). Zaloguj się przy użyciu tego samego konta subskrypcji platformy Azure, a następnie wybierz pozycję **symulacji urządzenia** akceleratora.

   [![Uruchom akceleratora symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Wprowadź wymagane parametry na **rozwiązania tworzenia symulacji urządzenia** strony.

   Parametr|Opis
   ---|---
   **Nazwa rozwiązania** | Unikatowa wartość służąca do tworzenia nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów.
   **Subskrypcja** | Podaj tę samą subskrypcję, która w poprzedniej sekcji była używana do utworzenia środowiska TSI.
   **Region** | Podaj ten sam region, który w poprzedniej sekcji był używany do utworzenia środowiska TSI.
   **Wdróż opcjonalne zasoby platformy Azure** | Pozostaw zaznaczone pole wyboru **IoT Hub**, ponieważ symulowane urządzenia zostaną użyte do połączenia/strumieniowania danych.

   Po zakończeniu kliknij przycisk **tworzenie rozwiązania** do aprowizowania zasobów platformy Azure z tego rozwiązania. Może potrwać 6 – 7 minut, aby ukończyć ten proces.

   [![Aprowizacja rozwiązania symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Po zakończeniu aprowizacji tekst powyżej nowe rozwiązanie ulegnie zmianie z **aprowizacji...**  do **gotowe**:

   >[!IMPORTANT]
   > Nie klikaj jeszcze przycisku **Uruchom**! Nie zamykaj też tej strony sieci WWW, gdyż powrócisz do niej później.

   [![Inicjowanie obsługi administracyjnej pełną rozwiązania symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Teraz wróć do portalu Azure i sprawdź nowo utworzone zasoby w ramach swojej subskrypcji. W portalu na stronie **Grupy zasobów** można zauważyć nową grupę zasobów utworzoną przy użyciu parametru **Nazwa rozwiązania** w ostatnim kroku. Ponadto będą tam wszystkie zasoby utworzone w celu obsługi rozwiązania symulacji urządzeń:

   [![Zasoby rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Dołączanie środowiska TSI do usługi IoT Hub

Wiesz już, jak utworzyć dwa zestawy zasobów, każdy we własnej grupie zasobów:

- Puste środowisko TSI.
- Urządzenie symulacji rozwiązania zasobów, w tym Centrum IoT hub, generowane przez akcelerator rozwiązań.

Przypominamy, że symulowane urządzenia muszą nawiązać połączenia z usługą IoT Hub w celu strumieniowego przesyłania danych urządzenia. Aby spowodować przepływ danych do środowiska TSI, należy wprowadzić zmiany w konfiguracji zarówno usługi IoT Hub, jak i środowiska TSI.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Konfiguracja usługi IoT Hub: definiowanie grupy konsumentów

Usługa IoT Hub zawiera różne punkty końcowe, służące do udostępniania funkcji innym aktorom. Punkt końcowy „Zdarzenia” umożliwia innym aplikacjom używanie danych, ponieważ są one przesyłane strumieniowo do wystąpienia usługi IoT Hub. W szczególności „grupy użytkowników” udostępniają aplikacjom mechanizm do nasłuchiwania i pobierania danych z usługi IoT Hub.

Następnie należy zdefiniować nową **grupy odbiorców** właściwości na rozwiązania symulacji urządzenia IoT hub **punkcie końcowym zdarzenia**.

1. W portalu Azure przejdź do strony **Omówienie** należącej do grupy zasobów utworzonej dla rozwiązania symulacji urządzeń, a następnie wybierz zasób IoT Hub:

   [![Grupa zasobów rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Zwróć również uwagę na parametr **Nazwa** zasobu IoT Hub wygenerowanego dla rozwiązania, gdyż będziesz odwoływać się do niego później.

1. Przewiń w dół i wybierz stronę **Punkty końcowe**, a następnie wybierz punkt końcowy **Zdarzenia**. Na stronie punktu końcowego **Właściwości** wprowadź unikatową nazwę punktu końcowego w obszarze grupy użytkowników „$Default”, a następnie kliknij przycisk **Zapisz**:

   [![Punkty końcowe usługi IoT Hub rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-configuration-define-an-event-source"></a>Konfiguracja usługi TSI: określania źródła zdarzeń

Teraz z nowego centrum IoT hub **grupy odbiorców** punktu końcowego zdarzeń do środowiska usługi TSI jako **źródła zdarzeń**.

1. Przejdź na stronę **Przegląd** grupy zasobów utworzonej dla środowiska TSI, a następnie wybierz środowisko TSI:

   [![Grupa zasobów środowiska TSI i środowisko](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. Na stronie usługi TSI środowiska wybierz **źródła zdarzeń**, następnie kliknij przycisk **+ Dodaj**.

   [![Omówienie środowiska usługi TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Wprowadź wymagane parametry na **nowe źródło zdarzeń** strony.

   Parametr|Opis
   ---|---
   **Nazwa źródła zdarzeń** | Wymaga unikatowej wartości, która jest używana jako nazwa źródła zdarzeń.
   **Element źródłowy** | Wybierz **usługi IoT Hub**.
   **Opcja importu** | Wybierz domyślną `Use IoT hub from available subscriptions`. Wybór tej opcji spowoduje, że następna lista rozwijana zostanie wypełniona dostępnymi subskrypcjami.
   **Subskrypcja** | Wybierz tę samą subskrypcję, w której utworzono środowisko TSI i zasoby symulacji urządzeń.
   **Nazwa IoT Hub** | Domyślnie powinna to być zanotowana wcześniej nazwa usługi IoT Hub. W przeciwnym przypadku wybierz prawidłową usługę IoT Hub.
   **Nazwa zasady IoT Hub** | Wybierz pozycję **iothubowner**.
   **Grupa użytkowników IoT Hub** | Powinna być taka sama jak nazwa grupy użytkowników IoT Hub utworzonej wcześniej. Jeśli tak nie jest, wybierz prawidłową nazwę grupy użytkowników.
   **Format serializacji zdarzeń** | Pozostaw wartości domyślne `JSON`.
   **Nazwa właściwości znacznika czasu** | Określ jako `timestamp`.

   Po zakończeniu kliknij przycisk **Utwórz**, aby dodać źródło zdarzeń. Po powrocie na stronę **Przegląd** grupy zasobów obok zasobu środowiska TSI zauważysz nowy zasób „Time Series Insights event source”.

   [![Usługa TSI Przesyła środowiska nowe źródło zdarzeń](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data-into-tsi"></a>Uruchamianie symulacji urządzenia do transmisji danych w usłudze TSI

Po zakończeniu prac związanych z konfiguracją nadszedł czas, aby wypełnić środowisko TSI danymi przykładowymi pochodzącymi z symulowanych urządzeń.

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

W tej końcowej sekcji sprawdź, czy dane telemetryczne zostały wygenerowane i są przechowywane w środowisku TSI. Aby sprawdzić dane, należy skorzystać z eksploratora Time Series Insights, używanego do wykonywania zapytań i analizowania danych telemetrycznych.

1. Wróć do grupy zasobów w środowisku usługi TSI **Przegląd** strony. Wybierz środowisko usługi TSI.

   [![Grupa zasobów środowiska TSI i środowisko](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. W środowisku usługi TSI **Przegląd** kliknij **URL Eksploratora usługi Time Series Insights** aby otworzyć Eksplorator usługi TSI.

   [![Eksplorator TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Eksplorator TSI przeprowadzi ładowanie i uwierzytelnienie przy użyciu Twojego konta portalu platformy Azure. Na obszarze wykresu od razu można dostrzec, że środowisko TSI rzeczywiście zostało wypełnione symulowanymi danymi telemetrycznymi. Do odfiltrowania mniejszego przedziału czasu użyj listy rozwijanej dostępnej w lewym górnym rogu. Następnie wprowadź przedział czasu na tyle duży, aby obejmował czas trwania symulacji urządzeń. Następnie kliknij przycisk wyszukiwania powiększanie klasy.

   [![Filtr zakresu czasu eksploratora TSI](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Skrócenie przedziału czasu umożliwia powiększenie wykresu w celu wyróżnienia serii danych przesyłanych do usługi IoT Hub i środowiska TSI. Należy również zwrócić uwagę na tekst **Streaming complete** (Zakończono przesyłanie strumieniowe), który zawiera całkowitą liczbę znalezionych zdarzeń. Możesz również przeciągnąć **rozmiar interwału** suwak, aby kontrolować stopień szczegółowości kreślenia wykresu.

   [![Widok odfiltrowanego przedziału czasu Eksploratora TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Ponadto można również lewym region, aby filtrowanie zakresu, a następnie kliknij prawym przyciskiem myszy i użyj **Eksploruj zdarzenia** Aby wyświetlić szczegóły zdarzenia w tabelarycznych **zdarzenia** widoku.

   [![Widok odfiltrowanego przedziału czasu Eksploratora TSI i zdarzeń](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym przewodniku zostanie utworzonych kilka działających usług platformy Azure w celu obsługi środowiska TSI i rozwiązania symulacji urządzeń. Jeśli chcesz zakończyć lub odłożyć pracę z tą serią przewodników, pamiętaj o usunięciu wszystkich zasobów, aby uniknąć niepotrzebnych kosztów.

W menu portalu Azure po lewej stronie:

1. Kliknij ikonę **Grupy zasobów**, a następnie wybierz grupę zasobów utworzoną dla środowiska TSI. W górnej części strony kliknij opcję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie kliknij przycisk **Usuń**.

1. Kliknij ikonę **Grupy zasobów**, a następnie wybierz grupę zasobów, która została utworzona przez akcelerator rozwiązania symulacji urządzeń. W górnej części strony kliknij **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie kliknij przycisk **Usuń**

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie środowiska usługi TSI 
> * Tworzenie rozwiązania symulacji urządzeń zawierającego usługę IoT Hub
> * Dołączanie środowiska TSI do usługi IoT Hub
> * Uruchamianie symulacji urządzeń w celu przesyłania danych do środowiska TSI
> * Sprawdzanie symulowanych danych telemetrycznych

Teraz, gdy już wiesz, jak utworzyć własne środowisko TSI, dowiedz się, jak utworzyć aplikację internetową używającą danych ze środowiska TSI:

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej usługi Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)