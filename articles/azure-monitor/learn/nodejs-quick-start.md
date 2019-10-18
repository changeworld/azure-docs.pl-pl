---
title: 'Szybki Start: monitorowanie za pomocą usługi Azure Application Insights'
description: W tym artykule przedstawiono instrukcje pozwalające szybko skonfigurować aplikację internetową Node.js w celu monitorowania za pomocą usługi Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.service: application-insights
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 84be9c2b1d8e28fb847e52bda36f9857bd28da28
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528784"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Szybki Start: Rozpoczynanie monitorowania aplikacji sieci Web Node. js za pomocą usługi Azure Application Insights

Ten przewodnik Szybki start przeprowadzi Cię przez proces dodawania zestawu SDK usługi Application Insights dla środowiska Node.js w wersji 0.22 do istniejącej aplikacji internetowej Node.js.

Usługa Azure Application Insights umożliwia łatwe monitorowanie dostępności, wydajności i użycia aplikacji internetowej. Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika. Zestaw SDK w wersji 0.20 lub nowszej umożliwia monitorowanie popularnych pakietów innych firm, w tym MongoDB, MySQL i Redis.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start:

- Wymagana jest subskrypcja platformy Azure i istniejąca aplikacja internetowa Node.js.

Jeśli nie masz aplikacji internetowej Node.js, możesz ją utworzyć, wykonując instrukcje przedstawione w [przewodniku Szybki start dotyczącym tworzenia aplikacji internetowej Node.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [portalu Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Usługa Application Insights umożliwia zbieranie danych telemetrycznych z dowolnej aplikacji połączonej z Internetem, niezależnie od tego, czy jest to aplikacja lokalna czy aplikacja w chmurze. Aby rozpocząć wyświetlanie tych danych, wykonaj poniższe czynności.

1. Wybierz pozycję **Utwórz zasób** > **Narzędzia deweloperskie** > **Application Insights**.

   ![Dodawanie zasobu usługi Azure Application Insights](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Jeśli tworzysz zasób Application Insights, możesz dowiedzieć się więcej, odwiedzając dokument [tworzenie Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Zostanie wyświetlona strona konfiguracji; Skorzystaj z poniższej tabeli, aby wypełnić pola wejściowe. 

    | Ustawienia        | Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowaną aplikację |
   | **Typ aplikacji** | Aplikacja Node.js | Typ monitorowanej aplikacji |
   | **Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

2. Wybierz pozycję **Utwórz**.

## <a name="configure-app-insights-sdk"></a>Konfigurowanie zestawu SDK usługi App Insights

1. Wybierz pozycję **Przegląd** i skopiuj **klucz Instrumentacji**aplikacji.

   ![Wyświetlanie klucza Instrumentacji Application Insights](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Dodaj zestaw SDK usługi Application Insights dla środowiska Node.js do aplikacji. W folderze głównym aplikacji uruchom polecenie:

   ```bash
   npm install applicationinsights --save
   ```

3. Edytuj pierwszy plik js aplikacji i dodaj dwa wiersze poniżej najwyższej części skryptu. Jeśli używasz [aplikacji szybkiego startu Node.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs), zmodyfikuj plik index.js. Zastąp klucz &lt;instrumentation_key&gt; kluczem instrumentacji aplikacji. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Uruchom ponownie aplikację.

> [!NOTE]
> Dane zaczną pojawiać się w portalu po 3–5 minutach. W przypadku aplikacji testowej o małym natężeniu ruchu należy pamiętać, że większość metryk jest przechwytywana tylko wtedy, gdy istnieją aktywne żądania lub operacje.

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

1. Możesz teraz ponownie otworzyć stronę **Przegląd** usługi Application Insights w witrynie Azure Portal, na której pobrano klucz instrumentacji, w celu wyświetlenia szczegółowych informacji o obecnie uruchomionej aplikacji.

   ![Menu przegląd Application Insights](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Wybierz pozycję **Mapa aplikacji** , aby uzyskać wizualny układ relacji zależności między składnikami aplikacji. Każdy składnik przedstawia kluczowe wskaźniki wydajności, takie jak obciążenie, wydajność, błędy i alerty.

   ![Application Insights mapowanie aplikacji](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Wybierz ikonę **Analiza aplikacji** ![Application ikonę mapy ](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **Widok w obszarze Analiza**.  Spowoduje to otwarcie strony **Analiza usługi Application Insights**, która udostępnia zaawansowany język zapytań na potrzeby analizy wszystkich danych zbieranych przez usługę Application Insights. W tym przypadku jest generowane zapytanie, które renderuje liczbę żądań w formie wykresu. Możesz pisać własne zapytania do analizy innych danych.

   ![Wykresy analityczne Application Insights](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Wróć do strony **Przegląd** i sprawdź grafy kluczowych wskaźników wydajności.  Ten pulpit nawigacyjny przedstawia dane statystyczne dotyczące kondycji aplikacji, w tym liczbę żądań przychodzących, czas trwania tych żądań i błędy.

   ![Wykresy osi czasu przegląd kondycji Application Insights](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

   Aby włączyć wykres **Wyświetlenie strony — czas ładowania** z danymi **telemetrycznymi po stronie klienta**, dodaj ten skrypt na każdej stronie, którą chcesz śledzić:

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

   ![Wykres metryk serwera Application Insights](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Więcej informacji na temat monitorowania środowiska Node.js można znaleźć w [dodatkowej dokumentacji środowiska Node.js w usłudze App Insights](../../azure-monitor/app/nodejs.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu testowania można usunąć grupę zasobów i wszystkie powiązane zasoby. W tym celu wykonaj poniższe kroki.

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal wybierz pozycję **Grupy zasobów**, a następnie wybierz pozycję **myResourceGroup**.
2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz w polu tekstowym pozycję Grupa **zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyszukiwanie i diagnozowanie problemów z wydajnością](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
