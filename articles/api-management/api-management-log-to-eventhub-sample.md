---
title: Monitorowanie interfejsów API za pomocą usługi Azure API Management, Event Hubs i Moesif
titleSuffix: Azure API Management
description: Przykładowa aplikacja demonstrująca zasady logowania do eventhub przez połączenie usługi Azure API Management, Usługi Azure Event Hubs i Moesif w celu rejestrowania i monitorowania http
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: 4a0717bf7a284668af4808acae3050cc7f42f836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442521"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Monitorowanie interfejsów API za pomocą usługi Azure API Management, Event Hubs i Moesif
[Usługa zarządzania interfejsami API](api-management-key-concepts.md) zapewnia wiele możliwości usprawniania przetwarzania żądań HTTP wysyłanych do interfejsu API HTTP. Jednak istnienie żądań i odpowiedzi jest przejściowe. Żądanie jest wykonane i przepływa za pośrednictwem usługi api Management do interfejsu API wewnętrznej bazy danych. Interfejs API przetwarza żądanie i odpowiedź przepływa z powrotem do konsumenta interfejsu API. Usługa zarządzania interfejsami API przechowuje niektóre ważne statystyki dotyczące interfejsów API do wyświetlenia na pulpicie nawigacyjnym portalu Azure, ale poza tym szczegóły zniknęły.

Korzystając z zasad log-to-eventhub w usłudze zarządzania interfejsami API, można wysłać dowolne szczegóły z żądania i odpowiedzi do [usługi Azure Event Hub.](../event-hubs/event-hubs-what-is-event-hubs.md) Istnieje wiele powodów, dla których warto generować zdarzenia z wiadomości HTTP wysyłanych do interfejsów API. Niektóre przykłady obejmują dziennik inspekcji aktualizacji, analizy użycia, alerty o wyjątkach i integracje innych firm.

W tym artykule pokazano, jak przechwycić cały komunikat żądania i odpowiedzi HTTP, wysłać go do Centrum zdarzeń, a następnie przekazać tę wiadomość do usługi innej firmy, która udostępnia usługi rejestrowania i monitorowania HTTP.

## <a name="why-send-from-api-management-service"></a>Dlaczego warto wysyłać z usługi zarządzania interfejsami API?
Istnieje możliwość zapisu oprogramowania pośredniczącego HTTP, które można podłączyć do frameworków INTERFEJSU API HTTP, aby przechwytywać żądania i odpowiedzi HTTP i przesyłać je do systemów rejestrowania i monitorowania. Wadą tego podejścia jest oprogramowanie pośredniczące HTTP musi być zintegrowany z interfejsu API wewnętrznej bazy danych i musi odpowiadać platformie interfejsu API. Jeśli istnieje wiele interfejsów API, a następnie każdy z nich należy wdrożyć oprogramowanie pośredniczące. Często istnieją powody, dla których nie można zaktualizować interfejsów API wewnętrznej bazy danych.

Korzystanie z usługi Azure API Management do integracji z infrastrukturą rejestrowania zapewnia scentralizowane i niezależne od platformy rozwiązanie. Jest również skalowalny, częściowo ze względu na możliwości [replikacji geograficznej](api-management-howto-deploy-multi-region.md) usługi Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Dlaczego warto wysyłać do usługi Azure Event Hub?
Uzasadnione jest pytanie, dlaczego warto utworzyć zasadę specyficzną dla usługi Azure Event Hubs? Istnieje wiele różnych miejsc, w których mogę chcieć zarejestrować moje żądania. Dlaczego nie wysłać wniosków bezpośrednio do miejsca docelowego?  To jest opcja. Jednak podczas rejestrowania żądań z usługi zarządzania interfejsem API, należy wziąć pod uwagę, jak rejestrowanie komunikatów wpływa na wydajność interfejsu API. Stopniowy wzrost obciążenia może być obsługiwany przez zwiększenie dostępnych wystąpień składników systemu lub korzystając z replikacji geograficznej. Jednak krótkie skoki ruchu może spowodować żądania być opóźnione, jeśli żądania do rejestrowania infrastruktury zaczynają spowalniać pod obciążeniem.

Usługa Azure Event Hubs jest przeznaczony do transferu danych przychodzących ogromne ilości danych, z pojemności do czynienia z znacznie większą liczbę zdarzeń niż liczba żądań HTTP większość procesów interfejsów API. Centrum zdarzeń działa jako rodzaj zaawansowanego buforu między usługą zarządzania interfejsem API a infrastrukturą, która przechowuje i przetwarza komunikaty. Gwarantuje to, że wydajność interfejsu API nie ucierpi z powodu infrastruktury rejestrowania.

Po przekazaniu danych do Centrum zdarzeń jest utrwalony i będzie czekać na konsumentów Usługi Event Hub do przetworzenia go. Centrum zdarzeń nie dba o sposób przetwarzania, po prostu dba o upewnienie się, że wiadomość zostanie pomyślnie dostarczona.

Centra zdarzeń ma możliwość przesyłania strumieniowego zdarzeń do wielu grup odbiorców. Dzięki temu zdarzenia mogą być przetwarzane przez różne systemy. Umożliwia to obsługę wielu scenariuszy integracji bez umieszczania opóźnień dodawania przetwarzania żądania interfejsu API w ramach usługi zarządzania interfejsami API, ponieważ musi zostać wygenerowane tylko jedno zdarzenie.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Zasady do wysyłania wiadomości aplikacji/http
Centrum zdarzeń akceptuje dane zdarzeń jako prosty ciąg. Zawartość tego ciągu zależy od Ciebie. Aby móc spakować żądanie HTTP i wysłać go do Centrum zdarzeń, musimy sformatować ciąg z informacjami o żądaniu lub odpowiedzi. W takich sytuacjach, jeśli istnieje istniejący format, który możemy użyć ponownie, możemy nie być naliczeniem własnego kodu analizy. Początkowo rozważałem użycie [HAR](http://www.softwareishard.com/blog/har-12-spec/) do wysyłania żądań HTTP i odpowiedzi. Jednak ten format jest zoptymalizowany do przechowywania sekwencji żądań HTTP w formacie opartym na JSON. Zawierała ona szereg obowiązkowych elementów, które dodały niepotrzebną złożoność scenariusza przekazywania wiadomości HTTP przez sieć.

Alternatywną opcją było `application/http` użycie typu nośnika zgodnie z opisem w specyfikacji HTTP [RFC 7230](https://tools.ietf.org/html/rfc7230). Ten typ nośnika używa dokładnie tego samego formatu, który jest używany do wysyłania wiadomości HTTP za pośrednictwem sieci, ale całą wiadomość można umieścić w treści innego żądania HTTP. W naszym przypadku po prostu będziemy używać treści jako naszej wiadomości, aby wysłać do Centrum zdarzeń. Wygodnie jest analizator, który istnieje w [microsoft ASP.NET web API 2.2 biblioteki klienta,](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) które można przeanalizować `HttpRequestMessage` `HttpResponseMessage` ten format i przekonwertować go na natywnych i obiektów.

Aby móc utworzyć ten komunikat, musimy skorzystać z [wyrażeń zasad opartych](/azure/api-management/api-management-policy-expressions) na języku C# w usłudze Azure API Management. Oto zasady, które wysyła komunikat żądania HTTP do usługi Azure Event Hubs.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Deklaracja zasad
Jest kilka konkretnych rzeczy, o których warto wspomnieć o tym wyrażeniu polityki. Zasady log-to-eventhub ma atrybut o nazwie logger-id, który odwołuje się do nazwy rejestratora, który został utworzony w ramach usługi zarządzania interfejsem API. Szczegółowe informacje na temat konfigurowania rejestratora Centrum zdarzeń w usłudze zarządzania interfejsami API można znaleźć w dokumencie [Jak rejestrować zdarzenia w centrum zdarzeń platformy Azure w usłudze Azure API Management.](api-management-howto-log-event-hubs.md) Drugi atrybut jest opcjonalnym parametrem, który instruuje centra zdarzeń, w której partycji ma być przechowywana wiadomość. Centra zdarzeń używa partycji, aby włączyć skalowalność i wymagają co najmniej dwóch. Zamówione dostarczanie wiadomości jest gwarantowane tylko w ramach partycji. Jeśli nie poinstruujemy Centrum zdarzeń, w którym partycji, aby umieścić wiadomość, używa algorytmu okrężnego do dystrybucji obciążenia. Może to jednak spowodować, że niektóre z naszych wiadomości zostaną przetworzone poza kolejnością.

### <a name="partitions"></a>Partycje
Aby upewnić się, że nasze wiadomości są dostarczane do konsumentów w kolejności i skorzystać z możliwości dystrybucji obciążenia partycji, wybrałem do wysyłania wiadomości żądania HTTP do jednej partycji i wiadomości odpowiedzi HTTP do drugiej partycji. Zapewnia to równomierną dystrybucję obciążenia i możemy zagwarantować, że wszystkie żądania zostaną zużyte w kolejności, a wszystkie odpowiedzi zostaną zużyte w kolejności. Jest możliwe, aby odpowiedź została wykorzystana przed odpowiednim żądaniem, ale ponieważ nie stanowi to problemu, ponieważ mamy inny mechanizm korelowania żądań do odpowiedzi i wiemy, że żądania zawsze są dostępne przed odpowiedziami.

### <a name="http-payloads"></a>Ładunki HTTP
Po zbudowaniu `requestLine`, sprawdzamy, czy treść wniosku powinna zostać obcięty. Treść żądania jest obcinana tylko do 1024. Ten może zostać zwiększona, jednak poszczególne komunikaty Centrum zdarzeń są ograniczone do 256 KB, więc jest prawdopodobne, że niektóre treści wiadomości HTTP nie zmieści się w jednej wiadomości. Podczas rejestrowania i analizy znaczną ilość informacji można uzyskać tylko z wiersza żądania HTTP i nagłówków. Ponadto wiele interfejsów API żąda tylko zwracać małe obiekty, a więc utrata wartości informacji przez obcinanie dużych obiektów jest dość minimalna w porównaniu do zmniejszenia kosztów transferu, przetwarzania i przechowywania, aby zachować całą zawartość treści. Ostatnią uwagą na temat przetwarzania ciała `true` jest `As<string>()` to, że musimy przejść do metody, ponieważ czytamy zawartość treści ciała, ale chciał również interfejs API wewnętrznej bazy danych, aby móc odczytać treść. Przekazując wierność tej metodzie, powodujemy, że ciało ma być buforowane, aby można było je odczytać po raz drugi. Jest to ważne, aby pamiętać, jeśli masz interfejs API, który przekazuje dużych plików lub używa długiego sondowania. W takich przypadkach najlepiej byłoby w ogóle unikać czytania ciała.

### <a name="http-headers"></a>Nagłówki HTTP
Nagłówki HTTP można przenieść do formatu wiadomości w prostym formacie pary klucz/wartość. Postanowiliśmy usunąć niektóre wrażliwe obszary zabezpieczeń, aby uniknąć niepotrzebnego wycieku informacji o poświadczeniach. Jest mało prawdopodobne, że klucze interfejsu API i inne poświadczenia będą używane do celów analitycznych. Jeśli chcemy zrobić analizę użytkownika i konkretnego produktu, którego używają, możemy `context` uzyskać to z obiektu i dodać to do wiadomości.

### <a name="message-metadata"></a>Metadane wiadomości
Podczas tworzenia pełnej wiadomości do wysłania do centrum zdarzeń, `application/http` pierwszy wiersz nie jest faktycznie częścią wiadomości. Pierwszy wiersz to dodatkowe metadane składające się z tego, czy wiadomość jest komunikatem żądania lub odpowiedzi oraz identyfikatorem wiadomości, który jest używany do skorelowania żądań do odpowiedzi. Identyfikator wiadomości jest tworzony przy użyciu innej zasady, która wygląda następująco:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Mogliśmy utworzyć komunikat żądania, przechowywane, że w zmiennej, dopóki odpowiedź została zwrócona, a następnie wysłał żądanie i odpowiedź jako pojedynczą wiadomość. Jednak wysyłając żądanie i odpowiedź niezależnie i używając identyfikatora wiadomości do skorelowania dwóch, otrzymujemy nieco większą elastyczność w rozmiarze wiadomości, możliwość korzystania z wielu partycji przy jednoczesnym zachowaniu kolejności wiadomości i pojawi się żądanie w naszym panelu rejestrowania wcześniej. Mogą istnieć również pewne scenariusze, w których prawidłowa odpowiedź nigdy nie jest wysyłana do Centrum zdarzeń, prawdopodobnie z powodu błędu żądania krytycznego w usłudze zarządzania interfejsami API, ale nadal mamy rekord żądania.

Zasady do wysyłania odpowiedzi komunikat HTTP wygląda podobnie do żądania i tak pełna konfiguracja zasad wygląda następująco:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

Zasady `set-variable` tworzy wartość, która jest `log-to-eventhub` dostępna zarówno `<inbound>` przez `<outbound>` zasady w sekcji i sekcji.

## <a name="receiving-events-from-event-hubs"></a>Odbieranie zdarzeń z Centrów zdarzeń
Zdarzenia z usługi Azure Event Hub są odbierane przy użyciu [protokołu AMQP](https://www.amqp.org/). Zespół usługi Microsoft Service Bus udostępnił biblioteki klientów, aby ułatwić korzystanie ze zdarzeń. Istnieją dwa różne podejścia obsługiwane, jeden jest *direct konsumenta,* `EventProcessorHost` a drugi używa klasy. Przykłady tych dwóch podejść można znaleźć w [Przewodniku programowania centrum zdarzeń](../event-hubs/event-hubs-programming-guide.md). Krótka wersja różnic jest, `Direct Consumer` daje pełną kontrolę `EventProcessorHost` i nie niektóre prace hydrauliczne dla Ciebie, ale sprawia, że pewne założenia o tym, jak przetwarzać te zdarzenia.

### <a name="eventprocessorhost"></a>EventProcessorHost
W tym przykładzie `EventProcessorHost` używamy dla uproszczenia, jednak może nie jest to najlepszy wybór dla tego konkretnego scenariusza. `EventProcessorHost`wykonuje ciężką pracę upewniając się, że nie musisz się martwić o problemy wątkowe w ramach określonej klasy procesora zdarzeń. Jednak w naszym scenariuszu po prostu konwertujemy wiadomość do innego formatu i przekazujemy ją do innej usługi przy użyciu metody asynchronii. Nie ma potrzeby aktualizowania stanu udostępnionego i w związku z tym nie ma ryzyka problemów z wątkami. W większości scenariuszy jest `EventProcessorHost` to prawdopodobnie najlepszy wybór i z pewnością jest to łatwiejsza opcja.

### <a name="ieventprocessor"></a>IEventProcessor
Główną koncepcją `EventProcessorHost` podczas korzystania jest `IEventProcessor` utworzenie implementacji interfejsu, który zawiera metodę `ProcessEventAsync`. Istota tej metody jest pokazana tutaj:

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

    foreach (EventData eventData in messages)
    {
        _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

        try
        {
            var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
            await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
        }
        catch (Exception ex)
        {
            _Logger.LogError(ex.Message);
        }
    }
    ... checkpointing code snipped ...
}
```

Lista EventData obiektów są przekazywane do metody i iteracji nad tą listą. Bajty każdej metody są analizowane w obiekcie HttpMessage i ten obiekt jest przekazywany do wystąpienia IHttpMessageProcessor.

### <a name="httpmessage"></a>Komunikat y łącze
Wystąpienie `HttpMessage` zawiera trzy fragmenty danych:

```csharp
public class HttpMessage
{
    public Guid MessageId { get; set; }
    public bool IsRequest { get; set; }
    public HttpRequestMessage HttpRequestMessage { get; set; }
    public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

Wystąpienie `HttpMessage` zawiera `MessageId` identyfikator GUID, który pozwala nam połączyć żądanie HTTP z odpowiednią odpowiedzią HTTP i wartością logiczną, która identyfikuje, jeśli obiekt zawiera wystąpienie httprequestMessage i HttpResponseMessage. Za pomocą wbudowanych klas `System.Net.Http`HTTP z , byłem w `application/http` stanie skorzystać z kodu `System.Net.Http.Formatting`analizy, który jest zawarty w .  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
Wystąpienie `HttpMessage` jest następnie przekazywane `IHttpMessageProcessor`do implementacji , który jest interfejs utworzony w celu oddzielenia odbierania i interpretacji zdarzenia z usługi Azure Event Hub i rzeczywistego przetwarzania go.

## <a name="forwarding-the-http-message"></a>Przesyłanie dalej wiadomości HTTP
Dla tego przykładu, postanowiłem, że byłoby interesujące, aby przesunąć żądanie HTTP do [Moesif API Analytics](https://www.moesif.com). Moesif to usługa oparta na chmurze, która specjalizuje się w analizie HTTP i debugowaniu. Mają bezpłatną warstwę, więc jest to łatwe do wypróbowania i pozwala nam zobaczyć żądania HTTP w czasie rzeczywistym przepływających za pośrednictwem naszej usługi zarządzania interfejsami API.

Implementacja `IHttpMessageProcessor` wygląda tak,

```csharp
public class MoesifHttpMessageProcessor : IHttpMessageProcessor
{
    private readonly string RequestTimeName = "MoRequestTime";
    private MoesifApiClient _MoesifClient;
    private ILogger _Logger;
    private string _SessionTokenKey;
    private string _ApiVersion;
    public MoesifHttpMessageProcessor(ILogger logger)
    {
        var appId = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-APP-ID", EnvironmentVariableTarget.Process);
        _MoesifClient = new MoesifApiClient(appId);
        _SessionTokenKey = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-SESSION-TOKEN", EnvironmentVariableTarget.Process);
        _ApiVersion = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-API-VERSION", EnvironmentVariableTarget.Process);
        _Logger = logger;
    }

    public async Task ProcessHttpMessage(HttpMessage message)
    {
        if (message.IsRequest)
        {
            message.HttpRequestMessage.Properties.Add(RequestTimeName, DateTime.UtcNow);
            return;
        }

        EventRequestModel moesifRequest = new EventRequestModel()
        {
            Time = (DateTime) message.HttpRequestMessage.Properties[RequestTimeName],
            Uri = message.HttpRequestMessage.RequestUri.OriginalString,
            Verb = message.HttpRequestMessage.Method.ToString(),
            Headers = ToHeaders(message.HttpRequestMessage.Headers),
            ApiVersion = _ApiVersion,
            IpAddress = null,
            Body = message.HttpRequestMessage.Content != null ? System.Convert.ToBase64String(await message.HttpRequestMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        EventResponseModel moesifResponse = new EventResponseModel()
        {
            Time = DateTime.UtcNow,
            Status = (int) message.HttpResponseMessage.StatusCode,
            IpAddress = Environment.MachineName,
            Headers = ToHeaders(message.HttpResponseMessage.Headers),
            Body = message.HttpResponseMessage.Content != null ? System.Convert.ToBase64String(await message.HttpResponseMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApimMessageId", message.MessageId.ToString());

        EventModel moesifEvent = new EventModel()
        {
            Request = moesifRequest,
            Response = moesifResponse,
            SessionToken = _SessionTokenKey != null ? message.HttpRequestMessage.Headers.GetValues(_SessionTokenKey).FirstOrDefault() : null,
            Tags = null,
            UserId = null,
            Metadata = metadata
        };

        Dictionary<string, string> response = await _MoesifClient.Api.CreateEventAsync(moesifEvent);

        _Logger.LogDebug("Message forwarded to Moesif");
    }

    private static Dictionary<string, string> ToHeaders(HttpHeaders headers)
    {
        IEnumerable<KeyValuePair<string, IEnumerable<string>>> enumerable = headers.GetEnumerator().ToEnumerable();
        return enumerable.ToDictionary(p => p.Key, p => p.Value.GetEnumerator()
                                                         .ToEnumerable()
                                                         .ToList()
                                                         .Aggregate((i, j) => i + ", " + j));
    }
}
```

Korzysta `MoesifHttpMessageProcessor` z [biblioteki interfejsu API języka C# dla Moesif,](https://www.moesif.com/docs/api?csharp#events) który ułatwia wypychanie danych zdarzeń HTTP do ich usługi. Aby wysłać dane HTTP do interfejsu API modułu zbierającego Moesif, potrzebne jest konto i identyfikator aplikacji. Otrzymasz identyfikator aplikacji Moesif, tworząc konto na [stronie internetowej Moesif,](https://www.moesif.com) a następnie przejdź do_ustawienia aplikacji_menu -> w prawym górnym _rogu._

## <a name="complete-sample"></a>Kompletna próbka
[Kod źródłowy](https://github.com/dgilling/ApimEventProcessor) i testy dla przykładu są na GitHub. Do samodzielnego uruchomienia próbki potrzebna jest [usługa zarządzania interfejsami API,](get-started-create-service-instance.md) [połączone Centrum zdarzeń](api-management-howto-log-event-hubs.md)i konto [magazynu.](../storage/common/storage-create-storage-account.md)   

Przykład jest tylko prostą aplikacją konsoli, która nasłuchuje zdarzeń pochodzących `EventRequestModel` z `EventResponseModel` Usługi Event Hub, konwertuje je na Moesif i obiektów, a następnie przekazuje je do interfejsu API modułu zbierającego Moesif.

Na poniższym obrazie animowanym można zobaczyć żądanie jest wysyłane do interfejsu API w portalu dla deweloperów, aplikacji konsoli pokazującej wiadomość odbieraną, przetwarzaną i przekazyzną, a następnie żądanie i odpowiedź wyświetlane w strumieniu zdarzeń.

![Demonstracja żądania przekazanego do Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Podsumowanie
Usługa Azure API Management zapewnia idealne miejsce do przechwytywania ruchu HTTP podróżujących do i z interfejsów API. Usługa Azure Event Hubs to wysoce skalowalne, niedrogie rozwiązanie do przechwytywania tego ruchu i podawania go do dodatkowych systemów przetwarzania do rejestrowania, monitorowania i innych zaawansowanych analiz. Łączenie się z systemami monitorowania ruchu innych firm, takimi jak Moesif, jest tak proste, jak kilkadziesiąt wierszy kodu.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o usłudze Azure Event Hubs
  * [Wprowadzenie do usługi Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Odbieranie komunikatów za pomocą klasy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Przewodnik po programowaniu centrów zdarzeń](../event-hubs/event-hubs-programming-guide.md)
* Dowiedz się więcej o integracji z centrum zarządzania interfejsami API i centrami zdarzeń
  * [Jak rejestrować zdarzenia w centrum zdarzeń platformy Azure w usłudze Azure API Management](api-management-howto-log-event-hubs.md)
  * [Odwołanie do encji rejestratora](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [log-to-eventhub odwołanie do zasad](/azure/api-management/api-management-advanced-policies#log-to-eventhub)
