---
title: 'Samouczek: Rozwiązywanie problemów z monitorem platformy Azure'
description: Dowiedz się, jak usługa Azure Monitor i usługa Log Analytics pomagają monitorować aplikację sieci web usługi App Service. Usługa Azure Monitor maksymalizuje dostępność, dostarczając kompleksowe rozwiązanie do monitorowania środowisk.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 2/28/2020
ms.openlocfilehash: d543a9364311b2cf5f0258fbf9185d27bb1bfb2f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78399531"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Samouczek: Rozwiązywanie problemów z aplikacją usługi App Service za pomocą usługi Azure Monitor

> [!NOTE]
> Integracja usługi Azure Monitor z usługą App Service jest w [wersji zapoznawczej.](https://aka.ms/appsvcblog-azmon)
>

[Usługa App Service w systemie Linux](app-service-linux-intro.md) zapewnia wysoce skalowalną, samoładującą się usługę hostingową przy użyciu systemu operacyjnego Linux. [Usługa Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) maksymalizuje dostępność i wydajność aplikacji i usług, dostarczając kompleksowe rozwiązanie do zbierania, analizowania i działania na podstawie danych telemetrycznych ze środowisk w chmurze i lokalnie.

W tym samouczku pokazano, jak rozwiązywać problemy z aplikacją za pomocą [usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview). Przykładowa aplikacja zawiera kod przeznaczony do wyczerpania pamięci i spowodować błędy HTTP 500, dzięki czemu można zdiagnozować i rozwiązać problem przy użyciu usługi Azure Monitor.

Po zakończeniu będziesz mieć przykładową aplikację uruchomioną w usłudze App Service w systemie Linux zintegrowaną z [usługą Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/overview)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie aplikacji sieci Web za pomocą usługi Azure Monitor
> * Wysyłanie dzienników konsoli do usługi Log Analytics
> * Używanie zapytań dziennika do identyfikowania błędów aplikacji sieci Web i rozwiązywania ich

Kroki opisane w tym samouczku można wykonać w systemie macOS, Linux i Windows.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz:

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

Najpierw uruchom kilka poleceń lokalnie, aby skonfigurować przykładową aplikację do użycia w tym samouczku. Polecenia klonują przykładową aplikację, tworzą zasoby platformy Azure, tworzą użytkownika wdrażania i wdrażają aplikację na platformie Azure. Zostanie wyświetlony monit o podanie hasła w ramach tworzenia użytkownika wdrażania. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor-preview"></a>Konfigurowanie usługi Azure Monitor (wersja zapoznawcza)

### <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics

Po wdrożeniu przykładowej aplikacji w usłudze Azure App Service skonfigurujesz możliwość monitorowania w celu rozwiązywania problemów z aplikacją w przypadku wystąpienia problemów. Usługa Azure Monitor przechowuje dane dziennika w obszarze roboczym usługi Log Analytics. Obszar roboczy jest kontenerem, który zawiera informacje o danych i konfiguracji.

W tym kroku utworzysz obszar roboczy usługi Log Analytics, aby skonfigurować usługę Azure Monitor za pomocą aplikacji.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [W przypadku usługi Azure Monitor Log Analytics płacisz za pozyskiwania danych i przechowywanie danych.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Tworzenie ustawienia diagnostycznego

Ustawienia diagnostyczne mogą służyć do zbierania metryk dla niektórych usług platformy Azure do dzienników usługi Azure Monitor do analizy z innymi danymi monitorowania przy użyciu zapytań dziennika. W tym samouczku można włączyć serwer www i standardowe dzienniki danych wyjściowych/błędów. Zobacz [obsługiwane typy dzienników,](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types) aby uzyskać pełną listę typów dzienników i opisów.

Uruchom następujące polecenia, aby utworzyć ustawienia diagnostyczne dla AppServiceConsoleLogs (standardowe dane wyjściowe/błąd) i AppServiceHTTPLogs (dzienniki serwera sieci web). Zamień _ \<>nazwy aplikacji_ i _ \<nazwy obszaru roboczego>_ wartościami. 

> [!NOTE]
> Pierwsze dwa polecenia `resourceID` i `workspaceID`, są zmiennymi, `az monitor diagnostic-settings create` które mają być używane w poleceniu. Aby uzyskać więcej informacji na temat tego polecenia, zobacz [Tworzenie ustawień diagnostycznych przy użyciu interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-using-azure-cli)
>

```bash

resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'

```

## <a name="troubleshoot-the-app"></a>Rozwiązywanie problemów z aplikacją

Przejdź do `http://<app-name>.azurewebsites.net`.

Przykładowa aplikacja ImageConverter konwertuje dołączone `JPG` `PNG`obrazy z . Błąd został celowo umieszczony w kodzie dla tego samouczka. Jeśli wybierzesz wystarczającą liczbę obrazów, aplikacja wygeneruje błąd HTTP 500 podczas konwersji obrazu. Wyobraź sobie, że ten scenariusz nie był brany pod uwagę w fazie rozwoju. Użyjesz usługi Azure Monitor, aby rozwiązać problem.

### <a name="verify-the-app-is-works"></a>Sprawdź, czy aplikacja działa

Aby przekonwertować obrazy, kliknij `Tools` i wybierz opcję `Convert to PNG`.

![Kliknij "Narzędzia" i wybierz "Konwertuj na PNG"](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Zaznacz dwa pierwsze obrazy `convert`i kliknij przycisk . Spowoduje to pomyślne przekonwertowanie.

![Wybierz dwa pierwsze obrazy](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Przerywanie aplikacji

Teraz, gdy zweryfikujesz aplikację, konwertując dwa obrazy pomyślnie, spróbujemy przekonwertować pierwsze pięć obrazów.

![Konwertowanie pierwszych pięciu obrazów](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Ta akcja kończy się `HTTP 500` niepowodzeniem i generuje błąd, który nie został przetestowany podczas tworzenia.

![Konwersja spowoduje błąd HTTP 500](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Wyświetlanie dzienników monitora usługi Azure Monitor za pomocą kwerendy dziennika

Zobaczmy, jakie dzienniki są dostępne w obszarze roboczym usługi Log Analytics. 

Kliknij to [łącze obszaru roboczego usługi Log Analytics,](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) aby uzyskać dostęp do obszaru roboczego w witrynie Azure portal.

W witrynie Azure portal wybierz obszar roboczy usługi Log Analytics.

### <a name="log-queries"></a>Rejestrowanie zapytań

Zapytania dziennika pomagają w pełni wykorzystać wartość danych zebranych w dziennikach usługi Azure Monitor. Kwerendy dziennika służy do identyfikowania dzienników w appservicehttplogs i AppServiceConsoleLogs. Zobacz [omówienie kwerendy dziennika, aby](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) uzyskać więcej informacji na temat kwerend dziennika.

### <a name="view-appservicehttplogs-with-log-query"></a>Wyświetlanie aplikacjiHTTPLogs z kwerendą dziennika

Teraz, gdy mamy dostęp do aplikacji, zobaczmy dane skojarzone z żądaniami HTTP, znalezione w `AppServiceHTTPLogs`.

1. Kliknij `Logs` z nawigacji po lewej stronie.

![Log Anlytics Worksace Dzienniki](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Wyszukaj `appservice` i `AppServiceHTTPLogs`kliknij dwukrotnie opcję .

![Tabele obszaru roboczego analizy dzienników](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Kliknij pozycję `Run` (Dalej).

![Dzienniki HTTP usługi aplikacji usługi usługi usługi rejestrowania usługi workspace](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

Kwerenda `AppServiceHTTPLogs` zwraca wszystkie żądania w ciągu ostatnich 24 godzin. Kolumna `ScStatus` zawiera stan HTTP. Aby zdiagnozować `HTTP 500` błędy, `ScStatus` ogranicz wartość do 500 i uruchom kwerendę, jak pokazano poniżej:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>Wyświetlanie appserviceconsoleLogs z kwerendą dziennika

Teraz, gdy potwierdziłeś HTTP 500s, przyjrzyjmy się standardowemu wynikowi/błędom z aplikacji. Te dzienniki znajdują się w "AppServiceConsoleLogs".

(1) `+` Kliknij, aby utworzyć nową kwerendę. 

(2) Kliknij dwukrotnie `AppServiceConsoleLogs` tabelę `Run`i kliknij przycisk . 

Ponieważ konwersja pięciu obrazów powoduje błędy serwera, możesz sprawdzić, czy aplikacja `ResultDescription` również zapisuje błędy, filtrując pod kątem błędów, jak pokazano poniżej:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

W `ResultDescription` kolumnie zostanie wyświetlony następujący błąd:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Dołącz do aplikacjiHTTPLogs i AppServiceConsoleLogs

Teraz, gdy zidentyfikowano zarówno HTTP 500s, jak i błędy standardowe, musisz potwierdzić, czy istnieje korelacja między tymi wiadomościami. Następnie należy połączyć tabele razem na `TimeGenerated`podstawie sygnatury czasowej, .

> [!NOTE]
> Przygotowano kwerendę, która wykonuje następujące czynności:
>
> - Filtry HTTPLogs dla 500 błędów
> - Dzienniki konsoli kwerendy
> - Dołącza do stołów na`TimeGenerated`
>

Uruchom zapytanie:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

W `ResultDescription` kolumnie zobaczysz następujący błąd w tym samym czasie, co błędy serwera sieci web:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

Komunikat stwierdza, że pamięć została wyczerpana `process.php`w wierszu 20 . Teraz potwierdzono, że aplikacja spowodowała błąd podczas błędu HTTP 500. Przyjrzyjmy się kodowi, aby zidentyfikować problem.

## <a name="identify-the-error"></a>Identyfikowanie błędu

W katalogu lokalnym otwórz `process.php` i spójrz na wiersz 20. 

```php
imagepng($imgArray[$x], $filename);
```

Pierwszy argument, `$imgArray[$x]`jest zmienną gospodarstwa wszystkie JPG (w pamięci) wymagających konwersji. Jednak `imagepng` tylko obraz jest konwertowany, a nie wszystkie obrazy. Wstępne ładowanie obrazów nie jest konieczne i może powodować wyczerpanie pamięci, co prowadzi do http 500s. Zaktualizujmy kod, aby załadować obrazy na żądanie, aby sprawdzić, czy rozwiązuje problem. Następnie można poprawić kod, aby rozwiązać problem z pamięcią.

## <a name="fix-the-app"></a>Napraw aplikację

### <a name="update-locally-and-redeploy-the-code"></a>Lokalne aktualizowanie i ponowne wdrażanie kodu

Należy wprowadzić następujące `process.php` zmiany, aby obsłużyć wyczerpanie pamięci:

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Zatwierdź zmiany w narzędziu Git, a następnie wypchnij zmiany kodu na platformę Azure.

```bash
git commit -am "Load images on-demand in process.php"
git push azure master
```

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do `http://<app-name>.azurewebsites.net`. 

Konwertowanie obrazów nie powinno już powodować błędów HTTP 500.

![Aplikacja PHP uruchomiona w usłudze Azure App Service](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

Usuń ustawienie diagnostyczne za pomocą następującego polecenia:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Konfigurowanie aplikacji sieci Web za pomocą usługi Azure Monitor
> * Wysłane dzienniki do usługi Log Analytics
> * Używane zapytania dziennika do identyfikowania błędów aplikacji sieci Web i rozwiązywania ich

## <a name="next-steps"></a><a name="nextsteps"></a>Kolejne kroki
* [Dzienniki zapytań za pomocą usługi Azure Monitor](../../azure-monitor/log-query/log-query-overview.md)
* [Rozwiązywanie problemów z usługą Azure App Service w programie Visual Studio](../troubleshoot-dotnet-visual-studio.md)
* [Analizowanie dzienników aplikacji w hdinsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
