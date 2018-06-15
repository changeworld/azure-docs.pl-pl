---
title: Jak używać usługi Twilio głosowe i programu SMS (Python) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonać połączenie telefoniczne i wysłać wiadomość SMS z usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku Python.
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
ms.openlocfilehash: f4a02bb7a7c46e7a0e3c75b870c522eae8294339
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866142"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Jak używać usługi Twilio głosowe i możliwości programu SMS w języku Python
W tym przewodniku przedstawiono sposób wykonywania typowych zadań programowania usługi interfejsu API usługi Twilio na platformie Azure. Omówione scenariusze obejmują tworzenie połączeń telefonicznych i wysyłanie wiadomości krótką wiadomość usługi (SMS). Aby uzyskać więcej informacji na temat usługi Twilio i komunikacja głosowa programu SMS w aplikacji, zobacz [następne kroki](#NextSteps) sekcji.

## <a id="WhatIs"></a>Co to jest usługi Twilio?
Usługi Twilio jest włączanie przyszłości komunikacji biznesowej, umożliwiają deweloperom osadzać głosowych, VoIP i wiadomości do aplikacji. Zwirtualizuj one wszystkich infrastrukturę potrzebną w środowisku chmury, globalnych ujawnienie go za pośrednictwem platformy komunikacji interfejsu API usługi Twilio. Aplikacje są proste tworzenie i skalowalności. Kredyty elastyczność Tobie płatności — jako Przejdź ceny i korzystać z niej niezawodności chmury.

**Usługi Twilio głosu** umożliwia aplikacjom wykonywanie i odbieranie połączeń telefonicznych.
**SMS usługi Twilio** umożliwia aplikacji wysyłanie i odbieranie wiadomości tekstowych.
**Klient usługi Twilio** służy do nawiązywania połączeń VoIP z dowolnego telefonu, tabletu lub przeglądarki i obsługuje WebRTC.

## <a id="Pricing"></a>Cennik usługi Twilio i oferty specjalne
Azure klienci otrzymają [oferta specjalna] [ special_offer] 10 $ środków usługi Twilio po uaktualnieniu konta usługi Twilio. Środki tej usługi Twilio można zastosować do bez użycia usługi Twilio (równoznaczne z wysyłaniem wiadomości SMS maksymalnie 1000 lub odbieranie maksymalnie 1000 przychodzących minut głosu w zależności od lokalizacji telefonu przeznaczenia numer i komunikatu lub wywołania kredyt $10). Zrealizować to [środki usługi Twilio] [ special_offer] i rozpocząć pracę.

Usługi Twilio jest z usługą. Nie żadne opłaty konfiguracji i w dowolnej chwili możesz zamknąć konto. Można znaleźć więcej szczegółów na [cennik usługi Twilio][twilio_pricing].

## <a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio jest interfejs API RESTful, która zapewnia funkcje programu SMS i komunikacja głosowa aplikacji. Klient biblioteki są dostępne w wielu językach; Aby uzyskać listę, zobacz [bibliotek interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio są zleceń usługi Twilio i usługi Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Usługi Twilio zlecenia
Interfejs API korzysta z usługi Twilio zleceń; na przykład  **&lt;powiedzieć&gt;**  zlecenie powoduje, że usługi Twilio komputerowi dostarczenia komunikatu w wywołaniu.

Poniżej znajduje się lista zleceń usługi Twilio. Dowiedz się więcej o innych poleceń i możliwości za pośrednictwem [dokumentacji usługi Twilio Markup Language][twiml].

* **&lt;Wybierania&gt;**: łączy wywołującego innego numeru telefonu.
* **&lt;Zbierz&gt;**: zbiera cyfry wprowadzone na klawiaturze telefonu.
* **&lt;Rozłączanie&gt;**: kończy wywołanie.
* **&lt;Wstrzymaj&gt;**: dyskretnie czeka na określoną liczbę sekund.
* **&lt;Odtwórz&gt;**: odtwarzania pliku audio.
* **&lt;Kolejka&gt;**: Dodaj do kolejki obiekty wywołujące.
* **&lt;Rekord&gt;**: rejestruje głos wywołującego i zwraca adres URL pliku zawierającego nagrywania.
* **&lt;Przekieruj&gt;**: przekazuje kontrolę nad wywołania lub wiadomości SMS do TwiML na inny adres URL.
* **&lt;Odrzuć&gt;**: odrzucanie połączenia przychodzącego na numer usługi Twilio bez rozliczeń można.
* **&lt;Powiedz&gt;**: Konwertuje tekst na mowę, które zostało utworzone w wywołaniu.
* **&lt;SMS&gt;**: wysyła wiadomość SMS.

### <a id="TwiML"></a>TwiML
TwiML to zestaw instrukcji oparte na języku XML, oparte na zlecenia usługi Twilio, które informują usługi Twilio sposobu przetwarzania połączenia lub wiadomości SMS.

Na przykład następujące TwiML czy Konwertuj tekst **Hello World** do rozpoznawania mowy.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Gdy aplikacja wywołuje interfejs API usługi Twilio, jeden z parametrów interfejsu API jest adres URL, który zwraca odpowiedź TwiML. Do celów programistycznych wprowadzone do usługi Twilio adresy URL służy do nadania odpowiedzi TwiML używane przez aplikacje. Może również udostępniać swoje własne adresy URL do tworzenia odpowiedzi TwiML i inną opcją jest użycie `TwiMLResponse` obiektu.

Aby uzyskać więcej informacji na temat usługi Twilio zlecenia i ich atrybuty oraz TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje na temat interfejsu API usługi Twilio, zobacz [interfejsu API usługi Twilio][twilio_api].

## <a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Gdy wszystko będzie gotowe, można uzyskać konta usługi Twilio, zarejestruj się w [spróbuj usługi Twilio][try_twilio]. Można rozpoczynać bezpłatne konto, a następnie Uaktualnij swoje konto później.

Po utworzeniu konta dla konta usługi Twilio, otrzymasz identyfikator SID konta i tokenu uwierzytelniania. Zarówno będą potrzebne w celu wykonywania wywołań interfejsu API usługi Twilio. Aby uniemożliwić nieautoryzowany dostęp do Twojego konta, bezpieczeństwo Twojego tokenu uwierzytelniania. Identyfikator SID konta, a token uwierzytelniania jest wyświetlana w [usługi Twilio Console][twilio_console], w polach etykietą **identyfikator SID konta** i **TOKEN uwierzytelniania**odpowiednio.

## <a id="create_app"></a>Tworzenie aplikacji Python
Nie różni się od innych aplikacji Python, korzystającą z usługi Twilio usługi się aplikacji Python, która używa usługi usługi Twilio i działa na platformie Azure. Usługi Twilio usług są opartego na interfejsie REST i może zostać wywołana z Python na kilka sposobów, w tym artykule dotyczą sposobu korzystania z usługi Twilio usług w programie [biblioteki usługi Twilio dla języka Python z witryny GitHub][twilio_python]. Aby uzyskać więcej informacji dotyczących korzystania z biblioteki usługi Twilio dla języka Python, zobacz [http://readthedocs.org/docs/twilio-python/en/latest/index.html][twilio_lib_docs].

Pierwsza strona, [konfiguracji nowej maszyny Wirtualnej systemu Linux Azure] [azure_vm_setup] do działania jako hosta dla aplikacji sieci web z nowego języka Python. Gdy maszyna wirtualna jest uruchomiona, należy udostępnić aplikacji na port publiczny, zgodnie z poniższym opisem.

### <a name="add-an-incoming-rule"></a>Dodawanie reguły przychodzące
  1. Przejdź do strony [sieciowej grupy zabezpieczeń] [azure_nsg].
  2. Wybierz grupy zabezpieczeń sieci, która odpowiada z maszyny wirtualnej.
  3. Dodaj i **reguły wychodzące** dla **port 80**. Pamiętaj umożliwić przychodzące z dowolnego adresu.

### <a name="set-the-dns-name-label"></a>Ustaw etykieta nazwy DNS
  1. Przejdź do strony [publicznego adresu IP adresów] [azure_ips].
  2. Wybierz publiczny adres IP tego correspends z maszyny wirtualnej.
  3. Ustaw **etykieta nazwy DNS** w **konfiguracji** sekcji. W tym przykładzie ekran będzie wyglądać następująco *your etykieta domeny*. centralus.cloudapp.azure.com

Po przejściu łączenie się za pośrednictwem protokołu SSH z maszyną wirtualną można zainstalować struktura sieci Web dowolnego (najbardziej dobrze znany jest Python dwa [Flask](http://flask.pocoo.org/) i [Django](https://www.djangoproject.com)). Można zainstalować jedną z nich tylko przez uruchomienie `pip install` polecenia.

Należy pamiętać, że został skonfigurowany maszynę wirtualną, aby zezwolić na ruch tylko na porcie 80. Dlatego upewnij się skonfigurować aplikację do użycia tego portu.

## <a id="configure_app"></a>Konfigurowanie aplikacji przy użyciu bibliotek usługi Twilio
Można skonfigurować aplikację do korzystania z biblioteki usługi Twilio dla języka Python na dwa sposoby:

* Zainstaluj biblioteki usługi Twilio dla języka Python w pakiecie narzędzia Pip. Można go zainstalować za pomocą następujących poleceń:
   
        $ pip install twilio

    — Lub —

* Pobierz biblioteki usługi Twilio dla języka Python z witryny GitHub ([https://github.com/twilio/twilio-python][twilio_python]) i zainstaluj go w następujący sposób:

        $ python setup.py install

Po zainstalowaniu biblioteki usługi Twilio dla języka Python, możesz następnie `import` go w plikach języka Python:

        import twilio

Aby uzyskać więcej informacji, zobacz [https://github.com/twilio/twilio-python/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Porady: wykonywanie połączenia wychodzące
Poniżej przedstawiono sposób wywoływania wychodzących. Ten kod zawiera również lokacja dostarczonych do usługi Twilio do zwracania odpowiedzi usługi Twilio Markup Language (TwiML). Podstaw wartości **from_number** i **to_number** numerów telefonów i upewnij się, że zostały zweryfikowane **from_number** numer telefonu dla konta usługi Twilio przed uruchomieniem kodu.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "http://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Jak wspomniano, ten kod używa lokacja dostarczonych do usługi Twilio do zwracania odpowiedzi TwiML. Własnej witryny można zamiast tego użyć, aby zapewnić odpowiedzi TwiML; Aby uzyskać więcej informacji, zobacz [jak zapewnić TwiML odpowiedzi z Twoje własne witryny sieci Web](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Porady: Wyślij wiadomość SMS
Poniżej pokazano, jak wysyłać wiadomość SMS przy użyciu `TwilioRestClient` klasy. **From_number** numer jest udostępniany przez usługi Twilio dla konta wersji próbnej można wysłać wiadomości SMS. **To_number** numer muszą być weryfikowane dla konta usługi Twilio przed uruchomieniem kodu.

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

## <a id="howto_provide_twiml_responses"></a>Porady: Podaj TwiML odpowiedzi z własnej witryny sieci Web
Gdy aplikacja jest inicjowany przez wywołanie interfejsu API usługi Twilio, usługi Twilio wysyła żądanie do adresu URL, który powinien zwrócić odpowiedzi TwiML. W powyższym przykładzie korzysta z adresu URL dostarczony do usługi Twilio [http://twimlets.com/message][twimlet_message_url]. (Podczas TwiML jest przeznaczony do użytku przez usługi Twilio, można wyświetlić ją w przeglądarce. For example, kliknij przycisk [http://twimlets.com/message] [ twimlet_message_url] wyświetlić pustą `<Response>` element; inny przykład kliknij [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] wyświetlić `<Response>` element, który zawiera `<Say>` elementu.)

Zamiast polegania na adres URL dostarczony do usługi Twilio, można utworzyć własne lokacji, która zwraca odpowiedzi HTTP. Można utworzyć witryny w dowolnym języku, który zwraca odpowiedzi XML; w tym temacie założono, że będziesz używać języka Python do utworzenia TwiML.

Poniższe przykłady dane wyjściowe obejmują odpowiedzi TwiML, informujący o **Hello World** przy wywołaniu.

Z Flask:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Przy użyciu platformy Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Jak widać w powyższym przykładzie, odpowiedź TwiML jest po prostu dokumentu XML. Biblioteka usługi Twilio dla języka Python zawiera klasy, które generuje TwiML dla Ciebie. Poniższy przykład tworzy równoważne odpowiedzi, jak pokazano powyżej, ale używa `twiml` modułu w bibliotece usługi Twilio dla języka Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Aby uzyskać więcej informacji o TwiML, zobacz [https://www.twilio.com/docs/api/twiml][twiml_reference].

Po utworzeniu aplikacji Python zdefiniowano udzielenie odpowiedzi TwiML, użyj adres URL aplikacji, adres URL przekazany `client.calls.create` metody. Na przykład, jeśli masz aplikację sieci Web o nazwie **MyTwiML** wdrożone do platformy Azure hostowanej usługi, możesz użyć adresu url jako elementu webhook, jak pokazano w poniższym przykładzie:

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

## <a id="AdditionalServices"></a>Porady: korzystania z usługi Twilio dodatkowych usług
Oprócz przykładach pokazano poniżej usługi Twilio oferuje opartych na sieci web interfejsów API, które można wykorzystać dodatkowych funkcji usługi Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API usługi Twilio][twilio_api].

## <a id="NextSteps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi usługi Twilio, skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Wytyczne dotyczące zabezpieczeń usługi Twilio][twilio_security_guidelines]
* [Porada usługi Twilio prowadnice i przykładowy kod][twilio_howtos]
* [Samouczki usługi Twilio Szybki Start][twilio_quickstarts]
* [Usługi Twilio w witrynie GitHub][twilio_on_github]
* [Zwróć się do pomocy technicznej usługi Twilio][twilio_support]

[special_offer]: http://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-python/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
