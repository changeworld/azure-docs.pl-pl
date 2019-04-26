---
title: Jak używać usługi Twilio dla połączeń głosowych i wiadomości SMS (.NET) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązywanie połączeń telefonicznych i wysyłanie wiadomości SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku .NET.
services: ''
documentationcenter: .net
author: devinrader
manager: twilio
editor: ''
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 3b8b21de9664a969e8b1ce5699034aa9ab41d0f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60329505"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Jak za pomocą usługi Twilio dla połączeń głosowych i SMS możliwości platformy Azure
Ten przewodnik pokazuje sposób wykonywania typowych zadań programistycznych w usłudze interfejsu API usługi Twilio, na platformie Azure. Omówione scenariusze obejmują tworzenie połączenia telefonicznego i wysyłanie wiadomości wiadomości usługi (SMS). Aby uzyskać więcej informacji na temat usługi Twilio i używania połączeń głosowych i wiadomości SMS w swoich aplikacjach, zobacz [następne kroki](#NextSteps) sekcji.

## <a id="WhatIs"></a>Co to jest Twilio?
Twilio to najważniejsza przyszłość komunikacji biznesowej, dzięki czemu deweloperzy mogą osadzać głosu, VoIP i komunikatów w aplikacjach. Wirtualizacja one wszystkie elementy infrastruktury potrzebne w środowisku oparte na chmurze, globalnej uwidaczniania go przy użyciu platformy łączności interfejsu API usługi Twilio. Aplikacje są proste do tworzenia i skalowalne. Korzystaj z elastycznej możliwości zgodnie z rzeczywistym użyciem, ceny i korzystać z niezawodności chmury.

**Twilio dla połączeń głosowych** umożliwia aplikacjom wykonywanie i odbieranie połączeń telefonicznych. **Wiadomości SMS usługi Twilio** umożliwia aplikacji wysyłanie i odbieranie wiadomości SMS. **Klient usługi Twilio** umożliwia nawiązywanie połączeń VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje protokołu WebRTC.

## <a id="Pricing"></a>Cennik usługi Twilio i ofert specjalnych
Klienci platformy Azure otrzymują [oferta specjalna](https://www.twilio.com/azure): bezpłatnych w wysokości 10 USD kredytu usługi Twilio, po uaktualnieniu konta usługi Twilio. Wartość tego kredytu Twilio mogą dotyczyć każde użycie usługi Twilio (równoważne do wysyłania wiadomości SMS maksymalnie 1000 lub odbieranie maksymalnie 1000 dla ruchu przychodzącego minut głosu w zależności od lokalizacji docelowej liczby i wiadomości lub wywołania telefonu środków w wysokości 10 USD). Zrealizuj wartość tego kredytu usługi Twilio i zacznij pracę w [twilio.com/azure](https://twilio.com/azure).

Twilio to usługa, zgodnie z rzeczywistym użyciem. Nie są pobierane żadne opłaty konfiguracji i w dowolnym momencie można zamknąć konto. Można znaleźć więcej szczegółów na [cennik usługi Twilio](https://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio to API typu RESTful, który zapewnia połączeń głosowych i SMS funkcje dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; Aby uzyskać listę, zobacz [bibliotek interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio to zleceń usługi Twilio i Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio zlecenia
Interfejs API korzysta z usługi Twilio zleceń; na przykład **&lt;Say&gt;** Twilio komputerowi dostarczenia komunikatu w wywołaniu powoduje, że zlecenie.

Oto lista poleceń usługi Twilio.  Dowiedz się więcej o innych poleceń i możliwości za pośrednictwem [dokumentacji usługi Twilio Markup Language](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Obiekt wywołujący nawiązanie połączenia innego numeru telefonu.
* `<Gather>`: Gromadzi informacje o cyfry wprowadzone na klawiaturze telefonu.
* `<Hangup>`: Kończy wywołanie.
* `<Play>`: Odtwarza plik audio.
* `<Pause>`: Dyskretnie czeka określoną liczbę sekund.
* `<Record>`: Rejestruje głosu wywołującego i zwraca adres URL pliku zawierającego nagrywania.
* `<Redirect>`: Transfer kontroli połączenia lub wiadomości SMS do TwiML na inny adres URL.
* `<Reject>`: Odrzuca połączenie na numer Twilio bez możesz rozliczeń
* `<Say>`: Konwertuje tekst na mowę, który składa się na wywołanie.
* `<Sms>`: Wysyła wiadomość SMS.

### <a name="twiml"></a>TwiML
TwiML to zbiór opartych na języku XML instrukcje zleceń Twilio, które informują Twilio sposób przetwarzania wywołania lub wysyłać wiadomości SMS.

Na przykład następujące TwiML czy Konwertowanie tekstu **Witaj, świecie** do rozpoznawania mowy.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Gdy Twoja aplikacja wywołuje interfejs API usługi Twilio, jeden z parametrów interfejs API jest adres URL, który zwraca odpowiedź TwiML. Do celów programistycznych dostarczone do usługi Twilio adresy URL służy do zapewniania odpowiedzi TwiML używane przez aplikacje. Może także udostępniać własne adresy URL służące do tworzenia odpowiedzi TwiML i innym rozwiązaniem jest użycie **TwiMLResponse** obiektu.

Aby uzyskać więcej informacji na temat Twilio zleceń, ich atrybuty i TwiML zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje na temat interfejsu API usługi Twilio, zobacz [interfejsu API usługi Twilio][twilio_api].

## <a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Gdy wszystko będzie gotowe uzyskać konta usługi Twilio, zarejestruj się pod adresem [spróbuj Twilio][try_twilio]. Można uruchomić przy użyciu bezpłatnego konta i później uaktualnić Twoje konto.

Po zarejestrowaniu się do konta usługi Twilio, otrzymasz identyfikator konta i tokenu uwierzytelniania. Oba będą potrzebne do wykonywania wywołań interfejsu API usługi Twilio. Aby uniemożliwić nieautoryzowany dostęp do Twojego konta, bezpieczeństwo Twojego tokenu uwierzytelniania. Swojego Identyfikatora konta i uwierzytelniania tokenu są wyświetlane w [strona konta usługi Twilio][twilio_account], w polach etykietą **identyfikator SID konta** i **TOKEN uwierzytelniania**, odpowiednio.

## <a id="create_app"></a>Tworzenie aplikacji platformy Azure
Aplikacją platformy Azure, która hostuje aplikację usługi Twilio, włączone nie różni się od innych aplikacji platformy Azure. Dodaj bibliotekę .NET usługi Twilio i skonfigurować rolę przy użyciu bibliotek platformy .NET usługi Twilio.
Aby uzyskać informacje na temat tworzenia początkowej projekt platformy Azure, zobacz [Tworzenie projektu platformy Azure za pomocą programu Visual Studio][vs_project].

## <a id="configure_app"></a>Konfigurowanie aplikacji przy użyciu bibliotek usługi Twilio
Twilio zawiera zbiór bibliotek .NET pomocnika, które umieszczają w otoce różnych aspektów usługi Twilio, aby zapewnić proste i łatwe sposoby interakcji z interfejsu API REST usługi Twilio i klienta usługi Twilio do generowania odpowiedzi TwiML.

Twilio zawiera pięć bibliotek dla deweloperów platformy .NET:

| Biblioteka | Opis |
| --- | --- |
| Twilio.API | Podstawowa biblioteka Twilio przyjazna biblioteki .NET otacza interfejs API REST usługi Twilio. Ta biblioteka jest dostępna dla platformy .NET, Silverlight i Windows Phone 7. |
| Twilio.TwiML | Umożliwia .NET przyjazna generują znaczniki TwiML. |
| Twilio.MVC | Dla deweloperów korzystających z platformy ASP.NET MVC Ta biblioteka zawiera TwilioController, TwiML ActionResult i atrybut weryfikacji żądania. |
| Twilio.WebMatrix | Dla deweloperów przy użyciu bezpłatnego narzędzia projektowania programu WebMatrix firmy Microsoft Ta biblioteka zawiera pomocnicy składni Razor do wykonywania różnych akcji usługi Twilio. |
| Twilio.Client.Capability | Zawiera generator tokenów funkcja do użycia z zestawem SDK JavaScript dla klienta usługi Twilio. |

> [!Important]
> Wszystkie biblioteki wymagają .NET 3.5, Silverlight 4 lub Windows Phone 7 lub nowszy.

Przykłady podane w tym przewodniku korzystanie z biblioteki Twilio.API.

Biblioteki mogą być [zainstalowane przy użyciu rozszerzenia Menedżera pakietów NuGet](https://www.twilio.com/docs/csharp/install) dostępne dla programu Visual Studio 2010 do 2015.  Kod źródłowy znajduje się na [GitHub][twilio_github_repo], która obejmuje witrynę typu Wiki, która zawiera wyczerpujące informacje dotyczące korzystania z biblioteki.

Domyślnie program Microsoft Visual Studio 2010 instaluje NuGet w wersji 1.2. Instalowanie bibliotek usługi Twilio wymaga wersji 1.6 NuGet lub nowszej. Aby uzyskać informacji na temat instalowania lub aktualizowania NuGet, zobacz [ https://nuget.org/ ] [ nuget].

> [!NOTE]
> Aby zainstalować najnowszą wersję pakietu NuGet, należy najpierw odinstalować załadowano wersję przy użyciu Menedżera rozszerzeń programu Visual Studio. Aby to zrobić, należy uruchomić program Visual Studio jako administrator. W przeciwnym razie przycisk Odinstaluj jest wyłączony.
>
>

### <a id="use_nuget"></a>Aby dodać biblioteki usługi Twilio do projektu programu Visual Studio:
1. Otwórz swoje rozwiązanie w programie Visual Studio.
2. Kliknij prawym przyciskiem myszy **odwołania**.
3. Kliknij przycisk **Zarządzaj pakietami NuGet...**
4. Kliknij przycisk **Online**.
5. W polu wyszukiwania online, wpisz *twilio*.
6. Kliknij przycisk **zainstalować** w pakiecie usługi Twilio.

## <a id="howto_make_call"></a>Jak: Wykonywanie wywołania interfejsu wychodzącego
Poniżej pokazano sposób wprowadzania wychodzące wywołanie przy użyciu **CallResource** klasy. Ten kod używa również lokacji dostarczone do usługi Twilio do zwracania odpowiedzi Twilio Markup Language (TwiML). Podstaw wartości **do** i **z** numerów telefonów i upewnij się, aby zweryfikować **z** numer telefonu dla konta usługi Twilio przed uruchomieniem kodu.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

Aby uzyskać więcej informacji na temat parametrów przekazanych do **CallResource.Create** metody, zobacz [ https://www.twilio.com/docs/api/rest/making-calls ] [ twilio_rest_making_calls].

Jak wspomniano wcześniej, ten kod używa witryny dostarczone do usługi Twilio do zwracania odpowiedzi TwiML. Zamiast tego można użyć do własnej witryny można podać odpowiedzi TwiML. Aby uzyskać więcej informacji, zobacz [Instrukcje: Zapewnienie TwiML odpowiedzi z własną witrynę sieci Web](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Jak: Wyślij wiadomość SMS
Poniższy zrzut ekranu przedstawia sposób wysłania wiadomości SMS za pomocą **MessageResource** klasy. **z** zostanie podany numer, Twilio dla konta wersji próbnej do wysyłania wiadomości SMS. **Do** numer musi być zweryfikowany dla konta usługi Twilio, przed uruchomieniem kodu.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

try
{
    // Send an SMS message.
    var message = MessageResource.Create(
        to: new PhoneNumber("+12069419717"),
        from: new PhoneNumber("+14155992671"),
        body: "This is my SMS message.");
}
catch (TwilioException ex)
{
    // An exception occurred making the REST call
    Console.WriteLine(ex.Message);
}
```

## <a id="howto_provide_twiml_responses"></a>Jak: Zapewnienie TwiML odpowiedzi z własną witrynę sieci Web
Gdy aplikacja inicjuje wywołanie interfejsu API usługi Twilio — na przykład za pośrednictwem **CallResource.Create** metoda - Twilio wysyła żądanie do adresu URL, który powinien zwrócić odpowiedź TwiML. W przykładzie w [jak: Wykonywanie wywołania interfejsu wychodzącego](#howto_make_call) używa adresu URL dostarczone do usługi Twilio [ https://twimlets.com/message ] [ twimlet_message_url] do zwracania odpowiedzi.

> [!NOTE]
> Gdy TwiML jest przeznaczony do użytku przez usługi sieci web, możesz wyświetlić TwiML w przeglądarce. Na przykład kliknij pozycję [ https://twimlets.com/message ] [ twimlet_message_url] będzie pusta `<Response>` element; inny przykład kliknij [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ](https://twimlets.com/message?Message%5B0%5D=Hello%20World) się `<Response>` element, który zawiera &lt;Say&gt; elementu.
>

Zamiast polegania na adres URL podany do usługi Twilio, można utworzyć własny adres URL w witrynie odpowiedzi HTTP. W dowolnym języku, który zwraca odpowiedzi HTTP, można utworzyć witryny. W tym temacie założono, że będziesz hostingu adres URL z ogólna procedura obsługi ASP.NET.

Następujące procedura obsługi ASP.NET crafts informujący, że odpowiedź TwiML **Witaj, świecie** przy wywołaniu.

```csharp
using System.Text;
using System.Web;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {
        public void ProcessRequest(HttpContext context)
        {
            const string twiMLResponse =
                "<Response><Say>Hello World.</Say></Response>";

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.ContentEncoding = Encoding.UTF8;
            context.Response.Write(twiMLResponse);
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```
    
Jak widać w powyższym przykładzie odpowiedzi TwiML jest po prostu dokumentu XML. Biblioteka Twilio.TwiML zawiera klasy, które będą generowały TwiML dla Ciebie. Poniższy przykład tworzy równoważne odpowiedzi, jak pokazano powyżej, ale używa **VoiceResponse** klasy.

```csharp
using System.Web;
using Twilio.TwiML;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {

        public void ProcessRequest(HttpContext context)
        {
            var twiml = new VoiceResponse();
            twiml.Say("Hello World.");

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.Write(twiml.ToString());
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

Aby uzyskać więcej informacji na temat TwiML zobacz [ https://www.twilio.com/docs/api/twiml ](https://www.twilio.com/docs/api/twiml).

Po skonfigurowaniu sposób zapewnienia TwiML odpowiedzi można przekazać tego adresu URL do **CallResource.Create** metody. Na przykład, jeśli masz aplikację sieci web o nazwie MyTwiML wdrożonych w usłudze w chmurze platformy Azure, a nazwa programu obsługi platformy ASP.NET to mytwiml.ashx, adres URL może być przekazywany do **CallResource.Create** jak pokazano w następującym przykładzie kodu:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Aby uzyskać dodatkowe informacje o korzystaniu z usługi Twilio na platformie Azure za pomocą platformy ASP.NET, zobacz [jak nawiązać połączenie telefoniczne w roli sieci web na platformie Azure za pomocą usługi Twilio][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
