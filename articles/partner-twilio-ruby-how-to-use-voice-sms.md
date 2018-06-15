---
title: Jak używać usługi Twilio głosowe i programu SMS (Ruby) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonać połączenie telefoniczne i wysłać wiadomość SMS z usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku Ruby.
services: ''
documentationcenter: ruby
author: devinrader
manager: twilio
editor: ''
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 69e50e7fe0e1f302e96c309878b8dea6869dff4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866079"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Jak używać usługi Twilio głosowe i możliwości programu SMS w języku Ruby
W tym przewodniku przedstawiono sposób wykonywania typowych zadań programowania usługi interfejsu API usługi Twilio na platformie Azure. Omówione scenariusze obejmują tworzenie połączeń telefonicznych i wysyłanie wiadomości krótką wiadomość usługi (SMS). Aby uzyskać więcej informacji na temat usługi Twilio i komunikacja głosowa programu SMS w aplikacji, zobacz [następne kroki](#NextSteps) sekcji.

## <a id="WhatIs"></a>Co to jest usługi Twilio?
Usługi Twilio jest interfejs API usługi sieci web telefonii, które umożliwia tworzenie aplikacji programu SMS i komunikacja głosowa przy użyciu istniejących języków sieci web i umiejętności. Usługi Twilio jest usługą innych firm (nie funkcji platformy Azure i nie produkt firmy Microsoft).

**Usługi Twilio głosu** umożliwia aplikacjom wykonywanie i odbieranie połączeń telefonicznych. **SMS usługi Twilio** umożliwia aplikacjom wykonywanie i odbieranie wiadomości SMS. **Klient usługi Twilio** umożliwia aplikacjom włączyć komunikację głosową, przy użyciu istniejących połączeń internetowych, w tym przenośnych połączenia.

## <a id="Pricing"></a>Cennik usługi Twilio i oferty specjalne
Informacje o cenach usługi Twilio znajduje się w temacie [cennik usługi Twilio][twilio_pricing]. Azure klienci otrzymają [oferta specjalna][special_offer]: wolnego środki tekstów 1000 lub 1000 ruchu przychodzącego minut. Zapisz się do tej oferty lub uzyskać więcej informacji, odwiedź [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio jest interfejs API RESTful, która zapewnia funkcje programu SMS i komunikacja głosowa aplikacji. Klient biblioteki są dostępne w wielu językach; Aby uzyskać listę, zobacz [bibliotek interfejsu API usługi Twilio][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML to zestaw instrukcji oparte na języku XML, które informują usługi Twilio sposobu przetwarzania połączenia lub wiadomości SMS.

Na przykład następujące TwiML czy Konwertuj tekst **Hello World** do rozpoznawania mowy.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wszystkie dokumenty TwiML mają `<Response>` jako ich elementu głównego. Dostępne usługi Twilio zleceń służą do definiowania zachowania aplikacji.

### <a id="Verbs"></a>TwiML zlecenia
Usługi Twilio zlecenia są tagi XML, które informują usługi Twilio, co **czy**. Na przykład  **&lt;powiedzieć&gt;**  zlecenie powoduje, że usługi Twilio komputerowi dostarczenia komunikatu w wywołaniu. 

Poniżej znajduje się lista zleceń usługi Twilio.

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

Aby uzyskać więcej informacji na temat usługi Twilio zlecenia i ich atrybuty oraz TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje na temat interfejsu API usługi Twilio, zobacz [interfejsu API usługi Twilio][twilio_api].

## <a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Po przygotowaniu można uzyskać konta usługi Twilio, zarejestruj się w [spróbuj usługi Twilio][try_twilio]. Można rozpoczynać bezpłatne konto, a następnie Uaktualnij swoje konto później.

Po utworzeniu konta dla konta usługi Twilio, otrzymasz numer telefonu bezpłatnej aplikacji. Otrzymasz również identyfikatora SID konta, a token uwierzytelniania. Zarówno będą potrzebne w celu wykonywania wywołań interfejsu API usługi Twilio. Aby uniemożliwić nieautoryzowany dostęp do Twojego konta, bezpieczeństwo Twojego tokenu uwierzytelniania. Identyfikator SID konta, a token uwierzytelniania jest wyświetlana w [strony konta usługi Twilio][twilio_account], w polach etykietą **identyfikator SID konta** i **TOKEN uwierzytelniania**odpowiednio.

### <a id="VerifyPhoneNumbers"></a>Sprawdź numery telefonów
Oprócz numer podano przez usługi Twilio, można również sprawdzić numery skonfigurujesz kontrolowaną (np. telefon komórkowy lub głównej numer telefonu) do użycia w aplikacji. 

Aby uzyskać informacje o zweryfikowanie numeru telefonu, zobacz [Zarządzanie numery][verify_phone].

## <a id="create_app"></a>Tworzenie aplikacji Ruby
Nie różni się od innych dopisków fonetycznych aplikację korzystającą z usługi Twilio usługi się dopisków fonetycznych aplikacji, która używa usługi usługi Twilio i działa na platformie Azure. Usługi Twilio usług są RESTful i może zostać wywołana z Ruby na kilka sposobów, w tym artykule dotyczą sposobu korzystania z usługi Twilio usług w programie [usługi Twilio biblioteki pomocnika dla środowiska Ruby][twilio_ruby].

Najpierw [konfiguracji nowej maszyny Wirtualnej systemu Linux Azure] [ azure_vm_setup] do działania jako hosta dla nowej aplikacji sieci web dopisków fonetycznych. Pomiń kroki dotyczące tworzenia aplikacji szyny, po prostu konfiguracji maszyny Wirtualnej. Upewnij się, że utworzono punkt końcowy z zewnętrznego portu 80 i wewnętrzny port 5000.

W poniższych przykładach użyjemy [Sinatra][sinatra], ramy bardzo proste sieci web dla środowiska Ruby. Ale pewnością służy biblioteki Pomocnik usługi Twilio dla środowiska Ruby z żadnych innych platforma sieci web, w tym Ruby na szyny.

SSH do nowej maszyny Wirtualnej i Utwórz katalog dla nowej aplikacji. W tym katalogu Utwórz plik o nazwie Gemfile i skopiuj do niego następujący kod:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

W wierszu polecenia Uruchom `bundle install`. Spowoduje to zainstalowanie zależności powyżej. Następnie utwórz plik o nazwie `web.rb`. Są to, gdzie znajduje się kod aplikacji sieci web. Wklej następujący kod do niej:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

W tym momencie powinno być możliwe uruchomienie polecenia `ruby web.rb -p 5000`. To spowoduje rozkręcenia serwera sieci web małej na port 5000. Można będzie przejdź do tej aplikacji w przeglądarce, przechodząc na stronę adres URL należy konfiguracji maszyny Wirtualnej Azure. Gdy aplikacja sieci web w przeglądarce może nawiązać połączenie, możesz rozpocząć tworzenie aplikacji usługi Twilio.

## <a id="configure_app"></a>Skonfigurować aplikację do używania usługi Twilio
Można skonfigurować aplikację sieci web do korzystania z biblioteki usługi Twilio, aktualizując Twojej `Gemfile` do uwzględnienia w tym wierszu:

    gem 'twilio-ruby'

W wierszu polecenia Uruchom `bundle install`. Teraz otworzyć `web.rb` i ten wiersz na początku, w tym:

    require 'twilio-ruby'

Teraz wszystko jest gotowe do korzystania z usługi Twilio pomocnika biblioteki dla środowiska Ruby w aplikacji sieci web.

## <a id="howto_make_call"></a>Porady: wykonywanie połączenia wychodzące
Poniżej przedstawiono sposób wywoływania wychodzących. Kluczowe założenia obejmują za pomocą usługi Twilio biblioteki pomocnika dla środowiska Ruby do wykonywania wywołań interfejsu API REST i renderowania TwiML. Podstaw wartości **z** i **do** numerów telefonów i upewnij się, że sprawdzisz **z** numer telefonu dla konta usługi Twilio przed uruchomieniem kodu.

Dodaj tę funkcję, aby `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
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

Jeśli otwarty up `http://yourdomain.cloudapp.net/make_call` w przeglądarce, która wyzwoli wywołanie interfejsu API usługi Twilio rozmowę telefoniczną. Pierwsze dwa parametry w `client.account.calls.create` dość wyjaśnień: liczba wywołanie jest `from` i liczba wywołanie jest `to`. 

Trzeci parametr (`url`) jest adres URL, który żąda usługi Twilio, aby uzyskać instrukcje, co należy zrobić po wywołaniu jest połączony. W tym przypadku firma Microsoft konfiguracji, a adres URL (`http://yourdomain.cloudapp.net`) zwraca prosty dokumentu TwiML i używa `<Say>` zlecenie czy niektóre tekst na mowę i wypowiedz "Hello małp" osobie odbierającej wywołania.

## <a id="howto_recieve_sms"></a>Porady: Odbierz wiadomość SMS
W poprzednim przykładzie mamy zainicjowano **wychodzących** rozmowy telefonicznej. Tego czasu, można użyć numer telefonu, który usługi Twilio podał podczas tworzenia konta do procesu **przychodzące** wiadomości SMS.

Po pierwsze, zaloguj się do Twojego [pulpitu nawigacyjnego usługi Twilio][twilio_account]. Kliknij przycisk "Numery" w górnym nav, a następnie kliknij przycisk z liczbą usługi Twilio, które zostały podane. Zostanie wyświetlone dwa adresy URL, które można skonfigurować. Adres URL żądania adresie URL żądania głosu i programu SMS. Są to adresy URL, które wywołuje usługi Twilio, zawsze, gdy nawiązuje połączenie telefoniczne lub programu SMS są wysyłane na numer. Adresy URL są również znane jako "punkty zaczepienia sieci web".

Chcielibyśmy przetwarza przychodzące wiadomości SMS, więc warto aktualizacja adresu URL do `http://yourdomain.cloudapp.net/sms_url`. Przejdź dalej i kliknij przycisk Zapisz zmiany w dolnej części strony. Teraz, ponownie `web.rb` teraz program naszą aplikację do obsługi to:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Po wprowadzeniu zmian, upewnij się ponownie uruchomić aplikacji sieci web. Teraz Wyjmij telefonu i Wyślij wiadomość SMS na numer usługi Twilio. Należy niezwłocznie pobrać odpowiedzi programu SMS z informacją, "Witaj, Dziękujemy ping! Skale usługi Twilio i na platformie Azure! ".

## <a id="additional_services"></a>Porady: korzystania z usługi Twilio dodatkowych usług
Oprócz przykładach pokazano poniżej usługi Twilio oferuje opartych na sieci web interfejsów API, które można wykorzystać dodatkowych funkcji usługi Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API usługi Twilio][twilio_api_documentation].

### <a id="NextSteps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi usługi Twilio, skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Wytyczne dotyczące zabezpieczeń usługi Twilio][twilio_security_guidelines]
* [HowTos usługi Twilio i przykładowy kod][twilio_howtos]
* [Samouczki usługi Twilio Szybki Start][twilio_quickstarts] 
* [Usługi Twilio w witrynie GitHub][twilio_on_github]
* [Zwróć się do pomocy technicznej usługi Twilio][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
