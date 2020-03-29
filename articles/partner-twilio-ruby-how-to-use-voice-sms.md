---
title: Jak używać programu Twilio do obsługi głosu i wiadomości SMS (Ruby) | Dokumenty firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w Ruby.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 4822e6feb29f5a17c653a60937b895ec584e0ee4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637198"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Jak korzystać z funkcji Twilio dla funkcji głosowych i SMS w Ruby
W tym przewodniku pokazano, jak wykonywać typowe zadania programowania za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Scenariusze obejmują nawiązanie połączenia telefonicznego i wysyłanie wiadomości SMS. Aby uzyskać więcej informacji na temat usługi Twilio i używania głosu i wiadomości SMS w aplikacjach, zobacz sekcję [Następne kroki.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co to jest Twilio?
Twilio to telefoniczny interfejs API usługi sieci web, który umożliwia korzystanie z istniejących języków internetowych i umiejętności do tworzenia aplikacji głosowych i SMS. Usługa Twilio jest usługą innej firmy (nie funkcją platformy Azure ani produktem firmy Microsoft).

**Aplikacja Twilio Voice** umożliwia aplikacjom nawiązywać i odbierać połączenia telefoniczne. **Usługa Twilio SMS** umożliwia aplikacjom na składanie i odbieranie wiadomości SMS. **Klient usługi Twilio** umożliwia aplikacjom włączanie komunikacji głosowej przy użyciu istniejących połączeń internetowych, w tym połączeń mobilnych.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny twilio i oferty specjalne
Informacje o cenach usługi Twilio są dostępne w [u cenniku Twilio][twilio_pricing]. Klienci platformy Azure otrzymują [specjalną ofertę:][special_offer]bezpłatny kredyt w wysokości 1000 tekstów lub 1000 minut przychodzących. Aby zarejestrować się w tej ofercie [https://ahoy.twilio.com/azure][special_offer]lub uzyskać więcej informacji, odwiedź stronę .  

## <a name="concepts"></a><a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio jest interfejsem API RESTful, który zapewnia funkcje głosowe i sms dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; aby uzyskać listę, zobacz [Biblioteki interfejsu API usługi Twilio][twilio_libraries].

### <a name="twiml"></a><a id="TwiML"></a>TwiML (twiml)
TwiML to zestaw instrukcji opartych na języku XML, które informują program Twilio o sposobie przetwarzania połączenia lub wiadomości SMS.

Na przykład następujący TwiML konwertuje tekst **Hello World** na mowę.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wszystkie dokumenty TwiML mają `<Response>` jako element główny. W tym miejscu można użyć programu Twilio Zlecenie do definiowania zachowania aplikacji.

### <a name="twiml-verbs"></a><a id="Verbs"></a>Czasowniki TwiML
Czasowniki usługi Twilio to znaczniki XML, które informują lek Twilio, co **należy zrobić.** Na przykład ** &lt;Say&gt; ** zlecenie instruuje Twilio do słyszalnego dostarczania wiadomości na wywołanie. 

Poniżej znajduje się lista czasowników Twilio.

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

Aby uzyskać więcej informacji na temat czasowników usługi Twilio, ich atrybutów i TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje dotyczące interfejsu API usługi Twilio, zobacz [interfejs API usługi Twilio][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Gdy będziesz gotowy do utworzenia konta usługi Twilio, zarejestruj się w [try Twilio][try_twilio]. Możesz zacząć od bezpłatnego konta, a później uaktualnić swoje konto.

Gdy zarejestrujesz konto usługi Twilio, otrzymasz bezpłatny numer telefonu do aplikacji. Otrzymasz również identyfikator SID konta i token omów. Oba będą potrzebne do wywołania interfejsu API usługi Twilio. Aby zapobiec nieautoryzowanemu dostępowi do konta, należy zabezpieczyć token uwierzytelniania. Identyfikator SID konta i token yuth są widoczne na [stronie konta usługi Twilio][twilio_account], odpowiednio w polach oznaczonych **identyfikatorem SID konta** i **tokenem AUTH.**

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Weryfikowanie numerów telefonów
Oprócz numeru podanego przez firmę Twilio, możesz również zweryfikować numery, które kontrolujesz (np. numer telefonu komórkowego lub domowego) do wykorzystania w aplikacjach. 

Aby uzyskać informacje dotyczące weryfikowania numeru telefonu, zobacz [Zarządzanie numerami][verify_phone].

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Tworzenie aplikacji Ruby
Aplikacja Ruby, która korzysta z usługi Twilio i jest uruchomiona na platformie Azure nie różni się od jakiejkolwiek innej aplikacji Ruby, która korzysta z usługi Twilio. Chociaż usługi Twilio są RESTful i mogą być wywoływane z Ruby na kilka sposobów, w tym artykule skupi się na tym, jak korzystać z usług Twilio z [biblioteki pomocnika Twilio dla Ruby][twilio_ruby].

Najpierw [skonfiguruj nową maszynę wirtualną systemu Azure z systemem Linux,][azure_vm_setup] aby działała jako host dla nowej aplikacji sieci Web Ruby. Ignoruj kroki związane z tworzeniem aplikacji Rails, wystarczy skonfigurować maszynę wirtualną. Upewnij się, że utworzysz punkt końcowy z portem zewnętrznym 80 i portem wewnętrznym 5000.

W poniższych przykładach będziemy używać [Sinatry][sinatra], bardzo prostej struktury internetowej dla Ruby. Ale z pewnością możesz użyć biblioteki pomocnika Twilio dla Ruby z dowolną inną platformą internetową, w tym Ruby on Rails.

SSH do nowej maszyny Wirtualnej i utworzyć katalog dla nowej aplikacji. Wewnątrz tego katalogu utwórz plik o nazwie Gemfile i skopiuj do niego następujący kod:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

W wierszu `bundle install`polecenia uruchom . Spowoduje to zainstalowanie zależności powyżej. Następnie utwórz `web.rb`plik o nazwie . W tym miejscu będzie istnieć kod aplikacji sieci web. Wklej do niego następujący kod:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

W tym momencie powinno być `ruby web.rb -p 5000`możliwe uruchomienie polecenia . Spowoduje to rozkręceniu małego serwera www na porcie 5000. Powinieneś być w stanie przejść do tej aplikacji w przeglądarce, odwiedzając adres URL, który skonfigurowany dla maszyny Wirtualnej platformy Azure. Gdy możesz dotrzeć do aplikacji internetowej w przeglądarce, możesz rozpocząć tworzenie aplikacji Twilio.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Konfigurowanie aplikacji do używania usługi Twilio
Aplikację sieci Web można skonfigurować tak, aby używała `Gemfile` biblioteki usługi Twilio, aktualizując tę linię w celu uwzględnienia tego wiersza:

    gem 'twilio-ruby'

W wierszu polecenia `bundle install`uruchom polecenie . Teraz `web.rb` otwórz i włączając tę linię u góry:

    require 'twilio-ruby'

Teraz wszystko jest ustawione na użycie biblioteki pomocniczej usługi Twilio dla ruby w aplikacji internetowej.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Jak: Nawiązać połączenie wychodzące
Poniżej przedstawiono sposób nawiązać połączenie wychodzące. Kluczowe pojęcia obejmują przy użyciu biblioteki pomocnika usługi Twilio dla Ruby do wywołania interfejsu API REST i renderowania TwiML. Zastąp wartości numerów telefonów **od** i **do** i upewnij się, że przed uruchomieniem kodu sprawdź numer telefonu **Od** dla swojego konta Twilio.

Dodaj tę `web.md`funkcję do:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";

    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);

      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

Jeśli otworzysz `http://yourdomain.cloudapp.net/make_call` się w przeglądarce, spowoduje to wywołanie interfejsu API usługi Twilio w celu nawiązać połączenie telefoniczne. Pierwsze dwa parametry `client.account.calls.create` są dość oczywiste: numer połączenia `from` i numer połączenia jest `to`. 

Trzeci parametr`url`( ) to adres URL, który twilio żąda, aby uzyskać instrukcje dotyczące tego, co zrobić po połączeniu. W takim przypadku skonfigurujemy`http://yourdomain.cloudapp.net`adres URL ( ), który zwraca `<Say>` prosty dokument TwiML i używa zlecenia do wykonania tekstu na mowę i powiedzenia "Hello Monkey" osobie odbierającej połączenie.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Jak: Odbieranie wiadomości SMS
W poprzednim przykładzie zainicjowaliśmy **wychodzącą** rozmowę telefoniczną. Tym razem użyjmy numeru telefonu, który firma Twilio dała nam podczas rejestracji, aby przetworzyć **przychodzącą** wiadomość SMS.

Najpierw zaloguj się do [pulpitu nawigacyjnego usługi Twilio][twilio_account]. Kliknij na "Numery" w górnej części nawigacyjnych, a następnie kliknij na numer Twilio, który ci podano. Zobaczysz dwa adresy URL, które można skonfigurować. Adres URL żądania głosowego i adres URL żądania SMS. Są to adresy URL, które usługa Twilio wywołuje za każdym razem, gdy nawiązywać połączenie telefoniczne lub na Twój numer jest wysyłany sms. Adresy URL są również znane jako "haki internetowe".

Chcielibyśmy przetwarzać przychodzące wiadomości SMS, więc zaktualizujmy adres URL do `http://yourdomain.cloudapp.net/sms_url`. Śmiało i kliknij pozycję Zapisz zmiany u dołu strony. Teraz, z `web.rb` powrotem w programie naszej aplikacji do obsługi tego:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Po dokonaniu zmiany upewnij się, że ponownie uruchomisz aplikację internetową. Teraz wyjmij telefon i wyślij SMS na swój numer Twilio. Powinieneś szybko otrzymać odpowiedź SMS z napisem "Hej, dzięki za ping! Twilio i Azure rock!".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Jak: Korzystanie z dodatkowych usług usługi Twilio
Oprócz przykładów pokazanych w tym miejscu usługa Twilio oferuje interfejsy API oparte na sieci Web, których można użyć do wykorzystania dodatkowych funkcji usługi Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API usługi Twilio][twilio_api_documentation].

### <a name="next-steps"></a><a id="NextSteps"></a>Kolejne kroki
Teraz, gdy znasz podstawy usługi Twilio, skorzystaj z tych łączy, aby dowiedzieć się więcej:

* [Wskazówki dotyczące zabezpieczeń usługi TWILIO][twilio_security_guidelines]
* [Instrukcje usługi Twilio i przykładowy kod][twilio_howtos]
* [Samouczki szybki start usługi Twilio][twilio_quickstarts] 
* [Twilio w usłudze GitHub][twilio_on_github]
* [Porozmawiaj z pomocą techniczną usługi Twilio][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
