---
title: Monitorowanie aplikacji Docker w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Docker liczników wydajności, zdarzeń i wyjątków mogą być wyświetlane w usłudze Application Insights oraz dane telemetryczne z aplikacji konteneryzowanych.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 53ade76b9dbdc27df90da1f7e197464816529d1d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295765"
---
# <a name="monitor-docker-applications-in-application-insights"></a>Monitorowanie aplikacji Docker w usłudze Application Insights
Zdarzenia cyklu życia i wydajności liczników z [Docker](https://www.docker.com/) kontenerów może być na wykresie na usługi Application Insights. Zainstaluj [usługi Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) obrazu w kontenerze w hoście, a wyświetli liczniki wydajności dla hosta, a także inne obrazy.

Z rozwiązaniem Docker, z dystrybucji w kontenerach lekkie ukończone wszystkie zależności aplikacji. By działały na dowolnym komputerze hosta z systemem aparatem platformy Docker.

Po uruchomieniu [obrazu usługi Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) na hoście Docker, możesz uzyskać następujące korzyści:

* Cykl życia telemetrii dotyczące wszystkich kontenerów, które są uruchomione na hoście — uruchamianie, zatrzymywanie i tak dalej.
* Liczniki wydajności dla wszystkich kontenerów. Procesor CPU, pamięci, użycie sieci i inne.
* Jeśli użytkownik [zainstalowany zestaw SDK usługi Application Insights dla języka Java](app-insights-java-live.md) w aplikacje uruchomione w kontenerach, wszystkie dane telemetryczne tych aplikacji będą miały dodatkowe właściwości identyfikacji komputera hosta i kontenera. Tak na przykład jeśli masz wystąpienie aplikacji uruchomionej w więcej niż jednego hosta, łatwo filtrować telemetrii aplikacji przez hosta.

![Przykład](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>Ustawianie zasobu usługi Application Insights
1. Zaloguj się do [portalu Microsoft Azure](https://azure.com) , a następnie otwórz zasobu usługi Application Insights dla aplikacji; lub [Utwórz nową](app-insights-create-new-resource.md). 
   
    *Którego zasobu należy użyć?* Jeśli aplikacje, które są uruchomione na hoście zostały opracowane przez innego użytkownika, a następnie trzeba [utworzyć nowy zasób usługi Application Insights](app-insights-create-new-resource.md). Jest to, gdzie przeglądać i analizować dane telemetryczne. (Wybierz Ogólne, dla typu aplikacji).
   
    Ale jeśli jesteś Deweloper aplikacji, a następnie mamy nadzieję [dodaje zestaw SDK usługi Application Insights](app-insights-java-live.md) do każdego z nich. Jeśli są one wszystkich naprawdę składników aplikacji biznesowej, można skonfigurować wszystkie z nich do wysyłania danych telemetrycznych do jednego zasobu, a więc będzie używać tego samego zasobu do wyświetlenia danych wydajności i cyklem życia rozwiązania Docker. 
   
    Trzeci scenariusz jest opracowany większość aplikacji, że używasz oddzielnych zasobów do wyświetlenia ich telemetrii. W takim przypadku użytkownik może być także tworzenie oddzielnych zasobu danych Docker. 
2. Dodaj kafelka Docker: Wybierz **dodać Kafelek**, przeciągnij Kafelek Docker z poziomu galerii, a następnie kliknij przycisk **gotowe**. 
   
    ![Przykład](./media/app-insights-docker/03.png)

> [!NOTE]
> W okienku omówienie w usłudze Application Insights jest teraz zablokowany i nie zezwala na dodawanie kafelków z galerii. Nadal możesz dodać kafelka Docker zgodnie z powyższym opisem za pośrednictwem interfejsu pulpitu nawigacyjnego platformy Azure.

3. Kliknij przycisk **Essentials** listy rozwijanej i skopiuj klucz instrumentacji. Możesz użyć tego stwierdzić, gdzie jego danych telemetrycznych zestawu SDK.

    ![Przykład](./media/app-insights-docker/02-props.png)

Zachowaj przydatne, to okno przeglądarki, jak będzie wrócić do niego wkrótce aby przyjrzeć się telemetrii.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Uruchom monitor usługi Application Insights na hoście
Skoro masz innym miejscu, aby wyświetlić dane telemetryczne, można skonfigurować konteneryzowanych aplikację, która będzie gromadzić i wysłać go.

1. Łączą się z hostem Docker. 
2. Edytuj klucz Instrumentacji do tego polecenia, a następnie uruchom go:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Tylko jeden obraz usługi Application Insights jest wymagany na hoście Docker. Jeśli aplikacja jest wdrażana na wielu hostach Docker, następnie powtórz polecenie na każdym hoście.

## <a name="update-your-app"></a>Aktualizowanie aplikacji
Jeśli aplikacja jest instrumentowane przy użyciu [zestaw SDK usługi Application Insights dla języka Java](app-insights-java-get-started.md), Dodaj następujący wiersz w pliku ApplicationInsights.xml w projekcie, w obszarze `<TelemetryInitializers>` elementu:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Spowoduje to dodanie Docker takie informacje jak kontener i identyfikator hosta do każdego elementu danych telemetrycznych wysłanych z aplikacji.

## <a name="view-your-telemetry"></a>Wyświetlanie telemetrii
Wróć do zasobu usługi Application Insights w portalu Azure.

Kliknij Kafelek Docker.

Wkrótce zobaczysz danych otrzymywanych z aplikacji Docker, zwłaszcza, jeśli masz inne kontenery zasilany z aparatem platformy Docker.

Poniżej przedstawiono niektóre widoki, które można pobrać.

### <a name="perf-counters-by-host-activity-by-image"></a>Liczniki wydajności przez hosta, działania przez obraz
![Przykład](./media/app-insights-docker/10.png)

![Przykład](./media/app-insights-docker/11.png)

Kliknij dowolną nazwę hosta lub obraz, aby uzyskać więcej szczegółów.

Aby dostosować widok, kliknij dowolnego wykresu, siatki nagłówka, lub użyj Dodawanie wykresu. 

[Dowiedz się więcej o Eksploratora metryk](app-insights-metrics-explorer.md).

### <a name="docker-container-events"></a>Zdarzenia kontenera docker
![Przykład](./media/app-insights-docker/13.png)

Aby zbadać pojedynczych zdarzeń, kliknij przycisk [wyszukiwania](app-insights-diagnostic-search.md). Wyszukaj i Filtruj zdarzenia, które chcesz odnaleźć. Kliknij przycisk dowolnego zdarzenia, aby uzyskać więcej szczegółów.

### <a name="exceptions-by-container-name"></a>Wyjątki według nazwy kontenera
![Przykład](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Docker dodano kontekst do telemetrii aplikacji
Dane telemetryczne żądania wysyłane z aplikacji Instrumentacji z zestawem SDK AI, wzbogaconych Docker kontekstu:

![Przykład](./media/app-insights-docker/16.png)

Liczniki wydajności dostępnej pamięci i czasu procesora wzbogacone i pogrupowane według nazwy kontenera Docker:

![Przykład](./media/app-insights-docker/15.png)

## <a name="q--a"></a>Pytania i odpowiedzi
*Co to oznacza usługi Application Insights udzielenia mnie, który nie może odczytać Docker?*

* Szczegółowy podział liczniki wydajności przez kontener i obrazów.
* Integrowanie aplikacji i kontenera danych na jednym pulpicie nawigacyjnym.
* [Eksportuj dane telemetryczne](app-insights-export-telemetry.md) do dalszej analizy do bazy danych, usługi Power BI lub innymi pulpitu nawigacyjnego.

*Jak uzyskać dane telemetryczne z aplikacji?*

* Zainstaluj zestaw SDK usługi Application Insights w aplikacji. Dowiedz się, jak dla: [aplikacje sieci web Java](app-insights-java-get-started.md), [aplikacji sieci web systemu Windows](app-insights-asp-net.md).

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Kolejne kroki

* [Application Insights dla języka Java](app-insights-java-get-started.md)
* [Usługa Application Insights dla środowiska Node.js](app-insights-nodejs.md)
* [Application Insights dla platformy ASP.NET](app-insights-asp-net.md)
