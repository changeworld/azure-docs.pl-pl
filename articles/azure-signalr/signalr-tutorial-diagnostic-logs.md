---
title: Dzienniki diagnostyczne dla usługi Azure SignalR
description: Dowiedz się, jak skonfigurować dzienniki diagnostyczne dla usługi Azure SignalR i jak wykorzystać ją do samodzielnego rozwiązywania problemów.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 72f57ba4bbbbde07f6d26edc88c158f301ebe2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536738"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Dzienniki diagnostyczne dla usługi Azure SignalR

W tym samouczku opisano, jakie są dzienniki diagnostyczne dla usługi Azure SignalR i jak skonfigurować dzienniki diagnostyczne i jak rozwiązywać problemy z dziennikami diagnostycznymi.

## <a name="prerequisites"></a>Wymagania wstępne
Aby włączyć dzienniki diagnostyczne, musisz gdzieś przechowywać dane dziennika. W tym samouczku użyto usługi Azure Storage i usługi Log Analytics.

* [Magazyn platformy Azure](../azure-monitor/platform/resource-logs-collect-storage.md) — zachowuje dzienniki diagnostyczne do inspekcji zasad, analizy statycznej lub kopii zapasowej.
* [Usługa Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) — elastyczne narzędzie do wyszukiwania i analizy dzienników, które umożliwia analizę nieprzetworzonych dzienników generowanych przez zasób platformy Azure.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Konfigurowanie dzienników diagnostycznych dla usługi Azure SignalR

Można wyświetlić dzienniki diagnostyczne dla usługi Azure SignalR. Te dzienniki zapewniają bogatszy widok łączności z wystąpieniem usługi Azure SignalR. Dzienniki diagnostyczne zawierają szczegółowe informacje o każdym połączeniu. Na przykład podstawowe informacje (identyfikator użytkownika, identyfikator połączenia i typ transportu itd.) oraz informacje o zdarzeniu (zdarzenie łączenia, rozłączania i przerwania itd.) połączenia. Dzienniki diagnostyczne mogą być używane do identyfikacji problemów, śledzenia połączeń i analizy.

### <a name="enable-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Dzienniki diagnostyczne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)w obszarze **Monitorowanie**kliknij pozycję **Ustawienia diagnostyczne**.

    ![Nawigacja do ustawień diagnostycznych w okienku](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Następnie kliknij pozycję **Dodaj ustawienie diagnostyczne**.

    ![Dodawanie dzienników diagnostycznych](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Ustaw odpowiedni cel archiwum. Obecnie obsługujemy **Archiwum na konto magazynu** i **wyślij do usługi Log Analytics.**

1. Wybierz dzienniki, które chcesz zarchiwizować.

    ![Okienko ustawień diagnostycznych](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Zapisz nowe ustawienia diagnostyczne.

Nowe ustawienia zajmą się efektem około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym celu archiwizacji w okienku **Dzienniki diagnostyki.**

Aby uzyskać więcej informacji na temat konfigurowania diagnostyki, zobacz [omówienie dzienników diagnostycznych platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="diagnostic-logs-categories"></a>Kategorie dzienników diagnostycznych

Usługa Azure SignalR przechwytuje dzienniki diagnostyczne w jednej kategorii:

* **Wszystkie dzienniki:** Śledzenie połączeń, które łączą się z usługą Azure SignalR. Dzienniki zawierają informacje o connect/disconnect, uwierzytelniania i ograniczania przepustowości. Aby uzyskać więcej informacji, zobacz następną sekcję.

### <a name="archive-to-a-storage-account"></a>Zarchiwizuj na koncie magazynu

Dzienniki są przechowywane na koncie magazynu skonfigurowanym w okienku **Dzienniki diagnostyki.** Kontener o `insights-logs-alllogs` nazwie jest tworzony automatycznie do przechowywania dzienników diagnostycznych. Wewnątrz kontenera dzienniki są przechowywane `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`w pliku . Zasadniczo ścieżka jest łączona przez `resource ID` i `Date Time`. Pliki dziennika są `hour`dzielone przez . Dlatego minuty zawsze są `m=00`.

Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis zawiera pola ciągów, które używają formatu opisanego w poniższych sekcjach.

Ciągi JSON dziennika archiwum zawierają elementy wymienione w następujących tabelach:

**Formacie**

Nazwa | Opis
------- | -------
time | Rejestrowanie czasu zdarzenia
poziom | Poziom zdarzenia dziennika
resourceId | Identyfikator zasobu usługi Azure SignalR
location | Lokalizacja usługi Azure SignalR
category | Kategoria zdarzenia dziennika
operationName | Nazwa operacji zdarzenia
callerIpAddress | Adres IP serwera/klienta
properties | Szczegółowe właściwości związane z tym zdarzeniem dziennika. Aby uzyskać więcej informacji, zobacz tabelę właściwości poniżej

**Tabela właściwości**

Nazwa | Opis
------- | -------
type | Typ zdarzenia dziennika. Obecnie dostarczamy informacje o łączności z usługą Azure SignalR. Dostępny `ConnectivityLogs` jest tylko typ
 — kolekcja | Kolekcja zdarzenia dziennika. Dozwolone wartości `Connection`to: `Authorization` , i`Throttling`
Connectionid | Tożsamość połączenia
transportTyp | Typ transportu połączenia. Dozwolone wartości to: `Websockets` \| `ServerSentEvents` \|`LongPolling`
Connectiontype | Typ połączenia. Dozwolone wartości to: `Server` \| `Client`. `Server`: połączenie od strony serwera; `Client`: połączenie od strony klienta
userId | Tożsamość użytkownika
message | Szczegółowy komunikat o zdarzeniu dziennika

Poniższy kod jest przykładem ciągu JSON dziennika archiwum:

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

### <a name="archive-logs-schema-for-log-analytics"></a>Schemat dzienników archiwum dla usługi Log Analytics

Aby wyświetlić dzienniki diagnostyczne, wykonaj następujące kroki:

1. Kliknij `Logs` docelową analizę dziennika.

    ![Element menu usługi Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Wprowadź `SignalRServiceDiagnosticLogs` i wybierz zakres czasu, aby wysyłać zapytania do dzienników diagnostycznych. Aby uzyskać zapytanie zaawansowane, zobacz [Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor](../azure-monitor/log-query/get-started-portal.md)

    ![Logowanie zapytań w usłudze Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Kolumny dziennika archiwum zawierają elementy wymienione w poniższej tabeli:

Nazwa | Opis
------- | ------- 
TimeGenerated | Rejestrowanie czasu zdarzenia
Collection | Kolekcja zdarzenia dziennika. Dozwolone wartości `Connection`to: `Authorization` , i`Throttling`
OperationName | Nazwa operacji zdarzenia
Lokalizacja | Lokalizacja usługi Azure SignalR
Poziom | Poziom zdarzenia dziennika
CallerIpAddress (Adres callerIpAddress) | Adres IP serwera/klienta
Komunikat | Szczegółowy komunikat o zdarzeniu dziennika
UserId | Tożsamość użytkownika
Connectionid | Tożsamość połączenia
Connectiontype | Typ połączenia. Dozwolone wartości to: `Server` \| `Client`. `Server`: połączenie od strony serwera; `Client`: połączenie od strony klienta
Rodzaj transportu | Typ transportu połączenia. Dozwolone wartości to: `Websockets` \| `ServerSentEvents` \|`LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>Rozwiązywanie problemów z dziennikami diagnostycznymi

Aby rozwiązać problemy z usługą Azure SignalR, można włączyć dzienniki po stronie serwera/klienta, aby rejestrować błędy. Obecnie usługa Azure SignalR udostępnia dzienniki diagnostyczne, można również włączyć dzienniki po stronie usługi.

W przypadku napotkania połączenia nieoczekiwane rosnącej lub upuszczania sytuacji, można skorzystać z dzienników diagnostycznych do rozwiązywania problemów.

Typowe problemy są często o nieoczekiwanych zmian ilości połączeń, połączenia osiągnąć limity połączenia i niepowodzenie autoryzacji. Zobacz następne sekcje dotyczące rozwiązywania problemów.

#### <a name="unexpected-connection-number-changes"></a>Nieoczekiwane zmiany numeru połączenia

##### <a name="unexpected-connection-dropping"></a>Nieoczekiwane upuszczenie połączenia

Jeśli wystąpi nieoczekiwane połączenia upuścić, po pierwsze włączyć dzienniki w serwisie, serwera i klienta.

Jeśli połączenie się rozłączy, dzienniki diagnostyczne zarejestrują `ConnectionAborted` to `ConnectionEnded` `operationName`zdarzenie rozłączania, zostanie wyświetlone lub w .

Różnica między `ConnectionAborted` `ConnectionEnded` i `ConnectionEnded` jest to, że jest oczekiwane rozłączenie, które jest wyzwalane przez klienta lub serwera po stronie. Podczas `ConnectionAborted` gdy jest to zwykle nieoczekiwane zdarzenie porzucenia połączenia, a przyczyna przerwania zostanie podana w `message`pliku .

Przyczyny przerwania są wymienione w poniższej tabeli:

Przyczyna | Opis
------- | ------- 
Liczba połączeń osiąga limit | Liczba połączeń osiąga limit bieżącej warstwy cenowej. Rozważ skalowanie jednostki serwisowej
Serwer aplikacji zamknął połączenie | Serwer aplikacji powoduje aborcję. Można ją uznać za oczekiwaną aborcję
Limit czasu ping połączenia | Zwykle jest to spowodowane problemem z siecią. Rozważ sprawdzenie dostępności serwera aplikacji w Internecie
Ponowne ładowanie usługi, ponowne połączenie | Usługa Azure SignalR jest ponowne ładowanie. Usługa Azure SignalR obsługuje automatyczne ponowne łączenie, możesz poczekać, aż ponownie się połącz lub ręcznie połączyć się ponownie z usługą Azure SignalR
Wewnętrzny błąd przejściowy serwera | Błąd przejściowy występuje w usłudze Azure SignalR, należy automatycznie odzyskać
Przerwano połączenie z serwerem | Połączenie z serwerem spada z nieznanym błędem, należy najpierw rozważyć samorozwiązanie z dziennikiem po stronie usługi/serwera/klienta. Spróbuj wykluczyć podstawowe problemy (np. problem z siecią, problem po stronie serwera aplikacji i tak dalej). Jeśli problem nie został rozwiązany, skontaktuj się z nami, aby uzyskać dalszą pomoc. Aby uzyskać więcej informacji, zobacz [Sekcja Uzyskaj pomoc.](#get-help) 

##### <a name="unexpected-connection-growing"></a>Nieoczekiwane połączenie rośnie

Aby rozwiązać problem związany z rozwojem nieoczekiwanego połączenia, pierwszą rzeczą, którą musisz zrobić, to odfiltrować dodatkowe połączenia. Można dodać unikatowy identyfikator użytkownika testowego do połączenia klienta testowego. Następnie sprawdź go za pomocą dzienników diagnostycznych, jeśli widzisz więcej niż jeden klient połączenia mają ten sam identyfikator użytkownika testu lub IP, to jest prawdopodobne, po stronie klienta utworzyć i ustanowić więcej połączeń niż oczekiwano. Sprawdź stronę klienta.

#### <a name="authorization-failure"></a>Błąd autoryzacji

Jeśli otrzymasz 401 Nieautoryzowane zwrócone dla żądań klientów, sprawdź dzienniki diagnostyczne. Jeśli napotkasz `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`, oznacza to, że wszyscy odbiorcy w tokenie dostępu są nieprawidłowe. Spróbuj użyć prawidłowych odbiorców sugerowanych w dzienniku.


#### <a name="throttling"></a>Ograniczanie przepływności

Jeśli okaże się, że nie można ustanowić połączenia klienta SignalR do usługi Azure SignalR, sprawdź dzienniki diagnostyczne. Jeśli napotkasz `Connection count reaches limit` w dzienniku diagnostycznym, należy ustanowić zbyt wiele połączeń z usługą SignalR, które osiągają limit liczby połączeń. Należy rozważyć skalowanie usługi SignalR. Jeśli napotkasz `Message count reaches limit` w dzienniku diagnostycznym, oznacza to, że używasz warstwy bezpłatnej i zużywają się przydział wiadomości. Jeśli chcesz wysłać więcej wiadomości, należy rozważyć zmianę usługi SignalR do warstwy standardowej, aby wysyłać dodatkowe wiadomości. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Uzyskiwanie pomocy

Zalecamy, aby najpierw rozwiązać problem samodzielnie. Większość problemów jest spowodowana problemami z serwerem aplikacji lub siecią. Postępuj zgodnie [z przewodnikiem rozwiązywania problemów z dziennikiem diagnostycznym](#troubleshooting-with-diagnostic-logs) i [podstawowym przewodnikiem rozwiązywania problemów,](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) aby znaleźć główną przyczynę.
Jeśli problemu nadal nie można rozwiązać, należy rozważyć otwarcie problemu w usłudze GitHub lub utworzenie biletu w witrynie Azure Portal.
Zapewnić:
1. Zakres czasu około 30 minut w przypadku wystąpienia problemu
2. Identyfikator zasobu usługi Azure SignalR
3. Szczegóły problemu, tak szczegółowe, jak to możliwe: Na przykład appserver nie wysyła wiadomości, spadki połączenia z klientem i tak dalej
4. Dzienniki zebrane po stronie serwera/klienta oraz inne materiały, które mogą być przydatne
5. [Opcjonalnie] Repro kod

> [!NOTE]
> Jeśli otworzysz problem w usłudze GitHub, zachowaj poufne informacje (na przykład identyfikator zasobu, dzienniki serwera/klienta) jako prywatne, tylko wysyłaj je do członków w organizacji firmy Microsoft.