---
title: Monitorowanie aplikacji platformy Docker w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Liczniki perf platformy Docker, zdarzenia i wyjątki mogą być wyświetlane w usłudze Application Insights wraz z telemetrią z konteneryzowanych aplikacji.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 5ff3d6a2b31425a3fb6857c8c6aed45f7ef18d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669611"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Monitorowanie aplikacji platformy Docker w usłudze Application Insights (przestarzałe)

> [!NOTE]
> To rozwiązanie zostało przestarzałe. Aby dowiedzieć się więcej o naszych bieżących inwestycjach w monitorowanie kontenerów, zalecamy sprawdzenie [usługi Azure Monitor dla kontenerów.](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)

Zdarzenia cyklu życia i liczniki wydajności z kontenerów [platformy Docker](https://www.docker.com/) można nakreślić w usłudze Application Insights. Zainstaluj obraz [usługi Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) w kontenerze na hoście i wyświetli liczniki wydajności dla hosta, a także dla innych obrazów.

Za pomocą platformy Docker możesz dystrybuować aplikacje w lekkich kontenerach wraz ze wszystkimi zależnościami. Będą one uruchamiane na dowolnym komputerze hosta, który uruchamia aparat platformy Docker.

Po uruchomieniu [obrazu usługi Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) na hoście platformy Docker można uzyskać następujące korzyści:

* Dane telemetryczne cyklu życia dotyczące wszystkich kontenerów uruchomionych na hoście — start, stop i tak dalej.
* Liczniki wydajności dla wszystkich kontenerów. procesora, pamięci, użycia sieci i innych.
* Jeśli [zainstalowano SDK usługi Application Insights dla języka Java](../../azure-monitor/app/java-get-started.md) w aplikacjach uruchomionych w kontenerach, wszystkie dane telemetryczne tych aplikacji będą miały dodatkowe właściwości identyfikujące kontener i komputer-host. Na przykład jeśli masz wystąpienia aplikacji uruchomionej na więcej niż jednym hoście, możesz łatwo filtrować dane telemetryczne aplikacji według hosta.

## <a name="set-up-your-application-insights-resource"></a>Konfigurowanie zasobu usługi Application Insights

1. Zaloguj się do [witryny Microsoft Azure portal](https://azure.com) i otwórz zasób usługi Application Insights dla swojej aplikacji; lub [utwórz nowy](../../azure-monitor/app/create-new-resource.md ). 
   
    *Jakiego zasobu należy użyć?* Jeśli aplikacje uruchomione na hoście zostały opracowane przez inną osobę, należy [utworzyć nowy zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md ). W tym miejscu można wyświetlać i analizować dane telemetryczne. (Wybierz opcję "Ogólne" dla typu aplikacji).
   
    Ale jeśli jesteś twórcą aplikacji, mamy nadzieję, że [dodałeś zestaw SDK usługi Application Insights](../../azure-monitor/app/java-get-started.md) do każdego z nich. Jeśli wszystkie one są naprawdę składniki pojedynczej aplikacji biznesowej, a następnie można skonfigurować wszystkie z nich do wysyłania danych telemetrycznych do jednego zasobu, a użyjesz tego samego zasobu do wyświetlania cyklu życia platformy Docker i danych wydajności. 
   
    Trzeci scenariusz jest, że opracowano większość aplikacji, ale używasz oddzielnych zasobów do wyświetlania ich danych telemetrycznych. W takim przypadku prawdopodobnie chcesz również utworzyć oddzielny zasób dla danych platformy Docker.

2. Kliknij przycisk **rozwijany Essentials** i skopiuj klucz instrumentacji. Służy to do informuj SDK, gdzie wysłać jego danych telemetrycznych.

Trzymaj to okno przeglądarki pod ręką, ponieważ wkrótce wrócisz do niego, aby przyjrzeć się telemetrii.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Uruchamianie monitora usługi Application Insights na hoście

Teraz, gdy masz gdzieś, aby wyświetlić dane telemetryczne, można skonfigurować konteneryzowany aplikacji, która będzie zbierać i wysyłać go.

1. Połącz się z hostem platformy Docker.
2. Edytuj klucz instrumentacji w tym poleceniu, a następnie uruchom go:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Tylko jeden obraz usługi Application Insights jest wymagany dla hosta platformy Docker. Jeśli aplikacja jest wdrażana na wielu hostach platformy Docker, powtórz polecenie na każdym hoście.

## <a name="update-your-app"></a>Aktualizowanie aplikacji
Jeśli aplikacja jest instrumentowana za pomocą [zestawie SDK usługi Application Insights dla języka Java,](../../azure-monitor/app/java-get-started.md)dodaj następujący `<TelemetryInitializers>` wiersz do pliku ApplicationInsights.xml w projekcie, w tym elemencie:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Spowoduje to dodanie informacji platformy Docker, takich jak kontener i identyfikator hosta, do każdego elementu telemetrii wysyłanego z aplikacji.

## <a name="view-your-telemetry"></a>Wyświetlanie telemetrii
Wróć do zasobu usługi Application Insights w witrynie Azure portal.

Kliknij kafelek platformy Docker.

Wkrótce zobaczysz dane pochodzące z aplikacji Platformy Docker, zwłaszcza jeśli masz inne kontenery uruchomione na silniku platformy Docker.

### <a name="docker-container-events"></a>Zdarzenia kontenera platformy Docker
![przykład](./media/docker/13.png)

Aby zbadać poszczególne zdarzenia, kliknij przycisk [Wyszukaj](../../azure-monitor/app/diagnostic-search.md). Wyszukaj i przefiltruj, aby znaleźć żądane zdarzenia. Kliknij dowolne zdarzenie, aby uzyskać więcej szczegółów.

### <a name="exceptions-by-container-name"></a>Wyjątki według nazwy kontenera
![przykład](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Kontekst platformy Docker dodany do telemetrii aplikacji
Żądania danych telemetrycznych wysyłanych z aplikacji instrumentowane za pomocą SDK AI, jest wzbogacony o informacje kontekstu platformy Docker.

## <a name="q--a"></a>Pytania i odpowiedzi
*Co daje aplikacji, które daje mi, że nie mogę uzyskać z platformy Docker?*

* Szczegółowy podział liczników wydajności według kontenera i obrazu.
* Integruj dane kontenerów i aplikacji na jednym pulpicie nawigacyjnym.
* [Eksportowanie danych telemetrycznych](export-telemetry.md) do dalszej analizy do bazy danych, usługi Power BI lub innego pulpitu nawigacyjnego.

*Jak uzyskać dane telemetryczne z samej aplikacji?*

* Zainstaluj SDK usługi Application Insights w aplikacji. Dowiedz się, jak: [aplikacje internetowe Java,](../../azure-monitor/app/java-get-started.md) [aplikacje internetowe systemu Windows](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Film wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Następne kroki

* [Usługa Application Insights dla języka Java](../../azure-monitor/app/java-get-started.md)
* [Usługa Application Insights dla środowiska Node.js](../../azure-monitor/app/nodejs.md)
* [Usługa Application Insights dla ASP.NET](../../azure-monitor/app/asp-net.md)
