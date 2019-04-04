---
title: Włączanie rejestrowania diagnostycznego dla aplikacji — usłudze Azure App Service
description: Dowiedz się, jak włączyć rejestrowanie diagnostyczne i dodać Instrumentację do aplikacji, a także jak uzyskać dostęp do informacji rejestrowanych przez system Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 37455c278d665d05636ec120ca91b76153e53d16
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894922"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Włączanie rejestrowania diagnostycznego dla aplikacji w usłudze Azure App Service
## <a name="overview"></a>Przegląd
Platforma Azure udostępnia wbudowaną funkcję diagnostyki, która pomaga w debugowaniu [aplikację usługi app Service](https://go.microsoft.com/fwlink/?LinkId=529714). W tym artykule dowiesz się, jak włączyć rejestrowanie diagnostyczne i dodać Instrumentację do aplikacji, a także jak uzyskać dostęp do informacji rejestrowanych przez system Azure.

W tym artykule wykorzystano [witryny Azure portal](https://portal.azure.com) i wiersza polecenia platformy Azure, aby pracować z dzienników diagnostycznych. Aby uzyskać informacje na temat pracy z dzienników diagnostycznych przy użyciu programu Visual Studio, zobacz [Rozwiązywanie problemów z platformy Azure w programie Visual Studio](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Diagnostyka serwera sieci Web i diagnostyki aplikacji
Usługa App Service oferuje funkcje diagnostyczne dla rejestrowanie informacji z serwera sieci web i aplikacji sieci web. Są one logicznie oddzielone w **diagnostyką serwerów w sieci web** i **programu application diagnostics**.

### <a name="web-server-diagnostics"></a>Diagnostyka serwera sieci Web
Można włączyć lub wyłączyć następujące rodzaje dzienników:

* **Szczegółowe rejestrowanie błędów** — szczegółowe informacje na temat każde żądanie, które powoduje kod stanu HTTP 400 lub nowszej. Może on zawierać informacje, które mogą pomóc ustalić, dlaczego serwer zwrócił kod błędu. Jeden plik HTML jest generowany dla każdego błędu, w systemie plików aplikacji i maksymalnie 50 błędów (pliki) są zachowywane. Jeżeli liczba plików HTML przekracza 50, 26 najstarsze pliki są automatycznie usuwane.
* **Nie powiodło się żądanie śledzenia** — szczegółowe informacje dotyczące żądań zakończonych niepowodzeniem, w tym śledzenia komponenty używani do przetwarzania żądania i czasu trwania w poszczególnych składnikach. Jest to przydatne, jeśli chcesz zwiększyć wydajność witryny lub izolowania określonego błędu HTTP. Jeden folder jest generowany dla każdego błędu w systemie plików aplikacji. Zasady przechowywania plików są takie same jak szczegółowy komunikat o błędzie logowania powyżej.
* **Rejestrowanie serwera w sieci Web** — informacje o transakcji HTTP za pomocą [rozszerzonym formacie W3C dziennika pliku](/windows/desktop/Http/w3c-logging). Jest to przydatne, podczas określania ogólnego metryki witryn, takie jak liczba żądań obsłużonych lub ile żądań pochodzących z określonego adresu IP.

### <a name="application-diagnostics"></a>Diagnostyka aplikacji
Usługa Application diagnostics można przechwytywać informacji generowanych przez aplikację sieci web. Aplikacje ASP.NET mogą używać [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) klasy do rejestrowania informacji w dzienniku diagnostyki aplikacji. Na przykład:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

W czasie wykonywania możesz pobrać te dzienniki w celu ułatwienia rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługi Azure App Service w programie Visual Studio](troubleshoot-dotnet-visual-studio.md).

Usługa App Service rejestruje informacje o wdrożeniu podczas publikowania zawartości do aplikacji. Odbywa się automatycznie i nie ma żadnych ustawień konfiguracji dla rejestrowania wdrożenia. Rejestrowanie wdrażania pozwala określić, dlaczego wdrożenie nie powiodło się. Na przykład jeśli używany jest skrypt wdrożenia niestandardowego, może być umożliwia rejestrowanie wdrożenia Sprawdź, dlaczego skrypt jest możliwe.

## <a name="enablediag"></a>Jak włączyć diagnostykę
Aby włączyć diagnostykę w [witryny Azure portal](https://portal.azure.com), przejdź do strony aplikacji i kliknij przycisk **Ustawienia > dzienniki diagnostyczne**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Część dzienników](./media/web-sites-enable-diagnostic-log/logspart.png)

Po włączeniu **programu application diagnostics**, możesz również wybrać **poziom**. W poniższej tabeli przedstawiono kategorie dzienników, które każdy poziom zawiera:

| Poziom| Uwzględnione Rejestruj kategorie |
|-|-|
|**Disabled (Wyłączony)** | Brak |
|**Błąd** | Błąd krytyczny |
|**Ostrzeżenie** | Ostrzeżenie, błąd krytyczny|
|**Informacje** | Info, Warning, błąd krytyczny|
|**Pełne** | Śledzenia, debugowania, informacje, ostrzeżenie, błąd krytyczny (wszystkie kategorie) |
|-|-|

Aby uzyskać **rejestrowanie aplikacji**, można włączyć opcję systemu pliku tymczasowego na potrzeby debugowania. Ta opcja powoduje wyłączenie automatycznie w ciągu 12 godzin. Można również włączyć opcję magazynu obiektów blob umożliwia wybór kontenera obiektów blob, będą zapisywane dzienniki.

> [!NOTE]
> Obecnie tylko Dzienniki aplikacji .NET można zapisać w magazynie obiektów blob. Java, PHP, Node.js, Python, dzienniki aplikacji mogą być przechowywane tylko w systemie plików (bez modyfikacji kodu do zapisywania dzienników magazynu zewnętrznego).
>
>

Aby uzyskać **rejestrowanie serwera sieci Web**, możesz wybrać **magazynu** lub **system plików**. Wybieranie **magazynu** służy do wybierania konta magazynu i kontener obiektów blob, które dzienniki są zapisywane. 

Jeśli dzienniki są przechowywane w systemie plików, pliki mogą być dostęp do FTP, lub pobrane jako archiwum Zip przy użyciu wiersza polecenia platformy Azure.

Domyślnie dzienniki nie są automatycznie usuwane (z wyjątkiem produktów **rejestrowanie aplikacji (system plików)**). Aby automatycznie usunąć dzienniki, ustaw **okres przechowywania (dni)** pola.

> [!NOTE]
> Jeśli użytkownik [ponowne generowanie kluczy dostępu do konta magazynu](../storage/common/storage-create-storage-account.md), musisz zresetować konfigurację rejestrowania odpowiednich do używania kluczy zaktualizowane. W tym celu:
>
> 1. W **Konfiguruj** karcie należy ustawić funkcji rejestrowania odpowiednich **poza**. Zapisywanie ustawień użytkownika.
> 2. Włącz rejestrowanie na obiekt blob z konta magazynu ponownie. Zapisywanie ustawień użytkownika.
>
>

Dowolne kombinacje file system lub usługi blob Storage można włączyć w tym samym czasie i mieć osobny dziennik konfiguracje poziomu. Na przykład możesz rejestrować błędy i ostrzeżenia do magazynu obiektów blob jako rozwiązaniem długoterminowym rejestrowania podczas włączania rejestrowania w systemie plików z poziomem pełne.

Gdy zarówno w lokalizacji magazynu zapewnia te same informacje podstawowe dla zarejestrowanych zdarzeń **magazynu obiektów blob** rejestruje informacje dodatkowe, takie jak identyfikator wystąpienia, identyfikator wątku i bardziej szczegółową sygnatura czasowa (format taktu) niż rejestrowania **system plików**.

> [!NOTE]
> Informacje przechowywane w **magazynu obiektów blob** może zostać oceniony jedynie przy użyciu klienta usługi storage lub aplikacji, która bezpośrednio może współpracować z tych systemów magazynowania. Na przykład programu Visual Studio 2013 zawiera Eksploratora magazynu, który może służyć do eksplorowania usługi blob storage i HDInsight dostęp do danych przechowywanych w magazynie obiektów blob. Możesz również zapisywać dane aplikacji, która uzyskuje dostęp do usługi Azure Storage przy użyciu jednej z [zestawami SDK Azure](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a> Jak: Dzienniki pobierania
Informacje diagnostyczne są przechowywane w systemie plików aplikacji są dostępne bezpośrednio za pomocą protokołu FTP. Można go również pobrać jako archiwum Zip, przy użyciu wiersza polecenia platformy Azure.

Struktury katalogów, które dzienniki są przechowywane w jest następująca:

* **Dzienniki aplikacji** -/LogFiles/aplikacji /. Ten folder zawiera jeden lub więcej plików tekstowych zawierających informacji generowanych przez rejestrowanie aplikacji.
* **Nie powiodło się żądanie ślady** -/ LogFiles/W3SVC ### /. Ten folder zawiera pliku XSL i co najmniej jeden plik XML. Upewnij się, pobrać pliku XSL do tego samego katalogu, jak pliki XML, ponieważ plik XSL zapewnia funkcje dotyczące formatowania i filtrowania zawartości plików XML podczas wyświetlania w przeglądarce Internet Explorer.
* **Szczegółowe dzienniki błędów** -/LogFiles/DetailedErrors /. Ten folder zawiera jeden lub więcej plików htm, które zapewniają szczegółowe informacje, które wystąpiły błędy HTTP.
* **Dzienniki serwera Web** -/LogFiles/http/RawLogs. Ten folder zawiera jeden lub więcej plików tekstowych formatowana przy użyciu [rozszerzonym formacie W3C dziennika pliku](/windows/desktop/Http/w3c-logging).
* **Dzienniki wdrożenia** -/ LogFiles/Git. Ten folder zawiera dzienniki generowane przez procesy wewnętrznego wdrażania używane przez usługę Azure App Service, a także dzienniki dla wdrożenia Git. Można również znaleźć dzienniki wdrożenia w obszarze D:\home\site\deployments.

### <a name="ftp"></a>FTP

Aby otworzyć połączenie FTP do serwera FTP swojej aplikacji, zobacz [wdrażanie aplikacji w usłudze Azure App Service przy użyciu protokołu FTP/S](deploy-ftp.md).

Po nawiązaniu połączenia do serwera FTP/S w swojej aplikacji otwórz **LogFiles** folder, w którym są przechowywane pliki dziennika.

### <a name="download-with-azure-cli"></a>Pobieranie z wiersza polecenia platformy Azure
Aby pobrać pliki dziennika przy użyciu interfejsu wiersza polecenia platformy Azure, otwórz nowy wiersz polecenia, programu PowerShell, Bash lub sesji terminalowej i wprowadź następujące polecenie:

    az webapp log download --resource-group resourcegroupname --name appname

To polecenie zapisuje w dziennikach aplikacji o nazwie "appname" w pliku o nazwie **webapp_logs.zip** w bieżącym katalogu.

> [!NOTE]
> Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure lub nie został skonfigurowany do korzystania z subskrypcji platformy Azure, zobacz [instrukcje wiersza polecenia platformy Azure użyj](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Instrukcje: Wyświetlanie dzienników w usłudze Application Insights
Visual Studio Application Insights udostępnia narzędzia, filtrowanie i przeszukiwanie dzienników oraz korelacji dzienniki z żądaniami oraz innymi zdarzeniami.

1. Dodaj zestaw Application Insights SDK do projektu w programie Visual Studio.
   * W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie Dodaj usługę Application Insights. Interfejs przeprowadzi Cię przez kroki, które obejmują tworzenie zasobu usługi Application Insights. [Dowiedz się więcej](../azure-monitor/app/asp-net.md)
2. Dodaj pakiet odbiornik śledzenia do projektu.
   * Kliknij prawym przyciskiem myszy projekt i wybierz polecenie Zarządzaj pakietami NuGet. Wybierz `Microsoft.ApplicationInsights.TraceListener` [Dowiedz się więcej](../azure-monitor/app/asp-net-trace-logs.md)
3. Przekaż swój projekt i uruchomić go w celu wygenerowania danych dziennika.
4. W [witryny Azure portal](https://portal.azure.com/), przejdź do nowego zasobu usługi Application Insights i Otwórz **wyszukiwania**. Powinny być widoczne dane dziennika oraz żądania, użycia i inną telemetrią. Dane telemetryczne może potrwać kilka minut: kliknij przycisk Odśwież. [Dowiedz się więcej](../azure-monitor/app/diagnostic-search.md)

[Więcej informacji na temat wydajności śledzenia za pomocą usługi Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a> Jak: Strumieniowe przesyłanie dzienników
Podczas tworzenia aplikacji, jest często warto wyświetlać informacje rejestrowania w czasie niemal rzeczywistym. Informacje o rejestrowaniu można przesyłać strumieniowo do środowiska deweloperskiego przy użyciu wiersza polecenia platformy Azure.

> [!NOTE]
> Niektóre rodzaje rejestrowania bufor zapisu do pliku dziennika, co może skutkować zdarzeń poza kolejnością w strumieniu. Na przykład wpis dziennika aplikacji, który występuje, gdy użytkownik odwiedzi strony mogą być wyświetlane w usłudze stream przed odpowiadający mu wpis dziennika HTTP dla żądania strony.
>
> [!NOTE]
> Przesyłanie strumieniowe dzienników również przesyła strumieniowo informacje zapisane do pliku tekstowego, wszystkie przechowywane w **D:\\macierzystego\\LogFiles\\**  folderu.
>
>

### <a name="streaming-with-azure-cli"></a>Przesyłanie strumieniowe z wiersza polecenia platformy Azure
Przesyłanie strumieniowe rejestrowanie informacji, otwórz nowy wiersz polecenia, programu PowerShell, Bash lub sesji terminalowej i wprowadź następujące polecenie:

    az webapp log tail --name appname --resource-group myResourceGroup

To polecenie łączy do przetworzenia aplikacji o nazwie "appname" i rozpocząć przesyłanie strumieniowe informacje do okna, w momencie wystąpienia zdarzenia dziennika, w aplikacji. Wszystkie informacje zapisane pliki kończące się na .txt, log lub .htm, które są przechowywane w katalogu /LogFiles (d:/home/logfiles) jest przesyłany strumieniowo do konsoli lokalnej.

Aby odfiltrować określone zdarzenia, takie jak błędy, użyj **— filtr** parametru. Na przykład:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Aby odfiltrować typów określonego dziennika, takich jak HTTP, użyj **— ścieżka** parametru. Na przykład:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure lub nie został skonfigurowany do korzystania z subskrypcji platformy Azure, zobacz [instrukcje wiersza polecenia platformy Azure użyj](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Jak: Zrozumienie dzienniki diagnostyczne
### <a name="application-diagnostics-logs"></a>Dzienniki diagnostyki aplikacji
Usługa Application diagnostics przechowuje informacje w określonym formacie dla aplikacji .NET, w zależności od tego, czy są przechowywane dzienniki do pliku systemu lub blob storage. 

Podstawowy zestaw przechowywanych danych jest taka sama w przypadku obu typów magazynu — Data i godzina wystąpienia zdarzenia, identyfikator procesu, który zdarzenia, typ zdarzenia (informacje, ostrzeżenie, błąd) oraz komunikatów o zdarzeniach. Korzystanie z systemu plików na potrzeby przechowywania dzienników jest przydatne, gdy potrzebujesz uzyskać natychmiastowy dostęp do rozwiązania problemu, ponieważ pliki dziennika są aktualizowane niemal natychmiast. Magazyn obiektów blob jest używany w celu jego archiwizacji, ponieważ pliki są buforowane, a następnie opróżniany do kontenera magazynu, zgodnie z harmonogramem.

**System plików**

Każdy wiersz logowania do systemu plików lub odebranych przy użyciu przesyłania strumieniowego znajduje się w następującym formacie:

    {Date}  PID[{process ID}] {event type/level} {message}

Na przykład zdarzenie błędu zostanie wyświetlony podobny do poniższego przykładu:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Logowanie do systemu plików zawiera podstawowe informacje z trzech dostępnych metod udostępnia tylko czasu, identyfikator procesu, poziom zdarzenia i komunikat.

**Blob Storage**

Gdy logujesz się do magazynu obiektów blob, dane są przechowywane w formacie wartości rozdzielanych przecinkami (CSV). Dodatkowe pola są rejestrowane w celu zapewnienia bardziej szczegółowe informacje o zdarzeniu. Następujące właściwości są używane dla każdego wiersza w woluminie CSV:

| Nazwa właściwości | Format wartości / |
| --- | --- |
| Date |Data i godzina wystąpienia zdarzenia |
| Poziom |Poziom zdarzenia (na przykład błąd, ostrzeżenie, informacje) |
| ApplicationName |Nazwa aplikacji |
| Identyfikator wystąpienia |Wystąpienie aplikacji, które zdarzenie |
| EventTickCount |Data i godzina wystąpienia zdarzenia, format taktu (większą precyzję) |
| Identyfikator zdarzenia |Identyfikator zdarzenia tego zdarzenia<p><p>Wartość domyślna to 0, jeśli żaden określony |
| Identyfikator PID |Identyfikator procesu |
| identyfikatora TID |Identyfikator wątku wątku, który jest generowane zdarzenie |
| Komunikat |Komunikat szczegółów zdarzenia |

Dane przechowywane w obiekcie blob powinien wyglądać podobnie do poniższego przykładu:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Dla platformy ASP.NET Core rejestrowanie odbywa się przy użyciu [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) dostawcy dzienniku dodatkowe depozyty dostawcy plików w kontenerze obiektów blob. Aby uzyskać więcej informacji, zobacz [platformy ASP.NET Core rejestrowania na platformie Azure](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#logging-in-azure).
>
>

### <a name="failed-request-traces"></a>Nie powiodło się żądanie śladów
Śledzenie żądań zakończonych niepowodzeniem są przechowywane w plikach XML o nazwie **fr ### .xml**. Aby ułatwić wyświetlać zarejestrowane informacje o nazwie arkusz stylów XSL **freb.xsl** znajduje się w tym samym katalogu co pliki XML. Po otwarciu pliki XML w programie Internet Explorer programu Internet Explorer używa arkusza stylów XSL, zapewnienie sformatowane wyświetlanie informacji o śledzeniu, podobny do poniższego przykładu:

![Żądanie zakończone niepowodzeniem w przeglądarce](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> Prosty sposób, aby wyświetlić ślady sformatowanych żądań zakończonych niepowodzeniem jest przejście na stronę aplikacji w portalu. Z menu po lewej stronie wybierz **diagnozowanie i rozwiązywanie problemów**, a następnie wyszukaj **nie powiodło się dzienniki śledzenia żądań**, następnie kliknij ikonę, aby przeglądać i wyświetlać śledzenia ma.
>

### <a name="detailed-error-logs"></a>Dzienniki szczegółowy komunikat o błędzie
Dzienniki szczegółowy komunikat o błędzie są dokumenty HTML, które zawierają bardziej szczegółowe informacje dotyczące błędów protokołu HTTP, które wystąpiły. Ponieważ są one po prostu dokumentów HTML, ich można wyświetlić w przeglądarce sieci web.

### <a name="web-server-logs"></a>Dzienniki serwera sieci Web
Dzienniki serwera sieci web są formatowane przy użyciu [rozszerzonym formacie W3C dziennika pliku](/windows/desktop/Http/w3c-logging). Te informacje mogą być odczytywane za pomocą edytora tekstu lub analizowany za pomocą narzędzi takich jak [analizator dzienników](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Dzienniki generowane przez usługę Azure App Service nie obsługują **s-computername**, **s-ip**, lub **cs-version** pola.
>
>

## <a name="nextsteps"></a> Następne kroki
* [Jak monitorować usługi Azure App Service](web-sites-monitor.md)
* [Rozwiązywanie problemów z usługi Azure App Service w programie Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizowanie dzienników aplikacji w HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
