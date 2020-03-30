---
title: Monitorowanie zasobu platformy Azure za pomocą usługi Azure Monitor
description: Dowiedz się, jak zbierać i analizować dane dla zasobu platformy Azure w usłudze Azure Monitor.
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: c4b80e62d3800392b847a411dfc66c3278e72bba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77661868"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Szybki start: monitorowanie zasobu platformy Azure za pomocą usługi Azure Monitor
[Usługa Azure Monitor](../overview.md) rozpoczyna zbieranie danych z zasobów platformy Azure w momencie ich utworzenia. Ten przewodnik szybki start zawiera krótki przewodnik po danych, które są automatycznie zbierane dla zasobu i jak wyświetlić go w witrynie Azure portal dla określonego zasobu. Później można dodać konfigurację do zbierania dodatkowych danych i można przejść do menu usługi Azure Monitor, aby użyć tych samych narzędzi, aby uzyskać dostęp do danych zebranych dla wszystkich zasobów w ramach subskrypcji.

Aby uzyskać bardziej szczegółowe opisy danych monitorowania zebranych z zasobów platformy Azure, zobacz [Monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor.](../insights/monitor-azure-resource.md)


## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com). 


## <a name="overview-page"></a>Strona przeglądowa
Wiele usług będzie zawierać dane monitorowania na ich stronie **przegląd** jako szybki rzut oka na ich działanie. Zazwyczaj będzie to oparte na podzbiorze metryk platformy przechowywanych w metrykach usługi Azure Monitor.

1. Znajdź zasób platformy Azure w ramach subskrypcji.
2. Przejdź do **strony Przegląd** i zanotuj, czy są wyświetlane jakieś dane dotyczące wydajności. Te dane zostaną dostarczone przez usługę Azure Monitor. Poniższy przykład przedstawia stronę **Przegląd** konta usługi Azure storage i widać, że wyświetlanych jest wiele metryk.

    ![Strona przeglądowa](media/quick-monitor-azure-resource/overview.png)

3. Możesz kliknąć dowolny z wykresów, aby otworzyć dane w eksploratorze metryk, który jest opisany poniżej.

## <a name="view-the-activity-log"></a>Wyświetlanie dziennika aktywności
Dziennik działania zapewnia wgląd w operacje na każdym zasobie platformy Azure w ramach subskrypcji. Będzie to obejmować takie informacje, jak podczas tworzenia lub modyfikowanie zasobu, gdy zadanie jest uruchamiane lub gdy wystąpi określona operacja.

1. U góry menu zasobu wybierz pozycję **Dziennik aktywności**.
2. Bieżący filtr jest ustawiony na zdarzenia związane z zasobem. Jeśli nie widzisz żadnych zdarzeń, spróbuj zmienić **timespan,** aby zwiększyć zakres czasu.

    ![Dziennik aktywności](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Jeśli chcesz zobaczyć zdarzenia z innych zasobów w subskrypcji, zmień kryteria w filtrze lub nawet usuń właściwości filtru.

    ![Dziennik aktywności](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Wyświetlanie metryk
Metryki są wartościami liczbowymi, które opisują niektóre aspekty zasobu w określonym czasie. Usługa Azure Monitor automatycznie zbiera metryki platformy w odstępach jednominutowych ze wszystkich zasobów platformy Azure. Te metryki można wyświetlić za pomocą Eksploratora metryk.

1. W sekcji **Monitorowanie** w menu zasobu wybierz pozycję **Metryki**. Spowoduje to otwarcie eksploratora metryk z zakresem ustawionym na zasób.
2. Kliknij **pozycję Dodaj metrykę,** aby dodać metrykę do wykresu.
   
   ![Eksplorator metryk](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Wybierz **metrykę** z listy rozwijanej, a następnie **agregację**. Definiuje to, jak zebrane wartości będą pobierane próbki w każdym przedziale czasu.

    ![Eksplorator metryk](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Kliknij **pozycję Dodaj metrykę,** aby dodać do wykresu dodatkowe kombinacje metryki i agregacji.

    ![Eksplorator metryk](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start wyświetlony dziennik aktywności i metryki dla zasobu platformy Azure, które są automatycznie zbierane przez usługę Azure Monitor. Dzienniki zasobów zapewniają wgląd w szczegółowe działanie zasobu, ale muszą być skonfigurowane w celu zebrania. Przejdź do samouczka do zbierania dzienników zasobów w obszarze roboczym usługi Log Analytics, gdzie mogą być analizowane przy użyciu zapytań dziennika.

> [!div class="nextstepaction"]
> [Zbieranie i analizowanie dzienników zasobów za pomocą usługi Azure Monitor](tutorial-resource-logs.md)
