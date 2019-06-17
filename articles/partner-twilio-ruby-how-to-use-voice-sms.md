---
title: Sposób użycia usługi Twilio dla połączeń głosowych i wiadomości SMS (Ruby) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązywanie połączeń telefonicznych i wysyłanie wiadomości SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku Ruby.
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
ms.openlocfilehash: 40b633c4e51a34e6640a9557be49bbe30543daf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61457655"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Sposób użycia usługi Twilio dla połączeń głosowych i SMS funkcji w języku Ruby
Ten przewodnik pokazuje sposób wykonywania typowych zadań programistycznych w usłudze interfejsu API usługi Twilio, na platformie Azure. Omówione scenariusze obejmują tworzenie połączenia telefonicznego i wysyłanie wiadomości wiadomości usługi (SMS). Aby uzyskać więcej informacji na temat usługi Twilio i używania połączeń głosowych i wiadomości SMS w swoich aplikacjach, zobacz [następne kroki](#NextSteps) sekcji.

## <a id="WhatIs"></a>Co to jest Twilio?
Twilio to interfejs API usługi sieci web telefonii, która umożliwia tworzenie połączeń głosowych i SMS aplikacje przy użyciu istniejących języków sieci web i umiejętności. Twilio to usługa innej firmy (nie nowa funkcja platformy Azure i nie produktu firmy Microsoft).

**Twilio dla połączeń głosowych** umożliwia aplikacjom wykonywanie i odbieranie połączeń telefonicznych. **Wiadomości SMS usługi Twilio** umożliwia aplikacjom wykonywanie i odbieranie wiadomości SMS. **Klient usługi Twilio** umożliwia aplikacjom obsługę połączeń głosowych przy użyciu istniejących połączeń internetowych, w tym mobilnych połączeń.

## <a id="Pricing"></a>Cennik usługi Twilio i ofert specjalnych
Informacje o cenach usługi Twilio znajduje się w temacie [cennik usługi Twilio][twilio_pricing]. Klienci platformy Azure otrzymują [oferta specjalna][special_offer]: bezpłatne środki w wysokości 1000 teksty lub 1000 dla ruchu przychodzącego w ciągu kilku minut. Zarejestruj się w ramach tej oferty, lub uzyskać więcej informacji, odwiedź stronę [ https://ahoy.twilio.com/azure ] [ special_offer].  

## <a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio to API typu RESTful, który zapewnia połączeń głosowych i SMS funkcje dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; Aby uzyskać listę, zobacz [bibliotek interfejsu API usługi Twilio][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML to zestaw instrukcji oparte na języku XML, które informują Twilio sposób przetwarzania wywołania lub wysyłać wiadomości SMS.

Na przykład następujące TwiML czy Konwertowanie tekstu **Witaj, świecie** do rozpoznawania mowy.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wszystkie dokumenty TwiML mają `<Response>` jako ich elementu głównego. W tym miejscu Twilio zlecenia służy do Definiowanie zachowania aplikacji.

### <a id="Verbs"></a>TwiML Verbs
Twilio zlecenia są tagi XML, określające Twilio, co **czy**. Na przykład **&lt;Say&gt;** Twilio komputerowi dostarczenia komunikatu w wywołaniu powoduje, że zlecenie. 

Oto lista poleceń usługi Twilio.

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

Aby uzyskać więcej informacji na temat Twilio zleceń, ich atrybuty i TwiML zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje na temat interfejsu API usługi Twilio, zobacz [interfejsu API usługi Twilio][twilio_api].

## <a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Gdy wszystko będzie gotowe uzyskać konta usługi Twilio, zarejestruj się pod adresem [spróbuj Twilio][try_twilio]. Można uruchomić przy użyciu bezpłatnego konta i później uaktualnić Twoje konto.

Podczas logowania do konta usługi Twilio, otrzymasz numer telefonu bezpłatnej aplikacji. Otrzymasz również identyfikator SID konta i tokenu uwierzytelniania. Oba będą potrzebne do wykonywania wywołań interfejsu API usługi Twilio. Aby uniemożliwić nieautoryzowany dostęp do Twojego konta, bezpieczeństwo Twojego tokenu uwierzytelniania. Identyfikator SID konta i token uwierzytelniania jest wyświetlana w [strona konta usługi Twilio][twilio_account], w polach etykietą **identyfikator SID konta** i **TOKEN uwierzytelniania**, odpowiednio.

### <a id="VerifyPhoneNumbers"></a>Sprawdzić numery telefonów
Oprócz numer są podane przez usługi Twilio, można również sprawdzić liczby formantu (czyli telefonu komórkowego lub home numer telefonu) do użytku w aplikacjach. 

Aby uzyskać informacje na temat sposobu weryfikowanie numeru telefonu, zobacz [numerów, zarządzanie][verify_phone].

## <a id="create_app"></a>Tworzenie aplikacji w języku Ruby
Aplikacja języka Ruby, który korzysta z usługi Twilio i działa w systemie Azure jest nie różni się od żadnej innej aplikacji języka Ruby, który korzysta z usługi Twilio. Podczas gdy usługi Twilio są zgodne ze specyfikacją REST i może być wywoływana z języka Ruby na kilka sposobów, w tym artykule koncentruje się na sposób użycia usługi Twilio za pomocą [Biblioteka pomocnicza Twilio dla języka Ruby][twilio_ruby].

Po pierwsze, [konfiguracji nowej maszyny Wirtualnej systemu Linux platformy Azure] [ azure_vm_setup] pełnić rolę hosta dla nowej aplikacji sieci web Ruby. Pomiń kroki dotyczące tworzenia aplikacji platformy Rails, po prostu konfiguracji maszyny Wirtualnej. Upewnij się, że utworzenie punktu końcowego za pomocą zewnętrznego portu 80 i wewnętrznego portu 5000.

W poniższych przykładach użyjemy [Sinatra][sinatra], platforma bardzo prostą dla języka Ruby. Jednak możesz bez obaw można używać Biblioteka pomocnicza Twilio dla języka Ruby przy użyciu wszelkie inne struktury sieci web, w tym platformy Ruby on Rails.

SSH do swojej nowej maszyny Wirtualnej i Utwórz katalog dla nowej aplikacji. W tym katalogu Utwórz plik o nazwie Gemfile i skopiuj następujący kod do niego:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

W wierszu polecenia Uruchom `bundle install`. Spowoduje to zainstalowanie zależności opisane powyżej. Następnie utwórz plik o nazwie `web.rb`. Są to, gdzie znajduje się kod aplikacji sieci web. Wklej następujący kod do niego:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

W tym momencie powinno być możliwe Uruchom polecenie `ruby web.rb -p 5000`. To spowoduje pokrętła telefoniczny serwer małych sieci web na porcie 5000. Można przeglądać do tej aplikacji w przeglądarce, przechodząc do adresu URL możesz konfiguracji dla maszyny Wirtualnej platformy Azure. Po dotarciu aplikacji sieci web w przeglądarce, możesz rozpocząć tworzenie aplikacji usługi Twilio.

## <a id="configure_app"></a>Umożliwia skonfigurowanie aplikacji za pomocą usługi Twilio
Można skonfigurować aplikację sieci web za pomocą biblioteki usługi Twilio, aktualizując swoje `Gemfile` obejmujący ten wiersz:

    gem 'twilio-ruby'

W wierszu polecenia Uruchom `bundle install`. Teraz Otwórz `web.rb` oraz ten wiersz u góry, takich jak:

    require 'twilio-ruby'

Teraz wszystko jest gotowe do użycia Biblioteka pomocnicza Twilio dla języka Ruby w aplikacji sieci web.

## <a id="howto_make_call"></a>Jak: Wykonywanie wywołania interfejsu wychodzącego
Poniżej przedstawiono sposób wykonania wywołania wychodzącego. Kluczowe założenia obejmują przy użyciu biblioteki pomocnika Twilio dla języka Ruby do wykonywania wywołań interfejsu API REST i renderowanie TwiML. Podstaw wartości **z** i **do** numerów telefonów i upewnij się, aby zweryfikować **z** numer telefonu dla konta usługi Twilio, przed uruchomieniem kodu.

Dodaj tę funkcję, aby `web.md`:

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

Jeśli open up `http://yourdomain.cloudapp.net/make_call` w przeglądarce, która spowoduje wyzwolenie wywołania interfejsu API usługi Twilio umożliwiają połączenie telefoniczne. Dwa pierwsze parametry w `client.account.calls.create` wyjaśnienia: liczba to wywołanie `from` i liczba to wywołanie `to`. 

Trzeci parametr (`url`) jest adresem URL, który żąda Twilio, aby uzyskać instrukcje, co należy zrobić po nawiązaniu połączenia. W takim przypadku firma Microsoft konfiguracji, a adres URL (`http://yourdomain.cloudapp.net`), zwraca prosty dokument TwiML i używa `<Say>` zlecenie pewne zamiany tekstu na mowę i powiedzieć "Hello małp", aby osobę otrzymania wywołania.

## <a id="howto_receive_sms"></a>Jak: Odbieranie wiadomości SMS
W poprzednim przykładzie mamy zainicjowane **wychodzących** połączeń telefonicznych. Ten czas, użyjemy numer telefonu Twilio z dużą dozą podczas rejestracji do procesu **przychodzących** wiadomości SMS.

Po pierwsze, zaloguj się do Twojej [pulpit nawigacyjny Twilio][twilio_account]. Kliknij pozycję "Cyfry" w górnym okienku nawigacji, a następnie kliknij polecenie numer usługi Twilio, do którego zostały podane. Zostaną wyświetlone dwa adresy URL, które można skonfigurować. Adres URL żądania adresie URL żądania głosowe i wiadomość SMS. Są to adresy URL, które wywołuje Twilio, zawsze, gdy rozmowy telefonicznej lub wiadomości SMS są wysyłane do numeru. Adresy URL są również określane jako "web hooks".

Prosimy o poświęcenie do przetwarzania przychodzących wiadomości SMS, więc zaktualizuj adres URL do `http://yourdomain.cloudapp.net/sms_url`. Przejdź dalej i kliknij przycisk Zapisz zmiany w dolnej części strony. Teraz ponownie do niej `web.rb` teraz program naszej aplikacji do obsługi to:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Po wprowadzeniu zmian, upewnij się, że ponowne uruchomienie aplikacji sieci web. Teraz Twój telefon w działaniu i Wyślij wiadomość SMS na numer usługi Twilio. Niezwłocznie powinna pojawić się odpowiedź programu SMS, która mówi "Hej, dziękuję ping! Rock Twilio i na platformie Azure! ".

## <a id="additional_services"></a>Jak: Za pomocą usługi Twilio dodatkowych usług
Oprócz przykładów, w tym miejscu Twilio oferuje oparte na sieci web interfejsów API, które można wykorzystać dodatkowych funkcji usługi Twilio, z aplikacji systemu Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API usługi Twilio][twilio_api_documentation].

### <a id="NextSteps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi Twilio, skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Twilio Security Guidelines][twilio_security_guidelines]
* [HowTos usługi Twilio i przykładowy kod][twilio_howtos]
* [Samouczki szybkiego startu usługi Twilio][twilio_quickstarts] 
* [Twilio w witrynie GitHub][twilio_on_github]
* [Zwróć się do pomocy technicznej usługi Twilio][twilio_support]

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
