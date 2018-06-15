---
title: Jak używać usługi Twilio głosowe i programu SMS (PHP) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonać połączenie telefoniczne i wysłać wiadomość SMS z usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku PHP.
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
ms.openlocfilehash: bd50eac7390e8639f77894689388e6926cdb619c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866177"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Jak używać usługi Twilio głosowe i możliwości programu SMS w języku PHP
W tym przewodniku przedstawiono sposób wykonywania typowych zadań programowania usługi interfejsu API usługi Twilio na platformie Azure. Omówione scenariusze obejmują tworzenie połączeń telefonicznych i wysyłanie wiadomości krótką wiadomość usługi (SMS). Aby uzyskać więcej informacji na temat usługi Twilio i komunikacja głosowa programu SMS w aplikacji, zobacz [następne kroki](#NextSteps) sekcji.

## <a id="WhatIs"></a>Co to jest usługi Twilio?
Usługi Twilio jest włączanie przyszłości komunikacji biznesowej, umożliwiają deweloperom osadzać głosowych, VoIP i wiadomości do aplikacji. Zwirtualizuj one wszystkich infrastrukturę potrzebną w środowisku chmury, globalnych ujawnienie go za pośrednictwem platformy komunikacji interfejsu API usługi Twilio. Aplikacje są proste tworzenie i skalowalności. Kredyty elastyczność Tobie płatności — jako Przejdź ceny i korzystać z niej niezawodności chmury.

**Usługi Twilio głosu** umożliwia aplikacjom wykonywanie i odbieranie połączeń telefonicznych. **SMS usługi Twilio** umożliwia aplikacji wysyłanie i odbieranie wiadomości tekstowych. **Klient usługi Twilio** służy do nawiązywania połączeń VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje WebRTC.

## <a id="Pricing"></a>Cennik usługi Twilio i oferty specjalne
Azure klienci otrzymają [oferta specjalna](http://www.twilio.com/azure): bezpłatnych 10 środków usługi Twilio po uaktualnieniu konta usługi Twilio. Środki tej usługi Twilio można zastosować do bez użycia usługi Twilio (równoznaczne z wysyłaniem wiadomości SMS maksymalnie 1000 lub odbieranie maksymalnie 1000 przychodzących minut głosu w zależności od lokalizacji telefonu przeznaczenia numer i komunikatu lub wywołania kredyt $10). Realizowanie tego środki usługi Twilio i rozpocząć pracę w: [http://ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Usługi Twilio jest z usługą. Nie żadne opłaty konfiguracji i w dowolnej chwili możesz zamknąć konto. Można znaleźć więcej szczegółów na [cennik usługi Twilio][twilio_pricing].

## <a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio jest interfejs API RESTful, która zapewnia funkcje programu SMS i komunikacja głosowa aplikacji. Klient biblioteki są dostępne w wielu językach; Aby uzyskać listę, zobacz [bibliotek interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio są zleceń usługi Twilio i usługi Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Usługi Twilio zlecenia
Interfejs API korzysta z usługi Twilio zleceń; na przykład  **&lt;powiedzieć&gt;**  zlecenie powoduje, że usługi Twilio komputerowi dostarczenia komunikatu w wywołaniu.

Poniżej znajduje się lista zleceń usługi Twilio. Dowiedz się więcej o innych poleceń i możliwości za pośrednictwem [dokumentacji usługi Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

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

## <a id="create_app"></a>Tworzenie aplikacji PHP
Nie różni się od innych aplikacji PHP, który korzysta z usługi Twilio usługi się aplikacji PHP, która używa usługi usługi Twilio i działa na platformie Azure. Usługi Twilio usług są opartego na interfejsie REST i może zostać wywołana z PHP na kilka sposobów, w tym artykule dotyczą sposobu korzystania z usługi Twilio usług w programie [biblioteki usługi Twilio dla programów PHP i z usługi GitHub][twilio_php]. Aby uzyskać więcej informacji dotyczących korzystania z biblioteki usługi Twilio dla programu PHP, zobacz [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Szczegółowe instrukcje dotyczące tworzenia i wdrażania aplikacji usługi Twilio/PHP na platformie Azure są dostępne pod adresem [porady: Tworzenie usługi przy użyciu połączenia telefonicznego Twilio w aplikacji PHP na platformie Azure][howto_phonecall_php].

## <a id="configure_app"></a>Konfigurowanie aplikacji przy użyciu bibliotek usługi Twilio
Można skonfigurować aplikację do korzystania z biblioteki usługi Twilio dla PHP na dwa sposoby:

1. Pobierz biblioteki usługi Twilio dla PHP z serwisu GitHub ([https://github.com/twilio/twilio-php][twilio_php]) i Dodaj **usług** katalogu aplikacji.
   
    — Lub —
2. Zainstaluj biblioteki usługi Twilio dla programu PHP w pakiecie GRUSZKOWA. Można go zainstalować za pomocą następujących poleceń:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Po zainstalowaniu usługi Twilio biblioteki dla programu PHP, można dodać **require_once** instrukcji w górnej części plików PHP, aby odwołać biblioteki:

        require_once 'Services/Twilio.php';

Aby uzyskać więcej informacji, zobacz [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Porady: wykonywanie połączenia wychodzące
Poniżej pokazano, jak wykonać wychodzące wywołanie przy użyciu **Services_Twilio** klasy. Ten kod zawiera również lokacja dostarczonych do usługi Twilio do zwracania odpowiedzi usługi Twilio Markup Language (TwiML). Podstaw wartości **z** i **do** numerów telefonów i upewnij się, że sprawdzisz **z** numer telefonu dla konta usługi Twilio przed uruchomieniem kodu.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

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

Jak wspomniano, ten kod używa lokacja dostarczonych do usługi Twilio do zwracania odpowiedzi TwiML. Własnej witryny można zamiast tego użyć, aby zapewnić odpowiedzi TwiML; Aby uzyskać więcej informacji, zobacz [jak zapewnić TwiML odpowiedzi z Twoje własne witryny sieci Web](#howto_provide_twiml_responses).

* **Uwaga**: Aby rozwiązać błędy sprawdzania poprawności certyfikatów SSL, zobacz [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Porady: Wyślij wiadomość SMS
Poniżej pokazano, jak wysyłać wiadomość SMS przy użyciu **Services_Twilio** klasy. **z** numer jest udostępniany przez usługi Twilio dla konta wersji próbnej można wysłać wiadomości SMS. **Do** numer muszą być weryfikowane dla konta usługi Twilio przed uruchomieniem kodu.

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

## <a id="howto_provide_twiml_responses"></a>Porady: Podaj TwiML odpowiedzi z własnej witryny sieci Web
Gdy aplikacja jest inicjowany przez wywołanie interfejsu API usługi Twilio, usługi Twilio wysyła żądanie do adresu URL, który powinien zwrócić odpowiedzi TwiML. W powyższym przykładzie korzysta z adresu URL dostarczony do usługi Twilio [http://twimlets.com/message][twimlet_message_url]. (Podczas TwiML jest przeznaczony do użytku przez usługi Twilio, można wyświetlić it w przeglądarce. For example, kliknij przycisk [http://twimlets.com/message] [ twimlet_message_url] wyświetlić pustą `<Response>` element; inny przykład kliknij [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] wyświetlić `<Response>` element, który zawiera `<Say>` elementu.)

Zamiast polegania na adres URL dostarczony do usługi Twilio, można utworzyć własne lokacji, która zwraca odpowiedzi HTTP. Można utworzyć witryny w dowolnym języku, który zwraca odpowiedzi XML; w tym temacie założono, że należy używać PHP do utworzenia TwiML.

Na następującej stronie PHP powoduje odpowiedzi TwiML informacją **Hello World** przy wywołaniu.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Jak widać w powyższym przykładzie, odpowiedź TwiML jest po prostu dokumentu XML. Biblioteka usługi Twilio dla programów PHP i zawiera klasy, które generuje TwiML dla Ciebie. Poniższy przykład tworzy równoważne odpowiedzi, jak pokazano powyżej, ale używa **usług\_usługi Twilio\_Twiml** klasy w bibliotece usługi Twilio dla programu PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Aby uzyskać więcej informacji o TwiML, zobacz [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

Po utworzeniu strony PHP zdefiniowano udzielenie odpowiedzi TwiML, użyj adres URL strony PHP adres URL przekazany `Services_Twilio->account->calls->create` metody. Na przykład, jeśli masz aplikację sieci Web o nazwie **MyTwiML** wdrożone usługi hostowanej platformy Azure i nazwa strony PHP jest **mytwiml.php**, adres URL można przekazać do **Services_Twilio -> wywołania -> konta -> Utwórz** jak pokazano w poniższym przykładzie:

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

Aby uzyskać dodatkowe informacje dotyczące korzystania z usługi Twilio na platformie Azure za pomocą języka PHP, zobacz [porady: Tworzenie usługi przy użyciu połączenia telefonicznego Twilio w aplikacji PHP na platformie Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Porady: korzystania z usługi Twilio dodatkowych usług
Oprócz przykładach pokazano poniżej usługi Twilio oferuje opartych na sieci web interfejsów API, które można wykorzystać dodatkowych funkcji usługi Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API usługi Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi usługi Twilio, skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Wytyczne dotyczące zabezpieczeń usługi Twilio][twilio_security_guidelines]
* [Porada usługi Twilio i przykładowy kod][twilio_howtos]
* [Samouczki usługi Twilio Szybki Start][twilio_quickstarts] 
* [Usługi Twilio w witrynie GitHub][twilio_on_github]
* [Zwróć się do pomocy technicznej usługi Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
