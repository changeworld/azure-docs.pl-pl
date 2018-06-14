---
title: Monitorowanie interfejsów API za pomocą usługi Azure API Management, usługa Event Hubs i Runscope | Dokumentacja firmy Microsoft
description: Przykładowa aplikacja prezentacja zasad dziennika do Centrum eventhub połączenia usługi Azure API Management, Azure Event Hubs i Runscope dla protokołu HTTP, rejestrowania i monitorowania
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
ms.openlocfilehash: 8ef8d64ba90960281faffc350821d7934e35749a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29117072"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Monitoruj swoje interfejsy API z usługi Azure API Management, usługa Event Hubs i Runscope
[Usługi Zarządzanie interfejsami API](api-management-key-concepts.md) zawiera wiele możliwości przetwarzania żądania HTTP wysyłane do interfejsu API protokołu HTTP. Jednak istnienie żądania i odpowiedzi jest przejściowy. Żądanie, a następnie za pomocą usługi Zarządzanie interfejsami API z wewnętrzną bazą danych interfejsu API. Interfejs API przetwarza żądania i odpowiedzi przechodzi wstecz przez konsumenta interfejsu API. Usługi Zarządzanie interfejsami API utrzymuje niektórych ważnych statystyk dotyczących interfejsów API do wyświetlenia w pulpicie nawigacyjnym portalu Azure w ale ponad, czy szczegóły znikną.

Za pomocą zasad dziennika do Centrum eventhub w usłudze API Management, możesz wysłać żadnych szczegółów żądania i odpowiedzi na [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Istnieje wiele przyczyn, dlaczego warto generowanie zdarzeń z wiadomości HTTP są wysyłane do swoje interfejsy API. Oto kilka przykładów dziennik inspekcji aktualizacji, analizy użycia, alerty wyjątków i integracji w innych firm.   

W tym artykule przedstawiono sposób przechwytywania cały komunikat żądania i odpowiedzi HTTP, wysyłają je do Centrum zdarzeń i następnie przekazywania wiadomości do usługi innej firmy, która dostarcza HTTP rejestrowania i monitorowania usług.

## <a name="why-send-from-api-management-service"></a>Dlaczego należy wysłać z interfejsu API usługi zarządzania?
Istnieje możliwość zapisu oprogramowania pośredniczącego protokołu HTTP, który można podłączyć do struktur HTTP API do przechwytywania żądań i odpowiedzi HTTP i ich źródła do rejestrowania i monitorowania systemów. Wadą tego podejścia interfejsu jest oprogramowanie pośredniczące HTTP musi być zintegrowane zaplecza interfejsu API i musi odpowiadać platformy interfejsu API. W przypadku wielu interfejsów API każdej z nich należy wdrożyć oprogramowanie pośredniczące. Często jest przyczyn, dlaczego nie można zaktualizować interfejsów API w wewnętrznej bazie danych.

Za pomocą usługi Zarządzanie interfejsami API Azure do integracji z infrastrukturą rejestrowania zapewnia rozwiązanie scentralizowany i niezależne od platformy. Jest również skalowalne, w części ze względu na [— replikacja geograficzna](api-management-howto-deploy-multi-region.md) możliwości usługi Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Dlaczego są wysyłane do usługi Azure Event Hub?
Jest to uzasadnione na pytanie, dlaczego Tworzenie zasad, które są specyficzne dla usługi Azure Event Hubs? Istnieje wiele różnych miejscach, w którym chcieć Moje żądania logowania. Dlaczego nie wystarczy wysłać żądania bezpośrednio do końcowego miejsca docelowego?  To opcja. Podczas tworzenia żądania rejestrowania z interfejsu API usługi zarządzania, należy wziąć pod uwagę wpływ rejestrowania komunikatów wydajności interfejsu API. Stopniowa wzrost obciążenia są obsługiwane przez odpowiednie zwiększenie dostępnych wystąpień składników systemu, czy dzięki wykorzystaniu — replikacja geograficzna. Krótki największego ruchu może jednak spowodować żądania do opóźnienia, jeśli żądania rejestrowania infrastruktury Uruchom, aby zmniejszyć obciążenie.

Azure Event Hubs jest przeznaczona do transferu dużych ilości danych, o pojemności na potrzeby zajmujących się znacznie większej liczby zdarzeń niż liczba żądań HTTP większości procesu interfejsów API. Centrum zdarzeń pełni rolę bufora zaawansowane między usługą API zarządzania i infrastruktury, która przechowuje i przetwarza wiadomości. Dzięki temu, że z powodu infrastruktury rejestrowania nie będzie pogorszyć wydajność z interfejsu API.  

Po upływie dane do Centrum zdarzeń jest trwały i będzie oczekiwał na konsumentów Centrum zdarzeń go przetworzyć. Centrum zdarzeń nie zależy sposób przetwarzania, ją po prostu dba upewnienie się, że wiadomość zostanie dostarczona pomyślnie.     

Usługa Event Hubs ma możliwość strumienia zdarzeń do wielu grup odbiorców. Dzięki temu zdarzenia, które mają być przetwarzane przez różnych systemów. Dzięki temu, obsługa wielu scenariuszy integracji bez umieszczania Dodawanie opóźnień w przetwarzania żądania interfejsu API w usłudze API Management, jak tylko jedno zdarzenie ma zostać wygenerowany.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Zasady do wysyłania wiadomości protokół http i aplikacji
Centrum zdarzeń akceptuje dane zdarzeń prosty ciąg znaków. Zawartość tego ciągu jest od użytkownika. Aby można było spakować żądania HTTP i wysłania go do usługi Event Hubs, musimy formatu ciągu z informacjami o żądania lub odpowiedzi. W sytuacjach, takich jak ta Jeśli istniejący format można ponownie użyć, a następnie firma Microsoft nie trzeba napisać własne analizy kodu. Początkowo I uznawane za pomocą [HAR](http://www.softwareishard.com/blog/har-12-spec/) do wysyłania żądań i odpowiedzi HTTP. Jednak ten format jest zoptymalizowany do przechowywania sekwencji żądań HTTP w formacie opartych na formacie JSON. Zawiera liczbę obowiązkowe elementy dodane niepotrzebnych złożoności scenariusza przekazywania komunikatu HTTP przez sieć.  

Opcja alternatywna było jednoczesne używanie `application/http` typ nośnika zgodnie z opisem w specyfikacji HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Ten typ nośnika używa dokładnie tego samego formatu używanego do faktycznego wysyłania wiadomości HTTP przez sieć, ale cały komunikat można umieścić w treści żądania HTTP innego. W tym przypadku po prostu zamierzamy być treści wiadomości do wysłania do usługi Event Hubs. Wygodnie jest analizatorem w [klienta Microsoft ASP.NET Web API 2.2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) bibliotek, które mogą przeanalizować tego formatu oraz przekształcać je do natywnego `HttpRequestMessage` i `HttpResponseMessage` obiektów.

Aby można było utworzyć tę wiadomość, musimy korzystać na podstawie języka C# [wyrażenie zasad](https://msdn.microsoft.com/library/azure/dn910913.aspx) w usłudze Azure API Management. Oto zasadę, która wysyła komunikat żądania HTTP do usługi Azure Event Hubs.

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
Istnieje kilka rzeczy określonego warto zauważyć o wyrażenie zasad. Zasada dziennika do Centrum eventhub ma atrybut o nazwie rejestratora — identyfikator, który odwołuje się do nazwy rejestratora, który został utworzony w ramach usługi Zarządzanie interfejsami API. Szczegóły dotyczące sposobu konfigurowania rejestratora Centrum zdarzeń w usłudze API Management znajduje się w dokumencie [sposób rejestrowania zdarzeń do usługi Azure Event Hubs w usłudze Azure API Management](api-management-howto-log-event-hubs.md). Drugi atrybut jest opcjonalnym parametrem, który powoduje, że usługa Event Hubs, która partycji do przechowywania wiadomości w. Usługa Event Hubs używa partycji, aby włączyć skalowalność i wymagają co najmniej dwa. Uporządkowanego dostarczenia komunikatów jest gwarantowaną jedynie w partycji. Jeśli można wydać Centrum zdarzeń, w których partycji można umieścić komunikat, używa algorytmu okrężnego rozłożenie obciążenia. Mogą jednak powodować niektóre nasze komunikaty do przetwarzania poza kolejnością.  

### <a name="partitions"></a>Partycje
Aby zapewnić naszym wiadomości są dostarczane do odbiorców w kolejności i skorzystać z zalet możliwości rozkładu obciążenia partycji, wybrano wysyłać żądania HTTP do jednej partycji i komunikatów odpowiedzi HTTP do drugiej partycji. Dzięki temu rozkład obciążenia nawet i firma Microsoft gwarantuje, że wszystkie żądania, które będą używane w kolejności i wszystkie odpowiedzi są używane w kolejności. Istnieje możliwość odpowiedzi zużywanych przed odpowiednie żądanie, ale który nie jest problem jak mamy inny mechanizm korelowanie żądania do odpowiedzi i wiemy, że żądania zawsze występować przed odpowiedzi.

### <a name="http-payloads"></a>Ładunki HTTP
Po budynku `requestLine`, możemy sprawdzić, czy obcięte treści żądania. Treść żądania jest obcinana tylko 1024 znaki. To można zwiększyć, ale poszczególne wiadomości Centrum zdarzeń są ograniczone do 256 KB, więc istnieje duże prawdopodobieństwo, że niektóre komunikaty HTTP organów zostanie nie mieści się w pojedynczym komunikacie. Podczas rejestrowania i analiza znaczną ilość informacji mogą pochodzić z właśnie wiersz żądania HTTP i nagłówków. Ponadto wiele interfejsów API żądania zwrócić tylko małych jednostek i dlatego utraty wartość informacji przez Obcinanie dużych jednostek jest dość minimalny w odróżnieniu od skrócenie transferu, przetwarzania i kosztów magazynowania, aby zachować całą zawartość treści. Jeden końcowego Uwaga dotycząca przetwarzanie treści jest potrzebujemy przekazać `true` do As<string>— metoda () ponieważ firma Microsoft czytają zawartość treści, jednak podano również chciał zaplecza interfejsu API, aby można było odczytać treści. Przekazując wartość PRAWDA, aby ta metoda, możemy spowodować jednostkę do buforowanego, dzięki czemu mogą być odczytywane po raz drugi. Jest to ważne, należy pamiętać o Jeśli interfejs API, który nie przekazywania dużych plików ani nie używa długiego sondowania. W takich przypadkach byłoby unikanie odczytu treści w ogóle.   

### <a name="http-headers"></a>Nagłówki HTTP
Nagłówki HTTP mogą być przesyłane przez do formatu wiadomości w formacie pary klucz/wartość prostą. Wybraliśmy usuwają niektórych zabezpieczeń pól, aby uniknąć niepotrzebnego przeciek informacji o poświadczeniach. Jest mało prawdopodobne, że klucze interfejsu API i inne poświadczenia mają być używane do celów analizy. Jeśli Życzymy czy analizy na użytkownika i określonego produktu używają, a następnie można pobrać z `context` obiektu i dodać go do wiadomości.     

### <a name="message-metadata"></a>Komunikat metadanych
Podczas kompilowania pełny komunikat do wysłania do Centrum zdarzeń, pierwszy wiersz nie jest częścią faktycznie `application/http` wiadomości. Pierwszy wiersz jest dodatkowe metadane składające się z tego, czy wiadomość jest żądanie lub komunikat odpowiedzi i identyfikator, który służy do skorelowania żądań do odpowiedzi na wiadomość. Identyfikator komunikatu jest tworzona przy użyciu innej zasady, która wygląda następująco:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Firma Microsoft może mieć utworzony komunikat żądania, który przechowywana w zmiennej, dopóki odpowiedź została zwrócona i następnie wysyłane jako pojedynczy komunikat żądania i odpowiedzi. Jednak wysyłania żądań i odpowiedzi niezależnie i za pomocą identyfikatora komunikatu do skorelowania dwa, uzyskujemy nieco większą elastyczność w rozmiar komunikatu, możliwość wykorzystania wiele partycji, jednocześnie zachowując kolejność wiadomości i żądania zostanie wyświetlony w naszym rejestrowania pulpitu nawigacyjnego wcześniej. Może istnieć sytuacje, w których prawidłowej odpowiedzi nigdy nie są wysyłane do Centrum zdarzeń, prawdopodobnie z powodu błędu krytycznego żądania w usłudze API Management, ale wciąż istnieje rekord żądania.

Zasady można wysłać komunikatu odpowiedzi HTTP podobny do żądania i dlatego konfiguracji zasad pełną wygląda następująco:

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

`set-variable` Zasad tworzy wartość, który jest dostępny dla obu `log-to-eventhub` zasad w `<inbound>` sekcji i `<outbound>` sekcji.  

## <a name="receiving-events-from-event-hubs"></a>Odbieranie zdarzeń z usługi Event Hubs
Zdarzenia z Centrum zdarzeń Azure są odbierane przy użyciu [protokołu AMQP](http://www.amqp.org/). Zespół Microsoft Service Bus zostały wprowadzone klienta biblioteki dostępne ułatwić korzystanie zdarzenia. Istnieją dwa różne podejścia obsługiwane, co jest *konsumenta bezpośredniego* , a drugi używa `EventProcessorHost` klasy. Przykłady te dwie metody znajdują się w [Event Hubs Programming Guide](../event-hubs/event-hubs-programming-guide.md). Jest krótkiej różnice, `Direct Consumer` umożliwia pełną kontrolę i `EventProcessorHost` wykonuje część pracy żmudne procesy, dla ale powoduje pewne założenia dotyczące sposobu procesu tych zdarzeń.  

### <a name="eventprocessorhost"></a>EventProcessorHost
W tym przykładzie używamy `EventProcessorHost` dla uproszczenia, jednak może ona nie najlepszym rozwiązaniem dla tego scenariusza. `EventProcessorHost`wykonuje pracę twardych zagwarantowanie, że nie trzeba martwić wątkowość problemów w obrębie klasy procesora konkretnego zdarzenia. Jednak w naszym scenariuszu firma Microsoft są po prostu Konwertowanie wiadomości do innego formatu i przekazanie jej wraz z inną usługą przy użyciu metody asynchronicznej. Nie istnieje potrzeba aktualizowania stanu udostępnionego i w związku z tym ryzyko problemy wielowątkowości. W przypadku większości scenariuszy `EventProcessorHost` najlepszym rozwiązaniem jest prawdopodobnie i na pewno jest łatwiejsze opcji.     

### <a name="ieventprocessor"></a>IEventProcessor
Centralna koncepcji przy użyciu `EventProcessorHost` jest utworzenie implementacja `IEventProcessor` interfejs, który zawiera metodę `ProcessEventAsync`. Użycia tej metody jest następujący:

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

Lista obiektów EventData są przekazywane do metody i możemy iteracja tej listy. Bajty każdej metody są parsowane do obiektu HttpMessage, a ten obiekt jest przekazywana do wystąpienia IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
`HttpMessage` Wystąpienia zawiera trzy elementy danych:

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

`HttpMessage` Wystąpienie zawiera `MessageId` identyfikator GUID, który pozwala na żądania HTTP łączyć się z odpowiedniego odpowiedzi HTTP i wartość logiczna, która określa, czy obiekt zawiera wystąpienie HttpRequestMessage i HttpResponseMessage. Za pomocą wbudowanych HTTP klas z `System.Net.Http`, I mógł korzystać z `application/http` analizy kodu, który znajduje się w `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
`HttpMessage` Wystąpienia jest następnie przekazywane do wykonania `IHttpMessageProcessor`, która jest interfejsem utworzono rozdzielenie odbieranie i interpretacji zdarzenia z Centrum zdarzeń platformy Azure i odpowiada za przetwarzanie.

## <a name="forwarding-the-http-message"></a>Przekazywanie komunikatów HTTP
Dla tego przykładu I uzgodnionych byłoby interesujące do dystrybuowania żądań HTTP za pośrednictwem do [Runscope](http://www.runscope.com). Runscope to usługa oparta na chmurze, która specjalizuje się w protokole HTTP, debugowanie, rejestrowania i monitorowania. Mają one warstwę bezpłatna, dzięki czemu ułatwia spróbuj i pozwala zobaczyć żądań HTTP w czasie rzeczywistym przepływających przez naszej usługi Zarządzanie interfejsami API.

`IHttpMessageProcessor` Implementacji wygląda tak,

```csharp
public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
{
   private HttpClient _HttpClient;
   private ILogger _Logger;
   private string _BucketKey;
   public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
   {
       _HttpClient = httpClient;
       var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
       _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
       _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
       _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
       _Logger = logger;
   }

   public async Task ProcessHttpMessage(HttpMessage message)
   {
       var runscopeMessage = new RunscopeMessage()
       {
           UniqueIdentifier = message.MessageId
       };

       if (message.IsRequest)
       {
           _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
           runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
       }
       else
       {
           _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
           runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
       }

       var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
       messagesLink.BucketKey = _BucketKey;
       messagesLink.RunscopeMessage = runscopeMessage;
       var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
       _Logger.LogDebug("Request sent to Runscope");
   }
}
```

I mógł korzystać z [istniejącej biblioteki klienta dla Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) ułatwia wypchnąć `HttpRequestMessage` i `HttpResponseMessage` wystąpienia się ich użytkowania. Aby uzyskać dostęp do interfejsu API Runscope, potrzebujesz konta i klucz interfejsu API. Instrukcje dotyczące pobierania klucza interfejsu API można znaleźć w [tworzenie aplikacji interfejsu API Runscope dostępu](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) screencast.

## <a name="complete-sample"></a>Kompletnego przykładu
[Kod źródłowy](https://github.com/darrelmiller/ApimEventProcessor) i testy przykładowej są w serwisie GitHub. Należy [usługi interfejsu API zarządzania](get-started-create-service-instance.md), [połączonego Centrum zdarzeń](api-management-howto-log-event-hubs.md), a [konta magazynu](../storage/common/storage-create-storage-account.md) do uruchomienia przykładu dla siebie.   

Próbka jest po prostu prostą aplikację konsoli, która nasłuchuje zdarzenia pochodzące z Centrum zdarzeń, konwertuje je do `HttpRequestMessage` i `HttpResponseMessage` obiekty i przekazuje je do interfejsu API Runscope.

Na poniższej ilustracji animowany widać żądanie do interfejsu API w portalu dla deweloperów, aplikacji konsoli przedstawiający komunikat jest odebrane, przetwarzane i przesyłane dalej i następnie żądania i odpowiedzi, które pojawiają się w inspektora Runscope ruchu.

![Pokaz żądanie przesyłane dalej do Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Podsumowanie
Usługi Zarządzanie interfejsami API Azure udostępnia doskonale nadaje się do przechwytywania ruchu HTTP do i z swoje interfejsy API w podróży. Usługa Azure Event Hubs to wysoce skalowalną, tanich rozwiązanie do przechwytywania tego ruchu i skierowanie go do systemów przetwarzania dodatkowej rejestrowania, monitorowania i inne zaawansowane analizy. Łączenie z systemów, takich jak Runscope jest tak proste, jak dozen zaledwie kilku wierszach kodu monitorowania ruchu innych firm.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej na temat usługi Azure Event Hubs
  * [Wprowadzenie do usługi Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Odbieranie komunikatów za pomocą klasy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Przewodnik programowania w usłudze Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Dowiedz się więcej na temat integracji usługi API Management i usługi Event Hubs
  * [Jak mają być rejestrowane zdarzenia do usługi Azure Event Hubs w usłudze Azure API Management](api-management-howto-log-event-hubs.md)
  * [Odwołanie do jednostki rejestratora](https://msdn.microsoft.com/library/azure/mt592020.aspx)
  * [informacje o zasadach dziennika do Centrum eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
