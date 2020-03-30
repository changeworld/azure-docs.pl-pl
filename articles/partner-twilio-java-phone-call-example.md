---
title: Jak nawiązać połączenie telefoniczne z Twilio (Java) | Dokumenty firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie telefoniczne ze strony sieci Web przy użyciu usługi Twilio w aplikacji Java na platformie Azure.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 168ec65cfd0ff4e87c33324daa353b554111c8aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838550"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Jak nawiązać połączenie telefoniczne przy użyciu usługi Twilio w aplikacji Java na platformie Azure
W poniższym przykładzie pokazano, jak można użyć usługi Twilio do nawinięcia połączenia ze strony sieci Web hostowanego na platformie Azure. Wynikowa aplikacja wyświetli monit o podanie wartości połączeń telefonicznych, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure przy użyciu usługi Twilio i Java][twilio_java]

Aby użyć kodu w tym temacie, należy wykonać następujące czynności:

1. Uzyskaj konto usługi Twilio i token uwierzytelniania. Aby rozpocząć korzystanie z usługi Twilio, należy ocenić ceny według . [https://www.twilio.com/pricing][twilio_pricing] Możesz zarejestrować [https://www.twilio.com/try-twilio][try_twilio]się w . Aby uzyskać informacje na temat interfejsu [https://www.twilio.com/api][twilio_api]API dostarczonego przez program Twilio, zobacz .
2. Uzyskaj plik SMwilio. W [https://github.com/twilio/twilio-java][twilio_java_github], można pobrać źródła GitHub i utworzyć własny JAR, lub pobrać wstępnie utworzony JAR (z lub bez zależności).
   Kod w tym temacie został napisany przy użyciu wstępnie utworzonego programu TwilioJava-3.3.8-with-dependencies JAR.
3. Dodaj JAR do ścieżki kompilacji Java.
4. Jeśli używasz Eclipse do tworzenia tej aplikacji Java, dołącz Twilio JAR w pliku wdrożenia aplikacji (WAR) przy użyciu funkcji zestawu wdrażania eclipse. Jeśli nie używasz eclipse do tworzenia tej aplikacji Java, upewnij się, że zestaw JAR usługi Twilio znajduje się w tej samej roli platformy Azure jako aplikacji Java i dodane do ścieżki klasy aplikacji.
5. Upewnij się, że magazyn kluczy cacerts zawiera certyfikat Bezpiecznego Urzędu Certyfikacji Equifax z odciskiem palca MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:1 7:1E:D1:1B:EC:D4 (numer seryjny to 35:DE:F4:CF, a odcisk palca SHA1 to D2:32:09:AD:23:D 3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Jest to certyfikat urzędu certyfikacji [https://api.twilio.com][twilio_api_service] (CA) dla usługi, który jest wywoływany podczas korzystania z interfejsów API usługi Twilio. Aby uzyskać informacje dotyczące dodawania tego certyfikatu urzędu certyfikacji do sklepu cacert w UDK, zobacz [Dodawanie certyfikatu do Magazynu certyfikatów urzędu certyfikacji Jawy][add_ca_cert].

Ponadto zaleca się zapoznanie się z informacjami na temat [Tworzenie aplikacji Hello World przy użyciu zestawu narzędzi azure dla programu Eclipse][azure_java_eclipse_hello_world]lub z innymi technikami hostowania aplikacji Java na platformie Azure, jeśli nie używasz programu Eclipse.

## <a name="create-a-web-form-for-making-a-call"></a>Tworzenie formularza internetowego do nawiązywania połączenia
Poniższy kod pokazuje, jak utworzyć formularz sieci web, aby pobrać dane użytkownika do nawiązywania połączenia. Na potrzeby tego przykładu utworzono nowy dynamiczny projekt sieci web o nazwie **TwilioCloud,** a **plik callform.jsp** został dodany jako plik JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Tworzenie kodu w celu nawiązać połączenie
Poniższy kod, który jest wywoływany, gdy użytkownik wypełnia formularz wyświetlany przez callform.jsp, tworzy komunikat wywołania i generuje wywołanie. Na potrzeby tego przykładu plik JSP nosi nazwę **makecall.jsp** i został dodany do projektu **TwilioCloud.** (Użyj konta usługi Twilio i tokenu uwierzytelniania zamiast wartości zastępczych przypisanych do **accountSID** i **authToken** w poniższym kodzie).

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="https://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Oprócz nawiązywania połączenia makecall.jsp wyświetla punkt końcowy usługi Twilio, wersję interfejsu API i stan wywołania. Przykładem jest następujący zrzut ekranu:

![Odpowiedź na wywołanie platformy Azure przy użyciu usług Twilio i Java][twilio_java_response]

## <a name="run-the-application"></a>Uruchamianie aplikacji
Poniżej przedstawiono kroki wysokiego poziomu, aby uruchomić aplikację; szczegółowe informacje na temat tych kroków można znaleźć na stronie [Tworzenie aplikacji Hello World przy użyciu zestawu narzędzi azure dla programu Eclipse][azure_java_eclipse_hello_world].

1. Wyeksportuj usługę TwilioCloud WAR do folderu **approot** platformy Azure. 
2. Zmodyfikuj **plik start.cmd,** aby rozpakować usługę TwilioCloud WAR.
3. Skompiluj aplikację dla emulatora obliczeń.
4. Uruchom wdrożenie w emulatorze obliczeniowym.
5. Otwórz przeglądarkę i `http://localhost:8080/TwilioCloud/callform.jsp`uruchom plik .
6. Wprowadź wartości w formularzu, kliknij przycisk **Nawiązuj to wywołanie**, a następnie zobacz wyniki w pliku makecall.jsp.

Gdy jesteś gotowy do wdrożenia na platformie Azure, ponownie skompiluj do wdrożenia w chmurze, wdrożyć na platformie Azure i uruchomić http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp w przeglądarce (zastąp wartość *your_hosted_name*).

## <a name="next-steps"></a>Następne kroki
Ten kod został dostarczony, aby pokazać podstawowe funkcje przy użyciu usługi Twilio w języku Java na platformie Azure. Przed wdrożeniem na platformie Azure w procesach produkcyjnych można dodać więcej obsługi błędów lub innych funkcji. Przykład:

* Zamiast używać formularza sieci web, można użyć obiektów blob magazynu platformy Azure lub bazy danych SQL do przechowywania numerów telefonów i wywoływania tekstu. Aby uzyskać informacje dotyczące korzystania z obiektów blob magazynu platformy Azure w języku Java, zobacz [Jak korzystać z usługi magazynu obiektów Blob z języka Java][howto_blob_storage_java]. 
* Za pomocą **programu RoleEnvironment.getConfigurationSettings** można pobrać identyfikator konta usługi Twilio i token uwierzytelniania z ustawień konfiguracji wdrożenia, zamiast kodowania wartości w pliku makecall.jsp. Aby uzyskać informacje na temat klasy **RoleEnvironment,** zobacz [Korzystanie z biblioteki środowiska uruchomieniowego usługi Azure w JSP][azure_runtime_jsp].
* Kod makecall.jsp przypisuje adres URL [https://twimlets.com/message][twimlet_message_url]dostarczony przez program Twilio do zmiennej **Url.** Ten adres URL zawiera odpowiedź TwiML (TwiML) języka znaczników usługi Twilio, która informuje Twilio, jak kontynuować wywołanie. Na przykład TwiML, który jest zwracany może zawierać ** &lt;Say&gt; ** zlecenie, które powoduje tekst jest używany do adresata wywołania. Zamiast używać adresu URL dostarczonego przez usługę Twilio, można utworzyć własną usługę, aby odpowiedzieć na żądanie usługi Twilio; Aby uzyskać więcej informacji, zobacz Jak używać funkcji [Twilio dla funkcji głosowych i SMS w języku Java][howto_twilio_voice_sms_java]. Więcej informacji na temat TwiML można znaleźć na [https://www.twilio.com/docs/api/twiml][twiml]stronie , a więcej informacji na temat [https://www.twilio.com/docs/api/twiml/say][twilio_say] ** &lt;Say&gt; ** i innych czasowników Twilio można znaleźć na stronie .
* Przeczytaj wytyczne dotyczące zabezpieczeń [https://www.twilio.com/docs/security][twilio_docs_security]usługi Twilio pod adresem .

Aby uzyskać dodatkowe informacje na [https://www.twilio.com/docs][twilio_docs]temat usługi Twilio, zobacz .

## <a name="see-also"></a>Zobacz też
* [Jak korzystać z funkcji Twilio dla funkcji głosowych i SMS w języku Java][howto_twilio_voice_sms_java]
* [Dodawanie certyfikatu do Magazynu certyfikatów urzędu certyfikacji Java][add_ca_cert]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: https://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
