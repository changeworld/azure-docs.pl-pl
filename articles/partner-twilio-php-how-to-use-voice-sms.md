---
title: Jak używać usługi Twilio dla połączeń głosowych i wiadomości SMS (PHP) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązywanie połączeń telefonicznych i wysyłanie wiadomości SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku PHP.
documentationcenter: php
services: ''
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: a51cd589702320ecb55e6a2e3c5f0a6139e281fe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60422359"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Jak używać usługi Twilio dla połączeń głosowych i SMS funkcji w języku PHP
Ten przewodnik pokazuje sposób wykonywania typowych zadań programistycznych w usłudze interfejsu API usługi Twilio, na platformie Azure. Omówione scenariusze obejmują tworzenie połączenia telefonicznego i wysyłanie wiadomości wiadomości usługi (SMS). Aby uzyskać więcej informacji na temat usługi Twilio i używania połączeń głosowych i wiadomości SMS w swoich aplikacjach, zobacz [następne kroki](#NextSteps) sekcji.

## <a id="WhatIs"></a>Co to jest Twilio?
Twilio to najważniejsza przyszłość komunikacji biznesowej, dzięki czemu deweloperzy mogą osadzać głosu, VoIP i komunikatów w aplikacjach. Wirtualizacja one wszystkie elementy infrastruktury potrzebne w środowisku oparte na chmurze, globalnej uwidaczniania go przy użyciu platformy łączności interfejsu API usługi Twilio. Aplikacje są proste do tworzenia i skalowalne. Elastyczność dzięki płatności — jako — można przejść, ceny i korzystać z niezawodności chmury.

**Twilio dla połączeń głosowych** umożliwia aplikacjom wykonywanie i odbieranie połączeń telefonicznych. **Wiadomości SMS usługi Twilio** umożliwia aplikacji wysyłanie i odbieranie wiadomości SMS. **Klient usługi Twilio** umożliwia nawiązywanie połączeń VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje protokołu WebRTC.

## <a id="Pricing"></a>Cennik usługi Twilio i ofert specjalnych
Klienci platformy Azure otrzymują [oferta specjalna](https://www.twilio.com/azure): bezpłatnych w wysokości 10 USD kredytu usługi Twilio, po uaktualnieniu konta usługi Twilio. Wartość tego kredytu Twilio mogą dotyczyć każde użycie usługi Twilio (równoważne do wysyłania wiadomości SMS maksymalnie 1000 lub odbieranie maksymalnie 1000 dla ruchu przychodzącego minut głosu w zależności od lokalizacji docelowej liczby i wiadomości lub wywołania telefonu środków w wysokości 10 USD). Zrealizuj wartość tego kredytu usługi Twilio i zacznij pracę w: [ https://ahoy.twilio.com/azure ](https://ahoy.twilio.com/azure).

Twilio to usługa, zgodnie z rzeczywistym użyciem. Nie są pobierane żadne opłaty konfiguracji i w dowolnym momencie można zamknąć konto. Można znaleźć więcej szczegółów na [cennik usługi Twilio][twilio_pricing].

## <a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio to API typu RESTful, który zapewnia połączeń głosowych i SMS funkcje dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; Aby uzyskać listę, zobacz [bibliotek interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio to zleceń usługi Twilio i Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio zlecenia
Interfejs API korzysta z usługi Twilio zleceń; na przykład **&lt;Say&gt;** Twilio komputerowi dostarczenia komunikatu w wywołaniu powoduje, że zlecenie.

Oto lista poleceń usługi Twilio. Dowiedz się więcej o innych poleceń i możliwości za pośrednictwem [dokumentacji usługi Twilio Markup Language](https://www.twilio.com/docs/api/twiml).

* **&lt;Wybierania&gt;** : Obiekt wywołujący nawiązanie połączenia innego numeru telefonu.
* **&lt;Gather&gt;** : Gromadzi informacje o cyfry wprowadzone na klawiaturze telefonu.
* **&lt;Rozłączanie&gt;** : Kończy wywołanie.
* **&lt;Play&gt;** : Odtwarza plik audio.
* **&lt;Pause&gt;** : Dyskretnie czeka określoną liczbę sekund.
* **&lt;Rekord&gt;** : Rejestruje głosu wywołującego i zwraca adres URL pliku, który zawiera nagrywania.
* **&lt;Redirect&gt;** : Transfer kontroli połączenia lub wiadomości SMS do TwiML na inny adres URL.
* **&lt;Reject&gt;** : Odrzuca połączenie na numer Twilio bez możesz rozliczeń
* **&lt;Powiedz&gt;** : Konwertuje tekst na mowę, który składa się na wywołanie.
* **&lt;Sms&gt;** : Wysyła wiadomość SMS.

### <a id="TwiML"></a>TwiML
TwiML to zbiór opartych na języku XML instrukcje zleceń Twilio, które informują Twilio sposób przetwarzania wywołania lub wysyłać wiadomości SMS.

Na przykład następujące TwiML czy Konwertowanie tekstu **Witaj, świecie** do rozpoznawania mowy.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Gdy Twoja aplikacja wywołuje interfejs API usługi Twilio, jeden z parametrów interfejs API jest adres URL, który zwraca odpowiedź TwiML. Do celów programistycznych dostarczone do usługi Twilio adresy URL służy do zapewniania odpowiedzi TwiML używane przez aplikacje. Może także udostępniać własne adresy URL służące do tworzenia odpowiedzi TwiML i innym rozwiązaniem jest użycie **TwiMLResponse** obiektu.

Aby uzyskać więcej informacji na temat Twilio zleceń, ich atrybuty i TwiML zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje na temat interfejsu API usługi Twilio, zobacz [interfejsu API usługi Twilio][twilio_api].

## <a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Gdy wszystko będzie gotowe uzyskać konta usługi Twilio, zarejestruj się pod adresem [spróbuj Twilio][try_twilio]. Można uruchomić przy użyciu bezpłatnego konta i później uaktualnić Twoje konto.

Po zarejestrowaniu się do konta usługi Twilio, otrzymasz identyfikator konta i tokenu uwierzytelniania. Oba będą potrzebne do wykonywania wywołań interfejsu API usługi Twilio. Aby uniemożliwić nieautoryzowany dostęp do Twojego konta, bezpieczeństwo Twojego tokenu uwierzytelniania. Swojego Identyfikatora konta i uwierzytelniania tokenu są wyświetlane w [strona konta usługi Twilio][twilio_account], w polach etykietą **identyfikator SID konta** i **TOKEN uwierzytelniania**, odpowiednio.

## <a id="create_app"></a>Tworzenie aplikacji PHP
Aplikacja PHP, która korzysta z usługi Twilio i działa w systemie Azure jest nie różni się od innych aplikacji PHP, która korzysta z usługi Twilio. Usługi Twilio, są oparte na protokole REST i może być wywoływana z języka PHP na kilka sposobów, w tym artykule koncentruje się na temat korzystania z usługi Twilio za pomocą [biblioteki Twilio dla programów PHP i z usługi GitHub][twilio_php]. Aby uzyskać więcej informacji na temat korzystania z biblioteki usługi Twilio dla języka PHP, zobacz [ https://www.twilio.com/docs/libraries/php ] [ twilio_lib_docs].

Szczegółowe instrukcje dotyczące tworzenia i wdrażania aplikacji usługi Twilio/PHP na platformie Azure są dostępne pod adresem [sposób wprowadzania Twilio za pomocą połączenia telefonicznego w aplikacji w języku PHP na platformie Azure][howto_phonecall_php].

## <a id="configure_app"></a>Konfigurowanie aplikacji przy użyciu bibliotek usługi Twilio
Można skonfigurować aplikację do używania biblioteki Twilio dla języka PHP na dwa sposoby:

1. Pobierz biblioteki Twilio dla programów PHP i z usługi GitHub ([https://github.com/twilio/twilio-php][twilio_php]) i Dodaj **usług** katalogu aplikacji.
   
    -LUB-
2. Zainstaluj bibliotekę usługi Twilio dla programu PHP w pakiecie GRUSZKI. Można go zainstalować za pomocą następujących poleceń:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Po zainstalowaniu biblioteki Twilio dla języka PHP można dodać **require_once** instrukcji w górnej części plików PHP, aby odwoływać się do biblioteki:

        require_once 'Services/Twilio.php';

Aby uzyskać więcej informacji, zobacz [ https://github.com/twilio/twilio-php/blob/master/README.md ] [ twilio_github_readme].

## <a id="howto_make_call"></a>Jak: Wykonywanie wywołania interfejsu wychodzącego
Poniżej pokazano sposób wprowadzania wychodzące wywołanie przy użyciu **Services_Twilio** klasy. Ten kod używa również lokacji dostarczone do usługi Twilio do zwracania odpowiedzi Twilio Markup Language (TwiML). Podstaw wartości **z** i **do** numerów telefonów i upewnij się, aby zweryfikować **z** numer telefonu dla konta usługi Twilio, przed uruchomieniem kodu.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "https://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Jak wspomniano wcześniej, ten kod używa witryny dostarczone do usługi Twilio do zwracania odpowiedzi TwiML. Do własnej witryny można zamiast tego użyć, aby zapewnić odpowiedzi TwiML; Aby uzyskać więcej informacji, zobacz [jak Podaj TwiML odpowiedzi z Twojej własnej witryny sieci Web](#howto_provide_twiml_responses).

* **Uwaga**: Aby rozwiązać błędy sprawdzania poprawności certyfikatów SSL, zobacz [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Jak: Wyślij wiadomość SMS
Poniżej pokazano, jak wysyłać wiadomość SMS przy użyciu **Services_Twilio** klasy. **z** zostanie podany numer, Twilio dla konta wersji próbnej do wysyłania wiadomości SMS. **Do** numer musi być zweryfikowany dla konta usługi Twilio, przed uruchomieniem kodu.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>Jak: Zapewnienie TwiML odpowiedzi z własną witrynę sieci Web
Gdy aplikacja inicjuje wywołanie interfejsu API usługi Twilio, Twilio będzie wysyłać żądania do adresu URL, który powinien zwrócić odpowiedź TwiML. W powyższym przykładzie używa adresu URL dostarczone do usługi Twilio [ https://twimlets.com/message ] [ twimlet_message_url]. (Choć TwiML jest przeznaczony do użytku przez usługi Twilio, można wyświetlić it w Twojej przeglądarce. Na przykład kliknij pozycję [ https://twimlets.com/message ] [ twimlet_message_url] będzie pusta `<Response>` element; inny przykład kliknij [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ] [ twimlet_message_url_hello_world]się `<Response>` element, który zawiera `<Say>` elementu.)

Zamiast polegania na adres URL podany do usługi Twilio, można utworzyć do własnej witryny, która zwraca odpowiedzi HTTP. Witryny można utworzyć w dowolnym języku, który zwraca odpowiedzi XML; w tym temacie założono, że będziesz korzystać z języka PHP do tworzenia TwiML.

Na następującej stronie PHP skutkuje informujący, że odpowiedź TwiML **Witaj, świecie** przy wywołaniu.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Jak widać w powyższym przykładzie odpowiedzi TwiML jest po prostu dokumentu XML. Biblioteka usługi Twilio dla języka PHP zawiera klasy, które będą generowały TwiML dla Ciebie. Poniższy przykład tworzy równoważne odpowiedzi, jak pokazano powyżej, ale używa **usług\_Twilio\_Twiml** klasy w bibliotece Twilio dla języka PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Aby uzyskać więcej informacji na temat TwiML zobacz [ https://www.twilio.com/docs/api/twiml ] [ twiml_reference]. 

Po utworzeniu strony PHP skonfigurowany do zapewniania TwiML odpowiedzi, użyj adres URL strony PHP adres URL przekazany do `Services_Twilio->account->calls->create` metody. Na przykład, jeśli masz aplikację sieci Web o nazwie **MyTwiML** wdrożone usługi hostowanej platformy Azure, a to nazwa strony PHP **mytwiml.php**, adres URL może być przekazywany do **Services_Twilio -> konto -> wywołań -> Tworzenie** jak pokazano w poniższym przykładzie:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Aby uzyskać dodatkowe informacje o korzystaniu z usługi Twilio na platformie Azure za pomocą języka PHP, zobacz [sposób wprowadzania Twilio za pomocą połączenia telefonicznego w aplikacji w języku PHP na platformie Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Jak: Za pomocą usługi Twilio dodatkowych usług
Oprócz przykładów, w tym miejscu Twilio oferuje oparte na sieci web interfejsów API, które można wykorzystać dodatkowych funkcji usługi Twilio, z aplikacji systemu Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API usługi Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi Twilio, skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Twilio Security Guidelines][twilio_security_guidelines]
* [Porada usługi Twilio i przykładowy kod][twilio_howtos]
* [Samouczki szybkiego startu usługi Twilio][twilio_quickstarts] 
* [Twilio w witrynie GitHub][twilio_on_github]
* [Zwróć się do pomocy technicznej usługi Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
