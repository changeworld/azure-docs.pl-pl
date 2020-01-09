---
title: Dzienniki diagnostyczne usługi Azure Signal Service
description: Dowiedz się, jak skonfigurować dzienniki diagnostyczne dla usługi Azure Signal Service oraz jak korzystać z niej do samorozwiązywania problemów.
author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: c5def306e5aa874432ebb5f47f3fdd8b5234ffd7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479555"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Dzienniki diagnostyczne usługi Azure Signal Service

W tym samouczku opisano, co to są dzienniki diagnostyczne usługi Azure Signal i jak skonfigurować dzienniki diagnostyczne oraz jak rozwiązywać problemy z dziennikami diagnostycznymi.

## <a name="prerequisites"></a>Wymagania wstępne
W celu włączenia dzienników diagnostycznych należy przechowywać dane dziennika. Ten samouczek używa usługi Azure Storage i Log Analytics.

* [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md) — przechowuje dzienniki diagnostyczne na potrzeby inspekcji zasad, statycznej analizy lub kopii zapasowej.
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) — elastyczne narzędzie do przeszukiwania dzienników i analizy umożliwiające analizę nieprzetworzonych dzienników generowanych przez zasób platformy Azure.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Konfigurowanie dzienników diagnostycznych dla usługi Azure Signal Service

Dzienniki diagnostyczne usługi Azure Signal można wyświetlić. Te dzienniki zapewniają bogatszy widok łączności z wystąpieniem usługi Azure sygnalizującego. Dzienniki diagnostyczne zawierają szczegółowe informacje dotyczące każdego połączenia. Na przykład podstawowe informacje (identyfikator użytkownika, identyfikator połączenia i typ transportu itd.) oraz informacje o zdarzeniu (łączenie, rozłączanie i przerywanie) połączenia. Dzienniki diagnostyczne mogą służyć do identyfikacji problemu, śledzenia połączeń i analizy.

### <a name="enable-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Dzienniki diagnostyczne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące kroki:

1.  W [Azure Portal](https://portal.azure.com)w obszarze **monitorowanie**kliknij pozycję **Ustawienia diagnostyczne**.

    ![Nawigacja w okienku do ustawień diagnostycznych](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1.  Następnie kliknij pozycję **Dodaj ustawienie diagnostyczne**.

    ![Dodawanie dzienników diagnostycznych](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1.  Ustaw żądany cel archiwum. Obecnie obsługujemy **Archiwizowanie na koncie magazynu** i **wysyłamy do log Analytics**.

1. Wybierz dzienniki, które chcesz zarchiwizować.

    ![Okienko ustawień diagnostycznych](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1.  Zapisz nowe ustawienia diagnostyki.

Nowe ustawienia zaczną obowiązywać w ciągu około 10 minut. Po tym dzienników pojawia się w skonfigurowanym archiwizacji lokalizacji docelowej w **dzienniki diagnostyczne** okienka.

Aby uzyskać więcej informacji na temat konfigurowania diagnostyki zobacz [Przegląd dzienniki diagnostyczne platformy Azure](../azure-monitor/platform/resource-logs-overview.md).

### <a name="diagnostic-logs-categories"></a>Kategorie dzienników diagnostycznych

Usługa Azure Signal przechwytuje dzienniki diagnostyczne w jednej kategorii:

* **Wszystkie dzienniki**: śledzenie połączeń łączących się z usługą Azure Signal Service. Dzienniki zawierają informacje o połączeniu/rozłączeniu, uwierzytelnianiu i ograniczaniu przepustowości. Aby uzyskać więcej informacji zobacz następną sekcję.

### <a name="archive-to-a-storage-account"></a>Zarchiwizuj na koncie magazynu

Dzienniki są przechowywane na koncie magazynu skonfigurowanym w okienku **dzienników diagnostycznych** . Kontener o nazwie `insights-logs-alllogs` jest automatycznie tworzony w celu przechowywania dzienników diagnostycznych. W kontenerze dzienniki są przechowywane w pliku `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`. W zasadzie ścieżka jest łączona przez `resource ID` i `Date Time`. Pliki dziennika są podzielone przez `hour`. W związku z tym minuty zawsze są `m=00`.

Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis ma pola ciągów, w formacie opisane w poniższych sekcjach.

Dzienniki archiwum JSON zawierają elementy wymienione w następujących tabelach:

**Formatowanie**

Nazwa | Opis
------- | -------
time | Czas zdarzenia dziennika
level | Poziom zdarzeń dziennika
resourceId | Identyfikator zasobu usługi Azure sygnalizacyjnej
location | Lokalizacja usługi Azure Signal
category | Kategoria zdarzenia dziennika
operationName | Nazwa operacji dla zdarzenia
callerIpAddress | Adres IP serwera/klienta
properties | Szczegółowe właściwości dotyczące tego zdarzenia dziennika. Aby uzyskać więcej szczegółów, zobacz poniższą tabelę właściwości.

**Tabela właściwości**

Nazwa | Opis
------- | -------
type | Typ zdarzenia dziennika. Obecnie udostępniamy informacje o łączności z usługą Azure Signal Service. Dostępny jest tylko typ `ConnectivityLogs`
Kolekcja | Kolekcja zdarzenia dziennika. Dozwolone wartości to: `Connection`, `Authorization` i `Throttling`
connectionId | Tożsamość połączenia
transportType | Typ transportu połączenia. Dozwolone wartości to: `Websockets` \| `ServerSentEvents` \| `LongPolling`
Element ConnectionType | Typ połączenia. Dozwolone wartości to: `Server` \| `Client`. `Server`: połączenie po stronie serwera; `Client`: połączenie po stronie klienta
userId | Tożsamość użytkownika
message | Szczegółowy komunikat zdarzenia dziennika

Poniższy kod jest przykładem Archiwizowanie dziennika ciągu JSON:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Schemat archiwum dzienników dla Log Analytics

Aby wyświetlić dzienniki diagnostyczne, wykonaj następujące kroki:

1. W Log Analytics docelowym kliknij `Logs`.

    ![Element menu Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Wprowadź `SignalRServiceDiagnosticLogs` i wybierz zakres czasu, aby wykonywać zapytania dotyczące dzienników diagnostycznych. Aby uzyskać zaawansowaną kwerendę, zobacz Wprowadzenie do [log Analytics w Azure monitor](../azure-monitor/log-query/get-started-portal.md)

    ![Zalogować się Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Kolumny dzienników archiwalnych zawierają elementy wymienione w poniższej tabeli:

Nazwa | Opis
------- | ------- 
TimeGenerated | Czas zdarzenia dziennika
Collection | Kolekcja zdarzenia dziennika. Dozwolone wartości to: `Connection`, `Authorization` i `Throttling`
OperationName | Nazwa operacji dla zdarzenia
Lokalizacja | Lokalizacja usługi Azure Signal
Poziom | Poziom zdarzeń dziennika
CallerIpAddress | Adres IP serwera/klienta
Wiadomość | Szczegółowy komunikat zdarzenia dziennika
UserId | Tożsamość użytkownika
ConnectionId | Tożsamość połączenia
Element ConnectionType | Typ połączenia. Dozwolone wartości to: `Server` \| `Client`. `Server`: połączenie po stronie serwera; `Client`: połączenie po stronie klienta
TransportType | Typ transportu połączenia. Dozwolone wartości to: `Websockets` \| `ServerSentEvents` \| `LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>Rozwiązywanie problemów z dziennikami diagnostycznymi

Aby rozwiązać problemy z usługą Azure Signal Service, można włączyć dzienniki po stronie serwera i klienta w celu przechwycenia błędów. W tej chwili usługa Azure sygnalizująca ujawnia dzienniki diagnostyczne, ale można również włączyć dzienniki po stronie usługi.

W przypadku wystąpienia nieoczekiwanego wzrostu lub porzucania połączeń można skorzystać z dzienników diagnostycznych do rozwiązywania problemów.

Typowe problemy często dotyczą połączeń "nieoczekiwane zmiany ilościowe", połączenia docierają do limitów połączeń i niepowodzeń autoryzacji. Zapoznaj się z kolejnymi sekcjami dotyczącymi rozwiązywania problemów.

#### <a name="unexpected-connection-number-changes"></a>Nieoczekiwane zmiany numeru połączenia

##### <a name="unexpected-connection-dropping"></a>Nieoczekiwane usunięcie połączenia

Jeśli wystąpią nieoczekiwane połączenia, po pierwsze włączenie dzienników na stronie usługi, serwera i klienta.

Jeśli połączenie zostanie rozłączone, dzienniki diagnostyczne spowodują zapisanie tego zdarzenia odłączenia. w `operationName`zostanie wyświetlony `ConnectionAborted` lub `ConnectionEnded`.

Różnica między `ConnectionAborted` i `ConnectionEnded` polega na tym, że `ConnectionEnded` jest to oczekiwane rozłączenie, które jest wyzwalane przez klienta lub po stronie serwera. `ConnectionAborted` jest zwykle nieoczekiwanym zdarzeniem porzucenia połączenia, a powód przerwania zostanie podany w `message`.

Przyczyny przerwania są wymienione w poniższej tabeli:

Przyczyna | Opis
------- | ------- 
Liczba połączeń osiągnie limit | Liczba połączeń osiągnie limit bieżącej warstwy cenowej. Rozważ skalowanie jednostki usługi
Serwer aplikacji zamknął połączenie | Serwer aplikacji wyzwala przerwanie. Może być traktowany jako oczekiwany przerwanie
Limit czasu polecenia ping połączenia | Zwykle jest to spowodowane problemem z siecią. Rozważ sprawdzenie dostępności serwera aplikacji z Internetu
Ponowne ładowanie usługi, ponowne łączenie | Trwa ponowne ładowanie usługi sygnałów platformy Azure. Usługa Azure sygnalizująca obsługuje autonawiązywanie połączenia. możesz poczekać do momentu ponownego połączenia lub ręcznego ponownego połączenia z usługą Azure Signaler
Przejściowy błąd wewnętrzny serwera | Wystąpił błąd przejściowy w usłudze Azure Signal
Połączenie z serwerem zostało usunięte | Połączenie z serwerem jest pomijane z nieznanym błędem, należy najpierw rozważyć samorozwiązywanie problemów z dziennikiem usługi/serwera/po stronie klienta. Spróbuj wykluczyć podstawowe problemy (np. problem z siecią, problem po stronie serwera aplikacji itd.). Jeśli problem nie zostanie rozwiązany, skontaktuj się z nami w celu uzyskania dalszej pomocy. Aby uzyskać więcej informacji, zobacz sekcję [Uzyskiwanie pomocy](#get-help) . 

##### <a name="unexpected-connection-growing"></a>Nieoczekiwany wzrost połączenia

Aby rozwiązać problemy związane z nieoczekiwanym wzrostem połączenia, należy najpierw odfiltrować dodatkowe połączenia. Możesz dodać unikatowy identyfikator użytkownika testowego do połączenia z klientem testowym. Następnie należy sprawdzić, czy w dziennikach diagnostycznych jest widocznych więcej niż jedno połączenie klienta, które ma ten sam identyfikator użytkownika lub adres IP. po stronie klienta można utworzyć i ustanowić więcej połączeń niż oczekiwano. Sprawdź po stronie klienta.

#### <a name="authorization-failure"></a>Błąd autoryzacji

Jeśli otrzymasz 401 nieautoryzowanych żądań klientów, Sprawdź dzienniki diagnostyczne. Jeśli wystąpi `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`, oznacza to, że wszyscy odbiorcy w tokenie dostępu są Nieprawidłowi. Spróbuj użyć prawidłowych odbiorców sugerowanych w dzienniku.


#### <a name="throttling"></a>Ograniczanie przepływności

Jeśli okaże się, że nie można ustanowić połączeń klienta sygnalizującego z usługą Azure Signal, Sprawdź dzienniki diagnostyczne. Jeśli napotkasz `Connection count reaches limit` w dzienniku diagnostycznym, nawiążesz zbyt wiele połączeń z usługą sygnalizującego, która osiągnie limit liczby połączeń. Rozważ skalowanie w górę usługi sygnalizującej. Jeśli napotkasz `Message count reaches limit` w dzienniku diagnostycznym, oznacza to, że używasz warstwy Bezpłatna i używasz limitu przydziału komunikatów. Jeśli chcesz wysłać więcej komunikatów, Rozważ zmianę usługi sygnalizującej na warstwę Standardowa w celu wysyłania dodatkowych komunikatów. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Signal](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Uzyskiwanie pomocy

Zalecamy najpierw Rozwiązywanie problemów. Większość problemów jest spowodowana przez serwer aplikacji lub problemy z siecią. Skorzystaj [z przewodnika rozwiązywania problemów z dziennikiem diagnostycznym](#troubleshooting-with-diagnostic-logs) i [Przewodnik po rozwiązaniu problemu podstawowego](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) , aby znaleźć główną przyczynę.
Jeśli problem nadal nie może zostać rozwiązany, rozważ otwarcie problemu w usłudze GitHub lub utworzenie biletu w witrynie Azure Portal.
Oferować
1. Zakres czasu około 30 minut, gdy występuje problem
2. Identyfikator zasobu usługi Azure Signal
3. Szczegółowe informacje o problemie, jak to możliwe: na przykład AppServer nie wysyła komunikatów, nie powoduje połączenia z klientem i tak dalej.
4. Dzienniki zbierane z serwera po stronie klienta i inne materiały, które mogą być przydatne
5. Obowiązkowe Kod Odtwórz

> Uwaga: Jeśli otworzysz problem w usłudze GitHub, Zachowaj poufne informacje (na przykład identyfikator zasobu, Dzienniki serwera/klienta), a następnie Wyślij tylko do członków w organizacji Microsoft Organization Private.  
