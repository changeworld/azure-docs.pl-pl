---
title: Włączanie rejestrowania diagnostyki dla aplikacji sieci web w usłudze aplikacji Azure
description: Dowiedz się, jak włączyć rejestrowanie diagnostyczne i dodać Instrumentacji do aplikacji, a także sposobu uzyskania dostępu do informacji rejestrowane przez platformę Azure.
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
ms.openlocfilehash: 15c580a026495d11ffdeb161d4bf0793850040f5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32158770"
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Włączanie rejestrowania diagnostyki dla aplikacji sieci web w usłudze aplikacji Azure
## <a name="overview"></a>Przegląd
Platforma Azure oferuje wbudowane narzędzia diagnostyczne, aby pomóc w debugowaniu [aplikacji sieci web usługi aplikacji](http://go.microsoft.com/fwlink/?LinkId=529714). W tym artykule dowiesz się, jak włączyć rejestrowanie diagnostyczne i dodać Instrumentacji do aplikacji, a także sposobu uzyskania dostępu do informacji rejestrowane przez platformę Azure.

W tym artykule wykorzystano [portalu Azure](https://portal.azure.com), programu Azure PowerShell i interfejsu wiersza polecenia platformy Azure (Azure CLI) do pracy z dzienników diagnostycznych. Aby uzyskać informacje na temat pracy z dzienników diagnostycznych przy użyciu programu Visual Studio, zobacz [Rozwiązywanie problemów z platformy Azure w programie Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Diagnostyka serwera sieci Web i diagnostyki aplikacji
Aplikacje sieci web usługi aplikacji udostępniają funkcje diagnostyczne dla rejestrowanie informacji z serwera sieci web i aplikacji sieci web. Są one logicznie podzielone na **sieci web diagnostyki serwera** i **programu application diagnostics**.

### <a name="web-server-diagnostics"></a>Diagnostyka serwera sieci Web
Można włączyć lub wyłączyć następujące rodzaje dzienników:

* **Szczegółowe rejestrowanie błędów** — szczegółowe informacje o błędzie kodów stanu HTTP, które wskazania błędu (kod stanu 400 lub nowszej). Może on zawierać informacje, które mogą pomóc ustalić, dlaczego serwer zwrócił kod błędu.
* **Nie powiodło się żądanie śledzenia** — szczegółowe informacje dotyczące żądań zakończonych niepowodzeniem, w tym śladu używane do przetwarzania żądania oraz godzinę w poszczególnych składników składników usług IIS. Jest on przydatny, gdy chcesz zwiększyć wydajność witryny lub określić, co powoduje określonego błędu HTTP do zwrócenia.
* **Rejestrowanie serwera w sieci Web** — informacje o transakcjach HTTP przy użyciu [rozszerzonym formacie W3C dziennika pliku](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Jest przydatne w przypadku określenia ogólną metryki lokacji, takie jak liczba żądań obsłużonych lub liczbę żądań pochodzą z określonego adresu IP.

### <a name="application-diagnostics"></a>Diagnostyka aplikacji
Diagnostyki aplikacji umożliwia przechwytywanie informacji generowanych przez aplikację sieci web. Aplikacje ASP.NET mogą używać [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) klasy do rejestrowania informacji w dzienniku diagnostyki aplikacji. Na przykład:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

W czasie wykonywania mogą pobierać te dzienniki, aby pomóc w rozwiązywaniu problemów. Aby uzyskać więcej informacji, zobacz [Azure Rozwiązywanie problemów aplikacji sieci web w programie Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Aplikacje sieci web usługi aplikacji także rejestrować informacje na temat wdrażania podczas publikowania zawartości do aplikacji sieci web. Odbywa się automatycznie i nie ma żadnych ustawień konfiguracji dla rejestrowania wdrożenia. Rejestrowanie wdrożenia pozwala określić, dlaczego wdrożenia nie powiodło się. Na przykład jeśli używasz skryptu wdrażania niestandardowych, można użyć wdrożenia rejestrowania do ustalenia, dlaczego skrypt nie działa prawidłowo.

## <a name="enablediag"></a>Jak włączyć diagnostyki
Aby włączyć diagnostyki w [portalu Azure](https://portal.azure.com), przejdź do strony dla aplikacji sieci web i kliknij przycisk **Ustawienia > dzienniki diagnostyczne**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Część dzienników](./media/web-sites-enable-diagnostic-log/logspart.png)

Po włączeniu **programu application diagnostics**, możesz również wybrać **poziom**. To ustawienie umożliwia filtrowanie informacji przechwycone do **informacyjny**, **ostrzeżenie**, lub **błąd** informacji. Ustawieniem dla niego **pełne** rejestruje wszystkie informacje produkowane przez aplikację.

> [!NOTE]
> W przeciwieństwie do zmiany pliku web.config, włączanie diagnostyki aplikacji lub zmienianie poziomów dzienników diagnostycznych nie jest odtwarzana aplikacji jest uruchamiany w ramach domeny aplikacji.
>
>

Aby uzyskać **rejestrowanie aplikacji**, możesz włączyć opcję systemu pliku tymczasowego na potrzeby debugowania. Ta opcja powoduje wyłączenie automatycznie w ciągu 12 godzin. Można również włączyć opcję magazynu obiektów blob, aby wybrać zapisywanie dzienników do kontenera obiektów blob.

Dla **rejestrowanie pracy serwera sieci Web**, można wybrać **magazynu** lub **system plików**. Wybieranie **magazynu** służy do wybierania konta magazynu i kontener obiektów blob, który dzienniki są zapisywane. 

Jeśli dzienniki są przechowywane w systemie plików, pliki mogą być dostęp do FTP, lub pobrać jako archiwum Zip przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure (Azure CLI).

Domyślnie dzienniki nie są automatycznie usuwane (z wyjątkiem produktów **rejestrowania aplikacji (systemu plików)**). Aby automatycznie usunąć dzienników, ustaw **okresu przechowywania (dni)** pola.

> [!NOTE]
> Jeśli użytkownik [ponowne generowanie kluczy dostępu do konta magazynu](../storage/common/storage-create-storage-account.md), należy zresetować konfigurację odpowiednich rejestrowania, aby używać kluczy zaktualizowane. W tym celu:
>
> 1. W **Konfiguruj** Ustaw funkcji rejestrowania odpowiednich **poza**. Zapisz ustawienia.
> 2. Włącz rejestrowanie dla obiektu blob z konta magazynu lub tabeli ponownie. Zapisz ustawienia.
>
>

Dowolną kombinację systemu plików, Magazyn tabel lub magazynie obiektów blob można włączyć w tym samym czasie i mieć osobny dziennik konfiguracje poziomu. Możesz na przykład rejestrowanie błędów i ostrzeżeń do magazynu obiektów blob jako rozwiązanie długoterminowe rejestrowania, podczas włączania rejestrowania w systemie plików z poziomu verbose.

Podczas wszystkich trzech lokalizacji magazynu zapewniają te same informacje podstawowe dla zarejestrowane zdarzenia **tabeli magazynu** i **magazynu obiektów blob** rejestrować dodatkowe informacje, takie jak identyfikator wystąpienia, identyfikator wątku i bardziej szczegółowe znacznik czasu (w formacie znaczników) niż rejestrowanie, aby **system plików**.

> [!NOTE]
> Informacje przechowywane w **tabeli magazynu** lub **magazynu obiektów blob** można uzyskać tylko za pomocą klienta magazynu lub aplikacji, która bezpośrednio może współpracować z tych systemów pamięci masowej. Na przykład Eksploratora magazynu, który może służyć do eksplorowania tabeli lub obiektu blob magazynu zawiera programu Visual Studio 2013 i usługi HDInsight można uzyskać dostępu do danych przechowywanych w magazynie obiektów blob. Możesz także zapisać aplikację, która uzyskuje dostęp do usługi Azure Storage za pomocą jednego z [zestawów SDK usługi Azure](/downloads/#).
>
> [!NOTE]
> Można również włączyć diagnostyki z programu Azure PowerShell przy użyciu **AzureWebsite zestaw** polecenia cmdlet. Jeśli nie zainstalowano programu Azure PowerShell lub nie zostały skonfigurowane do korzystania z subskrypcji Azure, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0).
>
>

## <a name="download"></a> Porady: pobieranie dzienników
Informacje diagnostyczne są przechowywane w systemie plików aplikacji sieci web są dostępne bezpośrednio za pomocą protokołu FTP. Można go również pobrać jako archiwum Zip przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

Dzienniki są przechowywane w strukturą katalogów jest następujący:

* **Dzienniki aplikacji** -/LogFiles/aplikacji /. Ten folder zawiera jeden lub więcej plików tekstowych, zawierający informacje o wygenerowane przez rejestrowanie aplikacji.
* **Nie powiodło się żądanie śladów** -/ LogFiles/W3SVC ### /. Ten folder zawiera plik XSL i co najmniej jeden plik XML. Upewnij się, Pobierz plik XSL do tego samego katalogu jako pliki XML, ponieważ plik XSL udostępnia funkcje dotyczące formatowania i filtrowanie zawartości plików XML, podczas wyświetlania w przeglądarce Internet Explorer.
* **Szczegółowe dzienniki błędów** -/LogFiles/DetailedErrors /. Ten folder zawiera jeden lub więcej plików htm, które zapewniają szczegółowe informacje, które wystąpiły błędy HTTP.
* **Dzienniki serwera w sieci Web** -/LogFiles/http/RawLogs. Ten folder zawiera jeden lub więcej plików tekst sformatowany przy użyciu [rozszerzonym formacie W3C dziennika pliku](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Dzienniki wdrożenia** -/ LogFiles/Git. Ten folder zawiera dzienniki generowane przez procesy wewnętrznego wdrażania aplikacji sieci web platformy Azure, a także wdrożeń Git w dziennikach. Można również znaleźć dzienniki wdrożenia w obszarze D:\home\site\deployments.

### <a name="ftp"></a>FTP

Aby otworzyć połączenie FTP do serwera FTP aplikacji, zobacz [wdrażanie aplikacji w usłudze Azure App Service przy użyciu FTP/S](app-service-deploy-ftp.md).

Po nawiązaniu połączenia z serwerem FTP/S aplikacji sieci web, otwórz **LogFiles** folderu, w którym będą przechowywane pliki dziennika.

### <a name="download-with-azure-powershell"></a>Pobierz przy użyciu programu Azure PowerShell
Pobierz pliki dziennika i uruchomić nowe wystąpienie programu Azure PowerShell, użyj następującego polecenia:

    Save-AzureWebSiteLog -Name webappname

To polecenie zapisuje dzienniki dla aplikacji sieci web, określony przez **— nazwa** parametru w pliku o nazwie **logs.zip** w bieżącym katalogu.

> [!NOTE]
> Jeśli nie zainstalowano programu Azure PowerShell lub nie zostały skonfigurowane do korzystania z subskrypcji Azure, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0).
>
>

### <a name="download-with-azure-command-line-interface"></a>Pobierz z interfejsu wiersza polecenia platformy Azure
Aby pobrać pliki dziennika przy użyciu interfejsu wiersza polecenia Azure, otwórz nowy wiersz polecenia, programu PowerShell, Bash lub sesję terminala i wprowadź następujące polecenie:

    az webapp log download --resource-group resourcegroupname --name webappname

To polecenie zapisuje dzienniki dla aplikacji sieci web o nazwie "webappname" w pliku o nazwie **diagnostics.zip** w bieżącym katalogu.

> [!NOTE]
> Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure (Azure CLI) lub nie zostały skonfigurowane do korzystania z subskrypcji Azure, zobacz [sposób użycia interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Porady: Wyświetl dzienniki w usłudze Application Insights
Visual Studio Application Insights udostępnia narzędzia do filtrowania i wyszukiwania dzienników oraz korelowanie dzienniki z żądaniami oraz innymi zdarzeniami.

1. Dodaj zestaw SDK usługi Application Insights do projektu programu Visual Studio.
   * W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie Dodaj usługę Application Insights. Interfejs prowadzi użytkownika przez kroki, które obejmują tworzenie zasobu usługi Application Insights. [Dowiedz się więcej](../application-insights/app-insights-asp-net.md)
2. Dodaj pakiet nasłuchującego śledzenia do projektu.
   * Kliknij prawym przyciskiem myszy projekt i wybierz polecenie Zarządzaj pakietami NuGet. Wybierz `Microsoft.ApplicationInsights.TraceListener` [Dowiedz się więcej](../application-insights/app-insights-asp-net-trace-logs.md)
3. Przekaż swój projekt, a następnie uruchom go, aby wygenerować dane dziennika.
4. W [portalu Azure](https://portal.azure.com/), przejdź do nowego zasobu usługi Application Insights i Otwórz **wyszukiwania**. Powinny być widoczne dane dziennika, wraz z żądania, użycia i innych danych telemetrycznych. Niektóre dane telemetryczne może potrwać kilka minut na odebranie: kliknij przycisk Odśwież. [Dowiedz się więcej](../application-insights/app-insights-diagnostic-search.md)

[Dowiedz się więcej na temat wydajności śledzenia z usługą Application Insights](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a> Porady: strumienia dzienników
Podczas tworzenia aplikacji, często jest przydatne wyświetlić informacje o rejestrowaniu w czasie niemal rzeczywistym. Informacje o rejestrowaniu można strumienia do środowiska deweloperskiego przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

> [!NOTE]
> Niektóre typy rejestrowania bufor zapisu do pliku dziennika, co może skutkować zdarzenia poza kolejnością w strumieniu. Na przykład wpis dziennika aplikacji, która występuje, gdy użytkownik odwiedza stronę mogą być wyświetlane w strumieniu przed odpowiadający mu wpis dziennika HTTP dla żądania strony.
>
> [!NOTE]
> Również dzienników przesyłania strumieniowego strumieni informacje zapisane w pliku tekstowym, wszystkie przechowywane w **D:\\macierzystego\\LogFiles\\**  folderu.
>
>

### <a name="streaming-with-azure-powershell"></a>Przesyłania strumieniowego przy użyciu programu Azure PowerShell
Informacje o rejestrowaniu strumienia i uruchomić nowe wystąpienie programu Azure PowerShell, użyj następującego polecenia:

    Get-AzureWebSiteLog -Name webappname -Tail

To polecenie łączy do aplikacji sieci web, określony przez **— nazwa** parametru i rozpocząć przesyłanie strumieniowe informacje do okna programu PowerShell w momencie wystąpienia zdarzenia dziennika w aplikacji sieci web. Wszystkie informacje zapisane pliki kończące się txt, log lub htm, które są przechowywane w katalogu /LogFiles (d: lub głównej/pliki dziennika) jest przesyłane strumieniowo do konsoli lokalnej.

Aby filtrować określonych zdarzeń, takich jak błędy, należy użyć **-komunikat** parametru. Na przykład:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Do filtrowania typów określonego dziennika, takich jak HTTP, użyj **-ścieżka** parametru. Na przykład:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Aby wyświetlić listę dostępnych ścieżek, użyj parametru - ListPath.

> [!NOTE]
> Jeśli nie zainstalowano programu Azure PowerShell lub nie zostały skonfigurowane do korzystania z subskrypcji Azure, zobacz [jak używać programu Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="streaming-with-azure-command-line-interface"></a>Przesyłanie strumieniowe z interfejsu wiersza polecenia platformy Azure
Do strumienia rejestrowanie informacji, otwórz nowy wiersz polecenia, programu PowerShell, Bash lub sesję terminala i wprowadź następujące polecenie:

    az webapp log tail --name webappname --resource-group myResourceGroup

To polecenie łączy do aplikacji sieci web o nazwie "webappname" i rozpocząć przesyłanie strumieniowe informacje do okna w momencie wystąpienia zdarzenia dziennika w aplikacji sieci web. Wszystkie informacje zapisane pliki kończące się txt, log lub htm, które są przechowywane w katalogu /LogFiles (d: lub głównej/pliki dziennika) jest przesyłane strumieniowo do konsoli lokalnej.

Aby filtrować określonych zdarzeń, takich jak błędy, należy użyć **— filtr** parametru. Na przykład:

    az webapp log tail --name webappname --resource-group myResourceGroup --filter Error

Do filtrowania typów określonego dziennika, takich jak HTTP, użyj **— ścieżka** parametru. Na przykład:

    az webapp log tail --name webappname --resource-group myResourceGroup --path http

> [!NOTE]
> Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure lub nie zostały skonfigurowane do korzystania z subskrypcji Azure, zobacz [jak można użyć interfejsu wiersza polecenia](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Porady: zrozumienie dzienników diagnostycznych
### <a name="application-diagnostics-logs"></a>Dzienniki diagnostyki aplikacji
Diagnostyka aplikacji są przechowywane informacje w określonym formacie aplikacji .NET, w zależności od tego, czy są przechowywane dzienniki systemu plików, Magazyn tabel lub magazynie obiektów blob. Podstawowy zestaw przechowywanych danych jest taka sama we wszystkich trzech typów magazynu - Data i godzina wystąpienia zdarzenia, identyfikator procesu, który utworzone zdarzenie, typu zdarzenia (informacje, ostrzeżenie, błąd) i komunikaty o zdarzeniach.

**System plików**

Każdym wierszu logowania do systemu plików lub odbierane przy użyciu przesyłania strumieniowego znajduje się w następującym formacie:

    {Date}  PID[{process ID}] {event type/level} {message}

Na przykład zdarzenie błędu pojawiały się podobnie do poniższego przykładu:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Rejestrowanie w systemie plików zapewnia podstawowe informacje z trzech dostępnych metod tylko czas, identyfikator procesu, poziom zdarzenia i komunikatów.

**Table Storage**

Po zalogowaniu się do magazynu tabel, dodatkowe właściwości są używane do ułatwienia wyszukiwania danych przechowywanych w tabeli, a także bardziej szczegółowe informacje o zdarzeniu. Następujące właściwości (kolumny) są używane dla każdej jednostki (wiersz) przechowywane w tabeli.

| Nazwa właściwości | Format wartości / |
| --- | --- |
| PartitionKey |Data/godzina zdarzenia w formacie yyyyMMddHH |
| RowKey |Wartość identyfikatora GUID, który unikatowo identyfikuje tę jednostkę |
| Sygnatura czasowa |Data i godzina wystąpienia zdarzenia |
| EventTickCount |Data i godzina wystąpienia zdarzenia, format znaczników (większą dokładność) |
| ApplicationName |Nazwa aplikacji sieci web |
| Poziom |Poziom zdarzenia (na przykład błąd, ostrzeżenie, informacje) |
| Identyfikator zdarzenia |Identyfikator zdarzenia to zdarzenie<p><p>Wartość domyślna to 0, jeśli nie określono żadnego |
| Identyfikator wystąpienia |Wystąpienie aplikacji sieci web, który wystąpił nawet na |
| Identyfikator procesu |Identyfikator procesu |
| TID |Identyfikator wątku wątku wytworzonego zdarzenia |
| Komunikat |Szczegóły komunikatu o zdarzeniu |

**Blob Storage**

Po zalogowaniu się do magazynu obiektów blob, dane są przechowywane w formacie wartości rozdzielanych przecinkami (CSV). Podobnie jak magazyn tabel dodatkowe pola są rejestrowane w celu zapewnienia bardziej szczegółowe informacje o zdarzeniu. Następujące właściwości są używane dla każdego wiersza w woluminie CSV:

| Nazwa właściwości | Format wartości / |
| --- | --- |
| Date |Data i godzina wystąpienia zdarzenia |
| Poziom |Poziom zdarzenia (na przykład błąd, ostrzeżenie, informacje) |
| ApplicationName |Nazwa aplikacji sieci web |
| Identyfikator wystąpienia |Wystąpienie aplikacji sieci web, w którym wystąpiło zdarzenie w |
| EventTickCount |Data i godzina wystąpienia zdarzenia, format znaczników (większą dokładność) |
| Identyfikator zdarzenia |Identyfikator zdarzenia to zdarzenie<p><p>Wartość domyślna to 0, jeśli nie określono żadnego |
| Identyfikator procesu |Identyfikator procesu |
| TID |Identyfikator wątku wątku wytworzonego zdarzenia |
| Komunikat |Szczegóły komunikatu o zdarzeniu |

Dane przechowywane w obiekcie blob będzie wyglądać podobnie do poniższego przykładu:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Pierwszy wiersz dziennika zawiera nagłówki kolumn, jak w tym przykładzie.
>
>

### <a name="failed-request-traces"></a>Nie powiodło się żądanie śledzenia
Dane śledzenia nieudanych żądań, są przechowywane w plikach XML o nazwie **fr ### .xml**. Aby ułatwić wyświetlać zarejestrowane informacje o nazwie arkusz stylów XSL **freb.xsl** znajduje się w tym samym katalogu co plik XML. Po otwarciu pliki XML w programie Internet Explorer program Internet Explorer używa arkusz stylów XSL zapewnienie sformatowany wyświetlanie informacji o śledzeniu, podobnie do poniższego przykładu:

![żądań zakończonych niepowodzeniem w przeglądarce](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Szczegółowe dzienniki błędów
Szczegółowe dzienniki błędów są dokumentów HTML, które zapewniają bardziej szczegółowe informacje o błędach HTTP, które wystąpiły. Ponieważ są one po prostu dokumentów HTML, ich można wyświetlić w przeglądarce sieci web.

### <a name="web-server-logs"></a>Dzienniki serwera sieci Web
Dzienniki serwera sieci web są sformatowane przy użyciu [rozszerzonym formacie W3C dziennika pliku](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Te informacje mogą być odczytywane za pomocą edytora tekstu lub analizować przy użyciu narzędzia, takie jak [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Dzienniki generowane przez aplikacje sieci web Azure nie obsługują **s-computername**, **s-ip**, lub **cs-version** pola.
>
>

## <a name="nextsteps"></a> Następne kroki
* [Jak monitorować aplikacje sieci Web](web-sites-monitor.md)
* [Rozwiązywanie problemów aplikacji sieci web platformy Azure w programie Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
* [Analizowanie dzienników aplikacji sieci web w usłudze HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> Jeśli chcesz zacząć korzystać z usługi Azure App Service przed utworzeniem konta platformy Azure, przejdź do artykułu [Try App Service](https://azure.microsoft.com/try/app-service/) (Wypróbuj usługę App Service), w którym wyjaśniono, jak od razu utworzyć początkową aplikację sieci Web o krótkim okresie istnienia w usłudze App Service. Bez kart kredytowych i bez zobowiązań.
>
>
