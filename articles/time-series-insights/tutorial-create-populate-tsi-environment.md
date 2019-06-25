---
title: 'Samouczek: Tworzenie środowiska usługi Azure Time Series Insights | Microsoft Docs'
description: Dowiedz się, jak utworzyć środowisko usługi Time Series Insights, które jest wypełniana przy użyciu danych z symulowanych urządzeń.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 06a450c47c7264bdecb663c9f71e3a9753df5e1e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273532"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Samouczek: Tworzenie środowiska usługi Azure Time Series Insights

Ten samouczek przeprowadzi Cię przez proces tworzenia środowisku usługi Azure Time Series Insights, w którym jest wypełniana przy użyciu danych z symulowanych urządzeń. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie środowiska usługi Time Series Insights.
> * Utwórz rozwiązanie do symulacji urządzenia, które zawiera usługi IoT hub.
> * Podłącz środowiska usługi Time Series Insights do usługi IoT hub.
> * Uruchamianie symulacji urządzenia przesyłanie strumieniowe danych do środowiska usługi Time Series Insights.
> * Weryfikacja danych symulowanych danych telemetrycznych.

## <a name="video"></a>Połączenia wideo

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Dowiedz się, jak używać akceleratora rozwiązań Azure IoT do generowania danych i wprowadzenie do usługi Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Twoje konto platformy Azure Zaloguj się również musi należeć do tej subskrypcji **właściciela** roli. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i witryny Azure portal](/azure/role-based-access-control/role-assignments-portal).

## <a name="overview"></a>Omówienie

Środowisko usługi Time Series Insights jest gdzie zbierania i przechowywania danych urządzenia. Po umieszczeniu [Eksploratora usługi Azure Time Series Insights](time-series-quickstart.md) i [czas serii Insights — interfejs API zapytań](/rest/api/time-series-insights/ga-query-api) może służyć do tworzenia zapytań i analizowania danych.

Usługa Azure IoT Hub jest źródło zdarzenia, który jest używany przez wszystkie urządzenia (symulowanych lub fizycznych) w tym samouczku do nawiązania bezpiecznego połączenia i transmisji danych w Twojej chmurze platformy Azure.

Ten samouczek używa również [akcelerator rozwiązań IoT](https://www.azureiotsolutions.com) Generowanie i przesyłanie strumieniowe przykładowe dane telemetrii do usługi IoT Hub.

>[!TIP]
> [Akceleratory rozwiązań IoT](https://www.azureiotsolutions.com) udostępniają korporacyjnej wstępnie skonfigurowanych rozwiązań, które umożliwia przyspieszenie rozwoju niestandardowych rozwiązań IoT.

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

Najpierw utwórz rozwiązania symulacji urządzenia, które generuje dane testowe, aby wypełnić środowiska usługi Time Series Insights.

1. W oddzielnym oknie lub na karcie, przejdź do [azureiotsolutions.com](https://www.azureiotsolutions.com). Zaloguj się przy użyciu tego samego konta subskrypcji platformy Azure, a następnie wybierz pozycję **symulacji urządzenia** akceleratora.

   [![Uruchom akceleratora symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Wprowadź wymagane parametry na **rozwiązania tworzenia symulacji urządzenia** strony.

   Parametr|Opis
   ---|---
   **Nazwa wdrożenia** | Ta unikatowa wartość jest używana do tworzenia nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów.
   **Subskrypcja platformy Azure** | Określenie tej samej subskrypcji, który został użyty do utworzenia środowiska usługi Time Series Insights w poprzedniej sekcji.
   **Opcje wdrażania** | Wybierz **Aprowizować nowe Centrum IoT Hub** do tworzenia nowego centrum IoT hub specyficzne dla tego samouczka.
   **Lokalizacja platformy Azure** | Określ tego samego regionu, który został użyty do utworzenia środowiska usługi Time Series Insights w poprzedniej sekcji.

   Gdy skończysz, wybierz pozycję **tworzenie rozwiązania** do aprowizowania zasobów platformy Azure z tego rozwiązania. Może potrwać do 20 minut, aby ukończyć ten proces.

   [![Aprowizacja rozwiązania symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Po zakończeniu aprowizacji tekst powyżej nowe rozwiązanie zmieni się z **aprowizacji** do **gotowe**.

   >[!IMPORTANT]
   > Nie wybieraj **Uruchom** jeszcze! Nie zamykaj tej strony sieci web ponieważ powrócisz do niego później.

   [![Inicjowanie obsługi administracyjnej pełną rozwiązania symulacji urządzenia](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Teraz Sprawdź, czy nowo utworzone zasoby w witrynie Azure portal. Na **grup zasobów** strony, zwróć uwagę, że utworzono nową grupę zasobów, przy użyciu **Nazwa rozwiązania** podany w poprzednim kroku. Zanotuj zasoby, które zostały utworzone do symulacji urządzenia.

   [![Zasoby symulacji urządzenia](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Tworzenie środowiska

Po drugie należy utworzyć środowisko usługi Time Series Insights w Twojej subskrypcji platformy Azure.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu swojego konta subskrypcji platformy Azure. 
1. W menu u góry po lewej stronie wybierz pozycję **+ Utwórz zasób**. 
1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz pozycję **Time Series Insights**. 

   [![Wybierz opcję Tworzenie środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Na **środowiska usługi Time Series Insights** strony, wypełnij wymagane parametry.

   Parametr|Opis
   ---|---
   **Nazwa środowiska** | Wybierz unikatową nazwę dla środowiska usługi Time Series Insights. Nazwy są wykorzystywane przez Eksploratora usługi Time Series Insights oraz [interfejsów API zapytań](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Subskrypcja** | Subskrypcje są kontenerami zasobów platformy Azure. Wybierz subskrypcję, aby utworzyć środowisko usługi Time Series Insights.
   **Grupa zasobów** | Grupa zasobów jest kontenerem zasobów platformy Azure. Wybierz istniejącą grupę zasobów lub Utwórz nową grupę dla zasobu środowiska usługi Time Series Insights.
   **Location** | Wybierz region centrum danych dla danego środowiska usługi Time Series Insights. Aby uniknąć dodatkowych opóźnień, należy utworzyć środowisko usługi Time Series Insights, w tym samym regionie, co inne zasoby IoT.
   **Warstwa** | Wybierz potrzebną przepustowość. Wybierz **S1**.
   **Dyspozycyjność** | Pojemność jest mnożnik stosowane do szybkości transferu danych przychodzących i pojemność magazynu skojarzone z wybrana jednostka SKU. Po utworzeniu można zmienić pojemność. Wybierz pojemności **1**.

   Po zakończeniu wybierz pozycję **przeglądu + Utwórz** można przejść do następnego kroku.

   [![Tworzenie zasobu środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Teraz Połącz środowiska usługi Time Series Insights do usługi IoT hub, utworzone przez akcelerator rozwiązań. Ustaw **wybierz koncentrator** do `Select existing`. Następnie wybierz utworzony przez akcelerator rozwiązań, gdy ustawienie usługi IoT hub **nazwę Centrum IoT Hub**.

   [![Łączenie środowiska usługi Time Series Insights do utworzonej usługi IoT hub](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png#lightbox)

1. Sprawdź **powiadomienia** panelu, aby monitorować ukończenie wdrożenia. 

   [![Wdrażanie środowiska Series Insights czasu zakończyło się pomyślnie](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Uruchamianie symulacji urządzenia do transmisji danych

Teraz, wdrażania i konfiguracji początkowej jego zakończenie, wypełnij środowiska usługi Time Series Insights przy użyciu przykładowych danych z [symulowanych urządzeń utworzonych przez akcelerator](#create-a-device-simulation).

Wraz z Centrum IoT hub aplikację sieci web w usłudze Azure App Service został wygenerowany do tworzenia i przesyłania danych telemetrycznych z urządzenia symulowanego.

1. Wróć do swojego [Pulpitu nawigacyjnego akceleratorów rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard). Zaloguj się ponownie, jeśli to konieczne, przy użyciu tego samego konta platformy Azure, którego używasz w tym samouczku. Teraz możesz wybrać **Uruchom** w ramach rozwiązania "Symulacji urządzenia".

     [![Pulpit nawigacyjny akceleratorów rozwiązania](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. W aplikacji sieci web symulacji urządzenia rozpoczyna się od monitowania użytkownika, aby przyznać aplikacji sieci web "zalogowania Cię i wczytania Twojego profilu" uprawnienia. To uprawnienie umożliwia aplikacji, które można pobrać informacji o profilu użytkownika, które są niezbędne do obsługi działanie aplikacji.

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

Ten samouczek tworzy kilka usług Azure działa pozwalające obsługiwać rozwiązanie symulacji środowiska i urządzeń usługi Time Series Insights. Aby je usunąć, przejdź z powrotem do witryny Azure portal.

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
