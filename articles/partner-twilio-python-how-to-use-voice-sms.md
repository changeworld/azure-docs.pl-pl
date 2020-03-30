---
title: Jak używać aplikacji Twilio do obsługi głosu i wiadomości SMS (Python) | Dokumenty firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku Python.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.openlocfilehash: edbc9eef6b5f0af2e70152b66228cdf09ef31110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242183"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Jak używać funkcji Twilio dla funkcji głosowych i SMS w pythonie
W tym przewodniku pokazano, jak wykonywać typowe zadania programowania za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Scenariusze obejmują nawiązanie połączenia telefonicznego i wysyłanie wiadomości SMS. Aby uzyskać więcej informacji na temat usługi Twilio i używania głosu i wiadomości SMS w aplikacjach, zobacz sekcję [Następne kroki.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co to jest Twilio?
Usługa Twilio napędza przyszłość komunikacji biznesowej, umożliwiając deweloperom osadzanie głosu, voip i wiadomości w aplikacjach. Wirtualizują całą infrastrukturę potrzebną w środowisku globalnym opartym na chmurze, udostępniając ją za pośrednictwem platformy interfejsu API komunikacji usługi Twilio. Aplikacje są proste w kompilacji i skalowalne. Ciesz się elastycznością dzięki cenom płatności zgodnie z rzeczywistym użyciem i korzystaj z niezawodności chmury.

**Aplikacja Twilio Voice** umożliwia aplikacjom nawiązywać i odbierać połączenia telefoniczne.
**Usługa Twilio SMS** umożliwia aplikacji wysyłanie i odbieranie wiadomości tekstowych.
**Klient Twilio** umożliwia wykonywanie połączeń VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny twilio i oferty specjalne
Klienci platformy Azure otrzymują [specjalną ofertę][special_offer] $10 programu Twilio Credit podczas uaktualniania konta usługi Twilio. Ten kredyt usługi Twilio można zastosować do dowolnego użycia usługi Twilio (10 usd kredytu równego wysyłaniu aż 1000 wiadomości SMS lub odbierania do 1000 przychodzących minut głosowych, w zależności od lokalizacji numeru telefonu i wiadomości lub miejsca docelowego połączenia). Zrealizuj ten [kredyt usługi Twilio][special_offer] i rozpocznij pracę.

Twilio to usługa płatności zgodnie z rzeczywistym i wyjazdem. Nie ma żadnych opłat za konfigurację i możesz zamknąć konto w dowolnym momencie. Więcej informacji można znaleźć w [cenniku Twilio][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio jest interfejsem API RESTful, który zapewnia funkcje głosowe i sms dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; aby uzyskać listę, zobacz [Biblioteki interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio to czasowniki usługi Twilio i język znaczników języka Twilio (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Czasowniki Twilio
Interfejs API używa czasowników usługi Twilio; na przykład ** &lt;say&gt; ** zlecenie instruuje Twilio do słyszalnego dostarczania wiadomości na wywołanie.

Poniżej znajduje się lista czasowników Twilio. Dowiedz się więcej o innych zleceniach i możliwościach za pomocą [dokumentacji języka znaczników usługi Twilio Markup .][twiml]

* **Pokrętło:&gt;łączy rozmówcę z innym telefonem. &lt;**
* **Zbieraj&gt;: Zbiera cyfry wprowadzone na klawiaturze &lt;** telefonu.
* **Hangup&gt;: Kończy &lt;** połączenie.
* **Pauza&gt;: Czeka cicho przez określoną liczbę &lt;** sekund.
* **&gt;Odtwórz : Odtwarza plik audio. &lt;**
* **Kolejka&gt;: Dodaj do kolejki rozmówców. &lt;**
* **&gt;Nagrywanie: Rejestruje głos osoby dzwoniącej i zwraca adres URL pliku zawierającego nagranie. &lt;**
* **Przekierowanie:&gt;Przenosi kontrolę nad połączeniem lub SMS-em do TwiML pod innym adresem URL. &lt;**
* **Odrzuć:&gt;Odrzuca połączenie przychodzące na twój numer usługi Twilio bez rozliczeń. &lt;**
* Powiedz : Konwertuje tekst na mowę, która jest nawoływalona. ** &lt;&gt;**
* Sms : Wysyła wiadomość SMS. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML (twiml)
TwiML to zestaw instrukcji opartych na języku XML opartych na czasownikach usługi Twilio, które informują program Twilio o sposobie przetwarzania połączenia lub wiadomości SMS.

Na przykład następujący TwiML konwertuje tekst **Hello World** na mowę.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Gdy aplikacja wywołuje interfejs API usługi Twilio, jednym z parametrów interfejsu API jest adres URL, który zwraca odpowiedź TwiML. Do celów programisty można użyć adresów URL dostarczonych przez program Twilio, aby zapewnić odpowiedzi TwiML używane przez aplikacje. Można również hostować własne adresy URL do tworzenia odpowiedzi TwiML, `TwiMLResponse` a inną opcją jest użycie obiektu.

Aby uzyskać więcej informacji na temat czasowników usługi Twilio, ich atrybutów i TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje dotyczące interfejsu API usługi Twilio, zobacz [interfejs API usługi Twilio][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Gdy będziesz gotowy do uzyskania konta usługi Twilio, zarejestruj się w [try Twilio][try_twilio]. Możesz zacząć od bezpłatnego konta, a później uaktualnić swoje konto.

Podczas rejestracji konta usługi Twilio otrzymasz identyfikator SID konta i token uwierzytelniania. Oba będą potrzebne do wywołania interfejsu API usługi Twilio. Aby zapobiec nieautoryzowanemu dostępowi do konta, należy zabezpieczyć token uwierzytelniania. Identyfikator SID konta i token uwierzytelniania są widoczne w [konsoli usługi Twilio][twilio_console], odpowiednio w polach oznaczonych **identyfikatorem SID konta** i **tokenem AUTH.**

## <a name="create-a-python-application"></a><a id="create_app"></a>Tworzenie aplikacji języka Python
Aplikacja języka Python, która korzysta z usługi Twilio i jest uruchomiona na platformie Azure nie różni się od jakiejkolwiek innej aplikacji języka Python, która korzysta z usługi Twilio. Chociaż usługi usługi Twilio są oparte na rest i mogą być wywoływane z Języka Python na kilka sposobów, w tym artykule skupi się na tym, jak korzystać z usług usługi Twilio z [biblioteką Twilio dla Języka Python z GitHub][twilio_python]. Aby uzyskać więcej informacji na temat korzystania [https://www.twilio.com/docs/libraries/python][twilio_lib_docs]z biblioteki usługi Twilio dla języka Python, zobacz .

Najpierw [skonfiguruj nową maszynę wirtualną systemu Azure Linux][azure_vm_setup], aby działać jako host dla nowej aplikacji sieci Web języka Python. Po uruchomieniu maszyny wirtualnej należy udostępnić aplikację na porcie publicznym, jak opisano poniżej.

### <a name="add-an-incoming-rule"></a>Dodawanie reguły przychodzącej
  1. Przejdź do strony [Network Security Group][azure_nsg].
  2. Wybierz grupę zabezpieczeń sieci, która odpowiada maszynie wirtualnej.
  3. Reguła **dodawania** i wychodząca dla **portu 80**. Pamiętaj, aby zezwolić na przychodzące z dowolnego adresu.

### <a name="set-the-dns-name-label"></a>Ustawianie etykiety nazwy DNS
  1. Przejdź do strony [Publiczne adresy IP][azure_ips].
  2. Wybierz publiczny adres IP, który odpowiada maszynie wirtualnej.
  3. Ustaw **etykietę nazwy DNS** w sekcji **Konfiguracja.** W przypadku tego przykładu będzie wyglądać mniej więcej tak *twoja domena-label*.centralus.cloudapp.azure.com

Gdy jesteś w stanie połączyć się za pośrednictwem SSH do maszyny wirtualnej można zainstalować Web Framework do wyboru (dwa najbardziej znane w Pythonie jest [Flask](http://flask.pocoo.org/) i [Django](https://www.djangoproject.com)). Możesz zainstalować jedną z nich po `pip install` prostu uruchamiając polecenie.

Należy pamiętać, że firma Virtual Machine skonfigurowała ją tak, aby zezwalała na ruch tylko na porcie 80. Dlatego upewnij się, że skonfiguruj aplikację do używania tego portu.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurowanie aplikacji do używania bibliotek usługi Twilio
Aplikację można skonfigurować tak, aby używała biblioteki usługi Twilio dla języka Python na dwa sposoby:

* Zainstaluj bibliotekę usługi Twilio dla języka Python jako pakiet Pip. Można go zainstalować za pomocą następujących poleceń:
   
        $ pip install twilio

    — Lub —

* Pobierz bibliotekę Twilio dla Pythona[https://github.com/twilio/twilio-python][twilio_python]z GitHub ( ) i zainstaluj ją w ten sposób:

        $ python setup.py install

Po zainstalowaniu biblioteki Twilio dla Języka `import` Python, można go następnie w plikach Pythona:

        import twilio

Aby uzyskać więcej informacji, zobacz [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Jak: Nawiązać połączenie wychodzące
Poniżej przedstawiono sposób nawiązać połączenie wychodzące. Ten kod używa również witryny dostarczonej przez program Twilio do zwrócenia odpowiedzi na język znaczników (TwiML) usługi Twilio. Zastąp wartości **from_number** i **to_number** numerów telefonów i upewnij się, że przed uruchomieniem kodu zweryfikowano **from_number** numer telefonu dla swojego konta usługi Twilio.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "https://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Jak wspomniano, ten kod używa witryny dostarczonej przez twilio do zwrócenia odpowiedzi TwiML. Zamiast tego można użyć własnej witryny, aby zapewnić odpowiedź TwiML; Aby uzyskać więcej informacji, zobacz [Jak zapewnić odpowiedzi TwiML z własnej witryny sieci Web](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Jak: Wyślij wiadomość SMS
Poniżej pokazano, jak wysłać `TwilioRestClient` wiadomość SMS przy użyciu klasy. Numer **from_number** jest dostarczany przez program Twilio dla kont próbnych do wysyłania wiadomości SMS. Numer **to_number** musi zostać zweryfikowany dla twojego konta usługi Twilio przed uruchomieniem kodu.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Jak: Dostarczaj odpowiedzi TwiML z własnej strony internetowej
Gdy aplikacja inicjuje wywołanie interfejsu API usługi Twilio, Twilio wyśle żądanie do adresu URL, który ma zwrócić odpowiedź TwiML. W powyższym przykładzie użyto adresu [https://twimlets.com/message][twimlet_message_url]URL dostarczonego przez program Twilio . (Podczas TwiML jest przeznaczony do użytku przez Twilio, można go wyświetlić w przeglądarce. Na przykład [https://twimlets.com/message][twimlet_message_url] kliknij, aby `<Response>` wyświetlić pusty element; jako inny przykład, kliknij, [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] aby wyświetlić `<Response>` element, który zawiera `<Say>` element.)

Zamiast polegać na adresie URL dostarczonym przez program Twilio, można utworzyć własną witrynę, która zwraca odpowiedzi HTTP. Witrynę można utworzyć w dowolnym języku, w który zwraca odpowiedzi XML; w tym temacie przyjęto założenie, że będzie używany Python do tworzenia TwiML.

Poniższe przykłady spowoduje wysunie odpowiedź TwiML, który mówi **Hello World** na wywołanie.

Z kolbą:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Z Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Jak widać na powyższym przykładzie odpowiedź TwiML jest po prostu dokumentem XML. Biblioteka usługi Twilio dla języka Python zawiera klasy, które będą generować TwiML dla Ciebie. Poniższy przykład daje równoważną odpowiedź, jak pokazano `twiml` powyżej, ale używa modułu w bibliotece usługi Twilio dla języka Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Aby uzyskać więcej informacji na [https://www.twilio.com/docs/api/twiml][twiml_reference]temat TwiML, zobacz .

Po skonfigurowaniu aplikacji Języka Python w celu zapewnienia odpowiedzi TwiML użyj adresu URL `client.calls.create` aplikacji jako adresu URL przekazanego do metody. Na przykład jeśli masz aplikację sieci Web o nazwie **MyTwiML** wdrożoną w usłudze hosta platformy Azure, możesz użyć jej adresu URL jako elementu webhook, jak pokazano w poniższym przykładzie:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Jak: Korzystanie z dodatkowych usług usługi Twilio
Oprócz przykładów pokazanych w tym miejscu usługa Twilio oferuje interfejsy API oparte na sieci Web, których można użyć do wykorzystania dodatkowych funkcji usługi Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API usługi Twilio][twilio_api].

## <a name="next-steps"></a><a id="NextSteps"></a>Kolejne kroki
Teraz, gdy znasz podstawy usługi Twilio, skorzystaj z tych łączy, aby dowiedzieć się więcej:

* [Wskazówki dotyczące zabezpieczeń usługi TWILIO][twilio_security_guidelines]
* [Przewodniki Twilio HowTo i przykładowy kod][twilio_howtos]
* [Samouczki szybki start usługi Twilio][twilio_quickstarts]
* [Twilio w usłudze GitHub][twilio_on_github]
* [Porozmawiaj z pomocą techniczną usługi Twilio][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
