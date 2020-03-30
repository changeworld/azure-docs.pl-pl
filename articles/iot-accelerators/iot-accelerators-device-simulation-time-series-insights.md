---
title: Wizualizuj symulowane dane telemetryczne za pomocą usługi Time Series Insights — Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować środowisko usługi Time Series Insights do eksplorowania i analizowania danych telemetrycznych generowanych przez akcelerator rozwiązań device simulation.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 2bbd7911a40d6a256d478e2533ad2469b8fd6973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889345"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Aplikacja Time Series Insights umożliwia wizualizację danych telemetrycznych wysyłanych z akceleratora rozwiązania Symulacja urządzenia

Akcelerator rozwiązań Device Simulation umożliwia generowanie danych telemetrycznych z symulowanych urządzeń w celu przetestowania rozwiązań IoT. W tym przewodniku przedstawiono sposób wizualizacji i analizowania symulowanych danych telemetrycznych przy użyciu środowiska usługi Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz aktywnej subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

Kroki opisane w tym przewodniku można założyć, że wdrożono akcelerator rozwiązań symulacji urządzeń do subskrypcji platformy Azure. Jeśli akcelerator rozwiązań nie został wdrożony, wykonaj kroki opisane w przewodniku Wdrażanie i uruchom szybki start [rozwiązania do symulacji urządzeń w chmurze.](quickstart-device-simulation-deploy.md)

W tym artykule przyjęto założenie, że nazwa akceleratora rozwiązań to **contoso-simulation**. Zamień **contoso-simulation** na nazwę akceleratora rozwiązań podczas wykonywania następujących kroków.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Tworzenie grupy odbiorców

Należy utworzyć dedykowaną grupę odbiorców w centrum IoT hub do przesyłania strumieniowego danych telemetrycznych do usługi Time Series Insights. Źródło zdarzeń w usłudze Time Series Insights powinno mieć wyłączne użycie grupy konsumentów usługi IoT Hub.

Następujące kroki używają interfejsu wiersza polecenia platformy Azure w usłudze Azure Cloud Shell do utworzenia grupy konsumentów:

1. Centrum IoT jest jednym z kilku zasobów utworzonych podczas wdrażania akceleratora rozwiązań Device Simulation. Wykonaj następujące polecenie znajdź nazwę centrum IoT hub — pamiętaj, aby użyć nazwy akceleratora rozwiązań:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Centrum IoT jest zasobem typu **Microsoft.Devices/IotHubs**.

1. Dodaj grupę odbiorców o nazwie **devicesimulationtsi** do koncentratora. W poniższym poleceniu użyj nazwy koncentratora i akceleratora rozwiązań:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Teraz można zamknąć usługę Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Tworzenie nowego środowiska usługi Time Series Insights

[Usługa Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) to w pełni zarządzana usługa analizy, magazynu i wizualizacji do zarządzania danymi szeregów czasowych w skali IoT w chmurze. Aby utworzyć nowe środowisko usługi Time Series Insights:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Wybierz **pozycję Utwórz zasób** > **Wgląd w szeregi czasowe Internet** > **rzeczy:**

    ![Nowe informacje o szeregach czasowych](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Aby utworzyć środowisko usługi Time Series Insights w tej samej grupie zasobów co akcelerator rozwiązań, należy użyć wartości w poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa środowiska | Poniższy zrzut ekranu używa nazwy **Contoso-TSI**. Po wykonaniu tego kroku wybierz własną unikatową nazwę. |
    | Subskrypcja | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Grupa zasobów | **contoso-symulacja**. Użyj nazwy akceleratora rozwiązań. |
    | Lokalizacja | W tym przykładzie użyto **wschodnich stanów USA**. Tworzenie środowiska w tym samym regionie co akcelerator symulacji urządzenia. |
    | SKU |**S1** |
    | Pojemność | **1** |

    ![Tworzenie statystyk szeregów czasowych](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Dodanie środowiska usługi Time Series Insights do tej samej grupy zasobów co akcelerator rozwiązań oznacza, że jest on usuwany po usunięciu akceleratora rozwiązania.

1. Kliknij przycisk **Utwórz**. Utworzenie środowiska może potrwać kilka minut.

## <a name="create-event-source"></a>Tworzenie źródła zdarzeń

Utwórz nowe źródło zdarzeń, aby połączyć się z centrum IoT Hub. Użyj grupy odbiorców utworzonej w poprzednich krokach. Źródło zdarzeń usługi Time Series Insights wymaga dedykowanej grupy odbiorców, która nie jest używana przez inną usługę.

1. W witrynie Azure portal przejdź do nowego środowiska szeregów czasowych.

1. Po lewej stronie kliknij pozycję **Źródła zdarzeń**:

    ![Wyświetlanie źródeł zdarzeń](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Kliknij **przycisk Dodaj**:

    ![Dodaj źródło zdarzeń](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Aby skonfigurować centrum IoT hub jako nowe źródło zdarzeń, użyj wartości w poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa źródła zdarzenia | Poniższy zrzut ekranu używa nazwy **contoso-iot-hub**. Użyj własnej unikatowej nazwy po wykonaniu tego kroku. |
    | Element źródłowy | **Iot** |
    | Opcja importu | **Korzystanie z Usługi IoT Hub z dostępnych subskrypcji** |
    | Identyfikator subskrypcji | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Nazwa centrum Iot | **contoso-simulation7d894**. Użyj nazwy centrum IoT hub z akceleratora rozwiązań Device Simulation. |
    | Nazwa zasady IoT Hub | **iothubowner** |
    | Klucz zasad centrum Iot | To pole jest wypełniane automatycznie. |
    | Grupa użytkowników IoT Hub | **devicesimulationtsi** |
    | Format serializacji zdarzeń | **Json** |
    | Nazwa właściwości znacznika czasu | Pozostaw puste |

    ![Utwórz źródło zdarzeń](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Kliknij przycisk **Utwórz**.

> [!NOTE]
> Można [udzielić dodatkowym użytkownikom dostępu](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) do Eksploratora Usługi Time Series Insights.

## <a name="start-a-simulation"></a>Rozpoczynanie symulacji

Przed użyciem eksploratora usługi Time Series Insights należy skonfigurować akcelerator rozwiązań symulacji urządzeń w celu wygenerowania niektórych danych telemetrycznych. Poniższy zrzut ekranu przedstawia symulację biegu z 10 urządzeniami agregatu chłodniczego:

![Symulacja uruchomionego urządzenia](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Eksplorator usługi Time Series Insights

Lordyrii usługi Time Series Insights to aplikacja sieci web, której można użyć do wizualizacji danych telemetrycznych.

1. W witrynie Azure portal wybierz kartę Przegląd statystyk szeregów **czasowych.**

1. Aby otworzyć aplikację internetową Eksploratora usługi Time Series Insights, kliknij pozycję **Przejdź do środowiska:**

    ![Eksplorator usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. W panelu wyboru czasu wybierz z menu Szybkie godziny pozycję **Ostatnie 30 minut** i kliknij przycisk **Wyszukaj**:

    ![Wyszukiwanie w eksploratorze usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. W panelu warunki po lewej stronie wybierz **opcję Temperatura** jako **miara** i **identyfikator iothub-connection-device jako** wartość Podziel **według:**

    ![Kwerenda eksploratora usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Kliknij prawym przyciskiem myszy wykres i wybierz **pozycję Eksploruj wydarzenia:**

    ![Zdarzenia eksploratora usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Dane zdarzenia są wyświetlane w siatce:

    ![Tabela eksploratora usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Kliknij przycisk widoku perspektywy:

    ![Perspektywa eksploracie usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kliknij, **+** aby dodać nową kwerendę do perspektywy:

    ![Explorer UsługiLorytów czasowych dodaj zapytanie](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Wybierz **opcję Ostatnie 30 minut** jako przedział czasu, **Wilgotność** jako **miara**i **iothub-connection-device-id** jako wartość **Podziel według:**

    ![Kwerenda eksploratora usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Kliknij przycisk widoku perspektywy, aby wyświetlić pulpit nawigacyjny telemetrii urządzenia:

    ![Pulpit nawigacyjny eksploratora usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz zbadać dalej, pozostawić akcelerator rozwiązania wdrożone.

Jeśli akcelerator rozwiązań nie jest już potrzebny, usuń go ze strony [Aproweuj rozwiązania,](https://www.azureiotsolutions.com/Accelerators#dashboard) zaznaczając go, a następnie klikając pozycję **Usuń rozwiązanie**.

Jeśli środowisko usługi Time Series Insights zostało dodane do grupy zasobów akceleratora rozwiązań, zostanie ono automatycznie usunięte po usunięciu akceleratora rozwiązań. W przeciwnym razie należy ręcznie usunąć środowisko usługi Time Series Insights z witryny Azure portal.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat eksplorowania i wykonywania zapytań o dane w Eksploratorze usługi Time Series Insights, zobacz [Eksplorator usługi Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).
