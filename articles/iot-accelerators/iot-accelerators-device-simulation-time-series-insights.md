---
title: Wizualizowanie danych telemetrycznych z symulacji urządzenia za pomocą usługi Time Series Insights — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować środowisko usługi Time Series Insights można badać i analizować dane telemetryczne wygenerowane przez akcelerator rozwiązań symulacji urządzenia.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 5d20adc11e0d679e12fd060e719593a50180db8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65834875"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Użyj usługi Time Series Insights, aby wizualizować dane telemetryczne wysyłane z akceleratora rozwiązań symulacji urządzenia

Symulacja urządzenia akceleratora rozwiązań pozwala wygenerować dane telemetryczne z symulowanych urządzeń w celu przetestowania rozwiązania IoT. Ten poradnik pokazuje, jak wizualizować i analizować symulowanych danych telemetrycznych, przy użyciu środowiska usługi Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, konieczne jest aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Kroki opisane w tym przewodniku przyjęto założenie, że udało Ci się wdrożyć akcelerator rozwiązań symulacji urządzenia do subskrypcji platformy Azure. Jeśli nie zostały wdrożone akcelerator rozwiązań, wykonaj kroki opisane w [Wdróż i uruchom rozwiązania oparte na chmurze urządzeniami symulacji](quickstart-device-simulation-deploy.md) Szybki Start.

W tym artykule założono, nazywa Twój akcelerator rozwiązań **symulacji contoso**. Zastąp **symulacji contoso** o nazwie Twój akcelerator rozwiązań, jak wykonać następujące kroki.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Utwórz grupę odbiorców

Musisz utworzyć dedykowanej grupy klientów w usłudze IoT hub do strumieni telemetrii do usługi Time Series Insights. Źródło zdarzenia w usłudze Time Series Insights powinny mieć wyłącznie do Twojego użytku grupy konsumentów usługi IoT Hub.

Następujące kroki umożliwiają wiersza polecenia platformy Azure w usłudze Azure Cloud Shell Utwórz grupę odbiorców:

1. Usługa IoT hub jest jednym z kilku zasobów utworzone podczas wdrażania akcelerator rozwiązań symulacji urządzenia. Wykonaj następujące polecenie Znajdź nazwę Centrum IoT hub — należy pamiętać użyć nazwy Twój akcelerator rozwiązań:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Usługa IoT hub jest zasób typu **Microsoft.Devices/IotHubs**.

1. Dodaj grupę konsumentów o nazwie **devicesimulationtsi** do koncentratora. W poniższym poleceniu należy użyć nazwy akceleratorze Centrum i rozwiązania:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Możesz teraz zamknąć usługę Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Utwórz nowe środowisko usługi Time Series Insights

[Usługa Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) jest w pełni zarządzana usługa analizy, magazynowania i wizualizacji związanych z zarządzaniem danymi szeregów czasowych w skali IoT w chmurze. Aby utworzyć nowego środowiska usługi Time Series Insights:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz **Utwórz zasób** > **Internet of Things** > **usługi Time Series Insights**:

    ![Nowe usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Aby utworzyć środowisko usługi Time Series Insights w tej samej grupie zasobów, co Twój akcelerator rozwiązań, należy użyć wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa środowiska | Poniższy zrzut ekranu używa nazwy **Contoso TSI**. Wybierz swoją własną unikatową nazwę, po wykonaniu tego kroku. |
    | Subskrypcja | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Grupa zasobów | **Symulacja contoso**. Użyj nazwy Twój akcelerator rozwiązań. |
    | Lokalizacja | W tym przykładzie użyto **wschodnie stany USA**. Utwórz swoje środowisko, w tym samym regionie, co akceleratorze symulacji urządzenia. |
    | SKU |**S1** |
    | Pojemność | **1** |

    ![Create Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Dodawanie usługi Time Series Insights środowisko do tej samej grupie zasobów jako akcelerator rozwiązań oznacza, że jest on usuwany po usunięciu akcelerator rozwiązań.

1. Kliknij pozycję **Utwórz**. Może upłynąć kilka minut, aż środowisko, które ma zostać utworzony.

## <a name="create-event-source"></a>Tworzenie źródła zdarzeń

Utwórz nowe źródło zdarzeń, aby nawiązać połączenie z Centrum IoT hub. Użyj grupy odbiorców, który został utworzony w poprzednich krokach. Źródła zdarzeń usługi Time Series Insights wymaga dedykowanej grupy klientów nieużywany przez inną usługę.

1. W witrynie Azure portal przejdź do Twojego nowego środowiska usługi Time Series.

1. Po lewej stronie, kliknij przycisk **źródła zdarzeń**:

    ![Widok źródła zdarzeń](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Kliknij przycisk **Dodaj**:

    ![Dodawanie źródła zdarzeń](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Aby skonfigurować usługi IoT hub jako nowe źródło zdarzeń, należy użyć wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Źródło zdarzenia nazwy | Poniższy zrzut ekranu używa nazwy **contoso-iot-hub**. Po wykonaniu tego kroku, należy użyć swoją własną unikatową nazwę. |
    | source | **IoT Hub** |
    | Opcja importu | **Użyj usługi IoT Hub z dostępnych subskrypcji** |
    | Identyfikator subskrypcji | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Nazwa centrum iot | **Contoso simulation7d894**. Użyj nazwy Centrum IoT hub z Twój akcelerator rozwiązań symulacji urządzenia. |
    | Nazwa zasad Centrum iot | **iothubowner** |
    | Klucz zasad Centrum iot | To pole jest wypełniane automatycznie. |
    | Grupa konsumentów Centrum iot | **devicesimulationtsi** |
    | Format serializacji zdarzeń | **JSON** |
    | Nazwa właściwości sygnatury czasowej | Pozostaw puste |

    ![Tworzenie źródła zdarzeń](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Kliknij pozycję **Utwórz**.

> [!NOTE]
> Możesz [przyznawania dostępu użytkownikom](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) do Eksploratora usługi Time Series Insights.

## <a name="start-a-simulation"></a>Rozpocznij symulację

Przed skorzystaniem z Eksploratora usługi Time Series Insights, należy skonfigurować akcelerator rozwiązań symulacji urządzenia, aby wygenerować dane telemetryczne. Poniższy zrzut ekranu przedstawia uruchamianie symulacji z 10 Chłodnica urządzeń:

![Uruchamianie symulacji urządzenia](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Eksplorator usługi Time Series Insights

Eksplorator usługi Time Series Insights to aplikację internetową, którą można użyć w celu wizualizowania danych telemetrycznych.

1. W witrynie Azure portal wybierz usługi Time Series Insights **Przegląd** kartę.

1. Aby otworzyć aplikację sieci web w Eksploratorze usługi Time Series Insights, kliknij przycisk **przejdź do środowiska**:

    ![Eksplorator usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. W panelu wyboru czasu, wybierz **ciągu ostatnich 30 minut** z szybkiego razy menu i kliknij przycisk **wyszukiwania**:

    ![Czas wyszukiwania Eksploratora Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. Ten panel terminów po lewej stronie wybierz **temperatury** jako **miary** i **iothub-— urządzenia — identyfikator połączenia** jako **podziału przez** wartość:

    ![Czas Series Insights Eksplorator zapytań](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Kliknij prawym przyciskiem myszy wykres i wybierz pozycję **Eksploruj zdarzenia**:

    ![Zdarzenia explorer Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Przedstawia dane zdarzeń w siatce:

    ![Tabela explorer Series Insights czasu](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Kliknij przycisk Wyświetl perspektywy:

    ![Czas Series Insights explorer perspektywy](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kliknij przycisk **+** można dodać nowe zapytanie do perspektywy:

    ![Eksplorator usługi Time Series Insights Dodaj zapytanie](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Wybierz **ciągu ostatnich 30 minut** jako przedział czasu **wilgotności** jako **miary**, i **iothub-— urządzenia — identyfikator połączenia** jako **Podziału przez** wartość:

    ![Czas Series Insights Eksplorator zapytań](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Kliknij przycisk Wyświetl perspektywy, aby wyświetlić pulpit nawigacyjny telemetrii urządzenia:

    ![Pulpit nawigacyjny explorer Series Insights czasu](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz dalszego zbadania, należy pozostawić akcelerator rozwiązań wdrożonych.

Jeśli akcelerator rozwiązań nie są już potrzebne, usuń go z [Aprowizowane rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard) strony, wybierając ją, a następnie klikając pozycję **Usuń rozwiązanie**.

Jeśli środowisko usługi Time Series Insights została dodana do grupy zasobów akcelerator rozwiązań, zostanie on automatycznie usunięty po usunięciu akcelerator rozwiązań. W przeciwnym razie należy ręcznie usunąć środowisko usługi Time Series Insights w witrynie Azure portal.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak i wysyłania zapytań dotyczących danych w Eksploratorze usługi Time Series Insights, zobacz [Eksploratora usługi Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).
