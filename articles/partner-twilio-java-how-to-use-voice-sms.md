---
title: Jak używać usługi Twilio głosowe i programu SMS (Java) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonać połączenie telefoniczne i wysłać wiadomość SMS z usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku Java.
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
ms.openlocfilehash: 5a1b2ffa160a31b639605242b651dc8d14e7a01b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866156"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Jak używać usługi Twilio głosowe i możliwości programu SMS w języku Java
W tym przewodniku przedstawiono sposób wykonywania typowych zadań programowania usługi interfejsu API usługi Twilio na platformie Azure. Omówione scenariusze obejmują tworzenie połączeń telefonicznych i wysyłanie wiadomości krótką wiadomość usługi (SMS). Aby uzyskać więcej informacji na temat usługi Twilio i komunikacja głosowa programu SMS w aplikacji, zobacz [następne kroki](#NextSteps) sekcji.

## <a id="WhatIs"></a>Co to jest usługi Twilio?
Usługi Twilio jest interfejs API usługi sieci web telefonii, które umożliwia tworzenie aplikacji programu SMS i komunikacja głosowa przy użyciu istniejących języków sieci web i umiejętności. Usługi Twilio jest usługą innych firm (nie funkcji platformy Azure i nie produkt firmy Microsoft).

**Usługi Twilio głosu** umożliwia aplikacjom wykonywanie i odbieranie połączeń telefonicznych. **SMS usługi Twilio** umożliwia aplikacjom wykonywanie i odbieranie wiadomości SMS. **Klient usługi Twilio** umożliwia aplikacjom włączyć komunikację głosową, przy użyciu istniejących połączeń internetowych, w tym przenośnych połączenia.

## <a id="Pricing"></a>Cennik usługi Twilio i oferty specjalne
Informacje o cenach usługi Twilio znajduje się w temacie [cennik usługi Twilio][twilio_pricing]. Azure klienci otrzymają [oferta specjalna][special_offer]: wolnego środki tekstów 1000 lub 1000 ruchu przychodzącego minut. Zapisz się do tej oferty lub uzyskać więcej informacji, odwiedź [http://ahoy.twilio.com/azure][special_offer].

## <a id="Concepts"></a>Pojęcia
Interfejs API usługi Twilio jest interfejs API RESTful, która zapewnia funkcje programu SMS i komunikacja głosowa aplikacji. Klient biblioteki są dostępne w wielu językach; Aby uzyskać listę, zobacz [bibliotek interfejsu API usługi Twilio][twilio_libraries].

Kluczowe aspekty interfejsu API usługi Twilio są zleceń usługi Twilio i usługi Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Usługi Twilio zlecenia
Interfejs API korzysta z usługi Twilio zleceń; na przykład  **&lt;powiedzieć&gt;**  zlecenie powoduje, że usługi Twilio komputerowi dostarczenia komunikatu w wywołaniu.

Poniżej znajduje się lista zleceń usługi Twilio.

* **&lt;Wybierania&gt;**: łączy wywołującego innego numeru telefonu.
* **&lt;Zbierz&gt;**: zbiera cyfry wprowadzone na klawiaturze telefonu.
* **&lt;Rozłączanie&gt;**: kończy wywołanie.
* **&lt;Odtwórz&gt;**: odtwarzania pliku audio.
* **&lt;Kolejka&gt;**: Dodaj do kolejki obiekty wywołujące.
* **&lt;Wstrzymaj&gt;**: dyskretnie czeka na określoną liczbę sekund.
* **&lt;Rekord&gt;**: rejestruje wywołującego głosu i zwraca adres URL pliku zawierającego nagrywania.
* **&lt;Przekieruj&gt;**: przekazuje kontrolę nad wywołania lub wiadomości SMS do TwiML na inny adres URL.
* **&lt;Odrzuć&gt;**: odrzucanie połączenia przychodzącego na numer usługi Twilio bez rozliczeń można.
* **&lt;Powiedz&gt;**: Konwertuje tekst na mowę, które zostało utworzone w wywołaniu.
* **&lt;SMS&gt;**: wysyła wiadomość SMS.

### <a id="TwiML"></a>TwiML
TwiML to zestaw instrukcji oparte na języku XML, oparte na zlecenia usługi Twilio, które informują usługi Twilio sposobu przetwarzania połączenia lub wiadomości SMS.

Na przykład następujące TwiML czy Konwertuj tekst **Hello World!** Aby mowy.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Gdy aplikacja wywołuje interfejs API usługi Twilio, jeden z parametrów interfejsu API jest adres URL, który zwraca odpowiedź TwiML. Do celów programistycznych wprowadzone do usługi Twilio adresy URL służy do nadania odpowiedzi TwiML używane przez aplikacje. Może również udostępniać swoje własne adresy URL do tworzenia odpowiedzi TwiML i inną opcją jest użycie **TwiMLResponse** obiektu.

Aby uzyskać więcej informacji na temat usługi Twilio zlecenia i ich atrybuty oraz TwiML, zobacz [TwiML][twiml]. Aby uzyskać dodatkowe informacje na temat interfejsu API usługi Twilio, zobacz [interfejsu API usługi Twilio][twilio_api].

## <a id="CreateAccount"></a>Tworzenie konta usługi Twilio
Po przygotowaniu można uzyskać konta usługi Twilio, zarejestruj się w [spróbuj usługi Twilio][try_twilio]. Można rozpoczynać bezpłatne konto, a następnie Uaktualnij swoje konto później.

Po utworzeniu konta dla konta usługi Twilio, otrzymasz Identyfikatora konta oraz tokenu uwierzytelniania. Zarówno będą potrzebne w celu wykonywania wywołań interfejsu API usługi Twilio. Aby uniemożliwić nieautoryzowany dostęp do Twojego konta, bezpieczeństwo Twojego tokenu uwierzytelniania. Twój identyfikator konta i uwierzytelniania tokenu jest wyświetlana w [usługi Twilio Console][twilio_console], w polach etykietą **identyfikator SID konta** i **TOKEN uwierzytelniania**odpowiednio.

## <a id="create_app"></a>Tworzenie aplikacji Java
1. Uzyskaj JAR usługi Twilio i dodaj go do ścieżka kompilacji języka Java i używanemu zestawowi wdrożenia WAR. W [https://github.com/twilio/twilio-java][twilio_java], możesz pobrać źródeł GitHub i tworzyć własne JAR lub pobrać wbudowanych JAR (z lub bez zależności).
2. Upewnij się, Twoje JDK **cacerts** magazynu kluczy zawiera certyfikat firmy Equifax Secure urzędu certyfikacji z 67:CB:9 odcisk palca MD5 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (numer seryjny jest 35:DE:F4:CF i SHA1 Odcisk palca jest D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Jest to certyfikat urzędu certyfikacji dla [https://api.twilio.com] [ twilio_api_service] usługi, która jest wywoływana, gdy używasz interfejsów API usługi Twilio. Informacji o sprawdzeniu Twojej JDK **cacerts** keystore zawiera prawidłowy certyfikat urzędu certyfikacji, zobacz [Dodawanie certyfikatu do magazynu certyfikatów urzędu certyfikacji Java][add_ca_cert].

Szczegółowe instrukcje dotyczące korzystania z biblioteki klienta usługi Twilio dla języka Java są dostępne pod adresem [porady: Tworzenie usługi przy użyciu połączenia telefonicznego Twilio w aplikacji Java na platformie Azure][howto_phonecall_java].

## <a id="configure_app"></a>Konfigurowanie aplikacji przy użyciu bibliotek usługi Twilio
W kodzie, możesz dodać **zaimportować** instrukcje w górnej części plików źródłowych dla pakietów usługi Twilio lub klasy, które ma być używany w aplikacji.

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
 
W zależności od tego, które pakiety usługi Twilio lub klasy, do którego chcesz użyć, Twoje **zaimportować** instrukcje mogą się różnić.

## <a id="howto_make_call"></a>Porady: wykonywanie połączenia wychodzące
Poniżej pokazano, jak wykonać wychodzące wywołanie przy użyciu **wywołać** klasy. Ten kod zawiera również lokacja dostarczonych do usługi Twilio do zwracania odpowiedzi usługi Twilio Markup Language (TwiML). Podstaw wartości **z** i **do** numerów telefonów i upewnij się, że sprawdzisz **z** numer telefonu dla konta usługi Twilio przed uruchomieniem kodu.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("http://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Aby uzyskać więcej informacji na temat parametrów przekazanych do **Call.creator** metody, zobacz [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Jak wspomniano, ten kod używa lokacja dostarczonych do usługi Twilio do zwracania odpowiedzi TwiML. Własnej witryny można zamiast tego użyć, aby zapewnić odpowiedzi TwiML; Aby uzyskać więcej informacji, zobacz [jak zapewnić TwiML odpowiedzi w aplikacji Java na platformie Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Porady: Wyślij wiadomość SMS
Poniżej pokazano, jak wysyłać wiadomość SMS przy użyciu **komunikat** klasy. **z** numer, **4155992671**, są udostępniane przez usługi Twilio dla konta wersji próbnej można wysłać wiadomości SMS. **Do** numer muszą być weryfikowane dla konta usługi Twilio przed uruchomieniem kodu.

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

Aby uzyskać więcej informacji na temat parametrów przekazanych do **Message.creator** metody, zobacz [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Porady: Podaj TwiML odpowiedzi z własnej witryny sieci Web
Gdy aplikacja inicjuje wywołanie interfejsu API usługi Twilio, na przykład za pomocą **CallCreator.create** metody usługi Twilio wyśle żądanie do adresu URL, który powinien zwrócić odpowiedzi TwiML. W powyższym przykładzie korzysta z adresu URL dostarczony do usługi Twilio [http://twimlets.com/message][twimlet_message_url]. (Podczas TwiML jest przeznaczony do użytku przez usługi sieci Web, można wyświetlić TwiML w przeglądarce. Na przykład kliknij pozycję [http://twimlets.com/message] [ twimlet_message_url] wyświetlić pustą  **&lt;odpowiedzi&gt;**  element; inny przykład kliknij [http://twimlets.com/message?Message%5B0%5D=Hello%20World%21] [ twimlet_message_url_hello_world] wyświetlić  **&lt;odpowiedzi&gt;**  element, który zawiera  **&lt;powiedzieć? &gt;**  elementu.)

Zamiast polegania na adres URL dostarczony do usługi Twilio, można utworzyć własny adres URL w witrynie odpowiedzi HTTP. Można utworzyć witryny w dowolnym języku, który zwraca odpowiedzi HTTP; w tym temacie założono, że będzie obsługiwać adres URL strony JSP.

Na następującej stronie JSP powoduje odpowiedzi TwiML informacją **Hello World!** w wywołaniu.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Następująca strona JSP powoduje odpowiedzi TwiML mówi część tekstu, ma kilka pauzy i mówi informacje o wersji interfejsu API usługi Twilio i nazwę roli platformy Azure.

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

**ApiVersion** parametr jest dostępny w żądaniach głosu usługi Twilio (nie liczba żądań programu SMS). Aby wyświetlić dostępne parametrów głosowe usługi Twilio i żądań programu SMS, zobacz <https://www.twilio.com/docs/api/twiml/twilio_request> i <https://www.twilio.com/docs/api/twiml/sms/twilio_request>odpowiednio. **RoleName** zmiennej środowiskowej jest dostępna jako część wdrożenia usługi Azure. (Jeśli chcesz dodać zmienne środowiskowe niestandardowych, tak aby ich może zostać pobrana z **System.getenv**, zobacz sekcję zmienne środowiskowe w [dodatkowych ustawień konfiguracji roli] [ misc_role_config_settings].)

Po utworzeniu strony JSP zdefiniowano udzielenie odpowiedzi TwiML, użyj adres URL strony JSP adres URL przekazany **Call.creator** metody. Na przykład, jeśli masz aplikację sieci Web o nazwie MyTwiML wdrożone do platformy Azure hostowanej usługi i nazwa strony JSP jest mytwiml.jsp, adres URL można przekazać do **Call.creator** jak pokazano poniżej:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Inną opcją w przypadku odpowiadać TwiML odbywa się za pośrednictwem **VoiceResponse** klasy, która jest dostępna w **com.twilio.twiml** pakietu.

Aby uzyskać dodatkowe informacje dotyczące korzystania z usługi Twilio w platformie Azure za pomocą języka Java, zobacz [porady: Tworzenie usługi przy użyciu połączenia telefonicznego Twilio w aplikacji Java na platformie Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Porady: korzystania z usługi Twilio dodatkowych usług
Oprócz przykładach pokazano poniżej usługi Twilio oferuje opartych na sieci web interfejsów API, które można wykorzystać dodatkowych funkcji usługi Twilio z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentacji interfejsu API usługi Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi usługi Twilio, skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Wytyczne dotyczące zabezpieczeń usługi Twilio][twilio_security_guidelines]
* [Porada usługi Twilio i przykładowy kod][twilio_howtos]
* [Samouczki usługi Twilio Szybki Start][twilio_quickstarts]
* [Usługi Twilio w witrynie GitHub][twilio_on_github]
* [Zwróć się do pomocy technicznej usługi Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/docs
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
