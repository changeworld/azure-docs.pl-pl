---
title: Monitorowanie wydajności usługi aplikacji Azure | Dokumentacja firmy Microsoft
description: Monitorowanie wydajności aplikacji dla usług Azure app services. Udostępnianie wykresów czasu ładowania i odpowiedzi oraz informacji o zależnościach oraz ustawianie alertów dotyczących wydajności.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 92a7c1a45655f8804aa1f81b1a77ebf7cd5197e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122169"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorowanie wydajności w usłudze Azure App Service
W [witryny Azure portal](https://portal.azure.com) możesz skonfigurować monitorowanie wydajności aplikacji dla aplikacji sieci web, mobilnych zaplecza i aplikacji API apps w [usługi Azure App Service](../../app-service/overview.md). [Usługa Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) umożliwia instrumentację aplikacji w celu wysyłania danych telemetrii do usługi Application Insights, gdzie są one przechowywane i analizowane. W usłudze tej można używać wykresów metryki i narzędzi wyszukiwania do ułatwiania diagnozowania problemów, zwiększania wydajności i oceny użycia.

## <a name="runtime-or-build-time"></a>Czas wykonywania lub kompilacji
Możesz skonfigurować monitorowanie, instrumentując aplikację na jeden z dwóch sposobów:

* **Środowisko uruchomieniowe** — możesz wybrać rozszerzenie monitorowania, gdy usługi app service jest już aktywna wydajności. Nie jest to konieczne ponownie skompilować lub ponownie zainstalować aplikację. Uzyskujesz standardowy zestaw pakietów, które monitorują czasy odpowiedzi, współczynniki sukcesu, wyjątki, zależności itd.

* **Czas kompilacji** — możesz zainstalować pakiet w aplikacji podczas programowania. Ta opcja jest bardziej wszechstronna. Oprócz tych samych pakietów standardowych możesz napisać kod w celu dostosowania telemetrii lub wysłania własnych danych telemetrii. Możesz rejestrować określone działania lub zdarzenia zgodnie z semantyką domeny aplikacji. To także zapewnia możliwość testowania najnowszą wersję zestawu SDK usługi Application Insights, ponieważ istnieje możliwość oceny zestawy SDK w wersji beta, natomiast monitorowanie w czasie wykonywania jest ograniczony do najnowszej stabilnej wersji.

## <a name="runtime-instrumentation-with-application-insights"></a>Środowiska uruchomieniowego Instrumentacji za pomocą usługi Application Insights
Jeśli korzystasz obecnie z usługi app service na platformie Azure, otrzymujesz pewne informacje monitorowania: żądania i współczynniki błędów domyślnie. Dodaj usługę Application Insights, aby uzyskać więcej informacji, na przykład czasy odpowiedzi, monitorowanie wywołań dla zależności, wykrywanie inteligentne, a dostęp do zaawansowanych język zapytania Kusto. 

1. **Wybierz usługę Application Insights** w Panelu sterowania platformy Azure dla usługi app service.

    ![W obszarze Ustawienia wybierz pozycję Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * O ile nie jest już skonfigurowany zasób usługi Application Insights dla tej aplikacji, należy wybrać opcję utworzenia nowego zasobu. 

     > [!NOTE]
     > Po kliknięciu **OK** do utworzenia nowego zasobu, zostanie wyświetlony monit do **Zastosuj ustawienia monitorowania**. Wybieranie **Kontynuuj** połączy nowy zasób usługi Application Insights do usługi app service, wykonując będzie również **wyzwolić ponowne uruchomienie usługi app service**. 

     ![Instrumentacja aplikacji internetowej](./media/azure-web-apps/create-resource.png)

2. Po określenie zasobu do użycia, możesz wybrać sposób usługi application insights do zbierania danych dla danej platformy dla aplikacji. Monitorowanie aplikacji ASP.NET jest na domyślnie z dwóch różnych poziomach w kolekcji.

    ![Wybierz opcje dla danej platformy](./media/azure-web-apps/choose-options-new.png)

   * .NET **podstawowe kolekcji** poziom oferuje podstawowe funkcje APM jednego wystąpienia.
    
   * .NET **zalecane kolekcji** poziom:
       * Dodaje trendy użycia procesora CPU, pamięci i operacji We/Wy.
       * Koreluje mikrousługi w granicach żądania/zależności.
       * Zbiera dane trendów użycia i umożliwia korelacji z wyniki dostępności transakcji.
       * Gromadzi informacje o wyjątki nieobsługiwane przez proces hosta.
       * Zwiększa dokładność metryki APM pod obciążeniem, pobieranie próbek jest używany.
    
     .NET core oferuje **zalecane kolekcji** lub **wyłączone** dla platformy .NET Core 2.0 i 2.1.

3. **Instrumentacja usługi app service** po zainstalowaniu usługi Application Insights.

   **Włączanie monitorowania po stronie klienta** dla telemetrii widoku i użytkownika dla strony.

    (Ta opcja jest włączona domyślnie w przypadku aplikacji .NET Core za pomocą **zalecane kolekcji**, niezależnie od tego, czy ma ustawienie "APPINSIGHTS_JAVASCRIPT_ENABLED" aplikacji. Pomoc techniczna oparta na szczegółową interfejsu użytkownika dla wyłączenie monitorowania po stronie klienta nie jest obecnie dostępna dla platformy .NET Core.)
    
   * Wybierz kolejno pozycje Ustawienia > Ustawienia aplikacji
   * W obszarze Ustawienia aplikacji dodaj nową parę klucz-wartość:

     Klucz: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Wartość:`true`
   * **Zapisz** ustawienia i **ponownie uruchom** aplikację.

4. Eksplorowanie danych monitorowania aplikacji, wybierając **ustawienia** > **usługi Application Insights** > **wyświetlić więcej szczegółowych informacji w aplikacji**.

W razie potrzeby możesz później utworzyć aplikację za pomocą usługi Application Insights.

*Jak usunąć usługę Application Insights lub przełączyć się na wysyłanie do innego zasobu?*

* Na platformie Azure, otwórz blok sterowania aplikacji sieci web, a w obszarze Ustawienia, otwórz **usługi Application Insights**. Można wyłączyć usługi Application Insights, klikając **wyłączyć** u góry strony, lub wybierz nowy zasób w **zmienić zasób** sekcji.

## <a name="build-the-app-with-application-insights"></a>Skompiluj aplikację za pomocą usługi Application Insights
Usługa Application Insights może zapewnić bardziej szczegółową telemetrię dzięki instalacji zestawu SDK w Twojej aplikacji. W szczególności możesz gromadzić dzienniki śledzenia, [zapisywać niestandardową telemetrię](../../azure-monitor/app/api-custom-events-metrics.md) i uzyskiwać bardziej szczegółowe raporty o wyjątkach.

1. **W programie Visual Studio** (2013 Update 2 lub nowszym) skonfiguruj usługę Application Insights dla projektu.

    Kliknij prawym przyciskiem myszy projekt sieci web, a następnie wybierz pozycję **Dodaj > Application Insights** lub **projektu** > **usługi Application Insights**  >  **Skonfiguruj usługę Application Insights**.

    ![Kliknij prawym przyciskiem myszy projekt sieci Web i pozycję Dodaj lub Konfiguruj usługę Application Insights](./media/azure-web-apps/03-add.png)

    Jeśli pojawi się prośba o zalogowanie, użyj poświadczeń konta platformy Azure.

    Operacja ma dwa skutki:

   1. Tworzy zasób usługi Application Insights na platformie Azure, gdzie dane telemetryczne są przechowywane, analizowane i wyświetlane.
   2. Dodaje pakiet aplikacji NuGet usługi Application Insights do kodu (jeśli jeszcze go tam nie ma) i konfiguruje go do wysyłania telemetrii do zasobu platformy Azure.
2. **Przetestuj telemetrię**, uruchamiając aplikację na komputerze deweloperskim (F5).
3. **Opublikuj aplikację** na platformie Azure w zwykły sposób. 

*Jak przełączyć się na wysyłanie do innego zasobu usługi Application Insights?*

* W programie Visual Studio, kliknij prawym przyciskiem myszy projekt, wybierz polecenie **Konfiguruj usługę Application Insights**i wybierz żądany zasób. Uzyskasz opcję tworzenia nowego zasobu. Ponownie skompiluj i wdróż.

## <a name="automate-monitoring"></a>Automatyzowanie monitorowania

Aby włączyć zbieranie danych telemetrycznych z usługi Application Insights, należy skonfigurować tylko ustawienia aplikacji:

   ![Ustawienia aplikacji usługi App z dostępnymi ustawieniami usługi Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definicje ustawień aplikacji

|Nazwa ustawienia aplikacji |  Definicja | Wartość |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Rozszerzenie głównego, który kontroluje, monitorowanie w czasie wykonywania. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  W domyślnym trybie tylko podstawowych funkcji są włączone w celu uzyskania optymalnej wydajności upewnij się. | `default` lub `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Kontroluje, czy aparat ponownego zapisywania danych binarnych `InstrumentationEngine` zostanie włączona. To ustawienie ma wpływ na wydajność i ma wpływ na czas zimny start/uruchamiania. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Określa, czy SQL i Azure table tekstu będzie można przechwycić wraz z wywołania zależności. Ostrzeżenie dotyczące wydajności: wymaga to `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Ustawienia aplikacji usługi aplikacji za pomocą usługi Azure Resource Manager

Zarządzane i skonfigurowano ustawienia aplikacji dla usług App Services [szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Ta metoda może służyć w przypadku wdrażania nowych zasobów usługi App Service z usługą automation usługi Azure Resource Manager lub modyfikując ustawienia istniejących zasobów.

Podstawowa struktura ustawienia aplikacji JSON dla usługi app service znajduje się poniżej:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Na przykład Azure Resource Manager szablon za pomocą ustawień aplikacji skonfigurowany pod kątem usługi Application Insights to [szablonu](https://github.com/Andrew-MSFT/BasicImageGallery) mogą być przydatne, w szczególności sekcji od [wiersz 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Aby zautomatyzować tworzenie zasobu usługi Application Insights i łącza do nowo utworzonej usługi aplikacji.

Aby utworzyć szablon usługi Azure Resource Manager z wszystkie usługi Application Insights ustawień domyślnych, rozpoczęcie procesu tak, jakby miało Tworzenie nowej aplikacji sieci Web za pomocą usługi Application Insights włączone.

Wybierz **opcje automatyzacji**

   ![Menu tworzenia aplikacji sieci web usługi App Service](./media/azure-web-apps/create-web-app.png)

Spowoduje to wygenerowanie najnowszy szablon usługi Azure Resource Manager przy użyciu wszystkich wymaganych ustawień skonfigurowane.

  ![Szablon aplikacji sieci web usługi App Service](./media/azure-web-apps/arm-template.png)

> [!NOTE]
> Szablon spowoduje wygenerowanie ustawienia aplikacji w trybie "default". Ten tryb jest zoptymalizowane pod kątem, wydajności, chociaż można zmodyfikować szablon, aby aktywować niezależnie od funkcji użytkownik sobie tego życzy.

## <a name="more-telemetry"></a>Dalsze funkcje telemetrii

* [Dane ładowania strony sieci Web](../../azure-monitor/app/javascript.md)
* [Telemetria niestandardowa](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="do-i-still-need-to-go-to-extensions---add---application-insights-extension-for-new-app-service-apps"></a>Nadal należy do rozszerzenia — Dodawanie — rozszerzenie usługi Application Insights w przypadku nowych aplikacji usługi App Service?

Nie, nie musisz ręcznie dodać rozszerzenie. Włączenie usługi Application Insights za pomocą bloku ustawienia doda wszystkie niezbędne ustawienia aplikacji do umożliwienia monitorowania. Teraz jest to możliwe, ponieważ pliki wcześniej dodanej przez rozszerzenie są teraz [wstępnie](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) jako część obrazu usługi App Service. Pliki znajdują się w `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`.

### <a name="if-runtime-and-build-time-monitoring-are-both-enabled-do-i-end-up-with-duplicate-data"></a>Jeśli środowisko uruchomieniowe i monitorowanie w czasie kompilacji są włączone czy mogę powstanie zduplikowanych danych?

Nie, domyślnie w przypadku wykrycia monitorowanie w czasie kompilacji monitorowanie w czasie wykonywania za pomocą rozszerzenia spowoduje zatrzymanie przesyłania danych i monitorowania konfiguracji tylko czas kompilacji będą uznawane. Określenie, czy należy wyłączyć monitorowanie w czasie wykonywania jest oparty na wykrycie któregokolwiek z tych trzech plików:

* Biblioteka dll Microsoft.ApplicationInsights
* Microsoft.ASP.NET.TelemetryCorrelation dll
* Biblioteka dll System.Diagnostics.DiagnosticSource

Należy pamiętać, że w wielu wersjach programu Visual Studio, niektóre lub wszystkie te pliki są domyślnie dodawane do plików szablonów platformy ASP.NET i ASP.NET Core programu Visual Studio. Jeśli projekt został utworzony na podstawie zniżki w stosunku do jednego z szablonów, nawet jeśli nie zostały jawnie włączone usługi Application Insights, obecności zależności pliku uniemożliwiłyby aktywowanie monitorowanie w czasie wykonywania.

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED powoduje, że niekompletne odpowiedzi HTML w aplikacji sieci web .NET CORE.

Włączenie języka Javascript przy użyciu usług App Services może spowodować odpowiedzi html na obcięte.

* Obejście 1: Określ ustawienie aplikacji APPINSIGHTS_JAVASCRIPT_ENABLED na wartość false lub całkowicie usunąć i ponownie uruchom
* Obejście 2: Dodawanie zestawu sdk przy użyciu kodu i usuń rozszerzenie (Profiler i Snapshot debugger nie będzie działać w przypadku tej konfiguracji)

Aby śledzić ten problem, przejdź do [rozszerzenie platformy Azure, powodując niekompletne odpowiedzi HTML](https://github.com/Microsoft/ApplicationInsights-Home/issues/277).

Dla platformy .NET Core poniżej znajdują się aktualnie **nieobsługiwane**:

* Niezależne wdrożenia.
* Aplikacje przeznaczone dla .NET Framework.
* Aplikacje platformy .NET core 2.2.

> [!NOTE]
> .NET core 2.0 i .NET Core 2.1 są obsługiwane. Po dodaniu obsługi platformy .NET Core 2.2 zostanie zaktualizowany w tym artykule.

## <a name="next-steps"></a>Kolejne kroki
* [Uruchom profilera aplikacji na żywo](../../azure-monitor/app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) — monitorowanie usługi Azure Functions za pomocą usługi Application Insights
* [Włącz diagnostykę platformy Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md), która ma być wysyłana do usługi Application Insights.
* [Monitoruj metryki kondycji usługi](../../azure-monitor/platform/data-collection.md), aby upewnić się, że usługa jest dostępna i szybko reaguje.
* [Odbieraj powiadomienia o alertach](../../azure-monitor/platform/alerts-overview.md) zawsze, gdy wystąpią zdarzenia operacyjne lub metryki przekroczą próg.
* Użyj pozycji [Usługa Application Insights dla aplikacji JavaScript i stron sieci Web](../../azure-monitor/app/javascript.md), aby pobrać telemetrię klienta z przeglądarek, w których odwiedzono stronę sieci Web.
* [Konfiguruj testy sieci Web dostępności](../../azure-monitor/app/monitor-web-app-availability.md), aby otrzymywać alerty, gdy witryna nie działa.