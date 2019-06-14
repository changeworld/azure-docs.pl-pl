---
title: Jak nawiązać połączenie telefoniczne z usługi Twilio (Java) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie telefoniczne ze strony sieci web w aplikacji Java na platformie Azure za pomocą usługi Twilio.
services: ''
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 0d055b1a78622665137a6abad18681a728ae2b30
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60422669"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Jak nawiązać połączenie telefoniczne w aplikacji Java na platformie Azure za pomocą usługi Twilio
Poniższy przykład pokazuje, jak można użyć usługi Twilio, aby nawiązać połączenie ze strony sieci web hostowanych na platformie Azure. Wynikłej aplikacji zostanie wyświetlony monit dla wartości połączenia telefonicznego, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure za pomocą usługi Twilio i środowiska Java][twilio_java]

Należy wykonać następujące czynności, aby użyć kodu, w tym temacie:

1. Uzyskiwanie konta usługi Twilio i uwierzytelnianie tokenu. Aby rozpocząć pracę z usługą Twilio, ocenić ceny na [ https://www.twilio.com/pricing ] [ twilio_pricing]. Możesz zarejestrować się w [ https://www.twilio.com/try-twilio ] [ try_twilio]. Aby uzyskać informacji na temat interfejsów API dostarczonych przez usługi Twilio, zobacz [ https://www.twilio.com/api ] [ twilio_api].
2. Uzyskaj Twilio JAR. W [ https://github.com/twilio/twilio-java ] [ twilio_java_github], możesz pobrać źródła usługi GitHub i Utwórz własny plik JAR lub pobrać wstępnie skompilowany plik JAR (z lub bez zależności).
   Kod w tym temacie zostały zapisane, za pomocą wstępnie skompilowanych JAR TwilioJava 3.3.8 z zależności.
3. Dodaj plik JAR do ścieżki kompilacji języka Java.
4. Jeśli używasz środowiska Eclipse do tworzenia tej aplikacji Java, obejmują usługi Twilio, JAR w pliku wdrożenia aplikacji (WAR) przy użyciu funkcji zestawu wdrażania w środowisku Eclipse. Jeśli nie używasz środowiska Eclipse do tworzenia tej aplikacji Java, upewnij się, Twilio, JAR jest w ramach tej samej roli platformy Azure jako swoją aplikację Java i dodany do ścieżki klas w aplikacji.
5. Upewnij się, magazyn kluczy cacerts zawiera certyfikat urzędu certyfikacji Secure firmy Equifax z MD5 odcisku palca 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (numer seryjny jest 35:DE:F4:CF i odcisk palca SHA1 jest D2:32:09:AD:23:D 3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Jest to certyfikat urzędu certyfikacji certyfikat dla [ https://api.twilio.com ] [ twilio_api_service] usługa, która jest wywoływana, gdy za pomocą interfejsów API usługi Twilio. Informacje o dodawaniu tego certyfikatu urzędu certyfikacji do magazynu cacert swoje JDK, zobacz [Dodawanie certyfikatu do Store certyfikatu urzędu certyfikacji Java][add_ca_cert].

Ponadto znajomość odpowiednie informacje zawiera temat [utworzenie Hello World aplikacji przy użyciu zestawu narzędzi platformy Azure dla środowiska Eclipse][azure_java_eclipse_hello_world], lub z innych technik do hostowania aplikacji Java na platformie Azure, jeśli jesteś Nie można za pomocą środowiska Eclipse, zdecydowanie zaleca się.

## <a name="create-a-web-form-for-making-a-call"></a>Tworzenie formularza sieci web nawiązywania połączenia
Poniższy kod przedstawia sposób tworzenia formularza sieci web do pobierania danych użytkownika do nawiązywania połączenia. Dla celów tego przykładu, nowe dynamicznego projektu internetowego, o nazwie **TwilioCloud**, został utworzony, i **callform.jsp** został dodany jako pliku JSP.

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

## <a name="create-the-code-to-make-the-call"></a>Tworzenie kodu do wykonania wywołania
Następujący kod, który jest wywoływana, gdy użytkownik wykona w postaci wyświetlanej przez callform.jsp, tworzy komunikat wywołania i generuje wywołanie. Do celów tego przykładu, nosi nazwę pliku JSP **makecall.jsp** i została dodana do **TwilioCloud** projektu. (Użyj swojego konta usługi Twilio i uwierzytelniania tokenu zamiast wartości symboli zastępczych, przypisany do **accountSID** i **authToken** w poniższym kodzie.)

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

Oprócz wywołania makecall.jsp Wyświetla punktu końcowego usługi Twilio, wersja interfejsu API i stan połączenia. Przykładem jest poniższy zrzut ekranu:

![Odpowiedź wywołania platformy Azure za pomocą usługi Twilio i środowiska Java][twilio_java_response]

## <a name="run-the-application"></a>Uruchamianie aplikacji
Poniżej przedstawiono ogólne kroki, aby uruchomić aplikację; Szczegóły dotyczące tych kroków można znaleźć w [utworzenie Hello World aplikacji przy użyciu zestawu narzędzi platformy Azure dla środowiska Eclipse][azure_java_eclipse_hello_world].

1. Eksportuj swoje TwilioCloud WAR na platformie Azure **approot** folderu. 
2. Modyfikowanie **startup.cmd** rozpakować swoje WAR TwilioCloud.
3. Skompiluj aplikację w emulatorze obliczeń.
4. Uruchom wdrożenie w emulatorze obliczeń.
5. Otwórz przeglądarkę, a następnie uruchom `http://localhost:8080/TwilioCloud/callform.jsp`.
6. Wprowadź wartości w formularzu, kliknij przycisk **wykonać następujące wywołanie**, a następnie wyświetlenie wyników w makecall.jsp.

Gdy wszystko jest gotowe do wdrożenia na platformie Azure, ponownej kompilacji do wdrożenia w chmurze, Wdróż na platformie Azure i uruchomić http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp w przeglądarce (Zastąp wartość dla  *your_hosted_name*).

## <a name="next-steps"></a>Kolejne kroki
Ten kod podano Wam podstawowe funkcje za pomocą usługi Twilio w języku Java na platformie Azure. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym, można dodać więcej obsługę błędów lub innych funkcji. Na przykład:

* Zamiast przy użyciu formularza sieci web, można użyć usługi Azure storage blob lub bazy danych SQL do przechowywania numerów telefonów i wywołać tekstu. Aby dowiedzieć się, jak za pomocą usługi Azure storage BLOB w języku Java, zobacz [sposób użycia usługi Blob Storage w języku Java][howto_blob_storage_java]. 
* Można użyć **RoleEnvironment.getConfigurationSettings** można pobrać usługi Twilio identyfikator konta i uwierzytelniania tokenu z ustawień konfiguracji wdrożenia, zamiast kodować wartości w makecall.jsp. Aby uzyskać informacje o **RoleEnvironment** klasy, zobacz [przy użyciu biblioteki uruchomieniowej usługi platformy Azure w JSP] [ azure_runtime_jsp] oraz dokumentację pakietu środowiska uruchomieniowego usługi Azure w [ http://dl.windowsazure.com/javadoc][azure_javadoc].
* Kod makecall.jsp przypisuje adres URL dostarczone do usługi Twilio, [ https://twimlets.com/message ] [ twimlet_message_url], **adresu Url** zmiennej. Ten adres URL zawiera odpowiedź Twilio Markup Language (TwiML), która informuje o sposobie kontynuować wywołanie usługi Twilio. Na przykład może zawierać TwiML, który jest zwracany **&lt;Say&gt;** zlecenie, powstałego w tekście jest używany do odbiorcy połączenia. Zamiast przy użyciu adresu URL dostarczone do usługi Twilio, można utworzyć usługi odpowie na żądanie firmy Twilio; Aby uzyskać więcej informacji, zobacz [sposobu użycia Twilio dla połączeń głosowych i SMS funkcji w języku Java][howto_twilio_voice_sms_java]. Więcej informacji na temat TwiML znajduje się w temacie [ https://www.twilio.com/docs/api/twiml ] [ twiml]i dowiedzieć się więcej o **&lt; Say&gt;** i innych poleceń usługi Twilio można znaleźć na stronie [ https://www.twilio.com/docs/api/twiml/say ] [ twilio_say].
* Zapoznaj się ze wskazówkami zabezpieczeń Twilio na [ https://www.twilio.com/docs/security ] [ twilio_docs_security].

Aby uzyskać dodatkowe informacje na temat usługi Twilio, zobacz [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="see-also"></a>Zobacz też
* [Jak za pomocą usługi Twilio dla połączeń głosowych i SMS funkcji w języku Java][howto_twilio_voice_sms_java]
* [Dodawanie certyfikatu do Store certyfikatu urzędu certyfikacji w języku Java][add_ca_cert]

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
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
