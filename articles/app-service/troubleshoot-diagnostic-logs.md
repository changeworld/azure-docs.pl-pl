---
title: Włączanie rejestrowania diagnostycznego dla aplikacji — Azure App Service
description: Dowiedz się, jak włączyć rejestrowanie diagnostyczne i dodać instrumentację do aplikacji, a także jak uzyskać dostęp do informacji rejestrowanych przez platformę Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: af6d8b61c5d49ae219e90513abb93185f957222e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074054"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Włączanie rejestrowania diagnostycznego dla aplikacji w Azure App Service
## <a name="overview"></a>Omówienie
Platforma Azure udostępnia wbudowaną diagnostykę, która pomaga w debugowaniu [aplikacji App Service](https://go.microsoft.com/fwlink/?LinkId=529714). W tym artykule dowiesz się, jak włączyć rejestrowanie diagnostyczne i dodać instrumentację do aplikacji, a także jak uzyskać dostęp do informacji rejestrowanych przez platformę Azure.

W tym artykule są wykorzystywane [Azure Portal](https://portal.azure.com) i interfejs wiersza polecenia platformy Azure do pracy z dziennikami diagnostycznymi. Aby uzyskać informacje dotyczące pracy z dziennikami diagnostycznymi przy użyciu programu Visual Studio, zobacz [Rozwiązywanie problemów z platformą Azure w programie Visual Studio](troubleshoot-dotnet-visual-studio.md)

## <a name="whatisdiag"></a>Diagnostyka serwera sieci Web i Diagnostyka aplikacji
App Service udostępnia funkcje diagnostyczne do rejestrowania informacji z serwera sieci Web i aplikacji sieci Web. Są one logicznie rozdzielone na **diagnostykę serwera sieci Web** i **diagnostykę aplikacji**.

### <a name="web-server-diagnostics"></a>Diagnostyka serwera sieci Web
Można włączać lub wyłączać następujące rodzaje dzienników:

* **Szczegóły rejestrowania błędów** — szczegółowe informacje dotyczące każdego żądania, które powoduje, że kod stanu HTTP 400 lub nowszy. Może on zawierać informacje, które mogą pomóc w ustaleniu, dlaczego serwer zwrócił kod błędu. Jeden plik HTML jest generowany dla każdego błędu w systemie plików aplikacji, a do 50 błędów (plików) są zachowywane. Gdy liczba plików HTML przekracza 50, najstarsze 26 plików zostanie automatycznie usunięta.
* **Śledzenie nieudanych żądań** — szczegółowe informacje o żądaniach zakończonych niepowodzeniem, w tym śledzenia składników usług IIS używanych do przetwarzania żądania oraz czasu wykonywanego w każdym składniku. Jest to przydatne, jeśli chcesz zwiększyć wydajność lokacji lub odizolować określony błąd HTTP. Jeden folder jest generowany dla każdego błędu w systemie plików aplikacji. Zasady przechowywania plików są takie same jak w przypadku szczegółowego rejestrowania błędów powyżej.
* **Rejestrowanie serwera sieci Web** — informacje o transakcjach http przy użyciu [rozszerzonego formatu W3C plików dziennika](/windows/desktop/Http/w3c-logging). Jest to przydatne podczas określania ogólnych metryk lokacji, takich jak Liczba obsłużonych żądań lub ile żądań pochodzi z określonego adresu IP.

### <a name="application-diagnostics"></a>Diagnostyka aplikacji
Diagnostyka aplikacji umożliwia przechwytywanie informacji generowanych przez aplikację sieci Web. Aplikacje ASP.NET mogą używać klasy [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) do rejestrowania informacji w dzienniku diagnostyki aplikacji. Na przykład:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

W środowisku uruchomieniowym można pobrać te dzienniki, aby ułatwić rozwiązywanie problemów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów Azure App Service w programie Visual Studio](troubleshoot-dotnet-visual-studio.md).

App Service również rejestruje informacje o wdrożeniu podczas publikowania zawartości w aplikacji. Odbywa się to automatycznie i nie ma ustawień konfiguracji rejestrowania wdrożenia. Rejestrowanie wdrożenia pozwala określić przyczynę niepowodzenia wdrożenia. Na przykład jeśli używasz niestandardowego skryptu wdrożenia, możesz użyć rejestrowania wdrożenia, aby określić, dlaczego skrypt kończy się niepowodzeniem.

## <a name="enablediag"></a>Jak włączyć diagnostykę
Aby włączyć diagnostykę w [Azure Portal](https://portal.azure.com), przejdź do strony aplikacji, a następnie kliknij pozycję **Ustawienia > dzienników diagnostycznych**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Część dzienników](./media/web-sites-enable-diagnostic-log/logspart.png)

Po włączeniu **diagnostyki aplikacji**należy również wybrać **poziom**. W poniższej tabeli przedstawiono kategorie dzienników na każdym poziomie:

| Poziom| Uwzględnione kategorie dzienników |
|-|-|
|**Disabled (Wyłączone)** | Brak |
|**Error** | Błąd, krytyczny |
|**Ostrzeżenie** | Ostrzeżenie, błąd, krytyczny|
|**Informacje o** | Informacje, ostrzeżenie, błąd, krytyczne|
|**Pełne** | Trace, Debug, info, Warning, Error, krytyczny (wszystkie kategorie) |
|-|-|

W przypadku **rejestrowania aplikacji**można tymczasowo włączyć opcję systemu plików na potrzeby debugowania. Ta opcja wyłącza się automatycznie w ciągu 12 godzin. Możesz również włączyć opcję magazynu obiektów blob, aby wybrać kontener obiektów blob, w którym mają zostać zapisane dzienniki.

> [!NOTE]
> Obecnie tylko Dzienniki aplikacji .NET mogą być zapisywane w magazynie obiektów BLOB. Java, PHP, Node. js Dzienniki aplikacji w języku Python mogą być przechowywane tylko w systemie plików (bez modyfikacji kodu do zapisu dzienników do magazynu zewnętrznego).
>
>

Aby można było **rejestrować serwer sieci Web**, wybierz pozycję **Magazyn** lub **system plików**. Wybranie pozycji **Magazyn** umożliwia wybranie konta magazynu, a następnie kontenera obiektów blob, w którym zapisywane są dzienniki. 

W przypadku przechowywania dzienników w systemie plików dostęp do plików można uzyskać za pomocą protokołu FTP lub pobrać jako archiwum zip przy użyciu interfejsu wiersza polecenia platformy Azure.

Domyślnie dzienniki nie są automatycznie usuwane (z wyjątkiem **rejestrowania aplikacji (FileSystem)** ). Aby automatycznie usunąć dzienniki, ustaw wartość pola **okres przechowywania (dni)** .

> [!NOTE]
> W przypadku ponownego [wygenerowania kluczy dostępu konta magazynu](../storage/common/storage-create-storage-account.md)należy zresetować odpowiednią konfigurację rejestrowania, aby użyć zaktualizowanych kluczy. W tym celu:
>
> 1. Na karcie **Konfiguracja** Ustaw odpowiednią funkcję rejestrowania na **off**. Zapisz ustawienie.
> 2. Ponownie Włącz rejestrowanie do obiektu BLOB konta magazynu. Zapisz ustawienie.
>
>

Dowolna kombinacja systemu plików lub magazynu obiektów BLOB może być włączona w tym samym czasie i ma poszczególne konfiguracje poziomu dziennika. Na przykład możesz chcieć rejestrować błędy i ostrzeżenia w usłudze BLOB Storage jako długoterminowe rozwiązanie do rejestrowania, jednocześnie włączając rejestrowanie systemu plików z poziomu pełnego.

Chociaż obie lokalizacje przechowywania zawierają te same podstawowe informacje dotyczące rejestrowanych zdarzeń, usługa **BLOB Storage** rejestruje dodatkowe informacje, takie jak identyfikator wystąpienia, identyfikator wątku i bardziej szczegółowy znacznik czasu (w formacie Tick) niż rejestrowanie w **systemie plików**.

> [!NOTE]
> Dostęp do informacji przechowywanych w usłudze **BLOB Storage** można uzyskać tylko przy użyciu klienta magazynu lub aplikacji, która może bezpośrednio współpracować z tymi systemami magazynu. Na przykład Visual Studio 2013 zawiera Eksplorator usługi Storage, których można użyć do eksplorowania usługi BLOB Storage, a Usługa HDInsight może uzyskać dostęp do danych przechowywanych w usłudze BLOB Storage. Możesz również napisać aplikację, która uzyskuje dostęp do usługi Azure Storage, korzystając z jednego z [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a>Jak: Dzienniki pobierania
Dostęp do informacji diagnostycznych przechowywanych w systemie plików aplikacji można uzyskać bezpośrednio przy użyciu protokołu FTP. Można go również pobrać jako archiwum zip przy użyciu interfejsu wiersza polecenia platformy Azure.

Struktura katalogów, w której są przechowywane dzienniki, jest następująca:

* **Dzienniki aplikacji** —/LogFiles/Application/. Ten folder zawiera jeden lub więcej plików tekstowych zawierających informacje generowane przez funkcję rejestrowania aplikacji.
* **Ślady nieudanych żądań** —/LogFiles/W3SVC # # # # # # # # #/. Ten folder zawiera plik XSL i jeden lub więcej plików XML. Upewnij się, że plik XSL został pobrany do tego samego katalogu co pliki XML, ponieważ plik XSL zawiera funkcje formatowania i filtrowania zawartości plików XML wyświetlanych w programie Internet Explorer.
* **Szczegółowe dzienniki błędów** —/LogFiles/DetailedErrors/. Ten folder zawiera jeden lub więcej plików. htm, które zawierają obszerne informacje dotyczące wszystkich błędów HTTP, które wystąpiły.
* **Dzienniki serwera sieci Web** —/LogFiles/http/RawLogs. Ten folder zawiera jeden lub więcej plików tekstowych sformatowanych przy użyciu [rozszerzonego formatu W3C plików dziennika](/windows/desktop/Http/w3c-logging).
* **Dzienniki wdrażania** —/LogFiles/git. Ten folder zawiera dzienniki wygenerowane przez procesy wdrażania wewnętrznego używane przez Azure App Service, a także dzienniki wdrożeń usługi git. Dzienniki wdrożenia można również znaleźć w obszarze D:\home\site\deployments.

### <a name="ftp"></a>Protokół FTP

Aby otworzyć połączenie FTP z serwerem FTP aplikacji, zobacz [wdrażanie aplikacji do Azure App Service przy użyciu protokołu FTP/s](deploy-ftp.md).

Po nawiązaniu połączenia z serwerem FTP/S aplikacji otwórz folder **LogFiles** , w którym są przechowywane pliki dziennika.

### <a name="download-with-azure-cli"></a>Pobierz za pomocą interfejsu wiersza polecenia platformy Azure
Aby pobrać pliki dziennika przy użyciu interfejsu wiersza polecenia platformy Azure, Otwórz nowy wiersz polecenia, PowerShell, bash lub sesję terminala, a następnie wprowadź następujące polecenie:

    az webapp log download --resource-group resourcegroupname --name appname

To polecenie zapisuje Dzienniki aplikacji o nazwie "nazwa_aplikacji" w pliku o nazwie **webapp_logs. zip** w bieżącym katalogu.

> [!NOTE]
> Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure lub nie został on skonfigurowany do korzystania z subskrypcji platformy Azure, zobacz [jak korzystać z interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Instrukcje: Wyświetlanie dzienników w Application Insights
Program Visual Studio Application Insights udostępnia narzędzia do filtrowania i wyszukiwania dzienników oraz skorelowania dzienników z żądaniami i innymi zdarzeniami.

1. Dodaj zestaw Application Insights SDK do projektu w programie Visual Studio.
   * W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie Dodaj Application Insights. Interfejs zawiera instrukcje dotyczące tworzenia zasobów Application Insights. [Dowiedz się więcej](../azure-monitor/app/asp-net.md)
2. Dodaj pakiet odbiornika śledzenia do projektu.
   * Kliknij prawym przyciskiem myszy projekt i wybierz polecenie Zarządzaj pakietami NuGet. Wybierz `Microsoft.ApplicationInsights.TraceListener` pozycję [Dowiedz się więcej](../azure-monitor/app/asp-net-trace-logs.md)
3. Przekaż projekt i uruchom go, aby wygenerować dane dziennika.
4. W [Azure Portal](https://portal.azure.com/)przejdź do nowego zasobu Application Insights i Otwórz pozycję **Wyszukaj**. Powinny pojawić się dane dziennika wraz z żądaniem, użyciem i inną telemetrią. Niektóre dane telemetryczne mogą potrwać kilka minut: kliknij przycisk Odśwież. [Dowiedz się więcej](../azure-monitor/app/diagnostic-search.md)

[Dowiedz się więcej o śledzeniu wydajności za pomocą Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a>Jak: Strumieniowe przesyłanie dzienników
Podczas tworzenia aplikacji często warto zobaczyć informacje o rejestrowaniu w czasie niemal rzeczywistym. Informacje o rejestrowaniu można przesyłać do środowiska deweloperskiego przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!NOTE]
> Niektóre typy buforów rejestrowania zapisu w pliku dziennika, co może spowodować brak kolejności zdarzeń w strumieniu. Na przykład wpis dziennika aplikacji, który występuje, gdy użytkownik odwiedzi stronę, może zostać wyświetlony w strumieniu przed odpowiednim wpisem dziennika HTTP dla żądania strony.
>
> [!NOTE]
> Przesyłanie strumieniowe dzienników również przesyła strumieniowo informacje zapisane w dowolnym pliku tekstowym przechowywanym w folderze **D:\\\\ Home\\LogFiles** .
>
>

### <a name="streaming-with-azure-cli"></a>Przesyłanie strumieniowe za pomocą interfejsu wiersza polecenia platformy Azure
Aby przesłać strumieniowo informacje o rejestrowaniu, Otwórz nowy wiersz polecenia, PowerShell, bash lub sesję terminala i wprowadź następujące polecenie:

    az webapp log tail --name appname --resource-group myResourceGroup

To polecenie umożliwia nawiązanie połączenia z aplikacją o nazwie "nazwa_aplikacji" i rozpoczęcie przesyłania strumieniowego informacji do okna jako zdarzenia dziennika występujące w aplikacji. Wszelkie informacje zapisane w plikach kończących się na. txt,. log lub. htm, które są przechowywane w katalogu/LogFiles (d:/Home/LogFiles), są przesyłane strumieniowo do konsoli lokalnej.

Aby odfiltrować określone zdarzenia, takie jak błędy, użyj parametru **--Filter** . Przykład:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Aby filtrować określone typy dzienników, takie jak HTTP, użyj parametru **--Path** . Na przykład:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure lub nie został on skonfigurowany do korzystania z subskrypcji platformy Azure, zobacz [jak korzystać z interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a>Jak: Omówienie dzienników diagnostycznych
### <a name="application-diagnostics-logs"></a>Dzienniki diagnostyki aplikacji
Program Application Diagnostics przechowuje informacje w określonym formacie dla aplikacji .NET, w zależności od tego, czy dzienniki są przechowywane w systemie plików czy w magazynie obiektów BLOB. 

Podstawowy zestaw przechowywanych danych jest taki sam dla obu typów magazynów — datę i godzinę wystąpienia zdarzenia, identyfikator procesu, który wygenerował zdarzenie, typ zdarzenia (informacje, ostrzeżenie, błąd) i komunikat o zdarzeniu. Korzystanie z systemu plików dla magazynu dzienników jest przydatne, gdy potrzebujesz natychmiastowego dostępu do rozwiązania problemu, ponieważ pliki dziennika są aktualizowane niemal natychmiastowo. Magazyn obiektów BLOB jest używany na potrzeby archiwizowania, ponieważ pliki są buforowane, a następnie opróżniane do kontenera magazynu zgodnie z harmonogramem.

**System plików**

Każdy wiersz zarejestrowany w systemie plików lub otrzymany przy użyciu przesyłania strumieniowego ma następujący format:

    {Date}  PID[{process ID}] {event type/level} {message}

Na przykład zdarzenie błędu będzie wyglądać podobnie do poniższego przykładu:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Rejestrowanie w systemie plików zapewnia najbardziej podstawowe informacje o trzech dostępnych metodach, zapewniając tylko godzinę, identyfikator procesu, poziom zdarzenia i komunikat.

**Blob Storage**

Podczas rejestrowania w usłudze BLOB Storage dane są przechowywane w formacie wartości rozdzielanych przecinkami (CSV). Dodatkowe pola są rejestrowane w celu zapewnienia bardziej szczegółowych informacji o zdarzeniu. Dla każdego wiersza w woluminie CSV są używane następujące właściwości:

| Nazwa właściwości | Wartość/format |
| --- | --- |
| Date |Data i godzina wystąpienia zdarzenia |
| Poziom |Poziom zdarzenia (na przykład błąd, ostrzeżenie, informacje) |
| ApplicationName |Nazwa aplikacji |
| Identyfikator wystąpienia |Wystąpienie aplikacji, na której wystąpiło zdarzenie |
| EventTickCount |Data i godzina wystąpienia zdarzenia w formacie osi (większa precyzja) |
| Identyfikator zdarzenia |Identyfikator zdarzenia tego zdarzenia<p><p>Wartość domyślna to 0, jeśli nie określono |
| Identyfikatora |Identyfikator procesu |
| TID |Identyfikator wątku wątku, który wygenerował zdarzenie. |
| Message |Komunikat szczegółowy o zdarzeniu |

Dane przechowywane w obiekcie blob wyglądają podobnie do poniższego przykładu:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> W przypadku ASP.NET Core rejestrowanie odbywa się przy użyciu dostawcy [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) ten dostawca złoży dodatkowe pliki dziennika do kontenera obiektów BLOB. Aby uzyskać więcej informacji, zobacz [rejestrowanie ASP.NET Core na platformie Azure](/aspnet/core/fundamentals/logging).
>
>

### <a name="failed-request-traces"></a>Ślady nieudanych żądań
Ślady nieudanych żądań są przechowywane w plikach XML o nazwie **fr # # # # # #. XML**. Aby ułatwić wyświetlanie zarejestrowanych informacji, arkusz stylów XSL o nazwie **freb. xsl** znajduje się w tym samym katalogu, w którym znajdują się pliki XML. W przypadku otwarcia jednego z plików XML w programie Internet Explorer program Internet Explorer używa arkusza stylów XSL do zapewnienia sformatowanego wyświetlania informacji śledzenia, podobnie jak w poniższym przykładzie:

![żądanie zakończone niepowodzeniem wyświetlane w przeglądarce](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> W prosty sposób można wyświetlić sformatowane śledzenie nieudanych żądań, aby przejść do strony aplikacji w portalu. Z menu po lewej stronie wybierz opcję **Diagnozuj i rozwiąż problemy**, a następnie wyszukaj **dzienniki śledzenia niepomyślnych żądań**, a następnie kliknij ikonę, aby przeglądać i wyświetlić odpowiedni ślad.
>

### <a name="detailed-error-logs"></a>Szczegółowe dzienniki błędów
Szczegółowe dzienniki błędów to dokumenty HTML, które zawierają bardziej szczegółowe informacje na temat błędów HTTP, które wystąpiły. Ponieważ są one po prostu dokumenty HTML, można je przeglądać za pomocą przeglądarki sieci Web.

### <a name="web-server-logs"></a>Dzienniki serwera sieci Web
Dzienniki serwera sieci Web są formatowane przy użyciu [rozszerzonego formatu W3C plików dziennika](/windows/desktop/Http/w3c-logging). Te informacje można odczytać za pomocą edytora tekstu lub przeanalizować przy użyciu narzędzi, takich jak [parser dzienników](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Dzienniki utworzone przez Azure App Service nie obsługują pól **s-ComputerName**, **s-IP**ani **cs-version** .
>
>

## <a name="nextsteps"></a> Następne kroki
* [Jak monitorować Azure App Service](web-sites-monitor.md)
* [Rozwiązywanie problemów Azure App Service w programie Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizowanie dzienników aplikacji w usłudze HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
