---
title: Monitorowanie aplikacji Docker w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Docker liczników wydajności, zdarzeń i wyjątki mogą być wyświetlane w usłudze Application Insights wraz z danych telemetrycznych z konteneryzowanych aplikacji.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: 115e2d6b041ecc3f38a2a6438d90777da9660221
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62098035"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Monitorowanie aplikacji Docker w usłudze Application Insights (przestarzałe)

> [!NOTE]
> To rozwiązanie jest przestarzała. Aby dowiedzieć się więcej o naszych dotychczasowe inwestycje w rozwiązania do monitorowania kontenerów firma Microsoft zaleca, wyewidencjonowywanie [usługi Azure Monitor dla kontenerów](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

Zdarzenia cyklu życia i wydajności liczników z [Docker](https://www.docker.com/) kontenery mogą być dobrobytu w usłudze Application Insights. Zainstaluj [usługi Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) obrazu w kontenerze w hoście, a wyświetli liczniki wydajności dla hosta, a także inne obrazy.

Za pomocą platformy Docker możesz dystrybuować swoje aplikacje w uproszczonych kontenerów ze wszystkimi zależnościami. Będą działać na dowolnym komputerze hosta, który działa aparat platformy Docker.

Po uruchomieniu [obrazu usługi Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) na hoście platformy Docker, uzyskasz następujące korzyści:

* Cykl życia telemetrii dotyczącej wszystkie kontenery działające na hoście — uruchamianie, zatrzymywanie i tak dalej.
* Liczniki wydajności dla wszystkich kontenerów. Procesor CPU, pamięci, użycie sieci i więcej.
* Jeśli użytkownik [zainstalować zestaw Application Insights SDK dla języka Java](../../azure-monitor/app/java-get-started.md) w aplikacji działających w kontenerach, wszystkie dane telemetryczne aplikacji, które będą miały dodatkowych właściwości identyfikowanie kontenera i hostów maszyn. Tak na przykład w przypadku wystąpienia aplikacji działających w więcej niż jednego hosta można łatwo filtrować telemetrii aplikacji przez hosta.

## <a name="set-up-your-application-insights-resource"></a>Konfigurowanie zasobu usługi Application Insights

1. Zaloguj się do [portalu Microsoft Azure](https://azure.com) , a następnie otwórz zasób usługi Application Insights dla aplikacji; lub [Utwórz nową](../../azure-monitor/app/create-new-resource.md ). 
   
    *Który zasób powinien używać?* Jeśli aplikacje, które są uruchomione na hoście zostały opracowane przez innego użytkownika, a następnie należy [Utwórz nowy zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md ). Jest to, gdzie wyświetlać i analizować dane telemetryczne. (Wybierz "Ogólne" dla typu aplikacji).
   
    Ale jeśli jesteś deweloperem aplikacji, a następnie mamy nadzieję, że możesz [dodano zestaw SDK usługi Application Insights](../../azure-monitor/app/java-get-started.md) do każdego z nich. Jeśli są one wszystkie naprawdę składniki aplikacji biznesowej, które można skonfigurować wszystkie z nich do wysyłania telemetrii do jednego zasobu i użyjesz tego samego zasobu, aby wyświetlić dane cyklu życia i wydajność platformy Docker. 
   
    Trzeci scenariusz jest opracowany większość aplikacji, że używasz oddzielne zasoby, aby wyświetlić ich dane telemetryczne. W takiej sytuacji użytkownik może być także utworzyć osobny zasób dla danych platformy Docker.

2. Kliknij przycisk **Essentials** listy rozwijanej i kopiowanie klucza instrumentacji. Możesz użyć tego powiedzieć, gdzie wysyłać swoje dane telemetryczne zestawu SDK.

Będziesz wróć wkrótce, aby przyjrzeć się telemetrii niskich zawsze pod ręką, to okno przeglądarki.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Uruchom monitor usługi Application Insights na hoście

Teraz, gdy masz innym miejscu, aby wyświetlić dane telemetryczne, można skonfigurować konteneryzowaną aplikację, która będzie zbierać i wysyłać je.

1. Podłącz do hosta platformy Docker.
2. Edytuj klucz Instrumentacji do tego polecenia, a następnie uruchom go:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Tylko jeden obraz usługi Application Insights jest wymagana na każdym hoście platformy Docker. Jeśli aplikacja jest wdrażana na wielu hostach Docker, następnie powtórz polecenie na każdym hoście.

## <a name="update-your-app"></a>Zaktualizuj aplikację
Jeśli aplikacja jest wyposażone w [zestaw Application Insights SDK dla języka Java](../../azure-monitor/app/java-get-started.md), Dodaj następujący wiersz do pliku ApplicationInsights.xml w projekcie, w obszarze `<TelemetryInitializers>` elementu:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Spowoduje to dodanie informacji platformy Docker, takich jak kontener i identyfikator hosta do każdego elementu telemetrii wysyłanych z aplikacji.

## <a name="view-your-telemetry"></a>Wyświetlanie telemetrii
Wróć do zasobu usługi Application Insights w witrynie Azure portal.

Kliknij Kafelek platformy Docker.

Wkrótce zostaną wyświetlone dane otrzymywane z aplikacji platformy Docker, zwłaszcza, jeśli masz inne kontenery uruchomione na aparat platformy Docker.

### <a name="docker-container-events"></a>Zdarzenia kontenera platformy docker
![Przykład](./media/docker/13.png)

Aby zbadać poszczególne zdarzenia, kliknij przycisk [wyszukiwania](../../azure-monitor/app/diagnostic-search.md). Wyszukaj i Filtruj zdarzenia, które chcesz znaleźć. Kliknij dowolne zdarzenie, aby uzyskać więcej informacji.

### <a name="exceptions-by-container-name"></a>Wyjątki według nazwy kontenera
![Przykład](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Kontekst platformy docker, dodane do telemetrii aplikacji
Telemetria żądań wysyłanych z aplikacji instrumentowanie za pomocą sztucznej Inteligencji zestawu SDK jest wzbogacone o informacje o kontekście platformy Docker.

## <a name="q--a"></a>Pytania i odpowiedzi
*Jakie usługi Application Insights zapewnia mnie, że nie można pobrać z platformy Docker?*

* Szczegółowy podział liczniki wydajności kontenera i obrazów.
* Integrowanie danych kontenerów i aplikacji na jednym pulpicie nawigacyjnym.
* [Eksportowanie telemetrii](export-telemetry.md) do dalszej analizy w bazie danych, usługa Power BI lub inny pulpit nawigacyjny.

*Jak uzyskać dane telemetryczne z aplikacji?*

* Zainstaluj zestaw SDK usługi Application Insights w aplikacji. Dowiedz się, jak dla: [Aplikacje sieci web Java](../../azure-monitor/app/java-get-started.md), [aplikacje sieci web Windows](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Kolejne kroki

* [Usługa Application Insights dla środowiska Java](../../azure-monitor/app/java-get-started.md)
* [Usługa Application Insights dla środowiska Node.js](../../azure-monitor/app/nodejs.md)
* [Usługa Application Insights dla platformy ASP.NET](../../azure-monitor/app/asp-net.md)
