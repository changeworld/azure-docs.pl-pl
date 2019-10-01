---
title: 'Szybki Start: monitorowanie za pomocą usługi Azure Application Insights'
description: Zawiera instrukcje umożliwiające szybką konfigurację aplikacji sieci Web Node. js na potrzeby monitorowania za pomocą Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.service: application-insights
ms.custom: mvc, seo-javascript-september2019
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 1db26002167f4b7c5b4fc19699ddb021de8ac23d
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703015"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Szybki Start: Rozpoczynanie monitorowania aplikacji sieci Web Node. js za pomocą usługi Azure Application Insights

Dzięki usłudze Azure Application Insights można łatwo monitorować aplikację sieci Web pod kątem dostępności, wydajności i użycia. Możesz również szybko identyfikować i diagnozować błędy w aplikacji bez czekania na zgłoszenie ich przez użytkownika. Dzięki wersji 0,20 zestawu SDK można monitorować typowe pakiety innych firm, w tym MongoDB, MySQL i Redis.

Ten przewodnik Szybki Start przeprowadzi Cię przez proces dodawania wersji 0,22 Application Insights SDK dla środowiska Node. js do istniejącej aplikacji sieci Web Node. js.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start:

- Potrzebna jest subskrypcja platformy Azure i istniejąca aplikacja sieci Web Node. js.

Jeśli nie masz aplikacji sieci Web w języku Node. js, możesz ją utworzyć, wykonując następujące czynności: [Przewodnik Szybki Start dotyczący tworzenia aplikacji sieci Web w języku Node. js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Zaloguj się do Azure Portal

Zaloguj się do [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Włącz Application Insights

Application Insights może zbierać dane telemetryczne z dowolnej aplikacji połączonej z Internetem, niezależnie od tego, czy działa on lokalnie, czy w chmurze. Aby rozpocząć wyświetlanie tych danych, wykonaj następujące kroki.

1. Wybierz pozycję **Utwórz zasób** > **narzędzia programistyczne** > **Application Insights**.

   ![Dodawanie zasobu Application Insights](./media/nodejs-quick-start/1createresourseappinsights.png)

   > [!NOTE]
   >Jeśli tworzysz zasób Application Insights, możesz dowiedzieć się więcej, odwiedzając dokument [tworzenie Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Zostanie wyświetlona strona konfiguracji; Skorzystaj z poniższej tabeli, aby wypełnić pola wejściowe. 

    | Ustawienia        | Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Globalnie unikatowa wartość | Nazwa identyfikująca monitorowaną aplikację |
   | **Typ aplikacji** | Aplikacja Node. js | Typ monitorowanej aplikacji |
   | **Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację znajdującą się w sąsiedztwie lub w miejscu, w którym znajduje się aplikacja |

2. Wybierz pozycję **Utwórz**.

## <a name="configure-app-insights-sdk"></a>Konfigurowanie zestawu SDK usługi App Insights

1. Wybierz pozycję **Przegląd** i skopiuj **klucz Instrumentacji**aplikacji.

   ![Formularz nowego zasobu usługi App Insights](./media/nodejs-quick-start/3key.png)

2. Dodaj Application Insights SDK dla środowiska Node. js do aplikacji. W folderze głównym aplikacji Uruchom:

   ```bash
   npm install applicationinsights --save
   ```

3. Edytuj pierwszy plik. js aplikacji i Dodaj dwa wiersze poniżej do najwyższej części skryptu. W przypadku korzystania z [aplikacji szybkiego startu środowiska Node. js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs)należy zmodyfikować plik index. js. Zastąp &lt;instrumentation_key @ no__t-1 kluczem Instrumentacji aplikacji. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Uruchom ponownie aplikację.

> [!NOTE]
> Trwa 3-5 minut, zanim dane pojawią się w portalu. Jeśli ta aplikacja jest aplikacją testową o małym ruchu, należy pamiętać, że większość metryk jest przechwytywana tylko wtedy, gdy istnieją aktywne żądania lub operacje.

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpocznij monitorowanie w Azure Portal

1. Możesz teraz ponownie otworzyć stronę **omówienia** Application Insights w Azure Portal, w której pobrano klucz instrumentacji, aby wyświetlić szczegółowe informacje o aktualnie uruchomionej aplikacji.

   ![Menu przegląd Application Insights](./media/nodejs-quick-start/4overview.png)

2. Wybierz pozycję **Mapa aplikacji** , aby uzyskać wizualny układ relacji zależności między składnikami aplikacji. Każdy składnik pokazuje kluczowe wskaźniki wydajności, takie jak obciążenie, wydajność, błędy i alerty.

   ![Mapa aplikacji](./media/nodejs-quick-start/5appmap.png)

3. Wybierz ikonę **Analiza aplikacji** ![Application ikona mapy @ no__t-2 **widoku w analizie**.  Spowoduje to otwarcie **Application Insights analizy**, który oferuje bogaty język zapytań umożliwiający analizowanie wszystkich danych zebranych przez Application Insights. W takim przypadku zostanie wygenerowane zapytanie, które renderuje liczbę żądań jako wykres. Można napisać własne zapytania, aby analizować inne dane.

   ![Graf analizy żądań użytkowników w danym okresie czasu](./media/nodejs-quick-start/6analytics.png)

4. Wróć do strony **Przegląd** i Przeanalizuj wykresy wskaźników KPI.  Ten pulpit nawigacyjny zawiera dane statystyczne dotyczące kondycji aplikacji, w tym liczbę żądań przychodzących, czas trwania tych żądań i wszelkie występujące błędy.

   ![Wykresy osi czasu przeglądu kondycji](./media/nodejs-quick-start/7kpidashboards.png)

   Aby włączyć wykres **czasu ładowania widoku strony** do wypełnienia przy użyciu danych **telemetrycznych po stronie klienta** , Dodaj ten skrypt do każdej strony, którą chcesz śledzić:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. Po lewej stronie wybierz pozycję **metryki**. Użyj Eksploratora metryk do zbadania kondycji i wykorzystania zasobu. Możesz wybrać opcję **Dodaj nowy wykres** , aby utworzyć dodatkowe widoki niestandardowe lub wybrać pozycję **Edytuj** , aby zmodyfikować istniejące typy wykresów, wysokość, paletę kolorów, grupowania i metryki. Na przykład można utworzyć wykres, który wyświetla średni czas ładowania strony w przeglądarce, wybierając pozycję "czas ładowania strony w przeglądarce" z listy rozwijanej metryki i "Średnia" z agregacji. Aby dowiedzieć się więcej o usłudze Azure Eksplorator metryk odwiedź stronę Rozpoczynanie [pracy z usługą azure Eksplorator metryk](../../azure-monitor/platform/metrics-getting-started.md).

   ![Wykres metryk serwera](./media/nodejs-quick-start/8metrics.png)

Aby dowiedzieć się więcej na temat monitorowania środowiska Node. js, zobacz [dodatkową dokumentację środowiska Node. js w usłudze App Insights](../../azure-monitor/app/nodejs.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu testowania można usunąć grupę zasobów i wszystkie powiązane zasoby. W tym celu wykonaj poniższe kroki.

1. Z menu po lewej stronie w obszarze Azure Portal wybierz pozycję **grupy zasobów** , a następnie wybierz pozycję Moja **resourceName**.
2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz w polu tekstowym pozycję Grupa **zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Znajdowanie i diagnozowanie problemów z wydajnością](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
