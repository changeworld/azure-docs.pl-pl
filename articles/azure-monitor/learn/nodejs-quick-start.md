---
title: 'Szybki start: monitorowanie pliku Node.js za pomocą usługi Azure Monitor Application Insights'
description: Zawiera instrukcje szybkiego konfigurowania aplikacji Node.js Web App do monitorowania za pomocą usługi Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 694d2ae529202223869fcbb2a084e32bccaedbf1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77660227"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Szybki start: rozpoczynanie monitorowania aplikacji sieci Web Node.js za pomocą usługi Azure Application Insights

W tym przewodniku Szybki start należy dodać sdk aplikacji SDK w wersji 0.22 dla node.js do istniejącej aplikacji sieci Web Node.js.

Usługa Azure Application Insights umożliwia łatwe monitorowanie dostępności, wydajności i użycia aplikacji internetowej. Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika. Zestaw SDK w wersji 0.20 lub nowszej umożliwia monitorowanie popularnych pakietów innych firm, w tym MongoDB, MySQL i Redis.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Działająca aplikacja Node.js.

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Usługa Application Insights może zbierać dane telemetryczne z dowolnej aplikacji połączonej z Internetem, niezależnie od tego, czy jest ona uruchomiona lokalnie, czy w chmurze. Aby rozpocząć wyświetlanie tych danych, wykonaj poniższe czynności.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Wybierz pozycję **Utwórz narzędzie** > **Developer tools** > deweloperskie aplikacji **.**

   ![Dodawanie zasobu usługi Azure Application Insights](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Jeśli jest to pierwszy raz tworzenia zasobu usługi Application Insights można dowiedzieć się więcej, odwiedzając [utwórz plik doc zasobów usługi Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

   Zostanie wyświetlona strona konfiguracji; użyj poniższej tabeli, aby wypełnić pola wejściowe. 

    | Ustawienia        | Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowana aplikacja |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów do hostowania danych AppInsights. istnieje możliwość utworzenia nowej lub użycia istniejącej grupy zasobów. |
   | **Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

3. Wybierz **pozycję Utwórz**.

## <a name="configure-appinsights-sdk"></a>Konfigurowanie sdk appinsights

1. Wybierz **opcję Przegląd** i skopiuj klucz **instrumentacji**aplikacji .

   ![Wyświetlanie klucza instrumentacji usługi Application Insights](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Dodaj zestaw SDK usługi Application Insights dla środowiska Node.js do aplikacji. W folderze głównym aplikacji uruchom polecenie:

   ```bash
   npm install applicationinsights --save
   ```

3. Edytuj pierwszy plik *.js* aplikacji i dodaj dwa wiersze poniżej do najwyższej części skryptu. Jeśli używasz [aplikacji Szybki start Node.js,](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs)należy zmodyfikować plik *index.js.* Wymień `<instrumentation_key>` klucz oprzyrządowania aplikacji. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Uruchom ponownie aplikację.

> [!NOTE]
> Dane zaczną pojawiać się w portalu po 3–5 minutach. W przypadku aplikacji testowej o małym natężeniu ruchu należy pamiętać, że większość metryk jest przechwytywana tylko wtedy, gdy istnieją aktywne żądania lub operacje.

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

1. Możesz teraz ponownie otworzyć stronę **Przegląd** usługi Application Insights w witrynie Azure Portal, na której pobrano klucz instrumentacji, w celu wyświetlenia szczegółowych informacji o obecnie uruchomionej aplikacji.

   ![Menu Przegląd aplikacji](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Wybierz **mapę aplikacji** dla wizualnego układu relacji zależności między składnikami aplikacji. Każdy składnik przedstawia kluczowe wskaźniki wydajności, takie jak obciążenie, wydajność, błędy i alerty.

   ![Mapa aplikacji usługi Application Insights](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Wybierz **App Analytics** ikonę ![](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) Mapy aplikacji Analizy aplikacji Wyświetl **ikonę w Analytics**.  Ta akcja otwiera **application insights analytics**, który zapewnia bogaty język zapytań do analizowania wszystkich danych zebranych przez usługa Application Insights. W tym przypadku jest generowane zapytanie, które renderuje liczbę żądań w formie wykresu. Możesz pisać własne zapytania do analizy innych danych.

   ![Wykresy analizy usługi Application Insights Analytics](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Wróć do strony **Przegląd** i sprawdź grafy kluczowych wskaźników wydajności.  Ten pulpit nawigacyjny przedstawia dane statystyczne dotyczące kondycji aplikacji, w tym liczbę żądań przychodzących, czas trwania tych żądań i błędy.

   ![Wykresy osi czasu przeglądu kondycji aplikacji](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

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

5. Po lewej stronie wybierz pozycję **Metryki**. Użyj Eksploratora metryk, aby zbadać kondycję i wykorzystanie zasobu. Możesz wybrać **pozycję Dodaj nowy wykres,** aby utworzyć dodatkowe widoki niestandardowe, lub wybrać pozycję **Edytuj,** aby zmodyfikować istniejące typy wykresów, wysokość, paletę kolorów, grupowania i metryki. Na przykład można zrobić wykres, który wyświetla średni czas ładowania strony przeglądarki, wybierając "Czas ładowania strony przeglądarki" z listy rozwijanej metryki i "Avg" z agregacji. Aby dowiedzieć się więcej o Eksploratorze metryk platformy Azure, odwiedź stronę [Wprowadzenie do Eksploratora metryk platformy Azure.](../../azure-monitor/platform/metrics-getting-started.md)

   ![Wykres metryk serwera usługi Application Insights Server](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Aby dowiedzieć się więcej na temat monitorowania pliku Node.js, zapoznaj się z [dodatkową dokumentacją aplikacji AppInsights Node.js](../../azure-monitor/app/nodejs.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu testowania można usunąć grupę zasobów i wszystkie powiązane zasoby. Aby to zrobić, wykonaj poniższe czynności.

> [!NOTE]
> Jeśli użyto istniejącej grupy zasobów, poniższe instrukcje nie będą działać i trzeba będzie po prostu usunąć pojedynczy zasób usługi Application Insights. Pamiętaj, że w każdej chwili usuniesz grupę zasobów, wszystkie zasoby, które są członkami tej grupy, zostaną usunięte.

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal wybierz pozycję **Grupy zasobów**, a następnie wybierz pozycję **myResourceGroup**.
2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wprowadź **myResourceGroup** w polu tekstowym, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyszukiwanie i diagnozowanie problemów z wydajnością](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
