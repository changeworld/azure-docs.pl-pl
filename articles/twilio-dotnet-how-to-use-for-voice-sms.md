---
title: Jak używać usługi Twilio głosowe i programu SMS (.NET) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonać połączenie telefoniczne i wysłać wiadomość SMS z usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w programie .NET.
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
ms.openlocfilehash: 1442e3af26ae87e645cf207228ed1197b2afdd4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877027"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Jak używać usługi Twilio głosowe i możliwości programu SMS z platformy Azure
W tym przewodniku przedstawiono sposób wykonywania typowych zadań programowania usługi interfejsu API usługi Twilio na platformie Azure. Omówione scenariusze obejmują tworzenie połączeń telefonicznych i wysyłanie wiadomości krótką wiadomość usługi (SMS). Aby uzyskać więcej informacji na temat usługi Twilio i komunikacja głosowa programu SMS w aplikacji, zobacz [następne kroki](#NextSteps) sekcji.

## <a id="WhatIs"></a>Co to jest usługi Twilio?
Usługi Twilio jest włączanie przyszłości komunikacji biznesowej, umożliwiają deweloperom osadzać głosowych, VoIP i wiadomości do aplikacji. Zwirtualizuj one wszystkich infrastrukturę potrzebną w środowisku chmury, globalnych ujawnienie go za pośrednictwem platformy komunikacji interfejsu API usługi Twilio. Aplikacje są proste tworzenie i skalowalności. Kredyty elastyczność z płatność za rzeczywiste użycie ceny i korzystać z niej niezawodności chmury.

**Usługi Twilio głosu** umożliwia aplikacjom wykonywanie i odbieranie połączeń telefonicznych. **SMS usługi Twilio** umożliwia aplikacji wysyłanie i odbieranie wiadomości SMS. **Klient usługi Twilio** służy do nawiązywania połączeń VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje WebRTC.

## <a id="Pricing"></a>Cennik usługi Twilio i oferty specjalne
Azure klienci otrzymają [oferta specjalna](http://www.twilio.com/azure): bezpłatnych 10 środków usługi Twilio po uaktualnieniu konta usługi Twilio. Środki tej usługi Twilio można zastosować do bez użycia usługi Twilio (równoznaczne z wysyłaniem wiadomości SMS maksymalnie 1000 lub odbieranie maksymalnie 1000 przychodzących minut głosu w zależności od lokalizacji telefonu przeznaczenia numer i komunikatu lub wywołania kredyt $10). Realizowanie tego środki usługi Twilio i rozpocząć pracę w [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Usługi Twilio jest z usługą. Istnieją żadne opłaty konfiguracji, a konto zostanie zamknięte w dowolnym momencie. Można znaleźć więcej szczegółów na [cennik usługi Twilio](http://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio jest interfejs API RESTful, która zapewnia funkcje programu SMS i komunikacja głosowa aplikacji. Klient biblioteki są dostępne w wielu językach; Aby uzyskać listę, zobacz [bibliotek interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio są zleceń usługi Twilio i usługi Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Usługi Twilio zlecenia
Interfejs API korzysta z usługi Twilio zleceń; na przykład  **&lt;powiedzieć&gt;**  zlecenie powoduje, że usługi Twilio komputerowi dostarczenia komunikatu w wywołaniu.

Poniżej znajduje się lista zleceń usługi Twilio.  Dowiedz się więcej o innych poleceń i możliwości za pośrednictwem [dokumentacji usługi Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

* **&lt;Wybierania&gt;**: łączy wywołującego innego numeru telefonu.
* **&lt;Zbierz&gt;**: zbiera cyfry wprowadzone na klawiaturze telefonu.
* **&lt;Rozłączanie&gt;**: kończy wywołanie.
* **&lt;Odtwórz&gt;**: odtwarzania pliku audio.
* **&lt;Wstrzymaj&gt;**: dyskretnie czeka na określoną liczbę sekund.
* **&lt;Rekord&gt;**: rejestruje wywołującego głosu i zwraca adres URL pliku zawierającego nagrywania.
* **&lt;Przekieruj&gt;**: przekazuje kontrolę nad wywołania lub wiadomości SMS do TwiML na inny adres URL.
* **&lt;Odrzuć&gt;**: odrzucanie połączenia przychodzącego na numer usługi Twilio bez rozliczeń można
* **&lt;Powiedz&gt;**: Konwertuje tekst na mowę, które zostało utworzone w wywołaniu.
* **&lt;SMS&gt;**: wysyła wiadomość SMS.

### <a id="TwiML"></a>TwiML
TwiML to zestaw instrukcji oparte na języku XML, oparte na zlecenia usługi Twilio, które informują usługi Twilio sposobu przetwarzania połączenia lub wiadomości SMS.

Na przykład następujące TwiML czy Konwertuj tekst **Hello World** do rozpoznawania mowy.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Gdy aplikacja wywołuje interfejs API usługi Twilio, jeden z parametrów interfejsu API jest adres URL, który zwraca odpowiedź TwiML. Do celów programistycznych wprowadzone do usługi Twilio adresy URL służy do nadania odpowiedzi TwiML używane przez aplikacje. Może również udostępniać swoje własne adresy URL do tworzenia odpowiedzi TwiML i inną opcją jest użycie **TwiMLResponse** obiektu.

Aby uzyskać więcej informacji na temat usługi Twilio zlecenia i ich atrybuty oraz TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje na temat interfejsu API usługi Twilio, zobacz [interfejsu API usługi Twilio][twilio_api].

## <a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Po przygotowaniu można uzyskać konta usługi Twilio, zarejestruj się w [spróbuj usługi Twilio][try_twilio]. Można rozpoczynać bezpłatne konto, a następnie Uaktualnij swoje konto później.

Po utworzeniu konta dla konta usługi Twilio, otrzymasz Identyfikatora konta oraz tokenu uwierzytelniania. Zarówno będą potrzebne w celu wykonywania wywołań interfejsu API usługi Twilio. Aby uniemożliwić nieautoryzowany dostęp do Twojego konta, bezpieczeństwo Twojego tokenu uwierzytelniania. Twój identyfikator konta i uwierzytelniania tokenu jest wyświetlana w [strony konta usługi Twilio][twilio_account], w polach etykietą **identyfikator SID konta** i **TOKEN uwierzytelniania**odpowiednio.

## <a id="create_app"></a>Tworzenie aplikacji Azure
Azure aplikacji, która obsługuje aplikację usługi Twilio włączone nie różni się od innych aplikacji Azure. Dodawanie biblioteki .NET usługi Twilio i skonfigurować rolę przy użyciu bibliotek .NET usługi Twilio.
Informacje dotyczące tworzenia początkowej projektu platformy Azure, zobacz [Tworzenie projektu platformy Azure z programem Visual Studio][vs_project].

## <a id="configure_app"></a>Skonfiguruj Twojej aplikacji do korzystania z usługi Twilio biblioteki
Usługi Twilio zawiera zestaw .NET pomocnika bibliotek, które otaczają różnych aspektów usługi Twilio, aby zapewnić proste i łatwe sposoby interakcji z interfejsu API REST usługi Twilio i klienta usługi Twilio do generowania odpowiedzi TwiML.

Usługi Twilio zawiera pięć bibliotek dla deweloperów platformy .NET:
Biblioteka|Opis
---|---
Twilio.API|Podstawowa biblioteka usługi Twilio opakowuje interfejsu API REST usługi Twilio przyjazną biblioteki .NET. Ta biblioteka jest dostępna dla platformy .NET, Silverlight i Windows Phone 7.
Twilio.TwiML|Umożliwia .NET przyjazną Generowanie TwiML znaczników.
Twilio.MVC|Dla deweloperów przy użyciu platformy ASP.NET MVC Ta biblioteka zawiera TwilioController, TwiML ActionResult i atrybutu sprawdzania poprawności żądania.
Twilio.WebMatrix|Dla deweloperów korzystających bezpłatne narzędzie programistyczne WebMatrix firmy Microsoft Ta biblioteka zawiera pomocników składni Razor do wykonywania różnych akcji usługi Twilio.
Twilio.Client.Capability|Zawiera token generatora możliwości do użycia z zestawem SDK JavaScript klienta usługi Twilio.

Należy pamiętać, że wszystkie biblioteki wymaga .NET 3.5, Silverlight 4 lub Windows Phone 7 lub nowszy.

Przykłady podane w tym przewodniku korzystania z biblioteki Twilio.API.

Biblioteki może być [zainstalowane przy użyciu rozszerzenia Menedżera pakietów NuGet](http://www.twilio.com/docs/csharp/install) dostępne dla programu Visual Studio 2010 do 2015.  Kod źródłowy jest hostowana na [GitHub][twilio_github_repo], w tym typu Wiki, który zawiera pełną dokumentację dla przy użyciu bibliotek.

Domyślnie program Microsoft Visual Studio 2010 instaluje NuGet w wersji 1.2. Instalacja bibliotek usługi Twilio wymaga wersji 1.6 NuGet lub nowszej. Aby uzyskać informacje dotyczące instalowania lub aktualizowania NuGet, zobacz [http://nuget.org/][nuget].

> [!NOTE]
> Aby zainstalować najnowszą wersję programu NuGet, należy najpierw odinstalować załadowano wersję za pomocą Menedżera rozszerzenia programu Visual Studio. Aby to zrobić, należy uruchomić program Visual Studio jako administrator. W przeciwnym razie jest wyłączona przycisk Odinstaluj.
>
>

### <a id="use_nuget"></a>Aby dodać biblioteki usługi Twilio do projektu programu Visual Studio:
1. Otwórz rozwiązanie w programie Visual Studio.
2. Kliknij prawym przyciskiem myszy **odwołania**.
3. Kliknij przycisk **Zarządzaj pakietami NuGet...**
4. Kliknij przycisk **Online**.
5. W polu wyszukiwania online, wpisz *usługi twilio*.
6. Kliknij przycisk **zainstalować** w pakiecie usługi Twilio.

## <a id="howto_make_call"></a>Porady: wykonywanie połączenia wychodzące
Poniżej pokazano, jak wykonać wychodzące wywołanie przy użyciu **CallResource** klasy. Ten kod zawiera również lokacja dostarczonych do usługi Twilio do zwracania odpowiedzi usługi Twilio Markup Language (TwiML). Podstaw wartości **do** i **z** numerów telefonów i upewnij się, że sprawdzisz **z** numer telefonu dla konta usługi Twilio przed uruchomieniem kodu.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    var url = "http://twimlets.com/message";
    url = $"{url}?Message%5B0%5D=Hello%20World";

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri(url));
        }

Aby uzyskać więcej informacji na temat parametrów przekazanych do **CallResource.Create** metody, zobacz [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Jak wspomniano, ten kod używa lokacja dostarczonych do usługi Twilio do zwracania odpowiedzi TwiML. Zamiast tego można użyć własnej witryny można podać odpowiedzi TwiML. Aby uzyskać więcej informacji, zobacz [porady: Podaj TwiML odpowiedzi z własnej witryny sieci Web](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Porady: Wyślij wiadomość SMS
Poniższy zrzut ekranu przedstawia sposób wysłania wiadomości SMS za pomocą **MessageResource** klasy. **z** numer jest udostępniany przez usługi Twilio dla konta wersji próbnej można wysłać wiadomości SMS. **Do** numer muszą być weryfikowane dla konta usługi Twilio, przed uruchomieniem kodu.

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

## <a id="howto_provide_twiml_responses"></a>Porady: Podaj TwiML odpowiedzi z własnej witryny sieci Web
Gdy aplikacja inicjuje wywołanie interfejsu API usługi Twilio — na przykład za pomocą **CallResource.Create** metody — usługi Twilio wysyła żądanie do adresu URL, który powinien zwrócić odpowiedzi TwiML. Przykład [porady: wykonania wywołania wychodzącego](#howto_make_call) korzysta z adresu URL dostarczony do usługi Twilio [http://twimlets.com/message] [ twimlet_message_url] do zwracania odpowiedzi.

> [!NOTE]
> Gdy TwiML jest przeznaczony do użytku przez usługi sieci web, można wyświetlić TwiML w przeglądarce. Na przykład kliknij pozycję [http://twimlets.com/message] [ twimlet_message_url] wyświetlić pustą &lt;odpowiedzi&gt; element; inny przykład kliknij [http://twimlets.com/message?Message%5B0%5D=Hello%20World](http://twimlets.com/message?Message%5B0%5D=Hello%20World) wyświetlić &lt;odpowiedzi&gt; element, który zawiera &lt;powiedzieć&gt; elementu.
>
>

Zamiast polegania na adres URL dostarczony do usługi Twilio, można utworzyć własny adres URL w witrynie odpowiedzi HTTP. Można utworzyć witryny w dowolnym języku, który zwraca odpowiedzi HTTP. W tym temacie założono, że będzie obsługiwać adresu URL z obsługi ogólnej ASP.NET.

Następujące obsługi ASP.NET crafts odpowiedzi TwiML informacją **Hello World** przy wywołaniu.

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
    
Jak widać w powyższym przykładzie, odpowiedź TwiML jest po prostu dokumentu XML. Biblioteka Twilio.TwiML zawiera klasy, które generuje TwiML dla Ciebie. Poniższy przykład tworzy równoważne odpowiedzi, jak pokazano powyżej, ale używa **VoiceResponse** klasy.

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

Aby uzyskać więcej informacji o TwiML, zobacz [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Po skonfigurowaniu sposobem zapewnienia odpowiedzi TwiML można przekazać tego adresu URL do **CallResource.Create** metody. Na przykład, jeśli masz aplikację sieci web o nazwie MyTwiML wdrożona usługa w chmurze Azure, a nazwa programu obsługi ASP.NET jest mytwiml.ashx, adres URL można przekazać do **CallResource.Create** jak pokazano w poniższym przykładzie kodu:

    // This sample uses the sandbox number provided by Twilio to make the call.
    // Place the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
        }

Aby uzyskać dodatkowe informacje dotyczące korzystania z usługi Twilio na platformie Azure za pomocą programu ASP.NET, zobacz [jak połączenie telefoniczne z rolą sieci web na platformie Azure przy użyciu usługi Twilio][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
