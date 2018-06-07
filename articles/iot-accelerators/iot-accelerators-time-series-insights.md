---
title: Wizualizuj dane z informacjami dotyczącymi serii Azure czasu monitorowania zdalnego | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania środowiska czasu serii wgląd do Eksplorowanie i analizowanie danych w serii. czas rozwiązania monitorowania zdalnego.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: ed1a55c26ab1ef2aadb4906757205df0ca09d866
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655509"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Wizualizuj zdalnych danych monitorowania z informacjami dotyczącymi serii czasu

Operator może być konieczne dalsze rozszerzanie poza danych pole wizualizacji podał monitorowania zdalnego wstępnie skonfigurowane rozwiązanie. Nasze akcelerator rozwiązań udostępnia poza integracji pole z TSI. W tym instrukcje dowiesz się, jak skonfigurować czas serii wgląd do analizowania telemetrii urządzenia i wykrywania anomalii.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten jak to zrobić, potrzebne następujące elementy:

* [Wdrażanie zdalne monitorowanie wstępnie skonfigurowane rozwiązanie](iot-accelerators-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Tworzenie grupy odbiorców

Należy utworzyć dedykowanej grupy klientów w Centrum IoT służący do strumieniowego przesyłania danych do szczegółowych danych serii czasu.

> [!NOTE]
> Grupy konsumentów są używane przez aplikacje w celu pobierania danych z Centrum IoT Azure. Każdej grupy odbiorców umożliwia do pięciu konsumenci danych wyjściowych. Należy utworzyć nową grupę odbiorców co pięć output wychwytywanie i możesz tworzyć grupy konsumentów do 32.

1. W portalu Azure kliknij przycisk powłoki chmury.

1. Wykonaj następujące polecenie, aby utworzyć nową grupę odbiorców:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Tworzenie nowego środowiska Insights serii czasu

Azure Time Series Insights to w pełni zarządzana usługa związana z analizą, przechowywaniem i wizualizacją do zarządzania danymi szeregów czasowych w skali IoT w chmurze. Zapewnia wysoce skalowalny magazyn danych szeregów czasowych i umożliwia badanie oraz analizowanie miliardów zdarzeń przesyłanych strumieniowo z całego świata w ciągu sekund. Użyj Insights serii czas przechowywania i zarządzać terabajtów danych szeregu czasowego, Eksploruj i jednocześnie wizualizacji miliardów zdarzeń, przeprowadzenia analizy głównej przyczyny problemu i porównać wiele lokacji i zasobów.

1. Zaloguj się w [Portalu Azure](http://portal.azure.com/).

1. Wybierz **Utwórz zasób** > **Internetu rzeczy** > **Insights serii czas**.

    ![Nowych danych serii czasu](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. Aby utworzyć środowisko Insights serii czasu, użyj wartości w poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa środowiska | Poniższy zrzut ekranu używa nazwy **contorosrmtsi**. Po wykonaniu tego kroku, należy wybrać własną unikatową nazwę. |
    | Subskrypcja | Wybierz subskrypcję platformy Azure w listy rozwijanej. |
    | Grupa zasobów | **Utwórz nowe**. Używamy nazwy **ContosoRM**. |
    | Lokalizacja | Używamy **wschodnie stany USA**. Utworzyć środowisko, w tym samym regionie co rozwiązanie monitorowania zdalnego. |
    | SKU |**S1** |
    | Pojemność | **1** |
    | Przypnij do pulpitu nawigacyjnego | **Tak** |

    ![Tworzenia analiz serii czasu](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. Kliknij przycisk **Utwórz**. Może zająć chwilę dla środowiska, które ma zostać utworzony.

## <a name="create-event-source"></a>Tworzenie źródła zdarzeń

Utwórz nowe źródło zdarzeń nawiązać połączenia z Centrum IoT. Upewnij się, że używasz grupy odbiorców utworzone w poprzednich krokach. Czas serii Insights wymaga poszczególnych usług w celu zapewnienia dedykowanej grupy klientów nie używany przez inną usługę.

1. Przejdź do nowego środowiska serii czasu.

1. Po lewej stronie, wybierz **źródła zdarzeń**.

    ![Widok źródła zdarzeń](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. Kliknij pozycję **Add** (Dodaj).

    ![Dodawanie źródła zdarzeń](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. Aby skonfigurować Centrum IoT jako nowe źródło zdarzeń, użyj wartości w poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Źródło zdarzenia nazwy | Poniższy zrzut ekranu używa nazwy **contosorm iot koncentrator**. Po wykonaniu tego kroku, należy korzystać z własnej unikatowe nazwy. |
    | Element źródłowy | **IoT Hub** |
    | Opcja importu | **Użyj Centrum IoT z dostępnych subskrypcji** |
    | Identyfikator subskrypcji | Wybierz subskrypcję platformy Azure w listy rozwijanej. |
    | Nazwa centrum iot | **contosorma57a6**. Użyj nazwy Centrum IoT z rozwiązania do monitorowania zdalnego. |
    | Nazwa zasad Centrum iot | **iothubowner** upewnij się, używane są zasady właściciela. |
    | Klucz zasad Centrum iot | To pole jest wypełniane automatycznie. |
    | Grupy odbiorców Centrum iot | **timeseriesinsights** |
    | Format serializacji zdarzeń | **JSON**     | Nazwa właściwości sygnatury czasowej | Pozostaw puste |

    ![Tworzenie źródła zdarzeń](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. Kliknij przycisk **Utwórz**.

> [!NOTE]
> Jeśli potrzebujesz przyznawania dostępu użytkownikom do Eksploratora Insights serii czasu, można użyć te kroki, aby [udzielić dostępu do danych](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Eksplorator usługi Time Series Insights

Eksplorator Insights serii czasu jest aplikacja sieci web, która pomaga tworzyć wizualizacje danych.

1. Wybierz **omówienie** kartę.

1. Kliknij przycisk **przejdź do środowiska**, która spowoduje otwarcie aplikacji sieci web explorer Insights serii czasu.

    ![Eksplorator usługi Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. W panelu wyboru czasu, wybierz **ostatnie 12 godzin** z szybkie razy i kliknąć menu **wyszukiwania**.

    ![Serie czasu Insights Eksplorator wyszukiwania](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. W panelu warunki po lewej stronie, wybierz wartość miary **temperatury** i podzielone przez wartości **Centrum iothub połączenia urządzenie id**.

    ![Serie czasu Insights Eksploratora zapytań](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. Kliknij prawym przyciskiem myszy na wykresie i wybierz **Eksploruj zdarzenia**.

    ![Zdarzenia Explorer wgląd w czasie serii](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. Zdarzenia są renderowane w siatce w formacie tabelarycznym.

    ![Czas serii Insights Explorer tabeli](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. Kliknij przycisk Widok perspektywy.

    ![Czas serii Insights Explorer perspektywy](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kliknij przycisk **Dodaj** można utworzyć nowego zapytania w perspektywie.

    ![Eksplorator Insights serii czasu dodać zapytania](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. Wybierz czas szybkie **ostatnie 12 godzin**, miary **wilgotności** i podział według z **Centrum iothub połączenia urządzenie id**.

    ![Serie czasu Insights Eksploratora zapytań](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. Kliknij przycisk Wyświetl perspektywy, aby wyświetlić pulpit nawigacyjny metryki urządzenia.

    ![Czas pulpit nawigacyjny Explorer szczegółowych informacji dla serii](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Eksploruj i zapytania na danych w Eksploratorze czasu serii Insights, zobacz [Eksplorator Azure czas serii Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-dashboard.png).
