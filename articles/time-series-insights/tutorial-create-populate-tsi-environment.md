---
title: 'Samouczek: Tworzenie środowiska usługi Azure Time Series Insights | Microsoft Docs'
description: Dowiedz się, jak utworzyć środowisko usługi Time Series Insights, które jest wypełniana przy użyciu danych z symulowanych urządzeń.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: b8b46db043113f29f559ad44855d19f0d6ca73c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244155"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Samouczek: Tworzenie środowiska usługi Azure Time Series Insights

Ten samouczek przeprowadzi Cię przez proces tworzenia środowisku usługi Azure Time Series Insights, w którym jest wypełniana przy użyciu danych z symulowanych urządzeń. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie środowiska usługi Time Series Insights.
> * Utwórz rozwiązanie do symulacji urządzenia, które zawiera usługi IoT hub.
> * Podłącz środowiska usługi Time Series Insights do usługi IoT hub.
> * Uruchamianie symulacji urządzenia przesyłanie strumieniowe danych do środowiska usługi Time Series Insights.
> * Weryfikacja danych symulowanych danych telemetrycznych.

## <a name="video"></a>Wideo

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Dowiedz się, jak używać akceleratora rozwiązań Azure IoT do generowania danych i wprowadzenie do usługi Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Twoje konto platformy Azure Zaloguj się również musi należeć do tej subskrypcji **właściciela** roli. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i witryny Azure portal](/azure/role-based-access-control/role-assignments-portal).

## <a name="overview"></a>Omówienie

Środowisko usługi Time Series Insights jest gdzie zbierania i przechowywania danych urządzenia. Po zapisaniu danych [Eksploratora usługi Azure Time Series Insights](time-series-quickstart.md) i [czas serii Insights — interfejs API zapytań](/rest/api/time-series-insights/ga-query-api) może służyć do tworzenia zapytań i analizowania danych. Usługa Azure IoT Hub jest punkt połączenia używany przez wszystkie urządzenia (symulowanych lub fizycznych) do nawiązania bezpiecznego połączenia i transmisji danych do chmury platformy Azure. [Omówienie usługi Time Series Insights](time-series-insights-overview.md) zauważa, że usługi Azure IoT Hub stanowi również źródłem zdarzenia dla danych przesyłanych strumieniowo do środowiska usługi Time Series Insights. W tym samouczku [akcelerator rozwiązań IoT](/azure/iot-accelerators/) Generowanie i przesyłanie strumieniowe przykładowe dane telemetrii do usługi IoT Hub.

>[!TIP]
> Akceleratory rozwiązań IoT udostępniają korporacyjnej wstępnie skonfigurowanych rozwiązań, które umożliwia przyspieszenie rozwoju niestandardowych rozwiązań IoT.

## <a name="create-an-environment"></a>Tworzenie środowiska

Najpierw należy utworzyć środowisko usługi Time Series Insights w Twojej subskrypcji platformy Azure.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu swojego konta subskrypcji platformy Azure. 
1. W menu u góry po lewej stronie wybierz pozycję **+ Utwórz zasób**. 
1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz pozycję **Time Series Insights**. 

   [![Wybierz opcję Tworzenie środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Na **środowiska usługi Time Series Insights** strony, wypełnij wymagane parametry.

   Parametr|Opis
   ---|---
   **Nazwa środowiska** | Wybierz unikatową nazwę dla środowiska usługi Time Series Insights. Nazwy są używane przez Eksploratora usługi Time Series Insights i interfejsów API zapytań.
   **Subskrypcja** | Subskrypcje są kontenerami zasobów platformy Azure. Wybierz subskrypcję, aby utworzyć środowisko usługi Time Series Insights.
   **Grupa zasobów** | Grupa zasobów jest kontenerem zasobów platformy Azure. Wybierz istniejącą grupę zasobów lub Utwórz nową grupę dla zasobu środowiska usługi Time Series Insights.
   **Lokalizacja** | Wybierz region centrum danych dla danego środowiska usługi Time Series Insights. Aby uniknąć kosztów dodano przepustowości i opóźnienia, przechowywać środowiska usługi Time Series Insights, w tym samym regionie, co inne zasoby IoT.
   **Cennik jednostki SKU** | Wybierz potrzebną przepustowość. Generuje najniższy koszt i wydajności modułu uruchamiającego, wybierz pozycję `S1`.
   **Dyspozycyjność** | Dyspozycyjność jest mnożnikiem stosowanym do prędkości wejściowej, pojemności oraz kosztów związanych z wybraną jednostką SKU. Po utworzeniu można zmienić pojemność. Na najniższym koszcie wybierz o pojemności 1.

   Po zakończeniu wybierz pozycję **Utwórz** aby rozpocząć proces inicjowania obsługi administracyjnej.

   [![Tworzenie zasobu środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Sprawdź **powiadomienia** panelu, aby monitorować ukończenie wdrożenia. 

   [![Wdrażanie środowiska Series Insights czasu zakończyło się pomyślnie](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

Następnie należy utworzyć rozwiązania symulacji urządzenia, które generuje dane testowe, aby wypełnić środowiska usługi Time Series Insights.

1. W oddzielnym oknie lub na karcie, przejdź do [azureiotsolutions.com](https://www.azureiotsolutions.com). Zaloguj się przy użyciu tego samego konta subskrypcji platformy Azure, a następnie wybierz pozycję **symulacji urządzenia** akceleratora.

   [![Uruchom akceleratora symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Wprowadź wymagane parametry na **rozwiązania tworzenia symulacji urządzenia** strony.

   Parametr|Opis
   ---|---
   **Nazwa rozwiązania** | Ta unikatowa wartość jest używana do tworzenia nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów.
   **Subskrypcja** | Określenie tej samej subskrypcji, który został użyty do utworzenia środowiska usługi Time Series Insights w poprzedniej sekcji.
   **Region** | Określ tego samego regionu, który został użyty do utworzenia środowiska usługi Time Series Insights w poprzedniej sekcji.
   **Wdróż opcjonalne zasoby platformy Azure** | Pozostaw **usługi IoT Hub** zaznaczone. Symulowane urządzenia umożliwia łączenie lub przesyłanie strumieniowe danych.

   Gdy skończysz, wybierz pozycję **tworzenie rozwiązania** do aprowizowania zasobów platformy Azure z tego rozwiązania. Może potrwać 6 – 7 minut, aby ukończyć ten proces.

   [![Aprowizacja rozwiązania symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Po zakończeniu aprowizacji tekst powyżej nowe rozwiązanie zmieni się z **aprowizacji** do **gotowe**.

   >[!IMPORTANT]
   > Nie wybieraj **Uruchom** jeszcze! Nie zamykaj tej strony sieci web ponieważ powrócisz do niego później.

   [![Inicjowanie obsługi administracyjnej pełną rozwiązania symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Teraz wróć do witryny Azure portal i sprawdzić nowo utworzone zasoby w ramach subskrypcji. W portalu **grup zasobów** strony, zwróć uwagę, że utworzono nową grupę zasobów, przy użyciu **Nazwa rozwiązania** podany w poprzednim kroku. Ponadto wszystkie zasoby, które zostały utworzone pozwalające obsługiwać rozwiązanie symulacji urządzenia.

   [![Zasoby rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-environment-to-the-iot-hub"></a>Łączenie środowiska w Centrum IoT Hub

Wiesz już, jak utworzyć dwa zestawy zasobów, każdy we własnej grupie zasobów:

- Puste środowisko usługi Time Series Insights.
- Zasoby rozwiązania symulacji urządzenia, takich jak usługi IoT hub, generowane przez akcelerator rozwiązań.

Przypominamy, że symulowane urządzenia muszą nawiązać połączenia z usługą IoT Hub w celu strumieniowego przesyłania danych urządzenia. Przepływ danych do środowiska usługi Time Series Insights, należy wprowadzić zmiany w konfiguracji do usługi IoT hub i środowiska usługi Time Series Insights.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Konfiguracja centrum IoT: Zdefiniowanie grupy konsumentów

IoT Hub udostępnia różne punkty końcowe do udostępniania funkcji do innych aktorów. Punkcie końcowym "Zdarzenia" umożliwia dla innych aplikacji do pracy z danymi, ponieważ jest ona przesyłana strumieniowo do Centrum IoT wystąpienia. W szczególności "grup odbiorców" udostępniają mechanizm dla aplikacji nasłuchiwać i pobierania danych z usługi IoT hub.

Następnie zdefiniuj nowy **grupy odbiorców** właściwości rozwiązania symulacji urządzenia IoT hub **punkcie końcowym zdarzenia**.

1. W witrynie Azure portal przejdź do **Przegląd** stronie grupy zasobów utworzonej dla rozwiązania symulacji urządzenia. Wybierz zasób Centrum IoT.

   [![Grupa zasobów rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Zanotuj **nazwa** zasobu usługi IoT Hub, generowany dla rozwiązania. Później będziesz odwoływać się do niego.

1. Przewiń w dół i wybierz **punktów końcowych** strony, a następnie wybierz pozycję **zdarzenia** punktu końcowego. W punkcie końcowym **właściwości** wpisz unikatową nazwę dla punktu końcowego usługi w ramach grupy odbiorców "$Default". Wybierz pozycję **Zapisz**.

   [![Punkty końcowe usługi IoT Hub rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="environment-configuration-define-an-event-source"></a>Konfiguracji środowiska: określania źródła zdarzeń

Teraz Połącz nowego centrum IoT hub **grupy odbiorców** endpoint zdarzeń do środowiska usługi Time Series Insights jako **źródła zdarzeń**.

1. Przejdź do **Przegląd** stronie grupy zasobów utworzonej dla środowiska usługi Time Series Insights. Wybierz środowisko usługi Time Series Insights.

   [![Grupy zasobów w środowisku Series Insights czasu i środowiska](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. Na stronie środowiska usługi Time Series Insights wybierz **źródła zdarzeń**. Następnie wybierz pozycję **+ Dodaj**.

   [![Omówienie środowiska Series Insights czasu](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Wprowadź wymagane parametry na **nowe źródło zdarzeń** strony.

   Parametr|Opis
   ---|---
   **Nazwa źródła zdarzeń** | Wymaga unikatowej wartości, która jest używana jako nazwa źródła zdarzeń.
   **Element źródłowy** | Wybierz **usługi IoT Hub**.
   **Opcja importu** | Wybierz domyślną `Use IoT hub from available subscriptions`. Ta opcja powoduje, że dalej listy rozwijanej, który będzie zapełniony dostępnych subskrypcji.
   **Subskrypcja** | Wybierz tę samą subskrypcję, w którym utworzono środowiska usługi Time Series Insights i zasobów symulacji urządzenia.
   **Nazwa IoT Hub** | Domyślnie powinna to być zanotowana wcześniej nazwa usługi IoT Hub. W przeciwnym przypadku wybierz prawidłową usługę IoT Hub.
   **Nazwa zasady IoT Hub** | Wybierz pozycję **iothubowner**.
   **Grupa użytkowników IoT Hub** | Powinna być taka sama jak nazwa grupy użytkowników IoT Hub utworzonej wcześniej. Jeśli tak nie jest, wybierz prawidłową nazwę grupy użytkowników.
   **Format serializacji zdarzeń** | Pozostaw wartości domyślne `JSON`.
   **Nazwa właściwości znacznika czasu** | Określ jako `timestamp`.

   Gdy skończysz, wybierz pozycję **Utwórz** do dodawania źródła zdarzeń. Po powrocie do grupy zasobów **Przegląd** strony, wraz z zasobu środowiska usługi Time Series Insights, zostanie wyświetlony nowy zasób "Źródła zdarzeń usługi Time Series Insights".

   [![Czas Series Insights środowiska nowe źródło zdarzeń](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Uruchamianie symulacji urządzenia do transmisji danych

Teraz, gdy wszystkie zadania konfiguracji jest zakończone, jest czasu na wypełnienie środowiska usługi Time Series Insights z przykładowymi danymi z symulowanych urządzeń.

Być może pamiętasz [Utwórz sekcję symulacji urządzenia](#create-a-device-simulation), kilka zasobów platformy Azure zostały utworzone przez akcelerator pozwalające obsługiwać rozwiązanie. Oprócz omawianej już usługi IoT Hub utworzono również aplikację sieci WWW Azure App Service, która służy do tworzenia i przesyłania danych telemetrycznych symulowanych urządzeń.

1. Wróć do swojego [Pulpitu nawigacyjnego akceleratorów rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard). Zaloguj się ponownie, jeśli to konieczne, przy użyciu tego samego konta platformy Azure, którego używasz w tym samouczku. Teraz możesz wybrać **Uruchom** w ramach rozwiązania "Symulacji urządzenia".

     [![Pulpit nawigacyjny akceleratorów rozwiązania](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. Urządzenie symulacji sieci web aplikacja zostanie uruchomiona w tym punkcie, a może potrwać kilka sekund, podczas ładowania początkowego. Zostanie również wyświetlony monit o zgodę przyznać aplikacji sieci web "zalogowania Cię i wczytania Twojego profilu" uprawnienia. To uprawnienie umożliwia aplikacji, które można pobrać informacji o profilu użytkownika, które są niezbędne do obsługi działanie aplikacji.

     [![Zgody aplikacji sieci web symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Po **Instalatora symulacji** ładowania strony, wprowadź wymagane parametry.

   Parametr|Opis
   ---|---
   **Docelowa usługa IoT Hub** | Wybierz **Użyj wstępnie przygotowany usługi IoT Hub**.
   **Model urządzenia** | Wybierz pozycję **Chiller** (Chłodnia).
   **Liczba urządzeń**  | Wprowadź `1000` w obszarze **kwota**.
   **Częstotliwość telemetrii** | Wprowadź `10` sekund.
   **Czas trwania symulacji** | Wybierz **zakończone:** i wprowadź `5` minut.

   Gdy skończysz, wybierz pozycję **Rozpocznij symulację**. Symulacja jest uruchamiany w sumie 5 minut. Generuje dane z 1000 symulowanych urządzeń co 10 sekund. 

   [![Instalator symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Podczas wykonywania symulacji, zwróć uwagę, że **łączna liczba komunikatów** i **komunikatów na sekundę** zaktualizować pól, co 10 sekund. Symulacja kończy się po upływie około 5 minut i powrót do **Instalatora symulacji**.

   [![Symulacja urządzenia z systemem](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Sprawdzanie danych telemetrycznych

W tej sekcji końcowej upewnieniu się, że dane telemetryczne został wygenerowany i przechowywane w środowisku usługi Time Series Insights. Aby sprawdzić dane, należy skorzystać z eksploratora Time Series Insights, używanego do wykonywania zapytań i analizowania danych telemetrycznych.

1. Wróć do grupy zasobów w środowisku usługi Time Series Insights **Przegląd** strony. Wybierz środowisko usługi Time Series Insights.

   [![Grupy zasobów w środowisku Series Insights czasu i środowiska](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. W środowisku usługi Time Series Insights **Przegląd** wybierz opcję **URL Eksploratora usługi Time Series Insights** otwarcie Eksploratora usługi Time Series Insights.

   [![Eksplorator usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Eksplorator usługi Time Series Insights ładuje i uwierzytelnia się za pomocą portalu konta platformy Azure. Po początkowej widoku widoczne w obszarze wykresu czy środowisko usługi Time Series Insights, został wypełniony danymi symulowanych danych telemetrycznych. Aby filtrować węższy zakres czasu, wybierz z listy rozwijanej w lewym górnym rogu. Wprowadź zakres czasu jest wystarczająco duży, aby obejmować czas trwania symulacji urządzenia. Następnie wybierz ikonę lupy wyszukiwania.

   [![Filtr zakresu czasu explorer czas Series Insights](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Zawężanie zakres czasu umożliwia wykres, aby powiększyć do odrębnych wzrosty transfer danych do usługi IoT hub i środowiska usługi Time Series Insights. Należy również zauważyć **ukończono przesyłanie strumieniowe** tekstu w prawym górnym rogu, który zawiera całkowitą liczbę zdarzeń, które można odnaleźć. Możesz również przeciągnąć **rozmiar interwału** suwak, aby kontrolować stopień szczegółowości kreślenia wykresu.

   [![Zakres czasu explorer Series Insights czasu filtrowany widok](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Ponadto można również lewym region, aby filtrowanie zakresu. Następnie kliknij prawym przyciskiem myszy i użyj **Eksploruj zdarzenia** Aby wyświetlić szczegóły zdarzenia w tabelarycznych **zdarzenia** widoku.

   [![Zakres czasu explorer Series Insights czasu filtrowany widok i zdarzenia](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ten samouczek tworzy kilka usług Azure działa pozwalające obsługiwać rozwiązanie symulacji środowiska i urządzeń usługi Time Series Insights. Jeśli chcesz porzucić lub odłożyć Praca z tej serii samouczków, należy usunąć wszystkie zasoby, aby uniknąć ponoszenia niepotrzebnych kosztów.

W menu po lewej stronie w witrynie Azure portal:

1. Wybierz **grup zasobów** ikony. Następnie wybierz grupę zasobów, utworzonej dla środowiska usługi Time Series Insights. W górnej części strony wybierz **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a wybierz **Usuń**.

1. Wybierz **grup zasobów** ikony. Następnie wybierz grupę zasobów, która została utworzona przez akcelerator rozwiązań symulacji urządzenia. W górnej części strony wybierz **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a wybierz **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie środowiska usługi Time Series Insights.
> * Utwórz rozwiązanie do symulacji urządzenia, które zawiera usługi IoT hub.
> * Podłącz środowiska usługi Time Series Insights do usługi IoT hub.
> * Uruchamianie symulacji urządzenia przesyłanie strumieniowe danych do środowiska usługi Time Series Insights.
> * Weryfikacja danych symulowanych danych telemetrycznych.

Teraz gdy wiesz, jak utworzyć środowiska usługi Time Series Insights, Dowiedz się, jak utworzyć aplikację sieci web, która zużywa dane ze środowiska usługi Time Series Insights:

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej usługi Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)
