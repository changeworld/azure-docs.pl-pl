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
ms.openlocfilehash: aea02cbde32d9485bd49ec39a6f300021c6ef927
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597702"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Użyj usługi Time Series Insights, aby wizualizować dane telemetryczne wysyłane z akceleratora rozwiązań symulacji urządzenia

Symulacja urządzenia akceleratora rozwiązań pozwala wygenerować dane telemetryczne z symulowanych urządzeń w celu przetestowania rozwiązania IoT. Ten poradnik pokazuje, jak wizualizować i analizować symulowanych danych telemetrycznych, przy użyciu środowiska usługi Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, konieczne jest aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Kroki opisane w tym przewodniku przyjęto założenie, że udało Ci się wdrożyć akcelerator rozwiązań symulacji urządzenia do subskrypcji platformy Azure. Jeśli nie zostały wdrożone akcelerator rozwiązań, wykonaj kroki opisane w [Wdróż i uruchom rozwiązania oparte na chmurze urządzeniami symulacji](quickstart-device-simulation-deploy.md) Szybki Start.

W tym artykule założono, nazywa Twój akcelerator rozwiązań **symulacji contoso**. Zastąp **symulacji contoso** o nazwie Twój akcelerator rozwiązań, jak wykonać następujące kroki.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

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

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak i wysyłania zapytań dotyczących danych w Eksploratorze usługi Time Series Insights, zobacz [Eksploratora usługi Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).
