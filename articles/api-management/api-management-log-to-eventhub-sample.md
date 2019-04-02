---
title: Monitorowanie interfejsów API za pomocą usługi Azure API Management, Event Hubs i Moesif | Dokumentacja firmy Microsoft
description: Przykładowa aplikacja ukazujące zasad dziennika do Centrum zdarzeń przez połączenie usługi Azure API Management, Azure Event Hubs i Moesif dla protokołu HTTP, rejestrowania i monitorowania
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
ms.openlocfilehash: c52a1942bda9881f8f782a227c81feaa4813722d
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793645"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Monitorowanie za pomocą usługi Azure API Management, Event Hubs i Moesif interfejsów API
[Usługi API Management](api-management-key-concepts.md) udostępnia wiele możliwości przetwarzania żądania HTTP wysyłane do interfejsu API protokołu HTTP. Jednak istnienie żądań i odpowiedzi jest przejściowy. Żądanie jest wysyłane i ich przepływu za pomocą usługi API Management do interfejsu API zaplecza. Interfejs API przetwarza żądania i odpowiedzi są przekazywane za pośrednictwem do konsumenta interfejsu API. Usługa API Management zachowuje niektórych ważnych statystyk dotyczących interfejsów API w celu wyświetlenia w nawigacyjnym witryny Azure portal, w ale nie tylko, że szczegóły znikną.

Za pomocą zasad dziennika do Centrum zdarzeń w usłudze API Management, można wysyłać żadnych szczegółów z żądania i odpowiedzi na [usługi Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Istnieje wiele powodów dlaczego warto generowanie zdarzeń na podstawie wiadomości HTTP są wysyłane do interfejsów API. Przykłady: dziennik inspekcji aktualizacji, analizy użycia, wyjątek alertów i integracje innych firm.

W tym artykule pokazano, jak przechwytywać cały komunikat żądania i odpowiedzi HTTP i następnie przekazać ten komunikat do usługi innej firmy, która dostarcza HTTP, rejestrowanie i monitorowanie usług i wyślij ją do Centrum zdarzeń.

## <a name="why-send-from-api-management-service"></a>Dlaczego warto wysłać z usługi API Management?
Istnieje możliwość napisania oprogramowania pośredniczącego protokołu HTTP, które można podłączyć do struktury interfejsu API protokołu HTTP do przechwycenia żądań i odpowiedzi HTTP i przekazania ich do rejestrowania i monitorowania systemów. Wadą tego podejścia jest oprogramowanie pośredniczące HTTP musi być zintegrowane z interfejsu API zaplecza i musi odpowiadać platformy interfejsu API. W przypadku wielu interfejsów API każdej z nich należy wdrożyć oprogramowanie pośredniczące. Często są przyczyny, dlaczego nie można zaktualizować interfejsów API w wewnętrznej bazie danych.

Integracja z infrastruktury rejestrowania przy użyciu usługi Azure API Management zapewnia rozwiązanie scentralizowany i niezależne od platformy. Jest również skalowalne, w całości ze względu na [geografickou replikaci](api-management-howto-deploy-multi-region.md) możliwości usługi Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Dlaczego warto wysłać do usługi Azure Event Hub?
Jest to uzasadnione zadać, dlaczego Tworzenie zasad, które są specyficzne dla usługi Azure Event Hubs? Istnieje wiele różnych miejsc, w których może być Moje żądania logowania. Dlaczego nie po prostu wysłać żądania bezpośrednio do ostatecznego miejsca docelowego?  To opcja. Podczas wprowadzania żądań rejestrowania z usługi API management, należy wziąć pod uwagę, jak rejestrowanie komunikatów ma wpływ na wydajność interfejsu API. Stopniowy wzrost obciążenia mogą być obsługiwane przez odpowiednie zwiększenie dostępne wystąpienia składników systemu lub wykorzystując replikacji geograficznej. Krótkie skoków ruchu może jednak spowodować żądań opóźnienie, jeśli żądania do rejestrowania infrastruktury zaczynają wolne pod obciążeniem.

Azure Event Hubs służy do transferu danych przychodzących olbrzymie ilości danych, o pojemności dla zajmujących się znacznie większej liczby zdarzeń niż liczba żądań HTTP większość procesów interfejsów API. Centrum zdarzeń pełni rolę bufora zaawansowanych między usługi API management i infrastrukturą, która przechowuje i przetwarza komunikaty. Daje to gwarancję, że wydajność interfejsu API nie będzie ponieść z powodu infrastruktury rejestrowania.

Po upływie dane do Centrum zdarzeń jest trwały i będzie czekać konsumentów Centrum zdarzeń do ich przetworzenia. Centrum zdarzeń obchodzi, jak przebiega przetwarzanie, ją po prostu tematyczne upewnienie się, że zostanie pomyślnie dostarczyć wiadomości.

Usługa Event Hubs ma możliwość Przesyłaj strumieniowo wydarzenia na wiele grup odbiorców. Dzięki temu zdarzeń do przetworzenia przez różne systemy. Dzięki temu, obsługa wielu scenariuszy integracji bez konieczności umieszczania Dodawanie opóźnienia na przetwarzanie żądań do interfejsu API w usłudze API Management, jak tylko jedno zdarzenie musi być wygenerowana.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Zasady w celu wysyłania komunikatów protokół http i aplikacji
Centrum zdarzeń akceptuje dane zdarzenia prosty ciąg znaków. Zawartość tego ciągu, zależą od Ciebie. Aby można było spakować żądania HTTP do wysłania go do usługi Event Hubs, należy sformatować ciąg z informacjami o żądania lub odpowiedzi. W sytuacjach, podobny do powyższego Jeśli istniejący format można ponownie użyć, a następnie napisać własną podczas analizowania kodu może być niedostępna. Początkowo I uznane za pomocą [HAR](http://www.softwareishard.com/blog/har-12-spec/) do wysyłania żądań i odpowiedzi HTTP. Jednak ten format jest zoptymalizowany do przechowywania sekwencji żądań HTTP w formacie opartych na formacie JSON. Zawiera szereg wymagane elementy, które cechują się niepotrzebne większą złożonością scenariusza przekazywania komunikatu HTTP przez sieć.

Dostępna Alternatywna opcja było jednoczesne używanie `application/http` typu nośnika, jak opisano w specyfikacji protokołu HTTP [RFC 7230](https://tools.ietf.org/html/rfc7230). Ten typ nośnika używa dokładnie tego samego formatu używanego w celu rzeczywistego wysłania wiadomości HTTP przez sieć, ale cały komunikat można umieścić w treści kolejne żądanie HTTP. W naszym przypadku są po prostu użyjemy treść jako naszej wiadomości do wysłania do usługi Event Hubs. Wygodne jest analizatora składni, która znajduje się w [Microsoft ASP.NET Web API 2.2 Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) bibliotek, które można przeanalizować tego formatu i przekonwertować go na natywnym `HttpRequestMessage` i `HttpResponseMessage` obiektów.

Aby można było utworzyć ten komunikat, należy korzystać z zalet języka C# na podstawie [wyrażenia zasad](/azure/api-management/api-management-policy-expressions) w usłudze Azure API Management. Poniżej przedstawiono zasady, które wysyła komunikat żądania HTTP do usługi Azure Event Hubs.

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
Istnieje kilka kwestii określonego warte wymienienie o to wyrażenie zasad. Zasada dziennika do Centrum zdarzeń ma atrybutu o nazwie rejestratora id, który odnosi się do nazwy rejestratora, który został utworzony w usłudze API Management. Szczegółowe informacje o sposobie ustawiania rejestratora Centrum zdarzeń w usłudze API Management można znaleźć w dokumencie [jak rejestrowanie zdarzeń w usłudze Azure Event Hubs w usłudze Azure API Management](api-management-howto-log-event-hubs.md). Drugi atrybut jest opcjonalnym parametrem, który powoduje, że usługa Event Hubs, która partycji do przechowywania wiadomości w. Usługa Event Hubs używa partycji, aby włączyć skalowalność i wymagają co najmniej dwa. Uporządkowany dostarczania wiadomości tylko jest gwarantowane, w ramach partycji. Jeśli można wydać Centrum zdarzeń, w której partycji można umieścić komunikatu, używa algorytmu okrężnym do dystrybucji obciążenia. Jednakże mogą powodować niektóre z naszych komunikatów poza kolejnością przetwarzania.

### <a name="partitions"></a>Partycje
Aby zapewnić nasze komunikaty są dostarczane do odbiorców w kolejności i skorzystać z możliwości rozkładu obciążenia partycji, wybieram wysyłać komunikaty żądania HTTP do jednej partycji i komunikatów odpowiedzi HTTP do drugiej partycji. Dzięki temu dystrybucji równomiernie obciążone, a firma Microsoft gwarantuje, że wszystkie żądania, które będą używane w kolejności, a wszystkie odpowiedzi są używane w kolejności. Istnieje możliwość, że odpowiedź zużywanego przed odpowiedniego żądania, ale nie jest problemem, ponieważ mamy innego mechanizmu do korelacji żądania do odpowiedzi i wiemy, że żądania pochodzą zawsze przed odpowiedzi.

### <a name="http-payloads"></a>Ładunków HTTP
Po kompilacji `requestLine`, możemy sprawdzić, aby wyświetlić treść żądania powinna zostać obcięty. Treść żądania jest obcinana do tylko 1024. To może zostać zwiększona, jednak pojedynczych komunikatów do Centrum zdarzeń jest ograniczona do 256 KB, więc istnieje prawdopodobieństwo, że niektóre komunikaty HTTP ustanawiają będzie mieści się w pojedynczym komunikacie. W trakcie rejestrowania i analizy znacznej ilości informacji mogą pochodzić z po prostu wiersz żądania HTTP i nagłówków. Również zwrócić tylko mały ciała żądania wiele interfejsów API i więc utraty wartości informacji tworzy dużych treści jest dość minimalny, porównaniu zmniejszenie transferu, przetwarzania i koszty magazynowania, aby zachować całą zawartość treści. Końcowe pamiętać o przetwarzania treści jest potrzebujemy przekazać `true` do `As<string>()` metody ponieważ możemy odczytujesz zawartość treści, jednak podano również potrzebowała zaplecza interfejsu API, aby można było odczytać treści. Przekazując wartość true, ta metoda, możemy spowodować, że treść w celu buforowane, tak aby mogły zostać odczytane po raz drugi. Jest to ważne, aby wiedzieć, w przypadku interfejsu API, który przekazywanie dużych plików, lub korzysta z długiego sondowania. W takich przypadkach byłoby unikanie podczas odczytywania treści w ogóle.

### <a name="http-headers"></a>Nagłówki HTTP
Nagłówki HTTP może być przenoszony przez na format wiadomości w formacie proste klucz/wartość pary. Wybraliśmy odłączenia niektóre zabezpieczenia poufnych pola wyciekiem niepotrzebnie informacji o poświadczeniach. Jest mało prawdopodobne, że klucze interfejsu API i innych poświadczeń będzie służyć do celów analizy. Jeśli firma chce wykonywać analizę na użytkownika i określonego produktu, które używają, a następnie firma Microsoft można uzyskać z `context` obiektu i dodać je do wiadomości.

### <a name="message-metadata"></a>Metadane wiadomości
Podczas kompilowania pełny komunikat do wysłania do Centrum zdarzeń, pierwszy wiersz nie jest faktycznie wchodzi w skład `application/http` wiadomości. Pierwszy wiersz jest dodatkowe metadane składający się z tego, czy komunikat jest żądania lub odpowiedzi komunikat i identyfikator, który służy do skorelowania żądań do odpowiedzi. Identyfikator wiadomości jest tworzona przy użyciu innego zasad, które wyglądają następująco:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Firma Microsoft może utworzony komunikat żądania, który przechowywany w zmiennej, do czasu odpowiedzi był zwracane, a następnie wysyłane żądania i odpowiedzi jako jeden komunikat o. Jednak przez wysyłanie żądań i odpowiedzi niezależnie i skorelować dwie przy użyciu identyfikatora wiadomości, uzyskujemy nieco większą elastyczność w rozmiarze komunikatu możliwości, aby móc korzystać z wielu partycji, przy jednoczesnym zachowaniu kolejność komunikatów i żądania zostanie wyświetlony w naszym rejestrowania pulpit nawigacyjny wcześniej. Ponadto mogą wystąpić sytuacje, w którym prawidłowej odpowiedzi nigdy nie są wysyłane do Centrum zdarzeń, prawdopodobnie z powodu błędu krytycznego żądanie w usłudze API Management, ale wciąż istnieje rekord żądania.

Zasady Aby wysłać komunikat odpowiedzi HTTP będzie zbliżony do żądania i dlatego konfiguracja zasad pełną wygląda następująco:

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

`set-variable` Zasad tworzy wartość, który jest dostępny dla obu `log-to-eventhub` zasady `<inbound>` sekcji i `<outbound>` sekcji.

## <a name="receiving-events-from-event-hubs"></a>Odbieranie zdarzeń z usługi Event Hubs
Zdarzenia z usługi Azure Event Hub są odbierane za pomocą [protokołu AMQP](https://www.amqp.org/). Zespół Microsoft Service Bus wprowadzono klienta biblioteki można ułatwić odbierająca komunikaty zdarzeń. Istnieją dwa różne podejścia obsługiwane, są jedną *klientów bezpośrednich* i innych za pomocą `EventProcessorHost` klasy. Przykłady te dwa rozwiązania można znaleźć w [Event Hubs Programming Guide](../event-hubs/event-hubs-programming-guide.md). Jest krótka wersja różnic, `Direct Consumer` daje pełną kontrolę i `EventProcessorHost` nie część obciążenia pracą nadmiar, na można ale sprawia, że niektóre założenia dotyczące sposobu przetwarzania tych zdarzeń.

### <a name="eventprocessorhost"></a>EventProcessorHost
W tym przykładzie używamy `EventProcessorHost` dla uproszczenia, jednak może ona nie jest najlepszym wyborem dla tego scenariusza. `EventProcessorHost` wykonuje trudną pracę z upewnienie się, że nie masz już martwić się o problemy w klasie procesora konkretne zdarzenie wielowątkowości. Jednak w naszym scenariuszu jesteśmy po prostu konwertowanie wiadomość na inny format i przekazanie jej wzdłuż do innej usługi, za pomocą metody asynchronicznej. Nie ma potrzeby aktualizowania udostępnionego stanu i dlatego nie ma ryzyka problemy wielowątkowości. W przypadku większości scenariuszy `EventProcessorHost` najlepszym rozwiązaniem jest prawdopodobnie i pewnością jest opcja łatwiejsza.

### <a name="ieventprocessor"></a>IEventProcessor
Centralnej pojęcia, korzystając z `EventProcessorHost` polega na utworzeniu implementację `IEventProcessor` interfejs, który zawiera metodę `ProcessEventAsync`. Kluczową cechą tej metody jest następujący:

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

Lista obiektów EventData są przekazywane do metody i możemy przejść przez tę listę. Bajty każdej metody są parsowane do obiektu HttpMessage i ten obiekt jest przekazywany do wystąpienia IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
`HttpMessage` Wystąpienie zawiera trzy elementy danych:

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

`HttpMessage` Wystąpienie zawiera `MessageId` identyfikator GUID, który pozwala nam na żądania HTTP łączenie się z odpowiedniej odpowiedzi HTTP i wartość logiczną, która określa, czy obiekt zawiera wystąpienie HttpRequestMessage i obiektu HttpResponseMessage. Za pomocą wbudowanych HTTP klas z `System.Net.Http`, udało mi się korzystać z zalet `application/http` podczas analizowania kodu, który znajduje się w `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
`HttpMessage` Wystąpienia jest następnie przekazywany do implementacji `IHttpMessageProcessor`, czyli interfejs został utworzony do oddzielenia odbieranie i interpretacji zdarzenia z usługi Azure Event Hub i odpowiada za przetwarzanie.

## <a name="forwarding-the-http-message"></a>Przekazywanie komunikatów HTTP
W tym przykładzie I uzgodnionych, byłoby interesujące wypychania żądania HTTP za pośrednictwem do [Moesif interfejsu API analizy](https://www.moesif.com). Moesif jest usługą opartą na chmurze, która specjalizuje się w analizę HTTP i debugowania. Mają one warstwę bezpłatną, dzięki czemu można łatwo spróbować i pozwala zobaczyć żądania HTTP do transmisji w czasie rzeczywistym za pomocą naszej usługi API Management.

`IHttpMessageProcessor` Implementacji wyglądają następująco,

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

`MoesifHttpMessageProcessor` Wykorzystuje [ C# biblioteki interfejsu API dla Moesif](https://www.moesif.com/docs/api?csharp#events) który ułatwia wypychanie HTTP danych zdarzeń do usługi. Aby wysyłać dane HTTP do interfejsu API modułu zbierającego Moesif, konieczne będzie konto i identyfikator aplikacji. Pobieranie identyfikatora aplikacji Moesif przez utworzenie konta na [witryny sieci Web firmy Moesif](https://www.moesif.com) a następnie przejdź do _Menu po prawej stronie najwyższego_ -> _konfiguracji aplikacji_.

## <a name="complete-sample"></a>Pełny przykład
[Kod źródłowy](https://github.com/dgilling/ApimEventProcessor) i testów na potrzeby przykładu w usłudze GitHub. Potrzebujesz [usługi API Management](get-started-create-service-instance.md), [połączonego Centrum zdarzeń](api-management-howto-log-event-hubs.md), a [konta magazynu](../storage/common/storage-create-storage-account.md) do uruchomienia przykładu dla siebie.   

Próbka jest po prostu prostą aplikację Konsolową która nasłuchuje zdarzeń przychodzących z Centrum zdarzeń, konwertuje je na Moesif `EventRequestModel` i `EventResponseModel` obiektów i przekazuje je do interfejsu API modułu zbierającego Moesif.

Na poniższej ilustracji animowany widać żądań wysyłanych do interfejsu API w portalu dla deweloperów, aplikacji konsoli, przedstawiający komunikat jest otrzymał, przetwarzane i przesyłane dalej i następnie żądania i odpowiedzi, które pojawią się w zdarzeniu Stream.

![Pokaz żądanie przesyłane do Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Podsumowanie
Usługa API Management udostępnia doskonale nadaje się do przechwytywania ruchu HTTP podróży do i z interfejsów API. Usługa Azure Event Hubs to wysoce skalowalne, ekonomiczne rozwiązanie do przechwytywania tego ruchu i systemów pomocniczych przetwarzania na rejestrowanie, monitorowanie i innych zaawansowanych możliwości analitycznych pojazdów. Łączenie z monitorowania systemów, takich jak Moesif wystarczy kilka wierszy kodu, kilkudziesięciu ruchu innych firm.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej na temat usługi Azure Event Hubs
  * [Rozpoczynanie pracy z usługą Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Odbieranie komunikatów za pomocą klasy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Przewodnik programowania w usłudze Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Dowiedz się więcej na temat integracji usługi API Management i centrów zdarzeń
  * [Jak rejestrowanie zdarzeń w usłudze Azure Event Hubs w usłudze Azure API Management](api-management-howto-log-event-hubs.md)
  * [Odwołanie do jednostki rejestratora](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [informacje o zasadach dziennika do Centrum zdarzeń](/azure/api-management/api-management-advanced-policies#log-to-eventhub)
