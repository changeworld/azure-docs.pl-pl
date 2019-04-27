---
title: Szybki start — Azure Application Insights | Microsoft Docs
description: W tym artykule przedstawiono instrukcje pozwalające szybko skonfigurować aplikację internetową Node.js w celu monitorowania za pomocą usługi Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/01/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: c6979ce5cade09d4daa4e6eddd79fb69175ec902
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540357"
---
# <a name="start-monitoring-your-nodejs-web-application"></a>Rozpoczynanie monitorowania aplikacji internetowej Node.js

Usługa Azure Application Insights umożliwia łatwe monitorowanie dostępności, wydajności i użycia aplikacji internetowej. Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika. Zestaw SDK w wersji 0.20 lub nowszej umożliwia monitorowanie popularnych pakietów innych firm, w tym MongoDB, MySQL i Redis.

Ten przewodnik Szybki start przeprowadzi Cię przez proces dodawania zestawu SDK usługi Application Insights dla środowiska Node.js w wersji 0.22 do istniejącej aplikacji internetowej Node.js.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

- Wymagana jest subskrypcja platformy Azure i istniejąca aplikacja internetowa Node.js.

Jeśli nie masz aplikacji internetowej Node.js, możesz ją utworzyć, wykonując instrukcje przedstawione w [przewodniku Szybki start dotyczącym tworzenia aplikacji internetowej Node.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Usługa Application Insights umożliwia zbieranie danych telemetrycznych z dowolnej aplikacji połączonej z Internetem, niezależnie od tego, czy jest to aplikacja lokalna czy aplikacja w chmurze. Aby rozpocząć wyświetlanie tych danych, wykonaj poniższe czynności.

1. Wybierz pozycję **Utwórz zasób** > **Narzędzia deweloperskie** > **Application Insights**.

   ![Dodawanie zasobu usługi Application Insights](./media/nodejs-quick-start/1createresourseappinsights.png)

   ![Dodawanie zasobu usługi Application Insights](./media/nodejs-quick-start/2createnodejs.png)


   Zostanie wyświetlone okno konfiguracji. Wypełnij pola wejściowe, używając poniższej tabeli.

    | Ustawienia        | Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowaną aplikację |
   | **Typ aplikacji** | Aplikacja Node.js | Typ monitorowanej aplikacji |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów hostującej dane usługi App Insights |
   | **Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

2. Kliknij pozycję **Utwórz**.

## <a name="configure-app-insights-sdk"></a>Konfigurowanie zestawu SDK usługi App Insights

1. Wybierz **Przegląd** i skopiuj aplikacji **klucz Instrumentacji**.

   ![Formularz nowego zasobu usługi App Insights](./media/nodejs-quick-start/3key.png)

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

   ![Menu Przegląd usługi Application Insights](./media/nodejs-quick-start/4overview.png)

2. Kliknij pozycję **Mapa aplikacji**, aby uzyskać wizualny układ relacji zależności między składnikami aplikacji. Każdy składnik przedstawia kluczowe wskaźniki wydajności, takie jak obciążenie, wydajność, błędy i alerty.

   ![Mapa aplikacji](./media/nodejs-quick-start/5appmap.png)

3. Kliknij pozycję **analizy aplikacji** ikonę ![ikona mapy aplikacji](./media/nodejs-quick-start/006.png) **Wyświetl w obszarze analiza**.  Spowoduje to otwarcie strony **Analiza usługi Application Insights**, która udostępnia zaawansowany język zapytań na potrzeby analizy wszystkich danych zbieranych przez usługę Application Insights. W tym przypadku jest generowane zapytanie, które renderuje liczbę żądań w formie wykresu. Możesz pisać własne zapytania do analizy innych danych.

   ![Wykres analizy żądań użytkowników w danym okresie](./media/nodejs-quick-start/6analytics.png)

4. Wróć do strony **Przegląd** i sprawdź grafy kluczowych wskaźników wydajności.  Ten pulpit nawigacyjny przedstawia dane statystyczne dotyczące kondycji aplikacji, w tym liczbę żądań przychodzących, czas trwania tych żądań i błędy.

   ![Wykresy osi czasu przeglądu kondycji](./media/nodejs-quick-start/7kpidashboards.png)

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

5. Po kliknięciu lewym na **metryki**. Skorzystaj z Eksploratora metryk do badania kondycji i wykorzystania zasobu. Możesz kliknąć pozycję **Dodaj nowy wykres**, aby utworzyć dodatkowe widoki niestandardowe, lub wybrać pozycję **Edytuj**, aby zmodyfikować istniejące typy wykresów, wysokość, paletę kolorów, grupowanie i metryki. Na przykład można utworzyć wykres, wyświetlającą czas ładowania strony przeglądarki średnia, wprost wybierając "Czas ładowania strony przeglądarki" z rozwijanego metryki i "Średnia" z agregacji. Aby dowiedzieć się więcej o Eksploratorze metryk platformy Azure, odwiedź stronę [wprowadzenie do Eksploratora metryk usługi Azure](../../azure-monitor/platform/metrics-getting-started.md).

   ![Wykres metryk serwera](./media/nodejs-quick-start/8metrics.png)

Więcej informacji na temat monitorowania środowiska Node.js można znaleźć w [dodatkowej dokumentacji środowiska Node.js w usłudze App Insights](../../azure-monitor/app/nodejs.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu testowania, możesz usunąć grupę zasobów i wszystkie pokrewne zasoby. Można więc wykonaj poniższe kroki.

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij pozycję **myResourceGroup**.
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę **myResourceGroup**, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wyszukiwanie i diagnozowanie problemów z wydajnością](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
