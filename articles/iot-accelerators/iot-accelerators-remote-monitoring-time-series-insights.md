---
title: Wizualizuj zdalnego monitorowania telemetrii w usłudze Azure Time Series Insights | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować środowisko usługi Time Series Insights można badać i analizować dane telemetryczne wygenerowane przez akcelerator rozwiązania monitorowania zdalnego.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 02b3afbc62be7a7a9c396de888378d762405248a
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2018
ms.locfileid: "43383281"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-remote-monitoring-solution-accelerator"></a>Użyj usługi Time Series Insights, aby wizualizować dane telemetryczne wysyłane z akceleratora rozwiązania monitorowania zdalnego

Operator można rozszerzyć możliwości wizualizacji danych poza pole akceleratora rozwiązania monitorowania zdalnego. W tym przewodniku dowiesz się, jak wizualizować i analizować dane telemetryczne wysyłane do akceleratora rozwiązań zdalne monitorowanie za pomocą środowiska usługi Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, konieczne jest aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Kroki opisane w tym przewodniku przyjęto założenie, że udało Ci się wdrożyć akcelerator rozwiązań zdalnego monitorowania do subskrypcji platformy Azure. Jeśli nie zostały wdrożone akcelerator rozwiązań, wykonaj kroki opisane w [wdrażania i uruchamiania oparte na chmurze rozwiązania do monitorowania zdalnego](quickstart-remote-monitoring-deploy.md) Szybki Start.

W tym artykule założono, nazywa Twój akcelerator rozwiązań **symulacji contoso**. Zastąp **symulacji contoso** o nazwie Twój akcelerator rozwiązań, jak wykonać następujące kroki.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="time-series-insights-explorer"></a>Eksplorator usługi Time Series Insights

Eksplorator usługi Time Series Insights to aplikację internetową, którą można użyć w celu wizualizowania danych telemetrycznych.

1. W witrynie Azure portal wybierz usługi Time Series Insights **Przegląd** kartę.

1. Aby otworzyć aplikację sieci web w Eksploratorze usługi Time Series Insights, kliknij przycisk **przejdź do środowiska**:

    ![Eksplorator usługi Time Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-environment.png)

1. W panelu wyboru czasu, wybierz **ciągu ostatnich 30 minut** z szybkiego razy menu i kliknij przycisk **wyszukiwania**:

    ![Czas wyszukiwania Eksploratora Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-search-time.png)

1. Ten panel terminów po lewej stronie wybierz **temperatury** jako **miary** i **iothub-— urządzenia — identyfikator połączenia** jako **podziału przez** wartość:

    ![Czas Series Insights Eksplorator zapytań](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-query1.png)

1. Kliknij prawym przyciskiem myszy wykres i wybierz pozycję **Eksploruj zdarzenia**:

    ![Zdarzenia explorer Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-explore-events.png)

1. Przedstawia dane zdarzeń w siatce:

    ![Tabela explorer Series Insights czasu](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-table.png)

1. Kliknij przycisk Wyświetl perspektywy:

    ![Czas Series Insights explorer perspektywy](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kliknij przycisk **+** można dodać nowe zapytanie do perspektywy:

    ![Eksplorator usługi Time Series Insights Dodaj zapytanie](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-new-query.png)

1. Wybierz **ciągu ostatnich 30 minut** jako przedział czasu **wilgotności** jako **miary**, i **iothub-— urządzenia — identyfikator połączenia** jako **Podziału przez** wartość:

    ![Czas Series Insights Eksplorator zapytań](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-query2.png)

1. Kliknij przycisk Wyświetl perspektywy, aby wyświetlić pulpit nawigacyjny telemetrii urządzenia:

    ![Pulpit nawigacyjny explorer Series Insights czasu](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-dashboard.png)

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak i wysyłania zapytań dotyczących danych w Eksploratorze usługi Time Series Insights, zobacz [Eksploratora usługi Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
