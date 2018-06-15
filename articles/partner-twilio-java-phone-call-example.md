---
title: 'Porady: tworzenie rozmowy telefonicznej z usługi Twilio (Java) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak nawiązać połączenie telefoniczne ze strony sieci web w aplikacji Java na platformie Azure przy użyciu usługi Twilio.
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
ms.openlocfilehash: 04ecb80a2a9e15b549b47138caf71c7e64bda500
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865939"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Porady: tworzenie rozmowę telefoniczną w aplikacji Java na platformie Azure przy użyciu usługi Twilio
Poniższy przykład pokazuje, jak można użyć usługi Twilio, aby nawiązać połączenie ze strony sieci web hostowanej na platformie Azure. Wynikowa aplikacja pojawi się monit dla wartości rozmowy telefonicznej, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure przy użyciu usługi Twilio i Java][twilio_java]

Należy wykonać następujące czynności, aby użyć kodu w tym temacie:

1. Uzyskać konta usługi Twilio i uwierzytelniania tokenu. Aby zacząć korzystać z usługi Twilio, ocenę cennik w [http://www.twilio.com/pricing][twilio_pricing]. Możesz utworzyć konto w [https://www.twilio.com/try-twilio][try_twilio]. Aby uzyskać informacje o interfejsie API dostarczonych przez usługi Twilio, zobacz [http://www.twilio.com/api][twilio_api].
2. Uzyskaj usługi Twilio JAR. W [https://github.com/twilio/twilio-java][twilio_java_github], możesz pobrać źródeł GitHub i tworzyć własne JAR lub pobrać wbudowanych JAR (z lub bez zależności).
   Kod w tym temacie została opracowana za pomocą wbudowanych JAR TwilioJava 3.3.8 z zależności.
3. Dodaj JAR do ścieżki kompilacji języka Java.
4. Jeśli używasz programu Eclipse do tworzenia aplikacji w języku Java, w pliku wdrożenia aplikacji (plik WAR) za pomocą funkcji zestawu wdrożenia w środowisku Eclipse należy umieścić JAR usługi Twilio. Jeśli nie używasz programu Eclipse do tworzenia aplikacji w języku Java, upewnij się, JAR usługi Twilio jest uwzględniony w obrębie tego samego elementu role Azure jako aplikacji w języku Java i dodawany do ścieżki klasy aplikacji.
5. Upewnij się Twojego magazynu kluczy cacerts zawiera certyfikat urzędu certyfikacji Secure firmy Equifax z 67:CB:9 odcisk palca MD5 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (numer seryjny jest 35:DE:F4:CF i odcisk palca SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Jest to certyfikat urzędu certyfikacji dla [https://api.twilio.com] [ twilio_api_service] usługi, która jest wywoływana, gdy używasz interfejsów API usługi Twilio. Informacje o dodawaniu tego certyfikatu urzędu certyfikacji do magazynu cacert Twojego JDK, zobacz [Dodawanie certyfikatu do magazynu certyfikatów urzędu certyfikacji Java][add_ca_cert].

Ponadto znajomość informacji w [utworzenie Hello World aplikacji przy użyciu zestawu narzędzi platformy Azure dla programu Eclipse][azure_java_eclipse_hello_world], lub z innych technik do hostowania aplikacji Java na platformie Azure, jeśli nie używasz programu Eclipse jest zdecydowanie zalecane.

## <a name="create-a-web-form-for-making-a-call"></a>Tworzenie formularza sieci web nawiązywania połączenia
Poniższy kod przedstawia sposób tworzenia formularzy sieci web do pobierania danych użytkownika do nawiązywania połączenia. Dla celów tego przykładu nowy projekt dynamicznej sieci web o nazwie **TwilioCloud**, został utworzony i **callform.jsp** został dodany jako plik JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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

## <a name="create-the-code-to-make-the-call"></a>Utwórz kod, aby wykonać wywołanie
Następujący kod, który jest wywoływany, gdy użytkownik zamyka formularz, wyświetlane przez callform.jsp, tworzy komunikat wywołania i generuje wywołania. Do celów tego przykładu, nosi nazwę pliku JSP **makecall.jsp** i została dodana do **TwilioCloud** projektu. (Użyj swojego konta usługi Twilio i uwierzytelniania tokenu zamiast wartości symbolu zastępczego, przypisane do **accountSID** i **parametru authToken** w poniższym kodzie.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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
         String Url="http://twimlets.com/message";
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

Oprócz wywołania, makecall.jsp Wyświetla punktu końcowego usługi Twilio, wersja interfejsu API i stan połączenia. Przykładem jest na poniższym zrzucie ekranu:

![Odpowiedź wywołania platformy Azure przy użyciu usługi Twilio i Java][twilio_java_response]

## <a name="run-the-application"></a>Uruchamianie aplikacji
Poniżej przedstawiono ogólne kroki do uruchamiania aplikacji; Szczegóły dla tych kroków można znaleźć w [utworzenie Hello World aplikacji przy użyciu zestawu narzędzi platformy Azure dla programu Eclipse][azure_java_eclipse_hello_world].

1. Eksportowanie z WAR TwilioCloud do platformy Azure **approot** folderu. 
2. Modyfikowanie **startup.cmd** rozpakować z WAR TwilioCloud.
3. Skompiluj aplikację w emulatorze obliczeń.
4. Uruchomić wdrożenia w emulatorze obliczeń.
5. Otwórz przeglądarkę, a następnie uruchom **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Wprowadź wartości w formularzu, kliknij polecenie **wykonać to wywołanie**i wyświetlić wyniki w makecall.jsp.

Gdy wszystko będzie gotowe do wdrożenia na platformie Azure, skompiluj ponownie do wdrożenia w chmurze, wdrażanie na platformie Azure, i uruchom http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp w przeglądarce (Zastąp wartość dla *your_hosted_name*).

## <a name="next-steps"></a>Następne kroki
Ten kod został podany pokazanie podstawowych funkcji przy użyciu usługi Twilio w języku Java na platformie Azure. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym, możesz dodać więcej obsługi błędów lub innych funkcji. Na przykład:

* Zamiast za pomocą formularza sieci web, można użyć obiektów blob magazynu Azure lub bazy danych SQL do przechowywania numerów telefonów i wywołać tekstu. Aby dowiedzieć się, jak za pomocą obiektów blob magazynu Azure w języku Java, zobacz [jak używać usługi magazynu obiektów Blob w języku Java][howto_blob_storage_java]. Aby uzyskać informacje dotyczące korzystania z bazy danych SQL w języku Java, zobacz [przy użyciu bazy danych SQL w języku Java][howto_sql_azure_java].
* Można użyć **RoleEnvironment.getConfigurationSettings** można pobrać usługi Twilio Identyfikatora konta oraz uwierzytelniania token z danym wdrożeniu ustawień konfiguracji, zamiast kodować wartości makecall.jsp. Informacje o **RoleEnvironment** , zobacz [za pomocą biblioteki środowiska uruchomieniowego usługi Azure w JSP] [ azure_runtime_jsp] i dokumentacji pakietu środowiska uruchomieniowego usługi Azure w [http://dl.windowsazure.com/javadoc][azure_javadoc].
* Kod makecall.jsp przypisuje adres URL udostępniony do usługi Twilio, [http://twimlets.com/message][twimlet_message_url], do **adres Url** zmiennej. Ten adres URL zawiera odpowiedzi usługi Twilio Markup Language (TwiML), który informuje usługi Twilio sposób postępowania z wywołaniem. Na przykład może zawierać TwiML, która jest zwracana  **&lt;powiedzieć&gt;**  zlecenia, który daje w tekście jest używany do adresata wywołania. Zamiast używać adresu URL dostarczony do usługi Twilio, można zbudować usługi na odpowiedź na żądanie dla usługi Twilio; Aby uzyskać więcej informacji, zobacz [sposobu użycia usługi Twilio, głosowe i możliwości programu SMS w języku Java][howto_twilio_voice_sms_java]. Więcej informacji na temat TwiML można znaleźć w folderze [http://www.twilio.com/docs/api/twiml][twiml]i więcej informacji na temat  **&lt;powiedzieć&gt;**  i inne usługi Twilio zlecenia można znaleźć w folderze [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Zapoznaj się ze wskazówkami zabezpieczeń usługi Twilio w [https://www.twilio.com/docs/security][twilio_docs_security].

Aby uzyskać dodatkowe informacje na temat usługi Twilio, zobacz [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Zobacz też
* [Jak używać usługi Twilio głosowe i możliwości programu SMS w języku Java][howto_twilio_voice_sms_java]
* [Dodawanie certyfikatu do magazynu certyfikatów urzędu certyfikacji Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
