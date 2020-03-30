---
title: Jak używać usługi Twilio dla poczty głosowej i wiadomości SMS (.NET) | Dokumenty firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w .NET.
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.openlocfilehash: 22b33d7b4b0ff69a2e751cadff70453f73ed4f8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69876821"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Jak korzystać z usługi Twilio dla funkcji głosowych i SMS z platformy Azure
W tym przewodniku pokazano, jak wykonywać typowe zadania programowania za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Scenariusze obejmują nawiązanie połączenia telefonicznego i wysyłanie wiadomości SMS. Aby uzyskać więcej informacji na temat usługi Twilio i używania głosu i wiadomości SMS w aplikacjach, zobacz sekcję [Następne kroki.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co to jest Twilio?
Usługa Twilio napędza przyszłość komunikacji biznesowej, umożliwiając deweloperom osadzanie głosu, voip i wiadomości w aplikacjach. Wirtualizują całą infrastrukturę potrzebną w środowisku globalnym opartym na chmurze, udostępniając ją za pośrednictwem platformy interfejsu API komunikacji usługi Twilio. Aplikacje są proste w kompilacji i skalowalne. Ciesz się elastycznością dzięki cenom płatności zgodnie z rzeczywistym użyciem i korzystaj z niezawodności chmury.

**Aplikacja Twilio Voice** umożliwia aplikacjom nawiązywać i odbierać połączenia telefoniczne. **Usługa Twilio SMS** umożliwia aplikacjom wysyłanie i odbieranie wiadomości SMS. **Klient Twilio** umożliwia wykonywanie połączeń VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny twilio i oferty specjalne
Klienci platformy Azure otrzymują [specjalną ofertę:](https://www.twilio.com/azure)bezpłatną 10 USD kredytu usługi Twilio po uaktualnieniu konta usługi Twilio. Ten kredyt usługi Twilio można zastosować do dowolnego użycia usługi Twilio (10 usd kredytu równego wysyłaniu aż 1000 wiadomości SMS lub odbierania do 1000 przychodzących minut głosowych, w zależności od lokalizacji numeru telefonu i wiadomości lub miejsca docelowego połączenia). Zrealizuj ten kredyt usługi Twilio i zacznij od [twilio.com/azure](https://twilio.com/azure).

Twilio to usługa płatności zgodnie z rzeczywistym i wyjazdem. Nie ma żadnych opłat za konfigurację i możesz zamknąć konto w dowolnym momencie. Więcej informacji można znaleźć w [cenniku Twilio](https://www.twilio.com/voice/pricing).

## <a name="concepts"></a><a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio jest interfejsem API RESTful, który zapewnia funkcje głosowe i sms dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; aby uzyskać listę, zobacz [Biblioteki interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio to czasowniki usługi Twilio i język znaczników języka Twilio (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Czasowniki Twilio
Interfejs API używa czasowników usługi Twilio; na przykład ** &lt;say&gt; ** zlecenie instruuje Twilio do słyszalnego dostarczania wiadomości na wywołanie.

Poniżej znajduje się lista czasowników Twilio.  Dowiedz się więcej o innych zleceniach i możliwościach za pomocą [dokumentacji języka znaczników usługi Twilio Markup .](https://www.twilio.com/docs/api/twiml)

* `<Dial>`: Łączy rozmówcę z innym telefonem.
* `<Gather>`: Zbiera cyfry wprowadzone na klawiaturze telefonu.
* `<Hangup>`: Kończy połączenie.
* `<Play>`: Odtwarza plik audio.
* `<Pause>`: Czeka cicho przez określoną liczbę sekund.
* `<Record>`: Rejestruje głos rozmówcy i zwraca adres URL pliku zawierającego nagranie.
* `<Redirect>`: Przenosi kontrolę nad połączeniem lub SMS-em do TwiML pod innym adresem URL.
* `<Reject>`: Odrzuca połączenie przychodzące na twój numer usługi Twilio bez
* `<Say>`: Konwertuje tekst na mowę nakonywalną podczas połączenia.
* `<Sms>`: Wysyła wiadomość SMS.

### <a name="twiml"></a>TwiML (twiml)
TwiML to zestaw instrukcji opartych na języku XML opartych na czasownikach usługi Twilio, które informują program Twilio o sposobie przetwarzania połączenia lub wiadomości SMS.

Na przykład następujący TwiML konwertuje tekst **Hello World** na mowę.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Gdy aplikacja wywołuje interfejs API usługi Twilio, jednym z parametrów interfejsu API jest adres URL, który zwraca odpowiedź TwiML. Do celów programisty można użyć adresów URL dostarczonych przez program Twilio, aby zapewnić odpowiedzi TwiML używane przez aplikacje. Można również hostować własne adresy URL do tworzenia odpowiedzi TwiML, a inną opcją jest użycie obiektu **TwiMLResponse.**

Aby uzyskać więcej informacji na temat czasowników usługi Twilio, ich atrybutów i TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje dotyczące interfejsu API usługi Twilio, zobacz [interfejs API usługi Twilio][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Gdy będziesz gotowy do utworzenia konta usługi Twilio, zarejestruj się w [try Twilio][try_twilio]. Możesz zacząć od bezpłatnego konta, a później uaktualnić swoje konto.

Gdy zarejestrujesz konto usługi Twilio, otrzymasz identyfikator konta i token uwierzytelniania. Oba będą potrzebne do wywołania interfejsu API usługi Twilio. Aby zapobiec nieautoryzowanemu dostępowi do konta, należy zabezpieczyć token uwierzytelniania. Identyfikator konta i token uwierzytelniania są widoczne na [stronie konta usługi Twilio][twilio_account], odpowiednio w polach oznaczonych **identyfikatorem SID konta** i **tokenem AUTH.**

## <a name="create-an-azure-application"></a><a id="create_app"></a>Tworzenie aplikacji platformy Azure
Aplikacja platformy Azure, która obsługuje aplikację obsługującą usługę Twilio, nie różni się od żadnej innej aplikacji platformy Azure. Dodaj bibliotekę Twilio .NET i konfigurujesz rolę do używania bibliotek programu Twilio .NET.
Aby uzyskać informacje na temat tworzenia początkowego projektu platformy Azure, zobacz [Tworzenie projektu platformy Azure za pomocą programu Visual Studio.][vs_project]

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurowanie aplikacji do używania bibliotek usługi Twilio
Usługa Twilio udostępnia zestaw bibliotek pomocniczych platformy .NET, które zawierają różne aspekty usługi Twilio, aby zapewnić proste i łatwe sposoby interakcji z interfejsem API REST usługi Twilio i klientem Twilio w celu generowania odpowiedzi TwiML.

Program Twilio udostępnia pięć bibliotek dla deweloperów platformy .NET:

| Biblioteka | Opis |
| --- | --- |
| Interfejs Twilio.API | Podstawowa biblioteka usługi Twilio, która otacza interfejs API REST usługi Twilio w przyjaznej bibliotece .NET. Ta biblioteka jest dostępna dla programów .NET, Silverlight i Windows Phone 7. |
| Plik Twilio.TwiML | Zapewnia przyjazny sposób generowania znaczników TwiML.Provides a .NET friendly way to generate TwiML markup. |
| Twilio.MVC | Dla deweloperów korzystających ASP.NET MVC ta biblioteka zawiera TwilioController, TwiML ActionResult i atrybut sprawdzania poprawności żądania. |
| Aplikacja Twilio.WebMatrix | Dla programistów korzystających z bezpłatnego narzędzia programistycznego WebMatrix firmy Microsoft ta biblioteka zawiera pomocników składni Razor dla różnych akcji Twilio. |
| Funkcja Twilio.Client.Capability | Zawiera generator tokenów możliwości do użytku z SDK JavaScript klienta usługi Twilio. |

> [!Important]
> Wszystkie biblioteki wymagają platformy .NET 3.5, Silverlight 4 lub Windows Phone 7 lub nowszej.

Przykłady zawarte w tym przewodniku należy użyć biblioteki Twilio.API.

Biblioteki można [zainstalować przy użyciu rozszerzenia Menedżera pakietów NuGet](https://www.twilio.com/docs/csharp/install) dostępne dla programu Visual Studio 2010 do 2015.  Kod źródłowy jest hostowany na [GitHub][twilio_github_repo], który zawiera Wiki, która zawiera pełną dokumentację do korzystania z bibliotek.

Domyślnie program Microsoft Visual Studio 2010 instaluje wersję 1.2 nuget. Instalowanie bibliotek usługi Twilio wymaga wersji 1.6 nuget lub wyższej. Aby uzyskać informacje na temat instalowania lub aktualizowania programu NuGet, zobacz [https://nuget.org/][nuget].

> [!NOTE]
> Aby zainstalować najnowszą wersję nuget, należy najpierw odinstalować załadowaną wersję przy użyciu Menedżera rozszerzeń programu Visual Studio. Aby to zrobić, należy uruchomić program Visual Studio jako administrator. W przeciwnym razie przycisk Odinstaluj jest wyłączony.
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>Aby dodać biblioteki usługi Twilio do projektu programu Visual Studio:
1. Otwórz rozwiązanie w programie Visual Studio.
2. Kliknij prawym przyciskiem myszy **pozycję Odwołania**.
3. Kliknij **pozycję Zarządzaj pakietami NuGet...**
4. Kliknij pozycję **Online**.
5. W polu wyszukiwania online wpisz *twilio*.
6. Kliknij **pozycję Zainstaluj** pakiet usługi Twilio.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Jak: Nawiązać połączenie wychodzące
Poniżej pokazano, jak nawiązać połączenie wychodzące przy użyciu **CallResource** klasy. Ten kod używa również witryny dostarczonej przez program Twilio do zwrócenia odpowiedzi na język znaczników (TwiML) usługi Twilio. Zastąp wartości dla numerów telefonów **do** i z i z i **z** i upewnij się, że przed uruchomieniem kodu należy zweryfikować numer telefonu **z** konta usługi Twilio.

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

Aby uzyskać więcej informacji na temat parametrów przekazanych [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]do **CallResource.Create** metody, zobacz .

Jak wspomniano, ten kod używa witryny dostarczonej przez twilio do zwrócenia odpowiedzi TwiML. Zamiast tego można użyć własnej witryny, aby zapewnić odpowiedź TwiML. Aby uzyskać więcej informacji, zobacz [Jak: Udzielanie odpowiedzi TwiML z własnej witryny sieci Web](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Jak: Wyślij wiadomość SMS
Poniższy zrzut ekranu pokazuje, jak wysłać wiadomość SMS przy użyciu **MessageResource** klasy. Numer **od** jest dostarczany przez program Twilio dla kont próbnych do wysyłania wiadomości SMS. Numer **do** musi zostać zweryfikowany dla konta usługi Twilio przed uruchomieniem kodu.

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Jak: Dostarczaj odpowiedzi TwiML z własnej strony internetowej
Gdy aplikacja inicjuje wywołanie interfejsu API usługi Twilio — na przykład za pośrednictwem **CallResource.Create** metody — Twilio wysyła żądanie do adresu URL, który ma zwrócić odpowiedź TwiML. Przykład w [Jak: Make połączenia wychodzącego](#howto_make_call) używa adresu URL [https://twimlets.com/message][twimlet_message_url] podanej w uodwie, aby zwrócić odpowiedź.

> [!NOTE]
> Podczas TwiML jest przeznaczony do użytku przez usługi internetowe, można wyświetlić TwiML w przeglądarce. Na przykład [https://twimlets.com/message][twimlet_message_url] kliknij, aby `<Response>` wyświetlić pusty element; jako inny [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) przykład, kliknij, aby wyświetlić `<Response>` element, który zawiera &lt;Say&gt; element.
>

Zamiast polegać na adresie URL dostarczonym przez program Twilio, można utworzyć własną witrynę adresu URL, która zwraca odpowiedzi HTTP. Witrynę można utworzyć w dowolnym języku, w który zwraca odpowiedzi HTTP. W tym temacie przyjęto założenie, że adres URL będzie hostować z ASP.NET programu obsługi ogólnej.

Następujące ASP.NET Handler rzemiosła odpowiedzi TwiML, który mówi **Hello World** na wezwanie.

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
    
Jak widać na powyższym przykładzie odpowiedź TwiML jest po prostu dokumentem XML. Biblioteka Twilio.TwiML zawiera klasy, które będą generować TwiML dla Ciebie. Poniższy przykład daje równoważną odpowiedź, jak pokazano powyżej, ale używa **VoiceResponse** klasy.

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

Aby uzyskać więcej informacji na [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml)temat TwiML, zobacz .

Po skonfigurowaniu sposobu dostarczania odpowiedzi TwiML, można przekazać ten adres URL do **CallResource.Create** metody. Na przykład jeśli masz aplikację sieci web o nazwie MyTwiML wdrożony w usłudze w chmurze platformy Azure, a nazwa programu obsługi ASP.NET jest mytwiml.ashx, adres URL może być przekazany do **CallResource.Create,** jak pokazano w poniższym przykładzie kodu:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Aby uzyskać dodatkowe informacje dotyczące korzystania z usługi Twilio na platformie Azure z ASP.NET, zobacz [Jak nawiązać połączenie telefoniczne przy użyciu usługi Twilio w roli sieci Web na platformie Azure.][howto_phonecall_dotnet]

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
