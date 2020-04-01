---
title: 'Samouczek: Tworzenie środowiska — usługa Azure Time Series Insights | Dokumenty firmy Microsoft'
description: Dowiedz się, jak utworzyć środowisko usługi Time Series Insights wypełnione danymi z symulowanych urządzeń.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 7bebc9e682f5156fa235b77ff020e502695a28be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76981195"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Samouczek: Tworzenie środowiska usługi Azure Time Series Insights

W tym samouczku można przejść przez proces tworzenia środowiska usługi Azure Time Series Insights, które jest wypełnione danymi z symulowanych urządzeń. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie środowiska usługi Time Series Insights.
> * Utwórz rozwiązanie symulacji urządzenia, które zawiera centrum IoT hub.
> * Połącz środowisko usługi Time Series Insights z centrum IoT Hub.
> * Uruchom symulację urządzenia, aby przesyłać strumieniowo dane do środowiska usługi Time Series Insights.
> * Sprawdź symulowane dane telemetryczne.

> [!IMPORTANT]
> Zarejestruj się, aby uzyskać [bezpłatną subskrypcję platformy Azure,](https://azure.microsoft.com/free/) jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Twoje konto logowania platformy Azure również musi być członkiem roli **właściciela** subskrypcji. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Przejrzyj film

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Dowiedz się, jak używać akceleratora rozwiązań IoT platformy Azure do generowania danych i rozpoczynania pracy z usługą Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Omówienie

Środowisko Time Series Insights to miejsce, w którym dane urządzenia są zbierane i przechowywane. Po [zapisaniu, Eksplorator usługi Azure Time Series Insights](time-series-quickstart.md) i interfejs API [kwerendy usługi Time Series Insights](/rest/api/time-series-insights/ga-query-api) mogą służyć do wykonywania zapytań i analizowania danych.

Usługa Azure IoT Hub to źródło zdarzeń używane przez wszystkie urządzenia (symulowane lub fizyczne) w samouczku do bezpiecznego łączenia się i przesyłania danych do chmury platformy Azure.

W tym samouczku użyto również [akceleratora rozwiązania IoT](https://www.azureiotsolutions.com) do generowania i przesyłania strumieniowego przykładowych danych telemetrycznych do usługi IoT Hub.

>[!TIP]
> [Akceleratory rozwiązań IoT](https://www.azureiotsolutions.com) zapewniają wstępnie skonfigurowane rozwiązania klasy korporacyjnej, których można użyć do przyspieszenia opracowywania niestandardowych rozwiązań IoT.

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

Najpierw utwórz rozwiązanie symulacji urządzenia, które generuje dane testowe w celu zapełnienia środowiska usługi Time Series Insights.

1. W osobnym oknie lub karcie przejdź do [azureiotsolutions.com](https://www.azureiotsolutions.com). Zaloguj się przy użyciu tego samego konta subskrypcji platformy Azure i wybierz akcelerator **symulacji urządzeń.**

   [![Uruchom akcelerator symulacji urządzeń](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1.  Wybierz pozycję **Wypróbuj teraz**. Następnie wprowadź wymagane parametry na stronie **Tworzenie rozwiązania symulacji urządzeń.**

   Parametr|Opis
   ---|---
   **Nazwa wdrożenia** | Ta unikatowa wartość jest używana do tworzenia nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów.
   **Subskrypcja platformy Azure** | Określ tę samą subskrypcję, która została użyta do utworzenia środowiska usługi Time Series Insights w poprzedniej sekcji.
   **Opcje wdrażania** | Wybierz **pozycję Aprowizuj nowy Centrum IoT,** aby utworzyć nowy centrum IoT specyficzne dla tego samouczka.
   **Lokalizacja platformy Azure** | Określ ten sam region, który został użyty do utworzenia środowiska usługi Time Series Insights w poprzedniej sekcji.

   Po zakończeniu wybierz pozycję **Utwórz,** aby aprowizować zasoby platformy Azure rozwiązania. Ukończenie tego procesu może potrwać do 20 minut.

   [![Inicjuj rozwiązanie do symulacji urządzeń](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Po zakończeniu inicjowania obsługi administracyjnej zostaną wyświetlone dwie aktualizacje informujące o przeniesieniu stanu wdrożenia z **inicjowania obsługi administracyjnej** do **gotowości.** 

   >[!IMPORTANT]
   > Nie wprowadzaj jeszcze akceleratora rozwiązań! Zachowaj tę stronę internetową otwartą, ponieważ powrócisz do niej później.

   [![Zakończono inicjowanie rozwiązania symulacji urządzeń](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Teraz sprawdź nowo utworzone zasoby w witrynie Azure portal. Na **stronie Grupy zasobów** zwróć uwagę, że nowa grupa zasobów została utworzona przy użyciu **nazwy rozwiązania** podanej w ostatnim kroku. Zanotuj zasoby utworzone dla symulacji urządzenia.

   [![Zasoby symulacji urządzeń](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Tworzenie środowiska

Po drugie, utwórz środowisko usługi Time Series Insights w ramach subskrypcji platformy Azure.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta subskrypcji platformy Azure. 
1. W menu u góry po lewej stronie wybierz pozycję **+ Utwórz zasób**. 
1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz pozycję **Time Series Insights**. 

   [![Wybieranie zasobu środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Na stronie **środowiska usługi Time Series Insights** wypełnij wymagane parametry.

   Parametr|Opis
   ---|---
   **Nazwa środowiska** | Wybierz unikatową nazwę środowiska usługi Time Series Insights. Nazwy są używane przez Eksploratora usługi Time Series Insights i [interfejsy API kwerendy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Subskrypcja** | Subskrypcje są kontenerami zasobów platformy Azure. Wybierz subskrypcję, aby utworzyć środowisko usługi Time Series Insights.
   **Grupa zasobów** | Grupa zasobów jest kontenerem zasobów platformy Azure. Wybierz istniejącą grupę zasobów lub utwórz nową dla zasobu środowiska usługi Time Series Insights.
   **Lokalizacja** | Wybierz obszar centrum danych dla środowiska usługi Time Series Insights. Aby uniknąć dodatkowych opóźnień, należy utworzyć środowisko usługi Time Series Insights w tym samym regionie, co inne zasoby IoT.
   **Warstwa** | Wybierz potrzebną przepustowość. Wybierz **S1**.
   **Pojemność** | Pojemność to mnożnik zastosowany do szybkości transferu danych przychodzących i pojemności magazynu skojarzonej z wybraną jednostką SKU. Pojemność można zmienić po utworzeniu. Wybierz pojemność **1**.

   Po zakończeniu wybierz **pozycję Dalej: Źródło zdarzenia,** aby przejść do następnego kroku.

   [![Tworzenie zasobu środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Teraz połącz środowisko Time Series Insights z centrum IoT utworzonym przez akcelerator rozwiązań. Ustaw **wybierz koncentrator** na `Select existing`. Następnie wybierz centrum IoT utworzone przez Akcelerator rozwiązań podczas ustawiania **nazwy Centrum IoT**.

   [![Łączenie środowiska usługi Time Series Insights z utworzonym centrum IoT](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Nareszcie wybierz **pozycję Przejrzyj + utwórz**.

1. Sprawdź panel **Powiadomienia,** aby monitorować zakończenie wdrażania. 

   [![Pomyślnie zakończono wdrażanie środowiska Time Series Insights](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Uruchamianie symulacji urządzenia

Teraz, po zakończeniu wdrażania i konfiguracji początkowej, wypełnij środowisko Time Series Insights przykładowymi danymi z [symulowanych urządzeń utworzonych przez akcelerator](#create-a-device-simulation).

Wraz z centrum IoT hub została wygenerowana aplikacja sieci web usługi Azure App Service do tworzenia i przesyłania symulowanych danych telemetrycznych urządzenia.

1. Wróć do swojego [Pulpitu nawigacyjnego akceleratorów rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard). Zaloguj się ponownie, jeśli to konieczne, przy użyciu tego samego konta platformy Azure, którego używasz w tym samouczku. Wybierz "Rozwiązanie urządzenia", a następnie **przejdź do akceleratora rozwiązań,** aby uruchomić wdrożone rozwiązanie.

   [![Pulpit nawigacyjny akceleratorów rozwiązań](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Aplikacja internetowa symulacji urządzenia rozpoczyna się od monitowania o udzielenie aplikacji sieci web **logowania użytkownika i przeczytanie** uprawnień do profilu. To uprawnienie umożliwia aplikacji pobranie informacji o profilu użytkownika niezbędnych do obsługi funkcjonowania aplikacji.

   [![Zgoda na aplikację sieci Web symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Wybierz **+ Nowa symulacja**. Po **załadowaniu** strony ustawienia symulacji wprowadź wymagane parametry.

   Parametr|Opis
   ---|---
   **Docelowa usługa IoT Hub** | Wybierz **pozycję Użyj wstępnie aprowizowanego Centrum IoT**Hub .
   **Model urządzenia** | Wybierz pozycję **Chiller** (Chłodnia).
   **Liczba urządzeń**  | Wprowadź `10` w obszarze **Kwota**.
   **Częstotliwość telemetrii** | Wprowadź `10` sekundy.
   **Czas trwania symulacji** | Wybierz opcję Zakończ `5` **w:** i wprowadź minuty.

   Po zakończeniu wybierz pozycję **Rozpocznij symulację**. Symulacja trwa łącznie 5 minut. Generuje dane z 1000 symulowanych urządzeń co 10 sekund. 

   [![Konfiguracja symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Podczas symulacji należy zauważyć, że **pola Całkowita liczba wiadomości** i wiadomości na **sekundę** są aktualizowane, co około 10 sekund. Symulacja kończy się po około 5 minutach i zwraca **konfigurację symulacji**.

   [![Przebieg symulacji urządzeń](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Sprawdzanie danych telemetrycznych

W tej ostatniej sekcji można sprawdzić, czy dane telemetryczne zostały wygenerowane i przechowywane w środowisku usługi Time Series Insights. Aby sprawdzić dane, należy skorzystać z eksploratora Time Series Insights, używanego do wykonywania zapytań i analizowania danych telemetrycznych.

1. Wróć do strony **Omówienie** grupy zasobów środowiska usługi Time Series Insights. Wybierz środowisko Usługi Wgląd w szeregi czasowe.

   [![Grupa zasobów i środowisko środowiska usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Na stronie Omówienie środowiska **wglądu** w szczegółowe informacje o szeregach czasu wybierz **adres URL eksploratora Usługi Time Series Insights,** aby otworzyć Eksploratora Usługi Time Series Insights.

   [![Eksplorator usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Eksplorator usługi Time Series Insights ładuje i uwierzytelnia się przy użyciu konta witryny Azure portal. Początkowo pojawi się obszar wykresu, który został wypełniony środowiskiem usługi Time Series Insights wraz z symulowanymi danymi telemetrycznymi. Aby filtrować węższy zakres czasu, wybierz pozycję rozwijaną w lewym górnym rogu. Wprowadź zakres czasu wystarczająco duży, aby objąć czas trwania symulacji urządzenia. Następnie wybierz lupę wyszukiwania.

   [![Filtr zakresu czasu eksploratora usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Zawężenie zakresu czasu umożliwia wykresowi powiększenie do różnych serii transferu danych do centrum IoT hub i środowiska usługi Time Series Insights. Zwróć również uwagę na pełny tekst **przesyłania strumieniowego** w prawym górnym rogu, który pokazuje całkowitą liczbę znalezionych zdarzeń. Można również przeciągnąć suwak **Rozmiar interwału,** aby kontrolować szczegółowość wykresu na wykresie.

   [![Widok filtrowany zakres czasu eksploratora usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Nareszcie można również kliknąć lewym przyciskiem myszy region, aby filtrować zakres. Następnie kliknij prawym przyciskiem myszy i użyj **funkcji Eksploruj zdarzenia,** aby wyświetlić szczegóły zdarzenia w widoku **zdarzenia** tabelaryczne.

   [![Widok i zdarzenia filtrowane zakresem czasu usługi Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym samouczku utworzy się kilka uruchomionych usług platformy Azure do obsługi środowiska usługi Time Series Insights i rozwiązania do symulacji urządzeń. Aby je usunąć, przejdź z powrotem do witryny Azure Portal.

Z menu po lewej stronie w witrynie Azure portal:

1. Wybierz ikonę **Grupy zasobów.** Następnie wybierz grupę zasobów utworzoną dla środowiska Usługi Time Series Insights. U góry strony wybierz pozycję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**.

1. Wybierz ikonę **Grupy zasobów.** Następnie wybierz grupę zasobów utworzoną przez akcelerator rozwiązań symulacji urządzenia. U góry strony wybierz pozycję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie środowiska usługi Time Series Insights.
> * Utwórz rozwiązanie symulacji urządzenia, które zawiera centrum IoT hub.
> * Połącz środowisko usługi Time Series Insights z centrum IoT Hub.
> * Uruchom symulację urządzenia, aby przesyłać strumieniowo dane do środowiska usługi Time Series Insights.
> * Sprawdź symulowane dane telemetryczne.

Teraz, gdy wiesz, jak utworzyć własne środowisko usługi Time Series Insights, dowiedz się, jak utworzyć aplikację sieci web, która zużywa dane ze środowiska usługi Time Series Insights:

> [!div class="nextstepaction"]
> [Odczytywanie próbek wizualizacji hostowanego sdk klienta](https://tsiclientsample.azurewebsites.net/)
