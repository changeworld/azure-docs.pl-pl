---
title: Jak używać usługi Twilio dla połączeń głosowych i wiadomości SMS (Java) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązywanie połączeń telefonicznych i wysyłanie wiadomości SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku Java.
services: ''
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 386b4b8440c74f6599e7147996b5843ea0f67e68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623956"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Jak za pomocą usługi Twilio dla połączeń głosowych i SMS funkcji w języku Java
Ten przewodnik pokazuje sposób wykonywania typowych zadań programistycznych w usłudze interfejsu API usługi Twilio, na platformie Azure. Omówione scenariusze obejmują tworzenie połączenia telefonicznego i wysyłanie wiadomości wiadomości usługi (SMS). Aby uzyskać więcej informacji na temat usługi Twilio i używania połączeń głosowych i wiadomości SMS w swoich aplikacjach, zobacz [następne kroki](#NextSteps) sekcji.

## <a id="WhatIs"></a>Co to jest Twilio?
Twilio to interfejs API usługi sieci web telefonii, która umożliwia tworzenie połączeń głosowych i SMS aplikacje przy użyciu istniejących języków sieci web i umiejętności. Twilio to usługa innej firmy (nie nowa funkcja platformy Azure i nie produktu firmy Microsoft).

**Twilio dla połączeń głosowych** umożliwia aplikacjom wykonywanie i odbieranie połączeń telefonicznych. **Wiadomości SMS usługi Twilio** umożliwia aplikacjom wykonywanie i odbieranie wiadomości SMS. **Klient usługi Twilio** umożliwia aplikacjom obsługę połączeń głosowych przy użyciu istniejących połączeń internetowych, w tym mobilnych połączeń.

## <a id="Pricing"></a>Cennik usługi Twilio i ofert specjalnych
Informacje o cenach usługi Twilio znajduje się w temacie [cennik usługi Twilio][twilio_pricing]. Klienci platformy Azure otrzymują [oferta specjalna][special_offer]: bezpłatne środki w wysokości 1000 teksty lub 1000 dla ruchu przychodzącego w ciągu kilku minut. Zarejestruj się w ramach tej oferty, lub uzyskać więcej informacji, odwiedź stronę [ https://ahoy.twilio.com/azure ] [ special_offer].

## <a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio to API typu RESTful, który zapewnia połączeń głosowych i SMS funkcje dla aplikacji. Biblioteki klienckie są dostępne w wielu językach; Aby uzyskać listę, zobacz [bibliotek interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio to zleceń usługi Twilio i Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio zlecenia
Interfejs API korzysta z usługi Twilio zleceń; na przykład **&lt;Say&gt;** Twilio komputerowi dostarczenia komunikatu w wywołaniu powoduje, że zlecenie.

Oto lista poleceń usługi Twilio.

* **&lt;Wybierania&gt;**: Obiekt wywołujący nawiązanie połączenia innego numeru telefonu.
* **&lt;Gather&gt;**: Gromadzi informacje o cyfry wprowadzone na klawiaturze telefonu.
* **&lt;Rozłączanie&gt;**: Kończy wywołanie.
* **&lt;Play&gt;**: Odtwarza plik audio.
* **&lt;Queue&gt;**: Dodaj do kolejki obiekty wywołujące.
* **&lt;Pause&gt;**: Dyskretnie czeka określoną liczbę sekund.
* **&lt;Rekord&gt;**: Rejestruje głosu wywołującego i zwraca adres URL pliku, który zawiera nagrywania.
* **&lt;Redirect&gt;**: Transfer kontroli połączenia lub wiadomości SMS do TwiML na inny adres URL.
* **&lt;Reject&gt;**: Odrzuca połączenie na numer Twilio bez możesz rozliczeń.
* **&lt;Powiedz&gt;**: Konwertuje tekst na mowę, który składa się na wywołanie.
* **&lt;Sms&gt;**: Wysyła wiadomość SMS.

### <a id="TwiML"></a>TwiML
TwiML to zbiór opartych na języku XML instrukcje zleceń Twilio, które informują Twilio sposób przetwarzania wywołania lub wysyłać wiadomości SMS.

Na przykład następujące TwiML czy Konwertowanie tekstu **Witaj, świecie!** do rozpoznawania mowy.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Gdy Twoja aplikacja wywołuje interfejs API usługi Twilio, jeden z parametrów interfejs API jest adres URL, który zwraca odpowiedź TwiML. Do celów programistycznych dostarczone do usługi Twilio adresy URL służy do zapewniania odpowiedzi TwiML używane przez aplikacje. Może także udostępniać własne adresy URL służące do tworzenia odpowiedzi TwiML i innym rozwiązaniem jest użycie **TwiMLResponse** obiektu.

Aby uzyskać więcej informacji na temat Twilio zleceń, ich atrybuty i TwiML zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje na temat interfejsu API usługi Twilio, zobacz [interfejsu API usługi Twilio][twilio_api].

## <a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Gdy wszystko będzie gotowe uzyskać konta usługi Twilio, zarejestruj się pod adresem [spróbuj Twilio][try_twilio]. Można uruchomić przy użyciu bezpłatnego konta i później uaktualnić Twoje konto.

Po zarejestrowaniu się do konta usługi Twilio, otrzymasz identyfikator konta i tokenu uwierzytelniania. Oba będą potrzebne do wykonywania wywołań interfejsu API usługi Twilio. Aby uniemożliwić nieautoryzowany dostęp do Twojego konta, bezpieczeństwo Twojego tokenu uwierzytelniania. Swojego Identyfikatora konta i uwierzytelniania tokenu są wyświetlane w [konsoli Twilio][twilio_console], w polach etykietą **identyfikator SID konta** i **TOKEN uwierzytelniania**, odpowiednio.

## <a id="create_app"></a>Tworzenie aplikacji Java
1. Uzyskaj JAR usługi Twilio i dodaj go do Twojego ścieżka kompilacji języka Java i zestawu deployment WAR. W [ https://github.com/twilio/twilio-java ] [ twilio_java], możesz pobrać źródła usługi GitHub i Utwórz własny plik JAR lub pobrać wstępnie skompilowany plik JAR (z lub bez zależności).
2. Upewnij się, Twoje JDK **cacerts** magazynu kluczy znajduje się certyfikat urzędu certyfikacji Secure firmy Equifax z MD5 odcisku palca 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (numer seryjny jest 35:DE:F4:CF i SHA1 Odcisk palca jest D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Jest to certyfikat urzędu certyfikacji certyfikat dla [ https://api.twilio.com ] [ twilio_api_service] usługa, która jest wywoływana, gdy za pomocą interfejsów API usługi Twilio. Dla informacji na temat sprawdzania swojej JDK **cacerts** magazynu kluczy zawiera prawidłowy certyfikat urzędu certyfikacji, zobacz temat [Dodawanie certyfikatu do Store certyfikatu urzędu certyfikacji Java][add_ca_cert].

Szczegółowe instrukcje dotyczące korzystania z biblioteki klienta usługi Twilio dla środowiska Java są dostępne pod adresem [sposób wprowadzania Twilio za pomocą połączenia telefonicznego w aplikacji Java na platformie Azure][howto_phonecall_java].

## <a id="configure_app"></a>Konfigurowanie aplikacji przy użyciu bibliotek usługi Twilio
W kodzie, można dodać **zaimportować** instrukcji w górnej części plików źródłowych dla pakietów usługi Twilio lub klasy, które chcesz użyć w aplikacji.

Dla plików źródłowych Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Dla plików źródłowych Java strony serwera (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Zależności od tego, które pakiety usługi Twilio lub klasy, do którego chcesz użyć, Twoje **zaimportować** instrukcje mogą być inne.

## <a id="howto_make_call"></a>Jak: Wykonywanie wywołania interfejsu wychodzącego
Poniżej pokazano sposób wprowadzania wychodzące wywołanie przy użyciu **wywołania** klasy. Ten kod używa również lokacji dostarczone do usługi Twilio do zwracania odpowiedzi Twilio Markup Language (TwiML). Podstaw wartości **z** i **do** numerów telefonów i upewnij się, aby zweryfikować **z** numer telefonu dla konta usługi Twilio, przed uruchomieniem kodu.

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

Aby uzyskać więcej informacji na temat parametrów przekazanych do **Call.creator** metody, zobacz [ https://www.twilio.com/docs/api/rest/making-calls ] [ twilio_rest_making_calls].

Jak wspomniano wcześniej, ten kod używa witryny dostarczone do usługi Twilio do zwracania odpowiedzi TwiML. Do własnej witryny można zamiast tego użyć, aby zapewnić odpowiedzi TwiML; Aby uzyskać więcej informacji, zobacz [jak Podaj TwiML odpowiedzi w aplikacji Java na platformie Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Jak: Wyślij wiadomość SMS
Poniżej pokazano, jak wysyłać wiadomość SMS przy użyciu **komunikat** klasy. **z** numer **4155992671**, jest świadczona przez Twilio dla konta wersji próbnej do wysyłania wiadomości SMS. **Do** numer musi być zweryfikowany dla konta usługi Twilio, przed uruchomieniem kodu.

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

Aby uzyskać więcej informacji na temat parametrów przekazanych do **Message.creator** metody, zobacz [ https://www.twilio.com/docs/api/rest/sending-sms ] [ twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Jak: Zapewnienie TwiML odpowiedzi z własną witrynę sieci Web
Gdy aplikacja inicjuje wywołanie interfejsu API usługi Twilio, na przykład za pośrednictwem **CallCreator.create** metody, Twilio wyśle żądanie do adresu URL, który powinien zwrócić odpowiedź TwiML. W powyższym przykładzie używa adresu URL dostarczone do usługi Twilio [ https://twimlets.com/message ] [ twimlet_message_url]. (Choć TwiML jest przeznaczony do użytku przez usługi sieci Web, można wyświetlić TwiML w przeglądarce. Na przykład kliknij pozycję [ https://twimlets.com/message ] [ twimlet_message_url] będzie pusta **&lt;odpowiedzi&gt;** element; inny przykład kliknij [ https://twimlets.com/message?Message%5B0%5D=Hello%20World%21 ] [ twimlet_message_url_hello_world] się **&lt;odpowiedzi&gt;** element, który zawiera **&lt;Say&gt;** elementu.)

Zamiast polegania na adres URL podany do usługi Twilio, można utworzyć własny adres URL w witrynie odpowiedzi HTTP. Witryny można utworzyć w dowolnym języku, który zwraca odpowiedzi HTTP; w tym temacie założono, że będziesz hostingu adres URL strony JSP.

Następującą stronę JSP skutkuje informujący, że odpowiedź TwiML **Witaj, świecie!** na wywołanie.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Następujące strony JSP powoduje odpowiedzi TwiML mówi jakiś tekst, który ma kilka wstrzymuje działanie i mówi informacje o wersji interfejsu API usługi Twilio i nazwy roli platformy Azure.

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

**ApiVersion** parametr jest dostępny w żądaniach głosu Twilio (nie liczba żądań programu SMS). Aby wyświetlić dostępne parametrów żądań programu SMS i twilio dla połączeń głosowych, zobacz <https://www.twilio.com/docs/api/twiml/twilio_request> i <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, odpowiednio. **RoleName** zmienna środowiskowa jest dostępny jako część wdrożenia platformy Azure. (Jeśli chcesz dodać niestandardowe zmienne środowiskowe, więc one mogą być pobierane z **System.getenv**, zobacz zmienne środowiskowe sekcji w [różne ustawienia konfiguracji roli] [ misc_role_config_settings].)

Po utworzeniu strony JSP skonfigurowany do zapewniania TwiML odpowiedzi, użyj adres URL strony JSP adres URL przekazany do **Call.creator** metody. Na przykład w przypadku aplikacji sieci Web o nazwie MyTwiML wdrożona na platformie Azure hostowane usługi i nazwy strony JSP mytwiml.jsp, adres URL może być przekazywany do **Call.creator** jak pokazano w następującym:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Inna opcja odpowiadamy przy użyciu TwiML jest za pośrednictwem **VoiceResponse** klasy, która jest dostępna w **com.twilio.twiml** pakietu.

Aby uzyskać dodatkowe informacje o platformie Azure przy użyciu języka Java za pomocą usługi Twilio, zobacz [sposób wprowadzania Twilio za pomocą połączenia telefonicznego w aplikacji Java na platformie Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Jak: Za pomocą usługi Twilio dodatkowych usług
Oprócz przykładów, w tym miejscu Twilio oferuje oparte na sieci web interfejsów API, które można wykorzystać dodatkowych funkcji usługi Twilio, z aplikacji systemu Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API usługi Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi Twilio, skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Twilio Security Guidelines][twilio_security_guidelines]
* [Porada usługi Twilio i przykładowy kod][twilio_howtos]
* [Samouczki szybkiego startu usługi Twilio][twilio_quickstarts]
* [Twilio w witrynie GitHub][twilio_on_github]
* [Zwróć się do pomocy technicznej usługi Twilio][twilio_support]

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
