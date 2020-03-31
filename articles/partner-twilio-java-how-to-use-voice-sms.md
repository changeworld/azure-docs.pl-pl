---
title: Jak używać usługi Twilio do obsługi głosu i wiadomości SMS (Java) | Dokumenty firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku Java.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 18e93ce18ed746612996399dc1aeb258abd26165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637223"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Jak korzystać z funkcji Twilio dla funkcji głosowych i SMS w języku Java
W tym przewodniku pokazano, jak wykonywać typowe zadania programowania za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Scenariusze obejmują nawiązanie połączenia telefonicznego i wysyłanie wiadomości SMS. Aby uzyskać więcej informacji na temat usługi Twilio i używania głosu i wiadomości SMS w aplikacjach, zobacz sekcję [Następne kroki.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co to jest Twilio?
Twilio to telefoniczny interfejs API usługi sieci web, który umożliwia korzystanie z istniejących języków internetowych i umiejętności do tworzenia aplikacji głosowych i SMS. Usługa Twilio jest usługą innej firmy (nie funkcją platformy Azure ani produktem firmy Microsoft).

**Aplikacja Twilio Voice** umożliwia aplikacjom nawiązywać i odbierać połączenia telefoniczne. **Usługa Twilio SMS** umożliwia aplikacjom na składanie i odbieranie wiadomości SMS. **Klient usługi Twilio** umożliwia aplikacjom włączanie komunikacji głosowej przy użyciu istniejących połączeń internetowych, w tym połączeń mobilnych.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny twilio i oferty specjalne
Informacje o cenach usługi Twilio są dostępne w [u cenniku Twilio][twilio_pricing]. Klienci platformy Azure otrzymują [specjalną ofertę:][special_offer]bezpłatny kredyt w wysokości 1000 tekstów lub 1000 minut przychodzących. Aby zarejestrować się w tej ofercie [https://ahoy.twilio.com/azure][special_offer]lub uzyskać więcej informacji, odwiedź stronę .

## <a name="concepts"></a><a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio jest interfejsem API RESTful, który zapewnia funkcje głosowe i sms dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; aby uzyskać listę, zobacz [Biblioteki interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio to czasowniki usługi Twilio i język znaczników języka Twilio (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Czasowniki Twilio
Interfejs API używa czasowników usługi Twilio; na przykład ** &lt;say&gt; ** zlecenie instruuje Twilio do słyszalnego dostarczania wiadomości na wywołanie.

Poniżej znajduje się lista czasowników Twilio.

* **Pokrętło:&gt;łączy rozmówcę z innym telefonem. &lt;**
* **Zbieraj&gt;: Zbiera cyfry wprowadzone na klawiaturze &lt;** telefonu.
* **Hangup&gt;: Kończy &lt;** połączenie.
* **&gt;Odtwórz : Odtwarza plik audio. &lt;**
* **Kolejka&gt;: Dodaj do kolejki rozmówców. &lt;**
* **Pauza&gt;: Czeka cicho przez określoną liczbę &lt;** sekund.
* **Rekord:&gt;Rejestruje głos dzwoniącego i zwraca adres URL pliku zawierającego &lt;** nagranie.
* **Przekierowanie:&gt;Przenosi kontrolę nad połączeniem lub SMS-em do TwiML pod innym adresem URL. &lt;**
* **Odrzuć:&gt;Odrzuca połączenie przychodzące na twój numer usługi Twilio bez rozliczeń. &lt;**
* Powiedz : Konwertuje tekst na mowę, która jest nawoływalona. ** &lt;&gt;**
* Sms : Wysyła wiadomość SMS. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML (twiml)
TwiML to zestaw instrukcji opartych na języku XML opartych na czasownikach usługi Twilio, które informują program Twilio o sposobie przetwarzania połączenia lub wiadomości SMS.

Na przykład następujący TwiML konwertuje tekst **Hello World!** do mowy.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Gdy aplikacja wywołuje interfejs API usługi Twilio, jednym z parametrów interfejsu API jest adres URL, który zwraca odpowiedź TwiML. Do celów programisty można użyć adresów URL dostarczonych przez program Twilio, aby zapewnić odpowiedzi TwiML używane przez aplikacje. Można również hostować własne adresy URL do tworzenia odpowiedzi TwiML, a inną opcją jest użycie obiektu **TwiMLResponse.**

Aby uzyskać więcej informacji na temat czasowników usługi Twilio, ich atrybutów i TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje dotyczące interfejsu API usługi Twilio, zobacz [interfejs API usługi Twilio][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Gdy będziesz gotowy do utworzenia konta usługi Twilio, zarejestruj się w [try Twilio][try_twilio]. Możesz zacząć od bezpłatnego konta, a później uaktualnić swoje konto.

Gdy zarejestrujesz konto usługi Twilio, otrzymasz identyfikator konta i token uwierzytelniania. Oba będą potrzebne do wywołania interfejsu API usługi Twilio. Aby zapobiec nieautoryzowanemu dostępowi do konta, należy zabezpieczyć token uwierzytelniania. Identyfikator konta i token uwierzytelniania są widoczne w [konsoli Twilio][twilio_console], w polach oznaczonych jako **SID konta** i **AUTH TOKEN**, odpowiednio.

## <a name="create-a-java-application"></a><a id="create_app"></a>Tworzenie aplikacji Java
1. Uzyskaj plik Twilio JAR i dodaj go do ścieżki kompilacji języka Java i zestawu wdrażania WAR. W [https://github.com/twilio/twilio-java][twilio_java], można pobrać źródła GitHub i utworzyć własny JAR, lub pobrać wstępnie utworzony JAR (z lub bez zależności).
2. Upewnij się, że magazyn **kluczy cacerts** firmy JDK zawiera certyfikat Bezpiecznego Urzędu Certyfikacji Equifax z odciskiem palca MD5 67:CB:9D:C0:13:24:24:82:8B:B2:17:1E:D1:1B:EC:D4 (numer seryjny to 35::1 DE:F4:CF i sha1 odcisk palca jest D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Jest to certyfikat urzędu certyfikacji [https://api.twilio.com][twilio_api_service] (CA) dla usługi, który jest wywoływany podczas korzystania z interfejsów API usługi Twilio. Aby uzyskać informacje dotyczące upewniania się, że magazyn **kluczy cacerts** w UDK zawiera poprawny certyfikat urzędu certyfikacji, zobacz [Dodawanie certyfikatu do Magazynu certyfikatów urzędu certyfikacji Jawy][add_ca_cert].

Szczegółowe instrukcje dotyczące korzystania z biblioteki klienta usługi Twilio dla języka Java są dostępne w [witrynie Jak nawiązać połączenie telefoniczne przy użyciu usługi Twilio w aplikacji Java na platformie Azure.][howto_phonecall_java]

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurowanie aplikacji do używania bibliotek usługi Twilio
W kodzie można dodać instrukcje **importu** w górnej części plików źródłowych dla pakietów usługi Twilio lub klas, które mają być używane w aplikacji.

W przypadku plików źródłowych Oprogramowania Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

W przypadku plików źródłowych strony serwera Java (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
W zależności od pakietów usługi Twilio lub klas, które chcesz użyć, instrukcje **importu** mogą być różne.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Jak: Nawiązać połączenie wychodzące
Poniżej pokazano, jak nawiązać połączenie wychodzące przy użyciu **Call** klasy. Ten kod używa również witryny dostarczonej przez program Twilio do zwrócenia odpowiedzi na język znaczników (TwiML) usługi Twilio. Zastąp wartości dla numerów telefonów **od** i do i **do** i upewnij się, że przed uruchomieniem kodu należy zweryfikować numer telefonu **z** konta usługi Twilio.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Aby uzyskać więcej informacji na temat parametrów przekazanych do metody **Call.creator,** zobacz [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Jak wspomniano, ten kod używa witryny dostarczonej przez twilio do zwrócenia odpowiedzi TwiML. Zamiast tego można użyć własnej witryny, aby zapewnić odpowiedź TwiML; Aby uzyskać więcej informacji, zobacz [Jak zapewnić odpowiedzi TwiML w aplikacji Java na platformie Azure](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Jak: Wyślij wiadomość SMS
Poniżej przedstawiono sposób wysyłania wiadomości SMS przy użyciu **Message** klasy. Numer **z** **numeru 4155992671**jest dostarczany przez program Twilio dla kont próbnych do wysyłania wiadomości SMS. Numer **do** musi zostać zweryfikowany dla konta usługi Twilio przed uruchomieniem kodu.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Aby uzyskać więcej informacji na temat parametrów przekazanych do metody **Message.creator,** zobacz [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Jak: Dostarczaj odpowiedzi TwiML z własnej strony internetowej
Gdy aplikacja inicjuje wywołanie interfejsu API usługi Twilio, na przykład za pośrednictwem **CallCreator.create** metody, Twilio wyśle żądanie do adresu URL, który ma zwrócić odpowiedź TwiML. W powyższym przykładzie użyto adresu [https://twimlets.com/message][twimlet_message_url]URL dostarczonego przez program Twilio . (Podczas TwiML jest przeznaczony do użytku przez usługi sieci Web, można wyświetlić TwiML w przeglądarce. Na przykład [https://twimlets.com/message][twimlet_message_url] kliknij, aby wyświetlić pusty ** &lt;element odpowiedzi;&gt; ** jako inny przykład, kliknij, [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] aby wyświetlić ** &lt;element Response,&gt; ** który zawiera ** &lt;Say&gt; ** element.)

Zamiast polegać na adresie URL dostarczonym przez program Twilio, można utworzyć własną witrynę adresu URL, która zwraca odpowiedzi HTTP. Witrynę można utworzyć w dowolnym języku, w który zwraca odpowiedzi HTTP; w tym temacie założono, że będziesz hostować adres URL na stronie JSP.

Następująca strona JSP powoduje odpowiedź TwiML, która mówi **Hello World!** podczas rozmowy.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Następująca strona JSP powoduje odpowiedź TwiML, która mówi, że niektóre tekst, ma kilka pauz i mówi informacje o wersji interfejsu API usługi Twilio i nazwę roli platformy Azure.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

Parametr **ApiVersion** jest dostępny w żądaniach głosowych usługi Twilio (nie w żądaniach SMS). Aby wyświetlić dostępne parametry żądania dla żądań <https://www.twilio.com/docs/api/twiml/twilio_request> głosowych i SMS usługi Twilio, zobacz i <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, odpowiednio. Zmienna środowiskowa **RoleName** jest dostępna w ramach wdrożenia platformy Azure. (Jeśli chcesz dodać niestandardowe zmienne środowiskowe, aby mogły zostać pobrane z **pliku System.getenv,** zobacz sekcję zmiennych środowiskowych w sekcji [Różne ustawienia konfiguracji roli][misc_role_config_settings].)

Po skonfigurowaniu strony JSP w celu zapewnienia odpowiedzi TwiML użyj adresu URL strony JSP, ponieważ adres URL został przekazany do metody **Call.creator.** Na przykład jeśli masz aplikację sieci Web o nazwie MyTwiML wdrożoną w usłudze hosta platformy Azure, a nazwa strony JSP to mytwiml.jsp, adres URL może zostać przekazany do **pliku Call.creator,** jak pokazano w poniższej wersji:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Inną opcją odpowiadania za pomocą TwiML jest za pośrednictwem **voiceresponse** klasy, która jest dostępna w **com.twilio.twiml** pakietu.

Aby uzyskać dodatkowe informacje dotyczące korzystania z usługi Twilio na platformie Azure w języku Java, zobacz [Jak nawiązać połączenie telefoniczne przy użyciu usługi Twilio w aplikacji Java na platformie Azure][howto_phonecall_java].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Jak: Korzystanie z dodatkowych usług usługi Twilio
Oprócz przykładów pokazanych w tym miejscu usługa Twilio oferuje interfejsy API oparte na sieci Web, których można użyć do wykorzystania dodatkowych funkcji usługi Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API usługi Twilio][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Kolejne kroki
Teraz, gdy znasz podstawy usługi Twilio, skorzystaj z tych łączy, aby dowiedzieć się więcej:

* [Wskazówki dotyczące zabezpieczeń usługi TWILIO][twilio_security_guidelines]
* [Kod Twilio HowTo i Przykładowy][twilio_howtos]
* [Samouczki szybki start usługi Twilio][twilio_quickstarts]
* [Twilio w usłudze GitHub][twilio_on_github]
* [Porozmawiaj z pomocą techniczną usługi Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
