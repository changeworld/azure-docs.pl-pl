---
title: Najważniejsze wskazówki dotyczące aplikacji Node.js i rozwiązywanie problemów
description: Zapoznaj się z najlepszymi rozwiązaniami i krokami rozwiązywania problemów dla aplikacji Node.js uruchomionych w usłudze Azure App Service.
author: msangapu-msft
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 682884d11b298a97e27056af3c10802dfd410e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430561"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Przewodnik po najlepszych rozwiązaniach i rozwiązywaniu problemów z aplikacjami węzłów w usłudze Azure App Service Windows

W tym artykule poznasz najważniejsze wskazówki i kroki rozwiązywania problemów dla [aplikacji węzłów uruchomionych](app-service-web-get-started-nodejs.md) w usłudze Azure App Service (z [iisnode).](https://github.com/azure/iisnode)

> [!WARNING]
> Należy zachować ostrożność podczas korzystania z kroków rozwiązywania problemów w witrynie produkcyjnej. Zaleca się rozwiązywanie problemów z aplikacją w konfiguracji nieprodukcyjnej, na przykład miejsca przejściowego i po rozwiązaniu problemu, zamienić miejsce przejściowe z gniazdem produkcyjnym.
>

## <a name="iisnode-configuration"></a>Konfiguracja usługi IISNODE

Ten [plik schematu](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) zawiera wszystkie ustawienia, które można skonfigurować dla iisnode. Niektóre ustawienia, które są przydatne dla aplikacji:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

To ustawienie steruje liczbą procesów węzłów uruchamianych dla aplikacji IIS. Wartość domyślna to 1. Można uruchomić dowolną liczbę node.exes jako liczbę vCPU maszyny Wirtualnej, zmieniając wartość na 0. Zalecana wartość wynosi 0 dla większości aplikacji, dzięki czemu można używać wszystkich procesorów wirtualnych na komputerze. Node.exe jest jednowątkowy, więc jeden node.exe zużywa maksymalnie 1 procesora wirtualnego. Aby uzyskać maksymalną wydajność z aplikacji węzła, należy użyć wszystkich procesorów wirtualnych.

### <a name="nodeprocesscommandline"></a>węzełProcessCommandLine

To ustawienie steruje ścieżką do pliku node.exe. Tę wartość można ustawić tak, aby wskazywała wersję node.exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

To ustawienie steruje maksymalną liczbą równoczesnych żądań wysyłanych przez iisnode do każdego pliku node.exe. W usłudze Azure App Service wartością domyślną jest Infinite. Można skonfigurować wartość w zależności od liczby żądań aplikacji odbiera i jak szybko aplikacja przetwarza każde żądanie.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

To ustawienie steruje maksymalną liczbą ponownych prób iisnode nawiązywanie połączenia na nazwanym potoku w celu wysłania żądań do pliku node.exe. To ustawienie w połączeniu z namedPipeConnectionRetryDelay określa całkowity limit czasu każdego żądania w iisnode. Wartość domyślna to 200 w usłudze Azure App Service. Całkowity limit czasu w sekundach = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>nazwaPipeConnectionRetryDelay

To ustawienie steruje czasem (w ms) iisnode czeka między każdym ponowić próbę wysłania żądania do node.exe za pomocą nazwanego potoku. Wartość domyślna to 250 ms.
Całkowity limit czasu w sekundach = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

Domyślnie całkowity limit czasu w iisnode w usłudze \* Azure App Service wynosi 200 250 ms = 50 sekund.

### <a name="logdirectory"></a>dziennikDirectory

To ustawienie steruje katalogiem, w którym iisnode loguje stdout/stderr. Wartością domyślną jest iisnode, która jest względem głównego katalogu skryptów (katalogu, w którym znajduje się główny serwer.js)

### <a name="debuggerextensiondll"></a>debugerExtensionDll

To ustawienie określa, jakiej wersji iisnode node-inspector używa podczas debugowania aplikacji węzła. Obecnie iisnode-inspector-0.7.3.dll i iisnode-inspector.dll są tylko dwiema prawidłowymi wartościami dla tego ustawienia. Wartością domyślną jest iisnode-inspector-0.7.3.dll. Wersja iisnode-inspector-0.7.3.dll używa node-inspector-0.7.3 i używa gniazd internetowych. Włącz gniazda sieci web w aplikacji WebApp platformy Azure, aby używać tej wersji. Zobacz <https://ranjithblogs.azurewebsites.net/?p=98> więcej szczegółów na temat konfigurowania iisnode do korzystania z nowego inspektora węzła.

### <a name="flushresponse"></a>flushResponse (odpowiadanie na opróżnienie)

Domyślne zachowanie usługi IIS jest, że buforuje dane odpowiedzi do 4 MB przed opróżnieniem lub do końca odpowiedzi, w zależności od tego, co nastąpi wcześniej. iisnode oferuje ustawienie konfiguracji w celu zastąpienia tego zachowania: aby opróżnić fragment treści jednostki odpowiedzi, gdy tylko iisnode otrzyma go z node.exe, musisz ustawić iisnode/@flushResponse atrybut w web.config na "true":

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Włącz opróżnianie każdego fragmentu treści jednostki odpowiedzi dodaje obciążenie wydajności, które zmniejsza przepływność systemu o ~5% (od wersji 0.1.13). Najlepiej, aby zakres tego ustawienia tylko do punktów końcowych, które `<location>` wymagają przesyłania strumieniowego odpowiedzi (na przykład przy użyciu elementu w web.config)

Oprócz tego dla aplikacji strumieniowych należy również ustawić responseBufferLimit obsługi iisnode do 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles (pliki obserwowane)

Średnik oddzielone listę plików, które są obserwowane pod kątem zmian. Wszelkie zmiany w pliku powoduje, że aplikacja do recyklingu. Każdy wpis składa się z opcjonalnej nazwy katalogu oraz wymaganej nazwy pliku, które są względem katalogu, w którym znajduje się główny punkt wejścia aplikacji. Symbole wieloznaczne są dozwolone tylko w części nazwy pliku. Wartością domyślną jest `*.js;iisnode.yml`.

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Wartość domyślna to false. Jeśli ta opcja jest włączona, aplikacja węzła może połączyć\_\_się z nazwanym potokiem (zmienną środowiskową IISNODE CONTROL PIPE) i wysłać komunikat "recykling". Powoduje to, że w3wp do recyklingu z wdziękiem.

### <a name="idlepageouttimeperiod"></a>IdlePageOutTimePeriod

Wartość domyślna to 0, co oznacza, że ta funkcja jest wyłączona. Po ustawieniu wartości większej niż 0, iisnode będzie strona wszystkie procesy podrzędne co "idlePageOutTimePeriod" w milisekundach. Zapoznaj się [z dokumentacją,](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) aby dowiedzieć się, co oznacza page out. To ustawienie jest przydatne w przypadku aplikacji, które zużywają dużą ilość pamięci i chcą od czasu do czasu przestronicować pamięć na dysku, aby zwolnić pamięć RAM.

> [!WARNING]
> Należy zachować ostrożność podczas włączania następujących ustawień konfiguracji w aplikacjach produkcyjnych. Zaleca się, aby nie włączać ich w zastosowaniach produkcyjnych na żywo.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Wartość domyślna to false. Jeśli ustawiona na true, iisnode `iisnode-debug` dodaje nagłówek odpowiedzi `iisnode-debug` HTTP do każdej odpowiedzi HTTP, która wysyła wartość nagłówka jest adresem URL. Poszczególne fragmenty informacji diagnostycznych można uzyskać, patrząc na fragment adresu URL, jednak wizualizacja jest dostępna przez otwarcie adresu URL w przeglądarce.

### <a name="loggingenabled"></a>logowanieWzdłuż

To ustawienie steruje rejestrowaniem stdout i stderr przez iisnode. Iisnode przechwytuje stdout/stderr z procesów węzłów uruchamia i zapisuje do katalogu określonego w ustawieniu "logDirectory". Po włączeniu tej funkcji aplikacja zapisuje dzienniki do systemu plików i w zależności od ilości rejestrowania wykonywane przez aplikację, może to mieć wpływ na wydajność.

### <a name="deverrorsenabled"></a>devErrorsEnabled devErrorsEnabled devErrorsEnabled devEr

Wartość domyślna to false. Po ustawieniu na wartość true iisnode wyświetla w przeglądarce kod stanu HTTP i kod błędu Win32. Kod win32 jest pomocny w debugowaniu niektórych typów problemów.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debugowanieEnabled (nie włączaj w miejscu produkcji na żywo)

To ustawienie steruje funkcją debugowania. Iisnode jest zintegrowany z inspektorem węzła. Włączenie tego ustawienia umożliwia debugowanie aplikacji węzła. Po włączeniu tego ustawienia iisnode tworzy pliki inspektora węzłów w katalogu "debugerVirtualDir" w pierwszym żądaniu debugowania do aplikacji węzła. Inspektora węzłów można załadować, `http://yoursite/server.js/debug`wysyłając żądanie do . Segment adresu URL debugowania można kontrolować za pomocą ustawienia "debuggerPathSegment". Domyślnie debuggerPathSegment='debug'. Można ustawić `debuggerPathSegment` identyfikator GUID, na przykład, tak, że jest trudniejsze do wykrycia przez innych.

Przeczytaj [aplikacje Debugowania node.js w systemie Windows,](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) aby uzyskać więcej informacji na temat debugowania.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scenariusze i zalecenia/rozwiązywanie problemów

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Moja aplikacja węzła jest wykonywanie nadmiernych połączeń wychodzących

Wiele aplikacji będzie chciał nawiązywać połączenia wychodzące w ramach ich regularnej pracy. Na przykład po wejściu żądania aplikacja węzła będzie chciał skontaktować się z interfejsem API REST w innym miejscu i uzyskać pewne informacje do przetworzenia żądania. Należy użyć agenta keep alive podczas wykonywania wywołań http lub https. Można użyć modułu agentkeepalive jako agenta keep alive podczas wykonywania tych wywołań wychodzących.

Moduł agentkeepalive zapewnia, że gniazda są ponownie na maszynie wirtualnej aplikacji sieci Web platformy Azure. Tworzenie nowego gniazda dla każdego żądania wychodzącego dodaje obciążenie do aplikacji. Posiadanie gniazda ponownego użycia aplikacji dla żądań wychodzących gwarantuje, że aplikacja nie przekracza maxSockets, które są przydzielane na maszynę wirtualną. Zalecenie dotyczące usługi Azure App Service jest ustawienie agentKeepAlive maxSockets wartość w sumie (4 wystąpienia node.exe \* 40 maxSockets/instance) 160 gniazd na maszynę wirtualną.

Przykładowa [konfiguracja agentKeepALive:](https://www.npmjs.com/package/agentkeepalive)

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> W tym przykładzie przyjęto założenie, że masz 4 node.exe uruchomiony na maszynie wirtualnej. Jeśli masz inną liczbę node.exe uruchomiony na maszynie Wirtualnej, należy odpowiednio zmodyfikować ustawienie maxSockets.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Moja aplikacja węzła zużywa zbyt dużo procesora

Możesz otrzymać zalecenie z usługi Azure App Service w portalu dotyczące wysokiego zużycia procesora. Można również skonfigurować monitory do oglądania dla [niektórych metryk](web-sites-monitor.md). Podczas sprawdzania użycia procesora CPU na [pulpicie nawigacyjnym portalu Azure](../azure-monitor/app/web-monitor-performance.md)sprawdź wartości MAX dla procesora CPU, aby nie przegapić wartości szczytowych.
Jeśli uważasz, że aplikacja zużywa zbyt dużo procesora CPU i nie można wyjaśnić, dlaczego, można profilu aplikacji węzła, aby dowiedzieć się.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Profilowanie aplikacji węzła w usłudze Azure App Service za pomocą programu V8-Profiler

Załóżmy na przykład, że masz aplikację hello world, którą chcesz profilować w następujący sposób:

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Przejdź do witryny konsoli debugowania`https://yoursite.scm.azurewebsites.net/DebugConsole`

Przejdź do katalogu wwwroot. Zostanie wyświetlony wiersz polecenia, jak pokazano w poniższym przykładzie:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Uruchom polecenie `npm install v8-profiler`.

To polecenie instaluje profiler v8 w katalogu modułów węzłów\_i wszystkie jego zależności.
Teraz edytuj plik server.js, aby profilować aplikację.

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Poprzedni kod profiluje writeconsolelog funkcji, a następnie zapisuje dane wyjściowe profilu do pliku "profile.cpuprofile" w witrynie wwwroot. Wyślij żądanie do aplikacji. Widzisz plik "profil.cpuprofile" utworzony pod witryną wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Pobierz ten plik i otwórz go za pomocą Narzędzi Chrome F12. Naciśnij klawisz F12 w Chrome, a następnie wybierz kartę **Profile.** Wybierz przycisk **Wczytaj.** Wybierz pobrany plik profilu profile.cpu. Kliknij na właśnie załadowany profil.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Widać, że 95% czasu została wykorzystana przez WriteConsoleLog funkcji. Dane wyjściowe pokazują również dokładne numery wierszy i pliki źródłowe, które spowodowały problem.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Moja aplikacja węzła zużywa zbyt dużo pamięci

Jeśli aplikacja zużywa zbyt dużo pamięci, zostanie wyświetlonego w portalu powiadomienie z usługi Azure App Service o wysokim zużyciu pamięci. Monitory można skonfigurować do oglądania niektórych [wskaźników](web-sites-monitor.md). Podczas sprawdzania użycia pamięci na [pulpicie nawigacyjnym portalu Azure](../azure-monitor/app/web-monitor-performance.md)należy sprawdzić wartości MAX dla pamięci, aby nie przegapić wartości szczytowych.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Wykrywanie wycieków i diff sterty dla node.js

Można użyć [node-memwatch,](https://github.com/lloyd/node-memwatch) aby ułatwić identyfikację przecieków pamięci.
Można zainstalować `memwatch` podobnie jak v8-profiler i edytować kod do przechwytywania i różnice sterty do identyfikacji przecieków pamięci w aplikacji.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Mój node.exe są coraz zabitych losowo

Istnieje kilka powodów, dla których node.exe jest zamykany losowo:

1. Aplikacja zgłasza nieprzechoczone wyjątki —\\\\sprawdź d: home LogFiles\\Application\\logging-errors.txt pliku szczegółów na wyjątek. Ten plik ma śledzenia stosu, aby pomóc debugowania i naprawić aplikację.
2. Aplikacja zużywa zbyt dużo pamięci, co wpływa na inne procesy od rozpoczęcia. Jeśli całkowita pamięć maszyny Wirtualnej jest bliska 100%, node.exe może zostać zabity przez menedżera procesów. Menedżer procesów zabija niektóre procesy, aby inne procesy otrzymały szansę na wykonanie pracy. Aby rozwiązać ten problem, profilu aplikacji przecieków pamięci. Jeśli aplikacja wymaga dużych ilości pamięci, skalowanie do większej maszyny Wirtualnej (co zwiększa pamięci RAM dostępne dla maszyny Wirtualnej).

### <a name="my-node-application-does-not-start"></a>Moja aplikacja węzła nie uruchamia się

Jeśli aplikacja zwraca 500 błędów po uruchomieniu, może istnieć kilka powodów:

1. Plik Node.exe nie jest obecny we właściwej lokalizacji. Sprawdź ustawienie nodeProcessCommandLine.
2. Główny plik skryptu nie jest obecny we właściwej lokalizacji. Sprawdź web.config i upewnij się, że nazwa głównego pliku skryptu w sekcji obsługi jest zgodna z głównym plikiem skryptu.
3. Konfiguracja web.config nie jest poprawna – sprawdź nazwy/wartości ustawień.
4. Cold Start — uruchomienie aplikacji trwa zbyt długo. Jeśli aplikacja trwa dłużej niż (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 sekund, iisnode zwraca błąd 500. Zwiększ wartości tych ustawień, aby dopasować czas rozpoczęcia aplikacji, aby zapobiec iisnode z limitu czasu i zwracania błędu 500.

### <a name="my-node-application-crashed"></a>Moja aplikacja węzła uległa awarii

Aplikacja zgłasza nieprzechoczone wyjątki `d:\\home\\LogFiles\\Application\\logging-errors.txt` — sprawdź plik, aby uzyskać szczegółowe informacje na temat wyjątku. Ten plik ma śledzenia stosu, aby pomóc zdiagnozować i naprawić aplikację.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Uruchomienie aplikacji dla mojego węzła zajmuje zbyt dużo czasu (Cold Start)

Częstą przyczyną długich czasów uruchamiania aplikacji jest\_duża liczba plików w modułach węzłów. Aplikacja próbuje załadować większość z tych plików podczas uruchamiania. Domyślnie, ponieważ pliki są przechowywane w udziale sieciowym w usłudze Azure App Service, ładowanie wielu plików może zająć trochę czasu.
Oto kilka rozwiązań, które przyspieszają ten proces:

1. Upewnij się, że masz strukturę zależności płaskich i nie ma zduplikowanych zależności za pomocą npm3 do zainstalowania modułów.
2. Spróbuj z opóźnieniem\_załadować moduły węzłów i nie ładować wszystkie moduły na początku aplikacji. Do modułów z opóźnieniem obciążenia, wywołanie require('module') powinny być wykonane, gdy rzeczywiście potrzebujesz modułu w ramach funkcji przed pierwszym wykonaniem kodu modułu.
3. Usługa Azure App Service oferuje funkcję o nazwie lokalnej pamięci podręcznej. Ta funkcja kopiuje zawartość z udziału sieciowego na dysk lokalny na maszynie wirtualnej. Ponieważ pliki są lokalne, czas\_ładowania modułów węzłów jest znacznie szybszy.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE http status i podstat

`cnodeconstants` [Plik źródłowy](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) zawiera listę wszystkich możliwych kombinacji stanu/podstatu, które iisnode może powrócić z powodu błędu.

Włącz FREB dla aplikacji, aby zobaczyć kod błędu win32 (upewnij się, że freb jest włączony tylko w zakładach nieprodukcyjnych ze względu na wydajność).

| Stan http | Podstatus http | Możliwy powód? |
| --- | --- | --- |
| 500 |1000 |Wystąpił problem z wysłaniem żądania do aplikacji IISNODE — sprawdź, czy uruchomiono plik node.exe. Node.exe mógł ulec awarii podczas uruchamiania. Sprawdź konfigurację web.config pod kątem błędów. |
| 500 |1001 |- Win32Error 0x2 - Aplikacja nie odpowiada na adres URL. Sprawdź reguły przepisywania adresów URL lub sprawdź, czy aplikacja express ma zdefiniowane poprawne trasy. - Win32Error 0x6d — nazwany potok jest zajęty — node.exe nie akceptuje żądań, ponieważ potok jest zajęty. Sprawdź wysokie zużycie procesora. - Inne błędy - sprawdź, czy node.exe uległ awarii. |
| 500 |1002 |Node.exe crashed –\\sprawdź\\d:\\strona główna LogFiles logging-errors.txt dla śledzenia stosu. |
| 500 |1003 |Problem z konfiguracją potoku — konfiguracja nazwanego potoku jest niepoprawna. |
| 500 |1004-1018 |Wystąpił błąd podczas wysyłania żądania lub przetwarzania odpowiedzi na adres/z pliku node.exe. Sprawdź, czy plik node.exe uległ awarii. sprawdź\\d:\\strona\\główna LogFiles log-errors.txt dla śledzenia stosu. |
| 503 |1000 |Za mało pamięci, aby przydzielić więcej nazwanych połączeń potoku. Sprawdź, dlaczego aplikacja zużywa tyle pamięci. Sprawdź wartość ustawienia maxConcurrentRequestsPerProcess. Jeśli nie jest nieskończona i masz wiele żądań, należy zwiększyć tę wartość, aby zapobiec temu błędowi. |
| 503 |1001 |Nie można wysłać żądania do pliku node.exe, ponieważ aplikacja jest przetwarzana. Po recyklingu aplikacji wnioski powinny być doręczane normalnie. |
| 503 |1002 |Sprawdź kod błędu win32 z rzeczywistej przyczyny — nie można wysłać żądania do pliku node.exe. |
| 503 |1003 |Nazwany potok jest zbyt zajęty — sprawdź, czy node.exe zużywa nadmierny procesor CPU |

Node.exe ma ustawienie `NODE_PENDING_PIPE_INSTANCES`o nazwie . W usłudze Azure App Service ta wartość jest ustawiona na 5000. Co oznacza, że node.exe może akceptować 5000 żądań naraz na nazwanym potoku. Ta wartość powinna być wystarczająco dobra dla większości aplikacji węzłów uruchomionych w usłudze Azure App Service. Nie powinieneś widzieć 503.1003 w usłudze Azure App Service ze względu na wysoką wartość`NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Więcej zasobów

Skorzystaj z tych łączy, aby dowiedzieć się więcej o aplikacjach node.js w usłudze Azure App Service.

* [Rozpoczynanie pracy z aplikacjami internetowymi środowiska Node.js w usłudze Azure App Service](app-service-web-get-started-nodejs.md)
* [How to debug a Node.js web app in Azure App Service (Jak debugować aplikację internetową Node.js w usłudze Azure App Service)](https://blogs.msdn.microsoft.com/azureossds/2018/08/03/debugging-node-js-apps-on-azure-app-services/)
* [Using Node.js Modules with Azure applications (Używanie modułów Node.js z aplikacjami platformy Azure)](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js (Aplikacje internetowe w usłudze Azure App Service: Node.js)](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js Developer Center (Centrum deweloperów środowiska Node.js)](../nodejs-use-node-modules-azure-apps.md)
* [Exploring the Super Secret Kudu Debug Console (Szczegółowe informacje o ściśle tajnej konsoli debugowania aparatu Kudu)](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
