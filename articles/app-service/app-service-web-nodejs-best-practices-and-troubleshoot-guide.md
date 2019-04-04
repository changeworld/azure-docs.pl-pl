---
title: Najlepsze rozwiązania i rozwiązywanie problemów z platformą Node.js — usłudze Azure App Service
description: Poznaj najlepsze rozwiązania i kroki rozwiązywania problemów aplikacji node w usłudze Azure App Service.
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: ''
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.custom: seodec18
ms.openlocfilehash: 321dbf891c77007952f01b32bb509a15c2ac3e6f
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895787"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Najlepsze praktyki i przewodnik rozwiązywania problemów aplikacji node w usłudze Azure App Service Windows

W tym artykule dowiesz się, najważniejsze wskazówki i procedury rozwiązywania problemów z [aplikacji node](app-service-web-get-started-nodejs.md) uruchomiona w usłudze Azure App Service (przy użyciu [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Należy zachować ostrożność, korzystając z kroków rozwiązywania problemów na witrynę produkcyjną. Zaleca się Rozwiązywanie problemów z aplikacją na temat instalacji spoza środowiska produkcyjnego na przykład miejsce na tymczasową, a gdy problem zostanie rozwiązany, należy zamienić swoje miejsce przejściowe z z miejscem produkcyjnym.
>

## <a name="iisnode-configuration"></a>Konfiguracja programu IISNODE

To [pliku schematu](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) pokazuje wszystkie ustawienia, można skonfigurować dla programu iisnode. Niektóre ustawienia, które są przydatne w przypadku aplikacji:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

To ustawienie określa liczbę procesów węzła, które będą uruchamiane w poszczególnych aplikacji usług IIS. Wartość domyślna to 1. Można uruchomić dowolną liczbę node.exes jako liczbę procesorów wirtualnych vCPU Twojej maszyny Wirtualnej, zmieniając wartość 0. Zalecana wartość to 0 w przypadku większości aplikacji, dzięki czemu można używać wszystkich procesorów wirtualnych na tym komputerze. Node.exe jest jednowątkowym tak node.exe co zużywa więcej niż 1 Procesor wirtualny. Aby uzyskać maksymalną wydajność poza węzeł aplikację, chcesz użyć wszystkich procesorów wirtualnych.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

To ustawienie określa ścieżkę do node.exe. Można ustawić tę wartość, aby wskazać swoją wersję node.exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

To ustawienie określa maksymalną liczbę równoczesnych żądań wysyłane przez program iisnode do każdego node.exe. W usłudze Azure App Service wartość domyślna to nieskończone. Możesz skonfigurować wartości w zależności od tego, ile żądań, że Twoja aplikacja otrzyma i jak szybko aplikacji przetwarzania każdego żądania.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

To ustawienie określa maksymalną liczbę razy iisnode liczbę ponownych prób nawiązywania połączenia na nazwanym potoku do wysyłania żądań do node.exe. To ustawienie w połączeniu z namedPipeConnectionRetryDelay określa łącznego limitu czasu każdego żądania w ramach programu iisnode. Wartość domyślna to 200 w usłudze Azure App Service. Całkowity limit czasu w sekundach = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

To ustawienie steruje ilością czasu (w ms) program iisnode w tym czasie czeka między kolejnymi próbami wysyłać żądania do node.exe przez nazwany potok. Wartość domyślna to 250 ms.
Całkowity limit czasu w sekundach = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

Domyślnie łącznego limitu czasu w program iisnode w usłudze Azure App Service to 200 \* 250 ms = 50 sekund.

### <a name="logdirectory"></a>logDirectory

To ustawienie określa katalog, w którym program iisnode dzienników stdout/stderr. Wartość domyślna to programu iisnode, która jest określona względem katalogu głównego skryptu (gdzie występuje server.js głównego katalogu)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

To ustawienie określa, jakie wersje programu iisnode narzędzia node-inspector używa podczas debugowania aplikację node. Obecnie program iisnode-inspector — 0.7.3.dll i iisnode inspector.dll są tylko dwa prawidłowe wartości dla tego ustawienia. Wartość domyślna to 0.7.3.dll-inspector — program iisnode. Wersja programu iisnode-inspector — 0.7.3.dll używa 0.7.3-node-inspector oraz korzysta z gniazda sieci web. Włącz gniazda sieci web w aplikacji sieci Web platformy Azure do korzystania z tej wersji. Zobacz <https://ranjithblogs.azurewebsites.net/?p=98> Aby uzyskać więcej informacji na temat konfigurowania programu iisnode, aby użyć nowego narzędzia node-inspector.

### <a name="flushresponse"></a>flushResponse

Domyślne zachowanie usług IIS jest buforuje dane odpowiedzi górę do 4 MB, przed opróżnianie lub do czasu zakończenia odpowiedzi osiągnięta jako pierwsza. iisnode oferuje ustawienia konfiguracji w celu zastąpienia tego zachowania: Flush fragmentu treści jednostki odpowiedzi, tak szybko, jak program iisnode odbiera je z node.exe, musisz ustawić iisnode/@flushResponse atrybutu w pliku web.config wartość "true":

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Włącz opróżnianie każdego fragmentu odpowiedzi treści jednostki dodaje zmniejszenie wydajności, która zmniejsza przepływności systemu % ~ 5 (począwszy od v0.1.13). Najlepiej zakres to ustawienie tylko dla punktów końcowych, które wymagają odpowiedzi przesyłania strumieniowego (na przykład za pomocą `<location>` elementu w pliku web.config)

Oprócz tego do przesyłania strumieniowego aplikacji, należy także ustawić responseBufferLimit programu obsługi program iisnode na 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Rozdzielana średnikami lista plików, które są monitorowane zmian. Każda zmiana w pliku powoduje, że aplikacja do Kosza. Każdy wpis zawiera nazwę katalogu opcjonalny, a także nazwę wymaganego pliku, które są względne wobec katalogu, w którym znajduje się punkt wejścia głównego aplikacji. Symbole wieloznaczne są dozwolone w tylko część nazwy pliku. Wartość domyślna to `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Wartość domyślna to false. Jeśli włączone, aplikację node można nawiązać połączenia nazwanego potoku (zmienna środowiskowa IISNODE\_kontroli\_POTOKU) i wysłać wiadomość "Odtwarzanie". Powoduje to w3wp odtwarzanie bezpiecznie.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Wartość domyślna to 0, co oznacza, że ta funkcja jest wyłączona. Kiedy ustawić na wartość większą niż 0, program iisnode będą zapisywać strony pamięci się jego procesów podrzędnych co "idlePageOutTimePeriod" (w milisekundach). Zobacz [dokumentacji](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) Aby zrozumieć, co stronie limit środków. To ustawienie jest przydatne w przypadku aplikacji, używać dużej ilości pamięci, które mają być stronie limit pamięci na dysku, od czasu do czasu, aby zwolnić pamięć RAM.

> [!WARNING]
> Włączenie następujących ustawień konfiguracji aplikacji w środowisku produkcyjnym, należy zachować ostrożność. Zaleca się nie można ich włączyć w aplikacji w środowisku produkcyjnym.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Wartość domyślna to false. Jeśli ustawiona na wartość true, program iisnode dodaje nagłówek odpowiedzi HTTP `iisnode-debug` do każdej odpowiedzi HTTP, wysyła `iisnode-debug` wartość nagłówka jest adresem URL. Poszczególne informacje diagnostyczne można uzyskać, analizując fragmentu adresu URL, jednak wizualizacji jest dostępny, otwierając adres URL w przeglądarce.

### <a name="loggingenabled"></a>loggingEnabled

To ustawienie kontroluje rejestrowanie strumienia stdout i stderr przez program iisnode. Iisnode przechwytuje stdout/stderr z węzła procesów uruchomi i zapisuje plik w katalogu określona w ustawieniu "logDirectory". Gdy ta opcja jest włączona, aplikacja zapisuje dzienniki w systemie plików, a także w zależności od ilości rejestrowania wykonywane przez aplikację, może mieć wpływ na wydajność.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Wartość domyślna to false. Gdy ustawiona na wartość true, program iisnode Wyświetla kod stanu HTTP i kod błędu Win32 w przeglądarce. Kod win32 jest pomocne w debugowaniu niektóre rodzaje problemów.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (nie należy włączać na witrynę produkcyjną)

To ustawienie steruje funkcja debugowania typu. Program Iisnode jest zintegrowany z narzędzia node-inspector. Po włączeniu tego ustawienia, należy włączyć debugowanie aplikację node. Po włączeniu tego ustawienia program iisnode tworzy pliki narzędzia node-inspector, w katalogu "debuggerVirtualDir" na pierwsze żądanie debugowania aplikację node. Możesz załadować narzędzia node-inspector, wysyłając żądanie do `http://yoursite/server.js/debug`. Segment adresu URL debugowania można kontrolować za pomocą ustawień "debuggerPathSegment". Domyślnie debuggerPathSegment = "debug". Możesz ustawić `debuggerPathSegment` na identyfikator GUID, na przykład, tak że trudniej jest być odnajdowane przez inne osoby.

Odczyt [debugowanie aplikacji node.js na Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) Aby uzyskać więcej informacji na temat debugowania.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scenariusze i zalecenia dotyczące/Rozwiązywanie problemów

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Moja aplikacja węzła jest wywołań nadmiernego ruchu wychodzącego

Wiele aplikacji, będzie chciała nawiązywać połączenia wychodzące w ramach ich regularnych operacji. Na przykład gdy nadejdzie żądanie, aplikacja node, będzie chciała skontaktuj się z interfejsu API REST gdzie indziej i uzyskać pewne informacje, aby przetworzyć żądanie. Chcesz korzystać z agentem przy życiu Zachowaj podczas nawiązywania połączeń http lub https. Podczas tych wywołań ruchu wychodzącego, można skorzystać z modułu agentkeepalive jako przedstawiciela keep alive.

Moduł agentkeepalive gwarantuje, że gniazda są ponownie na usługi platformy Azure webapp maszyny Wirtualnej. Tworzenie nowego gniazda na każde wychodzące żądanie obciążenie dodaje do aplikacji. Ponowne użycie gniazda dla wychodzących żądań aplikacji zapewnia, że aplikacja nie przekracza ustawienie maxSockets, które są przydzielane na podstawie maszyny Wirtualnej. Zalecenie dotyczące usługi Azure App Service jest równa wartości ustawienie maxSockets agentKeepAlive daje w sumie (4 wystąpień node.exe \* 40 ustawienie maxSockets/wystąpienie) 160 gniazda na maszynę Wirtualną.

Przykład [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) konfiguracji:

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> W tym przykładzie przyjęto założenie, że masz 4 node.exe uruchomiona na maszynie Wirtualnej. Jeśli masz inną liczbę node.exe uruchomiona na maszynie Wirtualnej, należy zmodyfikować ustawienie maxSockets ustawienie odpowiednio.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Moja aplikacja node zużywa zbyt dużo procesora CPU

Zalecenia z usługi Azure App Service może pojawić się w portalu dotyczące wysokiego użycia procesora cpu. Można również ustawić się monitorów, aby obejrzeć w przypadku niektórych [metryki](web-sites-monitor.md). Podczas sprawdzania użycia procesora CPU na [pulpit nawigacyjny portalu Azure](../azure-monitor/app/web-monitor-performance.md), sprawdź wartości MAX procesora CPU, więc nie przegap wartości szczytowe.
Jeśli uważasz, Twoja aplikacja zużywa zbyt dużo procesora CPU i nie może wyjaśnić, dlaczego, można profilować aplikację node, aby dowiedzieć się.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Profilowanie węzła aplikacji w usłudze Azure App Service przy użyciu V8 Profiler

Załóżmy na przykład, że aplikacja hello world, którego chcesz przeprowadzić profilowanie w następujący sposób:

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

Przejdź do witryny konsoli debugowania `https://yoursite.scm.azurewebsites.net/DebugConsole`

Przejdź do katalogu site/wwwroot. Zostanie wyświetlony wiersz polecenia, jak pokazano w poniższym przykładzie:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Uruchom polecenie `npm install v8-profiler`.

To polecenie powoduje zainstalowanie v8 profilera w węźle\_modułów katalogu i wszystkich jego zależności.
Teraz można edytować swoje server.js do profilu aplikacji.

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

Poprzedni kod profile WriteConsoleLog funkcji, a następnie zapisuje dane wyjściowe profilu do pliku "profile.cpuprofile" w ramach Twojego wwwroot lokacji. Wyślij żądanie do aplikacji. Zostanie wyświetlony plik "profile.cpuprofile" utworzonych w ramach Twojego wwwroot lokacji.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Pobierz ten plik i otwórz go za pomocą narzędzi F12 w przeglądarce Chrome. Naciśnij klawisz F12 w przeglądarce Chrome, a następnie wybierz **profile** kartę. Wybierz **obciążenia** przycisku. Wybierz swój plik profile.cpuprofile, który został pobrany. Kliknij profil, który załadowane przed chwilą.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Widać, że funkcja WriteConsoleLog zużyto 95% czasu. Dane wyjściowe pokazują możesz również numery wierszy dokładne i plików źródłowych, które przyczyny problemu.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Moja aplikacja node zużywa zbyt dużej ilości pamięci

Jeśli Twoja aplikacja zużywa zbyt dużej ilości pamięci, zobaczysz powiadomienie z usługi Azure App Service w portalu o duże użycie pamięci. Można ustawić monitorów, aby obejrzeć w przypadku niektórych [metryki](web-sites-monitor.md). Podczas sprawdzania użycia pamięci na [pulpit nawigacyjny portalu Azure](../azure-monitor/app/web-monitor-performance.md), należy koniecznie sprawdzić wartości Maksymalna pamięć, dzięki czemu nie przegap wartości szczytowe.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Wykrywania przecieków i różnic sterty dla środowiska node.js

Można użyć [memwatch węzła](https://github.com/lloyd/node-memwatch) umożliwia identyfikowanie pamięci, przecieków.
Możesz zainstalować `memwatch` podobnie jak v8 profiler i Edycja kodu do przechwytywania i różnic sterty deskryptorów do identyfikowania przecieki pamięci w aplikacji.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Moje node.exe są wprowadzenie losowo skasowane.

Istnieje kilka powodów dlaczego node.exe zamknie losowo:

1. Aplikacji wywołującej nieobsłużone wyjątki — d: wyboru\\macierzystego\\LogFiles\\aplikacji\\pliku errors.txt rejestrowanie szczegółowe informacje o wyjątku. Ten plik ma następujący ślad stosu, aby ułatwić debugowanie i naprawa aplikacji.
2. Twoja aplikacja zużywa zbyt dużej ilości pamięci, co ma wpływ na inne procesy z etapu wprowadzenia. Jeżeli całkowita pamięć maszyny Wirtualnej jest blisko 100%, Twoje node.exe można kasować przez menedżera procesów. Menedżer procesu kasuje niektóre procesy, aby umożliwić inne procesy, które umożliwiają wykonania dodatkowych czynności. Aby rozwiązać ten problem, Profiluj aplikację przecieki pamięci. Jeśli aplikacja wymaga dużej ilości pamięci, skalowanie w górę do większej maszyny Wirtualnej (który zwiększa ilość pamięci RAM, dostępne dla maszyny Wirtualnej).

### <a name="my-node-application-does-not-start"></a>Moja aplikacja node nie rozpoczyna się

Jeśli aplikacja zwraca błędy 500 podczas uruchamiania, może być kilka możliwych przyczyn:

1. Node.exe nie występuje na poprawną lokalizację. Sprawdź ustawienie nodeProcessCommandLine.
2. Plik skryptu główny nie występuje na poprawną lokalizację. Sprawdź plik web.config i upewnij się, że nazwa pliku głównego skryptu w sekcji obsługi pasuje do pliku głównego skryptu.
3. Konfiguracja pliku Web.config nie jest prawidłowa — Sprawdź nazwy/wartości ustawień.
4. Zimnego — aplikacji trwa zbyt długo, aby rozpocząć. Jeśli aplikacja trwa dłużej niż (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000 sekund iisnode returns 500 błąd. Zwiększenie wartości tych ustawień, aby dopasować godziną początkową aplikacji, aby zapobiec iisnode limit czasu i zwróci komunikat 500.

### <a name="my-node-application-crashed"></a>Moja aplikacja node wystąpiła awaria programu

Aplikacji wywołującej nieobsłużone wyjątki — sprawdzanie `d:\\home\\LogFiles\\Application\\logging-errors.txt` pliku szczegółowe informacje o wyjątku. Ten plik ma następujący ślad stosu, aby ułatwić diagnozowanie i usuwanie aplikacji.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Moja aplikacja node zajmuje zbyt dużo czasu, aby rozpocząć (zimny Start)

Typową przyczyną długi czas uruchamiania aplikacji jest dużą liczbę plików w węźle\_modułów. Aplikacja próbuje załadować większość tych plików, podczas uruchamiania. Domyślnie ponieważ pliki są przechowywane w udziale sieciowym, w usłudze Azure App Service ładowania wielu plików może potrwać.
Niektóre rozwiązania, aby przyspieszyć ten proces jest:

1. Upewnij się, że masz struktury płaskiej zależności i nie zduplikowane zależności za pomocą npm3, aby zainstalować moduły.
2. Próba powolne ładowanie Twój węzeł\_modułów i nie ładować wszystkie moduły przy uruchamianiu aplikacji. Powolne ładowanie modułów wywołanie require('module') ustanowić gdy rzeczywiście potrzebujesz modułu w obrębie funkcji przed pierwszym wykonywanie kodu modułu.
3. Usługa Azure App Service oferuje funkcję o nazwie lokalnej pamięci podręcznej. Ta funkcja kopiuje zawartość z udziału sieciowego na dysk lokalny na maszynie Wirtualnej. Ponieważ pliki są lokalne, czas ładowania węzła\_modułów jest znacznie wyższa.

## <a name="iisnode-http-status-and-substatus"></a>Stan http programu IISNODE i podstanu

`cnodeconstants` [Plik źródłowy](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) Wyświetla wszystkie możliwe stan/podstanu iisnode kombinacje może zwrócić się z powodu błędu.

Włącz FREB dla swojej aplikacji wyświetlić kod błędu win32 (należy włączyć FREB tylko w lokacjach nieprodukcyjnych ze względu na wydajność).

| Stan HTTP | Podstan protokołu HTTP | Możliwa przyczyna? |
| --- | --- | --- |
| 500 |1000 |Wystąpił jakiś problem, wysyła żądania do programu IISNODE — Sprawdź Jeśli node.exe została uruchomiona. Node.exe może ulec awarii podczas uruchamiania. Sprawdź konfigurację pliku web.config dla błędów. |
| 500 |1001 |-Win32Error 0x2 — aplikacja nie odpowiada na adres URL. Zaznacz reguł ponownego zapisywania adresów URL lub wyboru, jeśli aplikacja express ma poprawne trasy zdefiniowane. -Win32Error 0x6d — nazwany potok jest zajęty — Node.exe nie akceptuje żądań, ponieważ potoku jest zajęty. Sprawdź wysokie użycie procesora cpu. -Inne błędy — Sprawdź, jeśli wystąpiła awaria node.exe. |
| 500 |1002 |Wystąpiła awaria programu node.exe — sprawdź d:\\macierzystego\\LogFiles\\errors.txt rejestrowania, aby uzyskać ślad stosu. |
| 500 |1003 |Konfiguracja potoku wydania — Konfiguracja nazwany potok jest nieprawidłowa. |
| 500 |1004-1018 |Wystąpił błąd podczas wysyłania żądania lub przetwarzania odpowiedzi z node.exe. Sprawdź, czy wystąpiła awaria programu node.exe. Sprawdź d:\\macierzystego\\LogFiles\\errors.txt rejestrowania, aby uzyskać ślad stosu. |
| 503 |1000 |Nie ma wystarczającej ilości pamięci, aby przydzielić więcej połączenia nazwanego potoku. Sprawdź, dlaczego aplikacja zużywa bardzo dużo pamięci. Sprawdź wartość ustawienia maxConcurrentRequestsPerProcess. Jeśli nie jest nieskończona ma wiele żądań, należy zwiększyć tę wartość, aby uniknąć tego błędu. |
| 503 |1001 |Nie można wysłać żądania node.exe, ponieważ aplikacja jest odtwarzane. Po aplikacji po odtworzeniu, żądania należy zwykle obsługiwane. |
| 503 |1002 |Kod błędu win32 wyboru przyczyny rzeczywistego — żądanie nie mógł zostać wysłany do node.exe. |
| 503 |1003 |Nazwany potok jest zbyt zajęty, — sprawdź, jeśli node.exe zużywa nadmiernego użycia Procesora |

NODE.exe ma ustawienie o nazwie `NODE_PENDING_PIPE_INSTANCES`. W usłudze Azure App Service ta wartość jest równa 5000. Co oznacza, że node.exe może akceptować 5000 żądań w czasie na nazwanym potoku. Ta wartość powinna być wystarczające dla większości węzłów działających w usłudze Azure App Service. Nie powinny występować 503.1003 w usłudze Azure App Service ze względu na wysoką wartość `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Więcej zasobów

Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat aplikacji w technologii node.js w usłudze Azure App Service.

* [Rozpoczynanie pracy z aplikacjami internetowymi środowiska Node.js w usłudze Azure App Service](app-service-web-get-started-nodejs.md)
* [How to debug a Node.js web app in Azure App Service (Jak debugować aplikację internetową Node.js w usłudze Azure App Service)](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Using Node.js Modules with Azure applications (Używanie modułów Node.js z aplikacjami platformy Azure)](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js Developer Center (Centrum deweloperów środowiska Node.js)](../nodejs-use-node-modules-azure-apps.md)
* [Exploring the Super Secret Kudu Debug Console (Szczegółowe informacje o ściśle tajnej konsoli debugowania aparatu Kudu)](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
