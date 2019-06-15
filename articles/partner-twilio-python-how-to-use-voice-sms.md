---
title: Jak używać usługi Twilio dla połączeń głosowych i wiadomości SMS (Python) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązywanie połączeń telefonicznych i wysyłanie wiadomości SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku Python.
services: ''
documentationcenter: python
author: devinrader
manager: twilio
editor: ''
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: e6cfd9e72dc1a38e4ed0c11320336ccc4b44a2c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61457672"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Jak za pomocą usługi Twilio dla połączeń głosowych i SMS funkcji w języku Python
Ten przewodnik pokazuje sposób wykonywania typowych zadań programistycznych w usłudze interfejsu API usługi Twilio, na platformie Azure. Omówione scenariusze obejmują tworzenie połączenia telefonicznego i wysyłanie wiadomości wiadomości usługi (SMS). Aby uzyskać więcej informacji na temat usługi Twilio i używania połączeń głosowych i wiadomości SMS w swoich aplikacjach, zobacz [następne kroki](#NextSteps) sekcji.

## <a id="WhatIs"></a>Co to jest Twilio?
Twilio to najważniejsza przyszłość komunikacji biznesowej, dzięki czemu deweloperzy mogą osadzać głosu, VoIP i komunikatów w aplikacjach. Wirtualizacja one wszystkie elementy infrastruktury potrzebne w środowisku oparte na chmurze, globalnej uwidaczniania go przy użyciu platformy łączności interfejsu API usługi Twilio. Aplikacje są proste do tworzenia i skalowalne. Elastyczność dzięki płatności — jako — można przejść, ceny i korzystać z niezawodności chmury.

**Twilio dla połączeń głosowych** umożliwia aplikacjom wykonywanie i odbieranie połączeń telefonicznych.
**Wiadomości SMS usługi Twilio** umożliwia aplikacji wysyłanie i odbieranie wiadomości SMS.
**Klient usługi Twilio** umożliwia nawiązywanie połączeń VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje protokołu WebRTC.

## <a id="Pricing"></a>Cennik usługi Twilio i ofert specjalnych
Klienci platformy Azure otrzymują [oferta specjalna] [ special_offer] w wysokości 10 USD kredytu usługi Twilio, po uaktualnieniu konta usługi Twilio. Wartość tego kredytu Twilio mogą dotyczyć każde użycie usługi Twilio (równoważne do wysyłania wiadomości SMS maksymalnie 1000 lub odbieranie maksymalnie 1000 dla ruchu przychodzącego minut głosu w zależności od lokalizacji docelowej liczby i wiadomości lub wywołania telefonu środków w wysokości 10 USD). Zrealizuj ten [środki Twilio] [ special_offer] i rozpocząć pracę.

Twilio to usługa, zgodnie z rzeczywistym użyciem. Nie są pobierane żadne opłaty konfiguracji i w dowolnym momencie można zamknąć konto. Można znaleźć więcej szczegółów na [cennik usługi Twilio][twilio_pricing].

## <a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio to API typu RESTful, który zapewnia połączeń głosowych i SMS funkcje dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; Aby uzyskać listę, zobacz [bibliotek interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio to zleceń usługi Twilio i Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio zlecenia
Interfejs API korzysta z usługi Twilio zleceń; na przykład **&lt;Say&gt;** Twilio komputerowi dostarczenia komunikatu w wywołaniu powoduje, że zlecenie.

Oto lista poleceń usługi Twilio. Dowiedz się więcej o innych poleceń i możliwości za pośrednictwem [dokumentacji usługi Twilio Markup Language][twiml].

* **&lt;Wybierania&gt;** : Obiekt wywołujący nawiązanie połączenia innego numeru telefonu.
* **&lt;Gather&gt;** : Gromadzi informacje o cyfry wprowadzone na klawiaturze telefonu.
* **&lt;Rozłączanie&gt;** : Kończy wywołanie.
* **&lt;Pause&gt;** : Dyskretnie czeka określoną liczbę sekund.
* **&lt;Play&gt;** : Odtwarza plik audio.
* **&lt;Queue&gt;** : Dodaj do kolejki obiekty wywołujące.
* **&lt;Rekord&gt;** : Rejestruje głosu obiektu wywołującego i zwraca adres URL pliku, który zawiera nagrywania.
* **&lt;Redirect&gt;** : Transfer kontroli połączenia lub wiadomości SMS do TwiML na inny adres URL.
* **&lt;Reject&gt;** : Odrzuca połączenie na numer Twilio bez możesz rozliczeń.
* **&lt;Powiedz&gt;** : Konwertuje tekst na mowę, który składa się na wywołanie.
* **&lt;Sms&gt;** : Wysyła wiadomość SMS.

### <a id="TwiML"></a>TwiML
TwiML to zbiór opartych na języku XML instrukcje zleceń Twilio, które informują Twilio sposób przetwarzania wywołania lub wysyłać wiadomości SMS.

Na przykład następujące TwiML czy Konwertowanie tekstu **Witaj, świecie** do rozpoznawania mowy.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Gdy Twoja aplikacja wywołuje interfejs API usługi Twilio, jeden z parametrów interfejs API jest adres URL, który zwraca odpowiedź TwiML. Do celów programistycznych dostarczone do usługi Twilio adresy URL służy do zapewniania odpowiedzi TwiML używane przez aplikacje. Może także udostępniać własne adresy URL służące do tworzenia odpowiedzi TwiML i innym rozwiązaniem jest użycie `TwiMLResponse` obiektu.

Aby uzyskać więcej informacji na temat Twilio zleceń, ich atrybuty i TwiML zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje na temat interfejsu API usługi Twilio, zobacz [interfejsu API usługi Twilio][twilio_api].

## <a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Gdy jesteś gotowy, można uzyskać konta usługi Twilio, zarejestruj się pod adresem [spróbuj Twilio][try_twilio]. Można uruchomić przy użyciu bezpłatnego konta i później uaktualnić Twoje konto.

Po zarejestrowaniu się do konta usługi Twilio, otrzymasz identyfikator SID konta i tokenu uwierzytelniania. Oba będą potrzebne do wykonywania wywołań interfejsu API usługi Twilio. Aby uniemożliwić nieautoryzowany dostęp do Twojego konta, bezpieczeństwo Twojego tokenu uwierzytelniania. Identyfikator SID konta i token uwierzytelniania jest wyświetlana w [konsoli Twilio][twilio_console], w polach etykietą **identyfikator SID konta** i **TOKEN uwierzytelniania**, odpowiednio.

## <a id="create_app"></a>Tworzenie aplikacji w języku Python
Aplikacji w języku Python, który korzysta z usługi Twilio i działa w systemie Azure jest nie różni się od innej aplikacji języka Python, który korzysta z usługi Twilio. Usługi Twilio, są oparte na protokole REST i może być wywoływana z języka Python na kilka sposobów, w tym artykule koncentruje się na temat korzystania z usługi Twilio za pomocą [Twilio biblioteki dla języka Python z witryny GitHub][twilio_python]. Aby uzyskać więcej informacji na temat korzystania z biblioteki usługi Twilio dla języka Python, zobacz [ https://www.twilio.com/docs/libraries/python ] [ twilio_lib_docs].

Najpierw [konfiguracji nowej maszyny Wirtualnej systemu Linux platformy Azure] [azure_vm_setup], aby pełnić rolę hosta dla aplikacji sieci web nowego języka Python. Gdy maszyna wirtualna jest uruchomiona, należy udostępnić aplikację na publicznym porcie, zgodnie z poniższym opisem.

### <a name="add-an-incoming-rule"></a>Dodawanie reguły przychodzące
  1. Przejdź do strony [sieciowa grupa zabezpieczeń] [azure_nsg].
  2. Wybierz grupy zabezpieczeń sieci, który odpowiada na maszynie wirtualnej.
  3. Dodaj i **reguły wychodzące** dla **portu 80**. Pamiętaj zezwolić na przychodzący z dowolnego adresu.

### <a name="set-the-dns-name-label"></a>Ustaw etykietę nazwy DNS
  1. Przejdź do strony [publiczne adresy IP] [azure_ips].
  2. Wybierz publiczny adres IP odpowiadający na maszynie wirtualnej.
  3. Ustaw **etykiety nazwy DNS** w **konfiguracji** sekcji. W tym przykładzie ekran będzie wyglądać następująco *your etykietę domeny*. centralus.cloudapp.azure.com

Gdy jesteś w stanie nawiązać połączenie za pośrednictwem protokołu SSH z maszyną wirtualną można zainstalować wybranego środowiska sieci Web (dwie najbardziej popularną w języku Python jest [Flask](http://flask.pocoo.org/) i [Django](https://www.djangoproject.com)). Można zainstalować jedną z nich po prostu, uruchamiając `pip install` polecenia.

Należy pamiętać, że firma Microsoft skonfigurowany maszyny wirtualnej, aby zezwolić na ruch tylko na porcie 80. Dlatego upewnij się skonfigurować aplikację do używania tego portu.

## <a id="configure_app"></a>Konfigurowanie aplikacji przy użyciu bibliotek usługi Twilio
Można skonfigurować aplikację do używania biblioteki Twilio dla języka Python na dwa sposoby:

* Zainstaluj bibliotekę usługi Twilio dla języka Python, jako pakiet Pip. Można go zainstalować za pomocą następujących poleceń:
   
        $ pip install twilio

    -LUB-

* Pobierz biblioteki Twilio dla języka Python z usługi GitHub ([https://github.com/twilio/twilio-python][twilio_python]) i zainstaluj go w następujący sposób:

        $ python setup.py install

Po zainstalowaniu biblioteki Twilio dla języka Python, możesz następnie `import` je w plikach języka Python:

        import twilio

Aby uzyskać więcej informacji, zobacz [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.rst).

## <a id="howto_make_call"></a>Jak: Wykonywanie wywołania interfejsu wychodzącego
Poniżej przedstawiono sposób wykonania wywołania wychodzącego. Ten kod używa również lokacji dostarczone do usługi Twilio do zwracania odpowiedzi Twilio Markup Language (TwiML). Podstaw wartości **from_number** i **to_number** numerów telefonów i upewnij się, że masz już pewność **from_number** numer telefonu dla konta usługi Twilio przed uruchomieniem kodu.

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

Jak wspomniano wcześniej, ten kod używa witryny dostarczone do usługi Twilio do zwracania odpowiedzi TwiML. Do własnej witryny można zamiast tego użyć, aby zapewnić odpowiedzi TwiML; Aby uzyskać więcej informacji, zobacz [jak Podaj TwiML odpowiedzi z Twojej własnej witryny sieci Web](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Jak: Wyślij wiadomość SMS
Poniżej pokazano, jak wysyłać wiadomość SMS przy użyciu `TwilioRestClient` klasy. **From_number** zostanie podany numer, Twilio dla konta wersji próbnej do wysyłania wiadomości SMS. **To_number** numer musi być zweryfikowany dla konta usługi Twilio, przed uruchomieniem kodu.

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

## <a id="howto_provide_twiml_responses"></a>Jak: Zapewnienie TwiML odpowiedzi z własną witrynę sieci Web
Gdy aplikacja inicjuje wywołanie interfejsu API usługi Twilio, Twilio będzie wysyłać żądania do adresu URL, który powinien zwrócić odpowiedź TwiML. W powyższym przykładzie używa adresu URL dostarczone do usługi Twilio [ https://twimlets.com/message ] [ twimlet_message_url]. (Choć TwiML jest przeznaczony do użytku przez usługi Twilio, można wyświetlić je w przeglądarce. Na przykład kliknij pozycję [ https://twimlets.com/message ] [ twimlet_message_url] będzie pusta `<Response>` element; inny przykład kliknij [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ] [ twimlet_message_url_hello_world]się `<Response>` element, który zawiera `<Say>` elementu.)

Zamiast polegania na adres URL podany do usługi Twilio, można utworzyć do własnej witryny, która zwraca odpowiedzi HTTP. Witryny można utworzyć w dowolnym języku, który zwraca odpowiedzi XML; w tym temacie założono, że będziesz używać języka Python do tworzenia TwiML.

Poniższe przykłady zwróci odpowiedź TwiML informujący, że **Witaj, świecie** przy wywołaniu.

Za pomocą Flask:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Za pomocą Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Jak widać w powyższym przykładzie odpowiedzi TwiML jest po prostu dokumentu XML. Twilio dla języka Python w bibliotece klas, które będą generowały TwiML dla Ciebie. Poniższy przykład tworzy równoważne odpowiedzi, jak pokazano powyżej, ale używa `twiml` modułu w bibliotece Twilio dla języka Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Aby uzyskać więcej informacji na temat TwiML zobacz [ https://www.twilio.com/docs/api/twiml ] [ twiml_reference].

Po utworzeniu aplikacji Python skonfigurowany do zapewniania TwiML odpowiedzi, należy użyć adresu URL aplikacji jako adres URL przekazany do `client.calls.create` metody. Na przykład, jeśli masz aplikację sieci Web o nazwie **MyTwiML** wdrożona na platformie Azure hostowane usługi, możesz użyć adresu url jako elementu webhook, jak pokazano w poniższym przykładzie:

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

## <a id="AdditionalServices"></a>Jak: Za pomocą usługi Twilio dodatkowych usług
Oprócz przykładów, w tym miejscu Twilio oferuje oparte na sieci web interfejsów API, które można wykorzystać dodatkowych funkcji usługi Twilio, z aplikacji systemu Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API usługi Twilio][twilio_api].

## <a id="NextSteps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi Twilio, skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Twilio Security Guidelines][twilio_security_guidelines]
* [Przewodniki Porada usługi Twilio i przykładowy kod][twilio_howtos]
* [Samouczki szybkiego startu usługi Twilio][twilio_quickstarts]
* [Twilio w witrynie GitHub][twilio_on_github]
* [Zwróć się do pomocy technicznej usługi Twilio][twilio_support]

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
