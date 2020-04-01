---
title: Jak używać Twilio do obsługi głosu i wiadomości SMS (PHP) | Dokumenty firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: f9fb250109a1c9000eae8da0d6337c96f19f0f89
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410545"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Jak korzystać z funkcji Twilio dla funkcji głosowych i SMS w PHP
W tym przewodniku pokazano, jak wykonywać typowe zadania programowania za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Scenariusze obejmują nawiązanie połączenia telefonicznego i wysyłanie wiadomości SMS. Aby uzyskać więcej informacji na temat usługi Twilio i używania głosu i wiadomości SMS w aplikacjach, zobacz sekcję [Następne kroki.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co to jest Twilio?
Usługa Twilio napędza przyszłość komunikacji biznesowej, umożliwiając deweloperom osadzanie głosu, voip i wiadomości w aplikacjach. Wirtualizują całą infrastrukturę potrzebną w środowisku globalnym opartym na chmurze, udostępniając ją za pośrednictwem platformy interfejsu API komunikacji usługi Twilio. Aplikacje są proste w kompilacji i skalowalne. Ciesz się elastycznością dzięki cenom płatności zgodnie z rzeczywistym użyciem i korzystaj z niezawodności chmury.

**Aplikacja Twilio Voice** umożliwia aplikacjom nawiązywać i odbierać połączenia telefoniczne. **Usługa Twilio SMS** umożliwia aplikacji wysyłanie i odbieranie wiadomości tekstowych. **Klient Twilio** umożliwia wykonywanie połączeń VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny twilio i oferty specjalne
Klienci platformy Azure otrzymują [specjalną ofertę:](https://www.twilio.com/azure)bezpłatną 10 USD kredytu usługi Twilio po uaktualnieniu konta usługi Twilio. Ten kredyt usługi Twilio można zastosować do dowolnego użycia usługi Twilio (10 usd kredytu równego wysyłaniu aż 1000 wiadomości SMS lub odbierania do 1000 przychodzących minut głosowych, w zależności od lokalizacji numeru telefonu i wiadomości lub miejsca docelowego połączenia). Zrealizuj ten kredyt usługi [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure)Twilio i zacznij od: .

Twilio to usługa płatności zgodnie z rzeczywistym i wyjazdem. Nie ma żadnych opłat za konfigurację i możesz zamknąć konto w dowolnym momencie. Więcej informacji można znaleźć w [cenniku Twilio][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio jest interfejsem API RESTful, który zapewnia funkcje głosowe i sms dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; aby uzyskać listę, zobacz [Biblioteki interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio to czasowniki usługi Twilio i język znaczników języka Twilio (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Czasowniki Twilio
Interfejs API używa czasowników usługi Twilio; na przykład ** &lt;say&gt; ** zlecenie instruuje Twilio do słyszalnego dostarczania wiadomości na wywołanie.

Poniżej znajduje się lista czasowników Twilio. Dowiedz się więcej o innych zleceniach i możliwościach za pomocą [dokumentacji języka znaczników usługi Twilio Markup .](https://www.twilio.com/docs/api/twiml)

* **Pokrętło:&gt;łączy rozmówcę z innym telefonem. &lt;**
* **Zbieraj&gt;: Zbiera cyfry wprowadzone na klawiaturze &lt;** telefonu.
* **Hangup&gt;: Kończy &lt;** połączenie.
* **&gt;Odtwórz : Odtwarza plik audio. &lt;**
* **Pauza&gt;: Czeka cicho przez określoną liczbę &lt;** sekund.
* **Rekord:&gt;Rejestruje głos dzwoniącego i zwraca adres URL pliku zawierającego &lt;** nagranie.
* **Przekierowanie:&gt;Przenosi kontrolę nad połączeniem lub SMS-em do TwiML pod innym adresem URL. &lt;**
* **Odrzuć:&gt;Odrzuca połączenie przychodzące na twój numer usługi Twilio bez naliczania &lt;**
* Powiedz : Konwertuje tekst na mowę, która jest nawoływalona. ** &lt;&gt;**
* Sms : Wysyła wiadomość SMS. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML (twiml)
TwiML to zestaw instrukcji opartych na języku XML opartych na czasownikach usługi Twilio, które informują program Twilio o sposobie przetwarzania połączenia lub wiadomości SMS.

Na przykład następujący TwiML konwertuje tekst **Hello World** na mowę.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Gdy aplikacja wywołuje interfejs API usługi Twilio, jednym z parametrów interfejsu API jest adres URL, który zwraca odpowiedź TwiML. Do celów programisty można użyć adresów URL dostarczonych przez program Twilio, aby zapewnić odpowiedzi TwiML używane przez aplikacje. Można również hostować własne adresy URL do tworzenia odpowiedzi TwiML, a inną opcją jest użycie obiektu **TwiMLResponse.**

Aby uzyskać więcej informacji na temat czasowników usługi Twilio, ich atrybutów i TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje dotyczące interfejsu API usługi Twilio, zobacz [interfejs API usługi Twilio][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Gdy będziesz gotowy do utworzenia konta usługi Twilio, zarejestruj się w [try Twilio][try_twilio]. Możesz zacząć od bezpłatnego konta, a później uaktualnić swoje konto.

Gdy zarejestrujesz konto usługi Twilio, otrzymasz identyfikator konta i token uwierzytelniania. Oba będą potrzebne do wywołania interfejsu API usługi Twilio. Aby zapobiec nieautoryzowanemu dostępowi do konta, należy zabezpieczyć token uwierzytelniania. Identyfikator konta i token uwierzytelniania są widoczne na [stronie konta usługi Twilio][twilio_account], odpowiednio w polach oznaczonych **identyfikatorem SID konta** i **tokenem AUTH.**

## <a name="create-a-php-application"></a><a id="create_app"></a>Tworzenie aplikacji PHP
Aplikacja PHP, która korzysta z usługi Twilio i jest uruchomiona na platformie Azure, nie różni się od żadnej innej aplikacji PHP korzystającej z usługi Twilio. Podczas gdy usługi Twilio są oparte na REST i mogą być wywoływane z PHP na kilka sposobów, ten artykuł skupi się na tym, jak korzystać z usług Twilio z [biblioteką Twilio dla PHP z GitHub][twilio_php]. Aby uzyskać więcej informacji na temat korzystania z [https://www.twilio.com/docs/libraries/php][twilio_lib_docs]biblioteki usługi Twilio dla PHP, zobacz .

Szczegółowe instrukcje dotyczące tworzenia i wdrażania aplikacji Twilio/PHP na platformie Azure są dostępne w [witrynie Jak nawiązać połączenie telefoniczne przy użyciu usługi Twilio w aplikacji PHP na platformie Azure.][howto_phonecall_php]

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurowanie aplikacji do używania bibliotek usługi Twilio
Aplikację można skonfigurować tak, aby używała biblioteki usługi Twilio dla PHP na dwa sposoby:

1. Pobierz bibliotekę usługi Twilio dla PHP z gitHub ([https://github.com/twilio/twilio-php][twilio_php]) i dodaj katalog **usług** do aplikacji.
   
    — Lub —
2. Zainstaluj bibliotekę Twilio dla PHP jako pakiet PEAR. Można go zainstalować za pomocą następujących poleceń:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Po zainstalowaniu biblioteki Twilio dla PHP, można następnie dodać **require_once** instrukcji w górnej części plików PHP, aby odwołać się do biblioteki:

        require_once 'Services/Twilio.php';

Aby uzyskać więcej [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme]informacji, zobacz .

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Jak: Nawiązać połączenie wychodzące
Poniżej przedstawiono sposób nawiązać połączenie wychodzące przy użyciu **klasy Services_Twilio.** Ten kod używa również witryny dostarczonej przez program Twilio do zwrócenia odpowiedzi na język znaczników (TwiML) usługi Twilio. Zastąp wartości numerów telefonów **od** i **do** i upewnij się, że przed uruchomieniem kodu sprawdź numer telefonu **Od** dla swojego konta Twilio.

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

Jak wspomniano, ten kod używa witryny dostarczonej przez twilio do zwrócenia odpowiedzi TwiML. Zamiast tego można użyć własnej witryny, aby zapewnić odpowiedź TwiML; Aby uzyskać więcej informacji, zobacz [Jak zapewnić odpowiedzi TwiML z własnej witryny sieci Web](#howto_provide_twiml_responses).

* **Uwaga:** Aby rozwiązać problemy z błędami sprawdzania poprawności certyfikatów TLS/SSL, zobacz[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Jak: Wyślij wiadomość SMS
Poniżej przedstawiono sposób wysyłania wiadomości SMS przy użyciu **klasy Services_Twilio.** Numer **Od** jest dostarczany przez program Twilio dla kont próbnych do wysyłania wiadomości SMS. Numer **Do** musi zostać zweryfikowany dla konta usługi Twilio przed uruchomieniem kodu.

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Jak: Dostarczaj odpowiedzi TwiML z własnej strony internetowej
Gdy aplikacja inicjuje wywołanie interfejsu API usługi Twilio, Twilio wyśle żądanie do adresu URL, który ma zwrócić odpowiedź TwiML. W powyższym przykładzie użyto adresu [https://twimlets.com/message][twimlet_message_url]URL dostarczonego przez program Twilio . (Podczas TwiML jest przeznaczony do użytku przez Twilio, można go wyświetlić w przeglądarce. Na przykład [https://twimlets.com/message][twimlet_message_url] kliknij, aby `<Response>` wyświetlić pusty element; jako inny przykład, kliknij, [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] aby wyświetlić `<Response>` element, który zawiera `<Say>` element.)

Zamiast polegać na adresie URL dostarczonym przez program Twilio, można utworzyć własną witrynę, która zwraca odpowiedzi HTTP. Witrynę można utworzyć w dowolnym języku, w który zwraca odpowiedzi XML; w tym temacie założono, że będziesz używać PHP do tworzenia TwiML.

Poniższa strona PHP powoduje odpowiedź TwiML, która mówi **Hello World** na wezwanie.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Jak widać na powyższym przykładzie odpowiedź TwiML jest po prostu dokumentem XML. Biblioteka Twilio dla PHP zawiera klasy, które wygenerują TwiML dla Ciebie. Poniższy przykład daje równoważną odpowiedź, jak pokazano powyżej, ale używa **usługi\_Twilio\_Twiml** klasy w bibliotece Twilio dla PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Aby uzyskać więcej informacji na [https://www.twilio.com/docs/api/twiml][twiml_reference]temat TwiML, zobacz . 

Po skonfigurowaniu strony PHP w celu zapewnienia odpowiedzi TwiML użyj adresu URL strony PHP, ponieważ adres URL został przekazany do `Services_Twilio->account->calls->create` metody. Na przykład, jeśli masz aplikację sieci Web o nazwie **MyTwiML** wdrożony w usłudze hosta platformy Azure, a nazwa strony PHP jest **mytwiml.php**, adres URL może być przekazany do **Services_Twilio->account->calls->utworzyć,** jak pokazano w poniższym przykładzie:

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

Aby uzyskać dodatkowe informacje dotyczące korzystania z usługi Twilio na platformie Azure za pomocą php, zobacz [Jak nawiązać połączenie telefoniczne przy użyciu usługi Twilio w aplikacji PHP na platformie Azure][howto_phonecall_php].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Jak: Korzystanie z dodatkowych usług usługi Twilio
Oprócz przykładów pokazanych w tym miejscu usługa Twilio oferuje interfejsy API oparte na sieci Web, których można użyć do wykorzystania dodatkowych funkcji usługi Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API usługi Twilio][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Kolejne kroki
Teraz, gdy znasz podstawy usługi Twilio, skorzystaj z tych łączy, aby dowiedzieć się więcej:

* [Wskazówki dotyczące zabezpieczeń usługi TWILIO][twilio_security_guidelines]
* [Kod Twilio HowTo i Przykładowy][twilio_howtos]
* [Samouczki szybki start usługi Twilio][twilio_quickstarts] 
* [Twilio w usłudze GitHub][twilio_on_github]
* [Porozmawiaj z pomocą techniczną usługi Twilio][twilio_support]

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
