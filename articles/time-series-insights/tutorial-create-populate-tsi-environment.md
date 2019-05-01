---
title: 'Samouczek: Tworzenie środowiska usługi Azure Time Series Insights | Microsoft Docs'
description: Dowiedz się, jak utworzyć środowisko usługi Time Series Insights wypełniane przy użyciu danych z symulowanych urządzeń.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: c974e011e6f101eab617a370b24306f80c249b90
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718418"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Samouczek: Tworzenie środowiska usługi Azure Time Series Insights

Ten samouczek przeprowadzi Cię przez proces tworzenia środowiska Time Series Insight (TSI) wypełnianego przy użyciu danych z symulowanych urządzeń. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie środowiska usługi TSI 
> * Tworzenie rozwiązania symulacji urządzeń zawierającego usługę IoT Hub
> * Dołączanie środowiska TSI do usługi IoT Hub
> * Uruchamianie symulacji urządzeń w celu przesyłania danych do środowiska TSI
> * Sprawdzanie symulowanych danych telemetrycznych

## <a name="video"></a>Połączenia wideo

### <a name="in-this-video-we-show-you-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-that-can-be-used-to-get-started-with-time-series-insights-br"></a>W tym filmie pokazujemy, jak używać akceleratora rozwiązań IoT na platformie Azure do generowania danych, które mogą służyć do rozpoczęcia pracy z usługą Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). 

Twoje konto logowania do platformy Azure musi należeć do roli właściciela subskrypcji. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal](/azure/role-based-access-control/role-assignments-portal)

## <a name="overview"></a>Omówienie

Środowisko TSI służy do zbierania i przechowywania danych z urządzeń. Po umieszczeniu danych w środowisku TSI możesz użyć aplikacji [Eksplorator TSI](time-series-quickstart.md) i interfejsu [TSI Query API](/rest/api/time-series-insights/ga-query-api) do odpytywania i analizowania danych.

Podobnie jak wszystkie urządzenia, symulowane lub fizyczne, usługa IoT Hub jest punktem połączenia używanym przez urządzenia do nawiązania bezpiecznego połączenia i przesyłania danych do chmury platformy Azure. Zgodnie z opisem w [TLS — omówienie](time-series-insights-overview.md) usługa IoT Hub służy również jako źródło zdarzeń przy strumieniowym przesyłaniu danych do środowiska TSI. 

Ten samouczek używa również [akceleratora rozwiązania IoT](/azure/iot-accelerators/), aby wygenerować i przesyłać strumieniowo przykładowe dane telemetryczne do usługi IoT Hub. Akceleratory rozwiązania IoT są dostarczane ze wstępnie skonfigurowanymi rozwiązaniami klasy korporacyjnej umożliwiającymi przyspieszenie rozwoju niestandardowych rozwiązań IoT. 

## <a name="create-a-tsi-environment"></a>Tworzenie środowiska usługi TSI

Najpierw utwórz środowisko TSI w Twojej subskrypcji platformy Azure:

1. Zaloguj się do witryny [Azure portal](https://portal.azure.com) przy użyciu konta platformy Azure.  
2. W menu u góry po lewej stronie wybierz pozycję **+ Utwórz zasób**.  
3. Wybierz kategorię **Internet rzeczy**, a następnie wybierz **Time Series Insights**.  
   
   [![Wybierz opcję Tworzenie środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

4. Na stronie **Środowisko Time Series Insights** wypełnij wymagane parametry:
   
   Parametr|Opis
   ---|---
   **Nazwa środowiska** | Wybierz unikatową nazwę dla środowiska TSI. Nazwa jest używana przez aplikację Eksplorator TSI i interfejs Query API.
   **Subskrypcja** | Subskrypcje są kontenerami zasobów platformy Azure. Wybierz subskrypcję platformy Azure, w której chcesz utworzyć środowisko TSI.
   **Grupa zasobów** | Grupa zasobów jest kontenerem zasobów platformy Azure. Dla zasobu środowiska TSI wybierz istniejącą grupę zasobów lub utwórz nową.
   **Lokalizacja** | Wybierz region centrum danych dla używanego środowiska TSI. Aby uniknąć kosztów i opóźnień spowodowanych dodatkową przepustowością, najlepiej przechowywać środowiska TSI w tym samym regionie co inne zasoby IoT.
   **Cennik jednostki SKU** | Wybierz potrzebną przepustowość. Aby zapewnić najniższy koszt i wydajność typu Starter, wybierz S1.
   **Dyspozycyjność** | Dyspozycyjność jest mnożnikiem stosowanym do prędkości wejściowej, pojemności oraz kosztów związanych z wybraną jednostką SKU.  Wydajność środowiska można zmienić po jego utworzeniu. Aby uzyskać najniższy koszt, wybierz dyspozycyjność 1. 

   Po zakończeniu kliknij pozycję **Utwórz**, aby rozpocząć proces aprowizowania.

   ![Tworzenie zasobu środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)

5. Na panelu **Powiadomienia** można monitorować przebieg wdrażania, które nie powinno zabrać więcej niż minutę:  

   ![Pomyślnie zakończono wdrażanie środowiska Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

Następnie należy utworzyć rozwiązanie polegające na symulacji urządzeń, które wygeneruje dane testowe służące do wypełnienia środowiska TSI:

1. W oddzielnym oknie/oddzielnej karcie przejdź do https://www.azureiotsolutions.com, zaloguj się przy użyciu tego samego konta subskrypcji platformy Azure i wybierz akcelerator „Symulacja urządzeń”:

   ![Uruchom akcelerator symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sa-main.png)

2. Wprowadź wymagane parametry na stronie **Utwórz rozwiązanie symulacji urządzeń**:

   Parametr|Opis
   ---|---
   **Nazwa rozwiązania** | Unikatowa wartość służąca do tworzenia nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów.
   **Subskrypcja** | Podaj tę samą subskrypcję, która w poprzedniej sekcji była używana do utworzenia środowiska TSI.
   **Region** | Podaj ten sam region, który w poprzedniej sekcji był używany do utworzenia środowiska TSI. 
   **Wdróż opcjonalne zasoby platformy Azure** | Pozostaw zaznaczone pole wyboru **IoT Hub**, ponieważ symulowane urządzenia zostaną użyte do połączenia/strumieniowania danych.

   Gdy skończysz, kliknij przycisk **Utwórz rozwiązanie**, aby udostępnić zasoby Azure rozwiązania, co zajmie około 6–7 minut:

   ![Inicjuj rozwiązanie do symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)

3. Po zakończeniu inicjowania tekst wyświetlany powyżej nowego rozwiązania ulegnie zmianie z „Inicjowanie...” na „Gotowe”:

   >[!IMPORTANT]
   > Nie klikaj jeszcze przycisku **Uruchom**! Nie zamykaj też tej strony sieci WWW, gdyż powrócisz do niej później.

   ![Zakończono inicjowanie rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)

4. Teraz wróć do portalu Azure i sprawdź nowo utworzone zasoby w ramach swojej subskrypcji. W portalu na stronie **Grupy zasobów** można zauważyć nową grupę zasobów utworzoną przy użyciu parametru **Nazwa rozwiązania** w ostatnim kroku. Ponadto będą tam wszystkie zasoby utworzone w celu obsługi rozwiązania symulacji urządzeń:

   [![Zasoby rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Dołączanie środowiska TSI do usługi IoT Hub 

Wiesz już, jak utworzyć dwa zestawy zasobów, każdy we własnej grupie zasobów: 
- Tworzenie środowiska usługi TSI 
- Zasoby rozwiązania symulacji urządzeń, w tym usługi IoT Hub, wygenerowane przez akcelerator rozwiązań

Przypominamy, że symulowane urządzenia muszą nawiązać połączenia z usługą IoT Hub w celu strumieniowego przesyłania danych urządzenia. Aby spowodować przepływ danych do środowiska TSI, należy wprowadzić zmiany w konfiguracji zarówno usługi IoT Hub, jak i środowiska TSI. 

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Konfiguracja usługi IoT Hub: definiowanie grupy konsumentów

Usługa IoT Hub zawiera różne punkty końcowe, służące do udostępniania funkcji innym aktorom. Punkt końcowy „Zdarzenia” umożliwia innym aplikacjom używanie danych, ponieważ są one przesyłane strumieniowo do wystąpienia usługi IoT Hub. W szczególności „grupy użytkowników” udostępniają aplikacjom mechanizm do nasłuchiwania i pobierania danych z usługi IoT Hub. 

Następnie należy zdefiniować nową właściwość „Grupa użytkowników” w punkcie końcowym „Zdarzenia” usługi IoT Hub rozwiązania symulacji urządzeń. 

1. W portalu Azure przejdź do strony **Omówienie** należącej do grupy zasobów utworzonej dla rozwiązania symulacji urządzeń, a następnie wybierz zasób IoT Hub:

   [![Grupa zasobów rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Zwróć również uwagę na parametr **Nazwa** zasobu IoT Hub wygenerowanego dla rozwiązania, gdyż będziesz odwoływać się do niego później.

2. Przewiń w dół i wybierz stronę **Punkty końcowe**, a następnie wybierz punkt końcowy **Zdarzenia**. Na stronie punktu końcowego **Właściwości** wprowadź unikatową nazwę punktu końcowego w obszarze grupy użytkowników „$Default”, a następnie kliknij przycisk **Zapisz**:

   [![Punkty końcowe usługi IoT Hub rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-environment-configuration-define-an-event-source"></a>Konfiguracja środowiska TSI: zdefiniuj źródło zdarzeń

Teraz dołącz nowy punkt końcowy zdarzeń „grupa użytkowników” usługi IoT Hub do środowiska TSI jako „źródło zdarzeń”.

1. Przejdź na stronę **Przegląd** grupy zasobów utworzonej dla środowiska TSI, a następnie wybierz środowisko TSI:

   [![Grupa zasobów środowiska TSI i środowisko](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

2. Na stronie środowiska TSI wybierz **Źródła zdarzeń**, a następnie kliknij przycisk **+ Dodaj**:

   ![Środowisko TSI — przegląd](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)

3. Na stronie **Nowe źródło zdarzeń** wprowadź wymagane parametry: 

   Parametr|Opis
   ---|---
   **Nazwa źródła zdarzeń** | Wymaga unikatowej wartości, która jest używana jako nazwa źródła zdarzeń.
   **Element źródłowy** | Wybierz opcję „IoT Hub”.
   **Opcja importu** | Użyj opcji domyślnej „Użyj IoT Hub z dostępnych subskrypcji”. Wybór tej opcji spowoduje, że następna lista rozwijana zostanie wypełniona dostępnymi subskrypcjami.
   **Subskrypcja** | Wybierz tę samą subskrypcję, w której utworzono środowisko TSI i zasoby symulacji urządzeń.
   **Nazwa IoT Hub** | Domyślnie powinna to być zanotowana wcześniej nazwa usługi IoT Hub. W przeciwnym przypadku wybierz prawidłową usługę IoT Hub.
   **Nazwa zasady IoT Hub** | Pozostaw wartość domyślną „iothubowner”.
   **Grupa użytkowników IoT Hub** | Powinna być taka sama jak nazwa grupy użytkowników IoT Hub utworzonej wcześniej. Jeśli tak nie jest, wybierz prawidłową nazwę grupy użytkowników. 
   **Format serializacji zdarzeń** | Pozostaw wartość domyślną „JSON”.
   **Nazwa właściwości znacznika czasu** | Podaj „timestamp”.

   Po zakończeniu kliknij przycisk **Utwórz**, aby dodać źródło zdarzeń. Po powrocie na stronę **Przegląd** grupy zasobów obok zasobu środowiska TSI zauważysz nowy zasób „Time Series Insights event source”.

   ![Nowe źródło zdarzeń środowiska TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)

## <a name="run-a-device-simulation-to-stream-data-into-the-tsi-environment"></a>Uruchamianie symulacji urządzeń w celu przesyłania danych do środowiska TSI

Po zakończeniu prac związanych z konfiguracją nadszedł czas, aby wypełnić środowisko TSI danymi przykładowymi pochodzącymi z symulowanych urządzeń.

Przypominamy, że w [sekcji Tworzenie symulacji urządzeń](#create-a-device-simulation) akcelerator utworzył kilka zasobów Azure w celu obsługi naszego rozwiązania. Oprócz omawianej już usługi IoT Hub utworzono również aplikację sieci WWW Azure App Service, która służy do tworzenia i przesyłania danych telemetrycznych symulowanych urządzeń.

1. Wróć do swojego [Pulpitu nawigacyjnego akceleratorów rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard). W razie potrzeby zaloguj się ponownie, używając tego samego konta platformy Azure, które było używane w tym samouczku. Teraz możesz kliknąć przycisk **Uruchom** w rozwiązaniu „Symulacja urządzeń”:

     ![Pulpit nawigacyjny akceleratorów rozwiązań](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)

2. W tym momencie zostanie uruchomiona aplikacja sieci Web symulacji urządzeń, której ładowanie początkowe może potrwać kilka sekund. Zostanie również wyświetlony monit o zgodę na przyznanie aplikacji sieci Web uprawnienia „Zaloguj się i odczytaj profil”. To uprawnienie umożliwia aplikacji pobranie informacji o profilu użytkownika, które są niezbędne do obsługi działania aplikacji:

     ![Zgoda na aplikację sieci Web symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)

3. Po załadowaniu strony **Konfiguracja symulacji** wprowadź wymagane parametry: 

   Parametr|Opis
   ---|---
   **Docelowa usługa IoT Hub** | Wybierz „Użyj dostarczonej wstępnie usługi IoT Hub”.
   **Model urządzenia** | Wybierz opcję „Chiller”.
   **Liczba urządzeń**  | W obszarze **Kwota** wprowadź 1000.
   **Częstotliwość telemetrii** | Wprowadź 10 sekund.
   **Czas trwania symulacji** | Wybierz **Kończy się za:** i wprowadź 5 minut.

   Gdy skończysz, kliknij przycisk **Rozpocznij symulację**. Symulacja będzie trwać łącznie 5 minut. Dane będą generowane przez 1000 symulowanych urządzeń co 10 sekund.  

   ![Konfiguracja symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)

4. Podczas trwania symulacji można co 10 sekund zaobserwować aktualizowanie pól **Total messages** (Całkowita liczba komunikatów) i **Messages per second** (Komunikaty na sekundę). Symulacja zakończy się po około 5 minutach, po czym nastąpi powrót do strony **Konfiguracja symulacji**.

   ![Przebieg symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)

## <a name="verify-the-telemetry-data"></a>Sprawdzanie danych telemetrycznych

W tej końcowej sekcji sprawdź, czy dane telemetryczne zostały wygenerowane i są przechowywane w środowisku TSI. Aby sprawdzić dane, należy skorzystać z eksploratora Time Series Insights, używanego do wykonywania zapytań i analizowania danych telemetrycznych.

1. Powróć na stronę **Przegląd** grupy zasobów środowiska TSI, a następnie wybierz ponownie środowisko TSI:

   [![Grupa zasobów środowiska TSI i środowisko](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

2. Na stronie **Omówienie** środowiska TSI kliknij przycisk **Adres URL eksploratora Time Series Insights**, aby otworzyć eksploratora TSI:

   [![Eksplorator TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

3. Eksplorator TSI przeprowadzi ładowanie i uwierzytelnienie przy użyciu Twojego konta portalu platformy Azure. Na obszarze wykresu od razu można dostrzec, że środowisko TSI rzeczywiście zostało wypełnione symulowanymi danymi telemetrycznymi. Do odfiltrowania mniejszego przedziału czasu użyj listy rozwijanej dostępnej w lewym górnym rogu. Następnie wprowadź przedział czasu na tyle duży, aby obejmował czas trwania symulacji urządzeń. Następnie kliknij szkło powiększające:

   [![Filtr zakresu czasu eksploratora TSI](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

4. Skrócenie przedziału czasu umożliwia powiększenie wykresu w celu wyróżnienia serii danych przesyłanych do usługi IoT Hub i środowiska TSI. Należy również zwrócić uwagę na tekst **Streaming complete** (Zakończono przesyłanie strumieniowe), który zawiera całkowitą liczbę znalezionych zdarzeń. Szczegółowość wykresu można regulować suwakiem **Rozmiar interwału**:

   [![Widok odfiltrowanego przedziału czasu Eksploratora TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

5. Ponadto można także lewym przyciskiem myszy kliknąć region w celu odfiltrowania przedziału, a następnie kliknąć prawym przyciskiem myszy i użyć opcji **Eksploruj zdarzenia**, aby wyświetlić szczegóły zdarzeń w tabelarycznym widoku **Zdarzenia**:

   [![Widok odfiltrowanego przedziału czasu Eksploratora TSI i zdarzeń](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym przewodniku zostanie utworzonych kilka działających usług platformy Azure w celu obsługi środowiska TSI i rozwiązania symulacji urządzeń. Jeśli chcesz zakończyć lub odłożyć pracę z tą serią przewodników, pamiętaj o usunięciu wszystkich zasobów, aby uniknąć niepotrzebnych kosztów. 

W menu portalu Azure po lewej stronie:

1. Kliknij ikonę **Grupy zasobów**, a następnie wybierz grupę zasobów utworzoną dla środowiska TSI. W górnej części strony kliknij opcję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie kliknij przycisk **Usuń**. 
2. Kliknij ikonę **Grupy zasobów**, a następnie wybierz grupę zasobów, która została utworzona przez akcelerator rozwiązania symulacji urządzeń. W górnej części strony kliknij opcję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie kliknij przycisk **Usuń**. 

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


