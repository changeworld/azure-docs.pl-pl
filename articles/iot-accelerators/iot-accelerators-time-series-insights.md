---
title: Wizualizuj dane przy użyciu usługi Azure Time Series Insights monitorowania zdalnego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować środowisko usługi Time Series Insights Eksplorowanie i analizowanie danych szeregów czasowych rozwiązania do zdalnego monitorowania.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 10617c129212d8196897af750c02647f0086c8e5
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185894"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Wizualizuj dane monitorowania zdalnego, za pomocą usługi Time Series Insights

Operator może chcieć dalsze rozszerzanie poza pola danych wizualizacji dostarczonych przez zdalne monitorowanie wstępnie skonfigurowane rozwiązanie. Nasze akcelerator rozwiązań udostępnia poza integracji z usługą TSI. W tym instruktażu dowiesz się, jak skonfigurować usługi Time Series Insights do analizowania danych telemetrycznych z urządzenia i wykrywania anomalii.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć Instruktaż, potrzebne następujące elementy:

* [Wdrażanie rozwiązania do zdalnego monitorowania, wstępnie skonfigurowane](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Utwórz grupę odbiorców

Należy utworzyć dedykowanej grupy klientów w usłudze IoT hub ma być używany do przesyłania strumieniowego danych do usługi Time Series Insights.

> [!NOTE]
> Grupy konsumentów są używane przez aplikacje do pobierania danych z usługi Azure IoT Hub. Każdej grupy odbiorców zezwala na maksymalnie pięciu odbiorców danych wyjściowych. Należy utworzyć nową grupę odbiorców, co pięć ujścia w danych wyjściowych i można utworzyć maksymalnie 32 grup odbiorców.

1. W witrynie Azure portal kliknij przycisk Cloud Shell.

1. Wykonaj następujące polecenie, aby utworzyć nową grupę odbiorców:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Utwórz nowe środowisko usługi Time Series Insights

Azure Time Series Insights to w pełni zarządzana usługa związana z analizą, przechowywaniem i wizualizacją do zarządzania danymi szeregów czasowych w skali IoT w chmurze. Zapewnia wysoce skalowalny magazyn danych szeregów czasowych i umożliwia badanie oraz analizowanie miliardów zdarzeń przesyłanych strumieniowo z całego świata w ciągu sekund. Użyj usługi Time Series Insights, przechowywać i zarządzać terabajty danych szeregów czasowych, eksplorować i wizualizować miliardy zdarzeń jednocześnie, przeprowadzać analizy głównej przyczyny oraz porównywać wiele lokacji i zasobów.

1. Zaloguj się w [Portalu Azure](http://portal.azure.com/).

1. Wybierz **Utwórz zasób** > **Internet of Things** > **usługi Time Series Insights**.

    ![Nowe usługi Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. Aby utworzyć środowisko usługi Time Series Insights, należy użyć wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa środowiska | Poniższy zrzut ekranu używa nazwy **contorosrmtsi**. Wybierz swoją własną unikatową nazwę, po wykonaniu tego kroku. |
    | Subskrypcja | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Grupa zasobów | **Utwórz nową**. Używamy nazwy **ContosoRM**. |
    | Lokalizacja | Używamy **wschodnie stany USA**. Utwórz swoje środowisko, w tym samym regionie jako rozwiązania do zdalnego monitorowania. |
    | SKU |**S1** |
    | Pojemność | **1** |
    | Przypnij do pulpitu nawigacyjnego | **Tak** |

    ![Tworzenie usługi Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. Kliknij przycisk **Utwórz**. Może potrwać chwilę dla środowiska, które ma zostać utworzony.

## <a name="create-event-source"></a>Tworzenie źródła zdarzeń

Utwórz nowe źródło zdarzeń, aby nawiązać połączenie z Centrum IoT hub. Należy upewnić się, że grupa odbiorców, utworzony w poprzednich krokach. Usługa Time Series Insights wymaga każdej usługi, aby mieć dedykowanej grupy klientów nieużywany przez inną usługę.

1. Przejdź do nowego środowiska serii czasu.

1. Po lewej stronie, wybierz **źródła zdarzeń**.

    ![Widok źródła zdarzeń](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. Kliknij pozycję **Add** (Dodaj).

    ![Dodawanie źródła zdarzeń](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. Aby skonfigurować usługi IoT hub jako nowe źródło zdarzeń, należy użyć wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Źródło zdarzenia nazwy | Poniższy zrzut ekranu używa nazwy **contosorm-iot-hub**. Po wykonaniu tego kroku, należy użyć swoją własną unikatową nazwę. |
    | Element źródłowy | **IoT Hub** |
    | Opcja importu | **Użyj usługi IoT Hub z dostępnych subskrypcji** |
    | Identyfikator subskrypcji | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Nazwa centrum iot | **contosorma57a6**. Użyj nazwy Centrum IoT hub z rozwiązania do monitorowania zdalnego. |
    | Nazwa zasad Centrum iot | **iothubowner** upewnij się, zasady, stosowane są zasady właściciela. |
    | Klucz zasad Centrum iot | To pole jest wypełniane automatycznie. |
    | Grupa konsumentów Centrum iot | **timeseriesinsights** |
    | Format serializacji zdarzeń | **JSON**     | Nazwa właściwości sygnatury czasowej | Pozostaw puste |

    ![Tworzenie źródła zdarzeń](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. Kliknij przycisk **Utwórz**.

> [!NOTE]
> Jeśli zachodzi potrzeba przyznawania dostępu użytkownikom do Eksploratora usługi Time Series Insights, można następujące kroki, aby [przyznawanie dostępu do danych](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Eksplorator usługi Time Series Insights

Eksplorator usługi Time Series Insights jest aplikacją sieci web, która pomoże Ci w utworzeniu wizualizacji danych.

1. Wybierz **Przegląd** kartę.

1. Kliknij przycisk **przejdź do środowiska**, który spowoduje otwarcie aplikacji sieci web w Eksploratorze usługi Time Series Insights.

    ![Eksplorator usługi Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. W panelu wyboru czasu, wybierz **ostatnie 12 godzin** z szybkiego razy menu i kliknij przycisk **wyszukiwania**.

    ![Wyszukiwanie Explorer Insights szeregów czasowych](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. W panelu warunki po lewej stronie, wybierz wartość miary **temperatury** i podział według wartości **iothub-— urządzenia — identyfikator połączenia**.

    ![Zapytanie Explorer Insights szeregów czasowych](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. Kliknij prawym przyciskiem myszy wykres i wybierz pozycję **Eksploruj zdarzenia**.

    ![Zdarzenia Explorer Insights serii czasu](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. Zdarzenia są renderowane w siatce w formacie tabelarycznym.

    ![Tabela Explorer Insights serii czasu](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. Kliknij przycisk Wyświetl perspektywy.

    ![Czas serii Insights Explorer perspektywy](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kliknij przycisk **Dodaj** Aby utworzyć nowe zapytanie w perspektywie.

    ![Eksplorator usługi Time Series Insights Dodaj zapytanie](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. Wybierz skrócić czas z **ostatnie 12 godzin**, miara **wilgotności** i podziału z **iothub-— urządzenia — identyfikator połączenia**.

    ![Zapytanie Explorer Insights szeregów czasowych](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. Kliknij przycisk Wyświetl perspektywy, aby wyświetlić pulpit nawigacyjny metryk urządzenia.

    ![Pulpit nawigacyjny o Eksploratorze czas serii szczegółowych informacji](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak i wysyłania zapytań dotyczących danych w Eksploratorze usługi Time Series Insights, zobacz [Eksploratora usługi Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
